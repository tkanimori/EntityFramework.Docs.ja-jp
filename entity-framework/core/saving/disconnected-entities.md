---
title: 接続解除エンティティ - EF Core
author: ajcvickers
ms.author: avickers
ms.date: 10/27/2016
ms.assetid: 2533b195-d357-4056-b0e0-8698971bc3b0
uid: core/saving/disconnected-entities
ms.openlocfilehash: 51367d2619b1943c300f8954123f70b909ad96e7
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994400"
---
# <a name="disconnected-entities"></a><span data-ttu-id="59a2d-102">接続解除エンティティ</span><span class="sxs-lookup"><span data-stu-id="59a2d-102">Disconnected entities</span></span>

<span data-ttu-id="59a2d-103">DbContext インスタンスでは、データベースから返されるエンティティを自動的に追跡します。</span><span class="sxs-lookup"><span data-stu-id="59a2d-103">A DbContext instance will automatically track entities returned from the database.</span></span> <span data-ttu-id="59a2d-104">必要に応じて SaveChanges が呼び出され、データベースが更新されたときに、これらのエンティティに対して行われた変更が検出されます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-104">Changes made to these entities will then be detected when SaveChanges is called and the database will be updated as needed.</span></span> <span data-ttu-id="59a2d-105">詳細については、「[Basic Save](basic.md)」(基本の保存) および「[Related Data](related-data.md)」(関連データ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="59a2d-105">See [Basic Save](basic.md) and [Related Data](related-data.md) for details.</span></span>

<span data-ttu-id="59a2d-106">しかし、エンティティが 1 つのコンテキスト インスタンスを使って照会され、別のインスタンスを使って保存される場合があります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-106">However, sometimes entities are queried using one context instance and then saved using a different instance.</span></span> <span data-ttu-id="59a2d-107">これは、エンティティの照会、クライアントへの送信、変更、要求内でのサーバーへの返送、および保存が行われる Web アプリケーションなどの "接続解除" シナリオで、頻繁に発生します。</span><span class="sxs-lookup"><span data-stu-id="59a2d-107">This often happens in "disconnected" scenarios such as a web application where the entities are queried, sent to the client, modified, sent back to the server in a request, and then saved.</span></span> <span data-ttu-id="59a2d-108">この場合、2 番目のコンテキスト インスタンスでは、エンティティが新しいか (挿入する必要がある) または既存か (更新する必要があるか) を把握する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-108">In this case, the second context instance needs to know whether the entities are new (should be inserted) or existing (should be updated).</span></span>

> [!TIP]  
> <span data-ttu-id="59a2d-109">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Disconnected/)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-109">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Disconnected/) on GitHub.</span></span>

> [!TIP]
> <span data-ttu-id="59a2d-110">EF Core では、指定されたプライマリ キー値を持つ任意のエンティティの 1 つのインスタンスしか追跡できません。</span><span class="sxs-lookup"><span data-stu-id="59a2d-110">EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="59a2d-111">これを回避する最善の方法は、各作業単位に一時的なコンテキストを使用して、最初は空のコンテキストにエンティティをアタッチし、それらのエンティティを保存してから、コンテキストが消去および破棄されるようにすることです。</span><span class="sxs-lookup"><span data-stu-id="59a2d-111">The best way to avoid this being an issue is to use a short-lived context for each unit-of-work such that the context starts empty, has entities attached to it, saves those entities, and then the context is disposed and discarded.</span></span>

## <a name="identifying-new-entities"></a><span data-ttu-id="59a2d-112">新しいエンティティの識別</span><span class="sxs-lookup"><span data-stu-id="59a2d-112">Identifying new entities</span></span>

### <a name="client-identifies-new-entities"></a><span data-ttu-id="59a2d-113">クライアントが新しいエンティティを識別する</span><span class="sxs-lookup"><span data-stu-id="59a2d-113">Client identifies new entities</span></span>

<span data-ttu-id="59a2d-114">最も処理しやすいのは、エンティティが新規か既存かをクライアントからサーバーに知らせるタイミングです。</span><span class="sxs-lookup"><span data-stu-id="59a2d-114">The simplest case to deal with is when the client informs the server whether the entity is new or existing.</span></span> <span data-ttu-id="59a2d-115">たとえば、新しいエンティティを挿入するための要求は、多くの場合、既存のエンティティを更新するための要求とは異なります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-115">For example, often the request to insert a new entity is different from the request to update an existing entity.</span></span>

