---
title: デザイナーの TPH 継承 - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 72d26a8e-20ab-4500-bd13-394a08e73394
caps.latest.revision: 3
ms.openlocfilehash: 0a017d3b97808cede3134119940b2e5839d0f282
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122284"
---
# <a name="designer-tph-inheritance"></a><span data-ttu-id="31977-102">デザイナーの TPH 継承</span><span class="sxs-lookup"><span data-stu-id="31977-102">Designer TPH Inheritance</span></span>
<span data-ttu-id="31977-103">このステップ バイ ステップ チュートリアルでは、Entity Framework デザイナー (EF Designer) を使用した概念モデルでの table-per-hierarchy (TPH) 継承を実装する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="31977-103">This step-by-step walkthrough shows how to implement table-per-hierarchy (TPH) inheritance in your conceptual model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="31977-104">TPH 継承は、継承階層内のエンティティ型のすべてのデータを維持するために、1 つのデータベース テーブルを使用します。</span><span class="sxs-lookup"><span data-stu-id="31977-104">TPH inheritance uses one database table to maintain data for all of the entity types in an inheritance hierarchy.</span></span>

<span data-ttu-id="31977-105">このチュートリアルでは次の 3 つのエンティティ型を Person テーブルにマップします。 (基本型) のユーザー、学生 (個人から派生)、およびインストラクター (個人から派生)。</span><span class="sxs-lookup"><span data-stu-id="31977-105">In this walkthrough we will map the Person table to three entity types: Person (the base type), Student (derives from Person), and Instructor (derives from Person).</span></span> <span data-ttu-id="31977-106">(Database First) のデータベースから概念モデルを作成し、その EF デザイナーを使用して TPH 継承を実装するモデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="31977-106">We'll create a conceptual model from the database (Database First) and then alter the model to implement the TPH inheritance using the EF Designer.</span></span>

<span data-ttu-id="31977-107">Model First を使用して TPH 継承にマップすることは、複雑なは、独自のデータベース生成ワークフローを記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="31977-107">It is possible to map to a TPH inheritance using Model First but you would have to write your own database generation workflow which is complex.</span></span> <span data-ttu-id="31977-108">このワークフローを割り当てると、**データベース生成ワークフロー** EF Designer でのプロパティ。</span><span class="sxs-lookup"><span data-stu-id="31977-108">You would then assign this workflow to the **Database Generation Workflow** property in the EF Designer.</span></span> <span data-ttu-id="31977-109">簡単に別の方法では、Code First を使用します。</span><span class="sxs-lookup"><span data-stu-id="31977-109">An easier alternative is to use Code First.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="31977-110">その他の継承オプション</span><span class="sxs-lookup"><span data-stu-id="31977-110">Other Inheritance Options</span></span>

<span data-ttu-id="31977-111">テーブルあたり型 (TPT) が、別の種類の継承をデータベース内の別のテーブルは、継承に参加するエンティティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="31977-111">Table-per-Type (TPT) is another type of inheritance in which separate tables in the database are mapped to entities that participate in the inheritance.</span></span>  <span data-ttu-id="31977-112">EF デザイナーを使用したテーブルの種類ごとの継承にマップする方法については、次を参照してください。 [EF デザイナー TPT 継承](~/ef6/modeling/designer/inheritance/tpt.md)します。</span><span class="sxs-lookup"><span data-stu-id="31977-112">For information about how to map Table-per-Type inheritance with the EF Designer, see [EF Designer TPT Inheritance](~/ef6/modeling/designer/inheritance/tpt.md).</span></span>

