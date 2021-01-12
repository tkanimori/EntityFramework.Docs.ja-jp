---
title: Id 解決-EF Core
description: 主キー値を使用して複数のエンティティインスタンスを1つのインスタンスに解決する
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/identity-resolution
ms.openlocfilehash: f94b61371dcead27853799719dabc7849500d466
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129636"
---
# <a name="identity-resolution-in-ef-core"></a><span data-ttu-id="e0ce4-103">EF Core での id 解決</span><span class="sxs-lookup"><span data-stu-id="e0ce4-103">Identity Resolution in EF Core</span></span>

<span data-ttu-id="e0ce4-104">は、 <xref:Microsoft.EntityFrameworkCore.DbContext> 特定の主キー値を持つ1つのエンティティインスタンスだけを追跡できます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-104">A <xref:Microsoft.EntityFrameworkCore.DbContext> can only track one entity instance with any given primary key value.</span></span> <span data-ttu-id="e0ce4-105">これは、同じキー値を持つエンティティの複数のインスタンスを1つのインスタンスに解決する必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-105">This means multiple instances of an entity with the same key value must be resolved to a single instance.</span></span> <span data-ttu-id="e0ce4-106">これは "id 解決" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-106">This is called "identity resolution".</span></span> <span data-ttu-id="e0ce4-107">Id 解決によって Entity Framework Core (EF Core) は、エンティティのリレーションシップやプロパティ値に関するあいまいさのない一貫したグラフを追跡します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-107">Identity resolution ensures Entity Framework Core (EF Core) is tracking a consistent graph with no ambiguities about the relationships or property values of the entities.</span></span>

> [!TIP]
> <span data-ttu-id="e0ce4-108">このドキュメントでは、EF Core の変更の追跡のエンティティの状態と基本について理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-108">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="e0ce4-109">これらのトピックの詳細については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-109">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="e0ce4-110">[GitHub からサンプルコードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore)することで、このドキュメントのすべてのコードを実行し、デバッグすることができます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-110">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore).</span></span>

## <a name="introduction"></a><span data-ttu-id="e0ce4-111">はじめに</span><span class="sxs-lookup"><span data-stu-id="e0ce4-111">Introduction</span></span>

<span data-ttu-id="e0ce4-112">次のコードでは、エンティティに対してクエリを実行し、同じ主キー値を持つ別のインスタンスをアタッチしようとしています。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-112">The following code queries for an entity and then attempts to attach a different instance with the same primary key value:</span></span>

<!--
            using var context = new BlogsContext();

            var blogA = context.Blogs.Single(e => e.Id == 1);
            var blogB = new Blog { Id = 1, Name = ".NET Blog (All new!)" };

            try
            {
                context.Update(blogB); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Identity_Resolution_in_EF_Core_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Identity_Resolution_in_EF_Core_1)]

<span data-ttu-id="e0ce4-113">このコードを実行すると、次の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-113">Running this code results in the following exception:</span></span>

> <span data-ttu-id="e0ce4-114">InvalidOperationException: キー値 ' {Id: 1} ' を持つ別のインスタンスが既に追跡されているため、エンティティ型 ' Blog ' のインスタンスを追跡できません。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-114">System.InvalidOperationException: The instance of entity type 'Blog' cannot be tracked because another instance with the key value '{Id: 1}' is already being tracked.</span></span> <span data-ttu-id="e0ce4-115">既存のエンティティをアタッチする場合は、特定のキー値を持つ1つのエンティティインスタンスのみがアタッチされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-115">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="e0ce4-116">次の理由により、EF Core には1つのインスタンスが必要です。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-116">EF Core requires a single instance because:</span></span>

- <span data-ttu-id="e0ce4-117">プロパティ値は、複数のインスタンス間で異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-117">Property values may be different between multiple instances.</span></span> <span data-ttu-id="e0ce4-118">データベースを更新するときは、使用するプロパティ値を EF Core が認識している必要があります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-118">When updating the database, EF Core needs to know which property values to use.</span></span>
- <span data-ttu-id="e0ce4-119">他のエンティティとのリレーションシップは、複数のインスタンス間で異なる場合があります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-119">Relationships with other entities may be different between multiple instances.</span></span> <span data-ttu-id="e0ce4-120">たとえば、"ブログ a" は、"ブログ b" とは異なる投稿のコレクションに関連付けられている場合があります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-120">For example, "blogA" may be related to a different collection of posts than "blogB".</span></span>

<span data-ttu-id="e0ce4-121">上記の例外は、通常、次のような状況で発生します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-121">The exception above is commonly encountered in these situations:</span></span>

- <span data-ttu-id="e0ce4-122">エンティティを更新しようとしたとき</span><span class="sxs-lookup"><span data-stu-id="e0ce4-122">When attempting to update an entity</span></span>
- <span data-ttu-id="e0ce4-123">シリアル化されたエンティティのグラフを追跡しようとすると</span><span class="sxs-lookup"><span data-stu-id="e0ce4-123">When attempting to track a serialized graph of entities</span></span>
- <span data-ttu-id="e0ce4-124">自動的に生成されないキー値の設定に失敗した場合</span><span class="sxs-lookup"><span data-stu-id="e0ce4-124">When failing to set a key value that is not automatically generated</span></span>
- <span data-ttu-id="e0ce4-125">複数の作業単位に対して DbContext インスタンスを再利用する場合</span><span class="sxs-lookup"><span data-stu-id="e0ce4-125">When reusing a DbContext instance for multiple units-of-work</span></span>

<span data-ttu-id="e0ce4-126">これらの各状況については、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-126">Each of these situations is discussed in the following sections.</span></span>

## <a name="updating-an-entity"></a><span data-ttu-id="e0ce4-127">エンティティの更新</span><span class="sxs-lookup"><span data-stu-id="e0ce4-127">Updating an entity</span></span>

