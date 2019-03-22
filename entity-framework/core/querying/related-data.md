---
title: 関連データの読み込み - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 6beaebf2c6859fcf91e963bfae02f029f8f51147
ms.sourcegitcommit: 6c4e06bc62d98442530e93a44725e38e59483d42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58131434"
---
# <a name="loading-related-data"></a><span data-ttu-id="33ac6-102">関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="33ac6-102">Loading Related Data</span></span>

<span data-ttu-id="33ac6-103">Entity Framework Core を使用すると、モデル内でナビゲーション プロパティを使用して関連エンティティを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="33ac6-104">関連データの読み込みに使用される共通の O/RM パターンが 3 つあります。</span><span class="sxs-lookup"><span data-stu-id="33ac6-104">There are three common O/RM patterns used to load related data.</span></span>
* <span data-ttu-id="33ac6-105">**一括読み込み**。初期クエリの一部としてデータベースから関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="33ac6-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="33ac6-106">**明示的読み込み**。後でデータベースから明示的に関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="33ac6-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="33ac6-107">**遅延読み込み**。ナビゲーション プロパティにアクセスしたときに、データベースから透過的に関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="33ac6-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="33ac6-108">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-108">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="33ac6-109">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="33ac6-109">Eager loading</span></span>

<span data-ttu-id="33ac6-110">`Include` メソッドを使用して、クエリ結果に含める関連データを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="33ac6-111">次の例では、結果で返されるブログには `Posts` プロパティに関連する投稿が設定されます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="33ac6-112">Entity Framework Core は、以前にコンテキスト インスタンスに読み込まれた他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。</span><span class="sxs-lookup"><span data-stu-id="33ac6-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="33ac6-113">そのため、ナビゲーション プロパティのデータを明示的に含めていない場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>


<span data-ttu-id="33ac6-114">複数のリレーションシップの関連データを 1 つのクエリに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="33ac6-115">複数のレベルを含める</span><span class="sxs-lookup"><span data-stu-id="33ac6-115">Including multiple levels</span></span>

<span data-ttu-id="33ac6-116">`ThenInclude` メソッドを使用して、リレーションシップをドリル ダウンし、複数のレベルの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-116">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="33ac6-117">次の例では、すべてのブログ、関連記事、および各投稿の作成者を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleThenInclude)]

> [!NOTE]  
> <span data-ttu-id="33ac6-118">Visual Studio の現在のバージョンでは、適切なコード補完オプションが提供されていないため、コレクションのナビゲーション プロパティのあとに `ThenInclude` メソッドを使用すると、正しい式に構文エラーのフラグが付けられる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="33ac6-118">Current versions of Visual Studio offer incorrect code completion options and can cause correct expressions to be flagged with syntax errors when using the `ThenInclude` method after a collection navigation property.</span></span> <span data-ttu-id="33ac6-119">これは https://github.com/dotnet/roslyn/issues/8237 で追跡された IntelliSense のバグの症状です。</span><span class="sxs-lookup"><span data-stu-id="33ac6-119">This is a symptom of an IntelliSense bug tracked at https://github.com/dotnet/roslyn/issues/8237.</span></span> <span data-ttu-id="33ac6-120">コードが正しく、正常にコンパイルできる限り、このような見せかけ上の構文エラーは無視しても構いません。</span><span class="sxs-lookup"><span data-stu-id="33ac6-120">It is safe to ignore these spurious syntax errors as long as the code is correct and can be compiled successfully.</span></span> 

<span data-ttu-id="33ac6-121">`ThenInclude` に対して複数の呼び出しを連鎖させて、さらなるレベルの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-121">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="33ac6-122">このすべてを組み合わせて、複数のレベルと複数のルートの関連データを同じクエリ内に含めることができます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-122">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="33ac6-123">含まれているエンティティの 1 つについて複数の関連エンティティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-123">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="33ac6-124">たとえば、`Blogs` をクエリするときに、`Posts` を含め、さらに `Posts` の `Author` と `Tags` の両方を含めたい場合があります。</span><span class="sxs-lookup"><span data-stu-id="33ac6-124">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="33ac6-125">この場合、ルートから始まる各インクルード パスを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33ac6-125">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="33ac6-126">たとえば、`Blog -> Posts -> Author` と`Blog -> Posts -> Tags` です。</span><span class="sxs-lookup"><span data-stu-id="33ac6-126">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="33ac6-127">これで冗長的な結合を実現することにはならず、ほとんどの場合、SQL を生成するときに EF で結合は統合されます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-127">This does not mean you will get redundant joins; in most cases, EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="include-on-derived-types"></a><span data-ttu-id="33ac6-128">派生型に対するインクルード</span><span class="sxs-lookup"><span data-stu-id="33ac6-128">Include on derived types</span></span>

<span data-ttu-id="33ac6-129">`Include` と `ThenInclude` を使用して、派生型にのみ定義されているナビゲーションの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-129">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span> 

