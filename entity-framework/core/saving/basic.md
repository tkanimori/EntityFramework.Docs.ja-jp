---
title: 基本的な保存の EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 850d842e-3fad-4ef2-be17-053768e97b9e
ms.technology: entity-framework-core
uid: core/saving/basic
ms.openlocfilehash: deead323301dc4a0ee0748b4536ddff4596b99e6
ms.sourcegitcommit: 4997314356118d0d97b04ad82e433e49bb9420a2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="basic-save"></a><span data-ttu-id="93225-102">基本の保存</span><span class="sxs-lookup"><span data-stu-id="93225-102">Basic Save</span></span>

<span data-ttu-id="93225-103">追加、変更、および、コンテキストおよびエンティティ クラスを使用してデータを削除する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="93225-103">Learn how to add, modify, and remove data using your context and entity classes.</span></span>

> [!TIP]  
> <span data-ttu-id="93225-104">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Basics/)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="93225-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Basics/) on GitHub.</span></span>

## <a name="adding-data"></a><span data-ttu-id="93225-105">データの追加</span><span class="sxs-lookup"><span data-stu-id="93225-105">Adding Data</span></span>

<span data-ttu-id="93225-106">使用して、 *DbSet.Add*エンティティ クラスの新しいインスタンスを追加するメソッド。</span><span class="sxs-lookup"><span data-stu-id="93225-106">Use the *DbSet.Add* method to add new instances of your entity classes.</span></span> <span data-ttu-id="93225-107">データを呼び出すと、データベース内に挿入される*SaveChanges*です。</span><span class="sxs-lookup"><span data-stu-id="93225-107">The data will be inserted in the database when you call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> <span data-ttu-id="93225-108">エンティティのすべてのグラフ上のすべての作業の追加、アタッチ、および Update メソッド、渡された」の説明に従って、[関連データ](related-data.md)セクションです。</span><span class="sxs-lookup"><span data-stu-id="93225-108">The Add, Attach, and Update methods all work on the full graph of entities passed to them, as described in the [Related Data](related-data.md) section.</span></span> <span data-ttu-id="93225-109">または、EntityEntry.State プロパティを使用して、1 つのエンティティの状態を設定できます。</span><span class="sxs-lookup"><span data-stu-id="93225-109">Alternately, the EntityEntry.State property can be used to set the state of just a single entity.</span></span> <span data-ttu-id="93225-110">たとえば、`context.Entry(blog).State = EntityState.Modified` のようにします。</span><span class="sxs-lookup"><span data-stu-id="93225-110">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="updating-data"></a><span data-ttu-id="93225-111">データの更新</span><span class="sxs-lookup"><span data-stu-id="93225-111">Updating Data</span></span>

<span data-ttu-id="93225-112">EF は、コンテキストによって追跡されている既存のエンティティに行われた変更を自動的に検出されます。</span><span class="sxs-lookup"><span data-stu-id="93225-112">EF will automatically detect changes made to an existing entity that is tracked by the context.</span></span> <span data-ttu-id="93225-113">これには、エンティティをデータベースから読み込み/クエリして以前に追加され、データベースに保存されているエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="93225-113">This includes entities that you load/query from the database, and entities that were previously added and saved to the database.</span></span>

<span data-ttu-id="93225-114">プロパティに割り当てられた値を単に変更し、呼び出す*SaveChanges*です。</span><span class="sxs-lookup"><span data-stu-id="93225-114">Simply modify the values assigned to properties and then call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a><span data-ttu-id="93225-115">データの削除</span><span class="sxs-lookup"><span data-stu-id="93225-115">Deleting Data</span></span>

<span data-ttu-id="93225-116">使用して、 *DbSet.Remove*するエンティティ クラスのインスタンスを削除するメソッド。</span><span class="sxs-lookup"><span data-stu-id="93225-116">Use the *DbSet.Remove* method to delete instances of you entity classes.</span></span>

<span data-ttu-id="93225-117">エンティティがデータベースに既に存在する場合、中には削除されます*SaveChanges*です。</span><span class="sxs-lookup"><span data-stu-id="93225-117">If the entity already exists in the database, it will be deleted during *SaveChanges*.</span></span> <span data-ttu-id="93225-118">エンティティが (つまり追跡追加されると)、データベースに保存されていない場合それがコンテキストから削除されますあり、使用できなくなります挿入時に*SaveChanges*と呼びます。</span><span class="sxs-lookup"><span data-stu-id="93225-118">If the entity has not yet been saved to the database (i.e. it is tracked as added) then it will be removed from the context and will no longer be inserted when *SaveChanges* is called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a><span data-ttu-id="93225-119">1 つの SaveChanges で複数の操作</span><span class="sxs-lookup"><span data-stu-id="93225-119">Multiple Operations in a single SaveChanges</span></span>

<span data-ttu-id="93225-120">1 回の呼び出しに複数の追加/更新/削除操作を組み合わせることができます*SaveChanges*です。</span><span class="sxs-lookup"><span data-stu-id="93225-120">You can combine multiple Add/Update/Remove operations into a single call to *SaveChanges*.</span></span>

> [!NOTE]  
> <span data-ttu-id="93225-121">ほとんどのデータベース プロバイダーの*SaveChanges*がトランザクションです。</span><span class="sxs-lookup"><span data-stu-id="93225-121">For most database providers, *SaveChanges* is transactional.</span></span> <span data-ttu-id="93225-122">つまり、すべての操作の成功または失敗が操作しない左部分的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="93225-122">This means  all the operations will either succeed or fail and the operations will never be left partially applied.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#MultipleOperations)]
