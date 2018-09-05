---
title: UWP - 新しいデータベース - EF Core の概要
author: rowanmiller
ms.date: 08/08/2018
ms.assetid: a0ae2f21-1eef-43c6-83ad-92275f9c0727
uid: core/get-started/uwp/getting-started
ms.openlocfilehash: c243ef2a1940af9bf4f4b32f17acfcce7f972862
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996911"
---
# <a name="getting-started-with-ef-core-on-universal-windows-platform-uwp-with-a-new-database"></a><span data-ttu-id="e61fd-102">データベースを新たに作成して使用するユニバーサル Windows プラットフォーム (UWP) 上の EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="e61fd-102">Getting Started with EF Core on Universal Windows Platform (UWP) with a New Database</span></span>

<span data-ttu-id="e61fd-103">このチュートリアルでは、Entity Framework Core を使用してローカル SQLite データベースに対して基本データ アクセスを実行するユニバーサル Windows プラットフォーム (UWP) アプリケーションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="e61fd-103">In this tutorial, you build a Universal Windows Platform (UWP) application that performs basic data access against a local SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="e61fd-104">[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP)。</span><span class="sxs-lookup"><span data-stu-id="e61fd-104">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e61fd-105">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="e61fd-105">Prerequisites</span></span>

* <span data-ttu-id="e61fd-106">[Windows 10 Fall Creators Update (10.0; Build 16299) 以降](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10).</span><span class="sxs-lookup"><span data-stu-id="e61fd-106">[Windows 10 Fall Creators Update (10.0; Build 16299) or later](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10).</span></span>

