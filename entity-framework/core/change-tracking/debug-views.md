---
title: トラッカーのデバッグを変更する-EF Core
description: ChangeTracker DebugView とログメッセージを使用して EF Core 変更の追跡をデバッグする
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/debug-views
ms.openlocfilehash: 76108120cf7f532d085fef12121bb639b065add0
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129607"
---
# <a name="change-tracker-debugging"></a><span data-ttu-id="ed8c4-103">トラッカーのデバッグを変更する</span><span class="sxs-lookup"><span data-stu-id="ed8c4-103">Change Tracker Debugging</span></span>

<span data-ttu-id="ed8c4-104">Entity Framework Core (EF Core) の変更トラッカーは、デバッグに役立つ2種類の出力を生成します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-104">The Entity Framework Core (EF Core) change tracker generates two kinds of output to help with debugging:</span></span>

- <span data-ttu-id="ed8c4-105">は、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> 追跡されているすべてのエンティティの人間が判読できるビューを提供します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-105">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DebugView%2A?displayProperty=nameWithType> provides a human-readable view of all entities being tracked</span></span>
- <span data-ttu-id="ed8c4-106">デバッグレベルのログメッセージは、変更トラッカーによって状態が検出され、リレーションシップが修正されると生成されます</span><span class="sxs-lookup"><span data-stu-id="ed8c4-106">Debug-level log messages are generated when the change tracker detects state and fixes up relationships</span></span>

> [!TIP]
> <span data-ttu-id="ed8c4-107">このドキュメントでは、EF Core の変更の追跡のエンティティの状態と基本について理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-107">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="ed8c4-108">これらのトピックの詳細については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-108">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="ed8c4-109">[GitHub からサンプルコードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackerDebugging)することで、このドキュメントのすべてのコードを実行し、デバッグすることができます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-109">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackerDebugging).</span></span>

## <a name="change-tracker-debug-view"></a><span data-ttu-id="ed8c4-110">トラッカーデバッグビューの変更</span><span class="sxs-lookup"><span data-stu-id="ed8c4-110">Change tracker debug view</span></span>

<span data-ttu-id="ed8c4-111">変更トラッカーデバッグビューには、IDE のデバッガーでアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-111">The change tracker debug view can be accessed in the debugger of your IDE.</span></span> <span data-ttu-id="ed8c4-112">たとえば、Visual Studio では次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-112">For example, with Visual Studio:</span></span>

![Visual Studio デバッガーからの change tracker デバッグビューへのアクセス](_static/debug-view.png)

<span data-ttu-id="ed8c4-114">また、コードから直接アクセスすることもできます。たとえば、コンソールにデバッグビューを送信することができます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-114">It can also be accessed directly from code, for example to send the debug view to the console:</span></span>

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.ShortView);
-->
[!code-csharp[Change_tracker_debug_view_1b](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1b)]

<span data-ttu-id="ed8c4-115">デバッグビューには、短い形式と長い形式のフォームがあります。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-115">The debug view has a short form and a long form.</span></span> <span data-ttu-id="ed8c4-116">短い形式では、追跡対象のエンティティ、その状態、およびキー値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-116">The short form shows tracked entities, their state, and key values.</span></span> <span data-ttu-id="ed8c4-117">長い形式には、すべてのプロパティとナビゲーションの値および状態も含まれます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-117">The long form also includes all property and navigation values and state.</span></span>

### <a name="the-short-view"></a><span data-ttu-id="ed8c4-118">短いビュー</span><span class="sxs-lookup"><span data-stu-id="ed8c4-118">The short view</span></span>

<span data-ttu-id="ed8c4-119">このドキュメントの最後に示されているモデルを使用して、デバッグビューの例を見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-119">Let's look at a debug view example using the model shown at the end of this document.</span></span> <span data-ttu-id="ed8c4-120">まず、いくつかのエンティティを追跡し、いくつかの州に配置します。これにより、適切な変更追跡データが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-120">First, we will track some entities and put them in some different states, just so we have good change tracking data to view:</span></span>