<span data-ttu-id="59a2d-116">このセクションの以降の説明では、別の方法で挿入か更新かを判断する必要があるケースを取り上げます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-116">The remainder of this section covers the cases where it necessary to determine in some other way whether to insert or update.</span></span>

### <a name="with-auto-generated-keys"></a><span data-ttu-id="59a2d-117">自動生成キーを利用する</span><span class="sxs-lookup"><span data-stu-id="59a2d-117">With auto-generated keys</span></span>

<span data-ttu-id="59a2d-118">自動生成されたキーの値は、エンティティを挿入する必要があるか、または更新する必要があるかを判断するために使用されることが、よくあります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-118">The value of an automatically generated key can often be used to determine whether an entity needs to be inserted or updated.</span></span> <span data-ttu-id="59a2d-119">キーが設定済みでない (つまり、null や 0 などの CLR 既定値をまだ保持している) 場合、エンティティは常に新規となり、挿入する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-119">If the key has not been set (that is, it still has the CLR default value of null, zero, etc.), then the entity must be new and needs inserting.</span></span> <span data-ttu-id="59a2d-120">一方、キー値が設定済みの場合、エンティティは常に既に事前保存済みであり、更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-120">On the other hand, if the key value has been set, then it must have already been previously saved and now needs updating.</span></span> <span data-ttu-id="59a2d-121">つまり、キーが値を保持している場合、エンティティは照会され、クライアントに送信されて、更新されるようになります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-121">In other words, if the key has a value, then the entity was queried, sent to the client, and has now come back to be updated.</span></span>

<span data-ttu-id="59a2d-122">エンティティ型がわかっている場合、設定されていないキーのチェックは簡単です。</span><span class="sxs-lookup"><span data-stu-id="59a2d-122">It is easy to check for an unset key when the entity type is known:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewSimple)]

<span data-ttu-id="59a2d-123">しかし、EF でもエンティティ型やキーの種類をチェックするための組み込みの方法を備えています。</span><span class="sxs-lookup"><span data-stu-id="59a2d-123">However, EF also has a built-in way to do this for any entity type and key type:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> <span data-ttu-id="59a2d-124">エンティティがコンテキストによって追跡されると、エンティティが追加済みの状態になっていても、すぐにキーが設定されます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-124">Keys are set as soon as entities are tracked by the context, even if the entity is in the Added state.</span></span> <span data-ttu-id="59a2d-125">これは、TrackGraph API を使用している場合など、エンティティのグラフを走査して各グラフで行う操作を決定する際に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-125">This helps when traversing a graph of entities and deciding what to do with each, such as when using the TrackGraph API.</span></span> <span data-ttu-id="59a2d-126">キー値は、エンティティを追跡するために何らかの呼び出しが行われる "_前_" に、ここに示された方法でのみ使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-126">The key value should only be used in the way shown here _before_ any call is made to track the entity.</span></span>

### <a name="with-other-keys"></a><span data-ttu-id="59a2d-127">他のキーを利用する</span><span class="sxs-lookup"><span data-stu-id="59a2d-127">With other keys</span></span>

<span data-ttu-id="59a2d-128">キー値が自動生成されない場合、他のいくつかのメカニズムが、新しいエンティティの識別に利用されます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-128">Some other mechanism is needed to identify new entities when key values are not generated automatically.</span></span> <span data-ttu-id="59a2d-129">これを行うための一般的な方法として、次の 2 つがあります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-129">There are two general approaches to this:</span></span>
 * <span data-ttu-id="59a2d-130">エンティティに対してクエリを実行する</span><span class="sxs-lookup"><span data-stu-id="59a2d-130">Query for the entity</span></span>
 * <span data-ttu-id="59a2d-131">クライアントからフラグを渡す</span><span class="sxs-lookup"><span data-stu-id="59a2d-131">Pass a flag from the client</span></span>

<span data-ttu-id="59a2d-132">エンティティに対してクエリを実行するには、単純に Find メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="59a2d-132">To query for the entity, just use the Find method:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewQuery)]

