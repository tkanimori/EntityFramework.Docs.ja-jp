---
title: EF Core 2.0 の新機能 - EF Core
author: divega
ms.date: 02/20/2018
ms.assetid: 2CB5809E-0EFB-44F6-AF14-9D5BFFFBFF9D
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: b5ac31722f49589f1494a3d8d1c8a7011a4cf9ce
ms.sourcegitcommit: a013e243a14f384999ceccaf9c779b8c1ae3b936
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463270"
---
# <a name="new-features-in-ef-core-20"></a>EF Core 2.0 の新機能

## <a name="net-standard-20"></a>.NET Standard 2.0
EF Core が .NET Standard 2.0 対応になりました。つまり、.NET Core 2.0、.NET Framework 4.6.1、および .NET Standard 2.0 を実装するその他のライブラリで使えるようになりました。
サポート対象の詳細については、「[Supported .NET Implementations](../platforms/index.md)」 (サポートされている .NET 実装) を参照してください。

## <a name="modeling"></a>モデリング

### <a name="table-splitting"></a>テーブル分割

2 つまたはそれ以上のエンティティ型を同じテーブルにマッピングできるようになりました。そこで主キー列が共有され、各行が 2 つまたはそれ以上のエンティティに対応します。

テーブル分割を利用するには、テーブルを共有するすべてのエンティティ型の間で依存リレーションシップ (外部キー プロパティが主キーを形成する) を構成する必要があります。

``` csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

### <a name="owned-types"></a>所有されている型

所有されているエンティティ型は、別の所有されているエンティティ型と同じ CLR 型を共有できます。ただし、CLR 型だけでは識別できないため、別のエンティティ型からそれへのナビゲーションが必要になります。 定義となるナビゲーションを含むエンティティは所有者です。 所有者に問い合わせるとき、所有されている型が既定で含まれます。

慣例によって、所有されている型に対してシャドウ主キーが作成され、テーブル分割を利用し、同じテーブルに所有者としてマッピングされます。 それによって、所有されている型を EF6 の複合型の使用方法と同じように使用できます。

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, cb =>
    {
        cb.OwnsOne(c => c.BillingAddress);
        cb.OwnsOne(c => c.ShippingAddress);
    });

public class Order
{
    public int Id { get; set; }
    public OrderDetails OrderDetails { get; set; }
}

public class OrderDetails
{
    public StreetAddress BillingAddress { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```
この機能に関する詳細は、[所有エンティティ型のセクション](xref:core/modeling/owned-entities)をご覧ください。

### <a name="model-level-query-filters"></a>モデルレベルのクエリ フィルター

EF Core 2.0 には、モデルレベルのクエリ フィルターと呼んでいる新しい機能があります。 この機能では、(通常 OnModelCreating の) メタデータ モデルのエンティティ型で直接、LINQ クエリ述語 (通常 LINQ Where クエリ演算子に渡されるブール式) を定義できます。 このようなフィルターは、このようなエンティティ型が関係するあらゆる LINQ クエリに自動的に適用されます。これには間接的に参照されるエンティティ型が含まれます。たとえば、Include を利用して参照されます。あるいは、ナビゲーション プロパティが直接参照されます。 この機能の一般的な用途は次のようになっています。

- 論理削除 - エンティティ型が IsDeleted プロパティを定義します。
- マルチテナント - エンティティ型が TenantId プロパティを定義します。

次の単純なサンプルでは、上記の 2 つのシナリオに対してこの機能が使われています。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    public int TenantId { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>().HasQueryFilter(
            p => !p.IsDeleted
            && p.TenantId == this.TenantId );
    }
}
```
エンティティ型 ```Post``` のインスタンスに対してマルチテナントと論理削除を実行するモデルレベル フィルターを定義します。 DbContext インスタンス レベル プロパティ ```TenantId``` の使用に注目してください。 モデルレベル フィルターは、正しいコンテキスト インスタンス (つまり、クエリを実行しているコンテキスト インスタンス) の値を使用します。

フィルターは、IgnoreQueryFilters() 演算子を利用し、個々の LINQ クエリに対して無効にできます。

#### <a name="limitations"></a>制限事項

- ナビゲーション参照は許可されていません。 この機能はフィードバックに応じて追加される可能性があります。
- フィルターは、階層のエンティティ型ルートでのみ定義できます。

### <a name="database-scalar-function-mapping"></a>データベース スカラー関数マッピング

EF Core 2.0 には、[Paul Middleton](https://github.com/pmiddleton) の重要な貢献が不可欠でした。それは、データベース スカラー関数のメソッド スタブへのマッピングを可能にするものです。マッピングにより、LINQ クエリで使用したり、SQL に変換したりすることができます。

この機能の使用方法を簡単に説明すると次のようになります。

`DbContext` で静的メソッドを宣言し、それに `DbFunctionAttribute` で注釈を付けます。

``` csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new Exception();
    }
}
```

このようなメソッドが自動的に登録されます。 登録されると、LINQ クエリでのメソッドへの呼び出しが SQL での関数呼び出しに変換されます。

``` csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

