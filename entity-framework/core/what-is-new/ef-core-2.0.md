---
title: EF Core 2.0 の新機能 - EF Core
author: divega
ms.date: 02/20/2018
ms.assetid: 2CB5809E-0EFB-44F6-AF14-9D5BFFFBFF9D
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: a3e066056fc67031060920f5f7763007bdc1d2d3
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672884"
---
# <a name="new-features-in-ef-core-20"></a><span data-ttu-id="c683e-102">EF Core 2.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="c683e-102">New features in EF Core 2.0</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="c683e-103">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="c683e-103">.NET Standard 2.0</span></span>

<span data-ttu-id="c683e-104">EF Core が .NET Standard 2.0 対応になりました。つまり、.NET Core 2.0、.NET Framework 4.6.1、および .NET Standard 2.0 を実装するその他のライブラリで使えるようになりました。</span><span class="sxs-lookup"><span data-stu-id="c683e-104">EF Core now targets .NET Standard 2.0, which means it can work with .NET Core 2.0, .NET Framework 4.6.1, and other libraries that implement .NET Standard 2.0.</span></span>
<span data-ttu-id="c683e-105">サポート対象の詳細については、「[Supported .NET Implementations](../platforms/index.md)」 (サポートされている .NET 実装) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c683e-105">See [Supported .NET Implementations](../platforms/index.md) for more details on what is supported.</span></span>

## <a name="modeling"></a><span data-ttu-id="c683e-106">モデリング</span><span class="sxs-lookup"><span data-stu-id="c683e-106">Modeling</span></span>

### <a name="table-splitting"></a><span data-ttu-id="c683e-107">テーブル分割</span><span class="sxs-lookup"><span data-stu-id="c683e-107">Table splitting</span></span>

<span data-ttu-id="c683e-108">2 つまたはそれ以上のエンティティ型を同じテーブルにマッピングできるようになりました。そこで主キー列が共有され、各行が 2 つまたはそれ以上のエンティティに対応します。</span><span class="sxs-lookup"><span data-stu-id="c683e-108">It is now possible to map two or more entity types to the same table where the primary key column(s) will be shared and each row will correspond to two or more entities.</span></span>

<span data-ttu-id="c683e-109">テーブル分割を利用するには、テーブルを共有するすべてのエンティティ型の間で依存リレーションシップ (外部キー プロパティが主キーを形成する) を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c683e-109">To use table splitting an identifying relationship (where foreign key properties form the primary key) must be configured between all of the entity types sharing the table:</span></span>

``` csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

<span data-ttu-id="c683e-110">この機能の詳細については、[テーブル分割](xref:core/modeling/table-splitting)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c683e-110">Read the [section on table splitting](xref:core/modeling/table-splitting) for more information on this feature.</span></span>

### <a name="owned-types"></a><span data-ttu-id="c683e-111">所有されている型</span><span class="sxs-lookup"><span data-stu-id="c683e-111">Owned types</span></span>

<span data-ttu-id="c683e-112">所有されているエンティティ型は、別の所有されているエンティティ型と同じ .NET 型を共有できます。ただし、.NET 型だけでは識別できないため、別のエンティティ型からそれへのナビゲーションが必要になります。</span><span class="sxs-lookup"><span data-stu-id="c683e-112">An owned entity type can share the same .NET type with another owned entity type, but since it cannot be identified just by the .NET type there must be a navigation to it from another entity type.</span></span> <span data-ttu-id="c683e-113">定義となるナビゲーションを含むエンティティは所有者です。</span><span class="sxs-lookup"><span data-stu-id="c683e-113">The entity containing the defining navigation is the owner.</span></span> <span data-ttu-id="c683e-114">所有者に問い合わせるとき、所有されている型が既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="c683e-114">When querying the owner the owned types will be included by default.</span></span>

<span data-ttu-id="c683e-115">慣例によって、所有されている型に対してシャドウ主キーが作成され、テーブル分割を利用し、同じテーブルに所有者としてマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="c683e-115">By convention a shadow primary key will be created for the owned type and it will be mapped to the same table as the owner by using table splitting.</span></span> <span data-ttu-id="c683e-116">それによって、所有されている型を EF6 の複合型の使用方法と同じように使用できます。</span><span class="sxs-lookup"><span data-stu-id="c683e-116">This allows to use owned types similarly to how complex types are used in EF6:</span></span>

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

<span data-ttu-id="c683e-117">この機能に関する詳細は、[所有エンティティ型のセクション](xref:core/modeling/owned-entities)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="c683e-117">Read the [section on owned entity types](xref:core/modeling/owned-entities) for more information on this feature.</span></span>

### <a name="model-level-query-filters"></a><span data-ttu-id="c683e-118">モデルレベルのクエリ フィルター</span><span class="sxs-lookup"><span data-stu-id="c683e-118">Model-level query filters</span></span>

<span data-ttu-id="c683e-119">EF Core 2.0 には、モデルレベルのクエリ フィルターと呼んでいる新しい機能があります。</span><span class="sxs-lookup"><span data-stu-id="c683e-119">EF Core 2.0 includes a new feature we call Model-level query filters.</span></span> <span data-ttu-id="c683e-120">この機能では、(通常 OnModelCreating の) メタデータ モデルのエンティティ型で直接、LINQ クエリ述語 (通常 LINQ Where クエリ演算子に渡されるブール式) を定義できます。</span><span class="sxs-lookup"><span data-stu-id="c683e-120">This feature allows LINQ query predicates (a boolean expression typically passed to the LINQ Where query operator) to be defined directly on Entity Types in the metadata model (usually in OnModelCreating).</span></span> <span data-ttu-id="c683e-121">このようなフィルターは、このようなエンティティ型が関係するあらゆる LINQ クエリに自動的に適用されます。これには間接的に参照されるエンティティ型が含まれます。たとえば、Include を利用して参照されます。あるいは、ナビゲーション プロパティが直接参照されます。</span><span class="sxs-lookup"><span data-stu-id="c683e-121">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="c683e-122">この機能の一般的な用途は次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="c683e-122">Some common applications of this feature are:</span></span>

- <span data-ttu-id="c683e-123">論理削除 - エンティティ型が IsDeleted プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="c683e-123">Soft delete - An Entity Types defines an IsDeleted property.</span></span>
- <span data-ttu-id="c683e-124">マルチテナント - エンティティ型が TenantId プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="c683e-124">Multi-tenancy - An Entity Type defines a TenantId property.</span></span>

<span data-ttu-id="c683e-125">次の単純なサンプルでは、上記の 2 つのシナリオに対してこの機能が使われています。</span><span class="sxs-lookup"><span data-stu-id="c683e-125">Here is a simple example demonstrating the feature for the two scenarios listed above:</span></span>

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
            && p.TenantId == this.TenantId);
    }
}
```

<span data-ttu-id="c683e-126">エンティティ型 `Post` のインスタンスに対してマルチテナントと論理削除を実行するモデルレベル フィルターを定義します。</span><span class="sxs-lookup"><span data-stu-id="c683e-126">We define a model-level filter that implements multi-tenancy and soft-delete for instances of the `Post` Entity Type.</span></span> <span data-ttu-id="c683e-127">`DbContext` インスタンス レベル プロパティ `TenantId` の使用に注目してください。</span><span class="sxs-lookup"><span data-stu-id="c683e-127">Note the use of a `DbContext` instance-level property: `TenantId`.</span></span> <span data-ttu-id="c683e-128">モデルレベル フィルターは、正しいコンテキスト インスタンス (つまり、クエリを実行しているコンテキスト インスタンス) の値を使用します。</span><span class="sxs-lookup"><span data-stu-id="c683e-128">Model-level filters will use the value from the correct context instance (that is, the context instance that is executing the query).</span></span>

<span data-ttu-id="c683e-129">フィルターは、IgnoreQueryFilters() 演算子を利用し、個々の LINQ クエリに対して無効にできます。</span><span class="sxs-lookup"><span data-stu-id="c683e-129">Filters may be disabled for individual LINQ queries using the IgnoreQueryFilters() operator.</span></span>

#### <a name="limitations"></a><span data-ttu-id="c683e-130">制限事項</span><span class="sxs-lookup"><span data-stu-id="c683e-130">Limitations</span></span>

- <span data-ttu-id="c683e-131">ナビゲーション参照は許可されていません。</span><span class="sxs-lookup"><span data-stu-id="c683e-131">Navigation references are not allowed.</span></span> <span data-ttu-id="c683e-132">この機能はフィードバックに応じて追加される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c683e-132">This feature may be added based on feedback.</span></span>
- <span data-ttu-id="c683e-133">フィルターは、階層のエンティティ型ルートでのみ定義できます。</span><span class="sxs-lookup"><span data-stu-id="c683e-133">Filters can only be defined on the root Entity Type of a hierarchy.</span></span>

