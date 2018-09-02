---
title: .NET Framework - 新しいデータベース - EF Core の概要
author: rowanmiller
ms.date: 08/06/2018
ms.assetid: 52b69727-ded9-4a7b-b8d5-73f3acfbbad3
uid: core/get-started/full-dotnet/new-db
ms.openlocfilehash: 66d9011a5978fc3d8253a963bdb9df27848e9ff9
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997588"
---
# <a name="getting-started-with-ef-core-on-net-framework-with-a-new-database"></a><span data-ttu-id="2123f-102">新しいデータベースを使用した .NET Framework での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="2123f-102">Getting started with EF Core on .NET Framework with a New Database</span></span>

<span data-ttu-id="2123f-103">このチュートリアルでは、Entity Framework を使用して Microsoft SQL Server データベースに対して基本データ アクセスを実行するコンソール アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="2123f-103">In this tutorial, you build a console application that performs basic data access against a Microsoft SQL Server database using Entity Framework.</span></span> <span data-ttu-id="2123f-104">モデルからの移行によってデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="2123f-104">You use migrations to create the database from a model.</span></span>

<span data-ttu-id="2123f-105">[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.NewDb)。</span><span class="sxs-lookup"><span data-stu-id="2123f-105">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.NewDb).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2123f-106">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2123f-106">Prerequisites</span></span>

