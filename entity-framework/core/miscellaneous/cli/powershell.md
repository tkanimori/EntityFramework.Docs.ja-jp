---
title: EF Core ツール リファレンス (パッケージ マネージャー コンソール) - EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: cb05e3fb66adf96f8a6778711a76520d0be24c71
ms.sourcegitcommit: 645785187ae23ddf7d7b0642c7a4da5ffb0c7f30
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419771"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="42c38-102">Entity Framework Core ツールのリファレンス - Visual Studio でパッケージ マネージャー コンソール</span><span class="sxs-lookup"><span data-stu-id="42c38-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="42c38-103">Entity Framework Core のパッケージ マネージャー コンソール (PMC) ツールでは、デザイン時開発タスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="42c38-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="42c38-104">たとえば、作成[移行](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations)移行を適用し、既存のデータベースに基づくモデルのコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="42c38-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="42c38-105">コマンドは、Visual Studio を使用して内部で実行、[パッケージ マネージャー コンソール](/nuget/tools/package-manager-console)します。</span><span class="sxs-lookup"><span data-stu-id="42c38-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="42c38-106">これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。</span><span class="sxs-lookup"><span data-stu-id="42c38-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="42c38-107">Visual Studio を使用していないかどうか、お勧め、 [EF Core コマンド ライン ツール](dotnet.md)代わりにします。</span><span class="sxs-lookup"><span data-stu-id="42c38-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="42c38-108">CLI ツールは、クロス プラットフォームと、コマンド プロンプト内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="42c38-108">The CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="42c38-109">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="42c38-109">Installing the tools</span></span>

<span data-ttu-id="42c38-110">インストールして、ツールを更新する手順は、ASP.NET Core 2.1 以降と以前のバージョンまたはその他のプロジェクトの種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="42c38-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="42c38-111">ASP.NET Core 2.1 以降のバージョン</span><span class="sxs-lookup"><span data-stu-id="42c38-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="42c38-112">ユーザーがためにで、ツールは、ASP.NET Core 2.1 以降のプロジェクトに自動的に含め、`Microsoft.EntityFrameworkCore.Tools`にパッケージが含まれる、 [Microsoft.AspNetCore.App メタパッケージ](/aspnet/core/fundamentals/metapackage-app)します。</span><span class="sxs-lookup"><span data-stu-id="42c38-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="42c38-113">そのため、ツールをインストールすることが何もする必要はありませんを行うこと。</span><span class="sxs-lookup"><span data-stu-id="42c38-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>
* <span data-ttu-id="42c38-114">新しいプロジェクトのツールを使用する前にパッケージを復元します。</span><span class="sxs-lookup"><span data-stu-id="42c38-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="42c38-115">新しいバージョンにツールを更新するパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="42c38-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="42c38-116">ツールの最新バージョンを取得していることを確認するには、次の手順を実行することお勧めします。</span><span class="sxs-lookup"><span data-stu-id="42c38-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="42c38-117">編集、 *.csproj*ファイルを開き、最新バージョンを指定する行を追加、 [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="42c38-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="42c38-118">たとえば、 *.csproj*ファイルを含めることができます、`ItemGroup`次のようにします。</span><span class="sxs-lookup"><span data-stu-id="42c38-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

<span data-ttu-id="42c38-119">次の例のようなメッセージが表示された場合は、ツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="42c38-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="42c38-120">EF Core ツールのバージョン '2.1.1-rtm-30846' は、'2.1.3-rtm-32065' ランタイムのより古いです。</span><span class="sxs-lookup"><span data-stu-id="42c38-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="42c38-121">最新の機能とバグ修正するためのツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="42c38-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="42c38-122">ツールの更新。</span><span class="sxs-lookup"><span data-stu-id="42c38-122">To update the tools:</span></span>
* <span data-ttu-id="42c38-123">最新の .NET Core SDK をインストールします。</span><span class="sxs-lookup"><span data-stu-id="42c38-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="42c38-124">Visual Studio を最新バージョンに更新します。</span><span class="sxs-lookup"><span data-stu-id="42c38-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="42c38-125">編集、 *.csproj*ファイルの最新のツール パッケージにパッケージ参照が含まれるように前述のようにします。</span><span class="sxs-lookup"><span data-stu-id="42c38-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="42c38-126">他のバージョンおよびプロジェクトの種類</span><span class="sxs-lookup"><span data-stu-id="42c38-126">Other versions and project types</span></span>

