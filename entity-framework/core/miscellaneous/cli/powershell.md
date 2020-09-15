---
title: EF Core ツールリファレンス (パッケージマネージャーコンソール)-EF Core
description: Entity Framework Core Visual Studio パッケージマネージャーコンソールのリファレンスガイド
author: bricelam
ms.author: bricelam
ms.date: 09/09/2020
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 5dca397978c60c12610d9080caba972a66b079b6
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071863"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="43585-103">Entity Framework Core ツールリファレンス-Visual Studio のパッケージマネージャーコンソール</span><span class="sxs-lookup"><span data-stu-id="43585-103">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="43585-104">Entity Framework Core 用のパッケージマネージャーコンソール (PMC) ツールは、デザイン時の開発タスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="43585-104">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="43585-105">たとえば、既存のデータベースに基づいて、 [移行](/aspnet/core/data/ef-mvc/migrations)を作成し、移行を適用し、モデルのコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="43585-105">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="43585-106">コマンドは、Visual Studio 内で [パッケージマネージャーコンソール](/nuget/tools/package-manager-console)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="43585-106">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="43585-107">これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。</span><span class="sxs-lookup"><span data-stu-id="43585-107">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="43585-108">Visual Studio を使用していない場合は、代わりに [EF Core コマンドラインツール](xref:core/miscellaneous/cli/dotnet) を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="43585-108">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](xref:core/miscellaneous/cli/dotnet) instead.</span></span> <span data-ttu-id="43585-109">.NET Core CLI ツールはクロスプラットフォームで、コマンドプロンプト内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="43585-109">The .NET Core CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="43585-110">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="43585-110">Installing the tools</span></span>

<span data-ttu-id="43585-111">ツールのインストールと更新の手順は、ASP.NET Core 2.1 以降のバージョンまたはその他のプロジェクトの種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="43585-111">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="43585-112">ASP.NET Core バージョン2.1 以降</span><span class="sxs-lookup"><span data-stu-id="43585-112">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="43585-113">`Microsoft.EntityFrameworkCore.Tools`パッケージは[AspNetCore メタパッケージ](/aspnet/core/fundamentals/metapackage-app)に含まれているので、ツールは ASP.NET Core 2.1 以降のプロジェクトに自動的に含まれます。</span><span class="sxs-lookup"><span data-stu-id="43585-113">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="43585-114">そのため、ツールをインストールするために何もする必要はありませんが、次の操作を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="43585-114">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>

* <span data-ttu-id="43585-115">新しいプロジェクトでツールを使用する前に、パッケージを復元します。</span><span class="sxs-lookup"><span data-stu-id="43585-115">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="43585-116">パッケージをインストールして、ツールを新しいバージョンに更新します。</span><span class="sxs-lookup"><span data-stu-id="43585-116">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="43585-117">最新バージョンのツールを入手するには、次の手順も実行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="43585-117">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="43585-118">*.Csproj*ファイルを編集し、最新バージョンの[Microsoft Entityframeworkcore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)パッケージを指定する行を追加します。</span><span class="sxs-lookup"><span data-stu-id="43585-118">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="43585-119">たとえば、.csproj ファイルには、次のようなを含めることができ*ます。* `ItemGroup`</span><span class="sxs-lookup"><span data-stu-id="43585-119">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="3.1.2" />
  </ItemGroup>
  ```

<span data-ttu-id="43585-120">次の例のようなメッセージが表示されたら、ツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="43585-120">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="43585-121">EF Core ツールバージョン ' 2.1.1-30846 ' は、ランタイム ' 2.1.3-32065 ' より古いバージョンです。</span><span class="sxs-lookup"><span data-stu-id="43585-121">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="43585-122">最新の機能とバグ修正のためにツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="43585-122">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="43585-123">ツールを更新するには:</span><span class="sxs-lookup"><span data-stu-id="43585-123">To update the tools:</span></span>

* <span data-ttu-id="43585-124">最新の .NET Core SDK をインストールします。</span><span class="sxs-lookup"><span data-stu-id="43585-124">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="43585-125">Visual Studio を最新バージョンに更新します。</span><span class="sxs-lookup"><span data-stu-id="43585-125">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="43585-126">.Csproj ファイルを編集して、前に示したように、最新のツールパッケージへのパッケージ参照が含まれるようにし *ます* 。</span><span class="sxs-lookup"><span data-stu-id="43585-126">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="43585-127">その他のバージョンとプロジェクトの種類</span><span class="sxs-lookup"><span data-stu-id="43585-127">Other versions and project types</span></span>

<span data-ttu-id="43585-128">パッケージマネージャーコンソールで次のコマンドを実行して、パッケージマネージャーコンソールツールをインストールし**ます。**</span><span class="sxs-lookup"><span data-stu-id="43585-128">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="43585-129">**パッケージマネージャーコンソール**で次のコマンドを実行して、ツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="43585-129">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="43585-130">インストールの確認</span><span class="sxs-lookup"><span data-stu-id="43585-130">Verify the installation</span></span>

<span data-ttu-id="43585-131">次のコマンドを実行して、ツールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="43585-131">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="43585-132">出力は次のようになります (使用しているツールのバージョンはわかりません)。</span><span class="sxs-lookup"><span data-stu-id="43585-132">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

```console

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

