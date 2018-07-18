---
title: 関連エンティティの EF6 の読み込み
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: c8417e18-a2ee-499c-9ce9-2a48cc5b468a
caps.latest.revision: 3
ms.openlocfilehash: e7adc9aea11a7a8e9b87b4f9e9120aa7316588db
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122316"
---
# <a name="loading-related-entities"></a><span data-ttu-id="fd9cb-102">関連エンティティを読み込む</span><span class="sxs-lookup"><span data-stu-id="fd9cb-102">Loading Related Entities</span></span>
<span data-ttu-id="fd9cb-103">Entity Framework には、関連データの一括読み込み、遅延読み込みと明示的な読み込みを読み込む 3 つの方法がサポートしています。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-103">Entity Framework supports three ways to load related data - eager loading, lazy loading and explicit loading.</span></span> <span data-ttu-id="fd9cb-104">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-104">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="eagerly-loading"></a><span data-ttu-id="fd9cb-105">集中読み込み</span><span class="sxs-lookup"><span data-stu-id="fd9cb-105">Eagerly Loading</span></span>  

<span data-ttu-id="fd9cb-106">一括読み込みは、このエンティティの 1 つの種類のクエリ関連エンティティも読み込むクエリの一部としてプロセスです。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-106">Eager loading is the process whereby a query for one type of entity also loads related entities as part of the query.</span></span> <span data-ttu-id="fd9cb-107">一括読み込みは、Include メソッドを使用して実現されます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-107">Eager loading is achieved by use of the Include method.</span></span> <span data-ttu-id="fd9cb-108">たとえば、下記のクエリは、ブログや各ブログに関連するすべての投稿を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-108">For example, the queries below will load blogs and all the posts related to each blog.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs and related posts
    var blogs1 = context.Blogs
                          .Include(b => b.Posts)
                          .ToList();

    // Load one blogs and its related posts
    var blog1 = context.Blogs
                        .Where(b => b.Name == "ADO.NET Blog")
                        .Include(b => b.Posts)
                        .FirstOrDefault();

    // Load all blogs and related posts  
    // using a string to specify the relationship
    var blogs2 = context.Blogs
                          .Include("Posts")
                          .ToList();

    // Load one blog and its related posts  
    // using a string to specify the relationship
    var blog2 = context.Blogs
                        .Where(b => b.Name == "ADO.NET Blog")
                        .Include("Posts")
                        .FirstOrDefault();
}
```  

<span data-ttu-id="fd9cb-109">System.Data.Entity 名前空間での拡張メソッドは、含めるようにしてその名前空間を使用しています。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-109">Note that Include is an extension method in the System.Data.Entity namespace so make sure you are using that namespace.</span></span>  

### <a name="eagerly-loading-multiple-levels"></a><span data-ttu-id="fd9cb-110">複数のレベルを集中的読み込み</span><span class="sxs-lookup"><span data-stu-id="fd9cb-110">Eagerly loading multiple levels</span></span>  

<span data-ttu-id="fd9cb-111">関連エンティティの複数のレベルを集中的に読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-111">It is also possible to eagerly load multiple levels of related entities.</span></span> <span data-ttu-id="fd9cb-112">次のクエリでは、コレクションと参照ナビゲーション プロパティの両方にこれを行う方法の例を紹介します。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-112">The queries below show examples of how to do this for both collection and reference navigation properties.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs, all related posts, and all related comments
    var blogs1 = context.Blogs
                       .Include(b => b.Posts.Select(p => p.Comments))
                       .ToList();

    // Load all users their related profiles, and related avatar
    var users1 = context.Users
                        .Include(u => u.Profile.Avatar)
                        .ToList();

    // Load all blogs, all related posts, and all related comments  
    // using a string to specify the relationships
    var blogs2 = context.Blogs
                       .Include("Posts.Comments")
                       .ToList();

    // Load all users their related profiles, and related avatar  
    // using a string to specify the relationships
    var users2 = context.Users
                        .Include("Profile.Avatar")
                        .ToList();
}
```  

<span data-ttu-id="fd9cb-113">現在、どの関連エンティティが読み込まれるフィルターすることはできませんに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-113">Note that it is not currently possible to filter which related entities are loaded.</span></span> <span data-ttu-id="fd9cb-114">Include は常にすべての関連エンティティを取り込みます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-114">Include will always bring in all related entities.</span></span>  

## <a name="lazy-loading"></a><span data-ttu-id="fd9cb-115">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="fd9cb-115">Lazy Loading</span></span>  