<!--
        using var context = new BlogsContext();

        var blogs = context.Blogs
            .Include(e => e.Posts).ThenInclude(e => e.Tags)
            .Include(e => e.Assets)
            .ToList();

        // Mark something Added
        blogs[0].Posts.Add(
            new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many new features and..."
            });

        // Mark something Deleted
        blogs[1].Posts.Remove(blogs[1].Posts[1]);

        // Make something Modified
        blogs[0].Name = ".NET Blog (All new!)";

        context.ChangeTracker.DetectChanges();
-->
[!code-csharp[Change_tracker_debug_view_1a](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1a)]

<span data-ttu-id="ed8c4-121">この時点で短いビューを印刷すると、次のような出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-121">Printing the short view at this point, as shown above, results in the following output:</span></span>

```output
Blog {Id: 1} Modified AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}
Blog {Id: 2} Unchanged AK {AssetsId: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged FK {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged FK {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
Post {Id: -2147482643} Added FK {BlogId: 1}
Post {Id: 1} Unchanged FK {BlogId: 1}
Post {Id: 2} Unchanged FK {BlogId: 1}
Post {Id: 3} Unchanged FK {BlogId: 2}
Post {Id: 4} Deleted FK {BlogId: 2}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged FK {PostsId: 1} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged FK {PostsId: 1} FK {TagsId: 3}
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged FK {PostsId: 2} FK {TagsId: 1}
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged FK {PostsId: 3} FK {TagsId: 2}
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted FK {PostsId: 4} FK {TagsId: 2}
Tag {Id: 1} Unchanged
Tag {Id: 2} Unchanged
Tag {Id: 3} Unchanged
```

<span data-ttu-id="ed8c4-122">注意:</span><span class="sxs-lookup"><span data-stu-id="ed8c4-122">Notice:</span></span>

- <span data-ttu-id="ed8c4-123">追跡対象の各エンティティは、その主キー (PK) 値と共に一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-123">Each tracked entity is listed with its primary key (PK) value.</span></span> <span data-ttu-id="ed8c4-124">たとえば、「 `Blog {Id: 1}` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-124">For example, `Blog {Id: 1}`.</span></span>
- <span data-ttu-id="ed8c4-125">エンティティが共有型のエンティティ型である場合は、CLR 型も表示されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-125">If the entity is a shared-type entity type, then it's CLR type is also shown.</span></span> <span data-ttu-id="ed8c4-126">たとえば、「 `PostTag (Dictionary<string, object>)` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-126">For example, `PostTag (Dictionary<string, object>)`.</span></span>
- <span data-ttu-id="ed8c4-127"><xref:Microsoft.EntityFrameworkCore.EntityState>次に、が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-127">The <xref:Microsoft.EntityFrameworkCore.EntityState> is shown next.</span></span> <span data-ttu-id="ed8c4-128">これは、、、 `Unchanged` 、またはのいずれかになり `Added` `Modified` `Deleted` ます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-128">This will be one of `Unchanged`, `Added`, `Modified`, or `Deleted`.</span></span>
- <span data-ttu-id="ed8c4-129">次に、任意の代替キー (AKs) の値を示します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-129">Values for any alternate keys (AKs) are shown next.</span></span> <span data-ttu-id="ed8c4-130">たとえば、「 `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-130">For example, `AK {AssetsId: ed727978-1ffe-4709-baee-73913e8e44a0}`.</span></span>
- <span data-ttu-id="ed8c4-131">最後に、外部キー (FKs) の値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-131">Finally, values for any foreign keys (FKs) are shown.</span></span> <span data-ttu-id="ed8c4-132">たとえば、「 `FK {PostsId: 4} FK {TagsId: 2}` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-132">For example, `FK {PostsId: 4} FK {TagsId: 2}`.</span></span>

### <a name="the-long-view"></a><span data-ttu-id="ed8c4-133">長いビュー</span><span class="sxs-lookup"><span data-stu-id="ed8c4-133">The long view</span></span>

