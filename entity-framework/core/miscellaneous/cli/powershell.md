---
title: EF Core ツールリファレンス (パッケージマネージャーコンソール)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 45370a82131da9db8b724fe395d41b1e3641fcf8
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181329"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="f0be8-102">Entity Framework Core ツールリファレンス-Visual Studio のパッケージマネージャーコンソール</span><span class="sxs-lookup"><span data-stu-id="f0be8-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="f0be8-103">Entity Framework Core 用のパッケージマネージャーコンソール (PMC) ツールは、デザイン時の開発タスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="f0be8-104">たとえば、既存のデータベースに基づいて、[移行](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0)を作成し、移行を適用し、モデルのコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="f0be8-105">コマンドは、Visual Studio 内で[パッケージマネージャーコンソール](/nuget/tools/package-manager-console)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="f0be8-106">これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="f0be8-107">Visual Studio を使用していない場合は、代わりに[EF Core コマンドラインツール](dotnet.md)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f0be8-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="f0be8-108">CLI ツールはクロスプラットフォームで、コマンドプロンプト内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-108">The CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="f0be8-109">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="f0be8-109">Installing the tools</span></span>

<span data-ttu-id="f0be8-110">ツールのインストールと更新の手順は、ASP.NET Core 2.1 以降のバージョンまたはその他のプロジェクトの種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="f0be8-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="f0be8-111">ASP.NET Core バージョン2.1 以降</span><span class="sxs-lookup"><span data-stu-id="f0be8-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="f0be8-112">@No__t 0 のパッケージは[AspNetCore メタパッケージ](/aspnet/core/fundamentals/metapackage-app)に含まれているため、ツールは ASP.NET Core 2.1 以降のプロジェクトに自動的に含まれます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f0be8-113">そのため、ツールをインストールするために何もする必要はありませんが、次の操作を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="f0be8-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>
* <span data-ttu-id="f0be8-114">新しいプロジェクトでツールを使用する前に、パッケージを復元します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="f0be8-115">パッケージをインストールして、ツールを新しいバージョンに更新します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="f0be8-116">最新バージョンのツールを入手するには、次の手順も実行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f0be8-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="f0be8-117">*.Csproj*ファイルを編集し、最新バージョンの[Microsoft Entityframeworkcore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)パッケージを指定する行を追加します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="f0be8-118">たとえば、.csproj ファイルには、次のような `ItemGroup` が含ま*れ*ている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f0be8-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

<span data-ttu-id="f0be8-119">次の例のようなメッセージが表示されたら、ツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="f0be8-120">EF Core ツールバージョン ' 2.1.1-30846 ' は、ランタイム ' 2.1.3-32065 ' より古いバージョンです。</span><span class="sxs-lookup"><span data-stu-id="f0be8-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="f0be8-121">最新の機能とバグ修正のためにツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="f0be8-122">ツールを更新するには:</span><span class="sxs-lookup"><span data-stu-id="f0be8-122">To update the tools:</span></span>
* <span data-ttu-id="f0be8-123">最新の .NET Core SDK をインストールします。</span><span class="sxs-lookup"><span data-stu-id="f0be8-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="f0be8-124">Visual Studio を最新バージョンに更新します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="f0be8-125">.Csproj ファイルを編集して、前に示したように、最新のツールパッケージへのパッケージ参照が含まれるようにし*ます*。</span><span class="sxs-lookup"><span data-stu-id="f0be8-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="f0be8-126">その他のバージョンとプロジェクトの種類</span><span class="sxs-lookup"><span data-stu-id="f0be8-126">Other versions and project types</span></span>

<span data-ttu-id="f0be8-127">パッケージマネージャーコンソールで次のコマンドを実行して、パッケージマネージャーコンソールツールをインストールし**ます。**</span><span class="sxs-lookup"><span data-stu-id="f0be8-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="f0be8-128">**パッケージマネージャーコンソール**で次のコマンドを実行して、ツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="f0be8-129">インストールを確認する</span><span class="sxs-lookup"><span data-stu-id="f0be8-129">Verify the installation</span></span>

