---
title: "EF Core と EF6 を比較する"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a6b9cd22-6803-4c6c-a4d4-21147c0a81cb
uid: efcore-and-ef6/index
ms.openlocfilehash: 4442e6931327f6a07d98aee47211ddbeed51a467
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="compare-ef-core--ef6"></a><span data-ttu-id="fa423-102">EF Core と EF6 を比較する</span><span class="sxs-lookup"><span data-stu-id="fa423-102">Compare EF Core & EF6</span></span>

<span data-ttu-id="fa423-103">Entity Framework には 2 つのバージョンがあります。Entity Framework Core と Entity Framework 6 です。</span><span class="sxs-lookup"><span data-stu-id="fa423-103">There are two versions of Entity Framework, Entity Framework Core and Entity Framework 6.</span></span>

## <a name="entity-framework-6"></a><span data-ttu-id="fa423-104">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="fa423-104">Entity Framework 6</span></span>

<span data-ttu-id="fa423-105">Entity Framework 6 (EF6) は試験の結果、信頼性が立証されたデータ アクセス テクノロジです。何年にもわたり機能が蓄積され、安定しています。</span><span class="sxs-lookup"><span data-stu-id="fa423-105">Entity Framework 6 (EF6) is a tried and tested data access technology with many years of features and stabilization.</span></span> <span data-ttu-id="fa423-106">2008 年に初めて、.NET Framework 3.5 SP1 と Visual Studio 2008 SP1 の一部としてリリースされました。</span><span class="sxs-lookup"><span data-stu-id="fa423-106">It first released in 2008, as part of .NET Framework 3.5 SP1 and Visual Studio 2008 SP1.</span></span> <span data-ttu-id="fa423-107">EF4.1 リリース以降、[EntityFramework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)として出荷されています。これは NuGet.org で現在最も人気のあるパッケージです。</span><span class="sxs-lookup"><span data-stu-id="fa423-107">Starting with the EF4.1 release it has shipped as the [EntityFramework NuGet package](https://www.nuget.org/packages/EntityFramework/) - currently the most popular package on NuGet.org.</span></span>

<span data-ttu-id="fa423-108">EF6 は製品として今後もサポートされます。しばらくの間、バグ修正や細かな改善も提供されます。</span><span class="sxs-lookup"><span data-stu-id="fa423-108">EF6 continues to be a supported product, and will continue to see bug fixes and minor improvements for some time to come.</span></span>

## <a name="entity-framework-core"></a><span data-ttu-id="fa423-109">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="fa423-109">Entity Framework Core</span></span>

<span data-ttu-id="fa423-110">Entity Framework Core (EF Core) は Entity Framework の軽量版であり、拡張性に優れ、プラットフォームに依存しません。</span><span class="sxs-lookup"><span data-stu-id="fa423-110">Entity Framework Core (EF Core) is a lightweight, extensible, and cross-platform version of Entity Framework.</span></span> <span data-ttu-id="fa423-111">EF Core は EF6 と比べ、たくさんの機能改善と新機能が導入されています。</span><span class="sxs-lookup"><span data-stu-id="fa423-111">EF Core introduces many improvements and new features when compared with EF6.</span></span> <span data-ttu-id="fa423-112">一方で、EF Core は新しいコード ベースであり、EF6 ほど成熟していません。</span><span class="sxs-lookup"><span data-stu-id="fa423-112">At the same time, EF Core is a new code base and not as mature as EF6.</span></span>

<span data-ttu-id="fa423-113">開発者にとっては EF Core の使用感は EF6 と変わらず、ほとんどの上位 API は同じまま残っています。そのため、EF6 を使用した者にとっては、EF Core は非常になじみのあるフレームワークです。</span><span class="sxs-lookup"><span data-stu-id="fa423-113">EF Core keeps the developer experience from EF6, and most of the top-level APIs remain the same too, so EF Core will feel very familiar to folks who have used EF6.</span></span> <span data-ttu-id="fa423-114">一方で、EF Core はまったく新しいコア コンポーネント セットを土台にしています。</span><span class="sxs-lookup"><span data-stu-id="fa423-114">At the same time, EF Core is built over a completely new set of core components.</span></span> <span data-ttu-id="fa423-115">つまり、EF Core は EF6 からすべての機能を自動的に継承しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="fa423-115">This means EF Core doesn't automatically inherit all the features from EF6.</span></span> <span data-ttu-id="fa423-116">一部の機能 (遅延読み込みや接続の復元性など) は今後のリリースで導入されますが、一般的にはあまり使用されない機能は EF Core には実装されません。</span><span class="sxs-lookup"><span data-stu-id="fa423-116">Some of these features will show up in future releases (such as lazy loading and connection resiliency), other less commonly used features will not be implemented in EF Core.</span></span>

<span data-ttu-id="fa423-117">拡張性に優れ、軽量の新しいコアによって、EF6 では実装されない一部の機能も EF Core に追加できました (代替キーや LINQ クエリのクライアント/データベース混合評価など)。</span><span class="sxs-lookup"><span data-stu-id="fa423-117">The new, extensible, and lightweight core has also allowed us to add some features to EF Core that will not be implemented in EF6 (such as alternate keys and mixed client/database evaluation in LINQ queries).</span></span>
