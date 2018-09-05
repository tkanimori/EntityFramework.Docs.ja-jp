---
title: .NET Core - 新しいデータベース - EF Core の概要
author: rick-anderson
ms.author: riande
description: Entity Framework Core を使用した .NET Core の概要
ms.date: 08/03/2018
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: 51f5752eebce5603c663072f7b36dfecd4ddf227
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993693"
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a><span data-ttu-id="73df6-103">新しいデータベースを使用した .NET Core コンソール アプリでの EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="73df6-103">Getting Started with EF Core on .NET Core Console App with a New database</span></span>

<span data-ttu-id="73df6-104">このチュートリアルでは、Entity Framework Core を使用して SQLite データベースに対してデータ アクセスを実行する .NET Core コンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="73df6-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span> <span data-ttu-id="73df6-105">モデルからの移行によってデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="73df6-105">You use migrations to create the database from the model.</span></span> <span data-ttu-id="73df6-106">ASP.NET Core MVC を使用する Visual Studio バージョンについては [ASP.NET Core - 新しいデータベース](xref:core/get-started/aspnetcore/new-db)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="73df6-106">See [ASP.NET Core - New database](xref:core/get-started/aspnetcore/new-db) for a Visual Studio version using ASP.NET Core MVC.</span></span>

<span data-ttu-id="73df6-107">この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite)。</span><span class="sxs-lookup"><span data-stu-id="73df6-107">View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="73df6-108">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="73df6-108">Prerequisites</span></span>

