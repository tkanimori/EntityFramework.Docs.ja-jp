---
title: EF Core ツール リファレンス (パッケージ マネージャー コンソール) - EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: db4d89b6a0babe01bccbeadc51381a309ad8ca0f
ms.sourcegitcommit: c568d33214fc25c76e02c8529a29da7a356b37b4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2018
ms.locfileid: "47459562"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a><span data-ttu-id="23613-102">Entity Framework Core ツールのリファレンス - Visual Studio でパッケージ マネージャー コンソール</span><span class="sxs-lookup"><span data-stu-id="23613-102">Entity Framework Core tools reference - Package Manager Console in Visual Studio</span></span>

<span data-ttu-id="23613-103">Entity Framework Core のパッケージ マネージャー コンソール (PMC) ツールでは、デザイン時開発タスクを実行します。</span><span class="sxs-lookup"><span data-stu-id="23613-103">The Package Manager Console (PMC) tools for Entity Framework Core perform design-time development tasks.</span></span> <span data-ttu-id="23613-104">たとえば、作成[移行](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations)移行を適用し、既存のデータベースに基づくモデルのコードを生成します。</span><span class="sxs-lookup"><span data-stu-id="23613-104">For example, they create [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations), apply migrations, and generate code for a model based on an existing database.</span></span> <span data-ttu-id="23613-105">コマンドは、Visual Studio を使用して内部で実行、[パッケージ マネージャー コンソール](/nuget/tools/package-manager-console)します。</span><span class="sxs-lookup"><span data-stu-id="23613-105">The commands run inside of Visual Studio using the [Package Manager Console](/nuget/tools/package-manager-console).</span></span> <span data-ttu-id="23613-106">これらのツールは、.NET Framework プロジェクトと .NET Core プロジェクトの両方に使えます。</span><span class="sxs-lookup"><span data-stu-id="23613-106">These tools work with both .NET Framework and .NET Core projects.</span></span>

<span data-ttu-id="23613-107">Visual Studio を使用していないかどうか、お勧め、 [EF Core コマンド ライン ツール](dotnet.md)代わりにします。</span><span class="sxs-lookup"><span data-stu-id="23613-107">If you aren't using Visual Studio, we recommend the [EF Core Command-line Tools](dotnet.md) instead.</span></span> <span data-ttu-id="23613-108">CLI ツールは、クロス プラットフォームと、コマンド プロンプト内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="23613-108">The CLI tools are cross-platform and run inside a command prompt.</span></span>

## <a name="installing-the-tools"></a><span data-ttu-id="23613-109">ツールのインストール</span><span class="sxs-lookup"><span data-stu-id="23613-109">Installing the tools</span></span>

<span data-ttu-id="23613-110">インストールして、ツールを更新する手順は、ASP.NET Core 2.1 以降と以前のバージョンまたはその他のプロジェクトの種類によって異なります。</span><span class="sxs-lookup"><span data-stu-id="23613-110">The procedures for installing and updating the tools differ between ASP.NET Core 2.1+ and earlier versions or other project types.</span></span>

### <a name="aspnet-core-version-21-and-later"></a><span data-ttu-id="23613-111">ASP.NET Core 2.1 以降のバージョン</span><span class="sxs-lookup"><span data-stu-id="23613-111">ASP.NET Core version 2.1 and later</span></span>

<span data-ttu-id="23613-112">ユーザーがためにで、ツールは、ASP.NET Core 2.1 以降のプロジェクトに自動的に含め、`Microsoft.EntityFrameworkCore.Tools`にパッケージが含まれる、 [Microsoft.AspNetCore.App メタパッケージ](/aspnet/core/fundamentals/metapackage-app)します。</span><span class="sxs-lookup"><span data-stu-id="23613-112">The tools are automatically included in an ASP.NET Core 2.1+ project because the `Microsoft.EntityFrameworkCore.Tools` package is included in the [Microsoft.AspNetCore.App metapackage](/aspnet/core/fundamentals/metapackage-app).</span></span>

<span data-ttu-id="23613-113">そのため、ツールをインストールすることが何もする必要はありませんを行うこと。</span><span class="sxs-lookup"><span data-stu-id="23613-113">Therefore, you don't have to do anything to install the tools, but you do have to:</span></span>
* <span data-ttu-id="23613-114">新しいプロジェクトのツールを使用する前にパッケージを復元します。</span><span class="sxs-lookup"><span data-stu-id="23613-114">Restore packages before using the tools in a new project.</span></span>
* <span data-ttu-id="23613-115">新しいバージョンにツールを更新するパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="23613-115">Install a package to update the tools to a newer version.</span></span>

<span data-ttu-id="23613-116">ツールの最新バージョンを取得していることを確認するには、次の手順を実行することお勧めします。</span><span class="sxs-lookup"><span data-stu-id="23613-116">To make sure that you're getting the latest version of the tools, we recommend that you also do the following step:</span></span>

