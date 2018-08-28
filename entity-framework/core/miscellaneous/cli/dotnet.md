---
title: .NET core CLI - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 81427b010f63bdd591ffb9117c1556722313c3fa
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995298"
---
<a name="ef-core-net-command-line-tools"></a><span data-ttu-id="ca9ec-102">EF Core .NET コマンド ライン ツール</span><span class="sxs-lookup"><span data-stu-id="ca9ec-102">EF Core .NET Command-line Tools</span></span>
===============================
<span data-ttu-id="ca9ec-103">Entity Framework Core .NET コマンド ライン ツール、クロス プラットフォーム拡張機能**dotnet**コマンドで、一部の[.NET Core SDK][2]します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-103">The Entity Framework Core .NET Command-line Tools are an extension to the cross-platform **dotnet** command, which is part of the [.NET Core SDK][2].</span></span>

> [!TIP]
> <span data-ttu-id="ca9ec-104">Visual Studio を使用しているかどうか、お勧めします[に優れた PMC ツール][ 1]代わりに、統合性を提供するためです。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-104">If you're using Visual Studio, we recommend [the PMC Tools][1] instead since they provide a more integrated experience.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="ca9ec-105">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="ca9ec-105">Installing the tools</span></span>
--------------------
> [!NOTE]
> <span data-ttu-id="ca9ec-106">.NET Core SDK バージョン 2.1.300 新しいが含まれています**dotnet ef** EF Core 2.0 以降のバージョンと互換性のあるコマンド。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-106">The .NET Core SDK version 2.1.300 and newer includes **dotnet ef** commands that are compatible with EF Core 2.0 and later versions.</span></span> <span data-ttu-id="ca9ec-107">したがって最近のバージョンの .NET Core SDK と EF Core ランタイムを使用している場合、インストールは必要なく、このセクションの残りの部分を無視することができます。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-107">Therefore if you are using recent versions of the .NET Core SDK and the EF Core runtime, no installation is required and you can ignore the rest of this section.</span></span>
>
> <span data-ttu-id="ca9ec-108">一方で、 **dotnet ef**以降と .NET Core SDK バージョン 2.1.300 に格納されているツールは、EF Core のバージョン 1.0 および 1.1 と互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-108">On the other hand, the **dotnet ef** tool contained in .NET Core SDK version 2.1.300 and newer is not compatible with EF Core version 1.0 and 1.1.</span></span> <span data-ttu-id="ca9ec-109">2.1.200 のバージョンをインストールする前に、.NET Core SDK 2.1.300 をあるコンピューター上で以前のバージョンの EF Core を使用するプロジェクトを使用する、または新しいインストールもする必要がありますまたは SDK の古い変更してその以前のバージョンを使用するアプリケーションを構成して、 [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json)ファイル。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-109">Before you can work with a project that uses these earlier versions of EF Core on a computer that has .NET Core SDK 2.1.300 or newer installed, you must also install version 2.1.200 or older of the SDK and configure the application to use that older version by modifying its [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json) file.</span></span> <span data-ttu-id="ca9ec-110">このファイルは通常、(プロジェクトの上に 1 つ) のソリューション ディレクトリにインクルードします。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-110">This file is normally included in the solution directory (one above the project).</span></span> <span data-ttu-id="ca9ec-111">以下の installlation 命令を続行できます。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-111">Then you can proceed with the installlation instruction below.</span></span>

<span data-ttu-id="ca9ec-112">.NET Core SDK の以前のバージョンには、次の手順を使用して EF Core .NET コマンド ライン ツールをインストールできます。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-112">For previous versions of the .NET Core SDK, you can install the EF Core .NET Command-line Tools using these steps:</span></span>

1. <span data-ttu-id="ca9ec-113">プロジェクト ファイルを編集し、Microsoft.EntityFrameworkCore.Tools.DotNet を DotNetCliToolReference 項目 (下記参照) として追加</span><span class="sxs-lookup"><span data-stu-id="ca9ec-113">Edit the project file and add Microsoft.EntityFrameworkCore.Tools.DotNet as a DotNetCliToolReference item (See below)</span></span>
2. <span data-ttu-id="ca9ec-114">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-114">Run the following commands:</span></span>

       dotnet add package Microsoft.EntityFrameworkCore.Design
       dotnet restore

