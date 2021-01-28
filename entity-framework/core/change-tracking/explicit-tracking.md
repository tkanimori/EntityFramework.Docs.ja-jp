---
title: エンティティの明示的な追跡-EF Core
description: 追加、アタッチ、更新、および削除を使用して、DbContext でエンティティを明示的に追跡する
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/explicit-tracking
ms.openlocfilehash: 1428096b362c8016f7924c72ec9ac3e2f9203ed6
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983275"
---
# <a name="explicitly-tracking-entities"></a><span data-ttu-id="5a602-103">エンティティの明示的な追跡</span><span class="sxs-lookup"><span data-stu-id="5a602-103">Explicitly Tracking Entities</span></span>

<span data-ttu-id="5a602-104">各 <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスによって、エンティティに加えられる変更が追跡されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="5a602-105">さらに、これらの追跡対象エンティティによって、<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> が呼び出されたときにデータベースへの変更が実行されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="5a602-106">Entity Framework Core (EF Core) の変更の追跡は、同じ <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスを使用してエンティティのクエリを実行し、を呼び出すことによって更新する場合に最適 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> です。</span><span class="sxs-lookup"><span data-stu-id="5a602-106">Entity Framework Core (EF Core) change tracking works best when the same <xref:Microsoft.EntityFrameworkCore.DbContext> instance is used to both query for entities and update them by calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="5a602-107">これは、EF Core によって、クエリされたエンティティの状態が自動的に追跡され、SaveChanges が呼び出されたときにこれらのエンティティに加えられた変更が検出されるためです。</span><span class="sxs-lookup"><span data-stu-id="5a602-107">This is because EF Core automatically tracks the state of queried entities and then detects any changes made to these entities when SaveChanges is called.</span></span> <span data-ttu-id="5a602-108">この方法については、 [EF Core の Change Tracking](xref:core/change-tracking/index)に記載されています。</span><span class="sxs-lookup"><span data-stu-id="5a602-108">This approach is covered in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span>

> [!TIP]
> <span data-ttu-id="5a602-109">このドキュメントでは、EF Core の変更の追跡のエンティティの状態と基本について理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="5a602-109">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="5a602-110">これらのトピックの詳細については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a602-110">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="5a602-111">このドキュメントに含まれているすべてのコードは、[GitHub からサンプル コードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore)することで実行およびデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="5a602-111">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span></span>

> [!TIP]
> <span data-ttu-id="5a602-112">わかりやすくするために、このドキュメントでは <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> などの同期メソッドを使用および参照しています。その非同期バージョンである <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> などは使用していません。</span><span class="sxs-lookup"><span data-stu-id="5a602-112">For simplicity, this document uses and references synchronous methods such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> rather their async equivalents such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span></span> <span data-ttu-id="5a602-113">特に明記されていない限り、非同期メソッドの呼び出しと待機は置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="5a602-113">Calling and awaiting the async method can be substituted unless otherwise noted.</span></span>

## <a name="introduction"></a><span data-ttu-id="5a602-114">はじめに</span><span class="sxs-lookup"><span data-stu-id="5a602-114">Introduction</span></span>

<span data-ttu-id="5a602-115">エンティティは、 <xref:Microsoft.EntityFrameworkCore.DbContext> コンテキストがそれらのエンティティを追跡するように明示的に "アタッチ" できます。</span><span class="sxs-lookup"><span data-stu-id="5a602-115">Entities can be explicitly "attached" to a <xref:Microsoft.EntityFrameworkCore.DbContext> such that the context then tracks those entities.</span></span> <span data-ttu-id="5a602-116">これは主に次の場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="5a602-116">This is primarily useful when:</span></span>

1. <span data-ttu-id="5a602-117">データベースに挿入される新しいエンティティを作成しています。</span><span class="sxs-lookup"><span data-stu-id="5a602-117">Creating new entities that will be inserted into the database.</span></span>
2. <span data-ttu-id="5a602-118">以前に _別_ の dbcontext インスタンスによってクエリされた切断されたエンティティを再アタッチしています。</span><span class="sxs-lookup"><span data-stu-id="5a602-118">Re-attaching disconnected entities that were previously queried by a _different_ DbContext instance.</span></span>

<span data-ttu-id="5a602-119">これらのうちの1つは、ほとんどのアプリケーションで必要となるもので、メソッドによって処理されるプライマリです <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="5a602-119">The first of these will be needed by most applications, and is primary handled by the <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> methods.</span></span>

<span data-ttu-id="5a602-120">2つ目は、エンティティ **_が追跡されていないときに_** エンティティまたはリレーションシップを変更するアプリケーションによってのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="5a602-120">The second is only needed by applications that change entities or their relationships **_while the entities are not being tracked_**.</span></span> <span data-ttu-id="5a602-121">たとえば、web アプリケーションは、ユーザーが変更を行ってエンティティを返送する web クライアントにエンティティを送信する場合があります。</span><span class="sxs-lookup"><span data-stu-id="5a602-121">For example, a web application may send entities to the web client where the user makes changes and sends the entities back.</span></span> <span data-ttu-id="5a602-122">これらのエンティティは、最初に DbContext からクエリを行った後に "切断" と呼ばれますが、クライアントに送信されると、そのコンテキストから切断されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-122">These entities are referred to as "disconnected" since they were originally queried from a DbContext, but were then disconnected from that context when sent to the client.</span></span>

<span data-ttu-id="5a602-123">Web アプリケーションは、これらのエンティティを再アタッチして再度追跡し、データベースに対する適切な更新を可能にするために加えられた変更を示す必要があり <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-123">The web application must now re-attach these entities so that they are again tracked and indicate the changes that have been made such that <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> can make appropriate updates to the database.</span></span> <span data-ttu-id="5a602-124">これは主に、メソッドとメソッドによって処理され <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-124">This is primarily handled by the <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> methods.</span></span>

> [!TIP]
> <span data-ttu-id="5a602-125">通常は、クエリの対象となったのと _同じ DbContext インスタンス_ にエンティティをアタッチする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="5a602-125">Attaching entities to the _same DbContext instance_ that they were queried from should not normally be needed.</span></span> <span data-ttu-id="5a602-126">定期的に追跡しないクエリを実行し、返されたエンティティを同じコンテキストにアタッチすることは避けてください。</span><span class="sxs-lookup"><span data-stu-id="5a602-126">Do not routinely perform a no-tracking query and then attach the returned entities to the same context.</span></span> <span data-ttu-id="5a602-127">これは、追跡クエリを使用した場合よりも遅くなります。また、シャドウプロパティの値がないなどの問題が発生し、適切に機能しなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5a602-127">This will be slower than using a tracking query, and may also result in issues such as missing shadow property values, making it harder to get right.</span></span>

