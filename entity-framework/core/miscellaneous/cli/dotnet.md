---
title: .NET Core CLI - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 8a52cb8259bb381729a33a8161aec4b73f69f45d
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
<a name="ef-core-net-command-line-tools"></a><span data-ttu-id="256fe-102">EF コア .NET コマンド ライン ツール</span><span class="sxs-lookup"><span data-stu-id="256fe-102">EF Core .NET Command-line Tools</span></span>
===============================
<span data-ttu-id="256fe-103">Entity Framework Core .NET コマンド ライン ツールは、クロス プラットフォーム拡張機能**dotnet**コマンドでは、一部の[.NET Core SDK][2]です。</span><span class="sxs-lookup"><span data-stu-id="256fe-103">The Entity Framework Core .NET Command-line Tools are an extension to the cross-platform **dotnet** command, which is part of the [.NET Core SDK][2].</span></span>

> [!TIP]
> <span data-ttu-id="256fe-104">Visual Studio を使用するかどうか、ことをお勧め[PMC ツール][ 1]より統合されたエクスペリエンスを提供するために代わりにします。</span><span class="sxs-lookup"><span data-stu-id="256fe-104">If you're using Visual Studio, we recommend [the PMC Tools][1] instead since they provide a more integrated experience.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="256fe-105">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="256fe-105">Installing the tools</span></span>
--------------------
<span data-ttu-id="256fe-106">次の手順を使用して EF コア .NET コマンド ライン ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="256fe-106">Install the EF Core .NET Command-line Tools using these steps:</span></span>

1. <span data-ttu-id="256fe-107">プロジェクト ファイルを編集し、DotNetCliToolReference 項目 (下記参照) として Microsoft.EntityFrameworkCore.Tools.DotNet を追加</span><span class="sxs-lookup"><span data-stu-id="256fe-107">Edit the project file and add Microsoft.EntityFrameworkCore.Tools.DotNet as a DotNetCliToolReference item (See below)</span></span>
2. <span data-ttu-id="256fe-108">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="256fe-108">Run the following commands:</span></span>

       dotnet add package Microsoft.EntityFrameworkCore.Design
       dotnet restore


<span data-ttu-id="256fe-109">作成されたプロジェクトは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="256fe-109">The resulting project should look something like this:</span></span>

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
> <span data-ttu-id="256fe-110">パッケージの参照を`PrivateAssets="All"`開発時にのみ使用される通常パッケージに特に便利ですが、このプロジェクトを参照しているプロジェクトに公開されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="256fe-110">A package reference with `PrivateAssets="All"` means it isn't exposed to projects that reference this project, which is especially useful for packages that are typically only used during development.</span></span>

<span data-ttu-id="256fe-111">すべての権利を行った場合は、コマンド プロンプトで次のコマンドを正常に実行することができます。</span><span class="sxs-lookup"><span data-stu-id="256fe-111">If you did everything right, you should be able to successfully run the following command in a command prompt.</span></span>

``` Console
dotnet ef
```

<a name="using-the-tools"></a><span data-ttu-id="256fe-112">ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="256fe-112">Using the tools</span></span>
---------------
<span data-ttu-id="256fe-113">コマンドを呼び出すときに 2 つのプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="256fe-113">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="256fe-114">ターゲット プロジェクトでは、ファイルが追加されます (削除される場合もあります)。</span><span class="sxs-lookup"><span data-stu-id="256fe-114">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="256fe-115">ターゲット プロジェクトが、現在のディレクトリで、プロジェクトの既定値を使用して変更することが、 <nobr> **--プロジェクト**</nobr>オプション。</span><span class="sxs-lookup"><span data-stu-id="256fe-115">The target project defaults to the project in the current directory, but can be changed using the <nobr>**--project**</nobr> option.</span></span>

