---
title: "EF Core のインストール"
author: divega
ms.author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
ms.technology: entity-framework-core
uid: core/get-started/install/index
ms.openlocfilehash: 31b96ebd0ae282b88be98988eff6263084dc5dd5
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="installing-ef-core"></a><span data-ttu-id="778a5-102">EF Core のインストール</span><span class="sxs-lookup"><span data-stu-id="778a5-102">Installing EF Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="778a5-103">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="778a5-103">Prerequisites</span></span>

<span data-ttu-id="778a5-104">.NET Core 2.0 アプリケーション (.NET Core を対象とする ASP.NET Core 2.0 アプリケーションを含む) を開発するには、お使いのプラットフォームに適したバージョンの [.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core) をダウンロードし、インストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="778a5-104">In order to develop .NET Core 2.0 applications (including ASP.NET Core 2.0 applications that target .NET Core) you will need to download and install a version of the [.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core) that is appropriate to your platform.</span></span> <span data-ttu-id="778a5-105">**Visual Studio 2017 バージョン 15.3 をインストールしている場合でも必要です。**</span><span class="sxs-lookup"><span data-stu-id="778a5-105">**This is true even if you have installed Visual Studio 2017 version 15.3.**</span></span>

<span data-ttu-id="778a5-106">.NET Core 2.0 以外の .NET プラットフォーム (たとえば、.NET Framework 4.6.1 以降) で EF Core 2.0 やその他の .NET Standard 2.0 ライブラリを使用するには、.NET Standard 2.0 とその互換フレームワークを認識するバージョンの NuGet が必要になります。</span><span class="sxs-lookup"><span data-stu-id="778a5-106">In order to use EF Core 2.0 or any other .NET Standard 2.0 library with a .NET platforms besides .NET Core 2.0 (e.g. with .NET Framework 4.6.1 or greater) you will need a version of NuGet that is aware of the .NET Standard 2.0 and its compatible frameworks.</span></span> <span data-ttu-id="778a5-107">次の方法で入手できます。</span><span class="sxs-lookup"><span data-stu-id="778a5-107">Here are a few ways you can obtain this:</span></span>

