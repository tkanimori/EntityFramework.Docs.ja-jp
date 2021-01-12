---
title: 外部キーとナビゲーションの変更-EF Core
description: 外部キーとナビゲーションを操作してエンティティ間のリレーションシップを変更する方法
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/relationship-changes
ms.openlocfilehash: ac2110509b6748e85411dbb14989522465925ecf
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129620"
---
# <a name="changing-foreign-keys-and-navigations"></a><span data-ttu-id="a0c23-103">外部キーとナビゲーションの変更</span><span class="sxs-lookup"><span data-stu-id="a0c23-103">Changing Foreign Keys and Navigations</span></span>

## <a name="overview-of-foreign-keys-and-navigations"></a><span data-ttu-id="a0c23-104">外部キーとナビゲーションの概要</span><span class="sxs-lookup"><span data-stu-id="a0c23-104">Overview of foreign keys and navigations</span></span>

<span data-ttu-id="a0c23-105">Entity Framework Core (EF Core) モデルのリレーションシップは、外部キー (FKs) を使用して表されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-105">Relationships in an Entity Framework Core (EF Core) model are represented using foreign keys (FKs).</span></span> <span data-ttu-id="a0c23-106">FK は、リレーションシップの依存エンティティまたは子エンティティの1つ以上のプロパティで構成されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-106">An FK consists of one or more properties on the dependent or child entity in the relationship.</span></span> <span data-ttu-id="a0c23-107">この依存/子エンティティは、特定のプリンシパル/親エンティティに関連付けられています。これは、依存/子の外部キープロパティの値が、プリンシパル/親の代替または主キー (PK) プロパティの値と一致する場合です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-107">This dependent/child entity is associated with a given principal/parent entity when the values of the foreign key properties on the dependent/child match the values of the alternate or primary key (PK) properties on the principal/parent.</span></span>

<span data-ttu-id="a0c23-108">外部キーは、データベース内のリレーションシップを格納および操作するのに適した方法ですが、アプリケーションコードで複数の関連エンティティを操作する場合にはあまりわかりません。</span><span class="sxs-lookup"><span data-stu-id="a0c23-108">Foreign keys are a good way to store and manipulate relationships in the database, but are not very friendly when working with multiple related entities in application code.</span></span> <span data-ttu-id="a0c23-109">したがって、ほとんどの EF Core モデルでは、FK 表現に "ナビゲーション" がレイヤー化されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-109">Therefore, most EF Core models also layer "navigations" over the FK representation.</span></span> <span data-ttu-id="a0c23-110">ナビゲーションフォーム C# では、外部キー値を主キー値または代替キー値に一致させることによって検出されたアソシエーションを反映するエンティティインスタンス間の参照を/.NET します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-110">Navigations form C#/.NET references between entity instances that reflect the associations found by matching foreign key values to primary or alternate key values.</span></span>

<span data-ttu-id="a0c23-111">ナビゲーションは、リレーションシップの両側でのみ使用できます。一方の側でのみ使用することも、FK プロパティだけを残すこともできます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-111">Navigations can be used on both sides of the relationship, on one side only, or not at all, leaving only the FK property.</span></span> <span data-ttu-id="a0c23-112">FK プロパティを [shadow プロパティ](xref:core/modeling/shadow-properties)に設定することにより、非表示にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-112">The FK property can be hidden by making it a [shadow property](xref:core/modeling/shadow-properties).</span></span> <span data-ttu-id="a0c23-113">モデリング関係の詳細については、「 [リレーションシップ](xref:core/modeling/relationships) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-113">See [Relationships](xref:core/modeling/relationships) for more information on modelling relationships.</span></span>

> [!TIP]
> <span data-ttu-id="a0c23-114">このドキュメントでは、EF Core の変更の追跡のエンティティの状態と基本について理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-114">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="a0c23-115">これらのトピックの詳細については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-115">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="a0c23-116">[GitHub からサンプルコードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangingFKsAndNavigations)することで、このドキュメントのすべてのコードを実行し、デバッグすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-116">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangingFKsAndNavigations).</span></span>

### <a name="example-model"></a><span data-ttu-id="a0c23-117">モデルの例</span><span class="sxs-lookup"><span data-stu-id="a0c23-117">Example model</span></span>

<span data-ttu-id="a0c23-118">次のモデルには、リレーションシップを持つ4つのエンティティ型が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-118">The following model contains four entity types with relationships between them.</span></span> <span data-ttu-id="a0c23-119">コード内のコメントは、外部キー、主キー、およびナビゲーションであるプロパティを示します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-119">The comments in the code indicate which properties are foreign keys, primary keys, and navigations.</span></span>

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public int Id { get; set; } // Primary key
    public byte[] Banner { get; set; }

    public int BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; } // Foreign key
    public Blog Blog { get; set; } // Reference navigation

    public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
}

public class Tag
{
    public int Id { get; set; } // Primary key
    public string Text { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Model)]

<span data-ttu-id="a0c23-120">このモデルでは、次の3つのリレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-120">The three relationships in this model are:</span></span>

- <span data-ttu-id="a0c23-121">各ブログには、多数の投稿を含めることができます (一対多)。</span><span class="sxs-lookup"><span data-stu-id="a0c23-121">Each blog can have many posts (one-to-many):</span></span>
  - <span data-ttu-id="a0c23-122">`Blog` プリンシパル/親を示します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-122">`Blog` is the principal/parent.</span></span>
  - <span data-ttu-id="a0c23-123">`Post` は、依存/子です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-123">`Post` is the dependent/child.</span></span> <span data-ttu-id="a0c23-124">これには、FK プロパティが含まれています。このプロパティ `Post.BlogId` の値は、関連するブログの PK 値と一致する必要があり `Blog.Id` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-124">It contains the FK property `Post.BlogId`, the value of which must match the `Blog.Id` PK value of the related blog.</span></span>
  - <span data-ttu-id="a0c23-125">`Post.Blog` は、関連するブログへの投稿からの参照ナビゲーションです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-125">`Post.Blog` is a reference navigation from a post to the associated blog.</span></span> <span data-ttu-id="a0c23-126">`Post.Blog` は、の逆ナビゲーションです `Blog.Posts` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-126">`Post.Blog` is the inverse navigation for `Blog.Posts`.</span></span>
  - <span data-ttu-id="a0c23-127">`Blog.Posts` は、ブログからすべての関連する投稿へのコレクションナビゲーションです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-127">`Blog.Posts` is a collection navigation from a blog to all the associated posts.</span></span> <span data-ttu-id="a0c23-128">`Blog.Posts` は、の逆ナビゲーションです `Post.Blog` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-128">`Blog.Posts` is the inverse navigation for `Post.Blog`.</span></span>
- <span data-ttu-id="a0c23-129">各ブログは、1つの資産 (1 対 1) を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-129">Each blog can have one assets (one-to-one):</span></span>
  - <span data-ttu-id="a0c23-130">`Blog` プリンシパル/親を示します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-130">`Blog` is the principal/parent.</span></span>
  - <span data-ttu-id="a0c23-131">`BlogAssets` は、依存/子です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-131">`BlogAssets` is the dependent/child.</span></span> <span data-ttu-id="a0c23-132">これには、FK プロパティが含まれています。このプロパティ `BlogAssets.BlogId` の値は、関連するブログの PK 値と一致する必要があり `Blog.Id` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-132">It contains the FK property `BlogAssets.BlogId`, the value of which must match the `Blog.Id` PK value of the related blog.</span></span>
  - <span data-ttu-id="a0c23-133">`BlogAssets.Blog` は、アセットから関連するブログへの参照ナビゲーションです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-133">`BlogAssets.Blog` is a reference navigation from the assets to the associated blog.</span></span> <span data-ttu-id="a0c23-134">`BlogAssets.Blog` は、の逆ナビゲーションです `Blog.Assets` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-134">`BlogAssets.Blog` is the inverse navigation for `Blog.Assets`.</span></span>
  - <span data-ttu-id="a0c23-135">`Blog.Assets` は、ブログから関連付けられた資産への参照ナビゲーションです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-135">`Blog.Assets` is a reference navigation from the blog to the associated assets.</span></span> <span data-ttu-id="a0c23-136">`Blog.Assets` は、の逆ナビゲーションです `BlogAssets.Blog` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-136">`Blog.Assets` is the inverse navigation for `BlogAssets.Blog`.</span></span>
- <span data-ttu-id="a0c23-137">各投稿は多くのタグを持つことができ、各タグには多数の投稿を含めることができます (多対多)。</span><span class="sxs-lookup"><span data-stu-id="a0c23-137">Each post can have many tags and each tag can have many posts (many-to-many):</span></span>
  - <span data-ttu-id="a0c23-138">多対多リレーションシップは、2 1 対多の関係を超えるレイヤーです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-138">Many-to-many relationships are a further layer over two one-to-many relationships.</span></span> <span data-ttu-id="a0c23-139">多対多リレーションシップについては、このドキュメントで後ほど説明します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-139">Many-to-many relationships are covered later in this document.</span></span>
  - <span data-ttu-id="a0c23-140">`Post.Tags` は、関連付けられているすべてのタグへの投稿からのコレクションナビゲーションです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-140">`Post.Tags` is a collection navigation from a post to all the associated tags.</span></span> <span data-ttu-id="a0c23-141">`Post.Tags` は、の逆ナビゲーションです `Tag.Posts` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-141">`Post.Tags` is the inverse navigation for `Tag.Posts`.</span></span>
  - <span data-ttu-id="a0c23-142">`Tag.Posts` タグから、関連付けられているすべての投稿へのコレクションナビゲーションです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-142">`Tag.Posts` is a collection navigation from a tag to all the associated posts.</span></span> <span data-ttu-id="a0c23-143">`Tag.Posts` は、の逆ナビゲーションです `Post.Tags` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-143">`Tag.Posts` is the inverse navigation for `Post.Tags`.</span></span>

