---
title: ASP.NET Core - 新しいデータベース - EF Core の概要
author: rick-anderson
ms.author: riande
ms.author2: tdykstra
ms.date: 04/07/2017
ms.topic: get-started-article
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
ms.technology: entity-framework-core
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 80477ca57b8b3df6de8ba3595c9056c6b8412040
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2018
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a><span data-ttu-id="2deb1-102">新しいデータベースを使用した ASP.NET Core での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="2deb1-102">Getting Started with EF Core on ASP.NET Core with a New database</span></span>

<span data-ttu-id="2deb1-103">このチュートリアルでは、Entity Framework Core を使用して、基本的なデータ アクセスを実行する ASP.NET Core MVC アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-103">In this walkthrough, you will build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="2deb1-104">EF Core モデルからの移行によってデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-104">You will use migrations to create the database from your EF Core model.</span></span> <span data-ttu-id="2deb1-105">Entity Framework Core のチュートリアルについては「[その他のリソース](#additional-resources)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2deb1-105">See [Additional Resources](#additional-resources) for more Entity Framework Core tutorials.</span></span>

<span data-ttu-id="2deb1-106">このチュートリアルでは、次のものが必要になります。</span><span class="sxs-lookup"><span data-stu-id="2deb1-106">This tutorial requires:</span></span>
* <span data-ttu-id="2deb1-107">[Visual Studio 2017 15.3](https://www.visualstudio.com/downloads/) および以下のワークロード。</span><span class="sxs-lookup"><span data-stu-id="2deb1-107">[Visual Studio 2017 15.3](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="2deb1-108">**ASP.NET と Web 開発** (**[Web & Cloud]\(Web とクラウド\)** の下)</span><span class="sxs-lookup"><span data-stu-id="2deb1-108">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="2deb1-109">**.NET Core クロスプラットフォームの開発** (**[他のツールセット]** の下)</span><span class="sxs-lookup"><span data-stu-id="2deb1-109">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="2deb1-110">[.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="2deb1-110">[.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core).</span></span>

> [!TIP]  
> <span data-ttu-id="2deb1-111">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="2deb1-111">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb) on GitHub.</span></span>

## <a name="create-a-new-project-in-visual-studio-2017"></a><span data-ttu-id="2deb1-112">Visual Studio 2017 で新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="2deb1-112">Create a new project in Visual Studio 2017</span></span>

* <span data-ttu-id="2deb1-113">**[ファイル] > [新規] > [プロジェクト]**</span><span class="sxs-lookup"><span data-stu-id="2deb1-113">**File > New > Project**</span></span>
* <span data-ttu-id="2deb1-114">左側のメニューから **[インストール済み] > [テンプレート] > [Visual C#] > [.NET Core]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-114">From the left menu select **Installed > Templates > Visual C# > .NET Core**.</span></span>
* <span data-ttu-id="2deb1-115">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-115">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="2deb1-116">名前に「**EFGetStarted.AspNetCore.NewDb**」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2deb1-116">Enter **EFGetStarted.AspNetCore.NewDb** for the name and click **OK**.</span></span>
* <span data-ttu-id="2deb1-117">**[新しい ASP.NET Core Web アプリケーション]** ダイアログで次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-117">In the **New ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="2deb1-118">ドロップダウン リストで、**[.NET Core]** と **[ASP.NET Core 2.0]** のオプションが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-118">Ensure the options **.NET Core** and **ASP.NET Core 2.0** are selected in the drop down lists</span></span>
  * <span data-ttu-id="2deb1-119">**[Web アプリケーション (モデル ビュー コントローラー)]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-119">Select the **Web Application (Model-View-Controller)** project template</span></span>
  * <span data-ttu-id="2deb1-120">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-120">Ensure that **Authentication** is set to **No Authentication**</span></span>
  * <span data-ttu-id="2deb1-121">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2deb1-121">Click **OK**</span></span>

<span data-ttu-id="2deb1-122">注意: **[認証]** で **[なし]** ではなく **[個別のユーザー アカウント]** を使用すると、プロジェクトの `Models\IdentityModel.cs` に EntityFramework Core モデルが追加されます。</span><span class="sxs-lookup"><span data-stu-id="2deb1-122">Warning: If you use **Individual User Accounts** instead of **None** for **Authentication** then an Entity Framework Core model will be added to your project in `Models\IdentityModel.cs`.</span></span> <span data-ttu-id="2deb1-123">このチュートリアルで学習する手法を使用すると、エンティティ クラスを格納するのに、この既存のモデルに 2 つ目のモデルを追加するか、またはエンティティ クラスを拡張するかを選択できます。</span><span class="sxs-lookup"><span data-stu-id="2deb1-123">Using the techniques you will learn in this walkthrough, you can choose to add a second model, or extend this existing model to contain your entity classes.</span></span>

## <a name="install-entity-framework-core"></a><span data-ttu-id="2deb1-124">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="2deb1-124">Install Entity Framework Core</span></span>

<span data-ttu-id="2deb1-125">対象となる EF Core データベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2deb1-125">Install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="2deb1-126">このチュートリアルでは SQL Server を使用します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-126">This walkthrough uses SQL Server.</span></span> <span data-ttu-id="2deb1-127">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2deb1-127">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="2deb1-128">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="2deb1-128">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="2deb1-129">`Install-Package Microsoft.EntityFrameworkCore.SqlServer` を実行します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-129">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="2deb1-130">いくつかの Entity Framework Core Tools を使用して、EF Core モデルからデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-130">We will be using some Entity Framework Core Tools to create a database from your EF Core model.</span></span> <span data-ttu-id="2deb1-131">そのため、ツールのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2deb1-131">So we will install the tools package as well:</span></span>

* <span data-ttu-id="2deb1-132">`Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-132">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

<span data-ttu-id="2deb1-133">後で、いくつかの ASP.NET Core スキャフォールディング ツールを使用して、コント ローラーとビューを作成します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-133">We will be using some ASP.NET Core Scaffolding tools to create controllers and views later on.</span></span> <span data-ttu-id="2deb1-134">そのため、このデザイン パッケージもインストールします。</span><span class="sxs-lookup"><span data-stu-id="2deb1-134">So we will install this design package as well:</span></span>

* <span data-ttu-id="2deb1-135">`Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design` を実行します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-135">Run `Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design`</span></span>

## <a name="create-the-model"></a><span data-ttu-id="2deb1-136">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="2deb1-136">Create the model</span></span>

<span data-ttu-id="2deb1-137">モデルを構成するコンテキストおよびエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-137">Define a context and entity classes that make up the model:</span></span>

* <span data-ttu-id="2deb1-138">**Models** フォルダーを右クリックし、**[追加] > [クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-138">Right-click on the **Models** folder and select **Add > Class**.</span></span>
* <span data-ttu-id="2deb1-139">名前に「**Model.cs**」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2deb1-139">Enter **Model.cs** as the name and click **OK**.</span></span>
* <span data-ttu-id="2deb1-140">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2deb1-140">Replace the contents of the file with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

<span data-ttu-id="2deb1-141">注: 実際のアプリでは、モデルの各クラスは別のファイルに記述するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="2deb1-141">Note: In a real app you would typically put each class from your model in a separate file.</span></span> <span data-ttu-id="2deb1-142">わかりやすくするために、このチュートリアルではすべてのクラスを 1 つのファイルに記述しています。</span><span class="sxs-lookup"><span data-stu-id="2deb1-142">For the sake of simplicity, we are putting all the classes in one file for this tutorial.</span></span>

## <a name="register-your-context-with-dependency-injection"></a><span data-ttu-id="2deb1-143">依存関係の挿入にコンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-143">Register your context with dependency injection</span></span>

<span data-ttu-id="2deb1-144">サービス (`BloggingContext` など) は、アプリケーションの起動時に[依存関係の挿入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)に登録されます。</span><span class="sxs-lookup"><span data-stu-id="2deb1-144">Services (such as `BloggingContext`) are registered with [dependency injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) during application startup.</span></span> <span data-ttu-id="2deb1-145">これらのサービス (MVC コント ローラーなど) を必要とするコンポーネントには、コンス トラクターのパラメーターまたはプロパティを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="2deb1-145">Components that require these services (such as your MVC controllers) are then provided these services via constructor parameters or properties.</span></span>

<span data-ttu-id="2deb1-146">MVC コントローラーで利用するために、`BloggingContext` をサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-146">In order for our MVC controllers to make use of `BloggingContext` we will register it as a service.</span></span>

* <span data-ttu-id="2deb1-147">**Startup.cs** を開きます。</span><span class="sxs-lookup"><span data-stu-id="2deb1-147">Open **Startup.cs**</span></span>
* <span data-ttu-id="2deb1-148">次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-148">Add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

<span data-ttu-id="2deb1-149">`AddDbContext` メソッドを追加して、それをサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-149">Add the `AddDbContext` method to register it as a service:</span></span>

* <span data-ttu-id="2deb1-150">`ConfigureServices` メソッドに次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-150">Add the following code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=7-8)]

<span data-ttu-id="2deb1-151">注: 実際のアプリでは、接続文字列は構成ファイルに記述するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="2deb1-151">Note: A real app would generally put the connection string in a configuration file.</span></span> <span data-ttu-id="2deb1-152">わかりやすくするために、接続文字列はコード内に定義しています。</span><span class="sxs-lookup"><span data-stu-id="2deb1-152">For the sake of simplicity, we are defining it in code.</span></span> <span data-ttu-id="2deb1-153">詳細については、「[接続文字列](../../miscellaneous/connection-strings.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2deb1-153">See [Connection Strings](../../miscellaneous/connection-strings.md) for more information.</span></span>

## <a name="create-your-database"></a><span data-ttu-id="2deb1-154">データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="2deb1-154">Create your database</span></span>

<span data-ttu-id="2deb1-155">モデルを作成したら、[移行](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)を利用してデータベースを作成できます。</span><span class="sxs-lookup"><span data-stu-id="2deb1-155">Once you have a model, you can use [migrations](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) to create a database.</span></span>

* <span data-ttu-id="2deb1-156">PMC を開きます。</span><span class="sxs-lookup"><span data-stu-id="2deb1-156">Open the PMC:</span></span>

  <span data-ttu-id="2deb1-157">**[ツール] –> [NuGet パッケージ マネージャー] –> [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="2deb1-157">**Tools –> NuGet Package Manager –> Package Manager Console**</span></span>
* <span data-ttu-id="2deb1-158">`Add-Migration InitialCreate` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-158">Run `Add-Migration InitialCreate` to scaffold a migration to create the initial set of tables for your model.</span></span> <span data-ttu-id="2deb1-159">`The term 'add-migration' is not recognized as the name of a cmdlet` という内容のエラーが表示された場合は、Visual Studio を閉じて再度開きます。</span><span class="sxs-lookup"><span data-stu-id="2deb1-159">If you receive an error stating `The term 'add-migration' is not recognized as the name of a cmdlet`, close and reopen Visual Studio.</span></span>
* <span data-ttu-id="2deb1-160">`Update-Database` を実行して、新しい移行をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-160">Run `Update-Database` to apply the new migration to the database.</span></span> <span data-ttu-id="2deb1-161">このコマンドは、移行を適用する前に、データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-161">This command creates the database before applying migrations.</span></span>

## <a name="create-a-controller"></a><span data-ttu-id="2deb1-162">コントローラーを作成する</span><span class="sxs-lookup"><span data-stu-id="2deb1-162">Create a controller</span></span>

<span data-ttu-id="2deb1-163">プロジェクトでスキャフォールディングを有効にします。</span><span class="sxs-lookup"><span data-stu-id="2deb1-163">Enable scaffolding in the project:</span></span>

* <span data-ttu-id="2deb1-164">**ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加] > [コントローラー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-164">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="2deb1-165">**[最小の依存関係]** を選択し、**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2deb1-165">Select **Minimal Dependencies** and click **Add**.</span></span>
* <span data-ttu-id="2deb1-166">*ScaffoldingReadMe.txt* ファイルは無視しても、削除してもかまいません。</span><span class="sxs-lookup"><span data-stu-id="2deb1-166">You can ignore or delete the *ScaffoldingReadMe.txt* file.</span></span>

<span data-ttu-id="2deb1-167">スキャフォールディングが有効になったら、`Blog` エンティティのコントローラーをスキャフォールディングできます。</span><span class="sxs-lookup"><span data-stu-id="2deb1-167">Now that scaffolding is enabled, we can scaffold a controller for the `Blog` entity.</span></span>

* <span data-ttu-id="2deb1-168">**ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加] > [コントローラー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-168">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="2deb1-169">**[Entity Framework を使用したビューがある MVC コントローラー]** を選択し、**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2deb1-169">Select **MVC Controller with views, using Entity Framework** and click **Ok**.</span></span>
* <span data-ttu-id="2deb1-170">**[モデル クラス]** に **Blog** を、**[データ コンテキスト クラス]** に **BloggingContext** を設定します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-170">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**.</span></span>
* <span data-ttu-id="2deb1-171">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2deb1-171">Click **Add**.</span></span>


## <a name="run-the-application"></a><span data-ttu-id="2deb1-172">アプリケーションの実行</span><span class="sxs-lookup"><span data-stu-id="2deb1-172">Run the application</span></span>

<span data-ttu-id="2deb1-173">F5 キーを押してアプリを実行し、テストします。</span><span class="sxs-lookup"><span data-stu-id="2deb1-173">Press F5 to run and test the app.</span></span>

* <span data-ttu-id="2deb1-174">`/Blogs` に移動します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-174">Navigate to `/Blogs`</span></span>
* <span data-ttu-id="2deb1-175">[作成] リンクを使用して、いくつかのブログ エントリを作成します。</span><span class="sxs-lookup"><span data-stu-id="2deb1-175">Use the create link to create some blog entries.</span></span> <span data-ttu-id="2deb1-176">[詳細] リンクと [削除] リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="2deb1-176">Test the details and delete links.</span></span>

![イメージ](_static/create.png)

![イメージ](_static/index-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="2deb1-179">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2deb1-179">Additional Resources</span></span>

* <span data-ttu-id="2deb1-180">[EF - SQLite を使用した新しいデータベース](xref:core/get-started/netcore/new-db-sqlite) - クロスプラットフォーム コンソールでの EF のチュートリアル。</span><span class="sxs-lookup"><span data-stu-id="2deb1-180">[EF - New database with SQLite](xref:core/get-started/netcore/new-db-sqlite) -  a cross-platform console EF tutorial.</span></span>
* [<span data-ttu-id="2deb1-181">Mac または Linux での ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="2deb1-181">Introduction to ASP.NET Core MVC on Mac or Linux</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [<span data-ttu-id="2deb1-182">Visual Studio を使用した ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="2deb1-182">Introduction to ASP.NET Core MVC with Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [<span data-ttu-id="2deb1-183">Visual Studio を使用した ASP.NET Core と Entity Framework Core の概要</span><span class="sxs-lookup"><span data-stu-id="2deb1-183">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
