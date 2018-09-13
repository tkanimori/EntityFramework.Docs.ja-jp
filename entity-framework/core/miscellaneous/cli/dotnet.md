---
title: .NET core CLI - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/06/2017
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 3534336f1caeed96079b35c739d694a536919020
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489610"
---
<a name="ef-core-net-command-line-tools"></a><span data-ttu-id="f662c-102">EF Core .NET コマンド ライン ツール</span><span class="sxs-lookup"><span data-stu-id="f662c-102">EF Core .NET Command-line Tools</span></span>
===============================
<span data-ttu-id="f662c-103">Entity Framework Core .NET コマンド ライン ツール、クロス プラットフォーム拡張機能**dotnet**コマンドで、一部の[.NET Core SDK][2]します。</span><span class="sxs-lookup"><span data-stu-id="f662c-103">The Entity Framework Core .NET Command-line Tools are an extension to the cross-platform **dotnet** command, which is part of the [.NET Core SDK][2].</span></span>

> [!TIP]
> <span data-ttu-id="f662c-104">Visual Studio を使用しているかどうか、お勧めします[に優れた PMC ツール][ 1]代わりに、統合性を提供するためです。</span><span class="sxs-lookup"><span data-stu-id="f662c-104">If you're using Visual Studio, we recommend [the PMC Tools][1] instead since they provide a more integrated experience.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="f662c-105">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="f662c-105">Installing the tools</span></span>
--------------------
> [!NOTE]
> <span data-ttu-id="f662c-106">.NET Core SDK バージョン 2.1.300 新しいが含まれています**dotnet ef** EF Core 2.0 以降のバージョンと互換性のあるコマンド。</span><span class="sxs-lookup"><span data-stu-id="f662c-106">The .NET Core SDK version 2.1.300 and newer includes **dotnet ef** commands that are compatible with EF Core 2.0 and later versions.</span></span> <span data-ttu-id="f662c-107">したがって最近のバージョンの .NET Core SDK と EF Core ランタイムを使用している場合、インストールは必要なく、このセクションの残りの部分を無視することができます。</span><span class="sxs-lookup"><span data-stu-id="f662c-107">Therefore if you are using recent versions of the .NET Core SDK and the EF Core runtime, no installation is required and you can ignore the rest of this section.</span></span>
>
> <span data-ttu-id="f662c-108">一方で、 **dotnet ef**以降と .NET Core SDK バージョン 2.1.300 に格納されているツールは、EF Core のバージョン 1.0 および 1.1 と互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="f662c-108">On the other hand, the **dotnet ef** tool contained in .NET Core SDK version 2.1.300 and newer is not compatible with EF Core version 1.0 and 1.1.</span></span> <span data-ttu-id="f662c-109">2.1.200 のバージョンをインストールする前に、.NET Core SDK 2.1.300 をあるコンピューター上で以前のバージョンの EF Core を使用するプロジェクトを使用する、または新しいインストールもする必要がありますまたは SDK の古い変更してその以前のバージョンを使用するアプリケーションを構成して、 [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json)ファイル。</span><span class="sxs-lookup"><span data-stu-id="f662c-109">Before you can work with a project that uses these earlier versions of EF Core on a computer that has .NET Core SDK 2.1.300 or newer installed, you must also install version 2.1.200 or older of the SDK and configure the application to use that older version by modifying its [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json) file.</span></span> <span data-ttu-id="f662c-110">このファイルは通常、(プロジェクトの上に 1 つ) のソリューション ディレクトリにインクルードします。</span><span class="sxs-lookup"><span data-stu-id="f662c-110">This file is normally included in the solution directory (one above the project).</span></span> <span data-ttu-id="f662c-111">以下の installlation 命令を続行できます。</span><span class="sxs-lookup"><span data-stu-id="f662c-111">Then you can proceed with the installlation instruction below.</span></span>

<span data-ttu-id="f662c-112">.NET Core SDK の以前のバージョンには、次の手順を使用して EF Core .NET コマンド ライン ツールをインストールできます。</span><span class="sxs-lookup"><span data-stu-id="f662c-112">For previous versions of the .NET Core SDK, you can install the EF Core .NET Command-line Tools using these steps:</span></span>

1. <span data-ttu-id="f662c-113">プロジェクト ファイルを編集し、Microsoft.EntityFrameworkCore.Tools.DotNet を DotNetCliToolReference 項目 (下記参照) として追加</span><span class="sxs-lookup"><span data-stu-id="f662c-113">Edit the project file and add Microsoft.EntityFrameworkCore.Tools.DotNet as a DotNetCliToolReference item (See below)</span></span>
2. <span data-ttu-id="f662c-114">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="f662c-114">Run the following commands:</span></span>

       dotnet add package Microsoft.EntityFrameworkCore.Design
       dotnet restore

