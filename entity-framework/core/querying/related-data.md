---
title: 読み込みに関連したデータの EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
ms.technology: entity-framework-core
uid: core/querying/related-data
ms.openlocfilehash: 5f1fb9376300739ab0e306d9d60e7ec71aa2d2e7
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2018
---
# <a name="loading-related-data"></a>関連データの読み込み

Entity Framework Core では、関連エンティティの読み込みをモデルで、ナビゲーション プロパティを使用することができます。 関連するデータの読み込みに使用される 3 つの一般的な O/RM パターンがあります。
* **一括読み込み**最初のクエリの一部として、データベースから、関連するデータが読み込まれたことを意味します。
* **明示的な読み込み**は後で、データベースから、関連するデータを明示的に読み込むことを意味します。
* **遅延読み込み**ナビゲーション プロパティにアクセスする場合、関連するデータが透過的にデータベースから読み込むことを意味します。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。

## <a name="eager-loading"></a>一括読み込み

使用することができます、`Include`クエリの結果に含まれる関連データを指定します。 次の例では、ブログの結果に返される必要があります、`Posts`プロパティとして、関連する投稿に設定されます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> Entity Framework Core は自動的に修正をするナビゲーション プロパティをコンテキストのインスタンスに以前に読み込まれたその他のエンティティです。 場合でも、明示的には、ナビゲーション プロパティのデータを含まない、いくつかの場合は、プロパティを作成してか、以前に読み込まれたすべての関連するエンティティ。


単一のクエリでは、複数のリレーションシップから関連するデータを含めることができます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>複数のレベルを含む

使用して関連するデータの複数のレベルを含むへのリレーションシップを通じてドリルダウンすることができます、`ThenInclude`メソッドです。 次の例では、すべてのブログの関連する投稿を各投稿の作成者を読み込みます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleThenInclude)]

> [!NOTE]  
> Visual Studio の現在のバージョンが不適切なコードの入力候補オプションを提供を正しい式を使用する場合に、構文エラーが発生フラグが付けられますが、`ThenInclude`メソッド コレクション ナビゲーション プロパティ。 これは、IntelliSense のバグの追跡の現象https://github.com/dotnet/roslyn/issues/8237です。 これらの見かけ上の構文エラーを無視するコードが正しいと正常にコンパイルできる限り安全です。 

複数の呼び出しをチェーンする`ThenInclude`関連データのレベルを含めてさらに続行します。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

これを同じクエリで複数のレベルと複数のルートから関連するデータを含めるにはすべてを組み合わせることができます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IncludeTree)]

いずれかが含まれるエンティティの複数の関連エンティティを追加することがあります。 たとえば、クエリを実行するときに`Blog`が含まれています、`Posts`両方を格納して、`Author`と`Tags`の`Posts`です。 これを行うには、ルートからのパスを含めるそれぞれを指定する必要があります。 たとえば、`Blog -> Posts -> Author`と`Blog -> Posts -> Tags`です。 表示される冗長な結合、EF の統合はほとんどの場合、結合 SQL を生成するときにありません。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="include-on-derived-types"></a>派生型では、します。

ナビゲーションを使用して、派生型でのみ定義から関連するデータを含めることができます`Include`と`ThenInclude`です。 

次のようなモデルを指定します。

```Csharp
    public class SchoolContext : DbContext
    {
        public DbSet<Person> People { get; set; }
        public DbSet<School> Schools { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
        }
    }

    public class Person
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class Student : Person
    {
        public School School { get; set; }
    }

    public class School
    {
        public int Id { get; set; }
        public string Name { get; set; }

        public List<Student> Students { get; set; }
    }
```

内容`School`受講者は、すべてのユーザーのナビゲーションを集中的に読み込めるパターンの番号を使用します。

- キャストを使用してください。
  ```Csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

- 使用して`as`演算子
  ```Csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

- オーバー ロードを使用して`Include`型のパラメーターを受け取る `string`
  ```Csharp
  context.People.Include("Student").ToList()
  ```

### <a name="ignored-includes"></a>無視が含まれています

不要になったクエリの開始とエンティティ型のインスタンスを返すように、クエリを変更する場合は、include 演算子は無視されます。

次の例では、include 演算子に基づいて、 `Blog`、ところが、`Select`演算子を使用して、匿名型を取得するクエリを変更します。 この場合、include 演算子がある影響しません。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IgnoredInclude)]

