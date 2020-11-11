---
title: EF Core 2.0 の新機能 - EF Core
description: Entity Framework Core 2.0 での変更点と改善点
author: ajcvickers
ms.date: 02/20/2018
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: a670cba56c9ba4ebc45c81c1185faa114b78c146
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429378"
---
# <a name="new-features-in-ef-core-20"></a><span data-ttu-id="03fdb-103">EF Core 2.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="03fdb-103">New features in EF Core 2.0</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="03fdb-104">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="03fdb-104">.NET Standard 2.0</span></span>

<span data-ttu-id="03fdb-105">EF Core が .NET Standard 2.0 対応になりました。つまり、.NET Core 2.0、.NET Framework 4.6.1、および .NET Standard 2.0 を実装するその他のライブラリで使えるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03fdb-105">EF Core now targets .NET Standard 2.0, which means it can work with .NET Core 2.0, .NET Framework 4.6.1, and other libraries that implement .NET Standard 2.0.</span></span>
<span data-ttu-id="03fdb-106">サポート対象の詳細については、「[Supported .NET Implementations](xref:core/miscellaneous/platforms)」 (サポートされている .NET 実装) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="03fdb-106">See [Supported .NET Implementations](xref:core/miscellaneous/platforms) for more details on what is supported.</span></span>

## <a name="modeling"></a><span data-ttu-id="03fdb-107">モデリング</span><span class="sxs-lookup"><span data-stu-id="03fdb-107">Modeling</span></span>

### <a name="table-splitting"></a><span data-ttu-id="03fdb-108">テーブル分割</span><span class="sxs-lookup"><span data-stu-id="03fdb-108">Table splitting</span></span>

<span data-ttu-id="03fdb-109">2 つまたはそれ以上のエンティティ型を同じテーブルにマッピングできるようになりました。そこで主キー列が共有され、各行が 2 つまたはそれ以上のエンティティに対応します。</span><span class="sxs-lookup"><span data-stu-id="03fdb-109">It is now possible to map two or more entity types to the same table where the primary key column(s) will be shared and each row will correspond to two or more entities.</span></span>

<span data-ttu-id="03fdb-110">テーブル分割を利用するには、テーブルを共有するすべてのエンティティ型の間で依存リレーションシップ (外部キー プロパティが主キーを形成する) を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-110">To use table splitting an identifying relationship (where foreign key properties form the primary key) must be configured between all of the entity types sharing the table:</span></span>

```csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

<span data-ttu-id="03fdb-111">この機能の詳細については、[テーブル分割](xref:core/modeling/table-splitting)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="03fdb-111">Read the [section on table splitting](xref:core/modeling/table-splitting) for more information on this feature.</span></span>

### <a name="owned-types"></a><span data-ttu-id="03fdb-112">所有されている型</span><span class="sxs-lookup"><span data-stu-id="03fdb-112">Owned types</span></span>

<span data-ttu-id="03fdb-113">所有されているエンティティ型は、別の所有されているエンティティ型と同じ .NET 型を共有できます。ただし、.NET 型だけでは識別できないため、別のエンティティ型からそれへのナビゲーションが必要になります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-113">An owned entity type can share the same .NET type with another owned entity type, but since it cannot be identified just by the .NET type there must be a navigation to it from another entity type.</span></span> <span data-ttu-id="03fdb-114">定義となるナビゲーションを含むエンティティは所有者です。</span><span class="sxs-lookup"><span data-stu-id="03fdb-114">The entity containing the defining navigation is the owner.</span></span> <span data-ttu-id="03fdb-115">所有者に問い合わせるとき、所有されている型が既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-115">When querying the owner the owned types will be included by default.</span></span>

<span data-ttu-id="03fdb-116">慣例によって、所有されている型に対してシャドウ主キーが作成され、テーブル分割を利用し、同じテーブルに所有者としてマッピングされます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-116">By convention a shadow primary key will be created for the owned type and it will be mapped to the same table as the owner by using table splitting.</span></span> <span data-ttu-id="03fdb-117">それによって、所有されている型を EF6 の複合型の使用方法と同じように使用できます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-117">This allows to use owned types similarly to how complex types are used in EF6:</span></span>

```csharp
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