<span data-ttu-id="42c38-127">次のコマンドを実行して、パッケージ マネージャー コンソールのツールをインストール**パッケージ マネージャー コンソール**:</span><span class="sxs-lookup"><span data-stu-id="42c38-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="42c38-128">次のコマンドを実行してツールを更新する**パッケージ マネージャー コンソール**します。</span><span class="sxs-lookup"><span data-stu-id="42c38-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="42c38-129">インストールを確認します。</span><span class="sxs-lookup"><span data-stu-id="42c38-129">Verify the installation</span></span>

<span data-ttu-id="42c38-130">このコマンドを実行して、ツールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="42c38-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="42c38-131">出力は、(、しないことがわかるツールを使用しているのバージョン) を次のようになります。</span><span class="sxs-lookup"><span data-stu-id="42c38-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

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

## <a name="using-the-tools"></a><span data-ttu-id="42c38-132">ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="42c38-132">Using the tools</span></span>

<span data-ttu-id="42c38-133">前に、ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="42c38-133">Before using the tools:</span></span>
* <span data-ttu-id="42c38-134">ターゲットとスタートアップ プロジェクトの違いを理解します。</span><span class="sxs-lookup"><span data-stu-id="42c38-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="42c38-135">.NET Standard クラス ライブラリで、ツールを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="42c38-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="42c38-136">ASP.NET Core プロジェクトでは、環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="42c38-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="42c38-137">ターゲットとスタートアップ プロジェクト</span><span class="sxs-lookup"><span data-stu-id="42c38-137">Target and startup project</span></span>

<span data-ttu-id="42c38-138">コマンドを参照してください、*プロジェクト*と*スタートアップ プロジェクト*します。</span><span class="sxs-lookup"><span data-stu-id="42c38-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="42c38-139">*プロジェクト*とも呼ばれますが、*ターゲット プロジェクト*コマンドが追加または、ファイルを削除であるためです。</span><span class="sxs-lookup"><span data-stu-id="42c38-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="42c38-140">既定では、**既定のプロジェクト**で選択した**パッケージ マネージャー コンソール**ターゲット プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="42c38-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="42c38-141">ターゲット プロジェクトとして使用して、別のプロジェクトを指定することができます、 <nobr> `--project` </nobr>オプション。</span><span class="sxs-lookup"><span data-stu-id="42c38-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="42c38-142">*スタートアップ プロジェクト*ツールをビルドして実行されます。</span><span class="sxs-lookup"><span data-stu-id="42c38-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="42c38-143">ツールは、データベース接続文字列と、モデルの構成など、プロジェクトに関する情報を取得するデザイン時にアプリケーション コードを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="42c38-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="42c38-144">既定では、**スタートアップ プロジェクト**で**ソリューション エクスプ ローラー**はスタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="42c38-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="42c38-145">使用して、別のプロジェクトをスタートアップ プロジェクトとして指定できます、 <nobr> `--startup-project` </nobr>オプション。</span><span class="sxs-lookup"><span data-stu-id="42c38-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="42c38-146">スタートアップ プロジェクトとターゲット プロジェクトは、同じプロジェクトでは多くの場合です。</span><span class="sxs-lookup"><span data-stu-id="42c38-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="42c38-147">個別のプロジェクトが、一般的なシナリオは。</span><span class="sxs-lookup"><span data-stu-id="42c38-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="42c38-148">EF Core コンテキストとエンティティ クラスは、.NET Core クラス ライブラリでは。</span><span class="sxs-lookup"><span data-stu-id="42c38-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="42c38-149">.NET Core コンソール アプリまたは web アプリは、クラス ライブラリを参照します。</span><span class="sxs-lookup"><span data-stu-id="42c38-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="42c38-150">することも[EF Core コンテキストから別のクラス ライブラリにコードを移行配置](xref:core/managing-schemas/migrations/projects)します。</span><span class="sxs-lookup"><span data-stu-id="42c38-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="42c38-151">その他のターゲット フレームワーク</span><span class="sxs-lookup"><span data-stu-id="42c38-151">Other target frameworks</span></span>

