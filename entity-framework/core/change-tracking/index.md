---
title: 変更の追跡 - EF Core
description: EF Core の変更の追跡に関する概要
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/index
ms.openlocfilehash: 52223e5472b09271d19ac9449a3989b4a0e277f7
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129520"
---
# <a name="change-tracking-in-ef-core"></a><span data-ttu-id="707f6-103">EF Core での変更の追跡</span><span class="sxs-lookup"><span data-stu-id="707f6-103">Change Tracking in EF Core</span></span>

<span data-ttu-id="707f6-104">各 <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスによって、エンティティに加えられる変更が追跡されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="707f6-105">さらに、これらの追跡対象エンティティによって、<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> が呼び出されたときにデータベースへの変更が実行されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="707f6-106">このドキュメントでは、Entity Framework Core (EF Core) の変更の追跡に関する概要と、それがクエリと更新にどのように関連しているかについて説明します。</span><span class="sxs-lookup"><span data-stu-id="707f6-106">This document presents an overview of Entity Framework Core (EF Core) change tracking and how it relates to queries and updates.</span></span>

> [!TIP]
> <span data-ttu-id="707f6-107">このドキュメントに含まれているすべてのコードは、[GitHub からサンプル コードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore)することで実行およびデバッグできます。</span><span class="sxs-lookup"><span data-stu-id="707f6-107">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span></span>

> [!TIP]
> <span data-ttu-id="707f6-108">わかりやすくするために、このドキュメントでは <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> などの同期メソッドを使用および参照しています。その非同期バージョンである <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> などは使用していません。</span><span class="sxs-lookup"><span data-stu-id="707f6-108">For simplicity, this document uses and references synchronous methods such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> rather their async equivalents such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span></span> <span data-ttu-id="707f6-109">特に明記されていない限り、非同期メソッドの呼び出しと待機は置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="707f6-109">Calling and awaiting the async method can be substituted unless otherwise noted.</span></span>

## <a name="how-to-track-entities"></a><span data-ttu-id="707f6-110">エンティティを追跡する方法</span><span class="sxs-lookup"><span data-stu-id="707f6-110">How to track entities</span></span>

<span data-ttu-id="707f6-111">エンティティ インスタンスは、次の場合に追跡が開始されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-111">Entity instances become tracked when they are:</span></span>

- <span data-ttu-id="707f6-112">データベースに対して実行されたクエリから返されるとき</span><span class="sxs-lookup"><span data-stu-id="707f6-112">Returned from a query executed against the database</span></span>
- <span data-ttu-id="707f6-113">`Add`、`Attach`、`Update`、または同様のメソッドによって明示的に DbContext にアタッチされるとき</span><span class="sxs-lookup"><span data-stu-id="707f6-113">Explicitly attached to the DbContext by `Add`, `Attach`, `Update`, or similar methods</span></span>
- <span data-ttu-id="707f6-114">既存の追跡対象エンティティに接続された新しいエンティティとして検出されるとき</span><span class="sxs-lookup"><span data-stu-id="707f6-114">Detected as new entities connected to existing tracked entities</span></span>

<span data-ttu-id="707f6-115">次の場合、エンティティ インスタンスは追跡されなくなります。</span><span class="sxs-lookup"><span data-stu-id="707f6-115">Entity instances are no longer tracked when:</span></span>

- <span data-ttu-id="707f6-116">DbContext が破棄されるとき</span><span class="sxs-lookup"><span data-stu-id="707f6-116">The DbContext is disposed</span></span>
- <span data-ttu-id="707f6-117">変更トラッカーがクリアされるとき (EF Core 5.0 以降)</span><span class="sxs-lookup"><span data-stu-id="707f6-117">The change tracker is cleared (EF Core 5.0 and later)</span></span>
- <span data-ttu-id="707f6-118">エンティティが明示的にデタッチされるとき</span><span class="sxs-lookup"><span data-stu-id="707f6-118">The entities are explicitly detached</span></span>