<span data-ttu-id="ca9ec-115">作成されたプロジェクトは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-115">The resulting project should look something like this:</span></span>

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
> <span data-ttu-id="ca9ec-116">パッケージ参照を`PrivateAssets="All"`開発時にのみ使用される一般的なパッケージに特に便利ですが、このプロジェクトを参照するプロジェクトに公開されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-116">A package reference with `PrivateAssets="All"` means it isn't exposed to projects that reference this project, which is especially useful for packages that are typically only used during development.</span></span>

<span data-ttu-id="ca9ec-117">右のすべてのプロセスが、コマンド プロンプトで次のコマンドを正常に実行できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-117">If you did everything right, you should be able to successfully run the following command in a command prompt.</span></span>

``` Console
dotnet ef
```

<a name="using-the-tools"></a><span data-ttu-id="ca9ec-118">ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-118">Using the tools</span></span>
---------------
<span data-ttu-id="ca9ec-119">コマンドを呼び出すたびにある 2 つのプロジェクト関係します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-119">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="ca9ec-120">ターゲット プロジェクトでは、ファイルが追加されます (削除される場合もあります)。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-120">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="ca9ec-121">ターゲット プロジェクトが現在のディレクトリでプロジェクトの既定値を使用して変更することが、 <nobr> **--プロジェクト**</nobr>オプション。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-121">The target project defaults to the project in the current directory, but can be changed using the <nobr>**--project**</nobr> option.</span></span>

<span data-ttu-id="ca9ec-122">スタートアップ プロジェクトは、プロジェクトのコードを実行したとき、ツールによってエミュレートされるプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-122">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="ca9ec-123">またに既定値は、現在のディレクトリでプロジェクトを使用して変更できる、 **--スタートアップ プロジェクト**オプション。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-123">It also defaults to the project in the current directory, but can be changed using the **--startup-project** option.</span></span>

> [!NOTE]
> <span data-ttu-id="ca9ec-124">たとえば、次のようなりますを持つ別のプロジェクトにインストールされている EF Core web アプリケーションのデータベースの更新: `dotnet ef database update --project {project-path}` (から、web アプリのディレクトリ)</span><span class="sxs-lookup"><span data-stu-id="ca9ec-124">For instance, updating the database of your web application that has EF Core installed in a different project would look like this: `dotnet ef database update --project {project-path}` (from your web app directory)</span></span>

<span data-ttu-id="ca9ec-125">一般的なオプション:</span><span class="sxs-lookup"><span data-stu-id="ca9ec-125">Common options:</span></span>

