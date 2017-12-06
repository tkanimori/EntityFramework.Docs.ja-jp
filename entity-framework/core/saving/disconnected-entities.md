---
title: "切断されているエンティティの EF コア"
author: ajcvickers
ms.author: avickers
ms.date: 10/27/2016
ms.assetid: 2533b195-d357-4056-b0e0-8698971bc3b0
ms.technology: entity-framework-core
uid: core/saving/disconnected-entities
ms.openlocfilehash: b9d9662ce277e4f7b3d6f997a5117a0592f59fa3
ms.sourcegitcommit: c72d85805db0aa95f980514a18381fdc5e17c786
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="disconnected-entities"></a><span data-ttu-id="b459f-102">切断されているエンティティ</span><span class="sxs-lookup"><span data-stu-id="b459f-102">Disconnected entities</span></span>

<span data-ttu-id="b459f-103">DbContext インスタンスでは、データベースから返されるエンティティを自動的に追跡されます。</span><span class="sxs-lookup"><span data-stu-id="b459f-103">A DbContext instance will automatically track entities returned from the database.</span></span> <span data-ttu-id="b459f-104">SaveChanges が呼び出され、必要に応じて、データベースが更新されるときに、これらのエンティティに加えられた変更を検出し、されます。</span><span class="sxs-lookup"><span data-stu-id="b459f-104">Changes made to these entities will then be detected when SaveChanges is called and the database will be updated as needed.</span></span> <span data-ttu-id="b459f-105">参照してください[基本的な保存](basic.md)と[関連データ](related-data.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="b459f-105">See [Basic Save](basic.md) and [Related Data](related-data.md) for details.</span></span>

<span data-ttu-id="b459f-106">ただし、場合によってエンティティを照会コンテキストの 1 つのインスタンスを使用して、別のインスタンスを使用して保存します。</span><span class="sxs-lookup"><span data-stu-id="b459f-106">However, sometimes entities are queried using one context instance and then saved using a different instance.</span></span> <span data-ttu-id="b459f-107">これは、ここでエンティティはクエリを実行、クライアントに送信される、変更、要求でサーバーに送信されるおよび保存し、web アプリケーションなどの「切断済み」のシナリオでよく発生します。</span><span class="sxs-lookup"><span data-stu-id="b459f-107">This often happens in "disconnected" scenarios such as a web application where the entities are queried, sent to the client, modified, sent back to the server in a request, and then saved.</span></span> <span data-ttu-id="b459f-108">ここでは、2 つ目のコンテキスト エンティティが新しいかどうかを知る (を挿入する) 必要がありますか (更新する必要があります) を既存をインスタンスします。</span><span class="sxs-lookup"><span data-stu-id="b459f-108">In this case, the second context instance needs to know whether the entities are new (should be inserted) or existing (should be updated).</span></span>

> [!TIP]  
> <span data-ttu-id="b459f-109">この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Disconnected/)GitHub でします。</span><span class="sxs-lookup"><span data-stu-id="b459f-109">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Disconnected/) on GitHub.</span></span>

## <a name="identifying-new-entities"></a><span data-ttu-id="b459f-110">新しいエンティティを識別します。</span><span class="sxs-lookup"><span data-stu-id="b459f-110">Identifying new entities</span></span>

### <a name="client-identifies-new-entities"></a><span data-ttu-id="b459f-111">クライアントが新しいエンティティを識別します。</span><span class="sxs-lookup"><span data-stu-id="b459f-111">Client identifies new entities</span></span>

<span data-ttu-id="b459f-112">最も単純なに対処するしている場合、エンティティは、新しいまたは既存かどうかをクライアントがサーバーに通知します。</span><span class="sxs-lookup"><span data-stu-id="b459f-112">The simplest case to deal with is when the client informs the server whether the entity is new or existing.</span></span> <span data-ttu-id="b459f-113">たとえば、多くの場合、新しいエンティティを挿入する要求は、既存のエンティティを更新する要求を異なるです。</span><span class="sxs-lookup"><span data-stu-id="b459f-113">For example, often the request to insert a new entity is different from the request to update an existing entity.</span></span>