* [<span data-ttu-id="73df6-109">.NET Core 2.1 SDK</span><span class="sxs-lookup"><span data-stu-id="73df6-109">The .NET Core 2.1 SDK</span></span>](https://www.microsoft.com/net/core)

## <a name="create-a-new-project"></a><span data-ttu-id="73df6-110">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="73df6-110">Create a new project</span></span>

* <span data-ttu-id="73df6-111">新しいコンソール プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="73df6-111">Create a new console project:</span></span>

  ``` Console
  dotnet new console -o ConsoleApp.SQLite
  cd ConsoleApp.SQLite/
  ```

## <a name="install-entity-framework-core"></a><span data-ttu-id="73df6-112">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="73df6-112">Install Entity Framework Core</span></span>

<span data-ttu-id="73df6-113">EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="73df6-113">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="73df6-114">このチュートリアルでは SQLite を使用します。</span><span class="sxs-lookup"><span data-stu-id="73df6-114">This walkthrough uses SQLite.</span></span> <span data-ttu-id="73df6-115">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="73df6-115">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="73df6-116">Microsoft.EntityFrameworkCore.Sqlite と Microsoft.EntityFrameworkCore.Design をインストールします。</span><span class="sxs-lookup"><span data-stu-id="73df6-116">Install Microsoft.EntityFrameworkCore.Sqlite and Microsoft.EntityFrameworkCore.Design</span></span>

  ```Console
  dotnet add package Microsoft.EntityFrameworkCore.Sqlite
  dotnet add package Microsoft.EntityFrameworkCore.Design
  ```

* <span data-ttu-id="73df6-117">`dotnet restore` を実行して新しいパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="73df6-117">Run `dotnet restore` to install the new packages.</span></span>

## <a name="create-the-model"></a><span data-ttu-id="73df6-118">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="73df6-118">Create the model</span></span>

<span data-ttu-id="73df6-119">モデルを構成するコンテキストとエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="73df6-119">Define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="73df6-120">次の内容が含まれる新しい *Model.cs* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="73df6-120">Create a new *Model.cs* file with the following contents.</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

<span data-ttu-id="73df6-121">ヒント: 実際のアプリケーションでは、クラスはそれぞれ別々のファイルに記述し、接続文字列は構成ファイルまたは環境変数に記述します。</span><span class="sxs-lookup"><span data-stu-id="73df6-121">Tip: In a real application, you put each class in a separate file and put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="73df6-122">チュートリアルをわかりやすくするために、すべてを 1 つのファイルに記述しています。</span><span class="sxs-lookup"><span data-stu-id="73df6-122">To keep the tutorial simple, everything is contained in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="73df6-123">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="73df6-123">Create the database</span></span>

<span data-ttu-id="73df6-124">モデルを作成したら、[移行](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)を利用してデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="73df6-124">Once you have a model, you use [migrations](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) to create a database.</span></span>

* <span data-ttu-id="73df6-125">`dotnet ef migrations add InitialCreate` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。</span><span class="sxs-lookup"><span data-stu-id="73df6-125">Run `dotnet ef migrations add InitialCreate` to scaffold a migration and create the initial set of tables for the model.</span></span>
* <span data-ttu-id="73df6-126">`dotnet ef database update` を実行して、新しい移行をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="73df6-126">Run `dotnet ef database update` to apply the new migration to the database.</span></span> <span data-ttu-id="73df6-127">このコマンドは、移行を適用する前に、データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="73df6-127">This command creates the database before applying migrations.</span></span>

<span data-ttu-id="73df6-128">*blogging.db*\* SQLite DB はプロジェクト ディレクトリにあります。</span><span class="sxs-lookup"><span data-stu-id="73df6-128">The *blogging.db*\* SQLite DB is in the project directory.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="73df6-129">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="73df6-129">Use the model</span></span>

* <span data-ttu-id="73df6-130">*Program.cs* を開き、内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="73df6-130">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* <span data-ttu-id="73df6-131">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="73df6-131">Test the app:</span></span>

  `dotnet run`

  <span data-ttu-id="73df6-132">1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="73df6-132">One blog is saved to the database and the details of all blogs are displayed in the console.</span></span>

  ```Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a><span data-ttu-id="73df6-133">モデルを変更する</span><span class="sxs-lookup"><span data-stu-id="73df6-133">Changing the model:</span></span>

- <span data-ttu-id="73df6-134">モデルに変更を加える場合は、`dotnet ef migrations add` コマンドを使用して、新しい[移行](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)をスキャフォールディングすることができます。</span><span class="sxs-lookup"><span data-stu-id="73df6-134">If you make changes to the model, you can use the `dotnet ef migrations add` command to scaffold a new [migration](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations).</span></span> <span data-ttu-id="73df6-135">スキャフォールディング コードの確認 (および必要な変更) を行ったら、`dotnet ef database update` コマンドを使用してデータベースにスキーマの変更を適用できます。</span><span class="sxs-lookup"><span data-stu-id="73df6-135">Once you have checked the scaffolded code (and made any required changes), you can use the `dotnet ef database update` command to apply the schema changes to the database.</span></span>
- <span data-ttu-id="73df6-136">EF Core はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="73df6-136">EF Core uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>
- <span data-ttu-id="73df6-137">SQLite データベース エンジンでは、他のほとんどのリレーショナル データベースでサポートされているスキーマ変更のうち特定のものがサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="73df6-137">The SQLite database engine doesn't support certain schema changes that are supported by most other relational databases.</span></span> <span data-ttu-id="73df6-138">たとえば、`DropColumn` 操作はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="73df6-138">For example, the `DropColumn` operation is not supported.</span></span> <span data-ttu-id="73df6-139">EF Core の移行では、そのような操作のためのコードが作成されます。</span><span class="sxs-lookup"><span data-stu-id="73df6-139">EF Core Migrations will generate code for these operations.</span></span> <span data-ttu-id="73df6-140">しかし、それらをデータベースに適用しようとしたりスクリプトの作成を試みたりすると、EF Core によって例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="73df6-140">But if you try to apply them to a database or generate a script, EF Core throws exceptions.</span></span> <span data-ttu-id="73df6-141">[SQLite の制限](../../providers/sqlite/limitations.md)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="73df6-141">See [SQLite Limitations](../../providers/sqlite/limitations.md).</span></span> <span data-ttu-id="73df6-142">新しい開発の場合は、モデルを変更するときの移行を使用するのではなく、データベースを削除して新たに作成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="73df6-142">For new development, consider dropping the database and creating a new one rather than using migrations when the model changes.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73df6-143">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="73df6-143">Additional Resources</span></span>

* [<span data-ttu-id="73df6-144">Mac または Linux での ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="73df6-144">Introduction to ASP.NET Core MVC on Mac or Linux</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [<span data-ttu-id="73df6-145">Visual Studio を使用した ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="73df6-145">Introduction to ASP.NET Core MVC with Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [<span data-ttu-id="73df6-146">Visual Studio を使用した ASP.NET Core と Entity Framework Core の概要</span><span class="sxs-lookup"><span data-stu-id="73df6-146">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
