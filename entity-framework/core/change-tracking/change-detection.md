---
title: 変更の検出と通知-EF Core
description: 検出された変更または通知を使用したプロパティとリレーションシップの変更の検出
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/change-detection
ms.openlocfilehash: 39dc66a3ba74be89d3e470cfe788a357401965d1
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129612"
---
# <a name="change-detection-and-notifications"></a><span data-ttu-id="e1bf6-103">変更の検出と通知</span><span class="sxs-lookup"><span data-stu-id="e1bf6-103">Change Detection and Notifications</span></span>

<span data-ttu-id="e1bf6-104">各 <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスは、エンティティに加えられた変更を追跡します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="e1bf6-105">これらの追跡対象エンティティは、が呼び出されると、データベースへの変更を <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 行います。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="e1bf6-106">これについては、 [EF Core の Change Tracking](xref:core/change-tracking/index)に記載されています。このドキュメントでは、エンティティの状態と Entity Framework Core (EF Core) の変更の追跡の基礎を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-106">This is covered in [Change Tracking in EF Core](xref:core/change-tracking/index), and this document assumes that entity states and the basics of Entity Framework Core (EF Core) change tracking are understood.</span></span>

<span data-ttu-id="e1bf6-107">プロパティとリレーションシップの変更を追跡するには、DbContext でこれらの変更を検出できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-107">Tracking property and relationship changes requires that the DbContext is able to detect these changes.</span></span> <span data-ttu-id="e1bf6-108">このドキュメントでは、この検出がどのように行われるかについて説明します。また、プロパティ通知または変更追跡プロキシを使用して、変更を直ちに検出する方法についても説明します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-108">This document covers how this detection happens, as well as how to use property notifications or change-tracking proxies to force immediate detection of changes.</span></span>

> [!TIP]
> <span data-ttu-id="e1bf6-109">[GitHub からサンプルコードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeDetectionAndNotifications)することで、このドキュメントのすべてのコードを実行し、デバッグすることができます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-109">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeDetectionAndNotifications).</span></span>

## <a name="snapshot-change-tracking"></a><span data-ttu-id="e1bf6-110">スナップショットによる変更の追跡</span><span class="sxs-lookup"><span data-stu-id="e1bf6-110">Snapshot change tracking</span></span>

<span data-ttu-id="e1bf6-111">既定では、EF Core は、DbContext インスタンスによって最初に追跡されるときに、すべてのエンティティのプロパティ値のスナップショットを作成します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-111">By default, EF Core creates a snapshot of every entity's property values when it is first tracked by a DbContext instance.</span></span> <span data-ttu-id="e1bf6-112">このスナップショットに格納されている値は、変更されたプロパティ値を特定するために、エンティティの現在の値と比較されます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-112">The values stored in this snapshot are then compared against the current values of the entity in order to determine which property values have changed.</span></span>

<span data-ttu-id="e1bf6-113">この変更の検出は、データベースに更新を送信する前に、変更されたすべての値が検出されるように SaveChanges が呼び出されたときに発生します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-113">This detection of changes happens when SaveChanges is called to ensure all changed values are detected before sending updates to the database.</span></span> <span data-ttu-id="e1bf6-114">ただし、変更の検出も、アプリケーションが最新の追跡情報を操作していることを確認するために、他のタイミングでも行われます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-114">However, the detection of changes also happens at other times to ensure the application is working with up-to-date tracking information.</span></span> <span data-ttu-id="e1bf6-115">変更の検出は、を呼び出すことによっていつでも強制でき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-115">Detection of changes can be forced at any time by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>.</span></span>

### <a name="when-change-detection-is-needed"></a><span data-ttu-id="e1bf6-116">変更の検出が必要な場合</span><span class="sxs-lookup"><span data-stu-id="e1bf6-116">When change detection is needed</span></span>

<span data-ttu-id="e1bf6-117">_この変更を行うために EF Core を使用せず_ にプロパティまたはナビゲーションを変更した場合は、変更の検出が必要です。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-117">Detection of changes is needed when a property or navigation has been changed _without using EF Core to make this change_.</span></span> <span data-ttu-id="e1bf6-118">たとえば、ブログや投稿を読み込んで、次のようなエンティティを変更することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-118">For example, consider loading blogs and posts and then making changes to these entities:</span></span>

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        blog.Name = ".NET Blog (Updated!)";

        // Add a new entity to a navigation
        blog.Posts.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many..."
        });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_1)]