### <a name="generated-verses-explicit-key-values"></a><span data-ttu-id="5a602-128">生成されたキー値</span><span class="sxs-lookup"><span data-stu-id="5a602-128">Generated verses explicit key values</span></span>

<span data-ttu-id="5a602-129">既定では、整数と GUID の [キープロパティ](xref:core/modeling/keys) は、 [自動的に生成されたキー値](xref:core/modeling/generated-properties)を使用するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="5a602-129">By default, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="5a602-130">**変更の追跡には大きな利点があります。未設定のキー値は、エンティティが "新規" であることを示し** ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-130">This has a **major advantage for change tracking: an unset key value indicates that the entity is "new"**.</span></span> <span data-ttu-id="5a602-131">"New" によって、データベースにまだ挿入されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="5a602-131">By "new", we mean that it has not yet been inserted into the database.</span></span>

<span data-ttu-id="5a602-132">次のセクションでは、2つのモデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="5a602-132">Two models are used in the following sections.</span></span> <span data-ttu-id="5a602-133">最初のは、生成されたキー値を使用し **ない** ように構成されています。</span><span class="sxs-lookup"><span data-stu-id="5a602-133">The first is configured to **not** use generated key values:</span></span>

<!--
    public class Blog
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Model)]

<span data-ttu-id="5a602-134">生成されていない (明示的に設定された) キー値は、すべて明示的で簡単に実行できるため、各例で最初に示します。</span><span class="sxs-lookup"><span data-stu-id="5a602-134">Non-generated (i.e. explicitly set) key values are shown first in each example because everything is very explicit and easy to follow.</span></span> <span data-ttu-id="5a602-135">次に、生成されたキー値を使用する例を示します。</span><span class="sxs-lookup"><span data-stu-id="5a602-135">This is then followed by an example where generated key values are used:</span></span>

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

<span data-ttu-id="5a602-136">ここでは、生成されたキー値を使用することが [単純な整数キーの既定](xref:core/modeling/generated-properties)値であるため、このモデルのキープロパティには追加の構成は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5a602-136">Notice that the key properties in this model need no additional configuration here since using generated key values is the [default for simple integer keys](xref:core/modeling/generated-properties).</span></span>

## <a name="inserting-new-entities"></a><span data-ttu-id="5a602-137">新しいエンティティの挿入</span><span class="sxs-lookup"><span data-stu-id="5a602-137">Inserting new entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="5a602-138">明示的なキー値</span><span class="sxs-lookup"><span data-stu-id="5a602-138">Explicit key values</span></span>

<span data-ttu-id="5a602-139">エンティティは、 `Added` によって挿入される状態で追跡される必要があり <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-139">An entity must be tracked in the `Added` state to be inserted by <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="5a602-140">エンティティは、通常、、、、、またはの同等のメソッドのいずれかを呼び出すことによって、追加された状態に <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbSet%601> なります。</span><span class="sxs-lookup"><span data-stu-id="5a602-140">Entities are typically put in the Added state by calling one of <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span>

> [!TIP]
> <span data-ttu-id="5a602-141">これらのメソッドはすべて、変更追跡のコンテキストで同じ方法で動作します。</span><span class="sxs-lookup"><span data-stu-id="5a602-141">These methods all work in the same way in the context of change tracking.</span></span> <span data-ttu-id="5a602-142">詳細については、「 [その他の Change Tracking 機能](xref:core/change-tracking/miscellaneous) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a602-142">See [Additional Change Tracking Features](xref:core/change-tracking/miscellaneous) for more information.</span></span>

<span data-ttu-id="5a602-143">たとえば、新しいブログの追跡を開始するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="5a602-143">For example, to start tracking a new blog:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Inserting_new_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_1)]

<span data-ttu-id="5a602-144">この呼び出しの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を調べると、コンテキストが状態の新しいエンティティを追跡していることが示され `Added` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-144">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking the new entity in the `Added` state:</span></span>

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

<span data-ttu-id="5a602-145">ただし、Add メソッドは、個々のエンティティに対してのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="5a602-145">However, the Add methods don't just work on an individual entity.</span></span> <span data-ttu-id="5a602-146">実際には、 _関連エンティティのグラフ全体の_ 追跡が開始され、すべての `Added` 状態になります。</span><span class="sxs-lookup"><span data-stu-id="5a602-146">They actually start tracking an _entire graph of related entities_, putting them all to the `Added` state.</span></span> <span data-ttu-id="5a602-147">たとえば、新しいブログと関連する新しい投稿を挿入するには、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="5a602-147">For example, to insert a new blog and associated new posts:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_2)]

<span data-ttu-id="5a602-148">コンテキストは、これらのすべてのエンティティを次のように追跡するようになりました `Added` 。</span><span class="sxs-lookup"><span data-stu-id="5a602-148">The context is now tracking all these entities as `Added`:</span></span>

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Added
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Added
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="5a602-149">上記の例では、キープロパティに明示的な値が設定されていることに注意 `Id` してください。</span><span class="sxs-lookup"><span data-stu-id="5a602-149">Notice that explicit values have been set for the `Id` key properties in the examples above.</span></span> <span data-ttu-id="5a602-150">これは、キー値が自動的に生成されるのではなく、明示的に設定されたキー値を使用するようにモデルが構成されているためです。</span><span class="sxs-lookup"><span data-stu-id="5a602-150">This is because the model here has been configured to use explicitly set key values, rather than automatically generated key values.</span></span> <span data-ttu-id="5a602-151">生成されたキーを使用しない場合は、を呼び出す _前に_ 、キープロパティを明示的に設定する必要があり `Add` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-151">When not using generated keys, the key properties must be explicitly set _before_ calling `Add`.</span></span> <span data-ttu-id="5a602-152">これらのキー値は、SaveChanges が呼び出されたときに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-152">These key values are then inserted when SaveChanges is called.</span></span> <span data-ttu-id="5a602-153">たとえば、SQLite を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a602-153">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Id", "Name")
VALUES (@p0, @p1);

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String), @p3='1' (DbType = String), @p4='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p5='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p2, @p3, @p4, @p5);

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String), @p1='1' (DbType = String), @p2='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p3='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2, @p3);
```

<span data-ttu-id="5a602-154">これらのエンティティは、 `Unchanged` データベース内に存在するようになったため、SaveChanges の完了後の状態で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-154">All of these entities are tracked in the `Unchanged` state after SaveChanges completes, since these entities now exist in the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

### <a name="generated-key-values"></a><span data-ttu-id="5a602-155">生成されたキー値</span><span class="sxs-lookup"><span data-stu-id="5a602-155">Generated key values</span></span>

<span data-ttu-id="5a602-156">前述のように、既定では、[自動的に生成されたキー値](xref:core/modeling/generated-properties)を使用するように整数と GUID の[キープロパティ](xref:core/modeling/keys)が構成されています。</span><span class="sxs-lookup"><span data-stu-id="5a602-156">As mentioned above, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties) by default.</span></span> <span data-ttu-id="5a602-157">これは、アプリケーションが _キー値を明示的に設定してはいけない_ ことを意味します。</span><span class="sxs-lookup"><span data-stu-id="5a602-157">This means that the application _must not set any key value explicitly_.</span></span> <span data-ttu-id="5a602-158">たとえば、新しいブログを挿入し、生成されたキー値ですべてを投稿するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="5a602-158">For example, to insert a new blog and posts all with generated key values:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Inserting_new_entities_3)]

<span data-ttu-id="5a602-159">明示的なキー値と同様に、コンテキストはすべてのエンティティを次のように追跡するようになりました `Added` 。</span><span class="sxs-lookup"><span data-stu-id="5a602-159">As with explicit key values, the context is now tracking all these entities as `Added`:</span></span>

```output
Blog {Id: -2147482644} Added
  Id: -2147482644 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -2147482637}, {Id: -2147482636}]
