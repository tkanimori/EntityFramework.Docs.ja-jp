---
title: EF Core 5.0 の新機能
description: EF Core 5.0 の新機能の概要
author: ajcvickers
ms.date: 06/02/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 45d851a4b08a26dda0c24e20c79f42964fa4fae4
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418943"
---
# <a name="whats-new-in-ef-core-50"></a>EF Core 5.0 の新機能

EF Core 5.0 は現在開発中です。
このページには、各プレビューで導入された耳寄りな変更の概要が記載されています。

このページには [EF Core 5.0 のプラン](plan.md)を記載していません。
このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 5.0 のテーマ全体について説明します。

公開されている公式ドキュメントについては、リンクが追加されます。

## <a name="preview-5"></a>Preview 5

### <a name="database-collations"></a>データベースの照合順序

データベースにおける既定の照合順序を EF モデルで指定できるようになりました。
これは、データベースの作成時に照合順序を設定するために生成された移行にフローします。
次に例を示します。

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

次に、移行では、SQL Server 上にデータベースを作成するために次のものが生成されます。

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

特定のデータベース列に使用する照合順序を指定することもできます。
次に例を示します。

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

移行を使用しないものについては、DbContext をスキャフォールディングする際に、照合順序がデータベースからリバースエンジニアリングされるようになりました。

最後に、`EF.Functions.Collate()` では、さまざまな照合順序を使用したアドホック クエリが可能です。
次に例を示します。

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

引数は、コマンド ラインから [IDesignTimeDbContextFactory](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1?view=efcore-3.1)の `CreateDbContext` メソッドにフローされるようになりました。 たとえば、これが開発ビルドであることを示すために、カスタム引数 (`dev` など) をコマンド ラインで渡すことができます。

```
dotnet ef migrations add two --verbose --dev
``` 

この引数は次にファクトリにフローされ、そこではコンテキストの作成方法および初期化方法を制御するために使用できます。
次に例を示します。

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args) 
        => new SomeDbContext(args.Contains("--dev"));
}
```

ドキュメントは、イシュー [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) で追跡されます。

### <a name="no-tracking-queries-with-identity-resolution"></a>識別子の解決を使用した追跡なしのクエリ

識別子の解決を実行するように追跡なしのクエリ構成できるようになりました。
たとえば、次のクエリでは、各ブログの主キーが同じである場合でも、投稿ごとに新しいブログ インスタンスが作成されます。 

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

ただし、通常は速度が少し低下し、常により多くのメモリを使用することになりますが、1 つのブログ インスタンスだけを確実に作成するように、このクエリを変更することができます。

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

これは、追跡なしのクエリに対してのみ有効です。すべての追跡クエリでは既にこの動作が行われているからです。 また、API レビューに従って、`PerformIdentityResolution` 構文も変更されます。
[#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073) を参照してください。

ドキュメントは、イシュー [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) で追跡されます。

### <a name="stored-persisted-computed-columns"></a>格納された (保存された) 計算列

ほとんどのデータベースでは、計算後に計算列の値を格納することができます。
これによってディスク領域が消費されますが、計算列の計算は、その値が取得されるたびではなく、更新時に 1 回だけ行われます。
また、これにより、一部のデータベースについて列のインデックスを作成することもできます。

EF Core 5.0 では、計算列を格納済みとして構成できます。
次に例を示します。
 
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

モデル ビルダーを使用して、プロパティの有効桁数と小数点以下桁数を指定できるようになりました。
次に例を示します。

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

有効桁数と小数点以下桁数は、"decimal(16,4)" のように、完全なデータベースの種類を使用して設定することもできます。 

ドキュメントは、イシュー [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527) で追跡されます。

### <a name="specify-sql-server-index-fill-factor"></a>SQL Server インデックスの指定 FILL FACTOR を指定する

SQL Server でインデックスを作成するときに、FILL FACTOR を指定できるようになりました。
次に例を示します。

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a>Preview 3

### <a name="filtered-include"></a>フィルター処理されたインクルード

Include メソッドでは、インクルードされるエンティティのフィルター処理がサポートされるようになりました。
次に例を示します。

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

このクエリでは、投稿のタイトルに "チーズ" が含まれている場合にのみ、関連付けられている各投稿と共にブログが返されます。

Skip と Take を使用して、インクルードされるエンティティの数を減らすこともできます。
次に例を示します。
 
```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
このクエリでは、ブログ 1 件あたり最大 5 件の投稿が含まれるブログが返されます。