<span data-ttu-id="f662c-115">作成されたプロジェクトは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="f662c-115">The resulting project should look something like this:</span></span>

``` xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.0</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design"
                      Version="2.0.0"
                      PrivateAssets="All" />
  </ItemGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
                            Version="2.0.0" />
  </ItemGroup>
</Project>
```

> [!NOTE]
> <span data-ttu-id="f662c-116">パッケージ参照を`PrivateAssets="All"`開発時にのみ使用される一般的なパッケージに特に便利ですが、このプロジェクトを参照するプロジェクトに公開されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="f662c-116">A package reference with `PrivateAssets="All"` means it isn't exposed to projects that reference this project, which is especially useful for packages that are typically only used during development.</span></span>

<span data-ttu-id="f662c-117">右のすべてのプロセスが、コマンド プロンプトで次のコマンドを正常に実行できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="f662c-117">If you did everything right, you should be able to successfully run the following command in a command prompt.</span></span>

``` Console
dotnet ef
```

<a name="using-the-tools"></a><span data-ttu-id="f662c-118">ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="f662c-118">Using the tools</span></span>
---------------
<span data-ttu-id="f662c-119">コマンドを呼び出すたびにある 2 つのプロジェクト関係します。</span><span class="sxs-lookup"><span data-stu-id="f662c-119">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="f662c-120">ターゲット プロジェクトでは、ファイルが追加されます (削除される場合もあります)。</span><span class="sxs-lookup"><span data-stu-id="f662c-120">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="f662c-121">ターゲット プロジェクトが現在のディレクトリでプロジェクトの既定値を使用して変更することが、 <nobr> **`--project`** </nobr>オプション。</span><span class="sxs-lookup"><span data-stu-id="f662c-121">The target project defaults to the project in the current directory, but can be changed using the <nobr>**`--project`**</nobr> option.</span></span>

<span data-ttu-id="f662c-122">スタートアップ プロジェクトは、プロジェクトのコードを実行したとき、ツールによってエミュレートされるプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="f662c-122">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="f662c-123">またに既定値は、現在のディレクトリでプロジェクトを使用して変更できる、 **`--startup-project`** オプション。</span><span class="sxs-lookup"><span data-stu-id="f662c-123">It also defaults to the project in the current directory, but can be changed using the **`--startup-project`** option.</span></span>

> [!NOTE]
> <span data-ttu-id="f662c-124">たとえば、次のようなりますを持つ別のプロジェクトにインストールされている EF Core web アプリケーションのデータベースの更新: `dotnet ef database update --project {project-path}` (から、web アプリのディレクトリ)</span><span class="sxs-lookup"><span data-stu-id="f662c-124">For instance, updating the database of your web application that has EF Core installed in a different project would look like this: `dotnet ef database update --project {project-path}` (from your web app directory)</span></span>

<span data-ttu-id="f662c-125">一般的なオプション:</span><span class="sxs-lookup"><span data-stu-id="f662c-125">Common options:</span></span>

|    |                                  |                             |
|:---|:---------------------------------|:----------------------------|
|    | `--json`                           | <span data-ttu-id="f662c-126">JSON 出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="f662c-126">Show JSON output.</span></span>           |
| <span data-ttu-id="f662c-127">-c</span><span class="sxs-lookup"><span data-stu-id="f662c-127">-c</span></span> | `--context <DBCONTEXT>`           | <span data-ttu-id="f662c-128">使用する DbContext します。</span><span class="sxs-lookup"><span data-stu-id="f662c-128">The DbContext to use.</span></span>       |
| <span data-ttu-id="f662c-129">-p</span><span class="sxs-lookup"><span data-stu-id="f662c-129">-p</span></span> | `--project <PROJECT>`             | <span data-ttu-id="f662c-130">使用するプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="f662c-130">The project to use.</span></span>         |
| <span data-ttu-id="f662c-131">-s</span><span class="sxs-lookup"><span data-stu-id="f662c-131">-s</span></span> | `--startup-project <PROJECT>`     | <span data-ttu-id="f662c-132">使用するスタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="f662c-132">The startup project to use.</span></span> |
|    | `--framework <FRAMEWORK>`         | <span data-ttu-id="f662c-133">ターゲット フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="f662c-133">The target framework.</span></span>       |
|    | `--configuration <CONFIGURATION>` | <span data-ttu-id="f662c-134">使用する構成。</span><span class="sxs-lookup"><span data-stu-id="f662c-134">The configuration to use.</span></span>   |
|    | `--runtime <IDENTIFIER>`          | <span data-ttu-id="f662c-135">使用するランタイム。</span><span class="sxs-lookup"><span data-stu-id="f662c-135">The runtime to use.</span></span>         |
| <span data-ttu-id="f662c-136">-h</span><span class="sxs-lookup"><span data-stu-id="f662c-136">-h</span></span> | `--help`                           | <span data-ttu-id="f662c-137">ヘルプ情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f662c-137">Show help information.</span></span>      |
| <span data-ttu-id="f662c-138">-v</span><span class="sxs-lookup"><span data-stu-id="f662c-138">-v</span></span> | `--verbose`                        | <span data-ttu-id="f662c-139">詳細な出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="f662c-139">Show verbose output.</span></span>        |
|    | `--no-color`                       | <span data-ttu-id="f662c-140">しないと、出力が色分けして表示します。</span><span class="sxs-lookup"><span data-stu-id="f662c-140">Don't colorize output.</span></span>      |
|    | `--prefix-output`                  | <span data-ttu-id="f662c-141">プレフィックスのレベルで出力します。</span><span class="sxs-lookup"><span data-stu-id="f662c-141">Prefix output with level.</span></span>   |


