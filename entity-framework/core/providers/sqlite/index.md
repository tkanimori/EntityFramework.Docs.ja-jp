---
title: "SQLite データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
ms.technology: entity-framework-core
uid: core/providers/sqlite/index
ms.openlocfilehash: 0f3905a491fb5f7a657ce9037d166771e1f326d8
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="26b36-102">SQLite EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="26b36-102">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="26b36-103">このデータベース プロバイダーにより、SQLite と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="26b36-103">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="26b36-104">このプロバイダーは [EntityFramework GitHub プロジェクト](https://github.com/aspnet/EntityFramework)の一部として保守管理されます。</span><span class="sxs-lookup"><span data-stu-id="26b36-104">The provider is maintained as part of the [EntityFramework GitHub project](https://github.com/aspnet/EntityFramework).</span></span>

## <a name="install"></a><span data-ttu-id="26b36-105">Install</span><span class="sxs-lookup"><span data-stu-id="26b36-105">Install</span></span>

<span data-ttu-id="26b36-106">[Microsoft.EntityFrameworkCore.Sqlite NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="26b36-106">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="get-started"></a><span data-ttu-id="26b36-107">開始するには</span><span class="sxs-lookup"><span data-stu-id="26b36-107">Get Started</span></span>

<span data-ttu-id="26b36-108">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="26b36-108">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="26b36-109">UWP のローカル SQLite</span><span class="sxs-lookup"><span data-stu-id="26b36-109">Local SQLite on UWP</span></span>](../../get-started/uwp/getting-started.md)

* [<span data-ttu-id="26b36-110">.NET Core アプリケーションを新しい SQLite データベースに</span><span class="sxs-lookup"><span data-stu-id="26b36-110">.NET Core Application to New SQLite Database</span></span>](../../get-started/netcore/new-db-sqlite.md)

* [<span data-ttu-id="26b36-111">Unicorn Clicker サンプル アプリケーション</span><span class="sxs-lookup"><span data-stu-id="26b36-111">Unicorn Clicker Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornClicker/UWP)

* [<span data-ttu-id="26b36-112">Unicorn Packer サンプル アプリケーション</span><span class="sxs-lookup"><span data-stu-id="26b36-112">Unicorn Packer Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornPacker)

## <a name="supported-database-engines"></a><span data-ttu-id="26b36-113">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="26b36-113">Supported Database Engines</span></span>

* <span data-ttu-id="26b36-114">SQLite (3.7 以降)</span><span class="sxs-lookup"><span data-stu-id="26b36-114">SQLite (3.7 onwards)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="26b36-115">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="26b36-115">Supported Platforms</span></span>

* <span data-ttu-id="26b36-116">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="26b36-116">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="26b36-117">.NET Core</span><span class="sxs-lookup"><span data-stu-id="26b36-117">.NET Core</span></span>

* <span data-ttu-id="26b36-118">Mono (4.2.0 以降)</span><span class="sxs-lookup"><span data-stu-id="26b36-118">Mono (4.2.0 onwards)</span></span>

* <span data-ttu-id="26b36-119">ユニバーサル Windows プラットフォーム</span><span class="sxs-lookup"><span data-stu-id="26b36-119">Universal Windows Platform</span></span>

## <a name="limitations"></a><span data-ttu-id="26b36-120">制限事項</span><span class="sxs-lookup"><span data-stu-id="26b36-120">Limitations</span></span>

<span data-ttu-id="26b36-121">SQLite プロバイダーの重要な制限事項については、[SQLite の制限事項](limitations.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="26b36-121">See [SQLite Limitations](limitations.md) for some important limitations of the SQLite provider.</span></span>
