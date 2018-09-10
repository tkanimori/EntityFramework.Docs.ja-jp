---
title: 1 つのモデルの EF6 の複数のダイアグラム
author: divega
ms.date: 2016-10-23
ms.assetid: b95db5c8-de8d-43bd-9ccc-5df6a5e25e1b
ms.openlocfilehash: e23bf92ce3199b2162ca9a42bd0f797375179d77
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250960"
---
# <a name="multiple-diagrams-per-model"></a><span data-ttu-id="30abf-102">1 つのモデルの複数のダイアグラム</span><span class="sxs-lookup"><span data-stu-id="30abf-102">Multiple Diagrams per Model</span></span>
> [!NOTE]
> <span data-ttu-id="30abf-103">**EF5 以降のみ**-機能、Api、Entity Framework 5 で導入されたなどのこのページで説明します。</span><span class="sxs-lookup"><span data-stu-id="30abf-103">**EF5 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 5.</span></span> <span data-ttu-id="30abf-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="30abf-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="30abf-105">このビデオおよびページは、Entity Framework デザイナー (EF Designer) を使用して複数の図にモデルを分割する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="30abf-105">This video and page shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="30abf-106">この機能を表示または編集するには、モデルが大きすぎる場合に使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="30abf-106">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="30abf-107">以前のバージョンの EF Designer で EDMX ファイルあたり 1 つの図をしかありませんでした。</span><span class="sxs-lookup"><span data-stu-id="30abf-107">In earlier versions of the EF Designer you could only have one diagram per the EDMX file.</span></span> <span data-ttu-id="30abf-108">Visual Studio 2012 以降では、EDMX ファイルを分割して複数のダイアグラムに EF Designer を使用できます。</span><span class="sxs-lookup"><span data-stu-id="30abf-108">Starting with Visual Studio 2012, you can use the EF Designer to split your EDMX file into multiple diagrams.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="30abf-109">ビデオを見る</span><span class="sxs-lookup"><span data-stu-id="30abf-109">Watch the video</span></span>
<span data-ttu-id="30abf-110">このビデオでは、Entity Framework デザイナー (EF Designer) を使用して複数の図にモデルを分割する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="30abf-110">This video shows how to split a model into multiple diagrams using the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="30abf-111">この機能を表示または編集するには、モデルが大きすぎる場合に使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="30abf-111">You might want to use this feature when your model becomes too large to view or edit.</span></span>

<span data-ttu-id="30abf-112">**によって提示される**: Julia Kornich</span><span class="sxs-lookup"><span data-stu-id="30abf-112">**Presented By**: Julia Kornich</span></span>

