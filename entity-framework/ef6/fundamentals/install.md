---
title: Entity Framework の取得-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
ms.openlocfilehash: 2bdec6a9be228fbe934d0f46aa1bfafdfb2c971c
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181740"
---
# <a name="get-entity-framework"></a><span data-ttu-id="1050c-102">Entity Framework を取得する</span><span class="sxs-lookup"><span data-stu-id="1050c-102">Get Entity Framework</span></span>
<span data-ttu-id="1050c-103">Entity Framework は、EF Tools for Visual Studio と EF Runtime で構成されています。</span><span class="sxs-lookup"><span data-stu-id="1050c-103">Entity Framework is made up of the EF Tools for Visual Studio and the EF Runtime.</span></span>

## <a name="ef-tools-for-visual-studio"></a><span data-ttu-id="1050c-104">EF Tools for Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1050c-104">EF Tools for Visual Studio</span></span>

<span data-ttu-id="1050c-105">Visual Studio の Entity Framework Tools には、EF デザイナーと EF モデルウィザードが含まれており、データベースの最初のワークフローとモデルの最初のワークフローに必要です。</span><span class="sxs-lookup"><span data-stu-id="1050c-105">The Entity Framework Tools for Visual Studio include the EF Designer and the EF Model Wizard and are required for the database first and model first workflows.</span></span> <span data-ttu-id="1050c-106">EF ツールは、すべての最新バージョンの Visual Studio に含まれています。</span><span class="sxs-lookup"><span data-stu-id="1050c-106">EF Tools are included in all recent versions of Visual Studio.</span></span> <span data-ttu-id="1050c-107">Visual Studio のカスタムインストールを実行する場合は、項目を含むワークロードを選択するか、個々のコンポーネントとして選択することで、項目 "Entity Framework 6 ツール" が選択されていることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1050c-107">If you perform a custom install of Visual Studio you will need to ensure that the item "Entity Framework 6 Tools" is selected by either choosing a workload that includes it or by selecting it as an individual component.</span></span>

