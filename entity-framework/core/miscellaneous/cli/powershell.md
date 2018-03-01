---
title: "Package Manager Console (Visual Studio) - EF コア"
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.technology: entity-framework-core
ms.openlocfilehash: aacf8c8564a3966db6202c9ff1c1c02a19a10814
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
<a name="ef-core-package-manager-console-tools"></a><span data-ttu-id="ae215-102">EF コア パッケージ マネージャー コンソール ツール</span><span class="sxs-lookup"><span data-stu-id="ae215-102">EF Core Package Manager Console Tools</span></span>
=====================================
<span data-ttu-id="ae215-103">NuGet を使用して Visual Studio 内での EF コア パッケージ マネージャー コンソール (PMC) ツールの実行[Package Manager Console][2]です。</span><span class="sxs-lookup"><span data-stu-id="ae215-103">The EF Core Package Manager Console (PMC) Tools run inside of Visual Studio using NuGet's [Package Manager Console][2].</span></span>
<span data-ttu-id="ae215-104">これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。</span><span class="sxs-lookup"><span data-stu-id="ae215-104">These tools work with both .NET Framework and .NET Core projects.</span></span>

> [!TIP]
> <span data-ttu-id="ae215-105">Visual Studio を使用していないか。</span><span class="sxs-lookup"><span data-stu-id="ae215-105">Not using Visual Studio?</span></span> <span data-ttu-id="ae215-106">[コア コマンド ライン ツールの EF] [ 1]クロスプラット フォームにあり、コマンド プロンプト内で実行します。</span><span class="sxs-lookup"><span data-stu-id="ae215-106">The [EF Core Command-line Tools][1] are cross-platform and run inside a command prompt.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="ae215-107">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="ae215-107">Installing the tools</span></span>
--------------------
<span data-ttu-id="ae215-108">Microsoft.EntityFrameworkCore.Tools NuGet パッケージをインストールすることによって、EF コア パッケージ マネージャー コンソールのツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="ae215-108">Install the EF Core Package Manager Console Tools by installing the Microsoft.EntityFrameworkCore.Tools NuGet package.</span></span>
<span data-ttu-id="ae215-109">内の次のコマンドを実行して、インストールすることができます[Package Manager Console][2]です。</span><span class="sxs-lookup"><span data-stu-id="ae215-109">You can install it by executing the following command inside [Package Manager Console][2].</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="ae215-110">すべて正常である場合は、このコマンドを実行することができます。</span><span class="sxs-lookup"><span data-stu-id="ae215-110">If everything worked correctly, you should be able to run this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```
> [!TIP]
> <span data-ttu-id="ae215-111">スタートアップ プロジェクトの対象 .NET 標準場合[サポートされているフレームワークの間のターゲット][ 3]ツールを使用する前にします。</span><span class="sxs-lookup"><span data-stu-id="ae215-111">If your startup project targets .NET Standard, [cross-target a supported framework][3] before using the tools.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ae215-112">使用する場合**ユニバーサル Windows**または**Xamarin**、EF コードを標準 .NET クラス ライブラリに移動し、[サポートされているフレームワークの間のターゲット][ 3]ツールを使用する前にします。</span><span class="sxs-lookup"><span data-stu-id="ae215-112">If you're using **Universal Windows** or **Xamarin**, move your EF code to a .NET Standard class library and [cross-target a supported framework][3] before using the tools.</span></span> <span data-ttu-id="ae215-113">スタートアップ プロジェクトとして、クラス ライブラリを指定します。</span><span class="sxs-lookup"><span data-stu-id="ae215-113">Specify the class library as your startup project.</span></span>

<a name="using-the-tools"></a><span data-ttu-id="ae215-114">ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="ae215-114">Using the tools</span></span>
---------------
<span data-ttu-id="ae215-115">コマンドを呼び出すときに 2 つのプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="ae215-115">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="ae215-116">ターゲット プロジェクトでは、ファイルが追加されます (削除される場合もあります)。</span><span class="sxs-lookup"><span data-stu-id="ae215-116">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="ae215-117">ターゲット プロジェクトの既定値、**既定のプロジェクト**パッケージ マネージャー コンソールで、選択されているを使用して指定することもプロジェクト パラメーターです。</span><span class="sxs-lookup"><span data-stu-id="ae215-117">The target project defaults to the **Default project** selected in Package Manager Console, but can also be specified using the -Project parameter.</span></span>

<span data-ttu-id="ae215-118">スタートアップ プロジェクトは、プロジェクトのコードを実行したとき、ツールによってエミュレートされるプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="ae215-118">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="ae215-119">既定値は 1 つ**スタートアップ プロジェクトとして設定**ソリューション エクスプ ローラーでします。</span><span class="sxs-lookup"><span data-stu-id="ae215-119">It defaults to one **Set as StartUp Project** in Solution Explorer.</span></span> <span data-ttu-id="ae215-120">指定できます - スタートアップ プロジェクトに設定パラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="ae215-120">It can also be specified using the -StartupProject parameter.</span></span>

<span data-ttu-id="ae215-121">一般的なパラメーター:</span><span class="sxs-lookup"><span data-stu-id="ae215-121">Common parameters:</span></span>

|                           |                             |
|:--------------------------|:----------------------------|
| <span data-ttu-id="ae215-122">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="ae215-122">-Context \<String></span></span>        | <span data-ttu-id="ae215-123">使用する DbContext です。</span><span class="sxs-lookup"><span data-stu-id="ae215-123">The DbContext to use.</span></span>       |
| <span data-ttu-id="ae215-124">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="ae215-124">-Project \<String></span></span>        | <span data-ttu-id="ae215-125">使用するプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="ae215-125">The project to use.</span></span>         |
| <span data-ttu-id="ae215-126">-StartupProject \<String></span><span class="sxs-lookup"><span data-stu-id="ae215-126">-StartupProject \<String></span></span> | <span data-ttu-id="ae215-127">使用するスタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="ae215-127">The startup project to use.</span></span> |
| <span data-ttu-id="ae215-128">-Verbose</span><span class="sxs-lookup"><span data-stu-id="ae215-128">-Verbose</span></span>                  | <span data-ttu-id="ae215-129">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="ae215-129">Show verbose output.</span></span>        |

<span data-ttu-id="ae215-130">コマンドに関するヘルプ情報を表示するには、PowerShell を使用して`Get-Help`コマンド。</span><span class="sxs-lookup"><span data-stu-id="ae215-130">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="ae215-131">コンテキスト、プロジェクト、およびスタートアップ プロジェクトに設定のパラメーターは、タブ拡張をサポートします。</span><span class="sxs-lookup"><span data-stu-id="ae215-131">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

> [!TIP]
> <span data-ttu-id="ae215-132">設定**env:ASPNETCORE_ENVIRONMENT** ASP.NET Core 環境の指定を実行する前にします。</span><span class="sxs-lookup"><span data-stu-id="ae215-132">Set **env:ASPNETCORE_ENVIRONMENT** before running to specify the ASP.NET Core environment.</span></span>

<a name="commands"></a><span data-ttu-id="ae215-133">コマンド</span><span class="sxs-lookup"><span data-stu-id="ae215-133">Commands</span></span>
--------

### <a name="add-migration"></a><span data-ttu-id="ae215-134">追加の移行</span><span class="sxs-lookup"><span data-stu-id="ae215-134">Add-Migration</span></span>

<span data-ttu-id="ae215-135">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="ae215-135">Adds a new migration.</span></span>

<span data-ttu-id="ae215-136">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="ae215-136">Parameters:</span></span>

|                                   |                                                                                                                  |
|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ae215-137">***-Name*** \<String></span><span class="sxs-lookup"><span data-stu-id="ae215-137">***-Name*** \<String></span></span>             | <span data-ttu-id="ae215-138">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="ae215-138">The name of the migration.</span></span>                                                                                       |
| <span data-ttu-id="ae215-139"><nobr>-OutputDir \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="ae215-139"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="ae215-140">ディレクトリ (およびサブ名前空間) を使用します。</span><span class="sxs-lookup"><span data-stu-id="ae215-140">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="ae215-141">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="ae215-141">Paths are relative to the project directory.</span></span> <span data-ttu-id="ae215-142">既定値は「移行」です。</span><span class="sxs-lookup"><span data-stu-id="ae215-142">Defaults to "Migrations".</span></span> |

> [!NOTE]
> <span data-ttu-id="ae215-143">内のパラメーター**太字**が必要ですとで*斜体*位置指定されます。</span><span class="sxs-lookup"><span data-stu-id="ae215-143">Parameters in **bold** are required, and ones in *italics* are positional.</span></span>

### <a name="drop-database"></a><span data-ttu-id="ae215-144">データベースを削除</span><span class="sxs-lookup"><span data-stu-id="ae215-144">Drop-Database</span></span>

<span data-ttu-id="ae215-145">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="ae215-145">Drops the database.</span></span>

<span data-ttu-id="ae215-146">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="ae215-146">Parameters:</span></span>

|         |                                                          |
|:--------|:---------------------------------------------------------|
| <span data-ttu-id="ae215-147">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="ae215-147">-WhatIf</span></span> | <span data-ttu-id="ae215-148">データベースは削除しますが、それを削除しないを表示します。</span><span class="sxs-lookup"><span data-stu-id="ae215-148">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="get-dbcontext"></a><span data-ttu-id="ae215-149">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="ae215-149">Get-DbContext</span></span>

<span data-ttu-id="ae215-150">DbContext 型に関する情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="ae215-150">Gets information about a DbContext type.</span></span>

### <a name="remove-migration"></a><span data-ttu-id="ae215-151">削除の移行</span><span class="sxs-lookup"><span data-stu-id="ae215-151">Remove-Migration</span></span>

<span data-ttu-id="ae215-152">前回の移行を削除します。</span><span class="sxs-lookup"><span data-stu-id="ae215-152">Removes the last migration.</span></span>

<span data-ttu-id="ae215-153">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="ae215-153">Parameters:</span></span>

|        |                                                                       |
|:-------|:----------------------------------------------------------------------|
| <span data-ttu-id="ae215-154">-Force</span><span class="sxs-lookup"><span data-stu-id="ae215-154">-Force</span></span> | <span data-ttu-id="ae215-155">移行をデータベースに適用されているかどうかをチェックしないでください。</span><span class="sxs-lookup"><span data-stu-id="ae215-155">Don't check to see if the migration has been applied to the database.</span></span> |

### <a name="scaffold-dbcontext"></a><span data-ttu-id="ae215-156">Scaffold-DbContext</span><span class="sxs-lookup"><span data-stu-id="ae215-156">Scaffold-DbContext</span></span>

<span data-ttu-id="ae215-157">スキャフォールディング データベースの DbContext とエンティティの型。</span><span class="sxs-lookup"><span data-stu-id="ae215-157">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="ae215-158">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="ae215-158">Parameters:</span></span>

|                                          |                                                                                                  |
|:-----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ae215-159"><nobr>***-Connection*** \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="ae215-159"><nobr>***-Connection*** \<String></nobr></span></span> | <span data-ttu-id="ae215-160">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="ae215-160">The connection string to the database.</span></span>                                                           |
| <span data-ttu-id="ae215-161">***-Provider*** \<String></span><span class="sxs-lookup"><span data-stu-id="ae215-161">***-Provider*** \<String></span></span>                | <span data-ttu-id="ae215-162">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="ae215-162">The provider to use.</span></span> <span data-ttu-id="ae215-163">(例。</span><span class="sxs-lookup"><span data-stu-id="ae215-163">(E.g.</span></span> <span data-ttu-id="ae215-164">Microsoft.EntityFrameworkCore.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="ae215-164">Microsoft.EntityFrameworkCore.SqlServer)</span></span>                              |
| <span data-ttu-id="ae215-165">-OutputDir \<String></span><span class="sxs-lookup"><span data-stu-id="ae215-165">-OutputDir \<String></span></span>                     | <span data-ttu-id="ae215-166">ファイルを置くディレクトリです。</span><span class="sxs-lookup"><span data-stu-id="ae215-166">The directory to put files in.</span></span> <span data-ttu-id="ae215-167">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="ae215-167">Paths are relative to the project directory.</span></span>                      |
| <span data-ttu-id="ae215-168">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="ae215-168">-Context \<String></span></span>                       | <span data-ttu-id="ae215-169">生成する DbContext の名前。</span><span class="sxs-lookup"><span data-stu-id="ae215-169">The name of the DbContext to generate.</span></span>                                                           |
| <span data-ttu-id="ae215-170">-Schemas \<String[]></span><span class="sxs-lookup"><span data-stu-id="ae215-170">-Schemas \<String[]></span></span>                     | <span data-ttu-id="ae215-171">エンティティ型を生成するテーブルのスキーマです。</span><span class="sxs-lookup"><span data-stu-id="ae215-171">The schemas of tables to generate entity types for.</span></span>                                              |
| <span data-ttu-id="ae215-172">-Tables \<String[]></span><span class="sxs-lookup"><span data-stu-id="ae215-172">-Tables \<String[]></span></span>                      | <span data-ttu-id="ae215-173">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="ae215-173">The tables to generate entity types for.</span></span>                                                         |
| <span data-ttu-id="ae215-174">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="ae215-174">-DataAnnotations</span></span>                         | <span data-ttu-id="ae215-175">属性を使用すると、(可能な場合)、モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="ae215-175">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="ae215-176">省略すると、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="ae215-176">If omitted, only the fluent API is used.</span></span> |
| <span data-ttu-id="ae215-177">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="ae215-177">-UseDatabaseNames</span></span>                        | <span data-ttu-id="ae215-178">データベースから直接テーブルおよび列名を使用します。</span><span class="sxs-lookup"><span data-stu-id="ae215-178">Use table and column names directly from the database.</span></span>                                           |
| <span data-ttu-id="ae215-179">-Force</span><span class="sxs-lookup"><span data-stu-id="ae215-179">-Force</span></span>                                   | <span data-ttu-id="ae215-180">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="ae215-180">Overwrite existing files.</span></span>                                                                        |

### <a name="script-migration"></a><span data-ttu-id="ae215-181">Script-Migration</span><span class="sxs-lookup"><span data-stu-id="ae215-181">Script-Migration</span></span>

<span data-ttu-id="ae215-182">移行からの SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="ae215-182">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="ae215-183">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="ae215-183">Parameters:</span></span>

|                   |                                                                    |
|:------------------|:-------------------------------------------------------------------|
| <span data-ttu-id="ae215-184">*-From* \<String></span><span class="sxs-lookup"><span data-stu-id="ae215-184">*-From* \<String></span></span> | <span data-ttu-id="ae215-185">開始の移行。</span><span class="sxs-lookup"><span data-stu-id="ae215-185">The starting migration.</span></span> <span data-ttu-id="ae215-186">既定値は 0 (最初のデータベース)。</span><span class="sxs-lookup"><span data-stu-id="ae215-186">Defaults to 0 (the initial database).</span></span>      |
| <span data-ttu-id="ae215-187">*-To* \<String></span><span class="sxs-lookup"><span data-stu-id="ae215-187">*-To* \<String></span></span>   | <span data-ttu-id="ae215-188">終了の移行。</span><span class="sxs-lookup"><span data-stu-id="ae215-188">The ending migration.</span></span> <span data-ttu-id="ae215-189">既定値は最後に移行します。</span><span class="sxs-lookup"><span data-stu-id="ae215-189">Defaults to the last migration.</span></span>              |
| <span data-ttu-id="ae215-190">-Idempotent</span><span class="sxs-lookup"><span data-stu-id="ae215-190">-Idempotent</span></span>       | <span data-ttu-id="ae215-191">すべての移行でのデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="ae215-191">Generate a script that can be used on a database at any migration.</span></span> |
| <span data-ttu-id="ae215-192">-出力\<文字列 ></span><span class="sxs-lookup"><span data-stu-id="ae215-192">-Output \<String></span></span> | <span data-ttu-id="ae215-193">結果を書き込むファイルです。</span><span class="sxs-lookup"><span data-stu-id="ae215-193">The file to write the result to.</span></span>                                   |

> [!TIP]
> <span data-ttu-id="ae215-194">出力パラメーターは、タブ拡張をサポートします。</span><span class="sxs-lookup"><span data-stu-id="ae215-194">The To, From, and Output parameters support tab-expansion.</span></span>

### <a name="update-database"></a><span data-ttu-id="ae215-195">データベースの更新</span><span class="sxs-lookup"><span data-stu-id="ae215-195">Update-Database</span></span>

|                                     |                                                                                                |
|:------------------------------------|:-----------------------------------------------------------------------------------------------|
| <span data-ttu-id="ae215-196"><nobr>*移行*\<文字列 ></nobr></span><span class="sxs-lookup"><span data-stu-id="ae215-196"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="ae215-197">Target の移行。</span><span class="sxs-lookup"><span data-stu-id="ae215-197">The target migration.</span></span> <span data-ttu-id="ae215-198">'0' の場合はすべての移行を元に戻さです。</span><span class="sxs-lookup"><span data-stu-id="ae215-198">If '0', all migrations will be reverted.</span></span> <span data-ttu-id="ae215-199">既定値は最後に移行します。</span><span class="sxs-lookup"><span data-stu-id="ae215-199">Defaults to the last migration.</span></span> |

> [!TIP]
> <span data-ttu-id="ae215-200">移行パラメーターは、タブ拡張をサポートします。</span><span class="sxs-lookup"><span data-stu-id="ae215-200">The Migration parameter supports tab-expansion.</span></span>


  [1]: dotnet.md
  [2]: https://docs.microsoft.com/nuget/tools/package-manager-console
  [3]: index.md#frameworks
