---
title: EF Core のコンス トラクターを使用したエンティティ型
author: ajcvickers
ms.date: 02/23/2018
ms.assetid: 420AFFE7-B709-4A68-9149-F06F8746FB33
uid: core/modeling/constructors
ms.openlocfilehash: 0536393d074d82583f47faae13cc22498193cb7e
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994894"
---
# <a name="entity-types-with-constructors"></a><span data-ttu-id="20d5e-102">コンス トラクターを使用したエンティティ型</span><span class="sxs-lookup"><span data-stu-id="20d5e-102">Entity types with constructors</span></span>

> [!NOTE]  
> <span data-ttu-id="20d5e-103">これは EF Core 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="20d5e-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="20d5e-104">EF Core 2.1 以降の場合は、パラメーターを持つコンス トラクターを定義し、EF core がエンティティのインスタンスを作成するときに、このコンス トラクターを呼び出すようになりました。</span><span class="sxs-lookup"><span data-stu-id="20d5e-104">Starting with EF Core 2.1, it is now possible to define a constructor with parameters and have EF Core call this constructor when creating an instance of the entity.</span></span> <span data-ttu-id="20d5e-105">コンス トラクターのパラメーターは、マップされたプロパティにバインドできるまたは動作などの遅延読み込みを容易にするサービスのさまざまな種類にします。</span><span class="sxs-lookup"><span data-stu-id="20d5e-105">The constructor parameters can be bound to mapped properties, or to various kinds of services to facilitate behaviors like lazy-loading.</span></span>

> [!NOTE]  
> <span data-ttu-id="20d5e-106">EF Core 2.1 の時点では、コンス トラクターのすべてのバインドは、規則ことです。</span><span class="sxs-lookup"><span data-stu-id="20d5e-106">As of EF Core 2.1, all constructor binding is by convention.</span></span> <span data-ttu-id="20d5e-107">使用する特定のコンス トラクターの構成は、将来のリリース予定です。</span><span class="sxs-lookup"><span data-stu-id="20d5e-107">Configuration of specific constructors to use is planned for a future release.</span></span>

## <a name="binding-to-mapped-properties"></a><span data-ttu-id="20d5e-108">マップされたプロパティへのバインド</span><span class="sxs-lookup"><span data-stu-id="20d5e-108">Binding to mapped properties</span></span>

<span data-ttu-id="20d5e-109">一般的なブログの投稿/モデルを検討してください。</span><span class="sxs-lookup"><span data-stu-id="20d5e-109">Consider a typical Blog/Post model:</span></span>

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

<span data-ttu-id="20d5e-110">EF Core では、これらの型のインスタンスを作成するときなど、クエリの結果を最初に既定のパラメーターなしコンス トラクターを呼び出すし、値に、データベースからの各プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="20d5e-110">When EF Core creates instances of these types, such as for the results of a query, it will first call the default parameterless constructor and then set each property to the value from the database.</span></span> <span data-ttu-id="20d5e-111">ただし、EF Core でパラメーター化されたコンス トラクターが見つかった場合は、パラメーターの名前との一致する型のプロパティをマップし、それらのプロパティの値を持つパラメーター化されたコンス トラクターを呼び出す代わりには、各プロパティを明示的に設定しません。</span><span class="sxs-lookup"><span data-stu-id="20d5e-111">However, if EF Core finds a parameterized constructor with parameter names and types that match those of mapped properties, then it will instead call the parameterized constructor with values for those properties and will not set each property explicitly.</span></span> <span data-ttu-id="20d5e-112">例えば:</span><span class="sxs-lookup"><span data-stu-id="20d5e-112">For example:</span></span>

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
<span data-ttu-id="20d5e-113">いくつかの点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="20d5e-113">Some things to note:</span></span>
* <span data-ttu-id="20d5e-114">すべてのプロパティは、コンス トラクターのパラメーターを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="20d5e-114">Not all properties need to have constructor parameters.</span></span> <span data-ttu-id="20d5e-115">たとえば、ため、EF Core を使用すると、通常どおりに、コンス トラクターを呼び出した後設定は、コンス トラクター パラメーターで Post.Content プロパティは設定されません。</span><span class="sxs-lookup"><span data-stu-id="20d5e-115">For example, the Post.Content property is not set by any constructor parameter, so EF Core will set it after calling the constructor in the normal way.</span></span>
* <span data-ttu-id="20d5e-116">パラメーターは、camel 形式の中に、プロパティを pascal 形式で表記できますが、パラメーターの型と名前がプロパティの型と名前にする必要があります一致します。</span><span class="sxs-lookup"><span data-stu-id="20d5e-116">The parameter types and names must match property types and names, except that properties can be Pascal-cased while the parameters are camel-cased.</span></span>
* <span data-ttu-id="20d5e-117">EF Core がナビゲーション プロパティ (ブログや投稿上記) を設定できませんコンス トラクターを使用します。</span><span class="sxs-lookup"><span data-stu-id="20d5e-117">EF Core cannot set navigation properties (such as Blog or Posts above) using a constructor.</span></span>
* <span data-ttu-id="20d5e-118">コンス トラクターはパブリックであることができます、プライベート、またはその他の任意のアクセシビリティを持ちます。</span><span class="sxs-lookup"><span data-stu-id="20d5e-118">The constructor can be public, private, or have any other accessibility.</span></span>