<span data-ttu-id="59a2d-133">クライアントからフラグを渡す完全なコードの提示は、このドキュメントでは行いません。</span><span class="sxs-lookup"><span data-stu-id="59a2d-133">It is beyond the scope of this document to show the full code for passing a flag from a client.</span></span> <span data-ttu-id="59a2d-134">Web アプリでは、多くの場合、さまざまなアクションに対して異なる要求を行うか、または要求内で何らかの状態を渡して、コントローラーでその状態を抽出することになります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-134">In a web app, it usually means making different requests for different actions, or passing some state in the request then extracting it in the controller.</span></span>

## <a name="saving-single-entities"></a><span data-ttu-id="59a2d-135">単一のエンティティを保存する</span><span class="sxs-lookup"><span data-stu-id="59a2d-135">Saving single entities</span></span>

<span data-ttu-id="59a2d-136">挿入または更新のどちらが必要かがわかったら、次のように Add または Update を適切に使用できます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-136">If it is known whether or not an insert or update is needed, then either Add or Update can be used appropriately:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

<span data-ttu-id="59a2d-137">ただし、エンティティが自動生成されたキー値を使用する場合は、両方のケースで Update メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-137">However, if the entity uses auto-generated key values, then the Update method can be used for both cases:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

<span data-ttu-id="59a2d-138">Update メソッドは通常、挿入ではなく、更新用のエンティティをマークします。</span><span class="sxs-lookup"><span data-stu-id="59a2d-138">The Update method normally marks the entity for update, not insert.</span></span> <span data-ttu-id="59a2d-139">ただし、エンティティが自動生成されたキーを保持しており、設定済みのキー値がない場合、そのエンティティは更新用ではなく、自動的に挿入用にマークされます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-139">However, if the entity has a auto-generated key, and no key value has been set, then the entity is instead automatically marked for insert.</span></span>

> [!TIP]  
> <span data-ttu-id="59a2d-140">この動作は、EF Core 2.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="59a2d-140">This behavior was introduced in EF Core 2.0.</span></span> <span data-ttu-id="59a2d-141">それ以前のリリースでは、Add または Update のどちらかを常に明示的に選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-141">For earlier releases it is always necessary to explicitly choose either Add or Update.</span></span>

<span data-ttu-id="59a2d-142">エンティティが自動生成されたキーを使用していない場合、アプリケーションでは、エンティティの挿入または更新のどちらが必要かを、次の例のように判断する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-142">If the entity is not using auto-generated keys, then the application must decide whether the entity should be inserted or updated: For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

<span data-ttu-id="59a2d-143">この場合の手順は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-143">The steps here are:</span></span>
* <span data-ttu-id="59a2d-144">Find で null が返された場合、データベースがこの ID のブログを既に含んでいるわけではないため、Add を呼び出して挿入用にマークします。</span><span class="sxs-lookup"><span data-stu-id="59a2d-144">If Find returns null, then the database doesn't already contain the blog with this ID, so we call Add mark it for insertion.</span></span>
* <span data-ttu-id="59a2d-145">Find がエンティティを返した場合は、エンティティがデータベースに存在しており、コンテキストは既存のエンティティを追跡するようになります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-145">If Find returns an entity, then it exists in the database and the context is now tracking the existing entity</span></span>
  * <span data-ttu-id="59a2d-146">SetValues を使用して、このエンティティ上のすべてのプロパティの値を、クライアントから受信したプロパティに設定します。</span><span class="sxs-lookup"><span data-stu-id="59a2d-146">We then use SetValues to set the values for all properties on this entity to those that came from the client.</span></span>
  * <span data-ttu-id="59a2d-147">SetValues の呼び出しでは、必要に応じて更新されるエンティティをマークします。</span><span class="sxs-lookup"><span data-stu-id="59a2d-147">The SetValues call will mark the entity to be updated as needed.</span></span>

> [!TIP]  
> <span data-ttu-id="59a2d-148">SetValues は、追跡されたエンティティのプロパティに別の値を保持しているプロパティを、変更済みとしてマークすることしか行いません。</span><span class="sxs-lookup"><span data-stu-id="59a2d-148">SetValues will only mark as modified the properties that have different values to those in the tracked entity.</span></span> <span data-ttu-id="59a2d-149">これは、更新が送信されると、実際に変更されたそれらの列のみが更新されることを意味します </span><span class="sxs-lookup"><span data-stu-id="59a2d-149">This means that when the update is sent, only those columns that have actually changed will be updated.</span></span> <span data-ttu-id="59a2d-150">(また、何も変更されなかった場合は、更新もまったく送信されません)。</span><span class="sxs-lookup"><span data-stu-id="59a2d-150">(And if nothing has changed, then no update will be sent at all.)</span></span>

