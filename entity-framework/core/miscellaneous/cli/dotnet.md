---
title: '.NET core CLI: EF コア'
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
ms.openlocfilehash: d053d53bd50d2e7d16223c5b4e4009c9bb2298bb
ms.sourcegitcommit: 038acd91ce2f5a28d76dcd2eab72eeba225e366d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
---
<a name="ef-core-net-command-line-tools"></a><span data-ttu-id="3a687-102">EF コア .NET コマンド ライン ツール</span><span class="sxs-lookup"><span data-stu-id="3a687-102">EF Core .NET Command-line Tools</span></span>
===============================
<span data-ttu-id="3a687-103">Entity Framework Core .NET コマンド ライン ツールは、クロス プラットフォーム拡張機能**dotnet**コマンドでは、一部の[.NET Core SDK][2]です。</span><span class="sxs-lookup"><span data-stu-id="3a687-103">The Entity Framework Core .NET Command-line Tools are an extension to the cross-platform **dotnet** command, which is part of the [.NET Core SDK][2].</span></span>

> [!TIP]
> <span data-ttu-id="3a687-104">Visual Studio を使用するかどうか、ことをお勧め[PMC ツール][ 1]より統合されたエクスペリエンスを提供するために代わりにします。</span><span class="sxs-lookup"><span data-stu-id="3a687-104">If you're using Visual Studio, we recommend [the PMC Tools][1] instead since they provide a more integrated experience.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="3a687-105">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="3a687-105">Installing the tools</span></span>
--------------------
> [!NOTE]
> <span data-ttu-id="3a687-106">.NET Core SDK バージョン 2.1.300 おり、新しい**dotnet ef** EF コア 2.0 およびそれ以降のバージョンと互換性のあるコマンド。</span><span class="sxs-lookup"><span data-stu-id="3a687-106">The .NET Core SDK version 2.1.300 and newer includes **dotnet ef** commands that are compatible with EF Core 2.0 and later versions.</span></span> <span data-ttu-id="3a687-107">したがって最新バージョンの .NET Core SDK と EF Core ランタイムを使用している場合をインストールする必要はありませんし、このセクションの残りの部分を無視することができます。</span><span class="sxs-lookup"><span data-stu-id="3a687-107">Therefore if you are using recent versions of the .NET Core SDK and the EF Core runtime, no installation is required and you can ignore the rest of this section.</span></span>
>
> <span data-ttu-id="3a687-108">その一方で、 **dotnet ef**ツール .NET Core SDK バージョン 2.1.300 に含まれていると、それ以降は EF コア バージョン 1.0 および 1.1 と互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="3a687-108">On the other hand, the **dotnet ef** tool contained in .NET Core SDK version 2.1.300 and newer is not compatible with EF Core version 1.0 and 1.1.</span></span> <span data-ttu-id="3a687-109">.NET Core SDK 2.1.300 をあるコンピューター上で以前のバージョンの EF コアを使用するプロジェクトで作業することができます以降がインストールされて、前にバージョン 2.1.200 もインストールする必要がありますまたは SDK の古い変更してその古いバージョンを使用するアプリケーションを構成して、 [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json)ファイル。</span><span class="sxs-lookup"><span data-stu-id="3a687-109">Before you can work with a project that uses these earlier versions of EF Core on a computer that has .NET Core SDK 2.1.300 or newer installed, you must also install version 2.1.200 or older of the SDK and configure the application to use that older version by modifying its [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json) file.</span></span> <span data-ttu-id="3a687-110">このファイルは、(プロジェクトの上に 1 つ) のソリューション ディレクトリに通常含まれます。</span><span class="sxs-lookup"><span data-stu-id="3a687-110">This file is normally included in the solution directory (one above the project).</span></span> <span data-ttu-id="3a687-111">下の installlation 命令で開始できます。</span><span class="sxs-lookup"><span data-stu-id="3a687-111">Then you can proceed with the installlation instruction below.</span></span>

