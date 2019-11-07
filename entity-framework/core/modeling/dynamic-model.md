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
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>同じ DbContext 型の複数のモデルを交互に使用する

`OnModelCreating` 組み込まれているモデルでは、コンテキストのプロパティを使用して、モデルの構築方法を変更できます。 たとえば、特定のプロパティを除外するために使用できます。

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory

ただし、追加の変更なしで上記を実行しようとした場合、`IgnoreIntProperty`の任意の値に対して新しいコンテキストが作成されるたびに同じモデルが得られます。 これは、モデルキャッシュメカニズム EF がを使用して、`OnModelCreating` 一度だけ呼び出してモデルをキャッシュすることによってパフォーマンスを向上させるために発生します。

既定では、EF は特定のコンテキスト型に対してモデルが同じであると想定します。 これを実現するために、`IModelCacheKeyFactory` の既定の実装では、コンテキスト型のみを含むキーが返されます。 これを変更するには、`IModelCacheKeyFactory` サービスを置き換える必要があります。 新しい実装では、モデルに影響を与えるすべての変数を考慮する `Equals` メソッドを使用して、他のモデルキーと比較できるオブジェクトを返す必要があります。

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
