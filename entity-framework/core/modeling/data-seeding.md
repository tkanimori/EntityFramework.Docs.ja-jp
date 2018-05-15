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
---
# <a name="data-seeding"></a><span data-ttu-id="16dbd-102">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="16dbd-102">Data Seeding</span></span>

> [!NOTE]  
> <span data-ttu-id="16dbd-103">この機能は、EF コア 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="16dbd-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="16dbd-104">データベースに設定する初期のデータを提供する、データのシード処理できます。</span><span class="sxs-lookup"><span data-stu-id="16dbd-104">Data seeding allows to provide initial data to populate a database.</span></span> <span data-ttu-id="16dbd-105">異なり EF6、EF コア内でデータをシード処理に関連付けられたモデルの構成の一部としてエンティティ型。</span><span class="sxs-lookup"><span data-stu-id="16dbd-105">Unlike in EF6, in EF Core, seeding data is associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="16dbd-106">EF コアし[移行](xref:core/managing-schemas/migrations/index)どのような挿入、更新または削除、モデルの新しいバージョンにデータベースをアップグレードするときに適用される操作の必要性を自動的に計算できます。</span><span class="sxs-lookup"><span data-stu-id="16dbd-106">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="16dbd-107">たとえば、これをする際シードのデータの構成、`Blog`で`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="16dbd-107">As an example, you can use this to configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="16dbd-108">リレーションシップの外部キーの値を持つエンティティを追加するを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16dbd-108">To add entities that have a relationship the foreign key values need to be specified.</span></span> <span data-ttu-id="16dbd-109">頻繁に外部キー プロパティは、シャドウ状態でため、匿名クラス、値を設定するのには使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16dbd-109">Frequently the foreign key properties are in shadow state, so to be able to set the values an anonymous class should be used:</span></span>

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="16dbd-110">使用するエンティティが追加されるをお勧め[移行](xref:core/managing-schemas/migrations/index)変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="16dbd-110">Once entities have been added, it is recommended to use [migrations](xref:core/managing-schemas/migrations/index) to apply changes.</span></span> 

<span data-ttu-id="16dbd-111">また、使用することができます`context.Database.EnsureCreated()`など、テスト データベースまたはのメモリ内のプロバイダーを使用する場合は、シードのデータを含む新しいデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="16dbd-111">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider.</span></span> <span data-ttu-id="16dbd-112">されている場合、データベースが既に存在する`EnsureCreated()`はどちらも更新スキーマやデータベースのシード データ。</span><span class="sxs-lookup"><span data-stu-id="16dbd-112">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor the seed data in the database.</span></span>
