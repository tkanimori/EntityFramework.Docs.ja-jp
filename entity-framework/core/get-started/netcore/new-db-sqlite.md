---
title: ".NET Core - 新しいデータベース - EF Core の概要"
author: rick-anderson
ms.author: riande
ms.author2: tdykstra
description: "Entity Framework Core を使用した .NET Core の概要"
keywords: ".NET Core, Entity Framework Core, VS コード, Visual Studio コード, Mac, Linux"
ms.date: 04/05/2017
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
ms.technology: entity-framework-core
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: 3becf75e7a513a3aa18c3c2daf628b65327365b0
ms.sourcegitcommit: 0858f157b806f4a881b94ddbeecf1ece1d53e1e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a><span data-ttu-id="4d98a-104">新しいデータベースを使用した .NET Core コンソール アプリでの EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="4d98a-104">Getting Started with EF Core on .NET Core Console App with a New database</span></span>

<span data-ttu-id="4d98a-105">このチュートリアルでは、Entity Framework Core を使用して SQLite データベースに基本的なデータ アクセスを実行する .NET Core コンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="4d98a-105">In this walkthrough, you will create a .NET Core console app that performs basic data access against a SQLite database using Entity Framework Core.</span></span> <span data-ttu-id="4d98a-106">モデルからの移行によってデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="4d98a-106">You will use migrations to create the database from your model.</span></span> <span data-ttu-id="4d98a-107">ASP.NET Core MVC を使用する Visual Studio バージョンについては [ASP.NET Core - 新しいデータベース](xref:core/get-started/aspnetcore/new-db)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4d98a-107">See [ASP.NET Core - New database](xref:core/get-started/aspnetcore/new-db) for a Visual Studio version using ASP.NET Core MVC.</span></span>

