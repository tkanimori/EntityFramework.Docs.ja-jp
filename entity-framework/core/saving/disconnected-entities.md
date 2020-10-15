---
title: 接続解除エンティティ - EF Core
description: Entity Framework Core の複数のコンテキスト インスタンスにわたって存在する追跡されていない接続解除エンティティの操作
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/disconnected-entities
ms.openlocfilehash: f21cc71ef11ee4ef37618a68d5a5219e8174bf8b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063570"
---
# <a name="disconnected-entities"></a><span data-ttu-id="203e1-103">接続解除エンティティ</span><span class="sxs-lookup"><span data-stu-id="203e1-103">Disconnected entities</span></span>

<span data-ttu-id="203e1-104">DbContext インスタンスでは、データベースから返されるエンティティを自動的に追跡します。</span><span class="sxs-lookup"><span data-stu-id="203e1-104">A DbContext instance will automatically track entities returned from the database.</span></span> <span data-ttu-id="203e1-105">必要に応じて SaveChanges が呼び出され、データベースが更新されたときに、これらのエンティティに対して行われた変更が検出されます。</span><span class="sxs-lookup"><span data-stu-id="203e1-105">Changes made to these entities will then be detected when SaveChanges is called and the database will be updated as needed.</span></span> <span data-ttu-id="203e1-106">詳細については、「[Basic Save](xref:core/saving/basic)」(基本の保存) および「[Related Data](xref:core/saving/related-data)」(関連データ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="203e1-106">See [Basic Save](xref:core/saving/basic) and [Related Data](xref:core/saving/related-data) for details.</span></span>

<span data-ttu-id="203e1-107">しかし、エンティティが 1 つのコンテキスト インスタンスを使って照会され、別のインスタンスを使って保存される場合があります。</span><span class="sxs-lookup"><span data-stu-id="203e1-107">However, sometimes entities are queried using one context instance and then saved using a different instance.</span></span> <span data-ttu-id="203e1-108">これは、エンティティの照会、クライアントへの送信、変更、要求内でのサーバーへの返送、および保存が行われる Web アプリケーションなどの "接続解除" シナリオで、頻繁に発生します。</span><span class="sxs-lookup"><span data-stu-id="203e1-108">This often happens in "disconnected" scenarios such as a web application where the entities are queried, sent to the client, modified, sent back to the server in a request, and then saved.</span></span> <span data-ttu-id="203e1-109">この場合、2 番目のコンテキスト インスタンスでは、エンティティが新しいか (挿入する必要がある) または既存か (更新する必要があるか) を把握する必要があります。</span><span class="sxs-lookup"><span data-stu-id="203e1-109">In this case, the second context instance needs to know whether the entities are new (should be inserted) or existing (should be updated).</span></span>

<!-- markdownlint-disable MD028 -->
> [!TIP]
> <span data-ttu-id="203e1-110">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="203e1-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) on GitHub.</span></span>

> [!TIP]
> <span data-ttu-id="203e1-111">EF Core では、指定されたプライマリ キー値を持つ任意のエンティティの 1 つのインスタンスしか追跡できません。</span><span class="sxs-lookup"><span data-stu-id="203e1-111">EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="203e1-112">これを回避する最善の方法は、各作業単位に一時的なコンテキストを使用して、最初は空のコンテキストにエンティティをアタッチし、それらのエンティティを保存してから、コンテキストが消去および破棄されるようにすることです。</span><span class="sxs-lookup"><span data-stu-id="203e1-112">The best way to avoid this being an issue is to use a short-lived context for each unit-of-work such that the context starts empty, has entities attached to it, saves those entities, and then the context is disposed and discarded.</span></span>
<!-- markdownlint-enable MD028 -->

## <a name="identifying-new-entities"></a><span data-ttu-id="203e1-113">新しいエンティティの識別</span><span class="sxs-lookup"><span data-stu-id="203e1-113">Identifying new entities</span></span>

### <a name="client-identifies-new-entities"></a><span data-ttu-id="203e1-114">クライアントが新しいエンティティを識別する</span><span class="sxs-lookup"><span data-stu-id="203e1-114">Client identifies new entities</span></span>

<span data-ttu-id="203e1-115">最も処理しやすいのは、エンティティが新規か既存かをクライアントからサーバーに知らせるタイミングです。</span><span class="sxs-lookup"><span data-stu-id="203e1-115">The simplest case to deal with is when the client informs the server whether the entity is new or existing.</span></span> <span data-ttu-id="203e1-116">たとえば、新しいエンティティを挿入するための要求は、多くの場合、既存のエンティティを更新するための要求とは異なります。</span><span class="sxs-lookup"><span data-stu-id="203e1-116">For example, often the request to insert a new entity is different from the request to update an existing entity.</span></span>

<span data-ttu-id="203e1-117">このセクションの以降の説明では、別の方法で挿入か更新かを判断する必要があるケースを取り上げます。</span><span class="sxs-lookup"><span data-stu-id="203e1-117">The remainder of this section covers the cases where it necessary to determine in some other way whether to insert or update.</span></span>

### <a name="with-auto-generated-keys"></a><span data-ttu-id="203e1-118">自動生成キーを利用する</span><span class="sxs-lookup"><span data-stu-id="203e1-118">With auto-generated keys</span></span>

<span data-ttu-id="203e1-119">自動生成されたキーの値は、エンティティを挿入する必要があるか、または更新する必要があるかを判断するために使用されることが、よくあります。</span><span class="sxs-lookup"><span data-stu-id="203e1-119">The value of an automatically generated key can often be used to determine whether an entity needs to be inserted or updated.</span></span> <span data-ttu-id="203e1-120">キーが設定済みでない (つまり、null や 0 などの CLR 既定値をまだ保持している) 場合、エンティティは常に新規となり、挿入する必要があります。</span><span class="sxs-lookup"><span data-stu-id="203e1-120">If the key has not been set (that is, it still has the CLR default value of null, zero, etc.), then the entity must be new and needs inserting.</span></span> <span data-ttu-id="203e1-121">一方、キー値が設定済みの場合、エンティティは常に既に事前保存済みであり、更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="203e1-121">On the other hand, if the key value has been set, then it must have already been previously saved and now needs updating.</span></span> <span data-ttu-id="203e1-122">つまり、キーが値を保持している場合、エンティティは照会され、クライアントに送信されて、更新されるようになります。</span><span class="sxs-lookup"><span data-stu-id="203e1-122">In other words, if the key has a value, then the entity was queried, sent to the client, and has now come back to be updated.</span></span>

<span data-ttu-id="203e1-123">エンティティ型がわかっている場合、設定されていないキーのチェックは簡単です。</span><span class="sxs-lookup"><span data-stu-id="203e1-123">It is easy to check for an unset key when the entity type is known:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewSimple)]

