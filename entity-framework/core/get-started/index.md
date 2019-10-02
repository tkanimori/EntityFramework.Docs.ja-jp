---
title: 概要 - EF Core
author: rick-anderson
ms.date: 09/17/2019
ms.assetid: 3c88427c-20c6-42ec-a736-22d3eccd5071
uid: core/get-started/index
ms.openlocfilehash: fca1b532b34e20aeea1968939af96c692d60d738
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813604"
---
# <a name="getting-started-with-ef-core"></a><span data-ttu-id="a1ca3-102">EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="a1ca3-102">Getting Started with EF Core</span></span>

<span data-ttu-id="a1ca3-103">このチュートリアルでは、Entity Framework Core を使用して SQLite データベースに対してデータ アクセスを実行する .NET Core コンソール アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-103">In this tutorial, you create a .NET Core console app that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="a1ca3-104">このチュートリアルは、Windows 上の Visual Studio または Windows、macOS または Linux. 上の .NET Core CLI を対象としています。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-104">You can follow the tutorial by using Visual Studio on Windows, or by using the .NET Core CLI on Windows, macOS, or Linux.</span></span>

<span data-ttu-id="a1ca3-105">[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted)。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-105">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a1ca3-106">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="a1ca3-106">Prerequisites</span></span>

<span data-ttu-id="a1ca3-107">以下のソフトウェアをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-107">Install the following software:</span></span>

## <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a1ca3-108">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a1ca3-108">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="a1ca3-109">[.NET Core 3.0 SDK](https://www.microsoft.com/net/download/core)</span><span class="sxs-lookup"><span data-stu-id="a1ca3-109">[.NET Core 3.0 SDK](https://www.microsoft.com/net/download/core).</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a1ca3-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1ca3-110">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a1ca3-111">次のワークロードでは、[Visual Studio 2019 バージョン 16.3 以降](https://www.visualstudio.com/downloads/):</span><span class="sxs-lookup"><span data-stu-id="a1ca3-111">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this  workload:</span></span>
  * <span data-ttu-id="a1ca3-112">**.NET Core クロスプラットフォームの開発** ( **[他のツールセット]** の下)</span><span class="sxs-lookup"><span data-stu-id="a1ca3-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>

---

## <a name="create-a-new-project"></a><span data-ttu-id="a1ca3-113">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="a1ca3-113">Create a new project</span></span>

## <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a1ca3-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a1ca3-114">.NET Core CLI</span></span>](#tab/netcore-cli)