<span data-ttu-id="e0ce4-128">エンティティを新しい値で更新する方法はいくつかあります。これについては [Change Tracking 「EF Core」](xref:core/change-tracking/index) と「 [明示的なエンティティの追跡](xref:core/change-tracking/explicit-tracking)」を確認してください。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-128">There are several different approaches to update an entity with new values, as covered in [Change Tracking in EF Core](xref:core/change-tracking/index) and [Explicitly Tracking Entities](xref:core/change-tracking/explicit-tracking).</span></span> <span data-ttu-id="e0ce4-129">これらのアプローチについては、id 解決のコンテキストで以下に説明します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-129">These approaches are outlined below in the context of identity resolution.</span></span> <span data-ttu-id="e0ce4-130">注意すべき重要な点は、各方法では、クエリを使用するか、またはのいずれかの呼び出しを使用し `Update` `Attach` ますが、 **_両方_** を使用しないことです。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-130">An important point to notice is that each of the approaches use either a query or a call to one of `Update` or `Attach`, but **_never both_**.</span></span>

### <a name="call-update"></a><span data-ttu-id="e0ce4-131">Update の呼び出し</span><span class="sxs-lookup"><span data-stu-id="e0ce4-131">Call Update</span></span>

<span data-ttu-id="e0ce4-132">多くの場合、更新するエンティティは、SaveChanges に使用する DbContext に対するクエリからは取得されません。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-132">Often the entity to update does not come from a query on the DbContext that we are going to use for SaveChanges.</span></span> <span data-ttu-id="e0ce4-133">たとえば、web アプリケーションで、POST 要求の情報からエンティティインスタンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-133">For example, in a web application, an entity instance may be created from the information in a POST request.</span></span> <span data-ttu-id="e0ce4-134">これを処理する最も簡単な方法は、またはを使用することです <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-134">The simplest way to handle this is to use <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> or <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e0ce4-135">例:</span><span class="sxs-lookup"><span data-stu-id="e0ce4-135">For example:</span></span>

<!--
    public static void UpdateFromHttpPost1(Blog blog)
    {
        using var context = new BlogsContext();

        context.Update(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_1)]

<span data-ttu-id="e0ce4-136">この場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-136">In this case:</span></span>

- <span data-ttu-id="e0ce4-137">エンティティのインスタンスは1つだけ作成されます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-137">Only a single instance of the entity is created.</span></span>
- <span data-ttu-id="e0ce4-138">エンティティインスタンスは、更新の実行の一環としてデータベースからは照会され **ません** 。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-138">The entity instance is **not** queried from the database as part of making the update.</span></span>
- <span data-ttu-id="e0ce4-139">実際に変更されたかどうかに関係なく、すべてのプロパティ値がデータベースで更新されます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-139">All property values will be updated in the database, regardless of whether they have actually changed or not.</span></span>
- <span data-ttu-id="e0ce4-140">1つのデータベースラウンドトリップが行われます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-140">One database round-trip is made.</span></span>

### <a name="query-then-apply-changes"></a><span data-ttu-id="e0ce4-141">クエリを実行し、変更を適用する</span><span class="sxs-lookup"><span data-stu-id="e0ce4-141">Query then apply changes</span></span>

<span data-ttu-id="e0ce4-142">通常、POST 要求の情報からエンティティが作成されるときに、どのプロパティ値が実際に変更されたかは不明です。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-142">Usually it is not known which property values have actually been changed when an entity is created from information in a POST request or similar.</span></span> <span data-ttu-id="e0ce4-143">多くの場合、前の例で行ったように、データベース内のすべての値を更新するだけで十分です。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-143">Often it is fine to just update all values in the database, as we did in the previous example.</span></span> <span data-ttu-id="e0ce4-144">ただし、アプリケーションが多数のエンティティを処理していて、それらの少数の部分だけが実際の変更を行っている場合は、送信される更新プログラムを制限すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-144">However, if the application is handling many entities and only a small number of those have actual changes, then it may be useful to limit the updates sent.</span></span> <span data-ttu-id="e0ce4-145">これを実現するには、クエリを実行して、データベース内に現在存在しているエンティティを追跡し、それらの追跡対象エンティティに変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-145">This can be achieved by executing a query to track the entities as they currently exist in the database, and then applying changes to these tracked entities.</span></span> <span data-ttu-id="e0ce4-146">例:</span><span class="sxs-lookup"><span data-stu-id="e0ce4-146">For example:</span></span>

<!--
    public static void UpdateFromHttpPost2(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        trackedBlog.Name = blog.Name;
        trackedBlog.Summary = blog.Summary;

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_2)]

<span data-ttu-id="e0ce4-147">この場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-147">In this case:</span></span>

- <span data-ttu-id="e0ce4-148">エンティティの1つのインスタンスのみが追跡されます。クエリによってデータベースから返されるものです `Find` 。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-148">Only a single instance of the entity is tracked; the one that is returned from the database by the `Find` query.</span></span>
- <span data-ttu-id="e0ce4-149">`Update`、 `Attach` 、などが使用され **ていません** 。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-149">`Update`, `Attach`, etc. are **not** used.</span></span>
- <span data-ttu-id="e0ce4-150">実際に変更されたプロパティ値だけがデータベースで更新されます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-150">Only property values that have actually changed are updated in the database.</span></span>
- <span data-ttu-id="e0ce4-151">2つのデータベースのラウンドトリップが行われます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-151">Two database round-trips are made.</span></span>

<span data-ttu-id="e0ce4-152">EF Core には、このようなプロパティ値を転送するためのヘルパーがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-152">EF Core has some helpers for transferring property values like this.</span></span> <span data-ttu-id="e0ce4-153">たとえば、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> は、指定されたオブジェクトからすべての値をコピーし、追跡対象のオブジェクトに設定します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-153">For example, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> will copy all the values from the given object and set them on the tracked object:</span></span>

<!--
    public static void UpdateFromHttpPost3(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_3)]

<span data-ttu-id="e0ce4-154">`SetValues` では、エンティティ型のプロパティと一致するプロパティ名を持つデータ転送オブジェクト (Dto) を含む、さまざまなオブジェクトの種類を使用できます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-154">`SetValues` accepts various object types, including data transfer objects (DTOs) with property names that match the properties of the entity type.</span></span> <span data-ttu-id="e0ce4-155">例:</span><span class="sxs-lookup"><span data-stu-id="e0ce4-155">For example:</span></span>