<span data-ttu-id="a0c23-144">リレーションシップをモデル化して構成する方法の詳細については、「 [リレーションシップ](xref:core/modeling/relationships) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-144">See [Relationships](xref:core/modeling/relationships) for more information on how to model and configure relationships.</span></span>

## <a name="relationship-fixup"></a><span data-ttu-id="a0c23-145">リレーションシップの修正</span><span class="sxs-lookup"><span data-stu-id="a0c23-145">Relationship fixup</span></span>

<span data-ttu-id="a0c23-146">EF Core は、外部キー値との連携を維持します。また、その逆も可能です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-146">EF Core keeps navigations in alignment with foreign key values and vice versa.</span></span> <span data-ttu-id="a0c23-147">つまり、外部キーの値が変更され、別のプリンシパル/親エンティティが参照されるようになった場合、この変更を反映するためにナビゲーションが更新されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-147">That is, if a foreign key value changes such that it now refers to a different principal/parent entity, then the navigations are updated to reflect this change.</span></span> <span data-ttu-id="a0c23-148">同様に、ナビゲーションが変更された場合は、関連するエンティティの外部キー値が更新され、この変更が反映されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-148">Likewise, if a navigation is changed, then the foreign key values of the entities involved are updated to reflect this change.</span></span> <span data-ttu-id="a0c23-149">これは、"リレーションシップの修正" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-149">This is called "relationship fixup".</span></span>

### <a name="fixup-by-query"></a><span data-ttu-id="a0c23-150">クエリによる修正</span><span class="sxs-lookup"><span data-stu-id="a0c23-150">Fixup by query</span></span>

<span data-ttu-id="a0c23-151">Fixup は、エンティティがデータベースから照会されるときに最初に行われます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-151">Fixup first occurs when entities are queried from the database.</span></span> <span data-ttu-id="a0c23-152">データベースには外部キー値のみが含まれているので、EF Core がデータベースからエンティティインスタンスを作成する場合は、外部キー値を使用して参照ナビゲーションを設定し、必要に応じてエンティティをコレクションナビゲーションに追加します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-152">The database has only foreign key values, so when EF Core creates an entity instance from the database it uses the foreign key values to set reference navigations and add entities to collection navigations as appropriate.</span></span> <span data-ttu-id="a0c23-153">たとえば、ブログとそれに関連付けられている投稿と資産のクエリを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="a0c23-153">For example, consider a query for blogs and its associated posts and assets:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .ToList();

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Relationship_fixup_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Relationship_fixup_1)]

<span data-ttu-id="a0c23-154">ブログごとに、最初にインスタンスを作成 EF Core `Blog` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-154">For each blog, EF Core will first create a `Blog` instance.</span></span> <span data-ttu-id="a0c23-155">次に、各投稿がデータベースから読み込まれると、 `Post.Blog` 関連するブログをポイントするように参照ナビゲーションが設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-155">Then, as each post is loaded from the database its `Post.Blog` reference navigation is set to point to the associated blog.</span></span> <span data-ttu-id="a0c23-156">同様に、投稿はコレクションのナビゲーションに追加され `Blog.Posts` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-156">Likewise, the post is added to the `Blog.Posts` collection navigation.</span></span> <span data-ttu-id="a0c23-157">同じことが発生し `BlogAssets` ます。ただし、この場合も、両方のナビゲーションが参照である点が異なります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-157">The same thing happens with `BlogAssets`, except in this case both navigations are references.</span></span> <span data-ttu-id="a0c23-158">`Blog.Assets`ナビゲーションは assets インスタンスを指すように設定されており、 `BlogAsserts.Blog` ナビゲーションはブログインスタンスを指すように設定されています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-158">The `Blog.Assets` navigation is set to point to the assets instance, and the `BlogAsserts.Blog` navigation is set to point to the blog instance.</span></span>

<span data-ttu-id="a0c23-159">このクエリの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を見ると、2つのブログが表示されます。それぞれに1つの資産と2つの投稿が追跡されています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-159">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after this query shows two blogs, each with one assets and two posts being tracked:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="a0c23-160">[デバッグ] ビューには、キー値とナビゲーションの両方が表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-160">The debug view shows both key values and navigations.</span></span> <span data-ttu-id="a0c23-161">ナビゲーションは、関連エンティティの主キー値を使用して表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-161">Navigations are shown using the primary key values of the related entities.</span></span> <span data-ttu-id="a0c23-162">たとえば、 `Posts: [{Id: 1}, {Id: 2}]` 上記の出力では、 `Blog.Posts` コレクションナビゲーションに主キー1と2を持つ2つの関連する投稿が含まれていることを示しています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-162">For example, `Posts: [{Id: 1}, {Id: 2}]` in the output above indicates that the `Blog.Posts` collection navigation contains two related posts with primary keys 1 and 2 respectively.</span></span> <span data-ttu-id="a0c23-163">同様に、最初のブログに関連付けられている投稿ごとに、この `Blog: {Id: 1}` 行はナビゲーションが主キー1のブログを参照していることを示して `Post.Blog` います。</span><span class="sxs-lookup"><span data-stu-id="a0c23-163">Similarly, for each post associated with the first blog, the `Blog: {Id: 1}` line indicates that the `Post.Blog` navigation references the Blog with primary key 1.</span></span>

### <a name="fixup-to-locally-tracked-entities"></a><span data-ttu-id="a0c23-164">ローカルで追跡されるエンティティへの修正</span><span class="sxs-lookup"><span data-stu-id="a0c23-164">Fixup to locally tracked entities</span></span>

<span data-ttu-id="a0c23-165">リレーションシップの修正は、追跡クエリから返されるエンティティと、DbContext によって既に追跡されているエンティティの間でも行われます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-165">Relationship fixup also happens between entities returned from a tracking query and entities already tracked by the DbContext.</span></span> <span data-ttu-id="a0c23-166">たとえば、ブログ、投稿、および資産に対して3つの個別のクエリを実行することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-166">For example, consider executing three separate queries for blogs, posts, and assets:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var assets = context.Assets.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var posts = context.Posts.ToList();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
<span data-ttu-id="a0c23-167">[! code-csharp[Relationship_fixup_2](../../../ samples / core / ChangeTracking / ChangingFKsAndNavigations / OptionalRelationshipsSamples.cs ? name = Relationship_fixup_2
) ]もう一度デバッグビューを見て、最初のクエリが2つのブログだけを追跡した後に、次のように追跡します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-167">[!code-csharp[Relationship_fixup_2](../../../ samples / core / ChangeTracking / ChangingFKsAndNavigations / OptionalRelationshipsSamples.cs ? name = Relationship_fixup_2
) ] Looking again at the debug views, after the first query only the two blogs are tracked:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: []
```

<span data-ttu-id="a0c23-168">`Blog.Assets`参照ナビゲーションは null で `Blog.Posts` あり、関連付けられているエンティティがコンテキストによって現在追跡されていないため、コレクションナビゲーションは空です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-168">The `Blog.Assets` reference navigations are null, and the `Blog.Posts` collection navigations are empty because no associated entities are currently being tracked by the context.</span></span>

<span data-ttu-id="a0c23-169">2番目のクエリの後、 `Blogs.Assets` 参照ナビゲーションは、新しく追跡されたインスタンスを指すように修正されてい `BlogAsset` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-169">After the second query, the `Blogs.Assets` reference navigations have been fixed up to point to the newly tracked `BlogAsset` instances.</span></span> <span data-ttu-id="a0c23-170">同様に、 `BlogAssets.Blog` 参照ナビゲーションは、既に追跡されている適切なインスタンスを指すように設定され `Blog` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-170">Likewise, the `BlogAssets.Blog` reference navigations are set to point to the appropriate already tracked `Blog` instance.</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: []
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: []
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
```

<span data-ttu-id="a0c23-171">最後に、3番目のクエリの後に、 `Blog.Posts` コレクションのナビゲーションに関連するすべての投稿が含まれるようになり、 `Post.Blog` 参照が適切なインスタンスを指すようになりました `Blog` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-171">Finally, after the third query, the `Blog.Posts` collection navigations now contain all related posts, and the `Post.Blog` references point to the appropriate `Blog` instance:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: 1}
  Posts: [{Id: 1}, {Id: 2}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 1} Unchanged
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 2} Unchanged
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="a0c23-172">これは、元の単一のクエリで実現されたのと同じエンドステートです。これは、複数の異なるクエリを使用している場合でも、エンティティが追跡されていたため、EF Core は固定されているためです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-172">This is the same end-state as was achieved with the original single query, since EF Core fixed up navigations as entities were tracked, even when coming from multiple different queries.</span></span>

> [!NOTE]
> <span data-ttu-id="a0c23-173">Fixup によって、データベースからより多くのデータが返されることはありません。</span><span class="sxs-lookup"><span data-stu-id="a0c23-173">Fixup never causes more data to be returned from the database.</span></span> <span data-ttu-id="a0c23-174">クエリによって既に返されているエンティティ、または既に DbContext によって追跡されているエンティティのみを接続します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-174">It only connects entities that are already returned by the query or already tracked by the DbContext.</span></span> <span data-ttu-id="a0c23-175">エンティティをシリアル化するときに重複を処理する方法については、「 [EF Core の Id 解決](xref:core/change-tracking/identity-resolution) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-175">See [Identity Resolution in EF Core](xref:core/change-tracking/identity-resolution) for information about handling duplicates when serializing entities.</span></span>

## <a name="changing-relationships-using-navigations"></a><span data-ttu-id="a0c23-176">ナビゲーションを使用したリレーションシップの変更</span><span class="sxs-lookup"><span data-stu-id="a0c23-176">Changing relationships using navigations</span></span>

<span data-ttu-id="a0c23-177">2つのエンティティ間のリレーションシップを変更する最も簡単な方法は、ナビゲーションを操作し、逆のナビゲーションと FK 値を適切に修正するために EF Core を残したままにすることです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-177">The easiest way to change the relationship between two entities is by manipulating a navigation, while leaving EF Core to fixup the inverse navigation and FK values appropriately.</span></span> <span data-ttu-id="a0c23-178">このためには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="a0c23-178">This can be done by:</span></span>

