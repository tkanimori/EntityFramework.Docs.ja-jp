---
title: 関連データの読み込み - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
ms.technology: entity-framework-core
uid: core/querying/related-data
ms.openlocfilehash: 5f1fb9376300739ab0e306d9d60e7ec71aa2d2e7
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2018
ms.locfileid: "31812652"
---
# <a name="loading-related-data"></a>関連データの読み込み

Entity Framework Core を使用すると、モデル内でナビゲーション プロパティを使用して関連エンティティを読み込むことができます。 関連データの読み込みに使用される共通の O/RM パターンが 3 つあります。
* **一括読み込み**。初期クエリの一部としてデータベースから関連データが読み込まれることを意味します。
* **明示的読み込み**。後でデータベースから明示的に関連データが読み込まれることを意味します。
* **遅延読み込み**。ナビゲーション プロパティにアクセスしたときに、データベースから透過的に関連データが読み込まれることを意味します。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。

## <a name="eager-loading"></a>一括読み込み

`Include` メソッドを使用して、クエリ結果に含める関連データを指定することができます。 次の例では、結果で返されるブログには `Posts` プロパティに関連する投稿が設定されます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> Entity Framework Core は、以前にコンテキスト インスタンスに読み込まれた他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。 そのため、ナビゲーション プロパティのデータを明示的に含めていない場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。


複数のリレーションシップの関連データを 1 つのクエリに含めることができます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>複数のレベルを含める

`ThenInclude` メソッドを使用して、リレーションシップをドリル ダウンし、複数のレベルの関連データを含めることができます。 次の例では、すべてのブログ、関連記事、および各投稿の作成者を読み込みます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleThenInclude)]

> [!NOTE]  
> Visual Studio の現在のバージョンでは、適切なコード補完オプションが提供されていないため、コレクションのナビゲーション プロパティのあとに `ThenInclude` メソッドを使用すると、正しい式に構文エラーのフラグが付けられる可能性があります。 これは https://github.com/dotnet/roslyn/issues/8237 で追跡された IntelliSense のバグの症状です。 コードが正しく、正常にコンパイルできる限り、このような見せかけ上の構文エラーは無視しても構いません。 

`ThenInclude` に対して複数の呼び出しを連鎖させて、さらなるレベルの関連データを含めることができます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

このすべてを組み合わせて、複数のレベルと複数のルートの関連データを同じクエリ内に含めることができます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IncludeTree)]

含まれているエンティティの 1 つについて複数の関連エンティティを含めることができます。 たとえば、`Blog` をクエリするときに、`Posts` を含め、さらに `Posts` の `Author` と `Tags` の両方を含めたい場合があります。 この場合、ルートから始まる各インクルード パスを指定する必要があります。 たとえば、`Blog -> Posts -> Author` と`Blog -> Posts -> Tags` です。 これで冗長的な結合を実現することにはならず、ほとんどの場合、SQL を生成するときに EF で結合は統合されます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="include-on-derived-types"></a>派生型に対するインクルード

`Include` と `ThenInclude` を使用して、派生型にのみ定義されているナビゲーションの関連データを含めることができます。 

次のモデルがあるとします。

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

生徒である全ユーザーの `School` ナビゲーションの内容を一括して読み込むには、いくつかのパターンを使用できます。

- キャストを使用する
  ```Csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

- `as` 演算子を使用する
  ```Csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

- 型 `string` のパラメーターを受け取る `Include` のオーバーロードを使用する
  ```Csharp
  context.People.Include("Student").ToList()
  ```

### <a name="ignored-includes"></a>無視されるインクルード

クエリが開始されたエンティティ型のインスタンスを返さないようにクエリを変更した場合、インクルード演算子は無視されます。

次の例では、インクルード演算子は `Blog` に基づいていますが、匿名型を返すようにクエリを変更するために `Select` 演算子が使用されています。 この場合、インクルード演算子には何の効果もありません。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IgnoredInclude)]