### <a name="database-scalar-function-mapping"></a><span data-ttu-id="c683e-134">データベース スカラー関数マッピング</span><span class="sxs-lookup"><span data-stu-id="c683e-134">Database scalar function mapping</span></span>

<span data-ttu-id="c683e-135">EF Core 2.0 には、[Paul Middleton](https://github.com/pmiddleton) の重要な貢献が不可欠でした。それは、データベース スカラー関数のメソッド スタブへのマッピングを可能にするものです。マッピングにより、LINQ クエリで使用したり、SQL に変換したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="c683e-135">EF Core 2.0 includes an important contribution from [Paul Middleton](https://github.com/pmiddleton) which enables mapping database scalar functions to method stubs so that they can be used in LINQ queries and translated to SQL.</span></span>

<span data-ttu-id="c683e-136">この機能の使用方法を簡単に説明すると次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c683e-136">Here is a brief description of how the feature can be used:</span></span>

<span data-ttu-id="c683e-137">`DbContext` で静的メソッドを宣言し、それに `DbFunctionAttribute` で注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="c683e-137">Declare a static method on your `DbContext` and annotate it with `DbFunctionAttribute`:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new NotImplementedException();
    }
}
```

<span data-ttu-id="c683e-138">このようなメソッドが自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="c683e-138">Methods like this are automatically registered.</span></span> <span data-ttu-id="c683e-139">登録されると、LINQ クエリでのメソッドへの呼び出しが SQL での関数呼び出しに変換されます。</span><span class="sxs-lookup"><span data-stu-id="c683e-139">Once registered, calls to the method in a LINQ query can be translated to function calls in SQL:</span></span>

``` csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

<span data-ttu-id="c683e-140">次のことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="c683e-140">A few things to note:</span></span>

- <span data-ttu-id="c683e-141">規則では、SQL の生成時、関数 (この場合はユーザー定義関数) の名前としてメソッドの名前が使用されますが、メソッド登録時に名前とスキーマをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="c683e-141">By convention the name of the method is used as the name of a function (in this case a user-defined function) when generating the SQL, but you can override the name and schema during method registration.</span></span>
- <span data-ttu-id="c683e-142">現在のところ、スカラー関数のみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="c683e-142">Currently only scalar functions are supported.</span></span>
- <span data-ttu-id="c683e-143">データベースにマップされた関数を作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c683e-143">You must create the mapped function in the database.</span></span> <span data-ttu-id="c683e-144">EF Core の移行では作成が行われません。</span><span class="sxs-lookup"><span data-stu-id="c683e-144">EF Core migrations will not take care of creating it.</span></span>

### <a name="self-contained-type-configuration-for-code-first"></a><span data-ttu-id="c683e-145">Code First の自己完結型構成</span><span class="sxs-lookup"><span data-stu-id="c683e-145">Self-contained type configuration for code first</span></span>

<span data-ttu-id="c683e-146">EF6 では、*EntityTypeConfiguration* から派生することで、特定のエンティティ型の Code First 構成をカプセル化できました。</span><span class="sxs-lookup"><span data-stu-id="c683e-146">In EF6 it was possible to encapsulate the code first configuration of a specific entity type by deriving from *EntityTypeConfiguration*.</span></span> <span data-ttu-id="c683e-147">EF Core 2.0 では、このパターンが復活します。</span><span class="sxs-lookup"><span data-stu-id="c683e-147">In EF Core 2.0 we are bringing this pattern back:</span></span>

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

## <a name="high-performance"></a><span data-ttu-id="c683e-148">高パフォーマンス</span><span class="sxs-lookup"><span data-stu-id="c683e-148">High Performance</span></span>

### <a name="dbcontext-pooling"></a><span data-ttu-id="c683e-149">DbContext プール</span><span class="sxs-lookup"><span data-stu-id="c683e-149">DbContext pooling</span></span>

