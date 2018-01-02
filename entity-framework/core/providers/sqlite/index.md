---
title: "SQLite データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
ms.technology: entity-framework-core
uid: core/providers/sqlite/index
ms.openlocfilehash: 0f3905a491fb5f7a657ce9037d166771e1f326d8
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="sqlite-ef-core-database-provider"></a>SQLite EF Core データベース プロバイダー

このデータベース プロバイダーにより、SQLite と共に Entity Framework Core を使用できます。 このプロバイダーは [EntityFramework GitHub プロジェクト](https://github.com/aspnet/EntityFramework)の一部として保守管理されます。

## <a name="install"></a>Install

[Microsoft.EntityFrameworkCore.Sqlite NuGet パッケージ](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)をインストールします。

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="get-started"></a>開始するには

このプロバイダーを使い始めるにあたり、次のリソースを参考にしてください。
* [UWP のローカル SQLite](../../get-started/uwp/getting-started.md)

* [.NET Core アプリケーションを新しい SQLite データベースに](../../get-started/netcore/new-db-sqlite.md)

* [Unicorn Clicker サンプル アプリケーション](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornClicker/UWP)

* [Unicorn Packer サンプル アプリケーション](https://github.com/rowanmiller/UnicornStore/tree/master/UnicornPacker)

## <a name="supported-database-engines"></a>サポートされているデータベース エンジン

* SQLite (3.7 以降)

## <a name="supported-platforms"></a>サポートされているプラットフォーム

* .NET Framework (4.5.1 以降)

* .NET Core

* Mono (4.2.0 以降)

* ユニバーサル Windows プラットフォーム

## <a name="limitations"></a>制限事項

SQLite プロバイダーの重要な制限事項については、[SQLite の制限事項](limitations.md)をご覧ください。