- <span data-ttu-id="a0c23-179">コレクションナビゲーションからのエンティティの追加または削除。</span><span class="sxs-lookup"><span data-stu-id="a0c23-179">Adding or removing an entity from a collection navigation.</span></span>
- <span data-ttu-id="a0c23-180">別のエンティティを指すように参照ナビゲーションを変更するか、null に設定します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-180">Changing a reference navigation to point to a different entity, or setting it to null.</span></span>

### <a name="adding-or-removing-from-collection-navigations"></a><span data-ttu-id="a0c23-181">コレクションナビゲーションの追加または削除</span><span class="sxs-lookup"><span data-stu-id="a0c23-181">Adding or removing from collection navigations</span></span>

<span data-ttu-id="a0c23-182">たとえば、Visual Studio ブログから .NET ブログに投稿の1つを移動してみましょう。</span><span class="sxs-lookup"><span data-stu-id="a0c23-182">For example, let's move one of the posts from the Visual Studio blog to the .NET blog.</span></span> <span data-ttu-id="a0c23-183">そのためには、最初にブログと投稿を読み込んでから、1つのブログのナビゲーションコレクションからもう一方のブログのナビゲーションコレクションに投稿を移動する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-183">This requires first loading the blogs and posts, and then moving the post from the navigation collection on one blog to the navigation collection on the other blog:</span></span>

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");
        var vsBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == "Visual Studio Blog");

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Changing_relationships_using_navigations_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_1)]

> [!TIP]
> <span data-ttu-id="a0c23-184"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>ここでは、デバッグビューにアクセスしても、[変更の自動検出](xref:core/change-tracking/change-detection)が行われないため、への呼び出しが必要です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-184">A call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is needed here because accessing the debug view does not cause [automatic detection of changes](xref:core/change-tracking/change-detection).</span></span>

<span data-ttu-id="a0c23-185">上記のコードを実行した後に出力されるデバッグビューを次に示します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-185">This is the debug view printed after running the code above:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: <null>
  Posts: [{Id: 4}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: []
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
Post {Id: 4} Unchanged
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="a0c23-186">.NET ブログのナビゲーションには、 `Blog.Posts` 3 つの投稿 () が含まれるようになりました `Posts: [{Id: 1}, {Id: 2}, {Id: 3}]` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-186">The `Blog.Posts` navigation on the .NET Blog now has three posts (`Posts: [{Id: 1}, {Id: 2}, {Id: 3}]`).</span></span> <span data-ttu-id="a0c23-187">同様に、 `Blog.Posts` Visual Studio ブログのナビゲーションには、1つの投稿 () のみがあり `Posts: [{Id: 4}]` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-187">Likewise, the `Blog.Posts` navigation on the Visual Studio blog only has one post (`Posts: [{Id: 4}]`).</span></span> <span data-ttu-id="a0c23-188">このコードでは、これらのコレクションが明示的に変更されているため、これは想定されています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-188">This is to be expected since the code explicitly changed these collections.</span></span>

<span data-ttu-id="a0c23-189">興味深いことに、コードが明示的にナビゲーションを変更しなかった場合でも、 `Post.Blog` Visual Studio ブログ () を指すように修正されてい `Blog: {Id: 1}` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-189">More interestingly, even though the code did not explicitly change the `Post.Blog` navigation, it has been fixed-up to point to the Visual Studio blog (`Blog: {Id: 1}`).</span></span> <span data-ttu-id="a0c23-190">また、 `Post.BlogId` 外部キーの値は .net ブログの主キーの値と一致するように更新されています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-190">Also, the `Post.BlogId` foreign key value has been updated to match the primary key value of the .NET blog.</span></span> <span data-ttu-id="a0c23-191">この変更は、SaveChanges が呼び出されたときに、の FK 値に変更され、データベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-191">This change to the FK value in then persisted to the database when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='3' (DbType = String), @p0='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

### <a name="changing-reference-navigations"></a><span data-ttu-id="a0c23-192">参照ナビゲーションの変更</span><span class="sxs-lookup"><span data-stu-id="a0c23-192">Changing reference navigations</span></span>

<span data-ttu-id="a0c23-193">前の例では、各ブログの投稿のコレクションナビゲーションを操作して、投稿があるブログから別のブログに移動されました。</span><span class="sxs-lookup"><span data-stu-id="a0c23-193">In the previous example, a post was moved from one blog to another by manipulating the collection navigation of posts on each blog.</span></span> <span data-ttu-id="a0c23-194">代わりに、 `Post.Blog` 新しいブログを指すように参照ナビゲーションを変更することで、同じことを実現できます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-194">The same thing can be achieved by instead changing the `Post.Blog` reference navigation to point to the new blog.</span></span> <span data-ttu-id="a0c23-195">例:</span><span class="sxs-lookup"><span data-stu-id="a0c23-195">For example:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.Blog = dotNetBlog;
-->
[!code-csharp[Changing_relationships_using_navigations_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_navigations_2)]

<span data-ttu-id="a0c23-196">この変更の後のデバッグビューは、前の例と _まったく同じ_ です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-196">The debug view after this change is _exactly the same_ as it was in the previous example.</span></span> <span data-ttu-id="a0c23-197">これは、EF Core によって参照ナビゲーションの変更が検出され、コレクションナビゲーションと FK 値が一致するように修正されたためです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-197">This because EF Core detected the reference navigation change and then fixed up the collection navigations and FK value to match.</span></span>

## <a name="changing-relationships-using-foreign-key-values"></a><span data-ttu-id="a0c23-198">外部キー値を使用したリレーションシップの変更</span><span class="sxs-lookup"><span data-stu-id="a0c23-198">Changing relationships using foreign key values</span></span>

<span data-ttu-id="a0c23-199">前のセクションでは、外部キーの値を自動的に更新するナビゲーションによってリレーションシップが操作されていました。</span><span class="sxs-lookup"><span data-stu-id="a0c23-199">In the previous section, relationships were manipulated by navigations leaving foreign key values to be updated automatically.</span></span> <span data-ttu-id="a0c23-200">EF Core でリレーションシップを操作する場合は、この方法をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a0c23-200">This is the recommended way to manipulate relationships in EF Core.</span></span> <span data-ttu-id="a0c23-201">ただし、FK 値を直接操作することもできます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-201">However, it is also possible to manipulate FK values directly.</span></span> <span data-ttu-id="a0c23-202">たとえば、外部キーの値を変更することによって、投稿をあるブログから別のブログに移動でき `Post.BlogId` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-202">For example, we can move a post from one blog to another by changing the `Post.BlogId` foreign key value:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        post.BlogId = dotNetBlog.Id;
-->
[!code-csharp[Changing_relationships_using_foreign_key_values_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Changing_relationships_using_foreign_key_values_1)]

<span data-ttu-id="a0c23-203">前の例で示したように、これが参照ナビゲーションの変更と非常によく似ていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-203">Notice how this is very similar to changing the reference navigation, as shown in the previous example.</span></span>

<span data-ttu-id="a0c23-204">この変更後のデバッグビューは、前の2つの例の場合と _まったく同じ_ です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-204">The debug view after this change is again _exactly the same_ as was the case for the previous two examples.</span></span> <span data-ttu-id="a0c23-205">これは、EF Core FK 値の変更が検出され、参照とコレクションの両方のナビゲーションが一致するように修正されたためです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-205">This because EF Core detected the FK value change and then fixed up both the reference and collection navigations to match.</span></span>

> [!TIP]
> <span data-ttu-id="a0c23-206">リレーションシップが変更されるたびにすべてのナビゲーション値と FK 値を操作するコードを記述しないでください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-206">Do not write code to manipulate all navigations and FK values each time a relationship changes.</span></span> <span data-ttu-id="a0c23-207">このようなコードはより複雑で、すべてのケースで外部キーとナビゲーションに対して一貫した変更を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-207">Such code is more complicated and must ensure consistent changes to foreign keys and navigations in every case.</span></span> <span data-ttu-id="a0c23-208">可能であれば、1つのナビゲーションを操作するか、両方の移動を操作するだけです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-208">If possible, just manipulate a single navigation, or maybe both navigations.</span></span> <span data-ttu-id="a0c23-209">必要に応じて、FK 値のみを操作します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-209">If needed, just manipulate FK values.</span></span> <span data-ttu-id="a0c23-210">ナビゲーション値と FK 値の両方を操作しないようにします。</span><span class="sxs-lookup"><span data-stu-id="a0c23-210">Avoid manipulating both navigations and FK values.</span></span>

## <a name="fixup-for-added-or-deleted-entities"></a><span data-ttu-id="a0c23-211">追加または削除されたエンティティの修正</span><span class="sxs-lookup"><span data-stu-id="a0c23-211">Fixup for added or deleted entities</span></span>

### <a name="adding-to-a-collection-navigation"></a><span data-ttu-id="a0c23-212">コレクションナビゲーションへの追加</span><span class="sxs-lookup"><span data-stu-id="a0c23-212">Adding to a collection navigation</span></span>

<span data-ttu-id="a0c23-213">EF Core は、新しい依存/子エンティティがコレクションのナビゲーションに追加されたことを [検出](xref:core/change-tracking/change-detection) すると、次のアクションを実行します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-213">EF Core performs the following actions when it [detects](xref:core/change-tracking/change-detection) that a new dependent/child entity has been added to a collection navigation:</span></span>

- <span data-ttu-id="a0c23-214">エンティティが追跡されていない場合は、追跡されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-214">If the entity is not tracked, then it is tracked.</span></span> <span data-ttu-id="a0c23-215">(エンティティは通常、状態になり `Added` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-215">(The entity will usually be in the `Added` state.</span></span> <span data-ttu-id="a0c23-216">ただし、生成されたキーを使用するようにエンティティ型が構成されていて、主キーの値が設定されている場合、エンティティは状態で追跡され `Unchanged` ます)。</span><span class="sxs-lookup"><span data-stu-id="a0c23-216">However, if the entity type is configured to use generated keys and the primary key value is set, then the entity is tracked in the `Unchanged` state.)</span></span>
- <span data-ttu-id="a0c23-217">エンティティが別のプリンシパル/親に関連付けられている場合、そのリレーションシップは切断されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-217">If the entity is associated with a different principal/parent, then that relationship is severed.</span></span>
- <span data-ttu-id="a0c23-218">エンティティは、コレクションナビゲーションを所有するプリンシパル/親に関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-218">The entity becomes associated with the principal/parent that owns the collection navigation.</span></span>
- <span data-ttu-id="a0c23-219">ナビゲーションと外部キーの値は、関連するすべてのエンティティに対して固定されています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-219">Navigations and foreign key values are fixed up for all entities involved.</span></span>