<span data-ttu-id="c683e-150">ASP.NET Core アプリケーションで EF Core を使用する基本パターンは通常、カスタム DbContext 型を依存関係挿入システムに登録し、その後、コントローラーのコンストラクター パラメーターからその型のインスタンスを取得するというパターンになります。</span><span class="sxs-lookup"><span data-stu-id="c683e-150">The basic pattern for using EF Core in an ASP.NET Core application usually involves registering a custom DbContext type into the dependency injection system and later obtaining instances of that type through constructor parameters in controllers.</span></span> <span data-ttu-id="c683e-151">つまり、DbContext の新しいインスタンスが要求ごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="c683e-151">This means a new instance of the DbContext is created for each request.</span></span>

<span data-ttu-id="c683e-152">バージョン 2.0 では、依存関係挿入でカスタム DbContext 型を登録する新しい方法を導入しました。再利用可能な DbContext インスタンスのプールが透過的に導入されます。</span><span class="sxs-lookup"><span data-stu-id="c683e-152">In version 2.0 we are introducing a new way to register custom DbContext types in dependency injection which transparently introduces a pool of reusable DbContext instances.</span></span> <span data-ttu-id="c683e-153">DbContext プールを使用するには、サービス登録で `AddDbContext` の代わりに `AddDbContextPool` を使用します。</span><span class="sxs-lookup"><span data-stu-id="c683e-153">To use DbContext pooling, use the `AddDbContextPool` instead of `AddDbContext` during service registration:</span></span>

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="c683e-154">この方法を利用する場合、コントローラーが DbContext インスタンスを要求した時点で、プールに利用できるインスタンスがないか最初に確認されます。</span><span class="sxs-lookup"><span data-stu-id="c683e-154">If this method is used, at the time a DbContext instance is requested by a controller we will first check if there is an instance available in the pool.</span></span> <span data-ttu-id="c683e-155">要求処理が終わると、インスタンス上のあらゆる状態がリセットされ、インスタンス自体はプールに戻ります。</span><span class="sxs-lookup"><span data-stu-id="c683e-155">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="c683e-156">これは概念としては、DbContext プロバイダーの接続プールと似ており、DbContext インスタンスの初期化コストが一部節約されるという長所があります。</span><span class="sxs-lookup"><span data-stu-id="c683e-156">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of DbContext instance.</span></span>

### <a name="limitations"></a><span data-ttu-id="c683e-157">制限事項</span><span class="sxs-lookup"><span data-stu-id="c683e-157">Limitations</span></span>

<span data-ttu-id="c683e-158">この新しい方法では、DbContext の `OnConfiguring()` メソッドでできることにいくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="c683e-158">The new method introduces a few limitations on what can be done in the `OnConfiguring()` method of the DbContext.</span></span>

> [!WARNING]  
> <span data-ttu-id="c683e-159">派生した DbContext クラス (要求間で共有できない) で独自の状態を維持する場合 (プライベート フィールドなど)、DbContext プールを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="c683e-159">Avoid using DbContext Pooling if you maintain your own state (for example, private fields) in your derived DbContext class that should not be shared across requests.</span></span> <span data-ttu-id="c683e-160">EF Core は、DbContext インスタンスをプールに追加する前に認識した状態のみをリセットします。</span><span class="sxs-lookup"><span data-stu-id="c683e-160">EF Core will only reset the state that it is aware of before adding a DbContext instance to the pool.</span></span>

### <a name="explicitly-compiled-queries"></a><span data-ttu-id="c683e-161">明示的にコンパイルされたクエリ</span><span class="sxs-lookup"><span data-stu-id="c683e-161">Explicitly compiled queries</span></span>

<span data-ttu-id="c683e-162">これは 2 つ目のオプトイン パフォーマンス機能であり、大規模なシナリオで利点が得られるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="c683e-162">This is the second opt-in performance feature designed to offer benefits in high-scale scenarios.</span></span>

<span data-ttu-id="c683e-163">以前のバージョンの EF や LINQ to SQL では、手動または明示的にコンパイルされたクエリ API を利用できました。アプリケーションでは、1 回だけ計算し、何回も実行できるよう、クエリの変換をキャッシュできました。</span><span class="sxs-lookup"><span data-stu-id="c683e-163">Manual or explicitly compiled query APIs have been available in previous versions of EF and also in LINQ to SQL to allow applications to cache the translation of queries so that they can be computed only once and executed many times.</span></span>