* <span data-ttu-id="778a5-108">Visual Studio 2017 バージョン 15.3 をインストールする</span><span class="sxs-lookup"><span data-stu-id="778a5-108">Install Visual Studio 2017 version 15.3</span></span>
* <span data-ttu-id="778a5-109">Visual Studio 2015 を使用している場合、[NuGet クライアント バージョン 3.6.0 をダウンロードするか、このバージョンにアップグレードします](https://www.nuget.org/downloads)。</span><span class="sxs-lookup"><span data-stu-id="778a5-109">If you are using Visual Studio 2015, [download and upgrade NuGet client to version 3.6.0](https://www.nuget.org/downloads)</span></span>

<span data-ttu-id="778a5-110">以前のバージョンの Visual Studio とその対象となる .NET Framework で作成されたプロジェクトの場合、.NET Standard 2.0 ライブラリ互換にするには追加の変更が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="778a5-110">Projects created with previous versions of Visual Studio and targeting .NET Framework may need additional modifications in order to be compatible with .NET Standard 2.0 libraries:</span></span>

* <span data-ttu-id="778a5-111">プロジェクト ファイルを編集し、初期プロパティ グループに次のエントリが表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="778a5-111">Edit the project file and make sure the following entry appears in the initial property group:</span></span>
  ``` xml
  <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
  ```

* <span data-ttu-id="778a5-112">テスト プロジェクトの場合、次のエントリも必要です。</span><span class="sxs-lookup"><span data-stu-id="778a5-112">For test projects, also make sure the following entry is present:</span></span>
  ``` xml
  <GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
  ```

## <a name="getting-the-bits"></a><span data-ttu-id="778a5-113">ビットを取得する</span><span class="sxs-lookup"><span data-stu-id="778a5-113">Getting the bits</span></span>
<span data-ttu-id="778a5-114">EF Core ランタイム ライブラリをアプリケーションに追加するための推奨方法は、NuGet から EF Core データベース プロバイダーをインストールすることです。</span><span class="sxs-lookup"><span data-stu-id="778a5-114">The recommended way to add EF Core runtime libraries into an application is to install an EF Core database provider from NuGet.</span></span>

<span data-ttu-id="778a5-115">ランタイム ライブラリに加え、デザイン時にプロジェクトでいくつかの EF Core 関連タスクの実行を簡単にするツールをインストールできます。関連タスクには、移行の作成と適用、既存データベースに基づくモデルの作成などがあります。</span><span class="sxs-lookup"><span data-stu-id="778a5-115">Besides the runtime libraries, you can install tools which make it easier to perform several EF Core-related tasks in your project at design time, such as creating and applying migrations, and creating a model based on an existing database.</span></span>

> [!TIP]  
> <span data-ttu-id="778a5-116">サードパーティ データベース プロバイダーを使用するアプリケーションを更新する場合、使用するバージョンの EF Core との間で互換性があるプロバイダーの更新がないか、常に確認してください。</span><span class="sxs-lookup"><span data-stu-id="778a5-116">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="778a5-117">例:</span><span class="sxs-lookup"><span data-stu-id="778a5-117">E.g.</span></span> <span data-ttu-id="778a5-118">前のバージョンのデータベース プロバイダーは EF Core ランタイムのバージョン 2.0 との間で互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="778a5-118">database providers for previous versions are not compatible with version 2.0 of the EF Core runtime.</span></span>  

> [!TIP]  
> <span data-ttu-id="778a5-119">ASP.NET Core 2.0 を対象とするアプリケーションは、サードパーティ データベース プロバイダー以外の依存関係を追加せずに EF Core 2.0 を使用できます。</span><span class="sxs-lookup"><span data-stu-id="778a5-119">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="778a5-120">前バージョンの ASP.NET Core を対象とするアプリケーションで EF Core 2.0 を使用するには、ASP.NET Core 2.0 にアップグレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="778a5-120">Applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span>

<a name="cli"></a>
### <a name="cross-platform-development-using-the-net-core-command-line-interface-cli"></a><span data-ttu-id="778a5-121">.NET Core コマンド ライン インターフェイス (CLI) を利用したクロスプラットフォーム開発</span><span class="sxs-lookup"><span data-stu-id="778a5-121">Cross-platform development using the .NET Core Command Line Interface (CLI)</span></span>

<span data-ttu-id="778a5-122">[.NET Core](https://www.microsoft.com/net/download/core) を対象とするアプリケーションを開発するには、お気に入りのテキスト エディターや、Visual Studio、Visual Studio for Mac、Visual Studio Code などの統合開発環境 (IDE) と組み合わせて [`dotnet` CLI コマンド](https://docs.microsoft.com/dotnet/core/tools/)を使用できます。</span><span class="sxs-lookup"><span data-stu-id="778a5-122">To develop applications that target [.NET Core](https://www.microsoft.com/net/download/core) you can choose to use the [`dotnet` CLI commands](https://docs.microsoft.com/dotnet/core/tools/) in combination with your favorite text editor, or an Integrated Development Environment (IDE) such as Visual Studio, Visual Studio for Mac or Visual Studio Code.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="778a5-123">.NET Core を対象とするアプリケーションは特定のバージョンの Visual Studio を必要とします。たとえば、.NET Core 1.x 開発には Visual Studio 2017 が必要であり、.NET Core 2.0 開発には Visual Studio 2017 バージョン 15.3 が必要です。</span><span class="sxs-lookup"><span data-stu-id="778a5-123">Applications that target .NET Core require specific versions of Visual Studio, e.g. .NET Core 1.x development requires Visual Studio 2017, while .NET Core 2.0 development requires Visual Studio 2017 version 15.3.</span></span>

<span data-ttu-id="778a5-124">クロスプラットフォーム .NET Core アプリケーションで SQL Server プロバイダーをインストールまたはアップグレードするには、アプリケーションのディレクトリに切り替え、コマンド ラインで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="778a5-124">To install or upgrade the SQL Server provider in a cross-platform .NET Core application, switch to the application's directory and run the following in a command line:</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="778a5-125">`-v` 修飾子を利用し、`dotnet add package` コマンドで特定のバージョンのインストールを指定できます。</span><span class="sxs-lookup"><span data-stu-id="778a5-125">You can indicate a specific version install in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="778a5-126">例:</span><span class="sxs-lookup"><span data-stu-id="778a5-126">E.g.</span></span> <span data-ttu-id="778a5-127">EF Core 2.0 パッケージをインストールするには、コマンドに `-v 2.0.0` を追加します。</span><span class="sxs-lookup"><span data-stu-id="778a5-127">to install EF Core 2.0 packages, append `-v 2.0.0` to the command.</span></span>

<span data-ttu-id="778a5-128">EF Core には、[`dotnet` CLI の追加コマンド](../../miscellaneous/cli/dotnet.md)のセットが含まれており、その最初が `dotnet ef` です。</span><span class="sxs-lookup"><span data-stu-id="778a5-128">EF Core includes a set of [additional commands for the `dotnet` CLI](../../miscellaneous/cli/dotnet.md), starting with `dotnet ef`.</span></span> <span data-ttu-id="778a5-129">`dotnet ef` CLI コマンドを使用するには、アプリケーションの `.csproj` ファイルに次のエントリを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="778a5-129">In order to use the `dotnet ef` CLI commands, your application’s `.csproj` file needs to contain the following entry:</span></span>

``` xml
<ItemGroup>
  <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
</ItemGroup>
```

<span data-ttu-id="778a5-130">EF Core 向け .NET Core CLI ツールには、Microsoft.EntityFrameworkCore.Design と呼ばれている別個のパッケージも必要です。</span><span class="sxs-lookup"><span data-stu-id="778a5-130">The .NET Core CLI tools for EF Core also require a separate package called Microsoft.EntityFrameworkCore.Design.</span></span> <span data-ttu-id="778a5-131">次を利用し、プロジェクトに追加できます。</span><span class="sxs-lookup"><span data-stu-id="778a5-131">You can simply add it to the project using:</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.Design
```

> [!IMPORTANT]  
> <span data-ttu-id="778a5-132">ランタイム パッケージのメジャー バージョンと一致するバージョンのツール パッケージを常に使用してください。</span><span class="sxs-lookup"><span data-stu-id="778a5-132">Always use versions of the tools packages that match the major version of the runtime packages.</span></span>

<a name="visual-studio"></a>
### <a name="visual-studio-development"></a><span data-ttu-id="778a5-133">Visual Studio 開発</span><span class="sxs-lookup"><span data-stu-id="778a5-133">Visual Studio development</span></span>

<span data-ttu-id="778a5-134">.NET Core、.NET Framework、または EF Core でサポートされているその他のプラットフォームを対象とするさまざまなアプリケーションを Visual Studio で開発できます。</span><span class="sxs-lookup"><span data-stu-id="778a5-134">You can develop many different types of applications that target .NET Core, .NET Framework, or other platforms supported by EF Core using Visual Studio.</span></span>

<span data-ttu-id="778a5-135">Visual Studio からアプリケーションに EF Core データベース プロバイダーをインストールする方法が 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="778a5-135">There are two ways you can install an EF Core database provider in your application from Visual Studio:</span></span>

#### <a name="using-nugets-package-manager-user-interfacehttpsdocsmicrosoftcomnugettoolspackage-manager-ui"></a><span data-ttu-id="778a5-136">NuGet の[パッケージ マネージャー ユーザー インターフェイス](https://docs.microsoft.com/nuget/tools/package-manager-ui)を使用する</span><span class="sxs-lookup"><span data-stu-id="778a5-136">Using NuGet's [Package Manager User Interface](https://docs.microsoft.com/nuget/tools/package-manager-ui)</span></span>

* <span data-ttu-id="778a5-137">メニューで **[プロジェクト]、[NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="778a5-137">Select on the menu **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="778a5-138">**[参照]** または **[更新]** タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="778a5-138">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="778a5-139">`Microsoft.EntityFrameworkCore.SqlServer` パッケージと希望のバージョンを選択し、確定します。</span><span class="sxs-lookup"><span data-stu-id="778a5-139">Select the `Microsoft.EntityFrameworkCore.SqlServer` package and the desired version and confirm</span></span>

#### <a name="using-nugets-package-manager-console-pmchttpsdocsmicrosoftcomnugettoolspackage-manager-console"></a><span data-ttu-id="778a5-140">NuGet の[パッケージ マネージャー コンソール (PMC)](https://docs.microsoft.com/nuget/tools/package-manager-console) を使用する</span><span class="sxs-lookup"><span data-stu-id="778a5-140">Using NuGet's [Package Manager Console (PMC)](https://docs.microsoft.com/nuget/tools/package-manager-console)</span></span>

* <span data-ttu-id="778a5-141">メニューで **[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="778a5-141">Select on the menu **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="778a5-142">PMC で次のコマンドを入力し、実行します。</span><span class="sxs-lookup"><span data-stu-id="778a5-142">Type and run the following command in the PMC:</span></span>

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```
* <span data-ttu-id="778a5-143">`Update-Package` コマンドを代わりに使用し、既にインストールされているパッケージを新しいバージョンに更新できます。</span><span class="sxs-lookup"><span data-stu-id="778a5-143">You can use the `Update-Package` command instead to update a package that is already installed to a more recent  version</span></span>

* <span data-ttu-id="778a5-144">特定のバージョンを指定するには、`-Version` 修飾子を使用できます。たとえば、EF Core 2.0 パッケージをインストールするには、コマンドに `-Version 2.0.0` を追加します。</span><span class="sxs-lookup"><span data-stu-id="778a5-144">To specify a specific version, you can use the `-Version` modifier, e.g. to install EF Core 2.0 packages, append `-Version 2.0.0` to the commands</span></span>

#### <a name="tools"></a><span data-ttu-id="778a5-145">ツール</span><span class="sxs-lookup"><span data-stu-id="778a5-145">Tools</span></span>

<span data-ttu-id="778a5-146">Visual Studio で [PMC 内で実行される EF Core コマンド](../../miscellaneous/cli/powershell.md)の PowerShell バージョンもあります。機能は `dotnet ef` コマンドと似ています。</span><span class="sxs-lookup"><span data-stu-id="778a5-146">There is also a PowerShell version of the [EF Core commands which run inside the PMC](../../miscellaneous/cli/powershell.md) in Visual Studio, with similar capabilities to the `dotnet ef` commands.</span></span> <span data-ttu-id="778a5-147">これらを使用するには、パッケージ マネージャー UI または PMC を利用して `Microsoft.EntityFrameworkCore.Tools` パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="778a5-147">In order to use these, install the `Microsoft.EntityFrameworkCore.Tools` package using either the Package Manager UI or the PMC.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="778a5-148">ランタイム パッケージのメジャー バージョンと一致するバージョンのツール パッケージを常に使用してください。</span><span class="sxs-lookup"><span data-stu-id="778a5-148">Always use versions of the tools packages that match the major version of the runtime packages.</span></span>

> [!TIP]  
> <span data-ttu-id="778a5-149">Visual Studio では PMC から `dotnet ef` コマンドを使用することができますが、PowerShell バージョンの使用がはるかに便利です。</span><span class="sxs-lookup"><span data-stu-id="778a5-149">Although it is possible to use the `dotnet ef` commands from the PMC in Visual Studio, it is far more convenient to use the PowerShell version:</span></span>
> * <span data-ttu-id="778a5-150">PMC で現在選択されているプロジェクトと自動的に連動します。ディレクトリを手動で切り替える必要がありません。</span><span class="sxs-lookup"><span data-stu-id="778a5-150">They automatically work with the current project selected in the PMC without requiring manually switching directories.</span></span>  
> * <span data-ttu-id="778a5-151">コマンドの完了後、Visual Studio で、コマンドによって生成されたファイルが自動的に開きます。</span><span class="sxs-lookup"><span data-stu-id="778a5-151">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="778a5-152">**EF Core 2.0 の非推奨のパッケージ:** 既存のアプリケーションを EF Core 2.0 にアップグレードするとき、場合によっては、古い EF Core パッケージの一部の参照を手動で削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="778a5-152">**Deprecated packages in EF Core 2.0:** If you are upgrading an existing application to EF Core 2.0, some references to older EF Core packages may need to be removed manually.</span></span> <span data-ttu-id="778a5-153">具体的には、`Microsoft.EntityFrameworkCore.SqlServer.Design` など、データベース プロバイダーのデザイン時パッケージは EF Core 2.0 で不要になりましたが (サポートがなくなりましたが)、他のパッケージのアップグレード時に自動的に削除されることはありません。</span><span class="sxs-lookup"><span data-stu-id="778a5-153">In particular, database provider design-time packages such as `Microsoft.EntityFrameworkCore.SqlServer.Design` are no longer required or supported in EF Core 2.0, but will not be automatically removed when upgrading the other packages.</span></span>
