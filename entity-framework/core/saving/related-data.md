---
title: 関連データの保存 - EF Core
description: 関連エンティティのグラフの保存と Entity Framework Core でのリレーションシップの管理に関する情報
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/related-data
ms.openlocfilehash: 25b4e265f19e658097be7332f9cba6fec3b560db
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062946"
---
# <a name="saving-related-data"></a><span data-ttu-id="156b6-103">関連データの保存</span><span class="sxs-lookup"><span data-stu-id="156b6-103">Saving Related Data</span></span>

<span data-ttu-id="156b6-104">分離されたエンティティに加え、モデルに定義されたリレーションシップを活用することもできます。</span><span class="sxs-lookup"><span data-stu-id="156b6-104">In addition to isolated entities, you can also make use of the relationships defined in your model.</span></span>

> [!TIP]  
> <span data-ttu-id="156b6-105">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/RelatedData/)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="156b6-105">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/RelatedData/) on GitHub.</span></span>

## <a name="adding-a-graph-of-new-entities"></a><span data-ttu-id="156b6-106">新しいエンティティ グループの追加</span><span class="sxs-lookup"><span data-stu-id="156b6-106">Adding a graph of new entities</span></span>

<span data-ttu-id="156b6-107">関連するいくつかの新しいエンティティを作成しているときに、いずれかのエンティティをコンテキストに追加すると、他のエンティティも同様に追加されます。</span><span class="sxs-lookup"><span data-stu-id="156b6-107">If you create several new related entities, adding one of them to the context will cause the others to be added too.</span></span>

<span data-ttu-id="156b6-108">次の例では、ブログと 3 つの関連する投稿のすべてがデータベースに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="156b6-108">In the following example, the blog and three related posts are all inserted into the database.</span></span> <span data-ttu-id="156b6-109">これらの投稿が検出され、追加されるのは、`Blog.Posts` ナビゲーション プロパティ経由で到達可能であるためです。</span><span class="sxs-lookup"><span data-stu-id="156b6-109">The posts are found and added, because they are reachable via the `Blog.Posts` navigation property.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

> [!TIP]  
> <span data-ttu-id="156b6-110">単一のエンティティの状態を設定するには、EntityEntry.State プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="156b6-110">Use the EntityEntry.State property to set the state of just a single entity.</span></span> <span data-ttu-id="156b6-111">たとえば、「 `context.Entry(blog).State = EntityState.Modified` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="156b6-111">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="adding-a-related-entity"></a><span data-ttu-id="156b6-112">関連エンティティの追加</span><span class="sxs-lookup"><span data-stu-id="156b6-112">Adding a related entity</span></span>

<span data-ttu-id="156b6-113">コンテキストによって既に追跡されているエンティティのナビゲーション プロパティから新しいエンティティを参照した場合、そのエンティティが検出され、データベースに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="156b6-113">If you reference a new entity from the navigation property of an entity that is already tracked by the context, the entity will be discovered and inserted into the database.</span></span>

<span data-ttu-id="156b6-114">次の例では、データベースからフェッチされた `blog` エンティティの `Posts` プロパティに `post` エンティティが追加されているため、そのエンティティが挿入されます。</span><span class="sxs-lookup"><span data-stu-id="156b6-114">In the following example, the `post` entity is inserted because it is added to the `Posts` property of the `blog` entity which was fetched from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a><span data-ttu-id="156b6-115">リレーションシップの変更</span><span class="sxs-lookup"><span data-stu-id="156b6-115">Changing relationships</span></span>

<span data-ttu-id="156b6-116">エンティティのナビゲーション プロパティを変更すると、データベース内の外部キー列に対応する変更が行われます。</span><span class="sxs-lookup"><span data-stu-id="156b6-116">If you change the navigation property of an entity, the corresponding changes will be made to the foreign key column in the database.</span></span>

<span data-ttu-id="156b6-117">次の例では、`Blog` ナビゲーション プロパティが `blog` をポイントするように設定されているため、`post` エンティティが新しい `blog` エンティティに所属するように更新されます。</span><span class="sxs-lookup"><span data-stu-id="156b6-117">In the following example, the `post` entity is updated to belong to the new `blog` entity because its `Blog` navigation property is set to point to `blog`.</span></span> <span data-ttu-id="156b6-118">コンテキストによって既に追跡されているエンティティ (`post`) のナビゲーション プロパティによって参照される新しいエンティティであるため、`blog` もデータベースに挿入されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="156b6-118">Note that `blog` will also be inserted into the database because it is a new entity that is referenced by the navigation property of an entity that is already tracked by the context (`post`).</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a><span data-ttu-id="156b6-119">リレーションシップの削除</span><span class="sxs-lookup"><span data-stu-id="156b6-119">Removing relationships</span></span>

<span data-ttu-id="156b6-120">参照ナビゲーションを `null` に設定するか、コレクション ナビゲーションから関連エンティティを削除することで、リレーションシップを削除できます。</span><span class="sxs-lookup"><span data-stu-id="156b6-120">You can remove a relationship by setting a reference navigation to `null`, or removing the related entity from a collection navigation.</span></span>

<span data-ttu-id="156b6-121">リレーションシップの削除は、リレーションシップに構成された連鎖削除動作に従って、依存エンティティに影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="156b6-121">Removing a relationship can have side effects on the dependent entity, according to the cascade delete behavior configured in the relationship.</span></span>

<span data-ttu-id="156b6-122">必須リレーションシップでは、連鎖削除動作が既定で構成され、子/依存エンティティがデータベースから削除されます。</span><span class="sxs-lookup"><span data-stu-id="156b6-122">By default, for required relationships, a cascade delete behavior is configured and the child/dependent entity will be deleted from the database.</span></span> <span data-ttu-id="156b6-123">省略可能なリレーションシップでは、連鎖削除は既定では構成されていませんが、外部キー プロパティが null 設定されます。</span><span class="sxs-lookup"><span data-stu-id="156b6-123">For optional relationships, cascade delete is not configured by default, but the foreign key property will be set to null.</span></span>

<span data-ttu-id="156b6-124">リレーションシップが必須かどうかを構成する方法については、「[Required and Optional Relationships](xref:core/modeling/relationships#required-and-optional-relationships)」(必須リレーションシップと省略可能なリレーションシップ) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="156b6-124">See [Required and Optional Relationships](xref:core/modeling/relationships#required-and-optional-relationships) to learn about how the requiredness of relationships can be configured.</span></span>

<span data-ttu-id="156b6-125">連鎖削除の動作方法、それらを明示的に構成する方法、および慣例に従った選択方法については、「[連鎖削除](xref:core/saving/cascade-delete)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="156b6-125">See [Cascade Delete](xref:core/saving/cascade-delete) for more details on how cascade delete behaviors work, how they can be configured explicitly and  how they are selected by convention.</span></span>

<span data-ttu-id="156b6-126">次の例では、`Blog` と `Post` 間のリレーションシップに連鎖削除が構成されているため、`post` エンティティがデータベースから削除されます。</span><span class="sxs-lookup"><span data-stu-id="156b6-126">In the following example, a cascade delete is configured on the relationship between `Blog` and `Post`, so the `post` entity is deleted from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#RemovingRelationships)]