<span data-ttu-id="707f6-119">DbContext は、[DbContext の初期化と構成](xref:core/dbcontext-configuration/index)に関する記事で説明されているように、有効期間の短い作業単位を表すように設計されています。</span><span class="sxs-lookup"><span data-stu-id="707f6-119">DbContext is designed to represent a short-lived unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="707f6-120">つまり、DbContext の破棄は、エンティティの追跡を停止するための "_通常の方法_" です。</span><span class="sxs-lookup"><span data-stu-id="707f6-120">This means that disposing the DbContext is _the normal way_ to stop tracking entities.</span></span> <span data-ttu-id="707f6-121">言い換えると、DbContext の有効期間は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="707f6-121">In other words, the lifetime of a DbContext should be:</span></span>

1. <span data-ttu-id="707f6-122">DbContext インスタンスを作成する</span><span class="sxs-lookup"><span data-stu-id="707f6-122">Create the DbContext instance</span></span>
2. <span data-ttu-id="707f6-123">いくつかのエンティティを追跡する</span><span class="sxs-lookup"><span data-stu-id="707f6-123">Track some entities</span></span>
3. <span data-ttu-id="707f6-124">エンティティにいくつかの変更を加える</span><span class="sxs-lookup"><span data-stu-id="707f6-124">Make some changes to the entities</span></span>
4. <span data-ttu-id="707f6-125">SaveChanges を呼び出してデータベースを更新する</span><span class="sxs-lookup"><span data-stu-id="707f6-125">Call SaveChanges to update the database</span></span>
5. <span data-ttu-id="707f6-126">DbContext インスタンスを破棄する</span><span class="sxs-lookup"><span data-stu-id="707f6-126">Dispose the DbContext instance</span></span>

> [!TIP]
> <span data-ttu-id="707f6-127">この方法を採用する場合は、変更トラッカーをクリアしたり、エンティティ インスタンスを明示的にデタッチしたりする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="707f6-127">It is not necessary to clear the change tracker or explicitly detach entity instances when taking this approach.</span></span> <span data-ttu-id="707f6-128">ただし、エンティティをデタッチする必要がある場合は、エンティティを 1 つずつデタッチするよりも <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> を呼び出す方が効率的です。</span><span class="sxs-lookup"><span data-stu-id="707f6-128">However, if you do need to detach entities, then calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> is more efficient than detaching entities one-by-one.</span></span>

## <a name="entity-states"></a><span data-ttu-id="707f6-129">エンティティの状態</span><span class="sxs-lookup"><span data-stu-id="707f6-129">Entity states</span></span>

<span data-ttu-id="707f6-130">すべてのエンティティは、特定の <xref:Microsoft.EntityFrameworkCore.EntityState> に関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="707f6-130">Every entity is is associated with a given <xref:Microsoft.EntityFrameworkCore.EntityState>:</span></span>

- <span data-ttu-id="707f6-131">`Detached` エンティティは <xref:Microsoft.EntityFrameworkCore.DbContext> によって追跡されていません。</span><span class="sxs-lookup"><span data-stu-id="707f6-131">`Detached` entities are not being tracked by the <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span>
- <span data-ttu-id="707f6-132">`Added` エンティティは新規で、まだデータベースに挿入されていません。</span><span class="sxs-lookup"><span data-stu-id="707f6-132">`Added` entities are new and have not yet been inserted into the database.</span></span> <span data-ttu-id="707f6-133">つまり、<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> が呼び出されたときに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-133">This means they will be inserted when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>
- <span data-ttu-id="707f6-134">`Unchanged` エンティティは、データベースからクエリされてから変更されて "_いません_"。</span><span class="sxs-lookup"><span data-stu-id="707f6-134">`Unchanged` entities have _not_ been changed since they were queried from the database.</span></span> <span data-ttu-id="707f6-135">クエリから返されるすべてのエンティティは、最初はこの状態になります。</span><span class="sxs-lookup"><span data-stu-id="707f6-135">All entities returned from queries are initially in this state.</span></span>
- <span data-ttu-id="707f6-136">`Modified` エンティティは、データベースからクエリされてから変更されています。</span><span class="sxs-lookup"><span data-stu-id="707f6-136">`Modified` entities have been changed since they were queried from the database.</span></span> <span data-ttu-id="707f6-137">つまり、SaveChanges が呼び出されたときに更新されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-137">This means they will be updated when SaveChanges is called.</span></span>
- <span data-ttu-id="707f6-138">`Deleted` エンティティはデータベースに存在していますが、SaveChanges が呼び出されたときに削除されるようにマークされています。</span><span class="sxs-lookup"><span data-stu-id="707f6-138">`Deleted` entities exist in the database, but are marked to be deleted when SaveChanges is called.</span></span>