Post {Id: -2147482637} Added
  Id: -2147482637 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -2147482644}
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: -2147482644}
```

<span data-ttu-id="5a602-160">この場合、エンティティごとに [一時的なキー値](xref:core/change-tracking/miscellaneous#temporary-values) が生成されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="5a602-160">Notice in this case that [temporary key values](xref:core/change-tracking/miscellaneous#temporary-values) have been generated for each entity.</span></span> <span data-ttu-id="5a602-161">これらの値は SaveChanges が呼び出されるまで EF Core によって使用されます。この時点で、実際のキー値がデータベースから読み取られます。</span><span class="sxs-lookup"><span data-stu-id="5a602-161">These values are used by EF Core until SaveChanges is called, at which point real key values are read back from the database.</span></span> <span data-ttu-id="5a602-162">たとえば、SQLite を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a602-162">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Name")
VALUES (@p0);
SELECT "Id"
FROM "Blogs"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p2='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p3='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p1, @p2, @p3);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="5a602-163">SaveChanges が完了すると、すべてのエンティティが実際のキー値で更新され、データベースの状態と一致した状態で追跡され `Unchanged` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-163">After SaveChanges completes, all of the entities have been updated with their real key values and are tracked in the `Unchanged` state since they now match the state in the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="5a602-164">これは、明示的なキー値を使用した前の例とまったく同じ終了状態です。</span><span class="sxs-lookup"><span data-stu-id="5a602-164">This is exactly the same end-state as the previous example that used explicit key values.</span></span>

> [!TIP]
> <span data-ttu-id="5a602-165">生成されたキー値を使用している場合でも、明示的なキー値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="5a602-165">An explicit key value can still be set even when using generated key values.</span></span> <span data-ttu-id="5a602-166">EF Core は、このキー値を使用してを挿入しようとします。</span><span class="sxs-lookup"><span data-stu-id="5a602-166">EF Core will then attempt to insert using this key value.</span></span> <span data-ttu-id="5a602-167">Id 列を含む SQL Server を含む一部のデータベース構成では、このような挿入がサポートされていないため、スローされます ([回避策については、これらのドキュメントを参照してください](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns))。</span><span class="sxs-lookup"><span data-stu-id="5a602-167">Some database configurations, including SQL Server with Identity columns, do not support such inserts and will throw ([see these docs for a workaround](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)).</span></span>

## <a name="attaching-existing-entities"></a><span data-ttu-id="5a602-168">既存のエンティティのアタッチ</span><span class="sxs-lookup"><span data-stu-id="5a602-168">Attaching existing entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="5a602-169">明示的なキー値</span><span class="sxs-lookup"><span data-stu-id="5a602-169">Explicit key values</span></span>

<span data-ttu-id="5a602-170">クエリから返されるエンティティは、状態で追跡され `Unchanged` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-170">Entities returned from queries are tracked in the `Unchanged` state.</span></span> <span data-ttu-id="5a602-171">`Unchanged`この状態は、エンティティがクエリされてから変更されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="5a602-171">The `Unchanged` state means that the entity has not been modified since it was queried.</span></span> <span data-ttu-id="5a602-172">接続されていないエンティティは、HTTP 要求内の web クライアントから返されることがありますが、、 <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType> 、またはの同等のメソッドを使用して、この状態にすることができ <xref:Microsoft.EntityFrameworkCore.DbSet%601> ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-172">A disconnected entity, perhaps returned from a web client in an HTTP request, can be put into this state using either <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span> <span data-ttu-id="5a602-173">たとえば、既存のブログの追跡を開始するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="5a602-173">For example, to start tracking an existing blog:</span></span>

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Attaching_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_1)]

> [!NOTE]
> <span data-ttu-id="5a602-174">次の例では、簡略化のためにを使用してエンティティを明示的に作成してい `new` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-174">The examples here are creating entities explicitly with `new` for simplicity.</span></span> <span data-ttu-id="5a602-175">通常、エンティティインスタンスは別のソースから取得されます。たとえば、クライアントから逆シリアル化されたり、HTTP Post のデータから作成されたりすることがあります。</span><span class="sxs-lookup"><span data-stu-id="5a602-175">Normally the entity instances will have come from another source, such as being deserialized from a client, or being created from data in an HTTP Post.</span></span>

<span data-ttu-id="5a602-176">この呼び出しの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を調べると、エンティティが状態で追跡されていることがわかり `Unchanged` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-176">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the entity is tracked in the `Unchanged` state:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

<span data-ttu-id="5a602-177">と同様に `Add` 、は、 `Attach` 接続されているエンティティのグラフ全体を状態に設定し `Unchanged` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-177">Just like `Add`, `Attach` actually sets an entire graph of connected entities to the `Unchanged` state.</span></span> <span data-ttu-id="5a602-178">たとえば、既存のブログと関連する既存の投稿を添付するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="5a602-178">For example, to attach an existing blog and associated existing posts:</span></span>

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_2)]

<span data-ttu-id="5a602-179">コンテキストは、これらのすべてのエンティティを次のように追跡するようになりました `Unchanged` 。</span><span class="sxs-lookup"><span data-stu-id="5a602-179">The context is now tracking all these entities as `Unchanged`:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="5a602-180">この時点で SaveChanges を呼び出すと、効果はありません。</span><span class="sxs-lookup"><span data-stu-id="5a602-180">Calling SaveChanges at this point will have no effect.</span></span> <span data-ttu-id="5a602-181">すべてのエンティティはとしてマークさ `Unchanged` れるため、データベース内で更新するものはありません。</span><span class="sxs-lookup"><span data-stu-id="5a602-181">All the entities are marked as `Unchanged`, so there is nothing to update in the database.</span></span>

### <a name="generated-key-values"></a><span data-ttu-id="5a602-182">生成されたキー値</span><span class="sxs-lookup"><span data-stu-id="5a602-182">Generated key values</span></span>

<span data-ttu-id="5a602-183">前述のように、既定では、[自動的に生成されたキー値](xref:core/modeling/generated-properties)を使用するように整数と GUID の[キープロパティ](xref:core/modeling/keys)が構成されています。</span><span class="sxs-lookup"><span data-stu-id="5a602-183">As mentioned above, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties) by default.</span></span> <span data-ttu-id="5a602-184">これは、接続されていないエンティティを使用する場合の大きな利点です。未設定のキー値は、エンティティがまだデータベースに挿入されていないことを示します。</span><span class="sxs-lookup"><span data-stu-id="5a602-184">This has a major advantage when working with disconnected entities: an unset key value indicates that the entity has not yet been inserted into the database.</span></span> <span data-ttu-id="5a602-185">これにより、変更トラッカーは新しいエンティティを自動的に検出し、状態にすることができ `Added` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-185">This allows the change tracker to automatically detect new entities and put them in the `Added` state.</span></span> <span data-ttu-id="5a602-186">たとえば、次のブログと投稿のグラフを添付することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="5a602-186">For example, consider attaching this graph of a blog and posts:</span></span>

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Attaching_existing_entities_3)]

<span data-ttu-id="5a602-187">ブログのキー値は1であり、データベースに既に存在していることを示しています。</span><span class="sxs-lookup"><span data-stu-id="5a602-187">The blog has a key value of 1, indicating that it already exists in the database.</span></span> <span data-ttu-id="5a602-188">2つのポストにはキー値も設定されていますが、3番目の投稿はありません。</span><span class="sxs-lookup"><span data-stu-id="5a602-188">Two of the posts also have key values set, but the third does not.</span></span> <span data-ttu-id="5a602-189">EF Core には、このキー値が0として表示されます。これは、整数の CLR 既定値です。</span><span class="sxs-lookup"><span data-stu-id="5a602-189">EF Core will see this key value as 0, the CLR default for an integer.</span></span> <span data-ttu-id="5a602-190">この結果、次のように新しいエンティティがとしてマーク EF Core され `Added` `Unchanged` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-190">This results in EF Core marking the new entity as `Added` instead of `Unchanged`:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482636}]
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
```