## <a name="using-the-tools"></a><span data-ttu-id="43585-133">ツールの使用</span><span class="sxs-lookup"><span data-stu-id="43585-133">Using the tools</span></span>

<span data-ttu-id="43585-134">ツールを使用する前に:</span><span class="sxs-lookup"><span data-stu-id="43585-134">Before using the tools:</span></span>

* <span data-ttu-id="43585-135">ターゲットプロジェクトとスタートアッププロジェクトの違いを理解します。</span><span class="sxs-lookup"><span data-stu-id="43585-135">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="43585-136">.NET Standard クラスライブラリでツールを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="43585-136">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="43585-137">ASP.NET Core プロジェクトの場合は、環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="43585-137">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="43585-138">ターゲットプロジェクトとスタートアッププロジェクト</span><span class="sxs-lookup"><span data-stu-id="43585-138">Target and startup project</span></span>

<span data-ttu-id="43585-139">これらのコマンドは、 *プロジェクト* と *スタートアッププロジェクト*を参照します。</span><span class="sxs-lookup"><span data-stu-id="43585-139">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="43585-140">*プロジェクト*は*ターゲットプロジェクト*とも呼ばれます。これは、コマンドによってファイルが追加または削除されるためです。</span><span class="sxs-lookup"><span data-stu-id="43585-140">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="43585-141">既定では、**パッケージマネージャーコンソール**で選択された**既定のプロジェクト**はターゲットプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="43585-141">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="43585-142">オプションを使用して、別のプロジェクトをターゲットプロジェクトとして指定でき <nobr>`--project`</nobr> ます。</span><span class="sxs-lookup"><span data-stu-id="43585-142">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="43585-143">*スタートアッププロジェクト*は、ツールをビルドして実行するためのものです。</span><span class="sxs-lookup"><span data-stu-id="43585-143">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="43585-144">このツールでは、デザイン時にアプリケーションコードを実行して、プロジェクトに関する情報 (データベース接続文字列やモデルの構成など) を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="43585-144">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="43585-145">既定では、**ソリューションエクスプローラー**の**スタートアッププロジェクト**はスタートアッププロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="43585-145">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="43585-146">オプションを使用して、別のプロジェクトをスタートアッププロジェクトとして指定でき <nobr>`--startup-project`</nobr> ます。</span><span class="sxs-lookup"><span data-stu-id="43585-146">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="43585-147">多くの場合、スタートアッププロジェクトとターゲットプロジェクトは同じプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="43585-147">The startup project and target project are often the same project.</span></span> <span data-ttu-id="43585-148">個別のプロジェクトである一般的なシナリオは、次のような場合です。</span><span class="sxs-lookup"><span data-stu-id="43585-148">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="43585-149">EF Core コンテキストとエンティティクラスは、.NET Core クラスライブラリにあります。</span><span class="sxs-lookup"><span data-stu-id="43585-149">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="43585-150">.NET Core コンソールアプリまたは web アプリはクラスライブラリを参照します。</span><span class="sxs-lookup"><span data-stu-id="43585-150">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="43585-151">また、 [移行コードを EF Core コンテキストとは別のクラスライブラリに配置](xref:core/managing-schemas/migrations/projects)することもできます。</span><span class="sxs-lookup"><span data-stu-id="43585-151">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="43585-152">その他のターゲットフレームワーク</span><span class="sxs-lookup"><span data-stu-id="43585-152">Other target frameworks</span></span>

