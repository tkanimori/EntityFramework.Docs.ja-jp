---
title: Entity Framework の EF6 を取得します。
author: divega
ms.date: 2016-10-23
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
ms.openlocfilehash: 91b78e56f60edf7ebc8769b1c385f8547f63cd3d
ms.sourcegitcommit: 0cef7d448e1e47bdb333002e2254ed42d57b45b6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43152389"
---
# <a name="get-entity-framework"></a><span data-ttu-id="0db75-102">Entity Framework を入手します。</span><span class="sxs-lookup"><span data-stu-id="0db75-102">Get Entity Framework</span></span>
<span data-ttu-id="0db75-103">Entity Framework は、Visual Studio と EF のランタイムの EF ツールの構成されます。</span><span class="sxs-lookup"><span data-stu-id="0db75-103">Entity Framework is made up of the EF Tools for Visual Studio and the EF Runtime.</span></span>

## <a name="ef-tools-for-visual-studio"></a><span data-ttu-id="0db75-104">EF の Tools for Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0db75-104">EF Tools for Visual Studio</span></span>

<span data-ttu-id="0db75-105">Entity Framework Tools for Visual Studio は EF Designer、EF モデル ウィザードは、最初に、データベースに必要なし、最初のワークフロー モデルします。</span><span class="sxs-lookup"><span data-stu-id="0db75-105">The Entity Framework Tools for Visual Studio include the EF Designer and the EF Model Wizard and are required for the database first and model first workflows.</span></span> <span data-ttu-id="0db75-106">すべての最新バージョンの Visual Studio では、EF ツールが含まれます。</span><span class="sxs-lookup"><span data-stu-id="0db75-106">EF Tools are included in all recent versions of Visual Studio.</span></span> <span data-ttu-id="0db75-107">項目を確認する必要がありますが、Visual Studio のカスタム インストールを実行する場合、それを含むワークロードを選択するか、または個々 のコンポーネントとして選択して、"Entity Framework 6 Tools"が選択されます。</span><span class="sxs-lookup"><span data-stu-id="0db75-107">If you perform a custom install of Visual Studio you will need to ensure that the item "Entity Framework 6 Tools" is selected by either choosing a workload that includes it or by selecting it as an individual component.</span></span>

<span data-ttu-id="0db75-108">一部が Visual Studio の以前のバージョンには、更新された EF ツールは、ダウンロードとして入手できます。</span><span class="sxs-lookup"><span data-stu-id="0db75-108">For some past versions of Visual Studio, updated EF Tools are available as a download.</span></span> <span data-ttu-id="0db75-109">参照してください[バージョンの Visual Studio](~/ef6/what-is-new/visual-studio.md) Visual Studio のバージョンの EF ツールの最新バージョンを取得する方法のガイダンスについて。</span><span class="sxs-lookup"><span data-stu-id="0db75-109">See [Visual Studio Versions](~/ef6/what-is-new/visual-studio.md) for guidance on how to get the latest version of EF Tools available for your version of Visual Studio.</span></span>

## <a name="ef-runtime"></a><span data-ttu-id="0db75-110">EF ランタイム</span><span class="sxs-lookup"><span data-stu-id="0db75-110">EF Runtime</span></span>