<span data-ttu-id="e1bf6-119">を呼び出す前に [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> 表示すると、変更が検出されていないため、エンティティの状態および変更されたプロパティデータに反映されません。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-119">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> shows that the changes made have not been detected and hence are not reflected in the entity states and modified property data:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, <not found>]
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

<span data-ttu-id="e1bf6-120">具体的には、ブログエントリの状態は依然として、 `Unchanged` 新しい投稿は追跡対象エンティティとして表示されません。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-120">Specifically, the state of the blog entry is still `Unchanged`, and the new post does not appear as a tracked entity.</span></span> <span data-ttu-id="e1bf6-121">(EF Core によってこれらの変更がまだ検出されていない場合でも、ずる賢いはプロパティに新しい値を報告します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-121">(The astute will notice properties report their new values, even though these changes have not yet been detected by EF Core.</span></span> <span data-ttu-id="e1bf6-122">これは、デバッグビューがエンティティインスタンスから現在の値を直接読み取るためです。)</span><span class="sxs-lookup"><span data-stu-id="e1bf6-122">This is because the debug view is reading current values directly from the entity instance.)</span></span>

<span data-ttu-id="e1bf6-123">検出された変更を呼び出した後のデバッグビューと比較します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-123">Contrast this with the debug view after calling DetectChanges:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
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
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

<span data-ttu-id="e1bf6-124">これで、ブログはとして正しくマークされ、 `Modified` 新しい投稿が検出され、として追跡されるようになりました `Added` 。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-124">Now the blog is correctly marked as `Modified` and the new post has been detected and is tracked as `Added`.</span></span>

<span data-ttu-id="e1bf6-125">このセクションの冒頭では、EF Core を使用して _変更を行う_ のではなく、変更の検出が必要であることを説明しています。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-125">At the start of this section we stated that detecting changes is needed when not using _using EF Core to make the change_.</span></span> <span data-ttu-id="e1bf6-126">これは、上記のコードで発生しています。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-126">This is what is happening in the code above.</span></span> <span data-ttu-id="e1bf6-127">つまり、プロパティおよびナビゲーションへの変更は、EF Core メソッドを使用するのではなく、 _エンティティインスタンスに対して直接_ 行われます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-127">That is, the changes to the property and navigation are made _directly on the entity instances_, and not by using any EF Core methods.</span></span>

<span data-ttu-id="e1bf6-128">これを次のコードと比較して、同じようにエンティティを変更しますが、今回は EF Core メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-128">Contrast this to the following code which modifies the entities in the same way, but this time using EF Core methods:</span></span>

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        context.Entry(blog).Property(e => e.Name).CurrentValue = ".NET Blog (Updated!)";

        // Add a new entity to the DbContext
        context.Add(
            new Post
            {
                Blog = blog,
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_2)]

<span data-ttu-id="e1bf6-129">この場合、変更トラッカーデバッグビューは、変更の検出が行われていなくても、すべてのエンティティの状態とプロパティの変更が判明していることを示しています。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-129">In this case the change tracker debug view shows that all entity states and property modifications are known, even though detection of changes has not happened.</span></span> <span data-ttu-id="e1bf6-130">これは、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> が EF Core メソッドであるためです。これは EF Core が、このメソッドによって行われた変更をすぐに認識できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-130">This is because <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> is an EF Core method, which means that EF Core immediately knows about the change made by this method.</span></span> <span data-ttu-id="e1bf6-131">同様に、を呼び出すことで、 <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> 新しいエンティティに関する情報をすぐに把握し、適切に追跡 EF Core ことができます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-131">Likewise, calling <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> allows EF Core to immediately know about the new entity and track it appropriately.</span></span>

> [!TIP]
> <span data-ttu-id="e1bf6-132">エンティティの変更を行うために常に EF Core メソッドを使用して、変更を検出しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-132">Don't attempt to avoid detecting changes by always using EF Core methods to make entity changes.</span></span> <span data-ttu-id="e1bf6-133">多くの場合、これを行うと、通常の方法でエンティティを変更するよりも煩雑になり、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-133">Doing so is often more cumbersome and performs less well than making changes to entities in the normal way.</span></span> <span data-ttu-id="e1bf6-134">このドキュメントでは、変更が必要であることを検出するタイミングと、そうでない場合について説明します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-134">The intention of this document is to inform as to when detecting changes is needed and when it is not.</span></span> <span data-ttu-id="e1bf6-135">その目的は、変更の検出を回避することではありません。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-135">The intention is not to encourage avoidance of change detection.</span></span>

### <a name="methods-that-automatically-detect-changes"></a><span data-ttu-id="e1bf6-136">変更を自動的に検出するメソッド</span><span class="sxs-lookup"><span data-stu-id="e1bf6-136">Methods that automatically detect changes</span></span>

