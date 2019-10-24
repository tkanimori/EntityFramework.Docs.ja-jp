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
# <a name="microsoft-sql-server-ef-core-database-provider"></a>Microsoft SQL Server EF Core データベース プロバイダー

このデータベース プロバイダーにより、Microsoft SQL Server (SQL Azure を含む) と共に Entity Framework Core を使用できます。 このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。

## <a name="install"></a>インストール

[Microsoft.EntityFrameworkCore.SqlServer NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)をインストールします。

# <a name="net-core-clitabdotnet-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

``` console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

# <a name="visual-studiotabvs"></a>[Visual Studio](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

***

> [!NOTE]
> バージョン 3.0.0 以降、プロバイダーでは Microsoft.Data.SqlClient が参照されます (以前のバージョンでは System.Data.SqlClient に依存していました)。 ご使用のプロジェクトが SqlClient に直接依存している場合は、正しいパッケージが参照されていることを確認してください。

## <a name="supported-database-engines"></a>サポートされているデータベース エンジン

* Microsoft SQL Server (2012 以降)