<span data-ttu-id="31977-113">テーブル-ごとの具象型の継承 (TPC) と混在継承モデルは、Entity Framework ランタイムによってサポートされますが、EF Designer ではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="31977-113">Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="31977-114">2 つのオプションがある TPC または混在の継承を使用する場合は、: Code First を使用して、または EDMX ファイルを手動で編集します。</span><span class="sxs-lookup"><span data-stu-id="31977-114">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="31977-115">EDMX ファイルを使用する場合は、マッピングの詳細 ウィンドウは、「セーフ モード」に格納されます。 そして、デザイナーを使用して、マッピング変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="31977-115">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="31977-116">前提条件</span><span class="sxs-lookup"><span data-stu-id="31977-116">Prerequisites</span></span>

<span data-ttu-id="31977-117">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="31977-117">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="31977-118">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="31977-118">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="31977-119">[School サンプル データベース](~/ef6/resources/school-database.md)します。</span><span class="sxs-lookup"><span data-stu-id="31977-119">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="31977-120">プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="31977-120">Set up the Project</span></span>

-   <span data-ttu-id="31977-121">Visual Studio 2012 を開きます。</span><span class="sxs-lookup"><span data-stu-id="31977-121">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="31977-122">選択**ファイル -&gt;新機能 -&gt;プロジェクト**</span><span class="sxs-lookup"><span data-stu-id="31977-122">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="31977-123">左側のウィンドウで次のようにクリックします。 **Visual C\#** を選び、**コンソール**テンプレート。</span><span class="sxs-lookup"><span data-stu-id="31977-123">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="31977-124">入力**TPHDBFirstSample**名として。</span><span class="sxs-lookup"><span data-stu-id="31977-124">Enter **TPHDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="31977-125">**[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="31977-125">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="31977-126">モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="31977-126">Create a Model</span></span>

-   <span data-ttu-id="31977-127">ソリューション エクスプ ローラーでプロジェクト名を右クリックして**追加 -&gt;新しい項目の**します。</span><span class="sxs-lookup"><span data-stu-id="31977-127">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="31977-128">選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。</span><span class="sxs-lookup"><span data-stu-id="31977-128">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="31977-129">入力**TPHModel.edmx**のファイル名、およびクリック**追加**します。</span><span class="sxs-lookup"><span data-stu-id="31977-129">Enter **TPHModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="31977-130">モデルのコンテンツの選択 ダイアログ ボックスで、次のように選択します。**データベースから生成**、 をクリックし、**次**。</span><span class="sxs-lookup"><span data-stu-id="31977-130">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="31977-131">クリックして**新しい接続**します。</span><span class="sxs-lookup"><span data-stu-id="31977-131">Click **New Connection**.</span></span>
    <span data-ttu-id="31977-132">接続のプロパティ ダイアログ ボックスで、サーバー名を入力します (たとえば、 **(localdb)\\mssqllocaldb**) を選択します、認証方式として、型**学校**データベース名、し、。クリックして**OK**します。</span><span class="sxs-lookup"><span data-stu-id="31977-132">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="31977-133">データ接続の選択 ダイアログ ボックスは、データベース接続の設定で更新されます。</span><span class="sxs-lookup"><span data-stu-id="31977-133">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="31977-134">データベース オブジェクトの選択 ダイアログ ボックスの テーブル ノードを選択、 **Person**テーブル。</span><span class="sxs-lookup"><span data-stu-id="31977-134">In the Choose Your Database Objects dialog box, under the Tables node, select the **Person** table.</span></span>
-   <span data-ttu-id="31977-135">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="31977-135">Click **Finish**.</span></span>

<span data-ttu-id="31977-136">モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="31977-136">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="31977-137">[データベース オブジェクトの選択] ダイアログ ボックスで選択したすべてのオブジェクトは、モデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="31977-137">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

<span data-ttu-id="31977-138">つまり方法、**人**にデータベース テーブルを検索します。</span><span class="sxs-lookup"><span data-stu-id="31977-138">That is how the **Person** table looks in the database.</span></span>

