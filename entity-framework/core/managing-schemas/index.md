---
title: データベース スキーマを管理する - EF Core
author: bricelam
ms.date: 10/30/2017
ms.openlocfilehash: 2da17865cb0192fb3e6e3396e4ca5f31fde9c52a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78412737"
---
# <a name="managing-database-schemas"></a><span data-ttu-id="fd408-102">データベース スキーマを管理する</span><span class="sxs-lookup"><span data-stu-id="fd408-102">Managing Database Schemas</span></span>

<span data-ttu-id="fd408-103">EF Core には、EF Core モデルとデータベース スキーマを同期させる主要な方法が 2 つ用意されています。いずれかを選ぶ際は、EF Core とデータベース スキーマのどちらが信頼できる情報源か判断します。</span><span class="sxs-lookup"><span data-stu-id="fd408-103">EF Core provides two primary ways of keeping your EF Core model and database schema in sync. To choose between the two, decide whether your EF Core model or the database schema is the source of truth.</span></span>

<span data-ttu-id="fd408-104">EF Core モデルを信頼できるソースとする場合は、「[移行][1]」を使用してください。</span><span class="sxs-lookup"><span data-stu-id="fd408-104">If you want your EF Core model to be the source of truth, use [Migrations][1].</span></span> <span data-ttu-id="fd408-105">この手法では、EF Core を変更すると、それに対応するスキーマの変更がデータベースに増分的に適用され、EF Core モデルとの互換性が維持されます。</span><span class="sxs-lookup"><span data-stu-id="fd408-105">As you make changes to your EF Core model, this approach incrementally applies the corresponding schema changes to your database so that it remains compatible with your EF Core model.</span></span>

<span data-ttu-id="fd408-106">データベース スキーマを信頼できるソースとする場合は、「[リバース エンジニアリング][2]」を使用してください。</span><span class="sxs-lookup"><span data-stu-id="fd408-106">Use [Reverse Engineering][2] if you want your database schema to be the source of truth.</span></span> <span data-ttu-id="fd408-107">この手法では、データベース スキーマを EF Core モデルにリバース エンジニアリングすることで DbContext とエンティティ型クラスをスキャフォールディングできます。</span><span class="sxs-lookup"><span data-stu-id="fd408-107">This approach allows you to scaffold a DbContext and the entity type classes by reverse engineering your database schema into an EF Core model.</span></span>

> [!NOTE]
> <span data-ttu-id="fd408-108">[作成と削除の API][3] を使って EF Core モデルからデータベース スキーマを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="fd408-108">The [create and drop APIs][3] can also create the database schema from your EF Core model.</span></span> <span data-ttu-id="fd408-109">ただし、これらはテスト、試作、データベースの削除が認められるその他のシナリオを主に対象にしています。</span><span class="sxs-lookup"><span data-stu-id="fd408-109">However, they are primarily for testing, prototyping, and other scenarios where dropping the database is acceptable.</span></span>


  [1]: migrations/index.md
  [2]: scaffolding.md
  [3]: ensure-created.md