<span data-ttu-id="a0c23-220">これに基づいて、投稿をあるブログから別のブログに移動することができます。新しいに追加する前に、古いコレクションのナビゲーションから削除する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a0c23-220">Based on this we can see that to move a post from one blog to another we don't actually need to remove it from the old collection navigation before adding it to the new.</span></span> <span data-ttu-id="a0c23-221">このため、上記の例のコードは次のように変更できます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-221">So the code from the example above can be changed from:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_1)]

<span data-ttu-id="a0c23-222">移動先:</span><span class="sxs-lookup"><span data-stu-id="a0c23-222">To:</span></span>

<!--
        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        dotNetBlog.Posts.Add(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_2)]

<span data-ttu-id="a0c23-223">EF Core は、投稿が新しいブログに追加されていることを確認し、最初のブログのコレクションから自動的に削除します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-223">EF Core sees that the post has been added to a new blog and automatically removes it from the collection on the first blog.</span></span>

### <a name="removing-from-a-collection-navigation"></a><span data-ttu-id="a0c23-224">コレクションのナビゲーションからの削除</span><span class="sxs-lookup"><span data-stu-id="a0c23-224">Removing from a collection navigation</span></span>

<span data-ttu-id="a0c23-225">プリンシパル/親のコレクションナビゲーションから依存/子エンティティを削除すると、そのプリンシパル/親とのリレーションシップの切断が発生します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-225">Removing a dependent/child entity from the collection navigation of the principal/parent causes severing of the relationship to that principal/parent.</span></span> <span data-ttu-id="a0c23-226">次の処理は、リレーションシップが省略可能か必須かによって異なります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-226">What happens next depends on whether the relationship is optional or required.</span></span>

#### <a name="optional-relationships"></a><span data-ttu-id="a0c23-227">省略可能なリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="a0c23-227">Optional relationships</span></span>

<span data-ttu-id="a0c23-228">既定では、オプションのリレーションシップの場合、外部キーの値は null に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-228">By default for optional relationships, the foreign key value is set to null.</span></span> <span data-ttu-id="a0c23-229">これは、依存/子 _がプリンシパル/_ 親に関連付けられなくなったことを意味します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-229">This means that the dependent/child is no longer associated with _any_ principal/parent.</span></span> <span data-ttu-id="a0c23-230">たとえば、ブログや投稿を読み込んで、コレクションのナビゲーションから投稿の1つを削除してみましょう `Blog.Posts` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-230">For example, let's load a blog and posts and then remove one of the posts from the `Blog.Posts` collection navigation:</span></span>

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_3)]

<span data-ttu-id="a0c23-231">この変更後に、 [変更追跡デバッグビューを確認](xref:core/change-tracking/debug-views) すると、次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-231">Looking at the [change tracking debug view](xref:core/change-tracking/debug-views) after this change shows that:</span></span>

- <span data-ttu-id="a0c23-232">`Post.BlogId`FK が null () に設定されています `BlogId: <null> FK Modified Originally 1` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-232">The `Post.BlogId` FK has been set to null (`BlogId: <null> FK Modified Originally 1`)</span></span>
- <span data-ttu-id="a0c23-233">`Post.Blog`参照のナビゲーションが null () に設定されています `Blog: <null>` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-233">The `Post.Blog` reference navigation has been set to null (`Blog: <null>`)</span></span>
- <span data-ttu-id="a0c23-234">投稿は `Blog.Posts` コレクションナビゲーション () から削除されました `Posts: [{Id: 1}]`</span><span class="sxs-lookup"><span data-stu-id="a0c23-234">The post has been removed from `Blog.Posts` collection navigation (`Posts: [{Id: 1}]`)</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="a0c23-235">投稿がとしてマークされて _いない_ ことに注意して `Deleted` ください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-235">Notice that the post is _not_ marked as `Deleted`.</span></span> <span data-ttu-id="a0c23-236">`Modified`SaveChanges が呼び出されたときに、データベース内の FK 値が null に設定されるように、としてマークされています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-236">It is marked as `Modified` so that the FK value in the database will be set to null when SaveChanges is called.</span></span>

#### <a name="required-relationships"></a><span data-ttu-id="a0c23-237">必須リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="a0c23-237">Required relationships</span></span>

<span data-ttu-id="a0c23-238">FK 値を null に設定することはできません (通常は、必要なリレーションシップに対しては使用できません)。</span><span class="sxs-lookup"><span data-stu-id="a0c23-238">Setting the FK value to null is not allowed (and is usually not possible) for required relationships.</span></span> <span data-ttu-id="a0c23-239">したがって、必要なリレーションシップを切断するには、依存/子エンティティが新しいプリンシパル/親に再親されるか、または SaveChanges が呼び出されて参照制約違反が発生しないようにデータベースから削除される必要があります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-239">Therefore, severing a required relationship means that the dependent/child entity must be either re-parented to a new principal/parent, or removed from the database when SaveChanges is called to avoid a referential constraint violation.</span></span> <span data-ttu-id="a0c23-240">これは "孤立を削除しています" と呼ばれ、必要なリレーションシップの EF Core の既定の動作です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-240">This is known as "deleting orphans", and is the default behavior in EF Core for required relationships.</span></span>

<span data-ttu-id="a0c23-241">たとえば、ブログと投稿の関係を必要に応じて変更し、前の例と同じコードを実行してみましょう。</span><span class="sxs-lookup"><span data-stu-id="a0c23-241">For example, let's change the relationship between blog and posts to be required and then run the same code as in the previous example:</span></span>

<!--
        var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
        dotNetBlog.Posts.Remove(post);
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_4)]

<span data-ttu-id="a0c23-242">この変更の後にデバッグビューを表示すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-242">Looking at the debug view after this change shows that:</span></span>

- <span data-ttu-id="a0c23-243">この投稿は、 `Deleted` SaveChanges が呼び出されたときにデータベースから削除されるようにマークされています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-243">The post has been marked as `Deleted` such that it will be deleted from the database when SaveChanges is called.</span></span>
- <span data-ttu-id="a0c23-244">`Post.Blog`参照ナビゲーションが null () に設定されてい `Blog: <null>` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-244">The `Post.Blog` reference navigation has been set to null (`Blog: <null>`).</span></span>
- <span data-ttu-id="a0c23-245">投稿は `Blog.Posts` コレクションナビゲーション () から削除されました `Posts: [{Id: 1}]` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-245">The post has been removed from `Blog.Posts` collection navigation (`Posts: [{Id: 1}]`).</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: <null>
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="a0c23-246">は、必要なリレーションシップがあるため、変更されていないことに注意し `Post.BlogId` てください。 null に設定することはできません。</span><span class="sxs-lookup"><span data-stu-id="a0c23-246">Notice that the `Post.BlogId` remains unchanged since for a required relationship it cannot be set to null.</span></span>

<span data-ttu-id="a0c23-247">SaveChanges を呼び出すと、孤立した投稿が削除されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-247">Calling SaveChanges results in the orphaned post being deleted:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

#### <a name="delete-orphans-timing-and-re-parenting"></a><span data-ttu-id="a0c23-248">孤立したタイミングの削除と親の再親</span><span class="sxs-lookup"><span data-stu-id="a0c23-248">Delete orphans timing and re-parenting</span></span>

<span data-ttu-id="a0c23-249">既定では、 `Deleted` リレーションシップの変更が [検出](xref:core/change-tracking/change-detection)されるとすぐに、孤立をマークします。</span><span class="sxs-lookup"><span data-stu-id="a0c23-249">By default, marking orphans as `Deleted` happens as soon as the relationship change is [detected](xref:core/change-tracking/change-detection).</span></span> <span data-ttu-id="a0c23-250">ただし、このプロセスは、SaveChanges が実際に呼び出されるまで遅延させることができます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-250">However, this process can be delayed until SaveChanges is actually called.</span></span> <span data-ttu-id="a0c23-251">これは、1つのプリンシパル/親から削除されたエンティティの孤立を回避するのに役立ちますが、SaveChanges が呼び出される前に、新しいプリンシパル/親で親が再設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-251">This can be useful to avoid making orphans of entities that have been removed from one principal/parent, but will be re-parented with a new principal/parent before SaveChanges is called.</span></span> <span data-ttu-id="a0c23-252"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> は、このタイミングを設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-252"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DeleteOrphansTiming?displayProperty=nameWithType> is used to set this timing.</span></span> <span data-ttu-id="a0c23-253">例:</span><span class="sxs-lookup"><span data-stu-id="a0c23-253">For example:</span></span>

<!--
        context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;

        var post = vsBlog.Posts.Single(e => e.Title.StartsWith("Disassembly improvements"));
        vsBlog.Posts.Remove(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        dotNetBlog.Posts.Add(post);

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_5)]

