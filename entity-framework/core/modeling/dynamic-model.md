---
title: "複数のモデルと同じ DbContext 型 - EF コア間で切り替える"
author: AndriySvyryd
uid: core/modeling/dynamic-model
ms.openlocfilehash: e6828c62c55ae6f48d46a20528268264c3f22b26
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>同じ DbContext 型を持つ複数のモデル間で切り替える

組み込まれているモデル`OnModelCreating`でした、プロパティを使用して、コンテキストのモデルの構築方法を変更します。 たとえばを除外する特定のプロパティに使用できます。

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory
ただし追加変更なしには、上記を実行しようとした場合は、得られる、同じモデルのすべての値の新しいコンテキストが作成されるたびに`IgnoreIntProperty`です。 これは、モデルのキャッシュのみを呼び出すことによって、パフォーマンスを向上させるために使用して EF メカニズムにより発生`OnModelCreating`とモデルをキャッシュします。

既定では、EF は、任意の指定したコンテキストに対して、モデルの種類が同じであることと仮定します。 これを実現する既定の実装`IModelCacheKeyFactory`のみ、コンテキストの種類を含むキーを返します。 置換する必要があります。 これを変更する、`IModelCacheKeyFactory`サービス。 新しい実装を使用して他のモデルのキーを比較できるオブジェクトを返す必要があります、`Equals`モデルに影響するすべての変数を考慮したメソッド。

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]