<span data-ttu-id="5a602-191">この時点で SaveChanges を呼び出すと、エンティティに対して何も実行されません `Unchanged` が、新しいエンティティがデータベースに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-191">Calling SaveChanges at this point does nothing with the `Unchanged` entities, but inserts the new entity into the database.</span></span> <span data-ttu-id="5a602-192">たとえば、SQLite を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a602-192">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="5a602-193">ここで重要な点は、生成されたキー EF Core 値を使用して、切断された **グラフ内の既存のエンティティを自動的に区別** することができることです。</span><span class="sxs-lookup"><span data-stu-id="5a602-193">The important point to notice here is that, with generated key values, EF Core is able to **automatically distinguish new from existing entities in a disconnected graph**.</span></span> <span data-ttu-id="5a602-194">簡単に言うと、生成されたキーを使用すると、エンティティにキー値が設定されていない場合、EF Core は常にエンティティを挿入します。</span><span class="sxs-lookup"><span data-stu-id="5a602-194">In a nutshell, when using generated keys, EF Core will always insert an entity when that entity has no key value set.</span></span>

## <a name="updating-existing-entities"></a><span data-ttu-id="5a602-195">既存のエンティティの更新</span><span class="sxs-lookup"><span data-stu-id="5a602-195">Updating existing entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="5a602-196">明示的なキー値</span><span class="sxs-lookup"><span data-stu-id="5a602-196">Explicit key values</span></span>

<span data-ttu-id="5a602-197"><xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>、 <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType> 、およびの同等のメソッドは、上で説明した <xref:Microsoft.EntityFrameworkCore.DbSet%601> メソッドとまったく同じように動作しますが、 `Attach` エンティティは状態ではなくに配置される点が異なり `Modfied` `Unchanged` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-197"><xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType>, and the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601> behave exactly as the `Attach` methods described above, except that entities are put into the `Modfied` instead of the `Unchanged` state.</span></span> <span data-ttu-id="5a602-198">たとえば、既存のブログの追跡を開始するには、次のようにし `Modified` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-198">For example, to start tracking an existing blog as `Modified`:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Updating_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_1)]

<span data-ttu-id="5a602-199">この呼び出しの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を調べると、コンテキストが状態でこのエンティティを追跡していることが示され `Modified` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-199">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking this entity in the `Modified` state:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: []
```

<span data-ttu-id="5a602-200">やと同様 `Add` に `Attach` 、 `Update` 関連エンティティの _グラフ全体_ をとして実際にマークし `Modified` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-200">Just like with `Add` and `Attach`, `Update` actually marks an _entire graph_ of related entities as `Modified`.</span></span> <span data-ttu-id="5a602-201">たとえば、既存のブログと関連する既存の投稿を次のように添付し `Modified` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-201">For example, to attach an existing blog and associated existing posts as `Modified`:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_2)]

<span data-ttu-id="5a602-202">コンテキストは、これらのすべてのエンティティを次のように追跡するようになりました `Modified` 。</span><span class="sxs-lookup"><span data-stu-id="5a602-202">The context is now tracking all these entities as `Modified`:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

<span data-ttu-id="5a602-203">この時点で SaveChanges を呼び出すと、これらすべてのエンティティの更新がデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-203">Calling SaveChanges at this point will cause updates to be sent to the database for all these entities.</span></span> <span data-ttu-id="5a602-204">たとえば、SQLite を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a602-204">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();
```

### <a name="generated-key-values"></a><span data-ttu-id="5a602-205">生成されたキー値</span><span class="sxs-lookup"><span data-stu-id="5a602-205">Generated key values</span></span>

<span data-ttu-id="5a602-206">と同様に `Attach` 、生成されたキー値の主な利点は `Update` 次のとおりです。未設定のキー値は、エンティティが新規で、データベースにまだ挿入されていないことを示します。</span><span class="sxs-lookup"><span data-stu-id="5a602-206">As with `Attach`, generated key values have the same major benefit for `Update`: an unset key value indicates that the entity is new and has not yet been inserted into the database.</span></span> <span data-ttu-id="5a602-207">と同様に `Attach` 、これにより、DbContext は新しいエンティティを自動的に検出し、状態にすることができ `Added` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-207">As with `Attach`, this allows the DbContext to automatically detect new entities and put them in the `Added` state.</span></span> <span data-ttu-id="5a602-208">たとえば、 `Update` ブログと投稿の次のグラフを使用してを呼び出すことを検討してください。</span><span class="sxs-lookup"><span data-stu-id="5a602-208">For example, consider calling `Update` with this graph of a blog and posts:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Updating_existing_entities_3)]

