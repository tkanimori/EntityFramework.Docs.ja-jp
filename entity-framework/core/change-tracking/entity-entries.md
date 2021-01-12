---
title: 追跡対象のエンティティへのアクセス-EF Core
description: EntityEntry、DbContext. Entries、および Dbcontext. Local を使用した、追跡されたエンティティへのアクセス
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/entity-entries
ms.openlocfilehash: f385016aba61535f33e34c622dd43ce6dc823fc5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129613"
---
# <a name="accessing-tracked-entities"></a><span data-ttu-id="9b3d9-103">追跡対象のエンティティへのアクセス</span><span class="sxs-lookup"><span data-stu-id="9b3d9-103">Accessing Tracked Entities</span></span>

<span data-ttu-id="9b3d9-104">によって追跡されるエンティティにアクセスするには、次の4つの主要な Api があり <xref:Microsoft.EntityFrameworkCore.DbContext> ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-104">There are four main APIs for accessing entities tracked by a <xref:Microsoft.EntityFrameworkCore.DbContext>:</span></span>

- <span data-ttu-id="9b3d9-105"><xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601>指定されたエンティティインスタンスのインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-105"><xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> returns an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instance for a given entity instance.</span></span>
- <span data-ttu-id="9b3d9-106"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601>すべての追跡対象エンティティ、または特定の型のすべての追跡対象エンティティのインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-106"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> returns <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances for all tracked entities, or for all tracked entities of a given type.</span></span>
- <span data-ttu-id="9b3d9-107"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>、、、およびでは、 <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> 主キーによって1つのエンティティが検索されます。まず、追跡対象のエンティティを探し、必要に応じてデータベースに対してクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-107"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> find a single entity by primary key, first looking in tracked entities, and then querying the database if needed.</span></span>
- <span data-ttu-id="9b3d9-108"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> DbSet によって表されるエンティティ型のエンティティの実際のエンティティ (EntityEntry インスタンスではない) を返します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-108"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns actual entities (not EntityEntry instances) for entities of the entity type represented by the DbSet.</span></span>

<span data-ttu-id="9b3d9-109">これらの各トピックについては、以下のセクションで詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-109">Each of these is described in more detail in the sections below.</span></span>

> [!TIP]
> <span data-ttu-id="9b3d9-110">このドキュメントでは、EF Core の変更の追跡のエンティティの状態と基本について理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-110">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="9b3d9-111">これらのトピックの詳細については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-111">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="9b3d9-112">[GitHub からサンプルコードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities)することで、このドキュメントのすべてのコードを実行し、デバッグすることができます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-112">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities).</span></span>

## <a name="using-dbcontextentry-and-entityentry-instances"></a><span data-ttu-id="9b3d9-113">DbContext および EntityEntry インスタンスの使用</span><span class="sxs-lookup"><span data-stu-id="9b3d9-113">Using DbContext.Entry and EntityEntry instances</span></span>

<span data-ttu-id="9b3d9-114">追跡対象の各エンティティについて、Entity Framework Core (EF Core) は次のことを追跡します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-114">For each tracked entity, Entity Framework Core (EF Core) keeps track of:</span></span>

