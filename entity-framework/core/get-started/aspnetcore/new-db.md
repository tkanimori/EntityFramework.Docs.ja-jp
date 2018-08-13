---
title: ASP.NET Core - 新しいデータベース - EF Core の概要
author: rick-anderson
ms.author: riande
ms.author2: tdykstra
ms.date: 08/03/2018
ms.topic: get-started-article
ms.assetid: e153627f-f132-4c11-b13c-6c9a607addce
ms.technology: entity-framework-core
uid: core/get-started/aspnetcore/new-db
ms.openlocfilehash: 9e86bc9cff028ad9791f23cbb45f0a93110c0064
ms.sourcegitcommit: 902257be9c63c427dc793750a2b827d6feb8e38c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39614351"
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-a-new-database"></a><span data-ttu-id="722b6-102">新しいデータベースを使用した ASP.NET Core での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="722b6-102">Getting Started with EF Core on ASP.NET Core with a New database</span></span>

<span data-ttu-id="722b6-103">このチュートリアルでは、Entity Framework Core を使用して、基本的なデータ アクセスを実行する ASP.NET Core MVC アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="722b6-103">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="722b6-104">EF Core モデルからの移行によってデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="722b6-104">You use migrations to create the database from your EF Core model.</span></span>

<span data-ttu-id="722b6-105">[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb)。</span><span class="sxs-lookup"><span data-stu-id="722b6-105">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="722b6-106">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="722b6-106">Prerequisites</span></span>

<span data-ttu-id="722b6-107">以下のソフトウェアをインストールします。</span><span class="sxs-lookup"><span data-stu-id="722b6-107">Install the following software:</span></span>

