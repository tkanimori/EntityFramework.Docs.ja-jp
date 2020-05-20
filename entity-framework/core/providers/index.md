---
title: データベース プロバイダー - EF Core
author: ajcvickers
ms.date: 12/17/2019
uid: core/providers/index
ms.openlocfilehash: 695f8c311eb33f134bed053b1550612040da6fae
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672869"
---
# <a name="database-providers"></a>データベース プロバイダー

Entity Framework Core は、データベース プロバイダーと呼ばれるプラグイン ライブラリを通じて多数の異なるデータベースにアクセスできます。

## <a name="current-providers"></a>現在のプロバイダー

> [!IMPORTANT]  
> EF Core プロバイダーは、さまざまなソースによってビルドされます。 一部のプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されていません。 プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。 また、バージョンの互換性情報の詳細について、各プロバイダーのドキュメントを確認してください。

> [!IMPORTANT]  
> 通常、EF Core プロバイダーはマイナー バージョン間では動作しますが、メジャー バージョン間では動作しません。 たとえば、EF Core 2.1 用にリリースされたプロバイダーは EF Core 2.2 では動作しますが、EF Core 3.0 では動作しません。 

| NuGet パッケージ                                                                                                        | サポートされているデータベース エンジン | メンテナンス/仕入先                                                           | メモ/要件 | ビルドの対象バージョン | 役に立つリンク                                                                                                                                                                                       |
|:---------------------------------------------------------------------------------------------------------------------|:---------------------------|:------------------------------------------------------------------------------|:---------------------|:------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)    | SQL Server 2012 以降    | [EF Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | 3.1               | [docs](xref:core/providers/sql-server/index)                                                                                                                                                       |
| [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)          | SQLite 3.7 以降         | [EF Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | 3.1               | [docs](xref:core/providers/sqlite/index)                                                                                                                                                           |
| [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)      | EF Core In-Memory データベース | [EF Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) | [制限事項](xref:core/miscellaneous/testing/in-memory)                 | 3.1               | [docs](xref:core/providers/in-memory/index)                                                                                                                                                        |
| [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)          | Azure Cosmos DB SQL API    | [EF Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | 3.1               | [docs](xref:core/providers/cosmos/index)                                                                                                                                                           |
| [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)        | PostgreSQL                 | [Npgsql 開発チーム](https://github.com/npgsql)                          |                      | 3.1               | [docs](https://www.npgsql.org/efcore/index.html)                                                                                                                                                   |
| [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql)                  | MySQL、MariaDB             | [Pomelo Foundation プロジェクト](https://github.com/PomeloFoundation)              |                      | 3.1               | [readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md)                                                                                               |
| [Devart.Data.MySql.EFCore](https://www.nuget.org/packages/Devart.Data.MySql.EFCore/)                                 | MySQL 5 以降            | [DevArt](https://www.devart.com/)                                             | 支払い済み                 | 3.0               | [docs](https://www.devart.com/dotconnect/mysql/docs/)                                                                                                                                              |
| [Devart.Data.Oracle.EFCore](https://www.nuget.org/packages/Devart.Data.Oracle.EFCore/)                               | Oracle DB 9.2.0.4 以降  | [DevArt](https://www.devart.com/)                                             | 支払い済み                 | 3.0               | [docs](https://www.devart.com/dotconnect/oracle/docs/)                                                                                                                                             |
| [Devart.Data.PostgreSql.EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore/)                       | PostgreSQL 8.0 以降     | [DevArt](https://www.devart.com/)                                             | 支払い済み                 | 3.0               | [docs](https://www.devart.com/dotconnect/postgresql/docs/)                                                                                                                                         |
| [Devart.Data.SQLite.EFCore](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore/)                               | SQLite 3 以降           | [DevArt](https://www.devart.com/)                                             | 支払い済み                 | 3.0               | [docs](https://www.devart.com/dotconnect/sqlite/docs/)                                                                                                                                             |
| [FileContextCore](https://www.nuget.org/packages/FileContextCore/)                                                   | ファイルにデータを格納       | [Morris Janatzek](https://github.com/morrisjdev)                              | 開発用 | 3.0               | [readme](https://github.com/morrisjdev/FileContextCore/blob/master/README.md)                                                                                                                                              |
| [EntityFrameworkCore.Jet](https://www.nuget.org/packages/EntityFrameworkCore.Jet/)                                   | Microsoft Access ファイル     | [Bubi](https://github.com/bubibubi)                                           | .NET Framework       | 2.2               | [readme](https://github.com/bubibubi/EntityFrameworkCore.Jet/blob/master/docs/README.md)                                                                                                           |
| [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35)      | SQL Server Compact 3.5     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework       | 2.2               | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [EntityFrameworkCore.SqlServerCompact40](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)      | SQL Server Compact 4.0     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework       | 2.2               | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [FirebirdSql.EntityFrameworkCore.Firebird](https://www.nuget.org/packages/FirebirdSql.EntityFrameworkCore.Firebird/) | Firebird 2.5 および 3.x       | [Jiří Činčura](https://github.com/cincuranet)                                 |                      | 3.1               | [docs](https://github.com/cincuranet/FirebirdSql.Data.FirebirdClient/blob/master/Provider/docs/entity-framework-core.md)                                                                           |
| [Teradata.EntityFrameworkCore](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                         | Teradata データベース 16.10 以上 | [Teradata](https://downloads.teradata.com/download/connectivity/net-data-provider-for-teradata) | | 2.2               |[Web サイト](https://www.nuget.org/packages/Teradata.EntityFrameworkCore/)                                                                                                                            |
| [EntityFrameworkCore.FirebirdSql](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSql/)                   | Firebird 2.5 および 3.x       | [Rafael Almeida](https://github.com/ralmsdeveloper)                           |                      | 2.1               | [wiki](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)                                                                                                                     |
| [EntityFrameworkCore.OpenEdge](https://www.nuget.org/packages/EntityFrameworkCore.OpenEdge/)                         | Progress OpenEdge          | [Alex Wiese](https://github.com/alexwiese)                                    |                      | 2.1               | [readme](https://github.com/alexwiese/EntityFrameworkCore.OpenEdge/blob/master/README.md)                                                                                                          |
| [MySql.Data.EntityFrameworkCore](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)                      | MySQL                      | [MySQL プロジェクト](https://dev.mysql.com)(Oracle)                               |                      | 2.1               | [docs](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)                                                                                                         |
| [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)                             | Oracle DB 11.2 以降     | [Oracle](https://www.oracle.com/technetwork/topics/dotnet/)                   |                      | 2.1               | [Web サイト](https://www.oracle.com/technetwork/topics/dotnet/)                                                                                                                                       |
| [IBM.EntityFrameworkCore](https://www.nuget.org/packages/IBM.EntityFrameworkCore)                                    | Db2、Informix              | [IBM](https://ibm.com)                                                        | Windows のバージョン      | 2.0               | [ブログ](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [IBM.EntityFrameworkCore-lnx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx)                            | Db2、Informix              | [IBM](https://ibm.com)                                                        | Linux バージョン        | 2.0               | [ブログ](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [IBM.EntityFrameworkCore-osx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-osx)                            | Db2、Informix              | [IBM](https://ibm.com)                                                        | macOS バージョン        | 2.0               | [ブログ](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [Pomelo.EntityFrameworkCore.MyCat](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MyCat)                  | MyCAT Server               | [Pomelo Foundation プロジェクト](https://github.com/PomeloFoundation)              | プレリリースのみ      | 1.1               | [readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MyCat/blob/master/README.md)                                                                                               |

## <a name="adding-a-database-provider-to-your-application"></a>データベース プロバイダーをアプリケーションに追加する

EF Core のほとんどのデータベース プロバイダーは、NuGet パッケージとして配布され、次のようにインストールできます。

## <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package provider_package_name
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
install-package provider_package_name
```

***

インストールされたら、`DbContext` で、`OnConfiguring` メソッド、または依存関係注入コンテナーを使用している場合は `AddDbContext` メソッドを使用してプロバイダーを構成します。
たとえば、次の行は、渡された接続文字列を使用して、SQL Server プロバイダーを構成します。

``` csharp
optionsBuilder.UseSqlServer(
    "Server=(localdb)\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;");
```  

データベース プロバイダーは、特定のデータベースに固有の機能を有効にするように EF Core を拡張できます。
いくつかの概念はほとんどのデータベースに共通しており、プライマリ EF Core コンポーネントに含まれています。
そのような概念には、LINQ のクエリの表現、トランザクション、データベースから読み込んだときのオブジェクトの変更追跡などがあります。
いくつかの概念は、特定のプロバイダーに固有のものです。
たとえば、SQL Server プロバイダーの場合、[メモリが最適化されたテーブル (SQL Server 固有の機能) を構成できます](xref:core/providers/sql-server/memory-optimized-tables)。
その他の概念は、プロバイダーのクラスに固有のものです。
たとえば、リレーショナル データベースの EF Core プロバイダーの場合、一般的な `Microsoft.EntityFrameworkCore.Relational` ライブラリに基づいてビルドします。このライブラリは、テーブルと列のマッピングや外部キー制約などを構成するための API を提供します。プロバイダーは通常、NuGet パッケージとして配布されます。

> [!IMPORTANT]  
> EF Core の新しい修正プログラムのバージョンがリリースされるときには、多くの場合 `Microsoft.EntityFrameworkCore.Relational` パッケージの更新プログラムが含まれます。
> ユーザーがリレーショナル データベース プロバイダーを追加するとき、このパッケージは、アプリケーション依存関係が推移的になります。
> しかし、多くのプロバイダーが EF Core から独立してリリースされ、そのパッケージの新しい修正プログラムのバージョンに依存するように更新されないことがあります。
> すべてのバグ修正が確実に取得されるようにするために、`Microsoft.EntityFrameworkCore.Relational` の修正プログラム バージョンをアプリケーションの直接の依存関係として追加することをお勧めします。