<span data-ttu-id="256fe-116">スタートアップ プロジェクトは、プロジェクトのコードを実行したとき、ツールによってエミュレートされるプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="256fe-116">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="256fe-117">また、現在のディレクトリで、プロジェクトの既定値を使用して変更することができます、 **--スタートアップ プロジェクト**オプション。</span><span class="sxs-lookup"><span data-stu-id="256fe-117">It also defaults to the project in the current directory, but can be changed using the **--startup-project** option.</span></span>

<span data-ttu-id="256fe-118">一般的なオプション:</span><span class="sxs-lookup"><span data-stu-id="256fe-118">Common options:</span></span>

|    |                                  |                             |
|:---|:---------------------------------|:----------------------------|
|    | <span data-ttu-id="256fe-119">--json</span><span class="sxs-lookup"><span data-stu-id="256fe-119">--json</span></span>                           | <span data-ttu-id="256fe-120">JSON の出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="256fe-120">Show JSON output.</span></span>           |
| <span data-ttu-id="256fe-121">-c</span><span class="sxs-lookup"><span data-stu-id="256fe-121">-c</span></span> | <span data-ttu-id="256fe-122">--context \<DBCONTEXT></span><span class="sxs-lookup"><span data-stu-id="256fe-122">--context \<DBCONTEXT></span></span>           | <span data-ttu-id="256fe-123">使用する DbContext です。</span><span class="sxs-lookup"><span data-stu-id="256fe-123">The DbContext to use.</span></span>       |
| <span data-ttu-id="256fe-124">-p</span><span class="sxs-lookup"><span data-stu-id="256fe-124">-p</span></span> | <span data-ttu-id="256fe-125">--project \<PROJECT></span><span class="sxs-lookup"><span data-stu-id="256fe-125">--project \<PROJECT></span></span>             | <span data-ttu-id="256fe-126">使用するプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="256fe-126">The project to use.</span></span>         |
| <span data-ttu-id="256fe-127">-s</span><span class="sxs-lookup"><span data-stu-id="256fe-127">-s</span></span> | <span data-ttu-id="256fe-128">--startup-project \<PROJECT></span><span class="sxs-lookup"><span data-stu-id="256fe-128">--startup-project \<PROJECT></span></span>     | <span data-ttu-id="256fe-129">使用するスタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="256fe-129">The startup project to use.</span></span> |
|    | <span data-ttu-id="256fe-130">--framework \<FRAMEWORK ></span><span class="sxs-lookup"><span data-stu-id="256fe-130">--framework \<FRAMEWORK></span></span>         | <span data-ttu-id="256fe-131">ターゲット フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="256fe-131">The target framework.</span></span>       |
|    | <span data-ttu-id="256fe-132">--configuration \<CONFIGURATION></span><span class="sxs-lookup"><span data-stu-id="256fe-132">--configuration \<CONFIGURATION></span></span> | <span data-ttu-id="256fe-133">使用する構成。</span><span class="sxs-lookup"><span data-stu-id="256fe-133">The configuration to use.</span></span>   |
|    | <span data-ttu-id="256fe-134">--runtime \<IDENTIFIER></span><span class="sxs-lookup"><span data-stu-id="256fe-134">--runtime \<IDENTIFIER></span></span>          | <span data-ttu-id="256fe-135">使用するランタイム。</span><span class="sxs-lookup"><span data-stu-id="256fe-135">The runtime to use.</span></span>         |
| <span data-ttu-id="256fe-136">-h</span><span class="sxs-lookup"><span data-stu-id="256fe-136">-h</span></span> | <span data-ttu-id="256fe-137">-ヘルプ</span><span class="sxs-lookup"><span data-stu-id="256fe-137">--help</span></span>                           | <span data-ttu-id="256fe-138">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="256fe-138">Show help information.</span></span>      |
| <span data-ttu-id="256fe-139">-v</span><span class="sxs-lookup"><span data-stu-id="256fe-139">-v</span></span> | <span data-ttu-id="256fe-140">--verbose</span><span class="sxs-lookup"><span data-stu-id="256fe-140">--verbose</span></span>                        | <span data-ttu-id="256fe-141">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="256fe-141">Show verbose output.</span></span>        |
|    | <span data-ttu-id="256fe-142">--no-color</span><span class="sxs-lookup"><span data-stu-id="256fe-142">--no-color</span></span>                       | <span data-ttu-id="256fe-143">しないと、出力が色分けして表示します。</span><span class="sxs-lookup"><span data-stu-id="256fe-143">Don't colorize output.</span></span>      |
|    | <span data-ttu-id="256fe-144">--prefix-output</span><span class="sxs-lookup"><span data-stu-id="256fe-144">--prefix-output</span></span>                  | <span data-ttu-id="256fe-145">出力レベルをプレフィックスします。</span><span class="sxs-lookup"><span data-stu-id="256fe-145">Prefix output with level.</span></span>   |