<span data-ttu-id="f0be8-130">次のコマンドを実行して、ツールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="f0be8-131">出力は次のようになります (使用しているツールのバージョンはわかりません)。</span><span class="sxs-lookup"><span data-stu-id="f0be8-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

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

## <a name="using-the-tools"></a><span data-ttu-id="f0be8-132">ツールの使用</span><span class="sxs-lookup"><span data-stu-id="f0be8-132">Using the tools</span></span>

<span data-ttu-id="f0be8-133">ツールを使用する前に:</span><span class="sxs-lookup"><span data-stu-id="f0be8-133">Before using the tools:</span></span>
* <span data-ttu-id="f0be8-134">ターゲットプロジェクトとスタートアッププロジェクトの違いを理解します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="f0be8-135">.NET Standard クラスライブラリでツールを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="f0be8-136">ASP.NET Core プロジェクトの場合は、環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="f0be8-137">ターゲットプロジェクトとスタートアッププロジェクト</span><span class="sxs-lookup"><span data-stu-id="f0be8-137">Target and startup project</span></span>

<span data-ttu-id="f0be8-138">これらのコマンドは、*プロジェクト*と*スタートアッププロジェクト*を参照します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="f0be8-139">*プロジェクト*は*ターゲットプロジェクト*とも呼ばれます。これは、コマンドによってファイルが追加または削除されるためです。</span><span class="sxs-lookup"><span data-stu-id="f0be8-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="f0be8-140">既定では、**パッケージマネージャーコンソール**で選択された**既定のプロジェクト**はターゲットプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="f0be8-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="f0be8-141"><nobr>@No__t-1</nobr>オプションを使用して、別のプロジェクトをターゲットプロジェクトとして指定できます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="f0be8-142">*スタートアッププロジェクト*は、ツールをビルドして実行するためのものです。</span><span class="sxs-lookup"><span data-stu-id="f0be8-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="f0be8-143">このツールでは、デザイン時にアプリケーションコードを実行して、プロジェクトに関する情報 (データベース接続文字列やモデルの構成など) を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f0be8-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="f0be8-144">既定では、**ソリューションエクスプローラー**の**スタートアッププロジェクト**はスタートアッププロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="f0be8-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="f0be8-145"><nobr>@No__t-1</nobr>オプションを使用して、別のプロジェクトをスタートアッププロジェクトとして指定できます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="f0be8-146">多くの場合、スタートアッププロジェクトとターゲットプロジェクトは同じプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="f0be8-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="f0be8-147">個別のプロジェクトである一般的なシナリオは、次のような場合です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="f0be8-148">EF Core コンテキストとエンティティクラスは、.NET Core クラスライブラリにあります。</span><span class="sxs-lookup"><span data-stu-id="f0be8-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="f0be8-149">.NET Core コンソールアプリまたは web アプリはクラスライブラリを参照します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="f0be8-150">また、[移行コードを EF Core コンテキストとは別のクラスライブラリに配置](xref:core/managing-schemas/migrations/projects)することもできます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="f0be8-151">その他のターゲットフレームワーク</span><span class="sxs-lookup"><span data-stu-id="f0be8-151">Other target frameworks</span></span>

