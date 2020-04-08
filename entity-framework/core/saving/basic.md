---
title: 基本の保存 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 850d842e-3fad-4ef2-be17-053768e97b9e
uid: core/saving/basic
ms.openlocfilehash: 066d67d6104316832a33f5a3648f1f2fa6cc9c50
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413685"
---
# <a name="basic-save"></a><span data-ttu-id="967f1-102">基本の保存</span><span class="sxs-lookup"><span data-stu-id="967f1-102">Basic Save</span></span>

<span data-ttu-id="967f1-103">コンテキストとエンティティ クラスを使用してデータを追加、変更、および削除する方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="967f1-103">Learn how to add, modify, and remove data using your context and entity classes.</span></span>

> [!TIP]  
> <span data-ttu-id="967f1-104">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="967f1-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) on GitHub.</span></span>

## <a name="adding-data"></a><span data-ttu-id="967f1-105">データの追加</span><span class="sxs-lookup"><span data-stu-id="967f1-105">Adding Data</span></span>

<span data-ttu-id="967f1-106">エンティティ クラスの新しいインスタンスを追加するには、*DbSet.Add* メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="967f1-106">Use the *DbSet.Add* method to add new instances of your entity classes.</span></span> <span data-ttu-id="967f1-107">データは、*SaveChanges* が呼び出されたときにデータベースに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="967f1-107">The data will be inserted in the database when you call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> <span data-ttu-id="967f1-108">Add、Attach、および Update メソッドは、そのすべてが「[関連データ](related-data.md)」で説明するように、すべてのエンティティで動作します。</span><span class="sxs-lookup"><span data-stu-id="967f1-108">The Add, Attach, and Update methods all work on the full graph of entities passed to them, as described in the [Related Data](related-data.md) section.</span></span> <span data-ttu-id="967f1-109">EntityEntry.State プロパティを使用して、単一のエンティティの状態を設定できます。</span><span class="sxs-lookup"><span data-stu-id="967f1-109">Alternately, the EntityEntry.State property can be used to set the state of just a single entity.</span></span> <span data-ttu-id="967f1-110">たとえば、「 `context.Entry(blog).State = EntityState.Modified` 」のように入力します。</span><span class="sxs-lookup"><span data-stu-id="967f1-110">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="updating-data"></a><span data-ttu-id="967f1-111">データの更新</span><span class="sxs-lookup"><span data-stu-id="967f1-111">Updating Data</span></span>

<span data-ttu-id="967f1-112">EF は、コンテキストによって追跡されている既存のエンティティに対して行われた変更を自動的に検出します。</span><span class="sxs-lookup"><span data-stu-id="967f1-112">EF will automatically detect changes made to an existing entity that is tracked by the context.</span></span> <span data-ttu-id="967f1-113">これには、データベースから読み込む/クエリするエンティティと、既に追加され、データベースに保存されているエンティティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="967f1-113">This includes entities that you load/query from the database, and entities that were previously added and saved to the database.</span></span>

<span data-ttu-id="967f1-114">プロパティに割り当てられる値を変更した後、*SaveChanges* を呼び出すだけです。</span><span class="sxs-lookup"><span data-stu-id="967f1-114">Simply modify the values assigned to properties and then call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a><span data-ttu-id="967f1-115">データの削除</span><span class="sxs-lookup"><span data-stu-id="967f1-115">Deleting Data</span></span>

<span data-ttu-id="967f1-116">エンティティ クラスのインスタンスを削除するには、*DbSet.Remove* メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="967f1-116">Use the *DbSet.Remove* method to delete instances of your entity classes.</span></span>

<span data-ttu-id="967f1-117">エンティティが既にデータベースに存在する場合は、*SaveChanges* の処理中に削除されます。</span><span class="sxs-lookup"><span data-stu-id="967f1-117">If the entity already exists in the database, it will be deleted during *SaveChanges*.</span></span> <span data-ttu-id="967f1-118">エンティティがまだデータベースに保存されていない (追加時に追跡される) 場合は、コンテキストから削除され、*SaveChanges* が呼び出されたときにエンティティが挿入されることはなくなります。</span><span class="sxs-lookup"><span data-stu-id="967f1-118">If the entity has not yet been saved to the database (that is, it is tracked as added) then it will be removed from the context and will no longer be inserted when *SaveChanges* is called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a><span data-ttu-id="967f1-119">1 つの SaveChanges 内の複数の操作</span><span class="sxs-lookup"><span data-stu-id="967f1-119">Multiple Operations in a single SaveChanges</span></span>

<span data-ttu-id="967f1-120">複数の追加/更新/削除操作を組み合わせて、*SaveChanges* の 1 回の呼び出しで使用できます。</span><span class="sxs-lookup"><span data-stu-id="967f1-120">You can combine multiple Add/Update/Remove operations into a single call to *SaveChanges*.</span></span>

> [!NOTE]  
> <span data-ttu-id="967f1-121">ほとんどのデータベース プロバイダーでは、*SaveChanges* はトランザクションです。</span><span class="sxs-lookup"><span data-stu-id="967f1-121">For most database providers, *SaveChanges* is transactional.</span></span> <span data-ttu-id="967f1-122">これは、すべての操作が成功するか失敗するかのいずれかであり、操作の一部のみが適用されることはないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="967f1-122">This means  all the operations will either succeed or fail and the operations will never be left partially applied.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#MultipleOperations)]