<span data-ttu-id="3a687-112">以前のバージョンの .NET Core SDK では、次の手順を使用して EF コア .NET コマンド ライン ツールをインストールすることができます。</span><span class="sxs-lookup"><span data-stu-id="3a687-112">For previous versions of the .NET Core SDK, you can install the EF Core .NET Command-line Tools using these steps:</span></span>

1. <span data-ttu-id="3a687-113">プロジェクト ファイルを編集し、DotNetCliToolReference 項目 (下記参照) として Microsoft.EntityFrameworkCore.Tools.DotNet を追加</span><span class="sxs-lookup"><span data-stu-id="3a687-113">Edit the project file and add Microsoft.EntityFrameworkCore.Tools.DotNet as a DotNetCliToolReference item (See below)</span></span>
2. <span data-ttu-id="3a687-114">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="3a687-114">Run the following commands:</span></span>

       dotnet add package Microsoft.EntityFrameworkCore.Design
       dotnet restore

<span data-ttu-id="3a687-115">作成されたプロジェクトは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="3a687-115">The resulting project should look something like this:</span></span>

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
> <span data-ttu-id="3a687-116">パッケージの参照を`PrivateAssets="All"`開発時にのみ使用される通常パッケージに特に便利ですが、このプロジェクトを参照しているプロジェクトに公開されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="3a687-116">A package reference with `PrivateAssets="All"` means it isn't exposed to projects that reference this project, which is especially useful for packages that are typically only used during development.</span></span>

<span data-ttu-id="3a687-117">すべての権利を行った場合は、コマンド プロンプトで次のコマンドを正常に実行することができます。</span><span class="sxs-lookup"><span data-stu-id="3a687-117">If you did everything right, you should be able to successfully run the following command in a command prompt.</span></span>

``` Console
dotnet ef
```

<a name="using-the-tools"></a><span data-ttu-id="3a687-118">ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="3a687-118">Using the tools</span></span>
---------------
<span data-ttu-id="3a687-119">コマンドを呼び出すときに 2 つのプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="3a687-119">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="3a687-120">ターゲット プロジェクトでは、ファイルが追加されます (削除される場合もあります)。</span><span class="sxs-lookup"><span data-stu-id="3a687-120">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="3a687-121">ターゲット プロジェクトが、現在のディレクトリで、プロジェクトの既定値を使用して変更することが、 <nobr> **--プロジェクト**</nobr>オプション。</span><span class="sxs-lookup"><span data-stu-id="3a687-121">The target project defaults to the project in the current directory, but can be changed using the <nobr>**--project**</nobr> option.</span></span>

<span data-ttu-id="3a687-122">スタートアップ プロジェクトは、プロジェクトのコードを実行したとき、ツールによってエミュレートされるプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="3a687-122">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="3a687-123">また、現在のディレクトリで、プロジェクトの既定値を使用して変更することができます、 **--スタートアップ プロジェクト**オプション。</span><span class="sxs-lookup"><span data-stu-id="3a687-123">It also defaults to the project in the current directory, but can be changed using the **--startup-project** option.</span></span>

> [!NOTE]
> <span data-ttu-id="3a687-124">たとえば、次のようなりますを別のプロジェクトにインストールされている EF コアを持つ web アプリケーションのデータベースを更新します`dotnet ef database update --project {project-path}`(からの web アプリのディレクトリ)。</span><span class="sxs-lookup"><span data-stu-id="3a687-124">For instance, updating the database of your web application that has EF Core installed in a different project would look like this: `dotnet ef database update --project {project-path}` (from your web app directory)</span></span>

<span data-ttu-id="3a687-125">一般的なオプション:</span><span class="sxs-lookup"><span data-stu-id="3a687-125">Common options:</span></span>