<span data-ttu-id="f0be8-152">パッケージマネージャーコンソールツールは、.NET Core または .NET Framework プロジェクトで使用できます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="f0be8-153">.NET Standard クラスライブラリに EF Core モデルがあるアプリには、.NET Core または .NET Framework プロジェクトがない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f0be8-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="f0be8-154">たとえば、これは Xamarin とユニバーサル Windows プラットフォームアプリに当てはまります。</span><span class="sxs-lookup"><span data-stu-id="f0be8-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="f0be8-155">このような場合は、ツールのスタートアッププロジェクトとして機能することだけを目的とした .NET Core または .NET Framework コンソールアプリプロジェクトを作成できます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="f0be8-156">プロジェクトは、実際のコード @no__t ないダミープロジェクトにすることができます。0は、ツールのターゲットを指定する場合にのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="f0be8-157">ダミープロジェクトが必要な理由</span><span class="sxs-lookup"><span data-stu-id="f0be8-157">Why is a dummy project required?</span></span> <span data-ttu-id="f0be8-158">前述のように、ツールはデザイン時にアプリケーションコードを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f0be8-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="f0be8-159">そのためには、.NET Core または .NET Framework ランタイムを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f0be8-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="f0be8-160">EF Core モデルが .NET Core または .NET Framework を対象とするプロジェクト内にある場合、EF Core ツールはプロジェクトからランタイムを借用します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="f0be8-161">EF Core モデルが .NET Standard クラスライブラリ内にある場合は、これを行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="f0be8-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="f0be8-162">.NET Standard は実際の .NET 実装ではありません。これは、.NET 実装がサポートする必要のある一連の Api の仕様です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="f0be8-163">したがって、EF Core ツールでアプリケーションコードを実行するために .NET Standard は十分ではありません。</span><span class="sxs-lookup"><span data-stu-id="f0be8-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="f0be8-164">スタートアッププロジェクトとして使用するために作成するダミープロジェクトは、ツールが .NET Standard クラスライブラリを読み込むことができる具象ターゲットプラットフォームを提供します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="f0be8-165">ASP.NET Core 環境</span><span class="sxs-lookup"><span data-stu-id="f0be8-165">ASP.NET Core environment</span></span>

<span data-ttu-id="f0be8-166">ASP.NET Core プロジェクトの環境を指定するには、コマンドを実行する前に**env: ASPNETCORE_ENVIRONMENT**を設定します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="f0be8-167">共通パラメーター</span><span class="sxs-lookup"><span data-stu-id="f0be8-167">Common parameters</span></span>

