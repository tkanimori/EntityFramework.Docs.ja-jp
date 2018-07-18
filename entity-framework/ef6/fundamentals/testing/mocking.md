---
title: モック作成フレームワークの EF6 でのテスト
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
caps.latest.revision: 3
ms.openlocfilehash: 7529929a3ed3906e1201c0899f2fb8b959ec9ed2
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "39122579"
---
# <a name="testing-with-a-mocking-framework"></a><span data-ttu-id="5c188-102">モック作成フレームワークとテスト</span><span class="sxs-lookup"><span data-stu-id="5c188-102">Testing with a mocking framework</span></span>
> [!NOTE]
> <span data-ttu-id="5c188-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="5c188-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="5c188-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="5c188-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="5c188-105">アプリケーションのテストを記述する場合、データベースに達するを回避することが望ましいは多くの場合です。</span><span class="sxs-lookup"><span data-stu-id="5c188-105">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="5c188-106">Entity Framework からの使用によると、メモリ内のデータを使用するコンテキストを作成して – – テストで定義されている動作では、これを実現することができます。</span><span class="sxs-lookup"><span data-stu-id="5c188-106">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="5c188-107">テスト代替を作成するためのオプション</span><span class="sxs-lookup"><span data-stu-id="5c188-107">Options for creating test doubles</span></span>  

<span data-ttu-id="5c188-108">コンテキストのメモリ内のバージョンを作成するために使用できる 2 つのさまざまな方法はあります。</span><span class="sxs-lookup"><span data-stu-id="5c188-108">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="5c188-109">**独自のテスト代替を作成する**– この方法では、コンテキストと DbSets の独自のインメモリ実装を記述します。</span><span class="sxs-lookup"><span data-stu-id="5c188-109">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="5c188-110">これにより、多数の制御、クラスの動作が記述して、コードの妥当な量を所有している必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c188-110">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="5c188-111">**モック作成フレームワークを使用して、テスト代替を作成する**– (Moq) などのモック作成フレームワークを使用することが皆さんのインメモリ実装コンテキストとセットの実行時に動的に作成します。</span><span class="sxs-lookup"><span data-stu-id="5c188-111">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of you context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="5c188-112">この記事ではモック作成フレームワークを使用してを処理します。</span><span class="sxs-lookup"><span data-stu-id="5c188-112">This article will deal with using a mocking framework.</span></span> <span data-ttu-id="5c188-113">独自のテスト代替を作成するため、次を参照してください。 [、独自のテスト代替によるテスト](writing-test-doubles.md)します。</span><span class="sxs-lookup"><span data-stu-id="5c188-113">For creating your own test doubles see [Testing with Your Own Test Doubles](writing-test-doubles.md).</span></span>  