<span data-ttu-id="5a602-209">例と同様に `Attach` 、キー値のない post は new として検出され、状態に設定され `Added` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-209">As with the `Attach` example, the post with no key value is detected as new and set to the `Added` state.</span></span> <span data-ttu-id="5a602-210">その他のエンティティは次のようにマークされ `Modified` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-210">The other entities are marked as `Modified`:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482633}]
Post {Id: -2147482633} Added
  Id: -2147482633 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

<span data-ttu-id="5a602-211">この時点でを呼び出すと、 `SaveChanges` 新しいエンティティが挿入されている間に、既存のすべてのエンティティの更新がデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-211">Calling `SaveChanges` at this point will cause updates to be sent to the database for all the existing entities, while the new entity is inserted.</span></span> <span data-ttu-id="5a602-212">たとえば、SQLite を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a602-212">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="5a602-213">これは、切断されたグラフから更新と挿入を生成する非常に簡単な方法です。</span><span class="sxs-lookup"><span data-stu-id="5a602-213">This is a very easy way to generate updates and inserts from a disconnected graph.</span></span> <span data-ttu-id="5a602-214">ただし、一部のプロパティ値が変更されていない場合でも、追跡されるすべてのエンティティのすべてのプロパティについて、更新または挿入がデータベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-214">However, it results in updates or inserts being sent to the database for every property of every tracked entity, even when some property values may not have been changed.</span></span> <span data-ttu-id="5a602-215">怖いすぎないようにしてください。小さいグラフを持つ多くのアプリケーションでは、これは簡単で実用的な方法で更新プログラムを生成できます。</span><span class="sxs-lookup"><span data-stu-id="5a602-215">Don't be too scared by this; for many applications with small graphs, this can be an easy and pragmatic way of generating updates.</span></span> <span data-ttu-id="5a602-216">ただし、他の複雑なパターンでは、「 [EF Core の Id 解決](xref:core/change-tracking/identity-resolution)」で説明されているように、より効率的な更新が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5a602-216">That being said, other more complex patterns can sometimes result in more efficient updates, as described in [Identity Resolution in EF Core](xref:core/change-tracking/identity-resolution).</span></span>

## <a name="deleting-existing-entities"></a><span data-ttu-id="5a602-217">既存のエンティティの削除</span><span class="sxs-lookup"><span data-stu-id="5a602-217">Deleting existing entities</span></span>

<span data-ttu-id="5a602-218">SaveChanges によって削除されるエンティティについては、状態で追跡する必要があり `Deleted` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-218">For an entity to be deleted by SaveChanges it must be tracked in the `Deleted` state.</span></span> <span data-ttu-id="5a602-219">エンティティは、通常、、 `Deleted` 、またはの同等のメソッドのいずれかを呼び出すことによって、状態に <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbSet%601> なります。</span><span class="sxs-lookup"><span data-stu-id="5a602-219">Entities are typically put in the `Deleted` state by calling one of <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span> <span data-ttu-id="5a602-220">たとえば、既存の投稿を次のようにマークし `Deleted` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-220">For example, to mark an existing post as `Deleted`:</span></span>

<!--
            context.Remove(
                new Post
                {
                    Id = 2
                });
-->
[!code-csharp[Deleting_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_existing_entities_1)]

<span data-ttu-id="5a602-221">この呼び出しの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を調べると、コンテキストが状態のエンティティを追跡していることが示され `Deleted` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-221">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking the entity in the `Deleted` state:</span></span>

```output
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: <null> FK
  Content: <null>
  Title: <null>
  Blog: <null>
```

<span data-ttu-id="5a602-222">このエンティティは、SaveChanges が呼び出されたときに削除されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-222">This entity will be deleted when SaveChanges is called.</span></span> <span data-ttu-id="5a602-223">たとえば、SQLite を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a602-223">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

<span data-ttu-id="5a602-224">SaveChanges が完了した後、削除されたエンティティはデータベースに存在しなくなったため、DbContext からデタッチされます。</span><span class="sxs-lookup"><span data-stu-id="5a602-224">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="5a602-225">このため、追跡されているエンティティがないため、デバッグビューは空です。</span><span class="sxs-lookup"><span data-stu-id="5a602-225">The debug view is therefore empty because no entities are being tracked.</span></span>

## <a name="deleting-dependentchild-entities"></a><span data-ttu-id="5a602-226">依存/子エンティティの削除</span><span class="sxs-lookup"><span data-stu-id="5a602-226">Deleting dependent/child entities</span></span>

<span data-ttu-id="5a602-227">グラフからの依存/子エンティティの削除は、プリンシパル/親エンティティを削除するよりも簡単です。</span><span class="sxs-lookup"><span data-stu-id="5a602-227">Deleting dependent/child entities from a graph is more straightforward than deleting principal/parent entities.</span></span> <span data-ttu-id="5a602-228">詳細については、次のセクションと「 [外部キーとナビゲーションの変更](xref:core/change-tracking/relationship-changes) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a602-228">See the next section and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

<span data-ttu-id="5a602-229">で作成されたエンティティに対してを呼び出すことは通常あり `Remove` `new` ません。</span><span class="sxs-lookup"><span data-stu-id="5a602-229">It is unusual to call `Remove` on an entity created with `new`.</span></span> <span data-ttu-id="5a602-230">さらに、やとは異なり、 `Add` `Attach` `Update` `Remove` またはの状態で既に追跡されていないエンティティに対してを呼び出すことは珍しく `Unchanged` ありませ `Modified` ん。</span><span class="sxs-lookup"><span data-stu-id="5a602-230">Further, unlike `Add`, `Attach` and `Update`, it is uncommon to call `Remove` on an entity that isn't already tracked in the `Unchanged` or `Modified` state.</span></span> <span data-ttu-id="5a602-231">代わりに、通常は、1つのエンティティまたは関連エンティティのグラフを追跡し、 `Remove` 削除する必要があるエンティティに対してを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5a602-231">Instead it is typical to track a single entity or graph of related entities, and then call `Remove` on the entities that should be deleted.</span></span> <span data-ttu-id="5a602-232">通常、追跡対象のエンティティのグラフは次のいずれかによって作成されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-232">This graph of tracked entities is typically created by either:</span></span>

