---
title: データの保存 - EF Core
description: Entity Framework Core を使用したデータ保存の概要
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/index
ms.openlocfilehash: 7a31f92aff20f4026b984065e53a2dcc77ed46e7
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063544"
---
# <a name="saving-data"></a><span data-ttu-id="07f4d-103">データの保存</span><span class="sxs-lookup"><span data-stu-id="07f4d-103">Saving Data</span></span>

<span data-ttu-id="07f4d-104">各コンテキスト インスタンスには、データベースに書き込む必要がある変更の記録を担う `ChangeTracker` があります。</span><span class="sxs-lookup"><span data-stu-id="07f4d-104">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="07f4d-105">エンティティ クラスのインスタンスを変更すると、変更内容が `ChangeTracker` で記録され、`SaveChanges` を呼び出したときにデータベースに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="07f4d-105">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="07f4d-106">このデータベース プロバイダーは、変更をデータベース固有の操作 (リレーショナル データベースの `INSERT`、`UPDATE`、`DELETE` コマンドなど) に変換する作業を担当します。</span><span class="sxs-lookup"><span data-stu-id="07f4d-106">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
