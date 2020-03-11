---
title: モデルごとの複数のダイアグラム-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: b95db5c8-de8d-43bd-9ccc-5df6a5e25e1b
ms.openlocfilehash: e78b927a147143629ac5b73e23edf439ba6d0264
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415119"
---
# <a name="multiple-diagrams-per-model"></a><span data-ttu-id="ac3a9-102">モデルごとに複数のダイアグラム</span><span class="sxs-lookup"><span data-stu-id="ac3a9-102">Multiple Diagrams per Model</span></span>
> [!NOTE]
> <span data-ttu-id="ac3a9-103">**EF5**以降: このページで説明した機能、api などは、Entity Framework 5 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="ac3a9-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="ac3a9-105">このビデオとページでは、Entity Framework Designer (EF Designer) を使用してモデルを複数の図に分割する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-105">This video and page shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="ac3a9-106">モデルが大きすぎて表示または編集できない場合は、この機能を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-106">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="ac3a9-107">以前のバージョンの EF デザイナーでは、EDMX ファイルごとに1つの図しか使用できませんでした。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-107">In earlier versions of the EF Designer you could only have one diagram per the EDMX file.</span></span> <span data-ttu-id="ac3a9-108">Visual Studio 2012 以降では、EF デザイナーを使用して、EDMX ファイルを複数のダイアグラムに分割できます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-108">Starting with Visual Studio 2012, you can use the EF Designer to split your EDMX file into multiple diagrams.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="ac3a9-109">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="ac3a9-109">Watch the video</span></span>
<span data-ttu-id="ac3a9-110">このビデオでは、Entity Framework Designer (EF Designer) を使用してモデルを複数の図に分割する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-110">This video shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="ac3a9-111">モデルが大きすぎて表示または編集できない場合は、この機能を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-111">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="ac3a9-112">**表示者**: ジュリアつい</span><span class="sxs-lookup"><span data-stu-id="ac3a9-112">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="ac3a9-113">**ビデオ**: [wmv](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [wmv (ZIP)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span><span class="sxs-lookup"><span data-stu-id="ac3a9-113">**Video**: [WMV](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span></span>

## <a name="ef-designer-overview"></a><span data-ttu-id="ac3a9-114">EF デザイナーの概要</span><span class="sxs-lookup"><span data-stu-id="ac3a9-114">EF Designer Overview</span></span>

<span data-ttu-id="ac3a9-115">EF デザイナーの Entity Data Model ウィザードを使用してモデルを作成すると、.edmx ファイルが作成され、ソリューションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-115">When you create a model using the EF Designer’s Entity Data Model Wizard, an .edmx file is created and added to your solution.</span></span> <span data-ttu-id="ac3a9-116">このファイルでは、エンティティの形状と、エンティティをデータベースにマップする方法を定義します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-116">This file defines the shape of your entities and how they map to the database.</span></span>

<span data-ttu-id="ac3a9-117">EF デザイナーは、次のコンポーネントで構成されています。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-117">The EF Designer consists of the following components:</span></span>

-   <span data-ttu-id="ac3a9-118">モデルを編集するためのビジュアルデザインサーフェイス。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-118">A visual design surface for editing the model.</span></span> <span data-ttu-id="ac3a9-119">エンティティとアソシエーションの作成、変更、または削除を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-119">You can create, modify, or delete entities and associations.</span></span>
-   <span data-ttu-id="ac3a9-120">モデル **ブラウザー** ウィンドウ。モデルのツリービューが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-120">A **Model Browser** window that provides tree views of the model.</span></span><span data-ttu-id="ac3a9-121">  エンティティとその関連付けは、 *\[ModelName\]* フォルダーの下にあります。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-121">  The entities and their associations are located under the *\[ModelName\]* folder.</span></span> <span data-ttu-id="ac3a9-122">データベースのテーブルと制約は、 *\[ModelName\]* の下にあります。ストアフォルダー。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-122">The database tables and constraints are located under the *\[ModelName\]*.Store folder.</span></span>
-   <span data-ttu-id="ac3a9-123">マッピングを表示および編集するための [ **マッピングの詳細**] ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-123">A **Mapping Details** window for viewing and editing mappings.</span></span> <span data-ttu-id="ac3a9-124">エンティティ型またはアソシエーションをデータベース テーブル、列、およびストアド プロシージャにマップできます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-124">You can map entity types or associations to database tables, columns, and stored procedures.</span></span> 

<span data-ttu-id="ac3a9-125">Entity Data Model ウィザードが終了すると、ビジュアルデザインサーフェイスウィンドウが自動的に開きます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-125">The visual design surface window is automatically opened when the Entity Data Model Wizard finishes.</span></span> <span data-ttu-id="ac3a9-126">モデルブラウザーが表示されていない場合は、メインのデザイン画面を右クリックし、[ **モデルブラウザー**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-126">If the Model Browser is not visible, right-click the main design surface and select **Model Browser**.</span></span>

<span data-ttu-id="ac3a9-127">次のスクリーンショットは、EF デザイナーで開かれた .edmx ファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-127">The following screenshot shows an .edmx file opened in the EF Designer.</span></span> <span data-ttu-id="ac3a9-128">スクリーンショットには、ビジュアルデザイン画面 (左側) と [ **モデルブラウザー** ] ウィンドウ (右側) が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-128">The screenshot shows the visual design surface (to the left) and the **Model Browser** window (to the right).</span></span>

![EF デザイナー2](~/ef6/media/efdesigner2.png)

<span data-ttu-id="ac3a9-130">EF デザイナーで実行した操作を元に戻すには、Ctrl + Z キーを押します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-130">To undo an operation done in the EF Designer, click Ctrl-Z.</span></span>

## <a name="working-with-diagrams"></a><span data-ttu-id="ac3a9-131">ダイアグラムの操作</span><span class="sxs-lookup"><span data-stu-id="ac3a9-131">Working with Diagrams</span></span>

<span data-ttu-id="ac3a9-132">既定では、EF デザイナーは Diagram1 という1つのダイアグラムを作成します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-132">By default the EF Designer creates one diagram called Diagram1.</span></span> <span data-ttu-id="ac3a9-133">多数のエンティティとアソシエーションを含む図がある場合は、それらを論理的に分割することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-133">If you have a diagram with a large number of entities and associations, you will most like want to split them up logically.</span></span> <span data-ttu-id="ac3a9-134">Visual Studio 2012 以降では、複数の図で概念モデルを表示できます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-134">Starting with Visual Studio 2012, you can view your conceptual model in multiple diagrams.</span></span>   

<span data-ttu-id="ac3a9-135">新しいダイアグラムを追加すると、モデルブラウザーウィンドウの [ダイアグラム] フォルダーの下に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-135">As you add new diagrams, they appear under the Diagrams folder in the Model Browser window.</span></span> <span data-ttu-id="ac3a9-136">ダイアグラムの名前を変更するには、[モデルブラウザー] ウィンドウでダイアグラムを選択し、[名前] で [1 回] をクリックして、新しい名前を入力します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-136">To rename a diagram: select the diagram in the Model Browser window, click once on the name, and type the new name.</span></span> <span data-ttu-id="ac3a9-137"> ダイアグラム名を右クリックし、 **[名前の変更]** を選択することもできます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-137"> You can also right-click the diagram name and select **Rename**.</span></span>

<span data-ttu-id="ac3a9-138">ダイアグラム名は、Visual Studio エディターで .edmx ファイル名の横に表示されます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-138">The diagram name is displayed next to the .edmx file name, in the Visual Studio editor.</span></span> <span data-ttu-id="ac3a9-139">たとえば、Model1\[Diagram1\]です。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-139">For example Model1.edmx\[Diagram1\].</span></span>

![ダイアグラム名](~/ef6/media/diagramname.png)

<span data-ttu-id="ac3a9-141">図のコンテンツ (エンティティとアソシエーションの形状と色) は .edmx ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-141">The diagrams content (shape and color of entities and associations) is stored in the .edmx.diagram file.</span></span> <span data-ttu-id="ac3a9-142">このファイルを表示するには、ソリューションエクスプローラーを選択し、.edmx ファイルを展開します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-142">To view this file, select Solution Explorer and unfold the .edmx file.</span></span> 

![ダイアグラムファイル](~/ef6/media/diagramfiles.png)

<span data-ttu-id="ac3a9-144">.Edmx ファイルを手動で編集しないでください。このファイルの内容は EF デザイナーによって上書きされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-144">You should not edit the .edmx.diagram file manually, the content of this file maybe overwritten by the EF Designer.</span></span>
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a><span data-ttu-id="ac3a9-145">エンティティとアソシエーションを新しいダイアグラムに分割する</span><span class="sxs-lookup"><span data-stu-id="ac3a9-145">Splitting Entities and Associations into a New Diagram</span></span>

<span data-ttu-id="ac3a9-146">既存のダイアグラムでエンティティを選択できます (Shift キーを押しながら複数のエンティティを選択します)。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-146">You can select entities on the existing diagram (hold Shift to select multiple entities).</span></span> <span data-ttu-id="ac3a9-147">マウスの右ボタンをクリックし、 **[新しいダイアグラムに移動]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-147">Click the right mouse button and select **Move to new Diagram**.</span></span> <span data-ttu-id="ac3a9-148">新しいダイアグラムが作成され、選択したエンティティとその関連がダイアグラムに移動します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-148">The new diagram is created and the selected entities and their associations are moved to the diagram.</span></span>

<span data-ttu-id="ac3a9-149">または、モデルブラウザーで ダイアグラム フォルダーを右クリックし、**新しいダイアグラムの追加** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-149">Alternatively, you can right-click the Diagrams folder in Model Browser and select **Add new Diagram.**</span></span> <span data-ttu-id="ac3a9-150">その後、モデルブラウザーの [エンティティ型] フォルダーの下のエンティティをデザイン画面にドラッグアンドドロップできます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-150">You can then drag and drop entities from under the Entity Types folder in Model Browser onto the design surface.</span></span>

<span data-ttu-id="ac3a9-151">また、1つのダイアグラムから (Ctrl + X キーまたは Ctrl + C キーを使用して) エンティティを切り取りまたはコピーし、もう一方に (Ctrl キーを使用して) 貼り付けることもできます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-151">You can also cut or copy entities (using Ctrl-X or Ctrl-C keys) from one diagram and paste (using Ctrl-V key) on the other.</span></span> <span data-ttu-id="ac3a9-152">エンティティを貼り付ける図に、同じ名前のエンティティが既に含まれている場合は、新しいエンティティが作成され、モデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-152">If the diagram into which you are pasting an entity already contains an entity with the same name, a new entity will be created and added to the model.</span></span><span data-ttu-id="ac3a9-153">  たとえば、Diagram2 に Department エンティティが含まれているとします。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-153">  For example: Diagram2 contains the Department entity.</span></span> <span data-ttu-id="ac3a9-154">次に、別の部署を Diagram2 に貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-154">Then, you paste another Department on Diagram2.</span></span> <span data-ttu-id="ac3a9-155">Department1 エンティティが作成され、概念モデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-155">The Department1 entity is created and added to the conceptual model.</span></span>   

<span data-ttu-id="ac3a9-156">関連エンティティを図に含めるには、エンティティを rick クリックし、 **[関連するものを含める]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-156">To include related entities in a diagram, rick-click the entity and select **Include Related**.</span></span> <span data-ttu-id="ac3a9-157">これにより、指定された図に関連するエンティティとアソシエーションのコピーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-157">This will make a copy of the related entities and associations in the specified diagram.</span></span>

## <a name="changing-the-color-of-entities"></a><span data-ttu-id="ac3a9-158">エンティティの色の変更</span><span class="sxs-lookup"><span data-stu-id="ac3a9-158">Changing the Color of Entities</span></span>

<span data-ttu-id="ac3a9-159">モデルを複数の図に分割するだけでなく、エンティティの色を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-159">In addition to splitting a model into multiple diagrams, you can also change colors of your entities.</span></span>

<span data-ttu-id="ac3a9-160">色を変更するには、デザイン画面で1つまたは複数のエンティティを選択します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-160">To change the color, select an entity (or multiple entities) on the design surface.</span></span> <span data-ttu-id="ac3a9-161">次に、マウスの右ボタンをクリックし、 **[プロパティ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-161">Then, click the right mouse button and select **Properties**.</span></span> <span data-ttu-id="ac3a9-162">プロパティウィンドウで、 **[塗りつぶしの色]** プロパティを選択します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-162">In the Properties window, select the **Fill Color** property.</span></span> <span data-ttu-id="ac3a9-163">有効な色の名前 (たとえば、赤) または有効な RGB (255、128、128など) のいずれかを使用して色を指定します。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-163">Specify the color using either a valid color name (for example, Red) or a valid RGB (for example, 255, 128, 128).</span></span> 

![Color](~/ef6/media/color.png)

## <a name="summary"></a><span data-ttu-id="ac3a9-165">まとめ</span><span class="sxs-lookup"><span data-stu-id="ac3a9-165">Summary</span></span>

<span data-ttu-id="ac3a9-166">このトピックでは、モデルを複数の図に分割する方法と、Entity Framework Designer を使用してエンティティに異なる色を指定する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="ac3a9-166">In this topic we looked at how to split a model into multiple diagrams and also how to specify a different color for an entity using the Entity Framework Designer.</span></span> 