* <span data-ttu-id="23613-117">編集、 *.csproj*ファイルを開き、最新バージョンを指定する行を追加、 [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/)パッケージ。</span><span class="sxs-lookup"><span data-stu-id="23613-117">Edit your *.csproj* file and add a line specifying the latest version of the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package.</span></span> <span data-ttu-id="23613-118">たとえば、 *.csproj*ファイルを含めることができます、`ItemGroup`次のようにします。</span><span class="sxs-lookup"><span data-stu-id="23613-118">For example, the *.csproj* file might include an `ItemGroup` that looks like this:</span></span>

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

<span data-ttu-id="23613-119">次の例のようなメッセージが表示された場合は、ツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="23613-119">Update the tools when you get a message like the following example:</span></span>

> <span data-ttu-id="23613-120">EF Core ツールのバージョン '2.1.1-rtm-30846' は、'2.1.3-rtm-32065' ランタイムのより古いです。</span><span class="sxs-lookup"><span data-stu-id="23613-120">The EF Core tools version '2.1.1-rtm-30846' is older than that of the runtime '2.1.3-rtm-32065'.</span></span> <span data-ttu-id="23613-121">最新の機能とバグ修正するためのツールを更新します。</span><span class="sxs-lookup"><span data-stu-id="23613-121">Update the tools for the latest features and bug fixes.</span></span>

<span data-ttu-id="23613-122">ツールの更新。</span><span class="sxs-lookup"><span data-stu-id="23613-122">To update the tools:</span></span>
* <span data-ttu-id="23613-123">最新の .NET Core SDK をインストールします。</span><span class="sxs-lookup"><span data-stu-id="23613-123">Install the latest .NET Core SDK.</span></span>
* <span data-ttu-id="23613-124">Visual Studio を最新バージョンに更新します。</span><span class="sxs-lookup"><span data-stu-id="23613-124">Update Visual Studio to the latest version.</span></span>
* <span data-ttu-id="23613-125">編集、 *.csproj*ファイルの最新のツール パッケージにパッケージ参照が含まれるように前述のようにします。</span><span class="sxs-lookup"><span data-stu-id="23613-125">Edit the *.csproj* file so that it includes a package reference to the latest tools package, as shown earlier.</span></span>

### <a name="other-versions-and-project-types"></a><span data-ttu-id="23613-126">他のバージョンおよびプロジェクトの種類</span><span class="sxs-lookup"><span data-stu-id="23613-126">Other versions and project types</span></span>

<span data-ttu-id="23613-127">次のコマンドを実行して、パッケージ マネージャー コンソールのツールをインストール**パッケージ マネージャー コンソール**:</span><span class="sxs-lookup"><span data-stu-id="23613-127">Install the Package Manager Console tools by running the following command in **Package Manager Console**:</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="23613-128">次のコマンドを実行してツールを更新する**パッケージ マネージャー コンソール**します。</span><span class="sxs-lookup"><span data-stu-id="23613-128">Update the tools by running the following command in **Package Manager Console**.</span></span>

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a><span data-ttu-id="23613-129">インストールを確認します。</span><span class="sxs-lookup"><span data-stu-id="23613-129">Verify the installation</span></span>

<span data-ttu-id="23613-130">このコマンドを実行して、ツールがインストールされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="23613-130">Verify that the tools are installed by running this command:</span></span>

``` powershell
Get-Help about_EntityFrameworkCore
```

<span data-ttu-id="23613-131">出力は、(、しないことがわかるツールを使用しているのバージョン) を次のようになります。</span><span class="sxs-lookup"><span data-stu-id="23613-131">The output looks like this (it doesn't tell you which version of the tools you're using):</span></span>

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

## <a name="using-the-tools"></a><span data-ttu-id="23613-132">ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="23613-132">Using the tools</span></span>

<span data-ttu-id="23613-133">前に、ツールを使用します。</span><span class="sxs-lookup"><span data-stu-id="23613-133">Before using the tools:</span></span>
* <span data-ttu-id="23613-134">ターゲットとスタートアップ プロジェクトの違いを理解します。</span><span class="sxs-lookup"><span data-stu-id="23613-134">Understand the difference between target and startup project.</span></span>
* <span data-ttu-id="23613-135">.NET Standard クラス ライブラリで、ツールを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="23613-135">Learn how to use the tools with .NET Standard class libraries.</span></span>
* <span data-ttu-id="23613-136">ASP.NET Core プロジェクトでは、環境を設定します。</span><span class="sxs-lookup"><span data-stu-id="23613-136">For ASP.NET Core projects, set the environment.</span></span>

### <a name="target-and-startup-project"></a><span data-ttu-id="23613-137">ターゲットとスタートアップ プロジェクト</span><span class="sxs-lookup"><span data-stu-id="23613-137">Target and startup project</span></span>

<span data-ttu-id="23613-138">コマンドを参照してください、*プロジェクト*と*スタートアップ プロジェクト*します。</span><span class="sxs-lookup"><span data-stu-id="23613-138">The commands refer to a *project* and a *startup project*.</span></span>