<span data-ttu-id="fd9cb-116">遅延読み込みは、というエンティティまたはエンティティのコレクションが自動的に初めて読み込まれる、データベースからエンティティ/エンティティを参照するプロパティにアクセスするプロセスです。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-116">Lazy loading is the process whereby an entity or collection of entities is automatically loaded from the database the first time that a property referring to the entity/entities is accessed.</span></span> <span data-ttu-id="fd9cb-117">POCO エンティティ型を使用する場合、遅延読み込みは、プロキシの派生型のインスタンスを作成して、読み込み用のフックを追加する仮想プロパティをオーバーライドし、によって実現されます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-117">When using POCO entity types, lazy loading is achieved by creating instances of derived proxy types and then overriding virtual properties to add the loading hook.</span></span> <span data-ttu-id="fd9cb-118">たとえば、以下に定義されたブログ エンティティ クラスを使用する場合、投稿のナビゲーション プロパティにアクセス最初時に、関連する投稿が読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-118">For example, when using the Blog entity class defined below, the related Posts will be loaded the first time the Posts navigation property is accessed:</span></span>  

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

### <a name="turn-lazy-loading-off-for-serialization"></a><span data-ttu-id="fd9cb-119">遅延のシリアル化の読み込みをオフにします。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-119">Turn lazy loading off for serialization</span></span>  

<span data-ttu-id="fd9cb-120">遅延読み込みとシリアル化しないし、同時に、注意が必要ない場合は、遅延読み込みが有効になっているからといって、データベース全体のクエリを終了できます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-120">Lazy loading and serialization don’t mix well, and if you aren’t careful you can end up querying for your entire database just because lazy loading is enabled.</span></span> <span data-ttu-id="fd9cb-121">ほとんどのシリアライザーは、型のインスタンス上の各プロパティにアクセスする機能します。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-121">Most serializers work by accessing each property on an instance of a type.</span></span> <span data-ttu-id="fd9cb-122">プロパティへのアクセスは、以上のエンティティ シリアル化するため、遅延読み込みをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-122">Property access triggers lazy loading, so more entities get serialized.</span></span> <span data-ttu-id="fd9cb-123">これらのエンティティのプロパティにアクセス、およびさらに多くのエンティティが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-123">On those entities properties are accessed, and even more entities are loaded.</span></span> <span data-ttu-id="fd9cb-124">エンティティをシリアル化する前に、無効の読み込み遅延を有効にすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-124">It’s a good practice to turn lazy loading off before you serialize an entity.</span></span> <span data-ttu-id="fd9cb-125">次のセクションでは、これを行う方法を示します。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-125">The following sections show how to do this.</span></span>  

### <a name="turning-off-lazy-loading-for-specific-navigation-properties"></a><span data-ttu-id="fd9cb-126">特定のナビゲーション プロパティの遅延読み込みをオフにします。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-126">Turning off lazy loading for specific navigation properties</span></span>  

<span data-ttu-id="fd9cb-127">投稿のプロパティを仮想でないことで、投稿コレクションの遅延読み込みをオフにすることができます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-127">Lazy loading of the Posts collection can be turned off by making the Posts property non-virtual:</span></span>  

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

<span data-ttu-id="fd9cb-128">読み込みの投稿のコレクションも実現できます一括読み込みを使用して (を参照してください*集中読み込み*上) または Load メソッド (を参照してください*の明示的な読み込み*以下)。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-128">Loading of the Posts collection can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>  

### <a name="turn-off-lazy-loading-for-all-entities"></a><span data-ttu-id="fd9cb-129">すべてのエンティティの遅延読み込みをオフにします。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-129">Turn off lazy loading for all entities</span></span>  

<span data-ttu-id="fd9cb-130">遅延読み込みは、そのコンテキスト内のすべてのエンティティの構成プロパティにフラグを設定して無効にできます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-130">Lazy loading can be turned off for all entities in the context by setting a flag on the Configuration property.</span></span> <span data-ttu-id="fd9cb-131">例えば:</span><span class="sxs-lookup"><span data-stu-id="fd9cb-131">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.LazyLoadingEnabled = false;
    }
}
```  

<span data-ttu-id="fd9cb-132">関連エンティティの読み込みは一括読み込み実現することができます (を参照してください*集中読み込み*上) または Load メソッド (を参照してください*の明示的な読み込み*以下)。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-132">Loading of related entities can still be achieved using eager loading (see *Eagerly Loading* above) or the Load method (see *Explicitly Loading* below).</span></span>  

## <a name="explicitly-loading"></a><span data-ttu-id="fd9cb-133">明示的な読み込み</span><span class="sxs-lookup"><span data-stu-id="fd9cb-133">Explicitly Loading</span></span>  

<span data-ttu-id="fd9cb-134">遅延読み込みを無効にしても、関連エンティティを遅延読み込みすることも可能ですが、明示的な呼び出しを実行してください。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-134">Even with lazy loading disabled it is still possible to lazily load related entities, but it must be done with an explicit call.</span></span> <span data-ttu-id="fd9cb-135">これを行うには、関連エンティティのエントリの Load メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-135">To do so you use the Load method on the related entity’s entry.</span></span> <span data-ttu-id="fd9cb-136">例えば:</span><span class="sxs-lookup"><span data-stu-id="fd9cb-136">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var post = context.Posts.Find(2);

    // Load the blog related to a given post
    context.Entry(post).Reference(p => p.Blog).Load();

    // Load the blog related to a given post using a string  
    context.Entry(post).Reference("Blog").Load();

    var blog = context.Blogs.Find(1);

    // Load the posts related to a given blog
    context.Entry(blog).Collection(p => p.Posts).Load();

    // Load the posts related to a given blog  
    // using a string to specify the relationship
    context.Entry(blog).Collection("Posts").Load();
}
```  

