---
title: データベース プロバイダー - EF Core
author: rowanmiller
ms.author: divega
ms.date: 2/23/2018
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
ms.technology: entity-framework-core
uid: core/providers/index
ms.openlocfilehash: 6f058698f78c787fc6c313486874b0af2183f97a
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "37949317"
---
# <a name="database-providers"></a>データベース プロバイダー

Entity Framework Core は、データベース プロバイダーと呼ばれるプラグイン ライブラリを通じて多数の異なるデータベースにアクセスできます。

## <a name="current-providers"></a>現在のプロバイダー
> [!IMPORTANT]  
> EF Core プロバイダーは、さまざまなソースによってビルドされます。 一部のプロバイダーは、[Entity Framework Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore)の一部として保守管理されていません。 プロバイダーの利用を検討するとき、要件に合うよう、品質、使用許諾、サポートなどを必ず評価してください。 また、バージョンの互換性情報の詳細について、各プロバイダーのドキュメントを確認してください。

| NuGet パッケージ                                                                                                        | サポートされているデータベース エンジン | メンテナンス/仕入先                                                           | メモ/要件           | 役に立つリンク                                                                                                                                                                                       |
|:---------------------------------------------------------------------------------------------------------------------|:---------------------------|:------------------------------------------------------------------------------|:-------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)    | SQL Server 2008 以降    | [EF Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                                | [docs](xref:core/providers/sql-server/index)                                                                                                                                                       |
| [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)          | SQLite 3.7 以降         | [EF Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) |                                | [docs](xref:core/providers/sqlite/index)                                                                                                                                                           |
| [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)      | EF Core In-Memory データベース | [EF Core プロジェクト](https://github.com/aspnet/EntityFrameworkCore/) (Microsoft) | テストのみ               | [docs](xref:core/providers/in-memory/index)                                                                                                                                                        |
| [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL)        | PostgreSQL                 | [Npgsql 開発チーム](https://github.com/npgsql)                          |                                | [docs](http://www.npgsql.org/efcore/index.html)                                                                                                                                                    |
| [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql)                  | MySQL、MariaDB             | [Pomelo Foundation プロジェクト](https://github.com/PomeloFoundation)              |                                | [readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql/blob/master/README.md)                                                                                               |
| [Pomelo.EntityFrameworkCore.MyCat](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MyCat)                  | MyCAT Server               | [Pomelo Foundation プロジェクト](https://github.com/PomeloFoundation)              | EF Core 1.1 までのプレリリース | [readme](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MyCat/blob/master/README.md)                                                                                               |
| [EntityFrameworkCore.SqlServerCompact40](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact40)      | SQL Server Compact 4.0     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework                 | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [EntityFrameworkCore.SqlServerCompact35](https://www.nuget.org/packages/EntityFrameworkCore.SqlServerCompact35)      | SQL Server Compact 3.5     | [Erik Ejlskov Jensen](https://github.com/ErikEJ/)                             | .NET Framework                 | [wiki](https://github.com/ErikEJ/EntityFramework.SqlServerCompact/wiki/Using-EF-Core-with-SQL-Server-Compact-in-Traditional-.NET-Applications)                                                     |
| [MySql.Data.EntityFrameworkCore](https://www.nuget.org/packages/MySql.Data.EntityFrameworkCore)                      | MySQL                      | [MySQL プロジェクト](http://dev.mysql.com)(Oracle)                                | プレリリース                    | [docs](https://dev.mysql.com/doc/connector-net/en/)                                                                                                                                                |
| [FirebirdSql.EntityFrameworkCore.Firebird](https://www.nuget.org/packages/FirebirdSql.EntityFrameworkCore.Firebird/) | Firebird 2.5 および 3.x       | [Jiří Činčura](https://github.com/cincuranet)                                 | EF Core 2.0 以降            | [docs](https://github.com/cincuranet/FirebirdSql.Data.FirebirdClient/blob/master/Provider/docs/entity-framework-core.md)                                                                           |
| [EntityFrameworkCore.FirebirdSql](https://www.nuget.org/packages/EntityFrameworkCore.FirebirdSql/)                   | Firebird 2.5 および 3.x       | [Rafael Almeida](https://github.com/ralmsdeveloper)                           | EF Core 2.0 以降            | [wiki](https://github.com/ralmsdeveloper/EntityFrameworkCore.FirebirdSQL/wiki)                                                                                                                     |
| [IBM.EntityFrameworkCore](https://www.nuget.org/packages/IBM.EntityFrameworkCore)                                    | Db2、Informix              | [IBM](https://ibm.com)                                                        | Windows のバージョン                | [ブログ](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [IBM.EntityFrameworkCore-lnx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx)                            | Db2、Informix              | [IBM](https://ibm.com)                                                        | Linux バージョン                  | [ブログ](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [IBM.EntityFrameworkCore-osx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-osx)                            | Db2、Informix              | [IBM](https://ibm.com)                                                        | macOS バージョン                  | [ブログ](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/Creating_Entity_Data_Model_using_IBM_Data_Server_providers_for_Entity_Framework_Core?lang=en) |
| [Devart.Data.Oracle.EFCore](https://www.nuget.org/packages/Devart.Data.Oracle.EFCore/)                               | Oracle 9.2.0.4 以降     | [DevArt](https://www.devart.com/)                                             | 支払い済み                           | [docs](https://www.devart.com/dotconnect/oracle/docs/)                                                                                                                                             |
| [Devart.Data.PostgreSql.EFCore](https://www.nuget.org/packages/Devart.Data.PostgreSql.EFCore/)                       | PostgreSQL 8.0 以降     | [DevArt](https://www.devart.com/)                                             | 支払い済み                           | [docs](https://www.devart.com/dotconnect/postgresql/docs/)                                                                                                                                         |
| [Devart.Data.SQLite.EFCore](https://www.nuget.org/packages/Devart.Data.SQLite.EFCore/)                               | SQLite 3 以降           | [DevArt](https://www.devart.com/)                                             | 支払い済み                           | [docs](https://www.devart.com/dotconnect/sqlite/docs/)                                                                                                                                             |
| [Devart.Data.MySql.EFCore](https://www.nuget.org/packages/Devart.Data.MySql.EFCore/)                                 | MySQL 5 以降            | [DevArt](https://www.devart.com/)                                             | 支払い済み                           | [docs](https://www.devart.com/dotconnect/mysql/docs/)                                                                                                                                              |
| [EntityFrameworkCore.Jet](https://www.nuget.org/packages/EntityFrameworkCore.Jet/)                                   | Microsoft Access ファイル     | [Bubi](https://github.com/bubibubi)                                           | EF Core 2.0、.NET Framework    | [readme](https://github.com/bubibubi/EntityFrameworkCore.Jet/blob/master/docs/README.md)                                                                                                           |

## <a name="future-providers"></a>将来のプロバイダー

### <a name="cosmos-db"></a>Cosmos DB

Cosmos DB の DocumentDB API 向けの EF Core プロバイダーを開発しています。 これは弊社が生成した最初の完全なドキュメント指向データベース プロバイダーであり、この演習から学んだ内容は、2.1 以降のリリースの設計の改善のために通知されます。 現在の計画では、2.1 のタイムフレームで、プロバイダーの初期プレビューを公開する予定です。

### <a name="oracle"></a>Oracle
Oracle .NET チームは、2018 年第 3 四半期ごろに EF Core 2.0 のファーストパーティー プロバイダーをリリースする計画であると発表しました。 詳細については、「[statement of direction for .NET Core and Entity Framework Core](http://www.oracle.com/technetwork/topics/dotnet/tech-info/odpnet-dotnet-ef-core-sod-4395108.pdf)」(.NET Core および Entity Framework Core の方向性の説明) を参照してください。
リリース時期も含め、このプロバイダーに関するご質問は [Oracle コミュニティ サイト](https://community.oracle.com/)に直接お寄せください。

その間に、EF チームは、[Oracle データベース用のサンプル EF Core プロバイダー](https://github.com/aspnet/EntityFrameworkCore/blob/dev/samples/OracleProvider/README.md)を生成しました。 プロジェクトの目的は、Microsoft によって所有される EF Core プロバイダーを生成することではなく、EF Core のリレーショナルとベース機能のギャップを特定しやすくすることです。これは、Oracle のサポートを強化し、Oracle またはサードパーティーによる EF Core 用の他の Oracle プロバイダーの開発をすぐに開始できるようにするために解決する必要があります。

Microsoft は、サンプルの実装を改善するための貢献を検討します。 さらに、このサンプルを開始点として、EF Core 用のオープンソース Oracle プロバイダーを開発するためのコミュニティの作業を歓迎し奨励します。

## <a name="adding-a-database-provider-to-your-application"></a>データベース プロバイダーをアプリケーションに追加する

EF Core のほとんどのデータベース プロバイダーは、NuGet パッケージとして配布されます。 つまり、コマンド ラインで `dotnet` ツールを使用してそれらをインストールできます。

``` console
dotnet add package provider_package_name
```

あるいは、Visual Studio で NuGet のパッケージ マネージャー コンソールを使用する場合、次のようになります。

``` powershell
install-package provider_package_name
```

インストールされたら、`DbContext` で、`OnConfiguring` メソッド、または依存関係注入コンテナーを使用している場合は `AddDbContext` メソッドを使用してプロバイダーを構成します。 たとえば、次の行は、渡された接続文字列を使用して、SQL Server プロバイダーを構成します。

``` csharp
optionsBuilder.UseSqlServer(
    "Server=(localdb)\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;");
```  

データベース プロバイダーは、特定のデータベースに固有の機能を有効にするように EF Core を拡張できます。 いくつかの概念はほとんどのデータベースに共通しており、プライマリ EF Core コンポーネントに含まれています。 そのような概念には、LINQ のクエリの表現、トランザクション、データベースから読み込んだときのオブジェクトの変更追跡などがあります。 いくつかの概念は、特定のプロバイダーに固有のものです。 たとえば、SQL Server プロバイダーの場合、[メモリが最適化されたテーブル (SQL Server 固有の機能) を構成できます](xref:core/providers/sql-server/memory-optimized-tables)。 その他の概念は、プロバイダーのクラスに固有のものです。 たとえば、リレーショナル データベースの EF Core プロバイダーの場合、一般的な `Microsoft.EntityFrameworkCore.Relational` ライブラリに基づいてビルドします。このライブラリは、テーブルと列のマッピングや外部キー制約などを構成するための API を提供します。プロバイダーは通常、NuGet パッケージとして配布されます。

> [!IMPORTANT]  
> EF Core の新しい修正プログラムのバージョンがリリースされるときには、多くの場合 `Microsoft.EntityFrameworkCore.Relational` パッケージの更新プログラムが含まれます。 ユーザーがリレーショナル データベース プロバイダーを追加するとき、このパッケージは、アプリケーション依存関係が推移的になります。 しかし、多くのプロバイダーが EF Core から独立してリリースされ、そのパッケージの新しい修正プログラムのバージョンに依存するように更新されないことがあります。 すべてのバグ修正が確実に取得されるようにするために、`Microsoft.EntityFrameworkCore.Relational` の修正プログラム バージョンをアプリケーションの直接の依存関係として追加することをお勧めします。
