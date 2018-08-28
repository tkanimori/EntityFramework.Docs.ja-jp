---
title: パッケージ マネージャー コンソール (Visual Studio) - EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/6/2017
ms.openlocfilehash: 6a3594a3535f8de30ec1898fd21cfcbe272f216b
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997935"
---
<a name="ef-core-package-manager-console-tools"></a><span data-ttu-id="8bd9e-102">EF Core パッケージ マネージャー コンソール ツール</span><span class="sxs-lookup"><span data-stu-id="8bd9e-102">EF Core Package Manager Console Tools</span></span>
=====================================
<span data-ttu-id="8bd9e-103">NuGet を使用して Visual Studio 内での EF Core パッケージ マネージャー コンソール (PMC) ツールの実行[パッケージ マネージャー コンソール][2]します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-103">The EF Core Package Manager Console (PMC) Tools run inside of Visual Studio using NuGet's [Package Manager Console][2].</span></span>
<span data-ttu-id="8bd9e-104">これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-104">These tools work with both .NET Framework and .NET Core projects.</span></span>

> [!TIP]
> <span data-ttu-id="8bd9e-105">Visual Studio を使用していないか。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-105">Not using Visual Studio?</span></span> <span data-ttu-id="8bd9e-106">[EF Core コマンド ライン ツール][ 1]クロスプラット フォームであり、コマンド プロンプト内で実行します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-106">The [EF Core Command-line Tools][1] are cross-platform and run inside a command prompt.</span></span>

