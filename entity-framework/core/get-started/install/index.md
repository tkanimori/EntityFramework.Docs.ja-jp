---
title: EF Core のインストール
author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
uid: core/get-started/install/index
ms.openlocfilehash: 30ca81a0ede65506a6684d2322d31332115b1ed3
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996929"
---
# <a name="installing-ef-core"></a><span data-ttu-id="39a93-102">EF Core のインストール</span><span class="sxs-lookup"><span data-stu-id="39a93-102">Installing EF Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="39a93-103">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="39a93-103">Prerequisites</span></span>

<span data-ttu-id="39a93-104">.NET Core 2.1 アプリケーション (.NET Core を対象とする ASP.NET Core 2.1 アプリケーションを含む) を開発するには、お使いのプラットフォームに適したバージョンの [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core) をダウンロードし、インストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="39a93-104">In order to develop .NET Core 2.1 applications (including ASP.NET Core 2.1 applications that target .NET Core) you will need to download and install a version of the [.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core) that is appropriate to your platform.</span></span> <span data-ttu-id="39a93-105">**Visual Studio 2017 バージョン 15.7 をインストールしている場合でも必要です。**</span><span class="sxs-lookup"><span data-stu-id="39a93-105">**This is true even if you have installed Visual Studio 2017 version 15.7.**</span></span>

<span data-ttu-id="39a93-106">.NET Core 2.1 以外の .NET プラットフォーム (たとえば、.NET Framework 4.6.1 以降) で EF Core 2.1 やその他の .NET Standard 2.0 ライブラリを使用するには、.NET Standard 2.0 とその互換フレームワークを認識できる NuGet のバージョンが必要になります。</span><span class="sxs-lookup"><span data-stu-id="39a93-106">In order to use EF Core 2.1 or any other .NET Standard 2.0 library with a .NET platform besides .NET Core 2.1 (for example, with .NET Framework 4.6.1 or greater) you will need a version of NuGet that is aware of the .NET Standard 2.0 and its compatible frameworks.</span></span> <span data-ttu-id="39a93-107">次の方法で入手できます。</span><span class="sxs-lookup"><span data-stu-id="39a93-107">Here are a few ways you can obtain this:</span></span>