<span data-ttu-id="f0be8-168">次の表に、すべての EF Core コマンドに共通のパラメーターを示します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="f0be8-169">パラメーター</span><span class="sxs-lookup"><span data-stu-id="f0be8-169">Parameter</span></span>                 | <span data-ttu-id="f0be8-170">説明</span><span class="sxs-lookup"><span data-stu-id="f0be8-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f0be8-171">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="f0be8-171">-Context \<String></span></span>        | <span data-ttu-id="f0be8-172">使用する `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="f0be8-172">The `DbContext` class to use.</span></span> <span data-ttu-id="f0be8-173">クラス名のみ、または名前空間で完全修飾されています。</span><span class="sxs-lookup"><span data-stu-id="f0be8-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="f0be8-174">このパラメーターを省略した場合、EF Core によってコンテキストクラスが検索されます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="f0be8-175">複数のコンテキストクラスがある場合は、このパラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="f0be8-176">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="f0be8-176">-Project \<String></span></span>        | <span data-ttu-id="f0be8-177">ターゲットプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="f0be8-177">The target project.</span></span> <span data-ttu-id="f0be8-178">このパラメーターを省略すると、**パッケージマネージャーコンソール**の**既定のプロジェクト**がターゲットプロジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="f0be8-179">-StartupProject \<String></span><span class="sxs-lookup"><span data-stu-id="f0be8-179">-StartupProject \<String></span></span> | <span data-ttu-id="f0be8-180">スタートアッププロジェクト。</span><span class="sxs-lookup"><span data-stu-id="f0be8-180">The startup project.</span></span> <span data-ttu-id="f0be8-181">このパラメーターを省略すると、**ソリューションのプロパティ**の**スタートアッププロジェクト**がターゲットプロジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="f0be8-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="f0be8-182">-Verbose</span></span>                  | <span data-ttu-id="f0be8-183">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="f0be8-184">コマンドに関するヘルプ情報を表示するには、PowerShell の `Get-Help` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="f0be8-185">Context、Project、および StartupProject の各パラメーターでは、タブの展開がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f0be8-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="f0be8-186">Add-Migration</span><span class="sxs-lookup"><span data-stu-id="f0be8-186">Add-Migration</span></span>

<span data-ttu-id="f0be8-187">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-187">Adds a new migration.</span></span>

<span data-ttu-id="f0be8-188">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="f0be8-188">Parameters:</span></span>

| <span data-ttu-id="f0be8-189">パラメーター</span><span class="sxs-lookup"><span data-stu-id="f0be8-189">Parameter</span></span>                         | <span data-ttu-id="f0be8-190">説明</span><span class="sxs-lookup"><span data-stu-id="f0be8-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f0be8-191"><nobr>-Name\<String><nobr></span><span class="sxs-lookup"><span data-stu-id="f0be8-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="f0be8-192">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="f0be8-192">The name of the migration.</span></span> <span data-ttu-id="f0be8-193">位置指定パラメーターは、これが必要です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="f0be8-194"><nobr>-OutputDir\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="f0be8-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="f0be8-195">使用するディレクトリ (およびサブ名前空間)。</span><span class="sxs-lookup"><span data-stu-id="f0be8-195">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="f0be8-196">パスでは、ターゲット プロジェクトのディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="f0be8-197">既定値は「移行」です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-197">Defaults to "Migrations".</span></span> |

## <a name="drop-database"></a><span data-ttu-id="f0be8-198">Drop-Database</span><span class="sxs-lookup"><span data-stu-id="f0be8-198">Drop-Database</span></span>

<span data-ttu-id="f0be8-199">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-199">Drops the database.</span></span>

<span data-ttu-id="f0be8-200">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="f0be8-200">Parameters:</span></span>

| <span data-ttu-id="f0be8-201">パラメーター</span><span class="sxs-lookup"><span data-stu-id="f0be8-201">Parameter</span></span> | <span data-ttu-id="f0be8-202">説明</span><span class="sxs-lookup"><span data-stu-id="f0be8-202">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="f0be8-203">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="f0be8-203">-WhatIf</span></span>   | <span data-ttu-id="f0be8-204">削除するデータベースを表示しますが、削除はしません。</span><span class="sxs-lookup"><span data-stu-id="f0be8-204">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="f0be8-205">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="f0be8-205">Get-DbContext</span></span>

<span data-ttu-id="f0be8-206">@No__t 0 の型に関する情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-206">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="f0be8-207">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="f0be8-207">Remove-Migration</span></span>

<span data-ttu-id="f0be8-208">最後の移行を削除します (移行のために実行されたコード変更をロールバックします)。</span><span class="sxs-lookup"><span data-stu-id="f0be8-208">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="f0be8-209">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="f0be8-209">Parameters:</span></span>

| <span data-ttu-id="f0be8-210">パラメーター</span><span class="sxs-lookup"><span data-stu-id="f0be8-210">Parameter</span></span> | <span data-ttu-id="f0be8-211">説明</span><span class="sxs-lookup"><span data-stu-id="f0be8-211">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="f0be8-212">-Force</span><span class="sxs-lookup"><span data-stu-id="f0be8-212">-Force</span></span>    | <span data-ttu-id="f0be8-213">移行を元に戻します (データベースに適用された変更をロールバックします)。</span><span class="sxs-lookup"><span data-stu-id="f0be8-213">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="f0be8-214">スキャフォールディング-DbContext</span><span class="sxs-lookup"><span data-stu-id="f0be8-214">Scaffold-DbContext</span></span>

<span data-ttu-id="f0be8-215">データベースの @no__t 0 およびエンティティ型のコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-215">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="f0be8-216">@No__t 0 でエンティティ型を生成するには、データベーステーブルに主キーが必要です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-216">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="f0be8-217">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="f0be8-217">Parameters:</span></span>

| <span data-ttu-id="f0be8-218">パラメーター</span><span class="sxs-lookup"><span data-stu-id="f0be8-218">Parameter</span></span>                          | <span data-ttu-id="f0be8-219">説明</span><span class="sxs-lookup"><span data-stu-id="f0be8-219">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f0be8-220"><nobr>-接続 \< 文字列 ></nobr></span><span class="sxs-lookup"><span data-stu-id="f0be8-220"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="f0be8-221">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="f0be8-221">The connection string to the database.</span></span> <span data-ttu-id="f0be8-222">ASP.NET Core 2.x プロジェクトの場合、*接続文字列 > の名前 = \<* を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-222">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="f0be8-223">その場合、プロジェクト用に設定されている構成ソースから名前を取得します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-223">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="f0be8-224">位置指定パラメーターは、これが必要です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-224">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="f0be8-225"><nobr>-プロバイダー \< 文字列 ></nobr></span><span class="sxs-lookup"><span data-stu-id="f0be8-225"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="f0be8-226">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="f0be8-226">The provider to use.</span></span> <span data-ttu-id="f0be8-227">通常、これは NuGet パッケージの名前です。たとえば、`Microsoft.EntityFrameworkCore.SqlServer` です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-227">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="f0be8-228">位置指定パラメーターは、これが必要です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-228">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="f0be8-229">-OutputDir\<String></span><span class="sxs-lookup"><span data-stu-id="f0be8-229">-OutputDir \<String></span></span>               | <span data-ttu-id="f0be8-230">ファイルを配置するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="f0be8-230">The directory to put files in.</span></span> <span data-ttu-id="f0be8-231">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-231">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="f0be8-232">-ContextDir\<String></span><span class="sxs-lookup"><span data-stu-id="f0be8-232">-ContextDir \<String></span></span>              | <span data-ttu-id="f0be8-233">格納するディレクトリ、`DbContext`ファイルします。</span><span class="sxs-lookup"><span data-stu-id="f0be8-233">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="f0be8-234">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-234">Paths are relative to the project directory.</span></span>                                                                                                                                                                              |
| <span data-ttu-id="f0be8-235">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="f0be8-235">-Context \<String></span></span>                 | <span data-ttu-id="f0be8-236">生成する `DbContext` クラスの名前。</span><span class="sxs-lookup"><span data-stu-id="f0be8-236">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="f0be8-237">-Schemas \<String[]></span><span class="sxs-lookup"><span data-stu-id="f0be8-237">-Schemas \<String[]></span></span>               | <span data-ttu-id="f0be8-238">エンティティ型を生成するテーブルのスキーマ。</span><span class="sxs-lookup"><span data-stu-id="f0be8-238">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="f0be8-239">このパラメーターを省略すると、すべてのスキーマが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-239">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="f0be8-240">-Tables \<String[]></span><span class="sxs-lookup"><span data-stu-id="f0be8-240">-Tables \<String[]></span></span>                | <span data-ttu-id="f0be8-241">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="f0be8-241">The tables to generate entity types for.</span></span> <span data-ttu-id="f0be8-242">このパラメーターを省略すると、すべてのテーブルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-242">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="f0be8-243">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="f0be8-243">-DataAnnotations</span></span>                   | <span data-ttu-id="f0be8-244">属性を使用してモデルを構成します (可能な場合)。</span><span class="sxs-lookup"><span data-stu-id="f0be8-244">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="f0be8-245">このパラメーターを省略した場合は、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-245">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="f0be8-246">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="f0be8-246">-UseDatabaseNames</span></span>                  | <span data-ttu-id="f0be8-247">テーブル名と列名は、データベースに表示されるとおりに使用します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-247">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="f0be8-248">このパラメーターを省略した場合、データベース名は、名前のスタイルC#規則により厳密に準拠するように変更されます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-248">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="f0be8-249">-Force</span><span class="sxs-lookup"><span data-stu-id="f0be8-249">-Force</span></span>                             | <span data-ttu-id="f0be8-250">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="f0be8-250">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |

<span data-ttu-id="f0be8-251">例:</span><span class="sxs-lookup"><span data-stu-id="f0be8-251">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="f0be8-252">選択したテーブルのみをスキャフォールディングし、指定した名前の別のフォルダーにコンテキストを作成する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-252">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext
```