|    |                                  |                             |
|:---|:---------------------------------|:----------------------------|
|    | <span data-ttu-id="ca9ec-126">--json</span><span class="sxs-lookup"><span data-stu-id="ca9ec-126">--json</span></span>                           | <span data-ttu-id="ca9ec-127">JSON 出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-127">Show JSON output.</span></span>           |
| <span data-ttu-id="ca9ec-128">-c</span><span class="sxs-lookup"><span data-stu-id="ca9ec-128">-c</span></span> | <span data-ttu-id="ca9ec-129">--context \<DBCONTEXT></span><span class="sxs-lookup"><span data-stu-id="ca9ec-129">--context \<DBCONTEXT></span></span>           | <span data-ttu-id="ca9ec-130">使用する DbContext します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-130">The DbContext to use.</span></span>       |
| <span data-ttu-id="ca9ec-131">-p</span><span class="sxs-lookup"><span data-stu-id="ca9ec-131">-p</span></span> | <span data-ttu-id="ca9ec-132">--project \<PROJECT></span><span class="sxs-lookup"><span data-stu-id="ca9ec-132">--project \<PROJECT></span></span>             | <span data-ttu-id="ca9ec-133">使用するプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-133">The project to use.</span></span>         |
| <span data-ttu-id="ca9ec-134">-s</span><span class="sxs-lookup"><span data-stu-id="ca9ec-134">-s</span></span> | <span data-ttu-id="ca9ec-135">--startup-project \<PROJECT></span><span class="sxs-lookup"><span data-stu-id="ca9ec-135">--startup-project \<PROJECT></span></span>     | <span data-ttu-id="ca9ec-136">使用するスタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-136">The startup project to use.</span></span> |
|    | <span data-ttu-id="ca9ec-137">-framework \<FRAMEWORK ></span><span class="sxs-lookup"><span data-stu-id="ca9ec-137">--framework \<FRAMEWORK></span></span>         | <span data-ttu-id="ca9ec-138">ターゲット フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-138">The target framework.</span></span>       |
|    | <span data-ttu-id="ca9ec-139">-構成\<構成 ></span><span class="sxs-lookup"><span data-stu-id="ca9ec-139">--configuration \<CONFIGURATION></span></span> | <span data-ttu-id="ca9ec-140">使用する構成。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-140">The configuration to use.</span></span>   |
|    | <span data-ttu-id="ca9ec-141">-ランタイム\<識別子 ></span><span class="sxs-lookup"><span data-stu-id="ca9ec-141">--runtime \<IDENTIFIER></span></span>          | <span data-ttu-id="ca9ec-142">使用するランタイム。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-142">The runtime to use.</span></span>         |
| <span data-ttu-id="ca9ec-143">-h</span><span class="sxs-lookup"><span data-stu-id="ca9ec-143">-h</span></span> | <span data-ttu-id="ca9ec-144">-ヘルプ</span><span class="sxs-lookup"><span data-stu-id="ca9ec-144">--help</span></span>                           | <span data-ttu-id="ca9ec-145">ヘルプ情報が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-145">Show help information.</span></span>      |
| <span data-ttu-id="ca9ec-146">-v</span><span class="sxs-lookup"><span data-stu-id="ca9ec-146">-v</span></span> | <span data-ttu-id="ca9ec-147">-詳細</span><span class="sxs-lookup"><span data-stu-id="ca9ec-147">--verbose</span></span>                        | <span data-ttu-id="ca9ec-148">詳細な出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-148">Show verbose output.</span></span>        |
|    | <span data-ttu-id="ca9ec-149">--no-color</span><span class="sxs-lookup"><span data-stu-id="ca9ec-149">--no-color</span></span>                       | <span data-ttu-id="ca9ec-150">しないと、出力が色分けして表示します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-150">Don't colorize output.</span></span>      |
|    | <span data-ttu-id="ca9ec-151">--prefix-output</span><span class="sxs-lookup"><span data-stu-id="ca9ec-151">--prefix-output</span></span>                  | <span data-ttu-id="ca9ec-152">プレフィックスのレベルで出力します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-152">Prefix output with level.</span></span>   |


> [!TIP]
> <span data-ttu-id="ca9ec-153">ASP.NET Core 環境を指定するには、設定、 **ASPNETCORE_ENVIRONMENT**実行する前に環境変数。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-153">To specify the ASP.NET Core environment, set the **ASPNETCORE_ENVIRONMENT** environment variable before running.</span></span>

<a name="commands"></a><span data-ttu-id="ca9ec-154">コマンド</span><span class="sxs-lookup"><span data-stu-id="ca9ec-154">Commands</span></span>
--------

### <a name="dotnet-ef-database-drop"></a><span data-ttu-id="ca9ec-155">dotnet ef データベースの削除</span><span class="sxs-lookup"><span data-stu-id="ca9ec-155">dotnet ef database drop</span></span>

<span data-ttu-id="ca9ec-156">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-156">Drops the database.</span></span>

<span data-ttu-id="ca9ec-157">オプション:</span><span class="sxs-lookup"><span data-stu-id="ca9ec-157">Options:</span></span>

|    |           |                                                          |
|:---|:----------|:---------------------------------------------------------|
| <span data-ttu-id="ca9ec-158">-f</span><span class="sxs-lookup"><span data-stu-id="ca9ec-158">-f</span></span> | <span data-ttu-id="ca9ec-159">-強制</span><span class="sxs-lookup"><span data-stu-id="ca9ec-159">--force</span></span>   | <span data-ttu-id="ca9ec-160">しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-160">Don't confirm.</span></span>                                           |
|    | <span data-ttu-id="ca9ec-161">--ドライ ラン</span><span class="sxs-lookup"><span data-stu-id="ca9ec-161">--dry-run</span></span> | <span data-ttu-id="ca9ec-162">表示するデータベースが削除されます。 は削除しないでください。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-162">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="dotnet-ef-database-update"></a><span data-ttu-id="ca9ec-163">dotnet ef データベースの更新</span><span class="sxs-lookup"><span data-stu-id="ca9ec-163">dotnet ef database update</span></span>