<span data-ttu-id="43585-153">パッケージマネージャーコンソールツールは、.NET Core または .NET Framework プロジェクトで使用できます。</span><span class="sxs-lookup"><span data-stu-id="43585-153">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="43585-154">.NET Standard クラスライブラリに EF Core モデルがあるアプリには、.NET Core または .NET Framework プロジェクトがない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="43585-154">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="43585-155">たとえば、これは Xamarin とユニバーサル Windows プラットフォームアプリに当てはまります。</span><span class="sxs-lookup"><span data-stu-id="43585-155">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="43585-156">このような場合は、ツールのスタートアッププロジェクトとして機能することだけを目的とした .NET Core または .NET Framework コンソールアプリプロジェクトを作成できます。</span><span class="sxs-lookup"><span data-stu-id="43585-156">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="43585-157">プロジェクトは、実際のコードを持たないダミープロジェクトにすることができ &mdash; ます。これは、ツールのターゲットを指定するためにのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="43585-157">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="43585-158">ダミープロジェクトが必要な理由</span><span class="sxs-lookup"><span data-stu-id="43585-158">Why is a dummy project required?</span></span> <span data-ttu-id="43585-159">前述のように、ツールはデザイン時にアプリケーションコードを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="43585-159">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="43585-160">そのためには、.NET Core または .NET Framework ランタイムを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="43585-160">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="43585-161">EF Core モデルが .NET Core または .NET Framework を対象とするプロジェクト内にある場合、EF Core ツールはプロジェクトからランタイムを借用します。</span><span class="sxs-lookup"><span data-stu-id="43585-161">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="43585-162">EF Core モデルが .NET Standard クラスライブラリ内にある場合は、これを行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="43585-162">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="43585-163">.NET Standard は実際の .NET 実装ではありません。これは、.NET 実装がサポートする必要のある一連の Api の仕様です。</span><span class="sxs-lookup"><span data-stu-id="43585-163">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="43585-164">したがって、EF Core ツールでアプリケーションコードを実行するために .NET Standard は十分ではありません。</span><span class="sxs-lookup"><span data-stu-id="43585-164">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="43585-165">スタートアッププロジェクトとして使用するために作成するダミープロジェクトは、ツールが .NET Standard クラスライブラリを読み込むことができる具象ターゲットプラットフォームを提供します。</span><span class="sxs-lookup"><span data-stu-id="43585-165">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="43585-166">ASP.NET Core 環境</span><span class="sxs-lookup"><span data-stu-id="43585-166">ASP.NET Core environment</span></span>

<span data-ttu-id="43585-167">ASP.NET Core プロジェクトの環境を指定するには、コマンドを実行する前に **env: ASPNETCORE_ENVIRONMENT** を設定します。</span><span class="sxs-lookup"><span data-stu-id="43585-167">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="43585-168">共通パラメーター</span><span class="sxs-lookup"><span data-stu-id="43585-168">Common parameters</span></span>