> [!NOTE]  
> <span data-ttu-id="4d98a-108">[.NET Core SDK](https://www.microsoft.com/net/download/core) では、`project.json` と Visual Studio 2015 はサポートされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="4d98a-108">The [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or Visual Studio 2015.</span></span> <span data-ttu-id="4d98a-109">[project.json から csproj への移行](https://docs.microsoft.com/dotnet/articles/core/migration/)をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4d98a-109">We recommend you [migrate from project.json to csproj](https://docs.microsoft.com/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="4d98a-110">Visual Studio を使用している場合は、[Visual Studio 2017](https://www.visualstudio.com/downloads/) に移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4d98a-110">If you are using Visual Studio, we recommend you migrate to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

> [!TIP]  
> <span data-ttu-id="4d98a-111">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="4d98a-111">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4d98a-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="4d98a-112">Prerequisites</span></span>

<span data-ttu-id="4d98a-113">このチュートリアルを完了するには、次の条件を満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="4d98a-113">The following prerequisites are needed to complete this walkthrough:</span></span>
* <span data-ttu-id="4d98a-114">.NET Core をサポートするオペレーティング システム。</span><span class="sxs-lookup"><span data-stu-id="4d98a-114">An operating system that supports .NET Core.</span></span>
* <span data-ttu-id="4d98a-115">[.NET Core SDK](https://www.microsoft.com/net/core) 2.0 (ただし、以前のバージョンの手順にわずかな変更を加えることで、アプリケーションを作成できます)。</span><span class="sxs-lookup"><span data-stu-id="4d98a-115">[The .NET Core SDK](https://www.microsoft.com/net/core) 2.0 (although the instructions can be used to create an application with a previous version with very few modifications).</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="4d98a-116">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="4d98a-116">Create a new project</span></span>

* <span data-ttu-id="4d98a-117">プロジェクト用の新しい `ConsoleApp.SQLite` フォルダーを作成します。次に、`dotnet` コマンドを使用して、作成したフォルダーに .NET Core アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="4d98a-117">Create a new `ConsoleApp.SQLite` folder for your project and use the `dotnet` command to populate it with a .NET Core app.</span></span>

``` Console
mkdir ConsoleApp.SQLite
cd ConsoleApp.SQLite/
dotnet new console
```

## <a name="install-entity-framework-core"></a><span data-ttu-id="4d98a-118">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="4d98a-118">Install Entity Framework Core</span></span>

<span data-ttu-id="4d98a-119">EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="4d98a-119">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="4d98a-120">このチュートリアルでは SQLite を使用します。</span><span class="sxs-lookup"><span data-stu-id="4d98a-120">This walkthrough uses SQLite.</span></span> <span data-ttu-id="4d98a-121">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4d98a-121">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="4d98a-122">Microsoft.EntityFrameworkCore.Sqlite と Microsoft.EntityFrameworkCore.Design をインストールします。</span><span class="sxs-lookup"><span data-stu-id="4d98a-122">Install Microsoft.EntityFrameworkCore.Sqlite and Microsoft.EntityFrameworkCore.Design</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
```

* <span data-ttu-id="4d98a-123">手動で `ConsoleApp.SQLite.csproj` を編集し、Microsoft.EntityFrameworkCore.Tools.DotNet に DotNetCliToolReference を追加します。</span><span class="sxs-lookup"><span data-stu-id="4d98a-123">Manually edit `ConsoleApp.SQLite.csproj` to add a DotNetCliToolReference to Microsoft.EntityFrameworkCore.Tools.DotNet:</span></span>

  ``` xml
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
  </ItemGroup>
  ```

<span data-ttu-id="4d98a-124">`ConsoleApp.SQLite.csproj` は次のようになっているはずです。</span><span class="sxs-lookup"><span data-stu-id="4d98a-124">`ConsoleApp.SQLite.csproj` should now contain the following:</span></span>

[!code[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/ConsoleApp.SQLite.csproj)]

 <span data-ttu-id="4d98a-125">注: 上記で使用したバージョン番号は、公開された時点のものです。</span><span class="sxs-lookup"><span data-stu-id="4d98a-125">Note: The version numbers used above were correct at the time of publishing.</span></span>

*  <span data-ttu-id="4d98a-126">`dotnet restore` を実行して新しいパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="4d98a-126">Run `dotnet restore` to install the new packages.</span></span>

## <a name="create-the-model"></a><span data-ttu-id="4d98a-127">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="4d98a-127">Create the model</span></span>

<span data-ttu-id="4d98a-128">モデルを構成するコンテキストとエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="4d98a-128">Define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="4d98a-129">次の内容が含まれる新しい *Model.cs* ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="4d98a-129">Create a new *Model.cs* file with the following contents.</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

<span data-ttu-id="4d98a-130">ヒント: 実際のアプリケーションでは、各クラスは別のファイルに、接続文字列は構成ファイルにそれぞれ記述するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="4d98a-130">Tip: In a real application you would put each class in a separate file and put the connection string in a configuration file.</span></span> <span data-ttu-id="4d98a-131">チュートリアルをわかりやすくするために、すべてを 1 つのファイルに記述しています。</span><span class="sxs-lookup"><span data-stu-id="4d98a-131">To keep the tutorial simple, we are putting everything in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="4d98a-132">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="4d98a-132">Create the database</span></span>

<span data-ttu-id="4d98a-133">モデルを作成したら、[移行](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)を利用してデータベースを作成できます。</span><span class="sxs-lookup"><span data-stu-id="4d98a-133">Once you have a model, you can use [migrations](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) to create a database.</span></span>

* <span data-ttu-id="4d98a-134">`dotnet ef migrations add InitialCreate` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。</span><span class="sxs-lookup"><span data-stu-id="4d98a-134">Run `dotnet ef migrations add InitialCreate` to scaffold a migration and create the initial set of tables for the model.</span></span>
* <span data-ttu-id="4d98a-135">`dotnet ef database update` を実行して、新しい移行をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="4d98a-135">Run `dotnet ef database update` to apply the new migration to the database.</span></span> <span data-ttu-id="4d98a-136">このコマンドは、移行を適用する前に、データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="4d98a-136">This command creates the database before applying migrations.</span></span>

> [!NOTE]  
> <span data-ttu-id="4d98a-137">相対パスを SQLite で使用すると、パスがアプリケーションのメイン アセンブリに対して相対的になります。</span><span class="sxs-lookup"><span data-stu-id="4d98a-137">When using relative paths with SQLite, the path will be relative to the application's main assembly.</span></span> <span data-ttu-id="4d98a-138">このサンプルでは、メイン バイナリは `bin/Debug/netcoreapp2.0/ConsoleApp.SQLite.dll` であるため、SQLite データベースは `bin/Debug/netcoreapp2.0/blogging.db` に配置されます。</span><span class="sxs-lookup"><span data-stu-id="4d98a-138">In this sample, the main binary is `bin/Debug/netcoreapp2.0/ConsoleApp.SQLite.dll`, so the SQLite database will be in `bin/Debug/netcoreapp2.0/blogging.db`.</span></span>

## <a name="use-your-model"></a><span data-ttu-id="4d98a-139">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="4d98a-139">Use your model</span></span>

* <span data-ttu-id="4d98a-140">*Program.cs* を開き、内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="4d98a-140">Open *Program.cs* and replace the contents with the following code:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* <span data-ttu-id="4d98a-141">アプリをテストします。</span><span class="sxs-lookup"><span data-stu-id="4d98a-141">Test the app:</span></span>

 `dotnet run`

 <span data-ttu-id="4d98a-142">1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに表示されます。</span><span class="sxs-lookup"><span data-stu-id="4d98a-142">One blog is saved to the database and the details of all blogs are displayed in the console.</span></span>

  ``` Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a><span data-ttu-id="4d98a-143">モデルを変更する</span><span class="sxs-lookup"><span data-stu-id="4d98a-143">Changing the model:</span></span>

- <span data-ttu-id="4d98a-144">モデルを変更する場合、`dotnet ef migrations add` コマンドを使用して新しい[移行](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)をスキャフォールディングし、対応するスキーマの変更をデータベースに対して行います。</span><span class="sxs-lookup"><span data-stu-id="4d98a-144">If you make changes to your model, you can use the `dotnet ef migrations add` command to scaffold a new [migration](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)  to make the corresponding schema changes to the database.</span></span> <span data-ttu-id="4d98a-145">スキャフォールディング コードを確認 (および必要な変更) したら、`dotnet ef database update` コマンドを使用してデータベースに変更を適用できます。</span><span class="sxs-lookup"><span data-stu-id="4d98a-145">Once you have checked the scaffolded code (and made any required changes), you can use the `dotnet ef database update` command to apply the changes to the database.</span></span>
- <span data-ttu-id="4d98a-146">EF はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="4d98a-146">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>
- <span data-ttu-id="4d98a-147">SQLite には制限があるため、SQLite はすべての移行 (スキーマ変更) をサポートするわけではありません。</span><span class="sxs-lookup"><span data-stu-id="4d98a-147">SQLite does not support all migrations (schema changes) due to limitations in SQLite.</span></span> <span data-ttu-id="4d98a-148">[SQLite の制限](../../providers/sqlite/limitations.md)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="4d98a-148">See [SQLite Limitations](../../providers/sqlite/limitations.md).</span></span> <span data-ttu-id="4d98a-149">新しい開発の場合、モデルを変更したときは移行するのではなく、データベースを削除して新たに作成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="4d98a-149">For new development, consider dropping the database and creating a new one rather than using migrations when your model changes.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d98a-150">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="4d98a-150">Additional Resources</span></span>

* <span data-ttu-id="4d98a-151">[.NET core - SQLite を使用した新しいデータベース](xref:core/get-started/netcore/new-db-sqlite) - クロスプラットフォーム コンソールでの EF のチュートリアル。</span><span class="sxs-lookup"><span data-stu-id="4d98a-151">[.NET Core - New database with SQLite](xref:core/get-started/netcore/new-db-sqlite) -  a cross-platform console EF tutorial.</span></span>
* [<span data-ttu-id="4d98a-152">Mac または Linux での ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="4d98a-152">Introduction to ASP.NET Core MVC on Mac or Linux</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [<span data-ttu-id="4d98a-153">Visual Studio を使用した ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="4d98a-153">Introduction to ASP.NET Core MVC with Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [<span data-ttu-id="4d98a-154">Visual Studio を使用した ASP.NET Core と Entity Framework Core の概要</span><span class="sxs-lookup"><span data-stu-id="4d98a-154">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