<span data-ttu-id="a0c23-254">最初のコレクションから投稿を削除した後、オブジェクトは前の例のようにマークされていません `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-254">After removing the post from the first collection the object is not marked as `Deleted` as it was in the previous example.</span></span> <span data-ttu-id="a0c23-255">この場合、 _必要なリレーションシップ_ があるにもかかわらず、リレーションシップが切断されていることを追跡しているのは EF Core です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-255">Instead, EF Core is tracking that the relationship is severed _even though this is a required relationship_.</span></span> <span data-ttu-id="a0c23-256">(FK 値は null と見なされますが、null になることはありませんが、型は null 値を許容しないため、null になることはありません EF Core。</span><span class="sxs-lookup"><span data-stu-id="a0c23-256">(The FK value is considered null by EF Core even though it cannot really be null because the type is not nullable.</span></span> <span data-ttu-id="a0c23-257">これは "概念上の null" と呼ばれます)。</span><span class="sxs-lookup"><span data-stu-id="a0c23-257">This is known as a "conceptual null".)</span></span>

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="a0c23-258">この時点で SaveChanges を呼び出すと、孤立した投稿が削除されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-258">Calling SaveChanges at this time would result in the orphaned post being deleted.</span></span> <span data-ttu-id="a0c23-259">ただし、上記の例のように、SaveChanges が呼び出される前に、post が新しいブログに関連付けられている場合、その新しいブログに適切に修正され、孤立と見なされなくなります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-259">However, if as in the example above, post is associated with a new blog before SaveChanges is called, then it will be fixed up appropriately to that new blog and is no longer considered an orphan:</span></span>

```output
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: 1 FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 1}
  Tags: []
```

<span data-ttu-id="a0c23-260">この時点で SaveChanges が呼び出されると、データベース内の投稿は削除されるのではなく更新されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-260">SaveChanges called at this point will update the post in the database rather than deleting it.</span></span>

<span data-ttu-id="a0c23-261">孤立を自動的に削除しないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-261">It is also possible to turn off automatic deletion of orphans.</span></span> <span data-ttu-id="a0c23-262">これにより、孤立したの追跡中に SaveChanges が呼び出された場合、例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-262">This will result in an exception if SaveChanges is called while an orphan is being tracked.</span></span> <span data-ttu-id="a0c23-263">たとえば、次のコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-263">For example, this code:</span></span>

<!--
                var dotNetBlog = context.Blogs.Include(e => e.Posts).Single(e => e.Name == ".NET Blog");

                context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.Never;

                var post = dotNetBlog.Posts.Single(e => e.Title == "Announcing F# 5");
                dotNetBlog.Posts.Remove(post);

                context.SaveChanges(); // Throws
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/RequiredRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_6)]

<span data-ttu-id="a0c23-264">では、次の例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-264">Will throw this exception:</span></span>

> <span data-ttu-id="a0c23-265">InvalidOperationException: エンティティ ' Blog ' と ' Post ' の間のキー値 ' {Blog Id: 1} ' の関連付けが切断されましたが、リレーションシップは必須としてマークされているか、外部キーが null 許容ではないため、暗黙的に必要です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-265">System.InvalidOperationException: The association between entities 'Blog' and 'Post' with the key value '{BlogId: 1}' has been severed, but the relationship is either marked as required or is implicitly required because the foreign key is not nullable.</span></span> <span data-ttu-id="a0c23-266">必要なリレーションシップが存在しないときに依存/子エンティティを削除する必要がある場合は、連鎖削除を使用するようにリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-266">If the dependent/child entity should be deleted when a required relationship is severed, configure the relationship to use cascade deletes.</span></span>

<span data-ttu-id="a0c23-267">を呼び出すことにより、孤立した削除と連鎖削除をいつでも削除でき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-267">Deletion of orphans, as well as cascade deletes, can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a0c23-268">これを、削除の孤立したタイミングをに設定して組み合わせると、 `Never` EF Core に明示的に指示されていない限り、孤立が削除されることはありません。</span><span class="sxs-lookup"><span data-stu-id="a0c23-268">Combining this with setting the delete orphan timing to `Never` will ensure orphans are never deleted unless EF Core is explicitly instructed to do so.</span></span>

### <a name="changing-a-reference-navigation"></a><span data-ttu-id="a0c23-269">参照ナビゲーションの変更</span><span class="sxs-lookup"><span data-stu-id="a0c23-269">Changing a reference navigation</span></span>

<span data-ttu-id="a0c23-270">一対多リレーションシップの参照ナビゲーションを変更すると、リレーションシップのもう一方の端のコレクションナビゲーションを変更するのと同じ効果があります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-270">Changing the reference navigation of a one-to-many relationship has the same effect as changing the collection navigation on the other end of the relationship.</span></span> <span data-ttu-id="a0c23-271">依存/子の参照ナビゲーションを null に設定することは、プリンシパル/親のコレクションナビゲーションからエンティティを削除することと同じです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-271">Setting the reference navigation of dependent/child to null is equivalent to removing the entity from the collection navigation of the principal/parent.</span></span> <span data-ttu-id="a0c23-272">すべての修正とデータベースの変更は、前のセクションで説明したように行われます。これには、リレーションシップが必要な場合にエンティティを孤立させることも含まれます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-272">All fixup and database changes happen as described in the previous section, including making the entity an orphan if the relationship is required.</span></span>

#### <a name="optional-one-to-one-relationships"></a><span data-ttu-id="a0c23-273">省略可能な1対1のリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="a0c23-273">Optional one-to-one relationships</span></span>

<span data-ttu-id="a0c23-274">一対一のリレーションシップでは、参照ナビゲーションを変更すると、以前のリレーションシップが切断されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-274">For one-to-one relationships, changing a reference navigation causes any previous relationship to be severed.</span></span> <span data-ttu-id="a0c23-275">オプションのリレーションシップの場合、これは、前に関連付けられた依存/子の FK 値が null に設定されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-275">For optional relationships, this means that the FK value on the previously related dependent/child is set to null.</span></span> <span data-ttu-id="a0c23-276">例:</span><span class="sxs-lookup"><span data-stu-id="a0c23-276">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var dotNetBlog = context.Blogs.Include(e => e.Assets).Single(e => e.Name == ".NET Blog");
        dotNetBlog.Assets = new BlogAssets();

        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Fixup_for_added_or_deleted_entities_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Fixup_for_added_or_deleted_entities_7)]

<span data-ttu-id="a0c23-277">SaveChanges を呼び出す前のデバッグビューでは、新しいアセットが既存のアセットを置き換えることを示しています。これは現在、null の FK 値でとマークされてい `Modified` `BlogAssets.BlogId` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-277">The debug view before calling SaveChanges shows that the new assets has replaced the existing assets, which is now marked as `Modified` with a null `BlogAssets.BlogId` FK value:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482629}
  Posts: []
BlogAssets {Id: -2147482629} Added
  Id: -2147482629 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Modified
  Id: 1 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 1
  Blog: <null>
```

<span data-ttu-id="a0c23-278">この結果、SaveChanges が呼び出されると、更新と挿入が行われます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-278">This results in an update and an insert when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Assets" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2=NULL, @p3='1' (Nullable = true) (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p2, @p3);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

#### <a name="required-one-to-one-relationships"></a><span data-ttu-id="a0c23-279">必要な一対一のリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="a0c23-279">Required one-to-one relationships</span></span>

<span data-ttu-id="a0c23-280">前の例と同じコードを実行していますが、今回は必要な1対1のリレーションシップを持つとして、以前に関連付けられていたが、新しいの場所では孤立している `BlogAssets` ため、としてマークされてい `Deleted` `BlogAssets` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-280">Running the same code as in the previous example, but this time with a required one-to-one relationship, shows that the previously associated `BlogAssets` is now marked as `Deleted`, since it becomes an orphan when the new `BlogAssets` takes its place:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Assets: {Id: -2147482639}
  Posts: []
BlogAssets {Id: -2147482639} Added
  Id: -2147482639 PK Temporary
  Banner: <null>
  BlogId: 1 FK
  Blog: {Id: 1}
BlogAssets {Id: 1} Deleted
  Id: 1 PK
  Banner: <null>
  BlogId: 1 FK
  Blog: <null>
```

<span data-ttu-id="a0c23-281">これにより、SaveChanges が呼び出されたときに、が削除され、が挿入されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-281">This then results in an delete an and insert when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Assets"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1=NULL, @p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Assets" ("Banner", "BlogId")
VALUES (@p1, @p2);
SELECT "Id"
FROM "Assets"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="a0c23-282">孤立を削除済みとしてマークするタイミングは、コレクションのナビゲーションと同じように変更でき、同じ効果があります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-282">The timing of marking orphans as deleted can be changed in the same way as shown for collection navigations and has the same effects.</span></span>

### <a name="deleting-an-entity"></a><span data-ttu-id="a0c23-283">エンティティの削除</span><span class="sxs-lookup"><span data-stu-id="a0c23-283">Deleting an entity</span></span>

#### <a name="optional-relationships"></a><span data-ttu-id="a0c23-284">省略可能なリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="a0c23-284">Optional relationships</span></span>

<span data-ttu-id="a0c23-285">エンティティがとしてマークされている場合 `Deleted` (たとえば、を呼び出すことによって)、削除された <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> エンティティへの参照は、他のエンティティのナビゲーションから削除されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-285">When an entity is marked as `Deleted`, for example by calling <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>, then references to the deleted entity are removed from the navigations of other entities.</span></span> <span data-ttu-id="a0c23-286">オプションのリレーションシップでは、依存エンティティの FK 値は null に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-286">For optional relationships, the FK values in dependent entities are set to null.</span></span>

<span data-ttu-id="a0c23-287">たとえば、Visual Studio ブログを次のようにマークし `Deleted` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-287">For example, let's mark the Visual Studio blog as `Deleted`:</span></span>

<!--
        using var context = new BlogsContext();

        var vsBlog = context.Blogs
            .Include(e => e.Posts)
            .Include(e => e.Assets)
            .Single(e => e.Name == "Visual Studio Blog");

        context.Remove(vsBlog);

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);

        context.SaveChanges();
-->
[!code-csharp[Deleting_an_entity_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Deleting_an_entity_1)]

<span data-ttu-id="a0c23-288">SaveChanges を呼び出す前に、 [変更トラッカーのデバッグビューを確認](xref:core/change-tracking/debug-views) すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-288">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows:</span></span>

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Modified
  Id: 2 PK
  Banner: <null>
  BlogId: <null> FK Modified Originally 2
  Blog: <null>
Post {Id: 3} Modified
  Id: 3 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: []
Post {Id: 4} Modified
  Id: 4 PK
  BlogId: <null> FK Modified Originally 2
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: []
```