* <span data-ttu-id="722b6-108">[Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) および以下のワークロード。</span><span class="sxs-lookup"><span data-stu-id="722b6-108">[Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="722b6-109">**ASP.NET と Web 開発** (**[Web & Cloud]\(Web とクラウド\)** の下)</span><span class="sxs-lookup"><span data-stu-id="722b6-109">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="722b6-110">**.NET Core クロスプラットフォームの開発** (**[他のツールセット]** の下)</span><span class="sxs-lookup"><span data-stu-id="722b6-110">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="722b6-111">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="722b6-111">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

## <a name="create-a-new-project-in-visual-studio-2017"></a><span data-ttu-id="722b6-112">Visual Studio 2017 で新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="722b6-112">Create a new project in Visual Studio 2017</span></span>

* <span data-ttu-id="722b6-113">Visual Studio 2017 を開きます</span><span class="sxs-lookup"><span data-stu-id="722b6-113">Open Visual Studio 2017</span></span>
* <span data-ttu-id="722b6-114">**[ファイル] > [新規] > [プロジェクト]**</span><span class="sxs-lookup"><span data-stu-id="722b6-114">**File > New > Project**</span></span>
* <span data-ttu-id="722b6-115">左側のメニューから **[インストール済み] > [Visual C#] > [.NET Core]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="722b6-115">From the left menu select **Installed > Visual C# > .NET Core**.</span></span>
* <span data-ttu-id="722b6-116">**[ASP.NET Core Web アプリケーション]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="722b6-116">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="722b6-117">名前に「**EFGetStarted.AspNetCore.NewDb**」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="722b6-117">Enter **EFGetStarted.AspNetCore.NewDb** for the name and click **OK**.</span></span>
* <span data-ttu-id="722b6-118">**[新しい ASP.NET Core Web アプリケーション]** ダイアログで次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="722b6-118">In the **New ASP.NET Core Web Application** dialog:</span></span>
  * <span data-ttu-id="722b6-119">ドロップダウン リストで、**[.NET Core]** と **[ASP.NET Core 2.1]** のオプションが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="722b6-119">Ensure the options **.NET Core** and **ASP.NET Core 2.1** are selected in the drop down lists</span></span>
  * <span data-ttu-id="722b6-120">**[Web アプリケーション (モデル ビュー コントローラー)]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="722b6-120">Select the **Web Application (Model-View-Controller)** project template</span></span>
  * <span data-ttu-id="722b6-121">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="722b6-121">Ensure that **Authentication** is set to **No Authentication**</span></span>
  * <span data-ttu-id="722b6-122">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="722b6-122">Click **OK**</span></span>

<span data-ttu-id="722b6-123">注意: **[認証]** で **[なし]** ではなく **[個別のユーザー アカウント]** を使用すると、プロジェクトの `Models\IdentityModel.cs` に EntityFramework Core モデルが追加されます。</span><span class="sxs-lookup"><span data-stu-id="722b6-123">Warning: If you use **Individual User Accounts** instead of **None** for **Authentication** then an Entity Framework Core model will be added to your project in `Models\IdentityModel.cs`.</span></span> <span data-ttu-id="722b6-124">このチュートリアルで学習する手法を使用すると、エンティティ クラスを格納するのに、この既存のモデルに 2 つ目のモデルを追加するか、またはこの既存のクラスを拡張するかを選択できます。</span><span class="sxs-lookup"><span data-stu-id="722b6-124">Using the techniques you learn in this tutorial, you can choose to add a second model, or extend this existing model to contain your entity classes.</span></span>

## <a name="install-entity-framework-core"></a><span data-ttu-id="722b6-125">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="722b6-125">Install Entity Framework Core</span></span>

<span data-ttu-id="722b6-126">EF Core をインストールするには、対象となる EF Core データベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="722b6-126">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="722b6-127">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="722b6-127">For a list of available providers see [Database Providers](../../providers/index.md).</span></span> 

<span data-ttu-id="722b6-128">このチュートリアルでは、SQL Server を使用するため、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="722b6-128">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="722b6-129">この SQL Server のプロバイダー パッケージは、[Microsoft.AspnetCore.App メタパッケージ](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="722b6-129">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

## <a name="create-the-model"></a><span data-ttu-id="722b6-130">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="722b6-130">Create the model</span></span>

<span data-ttu-id="722b6-131">モデルを構成するコンテキスト クラスおよびエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="722b6-131">Define a context class and entity classes that make up the model:</span></span>

* <span data-ttu-id="722b6-132">**Models** フォルダーを右クリックし、**[追加] > [クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="722b6-132">Right-click on the **Models** folder and select **Add > Class**.</span></span>
* <span data-ttu-id="722b6-133">名前に「**Model.cs**」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="722b6-133">Enter **Model.cs** as the name and click **OK**.</span></span>
* <span data-ttu-id="722b6-134">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="722b6-134">Replace the contents of the file with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Models/Model.cs)]

<span data-ttu-id="722b6-135">実際のアプリでは、モデルの各クラスは別のファイルに記述するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="722b6-135">In a real app you would typically put each class from your model in a separate file.</span></span> <span data-ttu-id="722b6-136">わかりやすくするために、このチュートリアルではすべてのクラスを 1 つのファイルに記述しています。</span><span class="sxs-lookup"><span data-stu-id="722b6-136">For the sake of simplicity, this tutorial puts all the classes in one file.</span></span>

## <a name="register-your-context-with-dependency-injection"></a><span data-ttu-id="722b6-137">依存関係の挿入にコンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="722b6-137">Register your context with dependency injection</span></span>

<span data-ttu-id="722b6-138">サービス (`BloggingContext` など) は、アプリケーションの起動時に[依存関係の挿入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)に登録されます。</span><span class="sxs-lookup"><span data-stu-id="722b6-138">Services (such as `BloggingContext`) are registered with [dependency injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) during application startup.</span></span> <span data-ttu-id="722b6-139">これらのサービス (MVC コント ローラーなど) を必要とするコンポーネントには、コンス トラクターのパラメーターまたはプロパティを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="722b6-139">Components that require these services (such as your MVC controllers) are then provided these services via constructor parameters or properties.</span></span>

<span data-ttu-id="722b6-140">`BloggingContext` を MVC コント ローラーで使用できるようにするには、サービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="722b6-140">To make `BloggingContext` available to MVC controllers, register it as a service.</span></span>

* <span data-ttu-id="722b6-141">**Startup.cs** を開きます。</span><span class="sxs-lookup"><span data-stu-id="722b6-141">Open **Startup.cs**</span></span>
* <span data-ttu-id="722b6-142">次の `using` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="722b6-142">Add the following `using` statements:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs#AddedUsings)]

<span data-ttu-id="722b6-143">`AddDbContext` メソッドを呼び出して、コンテキストをサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="722b6-143">Call the `AddDbContext` method to register the context as a service.</span></span>

* <span data-ttu-id="722b6-144">次の強調表示されたコードが `ConfigureServices` メソッドに追加されます。</span><span class="sxs-lookup"><span data-stu-id="722b6-144">Add the following highlighted code to the `ConfigureServices` method:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.NewDb/Startup.cs?name=ConfigureServices&highlight=13-14)]