<span data-ttu-id="5c188-114">モック作成フレームワークで EF を使用する方法を紹介するには、Moq を使用して行います。</span><span class="sxs-lookup"><span data-stu-id="5c188-114">To demonstrate using EF with a mocking framework we are going to use Moq.</span></span> <span data-ttu-id="5c188-115">Moq を取得する最も簡単な方法は、インストールには、 [NuGet からパッケージを Moq](http://nuget.org/packages/Moq/)します。</span><span class="sxs-lookup"><span data-stu-id="5c188-115">The easiest way to get Moq is to install the [Moq package from NuGet](http://nuget.org/packages/Moq/).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="5c188-116">EF6 以前のバージョンでのテスト</span><span class="sxs-lookup"><span data-stu-id="5c188-116">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="5c188-117">この記事で示されているシナリオでは、EF6 で DbSet に加えられたいくつかの変更に依存します。</span><span class="sxs-lookup"><span data-stu-id="5c188-117">The scenario shown in this article is dependent on some changes we made to DbSet in EF6.</span></span> <span data-ttu-id="5c188-118">EF5 と以前のバージョンをテストするため、次を参照してください。[偽のコンテキストでのテスト](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)します。</span><span class="sxs-lookup"><span data-stu-id="5c188-118">For testing with EF5 and earlier version see [Testing with a Fake Context](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="5c188-119">EF のメモリ内のテスト代替の制限事項</span><span class="sxs-lookup"><span data-stu-id="5c188-119">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="5c188-120">メモリ内のテスト代替は、単体テスト、アプリケーションの EF を使用するビットのレベルのカバレッジを提供する優れた方法を指定できます。</span><span class="sxs-lookup"><span data-stu-id="5c188-120">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="5c188-121">ただし、これを行うときは、メモリ内のデータに対してクエリを実行するのに LINQ to Objects を使用しています。</span><span class="sxs-lookup"><span data-stu-id="5c188-121">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="5c188-122">これは、データベースに対して実行される SQL クエリに変換する EF の LINQ プロバイダー (LINQ to Entities) を使用するよりもの異なる動作になることができます。</span><span class="sxs-lookup"><span data-stu-id="5c188-122">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="5c188-123">このような相違点の 1 つの例では、関連するデータを読み込んでいます。</span><span class="sxs-lookup"><span data-stu-id="5c188-123">One example of such a difference is loading related data.</span></span> <span data-ttu-id="5c188-124">一連のブログを作成する場合、そのそれぞれが関連する投稿をし、各ブログのため常に、関連する投稿に読み込まれるメモリ内のデータを使用する場合。</span><span class="sxs-lookup"><span data-stu-id="5c188-124">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="5c188-125">ただし、データベースに対して実行する場合は、Include メソッドを使用する場合は、データを読み込むのみされます。</span><span class="sxs-lookup"><span data-stu-id="5c188-125">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="5c188-126">このため、常に一定レベルのエンド ツー エンドの (単体テスト) だけでなく、アプリケーションがデータベースに対して正しく動作させるテストを含めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5c188-126">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="5c188-127">この記事と併せてに従ってください。</span><span class="sxs-lookup"><span data-stu-id="5c188-127">Following along with this article</span></span>  

<span data-ttu-id="5c188-128">この記事では、希望する場合は、作業を進めるに Visual Studio にコピーできます。 完全なコード リストを提供します。</span><span class="sxs-lookup"><span data-stu-id="5c188-128">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="5c188-129">作成する最も簡単な**単体テスト プロジェクト**必要がありますターゲットに **.NET Framework 4.5** async を使用して、次のセクションを完了します。</span><span class="sxs-lookup"><span data-stu-id="5c188-129">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="the-ef-model"></a><span data-ttu-id="5c188-130">EF モデル</span><span class="sxs-lookup"><span data-stu-id="5c188-130">The EF model</span></span>  

<span data-ttu-id="5c188-131">テストをサービスでは、EF の使用により、モデル、BloggingContext とブログや投稿クラスから成ります。</span><span class="sxs-lookup"><span data-stu-id="5c188-131">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="5c188-132">このコードが EF デザイナーによって生成されたか、Code First モデルがあります。</span><span class="sxs-lookup"><span data-stu-id="5c188-132">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext
    {
        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }
        public string Url { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
}
```  

### <a name="virtual-dbset-properties-with-ef-designer"></a><span data-ttu-id="5c188-133">EF デザイナーを使用した仮想の DbSet プロパティ</span><span class="sxs-lookup"><span data-stu-id="5c188-133">Virtual DbSet properties with EF Designer</span></span>  

<span data-ttu-id="5c188-134">コンテキストの DbSet プロパティは、仮想化がマークされるに注意してください。</span><span class="sxs-lookup"><span data-stu-id="5c188-134">Note that the DbSet properties on the context are marked as virtual.</span></span> <span data-ttu-id="5c188-135">これで、モック作成フレームワークとモック実装でこれらのプロパティのオーバーライドをコンテキストから派生させる。</span><span class="sxs-lookup"><span data-stu-id="5c188-135">This will allow the mocking framework to derive from our context and overriding these properties with a mocked implementation.</span></span>  

<span data-ttu-id="5c188-136">Code First を使用している場合するクラスを直接編集できます。</span><span class="sxs-lookup"><span data-stu-id="5c188-136">If you are using Code First then you can edit your classes directly.</span></span> <span data-ttu-id="5c188-137">EF デザイナーを使用している場合が、コンテキストを生成する T4 テンプレートを編集する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c188-137">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="5c188-138">開き、 \<model_name\>します。Edmx ファイルの下に入れ子になっている.context.tt ファイルは、コードの次のフラグメントを検索しに示すように仮想キーワードを追加します。</span><span class="sxs-lookup"><span data-stu-id="5c188-138">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the virtual keyword as shown.</span></span>  

``` csharp
public string DbSet(EntitySet entitySet)
{
    return string.Format(
        CultureInfo.InvariantCulture,
        "{0} virtual DbSet\<{1}> {2} {{ get; set; }}",
        Accessibility.ForReadOnlyProperty(entitySet),
        _typeMapper.GetTypeName(entitySet.ElementType),
        _code.Escape(entitySet));
}
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="5c188-139">サービスをテストするには</span><span class="sxs-lookup"><span data-stu-id="5c188-139">Service to be tested</span></span>  

<span data-ttu-id="5c188-140">ここでは BlogService のいくつかのテストを記述するのには、インメモリ テスト代替によるテストを示します。</span><span class="sxs-lookup"><span data-stu-id="5c188-140">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="5c188-141">サービスが新しいブログ (AddBlog) を作成できると、名前 (GetAllBlogs) によって順序付けられたすべてのブログを返します。</span><span class="sxs-lookup"><span data-stu-id="5c188-141">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="5c188-142">GetAllBlogs、だけでなく名前 (GetAllBlogsAsync) 順でのすべてのブログを非同期的に取得する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="5c188-142">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private BloggingContext _context;

        public BlogService(BloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = _context.Blogs.Add(new Blog { Name = name, Url = url });
            _context.SaveChanges();

            return blog;
        }

        public List<Blog> GetAllBlogs()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return query.ToList();
        }

        public async Task<List<Blog>> GetAllBlogsAsync()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return await query.ToListAsync();
        }
    }
}
```  

## <a name="testing-non-query-scenarios"></a><span data-ttu-id="5c188-143">非クエリのシナリオのテスト</span><span class="sxs-lookup"><span data-stu-id="5c188-143">Testing non-query scenarios</span></span>  

<span data-ttu-id="5c188-144">非クエリ メソッドのテストを開始するために必要なことです。</span><span class="sxs-lookup"><span data-stu-id="5c188-144">That’s all we need to do to start testing non-query methods.</span></span> <span data-ttu-id="5c188-145">次のテストでは、Moq を使用して、コンテキストを作成します。</span><span class="sxs-lookup"><span data-stu-id="5c188-145">The following test uses Moq to create a context.</span></span> <span data-ttu-id="5c188-146">DbSet を作成し、\<ブログ\>つながり、コンテキストのブログのプロパティから返されるとします。</span><span class="sxs-lookup"><span data-stu-id="5c188-146">It then creates a DbSet\<Blog\> and wires it up to be returned from the context’s Blogs property.</span></span> <span data-ttu-id="5c188-147">次に、コンテキストを使用して、– AddBlog メソッドを使用して新しいブログの作成に使用される、新しい BlogService を作成します。</span><span class="sxs-lookup"><span data-stu-id="5c188-147">Next, the context is used to create a new BlogService which is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="5c188-148">最後に、テストは、サービスが新しいブログの追加し、コンテキストで SaveChanges を呼び出したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="5c188-148">Finally, the test verifies that the service added a new Blog and called SaveChanges on the context.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Data.Entity;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var mockSet = new Mock<DbSet<Blog>>();

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(m => m.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            mockSet.Verify(m => m.Add(It.IsAny<Blog>()), Times.Once());
            mockContext.Verify(m => m.SaveChanges(), Times.Once());
        }
    }
}
```  