<span data-ttu-id="a0c23-289">次のことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-289">Notice that:</span></span>

- <span data-ttu-id="a0c23-290">ブログはとしてマークされて `Deleted` います。</span><span class="sxs-lookup"><span data-stu-id="a0c23-290">The blog is marked as `Deleted`.</span></span>
- <span data-ttu-id="a0c23-291">削除されたブログに関連する資産に、null の FK 値 ( `BlogId: <null> FK Modified Originally 2` ) と null 参照のナビゲーション () が含まれています。 `Blog: <null>`</span><span class="sxs-lookup"><span data-stu-id="a0c23-291">The assets related to the deleted blog has a null FK value (`BlogId: <null> FK Modified Originally 2`) and a null reference navigation (`Blog: <null>`)</span></span>
- <span data-ttu-id="a0c23-292">削除されたブログに関連する各投稿には、null の FK 値 ( `BlogId: <null> FK Modified Originally 2` ) と null 参照のナビゲーション () があります。 `Blog: <null>`</span><span class="sxs-lookup"><span data-stu-id="a0c23-292">Each post related to the deleted blog has a null FK value (`BlogId: <null> FK Modified Originally 2`) and a null reference navigation (`Blog: <null>`)</span></span>

#### <a name="required-relationships"></a><span data-ttu-id="a0c23-293">必須リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="a0c23-293">Required relationships</span></span>

<span data-ttu-id="a0c23-294">必要なリレーションシップの fixup 動作は、オプションのリレーションシップの場合と同じです。ただし、依存/子エンティティは、 `Deleted` プリンシパル/親がなければ存在できず、参照制約の例外を回避するために SaveChanges が呼び出されたときにデータベースから削除される必要があるためです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-294">The fixup behavior for required relationships is the same as for optional relationships except that the dependent/child entities are marked as `Deleted` since they cannot exist without a principal/parent and must be removed from the database when SaveChanges is called to avoid a referential constraint exception.</span></span> <span data-ttu-id="a0c23-295">これは "連鎖削除" と呼ばれ、必要なリレーションシップの EF Core の既定の動作です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-295">This is known as "cascade delete", and is the default behavior in EF Core for required relationships.</span></span> <span data-ttu-id="a0c23-296">たとえば、前の例と同じコードを実行し、必要なリレーションシップを指定すると、SaveChanges が呼び出される前に、次のデバッグビューが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-296">For example, running the same code as in the previous example but with a required relationship results in the following debug view before SaveChanges is called:</span></span>

```output
Blog {Id: 2} Deleted
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Assets: {Id: 2}
  Posts: [{Id: 3}, {Id: 4}]
BlogAssets {Id: 2} Deleted
  Id: 2 PK
  Banner: <null>
  BlogId: 2 FK
  Blog: {Id: 2}
Post {Id: 3} Deleted
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: {Id: 2}
  Tags: []
```

<span data-ttu-id="a0c23-297">予想どおり、依存/子はとしてマークされ `Deleted` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-297">As expected, the dependents/children are now marked as `Deleted`.</span></span> <span data-ttu-id="a0c23-298">ただし、削除されたエンティティのナビゲーションが変更されて _いない_ ことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-298">However, notice that the navigations on the deleted entities have _not_ changed.</span></span> <span data-ttu-id="a0c23-299">これは奇妙に思えるかもしれませんが、すべてのナビゲーションをクリアすることで、削除されたエンティティのグラフを完全にシュレッダ処理することを回避できます</span><span class="sxs-lookup"><span data-stu-id="a0c23-299">This may seem strange, but it avoids completely shredding a deleted graph of entities by clearing all navigations.</span></span> <span data-ttu-id="a0c23-300">つまり、ブログ、資産、および投稿は、削除後もエンティティのグラフを形成します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-300">That is, the blog, asset, and posts still form a graph of entities even after having been deleted.</span></span> <span data-ttu-id="a0c23-301">これにより、グラフが細分化されている EF6 の場合よりも、エンティティのグラフの削除が大幅に簡単になります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-301">This makes it much easier to un-delete a graph of entities than was the case in EF6 where the graph was shredded.</span></span>

#### <a name="cascade-delete-timing-and-re-parenting"></a><span data-ttu-id="a0c23-302">連鎖削除のタイミングと再親</span><span class="sxs-lookup"><span data-stu-id="a0c23-302">Cascade delete timing and re-parenting</span></span>

<span data-ttu-id="a0c23-303">既定では、cascade delete は親/プリンシパルがとしてマークされるとすぐに発生し `Deleted` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-303">By default, cascade delete happens as soon as the parent/principal is marked as `Deleted`.</span></span> <span data-ttu-id="a0c23-304">これは、前に説明したように、孤立を削除する場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-304">This is the same as for deleting orphans, as described previously.</span></span> <span data-ttu-id="a0c23-305">孤立を削除するのと同様に、このプロセスは、SaveChanges が呼び出されるか、完全に無効にされるまで遅延することができ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-305">As with deleting orphans, this process can be delayed until SaveChanges is called, or even disabled entirely, by setting <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeDeleteTiming?displayProperty=nameWithType> appropriately.</span></span> <span data-ttu-id="a0c23-306">これは、孤立を削除する場合と同じように役立ちます。たとえば、プリンシパル/親を削除した後で、子/依存関係を再親することができます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-306">This is useful in the same way as it is for deleting orphans, including for re-parenting children/dependents after deletion of a principal/parent.</span></span>

<span data-ttu-id="a0c23-307">連鎖削除、およびオーファンの削除は、を呼び出すことによっていつでも強制でき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-307">Cascade deletes, as well as deleting orphans, can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a0c23-308">連鎖削除のタイミングをに設定してこれを組み合わせると、 `Never` EF Core が明示的に指定されていない限り、連鎖削除は行われません。</span><span class="sxs-lookup"><span data-stu-id="a0c23-308">Combining this with setting the cascade delete timing to `Never` will ensure cascade deletes never happen unless EF Core is explicitly instructed to do so.</span></span>

> [!TIP]
> <span data-ttu-id="a0c23-309">連鎖削除および孤立削除は密接に関連しています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-309">Cascade delete and deleting orphans are closely related.</span></span> <span data-ttu-id="a0c23-310">どちらの場合も、必要なプリンシパル/親とのリレーションシップが切断されると、依存/子エンティティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-310">Both result in deleting dependent/child entities when the relationship to their required principal/parent is severed.</span></span> <span data-ttu-id="a0c23-311">Cascade delete の場合、この切断は、プリンシパル/親自体が削除されているために発生します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-311">For cascade delete, this severing happens because the principal/parent is itself deleted.</span></span> <span data-ttu-id="a0c23-312">孤立した場合、プリンシパル/親エンティティは依然として存在しますが、依存/子エンティティに関連付けられなくなります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-312">For orphans, the principal/parent entity still exists, but is no longer related to the dependent/child entities.</span></span>

## <a name="many-to-many-relationships"></a><span data-ttu-id="a0c23-313">多対多のリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="a0c23-313">Many-to-many relationships</span></span>

<span data-ttu-id="a0c23-314">EF Core 内の多対多リレーションシップは、結合エンティティを使用して実装されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-314">Many-to-many relationships in EF Core are implemented using a join entity.</span></span> <span data-ttu-id="a0c23-315">多対多リレーションシップは、一対多リレーションシップを持つこの結合エンティティに関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-315">Each side the many-to-many relationship is related to this join entity with a one-to-many relationship.</span></span> <span data-ttu-id="a0c23-316">5.0 EF Core する前に、この結合エンティティは明示的に定義およびマップする必要がありました。</span><span class="sxs-lookup"><span data-stu-id="a0c23-316">Before EF Core 5.0, this join entity had to explicitly defined and mapped.</span></span> <span data-ttu-id="a0c23-317">EF Core 5.0 以降では、暗黙的かつ非表示にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-317">Starting with EF Core 5.0, it can be created implicitly and hidden.</span></span> <span data-ttu-id="a0c23-318">ただし、どちらの場合も、基になる動作は同じです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-318">However, in both cases the underlying behavior is the same.</span></span> <span data-ttu-id="a0c23-319">ここでは、この基になる動作を見て、多対多リレーションシップの追跡のしくみについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-319">We will look at this underlying behavior first to understand how tracking of many-to-many relationships works.</span></span>

### <a name="how-many-to-many-relationships-work"></a><span data-ttu-id="a0c23-320">多対多リレーションシップの動作</span><span class="sxs-lookup"><span data-stu-id="a0c23-320">How many-to-many relationships work</span></span>

<span data-ttu-id="a0c23-321">明示的に定義された結合エンティティ型を使用して、投稿とタグの間に多対多のリレーションシップを作成するこの EF Core モデルを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="a0c23-321">Consider this EF Core model that creates a many-to-many relationship between posts and tags using an explicitly defined join entity type:</span></span>

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
    -->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Model)]

