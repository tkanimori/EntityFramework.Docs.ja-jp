---
title: EF Core ツールリファレンス (パッケージマネージャーコンソール)-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 0676475d46a8d21dee7bd10e25dd273a11e96ac3
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538402"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="a78e3-102">Entity Framework Core ツールリファレンス-Visual Studio のパッケージマネージャーコンソール</span><span class="sxs-lookup"><span data-stu-id="a78e3-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="a78e3-103">Entity Framework Core 用のパッケージマネージャーコンソール (PMC) ツールは、デザイン時の開発タスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="a78e3-104">たとえば、既存のデータベースに基づいて、[移行](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0)を作成し、移行を適用し、モデルのコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="a78e3-105">コマンドは、Visual Studio 内で[パッケージマネージャーコンソール](/nuget/tools/package-manager-console)を使用して実行されます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="a78e3-106">これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="a78e3-107">Visual Studio を使用していない場合は、代わりに[EF Core コマンドラインツール](dotnet.md)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a78e3-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="a78e3-108">CLI ツールはクロスプラットフォームで、コマンドプロンプト内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-108">The CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="a78e3-109">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="a78e3-109">Installing the tools</span></span>

<span data-ttu-id="a78e3-110">ツールのインストールと更新の手順は、ASP.NET Core 2.1 以降のバージョンまたはその他のプロジェクトの種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="a78e3-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="a78e3-111">ASP.NET Core バージョン2.1 以降</span><span class="sxs-lookup"><span data-stu-id="a78e3-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="a78e3-112">`Microsoft.EntityFrameworkCore.Tools`パッケージは[AspNetCore メタパッケージ](/aspnet/core/fundamentals/metapackage-app)に含まれているので、ツールは ASP.NET Core 2.1 以降のプロジェクトに自動的に含まれます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a78e3-113">そのため、ツールをインストールするために何もする必要はありませんが、次の操作を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="a78e3-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>

* <span data-ttu-id="a78e3-114">新しいプロジェクトでツールを使用する前に、パッケージを復元します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="a78e3-115">パッケージをインストールして、ツールを新しいバージョンに更新します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="a78e3-116">最新バージョンのツールを入手するには、次の手順も実行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a78e3-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="a78e3-117">*.Csproj*ファイルを編集し、最新バージョンの[Microsoft Entityframeworkcore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)パッケージを指定する行を追加します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="a78e3-118">たとえば、.csproj ファイルには、 `ItemGroup`次のようなを含めることができ*ます。*</span><span class="sxs-lookup"><span data-stu-id="a78e3-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

<span data-ttu-id="a78e3-119">次の例のようなメッセージが表示されたら、ツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="a78e3-120">EF Core ツールバージョン ' 2.1.1-30846 ' は、ランタイム ' 2.1.3-32065 ' より古いバージョンです。</span><span class="sxs-lookup"><span data-stu-id="a78e3-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="a78e3-121">最新の機能とバグ修正のためにツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="a78e3-122">ツールを更新するには:</span><span class="sxs-lookup"><span data-stu-id="a78e3-122">To update the tools:</span></span>

* <span data-ttu-id="a78e3-123">最新の .NET Core SDK をインストールします。</span><span class="sxs-lookup"><span data-stu-id="a78e3-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="a78e3-124">Visual Studio を最新バージョンに更新します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="a78e3-125">.Csproj ファイルを編集して、前に示したように、最新のツールパッケージへのパッケージ参照が含まれるようにし*ます*。</span><span class="sxs-lookup"><span data-stu-id="a78e3-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="a78e3-126">その他のバージョンとプロジェクトの種類</span><span class="sxs-lookup"><span data-stu-id="a78e3-126">Other versions and project types</span></span>

<span data-ttu-id="a78e3-127">パッケージマネージャーコンソールで次のコマンドを実行して、パッケージマネージャーコンソールツールをインストールし**ます。**</span><span class="sxs-lookup"><span data-stu-id="a78e3-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="a78e3-128">**パッケージマネージャーコンソール**で次のコマンドを実行して、ツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="a78e3-129">インストールの確認</span><span class="sxs-lookup"><span data-stu-id="a78e3-129">Verify the installation</span></span>