<span data-ttu-id="fd9cb-137">エンティティに別の単一のエンティティにナビゲーション プロパティがあるときに参照メソッドを使用する必要がありますに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-137">Note that the Reference method should be used when an entity has a navigation property to another single entity.</span></span> <span data-ttu-id="fd9cb-138">その一方で、エンティティの他のエンティティのコレクションにナビゲーション プロパティがある場合は、収集方法を使用してください。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-138">On the other hand, the Collection method should be used when an entity has a navigation property to a collection of other entities.</span></span>  

### <a name="applying-filters-when-explicitly-loading-related-entities"></a><span data-ttu-id="fd9cb-139">関連エンティティを明示的に読み込むときに、フィルターを適用します。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-139">Applying filters when explicitly loading related entities</span></span>  

<span data-ttu-id="fd9cb-140">クエリ メソッドは、関連エンティティの読み込み時に Entity Framework を使用する基になるクエリへのアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-140">The Query method provides access to the underlying query that Entity Framework will use when loading related entities.</span></span> <span data-ttu-id="fd9cb-141">LINQ を使用して、ToList や負荷などの LINQ 拡張メソッドの呼び出しを使用して実行する前に、クエリにフィルターを適用することができます。クエリ メソッドは、ナビゲーション プロパティの参照とコレクションの両方で使用できますがコレクションに最も役立つコレクションの一部のみに使用できます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-141">You can then use LINQ to apply filters to the query before executing it with a call to a LINQ extension method such as ToList, Load, etc. The Query method can be used with both reference and collection navigation properties but is most useful for collections where it can be used to load only part of the collection.</span></span> <span data-ttu-id="fd9cb-142">例えば:</span><span class="sxs-lookup"><span data-stu-id="fd9cb-142">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework")
        .Load();

    // Load the posts with the 'entity-framework' tag related to a given blog  
    // using a string to specify the relationship  
    context.Entry(blog)
        .Collection("Posts")
        .Query()
        .Where(p => p.Tags.Contains("entity-framework")
        .Load();
}
```  

<span data-ttu-id="fd9cb-143">クエリ メソッドを使用する場合は、ナビゲーション プロパティの遅延読み込みをオフに、通常はお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-143">When using the Query method it is usually best to turn off lazy loading for the navigation property.</span></span> <span data-ttu-id="fd9cb-144">これは、ため、それ以外の場合コレクション全体がによって読み込まれる自動的に遅延読み込みメカニズム前に、またはフィルター選択されたクエリが実行された後です。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-144">This is because otherwise the entire collection may get loaded automatically by the lazy loading mechanism either before or after the filtered query has been executed.</span></span>  

<span data-ttu-id="fd9cb-145">リレーションシップは、ラムダ式ではなく文字列として指定できます、返された対象の IQueryable ではありません汎用文字列を使用すると、そのため、有益なもののように、前に、キャスト メソッドは通常必要に注意してください。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-145">Note that while the relationship can be specified as a string instead of a lambda expression, the returned IQueryable is not generic when a string is used and so the Cast method is usually needed before anything useful can be done with it.</span></span>  

## <a name="using-query-to-count-related-entities-without-loading-them"></a><span data-ttu-id="fd9cb-146">それらを読み込むことがなく、関連エンティティをカウントするクエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-146">Using Query to count related entities without loading them</span></span>  

<span data-ttu-id="fd9cb-147">場合によっては実際にこれらすべてのエンティティの読み込みのコストをかけずに、データベース内の別のエンティティに関連エンティティの数を把握するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-147">Sometimes it is useful to know how many entities are related to another entity in the database without actually incurring the cost of loading all those entities.</span></span> <span data-ttu-id="fd9cb-148">これを行うには、LINQ カウント メソッドを使用して、クエリ メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="fd9cb-148">The Query method with the LINQ Count method can be used to do this.</span></span> <span data-ttu-id="fd9cb-149">例えば:</span><span class="sxs-lookup"><span data-stu-id="fd9cb-149">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Count how many posts the blog has  
    var postCount = context.Entry(blog)
                          .Collection(b => b.Posts)
                          .Query()
                          .Count();
}
```  
