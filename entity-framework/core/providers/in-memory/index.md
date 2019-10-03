---
title: InMemory データベース プロバイダー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: b668e286993b9687be21aa815df4e8b8dd308c60
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813533"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="a0dfb-102">EF Core In-Memory データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="a0dfb-102">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="a0dfb-103">このデータベース プロバイダーにより、メモリ内のデータベースで Entity Framework Core を使用することが許可されます。</span><span class="sxs-lookup"><span data-stu-id="a0dfb-103">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="a0dfb-104">これはテストに役立つことがあります。ただし、リレーショナル データベースのテストの置き換えとしてはインメモリ モードの SQLite プロバイダーの方が適しています。</span><span class="sxs-lookup"><span data-stu-id="a0dfb-104">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="a0dfb-105">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="a0dfb-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="a0dfb-106">インストール</span><span class="sxs-lookup"><span data-stu-id="a0dfb-106">Install</span></span>

<span data-ttu-id="a0dfb-107">[Microsoft.EntityFrameworkCore.InMemory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a0dfb-107">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

# <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="a0dfb-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a0dfb-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

``` console
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

# <a name="visual-studiotabvs"></a>[<span data-ttu-id="a0dfb-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a0dfb-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="a0dfb-110">開始するには</span><span class="sxs-lookup"><span data-stu-id="a0dfb-110">Get Started</span></span>

<span data-ttu-id="a0dfb-111">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="a0dfb-111">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="a0dfb-112">InMemory のテスト</span><span class="sxs-lookup"><span data-stu-id="a0dfb-112">Testing with InMemory</span></span>](../../miscellaneous/testing/in-memory.md)
* [<span data-ttu-id="a0dfb-113">UnicornStore サンプル アプリケーション テスト</span><span class="sxs-lookup"><span data-stu-id="a0dfb-113">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="a0dfb-114">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="a0dfb-114">Supported Database Engines</span></span>

<span data-ttu-id="a0dfb-115">インプロセス メモリ内データベース (テスト目的専用として設計されています)</span><span class="sxs-lookup"><span data-stu-id="a0dfb-115">In-process memory database (designed for testing purposes only)</span></span>
