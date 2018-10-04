---
title: Entiy Framework Core のインストール
author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
uid: core/get-started/install/index
ms.openlocfilehash: 455eccbb149f0980cefa250ef5db443c73e66603
ms.sourcegitcommit: ae399f9f3d1bae2c446b552247bd3af3ca5a2cf9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2018
ms.locfileid: "48575640"
---
# <a name="installing-entity-framework-core"></a><span data-ttu-id="499c0-102">Entiy Framework Core のインストール</span><span class="sxs-lookup"><span data-stu-id="499c0-102">Installing Entity Framework Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="499c0-103">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="499c0-103">Prerequisites</span></span>

* <span data-ttu-id="499c0-104">.NET Core 2.1 を対象とするアプリを開発するには、[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core) をインストールします。</span><span class="sxs-lookup"><span data-stu-id="499c0-104">To develop apps that target .NET Core 2.1, install [the .NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span> <span data-ttu-id="499c0-105">SDK は、Visual Studio 2017 の最新バージョンがある場合でもインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="499c0-105">The SDK has to be installed even if you have the latest version of Visual Studio 2017.</span></span>

* <span data-ttu-id="499c0-106">.NET Core 2.1 を対象とするアプリの開発用 Visual Studio を使用するには、Visual Studio 2017 バージョン 15.7 以降をインストールします。</span><span class="sxs-lookup"><span data-stu-id="499c0-106">To use Visual Studio for development of apps that target .NET Core 2.1, install Visual Studio 2017 version 15.7 or later.</span></span>

* <span data-ttu-id="499c0-107">ASP.NET Core アプリケーションで Entity Framework 2.1 を使用するには、ASP.NET Core 2.1 を使用します。</span><span class="sxs-lookup"><span data-stu-id="499c0-107">To use Entity Framework 2.1 in ASP.NET Core applications, use ASP.NET Core 2.1.</span></span> <span data-ttu-id="499c0-108">以前のバージョンの ASP.NET Core を使用するアプリケーションは 2.1 に更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="499c0-108">Applications that use earlier versions of ASP.NET Core must be updated to 2.1.</span></span>

* <span data-ttu-id="499c0-109">Visual Studio 2015 は、.NET Framework 4.6.1 以降を対象とするアプリで使用することができます。</span><span class="sxs-lookup"><span data-stu-id="499c0-109">You can use Visual Studio 2015 for apps that target the .NET Framework 4.6.1 or later.</span></span> <span data-ttu-id="499c0-110">ただし .NET Standard 2.0 とその互換フレームワークを認識するバージョンの NuGet が必要です。</span><span class="sxs-lookup"><span data-stu-id="499c0-110">But you need a version of NuGet that is aware of the .NET Standard 2.0 and its compatible frameworks.</span></span> <span data-ttu-id="499c0-111">これを Visual Studio 2015 で取得するには、[NuGet クライアントをバージョン 3.6.0 にアップグレード](https://www.nuget.org/downloads)します。</span><span class="sxs-lookup"><span data-stu-id="499c0-111">To get that in Visual Studio 2015, [upgrade the NuGet client to version 3.6.0](https://www.nuget.org/downloads).</span></span>

## <a name="get-the-entity-framework-core-runtime"></a><span data-ttu-id="499c0-112">Entity Framework Core のランタイムを入手する</span><span class="sxs-lookup"><span data-stu-id="499c0-112">Get the Entity Framework Core runtime</span></span>

<span data-ttu-id="499c0-113">EF Core のランタイム ライブラリをアプリケーションに追加するには、使用するデータベース プロバイダーの NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="499c0-113">To add EF Core runtime libraries to an application, install the NuGet package for the database provider you want to use.</span></span> <span data-ttu-id="499c0-114">サポートされているプロバイダーとその NuGet パッケージ名の一覧は、「[データベース プロバイダー](../../providers/index.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="499c0-114">For a list of supported providers and their NuGet package names, see [Database providers](../../providers/index.md).</span></span>

<span data-ttu-id="499c0-115">NuGet パッケージをインストールまたは更新するには、.NET Core CLI、Visual Studio パッケージ マネージャー ダイアログ、または Visual Studio パッケージ マネージャー コンソールを使用します。</span><span class="sxs-lookup"><span data-stu-id="499c0-115">To install or update NuGet packages, use the .NET Core CLI, the Visual Studio Package Manager Dialog, or the Visual Studio Package Manager Console.</span></span>

<span data-ttu-id="499c0-116">ASP.NET Core 2.1 アプリケーションでは、メモリ内および SQL Server のプロバイダーが自動的に含まれるため、個別にインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="499c0-116">For ASP.NET Core 2.1 applications, the in-memory and SQL Server providers are automatically included, so there's no need to install them separately.</span></span>

> [!TIP]  
> <span data-ttu-id="499c0-117">サードパーティ データベース プロバイダーを使用するアプリケーションを更新する場合、使用するバージョンの EF Core との間で互換性があるプロバイダーの更新がないか、常に確認してください。</span><span class="sxs-lookup"><span data-stu-id="499c0-117">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="499c0-118">たとえば、前のバージョンのデータベース プロバイダーは EF Core ランタイムのバージョン 2.1 との間で互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="499c0-118">For example, database providers for previous versions are not compatible with version 2.1 of the EF Core runtime.</span></span>  

### <a name="net-core-cli"></a><span data-ttu-id="499c0-119">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="499c0-119">.NET Core CLI</span></span>

<span data-ttu-id="499c0-120">次の .NET Core CLI コマンドでは、SQL Server プロバイダーがインストールまたは更新されます。</span><span class="sxs-lookup"><span data-stu-id="499c0-120">The following .NET Core CLI command installs or updates the SQL Server provider:</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="499c0-121">`-v` 修飾子を利用し、`dotnet add package` コマンドで特定のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="499c0-121">You can indicate a specific version in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="499c0-122">たとえば、EF Core 2.1.0 パッケージをインストールするには、コマンドに `-v 2.1.0` を追加します。</span><span class="sxs-lookup"><span data-stu-id="499c0-122">For example, to install EF Core 2.1.0 packages, append `-v 2.1.0` to the command.</span></span>

### <a name="visual-studio-nuget-package-manager-dialog"></a><span data-ttu-id="499c0-123">Visual Studio の NuGet パッケージ マネージャー ダイアログ</span><span class="sxs-lookup"><span data-stu-id="499c0-123">Visual Studio NuGet Package Manager Dialog</span></span>

* <span data-ttu-id="499c0-124">メニューから **[プロジェクト]、[NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="499c0-124">From the menu, select **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="499c0-125">**[参照]** または **[更新]** タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="499c0-125">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="499c0-126">SQL Server プロバイダーをインストールまたは更新するには、`Microsoft.EntityFrameworkCore.SqlServer` パッケージを選択して確定します。</span><span class="sxs-lookup"><span data-stu-id="499c0-126">To install or update the SQL Server provider, select the `Microsoft.EntityFrameworkCore.SqlServer` package, and confirm.</span></span>

<span data-ttu-id="499c0-127">詳細については、「[NuGet パッケージ マネージャー UI](https://docs.microsoft.com/nuget/tools/package-manager-ui)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="499c0-127">For more information, see [NuGet Package Manager Dialog](https://docs.microsoft.com/nuget/tools/package-manager-ui).</span></span>

### <a name="visual-studio-nuget-package-manager-console"></a><span data-ttu-id="499c0-128">Visual Studio の NuGet パッケージ マネージャー コンソール</span><span class="sxs-lookup"><span data-stu-id="499c0-128">Visual Studio NuGet Package Manager Console</span></span>

* <span data-ttu-id="499c0-129">メニューから **[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します</span><span class="sxs-lookup"><span data-stu-id="499c0-129">From the menu, select **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="499c0-130">SQL Server プロバイダーをインストールするには、パッケージ マネージャー コンソールで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="499c0-130">To install the SQL Server provider, run the following command in the Package Manager Console:</span></span>

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```
* <span data-ttu-id="499c0-131">プロバイダーを更新するには、`Update-Package` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="499c0-131">To update the provider, use the `Update-Package` command.</span></span>

* <span data-ttu-id="499c0-132">特定のバージョンを指定するには、`-Version` 修飾子を使用します。</span><span class="sxs-lookup"><span data-stu-id="499c0-132">To specify a specific version, use the `-Version` modifier.</span></span> <span data-ttu-id="499c0-133">たとえば、EF Core 2.1.0 パッケージをインストールするには、コマンドに `-Version 2.1.0` を追加します</span><span class="sxs-lookup"><span data-stu-id="499c0-133">For example, to install EF Core 2.1.0 packages, append `-Version 2.1.0` to the commands</span></span>

<span data-ttu-id="499c0-134">詳細については、「[パッケージ マネージャー コンソール](https://docs.microsoft.com/nuget/tools/package-manager-console)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="499c0-134">For more information, see [Package Manager Console](https://docs.microsoft.com/nuget/tools/package-manager-console).</span></span>

## <a name="get-entity-framework-core-tools"></a><span data-ttu-id="499c0-135">Entity Framework Core ツールを入手する</span><span class="sxs-lookup"><span data-stu-id="499c0-135">Get Entity Framework Core tools</span></span>

<span data-ttu-id="499c0-136">設計時には、ランタイム ライブラリだけでなく、プロジェクトでいくつかの EF Core 関連タスクを実行できるツールをインストールできます。</span><span class="sxs-lookup"><span data-stu-id="499c0-136">Besides the runtime libraries, you can install tools that can perform some EF Core-related tasks in your project at design time.</span></span> <span data-ttu-id="499c0-137">たとえば、移行の作成、移行の適用、既存のデータベースに基づくモデルの作成などができます。</span><span class="sxs-lookup"><span data-stu-id="499c0-137">For example, you can create migrations, apply migrations, and create a model based on an existing database.</span></span>

<span data-ttu-id="499c0-138">2 つのツールのセットを使用できます。</span><span class="sxs-lookup"><span data-stu-id="499c0-138">Two sets of tools are available:</span></span>
* <span data-ttu-id="499c0-139">.NET Core の[コマンド ライン インターフェイス (CLI) ツール](../../miscellaneous/cli/dotnet.md)は、Windows、Linux、または macOS で使用できます。</span><span class="sxs-lookup"><span data-stu-id="499c0-139">The .NET Core [command-line interface (CLI) tools](../../miscellaneous/cli/dotnet.md) can be used on Windows, Linux, or macOS.</span></span> <span data-ttu-id="499c0-140">これらのコマンドは `dotnet ef` で始まります。</span><span class="sxs-lookup"><span data-stu-id="499c0-140">These commands begin with `dotnet ef`.</span></span> 
* <span data-ttu-id="499c0-141">[パッケージ マネージャー コンソール ツール](../../miscellaneous/cli/powershell.md)は、Windows 上の Visual Studio 2017 で実行されます。</span><span class="sxs-lookup"><span data-stu-id="499c0-141">The [Package Manager Console tools](../../miscellaneous/cli/powershell.md)  run in Visual Studio 2017 on Windows.</span></span> <span data-ttu-id="499c0-142">これらのコマンドは `Add-Migration`、`Update-Database` のように動詞で始まります。</span><span class="sxs-lookup"><span data-stu-id="499c0-142">These commands start with a verb, for example `Add-Migration`, `Update-Database`.</span></span>

<span data-ttu-id="499c0-143">パッケージ マネージャー コンソールの `dotnet ef` コマンドを使用できますが、Visual Studio を使用しているときにはパッケージ マネージャー コンソールのツールを使用する方が便利です。</span><span class="sxs-lookup"><span data-stu-id="499c0-143">Although you can use the `dotnet ef` commands from the Package Manager Console, it's more convenient to use the Package Manager Console tools when you're using Visual Studio:</span></span>
* <span data-ttu-id="499c0-144">パッケージ マネージャー コンソールで現在選択されているプロジェクトと自動的に連動します。ディレクトリを手動で切り替える必要がありません。</span><span class="sxs-lookup"><span data-stu-id="499c0-144">They automatically work with the current project selected in the Package Manager Console without requiring manually switching directories.</span></span>  
* <span data-ttu-id="499c0-145">コマンドの完了後、Visual Studio で、コマンドによって生成されたファイルが自動的に開きます。</span><span class="sxs-lookup"><span data-stu-id="499c0-145">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

<a name="cli"></a>

### <a name="get-the-cli-tools"></a><span data-ttu-id="499c0-146">CLI ツールを入手する</span><span class="sxs-lookup"><span data-stu-id="499c0-146">Get the CLI tools</span></span>

<span data-ttu-id="499c0-147">.NET Core SDK には `dotnet ef` コマンドが含まれていますが、コマンドを有効にするには、`Microsoft.EntityFrameworkCore.Design` パッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="499c0-147">The `dotnet ef` commands are included in the .NET Core SDK, but to enable the commands you have to install the `Microsoft.EntityFrameworkCore.Design` package:</span></span>

 ``` Console    
dotnet add package Microsoft.EntityFrameworkCore.Design 
``` 

<span data-ttu-id="499c0-148">ASP.NET Core 2.1 アプリでは、このパッケージが自動的に含まれています。</span><span class="sxs-lookup"><span data-stu-id="499c0-148">For ASP.NET Core 2.1 apps, this package is included automatically.</span></span>

<span data-ttu-id="499c0-149">[必須コンポーネント](#prerequisites)で説明したように、.NET Core 2.1 SDK もインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="499c0-149">As explained earlier in [Prerequisites](#prerequisites), you also need to install the .NET Core 2.1 SDK.</span></span>

> [!IMPORTANT]      
> <span data-ttu-id="499c0-150">ランタイム パッケージのメジャー バージョンと一致するバージョンのツール パッケージを常に使用してください。</span><span class="sxs-lookup"><span data-stu-id="499c0-150">Always use the version of the tools package that matches the major version of the runtime packages.</span></span>

### <a name="get-the-package-manager-console-tools"></a><span data-ttu-id="499c0-151">パッケージ マネージャー コンソール ツールを入手する</span><span class="sxs-lookup"><span data-stu-id="499c0-151">Get the Package Manager Console tools</span></span>

<span data-ttu-id="499c0-152">EF Core のパッケージ マネージャー コンソール ツールを入手するには、`Microsoft.EntityFrameworkCore.Tools` パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="499c0-152">To get the Package Manager Console tools for EF Core, install the `Microsoft.EntityFrameworkCore.Tools` package:</span></span>

 ``` Console    
dotnet add package Microsoft.EntityFrameworkCore.Tools
``` 

<span data-ttu-id="499c0-153">ASP.NET Core 2.1 アプリでは、このパッケージが自動的に含まれています。</span><span class="sxs-lookup"><span data-stu-id="499c0-153">For ASP.NET Core 2.1 apps, this package is included automatically.</span></span>

## <a name="upgrading-to-ef-core-21"></a><span data-ttu-id="499c0-154">EF Core 2.1 へのアップグレード</span><span class="sxs-lookup"><span data-stu-id="499c0-154">Upgrading to EF Core 2.1</span></span>

<span data-ttu-id="499c0-155">既存のアプリケーションを EF Core 2.1 にアップグレードするとき、場合によっては、古い EF Core パッケージの一部の参照を手動で削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="499c0-155">If you're upgrading an existing application to EF Core 2.1, some references to older EF Core packages may need to be removed manually:</span></span>

* <span data-ttu-id="499c0-156">データベース プロバイダーのデザイン時パッケージ (`Microsoft.EntityFrameworkCore.SqlServer.Design` など) は、EF Core 2.1 では不要になったか、またはサポートされなくなりましたが、他のパッケージをアップグレードする際に自動で削除されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="499c0-156">Database provider design-time packages such as `Microsoft.EntityFrameworkCore.SqlServer.Design` are no longer required or supported in EF Core 2.1, but aren't automatically removed when upgrading the other packages.</span></span>

* <span data-ttu-id="499c0-157">.NET CLI ツールが .NET SDK に含まれるようになりました。このため、*.csproj* ファイルからパッケージへの参照を削除することができます。</span><span class="sxs-lookup"><span data-stu-id="499c0-157">The .NET CLI tools are now included in the .NET SDK, so the reference to that package can be removed from the *.csproj* file:</span></span>

  ```
  <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
  ```

<span data-ttu-id="499c0-158">.NET Framework を対象とする、以前のバージョンの Visual Studio で作成されたアプリケーションの場合、.NET Standard 2.0 ライブラリと互換性があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="499c0-158">For applications that target the .NET Framework and were created by earlier versions of Visual Studio, make sure that they are compatible with .NET Standard 2.0 libraries:</span></span>

  * <span data-ttu-id="499c0-159">プロジェクト ファイルを編集し、初期プロパティ グループに次のエントリが表示されるようにします。</span><span class="sxs-lookup"><span data-stu-id="499c0-159">Edit the project file and make sure the following entry appears in the initial property group:</span></span>

    ``` xml
    <AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
    ```

  * <span data-ttu-id="499c0-160">テスト プロジェクトの場合、次のエントリも必要です。</span><span class="sxs-lookup"><span data-stu-id="499c0-160">For test projects, also make sure the following entry is present:</span></span>

    ``` xml
    <GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
    ```
