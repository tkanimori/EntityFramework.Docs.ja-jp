---
title: "データがシード処理の EF コア"
author: AndriySvyryd
ms.author: divega
ms.date: 02/23/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
ms.technology: entity-framework-core
uid: core/modeling/data-seeding
ms.openlocfilehash: 693ffe44e247a79e01ac7c98a36472bf2c68d37f
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="data-seeding"></a>データのシード処理

> [!NOTE]  
> この機能は、EF コア 2.1 の新機能です。

データベースに設定する初期のデータを提供する、データのシード処理できます。 異なり EF6、EF コア内でデータをシード処理に関連付けられたモデルの構成の一部としてエンティティ型。 EF コア移行自動的に計算できるどのような挿入、更新または削除、モデルの新しいバージョンにデータベースをアップグレードするときに適用する操作が必要です。

たとえば、これをする際シードのデータの構成、`Blog`で`OnModelCreating`:

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

リレーションシップの外部キーの値を持つエンティティを追加するを指定する必要があります。 頻繁に外部キー プロパティは、シャドウ状態でため、匿名クラス、値を設定するのには使用する必要があります。

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=PostSeed)]