<span data-ttu-id="b459f-114">このセクションの残りの部分は、ケースをカバーして、その他の何らかの方法で挿入または更新するかどうか判断するために必要です。</span><span class="sxs-lookup"><span data-stu-id="b459f-114">The remainder of this section covers the cases where it necessary to determine in some other way whether to insert or update.</span></span>

### <a name="with-auto-generated-keys"></a><span data-ttu-id="b459f-115">自動生成キーを持つ</span><span class="sxs-lookup"><span data-stu-id="b459f-115">With auto-generated keys</span></span>

<span data-ttu-id="b459f-116">自動的に生成されたキーの値は、エンティティを挿入または更新する必要があるかどうかを決定する多くの場合、使用できます。</span><span class="sxs-lookup"><span data-stu-id="b459f-116">The value of an automatically generated key can often be used to determine whether an entity needs to be inserted or updated.</span></span> <span data-ttu-id="b459f-117">キーが設定されて (つまり、まだ null、0 を返しなどの CLR の既定値)、エンティティが新しいである必要があるあり、挿入する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b459f-117">If the key has not been set (i.e. it still has the CLR default value of null, zero, etc.), then the entity must be new and needs inserting.</span></span> <span data-ttu-id="b459f-118">その一方で、キーの値が設定されている場合、必要がありますが既に保存されている以前し更新が必要なようになりました。</span><span class="sxs-lookup"><span data-stu-id="b459f-118">On the other hand, if the key value has been set, then it must have already been previously saved and now needs updating.</span></span> <span data-ttu-id="b459f-119">つまり、キーの値が、し、エンティティの照会、クライアントに送信されるがようになりました戻ると更新します。</span><span class="sxs-lookup"><span data-stu-id="b459f-119">In other words, if the key has a value, then entity was queried, sent to the client, and has now come back to be updated.</span></span>

<span data-ttu-id="b459f-120">これは、エンティティ型がわかっている場合に設定されていないキーの確認簡単です。</span><span class="sxs-lookup"><span data-stu-id="b459f-120">It is easy to check for an unset key when the entity type is known:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewSimple)]

<span data-ttu-id="b459f-121">ただし、EF は、これを行うすべてのエンティティ型とキーの種類の機能を組み込むこともあります。</span><span class="sxs-lookup"><span data-stu-id="b459f-121">However, EF also has a built-in way to do this for any entity type and key type:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> <span data-ttu-id="b459f-122">キーは、場合でも、状態が Added では、エンティティのエンティティが、コンテキストによって追跡されるとすぐに設定されます。</span><span class="sxs-lookup"><span data-stu-id="b459f-122">Keys are set as soon as entities are tracked by the context, even if the entity is in the Added state.</span></span> <span data-ttu-id="b459f-123">これは、エンティティと TrackGraph API を使用する場合と同様、このような処理を決定のグラフを走査する場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="b459f-123">This helps when traversing a graph of entities and deciding what to do with each, such as when using the TrackGraph API.</span></span> <span data-ttu-id="b459f-124">キーの値は、次に示す方法でのみ使用する必要があります_する前に_エンティティを追跡するために、呼び出しが行われます。</span><span class="sxs-lookup"><span data-stu-id="b459f-124">The key value should only be used in the way shown here _before_ any call is made to track the entity.</span></span>

### <a name="with-other-keys"></a><span data-ttu-id="b459f-125">その他のキーを持つ</span><span class="sxs-lookup"><span data-stu-id="b459f-125">With other keys</span></span>

<span data-ttu-id="b459f-126">キーの値が自動的に生成されたいないときに、新しいエンティティを識別するその他の機構が必要です。</span><span class="sxs-lookup"><span data-stu-id="b459f-126">Some other mechanism is needed to identity new entities when key values are not generated automatically.</span></span> <span data-ttu-id="b459f-127">この 2 つの一般的な方法があります。</span><span class="sxs-lookup"><span data-stu-id="b459f-127">There are two general approaches to this:</span></span>
 * <span data-ttu-id="b459f-128">エンティティのクエリ</span><span class="sxs-lookup"><span data-stu-id="b459f-128">Query for the entity</span></span>
 * <span data-ttu-id="b459f-129">クライアントから、フラグを渡します</span><span class="sxs-lookup"><span data-stu-id="b459f-129">Pass a flag from the client</span></span>