- <span data-ttu-id="9b3d9-115">エンティティの全体的な状態。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-115">The overall state of the entity.</span></span> <span data-ttu-id="9b3d9-116">これは、、、、またはのいずれかです。 `Unchanged` `Modified` 詳細については、EF Core の `Added` Change Tracking を参照し `Deleted` てください。 [](xref:core/change-tracking/index)</span><span class="sxs-lookup"><span data-stu-id="9b3d9-116">This is one of `Unchanged`, `Modified`, `Added`, or `Deleted`; see [Change Tracking in EF Core](xref:core/change-tracking/index) for more information.</span></span>
- <span data-ttu-id="9b3d9-117">追跡対象のエンティティ間のリレーションシップ。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-117">The relationships between tracked entities.</span></span> <span data-ttu-id="9b3d9-118">たとえば、投稿が属しているブログです。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-118">For example, the blog to which a post belongs.</span></span>
- <span data-ttu-id="9b3d9-119">プロパティの "現在の値"。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-119">The "current values" of properties.</span></span>
- <span data-ttu-id="9b3d9-120">プロパティの "元の値"。この情報を取得できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-120">The "original values" of properties, when this information is available.</span></span> <span data-ttu-id="9b3d9-121">元の値は、エンティティがデータベースから照会されたときに存在していたプロパティ値です。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-121">Original values are the property values that existed when entity was queried from the database.</span></span>
- <span data-ttu-id="9b3d9-122">クエリ後にどのプロパティ値が変更されたか。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-122">Which property values have been modified since they were queried.</span></span>
- <span data-ttu-id="9b3d9-123">値が [一時的](xref:core/change-tracking/miscellaneous#temporary-values)であるかどうかなど、プロパティ値に関するその他の情報。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-123">Other information about property values, such as whether or not the value is [temporary](xref:core/change-tracking/miscellaneous#temporary-values).</span></span>

<span data-ttu-id="9b3d9-124">エンティティインスタンスをに渡すと <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> 、によっ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> て、指定されたエンティティのこの情報にアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-124">Passing an entity instance to <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> results in an <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> providing access to this information for the given entity.</span></span> <span data-ttu-id="9b3d9-125">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-125">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var blog = context.Blogs.Single(e => e.Id == 1);
        var entityEntry = context.Entry(blog);

-->
[!code-csharp[Using_DbContext_Entry_and_EntityEntry_instances_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbContext_Entry_and_EntityEntry_instances_1)]

<span data-ttu-id="9b3d9-126">次のセクションでは、EntityEntry を使用してエンティティの状態にアクセスして操作する方法と、エンティティのプロパティとナビゲーションの状態について説明します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-126">The following sections show how to use an EntityEntry to access and manipulate entity state, as well as the state of the entity's properties and navigations.</span></span>

### <a name="working-with-the-entity"></a><span data-ttu-id="9b3d9-127">エンティティの操作</span><span class="sxs-lookup"><span data-stu-id="9b3d9-127">Working with the entity</span></span>

<span data-ttu-id="9b3d9-128">の最も一般的な使用方法 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> は、エンティティの現在のにアクセスすることです <xref:Microsoft.EntityFrameworkCore.EntityState> 。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-128">The most common use of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> is to access the current <xref:Microsoft.EntityFrameworkCore.EntityState> of an entity.</span></span> <span data-ttu-id="9b3d9-129">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-129">For example:</span></span>

<!--
        var currentState = context.Entry(blog).State;
        if (currentState == EntityState.Unchanged)
        {
            context.Entry(blog).State = EntityState.Modified;
        }
-->
[!code-csharp[Work_with_the_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_1)]

<span data-ttu-id="9b3d9-130">Entry メソッドは、まだ追跡されていないエンティティに対しても使用できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-130">The Entry method can also be used on entities that are not yet tracked.</span></span> <span data-ttu-id="9b3d9-131">これ _は、エンティティの追跡を開始しません_。エンティティの状態は依然と `Detatched` してです。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-131">This _does not start tracking the entity_; the state of the entity is still `Detatched`.</span></span> <span data-ttu-id="9b3d9-132">ただし、返された EntityEntry を使用してエンティティの状態を変更することができます。その時点で、エンティティは特定の状態で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-132">However, the returned EntityEntry can then be used to change the entity state, at which point the entity will become tracked in the given state.</span></span> <span data-ttu-id="9b3d9-133">たとえば、次のコードでは、ブログインスタンスの追跡が開始され `Added` ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-133">For example, the following code will start tracking a Blog instance as `Added`:</span></span>

<!--
        var newBlog = new Blog();
        Debug.Assert(context.Entry(newBlog).State == EntityState.Detached);

        context.Entry(newBlog).State = EntityState.Added;
        Debug.Assert(context.Entry(newBlog).State == EntityState.Added);
-->
[!code-csharp[Work_with_the_entity_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_2)]

> [!TIP]
> <span data-ttu-id="9b3d9-134">EF6 とは異なり、個々のエンティティの状態を設定しても、接続されているすべてのエンティティが追跡されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-134">Unlike in EF6, setting the state of an individual entity will not cause all connected entities to be tracked.</span></span> <span data-ttu-id="9b3d9-135">これにより `Add` 、 `Attach` `Update` エンティティのグラフ全体に作用する、、またはを呼び出すよりも低いレベルの操作に状態が設定されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-135">This makes setting the state this way a lower-level operation than calling `Add`, `Attach`, or `Update`, which operate on an entire graph of entities.</span></span>

<span data-ttu-id="9b3d9-136">次の表は、EntityEntry を使用してエンティティ全体を操作する方法をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-136">The following table summarizes ways to use an EntityEntry to work with an entire entity:</span></span>

| <span data-ttu-id="9b3d9-137">EntityEntry メンバー</span><span class="sxs-lookup"><span data-stu-id="9b3d9-137">EntityEntry member</span></span>                                                                                         | <span data-ttu-id="9b3d9-138">説明</span><span class="sxs-lookup"><span data-stu-id="9b3d9-138">Description</span></span>
|:-----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.State?displayProperty=nameWithType>         | <span data-ttu-id="9b3d9-139">エンティティのを取得または設定し <xref:Microsoft.EntityFrameworkCore.EntityState> ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-139">Gets and sets the <xref:Microsoft.EntityFrameworkCore.EntityState> of the entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Entity?displayProperty=nameWithType>        | <span data-ttu-id="9b3d9-140">エンティティインスタンスを取得します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-140">Gets the entity instance.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Context?displayProperty=nameWithType>       | <span data-ttu-id="9b3d9-141"><xref:Microsoft.EntityFrameworkCore.DbContext>このエンティティを追跡している。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-141">The <xref:Microsoft.EntityFrameworkCore.DbContext> that is tracking this entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Metadata?displayProperty=nameWithType>      | <span data-ttu-id="9b3d9-142"><xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> エンティティの型のメタデータ。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-142"><xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata for the type of entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.IsKeySet?displayProperty=nameWithType>      | <span data-ttu-id="9b3d9-143">エンティティのキー値が設定されているかどうか。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-143">Whether or not the entity has had its key value set.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload?displayProperty=nameWithType>        | <span data-ttu-id="9b3d9-144">データベースから読み取った値を使用してプロパティ値を上書きします。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-144">Overwrites property values with values read from the database.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> | <span data-ttu-id="9b3d9-145">このエンティティの変更のみを強制的に検出します。「 [変更の検出と通知」を](xref:core/change-tracking/change-detection)参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-145">Forces detection of changes for this entity only; see [Change Detection and Notifications](xref:core/change-tracking/change-detection).</span></span>

### <a name="working-with-a-single-property"></a><span data-ttu-id="9b3d9-146">1つのプロパティの操作</span><span class="sxs-lookup"><span data-stu-id="9b3d9-146">Working with a single property</span></span>

<span data-ttu-id="9b3d9-147">のいくつかのオーバーロードでは <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> 、エンティティの個々のプロパティに関する情報へのアクセスが許可されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-147">Several overloads of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> allow access to information about an individual property of an entity.</span></span> <span data-ttu-id="9b3d9-148">たとえば、厳密に型指定された、fluent に似た API を使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-148">For example, using a strongly-typed, fluent-like API:</span></span>

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property(e => e.Name);
-->
[!code-csharp[Work_with_a_single_property_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1a)]

<span data-ttu-id="9b3d9-149">代わりに、プロパティ名を文字列として渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-149">The property name can instead be passed as a string.</span></span> <span data-ttu-id="9b3d9-150">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-150">For example:</span></span>

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property<string>("Name");
-->
[!code-csharp[Work_with_a_single_property_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1b)]

<span data-ttu-id="9b3d9-151">返されたを <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> 使用して、プロパティに関する情報にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-151">The returned <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> can then be used to access information about the property.</span></span> <span data-ttu-id="9b3d9-152">たとえば、このエンティティのプロパティの現在の値を取得して設定するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-152">For example, it can be used to get and set the current value of the property on this entity:</span></span>

<!--
            string currentValue = context.Entry(blog).Property(e => e.Name).CurrentValue;
            context.Entry(blog).Property(e => e.Name).CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1d)]

<span data-ttu-id="9b3d9-153">上記のいずれのプロパティメソッドも、厳密に型指定されたジェネリックインスタンスを返し <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-153">Both of the Property methods used above return a strongly-typed generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> instance.</span></span> <span data-ttu-id="9b3d9-154">このジェネリック型を使用することをお勧めします。これは、 [値型をボックス](/dotnet/csharp/programming-guide/types/boxing-and-unboxing)化せずにプロパティ値にアクセスできるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-154">Using this generic type is preferred because it allows access to property values without [boxing value types](/dotnet/csharp/programming-guide/types/boxing-and-unboxing).</span></span> <span data-ttu-id="9b3d9-155">ただし、エンティティまたはプロパティの型がコンパイル時にわからない場合は、代わりに非ジェネリックを <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> 取得できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-155">However, if the type of entity or property is not known at compile-time, then a non-generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> can be obtained instead:</span></span>

<!--
            var propertyEntry = context.Entry(blog).Property("Name");
-->
[!code-csharp[Work_with_a_single_property_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1c)]

<span data-ttu-id="9b3d9-156">これにより、ボックス化された値の型を犠牲にして、型に関係なく、任意のプロパティのプロパティ情報にアクセスできるようになります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-156">This allows access to property information for any property regardless of its type, at the expense of boxing value types.</span></span> <span data-ttu-id="9b3d9-157">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-157">For example:</span></span>

<!--
            object blog = context.Blogs.Single(e => e.Id == 1);

            object currentValue = context.Entry(blog).Property("Name").CurrentValue;
            context.Entry(blog).Property("Name").CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1e)]

<span data-ttu-id="9b3d9-158">次の表は、PropertyEntry によって公開されるプロパティ情報をまとめたものです。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-158">The following table summarizes property information exposed by PropertyEntry:</span></span>