<!--
    public static void UpdateFromHttpPost4(BlogDto dto)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(dto.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(dto);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_4)]

<span data-ttu-id="e0ce4-156">または、プロパティ値の名前と値のエントリを含むディクショナリ。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-156">Or a dictionary with name/value entries for the property values:</span></span>

<!--
    public static void UpdateFromHttpPost5(Dictionary<string, object> propertyValues)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(propertyValues["Id"]);

        context.Entry(trackedBlog).CurrentValues.SetValues(propertyValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_5)]

<span data-ttu-id="e0ce4-157">このようなプロパティ値の使用方法の詳細については、「追跡対象の [エンティティへのアクセス](xref:core/change-tracking/entity-entries) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-157">See [Accessing tracked entities](xref:core/change-tracking/entity-entries) for more information on working with property values like this.</span></span>

### <a name="use-original-values"></a><span data-ttu-id="e0ce4-158">元の値を使用する</span><span class="sxs-lookup"><span data-stu-id="e0ce4-158">Use original values</span></span>

<span data-ttu-id="e0ce4-159">ここまでの手順では、更新を行う前にクエリを実行したか、変更されたかどうかに関係なくすべてのプロパティ値を更新しました。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-159">So far each approach has either executed a query before making the update, or updated all property values regardless of whether or not they have changed.</span></span> <span data-ttu-id="e0ce4-160">更新の一部としてクエリを実行せずに変更された値のみを更新するには、変更されたプロパティ値に関する特定の情報が必要です。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-160">To update only values that have changed without querying as part of the update requires specific information about which property values have changed.</span></span> <span data-ttu-id="e0ce4-161">この情報を取得する一般的な方法は、HTTP Post または同様に、現在の値と元の値の両方を返すことです。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-161">A common way to get this information is to send back both the current and original values in the HTTP Post or similar.</span></span> <span data-ttu-id="e0ce4-162">例:</span><span class="sxs-lookup"><span data-stu-id="e0ce4-162">For example:</span></span>

<!--
    public static void UpdateFromHttpPost6(Blog blog, Dictionary<string, object> originalValues)
    {
        using var context = new BlogsContext();

        context.Attach(blog);
        context.Entry(blog).OriginalValues.SetValues(originalValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_6](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_6)]

<span data-ttu-id="e0ce4-163">このコードでは、変更された値を持つエンティティが最初にアタッチされます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-163">In this code the entity with modified values is first attached.</span></span> <span data-ttu-id="e0ce4-164">これにより、EF Core は状態のエンティティを追跡します。 `Unchanged` つまり、プロパティ値が変更済みとしてマークされていません。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-164">This causes EF Core to track the entity in the `Unchanged` state; that is, with no property values marked as modified.</span></span> <span data-ttu-id="e0ce4-165">次に、元の値のディクショナリがこの追跡対象のエンティティに適用されます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-165">The dictionary of original values is then applied to this tracked entity.</span></span> <span data-ttu-id="e0ce4-166">これにより、現在の値と元の値が変更されたプロパティとしてマークが付けられます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-166">This will mark as modified properties with different current and original values.</span></span> <span data-ttu-id="e0ce4-167">現在の値と元の値が同じプロパティは、変更済みとしてマークされません。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-167">Properties that have the same current and original values will not be marked as modified.</span></span>

<span data-ttu-id="e0ce4-168">この場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-168">In this case:</span></span>

- <span data-ttu-id="e0ce4-169">アタッチを使用して、エンティティの1つのインスタンスのみが追跡されます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-169">Only a single instance of the entity is tracked, using Attach.</span></span>
- <span data-ttu-id="e0ce4-170">エンティティインスタンスは、更新の実行の一環としてデータベースからは照会され **ません** 。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-170">The entity instance is **not** queried from the database as part of making the update.</span></span>
- <span data-ttu-id="e0ce4-171">元の値を適用することで、実際に変更されたプロパティ値だけがデータベースで更新されるようになります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-171">Applying the original values ensures that only property values that have actually changed are updated in the database.</span></span>
- <span data-ttu-id="e0ce4-172">1つのデータベースラウンドトリップが行われます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-172">One database round-trip is made.</span></span>

<span data-ttu-id="e0ce4-173">前のセクションの例と同様に、元の値をディクショナリとして渡す必要はありません。エンティティインスタンスまたは DTO も機能します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-173">As with the examples in the previous section, the original values do not have to passed as a dictionary; an entity instance or DTO will also work.</span></span>

> [!TIP]
> <span data-ttu-id="e0ce4-174">このアプローチには魅力的な特性がありますが、エンティティの元の値を web クライアントとの間で送信する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-174">While this approach has appealing characteristics, it does require sending the entity's original values to and from the web client.</span></span> <span data-ttu-id="e0ce4-175">この追加の複雑さにメリットがあるかどうかを慎重に検討してください。多くのアプリケーションでは、より簡単な方法の1つがより実用的です。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-175">Carefully consider whether this extra complexity is worth the benefits; for many applications one of the simpler approaches is more pragmatic.</span></span>

## <a name="attaching-a-serialized-graph"></a><span data-ttu-id="e0ce4-176">シリアル化されたグラフのアタッチ</span><span class="sxs-lookup"><span data-stu-id="e0ce4-176">Attaching a serialized graph</span></span>

<span data-ttu-id="e0ce4-177">EF Core は、「 [外部キーと](xref:core/change-tracking/relationship-changes)ナビゲーションの変更」で説明されているように、外部キーおよびナビゲーションプロパティを介して接続されたエンティティのグラフを操作します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-177">EF Core works with graphs of entities connected via foreign keys and navigation properties, as described in [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes).</span></span> <span data-ttu-id="e0ce4-178">これらのグラフが、たとえば JSON ファイルからを使用して EF Core 以外で作成されている場合は、同じエンティティの複数のインスタンスを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-178">If these graphs are created outside of EF Core using, for example, from a JSON file, then they can have multiple instances of the same entity.</span></span> <span data-ttu-id="e0ce4-179">これらの重複は、グラフを追跡する前に1つのインスタンスに解決する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-179">These duplicates need to be resolved into single instances before the graph can be tracked.</span></span>

