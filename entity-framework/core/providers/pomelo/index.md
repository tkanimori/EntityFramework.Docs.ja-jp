---
title: "Pomelo MySQL データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d0198c04-d30d-4419-98f8-a54690cea3c8
ms.technology: entity-framework-core
uid: core/providers/pomelo/index
ms.openlocfilehash: 9ddcda1ae7b058c01937867817e2666b97e7f37a
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="pomelo-ef-core-database-provider-for-mysql"></a><span data-ttu-id="3bba3-102">MySQL 用 Pomelo EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="3bba3-102">Pomelo EF Core Database Provider for MySQL</span></span>

<span data-ttu-id="3bba3-103">このデータベース プロバイダーにより、MySQL と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="3bba3-103">This database provider allows Entity Framework Core to be used with MySQL.</span></span> <span data-ttu-id="3bba3-104">このプロバイダーは [Pomelo Foundation プロジェクト](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)の一部として保守管理されます。</span><span class="sxs-lookup"><span data-stu-id="3bba3-104">The provider is maintained as part of the [Pomelo Foundation Project](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql).</span></span>

> [!NOTE]  
>
> <span data-ttu-id="3bba3-105">このプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="3bba3-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="3bba3-106">サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="3bba3-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="3bba3-107">Install</span><span class="sxs-lookup"><span data-stu-id="3bba3-107">Install</span></span>

<span data-ttu-id="3bba3-108">[Pomelo.EntityFrameworkCore.MySql NuGet パッケージ](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="3bba3-108">Install the [Pomelo.EntityFrameworkCore.MySql NuGet package](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql).</span></span>

``` powershell
Install-Package Pomelo.EntityFrameworkCore.MySql
```

## <a name="get-started"></a><span data-ttu-id="3bba3-109">開始するには</span><span class="sxs-lookup"><span data-stu-id="3bba3-109">Get Started</span></span>

<span data-ttu-id="3bba3-110">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="3bba3-110">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="3bba3-111">スタートアップ ドキュメント</span><span class="sxs-lookup"><span data-stu-id="3bba3-111">Getting started documentation</span></span>](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md#getting-started)

* [<span data-ttu-id="3bba3-112">Yuuko ブログ サンプル アプリケーション</span><span class="sxs-lookup"><span data-stu-id="3bba3-112">Yuuko Blog sample application</span></span>](https://github.com/PomeloFoundation/YuukoBlog)

## <a name="supported-database-engines"></a><span data-ttu-id="3bba3-113">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="3bba3-113">Supported Database Engines</span></span>

* <span data-ttu-id="3bba3-114">MySQL</span><span class="sxs-lookup"><span data-stu-id="3bba3-114">MySQL</span></span>
* <span data-ttu-id="3bba3-115">MariaDB</span><span class="sxs-lookup"><span data-stu-id="3bba3-115">MariaDB</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="3bba3-116">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="3bba3-116">Supported Platforms</span></span>

* <span data-ttu-id="3bba3-117">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="3bba3-117">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="3bba3-118">.NET Core</span><span class="sxs-lookup"><span data-stu-id="3bba3-118">.NET Core</span></span>

* <span data-ttu-id="3bba3-119">Mono (4.2.0 以降)</span><span class="sxs-lookup"><span data-stu-id="3bba3-119">Mono (4.2.0 onwards)</span></span>