<span data-ttu-id="42c38-152">パッケージ マネージャー コンソール ツールは、.NET Core または .NET Framework プロジェクトで動作します。</span><span class="sxs-lookup"><span data-stu-id="42c38-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="42c38-153">.NET Standard クラス ライブラリで、EF Core モデルを必要とするアプリケーションは、.NET Core または .NET Framework プロジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="42c38-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="42c38-154">たとえば、これは、Xamarin とユニバーサル Windows プラットフォーム アプリの場合は true。</span><span class="sxs-lookup"><span data-stu-id="42c38-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="42c38-155">このような場合は、のみを目的が、ツールのスタートアップ プロジェクトとして機能するには、.NET Core または .NET Framework コンソール アプリケーション プロジェクトを作成できます。</span><span class="sxs-lookup"><span data-stu-id="42c38-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="42c38-156">プロジェクトは、実際のコードなしでダミー プロジェクト&mdash;ターゲット ツールを提供する必要があるだけです。</span><span class="sxs-lookup"><span data-stu-id="42c38-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="42c38-157">必要なダミー プロジェクトはなぜですか。</span><span class="sxs-lookup"><span data-stu-id="42c38-157">Why is a dummy project required?</span></span> <span data-ttu-id="42c38-158">前述のように、ツールは、デザイン時にアプリケーション コードを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="42c38-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="42c38-159">そのためには、.NET Core または .NET Framework のランタイムを使用して、必要があります。</span><span class="sxs-lookup"><span data-stu-id="42c38-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="42c38-160">EF Core モデルは、.NET Core または .NET Framework を対象とするプロジェクトでは、EF Core ツールは、プロジェクトからランタイムを借用します。</span><span class="sxs-lookup"><span data-stu-id="42c38-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="42c38-161">EF Core モデルが .NET Standard クラス ライブラリの場合、インストールできません。</span><span class="sxs-lookup"><span data-stu-id="42c38-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="42c38-162">.NET Standard は、実際の .NET 実装ではありません。一連の .NET 実装をサポートする必要がある Api の仕様です。</span><span class="sxs-lookup"><span data-stu-id="42c38-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="42c38-163">したがって .NET Standard はアプリケーション コードを実行する EF Core ツールのための十分です。</span><span class="sxs-lookup"><span data-stu-id="42c38-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="42c38-164">スタートアップ プロジェクトとして使用するように作成するダミーのプロジェクトでは、ツールが、.NET Standard クラス ライブラリを読み込むことができます、具体的なターゲット プラットフォームを提供します。</span><span class="sxs-lookup"><span data-stu-id="42c38-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="42c38-165">ASP.NET Core 環境</span><span class="sxs-lookup"><span data-stu-id="42c38-165">ASP.NET Core environment</span></span>

<span data-ttu-id="42c38-166">ASP.NET Core プロジェクト用の環境を指定するには、次のように設定します。 **env:ASPNETCORE_ENVIRONMENT**コマンドを実行する前にします。</span><span class="sxs-lookup"><span data-stu-id="42c38-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="42c38-167">共通パラメーター</span><span class="sxs-lookup"><span data-stu-id="42c38-167">Common parameters</span></span>