次のことに注意してください。

- 慣例では、SQL の生成時、関数 (この場合はユーザー定義関数) の名前としてメソッドの名前が使用されるが、メソッド登録では名前とスキーマをオーバーライドできる
- 現在のところ、スカラー関数にのみ対応している
- データベースにマップされた関数を作成する必要があります。 EF Core の移行では作成が行われません

### <a name="self-contained-type-configuration-for-code-first"></a>Code First の自己完結型構成

EF6 では、*EntityTypeConfiguration* から派生することで、特定のエンティティ型の Code First 構成をカプセル化できました。 EF Core 2.0 では、このパターンが復活します。

``` csharp
class CustomerConfiguration : IEntityTypeConfiguration<Customer>
{
  public void Configure(EntityTypeBuilder<Customer> builder)
  {
     builder.HasKey(c => c.AlternateKey);
     builder.Property(c => c.Name).HasMaxLength(200);
   }
}

...
// OnModelCreating
builder.ApplyConfiguration(new CustomerConfiguration());
```

## <a name="high-performance"></a>高パフォーマンス

### <a name="dbcontext-pooling"></a>DbContext プール

ASP.NET Core アプリケーションで EF Core を使用する基本パターンは通常、カスタム DbContext 型を依存関係挿入システムに登録し、その後、コントローラーのコンストラクター パラメーターからその型のインスタンスを取得するというパターンになります。 つまり、DbContext の新しいインスタンスが要求ごとに作成されます。

バージョン 2.0 では、依存関係挿入でカスタム DbContext 型を登録する新しい方法を導入しました。再利用可能な DbContext インスタンスのプールが透過的に導入されます。 DbContext プールを使用するには、サービス登録で ```AddDbContext``` の代わりに ```AddDbContextPool``` を使用します。

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

この方法を利用する場合、コントローラーが DbContext インスタンスを要求した時点で、プールに利用できるインスタンスがないか最初に確認されます。 要求処理が終わると、インスタンス上のあらゆる状態がリセットされ、インスタンス自体はプールに戻ります。

これは概念としては、DbContext プロバイダーの接続プールと似ており、DbContext インスタンスの初期化コストが一部節約されるという長所があります。

### <a name="limitations"></a>制限事項

この新しい方法では、DbContext の ```OnConfiguring()``` メソッドでできることにいくつかの制限があります。

> [!WARNING]  
> 派生した DbContext クラス (要求間で共有できない) で独自の状態を維持する場合 (プライベート フィールドなど)、DbContext プールを使用しないでください。 EF Core は、DbContext インスタンスをプールに追加する前に認識された状態のみをリセットします。

### <a name="explicitly-compiled-queries"></a>明示的にコンパイルされたクエリ

これは 2 つ目のオプトイン パフォーマンス機能であり、大規模なシナリオで利点が得られるように設計されています。

以前のバージョンの EF や LINQ to SQL では、手動または明示的にコンパイルされたクエリ API を利用できました。アプリケーションでは、1 回だけ計算し、何回も実行できるよう、クエリの変換をキャッシュできました。

一般的に、EF Core は、ハッシュ後のクエリ式に基づいてクエリを自動的にコンパイルし、キャッシュできますが、このメカニズムを利用し、ハッシュとキャッシュ参照の計算をバイパスすることでパフォーマンスを少し上げることができます。アプリケーションは、デリゲートを呼び出すことで、コンパイル済みのクエリを利用できます。

``` csharp
// Create an explicitly compiled query
private static Func<CustomerContext, int, Customer> _customerById =
    EF.CompileQuery((CustomerContext db, int id) =>
        db.Customers
            .Include(c => c.Address)
            .Single(c => c.Id == id));

// Use the compiled query by invoking it
using (var db = new CustomerContext())
{
   var customer = _customerById(db, 147);
}
```

## <a name="change-tracking"></a>変更追跡

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a>Attach で新規および既存のエンティティのグラフを追跡可能

EF Core では、さまざまメカニズムを利用し、キー値を自動生成できます。 この機能を使用するとき、キー プロパティが CLR 既定の場合 (通常はゼロか null)、値が生成されます。 つまり、エンティティのグラフを `DbContext.Attach` または `DbSet.Attach` に渡すことができます。EF Core は、既にキーが設定されているエンティティを `Unchanged` としてマークします。キーが設定されていないエンティティは `Added` としてマークされます。 それにより、生成されたキーを使用するとき、新規と既存の混合エンティティのグラフを簡単に添付できるようになります。 `DbContext.Update` と `DbSet.Update` は同じように動作しますが、キーが設定されているエンティティは `Unchanged` ではなく `Modified` としてマークされます。