<a name="installing-the-tools"></a><span data-ttu-id="8bd9e-107">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="8bd9e-107">Installing the tools</span></span>
--------------------
<span data-ttu-id="8bd9e-108">Microsoft.EntityFrameworkCore.Tools NuGet パッケージをインストールすることで、EF Core パッケージ マネージャー コンソールのツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-108">Install the EF Core Package Manager Console Tools by installing the Microsoft.EntityFrameworkCore.Tools NuGet package.</span></span>
<span data-ttu-id="8bd9e-109">内で次のコマンドを実行してインストールすることができます[パッケージ マネージャー コンソール][2]します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-109">You can install it by executing the following command inside [Package Manager Console][2].</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="8bd9e-110">すべて正常である場合は、このコマンドを実行できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-110">If everything worked correctly, you should be able to run this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```
> [!TIP]
> <span data-ttu-id="8bd9e-111">スタートアップ プロジェクトが .NET Standard を対象とする場合[クロス ターゲット フレームワークがサポートされている][ 3]ツールを使用する前にします。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-111">If your startup project targets .NET Standard, [cross-target a supported framework][3] before using the tools.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8bd9e-112">使用している場合**ユニバーサル Windows**または**Xamarin**、EF コードを .NET Standard クラス ライブラリに移動し、[クロス ターゲット フレームワークがサポートされている][ 3]ツールを使用する前にします。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-112">If you're using **Universal Windows** or **Xamarin**, move your EF code to a .NET Standard class library and [cross-target a supported framework][3] before using the tools.</span></span> <span data-ttu-id="8bd9e-113">スタートアップ プロジェクトとして、クラス ライブラリを指定します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-113">Specify the class library as your startup project.</span></span>

<a name="using-the-tools"></a><span data-ttu-id="8bd9e-114">ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-114">Using the tools</span></span>
---------------
<span data-ttu-id="8bd9e-115">コマンドを呼び出すたびにある 2 つのプロジェクト関係します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-115">Whenever you invoke a command, there are two projects involved:</span></span>

<span data-ttu-id="8bd9e-116">ターゲット プロジェクトでは、ファイルが追加されます (削除される場合もあります)。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-116">The target project is where any files are added (or in some cases removed).</span></span> <span data-ttu-id="8bd9e-117">ターゲット プロジェクトの既定値、**既定のプロジェクト**パッケージ マネージャー コンソールで、選択されているを使用して指定できますが、プロジェクト パラメーターです。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-117">The target project defaults to the **Default project** selected in Package Manager Console, but can also be specified using the -Project parameter.</span></span>

<span data-ttu-id="8bd9e-118">スタートアップ プロジェクトは、プロジェクトのコードを実行したとき、ツールによってエミュレートされるプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-118">The startup project is the one emulated by the tools when executing your project's code.</span></span> <span data-ttu-id="8bd9e-119">いずれかに既定で**スタートアップ プロジェクトとして設定**ソリューション エクスプ ローラーでします。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-119">It defaults to one **Set as StartUp Project** in Solution Explorer.</span></span> <span data-ttu-id="8bd9e-120">指定できます - スタートアップ プロジェクトのパラメーターを使用します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-120">It can also be specified using the -StartupProject parameter.</span></span>

<span data-ttu-id="8bd9e-121">一般的なパラメーター:</span><span class="sxs-lookup"><span data-stu-id="8bd9e-121">Common parameters:</span></span>

|                           |                             |
|:--------------------------|:----------------------------|
| <span data-ttu-id="8bd9e-122">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="8bd9e-122">-Context \<String></span></span>        | <span data-ttu-id="8bd9e-123">使用する DbContext します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-123">The DbContext to use.</span></span>       |
| <span data-ttu-id="8bd9e-124">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="8bd9e-124">-Project \<String></span></span>        | <span data-ttu-id="8bd9e-125">使用するプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-125">The project to use.</span></span>         |
| <span data-ttu-id="8bd9e-126">-StartupProject \<String></span><span class="sxs-lookup"><span data-stu-id="8bd9e-126">-StartupProject \<String></span></span> | <span data-ttu-id="8bd9e-127">使用するスタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-127">The startup project to use.</span></span> |
| <span data-ttu-id="8bd9e-128">-Verbose</span><span class="sxs-lookup"><span data-stu-id="8bd9e-128">-Verbose</span></span>                  | <span data-ttu-id="8bd9e-129">詳細な出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-129">Show verbose output.</span></span>        |

<span data-ttu-id="8bd9e-130">コマンドに関するヘルプ情報を表示するには、PowerShell を使用して`Get-Help`コマンド。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-130">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="8bd9e-131">コンテキスト、プロジェクト、およびスタートアップ プロジェクトのパラメーターでは、タブ拡張をサポートします。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-131">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

> [!TIP]
> <span data-ttu-id="8bd9e-132">設定**env:ASPNETCORE_ENVIRONMENT** ASP.NET Core 環境を指定して実行する前にします。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-132">Set **env:ASPNETCORE_ENVIRONMENT** before running to specify the ASP.NET Core environment.</span></span>

<a name="commands"></a><span data-ttu-id="8bd9e-133">コマンド</span><span class="sxs-lookup"><span data-stu-id="8bd9e-133">Commands</span></span>
--------

### <a name="add-migration"></a><span data-ttu-id="8bd9e-134">追加の移行</span><span class="sxs-lookup"><span data-stu-id="8bd9e-134">Add-Migration</span></span>

<span data-ttu-id="8bd9e-135">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-135">Adds a new migration.</span></span>

<span data-ttu-id="8bd9e-136">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="8bd9e-136">Parameters:</span></span>

|                                   |                                                                                                                  |
|:----------------------------------|:-----------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8bd9e-137">***-Name*** \<文字列 ></span><span class="sxs-lookup"><span data-stu-id="8bd9e-137">***-Name*** \<String></span></span>             | <span data-ttu-id="8bd9e-138">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-138">The name of the migration.</span></span>                                                                                       |
| <span data-ttu-id="8bd9e-139"><nobr>-OutputDir\<文字列 ></nobr></span><span class="sxs-lookup"><span data-stu-id="8bd9e-139"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="8bd9e-140">使用するディレクトリ (およびサブ名前空間)。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-140">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="8bd9e-141">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-141">Paths are relative to the project directory.</span></span> <span data-ttu-id="8bd9e-142">既定値は「移行」です。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-142">Defaults to "Migrations".</span></span> |

> [!NOTE]
> <span data-ttu-id="8bd9e-143">パラメーター**太字**が必要なもので、*斜体*は位置指定します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-143">Parameters in **bold** are required, and ones in *italics* are positional.</span></span>

### <a name="drop-database"></a><span data-ttu-id="8bd9e-144">データベースの削除</span><span class="sxs-lookup"><span data-stu-id="8bd9e-144">Drop-Database</span></span>

<span data-ttu-id="8bd9e-145">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-145">Drops the database.</span></span>

<span data-ttu-id="8bd9e-146">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="8bd9e-146">Parameters:</span></span>

|         |                                                          |
|:--------|:---------------------------------------------------------|
| <span data-ttu-id="8bd9e-147">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="8bd9e-147">-WhatIf</span></span> | <span data-ttu-id="8bd9e-148">表示するデータベースが削除されます。 は削除しないでください。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-148">Show which database would be dropped, but don't drop it.</span></span> |

### <a name="get-dbcontext"></a><span data-ttu-id="8bd9e-149">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="8bd9e-149">Get-DbContext</span></span>

<span data-ttu-id="8bd9e-150">DbContext 型に関する情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-150">Gets information about a DbContext type.</span></span>

### <a name="remove-migration"></a><span data-ttu-id="8bd9e-151">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="8bd9e-151">Remove-Migration</span></span>

<span data-ttu-id="8bd9e-152">最後の移行を削除します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-152">Removes the last migration.</span></span>

<span data-ttu-id="8bd9e-153">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="8bd9e-153">Parameters:</span></span>

|        |                                                              |
|:-------|:-------------------------------------------------------------|
| <span data-ttu-id="8bd9e-154">-Force</span><span class="sxs-lookup"><span data-stu-id="8bd9e-154">-Force</span></span> | <span data-ttu-id="8bd9e-155">データベースに適用されている場合は、移行を戻します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-155">Revert the migration if it has been applied to the database.</span></span> |

### <a name="scaffold-dbcontext"></a><span data-ttu-id="8bd9e-156">Scaffold-dbcontext</span><span class="sxs-lookup"><span data-stu-id="8bd9e-156">Scaffold-DbContext</span></span>

<span data-ttu-id="8bd9e-157">データベースの DbContext とエンティティ型をスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-157">Scaffolds a DbContext and entity types for a database.</span></span>

<span data-ttu-id="8bd9e-158">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="8bd9e-158">Parameters:</span></span>

|                                          |                                                                                                  |
|:-----------------------------------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8bd9e-159"><nobr>***の接続***\<文字列 ></nobr></span><span class="sxs-lookup"><span data-stu-id="8bd9e-159"><nobr>***-Connection*** \<String></nobr></span></span> | <span data-ttu-id="8bd9e-160">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-160">The connection string to the database.</span></span>                                                           |
| <span data-ttu-id="8bd9e-161">***プロバイダー*** \<文字列 ></span><span class="sxs-lookup"><span data-stu-id="8bd9e-161">***-Provider*** \<String></span></span>                | <span data-ttu-id="8bd9e-162">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-162">The provider to use.</span></span> <span data-ttu-id="8bd9e-163">(たとえば、Microsoft.EntityFrameworkCore.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="8bd9e-163">(for example, Microsoft.EntityFrameworkCore.SqlServer)</span></span>                      |
| <span data-ttu-id="8bd9e-164">-OutputDir\<文字列 ></span><span class="sxs-lookup"><span data-stu-id="8bd9e-164">-OutputDir \<String></span></span>                     | <span data-ttu-id="8bd9e-165">ファイルを配置するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-165">The directory to put files in.</span></span> <span data-ttu-id="8bd9e-166">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-166">Paths are relative to the project directory.</span></span>                      |
| <span data-ttu-id="8bd9e-167">-ContextDir\<文字列 ></span><span class="sxs-lookup"><span data-stu-id="8bd9e-167">-ContextDir \<String></span></span>                    | <span data-ttu-id="8bd9e-168">DbContext のファイルを配置するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-168">The directory to put DbContext file in.</span></span> <span data-ttu-id="8bd9e-169">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-169">Paths are relative to the project directory.</span></span>             |
| <span data-ttu-id="8bd9e-170">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="8bd9e-170">-Context \<String></span></span>                       | <span data-ttu-id="8bd9e-171">生成する DbContext の名前。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-171">The name of the DbContext to generate.</span></span>                                                           |
| <span data-ttu-id="8bd9e-172">-Schemas \<String[]></span><span class="sxs-lookup"><span data-stu-id="8bd9e-172">-Schemas \<String[]></span></span>                     | <span data-ttu-id="8bd9e-173">エンティティ型を生成するテーブルのスキーマです。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-173">The schemas of tables to generate entity types for.</span></span>                                              |
| <span data-ttu-id="8bd9e-174">-Tables \<String[]></span><span class="sxs-lookup"><span data-stu-id="8bd9e-174">-Tables \<String[]></span></span>                      | <span data-ttu-id="8bd9e-175">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-175">The tables to generate entity types for.</span></span>                                                         |
| <span data-ttu-id="8bd9e-176">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="8bd9e-176">-DataAnnotations</span></span>                         | <span data-ttu-id="8bd9e-177">属性を使用すると、可能な限り、モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-177">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="8bd9e-178">省略した場合、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-178">If omitted, only the fluent API is used.</span></span> |
| <span data-ttu-id="8bd9e-179">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="8bd9e-179">-UseDatabaseNames</span></span>                        | <span data-ttu-id="8bd9e-180">データベースから直接テーブルおよび列名を使用します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-180">Use table and column names directly from the database.</span></span>                                           |
| <span data-ttu-id="8bd9e-181">-Force</span><span class="sxs-lookup"><span data-stu-id="8bd9e-181">-Force</span></span>                                   | <span data-ttu-id="8bd9e-182">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-182">Overwrite existing files.</span></span>                                                                        |

### <a name="script-migration"></a><span data-ttu-id="8bd9e-183">スクリプトの移行</span><span class="sxs-lookup"><span data-stu-id="8bd9e-183">Script-Migration</span></span>

<span data-ttu-id="8bd9e-184">移行には、SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-184">Generates a SQL script from migrations.</span></span>

<span data-ttu-id="8bd9e-185">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="8bd9e-185">Parameters:</span></span>

|                   |                                                                    |
|:------------------|:-------------------------------------------------------------------|
| <span data-ttu-id="8bd9e-186">*-から*\<文字列 ></span><span class="sxs-lookup"><span data-stu-id="8bd9e-186">*-From* \<String></span></span> | <span data-ttu-id="8bd9e-187">開始の移行。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-187">The starting migration.</span></span> <span data-ttu-id="8bd9e-188">既定値は 0 (最初のデータベース) です。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-188">Defaults to 0 (the initial database).</span></span>      |
| <span data-ttu-id="8bd9e-189">*-* \<文字列 ></span><span class="sxs-lookup"><span data-stu-id="8bd9e-189">*-To* \<String></span></span>   | <span data-ttu-id="8bd9e-190">終了の移行。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-190">The ending migration.</span></span> <span data-ttu-id="8bd9e-191">移行の最後の既定値します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-191">Defaults to the last migration.</span></span>              |
| <span data-ttu-id="8bd9e-192">-Idempotent</span><span class="sxs-lookup"><span data-stu-id="8bd9e-192">-Idempotent</span></span>       | <span data-ttu-id="8bd9e-193">すべての移行でのデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-193">Generate a script that can be used on a database at any migration.</span></span> |
| <span data-ttu-id="8bd9e-194">-出力\<文字列 ></span><span class="sxs-lookup"><span data-stu-id="8bd9e-194">-Output \<String></span></span> | <span data-ttu-id="8bd9e-195">結果を書き込むファイル。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-195">The file to write the result to.</span></span>                                   |

> [!TIP]
> <span data-ttu-id="8bd9e-196">および出力パラメーターは、タブ拡張をサポートします。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-196">The To, From, and Output parameters support tab-expansion.</span></span>

### <a name="update-database"></a><span data-ttu-id="8bd9e-197">データベースの更新</span><span class="sxs-lookup"><span data-stu-id="8bd9e-197">Update-Database</span></span>

|                                     |                                                                                                |
|:------------------------------------|:-----------------------------------------------------------------------------------------------|
| <span data-ttu-id="8bd9e-198"><nobr>*-移行*\<文字列 ></nobr></span><span class="sxs-lookup"><span data-stu-id="8bd9e-198"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="8bd9e-199">Target の移行。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-199">The target migration.</span></span> <span data-ttu-id="8bd9e-200">'0' の場合、すべての移行は戻されます。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-200">If '0', all migrations will be reverted.</span></span> <span data-ttu-id="8bd9e-201">移行の最後の既定値します。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-201">Defaults to the last migration.</span></span> |

> [!TIP]
> <span data-ttu-id="8bd9e-202">移行のパラメーターは、タブ拡張をサポートします。</span><span class="sxs-lookup"><span data-stu-id="8bd9e-202">The Migration parameter supports tab-expansion.</span></span>


  [1]: dotnet.md
  [2]: https://docs.microsoft.com/nuget/tools/package-manager-console
  [3]: index.md#frameworks
