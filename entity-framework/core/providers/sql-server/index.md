---
title: Microsoft SQL Server データベース プロバイダー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/index
ms.openlocfilehash: 70e7f3d4bc1f57f1b23d9b3e0bd6264236ddbd27
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149198"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a><span data-ttu-id="b406b-102">Microsoft SQL Server EF Core データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="b406b-102">Microsoft SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="b406b-103">このデータベース プロバイダーにより、Microsoft SQL Server (SQL Azure を含む) と共に Entity Framework Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="b406b-103">This database provider allows Entity Framework Core to be used with Microsoft SQL Server (including SQL Azure).</span></span> <span data-ttu-id="b406b-104">このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。</span><span class="sxs-lookup"><span data-stu-id="b406b-104">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="b406b-105">インストール</span><span class="sxs-lookup"><span data-stu-id="b406b-105">Install</span></span>

<span data-ttu-id="b406b-106">[Microsoft.EntityFrameworkCore.SqlServer NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)をインストールします。</span><span class="sxs-lookup"><span data-stu-id="b406b-106">Install the [Microsoft.EntityFrameworkCore.SqlServer NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="supported-database-engines"></a><span data-ttu-id="b406b-107">サポートされているデータベース エンジン</span><span class="sxs-lookup"><span data-stu-id="b406b-107">Supported Database Engines</span></span>

* <span data-ttu-id="b406b-108">Microsoft SQL Server (2012 以降)</span><span class="sxs-lookup"><span data-stu-id="b406b-108">Microsoft SQL Server (2012 onwards)</span></span>