<span data-ttu-id="e1bf6-137"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> は、メソッドによって自動的に呼び出され、結果に影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-137"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> is called automatically by methods where doing so is likely to impact the results.</span></span> <span data-ttu-id="e1bf6-138">それらの方法を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-138">These methods are:</span></span>

- <span data-ttu-id="e1bf6-139"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> また <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType> 、データベースを更新する前にすべての変更が検出されるようにします。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-139"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType>, to ensure that all changes are detected before updating the database.</span></span>
- <span data-ttu-id="e1bf6-140"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType>エンティティの状態および変更されたプロパティが最新であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-140"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType>, to ensure entity states and modified properties are up-to-date.</span></span>
- <span data-ttu-id="e1bf6-141"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>。結果が正確であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-141"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, to ensure that the result is accurate.</span></span>
- <span data-ttu-id="e1bf6-142"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>。連鎖する前にプリンシパル/親エンティティのエンティティの状態を正しく保つために使用します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-142"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, to ensure correct entity states for principal/parent entities before cascading.</span></span>
- <span data-ttu-id="e1bf6-143"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>。追跡対象のグラフが最新であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-143"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>, to ensure that the tracked graph is up-to-date.</span></span>

<span data-ttu-id="e1bf6-144">また、追跡されるエンティティのグラフ全体ではなく、1つのエンティティインスタンスでのみ変更の検出が行われる場所もあります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-144">There are also some places where detection of changes happens on only a single entity instance, rather than on the entire graph of tracked entities.</span></span> <span data-ttu-id="e1bf6-145">次のような場所があります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-145">These places are:</span></span>

- <span data-ttu-id="e1bf6-146">を使用する場合 <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> 、エンティティの状態と変更されたプロパティが最新であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-146">When using <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>, to ensure that the entity's state and modified properties are up-to-date.</span></span>
- <span data-ttu-id="e1bf6-147">、、またはなどのメソッドを使用して、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> `Property` プロパティの `Collection` `Reference` `Member` 変更、現在の値などを確実に最新の状態にする場合。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-147">When using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> methods such as `Property`, `Collection`, `Reference` or `Member` to ensure property modifications, current values, etc. are up-to-date.</span></span>
- <span data-ttu-id="e1bf6-148">必要なリレーションシップが切断されているために依存/子エンティティを削除する場合。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-148">When a dependent/child entity is going to be deleted because a required relationship has been severed.</span></span> <span data-ttu-id="e1bf6-149">これは、エンティティが再親されたために削除されないことを検出します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-149">This detects when an entity should not be deleted because it has been re-parented.</span></span>

<span data-ttu-id="e1bf6-150">を呼び出すことにより、1つのエンティティに対する変更のローカル検出を明示的にトリガーでき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-150">Local detection of changes for a single entity can be triggered explicitly by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType>.</span></span>

> [!NOTE]
> <span data-ttu-id="e1bf6-151">ローカル検出の変更によって、完全な検出によって検出されるいくつかの変更が失われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-151">Local detect changes can miss some changes that a full detection would find.</span></span> <span data-ttu-id="e1bf6-152">これは、他のエンティティに対する検出されていない変更によって発生する連鎖アクションが、対象のエンティティに影響を与える場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-152">This happens when cascading actions resulting from undetected changes to other entities have an impact on the entity in question.</span></span> <span data-ttu-id="e1bf6-153">このような場合、アプリケーションは、明示的にを呼び出すことによって、すべてのエンティティのフルスキャンを強制的に実行する必要があり <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-153">In such situations the application may need to force a full scan of all entities by explicitly calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType>.</span></span>

### <a name="disabling-automatic-change-detection"></a><span data-ttu-id="e1bf6-154">自動変更検出の無効化</span><span class="sxs-lookup"><span data-stu-id="e1bf6-154">Disabling automatic change detection</span></span>

<span data-ttu-id="e1bf6-155">ほとんどのアプリケーションでは、変更の検出のパフォーマンスがボトルネックになることはありません。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-155">The performance of detecting changes is not a bottleneck for most applications.</span></span> <span data-ttu-id="e1bf6-156">ただし、変更を検出すると、何千ものエンティティを追跡する一部のアプリケーションでパフォーマンスの問題になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-156">However, detecting changes can become a performance problem for some applications that track thousands of entities.</span></span> <span data-ttu-id="e1bf6-157">(正確な数値は、エンティティ内のプロパティの数など、多くの点に依存しています)。このため、を使用して、変更の自動検出を無効にすることができ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-157">(The exact number will dependent on many things, such as the number of properties in the entity.) For this reason the automatic detection of changes can be disabled using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e1bf6-158">たとえば、ペイロードとの多対多リレーションシップで結合エンティティを処理することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-158">For example, consider processing join entities in a many-to-many relationship with payloads:</span></span>

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>()) // Detects changes automatically
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                    entityEntry.Entity.TaggedOn = DateTime.Now;
                }
            }

            try
            {
                ChangeTracker.AutoDetectChangesEnabled = false;
                return base.SaveChanges(); // Avoid automatically detecting changes again here
            }
            finally
            {
                ChangeTracker.AutoDetectChangesEnabled = true;
            }
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=SaveChanges)]