| <span data-ttu-id="9b3d9-159">PropertyEntry メンバー</span><span class="sxs-lookup"><span data-stu-id="9b3d9-159">PropertyEntry member</span></span>                               | <span data-ttu-id="9b3d9-160">説明</span><span class="sxs-lookup"><span data-stu-id="9b3d9-160">Description</span></span>
|:-------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.CurrentValue?displayProperty=nameWithType>  | <span data-ttu-id="9b3d9-161">プロパティの現在の値を取得および設定します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-161">Gets and sets the current value of the property.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.OriginalValue?displayProperty=nameWithType> | <span data-ttu-id="9b3d9-162">プロパティの元の値を取得または設定します (使用可能な場合)。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-162">Gets and sets the original value of the property, if available.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.EntityEntry?displayProperty=nameWithType>   | <span data-ttu-id="9b3d9-163">エンティティのへの後方参照 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> 。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-163">A back reference to the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> for the entity.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.Metadata?displayProperty=nameWithType>          | <span data-ttu-id="9b3d9-164"><xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> プロパティのメタデータ。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-164"><xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> metadata for the property.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified?displayProperty=nameWithType>        | <span data-ttu-id="9b3d9-165">このプロパティが変更済みとしてマークされているかどうかを示し、この状態を変更できるようにします。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-165">Indicates whether this property is marked as modified, and allows this state to be changed.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary?displayProperty=nameWithType>       | <span data-ttu-id="9b3d9-166">このプロパティが [一時](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values)としてマークされているかどうかを示し、この状態を変更できるようにします。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-166">Indicates whether this property is marked as [temporary](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values), and allows this state to be changed.</span></span>

<span data-ttu-id="9b3d9-167">注:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-167">Notes:</span></span>

- <span data-ttu-id="9b3d9-168">プロパティの元の値は、エンティティがデータベースから照会されたときのプロパティの値です。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-168">The original value of a property is the value that the property had when the entity was queried from the database.</span></span> <span data-ttu-id="9b3d9-169">ただし、またはなど、エンティティが切断され、別の DbContext に明示的にアタッチされている場合、元の値は使用できません `Attach` `Update` 。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-169">However, original values are not available if the entity was disconnected and then explicitly attached to another DbContext, for example with `Attach` or `Update`.</span></span> <span data-ttu-id="9b3d9-170">この場合、返される元の値は、現在の値と同じになります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-170">In this case, the original value returned will be the same as the current value.</span></span>
- <span data-ttu-id="9b3d9-171"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 変更済みとしてマークされているプロパティのみを更新します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-171"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> will only update properties marked as modified.</span></span> <span data-ttu-id="9b3d9-172"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified>指定したプロパティ値を EF Core に強制的に更新する場合は true に設定し、EF Core によってプロパティ値が更新されないようにする場合は false に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-172">Set <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> to true to force EF Core to update a given property value, or set it to false to prevent EF Core from updating the property value.</span></span>
- <span data-ttu-id="9b3d9-173">[一時的な値](xref:core/change-tracking/miscellaneous) は、通常 EF Core [値ジェネレーター](xref:core/modeling/generated-properties)によって生成されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-173">[Temporary values](xref:core/change-tracking/miscellaneous) are typically generated by EF Core [value generators](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="9b3d9-174">プロパティの現在の値を設定すると、一時的な値が指定された値に置き換えられ、プロパティが一時的ではないとマークされます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-174">Setting the current value of a property will replace the temporary value with the given value and mark the property as not temporary.</span></span> <span data-ttu-id="9b3d9-175"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary>明示的に設定された後でも強制的に値を一時的にする場合は true に設定します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-175">Set <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> to true to force a value to be temporary even after it has been explicitly set.</span></span>

### <a name="working-with-a-single-navigation"></a><span data-ttu-id="9b3d9-176">単一のナビゲーションの操作</span><span class="sxs-lookup"><span data-stu-id="9b3d9-176">Working with a single navigation</span></span>

<span data-ttu-id="9b3d9-177">、、およびのいくつかのオーバーロードは <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType> 、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType> 個々の <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> ナビゲーションに関する情報へのアクセスを許可します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-177">Several overloads of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> allow access to information about an individual navigation.</span></span>

<span data-ttu-id="9b3d9-178">1つの関連エンティティへの参照ナビゲーションには、メソッドを使用してアクセス <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-178">Reference navigations to a single related entity are accessed through the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> methods.</span></span> <span data-ttu-id="9b3d9-179">参照ナビゲーションは、一対多のリレーションシップの "一" の側、および一対一のリレーションシップの両側を指します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-179">Reference navigations point to the "one" sides of one-to-many relationships, and both sides of one-to-one relationships.</span></span> <span data-ttu-id="9b3d9-180">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-180">For example:</span></span>

<!--
        ReferenceEntry<Post, Blog> referenceEntry1 = context.Entry(post).Reference(e => e.Blog);
        ReferenceEntry<Post, Blog> referenceEntry2 = context.Entry(post).Reference<Blog>("Blog");
        ReferenceEntry referenceEntry3 = context.Entry(post).Reference("Blog");
-->
[!code-csharp[Work_with_a_single_navigation_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_1)]

<span data-ttu-id="9b3d9-181">ナビゲーションは、一対多および多対多のリレーションシップの "多" の側で使用される場合に、関連エンティティのコレクションにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-181">Navigations can also be collections of related entities when used for the "many" sides of one-to-many and many-to-many relationships.</span></span> <span data-ttu-id="9b3d9-182">メソッドは、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> コレクションのナビゲーションにアクセスするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-182">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> methods are used to access collection navigations.</span></span> <span data-ttu-id="9b3d9-183">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-183">For example:</span></span>

