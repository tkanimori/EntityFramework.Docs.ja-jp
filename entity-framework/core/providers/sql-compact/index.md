---
title: "Microsoft SQL Server Compact Edition データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 073f0004-3eb5-4618-ab93-0674910e1819
ms.technology: entity-framework-core
uid: core/providers/sql-compact/index
ms.openlocfilehash: d8b73621bdd363efec5bb7728886e0a0f6bdcf76
ms.sourcegitcommit: 6ed04bb05a3d05c367f0f55616807af2bf4037ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="microsoft-sql-server-compact-edition-ef-core-database-provider"></a><span data-ttu-id="9a5cd-102">Microsoft SQL Server Compact Edition EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9a5cd-102">Microsoft SQL Server Compact Edition EF Core Database Provider</span></span>

<span data-ttu-id="9a5cd-103">このデータベース プロバイダーにより、SQL Server Compact Edition と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9a5cd-103">This database provider allows Entity Framework Core to be used with SQL Server Compact Edition.</span></span> <span data-ttu-id="9a5cd-104">このプロバイダーは [ErikEJ/EntityFramework.SqlServerCompact GitHub プロジェクト](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)の一部として保守管理されます。</span><span class="sxs-lookup"><span data-stu-id="9a5cd-104">The provider is maintained as part of the [ErikEJ/EntityFramework.SqlServerCompact GitHub Project](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span></span>

> [!NOTE]  
> <span data-ttu-id="9a5cd-105">このプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="9a5cd-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="9a5cd-106">サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="9a5cd-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="9a5cd-107">インストール</span><span class="sxs-lookup"><span data-stu-id="9a5cd-107">Install</span></span>

<span data-ttu-id="9a5cd-108">SQL Server Compact Edition 4.0 を使用するには、[EntityFrameworkCore.SqlServerCompact40 NuGet パッケージ](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9a5cd-108">To work with SQL Server Compact Edition 4.0, install the [EntityFrameworkCore.SqlServerCompact40 NuGet package](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40).</span></span>

``` powershell
Install-Package EntityFrameworkCore.SqlServerCompact40
```

<span data-ttu-id="9a5cd-109">SQL Server Compact Edition 3.5 を使用するには、[EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9a5cd-109">To work with SQL Server Compact Edition 3.5, install the [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35).</span></span>

``` powershell
Install-Package EntityFrameworkCore.SqlServerCompact35
```

## <a name="get-started"></a><span data-ttu-id="9a5cd-110">開始するには</span><span class="sxs-lookup"><span data-stu-id="9a5cd-110">Get Started</span></span>

<span data-ttu-id="9a5cd-111">[プロジェクト サイトで入門ドキュメント](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9a5cd-111">See the [getting started documentation on the project site](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="9a5cd-112">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="9a5cd-112">Supported Database Engines</span></span>

* <span data-ttu-id="9a5cd-113">SQL Server Compact Edition 3.5</span><span class="sxs-lookup"><span data-stu-id="9a5cd-113">SQL Server Compact Edition 3.5</span></span>

* <span data-ttu-id="9a5cd-114">SQL Server Compact Edition 4.0</span><span class="sxs-lookup"><span data-stu-id="9a5cd-114">SQL Server Compact Edition 4.0</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="9a5cd-115">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="9a5cd-115">Supported Platforms</span></span>

* <span data-ttu-id="9a5cd-116">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="9a5cd-116">.NET Framework (4.5.1 onwards)</span></span>
