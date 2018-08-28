---
title: Microsoft SQL Server データベース プロバイダー - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/index
ms.openlocfilehash: a524794a61a9f5078998aea04b45c31c19357f2b
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995670"
---
# <a name="microsoft-sql-server-ef-core-database-provider"></a>Microsoft SQL Server EF Core データベース プロバイダー

このデータベース プロバイダーにより、Microsoft SQL Server (SQL Azure を含む) と共に Entity Framework Core を使用できます。 このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。

## <a name="install"></a>インストール

[Microsoft.EntityFrameworkCore.SqlServer NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)をインストールします。

``` powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="get-started"></a>開始するには

このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。
* [.NET framework の概要 (コンソール、WinForms、WPF など)](../../get-started/full-dotnet/index.md)

* [ASP.NET Core の概要](../../get-started/aspnetcore/index.md)

* [UnicornStore サンプル アプリケーション](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornStore)

## <a name="supported-database-engines"></a>サポートされているデータベース エンジン

* Microsoft SQL Server (2008 以降)

## <a name="supported-platforms"></a>サポートされているプラットフォーム

* .NET Framework (4.5.1 以降)

* .NET Core

* Mono (4.2.0 以降)

      Caution: Using this provider on Mono will make use of the Mono SQL Client implementation, which has a number of known issues. For example, it does not support secure connections (SSL).
