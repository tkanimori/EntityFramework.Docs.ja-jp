---
title: モックフレームワークを使用したテスト-EF6
description: Entity Framework 6 でのモックフレームワークを使用したテスト
author: divega
ms.date: 10/23/2016
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
uid: ef6/fundamentals/testing/mocking
ms.openlocfilehash: 01890ab3bb8dbf0caa7b3eff797e53b06bc8ec9b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618302"
---
# <a name="testing-with-a-mocking-framework"></a><span data-ttu-id="2fd9a-103">モックフレームワークを使用したテスト</span><span class="sxs-lookup"><span data-stu-id="2fd9a-103">Testing with a mocking framework</span></span>
> [!NOTE]
> <span data-ttu-id="2fd9a-104">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="2fd9a-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="2fd9a-106">アプリケーションのテストを作成するときは、多くの場合、データベースにヒットしないようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-106">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="2fd9a-107">Entity Framework では、インメモリデータを使用するコンテキスト (テストで定義された動作) を作成することによってこれを実現できます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-107">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="2fd9a-108">テスト代替を作成するためのオプション</span><span class="sxs-lookup"><span data-stu-id="2fd9a-108">Options for creating test doubles</span></span>  

<span data-ttu-id="2fd9a-109">インメモリバージョンのコンテキストを作成するには、2つの異なる方法を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-109">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="2fd9a-110">**独自のテスト double を作成** する–この方法では、独自のコンテキストと dbsets のインメモリ実装を記述します。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-110">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="2fd9a-111">これにより、クラスの動作を細かく制御できますが、適切な量のコードを記述して所有することができます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-111">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="2fd9a-112">**モックフレームワークを使用したテスト代替の作成** –モックフレームワーク (moq など) を使用すると、コンテキストのインメモリ実装と、実行時に動的に作成される設定を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-112">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of your context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="2fd9a-113">この記事では、モックフレームワークの使用について説明します。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-113">This article will deal with using a mocking framework.</span></span> <span data-ttu-id="2fd9a-114">独自のテスト double を作成する場合は、「テスト [を独自](xref:ef6/fundamentals/testing/writing-test-doubles)に作成する double」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-114">For creating your own test doubles see [Testing with Your Own Test Doubles](xref:ef6/fundamentals/testing/writing-test-doubles).</span></span>  

