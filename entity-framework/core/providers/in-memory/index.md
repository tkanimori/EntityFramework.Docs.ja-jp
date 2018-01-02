---
title: "InMemory データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 9af0cba7-7605-4f8f-9cfa-dd616fcb880c
ms.technology: entity-framework-core
uid: core/providers/in-memory/index
ms.openlocfilehash: a8e05f50837f3da554b338475d24215706dfa2ec
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="2ef61-102">EF Core In-Memory データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="2ef61-102">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="2ef61-103">このデータベース プロバイダーにより、メモリ内のデータベースで Entity Framework Core を使用することが許可されます。</span><span class="sxs-lookup"><span data-stu-id="2ef61-103">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="2ef61-104">Entity Framework Core を使用するコードをテストするときに便利です。</span><span class="sxs-lookup"><span data-stu-id="2ef61-104">This is useful when testing code that uses Entity Framework Core.</span></span> <span data-ttu-id="2ef61-105">このプロバイダーは [EntityFramework GitHub プロジェクト](https://github.com/aspnet/EntityFramework)の一部として保守管理されます。</span><span class="sxs-lookup"><span data-stu-id="2ef61-105">The provider is maintained as part of the [EntityFramework GitHub project](https://github.com/aspnet/EntityFramework).</span></span>

## <a name="install"></a><span data-ttu-id="2ef61-106">Install</span><span class="sxs-lookup"><span data-stu-id="2ef61-106">Install</span></span>

<span data-ttu-id="2ef61-107">[Microsoft.EntityFrameworkCore.InMemory NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="2ef61-107">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

## <a name="get-started"></a><span data-ttu-id="2ef61-108">開始するには</span><span class="sxs-lookup"><span data-stu-id="2ef61-108">Get Started</span></span>

<span data-ttu-id="2ef61-109">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="2ef61-109">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="2ef61-110">InMemory のテスト</span><span class="sxs-lookup"><span data-stu-id="2ef61-110">Testing with InMemory</span></span>](../../miscellaneous/testing/in-memory.md)

* [<span data-ttu-id="2ef61-111">UnicornStore サンプル アプリケーション テスト</span><span class="sxs-lookup"><span data-stu-id="2ef61-111">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="2ef61-112">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="2ef61-112">Supported Database Engines</span></span>

* <span data-ttu-id="2ef61-113">組み込みメモリ内データベース (テスト目的専用として設計されています)</span><span class="sxs-lookup"><span data-stu-id="2ef61-113">Built-in in-memory database (designed for testing purposes only)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="2ef61-114">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="2ef61-114">Supported Platforms</span></span>

* <span data-ttu-id="2ef61-115">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="2ef61-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="2ef61-116">.NET Core</span><span class="sxs-lookup"><span data-stu-id="2ef61-116">.NET Core</span></span>

* <span data-ttu-id="2ef61-117">Mono (4.2.0 以降)</span><span class="sxs-lookup"><span data-stu-id="2ef61-117">Mono (4.2.0 onwards)</span></span>

* <span data-ttu-id="2ef61-118">ユニバーサル Windows プラットフォーム</span><span class="sxs-lookup"><span data-stu-id="2ef61-118">Universal Windows Platform</span></span>
