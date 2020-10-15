---
title: SQLite データベース プロバイダー - EF Core
description: Entity Framework Core SQLite データベース プロバイダーに関する情報
author: bricelam
ms.date: 10/27/2016
uid: core/providers/sqlite/index
ms.openlocfilehash: 2d221168c093271d220d4d8fbc7779c1a5aab701
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063986"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="6a5d1-103">SQLite EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="6a5d1-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="6a5d1-104">このデータベース プロバイダーにより、SQLite と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6a5d1-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="6a5d1-105">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="6a5d1-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="6a5d1-106">インストール</span><span class="sxs-lookup"><span data-stu-id="6a5d1-106">Install</span></span>

<span data-ttu-id="6a5d1-107">[Microsoft.EntityFrameworkCore.Sqlite NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="6a5d1-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="6a5d1-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6a5d1-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="6a5d1-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a5d1-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="6a5d1-110">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="6a5d1-110">Supported Database Engines</span></span>

* <span data-ttu-id="6a5d1-111">SQLite (3.7 以降)</span><span class="sxs-lookup"><span data-stu-id="6a5d1-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="6a5d1-112">制限事項</span><span class="sxs-lookup"><span data-stu-id="6a5d1-112">Limitations</span></span>

<span data-ttu-id="6a5d1-113">SQLite プロバイダーの重要な制限事項については、[SQLite の制限事項](xref:core/providers/sqlite/limitations)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="6a5d1-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
