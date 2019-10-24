---
title: Microsoft SQL Server データベース プロバイダー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/index
ms.openlocfilehash: 1e75bc4bf334b1a60d13a2ec9ef314e3afcf0273
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812093"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="9f5a6-102">Microsoft SQL Server EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="9f5a6-102">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="9f5a6-103">このデータベース プロバイダーにより、Microsoft SQL Server (SQL Azure を含む) と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9f5a6-103">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including SQL Azure).</span></span> <span data-ttu-id="9f5a6-104">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="9f5a6-104">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="9f5a6-105">インストール</span><span class="sxs-lookup"><span data-stu-id="9f5a6-105">Install</span></span>

<span data-ttu-id="9f5a6-106">[Microsoft.EntityFrameworkCore.SqlServer NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9f5a6-106">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

# <a name="net-core-clitabdotnet-core-cli"></a>[<span data-ttu-id="9f5a6-107">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9f5a6-107">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

``` console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

# <a name="visual-studiotabvs"></a>[<span data-ttu-id="9f5a6-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f5a6-108">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> <span data-ttu-id="9f5a6-109">バージョン 3.0.0 以降、プロバイダーでは Microsoft.Data.SqlClient が参照されます (以前のバージョンでは System.Data.SqlClient に依存していました)。</span><span class="sxs-lookup"><span data-stu-id="9f5a6-109">Since version 3.0.0, the provider references Microsoft.Data.SqlClient (previous versions depended on System.Data.SqlClient).</span></span> <span data-ttu-id="9f5a6-110">ご使用のプロジェクトが SqlClient に直接依存している場合は、正しいパッケージが参照されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="9f5a6-110">If your project takes a direct dependency on SqlClient, make sure it references the correct package.</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="9f5a6-111">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="9f5a6-111">Supported Database Engines</span></span>

* <span data-ttu-id="9f5a6-112">Microsoft SQL Server (2012 以降)</span><span class="sxs-lookup"><span data-stu-id="9f5a6-112">Microsoft SQL Server (2012 onwards)</span></span>