### <a name="read-only-properties"></a><span data-ttu-id="20d5e-119">読み取り専用プロパティ</span><span class="sxs-lookup"><span data-stu-id="20d5e-119">Read-only properties</span></span>

<span data-ttu-id="20d5e-120">コンス トラクターを使用してプロパティが設定されていると合理的に一部の読み取り専用です。</span><span class="sxs-lookup"><span data-stu-id="20d5e-120">Once properties are being set via the constructor it can make sense to make some of them read-only.</span></span> <span data-ttu-id="20d5e-121">これには、EF Core をサポートしていますが、注意すべきいくつかの点があります。</span><span class="sxs-lookup"><span data-stu-id="20d5e-121">EF Core supports this, but there are some things to look out for:</span></span>
* <span data-ttu-id="20d5e-122">プロパティ set アクセス操作子なしは規約によってマップされていません。</span><span class="sxs-lookup"><span data-stu-id="20d5e-122">Properties without setters are not mapped by convention.</span></span> <span data-ttu-id="20d5e-123">(これにより、計算されたプロパティなど、マップしていないプロパティをマップする傾向)。</span><span class="sxs-lookup"><span data-stu-id="20d5e-123">(Doing so tends to map properties that should not be mapped, such as computed properties.)</span></span>
* <span data-ttu-id="20d5e-124">自動的に生成されたキーの値を使用して、キー プロパティ、キーの値は、新しいエンティティを挿入するときにキー生成コンポーネントによって設定する必要があるため、読み取り/書き込みである必要があります。</span><span class="sxs-lookup"><span data-stu-id="20d5e-124">Using automatically generated key values requires a key property that is read-write, since the key value needs to be set by the key generator when inserting new entities.</span></span>

<span data-ttu-id="20d5e-125">これらの操作を回避する簡単な方法では、プライベート セッターを使用します。</span><span class="sxs-lookup"><span data-stu-id="20d5e-125">An easy way to avoid these things is to use private setters.</span></span> <span data-ttu-id="20d5e-126">例えば:</span><span class="sxs-lookup"><span data-stu-id="20d5e-126">For example:</span></span>
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
<span data-ttu-id="20d5e-127">EF Core には、読み取り/書き込み、つまり、すべてのプロパティが以前と同様にマップされ、キーこともできますがストア生成としてプライベート セッターを持つプロパティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="20d5e-127">EF Core sees a property with a private setter as read-write, which means that all properties are mapped as before and the key can still be store-generated.</span></span>