![PersonTable](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a><span data-ttu-id="31977-140">Table-per-hierarchy 継承を実装します。</span><span class="sxs-lookup"><span data-stu-id="31977-140">Implement Table-per-Hierarchy Inheritance</span></span>

<span data-ttu-id="31977-141">**Person**テーブルには、**識別子**列は、2 つの値のいずれかの:"Student"および"Instructor"。</span><span class="sxs-lookup"><span data-stu-id="31977-141">The **Person** table has the **Discriminator** column, which can have one of two values: “Student” and “Instructor”.</span></span> <span data-ttu-id="31977-142">値に応じて、 **Person**テーブルにマップされる、**学生**エンティティまたは**インストラクター**エンティティ。</span><span class="sxs-lookup"><span data-stu-id="31977-142">Depending on the value the **Person** table will be mapped to the **Student** entity or the **Instructor** entity.</span></span> <span data-ttu-id="31977-143">**Person**テーブルに 2 つの列もあります**HireDate**と**EnrollmentDate**、する必要があります**null 許容**人ができないため、学生およびインストラクターと同時に (少なくともでこのチュートリアルでは)。</span><span class="sxs-lookup"><span data-stu-id="31977-143">The **Person** table also has two columns, **HireDate** and **EnrollmentDate**, which must be **nullable** because a person cannot be a student and an instructor at the same time (at least not in this walkthrough).</span></span>

### <a name="add-new-entities"></a><span data-ttu-id="31977-144">新しいエンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="31977-144">Add new Entities</span></span>

-   <span data-ttu-id="31977-145">新しいエンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="31977-145">Add a new entity.</span></span>
    <span data-ttu-id="31977-146">これを行うには、Entity Framework デザイナーのデザイン画面の空の領域を右クリックして**追加 -&gt;エンティティ**します。</span><span class="sxs-lookup"><span data-stu-id="31977-146">To do this, right-click on an empty space of the design surface of the Entity Framework Designer, and select **Add-&gt;Entity**.</span></span>
-   <span data-ttu-id="31977-147">型**インストラクター**の**エンティティ名**選択と**人**のドロップダウン リストから、**基本型**します。</span><span class="sxs-lookup"><span data-stu-id="31977-147">Type **Instructor** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>
-   <span data-ttu-id="31977-148">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="31977-148">Click **OK**.</span></span>
-   <span data-ttu-id="31977-149">もう 1 つの新しいエンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="31977-149">Add another new entity.</span></span> <span data-ttu-id="31977-150">型**学生**の**エンティティ名**選択**人**のドロップダウン リストから、**基本型**します。</span><span class="sxs-lookup"><span data-stu-id="31977-150">Type **Student** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>

<span data-ttu-id="31977-151">2 つの新しいエンティティ型は、デザイン画面に追加されました。</span><span class="sxs-lookup"><span data-stu-id="31977-151">Two new entity types were added to the design surface.</span></span> <span data-ttu-id="31977-152">新しいエンティティ型を指す矢印、 **Person**エンティティ型。 これが示す**人**は新しいエンティティ型の基本型です。</span><span class="sxs-lookup"><span data-stu-id="31977-152">An arrow points from the new entity types to the **Person** entity type; this indicates that **Person** is the base type for the new entity types.</span></span>

-   <span data-ttu-id="31977-153">右クリックし、 **HireDate**のプロパティ、 **Person**エンティティ。</span><span class="sxs-lookup"><span data-stu-id="31977-153">Right-click the **HireDate** property of the **Person** entity.</span></span> <span data-ttu-id="31977-154">選択**切り取り**(または、CTRL + X キーを使用)。</span><span class="sxs-lookup"><span data-stu-id="31977-154">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="31977-155">右クリックし、**インストラクター**エンティティと選択**貼り付け**(または ctrl + V キーを使用)。</span><span class="sxs-lookup"><span data-stu-id="31977-155">Right-click the **Instructor** entity and select **Paste** (or use the Ctrl-V key).</span></span>
-   <span data-ttu-id="31977-156">右クリックし、 **HireDate**プロパティと選択**プロパティ**します。</span><span class="sxs-lookup"><span data-stu-id="31977-156">Right-click the **HireDate** property and select **Properties**.</span></span>
-   <span data-ttu-id="31977-157">**プロパティ**ウィンドウで、設定、 **Nullable**プロパティを**false**します。</span><span class="sxs-lookup"><span data-stu-id="31977-157">In the **Properties** window, set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="31977-158">右クリックし、 **EnrollmentDate**のプロパティ、 **Person**エンティティ。</span><span class="sxs-lookup"><span data-stu-id="31977-158">Right-click the **EnrollmentDate** property of the **Person** entity.</span></span> <span data-ttu-id="31977-159">選択**切り取り**(または、CTRL + X キーを使用)。</span><span class="sxs-lookup"><span data-stu-id="31977-159">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="31977-160">右クリックし、**学生**エンティティと選択**貼り付け (または ctrl + V を使用してキー)。**</span><span class="sxs-lookup"><span data-stu-id="31977-160">Right-click the **Student** entity and select **Paste(or use the Ctrl-V key).**</span></span>
-   <span data-ttu-id="31977-161">選択、 **EnrollmentDate**プロパティと設定、 **Nullable**プロパティを**false**します。</span><span class="sxs-lookup"><span data-stu-id="31977-161">Select the **EnrollmentDate** property and set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="31977-162">選択、 **Person**エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="31977-162">Select the **Person** entity type.</span></span> <span data-ttu-id="31977-163">**プロパティ**ウィンドウで、その**抽象**プロパティを**true**します。</span><span class="sxs-lookup"><span data-stu-id="31977-163">In the **Properties** window, set its **Abstract** property to **true**.</span></span>
-   <span data-ttu-id="31977-164">削除、**識別子**プロパティから**Person**します。</span><span class="sxs-lookup"><span data-stu-id="31977-164">Delete the **Discriminator** property from **Person**.</span></span> <span data-ttu-id="31977-165">これを削除するかの理由は、次のセクションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="31977-165">The reason it should be deleted is explained in the following section.</span></span>

### <a name="map-the-entities"></a><span data-ttu-id="31977-166">エンティティにマップします</span><span class="sxs-lookup"><span data-stu-id="31977-166">Map the entities</span></span>

-   <span data-ttu-id="31977-167">右クリックし、**インストラクター**選択**テーブル マッピングです。**</span><span class="sxs-lookup"><span data-stu-id="31977-167">Right-click the **Instructor** and select **Table Mapping.**</span></span>
    <span data-ttu-id="31977-168">Instructor エンティティは、マッピングの詳細ウィンドウで選択されます。</span><span class="sxs-lookup"><span data-stu-id="31977-168">The Instructor entity is selected in the Mapping Details window.</span></span>
-   <span data-ttu-id="31977-169">クリックして**&lt;テーブルまたはビューの追加&gt;** で、**マッピングの詳細**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="31977-169">Click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
    <span data-ttu-id="31977-170">**&lt;テーブルまたはビューの追加&gt;** フィールドがテーブルのドロップダウン リストになります、またはビューを選択したエンティティをマップできます。</span><span class="sxs-lookup"><span data-stu-id="31977-170">The **&lt;Add a Table or View&gt;** field becomes a drop-down list of tables or views to which the selected entity can be mapped.</span></span>
-   <span data-ttu-id="31977-171">選択**Person**ドロップダウン リストから。</span><span class="sxs-lookup"><span data-stu-id="31977-171">Select **Person** from the drop-down list.</span></span>
-   <span data-ttu-id="31977-172">**マッピングの詳細**ウィンドウは、既定の列マッピングと条件を追加するためのオプションで更新されます。</span><span class="sxs-lookup"><span data-stu-id="31977-172">The **Mapping Details** window is updated with default column mappings and an option for adding a condition.</span></span>
-   <span data-ttu-id="31977-173">をクリックして**&lt;条件を追加&gt;** します。</span><span class="sxs-lookup"><span data-stu-id="31977-173">Click on **&lt;Add a Condition&gt;**.</span></span>
    <span data-ttu-id="31977-174">**&lt;条件を追加&gt;** フィールドの条件を設定する列のドロップダウン リストになります。</span><span class="sxs-lookup"><span data-stu-id="31977-174">The **&lt;Add a Condition&gt;** field becomes a drop-down list of columns for which conditions can be set.</span></span>
-   <span data-ttu-id="31977-175">選択**識別子**ドロップダウン リストから。</span><span class="sxs-lookup"><span data-stu-id="31977-175">Select **Discriminator** from the drop-down list.</span></span>
-   <span data-ttu-id="31977-176">**演算子**の列、**マッピングの詳細**ウィンドウで、ドロップダウン リストから =。</span><span class="sxs-lookup"><span data-stu-id="31977-176">In the **Operator** column of the **Mapping Details** window, select = from the drop-down list.</span></span>
-   <span data-ttu-id="31977-177">**値/プロパティ**列に「**インストラクター**します。</span><span class="sxs-lookup"><span data-stu-id="31977-177">In the **Value/Property** column, type **Instructor**.</span></span> <span data-ttu-id="31977-178">このよう、最終的な結果になります。</span><span class="sxs-lookup"><span data-stu-id="31977-178">The end result should look like this:</span></span>

    ![MappingDetails2](~/ef6/media/mappingdetails2.png)

-   <span data-ttu-id="31977-180">これらの手順を繰り返します、**学生**エンティティ型、それと等しい条件**学生**値。</span><span class="sxs-lookup"><span data-stu-id="31977-180">Repeat these steps for the **Student** entity type, but make the condition equal to **Student** value.</span></span>  
    <span data-ttu-id="31977-181">*削除する理由、**識別子**プロパティは、テーブルの列を複数回マップすることはできません。この列は、プロパティ マッピングにも使用できませんのでの条件付きマッピングでは、使用されます。唯一の方法が条件を使用している場合、両方に使用できます、 **Is Null**または**Is Not Null**比較します。*</span><span class="sxs-lookup"><span data-stu-id="31977-181">*The reason we wanted to remove the **Discriminator** property, is because you cannot map a table column more than once. This column will be used for conditional mapping, so it cannot be used for property mapping as well. The only way it can be used for both, if a condition uses an **Is Null** or **Is Not Null** comparison.*</span></span>

<span data-ttu-id="31977-182">これで、Table-Per-Hierarchy 継承が実装されました。</span><span class="sxs-lookup"><span data-stu-id="31977-182">Table-per-hierarchy inheritance is now implemented.</span></span>

![FinalTPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a><span data-ttu-id="31977-184">モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="31977-184">Use the Model</span></span>

<span data-ttu-id="31977-185">開く、 **Program.cs**ファイルの場所、 **Main**メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="31977-185">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="31977-186">次のコードを貼り付け、 **Main**関数。</span><span class="sxs-lookup"><span data-stu-id="31977-186">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="31977-187">コードでは、次の 3 つのクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="31977-187">The code executes three queries.</span></span> <span data-ttu-id="31977-188">最初のクエリを取り戻すすべて**Person**オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="31977-188">The first query brings back all **Person** objects.</span></span> <span data-ttu-id="31977-189">2 番目のクエリを使用して、 **OfType**メソッドを返す**インストラクター**オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="31977-189">The second query uses the **OfType** method to return **Instructor** objects.</span></span> <span data-ttu-id="31977-190">3 番目のクエリを使用して、 **OfType**メソッドを返す**学生**オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="31977-190">The third query uses the **OfType** method to return **Student** objects.</span></span>

``` csharp
    using (var context = new SchoolEntities())
    {
        Console.WriteLine("All people:");
        foreach (var person in context.People)
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Instructors only: ");
        foreach (var person in context.People.OfType<Instructor>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Students only: ");
        foreach (var person in context.People.OfType<Student>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }
    }
```