<!--
        CollectionEntry<Blog, Post> collectionEntry1 = context.Entry(blog).Collection(e => e.Posts);
        CollectionEntry<Blog, Post> collectionEntry2 = context.Entry(blog).Collection<Post>("Posts");
        CollectionEntry collectionEntry3 = context.Entry(blog).Collection("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2a)]

<span data-ttu-id="9b3d9-184">一部の操作は、すべてのナビゲーションで共通です。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-184">Some operations are common for all navigations.</span></span> <span data-ttu-id="9b3d9-185">これらのアクセスには、メソッドを使用して参照とコレクションの両方のナビゲーションを行うことができ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-185">These can be accessed for both reference and collection navigations using the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> method.</span></span> <span data-ttu-id="9b3d9-186">すべてのナビゲーションにアクセスするときに使用できるのは非ジェネリックアクセスのみであることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-186">Note that only non-generic access is available when accessing all navigations together.</span></span> <span data-ttu-id="9b3d9-187">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-187">For example:</span></span>

<!--
        NavigationEntry navigationEntry = context.Entry(blog).Navigation("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2b)]

<span data-ttu-id="9b3d9-188">次の表は、、、およびの使用方法をまとめたもの <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> です。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-188">The following table summarizes ways to use <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602>, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602>, and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry>:</span></span>

| <span data-ttu-id="9b3d9-189">NavigationEntry メンバー</span><span class="sxs-lookup"><span data-stu-id="9b3d9-189">NavigationEntry member</span></span>                                                                                    | <span data-ttu-id="9b3d9-190">説明</span><span class="sxs-lookup"><span data-stu-id="9b3d9-190">Description</span></span>
|:----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.MemberEntry.CurrentValue?displayProperty=nameWithType> | <span data-ttu-id="9b3d9-191">ナビゲーションの現在の値を取得および設定します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-191">Gets and sets the current value of the navigation.</span></span> <span data-ttu-id="9b3d9-192">これはコレクションナビゲーションのコレクション全体です。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-192">This is the entire collection for collection navigations.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Metadata?displayProperty=nameWithType> | <span data-ttu-id="9b3d9-193"><xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> ナビゲーションのメタデータ。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-193"><xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> metadata for the navigation.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.IsLoaded?displayProperty=nameWithType> | <span data-ttu-id="9b3d9-194">関連するエンティティまたはコレクションがデータベースから完全に読み込まれているかどうかを示す値を取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-194">Gets or sets a value indicating whether the related entity or collection has been fully loaded from the database.</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Load?displayProperty=nameWithType>     | <span data-ttu-id="9b3d9-195">データベースから関連エンティティまたはコレクションを読み込みます。「 [関連データの明示的な読み込み」を](xref:core/querying/related-data/explicit)参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-195">Loads the related entity or collection from the database; see [Explicit Loading of Related Data](xref:core/querying/related-data/explicit).</span></span>
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Query?displayProperty=nameWithType>    | <span data-ttu-id="9b3d9-196">クエリ EF Core は、を使用して、さらに構成できるとしてこのナビゲーションを読み込みます `IQueryable` 。「 [関連データの明示的な読み込み](xref:core/querying/related-data/explicit)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-196">The query EF Core would use to load this navigation as an `IQueryable` that can be further composed; see [Explicit Loading of Related Data](xref:core/querying/related-data/explicit).</span></span>

### <a name="working-with-all-properties-of-an-entity"></a><span data-ttu-id="9b3d9-197">エンティティのすべてのプロパティを操作する</span><span class="sxs-lookup"><span data-stu-id="9b3d9-197">Working with all properties of an entity</span></span>

<span data-ttu-id="9b3d9-198"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType><xref:System.Collections.Generic.IEnumerable%601> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> エンティティのすべてのプロパティについて、のを返します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-198"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> returns an <xref:System.Collections.Generic.IEnumerable%601> of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> for every property of the entity.</span></span> <span data-ttu-id="9b3d9-199">これは、エンティティのすべてのプロパティに対してアクションを実行するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-199">This can be used to perform an action for every property of the entity.</span></span> <span data-ttu-id="9b3d9-200">たとえば、任意の DateTime プロパティをに設定するには、次のようにし `DateTime.Now` ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-200">For example, to set any DateTime property to `DateTime.Now`:</span></span>

<!--
        foreach (var propertyEntry in context.Entry(blog).Properties)
        {
            if (propertyEntry.Metadata.ClrType == typeof(DateTime))
            {
                propertyEntry.CurrentValue = DateTime.Now;
            }
        }
-->
[!code-csharp[Work_with_all_properties_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_1)]

<span data-ttu-id="9b3d9-201">さらに、EntityEntry には、すべてのプロパティ値を同時に取得および設定するためのメソッドがいくつか含まれています。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-201">In addition, EntityEntry contains several methods to get and set all property values at the same time.</span></span> <span data-ttu-id="9b3d9-202">これらのメソッドは、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> プロパティとその値のコレクションを表すクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-202">These methods use the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> class, which represents a collection of properties and their values.</span></span> <span data-ttu-id="9b3d9-203">PropertyValues は、現在または元の値、または現在データベースに格納されている値に対して取得できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-203">PropertyValues can be obtained for current or original values, or for the values as currently stored in the database.</span></span> <span data-ttu-id="9b3d9-204">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-204">For example:</span></span>

<!--
        var currentValues = context.Entry(blog).CurrentValues;
        var originalValues = context.Entry(blog).OriginalValues;
        var databaseValues = context.Entry(blog).GetDatabaseValues();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2a)]

<span data-ttu-id="9b3d9-205">これらの PropertyValues オブジェクトは、独自にはあまり役に立ちません。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-205">These PropertyValues objects are not very useful on their own.</span></span> <span data-ttu-id="9b3d9-206">ただし、エンティティを操作するときに必要な一般的な操作を実行するために組み合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-206">However, they can be combined to perform common operations needed when manipulating entities.</span></span> <span data-ttu-id="9b3d9-207">これは、データ転送オブジェクトを操作する場合や、 [オプティミスティック同時実行の競合](xref:core/saving/concurrency)を解決する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-207">This is useful when working with data transfer objects and when resolving [optimistic concurrency conflicts](xref:core/saving/concurrency).</span></span> <span data-ttu-id="9b3d9-208">次のセクションでは、いくつかの例を示します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-208">The following sections show some examples.</span></span>

#### <a name="setting-current-or-original-values-from-an-entity-or-dto"></a><span data-ttu-id="9b3d9-209">エンティティまたは DTO からの現在または元の値の設定</span><span class="sxs-lookup"><span data-stu-id="9b3d9-209">Setting current or original values from an entity or DTO</span></span>

<span data-ttu-id="9b3d9-210">エンティティの現在の値または元の値は、別のオブジェクトから値をコピーすることによって更新できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-210">The current or original values of an entity can be updated by copying values from another object.</span></span> <span data-ttu-id="9b3d9-211">たとえば、 `BlogDto` エンティティ型と同じプロパティを持つデータ転送オブジェクト (DTO) について考えてみます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-211">For example, consider a `BlogDto` data transfer object (DTO) with the same properties as the entity type:</span></span>

<!--
public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
-->
[!code-csharp[BlogDto](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=BlogDto)]

<span data-ttu-id="9b3d9-212">これは、を使用して、追跡対象エンティティの現在の値を設定するために使用でき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-212">This can be used to set the current values of a tracked entity using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType>:</span></span>

<!--
        var blogDto = new BlogDto { Id = 1, Name = "1unicorn2" };

        context.Entry(blog).CurrentValues.SetValues(blogDto);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2b)]

<span data-ttu-id="9b3d9-213">この手法は、サービス呼び出しまたは n 層アプリケーションのクライアントから取得した値を使用してエンティティを更新するときに使用されることがあります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-213">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="9b3d9-214">使用されるオブジェクトは、エンティティと同じ型である必要はありません。ただし、エンティティの名前と名前が一致するプロパティがある場合に限ります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-214">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="9b3d9-215">上の例では、DTO のインスタンスを使用して、 `BlogDto` 追跡対象のエンティティの現在の値を設定してい `Blog` ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-215">In the example above, an instance of the DTO `BlogDto` is used to set the current values of a tracked `Blog` entity.</span></span>

<span data-ttu-id="9b3d9-216">プロパティは、現在の値と異なる値が設定されている場合にのみ、変更済みとしてマークされることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-216">Note that properties will only be marked as modified if the value set differs from the current value.</span></span>

#### <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="9b3d9-217">ディクショナリからの現在または元の値の設定</span><span class="sxs-lookup"><span data-stu-id="9b3d9-217">Setting current or original values from a dictionary</span></span>