## <a name="query"></a>クエリ

### <a name="improved-linq-translation"></a>強化された LINQ 変換

より多くのクエリを正常に実行できるようになりました。(メモリではなく) データベースで評価されるロジックが増え、データベースから取得できないデータが減りました。

### <a name="groupjoin-improvements"></a>GroupJoin の改善

これにより、グループ結合に対して生成される SQL が改善されます。 グループ結合は、大抵の場合、オプションのナビゲーション プロパティでのサブクエリの結果です。

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a>FromSql と ExecuteSqlCommand の文字列補間

C# 6 では文字列補間が導入されました。この機能では、C# 式を文字列リテラルに直接埋め込むことができます。実行時、文字列が効率的に構築されます。 EF Core 2.0 では、RAW SQL 文字列を受け取る 2 つのプライマリ API、```FromSql``` と ```ExecuteSqlCommand``` に、補間文字列の特殊なサポートを追加しました。 この新しいサポートにより、'安全な' 方法で C# 文字列補間を使用できます。 すなわち、実行時に SQL を動的に構築するときに起こりうる SQL 挿入のよくある間違いを防ぎます。

次に例を示します。

``` csharp
var city = "London";
var contactTitle = "Sales Representative";

using (var context = CreateContext())
{
    context.Set<Customer>()
        .FromSql($@"
            SELECT *
            FROM ""Customers""
            WHERE ""City"" = {city} AND
                ""ContactTitle"" = {contactTitle}")
            .ToArray();
  }
```

この例では、2 つの変数が SQL 形式文字列に埋め込まれています。 EF Core が次の SQL を生成します。

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a>EF.Functions.Like()

EF Core またはプロバイダーが利用できる EF.Functions プロパティを追加しました。このプロパティは、LINQ クエリで呼び出せるようにデータベースの関数や演算子にマッピングされるメソッドを定義します。 このようなメソッドの最初の例が Like() です。

``` csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%")
    select c;
```

Like() にはインメモリ実装が付随することに注目してください。インメモリ データベースに対して作業するときやクライアント側で述語を評価する必要があるときに便利です。

## <a name="database-management"></a>データベース管理

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a>DbContext スキャフォールディングの複数形化フック

EF Core 2.0 では、新しい *IPluralizer* サービスが導入されました。エンティティ型の名前を単数形化し、DbSet の名前を複数形化するために利用されます。 既定の実装は no-op (操作なし) です。独自のプルーラライザー (複数形にするもの) を簡単にプラグインできるフックに過ぎません。

開発者が独自のプルーラライザーをフックインすると次のようになります。

``` csharp
public class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
    {
        services.AddSingleton<IPluralizer, MyPluralizer>();
    }
}

public class MyPluralizer : IPluralizer
{
    public string Pluralize(string name)
    {
        return Inflector.Inflector.Pluralize(name) ?? name;
    }

    public string Singularize(string name)
    {
        return Inflector.Inflector.Singularize(name) ?? name;
    }
}
```

## <a name="others"></a>Others

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a>ADO.NET SQLite プロバイダーを SQLitePCL.raw に移動
これにより、Microsoft.Data.Sqlite で、さまざまプラットフォームでネイティブ SQLite バイナリを配信するためのソリューションがより堅牢になります。

### <a name="only-one-provider-per-model"></a>モデルごとにプロバイダーは 1 つ
プロバイダーがモデルと対話するしくみが大幅に増強され、規則、注釈、fluent API がさまざまプロバイダーと連動するしくみが単純になりました。

EF Core 2.0 は、使用されるプロバイダーごとに異なる [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) をビルドするようになりました。 これは通常、アプリケーションに対して透過的です。 下位レベルのメタデータ API が単純になり、*一般的なリレーショナル メタデータ コンセプト*へのあらゆるアクセスが `.SqlServer` や `.Sqlite` などではなく、`.Relational` の呼び出しで常に行われます。

### <a name="consolidated-logging-and-diagnostics"></a>統合されたログと診断

ログ記録 (ILogger に基づく) メカニズムと診断 (DiagnosticSource に基づく) メカニズムで共有されるコードが増えました。

ILogger に送信されるメッセージのイベント ID が 2.0 で変更されました。 イベント ID が EF Core コード全体で一意になりました。 これらのメッセージはまた、たとえば MVC で使用される構造化されたログ記録の標準パターンに従います。

ロガー カテゴリも変更されました。 現在、[DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs) 経由でアクセスされる既知のカテゴリ セットがあります。

DiagnosticSource イベントでは、それに対応する `ILogger` メッセージと同じイベント ID 名が使用されるようになりました。