1. <span data-ttu-id="5a602-233">エンティティに対するクエリの実行</span><span class="sxs-lookup"><span data-stu-id="5a602-233">Running a query for the entities</span></span>
2. <span data-ttu-id="5a602-234">前の `Attach` `Update` セクションで説明したように、接続されていないエンティティのグラフでメソッドまたはメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="5a602-234">Using the `Attach` or `Update` methods on a graph of disconnected entities, as described in the preceding sections.</span></span>

<span data-ttu-id="5a602-235">たとえば、前のセクションのコードでは、クライアントから投稿を取得し、次のような処理を実行する可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="5a602-235">For example, the code in the previous section is more likely obtain a post from a client and then do something like this:</span></span>

<!--
            context.Attach(post);
            context.Remove(post);
-->
[!code-csharp[Deleting_dependent_child_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_1)]

<span data-ttu-id="5a602-236">これは、前の例とまったく同じように動作します。これは、追跡されていないエンティティに対してを呼び出すと、 `Remove` 最初にアタッチされてからとしてマークが付けられるため `Deleted` です。</span><span class="sxs-lookup"><span data-stu-id="5a602-236">This behaves exactly the same way as the previous example, since calling `Remove` on an un-tracked entity causes it to first be attached and then marked as `Deleted`.</span></span>

<span data-ttu-id="5a602-237">より現実的な例では、エンティティのグラフが最初にアタッチされ、それらのエンティティの一部が削除済みとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="5a602-237">In more realistic examples, a graph of entities is first attached, and then some of those entities are marked as deleted.</span></span> <span data-ttu-id="5a602-238">例:</span><span class="sxs-lookup"><span data-stu-id="5a602-238">For example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_dependent_child_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_2)]

<span data-ttu-id="5a602-239">が呼び出されたエンティティを除き、すべてのエンティティはとしてマークされ `Unchanged` `Remove` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-239">All entities are marked as `Unchanged`, except the one on which `Remove` was called:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="5a602-240">このエンティティは、SaveChanges が呼び出されたときに削除されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-240">This entity will be deleted when SaveChanges is called.</span></span> <span data-ttu-id="5a602-241">たとえば、SQLite を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5a602-241">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

<span data-ttu-id="5a602-242">SaveChanges が完了した後、削除されたエンティティはデータベースに存在しなくなったため、DbContext からデタッチされます。</span><span class="sxs-lookup"><span data-stu-id="5a602-242">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="5a602-243">その他のエンティティの `Unchanged` 状態は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5a602-243">Other entities remain in the `Unchanged` state:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
```

## <a name="deleting-principalparent-entities"></a><span data-ttu-id="5a602-244">プリンシパル/親エンティティの削除</span><span class="sxs-lookup"><span data-stu-id="5a602-244">Deleting principal/parent entities</span></span>

<span data-ttu-id="5a602-245">2つのエンティティ型を接続する各リレーションシップには、プリンシパルまたは親 end、および依存または子 end があります。</span><span class="sxs-lookup"><span data-stu-id="5a602-245">Each relationship that connects two entity types has a principal or parent end, and a dependent or child end.</span></span> <span data-ttu-id="5a602-246">依存/子エンティティは、外部キープロパティを持つエンティティです。</span><span class="sxs-lookup"><span data-stu-id="5a602-246">The dependent/child entity is the one with the foreign key property.</span></span> <span data-ttu-id="5a602-247">一対多のリレーションシップでは、プリンシパル/親は "一" 側にあり、依存/子は "多" 側にあります。</span><span class="sxs-lookup"><span data-stu-id="5a602-247">In a one-to-many relationship, the principal/parent is on the "one" side, and the dependent/child is on the "many" side.</span></span> <span data-ttu-id="5a602-248">詳細については、「 [リレーションシップ](xref:core/modeling/relationships) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a602-248">See [Relationships](xref:core/modeling/relationships) for more information.</span></span>

<span data-ttu-id="5a602-249">上記の例では、投稿を削除しようとしています。これは、ブログ内の依存/子エンティティです。一対多のリレーションシップを投稿しています。</span><span class="sxs-lookup"><span data-stu-id="5a602-249">In the preceding examples we were deleting a post, which is a dependent/child entity in the blog-posts one-to-many relationship.</span></span> <span data-ttu-id="5a602-250">依存/子エンティティを削除しても、他のエンティティには影響がないため、この方法は比較的簡単です。</span><span class="sxs-lookup"><span data-stu-id="5a602-250">This is relatively straightforward since removal of a dependent/child entity does not have any impact on other entities.</span></span> <span data-ttu-id="5a602-251">一方、プリンシパル/親エンティティを削除すると、依存/子エンティティにも影響を与える必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a602-251">On the other hand, deleting a principal/parent entity must also impact any dependent/child entities.</span></span> <span data-ttu-id="5a602-252">そうしないと、外部キーの値は、存在しなくなった主キーの値を参照したままになります。</span><span class="sxs-lookup"><span data-stu-id="5a602-252">Not doing so would leave a foreign key value referencing a primary key value that no longer exists.</span></span> <span data-ttu-id="5a602-253">これは無効なモデル状態であるため、ほとんどのデータベースで参照制約エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="5a602-253">This is an invalid model state and results in a referential constraint error in most databases.</span></span>

<span data-ttu-id="5a602-254">この無効なモデルの状態は、次の2つの方法で処理できます。</span><span class="sxs-lookup"><span data-stu-id="5a602-254">This invalid model state can be handled in two ways:</span></span>

1. <span data-ttu-id="5a602-255">FK 値を null に設定しています。</span><span class="sxs-lookup"><span data-stu-id="5a602-255">Setting FK values to null.</span></span> <span data-ttu-id="5a602-256">これは、依存/子がプリンシパル/親に関連付けられなくなったことを示します。</span><span class="sxs-lookup"><span data-stu-id="5a602-256">This indicates that the dependents/children are no longer related to any principal/parent.</span></span> <span data-ttu-id="5a602-257">これは、外部キーを null 許容にする必要があるオプションのリレーションシップの既定値です。</span><span class="sxs-lookup"><span data-stu-id="5a602-257">This is the default for optional relationships where the foreign key must be nullable.</span></span> <span data-ttu-id="5a602-258">FK を null に設定することは、必要なリレーションシップに対しては無効です。通常、外部キーは null 非許容です。</span><span class="sxs-lookup"><span data-stu-id="5a602-258">Setting the FK to null is not valid for required relationships, where the foreign key is typically non-nullable.</span></span>
2. <span data-ttu-id="5a602-259">依存/子を削除しています。</span><span class="sxs-lookup"><span data-stu-id="5a602-259">Deleting the the dependents/children.</span></span> <span data-ttu-id="5a602-260">これは必須のリレーションシップの既定値であり、オプションのリレーションシップに対しても有効です。</span><span class="sxs-lookup"><span data-stu-id="5a602-260">This is the default for required relationships, and is also valid for optional relationships.</span></span>

<span data-ttu-id="5a602-261">変更の追跡とリレーションシップの詳細については、「 [外部キーとナビゲーションの変更](xref:core/change-tracking/relationship-changes) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5a602-261">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for detailed information on change tracking and relationships.</span></span>

### <a name="optional-relationships"></a><span data-ttu-id="5a602-262">省略可能なリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="5a602-262">Optional relationships</span></span>

<span data-ttu-id="5a602-263">`Post.BlogId`外部キープロパティは、使用しているモデルで null 値が許容されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-263">The `Post.BlogId` foreign key property is nullable in the model we have been using.</span></span> <span data-ttu-id="5a602-264">つまり、リレーションシップは省略可能であるため、EF Core の既定の動作では、 `BlogId` ブログが削除されるときに、外部キープロパティが null に設定されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-264">This means the relationship is optional, and hence the default behavior of EF Core is to set `BlogId` foreign key properties to null when the blog is deleted.</span></span> <span data-ttu-id="5a602-265">例:</span><span class="sxs-lookup"><span data-stu-id="5a602-265">For example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_principal_parent_entities_1)]

<span data-ttu-id="5a602-266">の呼び出しの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を調べると、 `Remove` 予想どおり、ブログが次のようにマークされていることがわかり `Deleted` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-266">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following the call to `Remove` shows that, as expected, the blog is now marked as `Deleted`:</span></span>

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

<span data-ttu-id="5a602-267">興味深いことに、すべての関連する投稿はとしてマークされました `Modified` 。</span><span class="sxs-lookup"><span data-stu-id="5a602-267">More interestingly, all the related posts are now marked as `Modified`.</span></span> <span data-ttu-id="5a602-268">これは、各エンティティの外部キープロパティが null に設定されているためです。</span><span class="sxs-lookup"><span data-stu-id="5a602-268">This is because the foreign key property in each entity has been set to null.</span></span> <span data-ttu-id="5a602-269">SaveChanges を呼び出すと、データベース内の各投稿の外部キー値が null に更新されてから、ブログが削除されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-269">Calling SaveChanges updates the foreign key value for each post to null in the database, before then deleting the blog:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p2;
SELECT changes();
```

