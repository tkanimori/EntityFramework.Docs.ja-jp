---
title: SQLite データベース プロバイダー - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 3e2f7698-fec2-4cec-9e2d-2e3e0074120c
ms.technology: entity-framework-core
uid: core/providers/sqlite/index
ms.openlocfilehash: 2e392f382f0e6f4d092a362c44f2149eb336db17
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678749"
---
# <a name="sqlite-ef-core-database-provider"></a>SQLite EF Core データベース プロバイダー

このデータベース プロバイダーにより、SQLite と共に Entity Framework Core を使用できます。 このプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されています。

## <a name="install"></a>インストール

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