<span data-ttu-id="03fdb-118">この機能に関する詳細は、[所有エンティティ型のセクション](xref:core/modeling/owned-entities)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="03fdb-118">Read the [section on owned entity types](xref:core/modeling/owned-entities) for more information on this feature.</span></span>

### <a name="model-level-query-filters"></a><span data-ttu-id="03fdb-119">モデルレベルのクエリ フィルター</span><span class="sxs-lookup"><span data-stu-id="03fdb-119">Model-level query filters</span></span>

<span data-ttu-id="03fdb-120">EF Core 2.0 には、モデルレベルのクエリ フィルターと呼んでいる新しい機能があります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-120">EF Core 2.0 includes a new feature we call Model-level query filters.</span></span> <span data-ttu-id="03fdb-121">この機能では、(通常 OnModelCreating の) メタデータ モデルのエンティティ型で直接、LINQ クエリ述語 (通常 LINQ Where クエリ演算子に渡されるブール式) を定義できます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-121">This feature allows LINQ query predicates (a boolean expression typically passed to the LINQ Where query operator) to be defined directly on Entity Types in the metadata model (usually in OnModelCreating).</span></span> <span data-ttu-id="03fdb-122">このようなフィルターは、このようなエンティティ型が関係するあらゆる LINQ クエリに自動的に適用されます。これには間接的に参照されるエンティティ型が含まれます。たとえば、Include を利用して参照されます。あるいは、ナビゲーション プロパティが直接参照されます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-122">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="03fdb-123">この機能の一般的な用途は次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="03fdb-123">Some common applications of this feature are:</span></span>

- <span data-ttu-id="03fdb-124">論理削除 - エンティティ型が IsDeleted プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="03fdb-124">Soft delete - An Entity Types defines an IsDeleted property.</span></span>
- <span data-ttu-id="03fdb-125">マルチテナント - エンティティ型が TenantId プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="03fdb-125">Multi-tenancy - An Entity Type defines a TenantId property.</span></span>

<span data-ttu-id="03fdb-126">次の単純なサンプルでは、上記の 2 つのシナリオに対してこの機能が使われています。</span><span class="sxs-lookup"><span data-stu-id="03fdb-126">Here is a simple example demonstrating the feature for the two scenarios listed above:</span></span>

```csharp
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

<span data-ttu-id="03fdb-127">エンティティ型 `Post` のインスタンスに対してマルチテナントと論理削除を実行するモデルレベル フィルターを定義します。</span><span class="sxs-lookup"><span data-stu-id="03fdb-127">We define a model-level filter that implements multi-tenancy and soft-delete for instances of the `Post` Entity Type.</span></span> <span data-ttu-id="03fdb-128">`DbContext` インスタンス レベル プロパティ `TenantId` の使用に注目してください。</span><span class="sxs-lookup"><span data-stu-id="03fdb-128">Note the use of a `DbContext` instance-level property: `TenantId`.</span></span> <span data-ttu-id="03fdb-129">モデルレベル フィルターは、正しいコンテキスト インスタンス (つまり、クエリを実行しているコンテキスト インスタンス) の値を使用します。</span><span class="sxs-lookup"><span data-stu-id="03fdb-129">Model-level filters will use the value from the correct context instance (that is, the context instance that is executing the query).</span></span>

<span data-ttu-id="03fdb-130">フィルターは、IgnoreQueryFilters() 演算子を利用し、個々の LINQ クエリに対して無効にできます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-130">Filters may be disabled for individual LINQ queries using the IgnoreQueryFilters() operator.</span></span>

#### <a name="limitations"></a><span data-ttu-id="03fdb-131">制限事項</span><span class="sxs-lookup"><span data-stu-id="03fdb-131">Limitations</span></span>

- <span data-ttu-id="03fdb-132">ナビゲーション参照は許可されていません。</span><span class="sxs-lookup"><span data-stu-id="03fdb-132">Navigation references are not allowed.</span></span> <span data-ttu-id="03fdb-133">この機能はフィードバックに応じて追加される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-133">This feature may be added based on feedback.</span></span>
- <span data-ttu-id="03fdb-134">フィルターは、階層のエンティティ型ルートでのみ定義できます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-134">Filters can only be defined on the root Entity Type of a hierarchy.</span></span>

### <a name="database-scalar-function-mapping"></a><span data-ttu-id="03fdb-135">データベース スカラー関数マッピング</span><span class="sxs-lookup"><span data-stu-id="03fdb-135">Database scalar function mapping</span></span>

<span data-ttu-id="03fdb-136">EF Core 2.0 には、[Paul Middleton](https://github.com/pmiddleton) の重要な貢献が不可欠でした。それは、データベース スカラー関数のメソッド スタブへのマッピングを可能にするものです。マッピングにより、LINQ クエリで使用したり、SQL に変換したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-136">EF Core 2.0 includes an important contribution from [Paul Middleton](https://github.com/pmiddleton) which enables mapping database scalar functions to method stubs so that they can be used in LINQ queries and translated to SQL.</span></span>

<span data-ttu-id="03fdb-137">この機能の使用方法を簡単に説明すると次のようになります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-137">Here is a brief description of how the feature can be used:</span></span>

<span data-ttu-id="03fdb-138">`DbContext` で静的メソッドを宣言し、それに `DbFunctionAttribute` で注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-138">Declare a static method on your `DbContext` and annotate it with `DbFunctionAttribute`:</span></span>

```csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new NotImplementedException();
    }
}
```

<span data-ttu-id="03fdb-139">このようなメソッドが自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-139">Methods like this are automatically registered.</span></span> <span data-ttu-id="03fdb-140">登録されると、LINQ クエリでのメソッドへの呼び出しが SQL での関数呼び出しに変換されます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-140">Once registered, calls to the method in a LINQ query can be translated to function calls in SQL:</span></span>

```csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