<span data-ttu-id="5a602-270">SaveChanges が完了した後、削除されたエンティティはデータベースに存在しなくなったため、DbContext からデタッチされます。</span><span class="sxs-lookup"><span data-stu-id="5a602-270">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="5a602-271">他のエンティティは `Unchanged` 、データベースの状態と一致する null 外部キー値を使用してとしてマークされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="5a602-271">Other entities are now marked as `Unchanged` with null foreign key values, which matches the state of the database:</span></span>

```output
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: <null> FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: <null> FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

### <a name="required-relationships"></a><span data-ttu-id="5a602-272">必須リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="5a602-272">Required relationships</span></span>

<span data-ttu-id="5a602-273">`Post.BlogId`外部キープロパティが null 非許容の場合、ブログと投稿の関係が "必須" になります。</span><span class="sxs-lookup"><span data-stu-id="5a602-273">If the `Post.BlogId` foreign key property is non-nullable, then the relationship between blogs and posts becomes "required".</span></span> <span data-ttu-id="5a602-274">このような場合、既定では、プリンシパル/親が削除されると、EF Core によって依存/子エンティティが削除されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-274">In this situation, EF Core will, by default, delete dependent/child entities when the principal/parent is deleted.</span></span> <span data-ttu-id="5a602-275">たとえば、前の例のように、関連する投稿を含むブログを削除します。</span><span class="sxs-lookup"><span data-stu-id="5a602-275">For example, deleting a blog with related posts as in the previous example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysRequiredSamples.cs?name=Deleting_principal_parent_entities_1)]

<span data-ttu-id="5a602-276">の呼び出しの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を調べると、 `Remove` 予想どおり、ブログは次のようにマークされ `Deleted` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-276">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following the call to `Remove` shows that, as expected, the blog is again marked as `Deleted`:</span></span>

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Deleted
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="5a602-277">ここで興味深いのは、関連するすべての投稿もとしてマークされていることです `Deleted` 。</span><span class="sxs-lookup"><span data-stu-id="5a602-277">More interestingly in this case is that all related posts have also been marked as `Deleted`.</span></span> <span data-ttu-id="5a602-278">SaveChanges を呼び出すと、ブログと関連するすべての投稿がデータベースから削除されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-278">Calling SaveChanges causes the blog and all related posts to be deleted from the database:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p1;
```

<span data-ttu-id="5a602-279">SaveChanges が完了した後、削除されたすべてのエンティティは、データベースに存在しなくなったため、DbContext からデタッチされます。</span><span class="sxs-lookup"><span data-stu-id="5a602-279">After SaveChanges completes, all the deleted entities are detached from the DbContext since they no longer exist in the database.</span></span> <span data-ttu-id="5a602-280">そのため、デバッグビューからの出力は空です。</span><span class="sxs-lookup"><span data-stu-id="5a602-280">Output from the debug view is therefore empty.</span></span>

> [!NOTE]
> <span data-ttu-id="5a602-281">このドキュメントでは、EF Core でのリレーションシップの操作に関する表面についてのみ説明します。</span><span class="sxs-lookup"><span data-stu-id="5a602-281">This document only scratches the surface on working with relationships in EF Core.</span></span> <span data-ttu-id="5a602-282">リレーションシップのモデリングの詳細および[外部キーとナビゲーションの変更](xref:core/change-tracking/relationship-changes)の詳細については、「[リレーションシップ](xref:core/modeling/relationships)」を参照してください。 SaveChanges を呼び出す場合の依存/子エンティティの更新/削除の詳細については、「</span><span class="sxs-lookup"><span data-stu-id="5a602-282">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships, and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on updating/deleting dependent/child entities when calling SaveChanges.</span></span>

## <a name="custom-tracking-with-trackgraph"></a><span data-ttu-id="5a602-283">TrackGraph を使用したカスタム追跡</span><span class="sxs-lookup"><span data-stu-id="5a602-283">Custom tracking with TrackGraph</span></span>

<span data-ttu-id="5a602-284"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> は `Add` と似 `Attach` て `Update` いますが、追跡する前にすべてのエンティティインスタンスに対してコールバックを生成する点が異なります。</span><span class="sxs-lookup"><span data-stu-id="5a602-284"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> works like `Add`, `Attach` and `Update` except that it generates a callback for every entity instance before tracking it.</span></span> <span data-ttu-id="5a602-285">これにより、グラフ内の個々のエンティティを追跡する方法を決定するときに、カスタムロジックを使用できます。</span><span class="sxs-lookup"><span data-stu-id="5a602-285">This allows custom logic to be used when determining how to track individual entities in a graph.</span></span>

