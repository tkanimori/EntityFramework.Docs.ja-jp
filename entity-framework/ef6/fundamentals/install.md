---
title: Entity Framework の取得-EF6
description: Entity Framework 6 を取得する
author: divega
ms.date: 10/23/2016
ms.assetid: 122c38a2-f9e8-4ecc-9c72-a83bc9af7814
uid: ef6/fundamentals/install
ms.openlocfilehash: bfd016a93de73ada4487a454ec7abd9251aeecfe
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616248"
---
# <a name="get-entity-framework"></a><span data-ttu-id="4ec22-103">Entity Framework を取得する</span><span class="sxs-lookup"><span data-stu-id="4ec22-103">Get Entity Framework</span></span>
<span data-ttu-id="4ec22-104">Entity Framework は、EF Tools for Visual Studio と EF Runtime で構成されています。</span><span class="sxs-lookup"><span data-stu-id="4ec22-104">Entity Framework is made up of the EF Tools for Visual Studio and the EF Runtime.</span></span>

## <a name="ef-tools-for-visual-studio"></a><span data-ttu-id="4ec22-105">EF Tools for Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4ec22-105">EF Tools for Visual Studio</span></span>

<span data-ttu-id="4ec22-106">Visual Studio の Entity Framework Tools には、EF デザイナーと EF モデルウィザードが含まれており、データベースの最初のワークフローとモデルの最初のワークフローに必要です。</span><span class="sxs-lookup"><span data-stu-id="4ec22-106">The Entity Framework Tools for Visual Studio include the EF Designer and the EF Model Wizard and are required for the database first and model first workflows.</span></span> <span data-ttu-id="4ec22-107">EF ツールは、すべての最新バージョンの Visual Studio に含まれています。</span><span class="sxs-lookup"><span data-stu-id="4ec22-107">EF Tools are included in all recent versions of Visual Studio.</span></span> <span data-ttu-id="4ec22-108">Visual Studio のカスタムインストールを実行する場合は、項目を含むワークロードを選択するか、個々のコンポーネントとして選択することで、項目 "Entity Framework 6 ツール" が選択されていることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4ec22-108">If you perform a custom install of Visual Studio you will need to ensure that the item "Entity Framework 6 Tools" is selected by either choosing a workload that includes it or by selecting it as an individual component.</span></span>

<span data-ttu-id="4ec22-109">以前のバージョンの Visual Studio では、更新された EF ツールはダウンロードとして入手できます。</span><span class="sxs-lookup"><span data-stu-id="4ec22-109">For some past versions of Visual Studio, updated EF Tools are available as a download.</span></span> <span data-ttu-id="4ec22-110">お使いのバージョンの Visual Studio で使用できる EF ツールの最新バージョンを入手する方法については、「 [Visual studio のバージョン](xref:ef6/what-is-new/visual-studio) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4ec22-110">See [Visual Studio Versions](xref:ef6/what-is-new/visual-studio) for guidance on how to get the latest version of EF Tools available for your version of Visual Studio.</span></span>

## <a name="ef-runtime"></a><span data-ttu-id="4ec22-111">EF ランタイム</span><span class="sxs-lookup"><span data-stu-id="4ec22-111">EF Runtime</span></span>