<span data-ttu-id="03fdb-141">次のことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="03fdb-141">A few things to note:</span></span>

- <span data-ttu-id="03fdb-142">規則では、SQL の生成時、関数 (この場合はユーザー定義関数) の名前としてメソッドの名前が使用されますが、メソッド登録時に名前とスキーマをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-142">By convention the name of the method is used as the name of a function (in this case a user-defined function) when generating the SQL, but you can override the name and schema during method registration.</span></span>
- <span data-ttu-id="03fdb-143">現在のところ、スカラー関数のみがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="03fdb-143">Currently only scalar functions are supported.</span></span>
- <span data-ttu-id="03fdb-144">データベースにマップされた関数を作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-144">You must create the mapped function in the database.</span></span> <span data-ttu-id="03fdb-145">EF Core の移行では作成が行われません。</span><span class="sxs-lookup"><span data-stu-id="03fdb-145">EF Core migrations will not take care of creating it.</span></span>

### <a name="self-contained-type-configuration-for-code-first"></a><span data-ttu-id="03fdb-146">Code First の自己完結型構成</span><span class="sxs-lookup"><span data-stu-id="03fdb-146">Self-contained type configuration for code first</span></span>

<span data-ttu-id="03fdb-147">EF6 では、 *EntityTypeConfiguration* から派生することで、特定のエンティティ型の Code First 構成をカプセル化できました。</span><span class="sxs-lookup"><span data-stu-id="03fdb-147">In EF6 it was possible to encapsulate the code first configuration of a specific entity type by deriving from *EntityTypeConfiguration*.</span></span> <span data-ttu-id="03fdb-148">EF Core 2.0 では、このパターンが復活します。</span><span class="sxs-lookup"><span data-stu-id="03fdb-148">In EF Core 2.0 we are bringing this pattern back:</span></span>