<span data-ttu-id="33ac6-130">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="33ac6-130">Given the following model:</span></span>

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

<span data-ttu-id="33ac6-131">生徒である全ユーザーの `School` ナビゲーションの内容を一括して読み込むには、いくつかのパターンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-131">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

- <span data-ttu-id="33ac6-132">キャストを使用する</span><span class="sxs-lookup"><span data-stu-id="33ac6-132">using cast</span></span>
  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

- <span data-ttu-id="33ac6-133">`as` 演算子を使用する</span><span class="sxs-lookup"><span data-stu-id="33ac6-133">using `as` operator</span></span>
  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

- <span data-ttu-id="33ac6-134">型 `string` のパラメーターを受け取る `Include` のオーバーロードを使用する</span><span class="sxs-lookup"><span data-stu-id="33ac6-134">using overload of `Include` that takes parameter of type `string`</span></span>
  ```csharp
  context.People.Include("Student").ToList()
  ```

### <a name="ignored-includes"></a><span data-ttu-id="33ac6-135">無視されるインクルード</span><span class="sxs-lookup"><span data-stu-id="33ac6-135">Ignored includes</span></span>

<span data-ttu-id="33ac6-136">クエリが開始されたエンティティ型のインスタンスを返さないようにクエリを変更した場合、インクルード演算子は無視されます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-136">If you change the query so that it no longer returns instances of the entity type that the query began with, then the include operators are ignored.</span></span>

<span data-ttu-id="33ac6-137">次の例では、インクルード演算子は `Blog` に基づいていますが、匿名型を返すようにクエリを変更するために `Select` 演算子が使用されています。</span><span class="sxs-lookup"><span data-stu-id="33ac6-137">In the following example, the include operators are based on the `Blog`, but then the `Select` operator is used to change the query to return an anonymous type.</span></span> <span data-ttu-id="33ac6-138">この場合、インクルード演算子には何の効果もありません。</span><span class="sxs-lookup"><span data-stu-id="33ac6-138">In this case, the include operators have no effect.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IgnoredInclude)]

<span data-ttu-id="33ac6-139">既定では、インクルード演算子が無視されると、EF Core では警告をログに記録します。</span><span class="sxs-lookup"><span data-stu-id="33ac6-139">By default, EF Core will log a warning when include operators are ignored.</span></span> <span data-ttu-id="33ac6-140">ログ記録の出力の表示に関する詳細については、[ログ記録](../miscellaneous/logging.md)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="33ac6-140">See [Logging](../miscellaneous/logging.md) for more information on viewing logging output.</span></span> <span data-ttu-id="33ac6-141">インクルード演算子が無視された場合の動作を、エラーをスローするか、または何もしないかに変更できます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-141">You can change the behavior when an include operator is ignored to either throw or do nothing.</span></span> <span data-ttu-id="33ac6-142">ASP.NET Core を使用している場合、通常は `DbContext.OnConfiguring` または `Startup.cs` でコンテキストのオプションを設定するときに、この変更が行われます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-142">This is done when setting up the options for your context - typically in `DbContext.OnConfiguring`, or in `Startup.cs` if you are using ASP.NET Core.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/ThrowOnIgnoredInclude/BloggingContext.cs#OnConfiguring)]

## <a name="explicit-loading"></a><span data-ttu-id="33ac6-143">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="33ac6-143">Explicit loading</span></span>

> [!NOTE]  
> <span data-ttu-id="33ac6-144">この機能は、EF Core 1.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="33ac6-144">This feature was introduced in EF Core 1.1.</span></span>

<span data-ttu-id="33ac6-145">`DbContext.Entry(...)` API を使用してナビゲーション プロパティを明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-145">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="33ac6-146">また、関連エンティティを返す個別のクエリを実行することで、ナビゲーション プロパティを明示的に読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-146">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="33ac6-147">変更の追跡が有効な場合、エンティティを読み込むと、EF Core は、既に読み込まれているエンティティがあれば参照するように、新しく読み込まれたエンティティのナビゲーション プロパティを自動的に設定します。また、新しく読み込まれたエンティティを参照するように、既に読み込まれているエンティティのナビゲーション プロパティを自動的に設定します。</span><span class="sxs-lookup"><span data-stu-id="33ac6-147">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="33ac6-148">関連エンティティのクエリ</span><span class="sxs-lookup"><span data-stu-id="33ac6-148">Querying related entities</span></span>

<span data-ttu-id="33ac6-149">また、ナビゲーション プロパティの内容を表す LINQ クエリを取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-149">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="33ac6-150">そのため、関連エンティティをメモリに読み込まずに集計演算子を実行するなどの操作を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-150">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="33ac6-151">関連エンティティがメモリに読み込まれるようにフィルター処理することもできます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-151">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="33ac6-152">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="33ac6-152">Lazy loading</span></span>