### <a name="graphs-with-no-duplicates"></a><span data-ttu-id="e0ce4-180">重複しないグラフ</span><span class="sxs-lookup"><span data-stu-id="e0ce4-180">Graphs with no duplicates</span></span>

<span data-ttu-id="e0ce4-181">さらに先に進む前に、次の点を認識しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-181">Before going any further it is important to recognize that:</span></span>

- <span data-ttu-id="e0ce4-182">多くの場合、シリアライザーには、グラフ内のループや重複するインスタンスを処理するオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-182">Serializers often have options for handling loops and duplicate instances in the graph.</span></span>
- <span data-ttu-id="e0ce4-183">グラフルートとして使用されるオブジェクトを選択すると、多くの場合、重複を減らしたり削除したりするのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-183">The choice of object used as the graph root can often help reduce or remove duplicates.</span></span>

<span data-ttu-id="e0ce4-184">可能な場合は、シリアル化オプションを使用して、重複しないルートを選択します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-184">If possible, use serialization options and choose roots that do not result in duplicates.</span></span> <span data-ttu-id="e0ce4-185">たとえば、次のコードでは、 [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) を使用して、ブログのリストをそれぞれに関連付けられた投稿と共にシリアル化しています。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-185">For example, the following code uses [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) to serialize a list of blogs each with its associated posts:</span></span>

<!--
            using var context = new BlogsContext();

            var blogs = context.Blogs.Include(e => e.Posts).ToList();

            var serialized = JsonConvert.SerializeObject(
                blogs,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_1a](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1a)]

<span data-ttu-id="e0ce4-186">このコードから生成される JSON は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-186">The JSON generated from this code is:</span></span>

```json
[
  {
    "Id": 1,
    "Name": ".NET Blog",
    "Summary": "Posts about .NET",
    "Posts": [
      {
        "Id": 1,
        "Title": "Announcing the Release of EF Core 5.0",
        "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
        "BlogId": 1
      },
      {
        "Id": 2,
        "Title": "Announcing F# 5",
        "Content": "F# 5 is the latest version of F#, the functional programming language...",
        "BlogId": 1
      }
    ]
  },
  {
    "Id": 2,
    "Name": "Visual Studio Blog",
    "Summary": "Posts about Visual Studio",
    "Posts": [
      {
        "Id": 3,
        "Title": "Disassembly improvements for optimized managed debugging",
        "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
        "BlogId": 2
      },
      {
        "Id": 4,
        "Title": "Database Profiling with Visual Studio",
        "Content": "Examine when database queries were executed and measure how long the take using...",
        "BlogId": 2
      }
    ]
  }
]
```

<span data-ttu-id="e0ce4-187">JSON には、重複するブログや投稿がないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-187">Notice that there are no duplicate blogs or posts in the JSON.</span></span> <span data-ttu-id="e0ce4-188">これは、への単純な呼び出しに `Update` よって、データベース内のこれらのエンティティが更新されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-188">This means that simple calls to `Update` will work to update these entities in the database:</span></span>

<!--
        public static void UpdateBlogsFromJson(string json)
        {
            using var context = new BlogsContext();

            var blogs = JsonConvert.DeserializeObject<List<Blog>>(json);

            foreach (var blog in blogs)
            {
                context.Update(blog);
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_1b](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1b)]

### <a name="handling-duplicates"></a><span data-ttu-id="e0ce4-189">重複の処理</span><span class="sxs-lookup"><span data-stu-id="e0ce4-189">Handling duplicates</span></span>

<span data-ttu-id="e0ce4-190">前の例のコードでは、各ブログに関連する投稿をシリアル化しています。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-190">The code in the previous example serialized each blog with its associated posts.</span></span> <span data-ttu-id="e0ce4-191">これが、関連付けられているブログと共に各投稿をシリアル化するように変更された場合は、シリアル化された JSON に重複が導入されます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-191">If this is changed to serialize each post with its associated blog, then duplicates are introduced into the serialized JSON.</span></span> <span data-ttu-id="e0ce4-192">例:</span><span class="sxs-lookup"><span data-stu-id="e0ce4-192">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var posts = context.Posts.Include(e => e.Blog).ToList();

            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_2)]

<span data-ttu-id="e0ce4-193">シリアル化された JSON は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-193">The serialized JSON now looks like this:</span></span>

```json
[
  {
    "Id": 1,
    "Title": "Announcing the Release of EF Core 5.0",
    "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 2,
          "Title": "Announcing F# 5",
          "Content": "F# 5 is the latest version of F#, the functional programming language...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 2,
    "Title": "Announcing F# 5",
    "Content": "F# 5 is the latest version of F#, the functional programming language...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 1,
          "Title": "Announcing the Release of EF Core 5.0",
          "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 3,
    "Title": "Disassembly improvements for optimized managed debugging",
    "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 4,
          "Title": "Database Profiling with Visual Studio",
          "Content": "Examine when database queries were executed and measure how long the take using...",
          "BlogId": 2
        }
      ]
    }
  },
  {
    "Id": 4,
    "Title": "Database Profiling with Visual Studio",
    "Content": "Examine when database queries were executed and measure how long the take using...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 3,
          "Title": "Disassembly improvements for optimized managed debugging",
          "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
          "BlogId": 2
        }
      ]
    }
  }
]
```

<span data-ttu-id="e0ce4-194">グラフに、同じキー値を持つ複数のブログインスタンスと、同じキー値を持つ複数の Post インスタンスが含まれるようになったことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-194">Notice that the graph now includes multiple Blog instances with the same key value, as well as multiple Post instance with the same key value.</span></span> <span data-ttu-id="e0ce4-195">前の例のようにこのグラフを追跡しようとすると、がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-195">Attempting to track this graph like we did in the previous example will throw:</span></span>

