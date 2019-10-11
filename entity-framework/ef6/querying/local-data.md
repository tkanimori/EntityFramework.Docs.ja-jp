---
title: ローカルデータ-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2eda668b-1e5d-487d-9a8c-0e3beef03fcb
ms.openlocfilehash: efd646348d8a18bbeed2d0a0e708d4d36eb26eac
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182427"
---
# <a name="local-data"></a><span data-ttu-id="666f3-102">ローカル データ</span><span class="sxs-lookup"><span data-stu-id="666f3-102">Local Data</span></span>
<span data-ttu-id="666f3-103">DbSet に対して LINQ クエリを直接実行すると、常にクエリがデータベースに送信されますが、現在、DbSet. Local プロパティを使用してメモリ内にあるデータにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="666f3-103">Running a LINQ query directly against a DbSet will always send a query to the database, but you can access the data that is currently in-memory using the DbSet.Local property.</span></span> <span data-ttu-id="666f3-104">また、DbContext. Entry メソッドと DbContext. ChangeTracker メソッドを使用して、エンティティに関する追加情報 EF が追跡している情報にアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="666f3-104">You can also access the extra information EF is tracking about your entities using the DbContext.Entry and DbContext.ChangeTracker.Entries methods.</span></span> <span data-ttu-id="666f3-105">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="666f3-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="using-local-to-look-at-local-data"></a><span data-ttu-id="666f3-106">ローカルデータの参照にローカルを使用する</span><span class="sxs-lookup"><span data-stu-id="666f3-106">Using Local to look at local data</span></span>  

<span data-ttu-id="666f3-107">DbSet のローカルプロパティは、現在コンテキストによって追跡されていて、削除済みとしてマークされていないセットのエンティティへの単純なアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="666f3-107">The Local property of DbSet provides simple access to the entities of the set that are currently being tracked by the context and have not been marked as Deleted.</span></span> <span data-ttu-id="666f3-108">ローカルプロパティにアクセスしても、クエリがデータベースに送信されることはありません。</span><span class="sxs-lookup"><span data-stu-id="666f3-108">Accessing the Local property never causes a query to be sent to the database.</span></span> <span data-ttu-id="666f3-109">これは、通常、クエリが既に実行された後に使用されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="666f3-109">This means that it is usually used after a query has already been performed.</span></span> <span data-ttu-id="666f3-110">読み込み拡張メソッドを使用してクエリを実行し、コンテキストが結果を追跡できるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="666f3-110">The Load extension method can be used to execute a query so that the context tracks the results.</span></span> <span data-ttu-id="666f3-111">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="666f3-111">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load all blogs from the database into the context
    context.Blogs.Load();

    // Add a new blog to the context
    context.Blogs.Add(new Blog { Name = "My New Blog" });

    // Mark one of the existing blogs as Deleted
    context.Blogs.Remove(context.Blogs.Find(1));

    // Loop over the blogs in the context.
    Console.WriteLine("In Local: ");
    foreach (var blog in context.Blogs.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }

    // Perform a query against the database.
    Console.WriteLine("\nIn DbSet query: ");
    foreach (var blog in context.Blogs)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            blog.BlogId,  
            blog.Name,
            context.Entry(blog).State);
    }
}
```  

<span data-ttu-id="666f3-112">ブログ Id が1で、ブログ id が2である ' ADO.NET Blog ' というデータベースに2つのブログがある場合、次の出力が予想される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="666f3-112">If we had two blogs in the database - 'ADO.NET Blog' with a BlogId of 1 and 'The Visual Studio Blog' with a BlogId of 2 - we could expect the following output:</span></span>  

```console
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

<span data-ttu-id="666f3-113">これは、3つの点を示しています。</span><span class="sxs-lookup"><span data-stu-id="666f3-113">This illustrates three points:</span></span>  

