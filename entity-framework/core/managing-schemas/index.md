---
title: データベース スキーマを管理する - EF Core
author: bricelam
ms.date: 10/30/2017
ms.openlocfilehash: 2da17865cb0192fb3e6e3396e4ca5f31fde9c52a
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655640"
---
# <a name="managing-database-schemas"></a>データベース スキーマを管理する

EF Core には、EF Core モデルとデータベース スキーマを同期させる主要な方法が 2 つ用意されています。いずれかを選ぶ際は、EF Core とデータベース スキーマのどちらが信頼できる情報源か判断します。

EF Core モデルを信頼できるソースとする場合は、「[移行][1]」を使用してください。 この手法では、EF Core を変更すると、それに対応するスキーマの変更がデータベースに増分的に適用され、EF Core モデルとの互換性が維持されます。

データベース スキーマを信頼できるソースとする場合は、「[リバース エンジニアリング][2]」を使用してください。 この手法では、データベース スキーマを EF Core モデルにリバース エンジニアリングすることで DbContext とエンティティ型クラスをスキャフォールディングできます。

> [!NOTE]
> [作成と削除の API][3] を使って EF Core モデルからデータベース スキーマを作成することもできます。 ただし、これらはテスト、試作、データベースの削除が認められるその他のシナリオを主に対象にしています。


  [1]: migrations/index.md
  [2]: scaffolding.md
  [3]: ensure-created.md
