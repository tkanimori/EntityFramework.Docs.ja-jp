---
title: EF Core ツールリファレンス (パッケージマネージャーコンソール)-EF Core
description: Entity Framework Core Visual Studio パッケージマネージャーコンソールのリファレンスガイド
author: bricelam
ms.date: 10/27/2020
uid: core/cli/powershell
ms.openlocfilehash: 4a1ab889fc1117b67252ace51fd3df4797b6c8d3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431314"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="2df6d-103">Entity Framework Core ツールリファレンス-Visual Studio のパッケージマネージャーコンソール</span><span class="sxs-lookup"><span data-stu-id="2df6d-103">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="2df6d-104">Entity Framework Core 用のパッケージマネージャーコンソール (PMC) ツールは、デザイン時の開発タスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-104">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="2df6d-105">たとえば、既存のデータベースに基づいて、 [移行](/aspnet/core/data/ef-mvc/migrations)を作成し、移行を適用し、モデルのコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-105">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="2df6d-106">コマンドは、Visual Studio 内で [パッケージマネージャーコンソール](/nuget/tools/package-manager-console)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-106">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="2df6d-107">これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-107">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="2df6d-108">Visual Studio を使用していない場合は、代わりに [EF Core コマンドラインツール](xref:core/cli/dotnet) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="2df6d-108">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](xref:core/cli/dotnet) instead.</span></span> <span data-ttu-id="2df6d-109">.NET Core CLI ツールはクロスプラットフォームで、コマンドプロンプト内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-109">The .NET Core CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="2df6d-110">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="2df6d-110">Installing the tools</span></span>

<span data-ttu-id="2df6d-111">パッケージマネージャーコンソールで次のコマンドを実行して、パッケージマネージャーコンソールツールをインストールし **ます。**</span><span class="sxs-lookup"><span data-stu-id="2df6d-111">Install the Package Manager Console tools by running the following command in **Package Manager Console** :</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="2df6d-112">**パッケージマネージャーコンソール** で次のコマンドを実行して、ツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-112">Update the tools by running the following command in **Package Manager Console**.</span></span>

```powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="2df6d-113">インストール環境を確認する</span><span class="sxs-lookup"><span data-stu-id="2df6d-113">Verify the installation</span></span>

<span data-ttu-id="2df6d-114">次のコマンドを実行して、ツールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-114">Verify that the tools are installed by running this command:</span></span>

```powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="2df6d-115">出力は次のようになります (使用しているツールのバージョンはわかりません)。</span><span class="sxs-lookup"><span data-stu-id="2df6d-115">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