## <a name="working-with-graphs"></a><span data-ttu-id="59a2d-151">グラフを操作する</span><span class="sxs-lookup"><span data-stu-id="59a2d-151">Working with graphs</span></span>

### <a name="identity-resolution"></a><span data-ttu-id="59a2d-152">識別子の解決</span><span class="sxs-lookup"><span data-stu-id="59a2d-152">Identity resolution</span></span>

<span data-ttu-id="59a2d-153">上述したように、EF Core では、指定されたプライマリ キー値を持つ任意のエンティティの 1 つのインスタンスしか追跡できません。</span><span class="sxs-lookup"><span data-stu-id="59a2d-153">As noted above, EF Core can only track one instance of any entity with a given primary key value.</span></span> <span data-ttu-id="59a2d-154">グラフを操作するとき、この不変の条件が維持されるようにグラフが理想的に作成される必要があり、コンテキストは 1 つの作業単位のみに対して使用される必要があります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-154">When working with graphs the graph should ideally be created such that this invariant is maintained, and the context should be used for only one unit-of-work.</span></span> <span data-ttu-id="59a2d-155">グラフに重複値が含まれている場合、EF に送信して複数のインスタンスを 1 つに統合する前に、そのグラフを処理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-155">If the graph does contain duplicates, then it will be necessary to process the graph before sending it to EF to consolidate multiple instances into one.</span></span> <span data-ttu-id="59a2d-156">インスタンスに競合する値やリレーションシップがある場合、この処理が複雑になるので、アプリケーションのパイプラインで競合の解決が発生しないように、できるだけすぐに複数の値の統合を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-156">This may not be trivial where instances have conflicting values and relationships, so consolidating duplicates should be done as soon as possible in your application pipeline to avoid conflict resolution.</span></span>

### <a name="all-newall-existing-entities"></a><span data-ttu-id="59a2d-157">すべての新規/既存のエンティティ</span><span class="sxs-lookup"><span data-stu-id="59a2d-157">All new/all existing entities</span></span>

<span data-ttu-id="59a2d-158">グラフの操作の例では、関連する投稿のコレクションと共にブログを挿入または更新しています。</span><span class="sxs-lookup"><span data-stu-id="59a2d-158">An example of working with graphs is inserting or updating a blog together with its collection of associated posts.</span></span> <span data-ttu-id="59a2d-159">グラフ内のすべてのエンティティが挿入される必要がある場合、またはすべてが更新される必要がある場合、プロセスは上述した単一のエンティティの場合と同じです。</span><span class="sxs-lookup"><span data-stu-id="59a2d-159">If all the entities in the graph should be inserted, or all should be updated, then the process is the same as described above for single entities.</span></span> <span data-ttu-id="59a2d-160">たとえば、作成されたブログと投稿のグラフは次のよになります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-160">For example, a graph of blogs and posts created like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

<span data-ttu-id="59a2d-161">また、次のように挿入できます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-161">can be inserted like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertGraph)]

<span data-ttu-id="59a2d-162">Add の呼び出しでは、ブログとすべての投稿が挿入されるようにマークします。</span><span class="sxs-lookup"><span data-stu-id="59a2d-162">The call to Add will mark the blog and all the posts to be inserted.</span></span>

<span data-ttu-id="59a2d-163">同様に、グラフ内のすべてのエンティティが更新される必要がある場合は、Update を使用できます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-163">Likewise, if all the entities in a graph need to be updated, then Update can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#UpdateGraph)]

<span data-ttu-id="59a2d-164">ブログとそのすべての投稿が、更新されるようにマークされます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-164">The blog and all its posts will be marked to be updated.</span></span>

### <a name="mix-of-new-and-existing-entities"></a><span data-ttu-id="59a2d-165">新規と既存のエンティティの混合</span><span class="sxs-lookup"><span data-stu-id="59a2d-165">Mix of new and existing entities</span></span>

<span data-ttu-id="59a2d-166">自動生成されたキーを使用する場合、挿入を必要とするエンティティと更新を必要とするエンティティがグラフ内に混在していても、挿入と更新の両方に Update を再使用できます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-166">With auto-generated keys, Update can again be used for both inserts and updates, even if the graph contains a mix of entities that require inserting and those that require updating:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