<span data-ttu-id="ca9ec-164">指定された移行するには、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-164">Updates the database to a specified migration.</span></span>

<span data-ttu-id="ca9ec-165">引数:</span><span class="sxs-lookup"><span data-stu-id="ca9ec-165">Arguments:</span></span>

|              |                                                                                              |
|:-------------|:---------------------------------------------------------------------------------------------|
| <span data-ttu-id="ca9ec-166">\<移行 &GT;</span><span class="sxs-lookup"><span data-stu-id="ca9ec-166">\<MIGRATION></span></span> | <span data-ttu-id="ca9ec-167">Target の移行。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-167">The target migration.</span></span> <span data-ttu-id="ca9ec-168">0 の場合、すべての移行は戻されます。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-168">If 0, all migrations will be reverted.</span></span> <span data-ttu-id="ca9ec-169">移行の最後の既定値します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-169">Defaults to the last migration.</span></span> |

### <a name="dotnet-ef-dbcontext-info"></a><span data-ttu-id="ca9ec-170">dotnet ef dbcontext の情報</span><span class="sxs-lookup"><span data-stu-id="ca9ec-170">dotnet ef dbcontext info</span></span>

<span data-ttu-id="ca9ec-171">DbContext 型に関する情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-171">Gets information about a DbContext type.</span></span>

### <a name="dotnet-ef-dbcontext-list"></a><span data-ttu-id="ca9ec-172">dotnet ef dbcontext の一覧</span><span class="sxs-lookup"><span data-stu-id="ca9ec-172">dotnet ef dbcontext list</span></span>

<span data-ttu-id="ca9ec-173">使用可能な DbContext 型を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-173">Lists available DbContext types.</span></span>

### <a name="dotnet-ef-dbcontext-scaffold"></a><span data-ttu-id="ca9ec-174">dotnet ef dbcontext スキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="ca9ec-174">dotnet ef dbcontext scaffold</span></span>

<span data-ttu-id="ca9ec-175">データベースの DbContext とエンティティ型をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-175">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="ca9ec-176">引数:</span><span class="sxs-lookup"><span data-stu-id="ca9ec-176">Arguments:</span></span>

|               |                                                                             |
|:--------------|:----------------------------------------------------------------------------|
| <span data-ttu-id="ca9ec-177">\<接続 &GT;</span><span class="sxs-lookup"><span data-stu-id="ca9ec-177">\<CONNECTION></span></span> | <span data-ttu-id="ca9ec-178">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-178">The connection string to the database.</span></span>                                      |
| <span data-ttu-id="ca9ec-179">\<プロバイダー &GT;</span><span class="sxs-lookup"><span data-stu-id="ca9ec-179">\<PROVIDER></span></span>   | <span data-ttu-id="ca9ec-180">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-180">The provider to use.</span></span> <span data-ttu-id="ca9ec-181">(たとえば、Microsoft.EntityFrameworkCore.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="ca9ec-181">(for example, Microsoft.EntityFrameworkCore.SqlServer)</span></span> |

<span data-ttu-id="ca9ec-182">オプション:</span><span class="sxs-lookup"><span data-stu-id="ca9ec-182">Options:</span></span>

