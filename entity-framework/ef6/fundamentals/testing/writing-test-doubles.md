---
title: 独自のテストの倍精度のテスト (EF6)
author: divega
ms.date: 10/23/2016
ms.assetid: 16a8b7c0-2d23-47f4-9cc0-e2eb2e738ca3
ms.openlocfilehash: 3d8933fb5e17f8c01f3971495a1fcdb5b8cfab57
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413889"
---
# <a name="testing-with-your-own-test-doubles"></a><span data-ttu-id="e000a-102">独自のテストの倍精度でテストする</span><span class="sxs-lookup"><span data-stu-id="e000a-102">Testing with your own test doubles</span></span>
> [!NOTE]
> <span data-ttu-id="e000a-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e000a-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="e000a-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="e000a-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="e000a-105">アプリケーションのテストを作成するときは、多くの場合、データベースにヒットしないようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e000a-105">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="e000a-106">Entity Framework では、インメモリデータを使用するコンテキスト (テストで定義された動作) を作成することによってこれを実現できます。</span><span class="sxs-lookup"><span data-stu-id="e000a-106">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="e000a-107">テスト代替を作成するためのオプション</span><span class="sxs-lookup"><span data-stu-id="e000a-107">Options for creating test doubles</span></span>  

<span data-ttu-id="e000a-108">インメモリバージョンのコンテキストを作成するには、2つの異なる方法を使用できます。</span><span class="sxs-lookup"><span data-stu-id="e000a-108">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="e000a-109">**独自のテスト double を作成**する–この方法では、独自のコンテキストと dbsets のインメモリ実装を記述します。</span><span class="sxs-lookup"><span data-stu-id="e000a-109">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="e000a-110">これにより、クラスの動作を細かく制御できますが、適切な量のコードを記述して所有することができます。</span><span class="sxs-lookup"><span data-stu-id="e000a-110">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="e000a-111">**モックフレームワークを使用してテスト代替を作成する**–モックフレームワーク (moq など) を使用すると、コンテキストのインメモリ実装と、実行時に動的に作成される設定を使用できます。</span><span class="sxs-lookup"><span data-stu-id="e000a-111">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of you context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="e000a-112">この記事では、独自のテスト double を作成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e000a-112">This article will deal with creating your own test double.</span></span> <span data-ttu-id="e000a-113">モックフレームワークの使用方法の詳細について[は、「モックフレームワークを使用したテスト](mocking.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e000a-113">For information on using a mocking framework see [Testing with a Mocking Framework](mocking.md).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="e000a-114">EF6 バージョンでのテスト</span><span class="sxs-lookup"><span data-stu-id="e000a-114">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="e000a-115">この記事に示されているコードは、EF6 と互換性があります。</span><span class="sxs-lookup"><span data-stu-id="e000a-115">The code shown in this article is compatible with EF6.</span></span> <span data-ttu-id="e000a-116">EF5 以前のバージョンでのテストについて[は、「偽のコンテキストを使用したテスト](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e000a-116">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="e000a-117">EF のメモリ内テストの2倍の制限事項</span><span class="sxs-lookup"><span data-stu-id="e000a-117">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="e000a-118">メモリ内テストの double は、EF を使用するアプリケーションのビットの単体テストレベルを提供するのに適した方法です。</span><span class="sxs-lookup"><span data-stu-id="e000a-118">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="e000a-119">ただし、これを行う場合は、LINQ to Objects を使用してインメモリデータに対してクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e000a-119">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="e000a-120">これにより、EF の LINQ プロバイダー (LINQ to Entities) を使用して、データベースに対して実行される SQL にクエリを変換する場合と動作が異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="e000a-120">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="e000a-121">このような違いの一例として、関連データの読み込みがあります。</span><span class="sxs-lookup"><span data-stu-id="e000a-121">One example of such a difference is loading related data.</span></span> <span data-ttu-id="e000a-122">それぞれが関連する投稿を持つ一連のブログを作成した場合、メモリ内データを使用すると、関連する投稿は常に各ブログに読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e000a-122">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="e000a-123">ただし、データベースに対して実行する場合は、Include メソッドを使用した場合にのみデータが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e000a-123">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="e000a-124">このため、アプリケーションがデータベースに対して正しく動作するようにするには、(単体テストに加えて) 何らかのレベルのエンドツーエンドテストを常に含めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e000a-124">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="e000a-125">この記事では次のことを行います。</span><span class="sxs-lookup"><span data-stu-id="e000a-125">Following along with this article</span></span>  

<span data-ttu-id="e000a-126">この記事では、必要に応じて実行するために Visual Studio にコピーできる完全なコードリストを示します。</span><span class="sxs-lookup"><span data-stu-id="e000a-126">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="e000a-127">**単体テストプロジェクト**を作成するのが最も簡単です。 async を使用するセクションを完了するには、 **.NET Framework 4.5**を対象にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e000a-127">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="creating-a-context-interface"></a><span data-ttu-id="e000a-128">コンテキストインターフェイスの作成</span><span class="sxs-lookup"><span data-stu-id="e000a-128">Creating a context interface</span></span>  

<span data-ttu-id="e000a-129">EF モデルを使用するサービスのテストについて見ていきます。</span><span class="sxs-lookup"><span data-stu-id="e000a-129">We're going to look at testing a service that makes use of an EF model.</span></span> <span data-ttu-id="e000a-130">EF コンテキストをテストのためにメモリ内のバージョンに置き換えることができるようにするために、EF コンテキスト (およびそのメモリ内の二重) が実装するインターフェイスを定義します。</span><span class="sxs-lookup"><span data-stu-id="e000a-130">In order to be able to replace our EF context with an in-memory version for testing, we'll define an interface that our EF context (and it's in-memory double) will implement.</span></span>

<span data-ttu-id="e000a-131">テストするサービスでは、コンテキストの DbSet プロパティを使用してデータのクエリと変更を行います。また、SaveChanges を呼び出して、変更をデータベースにプッシュします。</span><span class="sxs-lookup"><span data-stu-id="e000a-131">The service we are going to test will query and modify data using the DbSet properties of our context and also call SaveChanges to push changes to the database.</span></span> <span data-ttu-id="e000a-132">これらのメンバーをインターフェイスに含めます。</span><span class="sxs-lookup"><span data-stu-id="e000a-132">So we're including these members on the interface.</span></span>  

``` csharp
using System.Data.Entity;

namespace TestingDemo
{
    public interface IBloggingContext
    {
        DbSet<Blog> Blogs { get; }
        DbSet<Post> Posts { get; }
        int SaveChanges();
    }
}
```  

## <a name="the-ef-model"></a><span data-ttu-id="e000a-133">EF モデル</span><span class="sxs-lookup"><span data-stu-id="e000a-133">The EF model</span></span>  

<span data-ttu-id="e000a-134">テストするサービスでは、Bの出力コンテキストとブログクラスおよび Post クラスで構成された EF モデルが使用されています。</span><span class="sxs-lookup"><span data-stu-id="e000a-134">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="e000a-135">このコードは、EF デザイナーによって生成されたか、Code First モデルである可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e000a-135">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext, IBloggingContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a><span data-ttu-id="e000a-136">EF デザイナーを使用したコンテキストインターフェイスの実装</span><span class="sxs-lookup"><span data-stu-id="e000a-136">Implementing the context interface with the EF Designer</span></span>  

<span data-ttu-id="e000a-137">このコンテキストでは、Ibのコンテキストインターフェイスが実装されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e000a-137">Note that our context implements the IBloggingContext interface.</span></span>  

<span data-ttu-id="e000a-138">Code First を使用している場合は、コンテキストを直接編集して、インターフェイスを実装できます。</span><span class="sxs-lookup"><span data-stu-id="e000a-138">If you are using Code First then you can edit your context directly to implement the interface.</span></span> <span data-ttu-id="e000a-139">EF デザイナーを使用している場合は、コンテキストを生成する T4 テンプレートを編集する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e000a-139">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="e000a-140">\<model_name\>を開きます。Edmx ファイルの下に入れ子になっている Context.tt ファイルで、次のコードのフラグメントを検索し、次に示すようにインターフェイスにを追加します。</span><span class="sxs-lookup"><span data-stu-id="e000a-140">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the interface as shown.</span></span>  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="e000a-141">テストするサービス</span><span class="sxs-lookup"><span data-stu-id="e000a-141">Service to be tested</span></span>  

<span data-ttu-id="e000a-142">インメモリテストの2倍のテストを実行するために、ブログサービスに対していくつかのテストを記述します。</span><span class="sxs-lookup"><span data-stu-id="e000a-142">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="e000a-143">このサービスは、新しいブログ (AddBlog) を作成し、名前で並べ替えられたすべてのブログ (GetAllBlogs) を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="e000a-143">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="e000a-144">GetAllBlogs だけでなく、名前で並べ替えられたすべてのブログを非同期に取得するメソッドも用意されています (GetAllBlogsAsync)。</span><span class="sxs-lookup"><span data-stu-id="e000a-144">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private IBloggingContext _context;

        public BlogService(IBloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = new Blog { Name = name, Url = url };
            _context.Blogs.Add(blog);
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

## <a name="creating-the-in-memory-test-doubles"></a><span data-ttu-id="e000a-145">メモリ内テストの倍精度浮動小数点数の作成</span><span class="sxs-lookup"><span data-stu-id="e000a-145">Creating the in-memory test doubles</span></span>  

<span data-ttu-id="e000a-146">実際の EF モデルとそれを使用できるサービスが用意できたので、テストに使用できるメモリ内テストの double を作成します。</span><span class="sxs-lookup"><span data-stu-id="e000a-146">Now that we have the real EF model and the service that can use it, it's time to create the in-memory test double that we can use for testing.</span></span> <span data-ttu-id="e000a-147">ここでは、コンテキストに対して TestContext test double を作成しました。</span><span class="sxs-lookup"><span data-stu-id="e000a-147">We've created a TestContext test double for our context.</span></span> <span data-ttu-id="e000a-148">テストの double では、実行するテストをサポートするために必要な動作を選択します。</span><span class="sxs-lookup"><span data-stu-id="e000a-148">In test doubles we get to choose the behavior we want in order to support the tests we are going to run.</span></span> <span data-ttu-id="e000a-149">この例では、SaveChanges が呼び出された回数をキャプチャするだけですが、テストするシナリオを検証するために必要なロジックを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e000a-149">In this example we're just capturing the number of times SaveChanges is called, but you can include whatever logic is needed to verify the scenario you are testing.</span></span>  

<span data-ttu-id="e000a-150">また、DbSet のメモリ内実装を提供する TestDbSet も作成しました。</span><span class="sxs-lookup"><span data-stu-id="e000a-150">We've also created a TestDbSet that provides an in-memory implementation of DbSet.</span></span> <span data-ttu-id="e000a-151">DbSet のすべてのメソッド (Find を除く) に完全な実装を提供していますが、テストシナリオで使用するメンバーのみを実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e000a-151">We've provided a complete implemention for all the methods on DbSet (except for Find), but you only need to implement the members that your test scenario will use.</span></span>  

<span data-ttu-id="e000a-152">TestDbSet は、非同期クエリを処理できるようにするために用意されている他のインフラストラクチャクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="e000a-152">TestDbSet makes use of some other infrastructure classes that we've included to ensure that async queries can be processed.</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Collections.ObjectModel;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class TestContext : IBloggingContext
    {
        public TestContext()
        {
            this.Blogs = new TestDbSet<Blog>();
            this.Posts = new TestDbSet<Post>();
        }

        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
        public int SaveChangesCount { get; private set; }
        public int SaveChanges()
        {
            this.SaveChangesCount++;
            return 1;
        }
    }

    public class TestDbSet<TEntity> : DbSet<TEntity>, IQueryable, IEnumerable<TEntity>, IDbAsyncEnumerable<TEntity>
        where TEntity : class
    {
        ObservableCollection<TEntity> _data;
        IQueryable _query;

        public TestDbSet()
        {
            _data = new ObservableCollection<TEntity>();
            _query = _data.AsQueryable();
        }

        public override TEntity Add(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Remove(TEntity item)
        {
            _data.Remove(item);
            return item;
        }

        public override TEntity Attach(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Create()
        {
            return Activator.CreateInstance<TEntity>();
        }

        public override TDerivedEntity Create<TDerivedEntity>()
        {
            return Activator.CreateInstance<TDerivedEntity>();
        }

        public override ObservableCollection<TEntity> Local
        {
            get { return _data; }
        }

        Type IQueryable.ElementType
        {
            get { return _query.ElementType; }
        }

        Expression IQueryable.Expression
        {
            get { return _query.Expression; }
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<TEntity>(_query.Provider); }
        }

        System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IEnumerator<TEntity> IEnumerable<TEntity>.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IDbAsyncEnumerator<TEntity> IDbAsyncEnumerable<TEntity>.GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<TEntity>(_data.GetEnumerator());
        }
    }

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

### <a name="implementing-find"></a><span data-ttu-id="e000a-153">検索の実装</span><span class="sxs-lookup"><span data-stu-id="e000a-153">Implementing Find</span></span>  

<span data-ttu-id="e000a-154">Find メソッドは、汎用的な方法で実装するのは困難です。</span><span class="sxs-lookup"><span data-stu-id="e000a-154">The Find method is difficult to implement in a generic fashion.</span></span> <span data-ttu-id="e000a-155">Find メソッドを使用するコードをテストする必要がある場合は、find をサポートする必要がある各エンティティ型に対してテスト DbSet を作成するのが最も簡単です。</span><span class="sxs-lookup"><span data-stu-id="e000a-155">If you need to test code that makes use of the Find method it is easiest to create a test DbSet for each of the entity types that need to support find.</span></span> <span data-ttu-id="e000a-156">その後、次に示すように、特定の種類のエンティティを検索するロジックを記述できます。</span><span class="sxs-lookup"><span data-stu-id="e000a-156">You can then write logic to find that particular type of entity, as shown below.</span></span>  

``` csharp
using System.Linq;

namespace TestingDemo
{
    class TestBlogDbSet : TestDbSet<Blog>
    {
        public override Blog Find(params object[] keyValues)
        {
            var id = (int)keyValues.Single();
            return this.SingleOrDefault(b => b.BlogId == id);
        }
    }
}
```  

## <a name="writing-some-tests"></a><span data-ttu-id="e000a-157">テストの作成</span><span class="sxs-lookup"><span data-stu-id="e000a-157">Writing some tests</span></span>  

<span data-ttu-id="e000a-158">テストを開始するために必要な操作はこれだけです。</span><span class="sxs-lookup"><span data-stu-id="e000a-158">That’s all we need to do to start testing.</span></span> <span data-ttu-id="e000a-159">次のテストでは、このコンテキストに基づいて TestContext とサービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e000a-159">The following test creates a TestContext and then a service based on this context.</span></span> <span data-ttu-id="e000a-160">このサービスは、AddBlog メソッドを使用して、新しいブログを作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e000a-160">The service is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="e000a-161">最後に、このテストでは、サービスがコンテキストの [ブログ] プロパティに新しいブログを追加し、コンテキストで SaveChanges を呼び出したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="e000a-161">Finally, the test verifies that the service added a new Blog to the context's Blogs property and called SaveChanges on the context.</span></span>  

<span data-ttu-id="e000a-162">これは、メモリ内テストの double でテストできるものの一例にすぎません。テストの2つのロジックを調整し、要件を満たすために検証を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e000a-162">This is just an example of the types of things you can test with an in-memory test double and you can adjust the logic of the test doubles and the verification to meet your requirements.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var context = new TestContext();

            var service = new BlogService(context);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            Assert.AreEqual(1, context.Blogs.Count());
            Assert.AreEqual("ADO.NET Blog", context.Blogs.Single().Name);
            Assert.AreEqual("http://blogs.msdn.com/adonet", context.Blogs.Single().Url);
            Assert.AreEqual(1, context.SaveChangesCount);
        }
    }
}
```  

<span data-ttu-id="e000a-163">テストのもう1つの例を次に示します。ここでは、クエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="e000a-163">Here is another example of a test - this time one that performs a query.</span></span> <span data-ttu-id="e000a-164">このテストは、ブログのプロパティにデータを含むテストコンテキストを作成することによって開始されます。データがアルファベット順ではないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e000a-164">The test starts by creating a test context with some data in its Blog property - note that the data is not in alphabetical order.</span></span> <span data-ttu-id="e000a-165">次に、テストコンテキストに基づいてブログサービスを作成し、GetAllBlogs から返されるデータが名前で並べ替えられるようにします。</span><span class="sxs-lookup"><span data-stu-id="e000a-165">We can then create a BlogService based on our test context and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

<span data-ttu-id="e000a-166">最後に、非同期メソッドを使用して、 [Testdbset](#creating-the-in-memory-test-doubles)に含まれている非同期インフラストラクチャが動作することを確認するテストをもう1つ作成します。</span><span class="sxs-lookup"><span data-stu-id="e000a-166">Finally, we'll write one more test that uses our async method to ensure that the async infrastructure we included in [TestDbSet](#creating-the-in-memory-test-doubles) is working.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Collections.Generic;
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
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  