```output

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a><span data-ttu-id="2df6d-116">ツールの使用</span><span class="sxs-lookup"><span data-stu-id="2df6d-116">Using the tools</span></span>

<span data-ttu-id="2df6d-117">ツールを使用する前に:</span><span class="sxs-lookup"><span data-stu-id="2df6d-117">Before using the tools:</span></span>

* <span data-ttu-id="2df6d-118">ターゲットプロジェクトとスタートアッププロジェクトの違いを理解します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-118">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="2df6d-119">.NET Standard クラスライブラリでツールを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-119">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="2df6d-120">ASP.NET Core プロジェクトの場合は、環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-120">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="2df6d-121">ターゲットプロジェクトとスタートアッププロジェクト</span><span class="sxs-lookup"><span data-stu-id="2df6d-121">Target and startup project</span></span>

<span data-ttu-id="2df6d-122">これらのコマンドは、 *プロジェクト* と *スタートアッププロジェクト* を参照します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-122">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="2df6d-123">*プロジェクト* は *ターゲットプロジェクト* とも呼ばれます。これは、コマンドによってファイルが追加または削除されるためです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-123">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="2df6d-124">既定では、 **パッケージマネージャーコンソール** で選択された **既定のプロジェクト** はターゲットプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-124">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="2df6d-125">オプションを使用して、別のプロジェクトをターゲットプロジェクトとして指定でき <nobr>`--project`</nobr> ます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-125">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="2df6d-126">*スタートアッププロジェクト* は、ツールをビルドして実行するためのものです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-126">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="2df6d-127">このツールでは、デザイン時にアプリケーションコードを実行して、プロジェクトに関する情報 (データベース接続文字列やモデルの構成など) を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2df6d-127">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="2df6d-128">既定では、 **ソリューションエクスプローラー** の **スタートアッププロジェクト** はスタートアッププロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-128">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="2df6d-129">オプションを使用して、別のプロジェクトをスタートアッププロジェクトとして指定でき <nobr>`--startup-project`</nobr> ます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-129">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="2df6d-130">多くの場合、スタートアッププロジェクトとターゲットプロジェクトは同じプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-130">The startup project and target project are often the same project.</span></span> <span data-ttu-id="2df6d-131">個別のプロジェクトである一般的なシナリオは、次のような場合です。</span><span class="sxs-lookup"><span data-stu-id="2df6d-131">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="2df6d-132">EF Core コンテキストとエンティティクラスは、.NET Core クラスライブラリにあります。</span><span class="sxs-lookup"><span data-stu-id="2df6d-132">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="2df6d-133">.NET Core コンソールアプリまたは web アプリはクラスライブラリを参照します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-133">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="2df6d-134">また、 [移行コードを EF Core コンテキストとは別のクラスライブラリに配置](xref:core/managing-schemas/migrations/projects)することもできます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-134">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="2df6d-135">その他のターゲットフレームワーク</span><span class="sxs-lookup"><span data-stu-id="2df6d-135">Other target frameworks</span></span>

<span data-ttu-id="2df6d-136">パッケージマネージャーコンソールツールは、.NET Core または .NET Framework プロジェクトで使用できます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-136">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="2df6d-137">.NET Standard クラスライブラリに EF Core モデルがあるアプリには、.NET Core または .NET Framework プロジェクトがない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="2df6d-137">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="2df6d-138">たとえば、これは Xamarin とユニバーサル Windows プラットフォームアプリに当てはまります。</span><span class="sxs-lookup"><span data-stu-id="2df6d-138">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="2df6d-139">このような場合は、ツールのスタートアッププロジェクトとして機能することだけを目的とした .NET Core または .NET Framework コンソールアプリプロジェクトを作成できます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-139">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="2df6d-140">プロジェクトは、実際のコードを持たないダミープロジェクトにすることができ &mdash; ます。これは、ツールのターゲットを指定するためにのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="2df6d-140">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="2df6d-141">ダミープロジェクトが必要な理由</span><span class="sxs-lookup"><span data-stu-id="2df6d-141">Why is a dummy project required?</span></span> <span data-ttu-id="2df6d-142">前述のように、ツールはデザイン時にアプリケーションコードを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2df6d-142">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="2df6d-143">そのためには、.NET Core または .NET Framework ランタイムを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2df6d-143">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="2df6d-144">EF Core モデルが .NET Core または .NET Framework を対象とするプロジェクト内にある場合、EF Core ツールはプロジェクトからランタイムを借用します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-144">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="2df6d-145">EF Core モデルが .NET Standard クラスライブラリ内にある場合は、これを行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="2df6d-145">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="2df6d-146">.NET Standard は実際の .NET 実装ではありません。これは、.NET 実装がサポートする必要のある一連の Api の仕様です。</span><span class="sxs-lookup"><span data-stu-id="2df6d-146">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="2df6d-147">したがって、EF Core ツールでアプリケーションコードを実行するために .NET Standard は十分ではありません。</span><span class="sxs-lookup"><span data-stu-id="2df6d-147">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="2df6d-148">スタートアッププロジェクトとして使用するために作成するダミープロジェクトは、ツールが .NET Standard クラスライブラリを読み込むことができる具象ターゲットプラットフォームを提供します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-148">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="2df6d-149">ASP.NET Core 環境</span><span class="sxs-lookup"><span data-stu-id="2df6d-149">ASP.NET Core environment</span></span>

<span data-ttu-id="2df6d-150">ASP.NET Core プロジェクトの [環境](/aspnet/core/fundamentals/environments) を指定するには、コマンドを実行する前に **env: ASPNETCORE_ENVIRONMENT** を設定します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-150">To specify [the environment](/aspnet/core/fundamentals/environments) for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

<span data-ttu-id="2df6d-151">EF Core 5.0 以降では、追加の引数をプログラムに渡すこともできます。 CreateHostBuilder を使用すると、コマンドラインで環境を指定できます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-151">Starting in EF Core 5.0, additional arguments can also be passed into Program.CreateHostBuilder allowing you to specify the environment on the command-line:</span></span>

```powershell
Update-Database -Args '--environment Production'
```

## <a name="common-parameters"></a><span data-ttu-id="2df6d-152">共通パラメーター</span><span class="sxs-lookup"><span data-stu-id="2df6d-152">Common parameters</span></span>

<span data-ttu-id="2df6d-153">次の表に、すべての EF Core コマンドに共通のパラメーターを示します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-153">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="2df6d-154">パラメーター</span><span class="sxs-lookup"><span data-stu-id="2df6d-154">Parameter</span></span>                 | <span data-ttu-id="2df6d-155">説明</span><span class="sxs-lookup"><span data-stu-id="2df6d-155">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2df6d-156">-コンテキスト \<String></span><span class="sxs-lookup"><span data-stu-id="2df6d-156">-Context \<String></span></span>        | <span data-ttu-id="2df6d-157">使用する `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="2df6d-157">The `DbContext` class to use.</span></span> <span data-ttu-id="2df6d-158">クラス名のみ、または名前空間で完全修飾されています。</span><span class="sxs-lookup"><span data-stu-id="2df6d-158">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="2df6d-159">このパラメーターを省略した場合、EF Core によってコンテキストクラスが検索されます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-159">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="2df6d-160">複数のコンテキストクラスがある場合は、このパラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="2df6d-160">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="2df6d-161">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="2df6d-161">-Project \<String></span></span>        | <span data-ttu-id="2df6d-162">ターゲットプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="2df6d-162">The target project.</span></span> <span data-ttu-id="2df6d-163">このパラメーターを省略すると、 **パッケージマネージャーコンソール** の **既定のプロジェクト** がターゲットプロジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-163">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="2df6d-164"><nobr>-Startupproject</nobr>\<String></span><span class="sxs-lookup"><span data-stu-id="2df6d-164"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="2df6d-165">スタートアッププロジェクト。</span><span class="sxs-lookup"><span data-stu-id="2df6d-165">The startup project.</span></span> <span data-ttu-id="2df6d-166">このパラメーターを省略すると、 **ソリューションのプロパティ** の **スタートアッププロジェクト** がターゲットプロジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-166">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="2df6d-167">-Args \<String></span><span class="sxs-lookup"><span data-stu-id="2df6d-167">-Args \<String></span></span>           | <span data-ttu-id="2df6d-168">アプリケーションに渡される引数。</span><span class="sxs-lookup"><span data-stu-id="2df6d-168">Arguments passed to the application.</span></span> <span data-ttu-id="2df6d-169">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="2df6d-169">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="2df6d-170">-Verbose</span><span class="sxs-lookup"><span data-stu-id="2df6d-170">-Verbose</span></span>                  | <span data-ttu-id="2df6d-171">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-171">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="2df6d-172">コマンドに関するヘルプ情報を表示するには、PowerShell のコマンドを使用し `Get-Help` ます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-172">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="2df6d-173">Context、Project、および StartupProject の各パラメーターでは、タブの展開がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="2df6d-173">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="2df6d-174">Add-Migration</span><span class="sxs-lookup"><span data-stu-id="2df6d-174">Add-Migration</span></span>

