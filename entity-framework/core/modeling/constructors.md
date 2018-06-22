---
title: EF コアのコンス トラクターを持つエンティティ型
author: ajcvickers
ms.author: divega
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
ms.technology: entity-framework-core
uid: core/modeling/constructors
ms.openlocfilehash: 8cea624c295f99ef54cb8b4758642eade03c235e
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2018
ms.locfileid: "31812496"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="38385-102">コンス トラクターを持つエンティティ型</span><span class="sxs-lookup"><span data-stu-id="38385-102">Entity types with constructors</span></span>

> [!NOTE]  
> <span data-ttu-id="38385-103">この機能は、EF コア 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="38385-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="38385-104">EF コア 2.1 から始めて、場合は、パラメーターを持つコンス トラクターを定義し、エンティティのインスタンスを作成するときに、このコンス トラクターを呼び出す EF コアを可能なようになりました。</span><span class="sxs-lookup"><span data-stu-id="38385-104">Starting with EF Core 2.1, it is now possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="38385-105">コンス トラクターのパラメーターは、マップされているプロパティにバインドできます。 または動作を容易にするサービスのさまざまな種類には遅延読み込みと同様にします。</span><span class="sxs-lookup"><span data-stu-id="38385-105">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]  
> <span data-ttu-id="38385-106">EF コア 2.1 では、時点では、すべてのコンス トラクターのバインディングは、慣例です。</span><span class="sxs-lookup"><span data-stu-id="38385-106">As of EF Core 2.1, all constructor binding is by convention.</span></span> <span data-ttu-id="38385-107">使用する特定のコンス トラクターの構成は、今後のリリースされる予定です。</span><span class="sxs-lookup"><span data-stu-id="38385-107">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="38385-108">マップされたプロパティへのバインド</span><span class="sxs-lookup"><span data-stu-id="38385-108">Binding to mapped properties</span></span>

<span data-ttu-id="38385-109">一般的なブログの投稿/モデルを考慮してください。</span><span class="sxs-lookup"><span data-stu-id="38385-109">Consider a typical Blog/Post model:</span></span>

```Csharp
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

<span data-ttu-id="38385-110">EF コアは、これらの型のインスタンスを作成するときなど、クエリの結果をまず既定のパラメーターなしコンス トラクターを呼び出すされ値をデータベースの各プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="38385-110">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="38385-111">ただし、EF コアでパラメーター化されたコンス トラクターが見つかった場合パラメーターの名前との一致する型のプロパティをマップしてから、それらのプロパティの値を持つパラメーター化されたコンス トラクターを呼び出す代わりには、各プロパティを明示的に設定できません。</span><span class="sxs-lookup"><span data-stu-id="38385-111">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="38385-112">例えば:</span><span class="sxs-lookup"><span data-stu-id="38385-112">For example:</span></span>

```Csharp
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
<span data-ttu-id="38385-113">いくつかの点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="38385-113">Some things to note:</span></span>
* <span data-ttu-id="38385-114">すべてのプロパティには、コンス トラクターのパラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="38385-114">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="38385-115">たとえば、Post.Content プロパティが設定されていないのコンス トラクター パラメーターで EF コアを使用すると通常の方法で、コンス トラクターの呼び出し後に設定されます。</span><span class="sxs-lookup"><span data-stu-id="38385-115">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="38385-116">パラメーターは、camel 形式の中に、プロパティを pascal でく点を除いて、パラメーターの型と名前がプロパティの型と名前になければなりません。</span><span class="sxs-lookup"><span data-stu-id="38385-116">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="38385-117">(ブログ投稿上など) のナビゲーション プロパティは設定できません EF コア コンス トラクターを使用します。</span><span class="sxs-lookup"><span data-stu-id="38385-117">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="38385-118">コンス トラクターをパブリックにすることができます、プライベート、またはその他の任意のアクセシビリティがします。</span><span class="sxs-lookup"><span data-stu-id="38385-118">The constructor can be public, private, or have any other accessibility.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="38385-119">読み取り専用プロパティ</span><span class="sxs-lookup"><span data-stu-id="38385-119">Read-only properties</span></span>

