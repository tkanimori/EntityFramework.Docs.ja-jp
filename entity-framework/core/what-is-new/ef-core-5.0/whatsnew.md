---
title: EF Core 5.0 の新機能
description: EF Core 5.0 の新機能の概要
author: ajcvickers
ms.date: 09/10/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: 64b72ba2a6f752b9d71ea9b64ab08f4cf92ef03d
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129279"
---
# <a name="whats-new-in-ef-core-50"></a>EF Core 5.0 の新機能

以下のリストには、EF Core 5.0 の主な新機能が含まれています。 リリースの問題の完全なリストについては、[問題の追跡ツール](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A5.0.0)を参照してください。

メジャー リリースである EF Core 5.0 には、いくつかの[破壊的変更](xref:core/what-is-new/ef-core-5.0/breaking-changes)も含まれています。これは、既存のアプリケーションに悪影響を及ぼす可能性がある API の強化または動作変更です。

## <a name="many-to-many"></a>多対多

結合テーブルを明示的にマッピングしなくても、EF Core 5.0 によって多対多のリレーションシップがサポートされています。

たとえば、次のようなエンティティ型を考えてみます。

```csharp
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public ICollection<Tag> Tags { get; set; }
}

public class Tag
{
    public int Id { get; set; }
    public string Text { get; set; }
    public ICollection<Post> Posts { get; set; }
}
```

EF Core 5.0 では、規約により、多対多として認識され、データベース内に `PostTag` 結合テーブルが自動的に作成されます。 明示的に結合テーブルを参照せずに、データのクエリや更新を実行できるため、コードを大幅に簡素化することができます。 この場合でも、必要に応じて、結合テーブルをカスタマイズし、明示的にクエリを実行できます。

