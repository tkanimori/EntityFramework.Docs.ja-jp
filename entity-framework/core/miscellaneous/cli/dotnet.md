---
title: '.NET core CLI: EF コア'
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
uid: core/miscellaneous/cli/dotnet
ms.openlocfilehash: 721235b07e695efd8df43294e1f4e90c28ae83d7
ms.sourcegitcommit: 72e59e6af86b568653e1b29727529dfd7f65d312
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34754497"
---
<a name="ef-core-net-command-line-tools"></a><span data-ttu-id="9f24e-102">EF コア .NET コマンド ライン ツール</span><span class="sxs-lookup"><span data-stu-id="9f24e-102">EF Core .NET Command-line Tools</span></span>
===============================
<span data-ttu-id="9f24e-103">Entity Framework Core .NET コマンド ライン ツールは、クロス プラットフォーム拡張機能**dotnet**コマンドでは、一部の[.NET Core SDK][2]です。</span><span class="sxs-lookup"><span data-stu-id="9f24e-103">The Entity Framework Core .NET Command-line Tools are an extension to the cross-platform **dotnet** command, which is part of the [.NET Core SDK][2].</span></span>

> [!TIP]
> <span data-ttu-id="9f24e-104">Visual Studio を使用するかどうか、ことをお勧め[PMC ツール][ 1]より統合されたエクスペリエンスを提供するために代わりにします。</span><span class="sxs-lookup"><span data-stu-id="9f24e-104">If you're using Visual Studio, we recommend [the PMC Tools][1] instead since they provide a more integrated experience.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="9f24e-105">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="9f24e-105">Installing the tools</span></span>
--------------------
> [!NOTE]
> <span data-ttu-id="9f24e-106">.NET Core SDK バージョン 2.1.300 おり、新しい**dotnet ef** EF コア 2.0 およびそれ以降のバージョンと互換性のあるコマンド。</span><span class="sxs-lookup"><span data-stu-id="9f24e-106">The .NET Core SDK version 2.1.300 and newer includes **dotnet ef** commands that are compatible with EF Core 2.0 and later versions.</span></span> <span data-ttu-id="9f24e-107">したがって最新バージョンの .NET Core SDK と EF Core ランタイムを使用している場合をインストールする必要はありませんし、このセクションの残りの部分を無視することができます。</span><span class="sxs-lookup"><span data-stu-id="9f24e-107">Therefore if you are using recent versions of the .NET Core SDK and the EF Core runtime, no installation is required and you can ignore the rest of this section.</span></span>
>
> <span data-ttu-id="9f24e-108">その一方で、 **dotnet ef**ツール .NET Core SDK バージョン 2.1.300 に含まれていると、それ以降は EF コア バージョン 1.0 および 1.1 と互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="9f24e-108">On the other hand, the **dotnet ef** tool contained in .NET Core SDK version 2.1.300 and newer is not compatible with EF Core version 1.0 and 1.1.</span></span> <span data-ttu-id="9f24e-109">.NET Core SDK 2.1.300 をあるコンピューター上で以前のバージョンの EF コアを使用するプロジェクトで作業することができます以降がインストールされて、前にバージョン 2.1.200 もインストールする必要がありますまたは SDK の古い変更してその古いバージョンを使用するアプリケーションを構成して、 [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json)ファイル。</span><span class="sxs-lookup"><span data-stu-id="9f24e-109">Before you can work with a project that uses these earlier versions of EF Core on a computer that has .NET Core SDK 2.1.300 or newer installed, you must also install version 2.1.200 or older of the SDK and configure the application to use that older version by modifying its [global.json](https://docs.microsoft.com/en-us/dotnet/core/tools/global-json) file.</span></span> <span data-ttu-id="9f24e-110">このファイルは、(プロジェクトの上に 1 つ) のソリューション ディレクトリに通常含まれます。</span><span class="sxs-lookup"><span data-stu-id="9f24e-110">This file is normally included in the solution directory (one above the project).</span></span> <span data-ttu-id="9f24e-111">下の installlation 命令で開始できます。</span><span class="sxs-lookup"><span data-stu-id="9f24e-111">Then you can proceed with the installlation instruction below.</span></span>

<span data-ttu-id="9f24e-112">以前のバージョンの .NET Core SDK では、次の手順を使用して EF コア .NET コマンド ライン ツールをインストールすることができます。</span><span class="sxs-lookup"><span data-stu-id="9f24e-112">For previous versions of the .NET Core SDK, you can install the EF Core .NET Command-line Tools using these steps:</span></span>

1. <span data-ttu-id="9f24e-113">プロジェクト ファイルを編集し、DotNetCliToolReference 項目 (下記参照) として Microsoft.EntityFrameworkCore.Tools.DotNet を追加</span><span class="sxs-lookup"><span data-stu-id="9f24e-113">Edit the project file and add Microsoft.EntityFrameworkCore.Tools.DotNet as a DotNetCliToolReference item (See below)</span></span>
2. <span data-ttu-id="9f24e-114">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-114">Run the following commands:</span></span>

       dotnet add package Microsoft.EntityFrameworkCore.Design
       dotnet restore

<span data-ttu-id="9f24e-115">作成されたプロジェクトは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9f24e-115">The resulting project should look something like this:</span></span>

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
> <span data-ttu-id="9f24e-116">パッケージの参照を`PrivateAssets="All"`開発時にのみ使用される通常パッケージに特に便利ですが、このプロジェクトを参照しているプロジェクトに公開されていないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-116">A package reference with `PrivateAssets="All"` means it isn't exposed to projects that reference this project, which is especially useful for packages that are typically only used during development.</span></span>

<span data-ttu-id="9f24e-117">すべての権利を行った場合は、コマンド プロンプトで次のコマンドを正常に実行することができます。</span><span class="sxs-lookup"><span data-stu-id="9f24e-117">If you did everything right, you should be able to successfully run the following command in a command prompt.</span></span>

``` Console
dotnet ef
```

<a name="using-the-tools"></a><span data-ttu-id="9f24e-118">ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-118">Using the tools</span></span>
---------------
<span data-ttu-id="9f24e-119">コマンドを呼び出すときに 2 つのプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="9f24e-119">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="9f24e-120">ターゲット プロジェクトでは、ファイルが追加されます (削除される場合もあります)。</span><span class="sxs-lookup"><span data-stu-id="9f24e-120">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="9f24e-121">ターゲット プロジェクトが、現在のディレクトリで、プロジェクトの既定値を使用して変更することが、 <nobr> **--プロジェクト**</nobr>オプション。</span><span class="sxs-lookup"><span data-stu-id="9f24e-121">The target project defaults to the project in the current directory, but can be changed using the <nobr>**--project**</nobr> option.</span></span>

<span data-ttu-id="9f24e-122">スタートアップ プロジェクトは、プロジェクトのコードを実行したとき、ツールによってエミュレートされるプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="9f24e-122">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="9f24e-123">また、現在のディレクトリで、プロジェクトの既定値を使用して変更することができます、 **--スタートアップ プロジェクト**オプション。</span><span class="sxs-lookup"><span data-stu-id="9f24e-123">It also defaults to the project in the current directory, but can be changed using the **--startup-project** option.</span></span>

> [!NOTE]
> <span data-ttu-id="9f24e-124">たとえば、次のようなりますを別のプロジェクトにインストールされている EF コアを持つ web アプリケーションのデータベースを更新します`dotnet ef database update --project {project-path}`(からの web アプリのディレクトリ)。</span><span class="sxs-lookup"><span data-stu-id="9f24e-124">For instance, updating the database of your web application that has EF Core installed in a different project would look like this: `dotnet ef database update --project {project-path}` (from your web app directory)</span></span>

<span data-ttu-id="9f24e-125">一般的なオプション:</span><span class="sxs-lookup"><span data-stu-id="9f24e-125">Common options:</span></span>

|    |                                  |                             |
|:---|:---------------------------------|:----------------------------|
|    | <span data-ttu-id="9f24e-126">--json</span><span class="sxs-lookup"><span data-stu-id="9f24e-126">--json</span></span>                           | <span data-ttu-id="9f24e-127">JSON の出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-127">Show JSON output.</span></span>           |
| <span data-ttu-id="9f24e-128">-c</span><span class="sxs-lookup"><span data-stu-id="9f24e-128">-c</span></span> | <span data-ttu-id="9f24e-129">--context \<DBCONTEXT></span><span class="sxs-lookup"><span data-stu-id="9f24e-129">--context \<DBCONTEXT></span></span>           | <span data-ttu-id="9f24e-130">使用する DbContext です。</span><span class="sxs-lookup"><span data-stu-id="9f24e-130">The DbContext to use.</span></span>       |
| <span data-ttu-id="9f24e-131">-p</span><span class="sxs-lookup"><span data-stu-id="9f24e-131">-p</span></span> | <span data-ttu-id="9f24e-132">--project \<PROJECT></span><span class="sxs-lookup"><span data-stu-id="9f24e-132">--project \<PROJECT></span></span>             | <span data-ttu-id="9f24e-133">使用するプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="9f24e-133">The project to use.</span></span>         |
| <span data-ttu-id="9f24e-134">-s</span><span class="sxs-lookup"><span data-stu-id="9f24e-134">-s</span></span> | <span data-ttu-id="9f24e-135">--startup-project \<PROJECT></span><span class="sxs-lookup"><span data-stu-id="9f24e-135">--startup-project \<PROJECT></span></span>     | <span data-ttu-id="9f24e-136">使用するスタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="9f24e-136">The startup project to use.</span></span> |
|    | <span data-ttu-id="9f24e-137">--framework \<FRAMEWORK ></span><span class="sxs-lookup"><span data-stu-id="9f24e-137">--framework \<FRAMEWORK></span></span>         | <span data-ttu-id="9f24e-138">ターゲット フレームワーク。</span><span class="sxs-lookup"><span data-stu-id="9f24e-138">The target framework.</span></span>       |
|    | <span data-ttu-id="9f24e-139">--構成\<構成 ></span><span class="sxs-lookup"><span data-stu-id="9f24e-139">--configuration \<CONFIGURATION></span></span> | <span data-ttu-id="9f24e-140">使用する構成。</span><span class="sxs-lookup"><span data-stu-id="9f24e-140">The configuration to use.</span></span>   |
|    | <span data-ttu-id="9f24e-141">--ランタイム\<識別子 ></span><span class="sxs-lookup"><span data-stu-id="9f24e-141">--runtime \<IDENTIFIER></span></span>          | <span data-ttu-id="9f24e-142">使用するランタイム。</span><span class="sxs-lookup"><span data-stu-id="9f24e-142">The runtime to use.</span></span>         |
| <span data-ttu-id="9f24e-143">-h</span><span class="sxs-lookup"><span data-stu-id="9f24e-143">-h</span></span> | <span data-ttu-id="9f24e-144">-ヘルプ</span><span class="sxs-lookup"><span data-stu-id="9f24e-144">--help</span></span>                           | <span data-ttu-id="9f24e-145">ヘルプ情報を表示します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-145">Show help information.</span></span>      |
| <span data-ttu-id="9f24e-146">-v</span><span class="sxs-lookup"><span data-stu-id="9f24e-146">-v</span></span> | <span data-ttu-id="9f24e-147">-詳細</span><span class="sxs-lookup"><span data-stu-id="9f24e-147">--verbose</span></span>                        | <span data-ttu-id="9f24e-148">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-148">Show verbose output.</span></span>        |
|    | <span data-ttu-id="9f24e-149">--no-color</span><span class="sxs-lookup"><span data-stu-id="9f24e-149">--no-color</span></span>                       | <span data-ttu-id="9f24e-150">しないと、出力が色分けして表示します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-150">Don't colorize output.</span></span>      |
|    | <span data-ttu-id="9f24e-151">--prefix-output</span><span class="sxs-lookup"><span data-stu-id="9f24e-151">--prefix-output</span></span>                  | <span data-ttu-id="9f24e-152">出力レベルをプレフィックスします。</span><span class="sxs-lookup"><span data-stu-id="9f24e-152">Prefix output with level.</span></span>   |


> [!TIP]
> <span data-ttu-id="9f24e-153">ASP.NET Core 環境を指定するには、設定、 **ASPNETCORE_ENVIRONMENT**実行する前に環境変数。</span><span class="sxs-lookup"><span data-stu-id="9f24e-153">To specify the ASP.NET Core environment, set the **ASPNETCORE_ENVIRONMENT** environment variable before running.</span></span>

<a name="commands"></a><span data-ttu-id="9f24e-154">コマンド</span><span class="sxs-lookup"><span data-stu-id="9f24e-154">Commands</span></span>
--------

### <a name="dotnet-ef-database-drop"></a><span data-ttu-id="9f24e-155">dotnet ef データベースの削除</span><span class="sxs-lookup"><span data-stu-id="9f24e-155">dotnet ef database drop</span></span>

<span data-ttu-id="9f24e-156">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-156">Drops the database.</span></span>

<span data-ttu-id="9f24e-157">オプション:</span><span class="sxs-lookup"><span data-stu-id="9f24e-157">Options:</span></span>

|    |           |                                                          |
|:---|:----------|:---------------------------------------------------------|
| <span data-ttu-id="9f24e-158">-f</span><span class="sxs-lookup"><span data-stu-id="9f24e-158">-f</span></span> | <span data-ttu-id="9f24e-159">--強制</span><span class="sxs-lookup"><span data-stu-id="9f24e-159">--force</span></span>   | <span data-ttu-id="9f24e-160">しないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-160">Don't confirm.</span></span>                                           |
|    | <span data-ttu-id="9f24e-161">--予行</span><span class="sxs-lookup"><span data-stu-id="9f24e-161">--dry-run</span></span> | <span data-ttu-id="9f24e-162">データベースは削除しますが、それを削除しないを表示します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-162">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="dotnet-ef-database-update"></a><span data-ttu-id="9f24e-163">dotnet ef データベースの更新</span><span class="sxs-lookup"><span data-stu-id="9f24e-163">dotnet ef database update</span></span>

<span data-ttu-id="9f24e-164">指定された移行するには、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-164">Updates the database to a specified migration.</span></span>

<span data-ttu-id="9f24e-165">引数:</span><span class="sxs-lookup"><span data-stu-id="9f24e-165">Arguments:</span></span>

|              |                                                                                              |
|:-------------|:---------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f24e-166">\<移行 &GT;</span><span class="sxs-lookup"><span data-stu-id="9f24e-166">\<MIGRATION></span></span> | <span data-ttu-id="9f24e-167">Target の移行。</span><span class="sxs-lookup"><span data-stu-id="9f24e-167">The target migration.</span></span> <span data-ttu-id="9f24e-168">0 の場合、すべての移行は元に戻せません。</span><span class="sxs-lookup"><span data-stu-id="9f24e-168">If 0, all migrations will be reverted.</span></span> <span data-ttu-id="9f24e-169">既定値は最後に移行します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-169">Defaults to the last migration.</span></span> |

### <a name="dotnet-ef-dbcontext-info"></a><span data-ttu-id="9f24e-170">dotnet ef dbcontext 情報</span><span class="sxs-lookup"><span data-stu-id="9f24e-170">dotnet ef dbcontext info</span></span>

<span data-ttu-id="9f24e-171">DbContext 型に関する情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-171">Gets information about a DbContext type.</span></span>

### <a name="dotnet-ef-dbcontext-list"></a><span data-ttu-id="9f24e-172">dotnet ef dbcontext 一覧</span><span class="sxs-lookup"><span data-stu-id="9f24e-172">dotnet ef dbcontext list</span></span>

<span data-ttu-id="9f24e-173">使用可能な DbContext 型を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-173">Lists available DbContext types.</span></span>

### <a name="dotnet-ef-dbcontext-scaffold"></a><span data-ttu-id="9f24e-174">dotnet ef dbcontext scaffold</span><span class="sxs-lookup"><span data-stu-id="9f24e-174">dotnet ef dbcontext scaffold</span></span>

<span data-ttu-id="9f24e-175">スキャフォールディング データベースの DbContext とエンティティの型。</span><span class="sxs-lookup"><span data-stu-id="9f24e-175">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="9f24e-176">引数:</span><span class="sxs-lookup"><span data-stu-id="9f24e-176">Arguments:</span></span>

|               |                                                                             |
|:--------------|:----------------------------------------------------------------------------|
| <span data-ttu-id="9f24e-177">\<接続 &GT;</span><span class="sxs-lookup"><span data-stu-id="9f24e-177">\<CONNECTION></span></span> | <span data-ttu-id="9f24e-178">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="9f24e-178">The connection string to the database.</span></span>                                      |
| <span data-ttu-id="9f24e-179">\<プロバイダー &GT;</span><span class="sxs-lookup"><span data-stu-id="9f24e-179">\<PROVIDER></span></span>   | <span data-ttu-id="9f24e-180">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="9f24e-180">The provider to use.</span></span> <span data-ttu-id="9f24e-181">(たとえば、Microsoft.EntityFrameworkCore.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="9f24e-181">(for example, Microsoft.EntityFrameworkCore.SqlServer)</span></span> |

<span data-ttu-id="9f24e-182">オプション:</span><span class="sxs-lookup"><span data-stu-id="9f24e-182">Options:</span></span>

|                 |                                         |                                                                                                  |
|:----------------|:----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f24e-183"><nobr>-d</nobr></span><span class="sxs-lookup"><span data-stu-id="9f24e-183"><nobr>-d</nobr></span></span> | <span data-ttu-id="9f24e-184">-データ注釈</span><span class="sxs-lookup"><span data-stu-id="9f24e-184">--data-annotations</span></span>                      | <span data-ttu-id="9f24e-185">属性を使用すると、(可能な場合)、モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9f24e-185">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="9f24e-186">省略すると、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="9f24e-186">If omitted, only the fluent API is used.</span></span> |
| <span data-ttu-id="9f24e-187">-c</span><span class="sxs-lookup"><span data-stu-id="9f24e-187">-c</span></span>              | <span data-ttu-id="9f24e-188">--context \<NAME></span><span class="sxs-lookup"><span data-stu-id="9f24e-188">--context \<NAME></span></span>                       | <span data-ttu-id="9f24e-189">DbContext の名前。</span><span class="sxs-lookup"><span data-stu-id="9f24e-189">The name of the DbContext.</span></span>                                                                       |
|                 | <span data-ttu-id="9f24e-190">--コンテキスト dir\<パス ></span><span class="sxs-lookup"><span data-stu-id="9f24e-190">--context-dir \<PATH></span></span>                   | <span data-ttu-id="9f24e-191">DbContext ファイルを配置するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="9f24e-191">The directory to put DbContext file in.</span></span> <span data-ttu-id="9f24e-192">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="9f24e-192">Paths are relative to the project directory.</span></span>             |
| <span data-ttu-id="9f24e-193">-f</span><span class="sxs-lookup"><span data-stu-id="9f24e-193">-f</span></span>              | <span data-ttu-id="9f24e-194">--強制</span><span class="sxs-lookup"><span data-stu-id="9f24e-194">--force</span></span>                                 | <span data-ttu-id="9f24e-195">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="9f24e-195">Overwrite existing files.</span></span>                                                                        |
| <span data-ttu-id="9f24e-196">-o</span><span class="sxs-lookup"><span data-stu-id="9f24e-196">-o</span></span>              | <span data-ttu-id="9f24e-197">--output-dir \<PATH></span><span class="sxs-lookup"><span data-stu-id="9f24e-197">--output-dir \<PATH></span></span>                    | <span data-ttu-id="9f24e-198">ファイルを置くディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="9f24e-198">The directory to put files in.</span></span> <span data-ttu-id="9f24e-199">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="9f24e-199">Paths are relative to the project directory.</span></span>                      |
|                 | <span data-ttu-id="9f24e-200"><nobr>-スキーマ\<SCHEMA_NAME >.</nobr></span><span class="sxs-lookup"><span data-stu-id="9f24e-200"><nobr>--schema \<SCHEMA_NAME>...</nobr></span></span> | <span data-ttu-id="9f24e-201">エンティティ型を生成するテーブルのスキーマです。</span><span class="sxs-lookup"><span data-stu-id="9f24e-201">The schemas of tables to generate entity types for.</span></span>                                              |
| <span data-ttu-id="9f24e-202">-t</span><span class="sxs-lookup"><span data-stu-id="9f24e-202">-t</span></span>              | <span data-ttu-id="9f24e-203">-テーブル\<TABLE_NAME >.</span><span class="sxs-lookup"><span data-stu-id="9f24e-203">--table \<TABLE_NAME>...</span></span>                | <span data-ttu-id="9f24e-204">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="9f24e-204">The tables to generate entity types for.</span></span>                                                         |
|                 | <span data-ttu-id="9f24e-205">--use-database-names</span><span class="sxs-lookup"><span data-stu-id="9f24e-205">--use-database-names</span></span>                    | <span data-ttu-id="9f24e-206">データベースから直接テーブルおよび列名を使用します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-206">Use table and column names directly from the database.</span></span>                                           |

### <a name="dotnet-ef-migrations-add"></a><span data-ttu-id="9f24e-207">dotnet ef 移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-207">dotnet ef migrations add</span></span>

<span data-ttu-id="9f24e-208">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-208">Adds a new migration.</span></span>

<span data-ttu-id="9f24e-209">引数:</span><span class="sxs-lookup"><span data-stu-id="9f24e-209">Arguments:</span></span>

|         |                            |
|:--------|:---------------------------|
| <span data-ttu-id="9f24e-210">\<NAME></span><span class="sxs-lookup"><span data-stu-id="9f24e-210">\<NAME></span></span> | <span data-ttu-id="9f24e-211">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="9f24e-211">The name of the migration.</span></span> |

<span data-ttu-id="9f24e-212">オプション:</span><span class="sxs-lookup"><span data-stu-id="9f24e-212">Options:</span></span>

|                 |                                   |                                                                                                                  |
|:----------------|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="9f24e-213"><nobr>-o</nobr></span><span class="sxs-lookup"><span data-stu-id="9f24e-213"><nobr>-o</nobr></span></span> | <span data-ttu-id="9f24e-214"><nobr>--output-dir \<PATH></nobr></span><span class="sxs-lookup"><span data-stu-id="9f24e-214"><nobr>--output-dir \<PATH></nobr></span></span> | <span data-ttu-id="9f24e-215">ディレクトリ (およびサブ名前空間) を使用します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-215">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="9f24e-216">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="9f24e-216">Paths are relative to the project directory.</span></span> <span data-ttu-id="9f24e-217">既定値は「移行」です。</span><span class="sxs-lookup"><span data-stu-id="9f24e-217">Defaults to "Migrations".</span></span> |

### <a name="dotnet-ef-migrations-list"></a><span data-ttu-id="9f24e-218">dotnet ef 移行リスト</span><span class="sxs-lookup"><span data-stu-id="9f24e-218">dotnet ef migrations list</span></span>

<span data-ttu-id="9f24e-219">使用可能な移行を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-219">Lists available migrations.</span></span>

### <a name="dotnet-ef-migrations-remove"></a><span data-ttu-id="9f24e-220">dotnet ef 移行を削除します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-220">dotnet ef migrations remove</span></span>

<span data-ttu-id="9f24e-221">前回の移行を削除します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-221">Removes the last migration.</span></span>

<span data-ttu-id="9f24e-222">オプション:</span><span class="sxs-lookup"><span data-stu-id="9f24e-222">Options:</span></span>

|    |         |                                                                       |
|:---|:--------|:----------------------------------------------------------------------|
| <span data-ttu-id="9f24e-223">-f</span><span class="sxs-lookup"><span data-stu-id="9f24e-223">-f</span></span> | <span data-ttu-id="9f24e-224">--強制</span><span class="sxs-lookup"><span data-stu-id="9f24e-224">--force</span></span> | <span data-ttu-id="9f24e-225">それがデータベースに適用された場合は、移行を戻します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-225">Revert the migration if it has been applied to the database.</span></span> |

### <a name="dotnet-ef-migrations-script"></a><span data-ttu-id="9f24e-226">dotnet ef 移行スクリプト</span><span class="sxs-lookup"><span data-stu-id="9f24e-226">dotnet ef migrations script</span></span>

<span data-ttu-id="9f24e-227">移行からの SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-227">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="9f24e-228">引数:</span><span class="sxs-lookup"><span data-stu-id="9f24e-228">Arguments:</span></span>

|         |                                                               |
|:--------|:--------------------------------------------------------------|
| <span data-ttu-id="9f24e-229">\<FROM></span><span class="sxs-lookup"><span data-stu-id="9f24e-229">\<FROM></span></span> | <span data-ttu-id="9f24e-230">開始の移行。</span><span class="sxs-lookup"><span data-stu-id="9f24e-230">The starting migration.</span></span> <span data-ttu-id="9f24e-231">既定値は 0 (最初のデータベース)。</span><span class="sxs-lookup"><span data-stu-id="9f24e-231">Defaults to 0 (the initial database).</span></span> |
| <span data-ttu-id="9f24e-232">\<TO></span><span class="sxs-lookup"><span data-stu-id="9f24e-232">\<TO></span></span>   | <span data-ttu-id="9f24e-233">終了の移行。</span><span class="sxs-lookup"><span data-stu-id="9f24e-233">The ending migration.</span></span> <span data-ttu-id="9f24e-234">既定値は最後に移行します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-234">Defaults to the last migration.</span></span>         |

<span data-ttu-id="9f24e-235">オプション:</span><span class="sxs-lookup"><span data-stu-id="9f24e-235">Options:</span></span>

|    |                  |                                                                    |
|:---|:-----------------|:-------------------------------------------------------------------|
| <span data-ttu-id="9f24e-236">-o</span><span class="sxs-lookup"><span data-stu-id="9f24e-236">-o</span></span> | <span data-ttu-id="9f24e-237">--出力\<ファイル ></span><span class="sxs-lookup"><span data-stu-id="9f24e-237">--output \<FILE></span></span> | <span data-ttu-id="9f24e-238">結果を書き込むファイルです。</span><span class="sxs-lookup"><span data-stu-id="9f24e-238">The file to write the result to.</span></span>                                   |
| <span data-ttu-id="9f24e-239">-i</span><span class="sxs-lookup"><span data-stu-id="9f24e-239">-i</span></span> | <span data-ttu-id="9f24e-240">--idempotent</span><span class="sxs-lookup"><span data-stu-id="9f24e-240">--idempotent</span></span>     | <span data-ttu-id="9f24e-241">すべての移行でのデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="9f24e-241">Generate a script that can be used on a database at any migration.</span></span> |


  [1]: powershell.md
  [2]: https://www.microsoft.com/net/core
