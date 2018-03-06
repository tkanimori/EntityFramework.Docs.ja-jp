---
title: "SQLite データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
ms.technology: entity-framework-core
uid: core/providers/sqlite/index
ms.openlocfilehash: 2e392f382f0e6f4d092a362c44f2149eb336db17
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="a91ec-102">SQLite EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="a91ec-102">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="a91ec-103">このデータベース プロバイダーにより、SQLite と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a91ec-103">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="a91ec-104">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="a91ec-104">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="a91ec-105">インストール</span><span class="sxs-lookup"><span data-stu-id="a91ec-105">Install</span></span>

<span data-ttu-id="a91ec-106">[Microsoft.EntityFrameworkCore.Sqlite NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a91ec-106">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="get-started"></a><span data-ttu-id="a91ec-107">開始するには</span><span class="sxs-lookup"><span data-stu-id="a91ec-107">Get Started</span></span>

<span data-ttu-id="a91ec-108">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="a91ec-108">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="a91ec-109">UWP のローカル SQLite</span><span class="sxs-lookup"><span data-stu-id="a91ec-109">Local SQLite on UWP</span></span>](../../get-started/uwp/getting-started.md)

* [<span data-ttu-id="a91ec-110">.NET Core アプリケーションを新しい SQLite データベースに</span><span class="sxs-lookup"><span data-stu-id="a91ec-110">.NET Core Application to New SQLite Database</span></span>](../../get-started/netcore/new-db-sqlite.md)

* [<span data-ttu-id="a91ec-111">Unicorn Clicker サンプル アプリケーション</span><span class="sxs-lookup"><span data-stu-id="a91ec-111">Unicorn Clicker Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornClicker/UWP)

* [<span data-ttu-id="a91ec-112">Unicorn Packer サンプル アプリケーション</span><span class="sxs-lookup"><span data-stu-id="a91ec-112">Unicorn Packer Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornPacker)

## <a name="supported-database-engines"></a><span data-ttu-id="a91ec-113">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="a91ec-113">Supported Database Engines</span></span>

* <span data-ttu-id="a91ec-114">SQLite (3.7 以降)</span><span class="sxs-lookup"><span data-stu-id="a91ec-114">SQLite (3.7 onwards)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="a91ec-115">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="a91ec-115">Supported Platforms</span></span>

* <span data-ttu-id="a91ec-116">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="a91ec-116">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="a91ec-117">.NET Core</span><span class="sxs-lookup"><span data-stu-id="a91ec-117">.NET Core</span></span>

* <span data-ttu-id="a91ec-118">Mono (4.2.0 以降)</span><span class="sxs-lookup"><span data-stu-id="a91ec-118">Mono (4.2.0 onwards)</span></span>

* <span data-ttu-id="a91ec-119">ユニバーサル Windows プラットフォーム</span><span class="sxs-lookup"><span data-stu-id="a91ec-119">Universal Windows Platform</span></span>

## <a name="limitations"></a><span data-ttu-id="a91ec-120">制限事項</span><span class="sxs-lookup"><span data-stu-id="a91ec-120">Limitations</span></span>

<span data-ttu-id="a91ec-121">SQLite プロバイダーの重要な制限事項については、[SQLite の制限事項](limitations.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a91ec-121">See [SQLite Limitations](limitations.md) for some important limitations of the SQLite provider.</span></span>
