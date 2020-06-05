---
title: 概要 - EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: b45a7e5010dd3ef37ea952aaf483847ae95ea4ef
ms.sourcegitcommit: 1f0f93c66b2b50e03fcbed90260e94faa0279c46
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2020
ms.locfileid: "84418946"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="9fda5-102">EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="9fda5-102">Getting Started with EF Core</span></span>

<span data-ttu-id="9fda5-103">このチュートリアルでは、Entity Framework Core を使用して SQLite データベースに対してデータ アクセスを実行する .NET Core コンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="9fda5-103">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="9fda5-104">このチュートリアルは、Windows 上の Visual Studio または Windows、macOS または Linux. 上の .NET Core CLI を対象としています。</span><span class="sxs-lookup"><span data-stu-id="9fda5-104">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="9fda5-105">[この記事のサンプルは GitHub で確認してください](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted)。</span><span class="sxs-lookup"><span data-stu-id="9fda5-105">[View this article's sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9fda5-106">前提条件</span><span class="sxs-lookup"><span data-stu-id="9fda5-106">Prerequisites</span></span>

<span data-ttu-id="9fda5-107">次のソフトウェアをインストールします。</span><span class="sxs-lookup"><span data-stu-id="9fda5-107">Install the following software:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="9fda5-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9fda5-108">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="9fda5-109">[.NET Core SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="9fda5-109">[.NET Core SDK](https://www.microsoft.com/net/download/core).</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="9fda5-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fda5-110">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fda5-111">次のワークロードでは、[Visual Studio 2019 バージョン 16.3 以降](https://www.visualstudio.com/downloads/):</span><span class="sxs-lookup"><span data-stu-id="9fda5-111">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="9fda5-112">**.NET Core クロスプラットフォーム** ( **[他のツールセット]** 以下)</span><span class="sxs-lookup"><span data-stu-id="9fda5-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="9fda5-113">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="9fda5-113">Create a new project</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="9fda5-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9fda5-114">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new console -o EFGetStarted
cd EFGetStarted
```

### <a name="visual-studio"></a>[<span data-ttu-id="9fda5-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fda5-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fda5-116">Visual Studio を開きます</span><span class="sxs-lookup"><span data-stu-id="9fda5-116">Open Visual Studio</span></span>
* <span data-ttu-id="9fda5-117">**[新しいプロジェクトの作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9fda5-117">Click **Create a new project**</span></span>
* <span data-ttu-id="9fda5-118">**[C#]** タグを持つ **[コンソール アプリ (.NET Core)]** を選択し、 **[次へ]** を選択します</span><span class="sxs-lookup"><span data-stu-id="9fda5-118">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="9fda5-119">名前に「**EFGetStarted**」を入力して **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9fda5-119">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="9fda5-120">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="9fda5-120">Install Entity Framework Core</span></span>

<span data-ttu-id="9fda5-121">EF Core をインストールするには、対象となる EF Core データベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="9fda5-121">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="9fda5-122">このチュートリアルでは、.NET Core がサポートしているすべてのプラットフォームで実行できるため、SQLite を使用しています。</span><span class="sxs-lookup"><span data-stu-id="9fda5-122">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="9fda5-123">使用可能なプロバイダーの一覧については、「[Database Providers](../providers/index.md)」 (データベース プロバイダー) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9fda5-123">For a list of available providers, see [Database Providers](../providers/index.md).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="9fda5-124">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9fda5-124">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

### <a name="visual-studio"></a>[<span data-ttu-id="9fda5-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fda5-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fda5-126">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="9fda5-126">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="9fda5-127">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9fda5-127">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="9fda5-128">ヒント:プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択してパッケージをインストールすることもできます。</span><span class="sxs-lookup"><span data-stu-id="9fda5-128">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="9fda5-129">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="9fda5-129">Create the model</span></span>

<span data-ttu-id="9fda5-130">モデルを構成するコンテキスト クラスおよびエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="9fda5-130">Define a context class and entity classes that make up the model.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="9fda5-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9fda5-131">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="9fda5-132">以下のコードを使用して、プロジェクト ディレクトリで **Model.cs** を作成します</span><span class="sxs-lookup"><span data-stu-id="9fda5-132">In the project directory, create **Model.cs** with the following code</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="9fda5-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fda5-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fda5-134">プロジェクト名を右クリックし、 **[追加]、[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9fda5-134">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="9fda5-135">名前に「**Model.cs**」を入力して **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9fda5-135">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="9fda5-136">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9fda5-136">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="9fda5-137">EF Core では、既存のデータベースからモデルを[リバース エンジニアリング](../managing-schemas/scaffolding.md)することもできます。</span><span class="sxs-lookup"><span data-stu-id="9fda5-137">EF Core can also [reverse engineer](../managing-schemas/scaffolding.md) a model from an existing database.</span></span>

<span data-ttu-id="9fda5-138">ヒント:このアプリケーションでは、わかりやすくするために意図的に事をシンプルにしています。</span><span class="sxs-lookup"><span data-stu-id="9fda5-138">Tip: This application intentionally keeps things simple for clarity.</span></span> <span data-ttu-id="9fda5-139">運用アプリケーションのコードに、[接続文字列](../miscellaneous/connection-strings.md) は格納しないでください。</span><span class="sxs-lookup"><span data-stu-id="9fda5-139">[Connection strings](../miscellaneous/connection-strings.md) should not be stored in the code for production applications.</span></span> <span data-ttu-id="9fda5-140">また、各 C# クラスを独自のファイルに分割することが必要な場合もあります。</span><span class="sxs-lookup"><span data-stu-id="9fda5-140">You may also want to split each C# class into its own file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="9fda5-141">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="9fda5-141">Create the database</span></span>

<span data-ttu-id="9fda5-142">次の手順では、[移行](xref:core/managing-schemas/migrations/index)を使用し、データベースを作成しています。</span><span class="sxs-lookup"><span data-stu-id="9fda5-142">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="9fda5-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9fda5-143">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="9fda5-144">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9fda5-144">Run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="9fda5-145">これにより、プロジェクトでコマンドを実行するために必要な [dotnet ef](../miscellaneous/cli/dotnet.md) と設計パッケージがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="9fda5-145">This installs [dotnet ef](../miscellaneous/cli/dotnet.md) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="9fda5-146">この `migrations` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="9fda5-146">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="9fda5-147">`database update` コマンドではデータベースが作成され、それに新しい移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="9fda5-147">The `database update` command creates the database and applies the new migration to it.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="9fda5-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fda5-148">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9fda5-149">**パッケージ マネージャー コンソール**で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9fda5-149">Run the following commands in **Package Manager Console**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="9fda5-150">これにより [EF Core 用の PMC ツール](../miscellaneous/cli/powershell.md)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="9fda5-150">This installs the [PMC tools for EF Core](../miscellaneous/cli/powershell.md).</span></span> <span data-ttu-id="9fda5-151">この `Add-Migration` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="9fda5-151">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="9fda5-152">`Update-Database` コマンドではデータベースが作成され、それに新しい移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="9fda5-152">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="9fda5-153">作成、読み取り、更新、および削除</span><span class="sxs-lookup"><span data-stu-id="9fda5-153">Create, read, update & delete</span></span>

* <span data-ttu-id="9fda5-154">*Program.cs* を開き、その内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9fda5-154">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="9fda5-155">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="9fda5-155">Run the app</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="9fda5-156">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9fda5-156">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet run
```

### <a name="visual-studio"></a>[<span data-ttu-id="9fda5-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9fda5-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9fda5-158">Visual Studio で、.NET Core コンソール アプリの実行時に使用される作業ディレクトリには一貫性がありません。</span><span class="sxs-lookup"><span data-stu-id="9fda5-158">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="9fda5-159">([dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619) を参照)。これにより、次の例外がスローされます: "*そのようなテーブルはありません:Blogs*"。</span><span class="sxs-lookup"><span data-stu-id="9fda5-159">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="9fda5-160">作業ディレクトリを更新するには:</span><span class="sxs-lookup"><span data-stu-id="9fda5-160">To update the working directory:</span></span>

* <span data-ttu-id="9fda5-161">プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9fda5-161">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="9fda5-162">*[TargetFramework]* プロパティの直下に、次を追加します。</span><span class="sxs-lookup"><span data-stu-id="9fda5-162">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="9fda5-163">ファイルを保存する</span><span class="sxs-lookup"><span data-stu-id="9fda5-163">Save the file</span></span>

<span data-ttu-id="9fda5-164">これで次のように、そのアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="9fda5-164">Now you can run the app:</span></span>

* <span data-ttu-id="9fda5-165">**[デバッグ] > [デバッグなしで開始]**</span><span class="sxs-lookup"><span data-stu-id="9fda5-165">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="9fda5-166">次のステップ</span><span class="sxs-lookup"><span data-stu-id="9fda5-166">Next steps</span></span>

* <span data-ttu-id="9fda5-167">Web アプリでの EF Core の使用には、[ASP.NET Core のチュートリアル](/aspnet/core/data/ef-rp/intro)のページを参照してください</span><span class="sxs-lookup"><span data-stu-id="9fda5-167">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="9fda5-168">[LINQ クエリ式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)について参照してください</span><span class="sxs-lookup"><span data-stu-id="9fda5-168">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="9fda5-169">[required](xref:core/modeling/entity-properties#required-and-optional-properties) や [maximum length](xref:core/modeling/entity-properties#maximum-length) などを指定し、[モデルを構成](xref:core/modeling/index)します</span><span class="sxs-lookup"><span data-stu-id="9fda5-169">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
* <span data-ttu-id="9fda5-170">モデルの変更後のデータベース スキーマの更新に、[Migrations](xref:core/managing-schemas/migrations/index) を使用します</span><span class="sxs-lookup"><span data-stu-id="9fda5-170">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