<span data-ttu-id="203e1-124">しかし、EF でもエンティティ型やキーの種類をチェックするための組み込みの方法を備えています。</span><span class="sxs-lookup"><span data-stu-id="203e1-124">However, EF also has a built-in way to do this for any entity type and key type:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> <span data-ttu-id="203e1-125">エンティティがコンテキストによって追跡されると、エンティティが追加済みの状態になっていても、すぐにキーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="203e1-125">Keys are set as soon as entities are tracked by the context, even if the entity is in the Added state.</span></span> <span data-ttu-id="203e1-126">これは、TrackGraph API を使用している場合など、エンティティのグラフを走査して各グラフで行う操作を決定する際に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="203e1-126">This helps when traversing a graph of entities and deciding what to do with each, such as when using the TrackGraph API.</span></span> <span data-ttu-id="203e1-127">キー値は、エンティティを追跡するために何らかの呼び出しが行われる "_前_" に、ここに示された方法でのみ使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="203e1-127">The key value should only be used in the way shown here _before_ any call is made to track the entity.</span></span>

### <a name="with-other-keys"></a><span data-ttu-id="203e1-128">他のキーを利用する</span><span class="sxs-lookup"><span data-stu-id="203e1-128">With other keys</span></span>

<span data-ttu-id="203e1-129">キー値が自動生成されない場合、他のいくつかのメカニズムが、新しいエンティティの識別に利用されます。</span><span class="sxs-lookup"><span data-stu-id="203e1-129">Some other mechanism is needed to identify new entities when key values are not generated automatically.</span></span> <span data-ttu-id="203e1-130">これを行うための一般的な方法として、次の 2 つがあります。</span><span class="sxs-lookup"><span data-stu-id="203e1-130">There are two general approaches to this:</span></span>

* <span data-ttu-id="203e1-131">エンティティに対してクエリを実行する</span><span class="sxs-lookup"><span data-stu-id="203e1-131">Query for the entity</span></span>
* <span data-ttu-id="203e1-132">クライアントからフラグを渡す</span><span class="sxs-lookup"><span data-stu-id="203e1-132">Pass a flag from the client</span></span>

<span data-ttu-id="203e1-133">エンティティに対してクエリを実行するには、単純に Find メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="203e1-133">To query for the entity, just use the Find method:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewQuery)]