<span data-ttu-id="2df6d-175">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-175">Adds a new migration.</span></span>

<span data-ttu-id="2df6d-176">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="2df6d-176">Parameters:</span></span>

| <span data-ttu-id="2df6d-177">パラメーター</span><span class="sxs-lookup"><span data-stu-id="2df6d-177">Parameter</span></span>                         | <span data-ttu-id="2df6d-178">説明</span><span class="sxs-lookup"><span data-stu-id="2df6d-178">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2df6d-179"><nobr>-Name \<String><nobr></span><span class="sxs-lookup"><span data-stu-id="2df6d-179"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="2df6d-180">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="2df6d-180">The name of the migration.</span></span> <span data-ttu-id="2df6d-181">これは位置指定パラメーターであり、必須です。</span><span class="sxs-lookup"><span data-stu-id="2df6d-181">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="2df6d-182"><nobr>-OutputDir \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="2df6d-182"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="2df6d-183">ファイルの出力に使用するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="2df6d-183">The directory use to output the files.</span></span> <span data-ttu-id="2df6d-184">パスは、ターゲットプロジェクトディレクトリに対する相対パスです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-184">Paths are relative to the target project directory.</span></span> <span data-ttu-id="2df6d-185">既定値は "移行" です。</span><span class="sxs-lookup"><span data-stu-id="2df6d-185">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="2df6d-186"><nobr>-名前空間 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="2df6d-186"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="2df6d-187">生成されたクラスに使用する名前空間。</span><span class="sxs-lookup"><span data-stu-id="2df6d-187">The namespace to use for the generated classes.</span></span> <span data-ttu-id="2df6d-188">既定値は、出力ディレクトリから生成されます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-188">Defaults to generated from the output directory.</span></span> <span data-ttu-id="2df6d-189">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="2df6d-189">Added in EF Core 5.0.</span></span>  |

