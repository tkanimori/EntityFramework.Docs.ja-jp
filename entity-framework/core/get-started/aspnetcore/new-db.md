---
title: ASP.NET Core - 新しいデータベース - EF Core の概要
author: rick-anderson
ms.author: riande
ms.date: 08/03/2018
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 4734586adc89e9c1d866a1b4accd8b5e51fe2bb0
ms.sourcegitcommit: ebf661025d2ad2b62466fa7bf0e0772a7811cbe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2019
ms.locfileid: "54211167"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a><span data-ttu-id="d7b92-102">新しいデータベースを使用した ASP.NET Core での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="d7b92-102">Getting Started with EF Core on ASP.NET Core with a New database</span></span>

<span data-ttu-id="d7b92-103">このチュートリアルでは、Entity Framework Core を使用して、基本的なデータ アクセスを実行する ASP.NET Core MVC アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-103">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="d7b92-104">このチュートリアルでは、移行を使用してデータ モデルからデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-104">The tutorial uses migrations to create the database from the data model.</span></span>

<span data-ttu-id="d7b92-105">このチュートリアルは、Windows 上の Visual Studio 2017 または Windows、macOS または Linux. 上の .NET Core CLI で利用できます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-105">You can follow the tutorial by using Visual Studio 2017 on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="d7b92-106">この記事のサンプルは GitHub で確認してください。</span><span class="sxs-lookup"><span data-stu-id="d7b92-106">View this article's sample on GitHub:</span></span>
* [<span data-ttu-id="d7b92-107">Visual Studio 2017 と SQL Server</span><span class="sxs-lookup"><span data-stu-id="d7b92-107">Visual Studio 2017 with SQL Server</span></span>](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)
* <span data-ttu-id="d7b92-108">[.NET Core CLI と SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite).</span><span class="sxs-lookup"><span data-stu-id="d7b92-108">[.NET Core CLI with SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d7b92-109">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d7b92-109">Prerequisites</span></span>

<span data-ttu-id="d7b92-110">以下のソフトウェアをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d7b92-110">Install the following software:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7b92-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7b92-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d7b92-112">以下のワークロードで [Visual Studio 2017 バージョン 15.7 以降](https://www.visualstudio.com/downloads/):</span><span class="sxs-lookup"><span data-stu-id="d7b92-112">[Visual Studio 2017 version 15.7 or later](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="d7b92-113">**ASP.NET と Web 開発** (**[Web & Cloud]\(Web とクラウド\)** の下)</span><span class="sxs-lookup"><span data-stu-id="d7b92-113">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="d7b92-114">**.NET Core クロスプラットフォームの開発** (**[他のツールセット]** の下)</span><span class="sxs-lookup"><span data-stu-id="d7b92-114">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="d7b92-115">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="d7b92-115">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d7b92-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d7b92-116">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="d7b92-117">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="d7b92-117">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="d7b92-118">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="d7b92-118">Create a new project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7b92-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7b92-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d7b92-120">Visual Studio 2017 を開きます</span><span class="sxs-lookup"><span data-stu-id="d7b92-120">Open Visual Studio 2017</span></span>
* <span data-ttu-id="d7b92-121">**[ファイル] > [新規] > [プロジェクト]**</span><span class="sxs-lookup"><span data-stu-id="d7b92-121">**File > New > Project**</span></span>
* <span data-ttu-id="d7b92-122">左側のメニューから **[インストール済み] > [Visual C#] > [.NET Core]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-122">From the left menu, select **Installed > Visual C# > .NET Core**.</span></span>
* <span data-ttu-id="d7b92-123">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-123">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="d7b92-124">名前に「**EFGetStarted.AspNetCore.NewDb**」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7b92-124">Enter **EFGetStarted.AspNetCore.NewDb** for the name and click **OK**.</span></span>
* <span data-ttu-id="d7b92-125">**[新しい ASP.NET Core Web アプリケーション]** ダイアログで次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-125">In the **New ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="d7b92-126">ドロップダウン リストで、**[.NET Core]** と **[ASP.NET Core 2.1]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-126">Make sure that **.NET Core** and **ASP.NET Core 2.1** are selected in the drop-down lists</span></span>
  * <span data-ttu-id="d7b92-127">**[Web アプリケーション (モデル ビュー コントローラー)]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-127">Select the **Web Application (Model-View-Controller)** project template</span></span>
  * <span data-ttu-id="d7b92-128">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-128">Make sure that **Authentication** is set to **No Authentication**</span></span>
  * <span data-ttu-id="d7b92-129">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7b92-129">Click **OK**</span></span>

<span data-ttu-id="d7b92-130">警告:**[認証]** で **[なし]** ではなく **[個別のユーザー アカウント]** を使用すると、プロジェクトの `Models\IdentityModel.cs` に Entity Framework Core モデルが追加されます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-130">Warning: If you use **Individual User Accounts** instead of **None** for **Authentication** then an Entity Framework Core model will be added to the project in `Models\IdentityModel.cs`.</span></span> <span data-ttu-id="d7b92-131">このチュートリアルで学習する手法を使用すると、エンティティ クラスを格納するのに、この既存のモデルに 2 つ目のモデルを追加するか、またはこの既存のクラスを拡張するかを選択できます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-131">Using the techniques you learn in this tutorial, you can choose to add a second model, or extend this existing model to contain your entity classes.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d7b92-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d7b92-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="d7b92-133">MVC プロジェクトを作成するために、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-133">Run the following command to create an MVC project:</span></span>

   ```cli
   dotnet new mvc -n EFGetStarted.AspNetCore.NewDb
   ```
* <span data-ttu-id="d7b92-134">プロジェクト ディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-134">Change to the project directory.</span></span> <span data-ttu-id="d7b92-135">入力した次のコマンドは、新しいプロジェクトに対して実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7b92-135">The next commands you enter need to run against the new project.</span></span>

   ```cli
   cd EFGetStarted.AspNetCore.NewDb
   ```
---

## <a name="install-entity-framework-core"></a><span data-ttu-id="d7b92-136">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="d7b92-136">Install Entity Framework Core</span></span>

<span data-ttu-id="d7b92-137">EF Core をインストールするには、対象となる EF Core データベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d7b92-137">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="d7b92-138">使用可能なプロバイダーの一覧については、「[Database Providers](../../providers/index.md)」 (データベース プロバイダー) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d7b92-138">For a list of available providers, see [Database Providers](../../providers/index.md).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7b92-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7b92-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d7b92-140">このチュートリアルでは、SQL Server を使用するため、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d7b92-140">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="d7b92-141">この SQL Server のプロバイダー パッケージは、[Microsoft.AspnetCore.App メタパッケージ](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-141">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d7b92-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d7b92-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d7b92-143">このチュートリアルでは、.NET Core がサポートしているすべてのプラットフォームで実行できるため、SQLite を使用しています。</span><span class="sxs-lookup"><span data-stu-id="d7b92-143">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span>

* <span data-ttu-id="d7b92-144">次のコマンドを実行して、SQLite プロバイダーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d7b92-144">Run the following command to install the SQLite provider:</span></span>

   ```cli
   dotnet add package Microsoft.EntityFrameworkCore.Sqlite
   ```

---

## <a name="create-the-model"></a><span data-ttu-id="d7b92-145">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="d7b92-145">Create the model</span></span>

<span data-ttu-id="d7b92-146">モデルを構成するコンテキスト クラスおよびエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-146">Define a context class and entity classes that make up the model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7b92-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7b92-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d7b92-148">**Models** フォルダーを右クリックし、**[追加] > [クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-148">Right-click on the **Models** folder and select **Add > Class**.</span></span>
* <span data-ttu-id="d7b92-149">名前に「**Model.cs**」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7b92-149">Enter **Model.cs** as the name and click **OK**.</span></span>
* <span data-ttu-id="d7b92-150">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-150">Replace the contents of the file with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d7b92-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d7b92-151">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="d7b92-152">以下のコードを使用して、**Models** フォルダーで、**Model.cs** を作成します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-152">In the **Models** folder create **Model.cs** with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Models/Model.cs)]

---

<span data-ttu-id="d7b92-153">実稼働アプリでは、通常別のファイルに各クラスが記述されます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-153">A production app would typically put each class in a separate file.</span></span> <span data-ttu-id="d7b92-154">わかりやすくするために、このチュートリアルではすべてのクラスを 1 つのファイルに記述しています。</span><span class="sxs-lookup"><span data-stu-id="d7b92-154">For the sake of simplicity, this tutorial puts these classes in one file.</span></span>

## <a name="register-the-context-with-dependency-injection"></a><span data-ttu-id="d7b92-155">依存関係挿入にコンテキストを登録する</span><span class="sxs-lookup"><span data-stu-id="d7b92-155">Register the context with dependency injection</span></span>

<span data-ttu-id="d7b92-156">サービス (`BloggingContext` など) は、アプリケーションの起動時に[依存関係の挿入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)に登録されます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-156">Services (such as `BloggingContext`) are registered with [dependency injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) during application startup.</span></span> <span data-ttu-id="d7b92-157">これらのサービス (MVC コントローラーなど) を必要とするコンポーネントには、コンストラクターのパラメーターやプロパティを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-157">Components that require these services (such as MVC controllers) are provided these services via constructor parameters or properties.</span></span>

<span data-ttu-id="d7b92-158">`BloggingContext` を MVC コント ローラーで使用できるようにするには、サービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-158">To make `BloggingContext` available to MVC controllers, register it as a service.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7b92-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7b92-159">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d7b92-160">**Startup.cs** に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-160">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

* <span data-ttu-id="d7b92-161">次の強調表示されたコードが `ConfigureServices` メソッドに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-161">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=12-14)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d7b92-162">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d7b92-162">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="d7b92-163">**Startup.cs** に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-163">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs#AddedUsings)]

* <span data-ttu-id="d7b92-164">次の強調表示されたコードが `ConfigureServices` メソッドに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-164">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs?name=ConfigureServices&highlight=12-14)]

---

<span data-ttu-id="d7b92-165">実際のアプリでは、接続文字列は構成ファイルまたは環境変数に記述するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="d7b92-165">A production app would typically put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="d7b92-166">わかりやすくするために、このチュートリアルではコード内に定義します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-166">For the sake of simplicity, this tutorial defines it in code.</span></span> <span data-ttu-id="d7b92-167">詳細については、「[接続文字列](../../miscellaneous/connection-strings.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d7b92-167">See [Connection Strings](../../miscellaneous/connection-strings.md) for more information.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="d7b92-168">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="d7b92-168">Create the database</span></span>

<span data-ttu-id="d7b92-169">次の手順では、[移行](xref:core/managing-schemas/migrations/index)を使用し、データベースを作成しています。</span><span class="sxs-lookup"><span data-stu-id="d7b92-169">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7b92-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7b92-170">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d7b92-171">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="d7b92-171">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="d7b92-172">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-172">Run the following commands:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="d7b92-173">`The term 'add-migration' is not recognized as the name of a cmdlet` という内容のエラーが表示された場合は、Visual Studio を閉じて再度開きます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-173">If you get an error stating `The term 'add-migration' is not recognized as the name of a cmdlet`, close and reopen Visual Studio.</span></span>

  <span data-ttu-id="d7b92-174">この `Add-Migration` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-174">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="d7b92-175">`Update-Database` コマンドではデータベースが作成され、それに新しい移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-175">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d7b92-176">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d7b92-176">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="d7b92-177">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-177">Run the following commands:</span></span>

  ```cli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="d7b92-178">この `migrations` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-178">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="d7b92-179">`database update` コマンドではデータベースが作成され、それに新しい移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-179">The `database update` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-a-controller"></a><span data-ttu-id="d7b92-180">コントローラーを作成する</span><span class="sxs-lookup"><span data-stu-id="d7b92-180">Create a controller</span></span>

<span data-ttu-id="d7b92-181">`Blog` エンティティのコントローラーとビューをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="d7b92-181">Scaffold a controller and views for the `Blog` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7b92-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7b92-182">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d7b92-183">**ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加] > [コントローラー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-183">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="d7b92-184">**[Entity Framework を使用したビューがある MVC コントローラー]** を選択し、**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7b92-184">Select **MVC Controller with views, using Entity Framework** and click **Add**.</span></span>
* <span data-ttu-id="d7b92-185">**[モデル クラス]** に **Blog** を、**[データ コンテキスト クラス]** に **BloggingContext** を設定します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-185">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**.</span></span>
* <span data-ttu-id="d7b92-186">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7b92-186">Click **Add**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d7b92-187">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d7b92-187">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="d7b92-188">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-188">Run the following commands:</span></span>

  ```cli
  dotnet tool install -g dotnet-aspnet-codegenerator
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet restore
  dotnet aspnet-codegenerator controller -name BlogsController -m Blog -dc BloggingContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  <span data-ttu-id="d7b92-189">`tool install` コマンドと `add package` コマンドでは、コントローラーとビューをスキャフォールディングするツールがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-189">The `tool install` and `add package` commands install the tooling that can scaffold controllers and views.</span></span> <span data-ttu-id="d7b92-190">`restore` コマンドでは、すべてのプロジェクトのパッケージがダウンロードされることを保証し、`aspnet-codegenerator` コマンドによってスキャフォールディングが実行されます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-190">The `restore` command ensures that all of the project's packages are downloaded, and the `aspnet-codegenerator` command does the scaffolding.</span></span>
---

<span data-ttu-id="d7b92-191">スキャフォールディング エンジンによって次のファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="d7b92-191">The scaffolding engine creates the following files:</span></span>

* <span data-ttu-id="d7b92-192">コントローラー (*Controllers/BlogsController.cs*)</span><span class="sxs-lookup"><span data-stu-id="d7b92-192">A controller (*Controllers/BlogsController.cs*)</span></span>
* <span data-ttu-id="d7b92-193">作成、削除、詳細、編集、およびインデックス ページ用の Razor ビュー (_Views/Blogs/\*.cshtml_)</span><span class="sxs-lookup"><span data-stu-id="d7b92-193">Razor views for Create, Delete, Details, Edit, and Index pages (_Views/Blogs/\*.cshtml_)</span></span>

## <a name="run-the-application"></a><span data-ttu-id="d7b92-194">アプリケーションの実行</span><span class="sxs-lookup"><span data-stu-id="d7b92-194">Run the application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d7b92-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7b92-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d7b92-196">**[デバッグ]** > **[デバッグなしで開始]**</span><span class="sxs-lookup"><span data-stu-id="d7b92-196">**Debug** > **Start Without Debugging**</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d7b92-197">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d7b92-197">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet run
```
---

* <span data-ttu-id="d7b92-198">`/Blogs` に移動します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-198">Navigate to `/Blogs`</span></span>

* <span data-ttu-id="d7b92-199">**[新規作成]** リンクを使用して、いくつかのブログ エントリを作成します。</span><span class="sxs-lookup"><span data-stu-id="d7b92-199">Use the **Create New** link to create some blog entries.</span></span>

  ![[作成] ページ](_static/create.png)

* <span data-ttu-id="d7b92-201">**[詳細]**、**[編集]** および **[削除]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="d7b92-201">Test the **Details**, **Edit**, and **Delete** links.</span></span>

  ![インデックス ページ](_static/index-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="d7b92-203">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="d7b92-203">Additional Resources</span></span>

* [<span data-ttu-id="d7b92-204">チュートリアル: SQLite を使用した新しいデータベースでの .NET Core での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="d7b92-204">Tutorial: Get started with EF Core on .NET Core with a new database using SQLite</span></span>](xref:core/get-started/netcore/new-db-sqlite)
* [<span data-ttu-id="d7b92-205">チュートリアル: ASP.NET Core の Razor ページの概要</span><span class="sxs-lookup"><span data-stu-id="d7b92-205">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="d7b92-206">チュートリアル: ASP.NET Core での Entity Framework Core を使用した Razor ページ</span><span class="sxs-lookup"><span data-stu-id="d7b92-206">Tutorial: Razor Pages with Entity Framework Core in ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro)