<span data-ttu-id="9b3d9-218">前の例では、エンティティまたは DTO インスタンスから値を設定しています。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-218">The previous example set values from an entity or DTO instance.</span></span> <span data-ttu-id="9b3d9-219">プロパティ値がディクショナリ内の名前と値のペアとして格納されている場合も、同じ動作が使用できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-219">The same behavior is available when property values are stored as name/value pairs in a dictionary.</span></span> <span data-ttu-id="9b3d9-220">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-220">For example:</span></span>

<!--
        var blogDictionary = new Dictionary<string, object>
        {
            ["Id"] = 1,
            ["Name"] = "1unicorn2"
        };

        context.Entry(blog).CurrentValues.SetValues(blogDictionary);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2d)]

#### <a name="setting-current-or-original-values-from-the-database"></a><span data-ttu-id="9b3d9-221">データベースからの現在の値または元の値の設定</span><span class="sxs-lookup"><span data-stu-id="9b3d9-221">Setting current or original values from the database</span></span>

<span data-ttu-id="9b3d9-222">またはを呼び出し、返されたオブジェクトを使用して現在の値または元の <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> 値を設定することにより、エンティティの現在の値または元の値をデータベースの最新の値で更新できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-222">The current or original values of an entity can be updated with the latest values from the database by calling <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> or <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> and using the returned object to set current or original values, or both.</span></span> <span data-ttu-id="9b3d9-223">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-223">For example:</span></span>

<!--
        var databaseValues = context.Entry(blog).GetDatabaseValues();
        context.Entry(blog).CurrentValues.SetValues(databaseValues);
        context.Entry(blog).OriginalValues.SetValues(databaseValues);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2c)]

#### <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="9b3d9-224">現在、元、またはデータベースの値を含む複製されたオブジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="9b3d9-224">Creating a cloned object containing current, original, or database values</span></span>

<span data-ttu-id="9b3d9-225">CurrentValues、OriginalValues、または GetDatabaseValues から返された PropertyValues オブジェクトを使用すると、を使用してエンティティの複製を作成でき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-225">The PropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType>.</span></span> <span data-ttu-id="9b3d9-226">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-226">For example:</span></span>

<!--
var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2e)]

<span data-ttu-id="9b3d9-227">は、 `ToObject` DbContext によって追跡されない新しいインスタンスを返すことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-227">Note that `ToObject` returns a new instance that is not tracked by the DbContext.</span></span> <span data-ttu-id="9b3d9-228">返されたオブジェクトには、他のエンティティに設定されたリレーションシップもありません。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-228">The returned object also does not have any relationships set to other entities.</span></span>

<span data-ttu-id="9b3d9-229">複製されたオブジェクトは、特に特定の型のオブジェクトにデータをバインドする場合に、データベースに対する同時更新に関連する問題を解決するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-229">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially when data binding to objects of a certain type.</span></span> <span data-ttu-id="9b3d9-230">詳細については、「 [オプティミスティック同時実行制御](xref:core/saving/concurrency) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-230">See [optimistic concurrency](xref:core/saving/concurrency) for more information.</span></span>

### <a name="working-with-all-navigations-of-an-entity"></a><span data-ttu-id="9b3d9-231">エンティティのすべてのナビゲーションを操作する</span><span class="sxs-lookup"><span data-stu-id="9b3d9-231">Working with all navigations of an entity</span></span>

<span data-ttu-id="9b3d9-232"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType><xref:System.Collections.Generic.IEnumerable%601> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> エンティティのすべてのナビゲーションについて、のを返します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-232"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> returns an <xref:System.Collections.Generic.IEnumerable%601> of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> for every navigation of the entity.</span></span> <span data-ttu-id="9b3d9-233"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> とは同じことを <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> 行いますが、それぞれ参照またはコレクションのナビゲーションに限定されています。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-233"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> do the same thing, but restricted to reference or collection navigations respectively.</span></span> <span data-ttu-id="9b3d9-234">これは、エンティティのすべてのナビゲーションに対してアクションを実行するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-234">This can be used to perform an action for every navigation of the entity.</span></span> <span data-ttu-id="9b3d9-235">たとえば、すべての関連エンティティの読み込みを強制的に実行するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-235">For example, to force loading of all related entities:</span></span>

<!--
        foreach (var navigationEntry in context.Entry(blog).Navigations)
        {
            navigationEntry.Load();
        }
-->
[!code-csharp[Work_with_all_navigations_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_navigations_of_an_entity_1)]

### <a name="working-with-all-members-of-an-entity"></a><span data-ttu-id="9b3d9-236">エンティティのすべてのメンバーの操作</span><span class="sxs-lookup"><span data-stu-id="9b3d9-236">Working with all members of an entity</span></span>

<span data-ttu-id="9b3d9-237">標準プロパティとナビゲーションプロパティの状態と動作は異なります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-237">Regular properties and navigation properties have different state and behavior.</span></span> <span data-ttu-id="9b3d9-238">そのため、上のセクションで説明したように、ナビゲーションと非ナビゲーションを個別に処理することが一般的です。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-238">It is therefore common to process navigations and non-navigations separately, as shown in the sections above.</span></span> <span data-ttu-id="9b3d9-239">ただし、通常のプロパティとナビゲーションのどちらであるかに関係なく、エンティティのメンバーに対して何らかの処理を実行すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-239">However, sometimes it can be useful to do something with any member of the entity, regardless of whether it is a regular property or navigation.</span></span> <span data-ttu-id="9b3d9-240"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> および <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> は、この目的のために用意されています。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-240"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> are provided for this purpose.</span></span> <span data-ttu-id="9b3d9-241">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-241">For example:</span></span>

<!--
        foreach (var memberEntry in context.Entry(blog).Members)
        {
            Console.WriteLine(
                $"Member {memberEntry.Metadata.Name} is of type {memberEntry.Metadata.ClrType.ShortDisplayName()} and has value {memberEntry.CurrentValue}");
        }
-->
[!code-csharp[Work_with_all_members_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_members_of_an_entity_1)]

<span data-ttu-id="9b3d9-242">このサンプルのブログでこのコードを実行すると、次の出力が生成されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-242">Running this code on a blog from the sample generates the following output:</span></span>

