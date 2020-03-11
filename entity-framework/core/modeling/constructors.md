---
title: コンストラクターを使用したエンティティ型-EF Core
author: ajcvickers
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
uid: core/modeling/constructors
ms.openlocfilehash: ddfaa8eebde388a9d3309f21b8891de593077956
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414651"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="ec525-102">コンストラクターを使用したエンティティ型</span><span class="sxs-lookup"><span data-stu-id="ec525-102">Entity types with constructors</span></span>

> [!NOTE]  
> <span data-ttu-id="ec525-103">これは EF Core 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="ec525-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="ec525-104">EF Core 2.1 以降では、パラメーターを使用 EF Core してコンストラクターを定義し、エンティティのインスタンスを作成するときにこのコンストラクターを呼び出すことができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="ec525-104">Starting with EF Core 2.1, it is now possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="ec525-105">コンストラクターのパラメーターは、マップされたプロパティ、またはさまざまな種類のサービスにバインドして、遅延読み込みなどの動作を容易にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ec525-105">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]  
> <span data-ttu-id="ec525-106">EF Core 2.1 の場合、すべてのコンストラクターのバインドは規約に従っています。</span><span class="sxs-lookup"><span data-stu-id="ec525-106">As of EF Core 2.1, all constructor binding is by convention.</span></span> <span data-ttu-id="ec525-107">使用する特定のコンストラクターの構成は、将来のリリースで計画されています。</span><span class="sxs-lookup"><span data-stu-id="ec525-107">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="ec525-108">マップされたプロパティへのバインド</span><span class="sxs-lookup"><span data-stu-id="ec525-108">Binding to mapped properties</span></span>

<span data-ttu-id="ec525-109">一般的なブログ/ポストモデルを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="ec525-109">Consider a typical Blog/Post model:</span></span>

``` csharp
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="ec525-110">EF Core は、クエリの結果など、これらの型のインスタンスを作成するときに、最初に既定のパラメーターなしのコンストラクターを呼び出し、次に各プロパティをデータベースの値に設定します。</span><span class="sxs-lookup"><span data-stu-id="ec525-110">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="ec525-111">ただし、パラメーター名と型がマップされたプロパティと一致するパラメーター化されたコンストラクターが EF Core によって検出された場合は、代わりに、これらのプロパティの値を使用してパラメーター化されたコンストラクターが呼び出され、各プロパティが明示的に設定されることはありません。</span><span class="sxs-lookup"><span data-stu-id="ec525-111">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="ec525-112">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ec525-112">For example:</span></span>

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; set; }

    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="ec525-113">注意事項:</span><span class="sxs-lookup"><span data-stu-id="ec525-113">Some things to note:</span></span>

* <span data-ttu-id="ec525-114">すべてのプロパティにコンストラクターパラメーターを設定する必要があるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="ec525-114">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="ec525-115">たとえば、Post. Content プロパティは、コンストラクターのパラメーターによって設定されないため、EF Core はコンストラクターを通常の方法で呼び出した後に設定します。</span><span class="sxs-lookup"><span data-stu-id="ec525-115">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="ec525-116">パラメーターの型と名前は、プロパティの型と名前を一致させる必要があります。ただし、パラメーターは、Pascal 形式ですが、パラメーターは camel 形式にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ec525-116">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="ec525-117">EF Core は、コンストラクターを使用して、ナビゲーションプロパティ (ブログや投稿など) を設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="ec525-117">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="ec525-118">コンストラクターは、パブリック、プライベート、またはその他のアクセシビリティを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="ec525-118">The constructor can be public, private, or have any other accessibility.</span></span> <span data-ttu-id="ec525-119">ただし、レイジー読み込みプロキシでは、継承プロキシクラスからコンストラクターにアクセスできる必要があります。</span><span class="sxs-lookup"><span data-stu-id="ec525-119">However, lazy-loading proxies require that the constructor is accessible from the inheriting proxy class.</span></span> <span data-ttu-id="ec525-120">通常、これはパブリックまたは保護されていることを意味します。</span><span class="sxs-lookup"><span data-stu-id="ec525-120">Usually this means making it either public or protected.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="ec525-121">読み取り専用プロパティ</span><span class="sxs-lookup"><span data-stu-id="ec525-121">Read-only properties</span></span>

<span data-ttu-id="ec525-122">コンストラクターを使用してプロパティを設定したら、それらの一部を読み取り専用にすることが理にかなっています。</span><span class="sxs-lookup"><span data-stu-id="ec525-122">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="ec525-123">EF Core はこれをサポートしていますが、次の点を確認してください。</span><span class="sxs-lookup"><span data-stu-id="ec525-123">EF Core supports this, but there are some things to look out for:</span></span>

* <span data-ttu-id="ec525-124">Setter のないプロパティは規約によってマップされません。</span><span class="sxs-lookup"><span data-stu-id="ec525-124">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="ec525-125">(これにより、計算されたプロパティなど、マップしないプロパティがマップされる傾向があります)。</span><span class="sxs-lookup"><span data-stu-id="ec525-125">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="ec525-126">自動的に生成されたキー値を使用するには、新しいエンティティを挿入するときにキージェネレーターによってキー値が設定される必要があるため、読み取り/書き込み可能なキープロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="ec525-126">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="ec525-127">これらの作業を回避する簡単な方法は、プライベート setter を使用することです。</span><span class="sxs-lookup"><span data-stu-id="ec525-127">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="ec525-128">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ec525-128">For example:</span></span>

``` csharp
public class Blog
{
    public Blog(int id, string name, string author)
    {
        Id = id;
        Name = name;
        Author = author;
    }

