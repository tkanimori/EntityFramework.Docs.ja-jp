---
title: 関連データの読み込み - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 86b9d08377ea8295b746e5f0217a408edcfe1517
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370474"
---
# <a name="loading-related-data"></a><span data-ttu-id="77831-102">関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="77831-102">Loading Related Data</span></span>

<span data-ttu-id="77831-103">Entity Framework Core を使用すると、モデル内でナビゲーション プロパティを使用して関連エンティティを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="77831-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="77831-104">関連データの読み込みに使用される共通の O/RM パターンが 3 つあります。</span><span class="sxs-lookup"><span data-stu-id="77831-104">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="77831-105">**一括読み込み**。初期クエリの一部としてデータベースから関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="77831-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="77831-106">**明示的読み込み**。後でデータベースから明示的に関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="77831-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="77831-107">**遅延読み込み**。ナビゲーション プロパティにアクセスしたときに、データベースから透過的に関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="77831-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="77831-108">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="77831-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="77831-109">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="77831-109">Eager loading</span></span>

<span data-ttu-id="77831-110">`Include` メソッドを使用して、クエリ結果に含める関連データを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="77831-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="77831-111">次の例では、結果で返されるブログには `Posts` プロパティに関連する投稿が設定されます。</span><span class="sxs-lookup"><span data-stu-id="77831-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="77831-112">Entity Framework Core は、以前にコンテキスト インスタンスに読み込まれた他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。</span><span class="sxs-lookup"><span data-stu-id="77831-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="77831-113">そのため、ナビゲーション プロパティのデータを明示的に含めていない場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="77831-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="77831-114">複数のリレーションシップの関連データを 1 つのクエリに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="77831-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="77831-115">複数のレベルを含める</span><span class="sxs-lookup"><span data-stu-id="77831-115">Including multiple levels</span></span>

<span data-ttu-id="77831-116">`ThenInclude` メソッドを使用して、リレーションシップをドリル ダウンし、複数のレベルの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="77831-116">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="77831-117">次の例では、すべてのブログ、関連記事、および各投稿の作成者を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="77831-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="77831-118">`ThenInclude` に対して複数の呼び出しを連鎖させて、さらなるレベルの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="77831-118">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="77831-119">このすべてを組み合わせて、複数のレベルと複数のルートの関連データを同じクエリ内に含めることができます。</span><span class="sxs-lookup"><span data-stu-id="77831-119">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="77831-120">含まれているエンティティの 1 つについて複数の関連エンティティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="77831-120">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="77831-121">たとえば、`Blogs` をクエリするときに、`Posts` を含め、さらに `Posts` の `Author` と `Tags` の両方を含めたい場合があります。</span><span class="sxs-lookup"><span data-stu-id="77831-121">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="77831-122">この場合、ルートから始まる各インクルード パスを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="77831-122">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="77831-123">たとえば、`Blog -> Posts -> Author` と`Blog -> Posts -> Tags` です。</span><span class="sxs-lookup"><span data-stu-id="77831-123">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="77831-124">これで冗長的な結合を実現することにはならず、ほとんどの場合、SQL を生成するときに EF で結合は統合されます。</span><span class="sxs-lookup"><span data-stu-id="77831-124">This does not mean you will get redundant joins; in most cases, EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