<span data-ttu-id="42c38-168">次の表は、EF Core のコマンドのすべてに共通するパラメーターを示しています。</span><span class="sxs-lookup"><span data-stu-id="42c38-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="42c38-169">パラメーター</span><span class="sxs-lookup"><span data-stu-id="42c38-169">Parameter</span></span>                 | <span data-ttu-id="42c38-170">説明</span><span class="sxs-lookup"><span data-stu-id="42c38-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="42c38-171">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="42c38-171">-Context \<String></span></span>        | <span data-ttu-id="42c38-172">使用する `DbContext` クラス。</span><span class="sxs-lookup"><span data-stu-id="42c38-172">The `DbContext` class to use.</span></span> <span data-ttu-id="42c38-173">クラス名のみ、または名前空間を持つ完全修飾します。</span><span class="sxs-lookup"><span data-stu-id="42c38-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="42c38-174">このパラメーターを省略した場合、EF Core は、コンテキスト クラスを検索します。</span><span class="sxs-lookup"><span data-stu-id="42c38-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="42c38-175">複数のコンテキスト クラスがある場合は、このパラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="42c38-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="42c38-176">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="42c38-176">-Project \<String></span></span>        | <span data-ttu-id="42c38-177">ターゲットのプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="42c38-177">The target project.</span></span> <span data-ttu-id="42c38-178">このパラメーターを省略した場合、**既定のプロジェクト**の**パッケージ マネージャー コンソール**がターゲット プロジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="42c38-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="42c38-179">-StartupProject \<String></span><span class="sxs-lookup"><span data-stu-id="42c38-179">-StartupProject \<String></span></span> | <span data-ttu-id="42c38-180">スタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="42c38-180">The startup project.</span></span> <span data-ttu-id="42c38-181">このパラメーターを省略した場合、**スタートアップ プロジェクト**で**ソリューションのプロパティ**ターゲット プロジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="42c38-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="42c38-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="42c38-182">-Verbose</span></span>                  | <span data-ttu-id="42c38-183">詳細な出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="42c38-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="42c38-184">コマンドに関するヘルプ情報を表示するには、PowerShell を使用して`Get-Help`コマンド。</span><span class="sxs-lookup"><span data-stu-id="42c38-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="42c38-185">コンテキスト、プロジェクト、およびスタートアップ プロジェクトのパラメーターでは、タブ拡張をサポートします。</span><span class="sxs-lookup"><span data-stu-id="42c38-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="42c38-186">Add-Migration</span><span class="sxs-lookup"><span data-stu-id="42c38-186">Add-Migration</span></span>

<span data-ttu-id="42c38-187">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="42c38-187">Adds a new migration.</span></span>

<span data-ttu-id="42c38-188">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="42c38-188">Parameters:</span></span>

| <span data-ttu-id="42c38-189">パラメーター</span><span class="sxs-lookup"><span data-stu-id="42c38-189">Parameter</span></span>                         | <span data-ttu-id="42c38-190">説明</span><span class="sxs-lookup"><span data-stu-id="42c38-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="42c38-191"><nobr>-Name\<String><nobr></span><span class="sxs-lookup"><span data-stu-id="42c38-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="42c38-192">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="42c38-192">The name of the migration.</span></span> <span data-ttu-id="42c38-193">位置指定パラメーターは、これが必要です。</span><span class="sxs-lookup"><span data-stu-id="42c38-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="42c38-194"><nobr>-OutputDir\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="42c38-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="42c38-195">使用するディレクトリ (およびサブ名前空間)。</span><span class="sxs-lookup"><span data-stu-id="42c38-195">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="42c38-196">パスでは、ターゲット プロジェクトのディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="42c38-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="42c38-197">既定値は「移行」です。</span><span class="sxs-lookup"><span data-stu-id="42c38-197">Defaults to "Migrations".</span></span> |

## <a name="drop-database"></a><span data-ttu-id="42c38-198">Drop-Database</span><span class="sxs-lookup"><span data-stu-id="42c38-198">Drop-Database</span></span>

<span data-ttu-id="42c38-199">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="42c38-199">Drops the database.</span></span>

<span data-ttu-id="42c38-200">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="42c38-200">Parameters:</span></span>

| <span data-ttu-id="42c38-201">パラメーター</span><span class="sxs-lookup"><span data-stu-id="42c38-201">Parameter</span></span> | <span data-ttu-id="42c38-202">説明</span><span class="sxs-lookup"><span data-stu-id="42c38-202">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="42c38-203">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="42c38-203">-WhatIf</span></span>   | <span data-ttu-id="42c38-204">表示するデータベースが削除されます。 は削除しないでください。</span><span class="sxs-lookup"><span data-stu-id="42c38-204">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="42c38-205">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="42c38-205">Get-DbContext</span></span>

