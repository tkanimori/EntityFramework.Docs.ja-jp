---
title: 同じ DbContext 型の複数のモデルを交互に EF Core
description: Entity Framework Core を使用して同じ DbContext 型の複数のモデルを交互に切り替える
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 436cb46e002438f391c654b64efdfacf494d580d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617529"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="a9545-103">同じ DbContext 型の複数のモデルを交互に使用する</span><span class="sxs-lookup"><span data-stu-id="a9545-103">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="a9545-104">構築されたモデルでは、 `OnModelCreating` コンテキストのプロパティを使用して、モデルの構築方法を変更できます。</span><span class="sxs-lookup"><span data-stu-id="a9545-104">The model built in `OnModelCreating` can use a property on the context to change how the model is built.</span></span> <span data-ttu-id="a9545-105">たとえば、いくつかのプロパティに基づいてエンティティを異なる方法で構成するとします。</span><span class="sxs-lookup"><span data-stu-id="a9545-105">For example, suppose you wanted to configure an entity differently based on some property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

<span data-ttu-id="a9545-106">残念ながら、EF はモデルを構築して1回だけ実行するため、このコードはそのようには機能し `OnModelCreating` ません。そのため、パフォーマンス上の理由から結果がキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="a9545-106">Unfortunately, this code wouldn't work as-is, since EF builds the model and runs `OnModelCreating` only once, caching the result for performance reasons.</span></span> <span data-ttu-id="a9545-107">ただし、モデルキャッシュメカニズムにフックして、さまざまなモデルを生成するプロパティを EF に認識させることができます。</span><span class="sxs-lookup"><span data-stu-id="a9545-107">However, you can hook into the model caching mechanism to make EF aware of the property producing different models.</span></span>

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="a9545-108">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="a9545-108">IModelCacheKeyFactory</span></span>

<span data-ttu-id="a9545-109">EF は、を使用して `IModelCacheKeyFactory` モデルのキャッシュキーを生成します。既定では、ef は、特定のコンテキスト型のモデルが同じであることを前提としています。したがって、このサービスの既定の実装では、コンテキスト型のみを含むキーが返されます。</span><span class="sxs-lookup"><span data-stu-id="a9545-109">EF uses the `IModelCacheKeyFactory` to generate cache keys for models; by default, EF assumes that for any given context type the model will be the same, so the default implementation of this service returns a key that just contains the context type.</span></span> <span data-ttu-id="a9545-110">同じコンテキスト型から異なるモデルを生成するには、サービスを適切な実装に置き換える必要があります。生成されたキーは、 `IModelCacheKeyFactory` モデルに `Equals` 影響を与えるすべての変数を考慮して、メソッドを使用して他のモデルキーと比較されます。</span><span class="sxs-lookup"><span data-stu-id="a9545-110">To produce different models from the same context type, you need to replace the `IModelCacheKeyFactory` service with the correct implementation; the generated key will be compared to other model keys using the `Equals` method, taking into account all the variables that affect the model.</span></span>

<span data-ttu-id="a9545-111">次の実装は、 `UseIntProperty` モデルキャッシュキーを生成するときに、を考慮します。</span><span class="sxs-lookup"><span data-stu-id="a9545-111">The following implementation takes the `UseIntProperty` into account when producing a model cache key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

<span data-ttu-id="a9545-112">最後に、新しいを `IModelCacheKeyFactory` コンテキストに登録し `OnConfiguring` ます。</span><span class="sxs-lookup"><span data-stu-id="a9545-112">Finally, register your new `IModelCacheKeyFactory` in your context's `OnConfiguring`:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

<span data-ttu-id="a9545-113">詳細なコンテキストについては、 [完全なサンプルプロジェクト](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a9545-113">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) for more context.</span></span>