<span data-ttu-id="e1bf6-159">前のセクションで説明したように、との両方で <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> 自動的に変更が検出されます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-159">As we know from the previous section, both <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> automatically detect changes.</span></span> <span data-ttu-id="e1bf6-160">ただし、エントリを呼び出した後、コードでエンティティまたはプロパティの状態が変更されることはありません。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-160">However, after calling Entries, the code does not then make any entity or property state changes.</span></span> <span data-ttu-id="e1bf6-161">(追加されたエンティティに通常のプロパティ値を設定しても、状態の変更は発生しません)。したがって、このコードでは、ベースの SaveChanges メソッドを呼び出すときに、不要な自動変更検出が無効になります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-161">(Setting normal property values on Added entities does not cause any state changes.) The code therefore disables unnecessary automatic change detection when calling down into the base SaveChanges method.</span></span> <span data-ttu-id="e1bf6-162">また、このコードでは、SaveChanges が失敗した場合でも、既定の設定が復元されるように、try/finally ブロックを使用します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-162">The code also makes use of a try/finally block to ensure that the default setting is restored even if SaveChanges fails.</span></span>

> [!TIP]
> <span data-ttu-id="e1bf6-163">コードで自動変更検出を無効にして適切に実行する必要があると想定しないでください。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-163">Do not assume that your code must disable automatic change detection to to perform well.</span></span> <span data-ttu-id="e1bf6-164">これは、アプリケーションをプロファイリングする場合にのみ必要です。多くのエンティティを追跡することは、変更検出のパフォーマンスが問題であることを示します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-164">This is only needed when profiling an application tracking many entities indicates that performance of change detection is an issue.</span></span>

### <a name="detecting-changes-and-value-conversions"></a><span data-ttu-id="e1bf6-165">変更と値の変換の検出</span><span class="sxs-lookup"><span data-stu-id="e1bf6-165">Detecting changes and value conversions</span></span>

<span data-ttu-id="e1bf6-166">エンティティ型のスナップショット変更の追跡を使用するには、EF Core 次のことが可能である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-166">To use snapshot change tracking with an entity type, EF Core must be able to:</span></span>

- <span data-ttu-id="e1bf6-167">エンティティが追跡されるときに各プロパティ値のスナップショットを作成する</span><span class="sxs-lookup"><span data-stu-id="e1bf6-167">Make a snapshot of each property value when the entity is tracked</span></span>
- <span data-ttu-id="e1bf6-168">この値をプロパティの現在の値と比較します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-168">Compare this value to the current value of the property</span></span>
- <span data-ttu-id="e1bf6-169">値のハッシュコードを生成します</span><span class="sxs-lookup"><span data-stu-id="e1bf6-169">Generate a hash code for the value</span></span>

<span data-ttu-id="e1bf6-170">これは、データベースに直接マップできる型の EF Core によって自動的に処理されます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-170">This is handled automatically by EF Core for types that can be directly mapped to the database.</span></span> <span data-ttu-id="e1bf6-171">ただし、 [値コンバーターを使用](xref:core/modeling/value-conversions)してプロパティをマップする場合、そのコンバーターは、これらのアクションの実行方法を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-171">However, when a [value converter is used to map a property](xref:core/modeling/value-conversions), then that converter must specify how to perform these actions.</span></span> <span data-ttu-id="e1bf6-172">これは、値の比較子を使用して実現されます。詳細については、 [値](xref:core/modeling/value-comparers) の比較子のドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-172">This is achieved with a value comparer, and is described in detail in the [Value Comparers](xref:core/modeling/value-comparers) documentation.</span></span>

## <a name="notification-entities"></a><span data-ttu-id="e1bf6-173">通知エンティティ</span><span class="sxs-lookup"><span data-stu-id="e1bf6-173">Notification entities</span></span>

