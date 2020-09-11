---
title: SQLite データベース プロバイダー - EF Core
description: Entity Framework Core SQLite データベース プロバイダーに関する情報
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
uid: core/providers/sqlite/index
ms.openlocfilehash: 8620f0e37825368cb3d7965a05118ab1297fb9e8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616564"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="91068-103">SQLite EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="91068-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="91068-104">このデータベース プロバイダーにより、SQLite と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="91068-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="91068-105">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="91068-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="91068-106">インストール</span><span class="sxs-lookup"><span data-stu-id="91068-106">Install</span></span>

<span data-ttu-id="91068-107">[Microsoft.EntityFrameworkCore.Sqlite NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="91068-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="91068-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="91068-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="91068-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="91068-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="91068-110">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="91068-110">Supported Database Engines</span></span>

* <span data-ttu-id="91068-111">SQLite (3.7 以降)</span><span class="sxs-lookup"><span data-stu-id="91068-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="91068-112">制限事項</span><span class="sxs-lookup"><span data-stu-id="91068-112">Limitations</span></span>

<span data-ttu-id="91068-113">SQLite プロバイダーの重要な制限事項については、[SQLite の制限事項](xref:core/providers/sqlite/limitations)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="91068-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