詳細については、[多対多に関する完全なドキュメントを参照してください](xref:core/modeling/relationships#many-to-many)。

## <a name="split-queries"></a>分割クエリ

EF Core 3.0 以降、EF Core では、LINQ クエリごとに 1 つの SQL クエリが常に生成されます。 これにより、使用中のトランザクション モードの制約内で返されるデータの整合性が確保されます。 ただし、クエリで `Include` またはプロジェクションを使用して複数の関連コレクションを戻すと、この処理が非常に遅くなる可能性があります。

EF Core 5.0 では、関連するコレクションを含む単一の LINQ クエリを複数の SQL クエリに分割できるようになりました。 これにより、パフォーマンスを大幅に向上させることができますが、2 つのクエリ間でデータが変更された場合に返される結果に不整合が生じる可能性があります。 シリアル化可能な、またはスナップショットのトランザクションを使用すると、これを軽減し、分割されたクエリとの整合性を維持できますが、他のパフォーマンス コストと動作の違いが生じる可能性があります。

たとえば、`Include` を使用して 2 つのレベルの関連コレクションを取得するクエリについて考えてみます。

```csharp
var artists = context.Artists
    .Include(e => e.Albums)
    .ToList();
```

既定では、SQLite プロバイダーの使用時に、EF Core によって次の SQL が生成されます。

```sql
SELECT a."Id", a."Name", a0."Id", a0."ArtistId", a0."Title"
FROM "Artists" AS a
LEFT JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id", a0."Id"
```

分割クエリを使用すると、代わりに次の SQL が生成されます。

```sql
SELECT a."Id", a."Name"
FROM "Artists" AS a
ORDER BY a."Id"

SELECT a0."Id", a0."ArtistId", a0."Title", a."Id"
FROM "Artists" AS a
INNER JOIN "Album" AS a0 ON a."Id" = a0."ArtistId"
ORDER BY a."Id"
```

分割クエリを有効にするには、新しい `AsSplitQuery` 演算子を LINQ クエリ内の任意の場所に、またはグローバルにモデルの `OnConfiguring` に配置します。 詳細については、[分割クエリに関する完全なドキュメントを参照してください](xref:core/querying/single-split-queries)。

## <a name="simple-logging-and-improved-diagnostics"></a>シンプルなログと強化された診断

EF Core 5.0 では、新しい `LogTo` メソッドを使用してログを簡単に設定できるようになりました。 次の例では、EF Core によって生成されたすべての SQL を含め、ログ メッセージがコンソールに書き込まれます。

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder.LogTo(Console.WriteLine);
```

さらに、任意の LINQ クエリで `ToQueryString` を呼び出して、クエリで実行する SQL を取得できるようになりました。

```csharp
Console.WriteLine(
    ctx.Artists
    .Where(a => a.Name == "Pink Floyd")
    .ToQueryString());
```

最後に、内部の詳細ビューを提供する拡張 `DebugView` プロパティに合わせてさまざまな EF Core の型を使用できるようになりました。 たとえば、<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> を調べて、特定の時点でどのエンティティが追跡されるかを正確に把握することができます。

詳細については、[ログとインターセプトに関するドキュメントを参照してください](xref:core/logging-events-diagnostics/index)。

## <a name="filtered-include"></a>フィルター処理されたインクルード

`Include` メソッドで、インクルードされるエンティティのフィルター処理がサポートされるようになりました。

```csharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

このクエリでは、投稿のタイトルに "チーズ" が含まれている場合にのみ、関連付けられている各投稿と共にブログが返されます。

詳細については、[分割クエリに関する完全なドキュメントを参照してください](xref:core/querying/related-data/eager#filtered-include)。

## <a name="table-per-type-tpt-mapping"></a>Table-Per-Type (TPT) のマッピング

EF Core の既定では、.NET 型の継承階層が 1 つのデータベース テーブルにマップされます。 これは、Table-Per-Hierarchy (TPH) のマッピングと呼ばれます。 EF Core 5.0 を使用すると、継承階層の各 .NET 型を別のデータベース テーブルにマップすることもできます。これは Table-Per-Type (TPT) のマッピングと呼ばれます。

たとえば、次のようにマップされた階層を持つこのモデルを考えてみましょう。

```csharp
public class Animal
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Cat : Animal
{
    public string EducationLevel { get; set; }
}

public class Dog : Animal
{
    public string FavoriteToy { get; set; }
}
```

TPT を使用すると、階層内の型ごとにデータベース テーブルが作成されます。

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
);
```

詳細については、[TPT に関する完全なドキュメントを参照してください](xref:core/modeling/inheritance)。

## <a name="flexible-entity-mapping"></a>柔軟なエンティティ マッピング

エンティティ型は通常、その型に対してクエリを実行するときに、EF Core によってテーブルまたはビューの内容がプルバックされるようなテーブルまたはビューにマップされます。 EF Core 5.0 では、マッピング オプションが追加され、エンティティを SQL クエリ ("クエリ定義"と呼ばれる)、またはテーブル値関数 (TVF) にマップできます。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>().ToSqlQuery(
        @"SELECT Id, Name, Category, BlogId FROM posts
          UNION ALL
          SELECT Id, Name, ""Legacy"", BlogId from legacy_posts");

    modelBuilder.Entity<Blog>().ToFunction("BlogsReturningFunction");
}
```

さらに、テーブル値関数を DbSet ではなく .NET メソッドにマップしてパラメーターを渡すことができます。このマッピングは、<xref:Microsoft.EntityFrameworkCore.RelationalModelBuilderExtensions.HasDbFunction%2A> で設定できます。

最後に、クエリ時はエンティティをビュー (または関数や定義クエリ) にマップし、更新時にはテーブルにマップできるようになりました。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

## <a name="shared-type-entity-types-and-property-bags"></a>共有型のエンティティ型とプロパティ バッグ

EF Core 5.0 では、同じ CLR 型を複数の異なるエンティティ型にマップできます。このような型は、共有型のエンティティ型と呼ばれます。 この機能では、任意の CLR 型を使用できますが、.NET `Dictionary` により、"プロパティ バッグ" と呼ばれる特に魅力的なユースケースが提供されます。

```csharp
public class ProductsContext : DbContext
{
    public DbSet<Dictionary<string, object>> Products => Set<Dictionary<string, object>>("Product");

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.SharedTypeEntity<Dictionary<string, object>>("Product", b =>
        {
            b.IndexerProperty<int>("Id");
            b.IndexerProperty<string>("Name").IsRequired();
            b.IndexerProperty<decimal>("Price");
        });
    }
}
```

これらのエンティティは、専用の CLR 型を使用して、通常のエンティティ型と同様にクエリおよび更新できます。 詳細については、[プロパティ バッグ](xref:core/modeling/shadow-properties)に関するドキュメントを参照してください。

## <a name="required-11-dependents"></a>必要な一対一の依存

EF Core 3.1 では、一対一のリレーションシップの依存側は常に省略可能と見なされていました。 これは、所有エンティティを使用する場合に最も顕著でした。モデルで必須として構成されている場合でも、所有エンティティのすべての列がデータベースで Null 許容として作成されるためです。

EF Core 5.0 では、所有エンティティへのナビゲーションを必須の依存として構成できます。 次に例を示します。

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Person>(b =>
    {
        b.OwnsOne(e => e.HomeAddress,
            b =>
            {
                b.Property(e => e.City).IsRequired();
                b.Property(e => e.Postcode).IsRequired();
            });
        b.Navigation(e => e.HomeAddress).IsRequired();
    });
}
```

## <a name="dbcontextfactory"></a>DbContextFactory

EF Core 5.0 では、アプリケーションの依存関係挿入 (D.I.) コンテナーに DbContext インスタンスを作成するためのファクトリを登録する `AddDbContextFactory` と `AddPooledDbContextFactory` が導入されています。これは、アプリケーション コードでコンテキスト インスタンスを手動で作成と破棄を行う必要がある場合に便利です。

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

現在、`IDbContextFactory<TContext>` で ASP.NET Core コントローラーなどのアプリケーション サービスを挿入し、それを使用してコンテキスト インスタンスをインスタンス化できます。

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
        => _contextFactory = contextFactory;

    public void DoSomeThing()
    {
        using (var context = _contextFactory.CreateDbContext())
        {
            // ...
        }
    }
}
```