<span data-ttu-id="0db75-111">最新バージョンの Entity Framework は、 [EntityFramework NuGet パッケージ](http://nuget.org/packages/EntityFramework/)します。</span><span class="sxs-lookup"><span data-stu-id="0db75-111">The latest version of Entity Framework is available as the [EntityFramework NuGet package](http://nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="0db75-112">使い慣れた NuGet パッケージ マネージャーを使用しない場合は、ぜひを読み取る、 [NuGet の概要](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow)します。</span><span class="sxs-lookup"><span data-stu-id="0db75-112">If you are not familiar with the NuGet Package Manager, we encourage you to read the [NuGet Overview](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow).</span></span>

### <a name="installing-the-ef-nuget-package"></a><span data-ttu-id="0db75-113">EF の NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="0db75-113">Installing the EF NuGet Package</span></span>

<span data-ttu-id="0db75-114">右クリックし、EntityFramework パッケージをインストールすることができます、**参照**、プロジェクトのフォルダーを選択して**NuGet パッケージの管理.**</span><span class="sxs-lookup"><span data-stu-id="0db75-114">You can install the EntityFramework package by right-clicking on the **References** folder of your project and selecting **Manage NuGet Packages…**</span></span>

![ManageNuGetPackages](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a><span data-ttu-id="0db75-116">パッケージ マネージャー コンソールからインストールします。</span><span class="sxs-lookup"><span data-stu-id="0db75-116">Installing from Package Manager Console</span></span>

<span data-ttu-id="0db75-117">または、EntityFramework をインストールで、次のコマンドを実行して、[パッケージ マネージャー コンソール](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)します。</span><span class="sxs-lookup"><span data-stu-id="0db75-117">Alternatively, you can install EntityFramework by running the following command in the [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a><span data-ttu-id="0db75-118">EF の特定のバージョンをインストールします。</span><span class="sxs-lookup"><span data-stu-id="0db75-118">Installing a specific version of EF</span></span>

<span data-ttu-id="0db75-119">新しいバージョンの EF ランタイムは EF 4.1 以降からとしてリリースされていますが、 [EntityFramework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)します。</span><span class="sxs-lookup"><span data-stu-id="0db75-119">From EF 4.1 onwards, new versions of the EF runtime have been released as the [EntityFramework NuGet Package](https://www.nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="0db75-120">これらのバージョンの Visual studio の次のコマンドを実行して .NET Framework ベースのプロジェクトに追加できます[パッケージ マネージャー コンソール](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span><span class="sxs-lookup"><span data-stu-id="0db75-120">Any of those versions can be added to a .NET Framework-based project by running the following command in Visual Studio's [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span></span>

``` powershell
Install-Package EntityFramework -Version <number>
```

<span data-ttu-id="0db75-121">なお`<number>`をインストールする EF の特定のバージョンを表します。</span><span class="sxs-lookup"><span data-stu-id="0db75-121">Note that `<number>` represents the specific version of EF to install.</span></span> <span data-ttu-id="0db75-122">たとえば、6.2.0、EF 6.2 の番号のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="0db75-122">For example, 6.2.0 is the version of number for EF 6.2.</span></span>   

<span data-ttu-id="0db75-123">4.1 の前に EF のランタイムでは、.NET Framework の一部であったし、個別にインストールすることはできません。</span><span class="sxs-lookup"><span data-stu-id="0db75-123">EF runtimes before 4.1 were part of .NET Framework and cannot be installed separately.</span></span>

### <a name="installing-the-latest-preview"></a><span data-ttu-id="0db75-124">最新のプレビューをインストールします。</span><span class="sxs-lookup"><span data-stu-id="0db75-124">Installing the Latest Preview</span></span>

<span data-ttu-id="0db75-125">上記のメソッドが、最新バージョンを与える Entity Framework のリリースを完全にサポートします。</span><span class="sxs-lookup"><span data-stu-id="0db75-125">The above methods will give you the latest fully supported release of Entity Framework.</span></span> <span data-ttu-id="0db75-126">プレリリース バージョンの Entity Framework をぜひお試しし、についてフィードバックを提供する利用可能なことは少なくありません。</span><span class="sxs-lookup"><span data-stu-id="0db75-126">There are often prerelease versions of Entity Framework available that we would love you to try out and give us feedback on.</span></span>

<span data-ttu-id="0db75-127">選択 EntityFramework の最新のプレビューをインストールする**プレリリース版を含む**NuGet パッケージの管理 ウィンドウでします。</span><span class="sxs-lookup"><span data-stu-id="0db75-127">To install the latest preview of EntityFramework you can select **Include Prerelease** in the Manage NuGet Packages window.</span></span> <span data-ttu-id="0db75-128">プレリリース バージョンが利用できない場合は、最新を自動的に取得が完全にサポートされているバージョンの Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="0db75-128">If no prerelease versions are available you will automatically get the latest fully supported version of Entity Framework.</span></span>

![IncludePreRelease](~/ef6/media/includeprerelease.png)

<span data-ttu-id="0db75-130">また、次のコマンドを実行することができます、[パッケージ マネージャー コンソール](http://docs.nuget.org/docs/start-here/using-the-package-manager-console)します。</span><span class="sxs-lookup"><span data-stu-id="0db75-130">Alternatively, you can run the following command in the [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework -Pre
```
