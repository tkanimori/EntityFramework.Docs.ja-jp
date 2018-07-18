---
title: .NET Core - 新しいデータベース - EF Core の概要
author: rick-anderson
ms.author: riande
ms.author2: tdykstra
description: Entity Framework Core を使用した .NET Core の概要
keywords: .NET Core, Entity Framework Core, VS コード, Visual Studio コード, Mac, Linux
ms.date: 06/05/2018
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
ms.technology: entity-framework-core
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: e4eafed037325237345efbc3d7d42b32270a54e3
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "37911503"
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a>新しいデータベースを使用した .NET Core コンソール アプリでの EF Core の概要

このチュートリアルでは、Entity Framework Core を使用して SQLite データベースにデータ アクセスを実行する .NET Core コンソール アプリを作成します。 モデルからの移行によってデータベースを作成します。 ASP.NET Core MVC を使用する Visual Studio バージョンについては [ASP.NET Core - 新しいデータベース](xref:core/get-started/aspnetcore/new-db)に関する記事をご覧ください。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite)は GitHub で確認できます。

## <a name="prerequisites"></a>必須コンポーネント

[.NET Core SDK](https://www.microsoft.com/net/core) 2.1

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

* 新しいコンソール プロジェクトを作成します。

``` Console
dotnet new console -o ConsoleApp.SQLite
cd ConsoleApp.SQLite/
```

## <a name="install-entity-framework-core"></a>Entity Framework Core をインストールする

EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。 このチュートリアルでは SQLite を使用します。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。

* Microsoft.EntityFrameworkCore.Sqlite と Microsoft.EntityFrameworkCore.Design をインストールします。

``` Console
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
```

* `dotnet restore` を実行して新しいパッケージをインストールします。

## <a name="create-the-model"></a>モデルを作成する

モデルを構成するコンテキストとエンティティ クラスを定義します。

* 次の内容が含まれる新しい *Model.cs* ファイルを作成します。

[!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

ヒント: 実際のアプリケーションでは、各クラスは別のファイルに、接続文字列は構成ファイルにそれぞれ記述します。 チュートリアルをわかりやすくするために、すべてを 1 つのファイルに記述しています。

## <a name="create-the-database"></a>データベースの作成

モデルを作成したら、[移行](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)を利用してデータベースを作成します。

* `dotnet ef migrations add InitialCreate` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。
* `dotnet ef database update` を実行して、新しい移行をデータベースに適用します。 このコマンドは、移行を適用する前に、データベースを作成します。

*blogging.db** SQLite DB はプロジェクト ディレクトリにあります。

## <a name="use-your-model"></a>モデルを使用する

* *Program.cs* を開き、内容を次のコードに置き換えます。

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* アプリをテストします。

  `dotnet run`

  1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに表示されます。

  ``` Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a>モデルを変更する

- モデルを変更する場合、`dotnet ef migrations add` コマンドを使用して新しい[移行](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)をスキャフォールディングし、対応するスキーマの変更をデータベースに対して行います。 スキャフォールディング コードを確認 (および必要な変更) したら、`dotnet ef database update` コマンドを使用してデータベースに変更を適用できます。
- EF はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。
- SQLite には制限があるため、SQLite はすべての移行 (スキーマ変更) をサポートするわけではありません。 [SQLite の制限](../../providers/sqlite/limitations.md)に関する記事をご覧ください。 新しい開発の場合、モデルを変更したときは移行するのではなく、データベースを削除して新たに作成することを検討してください。

## <a name="additional-resources"></a>その他のリソース

* [.NET core - SQLite を使用した新しいデータベース](xref:core/get-started/netcore/new-db-sqlite) - クロスプラットフォーム コンソールでの EF のチュートリアル。
* [Mac または Linux での ASP.NET Core MVC の概要](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [Visual Studio を使用した ASP.NET Core MVC の概要](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [Visual Studio を使用した ASP.NET Core と Entity Framework Core の概要](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
