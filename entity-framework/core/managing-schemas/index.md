---
title: データベース スキーマを管理する - EF Core
author: bricelam
ms.author: divega
ms.date: 10/30/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 765c80f43832e51471928d5f653aa12c6bd7c7ac
ms.sourcegitcommit: b467368cc350e6059fdc0949e042a41cb11e61d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
ms.locfileid: "26049384"
---
# <a name="managing-database-schemas"></a><span data-ttu-id="a9c98-102">データベース スキーマを管理する</span><span class="sxs-lookup"><span data-stu-id="a9c98-102">Managing Database Schemas</span></span>
<span data-ttu-id="a9c98-103">EF Core には、EF Core モデルとデータベース スキーマを同期させる主要な方法が 2 つ用意されています。いずれかを選ぶ際は、EF Core とデータベース スキーマのどちらが信頼できる情報源か判断します。</span><span class="sxs-lookup"><span data-stu-id="a9c98-103">EF Core provides two primary ways of keeping your EF Core model and database schema in sync. To choose between the two, decide whether your EF Core model or the database schema is the source of truth.</span></span>

<span data-ttu-id="a9c98-104">EF Core モデルを信頼できる情報源とする場合、[移行][1]を使用します。</span><span class="sxs-lookup"><span data-stu-id="a9c98-104">If you want your EF Core model to be the source of truth, use [Migrations][1].</span></span> <span data-ttu-id="a9c98-105">この手法では、EF Core を変更すると、それに対応するスキーマの変更がデータベースに増分的に適用され、EF Core モデルとの互換性が維持されます。</span><span class="sxs-lookup"><span data-stu-id="a9c98-105">As you make changes to your EF Core model, this approach incrementally applies the corresponding schema changes to your database so that it remains compatible with your EF Core model.</span></span>

<span data-ttu-id="a9c98-106">データベース スキーマを信頼できる情報源とする場合、[リバース エンジニアリング][2]を使用します。</span><span class="sxs-lookup"><span data-stu-id="a9c98-106">Use [Reverse Engineering][2] if you want your database schema to be the source of truth.</span></span> <span data-ttu-id="a9c98-107">この手法では、データベース スキーマを EF Core モデルにリバース エンジニアリングすることで DbContext とエンティティ型クラスをスキャフォールディングできます。</span><span class="sxs-lookup"><span data-stu-id="a9c98-107">This approach allows you to scaffold a DbContext and the entity type classes by reverse engineering your database schema into an EF Core model.</span></span>

> [!NOTE]
> <span data-ttu-id="a9c98-108">[create API と drop API][3] でも、EF Core モデルからデータベース スキーマを作成できます。</span><span class="sxs-lookup"><span data-stu-id="a9c98-108">The [create and drop APIs][3] can also create the database schema from your EF Core model.</span></span> <span data-ttu-id="a9c98-109">ただし、これらはテスト、試作、データベースの削除が認められるその他のシナリオを主に対象にしています。</span><span class="sxs-lookup"><span data-stu-id="a9c98-109">However, they are primarily for testing, prototyping, and other scenarios where dropping the database is acceptable.</span></span>


  [1]: migrations/index.md
  [2]: scaffolding.md
  [3]: ensure-created.md