* <span data-ttu-id="23613-139">*プロジェクト*とも呼ばれますが、*ターゲット プロジェクト*コマンドが追加または、ファイルを削除であるためです。</span><span class="sxs-lookup"><span data-stu-id="23613-139">The *project* is also known as the *target project* because it's where the commands add or remove files.</span></span> <span data-ttu-id="23613-140">既定では、**既定のプロジェクト**で選択した**パッケージ マネージャー コンソール**ターゲット プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="23613-140">By default, the **Default project** selected in **Package Manager Console** is the target project.</span></span> <span data-ttu-id="23613-141">ターゲット プロジェクトとして使用して、別のプロジェクトを指定することができます、 <nobr> `--project` </nobr>オプション。</span><span class="sxs-lookup"><span data-stu-id="23613-141">You can specify a different project as target project by using the <nobr>`--project`</nobr> option.</span></span>

* <span data-ttu-id="23613-142">*スタートアップ プロジェクト*ツールをビルドして実行されます。</span><span class="sxs-lookup"><span data-stu-id="23613-142">The *startup project* is the one that the tools build and run.</span></span> <span data-ttu-id="23613-143">ツールは、データベース接続文字列と、モデルの構成など、プロジェクトに関する情報を取得するデザイン時にアプリケーション コードを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23613-143">The tools have to execute application code at design time to get information about the project, such as the database connection string and the configuration of the model.</span></span> <span data-ttu-id="23613-144">既定では、**スタートアップ プロジェクト**で**ソリューション エクスプ ローラー**はスタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="23613-144">By default, the **Startup Project** in **Solution Explorer** is the startup project.</span></span> <span data-ttu-id="23613-145">使用して、別のプロジェクトをスタートアップ プロジェクトとして指定できます、 <nobr> `--startup-project` </nobr>オプション。</span><span class="sxs-lookup"><span data-stu-id="23613-145">You can specify a different project as startup project by using the <nobr>`--startup-project`</nobr> option.</span></span>

<span data-ttu-id="23613-146">スタートアップ プロジェクトとターゲット プロジェクトは、同じプロジェクトでは多くの場合です。</span><span class="sxs-lookup"><span data-stu-id="23613-146">The startup project and target project are often the same project.</span></span> <span data-ttu-id="23613-147">個別のプロジェクトが、一般的なシナリオは。</span><span class="sxs-lookup"><span data-stu-id="23613-147">A typical scenario where they are separate projects is when:</span></span>

* <span data-ttu-id="23613-148">EF Core コンテキストとエンティティ クラスは、.NET Core クラス ライブラリでは。</span><span class="sxs-lookup"><span data-stu-id="23613-148">The EF Core context and entity classes are in a .NET Core class library.</span></span>
* <span data-ttu-id="23613-149">.NET Core コンソール アプリまたは web アプリは、クラス ライブラリを参照します。</span><span class="sxs-lookup"><span data-stu-id="23613-149">A .NET Core console app or web app references the class library.</span></span>

<span data-ttu-id="23613-150">することも[EF Core コンテキストから別のクラス ライブラリにコードを移行配置](xref:core/managing-schemas/migrations/projects)します。</span><span class="sxs-lookup"><span data-stu-id="23613-150">It's also possible to [put migrations code in a class library separate from the EF Core context](xref:core/managing-schemas/migrations/projects).</span></span>

### <a name="other-target-frameworks"></a><span data-ttu-id="23613-151">その他のターゲット フレームワーク</span><span class="sxs-lookup"><span data-stu-id="23613-151">Other target frameworks</span></span>

<span data-ttu-id="23613-152">パッケージ マネージャー コンソール ツールは、.NET Core または .NET Framework プロジェクトで動作します。</span><span class="sxs-lookup"><span data-stu-id="23613-152">The Package Manager Console tools work with .NET Core or .NET Framework projects.</span></span> <span data-ttu-id="23613-153">.NET Standard クラス ライブラリで、EF Core モデルを必要とするアプリケーションは、.NET Core または .NET Framework プロジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="23613-153">Apps that have the EF Core model in a .NET Standard class library might not have a .NET Core or .NET Framework project.</span></span> <span data-ttu-id="23613-154">たとえば、これは、Xamarin とユニバーサル Windows プラットフォーム アプリの場合は true。</span><span class="sxs-lookup"><span data-stu-id="23613-154">For example, this is true of Xamarin and Universal Windows Platform apps.</span></span> <span data-ttu-id="23613-155">このような場合は、のみを目的が、ツールのスタートアップ プロジェクトとして機能するには、.NET Core または .NET Framework コンソール アプリケーション プロジェクトを作成できます。</span><span class="sxs-lookup"><span data-stu-id="23613-155">In such cases, you can create a .NET Core or .NET Framework console app project whose only purpose is to act as startup project for the tools.</span></span> <span data-ttu-id="23613-156">プロジェクトは、実際のコードなしでダミー プロジェクト&mdash;ターゲット ツールを提供する必要があるだけです。</span><span class="sxs-lookup"><span data-stu-id="23613-156">The project can be a dummy project with no real code &mdash; it is only needed to provide a target for the tooling.</span></span>

