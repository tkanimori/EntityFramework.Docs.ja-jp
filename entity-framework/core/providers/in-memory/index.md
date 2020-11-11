---
title: InMemory データベース プロバイダー - EF Core
description: Entity Framework Core InMemory データベース プロバイダーに関する情報
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 1af75088ae892e3b428caf6bdb31dd2b750a05fe
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430171"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="ffee6-103">EF Core In-Memory データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ffee6-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="ffee6-104">このデータベース プロバイダーにより、メモリ内のデータベースで Entity Framework Core を使用すことが許可されます。</span><span class="sxs-lookup"><span data-stu-id="ffee6-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="ffee6-105">このメモリ内のデータベースはテストに役立つことがあります。ただし、リレーショナル データベースのテストの置き換えとしてはインメモリ モードの SQLite プロバイダーの方が適しています。</span><span class="sxs-lookup"><span data-stu-id="ffee6-105">The in-memory database can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="ffee6-106">メモリ内のデータベースはテスト専用に設計されています。</span><span class="sxs-lookup"><span data-stu-id="ffee6-106">The in-memory database is designed for testing only.</span></span> <span data-ttu-id="ffee6-107">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/dotnet/efcore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="ffee6-107">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="ffee6-108">インストール</span><span class="sxs-lookup"><span data-stu-id="ffee6-108">Install</span></span>

<span data-ttu-id="ffee6-109">[Microsoft.EntityFrameworkCore.InMemory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="ffee6-109">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="ffee6-110">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ffee6-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="ffee6-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ffee6-111">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="ffee6-112">開始するには</span><span class="sxs-lookup"><span data-stu-id="ffee6-112">Get Started</span></span>

<span data-ttu-id="ffee6-113">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="ffee6-113">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="ffee6-114">InMemory のテスト</span><span class="sxs-lookup"><span data-stu-id="ffee6-114">Testing with InMemory</span></span>](xref:core/testing/in-memory)
* [<span data-ttu-id="ffee6-115">UnicornStore サンプル アプリケーション テスト</span><span class="sxs-lookup"><span data-stu-id="ffee6-115">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="ffee6-116">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="ffee6-116">Supported Database Engines</span></span>

<span data-ttu-id="ffee6-117">インプロセス メモリ内データベース (テスト専用に設計されています)。</span><span class="sxs-lookup"><span data-stu-id="ffee6-117">In-process memory database, designed for testing purposes only.</span></span>
