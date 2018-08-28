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
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>同じ DbContext 型を持つ複数のモデル間で切り替える

構築されたモデル`OnModelCreating`モデルを構築する方法を変更するコンテキストにプロパティを使用できます。 たとえば、特定のプロパティを除外するを使用できます。

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory
ただし、上記の追加の変更なしに行った場合得、同じモデルのすべての値の新しいコンテキストが作成されるたびに`IgnoreIntProperty`します。 これはキャッシュ メカニズムのみを呼び出すことによって、パフォーマンスを向上させるために EF を使用して、モデルによって発生`OnModelCreating`1 回と、モデルをキャッシュします。

既定では、EF は、任意の指定したコンテキストに対して、モデルの種類が同じであること、想定しています。 既定の実装でこれを実現する`IModelCacheKeyFactory`のみコンテキストの種類を含むキーを返します。 置換する必要があります。 これを変更する、`IModelCacheKeyFactory`サービス。 新しい実装を使用して他のモデルのキーを比較できるオブジェクトを返す必要があります、`Equals`モデルに影響するすべての変数を考慮したメソッド。

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
