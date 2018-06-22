---
title: データがシード処理の EF コア
author: AndriySvyryd
ms.author: divega
ms.date: 02/23/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
ms.technology: entity-framework-core
uid: core/modeling/data-seeding
ms.openlocfilehash: 7028e1923152b27f56721dab75aae8b9c2f5ad75
ms.sourcegitcommit: 038acd91ce2f5a28d76dcd2eab72eeba225e366d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34163201"
---
# <a name="data-seeding"></a>データのシード処理

> [!NOTE]  
> この機能は、EF コア 2.1 の新機能です。

データベースに設定する初期のデータを提供する、データのシード処理できます。 異なり EF6、EF コア内でデータをシード処理に関連付けられたモデルの構成の一部としてエンティティ型。 EF コアし[移行](xref:core/managing-schemas/migrations/index)どのような挿入、更新または削除、モデルの新しいバージョンにデータベースをアップグレードするときに適用される操作の必要性を自動的に計算できます。

たとえば、これをする際シードのデータの構成、`Blog`で`OnModelCreating`:

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

リレーションシップの外部キーの値を持つエンティティを追加するを指定する必要があります。 頻繁に外部キー プロパティは、シャドウ状態でため、匿名クラス、値を設定するのには使用する必要があります。

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

使用するエンティティが追加されるをお勧め[移行](xref:core/managing-schemas/migrations/index)変更を適用します。 

また、使用することができます`context.Database.EnsureCreated()`など、テスト データベースまたはのメモリ内のプロバイダーを使用する場合は、シードのデータを含む新しいデータベースを作成します。 されている場合、データベースが既に存在する`EnsureCreated()`はどちらも更新スキーマやデータベースのシード データ。