|    |                                  |                             |
|:---|:---------------------------------|:----------------------------|
|    | <span data-ttu-id="3a687-126">--json</span><span class="sxs-lookup"><span data-stu-id="3a687-126">--json</span></span>                           | <span data-ttu-id="3a687-127">JSON の出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="3a687-127">Show JSON output.</span></span>           |
| <span data-ttu-id="3a687-128">-c</span><span class="sxs-lookup"><span data-stu-id="3a687-128">-c</span></span> | <span data-ttu-id="3a687-129">--context \<DBCONTEXT></span><span class="sxs-lookup"><span data-stu-id="3a687-129">--context \<DBCONTEXT></span></span>           | <span data-ttu-id="3a687-130">使用する DbContext です。</span><span class="sxs-lookup"><span data-stu-id="3a687-130">The DbContext to use.</span></span>       |
| <span data-ttu-id="3a687-131">-p</span><span class="sxs-lookup"><span data-stu-id="3a687-131">-p</span></span> | <span data-ttu-id="3a687-132">--project \<PROJECT></span><span class="sxs-lookup"><span data-stu-id="3a687-132">--project \<PROJECT></span></span>             | <span data-ttu-id="3a687-133">使用するプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="3a687-133">The project to use.</span></span>         |
| <span data-ttu-id="3a687-134">-s</span><span class="sxs-lookup"><span data-stu-id="3a687-134">-s</span></span> | <span data-ttu-id="3a687-135">--startup-project \<PROJECT></span><span class="sxs-lookup"><span data-stu-id="3a687-135">--startup-project \<PROJECT></span></span>     | <span data-ttu-id="3a687-136">使用するスタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="3a687-136">The startup project to use.</span></span> |
|    | <span data-ttu-id="3a687-137">--framework \<FRAMEWORK ></span><span class="sxs-lookup"><span data-stu-id="3a687-137">--framework \<FRAMEWORK></span></span>         | <span data-ttu-id="3a687-138">ターゲット フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="3a687-138">The target framework.</span></span>       |
|    | <span data-ttu-id="3a687-139">--構成\<構成 ></span><span class="sxs-lookup"><span data-stu-id="3a687-139">--configuration \<CONFIGURATION></span></span> | <span data-ttu-id="3a687-140">使用する構成。</span><span class="sxs-lookup"><span data-stu-id="3a687-140">The configuration to use.</span></span>   |
|    | <span data-ttu-id="3a687-141">--ランタイム\<識別子 ></span><span class="sxs-lookup"><span data-stu-id="3a687-141">--runtime \<IDENTIFIER></span></span>          | <span data-ttu-id="3a687-142">使用するランタイム。</span><span class="sxs-lookup"><span data-stu-id="3a687-142">The runtime to use.</span></span>         |
| <span data-ttu-id="3a687-143">-h</span><span class="sxs-lookup"><span data-stu-id="3a687-143">-h</span></span> | <span data-ttu-id="3a687-144">-ヘルプ</span><span class="sxs-lookup"><span data-stu-id="3a687-144">--help</span></span>                           | <span data-ttu-id="3a687-145">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="3a687-145">Show help information.</span></span>      |
| <span data-ttu-id="3a687-146">-v</span><span class="sxs-lookup"><span data-stu-id="3a687-146">-v</span></span> | <span data-ttu-id="3a687-147">-詳細</span><span class="sxs-lookup"><span data-stu-id="3a687-147">--verbose</span></span>                        | <span data-ttu-id="3a687-148">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="3a687-148">Show verbose output.</span></span>        |
|    | <span data-ttu-id="3a687-149">--no-color</span><span class="sxs-lookup"><span data-stu-id="3a687-149">--no-color</span></span>                       | <span data-ttu-id="3a687-150">しないと、出力が色分けして表示します。</span><span class="sxs-lookup"><span data-stu-id="3a687-150">Don't colorize output.</span></span>      |
|    | <span data-ttu-id="3a687-151">--prefix-output</span><span class="sxs-lookup"><span data-stu-id="3a687-151">--prefix-output</span></span>                  | <span data-ttu-id="3a687-152">出力レベルをプレフィックスします。</span><span class="sxs-lookup"><span data-stu-id="3a687-152">Prefix output with level.</span></span>   |


