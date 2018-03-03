---
title: "Microsoft SQL Server Compact Edition データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 073f0004-3eb5-4618-ab93-0674910e1819
ms.technology: entity-framework-core
uid: core/providers/sql-compact/index
ms.openlocfilehash: d8b73621bdd363efec5bb7728886e0a0f6bdcf76
ms.sourcegitcommit: 6ed04bb05a3d05c367f0f55616807af2bf4037ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="microsoft-sql-server-compact-edition-ef-core-database-provider"></a>Microsoft SQL Server Compact Edition EF Core データベース プロバイダー

このデータベース プロバイダーにより、SQL Server Compact Edition と共に Entity Framework Core を使用できます。 このプロバイダーは [ErikEJ/EntityFramework.SqlServerCompact GitHub プロジェクト](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)の一部として保守管理されます。

> [!NOTE]  
> このプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。 サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。

## <a name="install"></a>インストール

SQL Server Compact Edition 4.0 を使用するには、[EntityFrameworkCore.SqlServerCompact40 NuGet パッケージ](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)をインストールします。

``` powershell
Install-Package EntityFrameworkCore.SqlServerCompact40
```

SQL Server Compact Edition 3.5 を使用するには、[EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35) をインストールします。

``` powershell
Install-Package EntityFrameworkCore.SqlServerCompact35
```

## <a name="get-started"></a>開始するには

[プロジェクト サイトで入門ドキュメント](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)を参照してください。

## <a name="supported-database-engines"></a>サポートされているデータベース エンジン

* SQL Server Compact Edition 3.5

* SQL Server Compact Edition 4.0

## <a name="supported-platforms"></a>サポートされているプラットフォーム

* .NET Framework (4.5.1 以降)