```csharp
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

## <a name="high-performance"></a><span data-ttu-id="03fdb-149">高パフォーマンス</span><span class="sxs-lookup"><span data-stu-id="03fdb-149">High Performance</span></span>

### <a name="dbcontext-pooling"></a><span data-ttu-id="03fdb-150">DbContext プール</span><span class="sxs-lookup"><span data-stu-id="03fdb-150">DbContext pooling</span></span>

<span data-ttu-id="03fdb-151">ASP.NET Core アプリケーションで EF Core を使用する基本パターンは通常、カスタム DbContext 型を依存関係挿入システムに登録し、その後、コントローラーのコンストラクター パラメーターからその型のインスタンスを取得するというパターンになります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-151">The basic pattern for using EF Core in an ASP.NET Core application usually involves registering a custom DbContext type into the dependency injection system and later obtaining instances of that type through constructor parameters in controllers.</span></span> <span data-ttu-id="03fdb-152">つまり、DbContext の新しいインスタンスが要求ごとに作成されます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-152">This means a new instance of the DbContext is created for each request.</span></span>

<span data-ttu-id="03fdb-153">バージョン 2.0 では、依存関係挿入でカスタム DbContext 型を登録する新しい方法を導入しました。再利用可能な DbContext インスタンスのプールが透過的に導入されます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-153">In version 2.0 we are introducing a new way to register custom DbContext types in dependency injection which transparently introduces a pool of reusable DbContext instances.</span></span> <span data-ttu-id="03fdb-154">DbContext プールを使用するには、サービス登録で `AddDbContext` の代わりに `AddDbContextPool` を使用します。</span><span class="sxs-lookup"><span data-stu-id="03fdb-154">To use DbContext pooling, use the `AddDbContextPool` instead of `AddDbContext` during service registration:</span></span>

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="03fdb-155">この方法を利用する場合、コントローラーが DbContext インスタンスを要求した時点で、プールに利用できるインスタンスがないか最初に確認されます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-155">If this method is used, at the time a DbContext instance is requested by a controller we will first check if there is an instance available in the pool.</span></span> <span data-ttu-id="03fdb-156">要求処理が終わると、インスタンス上のあらゆる状態がリセットされ、インスタンス自体はプールに戻ります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-156">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="03fdb-157">これは概念としては、DbContext プロバイダーの接続プールと似ており、DbContext インスタンスの初期化コストが一部節約されるという長所があります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-157">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of DbContext instance.</span></span>

### <a name="limitations"></a><span data-ttu-id="03fdb-158">制限事項</span><span class="sxs-lookup"><span data-stu-id="03fdb-158">Limitations</span></span>

<span data-ttu-id="03fdb-159">この新しい方法では、DbContext の `OnConfiguring()` メソッドでできることにいくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-159">The new method introduces a few limitations on what can be done in the `OnConfiguring()` method of the DbContext.</span></span>

> [!WARNING]
> <span data-ttu-id="03fdb-160">派生した DbContext クラス (要求間で共有できない) で独自の状態を維持する場合 (プライベート フィールドなど)、DbContext プールを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="03fdb-160">Avoid using DbContext Pooling if you maintain your own state (for example, private fields) in your derived DbContext class that should not be shared across requests.</span></span> <span data-ttu-id="03fdb-161">EF Core は、DbContext インスタンスをプールに追加する前に認識した状態のみをリセットします。</span><span class="sxs-lookup"><span data-stu-id="03fdb-161">EF Core will only reset the state that it is aware of before adding a DbContext instance to the pool.</span></span>

### <a name="explicitly-compiled-queries"></a><span data-ttu-id="03fdb-162">明示的にコンパイルされたクエリ</span><span class="sxs-lookup"><span data-stu-id="03fdb-162">Explicitly compiled queries</span></span>

<span data-ttu-id="03fdb-163">これは 2 つ目のオプトイン パフォーマンス機能であり、大規模なシナリオで利点が得られるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="03fdb-163">This is the second opt-in performance feature designed to offer benefits in high-scale scenarios.</span></span>

<span data-ttu-id="03fdb-164">以前のバージョンの EF や LINQ to SQL では、手動または明示的にコンパイルされたクエリ API を利用できました。アプリケーションでは、1 回だけ計算し、何回も実行できるよう、クエリの変換をキャッシュできました。</span><span class="sxs-lookup"><span data-stu-id="03fdb-164">Manual or explicitly compiled query APIs have been available in previous versions of EF and also in LINQ to SQL to allow applications to cache the translation of queries so that they can be computed only once and executed many times.</span></span>

<span data-ttu-id="03fdb-165">一般的に、EF Core は、ハッシュ後のクエリ式に基づいてクエリを自動的にコンパイルし、キャッシュできますが、このメカニズムを利用し、ハッシュとキャッシュ参照の計算をバイパスすることでパフォーマンスを少し上げることができます。アプリケーションは、デリゲートを呼び出すことで、コンパイル済みのクエリを利用できます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-165">Although in general EF Core can automatically compile and cache queries based on a hashed representation of the query expressions, this mechanism can be used to obtain a small performance gain by bypassing the computation of the hash and the cache lookup, allowing the application to use an already compiled query through the invocation of a delegate.</span></span>

```csharp
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