* [<span data-ttu-id="2123f-107">Visual Studio 2017 バージョン 15.7 以降</span><span class="sxs-lookup"><span data-stu-id="2123f-107">Visual Studio 2017 version 15.7 or later</span></span>](https://www.visualstudio.com/downloads/)

## <a name="create-a-new-project"></a><span data-ttu-id="2123f-108">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="2123f-108">Create a new project</span></span>

* <span data-ttu-id="2123f-109">Visual Studio 2017 を開きます</span><span class="sxs-lookup"><span data-stu-id="2123f-109">Open Visual Studio 2017</span></span>

* <span data-ttu-id="2123f-110">**[ファイル] > [新規] > [プロジェクト]**</span><span class="sxs-lookup"><span data-stu-id="2123f-110">**File > New > Project...**</span></span>

* <span data-ttu-id="2123f-111">左側のメニューから **[インストール済み] > [Visual C#] > [Windows デスクトップ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2123f-111">From the left menu select **Installed > Visual C# > Windows Desktop**</span></span>

* <span data-ttu-id="2123f-112">**[コンソール アプリ (.NET Framework)**] プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="2123f-112">Select the **Console App (.NET Framework)** project template</span></span>

* <span data-ttu-id="2123f-113">プロジェクトの対象が **.NET Framework 4.6.1** 以降であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2123f-113">Make sure that the project targets **.NET Framework 4.6.1** or later</span></span>

* <span data-ttu-id="2123f-114">プロジェクトに *ConsoleApp.NewDb* という名前を付けて **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2123f-114">Name the project *ConsoleApp.NewDb* and click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="2123f-115">Entity Framework をインストールする</span><span class="sxs-lookup"><span data-stu-id="2123f-115">Install Entity Framework</span></span>

<span data-ttu-id="2123f-116">EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2123f-116">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="2123f-117">このチュートリアルでは、SQL Server を使用します。</span><span class="sxs-lookup"><span data-stu-id="2123f-117">This tutorial uses SQL Server.</span></span> <span data-ttu-id="2123f-118">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2123f-118">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="2123f-119">[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]</span><span class="sxs-lookup"><span data-stu-id="2123f-119">Tools > NuGet Package Manager > Package Manager Console</span></span>

* <span data-ttu-id="2123f-120">`Install-Package Microsoft.EntityFrameworkCore.SqlServer` を実行します。</span><span class="sxs-lookup"><span data-stu-id="2123f-120">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="2123f-121">このチュートリアルの後半では、データベースのメンテナンスで複数の Entity Framework ツールも使用します。</span><span class="sxs-lookup"><span data-stu-id="2123f-121">Later in this tutorial you use some Entity Framework Tools to maintain the database.</span></span> <span data-ttu-id="2123f-122">そのため、そのツールのパッケージもインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="2123f-122">So install the tools package as well.</span></span>

* <span data-ttu-id="2123f-123">`Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。</span><span class="sxs-lookup"><span data-stu-id="2123f-123">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

## <a name="create-the-model"></a><span data-ttu-id="2123f-124">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="2123f-124">Create the model</span></span>

<span data-ttu-id="2123f-125">ここで、モデルを編成するコンテキストとエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="2123f-125">Now it's time to define a context and entity classes that make up the model.</span></span>

* <span data-ttu-id="2123f-126">**[プロジェクト] > [クラスの追加]**</span><span class="sxs-lookup"><span data-stu-id="2123f-126">**Project > Add Class...**</span></span>

* <span data-ttu-id="2123f-127">名前に「*Model.cs*」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2123f-127">Enter *Model.cs* as the name and click **OK**</span></span>

* <span data-ttu-id="2123f-128">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2123f-128">Replace the contents of the file with the following code</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Model.cs)] 

> [!TIP]  
> <span data-ttu-id="2123f-129">実際のアプリケーションでは、各クラスは別のファイルに、接続文字列は構成ファイルまたは環境変数にそれぞれ記述します。</span><span class="sxs-lookup"><span data-stu-id="2123f-129">In a real application you would put each class in a separate file and put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="2123f-130">わかりやすくするために、このチュートリアルではすべてを 1 つのコード ファイルに記述しています。</span><span class="sxs-lookup"><span data-stu-id="2123f-130">For the sake of simplicity, everything is in a single code file for this tutorial.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="2123f-131">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="2123f-131">Create the database</span></span>

<span data-ttu-id="2123f-132">これでモデルができたので、移行を利用してデータベースを作成できます。</span><span class="sxs-lookup"><span data-stu-id="2123f-132">Now that you have a model, you can use migrations to create a database.</span></span>

* <span data-ttu-id="2123f-133">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="2123f-133">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="2123f-134">`Add-Migration InitialCreate` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。</span><span class="sxs-lookup"><span data-stu-id="2123f-134">Run `Add-Migration InitialCreate` to scaffold a migration to create the initial set of tables for the model.</span></span>

* <span data-ttu-id="2123f-135">`Update-Database` を実行して、新しい移行をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="2123f-135">Run `Update-Database` to apply the new migration to the database.</span></span> <span data-ttu-id="2123f-136">データベースがまだ存在しないため、データベースが作成されてから移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="2123f-136">Because the database doesn't exist yet, it will be created before the migration is applied.</span></span>

> [!TIP]  
> <span data-ttu-id="2123f-137">モデルを変更する場合、`Add-Migration` コマンドを使用して新しい移行をスキャフォールディングし、対応するスキーマの変更をデータベースに対して行います。</span><span class="sxs-lookup"><span data-stu-id="2123f-137">If you make changes to the model, you can use the `Add-Migration` command to scaffold a new migration to make the corresponding schema changes to the database.</span></span> <span data-ttu-id="2123f-138">スキャフォールディング コードを確認 (および必要な変更) したら、`Update-Database` コマンドを使用してデータベースに変更を適用できます。</span><span class="sxs-lookup"><span data-stu-id="2123f-138">Once you have checked the scaffolded code (and made any required changes), you can use the `Update-Database` command to apply the changes to the database.</span></span>
>
> <span data-ttu-id="2123f-139">EF はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="2123f-139">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="2123f-140">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="2123f-140">Use the model</span></span>

<span data-ttu-id="2123f-141">モデルを使用してデータ アクセスを実行できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="2123f-141">You can now use the model to perform data access.</span></span>

* <span data-ttu-id="2123f-142">*Program.cs* を開きます。</span><span class="sxs-lookup"><span data-stu-id="2123f-142">Open *Program.cs*</span></span>

* <span data-ttu-id="2123f-143">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2123f-143">Replace the contents of the file with the following code</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Program.cs)]

* <span data-ttu-id="2123f-144">**[デバッグ] > [デバッグなしで開始]**</span><span class="sxs-lookup"><span data-stu-id="2123f-144">**Debug > Start Without Debugging**</span></span>

  <span data-ttu-id="2123f-145">1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに出力されます。</span><span class="sxs-lookup"><span data-stu-id="2123f-145">You see that one blog is saved to the database and then the details of all blogs are printed to the console.</span></span>

  ![イメージ](_static/output-new-db.png)

## <a name="additional-resources"></a><span data-ttu-id="2123f-147">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2123f-147">Additional Resources</span></span>

* [<span data-ttu-id="2123f-148">既存のデータベースを使用した .NET Framework での EF Core</span><span class="sxs-lookup"><span data-stu-id="2123f-148">EF Core on .NET Framework with an existing database</span></span>](xref:core/get-started/full-dotnet/existing-db)
* <span data-ttu-id="2123f-149">[新しいデータベース SQLite を使用した .NET core での EF Core](xref:core/get-started/netcore/new-db-sqlite) - クロスプラットフォーム コンソールでの EF のチュートリアル。</span><span class="sxs-lookup"><span data-stu-id="2123f-149">[EF Core on .NET Core with a new database - SQLite](xref:core/get-started/netcore/new-db-sqlite) -  a cross-platform console EF tutorial.</span></span>