<span data-ttu-id="a78e3-130">次のコマンドを実行して、ツールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="a78e3-131">出力は次のようになります (使用しているツールのバージョンはわかりません)。</span><span class="sxs-lookup"><span data-stu-id="a78e3-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

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

## <a name="using-the-tools"></a><span data-ttu-id="a78e3-132">ツールの使用</span><span class="sxs-lookup"><span data-stu-id="a78e3-132">Using the tools</span></span>

<span data-ttu-id="a78e3-133">ツールを使用する前に:</span><span class="sxs-lookup"><span data-stu-id="a78e3-133">Before using the tools:</span></span>

* <span data-ttu-id="a78e3-134">ターゲットプロジェクトとスタートアッププロジェクトの違いを理解します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="a78e3-135">.NET Standard クラスライブラリでツールを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="a78e3-136">ASP.NET Core プロジェクトの場合は、環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="a78e3-137">ターゲットプロジェクトとスタートアッププロジェクト</span><span class="sxs-lookup"><span data-stu-id="a78e3-137">Target and startup project</span></span>

<span data-ttu-id="a78e3-138">これらのコマンドは、*プロジェクト*と*スタートアッププロジェクト*を参照します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="a78e3-139">*プロジェクト*は*ターゲットプロジェクト*とも呼ばれます。これは、コマンドによってファイルが追加または削除されるためです。</span><span class="sxs-lookup"><span data-stu-id="a78e3-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="a78e3-140">既定では、**パッケージマネージャーコンソール**で選択された**既定のプロジェクト**はターゲットプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="a78e3-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="a78e3-141"><nobr>`--project`</nobr>オプションを使用して、別のプロジェクトをターゲットプロジェクトとして指定できます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="a78e3-142">*スタートアッププロジェクト*は、ツールをビルドして実行するためのものです。</span><span class="sxs-lookup"><span data-stu-id="a78e3-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="a78e3-143">このツールでは、デザイン時にアプリケーションコードを実行して、プロジェクトに関する情報 (データベース接続文字列やモデルの構成など) を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a78e3-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="a78e3-144">既定では、**ソリューションエクスプローラー**の**スタートアッププロジェクト**はスタートアッププロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="a78e3-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="a78e3-145"><nobr>`--startup-project`</nobr>オプションを使用して、別のプロジェクトをスタートアッププロジェクトとして指定できます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="a78e3-146">多くの場合、スタートアッププロジェクトとターゲットプロジェクトは同じプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="a78e3-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="a78e3-147">個別のプロジェクトである一般的なシナリオは、次のような場合です。</span><span class="sxs-lookup"><span data-stu-id="a78e3-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="a78e3-148">EF Core コンテキストとエンティティクラスは、.NET Core クラスライブラリにあります。</span><span class="sxs-lookup"><span data-stu-id="a78e3-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="a78e3-149">.NET Core コンソールアプリまたは web アプリはクラスライブラリを参照します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="a78e3-150">また、[移行コードを EF Core コンテキストとは別のクラスライブラリに配置](xref:core/managing-schemas/migrations/projects)することもできます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="a78e3-151">その他のターゲットフレームワーク</span><span class="sxs-lookup"><span data-stu-id="a78e3-151">Other target frameworks</span></span>