## <a name="change-tracking"></a><span data-ttu-id="03fdb-166">変更追跡</span><span class="sxs-lookup"><span data-stu-id="03fdb-166">Change Tracking</span></span>

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a><span data-ttu-id="03fdb-167">Attach で新規および既存のエンティティのグラフを追跡可能</span><span class="sxs-lookup"><span data-stu-id="03fdb-167">Attach can track a graph of new and existing entities</span></span>

<span data-ttu-id="03fdb-168">EF Core では、さまざまメカニズムを利用し、キー値を自動生成できます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-168">EF Core supports automatic generation of key values through a variety of mechanisms.</span></span> <span data-ttu-id="03fdb-169">この機能を使用するとき、キー プロパティが CLR 既定の場合 (通常はゼロか null)、値が生成されます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-169">When using this feature, a value is generated if the key property is the CLR default--usually zero or null.</span></span> <span data-ttu-id="03fdb-170">つまり、エンティティのグラフを `DbContext.Attach` または `DbSet.Attach` に渡すことができます。EF Core は、既にキーが設定されているエンティティを `Unchanged` としてマークします。キーが設定されていないエンティティは `Added` としてマークされます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-170">This means that a graph of entities can be passed to `DbContext.Attach` or `DbSet.Attach` and EF Core will mark those entities that have a key already set as `Unchanged` while those entities that do not have a key set will be marked as `Added`.</span></span> <span data-ttu-id="03fdb-171">それにより、生成されたキーを使用するとき、新規と既存の混合エンティティのグラフを簡単に添付できるようになります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-171">This makes it easy to attach a graph of mixed new and existing entities when using generated keys.</span></span> <span data-ttu-id="03fdb-172">`DbContext.Update` と `DbSet.Update` は同じように動作しますが、キーが設定されているエンティティは `Unchanged` ではなく `Modified` としてマークされます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-172">`DbContext.Update` and `DbSet.Update` work in the same way, except that entities with a key set are marked as `Modified` instead of `Unchanged`.</span></span>

## <a name="query"></a><span data-ttu-id="03fdb-173">クエリ</span><span class="sxs-lookup"><span data-stu-id="03fdb-173">Query</span></span>

### <a name="improved-linq-translation"></a><span data-ttu-id="03fdb-174">強化された LINQ 変換</span><span class="sxs-lookup"><span data-stu-id="03fdb-174">Improved LINQ translation</span></span>

<span data-ttu-id="03fdb-175">より多くのクエリを正常に実行できるようになりました。(メモリではなく) データベースで評価されるロジックが増え、データベースから取得できないデータが減りました。</span><span class="sxs-lookup"><span data-stu-id="03fdb-175">Enables more queries to successfully execute, with more logic being evaluated in the database (rather than in-memory) and less data unnecessarily being retrieved from the database.</span></span>

### <a name="groupjoin-improvements"></a><span data-ttu-id="03fdb-176">GroupJoin の改善</span><span class="sxs-lookup"><span data-stu-id="03fdb-176">GroupJoin improvements</span></span>

<span data-ttu-id="03fdb-177">これにより、グループ結合に対して生成される SQL が改善されます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-177">This work improves the SQL that is generated for group joins.</span></span> <span data-ttu-id="03fdb-178">グループ結合は、大抵の場合、オプションのナビゲーション プロパティでのサブクエリの結果です。</span><span class="sxs-lookup"><span data-stu-id="03fdb-178">Group joins are most often a result of sub-queries on optional navigation properties.</span></span>

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a><span data-ttu-id="03fdb-179">FromSql と ExecuteSqlCommand の文字列補間</span><span class="sxs-lookup"><span data-stu-id="03fdb-179">String interpolation in FromSql and ExecuteSqlCommand</span></span>