<span data-ttu-id="20d5e-128">プライベート セッターを使用する代わりに実際に読み取り専用プロパティを作成し、OnModelCreating でより明確なマッピングを追加することです。</span><span class="sxs-lookup"><span data-stu-id="20d5e-128">An alternative to using private setters is to make properties really read-only and add more explicit mapping in OnModelCreating.</span></span> <span data-ttu-id="20d5e-129">同様に、いくつかのプロパティを完全に削除し、フィールドだけに置き換えられます。</span><span class="sxs-lookup"><span data-stu-id="20d5e-129">Likewise, some properties can be removed completely and replaced with only fields.</span></span> <span data-ttu-id="20d5e-130">たとえば、これらのエンティティ型があるとします。</span><span class="sxs-lookup"><span data-stu-id="20d5e-130">For example, consider these entity types:</span></span>

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
<span data-ttu-id="20d5e-131">OnModelCreating では、この構成:</span><span class="sxs-lookup"><span data-stu-id="20d5e-131">And this configuration in OnModelCreating:</span></span>
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
<span data-ttu-id="20d5e-132">点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="20d5e-132">Things to note:</span></span>
* <span data-ttu-id="20d5e-133">"Property"、キーは、今すぐフィールドです。</span><span class="sxs-lookup"><span data-stu-id="20d5e-133">The key "property" is now a field.</span></span> <span data-ttu-id="20d5e-134">`readonly`フィールド ストア生成キーを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="20d5e-134">It is not a `readonly` field so that store-generated keys can be used.</span></span>
* <span data-ttu-id="20d5e-135">その他のプロパティは、コンス トラクターでのみ設定する読み取り専用のプロパティです。</span><span class="sxs-lookup"><span data-stu-id="20d5e-135">The other properties are read-only properties set only in the constructor.</span></span>
* <span data-ttu-id="20d5e-136">主キーの値を EF によって設定しかまたはデータベースからの読み取りは、コンス トラクターに含める必要はあるありません。</span><span class="sxs-lookup"><span data-stu-id="20d5e-136">If the primary key value is only ever set by EF or read from the database, then there is no need to include it in the constructor.</span></span> <span data-ttu-id="20d5e-137">これは、操作を行うと、"property"、キーを単純なフィールドとしてなりに設定してはならない明示的に新しいブログや投稿を作成するときにことを明確になります。</span><span class="sxs-lookup"><span data-stu-id="20d5e-137">This leaves the key "property" as a simple field and makes it clear that it should not be set explicitly when creating new blogs or posts.</span></span>

> [!NOTE]  
> <span data-ttu-id="20d5e-138">このコードは、コンパイラの警告 '169' を示すフィールドは使用しないことになります。</span><span class="sxs-lookup"><span data-stu-id="20d5e-138">This code will result in compiler warning '169' indicating that the field is never used.</span></span> <span data-ttu-id="20d5e-139">これは、実際には EF Core が extralinguistic の方法で、フィールドを使用しているので無視できます。</span><span class="sxs-lookup"><span data-stu-id="20d5e-139">This can be ignored since in reality EF Core is using the field in an extralinguistic manner.</span></span>

## <a name="injecting-services"></a><span data-ttu-id="20d5e-140">サービスを挿入します。</span><span class="sxs-lookup"><span data-stu-id="20d5e-140">Injecting services</span></span>