<span data-ttu-id="43585-169">次の表に、すべての EF Core コマンドに共通のパラメーターを示します。</span><span class="sxs-lookup"><span data-stu-id="43585-169">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="43585-170">パラメーター</span><span class="sxs-lookup"><span data-stu-id="43585-170">Parameter</span></span>                 | <span data-ttu-id="43585-171">説明</span><span class="sxs-lookup"><span data-stu-id="43585-171">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="43585-172">-コンテキスト \<String></span><span class="sxs-lookup"><span data-stu-id="43585-172">-Context \<String></span></span>        | <span data-ttu-id="43585-173">使用する `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="43585-173">The `DbContext` class to use.</span></span> <span data-ttu-id="43585-174">クラス名のみ、または名前空間で完全修飾されています。</span><span class="sxs-lookup"><span data-stu-id="43585-174">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="43585-175">このパラメーターを省略した場合、EF Core によってコンテキストクラスが検索されます。</span><span class="sxs-lookup"><span data-stu-id="43585-175">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="43585-176">複数のコンテキストクラスがある場合は、このパラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="43585-176">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="43585-177">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="43585-177">-Project \<String></span></span>        | <span data-ttu-id="43585-178">ターゲットプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="43585-178">The target project.</span></span> <span data-ttu-id="43585-179">このパラメーターを省略すると、**パッケージマネージャーコンソール**の**既定のプロジェクト**がターゲットプロジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="43585-179">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="43585-180"><nobr>-Startupproject</nobr>\<String></span><span class="sxs-lookup"><span data-stu-id="43585-180"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="43585-181">スタートアッププロジェクト。</span><span class="sxs-lookup"><span data-stu-id="43585-181">The startup project.</span></span> <span data-ttu-id="43585-182">このパラメーターを省略すると、**ソリューションのプロパティ**の**スタートアッププロジェクト**がターゲットプロジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="43585-182">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="43585-183">-Args \<String></span><span class="sxs-lookup"><span data-stu-id="43585-183">-Args \<String></span></span>           | <span data-ttu-id="43585-184">アプリケーションに渡される引数。</span><span class="sxs-lookup"><span data-stu-id="43585-184">Arguments passed to the application.</span></span> <span data-ttu-id="43585-185">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="43585-185">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="43585-186">-Verbose</span><span class="sxs-lookup"><span data-stu-id="43585-186">-Verbose</span></span>                  | <span data-ttu-id="43585-187">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="43585-187">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="43585-188">コマンドに関するヘルプ情報を表示するには、PowerShell のコマンドを使用し `Get-Help` ます。</span><span class="sxs-lookup"><span data-stu-id="43585-188">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="43585-189">Context、Project、および StartupProject の各パラメーターでは、タブの展開がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="43585-189">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="43585-190">移行の追加</span><span class="sxs-lookup"><span data-stu-id="43585-190">Add-Migration</span></span>

<span data-ttu-id="43585-191">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="43585-191">Adds a new migration.</span></span>

<span data-ttu-id="43585-192">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="43585-192">Parameters:</span></span>

| <span data-ttu-id="43585-193">パラメーター</span><span class="sxs-lookup"><span data-stu-id="43585-193">Parameter</span></span>                         | <span data-ttu-id="43585-194">説明</span><span class="sxs-lookup"><span data-stu-id="43585-194">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="43585-195"><nobr>-Name \<String><nobr></span><span class="sxs-lookup"><span data-stu-id="43585-195"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="43585-196">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="43585-196">The name of the migration.</span></span> <span data-ttu-id="43585-197">これは位置指定パラメーターであり、必須です。</span><span class="sxs-lookup"><span data-stu-id="43585-197">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="43585-198"><nobr>-OutputDir \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="43585-198"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="43585-199">ファイルの出力に使用するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="43585-199">The directory use to output the files.</span></span> <span data-ttu-id="43585-200">パスは、ターゲットプロジェクトディレクトリに対する相対パスです。</span><span class="sxs-lookup"><span data-stu-id="43585-200">Paths are relative to the target project directory.</span></span> <span data-ttu-id="43585-201">既定値は "移行" です。</span><span class="sxs-lookup"><span data-stu-id="43585-201">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="43585-202"><nobr>-名前空間 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="43585-202"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="43585-203">生成されたクラスに使用する名前空間。</span><span class="sxs-lookup"><span data-stu-id="43585-203">The namespace to use for the generated classes.</span></span> <span data-ttu-id="43585-204">既定値は、出力ディレクトリから生成されます。</span><span class="sxs-lookup"><span data-stu-id="43585-204">Defaults to generated from the output directory.</span></span> <span data-ttu-id="43585-205">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="43585-205">Added in EF Core 5.0.</span></span>  |

<span data-ttu-id="43585-206">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="43585-206">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="drop-database"></a><span data-ttu-id="43585-207">Drop Database</span><span class="sxs-lookup"><span data-stu-id="43585-207">Drop-Database</span></span>

<span data-ttu-id="43585-208">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="43585-208">Drops the database.</span></span>

<span data-ttu-id="43585-209">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="43585-209">Parameters:</span></span>

| <span data-ttu-id="43585-210">パラメーター</span><span class="sxs-lookup"><span data-stu-id="43585-210">Parameter</span></span> | <span data-ttu-id="43585-211">説明</span><span class="sxs-lookup"><span data-stu-id="43585-211">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="43585-212">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="43585-212">-WhatIf</span></span>   | <span data-ttu-id="43585-213">削除するデータベースを表示しますが、削除はしません。</span><span class="sxs-lookup"><span data-stu-id="43585-213">Show which database would be dropped, but don't drop it.</span></span> |

<span data-ttu-id="43585-214">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="43585-214">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="get-dbcontext"></a><span data-ttu-id="43585-215">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="43585-215">Get-DbContext</span></span>

<span data-ttu-id="43585-216">使用可能な種類に関する情報を一覧表示して取得し `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="43585-216">Lists and gets information about available `DbContext` types.</span></span>

