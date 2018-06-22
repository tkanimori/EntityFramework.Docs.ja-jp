---
title: データベース スキーマを管理する - EF Core
author: bricelam
ms.author: divega
ms.date: 10/30/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 765c80f43832e51471928d5f653aa12c6bd7c7ac
ms.sourcegitcommit: b467368cc350e6059fdc0949e042a41cb11e61d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
ms.locfileid: "26049384"
---
# <a name="managing-database-schemas"></a>データベース スキーマを管理する
EF Core には、EF Core モデルとデータベース スキーマを同期させる主要な方法が 2 つ用意されています。いずれかを選ぶ際は、EF Core とデータベース スキーマのどちらが信頼できる情報源か判断します。

EF Core モデルを信頼できる情報源とする場合、[移行][1]を使用します。 この手法では、EF Core を変更すると、それに対応するスキーマの変更がデータベースに増分的に適用され、EF Core モデルとの互換性が維持されます。

データベース スキーマを信頼できる情報源とする場合、[リバース エンジニアリング][2]を使用します。 この手法では、データベース スキーマを EF Core モデルにリバース エンジニアリングすることで DbContext とエンティティ型クラスをスキャフォールディングできます。

> [!NOTE]
> [create API と drop API][3] でも、EF Core モデルからデータベース スキーマを作成できます。 ただし、これらはテスト、試作、データベースの削除が認められるその他のシナリオを主に対象にしています。


  [1]: migrations/index.md
  [2]: scaffolding.md
  [3]: ensure-created.md
