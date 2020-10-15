---
title: モデルごとの複数のダイアグラム-EF6
description: Entity Framework 6 のモデルあたりの複数のダイアグラム
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/multiple-diagrams
ms.openlocfilehash: 625cb932d23805f6bd22b658e4cddab1a487751c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066214"
---
# <a name="multiple-diagrams-per-model"></a><span data-ttu-id="35cc7-103">モデルごとに複数のダイアグラム</span><span class="sxs-lookup"><span data-stu-id="35cc7-103">Multiple Diagrams per Model</span></span>
> [!NOTE]
> <span data-ttu-id="35cc7-104">**EF5** 以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="35cc7-104">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="35cc7-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="35cc7-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="35cc7-106">このビデオとページでは、Entity Framework Designer (EF Designer) を使用してモデルを複数の図に分割する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-106">This video and page shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="35cc7-107">モデルが大きすぎて表示または編集できない場合は、この機能を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-107">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="35cc7-108">以前のバージョンの EF デザイナーでは、EDMX ファイルごとに1つの図しか使用できませんでした。</span><span class="sxs-lookup"><span data-stu-id="35cc7-108">In earlier versions of the EF Designer you could only have one diagram per the EDMX file.</span></span> <span data-ttu-id="35cc7-109">Visual Studio 2012 以降では、EF デザイナーを使用して、EDMX ファイルを複数のダイアグラムに分割できます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-109">Starting with Visual Studio 2012, you can use the EF Designer to split your EDMX file into multiple diagrams.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="35cc7-110">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="35cc7-110">Watch the video</span></span>
<span data-ttu-id="35cc7-111">このビデオでは、Entity Framework Designer (EF Designer) を使用してモデルを複数の図に分割する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-111">This video shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="35cc7-112">モデルが大きすぎて表示または編集できない場合は、この機能を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-112">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="35cc7-113">**表示者**: ジュリアつい</span><span class="sxs-lookup"><span data-stu-id="35cc7-113">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="35cc7-114">**ビデオ**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv)  |  [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v)  |  [WMV (ZIP)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span><span class="sxs-lookup"><span data-stu-id="35cc7-114">**Video**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span></span>

## <a name="ef-designer-overview"></a><span data-ttu-id="35cc7-115">EF デザイナーの概要</span><span class="sxs-lookup"><span data-stu-id="35cc7-115">EF Designer Overview</span></span>

<span data-ttu-id="35cc7-116">EF デザイナーの Entity Data Model ウィザードを使用してモデルを作成すると、.edmx ファイルが作成され、ソリューションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-116">When you create a model using the EF Designer’s Entity Data Model Wizard, an .edmx file is created and added to your solution.</span></span> <span data-ttu-id="35cc7-117">このファイルでは、エンティティの形状と、エンティティをデータベースにマップする方法を定義します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-117">This file defines the shape of your entities and how they map to the database.</span></span>

<span data-ttu-id="35cc7-118">EF デザイナーは、次のコンポーネントで構成されています。</span><span class="sxs-lookup"><span data-stu-id="35cc7-118">The EF Designer consists of the following components:</span></span>

-   <span data-ttu-id="35cc7-119">モデルを編集するためのビジュアルデザインサーフェイス。</span><span class="sxs-lookup"><span data-stu-id="35cc7-119">A visual design surface for editing the model.</span></span> <span data-ttu-id="35cc7-120">エンティティとアソシエーションの作成、変更、または削除を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-120">You can create, modify, or delete entities and associations.</span></span>
-   <span data-ttu-id="35cc7-121">モデル **Model Browser**   のツリービューを提供するモデルブラウザーウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="35cc7-121">A **Model Browser** window that provides tree views of the model.</span></span><span data-ttu-id="35cc7-122">エンティティとその関連付けは、 \* \[ ModelName \] \*フォルダーにあります。</span><span class="sxs-lookup"><span data-stu-id="35cc7-122">  The entities and their associations are located under the *\[ModelName\]* folder.</span></span> <span data-ttu-id="35cc7-123">データベースのテーブルと制約は、 \* \[ ModelName \] \*の下にあります。ストアフォルダー。</span><span class="sxs-lookup"><span data-stu-id="35cc7-123">The database tables and constraints are located under the *\[ModelName\]*.Store folder.</span></span>
-   <span data-ttu-id="35cc7-124">マッピングを表示および編集するための [ **マッピングの詳細** ] ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="35cc7-124">A **Mapping Details** window for viewing and editing mappings.</span></span> <span data-ttu-id="35cc7-125">エンティティ型またはアソシエーションをデータベース テーブル、列、およびストアド プロシージャにマップできます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-125">You can map entity types or associations to database tables, columns, and stored procedures.</span></span> 

<span data-ttu-id="35cc7-126">Entity Data Model ウィザードが終了すると、ビジュアルデザインサーフェイスウィンドウが自動的に開きます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-126">The visual design surface window is automatically opened when the Entity Data Model Wizard finishes.</span></span> <span data-ttu-id="35cc7-127">モデルブラウザーが表示されていない場合は、メインのデザイン画面を右クリックし、[ **モデルブラウザー**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-127">If the Model Browser is not visible, right-click the main design surface and select **Model Browser**.</span></span>

<span data-ttu-id="35cc7-128">次のスクリーンショットは、EF デザイナーで開かれた .edmx ファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="35cc7-128">The following screenshot shows an .edmx file opened in the EF Designer.</span></span> <span data-ttu-id="35cc7-129">スクリーンショットには、ビジュアルデザイン画面 (左側) と [ **モデルブラウザー**   ] ウィンドウ (右側) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-129">The screenshot shows the visual design surface (to the left) and the **Model Browser** window (to the right).</span></span>

![EF デザイナー2](~/ef6/media/efdesigner2.png)

<span data-ttu-id="35cc7-131">EF デザイナーで実行した操作を元に戻すには、Ctrl + Z キーを押します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-131">To undo an operation done in the EF Designer, click Ctrl-Z.</span></span>

## <a name="working-with-diagrams"></a><span data-ttu-id="35cc7-132">ダイアグラムの操作</span><span class="sxs-lookup"><span data-stu-id="35cc7-132">Working with Diagrams</span></span>

<span data-ttu-id="35cc7-133">既定では、EF デザイナーは Diagram1 という1つのダイアグラムを作成します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-133">By default the EF Designer creates one diagram called Diagram1.</span></span> <span data-ttu-id="35cc7-134">多数のエンティティとアソシエーションを含む図がある場合は、それらを論理的に分割することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="35cc7-134">If you have a diagram with a large number of entities and associations, you will most like want to split them up logically.</span></span> <span data-ttu-id="35cc7-135">Visual Studio 2012 以降では、複数の図で概念モデルを表示できます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-135">Starting with Visual Studio 2012, you can view your conceptual model in multiple diagrams.</span></span>   

<span data-ttu-id="35cc7-136">新しいダイアグラムを追加すると、モデルブラウザーウィンドウの [ダイアグラム] フォルダーの下に表示されます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-136">As you add new diagrams, they appear under the Diagrams folder in the Model Browser window.</span></span> <span data-ttu-id="35cc7-137">ダイアグラムの名前を変更するには、[モデルブラウザー] ウィンドウでダイアグラムを選択し、[名前] で [1 回] をクリックして、新しい名前を入力します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-137">To rename a diagram: select the diagram in the Model Browser window, click once on the name, and type the new name.</span></span> <span data-ttu-id="35cc7-138">ダイアグラム名を右クリックし、[ **名前の変更**] を選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-138"> You can also right-click the diagram name and select **Rename**.</span></span>

<span data-ttu-id="35cc7-139">ダイアグラム名は、Visual Studio エディターで .edmx ファイル名の横に表示されます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-139">The diagram name is displayed next to the .edmx file name, in the Visual Studio editor.</span></span> <span data-ttu-id="35cc7-140">たとえば、Model1 \[ Diagram1 \] です。</span><span class="sxs-lookup"><span data-stu-id="35cc7-140">For example Model1.edmx\[Diagram1\].</span></span>

![ダイアグラム名](~/ef6/media/diagramname.png)

<span data-ttu-id="35cc7-142">図のコンテンツ (エンティティとアソシエーションの形状と色) は .edmx ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-142">The diagrams content (shape and color of entities and associations) is stored in the .edmx.diagram file.</span></span> <span data-ttu-id="35cc7-143">このファイルを表示するには、[ソリューションエクスプローラーを選択し、.edmx ファイルを展開します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-143">To view this file, select Solution Explorer and unfold the .edmx file.</span></span> 

![ダイアグラムファイル](~/ef6/media/diagramfiles.png)

<span data-ttu-id="35cc7-145">.Edmx ファイルを手動で編集しないでください。このファイルの内容は EF デザイナーによって上書きされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="35cc7-145">You should not edit the .edmx.diagram file manually, the content of this file maybe overwritten by the EF Designer.</span></span>
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a><span data-ttu-id="35cc7-146">エンティティとアソシエーションを新しいダイアグラムに分割する</span><span class="sxs-lookup"><span data-stu-id="35cc7-146">Splitting Entities and Associations into a New Diagram</span></span>

<span data-ttu-id="35cc7-147">既存のダイアグラムでエンティティを選択できます (Shift キーを押しながら複数のエンティティを選択します)。</span><span class="sxs-lookup"><span data-stu-id="35cc7-147">You can select entities on the existing diagram (hold Shift to select multiple entities).</span></span> <span data-ttu-id="35cc7-148">マウスの右ボタンをクリックし、[ **新しいダイアグラムに移動**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-148">Click the right mouse button and select **Move to new Diagram**.</span></span> <span data-ttu-id="35cc7-149">新しいダイアグラムが作成され、選択したエンティティとその関連がダイアグラムに移動します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-149">The new diagram is created and the selected entities and their associations are moved to the diagram.</span></span>

<span data-ttu-id="35cc7-150">または、モデルブラウザーで [ダイアグラム] フォルダーを右クリックし、[**新しいダイアグラムの追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-150">Alternatively, you can right-click the Diagrams folder in Model Browser and select **Add new Diagram.**</span></span> <span data-ttu-id="35cc7-151">その後、モデルブラウザーの [エンティティ型] フォルダーの下のエンティティをデザイン画面にドラッグアンドドロップできます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-151">You can then drag and drop entities from under the Entity Types folder in Model Browser onto the design surface.</span></span>

<span data-ttu-id="35cc7-152">また、1つのダイアグラムから (Ctrl + X キーまたは Ctrl + C キーを使用して) エンティティを切り取りまたはコピーし、もう一方に (Ctrl キーを使用して) 貼り付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-152">You can also cut or copy entities (using Ctrl-X or Ctrl-C keys) from one diagram and paste (using Ctrl-V key) on the other.</span></span> <span data-ttu-id="35cc7-153">エンティティを貼り付ける図に、同じ名前のエンティティが既に含まれている場合は、新しいエンティティが作成され、モデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-153">If the diagram into which you are pasting an entity already contains an entity with the same name, a new entity will be created and added to the model.</span></span><span data-ttu-id="35cc7-154">たとえば、Diagram2 に Department エンティティが含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="35cc7-154">  For example: Diagram2 contains the Department entity.</span></span> <span data-ttu-id="35cc7-155">次に、別の部署を Diagram2 に貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-155">Then, you paste another Department on Diagram2.</span></span> <span data-ttu-id="35cc7-156">Department1 エンティティが作成され、概念モデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-156">The Department1 entity is created and added to the conceptual model.</span></span>   

<span data-ttu-id="35cc7-157">関連エンティティを図に含めるには、エンティティを rick クリックし、[ **関連するものを含める**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-157">To include related entities in a diagram, rick-click the entity and select **Include Related**.</span></span> <span data-ttu-id="35cc7-158">これにより、指定された図に関連するエンティティとアソシエーションのコピーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-158">This will make a copy of the related entities and associations in the specified diagram.</span></span>

## <a name="changing-the-color-of-entities"></a><span data-ttu-id="35cc7-159">エンティティの色の変更</span><span class="sxs-lookup"><span data-stu-id="35cc7-159">Changing the Color of Entities</span></span>

<span data-ttu-id="35cc7-160">モデルを複数の図に分割するだけでなく、エンティティの色を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="35cc7-160">In addition to splitting a model into multiple diagrams, you can also change colors of your entities.</span></span>

<span data-ttu-id="35cc7-161">色を変更するには、デザイン画面で1つまたは複数のエンティティを選択します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-161">To change the color, select an entity (or multiple entities) on the design surface.</span></span> <span data-ttu-id="35cc7-162">次に、マウスの右ボタンをクリックし、[ **プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-162">Then, click the right mouse button and select **Properties**.</span></span> <span data-ttu-id="35cc7-163">プロパティウィンドウで、[ **塗りつぶしの色** ] プロパティを選択します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-163">In the Properties window, select the **Fill Color** property.</span></span> <span data-ttu-id="35cc7-164">有効な色の名前 (たとえば、赤) または有効な RGB (255、128、128など) のいずれかを使用して色を指定します。</span><span class="sxs-lookup"><span data-stu-id="35cc7-164">Specify the color using either a valid color name (for example, Red) or a valid RGB (for example, 255, 128, 128).</span></span> 

![色の変更](~/ef6/media/color.png)

## <a name="summary"></a><span data-ttu-id="35cc7-166">まとめ</span><span class="sxs-lookup"><span data-stu-id="35cc7-166">Summary</span></span>

<span data-ttu-id="35cc7-167">このトピックでは、モデルを複数の図に分割する方法と、Entity Framework Designer を使用してエンティティに異なる色を指定する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="35cc7-167">In this topic we looked at how to split a model into multiple diagrams and also how to specify a different color for an entity using the Entity Framework Designer.</span></span> 