> [!NOTE]  
> <span data-ttu-id="33ac6-153">この機能は、EF Core 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="33ac6-153">This feature was introduced in EF Core 2.1.</span></span>

<span data-ttu-id="33ac6-154">遅延読み込みを使用するには、[Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) パッケージをインストールし、`UseLazyLoadingProxies` を呼び出して有効にする方法が最も簡単です。</span><span class="sxs-lookup"><span data-stu-id="33ac6-154">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="33ac6-155">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33ac6-155">For example:</span></span>
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```
<span data-ttu-id="33ac6-156">または、AddDbContext を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="33ac6-156">Or when using AddDbContext:</span></span>
```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```
<span data-ttu-id="33ac6-157">EF Core は、オーバーライド可能なナビゲーション プロパティの場合に遅延読み込みを有効にします。つまり `virtual` であり、継承可能なクラスのナビゲーション プロパティである必要があります。</span><span class="sxs-lookup"><span data-stu-id="33ac6-157">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="33ac6-158">たとえば、次のエンティティでは、`Post.Blog` および `Blog.Posts` ナビゲーション プロパティの遅延読み込みが実行されます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-158">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>
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
### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="33ac6-159">プロキシを使用しない遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="33ac6-159">Lazy loading without proxies</span></span>

<span data-ttu-id="33ac6-160">遅延読み込みプロキシは、[エンティティ型コンストラクター](../modeling/constructors.md)に関するページで説明されているように、エンティティに `ILazyLoader` サービスを挿入することで機能します。</span><span class="sxs-lookup"><span data-stu-id="33ac6-160">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="33ac6-161">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33ac6-161">For example:</span></span>
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
<span data-ttu-id="33ac6-162">この場合、エンティティ型を継承したり、ナビゲーション プロパティを仮想にしたりする必要はありません。コンテキストにアタッチされたときに、`new` で作成されたエンティティ インスタンスの遅延読み込みを実行することができます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-162">This doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="33ac6-163">ただし、これには、[Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) パッケージで定義される `ILazyLoader` サービスが必要です。</span><span class="sxs-lookup"><span data-stu-id="33ac6-163">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="33ac6-164">このパッケージに含まれる型のセットは、それに依存してもほとんど影響がないように、最低限のセットになっています。</span><span class="sxs-lookup"><span data-stu-id="33ac6-164">This package contains a minimal set of types so that there is very little impact in depending on it.</span></span> <span data-ttu-id="33ac6-165">しかし、エンティティ型で EF Core パッケージへの依存を完全に回避するには、デリゲートとして `ILazyLoader.Load` メソッドを挿入することが可能です。</span><span class="sxs-lookup"><span data-stu-id="33ac6-165">However, to completely avoid depending on any EF Core packages in the entity types, it is possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="33ac6-166">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33ac6-166">For example:</span></span>
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
<span data-ttu-id="33ac6-167">上記のコードでは、デリゲートの使用をビット クリーナーにするために、拡張メソッド `Load` を使用しています。</span><span class="sxs-lookup"><span data-stu-id="33ac6-167">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>
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
> <span data-ttu-id="33ac6-168">遅延読み込みデリゲートのコンストラクター パラメーターは、"lazyLoader" と指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33ac6-168">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="33ac6-169">これとは別の名前を使用する構成が今後のリリースで計画されています。</span><span class="sxs-lookup"><span data-stu-id="33ac6-169">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="33ac6-170">関連データとシリアル化</span><span class="sxs-lookup"><span data-stu-id="33ac6-170">Related data and serialization</span></span>

<span data-ttu-id="33ac6-171">EF Core はナビゲーション プロパティを自動的に修正するので、最終的にオブジェクト グラフの循環が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="33ac6-171">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="33ac6-172">たとえば、ブログとその関連する投稿を読み込むと、投稿のコレクションを参照するブログ オブジェクトになります。</span><span class="sxs-lookup"><span data-stu-id="33ac6-172">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="33ac6-173">これらの各投稿には元のブログへの参照が含まれることになります。</span><span class="sxs-lookup"><span data-stu-id="33ac6-173">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="33ac6-174">一部のシリアル化フレームワークでは、このような循環は許可されていません。</span><span class="sxs-lookup"><span data-stu-id="33ac6-174">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="33ac6-175">たとえば、Json.NET では、循環が発生した場合に次の例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="33ac6-175">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="33ac6-176">Newtonsoft.Json.JsonSerializationException:Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span><span class="sxs-lookup"><span data-stu-id="33ac6-176">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="33ac6-177">ASP.NET Core を使用している場合は、オブジェクト グラフで見つかった循環を無視するように Json.NET を構成できます。</span><span class="sxs-lookup"><span data-stu-id="33ac6-177">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="33ac6-178">この操作は、`Startup.cs` の `ConfigureServices(...)` メソッドで実行します。</span><span class="sxs-lookup"><span data-stu-id="33ac6-178">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

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