* <span data-ttu-id="39a93-108">Visual Studio 2017 バージョン 15.7 をインストールする</span><span class="sxs-lookup"><span data-stu-id="39a93-108">Install Visual Studio 2017 version 15.7</span></span>
* <span data-ttu-id="39a93-109">Visual Studio 2015 を使用している場合、[NuGet クライアント バージョン 3.6.0 をダウンロードするか、このバージョンにアップグレードします](https://www.nuget.org/downloads)。</span><span class="sxs-lookup"><span data-stu-id="39a93-109">If you are using Visual Studio 2015, [download and upgrade NuGet client to version 3.6.0](https://www.nuget.org/downloads)</span></span>

<span data-ttu-id="39a93-110">以前のバージョンの Visual Studio とその対象となる .NET Framework で作成されたプロジェクトの場合、.NET Standard 2.0 ライブラリ互換にするには追加の変更が必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="39a93-110">Projects created with previous versions of Visual Studio and targeting .NET Framework may need additional modifications in order to be compatible with .NET Standard 2.0 libraries:</span></span>

* <span data-ttu-id="39a93-111">プロジェクト ファイルを編集し、初期プロパティ グループに次のエントリが表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="39a93-111">Edit the project file and make sure the following entry appears in the initial property group:</span></span>
  ``` xml
  <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
  ```

* <span data-ttu-id="39a93-112">テスト プロジェクトの場合、次のエントリも必要です。</span><span class="sxs-lookup"><span data-stu-id="39a93-112">For test projects, also make sure the following entry is present:</span></span>
  ``` xml
  <GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
  ```

## <a name="getting-the-bits"></a><span data-ttu-id="39a93-113">ビットを取得する</span><span class="sxs-lookup"><span data-stu-id="39a93-113">Getting the bits</span></span>
<span data-ttu-id="39a93-114">EF Core ランタイム ライブラリをアプリケーションに追加するための推奨方法は、NuGet から EF Core データベース プロバイダーをインストールすることです。</span><span class="sxs-lookup"><span data-stu-id="39a93-114">The recommended way to add EF Core runtime libraries into an application is to install an EF Core database provider from NuGet.</span></span>

<span data-ttu-id="39a93-115">ランタイム ライブラリに加え、デザイン時にプロジェクトでいくつかの EF Core 関連タスクの実行を簡単にするツールをインストールできます。関連タスクには、移行の作成と適用、既存データベースに基づくモデルの作成などがあります。</span><span class="sxs-lookup"><span data-stu-id="39a93-115">Besides the runtime libraries, you can install tools which make it easier to perform several EF Core-related tasks in your project at design time, such as creating and applying migrations, and creating a model based on an existing database.</span></span>

> [!TIP]  
> <span data-ttu-id="39a93-116">サードパーティ データベース プロバイダーを使用するアプリケーションを更新する場合、使用するバージョンの EF Core との間で互換性があるプロバイダーの更新がないか、常に確認してください。</span><span class="sxs-lookup"><span data-stu-id="39a93-116">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="39a93-117">たとえば、前のバージョンのデータベース プロバイダーは EF Core ランタイムのバージョン 2.1 との間で互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="39a93-117">For example, database providers for previous versions are not compatible with version 2.1 of the EF Core runtime.</span></span>  

> [!TIP]  
> <span data-ttu-id="39a93-118">ASP.NET Core 2.1 を対象とするアプリケーションは、サードパーティ データベース プロバイダー以外の依存関係を追加せずに EF Core 2.1 を使用できます。</span><span class="sxs-lookup"><span data-stu-id="39a93-118">Applications targeting ASP.NET Core 2.1 can use EF Core 2.1 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="39a93-119">前バージョンの ASP.NET Core を対象とするアプリケーションで EF Core 2.1 を使用するには、ASP.NET Core 2.1 にアップグレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="39a93-119">Applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.1 in order to use EF Core 2.1.</span></span>

<a name="cli"></a>
### <a name="cross-platform-development-using-the-net-core-command-line-interface-cli"></a><span data-ttu-id="39a93-120">.NET Core コマンド ライン インターフェイス (CLI) を利用したクロスプラットフォーム開発</span><span class="sxs-lookup"><span data-stu-id="39a93-120">Cross-platform development using the .NET Core Command Line Interface (CLI)</span></span>

<span data-ttu-id="39a93-121">[.NET Core](https://www.microsoft.com/net/download/core) を対象とするアプリケーションを開発するには、お気に入りのテキスト エディターや、Visual Studio、Visual Studio for Mac、Visual Studio Code などの統合開発環境 (IDE) と組み合わせて [`dotnet` CLI コマンド](https://docs.microsoft.com/dotnet/core/tools/)を使用できます。</span><span class="sxs-lookup"><span data-stu-id="39a93-121">To develop applications that target [.NET Core](https://www.microsoft.com/net/download/core) you can choose to use the [`dotnet` CLI commands](https://docs.microsoft.com/dotnet/core/tools/) in combination with your favorite text editor, or an Integrated Development Environment (IDE) such as Visual Studio, Visual Studio for Mac, or Visual Studio Code.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="39a93-122">.NET Core をターゲットとするアプリケーションでは、特定のバージョンの Visual Studio が必要です。</span><span class="sxs-lookup"><span data-stu-id="39a93-122">Applications that target .NET Core require specific versions of Visual Studio.</span></span> <span data-ttu-id="39a93-123">たとえば、.NET Core 1.x の開発には Visual Studio 2017 が必要です。一方、.NET Core 2.1 の開発には Visual Studio 2017 バージョン 15.7 が必要です。</span><span class="sxs-lookup"><span data-stu-id="39a93-123">For example, .NET Core 1.x development requires Visual Studio 2017, while .NET Core 2.1 development requires Visual Studio 2017 version 15.7.</span></span>

<span data-ttu-id="39a93-124">クロスプラットフォーム .NET Core アプリケーションで SQL Server プロバイダーをインストールまたはアップグレードするには、アプリケーションのディレクトリに切り替え、コマンド ラインで次を実行します。</span><span class="sxs-lookup"><span data-stu-id="39a93-124">To install or upgrade the SQL Server provider in a cross-platform .NET Core application, switch to the application's directory and run the following in a command line:</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="39a93-125">`-v` 修飾子を利用し、`dotnet add package` コマンドで特定のバージョンのインストールを指定できます。</span><span class="sxs-lookup"><span data-stu-id="39a93-125">You can indicate a specific version install in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="39a93-126">たとえば、EF Core 2.1 パッケージをインストールするには、コマンドに `-v 2.1.0` を追加します。</span><span class="sxs-lookup"><span data-stu-id="39a93-126">For example, to install EF Core 2.1 packages, append `-v 2.1.0` to the command.</span></span>

<span data-ttu-id="39a93-127">EF Core には、[`dotnet` CLI の追加コマンド](../../miscellaneous/cli/dotnet.md)のセットが含まれており、その最初が `dotnet ef` です。</span><span class="sxs-lookup"><span data-stu-id="39a93-127">EF Core includes a set of [additional commands for the `dotnet` CLI](../../miscellaneous/cli/dotnet.md), starting with `dotnet ef`.</span></span> <span data-ttu-id="39a93-128">EF Core 向け .NET Core CLI ツールには、`Microsoft.EntityFrameworkCore.Design` というパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="39a93-128">The .NET Core CLI tools for EF Core require a package called `Microsoft.EntityFrameworkCore.Design`.</span></span> <span data-ttu-id="39a93-129">これは次のようにしてプロジェクトに追加できます。</span><span class="sxs-lookup"><span data-stu-id="39a93-129">You can add it to the project using:</span></span>

 ``` Console    
dotnet add package Microsoft.EntityFrameworkCore.Design 
``` 

> [!IMPORTANT]      
> <span data-ttu-id="39a93-130">ランタイム パッケージのメジャー バージョンと一致するバージョンのツール パッケージを常に使用してください。</span><span class="sxs-lookup"><span data-stu-id="39a93-130">Always use the version of the tools package that matches the major version of the runtime packages.</span></span>

<a name="visual-studio"></a>
### <a name="visual-studio-development"></a><span data-ttu-id="39a93-131">Visual Studio 開発</span><span class="sxs-lookup"><span data-stu-id="39a93-131">Visual Studio development</span></span>

<span data-ttu-id="39a93-132">.NET Core、.NET Framework、または EF Core でサポートされているその他のプラットフォームを対象とするさまざまなアプリケーションを Visual Studio で開発できます。</span><span class="sxs-lookup"><span data-stu-id="39a93-132">You can develop many different types of applications that target .NET Core, .NET Framework, or other platforms supported by EF Core using Visual Studio.</span></span>

<span data-ttu-id="39a93-133">Visual Studio からアプリケーションに EF Core データベース プロバイダーをインストールする方法が 2 つあります。</span><span class="sxs-lookup"><span data-stu-id="39a93-133">There are two ways you can install an EF Core database provider in your application from Visual Studio:</span></span>

#### <a name="using-nugets-package-manager-user-interfacehttpsdocsmicrosoftcomnugettoolspackage-manager-ui"></a><span data-ttu-id="39a93-134">NuGet の[パッケージ マネージャー ユーザー インターフェイス](https://docs.microsoft.com/nuget/tools/package-manager-ui)を使用する</span><span class="sxs-lookup"><span data-stu-id="39a93-134">Using NuGet's [Package Manager User Interface](https://docs.microsoft.com/nuget/tools/package-manager-ui)</span></span>

* <span data-ttu-id="39a93-135">メニューで **[プロジェクト]、[NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="39a93-135">Select on the menu **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="39a93-136">**[参照]** または **[更新]** タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="39a93-136">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="39a93-137">`Microsoft.EntityFrameworkCore.SqlServer` パッケージと希望のバージョンを選択し、確定します。</span><span class="sxs-lookup"><span data-stu-id="39a93-137">Select the `Microsoft.EntityFrameworkCore.SqlServer` package and the desired version and confirm</span></span>

#### <a name="using-nugets-package-manager-console-pmchttpsdocsmicrosoftcomnugettoolspackage-manager-console"></a><span data-ttu-id="39a93-138">NuGet の[パッケージ マネージャー コンソール (PMC)](https://docs.microsoft.com/nuget/tools/package-manager-console) を使用する</span><span class="sxs-lookup"><span data-stu-id="39a93-138">Using NuGet's [Package Manager Console (PMC)](https://docs.microsoft.com/nuget/tools/package-manager-console)</span></span>

* <span data-ttu-id="39a93-139">メニューで **[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="39a93-139">Select on the menu **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="39a93-140">PMC で次のコマンドを入力し、実行します。</span><span class="sxs-lookup"><span data-stu-id="39a93-140">Type and run the following command in the PMC:</span></span>

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```
* <span data-ttu-id="39a93-141">`Update-Package` コマンドを代わりに使用し、既にインストールされているパッケージを新しいバージョンに更新できます。</span><span class="sxs-lookup"><span data-stu-id="39a93-141">You can use the `Update-Package` command instead to update a package that is already installed to a more recent  version</span></span>

* <span data-ttu-id="39a93-142">特定のバージョンを指定するために、`-Version` 修飾子を使用できます。</span><span class="sxs-lookup"><span data-stu-id="39a93-142">To specify a specific version, you can use the `-Version` modifier.</span></span> <span data-ttu-id="39a93-143">たとえば、EF Core 2.1 パッケージをインストールするには、コマンドに `-Version 2.1.0` を追加します</span><span class="sxs-lookup"><span data-stu-id="39a93-143">For example, to install EF Core 2.1 packages, append `-Version 2.1.0` to the commands</span></span>

#### <a name="tools"></a><span data-ttu-id="39a93-144">ツール</span><span class="sxs-lookup"><span data-stu-id="39a93-144">Tools</span></span>

<span data-ttu-id="39a93-145">Visual Studio で [PMC 内で実行される EF Core コマンド](../../miscellaneous/cli/powershell.md)の PowerShell バージョンもあります。機能は `dotnet ef` コマンドと似ています。</span><span class="sxs-lookup"><span data-stu-id="39a93-145">There is also a PowerShell version of the [EF Core commands which run inside the PMC](../../miscellaneous/cli/powershell.md) in Visual Studio, with similar capabilities to the `dotnet ef` commands.</span></span> 

> [!TIP]  
> <span data-ttu-id="39a93-146">Visual Studio では PMC から `dotnet ef` コマンドを使用することができますが、PowerShell バージョンの使用がはるかに便利です。</span><span class="sxs-lookup"><span data-stu-id="39a93-146">Although it is possible to use the `dotnet ef` commands from the PMC in Visual Studio, it is far more convenient to use the PowerShell version:</span></span>
> * <span data-ttu-id="39a93-147">PMC で現在選択されているプロジェクトと自動的に連動します。ディレクトリを手動で切り替える必要がありません。</span><span class="sxs-lookup"><span data-stu-id="39a93-147">They automatically work with the current project selected in the PMC without requiring manually switching directories.</span></span>  
> * <span data-ttu-id="39a93-148">コマンドの完了後、Visual Studio で、コマンドによって生成されたファイルが自動的に開きます。</span><span class="sxs-lookup"><span data-stu-id="39a93-148">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="39a93-149">**EF Core 2.1 の非推奨のパッケージ:** 既存のアプリケーションを EF Core 2.1 にアップグレードするとき、場合によっては、古い EF Core パッケージの一部の参照を手動で削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="39a93-149">**Deprecated packages in EF Core 2.1:** If you're upgrading an existing application to EF Core 2.1, some references to older EF Core packages may need to be removed manually:</span></span>
> * <span data-ttu-id="39a93-150">データベース プロバイダーのデザイン時パッケージ (`Microsoft.EntityFrameworkCore.SqlServer.Design` など) は、EF Core 2.1 では不要になり、サポートされなくなりましたが、他のパッケージをアップグレードする際に自動で削除されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="39a93-150">Database provider design-time packages such as `Microsoft.EntityFrameworkCore.SqlServer.Design` are no longer required or supported in EF Core 2.1, but will not be automatically removed when upgrading the other packages.</span></span>
> * <span data-ttu-id="39a93-151">.NET CLI ツールが .NET SDK に含まれるようになりました。このため、*.csproj* ファイルからパッケージへの参照を削除することができます。</span><span class="sxs-lookup"><span data-stu-id="39a93-151">The .NET CLI tools are now included in the .NET SDK, so the reference to that package can be removed from the *.csproj* file:</span></span>
>   ```
>   <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
>   ```
