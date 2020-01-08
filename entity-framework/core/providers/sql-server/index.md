---
title: Microsoft SQL Server データベース プロバイダー - EF Core
description: Microsoft SQL Server と共に Entity Framework Core を使えるようにするデータベース プロバイダーに関するドキュメントです
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/index
ms.openlocfilehash: baae668a7ec255e35ab0e23e5c5ddfa47bda917e
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502267"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a>Microsoft SQL Server EF Core データベース プロバイダー

このデータベース プロバイダーにより、Microsoft SQL Server (Azure SQL Database を含む) と共に Entity Framework Core を使用できます。 このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。

## <a name="install"></a>インストール

[Microsoft.EntityFrameworkCore.SqlServer NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)をインストールします。

### <a name="net-core-clitabdotnet-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="visual-studiotabvs"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> バージョン 3.0.0 以降、プロバイダーでは Microsoft.Data.SqlClient が参照されます (以前のバージョンでは System.Data.SqlClient に依存していました)。 ご使用のプロジェクトが SqlClient に直接依存している場合は、Microsoft.Data.SqlClient パッケージが参照されていることを確認してください。

## <a name="supported-database-engines"></a>サポートされているデータベース エンジン

* Microsoft SQL Server (2012 以降)