詳細については、[DbContextFactory に関する完全なドキュメントを参照してください](xref:core/dbcontext-configuration/index#using-a-dbcontext-factory-eg-for-blazor)。

## <a name="sqlite-table-rebuilds"></a>SQLite テーブルの再構築

SQLite では、他のデータベースと比べて、スキーマ操作機能がより制限されます。たとえば、既存のテーブルから列を削除することはサポートされていません。 EF Core 5.0 では、自動的に新しいテーブルを作成し、データを古いテーブルからコピーし、古いテーブルを削除して、新しいテーブルを削除して名前を変更することにより、これらの制限を回避します。 これにより、テーブルが "再構築" され、以前はサポートされていなかった移行操作を安全に適用できるようになります。

テーブルの再構築によってサポートされるようになった移行操作の詳細については、[このドキュメント ページを参照してください](xref:core/providers/sqlite/limitations#migrations-limitations)。

## <a name="database-collations"></a>データベースの照合順序

EF Core 5.0 では、データベース、列、またはクエリのレベルでテキスト照合順序を指定するためのサポートが導入されています。 これにより、大文字と小文字の区別やその他のテキストの側面を柔軟に構成でき、かつクエリのパフォーマンスを低下させることもありません。

たとえば、次の例では、SQL Server で大文字と小文字を区別するように `Name` 列を構成し、それに応じて、その列で作成されたインデックスが機能します。

```csharp
modelBuilder
    .Entity<User>()
    .Property(e => e.Name)
    .UseCollation("SQL_Latin1_General_CP1_CS_AS");
```

詳細については、[照合順序と大文字と小文字の区別に関する完全なドキュメントを参照してください](xref:core/miscellaneous/collations-and-case-sensitivity)。

## <a name="event-counters"></a>イベント カウンター

EF Core 5.0 では、アプリケーションのパフォーマンスを追跡し、さまざまな異常を特定するために使用できる[イベント カウンター](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0)が公開されています。 [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) ツールを使用して EF を実行するプロセスにアタッチするだけです。

```console
> dotnet counters monitor Microsoft.EntityFrameworkCore -p 49496

[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                     1,755
    Queries (Total)                                            98,402
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             1
```

詳細については、[イベント カウンターに関する完全なドキュメントを参照してください](xref:core/logging-events-diagnostics/event-counters)。

## <a name="other-features"></a>その他の機能

### <a name="model-building"></a>モデル構築

* [値の比較演算子](xref:core/modeling/value-comparers)をより簡単に構築するためのモデル構築 API が導入されました。
* 計算列を [*stored* または *virtual*](xref:core/modeling/generated-properties#computed-columns) として構成できるようになりました。
* 精度とスケールを [Fluent API を使用して](xref:core/modeling/entity-properties#precision-and-scale)構成できるようになりました。
* [ナビゲーション プロパティ](xref:core/modeling/relationships#configuring-navigation-properties)用の新しいモデル構築 API が導入されました。
* プロパティと同様に、フィールド用の新しいモデル構築 API が導入されました。
* .NET の [PhysicalAddress](/dotnet/api/system.net.networkinformation.physicaladdress) 型と [IPAddress](/dotnet/api/system.net.ipaddress) 型をデータベース文字列の列にマップできるようになりました。
* [新しい `[BackingField]` 属性](xref:core/modeling/backing-field)を使用してバッキング フィールドを構成できるようになりました。
* Null 許容のバッキング フィールドを使用できるようになりました。これにより、CLR 既定値が適切な sentinel 値ではない場合にストア生成の既定値のサポートが向上しました (新しい `bool`)。
* Fluent API を使用する代わりに、エンティティ型に対して新しい `[Index]` 属性を使用してインデックスを指定することができます。
* 新しい `[Keyless]` 属性を使用して、エンティティ型を [キーなしとして](xref:core/modeling/keyless-entity-types)構成できます。
* 既定により、[EF Core では識別子が "*完全*](xref:core/modeling/inheritance#table-per-hierarchy-and-discriminator-configuration)" と見なされるようになりました。つまり、モデル内のアプリケーションによって構成されていない識別子の値は表示されないことが想定されます。 これにより、パフォーマンスが多少向上し、識別子列に不明な値が含まれている場合は無効にすることができます。

### <a name="query"></a>クエリ

* クエリ変換エラーの例外に、失敗の理由についてより明確な説明が含まれるようになり、問題を特定するのに役立ちます。
* 追跡なしのクエリで、[ID の解決](xref:core/querying/tracking#identity-resolution)を実行できるようになりました。これにより、同じデータベース オブジェクトについて複数のエンティティ インスタンスが返されるのを回避できます。
* 条件付き集計で GroupBy のサポートが追加されました (例: `GroupBy(o => o.OrderDate).Select(g => g.Count(i => i.OrderDate != null))`)。
* 集計前に Distinct 演算子をグループ要素に変換するためのサポートが追加されました。
* [`Reverse`](/dotnet/api/system.linq.queryable.reverse) の変換。
* SQL Server の `DateTime` の変換が改善されました (例: [`DateDiffWeek`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateDiffWeek%2A)、[`DateFromParts`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DateFromParts%2A))。
* バイト配列での新しいメソッドの変換 (例: [`Contains`](/dotnet/api/system.linq.enumerable.contains)、[`Length`](/dotnet/api/system.array.length)、[`SequenceEqual`](/dotnet/api/system.linq.enumerable.sequenceequal))。
* いくつかの追加のビット処理演算子 (2 の補数など) の変換。
* 文字列に対する `FirstOrDefault` の変換。
* null セマンティクスに関するクエリ変換が改善され、その結果、クエリの厳密さと効率が向上しました。
* null 値の反映を制御するためにユーザーマップ関数に注釈を付けられるようになりました。この場合も、その結果、クエリの厳密さと効率が向上しました。
* CASE ブロックを含む SQL は、大幅に簡潔になりました。
* 新しい [`EF.Functions.DataLength`](xref:Microsoft.EntityFrameworkCore.SqlServerDbFunctionsExtensions.DataLength%2A) メソッドを使用してクエリで SQL Server [`DATELENGTH`](https://docs.microsoft.com/sql/t-sql/functions/datalength-transact-sql) 関数を呼び出すことができるようになりました。
* `EnableDetailedErrors` を使用すると、[例外にさらに詳細が追加されます](xref:core/logging-events-diagnostics/simple-logging#detailed-query-exceptions)。

### <a name="saving"></a>Saving

* SaveChanges [インターセプト](xref:core/logging-events-diagnostics/interceptors#savechanges-interception)と[イベント](xref:core/logging-events-diagnostics/events)。
* [トランザクション セーブポイント](xref:core/saving/transactions#savepoints)を制御するための API が導入されました。 さらに、EF Core では、`SaveChanges` が呼び出され、トランザクションが既に進行中であるときにセーブポイントを自動的に作成し、障害が発生した場合にそれをロールバックします。
* トランザクション ID をアプリケーションによって明示的に設定できます。これにより、ログやその他の場所でトランザクション イベントの関連付けがより簡単になります。
* バッチ処理のパフォーマンス分析に基づいて、SQL Server の既定の最大バッチ サイズが 42 に変更されました。

### <a name="migrations-and-scaffolding"></a>移行とスキャフォールディング

* テーブルを[移行から除外](xref:core/modeling/entity-types#excluding-from-migrations)できるようになりました。
* 新しい [`dotnet ef migrations list`](xref:core/cli/dotnet#dotnet-ef-migrations-list) コマンドを使用すると、データベースにまだ適用されていない移行が表示されるようになりました (Package Management コンソールでは、[`Get-Migration`](xref:core/cli/powershell#get-migration) が同じ機能を備えています)。
* 移行アプリケーションが失敗したときの処理を改善するために、必要に応じて、移行スクリプトにトランザクション ステートメントが含まれるようになりました。
* マップされていない基底クラスの列が、マップされたエンティティ型の他の列の後に並べ替えられるようになりました。 これは、新しく作成されたテーブルにのみ影響し、既存のテーブルの列順は変更されないことに注意してください。
* 移行生成で、生成される移行がべき等であるかどうか、および出力が即座に実行されるか、またはスクリプトとして生成されるかどうかを認識できるようになりました。
* [移行](xref:core/managing-schemas/migrations/index#namespaces)および[スキャフォールディング](xref:core/managing-schemas/scaffolding#directories-and-namespaces)で名前空間を指定するための新しいコマンドライン パラメーターが追加されました。
* [dotnet ef database update](xref:core/cli/dotnet#dotnet-ef-database-update) コマンドで、接続文字列を指定するための新しい `--connection` パラメーターが受け取られるようになりました。
* 既存のデータベースのスキャフォールディングで、テーブル名が単数化されるようになりました。これにより、`People` および `Addresses` という名前のテーブルは、`Person` および `Address` という名前のエンティティ型にスキャフォールディングされます。 [元のデータベース名はそのまま保持されます](xref:core/managing-schemas/scaffolding#preserving-names)。
* モデルをスキャフォールディングする場合、新しい [`--no-onconfiguring`](xref:core/cli/dotnet#dotnet-ef-dbcontext-scaffold) オプションを使用して、`OnModelConfiguring` を除外するように EF Core に指示することができます。

### <a name="cosmos"></a>Cosmos

* [Cosmos 接続の設定](xref:core/providers/cosmos/index#cosmos-options) が拡張されました。
* オプティミスティック同時実行制御が、[ETag を使用して Cosmos でサポートされる](xref:core/providers/cosmos/index#optimistic-concurrency-with-etags)ようになりました。
* 新しい `WithPartitionKey` メソッドを使用することにより、Cosmos [パーティション キー](xref:core/providers/cosmos/index#partition-keys)をモデルとクエリの両方に含めることができます。
* 文字列メソッド [`Contains`](/dotnet/api/system.string.contains)、[`StartsWith`](/dotnet/api/system.string.startswith)、[`EndsWith`](/dotnet/api/system.string.endswith) が Cosmos 用に変換されるようになりました。
* Cosmos で、C# `is` 演算子が変換されるようになりました。

### <a name="sqlite"></a>Sqlite

* 計算列がサポートされるようになりました。
* GetBytes、GetChars、GetTextReader でバイナリ データや文字列データを読み出す作業が SqliteBlob とストリームを利用することで一層効率的になりました。
* SqliteConnection の初期化がゆっくりになりました。

### <a name="other"></a>その他

* [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) および [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) を自動的に実装する変更追跡プロキシを生成できるようになりました。 これにより、`SaveChanges` が呼び出されたときに変更をスキャンしない、変更追跡の代替方法が提供されます。
* 既に初期化された DbContext で <xref:System.Data.Common.DbConnection> または接続文字列を変更できるようになりました。
* 新しい <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType>.0#Microsoft_EntityFrameworkCore_ChangeTracking_ChangeTracker_Clear) メソッドを使用すると、追跡されたすべてのエンティティの DbContext がクリアされます。 これは、作業単位ごとに有効期間が短い新しいコンテキスト インスタンスを作成するベスト プラクティスを使用している場合には通常不要です。 ただし、DbContext インスタンスの状態をリセットする必要がある場合、この新しい `Clear()` メソッドを使用すると、すべてのエンティティを一括でデタッチするよりも、効率と堅牢性が向上します。
* EF Core コマンド ライン ツールによって、環境変数 `ASPNETCORE_ENVIRONMENT` "_および_" `DOTNET_ENVIRONMENT` が "Development" に自動的に構成されるようになりました。 これにより、汎用ホストを使用する場合のエクスペリエンスと、開発時の ASP.NET Core のエクスペリエンスが一致するようになります。
* カスタム コマンドライン引数を <xref:Microsoft.EntityFrameworkCore.Design.IDesignTimeDbContextFactory%601> に渡すことができます。これにより、アプリケーションで、コンテキストの作成および初期化方法を制御できます。
* インデックス FILL FACTOR を [SQL Server で構成](xref:core/providers/sql-server/indexes#fill-factor)できるようになりました。
* 新しい <xref:Microsoft.EntityFrameworkCore.RelationalDatabaseFacadeExtensions.IsRelational%2A> プロパティを使用して、リレーショナル プロバイダーを使用する場合と非リレーション プロバイダー (InMemory など) を使用する場合を区別できます。