<span data-ttu-id="23613-157">必要なダミー プロジェクトはなぜですか。</span><span class="sxs-lookup"><span data-stu-id="23613-157">Why is a dummy project required?</span></span> <span data-ttu-id="23613-158">前述のように、ツールは、デザイン時にアプリケーション コードを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23613-158">As mentioned earlier, the tools have to execute application code at design time.</span></span> <span data-ttu-id="23613-159">そのためには、.NET Core または .NET Framework のランタイムを使用して、必要があります。</span><span class="sxs-lookup"><span data-stu-id="23613-159">To do that, they need to use the .NET Core or .NET Framework runtime.</span></span> <span data-ttu-id="23613-160">EF Core モデルは、.NET Core または .NET Framework を対象とするプロジェクトでは、EF Core ツールは、プロジェクトからランタイムを借用します。</span><span class="sxs-lookup"><span data-stu-id="23613-160">When the EF Core model is in a project that targets .NET Core or .NET Framework, the EF Core tools borrow the runtime from the project.</span></span> <span data-ttu-id="23613-161">EF Core モデルが .NET Standard クラス ライブラリの場合、インストールできません。</span><span class="sxs-lookup"><span data-stu-id="23613-161">They can't do that if the EF Core model is in a .NET Standard class library.</span></span> <span data-ttu-id="23613-162">.NET Standard は、実際の .NET 実装ではありません。一連の .NET 実装をサポートする必要がある Api の仕様です。</span><span class="sxs-lookup"><span data-stu-id="23613-162">The .NET Standard is not an actual .NET implementation; it's a specification of a set of APIs that .NET implementations must support.</span></span> <span data-ttu-id="23613-163">したがって .NET Standard はアプリケーション コードを実行する EF Core ツールのための十分です。</span><span class="sxs-lookup"><span data-stu-id="23613-163">Therefore .NET Standard is not sufficient for the EF Core tools to execute application code.</span></span> <span data-ttu-id="23613-164">スタートアップ プロジェクトとして使用するように作成するダミーのプロジェクトでは、ツールが、.NET Standard クラス ライブラリを読み込むことができます、具体的なターゲット プラットフォームを提供します。</span><span class="sxs-lookup"><span data-stu-id="23613-164">The dummy project you create to use as startup project provides a concrete target platform into which the tools can load the .NET Standard class library.</span></span>

### <a name="aspnet-core-environment"></a><span data-ttu-id="23613-165">ASP.NET Core 環境</span><span class="sxs-lookup"><span data-stu-id="23613-165">ASP.NET Core environment</span></span>

<span data-ttu-id="23613-166">ASP.NET Core プロジェクト用の環境を指定するには、次のように設定します。 **env:ASPNETCORE_ENVIRONMENT**コマンドを実行する前にします。</span><span class="sxs-lookup"><span data-stu-id="23613-166">To specify the environment for ASP.NET Core projects, set **env:ASPNETCORE_ENVIRONMENT** before running commands.</span></span>

## <a name="common-parameters"></a><span data-ttu-id="23613-167">共通パラメーター</span><span class="sxs-lookup"><span data-stu-id="23613-167">Common parameters</span></span>

<span data-ttu-id="23613-168">次の表は、EF Core のコマンドのすべてに共通するパラメーターを示しています。</span><span class="sxs-lookup"><span data-stu-id="23613-168">The following table shows parameters that are common to all of the EF Core commands:</span></span>

| <span data-ttu-id="23613-169">パラメーター</span><span class="sxs-lookup"><span data-stu-id="23613-169">Parameter</span></span>                 | <span data-ttu-id="23613-170">説明</span><span class="sxs-lookup"><span data-stu-id="23613-170">Description</span></span>                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23613-171">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="23613-171">-Context \<String></span></span>        | <span data-ttu-id="23613-172">`DbContext`クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="23613-172">The `DbContext` class to use.</span></span> <span data-ttu-id="23613-173">クラス名のみ、または名前空間を持つ完全修飾します。</span><span class="sxs-lookup"><span data-stu-id="23613-173">Class name only or fully qualified with namespaces.</span></span>  <span data-ttu-id="23613-174">このパラメーターを省略した場合、EF Core は、コンテキスト クラスを検索します。</span><span class="sxs-lookup"><span data-stu-id="23613-174">If this parameter is omitted, EF Core finds the context class.</span></span> <span data-ttu-id="23613-175">複数のコンテキスト クラスがある場合は、このパラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="23613-175">If there are multiple context classes, this parameter is required.</span></span> |
| <span data-ttu-id="23613-176">-Project \<String></span><span class="sxs-lookup"><span data-stu-id="23613-176">-Project \<String></span></span>        | <span data-ttu-id="23613-177">ターゲットのプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="23613-177">The target project.</span></span> <span data-ttu-id="23613-178">このパラメーターを省略した場合、**既定のプロジェクト**の**パッケージ マネージャー コンソール**がターゲット プロジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="23613-178">If this parameter is omitted, the **Default project** for **Package Manager Console** is used as the target project.</span></span>                                                                             |
| <span data-ttu-id="23613-179">-StartupProject \<String></span><span class="sxs-lookup"><span data-stu-id="23613-179">-StartupProject \<String></span></span> | <span data-ttu-id="23613-180">スタートアップ プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="23613-180">The startup project.</span></span> <span data-ttu-id="23613-181">このパラメーターを省略した場合、**スタートアップ プロジェクト**で**ソリューションのプロパティ**ターゲット プロジェクトとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="23613-181">If this parameter is omitted, the **Startup project** in **Solution properties** is used as the target project.</span></span>                                                                                 |
| <span data-ttu-id="23613-182">-Verbose</span><span class="sxs-lookup"><span data-stu-id="23613-182">-Verbose</span></span>                  | <span data-ttu-id="23613-183">詳細な出力を表示します。</span><span class="sxs-lookup"><span data-stu-id="23613-183">Show verbose output.</span></span>                                                                                                                                                                                                 |

