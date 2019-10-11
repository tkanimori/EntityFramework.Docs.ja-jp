---
title: モックフレームワークを使用したテスト-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
ms.openlocfilehash: 790e077c5b30c4a68a96b3c1a99b40893b2bbe55
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181571"
---
# <a name="testing-with-a-mocking-framework"></a>モックフレームワークを使用したテスト
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

アプリケーションのテストを作成するときは、多くの場合、データベースにヒットしないようにすることをお勧めします。  Entity Framework では、インメモリデータを使用するコンテキスト (テストで定義された動作) を作成することによってこれを実現できます。  

## <a name="options-for-creating-test-doubles"></a>テスト代替を作成するためのオプション  

インメモリバージョンのコンテキストを作成するには、2つの異なる方法を使用できます。  

- **独自のテスト double を作成**する–この方法では、独自のコンテキストと dbsets のインメモリ実装を記述します。 これにより、クラスの動作を細かく制御できますが、適切な量のコードを記述して所有することができます。  
- **モックフレームワークを使用したテスト代替の作成**–モックフレームワーク (moq など) を使用すると、コンテキストのインメモリ実装と、実行時に動的に作成される設定を使用できます。  

この記事では、モックフレームワークの使用について説明します。 独自のテスト double を作成する場合は、「テスト[を独自](writing-test-doubles.md)に作成する double」を参照してください。  

モックフレームワークで EF を使用する方法を示すために、Moq を使用します。 Moq を取得する最も簡単な方法は、 [NuGet から moq パッケージ](https://nuget.org/packages/Moq/)をインストールすることです。  

## <a name="testing-with-pre-ef6-versions"></a>EF6 バージョンでのテスト  

この記事に示されているシナリオは、EF6 で DbSet に加えられたいくつかの変更に依存しています。 EF5 以前のバージョンでのテストについて[は、「偽のコンテキストを使用したテスト](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)」を参照してください。  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>EF のメモリ内テストの2倍の制限事項  

メモリ内テストの double は、EF を使用するアプリケーションのビットの単体テストレベルを提供するのに適した方法です。 ただし、これを行う場合は、LINQ to Objects を使用してインメモリデータに対してクエリを実行します。 これにより、EF の LINQ プロバイダー (LINQ to Entities) を使用して、データベースに対して実行される SQL にクエリを変換する場合と動作が異なる場合があります。  

このような違いの一例として、関連データの読み込みがあります。 それぞれが関連する投稿を持つ一連のブログを作成した場合、メモリ内データを使用すると、関連する投稿は常に各ブログに読み込まれます。 ただし、データベースに対して実行する場合は、Include メソッドを使用した場合にのみデータが読み込まれます。  

このため、アプリケーションがデータベースに対して正しく動作するようにするには、(単体テストに加えて) 何らかのレベルのエンドツーエンドテストを常に含めることをお勧めします。  

## <a name="following-along-with-this-article"></a>この記事では次のことを行います。  

この記事では、必要に応じて実行するために Visual Studio にコピーできる完全なコードリストを示します。 **単体テストプロジェクト**を作成するのが最も簡単です。 async を使用するセクションを完了するには、 **.NET Framework 4.5**を対象にする必要があります。  

## <a name="the-ef-model"></a>EF モデル  

テストするサービスでは、Bの出力コンテキストとブログクラスおよび Post クラスで構成された EF モデルが使用されています。 このコードは、EF デザイナーによって生成されたか、Code First モデルである可能性があります。  

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

### <a name="virtual-dbset-properties-with-ef-designer"></a>EF デザイナーを使用した仮想 DbSet プロパティ  

コンテキストの DbSet プロパティは virtual とマークされていることに注意してください。 これにより、モックフレームワークをコンテキストから派生させ、モック実装でこれらのプロパティをオーバーライドできます。  

Code First を使用している場合は、クラスを直接編集できます。 EF デザイナーを使用している場合は、コンテキストを生成する T4 テンプレートを編集する必要があります。 @No__t-0model_name @ no__t-1 を開きます。Edmx ファイルの下に入れ子になっている Context.tt ファイル。次のコードのフラグメントを検索し、次に示すように仮想キーワードを追加します。  

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

## <a name="service-to-be-tested"></a>テストするサービス  

インメモリテストの2倍のテストを実行するために、ブログサービスに対していくつかのテストを記述します。 このサービスは、新しいブログ (AddBlog) を作成し、名前で並べ替えられたすべてのブログ (GetAllBlogs) を返すことができます。 GetAllBlogs だけでなく、名前で並べ替えられたすべてのブログを非同期に取得するメソッドも用意されています (GetAllBlogsAsync)。  

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

## <a name="testing-non-query-scenarios"></a>クエリ以外のシナリオのテスト  

クエリ以外のメソッドのテストを開始するために必要な操作はこれだけです。 次のテストでは、Moq を使用してコンテキストを作成します。 次に、DbSet @ no__t-0Blog @ no__t-1 を作成し、コンテキストのブログプロパティから返されるように配線します。 次に、コンテキストを使用して新しいブログサービスを作成します。このサービスは、AddBlog メソッドを使用して新しいブログを作成するために使用されます。 最後に、このテストでは、サービスが新しいブログを追加し、コンテキストに SaveChanges という名前を追加したことを確認します。  

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

## <a name="testing-query-scenarios"></a>クエリシナリオのテスト  

DbSet テスト double に対してクエリを実行できるようにするには、IQueryable の実装を設定する必要があります。 最初の手順では、メモリ内のデータをいくつか作成します。 @ no__t-0Blog @ no__t-1 というリストを使用します。 次に、コンテキストと DBSet @ no__t-0Blog @ no__t を作成し、DbSet の IQueryable 実装を接続します。これらは、List @ no__t-2T @ no__t で動作する LINQ to Objects プロバイダーに委任するだけです。  

次に、テスト double に基づいてブログサービスを作成し、GetAllBlogs から返されるデータが名前で並べ替えられるようにします。  

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

### <a name="testing-with-async-queries"></a>非同期クエリを使用したテスト

Entity Framework 6 では、非同期的にクエリを実行するために使用できる一連の拡張メソッドが導入されました。 これらのメソッドの例には、ToListAsync、FirstAsync、ForEachAsync などがあります。  

Entity Framework クエリでは LINQ が使用されるため、拡張メソッドは IQueryable と IEnumerable で定義されます。 ただし、これらは Entity Framework と共に使用するように設計されているため、Entity Framework クエリではない LINQ クエリで使用しようとすると、次のエラーが発生することがあります。

> ソース IQueryable は IDbAsyncEnumerable @ no__t-0 を実装していません。 Entity Framework 非同期操作に使用できるのは、IDbAsyncEnumerable を実装するソースだけです。 詳細については[、 http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068)を参照してください。  

非同期メソッドは、EF クエリに対して実行する場合にのみサポートされますが、DbSet のメモリ内テスト double に対して実行するときに、単体テストで使用することもできます。  

非同期メソッドを使用するには、インメモリ DbAsyncQueryProvider を作成して非同期クエリを処理する必要があります。 Moq を使用してクエリプロバイダーを設定することもできますが、コードでテストの二重実装を作成する方がはるかに簡単です。 この実装のコードは次のとおりです。  

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

これで、非同期クエリプロバイダーが作成されました。ここでは、新しい GetAllBlogsAsync メソッドの単体テストを記述できます。  

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