<span data-ttu-id="38385-120">コンス トラクターを使用して設定されるプロパティとできます賢明、一部の読み取り専用にします。</span><span class="sxs-lookup"><span data-stu-id="38385-120">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="38385-121">EF コアが、これをサポートしますが、いくつかの点を注意してください。</span><span class="sxs-lookup"><span data-stu-id="38385-121">EF Core supports this, but there are some things to look out for:</span></span>
* <span data-ttu-id="38385-122">プロパティ set アクセス操作子なしでは、規約によってマップされていません。</span><span class="sxs-lookup"><span data-stu-id="38385-122">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="38385-123">(これにより傾向がありますにはない対応する、計算済みプロパティなどのプロパティをマップします。)</span><span class="sxs-lookup"><span data-stu-id="38385-123">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="38385-124">自動的に生成されたキー値を使用するには、キーの値は、新しいエンティティを挿入するときに、キー生成コンポーネントに設定する必要があるため、読み取り/書き込みであるキー プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="38385-124">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="38385-125">次の作業を回避する簡単な方法では、プライベート set アクセス操作子を使用します。</span><span class="sxs-lookup"><span data-stu-id="38385-125">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="38385-126">例えば:</span><span class="sxs-lookup"><span data-stu-id="38385-126">For example:</span></span>
```Csharp
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
<span data-ttu-id="38385-127">EF Core では、以前と同様にマップされているすべてのプロパティと、キーができることを意味するストアで生成された、読み取り/書き込みとしてプライベート set アクセス操作子を持つプロパティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="38385-127">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="38385-128">プライベート set アクセス操作子を使用する代わりにを本当に読み取り専用プロパティを作成し、OnModelCreating により明示的なマッピングを追加します。</span><span class="sxs-lookup"><span data-stu-id="38385-128">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="38385-129">同様に、いくつかのプロパティを完全に削除およびフィールドだけに置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="38385-129">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="38385-130">たとえば、これらのエンティティ型があるとします。</span><span class="sxs-lookup"><span data-stu-id="38385-130">For example, consider these entity types:</span></span>

```Csharp
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
<span data-ttu-id="38385-131">および OnModelCreating でこの構成:</span><span class="sxs-lookup"><span data-stu-id="38385-131">And this configuration in OnModelCreating:</span></span>
```Csharp
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
<span data-ttu-id="38385-132">点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="38385-132">Things to note:</span></span>
* <span data-ttu-id="38385-133">"Property"、キーは、今すぐフィールドです。</span><span class="sxs-lookup"><span data-stu-id="38385-133">The key "property" is now a field.</span></span> <span data-ttu-id="38385-134">`readonly`ストア生成のキーを使用できるようにするフィールドです。</span><span class="sxs-lookup"><span data-stu-id="38385-134">It is not a `readonly` field so that store-generated keys can be used.</span></span> 
* <span data-ttu-id="38385-135">その他のプロパティは、コンス トラクターでのみ設定する読み取り専用のプロパティです。</span><span class="sxs-lookup"><span data-stu-id="38385-135">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="38385-136">主キーの値をデータベースからの読み取り、または EF によって設定だけ場合、コンス トラクターに含める必要はありますありません。</span><span class="sxs-lookup"><span data-stu-id="38385-136">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="38385-137">これは、単純なフィールドとして、キー"property"のままにし、オフにする必要がありますいないを設定するために明示的に新しいブログまたは投稿を作成するときになります。</span><span class="sxs-lookup"><span data-stu-id="38385-137">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]  
> <span data-ttu-id="38385-138">このコードは、コンパイラの警告 '169' を示す、フィールドが使用されないことになります。</span><span class="sxs-lookup"><span data-stu-id="38385-138">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="38385-139">実際に EF コアを使用いるため、フィールド extralinguistic 方法では無視できます。</span><span class="sxs-lookup"><span data-stu-id="38385-139">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="38385-140">サービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="38385-140">Injecting services</span></span>

<span data-ttu-id="38385-141">EF コアは、エンティティ型のコンス トラクターに「サービス」を挿入することができますも。</span><span class="sxs-lookup"><span data-stu-id="38385-141">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="38385-142">たとえば、次を挿入できます。</span><span class="sxs-lookup"><span data-stu-id="38385-142">For example, the following can be injected:</span></span>
* <span data-ttu-id="38385-143">`DbContext` -現在のコンテキストのインスタンスは、派生 DbContext 型として型指定することができますも</span><span class="sxs-lookup"><span data-stu-id="38385-143">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="38385-144">`ILazyLoader` --、遅延読み込みサービスを参照してください、[遅延読み込みドキュメント](../querying/related-data.md)詳細については</span><span class="sxs-lookup"><span data-stu-id="38385-144">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="38385-145">`Action<object, string>` -遅延読み込みデリゲート--を参照してください、[遅延読み込みドキュメント](../querying/related-data.md)詳細については</span><span class="sxs-lookup"><span data-stu-id="38385-145">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="38385-146">`IEntityType` -このエンティティ型に関連付けられている EF コア メタデータ</span><span class="sxs-lookup"><span data-stu-id="38385-146">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]  
> <span data-ttu-id="38385-147">EF コア 2.1 では、時点で既知の EF のコアでのサービスだけを挿入できます。</span><span class="sxs-lookup"><span data-stu-id="38385-147">As of EF Core 2.1, only services known by EF Core can be injected.</span></span> <span data-ttu-id="38385-148">今後のリリースでは、アプリケーション サービスを提供するためのサポートを検討しています。</span><span class="sxs-lookup"><span data-stu-id="38385-148">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="38385-149">たとえば、挿入された DbContext は、選択的にそれらのすべてを読み込むことがなく、関連エンティティに関する情報を取得するデータベースへのアクセスに使用できます。</span><span class="sxs-lookup"><span data-stu-id="38385-149">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="38385-150">次の例では、これは、投稿を読み込むことがなくブログの投稿の数の取得に使用しています。</span><span class="sxs-lookup"><span data-stu-id="38385-150">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

```Csharp
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
<span data-ttu-id="38385-151">これについて注意するいくつかの点:</span><span class="sxs-lookup"><span data-stu-id="38385-151">A few things to notice about this:</span></span>
* <span data-ttu-id="38385-152">コンス トラクターはプライベートである EF のコアでしかと呼ばれ、一般的な用途の別のパブリック コンス トラクターがあるためです。</span><span class="sxs-lookup"><span data-stu-id="38385-152">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="38385-153">挿入されたサービスを使用して、コード (つまり、コンテキスト) がこれに対する防御されて`null`EF コア、インスタンスの作成がないケースを処理します。</span><span class="sxs-lookup"><span data-stu-id="38385-153">The code using the injected service (i.e. the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="38385-154">サービスが、読み取り/書き込みプロパティに格納されているために、エンティティがコンテキストの新しいインスタンスに接続されている場合にリセットされます。</span><span class="sxs-lookup"><span data-stu-id="38385-154">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]  
> <span data-ttu-id="38385-155">次のように DbContext を挿入することは多くの場合と見なされますアンチ パターン EF コアに直接、エンティティ型ではそのためです。</span><span class="sxs-lookup"><span data-stu-id="38385-155">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="38385-156">次のようにサービスの挿入を使用する前にすべてのオプションを慎重に検討します。</span><span class="sxs-lookup"><span data-stu-id="38385-156">Carefully consider all options before using service injection like this.</span></span>
