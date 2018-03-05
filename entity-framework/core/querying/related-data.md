---
title: "読み込みに関連したデータの EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
ms.technology: entity-framework-core
uid: core/querying/related-data
ms.openlocfilehash: 0d7705e0e5368435536e98d319c853ea8c732643
ms.sourcegitcommit: 8f3be0a2a394253efb653388ec66bda964e5ee1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
# <a name="loading-related-data"></a><span data-ttu-id="d2223-102">関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="d2223-102">Loading Related Data</span></span>

<span data-ttu-id="d2223-103">Entity Framework Core では、関連エンティティの読み込みをモデルで、ナビゲーション プロパティを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="d2223-103">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="d2223-104">関連するデータの読み込みに使用される 3 つの一般的な O/RM パターンがあります。</span><span class="sxs-lookup"><span data-stu-id="d2223-104">There are three common O/RM patterns used to load related data.</span></span>
* <span data-ttu-id="d2223-105">**一括読み込み**最初のクエリの一部として、データベースから、関連するデータが読み込まれたことを意味します。</span><span class="sxs-lookup"><span data-stu-id="d2223-105">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="d2223-106">**明示的な読み込み**は後で、データベースから、関連するデータを明示的に読み込むことを意味します。</span><span class="sxs-lookup"><span data-stu-id="d2223-106">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="d2223-107">**遅延読み込み**ナビゲーション プロパティにアクセスする場合、関連するデータが透過的にデータベースから読み込むことを意味します。</span><span class="sxs-lookup"><span data-stu-id="d2223-107">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]  
> <span data-ttu-id="d2223-108">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="d2223-108">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="d2223-109">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="d2223-109">Eager loading</span></span>

<span data-ttu-id="d2223-110">使用することができます、`Include`クエリの結果に含まれる関連データを指定します。</span><span class="sxs-lookup"><span data-stu-id="d2223-110">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="d2223-111">次の例では、ブログの結果に返される必要があります、`Posts`プロパティとして、関連する投稿に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d2223-111">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> <span data-ttu-id="d2223-112">Entity Framework Core は自動的に修正をするナビゲーション プロパティをコンテキストのインスタンスに以前に読み込まれたその他のエンティティです。</span><span class="sxs-lookup"><span data-stu-id="d2223-112">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="d2223-113">場合でも、明示的には、ナビゲーション プロパティのデータを含まない、いくつかの場合は、プロパティを作成してか、以前に読み込まれたすべての関連するエンティティ。</span><span class="sxs-lookup"><span data-stu-id="d2223-113">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>


<span data-ttu-id="d2223-114">単一のクエリでは、複数のリレーションシップから関連するデータを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="d2223-114">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="d2223-115">複数のレベルを含む</span><span class="sxs-lookup"><span data-stu-id="d2223-115">Including multiple levels</span></span>

<span data-ttu-id="d2223-116">使用して関連するデータの複数のレベルを含むへのリレーションシップを通じてドリルダウンすることができます、`ThenInclude`メソッドです。</span><span class="sxs-lookup"><span data-stu-id="d2223-116">You can drill down thru relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="d2223-117">次の例では、すべてのブログの関連する投稿を各投稿の作成者を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="d2223-117">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleThenInclude)]

> [!NOTE]  
> <span data-ttu-id="d2223-118">Visual Studio の現在のバージョンが不適切なコードの入力候補オプションを提供を正しい式を使用する場合に、構文エラーが発生フラグが付けられますが、`ThenInclude`メソッド コレクション ナビゲーション プロパティ。</span><span class="sxs-lookup"><span data-stu-id="d2223-118">Current versions of Visual Studio offer incorrect code completion options and can cause correct expressions to be flagged with syntax errors when using the `ThenInclude` method after a collection navigation property.</span></span> <span data-ttu-id="d2223-119">これは、https://github.com/dotnet/roslyn/issues/8237 で追跡 IntelliSense バグが発生する可能性です。</span><span class="sxs-lookup"><span data-stu-id="d2223-119">This is a symptom of an IntelliSense bug tracked at https://github.com/dotnet/roslyn/issues/8237.</span></span> <span data-ttu-id="d2223-120">これらの見かけ上の構文エラーを無視するコードが正しいと正常にコンパイルできる限り安全です。</span><span class="sxs-lookup"><span data-stu-id="d2223-120">It is safe to ignore these spurious syntax errors as long as the code is correct and can be compiled successfully.</span></span> 

