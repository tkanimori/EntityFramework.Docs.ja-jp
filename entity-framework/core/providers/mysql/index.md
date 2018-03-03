---
title: "MySQL データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 4900b882-79c5-40d2-a44a-ccb0292f6ed9
ms.technology: entity-framework-core
uid: core/providers/mysql/index
ms.openlocfilehash: 1500d017cb463c3f394131a79b9063ff90cce5e2
ms.sourcegitcommit: 6ed04bb05a3d05c367f0f55616807af2bf4037ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="mysql-ef-core-database-provider"></a><span data-ttu-id="5def5-102">MySQL EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="5def5-102">MySQL EF Core Database Provider</span></span>

<span data-ttu-id="5def5-103">このデータベース プロバイダーにより、MySQL と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="5def5-103">This database provider allows Entity Framework Core to be used with MySQL.</span></span> <span data-ttu-id="5def5-104">このプロバイダーは [MySQL プロジェクト](http://dev.mysql.com)の一部として保守管理されます。</span><span class="sxs-lookup"><span data-stu-id="5def5-104">The provider is maintained as part of the [MySQL project](http://dev.mysql.com).</span></span>

> [!WARNING]  
> <span data-ttu-id="5def5-105">このプロバイダーはプレリリースです。</span><span class="sxs-lookup"><span data-stu-id="5def5-105">This provider is pre-release.</span></span>

> [!NOTE]  
> <span data-ttu-id="5def5-106">このプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="5def5-106">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="5def5-107">サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="5def5-107">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="5def5-108">インストール</span><span class="sxs-lookup"><span data-stu-id="5def5-108">Install</span></span>

<span data-ttu-id="5def5-109">[MySql.Data.EntityFrameworkCore NuGet パッケージ](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="5def5-109">Install the [MySql.Data.EntityFrameworkCore NuGet package](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore).</span></span>

``` powershell
Install-Package MySql.Data.EntityFrameworkCore -Pre
```

## <a name="get-started"></a><span data-ttu-id="5def5-110">開始するには</span><span class="sxs-lookup"><span data-stu-id="5def5-110">Get Started</span></span>

<span data-ttu-id="5def5-111">「[Starting with MySQL EF Core provider and Connector/Net 7.0.4](http://insidemysql.com/howto-starting-with-mysql-ef-core-provider-and-connectornet-7-0-4/)」 (MySQL EF Core プロバイダーと Connector/Net 7.0.4 の入門) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5def5-111">See [Starting with MySQL EF Core provider and Connector/Net 7.0.4](http://insidemysql.com/howto-starting-with-mysql-ef-core-provider-and-connectornet-7-0-4/).</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="5def5-112">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="5def5-112">Supported Database Engines</span></span>

* <span data-ttu-id="5def5-113">MySQL</span><span class="sxs-lookup"><span data-stu-id="5def5-113">MySQL</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="5def5-114">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="5def5-114">Supported Platforms</span></span>

* <span data-ttu-id="5def5-115">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="5def5-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="5def5-116">.NET Core</span><span class="sxs-lookup"><span data-stu-id="5def5-116">.NET Core</span></span>

<span data-ttu-id="5def5-117">バージョンの互換性の情報については、MySQL のドキュメントの[ここ](https://dev.mysql.com/doc/connector-net/en/connector-net-versions.html)と[ここ](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)を確認してください</span><span class="sxs-lookup"><span data-stu-id="5def5-117">Be sure to review the MySQL documentation for version compatibility information [here](https://dev.mysql.com/doc/connector-net/en/connector-net-versions.html) and [here](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)</span></span>