既定では、インクルード演算子が無視されると、EF Core では警告をログに記録します。 ログ記録の出力の表示に関する詳細については、[ログ記録](../miscellaneous/logging.md)に関するページを参照してください。 インクルード演算子が無視された場合の動作を、エラーをスローするか、または何もしないかに変更できます。 ASP.NET Core を使用している場合、通常は `DbContext.OnConfiguring` または `Startup.cs` でコンテキストのオプションを設定するときに、この変更が行われます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/ThrowOnIgnoredInclude/BloggingContext.cs#OnConfiguring)]

## <a name="explicit-loading"></a>明示的読み込み

> [!NOTE]  
> この機能は、EF Core 1.1 で導入されました。

`DbContext.Entry(...)` API を使用してナビゲーション プロパティを明示的に読み込むことができます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#Eager)]

また、関連エンティティを返す個別のクエリを実行することで、ナビゲーション プロパティを明示的に読み込むこともできます。 変更の追跡が有効な場合、エンティティを読み込むと、EF Core は、既に読み込まれているエンティティがあれば参照するように、新しく読み込まれたエンティティのナビゲーション プロパティを自動的に設定します。また、新しく読み込まれたエンティティを参照するように、既に読み込まれているエンティティのナビゲーション プロパティを自動的に設定します。

### <a name="querying-related-entities"></a>関連エンティティのクエリ

また、ナビゲーション プロパティの内容を表す LINQ クエリを取得することもできます。

そのため、関連エンティティをメモリに読み込まずに集計演算子を実行するなどの操作を行うことができます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

関連エンティティがメモリに読み込まれるようにフィルター処理することもできます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>遅延読み込み

> [!NOTE]  
> この機能は、EF Core 2.1 で導入されました。

遅延読み込みを使用するには、[Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) パッケージをインストールし、`UseLazyLoadingProxies` を呼び出して有効にする方法が最も簡単です。 例:
```Csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```
または、AddDbContext を使用する場合:
```Csharp
    .AddDbContext<BloggingContext>(
        b => b.UseLazyLoadingProxies()
              .UseSqlServer(myConnectionString));
```
EF Core は、オーバーライド可能なナビゲーション プロパティの場合に遅延読み込みを有効にします。つまり `virtual` であり、継承可能なクラスの ナビゲーション プロパティである必要があります。 たとえば、次のエンティティでは、`Post.Blog` および `Blog.Posts` ナビゲーション プロパティの遅延読み込みが実行されます。
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
### <a name="lazy-loading-without-proxies"></a>プロキシを使用しない遅延読み込み

遅延読み込みプロキシは、[エンティティ型コンストラクター](../modeling/constructors.md)に関するページで説明されているように、エンティティに `ILazyLoader` サービスを挿入することで機能します。 例:
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
この場合、エンティティ型を継承したり、ナビゲーション プロパティを仮想にしたりする必要はありません。コンテキストにアタッチされたときに、`new` で作成されたエンティティ インスタンスの遅延読み込みを実行することができます。 ただし、エンティティ型を EF Core アセンブリに結合する `ILazyLoader` サービスを参照する必要があります。 これを回避するため、EF Core では `ILazyLoader.Load` メソッドをデリゲートとして挿入することができます。 例:
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
上記のコードでは、デリゲートの使用をビット クリーナーにするために、拡張メソッド `Load` を使用しています。
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
> 遅延読み込みデリゲートのコンストラクター パラメーターは、"lazyLoader" と指定する必要があります。 これに別の名前を使用する構成が今後のリリースで計画されています。

## <a name="related-data-and-serialization"></a>関連データとシリアル化

EF Core はナビゲーション プロパティを自動的に修正するので、最終的にオブジェクト グラフの循環が生じる可能性があります。 たとえば、ブログとその関連する投稿を読み込むと、投稿のコレクションを参照するブログ オブジェクトになります。 これらの各投稿には元のブログへの参照が含まれることになります。

一部のシリアル化フレームワークでは、このような循環は許可されていません。 たとえば、Json.NET では、循環が発生した場合に次の例外をスローします。

> Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.

ASP.NET Core を使用している場合は、オブジェクト グラフで見つかった循環を無視するように Json.NET を構成できます。 この操作は、`Startup.cs` の `ConfigureServices(...)` メソッドで実行します。

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
