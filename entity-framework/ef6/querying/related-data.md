---
title: 関連エンティティを読み込んでいます-EF6
description: Entity Framework 6 で関連エンティティを読み込んでいます
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/related-data
ms.openlocfilehash: 30d5bcff0696b4886655f5413e4878f1a611cf88
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064693"
---
# <a name="loading-related-entities"></a><span data-ttu-id="2d848-103">関連エンティティを読み込んでいます</span><span class="sxs-lookup"><span data-stu-id="2d848-103">Loading Related Entities</span></span>

<span data-ttu-id="2d848-104">Entity Framework は、関連するデータの一括読み込み、遅延読み込み、および明示的な読み込みを読み込む3つの方法をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="2d848-104">Entity Framework supports three ways to load related data - eager loading, lazy loading and explicit loading.</span></span> <span data-ttu-id="2d848-105">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="2d848-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>

## <a name="eagerly-loading"></a><span data-ttu-id="2d848-106">集中的の読み込み</span><span class="sxs-lookup"><span data-stu-id="2d848-106">Eagerly Loading</span></span>

<span data-ttu-id="2d848-107">一括読み込みは、ある種類のエンティティに対するクエリでも、クエリの一部として関連エンティティを読み込むプロセスです。</span><span class="sxs-lookup"><span data-stu-id="2d848-107">Eager loading is the process whereby a query for one type of entity also loads related entities as part of the query.</span></span> <span data-ttu-id="2d848-108">一括読み込みは、Include メソッドを使用して実現されます。</span><span class="sxs-lookup"><span data-stu-id="2d848-108">Eager loading is achieved by use of the Include method.</span></span> <span data-ttu-id="2d848-109">たとえば、次のクエリでは、ブログと各ブログに関連するすべての投稿が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2d848-109">For example, the queries below will load blogs and all the posts related to each blog.</span></span>

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts)
                        .ToList();

    // Load one blog and its related posts.
    var blog1 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include(b => b.Posts)
                       .FirstOrDefault();

    // Load all blogs and related posts
    // using a string to specify the relationship.
    var blogs2 = context.Blogs
                        .Include("Posts")
                        .ToList();

    // Load one blog and its related posts
    // using a string to specify the relationship.
    var blog2 = context.Blogs
                       .Where(b => b.Name == "ADO.NET Blog")
                       .Include("Posts")
                       .FirstOrDefault();
}
```

> [!NOTE]
> <span data-ttu-id="2d848-110">Include は、system.string 名前空間の拡張メソッドであるため、その名前空間を使用していることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="2d848-110">Include is an extension method in the System.Data.Entity namespace so make sure you are using that namespace.</span></span>

### <a name="eagerly-loading-multiple-levels"></a><span data-ttu-id="2d848-111">集中的複数レベルの読み込み</span><span class="sxs-lookup"><span data-stu-id="2d848-111">Eagerly loading multiple levels</span></span>

<span data-ttu-id="2d848-112">また、関連エンティティの複数のレベルを集中的に読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="2d848-112">It is also possible to eagerly load multiple levels of related entities.</span></span> <span data-ttu-id="2d848-113">次のクエリは、コレクションと参照の両方のナビゲーションプロパティでこの操作を行う方法の例を示しています。</span><span class="sxs-lookup"><span data-stu-id="2d848-113">The queries below show examples of how to do this for both collection and reference navigation properties.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments.
    var blogs1 = context.Blogs
                        .Include(b => b.Posts.Select(p => p.Comments))
                        .ToList();

    // Load all users, their related profiles, and related avatar.
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships.
    var blogs2 = context.Blogs
                        .Include("Posts.Comments")
                        .ToList();

    // Load all users, their related profiles, and related avatar  
    // using a string to specify the relationships.
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

> [!NOTE]
> <span data-ttu-id="2d848-114">現在、どの関連エンティティが読み込まれているかをフィルター処理することはできません。</span><span class="sxs-lookup"><span data-stu-id="2d848-114">It is not currently possible to filter which related entities are loaded.</span></span> <span data-ttu-id="2d848-115">Include は常に、関連するすべてのエンティティを取り込みます。</span><span class="sxs-lookup"><span data-stu-id="2d848-115">Include will always bring in all related entities.</span></span>  

## <a name="lazy-loading"></a><span data-ttu-id="2d848-116">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="2d848-116">Lazy Loading</span></span>

<span data-ttu-id="2d848-117">遅延読み込みは、エンティティまたはエンティティを参照するプロパティがアクセスされるときに、エンティティまたはエンティティのコレクションがデータベースから自動的に読み込まれるようにするプロセスです。</span><span class="sxs-lookup"><span data-stu-id="2d848-117">Lazy loading is the process whereby an entity or collection of entities is automatically loaded from the database the first time that a property referring to the entity/entities is accessed.</span></span> <span data-ttu-id="2d848-118">POCO エンティティ型を使用する場合、遅延読み込みは、派生プロキシ型のインスタンスを作成してから、仮想プロパティをオーバーライドして読み込みフックを追加することで実現されます。</span><span class="sxs-lookup"><span data-stu-id="2d848-118">When using POCO entity types, lazy loading is achieved by creating instances of derived proxy types and then overriding virtual properties to add the loading hook.</span></span> <span data-ttu-id="2d848-119">たとえば、次に定義されているブログエンティティクラスを使用する場合、投稿ナビゲーションプロパティに初めてアクセスするときに関連する投稿が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2d848-119">For example, when using the Blog entity class defined below, the related Posts will be loaded the first time the Posts navigation property is accessed:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

### <a name="turn-lazy-loading-off-for-serialization"></a><span data-ttu-id="2d848-120">シリアル化の遅延読み込みをオフにする</span><span class="sxs-lookup"><span data-stu-id="2d848-120">Turn lazy loading off for serialization</span></span>

<span data-ttu-id="2d848-121">遅延読み込みとシリアル化にはあまり適していません。また、遅延読み込みが有効になっているので、データベース全体のクエリを終了することもできます。</span><span class="sxs-lookup"><span data-stu-id="2d848-121">Lazy loading and serialization don’t mix well, and if you aren’t careful you can end up querying for your entire database just because lazy loading is enabled.</span></span> <span data-ttu-id="2d848-122">ほとんどのシリアライザーは、型のインスタンスの各プロパティにアクセスすることによって機能します。</span><span class="sxs-lookup"><span data-stu-id="2d848-122">Most serializers work by accessing each property on an instance of a type.</span></span> <span data-ttu-id="2d848-123">プロパティアクセスは遅延読み込みをトリガーするため、より多くのエンティティがシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="2d848-123">Property access triggers lazy loading, so more entities get serialized.</span></span> <span data-ttu-id="2d848-124">これらのエンティティにはプロパティがアクセスされ、さらに多くのエンティティが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2d848-124">On those entities properties are accessed, and even more entities are loaded.</span></span> <span data-ttu-id="2d848-125">エンティティをシリアル化する前に、遅延読み込みをオフにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2d848-125">It’s a good practice to turn lazy loading off before you serialize an entity.</span></span> <span data-ttu-id="2d848-126">以降のセクションでは、その方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2d848-126">The following sections show how to do this.</span></span>

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a><span data-ttu-id="2d848-127">特定のナビゲーションプロパティの遅延読み込みをオフにする</span><span class="sxs-lookup"><span data-stu-id="2d848-127">Turning off lazy loading for specific navigation properties</span></span>

<span data-ttu-id="2d848-128">Post コレクションの遅延読み込みを無効にするには、Post プロパティを非仮想にします。</span><span class="sxs-lookup"><span data-stu-id="2d848-128">Lazy loading of the Posts collection can be turned off by making the Posts property non-virtual:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }
    public string Tags { get; set; }

    public ICollection<Post> Posts { get; set; }
}
```

