---
title: UWP - 新しいデータベース - EF Core の概要
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.topic: get-started-article
ms.assetid: a0ae2f21-1eef-43c6-83ad-92275f9c0727
ms.technology: entity-framework-core
uid: core/get-started/uwp/getting-started
ms.openlocfilehash: f743ff5392d1f30283a13d2e7fb8029be88387aa
ms.sourcegitcommit: 96324e58c02b97277395ed43173bf13ac80d2012
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
ms.locfileid: "26049835"
---
# <a name="getting-started-with-ef-core-on-universal-windows-platform-uwp-with-a-new-database"></a><span data-ttu-id="84c6c-102">データベースを新たに作成して使用するユニバーサル Windows プラットフォーム (UWP) 上の EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="84c6c-102">Getting Started with EF Core on Universal Windows Platform (UWP) with a New Database</span></span>

> [!NOTE]
> <span data-ttu-id="84c6c-103">このチュートリアルでは、EF Core 2.0.1 (ASP.NET Core と .NET Core SDK 2.0.3 と共にリリース) を使用します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-103">This tutorial uses EF Core 2.0.1 (released alongside ASP.NET Core and .NET Core SDK 2.0.3).</span></span> <span data-ttu-id="84c6c-104">EF Core 2.0.0 では、良好な UWP エクスペリエンスに必要な、重要なバグ修正が一部不足しています。</span><span class="sxs-lookup"><span data-stu-id="84c6c-104">EF Core 2.0.0 lacks some crucial bug fixes required for a good UWP experience.</span></span>

<span data-ttu-id="84c6c-105">このチュートリアルでは、Entity Framework を使用してローカル SQLite データベースに対して基本データ アクセスを実行するユニバーサル Windows プラットフォーム (UWP) アプリケーションをビルドします。</span><span class="sxs-lookup"><span data-stu-id="84c6c-105">In this walkthrough, you will build a Universal Windows Platform (UWP) application that performs basic data access against a local SQLite database using Entity Framework.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="84c6c-106">**UWP 上の LINQ クエリでは、匿名型を使用しないことを考慮してください**。</span><span class="sxs-lookup"><span data-stu-id="84c6c-106">**Consider avoiding anonymous types in LINQ queries on UWP**.</span></span> <span data-ttu-id="84c6c-107">UWP アプリケーションをアプリ ストアに配置するには、アプリケーションを .NET ネイティブでコンパイルする必要があります。</span><span class="sxs-lookup"><span data-stu-id="84c6c-107">Deploying a UWP application to the app store requires your application to be compiled with .NET Native.</span></span> <span data-ttu-id="84c6c-108">匿名型のクエリは、.NET ネイティブではパフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-108">Queries with anonymous types have worse performance on .NET Native.</span></span>

> [!TIP]
> <span data-ttu-id="84c6c-109">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP/UWP.SQLite)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="84c6c-109">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/UWP/UWP.SQLite) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84c6c-110">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="84c6c-110">Prerequisites</span></span>

<span data-ttu-id="84c6c-111">このチュートリアルを実施するのに必要な項目は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="84c6c-111">The following items are required to complete this walkthrough:</span></span>