<span data-ttu-id="2df6d-190">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-190">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="drop-database"></a><span data-ttu-id="2df6d-191">Drop-Database</span><span class="sxs-lookup"><span data-stu-id="2df6d-191">Drop-Database</span></span>

<span data-ttu-id="2df6d-192">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-192">Drops the database.</span></span>

<span data-ttu-id="2df6d-193">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="2df6d-193">Parameters:</span></span>

| <span data-ttu-id="2df6d-194">パラメーター</span><span class="sxs-lookup"><span data-stu-id="2df6d-194">Parameter</span></span> | <span data-ttu-id="2df6d-195">説明</span><span class="sxs-lookup"><span data-stu-id="2df6d-195">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="2df6d-196">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="2df6d-196">-WhatIf</span></span>   | <span data-ttu-id="2df6d-197">削除するデータベースを表示しますが、削除はしません。</span><span class="sxs-lookup"><span data-stu-id="2df6d-197">Show which database would be dropped, but don't drop it.</span></span> |

<span data-ttu-id="2df6d-198">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-198">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="get-dbcontext"></a><span data-ttu-id="2df6d-199">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="2df6d-199">Get-DbContext</span></span>

<span data-ttu-id="2df6d-200">使用可能な種類に関する情報を一覧表示して取得し `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-200">Lists and gets information about available `DbContext` types.</span></span>

<span data-ttu-id="2df6d-201">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-201">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="get-migration"></a><span data-ttu-id="2df6d-202">Get-Migration</span><span class="sxs-lookup"><span data-stu-id="2df6d-202">Get-Migration</span></span>

<span data-ttu-id="2df6d-203">使用可能な移行を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-203">Lists available migrations.</span></span> <span data-ttu-id="2df6d-204">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="2df6d-204">Added in EF Core 5.0.</span></span>

<span data-ttu-id="2df6d-205">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="2df6d-205">Parameters:</span></span>

| <span data-ttu-id="2df6d-206">パラメーター</span><span class="sxs-lookup"><span data-stu-id="2df6d-206">Parameter</span></span>                          | <span data-ttu-id="2df6d-207">説明</span><span class="sxs-lookup"><span data-stu-id="2df6d-207">Description</span></span>                                                                                            |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <span data-ttu-id="2df6d-208"><nobr>-接続 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="2df6d-208"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="2df6d-209">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="2df6d-209">The connection string to the database.</span></span> <span data-ttu-id="2df6d-210">既定値は、AddDbContext または OnConfiguring で指定されたものです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-210">Defaults to the one specified in AddDbContext or OnConfiguring.</span></span> |
| <span data-ttu-id="2df6d-211">-NoConnect</span><span class="sxs-lookup"><span data-stu-id="2df6d-211">-NoConnect</span></span>                         | <span data-ttu-id="2df6d-212">データベースに接続しないでください。</span><span class="sxs-lookup"><span data-stu-id="2df6d-212">Don't connect to the database.</span></span>                                                                         |

<span data-ttu-id="2df6d-213">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-213">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="2df6d-214">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="2df6d-214">Remove-Migration</span></span>

<span data-ttu-id="2df6d-215">最後の移行を削除します (移行のために実行されたコード変更をロールバックします)。</span><span class="sxs-lookup"><span data-stu-id="2df6d-215">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="2df6d-216">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="2df6d-216">Parameters:</span></span>

| <span data-ttu-id="2df6d-217">パラメーター</span><span class="sxs-lookup"><span data-stu-id="2df6d-217">Parameter</span></span> | <span data-ttu-id="2df6d-218">説明</span><span class="sxs-lookup"><span data-stu-id="2df6d-218">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="2df6d-219">-Force</span><span class="sxs-lookup"><span data-stu-id="2df6d-219">-Force</span></span>    | <span data-ttu-id="2df6d-220">移行を元に戻します (データベースに適用された変更をロールバックします)。</span><span class="sxs-lookup"><span data-stu-id="2df6d-220">Revert the migration (roll back the changes that were applied to the database).</span></span> |

<span data-ttu-id="2df6d-221">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-221">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="scaffold-dbcontext"></a><span data-ttu-id="2df6d-222">Scaffold-DbContext</span><span class="sxs-lookup"><span data-stu-id="2df6d-222">Scaffold-DbContext</span></span>

<span data-ttu-id="2df6d-223">`DbContext`データベースのおよびエンティティ型のコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-223">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="2df6d-224">で `Scaffold-DbContext` エンティティ型を生成するには、データベーステーブルに主キーが必要です。</span><span class="sxs-lookup"><span data-stu-id="2df6d-224">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="2df6d-225">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="2df6d-225">Parameters:</span></span>

| <span data-ttu-id="2df6d-226">パラメーター</span><span class="sxs-lookup"><span data-stu-id="2df6d-226">Parameter</span></span>                          | <span data-ttu-id="2df6d-227">説明</span><span class="sxs-lookup"><span data-stu-id="2df6d-227">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2df6d-228"><nobr>-接続 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="2df6d-228"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="2df6d-229">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="2df6d-229">The connection string to the database.</span></span> <span data-ttu-id="2df6d-230">ASP.NET Core 2.x プロジェクトの場合、値には *name = \<name of connection string>* を指定できます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-230">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="2df6d-231">その場合、プロジェクト用に設定されている構成ソースから名前を取得します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-231">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="2df6d-232">これは位置指定パラメーターであり、必須です。</span><span class="sxs-lookup"><span data-stu-id="2df6d-232">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="2df6d-233"><nobr>-プロバイダー \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="2df6d-233"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="2df6d-234">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="2df6d-234">The provider to use.</span></span> <span data-ttu-id="2df6d-235">通常、これは NuGet パッケージの名前です (例:) `Microsoft.EntityFrameworkCore.SqlServer` 。</span><span class="sxs-lookup"><span data-stu-id="2df6d-235">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="2df6d-236">これは位置指定パラメーターであり、必須です。</span><span class="sxs-lookup"><span data-stu-id="2df6d-236">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="2df6d-237">-OutputDir \<String></span><span class="sxs-lookup"><span data-stu-id="2df6d-237">-OutputDir \<String></span></span>               | <span data-ttu-id="2df6d-238">ファイルを格納するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="2df6d-238">The directory to put files in.</span></span> <span data-ttu-id="2df6d-239">パスは、プロジェクトディレクトリに対する相対パスです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-239">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="2df6d-240">-ContextDir \<String></span><span class="sxs-lookup"><span data-stu-id="2df6d-240">-ContextDir \<String></span></span>              | <span data-ttu-id="2df6d-241">ファイルを格納するディレクトリ `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="2df6d-241">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="2df6d-242">パスは、プロジェクトディレクトリに対する相対パスです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-242">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="2df6d-243">-名前空間 \<String></span><span class="sxs-lookup"><span data-stu-id="2df6d-243">-Namespace \<String></span></span>               | <span data-ttu-id="2df6d-244">生成されたすべてのクラスに使用する名前空間。</span><span class="sxs-lookup"><span data-stu-id="2df6d-244">The namespace to use for all generated classes.</span></span> <span data-ttu-id="2df6d-245">既定値は、ルート名前空間と出力ディレクトリから生成されます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-245">Defaults to generated from the root namespace and the output directory.</span></span> <span data-ttu-id="2df6d-246">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="2df6d-246">Added in EF Core 5.0.</span></span>                                                                                                                           |
| <span data-ttu-id="2df6d-247">-ContextNamespace \<String></span><span class="sxs-lookup"><span data-stu-id="2df6d-247">-ContextNamespace \<String></span></span>        | <span data-ttu-id="2df6d-248">生成されたクラスに使用する名前空間 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="2df6d-248">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="2df6d-249">注: はオーバーライドさ `-Namespace` れます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-249">Note: overrides `-Namespace`.</span></span> <span data-ttu-id="2df6d-250">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="2df6d-250">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="2df6d-251">-コンテキスト \<String></span><span class="sxs-lookup"><span data-stu-id="2df6d-251">-Context \<String></span></span>                 | <span data-ttu-id="2df6d-252">`DbContext`生成するクラスの名前。</span><span class="sxs-lookup"><span data-stu-id="2df6d-252">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="2df6d-253">-スキーマ \<String[]></span><span class="sxs-lookup"><span data-stu-id="2df6d-253">-Schemas \<String[]></span></span>               | <span data-ttu-id="2df6d-254">エンティティ型を生成するテーブルのスキーマ。</span><span class="sxs-lookup"><span data-stu-id="2df6d-254">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="2df6d-255">このパラメーターを省略すると、すべてのスキーマが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-255">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="2df6d-256">-テーブル \<String[]></span><span class="sxs-lookup"><span data-stu-id="2df6d-256">-Tables \<String[]></span></span>                | <span data-ttu-id="2df6d-257">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="2df6d-257">The tables to generate entity types for.</span></span> <span data-ttu-id="2df6d-258">このパラメーターを省略すると、すべてのテーブルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-258">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="2df6d-259">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="2df6d-259">-DataAnnotations</span></span>                   | <span data-ttu-id="2df6d-260">属性を使用してモデルを構成します (可能な場合)。</span><span class="sxs-lookup"><span data-stu-id="2df6d-260">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="2df6d-261">このパラメーターを省略した場合は、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-261">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="2df6d-262">-UseDatabaseNames 場合</span><span class="sxs-lookup"><span data-stu-id="2df6d-262">-UseDatabaseNames</span></span>                  | <span data-ttu-id="2df6d-263">テーブル名と列名は、データベースに表示されるとおりに使用します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-263">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="2df6d-264">このパラメーターを省略した場合、データベース名は、C# の名前のスタイル規則により厳密に準拠するように変更されます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-264">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="2df6d-265">-Force</span><span class="sxs-lookup"><span data-stu-id="2df6d-265">-Force</span></span>                             | <span data-ttu-id="2df6d-266">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="2df6d-266">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |
| <span data-ttu-id="2df6d-267">-NoOnConfiguring</span><span class="sxs-lookup"><span data-stu-id="2df6d-267">-NoOnConfiguring</span></span>                   | <span data-ttu-id="2df6d-268">生成しません `DbContext.OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="2df6d-268">Don't generate `DbContext.OnConfiguring`.</span></span> <span data-ttu-id="2df6d-269">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="2df6d-269">Added in EF Core 5.0.</span></span>                                                                                                                                                                                                         |
| <span data-ttu-id="2df6d-270">-NoPluralize</span><span class="sxs-lookup"><span data-stu-id="2df6d-270">-NoPluralize</span></span>                       | <span data-ttu-id="2df6d-271">プルーラライザーは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="2df6d-271">Don't use the pluralizer.</span></span> <span data-ttu-id="2df6d-272">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="2df6d-272">Added in EF Core 5.0.</span></span>                                                                                                                                                                                                                         |

<span data-ttu-id="2df6d-273">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-273">The [common parameters](#common-parameters) are listed above.</span></span>

<span data-ttu-id="2df6d-274">例:</span><span class="sxs-lookup"><span data-stu-id="2df6d-274">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="2df6d-275">選択されたテーブルのみをスキャフォールディングし、指定された名前と名前空間を持つ別のフォルダーにコンテキストを作成する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-275">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

<span data-ttu-id="2df6d-276">次の例では、 [共有マネージャーツール](/aspnet/core/security/app-secrets#secret-manager)を使用して、プロジェクトの構成から接続文字列を読み取ります。</span><span class="sxs-lookup"><span data-stu-id="2df6d-276">The following example reads the connection string from the project's configuration possibly set using the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager).</span></span>

```powershell
Scaffold-DbContext "Name=ConnectionStrings.Blogging" Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="script-dbcontext"></a><span data-ttu-id="2df6d-277">Script-DbContext</span><span class="sxs-lookup"><span data-stu-id="2df6d-277">Script-DbContext</span></span>