<span data-ttu-id="2d848-129">Post コレクションの読み込みは、一括読み込みを使用して行うことができます (上記の「 *集中的の読み込み* 」を参照してください)。または Load メソッドを使用します (以下の「 *明示的な読み込み* 」を参照)。</span><span class="sxs-lookup"><span data-stu-id="2d848-129">Loading of the Posts collection can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>

### <a name="turn-off-lazy-loading-for-all-entities"></a><span data-ttu-id="2d848-130">すべてのエンティティの遅延読み込みをオフにする</span><span class="sxs-lookup"><span data-stu-id="2d848-130">Turn off lazy loading for all entities</span></span>

<span data-ttu-id="2d848-131">遅延読み込みは、構成プロパティにフラグを設定することによって、コンテキスト内のすべてのエンティティに対して無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="2d848-131">Lazy loading can be turned off for all entities in the context by setting a flag on the Configuration property.</span></span> <span data-ttu-id="2d848-132">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2d848-132">For example:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```

<span data-ttu-id="2d848-133">関連エンティティの読み込みは、一括読み込みを使用して実行できます (上記の「 *集中的の読み込み* 」を参照してください)。または Load メソッドを使用します (以下の「 *明示的な読み込み* 」を参照)。</span><span class="sxs-lookup"><span data-stu-id="2d848-133">Loading of related entities can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>

## <a name="explicitly-loading"></a><span data-ttu-id="2d848-134">明示的な読み込み</span><span class="sxs-lookup"><span data-stu-id="2d848-134">Explicitly Loading</span></span>