> [!TIP]
> <span data-ttu-id="256fe-146">ASP.NET Core 環境を指定するには、設定、 **ASPNETCORE_ENVIRONMENT**実行する前に環境変数。</span><span class="sxs-lookup"><span data-stu-id="256fe-146">To specify the ASP.NET Core environment, set the **ASPNETCORE_ENVIRONMENT** environment variable before running.</span></span>

<a name="commands"></a><span data-ttu-id="256fe-147">コマンド</span><span class="sxs-lookup"><span data-stu-id="256fe-147">Commands</span></span>
--------

### <a name="dotnet-ef-database-drop"></a><span data-ttu-id="256fe-148">dotnet ef データベースの削除</span><span class="sxs-lookup"><span data-stu-id="256fe-148">dotnet ef database drop</span></span>

<span data-ttu-id="256fe-149">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="256fe-149">Drops the database.</span></span>

<span data-ttu-id="256fe-150">オプション:</span><span class="sxs-lookup"><span data-stu-id="256fe-150">Options:</span></span>

|    |           |                                                          |
|:---|:----------|:---------------------------------------------------------|
| <span data-ttu-id="256fe-151">-f</span><span class="sxs-lookup"><span data-stu-id="256fe-151">-f</span></span> | <span data-ttu-id="256fe-152">--force</span><span class="sxs-lookup"><span data-stu-id="256fe-152">--force</span></span>   | <span data-ttu-id="256fe-153">しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="256fe-153">Don't confirm.</span></span>                                           |
|    | <span data-ttu-id="256fe-154">--dry-run</span><span class="sxs-lookup"><span data-stu-id="256fe-154">--dry-run</span></span> | <span data-ttu-id="256fe-155">データベースは削除しますが、それを削除しないを表示します。</span><span class="sxs-lookup"><span data-stu-id="256fe-155">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="dotnet-ef-database-update"></a><span data-ttu-id="256fe-156">dotnet ef database update</span><span class="sxs-lookup"><span data-stu-id="256fe-156">dotnet ef database update</span></span>

<span data-ttu-id="256fe-157">指定された移行するには、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="256fe-157">Updates the database to a specified migration.</span></span>

<span data-ttu-id="256fe-158">引数:</span><span class="sxs-lookup"><span data-stu-id="256fe-158">Arguments:</span></span>

|              |                                                                                              |
|:-------------|:---------------------------------------------------------------------------------------------|
| <span data-ttu-id="256fe-159">\<MIGRATION></span><span class="sxs-lookup"><span data-stu-id="256fe-159">\<MIGRATION></span></span> | <span data-ttu-id="256fe-160">Target の移行。</span><span class="sxs-lookup"><span data-stu-id="256fe-160">The target migration.</span></span> <span data-ttu-id="256fe-161">0 の場合、すべての移行は元に戻せません。</span><span class="sxs-lookup"><span data-stu-id="256fe-161">If 0, all migrations will be reverted.</span></span> <span data-ttu-id="256fe-162">既定値は最後に移行します。</span><span class="sxs-lookup"><span data-stu-id="256fe-162">Defaults to the last migration.</span></span> |

