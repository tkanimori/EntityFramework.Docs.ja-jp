---
title: データベース プロバイダー - EF Core
author: rowanmiller
ms.date: 02/23/2018
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/providers/index
ms.openlocfilehash: 0995c2cb403cf4f0db1462f1e3446a13563a1064
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73654815"
---
# <a name="database-providers"></a>データベース プロバイダー

Entity Framework Core は、データベース プロバイダーと呼ばれるプラグイン ライブラリを通じて多数の異なるデータベースにアクセスできます。

## <a name="current-providers"></a>現在のプロバイダー

> [!IMPORTANT]  
> EF Core プロバイダーは、さまざまなソースによってビルドされます。 一部のプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されていません。 プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。 また、バージョンの互換性情報の詳細について、各プロバイダーのドキュメントを確認してください。

| NuGet パッケージ                                                                                                        | サポートされているデータベース エンジン | メンテナンス/仕入先                                                           | メモ/要件 | 役に立つリンク                                                                                                                                                                                       |
|:---------------------------------------------------------------------------------------------------------------------|:---------------------------|:------------------------------------------------------------------------------|:---------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)    | SQL Server 2012 以降    | [EF Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | [docs](xref:core/providers/sql-server/index)                                                                                                                                                       |
| [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)          | SQLite 3.7 以降         | [EF Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | [docs](xref:core/providers/sqlite/index)                                                                                                                                                           |
| [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)      | EF Core In-Memory データベース | [EF Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) | テストのみ     | [docs](xref:core/providers/in-memory/index)                                                                                                                                                        |
| [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)          | Azure Cosmos DB SQL API    | [EF Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                      | [docs](xref:core/providers/cosmos/index)                                                                                                                                                           |
| [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)        | PostgreSQL                 | [Npgsql 開発チーム](https://github.com/npgsql)                          |                      | [docs](https://www.npgsql.org/efcore/index.html)                                                                                                                                                   |
| [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql)                  | MySQL、MariaDB             | [Pomelo Foundation プロジェクト](https://github.com/PomeloFoundation)              |                      | [readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md)                                                                                               |
| [Pomelo.EntityFrameworkCore.MyCat](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MyCat)                  | MyCAT Server               | [Pomelo Foundation プロジェクト](https://github.com/PomeloFoundation)              | プレリリースのみ      | [readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MyCat/blob/master/README.md)                                                                                               |
| [EntityFrameworkCore.SqlServerCompact40](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)      | SQL Server Compact 4.0     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework       | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35)      | SQL Server Compact 3.5     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework       | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [FirebirdSql.EntityFrameworkCore.Firebird](https://www.nuget.org/packages/FirebirdSql.EntityFrameworkCore.Firebird/) | Firebird 2.5 および 3.x       | [Jiří Činčura](https://github.com/cincuranet)                                 |                      | [docs](https://github.com/cincuranet/FirebirdSql.Data.FirebirdClient/blob/master/Provider/docs/entity-framework-core.md)                                                                           |
| [EntityFrameworkCore.FirebirdSql](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSql/)                   | Firebird 2.5 および 3.x       | [Rafael Almeida](https://github.com/ralmsdeveloper)                           |                      | [wiki](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)                                                                                                                     |
| [MySql.Data.EntityFrameworkCore](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)                      | MySQL                      | [MySQL プロジェクト](https://dev.mysql.com)(Oracle)                               |                      | [docs](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework-core.html)                                                                                                         |
| [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)                             | Oracle DB 11.2 以降     | [Oracle](https://www.oracle.com/technetwork/topics/dotnet/)                   | プレリリース           | [Web サイト](https://www.oracle.com/technetwork/topics/dotnet/)                                                                                                                                       |
| [IBM.EntityFrameworkCore](https://www.nuget.org/packages/IBM.EntityFrameworkCore)                                    | Db2、Informix              | [IBM](https://ibm.com)                                                        | Windows のバージョン      | [ブログ](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [IBM.EntityFrameworkCore-lnx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx)                            | Db2、Informix              | [IBM](https://ibm.com)                                                        | Linux バージョン        | [ブログ](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [IBM.EntityFrameworkCore-osx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-osx)                            | Db2、Informix              | [IBM](https://ibm.com)                                                        | macOS バージョン        | [ブログ](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [EntityFrameworkCore.Jet](https://www.nuget.org/packages/EntityFrameworkCore.Jet/)                                   | Microsoft Access ファイル     | [Bubi](https://github.com/bubibubi)                                           | .NET Framework       | [readme](https://github.com/bubibubi/EntityFrameworkCore.Jet/blob/master/docs/README.md)                                                                                                           |
| [EntityFrameworkCore.OpenEdge](https://www.nuget.org/packages/EntityFrameworkCore.OpenEdge/)                         | Progress OpenEdge          | [Alex Wiese](https://github.com/alexwiese)                                    |                      | [readme](https://github.com/alexwiese/EntityFrameworkCore.OpenEdge/blob/master/README.md)                                                                                                          |
| [Devart.Data.Oracle.EFCore](https://www.nuget.org/packages/Devart.Data.Oracle.EFCore/)                               | Oracle DB 9.2.0.4 以降  | [DevArt](https://www.devart.com/)                                             | 支払い済み                 | [docs](https://www.devart.com/dotconnect/oracle/docs/)                                                                                                                                             |
| [Devart.Data.PostgreSql.EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore/)                       | PostgreSQL 8.0 以降     | [DevArt](https://www.devart.com/)                                             | 支払い済み                 | [docs](https://www.devart.com/dotconnect/postgresql/docs/)                                                                                                                                         |
| [Devart.Data.SQLite.EFCore](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore/)                               | SQLite 3 以降           | [DevArt](https://www.devart.com/)                                             | 支払い済み                 | [docs](https://www.devart.com/dotconnect/sqlite/docs/)                                                                                                                                             |
| [Devart.Data.MySql.EFCore](https://www.nuget.org/packages/Devart.Data.MySql.EFCore/)                                 | MySQL 5 以降            | [DevArt](https://www.devart.com/)                                             | 支払い済み                 | [docs](https://www.devart.com/dotconnect/mysql/docs/)                                                                                                                                              |
| [FileContextCore](https://www.nuget.org/packages/FileContextCore/)                                 | ファイルにデータを格納            | [Morris Janatzek](https://github.com/morrisjdev)                                             | 開発用                 | [readme](https://github.com/morrisjdev/FileContextCore/blob/master/README.md)                                                                                                                                              |

## <a name="adding-a-database-provider-to-your-application"></a>データベース プロバイダーをアプリケーションに追加する

EF Core のほとんどのデータベース プロバイダーは、NuGet パッケージとして配布され、次のようにインストールできます。

## <a name="net-core-clitabdotnet-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

``` console
dotnet add package provider_package_name
```

## <a name="visual-studiotabvs"></a>[Visual Studio](#tab/vs)

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