<span data-ttu-id="20d5e-141">EF Core は、エンティティ型のコンス トラクターに「サービス」を挿入することができますも。</span><span class="sxs-lookup"><span data-stu-id="20d5e-141">EF Core can also inject "services" into an entity type's constructor.</span></span> <span data-ttu-id="20d5e-142">たとえば、次を挿入できます。</span><span class="sxs-lookup"><span data-stu-id="20d5e-142">For example, the following can be injected:</span></span>
* <span data-ttu-id="20d5e-143">`DbContext` -現在のコンテキストのインスタンスは、DbContext 派生型として型指定することもできます</span><span class="sxs-lookup"><span data-stu-id="20d5e-143">`DbContext` - the current context instance, which can also be typed as your derived DbContext type</span></span>
* <span data-ttu-id="20d5e-144">`ILazyLoader` -遅延読み込みサービス--を参照してください、[遅延読み込みドキュメント](../querying/related-data.md)の詳細</span><span class="sxs-lookup"><span data-stu-id="20d5e-144">`ILazyLoader` - the lazy-loading service--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="20d5e-145">`Action<object, string>` -- 遅延読み込みデリゲートを参照してください、[遅延読み込みドキュメント](../querying/related-data.md)の詳細</span><span class="sxs-lookup"><span data-stu-id="20d5e-145">`Action<object, string>` - a lazy-loading delegate--see the [lazy-loading documentation](../querying/related-data.md) for more details</span></span>
* <span data-ttu-id="20d5e-146">`IEntityType` -このエンティティ型に関連付けられている EF Core のメタデータ</span><span class="sxs-lookup"><span data-stu-id="20d5e-146">`IEntityType` - the EF Core metadata associated with this entity type</span></span>

> [!NOTE]  
> <span data-ttu-id="20d5e-147">EF Core 2.1 の時点では、EF Core によって認識されているサービスのみを挿入できます。</span><span class="sxs-lookup"><span data-stu-id="20d5e-147">As of EF Core 2.1, only services known by EF Core can be injected.</span></span> <span data-ttu-id="20d5e-148">今後のリリースでは、アプリケーション サービスを挿入するためのサポートを検討しています。</span><span class="sxs-lookup"><span data-stu-id="20d5e-148">Support for injecting application services is being considered for a future release.</span></span>

<span data-ttu-id="20d5e-149">たとえば、選択的にそれらすべてを読み込むことがなく、関連エンティティに関する情報を取得するデータベースにアクセスする挿入の DbContext を使用できます。</span><span class="sxs-lookup"><span data-stu-id="20d5e-149">For example, an injected DbContext can be used to selectively access the database to obtain information about related entities without loading them all.</span></span> <span data-ttu-id="20d5e-150">次の例では、これは、投稿を読み込むことがなく、ブログの投稿の数の取得に使用しています。</span><span class="sxs-lookup"><span data-stu-id="20d5e-150">In the example below this is used to obtain the number of posts in a blog without loading the posts:</span></span>

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
<span data-ttu-id="20d5e-151">これについて注意する少数です。</span><span class="sxs-lookup"><span data-stu-id="20d5e-151">A few things to notice about this:</span></span>
* <span data-ttu-id="20d5e-152">EF Core でしかと呼ばれ、一般的な用途の別のパブリック コンス トラクターがあるために、コンス トラクターはプライベートでします。</span><span class="sxs-lookup"><span data-stu-id="20d5e-152">The constructor is private, since it is only ever called by EF Core, and there is another public constructor for general use.</span></span>
* <span data-ttu-id="20d5e-153">挿入されたサービス (コンテキスト) を使用したコードはこれに対する防御されて`null`EF Core インスタンスの作成がない場合を処理します。</span><span class="sxs-lookup"><span data-stu-id="20d5e-153">The code using the injected service (that is, the context) is defensive against it being `null` to handle cases where EF Core is not creating the instance.</span></span>
* <span data-ttu-id="20d5e-154">サービスが、読み取り/書き込みプロパティに格納されているため、エンティティが新しいコンテキスト インスタンスにアタッチされているときにリセットされます。</span><span class="sxs-lookup"><span data-stu-id="20d5e-154">Because service is stored in a read/write property it will be reset when the entity is attached to a new context instance.</span></span>

> [!WARNING]  
> <span data-ttu-id="20d5e-155">次のように DbContext を挿入する多くの場合と見なされますアンチ パターンから EF Core に直接、エンティティ型が関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="20d5e-155">Injecting the DbContext like this is often considered an anti-pattern since it couples your entity types directly to EF Core.</span></span> <span data-ttu-id="20d5e-156">このようなサービスの挿入を使用する前にすべてのオプションを慎重に検討します。</span><span class="sxs-lookup"><span data-stu-id="20d5e-156">Carefully consider all options before using service injection like this.</span></span>
