---
title: EF Core 5.0 の新機能
description: EF Core 5.0 の新機能の概要
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: b4551a3c593694b104a750d500d81eb170a83dc0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618597"
---
# <a name="whats-new-in-ef-core-50"></a>EF Core 5.0 の新機能

EF Core 5.0 は現在開発中です。 このページには、各プレビューで導入された耳寄りな変更の概要が記載されています。

このページには [EF Core 5.0 のプラン](xref:core/what-is-new/ef-core-5.0/plan)を記載していません。 このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 5.0 のテーマ全体について説明します。

公開されている公式ドキュメントについては、リンクが追加されます。

## <a name="preview-8"></a>Preview 8

### <a name="table-per-type-tpt-mapping"></a>Table-Per-Type (TPT) のマッピング

EF Core の既定では、.NET 型の継承階層が 1 つのデータベース テーブルにマップされます。 これは、Table-Per-Hierarchy (TPH) のマッピングと呼ばれます。 EF Core 5.0 を使用すると、継承階層の各 .NET 型を別のデータベース テーブルにマップすることもできます。これは Table-Per-Type (TPT) のマッピングと呼ばれます。

たとえば、次のようにマップされた階層を持つこのモデルを考えてみましょう。

```c#
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

public class Pet : Animal
{
    public string Name { get; set; }
}

public class Cat : Pet
{
    public string EducationLevel { get; set; }
}

public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

EF Core の既定では、これが 1 つのテーブルにマップされます。

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

一方、各エンティティ型を別のテーブルにマップすると、代わりに型ごとに 1 つのテーブルが作成されます。

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Pets] (
    [Id] int NOT NULL,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Pets] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Pets_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Cats_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Dogs_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);
```

前述の外部キー制約の作成は、プレビュー 8 のコードを分岐した後に追加されたことに注意してください。

エンティティ型を別のテーブルにマップするには、マッピング属性を使用します。

```c#
[Table("Animals")]
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

[Table("Pets")]
public class Pet : Animal
{
    public string Name { get; set; }
}

[Table("Cats")]
public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

または `ModelBuilder` 構成を使用します。

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

ドキュメントは、イシュー [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979) で追跡されています。

### <a name="migrations-rebuild-sqlite-tables"></a>移行:SQLite テーブルを再構築する

他のデータベースと比較すると、SQLite のスキーマ操作機能は比較的限られています。 たとえば、既存のテーブルから列を削除するには、テーブル全体を削除して再作成する必要があります。 EF Core 5.0 の移行では、必要なスキーマ変更のためにテーブルの自動再構築がサポートされるようになりました。

たとえば、エンティティ型 `Unicorn` に対して作成された `Unicorns` テーブルがあるとします。

```c#
public class Unicorn
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```sql
CREATE TABLE "Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "Age" INTEGER NOT NULL
);
```

次に、ユニコーンの年齢を格納することは非常に失礼だと考えられることがわかったので、そのプロパティを削除し、新しい移行を追加して、データベースを更新しましょう。 EF Core 3.1 を使用している場合は列を削除できないため、この更新は失敗します。 EF Core 5.0 ではそうではなく、移行によってテーブルが再構築されます。

```sql
CREATE TABLE "ef_temp_Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL
);

INSERT INTO "ef_temp_Unicorns" ("Id", "Name")
SELECT "Id", "Name"
FROM Unicorns;

PRAGMA foreign_keys = 0;

DROP TABLE "Unicorns";

ALTER TABLE "ef_temp_Unicorns" RENAME TO "Unicorns";

PRAGMA foreign_keys = 1;
```

次のことに注意してください。
* 新しいテーブルに必要なスキーマを使用して一時テーブルが作成されます
* データは現在のテーブルから一時テーブルにコピーされます
* 外部キーの適用はオフに切り替えられます
* 現在のテーブルはドロップされます
* 一時テーブルは新しいテーブルとして名前が変更されます

ドキュメントは、イシュー [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583) で追跡されています。

### <a name="table-valued-functions"></a>テーブル値関数