<span data-ttu-id="43585-217">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="43585-217">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="get-migration"></a><span data-ttu-id="43585-218">取得-移行</span><span class="sxs-lookup"><span data-stu-id="43585-218">Get-Migration</span></span>

<span data-ttu-id="43585-219">使用可能な移行を一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="43585-219">Lists available migrations.</span></span> <span data-ttu-id="43585-220">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="43585-220">Added in EF Core 5.0.</span></span>

<span data-ttu-id="43585-221">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="43585-221">Parameters:</span></span>

| <span data-ttu-id="43585-222">パラメーター</span><span class="sxs-lookup"><span data-stu-id="43585-222">Parameter</span></span>                          | <span data-ttu-id="43585-223">説明</span><span class="sxs-lookup"><span data-stu-id="43585-223">Description</span></span>                                                                                            |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------ |
| <span data-ttu-id="43585-224"><nobr>-接続 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="43585-224"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="43585-225">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="43585-225">The connection string to the database.</span></span> <span data-ttu-id="43585-226">既定値は、AddDbContext または OnConfiguring で指定されたものです。</span><span class="sxs-lookup"><span data-stu-id="43585-226">Defaults to the one specified in AddDbContext or OnConfiguring.</span></span> |
| <span data-ttu-id="43585-227">-NoConnect</span><span class="sxs-lookup"><span data-stu-id="43585-227">-NoConnect</span></span>                         | <span data-ttu-id="43585-228">データベースに接続しないでください。</span><span class="sxs-lookup"><span data-stu-id="43585-228">Don't connect to the database.</span></span>                                                                         |

<span data-ttu-id="43585-229">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="43585-229">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="43585-230">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="43585-230">Remove-Migration</span></span>

<span data-ttu-id="43585-231">最後の移行を削除します (移行のために実行されたコード変更をロールバックします)。</span><span class="sxs-lookup"><span data-stu-id="43585-231">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="43585-232">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="43585-232">Parameters:</span></span>

| <span data-ttu-id="43585-233">パラメーター</span><span class="sxs-lookup"><span data-stu-id="43585-233">Parameter</span></span> | <span data-ttu-id="43585-234">説明</span><span class="sxs-lookup"><span data-stu-id="43585-234">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="43585-235">-Force</span><span class="sxs-lookup"><span data-stu-id="43585-235">-Force</span></span>    | <span data-ttu-id="43585-236">移行を元に戻します (データベースに適用された変更をロールバックします)。</span><span class="sxs-lookup"><span data-stu-id="43585-236">Revert the migration (roll back the changes that were applied to the database).</span></span> |

<span data-ttu-id="43585-237">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="43585-237">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="scaffold-dbcontext"></a><span data-ttu-id="43585-238">スキャフォールディング-DbContext</span><span class="sxs-lookup"><span data-stu-id="43585-238">Scaffold-DbContext</span></span>

<span data-ttu-id="43585-239">`DbContext`データベースのおよびエンティティ型のコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="43585-239">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="43585-240">で `Scaffold-DbContext` エンティティ型を生成するには、データベーステーブルに主キーが必要です。</span><span class="sxs-lookup"><span data-stu-id="43585-240">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="43585-241">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="43585-241">Parameters:</span></span>

