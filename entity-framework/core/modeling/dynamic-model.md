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

詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel)を参照してください。