<span data-ttu-id="b459f-130">クエリを実行するエンティティのだけ検索メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="b459f-130">To query for the entity, just use the Find method:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#IsItNewQuery)]

<span data-ttu-id="b459f-131">クライアントからフラグを渡すため完全なコードを表示するには、このドキュメントの範囲を超えています。</span><span class="sxs-lookup"><span data-stu-id="b459f-131">It is beyond the scope of this document to show the full code for passing a flag from a client.</span></span> <span data-ttu-id="b459f-132">Web アプリで通常意味をさまざまな操作は、別の要求を行うか、要求にいくつかの状態を渡すこと、コント ローラーに抽出します。</span><span class="sxs-lookup"><span data-stu-id="b459f-132">In a web app, it usually means making different requests for different actions, or passing some state in the request then extracting it in the controller.</span></span>

## <a name="saving-single-entities"></a><span data-ttu-id="b459f-133">単一のエンティティの保存</span><span class="sxs-lookup"><span data-stu-id="b459f-133">Saving single entities</span></span>

<span data-ttu-id="b459f-134">場合は認識されているかどうか、insert または update は、必要な追加または更新のいずれかを適切に使用することができます。</span><span class="sxs-lookup"><span data-stu-id="b459f-134">If it is known whether or not an insert or update is needed, then either Add or Update can be used appropriately:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

<span data-ttu-id="b459f-135">ただし、エンティティは、自動生成されたキー値を使用する場合のどちらの場合、Update メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b459f-135">However, if the entity uses auto-generated key values, then the Update method can be used for both cases:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

<span data-ttu-id="b459f-136">Update メソッドは、通常、更新、挿入ではないエンティティをマークします。</span><span class="sxs-lookup"><span data-stu-id="b459f-136">The Update method normally marks the entity for update, not insert.</span></span> <span data-ttu-id="b459f-137">ただし、エンティティが、自動生成されたキーとキーの値が設定されていない、エンティティは代わりに自動的に設定されていますし、次のように挿入します。</span><span class="sxs-lookup"><span data-stu-id="b459f-137">However, if the entity has a auto-generated key, and no key value has been set, then the entity is instead automatically marked for insert.</span></span>

