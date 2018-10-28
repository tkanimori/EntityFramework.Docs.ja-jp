---
title: UWP - 新しいデータベース - EF Core の概要
author: rowanmiller
ms.date: 10/13/2018
ms.assetid: a0ae2f21-1eef-43c6-83ad-92275f9c0727
uid: core/get-started/uwp/getting-started
ms.openlocfilehash: 456967a0dc981053919064a19cc9c98bf7309865
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022377"
---
# <a name="getting-started-with-ef-core-on-universal-windows-platform-uwp-with-a-new-database"></a><span data-ttu-id="ade61-102">データベースを新たに作成して使用するユニバーサル Windows プラットフォーム (UWP) 上の EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="ade61-102">Getting Started with EF Core on Universal Windows Platform (UWP) with a New Database</span></span>

<span data-ttu-id="ade61-103">このチュートリアルでは、Entity Framework Core を使用してローカル SQLite データベースに対して基本データ アクセスを実行するユニバーサル Windows プラットフォーム (UWP) アプリケーションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="ade61-103">In this tutorial, you build a Universal Windows Platform (UWP) application that performs basic data access against a local SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="ade61-104">[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP)。</span><span class="sxs-lookup"><span data-stu-id="ade61-104">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ade61-105">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="ade61-105">Prerequisites</span></span>

