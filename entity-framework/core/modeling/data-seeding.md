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
# <a name="data-seeding"></a><span data-ttu-id="6806f-102">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="6806f-102">Data Seeding</span></span>

> [!NOTE]  
> <span data-ttu-id="6806f-103">これは EF Core 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="6806f-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="6806f-104">データのシード処理は、データベースに設定する初期のデータを提供できます。</span><span class="sxs-lookup"><span data-stu-id="6806f-104">Data seeding allows to provide initial data to populate a database.</span></span> <span data-ttu-id="6806f-105">異なり、EF Core での EF6 でデータをシード処理に関連付けられているモデルの構成の一部としてエンティティ型。</span><span class="sxs-lookup"><span data-stu-id="6806f-105">Unlike in EF6, in EF Core, seeding data is associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="6806f-106">EF Core し[移行](xref:core/managing-schemas/migrations/index)どのような挿入、更新または削除、モデルの新しいバージョンにデータベースのアップグレード時に適用される操作の必要性を自動的に計算できます。</span><span class="sxs-lookup"><span data-stu-id="6806f-106">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="6806f-107">たとえば、ことがこれを使用する構成のシード データを`Blog`で`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="6806f-107">As an example, you can use this to configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="6806f-108">リレーションシップの外部キーの値を持つエンティティを追加するを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6806f-108">To add entities that have a relationship the foreign key values need to be specified.</span></span> <span data-ttu-id="6806f-109">頻繁に外部キー プロパティは、匿名クラスの値を設定するのには使用する必要がありますのでシャドウ状態では。</span><span class="sxs-lookup"><span data-stu-id="6806f-109">Frequently the foreign key properties are in shadow state, so to be able to set the values an anonymous class should be used:</span></span>

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="6806f-110">使用することが推奨エンティティが追加されたら、[移行](xref:core/managing-schemas/migrations/index)変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="6806f-110">Once entities have been added, it is recommended to use [migrations](xref:core/managing-schemas/migrations/index) to apply changes.</span></span> 

<span data-ttu-id="6806f-111">使用する代わりに、`context.Database.EnsureCreated()`テスト データベースなどのまたはメモリ内プロバイダーを使用して、シード データを含む新しいデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="6806f-111">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider.</span></span> <span data-ttu-id="6806f-112">されている場合、データベースが既に存在する`EnsureCreated()`はどちらも更新スキーマも、データベースのシード データ。</span><span class="sxs-lookup"><span data-stu-id="6806f-112">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor the seed data in the database.</span></span>
