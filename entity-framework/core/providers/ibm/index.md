---
title: "IBM Data Server (DB2) データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 02/15/2017
ms.assetid: 825e5332-5aa3-4600-9efb-ab71aaff59ec
ms.technology: entity-framework-core
uid: core/providers/ibm/index
ms.openlocfilehash: a9caa8df63850d4f6b5f2164dad7ac5af7504076
ms.sourcegitcommit: 6ed04bb05a3d05c367f0f55616807af2bf4037ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="ibm-data-server-db2-ef-core-database-providers"></a><span data-ttu-id="54759-102">IBM Data Server (DB2) EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="54759-102">IBM Data Server (DB2) EF Core Database Providers</span></span>

<span data-ttu-id="54759-103">このデータベース プロバイダーにより、IBM データ サーバーと共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="54759-103">This database provider allows Entity Framework Core to be used with IBM Data Server.</span></span> <span data-ttu-id="54759-104">このプロバイダーは IBM によって保守管理されます。</span><span class="sxs-lookup"><span data-stu-id="54759-104">The provider is maintained by IBM.</span></span>

> [!NOTE]  
> <span data-ttu-id="54759-105">このプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="54759-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="54759-106">サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="54759-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="54759-107">インストール</span><span class="sxs-lookup"><span data-stu-id="54759-107">Install</span></span>

<span data-ttu-id="54759-108">Windows 上で IBM データ サーバーを操作するには、[IBM.EntityFrameworkCore NuGet パッケージ](https://www.nuget.org/packages/IBM.EntityFrameworkCore)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="54759-108">To work with IBM Data Server on Windows, install the [IBM.EntityFrameworkCore NuGet package](https://www.nuget.org/packages/IBM.EntityFrameworkCore).</span></span>

``` powershell
Install-Package IBM.EntityFrameworkCore
```

<span data-ttu-id="54759-109">Linux 上で IBM データ サーバーを操作するには、[IBM.EntityFrameworkCore-lnx NuGet パッケージ](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="54759-109">To work with IBM Data Server on Linux, install the [IBM.EntityFrameworkCore-lnx NuGet package](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx).</span></span>

``` powershell
Install-Package IBM.EntityFrameworkCore-lnx
```

## <a name="get-started"></a><span data-ttu-id="54759-110">開始するには</span><span class="sxs-lookup"><span data-stu-id="54759-110">Get Started</span></span>

[<span data-ttu-id="54759-111">IBM .NET Provider for .NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="54759-111">Getting started with IBM .NET Provider for .NET Core</span></span>](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/DB2DotnetCore?lang=en)

## <a name="supported-database-engines"></a><span data-ttu-id="54759-112">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="54759-112">Supported Database Engines</span></span>

* <span data-ttu-id="54759-113">zOS</span><span class="sxs-lookup"><span data-stu-id="54759-113">zOS</span></span>
* <span data-ttu-id="54759-114">IBM dashDB を含む LUW</span><span class="sxs-lookup"><span data-stu-id="54759-114">LUW including IBM dashDB</span></span>
* <span data-ttu-id="54759-115">IBM I</span><span class="sxs-lookup"><span data-stu-id="54759-115">IBM I</span></span>
* <span data-ttu-id="54759-116">Informix</span><span class="sxs-lookup"><span data-stu-id="54759-116">Informix</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="54759-117">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="54759-117">Supported Platforms</span></span>

* <span data-ttu-id="54759-118">.NET Framework (4.6 以降)</span><span class="sxs-lookup"><span data-stu-id="54759-118">.NET Framework (4.6 onwards)</span></span>
* <span data-ttu-id="54759-119">.NET Core</span><span class="sxs-lookup"><span data-stu-id="54759-119">.NET Core</span></span>
