---
title: モック作成フレームワークの EF6 でのテスト
author: divega
ms.date: 10/23/2016
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
ms.openlocfilehash: 20799b55b2dffe27637c4fb84df06cee174e6dd9
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46284097"
---
# <a name="testing-with-a-mocking-framework"></a>モック作成フレームワークとテスト
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

アプリケーションのテストを記述する場合、データベースに達するを回避することが望ましいは多くの場合です。  Entity Framework からの使用によると、メモリ内のデータを使用するコンテキストを作成して – – テストで定義されている動作では、これを実現することができます。  

## <a name="options-for-creating-test-doubles"></a>テスト代替を作成するためのオプション  

コンテキストのメモリ内のバージョンを作成するために使用できる 2 つのさまざまな方法はあります。  

- **独自のテスト代替を作成する**– この方法では、コンテキストと DbSets の独自のインメモリ実装を記述します。 これにより、多数の制御、クラスの動作が記述して、コードの妥当な量を所有している必要があります。  
- **モック作成フレームワークを使用して、テスト代替を作成する**– (Moq) などのモック作成フレームワークを使用することが皆さんのインメモリ実装コンテキストとセットの実行時に動的に作成します。  

この記事ではモック作成フレームワークを使用してを処理します。 独自のテスト代替を作成するため、次を参照してください。 [、独自のテスト代替によるテスト](writing-test-doubles.md)します。  

モック作成フレームワークで EF を使用する方法を紹介するには、Moq を使用して行います。 Moq を取得する最も簡単な方法は、インストールには、 [NuGet からパッケージを Moq](http://nuget.org/packages/Moq/)します。  

## <a name="testing-with-pre-ef6-versions"></a>EF6 以前のバージョンでのテスト  

この記事で示されているシナリオでは、EF6 で DbSet に加えられたいくつかの変更に依存します。 EF5 と以前のバージョンをテストするため、次を参照してください。[偽のコンテキストでのテスト](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)します。  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>EF のメモリ内のテスト代替の制限事項  

メモリ内のテスト代替は、単体テスト、アプリケーションの EF を使用するビットのレベルのカバレッジを提供する優れた方法を指定できます。 ただし、これを行うときは、メモリ内のデータに対してクエリを実行するのに LINQ to Objects を使用しています。 これは、データベースに対して実行される SQL クエリに変換する EF の LINQ プロバイダー (LINQ to Entities) を使用するよりもの異なる動作になることができます。  

このような相違点の 1 つの例では、関連するデータを読み込んでいます。 一連のブログを作成する場合、そのそれぞれが関連する投稿をし、各ブログのため常に、関連する投稿に読み込まれるメモリ内のデータを使用する場合。 ただし、データベースに対して実行する場合は、Include メソッドを使用する場合は、データを読み込むのみされます。  

このため、常に一定レベルのエンド ツー エンドの (単体テスト) だけでなく、アプリケーションがデータベースに対して正しく動作させるテストを含めることをお勧めします。  

## <a name="following-along-with-this-article"></a>この記事と併せてに従ってください。  

この記事では、希望する場合は、作業を進めるに Visual Studio にコピーできます。 完全なコード リストを提供します。 作成する最も簡単な**単体テスト プロジェクト**必要がありますターゲットに **.NET Framework 4.5** async を使用して、次のセクションを完了します。  

## <a name="the-ef-model"></a>EF モデル  

テストをサービスでは、EF の使用により、モデル、BloggingContext とブログや投稿クラスから成ります。 このコードが EF デザイナーによって生成されたか、Code First モデルがあります。  

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

### <a name="virtual-dbset-properties-with-ef-designer"></a>EF デザイナーを使用した仮想の DbSet プロパティ  

コンテキストの DbSet プロパティは、仮想化がマークされるに注意してください。 これで、モック作成フレームワークとモック実装でこれらのプロパティのオーバーライドをコンテキストから派生させる。  

Code First を使用している場合するクラスを直接編集できます。 EF デザイナーを使用している場合が、コンテキストを生成する T4 テンプレートを編集する必要があります。 開き、 \<model_name\>します。Edmx ファイルの下に入れ子になっている.context.tt ファイルは、コードの次のフラグメントを検索しに示すように仮想キーワードを追加します。  

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

## <a name="service-to-be-tested"></a>サービスをテストするには  

ここでは BlogService のいくつかのテストを記述するのには、インメモリ テスト代替によるテストを示します。 サービスが新しいブログ (AddBlog) を作成できると、名前 (GetAllBlogs) によって順序付けられたすべてのブログを返します。 GetAllBlogs、だけでなく名前 (GetAllBlogsAsync) 順でのすべてのブログを非同期的に取得する方法も示します。  

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

## <a name="testing-non-query-scenarios"></a>非クエリのシナリオのテスト  

非クエリ メソッドのテストを開始するために必要なことです。 次のテストでは、Moq を使用して、コンテキストを作成します。 DbSet を作成し、\<ブログ\>つながり、コンテキストのブログのプロパティから返されるとします。 次に、コンテキストを使用して、– AddBlog メソッドを使用して新しいブログの作成に使用される、新しい BlogService を作成します。 最後に、テストは、サービスが新しいブログの追加し、コンテキストで SaveChanges を呼び出したことを確認します。  

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

## <a name="testing-query-scenarios"></a>クエリのシナリオのテスト  

Double、DbSet のテストに対してクエリを実行できるようにするためには、IQueryable の実装をセットアップする必要があります。 最初の手順がいくつかのメモリ内データを作成するには – 一覧を使用している\<ブログ\>します。 次に、作成コンテキストと DBSet\<ブログ\>し、linq to Objects プロバイダーの一覧で動作する委任がだけ –、DbSet の IQueryable 実装をワイヤ\<T\>します。  

名前によって GetAllBlogs から戻るデータが順序付けすることを確認および作成、テスト代替に基づいて BlogService できます。  

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

### <a name="testing-with-async-queries"></a>非同期クエリでのテスト

Entity Framework 6 では、非同期的にクエリの実行に使用できる拡張メソッドのセットが導入されました。 これらのメソッドの例には、ToListAsync、firstasync へ、ForEachAsync などが含まれます。  

Entity Framework のクエリは、LINQ の利用、ために、拡張メソッドは、IQueryable と IEnumerable に定義されます。 ただし、Entity Framework で使用するだけで設計されるため、Entity Framework クエリが LINQ クエリで使用しようとする場合、次のエラーを受け取る可能性があります。

> IDbAsyncEnumerable ソース IQueryable を実装していない{0}します。 Entity Framework の非同期操作の IDbAsyncEnumerable を実装するソースのみを使用できます。 詳細についてを参照してください[ http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068)します。  

非同期メソッドは、に対して EF クエリを実行している場合にのみサポート、ながらに対するメモリ内の実行、DbSet の double をテストするときに、単体テストで使用したい場合があります。  

非同期メソッドを使用するためには、非同期クエリを処理する、インメモリ DbAsyncQueryProvider を作成する必要があります。 Moq を使用してクエリ プロバイダーをセットアップすることができます中、は、コードでテスト ダブル実装を作成するはるかに簡単です。 この実装のコードは次のとおりです。  

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

非同期のクエリ プロバイダーが作成できた、新しい GetAllBlogsAsync メソッドの単体テストを記述できます。  

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