<span data-ttu-id="23613-184">コマンドに関するヘルプ情報を表示するには、PowerShell を使用して`Get-Help`コマンド。</span><span class="sxs-lookup"><span data-stu-id="23613-184">To show help information about a command, use PowerShell's `Get-Help` command.</span></span>

> [!TIP]
> <span data-ttu-id="23613-185">コンテキスト、プロジェクト、およびスタートアップ プロジェクトのパラメーターでは、タブ拡張をサポートします。</span><span class="sxs-lookup"><span data-stu-id="23613-185">The Context, Project, and StartupProject parameters support tab-expansion.</span></span>

## <a name="add-migration"></a><span data-ttu-id="23613-186">追加の移行</span><span class="sxs-lookup"><span data-stu-id="23613-186">Add-Migration</span></span>

<span data-ttu-id="23613-187">新しい移行を追加します。</span><span class="sxs-lookup"><span data-stu-id="23613-187">Adds a new migration.</span></span>

<span data-ttu-id="23613-188">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="23613-188">Parameters:</span></span>

| <span data-ttu-id="23613-189">パラメーター</span><span class="sxs-lookup"><span data-stu-id="23613-189">Parameter</span></span>                         | <span data-ttu-id="23613-190">説明</span><span class="sxs-lookup"><span data-stu-id="23613-190">Description</span></span>                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23613-191"><nobr>-Name\<文字列 ><nobr></span><span class="sxs-lookup"><span data-stu-id="23613-191"><nobr>-Name \<String><nobr></span></span>       | <span data-ttu-id="23613-192">移行の名前。</span><span class="sxs-lookup"><span data-stu-id="23613-192">The name of the migration.</span></span> <span data-ttu-id="23613-193">位置指定パラメーターは、これが必要です。</span><span class="sxs-lookup"><span data-stu-id="23613-193">This is a positional parameter and is required.</span></span>                                              |
| <span data-ttu-id="23613-194"><nobr>-OutputDir\<文字列 ></nobr></span><span class="sxs-lookup"><span data-stu-id="23613-194"><nobr>-OutputDir \<String></nobr></span></span> | <span data-ttu-id="23613-195">使用するディレクトリ (およびサブ名前空間)。</span><span class="sxs-lookup"><span data-stu-id="23613-195">The directory (and sub-namespace) to use.</span></span> <span data-ttu-id="23613-196">パスでは、ターゲット プロジェクトのディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="23613-196">Paths are relative to the target project directory.</span></span> <span data-ttu-id="23613-197">既定値は「移行」です。</span><span class="sxs-lookup"><span data-stu-id="23613-197">Defaults to "Migrations".</span></span> |

## <a name="drop-database"></a><span data-ttu-id="23613-198">データベースの削除</span><span class="sxs-lookup"><span data-stu-id="23613-198">Drop-Database</span></span>

<span data-ttu-id="23613-199">データベースを削除します。</span><span class="sxs-lookup"><span data-stu-id="23613-199">Drops the database.</span></span>

<span data-ttu-id="23613-200">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="23613-200">Parameters:</span></span>

| <span data-ttu-id="23613-201">パラメーター</span><span class="sxs-lookup"><span data-stu-id="23613-201">Parameter</span></span> | <span data-ttu-id="23613-202">説明</span><span class="sxs-lookup"><span data-stu-id="23613-202">Description</span></span>                                              |
|:----------|:---------------------------------------------------------|
| <span data-ttu-id="23613-203">-WhatIf</span><span class="sxs-lookup"><span data-stu-id="23613-203">-WhatIf</span></span>   | <span data-ttu-id="23613-204">表示するデータベースが削除されます。 は削除しないでください。</span><span class="sxs-lookup"><span data-stu-id="23613-204">Show which database would be dropped, but don't drop it.</span></span> |

## <a name="get-dbcontext"></a><span data-ttu-id="23613-205">Get-DbContext</span><span class="sxs-lookup"><span data-stu-id="23613-205">Get-DbContext</span></span>

<span data-ttu-id="23613-206">使用可能な`DbContext`型。</span><span class="sxs-lookup"><span data-stu-id="23613-206">Lists available `DbContext` types.</span></span>

## <a name="remove-migration"></a><span data-ttu-id="23613-207">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="23613-207">Remove-Migration</span></span>