<span data-ttu-id="203e1-134">クライアントからフラグを渡す完全なコードの提示は、このドキュメントでは行いません。</span><span class="sxs-lookup"><span data-stu-id="203e1-134">It is beyond the scope of this document to show the full code for passing a flag from a client.</span></span> <span data-ttu-id="203e1-135">Web アプリでは、多くの場合、さまざまなアクションに対して異なる要求を行うか、または要求内で何らかの状態を渡して、コントローラーでその状態を抽出することになります。</span><span class="sxs-lookup"><span data-stu-id="203e1-135">In a web app, it usually means making different requests for different actions, or passing some state in the request then extracting it in the controller.</span></span>

## <a name="saving-single-entities"></a><span data-ttu-id="203e1-136">単一のエンティティを保存する</span><span class="sxs-lookup"><span data-stu-id="203e1-136">Saving single entities</span></span>

<span data-ttu-id="203e1-137">挿入または更新のどちらが必要かがわかったら、次のように Add または Update を適切に使用できます。</span><span class="sxs-lookup"><span data-stu-id="203e1-137">If it is known whether or not an insert or update is needed, then either Add or Update can be used appropriately:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

<span data-ttu-id="203e1-138">ただし、エンティティが自動生成されたキー値を使用する場合は、両方のケースで Update メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="203e1-138">However, if the entity uses auto-generated key values, then the Update method can be used for both cases:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

<span data-ttu-id="203e1-139">Update メソッドは通常、挿入ではなく、更新用のエンティティをマークします。</span><span class="sxs-lookup"><span data-stu-id="203e1-139">The Update method normally marks the entity for update, not insert.</span></span> <span data-ttu-id="203e1-140">ただし、エンティティが自動生成されたキーを保持しており、設定済みのキー値がない場合、そのエンティティは更新用ではなく、自動的に挿入用にマークされます。</span><span class="sxs-lookup"><span data-stu-id="203e1-140">However, if the entity has a auto-generated key, and no key value has been set, then the entity is instead automatically marked for insert.</span></span>

<span data-ttu-id="203e1-141">エンティティが自動生成されたキーを使用していない場合、アプリケーションでは、エンティティの挿入または更新のどちらが必要かを、次の例のように判断する必要があります。</span><span class="sxs-lookup"><span data-stu-id="203e1-141">If the entity is not using auto-generated keys, then the application must decide whether the entity should be inserted or updated: For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

<span data-ttu-id="203e1-142">この場合の手順は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="203e1-142">The steps here are:</span></span>

* <span data-ttu-id="203e1-143">Find で null が返された場合、データベースがこの ID のブログを既に含んでいるわけではないため、Add を呼び出して挿入用にマークします。</span><span class="sxs-lookup"><span data-stu-id="203e1-143">If Find returns null, then the database doesn't already contain the blog with this ID, so we call Add mark it for insertion.</span></span>
* <span data-ttu-id="203e1-144">Find がエンティティを返した場合は、エンティティがデータベースに存在しており、コンテキストは既存のエンティティを追跡するようになります。</span><span class="sxs-lookup"><span data-stu-id="203e1-144">If Find returns an entity, then it exists in the database and the context is now tracking the existing entity</span></span>
  * <span data-ttu-id="203e1-145">SetValues を使用して、このエンティティ上のすべてのプロパティの値を、クライアントから受信したプロパティに設定します。</span><span class="sxs-lookup"><span data-stu-id="203e1-145">We then use SetValues to set the values for all properties on this entity to those that came from the client.</span></span>
  * <span data-ttu-id="203e1-146">SetValues の呼び出しでは、必要に応じて更新されるエンティティをマークします。</span><span class="sxs-lookup"><span data-stu-id="203e1-146">The SetValues call will mark the entity to be updated as needed.</span></span>

> [!TIP]  
> <span data-ttu-id="203e1-147">SetValues は、追跡されたエンティティのプロパティに別の値を保持しているプロパティを、変更済みとしてマークすることしか行いません。</span><span class="sxs-lookup"><span data-stu-id="203e1-147">SetValues will only mark as modified the properties that have different values to those in the tracked entity.</span></span> <span data-ttu-id="203e1-148">これは、更新が送信されると、実際に変更されたそれらの列のみが更新されることを意味します </span><span class="sxs-lookup"><span data-stu-id="203e1-148">This means that when the update is sent, only those columns that have actually changed will be updated.</span></span> <span data-ttu-id="203e1-149">(また、何も変更されなかった場合は、更新もまったく送信されません)。</span><span class="sxs-lookup"><span data-stu-id="203e1-149">(And if nothing has changed, then no update will be sent at all.)</span></span>

