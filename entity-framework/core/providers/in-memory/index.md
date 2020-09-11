---
title: InMemory データベース プロバイダー - EF Core
description: Entity Framework Core InMemory データベース プロバイダーに関する情報
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: 6af1d61a6ff76b82cc0096edbf095a6338d21109
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619009"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="def66-103">EF Core In-Memory データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="def66-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="def66-104">このデータベース プロバイダーにより、メモリ内のデータベースで Entity Framework Core を使用すことが許可されます。</span><span class="sxs-lookup"><span data-stu-id="def66-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="def66-105">これはテストに役立つことがあります。ただし、リレーショナル データベースのテストの置き換えとしてはインメモリ モードの SQLite プロバイダーの方が適しています。</span><span class="sxs-lookup"><span data-stu-id="def66-105">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="def66-106">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="def66-106">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="def66-107">インストール</span><span class="sxs-lookup"><span data-stu-id="def66-107">Install</span></span>

<span data-ttu-id="def66-108">[Microsoft.EntityFrameworkCore.InMemory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="def66-108">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="def66-109">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="def66-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="def66-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="def66-110">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="def66-111">開始するには</span><span class="sxs-lookup"><span data-stu-id="def66-111">Get Started</span></span>

<span data-ttu-id="def66-112">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="def66-112">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="def66-113">InMemory のテスト</span><span class="sxs-lookup"><span data-stu-id="def66-113">Testing with InMemory</span></span>](xref:core/miscellaneous/testing/in-memory)
* [<span data-ttu-id="def66-114">UnicornStore サンプル アプリケーション テスト</span><span class="sxs-lookup"><span data-stu-id="def66-114">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="def66-115">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="def66-115">Supported Database Engines</span></span>

<span data-ttu-id="def66-116">インプロセス メモリ内データベース (テスト目的専用として設計されています)</span><span class="sxs-lookup"><span data-stu-id="def66-116">In-process memory database (designed for testing purposes only)</span></span>
