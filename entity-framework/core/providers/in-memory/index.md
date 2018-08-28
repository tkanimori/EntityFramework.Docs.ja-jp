---
title: InMemory データベース プロバイダー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: ca802f55d973b9f79073c2507c1e0c7a853a1fce
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993321"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="abbd2-102">EF Core In-Memory データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="abbd2-102">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="abbd2-103">このデータベース プロバイダーにより、メモリ内のデータベースで Entity Framework Core を使用することが許可されます。</span><span class="sxs-lookup"><span data-stu-id="abbd2-103">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="abbd2-104">これはテストに役立つことがあります。ただし、リレーショナル データベースのテストの置き換えとしてはインメモリ モードの SQLite プロバイダーの方が適しています。</span><span class="sxs-lookup"><span data-stu-id="abbd2-104">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="abbd2-105">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="abbd2-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="abbd2-106">インストール</span><span class="sxs-lookup"><span data-stu-id="abbd2-106">Install</span></span>

<span data-ttu-id="abbd2-107">[Microsoft.EntityFrameworkCore.InMemory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="abbd2-107">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

## <a name="get-started"></a><span data-ttu-id="abbd2-108">開始するには</span><span class="sxs-lookup"><span data-stu-id="abbd2-108">Get Started</span></span>

<span data-ttu-id="abbd2-109">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="abbd2-109">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="abbd2-110">InMemory のテスト</span><span class="sxs-lookup"><span data-stu-id="abbd2-110">Testing with InMemory</span></span>](../../miscellaneous/testing/in-memory.md)

* [<span data-ttu-id="abbd2-111">UnicornStore サンプル アプリケーション テスト</span><span class="sxs-lookup"><span data-stu-id="abbd2-111">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="abbd2-112">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="abbd2-112">Supported Database Engines</span></span>

* <span data-ttu-id="abbd2-113">組み込みメモリ内データベース (テスト目的専用として設計されています)</span><span class="sxs-lookup"><span data-stu-id="abbd2-113">Built-in in-memory database (designed for testing purposes only)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="abbd2-114">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="abbd2-114">Supported Platforms</span></span>

* <span data-ttu-id="abbd2-115">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="abbd2-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="abbd2-116">.NET Core</span><span class="sxs-lookup"><span data-stu-id="abbd2-116">.NET Core</span></span>

* <span data-ttu-id="abbd2-117">Mono (4.2.0 以降)</span><span class="sxs-lookup"><span data-stu-id="abbd2-117">Mono (4.2.0 onwards)</span></span>

* <span data-ttu-id="abbd2-118">ユニバーサル Windows プラットフォーム</span><span class="sxs-lookup"><span data-stu-id="abbd2-118">Universal Windows Platform</span></span>