<span data-ttu-id="c683e-164">一般的に、EF Core は、ハッシュ後のクエリ式に基づいてクエリを自動的にコンパイルし、キャッシュできますが、このメカニズムを利用し、ハッシュとキャッシュ参照の計算をバイパスすることでパフォーマンスを少し上げることができます。アプリケーションは、デリゲートを呼び出すことで、コンパイル済みのクエリを利用できます。</span><span class="sxs-lookup"><span data-stu-id="c683e-164">Although in general EF Core can automatically compile and cache queries based on a hashed representation of the query expressions, this mechanism can be used to obtain a small performance gain by bypassing the computation of the hash and the cache lookup, allowing the application to use an already compiled query through the invocation of a delegate.</span></span>

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

## <a name="change-tracking"></a><span data-ttu-id="c683e-165">変更追跡</span><span class="sxs-lookup"><span data-stu-id="c683e-165">Change Tracking</span></span>

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a><span data-ttu-id="c683e-166">Attach で新規および既存のエンティティのグラフを追跡可能</span><span class="sxs-lookup"><span data-stu-id="c683e-166">Attach can track a graph of new and existing entities</span></span>

<span data-ttu-id="c683e-167">EF Core では、さまざまメカニズムを利用し、キー値を自動生成できます。</span><span class="sxs-lookup"><span data-stu-id="c683e-167">EF Core supports automatic generation of key values through a variety of mechanisms.</span></span> <span data-ttu-id="c683e-168">この機能を使用するとき、キー プロパティが CLR 既定の場合 (通常はゼロか null)、値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="c683e-168">When using this feature, a value is generated if the key property is the CLR default--usually zero or null.</span></span> <span data-ttu-id="c683e-169">つまり、エンティティのグラフを `DbContext.Attach` または `DbSet.Attach` に渡すことができます。EF Core は、既にキーが設定されているエンティティを `Unchanged` としてマークします。キーが設定されていないエンティティは `Added` としてマークされます。</span><span class="sxs-lookup"><span data-stu-id="c683e-169">This means that a graph of entities can be passed to `DbContext.Attach` or `DbSet.Attach` and EF Core will mark those entities that have a key already set as `Unchanged` while those entities that do not have a key set will be marked as `Added`.</span></span> <span data-ttu-id="c683e-170">それにより、生成されたキーを使用するとき、新規と既存の混合エンティティのグラフを簡単に添付できるようになります。</span><span class="sxs-lookup"><span data-stu-id="c683e-170">This makes it easy to attach a graph of mixed new and existing entities when using generated keys.</span></span> <span data-ttu-id="c683e-171">`DbContext.Update` と `DbSet.Update` は同じように動作しますが、キーが設定されているエンティティは `Unchanged` ではなく `Modified` としてマークされます。</span><span class="sxs-lookup"><span data-stu-id="c683e-171">`DbContext.Update` and `DbSet.Update` work in the same way, except that entities with a key set are marked as `Modified` instead of `Unchanged`.</span></span>

## <a name="query"></a><span data-ttu-id="c683e-172">クエリ</span><span class="sxs-lookup"><span data-stu-id="c683e-172">Query</span></span>

### <a name="improved-linq-translation"></a><span data-ttu-id="c683e-173">強化された LINQ 変換</span><span class="sxs-lookup"><span data-stu-id="c683e-173">Improved LINQ translation</span></span>

<span data-ttu-id="c683e-174">より多くのクエリを正常に実行できるようになりました。(メモリではなく) データベースで評価されるロジックが増え、データベースから取得できないデータが減りました。</span><span class="sxs-lookup"><span data-stu-id="c683e-174">Enables more queries to successfully execute, with more logic being evaluated in the database (rather than in-memory) and less data unnecessarily being retrieved from the database.</span></span>

### <a name="groupjoin-improvements"></a><span data-ttu-id="c683e-175">GroupJoin の改善</span><span class="sxs-lookup"><span data-stu-id="c683e-175">GroupJoin improvements</span></span>

<span data-ttu-id="c683e-176">これにより、グループ結合に対して生成される SQL が改善されます。</span><span class="sxs-lookup"><span data-stu-id="c683e-176">This work improves the SQL that is generated for group joins.</span></span> <span data-ttu-id="c683e-177">グループ結合は、大抵の場合、オプションのナビゲーション プロパティでのサブクエリの結果です。</span><span class="sxs-lookup"><span data-stu-id="c683e-177">Group joins are most often a result of sub-queries on optional navigation properties.</span></span>

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a><span data-ttu-id="c683e-178">FromSql と ExecuteSqlCommand の文字列補間</span><span class="sxs-lookup"><span data-stu-id="c683e-178">String interpolation in FromSql and ExecuteSqlCommand</span></span>

