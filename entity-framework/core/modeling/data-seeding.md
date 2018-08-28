---
title: データのシード処理 - EF Core
author: AndriySvyryd
ms.date: 02/23/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 48ba2389de4b57dbe4c2b2124911c71440d45556
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994480"
---
# <a name="data-seeding"></a>データのシード処理

> [!NOTE]  
> これは EF Core 2.1 の新機能です。

データのシード処理は、データベースに設定する初期のデータを提供できます。 異なり、EF Core での EF6 でデータをシード処理に関連付けられているモデルの構成の一部としてエンティティ型。 EF Core し[移行](xref:core/managing-schemas/migrations/index)どのような挿入、更新または削除、モデルの新しいバージョンにデータベースのアップグレード時に適用される操作の必要性を自動的に計算できます。

たとえば、ことがこれを使用する構成のシード データを`Blog`で`OnModelCreating`:

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

リレーションシップの外部キーの値を持つエンティティを追加するを指定する必要があります。 頻繁に外部キー プロパティは、匿名クラスの値を設定するのには使用する必要がありますのでシャドウ状態では。

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

使用することが推奨エンティティが追加されたら、[移行](xref:core/managing-schemas/migrations/index)変更を適用します。 

使用する代わりに、`context.Database.EnsureCreated()`テスト データベースなどのまたはメモリ内プロバイダーを使用して、シード データを含む新しいデータベースを作成します。 されている場合、データベースが既に存在する`EnsureCreated()`はどちらも更新スキーマも、データベースのシード データ。
