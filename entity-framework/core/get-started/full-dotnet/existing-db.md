---
title: .NET Framework - 既存のデータベース - EF Core の概要
author: rowanmiller
ms.date: 08/06/2018
ms.assetid: a29a3d97-b2d8-4d33-9475-40ac67b3b2c6
uid: core/get-started/full-dotnet/existing-db
ms.openlocfilehash: edcdc0b76394c4d604cf43fc170424e474532b17
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993419"
---
# <a name="getting-started-with-ef-core-on-net-framework-with-an-existing-database"></a><span data-ttu-id="74b11-102">既存のデータベースを使用した .NET Framework での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="74b11-102">Getting started with EF Core on .NET Framework with an Existing Database</span></span>

<span data-ttu-id="74b11-103">このチュートリアルでは、Entity Framework を使用して Microsoft SQL Server データベースに対して基本データ アクセスを実行するコンソール アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="74b11-103">In this tutorial, you build a console application that performs basic data access against a Microsoft SQL Server database using Entity Framework.</span></span> <span data-ttu-id="74b11-104">既存のデータベースのリバース エンジニアリングによって Entity Framework モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="74b11-104">You create an Entity Framework model by reverse engineering an existing database.</span></span>

<span data-ttu-id="74b11-105">[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb)。</span><span class="sxs-lookup"><span data-stu-id="74b11-105">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="74b11-106">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="74b11-106">Prerequisites</span></span>