<span data-ttu-id="e1bf6-174">ほとんどのアプリケーションでは、スナップショットの変更の追跡をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-174">Snapshot change tracking is recommended for most applications.</span></span> <span data-ttu-id="e1bf6-175">ただし、多数のエンティティを追跡するアプリケーションや、それらのエンティティに多くの変更を加えるアプリケーションでは、プロパティとナビゲーション値が変更されたときに EF Core に自動的に通知するエンティティを実装する方がメリットがあります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-175">However, applications that track many entities and/or make many changes to those entities may benefit from implementing entities that automatically notify EF Core when their property and navigation values change.</span></span> <span data-ttu-id="e1bf6-176">これらは "通知エンティティ" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-176">These are known as "notification entities".</span></span>

### <a name="implementing-notification-entities"></a><span data-ttu-id="e1bf6-177">通知エンティティの実装</span><span class="sxs-lookup"><span data-stu-id="e1bf6-177">Implementing notification entities</span></span>

<span data-ttu-id="e1bf6-178">通知エンティティ <xref:System.ComponentModel.INotifyPropertyChanging> <xref:System.ComponentModel.INotifyPropertyChanged> は、.net 基本クラスライブラリ (BCL) の一部であるおよびインターフェイスを使用します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-178">Notification entities make use of the <xref:System.ComponentModel.INotifyPropertyChanging> and <xref:System.ComponentModel.INotifyPropertyChanged> interfaces, which are part of the .NET base class library (BCL).</span></span> <span data-ttu-id="e1bf6-179">これらのインターフェイスは、プロパティ値の変更前後に発生する必要があるイベントを定義します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-179">These interfaces define events that must be fired before and after changing a property value.</span></span> <span data-ttu-id="e1bf6-180">例:</span><span class="sxs-lookup"><span data-stu-id="e1bf6-180">For example:</span></span>

<!--
    public class Blog : INotifyPropertyChanging, INotifyPropertyChanged
    {
        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private int _id;
        public int Id
        {
            get => _id;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Id)));
                _id = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Id)));
            }
        }

        private string _name;
        public string Name
        {
            get => _name;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Name)));
                _name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
            }
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationEntitiesSamples.cs?name=Model)]

<span data-ttu-id="e1bf6-181">また、コレクションのナビゲーションでは、を実装する必要があります。 `INotifyCollectionChanged` 上記の例では、投稿のを使用してこれを満たしています。 <xref:System.Collections.ObjectModel.ObservableCollection%601></span><span class="sxs-lookup"><span data-stu-id="e1bf6-181">In addition, any collection navigations must implement `INotifyCollectionChanged`; in the example above this satisfied by using an <xref:System.Collections.ObjectModel.ObservableCollection%601> of posts.</span></span> <span data-ttu-id="e1bf6-182">また EF Core には、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> 安定した順序でより効率的な参照を持つ実装が付属しています。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-182">EF Core also ships with an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> implementation that has more efficient lookups at the expense of stable ordering.</span></span>

<span data-ttu-id="e1bf6-183">通常、この通知コードのほとんどは、マップされていない基本クラスに移動されます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-183">Most of this notification code is typically moved into an unmapped base class.</span></span> <span data-ttu-id="e1bf6-184">例:</span><span class="sxs-lookup"><span data-stu-id="e1bf6-184">For example:</span></span>

<!--
    public class Blog : NotifyingEntity
    {
        private int _id;
        public int Id
        {
            get => _id;
            set => SetWithNotify(value, out _id);
        }

        private string _name;
        public string Name
        {
            get => _name;
            set => SetWithNotify(value, out _name);
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public abstract class NotifyingEntity : INotifyPropertyChanging, INotifyPropertyChanged
    {
        protected void SetWithNotify<T>(T value, out T field, [CallerMemberName] string propertyName = "")
        {
            NotifyChanging(propertyName);
            field = value;
            NotifyChanged(propertyName);
        }

        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private void NotifyChanged(string propertyName)
            => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

        private void NotifyChanging(string propertyName)
            => PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(propertyName));
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Model)]

### <a name="configuring-notification-entities"></a><span data-ttu-id="e1bf6-185">通知エンティティの構成</span><span class="sxs-lookup"><span data-stu-id="e1bf6-185">Configuring notification entities</span></span>

<span data-ttu-id="e1bf6-186">`INotifyPropertyChanging` `INotifyPropertyChanged` EF Core で使用するために完全に実装されているかどうかを検証 EF Core する方法はありません。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-186">There is no way for EF Core to validate that `INotifyPropertyChanging` or `INotifyPropertyChanged` are fully implemented for use with EF Core.</span></span> <span data-ttu-id="e1bf6-187">特に、これらのインターフェイスの一部の用途では、EF Core で必要とされるすべてのプロパティ (ナビゲーションを含む) ではなく、特定のプロパティに対してのみ通知を行います。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-187">In particular, some uses of these interfaces do so with notifications only on certain properties, rather than on all properties (including navigations) as required by EF Core.</span></span> <span data-ttu-id="e1bf6-188">このため、EF Core はこれらのイベントに自動的にフックしません。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-188">For this reason, EF Core does not automatically hook into these events.</span></span>