> [!TIP]  
> <span data-ttu-id="b459f-138">この動作は、EF コア 2.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="b459f-138">This behavior was introduced in EF Core 2.0.</span></span> <span data-ttu-id="b459f-139">以前のリリースは常に使用するために必要な明示的に追加] または [更新プログラムを選択します。</span><span class="sxs-lookup"><span data-stu-id="b459f-139">For earlier releases it is always necessary to explicitly choose either Add or Update.</span></span>

<span data-ttu-id="b459f-140">エンティティは、自動生成キーを使用していないかどうかは、アプリケーションは、エンティティを挿入または更新するかどうかを決定する必要があります。 例。</span><span class="sxs-lookup"><span data-stu-id="b459f-140">If the entity is not using auto-generated keys, then the application must decide whether the entity should be inserted or updated: For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

<span data-ttu-id="b459f-141">ここに示す手順は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b459f-141">The steps here are:</span></span>
* <span data-ttu-id="b459f-142">検索を返します。 null の場合、データベースに含まれていないこの ID を持つブログおを呼び出すようにし、追加する場合は、挿入のためマークします。</span><span class="sxs-lookup"><span data-stu-id="b459f-142">If Find returns null, then the database doesn't already contain the blog with this ID, so we call Add mark it for insertion.</span></span>
* <span data-ttu-id="b459f-143">検索では、エンティティが返された場合、データベース内に存在する、およびコンテキストは既存のエンティティを追跡してようになりました</span><span class="sxs-lookup"><span data-stu-id="b459f-143">If Find returns an entity, then it exists in the database and the context is now tracking the existing entity</span></span>
  * <span data-ttu-id="b459f-144">SetValues を使用して、クライアントから得られたものには、このエンティティのすべてのプロパティの値を設定します。</span><span class="sxs-lookup"><span data-stu-id="b459f-144">We then use SetValues to set the values for all properties on this entity to those that came from the client.</span></span>
  * <span data-ttu-id="b459f-145">SetValues 呼び出しでは、必要に応じて更新するエンティティをマークします。</span><span class="sxs-lookup"><span data-stu-id="b459f-145">The SetValues call will mark the entity to be updated as needed.</span></span>

> [!TIP]  
> <span data-ttu-id="b459f-146">追跡対象エンティティの別の値を持つプロパティを変更済みとして SetValues にマークされるだけです。</span><span class="sxs-lookup"><span data-stu-id="b459f-146">SetValues will only mark as modified the properties that have different values to those in the tracked entity.</span></span> <span data-ttu-id="b459f-147">これは、更新が送信されると、実際に変更された列のみが更新されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="b459f-147">This means that when the update is sent, only those columns that have actually changed will be updated.</span></span> <span data-ttu-id="b459f-148">(と何が変更された場合、更新プログラムは送信されませんまったく)。</span><span class="sxs-lookup"><span data-stu-id="b459f-148">(And if nothing has changed, then no update will be sent at all.)</span></span>

## <a name="working-with-graphs"></a><span data-ttu-id="b459f-149">グラフの操作</span><span class="sxs-lookup"><span data-stu-id="b459f-149">Working with graphs</span></span>

### <a name="all-newall-existing-entities"></a><span data-ttu-id="b459f-150">すべての新しいまたはすべて既存のエンティティ</span><span class="sxs-lookup"><span data-stu-id="b459f-150">All new/all existing entities</span></span>

<span data-ttu-id="b459f-151">グラフの操作の例を挿入またはその関連する投稿のコレクションと共にブログを更新するとします。</span><span class="sxs-lookup"><span data-stu-id="b459f-151">An example of working with graphs is inserting or updating a blog together with its collection of associated posts.</span></span> <span data-ttu-id="b459f-152">グラフ内のすべてのエンティティを挿入する、またはすべてを更新する、し、プロセスが単一のエンティティの上記と同じです。</span><span class="sxs-lookup"><span data-stu-id="b459f-152">If all the entities in the graph should be inserted, or all should be updated, then the process is the same as described above for single entities.</span></span> <span data-ttu-id="b459f-153">たとえば、ブログと次のように作成された投稿のグラフ:</span><span class="sxs-lookup"><span data-stu-id="b459f-153">For example, a graph of blogs and posts created like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

<span data-ttu-id="b459f-154">次のように挿入することができます。</span><span class="sxs-lookup"><span data-stu-id="b459f-154">can be inserted like this:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertGraph)]

<span data-ttu-id="b459f-155">追加の呼び出しは、ブログや挿入されるすべての投稿にマークされます。</span><span class="sxs-lookup"><span data-stu-id="b459f-155">The call to Add will mark the blog and all the posts to be inserted.</span></span>

<span data-ttu-id="b459f-156">同様に、グラフ内のすべてのエンティティを更新する必要がある場合、更新プログラムを使用できます。</span><span class="sxs-lookup"><span data-stu-id="b459f-156">Likewise, if all the entities in a graph need to be updated, then Update can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#UpdateGraph)]

<span data-ttu-id="b459f-157">ブログとそのすべての投稿を更新するマークされます。</span><span class="sxs-lookup"><span data-stu-id="b459f-157">The blog and all its posts will be marked to be updated.</span></span>

### <a name="mix-of-new-and-existing-entities"></a><span data-ttu-id="b459f-158">新規および既存のエンティティのミックス</span><span class="sxs-lookup"><span data-stu-id="b459f-158">Mix of new and existing entities</span></span>

<span data-ttu-id="b459f-159">、自動生成キーを持つ更新もう一度使用できます挿入と更新の両方の場合でも、グラフには、さまざまなエンティティを挿入するを必要として更新の必要なものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="b459f-159">With auto-generated keys, Update can again be used for both inserts and updates, even if the graph contains a mix of entities that require inserting and those that require updating:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