<span data-ttu-id="23613-208">最後の移行 (ロールバックの移行の実行されたコードの変更) を削除します。</span><span class="sxs-lookup"><span data-stu-id="23613-208">Removes the last migration (rolls back the code changes that were done for the migration).</span></span>

<span data-ttu-id="23613-209">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="23613-209">Parameters:</span></span>

| <span data-ttu-id="23613-210">パラメーター</span><span class="sxs-lookup"><span data-stu-id="23613-210">Parameter</span></span> | <span data-ttu-id="23613-211">説明</span><span class="sxs-lookup"><span data-stu-id="23613-211">Description</span></span>                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| <span data-ttu-id="23613-212">-Force</span><span class="sxs-lookup"><span data-stu-id="23613-212">-Force</span></span>    | <span data-ttu-id="23613-213">移行を元に戻す (ロールバックをデータベースに適用された変更)。</span><span class="sxs-lookup"><span data-stu-id="23613-213">Revert the migration (roll back the changes that were applied to the database).</span></span> |

## <a name="scaffold-dbcontext"></a><span data-ttu-id="23613-214">Scaffold-dbcontext</span><span class="sxs-lookup"><span data-stu-id="23613-214">Scaffold-DbContext</span></span>

<span data-ttu-id="23613-215">コードを生成、`DbContext`とデータベースのエンティティ型。</span><span class="sxs-lookup"><span data-stu-id="23613-215">Generates code for a `DbContext` and entity types for a database.</span></span>

<span data-ttu-id="23613-216">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="23613-216">Parameters:</span></span>

| <span data-ttu-id="23613-217">パラメーター</span><span class="sxs-lookup"><span data-stu-id="23613-217">Parameter</span></span>                          | <span data-ttu-id="23613-218">説明</span><span class="sxs-lookup"><span data-stu-id="23613-218">Description</span></span>                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23613-219"><nobr>の接続\<文字列 ></nobr></span><span class="sxs-lookup"><span data-stu-id="23613-219"><nobr>-Connection \<String></nobr></span></span> | <span data-ttu-id="23613-220">データベースへの接続文字列。</span><span class="sxs-lookup"><span data-stu-id="23613-220">The connection string to the database.</span></span> <span data-ttu-id="23613-221">ASP.NET Core 2.x プロジェクトの値を指定できます*名 =\<接続文字列の名前 >* します。</span><span class="sxs-lookup"><span data-stu-id="23613-221">For ASP.NET Core 2.x projects, the value can be *name=\<name of connection string>*.</span></span> <span data-ttu-id="23613-222">その場合は、名前は、プロジェクトに設定されている構成ソースから取得されます。</span><span class="sxs-lookup"><span data-stu-id="23613-222">In that case the name comes from the configuration sources that are set up for the project.</span></span> <span data-ttu-id="23613-223">位置指定パラメーターは、これが必要です。</span><span class="sxs-lookup"><span data-stu-id="23613-223">This is a positional parameter and is required.</span></span> |
| <span data-ttu-id="23613-224"><nobr>プロバイダー\<文字列 ></nobr></span><span class="sxs-lookup"><span data-stu-id="23613-224"><nobr>-Provider \<String></nobr></span></span>   | <span data-ttu-id="23613-225">使用するプロバイダー。</span><span class="sxs-lookup"><span data-stu-id="23613-225">The provider to use.</span></span> <span data-ttu-id="23613-226">たとえば、NuGet パッケージの名前は、この通常:`Microsoft.EntityFrameworkCore.SqlServer`します。</span><span class="sxs-lookup"><span data-stu-id="23613-226">Typically this is the name of the NuGet package, for example: `Microsoft.EntityFrameworkCore.SqlServer`.</span></span> <span data-ttu-id="23613-227">位置指定パラメーターは、これが必要です。</span><span class="sxs-lookup"><span data-stu-id="23613-227">This is a positional parameter and is required.</span></span>                                                                                           |
| <span data-ttu-id="23613-228">-OutputDir\<文字列 ></span><span class="sxs-lookup"><span data-stu-id="23613-228">-OutputDir \<String></span></span>               | <span data-ttu-id="23613-229">ファイルを配置するディレクトリ。</span><span class="sxs-lookup"><span data-stu-id="23613-229">The directory to put files in.</span></span> <span data-ttu-id="23613-230">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="23613-230">Paths are relative to the project directory.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="23613-231">-ContextDir\<文字列 ></span><span class="sxs-lookup"><span data-stu-id="23613-231">-ContextDir \<String></span></span>              | <span data-ttu-id="23613-232">格納するディレクトリ、`DbContext`ファイルします。</span><span class="sxs-lookup"><span data-stu-id="23613-232">The directory to put the `DbContext` file in.</span></span> <span data-ttu-id="23613-233">パスでは、プロジェクト ディレクトリに対して相対的です。</span><span class="sxs-lookup"><span data-stu-id="23613-233">Paths are relative to the project directory.</span></span>                                                                                                                                                                              |
| <span data-ttu-id="23613-234">-Context \<String></span><span class="sxs-lookup"><span data-stu-id="23613-234">-Context \<String></span></span>                 | <span data-ttu-id="23613-235">名前、`DbContext`クラスを生成します。</span><span class="sxs-lookup"><span data-stu-id="23613-235">The name of the `DbContext` class to generate.</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="23613-236">-Schemas \<String[]></span><span class="sxs-lookup"><span data-stu-id="23613-236">-Schemas \<String[]></span></span>               | <span data-ttu-id="23613-237">エンティティ型を生成するテーブルのスキーマです。</span><span class="sxs-lookup"><span data-stu-id="23613-237">The schemas of tables to generate entity types for.</span></span> <span data-ttu-id="23613-238">このパラメーターを省略すると、すべてのスキーマが含まれます。</span><span class="sxs-lookup"><span data-stu-id="23613-238">If this parameter is omitted, all schemas are included.</span></span>                                                                                                                                                             |
| <span data-ttu-id="23613-239">-Tables \<String[]></span><span class="sxs-lookup"><span data-stu-id="23613-239">-Tables \<String[]></span></span>                | <span data-ttu-id="23613-240">エンティティ型を生成するテーブル。</span><span class="sxs-lookup"><span data-stu-id="23613-240">The tables to generate entity types for.</span></span> <span data-ttu-id="23613-241">このパラメーターを省略すると、すべてのテーブルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="23613-241">If this parameter is omitted, all tables are included.</span></span>                                                                                                                                                                         |
| <span data-ttu-id="23613-242">-DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="23613-242">-DataAnnotations</span></span>                   | <span data-ttu-id="23613-243">属性を使用すると、可能な限り、モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="23613-243">Use attributes to configure the model (where possible).</span></span> <span data-ttu-id="23613-244">このパラメーターを省略した場合は、fluent API のみが使用されます。</span><span class="sxs-lookup"><span data-stu-id="23613-244">If this parameter is omitted, only the fluent API is used.</span></span>                                                                                                                                                      |
| <span data-ttu-id="23613-245">-UseDatabaseNames</span><span class="sxs-lookup"><span data-stu-id="23613-245">-UseDatabaseNames</span></span>                  | <span data-ttu-id="23613-246">データベースに表示されているとおりに、テーブルおよび列名を使用します。</span><span class="sxs-lookup"><span data-stu-id="23613-246">Use table and column names exactly as they appear in the database.</span></span> <span data-ttu-id="23613-247">このパラメーターを省略すると、データベース名は、c# 名前表記規則をより厳密に準拠するように変更されます。</span><span class="sxs-lookup"><span data-stu-id="23613-247">If this parameter is omitted, database names are changed to more closely conform to C# name style conventions.</span></span>                                                                                       |
| <span data-ttu-id="23613-248">-Force</span><span class="sxs-lookup"><span data-stu-id="23613-248">-Force</span></span>                             | <span data-ttu-id="23613-249">既存のファイルを上書きします。</span><span class="sxs-lookup"><span data-stu-id="23613-249">Overwrite existing files.</span></span>                                                                                                                                                                                                                                               |