* [<span data-ttu-id="74b11-107">Visual Studio 2017 バージョン 15.7 以降</span><span class="sxs-lookup"><span data-stu-id="74b11-107">Visual Studio 2017 version 15.7 or later</span></span>](https://www.visualstudio.com/downloads/)

## <a name="create-blogging-database"></a><span data-ttu-id="74b11-108">Blogging データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="74b11-108">Create Blogging database</span></span>

<span data-ttu-id="74b11-109">このチュートリアルでは、LocalDb インスタンス上の **Blogging** データベースを、既存のデータベースとして使用します。</span><span class="sxs-lookup"><span data-stu-id="74b11-109">This tutorial uses a **Blogging** database on the LocalDb instance as the existing database.</span></span> <span data-ttu-id="74b11-110">**Blogging** データベースを、別のチュートリアルの一部として既に作成している場合は、次の手順を省略してください。</span><span class="sxs-lookup"><span data-stu-id="74b11-110">If you have already created the **Blogging** database as part of another tutorial, skip these steps.</span></span>

* <span data-ttu-id="74b11-111">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="74b11-111">Open Visual Studio</span></span>

* <span data-ttu-id="74b11-112">**[ツール] > [データベースへの接続]**</span><span class="sxs-lookup"><span data-stu-id="74b11-112">**Tools > Connect to Database...**</span></span>

* <span data-ttu-id="74b11-113">**[Microsoft SQL Server]** を選択して **[続行]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="74b11-113">Select **Microsoft SQL Server** and click **Continue**</span></span>

* <span data-ttu-id="74b11-114">**[サーバー名]** に「**(localdb) \mssqllocaldb**」を入力します。</span><span class="sxs-lookup"><span data-stu-id="74b11-114">Enter **(localdb)\mssqllocaldb** as the **Server Name**</span></span>

* <span data-ttu-id="74b11-115">**[データベース名]** に「**master**」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="74b11-115">Enter **master** as the **Database Name** and click **OK**</span></span>

* <span data-ttu-id="74b11-116">**[サーバー エクスプローラー]** 内の **[データ接続]** の下に、master データベースが表示されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="74b11-116">The master database is now displayed under **Data Connections** in **Server Explorer**</span></span>

* <span data-ttu-id="74b11-117">**[サーバー エクスプローラー]** でこのデータベースを右クリックし、**[新しいクエリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="74b11-117">Right-click on the database in **Server Explorer** and select **New Query**</span></span>

* <span data-ttu-id="74b11-118">以下に表示されているスクリプトを、クエリ エディターにコピーします。</span><span class="sxs-lookup"><span data-stu-id="74b11-118">Copy the script listed below into the query editor</span></span>

* <span data-ttu-id="74b11-119">クエリ エディターを右クリックし、**[実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="74b11-119">Right-click on the query editor and select **Execute**</span></span>

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a><span data-ttu-id="74b11-120">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="74b11-120">Create a new project</span></span>

* <span data-ttu-id="74b11-121">Visual Studio 2017 を開きます</span><span class="sxs-lookup"><span data-stu-id="74b11-121">Open Visual Studio 2017</span></span>

* <span data-ttu-id="74b11-122">**[ファイル] > [新規] > [プロジェクト]**</span><span class="sxs-lookup"><span data-stu-id="74b11-122">**File > New > Project...**</span></span>

* <span data-ttu-id="74b11-123">左側のメニューから **[インストール済み] > [Visual C#] > [Windows デスクトップ]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="74b11-123">From the left menu select **Installed > Visual C# > Windows Desktop**</span></span>

* <span data-ttu-id="74b11-124">**[コンソール アプリ (.NET Framework)**] プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="74b11-124">Select the **Console App (.NET Framework)** project template</span></span>

* <span data-ttu-id="74b11-125">プロジェクトの対象が **.NET Framework 4.6.1** 以降であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="74b11-125">Make sure that the project targets **.NET Framework 4.6.1** or later</span></span>

* <span data-ttu-id="74b11-126">プロジェクトに *ConsoleApp.ExistingDb* という名前を付けて **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="74b11-126">Name the project *ConsoleApp.ExistingDb* and click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="74b11-127">Entity Framework をインストールする</span><span class="sxs-lookup"><span data-stu-id="74b11-127">Install Entity Framework</span></span>

<span data-ttu-id="74b11-128">EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="74b11-128">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="74b11-129">このチュートリアルでは、SQL Server を使用します。</span><span class="sxs-lookup"><span data-stu-id="74b11-129">This tutorial uses SQL Server.</span></span> <span data-ttu-id="74b11-130">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="74b11-130">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="74b11-131">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="74b11-131">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="74b11-132">`Install-Package Microsoft.EntityFrameworkCore.SqlServer` を実行します。</span><span class="sxs-lookup"><span data-stu-id="74b11-132">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="74b11-133">次の手順では、いくつかの Entity Framework ツールを使用して、データベースをリバース エンジニアリングします。</span><span class="sxs-lookup"><span data-stu-id="74b11-133">In the next step, you use some Entity Framework Tools to reverse engineer the database.</span></span> <span data-ttu-id="74b11-134">そのため、そのツールのパッケージもインストールしてください。</span><span class="sxs-lookup"><span data-stu-id="74b11-134">So install the tools package as well.</span></span>

* <span data-ttu-id="74b11-135">`Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。</span><span class="sxs-lookup"><span data-stu-id="74b11-135">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

## <a name="reverse-engineer-the-model"></a><span data-ttu-id="74b11-136">モデルのリバース エンジニアリングを行う</span><span class="sxs-lookup"><span data-stu-id="74b11-136">Reverse engineer the model</span></span>

<span data-ttu-id="74b11-137">ここで、既存のデータベースに基づいて、EF モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="74b11-137">Now it's time to create the EF model based on an existing database.</span></span>

* <span data-ttu-id="74b11-138">**[ツール] –> [NuGet パッケージ マネージャー] –> [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="74b11-138">**Tools –> NuGet Package Manager –> Package Manager Console**</span></span>

* <span data-ttu-id="74b11-139">既存のデータベースからモデルを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="74b11-139">Run the following command to create a model from the existing database</span></span>

  ``` powershell
  Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
  ```

> [!TIP]  
> <span data-ttu-id="74b11-140">上記のコマンドに引数 `-Tables` を追加すると、エンティティを生成するためのテーブルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="74b11-140">You can specify the tables to generate entities for by adding the `-Tables` argument to the command above.</span></span> <span data-ttu-id="74b11-141">たとえば、`-Tables Blog,Post` のようにします。</span><span class="sxs-lookup"><span data-stu-id="74b11-141">For example, `-Tables Blog,Post`.</span></span>

<span data-ttu-id="74b11-142">リバース エンジニアリングのプロセスにより、既存のデータベースのスキーマに基づいてエンティティ クラス (`Blog` および `Post`) と派生コンテキスト (`BloggingContext`) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="74b11-142">The reverse engineer process created entity classes (`Blog` and `Post`) and a derived context (`BloggingContext`) based on the schema of the existing database.</span></span>

<span data-ttu-id="74b11-143">エンティティ クラスはクエリを実行し保存するデータを表す、単純な C# オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="74b11-143">The entity classes are simple C# objects that represent the data you will be querying and saving.</span></span> <span data-ttu-id="74b11-144">こちらが、`Blog` と `Post` のエンティティ クラスです。</span><span class="sxs-lookup"><span data-stu-id="74b11-144">Here are the `Blog` and `Post` entity classes:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Blog.cs)]

[!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Post.cs)]

> [!TIP]  
> <span data-ttu-id="74b11-145">遅延読み込みを有効にするため、ナビゲーション プロパティ `virtual` を作成することができます (Blog.Post および Post.Blog)。</span><span class="sxs-lookup"><span data-stu-id="74b11-145">To enable lazy loading, you can make navigation properties `virtual` (Blog.Post and Post.Blog).</span></span>

<span data-ttu-id="74b11-146">このコンテキストはデータベースとのセッションを表します。</span><span class="sxs-lookup"><span data-stu-id="74b11-146">The context represents a session with the database.</span></span> <span data-ttu-id="74b11-147">エンティティ クラスのインスタンスのクエリと保存に使用できるメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="74b11-147">It has methods that you can use to query and save instances of the entity classes.</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/BloggingContext.cs)]

## <a name="use-the-model"></a><span data-ttu-id="74b11-148">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="74b11-148">Use the model</span></span>

<span data-ttu-id="74b11-149">モデルを使用してデータ アクセスを実行できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="74b11-149">You can now use the model to perform data access.</span></span>

* <span data-ttu-id="74b11-150">*Program.cs* を開きます。</span><span class="sxs-lookup"><span data-stu-id="74b11-150">Open *Program.cs*</span></span>

* <span data-ttu-id="74b11-151">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="74b11-151">Replace the contents of the file with the following code</span></span>

  [!code-csharp[Main](../../../../samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Program.cs)] 

* <span data-ttu-id="74b11-152">[デバッグ] > [デバッグなしで開始]</span><span class="sxs-lookup"><span data-stu-id="74b11-152">Debug > Start Without Debugging</span></span>

  <span data-ttu-id="74b11-153">1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに出力されます。</span><span class="sxs-lookup"><span data-stu-id="74b11-153">You see that one blog is saved to the database and then the details of all blogs are printed to the console.</span></span>

  ![イメージ](_static/output-existing-db.png)

## <a name="additional-resources"></a><span data-ttu-id="74b11-155">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="74b11-155">Additional Resources</span></span>

* [<span data-ttu-id="74b11-156">新しいデータベースを使用した .NET Framework での EF Core</span><span class="sxs-lookup"><span data-stu-id="74b11-156">EF Core on .NET Framework with a new database</span></span>](xref:core/get-started/full-dotnet/new-db)
* <span data-ttu-id="74b11-157">[新しいデータベース SQLite を使用した .NET core での EF Core](xref:core/get-started/netcore/new-db-sqlite) - クロスプラットフォーム コンソールでの EF のチュートリアル。</span><span class="sxs-lookup"><span data-stu-id="74b11-157">[EF Core on .NET Core with a new database - SQLite](xref:core/get-started/netcore/new-db-sqlite) -  a cross-platform console EF tutorial.</span></span>