<span data-ttu-id="42c38-206">に関する情報を取得します、`DbContext`型。</span><span class="sxs-lookup"><span data-stu-id="42c38-206">Gets information about a `DbContext` type.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="42c38-207">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="42c38-207">Remove-Migration</span></span>

<span data-ttu-id="42c38-208">最後の移行 (ロールバックの移行の実行されたコードの変更) を削除します。</span><span class="sxs-lookup"><span data-stu-id="42c38-208">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="42c38-209">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="42c38-209">Parameters:</span></span>

| <span data-ttu-id="42c38-210">パラメーター</span><span class="sxs-lookup"><span data-stu-id="42c38-210">Parameter</span></span> | <span data-ttu-id="42c38-211">説明</span><span class="sxs-lookup"><span data-stu-id="42c38-211">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="42c38-212">-Force</span><span class="sxs-lookup"><span data-stu-id="42c38-212">-Force</span></span>    | <span data-ttu-id="42c38-213">移行を元に戻す (ロールバックをデータベースに適用された変更)。</span><span class="sxs-lookup"><span data-stu-id="42c38-213">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="42c38-214">Scaffold-dbcontext</span><span class="sxs-lookup"><span data-stu-id="42c38-214">Scaffold-DbContext</span></span>

<span data-ttu-id="42c38-215">コードを生成、`DbContext`とデータベースのエンティティ型。</span><span class="sxs-lookup"><span data-stu-id="42c38-215">Generates code for a `DbContext` and entity types for a database.</span></span> <span data-ttu-id="42c38-216">順序で`Scaffold-DbContext`エンティティ型を生成するには、データベース テーブルが主キーをいる必要があります。</span><span class="sxs-lookup"><span data-stu-id="42c38-216">In order for `Scaffold-DbContext` to generate an entity type, the database table must have a primary key.</span></span>

<span data-ttu-id="42c38-217">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="42c38-217">Parameters:</span></span>

