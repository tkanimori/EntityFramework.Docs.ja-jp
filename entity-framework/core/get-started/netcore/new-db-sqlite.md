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
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a><span data-ttu-id="84001-104">新しいデータベースを使用した .NET Core コンソール アプリでの EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="84001-104">Getting Started with EF Core on .NET Core Console App with a New database</span></span>

<span data-ttu-id="84001-105">このチュートリアルでは、Entity Framework Core を使用して SQLite データベースにデータ アクセスを実行する .NET Core コンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="84001-105">In this walkthrough, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span> <span data-ttu-id="84001-106">モデルからの移行によってデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="84001-106">You use migrations to create the database from the model.</span></span> <span data-ttu-id="84001-107">ASP.NET Core MVC を使用する Visual Studio バージョンについては [ASP.NET Core - 新しいデータベース](xref:core/get-started/aspnetcore/new-db)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="84001-107">See [ASP.NET Core - New database](xref:core/get-started/aspnetcore/new-db) for a Visual Studio version using ASP.NET Core MVC.</span></span>

> [!TIP]  
> <span data-ttu-id="84001-108">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="84001-108">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84001-109">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="84001-109">Prerequisites</span></span>

<span data-ttu-id="84001-110">[.NET Core SDK](https://www.microsoft.com/net/core) 2.1</span><span class="sxs-lookup"><span data-stu-id="84001-110">[The .NET Core SDK](https://www.microsoft.com/net/core) 2.1</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="84001-111">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="84001-111">Create a new project</span></span>

* <span data-ttu-id="84001-112">新しいコンソール プロジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="84001-112">Create a new console project:</span></span>

``` Console
dotnet new console -o ConsoleApp.SQLite
cd ConsoleApp.SQLite/
```

## <a name="install-entity-framework-core"></a><span data-ttu-id="84001-113">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="84001-113">Install Entity Framework Core</span></span>

<span data-ttu-id="84001-114">EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="84001-114">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="84001-115">このチュートリアルでは SQLite を使用します。</span><span class="sxs-lookup"><span data-stu-id="84001-115">This walkthrough uses SQLite.</span></span> <span data-ttu-id="84001-116">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="84001-116">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="84001-117">Microsoft.EntityFrameworkCore.Sqlite と Microsoft.EntityFrameworkCore.Design をインストールします。</span><span class="sxs-lookup"><span data-stu-id="84001-117">Install Microsoft.EntityFrameworkCore.Sqlite and Microsoft.EntityFrameworkCore.Design</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
```

* <span data-ttu-id="84001-118">`dotnet restore` を実行して新しいパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="84001-118">Run `dotnet restore` to install the new packages.</span></span>

## <a name="create-the-model"></a><span data-ttu-id="84001-119">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="84001-119">Create the model</span></span>

<span data-ttu-id="84001-120">モデルを構成するコンテキストとエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="84001-120">Define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="84001-121">次の内容が含まれる新しい *Model.cs* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="84001-121">Create a new *Model.cs* file with the following contents.</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

<span data-ttu-id="84001-122">ヒント: 実際のアプリケーションでは、各クラスは別のファイルに、接続文字列は構成ファイルにそれぞれ記述します。</span><span class="sxs-lookup"><span data-stu-id="84001-122">Tip: In a real application, you put each class in a separate file and put the connection string in a configuration file.</span></span> <span data-ttu-id="84001-123">チュートリアルをわかりやすくするために、すべてを 1 つのファイルに記述しています。</span><span class="sxs-lookup"><span data-stu-id="84001-123">To keep the tutorial simple, everything is contained in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="84001-124">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="84001-124">Create the database</span></span>

<span data-ttu-id="84001-125">モデルを作成したら、[移行](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)を利用してデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="84001-125">Once you have a model, you use [migrations](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) to create a database.</span></span>

* <span data-ttu-id="84001-126">`dotnet ef migrations add InitialCreate` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。</span><span class="sxs-lookup"><span data-stu-id="84001-126">Run `dotnet ef migrations add InitialCreate` to scaffold a migration and create the initial set of tables for the model.</span></span>
* <span data-ttu-id="84001-127">`dotnet ef database update` を実行して、新しい移行をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="84001-127">Run `dotnet ef database update` to apply the new migration to the database.</span></span> <span data-ttu-id="84001-128">このコマンドは、移行を適用する前に、データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="84001-128">This command creates the database before applying migrations.</span></span>

<span data-ttu-id="84001-129">*blogging.db*\* SQLite DB はプロジェクト ディレクトリにあります。</span><span class="sxs-lookup"><span data-stu-id="84001-129">The *blogging.db*\* SQLite DB is in the project directory.</span></span>

## <a name="use-your-model"></a><span data-ttu-id="84001-130">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="84001-130">Use your model</span></span>

* <span data-ttu-id="84001-131">*Program.cs* を開き、内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="84001-131">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* <span data-ttu-id="84001-132">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="84001-132">Test the app:</span></span>

  `dotnet run`

  <span data-ttu-id="84001-133">1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="84001-133">One blog is saved to the database and the details of all blogs are displayed in the console.</span></span>

  ``` Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a><span data-ttu-id="84001-134">モデルを変更する</span><span class="sxs-lookup"><span data-stu-id="84001-134">Changing the model:</span></span>

- <span data-ttu-id="84001-135">モデルを変更する場合、`dotnet ef migrations add` コマンドを使用して新しい[移行](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)をスキャフォールディングし、対応するスキーマの変更をデータベースに対して行います。</span><span class="sxs-lookup"><span data-stu-id="84001-135">If you make changes to your model, you can use the `dotnet ef migrations add` command to scaffold a new [migration](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)  to make the corresponding schema changes to the database.</span></span> <span data-ttu-id="84001-136">スキャフォールディング コードを確認 (および必要な変更) したら、`dotnet ef database update` コマンドを使用してデータベースに変更を適用できます。</span><span class="sxs-lookup"><span data-stu-id="84001-136">Once you have checked the scaffolded code (and made any required changes), you can use the `dotnet ef database update` command to apply the changes to the database.</span></span>
- <span data-ttu-id="84001-137">EF はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="84001-137">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>
- <span data-ttu-id="84001-138">SQLite には制限があるため、SQLite はすべての移行 (スキーマ変更) をサポートするわけではありません。</span><span class="sxs-lookup"><span data-stu-id="84001-138">SQLite does not support all migrations (schema changes) due to limitations in SQLite.</span></span> <span data-ttu-id="84001-139">[SQLite の制限](../../providers/sqlite/limitations.md)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="84001-139">See [SQLite Limitations](../../providers/sqlite/limitations.md).</span></span> <span data-ttu-id="84001-140">新しい開発の場合、モデルを変更したときは移行するのではなく、データベースを削除して新たに作成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="84001-140">For new development, consider dropping the database and creating a new one rather than using migrations when your model changes.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84001-141">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="84001-141">Additional Resources</span></span>

* <span data-ttu-id="84001-142">[.NET core - SQLite を使用した新しいデータベース](xref:core/get-started/netcore/new-db-sqlite) - クロスプラットフォーム コンソールでの EF のチュートリアル。</span><span class="sxs-lookup"><span data-stu-id="84001-142">[.NET Core - New database with SQLite](xref:core/get-started/netcore/new-db-sqlite) -  a cross-platform console EF tutorial.</span></span>
* [<span data-ttu-id="84001-143">Mac または Linux での ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="84001-143">Introduction to ASP.NET Core MVC on Mac or Linux</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [<span data-ttu-id="84001-144">Visual Studio を使用した ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="84001-144">Introduction to ASP.NET Core MVC with Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [<span data-ttu-id="84001-145">Visual Studio を使用した ASP.NET Core と Entity Framework Core の概要</span><span class="sxs-lookup"><span data-stu-id="84001-145">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
