---
title: 関連データの保存 - EF Core
description: 関連エンティティのグラフの保存と Entity Framework Core でのリレーションシップの管理に関する情報
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/related-data
ms.openlocfilehash: f1cc752587e6a3eb58d070f5f573450b51986f70
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129097"
---
# <a name="saving-related-data"></a>関連データの保存

分離されたエンティティに加え、モデルに定義されたリレーションシップを活用することもできます。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/RelatedData/)は GitHub で確認できます。

## <a name="adding-a-graph-of-new-entities"></a>新しいエンティティ グループの追加

関連するいくつかの新しいエンティティを作成しているときに、いずれかのエンティティをコンテキストに追加すると、他のエンティティも同様に追加されます。

次の例では、ブログと 3 つの関連する投稿のすべてがデータベースに挿入されます。 これらの投稿が検出され、追加されるのは、`Blog.Posts` ナビゲーション プロパティ経由で到達可能であるためです。

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingGraphOfEntities)]

> [!TIP]
> 単一のエンティティの状態を設定するには、EntityEntry.State プロパティを使用します。 たとえば、「 `context.Entry(blog).State = EntityState.Modified` 」のように入力します。

## <a name="adding-a-related-entity"></a>関連エンティティの追加

コンテキストによって既に追跡されているエンティティのナビゲーション プロパティから新しいエンティティを参照した場合、そのエンティティが検出され、データベースに挿入されます。

次の例では、データベースからフェッチされた `blog` エンティティの `Posts` プロパティに `post` エンティティが追加されているため、そのエンティティが挿入されます。

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#AddingRelatedEntity)]

## <a name="changing-relationships"></a>リレーションシップの変更

エンティティのナビゲーション プロパティを変更すると、データベース内の外部キー列に対応する変更が行われます。

次の例では、`Blog` ナビゲーション プロパティが `blog` をポイントするように設定されているため、`post` エンティティが新しい `blog` エンティティに所属するように更新されます。 コンテキストによって既に追跡されているエンティティ (`post`) のナビゲーション プロパティによって参照される新しいエンティティであるため、`blog` もデータベースに挿入されることに注意してください。

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#ChangingRelationships)]

## <a name="removing-relationships"></a>リレーションシップの削除

参照ナビゲーションを `null` に設定するか、コレクション ナビゲーションから関連エンティティを削除することで、リレーションシップを削除できます。

リレーションシップの削除は、リレーションシップに構成された連鎖削除動作に従って、依存エンティティに影響を与える可能性があります。

必須リレーションシップでは、連鎖削除動作が既定で構成され、子/依存エンティティがデータベースから削除されます。 省略可能なリレーションシップでは、連鎖削除は既定では構成されていませんが、外部キー プロパティが null 設定されます。

リレーションシップが必須かどうかを構成する方法については、「[Required and Optional Relationships](xref:core/modeling/relationships#required-and-optional-relationships)」(必須リレーションシップと省略可能なリレーションシップ) を参照してください。

連鎖削除の動作方法、それらを明示的に構成する方法、および慣例に従った選択方法については、「[連鎖削除](xref:core/saving/cascade-delete)」を参照してください。

次の例では、`Blog` と `Post` 間のリレーションシップに連鎖削除が構成されているため、`post` エンティティがデータベースから削除されます。

[!code-csharp[Main](../../../samples/core/Saving/RelatedData/Sample.cs#RemovingRelationships)]