<span data-ttu-id="2df6d-278">DbContext から SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-278">Generates a SQL script from the DbContext.</span></span> <span data-ttu-id="2df6d-279">すべての移行をバイパスします。</span><span class="sxs-lookup"><span data-stu-id="2df6d-279">Bypasses any migrations.</span></span> <span data-ttu-id="2df6d-280">EF Core 3.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="2df6d-280">Added in EF Core 3.0.</span></span>

<span data-ttu-id="2df6d-281">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="2df6d-281">Parameters:</span></span>

| <span data-ttu-id="2df6d-282">パラメーター</span><span class="sxs-lookup"><span data-stu-id="2df6d-282">Parameter</span></span>                      | <span data-ttu-id="2df6d-283">説明</span><span class="sxs-lookup"><span data-stu-id="2df6d-283">Description</span></span>                      |
| ------------------------------ | -------------------------------- |
| <span data-ttu-id="2df6d-284"><nobr>-出力 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="2df6d-284"><nobr>-Output \<String></nobr></span></span> | <span data-ttu-id="2df6d-285">結果の書き込み先のファイル。</span><span class="sxs-lookup"><span data-stu-id="2df6d-285">The file to write the result to.</span></span> |

<span data-ttu-id="2df6d-286">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-286">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="script-migration"></a><span data-ttu-id="2df6d-287">Script-Migration</span><span class="sxs-lookup"><span data-stu-id="2df6d-287">Script-Migration</span></span>