    public int Id { get; private set; }

    public string Name { get; private set; }
    public string Author { get; private set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public Post(int id, string title, DateTime postedOn)
    {
        Id = id;
        Title = title;
        PostedOn = postedOn;
    }

    public int Id { get; private set; }

    public string Title { get; private set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; private set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="ec525-129">EF Core は、プライベート setter を持つプロパティを読み取り/書き込み可能として認識します。つまり、すべてのプロパティが以前としてマップされ、キーを格納して生成することができます。</span><span class="sxs-lookup"><span data-stu-id="ec525-129">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="ec525-130">プライベート setter を使用する代わりに、プロパティを実際に読み取り専用にし、OnModelCreating で明示的なマッピングを追加することもできます。</span><span class="sxs-lookup"><span data-stu-id="ec525-130">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="ec525-131">同様に、一部のプロパティを完全に削除し、フィールドのみに置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="ec525-131">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="ec525-132">たとえば、次のようなエンティティ型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="ec525-132">For example, consider these entity types:</span></span>

``` csharp
public class Blog
{
    private int _id;

    public Blog(string name, string author)
    {
        Name = name;
        Author = author;
    }

    public string Name { get; }
    public string Author { get; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    private int _id;

    public Post(string title, DateTime postedOn)
    {
        Title = title;
        PostedOn = postedOn;
    }

    public string Title { get; }
    public string Content { get; set; }
    public DateTime PostedOn { get; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="ec525-133">また、OnModelCreating のこの構成は次のように作成されます。</span><span class="sxs-lookup"><span data-stu-id="ec525-133">And this configuration in OnModelCreating:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Author);
            b.Property(e => e.Name);
        });

    modelBuilder.Entity<Post>(
        b =>
        {
            b.HasKey("_id");
            b.Property(e => e.Title);
            b.Property(e => e.PostedOn);
        });
}
```

<span data-ttu-id="ec525-134">注意する点:</span><span class="sxs-lookup"><span data-stu-id="ec525-134">Things to note:</span></span>

* <span data-ttu-id="ec525-135">キー "property" はフィールドになりました。</span><span class="sxs-lookup"><span data-stu-id="ec525-135">The key "property" is now a field.</span></span> <span data-ttu-id="ec525-136">これは、ストアによって生成されたキーを使用できるように、`readonly` フィールドではありません。</span><span class="sxs-lookup"><span data-stu-id="ec525-136">It is not a `readonly` field so that store-generated keys can be used.</span></span>
* <span data-ttu-id="ec525-137">その他のプロパティは、コンストラクターでのみ設定される読み取り専用プロパティです。</span><span class="sxs-lookup"><span data-stu-id="ec525-137">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="ec525-138">主キーの値が EF によってのみ設定された場合、またはデータベースから読み取られた場合は、それをコンストラクターに含める必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ec525-138">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="ec525-139">これにより、キー "プロパティ" は単純なフィールドとして残され、新しいブログや投稿を作成するときに明示的に設定する必要がないことがわかります。</span><span class="sxs-lookup"><span data-stu-id="ec525-139">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]  
> <span data-ttu-id="ec525-140">このコードでは、フィールドが使用されていないことを示すコンパイラ警告 ' 169 ' が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ec525-140">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="ec525-141">実際には、extralinguistic の方法でフィールドが使用されているため、これは無視でき EF Core。</span><span class="sxs-lookup"><span data-stu-id="ec525-141">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="ec525-142">サービスの挿入</span><span class="sxs-lookup"><span data-stu-id="ec525-142">Injecting services</span></span>

<span data-ttu-id="ec525-143">EF Core は、エンティティ型のコンストラクターに "services" を挿入することもできます。</span><span class="sxs-lookup"><span data-stu-id="ec525-143">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="ec525-144">たとえば、次のコードを挿入できます。</span><span class="sxs-lookup"><span data-stu-id="ec525-144">For example, the following can be injected:</span></span>

* <span data-ttu-id="ec525-145">`DbContext`-現在のコンテキストインスタンス。派生した DbContext 型として型指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="ec525-145">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="ec525-146">`ILazyLoader`-遅延読み込みサービス-詳細については、[遅延読み込みのドキュメント](../querying/related-data.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec525-146">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="ec525-147">`Action<object, string>`-遅延読み込みデリゲート-詳細については、[遅延読み込みのドキュメント](../querying/related-data.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ec525-147">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="ec525-148">`IEntityType`-このエンティティ型に関連付けられた EF Core メタデータ</span><span class="sxs-lookup"><span data-stu-id="ec525-148">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]  
> <span data-ttu-id="ec525-149">EF Core 2.1 の場合、EF Core によって認識されるサービスのみを挿入できます。</span><span class="sxs-lookup"><span data-stu-id="ec525-149">As of EF Core 2.1, only services known by EF Core can be injected.</span></span> <span data-ttu-id="ec525-150">アプリケーションサービスの挿入のサポートは、今後のリリースで考慮されています。</span><span class="sxs-lookup"><span data-stu-id="ec525-150">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="ec525-151">たとえば、挿入された DbContext を使用して、データベースに選択的にアクセスし、それらのエンティティをすべて読み込まずに関連エンティティに関する情報を取得することができます。</span><span class="sxs-lookup"><span data-stu-id="ec525-151">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="ec525-152">次の例では、投稿を読み込まずにブログ内の投稿数を取得するために使用されています。</span><span class="sxs-lookup"><span data-stu-id="ec525-152">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

``` csharp
public class Blog
{
    public Blog()
    {
    }

    private Blog(BloggingContext context)
    {
        Context = context;
    }

    private BloggingContext Context { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }
    public string Author { get; set; }

    public ICollection<Post> Posts { get; set; }

    public int PostsCount
        => Posts?.Count
           ?? Context?.Set<Post>().Count(p => Id == EF.Property<int?>(p, "BlogId"))
           ?? 0;
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public DateTime PostedOn { get; set; }

    public Blog Blog { get; set; }
}
```

<span data-ttu-id="ec525-153">これについては、次の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="ec525-153">A few things to notice about this:</span></span>

* <span data-ttu-id="ec525-154">コンストラクターは EF Core によって呼び出されるだけなのでプライベートです。一般に使用するための別のパブリックコンストラクターもあります。</span><span class="sxs-lookup"><span data-stu-id="ec525-154">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="ec525-155">挿入されたサービス (コンテキスト) を使用するコードは、EF Core がインスタンスを作成していないケースを処理するために `null` されていることに対して防御されます。</span><span class="sxs-lookup"><span data-stu-id="ec525-155">The code using the injected service (that is, the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="ec525-156">サービスは読み取り/書き込みプロパティに格納されるため、エンティティが新しいコンテキストインスタンスにアタッチされるときにリセットされます。</span><span class="sxs-lookup"><span data-stu-id="ec525-156">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]  
> <span data-ttu-id="ec525-157">このような DbContext の挿入は、エンティティ型を EF Core に直接結合するため、多くの場合、アンチパターンと見なされます。</span><span class="sxs-lookup"><span data-stu-id="ec525-157">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="ec525-158">このようなサービスインジェクションを使用する前に、すべてのオプションを慎重に検討してください。</span><span class="sxs-lookup"><span data-stu-id="ec525-158">Carefully consider all options before using service injection like this.</span></span>
