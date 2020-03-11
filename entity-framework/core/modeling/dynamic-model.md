---
title: 同じ DbContext 型の複数のモデルを交互に EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/dynamic-model
ms.openlocfilehash: a160f0d382ee2a3ac7130ce1ac98eb24b3f79394
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413913"
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>同じ DbContext 型の複数のモデルを交互に使用する

`OnModelCreating` 組み込まれているモデルでは、コンテキストのプロパティを使用して、モデルの構築方法を変更できます。 たとえば、いくつかのプロパティに基づいてエンティティを異なる方法で構成するとします。

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

しかし、このコードはそのように動作しません。 EF は、モデルを構築して `OnModelCreating` 1 回だけ実行するため、パフォーマンス上の理由から結果がキャッシュされるためです。 ただし、モデルキャッシュメカニズムにフックして、さまざまなモデルを生成するプロパティを EF に認識させることができます。

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory

EF では、`IModelCacheKeyFactory` を使用してモデルのキャッシュキーを生成します。既定では、EF は、特定のコンテキスト型に対してモデルが同じであると想定しています。したがって、このサービスの既定の実装では、コンテキスト型のみを含むキーが返されます。 同じコンテキスト型からさまざまなモデルを生成するには、`IModelCacheKeyFactory` サービスを正しい実装に置き換える必要があります。生成されたキーは、モデルに影響を与えるすべての変数を考慮して、`Equals` メソッドを使用して他のモデルキーと比較されます。

次の実装では、モデルキャッシュキーを作成するときに `IgnoreIntProperty` が考慮されます。

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

最後に、コンテキストの `OnConfiguring`に新しい `IModelCacheKeyFactory` を登録します。

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel)を参照してください。
