---
title: 概要 - EF Core
description: Entity Framework Core の概要チュートリアル
author: rick-anderson
ms.date: 09/17/2019
uid: core/get-started/index
ms.openlocfilehash: 9f0bb1eb99cb7f4cb7542c444ad86480917bdd0f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071980"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="f1fc6-103">EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="f1fc6-103">Getting Started with EF Core</span></span>

<span data-ttu-id="f1fc6-104">このチュートリアルでは、Entity Framework Core を使用して SQLite データベースに対してデータ アクセスを実行する .NET Core コンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-104">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="f1fc6-105">このチュートリアルは、Windows 上の Visual Studio または Windows、macOS または Linux. 上の .NET Core CLI を対象としています。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-105">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="f1fc6-106">[この記事のサンプルは GitHub で確認してください](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted)。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-106">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f1fc6-107">前提条件</span><span class="sxs-lookup"><span data-stu-id="f1fc6-107">Prerequisites</span></span>

<span data-ttu-id="f1fc6-108">次のソフトウェアをインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-108">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f1fc6-109">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f1fc6-109">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="f1fc6-110">[.NET Core SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-110">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="f1fc6-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1fc6-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f1fc6-112">次のワークロードでは、[Visual Studio 2019 バージョン 16.3 以降](https://www.visualstudio.com/downloads/):</span><span class="sxs-lookup"><span data-stu-id="f1fc6-112">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="f1fc6-113">**.NET Core クロスプラットフォームの開発** ( **[他のツールセット]** の下)</span><span class="sxs-lookup"><span data-stu-id="f1fc6-113">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="f1fc6-114">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="f1fc6-114">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f1fc6-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f1fc6-115">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="f1fc6-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1fc6-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f1fc6-117">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="f1fc6-117">Open Visual Studio</span></span>
* <span data-ttu-id="f1fc6-118">**[新しいプロジェクトの作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-118">Click **Create a new project**</span></span>
* <span data-ttu-id="f1fc6-119">**[C#]** タグを持つ **[コンソール アプリ (.NET Core)]** を選択し、 **[次へ]** を選択します</span><span class="sxs-lookup"><span data-stu-id="f1fc6-119">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="f1fc6-120">名前に「**EFGetStarted**」を入力して **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-120">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="f1fc6-121">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="f1fc6-121">Install Entity Framework Core</span></span>

<span data-ttu-id="f1fc6-122">EF Core をインストールするには、対象となる EF Core データベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-122">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="f1fc6-123">このチュートリアルでは、.NET Core がサポートしているすべてのプラットフォームで実行できるため、SQLite を使用しています。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-123">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="f1fc6-124">使用可能なプロバイダーの一覧については、「[Database Providers](xref:core/providers/index)」 (データベース プロバイダー) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-124">For a list of available providers, see [Database Providers](xref:core/providers/index).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f1fc6-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f1fc6-125">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="f1fc6-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1fc6-126">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f1fc6-127">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="f1fc6-127">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="f1fc6-128">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-128">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="f1fc6-129">ヒント :プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択してパッケージをインストールすることもできます。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-129">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="f1fc6-130">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="f1fc6-130">Create the model</span></span>

<span data-ttu-id="f1fc6-131">モデルを構成するコンテキスト クラスおよびエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-131">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f1fc6-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f1fc6-132">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="f1fc6-133">以下のコードを使用して、プロジェクト ディレクトリで **Model.cs** を作成します</span><span class="sxs-lookup"><span data-stu-id="f1fc6-133">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="f1fc6-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1fc6-134">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f1fc6-135">プロジェクト名を右クリックし、 **[追加]、[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-135">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="f1fc6-136">名前に「**Model.cs**」を入力して **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-136">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="f1fc6-137">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-137">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="f1fc6-138">EF Core では、既存のデータベースからモデルを[リバース エンジニアリング](xref:core/managing-schemas/scaffolding)することもできます。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-138">EF Core can also [reverse engineer](xref:core/managing-schemas/scaffolding) a model from an existing database.</span></span>

<span data-ttu-id="f1fc6-139">ヒント :このアプリケーションでは、わかりやすくするために意図的に事をシンプルにしています。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-139">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="f1fc6-140">運用アプリケーションのコードに、[接続文字列](xref:core/miscellaneous/connection-strings) は格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-140">[Connection strings](xref:core/miscellaneous/connection-strings) should not be stored in the code for production applications.</span></span> <span data-ttu-id="f1fc6-141">また、各 C# クラスを独自のファイルに分割することが必要な場合もあります。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-141">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="f1fc6-142">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="f1fc6-142">Create the database</span></span>

<span data-ttu-id="f1fc6-143">次の手順では、[移行](xref:core/managing-schemas/migrations/index)を使用し、データベースを作成しています。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-143">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f1fc6-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f1fc6-144">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="f1fc6-145">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-145">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="f1fc6-146">これにより、プロジェクトでコマンドを実行するために必要な [dotnet ef](xref:core/miscellaneous/cli/dotnet) と設計パッケージがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-146">This installs [dotnet ef](xref:core/miscellaneous/cli/dotnet) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="f1fc6-147">この `migrations` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-147">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="f1fc6-148">`database update` コマンドではデータベースが作成され、それに新しい移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-148">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="f1fc6-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1fc6-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f1fc6-150">**パッケージ マネージャー コンソール (PMC)** で、次のコマンドを実行します</span><span class="sxs-lookup"><span data-stu-id="f1fc6-150">Run the following commands in **Package Manager Console (PMC)**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="f1fc6-151">これにより [EF Core 用の PMC ツール](xref:core/miscellaneous/cli/powershell)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-151">This installs the [PMC tools for EF Core](xref:core/miscellaneous/cli/powershell).</span></span> <span data-ttu-id="f1fc6-152">この `Add-Migration` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-152">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="f1fc6-153">`Update-Database` コマンドではデータベースが作成され、それに新しい移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-153">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="f1fc6-154">作成、読み取り、更新、および削除</span><span class="sxs-lookup"><span data-stu-id="f1fc6-154">Create, read, update & delete</span></span>

* <span data-ttu-id="f1fc6-155">*Program.cs* を開き、内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-155">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="f1fc6-156">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="f1fc6-156">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="f1fc6-157">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f1fc6-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="f1fc6-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f1fc6-158">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f1fc6-159">Visual Studio で、.NET Core コンソール アプリの実行時に使用される作業ディレクトリには一貫性がありません。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-159">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="f1fc6-160">([dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619) を参照)。これにより、次の例外がスローされます: "*そのようなテーブルはありません:Blogs*"。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-160">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="f1fc6-161">作業ディレクトリを更新するには:</span><span class="sxs-lookup"><span data-stu-id="f1fc6-161">To update the working directory:</span></span>

* <span data-ttu-id="f1fc6-162">プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-162">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="f1fc6-163">*[TargetFramework]* プロパティの直下に、次を追加します。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-163">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="f1fc6-164">ファイルを保存する</span><span class="sxs-lookup"><span data-stu-id="f1fc6-164">Save the file</span></span>

<span data-ttu-id="f1fc6-165">これで次のように、そのアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="f1fc6-165">Now you can run the app:</span></span>

* <span data-ttu-id="f1fc6-166">**[デバッグ] > [デバッグなしで開始]**</span><span class="sxs-lookup"><span data-stu-id="f1fc6-166">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="f1fc6-167">次のステップ</span><span class="sxs-lookup"><span data-stu-id="f1fc6-167">Next steps</span></span>

* <span data-ttu-id="f1fc6-168">Web アプリでの EF Core の使用には、[ASP.NET Core のチュートリアル](/aspnet/core/data/ef-rp/intro)のページを参照してください</span><span class="sxs-lookup"><span data-stu-id="f1fc6-168">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="f1fc6-169">[LINQ クエリ式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)について参照してください</span><span class="sxs-lookup"><span data-stu-id="f1fc6-169">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="f1fc6-170">[required](xref:core/modeling/entity-properties#required-and-optional-properties) や [maximum length](xref:core/modeling/entity-properties#maximum-length) などを指定し、[モデルを構成](xref:core/modeling/index)します</span><span class="sxs-lookup"><span data-stu-id="f1fc6-170">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="f1fc6-171">モデルの変更後のデータベース スキーマの更新に、[Migrations](xref:core/managing-schemas/migrations/index) を使用します</span><span class="sxs-lookup"><span data-stu-id="f1fc6-171">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