詳細については、[インクルードに関するドキュメント](xref:core/querying/related-data#filtered-include)を参照してください。

### <a name="new-modelbuilder-api-for-navigation-properties"></a>ナビゲーション プロパティの新しい ModelBuilder API

ナビゲーション プロパティは、主に[リレーションシップを定義する](xref:core/modeling/relationships)ときに構成されます。
ただし、ナビゲーション プロパティに追加の構成が必要な場合は、新しい `Navigation` メソッドを使用できます。
たとえば、フィールドが規約によって見つからない場合にナビゲーションにバッキング フィールドを設定するには、次を実行します。

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

`Navigation` API は、リレーションシップの構成に代わるものではないことに注意してください。
代わりに、既に検出または定義されたリレーションシップで、ナビゲーション プロパティの追加構成を行うことができます。

[ナビゲーション プロパティの構成に関するドキュメント](xref:core/modeling/relationships#configuring-navigation-properties)を参照してください。

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a>名前空間と接続文字列の新しいコマンドライン パラメーター 

移行とスキャフォールディングでは、コマンド ラインで名前空間を指定できるようになりました。
たとえば、別の名前空間にコンテキスト クラスとモデル クラスを配置するデータベースをリバース エンジニアリングするには、次を実行します。 

```
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

詳細については、「[移行](xref:core/managing-schemas/migrations/index#namespaces)」と「[リバース エンジニアリング](xref:core/managing-schemas/scaffolding#directories-and-namespaces)」のドキュメントを参照してください。

---
また、接続文字列を `database-update` コマンドに渡すことができるようになりました。

```
dotnet ef database update --connection "connection string"
```

詳細については、[ツールのドキュメント](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)を参照してください。

VS パッケージ マネージャー コンソールで使用される PowerShell コマンドにも、同等のパラメーターが追加されています。

### <a name="enabledetailederrors-has-returned"></a>EnableDetailedErrors が返された

パフォーマンス上の理由から、EF では、データベースから値を読み取るときに追加の null チェックは行われません。
これにより、予期しない null が検出された場合に、根本原因を突き止めることが困難な例外が発生する可能性があります。

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

指定されたクエリに使用するパーティション キーをクエリで指定できるようになりました。
次に例を示します。

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

ドキュメントは、イシュー [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) で追跡されます。

### <a name="support-for-the-sql-server-datalength-function"></a>SQL Server DATALENGTH 関数のサポート

これには、新しい `EF.Functions.DataLength` メソッドを使用してアクセスできます。
次に例を示します。
```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
``` 

## <a name="preview-2"></a>Preview 2

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a>C# 属性を利用し、プロパティ バッキング フィールドを指定する

C# 属性を利用し、プロパティのバッキング フィールドを指定できるようになりました。
この属性を利用することで、EF Core では、バッキング フィールドが自動的に検出できないときでさえ、通常のようにバッキング フィールドとの間で読み書きできます。
次に例を示します。

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

EF Core では、[継承階層の TPH マッピング](/ef/core/modeling/inheritance)のために識別子列が使用されます。
識別子に利用できるすべての値が EF Core で認識されている限り、パフォーマンスを一部改善できます。
EF Core 5.0 にはこのような機能強化が実装されました。

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

この機能により、EF6 の `Database.Log` に似た機能が追加されます。
つまり、外部のログ記録フレームワークを構成せずに、EF Core からログを簡単に取得できるようになります。

暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。

追加のドキュメントは、イシュー [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) で追跡されます。

### <a name="simple-way-to-get-generated-sql"></a>生成された SQL の取得方法がシンプルに

EF Core 5.0 では、LINQ クエリの実行時に EF Core によって生成される SQL を返す `ToQueryString` 拡張メソッドが導入されています。

暫定版ドキュメントは、[2020 年 1 月 9 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)に含まれています。

追加のドキュメントは、イシュー [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) で追跡されます。

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a>エンティティにキーがないことを示すために C# 属性を使用

新しい `KeylessAttribute` を使用したキーを持たないようにエンティティ タイプを構成できるようになりました。
次に例を示します。

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

接続や接続文字列を使用しなくても、DbContext インスタンスを簡単に作成できるようになりました。
また、コンテキスト インスタンスで接続または接続文字列をミュートできるようになりました。
この機能により、同じコンテキスト インスタンスを異なるデータベースに動的に接続できるようになります。

ドキュメントは、イシュー [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) で追跡されます。

### <a name="change-tracking-proxies"></a>変更追跡のプロキシ

EF Core で、[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) および [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) を自動的に実装するランタイム プロキシを生成できるようになりました。
これにより、エンティティ プロパティの値の変更が EF Core に直接報告されるため、変更をスキャンする必要がなくなります。
ただし、プロキシには独自の制限のセットが付属しているため、すべてのユーザーが使用できるわけではありません

ドキュメントは、イシュー [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) で追跡されます。

### <a name="enhanced-debug-views"></a>強化されたデバッグ ビュー

デバッグ ビューで、イシューのデバッグ時に EF Core の内部を簡単に確認できます。
モデルのデバッグ ビューは少し前に実装されました。
EF Core 5.0 では、モデル ビューを読みやすくし、状態マネージャーの追跡対象エンティティの新しいデバッグ ビューを追加しました。

暫定版のドキュメントは、[2019 年 12 月 12 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)に含まれています。

追加のドキュメントは、イシュー [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) で追跡されます。

### <a name="improved-handling-of-database-null-semantics"></a>データベースの null セマンティクスの処理の向上

リレーショナル データベースは通常、NULL を不明な値として扱うため、他の NULL とは等しくありません。
C# では、null は、他の null と等しいかどうかを比較する定義済みの値として扱われます。
EF Core は、既定で C# の null セマンティクスを使用できるようにクエリを変換します。
EF Core 5.0 では、この変換の効率が大幅に向上します。

ドキュメントは、イシュー [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) で追跡されます。

### <a name="indexer-properties"></a>インデクサーのプロパティ

EF Core 5.0 では、C# インデクサー プロパティのマッピングがサポートされています。
これらのプロパティにより、エンティティはプロパティ バッグとして機能し、列がバッグの名前付きプロパティにマップされます。

ドキュメントは、イシュー [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) で追跡されます。

### <a name="generation-of-check-constraints-for-enum-mappings"></a>列挙型マッピングの CHECK 制約の生成

EF Core 5.0 の移行で、列挙型プロパティのマッピングに CHECK 制約を生成できるようになりました。
次に例を示します。

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

ドキュメントは、イシュー [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) で追跡されます。

### <a name="isrelational"></a>IsRelational

既存の `IsSqlServer`、`IsSqlite`、`IsInMemory` に加えて、新しい `IsRelational` メソッドが追加されました。
このメソッドは、DbContext がリレーショナル データベース プロバイダーを使用しているかどうかをテストするために使用できます。
次に例を示します。

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

Azure Cosmos DB データベース プロバイダーで、Etag を使用したオプティミスティック同時実行制御がサポートされるようになりました。
OnModelCreating のモデル ビルダーを使用して ETag を構成します。

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

SaveChanges はコンカレンシーの競合に対して `DbUpdateConcurrencyException` をスローします。これを[処理して](https://docs.microsoft.com/ef/core/saving/concurrency)、たとえば再試行を実装することができます。

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

`Reverse` を使用したクエリが変換されるようになりました。
次に例を示します。

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