既定では、EF コアが、警告をログとは、演算子は無視されます。 参照してください[ログ](../miscellaneous/logging.md)ログ出力を表示する方法の詳細についてです。 スローするか、何もする include 演算子が無視される動作を変更できます。 では通常、コンテキストでのオプションを設定するときにこれは、 `DbContext.OnConfiguring`、または`Startup.cs`ASP.NET Core を使用している場合。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/ThrowOnIgnoredInclude/BloggingContext.cs#OnConfiguring)]

## <a name="explicit-loading"></a>明示的読み込み

> [!NOTE]  
> この機能は、EF コア 1.1 で導入されました。

使用して、ナビゲーション プロパティを明示的に読み込むことができます、 `DbContext.Entry(...)` API です。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#Eager)]

ナビゲーション プロパティは、関連エンティティを返す別のクエリを実行することによっても明示的に読み込むことができます。 変更の追跡が有効になっているかどうか、EF コアのすべてのエンティティは既に読み込まれているを参照する新たに読み込まれたエンティティのナビゲーション プロパティを設定を参照してくださいに既に読み込まれているエンティティのナビゲーション プロパティを設定が自動的にエンティティを読み込むときに、新たに読み込まれたエンティティです。

### <a name="querying-related-entities"></a>関連エンティティのクエリを実行します。

LINQ クエリを表すナビゲーション プロパティの内容を取得することもできます。

これにより、それらをメモリに読み込むことがなく、関連エンティティを aggregate 操作を実行しているなどの作業を行うことができます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

関連エンティティは、メモリに読み込まれるをフィルターすることもできます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>遅延読み込み

> [!NOTE]  
> この機能は、EF コア 2.1 で導入されました。

遅延読み込みを使用する最も簡単な方法は、インストールすることによって、 [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)パッケージと呼び出しを有効にすると`UseLazyLoadingProxies`です。 例えば:
```Csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```
または、AddDbContext を使用する場合。
```Csharp
    .AddDbContext<BloggingContext>(
        b => b.UseLazyLoadingProxies()
              .UseSqlServer(myConnectionString));
```
EF コアを遅延読み込みがされる--オーバーライド可能なナビゲーション プロパティに対して有効にする必要があります`virtual`およびクラスから継承されることができます。 たとえば、次のエンティティで、`Post.Blog`と`Blog.Posts`遅延読み込みされたナビゲーション プロパティになります。
```Csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```
### <a name="lazy-loading-without-proxies"></a>プロキシなしの遅延読み込み

遅延読み込みのプロキシを挿入することによって機能、 `ILazyLoader` 」の説明に従って、エンティティ サービス[エンティティ型のコンス トラクター](../modeling/constructors.md)です。 例えば:
```Csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader?.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader?.Load(this, ref _blog);
        set => _blog = value;
    }
}
```
これから継承するエンティティ型または仮想するナビゲーション プロパティを必要としないできで作成されたエンティティ インスタンス`new`遅延読み込み 1 回に、コンテキストにアタッチします。 ただしへの参照が必要、`ILazyLoader`サービスで、エンティティ型を EF コア アセンブリに結合します。 この EF コアを回避するを許可、`ILazyLoader.Load`代理人として挿入するメソッド。 例えば:
```Csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader?.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader?.Load(this, ref _blog);
        set => _blog = value;
    }
}
```
使用して上記のコード、`Load`ビット クリーナー デリゲートを使用できるようにする拡張メソッド。
```Csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```
> [!NOTE]  
> 遅延読み込みデリゲート コンス トラクターのパラメーターには、"lazyLoader"を呼び出す必要があります。 今後のリリースでこれが予定されて別の名前を使用する構成。

## <a name="related-data-and-serialization"></a>関連データとシリアル化

EF コアは自動的に修正をナビゲーション プロパティ、しまうサイクルで、オブジェクト グラフ内ためです。 たとえば、ブログを読み込みが関連付けられて投稿投稿のコレクションを参照するブログ オブジェクトになります。 これらの投稿の各ブログへの参照になります。

一部のシリアル化フレームワークでは、このようなサイクルは許可されません。 たとえば、Json.NET では、循環参照が発生した場合、次の例外がスローされます。

> Newtonsoft.Json.JsonSerializationException: 自己のループを型 'MyApplication.Models.Blog' と 'ブログ' プロパティの検出を参照します。

ASP.NET Core を使用している場合は、オブジェクト グラフ内で見つかったサイクルを無視する Json.NET を構成できます。 これには、`ConfigureServices(...)`メソッド`Startup.cs`です。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```