<span data-ttu-id="1050c-108">以前のバージョンの Visual Studio では、更新された EF ツールはダウンロードとして入手できます。</span><span class="sxs-lookup"><span data-stu-id="1050c-108">For some past versions of Visual Studio, updated EF Tools are available as a download.</span></span> <span data-ttu-id="1050c-109">お使いのバージョンの Visual Studio で使用できる EF ツールの最新バージョンを入手する方法については、「 [Visual studio のバージョン](~/ef6/what-is-new/visual-studio.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1050c-109">See [Visual Studio Versions](~/ef6/what-is-new/visual-studio.md) for guidance on how to get the latest version of EF Tools available for your version of Visual Studio.</span></span>

## <a name="ef-runtime"></a><span data-ttu-id="1050c-110">EF ランタイム</span><span class="sxs-lookup"><span data-stu-id="1050c-110">EF Runtime</span></span>

<span data-ttu-id="1050c-111">Entity Framework の最新バージョンは、 [Entityframework NuGet パッケージ](https://nuget.org/packages/EntityFramework/)として入手できます。</span><span class="sxs-lookup"><span data-stu-id="1050c-111">The latest version of Entity Framework is available as the [EntityFramework NuGet package](https://nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="1050c-112">NuGet パッケージマネージャーに慣れていない場合は、「 [nuget の概要」](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow)をお読みになることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1050c-112">If you are not familiar with the NuGet Package Manager, we encourage you to read the [NuGet Overview](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow).</span></span>

### <a name="installing-the-ef-nuget-package"></a><span data-ttu-id="1050c-113">EF NuGet パッケージのインストール</span><span class="sxs-lookup"><span data-stu-id="1050c-113">Installing the EF NuGet Package</span></span>

<span data-ttu-id="1050c-114">EntityFramework パッケージをインストールするには、プロジェクトの **[参照]** フォルダーを右クリックし、 **[NuGet パッケージの管理...]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1050c-114">You can install the EntityFramework package by right-clicking on the **References** folder of your project and selecting **Manage NuGet Packages…**</span></span>

![NuGet パッケージの管理](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a><span data-ttu-id="1050c-116">パッケージマネージャーコンソールからのインストール</span><span class="sxs-lookup"><span data-stu-id="1050c-116">Installing from Package Manager Console</span></span>

<span data-ttu-id="1050c-117">または、[パッケージマネージャーコンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)で次のコマンドを実行して、entityframework をインストールすることもできます。</span><span class="sxs-lookup"><span data-stu-id="1050c-117">Alternatively, you can install EntityFramework by running the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a><span data-ttu-id="1050c-118">特定のバージョンの EF のインストール</span><span class="sxs-lookup"><span data-stu-id="1050c-118">Installing a specific version of EF</span></span>

<span data-ttu-id="1050c-119">EF 4.1 以降では、新しいバージョンの EF runtime が[Entityframework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)としてリリースされました。</span><span class="sxs-lookup"><span data-stu-id="1050c-119">From EF 4.1 onwards, new versions of the EF runtime have been released as the [EntityFramework NuGet Package](https://www.nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="1050c-120">これらのバージョンはいずれも、Visual Studio の[パッケージマネージャーコンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)で次のコマンドを実行して、.NET Framework ベースのプロジェクトに追加できます。</span><span class="sxs-lookup"><span data-stu-id="1050c-120">Any of those versions can be added to a .NET Framework-based project by running the following command in Visual Studio's [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span></span>

``` powershell
Install-Package EntityFramework -Version <number>
```

<span data-ttu-id="1050c-121">@No__t-0 は、インストールする EF の特定のバージョンを表していることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="1050c-121">Note that `<number>` represents the specific version of EF to install.</span></span> <span data-ttu-id="1050c-122">たとえば、6.2.0 は EF 6.2 の number のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="1050c-122">For example, 6.2.0 is the version of number for EF 6.2.</span></span>   

<span data-ttu-id="1050c-123">4\.1 より前の EF runtime は .NET Framework に含まれており、個別にインストールすることはできません。</span><span class="sxs-lookup"><span data-stu-id="1050c-123">EF runtimes before 4.1 were part of .NET Framework and cannot be installed separately.</span></span>

### <a name="installing-the-latest-preview"></a><span data-ttu-id="1050c-124">最新のプレビューのインストール</span><span class="sxs-lookup"><span data-stu-id="1050c-124">Installing the Latest Preview</span></span>

<span data-ttu-id="1050c-125">上記の方法では、Entity Framework の完全にサポートされた最新のリリースが提供されます。</span><span class="sxs-lookup"><span data-stu-id="1050c-125">The above methods will give you the latest fully supported release of Entity Framework.</span></span> <span data-ttu-id="1050c-126">多くの場合、プレリリース版の Entity Framework 利用可能であり、お客様にご意見をお寄せください。</span><span class="sxs-lookup"><span data-stu-id="1050c-126">There are often prerelease versions of Entity Framework available that we would love you to try out and give us feedback on.</span></span>

<span data-ttu-id="1050c-127">EntityFramework の最新のプレビューをインストールするには、NuGet パッケージの管理 ウィンドウで **プレリリースを含める** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1050c-127">To install the latest preview of EntityFramework you can select **Include Prerelease** in the Manage NuGet Packages window.</span></span> <span data-ttu-id="1050c-128">使用可能なプレリリースバージョンがない場合は、最新の完全サポートバージョンの Entity Framework が自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="1050c-128">If no prerelease versions are available you will automatically get the latest fully supported version of Entity Framework.</span></span>

![プレリリースを含める](~/ef6/media/includeprerelease.png)

<span data-ttu-id="1050c-130">または、[パッケージマネージャーコンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="1050c-130">Alternatively, you can run the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework -Pre
```