<span data-ttu-id="2d848-135">遅延読み込みが無効になっていても、関連エンティティを遅延読み込みすることはできますが、明示的な呼び出しを使用して実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2d848-135">Even with lazy loading disabled it is still possible to lazily load related entities, but it must be done with an explicit call.</span></span> <span data-ttu-id="2d848-136">これを行うには、関連エンティティのエントリに対して Load メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="2d848-136">To do so you use the Load method on the related entity’s entry.</span></span> <span data-ttu-id="2d848-137">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2d848-137">For example:</span></span>

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post.
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string.
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog.
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog).Collection("Posts").Load();
}
```

> [!NOTE]
> <span data-ttu-id="2d848-138">参照メソッドは、エンティティに別の1つのエンティティへのナビゲーションプロパティがある場合に使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2d848-138">The Reference method should be used when an entity has a navigation property to another single entity.</span></span> <span data-ttu-id="2d848-139">一方、エンティティに他のエンティティのコレクションへのナビゲーションプロパティがある場合は、コレクションメソッドを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2d848-139">On the other hand, the Collection method should be used when an entity has a navigation property to a collection of other entities.</span></span>

### <a name="applying-filters-when-explicitly-loading-related-entities"></a><span data-ttu-id="2d848-140">関連エンティティを明示的に読み込むときにフィルターを適用する</span><span class="sxs-lookup"><span data-stu-id="2d848-140">Applying filters when explicitly loading related entities</span></span>

<span data-ttu-id="2d848-141">クエリメソッドを使用すると、関連エンティティを読み込むときに使用 Entity Framework 基になるクエリにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="2d848-141">The Query method provides access to the underlying query that Entity Framework will use when loading related entities.</span></span> <span data-ttu-id="2d848-142">その後、LINQ を使用してクエリにフィルターを適用してから、そのクエリを実行する前に、ToList、Load などの LINQ 拡張メソッドを呼び出すことができます。クエリメソッドは参照とコレクションの両方のナビゲーションプロパティと共に使用できますが、コレクションの一部だけを読み込むために使用できるコレクションに最も役立ちます。</span><span class="sxs-lookup"><span data-stu-id="2d848-142">You can then use LINQ to apply filters to the query before executing it with a call to a LINQ extension method such as ToList, Load, etc. The Query method can be used with both reference and collection navigation properties but is most useful for collections where it can be used to load only part of the collection.</span></span> <span data-ttu-id="2d848-143">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2d848-143">For example:</span></span>

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog.
    context.Entry(blog)
           .Collection(b => b.Posts)
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog
    // using a string to specify the relationship.
    context.Entry(blog)
           .Collection("Posts")
           .Query()
           .Where(p => p.Tags.Contains("entity-framework"))
           .Load();
}
```

<span data-ttu-id="2d848-144">クエリメソッドを使用する場合は、通常、ナビゲーションプロパティの遅延読み込みを無効にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2d848-144">When using the Query method it is usually best to turn off lazy loading for the navigation property.</span></span> <span data-ttu-id="2d848-145">これは、フィルター選択されたクエリの実行前または実行後に、遅延読み込みメカニズムによってコレクション全体が自動的に読み込まれるためです。</span><span class="sxs-lookup"><span data-stu-id="2d848-145">This is because otherwise the entire collection may get loaded automatically by the lazy loading mechanism either before or after the filtered query has been executed.</span></span>

> [!NOTE]
> <span data-ttu-id="2d848-146">リレーションシップは、ラムダ式の代わりに文字列として指定できますが、文字列が使用されている場合、返される IQueryable はジェネリックではないため、通常はキャストメソッドを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2d848-146">While the relationship can be specified as a string instead of a lambda expression, the returned IQueryable is not generic when a string is used and so the Cast method is usually needed before anything useful can be done with it.</span></span>

## <a name="using-query-to-count-related-entities-without-loading-them"></a><span data-ttu-id="2d848-147">クエリを使用して関連エンティティを読み込んでカウントする</span><span class="sxs-lookup"><span data-stu-id="2d848-147">Using Query to count related entities without loading them</span></span>

<span data-ttu-id="2d848-148">場合によっては、データベース内の別のエンティティに関連付けられているエンティティの数を把握することが役に立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="2d848-148">Sometimes it is useful to know how many entities are related to another entity in the database without actually incurring the cost of loading all those entities.</span></span> <span data-ttu-id="2d848-149">LINQ Count メソッドを使用したクエリメソッドを使用してこれを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="2d848-149">The Query method with the LINQ Count method can be used to do this.</span></span> <span data-ttu-id="2d848-150">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="2d848-150">For example:</span></span>

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has.
    var postCount = context.Entry(blog)
                           .Collection(b => b.Posts)
                           .Query()
                           .Count();
}
```