<span data-ttu-id="722b6-145">注: 実際のアプリでは、接続文字列は構成ファイルまたは環境変数に記述するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="722b6-145">Note: A real app would generally put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="722b6-146">わかりやすくするために、このチュートリアルではコード内に定義します。</span><span class="sxs-lookup"><span data-stu-id="722b6-146">For the sake of simplicity, this tutorial defines it in code.</span></span> <span data-ttu-id="722b6-147">詳細については、「[接続文字列](../../miscellaneous/connection-strings.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="722b6-147">See [Connection Strings](../../miscellaneous/connection-strings.md) for more information.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="722b6-148">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="722b6-148">Create the database</span></span>

<span data-ttu-id="722b6-149">モデルを作成したら、[移行](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)を利用してデータベースを作成できます。</span><span class="sxs-lookup"><span data-stu-id="722b6-149">Once you have a model, you can use [migrations](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) to create a database.</span></span>

* <span data-ttu-id="722b6-150">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="722b6-150">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="722b6-151">`Add-Migration InitialCreate` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。</span><span class="sxs-lookup"><span data-stu-id="722b6-151">Run `Add-Migration InitialCreate` to scaffold a migration to create the initial set of tables for your model.</span></span> <span data-ttu-id="722b6-152">`The term 'add-migration' is not recognized as the name of a cmdlet` という内容のエラーが表示された場合は、Visual Studio を閉じて再度開きます。</span><span class="sxs-lookup"><span data-stu-id="722b6-152">If you receive an error stating `The term 'add-migration' is not recognized as the name of a cmdlet`, close and reopen Visual Studio.</span></span>
* <span data-ttu-id="722b6-153">`Update-Database` を実行して、新しい移行をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="722b6-153">Run `Update-Database` to apply the new migration to the database.</span></span> <span data-ttu-id="722b6-154">このコマンドは、移行を適用する前に、データベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="722b6-154">This command creates the database before applying migrations.</span></span>

## <a name="create-a-controller"></a><span data-ttu-id="722b6-155">コントローラーを作成する</span><span class="sxs-lookup"><span data-stu-id="722b6-155">Create a controller</span></span>

<span data-ttu-id="722b6-156">`Blog` エンティティのコントローラーとビューをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="722b6-156">Scaffold a controller and views for the `Blog` entity.</span></span>

* <span data-ttu-id="722b6-157">**ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加] > [コントローラー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="722b6-157">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add > Controller**.</span></span>
* <span data-ttu-id="722b6-158">**[Entity Framework を使用したビューがある MVC コントローラー]** を選択し、**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="722b6-158">Select **MVC Controller with views, using Entity Framework** and click **Add**.</span></span>
* <span data-ttu-id="722b6-159">**[モデル クラス]** に **Blog** を、**[データ コンテキスト クラス]** に **BloggingContext** を設定します。</span><span class="sxs-lookup"><span data-stu-id="722b6-159">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**.</span></span>
* <span data-ttu-id="722b6-160">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="722b6-160">Click **Add**.</span></span>


## <a name="run-the-application"></a><span data-ttu-id="722b6-161">アプリケーションの実行</span><span class="sxs-lookup"><span data-stu-id="722b6-161">Run the application</span></span>

<span data-ttu-id="722b6-162">F5 キーを押してアプリを実行し、テストします。</span><span class="sxs-lookup"><span data-stu-id="722b6-162">Press F5 to run and test the app.</span></span>

* <span data-ttu-id="722b6-163">`/Blogs` に移動します。</span><span class="sxs-lookup"><span data-stu-id="722b6-163">Navigate to `/Blogs`</span></span>
* <span data-ttu-id="722b6-164">[作成] リンクを使用して、いくつかのブログ エントリを作成します。</span><span class="sxs-lookup"><span data-stu-id="722b6-164">Use the create link to create some blog entries.</span></span> <span data-ttu-id="722b6-165">[詳細] リンクと [削除] リンクをテストします。</span><span class="sxs-lookup"><span data-stu-id="722b6-165">Test the details and delete links.</span></span>

![イメージ](_static/create.png)

![イメージ](_static/index-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="722b6-168">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="722b6-168">Additional Resources</span></span>

* <span data-ttu-id="722b6-169">[EF - SQLite を使用した新しいデータベース](xref:core/get-started/netcore/new-db-sqlite) - クロスプラットフォーム コンソールでの EF のチュートリアル。</span><span class="sxs-lookup"><span data-stu-id="722b6-169">[EF - New database with SQLite](xref:core/get-started/netcore/new-db-sqlite) -  a cross-platform console EF tutorial.</span></span>
* [<span data-ttu-id="722b6-170">Mac または Linux での ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="722b6-170">Introduction to ASP.NET Core MVC on Mac or Linux</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [<span data-ttu-id="722b6-171">Visual Studio を使用した ASP.NET Core MVC の概要</span><span class="sxs-lookup"><span data-stu-id="722b6-171">Introduction to ASP.NET Core MVC with Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [<span data-ttu-id="722b6-172">Visual Studio を使用した ASP.NET Core と Entity Framework Core の概要</span><span class="sxs-lookup"><span data-stu-id="722b6-172">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)
