---
title: 追跡と追跡なしのクエリ - EF Core
author: smitpatel
ms.date: 10/10/2019
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
uid: core/querying/tracking
ms.openlocfilehash: 66988f936ab75e17620398c8f21e4a32bbc950bd
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445957"
---
# <a name="tracking-vs-no-tracking-queries"></a><span data-ttu-id="35add-102">追跡と追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="35add-102">Tracking vs. No-Tracking Queries</span></span>

<span data-ttu-id="35add-103">Entity Framework Core によってその変更トラッカー内のエンティティ インスタンスに関する情報が保持されるかどうかは、追跡動作によって制御されます。</span><span class="sxs-lookup"><span data-stu-id="35add-103">Tracking behavior controls if Entity Framework Core will keep information about an entity instance in its change tracker.</span></span> <span data-ttu-id="35add-104">エンティティが追跡されている場合、エンティティ内で検出された変更はすべて、`SaveChanges()` の間にデータベースに対して永続化されます。</span><span class="sxs-lookup"><span data-stu-id="35add-104">If an entity is tracked, any changes detected in the entity will be persisted to the database during `SaveChanges()`.</span></span> <span data-ttu-id="35add-105">また、EF Core により、追跡クエリの結果内のエンティティ、および変更トラッカーに含まれるエンティティの間のナビゲーション プロパティも修正されます。</span><span class="sxs-lookup"><span data-stu-id="35add-105">EF Core will also fix up navigation properties between the entities in a tracking query result and the entities that are in the change tracker.</span></span>

> [!NOTE]
> <span data-ttu-id="35add-106">[キーなしエンティティ型](xref:core/modeling/keyless-entity-types)は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="35add-106">[Keyless entity types](xref:core/modeling/keyless-entity-types) are never tracked.</span></span> <span data-ttu-id="35add-107">この記事でエンティティ型に関する言及がある場合は、必ずキーが定義されているエンティティ型を参照しています。</span><span class="sxs-lookup"><span data-stu-id="35add-107">Wherever this article mentions entity types, it refers to entity types which have a key defined.</span></span>

> [!TIP]  
> <span data-ttu-id="35add-108">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="35add-108">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="tracking-queries"></a><span data-ttu-id="35add-109">追跡クエリ</span><span class="sxs-lookup"><span data-stu-id="35add-109">Tracking queries</span></span>

