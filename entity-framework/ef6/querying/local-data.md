---
title: ローカル データ - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2eda668b-1e5d-487d-9a8c-0e3beef03fcb
ms.openlocfilehash: 400b9e1337edac1b9fa4f0ec9e1384ca58aa2fbc
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490455"
---
# <a name="local-data"></a><span data-ttu-id="0e9e2-102">ローカル データ</span><span class="sxs-lookup"><span data-stu-id="0e9e2-102">Local Data</span></span>
<span data-ttu-id="0e9e2-103">DbSet に対して直接、LINQ クエリを実行しているは常にクエリを送信、データベースが現在のインメモリ DbSet.Local プロパティを使用しているデータにアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-103">Running a LINQ query directly against a DbSet will always send a query to the database, but you can access the data that is currently in-memory using the DbSet.Local property.</span></span> <span data-ttu-id="0e9e2-104">EF を追跡する追加情報は、DbContext.Entry および DbContext.ChangeTracker.Entries メソッドを使用してエンティティについてアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-104">You can also access the extra information EF is tracking about your entities using the DbContext.Entry and DbContext.ChangeTracker.Entries methods.</span></span> <span data-ttu-id="0e9e2-105">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="using-local-to-look-at-local-data"></a><span data-ttu-id="0e9e2-106">ローカルを使用してローカル データを確認するには</span><span class="sxs-lookup"><span data-stu-id="0e9e2-106">Using Local to look at local data</span></span>  

<span data-ttu-id="0e9e2-107">DbSet のローカルのプロパティは、現在のコンテキストによって追跡されているし、削除済みとしてマークされていない、一連のエンティティにシンプルなアクセスを提供します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-107">The Local property of DbSet provides simple access to the entities of the set that are currently being tracked by the context and have not been marked as Deleted.</span></span> <span data-ttu-id="0e9e2-108">データベースに送信されるクエリが、ローカル プロパティにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-108">Accessing the Local property never causes a query to be sent to the database.</span></span> <span data-ttu-id="0e9e2-109">クエリの実行後、通常、使用はことを意味これです。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-109">This means that it is usually used after a query has already been performed.</span></span> <span data-ttu-id="0e9e2-110">クエリの実行コンテキストは、結果を追跡するために、Load 拡張メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-110">The Load extension method can be used to execute a query so that the context tracks the results.</span></span> <span data-ttu-id="0e9e2-111">例えば:</span><span class="sxs-lookup"><span data-stu-id="0e9e2-111">For example:</span></span>  

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

<span data-ttu-id="0e9e2-112">2 つのブログ - 'ADO.NET Blog' の 1 の BlogId で' The Visual Studio Blog' の 2 の BlogId でのデータベースがある場合は、次の出力を予定でした。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-112">If we had two blogs in the database - 'ADO.NET Blog' with a BlogId of 1 and 'The Visual Studio Blog' with a BlogId of 2 - we could expect the following output:</span></span>  

```  
In Local:
Found 0: My New Blog with state Added
Found 2: The Visual Studio Blog with state Unchanged

In DbSet query:
Found 1: ADO.NET Blog with state Deleted
Found 2: The Visual Studio Blog with state Unchanged
```  

<span data-ttu-id="0e9e2-113">これは、3 つの点を示しています。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-113">This illustrates three points:</span></span>  

- <span data-ttu-id="0e9e2-114">新しいブログ 'マイ ブログの新しい' は、データベースに保存されていない場合でも、ローカル コレクションに含まれます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-114">The new blog 'My New Blog' is included in the Local collection even though it has not yet been saved to the database.</span></span> <span data-ttu-id="0e9e2-115">このブログは、データベースの表示、エンティティの実際のキーがまだ生成するために、ゼロの主キーを持ちます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-115">This blog has a primary key of zero because the database has not yet generated a real key for the entity.</span></span>  
- <span data-ttu-id="0e9e2-116">コンテキストがまだ追跡されている場合でも、ADO.NET ブログはローカル コレクションに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-116">The 'ADO.NET Blog' is not included in the local collection even though it is still being tracked by the context.</span></span> <span data-ttu-id="0e9e2-117">これは、ため、削除済みとしてマークする DbSet から削除したためです。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-117">This is because we removed it from the DbSet thereby marking it as deleted.</span></span>  
- <span data-ttu-id="0e9e2-118">DbSet を使用してクエリを実行する (ADO.NET のブログ) を削除対象としてマークするブログが結果に含めるし、データベースに保存されていない新しいブログ (マイ新しいブログ) は、結果に含まれていません。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-118">When DbSet is used to perform a query the blog marked for deletion (ADO.NET Blog) is included in the results and the new blog (My New Blog) that has not yet been saved to the database is not included in the results.</span></span> <span data-ttu-id="0e9e2-119">DbSet がデータベースに対してクエリを実行して、常に返される結果が、データベースの新機能を反映するためです。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-119">This is because DbSet is performing a query against the database and the results returned always reflect what is in the database.</span></span>  