* <span data-ttu-id="ade61-106">[Windows 10 Fall Creators Update (10.0; Build 16299) 以降](https://support.microsoft.com/help/4027667/windows-update-windows-10).</span><span class="sxs-lookup"><span data-stu-id="ade61-106">[Windows 10 Fall Creators Update (10.0; Build 16299) or later](https://support.microsoft.com/help/4027667/windows-update-windows-10).</span></span>

* <span data-ttu-id="ade61-107">**ユニバーサル Windows プラットフォーム開発**ワークロードを含む [Visual Studio 2017](https://www.visualstudio.com/downloads/) バージョン 15.7 以降。</span><span class="sxs-lookup"><span data-stu-id="ade61-107">[Visual Studio 2017 version 15.7 or later](https://www.visualstudio.com/downloads/) with the **Universal Windows Platform Development** workload.</span></span>

* <span data-ttu-id="ade61-108">[.NET Core 2.1 SDK 以降](https://www.microsoft.com/net/core)。</span><span class="sxs-lookup"><span data-stu-id="ade61-108">[.NET Core 2.1 SDK or later](https://www.microsoft.com/net/core) or later.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ade61-109">このチュートリアルでは Entity Framework Core[ 移行](xref:core/managing-schemas/migrations/index)コマンドを使用して、データベースのスキーマを作成および更新します。</span><span class="sxs-lookup"><span data-stu-id="ade61-109">This tutorial uses Entity Framework Core [migrations](xref:core/managing-schemas/migrations/index) commands to create and update the schema of the database.</span></span>
> <span data-ttu-id="ade61-110">これらのコマンドによって、UWP プロジェクトは直接操作されません。</span><span class="sxs-lookup"><span data-stu-id="ade61-110">These commands don't work directly with UWP projects.</span></span>
> <span data-ttu-id="ade61-111">このため、アプリケーションのデータ モデルは共有ライブラリ プロジェクト内に配置され、コマンドは別の .NET Core コンソール アプリケーションを使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="ade61-111">For this reason, the application's data model is placed in a shared library project, and a separate .NET Core console application is used to run the commands.</span></span>

## <a name="create-a-library-project-to-hold-the-data-model"></a><span data-ttu-id="ade61-112">データ モデルを保持するためのライブラリ プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="ade61-112">Create a library project to hold the data model</span></span>

* <span data-ttu-id="ade61-113">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="ade61-113">Open Visual Studio</span></span>

* <span data-ttu-id="ade61-114">**[ファイル] > [新規] > [プロジェクト]**</span><span class="sxs-lookup"><span data-stu-id="ade61-114">**File > New > Project**</span></span>

* <span data-ttu-id="ade61-115">左側のメニューから **[インストール済み] > [Visual C#] > [.NET Standard]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ade61-115">From the left menu select **Installed > Visual C# > .NET Standard**.</span></span>

* <span data-ttu-id="ade61-116">**[Class Library (.NET Standard)]\(クラス ライブラリ (.NET Standard)\)** テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="ade61-116">Select the **Class Library (.NET Standard)** template.</span></span>

* <span data-ttu-id="ade61-117">プロジェクト *Blogging.Model* に名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="ade61-117">Name the project *Blogging.Model*.</span></span>

* <span data-ttu-id="ade61-118">ソリューション *Blogging* に名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="ade61-118">Name the solution *Blogging*.</span></span>

* <span data-ttu-id="ade61-119">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ade61-119">Click **OK**.</span></span>

## <a name="install-entity-framework-core-runtime-in-the-data-model-project"></a><span data-ttu-id="ade61-120">データ モデル プロジェクトに Entity Framework Core ランタイムをインストールする</span><span class="sxs-lookup"><span data-stu-id="ade61-120">Install Entity Framework Core runtime in the data model project</span></span>

<span data-ttu-id="ade61-121">EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="ade61-121">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="ade61-122">このチュートリアルでは、SQLite を使用します。</span><span class="sxs-lookup"><span data-stu-id="ade61-122">This tutorial uses SQLite.</span></span> <span data-ttu-id="ade61-123">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ade61-123">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="ade61-124">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**。</span><span class="sxs-lookup"><span data-stu-id="ade61-124">**Tools > NuGet Package Manager > Package Manager Console**.</span></span>

* <span data-ttu-id="ade61-125">パッケージ マネージャー コンソール内でライブラリ プロジェクト *Blogging.Model* が **[既定のプロジェクト]** として選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="ade61-125">Make sure that the library project *Blogging.Model* is selected as the **Default Project** in the Package Manager Console.</span></span>

* <span data-ttu-id="ade61-126">`Install-Package Microsoft.EntityFrameworkCore.Sqlite` を実行します。</span><span class="sxs-lookup"><span data-stu-id="ade61-126">Run `Install-Package Microsoft.EntityFrameworkCore.Sqlite`</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="ade61-127">データ モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="ade61-127">Create the data model</span></span>

<span data-ttu-id="ade61-128">ここで、モデルを編成する*DbContext* とエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="ade61-128">Now it's time to define the *DbContext* and entity classes that make up the model.</span></span>

* <span data-ttu-id="ade61-129">*Class1.cs* を削除します。</span><span class="sxs-lookup"><span data-stu-id="ade61-129">Delete *Class1.cs*.</span></span>

* <span data-ttu-id="ade61-130">次のコードを使用して、*Model.cs* を作成します。</span><span class="sxs-lookup"><span data-stu-id="ade61-130">Create *Model.cs* with the following code:</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.Model/Model.cs)]

## <a name="create-a-new-console-project-to-run-migrations-commands"></a><span data-ttu-id="ade61-131">移行コマンドを実行するための新しいコンソール プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="ade61-131">Create a new console project to run migrations commands</span></span>

* <span data-ttu-id="ade61-132">**ソリューション エクスプローラー**で、ソリューションを右クリックし、次に **[追加]、[新しいプロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ade61-132">In **Solution Explorer**, right-click the solution, and then choose **Add > New Project**.</span></span>

* <span data-ttu-id="ade61-133">左側のメニューから **[インストール済み] > [Visual C#] > [.NET Core]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ade61-133">From the left menu select **Installed > Visual C# > .NET Core**.</span></span>

* <span data-ttu-id="ade61-134">**[コンソール アプリ (.NET Core)]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="ade61-134">Select the **Console App (.NET Core)** project template.</span></span>

* <span data-ttu-id="ade61-135">プロジェクト *Blogging.Migrations.Startup* に名前を付けて、**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ade61-135">Name the project *Blogging.Migrations.Startup*, and click **OK**.</span></span>

* <span data-ttu-id="ade61-136">*Blogging.Migrations.Startup* プロジェクトから *Blogging.Model* プロジェクトへのプロジェクト参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="ade61-136">Add a project reference from the *Blogging.Migrations.Startup* project to the *Blogging.Model* project.</span></span>

## <a name="install-entity-framework-core-tools-in-the-migrations-startup-project"></a><span data-ttu-id="ade61-137">移行のスタートアップ プロジェクトに Entity Framework Core ツールをインストールする</span><span class="sxs-lookup"><span data-stu-id="ade61-137">Install Entity Framework Core tools in the migrations startup project</span></span>

<span data-ttu-id="ade61-138">パッケージ マネージャー コンソール内で EF Core 移行コマンドを有効にするには、EF Core ツールのパッケージをコンソール アプリケーションにインストールします。</span><span class="sxs-lookup"><span data-stu-id="ade61-138">To enable the EF Core migration commands in the Package Manager Console, install the EF Core tools package in the console application.</span></span>

* <span data-ttu-id="ade61-139">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="ade61-139">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="ade61-140">`Install-Package Microsoft.EntityFrameworkCore.Tools -ProjectName Blogging.Migrations.Startup` を実行します。</span><span class="sxs-lookup"><span data-stu-id="ade61-140">Run `Install-Package Microsoft.EntityFrameworkCore.Tools -ProjectName Blogging.Migrations.Startup`</span></span>

## <a name="create-the-initial-migration"></a><span data-ttu-id="ade61-141">初期移行を作成する</span><span class="sxs-lookup"><span data-stu-id="ade61-141">Create the initial migration</span></span>

 <span data-ttu-id="ade61-142">最初の移行を作成し、コンソール アプリケーションをスタートアップ プロジェクトとして指定します。</span><span class="sxs-lookup"><span data-stu-id="ade61-142">Create the initial migration, specifying the console application as the startup project.</span></span>

* <span data-ttu-id="ade61-143">`Add-Migration InitialCreate -StartupProject Blogging.Migrations.Startup` を実行します。</span><span class="sxs-lookup"><span data-stu-id="ade61-143">Run `Add-Migration InitialCreate -StartupProject Blogging.Migrations.Startup`</span></span>

<span data-ttu-id="ade61-144">このコマンドでは、ご利用のデータ モデル用に最初のデータベース テーブル セットを作成する移行がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="ade61-144">This command scaffolds a migration that creates the initial set of database tables for your data model.</span></span>

## <a name="create-the-uwp-project"></a><span data-ttu-id="ade61-145">UWP プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="ade61-145">Create the UWP project</span></span>

* <span data-ttu-id="ade61-146">**ソリューション エクスプローラー**で、ソリューションを右クリックし、次に **[追加]、[新しいプロジェクト]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ade61-146">In **Solution Explorer**, right-click the solution, and then choose **Add > New Project**.</span></span>

* <span data-ttu-id="ade61-147">左側のメニューから **[インストール済み] > [Visual C#] > [Windows ユニバーサル]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="ade61-147">From the left menu select **Installed > Visual C# > Windows Universal**.</span></span>

* <span data-ttu-id="ade61-148">**[空のアプリ (ユニバーサル Windows)]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="ade61-148">Select the **Blank App (Universal Windows)** project template.</span></span>

* <span data-ttu-id="ade61-149">プロジェクト *Blogging.UWP* に名前を付け、**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ade61-149">Name the project *Blogging.UWP*, and click **OK**</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ade61-150">ターゲットおよび最小バージョンを、**Windows 10 Fall Creators Update (10.0; build 16299.0)** 以上に設定します。</span><span class="sxs-lookup"><span data-stu-id="ade61-150">Set the target and minimum versions to at least **Windows 10 Fall Creators Update (10.0; build 16299.0)**.</span></span>
> <span data-ttu-id="ade61-151">Windows 10 の以前のバージョンでは、Entity Framework Core によって必要とされる .NET Standard 2.0 がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ade61-151">Previous  versions of Windows 10 do not support .NET Standard 2.0, which is required by Entity Framework Core.</span></span>

## <a name="add-code-to-create-the-database-on-application-startup"></a><span data-ttu-id="ade61-152">アプリケーションの起動時にデータベースを作成するためのコードを追加する</span><span class="sxs-lookup"><span data-stu-id="ade61-152">Add code to create the database on application startup</span></span>

<span data-ttu-id="ade61-153">アプリが実行されているデバイス上にデータベースが作成されるようにしたいので、アプリケーションの起動時に保留中の移行をローカル データベースに適用するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ade61-153">Since you want the database to be created on the device that the app runs on, add code to apply any pending migrations to the local database on application startup.</span></span> <span data-ttu-id="ade61-154">これにより、アプリの初回実行時にローカル データベースの作成が行われます。</span><span class="sxs-lookup"><span data-stu-id="ade61-154">The first time that the app runs, this will take care of creating the local database.</span></span>

* <span data-ttu-id="ade61-155">*Blogging.UWP* プロジェクトから *Blogging.Model* プロジェクトへのプロジェクト参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="ade61-155">Add a project reference from the *Blogging.UWP* project to the *Blogging.Model* project.</span></span>

* <span data-ttu-id="ade61-156">*App.xaml.cs* を開きます。</span><span class="sxs-lookup"><span data-stu-id="ade61-156">Open *App.xaml.cs*.</span></span>

* <span data-ttu-id="ade61-157">保留中の移行を適用する強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ade61-157">Add the highlighted code to apply any pending migrations.</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/App.xaml.cs?highlight=1-2,26-29)]

> [!TIP]  
> <span data-ttu-id="ade61-158">ご利用のモデルを変更する場合は、`Add-Migration` コマンドを使用して新しい移行をスキャフォールディングして、対応する変更をデータベースに適用できます。</span><span class="sxs-lookup"><span data-stu-id="ade61-158">If you change your model, use the `Add-Migration` command to scaffold a new migration to apply the corresponding changes to the database.</span></span> <span data-ttu-id="ade61-159">保留中の移行は、アプリケーションの起動時に各デバイス上のローカル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="ade61-159">Any pending migrations will be applied to the local database on each device when the application starts.</span></span>
>
><span data-ttu-id="ade61-160">EF Core はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="ade61-160">EF Core uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>

## <a name="use-the-data-model"></a><span data-ttu-id="ade61-161">データ モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="ade61-161">Use the data model</span></span>

<span data-ttu-id="ade61-162">EF Core 使用してデータ アクセスを使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="ade61-162">You can now use EF Core to perform data access.</span></span>

* <span data-ttu-id="ade61-163">*MainPage.xaml* を開きます。</span><span class="sxs-lookup"><span data-stu-id="ade61-163">Open *MainPage.xaml*.</span></span>

* <span data-ttu-id="ade61-164">以下の強調表示されたページ読み込みハンドラーと UI コンテンツを追加します。</span><span class="sxs-lookup"><span data-stu-id="ade61-164">Add the page load handler and UI content highlighted below</span></span>

[!code-xml[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/MainPage.xaml?highlight=9,11-23)]

<span data-ttu-id="ade61-165">ここで、UI とデータベースを接続するためのコードを追加する</span><span class="sxs-lookup"><span data-stu-id="ade61-165">Now add code to wire up the UI with the database</span></span>

* <span data-ttu-id="ade61-166">*MainPage.xaml.cs* を開きます。</span><span class="sxs-lookup"><span data-stu-id="ade61-166">Open *MainPage.xaml.cs*.</span></span>

* <span data-ttu-id="ade61-167">次の一覧の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ade61-167">Add the highlighted code from the following listing:</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/Blogging.UWP/MainPage.xaml.cs?highlight=1,31-49)]

<span data-ttu-id="ade61-168">アプリケーションを実行して動作を確認できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="ade61-168">You can now run the application to see it in action.</span></span>

* <span data-ttu-id="ade61-169">**ソリューション エクスプローラー**で、*Blogging.UWP* プロジェクトを右クリックし、次に **[配置]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ade61-169">In **Solution Explorer**, right-click the *Blogging.UWP* project and then select **Deploy**.</span></span>

* <span data-ttu-id="ade61-170">スタートアップ プロジェクトとして、*Blogging.UWP* を設定します。</span><span class="sxs-lookup"><span data-stu-id="ade61-170">Set *Blogging.UWP* as the startup project.</span></span>

* <span data-ttu-id="ade61-171">**[デバッグ] > [デバッグなしで開始]**</span><span class="sxs-lookup"><span data-stu-id="ade61-171">**Debug > Start Without Debugging**</span></span>

  <span data-ttu-id="ade61-172">テストがビルドされ、実行されます。</span><span class="sxs-lookup"><span data-stu-id="ade61-172">The app builds and runs.</span></span>

* <span data-ttu-id="ade61-173">URL を入力して **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="ade61-173">Enter a URL and click the **Add** button</span></span>

  ![イメージ](_static/create.png)

  ![イメージ](_static/list.png)

  <span data-ttu-id="ade61-176">さあ、できました!</span><span class="sxs-lookup"><span data-stu-id="ade61-176">Tada!</span></span> <span data-ttu-id="ade61-177">これで Entity Framework Core を実行するシンプルな UWP アプリが完成しました。</span><span class="sxs-lookup"><span data-stu-id="ade61-177">You now have a simple UWP app running Entity Framework Core.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ade61-178">次の手順</span><span class="sxs-lookup"><span data-stu-id="ade61-178">Next steps</span></span>

<span data-ttu-id="ade61-179">EF Core と UWP を一緒に使用する場合に把握しておく必要がある互換性とパフォーマンスに関する情報については、「[EF Core でサポートされている .NET 実装](../../platforms/index.md#universal-windows-platform)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ade61-179">For compatibility and performance information that you should know when using EF Core with UWP, see [.NET implementations supported by EF Core](../../platforms/index.md#universal-windows-platform).</span></span>

<span data-ttu-id="ade61-180">Entity Framework Core の機能の詳細については、このドキュメントの他の記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="ade61-180">Check out other articles in this documentation to learn more about Entity Framework Core features.</span></span>
