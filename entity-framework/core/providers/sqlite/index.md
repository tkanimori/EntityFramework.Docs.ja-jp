---
title: SQLite データベース プロバイダー - EF Core
description: Entity Framework Core SQLite データベース プロバイダーに関する情報
author: rowanmiller
ms.date: 10/27/2016
uid: core/providers/sqlite/index
ms.openlocfilehash: ba537acdf537fa475378c08b8c6290930b29239d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071239"
---
# <a name="sqlite-ef-core-database-provider"></a><span data-ttu-id="9c5d4-103">SQLite EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9c5d4-103">SQLite EF Core Database Provider</span></span>

<span data-ttu-id="9c5d4-104">このデータベース プロバイダーにより、SQLite と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9c5d4-104">This database provider allows Entity Framework Core to be used with SQLite.</span></span> <span data-ttu-id="9c5d4-105">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="9c5d4-105">The provider is maintained as part of the [Entity Framework Core project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="9c5d4-106">インストール</span><span class="sxs-lookup"><span data-stu-id="9c5d4-106">Install</span></span>

<span data-ttu-id="9c5d4-107">[Microsoft.EntityFrameworkCore.Sqlite NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9c5d4-107">Install the [Microsoft.EntityFrameworkCore.Sqlite NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="9c5d4-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9c5d4-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="9c5d4-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9c5d4-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

***

## <a name="supported-database-engines"></a><span data-ttu-id="9c5d4-110">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="9c5d4-110">Supported Database Engines</span></span>

* <span data-ttu-id="9c5d4-111">SQLite (3.7 以降)</span><span class="sxs-lookup"><span data-stu-id="9c5d4-111">SQLite (3.7 onwards)</span></span>

## <a name="limitations"></a><span data-ttu-id="9c5d4-112">制限事項</span><span class="sxs-lookup"><span data-stu-id="9c5d4-112">Limitations</span></span>

<span data-ttu-id="9c5d4-113">SQLite プロバイダーの重要な制限事項については、[SQLite の制限事項](xref:core/providers/sqlite/limitations)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9c5d4-113">See [SQLite Limitations](xref:core/providers/sqlite/limitations) for some important limitations of the SQLite provider.</span></span>