<span data-ttu-id="707f6-139">EF Core では、プロパティ レベルで変更が追跡されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-139">EF Core tracks changes at the property level.</span></span> <span data-ttu-id="707f6-140">たとえば、1 つのプロパティ値のみが変更された場合、データベースの更新によってその値のみが変更されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-140">For example, if only a single property value is modified, then a database update will change only that value.</span></span> <span data-ttu-id="707f6-141">ただし、プロパティは、エンティティ自体が Modified 状態である場合にのみ、変更済みとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="707f6-141">However, properties can only be marked as modified when the entity itself is in the Modified state.</span></span> <span data-ttu-id="707f6-142">(別の観点から見ると、Modified 状態は、少なくとも 1 つのプロパティ値が変更済みとしてマークされていることを意味します。)</span><span class="sxs-lookup"><span data-stu-id="707f6-142">(Or, from an alternate perspective, the Modified state means that at least one property value has been marked as modified.)</span></span>

<span data-ttu-id="707f6-143">次の表は、さまざまな状態をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="707f6-143">The following table summarizes the different states:</span></span>

| <span data-ttu-id="707f6-144">エンティティ状態</span><span class="sxs-lookup"><span data-stu-id="707f6-144">Entity state</span></span>     | <span data-ttu-id="707f6-145">DbContext によって追跡される</span><span class="sxs-lookup"><span data-stu-id="707f6-145">Tracked by DbContext</span></span> | <span data-ttu-id="707f6-146">データベースに存在する</span><span class="sxs-lookup"><span data-stu-id="707f6-146">Exists in database</span></span> | <span data-ttu-id="707f6-147">プロパティが変更された</span><span class="sxs-lookup"><span data-stu-id="707f6-147">Properties modified</span></span> | <span data-ttu-id="707f6-148">SaveChanges でのアクション</span><span class="sxs-lookup"><span data-stu-id="707f6-148">Action on SaveChanges</span></span>
|:-----------------|----------------------|--------------------|---------------------|-----------------------
| `Detached`       | <span data-ttu-id="707f6-149">いいえ</span><span class="sxs-lookup"><span data-stu-id="707f6-149">No</span></span>                   | -                  | -                   | -
| `Added`          | <span data-ttu-id="707f6-150">はい</span><span class="sxs-lookup"><span data-stu-id="707f6-150">Yes</span></span>                  | <span data-ttu-id="707f6-151">いいえ</span><span class="sxs-lookup"><span data-stu-id="707f6-151">No</span></span>                 | -                   | <span data-ttu-id="707f6-152">挿入</span><span class="sxs-lookup"><span data-stu-id="707f6-152">Insert</span></span>
| `Unchanged`      | <span data-ttu-id="707f6-153">はい</span><span class="sxs-lookup"><span data-stu-id="707f6-153">Yes</span></span>                  | <span data-ttu-id="707f6-154">はい</span><span class="sxs-lookup"><span data-stu-id="707f6-154">Yes</span></span>                | <span data-ttu-id="707f6-155">いいえ</span><span class="sxs-lookup"><span data-stu-id="707f6-155">No</span></span>                  | -
| `Modified`       | <span data-ttu-id="707f6-156">はい</span><span class="sxs-lookup"><span data-stu-id="707f6-156">Yes</span></span>                  | <span data-ttu-id="707f6-157">はい</span><span class="sxs-lookup"><span data-stu-id="707f6-157">Yes</span></span>                | <span data-ttu-id="707f6-158">はい</span><span class="sxs-lookup"><span data-stu-id="707f6-158">Yes</span></span>                 | <span data-ttu-id="707f6-159">更新</span><span class="sxs-lookup"><span data-stu-id="707f6-159">Update</span></span>
| `Deleted`        | <span data-ttu-id="707f6-160">はい</span><span class="sxs-lookup"><span data-stu-id="707f6-160">Yes</span></span>                  | <span data-ttu-id="707f6-161">はい</span><span class="sxs-lookup"><span data-stu-id="707f6-161">Yes</span></span>                | -                   | <span data-ttu-id="707f6-162">削除</span><span class="sxs-lookup"><span data-stu-id="707f6-162">Delete</span></span>

