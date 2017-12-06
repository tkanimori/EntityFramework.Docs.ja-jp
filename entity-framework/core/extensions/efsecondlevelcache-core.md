---
title: "ツールと拡張機能 - EFSecondLevelCache.Core EF コア"
author: ErikEJ
ms.author: divega
ms.date: 01/19/2017
ms.assetid: 6021D246-151D-4634-B0CB-413BAAA82D17
ms.technology: entity-framework-core
uid: core/extensions/efsecondlevelcache-core
ms.openlocfilehash: d54a770bae7c769613cfc14306880a28e923cb05
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="efsecondlevelcachecore-extension"></a><span data-ttu-id="d632e-102">EFSecondLevelCache.Core 拡張機能</span><span class="sxs-lookup"><span data-stu-id="d632e-102">EFSecondLevelCache.Core Extension</span></span>

> [!NOTE]  
> <span data-ttu-id="d632e-103">この拡張機能は、Entity Framework Core プロジェクトの一部としては維持されません。</span><span class="sxs-lookup"><span data-stu-id="d632e-103">This extension is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="d632e-104">サード パーティ製の拡張機能を考慮する場合は、品質、ライセンス、サポートなど、要件を満たしていることを確認するを評価することを確認します。</span><span class="sxs-lookup"><span data-stu-id="d632e-104">When considering a third party extension, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

<span data-ttu-id="d632e-105">第 2 レベルのライブラリをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="d632e-105">Second Level Caching Library.</span></span> <span data-ttu-id="d632e-106">第 2 レベル キャッシュは、クエリ キャッシュです。</span><span class="sxs-lookup"><span data-stu-id="d632e-106">Second level caching is a query cache.</span></span> <span data-ttu-id="d632e-107">EF コマンドの結果は、同じ EF コマンドは、データベースに対してもう一度実行するのではなく、キャッシュからのデータを取得するように、キャッシュに格納されます。</span><span class="sxs-lookup"><span data-stu-id="d632e-107">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span>

<span data-ttu-id="d632e-108">次のリソースを使用すると、EFSecondLevelCache.Core で作業を開始できます。</span><span class="sxs-lookup"><span data-stu-id="d632e-108">The following resource will help you get started with EFSecondLevelCache.Core.</span></span>
* [<span data-ttu-id="d632e-109">EFSecondLevelCache.Core GitHub リポジトリ</span><span class="sxs-lookup"><span data-stu-id="d632e-109">EFSecondLevelCache.Core GitHub repository</span></span>](https://github.com/VahidN/EFSecondLevelCache.Core/)