|                 |                                         |                                                                                                  |
|:----------------|:----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ca9ec-183"><nobr>-d</nobr></span><span class="sxs-lookup"><span data-stu-id="ca9ec-183"><nobr>-d</nobr></span></span> | <span data-ttu-id="ca9ec-184">-データの注釈</span><span class="sxs-lookup"><span data-stu-id="ca9ec-184">--data-annotations</span></span>                      | <span data-ttu-id="ca9ec-185">属性を使用すると、可能な限り、モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-185">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="ca9ec-186">省略した場合、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-186">If omitted, only the fluent API is used.</span></span> |
| <span data-ttu-id="ca9ec-187">-c</span><span class="sxs-lookup"><span data-stu-id="ca9ec-187">-c</span></span>              | <span data-ttu-id="ca9ec-188">--context \<NAME></span><span class="sxs-lookup"><span data-stu-id="ca9ec-188">--context \<NAME></span></span>                       | <span data-ttu-id="ca9ec-189">DbContext の名前。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-189">The name of the DbContext.</span></span>                                                                       |
|                 | <span data-ttu-id="ca9ec-190">-コンテキスト dir\<パス ></span><span class="sxs-lookup"><span data-stu-id="ca9ec-190">--context-dir \<PATH></span></span>                   | <span data-ttu-id="ca9ec-191">DbContext のファイルを配置するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-191">The directory to put DbContext file in.</span></span> <span data-ttu-id="ca9ec-192">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-192">Paths are relative to the project directory.</span></span>             |
| <span data-ttu-id="ca9ec-193">-f</span><span class="sxs-lookup"><span data-stu-id="ca9ec-193">-f</span></span>              | <span data-ttu-id="ca9ec-194">-強制</span><span class="sxs-lookup"><span data-stu-id="ca9ec-194">--force</span></span>                                 | <span data-ttu-id="ca9ec-195">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-195">Overwrite existing files.</span></span>                                                                        |
| <span data-ttu-id="ca9ec-196">-o</span><span class="sxs-lookup"><span data-stu-id="ca9ec-196">-o</span></span>              | <span data-ttu-id="ca9ec-197">--output-dir \<PATH></span><span class="sxs-lookup"><span data-stu-id="ca9ec-197">--output-dir \<PATH></span></span>                    | <span data-ttu-id="ca9ec-198">ファイルを配置するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-198">The directory to put files in.</span></span> <span data-ttu-id="ca9ec-199">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-199">Paths are relative to the project directory.</span></span>                      |
|                 | <span data-ttu-id="ca9ec-200"><nobr>-スキーマ\<SCHEMA_NAME >.</nobr></span><span class="sxs-lookup"><span data-stu-id="ca9ec-200"><nobr>--schema \<SCHEMA_NAME>...</nobr></span></span> | <span data-ttu-id="ca9ec-201">エンティティ型を生成するテーブルのスキーマです。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-201">The schemas of tables to generate entity types for.</span></span>                                              |
| <span data-ttu-id="ca9ec-202">-t</span><span class="sxs-lookup"><span data-stu-id="ca9ec-202">-t</span></span>              | <span data-ttu-id="ca9ec-203">-テーブル\<TABLE_NAME >.</span><span class="sxs-lookup"><span data-stu-id="ca9ec-203">--table \<TABLE_NAME>...</span></span>                | <span data-ttu-id="ca9ec-204">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-204">The tables to generate entity types for.</span></span>                                                         |
|                 | <span data-ttu-id="ca9ec-205">--use-database-names</span><span class="sxs-lookup"><span data-stu-id="ca9ec-205">--use-database-names</span></span>                    | <span data-ttu-id="ca9ec-206">データベースから直接テーブルおよび列名を使用します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-206">Use table and column names directly from the database.</span></span>                                           |

### <a name="dotnet-ef-migrations-add"></a><span data-ttu-id="ca9ec-207">dotnet ef migrations を追加します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-207">dotnet ef migrations add</span></span>

<span data-ttu-id="ca9ec-208">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-208">Adds a new migration.</span></span>

<span data-ttu-id="ca9ec-209">引数:</span><span class="sxs-lookup"><span data-stu-id="ca9ec-209">Arguments:</span></span>

|         |                            |
|:--------|:---------------------------|
| <span data-ttu-id="ca9ec-210">\<NAME></span><span class="sxs-lookup"><span data-stu-id="ca9ec-210">\<NAME></span></span> | <span data-ttu-id="ca9ec-211">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-211">The name of the migration.</span></span> |

<span data-ttu-id="ca9ec-212">オプション:</span><span class="sxs-lookup"><span data-stu-id="ca9ec-212">Options:</span></span>

|                 |                                   |                                                                                                                  |
|:----------------|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ca9ec-213"><nobr>-o</nobr></span><span class="sxs-lookup"><span data-stu-id="ca9ec-213"><nobr>-o</nobr></span></span> | <span data-ttu-id="ca9ec-214"><nobr>--output-dir \<PATH></nobr></span><span class="sxs-lookup"><span data-stu-id="ca9ec-214"><nobr>--output-dir \<PATH></nobr></span></span> | <span data-ttu-id="ca9ec-215">使用するディレクトリ (およびサブ名前空間)。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-215">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="ca9ec-216">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-216">Paths are relative to the project directory.</span></span> <span data-ttu-id="ca9ec-217">既定値は「移行」です。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-217">Defaults to "Migrations".</span></span> |

