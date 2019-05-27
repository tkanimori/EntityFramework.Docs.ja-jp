---
title: ASP.NET Core - 新しいデータベース - EF Core の概要
author: rick-anderson
ms.author: riande
ms.date: 08/03/2018
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: fbc1a00d6d6d0624bcbbfa1e51f4e21a915baaaa
ms.sourcegitcommit: f277883a5ed28eba57d14aaaf17405bc1ae9cf94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/18/2019
ms.locfileid: "65874574"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a><span data-ttu-id="05b0b-102">新しいデータベースを使用した ASP.NET Core での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="05b0b-102">Getting Started with EF Core on ASP.NET Core with a New database</span></span>

<span data-ttu-id="05b0b-103">このチュートリアルでは、Entity Framework Core を使用して、基本的なデータ アクセスを実行する ASP.NET Core MVC アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-103">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="05b0b-104">このチュートリアルでは、移行を使用してデータ モデルからデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-104">The tutorial uses migrations to create the database from the data model.</span></span>

<span data-ttu-id="05b0b-105">このチュートリアルは、Windows 上の Visual Studio 2017 または Windows、macOS または Linux. 上の .NET Core CLI で利用できます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-105">You can follow the tutorial by using Visual Studio 2017 on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="05b0b-106">この記事のサンプルは GitHub で確認してください。</span><span class="sxs-lookup"><span data-stu-id="05b0b-106">View this article's sample on GitHub:</span></span>
* [<span data-ttu-id="05b0b-107">Visual Studio 2017 と SQL Server</span><span class="sxs-lookup"><span data-stu-id="05b0b-107">Visual Studio 2017 with SQL Server</span></span>](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)
* <span data-ttu-id="05b0b-108">[.NET Core CLI と SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite).</span><span class="sxs-lookup"><span data-stu-id="05b0b-108">[.NET Core CLI with SQLite](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="05b0b-109">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="05b0b-109">Prerequisites</span></span>

<span data-ttu-id="05b0b-110">以下のソフトウェアをインストールします。</span><span class="sxs-lookup"><span data-stu-id="05b0b-110">Install the following software:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="05b0b-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05b0b-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05b0b-112">以下のワークロードで [Visual Studio 2017 バージョン 15.7 以降](https://www.visualstudio.com/downloads/):</span><span class="sxs-lookup"><span data-stu-id="05b0b-112">[Visual Studio 2017 version 15.7 or later](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="05b0b-113">**ASP.NET と Web 開発** ( **[Web & Cloud]\(Web とクラウド\)** の下)</span><span class="sxs-lookup"><span data-stu-id="05b0b-113">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="05b0b-114">**.NET Core クロスプラットフォームの開発** ( **[他のツールセット]** の下)</span><span class="sxs-lookup"><span data-stu-id="05b0b-114">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="05b0b-115">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="05b0b-115">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="05b0b-116">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="05b0b-116">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="05b0b-117">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="05b0b-117">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="05b0b-118">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="05b0b-118">Create a new project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="05b0b-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05b0b-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05b0b-120">Visual Studio 2017 を開きます</span><span class="sxs-lookup"><span data-stu-id="05b0b-120">Open Visual Studio 2017</span></span>
* <span data-ttu-id="05b0b-121">**[ファイル] > [新規] > [プロジェクト]**</span><span class="sxs-lookup"><span data-stu-id="05b0b-121">**File > New > Project**</span></span>
* <span data-ttu-id="05b0b-122">左側のメニューから **[インストール済み] > [Visual C#] > [.NET Core]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-122">From the left menu, select **Installed > Visual C# > .NET Core**.</span></span>
* <span data-ttu-id="05b0b-123">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-123">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="05b0b-124">名前に「**EFGetStarted.AspNetCore.NewDb**」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="05b0b-124">Enter **EFGetStarted.AspNetCore.NewDb** for the name and click **OK**.</span></span>
* <span data-ttu-id="05b0b-125">**[新しい ASP.NET Core Web アプリケーション]** ダイアログで次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-125">In the **New ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="05b0b-126">ドロップダウン リストで、 **[.NET Core]** と **[ASP.NET Core 2.1]** が選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-126">Make sure that **.NET Core** and **ASP.NET Core 2.1** are selected in the drop-down lists</span></span>
  * <span data-ttu-id="05b0b-127">**[Web アプリケーション (モデル ビュー コントローラー)]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-127">Select the **Web Application (Model-View-Controller)** project template</span></span>
  * <span data-ttu-id="05b0b-128">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-128">Make sure that **Authentication** is set to **No Authentication**</span></span>
  * <span data-ttu-id="05b0b-129">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="05b0b-129">Click **OK**</span></span>

<span data-ttu-id="05b0b-130">警告 : **[認証]** で **[なし]** ではなく **[個別のユーザー アカウント]** を使用すると、プロジェクトの `Models\IdentityModel.cs` に Entity Framework Core モデルが追加されます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-130">Warning: If you use **Individual User Accounts** instead of **None** for **Authentication** then an Entity Framework Core model will be added to the project in `Models\IdentityModel.cs`.</span></span> <span data-ttu-id="05b0b-131">このチュートリアルで学習する手法を使用すると、エンティティ クラスを格納するのに、この既存のモデルに 2 つ目のモデルを追加するか、またはこの既存のクラスを拡張するかを選択できます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-131">Using the techniques you learn in this tutorial, you can choose to add a second model, or extend this existing model to contain your entity classes.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="05b0b-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="05b0b-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="05b0b-133">MVC プロジェクトを作成するために、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-133">Run the following command to create an MVC project:</span></span>

   ```cli
   dotnet new mvc -n EFGetStarted.AspNetCore.NewDb
   ```
* <span data-ttu-id="05b0b-134">プロジェクト ディレクトリに移動します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-134">Change to the project directory.</span></span> <span data-ttu-id="05b0b-135">入力した次のコマンドは、新しいプロジェクトに対して実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="05b0b-135">The next commands you enter need to run against the new project.</span></span>

   ```cli
   cd EFGetStarted.AspNetCore.NewDb
   ```
---

## <a name="install-entity-framework-core"></a><span data-ttu-id="05b0b-136">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="05b0b-136">Install Entity Framework Core</span></span>

<span data-ttu-id="05b0b-137">EF Core をインストールするには、対象となる EF Core データベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="05b0b-137">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="05b0b-138">使用可能なプロバイダーの一覧については、「[Database Providers](../../providers/index.md)」 (データベース プロバイダー) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="05b0b-138">For a list of available providers, see [Database Providers](../../providers/index.md).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="05b0b-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05b0b-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="05b0b-140">このチュートリアルでは、SQL Server を使用するため、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="05b0b-140">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="05b0b-141">この SQL Server のプロバイダー パッケージは、[Microsoft.AspnetCore.App メタパッケージ](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-141">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="05b0b-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="05b0b-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="05b0b-143">このチュートリアルでは、.NET Core がサポートしているすべてのプラットフォームで実行できるため、SQLite を使用しています。</span><span class="sxs-lookup"><span data-stu-id="05b0b-143">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span>

* <span data-ttu-id="05b0b-144">次のコマンドを実行して、SQLite プロバイダーをインストールします。</span><span class="sxs-lookup"><span data-stu-id="05b0b-144">Run the following command to install the SQLite provider:</span></span>

   ```cli
   dotnet add package Microsoft.EntityFrameworkCore.Sqlite
   ```

---

## <a name="create-the-model"></a><span data-ttu-id="05b0b-145">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="05b0b-145">Create the model</span></span>

<span data-ttu-id="05b0b-146">モデルを構成するコンテキスト クラスおよびエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-146">Define a context class and entity classes that make up the model.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="05b0b-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05b0b-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05b0b-148">**Models** フォルダーを右クリックし、 **[追加] > [クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-148">Right-click on the **Models** folder and select **Add > Class**.</span></span>
* <span data-ttu-id="05b0b-149">名前に「**Model.cs**」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="05b0b-149">Enter **Model.cs** as the name and click **OK**.</span></span>
* <span data-ttu-id="05b0b-150">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-150">Replace the contents of the file with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="05b0b-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="05b0b-151">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="05b0b-152">以下のコードを使用して、**Models** フォルダーで、**Model.cs** を作成します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-152">In the **Models** folder create **Model.cs** with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Models/Model.cs)]

---

<span data-ttu-id="05b0b-153">実稼働アプリでは、通常別のファイルに各クラスが記述されます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-153">A production app would typically put each class in a separate file.</span></span> <span data-ttu-id="05b0b-154">わかりやすくするために、このチュートリアルではすべてのクラスを 1 つのファイルに記述しています。</span><span class="sxs-lookup"><span data-stu-id="05b0b-154">For the sake of simplicity, this tutorial puts these classes in one file.</span></span>

## <a name="register-the-context-with-dependency-injection"></a><span data-ttu-id="05b0b-155">依存関係挿入にコンテキストを登録する</span><span class="sxs-lookup"><span data-stu-id="05b0b-155">Register the context with dependency injection</span></span>

<span data-ttu-id="05b0b-156">`BloggingContext` を MVC コント ローラーで使用できるようにするには、`Startup.cs` にサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-156">To make `BloggingContext` available to MVC controllers, register it as a service in `Startup.cs`.</span></span>

<span data-ttu-id="05b0b-157">サービス (`BloggingContext` など) はアプリケーションの起動時に[依存関係の挿入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)によって登録され、コンストラクターのパラメーターやプロパティを使用してサービス (MVC コントローラーなど) を使用するコンポーネントに自動的に提供できるようになります。</span><span class="sxs-lookup"><span data-stu-id="05b0b-157">Services (such as `BloggingContext`) are registered with [dependency injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) during application startup so that they can be provided automatically to components that consume services (such as MVC controllers) via constructor parameters and properties.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="05b0b-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05b0b-158">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05b0b-159">**Startup.cs** に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-159">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

* <span data-ttu-id="05b0b-160">次の強調表示されたコードが `ConfigureServices` メソッドに追加されます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-160">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=12-14)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="05b0b-161">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="05b0b-161">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="05b0b-162">**Startup.cs** に次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-162">In **Startup.cs** add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs#AddedUsings)]

* <span data-ttu-id="05b0b-163">次の強調表示されたコードが `ConfigureServices` メソッドに追加されます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-163">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb.Sqlite/Startup.cs?name=ConfigureServices&highlight=12-14)]

---

<span data-ttu-id="05b0b-164">実際のアプリでは、接続文字列は構成ファイルまたは環境変数に記述するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="05b0b-164">A production app would typically put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="05b0b-165">わかりやすくするために、このチュートリアルではコード内に定義します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-165">For the sake of simplicity, this tutorial defines it in code.</span></span> <span data-ttu-id="05b0b-166">詳細については、「[接続文字列](../../miscellaneous/connection-strings.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="05b0b-166">See [Connection Strings](../../miscellaneous/connection-strings.md) for more information.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="05b0b-167">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="05b0b-167">Create the database</span></span>

<span data-ttu-id="05b0b-168">次の手順では、[移行](xref:core/managing-schemas/migrations/index)を使用し、データベースを作成しています。</span><span class="sxs-lookup"><span data-stu-id="05b0b-168">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="05b0b-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05b0b-169">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05b0b-170">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="05b0b-170">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="05b0b-171">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-171">Run the following commands:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="05b0b-172">`The term 'add-migration' is not recognized as the name of a cmdlet` という内容のエラーが表示された場合は、Visual Studio を閉じて再度開きます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-172">If you get an error stating `The term 'add-migration' is not recognized as the name of a cmdlet`, close and reopen Visual Studio.</span></span>

  <span data-ttu-id="05b0b-173">この `Add-Migration` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-173">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="05b0b-174">`Update-Database` コマンドではデータベースが作成され、それに新しい移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-174">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="05b0b-175">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="05b0b-175">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="05b0b-176">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-176">Run the following commands:</span></span>

  ```cli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="05b0b-177">この `migrations` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-177">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="05b0b-178">`database update` コマンドではデータベースが作成され、それに新しい移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-178">The `database update` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-a-controller"></a><span data-ttu-id="05b0b-179">コントローラーを作成する</span><span class="sxs-lookup"><span data-stu-id="05b0b-179">Create a controller</span></span>

<span data-ttu-id="05b0b-180">`Blog` エンティティのコントローラーとビューをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="05b0b-180">Scaffold a controller and views for the `Blog` entity.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="05b0b-181">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05b0b-181">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05b0b-182">**ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、 **[追加] > [コントローラー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-182">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="05b0b-183">**[Entity Framework を使用したビューがある MVC コントローラー]** を選択し、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="05b0b-183">Select **MVC Controller with views, using Entity Framework** and click **Add**.</span></span>
* <span data-ttu-id="05b0b-184">**[モデル クラス]** に **Blog** を、 **[データ コンテキスト クラス]** に **BloggingContext** を設定します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-184">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**.</span></span>
* <span data-ttu-id="05b0b-185">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="05b0b-185">Click **Add**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="05b0b-186">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="05b0b-186">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="05b0b-187">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-187">Run the following commands:</span></span>

  ```cli
  dotnet tool install -g dotnet-aspnet-codegenerator
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet restore
  dotnet aspnet-codegenerator controller -name BlogsController -m Blog -dc BloggingContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  <span data-ttu-id="05b0b-188">`tool install` コマンドと `add package` コマンドでは、コントローラーとビューをスキャフォールディングするツールがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-188">The `tool install` and `add package` commands install the tooling that can scaffold controllers and views.</span></span> <span data-ttu-id="05b0b-189">`restore` コマンドでは、すべてのプロジェクトのパッケージがダウンロードされることを保証し、`aspnet-codegenerator` コマンドによってスキャフォールディングが実行されます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-189">The `restore` command ensures that all of the project's packages are downloaded, and the `aspnet-codegenerator` command does the scaffolding.</span></span>
---

<span data-ttu-id="05b0b-190">スキャフォールディング エンジンによって次のファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="05b0b-190">The scaffolding engine creates the following files:</span></span>

* <span data-ttu-id="05b0b-191">コントローラー (*Controllers/BlogsController.cs*)</span><span class="sxs-lookup"><span data-stu-id="05b0b-191">A controller (*Controllers/BlogsController.cs*)</span></span>
* <span data-ttu-id="05b0b-192">作成、削除、詳細、編集、およびインデックス ページ用の Razor ビュー (_Views/Blogs/\*.cshtml_)</span><span class="sxs-lookup"><span data-stu-id="05b0b-192">Razor views for Create, Delete, Details, Edit, and Index pages (_Views/Blogs/\*.cshtml_)</span></span>

## <a name="run-the-application"></a><span data-ttu-id="05b0b-193">アプリケーションの実行</span><span class="sxs-lookup"><span data-stu-id="05b0b-193">Run the application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="05b0b-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="05b0b-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="05b0b-195">**[デバッグ]**  >  **[デバッグなしで開始]**</span><span class="sxs-lookup"><span data-stu-id="05b0b-195">**Debug** > **Start Without Debugging**</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="05b0b-196">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="05b0b-196">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet run
```
---

* <span data-ttu-id="05b0b-197">`/Blogs` に移動します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-197">Navigate to `/Blogs`</span></span>

* <span data-ttu-id="05b0b-198">**[新規作成]** リンクを使用して、いくつかのブログ エントリを作成します。</span><span class="sxs-lookup"><span data-stu-id="05b0b-198">Use the **Create New** link to create some blog entries.</span></span>

  ![[作成] ページ](_static/create.png)

* <span data-ttu-id="05b0b-200">**[詳細]** 、 **[編集]** および **[削除]** リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="05b0b-200">Test the **Details**, **Edit**, and **Delete** links.</span></span>

  ![インデックス ページ](_static/index-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="05b0b-202">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="05b0b-202">Additional Resources</span></span>

* [<span data-ttu-id="05b0b-203">チュートリアル: SQLite を使用した新しいデータベースでの .NET Core での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="05b0b-203">Tutorial: Get started with EF Core on .NET Core with a new database using SQLite</span></span>](xref:core/get-started/netcore/new-db-sqlite)
* <span data-ttu-id="05b0b-204">[ASP.NET Core の Razor Pages の概要](/aspnet/core/tutorials/razor-pages/razor-pages-start)または [ASP.NET Core MVC の概要](/aspnet/core/tutorials/first-mvc-app/start-mvc)</span><span class="sxs-lookup"><span data-stu-id="05b0b-204">[Get started with Razor Pages in ASP.NET Core](/aspnet/core/tutorials/razor-pages/razor-pages-start) or [Get started with ASP.NET Core MVC ](/aspnet/core/tutorials/first-mvc-app/start-mvc)</span></span>
* <span data-ttu-id="05b0b-205">[チュートリアル: ASP.NET Core での Entity Framework Core を使用した Razor Pages](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro) または [ASP.NET MVC Web アプリでの EF Core の概要](/aspnet/core/data/ef-mvc/intro)</span><span class="sxs-lookup"><span data-stu-id="05b0b-205">[Tutorial: Razor Pages with Entity Framework Core in ASP.NET Core](https://docs.microsoft.com/aspnet/core/data/ef-rp/intro) or [Get started with EF Core in an ASP.NET MVC web app](/aspnet/core/data/ef-mvc/intro)</span></span>