* <span data-ttu-id="84c6c-112">[Windows 10 Fall Creators Update](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10) (10.0.16299.0)</span><span class="sxs-lookup"><span data-stu-id="84c6c-112">[Windows 10 Fall Creators Update](https://support.microsoft.com/en-us/help/4027667/windows-update-windows-10) (10.0.16299.0)</span></span>

* <span data-ttu-id="84c6c-113">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) 以降。</span><span class="sxs-lookup"><span data-stu-id="84c6c-113">[.NET Core 2.0.0 SDK](https://www.microsoft.com/net/core) or later.</span></span>

* <span data-ttu-id="84c6c-114">**Universal Windows Platform Development** ワークロードを含む [Visual Studio 2017](https://www.visualstudio.com/downloads/) バージョン 15.4 以降。</span><span class="sxs-lookup"><span data-stu-id="84c6c-114">[Visual Studio 2017](https://www.visualstudio.com/downloads/) version 15.4 or later with the **Universal Windows Platform Development** workload.</span></span>

## <a name="create-a-new-model-project"></a><span data-ttu-id="84c6c-115">新しいモデル プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="84c6c-115">Create a new model project</span></span>

> [!WARNING]
> <span data-ttu-id="84c6c-116">.NET Core ツールが UWP プロジェクトとやりとりする方法に制限があるため、モデルを UWP 以外のプロジェクトに配置し、パッケージ マネージャー コンソールで移行コマンドを実行できるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="84c6c-116">Due to limitations in the way .NET Core tools interact with UWP projects the model needs to be placed in a non-UWP project to be able to run migrations commands in the Package Manager Console</span></span>

* <span data-ttu-id="84c6c-117">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="84c6c-117">Open Visual Studio</span></span>

* <span data-ttu-id="84c6c-118">[ファイル] > [新規] > [プロジェクト...]</span><span class="sxs-lookup"><span data-stu-id="84c6c-118">File > New > Project...</span></span>

* <span data-ttu-id="84c6c-119">左側のメニューから [テンプレート] > [Visual C#] を選択します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-119">From the left menu select Templates > Visual C#</span></span>

* <span data-ttu-id="84c6c-120">**[Class Library (.NET Standard)]\(クラス ライブラリ (.NET Standard)\)** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-120">Select the **Class Library (.NET Standard)** project template</span></span>

* <span data-ttu-id="84c6c-121">プロジェクト名を設定し、**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="84c6c-121">Give the project a name and click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="84c6c-122">Entity Framework をインストールする</span><span class="sxs-lookup"><span data-stu-id="84c6c-122">Install Entity Framework</span></span>

<span data-ttu-id="84c6c-123">EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="84c6c-123">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="84c6c-124">このチュートリアルでは SQLite を使用します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-124">This walkthrough uses SQLite.</span></span> <span data-ttu-id="84c6c-125">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="84c6c-125">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="84c6c-126">[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]</span><span class="sxs-lookup"><span data-stu-id="84c6c-126">Tools > NuGet Package Manager > Package Manager Console</span></span>

* <span data-ttu-id="84c6c-127">`Install-Package Microsoft.EntityFrameworkCore.Sqlite` を実行します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-127">Run `Install-Package Microsoft.EntityFrameworkCore.Sqlite`</span></span>

<span data-ttu-id="84c6c-128">このチュートリアルの後半では、データベースのメンテナンスで複数の Entity Framework ツールも使用します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-128">Later in this walkthrough we will also be using some Entity Framework Tools to maintain the database.</span></span> <span data-ttu-id="84c6c-129">そこで、そのツールのパッケージもインストールします。</span><span class="sxs-lookup"><span data-stu-id="84c6c-129">So we will install the tools package as well.</span></span>

* <span data-ttu-id="84c6c-130">`Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-130">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

* <span data-ttu-id="84c6c-131">.csproj ファイルを編集して `<TargetFramework>netstandard2.0</TargetFramework>` を `<TargetFrameworks>netcoreapp2.0;netstandard2.0</TargetFrameworks>` に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="84c6c-131">Edit the .csproj file and replace `<TargetFramework>netstandard2.0</TargetFramework>` with `<TargetFrameworks>netcoreapp2.0;netstandard2.0</TargetFrameworks>`</span></span>

## <a name="create-your-model"></a><span data-ttu-id="84c6c-132">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="84c6c-132">Create your model</span></span>

<span data-ttu-id="84c6c-133">ここで、モデルを編成するコンテキストとエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-133">Now it's time to define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="84c6c-134">[プロジェクト] > [クラスの追加...]</span><span class="sxs-lookup"><span data-stu-id="84c6c-134">Project > Add Class...</span></span>

* <span data-ttu-id="84c6c-135">名前に「*Model.cs*」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="84c6c-135">Enter *Model.cs* as the name and click **OK**</span></span>

* <span data-ttu-id="84c6c-136">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="84c6c-136">Replace the contents of the file with the following code</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/UWP.Model/Model.cs)]

## <a name="create-a-new-uwp-project"></a><span data-ttu-id="84c6c-137">新しい UWP プロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="84c6c-137">Create a new UWP project</span></span>

* <span data-ttu-id="84c6c-138">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="84c6c-138">Open Visual Studio</span></span>

* <span data-ttu-id="84c6c-139">[ファイル] > [新規] > [プロジェクト...]</span><span class="sxs-lookup"><span data-stu-id="84c6c-139">File > New > Project...</span></span>

* <span data-ttu-id="84c6c-140">左側のメニューから、[テンプレート] > [Visual C#] > [Windows ユニバーサル] を選択します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-140">From the left menu select Templates > Visual C# > Windows Universal</span></span>

* <span data-ttu-id="84c6c-141">**[空のアプリ (ユニバーサル Windows)]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-141">Select the **Blank App (Universal Windows)** project template</span></span>

* <span data-ttu-id="84c6c-142">プロジェクト名を設定し、**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="84c6c-142">Give the project a name and click **OK**</span></span>

* <span data-ttu-id="84c6c-143">ターゲット バージョンと最小バージョンを `Windows 10 Fall Creators Update (10.0; build 16299.0)` 以上に設定します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-143">Set the target and minimum versions to at least `Windows 10 Fall Creators Update (10.0; build 16299.0)`</span></span>

## <a name="create-your-database"></a><span data-ttu-id="84c6c-144">データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="84c6c-144">Create your database</span></span>

<span data-ttu-id="84c6c-145">これでモデルができたので、移行を利用して自分用のデータベースを作成できます。</span><span class="sxs-lookup"><span data-stu-id="84c6c-145">Now that you have a model, you can use migrations to create a database for you.</span></span>

* <span data-ttu-id="84c6c-146">[ツール] -> [NuGet パッケージ マネージャー] -> [パッケージ マネージャー コンソール]</span><span class="sxs-lookup"><span data-stu-id="84c6c-146">Tools –> NuGet Package Manager –> Package Manager Console</span></span>

* <span data-ttu-id="84c6c-147">モデル プロジェクトを既定のプロジェクトに選択し、スタートアップ プロジェクトに設定します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-147">Select the model project as the Default project and set it as the startup project</span></span>

* <span data-ttu-id="84c6c-148">`Add-Migration MyFirstMigration` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-148">Run `Add-Migration MyFirstMigration` to scaffold a migration to create the initial set of tables for your model.</span></span>

<span data-ttu-id="84c6c-149">アプリが実行されているデバイス上にデータベースを作成したいので、アプリケーションの起動時に保留中の移行をローカル データベースに適用するコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-149">Since we want the database to be created on the device that the app runs on, we will add some code to apply any pending migrations to the local database on application startup.</span></span> <span data-ttu-id="84c6c-150">これにより、アプリの初回実行時にローカル データベースの作成が自動的に管理されることになります。</span><span class="sxs-lookup"><span data-stu-id="84c6c-150">The first time that the app runs, this will take care of creating the local database for us.</span></span>

* <span data-ttu-id="84c6c-151">**ソリューション エクスプローラー**で **[App.xaml]** を右クリックし、**[コードの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-151">Right-click on **App.xaml** in **Solution Explorer** and select **View Code**</span></span>

* <span data-ttu-id="84c6c-152">強調表示された using をファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-152">Add the highlighted using to the start of the file</span></span>

* <span data-ttu-id="84c6c-153">保留中の移行を適用する強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-153">Add the highlighted code to apply any pending migrations</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/UWP.SQLite/App.xaml.cs?highlight=1,25-28)]

> [!TIP]  
> <span data-ttu-id="84c6c-154">モデルを今後変更する場合は、`Add-Migration` コマンドを使用して新しい移行をスキャフォールディングし、対応する変更をデータベースに適用できます。</span><span class="sxs-lookup"><span data-stu-id="84c6c-154">If you make future changes to your model, you can use the `Add-Migration` command to scaffold a new migration to apply the corresponding changes to the database.</span></span> <span data-ttu-id="84c6c-155">保留中の移行は、アプリケーションの起動時に各デバイス上のローカル データベースに適用されます。</span><span class="sxs-lookup"><span data-stu-id="84c6c-155">Any pending migrations will be applied to the local database on each device when the application starts.</span></span>
>
><span data-ttu-id="84c6c-156">EF はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-156">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>

## <a name="use-your-model"></a><span data-ttu-id="84c6c-157">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="84c6c-157">Use your model</span></span>

<span data-ttu-id="84c6c-158">モデルを使用してデータ アクセスを実行できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="84c6c-158">You can now use your model to perform data access.</span></span>

* <span data-ttu-id="84c6c-159">*MainPage.xaml* を開きます。</span><span class="sxs-lookup"><span data-stu-id="84c6c-159">Open *MainPage.xaml*</span></span>

* <span data-ttu-id="84c6c-160">以下の強調表示されたページ読み込みハンドラーと UI コンテンツを追加します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-160">Add the page load handler and UI content highlighted below</span></span>

[!code-xml[Main](../../../../samples/core/GetStarted/UWP/UWP.SQLite/MainPage.xaml?highlight=9,11-23)]

<span data-ttu-id="84c6c-161">ここで、UI とデータベースを接続するためのコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-161">Now we'll add code to wire up the UI with the database</span></span>

* <span data-ttu-id="84c6c-162">**ソリューション エクスプローラー**で **[MainPage.xaml]** を右クリックし、**[コードの表示]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-162">Right-click **MainPage.xaml** in **Solution Explorer** and select **View Code**</span></span>

* <span data-ttu-id="84c6c-163">次の一覧の強調表示されたコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="84c6c-163">Add the highlighted code from the following listing</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/UWP/UWP.SQLite/MainPage.xaml.cs?highlight=30-48)]

<span data-ttu-id="84c6c-164">アプリケーションを実行して動作を確認できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="84c6c-164">You can now run the application to see it in action.</span></span>

* <span data-ttu-id="84c6c-165">[デバッグ] > [デバッグなしで開始]</span><span class="sxs-lookup"><span data-stu-id="84c6c-165">Debug > Start Without Debugging</span></span>

* <span data-ttu-id="84c6c-166">アプリケーションがビルドされ、起動されます。</span><span class="sxs-lookup"><span data-stu-id="84c6c-166">The application will build and launch</span></span>

* <span data-ttu-id="84c6c-167">URL を入力して **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="84c6c-167">Enter a URL and click the **Add** button</span></span>

![image](_static/create.png)

![image](_static/list.png)

## <a name="next-steps"></a><span data-ttu-id="84c6c-170">次のステップ</span><span class="sxs-lookup"><span data-stu-id="84c6c-170">Next steps</span></span>

> [!TIP]
> <span data-ttu-id="84c6c-171">エンティティ型に [`INotifyPropertyChanged`](https://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanged.aspx)、[`INotifyPropertyChanging`](https://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanging.aspx)、[`INotifyCollectionChanged`](https://msdn.microsoft.com/en-us/library/system.collections.specialized.inotifycollectionchanged.aspx) を実装し、`ChangeTrackingStrategy.ChangingAndChangedNotifications` を使用することで、`SaveChanges()` のパフォーマンスを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="84c6c-171">`SaveChanges()` performance can be improved by implementing [`INotifyPropertyChanged`](https://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanged.aspx), [`INotifyPropertyChanging`](https://msdn.microsoft.com/en-us/library/system.componentmodel.inotifypropertychanging.aspx), [`INotifyCollectionChanged`](https://msdn.microsoft.com/en-us/library/system.collections.specialized.inotifycollectionchanged.aspx) in your entity types and using `ChangeTrackingStrategy.ChangingAndChangedNotifications`.</span></span>

<span data-ttu-id="84c6c-172">さあ、できました!</span><span class="sxs-lookup"><span data-stu-id="84c6c-172">Tada!</span></span> <span data-ttu-id="84c6c-173">Entity Framework を実行する簡単な UWP アプリが完成しました。</span><span class="sxs-lookup"><span data-stu-id="84c6c-173">You now have a simple UWP app running Entity Framework.</span></span>

<span data-ttu-id="84c6c-174">Entity Framework の機能の詳細については、このドキュメントの他の記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="84c6c-174">Check out other articles in this documentation to learn more about Entity Framework's features.</span></span>