> [!TIP]
> <span data-ttu-id="3a687-153">ASP.NET Core 環境を指定するには、設定、 **ASPNETCORE_ENVIRONMENT**実行する前に環境変数。</span><span class="sxs-lookup"><span data-stu-id="3a687-153">To specify the ASP.NET Core environment, set the **ASPNETCORE_ENVIRONMENT** environment variable before running.</span></span>

<a name="commands"></a><span data-ttu-id="3a687-154">コマンド</span><span class="sxs-lookup"><span data-stu-id="3a687-154">Commands</span></span>
--------

### <a name="dotnet-ef-database-drop"></a><span data-ttu-id="3a687-155">dotnet ef データベースの削除</span><span class="sxs-lookup"><span data-stu-id="3a687-155">dotnet ef database drop</span></span>

<span data-ttu-id="3a687-156">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="3a687-156">Drops the database.</span></span>

<span data-ttu-id="3a687-157">オプション:</span><span class="sxs-lookup"><span data-stu-id="3a687-157">Options:</span></span>

|    |           |                                                          |
|:---|:----------|:---------------------------------------------------------|
| <span data-ttu-id="3a687-158">-f</span><span class="sxs-lookup"><span data-stu-id="3a687-158">-f</span></span> | <span data-ttu-id="3a687-159">--強制</span><span class="sxs-lookup"><span data-stu-id="3a687-159">--force</span></span>   | <span data-ttu-id="3a687-160">しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="3a687-160">Don't confirm.</span></span>                                           |
|    | <span data-ttu-id="3a687-161">--予行</span><span class="sxs-lookup"><span data-stu-id="3a687-161">--dry-run</span></span> | <span data-ttu-id="3a687-162">データベースは削除しますが、それを削除しないを表示します。</span><span class="sxs-lookup"><span data-stu-id="3a687-162">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="dotnet-ef-database-update"></a><span data-ttu-id="3a687-163">dotnet ef データベースの更新</span><span class="sxs-lookup"><span data-stu-id="3a687-163">dotnet ef database update</span></span>

<span data-ttu-id="3a687-164">指定された移行するには、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="3a687-164">Updates the database to a specified migration.</span></span>

<span data-ttu-id="3a687-165">引数:</span><span class="sxs-lookup"><span data-stu-id="3a687-165">Arguments:</span></span>

|              |                                                                                              |
|:-------------|:---------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a687-166">\<移行 &GT;</span><span class="sxs-lookup"><span data-stu-id="3a687-166">\<MIGRATION></span></span> | <span data-ttu-id="3a687-167">Target の移行。</span><span class="sxs-lookup"><span data-stu-id="3a687-167">The target migration.</span></span> <span data-ttu-id="3a687-168">0 の場合、すべての移行は元に戻せません。</span><span class="sxs-lookup"><span data-stu-id="3a687-168">If 0, all migrations will be reverted.</span></span> <span data-ttu-id="3a687-169">既定値は最後に移行します。</span><span class="sxs-lookup"><span data-stu-id="3a687-169">Defaults to the last migration.</span></span> |

### <a name="dotnet-ef-dbcontext-info"></a><span data-ttu-id="3a687-170">dotnet ef dbcontext 情報</span><span class="sxs-lookup"><span data-stu-id="3a687-170">dotnet ef dbcontext info</span></span>

<span data-ttu-id="3a687-171">DbContext 型に関する情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="3a687-171">Gets information about a DbContext type.</span></span>

### <a name="dotnet-ef-dbcontext-list"></a><span data-ttu-id="3a687-172">dotnet ef dbcontext 一覧</span><span class="sxs-lookup"><span data-stu-id="3a687-172">dotnet ef dbcontext list</span></span>