## <a name="script-migration"></a><span data-ttu-id="f0be8-253">Script-Migration</span><span class="sxs-lookup"><span data-stu-id="f0be8-253">Script-Migration</span></span>

<span data-ttu-id="f0be8-254">選択した移行のすべての変更を、選択した別の移行に適用する SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-254">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="f0be8-255">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="f0be8-255">Parameters:</span></span>

| <span data-ttu-id="f0be8-256">パラメーター</span><span class="sxs-lookup"><span data-stu-id="f0be8-256">Parameter</span></span>                | <span data-ttu-id="f0be8-257">説明</span><span class="sxs-lookup"><span data-stu-id="f0be8-257">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f0be8-258">*-From*\<String></span><span class="sxs-lookup"><span data-stu-id="f0be8-258">*-From* \<String></span></span>        | <span data-ttu-id="f0be8-259">開始の移行。</span><span class="sxs-lookup"><span data-stu-id="f0be8-259">The starting migration.</span></span> <span data-ttu-id="f0be8-260">移行は名前または ID で識別される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f0be8-260">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="f0be8-261">数値 0 は特殊なケースでは意味*最初の移行の前に*します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-261">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="f0be8-262">既定値は 0 です。</span><span class="sxs-lookup"><span data-stu-id="f0be8-262">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="f0be8-263">*-To* \<String></span><span class="sxs-lookup"><span data-stu-id="f0be8-263">*-To* \<String></span></span>          | <span data-ttu-id="f0be8-264">終了の移行。</span><span class="sxs-lookup"><span data-stu-id="f0be8-264">The ending migration.</span></span> <span data-ttu-id="f0be8-265">移行の最後の既定値します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-265">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="f0be8-266"><nobr>-Idempotent</nobr></span><span class="sxs-lookup"><span data-stu-id="f0be8-266"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="f0be8-267">すべての移行でのデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-267">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="f0be8-268">-Output\<String></span><span class="sxs-lookup"><span data-stu-id="f0be8-268">-Output \<String></span></span>        | <span data-ttu-id="f0be8-269">結果を書き込むファイル。</span><span class="sxs-lookup"><span data-stu-id="f0be8-269">The file to write the result to.</span></span> <span data-ttu-id="f0be8-270">たとえば、アプリのランタイム ファイルが作成されると、同じフォルダーに生成された名前でファイルを作成するこのパラメーターを省略すると、: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/* します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-270">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="f0be8-271">To、From、および Output パラメーターでは、タブの拡張がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f0be8-271">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="f0be8-272">次の例では、移行名を使用して、InitialCreate 移行用のスクリプトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-272">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="f0be8-273">次の例では、移行 ID を使用して、InitialCreate 移行後のすべての移行用のスクリプトを作成します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-273">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="f0be8-274">Update-Database</span><span class="sxs-lookup"><span data-stu-id="f0be8-274">Update-Database</span></span>