### <a name="dotnet-ef-dbcontext-info"></a><span data-ttu-id="256fe-163">dotnet ef dbcontext info</span><span class="sxs-lookup"><span data-stu-id="256fe-163">dotnet ef dbcontext info</span></span>

<span data-ttu-id="256fe-164">DbContext 型に関する情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="256fe-164">Gets information about a DbContext type.</span></span>

### <a name="dotnet-ef-dbcontext-list"></a><span data-ttu-id="256fe-165">dotnet ef dbcontext list</span><span class="sxs-lookup"><span data-stu-id="256fe-165">dotnet ef dbcontext list</span></span>

<span data-ttu-id="256fe-166">使用可能な DbContext 型を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="256fe-166">Lists available DbContext types.</span></span>

### <a name="dotnet-ef-dbcontext-scaffold"></a><span data-ttu-id="256fe-167">dotnet ef dbcontext scaffold</span><span class="sxs-lookup"><span data-stu-id="256fe-167">dotnet ef dbcontext scaffold</span></span>

<span data-ttu-id="256fe-168">スキャフォールディング データベースの DbContext とエンティティの型。</span><span class="sxs-lookup"><span data-stu-id="256fe-168">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="256fe-169">引数:</span><span class="sxs-lookup"><span data-stu-id="256fe-169">Arguments:</span></span>