<span data-ttu-id="a78e3-152">パッケージマネージャーコンソールツールは、.NET Core または .NET Framework プロジェクトで使用できます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="a78e3-153">.NET Standard クラスライブラリに EF Core モデルがあるアプリには、.NET Core または .NET Framework プロジェクトがない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a78e3-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="a78e3-154">たとえば、これは Xamarin とユニバーサル Windows プラットフォームアプリに当てはまります。</span><span class="sxs-lookup"><span data-stu-id="a78e3-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="a78e3-155">このような場合は、ツールのスタートアッププロジェクトとして機能することだけを目的とした .NET Core または .NET Framework コンソールアプリプロジェクトを作成できます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="a78e3-156">プロジェクトは、実際のコード&mdash;を持たないダミープロジェクトにすることができます。これは、ツールのターゲットを指定するためにのみ必要です。</span><span class="sxs-lookup"><span data-stu-id="a78e3-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="a78e3-157">ダミープロジェクトが必要な理由</span><span class="sxs-lookup"><span data-stu-id="a78e3-157">Why is a dummy project required?</span></span> <span data-ttu-id="a78e3-158">前述のように、ツールはデザイン時にアプリケーションコードを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a78e3-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="a78e3-159">そのためには、.NET Core または .NET Framework ランタイムを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a78e3-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="a78e3-160">EF Core モデルが .NET Core または .NET Framework を対象とするプロジェクト内にある場合、EF Core ツールはプロジェクトからランタイムを借用します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="a78e3-161">EF Core モデルが .NET Standard クラスライブラリ内にある場合は、これを行うことはできません。</span><span class="sxs-lookup"><span data-stu-id="a78e3-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="a78e3-162">.NET Standard は実際の .NET 実装ではありません。これは、.NET 実装がサポートする必要のある一連の Api の仕様です。</span><span class="sxs-lookup"><span data-stu-id="a78e3-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="a78e3-163">したがって、EF Core ツールでアプリケーションコードを実行するために .NET Standard は十分ではありません。</span><span class="sxs-lookup"><span data-stu-id="a78e3-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="a78e3-164">スタートアッププロジェクトとして使用するために作成するダミープロジェクトは、ツールが .NET Standard クラスライブラリを読み込むことができる具象ターゲットプラットフォームを提供します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="a78e3-165">ASP.NET Core 環境</span><span class="sxs-lookup"><span data-stu-id="a78e3-165">ASP.NET Core environment</span></span>

<span data-ttu-id="a78e3-166">ASP.NET Core プロジェクトの環境を指定するには、コマンドを実行する前に**env: ASPNETCORE_ENVIRONMENT**を設定します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="a78e3-167">共通パラメーター</span><span class="sxs-lookup"><span data-stu-id="a78e3-167">Common parameters</span></span>