<span data-ttu-id="a0c23-322">`PostTag`結合エンティティ型には、2つの外部キープロパティが含まれていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-322">Notice that the `PostTag` join entity type contains two foreign key properties.</span></span> <span data-ttu-id="a0c23-323">このモデルでは、タグに関連する投稿の場合、 `PostTag.PostId` 外部キーの値が主キーの値と一致し、外部キーの値が主キーの値と一致するポストタグ結合エンティティが必要 `Post.Id` `PostTag.TagId` `Tag.Id` です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-323">In this model, for a post to be related to a tag, there must be a PostTag join entity where the `PostTag.PostId` foreign key value matches the `Post.Id` primary key value, and where the `PostTag.TagId` foreign key value matches the `Tag.Id` primary key value.</span></span> <span data-ttu-id="a0c23-324">例:</span><span class="sxs-lookup"><span data-stu-id="a0c23-324">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_1](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_1)]

<span data-ttu-id="a0c23-325">このコードを実行した後に、 [変更トラッカーデバッグビュー](xref:core/change-tracking/debug-views) を見ると、post とタグが新しい結合エンティティによって関連付けられていることがわかり `PostTag` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-325">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after running this code shows that the post and tag are related by the new `PostTag` join entity:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
```

<span data-ttu-id="a0c23-326">とでのコレクションのナビゲーションが修正されていることに注意して `Post` `Tag` `PostTag` ください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-326">Notice that the collection navigations on `Post` and `Tag` have been fixed up, as have the reference navigations on `PostTag`.</span></span> <span data-ttu-id="a0c23-327">これらのリレーションシップは、前の例と同様に、FK 値ではなく、ナビゲーションによって操作できます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-327">These relationships can be manipulated by navigations instead of FK values, just as in all the preceding examples.</span></span> <span data-ttu-id="a0c23-328">たとえば、上記のコードを変更して、結合エンティティに対する参照ナビゲーションを設定することにより、リレーションシップを追加できます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-328">For example, the code above can be modified to add the relationship by setting the reference navigations on the join entity:</span></span>

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_2](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntitySamples.cs?name=Many_to_many_relationships_2)]

<span data-ttu-id="a0c23-329">これにより、前の例のように、FKs とナビゲーションがまったく同じ変更になります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-329">This results in exactly the same change to FKs and navigations as in the previous example.</span></span>

### <a name="skip-navigations"></a><span data-ttu-id="a0c23-330">ナビゲーションのスキップ</span><span class="sxs-lookup"><span data-stu-id="a0c23-330">Skip navigations</span></span>

> [!NOTE]
> <span data-ttu-id="a0c23-331">スキップナビゲーションは EF Core 5.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="a0c23-331">Skip navigations were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="a0c23-332">結合テーブルの手動操作は煩雑になることがあります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-332">Manipulating the join table manually can be cumbersome.</span></span> <span data-ttu-id="a0c23-333">EF Core 5.0 以降、多対多リレーションシップは、結合エンティティを "スキップ" する特別なコレクションナビゲーションを使用して直接操作できます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-333">Starting with EF Core 5.0, many-to-many relationships can be manipulated directly using special collection navigations that "skip over" the join entity.</span></span> <span data-ttu-id="a0c23-334">たとえば、上記のモデルには、2つのスキップナビゲーションを追加できます。1つは Post からタグに、もう1つはタグから投稿までです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-334">For example, two skip navigations can be added to the model above; one from Post to Tags, and the other from Tag to Posts:</span></span>

<!--
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }

        public IList<Tag> Tags { get; } = new List<Tag>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class Tag
    {
        public int Id { get; set; }
        public string Text { get; set; }

        public IList<Post> Posts { get; } = new List<Post>(); // Skip collection navigation
        public IList<PostTag> PostTags { get; } = new List<PostTag>(); // Collection navigation
    }

    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public Post Post { get; set; } // Reference navigation
        public Tag Tag { get; set; } // Reference navigation
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Model)]

<span data-ttu-id="a0c23-335">この多対多リレーションシップでは、スキップナビゲーションと通常のナビゲーションがすべて同じ多対多リレーションシップで使用されるようにするために、次の構成が必要です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-335">This many-to-many relationship requires the following configuration to ensure the skip navigations and normal navigations are all used for the same many-to-many relationship:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne(t => t.Tag).WithMany(p => p.PostTags),
                    j => j.HasOne(t => t.Post).WithMany(p => p.PostTags));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=OnModelCreating)]

<span data-ttu-id="a0c23-336">多対多リレーションシップのマッピングの詳細については、「 [リレーションシップ](xref:core/modeling/relationships) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-336">See [Relationships](xref:core/modeling/relationships) for more information on mapping many-to-many relationships.</span></span>

<span data-ttu-id="a0c23-337">ナビゲーションをスキップし、通常のコレクションのナビゲーションと同様に動作します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-337">Skip navigations look and behave like normal collection navigations.</span></span> <span data-ttu-id="a0c23-338">ただし、外部キーの値を操作する方法は異なります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-338">However, the way they work with foreign key values is different.</span></span> <span data-ttu-id="a0c23-339">投稿をタグに関連付けますが、今回はスキップナビゲーションを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-339">Let's associate a post with a tag, but this time using a skip navigation:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_3](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_3)]

<span data-ttu-id="a0c23-340">このコードは join エンティティを使用しないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-340">Notice that this code doesn't use the join entity.</span></span> <span data-ttu-id="a0c23-341">代わりに、1対多のリレーションシップの場合と同じように、ナビゲーションコレクションにエンティティを追加するだけです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-341">It instead just adds an entity to a navigation collection in the same way as would be done if this were a one-to-many relationship.</span></span> <span data-ttu-id="a0c23-342">結果のデバッグビューは、基本的には以前と同じです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-342">The resulting debug view is essentially the same as before:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  PostTags: [{PostId: 3, TagId: 1}]
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Added
  PostId: 3 PK FK
  TagId: 1 PK FK
  Post: {Id: 3}
  Tag: {Id: 1}
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  PostTags: [{PostId: 3, TagId: 1}]
  Posts: [{Id: 3}]
```

<span data-ttu-id="a0c23-343">結合エンティティのインスタンスが `PostTag` 自動的に作成され、FK 値が、現在関連付けられているタグと post の PK 値に設定されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-343">Notice that an instance of the `PostTag` join entity was created automatically with FK values set to the PK values of the tag and post that are now associated.</span></span> <span data-ttu-id="a0c23-344">これらの FK 値に一致するように、通常の参照とコレクションのナビゲーションがすべて修正されました。</span><span class="sxs-lookup"><span data-stu-id="a0c23-344">All the normal reference and collection navigations have been fixed up to match these FK values.</span></span> <span data-ttu-id="a0c23-345">また、このモデルにはスキップナビゲーションが含まれているため、これらも修正されました。</span><span class="sxs-lookup"><span data-stu-id="a0c23-345">Also, since this model contains skip navigations, these have also been fixed up.</span></span> <span data-ttu-id="a0c23-346">具体的には、スキップナビゲーションにタグを追加した場合でも、 `Post.Tags` `Tag.Posts` このリレーションシップのもう一方の側の逆スキップナビゲーションも、関連付けられている投稿を含むように修正されています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-346">Specifically, even though we added the tag to the `Post.Tags` skip navigation, the `Tag.Posts` inverse skip navigation on the other side of this relationship has also been fixed up to contain the associated post.</span></span>

<span data-ttu-id="a0c23-347">これは、スキップナビゲーションが上位に重なっている場合でも、基になる多対多リレーションシップを直接操作できることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-347">It is worth noting that the underlying many-to-many relationships can still be manipulated directly even when skip navigations have been layered on top.</span></span> <span data-ttu-id="a0c23-348">たとえば、スキップナビゲーションを導入する前と同じようにタグと投稿を関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-348">For example, the tag and Post could be associated as we did before introducing skip navigations:</span></span>

<!--
            context.Add(new PostTag { Post = post, Tag = tag });
-->
[!code-csharp[Many_to_many_relationships_4](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_4)]

<span data-ttu-id="a0c23-349">または、FK 値を使用します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-349">Or using FK values:</span></span>

<!--
            context.Add(new PostTag { PostId = post.Id, TagId = tag.Id });
-->
[!code-csharp[Many_to_many_relationships_5](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityAndSkipsSamples.cs?name=Many_to_many_relationships_5)]

<span data-ttu-id="a0c23-350">この場合も、スキップナビゲーションが正しく修正されるため、前の例と同じデバッグビュー出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-350">This will still result in the skip navigations being fixed up correctly, resulting in the same debug view output as in the previous example.</span></span>

### <a name="skip-navigations-only"></a><span data-ttu-id="a0c23-351">ナビゲーションのみスキップ</span><span class="sxs-lookup"><span data-stu-id="a0c23-351">Skip navigations only</span></span>

<span data-ttu-id="a0c23-352">前のセクションでは、2つの基になる一対多リレーションシップ _を完全に定義するだけで_ なく、スキップナビゲーションを追加しました。</span><span class="sxs-lookup"><span data-stu-id="a0c23-352">In the previous section we added skip navigations _in addition to_ fully defining the two underlying one-to-many relationships.</span></span> <span data-ttu-id="a0c23-353">これは、FK 値に対して何が起こるかを示すのに便利ですが、多くの場合は不要です。</span><span class="sxs-lookup"><span data-stu-id="a0c23-353">This is useful to illustrate what happens to FK values, but is often unnecessary.</span></span> <span data-ttu-id="a0c23-354">代わりに、 _スキップナビゲーションのみ_ を使用して多対多リレーションシップを定義できます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-354">Instead, the many-to-many relationship can be defined using _only skip navigations_.</span></span> <span data-ttu-id="a0c23-355">このドキュメントの最上位にあるモデルでは、多対多リレーションシップが定義されています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-355">This is how the many-to-many relationship is defined in the model at the very top of this document.</span></span> <span data-ttu-id="a0c23-356">このモデルを使用すると、スキップナビゲーションに投稿を追加することによって、投稿とタグを再び関連付けることができ `Tag.Posts` ます (または、スキップナビゲーションにタグを追加することもでき `Post.Tags` ます)。</span><span class="sxs-lookup"><span data-stu-id="a0c23-356">Using this model, we can again associate a Post and a Tag by adding a post to the `Tag.Posts` skip navigation (or, alternately, adding a tag to the `Post.Tags` skip navigation):</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_6](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/OptionalRelationshipsSamples.cs?name=Many_to_many_relationships_6)]

