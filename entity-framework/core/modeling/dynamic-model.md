---
title: 同じ DbContext 型の複数のモデルを交互に EF Core
author: AndriySvyryd
ms.date: 12/10/2017
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 034076b1595894e80b98467354f6c9f139bd7426
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655727"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="a440f-102">同じ DbContext 型の複数のモデルを交互に使用する</span><span class="sxs-lookup"><span data-stu-id="a440f-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="a440f-103">`OnModelCreating` 組み込まれているモデルでは、コンテキストのプロパティを使用して、モデルの構築方法を変更できます。</span><span class="sxs-lookup"><span data-stu-id="a440f-103">The model built in `OnModelCreating` could use a property on the context to change how the model is built.</span></span> <span data-ttu-id="a440f-104">たとえば、特定のプロパティを除外するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="a440f-104">For example it could be used to exclude a certain property:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="a440f-105">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="a440f-105">IModelCacheKeyFactory</span></span>

<span data-ttu-id="a440f-106">ただし、追加の変更なしで上記を実行しようとした場合、`IgnoreIntProperty`の任意の値に対して新しいコンテキストが作成されるたびに同じモデルが得られます。</span><span class="sxs-lookup"><span data-stu-id="a440f-106">However if you tried doing the above without additional changes you would get the same model every time a new context is created for any value of `IgnoreIntProperty`.</span></span> <span data-ttu-id="a440f-107">これは、モデルキャッシュメカニズム EF がを使用して、`OnModelCreating` 一度だけ呼び出してモデルをキャッシュすることによってパフォーマンスを向上させるために発生します。</span><span class="sxs-lookup"><span data-stu-id="a440f-107">This is caused by the model caching mechanism EF uses to improve the performance by only invoking `OnModelCreating` once and caching the model.</span></span>

<span data-ttu-id="a440f-108">既定では、EF は特定のコンテキスト型に対してモデルが同じであると想定します。</span><span class="sxs-lookup"><span data-stu-id="a440f-108">By default EF assumes that for any given context type the model will be the same.</span></span> <span data-ttu-id="a440f-109">これを実現するために、`IModelCacheKeyFactory` の既定の実装では、コンテキスト型のみを含むキーが返されます。</span><span class="sxs-lookup"><span data-stu-id="a440f-109">To accomplish this the default implementation of `IModelCacheKeyFactory` returns a key that just contains the context type.</span></span> <span data-ttu-id="a440f-110">これを変更するには、`IModelCacheKeyFactory` サービスを置き換える必要があります。</span><span class="sxs-lookup"><span data-stu-id="a440f-110">To change this you need to replace the `IModelCacheKeyFactory` service.</span></span> <span data-ttu-id="a440f-111">新しい実装では、モデルに影響を与えるすべての変数を考慮する `Equals` メソッドを使用して、他のモデルキーと比較できるオブジェクトを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="a440f-111">The new implementation needs to return an object that can be compared to other model keys using the `Equals` method that takes into account all the variables that affect the model:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