<span data-ttu-id="ed8c4-134">長いビューは、短いビューと同じようにコンソールに送信できます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-134">The long view can be sent to the console in the same way as the short view:</span></span>

<!--
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracker_debug_view_1c](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Change_tracker_debug_view_1c)]

<span data-ttu-id="ed8c4-135">上の短いビューと同じ状態の出力は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-135">The output for the same state as the short view above is:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK
  Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'
  Assets: {Id: ed727978-1ffe-4709-baee-73913e8e44a0}
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  AssetsId: '3a54b880-2b9d-486b-9403-dc2e52d36d65' AK
  Name: 'Visual Studio Blog'
  Assets: {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65}
  Posts: [{Id: 3}]
BlogAssets {Id: 3a54b880-2b9d-486b-9403-dc2e52d36d65} Unchanged
  Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK
  Banner: <null>
  Blog: {Id: 2}
BlogAssets {Id: ed727978-1ffe-4709-baee-73913e8e44a0} Unchanged
  Id: 'ed727978-1ffe-4709-baee-73913e8e44a0' PK FK
  Banner: <null>
  Blog: {Id: 1}
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many new fe...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
  Tags: []
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: [{Id: 1}, {Id: 3}]
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
  Tags: [{Id: 1}]
Post {Id: 3} Unchanged
  Id: 3 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: [{Id: 2}]
Post {Id: 4} Deleted
  Id: 4 PK
  BlogId: 2 FK
  Content: 'Examine when database queries were executed and measure how ...'
  Title: 'Database Profiling with Visual Studio'
  Blog: <null>
  Tags: [{Id: 2}]
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 1} Unchanged
  PostsId: 1 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 1, TagsId: 3} Unchanged
  PostsId: 1 PK FK
  TagsId: 3 PK FK
PostTag (Dictionary<string, object>) {PostsId: 2, TagsId: 1} Unchanged
  PostsId: 2 PK FK
  TagsId: 1 PK FK
PostTag (Dictionary<string, object>) {PostsId: 3, TagsId: 2} Unchanged
  PostsId: 3 PK FK
  TagsId: 2 PK FK
PostTag (Dictionary<string, object>) {PostsId: 4, TagsId: 2} Deleted
  PostsId: 4 PK FK
  TagsId: 2 PK FK
Tag {Id: 1} Unchanged
  Id: 1 PK
  Text: '.NET'
  Posts: [{Id: 1}, {Id: 2}]
Tag {Id: 2} Unchanged
  Id: 2 PK
  Text: 'Visual Studio'
  Posts: [{Id: 3}, {Id: 4}]
Tag {Id: 3} Unchanged
  Id: 3 PK
  Text: 'EF Core'
  Posts: [{Id: 1}]
