---
title: 独自のテストの倍精度のテスト (EF6)
description: Entity Framework 6 で独自のテスト double を使用したテスト
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/testing/writing-test-doubles
ms.openlocfilehash: 575007c5568049e6508d1bd6435597a1acd843ab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070433"
---
# <a name="testing-with-your-own-test-doubles"></a>独自のテストの倍精度でテストする
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

アプリケーションのテストを作成するときは、多くの場合、データベースにヒットしないようにすることをお勧めします。  Entity Framework では、インメモリデータを使用するコンテキスト (テストで定義された動作) を作成することによってこれを実現できます。  

## <a name="options-for-creating-test-doubles"></a>テスト代替を作成するためのオプション  

インメモリバージョンのコンテキストを作成するには、2つの異なる方法を使用できます。  

- **独自のテスト double を作成** する–この方法では、独自のコンテキストと dbsets のインメモリ実装を記述します。 これにより、クラスの動作を細かく制御できますが、適切な量のコードを記述して所有することができます。  
- **モックフレームワークを使用してテスト代替を作成する** –モックフレームワーク (moq など) を使用すると、コンテキストのインメモリ実装と、実行時に動的に作成される設定を使用できます。  

この記事では、独自のテスト double を作成する方法について説明します。 モックフレームワークの使用方法の詳細について [は、「モックフレームワークを使用したテスト](xref:ef6/fundamentals/testing/mocking)」を参照してください。  

## <a name="testing-with-pre-ef6-versions"></a>EF6 バージョンでのテスト  

この記事に示されているコードは、EF6 と互換性があります。 EF5 以前のバージョンでのテストについて [は、「偽のコンテキストを使用したテスト](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)」を参照してください。  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>EF のメモリ内テストの2倍の制限事項  

メモリ内テストの double は、EF を使用するアプリケーションのビットの単体テストレベルを提供するのに適した方法です。 ただし、これを行う場合は、LINQ to Objects を使用してインメモリデータに対してクエリを実行します。 これにより、EF の LINQ プロバイダー (LINQ to Entities) を使用して、データベースに対して実行される SQL にクエリを変換する場合と動作が異なる場合があります。  

このような違いの一例として、関連データの読み込みがあります。 それぞれが関連する投稿を持つ一連のブログを作成した場合、メモリ内データを使用すると、関連する投稿は常に各ブログに読み込まれます。 ただし、データベースに対して実行する場合は、Include メソッドを使用した場合にのみデータが読み込まれます。  

このため、アプリケーションがデータベースに対して正しく動作するようにするには、(単体テストに加えて) 何らかのレベルのエンドツーエンドテストを常に含めることをお勧めします。  

## <a name="following-along-with-this-article"></a>この記事では次のことを行います。  

この記事では、必要に応じて実行するために Visual Studio にコピーできる完全なコードリストを示します。 **単体テストプロジェクト**を作成するのが最も簡単です。 async を使用するセクションを完了するには、 **.NET Framework 4.5**を対象にする必要があります。  

## <a name="creating-a-context-interface"></a>コンテキストインターフェイスの作成  

EF モデルを使用するサービスのテストについて見ていきます。 EF コンテキストをテストのためにメモリ内のバージョンに置き換えることができるようにするために、EF コンテキスト (およびそのメモリ内の二重) が実装するインターフェイスを定義します。

テストするサービスでは、コンテキストの DbSet プロパティを使用してデータのクエリと変更を行います。また、SaveChanges を呼び出して、変更をデータベースにプッシュします。 これらのメンバーをインターフェイスに含めます。  

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

## <a name="the-ef-model"></a>EF モデル  

テストするサービスでは、Bの出力コンテキストとブログクラスおよび Post クラスで構成された EF モデルが使用されています。 このコードは、EF デザイナーによって生成されたか、Code First モデルである可能性があります。  

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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a>EF デザイナーを使用したコンテキストインターフェイスの実装  

このコンテキストでは、Ibのコンテキストインターフェイスが実装されていることに注意してください。  

Code First を使用している場合は、コンテキストを直接編集して、インターフェイスを実装できます。 EF デザイナーを使用している場合は、コンテキストを生成する T4 テンプレートを編集する必要があります。 を開き \<model_name\> ます。Edmx ファイルの下に入れ子になっている Context.tt ファイルで、次のコードのフラグメントを検索し、次に示すようにインターフェイスにを追加します。  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
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

## <a name="creating-the-in-memory-test-doubles"></a>メモリ内テストの倍精度浮動小数点数の作成  

実際の EF モデルとそれを使用できるサービスが用意できたので、テストに使用できるメモリ内テストの double を作成します。 ここでは、コンテキストに対して TestContext test double を作成しました。 テストの double では、実行するテストをサポートするために必要な動作を選択します。 この例では、SaveChanges が呼び出された回数をキャプチャするだけですが、テストするシナリオを検証するために必要なロジックを含めることができます。  

また、DbSet のメモリ内実装を提供する TestDbSet も作成しました。 DbSet のすべてのメソッド (Find を除く) に完全な実装を提供していますが、テストシナリオで使用するメンバーのみを実装する必要があります。  

TestDbSet は、非同期クエリを処理できるようにするために用意されている他のインフラストラクチャクラスを使用します。  

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

### <a name="implementing-find"></a>検索の実装  

Find メソッドは、汎用的な方法で実装するのは困難です。 Find メソッドを使用するコードをテストする必要がある場合は、find をサポートする必要がある各エンティティ型に対してテスト DbSet を作成するのが最も簡単です。 その後、次に示すように、特定の種類のエンティティを検索するロジックを記述できます。  

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

## <a name="writing-some-tests"></a>テストの作成  

テストを開始するために必要な操作はこれだけです。 次のテストでは、このコンテキストに基づいて TestContext とサービスを作成します。 このサービスは、AddBlog メソッドを使用して、新しいブログを作成するために使用されます。 最後に、このテストでは、サービスがコンテキストの [ブログ] プロパティに新しいブログを追加し、コンテキストで SaveChanges を呼び出したことを確認します。  

これは、メモリ内テストの double でテストできるものの一例にすぎません。テストの2つのロジックを調整し、要件を満たすために検証を行うことができます。  

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

テストのもう1つの例を次に示します。ここでは、クエリを実行します。 このテストは、ブログのプロパティにデータを含むテストコンテキストを作成することによって開始されます。データがアルファベット順ではないことに注意してください。 次に、テストコンテキストに基づいてブログサービスを作成し、GetAllBlogs から返されるデータが名前で並べ替えられるようにします。  

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

最後に、非同期メソッドを使用して、 [Testdbset](#creating-the-in-memory-test-doubles) に含まれている非同期インフラストラクチャが動作することを確認するテストをもう1つ作成します。  

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