> <span data-ttu-id="e0ce4-196">InvalidOperationException: キー値 ' {Id: 2} ' を持つ別のインスタンスが既に追跡されているため、エンティティ型 ' Post ' のインスタンスを追跡できません。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-196">System.InvalidOperationException: The instance of entity type 'Post' cannot be tracked because another instance with the key value '{Id: 2}' is already being tracked.</span></span> <span data-ttu-id="e0ce4-197">既存のエンティティをアタッチする場合は、特定のキー値を持つ1つのエンティティインスタンスのみがアタッチされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-197">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="e0ce4-198">これを解決するには、次の2つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-198">We can fix this in two ways:</span></span>

- <span data-ttu-id="e0ce4-199">参照を保持する JSON シリアル化オプションを使用する</span><span class="sxs-lookup"><span data-stu-id="e0ce4-199">Use JSON serialization options that preserve references</span></span>
- <span data-ttu-id="e0ce4-200">グラフの追跡中に id 解決を実行する</span><span class="sxs-lookup"><span data-stu-id="e0ce4-200">Perform identity resolution while the graph is being tracked</span></span>

#### <a name="preserve-references"></a><span data-ttu-id="e0ce4-201">参照を保持する</span><span class="sxs-lookup"><span data-stu-id="e0ce4-201">Preserve references</span></span>

<span data-ttu-id="e0ce4-202">Json.NET は、 `PreserveReferencesHandling` これを処理するオプションを提供します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-202">Json.NET provides the `PreserveReferencesHandling` option to handle this.</span></span> <span data-ttu-id="e0ce4-203">例:</span><span class="sxs-lookup"><span data-stu-id="e0ce4-203">For example:</span></span>

<!--
            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    PreserveReferencesHandling = PreserveReferencesHandling.All,
                    Formatting = Formatting.Indented
                });
-->
[!code-csharp[Attaching_a_serialized_graph_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_3)]

<span data-ttu-id="e0ce4-204">結果の JSON は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-204">The resulting JSON now looks like this:</span></span>

```json
{
  "$id": "1",
  "$values": [
    {
      "$id": "2",
      "Id": 1,
      "Title": "Announcing the Release of EF Core 5.0",
      "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
      "BlogId": 1,
      "Blog": {
        "$id": "3",
        "Id": 1,
        "Name": ".NET Blog",
        "Summary": "Posts about .NET",
        "Posts": [
          {
            "$ref": "2"
          },
          {
            "$id": "4",
            "Id": 2,
            "Title": "Announcing F# 5",
            "Content": "F# 5 is the latest version of F#, the functional programming language...",
            "BlogId": 1,
            "Blog": {
              "$ref": "3"
            }
          }
        ]
      }
    },
    {
      "$ref": "4"
    },
    {
      "$id": "5",
      "Id": 3,
      "Title": "Disassembly improvements for optimized managed debugging",
      "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
      "BlogId": 2,
      "Blog": {
        "$id": "6",
        "Id": 2,
        "Name": "Visual Studio Blog",
        "Summary": "Posts about Visual Studio",
        "Posts": [
          {
            "$ref": "5"
          },
          {
            "$id": "7",
            "Id": 4,
            "Title": "Database Profiling with Visual Studio",
            "Content": "Examine when database queries were executed and measure how long the take using...",
            "BlogId": 2,
            "Blog": {
              "$ref": "6"
            }
          }
        ]
      }
    },
    {
      "$ref": "7"
    }
  ]
}
```

<span data-ttu-id="e0ce4-205">この JSON では `"$ref": "5"` 、グラフ内の既存のインスタンスを参照するのような参照で重複部分が置換されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-205">Notice that this JSON has replaced duplicates with references like `"$ref": "5"` that refer to the already existing instance in the graph.</span></span> <span data-ttu-id="e0ce4-206">このグラフは、上に示すように、の単純な呼び出しを使用して追跡でき `Update` ます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-206">This graph can again be tracked using the simple calls to `Update`, as shown above.</span></span>

<span data-ttu-id="e0ce4-207"><xref:System.Text.Json>.Net 基底クラスライブラリ (BCL) でのサポートには同様のオプションがあるため、同じ結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-207">The <xref:System.Text.Json> support in the .NET base class libraries (BCL) has a similar option which produces the same result.</span></span> <span data-ttu-id="e0ce4-208">例:</span><span class="sxs-lookup"><span data-stu-id="e0ce4-208">For example:</span></span>

<!--
            var serialized = System.Text.Json.JsonSerializer.Serialize(posts, new System.Text.Json.JsonSerializerOptions
            {
                ReferenceHandler = ReferenceHandler.Preserve,
                WriteIndented = true
            });
-->
[!code-csharp[Attaching_a_serialized_graph_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_4)]

#### <a name="resolve-duplicates"></a><span data-ttu-id="e0ce4-209">重複の解決</span><span class="sxs-lookup"><span data-stu-id="e0ce4-209">Resolve duplicates</span></span>

<span data-ttu-id="e0ce4-210">シリアル化プロセスで重複を排除できない場合は、を使用してその <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> 処理方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-210">If it is not possible to eliminate duplicates in the serialization process, then <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> provides a way to handle this.</span></span> <span data-ttu-id="e0ce4-211">TrackGraph はと同様に動作 `Add` `Attach` し `Update` ますが、追跡する前にすべてのエンティティインスタンスに対してコールバックが生成される点が異なります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-211">TrackGraph works like `Add`, `Attach` and `Update` except that it generates a callback for every entity instance before tracking it.</span></span> <span data-ttu-id="e0ce4-212">このコールバックを使用して、エンティティを追跡するか無視することができます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-212">This callback can be used to either track the entity or ignore it.</span></span> <span data-ttu-id="e0ce4-213">例:</span><span class="sxs-lookup"><span data-stu-id="e0ce4-213">For example:</span></span>

<!--
        public static void UpdatePostsFromJsonWithIdentityResolution(string json)
        {
            using var context = new BlogsContext();

            var posts = JsonConvert.DeserializeObject<List<Post>>(json);

            foreach (var post in posts)
            {
                context.ChangeTracker.TrackGraph(
                    post, node =>
                        {
                            var keyValue = node.Entry.Property("Id").CurrentValue;
                            var entityType = node.Entry.Metadata;

                            var existingEntity = node.Entry.Context.ChangeTracker.Entries()
                                .FirstOrDefault(
                                    e => Equals(e.Metadata, entityType)
                                         && Equals(e.Property("Id").CurrentValue, keyValue));

                            if (existingEntity == null)
                            {
                                Console.WriteLine($"Tracking {entityType} entity with key value {keyValue}");

                                node.Entry.State = EntityState.Modified;
                            }
                            else
                            {
                                Console.WriteLine($"Discarding duplicate {entityType} entity with key value {keyValue}");
                            }
                        });
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_5)]

