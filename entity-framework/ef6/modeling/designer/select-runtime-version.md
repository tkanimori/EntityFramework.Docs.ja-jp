---
title: EF デザイナー モデル - EF6 の Entity Framework ランタイムのバージョンを選択します。
author: divega
ms.date: 10/23/2016
ms.assetid: 7ace90a6-46f8-4f55-a88c-7cad9620085c
ms.openlocfilehash: 40ad05c1f015e6a51150d04eee8d9aa581d72c33
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45488492"
---
# <a name="selecting-entity-framework-runtime-version-for-ef-designer-models"></a><span data-ttu-id="c8851-102">Entity Framework ランタイムのバージョンの EF デザイナー モデルの選択</span><span class="sxs-lookup"><span data-stu-id="c8851-102">Selecting Entity Framework Runtime Version for EF Designer Models</span></span>
> [!NOTE]
> <span data-ttu-id="c8851-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="c8851-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="c8851-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="c8851-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="c8851-105">EF6 以降、次の画面は、モデルの作成時に対象にするランタイムのバージョンを選択するように EF Designer に追加されました。</span><span class="sxs-lookup"><span data-stu-id="c8851-105">Starting with EF6 the following screen was added to the EF Designer to allow you to select the version of the runtime you wish to target when creating a model.</span></span> <span data-ttu-id="c8851-106">Entity Framework の最新バージョンが既にプロジェクトにインストールされていないときに、画面が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c8851-106">The screen will appear when the latest version of Entity Framework is not already installed in the project.</span></span> <span data-ttu-id="c8851-107">最新のバージョンが既にインストールされている場合のみ既定で使用できます。</span><span class="sxs-lookup"><span data-stu-id="c8851-107">If the latest version is already installed it will just be used by default.</span></span>

![画面](~/ef6/media/screen.png)


## <a name="targeting-ef6x"></a><span data-ttu-id="c8851-109">EF6.x を対象とします。</span><span class="sxs-lookup"><span data-stu-id="c8851-109">Targeting EF6.x</span></span>

<span data-ttu-id="c8851-110">EF6 は EF6 ランタイム プロジェクトを追加する [バージョンの選択] 画面から選択できます。</span><span class="sxs-lookup"><span data-stu-id="c8851-110">You can choose EF6 from the 'Choose Your Version' screen to add the EF6 runtime to your project.</span></span> <span data-ttu-id="c8851-111">EF6 を追加すると、現在のプロジェクトでは、この画面が表示されるを停止します。</span><span class="sxs-lookup"><span data-stu-id="c8851-111">Once you've added EF6, you’ll stop seeing this screen in the current project.</span></span>

<span data-ttu-id="c8851-112">以前のバージョンの EF が (同じプロジェクトからランタイムの複数のバージョンを対象とすることはできません) からのインストールが既にある場合は、EF6 を無効化されます。</span><span class="sxs-lookup"><span data-stu-id="c8851-112">EF6 will be disabled if you already have an older version of EF installed (since you can't target multiple versions of the runtime from the same project).</span></span> <span data-ttu-id="c8851-113">EF6 のオプションが有効にしない場合は、プロジェクトを EF6 にアップグレードする手順に従います。</span><span class="sxs-lookup"><span data-stu-id="c8851-113">If EF6 option is not enabled here, follow these steps to upgrade your project to EF6:</span></span>

1.  <span data-ttu-id="c8851-114">ソリューション エクスプ ローラーでプロジェクトを右クリックして**NuGet パッケージの管理.**</span><span class="sxs-lookup"><span data-stu-id="c8851-114">Right-click on your project in Solution Explorer and select **Manage NuGet Packages...**</span></span>
2.  <span data-ttu-id="c8851-115">選択**更新プログラム**</span><span class="sxs-lookup"><span data-stu-id="c8851-115">Select **Updates**</span></span>
3.  <span data-ttu-id="c8851-116">選択**EntityFramework** (必要なバージョンに更新することを確認してください)</span><span class="sxs-lookup"><span data-stu-id="c8851-116">Select **EntityFramework** (make sure it is going to update it to the version you want)</span></span>
4.  <span data-ttu-id="c8851-117">クリックして**Update**</span><span class="sxs-lookup"><span data-stu-id="c8851-117">Click **Update**</span></span>

 

## <a name="targeting-ef5x"></a><span data-ttu-id="c8851-118">EF5.x を対象とします。</span><span class="sxs-lookup"><span data-stu-id="c8851-118">Targeting EF5.x</span></span>

<span data-ttu-id="c8851-119">EF5 EF5 ランタイム プロジェクトを追加する [バージョンの選択] 画面から選択できます。</span><span class="sxs-lookup"><span data-stu-id="c8851-119">You can choose EF5 from the 'Choose Your Version' screen to add the EF5 runtime to your project.</span></span> <span data-ttu-id="c8851-120">EF5 を追加したら、EF6 のオプションを無効になっていますが、画面が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c8851-120">Once you've added EF5, you’ll still see the screen with the EF6 option disabled.</span></span>

<span data-ttu-id="c8851-121">既にインストールされているランタイムの EF4.x バージョンがあると、そのバージョンの EF EF5 ではなく、画面に表示が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c8851-121">If you have an EF4.x version of the runtime already installed then you will see that version of EF listed in the screen rather than EF5.</span></span> <span data-ttu-id="c8851-122">EF5 にアップグレードするこのような状況で、次の手順を使用します。</span><span class="sxs-lookup"><span data-stu-id="c8851-122">In this situation you can upgrade to EF5 using the following steps:</span></span>

1.  <span data-ttu-id="c8851-123">選択**ツール -&gt;ライブラリ パッケージ マネージャー -&gt;パッケージ マネージャー コンソール**</span><span class="sxs-lookup"><span data-stu-id="c8851-123">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="c8851-124">実行 **"Install-package entityframework"-バージョン 5.0.0**</span><span class="sxs-lookup"><span data-stu-id="c8851-124">Run **Install-Package EntityFramework -version 5.0.0**</span></span>

 

## <a name="targeting-ef4x"></a><span data-ttu-id="c8851-125">EF4.x を対象とします。</span><span class="sxs-lookup"><span data-stu-id="c8851-125">Targeting EF4.x</span></span>

<span data-ttu-id="c8851-126">EF4.x ランタイムは、次の手順を使用して、プロジェクトにインストールできます。</span><span class="sxs-lookup"><span data-stu-id="c8851-126">You can install the EF4.x runtime to your project using the following steps:</span></span>

1.  <span data-ttu-id="c8851-127">選択**ツール -&gt;ライブラリ パッケージ マネージャー -&gt;パッケージ マネージャー コンソール**</span><span class="sxs-lookup"><span data-stu-id="c8851-127">Select **Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
2.  <span data-ttu-id="c8851-128">実行 **"Install-package entityframework"、バージョン 4.3.0**</span><span class="sxs-lookup"><span data-stu-id="c8851-128">Run **Install-Package EntityFramework -version 4.3.0**</span></span>
