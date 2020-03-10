---
title: データの保存 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ef044629-feca-4fd1-a48f-d208daedaf92
uid: core/saving/index
ms.openlocfilehash: c610ea2a9138482f93d2d54c9085ef827af276c8
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413097"
---
# <a name="saving-data"></a><span data-ttu-id="7d7e3-102">データの保存</span><span class="sxs-lookup"><span data-stu-id="7d7e3-102">Saving Data</span></span>

<span data-ttu-id="7d7e3-103">各コンテキスト インスタンスには、データベースに書き込む必要がある変更の記録を担う `ChangeTracker` があります。</span><span class="sxs-lookup"><span data-stu-id="7d7e3-103">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="7d7e3-104">エンティティ クラスのインスタンスを変更すると、変更内容が `ChangeTracker` で記録され、`SaveChanges` を呼び出したときにデータベースに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="7d7e3-104">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="7d7e3-105">このデータベース プロバイダーは、変更をデータベース固有の操作 (リレーショナル データベースの `INSERT`、`UPDATE`、`DELETE` コマンドなど) に変換する作業を担当します。</span><span class="sxs-lookup"><span data-stu-id="7d7e3-105">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