- <span data-ttu-id="666f3-114">新しいブログ「My New Blog」は、データベースにまだ保存されていない場合でも、ローカルコレクションに含まれています。</span><span class="sxs-lookup"><span data-stu-id="666f3-114">The new blog 'My New Blog' is included in the Local collection even though it has not yet been saved to the database.</span></span> <span data-ttu-id="666f3-115">このブログでは、エンティティの実際のキーがデータベースによって生成されていないため、主キーが0になっています。</span><span class="sxs-lookup"><span data-stu-id="666f3-115">This blog has a primary key of zero because the database has not yet generated a real key for the entity.</span></span>  
- <span data-ttu-id="666f3-116">コンテキストによって追跡されているにもかかわらず、' ADO.NET Blog ' はローカルコレクションに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="666f3-116">The 'ADO.NET Blog' is not included in the local collection even though it is still being tracked by the context.</span></span> <span data-ttu-id="666f3-117">これは、DbSet から削除されたため、削除済みとしてマークされているためです。</span><span class="sxs-lookup"><span data-stu-id="666f3-117">This is because we removed it from the DbSet thereby marking it as deleted.</span></span>  
- <span data-ttu-id="666f3-118">DbSet を使用してクエリを実行すると、削除のマークが付いているブログ (ADO.NET Blog) が結果に含まれ、データベースに保存されていない新しいブログ (My New Blog) は結果に含まれません。</span><span class="sxs-lookup"><span data-stu-id="666f3-118">When DbSet is used to perform a query the blog marked for deletion (ADO.NET Blog) is included in the results and the new blog (My New Blog) that has not yet been saved to the database is not included in the results.</span></span> <span data-ttu-id="666f3-119">これは、DbSet がデータベースに対してクエリを実行し、返される結果には常にデータベースの内容が反映されるためです。</span><span class="sxs-lookup"><span data-stu-id="666f3-119">This is because DbSet is performing a query against the database and the results returned always reflect what is in the database.</span></span>  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a><span data-ttu-id="666f3-120">ローカルを使用してコンテキストのエンティティを追加および削除する</span><span class="sxs-lookup"><span data-stu-id="666f3-120">Using Local to add and remove entities from the context</span></span>  