<span data-ttu-id="f0be8-275">最後に移行したデータベース、または指定した移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-275">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="f0be8-276">パラメーター</span><span class="sxs-lookup"><span data-stu-id="f0be8-276">Parameter</span></span>                           | <span data-ttu-id="f0be8-277">説明</span><span class="sxs-lookup"><span data-stu-id="f0be8-277">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f0be8-278"><nobr> *-Migration*\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="f0be8-278"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="f0be8-279">Target の移行。</span><span class="sxs-lookup"><span data-stu-id="f0be8-279">The target migration.</span></span> <span data-ttu-id="f0be8-280">移行は名前または ID で識別される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f0be8-280">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="f0be8-281">数値 0 は特殊なケースでは意味*最初の移行の前に*と、すべての移行を元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="f0be8-281">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="f0be8-282">移行が指定されていない場合のコマンドは、既定最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="f0be8-282">If no migration is specified, the command defaults to the last migration.</span></span> |

> [!TIP]
> <span data-ttu-id="f0be8-283">移行パラメーターでは、タブの拡張がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f0be8-283">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="f0be8-284">次の例では、すべての移行を元に戻します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-284">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="f0be8-285">次の例では、指定された移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-285">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="f0be8-286">最初のは移行名を使用し、2番目のは移行 ID を使用します。</span><span class="sxs-lookup"><span data-stu-id="f0be8-286">The first uses the migration name and the second uses the migration ID:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate
```

## <a name="additional-resources"></a><span data-ttu-id="f0be8-287">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="f0be8-287">Additional resources</span></span>

* [<span data-ttu-id="f0be8-288">移行</span><span class="sxs-lookup"><span data-stu-id="f0be8-288">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="f0be8-289">リバース エンジニアリング</span><span class="sxs-lookup"><span data-stu-id="f0be8-289">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