<span data-ttu-id="e0ce4-214">グラフ内の各エンティティに対して、次のコードが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-214">For each entity in the graph, this code will:</span></span>

- <span data-ttu-id="e0ce4-215">エンティティのエンティティ型とキー値を検索する</span><span class="sxs-lookup"><span data-stu-id="e0ce4-215">Find the entity type and key value of the entity</span></span>
- <span data-ttu-id="e0ce4-216">変更トラッカーでこのキーを使用してエンティティを参照します</span><span class="sxs-lookup"><span data-stu-id="e0ce4-216">Lookup the entity with this key in the change tracker</span></span>
  - <span data-ttu-id="e0ce4-217">エンティティが見つかった場合は、エンティティが重複しているため、それ以上のアクションは実行されません。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-217">If the entity is found, then no further action is taken as the entity is a duplicate</span></span>
  - <span data-ttu-id="e0ce4-218">エンティティが見つからない場合は、状態をに設定して追跡します。 `Modified`</span><span class="sxs-lookup"><span data-stu-id="e0ce4-218">If the entity is not found, then it is tracked by setting the state to `Modified`</span></span>

<span data-ttu-id="e0ce4-219">このコードを実行した場合の出力は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-219">The output from running this code is:</span></span>

```output
Tracking EntityType: Post entity with key value 1
Tracking EntityType: Blog entity with key value 1
Tracking EntityType: Post entity with key value 2
Discarding duplicate EntityType: Post entity with key value 2
Tracking EntityType: Post entity with key value 3
Tracking EntityType: Blog entity with key value 2
Tracking EntityType: Post entity with key value 4
Discarding duplicate EntityType: Post entity with key value 4
```

> [!IMPORTANT]
> <span data-ttu-id="e0ce4-220">このコード **は、すべての重複が同一であることを前提として** います。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-220">This code **assumes that all duplicates are identical**.</span></span> <span data-ttu-id="e0ce4-221">これにより、他のものを破棄しながら、任意の複製を追跡することが安全です。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-221">This makes it safe to arbitrarily choose one of the duplicates to track while discarding the others.</span></span> <span data-ttu-id="e0ce4-222">重複が異なる場合は、コードで使用するものを決定する方法、およびプロパティとナビゲーション値を組み合わせて使用する方法を決定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-222">If the duplicates can differ, then the code will need to decide how to determine which one to use, and how to combine property and navigation values together.</span></span>

> [!NOTE]
> <span data-ttu-id="e0ce4-223">わかりやすくするために、このコードでは、各エンティティにという主キープロパティがあることを前提としてい `Id` ます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-223">For simplicity, this code assumes each entity has a primary key property called `Id`.</span></span> <span data-ttu-id="e0ce4-224">これは、抽象基本クラスまたはインターフェイスに体系化ことができます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-224">This could be codified into an abstract base class or interface.</span></span> <span data-ttu-id="e0ce4-225">または、 <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> このコードがあらゆる種類のエンティティで動作するように、メタデータから主キーのプロパティを取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-225">Alternately, the primary key property or properties could be obtained from the <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata such that this code would work with any type of entity.</span></span>

## <a name="failing-to-set-key-values"></a><span data-ttu-id="e0ce4-226">キー値の設定に失敗する</span><span class="sxs-lookup"><span data-stu-id="e0ce4-226">Failing to set key values</span></span>

<span data-ttu-id="e0ce4-227">エンティティ型は、多くの場合、 [自動的に生成されたキー値](xref:core/modeling/generated-properties)を使用するように構成されます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-227">Entity types are often configured to use [automatically generated key values](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="e0ce4-228">これは、非複合キーの整数および GUID プロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-228">This is the default for integer and GUID properties of non-composite keys.</span></span> <span data-ttu-id="e0ce4-229">ただし、エンティティ型が自動生成されたキー値を使用するように構成されていない場合は、エンティティを追跡する前に明示的なキー値を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-229">However, if the entity type is not configured to use automatically generated key values, then an explicit key value must be set before tracking the entity.</span></span> <span data-ttu-id="e0ce4-230">たとえば、次のエンティティ型を使用します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-230">For example, using the following entity type:</span></span>

<!--
    public class Pet
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }
    }
-->
[!code-csharp[Pet](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Pet)]

<span data-ttu-id="e0ce4-231">キー値を設定せずに2つの新しいエンティティインスタンスを追跡するコードを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-231">Consider code that attempts to tracker two new entity instances without setting key values:</span></span>

<!--
            using var context = new BlogsContext();

            context.Add(new Pet { Name = "Smokey" });

            try
            {
                context.Add(new Pet { Name = "Clippy" }); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Failing_to_set_key_values_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Failing_to_set_key_values_1)]

<span data-ttu-id="e0ce4-232">このコードでは、次のことがスローされます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-232">This code will throw:</span></span>

> <span data-ttu-id="e0ce4-233">InvalidOperationException: キー値 ' {Id: 0} ' を持つ別のインスタンスが既に追跡されているため、エンティティ型 ' Pet ' のインスタンスを追跡できません。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-233">System.InvalidOperationException: The instance of entity type 'Pet' cannot be tracked because another instance with the key value '{Id: 0}' is already being tracked.</span></span> <span data-ttu-id="e0ce4-234">既存のエンティティをアタッチする場合は、特定のキー値を持つ1つのエンティティインスタンスのみがアタッチされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-234">When attaching existing entities, ensure that only one entity instance with a given key value is attached.</span></span>

