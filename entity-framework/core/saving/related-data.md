---
title: 関連するデータの EF コアの保存
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 07b6680f-ffcf-412c-9857-f997486b386c
ms.technology: entity-framework-core
uid: core/saving/related-data
ms.openlocfilehash: b0ed25267c85e82db18d8a89693b6040db7e4b34
ms.sourcegitcommit: 4997314356118d0d97b04ad82e433e49bb9420a2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="saving-related-data"></a><span data-ttu-id="0f1dd-102">関連するデータの保存</span><span class="sxs-lookup"><span data-stu-id="0f1dd-102">Saving Related Data</span></span>

<span data-ttu-id="0f1dd-103">分離のエンティティだけでなく行うことも、モデルで定義されているリレーションシップを使用します。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-103">In addition to isolated entities, you can also make use of the relationships defined in your model.</span></span>

> [!TIP]  
> <span data-ttu-id="0f1dd-104">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/RelatedData/)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/RelatedData/) on GitHub.</span></span>

## <a name="adding-a-graph-of-new-entities"></a><span data-ttu-id="0f1dd-105">新しいエンティティのグラフを追加します。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-105">Adding a graph of new entities</span></span>

<span data-ttu-id="0f1dd-106">いくつかの新しい関連エンティティを作成する場合、コンテキストへのうちの 1 つの追加により、他のユーザーを追加するされます。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-106">If you create several new related entities, adding one of them to the context will cause the others to be added too.</span></span>

<span data-ttu-id="0f1dd-107">次の例では、ブログと 3 つの関連する投稿はすべて、データベースに挿入します。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-107">In the following example, the blog and three related posts are all inserted into the database.</span></span> <span data-ttu-id="0f1dd-108">投稿が検出され、追加、経由で到達可能であるため、`Blog.Posts`ナビゲーション プロパティ。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-108">The posts are found and added, because they are reachable via the `Blog.Posts` navigation property.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

> [!TIP]  
> <span data-ttu-id="0f1dd-109">EntityEntry.State プロパティを使用すると、1 つのエンティティの状態を設定できます。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-109">Use the EntityEntry.State property to set the state of just a single entity.</span></span> <span data-ttu-id="0f1dd-110">たとえば、`context.Entry(blog).State = EntityState.Modified` のようにします。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-110">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="adding-a-related-entity"></a><span data-ttu-id="0f1dd-111">関連するエンティティの追加</span><span class="sxs-lookup"><span data-stu-id="0f1dd-111">Adding a related entity</span></span>

<span data-ttu-id="0f1dd-112">コンテキストによって既に追跡されているエンティティのナビゲーション プロパティから、新しいエンティティを参照する場合、エンティティが検出され、データベースに挿入します。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-112">If you reference a new entity from the navigation property of an entity that is already tracked by the context, the entity will be discovered and inserted into the database.</span></span>

<span data-ttu-id="0f1dd-113">次の例で、`post`に追加されるため、エンティティが挿入された、`Posts`のプロパティ、`blog`データベースからフェッチされているエンティティ。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-113">In the following example, the `post` entity is inserted because it is added to the `Posts` property of the `blog` entity which was fetched from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a><span data-ttu-id="0f1dd-114">リレーションシップを変更します。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-114">Changing relationships</span></span>

<span data-ttu-id="0f1dd-115">エンティティのナビゲーション プロパティを変更すると、対応する変更部分は、データベースの外部キー列になります。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-115">If you change the navigation property of an entity, the corresponding changes will be made to the foreign key column in the database.</span></span>

<span data-ttu-id="0f1dd-116">次の例で、`post`新しいに属しているエンティティが更新された`blog`エンティティのためその`Blog` をポイントするナビゲーション プロパティが設定`blog`です。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-116">In the following example, the `post` entity is updated to belong to the new `blog` entity because its `Blog` navigation property is set to point to `blog`.</span></span> <span data-ttu-id="0f1dd-117">なお`blog`コンテキストによって既に追跡されているエンティティのナビゲーション プロパティによって参照される新しいエンティティであるために、データベースに挿入もされます (`post`)。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-117">Note that `blog` will also be inserted into the database because it is a new entity that is referenced by the navigation property of an entity that is already tracked by the context (`post`).</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a><span data-ttu-id="0f1dd-118">リレーションシップを削除します。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-118">Removing relationships</span></span>

<span data-ttu-id="0f1dd-119">リレーションシップを削除するには参照ナビゲーションに設定して`null`、またはコレクションのナビゲーションから、関連するエンティティを削除します。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-119">You can remove a relationship by setting a reference navigation to `null`, or removing the related entity from a collection navigation.</span></span>

<span data-ttu-id="0f1dd-120">関係を削除すると、依存エンティティに副作用があることができます、カスケードに従ってリレーションシップで構成されている動作を削除します。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-120">Removing a relationship can have side effects on the dependent entity, according to the cascade delete behavior configured in the relationship.</span></span>

<span data-ttu-id="0f1dd-121">既定では、必須のリレーションシップで連鎖削除の動作が構成され、子/依存エンティティは、データベースから削除されます。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-121">By default, for required relationships, a cascade delete behavior is configured and the child/dependent entity will be deleted from the database.</span></span> <span data-ttu-id="0f1dd-122">省略可能なリレーションシップで連鎖削除が構成されていない既定では、外部キー プロパティが設定されますを null にします。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-122">For optional relationships, cascade delete is not configured by default, but the foreign key property will be set to null.</span></span>

<span data-ttu-id="0f1dd-123">参照してください[必須および省略可能なリレーションシップ](../modeling/relationships.md#required-and-optional-relationships)に関係の requiredness を構成する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-123">See [Required and Optional Relationships](../modeling/relationships.md#required-and-optional-relationships) to learn about how the requiredness of relationships can be configured.</span></span>

<span data-ttu-id="0f1dd-124">参照してください[連鎖削除](cascade-delete.md)それらを構成する方法に明示的に、慣例の選択方法、連鎖が動作を削除する方法の詳細についてが機能します。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-124">See [Cascade Delete](cascade-delete.md) for more details on how cascade delete behaviors work, how they can be configured explicitly and  how they are selected by convention.</span></span>

<span data-ttu-id="0f1dd-125">次の例では、連鎖削除が構成されている間のリレーションシップに`Blog`と`Post`ので、`post`エンティティをデータベースから削除します。</span><span class="sxs-lookup"><span data-stu-id="0f1dd-125">In the following example, a cascade delete is configured on the relationship between `Blog` and `Post`, so the `post` entity is deleted from the database.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#RemovingRelationships)]