<span data-ttu-id="a78e3-168">次の表に、すべての EF Core コマンドに共通のパラメーターを示します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="a78e3-169">パラメーター</span><span class="sxs-lookup"><span data-stu-id="a78e3-169">Parameter</span></span>                 | <span data-ttu-id="a78e3-170">説明</span><span class="sxs-lookup"><span data-stu-id="a78e3-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a78e3-171">-コンテキスト\<文字列></span><span class="sxs-lookup"><span data-stu-id="a78e3-171">-Context \<String></span></span>        | <span data-ttu-id="a78e3-172">使用する `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="a78e3-172">The `DbContext` class to use.</span></span> <span data-ttu-id="a78e3-173">クラス名のみ、または名前空間で完全修飾されています。</span><span class="sxs-lookup"><span data-stu-id="a78e3-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="a78e3-174">このパラメーターを省略した場合、EF Core によってコンテキストクラスが検索されます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="a78e3-175">複数のコンテキストクラスがある場合は、このパラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="a78e3-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="a78e3-176">-プロジェクト\<文字列></span><span class="sxs-lookup"><span data-stu-id="a78e3-176">-Project \<String></span></span>        | <span data-ttu-id="a78e3-177">ターゲットプロジェクト。</span><span class="sxs-lookup"><span data-stu-id="a78e3-177">The target project.</span></span> <span data-ttu-id="a78e3-178">このパラメーターを省略すると、**パッケージマネージャーコンソール**の**既定のプロジェクト**がターゲットプロジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="a78e3-179"><nobr>-Startupproject</nobr> \<String></span><span class="sxs-lookup"><span data-stu-id="a78e3-179"><nobr>-StartupProject</nobr> \<String></span></span> | <span data-ttu-id="a78e3-180">スタートアッププロジェクト。</span><span class="sxs-lookup"><span data-stu-id="a78e3-180">The startup project.</span></span> <span data-ttu-id="a78e3-181">このパラメーターを省略すると、**ソリューションのプロパティ**の**スタートアッププロジェクト**がターゲットプロジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="a78e3-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="a78e3-182">-Verbose</span></span>                  | <span data-ttu-id="a78e3-183">詳細出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="a78e3-184">コマンドに関するヘルプ情報を表示するには、 `Get-Help` PowerShell のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="a78e3-185">Context、Project、および StartupProject の各パラメーターでは、タブの展開がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a78e3-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="a78e3-186">移行の追加</span><span class="sxs-lookup"><span data-stu-id="a78e3-186">Add-Migration</span></span>

<span data-ttu-id="a78e3-187">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-187">Adds a new migration.</span></span>

<span data-ttu-id="a78e3-188">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="a78e3-188">Parameters:</span></span>

| <span data-ttu-id="a78e3-189">パラメーター</span><span class="sxs-lookup"><span data-stu-id="a78e3-189">Parameter</span></span>                         | <span data-ttu-id="a78e3-190">説明</span><span class="sxs-lookup"><span data-stu-id="a78e3-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a78e3-191"><nobr>-Name \<文字列><nobr></span><span class="sxs-lookup"><span data-stu-id="a78e3-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="a78e3-192">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="a78e3-192">The name of the migration.</span></span> <span data-ttu-id="a78e3-193">これは位置指定パラメーターであり、必須です。</span><span class="sxs-lookup"><span data-stu-id="a78e3-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="a78e3-194"><nobr>-OutputDir \<文字列></nobr></span><span class="sxs-lookup"><span data-stu-id="a78e3-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="a78e3-195">ファイルの出力に使用するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="a78e3-195">The directory use to output the files.</span></span> <span data-ttu-id="a78e3-196">パスは、ターゲットプロジェクトディレクトリに対する相対パスです。</span><span class="sxs-lookup"><span data-stu-id="a78e3-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="a78e3-197">既定値は "移行" です。</span><span class="sxs-lookup"><span data-stu-id="a78e3-197">Defaults to "Migrations".</span></span> |
| <span data-ttu-id="a78e3-198"><nobr>-名前\<空間文字列></nobr></span><span class="sxs-lookup"><span data-stu-id="a78e3-198"><nobr>-Namespace \<String></nobr></span></span> | <span data-ttu-id="a78e3-199">生成されたクラスに使用する名前空間。</span><span class="sxs-lookup"><span data-stu-id="a78e3-199">The namespace to use for the generated classes.</span></span> <span data-ttu-id="a78e3-200">既定値は、出力ディレクトリから生成されます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-200">Defaults to generated from the output directory.</span></span> |

## <a name="drop-database"></a><span data-ttu-id="a78e3-201">Drop Database</span><span class="sxs-lookup"><span data-stu-id="a78e3-201">Drop-Database</span></span>

<span data-ttu-id="a78e3-202">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-202">Drops the database.</span></span>

<span data-ttu-id="a78e3-203">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="a78e3-203">Parameters:</span></span>

| <span data-ttu-id="a78e3-204">パラメーター</span><span class="sxs-lookup"><span data-stu-id="a78e3-204">Parameter</span></span> | <span data-ttu-id="a78e3-205">説明</span><span class="sxs-lookup"><span data-stu-id="a78e3-205">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="a78e3-206">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="a78e3-206">-WhatIf</span></span>   | <span data-ttu-id="a78e3-207">削除するデータベースを表示しますが、削除はしません。</span><span class="sxs-lookup"><span data-stu-id="a78e3-207">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="a78e3-208">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="a78e3-208">Get-DbContext</span></span>

<span data-ttu-id="a78e3-209">`DbContext`型に関する情報を取得します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-209">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="a78e3-210">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="a78e3-210">Remove-Migration</span></span>

<span data-ttu-id="a78e3-211">最後の移行を削除します (移行のために実行されたコード変更をロールバックします)。</span><span class="sxs-lookup"><span data-stu-id="a78e3-211">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="a78e3-212">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="a78e3-212">Parameters:</span></span>

| <span data-ttu-id="a78e3-213">パラメーター</span><span class="sxs-lookup"><span data-stu-id="a78e3-213">Parameter</span></span> | <span data-ttu-id="a78e3-214">説明</span><span class="sxs-lookup"><span data-stu-id="a78e3-214">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="a78e3-215">-Force</span><span class="sxs-lookup"><span data-stu-id="a78e3-215">-Force</span></span>    | <span data-ttu-id="a78e3-216">移行を元に戻します (データベースに適用された変更をロールバックします)。</span><span class="sxs-lookup"><span data-stu-id="a78e3-216">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="a78e3-217">スキャフォールディング-DbContext</span><span class="sxs-lookup"><span data-stu-id="a78e3-217">Scaffold-DbContext</span></span>

<span data-ttu-id="a78e3-218">データベースの`DbContext`およびエンティティ型のコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-218">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="a78e3-219">でエンティティ型`Scaffold-DbContext`を生成するには、データベーステーブルに主キーが必要です。</span><span class="sxs-lookup"><span data-stu-id="a78e3-219">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="a78e3-220">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="a78e3-220">Parameters:</span></span>

| <span data-ttu-id="a78e3-221">パラメーター</span><span class="sxs-lookup"><span data-stu-id="a78e3-221">Parameter</span></span>                          | <span data-ttu-id="a78e3-222">説明</span><span class="sxs-lookup"><span data-stu-id="a78e3-222">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a78e3-223"><nobr>-接続\<文字列></nobr></span><span class="sxs-lookup"><span data-stu-id="a78e3-223"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="a78e3-224">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="a78e3-224">The connection string to the database.</span></span> <span data-ttu-id="a78e3-225">ASP.NET Core 2.x プロジェクトの場合、値には*名前 =\<接続文字列>の名前*を指定できます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-225">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="a78e3-226">その場合、プロジェクト用に設定されている構成ソースから名前を取得します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-226">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="a78e3-227">これは位置指定パラメーターであり、必須です。</span><span class="sxs-lookup"><span data-stu-id="a78e3-227">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="a78e3-228"><nobr>-プロバイダー \<文字列></nobr></span><span class="sxs-lookup"><span data-stu-id="a78e3-228"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="a78e3-229">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="a78e3-229">The provider to use.</span></span> <span data-ttu-id="a78e3-230">通常、これは NuGet パッケージの名前です (例: `Microsoft.EntityFrameworkCore.SqlServer`)。</span><span class="sxs-lookup"><span data-stu-id="a78e3-230">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="a78e3-231">これは位置指定パラメーターであり、必須です。</span><span class="sxs-lookup"><span data-stu-id="a78e3-231">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="a78e3-232">-OutputDir \<文字列></span><span class="sxs-lookup"><span data-stu-id="a78e3-232">-OutputDir \<String></span></span>               | <span data-ttu-id="a78e3-233">ファイルを格納するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="a78e3-233">The directory to put files in.</span></span> <span data-ttu-id="a78e3-234">パスは、プロジェクトディレクトリに対する相対パスです。</span><span class="sxs-lookup"><span data-stu-id="a78e3-234">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="a78e3-235">-ContextDir \<文字列></span><span class="sxs-lookup"><span data-stu-id="a78e3-235">-ContextDir \<String></span></span>              | <span data-ttu-id="a78e3-236">`DbContext`ファイルを格納するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="a78e3-236">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="a78e3-237">パスは、プロジェクトディレクトリに対する相対パスです。</span><span class="sxs-lookup"><span data-stu-id="a78e3-237">Paths are relative to the project directory.</span></span>                                                                                                                                                               |
| <span data-ttu-id="a78e3-238">-名前\<空間文字列></span><span class="sxs-lookup"><span data-stu-id="a78e3-238">-Namespace \<String></span></span>               | <span data-ttu-id="a78e3-239">生成されたすべてのクラスに使用する名前空間。</span><span class="sxs-lookup"><span data-stu-id="a78e3-239">The namespace to use for all generated classes.</span></span> <span data-ttu-id="a78e3-240">既定値は、ルート名前空間と出力ディレクトリから生成されます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-240">Defaults to generated from the root namespace and the output directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="a78e3-241">-ContextNamespace \<文字列></span><span class="sxs-lookup"><span data-stu-id="a78e3-241">-ContextNamespace \<String></span></span>        | <span data-ttu-id="a78e3-242">生成さ`DbContext`れたクラスに使用する名前空間。</span><span class="sxs-lookup"><span data-stu-id="a78e3-242">The namespace to use for the generated `DbContext` class.</span></span> <span data-ttu-id="a78e3-243">注: は`-Namespace`オーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-243">Note: overrides `-Namespace`.</span></span>                                                                                                                                                                              |
| <span data-ttu-id="a78e3-244">-コンテキスト\<文字列></span><span class="sxs-lookup"><span data-stu-id="a78e3-244">-Context \<String></span></span>                 | <span data-ttu-id="a78e3-245">生成する`DbContext`クラスの名前。</span><span class="sxs-lookup"><span data-stu-id="a78e3-245">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="a78e3-246">-スキーマ\<文字列 [] ></span><span class="sxs-lookup"><span data-stu-id="a78e3-246">-Schemas \<String[]></span></span>               | <span data-ttu-id="a78e3-247">エンティティ型を生成するテーブルのスキーマ。</span><span class="sxs-lookup"><span data-stu-id="a78e3-247">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="a78e3-248">このパラメーターを省略すると、すべてのスキーマが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-248">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="a78e3-249">-Tables \<String [] ></span><span class="sxs-lookup"><span data-stu-id="a78e3-249">-Tables \<String[]></span></span>                | <span data-ttu-id="a78e3-250">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="a78e3-250">The tables to generate entity types for.</span></span> <span data-ttu-id="a78e3-251">このパラメーターを省略すると、すべてのテーブルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-251">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="a78e3-252">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="a78e3-252">-DataAnnotations</span></span>                   | <span data-ttu-id="a78e3-253">属性を使用してモデルを構成します (可能な場合)。</span><span class="sxs-lookup"><span data-stu-id="a78e3-253">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="a78e3-254">このパラメーターを省略した場合は、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-254">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="a78e3-255">-UseDatabaseNames 場合</span><span class="sxs-lookup"><span data-stu-id="a78e3-255">-UseDatabaseNames</span></span>                  | <span data-ttu-id="a78e3-256">テーブル名と列名は、データベースに表示されるとおりに使用します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-256">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="a78e3-257">このパラメーターを省略した場合、データベース名は、C# の名前のスタイル規則により厳密に準拠するように変更されます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-257">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="a78e3-258">-Force</span><span class="sxs-lookup"><span data-stu-id="a78e3-258">-Force</span></span>                             | <span data-ttu-id="a78e3-259">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="a78e3-259">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |

<span data-ttu-id="a78e3-260">例:</span><span class="sxs-lookup"><span data-stu-id="a78e3-260">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="a78e3-261">選択されたテーブルのみをスキャフォールディングし、指定された名前と名前空間を持つ別のフォルダーにコンテキストを作成する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-261">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name and namespace:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext -ContextNamespace New.Namespace
```