<span data-ttu-id="2df6d-288">選択した移行のすべての変更を、選択した別の移行に適用する SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-288">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="2df6d-289">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="2df6d-289">Parameters:</span></span>

| <span data-ttu-id="2df6d-290">パラメーター</span><span class="sxs-lookup"><span data-stu-id="2df6d-290">Parameter</span></span>                    | <span data-ttu-id="2df6d-291">説明</span><span class="sxs-lookup"><span data-stu-id="2df6d-291">Description</span></span>                                                                                                                                                                                                                |
|:---------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2df6d-292">*-From*\<String></span><span class="sxs-lookup"><span data-stu-id="2df6d-292">*-From* \<String></span></span>            | <span data-ttu-id="2df6d-293">移行を開始しています。</span><span class="sxs-lookup"><span data-stu-id="2df6d-293">The starting migration.</span></span> <span data-ttu-id="2df6d-294">移行は、名前または ID で識別できます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-294">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="2df6d-295">数値0は、 *最初の移行の前に* 特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-295">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="2df6d-296">既定値は 0 です。</span><span class="sxs-lookup"><span data-stu-id="2df6d-296">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="2df6d-297">*-To*\<String></span><span class="sxs-lookup"><span data-stu-id="2df6d-297">*-To* \<String></span></span>              | <span data-ttu-id="2df6d-298">移行を終了しています。</span><span class="sxs-lookup"><span data-stu-id="2df6d-298">The ending migration.</span></span> <span data-ttu-id="2df6d-299">既定では、最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="2df6d-299">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="2df6d-300">-べき等</span><span class="sxs-lookup"><span data-stu-id="2df6d-300">-Idempotent</span></span>                  | <span data-ttu-id="2df6d-301">任意の移行時にデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-301">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="2df6d-302"><nobr>-NoTransactions</nobr></span><span class="sxs-lookup"><span data-stu-id="2df6d-302"><nobr>-NoTransactions</nobr></span></span> | <span data-ttu-id="2df6d-303">SQL トランザクションステートメントを生成しません。</span><span class="sxs-lookup"><span data-stu-id="2df6d-303">Don't generate SQL transaction statements.</span></span> <span data-ttu-id="2df6d-304">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="2df6d-304">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="2df6d-305">-出力 \<String></span><span class="sxs-lookup"><span data-stu-id="2df6d-305">-Output \<String></span></span>            | <span data-ttu-id="2df6d-306">結果の書き込み先のファイル。</span><span class="sxs-lookup"><span data-stu-id="2df6d-306">The file to write the result to.</span></span> <span data-ttu-id="2df6d-307">このパラメーターを省略すると、 */obj/Debug/netcoreapp2.1/ghbkztfz.sql/* のように、アプリのランタイムファイルが作成されたときと同じフォルダーに生成された名前でファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-307">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

