---
title: 同じ DbContext 型の複数のモデルを交互に EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 35743b5ba87bb4239d7f758320f9facccc74330f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664222"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="8cb68-102">同じ DbContext 型の複数のモデルを交互に使用する</span><span class="sxs-lookup"><span data-stu-id="8cb68-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="8cb68-103">構築されたモデルでは、 `OnModelCreating` コンテキストのプロパティを使用して、モデルの構築方法を変更できます。</span><span class="sxs-lookup"><span data-stu-id="8cb68-103">The model built in `OnModelCreating` can use a property on the context to change how the model is built.</span></span> <span data-ttu-id="8cb68-104">たとえば、いくつかのプロパティに基づいてエンティティを異なる方法で構成するとします。</span><span class="sxs-lookup"><span data-stu-id="8cb68-104">For example, suppose you wanted to configure an entity differently based on some property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

<span data-ttu-id="8cb68-105">残念ながら、EF はモデルを構築して1回だけ実行するため、このコードはそのようには機能し `OnModelCreating` ません。そのため、パフォーマンス上の理由から結果がキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="8cb68-105">Unfortunately, this code wouldn't work as-is, since EF builds the model and runs `OnModelCreating` only once, caching the result for performance reasons.</span></span> <span data-ttu-id="8cb68-106">ただし、モデルキャッシュメカニズムにフックして、さまざまなモデルを生成するプロパティを EF に認識させることができます。</span><span class="sxs-lookup"><span data-stu-id="8cb68-106">However, you can hook into the model caching mechanism to make EF aware of the property producing different models.</span></span>

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="8cb68-107">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="8cb68-107">IModelCacheKeyFactory</span></span>

<span data-ttu-id="8cb68-108">EF は、を使用して `IModelCacheKeyFactory` モデルのキャッシュキーを生成します。既定では、ef は、特定のコンテキスト型のモデルが同じであることを前提としています。したがって、このサービスの既定の実装では、コンテキスト型のみを含むキーが返されます。</span><span class="sxs-lookup"><span data-stu-id="8cb68-108">EF uses the `IModelCacheKeyFactory` to generate cache keys for models; by default, EF assumes that for any given context type the model will be the same, so the default implementation of this service returns a key that just contains the context type.</span></span> <span data-ttu-id="8cb68-109">同じコンテキスト型から異なるモデルを生成するには、サービスを適切な実装に置き換える必要があります。生成されたキーは、 `IModelCacheKeyFactory` モデルに `Equals` 影響を与えるすべての変数を考慮して、メソッドを使用して他のモデルキーと比較されます。</span><span class="sxs-lookup"><span data-stu-id="8cb68-109">To produce different models from the same context type, you need to replace the `IModelCacheKeyFactory` service with the correct implementation; the generated key will be compared to other model keys using the `Equals` method, taking into account all the variables that affect the model.</span></span>

<span data-ttu-id="8cb68-110">次の実装は、 `UseIntProperty` モデルキャッシュキーを生成するときに、を考慮します。</span><span class="sxs-lookup"><span data-stu-id="8cb68-110">The following implementation takes the `UseIntProperty` into account when producing a model cache key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

<span data-ttu-id="8cb68-111">最後に、新しいを `IModelCacheKeyFactory` コンテキストに登録し `OnConfiguring` ます。</span><span class="sxs-lookup"><span data-stu-id="8cb68-111">Finally, register your new `IModelCacheKeyFactory` in your context's `OnConfiguring`:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

<span data-ttu-id="8cb68-112">詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8cb68-112">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) for more context.</span></span>
