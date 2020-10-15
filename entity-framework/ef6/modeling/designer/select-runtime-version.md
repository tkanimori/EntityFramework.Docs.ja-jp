---
title: EF デザイナーモデルに Entity Framework ランタイムバージョンを選択する-EF6
description: Entity Framework 6 の EF デザイナーモデルで Entity Framework ランタイムバージョンを選択する
author: ajcvickers
ms.date: 10/23/2016
ms.openlocfilehash: dcd06ed9585028b3a318d867b03980254979a32f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066097"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a><span data-ttu-id="e1642-103">EF デザイナーモデルの Entity Framework ランタイムバージョンの選択</span><span class="sxs-lookup"><span data-stu-id="e1642-103">Selecting Entity Framework Runtime Version for EF Designer Models</span></span>
> [!NOTE]
> <span data-ttu-id="e1642-104">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e1642-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="e1642-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="e1642-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="e1642-106">EF6 以降では、次の画面が EF デザイナーに追加され、モデルの作成時に対象とするランタイムのバージョンを選択できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e1642-106">Starting with EF6 the following screen was added to the EF Designer to allow you to select the version of the runtime you wish to target when creating a model.</span></span> <span data-ttu-id="e1642-107">この画面は、最新バージョンの Entity Framework がプロジェクトにまだインストールされていない場合に表示されます。</span><span class="sxs-lookup"><span data-stu-id="e1642-107">The screen will appear when the latest version of Entity Framework is not already installed in the project.</span></span> <span data-ttu-id="e1642-108">最新バージョンが既にインストールされている場合は、既定でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="e1642-108">If the latest version is already installed it will just be used by default.</span></span>

![ランタイムバージョンの選択](~/ef6/media/screen.png)

## <a name="targeting-ef6x"></a><span data-ttu-id="e1642-110">EF6 をターゲットにする</span><span class="sxs-lookup"><span data-stu-id="e1642-110">Targeting EF6.x</span></span>

<span data-ttu-id="e1642-111">[バージョンの選択] 画面で [EF6] を選択して、EF6 ランタイムをプロジェクトに追加できます。</span><span class="sxs-lookup"><span data-stu-id="e1642-111">You can choose EF6 from the 'Choose Your Version' screen to add the EF6 runtime to your project.</span></span> <span data-ttu-id="e1642-112">EF6 を追加すると、現在のプロジェクトでこの画面が表示されなくなります。</span><span class="sxs-lookup"><span data-stu-id="e1642-112">Once you've added EF6, you’ll stop seeing this screen in the current project.</span></span>

<span data-ttu-id="e1642-113">以前のバージョンの EF が既にインストールされている場合、EF6 は無効になります (同じプロジェクトから複数のバージョンのランタイムを対象にすることはできません)。</span><span class="sxs-lookup"><span data-stu-id="e1642-113">EF6 will be disabled if you already have an older version of EF installed (since you can't target multiple versions of the runtime from the same project).</span></span> <span data-ttu-id="e1642-114">ここで EF6 オプションが有効になっていない場合は、次の手順に従ってプロジェクトを EF6 にアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="e1642-114">If EF6 option is not enabled here, follow these steps to upgrade your project to EF6:</span></span>

1.  <span data-ttu-id="e1642-115">ソリューションエクスプローラーでプロジェクトを右クリックし、[ **NuGet パッケージの管理**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="e1642-115">Right-click on your project in Solution Explorer and select **Manage NuGet Packages...**</span></span>
2.  <span data-ttu-id="e1642-116">**更新プログラム**の選択</span><span class="sxs-lookup"><span data-stu-id="e1642-116">Select **Updates**</span></span>
3.  <span data-ttu-id="e1642-117">**Entityframework**を選択します (必要なバージョンに更新することを確認してください)</span><span class="sxs-lookup"><span data-stu-id="e1642-117">Select **EntityFramework** (make sure it is going to update it to the version you want)</span></span>
4.  <span data-ttu-id="e1642-118">**[Update]\(更新\)** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="e1642-118">Click **Update**</span></span>

 

## <a name="targeting-ef5x"></a><span data-ttu-id="e1642-119">EF5 をターゲットにする</span><span class="sxs-lookup"><span data-stu-id="e1642-119">Targeting EF5.x</span></span>

<span data-ttu-id="e1642-120">[バージョンの選択] 画面で [EF5] を選択して、EF5 ランタイムをプロジェクトに追加できます。</span><span class="sxs-lookup"><span data-stu-id="e1642-120">You can choose EF5 from the 'Choose Your Version' screen to add the EF5 runtime to your project.</span></span> <span data-ttu-id="e1642-121">EF5 を追加した後も、EF6 オプションが無効になっている画面が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e1642-121">Once you've added EF5, you’ll still see the screen with the EF6 option disabled.</span></span>

<span data-ttu-id="e1642-122">EF4 バージョンのランタイムが既にインストールされている場合は、EF5 ではなく、そのバージョンの EF が画面に表示されます。</span><span class="sxs-lookup"><span data-stu-id="e1642-122">If you have an EF4.x version of the runtime already installed then you will see that version of EF listed in the screen rather than EF5.</span></span> <span data-ttu-id="e1642-123">このような状況では、次の手順を使用して EF5 にアップグレードできます。</span><span class="sxs-lookup"><span data-stu-id="e1642-123">In this situation you can upgrade to EF5 using the following steps:</span></span>

1.  <span data-ttu-id="e1642-124">**ツール- &gt; ライブラリパッケージマネージャー- &gt; パッケージマネージャーコンソールの**選択</span><span class="sxs-lookup"><span data-stu-id="e1642-124">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="e1642-125">**インストールパッケージ EntityFramework の実行-バージョン 5.0.0**</span><span class="sxs-lookup"><span data-stu-id="e1642-125">Run **Install-Package EntityFramework -version 5.0.0**</span></span>

 

## <a name="targeting-ef4x"></a><span data-ttu-id="e1642-126">EF4 をターゲットにする</span><span class="sxs-lookup"><span data-stu-id="e1642-126">Targeting EF4.x</span></span>

<span data-ttu-id="e1642-127">EF4 ランタイムをプロジェクトにインストールするには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="e1642-127">You can install the EF4.x runtime to your project using the following steps:</span></span>

1.  <span data-ttu-id="e1642-128">**ツール- &gt; ライブラリパッケージマネージャー- &gt; パッケージマネージャーコンソールの**選択</span><span class="sxs-lookup"><span data-stu-id="e1642-128">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="e1642-129">**Install-Package EntityFramework-version 4.3.0 を**実行します。</span><span class="sxs-lookup"><span data-stu-id="e1642-129">Run **Install-Package EntityFramework -version 4.3.0**</span></span>