> [!NOTE]
> <span data-ttu-id="707f6-163">このテキストでは、わかりやすくするために、リレーショナル データベースの用語を使用しています。</span><span class="sxs-lookup"><span data-stu-id="707f6-163">This text uses relational database terms for clarity.</span></span> <span data-ttu-id="707f6-164">通常、NoSQL データベースでも同様の操作がサポートされていますが、名前が異なる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="707f6-164">NoSQL databases typically support similar operations but possibly with different names.</span></span> <span data-ttu-id="707f6-165">詳細については、お使いのデータベース プロバイダーのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="707f6-165">Consult your database provider documentation for more information.</span></span>

## <a name="tracking-from-queries"></a><span data-ttu-id="707f6-166">クエリからの追跡</span><span class="sxs-lookup"><span data-stu-id="707f6-166">Tracking from queries</span></span>

<span data-ttu-id="707f6-167">EF Core の変更の追跡が最も効果を発揮するのは、同じ <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスを使用してエンティティのクエリを実行し、<xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> を呼び出して更新する場合です。</span><span class="sxs-lookup"><span data-stu-id="707f6-167">EF Core change tracking works best when the same <xref:Microsoft.EntityFrameworkCore.DbContext> instance is used to both query for entities and update them by calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="707f6-168">これは、EF Core によって、クエリされたエンティティの状態が自動的に追跡され、SaveChanges が呼び出されたときにこれらのエンティティに加えられた変更が検出されるためです。</span><span class="sxs-lookup"><span data-stu-id="707f6-168">This is because EF Core automatically tracks the state of queried entities and then detects any changes made to these entities when SaveChanges is called.</span></span>

<span data-ttu-id="707f6-169">この方法には、[エンティティ インスタンスを明示的に追跡する](xref:core/change-tracking/explicit-tracking)場合と比べていくつかの利点があります。</span><span class="sxs-lookup"><span data-stu-id="707f6-169">This approach has several advantages over [explicitly tracking entity instances](xref:core/change-tracking/explicit-tracking):</span></span>

- <span data-ttu-id="707f6-170">単純である。</span><span class="sxs-lookup"><span data-stu-id="707f6-170">It is simple.</span></span> <span data-ttu-id="707f6-171">エンティティの状態を明示的に操作することが必要になる場合はほとんどありません。EF Core によって状態の変化が対処されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-171">Entity states rarely need to be manipulated explicitly--EF Core takes care of state changes.</span></span>
- <span data-ttu-id="707f6-172">更新の対象は、実際に変更された値のみに制限されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-172">Updates are limited to only those values that have actually changed.</span></span>
- <span data-ttu-id="707f6-173">[シャドウ プロパティ](xref:core/modeling/shadow-properties)の値は保持され、必要に応じて使用されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-173">The values of [shadow properties](xref:core/modeling/shadow-properties) are preserved and used as needed.</span></span> <span data-ttu-id="707f6-174">これは、外部キーがシャドウ状態で格納されている場合に特に重要になります。</span><span class="sxs-lookup"><span data-stu-id="707f6-174">This is especially relevant when foreign keys are stored in shadow state.</span></span>
- <span data-ttu-id="707f6-175">プロパティの元の値は自動的に保持され、効率的な更新のために使用されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-175">The original values of properties are preserved automatically and used for efficient updates.</span></span>

## <a name="simple-query-and-update"></a><span data-ttu-id="707f6-176">シンプルなクエリと更新</span><span class="sxs-lookup"><span data-stu-id="707f6-176">Simple query and update</span></span>

<span data-ttu-id="707f6-177">たとえば、シンプルなブログ/投稿モデルを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="707f6-177">For example, consider a simple blog/posts model:</span></span>

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

<span data-ttu-id="707f6-178">このモデルを使用して、ブログや投稿に対するクエリを実行した後、データベースにいくつかの更新を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="707f6-178">We can use this model to query for blogs and posts and then make some updates to the database:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            blog.Name = ".NET Blog (Updated!)";

            foreach (var post in blog.Posts.Where(e => !e.Title.Contains("5.0")))
            {
                post.Title = post.Title.Replace("5", "5.0");
            }

            context.SaveChanges();
