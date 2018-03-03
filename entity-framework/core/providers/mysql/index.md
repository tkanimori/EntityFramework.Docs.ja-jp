---
title: "MySQL データベース プロバイダー - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 4900b882-79c5-40d2-a44a-ccb0292f6ed9
ms.technology: entity-framework-core
uid: core/providers/mysql/index
ms.openlocfilehash: 1500d017cb463c3f394131a79b9063ff90cce5e2
ms.sourcegitcommit: 6ed04bb05a3d05c367f0f55616807af2bf4037ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="mysql-ef-core-database-provider"></a>MySQL EF Core データベース プロバイダー

このデータベース プロバイダーにより、MySQL と共に Entity Framework Core を使用できます。 このプロバイダーは [MySQL プロジェクト](http://dev.mysql.com)の一部として保守管理されます。

> [!WARNING]  
> このプロバイダーはプレリリースです。

> [!NOTE]  
> このプロバイダーは、Entity Framework Core プロジェクトの一部として保守管理されていません。 サードパーティ プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。

## <a name="install"></a>インストール

[MySql.Data.EntityFrameworkCore NuGet パッケージ](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)をインストールします。

``` powershell
Install-Package MySql.Data.EntityFrameworkCore -Pre
```

## <a name="get-started"></a>開始するには

「[Starting with MySQL EF Core provider and Connector/Net 7.0.4](http://insidemysql.com/howto-starting-with-mysql-ef-core-provider-and-connectornet-7-0-4/)」 (MySQL EF Core プロバイダーと Connector/Net 7.0.4 の入門) を参照してください。

## <a name="supported-database-engines"></a>サポートされているデータベース エンジン

* MySQL

## <a name="supported-platforms"></a>サポートされているプラットフォーム

* .NET Framework (4.5.1 以降)

* .NET Core

バージョンの互換性の情報については、MySQL のドキュメントの[ここ](https://dev.mysql.com/doc/connector-net/en/connector-net-versions.html)と[ここ](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)を確認してください