<span data-ttu-id="30abf-113">**ビデオ**: [WMV](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span><span class="sxs-lookup"><span data-stu-id="30abf-113">**Video**: [WMV](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.wmv) | [MP4](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-mp4video-multiplediagrams.m4v) | [WMV (ZIP)](http://download.microsoft.com/download/5/C/2/5C2B52AB-5532-426F-B078-1E253341B5FA/HDI-ITPro-MSDN-winvideo-multiplediagrams.zip)</span></span>

## <a name="ef-designer-overview"></a><span data-ttu-id="30abf-114">EF デザイナーの概要</span><span class="sxs-lookup"><span data-stu-id="30abf-114">EF Designer Overview</span></span>

<span data-ttu-id="30abf-115">EF デザイナーの Entity Data Model ウィザードを使用して、モデルを作成するときに .edmx ファイルは作成され、ソリューションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="30abf-115">When you create a model using the EF Designer’s Entity Data Model Wizard, an .edmx file is created and added to your solution.</span></span> <span data-ttu-id="30abf-116">このファイルは、エンティティとデータベースへの割り当て方法の形状を定義します。</span><span class="sxs-lookup"><span data-stu-id="30abf-116">This file defines the shape of your entities and how they map to the database.</span></span>

<span data-ttu-id="30abf-117">EF デザイナーは、次のコンポーネントで構成されます。</span><span class="sxs-lookup"><span data-stu-id="30abf-117">The EF Designer consists of the following components:</span></span>

-   <span data-ttu-id="30abf-118">モデルを編集するためのビジュアル デ ザイン サーフェイス。</span><span class="sxs-lookup"><span data-stu-id="30abf-118">A visual design surface for editing the model.</span></span> <span data-ttu-id="30abf-119">エンティティとアソシエーションの作成、変更、または削除を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="30abf-119">You can create, modify, or delete entities and associations.</span></span>
-   <span data-ttu-id="30abf-120">A**モデル ブラウザー**モデルのツリー ビューを提供するウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="30abf-120">A **Model Browser** window that provides tree views of the model.</span></span>  <span data-ttu-id="30abf-121">エンティティとその関連付けが下にある、 *\[ModelName\]* フォルダー。</span><span class="sxs-lookup"><span data-stu-id="30abf-121">The entities and their associations are located under the *\[ModelName\]* folder.</span></span> <span data-ttu-id="30abf-122">データベースのテーブルと制約が下にある、  *\[ModelName\]* します。フォルダーを格納します。</span><span class="sxs-lookup"><span data-stu-id="30abf-122">The database tables and constraints are located under the *\[ModelName\]*.Store folder.</span></span>
-   <span data-ttu-id="30abf-123">A**マッピングの詳細**ウィンドウの表示とのマッピングを編集します。</span><span class="sxs-lookup"><span data-stu-id="30abf-123">A **Mapping Details** window for viewing and editing mappings.</span></span> <span data-ttu-id="30abf-124">エンティティ型またはアソシエーションをデータベース テーブル、列、およびストアド プロシージャにマップできます。</span><span class="sxs-lookup"><span data-stu-id="30abf-124">You can map entity types or associations to database tables, columns, and stored procedures.</span></span> 

<span data-ttu-id="30abf-125">Entity Data Model ウィザードが完了すると、ビジュアル デ ザイン サーフェイス ウィンドウが自動的に開きます。</span><span class="sxs-lookup"><span data-stu-id="30abf-125">The visual design surface window is automatically opened when the Entity Data Model Wizard finishes.</span></span> <span data-ttu-id="30abf-126">モデル ブラウザーが表示されない場合、メインのデザイン サーフェスと選択を右クリックして**モデル ブラウザー**します。</span><span class="sxs-lookup"><span data-stu-id="30abf-126">If the Model Browser is not visible, right-click the main design surface and select **Model Browser**.</span></span>

<span data-ttu-id="30abf-127">次のスクリーン ショットは、EF デザイナーで開いた .edmx ファイルを示しています。</span><span class="sxs-lookup"><span data-stu-id="30abf-127">The following screenshot shows an .edmx file opened in the EF Designer.</span></span> <span data-ttu-id="30abf-128">スクリーン ショット (左側) に、ビジュアル デ ザイン サーフェイスと**モデル ブラウザー**ウィンドウ (右側) にします。</span><span class="sxs-lookup"><span data-stu-id="30abf-128">The screenshot shows the visual design surface (to the left) and the **Model Browser** window (to the right).</span></span>

![EF Designer 2](~/ef6/media/efdesigner2.png)

<span data-ttu-id="30abf-130">EF Designer で実行する操作を元に戻すには、CTRL + Z をクリックします。</span><span class="sxs-lookup"><span data-stu-id="30abf-130">To undo an operation done in the EF Designer, click Ctrl-Z.</span></span>

## <a name="working-with-diagrams"></a><span data-ttu-id="30abf-131">ダイアグラムの使用</span><span class="sxs-lookup"><span data-stu-id="30abf-131">Working with Diagrams</span></span>

<span data-ttu-id="30abf-132">既定では、EF Designer は Diagram1 と呼ばれる 1 つの図を作成します。</span><span class="sxs-lookup"><span data-stu-id="30abf-132">By default the EF Designer creates one diagram called Diagram1.</span></span> <span data-ttu-id="30abf-133">エンティティとアソシエーションの数が多い図がある場合は、論理的に分割する最もなどされます。</span><span class="sxs-lookup"><span data-stu-id="30abf-133">If you have a diagram with a large number of entities and associations, you will most like want to split them up logically.</span></span> <span data-ttu-id="30abf-134">Visual Studio 2012 以降では、複数のダイアグラムで、概念モデルを表示できます。</span><span class="sxs-lookup"><span data-stu-id="30abf-134">Starting with Visual Studio 2012, you can view your conceptual model in multiple diagrams.</span></span>   

<span data-ttu-id="30abf-135">新しいダイアグラムを追加すると、モデル ブラウザー ウィンドウで、ダイアグラム フォルダーの下で表示されます。</span><span class="sxs-lookup"><span data-stu-id="30abf-135">As you add new diagrams, they appear under the Diagrams folder in the Model Browser window.</span></span> <span data-ttu-id="30abf-136">ダイアグラムの名前を変更する: モデル ブラウザー ウィンドウで、ダイアグラムを選択、名をクリックして新しい名前を入力します。</span><span class="sxs-lookup"><span data-stu-id="30abf-136">To rename a diagram: select the diagram in the Model Browser window, click once on the name, and type the new name.</span></span>  <span data-ttu-id="30abf-137">ダイアグラム名を右クリックして選択できますも**の名前を変更**します。</span><span class="sxs-lookup"><span data-stu-id="30abf-137">You can also right-click the diagram name and select **Rename**.</span></span>

<span data-ttu-id="30abf-138">ダイアグラムの名前は、Visual Studio エディターで、.edmx ファイル名の横に表示されます。</span><span class="sxs-lookup"><span data-stu-id="30abf-138">The diagram name is displayed next to the .edmx file name, in the Visual Studio editor.</span></span> <span data-ttu-id="30abf-139">たとえば Model1.edmx\[Diagram1\]します。</span><span class="sxs-lookup"><span data-stu-id="30abf-139">For example Model1.edmx\[Diagram1\].</span></span>

![ダイアグラム名](~/ef6/media/diagramname.png)

<span data-ttu-id="30abf-141">図のコンテンツ (図形およびエンティティとアソシエーションの色) に保存されて、。 edmx.diagram ファイル。</span><span class="sxs-lookup"><span data-stu-id="30abf-141">The diagrams content (shape and color of entities and associations) is stored in the .edmx.diagram file.</span></span> <span data-ttu-id="30abf-142">このファイルを表示するには、ソリューション エクスプ ローラーを選択し、.edmx ファイルを展開します。</span><span class="sxs-lookup"><span data-stu-id="30abf-142">To view this file, select Solution Explorer and unfold the .edmx file.</span></span> 

![DiagramFiles](~/ef6/media/diagramfiles.png)

<span data-ttu-id="30abf-144">編集しないでください、。 edmx.diagram ファイルを手動で EF Designer によって上書きされる可能性があります、このファイルの内容。</span><span class="sxs-lookup"><span data-stu-id="30abf-144">You should not edit the .edmx.diagram file manually, the content of this file maybe overwritten by the EF Designer.</span></span>
 
## <a name="splitting-entities-and-associations-into-a-new-diagram"></a><span data-ttu-id="30abf-145">エンティティとアソシエーションを新しいダイアグラムの分割</span><span class="sxs-lookup"><span data-stu-id="30abf-145">Splitting Entities and Associations into a New Diagram</span></span>

<span data-ttu-id="30abf-146">既存のダイアグラム (複数のエンティティを選択するには、shift キーを押し) 上のエンティティを選択できます。</span><span class="sxs-lookup"><span data-stu-id="30abf-146">You can select entities on the existing diagram (hold Shift to select multiple entities).</span></span> <span data-ttu-id="30abf-147">マウスの右ボタンをクリックし、選択**新しい図へ移動**します。</span><span class="sxs-lookup"><span data-stu-id="30abf-147">Click the right mouse button and select **Move to new Diagram**.</span></span> <span data-ttu-id="30abf-148">新しいダイアグラムが作成され、選択したエンティティとその関連付けがダイアグラムに移動されます。</span><span class="sxs-lookup"><span data-stu-id="30abf-148">The new diagram is created and the selected entities and their associations are moved to the diagram.</span></span>

<span data-ttu-id="30abf-149">または、モデル ブラウザーで、[ダイアグラム] フォルダーを右クリックし、選択**新しいダイアグラムを追加します。**</span><span class="sxs-lookup"><span data-stu-id="30abf-149">Alternatively, you can right-click the Diagrams folder in Model Browser and select **Add new Diagram.**</span></span> <span data-ttu-id="30abf-150">ドラッグし、デザイン サーフェイスにモデル ブラウザーでエンティティ型のフォルダーの下のエンティティを削除することができます。</span><span class="sxs-lookup"><span data-stu-id="30abf-150">You can then drag and drop entities from under the Entity Types folder in Model Browser onto the design surface.</span></span>

<span data-ttu-id="30abf-151">切り取り、(CTRL + X または Ctrl + C キーを使用して) エンティティを 1 つの図からコピーまたはし、他の (ctrl + V キーを使用) を貼り付けるなどもできます。</span><span class="sxs-lookup"><span data-stu-id="30abf-151">You can also cut or copy entities (using Ctrl-X or Ctrl-C keys) from one diagram and paste (using Ctrl-V key) on the other.</span></span> <span data-ttu-id="30abf-152">貼り付けるエンティティ既にダイアグラムには、同じ名前のエンティティが含まれて、新しいエンティティが作成され、モデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="30abf-152">If the diagram into which you are pasting an entity already contains an entity with the same name, a new entity will be created and added to the model.</span></span>  <span data-ttu-id="30abf-153">例: Diagram2 に Department エンティティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="30abf-153">For example: Diagram2 contains the Department entity.</span></span> <span data-ttu-id="30abf-154">次に、Diagram2 に別の部門を貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="30abf-154">Then, you paste another Department on Diagram2.</span></span> <span data-ttu-id="30abf-155">Department1 エンティティが作成され、概念モデルに追加します。</span><span class="sxs-lookup"><span data-stu-id="30abf-155">The Department1 entity is created and added to the conceptual model.</span></span>   

<span data-ttu-id="30abf-156">関連エンティティをダイアグラムに含めるにエンティティを右クリックしますし、選択**関連含める**します。</span><span class="sxs-lookup"><span data-stu-id="30abf-156">To include related entities in a diagram, rick-click the entity and select **Include Related**.</span></span> <span data-ttu-id="30abf-157">指定したダイアグラムで関連エンティティとアソシエーションは、のコピーをこのように。</span><span class="sxs-lookup"><span data-stu-id="30abf-157">This will make a copy of the related entities and associations in the specified diagram.</span></span>

## <a name="changing-the-color-of-entities"></a><span data-ttu-id="30abf-158">エンティティの色を変更します。</span><span class="sxs-lookup"><span data-stu-id="30abf-158">Changing the Color of Entities</span></span>

<span data-ttu-id="30abf-159">だけでなく、モデルを複数のダイアグラムに分割する、エンティティの色を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="30abf-159">In addition to splitting a model into multiple diagrams, you can also change colors of your entities.</span></span>

<span data-ttu-id="30abf-160">色を変更するには、デザイン画面でエンティティ (または複数のエンティティ) を選択します。</span><span class="sxs-lookup"><span data-stu-id="30abf-160">To change the color, select an entity (or multiple entities) on the design surface.</span></span> <span data-ttu-id="30abf-161">次に、マウスの右ボタンをクリックし、選択**プロパティ**します。</span><span class="sxs-lookup"><span data-stu-id="30abf-161">Then, click the right mouse button and select **Properties**.</span></span> <span data-ttu-id="30abf-162">[プロパティ] ウィンドウで、選択、**塗りつぶしの色**プロパティ。</span><span class="sxs-lookup"><span data-stu-id="30abf-162">In the Properties window, select the **Fill Color** property.</span></span> <span data-ttu-id="30abf-163">有効な色の名前 (たとえば、赤) または、有効な RGB (255, 128, 128) を使用して色を指定します。</span><span class="sxs-lookup"><span data-stu-id="30abf-163">Specify the color using either a valid color name (for example, Red) or a valid RGB (for example, 255, 128, 128).</span></span> 

![色](~/ef6/media/color.png)

## <a name="summary"></a><span data-ttu-id="30abf-165">まとめ</span><span class="sxs-lookup"><span data-stu-id="30abf-165">Summary</span></span>

<span data-ttu-id="30abf-166">このトピックでは、Entity Framework デザイナーを使用してエンティティを別の色を指定する方法と、モデルを複数のダイアグラムに分割する方法をについて説明しました。</span><span class="sxs-lookup"><span data-stu-id="30abf-166">In this topic we looked at how to split a model into multiple diagrams and also how to specify a different color for an entity using the Entity Framework Designer.</span></span> 