```

<span data-ttu-id="ed8c4-136">追跡対象の各エンティティとその状態は、以前と同様に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-136">Each tracked entity and its state is shown as before.</span></span> <span data-ttu-id="ed8c4-137">ただし、長いビューにもプロパティ値とナビゲーション値が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-137">However, the long view also shows property and navigation values.</span></span>

#### <a name="property-values"></a><span data-ttu-id="ed8c4-138">プロパティ値</span><span class="sxs-lookup"><span data-stu-id="ed8c4-138">Property values</span></span>

<span data-ttu-id="ed8c4-139">長いビューでは、プロパティごとに、プロパティが主キー (PK)、代替キー (AK)、または外部キー (FK) の一部であるかどうかが示されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-139">For each property, the long view shows whether or not the property is part of a primary key (PK), alternate key (AK), or foreign key (FK).</span></span> <span data-ttu-id="ed8c4-140">例:</span><span class="sxs-lookup"><span data-stu-id="ed8c4-140">For example:</span></span>

- <span data-ttu-id="ed8c4-141">`Blog.Id` は主キープロパティです。 `Id: 1 PK`</span><span class="sxs-lookup"><span data-stu-id="ed8c4-141">`Blog.Id` is a primary key property: `Id: 1 PK`</span></span>
- <span data-ttu-id="ed8c4-142">`Blog.AssetsId` 代替のキープロパティは次のとおりです。 `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`</span><span class="sxs-lookup"><span data-stu-id="ed8c4-142">`Blog.AssetsId` is an alternate key property: `AssetsId: 'ed727978-1ffe-4709-baee-73913e8e44a0' AK`</span></span>
- <span data-ttu-id="ed8c4-143">`Post.BlogId` 外部キープロパティを次に示します。 `BlogId: 2 FK`</span><span class="sxs-lookup"><span data-stu-id="ed8c4-143">`Post.BlogId` is a foreign key property: `BlogId: 2 FK`</span></span>
- <span data-ttu-id="ed8c4-144">`BlogAssets.Id` は主キーと外部キーの両方のプロパティです。 `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`</span><span class="sxs-lookup"><span data-stu-id="ed8c4-144">`BlogAssets.Id` is both a primary key and a foreign key property: `Id: '3a54b880-2b9d-486b-9403-dc2e52d36d65' PK FK`</span></span>

<span data-ttu-id="ed8c4-145">変更されたプロパティ値はそのようにマークされ、プロパティの元の値も表示されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-145">Property values that have been modified are marked as such, and the original value of the property is also shown.</span></span> <span data-ttu-id="ed8c4-146">たとえば、「 `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-146">For example, `Name: '.NET Blog (All new!)' Modified Originally '.NET Blog'`.</span></span>

<span data-ttu-id="ed8c4-147">最後に、 `Added` 一時的なキー値を持つエンティティは、値が一時的であることを示します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-147">Finally, `Added` entities with temporary key values indicate that the value is temporary.</span></span> <span data-ttu-id="ed8c4-148">たとえば、「 `Id: -2147482643 PK Temporary` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-148">For example, `Id: -2147482643 PK Temporary`.</span></span>

#### <a name="navigation-values"></a><span data-ttu-id="ed8c4-149">ナビゲーション値</span><span class="sxs-lookup"><span data-stu-id="ed8c4-149">Navigation values</span></span>

<span data-ttu-id="ed8c4-150">ナビゲーション値は、ナビゲーションが参照するエンティティの主キー値を使用して表示されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-150">Navigation values are displayed using the primary key values of the entities that the navigations reference.</span></span> <span data-ttu-id="ed8c4-151">たとえば、上記の出力では、post 3 はブログ2に関連しています。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-151">For example, in the output above, post 3 is related to blog 2.</span></span> <span data-ttu-id="ed8c4-152">これは、 `Post.Blog` ナビゲーションが ID 2 のインスタンスを指すことを意味 `Blog` します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-152">This means that the `Post.Blog` navigation points to the `Blog` instance with ID 2.</span></span> <span data-ttu-id="ed8c4-153">これはとして表示され `Blog: {Id: 2}` ます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-153">This is shown as `Blog: {Id: 2}`.</span></span>

<span data-ttu-id="ed8c4-154">コレクションのナビゲーションでも同じことが行われます。ただし、この場合は、複数の関連エンティティが存在する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-154">The same thing happens for collection navigations, except that in this case there can be multiple related entities.</span></span> <span data-ttu-id="ed8c4-155">たとえば、コレクションのナビゲーションには、 `Blog.Posts` キー値1、2、および-2147482643 を持つ3つのエンティティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-155">For example, the collection navigation `Blog.Posts` contains three entities, with key values 1, 2, and -2147482643 respectively.</span></span> <span data-ttu-id="ed8c4-156">これはとして表示され `[{Id: 1}, {Id: 2}, {Id: -2147482643}]` ます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-156">This is shown as `[{Id: 1}, {Id: 2}, {Id: -2147482643}]`.</span></span>

## <a name="change-tracker-logging"></a><span data-ttu-id="ed8c4-157">トラッカーのログ記録の変更</span><span class="sxs-lookup"><span data-stu-id="ed8c4-157">Change tracker logging</span></span>