<span data-ttu-id="03fdb-180">C# 6 では文字列補間が導入されました。この機能では、C# 式を文字列リテラルに直接埋め込むことができます。実行時、文字列が効率的に構築されます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-180">C# 6 introduced String Interpolation, a feature that allows C# expressions to be directly embedded in string literals, providing a nice way of building strings at runtime.</span></span> <span data-ttu-id="03fdb-181">EF Core 2.0 では、RAW SQL 文字列を受け取る 2 つのプライマリ API、`FromSql` と `ExecuteSqlCommand` に、補間文字列の特殊なサポートを追加しました。</span><span class="sxs-lookup"><span data-stu-id="03fdb-181">In EF Core 2.0 we added special support for interpolated strings to our two primary APIs that accept raw SQL strings: `FromSql` and `ExecuteSqlCommand`.</span></span> <span data-ttu-id="03fdb-182">この新しいサポートにより、"安全な" 方法で C# 文字列補間を使用できます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-182">This new support allows C# string interpolation to be used in a "safe" manner.</span></span> <span data-ttu-id="03fdb-183">すなわち、実行時に SQL を動的に構築するときに起こりうる SQL 挿入のよくある間違いを防ぎます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-183">That is, in a way that protects against common SQL injection mistakes that can occur when dynamically constructing SQL at runtime.</span></span>

<span data-ttu-id="03fdb-184">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="03fdb-184">Here is an example:</span></span>

```csharp
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

<span data-ttu-id="03fdb-185">この例では、2 つの変数が SQL 形式文字列に埋め込まれています。</span><span class="sxs-lookup"><span data-stu-id="03fdb-185">In this example, there are two variables embedded in the SQL format string.</span></span> <span data-ttu-id="03fdb-186">EF Core が次の SQL を生成します。</span><span class="sxs-lookup"><span data-stu-id="03fdb-186">EF Core will produce the following SQL:</span></span>

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a><span data-ttu-id="03fdb-187">EF.Functions.Like()</span><span class="sxs-lookup"><span data-stu-id="03fdb-187">EF.Functions.Like()</span></span>

<span data-ttu-id="03fdb-188">EF Core またはプロバイダーが利用できる EF.Functions プロパティを追加しました。このプロパティは、LINQ クエリで呼び出せるようにデータベースの関数や演算子にマッピングされるメソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="03fdb-188">We have added the EF.Functions property which can be used by EF Core or providers to define methods that map to database functions or operators so that those can be invoked in LINQ queries.</span></span> <span data-ttu-id="03fdb-189">このようなメソッドの最初の例が Like() です。</span><span class="sxs-lookup"><span data-stu-id="03fdb-189">The first example of such a method is Like():</span></span>

```csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%")
    select c;
```

<span data-ttu-id="03fdb-190">Like() にはインメモリ実装が付随することに注目してください。インメモリ データベースに対して作業するときやクライアント側で述語を評価する必要があるときに便利です。</span><span class="sxs-lookup"><span data-stu-id="03fdb-190">Note that Like() comes with an in-memory implementation, which can be handy when working against an in-memory database or when evaluation of the predicate needs to occur on the client side.</span></span>

## <a name="database-management"></a><span data-ttu-id="03fdb-191">データベース管理</span><span class="sxs-lookup"><span data-stu-id="03fdb-191">Database management</span></span>

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a><span data-ttu-id="03fdb-192">DbContext スキャフォールディングの複数形化フック</span><span class="sxs-lookup"><span data-stu-id="03fdb-192">Pluralization hook for DbContext scaffolding</span></span>

<span data-ttu-id="03fdb-193">EF Core 2.0 では、新しい *IPluralizer* サービスが導入されました。エンティティ型の名前を単数形化し、DbSet の名前を複数形化するために利用されます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-193">EF Core 2.0 introduces a new *IPluralizer* service that is used to singularize entity type names and pluralize DbSet names.</span></span> <span data-ttu-id="03fdb-194">既定の実装は no-op (操作なし) です。独自のプルーラライザー (複数形にするもの) を簡単にプラグインできるフックに過ぎません。</span><span class="sxs-lookup"><span data-stu-id="03fdb-194">The default implementation is a no-op, so this is just a hook where folks can easily plug in their own pluralizer.</span></span>

<span data-ttu-id="03fdb-195">開発者が独自のプルーラライザーをフックインすると次のようになります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-195">Here is what it looks like for a developer to hook in their own pluralizer:</span></span>

```csharp
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