<span data-ttu-id="e0ce4-235">この問題を解決するには、キー値を明示的に設定するか、生成されたキー値を使用するようにキープロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-235">The fix for this is to either to set key values explicitly or configure the key property to use generated key values.</span></span> <span data-ttu-id="e0ce4-236">詳細については、「 [生成される値](xref:core/modeling/generated-properties) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-236">See [Generated Values](xref:core/modeling/generated-properties) for more information.</span></span>

## <a name="overusing-a-single-dbcontext-instance"></a><span data-ttu-id="e0ce4-237">単一の DbContext インスタンスの乱用</span><span class="sxs-lookup"><span data-stu-id="e0ce4-237">Overusing a single DbContext instance</span></span>

<span data-ttu-id="e0ce4-238"><xref:Microsoft.EntityFrameworkCore.DbContext> は、 [Dbcontext の初期化と構成](xref:core/dbcontext-configuration/index)に関するページで説明されているように、有効期間の短い作業単位を表すように設計されており、 [EF Core の Change Tracking](xref:core/change-tracking/index)で詳しく説明しています。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-238"><xref:Microsoft.EntityFrameworkCore.DbContext> is designed to represent a short-lived unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index), and elaborated on in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span> <span data-ttu-id="e0ce4-239">このガイダンスに従っていないと、同じエンティティの複数のインスタンスを追跡しようとした場合に、簡単に実行できるようになります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-239">Not following this guidance makes it is easy to run into situations where an attempt is made to track multiple instances of the same entity.</span></span> <span data-ttu-id="e0ce4-240">一般的な例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-240">Common examples are:</span></span>

- <span data-ttu-id="e0ce4-241">同じ DbContext インスタンスを使用して、テスト状態を設定し、テストを実行します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-241">Using the same DbContext instance to both set up test state and then execute the test.</span></span> <span data-ttu-id="e0ce4-242">これにより、多くの場合、DbContext はテストセットアップから1つのエンティティインスタンスを追跡し、次にテストで新しいインスタンスを適切にアタッチしようとします。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-242">This often results in the DbContext still tracking one entity instance from test setup, while then attempting to attach a new instance in the test proper.</span></span> <span data-ttu-id="e0ce4-243">代わりに、別の DbContext インスタンスを使用して、テストの状態とテストコードを適切に設定します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-243">Instead, use a different DbContext instance for setting up test state and the test code proper.</span></span>
- <span data-ttu-id="e0ce4-244">リポジトリまたは同様のコードでの共有 DbContext インスタンスの使用。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-244">Using a shared DbContext instance in a repository or similar code.</span></span> <span data-ttu-id="e0ce4-245">代わりに、各作業単位に対して1つの DbContext インスタンスを使用していることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-245">Instead, make sure your repository uses a single DbContext instance for each unit-of-work.</span></span>

## <a name="identity-resolution-and-queries"></a><span data-ttu-id="e0ce4-246">Id の解決とクエリ</span><span class="sxs-lookup"><span data-stu-id="e0ce4-246">Identity resolution and queries</span></span>

<span data-ttu-id="e0ce4-247">Id 解決は、エンティティがクエリから追跡されるときに自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-247">Identity resolution happens automatically when entities are tracked from a query.</span></span> <span data-ttu-id="e0ce4-248">これは、特定のキー値を持つエンティティインスタンスが既に追跡されている場合に、新しいインスタンスを作成する代わりに、この既存の追跡対象インスタンスが使用されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-248">This means that if an entity instance with a given key value is already tracked, then this existing tracked instance is used instead of creating a new instance.</span></span> <span data-ttu-id="e0ce4-249">これには重要な結果があります。データベースでデータが変更された場合、クエリの結果には反映されません。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-249">This has an important consequence: if the data has changed in the database, then this will not be reflected in the results of the query.</span></span> <span data-ttu-id="e0ce4-250">これは、「 [Dbcontext の初期化と構成](xref:core/dbcontext-configuration/index)」で説明されているように、作業単位ごとに新しい dbcontext インスタンスを使用し、 [EF Core の Change Tracking](xref:core/change-tracking/index)で詳細を使用する理由です。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-250">This is angood reason to use a new DbContext instance for each unit-of-work, as described in [DbContext Initialization and Configuration](xref:core/dbcontext-configuration/index), and elaborated on in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e0ce4-251">EF Core は常にデータベースに対して DbSet に対して LINQ クエリを実行し、データベースの内容に基づいてのみ結果を返すことを理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-251">It is important to understand that EF Core always executes a LINQ query on a DbSet against the database and only returns results based on what is in the database.</span></span> <span data-ttu-id="e0ce4-252">ただし、追跡クエリの場合、返されたエンティティが既に追跡されている場合は、データベース内のデータからインスタンスを作成する代わりに、追跡対象のインスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-252">However, for a tracking query, if the entities returned are already tracked, then the tracked instances are used instead of creating a instances from the data in the database.</span></span>

<span data-ttu-id="e0ce4-253"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> またはは、追跡対象の <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> エンティティをデータベースの最新データで更新する必要がある場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-253"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> or <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> can be used when tracked entities need to be refreshed with the latest data from the database.</span></span> <span data-ttu-id="e0ce4-254">詳細については、「 [追跡対象エンティティへのアクセス](xref:core/change-tracking/entity-entries) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-254">See [Accessing Tracked Entities](xref:core/change-tracking/entity-entries) for more information.</span></span>

<span data-ttu-id="e0ce4-255">クエリの追跡とは異なり、追跡しないクエリでは、id 解決は実行されません。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-255">In contrast to tracking queries, no-tracking queries do not perform identity resolution.</span></span> <span data-ttu-id="e0ce4-256">これは、前に説明した JSON シリアル化の場合と同じように、追跡なしのクエリが重複を返すことができることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-256">This means that no-tracking queries can return duplicates just like in the JSON serialization case described earlier.</span></span> <span data-ttu-id="e0ce4-257">これは、クエリ結果をシリアル化してクライアントに送信する場合、通常は問題ではありません。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-257">This is usually not an issue if the query results are going to be serialized and sent to the client.</span></span>