| <span data-ttu-id="43585-242">パラメーター</span><span class="sxs-lookup"><span data-stu-id="43585-242">Parameter</span></span>                          | <span data-ttu-id="43585-243">説明</span><span class="sxs-lookup"><span data-stu-id="43585-243">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="43585-244"><nobr>-接続 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="43585-244"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="43585-245">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="43585-245">The connection string to the database.</span></span> <span data-ttu-id="43585-246">ASP.NET Core 2.x プロジェクトの場合、値には\*name = \<name of connection string> \*を指定できます。</span><span class="sxs-lookup"><span data-stu-id="43585-246">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="43585-247">その場合、プロジェクト用に設定されている構成ソースから名前を取得します。</span><span class="sxs-lookup"><span data-stu-id="43585-247">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="43585-248">これは位置指定パラメーターであり、必須です。</span><span class="sxs-lookup"><span data-stu-id="43585-248">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="43585-249"><nobr>-プロバイダー \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="43585-249"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="43585-250">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="43585-250">The provider to use.</span></span> <span data-ttu-id="43585-251">通常、これは NuGet パッケージの名前です (例:) `Microsoft.EntityFrameworkCore.SqlServer` 。</span><span class="sxs-lookup"><span data-stu-id="43585-251">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="43585-252">これは位置指定パラメーターであり、必須です。</span><span class="sxs-lookup"><span data-stu-id="43585-252">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="43585-253">-OutputDir \<String></span><span class="sxs-lookup"><span data-stu-id="43585-253">-OutputDir \<String></span></span>               | <span data-ttu-id="43585-254">ファイルを格納するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="43585-254">The directory to put files in.</span></span> <span data-ttu-id="43585-255">パスは、プロジェクトディレクトリに対する相対パスです。</span><span class="sxs-lookup"><span data-stu-id="43585-255">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="43585-256">-ContextDir \<String></span><span class="sxs-lookup"><span data-stu-id="43585-256">-ContextDir \<String></span></span>              | <span data-ttu-id="43585-257">ファイルを格納するディレクトリ `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="43585-257">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="43585-258">パスは、プロジェクトディレクトリに対する相対パスです。</span><span class="sxs-lookup"><span data-stu-id="43585-258">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="43585-259">-名前空間 \<String></span><span class="sxs-lookup"><span data-stu-id="43585-259">-Namespace \<String></span></span>               | <span data-ttu-id="43585-260">生成されたすべてのクラスに使用する名前空間。</span><span class="sxs-lookup"><span data-stu-id="43585-260">The namespace to use for all generated classes.</span></span> <span data-ttu-id="43585-261">既定値は、ルート名前空間と出力ディレクトリから生成されます。</span><span class="sxs-lookup"><span data-stu-id="43585-261">Defaults to generated from the root namespace and the output directory.</span></span> <span data-ttu-id="43585-262">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="43585-262">Added in EF Core 5.0.</span></span>                                                                                                                           |
| <span data-ttu-id="43585-263">-ContextNamespace \<String></span><span class="sxs-lookup"><span data-stu-id="43585-263">-ContextNamespace \<String></span></span>        | <span data-ttu-id="43585-264">生成されたクラスに使用する名前空間 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="43585-264">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="43585-265">注: はオーバーライドさ `-Namespace` れます。</span><span class="sxs-lookup"><span data-stu-id="43585-265">Note: overrides `-Namespace`.</span></span> <span data-ttu-id="43585-266">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="43585-266">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="43585-267">-コンテキスト \<String></span><span class="sxs-lookup"><span data-stu-id="43585-267">-Context \<String></span></span>                 | <span data-ttu-id="43585-268">`DbContext`生成するクラスの名前。</span><span class="sxs-lookup"><span data-stu-id="43585-268">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="43585-269">-スキーマ \<String[]></span><span class="sxs-lookup"><span data-stu-id="43585-269">-Schemas \<String[]></span></span>               | <span data-ttu-id="43585-270">エンティティ型を生成するテーブルのスキーマ。</span><span class="sxs-lookup"><span data-stu-id="43585-270">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="43585-271">このパラメーターを省略すると、すべてのスキーマが含まれます。</span><span class="sxs-lookup"><span data-stu-id="43585-271">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="43585-272">-テーブル \<String[]></span><span class="sxs-lookup"><span data-stu-id="43585-272">-Tables \<String[]></span></span>                | <span data-ttu-id="43585-273">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="43585-273">The tables to generate entity types for.</span></span> <span data-ttu-id="43585-274">このパラメーターを省略すると、すべてのテーブルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="43585-274">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="43585-275">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="43585-275">-DataAnnotations</span></span>                   | <span data-ttu-id="43585-276">属性を使用してモデルを構成します (可能な場合)。</span><span class="sxs-lookup"><span data-stu-id="43585-276">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="43585-277">このパラメーターを省略した場合は、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="43585-277">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="43585-278">-UseDatabaseNames 場合</span><span class="sxs-lookup"><span data-stu-id="43585-278">-UseDatabaseNames</span></span>                  | <span data-ttu-id="43585-279">テーブル名と列名は、データベースに表示されるとおりに使用します。</span><span class="sxs-lookup"><span data-stu-id="43585-279">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="43585-280">このパラメーターを省略した場合、データベース名は、C# の名前のスタイル規則により厳密に準拠するように変更されます。</span><span class="sxs-lookup"><span data-stu-id="43585-280">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="43585-281">-Force</span><span class="sxs-lookup"><span data-stu-id="43585-281">-Force</span></span>                             | <span data-ttu-id="43585-282">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="43585-282">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |
| <span data-ttu-id="43585-283">-NoOnConfiguring</span><span class="sxs-lookup"><span data-stu-id="43585-283">-NoOnConfiguring</span></span>                   | <span data-ttu-id="43585-284">生成しません `DbContext.OnConfiguring` 。</span><span class="sxs-lookup"><span data-stu-id="43585-284">Don't generate `DbContext.OnConfiguring`.</span></span> <span data-ttu-id="43585-285">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="43585-285">Added in EF Core 5.0.</span></span>                                                                                                                                                                                                         |
| <span data-ttu-id="43585-286">-NoPluralize</span><span class="sxs-lookup"><span data-stu-id="43585-286">-NoPluralize</span></span>                       | <span data-ttu-id="43585-287">プルーラライザーは使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="43585-287">Don't use the pluralizer.</span></span> <span data-ttu-id="43585-288">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="43585-288">Added in EF Core 5.0.</span></span>                                                                                                                                                                                                                         |

<span data-ttu-id="43585-289">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="43585-289">The [common parameters](#common-parameters) are listed above.</span></span>

<span data-ttu-id="43585-290">例:</span><span class="sxs-lookup"><span data-stu-id="43585-290">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="43585-291">選択されたテーブルのみをスキャフォールディングし、指定された名前と名前空間を持つ別のフォルダーにコンテキストを作成する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="43585-291">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-dbcontext"></a><span data-ttu-id="43585-292">スクリプト-DbContext</span><span class="sxs-lookup"><span data-stu-id="43585-292">Script-DbContext</span></span>

<span data-ttu-id="43585-293">DbContext から SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="43585-293">Generates a SQL script from the DbContext.</span></span> <span data-ttu-id="43585-294">すべての移行をバイパスします。</span><span class="sxs-lookup"><span data-stu-id="43585-294">Bypasses any migrations.</span></span> <span data-ttu-id="43585-295">EF Core 3.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="43585-295">Added in EF Core 3.0.</span></span>

<span data-ttu-id="43585-296">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="43585-296">Parameters:</span></span>

| <span data-ttu-id="43585-297">パラメーター</span><span class="sxs-lookup"><span data-stu-id="43585-297">Parameter</span></span>                      | <span data-ttu-id="43585-298">説明</span><span class="sxs-lookup"><span data-stu-id="43585-298">Description</span></span>                      |
| ------------------------------ | -------------------------------- |
| <span data-ttu-id="43585-299"><nobr>-出力 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="43585-299"><nobr>-Output \<String></nobr></span></span> | <span data-ttu-id="43585-300">結果の書き込み先のファイル。</span><span class="sxs-lookup"><span data-stu-id="43585-300">The file to write the result to.</span></span> |

<span data-ttu-id="43585-301">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="43585-301">The [common parameters](#common-parameters) are listed above.</span></span>

## <a name="script-migration"></a><span data-ttu-id="43585-302">スクリプト-移行</span><span class="sxs-lookup"><span data-stu-id="43585-302">Script-Migration</span></span>

<span data-ttu-id="43585-303">選択した移行のすべての変更を、選択した別の移行に適用する SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="43585-303">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="43585-304">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="43585-304">Parameters:</span></span>

| <span data-ttu-id="43585-305">パラメーター</span><span class="sxs-lookup"><span data-stu-id="43585-305">Parameter</span></span>                    | <span data-ttu-id="43585-306">説明</span><span class="sxs-lookup"><span data-stu-id="43585-306">Description</span></span>                                                                                                                                                                                                                |
|:---------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="43585-307">*-From*\<String></span><span class="sxs-lookup"><span data-stu-id="43585-307">*-From* \<String></span></span>            | <span data-ttu-id="43585-308">移行を開始しています。</span><span class="sxs-lookup"><span data-stu-id="43585-308">The starting migration.</span></span> <span data-ttu-id="43585-309">移行は、名前または ID で識別できます。</span><span class="sxs-lookup"><span data-stu-id="43585-309">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="43585-310">数値0は、 *最初の移行の前に*特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="43585-310">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="43585-311">既定値は 0 です。</span><span class="sxs-lookup"><span data-stu-id="43585-311">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="43585-312">*-To*\<String></span><span class="sxs-lookup"><span data-stu-id="43585-312">*-To* \<String></span></span>              | <span data-ttu-id="43585-313">移行を終了しています。</span><span class="sxs-lookup"><span data-stu-id="43585-313">The ending migration.</span></span> <span data-ttu-id="43585-314">既定では、最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="43585-314">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="43585-315">-べき等</span><span class="sxs-lookup"><span data-stu-id="43585-315">-Idempotent</span></span>                  | <span data-ttu-id="43585-316">任意の移行時にデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="43585-316">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="43585-317"><nobr>-NoTransactions</nobr></span><span class="sxs-lookup"><span data-stu-id="43585-317"><nobr>-NoTransactions</nobr></span></span> | <span data-ttu-id="43585-318">SQL トランザクションステートメントを生成しません。</span><span class="sxs-lookup"><span data-stu-id="43585-318">Don't generate SQL transaction statements.</span></span> <span data-ttu-id="43585-319">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="43585-319">Added in EF Core 5.0.</span></span>                                                                                                                                                           |
| <span data-ttu-id="43585-320">-出力 \<String></span><span class="sxs-lookup"><span data-stu-id="43585-320">-Output \<String></span></span>            | <span data-ttu-id="43585-321">結果の書き込み先のファイル。</span><span class="sxs-lookup"><span data-stu-id="43585-321">The file to write the result to.</span></span> <span data-ttu-id="43585-322">このパラメーターを省略すると、 */obj/Debug/netcoreapp2.1/ghbkztfz.sql/* のように、アプリのランタイムファイルが作成されたときと同じフォルダーに生成された名前でファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="43585-322">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

<span data-ttu-id="43585-323">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="43585-323">The [common parameters](#common-parameters) are listed above.</span></span>

> [!TIP]
> <span data-ttu-id="43585-324">To、From、および Output パラメーターでは、タブの拡張がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="43585-324">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="43585-325">次の例では、移行名を使用して、InitialCreate 移行用のスクリプトを作成します。</span><span class="sxs-lookup"><span data-stu-id="43585-325">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="43585-326">次の例では、移行 ID を使用して、InitialCreate 移行後のすべての移行用のスクリプトを作成します。</span><span class="sxs-lookup"><span data-stu-id="43585-326">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="43585-327">データベースの更新</span><span class="sxs-lookup"><span data-stu-id="43585-327">Update-Database</span></span>

<span data-ttu-id="43585-328">最後に移行したデータベース、または指定した移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="43585-328">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="43585-329">パラメーター</span><span class="sxs-lookup"><span data-stu-id="43585-329">Parameter</span></span>                           | <span data-ttu-id="43585-330">説明</span><span class="sxs-lookup"><span data-stu-id="43585-330">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="43585-331"><nobr>*-移行*\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="43585-331"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="43585-332">ターゲットの移行。</span><span class="sxs-lookup"><span data-stu-id="43585-332">The target migration.</span></span> <span data-ttu-id="43585-333">移行は、名前または ID で識別できます。</span><span class="sxs-lookup"><span data-stu-id="43585-333">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="43585-334">数値0は、 *最初の移行の前に* 特別なケースであり、すべての移行が元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="43585-334">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="43585-335">移行が指定されていない場合、コマンドは既定で最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="43585-335">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="43585-336"><nobr>-接続 \<String></nobr></span><span class="sxs-lookup"><span data-stu-id="43585-336"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="43585-337">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="43585-337">The connection string to the database.</span></span> <span data-ttu-id="43585-338">既定値は、またはで指定されたもの `AddDbContext` `OnConfiguring` です。</span><span class="sxs-lookup"><span data-stu-id="43585-338">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> <span data-ttu-id="43585-339">EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="43585-339">Added in EF Core 5.0.</span></span>                                                                                                                                |

<span data-ttu-id="43585-340">[共通パラメーター](#common-parameters)は上記のとおりです。</span><span class="sxs-lookup"><span data-stu-id="43585-340">The [common parameters](#common-parameters) are listed above.</span></span>

> [!TIP]
> <span data-ttu-id="43585-341">移行パラメーターでは、タブの拡張がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="43585-341">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="43585-342">次の例では、すべての移行を元に戻します。</span><span class="sxs-lookup"><span data-stu-id="43585-342">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="43585-343">次の例では、指定された移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="43585-343">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="43585-344">最初のは移行名を使用し、2つ目のは移行 ID と指定された接続を使用します。</span><span class="sxs-lookup"><span data-stu-id="43585-344">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="43585-345">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="43585-345">Additional resources</span></span>

* [<span data-ttu-id="43585-346">移行</span><span class="sxs-lookup"><span data-stu-id="43585-346">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="43585-347">リバースエンジニアリング</span><span class="sxs-lookup"><span data-stu-id="43585-347">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