## <a name="script-migration"></a><span data-ttu-id="a78e3-262">スクリプト-移行</span><span class="sxs-lookup"><span data-stu-id="a78e3-262">Script-Migration</span></span>

<span data-ttu-id="a78e3-263">選択した移行のすべての変更を、選択した別の移行に適用する SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-263">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="a78e3-264">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="a78e3-264">Parameters:</span></span>

| <span data-ttu-id="a78e3-265">パラメーター</span><span class="sxs-lookup"><span data-stu-id="a78e3-265">Parameter</span></span>                | <span data-ttu-id="a78e3-266">説明</span><span class="sxs-lookup"><span data-stu-id="a78e3-266">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a78e3-267">*-* \<文字列> から</span><span class="sxs-lookup"><span data-stu-id="a78e3-267">*-From* \<String></span></span>        | <span data-ttu-id="a78e3-268">移行を開始しています。</span><span class="sxs-lookup"><span data-stu-id="a78e3-268">The starting migration.</span></span> <span data-ttu-id="a78e3-269">移行は、名前または ID で識別できます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-269">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="a78e3-270">数値0は、*最初の移行の前に*特別なケースです。</span><span class="sxs-lookup"><span data-stu-id="a78e3-270">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="a78e3-271">既定値は 0 です。</span><span class="sxs-lookup"><span data-stu-id="a78e3-271">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="a78e3-272">*-To* \<文字列></span><span class="sxs-lookup"><span data-stu-id="a78e3-272">*-To* \<String></span></span>          | <span data-ttu-id="a78e3-273">移行を終了しています。</span><span class="sxs-lookup"><span data-stu-id="a78e3-273">The ending migration.</span></span> <span data-ttu-id="a78e3-274">既定では、最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="a78e3-274">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="a78e3-275"><nobr>-べき等</nobr></span><span class="sxs-lookup"><span data-stu-id="a78e3-275"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="a78e3-276">任意の移行時にデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-276">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="a78e3-277">-出力\<文字列></span><span class="sxs-lookup"><span data-stu-id="a78e3-277">-Output \<String></span></span>        | <span data-ttu-id="a78e3-278">結果の書き込み先のファイル。</span><span class="sxs-lookup"><span data-stu-id="a78e3-278">The file to write the result to.</span></span> <span data-ttu-id="a78e3-279">このパラメーターを省略すると、 */obj/Debug/netcoreapp2.1/ghbkztfz.sql/* のように、アプリのランタイムファイルが作成されたときと同じフォルダーに生成された名前でファイルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-279">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="a78e3-280">To、From、および Output パラメーターでは、タブの拡張がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a78e3-280">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="a78e3-281">次の例では、移行名を使用して、InitialCreate 移行用のスクリプトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-281">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="a78e3-282">次の例では、移行 ID を使用して、InitialCreate 移行後のすべての移行用のスクリプトを作成します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-282">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="a78e3-283">データベースの更新</span><span class="sxs-lookup"><span data-stu-id="a78e3-283">Update-Database</span></span>