``` Console
dotnet new console -o EFGetStarted
cd EFGetStarted
```

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a1ca3-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1ca3-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a1ca3-116">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="a1ca3-116">Open Visual Studio</span></span>
* <span data-ttu-id="a1ca3-117">**[新しいプロジェクトの作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-117">Click **Create a new project**</span></span>
* <span data-ttu-id="a1ca3-118">**[C#]** タグを持つ **[コンソール アプリ (.NET Core)]** を選択し、 **[次へ]** を選択します</span><span class="sxs-lookup"><span data-stu-id="a1ca3-118">Select **Console App (.NET Core)** with the **C#** tag and click **Next**</span></span>
* <span data-ttu-id="a1ca3-119">名前に「**EFGetStarted**」を入力して **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-119">Enter **EFGetStarted** for the name and click **Create**</span></span>

---

## <a name="install-entity-framework-core"></a><span data-ttu-id="a1ca3-120">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="a1ca3-120">Install Entity Framework Core</span></span>

<span data-ttu-id="a1ca3-121">EF Core をインストールするには、対象となる EF Core データベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-121">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="a1ca3-122">このチュートリアルでは、.NET Core がサポートしているすべてのプラットフォームで実行できるため、SQLite を使用しています。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-122">This tutorial uses SQLite because it runs on all platforms that .NET Core supports.</span></span> <span data-ttu-id="a1ca3-123">使用可能なプロバイダーの一覧については、「[Database Providers](../providers/index.md)」 (データベース プロバイダー) をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-123">For a list of available providers, see [Database Providers](../providers/index.md).</span></span>

## <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a1ca3-124">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a1ca3-124">.NET Core CLI</span></span>](#tab/netcore-cli)

``` Console
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
```

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a1ca3-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1ca3-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a1ca3-126">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="a1ca3-126">**Tools > NuGet Package Manager > Package Manager Console**</span></span>
* <span data-ttu-id="a1ca3-127">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-127">Run the following commands:</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Sqlite
  ```

<span data-ttu-id="a1ca3-128">ヒント :プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択してパッケージをインストールすることもできます。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-128">Tip: You can also install packages by right-clicking on the project and selecting **Manage NuGet Packages**</span></span>

---

## <a name="create-the-model"></a><span data-ttu-id="a1ca3-129">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="a1ca3-129">Create the model</span></span>

<span data-ttu-id="a1ca3-130">モデルを構成するコンテキスト クラスおよびエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-130">Define a context class and entity classes that make up the model.</span></span>

## <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a1ca3-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a1ca3-131">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="a1ca3-132">以下のコードを使用して、プロジェクト ディレクトリで **Model.cs** を作成します。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-132">In the project directory create **Model.cs** with the following code</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a1ca3-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1ca3-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a1ca3-134">プロジェクト名を右クリックし、 **[追加]、[クラス]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-134">Right-click on the project and select **Add > Class**</span></span>
* <span data-ttu-id="a1ca3-135">名前に「**Model.cs**」を入力して **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-135">Enter **Model.cs** as the name and click **Add**</span></span>
* <span data-ttu-id="a1ca3-136">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-136">Replace the contents of the file with the following code</span></span>

---

[!code-csharp[Main](../../../samples/core/GetStarted/Model.cs)]

<span data-ttu-id="a1ca3-137">EF Core では、既存のデータベースからモデルを[リバース エンジニアリング](../managing-schemas/scaffolding.md)することもできます。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-137">EF Core can also [reverse engineer](../managing-schemas/scaffolding.md) a model from an existing database.</span></span>

<span data-ttu-id="a1ca3-138">ヒント :実際のアプリでは、クラスはそれぞれ別々のファイルに記述し、[接続文字列](../miscellaneous/connection-strings.md)は構成ファイルまたは環境変数に記述します。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-138">Tip: In a real app, you put each class in a separate file and put the [connection string](../miscellaneous/connection-strings.md) in a configuration file or environment variable.</span></span> <span data-ttu-id="a1ca3-139">チュートリアルをわかりやすくするために、すべてを 1 つのファイルに記述しています。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-139">To keep the tutorial simple, everything is contained in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="a1ca3-140">データベースの作成</span><span class="sxs-lookup"><span data-stu-id="a1ca3-140">Create the database</span></span>

<span data-ttu-id="a1ca3-141">次の手順では、[移行](xref:core/managing-schemas/migrations/index)を使用し、データベースを作成しています。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-141">The following steps use [migrations](xref:core/managing-schemas/migrations/index) to create a database.</span></span>

## <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a1ca3-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a1ca3-142">.NET Core CLI</span></span>](#tab/netcore-cli)

* <span data-ttu-id="a1ca3-143">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-143">Run the following commands:</span></span>

  ``` Console
  dotnet tool install --global dotnet-ef
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

  <span data-ttu-id="a1ca3-144">これにより、プロジェクトでコマンドを実行するために必要な [dotnet ef](../miscellaneous/cli/dotnet.md) と設計パッケージがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-144">This installs [dotnet ef](../miscellaneous/cli/dotnet.md) and the design package which is required to run the command on a project.</span></span> <span data-ttu-id="a1ca3-145">この `migrations` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-145">The `migrations` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="a1ca3-146">`database update` コマンドではデータベースが作成され、それに新しい移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-146">The `database update` command creates the database and applies the new migration to it.</span></span>

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a1ca3-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1ca3-147">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a1ca3-148">**パッケージ マネージャー コンソール**で、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-148">Run the following commands in **Package Manager Console**</span></span>

  ``` PowerShell
  Install-Package Microsoft.EntityFrameworkCore.Tools
  Add-Migration InitialCreate
  Update-Database
  ```

  <span data-ttu-id="a1ca3-149">これにより [EF Core 用の PMC ツール](../miscellaneous/cli/powershell.md)がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-149">This installs the [PMC tools for EF Core](../miscellaneous/cli/powershell.md).</span></span> <span data-ttu-id="a1ca3-150">この `Add-Migration` では、モデルの最初のテーブル セットを作成する移行がスキャフォールディングされます。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-150">The `Add-Migration` command scaffolds a migration to create the initial set of tables for the model.</span></span> <span data-ttu-id="a1ca3-151">`Update-Database` コマンドではデータベースが作成され、それに新しい移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-151">The `Update-Database` command creates the database and applies the new migration to it.</span></span>

---

## <a name="create-read-update--delete"></a><span data-ttu-id="a1ca3-152">作成、読み取り、更新、および削除</span><span class="sxs-lookup"><span data-stu-id="a1ca3-152">Create, read, update & delete</span></span>

* <span data-ttu-id="a1ca3-153">*Program.cs* を開き、内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-153">Open *Program.cs* and replace the contents with the following code:</span></span>

  [!code-csharp[Main](../../../samples/core/GetStarted/Program.cs)]

## <a name="run-the-app"></a><span data-ttu-id="a1ca3-154">アプリを実行する</span><span class="sxs-lookup"><span data-stu-id="a1ca3-154">Run the app</span></span>

## <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a1ca3-155">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a1ca3-155">.NET Core CLI</span></span>](#tab/netcore-cli)

``` Console
dotnet run
```

## <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a1ca3-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a1ca3-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a1ca3-157">Visual Studio で、.NET Core コンソール アプリの実行時に使用される作業ディレクトリには一貫性がありません。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-157">Visual Studio uses an inconsistent working directory when running .NET Core console apps.</span></span> <span data-ttu-id="a1ca3-158">([dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619) を参照)。これにより、次の例外がスローされます: "*そのようなテーブルはありません:Blogs*"。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-158">(see [dotnet/project-system#3619](https://github.com/dotnet/project-system/issues/3619)) This results in an exception being thrown: *no such table: Blogs*.</span></span> <span data-ttu-id="a1ca3-159">作業ディレクトリを更新するには:</span><span class="sxs-lookup"><span data-stu-id="a1ca3-159">To update the working directory:</span></span>

* <span data-ttu-id="a1ca3-160">プロジェクトを右クリックし、 **[プロジェクト ファイルの編集]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-160">Right-click on the project and select **Edit Project File**</span></span>
* <span data-ttu-id="a1ca3-161">*[TargetFramework]* プロパティの直下に、次を追加します。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-161">Just below the *TargetFramework* property, add the following:</span></span>

  ``` XML
  <StartWorkingDirectory>$(MSBuildProjectDirectory)</StartWorkingDirectory>
  ```

* <span data-ttu-id="a1ca3-162">ファイルを保存します。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-162">Save the file</span></span>

<span data-ttu-id="a1ca3-163">これで次のように、そのアプリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="a1ca3-163">Now you can run the app:</span></span>

* <span data-ttu-id="a1ca3-164">**[デバッグ] > [デバッグなしで開始]**</span><span class="sxs-lookup"><span data-stu-id="a1ca3-164">**Debug > Start Without Debugging**</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="a1ca3-165">次の手順</span><span class="sxs-lookup"><span data-stu-id="a1ca3-165">Next steps</span></span>

* <span data-ttu-id="a1ca3-166">Web アプリでの EF Core の使用には、[ASP.NET Core のチュートリアル](/aspnet/core/data/ef-rp/intro)のページを参照してください</span><span class="sxs-lookup"><span data-stu-id="a1ca3-166">Follow the [ASP.NET Core Tutorial](/aspnet/core/data/ef-rp/intro) to use EF Core in a web app</span></span>
* <span data-ttu-id="a1ca3-167">[LINQ クエリ式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)について参照してください</span><span class="sxs-lookup"><span data-stu-id="a1ca3-167">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="a1ca3-168">[required](xref:core/modeling/required-optional) や [maximum length](xref:core/modeling/max-length) などを指定し、[モデルを構成](xref:core/modeling/index)します</span><span class="sxs-lookup"><span data-stu-id="a1ca3-168">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/required-optional) and [maximum length](xref:core/modeling/max-length)</span></span>
* <span data-ttu-id="a1ca3-169">モデルの変更後のデータベース スキーマの更新に、[Migrations](xref:core/managing-schemas/migrations/index) を使用します</span><span class="sxs-lookup"><span data-stu-id="a1ca3-169">Use [Migrations](xref:core/managing-schemas/migrations/index) to update the database schema after changing your model</span></span>