## <a name="working-with-graphs"></a><span data-ttu-id="203e1-150">グラフを操作する</span><span class="sxs-lookup"><span data-stu-id="203e1-150">Working with graphs</span></span>

### <a name="identity-resolution"></a><span data-ttu-id="203e1-151">識別子の解決</span><span class="sxs-lookup"><span data-stu-id="203e1-151">Identity resolution</span></span>

<span data-ttu-id="203e1-152">上述したように、EF Core では、指定されたプライマリ キー値を持つ任意のエンティティの 1 つのインスタンスしか追跡できません。</span><span class="sxs-lookup"><span data-stu-id="203e1-152">As noted above, EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="203e1-153">グラフを操作するとき、この不変の条件が維持されるようにグラフが理想的に作成される必要があり、コンテキストは 1 つの作業単位のみに対して使用される必要があります。</span><span class="sxs-lookup"><span data-stu-id="203e1-153">When working with graphs the graph should ideally be created such that this invariant is maintained, and the context should be used for only one unit-of-work.</span></span> <span data-ttu-id="203e1-154">グラフに重複値が含まれている場合、EF に送信して複数のインスタンスを 1 つに統合する前に、そのグラフを処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="203e1-154">If the graph does contain duplicates, then it will be necessary to process the graph before sending it to EF to consolidate multiple instances into one.</span></span> <span data-ttu-id="203e1-155">インスタンスに競合する値やリレーションシップがある場合、この処理が複雑になるので、アプリケーションのパイプラインで競合の解決が発生しないように、できるだけすぐに複数の値の統合を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="203e1-155">This may not be trivial where instances have conflicting values and relationships, so consolidating duplicates should be done as soon as possible in your application pipeline to avoid conflict resolution.</span></span>

### <a name="all-newall-existing-entities"></a><span data-ttu-id="203e1-156">すべての新規/既存のエンティティ</span><span class="sxs-lookup"><span data-stu-id="203e1-156">All new/all existing entities</span></span>

<span data-ttu-id="203e1-157">グラフの操作の例では、関連する投稿のコレクションと共にブログを挿入または更新しています。</span><span class="sxs-lookup"><span data-stu-id="203e1-157">An example of working with graphs is inserting or updating a blog together with its collection of associated posts.</span></span> <span data-ttu-id="203e1-158">グラフ内のすべてのエンティティが挿入される必要がある場合、またはすべてが更新される必要がある場合、プロセスは上述した単一のエンティティの場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="203e1-158">If all the entities in the graph should be inserted, or all should be updated, then the process is the same as described above for single entities.</span></span> <span data-ttu-id="203e1-159">たとえば、作成されたブログと投稿のグラフは次のよになります。</span><span class="sxs-lookup"><span data-stu-id="203e1-159">For example, a graph of blogs and posts created like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

<span data-ttu-id="203e1-160">また、次のように挿入できます。</span><span class="sxs-lookup"><span data-stu-id="203e1-160">can be inserted like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertGraph)]

<span data-ttu-id="203e1-161">Add の呼び出しでは、ブログとすべての投稿が挿入されるようにマークします。</span><span class="sxs-lookup"><span data-stu-id="203e1-161">The call to Add will mark the blog and all the posts to be inserted.</span></span>

<span data-ttu-id="203e1-162">同様に、グラフ内のすべてのエンティティが更新される必要がある場合は、Update を使用できます。</span><span class="sxs-lookup"><span data-stu-id="203e1-162">Likewise, if all the entities in a graph need to be updated, then Update can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#UpdateGraph)]

<span data-ttu-id="203e1-163">ブログとそのすべての投稿が、更新されるようにマークされます。</span><span class="sxs-lookup"><span data-stu-id="203e1-163">The blog and all its posts will be marked to be updated.</span></span>

### <a name="mix-of-new-and-existing-entities"></a><span data-ttu-id="203e1-164">新規と既存のエンティティの混合</span><span class="sxs-lookup"><span data-stu-id="203e1-164">Mix of new and existing entities</span></span>

<span data-ttu-id="203e1-165">自動生成されたキーを使用する場合、挿入を必要とするエンティティと更新を必要とするエンティティがグラフ内に混在していても、挿入と更新の両方に Update を再使用できます。</span><span class="sxs-lookup"><span data-stu-id="203e1-165">With auto-generated keys, Update can again be used for both inserts and updates, even if the graph contains a mix of entities that require inserting and those that require updating:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

