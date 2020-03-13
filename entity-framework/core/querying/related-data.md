---
title: 関連データの読み込み - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 915aaa41beb495a046f2d6260e9c3b174d5f3031
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413721"
---
# <a name="loading-related-data"></a>関連データの読み込み

Entity Framework Core を使用すると、モデル内でナビゲーション プロパティを使用して関連エンティティを読み込むことができます。 関連データの読み込みに使用される共通の O/RM パターンが 3 つあります。

* **一括読み込み**。初期クエリの一部としてデータベースから関連データが読み込まれることを意味します。
* **明示的読み込み**。後でデータベースから明示的に関連データが読み込まれることを意味します。
* **遅延読み込み**。ナビゲーション プロパティにアクセスしたときに、データベースから透過的に関連データが読み込まれることを意味します。

> [!TIP]  
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。

## <a name="eager-loading"></a>一括読み込み

`Include` メソッドを使用して、クエリ結果に含める関連データを指定することができます。 次の例では、結果で返されるブログには `Posts` プロパティに関連する投稿が設定されます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> Entity Framework Core は、以前にコンテキスト インスタンスに読み込まれた他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。 そのため、ナビゲーション プロパティのデータを明示的に含めていない場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。

複数のリレーションシップの関連データを 1 つのクエリに含めることができます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>複数のレベルを含める

`ThenInclude` メソッドを使用して、リレーションシップをドリル ダウンし、複数のレベルの関連データを含めることができます。 次の例では、すべてのブログ、関連記事、および各投稿の作成者を読み込みます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

`ThenInclude` に対して複数の呼び出しを連鎖させて、さらなるレベルの関連データを含めることができます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

このすべてを組み合わせて、複数のレベルと複数のルートの関連データを同じクエリ内に含めることができます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

含まれているエンティティの 1 つについて複数の関連エンティティを含めることができます。 たとえば、`Blogs` をクエリするときに、`Posts` を含め、さらに `Posts` の `Author` と `Tags` の両方を含めたい場合があります。 この場合、ルートから始まる各インクルード パスを指定する必要があります。 たとえば、`Blog -> Posts -> Author` と`Blog -> Posts -> Tags` です。 これで冗長的な結合を実現することにはならず、ほとんどの場合、SQL を生成するときに EF で結合は統合されます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

> [!CAUTION]
> バージョン 3.0.0 より、各 `Include` によって追加の JOIN がリレーショナル プロバイダーによって作成された SQL クエリに追加されます。以前のバージョンでは追加の SQL クエリが生成されていました。 これにより、クエリのパフォーマンスが良かれ悪しかれ大幅に変わります。 特に、`Include` 演算子が非常に多い LINQ クエリは、デカルト爆発の問題を回避するために、複数の個別の LINQ クエリに分割する必要がある場合があります。

### <a name="include-on-derived-types"></a>派生型に対するインクルード

`Include` と `ThenInclude` を使用して、派生型にのみ定義されているナビゲーションの関連データを含めることができます。

次のモデルがあるとします。

```csharp
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

* キャストを使用する

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* `as` 演算子を使用する

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* 型 `string` のパラメーターを受け取る `Include` のオーバーロードを使用する

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a>明示的読み込み

`DbContext.Entry(...)` API を使用してナビゲーション プロパティを明示的に読み込むことができます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

また、関連エンティティを返す個別のクエリを実行することで、ナビゲーション プロパティを明示的に読み込むこともできます。 変更の追跡が有効な場合、エンティティを読み込むと、EF Core は、既に読み込まれているエンティティがあれば参照するように、新しく読み込まれたエンティティのナビゲーション プロパティを自動的に設定します。また、新しく読み込まれたエンティティを参照するように、既に読み込まれているエンティティのナビゲーション プロパティを自動的に設定します。

### <a name="querying-related-entities"></a>関連エンティティのクエリ

また、ナビゲーション プロパティの内容を表す LINQ クエリを取得することもできます。

そのため、関連エンティティをメモリに読み込まずに集計演算子を実行するなどの操作を行うことができます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

関連エンティティがメモリに読み込まれるようにフィルター処理することもできます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>遅延読み込み

遅延読み込みを使用するには、[Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) パッケージをインストールし、`UseLazyLoadingProxies` を呼び出して有効にする方法が最も簡単です。 次に例を示します。

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

または、AddDbContext を使用する場合:

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

EF Core は、オーバーライド可能なナビゲーション プロパティの場合に遅延読み込みを有効にします。つまり `virtual` であり、継承可能なクラスのナビゲーション プロパティである必要があります。 たとえば、次のエンティティでは、`Post.Blog` および `Blog.Posts` ナビゲーション プロパティの遅延読み込みが実行されます。

```csharp
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

遅延読み込みプロキシは、[エンティティ型コンストラクター](../modeling/constructors.md)に関するページで説明されているように、エンティティに `ILazyLoader` サービスを挿入することで機能します。 次に例を示します。

```csharp
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
        get => LazyLoader.Load(this, ref _posts);
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
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

この場合、エンティティ型を継承したり、ナビゲーション プロパティを仮想にしたりする必要はありません。コンテキストにアタッチされたときに、`new` で作成されたエンティティ インスタンスの遅延読み込みを実行することができます。 ただし、これには、[Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) パッケージで定義される `ILazyLoader` サービスが必要です。 このパッケージに含まれる型のセットは、それに依存してもほとんど影響がないように、最低限のセットになっています。 しかし、エンティティ型で EF Core パッケージへの依存を完全に回避するには、デリゲートとして `ILazyLoader.Load` メソッドを挿入することが可能です。 次に例を示します。

```csharp
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
        get => LazyLoader.Load(this, ref _posts);
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
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

上記のコードでは、デリゲートの使用をビット クリーナーにするために、拡張メソッド `Load` を使用しています。

```csharp
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
> 遅延読み込みデリゲートのコンストラクター パラメーターは、"lazyLoader" と指定する必要があります。 これとは別の名前を使用する構成が今後のリリースで計画されています。

## <a name="related-data-and-serialization"></a>関連データとシリアル化

EF Core はナビゲーション プロパティを自動的に修正するので、最終的にオブジェクト グラフの循環が生じる可能性があります。 たとえば、ブログとその関連する投稿を読み込むと、投稿のコレクションを参照するブログ オブジェクトになります。 これらの各投稿には元のブログへの参照が含まれることになります。

一部のシリアル化フレームワークでは、このような循環は許可されていません。 たとえば、Json.NET では、循環が発生した場合に次の例外をスローします。

> Newtonsoft.Json.JsonSerializationException:Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.

ASP.NET Core を使用している場合は、オブジェクト グラフで見つかった循環を無視するように Json.NET を構成できます。 この操作は、`Startup.cs` の `ConfigureServices(...)` メソッドで実行します。

```csharp
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

別の方法は、いずれかのナビゲーション プロパティを `[JsonIgnore]` 属性で装飾し、シリアル化中にそのナビゲーション プロパティを走査しないように Json.NET に指示することです。