<span data-ttu-id="35add-110">既定では、エンティティ型を返すクエリは、追跡を行います。</span><span class="sxs-lookup"><span data-stu-id="35add-110">By default, queries that return entity types are tracking.</span></span> <span data-ttu-id="35add-111">つまり、それらのエンティティ インスタンスに変更を加え、`SaveChanges()` によってその変更を永続化させることができます。</span><span class="sxs-lookup"><span data-stu-id="35add-111">Which means you can make changes to those entity instances and have those changes persisted by `SaveChanges()`.</span></span> <span data-ttu-id="35add-112">次の例では、ブログ評価に対する変更が検出され、`SaveChanges()` の間にデータベースに対して永続化されています。</span><span class="sxs-lookup"><span data-stu-id="35add-112">In the following example, the change to the blogs rating will be detected and persisted to the database during `SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#Tracking)]

## <a name="no-tracking-queries"></a><span data-ttu-id="35add-113">追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="35add-113">No-tracking queries</span></span>

<span data-ttu-id="35add-114">追跡なしのクエリは、読み取り専用のシナリオで結果が使用される場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="35add-114">No tracking queries are useful when the results are used in a read-only scenario.</span></span> <span data-ttu-id="35add-115">これらは、変更の追跡情報を設定する必要がないため、より高速に実行できます。</span><span class="sxs-lookup"><span data-stu-id="35add-115">They're quicker to execute because there's no need to set up the change tracking information.</span></span> <span data-ttu-id="35add-116">データベースから取得されたエンティティを更新する必要がない場合は、追跡なしのクエリを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="35add-116">If you don't need to update the entities retrieved from the database, then a no-tracking query should be used.</span></span> <span data-ttu-id="35add-117">個別のクエリをスワップして、追跡なしにできます。</span><span class="sxs-lookup"><span data-stu-id="35add-117">You can swap an individual query to be no-tracking.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#NoTracking)]

<span data-ttu-id="35add-118">また、コンテキスト インスタンスのレベルで、既定の追跡動作を変更できます。</span><span class="sxs-lookup"><span data-stu-id="35add-118">You can also change the default tracking behavior at the context instance level:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a><span data-ttu-id="35add-119">識別子の解決</span><span class="sxs-lookup"><span data-stu-id="35add-119">Identity resolution</span></span>

<span data-ttu-id="35add-120">追跡クエリでは変更トラッカーが使用されます。そのため、EF Core では追跡クエリで ID 解決が実行されます。</span><span class="sxs-lookup"><span data-stu-id="35add-120">Since a tracking query uses the change tracker, EF Core will do identity resolution in a tracking query.</span></span> <span data-ttu-id="35add-121">エンティティを具体化するとき、それが既に追跡されている場合は、EF Core によって変更トラッカーから同じエンティティ インスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="35add-121">When materializing an entity, EF Core will return the same entity instance from the change tracker if it's already being tracked.</span></span> <span data-ttu-id="35add-122">結果に同じエンティティが複数回含まれている場合は、そのたびに同じインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="35add-122">If the result contains same entity multiple times, you get back same instance for each occurrence.</span></span> <span data-ttu-id="35add-123">追跡なしのクエリでは変更トラッカーが使用されず、ID 解決は実行されません。</span><span class="sxs-lookup"><span data-stu-id="35add-123">No-tracking queries don't use the change tracker and don't do identity resolution.</span></span> <span data-ttu-id="35add-124">そのため、同じエンティティが結果に複数回含まれている場合でも、エンティティの新しいインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="35add-124">So you get back new instance of entity even when the same entity is contained in the result multiple times.</span></span> <span data-ttu-id="35add-125">この動作は、EF Core 3.0 以前のバージョンでは異なります (「[以前のバージョン](#previous-versions)」を参照)。</span><span class="sxs-lookup"><span data-stu-id="35add-125">This behavior was different in versions before EF Core 3.0, see [previous versions](#previous-versions).</span></span>

## <a name="tracking-and-custom-projections"></a><span data-ttu-id="35add-126">追跡とカスタム プロジェクション</span><span class="sxs-lookup"><span data-stu-id="35add-126">Tracking and custom projections</span></span>

<span data-ttu-id="35add-127">クエリの結果の型がエンティティ型ではない場合でも、EF Core では既定で、結果に含まれているエンティティ型が引き続き追跡されます。</span><span class="sxs-lookup"><span data-stu-id="35add-127">Even if the result type of the query isn't an entity type, EF Core will still track entity types contained in the result by default.</span></span> <span data-ttu-id="35add-128">次のクエリでは、匿名の型が返され、結果セット内で `Blog` のインスタンスが追跡されます。</span><span class="sxs-lookup"><span data-stu-id="35add-128">In the following query, which returns an anonymous type, the instances of `Blog` in the result set will be tracked.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection1)]

<span data-ttu-id="35add-129">結果セットに LINQ の合成に由来するエンティティ型が含まれていた場合は、EF Core によってそれらが追跡されます。</span><span class="sxs-lookup"><span data-stu-id="35add-129">If the result set contains entity types coming out from LINQ composition, EF Core will track them.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

<span data-ttu-id="35add-130">結果セットにいかなるエンティティ型も含まれていなかった場合は、追跡なしが実行されます。</span><span class="sxs-lookup"><span data-stu-id="35add-130">If the result set doesn't contain any entity types, then no tracking is done.</span></span> <span data-ttu-id="35add-131">次のクエリでは、エンティティの値の一部を含む (ただし実際のエンティティ型のインスタンスは含まない) 匿名型が返されます。</span><span class="sxs-lookup"><span data-stu-id="35add-131">In the following query, we return an anonymous type with some of the values from the entity (but no instances of the actual entity type).</span></span> <span data-ttu-id="35add-132">クエリから生じた追跡対象のエンティティはありません。</span><span class="sxs-lookup"><span data-stu-id="35add-132">There are no tracked entities coming out of the query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection3)]

 <span data-ttu-id="35add-133">EF Core では、最上位レベルのプロジェクションでクライアント評価を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="35add-133">EF Core supports doing client evaluation in the top-level projection.</span></span> <span data-ttu-id="35add-134">クライアント評価のために、EF Core によりエンティティ インスタンスが具体化された場合、それは追跡されます。</span><span class="sxs-lookup"><span data-stu-id="35add-134">If EF Core materializes an entity instance for client evaluation, it will be tracked.</span></span> <span data-ttu-id="35add-135">ここでは、`blog` エンティティがクライアント メソッド `StandardizeURL` に渡されているため、EF Core によって blog インスタンスも追跡されます。</span><span class="sxs-lookup"><span data-stu-id="35add-135">Here, since we're passing `blog` entities to the client method `StandardizeURL`, EF Core will track the blog instances too.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientMethod)]

<span data-ttu-id="35add-136">EF Core では、結果に含まれているキーなしのエンティティ インスタンスは追跡されません。</span><span class="sxs-lookup"><span data-stu-id="35add-136">EF Core doesn't track the keyless entity instances contained in the result.</span></span> <span data-ttu-id="35add-137">ただし、キーを持つその他のすべてのエンティティ型のインスタンスは、上記のルールに従って、EF Core によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="35add-137">But EF Core tracks all the other instances of entity types with key according to rules above.</span></span>

<span data-ttu-id="35add-138">上記のルールの一部は、EF Core 3.0 以前では異なった動作をします。</span><span class="sxs-lookup"><span data-stu-id="35add-138">Some of the above rules worked differently before EF Core 3.0.</span></span> <span data-ttu-id="35add-139">詳細については、「[以前のバージョン](#previous-versions)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="35add-139">For more information, see [previous versions](#previous-versions).</span></span>

## <a name="previous-versions"></a><span data-ttu-id="35add-140">以前のバージョン</span><span class="sxs-lookup"><span data-stu-id="35add-140">Previous versions</span></span>

<span data-ttu-id="35add-141">バージョン 3.0 より前の EF Core では、追跡の実行方法にいくつかの違いがありました。</span><span class="sxs-lookup"><span data-stu-id="35add-141">Before version 3.0, EF Core had some differences in how tracking was done.</span></span> <span data-ttu-id="35add-142">注意すべき違いは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="35add-142">Notable differences are as follows:</span></span>

- <span data-ttu-id="35add-143">「[クライアントとサーバーの評価](xref:core/querying/client-eval)」ページで説明されているように、バージョン 3.0 より前の EF Core では、クエリのあらゆる部分でのクライアント評価がサポートされていました。</span><span class="sxs-lookup"><span data-stu-id="35add-143">As explained in [Client vs Server Evaluation](xref:core/querying/client-eval) page, EF Core supported client evaluation in any part of the query before version 3.0.</span></span> <span data-ttu-id="35add-144">クライアント評価によってエンティティの具体化が発生し、それは結果に含まれていませんでした。</span><span class="sxs-lookup"><span data-stu-id="35add-144">Client evaluation caused materialization of entities, which weren't part of the result.</span></span> <span data-ttu-id="35add-145">そのため、EF Core では、追跡対象を検出するために、その結果が分析されていました。この設計には、次のようないくつかの相違点があります。</span><span class="sxs-lookup"><span data-stu-id="35add-145">So EF Core analyzed the result to detect what to track. This design had certain differences as follows:</span></span>
  - <span data-ttu-id="35add-146">プロジェクションでのクライアント評価は、具体化を発生させましたが、具体化されたエンティティ インスタンスは返されず、追跡されませんでした。</span><span class="sxs-lookup"><span data-stu-id="35add-146">Client evaluation in the projection, which caused materialization but didn't return the materialized entity instance wasn't tracked.</span></span> <span data-ttu-id="35add-147">次の例では、`blog` エンティティが追跡されませんでした。</span><span class="sxs-lookup"><span data-stu-id="35add-147">The following example didn't track `blog` entities.</span></span>
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#ClientProjection)]

  - <span data-ttu-id="35add-148">場合によっては、EF Core では LINQ の合成に由来するオブジェクトが追跡されませんでした。</span><span class="sxs-lookup"><span data-stu-id="35add-148">EF Core didn't track the objects coming out of LINQ composition in certain cases.</span></span> <span data-ttu-id="35add-149">次の例では、`Post` が追跡されませんでした。</span><span class="sxs-lookup"><span data-stu-id="35add-149">The following example didn't track `Post`.</span></span>
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Sample.cs#CustomProjection2)]

- <span data-ttu-id="35add-150">クエリ結果にキーなしエンティティ型が含まれていた場合は、常にクエリ全体が追跡なしになりました。</span><span class="sxs-lookup"><span data-stu-id="35add-150">Whenever query results contained keyless entity types, the whole query was made non-tracking.</span></span> <span data-ttu-id="35add-151">つまり、結果に含まれている、キーを持つエンティティ型も追跡されませんでした。</span><span class="sxs-lookup"><span data-stu-id="35add-151">That means that entity types with keys, which are in result weren't being tracked either.</span></span>
- <span data-ttu-id="35add-152">EF Core により、追跡なしのクエリで ID 解決が実行されました。</span><span class="sxs-lookup"><span data-stu-id="35add-152">EF Core did identity resolution in no-tracking query.</span></span> <span data-ttu-id="35add-153">既に返されているエンティティを追跡するために、弱参照が使用されました。</span><span class="sxs-lookup"><span data-stu-id="35add-153">It used weak references to keep track of entities that had already been returned.</span></span> <span data-ttu-id="35add-154">そのため、結果セットに同じエンティティが複数回含まれていた場合は、そのたびに同じインスタンスが返されました。</span><span class="sxs-lookup"><span data-stu-id="35add-154">So if a result set contained the same entity multiples times, you would get the same instance for each occurrence.</span></span> <span data-ttu-id="35add-155">同じ ID を持つ前の結果がスコープから外れ、ガベージ コレクションが行われても、EF Core により新しいインスタンスが返されました。</span><span class="sxs-lookup"><span data-stu-id="35add-155">Though if a previous result with the same identity went out of scope and got garbage collected, EF Core returned a new instance.</span></span>