<span data-ttu-id="2fd9a-115">モックフレームワークで EF を使用する方法を示すために、Moq を使用します。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-115">To demonstrate using EF with a mocking framework we are going to use Moq.</span></span> <span data-ttu-id="2fd9a-116">Moq を取得する最も簡単な方法は、 [NuGet から moq パッケージ](https://nuget.org/packages/Moq/)をインストールすることです。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-116">The easiest way to get Moq is to install the [Moq package from NuGet](https://nuget.org/packages/Moq/).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="2fd9a-117">EF6 バージョンでのテスト</span><span class="sxs-lookup"><span data-stu-id="2fd9a-117">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="2fd9a-118">この記事に示されているシナリオは、EF6 で DbSet に加えられたいくつかの変更に依存しています。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-118">The scenario shown in this article is dependent on some changes we made to DbSet in EF6.</span></span> <span data-ttu-id="2fd9a-119">EF5 以前のバージョンでのテストについて [は、「偽のコンテキストを使用したテスト](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-119">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="2fd9a-120">EF のメモリ内テストの2倍の制限事項</span><span class="sxs-lookup"><span data-stu-id="2fd9a-120">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="2fd9a-121">メモリ内テストの double は、EF を使用するアプリケーションのビットの単体テストレベルを提供するのに適した方法です。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-121">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="2fd9a-122">ただし、これを行う場合は、LINQ to Objects を使用してインメモリデータに対してクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-122">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="2fd9a-123">これにより、EF の LINQ プロバイダー (LINQ to Entities) を使用して、データベースに対して実行される SQL にクエリを変換する場合と動作が異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-123">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="2fd9a-124">このような違いの一例として、関連データの読み込みがあります。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-124">One example of such a difference is loading related data.</span></span> <span data-ttu-id="2fd9a-125">それぞれが関連する投稿を持つ一連のブログを作成した場合、メモリ内データを使用すると、関連する投稿は常に各ブログに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-125">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="2fd9a-126">ただし、データベースに対して実行する場合は、Include メソッドを使用した場合にのみデータが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-126">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="2fd9a-127">このため、アプリケーションがデータベースに対して正しく動作するようにするには、(単体テストに加えて) 何らかのレベルのエンドツーエンドテストを常に含めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-127">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="2fd9a-128">この記事では次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-128">Following along with this article</span></span>  

<span data-ttu-id="2fd9a-129">この記事では、必要に応じて実行するために Visual Studio にコピーできる完全なコードリストを示します。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-129">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="2fd9a-130">**単体テストプロジェクト**を作成するのが最も簡単です。 async を使用するセクションを完了するには、 **.NET Framework 4.5**を対象にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-130">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="the-ef-model"></a><span data-ttu-id="2fd9a-131">EF モデル</span><span class="sxs-lookup"><span data-stu-id="2fd9a-131">The EF model</span></span>  

<span data-ttu-id="2fd9a-132">テストするサービスでは、Bの出力コンテキストとブログクラスおよび Post クラスで構成された EF モデルが使用されています。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-132">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="2fd9a-133">このコードは、EF デザイナーによって生成されたか、Code First モデルである可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-133">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

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

### <a name="virtual-dbset-properties-with-ef-designer"></a><span data-ttu-id="2fd9a-134">EF デザイナーを使用した仮想 DbSet プロパティ</span><span class="sxs-lookup"><span data-stu-id="2fd9a-134">Virtual DbSet properties with EF Designer</span></span>  

<span data-ttu-id="2fd9a-135">コンテキストの DbSet プロパティは virtual とマークされていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-135">Note that the DbSet properties on the context are marked as virtual.</span></span> <span data-ttu-id="2fd9a-136">これにより、モックフレームワークをコンテキストから派生させ、モック実装でこれらのプロパティをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-136">This will allow the mocking framework to derive from our context and overriding these properties with a mocked implementation.</span></span>  

<span data-ttu-id="2fd9a-137">Code First を使用している場合は、クラスを直接編集できます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-137">If you are using Code First then you can edit your classes directly.</span></span> <span data-ttu-id="2fd9a-138">EF デザイナーを使用している場合は、コンテキストを生成する T4 テンプレートを編集する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-138">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="2fd9a-139">を開き \<model_name\> ます。Edmx ファイルの下に入れ子になっている Context.tt ファイル。次のコードのフラグメントを検索し、次に示すように仮想キーワードを追加します。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-139">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the virtual keyword as shown.</span></span>  

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

## <a name="service-to-be-tested"></a><span data-ttu-id="2fd9a-140">テストするサービス</span><span class="sxs-lookup"><span data-stu-id="2fd9a-140">Service to be tested</span></span>  

<span data-ttu-id="2fd9a-141">インメモリテストの2倍のテストを実行するために、ブログサービスに対していくつかのテストを記述します。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-141">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="2fd9a-142">このサービスは、新しいブログ (AddBlog) を作成し、名前で並べ替えられたすべてのブログ (GetAllBlogs) を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-142">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="2fd9a-143">GetAllBlogs だけでなく、名前で並べ替えられたすべてのブログを非同期に取得するメソッドも用意されています (GetAllBlogsAsync)。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-143">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

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

## <a name="testing-non-query-scenarios"></a><span data-ttu-id="2fd9a-144">クエリ以外のシナリオのテスト</span><span class="sxs-lookup"><span data-stu-id="2fd9a-144">Testing non-query scenarios</span></span>  

<span data-ttu-id="2fd9a-145">クエリ以外のメソッドのテストを開始するために必要な操作はこれだけです。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-145">That’s all we need to do to start testing non-query methods.</span></span> <span data-ttu-id="2fd9a-146">次のテストでは、Moq を使用してコンテキストを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-146">The following test uses Moq to create a context.</span></span> <span data-ttu-id="2fd9a-147">次に、DbSet を作成 \<Blog\> し、コンテキストの [ブログ] プロパティから返されるようにします。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-147">It then creates a DbSet\<Blog\> and wires it up to be returned from the context’s Blogs property.</span></span> <span data-ttu-id="2fd9a-148">次に、コンテキストを使用して新しいブログサービスを作成します。このサービスは、AddBlog メソッドを使用して新しいブログを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-148">Next, the context is used to create a new BlogService which is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="2fd9a-149">最後に、このテストでは、サービスが新しいブログを追加し、コンテキストに SaveChanges という名前を追加したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-149">Finally, the test verifies that the service added a new Blog and called SaveChanges on the context.</span></span>  

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

## <a name="testing-query-scenarios"></a><span data-ttu-id="2fd9a-150">クエリシナリオのテスト</span><span class="sxs-lookup"><span data-stu-id="2fd9a-150">Testing query scenarios</span></span>  

<span data-ttu-id="2fd9a-151">DbSet テスト double に対してクエリを実行できるようにするには、IQueryable の実装を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-151">In order to be able to execute queries against our DbSet test double we need to setup an implementation of IQueryable.</span></span> <span data-ttu-id="2fd9a-152">最初の手順では、メモリ内のデータをいくつか作成します。リストを使用してい \<Blog\> ます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-152">The first step is to create some in-memory data – we’re using a List\<Blog\>.</span></span> <span data-ttu-id="2fd9a-153">次に、コンテキストと DBSet を作成 \<Blog\> し、DbSet の IQueryable 実装を接続します。これは、List で動作する LINQ to Objects プロバイダーに委任するだけ \<T\> です。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-153">Next, we create a context and DBSet\<Blog\> then wire up the IQueryable implementation for the DbSet – they’re just delegating to the LINQ to Objects provider that works with List\<T\>.</span></span>  

<span data-ttu-id="2fd9a-154">次に、テスト double に基づいてブログサービスを作成し、GetAllBlogs から返されるデータが名前で並べ替えられるようにします。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-154">We can then create a BlogService based on our test doubles and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

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
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

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

### <a name="testing-with-async-queries"></a><span data-ttu-id="2fd9a-155">非同期クエリを使用したテスト</span><span class="sxs-lookup"><span data-stu-id="2fd9a-155">Testing with async queries</span></span>

<span data-ttu-id="2fd9a-156">Entity Framework 6 では、非同期的にクエリを実行するために使用できる一連の拡張メソッドが導入されました。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-156">Entity Framework 6 introduced a set of extension methods that can be used to asynchronously execute a query.</span></span> <span data-ttu-id="2fd9a-157">これらのメソッドの例には、ToListAsync、FirstAsync、ForEachAsync などがあります。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-157">Examples of these methods include ToListAsync, FirstAsync, ForEachAsync, etc.</span></span>  

<span data-ttu-id="2fd9a-158">Entity Framework クエリでは LINQ が使用されるため、拡張メソッドは IQueryable と IEnumerable で定義されます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-158">Because Entity Framework queries make use of LINQ, the extension methods are defined on IQueryable and IEnumerable.</span></span> <span data-ttu-id="2fd9a-159">ただし、これらは Entity Framework と共に使用するように設計されているため、Entity Framework クエリではない LINQ クエリで使用しようとすると、次のエラーが発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-159">However, because they are only designed to be used with Entity Framework you may receive the following error if you try to use them on a LINQ query that isn’t an Entity Framework query:</span></span>

> <span data-ttu-id="2fd9a-160">ソース IQueryable は IDbAsyncEnumerable を実装していません {0} 。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-160">The source IQueryable doesn't implement IDbAsyncEnumerable{0}.</span></span> <span data-ttu-id="2fd9a-161">Entity Framework 非同期操作に使用できるのは、IDbAsyncEnumerable を実装するソースだけです。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-161">Only sources that implement IDbAsyncEnumerable can be used for Entity Framework asynchronous operations.</span></span> <span data-ttu-id="2fd9a-162">詳細については、「」を参照してください [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068) 。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-162">For more details see [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068).</span></span>  

<span data-ttu-id="2fd9a-163">非同期メソッドは、EF クエリに対して実行する場合にのみサポートされますが、DbSet のメモリ内テスト double に対して実行するときに、単体テストで使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-163">Whilst the async methods are only supported when running against an EF query, you may want to use them in your unit test when running against an in-memory test double of a DbSet.</span></span>  

<span data-ttu-id="2fd9a-164">非同期メソッドを使用するには、インメモリ DbAsyncQueryProvider を作成して非同期クエリを処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-164">In order to use the async methods we need to create an in-memory DbAsyncQueryProvider to process the async query.</span></span> <span data-ttu-id="2fd9a-165">Moq を使用してクエリプロバイダーを設定することもできますが、コードでテストの二重実装を作成する方がはるかに簡単です。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-165">Whilst it would be possible to setup a query provider using Moq, it is much easier to create a test double implementation in code.</span></span> <span data-ttu-id="2fd9a-166">この実装のコードは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-166">The code for this implementation is as follows:</span></span>  

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

<span data-ttu-id="2fd9a-167">これで、非同期クエリプロバイダーが作成されました。ここでは、新しい GetAllBlogsAsync メソッドの単体テストを記述できます。</span><span class="sxs-lookup"><span data-stu-id="2fd9a-167">Now that we have an async query provider we can write a unit test for our new GetAllBlogsAsync method.</span></span>  

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
