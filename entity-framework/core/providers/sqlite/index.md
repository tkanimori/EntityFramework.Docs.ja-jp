---
title: SQLite データベース プロバイダー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: 31de8449a12a10d4f98ebb4bb6125389606e9bbd
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994003"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="4c42b-102">SQLite EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4c42b-102">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="4c42b-103">このデータベース プロバイダーにより、SQLite と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="4c42b-103">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="4c42b-104">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="4c42b-104">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="4c42b-105">インストール</span><span class="sxs-lookup"><span data-stu-id="4c42b-105">Install</span></span>

<span data-ttu-id="4c42b-106">[Microsoft.EntityFrameworkCore.Sqlite NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4c42b-106">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="get-started"></a><span data-ttu-id="4c42b-107">開始するには</span><span class="sxs-lookup"><span data-stu-id="4c42b-107">Get Started</span></span>

<span data-ttu-id="4c42b-108">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="4c42b-108">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="4c42b-109">UWP のローカル SQLite</span><span class="sxs-lookup"><span data-stu-id="4c42b-109">Local SQLite on UWP</span></span>](../../get-started/uwp/getting-started.md)

* [<span data-ttu-id="4c42b-110">.NET Core アプリケーションを新しい SQLite データベースに</span><span class="sxs-lookup"><span data-stu-id="4c42b-110">.NET Core Application to New SQLite Database</span></span>](../../get-started/netcore/new-db-sqlite.md)

* [<span data-ttu-id="4c42b-111">Unicorn Clicker サンプル アプリケーション</span><span class="sxs-lookup"><span data-stu-id="4c42b-111">Unicorn Clicker Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornClicker/UWP)

* [<span data-ttu-id="4c42b-112">Unicorn Packer サンプル アプリケーション</span><span class="sxs-lookup"><span data-stu-id="4c42b-112">Unicorn Packer Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornPacker)

## <a name="supported-database-engines"></a><span data-ttu-id="4c42b-113">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="4c42b-113">Supported Database Engines</span></span>

* <span data-ttu-id="4c42b-114">SQLite (3.7 以降)</span><span class="sxs-lookup"><span data-stu-id="4c42b-114">SQLite (3.7 onwards)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="4c42b-115">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="4c42b-115">Supported Platforms</span></span>

* <span data-ttu-id="4c42b-116">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="4c42b-116">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="4c42b-117">.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c42b-117">.NET Core</span></span>

* <span data-ttu-id="4c42b-118">Mono (4.2.0 以降)</span><span class="sxs-lookup"><span data-stu-id="4c42b-118">Mono (4.2.0 onwards)</span></span>

* <span data-ttu-id="4c42b-119">ユニバーサル Windows プラットフォーム</span><span class="sxs-lookup"><span data-stu-id="4c42b-119">Universal Windows Platform</span></span>

## <a name="limitations"></a><span data-ttu-id="4c42b-120">制限事項</span><span class="sxs-lookup"><span data-stu-id="4c42b-120">Limitations</span></span>

<span data-ttu-id="4c42b-121">SQLite プロバイダーの重要な制限事項については、[SQLite の制限事項](limitations.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4c42b-121">See [SQLite Limitations](limitations.md) for some important limitations of the SQLite provider.</span></span>