<span data-ttu-id="2df6d-308">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-308">The [common parameters](#common-parameters) are listed above.</span></span>

> [!TIP]
> <span data-ttu-id="2df6d-309">To、From、および Output パラメーターでは、タブの拡張がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="2df6d-309">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="2df6d-310">次の例では、移行名を使用して、(移行のないデータベースから) InitialCreate 移行用のスクリプトを作成します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-310">The following example creates a script for the InitialCreate migration (from a database without any migrations), using the migration name.</span></span>

```powershell
Script-Migration 0 InitialCreate
```

<span data-ttu-id="2df6d-311">次の例では、移行 ID を使用して、InitialCreate 移行後のすべての移行用のスクリプトを作成します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-311">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="2df6d-312">Update-Database</span><span class="sxs-lookup"><span data-stu-id="2df6d-312">Update-Database</span></span>

<span data-ttu-id="2df6d-313">最後に移行したデータベース、または指定した移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-313">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="2df6d-314">パラメーター</span><span class="sxs-lookup"><span data-stu-id="2df6d-314">Parameter</span></span>                           | <span data-ttu-id="2df6d-315">説明</span><span class="sxs-lookup"><span data-stu-id="2df6d-315">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="2df6d-316"><nobr>*-移行*\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="2df6d-316"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="2df6d-317">ターゲットの移行。</span><span class="sxs-lookup"><span data-stu-id="2df6d-317">The target migration.</span></span> <span data-ttu-id="2df6d-318">移行は、名前または ID で識別できます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-318">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="2df6d-319">数値0は、 *最初の移行の前に* 特別なケースであり、すべての移行が元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="2df6d-319">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="2df6d-320">移行が指定されていない場合、コマンドは既定で最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="2df6d-320">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="2df6d-321"><nobr>-接続 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="2df6d-321"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="2df6d-322">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="2df6d-322">The connection string to the database.</span></span> <span data-ttu-id="2df6d-323">既定値は、またはで指定されたもの `AddDbContext` `OnConfiguring` です。</span><span class="sxs-lookup"><span data-stu-id="2df6d-323">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> <span data-ttu-id="2df6d-324">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="2df6d-324">Added in EF Core 5.0.</span></span>                                                                                                                                |

<span data-ttu-id="2df6d-325">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="2df6d-325">The [common parameters](#common-parameters) are listed above.</span></span>

> [!TIP]
> <span data-ttu-id="2df6d-326">移行パラメーターでは、タブの拡張がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="2df6d-326">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="2df6d-327">次の例では、すべての移行を元に戻します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-327">The following example reverts all migrations.</span></span>

```powershell
Update-Database 0
```

<span data-ttu-id="2df6d-328">次の例では、指定された移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-328">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="2df6d-329">最初のは移行名を使用し、2つ目のは移行 ID と指定された接続を使用します。</span><span class="sxs-lookup"><span data-stu-id="2df6d-329">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database InitialCreate
Update-Database 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="2df6d-330">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="2df6d-330">Additional resources</span></span>

* [<span data-ttu-id="2df6d-331">移行</span><span class="sxs-lookup"><span data-stu-id="2df6d-331">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="2df6d-332">リバースエンジニアリング</span><span class="sxs-lookup"><span data-stu-id="2df6d-332">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