-->
[!code-csharp[Simple_query_and_update_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_1)]

<span data-ttu-id="707f6-179">SaveChanges を呼び出すと、次のデータベースの更新が発生します (データベースの例として SQLite を使用しています)。</span><span class="sxs-lookup"><span data-stu-id="707f6-179">Calling SaveChanges results in the following database updates, using SQLite as an example database:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0='Announcing F# 5.0' (Size = 17)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "Title" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

<span data-ttu-id="707f6-180">[変更トラッカーのデバッグ ビュー](xref:core/change-tracking/debug-views)は、追跡されているエンティティとその状態を視覚化するための優れた方法です。</span><span class="sxs-lookup"><span data-stu-id="707f6-180">The [change tracker debug view](xref:core/change-tracking/debug-views) is a great way visualize which entities are being tracked and what their states are.</span></span> <span data-ttu-id="707f6-181">たとえば、上記のサンプルの SaveChanges を呼び出す前に、次のコードを挿入します。</span><span class="sxs-lookup"><span data-stu-id="707f6-181">For example, inserting the following code into the sample above before calling SaveChanges:</span></span>

<!--
                context.ChangeTracker.DetectChanges();
                Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Simple_query_and_update_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_2)]

<span data-ttu-id="707f6-182">次の出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-182">Generates the following output:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="707f6-183">特に以下の点に注目してください。</span><span class="sxs-lookup"><span data-stu-id="707f6-183">Notice specifically:</span></span>

- <span data-ttu-id="707f6-184">`Blog.Name` プロパティは変更済み (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`) としてマークされており、その結果、ブログの状態が `Modified` になっています。</span><span class="sxs-lookup"><span data-stu-id="707f6-184">The `Blog.Name` property is marked as modified (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`), and this results in the blog being in the `Modified` state.</span></span>
- <span data-ttu-id="707f6-185">投稿 2 の `Post.Title` プロパティは変更済み (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`) としてマークされており、その結果、この投稿の状態が `Modified` になっています。</span><span class="sxs-lookup"><span data-stu-id="707f6-185">The `Post.Title` property of post 2 is marked as modified (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`), and this results in this post being in the `Modified` state.</span></span>
- <span data-ttu-id="707f6-186">投稿 2 のその他のプロパティ値は変更されていないため、変更済みとしてマークされていません。</span><span class="sxs-lookup"><span data-stu-id="707f6-186">The other property values of post 2 have not changed and are therefore not marked as modified.</span></span> <span data-ttu-id="707f6-187">このため、これらの値はデータベースの更新に含まれていません。</span><span class="sxs-lookup"><span data-stu-id="707f6-187">This is why these values are not included in the database update.</span></span>
- <span data-ttu-id="707f6-188">その他の投稿は、どのような方法でも変更されていません。</span><span class="sxs-lookup"><span data-stu-id="707f6-188">The other post was not modified in any way.</span></span> <span data-ttu-id="707f6-189">そのため `Unchanged` 状態のままであり、データベースの更新には含まれていません。</span><span class="sxs-lookup"><span data-stu-id="707f6-189">This is why it is still in the `Unchanged` state and is not included in the database update.</span></span>

## <a name="query-then-insert-update-and-delete"></a><span data-ttu-id="707f6-190">クエリの後に挿入、更新、および削除を行う</span><span class="sxs-lookup"><span data-stu-id="707f6-190">Query then insert, update, and delete</span></span>

<span data-ttu-id="707f6-191">前の例に含まれているような更新は、同じ作業単位の中で挿入や削除と組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="707f6-191">Updates like those in the previous example can be combined with inserts and deletes in the same unit-of-work.</span></span> <span data-ttu-id="707f6-192">例:</span><span class="sxs-lookup"><span data-stu-id="707f6-192">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Modify property values
            blog.Name = ".NET Blog (Updated!)";

            // Insert a new Post
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            // Mark an existing Post as Deleted
            var postToDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            context.Remove(postToDelete);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[Query_then_insert_update_and_delete_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Query_then_insert_update_and_delete_1)]

<span data-ttu-id="707f6-193">次の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="707f6-193">In this example:</span></span>