<span data-ttu-id="e1bf6-189">代わりに、これらの通知エンティティを使用するように EF Core を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-189">Instead, EF Core must be configured to use these notification entities.</span></span> <span data-ttu-id="e1bf6-190">通常、これは、を呼び出すことによって、すべてのエンティティ型に対して行われ <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-190">This is usually done for all entity types by calling <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e1bf6-191">例:</span><span class="sxs-lookup"><span data-stu-id="e1bf6-191">For example:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.HasChangeTrackingStrategy(ChangeTrackingStrategy.ChangingAndChangedNotifications);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=OnModelCreating)]

<span data-ttu-id="e1bf6-192">(を使用して、さまざまなエンティティ型に対して方法を異なる方法で設定することもでき <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> ますが、これは通常、通知エンティティではない型に対しても検出の変更が必要になるためです)。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-192">(The strategy can also be set differently for different entity types using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType>, but this is usually counterproductive since DetectChanges is still required for those types that are not notification entities.)</span></span>

<span data-ttu-id="e1bf6-193">完全な通知変更の追跡を行うに `INotifyPropertyChanging` は、との両方が実装されている必要があり `INotifyPropertyChanged` ます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-193">Full notification change tracking requires that both `INotifyPropertyChanging` and `INotifyPropertyChanged` are implemented.</span></span> <span data-ttu-id="e1bf6-194">これにより、プロパティ値が変更される直前に元の値を保存できるため、エンティティを追跡するときにスナップショットを作成する EF Core 必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-194">This allows original values to be saved just before the property value is changed, avoiding the need for EF Core to create a snapshot when tracking the entity.</span></span> <span data-ttu-id="e1bf6-195">を実装するエンティティ型は、 `INotifyPropertyChanged` EF Core と共に使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-195">Entity types that implement only `INotifyPropertyChanged` can also be used with EF Core.</span></span> <span data-ttu-id="e1bf6-196">この場合、EF では、元の値を追跡するためにエンティティを追跡するときにスナップショットが作成されますが、その後、通知を使用して変更をすぐに検出し、検出された変更を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-196">In this case, EF still creates a snapshot when tracking an entity to keep track of original values, but then uses the notifications to detect changes immediately, rather than needing DetectChanges to be called.</span></span>

<span data-ttu-id="e1bf6-197">次の <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> 表に、異なる値をまとめて示します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-197">The different <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> values are summarized in the the following table.</span></span>

