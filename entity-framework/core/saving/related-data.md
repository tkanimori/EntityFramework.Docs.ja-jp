---
title: "関連するデータの EF コアの保存"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 07b6680f-ffcf-412c-9857-f997486b386c
ms.technology: entity-framework-core
uid: core/saving/related-data
ms.openlocfilehash: 078879163002cb66e0f0f439415789963181ec15
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="saving-related-data"></a>関連するデータの保存

分離のエンティティだけでなく行うことも、モデルで定義されているリレーションシップを使用します。

> [!TIP]  
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/RelatedData/)GitHub でします。

## <a name="adding-a-graph-of-new-entities"></a>新しいエンティティのグラフを追加します。

いくつかの新しい関連エンティティを作成する場合、コンテキストへのうちの 1 つの追加により、他のユーザーを追加するされます。

次の例では、ブログと 3 つの関連する投稿はすべて、データベースに挿入します。 投稿が検出され、追加、経由で到達可能であるため、`Blog.Posts`ナビゲーション プロパティ。

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

## <a name="adding-a-related-entity"></a>関連するエンティティの追加

コンテキストによって既に追跡されているエンティティのナビゲーション プロパティから、新しいエンティティを参照する場合、エンティティが検出され、データベースに挿入します。

次の例で、`post`に追加されるため、エンティティが挿入された、`Posts`のプロパティ、`blog`データベースからフェッチされているエンティティ。

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a>リレーションシップを変更します。

エンティティのナビゲーション プロパティを変更すると、対応する変更部分は、データベースの外部キー列になります。

次の例で、`post`新しいに属しているエンティティが更新された`blog`エンティティのためその`Blog` をポイントするナビゲーション プロパティが設定`blog`です。 なお`blog`コンテキストによって既に追跡されているエンティティのナビゲーション プロパティによって参照される新しいエンティティであるために、データベースに挿入もされます (`post`)。

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a>リレーションシップを削除します。

リレーションシップを削除するには参照ナビゲーションに設定して`null`、またはコレクションのナビゲーションから、関連するエンティティを削除します。

関係を削除すると、依存エンティティに副作用があることができます、カスケードに従ってリレーションシップで構成されている動作を削除します。

既定では、必須のリレーションシップで連鎖削除の動作が構成され、子/依存エンティティは、データベースから削除されます。 省略可能なリレーションシップで連鎖削除が構成されていない既定では、外部キー プロパティが設定されますを null にします。

参照してください[必須および省略可能なリレーションシップ](../modeling/relationships.md#required-and-optional-relationships)に関係の requiredness を構成する方法について説明します。

参照してください[連鎖削除](cascade-delete.md)それらを構成する方法に明示的に、慣例の選択方法、連鎖が動作を削除する方法の詳細についてが機能します。

次の例では、連鎖削除が構成されている間のリレーションシップに`Blog`と`Post`ので、`post`エンティティをデータベースから削除します。

[!code-csharp[Main](../../../samples/core/Saving/Saving/RelatedData/Sample.cs#RemovingRelationships)]