- <span data-ttu-id="707f6-194">ブログと関連する投稿がデータベースからクエリされ、追跡されます</span><span class="sxs-lookup"><span data-stu-id="707f6-194">A blog and related posts are queried from the database and tracked</span></span>
- <span data-ttu-id="707f6-195">`Blog.Name` プロパティが変更されます</span><span class="sxs-lookup"><span data-stu-id="707f6-195">The `Blog.Name` property is changed</span></span>
- <span data-ttu-id="707f6-196">ブログの既存の投稿のコレクションに新しい投稿が追加されます</span><span class="sxs-lookup"><span data-stu-id="707f6-196">A new post is added to the collection of existing posts for the blog</span></span>
- <span data-ttu-id="707f6-197">既存の投稿が、<xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> を呼び出すことによって削除対象としてマークされます</span><span class="sxs-lookup"><span data-stu-id="707f6-197">An existing post is marked for deletion by calling <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType></span></span>

<span data-ttu-id="707f6-198">SaveChanges を呼び出す前に[変更トラッカーのデバッグ ビュー](xref:core/change-tracking/debug-views)をもう一度確認すると、EF Core によってこれらの変更がどのように追跡されているかがわかります。</span><span class="sxs-lookup"><span data-stu-id="707f6-198">Looking again at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows how EF Core is tracking these changes:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}, {Id: -2147482638}]
Post {Id: -2147482638} Added
  Id: -2147482638 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
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

<span data-ttu-id="707f6-199">次のことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="707f6-199">Notice that:</span></span>

- <span data-ttu-id="707f6-200">ブログは `Modified` としてマークされています。</span><span class="sxs-lookup"><span data-stu-id="707f6-200">The blog is marked as `Modified`.</span></span> <span data-ttu-id="707f6-201">これにより、データベースの更新が生成されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-201">This will generate a database update.</span></span>
- <span data-ttu-id="707f6-202">投稿 2 は `Deleted` としてマークされています。</span><span class="sxs-lookup"><span data-stu-id="707f6-202">Post 2 is marked as `Deleted`.</span></span> <span data-ttu-id="707f6-203">これにより、データベースの削除が生成されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-203">This will generate a database delete.</span></span>
- <span data-ttu-id="707f6-204">一時的な ID を持つ新しい投稿がブログ 1 に関連付けられ、`Added` としてマークされています。</span><span class="sxs-lookup"><span data-stu-id="707f6-204">A new post with a temporary ID is associated with blog 1 and is marked as `Added`.</span></span> <span data-ttu-id="707f6-205">これにより、データベースの挿入が生成されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-205">This will generate a database insert.</span></span>

<span data-ttu-id="707f6-206">これによって、SaveChanges が呼び出されたときに、次のデータベース コマンド (SQLite を使用) が生成されます。</span><span class="sxs-lookup"><span data-stu-id="707f6-206">This results in the following database commands (using SQLite) when SaveChanges is called:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 was released recently and has come with many...' (Size = 56), @p2='What's next for System.Text.Json?' (Size = 33)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="707f6-207">エンティティの挿入と削除の詳細については、「[エンティティの明示的な追跡](xref:core/change-tracking/explicit-tracking)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="707f6-207">See [Explicitly Tracking Entities](xref:core/change-tracking/explicit-tracking) for more information on inserting and deleting entities.</span></span> <span data-ttu-id="707f6-208">EF Core でこのような変更が自動的に検出されるしくみについて詳しくは、「[変更の検出と通知](xref:core/change-tracking/change-detection)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="707f6-208">See [Change Detection and Notifications](xref:core/change-tracking/change-detection) for more information on how EF Core automatically detects changes like this.</span></span>

> [!TIP]
> <span data-ttu-id="707f6-209"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> を呼び出すと、SaveChanges によってデータベースが更新されるような変更が行われたかどうかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="707f6-209">Call <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> to determine whether any changes have been made that will cause SaveChanges to make updates to the database.</span></span> <span data-ttu-id="707f6-210">HasChanges から false が返される場合は、SaveChanges によって何も行われません。</span><span class="sxs-lookup"><span data-stu-id="707f6-210">If HasChanges return false, then SaveChanges will be a no-op.</span></span>
