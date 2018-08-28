---
title: EF Core - 同じ DbContext 型を持つ複数のモデル間で切り替える
author: AndriySvyryd
ms.date: 12/10/2017
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 1d87efb668c12a2862583fba16a6c444b3cda9de
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994987"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="cf1af-102">同じ DbContext 型を持つ複数のモデル間で切り替える</span><span class="sxs-lookup"><span data-stu-id="cf1af-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="cf1af-103">構築されたモデル`OnModelCreating`モデルを構築する方法を変更するコンテキストにプロパティを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf1af-103">The model built in `OnModelCreating` could use a property on the context to change how the model is built.</span></span> <span data-ttu-id="cf1af-104">たとえば、特定のプロパティを除外するを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf1af-104">For example it could be used to exclude a certain property:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="cf1af-105">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="cf1af-105">IModelCacheKeyFactory</span></span>
<span data-ttu-id="cf1af-106">ただし、上記の追加の変更なしに行った場合得、同じモデルのすべての値の新しいコンテキストが作成されるたびに`IgnoreIntProperty`します。</span><span class="sxs-lookup"><span data-stu-id="cf1af-106">However if you tried doing the above without additional changes you would get the same model every time a new context is created for any value of `IgnoreIntProperty`.</span></span> <span data-ttu-id="cf1af-107">これはキャッシュ メカニズムのみを呼び出すことによって、パフォーマンスを向上させるために EF を使用して、モデルによって発生`OnModelCreating`1 回と、モデルをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="cf1af-107">This is caused by the model caching mechanism EF uses to improve the performance by only invoking `OnModelCreating` once and caching the model.</span></span>

<span data-ttu-id="cf1af-108">既定では、EF は、任意の指定したコンテキストに対して、モデルの種類が同じであること、想定しています。</span><span class="sxs-lookup"><span data-stu-id="cf1af-108">By default EF assumes that for any given context type the model will be the same.</span></span> <span data-ttu-id="cf1af-109">既定の実装でこれを実現する`IModelCacheKeyFactory`のみコンテキストの種類を含むキーを返します。</span><span class="sxs-lookup"><span data-stu-id="cf1af-109">To accomplish this the default implementation of `IModelCacheKeyFactory` returns a key that just contains the context type.</span></span> <span data-ttu-id="cf1af-110">置換する必要があります。 これを変更する、`IModelCacheKeyFactory`サービス。</span><span class="sxs-lookup"><span data-stu-id="cf1af-110">To change this you need to replace the `IModelCacheKeyFactory` service.</span></span> <span data-ttu-id="cf1af-111">新しい実装を使用して他のモデルのキーを比較できるオブジェクトを返す必要があります、`Equals`モデルに影響するすべての変数を考慮したメソッド。</span><span class="sxs-lookup"><span data-stu-id="cf1af-111">The new implementation needs to return an object that can be compared to other model keys using the `Equals` method that takes into account all the variables that affect the model:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
