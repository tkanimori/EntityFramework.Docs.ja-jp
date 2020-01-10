---
title: 同じ DbContext 型の複数のモデルを交互に EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: 156d5666cbd9352b274ddc70c99704ca62aeb1fd
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781132"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="c2cc4-102">同じ DbContext 型の複数のモデルを交互に使用する</span><span class="sxs-lookup"><span data-stu-id="c2cc4-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="c2cc4-103">`OnModelCreating` 組み込まれているモデルでは、コンテキストのプロパティを使用して、モデルの構築方法を変更できます。</span><span class="sxs-lookup"><span data-stu-id="c2cc4-103">The model built in `OnModelCreating` can use a property on the context to change how the model is built.</span></span> <span data-ttu-id="c2cc4-104">たとえば、いくつかのプロパティに基づいてエンティティを異なる方法で構成するとします。</span><span class="sxs-lookup"><span data-stu-id="c2cc4-104">For example, suppose you wanted to configure an entity differently based on some property:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

<span data-ttu-id="c2cc4-105">しかし、このコードはそのように動作しません。 EF は、モデルを構築して `OnModelCreating` 1 回だけ実行するため、パフォーマンス上の理由から結果がキャッシュされるためです。</span><span class="sxs-lookup"><span data-stu-id="c2cc4-105">Unfortunately, this code wouldn't work as-is, since EF builds the model and runs `OnModelCreating` only once, caching the result for performance reasons.</span></span> <span data-ttu-id="c2cc4-106">ただし、モデルキャッシュメカニズムにフックして、さまざまなモデルを生成するプロパティを EF に認識させることができます。</span><span class="sxs-lookup"><span data-stu-id="c2cc4-106">However, you can hook into the model caching mechanism to make EF aware of the property producing different models.</span></span>

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="c2cc4-107">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="c2cc4-107">IModelCacheKeyFactory</span></span>

<span data-ttu-id="c2cc4-108">EF では、`IModelCacheKeyFactory` を使用してモデルのキャッシュキーを生成します。既定では、EF は、特定のコンテキスト型に対してモデルが同じであると想定しています。したがって、このサービスの既定の実装では、コンテキスト型のみを含むキーが返されます。</span><span class="sxs-lookup"><span data-stu-id="c2cc4-108">EF uses the `IModelCacheKeyFactory` to generate cache keys for models; by default, EF assumes that for any given context type the model will be the same, so the default implementation of this service returns a key that just contains the context type.</span></span> <span data-ttu-id="c2cc4-109">同じコンテキスト型からさまざまなモデルを生成するには、`IModelCacheKeyFactory` サービスを正しい実装に置き換える必要があります。生成されたキーは、モデルに影響を与えるすべての変数を考慮して、`Equals` メソッドを使用して他のモデルキーと比較されます。</span><span class="sxs-lookup"><span data-stu-id="c2cc4-109">To produce different models from the same context type, you need to replace the `IModelCacheKeyFactory` service with the correct  implementation; the generated key will be compared to other model keys using the `Equals` method, taking into account all the variables that affect the model:</span></span>

<span data-ttu-id="c2cc4-110">次の実装では、モデルキャッシュキーを作成するときに `IgnoreIntProperty` が考慮されます。</span><span class="sxs-lookup"><span data-stu-id="c2cc4-110">The following implementation takes the `IgnoreIntProperty` into account when producing a model cache key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

<span data-ttu-id="c2cc4-111">最後に、コンテキストの `OnConfiguring`に新しい `IModelCacheKeyFactory` を登録します。</span><span class="sxs-lookup"><span data-stu-id="c2cc4-111">Finally, register your new `IModelCacheKeyFactory` in your context's `OnConfiguring`:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

<span data-ttu-id="c2cc4-112">詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c2cc4-112">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel) for more context.</span></span>
