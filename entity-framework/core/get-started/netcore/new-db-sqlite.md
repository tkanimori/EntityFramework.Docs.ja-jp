---
title: .NET Core - 新しいデータベース - EF Core の概要
author: rick-anderson
ms.author: riande
description: Entity Framework Core を使用した .NET Core の概要
ms.date: 08/03/2018
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: ec20040917a2bca8177924b6905b1cd79e5cd9da
ms.sourcegitcommit: 7a7da65404c9338e1e3df42576a13be536a6f95f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2018
ms.locfileid: "48834736"
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a><span data-ttu-id="85cfe-103">新しいデータベースを使用した .NET Core コンソール アプリでの EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="85cfe-103">Getting Started with EF Core on .NET Core Console App with a New database</span></span>

<span data-ttu-id="85cfe-104">このチュートリアルでは、Entity Framework Core を使用して SQLite データベースに対してデータ アクセスを実行する .NET Core コンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span> <span data-ttu-id="85cfe-105">モデルからの移行によってデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-105">You use migrations to create the database from the model.</span></span> <span data-ttu-id="85cfe-106">ASP.NET Core MVC を使用する Visual Studio バージョンについては [ASP.NET Core - 新しいデータベース](xref:core/get-started/aspnetcore/new-db)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="85cfe-106">See [ASP.NET Core - New database](xref:core/get-started/aspnetcore/new-db) for a Visual Studio version using ASP.NET Core MVC.</span></span>

<span data-ttu-id="85cfe-107">この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite)。</span><span class="sxs-lookup"><span data-stu-id="85cfe-107">View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="85cfe-108">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="85cfe-108">Prerequisites</span></span>