| <span data-ttu-id="e1bf6-198">ChangeTrackingStrategy</span><span class="sxs-lookup"><span data-stu-id="e1bf6-198">ChangeTrackingStrategy</span></span>                              | <span data-ttu-id="e1bf6-199">必要なインターフェイス</span><span class="sxs-lookup"><span data-stu-id="e1bf6-199">Interfaces needed</span></span>                                      | <span data-ttu-id="e1bf6-200">検出の変更が必要</span><span class="sxs-lookup"><span data-stu-id="e1bf6-200">Needs DetectChanges</span></span> | <span data-ttu-id="e1bf6-201">スナップショットの元の値</span><span class="sxs-lookup"><span data-stu-id="e1bf6-201">Snapshots original values</span></span>
|:----------------------------------------------------|--------------------------------------------------------|---------------------|--------------------------
| <span data-ttu-id="e1bf6-202">スナップショット</span><span class="sxs-lookup"><span data-stu-id="e1bf6-202">Snapshot</span></span>                                            | <span data-ttu-id="e1bf6-203">None</span><span class="sxs-lookup"><span data-stu-id="e1bf6-203">None</span></span>                                                   | <span data-ttu-id="e1bf6-204">はい</span><span class="sxs-lookup"><span data-stu-id="e1bf6-204">Yes</span></span>                 | <span data-ttu-id="e1bf6-205">はい</span><span class="sxs-lookup"><span data-stu-id="e1bf6-205">Yes</span></span>
| <span data-ttu-id="e1bf6-206">警告の通知</span><span class="sxs-lookup"><span data-stu-id="e1bf6-206">ChangedNotifications</span></span>                                | <span data-ttu-id="e1bf6-207">INotifyPropertyChanged</span><span class="sxs-lookup"><span data-stu-id="e1bf6-207">INotifyPropertyChanged</span></span>                                 | <span data-ttu-id="e1bf6-208">いいえ</span><span class="sxs-lookup"><span data-stu-id="e1bf6-208">No</span></span>                  | <span data-ttu-id="e1bf6-209">はい</span><span class="sxs-lookup"><span data-stu-id="e1bf6-209">Yes</span></span>
| <span data-ttu-id="e1bf6-210">すべての通知の方法</span><span class="sxs-lookup"><span data-stu-id="e1bf6-210">ChangingAndChangedNotifications</span></span>                     | <span data-ttu-id="e1bf6-211">INotifyPropertyChanged と INotifyPropertyChanging</span><span class="sxs-lookup"><span data-stu-id="e1bf6-211">INotifyPropertyChanged and INotifyPropertyChanging</span></span>     | <span data-ttu-id="e1bf6-212">いいえ</span><span class="sxs-lookup"><span data-stu-id="e1bf6-212">No</span></span>                  | <span data-ttu-id="e1bf6-213">いいえ</span><span class="sxs-lookup"><span data-stu-id="e1bf6-213">No</span></span>
| <span data-ttu-id="e1bf6-214">ChangingAndChangedNotificationsWithOriginalValues</span><span class="sxs-lookup"><span data-stu-id="e1bf6-214">ChangingAndChangedNotificationsWithOriginalValues</span></span>   | <span data-ttu-id="e1bf6-215">INotifyPropertyChanged と INotifyPropertyChanging</span><span class="sxs-lookup"><span data-stu-id="e1bf6-215">INotifyPropertyChanged and INotifyPropertyChanging</span></span>     | <span data-ttu-id="e1bf6-216">いいえ</span><span class="sxs-lookup"><span data-stu-id="e1bf6-216">No</span></span>                  | <span data-ttu-id="e1bf6-217">はい</span><span class="sxs-lookup"><span data-stu-id="e1bf6-217">Yes</span></span>

### <a name="using-notification-entities"></a><span data-ttu-id="e1bf6-218">通知エンティティの使用</span><span class="sxs-lookup"><span data-stu-id="e1bf6-218">Using notification entities</span></span>

<span data-ttu-id="e1bf6-219">通知エンティティは他のエンティティと同様に動作しますが、エンティティインスタンスを変更するときに、これらの変更を検出するためにを呼び出す必要がない点が異なり <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-219">Notification entities behave like any other entities, except that making changes to the entity instances do not require a call to <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> to detect these changes.</span></span> <span data-ttu-id="e1bf6-220">例:</span><span class="sxs-lookup"><span data-stu-id="e1bf6-220">For example:</span></span>

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Notification_entities_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Notification_entities_2)]

<span data-ttu-id="e1bf6-221">通常のエンティティの場合、 [変更トラッカーデバッグビュー](xref:core/change-tracking/debug-views) では、検出された変更が呼び出されるまでこれらの変更が検出されなかったことが示されていました。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-221">With normal entities, the [change tracker debug view](xref:core/change-tracking/debug-views) showed that these changes were not detected until DetectChanges was called.</span></span> <span data-ttu-id="e1bf6-222">通知エンティティが使用されているときにデバッグビューを見ると、これらの変更がすぐに検出されていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-222">Looking at the debug view when notification entities are used shows that these changes have been detected immediately:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
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
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

## <a name="change-tracking-proxies"></a><span data-ttu-id="e1bf6-223">変更追跡のプロキシ</span><span class="sxs-lookup"><span data-stu-id="e1bf6-223">Change-tracking proxies</span></span>

> [!NOTE]
> <span data-ttu-id="e1bf6-224">変更追跡プロキシは EF Core 5.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-224">Change-tracking proxies were introduced in EF Core 5.0.</span></span>

<span data-ttu-id="e1bf6-225">EF Core は、およびを実装するプロキシ型を動的に生成でき <xref:System.ComponentModel.INotifyPropertyChanging> <xref:System.ComponentModel.INotifyPropertyChanged> ます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-225">EF Core can dynamically generate proxy types that implement <xref:System.ComponentModel.INotifyPropertyChanging> and <xref:System.ComponentModel.INotifyPropertyChanged>.</span></span> <span data-ttu-id="e1bf6-226">これには、次の例のように、 [Microsoft の EntityFrameworkCore プロキシ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)NuGet パッケージをインストールし、変更追跡プロキシを有効にする必要があります。 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A></span><span class="sxs-lookup"><span data-stu-id="e1bf6-226">This requires installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet package, and enabling change-tracking proxies with <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.UseChangeTrackingProxies();
-->
[!code-csharp[OnConfiguring](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=OnConfiguring)]