<span data-ttu-id="a0c23-357">この変更を行った後にデバッグビューを確認すると、結合エンティティを表すのインスタンスが EF Core によって作成されていることがわかり `Dictionary<string, object>` ます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-357">Looking at the debug view after making this change reveals that EF Core has created an instance of `Dictionary<string, object>` to represent the join entity.</span></span> <span data-ttu-id="a0c23-358">この結合エンティティに `PostsId` は、関連付けられている `TagsId` post と TAG の PK 値に一致するように設定されているとの両方のキープロパティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-358">This join entity contains both `PostsId` and `TagsId` foreign key properties which have been set to match the PK values of the post and tag that are associated.</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 1} Added
  PostsId: 3 PK FK
  TagsId: 1 PK FK
```

<span data-ttu-id="a0c23-359">暗黙の結合エンティティとエンティティ型の使用の詳細については、「 [リレーションシップ](xref:core/modeling/relationships) 」を参照してください `Dictionary<string, object>` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-359">See [Relationships](xref:core/modeling/relationships) for more information about implicit join entities and the use of `Dictionary<string, object>` entity types.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a0c23-360">規則に従ってエンティティ型を結合するために使用される CLR 型は、パフォーマンスを向上させるために将来のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-360">The CLR type used for join entity types by convention may change in future releases to improve performance.</span></span> <span data-ttu-id="a0c23-361">`Dictionary<string, object>`このが明示的に構成されていない限り、結合の種類に依存することはできません。</span><span class="sxs-lookup"><span data-stu-id="a0c23-361">Do not depend on the join type being `Dictionary<string, object>` unless this has been explicitly configured.</span></span>

### <a name="join-entities-with-payloads"></a><span data-ttu-id="a0c23-362">エンティティとペイロードの結合</span><span class="sxs-lookup"><span data-stu-id="a0c23-362">Join entities with payloads</span></span>

<span data-ttu-id="a0c23-363">ここまでの例では、多対多リレーションシップに必要な2つの外部キープロパティのみを含む結合エンティティ型 (明示的または暗黙的) が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="a0c23-363">So far all the examples have used a join entity type (whether explicit or implicit) that contains only the two foreign key properties needed for the many-to-many relationship.</span></span> <span data-ttu-id="a0c23-364">これらの FK 値は、関連エンティティの主キープロパティから取得されるため、リレーションシップを操作するときに、アプリケーションによって明示的に設定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a0c23-364">Neither of these FK values need to be explicitly set by the application when manipulating relationships because their values come from the primary key properties of the related entities.</span></span> <span data-ttu-id="a0c23-365">これにより、データが欠落していなくても、結合エンティティのインスタンスを作成 EF Core ことができます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-365">This allows EF Core to create instances of the join entity without missing data.</span></span>

#### <a name="payloads-with-generated-values"></a><span data-ttu-id="a0c23-366">生成された値を含むペイロード</span><span class="sxs-lookup"><span data-stu-id="a0c23-366">Payloads with generated values</span></span>

<span data-ttu-id="a0c23-367">EF Core では、結合エンティティ型へのプロパティの追加がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a0c23-367">EF Core supports adding additional properties to the join entity type.</span></span> <span data-ttu-id="a0c23-368">これは、結合エンティティに "ペイロード" を与えることと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-368">This is known as giving the join entity a "payload".</span></span> <span data-ttu-id="a0c23-369">たとえば、 `TaggedOn` join エンティティにプロパティを追加してみましょう `PostTag` 。</span><span class="sxs-lookup"><span data-stu-id="a0c23-369">For example, let's add `TaggedOn` property to the `PostTag` join entity:</span></span>

<!--
    public class PostTag
    {
        public int PostId { get; set; } // Foreign key to Post
        public int TagId { get; set; } // Foreign key to Tag

        public DateTime TaggedOn { get; set; } // Payload
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Model)]

<span data-ttu-id="a0c23-370">このペイロードプロパティは、EF Core が結合エンティティインスタンスを作成するときには設定されません。</span><span class="sxs-lookup"><span data-stu-id="a0c23-370">This payload property will not be set when EF Core creates a join entity instance.</span></span> <span data-ttu-id="a0c23-371">これに対処する最も一般的な方法は、ペイロードプロパティを自動的に生成された値と共に使用することです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-371">The most common way to deal with this is to use payload properties with automatically generated values.</span></span> <span data-ttu-id="a0c23-372">たとえば、 `TaggedOn` 新しいエンティティが挿入されるたびに、ストアによって生成されたタイムスタンプを使用するようにプロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-372">For example, the `TaggedOn` property can be configured to use a store-generated timestamp when each new entity is inserted:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<PostTag>(
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany(),
                    j => j.Property(e => e.TaggedOn).HasDefaultValueSql("CURRENT_TIMESTAMP"));
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=OnModelCreating)]

<span data-ttu-id="a0c23-373">投稿には、前と同じ方法でタグを付けることができるようになりました。</span><span class="sxs-lookup"><span data-stu-id="a0c23-373">A post can now be tagged in the same way as before:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_7](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithPayloadSamples.cs?name=Many_to_many_relationships_7)]

<span data-ttu-id="a0c23-374">SaveChanges を呼び出した後に [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を見ると、ペイロードプロパティが適切に設定されていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="a0c23-374">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) after calling SaveChanges shows that the payload property has been set appropriately:</span></span>

```output
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: <null>
  Tags: [{Id: 1}]
PostTag {PostId: 3, TagId: 1} Unchanged
  PostId: 3 PK FK
  TagId: 1 PK FK
  TaggedOn: '12/29/2020 8:13:21 PM'
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 3}]
```

#### <a name="explicitly-setting-payload-values"></a><span data-ttu-id="a0c23-375">ペイロード値の明示的な設定</span><span class="sxs-lookup"><span data-stu-id="a0c23-375">Explicitly setting payload values</span></span>

<span data-ttu-id="a0c23-376">前の例の後に、自動的に生成された値を使用しないペイロードプロパティを追加してみましょう。</span><span class="sxs-lookup"><span data-stu-id="a0c23-376">Following on from the previous example, let's add a payload property that does not use an automatically generated value:</span></span>

<!--
    public class PostTag
    {
        public int PostId { get; set; } // First part of composite PK; FK to Post
        public int TagId { get; set; } // Second part of composite PK; FK to Tag

        public DateTime TaggedOn { get; set; } // Auto-generated payload property
        public string TaggedBy { get; set; } // Not-generated payload property
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Model)]

<span data-ttu-id="a0c23-377">前と同じように投稿をタグ付けすることができるようになりました。結合エンティティは自動的に作成されます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-377">A post can now be tagged in the same way as before, and the join entity will still be created automatically.</span></span> <span data-ttu-id="a0c23-378">このエンティティには、「追跡対象の [エンティティへのアクセス](xref:core/change-tracking/entity-entries)」で説明されているいずれかのメカニズムを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-378">This entity can then be accessed using one of the mechanisms described in [Accessing Tracked Entities](xref:core/change-tracking/entity-entries).</span></span> <span data-ttu-id="a0c23-379">たとえば、次のコードでは、を使用して <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> 結合エンティティインスタンスにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="a0c23-379">For example, the code below uses <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> to access the join entity instance:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            post.Tags.Add(tag);

            context.ChangeTracker.DetectChanges();

            var joinEntity = context.Set<PostTag>().Find(post.Id, tag.Id);

            joinEntity.TaggedBy = "ajcvickers";

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_8](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_8)]

<span data-ttu-id="a0c23-380">結合エンティティが特定されたら、通常の方法で操作できます。この例では、 `TaggedBy` SaveChanges を呼び出す前にペイロードプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-380">Once the join entity has been located it can be manipulated in the normal way--in this example, to set the `TaggedBy` payload property before calling SaveChanges.</span></span>

> [!NOTE]
> <span data-ttu-id="a0c23-381">ここでは、を呼び出すことによって、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> ナビゲーションプロパティの変更を検出し、を使用する前に結合エンティティインスタンスを作成する EF Core を提供する必要があることに注意して `Find` ください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-381">Note that a call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is required here to give EF Core a chance to detect the navigation property change and create the join entity instance before `Find` is used.</span></span> <span data-ttu-id="a0c23-382">詳細については [、「変更の検出と通知](xref:core/change-tracking/change-detection) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a0c23-382">See [Change Detection and Notifications](xref:core/change-tracking/change-detection) for more information.</span></span>

<span data-ttu-id="a0c23-383">また、結合エンティティを明示的に作成して、投稿をタグに関連付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="a0c23-383">Alternately, the join entity can be created explicitly to associate a post with a tag.</span></span> <span data-ttu-id="a0c23-384">例:</span><span class="sxs-lookup"><span data-stu-id="a0c23-384">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            context.Add(new PostTag()
            {
                PostId = post.Id,
                TagId = tag.Id,
                TaggedBy = "ajcvickers"
            });

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Many_to_many_relationships_9](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=Many_to_many_relationships_9)]

<span data-ttu-id="a0c23-385">最後に、ペイロードデータを設定するもう1つの方法は、 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> データベースを更新する前に、イベントをオーバーライドするか、イベントを使用してエンティティを <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> 処理することです。</span><span class="sxs-lookup"><span data-stu-id="a0c23-385">Finally, another way to set payload data is by either overriding <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or using the <xref:Microsoft.EntityFrameworkCore.DbContext.SavingChanges?displayProperty=nameWithType> event to process entities before updating the database.</span></span> <span data-ttu-id="a0c23-386">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a0c23-386">For example:</span></span>

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>())
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                }
            }

            return base.SaveChanges();
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangingFKsAndNavigations/ExplicitJoinEntityWithStringPayloadSamples.cs?name=SaveChanges)]