<span data-ttu-id="203e1-166">Update は、設定されたキー値がない場合は、グラフ、ブログ、または投稿内のエンティティを挿入用にマークし、その他のエンティティはすべて更新用にマークします。</span><span class="sxs-lookup"><span data-stu-id="203e1-166">Update will mark any entity in the graph, blog or post, for insertion if it does not have a key value set, while all other entities are marked for update.</span></span>

<span data-ttu-id="203e1-167">前述のように、自動生成キーを使用しない場合は、クエリおよびいくつかの処理を使用できます。</span><span class="sxs-lookup"><span data-stu-id="203e1-167">As before, when not using auto-generated keys, a query and some processing can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a><span data-ttu-id="203e1-168">削除の処理</span><span class="sxs-lookup"><span data-stu-id="203e1-168">Handling deletes</span></span>

<span data-ttu-id="203e1-169">エンティティの不在は、エンティティが削除されていることを意味することがよくあるため、削除は慎重に扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="203e1-169">Delete can be tricky to handle since often the absence of an entity means that it should be deleted.</span></span> <span data-ttu-id="203e1-170">これに対処する方法の 1 つは、エンティティが実際に削除されるのではなく、削除としてマークされるように、"論理的な削除" を使用することです。</span><span class="sxs-lookup"><span data-stu-id="203e1-170">One way to deal with this is to use "soft deletes" such that the entity is marked as deleted rather than actually being deleted.</span></span> <span data-ttu-id="203e1-171">これで、削除は更新と同様になります。</span><span class="sxs-lookup"><span data-stu-id="203e1-171">Deletes then becomes the same as updates.</span></span> <span data-ttu-id="203e1-172">論理的な削除は、[クエリ フィルター](xref:core/querying/filters)を使用して実装できます。</span><span class="sxs-lookup"><span data-stu-id="203e1-172">Soft deletes can be implemented in using [query filters](xref:core/querying/filters).</span></span>

<span data-ttu-id="203e1-173">実際の削除では、一般的なパターンとしてクエリ パターンの拡張機能を使用して、本質的なグラフの差分特定を行います。</span><span class="sxs-lookup"><span data-stu-id="203e1-173">For true deletes, a common pattern is to use an extension of the query pattern to perform what is essentially a graph diff.</span></span> <span data-ttu-id="203e1-174">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="203e1-174">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a><span data-ttu-id="203e1-175">TrackGraph</span><span class="sxs-lookup"><span data-stu-id="203e1-175">TrackGraph</span></span>

<span data-ttu-id="203e1-176">内部的には、Add、Attach、および Update では、追加済み (挿入する)、変更済み (更新する)、変更なし (何もしない)、または削除済み (削除) としてマークする必要があるかどうかに関して各エンティティで行われる判断と共に、グラフ走査を使用します。</span><span class="sxs-lookup"><span data-stu-id="203e1-176">Internally, Add, Attach, and Update use graph-traversal with a determination made for each entity as to whether it should be marked as Added (to insert), Modified (to update), Unchanged (do nothing), or Deleted (to delete).</span></span> <span data-ttu-id="203e1-177">このメカニズムは TrackGraph API 経由で公開されています。</span><span class="sxs-lookup"><span data-stu-id="203e1-177">This mechanism is exposed via the TrackGraph API.</span></span> <span data-ttu-id="203e1-178">たとえば、クライアントがエンティティのグラフを返送するときに、処理方法を示したフラグが各エンティティ上に設定されていると仮定しましょう。</span><span class="sxs-lookup"><span data-stu-id="203e1-178">For example, let's assume that when the client sends back a graph of entities it sets some flag on each entity indicating how it should be handled.</span></span> <span data-ttu-id="203e1-179">このフラグを処理するために、次のように TrackGraph を使用できます。</span><span class="sxs-lookup"><span data-stu-id="203e1-179">TrackGraph can then be used to process this flag:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#TrackGraph)]

<span data-ttu-id="203e1-180">例を簡単にするために、フラグはエンティティの一部分としてしか表示されていません。</span><span class="sxs-lookup"><span data-stu-id="203e1-180">The flags are only shown as part of the entity for simplicity of the example.</span></span> <span data-ttu-id="203e1-181">フラグは通常、DTO の一部か、または要求に含まれている他の状態になります。</span><span class="sxs-lookup"><span data-stu-id="203e1-181">Typically the flags would be part of a DTO or some other state included in the request.</span></span>