| <span data-ttu-id="42c38-218">パラメーター</span><span class="sxs-lookup"><span data-stu-id="42c38-218">Parameter</span></span>                          | <span data-ttu-id="42c38-219">説明</span><span class="sxs-lookup"><span data-stu-id="42c38-219">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="42c38-220"><nobr>の接続\<文字列 ></nobr></span><span class="sxs-lookup"><span data-stu-id="42c38-220"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="42c38-221">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="42c38-221">The connection string to the database.</span></span> <span data-ttu-id="42c38-222">ASP.NET Core 2.x プロジェクトの値を指定できます*名 =\<接続文字列の名前 >* します。</span><span class="sxs-lookup"><span data-stu-id="42c38-222">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="42c38-223">その場合は、名前は、プロジェクトに設定されている構成ソースから取得されます。</span><span class="sxs-lookup"><span data-stu-id="42c38-223">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="42c38-224">位置指定パラメーターは、これが必要です。</span><span class="sxs-lookup"><span data-stu-id="42c38-224">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="42c38-225"><nobr>プロバイダー\<文字列 ></nobr></span><span class="sxs-lookup"><span data-stu-id="42c38-225"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="42c38-226">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="42c38-226">The provider to use.</span></span> <span data-ttu-id="42c38-227">たとえば、NuGet パッケージの名前は、この通常:`Microsoft.EntityFrameworkCore.SqlServer`します。</span><span class="sxs-lookup"><span data-stu-id="42c38-227">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="42c38-228">位置指定パラメーターは、これが必要です。</span><span class="sxs-lookup"><span data-stu-id="42c38-228">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="42c38-229">-OutputDir\<String></span><span class="sxs-lookup"><span data-stu-id="42c38-229">-OutputDir \<String></span></span>               | <span data-ttu-id="42c38-230">ファイルを配置するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="42c38-230">The directory to put files in.</span></span> <span data-ttu-id="42c38-231">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="42c38-231">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="42c38-232">-ContextDir\<String></span><span class="sxs-lookup"><span data-stu-id="42c38-232">-ContextDir \<String></span></span>              | <span data-ttu-id="42c38-233">格納するディレクトリ、`DbContext`ファイルします。</span><span class="sxs-lookup"><span data-stu-id="42c38-233">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="42c38-234">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="42c38-234">Paths are relative to the project directory.</span></span>                                                                                                                                                                              |
| <span data-ttu-id="42c38-235">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="42c38-235">-Context \<String></span></span>                 | <span data-ttu-id="42c38-236">名前、`DbContext`クラスを生成します。</span><span class="sxs-lookup"><span data-stu-id="42c38-236">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="42c38-237">-Schemas \<String[]></span><span class="sxs-lookup"><span data-stu-id="42c38-237">-Schemas \<String[]></span></span>               | <span data-ttu-id="42c38-238">エンティティ型を生成するテーブルのスキーマです。</span><span class="sxs-lookup"><span data-stu-id="42c38-238">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="42c38-239">このパラメーターを省略すると、すべてのスキーマが含まれます。</span><span class="sxs-lookup"><span data-stu-id="42c38-239">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="42c38-240">-Tables \<String[]></span><span class="sxs-lookup"><span data-stu-id="42c38-240">-Tables \<String[]></span></span>                | <span data-ttu-id="42c38-241">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="42c38-241">The tables to generate entity types for.</span></span> <span data-ttu-id="42c38-242">このパラメーターを省略すると、すべてのテーブルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="42c38-242">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="42c38-243">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="42c38-243">-DataAnnotations</span></span>                   | <span data-ttu-id="42c38-244">属性を使用すると、可能な限り、モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="42c38-244">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="42c38-245">このパラメーターを省略した場合は、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="42c38-245">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="42c38-246">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="42c38-246">-UseDatabaseNames</span></span>                  | <span data-ttu-id="42c38-247">データベースに表示されているとおりに、テーブルおよび列名を使用します。</span><span class="sxs-lookup"><span data-stu-id="42c38-247">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="42c38-248">このパラメーターを省略すると、データベース名は、c# 名前表記規則をより厳密に準拠するように変更されます。</span><span class="sxs-lookup"><span data-stu-id="42c38-248">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="42c38-249">-Force</span><span class="sxs-lookup"><span data-stu-id="42c38-249">-Force</span></span>                             | <span data-ttu-id="42c38-250">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="42c38-250">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |

<span data-ttu-id="42c38-251">例:</span><span class="sxs-lookup"><span data-stu-id="42c38-251">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="42c38-252">選択したテーブルのみをスキャフォールディングし、指定した名前の別のフォルダーにコンテキストを作成する例:</span><span class="sxs-lookup"><span data-stu-id="42c38-252">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext
```

## <a name="script-migration"></a><span data-ttu-id="42c38-253">Script-Migration</span><span class="sxs-lookup"><span data-stu-id="42c38-253">Script-Migration</span></span>

<span data-ttu-id="42c38-254">すべての変更を 1 つの選択された移行から別の選択された移行に適用される SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="42c38-254">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="42c38-255">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="42c38-255">Parameters:</span></span>

| <span data-ttu-id="42c38-256">パラメーター</span><span class="sxs-lookup"><span data-stu-id="42c38-256">Parameter</span></span>                | <span data-ttu-id="42c38-257">説明</span><span class="sxs-lookup"><span data-stu-id="42c38-257">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="42c38-258">*-From*\<String></span><span class="sxs-lookup"><span data-stu-id="42c38-258">*-From* \<String></span></span>        | <span data-ttu-id="42c38-259">開始の移行。</span><span class="sxs-lookup"><span data-stu-id="42c38-259">The starting migration.</span></span> <span data-ttu-id="42c38-260">移行は名前または ID で識別される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42c38-260">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="42c38-261">数値 0 は特殊なケースでは意味*最初の移行の前に*します。</span><span class="sxs-lookup"><span data-stu-id="42c38-261">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="42c38-262">既定値は 0 です。</span><span class="sxs-lookup"><span data-stu-id="42c38-262">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="42c38-263">*-To* \<String></span><span class="sxs-lookup"><span data-stu-id="42c38-263">*-To* \<String></span></span>          | <span data-ttu-id="42c38-264">終了の移行。</span><span class="sxs-lookup"><span data-stu-id="42c38-264">The ending migration.</span></span> <span data-ttu-id="42c38-265">移行の最後の既定値します。</span><span class="sxs-lookup"><span data-stu-id="42c38-265">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="42c38-266"><nobr>-Idempotent</nobr></span><span class="sxs-lookup"><span data-stu-id="42c38-266"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="42c38-267">すべての移行でのデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="42c38-267">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="42c38-268">-Output\<String></span><span class="sxs-lookup"><span data-stu-id="42c38-268">-Output \<String></span></span>        | <span data-ttu-id="42c38-269">結果を書き込むファイル。</span><span class="sxs-lookup"><span data-stu-id="42c38-269">The file to write the result to.</span></span> <span data-ttu-id="42c38-270">たとえば、アプリのランタイム ファイルが作成されると、同じフォルダーに生成された名前でファイルを作成するこのパラメーターを省略すると、: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/* します。</span><span class="sxs-lookup"><span data-stu-id="42c38-270">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="42c38-271">および出力パラメーターは、タブ拡張をサポートします。</span><span class="sxs-lookup"><span data-stu-id="42c38-271">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="42c38-272">次の例では、移行の名前を使用して、InitialCreate 移行用のスクリプトを作成します。</span><span class="sxs-lookup"><span data-stu-id="42c38-272">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="42c38-273">次の例では、移行 ID を使用して、InitialCreate 移行後のすべての移行スクリプトを作成します</span><span class="sxs-lookup"><span data-stu-id="42c38-273">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="42c38-274">Update-Database</span><span class="sxs-lookup"><span data-stu-id="42c38-274">Update-Database</span></span>

<span data-ttu-id="42c38-275">最後に移行したり、指定された移行は、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="42c38-275">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="42c38-276">パラメーター</span><span class="sxs-lookup"><span data-stu-id="42c38-276">Parameter</span></span>                           | <span data-ttu-id="42c38-277">説明</span><span class="sxs-lookup"><span data-stu-id="42c38-277">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="42c38-278"><nobr>*-Migration*\<String></nobr></span><span class="sxs-lookup"><span data-stu-id="42c38-278"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="42c38-279">Target の移行。</span><span class="sxs-lookup"><span data-stu-id="42c38-279">The target migration.</span></span> <span data-ttu-id="42c38-280">移行は名前または ID で識別される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42c38-280">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="42c38-281">数値 0 は特殊なケースでは意味*最初の移行の前に*と、すべての移行を元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="42c38-281">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="42c38-282">移行が指定されていない場合のコマンドは、既定最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="42c38-282">If no migration is specified, the command defaults to the last migration.</span></span> |

> [!TIP]
> <span data-ttu-id="42c38-283">移行のパラメーターは、タブ拡張をサポートします。</span><span class="sxs-lookup"><span data-stu-id="42c38-283">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="42c38-284">次の例では、すべての移行を元に戻します。</span><span class="sxs-lookup"><span data-stu-id="42c38-284">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="42c38-285">次の例では、指定された移行するデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="42c38-285">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="42c38-286">最初の移行の名前を使用し、2 つ目は移行 ID を使用します。</span><span class="sxs-lookup"><span data-stu-id="42c38-286">The first uses the migration name and the second uses the migration ID:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate
```

## <a name="additional-resources"></a><span data-ttu-id="42c38-287">その他の技術情報</span><span class="sxs-lookup"><span data-stu-id="42c38-287">Additional resources</span></span>

* [<span data-ttu-id="42c38-288">移行</span><span class="sxs-lookup"><span data-stu-id="42c38-288">Migrations</span></span>](xref:core/managing-schemas/migrations/index)
* [<span data-ttu-id="42c38-289">リバース エンジニアリング</span><span class="sxs-lookup"><span data-stu-id="42c38-289">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