> [!CAUTION]
> <span data-ttu-id="77831-125">バージョン 3.0.0 より、各 `Include` によって追加の JOIN がリレーショナル プロバイダーによって作成された SQL クエリに追加されます。以前のバージョンでは追加の SQL クエリが生成されていました。</span><span class="sxs-lookup"><span data-stu-id="77831-125">Since version 3.0.0, each `Include` will cause an additional JOIN to be added to SQL queries produced by relational providers, whereas previous versions generated additional SQL queries.</span></span> <span data-ttu-id="77831-126">これにより、クエリのパフォーマンスが良かれ悪しかれ大幅に変わります。</span><span class="sxs-lookup"><span data-stu-id="77831-126">This can significantly change the performance of your queries, for better or worse.</span></span> <span data-ttu-id="77831-127">特に、`Include` 演算子が非常に多い LINQ クエリは、デカルト爆発の問題を回避するために、複数の個別の LINQ クエリに分割する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="77831-127">In particular, LINQ queries with an exceedingly high number of `Include` operators may need to be broken down into multiple separate LINQ queries in order to avoid the cartesian explosion problem.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="77831-128">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="77831-128">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="77831-129">この機能は EF Core 5.0 で導入されています。</span><span class="sxs-lookup"><span data-stu-id="77831-129">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="77831-130">インクルードを適用して関連データを読み込む場合は、インクルードされているコレクション ナビゲーションに特定の列挙可能な操作を適用できます。これにより、結果のフィルター処理と並べ替えが可能になります。</span><span class="sxs-lookup"><span data-stu-id="77831-130">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="77831-131">サポートされている操作は、`Where`、`OrderBy`、`OrderByDescending`、`ThenBy`、`ThenByDescending`、`Skip`、`Take` です。</span><span class="sxs-lookup"><span data-stu-id="77831-131">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="77831-132">このような操作は、次の例に示すように、インクルード メソッドに渡されるラムダのコレクション ナビゲーションに適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="77831-132">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="77831-133">インクルードされている各ナビゲーションでは、フィルター操作の一意のセットが 1 つだけ許可されます。</span><span class="sxs-lookup"><span data-stu-id="77831-133">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="77831-134">特定のコレクション ナビゲーション (次の例では `blog.Posts`) に複数のインクルード操作が適用されている場合、フィルター操作はそのいずれかでのみ指定できます。</span><span class="sxs-lookup"><span data-stu-id="77831-134">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span> 

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="77831-135">また、複数回インクルードされているナビゲーションごとに、同一の操作を適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="77831-135">Alternatively, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="77831-136">クエリの追跡では、[ナビゲーション修正](tracking.md)により、フィルター処理されたインクルードの結果が予期しないものになることがあります。</span><span class="sxs-lookup"><span data-stu-id="77831-136">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](tracking.md).</span></span> <span data-ttu-id="77831-137">以前にクエリされ、変更トラッカーに格納されていた関連エンティティはすべて、フィルターの要件を満たしていなくても、フィルター処理されたインクルードのクエリの結果に表示されます。</span><span class="sxs-lookup"><span data-stu-id="77831-137">All relevant entities that have been querried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="77831-138">そのような状況でフィルター処理されたインクルードを使用する場合は、`NoTracking` クエリを使用するか、DbContext を再作成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="77831-138">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="77831-139">例:</span><span class="sxs-lookup"><span data-stu-id="77831-139">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rathat than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a><span data-ttu-id="77831-140">派生型に対するインクルード</span><span class="sxs-lookup"><span data-stu-id="77831-140">Include on derived types</span></span>

<span data-ttu-id="77831-141">`Include` と `ThenInclude` を使用して、派生型にのみ定義されているナビゲーションの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="77831-141">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="77831-142">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="77831-142">Given the following model:</span></span>

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

<span data-ttu-id="77831-143">生徒である全ユーザーの `School` ナビゲーションの内容を一括して読み込むには、いくつかのパターンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="77831-143">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="77831-144">キャストを使用する</span><span class="sxs-lookup"><span data-stu-id="77831-144">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="77831-145">`as` 演算子を使用する</span><span class="sxs-lookup"><span data-stu-id="77831-145">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="77831-146">型 `string` のパラメーターを受け取る `Include` のオーバーロードを使用する</span><span class="sxs-lookup"><span data-stu-id="77831-146">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="77831-147">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="77831-147">Explicit loading</span></span>

<span data-ttu-id="77831-148">`DbContext.Entry(...)` API を使用してナビゲーション プロパティを明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="77831-148">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="77831-149">また、関連エンティティを返す個別のクエリを実行することで、ナビゲーション プロパティを明示的に読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="77831-149">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="77831-150">変更の追跡が有効な場合、エンティティを読み込むと、EF Core は、既に読み込まれているエンティティがあれば参照するように、新しく読み込まれたエンティティのナビゲーション プロパティを自動的に設定します。また、新しく読み込まれたエンティティを参照するように、既に読み込まれているエンティティのナビゲーション プロパティを自動的に設定します。</span><span class="sxs-lookup"><span data-stu-id="77831-150">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="77831-151">関連エンティティのクエリ</span><span class="sxs-lookup"><span data-stu-id="77831-151">Querying related entities</span></span>

<span data-ttu-id="77831-152">また、ナビゲーション プロパティの内容を表す LINQ クエリを取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="77831-152">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="77831-153">そのため、関連エンティティをメモリに読み込まずに集計演算子を実行するなどの操作を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="77831-153">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="77831-154">関連エンティティがメモリに読み込まれるようにフィルター処理することもできます。</span><span class="sxs-lookup"><span data-stu-id="77831-154">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="77831-155">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="77831-155">Lazy loading</span></span>