## <a name="testing-query-scenarios"></a><span data-ttu-id="5c188-149">クエリのシナリオのテスト</span><span class="sxs-lookup"><span data-stu-id="5c188-149">Testing query scenarios</span></span>  

<span data-ttu-id="5c188-150">Double、DbSet のテストに対してクエリを実行できるようにするためには、IQueryable の実装をセットアップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c188-150">In order to be able to execute queries against our DbSet test double we need to setup an implementation of IQueryable.</span></span> <span data-ttu-id="5c188-151">最初の手順がいくつかのメモリ内データを作成するには – 一覧を使用している\<ブログ\>します。</span><span class="sxs-lookup"><span data-stu-id="5c188-151">The first step is to create some in-memory data – we’re using a List\<Blog\>.</span></span> <span data-ttu-id="5c188-152">次に、作成コンテキストと DBSet\<ブログ\>し、linq to Objects プロバイダーの一覧で動作する委任がだけ –、DbSet の IQueryable 実装をワイヤ\<T\>します。</span><span class="sxs-lookup"><span data-stu-id="5c188-152">Next, we create a context and DBSet\<Blog\> then wire up the IQueryable implementation for the DbSet – they’re just delegating to the LINQ to Objects provider that works with List\<T\>.</span></span>  

<span data-ttu-id="5c188-153">名前によって GetAllBlogs から戻るデータが順序付けすることを確認および作成、テスト代替に基づいて BlogService できます。</span><span class="sxs-lookup"><span data-stu-id="5c188-153">We can then create a BlogService based on our test doubles and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Provider).Returns(data.Provider);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(0 => data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

### <a name="testing-with-async-queries"></a><span data-ttu-id="5c188-154">非同期クエリでのテスト</span><span class="sxs-lookup"><span data-stu-id="5c188-154">Testing with async queries</span></span>

<span data-ttu-id="5c188-155">Entity Framework 6 では、非同期的にクエリの実行に使用できる拡張メソッドのセットが導入されました。</span><span class="sxs-lookup"><span data-stu-id="5c188-155">Entity Framework 6 introduced a set of extension methods that can be used to asynchronously execute a query.</span></span> <span data-ttu-id="5c188-156">これらのメソッドの例には、ToListAsync、firstasync へ、ForEachAsync などが含まれます。</span><span class="sxs-lookup"><span data-stu-id="5c188-156">Examples of these methods include ToListAsync, FirstAsync, ForEachAsync, etc.</span></span>  

<span data-ttu-id="5c188-157">Entity Framework のクエリは、LINQ の利用、ために、拡張メソッドは、IQueryable と IEnumerable に定義されます。</span><span class="sxs-lookup"><span data-stu-id="5c188-157">Because Entity Framework queries make use of LINQ, the extension methods are defined on IQueryable and IEnumerable.</span></span> <span data-ttu-id="5c188-158">ただし、Entity Framework で使用するだけで設計されるため、Entity Framework クエリが LINQ クエリで使用しようとする場合、次のエラーを受け取る可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5c188-158">However, because they are only designed to be used with Entity Framework you may receive the following error if you try to use them on a LINQ query that isn’t an Entity Framework query:</span></span>

> <span data-ttu-id="5c188-159">IDbAsyncEnumerable ソース IQueryable を実装していない{0}します。</span><span class="sxs-lookup"><span data-stu-id="5c188-159">The source IQueryable doesn't implement IDbAsyncEnumerable{0}.</span></span> <span data-ttu-id="5c188-160">Entity Framework の非同期操作の IDbAsyncEnumerable を実装するソースのみを使用できます。</span><span class="sxs-lookup"><span data-stu-id="5c188-160">Only sources that implement IDbAsyncEnumerable can be used for Entity Framework asynchronous operations.</span></span> <span data-ttu-id="5c188-161">詳細についてを参照してください[ http://go.microsoft.com/fwlink/?LinkId=287068](http://go.microsoft.com/fwlink/?LinkId=287068)します。</span><span class="sxs-lookup"><span data-stu-id="5c188-161">For more details see [http://go.microsoft.com/fwlink/?LinkId=287068](http://go.microsoft.com/fwlink/?LinkId=287068).</span></span>  

<span data-ttu-id="5c188-162">非同期メソッドは、に対して EF クエリを実行している場合にのみサポート、ながらに対するメモリ内の実行、DbSet の double をテストするときに、単体テストで使用したい場合があります。</span><span class="sxs-lookup"><span data-stu-id="5c188-162">Whilst the async methods are only supported when running against an EF query, you may want to use them in your unit test when running against an in-memory test double of a DbSet.</span></span>  

<span data-ttu-id="5c188-163">非同期メソッドを使用するためには、非同期クエリを処理する、インメモリ DbAsyncQueryProvider を作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5c188-163">In order to use the async methods we need to create an in-memory DbAsyncQueryProvider to process the async query.</span></span> <span data-ttu-id="5c188-164">Moq を使用してクエリ プロバイダーをセットアップすることができます中、は、コードでテスト ダブル実装を作成するはるかに簡単です。</span><span class="sxs-lookup"><span data-stu-id="5c188-164">Whilst it would be possible to setup a query provider using Moq, it is much easier to create a test double implementation in code.</span></span> <span data-ttu-id="5c188-165">この実装のコードは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5c188-165">The code for this implementation is as follows:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
    internal class TestDbAsyncQueryProvider<TEntity> : IDbAsyncQueryProvider
    {
        private readonly IQueryProvider _inner;

        internal TestDbAsyncQueryProvider(IQueryProvider inner)
        {
            _inner = inner;
        }

        public IQueryable CreateQuery(Expression expression)
        {
            return new TestDbAsyncEnumerable<TEntity>(expression);
        }

        public IQueryable<TElement> CreateQuery<TElement>(Expression expression)
        {
            return new TestDbAsyncEnumerable<TElement>(expression);
        }

        public object Execute(Expression expression)
        {
            return _inner.Execute(expression);
        }

        public TResult Execute<TResult>(Expression expression)
        {
            return _inner.Execute<TResult>(expression);
        }

        public Task<object> ExecuteAsync(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute(expression));
        }

        public Task<TResult> ExecuteAsync<TResult>(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute<TResult>(expression));
        }
    }

    internal class TestDbAsyncEnumerable<T> : EnumerableQuery<T>, IDbAsyncEnumerable<T>, IQueryable<T>
    {
        public TestDbAsyncEnumerable(IEnumerable<T> enumerable)
            : base(enumerable)
        { }

        public TestDbAsyncEnumerable(Expression expression)
            : base(expression)
        { }

        public IDbAsyncEnumerator<T> GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<T>(this.AsEnumerable().GetEnumerator());
        }

        IDbAsyncEnumerator IDbAsyncEnumerable.GetAsyncEnumerator()
        {
            return GetAsyncEnumerator();
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<T>(this); }
        }
    }

    internal class TestDbAsyncEnumerator<T> : IDbAsyncEnumerator<T>
    {
        private readonly IEnumerator<T> _inner;

        public TestDbAsyncEnumerator(IEnumerator<T> inner)
        {
            _inner = inner;
        }

        public void Dispose()
        {
            _inner.Dispose();
        }

        public Task<bool> MoveNextAsync(CancellationToken cancellationToken)
        {
            return Task.FromResult(_inner.MoveNext());
        }

        public T Current
        {
            get { return _inner.Current; }
        }

        object IDbAsyncEnumerator.Current
        {
            get { return Current; }
        }
    }
}
```  

<span data-ttu-id="5c188-166">非同期のクエリ プロバイダーが作成できた、新しい GetAllBlogsAsync メソッドの単体テストを記述できます。</span><span class="sxs-lookup"><span data-stu-id="5c188-166">Now that we have an async query provider we can write a unit test for our new GetAllBlogsAsync method.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    [TestClass]
    public class AsyncQueryTests
    {
        [TestMethod]
        public async Task GetAllBlogsAsync_orders_by_name()
        {

            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IDbAsyncEnumerable<Blog>>()
                .Setup(m => m.GetAsyncEnumerator())
                .Returns(new TestDbAsyncEnumerator<Blog>(data.GetEnumerator()));

            mockSet.As<IQueryable<Blog>>()
                .Setup(m => m.Provider)
                .Returns(new TestDbAsyncQueryProvider<Blog>(data.Provider));

            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  