### <a name="dotnet-ef-migrations-list"></a><span data-ttu-id="ca9ec-218">dotnet ef migrations 一覧</span><span class="sxs-lookup"><span data-stu-id="ca9ec-218">dotnet ef migrations list</span></span>

<span data-ttu-id="ca9ec-219">使用可能な移行を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-219">Lists available migrations.</span></span>

### <a name="dotnet-ef-migrations-remove"></a><span data-ttu-id="ca9ec-220">dotnet ef migrations を削除します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-220">dotnet ef migrations remove</span></span>

<span data-ttu-id="ca9ec-221">最後の移行を削除します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-221">Removes the last migration.</span></span>

<span data-ttu-id="ca9ec-222">オプション:</span><span class="sxs-lookup"><span data-stu-id="ca9ec-222">Options:</span></span>

|    |         |                                                                       |
|:---|:--------|:----------------------------------------------------------------------|
| <span data-ttu-id="ca9ec-223">-f</span><span class="sxs-lookup"><span data-stu-id="ca9ec-223">-f</span></span> | <span data-ttu-id="ca9ec-224">-強制</span><span class="sxs-lookup"><span data-stu-id="ca9ec-224">--force</span></span> | <span data-ttu-id="ca9ec-225">データベースに適用されている場合は、移行を戻します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-225">Revert the migration if it has been applied to the database.</span></span> |

### <a name="dotnet-ef-migrations-script"></a><span data-ttu-id="ca9ec-226">dotnet ef migrations スクリプト</span><span class="sxs-lookup"><span data-stu-id="ca9ec-226">dotnet ef migrations script</span></span>

<span data-ttu-id="ca9ec-227">移行には、SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-227">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="ca9ec-228">引数:</span><span class="sxs-lookup"><span data-stu-id="ca9ec-228">Arguments:</span></span>

|         |                                                               |
|:--------|:--------------------------------------------------------------|
| <span data-ttu-id="ca9ec-229">\<FROM></span><span class="sxs-lookup"><span data-stu-id="ca9ec-229">\<FROM></span></span> | <span data-ttu-id="ca9ec-230">開始の移行。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-230">The starting migration.</span></span> <span data-ttu-id="ca9ec-231">既定値は 0 (最初のデータベース) です。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-231">Defaults to 0 (the initial database).</span></span> |
| <span data-ttu-id="ca9ec-232">\<TO></span><span class="sxs-lookup"><span data-stu-id="ca9ec-232">\<TO></span></span>   | <span data-ttu-id="ca9ec-233">終了の移行。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-233">The ending migration.</span></span> <span data-ttu-id="ca9ec-234">移行の最後の既定値します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-234">Defaults to the last migration.</span></span>         |

<span data-ttu-id="ca9ec-235">オプション:</span><span class="sxs-lookup"><span data-stu-id="ca9ec-235">Options:</span></span>

|    |                  |                                                                    |
|:---|:-----------------|:-------------------------------------------------------------------|
| <span data-ttu-id="ca9ec-236">-o</span><span class="sxs-lookup"><span data-stu-id="ca9ec-236">-o</span></span> | <span data-ttu-id="ca9ec-237">-出力\<ファイル ></span><span class="sxs-lookup"><span data-stu-id="ca9ec-237">--output \<FILE></span></span> | <span data-ttu-id="ca9ec-238">結果を書き込むファイル。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-238">The file to write the result to.</span></span>                                   |
| <span data-ttu-id="ca9ec-239">-i</span><span class="sxs-lookup"><span data-stu-id="ca9ec-239">-i</span></span> | <span data-ttu-id="ca9ec-240">--idempotent</span><span class="sxs-lookup"><span data-stu-id="ca9ec-240">--idempotent</span></span>     | <span data-ttu-id="ca9ec-241">すべての移行でのデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="ca9ec-241">Generate a script that can be used on a database at any migration.</span></span> |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core