<span data-ttu-id="77831-156">遅延読み込みを使用するには、[Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) パッケージをインストールし、`UseLazyLoadingProxies` を呼び出して有効にする方法が最も簡単です。</span><span class="sxs-lookup"><span data-stu-id="77831-156">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="77831-157">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="77831-157">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="77831-158">または、AddDbContext を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="77831-158">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="77831-159">EF Core は、オーバーライド可能なナビゲーション プロパティの場合に遅延読み込みを有効にします。つまり `virtual` であり、継承可能なクラスのナビゲーション プロパティである必要があります。</span><span class="sxs-lookup"><span data-stu-id="77831-159">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="77831-160">たとえば、次のエンティティでは、`Post.Blog` および `Blog.Posts` ナビゲーション プロパティの遅延読み込みが実行されます。</span><span class="sxs-lookup"><span data-stu-id="77831-160">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="77831-161">プロキシを使用しない遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="77831-161">Lazy loading without proxies</span></span>

<span data-ttu-id="77831-162">遅延読み込みプロキシは、[エンティティ型コンストラクター](../modeling/constructors.md)に関するページで説明されているように、エンティティに `ILazyLoader` サービスを挿入することで機能します。</span><span class="sxs-lookup"><span data-stu-id="77831-162">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="77831-163">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="77831-163">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="77831-164">この場合、エンティティ型を継承したり、ナビゲーション プロパティを仮想にしたりする必要はありません。コンテキストにアタッチされたときに、`new` で作成されたエンティティ インスタンスの遅延読み込みを実行することができます。</span><span class="sxs-lookup"><span data-stu-id="77831-164">This doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="77831-165">ただし、これには、[Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) パッケージで定義される `ILazyLoader` サービスが必要です。</span><span class="sxs-lookup"><span data-stu-id="77831-165">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="77831-166">このパッケージに含まれる型のセットは、それに依存してもほとんど影響がないように、最低限のセットになっています。</span><span class="sxs-lookup"><span data-stu-id="77831-166">This package contains a minimal set of types so that there is very little impact in depending on it.</span></span> <span data-ttu-id="77831-167">しかし、エンティティ型で EF Core パッケージへの依存を完全に回避するには、デリゲートとして `ILazyLoader.Load` メソッドを挿入することが可能です。</span><span class="sxs-lookup"><span data-stu-id="77831-167">However, to completely avoid depending on any EF Core packages in the entity types, it is possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="77831-168">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="77831-168">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="77831-169">上記のコードでは、デリゲートの使用をビット クリーナーにするために、拡張メソッド `Load` を使用しています。</span><span class="sxs-lookup"><span data-stu-id="77831-169">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]  
> <span data-ttu-id="77831-170">遅延読み込みデリゲートのコンストラクター パラメーターは、"lazyLoader" と指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="77831-170">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="77831-171">これとは別の名前を使用する構成が今後のリリースで計画されています。</span><span class="sxs-lookup"><span data-stu-id="77831-171">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="77831-172">関連データとシリアル化</span><span class="sxs-lookup"><span data-stu-id="77831-172">Related data and serialization</span></span>

<span data-ttu-id="77831-173">EF Core はナビゲーション プロパティを自動的に修正するので、最終的にオブジェクト グラフの循環が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="77831-173">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="77831-174">たとえば、ブログとその関連する投稿を読み込むと、投稿のコレクションを参照するブログ オブジェクトになります。</span><span class="sxs-lookup"><span data-stu-id="77831-174">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="77831-175">これらの各投稿には元のブログへの参照が含まれることになります。</span><span class="sxs-lookup"><span data-stu-id="77831-175">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="77831-176">一部のシリアル化フレームワークでは、このような循環は許可されていません。</span><span class="sxs-lookup"><span data-stu-id="77831-176">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="77831-177">たとえば、Json.NET では、循環が発生した場合に次の例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="77831-177">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="77831-178">Newtonsoft.Json.JsonSerializationException:Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span><span class="sxs-lookup"><span data-stu-id="77831-178">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="77831-179">ASP.NET Core を使用している場合は、オブジェクト グラフで見つかった循環を無視するように Json.NET を構成できます。</span><span class="sxs-lookup"><span data-stu-id="77831-179">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="77831-180">この操作は、`Startup.cs` の `ConfigureServices(...)` メソッドで実行します。</span><span class="sxs-lookup"><span data-stu-id="77831-180">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="77831-181">別の方法は、いずれかのナビゲーション プロパティを `[JsonIgnore]` 属性で装飾し、シリアル化中にそのナビゲーション プロパティを走査しないように Json.NET に指示することです。</span><span class="sxs-lookup"><span data-stu-id="77831-181">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