> [!TIP]
> <span data-ttu-id="f662c-142">ASP.NET Core 環境を指定するには、設定、 **ASPNETCORE_ENVIRONMENT**実行する前に環境変数。</span><span class="sxs-lookup"><span data-stu-id="f662c-142">To specify the ASP.NET Core environment, set the **ASPNETCORE_ENVIRONMENT** environment variable before running.</span></span>

<a name="commands"></a><span data-ttu-id="f662c-143">コマンド</span><span class="sxs-lookup"><span data-stu-id="f662c-143">Commands</span></span>
--------

### <a name="dotnet-ef-database-drop"></a><span data-ttu-id="f662c-144">dotnet ef データベースの削除</span><span class="sxs-lookup"><span data-stu-id="f662c-144">dotnet ef database drop</span></span>

<span data-ttu-id="f662c-145">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="f662c-145">Drops the database.</span></span>

<span data-ttu-id="f662c-146">オプション:</span><span class="sxs-lookup"><span data-stu-id="f662c-146">Options:</span></span>

|    |           |                                                          |
|:---|:----------|:---------------------------------------------------------|
| <span data-ttu-id="f662c-147">-f</span><span class="sxs-lookup"><span data-stu-id="f662c-147">-f</span></span> | `--force`   | <span data-ttu-id="f662c-148">しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="f662c-148">Don't confirm.</span></span>                                           |
|    | `--dry-run` | <span data-ttu-id="f662c-149">表示するデータベースが削除されます。 は削除しないでください。</span><span class="sxs-lookup"><span data-stu-id="f662c-149">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="dotnet-ef-database-update"></a><span data-ttu-id="f662c-150">dotnet ef データベースの更新</span><span class="sxs-lookup"><span data-stu-id="f662c-150">dotnet ef database update</span></span>

<span data-ttu-id="f662c-151">指定された移行するには、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="f662c-151">Updates the database to a specified migration.</span></span>

<span data-ttu-id="f662c-152">引数:</span><span class="sxs-lookup"><span data-stu-id="f662c-152">Arguments:</span></span>

|              |                                                                                              |
|:-------------|:---------------------------------------------------------------------------------------------|
| `<MIGRATION>` | <span data-ttu-id="f662c-153">Target の移行。</span><span class="sxs-lookup"><span data-stu-id="f662c-153">The target migration.</span></span> <span data-ttu-id="f662c-154">0 の場合、すべての移行は戻されます。</span><span class="sxs-lookup"><span data-stu-id="f662c-154">If 0, all migrations will be reverted.</span></span> <span data-ttu-id="f662c-155">移行の最後の既定値します。</span><span class="sxs-lookup"><span data-stu-id="f662c-155">Defaults to the last migration.</span></span> |

### <a name="dotnet-ef-dbcontext-info"></a><span data-ttu-id="f662c-156">dotnet ef dbcontext の情報</span><span class="sxs-lookup"><span data-stu-id="f662c-156">dotnet ef dbcontext info</span></span>

<span data-ttu-id="f662c-157">DbContext 型に関する情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="f662c-157">Gets information about a DbContext type.</span></span>

### <a name="dotnet-ef-dbcontext-list"></a><span data-ttu-id="f662c-158">dotnet ef dbcontext の一覧</span><span class="sxs-lookup"><span data-stu-id="f662c-158">dotnet ef dbcontext list</span></span>

