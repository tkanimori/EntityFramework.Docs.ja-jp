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
# <a name="data-seeding"></a><span data-ttu-id="cfc2e-102">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="cfc2e-102">Data Seeding</span></span>

> [!NOTE]  
> <span data-ttu-id="cfc2e-103">この機能は、EF コア 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="cfc2e-103">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="cfc2e-104">データベースに設定する初期のデータを提供する、データのシード処理できます。</span><span class="sxs-lookup"><span data-stu-id="cfc2e-104">Data seeding allows to provide initial data to populate a database.</span></span> <span data-ttu-id="cfc2e-105">異なり EF6、EF コア内でデータをシード処理に関連付けられたモデルの構成の一部としてエンティティ型。</span><span class="sxs-lookup"><span data-stu-id="cfc2e-105">Unlike in EF6, in EF Core, seeding data is associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="cfc2e-106">EF コア移行自動的に計算できるどのような挿入、更新または削除、モデルの新しいバージョンにデータベースをアップグレードするときに適用する操作が必要です。</span><span class="sxs-lookup"><span data-stu-id="cfc2e-106">Then EF Core migrations can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="cfc2e-107">たとえば、これをする際シードのデータの構成、`Blog`で`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="cfc2e-107">As an example, you can use this to configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="cfc2e-108">リレーションシップの外部キーの値を持つエンティティを追加するを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cfc2e-108">To add entities that have a relationship the foreign key values need to be specified.</span></span> <span data-ttu-id="cfc2e-109">頻繁に外部キー プロパティは、シャドウ状態でため、匿名クラス、値を設定するのには使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cfc2e-109">Frequently the foreign key properties are in shadow state, so to be able to set the values an anonymous class should be used:</span></span>

[!code-csharp[Main](../../../samples/core/DataSeeding/DataSeedingContext.cs?name=PostSeed)]
