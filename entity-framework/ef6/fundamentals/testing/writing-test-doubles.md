---
title: 独自のテスト代替の EF6 でのテスト
author: divega
ms.date: 10/23/2016
ms.assetid: 16a8b7c0-2d23-47f4-9cc0-e2eb2e738ca3
ms.openlocfilehash: 9db56e28cd89084fece36c3e5a2c1b4495991d01
ms.sourcegitcommit: 645785187ae23ddf7d7b0642c7a4da5ffb0c7f30
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419732"
---
# <a name="testing-with-your-own-test-doubles"></a>独自のテスト代替によるテスト
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

アプリケーションのテストを記述する場合、データベースに達するを回避することが望ましいは多くの場合です。  Entity Framework からの使用によると、メモリ内のデータを使用するコンテキストを作成して – – テストで定義されている動作では、これを実現することができます。  

## <a name="options-for-creating-test-doubles"></a>テスト代替を作成するためのオプション  

コンテキストのメモリ内のバージョンを作成するために使用できる 2 つのさまざまな方法はあります。  

- **独自のテスト代替を作成する**– この方法では、コンテキストと DbSets の独自のインメモリ実装を記述します。 これにより、多数の制御、クラスの動作が記述して、コードの妥当な量を所有している必要があります。  
- **モック作成フレームワークを使用して、テスト代替を作成する**– (Moq) などのモック作成フレームワークを使用することが皆さんのインメモリ実装コンテキストとセットの実行時に動的に作成します。  

この記事では、二重、独自のテストを作成するを扱うとしています。 モック作成フレームワークを使用するのには、次を参照してください。[テストでモック作成フレームワーク](mocking.md)します。  

## <a name="testing-with-pre-ef6-versions"></a>EF6 以前のバージョンでのテスト  

この記事で示したコードは、EF6 と互換性が。 EF5 と以前のバージョンをテストするため、次を参照してください。[偽のコンテキストでのテスト](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)します。  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>EF のメモリ内のテスト代替の制限事項  

メモリ内のテスト代替は、単体テスト、アプリケーションの EF を使用するビットのレベルのカバレッジを提供する優れた方法を指定できます。 ただし、これを行うときは、メモリ内のデータに対してクエリを実行するのに LINQ to Objects を使用しています。 これは、データベースに対して実行される SQL クエリに変換する EF の LINQ プロバイダー (LINQ to Entities) を使用するよりもの異なる動作になることができます。  

このような相違点の 1 つの例では、関連するデータを読み込んでいます。 一連のブログを作成する場合、そのそれぞれが関連する投稿をし、各ブログのため常に、関連する投稿に読み込まれるメモリ内のデータを使用する場合。 ただし、データベースに対して実行する場合は、Include メソッドを使用する場合は、データを読み込むのみされます。  

このため、常に一定レベルのエンド ツー エンドの (単体テスト) だけでなく、アプリケーションがデータベースに対して正しく動作させるテストを含めることをお勧めします。  

## <a name="following-along-with-this-article"></a>この記事と併せてに従ってください。  

この記事では、希望する場合は、作業を進めるに Visual Studio にコピーできます。 完全なコード リストを提供します。 作成する最も簡単な**単体テスト プロジェクト**必要がありますターゲットに **.NET Framework 4.5** async を使用して、次のセクションを完了します。  

## <a name="creating-a-context-interface"></a>コンテキストのインターフェイスを作成します。  

EF は、サービスをテストを見ていきますモデル。 テスト用のメモリ内のバージョンで、EF コンテキストを交換できるようにするには、するには、EF コンテキスト (およびメモリの二重) を実装するインターフェイスを定義します。

テストをここでは、サービスはクエリし、コンテキストの DbSet プロパティを使用してデータの変更し、データベースに変更をプッシュする SaveChanges を呼び出すこともします。 したがって、インターフェイスでこれらのメンバーを含めています。  

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

テストをサービスでは、EF の使用により、モデル、BloggingContext とブログや投稿クラスから成ります。 このコードが EF デザイナーによって生成されたか、Code First モデルがあります。  

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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a>EF デザイナーを使用したコンテキストのインターフェイスを実装します。  

このコンテキストが IBloggingContext インターフェイスを実装することに注意してください。  

Code First を使用する場合は、インターフェイスを実装するには、直接のコンテキストを編集できます。 EF デザイナーを使用している場合が、コンテキストを生成する T4 テンプレートを編集する必要があります。 開き、 \<model_name\>します。Edmx ファイルの下に入れ子になっている.context.tt ファイルは、次のコードのフラグメントを見つけてに示すように、インターフェイスに追加します。  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
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

<a name="creating-the-in-memory-test-doubles"/> ## インメモリ テストの作成を 2 倍に  

あるので、実際の EF モデルとそれを使用するサービス、二重のテストで使用できるメモリ内のテストの作成に時間を勧めします。 作成しました。 TestContext テスト ダブル、コンテキスト。 テスト代替の取得、テストをサポートするために想定どおりの動作を選択するを実行するいきます。 この例では、SaveChanges が呼び出された回数をキャプチャしましたいるだけですが、任意のロジックがテストするシナリオを確認するために必要なを含めることができます。  

DbSet のインメモリ実装を提供する TestDbSet も作成しました。 すべてのメソッドの完全な実装 (検索) を除く DbSet に用意されていますが、テスト シナリオで使用するメンバーを実装するだけで済みます。  

TestDbSet 含めました、非同期クエリを処理できるようにするいくつかのインフラストラクチャ クラスを使用します。  

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

### <a name="implementing-find"></a>検索を実装します。  

Find メソッドでは、汎用的な方法で実装するために困難です。 テストする必要がある場合はそれぞれをサポートする必要があるエンティティ型の DbSet を検索するテストを作成する最も簡単な Find メソッドのコードを使用します。 次に示すように、エンティティの特定の種類を検索するためのロジックを記述できます。  

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

## <a name="writing-some-tests"></a>いくつかのテストの記述  

テストを開始するために必要なことです。 次のテストでは、TestContext し、このコンテキストに基づくサービスを作成します。 サービスは – AddBlog メソッドを使用して新しいブログの作成に使用されます。 最後に、テストは、サービスがコンテキストのブログのプロパティに新しいブログを追加して、コンテキストで SaveChanges を呼び出したことを確認します。  

これは二重のメモリ内のテストでテストすることができるものの種類のほんの一例であり、テスト代替とお客様の要件を満たすために検証ロジックを調整することができます。  

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

テストのいずれかのクエリを実行する現時点の別の例を次に示します。 テストは、そのブログのプロパティ - アルファベット順にデータがないので注意データを含むテスト コンテキストを作成して開始します。 GetAllBlogs から戻るデータが名前によって順序付けられたことを確認したり、テスト コンテキストに基づく BlogService を作成できます。  

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

最後に、作成、非同期メソッドを使用して、非同期のインフラストラクチャに含めたことを確認する 1 つ以上のテスト[TestDbSet](#creating-the-in-memory-test-doubles)が動作します。  

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
