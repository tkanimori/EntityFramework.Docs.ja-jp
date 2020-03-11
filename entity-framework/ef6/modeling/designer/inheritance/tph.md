---
title: デザイナー TPH 継承-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 72d26a8e-20ab-4500-bd13-394a08e73394
ms.openlocfilehash: 43ba34a98c3960a7a3052a00e2ed2751c2f2b121
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415233"
---
# <a name="designer-tph-inheritance"></a><span data-ttu-id="1f3a2-102">デザイナーの TPH 継承</span><span class="sxs-lookup"><span data-stu-id="1f3a2-102">Designer TPH Inheritance</span></span>
<span data-ttu-id="1f3a2-103">このステップバイステップチュートリアルでは、Entity Framework Designer (EF デザイナー) を使用して概念モデルに階層構造 (TPH) の継承を実装する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-103">This step-by-step walkthrough shows how to implement table-per-hierarchy (TPH) inheritance in your conceptual model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="1f3a2-104">TPH 継承では、1つのデータベーステーブルを使用して、継承階層内のすべてのエンティティ型のデータを保持します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-104">TPH inheritance uses one database table to maintain data for all of the entity types in an inheritance hierarchy.</span></span>

<span data-ttu-id="1f3a2-105">このチュートリアルでは、person テーブルを、Person (基本型)、Student (Person から派生)、およびインストラクター (Person から派生) の3つのエンティティ型にマップします。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-105">In this walkthrough we will map the Person table to three entity types: Person (the base type), Student (derives from Person), and Instructor (derives from Person).</span></span> <span data-ttu-id="1f3a2-106">データベースから概念モデルを作成し (Database First)、EF デザイナーを使用して TPH 継承を実装するようにモデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-106">We'll create a conceptual model from the database (Database First) and then alter the model to implement the TPH inheritance using the EF Designer.</span></span>

<span data-ttu-id="1f3a2-107">Model First を使用して TPH 継承にマップすることはできますが、複雑な独自のデータベース生成ワークフローを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-107">It is possible to map to a TPH inheritance using Model First but you would have to write your own database generation workflow which is complex.</span></span> <span data-ttu-id="1f3a2-108">次に、EF デザイナーでこのワークフローを**データベース生成ワークフロー**プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-108">You would then assign this workflow to the **Database Generation Workflow** property in the EF Designer.</span></span> <span data-ttu-id="1f3a2-109">別の方法として、Code First を使用する方が簡単です。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-109">An easier alternative is to use Code First.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="1f3a2-110">その他の継承オプション</span><span class="sxs-lookup"><span data-stu-id="1f3a2-110">Other Inheritance Options</span></span>

<span data-ttu-id="1f3a2-111">型ごとのテーブル (TPT) はもう1つの種類の継承で、データベース内の個別のテーブルが継承に参加するエンティティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-111">Table-per-Type (TPT) is another type of inheritance in which separate tables in the database are mapped to entities that participate in the inheritance.</span></span> <span data-ttu-id="1f3a2-112"> EF デザイナーを使用してテーブルごとの継承をマップする方法の詳細については、「 [Ef DESIGNER TPT の継承](~/ef6/modeling/designer/inheritance/tpt.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-112"> For information about how to map Table-per-Type inheritance with the EF Designer, see [EF Designer TPT Inheritance](~/ef6/modeling/designer/inheritance/tpt.md).</span></span>

<span data-ttu-id="1f3a2-113">具象型継承 (TPC) と混合継承モデルは、Entity Framework ランタイムによってサポートされていますが、EF デザイナーではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-113">Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="1f3a2-114">TPC または混合継承を使用する場合は、Code First を使用する方法と、EDMX ファイルを手動で編集する方法の2つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-114">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="1f3a2-115">EDMX ファイルを使用することを選択した場合、[マッピングの詳細] ウィンドウは "セーフモード" になり、デザイナーを使用してマッピングを変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-115">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1f3a2-116">前提条件</span><span class="sxs-lookup"><span data-stu-id="1f3a2-116">Prerequisites</span></span>

<span data-ttu-id="1f3a2-117">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-117">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="1f3a2-118">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-118">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="1f3a2-119">[School サンプルデータベース](~/ef6/resources/school-database.md)。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-119">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="1f3a2-120">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="1f3a2-120">Set up the Project</span></span>

