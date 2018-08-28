---
title: 独自のテスト代替の EF6 でのテスト
author: divega
ms.date: 2016-10-23
ms.assetid: 16a8b7c0-2d23-47f4-9cc0-e2eb2e738ca3
ms.openlocfilehash: fa63c483e0a55b6cbd43382f68ab9592f3c1768b
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997218"
---
# <a name="testing-with-your-own-test-doubles"></a><span data-ttu-id="d034d-102">独自のテスト代替によるテスト</span><span class="sxs-lookup"><span data-stu-id="d034d-102">Testing with your own test doubles</span></span>
> [!NOTE]
> <span data-ttu-id="d034d-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="d034d-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="d034d-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="d034d-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="d034d-105">アプリケーションのテストを記述する場合、データベースに達するを回避することが望ましいは多くの場合です。</span><span class="sxs-lookup"><span data-stu-id="d034d-105">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="d034d-106">Entity Framework からの使用によると、メモリ内のデータを使用するコンテキストを作成して – – テストで定義されている動作では、これを実現することができます。</span><span class="sxs-lookup"><span data-stu-id="d034d-106">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="d034d-107">テスト代替を作成するためのオプション</span><span class="sxs-lookup"><span data-stu-id="d034d-107">Options for creating test doubles</span></span>  