|               |                                                                     |
|:--------------|:--------------------------------------------------------------------|
| <span data-ttu-id="256fe-170">\<CONNECTION></span><span class="sxs-lookup"><span data-stu-id="256fe-170">\<CONNECTION></span></span> | <span data-ttu-id="256fe-171">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="256fe-171">The connection string to the database.</span></span>                              |
| <span data-ttu-id="256fe-172">\<PROVIDER></span><span class="sxs-lookup"><span data-stu-id="256fe-172">\<PROVIDER></span></span>   | <span data-ttu-id="256fe-173">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="256fe-173">The provider to use.</span></span> <span data-ttu-id="256fe-174">(例。</span><span class="sxs-lookup"><span data-stu-id="256fe-174">(E.g.</span></span> <span data-ttu-id="256fe-175">Microsoft.EntityFrameworkCore.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="256fe-175">Microsoft.EntityFrameworkCore.SqlServer)</span></span> |

<span data-ttu-id="256fe-176">オプション:</span><span class="sxs-lookup"><span data-stu-id="256fe-176">Options:</span></span>

|                 |                                         |                                                                                                  |
|:----------------|:----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="256fe-177"><nobr>-d</nobr></span><span class="sxs-lookup"><span data-stu-id="256fe-177"><nobr>-d</nobr></span></span> | <span data-ttu-id="256fe-178">--data-annotations</span><span class="sxs-lookup"><span data-stu-id="256fe-178">--data-annotations</span></span>                      | <span data-ttu-id="256fe-179">属性を使用すると、(可能な場合)、モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="256fe-179">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="256fe-180">省略すると、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="256fe-180">If omitted, only the fluent API is used.</span></span> |
| <span data-ttu-id="256fe-181">-c</span><span class="sxs-lookup"><span data-stu-id="256fe-181">-c</span></span>              | <span data-ttu-id="256fe-182">--context \<NAME></span><span class="sxs-lookup"><span data-stu-id="256fe-182">--context \<NAME></span></span>                       | <span data-ttu-id="256fe-183">DbContext の名前。</span><span class="sxs-lookup"><span data-stu-id="256fe-183">The name of the DbContext.</span></span>                                                                       |
| <span data-ttu-id="256fe-184">-f</span><span class="sxs-lookup"><span data-stu-id="256fe-184">-f</span></span>              | <span data-ttu-id="256fe-185">--force</span><span class="sxs-lookup"><span data-stu-id="256fe-185">--force</span></span>                                 | <span data-ttu-id="256fe-186">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="256fe-186">Overwrite existing files.</span></span>                                                                        |
| <span data-ttu-id="256fe-187">-o</span><span class="sxs-lookup"><span data-stu-id="256fe-187">-o</span></span>              | <span data-ttu-id="256fe-188">--output-dir \<PATH></span><span class="sxs-lookup"><span data-stu-id="256fe-188">--output-dir \<PATH></span></span>                    | <span data-ttu-id="256fe-189">ファイルを置くディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="256fe-189">The directory to put files in.</span></span> <span data-ttu-id="256fe-190">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="256fe-190">Paths are relative to the project directory.</span></span>                      |
|                 | <span data-ttu-id="256fe-191"><nobr>--schema \<SCHEMA_NAME>...</nobr></span><span class="sxs-lookup"><span data-stu-id="256fe-191"><nobr>--schema \<SCHEMA_NAME>...</nobr></span></span> | <span data-ttu-id="256fe-192">エンティティ型を生成するテーブルのスキーマです。</span><span class="sxs-lookup"><span data-stu-id="256fe-192">The schemas of tables to generate entity types for.</span></span>                                              |
| <span data-ttu-id="256fe-193">-t</span><span class="sxs-lookup"><span data-stu-id="256fe-193">-t</span></span>              | <span data-ttu-id="256fe-194">--table \<TABLE_NAME>...</span><span class="sxs-lookup"><span data-stu-id="256fe-194">--table \<TABLE_NAME>...</span></span>                | <span data-ttu-id="256fe-195">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="256fe-195">The tables to generate entity types for.</span></span>                                                         |
|                 | <span data-ttu-id="256fe-196">--use-database-names</span><span class="sxs-lookup"><span data-stu-id="256fe-196">--use-database-names</span></span>                    | <span data-ttu-id="256fe-197">データベースから直接テーブルおよび列名を使用します。</span><span class="sxs-lookup"><span data-stu-id="256fe-197">Use table and column names directly from the database.</span></span>                                           |

### <a name="dotnet-ef-migrations-add"></a><span data-ttu-id="256fe-198">dotnet ef 移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="256fe-198">dotnet ef migrations add</span></span>

<span data-ttu-id="256fe-199">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="256fe-199">Adds a new migration.</span></span>

<span data-ttu-id="256fe-200">引数:</span><span class="sxs-lookup"><span data-stu-id="256fe-200">Arguments:</span></span>

|         |                            |
|:--------|:---------------------------|
| <span data-ttu-id="256fe-201">\<NAME></span><span class="sxs-lookup"><span data-stu-id="256fe-201">\<NAME></span></span> | <span data-ttu-id="256fe-202">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="256fe-202">The name of the migration.</span></span> |

<span data-ttu-id="256fe-203">オプション:</span><span class="sxs-lookup"><span data-stu-id="256fe-203">Options:</span></span>

|                 |                                   |                                                                                                                  |
|:----------------|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="256fe-204"><nobr>-o</nobr></span><span class="sxs-lookup"><span data-stu-id="256fe-204"><nobr>-o</nobr></span></span> | <span data-ttu-id="256fe-205"><nobr>--output-dir \<PATH></nobr></span><span class="sxs-lookup"><span data-stu-id="256fe-205"><nobr>--output-dir \<PATH></nobr></span></span> | <span data-ttu-id="256fe-206">ディレクトリ (およびサブ名前空間) を使用します。</span><span class="sxs-lookup"><span data-stu-id="256fe-206">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="256fe-207">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="256fe-207">Paths are relative to the project directory.</span></span> <span data-ttu-id="256fe-208">既定値は「移行」です。</span><span class="sxs-lookup"><span data-stu-id="256fe-208">Defaults to "Migrations".</span></span> |

### <a name="dotnet-ef-migrations-list"></a><span data-ttu-id="256fe-209">dotnet ef 移行リスト</span><span class="sxs-lookup"><span data-stu-id="256fe-209">dotnet ef migrations list</span></span>

<span data-ttu-id="256fe-210">使用可能な移行を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="256fe-210">Lists available migrations.</span></span>

### <a name="dotnet-ef-migrations-remove"></a><span data-ttu-id="256fe-211">dotnet ef 移行を削除します。</span><span class="sxs-lookup"><span data-stu-id="256fe-211">dotnet ef migrations remove</span></span>

<span data-ttu-id="256fe-212">前回の移行を削除します。</span><span class="sxs-lookup"><span data-stu-id="256fe-212">Removes the last migration.</span></span>

<span data-ttu-id="256fe-213">オプション:</span><span class="sxs-lookup"><span data-stu-id="256fe-213">Options:</span></span>

|    |         |                                                                       |
|:---|:--------|:----------------------------------------------------------------------|
| <span data-ttu-id="256fe-214">-f</span><span class="sxs-lookup"><span data-stu-id="256fe-214">-f</span></span> | <span data-ttu-id="256fe-215">--force</span><span class="sxs-lookup"><span data-stu-id="256fe-215">--force</span></span> | <span data-ttu-id="256fe-216">移行をデータベースに適用されているかどうかをチェックしないでください。</span><span class="sxs-lookup"><span data-stu-id="256fe-216">Don't check to see if the migration has been applied to the database.</span></span> |

### <a name="dotnet-ef-migrations-script"></a><span data-ttu-id="256fe-217">dotnet ef 移行スクリプト</span><span class="sxs-lookup"><span data-stu-id="256fe-217">dotnet ef migrations script</span></span>

<span data-ttu-id="256fe-218">移行からの SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="256fe-218">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="256fe-219">引数:</span><span class="sxs-lookup"><span data-stu-id="256fe-219">Arguments:</span></span>

|         |                                                               |
|:--------|:--------------------------------------------------------------|
| <span data-ttu-id="256fe-220">\<FROM></span><span class="sxs-lookup"><span data-stu-id="256fe-220">\<FROM></span></span> | <span data-ttu-id="256fe-221">開始の移行。</span><span class="sxs-lookup"><span data-stu-id="256fe-221">The starting migration.</span></span> <span data-ttu-id="256fe-222">既定値は 0 (最初のデータベース)。</span><span class="sxs-lookup"><span data-stu-id="256fe-222">Defaults to 0 (the initial database).</span></span> |
| <span data-ttu-id="256fe-223">\<TO></span><span class="sxs-lookup"><span data-stu-id="256fe-223">\<TO></span></span>   | <span data-ttu-id="256fe-224">終了の移行。</span><span class="sxs-lookup"><span data-stu-id="256fe-224">The ending migration.</span></span> <span data-ttu-id="256fe-225">既定値は最後に移行します。</span><span class="sxs-lookup"><span data-stu-id="256fe-225">Defaults to the last migration.</span></span>         |

<span data-ttu-id="256fe-226">オプション:</span><span class="sxs-lookup"><span data-stu-id="256fe-226">Options:</span></span>

|    |                  |                                                                    |
|:---|:-----------------|:-------------------------------------------------------------------|
| <span data-ttu-id="256fe-227">-o</span><span class="sxs-lookup"><span data-stu-id="256fe-227">-o</span></span> | <span data-ttu-id="256fe-228">--output \<FILE></span><span class="sxs-lookup"><span data-stu-id="256fe-228">--output \<FILE></span></span> | <span data-ttu-id="256fe-229">結果を書き込むファイルです。</span><span class="sxs-lookup"><span data-stu-id="256fe-229">The file to write the result to.</span></span>                                   |
| <span data-ttu-id="256fe-230">-i</span><span class="sxs-lookup"><span data-stu-id="256fe-230">-i</span></span> | <span data-ttu-id="256fe-231">--idempotent</span><span class="sxs-lookup"><span data-stu-id="256fe-231">--idempotent</span></span>     | <span data-ttu-id="256fe-232">すべての移行でのデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="256fe-232">Generate a script that can be used on a database at any migration.</span></span> |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core
