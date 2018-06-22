---
title: Microsoft SQL Server データベース プロバイダー - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
ms.technology: entity-framework-core
uid: core/providers/sql-server/index
ms.openlocfilehash: 2ed7c0dd127db03d5e7340fde1ef83cf01b30135
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678651"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="df58b-102">Microsoft SQL Server EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="df58b-102">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="df58b-103">このデータベース プロバイダーにより、Microsoft SQL Server (SQL Azure を含む) と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="df58b-103">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including SQL Azure).</span></span> <span data-ttu-id="df58b-104">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="df58b-104">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="df58b-105">インストール</span><span class="sxs-lookup"><span data-stu-id="df58b-105">Install</span></span>

<span data-ttu-id="df58b-106">[Microsoft.EntityFrameworkCore.SqlServer NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="df58b-106">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="get-started"></a><span data-ttu-id="df58b-107">開始するには</span><span class="sxs-lookup"><span data-stu-id="df58b-107">Get Started</span></span>

<span data-ttu-id="df58b-108">このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。</span><span class="sxs-lookup"><span data-stu-id="df58b-108">The following resources will help you get started with this provider.</span></span>
* [<span data-ttu-id="df58b-109">.NET framework の概要 (コンソール、WinForms、WPF など)</span><span class="sxs-lookup"><span data-stu-id="df58b-109">Getting Started on .NET Framework (Console, WinForms, WPF, etc.)</span></span>](../../get-started/full-dotnet/index.md)

* [<span data-ttu-id="df58b-110">ASP.NET Core の概要</span><span class="sxs-lookup"><span data-stu-id="df58b-110">Getting Started on ASP.NET Core</span></span>](../../get-started/aspnetcore/index.md)

* [<span data-ttu-id="df58b-111">UnicornStore サンプル アプリケーション</span><span class="sxs-lookup"><span data-stu-id="df58b-111">UnicornStore Sample Application</span></span>](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornStore)

## <a name="supported-database-engines"></a><span data-ttu-id="df58b-112">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="df58b-112">Supported Database Engines</span></span>

* <span data-ttu-id="df58b-113">Microsoft SQL Server (2008 以降)</span><span class="sxs-lookup"><span data-stu-id="df58b-113">Microsoft SQL Server (2008 onwards)</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="df58b-114">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="df58b-114">Supported Platforms</span></span>

* <span data-ttu-id="df58b-115">.NET Framework (4.5.1 以降)</span><span class="sxs-lookup"><span data-stu-id="df58b-115">.NET Framework (4.5.1 onwards)</span></span>

* <span data-ttu-id="df58b-116">.NET Core</span><span class="sxs-lookup"><span data-stu-id="df58b-116">.NET Core</span></span>

* <span data-ttu-id="df58b-117">Mono (4.2.0 以降)</span><span class="sxs-lookup"><span data-stu-id="df58b-117">Mono (4.2.0 onwards)</span></span>

      Caution: Using this provider on Mono will make use of the Mono SQL Client implementation, which has a number of known issues. For example, it does not support secure connections (SSL).