-   <span data-ttu-id="1f3a2-121">Visual Studio 2012 を開きます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-121">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="1f3a2-122">[**ファイル]、[新規&gt; プロジェクトの&gt;** ] の選択</span><span class="sxs-lookup"><span data-stu-id="1f3a2-122">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="1f3a2-123">左側のウィンドウで、[ **Visual C\#** ] をクリックし、**コンソール**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-123">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="1f3a2-124">名前として「 **Tphdbfirstsample** 」と入力します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-124">Enter **TPHDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="1f3a2-125"> **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-125">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="1f3a2-126">モデルの作成</span><span class="sxs-lookup"><span data-stu-id="1f3a2-126">Create a Model</span></span>

-   <span data-ttu-id="1f3a2-127">ソリューションエクスプローラーでプロジェクト名を右クリックし、[**追加]&gt; [新しい項目**] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-127">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="1f3a2-128">左側のメニューから **[データ]** を選択し、テンプレート ペインで  **[ADO.NET Entity Data Model]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-128">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="1f3a2-129">ファイル名として「 **Tphmodel. .edmx** 」と入力し、 **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-129">Enter **TPHModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="1f3a2-130">[モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[ **次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-130">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="1f3a2-131">[ **新しい接続**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-131">Click **New Connection**.</span></span>
    <span data-ttu-id="1f3a2-132">[接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb)\\mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School** 」と入力し、[ **OK**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-132">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="1f3a2-133">[データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-133">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="1f3a2-134">[データベースオブジェクトの選択] ダイアログボックスの [テーブル] ノードで、[ **Person** ] テーブルを選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-134">In the Choose Your Database Objects dialog box, under the Tables node, select the **Person** table.</span></span>
-   <span data-ttu-id="1f3a2-135">[ **完了**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-135">Click **Finish**.</span></span>

<span data-ttu-id="1f3a2-136">モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-136">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="1f3a2-137">[データベースオブジェクトの選択] ダイアログボックスで選択したすべてのオブジェクトがモデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-137">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

<span data-ttu-id="1f3a2-138">これが、 **Person**テーブルでのデータベースの検索方法です。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-138">That is how the **Person** table looks in the database.</span></span>

![Person テーブル](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a><span data-ttu-id="1f3a2-140">階層ごとのテーブル継承の実装</span><span class="sxs-lookup"><span data-stu-id="1f3a2-140">Implement Table-per-Hierarchy Inheritance</span></span>

<span data-ttu-id="1f3a2-141"> **Person**テーブルには**識別子**列があり、"Student" と "インストラクター" の2つの値のいずれかを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-141">The **Person** table has the **Discriminator** column, which can have one of two values: “Student” and “Instructor”.</span></span> <span data-ttu-id="1f3a2-142">値に応じて、 **Person**テーブルは**Student**エンティティまたは**インストラクター**エンティティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-142">Depending on the value the **Person** table will be mapped to the **Student** entity or the **Instructor** entity.</span></span> <span data-ttu-id="1f3a2-143"> **Person**テーブルには、 **HireDate** と **EnrollmentDate**という2つの列があります。これは、ユーザーが同時に学生とインストラクター**になること**はできません (少なくともこのチュートリアルには含まれません)。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-143">The **Person** table also has two columns, **HireDate** and **EnrollmentDate**, which must be **nullable** because a person cannot be a student and an instructor at the same time (at least not in this walkthrough).</span></span>

### <a name="add-new-entities"></a><span data-ttu-id="1f3a2-144">新しいエンティティの追加</span><span class="sxs-lookup"><span data-stu-id="1f3a2-144">Add new Entities</span></span>

-   <span data-ttu-id="1f3a2-145">新しいエンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-145">Add a new entity.</span></span>
    <span data-ttu-id="1f3a2-146">これを行うには、Entity Framework Designer のデザイン画面の空の領域を右クリックし、[ **&gt;エンティティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-146">To do this, right-click on an empty space of the design surface of the Entity Framework Designer, and select **Add-&gt;Entity**.</span></span>
-   <span data-ttu-id="1f3a2-147">[ **エンティティ名**] に「 **講師** 」と入力し、 **基本データ型**のドロップダウンリストから [ **Person** ] を選択し ます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-147">Type **Instructor** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>
-   <span data-ttu-id="1f3a2-148"> **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-148">Click **OK**.</span></span>
-   <span data-ttu-id="1f3a2-149">別の新しいエンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-149">Add another new entity.</span></span> <span data-ttu-id="1f3a2-150">[ **エンティティ名**] に「 **Student** 」と入力し 、 **基本データ型**のドロップダウンリストから [ **Person** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-150">Type **Student** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>

<span data-ttu-id="1f3a2-151">2つの新しいエンティティ型がデザインサーフェイスに追加されました。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-151">Two new entity types were added to the design surface.</span></span> <span data-ttu-id="1f3a2-152">新しいエンティティ型から **Person** エンティティ型への矢印が参照されます。これは、 **Person** が新しいエンティティ型の基本型であることを示します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-152">An arrow points from the new entity types to the **Person** entity type; this indicates that **Person** is the base type for the new entity types.</span></span>

-   <span data-ttu-id="1f3a2-153"> **Person** エンティティの **HireDate** プロパティを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-153">Right-click the **HireDate** property of the **Person** entity.</span></span> <span data-ttu-id="1f3a2-154">[ **切り取り**] を選択します (または Ctrl + X キーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-154">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="1f3a2-155"> **インストラクター** エンティティを右クリックし、[ **貼り付け**] を選択します (または Ctrl + V キーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-155">Right-click the **Instructor** entity and select **Paste** (or use the Ctrl-V key).</span></span>
-   <span data-ttu-id="1f3a2-156"> **HireDate** プロパティを右クリックし、[ **プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-156">Right-click the **HireDate** property and select **Properties**.</span></span>
-   <span data-ttu-id="1f3a2-157"> **プロパティ** ウィンドウで、 **Nullable**  プロパティを  **false** に設定します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-157">In the **Properties** window, set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="1f3a2-158"> **Person** エンティティの **EnrollmentDate** プロパティを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-158">Right-click the **EnrollmentDate** property of the **Person** entity.</span></span> <span data-ttu-id="1f3a2-159">[ **切り取り**] を選択します (または Ctrl + X キーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-159">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="1f3a2-160"> **Student**エンティティを右クリックし、[貼り付け] を選択し **ます (または Ctrl + V キーを使用します)。**</span><span class="sxs-lookup"><span data-stu-id="1f3a2-160">Right-click the **Student** entity and select **Paste(or use the Ctrl-V key).**</span></span>
-   <span data-ttu-id="1f3a2-161"> **EnrollmentDate** プロパティを選択し、 **Nullable** プロパティを **false**に設定します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-161">Select the **EnrollmentDate** property and set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="1f3a2-162"> **Person** エンティティ型を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-162">Select the **Person** entity type.</span></span> <span data-ttu-id="1f3a2-163"> **プロパティ** ウィンドウで、 **Abstract**  プロパティを  **true** に設定します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-163">In the **Properties** window, set its **Abstract** property to **true**.</span></span>
-   <span data-ttu-id="1f3a2-164">**Person**から**識別子**のプロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-164">Delete the **Discriminator** property from **Person**.</span></span> <span data-ttu-id="1f3a2-165">削除する理由については、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-165">The reason it should be deleted is explained in the following section.</span></span>

### <a name="map-the-entities"></a><span data-ttu-id="1f3a2-166">エンティティのマップ</span><span class="sxs-lookup"><span data-stu-id="1f3a2-166">Map the entities</span></span>

-   <span data-ttu-id="1f3a2-167"> **インストラクター**を右クリックし、 **[テーブルマッピング]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-167">Right-click the **Instructor** and select **Table Mapping.**</span></span>
    <span data-ttu-id="1f3a2-168">[マッピングの詳細] ウィンドウで [インストラクター] エンティティが選択されます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-168">The Instructor entity is selected in the Mapping Details window.</span></span>
-   <span data-ttu-id="1f3a2-169"> **マッピングの詳細** ウィンドウに **テーブルまたはビュー&gt; を追加&lt;を**クリックします。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-169">Click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
    <span data-ttu-id="1f3a2-170"> フィールド **&gt;[テーブルまたはビューの追加]&lt;** 、選択したエンティティをマップできるテーブルまたはビューのドロップダウンリストになります。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-170">The **&lt;Add a Table or View&gt;** field becomes a drop-down list of tables or views to which the selected entity can be mapped.</span></span>
-   <span data-ttu-id="1f3a2-171">ドロップダウンリストから [ **Person** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-171">Select **Person** from the drop-down list.</span></span>
-   <span data-ttu-id="1f3a2-172">[ **マッピングの詳細** ] ウィンドウが、既定の列マッピングと、条件を追加するためのオプションで更新されます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-172">The **Mapping Details** window is updated with default column mappings and an option for adding a condition.</span></span>
-   <span data-ttu-id="1f3a2-173">[条件の追加] をクリックして **&gt;&lt;** ます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-173">Click on **&lt;Add a Condition&gt;**.</span></span>
    <span data-ttu-id="1f3a2-174"> フィールド **&gt;条件を追加&lt;** 、条件を設定できる列のドロップダウンリストになります。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-174">The **&lt;Add a Condition&gt;** field becomes a drop-down list of columns for which conditions can be set.</span></span>
-   <span data-ttu-id="1f3a2-175">ドロップダウンリストから [ **識別子** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-175">Select **Discriminator** from the drop-down list.</span></span>
-   <span data-ttu-id="1f3a2-176"> **マッピングの詳細** ウィンドウの  **演算子**  列で、ドロップダウンリストから = を選択します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-176">In the **Operator** column of the **Mapping Details** window, select = from the drop-down list.</span></span>
-   <span data-ttu-id="1f3a2-177">[ **値/プロパティ**] 列に「 **インストラクター**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-177">In the **Value/Property** column, type **Instructor**.</span></span> <span data-ttu-id="1f3a2-178">最終的な結果は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-178">The end result should look like this:</span></span>

    ![マッピングの詳細](~/ef6/media/mappingdetails2.png)

-   <span data-ttu-id="1f3a2-180"> **Student** エンティティ型についてもこの手順を繰り返しますが、条件は**student**の値と同じにしてください。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-180">Repeat these steps for the **Student** entity type, but make the condition equal to **Student** value.</span></span>  
    <span data-ttu-id="1f3a2-181">\***識別子**のプロパティを削除する理由は、テーブルの列を複数回マップできないためです。この列は条件付きマッピングに使用されるため、プロパティマッピングにも使用できません。条件で **Is null** が使用されている場合、または null では **ない**場合に使用できる唯一の方法は、 比較です。\*</span><span class="sxs-lookup"><span data-stu-id="1f3a2-181">*The reason we wanted to remove the **Discriminator** property, is because you cannot map a table column more than once. This column will be used for conditional mapping, so it cannot be used for property mapping as well. The only way it can be used for both, if a condition uses an **Is Null** or **Is Not Null** comparison.*</span></span>

<span data-ttu-id="1f3a2-182">これで、Table-Per-Hierarchy 継承が実装されました。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-182">Table-per-hierarchy inheritance is now implemented.</span></span>

![最終 TPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a><span data-ttu-id="1f3a2-184">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="1f3a2-184">Use the Model</span></span>

<span data-ttu-id="1f3a2-185">**Main**メソッドが定義されている**Program.cs**ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-185">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="1f3a2-186">**Main**関数に次のコードを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-186">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="1f3a2-187">このコードは、3つのクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-187">The code executes three queries.</span></span> <span data-ttu-id="1f3a2-188">最初のクエリでは、すべての**Person**オブジェクトが返されます。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-188">The first query brings back all **Person** objects.</span></span> <span data-ttu-id="1f3a2-189">2番目のクエリでは、 **OfType**メソッドを使用して、**インストラクター**オブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-189">The second query uses the **OfType** method to return **Instructor** objects.</span></span> <span data-ttu-id="1f3a2-190">3番目のクエリでは、 **OfType**メソッドを使用して**Student**オブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="1f3a2-190">The third query uses the **OfType** method to return **Student** objects.</span></span>

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