> [!TIP]
> <span data-ttu-id="e0ce4-258">定期的に追跡しないクエリを実行し、返されたエンティティを同じコンテキストにアタッチすることは避けてください。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-258">Do not routinely perform a no-tracking query and then attach the returned entities to the same context.</span></span> <span data-ttu-id="e0ce4-259">これは、追跡クエリを使用するよりも処理速度が遅く、困難になることがあります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-259">This will be both slower and harder to get right than using a tracking query.</span></span>

<span data-ttu-id="e0ce4-260">追跡なしのクエリは、クエリから多数のエンティティをストリーミングする場合のパフォーマンスに影響するため、id 解決を実行しません。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-260">No-tracking queries do not perform identity resolution because doing so impacts the performance of streaming a large number of entities from a query.</span></span> <span data-ttu-id="e0ce4-261">これは、id 解決によって返される各インスタンスを追跡し、後で複製を作成するのではなく使用できるようにするためです。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-261">This is because identity resolution requires keeping track of each instance returned so that it can be used instead of later creating a duplicate.</span></span>

<span data-ttu-id="e0ce4-262">EF Core 5.0 以降では、を使用して、追跡なしのクエリで id 解決を強制的に実行でき <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})> ます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-262">Starting with EF Core 5.0, no-tracking queries can be forced to perform identity resolution by using <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})>.</span></span> <span data-ttu-id="e0ce4-263">次に、返されたインスタンス (通常の方法では追跡せずに) を追跡し、クエリ結果に重複が作成されないようにします。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-263">The query will then keep track of returned instances (without tracking them in the normal way) and ensure no duplicates are created in the query results.</span></span>

## <a name="overriding-object-equality"></a><span data-ttu-id="e0ce4-264">オーバーライド (オブジェクトの等価性を)</span><span class="sxs-lookup"><span data-stu-id="e0ce4-264">Overriding object equality</span></span>

<span data-ttu-id="e0ce4-265">EF Core は、エンティティインスタンスを比較するときに [参照の等価性](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) を使用します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-265">EF Core uses [reference equality](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) when comparing entity instances.</span></span> <span data-ttu-id="e0ce4-266">これは、エンティティ型がオーバーライドする場合や、オブジェクトの等価性を変更する場合にも当てはまり <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-266">This is the case even if the entity types override <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> or otherwise change object equality.</span></span> <span data-ttu-id="e0ce4-267">ただし、等価性のオーバーライドが EF Core の動作に影響を与える可能性があります。コレクションのナビゲーションでは、参照の等価性ではなくオーバーライドされた等値が使用され、そのために複数のインスタンスが同じものとしてレポートされます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-267">However, there is one place where overriding equality can impact EF Core behavior: when collection navigations use the overridden equality instead of reference equality, and hence report multiple instances as the same.</span></span>

<span data-ttu-id="e0ce4-268">このため、エンティティの等価性を上書きしないようにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-268">Because of this it is recommended that overriding entity equality should be avoided.</span></span> <span data-ttu-id="e0ce4-269">使用する場合は、参照の等価性を強制するコレクションナビゲーションを作成してください。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-269">If it is used, then make sure to create collection navigations that force reference equality.</span></span> <span data-ttu-id="e0ce4-270">たとえば、参照の等価性を使用する等値比較子を作成します。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-270">For example, create an equality comparer that uses reference equality:</span></span>

<!--
    public sealed class ReferenceEqualityComparer : IEqualityComparer<object>
    {
        private ReferenceEqualityComparer()
        {
        }

        public static ReferenceEqualityComparer Instance { get; } = new ReferenceEqualityComparer();

        bool IEqualityComparer<object>.Equals(object x, object y) => x == y;

        int IEqualityComparer<object>.GetHashCode(object obj) => RuntimeHelpers.GetHashCode(obj);
    }
-->
[!code-csharp[ReferenceEqualityComparer](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/ReferenceEqualityComparer.cs?name=ReferenceEqualityComparer)]

<span data-ttu-id="e0ce4-271">(.NET 5 以降では、これはとして BCL に含まれてい <xref:System.Collections.Generic.ReferenceEqualityComparer> ます)。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-271">(Starting with .NET 5, this is included in the BCL as <xref:System.Collections.Generic.ReferenceEqualityComparer>.)</span></span>

<span data-ttu-id="e0ce4-272">この比較子は、コレクションナビゲーションを作成するときに使用できます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-272">This comparer can then be used when creating collection navigations.</span></span> <span data-ttu-id="e0ce4-273">例:</span><span class="sxs-lookup"><span data-stu-id="e0ce4-273">For example:</span></span>

<!--
        public ICollection<Order> Orders { get; set; }
            = new HashSet<Order>(ReferenceEqualityComparer.Instance);
-->
[!code-csharp[OrdersCollection](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=OrdersCollection)]

### <a name="comparing-key-properties"></a><span data-ttu-id="e0ce4-274">キープロパティの比較</span><span class="sxs-lookup"><span data-stu-id="e0ce4-274">Comparing key properties</span></span>

<span data-ttu-id="e0ce4-275">等価比較に加えて、キー値も順序付けする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-275">In addition to equality comparisons, key values also need to be ordered.</span></span> <span data-ttu-id="e0ce4-276">これは、SaveChanges の1回の呼び出しで複数のエンティティを更新するときに、デッドロックを回避するために重要です。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-276">This is important for avoiding deadlocks when updating multiple entities in a single call to SaveChanges.</span></span> <span data-ttu-id="e0ce4-277">プライマリ、代替、または外部キーのプロパティ、および一意のインデックスに使用されるすべての型は、およびを実装する必要があり <xref:System.IComparable%601> <xref:System.IEquatable%601> ます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-277">All types used for primary, alternate, or foreign key properties, as well as those used for unique indexes, must implement <xref:System.IComparable%601> and <xref:System.IEquatable%601>.</span></span> <span data-ttu-id="e0ce4-278">通常、キーとして使用される型 (int、Guid、文字列など) は、これらのインターフェイスを既にサポートしています。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-278">Types normally used as keys (int, Guid, string, etc.) already support these interfaces.</span></span> <span data-ttu-id="e0ce4-279">カスタムキーの種類では、これらのインターフェイスを追加できます。</span><span class="sxs-lookup"><span data-stu-id="e0ce4-279">Custom key types may to add these interfaces.</span></span>