<span data-ttu-id="23613-250">例:</span><span class="sxs-lookup"><span data-stu-id="23613-250">Example:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="23613-251">選択したテーブルのみをスキャフォールディングし、指定した名前の別のフォルダーにコンテキストを作成する例:</span><span class="sxs-lookup"><span data-stu-id="23613-251">Example that scaffolds only selected tables and creates the context in a separate folder with a specified name:</span></span>

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext
```

## <a name="script-migration"></a><span data-ttu-id="23613-252">スクリプトの移行</span><span class="sxs-lookup"><span data-stu-id="23613-252">Script-Migration</span></span>

<span data-ttu-id="23613-253">すべての変更を 1 つの選択された移行から別の選択された移行に適用される SQL スクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="23613-253">Generates a SQL script that applies all of the changes from one selected migration to another selected migration.</span></span>

<span data-ttu-id="23613-254">パラメーター:</span><span class="sxs-lookup"><span data-stu-id="23613-254">Parameters:</span></span>

| <span data-ttu-id="23613-255">パラメーター</span><span class="sxs-lookup"><span data-stu-id="23613-255">Parameter</span></span>                | <span data-ttu-id="23613-256">説明</span><span class="sxs-lookup"><span data-stu-id="23613-256">Description</span></span>                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23613-257">*-から*\<文字列 ></span><span class="sxs-lookup"><span data-stu-id="23613-257">*-From* \<String></span></span>        | <span data-ttu-id="23613-258">開始の移行。</span><span class="sxs-lookup"><span data-stu-id="23613-258">The starting migration.</span></span> <span data-ttu-id="23613-259">移行は名前または ID で識別される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="23613-259">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="23613-260">数値 0 は特殊なケースでは意味*最初の移行の前に*します。</span><span class="sxs-lookup"><span data-stu-id="23613-260">The number 0 is a special case that means *before the first migration*.</span></span> <span data-ttu-id="23613-261">既定値は 0 です。</span><span class="sxs-lookup"><span data-stu-id="23613-261">Defaults to 0.</span></span>                                                              |
| <span data-ttu-id="23613-262">*-* \<文字列 ></span><span class="sxs-lookup"><span data-stu-id="23613-262">*-To* \<String></span></span>          | <span data-ttu-id="23613-263">終了の移行。</span><span class="sxs-lookup"><span data-stu-id="23613-263">The ending migration.</span></span> <span data-ttu-id="23613-264">移行の最後の既定値します。</span><span class="sxs-lookup"><span data-stu-id="23613-264">Defaults to the last migration.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="23613-265"><nobr>-べき等であります。</nobr></span><span class="sxs-lookup"><span data-stu-id="23613-265"><nobr>-Idempotent</nobr></span></span> | <span data-ttu-id="23613-266">すべての移行でのデータベースで使用できるスクリプトを生成します。</span><span class="sxs-lookup"><span data-stu-id="23613-266">Generate a script that can be used on a database at any migration.</span></span>                                                                                                                                                         |
| <span data-ttu-id="23613-267">-出力\<文字列 ></span><span class="sxs-lookup"><span data-stu-id="23613-267">-Output \<String></span></span>        | <span data-ttu-id="23613-268">結果を書き込むファイル。</span><span class="sxs-lookup"><span data-stu-id="23613-268">The file to write the result to.</span></span> <span data-ttu-id="23613-269">たとえば、アプリのランタイム ファイルが作成されると、同じフォルダーに生成された名前でファイルを作成するこのパラメーターを省略すると、: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/* します。</span><span class="sxs-lookup"><span data-stu-id="23613-269">IF this parameter is omitted, the file is created with a generated name in the same folder as the app's runtime files are created, for example: */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*.</span></span> |

> [!TIP]
> <span data-ttu-id="23613-270">および出力パラメーターは、タブ拡張をサポートします。</span><span class="sxs-lookup"><span data-stu-id="23613-270">The To, From, and Output parameters support tab-expansion.</span></span>

<span data-ttu-id="23613-271">次の例では、移行の名前を使用して、InitialCreate 移行用のスクリプトを作成します。</span><span class="sxs-lookup"><span data-stu-id="23613-271">The following example creates a script for the InitialCreate migration, using the migration name.</span></span>

```powershell
Script-Migration -To InitialCreate
```

<span data-ttu-id="23613-272">次の例では、移行 ID を使用して、InitialCreate 移行後のすべての移行スクリプトを作成します</span><span class="sxs-lookup"><span data-stu-id="23613-272">The following example creates a script for all migrations after the InitialCreate migration, using the migration ID.</span></span>

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a><span data-ttu-id="23613-273">データベースの更新</span><span class="sxs-lookup"><span data-stu-id="23613-273">Update-Database</span></span>

<span data-ttu-id="23613-274">最後に移行したり、指定された移行は、データベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="23613-274">Updates the database to the last migration or to a specified migration.</span></span>

| <span data-ttu-id="23613-275">パラメーター</span><span class="sxs-lookup"><span data-stu-id="23613-275">Parameter</span></span>                           | <span data-ttu-id="23613-276">説明</span><span class="sxs-lookup"><span data-stu-id="23613-276">Description</span></span>                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23613-277"><nobr>*-移行*\<文字列 ></nobr></span><span class="sxs-lookup"><span data-stu-id="23613-277"><nobr>*-Migration* \<String></nobr></span></span> | <span data-ttu-id="23613-278">Target の移行。</span><span class="sxs-lookup"><span data-stu-id="23613-278">The target migration.</span></span> <span data-ttu-id="23613-279">移行は名前または ID で識別される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="23613-279">Migrations may be identified by name or by ID.</span></span> <span data-ttu-id="23613-280">数値 0 は特殊なケースでは意味*最初の移行の前に*と、すべての移行を元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="23613-280">The number 0 is a special case that means *before the first migration* and causes all migrations to be reverted.</span></span> <span data-ttu-id="23613-281">移行が指定されていない場合のコマンドは、既定最後の移行になります。</span><span class="sxs-lookup"><span data-stu-id="23613-281">If no migration is specified, the command defaults to the last migration.</span></span> |

> [!TIP]
> <span data-ttu-id="23613-282">移行のパラメーターは、タブ拡張をサポートします。</span><span class="sxs-lookup"><span data-stu-id="23613-282">The Migration parameter supports tab-expansion.</span></span>

<span data-ttu-id="23613-283">次の例では、すべての移行を元に戻します。</span><span class="sxs-lookup"><span data-stu-id="23613-283">The following example reverts all migrations.</span></span>

```powershell
Update-Database -Migration 0
```

<span data-ttu-id="23613-284">次の例では、指定された移行するデータベースを更新します。</span><span class="sxs-lookup"><span data-stu-id="23613-284">The following examples update the database to a specified migration.</span></span> <span data-ttu-id="23613-285">最初の移行の名前を使用し、2 つ目は移行 ID を使用します。</span><span class="sxs-lookup"><span data-stu-id="23613-285">The first uses the migration name and the second uses the migration ID:</span></span>

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate
```