<span data-ttu-id="d2223-121">複数の呼び出しをチェーンする`ThenInclude`関連データのレベルを含めてさらに続行します。</span><span class="sxs-lookup"><span data-stu-id="d2223-121">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="d2223-122">これを同じクエリで複数のレベルと複数のルートから関連するデータを含めるにはすべてを組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="d2223-122">You can combine all of this to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="d2223-123">いずれかが含まれるエンティティの複数の関連エンティティを追加することがあります。</span><span class="sxs-lookup"><span data-stu-id="d2223-123">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="d2223-124">たとえば、クエリを実行するときに`Blog`が含まれています、`Posts`両方を格納して、`Author`と`Tags`の`Posts`です。</span><span class="sxs-lookup"><span data-stu-id="d2223-124">For example, when querying `Blog`s, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="d2223-125">これを行うには、ルートからのパスを含めるそれぞれを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d2223-125">To do this, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="d2223-126">たとえば、`Blog -> Posts -> Author`と`Blog -> Posts -> Tags`です。</span><span class="sxs-lookup"><span data-stu-id="d2223-126">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="d2223-127">表示される冗長な結合、EF の統合はほとんどの場合、結合 SQL を生成するときにありません。</span><span class="sxs-lookup"><span data-stu-id="d2223-127">This does not mean you will get redundant joins, in most cases EF will consolidate the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="include-on-derived-types"></a><span data-ttu-id="d2223-128">派生型では、します。</span><span class="sxs-lookup"><span data-stu-id="d2223-128">Include on derived types</span></span>

<span data-ttu-id="d2223-129">ナビゲーションを使用して、派生型でのみ定義から関連するデータを含めることができます`Include`と`ThenInclude`です。</span><span class="sxs-lookup"><span data-stu-id="d2223-129">You can include related data from navigations defined only on a derived type using `Include` and `ThenInclude`.</span></span> 

<span data-ttu-id="d2223-130">次のようなモデルを指定します。</span><span class="sxs-lookup"><span data-stu-id="d2223-130">Given the following model:</span></span>