<span data-ttu-id="666f3-121">DbSet の Local プロパティは、コンテキストのコンテンツと同期したままになるように、イベントがフックされた[system.collections.objectmodel.observablecollection](https://msdn.microsoft.com/library/ms668604.aspx)を返します。</span><span class="sxs-lookup"><span data-stu-id="666f3-121">The Local property on DbSet returns an [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) with events hooked up such that it stays in sync with the contents of the context.</span></span> <span data-ttu-id="666f3-122">これは、ローカルコレクションまたは DbSet からエンティティを追加または削除できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="666f3-122">This means that entities can be added or removed from either the Local collection or the DbSet.</span></span> <span data-ttu-id="666f3-123">また、新しいエンティティをコンテキストに取り込むクエリによって、そのエンティティでローカルコレクションが更新されることも意味します。</span><span class="sxs-lookup"><span data-stu-id="666f3-123">It also means that queries that bring new entities into the context will result in the Local collection being updated with those entities.</span></span> <span data-ttu-id="666f3-124">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="666f3-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load some posts from the database into the context
    context.Posts.Where(p => p.Tags.Contains("entity-framework")).Load();  

    // Get the local collection and make some changes to it
    var localPosts = context.Posts.Local;
    localPosts.Add(new Post { Name = "What's New in EF" });
    localPosts.Remove(context.Posts.Find(1));  

    // Loop over the posts in the context.
    Console.WriteLine("In Local after entity-framework query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }

    var post1 = context.Posts.Find(1);
    Console.WriteLine(
        "State of post 1: {0} is {1}",
        post1.Name,  
        context.Entry(post1).State);  

    // Query some more posts from the database
    context.Posts.Where(p => p.Tags.Contains("asp.net").Load();  

    // Loop over the posts in the context again.
    Console.WriteLine("\nIn Local after asp.net query: ");
    foreach (var post in context.Posts.Local)
    {
        Console.WriteLine(
            "Found {0}: {1} with state {2}",
            post.Id,  
            post.Title,
            context.Entry(post).State);
    }
}
```  

<span data-ttu-id="666f3-125">' Entity-framework ' と ' asp.net ' にタグ付けされたいくつかの投稿があると仮定した場合、出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="666f3-125">Assuming we had a few posts tagged with 'entity-framework' and 'asp.net' the output may look something like this:</span></span>  

```console
In Local after entity-framework query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
State of post 1: EF Beginners Guide is Deleted

In Local after asp.net query:
Found 3: EF Designer Basics with state Unchanged
Found 5: EF Code First Basics with state Unchanged
Found 0: What's New in EF with state Added
Found 4: ASP.NET Beginners Guide with state Unchanged
```  

<span data-ttu-id="666f3-126">これは、3つの点を示しています。</span><span class="sxs-lookup"><span data-stu-id="666f3-126">This illustrates three points:</span></span>  

- <span data-ttu-id="666f3-127">ローカルコレクションに追加された新しい post ' EF の新機能 ' は、追加された状態のコンテキストによって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="666f3-127">The new post 'What's New in EF' that was added to the Local collection becomes tracked by the context in the Added state.</span></span> <span data-ttu-id="666f3-128">そのため、SaveChanges が呼び出されると、データベースに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="666f3-128">It will therefore be inserted into the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="666f3-129">ローカルコレクションから削除された投稿 (EF 初心者ガイド) は、コンテキストで削除済みとしてマークされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="666f3-129">The post that was removed from the Local collection (EF Beginners Guide) is now marked as deleted in the context.</span></span> <span data-ttu-id="666f3-130">そのため、SaveChanges が呼び出されると、データベースから削除されます。</span><span class="sxs-lookup"><span data-stu-id="666f3-130">It will therefore be deleted from the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="666f3-131">2番目のクエリを使用してコンテキストに読み込まれた追加の post (ASP.NET 初心者ガイド) は、自動的にローカルコレクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="666f3-131">The additional post (ASP.NET Beginners Guide) loaded into the context with the second query is automatically added to the Local collection.</span></span>  

<span data-ttu-id="666f3-132">ローカルに関する最後の注意点としては、System.collections.objectmodel.observablecollection のパフォーマンスは、大量のエンティティには適していません。</span><span class="sxs-lookup"><span data-stu-id="666f3-132">One final thing to note about Local is that because it is an ObservableCollection performance is not great for large numbers of entities.</span></span> <span data-ttu-id="666f3-133">そのため、コンテキスト内で数千のエンティティを扱う場合は、ローカルを使用することをお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="666f3-133">Therefore if you are dealing with thousands of entities in your context it may not be advisable to use Local.</span></span>  

## <a name="using-local-for-wpf-data-binding"></a><span data-ttu-id="666f3-134">WPF データバインディングにローカルを使用する</span><span class="sxs-lookup"><span data-stu-id="666f3-134">Using Local for WPF data binding</span></span>  

<span data-ttu-id="666f3-135">DbSet のローカルプロパティは、System.collections.objectmodel.observablecollection のインスタンスであるため、WPF アプリケーションのデータバインディングに直接使用できます。</span><span class="sxs-lookup"><span data-stu-id="666f3-135">The Local property on DbSet can be used directly for data binding in a WPF application because it is an instance of ObservableCollection.</span></span> <span data-ttu-id="666f3-136">前のセクションで説明したように、これはコンテキストのコンテンツと自動的に同期されることを意味し、コンテキストの内容は自動的に同期されます。</span><span class="sxs-lookup"><span data-stu-id="666f3-136">As described in the previous sections this means that it will automatically stay in sync with the contents of the context and the contents of the context will automatically stay in sync with it.</span></span> <span data-ttu-id="666f3-137">ローカルコレクションにバインドするデータを事前に設定する必要があることに注意してください。これは、Local がデータベースクエリを発生させないためです。</span><span class="sxs-lookup"><span data-stu-id="666f3-137">Note that you do need to pre-populate the Local collection with data for there to be anything to bind to since Local never causes a database query.</span></span>  

<span data-ttu-id="666f3-138">これは、完全な WPF データバインディングサンプルでは適切な場所ではありませんが、主な要素は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="666f3-138">This is not an appropriate place for a full WPF data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="666f3-139">バインディングソースの設定</span><span class="sxs-lookup"><span data-stu-id="666f3-139">Setup a binding source</span></span>  
- <span data-ttu-id="666f3-140">Set のローカルプロパティにバインドする</span><span class="sxs-lookup"><span data-stu-id="666f3-140">Bind it to the Local property of your set</span></span>  
- <span data-ttu-id="666f3-141">データベースにクエリを使用してローカルにデータを設定します。</span><span class="sxs-lookup"><span data-stu-id="666f3-141">Populate Local using a query to the database.</span></span>  

## <a name="wpf-binding-to-navigation-properties"></a><span data-ttu-id="666f3-142">WPF のナビゲーションプロパティへのバインド</span><span class="sxs-lookup"><span data-stu-id="666f3-142">WPF binding to navigation properties</span></span>  

<span data-ttu-id="666f3-143">マスター/詳細データのバインドを行う場合は、いずれかのエンティティのナビゲーションプロパティに詳細ビューをバインドすることができます。</span><span class="sxs-lookup"><span data-stu-id="666f3-143">If you are doing master/detail data binding you may want to bind the detail view to a navigation property of one of your entities.</span></span> <span data-ttu-id="666f3-144">この作業を行う簡単な方法は、ナビゲーションプロパティに System.collections.objectmodel.observablecollection を使用することです。</span><span class="sxs-lookup"><span data-stu-id="666f3-144">An easy way to make this work is to use an ObservableCollection for the navigation property.</span></span> <span data-ttu-id="666f3-145">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="666f3-145">For example:</span></span>  

``` csharp
public class Blog
{
    private readonly ObservableCollection<Post> _posts =
        new ObservableCollection<Post>();

    public int BlogId { get; set; }
    public string Name { get; set; }

    public virtual ObservableCollection<Post> Posts
    {
        get { return _posts; }
    }
}
```  

## <a name="using-local-to-clean-up-entities-in-savechanges"></a><span data-ttu-id="666f3-146">ローカルを使用して SaveChanges でエンティティをクリーンアップする</span><span class="sxs-lookup"><span data-stu-id="666f3-146">Using Local to clean up entities in SaveChanges</span></span>  

<span data-ttu-id="666f3-147">ほとんどの場合、ナビゲーションプロパティから削除されたエンティティは、コンテキストで削除済みとして自動的にマークされません。</span><span class="sxs-lookup"><span data-stu-id="666f3-147">In most cases entities removed from a navigation property will not be automatically marked as deleted in the context.</span></span> <span data-ttu-id="666f3-148">たとえば、Post オブジェクトを Blog コレクションから削除した場合、SaveChanges が呼び出されても、その投稿は自動的には削除されません。</span><span class="sxs-lookup"><span data-stu-id="666f3-148">For example, if you remove a Post object from the Blog.Posts collection then that post will not be automatically deleted when SaveChanges is called.</span></span> <span data-ttu-id="666f3-149">削除する必要がある場合は、SaveChanges を呼び出す前、またはオーバーライドされた SaveChanges の一部として、これらのぶら下がりエンティティを検索し、削除済みとしてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="666f3-149">If you need it to be deleted then you may need to find these dangling entities and mark them as deleted before calling SaveChanges or as part of an overridden SaveChanges.</span></span> <span data-ttu-id="666f3-150">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="666f3-150">For example:</span></span>  

``` csharp
public override int SaveChanges()
{
    foreach (var post in this.Posts.Local.ToList())
    {
        if (post.Blog == null)
        {
            this.Posts.Remove(post);
        }
    }

    return base.SaveChanges();
}
```  

<span data-ttu-id="666f3-151">上記のコードでは、ローカルコレクションを使用してすべての投稿を検索し、ブログ参照が削除済みとして含まれていないをマークします。</span><span class="sxs-lookup"><span data-stu-id="666f3-151">The code above uses the Local collection to find all posts and marks any that do not have a blog reference as deleted.</span></span> <span data-ttu-id="666f3-152">それ以外の場合は、列挙中に削除呼び出しによってコレクションが変更されるため、ToList 呼び出しが必要です。</span><span class="sxs-lookup"><span data-stu-id="666f3-152">The ToList call is required because otherwise the collection will be modified by the Remove call while it is being enumerated.</span></span> <span data-ttu-id="666f3-153">その他のほとんどの状況では、ToList first を使用せずに、ローカルプロパティに対して直接クエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="666f3-153">In most other situations you can query directly against the Local property without using ToList first.</span></span>  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a><span data-ttu-id="666f3-154">Windows フォームデータバインディングにローカルと ToBindingList を使用する</span><span class="sxs-lookup"><span data-stu-id="666f3-154">Using Local and ToBindingList for Windows Forms data binding</span></span>  

<span data-ttu-id="666f3-155">Windows フォームは、System.collections.objectmodel.observablecollection を直接使用した忠実度の高いデータバインディングをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="666f3-155">Windows Forms does not support full fidelity data binding using ObservableCollection directly.</span></span> <span data-ttu-id="666f3-156">ただし、前のセクションで説明したすべての利点を取得するには、データバインディングに対して DbSet ローカルプロパティを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="666f3-156">However, you can still use the DbSet Local property for data binding to get all the benefits described in the previous sections.</span></span> <span data-ttu-id="666f3-157">これは、ローカルの System.collections.objectmodel.observablecollection によってサポートされる[IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx)実装を作成する ToBindingList 拡張メソッドを使用して実現されます。</span><span class="sxs-lookup"><span data-stu-id="666f3-157">This is achieved through the ToBindingList extension method which creates an [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) implementation backed by the Local ObservableCollection.</span></span>  

<span data-ttu-id="666f3-158">これは、完全 Windows フォームデータバインディングサンプルには適していませんが、主な要素は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="666f3-158">This is not an appropriate place for a full Windows Forms data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="666f3-159">オブジェクトのバインドソースの設定</span><span class="sxs-lookup"><span data-stu-id="666f3-159">Setup an object binding source</span></span>  
- <span data-ttu-id="666f3-160">ToBindingList () を使用して、セットのローカルプロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="666f3-160">Bind it to the Local property of your set using Local.ToBindingList()</span></span>  
- <span data-ttu-id="666f3-161">データベースにクエリを使用してローカルにデータを設定する</span><span class="sxs-lookup"><span data-stu-id="666f3-161">Populate Local using a query to the database</span></span>  

## <a name="getting-detailed-information-about-tracked-entities"></a><span data-ttu-id="666f3-162">追跡対象エンティティに関する詳細情報の取得</span><span class="sxs-lookup"><span data-stu-id="666f3-162">Getting detailed information about tracked entities</span></span>  

<span data-ttu-id="666f3-163">このシリーズの多くの例では、Entry メソッドを使用して、エンティティの DbEntityEntry インスタンスを返しています。</span><span class="sxs-lookup"><span data-stu-id="666f3-163">Many of the examples in this series use the Entry method to return a DbEntityEntry instance for an entity.</span></span> <span data-ttu-id="666f3-164">このエントリオブジェクトは、現在の状態などのエンティティに関する情報を収集するための開始点として機能します。また、関連エンティティの明示的な読み込みなど、エンティティに対する操作の実行にも使用されます。</span><span class="sxs-lookup"><span data-stu-id="666f3-164">This entry object then acts as the starting point for gathering information about the entity such as its current state, as well as for performing operations on the entity such as explicitly loading a related entity.</span></span>  

<span data-ttu-id="666f3-165">Entries メソッドは、コンテキストによって追跡されている多数またはすべてのエンティティに対して DbEntityEntry オブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="666f3-165">The Entries methods return DbEntityEntry objects for many or all entities being tracked by the context.</span></span> <span data-ttu-id="666f3-166">これにより、情報を収集したり、1つのエントリだけではなく、多くのエンティティに対して操作を実行したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="666f3-166">This allows you to gather information or perform operations on many entities rather than just a single entry.</span></span> <span data-ttu-id="666f3-167">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="666f3-167">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load some entities into the context
    context.Blogs.Load();
    context.Authors.Load();
    context.Readers.Load();

    // Make some changes
    context.Blogs.Find(1).Title = "The New ADO.NET Blog";
    context.Blogs.Remove(context.Blogs.Find(2));
    context.Authors.Add(new Author { Name = "Jane Doe" });
    context.Readers.Find(1).Username = "johndoe1987";

    // Look at the state of all entities in the context
    Console.WriteLine("All tracked entities: ");
    foreach (var entry in context.ChangeTracker.Entries())
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Find modified entities of any type
    Console.WriteLine("\nAll modified entities: ");
    foreach (var entry in context.ChangeTracker.Entries()
                              .Where(e => e.State == EntityState.Modified))
    {
        Console.WriteLine(
            "Found entity of type {0} with state {1}",
            ObjectContext.GetObjectType(entry.Entity.GetType()).Name,
            entry.State);
    }

    // Get some information about just the tracked blogs
    Console.WriteLine("\nTracked blogs: ");
    foreach (var entry in context.ChangeTracker.Entries<Blog>())
    {
        Console.WriteLine(
            "Found Blog {0}: {1} with original Name {2}",
            entry.Entity.BlogId,  
            entry.Entity.Name,
            entry.Property(p => p.Name).OriginalValue);
    }

    // Find all people (author or reader)
    Console.WriteLine("\nPeople: ");
    foreach (var entry in context.ChangeTracker.Entries<IPerson>())
    {
        Console.WriteLine("Found Person {0}", entry.Entity.Name);
    }
}
```  

<span data-ttu-id="666f3-168">例に著者とリーダークラスが導入されていることがわかります。これらのクラスはどちらも IPerson インターフェイスを実装しています。</span><span class="sxs-lookup"><span data-stu-id="666f3-168">You'll notice we are introducing a Author and Reader class into the example - both of these classes implement the IPerson interface.</span></span>  

``` csharp
public class Author : IPerson
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public string Biography { get; set; }
}

public class Reader : IPerson
{
    public int ReaderId { get; set; }
    public string Name { get; set; }
    public string Username { get; set; }
}

public interface IPerson
{
    string Name { get; }
}
```  

<span data-ttu-id="666f3-169">データベースに次のデータが含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="666f3-169">Let's assume we have the following data in the database:</span></span>

<span data-ttu-id="666f3-170">Blog Id = 1、Name = ' ADO.NET Blog ' のブログ</span><span class="sxs-lookup"><span data-stu-id="666f3-170">Blog with BlogId = 1 and Name = 'ADO.NET Blog'</span></span>  
<span data-ttu-id="666f3-171">ブログ Id = 2、名前 = ' Visual Studio ブログ '</span><span class="sxs-lookup"><span data-stu-id="666f3-171">Blog with BlogId = 2 and Name = 'The Visual Studio Blog'</span></span>  
<span data-ttu-id="666f3-172">ブログ Id = 3、名前 = ' .NET Framework ブログ '</span><span class="sxs-lookup"><span data-stu-id="666f3-172">Blog with BlogId = 3 and Name = '.NET Framework Blog'</span></span>  
<span data-ttu-id="666f3-173">AuthorId = 1、Name = ' Joe ブログ Gs ' を使用した作成者</span><span class="sxs-lookup"><span data-stu-id="666f3-173">Author with AuthorId = 1 and Name = 'Joe Bloggs'</span></span>  
<span data-ttu-id="666f3-174">ReaderId = 1、Name = ' John Doe ' のリーダー</span><span class="sxs-lookup"><span data-stu-id="666f3-174">Reader with ReaderId = 1 and Name = 'John Doe'</span></span>  

<span data-ttu-id="666f3-175">コードを実行した場合の出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="666f3-175">The output from running the code would be:</span></span>  

```console
All tracked entities:
Found entity of type Blog with state Modified
Found entity of type Blog with state Deleted
Found entity of type Blog with state Unchanged
Found entity of type Author with state Unchanged
Found entity of type Author with state Added
Found entity of type Reader with state Modified

All modified entities:
Found entity of type Blog with state Modified
Found entity of type Reader with state Modified

Tracked blogs:
Found Blog 1: The New ADO.NET Blog with original Name ADO.NET Blog
Found Blog 2: The Visual Studio Blog with original Name The Visual Studio Blog
Found Blog 3: .NET Framework Blog with original Name .NET Framework Blog

People:
Found Person John Doe
Found Person Joe Bloggs
Found Person Jane Doe
```  

<span data-ttu-id="666f3-176">次の例は、いくつかの点を示しています。</span><span class="sxs-lookup"><span data-stu-id="666f3-176">These examples illustrate several points:</span></span>  

- <span data-ttu-id="666f3-177">Entries メソッドは、Deleted を含むすべての状態のエンティティのエントリを返します。</span><span class="sxs-lookup"><span data-stu-id="666f3-177">The Entries methods return entries for entities in all states, including Deleted.</span></span> <span data-ttu-id="666f3-178">これをローカルと比較して、削除されたエンティティを除外します。</span><span class="sxs-lookup"><span data-stu-id="666f3-178">Compare this to Local which excludes Deleted entities.</span></span>  
- <span data-ttu-id="666f3-179">非ジェネリックエントリメソッドを使用すると、すべてのエンティティ型のエントリが返されます。</span><span class="sxs-lookup"><span data-stu-id="666f3-179">Entries for all entity types are returned when the non-generic Entries method is used.</span></span> <span data-ttu-id="666f3-180">汎用エントリメソッドが使用されている場合、エントリは、ジェネリック型のインスタンスであるエンティティに対してのみ返されます。</span><span class="sxs-lookup"><span data-stu-id="666f3-180">When the generic entries method is used entries are only returned for entities that are instances of the generic type.</span></span> <span data-ttu-id="666f3-181">これは、すべてのブログのエントリを取得するために上記で使用されていました。</span><span class="sxs-lookup"><span data-stu-id="666f3-181">This was used above to get entries for all blogs.</span></span> <span data-ttu-id="666f3-182">また、IPerson を実装するすべてのエンティティのエントリを取得するためにも使用されていました。</span><span class="sxs-lookup"><span data-stu-id="666f3-182">It was also used to get entries for all entities that implement IPerson.</span></span> <span data-ttu-id="666f3-183">これは、ジェネリック型が実際のエンティティ型である必要がないことを示しています。</span><span class="sxs-lookup"><span data-stu-id="666f3-183">This demonstrates that the generic type does not have to be an actual entity type.</span></span>  
- <span data-ttu-id="666f3-184">LINQ to Objects は、返される結果をフィルター処理するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="666f3-184">LINQ to Objects can be used to filter the results returned.</span></span> <span data-ttu-id="666f3-185">これは、変更されている限り、任意の型のエンティティを検索するために上記で使用されていました。</span><span class="sxs-lookup"><span data-stu-id="666f3-185">This was used above to find entities of any type as long as they are modified.</span></span>  

<span data-ttu-id="666f3-186">DbEntityEntry インスタンスには、常に null 以外のエンティティが含まれていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="666f3-186">Note that DbEntityEntry instances always contain a non-null Entity.</span></span> <span data-ttu-id="666f3-187">リレーションシップエントリとスタブエントリは DbEntityEntry インスタンスとしては表されないため、これらをフィルター処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="666f3-187">Relationship entries and stub entries are not represented as DbEntityEntry instances so there is no need to filter for these.</span></span>
