---
title: Microsoft SQL Server データベース プロバイダー - EF Core
description: Microsoft SQL Server と共に Entity Framework Core を使えるようにするデータベース プロバイダーに関するドキュメントです
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: 4118cd5737ece1ad084bb85d409523d217065353
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430325"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="4be08-103">Microsoft SQL Server EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4be08-103">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="4be08-104">このデータベース プロバイダーにより、Microsoft SQL Server (Azure SQL Database を含む) と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="4be08-104">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including Azure SQL Database).</span></span> <span data-ttu-id="4be08-105">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/dotnet/efcore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="4be08-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="4be08-106">インストール</span><span class="sxs-lookup"><span data-stu-id="4be08-106">Install</span></span>

<span data-ttu-id="4be08-107">[Microsoft.EntityFrameworkCore.SqlServer NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4be08-107">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="4be08-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4be08-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[<span data-ttu-id="4be08-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4be08-109">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="4be08-110">バージョン 3.0.0 以降、プロバイダーでは Microsoft.Data.SqlClient が参照されます (以前のバージョンでは System.Data.SqlClient に依存していました)。</span><span class="sxs-lookup"><span data-stu-id="4be08-110">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="4be08-111">ご使用のプロジェクトが SqlClient に直接依存している場合は、Microsoft.Data.SqlClient パッケージが参照されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="4be08-111">If your project takes a direct dependency on SqlClient, make sure it references the Microsoft.Data.SqlClient package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="4be08-112">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="4be08-112">Supported Database Engines</span></span>

* <span data-ttu-id="4be08-113">Microsoft SQL Server (2012 以降)</span><span class="sxs-lookup"><span data-stu-id="4be08-113">Microsoft SQL Server (2012 onwards)</span></span>