<span data-ttu-id="c683e-179">C# 6 では文字列補間が導入されました。この機能では、C# 式を文字列リテラルに直接埋め込むことができます。実行時、文字列が効率的に構築されます。</span><span class="sxs-lookup"><span data-stu-id="c683e-179">C# 6 introduced String Interpolation, a feature that allows C# expressions to be directly embedded in string literals, providing a nice way of building strings at runtime.</span></span> <span data-ttu-id="c683e-180">EF Core 2.0 では、RAW SQL 文字列を受け取る 2 つのプライマリ API、`FromSql` と `ExecuteSqlCommand` に、補間文字列の特殊なサポートを追加しました。</span><span class="sxs-lookup"><span data-stu-id="c683e-180">In EF Core 2.0 we added special support for interpolated strings to our two primary APIs that accept raw SQL strings: `FromSql` and `ExecuteSqlCommand`.</span></span> <span data-ttu-id="c683e-181">この新しいサポートにより、"安全な" 方法で C# 文字列補間を使用できます。</span><span class="sxs-lookup"><span data-stu-id="c683e-181">This new support allows C# string interpolation to be used in a "safe" manner.</span></span> <span data-ttu-id="c683e-182">すなわち、実行時に SQL を動的に構築するときに起こりうる SQL 挿入のよくある間違いを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="c683e-182">That is, in a way that protects against common SQL injection mistakes that can occur when dynamically constructing SQL at runtime.</span></span>

<span data-ttu-id="c683e-183">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="c683e-183">Here is an example:</span></span>

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

<span data-ttu-id="c683e-184">この例では、2 つの変数が SQL 形式文字列に埋め込まれています。</span><span class="sxs-lookup"><span data-stu-id="c683e-184">In this example, there are two variables embedded in the SQL format string.</span></span> <span data-ttu-id="c683e-185">EF Core が次の SQL を生成します。</span><span class="sxs-lookup"><span data-stu-id="c683e-185">EF Core will produce the following SQL:</span></span>

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a><span data-ttu-id="c683e-186">EF.Functions.Like()</span><span class="sxs-lookup"><span data-stu-id="c683e-186">EF.Functions.Like()</span></span>

<span data-ttu-id="c683e-187">EF Core またはプロバイダーが利用できる EF.Functions プロパティを追加しました。このプロパティは、LINQ クエリで呼び出せるようにデータベースの関数や演算子にマッピングされるメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="c683e-187">We have added the EF.Functions property which can be used by EF Core or providers to define methods that map to database functions or operators so that those can be invoked in LINQ queries.</span></span> <span data-ttu-id="c683e-188">このようなメソッドの最初の例が Like() です。</span><span class="sxs-lookup"><span data-stu-id="c683e-188">The first example of such a method is Like():</span></span>

``` csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%")
    select c;
```

<span data-ttu-id="c683e-189">Like() にはインメモリ実装が付随することに注目してください。インメモリ データベースに対して作業するときやクライアント側で述語を評価する必要があるときに便利です。</span><span class="sxs-lookup"><span data-stu-id="c683e-189">Note that Like() comes with an in-memory implementation, which can be handy when working against an in-memory database or when evaluation of the predicate needs to occur on the client side.</span></span>

## <a name="database-management"></a><span data-ttu-id="c683e-190">データベース管理</span><span class="sxs-lookup"><span data-stu-id="c683e-190">Database management</span></span>

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a><span data-ttu-id="c683e-191">DbContext スキャフォールディングの複数形化フック</span><span class="sxs-lookup"><span data-stu-id="c683e-191">Pluralization hook for DbContext scaffolding</span></span>

<span data-ttu-id="c683e-192">EF Core 2.0 では、新しい *IPluralizer* サービスが導入されました。エンティティ型の名前を単数形化し、DbSet の名前を複数形化するために利用されます。</span><span class="sxs-lookup"><span data-stu-id="c683e-192">EF Core 2.0 introduces a new *IPluralizer* service that is used to singularize entity type names and pluralize DbSet names.</span></span> <span data-ttu-id="c683e-193">既定の実装は no-op (操作なし) です。独自のプルーラライザー (複数形にするもの) を簡単にプラグインできるフックに過ぎません。</span><span class="sxs-lookup"><span data-stu-id="c683e-193">The default implementation is a no-op, so this is just a hook where folks can easily plug in their own pluralizer.</span></span>