この機能は、[@pmiddleton](https://github.com/pmiddleton) によってコミュニティから提供されました。 投稿に感謝します。

EF Core 5.0 には、.NET メソッドをテーブル値関数 (TVF) にマップするための最上級のサポートが含まれています。 これらの関数を LINQ クエリで使用することができます。また、この関数の結果に対する追加の構成も SQL に変換されます。

たとえば、SQL Server データベースで定義されている次の TVF について考えてみましょう。

```sql
CREATE FUNCTION GetReports(@employeeId int)
RETURNS @reports TABLE
(
    Name nvarchar(50) NOT NULL,
    IsDeveloper bit NOT NULL
)
AS
BEGIN
    WITH cteEmployees AS
    (
        SELECT Id, Name, ManagerId, IsDeveloper
        FROM Employees
        WHERE Id = @employeeId
        UNION ALL
        SELECT e.Id, e.Name, e.ManagerId, e.IsDeveloper
        FROM Employees e
        INNER JOIN cteEmployees cteEmp ON cteEmp.Id = e.ManagerId
    )
    INSERT INTO @reports
    SELECT Name, IsDeveloper
    FROM cteEmployees
    WHERE Id != @employeeId

    RETURN
END
```

EF Core モデルでは、この TVF を使用するために 2 つのエンティティ型が必要です。
* 通常の方法で Employees テーブルにマップする `Employee` 型
* TVF から返されるシェイプと一致する `Report` 型

```c#
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```c#
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

これらの型は、EF Core モデルに含まれている必要があります。

```c#
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

`Report` には主キーがないため、そのように構成する必要があることに注意してください。

最後に、.NET メソッドをデータベースの TVF にマップする必要があります。 このメソッドは、新しい `FromExpression` メソッドを使用して DbContext に対して定義できます。

```c#
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

このメソッドには、上記で定義された TVF に一致するパラメーターと戻り値の型が使用されます。 次に、メソッドは OnModelCreating の EF Core モデルに追加されます

```c#
modelBuilder.HasDbFunction(() => GetReports(default));
```

(ここでラムダを使用すると、`MethodInfo` を EF Core に簡単に渡すことができます。 メソッドに渡された引数は無視されます)。

これで、`GetReports` を呼び出して結果を構成するクエリを作成できるようになりました。 次に例を示します。

```c#
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

SQL Server では、これは次のように変換されます。

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

SQL では `Employees` テーブルがルートとされ、`GetReports` が呼び出され、関数の結果に WHERE 句が追加されることに注目してください。

### <a name="flexible-queryupdate-mapping"></a>柔軟なクエリと更新マップ

EF Core 5.0 では、同じエンティティ型を異なるデータベース オブジェクトにマップできます。 これらのオブジェクトは、テーブル、ビュー、または関数です。

たとえば、エンティティ型はデータベース ビューとデータベース テーブルの両方にマップできます。

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

既定では、EF Core はビューからクエリを実行し、更新をテーブルに送信します。 たとえば、次のコードを実行します。

```c#
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

ビューに対してクエリを実行し、テーブルを更新します。

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a>コンテキスト全体の分割クエリ構成

分割クエリ (以下を参照してください) は、DbContext によって実行されるクエリの既定値として構成できるようになりました。 この構成はリレーショナル プロバイダーの場合にのみ使用できるため、`UseProvider` 構成の一部として指定する必要があります。 次に例を示します。

```c#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

ドキュメントは、イシュー [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) で追跡されます。

### <a name="physicaladdress-mapping"></a>PhysicalAddress のマッピング

この機能は、[@ralmsdeveloper](https://github.com/ralmsdeveloper) によってコミュニティから提供されました。 投稿に感謝します。

この構成はリレーショナル プロバイダーでのみ使用できるた標準の .NET [PhysicalAddress クラス](/dotnet/api/system.net.networkinformation.physicaladdress)が、ネイティブ サポートをまだ持っていないデータベースの文字列型の列に自動的にマップされるようになりました。 詳細については、以下の `IPAddress` の例を参照してください。

## <a name="preview-7"></a>Preview 7

### <a name="dbcontextfactory"></a>DbContextFactory

EF Core 5.0 には、アプリケーションの依存関係挿入 (D.I.) コンテナーに DbContext インスタンスを作成するファクトリを登録する `AddDbContextFactory` と `AddPooledDbContextFactory` が導入されています。 次に例を示します。

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

これにより、ASP.NET Core コントローラーなどのアプリケーション サービスが、サービス コンストラクターの `IDbContextFactory<TContext>` に依存できます。 次に例を示します。

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

これにより、DbContext インスタンスを必要に応じて作成および使用できます。 次に例を示します。

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

なお、この方法で作成された DbContext インスタンスは、アプリケーションのサービス プロバイダーには管理 "_されず_"、従ってアプリケーションによって破棄される必要があります。 この分離は、`IDbContextFactory` の使用が推奨される Blazor アプリケーションで非常に便利ですが、他のシナリオでも役立つ場合があります。

DbContext インスタンスは、`AddPooledDbContextFactory` を呼び出すことによってプールできます。 このプールは、`AddDbContextPool` の場合と同じように動作しますが、同じ制限もあります。

ドキュメントは、イシュー [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523) で追跡されています。

### <a name="reset-dbcontext-state"></a>DbContext の状態のリセット

EF Core 5.0 には、追跡対象のすべてのエンティティの DbContext をクリアする `ChangeTracker.Clear()` が導入されています。 これは、作業単位ごとに有効期間が短い新しいコンテキスト インスタンスを作成するベスト プラクティスを使用している場合には通常不要です。 ただし、DbContext インスタンスの状態をリセットする必要がある場合、この新しい `Clear()` メソッドを使用すると、すべてのエンティティを一括でデタッチするよりも、パフォーマンスと堅牢性が向上します。

ドキュメントは、イシュー [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524) で追跡されています。

### <a name="new-pattern-for-store-generated-defaults"></a>ストアで生成された既定値に新しいパターン

EF Core では、既定の制約値がある列に、値を明示的に設定できます。 EF Core では、このセンチネルとして、プロパティ型に CLR の既定値を使用しています。つまり、その値が CLR の既定でない場合はそれが挿入され、それ以外の場合は、データベースの既定値が使用されます。

これは、既定の CLR が適切なセンチネルではない型 (特に、`bool` のプロパティ) で問題が発生します。 EF Core 5.0 で、次のようなケースで、バッキング フィールドに null 値が許容されるようになりました。 次に例を示します。

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

なお、null 値はバッキング フィールドでは許容されますが、公開されているプロパティでは許容されません。 これにより、エンティティ型の公開サーフェスに影響を与えることなく、センチネル値を `null` にできます。 この場合、`IsValid` が設定されない場合、バッキング フィールドは null のままであるため、データベースの既定値が使用されます。 `true` または `false` のいずれかが設定される場合、この値はデータベースに明示的に保存されます。

ドキュメントは、イシュー [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525) で追跡されています。

### <a name="cosmos-partition-keys"></a>Cosmos のパーティション キー

EF Core では、EF モデルに Cosmos パーティション キーを含めることができます。 次に例を示します。

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

Preview 7 以降では、一部のクエリのパフォーマンスの向上のために、パーティション キーがエンティティ型の PK に含まれるようになりました。

ドキュメントは、イシュー [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) で追跡されています。

### <a name="cosmos-configuration"></a>Cosmos の構成

EF Core 5.0 では、Cosmos と Cosmos への接続の構成が改善されています。

以前は、EF Core を Cosmos データベースに接続する場合に、エンドポイントとキーを明示的に指定する必要がありました。 EF Core 5.0 では、代わりに接続文字列を使用できます。 また、EF Core 5.0 では、WebProxy インスタンスを明示的に設定することができます。 次に例を示します。

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

その他、多くのタイムアウト値、制限なども構成できるようになりました。 次に例を示します。

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

最後に、一般により互換性がある、`ConnectionMode.Gateway` が既定の接続モードになりました。

ドキュメントは、イシュー [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) で追跡されています。

### <a name="scaffold-dbcontext-now-singularizes"></a>DbContext のスキャフォールディングの単数化

以前は EF Core で、既存のデータベースから DbContext をスキャフォールディングする場合、データベース内のテーブル名と一致するエンティティ型名が作成されていました。 たとえば、`People` テーブルと `Addresses` テーブルには、`People` と `Addresses` という名前のエンティティ型が生成されていました。

この動作は、以前のリリースでは、複数形化サービスの登録によって構成できました。 EF Core 5.0 では、既定の複数形化サービスとして [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) パッケージが使用されるようになりました。 つまり、`People` テーブルと `Addresses` テーブルは、`Person` と `Address` という名前のエンティティ型にリバース エンジニアリングされます。

### <a name="savepoints"></a>セーブポイント

EF Core では、複数の操作を実行するトランザクションをより細かく制御する、[セーブポイント](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks)がサポートされるようになりました。

セーブポイントは、手動で作成、解放、ロールバックすることができます。 次に例を示します。

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

また、`SaveChanges` の実行に失敗すると、EF Core では最後のセーブポイントにロールバックされるようになりました。 これにより、トランザクション全体を再実行しなくても、SaveChanges を再実行できます。

ドキュメントは、イシュー [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429) で追跡されています。

## <a name="preview-6"></a>Preview 6

### <a name="split-queries-for-related-collections"></a>関連するコレクションのクエリを分割する

EF Core 3.0 以降、EF Core では、LINQ クエリごとに 1 つの SQL クエリが常に生成されます。 これにより、使用中のトランザクション モードの制約内で返されるデータの整合性が確保されます。 ただし、クエリで `Include` またはプロジェクションを使用して複数の関連コレクションを戻すと、この処理が非常に遅くなる可能性があります。

EF Core 5.0 では、関連するコレクションを含む単一の LINQ クエリを複数の SQL クエリに分割できるようになりました。 これにより、パフォーマンスを大幅に向上させることができますが、2 つのクエリ間でデータが変更された場合に返される結果に不整合が生じる可能性があります。 シリアル化可能な、またはスナップショットのトランザクションを使用すると、これを軽減し、分割されたクエリとの整合性を維持できますが、他のパフォーマンス コストと動作の違いが生じる可能性があります。

#### <a name="split-queries-with-include"></a>インクルードを使用してクエリを分割する

たとえば、`Include` を使用して 2 つのレベルの関連コレクションを取得するクエリについて考えてみます。

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

既定では、SQLite プロバイダーの使用時に、EF Core によって次の SQL が生成されます。

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

新しい `AsSplitQuery` API を使用して、この動作を変更できます。 次に例を示します。

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

AsSplitQuery は、すべてのリレーショナル データベース プロバイダーで使用でき、AsNoTracking と同様に、クエリ内の任意の場所で使用できます。 EF Core によって、次の 3 つの SQL クエリが生成されるようになりました。

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

クエリ ルートに対するすべての操作がサポートされています。 これには、OrderBy/Skip/Take、結合操作、FirstOrDefault、および類似した単一結果選択操作が含まれます。

OrderBy/Skip/Take でフィルター処理されたインクルードは、Preview 6 ではサポートされませんが、デイリー ビルドで使用でき、Preview 7 には含まれます。

#### <a name="split-queries-with-collection-projections"></a>コレクション プロジェクションを使用してクエリを分割する

`AsSplitQuery` は、コレクションがプロジェクションに読み込まれるときにも使用できます。 次に例を示します。

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

この LINQ クエリでは、SQLite プロバイダーを使用するときに、次の 2 つの SQL クエリが生成されます。

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

サポートされるのはコレクションの具体化のみであることに注意してください。 上記のケースの `e.Albums` 後のコンポジションでは、分割クエリは発生しません。 この領域の機能強化は、[#21234](https://github.com/dotnet/efcore/issues/21234) で追跡されます。

### <a name="indexattribute"></a>IndexAttribute

新しい IndexAttribute をエンティティ型に配置して、1 つの列のインデックスを指定することができます。 次に例を示します。

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

SQL Server の場合、移行によって次の SQL が生成されます。

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

IndexAttribute を使用して、複数の列にまたがるインデックスを指定することもできます。 次に例を示します。

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

SQL Server の場合、次のようになります。

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

ドキュメントは、イシュー [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) で追跡されます。

### <a name="improved-query-translation-exceptions"></a>クエリ変換の例外の改善

クエリ変換が失敗したときに生成される例外メッセージの改善を続けています。 たとえば、次のクエリでは、マップされていないプロパティ `IsSigned` を使用します。

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

EF Core は、`IsSigned` がマップされていないために変換が失敗したことを示す次の例外をスローします。

> ハンドルされていない例外です。 System.InvalidOperationException:LINQ 式 'DbSet<Artist>() .Where(a => a.IsSigned)' を変換できませんでした。 追加情報:エンティティ型 'Artist' のメンバー 'IsSigned' の変換に失敗しました。 指定されたメンバーがマップされていない可能性があります。 変換できる形式でクエリを書き直すか、AsEnumerable()、AsAsyncEnumerable()、ToList()、または ToListAsync() のいずれかの呼び出しを挿入して、クライアント評価に明示的に切り替えてください。 詳細については、「 https://go.microsoft.com/fwlink/?linkid=2101038 」を参照してください。

同様に、カルチャに依存するセマンティクスを使用して文字列の比較を変換しようとしたときに、より適切な例外メッセージが生成されるようになりました。 たとえば、次のクエリは `StringComparison.CurrentCulture` を使用しようとします。

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

EF Core では、次の例外がスローされるようになりました。

> ハンドルされていない例外です。 System.InvalidOperationException:LINQ 式 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' を変換できませんでした。 追加情報:'StringComparison' 引数を受け取る 'string.Equals' メソッドの変換はサポートされていません。 詳細については、「 https://go.microsoft.com/fwlink/?linkid=2129535 」を参照してください。 変換できる形式でクエリを書き直すか、AsEnumerable()、AsAsyncEnumerable()、ToList()、または ToListAsync() のいずれかの呼び出しを挿入して、クライアント評価に明示的に切り替えてください。 詳細については、「 https://go.microsoft.com/fwlink/?linkid=2101038 」を参照してください。

### <a name="specify-transaction-id"></a>トランザクション ID を指定する

この機能は、[@Marusyk](https://github.com/Marusyk) によってコミュニティから提供されました。 投稿に感謝します。

EF Core では、呼び出し全体のトランザクションの相関関係に対するトランザクション ID が公開されます。 この ID は通常、トランザクションの開始時に EF Core によって設定されます。 アプリケーションがトランザクションを代わりに開始する場合、この機能により、アプリケーションが、使用されるすべての場所で正しく関連付けられるように、トランザクション ID を明示的に設定できるようになります。 次に例を示します。

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a>IPAddress マッピング

この機能は、[@ralmsdeveloper](https://github.com/ralmsdeveloper) によってコミュニティから提供されました。 投稿に感謝します。

標準の .NET [IPAddress クラス](/dotnet/api/system.net.ipaddress)が、ネイティブ サポートをまだ持っていないデータベースの文字列型の列に自動的にマップされるようになりました。 たとえば、次のエンティティ型をマップすることを検討してください。

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

SQL Server では、移行によって次のテーブルが作成されます。

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

これで、通常の方法でエンティティを追加できます。

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

結果として生成される SQL では、正規化された IPv4 または IPv6 のアドレスが挿入されます。

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a>スキャフォールディング時の OnConfiguring を除外する

DbContext が既存のデータベースからスキャフォールディングされると、EF Core の既定では、接続文字列を使用して OnConfiguring オーバーロードが作成され、コンテキストがすぐに使用できるようになります。 ただし、これは、OnConfiguring を持つ部分クラスが既に存在する場合や、コンテキストを他の方法で構成する場合には役に立ちません。

これに対処するため、スキャフォールディング コマンドに、OnConfiguring の生成を省略するように指示できるようになりました。 次に例を示します。

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

または、パッケージ マネージャー コンソールで:

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

[名前付き接続文字列と、ユーザー シークレットのようなセキュリティで保護されたストレージ](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets)を使用することをお勧めします。

### <a name="translations-for-firstordefault-on-strings"></a>文字列の FirstOrDefault の変換

この機能は、[@dvoreckyaa](https://github.com/dvoreckyaa) によってコミュニティから提供されました。 投稿に感謝します。

FirstOrDefault と文字列内の文字に対する類似の演算子が変換されました。 たとえば、次の LINQ クエリ:

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

は、SQL Server を使用すると、次の SQL に変換されます。

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a>CASE ブロックの簡略化

EF Core は、CASE ブロックを使用してより良いクエリを生成するようになりました。 たとえば、次の LINQ クエリ:

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

は、SQL Server では、正式には次のように変換されていました。

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

しかし、今後は次のように変換されます。

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a>Preview 5

### <a name="database-collations"></a>データベースの照合順序

データベースにおける既定の照合順序を EF モデルで指定できるようになりました。 これは、データベースの作成時に照合順序を設定するために生成された移行にフローします。 次に例を示します。

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

次に、移行では、SQL Server 上にデータベースを作成するために次のものが生成されます。

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

特定のデータベース列に使用する照合順序を指定することもできます。 次に例を示します。

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

移行を使用しないものについては、DbContext をスキャフォールディングする際に、照合順序がデータベースからリバースエンジニアリングされるようになりました。

最後に、`EF.Functions.Collate()` では、さまざまな照合順序を使用したアドホック クエリが可能です。 次に例を示します。

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

これにより、SQL Server に対して次のクエリが生成されます。

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

アドホック照合順序はデータベースのパフォーマンスに悪影響を及ぼす可能性があるため、注意して使用する必要があります。

ドキュメントは、イシュー [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) で追跡されます。

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a>引数を IDesignTimeDbContextFactory にフローする

引数は、コマンド ラインから [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1)の `CreateDbContext` メソッドにフローされるようになりました。 たとえば、これが開発ビルドであることを示すために、カスタム引数 (`dev` など) をコマンド ラインで渡すことができます。

```
dotnet ef migrations add two --verbose --dev
```

この引数は次にファクトリにフローされ、そこではコンテキストの作成方法および初期化方法を制御するために使用できます。 次に例を示します。

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

ドキュメントは、イシュー [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) で追跡されます。

### <a name="no-tracking-queries-with-identity-resolution"></a>識別子の解決を使用した追跡なしのクエリ

識別子の解決を実行するように追跡なしのクエリ構成できるようになりました。 たとえば、次のクエリでは、各ブログの主キーが同じである場合でも、投稿ごとに新しいブログ インスタンスが作成されます。

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

ただし、通常は速度が少し低下し、常により多くのメモリを使用することになりますが、1 つのブログ インスタンスだけを確実に作成するように、このクエリを変更することができます。

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

これは、追跡なしのクエリに対してのみ有効です。すべての追跡クエリでは既にこの動作が行われているからです。 また、API レビューに従って、`PerformIdentityResolution` 構文も変更されます。 [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073) を参照してください。

ドキュメントは、イシュー [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) で追跡されます。

### <a name="stored-persisted-computed-columns"></a>格納された (保存された) 計算列

ほとんどのデータベースでは、計算後に計算列の値を格納することができます。 これによってディスク領域が消費されますが、計算列の計算は、その値が取得されるたびではなく、更新時に 1 回だけ行われます。 また、これにより、一部のデータベースについて列のインデックスを作成することもできます。

EF Core 5.0 では、計算列を格納済みとして構成できます。 次に例を示します。

```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a>SQLite の計算列

EF Core では、SQLite データベースの計算列がサポートされるようになりました。

## <a name="preview-4"></a>Preview 4

### <a name="configure-database-precisionscale-in-model"></a>モデルでデータベースの有効桁数または小数点以下桁数を構成する

モデル ビルダーを使用して、プロパティの有効桁数と小数点以下桁数を指定できるようになりました。 次に例を示します。

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

有効桁数と小数点以下桁数は、"decimal(16,4)" のように、完全なデータベースの種類を使用して設定することもできます。

ドキュメントは、イシュー [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527) で追跡されます。

### <a name="specify-sql-server-index-fill-factor"></a>SQL Server インデックスの指定 FILL FACTOR を指定する

SQL Server でインデックスを作成するときに、FILL FACTOR を指定できるようになりました。 次に例を示します。

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>Preview 3

### <a name="filtered-include"></a>フィルター処理されたインクルード

Include メソッドでは、インクルードされるエンティティのフィルター処理がサポートされるようになりました。 次に例を示します。

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

このクエリでは、投稿のタイトルに "チーズ" が含まれている場合にのみ、関連付けられている各投稿と共にブログが返されます。

Skip と Take を使用して、インクルードされるエンティティの数を減らすこともできます。 次に例を示します。

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
このクエリでは、ブログ 1 件あたり最大 5 件の投稿が含まれるブログが返されます。

詳細については、[インクルードに関するドキュメント](xref:core/querying/related-data#filtered-include)を参照してください。

### <a name="new-modelbuilder-api-for-navigation-properties"></a>ナビゲーション プロパティの新しい ModelBuilder API

ナビゲーション プロパティは、主に[リレーションシップを定義する](xref:core/modeling/relationships)ときに構成されます。 ただし、ナビゲーション プロパティに追加の構成が必要な場合は、新しい `Navigation` メソッドを使用できます。 たとえば、フィールドが規約によって見つからない場合にナビゲーションにバッキング フィールドを設定するには、次を実行します。

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

`Navigation` API は、リレーションシップの構成に代わるものではないことに注意してください。 代わりに、既に検出または定義されたリレーションシップで、ナビゲーション プロパティの追加構成を行うことができます。

[ナビゲーション プロパティの構成に関するドキュメント](xref:core/modeling/relationships#configuring-navigation-properties)を参照してください。

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>名前空間と接続文字列の新しいコマンドライン パラメーター

移行とスキャフォールディングでは、コマンド ラインで名前空間を指定できるようになりました。 たとえば、別の名前空間にコンテキスト クラスとモデル クラスを配置するデータベースをリバース エンジニアリングするには、次を実行します。

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

詳細については、「[移行](xref:core/managing-schemas/migrations/index#namespaces)」と「[リバース エンジニアリング](xref:core/managing-schemas/scaffolding#directories-and-namespaces)」のドキュメントを参照してください。

---
また、接続文字列を `database-update` コマンドに渡すことができるようになりました。

```dotnetcli
dotnet ef database update --connection "connection string"
```

詳細については、[ツールのドキュメント](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)を参照してください。

VS パッケージ マネージャー コンソールで使用される PowerShell コマンドにも、同等のパラメーターが追加されています。

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors が返された

パフォーマンス上の理由から、EF では、データベースから値を読み取るときに追加の null チェックは行われません。 これにより、予期しない null が検出された場合に、根本原因を突き止めることが困難な例外が発生する可能性があります。

`EnableDetailedErrors` を使用すると、クエリに null チェックがさらに追加されます。パフォーマンスのオーバーヘッドが小さいため、これらのエラーでは、根本原因まで簡単に追跡できるようになります。

次に例を示します。

```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

ドキュメントは、イシュー [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955) で追跡されます。

### <a name="cosmos-partition-keys"></a>Cosmos のパーティション キー

指定されたクエリに使用するパーティション キーをクエリで指定できるようになりました。 次に例を示します。

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

ドキュメントは、イシュー [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) で追跡されます。

### <a name="support-for-the-sql-server-datalength-function"></a>SQL Server DATALENGTH 関数のサポート

これには、新しい `EF.Functions.DataLength` メソッドを使用してアクセスできます。 次に例を示します。

```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a>Preview 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>C# 属性を利用し、プロパティ バッキング フィールドを指定する

C# 属性を利用し、プロパティのバッキング フィールドを指定できるようになりました。 この属性を利用することで、EF Core では、バッキング フィールドが自動的に検出できないときでさえ、通常のようにバッキング フィールドとの間で読み書きできます。 次に例を示します。

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

ドキュメントは、イシュー [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) で追跡されます。

### <a name="complete-discriminator-mapping"></a>完全な識別子マッピング

EF Core では、[継承階層の TPH マッピング](xref:core/modeling/inheritance)のために識別子列が使用されます。 識別子に利用できるすべての値が EF Core で認識されている限り、パフォーマンスを一部改善できます。 EF Core 5.0 にはこのような機能強化が実装されました。

たとえば、EF Core の前バージョンでは、ある階層内のすべての型を返すクエリにこの SQL が常に生成されました。

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

EF Core 5.0 では現在、完全な識別子マッピングが構成されたとき、次が生成されます。

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

プレビュー 3 以降、これが既定の動作になります。

### <a name="performance-improvements-in-microsoftdatasqlite"></a>Microsoft.Data.Sqlite のパフォーマンス向上

SQLIte でパフォーマンスが 2 点改善されました。

* GetBytes、GetChars、GetTextReader でバイナリ データや文字列データを読み出す作業が SqliteBlob とストリームを利用することで一層効率的になりました。
* SqliteConnection の初期化がゆっくりになりました。

これらの機能強化は ADO.NET Microsoft.Data.Sqlite プロバイダーにあり、そのため、EF Core の外でもパフォーマンスが改善されます。

## <a name="preview-1"></a>Preview 1

### <a name="simple-logging"></a>シンプルなログ

この機能により、EF6 の `Database.Log` に似た機能が追加されます。 つまり、外部のログ記録フレームワークを構成せずに、EF Core からログを簡単に取得できるようになります。

暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。

追加のドキュメントは、イシュー [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) で追跡されます。

### <a name="simple-way-to-get-generated-sql"></a>生成された SQL の取得方法がシンプルに

EF Core 5.0 では、LINQ クエリの実行時に EF Core によって生成される SQL を返す `ToQueryString` 拡張メソッドが導入されています。

暫定版ドキュメントは、[2020 年 1 月 9 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)に含まれています。

追加のドキュメントは、イシュー [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) で追跡されます。

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>エンティティにキーがないことを示すために C# 属性を使用

新しい `KeylessAttribute` を使用したキーを持たないようにエンティティ タイプを構成できるようになりました。 次に例を示します。

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

ドキュメントは、イシュー [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) で追跡されます。

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a>初期化された DbContext で接続または接続文字列が変更可能に

接続や接続文字列を使用しなくても、DbContext インスタンスを簡単に作成できるようになりました。 また、コンテキスト インスタンスで接続または接続文字列をミュートできるようになりました。 この機能により、同じコンテキスト インスタンスを異なるデータベースに動的に接続できるようになります。

ドキュメントは、イシュー [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) で追跡されます。

### <a name="change-tracking-proxies"></a>変更追跡のプロキシ

EF Core で、[INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) および [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) を自動的に実装するランタイム プロキシを生成できるようになりました。 これにより、エンティティ プロパティの値の変更が EF Core に直接報告されるため、変更をスキャンする必要がなくなります。 ただし、プロキシには独自の制限のセットが付属しているため、すべてのユーザーが使用できるわけではありません

ドキュメントは、イシュー [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) で追跡されます。

### <a name="enhanced-debug-views"></a>強化されたデバッグ ビュー

デバッグ ビューで、イシューのデバッグ時に EF Core の内部を簡単に確認できます。 モデルのデバッグ ビューは少し前に実装されました。 EF Core 5.0 では、モデル ビューを読みやすくし、状態マネージャーの追跡対象エンティティの新しいデバッグ ビューを追加しました。

暫定版のドキュメントは、[2019 年 12 月 12 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)に含まれています。

追加のドキュメントは、イシュー [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) で追跡されます。

### <a name="improved-handling-of-database-null-semantics"></a>データベースの null セマンティクスの処理の向上

リレーショナル データベースは通常、NULL を不明な値として扱うため、他の NULL とは等しくありません。 C# では、null は、他の null と等しいかどうかを比較する定義済みの値として扱われます。 EF Core は、既定で C# の null セマンティクスを使用できるようにクエリを変換します。 EF Core 5.0 では、この変換の効率が大幅に向上します。

ドキュメントは、イシュー [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) で追跡されます。

### <a name="indexer-properties"></a>インデクサーのプロパティ

EF Core 5.0 では、C# インデクサー プロパティのマッピングがサポートされています。 これらのプロパティにより、エンティティはプロパティ バッグとして機能し、列がバッグの名前付きプロパティにマップされます。

ドキュメントは、イシュー [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) で追跡されます。

### <a name="generation-of-check-constraints-for-enum-mappings"></a>列挙型マッピングの CHECK 制約の生成

EF Core 5.0 の移行で、列挙型プロパティのマッピングに CHECK 制約を生成できるようになりました。 次に例を示します。

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

ドキュメントは、イシュー [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) で追跡されます。

### <a name="isrelational"></a>IsRelational

既存の `IsSqlServer`、`IsSqlite`、`IsInMemory` に加えて、新しい `IsRelational` メソッドが追加されました。 このメソッドは、DbContext がリレーショナル データベース プロバイダーを使用しているかどうかをテストするために使用できます。 次に例を示します。

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

ドキュメントは、イシュー [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) で追跡されます。

### <a name="cosmos-optimistic-concurrency-with-etags"></a>ETag を使用した Cosmos オプティミスティック同時実行制御

Azure Cosmos DB データベース プロバイダーで、Etag を使用したオプティミスティック同時実行制御がサポートされるようになりました。 OnModelCreating のモデル ビルダーを使用して ETag を構成します。

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

SaveChanges はコンカレンシーの競合に対して `DbUpdateConcurrencyException` をスローします。これを[処理して](xref:core/saving/concurrency)、たとえば再試行を実装することができます。

ドキュメントは、イシュー [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) で追跡されます。

### <a name="query-translations-for-more-datetime-constructs"></a>より多くの DateTime コンストラクトに対するクエリ変換

新しい DateTime のコンストラクトが含まれるクエリを変換できるようになりました。

さらに、次の SQL Server 関数がマップされました。

* DateDiffWeek
* DateFromParts

次に例を示します。

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。

### <a name="query-translations-for-more-byte-array-constructs"></a>より多くのバイト配列コンストラクトに対するクエリ変換

byte[] プロパティで Contains、Length、SequenceEqual などを使用するクエリが SQL に変換されるようになりました。

暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。

追加のドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。

### <a name="query-translation-for-reverse"></a>Reverse のクエリ変換

`Reverse` を使用したクエリが変換されるようになりました。 次に例を示します。

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。

### <a name="query-translation-for-bitwise-operators"></a>ビット処理演算子のクエリ変換

ビット処理演算子を使用するクエリが、次のようなケースでも変換されるようになりました。

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。

### <a name="query-translation-for-strings-on-cosmos"></a>Cosmos の文字列のクエリ変換

文字列メソッド Contains、StartsWith、EndsWith を使用するクエリが、Azure Cosmos DB プロバイダーの使用時に変換されるようになりました。

ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。