<span data-ttu-id="e1bf6-227">動的プロキシを作成するには、エンティティ型から継承する新しい動的な .NET 型を作成し、その後、すべてのプロパティ setter をオーバーライドし[ます。](https://www.nuget.org/packages/Castle.Core/)</span><span class="sxs-lookup"><span data-stu-id="e1bf6-227">Creating a dynamic proxy involves creating a new, dynamic .NET type (using the [Castle.Core](https://www.nuget.org/packages/Castle.Core/) proxies implementation), which inherits from the entity type and then overrides all property setters.</span></span> <span data-ttu-id="e1bf6-228">したがって、プロキシのエンティティ型は、から継承できる型である必要があり、オーバーライドできるプロパティを持つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-228">Entity types for proxies must therefore be types that can be inherited from and must have properties that can be overridden.</span></span> <span data-ttu-id="e1bf6-229">また、明示的に作成されたコレクションのナビゲーションは、次のように実装する必要があり <xref:System.Collections.Specialized.INotifyCollectionChanged> ます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-229">Also, collection navigations created explicitly must implement <xref:System.Collections.Specialized.INotifyCollectionChanged> For example:</span></span>

<!--
    public class Blog
    {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }

        public virtual IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public class Post
    {
        public virtual int Id { get; set; }
        public virtual string Title { get; set; }
        public virtual string Content { get; set; }

        public virtual int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Model)]

<span data-ttu-id="e1bf6-230">変更追跡プロキシの重要な欠点の1つは、EF Core は常に、基になるエンティティ型のインスタンスではなく、プロキシのインスタンスを追跡する必要があることです。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-230">One significant downside to change-tracking proxies is that EF Core must always track instances of the proxy, never instances of the underlying entity type.</span></span> <span data-ttu-id="e1bf6-231">これは、基になるエンティティ型のインスタンスが通知を生成しないためです。つまり、これらのエンティティに対して行われた変更が失われることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-231">This is because instances of the underlying entity type will not generate notifications, which means changes made to these entities will be missed.</span></span>

<span data-ttu-id="e1bf6-232">EF Core は、データベースに対してクエリを実行するときにプロキシインスタンスを自動的に作成するため、この欠点は、通常、新しいエンティティインスタンスの追跡に限定されます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-232">EF Core creates proxy instances automatically when querying the database, so this downside is generally limited to tracking new entity instances.</span></span> <span data-ttu-id="e1bf6-233">これらのインスタンスは、 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> を使用する通常の方法では **なく** 、拡張メソッドを使用して作成する必要があり `new` ます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-233">These instances must be created using the <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> extension methods, and **not** in the normal way using `new`.</span></span> <span data-ttu-id="e1bf6-234">これは、前の例のコードでを使用する必要があることを意味し `CreateProxy` ます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-234">This means the code from the previous examples must now make use of `CreateProxy`:</span></span>

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(
                context.CreateProxy<Post>(
                    p =>
                        {
                            p.Title = "What’s next for System.Text.Json?";
                            p.Content = ".NET 5.0 was released recently and has come with many...";
                        }));

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracking_proxies_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Change_tracking_proxies_1)]

## <a name="change-tracking-events"></a><span data-ttu-id="e1bf6-235">変更の追跡イベント</span><span class="sxs-lookup"><span data-stu-id="e1bf6-235">Change tracking events</span></span>

<span data-ttu-id="e1bf6-236"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType>エンティティが初めて追跡されたときにイベントを発生させる EF Core ます。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-236">EF Core fires the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> event when an entity is tracked for the first time.</span></span> <span data-ttu-id="e1bf6-237">将来のエンティティの状態の変更によって、イベントが発生 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> します。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-237">Future entity state changes result in <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> events.</span></span> <span data-ttu-id="e1bf6-238">詳細については、「[EF Core での .NET イベント](xref:core/logging-events-diagnostics/events)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-238">See [.NET Events in EF Core](xref:core/logging-events-diagnostics/events) for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="e1bf6-239">`StateChanged`状態がから `Detached` 他の状態に変更された場合でも、エンティティが最初に追跡されるときにイベントは発生しません。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-239">The `StateChanged` event is not fired when an entity is first tracked, even though the state has changed from `Detached` to one of the other states.</span></span> <span data-ttu-id="e1bf6-240">イベントとイベントの両方をリッスンして、関連するすべての通知を取得するようにしてください `StateChanged` `Tracked` 。</span><span class="sxs-lookup"><span data-stu-id="e1bf6-240">Make sure to listen for both `StateChanged` and `Tracked` events to get all relevant notifications.</span></span>
