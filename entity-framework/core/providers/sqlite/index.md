---
title: SQLite データベース プロバイダー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: e8c3d675322b163fdf1e2e7e01f3815e28f427a2
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502254"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="66d88-102">SQLite EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="66d88-102">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="66d88-103">このデータベース プロバイダーにより、SQLite と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="66d88-103">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="66d88-104">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="66d88-104">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="66d88-105">インストール</span><span class="sxs-lookup"><span data-stu-id="66d88-105">Install</span></span>

<span data-ttu-id="66d88-106">[Microsoft.EntityFrameworkCore.Sqlite NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="66d88-106">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="66d88-107">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="66d88-107">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studiotabvs"></a>[<span data-ttu-id="66d88-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66d88-108">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="66d88-109">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="66d88-109">Supported Database Engines</span></span>

* <span data-ttu-id="66d88-110">SQLite (3.7 以降)</span><span class="sxs-lookup"><span data-stu-id="66d88-110">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="66d88-111">制限事項</span><span class="sxs-lookup"><span data-stu-id="66d88-111">Limitations</span></span>

<span data-ttu-id="66d88-112">SQLite プロバイダーの重要な制限事項については、[SQLite の制限事項](limitations.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="66d88-112">See [SQLite Limitations](limitations.md) for some important limitations of the SQLite provider.</span></span>
