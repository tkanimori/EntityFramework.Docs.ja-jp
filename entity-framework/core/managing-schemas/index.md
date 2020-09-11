---
title: データベース スキーマを管理する - EF Core
description: Entity Framework Core でデータベース スキーマを管理する方法の概要
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/index
ms.openlocfilehash: e4f8c82125534e9e7e0c2de552bce336a544a2aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619494"
---
# <a name="managing-database-schemas"></a>データベース スキーマを管理する

EF Core には、EF Core モデルとデータベース スキーマを同期させる主要な方法が 2 つ用意されています。いずれかを選ぶ際は、EF Core とデータベース スキーマのどちらが信頼できる情報源か判断します。

EF Core モデルを信頼できるソースとする場合は、「[移行][1]」を使用してください。 この手法では、EF Core を変更すると、それに対応するスキーマの変更がデータベースに増分的に適用され、EF Core モデルとの互換性が維持されます。

データベース スキーマを信頼できるソースとする場合は、「[リバース エンジニアリング][2]」を使用してください。 この手法では、データベース スキーマを EF Core モデルにリバース エンジニアリングすることで DbContext とエンティティ型クラスをスキャフォールディングできます。

> [!NOTE]
> [作成と削除の API][3] を使って EF Core モデルからデータベース スキーマを作成することもできます。 ただし、これらはテスト、試作、データベースの削除が認められるその他のシナリオを主に対象にしています。

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/managing-schemas/scaffolding
  [3]: xref:core/managing-schemas/ensure-created