<span data-ttu-id="5a602-286">たとえば、生成されたキー値を持つエンティティを追跡するときに使用 EF Core ルールを考えてみます。キー値がゼロの場合、エンティティは新しいので、挿入する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a602-286">For example, consider the rule EF Core uses when tracking entities with generated key values: if the key value is zero, then the entity is new and should be inserted.</span></span> <span data-ttu-id="5a602-287">このルールを拡張して、キー値が負であるかどうかを示し、エンティティを削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a602-287">Let's extend this rule to say if the key value is negative, then the entity should be deleted.</span></span> <span data-ttu-id="5a602-288">これにより、切断されたグラフのエンティティの主キー値を変更して、削除されたエンティティをマークすることができます。</span><span class="sxs-lookup"><span data-stu-id="5a602-288">This allows us to change the primary key values in entities of a disconnected graph to mark deleted entities:</span></span>

<!--
            blog.Posts.Add(
                new Post
                {
                    Title = "Announcing .NET 5.0",
                    Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                }
            );

            var toDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            toDelete.Id = -toDelete.Id;
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1a](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1a)]

<span data-ttu-id="5a602-289">この切断されたグラフは、TrackGraph を使用して追跡できます。</span><span class="sxs-lookup"><span data-stu-id="5a602-289">This disconnected graph can then be tracked using TrackGraph:</span></span>

<!--
        public static void UpdateBlog(Blog blog)
        {
            using var context = new BlogsContext();

            context.ChangeTracker.TrackGraph(
                blog, node =>
                    {
                        var propertyEntry = node.Entry.Property("Id");
                        var keyValue = (int)propertyEntry.CurrentValue;

                        if (keyValue == 0)
                        {
                            node.Entry.State = EntityState.Added;
                        }
                        else if (keyValue < 0)
                        {
                            propertyEntry.CurrentValue = -keyValue;
                            node.Entry.State = EntityState.Deleted;
                        }
                        else
                        {
                            node.Entry.State = EntityState.Modified;
                        }

                        Console.WriteLine($"Tracking {node.Entry.Metadata.DisplayName()} with key value {keyValue} as {node.Entry.State}");

                    });

            context.SaveChanges();
        }
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1b](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1b)]

<span data-ttu-id="5a602-290">上記のコードでは、グラフ内の各エンティティについて、 _エンティティを追跡する前に_ 主キーの値を確認します。</span><span class="sxs-lookup"><span data-stu-id="5a602-290">For each entity in the graph, the code above checks the primary key value _before tracking the entity_.</span></span> <span data-ttu-id="5a602-291">値が設定されていない (ゼロ) キー値の場合、コードは通常実行する EF Core を行います。</span><span class="sxs-lookup"><span data-stu-id="5a602-291">For unset (zero) key values, the code does what EF Core would normally do.</span></span> <span data-ttu-id="5a602-292">つまり、キーが設定されていない場合、エンティティはとマークされ `Added` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-292">That is, if the key is not set, then the entity is marked as `Added`.</span></span> <span data-ttu-id="5a602-293">キーが設定されていて、値が負でない場合、エンティティはとマークされ `Modified` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-293">If the key is set and the value is non-negative, then the entity is marked as `Modified`.</span></span> <span data-ttu-id="5a602-294">ただし、負ではないキー値が見つかった場合は、その実際の負でない値が復元され、エンティティはとして追跡され `Deleted` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-294">However, if a negative key value is found, then its real, non-negative value is restored and the entity is tracked as `Deleted`.</span></span>

<span data-ttu-id="5a602-295">このコードを実行した場合の出力は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="5a602-295">The output from running this code is:</span></span>

```output
Tracking Blog with key value 1 as Modified
Tracking Post with key value 1 as Modified
Tracking Post with key value -2 as Deleted
Tracking Post with key value 0 as Added
```

> [!NOTE]
> <span data-ttu-id="5a602-296">わかりやすくするために、このコードでは、各エンティティにという整数の主キープロパティがあることを前提としてい `Id` ます。</span><span class="sxs-lookup"><span data-stu-id="5a602-296">For simplicity, this code assumes each entity has an integer primary key property called `Id`.</span></span> <span data-ttu-id="5a602-297">これは、抽象基本クラスまたはインターフェイスに体系化ことができます。</span><span class="sxs-lookup"><span data-stu-id="5a602-297">This could be codified into an abstract base class or interface.</span></span> <span data-ttu-id="5a602-298">または、 <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> このコードがあらゆる種類のエンティティで動作するように、メタデータから主キーのプロパティを取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="5a602-298">Alternately, the primary key property or properties could be obtained from the <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata such that this code would work with any type of entity.</span></span>

<span data-ttu-id="5a602-299">TrackGraph には2つのオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="5a602-299">TrackGraph has two overloads.</span></span> <span data-ttu-id="5a602-300">上で使用した単純なオーバーロードでは、EF Core は、グラフの走査をいつ停止するかを決定します。</span><span class="sxs-lookup"><span data-stu-id="5a602-300">In the simple overload used above, EF Core determines when to stop traversing the graph.</span></span> <span data-ttu-id="5a602-301">具体的には、エンティティが既に追跡されている場合、またはコールバックがエンティティの追跡を開始しない場合、特定のエンティティからの新しい関連エンティティへのアクセスを停止します。</span><span class="sxs-lookup"><span data-stu-id="5a602-301">Specifically, it stops visiting new related entities from a given entity when that entity is either already tracked, or when the callback does not start tracking the entity.</span></span>

<span data-ttu-id="5a602-302">高度なオーバーロードには、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType> ブール値を返すコールバックがあります。</span><span class="sxs-lookup"><span data-stu-id="5a602-302">The advanced overload, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType>, has a callback that returns a bool.</span></span> <span data-ttu-id="5a602-303">コールバックが false を返す場合、グラフトラバーサルは停止します。それ以外の場合は、続行されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-303">If the callback returns false, then graph traversal stops, otherwise it continues.</span></span> <span data-ttu-id="5a602-304">このオーバーロードを使用する場合は、無限ループを避けるために注意する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5a602-304">Care must be taken to avoid infinite loops when using this overload.</span></span>

<span data-ttu-id="5a602-305">高度なオーバーロードでは、TrackGraph に状態を指定することもできます。この状態は、各コールバックに渡されます。</span><span class="sxs-lookup"><span data-stu-id="5a602-305">The advanced overload also allows state to be supplied to TrackGraph and this state is then passed to each callback.</span></span>