```output
Member Id is of type int and has value 1
Member Name is of type string and has value .NET Blog
Member Posts is of type IList<Post> and has value System.Collections.Generic.List`1[Post]
```

> [!TIP]
> <span data-ttu-id="9b3d9-243">[ [トラッカーの変更] デバッグビュー](xref:core/change-tracking/debug-views) には、次のような情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-243">The [change tracker debug view](xref:core/change-tracking/debug-views) shows information like this.</span></span> <span data-ttu-id="9b3d9-244">変更トラッカー全体の [デバッグ] ビューは、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> 追跡対象の各エンティティの個々のから生成されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-244">The debug view for the entire change tracker is generated from the individual <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> of each tracked entity.</span></span>

## <a name="find-and-findasync"></a><span data-ttu-id="9b3d9-245">Find と FindAsync</span><span class="sxs-lookup"><span data-stu-id="9b3d9-245">Find and FindAsync</span></span>

<span data-ttu-id="9b3d9-246"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>、 <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> 、 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> 、および <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> は、主キーがわかっている場合に、1つのエンティティを効率的に検索できるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-246"><xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> are designed for efficient lookup of a single entity when its primary key is known.</span></span> <span data-ttu-id="9b3d9-247">最初にエンティティが追跡されているかどうかを検索し、存在する場合はそのエンティティを直ちに返します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-247">Find first checks if the entity is already tracked, and if so returns the entity immediately.</span></span> <span data-ttu-id="9b3d9-248">データベースクエリが行われるのは、エンティティがローカルで追跡されていない場合のみです。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-248">A database query is only made if the entity is not tracked locally.</span></span> <span data-ttu-id="9b3d9-249">たとえば、同じエンティティに対して Find を2回呼び出すコードを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-249">For example, consider this code that calls Find twice for the same entity:</span></span>

<!--
        using var context = new BlogsContext();

        Console.WriteLine("First call to Find...");
        var blog1 = context.Blogs.Find(1);

        Console.WriteLine($"...found blog {blog1.Name}");

        Console.WriteLine();
        Console.WriteLine("Second call to Find...");
        var blog2 = context.Blogs.Find(1);
        Debug.Assert(blog1 == blog2);

        Console.WriteLine("...returned the same instance without executing a query.");
-->
[!code-csharp[Find_and_FindAsync_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_1)]

<span data-ttu-id="9b3d9-250">SQLite を使用した場合のこのコード (EF Core のログ記録を含む) の出力は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-250">The output from this code (including EF Core logging) when using SQLite is:</span></span>

```output
First call to Find...
info: 12/29/2020 07:45:53.682 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (1ms) [Parameters=[@__p_0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
      SELECT "b"."Id", "b"."Name"
      FROM "Blogs" AS "b"
      WHERE "b"."Id" = @__p_0
      LIMIT 1
...found blog .NET Blog

Second call to Find...
...returned the same instance without executing a query.
```

<span data-ttu-id="9b3d9-251">最初の呼び出しではエンティティがローカルに見つからないことに注意してください。そのため、データベースクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-251">Notice that the first call does not find the entity locally and so executes a database query.</span></span> <span data-ttu-id="9b3d9-252">逆に、2回目の呼び出しは、既に追跡されているため、データベースに対してクエリを実行せずに同じインスタンスを返します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-252">Conversely, the second call returns the same instance without querying the database because it is already being tracked.</span></span>

<span data-ttu-id="9b3d9-253">指定されたキーを持つエンティティがローカルで追跡されず、データベースに存在しない場合、Find は null を返します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-253">Find returns null if an entity with the given key is not tracked locally and does not exist in the database.</span></span>

### <a name="composite-keys"></a><span data-ttu-id="9b3d9-254">複合キー</span><span class="sxs-lookup"><span data-stu-id="9b3d9-254">Composite keys</span></span>

<span data-ttu-id="9b3d9-255">Find は、複合キーと共に使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-255">Find can also be used with composite keys.</span></span> <span data-ttu-id="9b3d9-256">たとえば、 `OrderLine` 注文 ID と製品 ID で構成される複合キーを持つエンティティについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-256">For example, consider an `OrderLine` entity with a composite key consisting of the order ID and the product ID:</span></span>

<!--
public class OrderLine
{
    public int OrderId { get; set; }
    public int ProductId { get; set; }

    //...
}
-->
[!code-csharp[OrderLine](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OrderLine)]

<span data-ttu-id="9b3d9-257"><xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>キー部分 _とその順序_ を定義するには、複合キーをで構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-257">The composite key must be configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> to define the key parts _and their order_.</span></span> <span data-ttu-id="9b3d9-258">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-258">For example:</span></span>

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<OrderLine>()
            .HasKey(e => new { e.OrderId, e.ProductId });
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OnModelCreating)]

<span data-ttu-id="9b3d9-259">`OrderId`はキーの最初の部分であり、 `ProductId` キーの2番目の部分であることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-259">Notice that `OrderId` is the first part of the key and `ProductId` is the second part of the key.</span></span> <span data-ttu-id="9b3d9-260">検索するキー値を渡すときには、この順序を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-260">This order must be used when passing key values to Find.</span></span> <span data-ttu-id="9b3d9-261">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-261">For example:</span></span>

<!--
        var orderline = context.OrderLines.Find(orderId, productId);
-->
[!code-csharp[Find_and_FindAsync_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_2)]

## <a name="using-changetrackerentries-to-access-all-tracked-entities"></a><span data-ttu-id="9b3d9-262">ChangeTracker を使用して、すべての追跡対象エンティティにアクセスする</span><span class="sxs-lookup"><span data-stu-id="9b3d9-262">Using ChangeTracker.Entries to access all tracked entities</span></span>

<span data-ttu-id="9b3d9-263">これまでは、一度に1つのにしかアクセス <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> できませんでした。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-263">So far we have accessed only a single <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> at a time.</span></span> <span data-ttu-id="9b3d9-264"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> DbContext によって現在追跡されているすべてのエンティティの EntityEntry を返します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-264"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> returns an EntityEntry for every entity currently tracked by the DbContext.</span></span> <span data-ttu-id="9b3d9-265">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-265">For example:</span></span>

<!--
        using var context = new BlogsContext();
        var blogs = context.Blogs.Include(e => e.Posts).ToList();

        foreach (var entityEntry in context.ChangeTracker.Entries())
        {
            Console.WriteLine($"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property("Id").CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a)]

<span data-ttu-id="9b3d9-266">このコードは、次の出力を生成します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-266">This code generates the following output:</span></span>

```output
Found Blog entity with ID 1
Found Post entity with ID 1
Found Post entity with ID 2
```

<span data-ttu-id="9b3d9-267">ブログと投稿の両方のエントリが返されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-267">Notice that entries for both blogs and posts are returned.</span></span> <span data-ttu-id="9b3d9-268">代わりに、ジェネリックオーバーロードを使用して、結果を特定のエンティティ型にフィルター処理でき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-268">The results can instead be filtered to a specific entity type using the <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> generic overload:</span></span>

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<Post>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b)]

<span data-ttu-id="9b3d9-269">このコードからの出力は、投稿だけが返されることを示しています。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-269">The output from this code shows that only posts are returned:</span></span>

```output
Found Post entity with ID 1
Found Post entity with ID 2
```

<span data-ttu-id="9b3d9-270">また、ジェネリックオーバーロードを使用すると、ジェネリックインスタンスが返さ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> れます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-270">Also, using the generic overload returns generic <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instances.</span></span> <span data-ttu-id="9b3d9-271">これにより、この例のプロパティへの fluent 形式のアクセスが可能に `Id` なります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-271">This is what allows that fluent-like access to the `Id` property in this example.</span></span>

<span data-ttu-id="9b3d9-272">フィルター処理に使用されるジェネリック型は、マップされたエンティティ型である必要はありません。代わりに、マップされていない基本型またはインターフェイスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-272">The generic type used for filtering does not have to be a mapped entity type; an unmapped base type or interface can be used instead.</span></span> <span data-ttu-id="9b3d9-273">たとえば、モデル内のすべてのエンティティ型で、キープロパティを定義するインターフェイスが実装されているとします。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-273">For example, if all the entity types in the model implement an interface defining their key property:</span></span>

<!--
public interface IEntityWithKey
{
    int Id { get; set; }
}
-->
[!code-csharp[IEntityWithKey](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=IEntityWithKey)]

<span data-ttu-id="9b3d9-274">その後、このインターフェイスを使用して、厳密に型指定された方法で、任意の追跡対象エンティティのキーを操作できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-274">Then this interface can be used to work with the key of any tracked entity in a strongly-typed manner.</span></span> <span data-ttu-id="9b3d9-275">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-275">For example:</span></span>

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<IEntityWithKey>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c)]

## <a name="using-dbsetlocal-to-query-tracked-entities"></a><span data-ttu-id="9b3d9-276">DbSet. ローカルを使用した追跡対象エンティティの照会</span><span class="sxs-lookup"><span data-stu-id="9b3d9-276">Using DbSet.Local to query tracked entities</span></span>

<span data-ttu-id="9b3d9-277">EF Core のクエリは常にデータベースで実行され、データベースに保存されているエンティティのみが返されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-277">EF Core queries are always executed on the database, and only return entities that have been saved to the database.</span></span> <span data-ttu-id="9b3d9-278"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> ローカルの追跡対象エンティティの DbContext に対してクエリを実行するための機構を提供します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-278"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> provides a mechanism to query the DbContext for local, tracked entities.</span></span>

<span data-ttu-id="9b3d9-279">`DbSet.Local`は、追跡されたエンティティにクエリを実行するために使用されるため、通常は、エンティティを DbContext に読み込み、読み込まれたエンティティを操作します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-279">Since `DbSet.Local` is used to query tracked entities, it is typical to load entities into the DbContext and then work with those loaded entities.</span></span> <span data-ttu-id="9b3d9-280">これはデータバインディングに特に当てはまりますが、他の状況でも役立つことがあります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-280">This is especially true for data binding, but can also be useful in other situations.</span></span> <span data-ttu-id="9b3d9-281">たとえば、次のコードでは、最初にすべてのブログと投稿に対してデータベースにクエリを行います。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-281">For example, in the following code the database is first queried for all blogs and posts.</span></span> <span data-ttu-id="9b3d9-282"><xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A>拡張メソッドは、アプリケーションに直接返されることなく、コンテキストによって追跡された結果を使用して、このクエリを実行するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-282">The <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> extension method is used to execute this query with the results tracked by the context without being returned directly to the application.</span></span> <span data-ttu-id="9b3d9-283">(または類似点を使用する `ToList` と同じ効果がありますが、ここでは必要のない、返されるリストを作成するオーバーヘッドがあります)。次に、を使用して、 `DbSet.Local` ローカルで追跡されているエンティティにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-283">(Using `ToList` or similar has the same effect but with the overhead of creating the returned list, which is not needed here.) The example then uses `DbSet.Local` to access the locally tracked entities:</span></span>

<!--
        using var context = new BlogsContext();

        context.Blogs.Include(e => e.Posts).Load();

        foreach (var blog in context.Blogs.Local)
        {
            Console.WriteLine($"Blog: {blog.Name}");
        }

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_1)]

<span data-ttu-id="9b3d9-284">とは異なり、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> は `DbSet.Local` エンティティのインスタンスを直接返します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-284">Notice that, unlike <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType>, `DbSet.Local` returns entity instances directly.</span></span> <span data-ttu-id="9b3d9-285">もちろん、を呼び出して、返されたエンティティに対して EntityEntry を常に取得でき <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-285">An EntityEntry can, of course, always be obtained for the returned entity by calling <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType>.</span></span>

### <a name="the-local-view"></a><span data-ttu-id="9b3d9-286">ローカルビュー</span><span class="sxs-lookup"><span data-stu-id="9b3d9-286">The local view</span></span>

<span data-ttu-id="9b3d9-287"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> ローカルで追跡されているエンティティのうち、現在のエンティティを反映するビューを返し <xref:Microsoft.EntityFrameworkCore.EntityState> ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-287"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns a view of locally tracked entities that reflects the current <xref:Microsoft.EntityFrameworkCore.EntityState> of those entities.</span></span> <span data-ttu-id="9b3d9-288">具体的には、次のことを意味します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-288">Specifically, this means that:</span></span>

- <span data-ttu-id="9b3d9-289">`Added` エンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-289">`Added` entities are included.</span></span> <span data-ttu-id="9b3d9-290">`Added`エンティティはデータベースにまだ存在していないため、データベースクエリによって返されることはないため、通常の EF Core クエリではこれは当てはまりません。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-290">Note that this is not the case for normal EF Core queries, since `Added` entities do not yet exist in the database and so are therefore never returned by a database query.</span></span>
- <span data-ttu-id="9b3d9-291">`Deleted` エンティティは除外されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-291">`Deleted` entities are excluded.</span></span> <span data-ttu-id="9b3d9-292">`Deleted`エンティティはデータベースにまだ存在して _いるため、_ データベースクエリによって返されるので、通常の EF Core クエリの場合は、このようになります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-292">Note that this is again not the case for normal EF Core queries, since `Deleted` entities still exist in the database and so _are_ returned by database queries.</span></span>

<span data-ttu-id="9b3d9-293">これは、エンティティ `DbSet.Local` グラフの現在の概念状態を反映するデータを表示することを意味し `Added` ます。エンティティとエンティティは除外され `Deleted` ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-293">All of this means that `DbSet.Local` is view over the data that reflects the current conceptual state of the entity graph, with `Added` entities included and `Deleted` entities excluded.</span></span> <span data-ttu-id="9b3d9-294">これは、SaveChanges が呼び出された後に、どのデータベースの状態が予想されるかと一致します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-294">This matches what database state is expected to be after SaveChanges is called.</span></span>

<span data-ttu-id="9b3d9-295">これは、通常、データバインディングに最適なビューです。これは、アプリケーションによって行われた変更に基づいてデータを理解する際にユーザーに表示されるためです。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-295">This is typically the ideal view for data binding, since it presents to the user the data as they understand it based on the changes made by the application.</span></span>

<span data-ttu-id="9b3d9-296">次のコードは、1つの投稿をとしてマークした後、 `Deleted` 新しい投稿を追加し、としてマークする方法を示してい `Added` ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-296">The following code demonstrates this my marking one post as `Deleted` and then adding a new post, marking it as `Added`:</span></span>

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Remove(posts[1]);

        context.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_2)]

<span data-ttu-id="9b3d9-297">このコードからの出力は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-297">The output from this code is:</span></span>

```output
Local view after loading posts:
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing F# 5
  Post: Announcing .NET 5.0
Local view after adding and deleting posts:
  Post: What’s next for System.Text.Json?
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing .NET 5.0
```

<span data-ttu-id="9b3d9-298">削除された投稿がローカルビューから削除され、追加された投稿が含まれていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-298">Notice that the deleted post is removed from the local view, and the added post is included.</span></span>

### <a name="using-local-to-add-and-remove-entities"></a><span data-ttu-id="9b3d9-299">ローカルを使用したエンティティの追加と削除</span><span class="sxs-lookup"><span data-stu-id="9b3d9-299">Using Local to add and remove entities</span></span>

<span data-ttu-id="9b3d9-300"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> によって <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601> のインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-300"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> returns an instance of <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601>.</span></span> <span data-ttu-id="9b3d9-301">これは、 <xref:System.Collections.Generic.ICollection%601> エンティティがコレクションから追加および削除されたときに、通知を生成して応答するの実装です。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-301">This is an implementation of <xref:System.Collections.Generic.ICollection%601> that generates and responds to notifications when entities are added and removed from the collection.</span></span> <span data-ttu-id="9b3d9-302">(これはと同じ概念です <xref:System.Collections.ObjectModel.ObservableCollection%601> が、独立したコレクションとしてではなく、既存の EF Core 変更追跡エントリに対する射影として実装されます)。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-302">(This is the same concept as <xref:System.Collections.ObjectModel.ObservableCollection%601>, but implemented as a projection over existing EF Core change tracking entries, rather than as an independent collection.)</span></span>

<span data-ttu-id="9b3d9-303">ローカルビューの通知は、dbcontext の変更の追跡にフックされます。これにより、ローカルビューは DbContext と同期したままになります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-303">The local view's notifications are hooked into DbContext change tracking such that the local view stays in sync with the DbContext.</span></span> <span data-ttu-id="9b3d9-304">具体的な内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-304">Specifically:</span></span>

- <span data-ttu-id="9b3d9-305">新しいエンティティを追加する `DbSet.Local` と、DbContext (通常は状態) によって追跡され `Added` ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-305">Adding a new entity to `DbSet.Local` causes it to be tracked by the DbContext, typically in the `Added` state.</span></span> <span data-ttu-id="9b3d9-306">(エンティティに既に生成されたキー値がある場合は、代わりにとして追跡され `Unchanged` ます)。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-306">(If the entity already has a generated key value, then it is tracked as `Unchanged` instead.)</span></span>
- <span data-ttu-id="9b3d9-307">からエンティティを削除 `DbSet.Local` すると、そのエンティティはとマークさ `Deleted` れます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-307">Removing an entity from `DbSet.Local` causes it to be marked as `Deleted`.</span></span>
- <span data-ttu-id="9b3d9-308">DbContext によって追跡されるエンティティは、自動的にコレクションに表示され `DbSet.Local` ます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-308">An entity that becomes tracked by the DbContext will automatically appear in the `DbSet.Local` collection.</span></span> <span data-ttu-id="9b3d9-309">たとえば、より多くのエンティティを取り込むためにクエリを実行すると、ローカルビューが自動的に更新されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-309">For example, executing a query to bring in more entities automatically causes the local view to be updated.</span></span>
- <span data-ttu-id="9b3d9-310">としてマークされているエンティティ `Deleted` は、自動的にローカルコレクションから削除されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-310">An entity that is marked as `Deleted` will be removed from the local collection automatically.</span></span>

<span data-ttu-id="9b3d9-311">これは、コレクションに対してを追加および削除するだけで、追跡されたエンティティを操作するためにローカルビューを使用できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-311">This means the local view can be used to manipulate tracked entities simply by adding and removing from the collection.</span></span> <span data-ttu-id="9b3d9-312">たとえば、では、前の例のコードを変更して、ローカルコレクションから投稿を追加および削除できます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-312">For example, lets modify the previous example code to add and remove posts from the local collection:</span></span>

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Posts.Local.Remove(posts[1]);

        context.Posts.Local.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_3](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_3)]

<span data-ttu-id="9b3d9-313">ローカルビューに加えられた変更は DbContext と同期されるため、出力は前の例から変更されていません。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-313">The output remains unchanged from the previous example because changes made to the local view are synced with the DbContext.</span></span>

### <a name="using-the-local-view-for-windows-forms-or-wpf-data-binding"></a><span data-ttu-id="9b3d9-314">Windows フォームまたは WPF データバインディングのためのローカルビューの使用</span><span class="sxs-lookup"><span data-stu-id="9b3d9-314">Using the local view for Windows Forms or WPF data binding</span></span>

<span data-ttu-id="9b3d9-315"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> EF Core エンティティへのデータバインディングの基礎を形成します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-315"><xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> forms the basis for data binding to EF Core entities.</span></span> <span data-ttu-id="9b3d9-316">ただし、Windows フォームと WPF は、想定される特定の種類の通知コレクションと共に使用すると最適に動作します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-316">However, both Windows Forms and WPF work best when used with the specific type of notifying collection that they expect.</span></span> <span data-ttu-id="9b3d9-317">ローカルビューでは、次の特定のコレクション型の作成がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-317">The local view supports creating these specific collection types:</span></span>

- <span data-ttu-id="9b3d9-318"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType><xref:System.Collections.ObjectModel.ObservableCollection%601>WPF データバインディングのを返します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-318"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> returns an <xref:System.Collections.ObjectModel.ObservableCollection%601> for WPF data binding.</span></span>
- <span data-ttu-id="9b3d9-319"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType><xref:System.ComponentModel.BindingList%601>Windows フォームデータバインディングのを返します。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-319"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> returns a <xref:System.ComponentModel.BindingList%601> for Windows Forms data binding.</span></span>

<span data-ttu-id="9b3d9-320">例:</span><span class="sxs-lookup"><span data-stu-id="9b3d9-320">For example:</span></span>

<!--
        ObservableCollection<Post> observableCollection = context.Posts.Local.ToObservableCollection();
        BindingList<Post> bindingList = context.Posts.Local.ToBindingList();
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_4](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_4)]

<span data-ttu-id="9b3d9-321">EF Core による WPF のデータバインディングの詳細については、「 [wpf の概要](xref:core/get-started/wpf) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-321">See [Get Started with WPF](xref:core/get-started/wpf) for more information on WPF data binding with EF Core.</span></span>

> [!TIP]
> <span data-ttu-id="9b3d9-322">特定の DbSet インスタンスのローカルビューは、最初にアクセスされてからキャッシュされるときに、遅延して作成されます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-322">The local view for a given DbSet instance is created lazily when first accessed and then cached.</span></span> <span data-ttu-id="9b3d9-323">LocalView の作成自体は高速で、大量のメモリは使用されません。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-323">LocalView creation itself is fast and it does not use significant memory.</span></span> <span data-ttu-id="9b3d9-324">ただし、検出された [変更](xref:core/change-tracking/change-detection)は、大量のエンティティの処理速度が低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-324">However, it does call [DetectChanges](xref:core/change-tracking/change-detection), which can be slow for large numbers of entities.</span></span> <span data-ttu-id="9b3d9-325">およびによって作成されたコレクション `ToObservableCollection` `ToBindingList` も、遅延してキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-325">The collections created by `ToObservableCollection` and `ToBindingList` are also created lazily and and then cached.</span></span> <span data-ttu-id="9b3d9-326">これらの方法では、新しいコレクションが作成されます。これにより、何千ものエンティティが関係しているときに、処理速度が遅く、大量のメモリを使用する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9b3d9-326">Both of these methods create new collections, which can be slow and use a lot of memory when thousands of entities are involved.</span></span>