<span data-ttu-id="a78e3-284">最後に移行したデータベース、または指定した移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-284">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="a78e3-285">パラメーター</span><span class="sxs-lookup"><span data-stu-id="a78e3-285">Parameter</span></span>                           | <span data-ttu-id="a78e3-286">説明</span><span class="sxs-lookup"><span data-stu-id="a78e3-286">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a78e3-287"><nobr>*-移行* \<文字列></nobr></span><span class="sxs-lookup"><span data-stu-id="a78e3-287"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="a78e3-288">ターゲットの移行。</span><span class="sxs-lookup"><span data-stu-id="a78e3-288">The target migration.</span></span> <span data-ttu-id="a78e3-289">移行は、名前または ID で識別できます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-289">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="a78e3-290">数値0は、*最初の移行の前に*特別なケースであり、すべての移行が元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="a78e3-290">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="a78e3-291">移行が指定されていない場合、コマンドは既定で最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="a78e3-291">If no migration is specified, the command defaults to the last migration.</span></span> |
| <span data-ttu-id="a78e3-292"><nobr>-接続\<文字列></nobr></span><span class="sxs-lookup"><span data-stu-id="a78e3-292"><nobr>-Connection \<String></nobr></span></span>  | <span data-ttu-id="a78e3-293">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="a78e3-293">The connection string to the database.</span></span> <span data-ttu-id="a78e3-294">既定値は、または`AddDbContext` `OnConfiguring`で指定されたものです。</span><span class="sxs-lookup"><span data-stu-id="a78e3-294">Defaults to the one specified in `AddDbContext` or `OnConfiguring`.</span></span> |

> [!TIP]
> <span data-ttu-id="a78e3-295">移行パラメーターでは、タブの拡張がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a78e3-295">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="a78e3-296">次の例では、すべての移行を元に戻します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-296">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="a78e3-297">次の例では、指定された移行にデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-297">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="a78e3-298">最初のは移行名を使用し、2つ目のは移行 ID と指定された接続を使用します。</span><span class="sxs-lookup"><span data-stu-id="a78e3-298">The first uses the migration name and the second uses the migration ID and a specified connection:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate -Connection your_connection_string
```

## <a name="additional-resources"></a><span data-ttu-id="a78e3-299">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="a78e3-299">Additional resources</span></span>

* [<span data-ttu-id="a78e3-300">移行</span><span class="sxs-lookup"><span data-stu-id="a78e3-300">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="a78e3-301">リバースエンジニアリング</span><span class="sxs-lookup"><span data-stu-id="a78e3-301">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
