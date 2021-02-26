---
title: EF Core の NuGet パッケージ
description: Entity Framework Core の各種 NuGet パッケージの概要
author: ajcvickers
ms.date: 01/21/2021
uid: core/what-is-new/nuget-packages
ms.openlocfilehash: 4b6e210f2324ea97e006d681d399bfdd6918d1b4
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100544583"
---
# <a name="ef-core-nuget-packages"></a>EF Core の NuGet パッケージ

Entity Framework Core (EF Core) は [NuGet](https://www.nuget.org/) パッケージとして出荷されます。 アプリケーションで必要なパッケージは、次のものに依存します。

- 使用されているデータベース システムの種類 (SQL Server、SQLite など)
- 必要とする EF Core 機能

パッケージをインストールするための通常のプロセスは次のとおりです。

- データベース プロバイダーを決定し、適切なパッケージをインストールします ([下記参照](#database-providers))
- また、リレーショナル プロバイダーを使用している場合は、[Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/) および [Microsoft.EntityFrameworkCore.Relational](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational/) をインストールします。 これにより、一貫性のあるバージョンが確実に使用されるようになります。また、新しいパッケージ バージョンが出荷される時期が、NuGet によって通知されるようになります。
- 必要に応じて、必要としているツールの種類を決定し、それに適したパッケージをインストールします (下記参照)。

EF Core の概要については、[Entity Framework Core の概要チュートリアル](xref:core/get-started/overview/first-app)に関するページを参照してください。

## <a name="package-versions"></a>パッケージのバージョン

必ず、Microsoft から出荷されたすべての EF Core パッケージの同じバージョンをインストールしてください。 たとえば、Microsoft.EntityFrameworkCore.SqlServer のバージョン 5.0.3 をインストールする場合は、他の Microsoft.EntityFrameworkCore.* パッケージもすべて 5.0.3 にする必要があります。

また、外部パッケージが、使用されている EF Core のバージョンと互換性があることを確認します。 特に、使用している EF Core のバージョンが外部データベース プロバイダーでサポートされていることを確認します。 EF Core の新しいメジャー バージョンには、通常、更新されたデータベース プロバイダーが必要です。

> [!WARNING]
> NuGet によって、一貫性のあるパッケージ バージョンが適用されることはありません。 ご利用の `csproj` ファイルまたは同等のファイル内で参照しているバージョンを常に慎重にご確認ください。

## <a name="database-providers"></a>データベース プロバイダー

EF Core では、"データベース プロバイダー" を使用することによって、さまざまなデータベース システムがサポートされています。 各システムには、NuGet パッケージとして出荷される独自のデータベース プロバイダーがあります。 アプリケーションでは、これらのプロバイダー パッケージの 1 つまたは複数をインストールする必要があります。

一般的なデータベース プロバイダーを次の表に示します。 使用可能なプロバイダーの全体の一覧については、「[データベース プロバイダー](xref:core/providers/index)」を参照してください。

| データベース システム                   | Package
|:----------------------------------|----------------------
| SQL Server および SQL Azure          | [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer)
| SQLite                            | [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite)
| Azure Cosmos DB                   | [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos)
| PostgreSQL                        | [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)*
| MySQL                             | [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)*
| EF Core のインメモリ データベース**      | [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)

*これらは、コミュニティによって開発および出荷された人気のある高品質のオープンソース プロバイダーです。 一覧されている他のプロバイダーは、Microsoft から出荷されています。

**インメモリ データベースを使用するかどうかは、慎重に検討してください。 これは運用環境で使用するように設計されたものではありません。また、[テストに最適なソリューション](xref:core/testing/index)ではない場合もあります。

## <a name="tools"></a>ツール

[EF Core の移行](xref:core/managing-schemas/migrations/index)および[既存のデータベースからのリバース エンジニアリング (スキャフォールディング)](xref:core/managing-schemas/scaffolding) にツールを使用するには、適切なツール パッケージをインストールする必要があります。

- Visual Studio [パッケージ マネージャー コンソール](/nuget/consume-packages/install-use-packages-powershell)で動作する PowerShell ツール用の [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)
- クロスプラットフォーム コマンド ラインツール用の [dotnet-ef](https://www.nuget.org/packages/dotnet-ef/) および [Microsoft.EntityFrameworkCore.Design](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Design/)

プロジェクトに、またはグローバルに `dotnet-ef` ツールを正しくインストールする方法など、EF Core ツールの使い方の詳細については、「[Entity Framework Core ツールのリファレンス](xref:core/cli/index)」を参照してください。

> [!TIP]
> 既定では、Microsoft.EntityFrameworkCore.Design パッケージは、ご利用のアプリケーションと一緒に展開されないようにインストールされます。 これはまた、その型を他のプロジェクトで推移的に使用できないことを意味します。 パッケージ内の型にアクセスする必要がある場合は、ご利用の `.csproj` ファイルまたは同等のファイル内で通常の `PackageReference` を使用します。 詳細については、「[デザイン時サービス](xref:core/cli/services)」を参照してください。

## <a name="extension-packages"></a>拡張機能パッケージ

Microsoft とサード パーティの両者が NuGet パッケージとして多数の [EF Core の拡張機能](xref:core/extensions/index)を発行しています。 一般的に使用されるパッケージには次のものがあります。

| 機能                                | Package | 追加の依存関係
|:---------------------------------------------|---------|------------------------
| 遅延読み込みと変更追跡のためのプロキシ | [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) | [Castle.Core](https://www.nuget.org/packages/Castle.Core/)
| SQL Server の空間サポート               | [Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) および [NetTopologySuite.IO.SqlServerBytes](https://www.nuget.org/packages/NetTopologySuite.IO.SqlServerBytes/)
| SQLite の空間サポート                   | [Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) および [NetTopologySuite.IO.SpatiaLite](https://www.nuget.org/packages/NetTopologySuite.IO.SpatiaLite/)
| PostgreSQL の空間サポート               | [Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/) と [NetTopologySuite.IO.PostGIS](https://www.nuget.org/packages/NetTopologySuite.IO.PostGIS/) ([Npgsql.NetTopologySuite](https://www.nuget.org/packages/Npgsql.NetTopologySuite/) を使用)
| MySQL の空間サポート                    | [Pomelo.EntityFrameworkCore.MySql.NetTopologySuite](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql.NetTopologySuite) | [NetTopologySuite](https://www.nuget.org/packages/NetTopologySuite/)

## <a name="other-packages"></a>その他のパッケージ

その他の EF Core パッケージは、データベース プロバイダー パッケージの依存関係として取り込まれます。 ただし、新しいバージョンがリリースされたときに NuGet によって通知が提供されるように、これらのパッケージの明示的なパッケージ参照を追加することもできます。

| 機能                                              | Package
|:-----------------------------------------------------------|--------
| EF Core の基本的な機能                                | [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/)
| 一般的なリレーショナル データベースの機能                   | [Microsoft.EntityFrameworkCore.Relational](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational/)
| EF Core 属性用の簡易パッケージ (その他)           | [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/)
| EF Core の使用状況の Roslyn コード アナライザー                    | [Microsoft.EntityFrameworkCore.Analyzers](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Analyzers/)
| ネイティブ SQLite 依存関係のない EF Core SQLite プロバイダー | [Microsoft.EntityFrameworkCore.Sqlite.Core](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.Core/)

## <a name="packages-for-database-provider-testing"></a>データベース プロバイダー テスト用のパッケージ

外部の GitHub リポジトリに構築されたデータベース プロバイダーをテストするには、次のパッケージを使用します。 例については、[efcore.pg](https://github.com/npgsql/efcore.pg) および [Pomelo.EntityFrameworkCore.MySql](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) に関するページを参照してください。 アプリケーションで、これらのパッケージをインストールしないでください。

| 機能                                              | Package
|:-----------------------------------------------------------|--------
| 任意のデータベース プロバイダーのテスト                            | [Microsoft.EntityFrameworkCore.Specification.Tests](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Specification.Tests/)
| リレーショナル データベース プロバイダーのテスト                    | [Microsoft.EntityFrameworkCore.Relational.Specification.Tests](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational.Specification.Tests/)

## <a name="obsolete-packages"></a>古いパッケージ

次の古いパッケージはインストール **しない** でください。それらがご利用のプロジェクトに現在インストールされている場合は、削除してください。

- Microsoft.EntityFrameworkCore.Relational.Design
- Microsoft.EntityFrameworkCore.Tools.DotNet
- Microsoft.EntityFrameworkCore.SqlServer.Design
- Microsoft.EntityFrameworkCore.Sqlite.Design
- Microsoft.EntityFrameworkCore.Relational.Design.Specification.Tests
