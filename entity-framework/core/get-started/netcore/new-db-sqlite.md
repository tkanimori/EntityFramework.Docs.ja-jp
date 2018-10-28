---
title: .NET Core - 新しいデータベース - EF Core の概要
author: rick-anderson
ms.author: riande
description: Entity Framework Core を使用した .NET Core の概要
ms.date: 08/03/2018
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: 6cebe14e179cb6998592f5d3823c114b3bda0138
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022312"
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a>新しいデータベースを使用した .NET Core コンソール アプリでの EF Core の概要

このチュートリアルでは、Entity Framework Core を使用して SQLite データベースに対してデータ アクセスを実行する .NET Core コンソール アプリを作成します。 モデルからの移行によってデータベースを作成します。 ASP.NET Core MVC を使用する Visual Studio バージョンについては [ASP.NET Core - 新しいデータベース](xref:core/get-started/aspnetcore/new-db)に関する記事をご覧ください。

[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite)。

## <a name="prerequisites"></a>必須コンポーネント

* [.NET Core 2.1 SDK](https://www.microsoft.com/net/core)

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

* 新しいコンソール プロジェクトを作成します。

  ``` Console
  dotnet new console -o ConsoleApp.SQLite
  ```
## <a name="change-the-current-directory"></a>現在のディレクトリを変更する

以降の手順では、アプリケーションに対して `dotnet` コマンドを発行する必要があります。

* 現在のディレクトリを、次のようにアプリケーションのディレクトリに変更します。

  ``` Console
  cd ConsoleApp.SQLite/
  ```
## <a name="install-entity-framework-core"></a>Entity Framework Core をインストールする

EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。 このチュートリアルでは SQLite を使用します。 使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。

* Microsoft.EntityFrameworkCore.Sqlite と Microsoft.EntityFrameworkCore.Design をインストールします。

  ```Console
  dotnet add package Microsoft.EntityFrameworkCore.Sqlite
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

* `dotnet restore` を実行して新しいパッケージをインストールします。

## <a name="create-the-model"></a>モデルを作成する

モデルを構成するコンテキストとエンティティ クラスを定義します。

* 次の内容が含まれる新しい *Model.cs* ファイルを作成します。

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

ヒント: 実際のアプリケーションでは、クラスはそれぞれ別々のファイルに記述し、接続文字列は構成ファイルまたは環境変数に記述します。 チュートリアルをわかりやすくするために、すべてを 1 つのファイルに記述しています。

## <a name="create-the-database"></a>データベースの作成

モデルを作成したら、[移行](xref:core/managing-schemas/migrations/index)を利用してデータベースを作成します。

* `dotnet ef migrations add InitialCreate` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。
* `dotnet ef database update` を実行して、新しい移行をデータベースに適用します。 このコマンドは、移行を適用する前に、データベースを作成します。

*blogging.db** SQLite DB はプロジェクト ディレクトリにあります。

## <a name="use-the-model"></a>モデルを使用する

* *Program.cs* を開き、内容を次のコードに置き換えます。

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* コンソールからアプリをテストします。 Visual Studio からアプリを実行するには、[Visual Studio のメモ](#vs)に関するページを参照してください。

  `dotnet run`

  1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに表示されます。

  ```Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a>モデルを変更する

- モデルに変更を加える場合は、`dotnet ef migrations add` コマンドを使用して、新しい[移行](xref:core/managing-schemas/migrations/index)をスキャフォールディングすることができます。 スキャフォールディング コードの確認 (および必要な変更) を行ったら、`dotnet ef database update` コマンドを使用してデータベースにスキーマの変更を適用できます。
- EF Core はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。
- SQLite データベース エンジンでは、他のほとんどのリレーショナル データベースでサポートされているスキーマ変更のうち特定のものがサポートされていません。 たとえば、`DropColumn` 操作はサポートされていません。 EF Core の移行では、そのような操作のためのコードが作成されます。 しかし、それらをデータベースに適用しようとしたりスクリプトの作成を試みたりすると、EF Core によって例外がスローされます。 [SQLite の制限](../../providers/sqlite/limitations.md)に関する記事をご覧ください。 新しい開発の場合は、モデルを変更するときの移行を使用するのではなく、データベースを削除して新たに作成することを検討してください。

<a name="vs"></a>
### <a name="run-from-visual-studio"></a>Visual Studio から実行する

Visual Studio からこのサンプルを実行するには、作業ディレクトリをプロジェクトのルートに設定する必要があります。 作業ディレクトリを設定しない場合、`SQLite Error 1: 'no such table: Blogs'` の `Microsoft.Data.Sqlite.SqliteException` がスローされます。

作業ディレクトリを設定するには:

* **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[プロパティ]** を選択します。
* 左ウィンドウの **[デバッグ]** タブを選択します。
* **作業ディレクトリ**をプロジェクトのディレクトリに設定します。
* 変更を保存します。

## <a name="additional-resources"></a>その他のリソース

* [チュートリアル: SQLite を使用した新しいデータベースでの ASP.NET Core での EF Core の概要](xref:core/get-started/aspnetcore/new-db)
* [チュートリアル: ASP.NET Core の Razor Pages の概要](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start)
* [チュートリアル: ASP.NET Core での Entity Framework Core を使用した Razor Pages](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