<span data-ttu-id="f662c-159">使用可能な DbContext 型を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="f662c-159">Lists available DbContext types.</span></span>

### <a name="dotnet-ef-dbcontext-scaffold"></a><span data-ttu-id="f662c-160">dotnet ef dbcontext スキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="f662c-160">dotnet ef dbcontext scaffold</span></span>

<span data-ttu-id="f662c-161">データベースの DbContext とエンティティ型をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="f662c-161">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="f662c-162">引数:</span><span class="sxs-lookup"><span data-stu-id="f662c-162">Arguments:</span></span>

|               |                                                                             |
|:--------------|:----------------------------------------------------------------------------|
| `<CONNECTION>` | <span data-ttu-id="f662c-163">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="f662c-163">The connection string to the database.</span></span>                                      |
| `<PROVIDER>`   | <span data-ttu-id="f662c-164">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="f662c-164">The provider to use.</span></span> <span data-ttu-id="f662c-165">(たとえば、Microsoft.EntityFrameworkCore.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="f662c-165">(for example, Microsoft.EntityFrameworkCore.SqlServer)</span></span> |

<span data-ttu-id="f662c-166">オプション:</span><span class="sxs-lookup"><span data-stu-id="f662c-166">Options:</span></span>

|                 |                                         |                                                                                                  |
|:----------------|:----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f662c-167"><nobr>-d</nobr></span><span class="sxs-lookup"><span data-stu-id="f662c-167"><nobr>-d</nobr></span></span> | `--data-annotations`                      | <span data-ttu-id="f662c-168">属性を使用すると、可能な限り、モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="f662c-168">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="f662c-169">省略した場合、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f662c-169">If omitted, only the fluent API is used.</span></span> |
| <span data-ttu-id="f662c-170">-c</span><span class="sxs-lookup"><span data-stu-id="f662c-170">-c</span></span>              | `--context <NAME>`                       | <span data-ttu-id="f662c-171">DbContext の名前。</span><span class="sxs-lookup"><span data-stu-id="f662c-171">The name of the DbContext.</span></span>                                                                       |
|                 | `--context-dir <PATH>`                   | <span data-ttu-id="f662c-172">DbContext のファイルを配置するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="f662c-172">The directory to put DbContext file in.</span></span> <span data-ttu-id="f662c-173">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="f662c-173">Paths are relative to the project directory.</span></span>             |
| <span data-ttu-id="f662c-174">-f</span><span class="sxs-lookup"><span data-stu-id="f662c-174">-f</span></span>              | `--force`                                 | <span data-ttu-id="f662c-175">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="f662c-175">Overwrite existing files.</span></span>                                                                        |
| <span data-ttu-id="f662c-176">-o</span><span class="sxs-lookup"><span data-stu-id="f662c-176">-o</span></span>              | `--output-dir <PATH>`                    | <span data-ttu-id="f662c-177">ファイルを配置するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="f662c-177">The directory to put files in.</span></span> <span data-ttu-id="f662c-178">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="f662c-178">Paths are relative to the project directory.</span></span>                      |
|                 | <nobr>`--schema <SCHEMA_NAME>...`</nobr> | <span data-ttu-id="f662c-179">エンティティ型を生成するテーブルのスキーマです。</span><span class="sxs-lookup"><span data-stu-id="f662c-179">The schemas of tables to generate entity types for.</span></span>                                              |
| <span data-ttu-id="f662c-180">-t</span><span class="sxs-lookup"><span data-stu-id="f662c-180">-t</span></span>              | <span data-ttu-id="f662c-181">`--table <TABLE_NAME>`...</span><span class="sxs-lookup"><span data-stu-id="f662c-181">`--table <TABLE_NAME>`...</span></span>                | <span data-ttu-id="f662c-182">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="f662c-182">The tables to generate entity types for.</span></span>                                                         |
|                 | `--use-database-names`                    | <span data-ttu-id="f662c-183">データベースから直接テーブルおよび列名を使用します。</span><span class="sxs-lookup"><span data-stu-id="f662c-183">Use table and column names directly from the database.</span></span>                                           |

### <a name="dotnet-ef-migrations-add"></a><span data-ttu-id="f662c-184">dotnet ef migrations を追加します。</span><span class="sxs-lookup"><span data-stu-id="f662c-184">dotnet ef migrations add</span></span>

<span data-ttu-id="f662c-185">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="f662c-185">Adds a new migration.</span></span>

<span data-ttu-id="f662c-186">引数:</span><span class="sxs-lookup"><span data-stu-id="f662c-186">Arguments:</span></span>

|         |                            |
|:--------|:---------------------------|
| `<NAME>` | <span data-ttu-id="f662c-187">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="f662c-187">The name of the migration.</span></span> |

<span data-ttu-id="f662c-188">オプション:</span><span class="sxs-lookup"><span data-stu-id="f662c-188">Options:</span></span>

|                 |                                   |                                                                                                                  |
|:----------------|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f662c-189"><nobr>-o</nobr></span><span class="sxs-lookup"><span data-stu-id="f662c-189"><nobr>-o</nobr></span></span> | <span data-ttu-id="f662c-190"><nobr> `--output-dir <PATH>` </nobr></span><span class="sxs-lookup"><span data-stu-id="f662c-190"><nobr> `--output-dir <PATH>` </nobr></span></span> | <span data-ttu-id="f662c-191">使用するディレクトリ (およびサブ名前空間)。</span><span class="sxs-lookup"><span data-stu-id="f662c-191">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="f662c-192">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="f662c-192">Paths are relative to the project directory.</span></span> <span data-ttu-id="f662c-193">既定値は「移行」です。</span><span class="sxs-lookup"><span data-stu-id="f662c-193">Defaults to "Migrations".</span></span> |

### <a name="dotnet-ef-migrations-list"></a><span data-ttu-id="f662c-194">dotnet ef migrations 一覧</span><span class="sxs-lookup"><span data-stu-id="f662c-194">dotnet ef migrations list</span></span>

<span data-ttu-id="f662c-195">使用可能な移行を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="f662c-195">Lists available migrations.</span></span>

### <a name="dotnet-ef-migrations-remove"></a><span data-ttu-id="f662c-196">dotnet ef migrations を削除します。</span><span class="sxs-lookup"><span data-stu-id="f662c-196">dotnet ef migrations remove</span></span>

<span data-ttu-id="f662c-197">最後の移行を削除します。</span><span class="sxs-lookup"><span data-stu-id="f662c-197">Removes the last migration.</span></span>

<span data-ttu-id="f662c-198">オプション:</span><span class="sxs-lookup"><span data-stu-id="f662c-198">Options:</span></span>

|    |         |                                                                       |
|:---|:--------|:----------------------------------------------------------------------|
| <span data-ttu-id="f662c-199">-f</span><span class="sxs-lookup"><span data-stu-id="f662c-199">-f</span></span> | `--force` | <span data-ttu-id="f662c-200">データベースに適用されている場合は、移行を戻します。</span><span class="sxs-lookup"><span data-stu-id="f662c-200">Revert the migration if it has been applied to the database.</span></span> |

### <a name="dotnet-ef-migrations-script"></a><span data-ttu-id="f662c-201">dotnet ef migrations スクリプト</span><span class="sxs-lookup"><span data-stu-id="f662c-201">dotnet ef migrations script</span></span>

<span data-ttu-id="f662c-202">移行には、SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="f662c-202">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="f662c-203">引数:</span><span class="sxs-lookup"><span data-stu-id="f662c-203">Arguments:</span></span>

|         |                                                               |
|:--------|:--------------------------------------------------------------|
| `<FROM>` | <span data-ttu-id="f662c-204">開始の移行。</span><span class="sxs-lookup"><span data-stu-id="f662c-204">The starting migration.</span></span> <span data-ttu-id="f662c-205">既定値は 0 (最初のデータベース) です。</span><span class="sxs-lookup"><span data-stu-id="f662c-205">Defaults to 0 (the initial database).</span></span> |
| `<TO>`   | <span data-ttu-id="f662c-206">終了の移行。</span><span class="sxs-lookup"><span data-stu-id="f662c-206">The ending migration.</span></span> <span data-ttu-id="f662c-207">移行の最後の既定値します。</span><span class="sxs-lookup"><span data-stu-id="f662c-207">Defaults to the last migration.</span></span>         |

<span data-ttu-id="f662c-208">オプション:</span><span class="sxs-lookup"><span data-stu-id="f662c-208">Options:</span></span>

|    |                  |                                                                    |
|:---|:-----------------|:-------------------------------------------------------------------|
| <span data-ttu-id="f662c-209">-o</span><span class="sxs-lookup"><span data-stu-id="f662c-209">-o</span></span> | `--output <FILE>` | <span data-ttu-id="f662c-210">結果を書き込むファイル。</span><span class="sxs-lookup"><span data-stu-id="f662c-210">The file to write the result to.</span></span>                                   |
| <span data-ttu-id="f662c-211">-i</span><span class="sxs-lookup"><span data-stu-id="f662c-211">-i</span></span> | `--idempotent`     | <span data-ttu-id="f662c-212">すべての移行でのデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="f662c-212">Generate a script that can be used on a database at any migration.</span></span> |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core