```Csharp
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

<span data-ttu-id="d2223-131">内容`School`受講者は、すべてのユーザーのナビゲーションを集中的に読み込めるパターンの番号を使用します。</span><span class="sxs-lookup"><span data-stu-id="d2223-131">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

- <span data-ttu-id="d2223-132">キャストを使用してください。</span><span class="sxs-lookup"><span data-stu-id="d2223-132">using cast</span></span>
```Csharp
context.People.Include(person => ((Student)person).School).ToList()
```

- <span data-ttu-id="d2223-133">使用して`as`演算子</span><span class="sxs-lookup"><span data-stu-id="d2223-133">using `as` operator</span></span>
```Csharp
context.People.Include(person => (person as Student).School).ToList()
```

- <span data-ttu-id="d2223-134">オーバー ロードを使用して`Include`型のパラメーターを受け取る `string`</span><span class="sxs-lookup"><span data-stu-id="d2223-134">using overload of `Include` that takes parameter of type `string`</span></span>
```Csharp
context.People.Include("Student").ToList()
```

### <a name="ignored-includes"></a><span data-ttu-id="d2223-135">無視が含まれています</span><span class="sxs-lookup"><span data-stu-id="d2223-135">Ignored includes</span></span>

<span data-ttu-id="d2223-136">不要になったクエリの開始とエンティティ型のインスタンスを返すように、クエリを変更する場合は、include 演算子は無視されます。</span><span class="sxs-lookup"><span data-stu-id="d2223-136">If you change the query so that it no longer returns instances of the entity type that the query began with, then the include operators are ignored.</span></span>

<span data-ttu-id="d2223-137">次の例では、include 演算子に基づいて、 `Blog`、ところが、`Select`演算子を使用して、匿名型を取得するクエリを変更します。</span><span class="sxs-lookup"><span data-stu-id="d2223-137">In the following example, the include operators are based on the `Blog`, but then the `Select` operator is used to change the query to return an anonymous type.</span></span> <span data-ttu-id="d2223-138">この場合、include 演算子がある影響しません。</span><span class="sxs-lookup"><span data-stu-id="d2223-138">In this case, the include operators have no effect.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IgnoredInclude)]

<span data-ttu-id="d2223-139">既定では、EF コアが、警告をログとは、演算子は無視されます。</span><span class="sxs-lookup"><span data-stu-id="d2223-139">By default, EF Core will log a warning when include operators are ignored.</span></span> <span data-ttu-id="d2223-140">参照してください[ログ](../miscellaneous/logging.md)ログ出力を表示する方法の詳細についてです。</span><span class="sxs-lookup"><span data-stu-id="d2223-140">See [Logging](../miscellaneous/logging.md) for more information on viewing logging output.</span></span> <span data-ttu-id="d2223-141">スローするか、何もする include 演算子が無視される動作を変更できます。</span><span class="sxs-lookup"><span data-stu-id="d2223-141">You can change the behavior when an include operator is ignored to either throw or do nothing.</span></span> <span data-ttu-id="d2223-142">では通常、コンテキストでのオプションを設定するときにこれは、 `DbContext.OnConfiguring`、または`Startup.cs`ASP.NET Core を使用している場合。</span><span class="sxs-lookup"><span data-stu-id="d2223-142">This is done when setting up the options for your context - typically in `DbContext.OnConfiguring`, or in `Startup.cs` if you are using ASP.NET Core.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/ThrowOnIgnoredInclude/BloggingContext.cs#OnConfiguring)]

## <a name="explicit-loading"></a><span data-ttu-id="d2223-143">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="d2223-143">Explicit loading</span></span>

> [!NOTE]  
> <span data-ttu-id="d2223-144">この機能は、EF コア 1.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="d2223-144">This feature was introduced in EF Core 1.1.</span></span>

<span data-ttu-id="d2223-145">使用して、ナビゲーション プロパティを明示的に読み込むことができます、 `DbContext.Entry(...)` API です。</span><span class="sxs-lookup"><span data-stu-id="d2223-145">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="d2223-146">ナビゲーション プロパティは、関連エンティティを返す別のクエリを実行することによっても明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="d2223-146">You can also explicitly load a navigation property by executing a seperate query that returns the related entities.</span></span> <span data-ttu-id="d2223-147">変更の追跡が有効になっているかどうか、EF コアのすべてのエンティティは既に読み込まれているを参照する新たに読み込まれたエンティティのナビゲーション プロパティを設定を参照してくださいに既に読み込まれているエンティティのナビゲーション プロパティを設定が自動的にエンティティを読み込むときに、新たに読み込まれたエンティティです。</span><span class="sxs-lookup"><span data-stu-id="d2223-147">If change tracking is enabled, then when loading an entity, EF Core will automatically set the navigation properties of the newly-loaded entitiy to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly-loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="d2223-148">関連エンティティのクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d2223-148">Querying related entities</span></span>

<span data-ttu-id="d2223-149">LINQ クエリを表すナビゲーション プロパティの内容を取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="d2223-149">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="d2223-150">これにより、それらをメモリに読み込むことがなく、関連エンティティを aggregate 操作を実行しているなどの作業を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d2223-150">This allows you to do things such as running an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="d2223-151">関連エンティティは、メモリに読み込まれるをフィルターすることもできます。</span><span class="sxs-lookup"><span data-stu-id="d2223-151">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="d2223-152">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="d2223-152">Lazy loading</span></span>

> [!NOTE]  
> <span data-ttu-id="d2223-153">この機能は、EF コア 2.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="d2223-153">This feature was introduced in EF Core 2.1.</span></span>

<span data-ttu-id="d2223-154">遅延読み込みを使用する最も簡単な方法は、インストールすることによって、 [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)パッケージと呼び出しを有効にすると`UseLazyLoadingProxies`です。</span><span class="sxs-lookup"><span data-stu-id="d2223-154">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="d2223-155">例:</span><span class="sxs-lookup"><span data-stu-id="d2223-155">For example:</span></span>
```Csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```
<span data-ttu-id="d2223-156">または、AddDbContext を使用する場合。</span><span class="sxs-lookup"><span data-stu-id="d2223-156">Or when using AddDbContext:</span></span>
```Csharp
    .AddDbContext<BloggingContext>(
        b => b.UseLazyLoadingProxies()
              .UseSqlServer(myConnectionString));
```
<span data-ttu-id="d2223-157">EF コアを遅延読み込みがされる--オーバーライド可能なナビゲーション プロパティに対して有効にする必要があります`virtual`およびクラスから継承されることができます。</span><span class="sxs-lookup"><span data-stu-id="d2223-157">EF Core will then enable lazy-loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="d2223-158">たとえば、次のエンティティで、`Post.Blog`と`Blog.Posts`遅延読み込みされたナビゲーション プロパティになります。</span><span class="sxs-lookup"><span data-stu-id="d2223-158">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>
```Csharp
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
### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="d2223-159">プロキシなしの遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="d2223-159">Lazy-loading without proxies</span></span>