## <a name="using-local-to-add-and-remove-entities-from-the-context"></a><span data-ttu-id="0e9e2-120">ローカルを使用して追加し、コンテキストからエンティティを削除するには</span><span class="sxs-lookup"><span data-stu-id="0e9e2-120">Using Local to add and remove entities from the context</span></span>  

<span data-ttu-id="0e9e2-121">DbSet にローカル プロパティを返します、 [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx)コンテキストの内容との同期に保持されるようにフックするイベントです。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-121">The Local property on DbSet returns an [ObservableCollection](https://msdn.microsoft.com/library/ms668604.aspx) with events hooked up such that it stays in sync with the contents of the context.</span></span> <span data-ttu-id="0e9e2-122">これは、エンティティの追加またはローカル コレクションや、DbSet のいずれかから削除されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-122">This means that entities can be added or removed from either the Local collection or the DbSet.</span></span> <span data-ttu-id="0e9e2-123">また、新しいエンティティをコンテキストに読み込むクエリはこれらのエンティティで更新されているローカル コレクションになります。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-123">It also means that queries that bring new entities into the context will result in the Local collection being updated with those entities.</span></span> <span data-ttu-id="0e9e2-124">例えば:</span><span class="sxs-lookup"><span data-stu-id="0e9e2-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Load some posts from the database into the context
    context.Posts.Where(p => p.Tags.Contains("entity-framework").Load();  

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

<span data-ttu-id="0e9e2-125">' Entity framework' と 'asp.net' 出力のタグ付けされたいくつか投稿ことが前提と次のように見えます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-125">Assuming we had a few posts tagged with 'entity-framework' and 'asp.net' the output may look something like this:</span></span>  

```  
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

<span data-ttu-id="0e9e2-126">これは、3 つの点を示しています。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-126">This illustrates three points:</span></span>  

- <span data-ttu-id="0e9e2-127">'New in EF は' 新しい投稿追加されたローカル コレクションが Added 状態でコンテキストによって追跡になります。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-127">The new post 'What's New in EF' that was added to the Local collection becomes tracked by the context in the Added state.</span></span> <span data-ttu-id="0e9e2-128">そのため挿入のデータベースに SaveChanges が呼び出されるとします。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-128">It will therefore be inserted into the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="0e9e2-129">ローカル コレクション (EF 初心者向けガイド) から削除された投稿は、コンテキストで削除済みとしてマークされているようになりました。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-129">The post that was removed from the Local collection (EF Beginners Guide) is now marked as deleted in the context.</span></span> <span data-ttu-id="0e9e2-130">削除されます、データベースから SaveChanges が呼び出されるとします。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-130">It will therefore be deleted from the database when SaveChanges is called.</span></span>  
- <span data-ttu-id="0e9e2-131">2 番目のクエリを使用して、コンテキストに読み込まれるその他の投稿 (ASP.NET の初心者向けガイド) は、ローカル コレクションに自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-131">The additional post (ASP.NET Beginners Guide) loaded into the context with the second query is automatically added to the Local collection.</span></span>  

<span data-ttu-id="0e9e2-132">ローカルに関する注意事項を 1 つの最後には、ObservableCollection パフォーマンスは多数のエンティティに適していないためです。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-132">One final thing to note about Local is that because it is an ObservableCollection performance is not great for large numbers of entities.</span></span> <span data-ttu-id="0e9e2-133">そのためのコンテキストで何千ものエンティティを扱う場合ある可能性がありますいないローカルを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-133">Therefore if you are dealing with thousands of entities in your context it may not be advisable to use Local.</span></span>  

## <a name="using-local-for-wpf-data-binding"></a><span data-ttu-id="0e9e2-134">WPF データ バインディングのローカルを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-134">Using Local for WPF data binding</span></span>  

<span data-ttu-id="0e9e2-135">DbSet にローカル プロパティは、ObservableCollection のインスタンスであるため、WPF アプリケーションでのデータ バインディングを直接使用できます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-135">The Local property on DbSet can be used directly for data binding in a WPF application because it is an instance of ObservableCollection.</span></span> <span data-ttu-id="0e9e2-136">つまり、自動的には、前のセクションで説明されている同期コンテキストの内容を維持し、コンテキストの内容は自動的に同期して最新します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-136">As described in the previous sections this means that it will automatically stay in sync with the contents of the context and the contents of the context will automatically stay in sync with it.</span></span> <span data-ttu-id="0e9e2-137">データベース クエリが、ローカルにバインドするものにするデータを使用してローカル コレクションを事前に設定する必要に注意してください。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-137">Note that you do need to pre-populate the Local collection with data for there to be anything to bind to since Local never causes a database query.</span></span>  

<span data-ttu-id="0e9e2-138">これは適切な場所の完全な WPF データ バインド サンプルではありませんが、主な要素は。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-138">This is not an appropriate place for a full WPF data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="0e9e2-139">バインディング ソースのセットアップ</span><span class="sxs-lookup"><span data-stu-id="0e9e2-139">Setup a binding source</span></span>  
- <span data-ttu-id="0e9e2-140">セットのローカル プロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-140">Bind it to the Local property of your set</span></span>  
- <span data-ttu-id="0e9e2-141">データベースにクエリを使用してローカルに設定します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-141">Populate Local using a query to the database.</span></span>  

## <a name="wpf-binding-to-navigation-properties"></a><span data-ttu-id="0e9e2-142">ナビゲーション プロパティを WPF のバインド</span><span class="sxs-lookup"><span data-stu-id="0e9e2-142">WPF binding to navigation properties</span></span>  

<span data-ttu-id="0e9e2-143">実行している場合マスター/詳細データをバインドすることがあります、エンティティのいずれかのナビゲーション プロパティに詳細ビューをバインドするとします。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-143">If you are doing master/detail data binding you may want to bind the detail view to a navigation property of one of your entities.</span></span> <span data-ttu-id="0e9e2-144">この作業を行う簡単な方法では、ナビゲーション プロパティの ObservableCollection を使用します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-144">An easy way to make this work is to use an ObservableCollection for the navigation property.</span></span> <span data-ttu-id="0e9e2-145">例えば:</span><span class="sxs-lookup"><span data-stu-id="0e9e2-145">For example:</span></span>  

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

## <a name="using-local-to-clean-up-entities-in-savechanges"></a><span data-ttu-id="0e9e2-146">ローカルを使用して、SaveChanges 内のエンティティをクリーンアップするには</span><span class="sxs-lookup"><span data-stu-id="0e9e2-146">Using Local to clean up entities in SaveChanges</span></span>  

<span data-ttu-id="0e9e2-147">エンティティのナビゲーション プロパティから削除ほとんどの場合、コンテキストで削除済みとして自動的にマークされません。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-147">In most cases entities removed from a navigation property will not be automatically marked as deleted in the context.</span></span> <span data-ttu-id="0e9e2-148">たとえば、Blog.Posts コレクションから投稿する Post オブジェクトを削除する場合は自動的に削除されません SaveChanges が呼び出されるとします。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-148">For example, if you remove a Post object from the Blog.Posts collection then that post will not be automatically deleted when SaveChanges is called.</span></span> <span data-ttu-id="0e9e2-149">削除する必要がある場合は、これらの未解決のエンティティを見つけて、SaveChanges を呼び出す前に、またはオーバーライドの SaveChanges の一部として削除済みとしてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-149">If you need it to be deleted then you may need to find these dangling entities and mark them as deleted before calling SaveChanges or as part of an overridden SaveChanges.</span></span> <span data-ttu-id="0e9e2-150">例えば:</span><span class="sxs-lookup"><span data-stu-id="0e9e2-150">For example:</span></span>  

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

<span data-ttu-id="0e9e2-151">上記のコードでは、ローカルのコレクションを使用して、すべての投稿とをブログの参照を持たない場合削除済みとしてマークを見つけます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-151">The code above uses the Local collection to find all posts and marks any that do not have a blog reference as deleted.</span></span> <span data-ttu-id="0e9e2-152">それ以外の場合、削除によってコレクションが変更されるため、ToList 呼び出しが必要ですが列挙されているときに呼び出します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-152">The ToList call is required because otherwise the collection will be modified by the Remove call while it is being enumerated.</span></span> <span data-ttu-id="0e9e2-153">他のほとんどの状況では、ToList をまずを使用せずローカル プロパティを直接照会できます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-153">In most other situations you can query directly against the Local property without using ToList first.</span></span>  

## <a name="using-local-and-tobindinglist-for-windows-forms-data-binding"></a><span data-ttu-id="0e9e2-154">ローカルおよび ToBindingList の Windows フォーム データ バインディングの使用</span><span class="sxs-lookup"><span data-stu-id="0e9e2-154">Using Local and ToBindingList for Windows Forms data binding</span></span>  

<span data-ttu-id="0e9e2-155">Windows フォームは、ObservableCollection を直接使用する完全な忠実度のデータ バインディングをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-155">Windows Forms does not support full fidelity data binding using ObservableCollection directly.</span></span> <span data-ttu-id="0e9e2-156">ただし、前のセクションで説明されているすべての利点を得るため、データ バインディングの DbSet ローカル プロパティを使用できます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-156">However, you can still use the DbSet Local property for data binding to get all the benefits described in the previous sections.</span></span> <span data-ttu-id="0e9e2-157">これを作成する ToBindingList 拡張メソッドによって実現されますが、 [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx)実装、ローカルの ObservableCollection によってバックアップされています。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-157">This is achieved through the ToBindingList extension method which creates an [IBindingList](https://msdn.microsoft.com/library/system.componentmodel.ibindinglist.aspx) implementation backed by the Local ObservableCollection.</span></span>  

<span data-ttu-id="0e9e2-158">これは適切な場所の完全な Windows フォーム データ バインド サンプルではありませんが、主な要素は。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-158">This is not an appropriate place for a full Windows Forms data binding sample but the key elements are:</span></span>  

- <span data-ttu-id="0e9e2-159">オブジェクトのバインディング ソースをセットアップします。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-159">Setup an object binding source</span></span>  
- <span data-ttu-id="0e9e2-160">Local.ToBindingList() を使用して、セットのローカル プロパティにバインドします。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-160">Bind it to the Local property of your set using Local.ToBindingList()</span></span>  
- <span data-ttu-id="0e9e2-161">データベースにクエリを使用してローカルを設定します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-161">Populate Local using a query to the database</span></span>  

## <a name="getting-detailed-information-about-tracked-entities"></a><span data-ttu-id="0e9e2-162">追跡対象のエンティティに関する詳細な情報の取得</span><span class="sxs-lookup"><span data-stu-id="0e9e2-162">Getting detailed information about tracked entities</span></span>  

<span data-ttu-id="0e9e2-163">このシリーズの例の多くは、エンティティの対象の DbEntityEntry インスタンスを返すエントリ メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-163">Many of the examples in this series use the Entry method to return a DbEntityEntry instance for an entity.</span></span> <span data-ttu-id="0e9e2-164">このエントリのオブジェクトは、エンティティなど、現在の状態に関する情報を収集すると、関連エンティティの明示的な読み込みなどのエンティティに対して操作を実行するための出発点として機能します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-164">This entry object then acts as the starting point for gathering information about the entity such as its current state, as well as for performing operations on the entity such as explicitly loading a related entity.</span></span>  

<span data-ttu-id="0e9e2-165">エントリ メソッドでは、コンテキストによって追跡されている多くまたはすべてのエンティティの対象の DbEntityEntry オブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-165">The Entries methods return DbEntityEntry objects for many or all entities being tracked by the context.</span></span> <span data-ttu-id="0e9e2-166">これにより、情報を収集または 1 つのエントリだけではなく、多数のエンティティに対して操作を実行することができます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-166">This allows you to gather information or perform operations on many entities rather than just a single entry.</span></span> <span data-ttu-id="0e9e2-167">例えば:</span><span class="sxs-lookup"><span data-stu-id="0e9e2-167">For example:</span></span>  

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

<span data-ttu-id="0e9e2-168">例では、作成者、閲覧者のクラスが導入されています - IPerson インターフェイスを実装してこれらのクラスの両方がわかります。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-168">You'll notice we are introducing a Author and Reader class into the example - both of these classes implement the IPerson interface.</span></span>  

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

<span data-ttu-id="0e9e2-169">データベースに、次のデータがあると仮定します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-169">Let's assume we have the following data in the database:</span></span>

<span data-ttu-id="0e9e2-170">BlogId ブログ = 1、名前 = ' ADO.NET ブログ '</span><span class="sxs-lookup"><span data-stu-id="0e9e2-170">Blog with BlogId = 1 and Name = 'ADO.NET Blog'</span></span>  
<span data-ttu-id="0e9e2-171">BlogId ブログ = 2、名前 = 'Visual Studio ブログ'</span><span class="sxs-lookup"><span data-stu-id="0e9e2-171">Blog with BlogId = 2 and Name = 'The Visual Studio Blog'</span></span>  
<span data-ttu-id="0e9e2-172">BlogId ブログ = 3 および名 = '.NET Framework ブログ'</span><span class="sxs-lookup"><span data-stu-id="0e9e2-172">Blog with BlogId = 3 and Name = '.NET Framework Blog'</span></span>  
<span data-ttu-id="0e9e2-173">著者 Id で作成者 = 1、名前 = ' Joe Bloggs'</span><span class="sxs-lookup"><span data-stu-id="0e9e2-173">Author with AuthorId = 1 and Name = 'Joe Bloggs'</span></span>  
<span data-ttu-id="0e9e2-174">使用した ReaderId リーダー = 1、名前 = ' John Doe'</span><span class="sxs-lookup"><span data-stu-id="0e9e2-174">Reader with ReaderId = 1 and Name = 'John Doe'</span></span>  

<span data-ttu-id="0e9e2-175">コードの実行からの出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-175">The output from running the code would be:</span></span>  

```  
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

<span data-ttu-id="0e9e2-176">これらの例は、いくつかの点を示しています。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-176">These examples illustrate several points:</span></span>  

- <span data-ttu-id="0e9e2-177">エントリ メソッドでは、削除済みを含む、すべての状態でエンティティのエントリを返します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-177">The Entries methods return entries for entities in all states, including Deleted.</span></span> <span data-ttu-id="0e9e2-178">これと比較を除くローカル エンティティを削除します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-178">Compare this to Local which excludes Deleted entities.</span></span>  
- <span data-ttu-id="0e9e2-179">エントリの非ジェネリック メソッドを使用する場合は、すべてのエンティティ型のエントリが返されます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-179">Entries for all entity types are returned when the non-generic Entries method is used.</span></span> <span data-ttu-id="0e9e2-180">エントリのジェネリック メソッドを使用する場合エントリはジェネリック型のインスタンスであるエンティティに対してのみ返されます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-180">When the generic entries method is used entries are only returned for entities that are instances of the generic type.</span></span> <span data-ttu-id="0e9e2-181">これは、上のすべてのブログのエントリを取得に使用されました。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-181">This was used above to get entries for all blogs.</span></span> <span data-ttu-id="0e9e2-182">でも、IPerson を実装するすべてのエンティティのエントリの取得に使用されていました。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-182">It was also used to get entries for all entities that implement IPerson.</span></span> <span data-ttu-id="0e9e2-183">これは、実際のエンティティ型をジェネリック型がないことを示します。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-183">This demonstrates that the generic type does not have to be an actual entity type.</span></span>  
- <span data-ttu-id="0e9e2-184">オブジェクトに LINQ を使用して、返される結果をフィルター処理できます。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-184">LINQ to Objects can be used to filter the results returned.</span></span> <span data-ttu-id="0e9e2-185">これは、上記変更される場合に限り、任意の型のエンティティを検索に使用されました。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-185">This was used above to find entities of any type as long as they are modified.</span></span>  

<span data-ttu-id="0e9e2-186">対象の DbEntityEntry インスタンスが常に null 以外のエンティティを含めることことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-186">Note that DbEntityEntry instances always contain a non-null Entity.</span></span> <span data-ttu-id="0e9e2-187">リレーションシップ エントリとスタブ エントリは表されませんとして対象の DbEntityEntry インスタンスのため、これらのフィルター処理する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="0e9e2-187">Relationship entries and stub entries are not represented as DbEntityEntry instances so there is no need to filter for these.</span></span>