* [<span data-ttu-id="85cfe-109">.NET Core 2.1 SDK</span><span class="sxs-lookup"><span data-stu-id="85cfe-109">The .NET Core 2.1 SDK</span></span>](https://www.microsoft.com/net/core)

## <a name="create-a-new-project"></a><span data-ttu-id="85cfe-110">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="85cfe-110">Create a new project</span></span>

* <span data-ttu-id="85cfe-111">新しいコンソール プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-111">Create a new console project:</span></span>

  ``` Console
  dotnet new console -o ConsoleApp.SQLite
  ```
## <a name="change-the-current-directory"></a><span data-ttu-id="85cfe-112">現在のディレクトリを変更する</span><span class="sxs-lookup"><span data-stu-id="85cfe-112">Change the current directory</span></span>

<span data-ttu-id="85cfe-113">以降の手順では、アプリケーションに対して `dotnet` コマンドを発行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85cfe-113">In subsequent steps, we need to issue `dotnet` commands against the application.</span></span>

* <span data-ttu-id="85cfe-114">現在のディレクトリを、次のようにアプリケーションのディレクトリに変更します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-114">We change the current directory to the application's directory like this:</span></span>

  ``` Console
  cd ConsoleApp.SQLite/
  ```
## <a name="install-entity-framework-core"></a><span data-ttu-id="85cfe-115">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="85cfe-115">Install Entity Framework Core</span></span>

<span data-ttu-id="85cfe-116">EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="85cfe-116">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="85cfe-117">このチュートリアルでは SQLite を使用します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-117">This walkthrough uses SQLite.</span></span> <span data-ttu-id="85cfe-118">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="85cfe-118">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="85cfe-119">Microsoft.EntityFrameworkCore.Sqlite と Microsoft.EntityFrameworkCore.Design をインストールします。</span><span class="sxs-lookup"><span data-stu-id="85cfe-119">Install Microsoft.EntityFrameworkCore.Sqlite and Microsoft.EntityFrameworkCore.Design</span></span>

  ```Console
  dotnet add package Microsoft.EntityFrameworkCore.Sqlite
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

* <span data-ttu-id="85cfe-120">`dotnet restore` を実行して新しいパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="85cfe-120">Run `dotnet restore` to install the new packages.</span></span>

## <a name="create-the-model"></a><span data-ttu-id="85cfe-121">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="85cfe-121">Create the model</span></span>

<span data-ttu-id="85cfe-122">モデルを構成するコンテキストとエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-122">Define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="85cfe-123">次の内容が含まれる新しい *Model.cs* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-123">Create a new *Model.cs* file with the following contents.</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

<span data-ttu-id="85cfe-124">ヒント: 実際のアプリケーションでは、クラスはそれぞれ別々のファイルに記述し、接続文字列は構成ファイルまたは環境変数に記述します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-124">Tip: In a real application, you put each class in a separate file and put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="85cfe-125">チュートリアルをわかりやすくするために、すべてを 1 つのファイルに記述しています。</span><span class="sxs-lookup"><span data-stu-id="85cfe-125">To keep the tutorial simple, everything is contained in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="85cfe-126">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="85cfe-126">Create the database</span></span>

<span data-ttu-id="85cfe-127">モデルを作成したら、[移行](xref:core/managing-schemas/migrations/index)を利用してデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-127">Once you have a model, you use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

* <span data-ttu-id="85cfe-128">`dotnet ef migrations add InitialCreate` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-128">Run `dotnet ef migrations add InitialCreate` to scaffold a migration and create the initial set of tables for the model.</span></span>
* <span data-ttu-id="85cfe-129">`dotnet ef database update` を実行して、新しい移行をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-129">Run `dotnet ef database update` to apply the new migration to the database.</span></span> <span data-ttu-id="85cfe-130">このコマンドは、移行を適用する前に、データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-130">This command creates the database before applying migrations.</span></span>

<span data-ttu-id="85cfe-131">*blogging.db*\* SQLite DB はプロジェクト ディレクトリにあります。</span><span class="sxs-lookup"><span data-stu-id="85cfe-131">The *blogging.db*\* SQLite DB is in the project directory.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="85cfe-132">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="85cfe-132">Use the model</span></span>

* <span data-ttu-id="85cfe-133">*Program.cs* を開き、内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="85cfe-133">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* <span data-ttu-id="85cfe-134">コンソールからアプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="85cfe-134">Test the app from the console.</span></span> <span data-ttu-id="85cfe-135">Visual Studio からアプリを実行するには、[Visual Studio のメモ](#vs)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="85cfe-135">See the [Visual Studio note](#vs) to run the app from Visual Studio.</span></span>

  `dotnet run`

  <span data-ttu-id="85cfe-136">1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="85cfe-136">One blog is saved to the database and the details of all blogs are displayed in the console.</span></span>

  ```Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a><span data-ttu-id="85cfe-137">モデルを変更する</span><span class="sxs-lookup"><span data-stu-id="85cfe-137">Changing the model:</span></span>

- <span data-ttu-id="85cfe-138">モデルに変更を加える場合は、`dotnet ef migrations add` コマンドを使用して、新しい[移行](xref:core/managing-schemas/migrations/index)をスキャフォールディングすることができます。</span><span class="sxs-lookup"><span data-stu-id="85cfe-138">If you make changes to the model, you can use the `dotnet ef migrations add` command to scaffold a new [migration](xref:core/managing-schemas/migrations/index).</span></span> <span data-ttu-id="85cfe-139">スキャフォールディング コードの確認 (および必要な変更) を行ったら、`dotnet ef database update` コマンドを使用してデータベースにスキーマの変更を適用できます。</span><span class="sxs-lookup"><span data-stu-id="85cfe-139">Once you have checked the scaffolded code (and made any required changes), you can use the `dotnet ef database update` command to apply the schema changes to the database.</span></span>
- <span data-ttu-id="85cfe-140">EF Core はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-140">EF Core uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>
- <span data-ttu-id="85cfe-141">SQLite データベース エンジンでは、他のほとんどのリレーショナル データベースでサポートされているスキーマ変更のうち特定のものがサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="85cfe-141">The SQLite database engine doesn't support certain schema changes that are supported by most other relational databases.</span></span> <span data-ttu-id="85cfe-142">たとえば、`DropColumn` 操作はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="85cfe-142">For example, the `DropColumn` operation is not supported.</span></span> <span data-ttu-id="85cfe-143">EF Core の移行では、そのような操作のためのコードが作成されます。</span><span class="sxs-lookup"><span data-stu-id="85cfe-143">EF Core Migrations will generate code for these operations.</span></span> <span data-ttu-id="85cfe-144">しかし、それらをデータベースに適用しようとしたりスクリプトの作成を試みたりすると、EF Core によって例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="85cfe-144">But if you try to apply them to a database or generate a script, EF Core throws exceptions.</span></span> <span data-ttu-id="85cfe-145">[SQLite の制限](../../providers/sqlite/limitations.md)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="85cfe-145">See [SQLite Limitations](../../providers/sqlite/limitations.md).</span></span> <span data-ttu-id="85cfe-146">新しい開発の場合は、モデルを変更するときの移行を使用するのではなく、データベースを削除して新たに作成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="85cfe-146">For new development, consider dropping the database and creating a new one rather than using migrations when the model changes.</span></span>

<a name="vs"></a>
### <a name="run-from-visual-studio"></a><span data-ttu-id="85cfe-147">Visual Studio から実行する</span><span class="sxs-lookup"><span data-stu-id="85cfe-147">Run from Visual Studio</span></span>

<span data-ttu-id="85cfe-148">Visual Studio からこのサンプルを実行するには、作業ディレクトリをプロジェクトのルートに設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85cfe-148">To run this sample from Visual Studio, you must set the working directory manually to be the root of the project.</span></span> <span data-ttu-id="85cfe-149">作業ディレクトリを設定しない場合、`SQLite Error 1: 'no such table: Blogs'` の `Microsoft.Data.Sqlite.SqliteException` がスローされます。</span><span class="sxs-lookup"><span data-stu-id="85cfe-149">If  you don't set the working directory, the following `Microsoft.Data.Sqlite.SqliteException` is thrown: `SQLite Error 1: 'no such table: Blogs'`.</span></span>

<span data-ttu-id="85cfe-150">作業ディレクトリを設定するには:</span><span class="sxs-lookup"><span data-stu-id="85cfe-150">To set the working directory:</span></span>

* <span data-ttu-id="85cfe-151">**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-151">In **Solution Explorer**, right click the project and then select **Properties**.</span></span>
* <span data-ttu-id="85cfe-152">左ウィンドウの **[デバッグ]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-152">Select the **Debug** tab in the left pane.</span></span>
* <span data-ttu-id="85cfe-153">**作業ディレクトリ**をプロジェクトのディレクトリに設定します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-153">Set **Working directory** to the project directory.</span></span>
* <span data-ttu-id="85cfe-154">変更を保存します。</span><span class="sxs-lookup"><span data-stu-id="85cfe-154">Save the changes.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="85cfe-155">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="85cfe-155">Additional Resources</span></span>

* [<span data-ttu-id="85cfe-156">チュートリアル: SQLite を使用した新しいデータベースでの ASP.NET Core での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="85cfe-156">Tutorial: Get started with EF Core on ASP.NET Core with a new database using SQLite</span></span>](xref:core/get-started/aspnetcore/new-db)
* [<span data-ttu-id="85cfe-157">チュートリアル: ASP.NET Core の Razor Pages の概要</span><span class="sxs-lookup"><span data-stu-id="85cfe-157">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="85cfe-158">チュートリアル: ASP.NET Core での Entity Framework Core を使用した Razor Pages</span><span class="sxs-lookup"><span data-stu-id="85cfe-158">Tutorial: Razor Pages with Entity Framework Core in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