<span data-ttu-id="d2223-160">遅延読み込みのプロキシを挿入することによって機能、 `ILazyLoader` 」の説明に従って、エンティティ サービス[エンティティ型のコンス トラクター](../modeling/constructors.md)です。</span><span class="sxs-lookup"><span data-stu-id="d2223-160">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](../modeling/constructors.md).</span></span> <span data-ttu-id="d2223-161">例:</span><span class="sxs-lookup"><span data-stu-id="d2223-161">For example:</span></span>
```Csharp
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
        get => LazyLoader?.Load(this, ref _posts);
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
        get => LazyLoader?.Load(this, ref _blog);
        set => _blog = value;
    }
}
```
<span data-ttu-id="d2223-162">これから継承するエンティティ型または仮想するナビゲーション プロパティを必要としないできで作成されたエンティティ インスタンス`new`遅延読み込み 1 回に、コンテキストにアタッチします。</span><span class="sxs-lookup"><span data-stu-id="d2223-162">This doesn't require entity types to be inherited from or navigation properties to be virtual and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="d2223-163">ただしへの参照が必要、`ILazyLoader`サービスで、エンティティ型を EF コア アセンブリに結合します。</span><span class="sxs-lookup"><span data-stu-id="d2223-163">However, it requires a reference to the `ILazyLoader` service, which couples entity types to the EF Core assembly.</span></span> <span data-ttu-id="d2223-164">この EF コアを回避するを許可、`ILazyLoader.Load`代理人として挿入するメソッド。</span><span class="sxs-lookup"><span data-stu-id="d2223-164">To avoid this EF Core allows the `ILazyLoader.Load` method to be injected as a delegate.</span></span> <span data-ttu-id="d2223-165">例:</span><span class="sxs-lookup"><span data-stu-id="d2223-165">For example:</span></span>
```Csharp
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
        get => LazyLoader?.Load(this, ref _posts);
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
        get => LazyLoader?.Load(this, ref _blog);
        set => _blog = value;
    }
}
```
<span data-ttu-id="d2223-166">使用して上記のコード、`Load`ビット クリーナー デリゲートを使用できるようにする拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="d2223-166">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>
```Csharp
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
> <span data-ttu-id="d2223-167">遅延読み込みデリゲート コンス トラクターのパラメーターには、"lazyLoader"を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="d2223-167">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="d2223-168">今後のリリースでこれが予定されて別の名前を使用する構成。</span><span class="sxs-lookup"><span data-stu-id="d2223-168">Configuration to use a different name this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="d2223-169">関連データとシリアル化</span><span class="sxs-lookup"><span data-stu-id="d2223-169">Related data and serialization</span></span>

<span data-ttu-id="d2223-170">EF コアは自動的に修正をナビゲーション プロパティ、しまうサイクルで、オブジェクト グラフ内ためです。</span><span class="sxs-lookup"><span data-stu-id="d2223-170">Because EF Core will automatically fix-up navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="d2223-171">たとえば、ブログを読み込みが関連付けられて投稿投稿のコレクションを参照するブログ オブジェクトになります。</span><span class="sxs-lookup"><span data-stu-id="d2223-171">For example, Loading a blog and it's related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="d2223-172">これらの投稿の各ブログへの参照になります。</span><span class="sxs-lookup"><span data-stu-id="d2223-172">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="d2223-173">一部のシリアル化フレームワークでは、このようなサイクルは許可されません。</span><span class="sxs-lookup"><span data-stu-id="d2223-173">Some serialization frameworks do not allow such cycles.</span></span> <span data-ttu-id="d2223-174">たとえば、Json.NET では、循環参照が発生した場合、次の例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="d2223-174">For example, Json.NET will throw the following exception if a cycle is encountered.</span></span>

> <span data-ttu-id="d2223-175">Newtonsoft.Json.JsonSerializationException: 自己のループを型 'MyApplication.Models.Blog' と 'ブログ' プロパティの検出を参照します。</span><span class="sxs-lookup"><span data-stu-id="d2223-175">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="d2223-176">ASP.NET Core を使用している場合は、オブジェクト グラフ内で見つかったサイクルを無視する Json.NET を構成できます。</span><span class="sxs-lookup"><span data-stu-id="d2223-176">If you are using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="d2223-177">これには、`ConfigureServices(...)`メソッド`Startup.cs`です。</span><span class="sxs-lookup"><span data-stu-id="d2223-177">This is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

``` csharp
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
