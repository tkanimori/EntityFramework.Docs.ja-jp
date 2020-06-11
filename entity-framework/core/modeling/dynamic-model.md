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
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a>同じ DbContext 型の複数のモデルを交互に使用する

構築されたモデルでは、 `OnModelCreating` コンテキストのプロパティを使用して、モデルの構築方法を変更できます。 たとえば、いくつかのプロパティに基づいてエンティティを異なる方法で構成するとします。

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnModelCreating)]

残念ながら、EF はモデルを構築して1回だけ実行するため、このコードはそのようには機能し `OnModelCreating` ません。そのため、パフォーマンス上の理由から結果がキャッシュされます。 ただし、モデルキャッシュメカニズムにフックして、さまざまなモデルを生成するプロパティを EF に認識させることができます。

## <a name="imodelcachekeyfactory"></a>IModelCacheKeyFactory

EF は、を使用して `IModelCacheKeyFactory` モデルのキャッシュキーを生成します。既定では、ef は、特定のコンテキスト型のモデルが同じであることを前提としています。したがって、このサービスの既定の実装では、コンテキスト型のみを含むキーが返されます。 同じコンテキスト型から異なるモデルを生成するには、サービスを適切な実装に置き換える必要があります。生成されたキーは、 `IModelCacheKeyFactory` モデルに `Equals` 影響を与えるすべての変数を考慮して、メソッドを使用して他のモデルキーと比較されます。

次の実装は、 `UseIntProperty` モデルキャッシュキーを生成するときに、を考慮します。

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicModelCacheKeyFactory.cs?name=DynamicModel)]

最後に、新しいを `IModelCacheKeyFactory` コンテキストに登録し `OnConfiguring` ます。

[!code-csharp[Main](../../../samples/core/Modeling/DynamicModel/DynamicContext.cs?name=OnConfiguring)]

詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DynamicModel)を参照してください。