## <a name="others"></a><span data-ttu-id="03fdb-196">Others</span><span class="sxs-lookup"><span data-stu-id="03fdb-196">Others</span></span>

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a><span data-ttu-id="03fdb-197">ADO.NET SQLite プロバイダーを SQLitePCL.raw に移動</span><span class="sxs-lookup"><span data-stu-id="03fdb-197">Move ADO.NET SQLite provider to SQLitePCL.raw</span></span>

<span data-ttu-id="03fdb-198">これにより、Microsoft.Data.Sqlite で、さまざまプラットフォームでネイティブ SQLite バイナリを配信するためのソリューションがより堅牢になります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-198">This gives us a more robust solution in Microsoft.Data.Sqlite for distributing native SQLite binaries on different platforms.</span></span>

### <a name="only-one-provider-per-model"></a><span data-ttu-id="03fdb-199">モデルごとにプロバイダーは 1 つ</span><span class="sxs-lookup"><span data-stu-id="03fdb-199">Only one provider per model</span></span>

<span data-ttu-id="03fdb-200">プロバイダーがモデルと対話するしくみが大幅に増強され、規則、注釈、fluent API がさまざまプロバイダーと連動するしくみが単純になりました。</span><span class="sxs-lookup"><span data-stu-id="03fdb-200">Significantly augments how providers can interact with the model and simplifies how conventions, annotations and fluent APIs work with different providers.</span></span>

<span data-ttu-id="03fdb-201">EF Core 2.0 は、使用されるプロバイダーごとに異なる [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) をビルドするようになりました。</span><span class="sxs-lookup"><span data-stu-id="03fdb-201">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="03fdb-202">これは通常、アプリケーションに対して透過的です。</span><span class="sxs-lookup"><span data-stu-id="03fdb-202">This is usually transparent to the application.</span></span> <span data-ttu-id="03fdb-203">下位レベルのメタデータ API が単純になり、 *一般的なリレーショナル メタデータ コンセプト* へのあらゆるアクセスが `.SqlServer` や `.Sqlite` などではなく、`.Relational` の呼び出しで常に行われます。</span><span class="sxs-lookup"><span data-stu-id="03fdb-203">This has facilitated a simplification of lower-level metadata APIs such that any access to *common relational metadata concepts* is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc.</span></span>

### <a name="consolidated-logging-and-diagnostics"></a><span data-ttu-id="03fdb-204">統合されたログと診断</span><span class="sxs-lookup"><span data-stu-id="03fdb-204">Consolidated logging and diagnostics</span></span>

<span data-ttu-id="03fdb-205">ログ記録 (ILogger に基づく) メカニズムと診断 (DiagnosticSource に基づく) メカニズムで共有されるコードが増えました。</span><span class="sxs-lookup"><span data-stu-id="03fdb-205">Logging (based on ILogger) and Diagnostics (based on DiagnosticSource) mechanisms now share more code.</span></span>

<span data-ttu-id="03fdb-206">ILogger に送信されるメッセージのイベント ID が 2.0 で変更されました。</span><span class="sxs-lookup"><span data-stu-id="03fdb-206">The event IDs for messages sent to an ILogger have changed in 2.0.</span></span> <span data-ttu-id="03fdb-207">イベント ID が EF Core コード全体で一意になりました。</span><span class="sxs-lookup"><span data-stu-id="03fdb-207">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="03fdb-208">これらのメッセージはまた、たとえば MVC で使用される構造化されたログ記録の標準パターンに従います。</span><span class="sxs-lookup"><span data-stu-id="03fdb-208">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="03fdb-209">ロガー カテゴリも変更されました。</span><span class="sxs-lookup"><span data-stu-id="03fdb-209">Logger categories have also changed.</span></span> <span data-ttu-id="03fdb-210">現在、[DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs) 経由でアクセスされる既知のカテゴリ セットがあります。</span><span class="sxs-lookup"><span data-stu-id="03fdb-210">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="03fdb-211">DiagnosticSource イベントでは、それに対応する `ILogger` メッセージと同じイベント ID 名が使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03fdb-211">DiagnosticSource events now use the same event ID names as the corresponding `ILogger` messages.</span></span>