<span data-ttu-id="c683e-194">開発者が独自のプルーラライザーをフックインすると次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c683e-194">Here is what it looks like for a developer to hook in their own pluralizer:</span></span>

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

## <a name="others"></a><span data-ttu-id="c683e-195">Others</span><span class="sxs-lookup"><span data-stu-id="c683e-195">Others</span></span>

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a><span data-ttu-id="c683e-196">ADO.NET SQLite プロバイダーを SQLitePCL.raw に移動</span><span class="sxs-lookup"><span data-stu-id="c683e-196">Move ADO.NET SQLite provider to SQLitePCL.raw</span></span>

<span data-ttu-id="c683e-197">これにより、Microsoft.Data.Sqlite で、さまざまプラットフォームでネイティブ SQLite バイナリを配信するためのソリューションがより堅牢になります。</span><span class="sxs-lookup"><span data-stu-id="c683e-197">This gives us a more robust solution in Microsoft.Data.Sqlite for distributing native SQLite binaries on different platforms.</span></span>

### <a name="only-one-provider-per-model"></a><span data-ttu-id="c683e-198">モデルごとにプロバイダーは 1 つ</span><span class="sxs-lookup"><span data-stu-id="c683e-198">Only one provider per model</span></span>

<span data-ttu-id="c683e-199">プロバイダーがモデルと対話するしくみが大幅に増強され、規則、注釈、fluent API がさまざまプロバイダーと連動するしくみが単純になりました。</span><span class="sxs-lookup"><span data-stu-id="c683e-199">Significantly augments how providers can interact with the model and simplifies how conventions, annotations and fluent APIs work with different providers.</span></span>

<span data-ttu-id="c683e-200">EF Core 2.0 は、使用されるプロバイダーごとに異なる [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) をビルドするようになりました。</span><span class="sxs-lookup"><span data-stu-id="c683e-200">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="c683e-201">これは通常、アプリケーションに対して透過的です。</span><span class="sxs-lookup"><span data-stu-id="c683e-201">This is usually transparent to the application.</span></span> <span data-ttu-id="c683e-202">下位レベルのメタデータ API が単純になり、*一般的なリレーショナル メタデータ コンセプト*へのあらゆるアクセスが `.SqlServer` や `.Sqlite` などではなく、`.Relational` の呼び出しで常に行われます。</span><span class="sxs-lookup"><span data-stu-id="c683e-202">This has facilitated a simplification of lower-level metadata APIs such that any access to *common relational metadata concepts* is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc.</span></span>

### <a name="consolidated-logging-and-diagnostics"></a><span data-ttu-id="c683e-203">統合されたログと診断</span><span class="sxs-lookup"><span data-stu-id="c683e-203">Consolidated logging and diagnostics</span></span>

<span data-ttu-id="c683e-204">ログ記録 (ILogger に基づく) メカニズムと診断 (DiagnosticSource に基づく) メカニズムで共有されるコードが増えました。</span><span class="sxs-lookup"><span data-stu-id="c683e-204">Logging (based on ILogger) and Diagnostics (based on DiagnosticSource) mechanisms now share more code.</span></span>

<span data-ttu-id="c683e-205">ILogger に送信されるメッセージのイベント ID が 2.0 で変更されました。</span><span class="sxs-lookup"><span data-stu-id="c683e-205">The event IDs for messages sent to an ILogger have changed in 2.0.</span></span> <span data-ttu-id="c683e-206">イベント ID が EF Core コード全体で一意になりました。</span><span class="sxs-lookup"><span data-stu-id="c683e-206">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="c683e-207">これらのメッセージはまた、たとえば MVC で使用される構造化されたログ記録の標準パターンに従います。</span><span class="sxs-lookup"><span data-stu-id="c683e-207">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="c683e-208">ロガー カテゴリも変更されました。</span><span class="sxs-lookup"><span data-stu-id="c683e-208">Logger categories have also changed.</span></span> <span data-ttu-id="c683e-209">現在、[DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs) 経由でアクセスされる既知のカテゴリ セットがあります。</span><span class="sxs-lookup"><span data-stu-id="c683e-209">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="c683e-210">DiagnosticSource イベントでは、それに対応する `ILogger` メッセージと同じイベント ID 名が使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="c683e-210">DiagnosticSource events now use the same event ID names as the corresponding `ILogger` messages.</span></span>
