---
title: Microsoft SQL Server データベース プロバイダー - EF Core
description: Microsoft SQL Server と共に Entity Framework Core を使えるようにするデータベース プロバイダーに関するドキュメントです
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: 344bf243577d39d2d5a03cf321f8d84c275d10d3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065330"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a>Microsoft SQL Server EF Core データベース プロバイダー

このデータベース プロバイダーにより、Microsoft SQL Server (Azure SQL Database を含む) と共に Entity Framework Core を使用できます。 このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。

## <a name="install"></a>インストール

[Microsoft.EntityFrameworkCore.SqlServer NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)をインストールします。

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> バージョン 3.0.0 以降、プロバイダーでは Microsoft.Data.SqlClient が参照されます (以前のバージョンでは System.Data.SqlClient に依存していました)。 ご使用のプロジェクトが SqlClient に直接依存している場合は、Microsoft.Data.SqlClient パッケージが参照されていることを確認してください。

## <a name="supported-database-engines"></a>サポートされているデータベース エンジン

* Microsoft SQL Server (2012 以降)
