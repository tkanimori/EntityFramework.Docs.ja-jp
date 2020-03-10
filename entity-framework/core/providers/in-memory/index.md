---
title: InMemory データベース プロバイダー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
uid: core/providers/in-memory/index
ms.openlocfilehash: fd31c8ef2dc2e35e69f9845933a5578a5ff84c9c
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413007"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="edc95-102">EF Core In-Memory データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="edc95-102">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="edc95-103">このデータベース プロバイダーにより、メモリ内のデータベースで Entity Framework Core を使用することが許可されます。</span><span class="sxs-lookup"><span data-stu-id="edc95-103">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="edc95-104">これはテストに役立つことがあります。ただし、リレーショナル データベースのテストの置き換えとしてはインメモリ モードの SQLite プロバイダーの方が適しています。</span><span class="sxs-lookup"><span data-stu-id="edc95-104">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="edc95-105">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="edc95-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="edc95-106">インストール</span><span class="sxs-lookup"><span data-stu-id="edc95-106">Install</span></span>

<span data-ttu-id="edc95-107">[Microsoft.EntityFrameworkCore.InMemory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="edc95-107">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="edc95-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="edc95-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="edc95-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="edc95-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="edc95-110">はじめに</span><span class="sxs-lookup"><span data-stu-id="edc95-110">Get Started</span></span>

<span data-ttu-id="edc95-111">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="edc95-111">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="edc95-112">InMemory のテスト</span><span class="sxs-lookup"><span data-stu-id="edc95-112">Testing with InMemory</span></span>](../../miscellaneous/testing/in-memory.md)
* [<span data-ttu-id="edc95-113">UnicornStore サンプル アプリケーション テスト</span><span class="sxs-lookup"><span data-stu-id="edc95-113">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="edc95-114">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="edc95-114">Supported Database Engines</span></span>

<span data-ttu-id="edc95-115">インプロセス メモリ内データベース (テスト目的専用として設計されています)</span><span class="sxs-lookup"><span data-stu-id="edc95-115">In-process memory database (designed for testing purposes only)</span></span>