<span data-ttu-id="3a687-173">使用可能な DbContext 型を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="3a687-173">Lists available DbContext types.</span></span>

### <a name="dotnet-ef-dbcontext-scaffold"></a><span data-ttu-id="3a687-174">dotnet ef dbcontext scaffold</span><span class="sxs-lookup"><span data-stu-id="3a687-174">dotnet ef dbcontext scaffold</span></span>

<span data-ttu-id="3a687-175">スキャフォールディング データベースの DbContext とエンティティの型。</span><span class="sxs-lookup"><span data-stu-id="3a687-175">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="3a687-176">引数:</span><span class="sxs-lookup"><span data-stu-id="3a687-176">Arguments:</span></span>

|               |                                                                     |
|:--------------|:--------------------------------------------------------------------|
| <span data-ttu-id="3a687-177">\<接続 &GT;</span><span class="sxs-lookup"><span data-stu-id="3a687-177">\<CONNECTION></span></span> | <span data-ttu-id="3a687-178">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="3a687-178">The connection string to the database.</span></span>                              |
| <span data-ttu-id="3a687-179">\<プロバイダー &GT;</span><span class="sxs-lookup"><span data-stu-id="3a687-179">\<PROVIDER></span></span>   | <span data-ttu-id="3a687-180">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="3a687-180">The provider to use.</span></span> <span data-ttu-id="3a687-181">(例:</span><span class="sxs-lookup"><span data-stu-id="3a687-181">(E.g.</span></span> <span data-ttu-id="3a687-182">Microsoft.EntityFrameworkCore.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="3a687-182">Microsoft.EntityFrameworkCore.SqlServer)</span></span> |

<span data-ttu-id="3a687-183">オプション:</span><span class="sxs-lookup"><span data-stu-id="3a687-183">Options:</span></span>

|                 |                                         |                                                                                                  |
|:----------------|:----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a687-184"><nobr>-d</nobr></span><span class="sxs-lookup"><span data-stu-id="3a687-184"><nobr>-d</nobr></span></span> | <span data-ttu-id="3a687-185">-データ注釈</span><span class="sxs-lookup"><span data-stu-id="3a687-185">--data-annotations</span></span>                      | <span data-ttu-id="3a687-186">属性を使用すると、(可能な場合)、モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="3a687-186">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="3a687-187">省略すると、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="3a687-187">If omitted, only the fluent API is used.</span></span> |
| <span data-ttu-id="3a687-188">-c</span><span class="sxs-lookup"><span data-stu-id="3a687-188">-c</span></span>              | <span data-ttu-id="3a687-189">--context \<NAME></span><span class="sxs-lookup"><span data-stu-id="3a687-189">--context \<NAME></span></span>                       | <span data-ttu-id="3a687-190">DbContext の名前。</span><span class="sxs-lookup"><span data-stu-id="3a687-190">The name of the DbContext.</span></span>                                                                       |
|                 | <span data-ttu-id="3a687-191">--コンテキスト dir\<パス ></span><span class="sxs-lookup"><span data-stu-id="3a687-191">--context-dir \<PATH></span></span>                   | <span data-ttu-id="3a687-192">DbContext ファイルを配置するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="3a687-192">The directory to put DbContext file in.</span></span> <span data-ttu-id="3a687-193">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="3a687-193">Paths are relative to the project directory.</span></span>             |
| <span data-ttu-id="3a687-194">-f</span><span class="sxs-lookup"><span data-stu-id="3a687-194">-f</span></span>              | <span data-ttu-id="3a687-195">--強制</span><span class="sxs-lookup"><span data-stu-id="3a687-195">--force</span></span>                                 | <span data-ttu-id="3a687-196">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="3a687-196">Overwrite existing files.</span></span>                                                                        |
| <span data-ttu-id="3a687-197">-o</span><span class="sxs-lookup"><span data-stu-id="3a687-197">-o</span></span>              | <span data-ttu-id="3a687-198">--output-dir \<PATH></span><span class="sxs-lookup"><span data-stu-id="3a687-198">--output-dir \<PATH></span></span>                    | <span data-ttu-id="3a687-199">ファイルを置くディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="3a687-199">The directory to put files in.</span></span> <span data-ttu-id="3a687-200">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="3a687-200">Paths are relative to the project directory.</span></span>                      |
|                 | <span data-ttu-id="3a687-201"><nobr>-スキーマ\<SCHEMA_NAME >.</nobr></span><span class="sxs-lookup"><span data-stu-id="3a687-201"><nobr>--schema \<SCHEMA_NAME>...</nobr></span></span> | <span data-ttu-id="3a687-202">エンティティ型を生成するテーブルのスキーマです。</span><span class="sxs-lookup"><span data-stu-id="3a687-202">The schemas of tables to generate entity types for.</span></span>                                              |
| <span data-ttu-id="3a687-203">-t</span><span class="sxs-lookup"><span data-stu-id="3a687-203">-t</span></span>              | <span data-ttu-id="3a687-204">-テーブル\<TABLE_NAME >.</span><span class="sxs-lookup"><span data-stu-id="3a687-204">--table \<TABLE_NAME>...</span></span>                | <span data-ttu-id="3a687-205">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="3a687-205">The tables to generate entity types for.</span></span>                                                         |
|                 | <span data-ttu-id="3a687-206">--use-database-names</span><span class="sxs-lookup"><span data-stu-id="3a687-206">--use-database-names</span></span>                    | <span data-ttu-id="3a687-207">データベースから直接テーブルおよび列名を使用します。</span><span class="sxs-lookup"><span data-stu-id="3a687-207">Use table and column names directly from the database.</span></span>                                           |

### <a name="dotnet-ef-migrations-add"></a><span data-ttu-id="3a687-208">dotnet ef 移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="3a687-208">dotnet ef migrations add</span></span>

<span data-ttu-id="3a687-209">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="3a687-209">Adds a new migration.</span></span>

<span data-ttu-id="3a687-210">引数:</span><span class="sxs-lookup"><span data-stu-id="3a687-210">Arguments:</span></span>

|         |                            |
|:--------|:---------------------------|
| <span data-ttu-id="3a687-211">\<NAME></span><span class="sxs-lookup"><span data-stu-id="3a687-211">\<NAME></span></span> | <span data-ttu-id="3a687-212">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="3a687-212">The name of the migration.</span></span> |

<span data-ttu-id="3a687-213">オプション:</span><span class="sxs-lookup"><span data-stu-id="3a687-213">Options:</span></span>

|                 |                                   |                                                                                                                  |
|:----------------|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a687-214"><nobr>-o</nobr></span><span class="sxs-lookup"><span data-stu-id="3a687-214"><nobr>-o</nobr></span></span> | <span data-ttu-id="3a687-215"><nobr>--output-dir \<PATH></nobr></span><span class="sxs-lookup"><span data-stu-id="3a687-215"><nobr>--output-dir \<PATH></nobr></span></span> | <span data-ttu-id="3a687-216">ディレクトリ (およびサブ名前空間) を使用します。</span><span class="sxs-lookup"><span data-stu-id="3a687-216">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="3a687-217">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="3a687-217">Paths are relative to the project directory.</span></span> <span data-ttu-id="3a687-218">既定値は「移行」です。</span><span class="sxs-lookup"><span data-stu-id="3a687-218">Defaults to "Migrations".</span></span> |

### <a name="dotnet-ef-migrations-list"></a><span data-ttu-id="3a687-219">dotnet ef 移行リスト</span><span class="sxs-lookup"><span data-stu-id="3a687-219">dotnet ef migrations list</span></span>

<span data-ttu-id="3a687-220">使用可能な移行を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="3a687-220">Lists available migrations.</span></span>

### <a name="dotnet-ef-migrations-remove"></a><span data-ttu-id="3a687-221">dotnet ef 移行を削除します。</span><span class="sxs-lookup"><span data-stu-id="3a687-221">dotnet ef migrations remove</span></span>

<span data-ttu-id="3a687-222">前回の移行を削除します。</span><span class="sxs-lookup"><span data-stu-id="3a687-222">Removes the last migration.</span></span>

<span data-ttu-id="3a687-223">オプション:</span><span class="sxs-lookup"><span data-stu-id="3a687-223">Options:</span></span>

|    |         |                                                                       |
|:---|:--------|:----------------------------------------------------------------------|
| <span data-ttu-id="3a687-224">-f</span><span class="sxs-lookup"><span data-stu-id="3a687-224">-f</span></span> | <span data-ttu-id="3a687-225">--強制</span><span class="sxs-lookup"><span data-stu-id="3a687-225">--force</span></span> | <span data-ttu-id="3a687-226">それがデータベースに適用された場合は、移行を戻します。</span><span class="sxs-lookup"><span data-stu-id="3a687-226">Revert the migration if it has been applied to the database.</span></span> |

### <a name="dotnet-ef-migrations-script"></a><span data-ttu-id="3a687-227">dotnet ef 移行スクリプト</span><span class="sxs-lookup"><span data-stu-id="3a687-227">dotnet ef migrations script</span></span>

<span data-ttu-id="3a687-228">移行からの SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="3a687-228">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="3a687-229">引数:</span><span class="sxs-lookup"><span data-stu-id="3a687-229">Arguments:</span></span>

|         |                                                               |
|:--------|:--------------------------------------------------------------|
| <span data-ttu-id="3a687-230">\<FROM></span><span class="sxs-lookup"><span data-stu-id="3a687-230">\<FROM></span></span> | <span data-ttu-id="3a687-231">開始の移行。</span><span class="sxs-lookup"><span data-stu-id="3a687-231">The starting migration.</span></span> <span data-ttu-id="3a687-232">既定値は 0 (最初のデータベース)。</span><span class="sxs-lookup"><span data-stu-id="3a687-232">Defaults to 0 (the initial database).</span></span> |
| <span data-ttu-id="3a687-233">\<TO></span><span class="sxs-lookup"><span data-stu-id="3a687-233">\<TO></span></span>   | <span data-ttu-id="3a687-234">終了の移行。</span><span class="sxs-lookup"><span data-stu-id="3a687-234">The ending migration.</span></span> <span data-ttu-id="3a687-235">既定値は最後に移行します。</span><span class="sxs-lookup"><span data-stu-id="3a687-235">Defaults to the last migration.</span></span>         |

<span data-ttu-id="3a687-236">オプション:</span><span class="sxs-lookup"><span data-stu-id="3a687-236">Options:</span></span>

|    |                  |                                                                    |
|:---|:-----------------|:-------------------------------------------------------------------|
| <span data-ttu-id="3a687-237">-o</span><span class="sxs-lookup"><span data-stu-id="3a687-237">-o</span></span> | <span data-ttu-id="3a687-238">--出力\<ファイル ></span><span class="sxs-lookup"><span data-stu-id="3a687-238">--output \<FILE></span></span> | <span data-ttu-id="3a687-239">結果を書き込むファイルです。</span><span class="sxs-lookup"><span data-stu-id="3a687-239">The file to write the result to.</span></span>                                   |
| <span data-ttu-id="3a687-240">-i</span><span class="sxs-lookup"><span data-stu-id="3a687-240">-i</span></span> | <span data-ttu-id="3a687-241">--idempotent</span><span class="sxs-lookup"><span data-stu-id="3a687-241">--idempotent</span></span>     | <span data-ttu-id="3a687-242">すべての移行でのデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="3a687-242">Generate a script that can be used on a database at any migration.</span></span> |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core