<span data-ttu-id="59a2d-167">Update は、設定されたキー値がない場合は、グラフ、ブログ、または投稿内のエンティティを挿入用にマークし、その他のエンティティはすべて更新用にマークします。</span><span class="sxs-lookup"><span data-stu-id="59a2d-167">Update will mark any entity in the graph, blog or post, for insertion if it does not have a key value set, while all other entities are marked for update.</span></span>

<span data-ttu-id="59a2d-168">前述のように、自動生成キーを使用しない場合は、クエリおよびいくつかの処理を使用できます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-168">As before, when not using auto-generated keys, a query and some processing can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a><span data-ttu-id="59a2d-169">削除の処理</span><span class="sxs-lookup"><span data-stu-id="59a2d-169">Handling deletes</span></span>

<span data-ttu-id="59a2d-170">エンティティの不在は、エンティティが削除されていることを意味することがよくあるため、削除は慎重に扱う必要があります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-170">Delete can be tricky to handle since often the absence of an entity means that it should be deleted.</span></span> <span data-ttu-id="59a2d-171">これに対処する方法の 1 つは、エンティティが実際に削除されるのではなく、削除としてマークされるように、"論理的な削除" を使用することです。</span><span class="sxs-lookup"><span data-stu-id="59a2d-171">One way to deal with this is to use "soft deletes" such that the entity is marked as deleted rather than actually being deleted.</span></span> <span data-ttu-id="59a2d-172">これで、削除は更新と同様になります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-172">Deletes then becomes the same as updates.</span></span> <span data-ttu-id="59a2d-173">論理的な削除は、[クエリ フィルター](xref:core/querying/filters)を使用して実装できます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-173">Soft deletes can be implemented in using [query filters](xref:core/querying/filters).</span></span>

<span data-ttu-id="59a2d-174">実際の削除では、一般的なパターンとしてクエリ パターンの拡張機能を使用して、本質的なグラフの差分特定を行います。例:</span><span class="sxs-lookup"><span data-stu-id="59a2d-174">For true deletes, a common pattern is to use an extension of the query pattern to perform what is essentially a graph diff. For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a><span data-ttu-id="59a2d-175">TrackGraph</span><span class="sxs-lookup"><span data-stu-id="59a2d-175">TrackGraph</span></span>

<span data-ttu-id="59a2d-176">内部的には、Add、Attach、および Update では、追加済み (挿入する)、変更済み (更新する)、変更なし (何もしない)、または削除済み (削除) としてマークする必要があるかどうかに関して各エンティティで行われる判断と共に、グラフ走査を使用します。</span><span class="sxs-lookup"><span data-stu-id="59a2d-176">Internally, Add, Attach, and Update use graph-traversal with a determination made for each entity as to whether it should be marked as Added (to insert), Modified (to update), Unchanged (do nothing), or Deleted (to delete).</span></span> <span data-ttu-id="59a2d-177">このメカニズムは TrackGraph API 経由で公開されています。</span><span class="sxs-lookup"><span data-stu-id="59a2d-177">This mechanism is exposed via the TrackGraph API.</span></span> <span data-ttu-id="59a2d-178">たとえば、クライアントがエンティティのグラフを返送するときに、処理方法を示したフラグが各エンティティ上に設定されていると仮定しましょう。</span><span class="sxs-lookup"><span data-stu-id="59a2d-178">For example, let's assume that when the client sends back a graph of entities it sets some flag on each entity indicating how it should be handled.</span></span> <span data-ttu-id="59a2d-179">このフラグを処理するために、次のように TrackGraph を使用できます。</span><span class="sxs-lookup"><span data-stu-id="59a2d-179">TrackGraph can then be used to process this flag:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#TrackGraph)]

<span data-ttu-id="59a2d-180">例を簡単にするために、フラグはエンティティの一部分としてしか表示されていません。</span><span class="sxs-lookup"><span data-stu-id="59a2d-180">The flags are only shown as part of the entity for simplicity of the example.</span></span> <span data-ttu-id="59a2d-181">フラグは通常、DTO の一部か、または要求に含まれている他の状態になります。</span><span class="sxs-lookup"><span data-stu-id="59a2d-181">Typically the flags would be part of a DTO or some other state included in the request.</span></span>
