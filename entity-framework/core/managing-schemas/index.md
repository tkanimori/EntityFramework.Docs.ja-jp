---
title: データベース スキーマを管理する - EF Core
author: bricelam
ms.date: 10/30/2017
ms.openlocfilehash: c1ebe33b5575cab76a54721ef86ecbcb7ff8b98b
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994386"
---
# <a name="managing-database-schemas"></a><span data-ttu-id="cf191-102">データベース スキーマを管理する</span><span class="sxs-lookup"><span data-stu-id="cf191-102">Managing Database Schemas</span></span>
<span data-ttu-id="cf191-103">EF Core には、EF Core モデルとデータベース スキーマを同期させる主要な方法が 2 つ用意されています。いずれかを選ぶ際は、EF Core とデータベース スキーマのどちらが信頼できる情報源か判断します。</span><span class="sxs-lookup"><span data-stu-id="cf191-103">EF Core provides two primary ways of keeping your EF Core model and database schema in sync. To choose between the two, decide whether your EF Core model or the database schema is the source of truth.</span></span>

<span data-ttu-id="cf191-104">EF Core モデルを信頼できる情報源とする場合、[移行][1]を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf191-104">If you want your EF Core model to be the source of truth, use [Migrations][1].</span></span> <span data-ttu-id="cf191-105">この手法では、EF Core を変更すると、それに対応するスキーマの変更がデータベースに増分的に適用され、EF Core モデルとの互換性が維持されます。</span><span class="sxs-lookup"><span data-stu-id="cf191-105">As you make changes to your EF Core model, this approach incrementally applies the corresponding schema changes to your database so that it remains compatible with your EF Core model.</span></span>

<span data-ttu-id="cf191-106">データベース スキーマを信頼できる情報源とする場合、[リバース エンジニアリング][2]を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf191-106">Use [Reverse Engineering][2] if you want your database schema to be the source of truth.</span></span> <span data-ttu-id="cf191-107">この手法では、データベース スキーマを EF Core モデルにリバース エンジニアリングすることで DbContext とエンティティ型クラスをスキャフォールディングできます。</span><span class="sxs-lookup"><span data-stu-id="cf191-107">This approach allows you to scaffold a DbContext and the entity type classes by reverse engineering your database schema into an EF Core model.</span></span>

> [!NOTE]
> <span data-ttu-id="cf191-108">[create API と drop API][3] でも、EF Core モデルからデータベース スキーマを作成できます。</span><span class="sxs-lookup"><span data-stu-id="cf191-108">The [create and drop APIs][3] can also create the database schema from your EF Core model.</span></span> <span data-ttu-id="cf191-109">ただし、これらはテスト、試作、データベースの削除が認められるその他のシナリオを主に対象にしています。</span><span class="sxs-lookup"><span data-stu-id="cf191-109">However, they are primarily for testing, prototyping, and other scenarios where dropping the database is acceptable.</span></span>


  [1]: migrations/index.md
  [2]: scaffolding.md
  [3]: ensure-created.md
