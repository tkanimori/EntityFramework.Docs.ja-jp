---
title: InMemory データベース プロバイダー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: fd31c8ef2dc2e35e69f9845933a5578a5ff84c9c
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413007"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="6d5f4-102">EF Core In-Memory データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="6d5f4-102">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="6d5f4-103">このデータベース プロバイダーにより、メモリ内のデータベースで Entity Framework Core を使用することが許可されます。</span><span class="sxs-lookup"><span data-stu-id="6d5f4-103">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="6d5f4-104">これはテストに役立つことがあります。ただし、リレーショナル データベースのテストの置き換えとしてはインメモリ モードの SQLite プロバイダーの方が適しています。</span><span class="sxs-lookup"><span data-stu-id="6d5f4-104">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="6d5f4-105">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="6d5f4-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="6d5f4-106">インストール</span><span class="sxs-lookup"><span data-stu-id="6d5f4-106">Install</span></span>

<span data-ttu-id="6d5f4-107">[Microsoft.EntityFrameworkCore.InMemory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="6d5f4-107">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="6d5f4-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6d5f4-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="6d5f4-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6d5f4-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="6d5f4-110">開始するには</span><span class="sxs-lookup"><span data-stu-id="6d5f4-110">Get Started</span></span>

<span data-ttu-id="6d5f4-111">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="6d5f4-111">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="6d5f4-112">InMemory のテスト</span><span class="sxs-lookup"><span data-stu-id="6d5f4-112">Testing with InMemory</span></span>](../../miscellaneous/testing/in-memory.md)
* [<span data-ttu-id="6d5f4-113">UnicornStore サンプル アプリケーション テスト</span><span class="sxs-lookup"><span data-stu-id="6d5f4-113">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="6d5f4-114">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="6d5f4-114">Supported Database Engines</span></span>

<span data-ttu-id="6d5f4-115">インプロセス メモリ内データベース (テスト目的専用として設計されています)</span><span class="sxs-lookup"><span data-stu-id="6d5f4-115">In-process memory database (designed for testing purposes only)</span></span>