* <span data-ttu-id="e61fd-107">**ユニバーサル Windows プラットフォーム開発**ワークロードを含む [Visual Studio 2017](https://www.visualstudio.com/downloads/) バージョン 15.7 以降。</span><span class="sxs-lookup"><span data-stu-id="e61fd-107">[Visual Studio 2017 version 15.7 or later](https://www.visualstudio.com/downloads/) with the **Universal Windows Platform Development** workload.</span></span>

* <span data-ttu-id="e61fd-108">[.NET Core 2.1 SDK 以降](https://www.microsoft.com/net/core)。</span><span class="sxs-lookup"><span data-stu-id="e61fd-108">[.NET Core 2.1 SDK or later](https://www.microsoft.com/net/core) or later.</span></span>

## <a name="create-a-model-project"></a><span data-ttu-id="e61fd-109">モデル プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="e61fd-109">Create a model project</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e61fd-110">.NET Core ツールが UWP プロジェクトとやりとりする方法に制限があるため、モデルを UWP 以外のプロジェクトに配置し、**パッケージ マネージャー コンソール** (PMC) で移行コマンドを実行できるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e61fd-110">Due to limitations in the way .NET Core tools interact with UWP projects the model needs to be placed in a non-UWP project to be able to run migrations commands in the **Package Manager Console** (PMC)</span></span>

* <span data-ttu-id="e61fd-111">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="e61fd-111">Open Visual Studio</span></span>

* <span data-ttu-id="e61fd-112">**[ファイル] > [新規] > [プロジェクト]**</span><span class="sxs-lookup"><span data-stu-id="e61fd-112">**File > New > Project**</span></span>

* <span data-ttu-id="e61fd-113">左側のメニューから **[インストール済み] > [Visual C#] > [.NET Standard]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-113">From the left menu select **Installed > Visual C# > .NET Standard**.</span></span>

* <span data-ttu-id="e61fd-114">**[Class Library (.NET Standard)]\(クラス ライブラリ (.NET Standard)\)** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-114">Select the **Class Library (.NET Standard)** template.</span></span>

* <span data-ttu-id="e61fd-115">プロジェクト *Blogging.Model* に名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="e61fd-115">Name the project *Blogging.Model*.</span></span>

* <span data-ttu-id="e61fd-116">ソリューション *Blogging* に名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="e61fd-116">Name the solution *Blogging*.</span></span>

* <span data-ttu-id="e61fd-117">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e61fd-117">Click **OK**.</span></span>

## <a name="install-entity-framework-core"></a><span data-ttu-id="e61fd-118">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="e61fd-118">Install Entity Framework Core</span></span>

<span data-ttu-id="e61fd-119">EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="e61fd-119">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="e61fd-120">このチュートリアルでは、SQLite を使用します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-120">This tutorial uses SQLite.</span></span> <span data-ttu-id="e61fd-121">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e61fd-121">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="e61fd-122">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**。</span><span class="sxs-lookup"><span data-stu-id="e61fd-122">**Tools > NuGet Package Manager > Package Manager Console**.</span></span>

* <span data-ttu-id="e61fd-123">`Install-Package Microsoft.EntityFrameworkCore.Sqlite` を実行します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-123">Run `Install-Package Microsoft.EntityFrameworkCore.Sqlite`</span></span>

<span data-ttu-id="e61fd-124">このチュートリアルの後半では、複数の Entity Framework Core ツールを使用してデータベースのメンテナンスを行います。</span><span class="sxs-lookup"><span data-stu-id="e61fd-124">Later in this tutorial you will be using some Entity Framework Core tools to maintain the database.</span></span> <span data-ttu-id="e61fd-125">そのため、そのツールのパッケージもインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="e61fd-125">So install the tools package as well.</span></span>

* <span data-ttu-id="e61fd-126">`Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-126">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

## <a name="create-the-model"></a><span data-ttu-id="e61fd-127">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="e61fd-127">Create the model</span></span>

<span data-ttu-id="e61fd-128">ここで、モデルを編成するコンテキストとエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-128">Now it's time to define a context and entity classes that make up the model.</span></span>

* <span data-ttu-id="e61fd-129">*Class1.cs* を削除します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-129">Delete *Class1.cs*.</span></span>

* <span data-ttu-id="e61fd-130">次のコードを使用して、*Model.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-130">Create *Model.cs* with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.Model/Model.cs)]

## <a name="create-a-new-uwp-project"></a><span data-ttu-id="e61fd-131">新しい UWP プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="e61fd-131">Create a new UWP project</span></span>

* <span data-ttu-id="e61fd-132">**ソリューション エクスプローラー**で、ソリューションを右クリックし、次に **[追加]、[新しいプロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-132">In **Solution Explorer**, right-click the solution, and then choose **Add > New Project**.</span></span>

* <span data-ttu-id="e61fd-133">左側のメニューから **[インストール済み] > [Visual C#] > [Windows ユニバーサル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-133">From the left menu select **Installed > Visual C# > Windows Universal**.</span></span>

* <span data-ttu-id="e61fd-134">**[空のアプリ (ユニバーサル Windows)]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-134">Select the **Blank App (Universal Windows)** project template.</span></span>

* <span data-ttu-id="e61fd-135">プロジェクト *Blogging.UWP* に名前を付け、**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e61fd-135">Name the project *Blogging.UWP*, and click **OK**</span></span>

* <span data-ttu-id="e61fd-136">ターゲットおよび最小バージョンを、**Windows 10 Fall Creators Update (10.0; build 16299.0)** 以上に設定します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-136">Set the target and minimum versions to at least **Windows 10 Fall Creators Update (10.0; build 16299.0)**.</span></span>

## <a name="create-the-initial-migration"></a><span data-ttu-id="e61fd-137">初期移行を作成する</span><span class="sxs-lookup"><span data-stu-id="e61fd-137">Create the initial migration</span></span>

<span data-ttu-id="e61fd-138">これでモデルの用意ができましたので、初めて実行されたときにデータベースが作成されるように、アプリを設定します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-138">Now that you have a model, set up the app to create a database the first time it runs.</span></span> <span data-ttu-id="e61fd-139">このセクションでは、初期移行を作成します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-139">In this section, you create the initial migration.</span></span> <span data-ttu-id="e61fd-140">次のセクションでは、アプリの起動時にこの移行を適用するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-140">In the following section, you add code that applies this migration when the app starts.</span></span>

<span data-ttu-id="e61fd-141">移行ツールでは UWP 以外のスタートアップ プロジェクトが必要であるため、まずそれを作成します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-141">Migrations tools require a non-UWP startup project, so create that first.</span></span>

* <span data-ttu-id="e61fd-142">**ソリューション エクスプローラー**で、ソリューションを右クリックし、次に **[追加]、[新しいプロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-142">In **Solution Explorer**, right-click the solution, and then choose **Add > New Project**.</span></span>

* <span data-ttu-id="e61fd-143">左側のメニューから **[インストール済み] > [Visual C#] > [.NET Core]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-143">From the left menu select **Installed > Visual C# > .NET Core**.</span></span>

* <span data-ttu-id="e61fd-144">**[コンソール アプリ (.NET Core)]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-144">Select the **Console App (.NET Core)** project template.</span></span>

* <span data-ttu-id="e61fd-145">プロジェクト *Blogging.Migrations.Startup* に名前を付けて、**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e61fd-145">Name the project *Blogging.Migrations.Startup*, and click **OK**.</span></span>

* <span data-ttu-id="e61fd-146">*Blogging.Migrations.Startup* プロジェクトから *Blogging.Model* プロジェクトへのプロジェクト参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-146">Add a project reference from the *Blogging.Migrations.Startup* project to the *Blogging.Model* project.</span></span>

<span data-ttu-id="e61fd-147">これで、ご自分の初期移行を作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e61fd-147">Now you can create your initial migration.</span></span>

* <span data-ttu-id="e61fd-148">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="e61fd-148">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="e61fd-149">**[既定のプロジェクト]** として *Blogging.Model* プロジェクトを選択します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-149">Select the *Blogging.Model* project as the **Default project**.</span></span>

* <span data-ttu-id="e61fd-150">**ソリューション エクスプローラー**で、*Blogging.Migrations.Startup* プロジェクトをスタートアップ プロジェクトに設定します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-150">In **Solution Explorer**, set the *Blogging.Migrations.Startup* project as the startup project.</span></span>

* <span data-ttu-id="e61fd-151">`Add-Migration InitialCreate` を実行します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-151">Run `Add-Migration InitialCreate`.</span></span>

  <span data-ttu-id="e61fd-152">このコマンドでは、ご利用のモデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="e61fd-152">This command scaffolds a migration that creates the initial set of tables for your model.</span></span>

## <a name="create-the-database-on-app-startup"></a><span data-ttu-id="e61fd-153">アプリの起動時にデータベースを作成する</span><span class="sxs-lookup"><span data-stu-id="e61fd-153">Create the database on app startup</span></span>

<span data-ttu-id="e61fd-154">アプリが実行されているデバイス上にデータベースが作成されるようにしたいので、アプリケーションの起動時に保留中の移行をローカル データベースに適用するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-154">Since you want the database to be created on the device that the app runs on, add code to apply any pending migrations to the local database on application startup.</span></span> <span data-ttu-id="e61fd-155">これにより、アプリの初回実行時にローカル データベースの作成が行われます。</span><span class="sxs-lookup"><span data-stu-id="e61fd-155">The first time that the app runs, this will take care of creating the local database.</span></span>

* <span data-ttu-id="e61fd-156">*Blogging.UWP* プロジェクトから *Blogging.Model* プロジェクトへのプロジェクト参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-156">Add a project reference from the *Blogging.UWP* project to the *Blogging.Model* project.</span></span>

* <span data-ttu-id="e61fd-157">*App.xaml.cs* を開きます。</span><span class="sxs-lookup"><span data-stu-id="e61fd-157">Open *App.xaml.cs*.</span></span>

* <span data-ttu-id="e61fd-158">保留中の移行を適用する強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-158">Add the highlighted code to apply any pending migrations.</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/App.xaml.cs?highlight=1-2,26-29)]

> [!TIP]  
> <span data-ttu-id="e61fd-159">ご利用のモデルを変更する場合は、`Add-Migration` コマンドを使用して新しい移行をスキャフォールディングして、対応する変更をデータベースに適用できます。</span><span class="sxs-lookup"><span data-stu-id="e61fd-159">If you change your model, use the `Add-Migration` command to scaffold a new migration to apply the corresponding changes to the database.</span></span> <span data-ttu-id="e61fd-160">保留中の移行は、アプリケーションの起動時に各デバイス上のローカル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="e61fd-160">Any pending migrations will be applied to the local database on each device when the application starts.</span></span>
>
><span data-ttu-id="e61fd-161">EF はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-161">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="e61fd-162">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="e61fd-162">Use the model</span></span>

<span data-ttu-id="e61fd-163">モデルを使用してデータ アクセスを実行できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e61fd-163">You can now use the model to perform data access.</span></span>

* <span data-ttu-id="e61fd-164">*MainPage.xaml* を開きます。</span><span class="sxs-lookup"><span data-stu-id="e61fd-164">Open *MainPage.xaml*.</span></span>

* <span data-ttu-id="e61fd-165">以下の強調表示されたページ読み込みハンドラーと UI コンテンツを追加します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-165">Add the page load handler and UI content highlighted below</span></span>

[!code-xml[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/MainPage.xaml?highlight=9,11-23)]

<span data-ttu-id="e61fd-166">ここで、UI とデータベースを接続するためのコードを追加する</span><span class="sxs-lookup"><span data-stu-id="e61fd-166">Now add code to wire up the UI with the database</span></span>

* <span data-ttu-id="e61fd-167">*MainPage.xaml.cs* を開きます。</span><span class="sxs-lookup"><span data-stu-id="e61fd-167">Open *MainPage.xaml.cs*.</span></span>

* <span data-ttu-id="e61fd-168">次の一覧の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-168">Add the highlighted code from the following listing:</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/MainPage.xaml.cs?highlight=1,31-49)]

<span data-ttu-id="e61fd-169">アプリケーションを実行して動作を確認できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e61fd-169">You can now run the application to see it in action.</span></span>

* <span data-ttu-id="e61fd-170">**ソリューション エクスプローラー**で、*Blogging.UWP* プロジェクトを右クリックし、次に **[配置]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-170">In **Solution Explorer**, right-click the *Blogging.UWP* project and then select **Deploy**.</span></span>

* <span data-ttu-id="e61fd-171">スタートアップ プロジェクトとして、*Blogging.UWP* を設定します。</span><span class="sxs-lookup"><span data-stu-id="e61fd-171">Set *Blogging.UWP* as the startup project.</span></span>

* <span data-ttu-id="e61fd-172">**[デバッグ] > [デバッグなしで開始]**</span><span class="sxs-lookup"><span data-stu-id="e61fd-172">**Debug > Start Without Debugging**</span></span>

  <span data-ttu-id="e61fd-173">テストがビルドされ、実行されます。</span><span class="sxs-lookup"><span data-stu-id="e61fd-173">The app builds and runs.</span></span>

* <span data-ttu-id="e61fd-174">URL を入力して **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e61fd-174">Enter a URL and click the **Add** button</span></span>

  ![イメージ](_static/create.png)

  ![イメージ](_static/list.png)

  <span data-ttu-id="e61fd-177">さあ、できました!</span><span class="sxs-lookup"><span data-stu-id="e61fd-177">Tada!</span></span> <span data-ttu-id="e61fd-178">これで Entity Framework Core を実行するシンプルな UWP アプリが完成しました。</span><span class="sxs-lookup"><span data-stu-id="e61fd-178">You now have a simple UWP app running Entity Framework Core.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e61fd-179">次の手順</span><span class="sxs-lookup"><span data-stu-id="e61fd-179">Next steps</span></span>

<span data-ttu-id="e61fd-180">EF Core と UWP を一緒に使用する場合に把握しておく必要がある互換性とパフォーマンスに関する情報については、「[EF Core でサポートされている .NET 実装](../../platforms/index.md#universal-windows-platform)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e61fd-180">For compatibility and performance information that you should know when using EF Core with UWP, see [.NET implementations supported by EF Core](../../platforms/index.md#universal-windows-platform).</span></span>

<span data-ttu-id="e61fd-181">Entity Framework Core の機能の詳細については、このドキュメントの他の記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e61fd-181">Check out other articles in this documentation to learn more about Entity Framework Core features.</span></span>