<span data-ttu-id="ed8c4-158">変更トラッカーは、 `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> [プロパティまたはナビゲーションの変更が検出](xref:core/change-tracking/debug-views)されるたびに、にメッセージを記録します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-158">The change tracker logs messages at the `Debug` <xref:Microsoft.Extensions.Logging.LogLevel> whenever it [detects property or navigation changes](xref:core/change-tracking/debug-views).</span></span> <span data-ttu-id="ed8c4-159">たとえば、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> このドキュメントの先頭にあるコードでが呼び出され、 [デバッグログが有効になっ](xref:core/logging-events-diagnostics/index)ている場合、次のログが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-159">For example, when <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> is called in the code at the top of this document and [debug logging is enabled](xref:core/logging-events-diagnostics/index), then the following logs are generated:</span></span>

```output
dbug: 12/30/2020 13:52:44.815 CoreEventId.DetectChangesStarting[10800] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges starting for 'BlogsContext'.
dbug: 12/30/2020 13:52:44.818 CoreEventId.PropertyChangeDetected[10802] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The unchanged property 'Blog.Name' was detected as changed from '.NET Blog' to '.NET Blog (All new!)' and will be marked as modified for entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.820 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Blog' entity with key '{Id: 1}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.821 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      1 entities were added and 0 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 1}'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.ValueGenerated[10808] (Microsoft.EntityFrameworkCore.ChangeTracking)
      'BlogsContext' generated temporary value '-2147482638' for the property 'Id.Post'.
dbug: 12/30/2020 13:52:44.822 CoreEventId.StartedTracking[10806] (Microsoft.EntityFrameworkCore.ChangeTracking)
      Context 'BlogsContext' started tracking 'Post' entity with key '{Id: -2147482638}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.827 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Modified'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CascadeDeleteOrphan[10003] (Microsoft.EntityFrameworkCore.Update)
      An entity of type 'Post' with key '{Id: 4}' changed to 'Deleted' state due to severed required relationship to its parent entity of type 'Blog'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'Post' entity with key '{Id: 4}' tracked by 'BlogsContext' changed state from 'Modified' to 'Deleted'.
dbug: 12/30/2020 13:52:44.829 CoreEventId.CollectionChangeDetected[10804] (Microsoft.EntityFrameworkCore.ChangeTracking)
      0 entities were added and 1 entities were removed from navigation 'Blog.Posts' on entity with key '{Id: 2}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.CascadeDelete[10002] (Microsoft.EntityFrameworkCore.Update)
      A cascade state change of an entity of type 'PostTag' with key '{PostsId: 4, TagsId: 2}' to 'Deleted' occurred due to the deletion of its parent entity of type 'Post' with key '{Id: 4}'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.StateChanged[10807] (Microsoft.EntityFrameworkCore.ChangeTracking)
      The 'PostTag' entity with key '{PostsId: 4, TagsId: 2}' tracked by 'BlogsContext' changed state from 'Unchanged' to 'Deleted'.
dbug: 12/30/2020 13:52:44.831 CoreEventId.DetectChangesCompleted[10801] (Microsoft.EntityFrameworkCore.ChangeTracking)
      DetectChanges completed for 'BlogsContext'.