<span data-ttu-id="d034d-108">コンテキストのメモリ内のバージョンを作成するために使用できる 2 つのさまざまな方法はあります。</span><span class="sxs-lookup"><span data-stu-id="d034d-108">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="d034d-109">**独自のテスト代替を作成する**– この方法では、コンテキストと DbSets の独自のインメモリ実装を記述します。</span><span class="sxs-lookup"><span data-stu-id="d034d-109">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="d034d-110">これにより、多数の制御、クラスの動作が記述して、コードの妥当な量を所有している必要があります。</span><span class="sxs-lookup"><span data-stu-id="d034d-110">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="d034d-111">**モック作成フレームワークを使用して、テスト代替を作成する**– (Moq) などのモック作成フレームワークを使用することが皆さんのインメモリ実装コンテキストとセットの実行時に動的に作成します。</span><span class="sxs-lookup"><span data-stu-id="d034d-111">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of you context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="d034d-112">この記事では、二重、独自のテストを作成するを扱うとしています。</span><span class="sxs-lookup"><span data-stu-id="d034d-112">This article will deal with creating your own test double.</span></span> <span data-ttu-id="d034d-113">モック作成フレームワークを使用するのには、次を参照してください。[テストでモック作成フレームワーク](mocking.md)します。</span><span class="sxs-lookup"><span data-stu-id="d034d-113">For information on using a mocking framework see [Testing with a Mocking Framework](mocking.md).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="d034d-114">EF6 以前のバージョンでのテスト</span><span class="sxs-lookup"><span data-stu-id="d034d-114">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="d034d-115">この記事で示したコードは、EF6 と互換性が。</span><span class="sxs-lookup"><span data-stu-id="d034d-115">The code shown in this article is compatible with EF6.</span></span> <span data-ttu-id="d034d-116">EF5 と以前のバージョンをテストするため、次を参照してください。[偽のコンテキストでのテスト](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)します。</span><span class="sxs-lookup"><span data-stu-id="d034d-116">For testing with EF5 and earlier version see [Testing with a Fake Context](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="d034d-117">EF のメモリ内のテスト代替の制限事項</span><span class="sxs-lookup"><span data-stu-id="d034d-117">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="d034d-118">メモリ内のテスト代替は、単体テスト、アプリケーションの EF を使用するビットのレベルのカバレッジを提供する優れた方法を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d034d-118">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="d034d-119">ただし、これを行うときは、メモリ内のデータに対してクエリを実行するのに LINQ to Objects を使用しています。</span><span class="sxs-lookup"><span data-stu-id="d034d-119">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="d034d-120">これは、データベースに対して実行される SQL クエリに変換する EF の LINQ プロバイダー (LINQ to Entities) を使用するよりもの異なる動作になることができます。</span><span class="sxs-lookup"><span data-stu-id="d034d-120">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="d034d-121">このような相違点の 1 つの例では、関連するデータを読み込んでいます。</span><span class="sxs-lookup"><span data-stu-id="d034d-121">One example of such a difference is loading related data.</span></span> <span data-ttu-id="d034d-122">一連のブログを作成する場合、そのそれぞれが関連する投稿をし、各ブログのため常に、関連する投稿に読み込まれるメモリ内のデータを使用する場合。</span><span class="sxs-lookup"><span data-stu-id="d034d-122">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="d034d-123">ただし、データベースに対して実行する場合は、Include メソッドを使用する場合は、データを読み込むのみされます。</span><span class="sxs-lookup"><span data-stu-id="d034d-123">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="d034d-124">このため、常に一定レベルのエンド ツー エンドの (単体テスト) だけでなく、アプリケーションがデータベースに対して正しく動作させるテストを含めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d034d-124">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="d034d-125">この記事と併せてに従ってください。</span><span class="sxs-lookup"><span data-stu-id="d034d-125">Following along with this article</span></span>  

<span data-ttu-id="d034d-126">この記事では、希望する場合は、作業を進めるに Visual Studio にコピーできます。 完全なコード リストを提供します。</span><span class="sxs-lookup"><span data-stu-id="d034d-126">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="d034d-127">作成する最も簡単な**単体テスト プロジェクト**必要がありますターゲットに **.NET Framework 4.5** async を使用して、次のセクションを完了します。</span><span class="sxs-lookup"><span data-stu-id="d034d-127">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="creating-a-context-interface"></a><span data-ttu-id="d034d-128">コンテキストのインターフェイスを作成します。</span><span class="sxs-lookup"><span data-stu-id="d034d-128">Creating a context interface</span></span>  

<span data-ttu-id="d034d-129">EF は、サービスをテストを見ていきますモデル。</span><span class="sxs-lookup"><span data-stu-id="d034d-129">We're going to look at testing a service that makes use of an EF model.</span></span> <span data-ttu-id="d034d-130">テスト用のメモリ内のバージョンで、EF コンテキストを交換できるようにするには、するためには、インターフェイスを定義します、EF コンテキスト (および、インメモリ倍) が imeplement をされます。</span><span class="sxs-lookup"><span data-stu-id="d034d-130">In order to be able to replace our EF context with an in-memory version for testing, we'll define an interface that our EF context (and it's in-memory double) will imeplement.</span></span>  

<span data-ttu-id="d034d-131">テストをここでは、サービスはクエリし、コンテキストの DbSet プロパティを使用してデータの変更し、データベースに変更をプッシュする SaveChanges を呼び出すこともします。</span><span class="sxs-lookup"><span data-stu-id="d034d-131">The service we are going to test will query and modify data using the DbSet properties of our context and also call SaveChanges to push changes to the database.</span></span> <span data-ttu-id="d034d-132">したがって、インターフェイスでこれらのメンバーを含めています。</span><span class="sxs-lookup"><span data-stu-id="d034d-132">So we're including these members on the interface.</span></span>  

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

## <a name="the-ef-model"></a><span data-ttu-id="d034d-133">EF モデル</span><span class="sxs-lookup"><span data-stu-id="d034d-133">The EF model</span></span>  

<span data-ttu-id="d034d-134">テストをサービスでは、EF の使用により、モデル、BloggingContext とブログや投稿クラスから成ります。</span><span class="sxs-lookup"><span data-stu-id="d034d-134">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="d034d-135">このコードが EF デザイナーによって生成されたか、Code First モデルがあります。</span><span class="sxs-lookup"><span data-stu-id="d034d-135">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a><span data-ttu-id="d034d-136">EF デザイナーを使用したコンテキストのインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="d034d-136">Implementing the context interface with the EF Designer</span></span>  

<span data-ttu-id="d034d-137">このコンテキストが IBloggingContext インターフェイスを実装することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d034d-137">Note that our context implements the IBloggingContext interface.</span></span>  

<span data-ttu-id="d034d-138">Code First を使用する場合は、インターフェイスを実装するには、直接のコンテキストを編集できます。</span><span class="sxs-lookup"><span data-stu-id="d034d-138">If you are using Code First then you can edit your context directly to implement the interface.</span></span> <span data-ttu-id="d034d-139">EF デザイナーを使用している場合が、コンテキストを生成する T4 テンプレートを編集する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d034d-139">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="d034d-140">開き、 \<model_name\>します。Edmx ファイルの下に入れ子になっている.context.tt ファイルは、次のコードのフラグメントを見つけてに示すように、インターフェイスに追加します。</span><span class="sxs-lookup"><span data-stu-id="d034d-140">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the interface as shown.</span></span>  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="d034d-141">サービスをテストするには</span><span class="sxs-lookup"><span data-stu-id="d034d-141">Service to be tested</span></span>  

<span data-ttu-id="d034d-142">ここでは BlogService のいくつかのテストを記述するのには、インメモリ テスト代替によるテストを示します。</span><span class="sxs-lookup"><span data-stu-id="d034d-142">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="d034d-143">サービスが新しいブログ (AddBlog) を作成できると、名前 (GetAllBlogs) によって順序付けられたすべてのブログを返します。</span><span class="sxs-lookup"><span data-stu-id="d034d-143">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="d034d-144">GetAllBlogs、だけでなく名前 (GetAllBlogsAsync) 順でのすべてのブログを非同期的に取得する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="d034d-144">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

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

<span data-ttu-id="d034d-145"><a name="creating-the-in-memory-test-doubles"/> ## インメモリ テストの作成を 2 倍に</span><span class="sxs-lookup"><span data-stu-id="d034d-145"><a name="creating-the-in-memory-test-doubles"/> ## Creating the in-memory test doubles</span></span>  

<span data-ttu-id="d034d-146">あるので、実際の EF モデルとそれを使用するサービス、二重のテストで使用できるメモリ内のテストの作成に時間を勧めします。</span><span class="sxs-lookup"><span data-stu-id="d034d-146">Now that we have the real EF model and the service that can use it, it's time to create the in-memory test double that we can use for testing.</span></span> <span data-ttu-id="d034d-147">作成しました。 TestContext テスト ダブル、コンテキスト。</span><span class="sxs-lookup"><span data-stu-id="d034d-147">We've created a TestContext test double for our context.</span></span> <span data-ttu-id="d034d-148">テスト代替の取得、テストをサポートするために想定どおりの動作を選択するを実行するいきます。</span><span class="sxs-lookup"><span data-stu-id="d034d-148">In test doubles we get to choose the behavior we want in order to support the tests we are going to run.</span></span> <span data-ttu-id="d034d-149">この例では、SaveChanges が呼び出された回数をキャプチャしましたいるだけですが、任意のロジックがテストするシナリオを確認するために必要なを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="d034d-149">In this example we're just capturing the number of times SaveChanges is called, but you can include whatever logic is needed to verify the scenario you are testing.</span></span>  

<span data-ttu-id="d034d-150">DbSet のインメモリ実装を提供する TestDbSet も作成しました。</span><span class="sxs-lookup"><span data-stu-id="d034d-150">We've also created a TestDbSet that provides an in-memory implementation of DbSet.</span></span> <span data-ttu-id="d034d-151">すべてのメソッドの完全な実装 (検索) を除く DbSet に用意されていますが、テスト シナリオで使用するメンバーを実装するだけで済みます。</span><span class="sxs-lookup"><span data-stu-id="d034d-151">We've provided a complete implemention for all the methods on DbSet (except for Find), but you only need to implement the members that your test scenario will use.</span></span>  

<span data-ttu-id="d034d-152">TestDbSet 含めました、非同期クエリを処理できるようにするいくつかのインフラストラクチャ クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="d034d-152">TestDbSet makes use of some other infrastructure classes that we've included to ensure that async queries can be processed.</span></span>  

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

### <a name="implementing-find"></a><span data-ttu-id="d034d-153">検索を実装します。</span><span class="sxs-lookup"><span data-stu-id="d034d-153">Implementing Find</span></span>  

<span data-ttu-id="d034d-154">Find メソッドでは、汎用的な方法で実装するために困難です。</span><span class="sxs-lookup"><span data-stu-id="d034d-154">The Find method is difficult to implement in a generic fashion.</span></span> <span data-ttu-id="d034d-155">テストする必要がある場合はそれぞれをサポートする必要があるエンティティ型の DbSet を検索するテストを作成する最も簡単な Find メソッドのコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="d034d-155">If you need to test code that makes use of the Find method it is easiest to create a test DbSet for each of the entity types that need to support find.</span></span> <span data-ttu-id="d034d-156">次に示すように、エンティティの特定の種類を検索するためのロジックを記述できます。</span><span class="sxs-lookup"><span data-stu-id="d034d-156">You can then write logic to find that particular type of entity, as shown below.</span></span>  

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

## <a name="writing-some-tests"></a><span data-ttu-id="d034d-157">いくつかのテストの記述</span><span class="sxs-lookup"><span data-stu-id="d034d-157">Writing some tests</span></span>  

<span data-ttu-id="d034d-158">テストを開始するために必要なことです。</span><span class="sxs-lookup"><span data-stu-id="d034d-158">That’s all we need to do to start testing.</span></span> <span data-ttu-id="d034d-159">次のテストでは、TestContext し、このコンテキストに基づくサービスを作成します。</span><span class="sxs-lookup"><span data-stu-id="d034d-159">The following test creates a TestContext and then a service based on this context.</span></span> <span data-ttu-id="d034d-160">サービスは – AddBlog メソッドを使用して新しいブログの作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d034d-160">The service is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="d034d-161">最後に、テストは、サービスがコンテキストのブログのプロパティに新しいブログを追加して、コンテキストで SaveChanges を呼び出したことを確認します。</span><span class="sxs-lookup"><span data-stu-id="d034d-161">Finally, the test verifies that the service added a new Blog to the context's Blogs property and called SaveChanges on the context.</span></span>  

<span data-ttu-id="d034d-162">これは二重のメモリ内のテストでテストすることができるものの種類のほんの一例であり、テスト代替とお客様の要件を満たすために検証ロジックを調整することができます。</span><span class="sxs-lookup"><span data-stu-id="d034d-162">This is just an example of the types of things you can test with an in-memory test double and you can adjust the logic of the test doubles and the verification to meet your requirements.</span></span>  

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

<span data-ttu-id="d034d-163">テストのいずれかのクエリを実行する現時点の別の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d034d-163">Here is another example of a test - this time one that performs a query.</span></span> <span data-ttu-id="d034d-164">テストは、そのブログのプロパティ - アルファベット順にデータがないので注意データを含むテスト コンテキストを作成して開始します。</span><span class="sxs-lookup"><span data-stu-id="d034d-164">The test starts by creating a test context with some data in its Blog property - note that the data is not in alphabetical order.</span></span> <span data-ttu-id="d034d-165">GetAllBlogs から戻るデータが名前によって順序付けられたことを確認したり、テスト コンテキストに基づく BlogService を作成できます。</span><span class="sxs-lookup"><span data-stu-id="d034d-165">We can then create a BlogService based on our test context and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

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

<span data-ttu-id="d034d-166">最後に、作成、非同期メソッドを使用して、非同期のインフラストラクチャに含めたことを確認する 1 つ以上のテスト[TestDbSet](#creating-the-in-memory-test-doubles)が動作します。</span><span class="sxs-lookup"><span data-stu-id="d034d-166">Finally, we'll write one more test that uses our async method to ensure that the async infrastructure we included in [TestDbSet](#creating-the-in-memory-test-doubles) is working.</span></span>  

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
