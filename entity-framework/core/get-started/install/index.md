---
title: Entiy Framework Core のインストール - EF Core
author: divega
ms.date: 08/06/2017
ms.assetid: 608cc774-c570-4809-8a3e-cd2c8446b8b2
uid: core/get-started/install/index
ms.openlocfilehash: 6575b1ac028f8b67b49ca7f4e49d6f19500be98f
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "80136179"
---
# <a name="installing-entity-framework-core"></a><span data-ttu-id="fdb07-102">Entiy Framework Core のインストール</span><span class="sxs-lookup"><span data-stu-id="fdb07-102">Installing Entity Framework Core</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fdb07-103">前提条件</span><span class="sxs-lookup"><span data-stu-id="fdb07-103">Prerequisites</span></span>

* <span data-ttu-id="fdb07-104">EF Core は [.NET Standard 2.0](/dotnet/standard/net-standard) ライブラリの 1 つです。</span><span class="sxs-lookup"><span data-stu-id="fdb07-104">EF Core is a [.NET Standard 2.0](/dotnet/standard/net-standard) library.</span></span> <span data-ttu-id="fdb07-105">したがって、EF Core が動作するには .NET Standard 2.0 をサポートする .NET 実装が必要です。</span><span class="sxs-lookup"><span data-stu-id="fdb07-105">So EF Core requires a .NET implementation that supports .NET Standard 2.0 to run.</span></span> <span data-ttu-id="fdb07-106">EF Core は他の .NET Standard 2.0 ライブラリから参照することもできます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-106">EF Core can also be referenced by other .NET Standard 2.0 libraries.</span></span>