```

<span data-ttu-id="ed8c4-160">次の表に、変更トラッカーのログメッセージの概要を示します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-160">The following table summaries the change tracker logging messages:</span></span>

| <span data-ttu-id="ed8c4-161">イベント ID</span><span class="sxs-lookup"><span data-stu-id="ed8c4-161">Event ID</span></span>                                                                                                               | <span data-ttu-id="ed8c4-162">説明</span><span class="sxs-lookup"><span data-stu-id="ed8c4-162">Description</span></span>
|:-----------------------------------------------------------------------------------------------------------------------|----
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesStarting?displayProperty=nameWithType>        | <span data-ttu-id="ed8c4-163"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> 開始しています</span><span class="sxs-lookup"><span data-stu-id="ed8c4-163"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> is starting</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.DetectChangesCompleted?displayProperty=nameWithType>       | <span data-ttu-id="ed8c4-164"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> 完了しました</span><span class="sxs-lookup"><span data-stu-id="ed8c4-164"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> has completed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.PropertyChangeDetected?displayProperty=nameWithType>       | <span data-ttu-id="ed8c4-165">通常のプロパティ値が変更されました</span><span class="sxs-lookup"><span data-stu-id="ed8c4-165">A normal property value has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ForeignKeyChangeDetected?displayProperty=nameWithType>     | <span data-ttu-id="ed8c4-166">外部キープロパティの値が変更されました</span><span class="sxs-lookup"><span data-stu-id="ed8c4-166">A foreign key property value has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.CollectionChangeDetected?displayProperty=nameWithType>     | <span data-ttu-id="ed8c4-167">スキップ以外のコレクションのナビゲーションで、関連エンティティが追加または削除されました。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-167">A non-skip collection navigation has had related entities added or removed.</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ReferenceChangeDetected?displayProperty=nameWithType>      | <span data-ttu-id="ed8c4-168">参照ナビゲーションが別のエンティティを指すように変更されているか、null に設定されています。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-168">A reference navigation has been changed to point to another entity, or set to null</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StartedTracking?displayProperty=nameWithType>              | <span data-ttu-id="ed8c4-169">EF Core エンティティの追跡を開始しました</span><span class="sxs-lookup"><span data-stu-id="ed8c4-169">EF Core started tracking an entity</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.StateChanged?displayProperty=nameWithType>                 | <span data-ttu-id="ed8c4-170"><xref:Microsoft.EntityFrameworkCore.EntityState>エンティティのが変更されました</span><span class="sxs-lookup"><span data-stu-id="ed8c4-170">The <xref:Microsoft.EntityFrameworkCore.EntityState> of an entity has changed</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.ValueGenerated?displayProperty=nameWithType>               | <span data-ttu-id="ed8c4-171">プロパティに対して値が生成されました</span><span class="sxs-lookup"><span data-stu-id="ed8c4-171">A value was generated for a property</span></span>
| <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId.SkipCollectionChangeDetected?displayProperty=nameWithType> | <span data-ttu-id="ed8c4-172">スキップコレクションのナビゲーションに関連エンティティが追加または削除されました</span><span class="sxs-lookup"><span data-stu-id="ed8c4-172">A skip collection navigation has had related entities added or removed</span></span>

## <a name="the-model"></a><span data-ttu-id="ed8c4-173">Model</span><span class="sxs-lookup"><span data-stu-id="ed8c4-173">The model</span></span>

<span data-ttu-id="ed8c4-174">上記の例で使用するモデルには、次のエンティティ型が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-174">The model used for the examples above contains the following entity types:</span></span>

<!--
public class Blog
{
    public int Id { get; set; } // Primary key
    public Guid AssetsId { get; set; } // Alternate key
    public string Name { get; set; }

    public IList<Post> Posts { get; } = new List<Post>(); // Collection navigation
    public BlogAssets Assets { get; set; } // Reference navigation
}

public class BlogAssets
{
    public Guid Id { get; set; } // Primary key and foreign key
    public byte[] Banner { get; set; }

    public Blog Blog { get; set; } // Reference navigation
}

public class Post
{
    public int Id { get; set; } // Primary key
    public string Title { get; set; }
    public string Content { get; set; }

    public int BlogId { get; set; } // Foreign key
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
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=Model)]

<span data-ttu-id="ed8c4-175">モデルは、主に規則によって構成されます。 OnModelCreating 数行だけを作成します。</span><span class="sxs-lookup"><span data-stu-id="ed8c4-175">The model is mostly configured by convention, with just a few lines in OnModelCreating:</span></span>

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<Blog>()
            .Property(e => e.AssetsId)
            .ValueGeneratedOnAdd();

        modelBuilder
            .Entity<BlogAssets>()
            .HasOne(e => e.Blog)
            .WithOne(e => e.Assets)
            .HasForeignKey<BlogAssets>(e => e.Id)
            .HasPrincipalKey<Blog>(e => e.AssetsId);
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeTrackerDebugging/Samples.cs?name=OnModelCreating)]