<span data-ttu-id="b459f-160">更新プログラムは、グラフ、ブログまたは post の場合に、キーの値が設定されていない、更新プログラムの他のすべてのエンティティがマークされているときに挿入するため、すべてのエンティティとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="b459f-160">Update will mark any entity in the graph, blog or post, for insertion if it does not have a key value set, while all other entities are marked for update.</span></span>

<span data-ttu-id="b459f-161">としてする前に、自動生成キーを使用していない場合、クエリといくつかの処理を使用できます。</span><span class="sxs-lookup"><span data-stu-id="b459f-161">As before, when not using auto-generated keys, a query and some processing can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a><span data-ttu-id="b459f-162">削除の処理</span><span class="sxs-lookup"><span data-stu-id="b459f-162">Handling deletes</span></span>

<span data-ttu-id="b459f-163">削除はされてから処理する複雑になることが多くの場合、エンティティがない場合では削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b459f-163">Delete can be tricky to handle since often the absence of an entity means that it should be deleted.</span></span> <span data-ttu-id="b459f-164">これに対処する 1 つの方法では、「回復可能な削除」を使用して、エンティティが実際に削除されるのではなく削除済みとしてマークされているようです。</span><span class="sxs-lookup"><span data-stu-id="b459f-164">One way to deal with this is to use "soft deletes" such that the entity is marked as deleted rather than actually being deleted.</span></span> <span data-ttu-id="b459f-165">削除してから、更新プログラムと同じになります。</span><span class="sxs-lookup"><span data-stu-id="b459f-165">Deletes then becomes the same as updates.</span></span> <span data-ttu-id="b459f-166">使用して実装できる回復可能な削除[フィルターをクエリ](xref:core/querying/filters)です。</span><span class="sxs-lookup"><span data-stu-id="b459f-166">Soft deletes can be implemented in using [query filters](xref:core/querying/filters).</span></span>

<span data-ttu-id="b459f-167">場合は true。 削除は、一般的なパターンでは、クエリ パターンの拡張機能を使用して graph 相違では基本的には、何を実行するには例:</span><span class="sxs-lookup"><span data-stu-id="b459f-167">For true deletes, a common pattern is to use an extension of the query pattern to perform what is essentially a graph diff. For example:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a><span data-ttu-id="b459f-168">TrackGraph</span><span class="sxs-lookup"><span data-stu-id="b459f-168">TrackGraph</span></span>

<span data-ttu-id="b459f-169">内部的には、追加、アタッチ、および Update を使用して graph トラバーサルを Unchanged にするかどうか、としてマークしておく (挿入) を追加、変更 (更新) を各エンティティに対して行われた判断 (何もしない)、または (削除) を削除します。</span><span class="sxs-lookup"><span data-stu-id="b459f-169">Internally, Add, Attach, and Update use graph-traversal with a determination made for each entity as to whether it should be marked as Added (to insert), Modified (to update), Unchanged (do nothing), or Deleted (to delete).</span></span> <span data-ttu-id="b459f-170">このメカニズムは TrackGraph API を介して公開されます。</span><span class="sxs-lookup"><span data-stu-id="b459f-170">This mechanism is exposed via the TrackGraph API.</span></span> <span data-ttu-id="b459f-171">たとえば、クライアントがエンティティのグラフを返信ときにいくつかフラグを設定の処理方法を示す各エンティティを行うことと仮定します。</span><span class="sxs-lookup"><span data-stu-id="b459f-171">For example, let's assume that when the client sends back a graph of entities it sets some flag on each entity indicating how it should be handled.</span></span> <span data-ttu-id="b459f-172">TrackGraph はこのフラグを処理するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="b459f-172">TrackGraph can then be used to process this flag:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Disconnected/Sample.cs#TrackGraph)]

<span data-ttu-id="b459f-173">フラグは、わかりやすくするための例では、エンティティの一部としてのみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="b459f-173">The flags are only shown as part of the entity for simplicity of the example.</span></span> <span data-ttu-id="b459f-174">通常、フラグは DTO または要求に含まれるその他の状態の一部になります。</span><span class="sxs-lookup"><span data-stu-id="b459f-174">Typically the flags would be part of a DTO or some other state included in the request.</span></span>