* <span data-ttu-id="fdb07-107">たとえば、.NET Core をターゲットとするアプリを EF Core を使用して開発することができます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-107">For example, you can use EF Core to develop apps that target .NET Core.</span></span> <span data-ttu-id="fdb07-108">.NET Core アプリを構築するには、[.NET Core SDK](https://dotnet.microsoft.com/download) が必要です。</span><span class="sxs-lookup"><span data-stu-id="fdb07-108">Building .NET Core apps requires the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="fdb07-109">必要に応じて、[Visual Studio](https://visualstudio.microsoft.com/vs)、[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac)、[Visual Studio Code](https://code.visualstudio.com) などの開発環境を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-109">Optionally, you can also use a development environment like [Visual Studio](https://visualstudio.microsoft.com/vs), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac), or [Visual Studio Code](https://code.visualstudio.com).</span></span> <span data-ttu-id="fdb07-110">詳しくは、「[.NET Core の概要](/dotnet/core/get-started)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="fdb07-110">For more information, check [Getting Started with .NET Core](/dotnet/core/get-started).</span></span>

* <span data-ttu-id="fdb07-111">EF Core を使用すると、Windows 上で Visual Studio を使用してアプリケーションを開発できます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-111">You can use EF Core to develop applications on Windows using Visual Studio.</span></span> <span data-ttu-id="fdb07-112">[Visual Studio](https://visualstudio.microsoft.com/vs) の最新バージョンをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fdb07-112">The latest version of [Visual Studio](https://visualstudio.microsoft.com/vs) is recommended.</span></span>

* <span data-ttu-id="fdb07-113">EF Core を他の .NET 実装 (たとえば [Xamarin](https://dotnet.microsoft.com/apps/xamarin) や .NET Native) 上で実行することができます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-113">EF Core can run on other .NET implementations like [Xamarin](https://dotnet.microsoft.com/apps/xamarin) and .NET Native.</span></span> <span data-ttu-id="fdb07-114">ただし、実際にはそのような実装には実行時の制約があるため、開発したアプリでの EF Core の動作に影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fdb07-114">But in practice those implementations have runtime limitations that may affect how well EF Core works on your app.</span></span> <span data-ttu-id="fdb07-115">詳しくは、「[EF Core でサポートされている .NET 実装](xref:core/platforms/index)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="fdb07-115">For more information, see [.NET implementations supported by EF Core](xref:core/platforms/index).</span></span>

* <span data-ttu-id="fdb07-116">データベース プロバイダーによっては、特定のデータベース エンジン バージョン、.NET 実装、またはオペレーティング システムが要求される場合があります。</span><span class="sxs-lookup"><span data-stu-id="fdb07-116">Finally, different database providers may require specific database engine versions, .NET implementations, or operating systems.</span></span> <span data-ttu-id="fdb07-117">開発するアプリケーションに適した環境をサポートする [EF Core データベース プロバイダー](xref:core/providers/index)があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="fdb07-117">Make sure an [EF Core database provider](xref:core/providers/index) is available that supports the right environment for your application.</span></span>

## <a name="get-the-entity-framework-core-runtime"></a><span data-ttu-id="fdb07-118">Entity Framework Core のランタイムを入手する</span><span class="sxs-lookup"><span data-stu-id="fdb07-118">Get the Entity Framework Core runtime</span></span>

<span data-ttu-id="fdb07-119">EF Core をアプリケーションに追加するには、使用するデータベース プロバイダーに対応した NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="fdb07-119">To add EF Core to an application, install the NuGet package for the database provider you want to use.</span></span>

<span data-ttu-id="fdb07-120">ASP.NET Core アプリケーションを構築する場合は、インメモリや SQL Server のプロバイダーをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="fdb07-120">If you're building an ASP.NET Core application, you don't need to install the in-memory and SQL Server providers.</span></span> <span data-ttu-id="fdb07-121">これらのプロバイダーは、現在のバージョンの ASP.NET Core に、EF Core ランタイムとともに含まれています。</span><span class="sxs-lookup"><span data-stu-id="fdb07-121">Those providers are included in current versions of ASP.NET Core, alongside the EF Core runtime.</span></span>  

<span data-ttu-id="fdb07-122">NuGet パッケージをインストールまたは更新するには、.NET Core コマンド ライン インターフェイス (CLI)、Visual Studio パッケージ マネージャー ダイアログ、または Visual Studio パッケージ マネージャー コンソールを使用できます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-122">To install or update NuGet packages, you can use the .NET Core command-line interface (CLI), the Visual Studio Package Manager Dialog, or the Visual Studio Package Manager Console.</span></span>

### <a name="net-core-cli"></a><span data-ttu-id="fdb07-123">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="fdb07-123">.NET Core CLI</span></span>

* <span data-ttu-id="fdb07-124">次の .NET Core CLI コマンドをオペレーティング システムのコマンド ラインで使用して EF Core SQL Server プロバイダーのインストールまたは更新を行います。</span><span class="sxs-lookup"><span data-stu-id="fdb07-124">Use the following .NET Core CLI command from the operating system's command line to install or update the EF Core SQL Server provider:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="fdb07-125">`dotnet add package` 修飾子を利用し、`-v` コマンドで特定のバージョンを指定できます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-125">You can indicate a specific version in the `dotnet add package` command, using the `-v` modifier.</span></span> <span data-ttu-id="fdb07-126">たとえば、EF Core 2.2.0 のパッケージをインストールするには、コマンドの末尾に `-v 2.2.0` を追加します。</span><span class="sxs-lookup"><span data-stu-id="fdb07-126">For example, to install EF Core 2.2.0 packages, append `-v 2.2.0` to the command.</span></span>

<span data-ttu-id="fdb07-127">詳細については、「[.NET コマンド ライン インターフェイス (CLI) ツール](/dotnet/core/tools/)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="fdb07-127">For more information, see [.NET command-line interface (CLI) tools](/dotnet/core/tools/).</span></span>

### <a name="visual-studio-nuget-package-manager-dialog"></a><span data-ttu-id="fdb07-128">Visual Studio の NuGet パッケージ マネージャー ダイアログ</span><span class="sxs-lookup"><span data-stu-id="fdb07-128">Visual Studio NuGet Package Manager Dialog</span></span>

* <span data-ttu-id="fdb07-129">Visual Studio のメニューから **[プロジェクト]、[NuGet パッケージの管理]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="fdb07-129">From the Visual Studio menu, select **Project > Manage NuGet Packages**</span></span>

* <span data-ttu-id="fdb07-130">**[参照]** または **[更新]** タブをクリックします。</span><span class="sxs-lookup"><span data-stu-id="fdb07-130">Click on the **Browse** or the **Updates** tab</span></span>

* <span data-ttu-id="fdb07-131">SQL Server プロバイダーをインストールまたは更新するには、`Microsoft.EntityFrameworkCore.SqlServer` パッケージを選択して確定します。</span><span class="sxs-lookup"><span data-stu-id="fdb07-131">To install or update the SQL Server provider, select the `Microsoft.EntityFrameworkCore.SqlServer` package, and confirm.</span></span>

<span data-ttu-id="fdb07-132">詳細については、「[NuGet パッケージ マネージャー UI](/nuget/tools/package-manager-ui)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fdb07-132">For more information, see [NuGet Package Manager Dialog](/nuget/tools/package-manager-ui).</span></span>

### <a name="visual-studio-nuget-package-manager-console"></a><span data-ttu-id="fdb07-133">Visual Studio の NuGet パッケージ マネージャー コンソール</span><span class="sxs-lookup"><span data-stu-id="fdb07-133">Visual Studio NuGet Package Manager Console</span></span>

* <span data-ttu-id="fdb07-134">Visual Studio のメニューから **[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="fdb07-134">From the Visual Studio menu, select **Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="fdb07-135">SQL Server プロバイダーをインストールするには、パッケージ マネージャー コンソールで、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="fdb07-135">To install the SQL Server provider, run the following command in the Package Manager Console:</span></span>

  ``` PowerShell  
  Install-Package Microsoft.EntityFrameworkCore.SqlServer
  ```

* <span data-ttu-id="fdb07-136">プロバイダーを更新するには、`Update-Package` コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="fdb07-136">To update the provider, use the `Update-Package` command.</span></span>

* <span data-ttu-id="fdb07-137">特定のバージョンを指定するには、`-Version` 修飾子を使用します。</span><span class="sxs-lookup"><span data-stu-id="fdb07-137">To specify a specific version, use the `-Version` modifier.</span></span> <span data-ttu-id="fdb07-138">たとえば、EF Core 2.2.0 のパッケージをインストールするには、コマンドの末尾に `-Version 2.2.0` を追加します。</span><span class="sxs-lookup"><span data-stu-id="fdb07-138">For example, to install EF Core 2.2.0 packages, append `-Version 2.2.0` to the commands</span></span>

<span data-ttu-id="fdb07-139">詳細については、「[パッケージ マネージャー コンソール](/nuget/tools/package-manager-console)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fdb07-139">For more information, see [Package Manager Console](/nuget/tools/package-manager-console).</span></span>

## <a name="get-the-entity-framework-core-tools"></a><span data-ttu-id="fdb07-140">Entity Framework Core のツールを入手する</span><span class="sxs-lookup"><span data-stu-id="fdb07-140">Get the Entity Framework Core tools</span></span>

<span data-ttu-id="fdb07-141">EF Core 関連のタスクをプロジェクト内で実行するためのツールをインストールできます。たとえば、データベース移行を作成して適用することや、EF Core モデルを既存のデータベースに基づいて作成することができます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-141">You can install tools to carry out EF Core-related tasks in your project, like creating and applying database migrations, or creating an EF Core model based on an existing database.</span></span>

<span data-ttu-id="fdb07-142">2 つのツールのセットを使用できます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-142">Two sets of tools are available:</span></span>

* <span data-ttu-id="fdb07-143">[.NET Core コマンド ライン インターフェイス (CLI) ツール](xref:core/miscellaneous/cli/dotnet)は、Windows、Linux、macOS で使用できます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-143">The [.NET Core command-line interface (CLI) tools](xref:core/miscellaneous/cli/dotnet) can be used on Windows, Linux, or macOS.</span></span> <span data-ttu-id="fdb07-144">これらのコマンドは `dotnet ef` で始まります。</span><span class="sxs-lookup"><span data-stu-id="fdb07-144">These commands begin with `dotnet ef`.</span></span>

* <span data-ttu-id="fdb07-145">[パッケージ マネージャー コンソール (PMC) ツール](xref:core/miscellaneous/cli/powershell)は、Windows 上の Visual Studio の中で動作します。</span><span class="sxs-lookup"><span data-stu-id="fdb07-145">The [Package Manager Console (PMC) tools](xref:core/miscellaneous/cli/powershell) run in Visual Studio on Windows.</span></span> <span data-ttu-id="fdb07-146">これらのコマンドは `Add-Migration`、`Update-Database` のように動詞で始まります。</span><span class="sxs-lookup"><span data-stu-id="fdb07-146">These commands start with a verb, for example `Add-Migration`, `Update-Database`.</span></span>

<span data-ttu-id="fdb07-147">`dotnet ef` コマンドをパッケージ マネージャー コンソールで使用することもできますが、Visual Studio を使用しているときは、次のような理由からパッケージ マネージャー コンソールのツールを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fdb07-147">Although you can also use the `dotnet ef` commands from the Package Manager Console, it's recommended to use the Package Manager Console tools when you're using Visual Studio:</span></span>

* <span data-ttu-id="fdb07-148">Visual Studio の PMC で現在選択されているプロジェクトと自動的に連動します。ディレクトリを手動で切り替える必要がありません。</span><span class="sxs-lookup"><span data-stu-id="fdb07-148">They automatically work with the current project selected in the PMC in Visual Studio, without requiring manually switching directories.</span></span>  

* <span data-ttu-id="fdb07-149">コマンドの完了後、Visual Studio で、コマンドによって生成されたファイルが自動的に開きます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-149">They automatically open files generated by the commands in Visual Studio after the command is completed.</span></span>

<a name="cli"></a>

### <a name="get-the-net-core-cli-tools"></a><span data-ttu-id="fdb07-150">.NET Core CLI ツールを入手する</span><span class="sxs-lookup"><span data-stu-id="fdb07-150">Get the .NET Core CLI tools</span></span>

<span data-ttu-id="fdb07-151">.NET Core CLI ツールを使用するには、「[必須コンポーネント](#prerequisites)」で前述したとおり .NET Core SDK が必要です。</span><span class="sxs-lookup"><span data-stu-id="fdb07-151">.NET Core CLI tools require the .NET Core SDK, mentioned earlier in [Prerequisites](#prerequisites).</span></span>

<span data-ttu-id="fdb07-152">`dotnet ef` のコマンドは現在のバージョンの .NET Core SDK に含まれていますが、このコマンドを特定のプロジェクトで使用できるようにするには、次の方法で `Microsoft.EntityFrameworkCore.Design` パッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fdb07-152">The `dotnet ef` commands are included in current versions of the .NET Core SDK, but to enable the commands on a specific project, you have to install the `Microsoft.EntityFrameworkCore.Design` package:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Design
```

> [!IMPORTANT]
> <span data-ttu-id="fdb07-153">ランタイム パッケージのメジャー バージョンと一致するバージョンのツール パッケージを常に使用してください。</span><span class="sxs-lookup"><span data-stu-id="fdb07-153">Always use the version of the tools package that matches the major version of the runtime packages.</span></span>

### <a name="get-the-package-manager-console-tools"></a><span data-ttu-id="fdb07-154">パッケージ マネージャー コンソール ツールを入手する</span><span class="sxs-lookup"><span data-stu-id="fdb07-154">Get the Package Manager Console tools</span></span>

<span data-ttu-id="fdb07-155">EF Core 用のパッケージ マネージャー コンソール ツールを入手するには、`Microsoft.EntityFrameworkCore.Tools` パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="fdb07-155">To get the Package Manager Console tools for EF Core, install the `Microsoft.EntityFrameworkCore.Tools` package.</span></span> <span data-ttu-id="fdb07-156">たとえば、Visual Studio で次のようにします。</span><span class="sxs-lookup"><span data-stu-id="fdb07-156">For example, from Visual Studio:</span></span>

``` PowerShell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="fdb07-157">ASP.NET Core アプリの場合は、このパッケージが自動的に組み込まれます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-157">For ASP.NET Core apps, this package is included automatically.</span></span>

## <a name="upgrading-to-the-latest-ef-core"></a><span data-ttu-id="fdb07-158">最新の EF Core にアップグレードする</span><span class="sxs-lookup"><span data-stu-id="fdb07-158">Upgrading to the latest EF Core</span></span>

* <span data-ttu-id="fdb07-159">新しいバージョンの EF Core がリリースされると、EF Core プロジェクトの一部であるプロバイダー (Microsoft.EntityFrameworkCore.SqlServer、Microsoft.EntityFrameworkCore.Sqlite、Microsoft.EntityFrameworkCore.InMemory など) の新しいバージョンもリリースされます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-159">Any time we release a new version of EF Core, we also release a new version of the providers that are part of the EF Core project, like Microsoft.EntityFrameworkCore.SqlServer, Microsoft.EntityFrameworkCore.Sqlite, and Microsoft.EntityFrameworkCore.InMemory.</span></span> <span data-ttu-id="fdb07-160">新しいバージョンのプロバイダーにアップグレードするだけで、すべての機能強化を入手できます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-160">You can just upgrade to the new version of the provider to get all the improvements.</span></span>

* <span data-ttu-id="fdb07-161">EF Core は、SQL Server やインメモリのプロバイダーとともに現在のバージョンの ASP.NET Core に含まれています。</span><span class="sxs-lookup"><span data-stu-id="fdb07-161">EF Core, together with the SQL Server and the in-memory providers are included in current versions of ASP.NET Core.</span></span> <span data-ttu-id="fdb07-162">既存の ASP.NET Core アプリケーションを新しいバージョンの EF Core に合わせてアップグレードするには、必ず ASP.NET Core のバージョンをアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="fdb07-162">To upgrade an existing ASP.NET Core application to a newer version of EF Core, always upgrade the version of ASP.NET Core.</span></span>

* <span data-ttu-id="fdb07-163">サードパーティ データベース プロバイダーを使用するアプリケーションを更新する場合、使用するバージョンの EF Core との間で互換性があるプロバイダーの更新がないか、常に確認してください。</span><span class="sxs-lookup"><span data-stu-id="fdb07-163">If you need to update an application that is using a third-party database provider, always check for an update of the provider that is compatible with the version of EF Core you want to use.</span></span> <span data-ttu-id="fdb07-164">たとえば、前のバージョンのデータベース プロバイダーは EF Core ランタイムのバージョン 2.0 との間で互換性がありません。</span><span class="sxs-lookup"><span data-stu-id="fdb07-164">For example, database providers for previous versions are not compatible with version 2.0 of the EF Core runtime.</span></span>

* <span data-ttu-id="fdb07-165">EF Core 用のサードパーティ プロバイダーは通常、EF Core ランタイと並行してパッチ バージョンをリリースすることはありません。</span><span class="sxs-lookup"><span data-stu-id="fdb07-165">Third-party providers for EF Core usually don't release patch versions alongside the EF Core runtime.</span></span> <span data-ttu-id="fdb07-166">サードパーティ プロバイダーを使用するアプリケーションを EF Core のパッチ バージョンに合わせてアップグレードするには、個々の EF Core ランタイム コンポーネント (たとえば Microsoft.EntityFrameworkCore や Microsoft.EntityFrameworkCore.Relational) への直接の参照を追加することが必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fdb07-166">To upgrade an application that uses a third-party provider to a patch version of EF Core, you may need to add a direct reference to individual EF Core runtime components, such as Microsoft.EntityFrameworkCore, and Microsoft.EntityFrameworkCore.Relational.</span></span>

* <span data-ttu-id="fdb07-167">既存のアプリケーションを最新バージョンの EF Core に合わせてアップグレードする場合は、古い EF Core パッケージの参照を手動で削除することが必要になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fdb07-167">If you're upgrading an existing application to the latest version of EF Core, some references to older EF Core packages may need to be removed manually:</span></span>

  * <span data-ttu-id="fdb07-168">データベース プロバイダーのデザイン時パッケージ (たとえば `Microsoft.EntityFrameworkCore.SqlServer.Design`) は、EF Core 2.0 からは不要になり、サポートもされなくなりましたが、他のパッケージをアップグレードするときに自動で削除されることはありません。</span><span class="sxs-lookup"><span data-stu-id="fdb07-168">Database provider design-time packages such as `Microsoft.EntityFrameworkCore.SqlServer.Design` are no longer required or supported from EF Core 2.0 and later, but aren't automatically removed when upgrading the other packages.</span></span>

  * <span data-ttu-id="fdb07-169">.NET CLI ツールがバージョン 2.1 以降の .NET SDK に含まれているので、そのパッケージへの参照は次の方法でプロジェクト ファイルから削除できます。</span><span class="sxs-lookup"><span data-stu-id="fdb07-169">The .NET CLI tools are included in the .NET SDK since version 2.1, so the reference to that package can be removed from the project file:</span></span>

    ``` xml
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
    ```
