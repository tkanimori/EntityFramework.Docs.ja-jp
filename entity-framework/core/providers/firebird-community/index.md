---
title: "FirebirdSQL データベース プロバイダー - EF Core"
author: ralmsdeveloper
ms.author: ralmsdeveloper
ms.date: 11/22/2017
ms.assetid: d0168c04-d30d-4219-98f8-a54690cea3c6
ms.technology: entity-framework-core
uid: core/providers/firebird-community/index
ms.openlocfilehash: 682988a91ef04dbd552588a537f53124b931f17d
ms.sourcegitcommit: 1cbd3d3cd92bdaf8223b8821c58200bcfed10ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="firebird-ef-core-database-provider"></a><span data-ttu-id="80d1b-102">Firebird EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="80d1b-102">Firebird EF Core Database Provider</span></span>

<span data-ttu-id="80d1b-103">このデータベース プロバイダーにより、FirebirdSQL と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="80d1b-103">This database provider allows Entity Framework Core to be used with FirebirdSQL.</span></span> <span data-ttu-id="80d1b-104">このプロバイダーは、[ralmsdeveloper/EntityFrameworkCore.FirebirdSQL GitHub プロジェクト](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="80d1b-104">The provider is maintained as part of the [ralmsdeveloper/EntityFrameworkCore.FirebirdSQL GitHub Project](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL).</span></span>

> [!NOTE]  
>
> <span data-ttu-id="80d1b-105">このプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="80d1b-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="80d1b-106">サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="80d1b-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="80d1b-107">Install</span><span class="sxs-lookup"><span data-stu-id="80d1b-107">Install</span></span>

<span data-ttu-id="80d1b-108">[EntityFrameworkCore.FirebirdSQL NuGet パッケージ](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSQL)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="80d1b-108">Install the [EntityFrameworkCore.FirebirdSQL NuGet package](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSQL).</span></span>

``` powershell
Install-Package EntityFrameworkCore.FirebirdSQL
```

## <a name="get-started"></a><span data-ttu-id="80d1b-109">開始するには</span><span class="sxs-lookup"><span data-stu-id="80d1b-109">Get Started</span></span>

<span data-ttu-id="80d1b-110">[プロジェクト サイトで入門ドキュメント](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="80d1b-110">See the [getting started documentation on the project site](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="80d1b-111">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="80d1b-111">Supported Database Engines</span></span>

* <span data-ttu-id="80d1b-112">FirebirdSql 2.5</span><span class="sxs-lookup"><span data-stu-id="80d1b-112">FirebirdSql 2.5</span></span>
* <span data-ttu-id="80d1b-113">FirebirdSql 3.X</span><span class="sxs-lookup"><span data-stu-id="80d1b-113">FirebirdSql 3.X</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="80d1b-114">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="80d1b-114">Supported Platforms</span></span>

* <span data-ttu-id="80d1b-115">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="80d1b-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="80d1b-116">.NET Core</span><span class="sxs-lookup"><span data-stu-id="80d1b-116">.NET Core</span></span>

* <span data-ttu-id="80d1b-117">Mono (4.2.0 以降)</span><span class="sxs-lookup"><span data-stu-id="80d1b-117">Mono (4.2.0 onwards)</span></span>