<span data-ttu-id="4ec22-112">Entity Framework の最新バージョンは、 [Entityframework NuGet パッケージ](https://nuget.org/packages/EntityFramework/)として入手できます。</span><span class="sxs-lookup"><span data-stu-id="4ec22-112">The latest version of Entity Framework is available as the [EntityFramework NuGet package](https://nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="4ec22-113">NuGet パッケージマネージャーに慣れていない場合は、「 [nuget の概要」](/nuget/consume-packages/overview-and-workflow)をお読みになることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="4ec22-113">If you are not familiar with the NuGet Package Manager, we encourage you to read the [NuGet Overview](/nuget/consume-packages/overview-and-workflow).</span></span>

### <a name="installing-the-ef-nuget-package"></a><span data-ttu-id="4ec22-114">EF NuGet パッケージのインストール</span><span class="sxs-lookup"><span data-stu-id="4ec22-114">Installing the EF NuGet Package</span></span>

<span data-ttu-id="4ec22-115">EntityFramework パッケージをインストールするには、プロジェクトの [**参照**] フォルダーを右クリックし、[ **NuGet パッケージの管理...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="4ec22-115">You can install the EntityFramework package by right-clicking on the **References** folder of your project and selecting **Manage NuGet Packages…**</span></span>

![NuGet パッケージの管理](~/ef6/media/managenugetpackages.png)

### <a name="installing-from-package-manager-console"></a><span data-ttu-id="4ec22-117">パッケージマネージャーコンソールからのインストール</span><span class="sxs-lookup"><span data-stu-id="4ec22-117">Installing from Package Manager Console</span></span>

<span data-ttu-id="4ec22-118">または、 [パッケージマネージャーコンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)で次のコマンドを実行して、entityframework をインストールすることもできます。</span><span class="sxs-lookup"><span data-stu-id="4ec22-118">Alternatively, you can install EntityFramework by running the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework
```

## <a name="installing-a-specific-version-of-ef"></a><span data-ttu-id="4ec22-119">特定のバージョンの EF のインストール</span><span class="sxs-lookup"><span data-stu-id="4ec22-119">Installing a specific version of EF</span></span>

<span data-ttu-id="4ec22-120">EF 4.1 以降では、新しいバージョンの EF runtime が [Entityframework NuGet パッケージ](https://www.nuget.org/packages/EntityFramework/)としてリリースされました。</span><span class="sxs-lookup"><span data-stu-id="4ec22-120">From EF 4.1 onwards, new versions of the EF runtime have been released as the [EntityFramework NuGet Package](https://www.nuget.org/packages/EntityFramework/).</span></span> <span data-ttu-id="4ec22-121">これらのバージョンはいずれも、Visual Studio の [パッケージマネージャーコンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)で次のコマンドを実行して、.NET Framework ベースのプロジェクトに追加できます。</span><span class="sxs-lookup"><span data-stu-id="4ec22-121">Any of those versions can be added to a .NET Framework-based project by running the following command in Visual Studio's [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console):</span></span>

``` powershell
Install-Package EntityFramework -Version <number>
```

<span data-ttu-id="4ec22-122">は `<number>` 、インストールする EF の特定のバージョンを表すことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="4ec22-122">Note that `<number>` represents the specific version of EF to install.</span></span> <span data-ttu-id="4ec22-123">たとえば、6.2.0 は EF 6.2 の number のバージョンです。</span><span class="sxs-lookup"><span data-stu-id="4ec22-123">For example, 6.2.0 is the version of number for EF 6.2.</span></span>   

<span data-ttu-id="4ec22-124">4.1 より前の EF runtime は .NET Framework に含まれており、個別にインストールすることはできません。</span><span class="sxs-lookup"><span data-stu-id="4ec22-124">EF runtimes before 4.1 were part of .NET Framework and cannot be installed separately.</span></span>

### <a name="installing-the-latest-preview"></a><span data-ttu-id="4ec22-125">最新のプレビューのインストール</span><span class="sxs-lookup"><span data-stu-id="4ec22-125">Installing the Latest Preview</span></span>

<span data-ttu-id="4ec22-126">上記の方法では、Entity Framework の完全にサポートされた最新のリリースが提供されます。</span><span class="sxs-lookup"><span data-stu-id="4ec22-126">The above methods will give you the latest fully supported release of Entity Framework.</span></span> <span data-ttu-id="4ec22-127">多くの場合、プレリリース版の Entity Framework 利用可能であり、お客様にご意見をお寄せください。</span><span class="sxs-lookup"><span data-stu-id="4ec22-127">There are often prerelease versions of Entity Framework available that we would love you to try out and give us feedback on.</span></span>

<span data-ttu-id="4ec22-128">EntityFramework の最新のプレビューをインストールするには、[NuGet パッケージの管理] ウィンドウで [ **プレリリースを含める** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="4ec22-128">To install the latest preview of EntityFramework you can select **Include Prerelease** in the Manage NuGet Packages window.</span></span> <span data-ttu-id="4ec22-129">使用可能なプレリリースバージョンがない場合は、最新の完全サポートバージョンの Entity Framework が自動的に取得されます。</span><span class="sxs-lookup"><span data-stu-id="4ec22-129">If no prerelease versions are available you will automatically get the latest fully supported version of Entity Framework.</span></span>

![プレリリースを含める](~/ef6/media/includeprerelease.png)

<span data-ttu-id="4ec22-131">または、 [パッケージマネージャーコンソール](https://docs.nuget.org/docs/start-here/using-the-package-manager-console)で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="4ec22-131">Alternatively, you can run the following command in the [Package Manager Console](https://docs.nuget.org/docs/start-here/using-the-package-manager-console).</span></span>

``` powershell
Install-Package EntityFramework -Pre
```
