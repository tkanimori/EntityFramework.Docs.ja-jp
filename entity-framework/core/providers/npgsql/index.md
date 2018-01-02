---
title: "PostgreSQL の Npgsql データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 5ecd1b22-c68e-4d87-ba39-b0761f4d5b90
ms.technology: entity-framework-core
uid: core/providers/npgsql/index
ms.openlocfilehash: acf2e18d7a608b0d75b571a5ac0199d84f86066b
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="npgsql-ef-core-database-provider-for-postgresql"></a><span data-ttu-id="728f8-102">PostgreSQL の Npgsql EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="728f8-102">Npgsql EF Core Database Provider for PostgreSQL</span></span>

<span data-ttu-id="728f8-103">このデータベース プロバイダーにより、PostgreSQL と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="728f8-103">This database provider allows Entity Framework Core to be used with PostgreSQL.</span></span> <span data-ttu-id="728f8-104">このプロバイダーは [Npgsql プロジェクト](http://www.npgsql.org)の一部として保守管理されます。</span><span class="sxs-lookup"><span data-stu-id="728f8-104">The provider is maintained as part of the [Npgsql project](http://www.npgsql.org).</span></span>

> [!NOTE]  
> <span data-ttu-id="728f8-105">このプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。</span><span class="sxs-lookup"><span data-stu-id="728f8-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="728f8-106">サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。</span><span class="sxs-lookup"><span data-stu-id="728f8-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="728f8-107">Install</span><span class="sxs-lookup"><span data-stu-id="728f8-107">Install</span></span>

<span data-ttu-id="728f8-108">[Npgsql.EntityFrameworkCore.PostgreSQL NuGet パッケージ](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="728f8-108">Install the [Npgsql.EntityFrameworkCore.PostgreSQL NuGet package](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL).</span></span>

``` powershell
Install-Package Npgsql.EntityFrameworkCore.PostgreSQL
```

## <a name="get-started"></a><span data-ttu-id="728f8-109">開始するには</span><span class="sxs-lookup"><span data-stu-id="728f8-109">Get Started</span></span>

<span data-ttu-id="728f8-110">開始するにあたり、[Npgsql ドキュメント](http://www.npgsql.org/efcore/index.html)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="728f8-110">See the [Npgsql documentation](http://www.npgsql.org/efcore/index.html) to get started.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="728f8-111">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="728f8-111">Supported Database Engines</span></span>

* <span data-ttu-id="728f8-112">PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="728f8-112">PostgreSQL</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="728f8-113">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="728f8-113">Supported Platforms</span></span>

* <span data-ttu-id="728f8-114">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="728f8-114">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="728f8-115">.NET Core</span><span class="sxs-lookup"><span data-stu-id="728f8-115">.NET Core</span></span>

* <span data-ttu-id="728f8-116">Mono (4.2.0 以降)</span><span class="sxs-lookup"><span data-stu-id="728f8-116">Mono (4.2.0 onwards)</span></span>
