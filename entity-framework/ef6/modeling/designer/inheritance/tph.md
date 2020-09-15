---
title: デザイナー TPH 継承-EF6
description: Entity Framework 6 でのデザイナーの継承
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tph
ms.openlocfilehash: 39675f9533dfef0ddad1867e3b70cd13b30708ea
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073397"
---
# <a name="designer-tph-inheritance"></a><span data-ttu-id="d7f1a-103">デザイナーの TPH 継承</span><span class="sxs-lookup"><span data-stu-id="d7f1a-103">Designer TPH Inheritance</span></span>
<span data-ttu-id="d7f1a-104">このステップバイステップチュートリアルでは、Entity Framework Designer (EF デザイナー) を使用して概念モデルに階層構造 (TPH) の継承を実装する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-104">This step-by-step walkthrough shows how to implement table-per-hierarchy (TPH) inheritance in your conceptual model with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="d7f1a-105">TPH 継承では、1つのデータベーステーブルを使用して、継承階層内のすべてのエンティティ型のデータを保持します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-105">TPH inheritance uses one database table to maintain data for all of the entity types in an inheritance hierarchy.</span></span>

<span data-ttu-id="d7f1a-106">このチュートリアルでは、person テーブルを、Person (基本型)、Student (Person から派生)、およびインストラクター (Person から派生) の3つのエンティティ型にマップします。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-106">In this walkthrough we will map the Person table to three entity types: Person (the base type), Student (derives from Person), and Instructor (derives from Person).</span></span> <span data-ttu-id="d7f1a-107">データベースから概念モデルを作成し (Database First)、EF デザイナーを使用して TPH 継承を実装するようにモデルを変更します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-107">We'll create a conceptual model from the database (Database First) and then alter the model to implement the TPH inheritance using the EF Designer.</span></span>

<span data-ttu-id="d7f1a-108">Model First を使用して TPH 継承にマップすることはできますが、複雑な独自のデータベース生成ワークフローを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-108">It is possible to map to a TPH inheritance using Model First but you would have to write your own database generation workflow which is complex.</span></span> <span data-ttu-id="d7f1a-109">次に、EF デザイナーでこのワークフローを **データベース生成ワークフロー** プロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-109">You would then assign this workflow to the **Database Generation Workflow** property in the EF Designer.</span></span> <span data-ttu-id="d7f1a-110">別の方法として、Code First を使用する方が簡単です。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-110">An easier alternative is to use Code First.</span></span>

## <a name="other-inheritance-options"></a><span data-ttu-id="d7f1a-111">その他の継承オプション</span><span class="sxs-lookup"><span data-stu-id="d7f1a-111">Other Inheritance Options</span></span>

<span data-ttu-id="d7f1a-112">型ごとのテーブル (TPT) はもう1つの種類の継承で、データベース内の個別のテーブルが継承に参加するエンティティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-112">Table-per-Type (TPT) is another type of inheritance in which separate tables in the database are mapped to entities that participate in the inheritance.</span></span> <span data-ttu-id="d7f1a-113">EF デザイナーを使用してテーブルごとの継承をマップする方法の詳細については、「 [Ef DESIGNER TPT の継承](xref:ef6/modeling/designer/inheritance/tpt)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-113"> For information about how to map Table-per-Type inheritance with the EF Designer, see [EF Designer TPT Inheritance](xref:ef6/modeling/designer/inheritance/tpt).</span></span>

<span data-ttu-id="d7f1a-114">具象型継承 (TPC) と混合継承モデルは、Entity Framework ランタイムによってサポートされていますが、EF デザイナーではサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-114">Table-per-Concrete Type Inheritance (TPC) and mixed inheritance models are supported by the Entity Framework runtime but are not supported by the EF Designer.</span></span> <span data-ttu-id="d7f1a-115">TPC または混合継承を使用する場合は、Code First を使用する方法と、EDMX ファイルを手動で編集する方法の2つのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-115">If you want to use TPC or mixed inheritance, you have two options: use Code First, or manually edit the EDMX file.</span></span> <span data-ttu-id="d7f1a-116">EDMX ファイルを使用することを選択した場合、[マッピングの詳細] ウィンドウは "セーフモード" になり、デザイナーを使用してマッピングを変更することはできません。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-116">If you choose to work with the EDMX file, the Mapping Details Window will be put into “safe mode” and you will not be able to use the designer to change the mappings.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d7f1a-117">前提条件</span><span class="sxs-lookup"><span data-stu-id="d7f1a-117">Prerequisites</span></span>

<span data-ttu-id="d7f1a-118">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-118">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="d7f1a-119">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-119">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="d7f1a-120">[School サンプルデータベース](xref:ef6/resources/school-database)。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-120">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="d7f1a-121">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="d7f1a-121">Set up the Project</span></span>

-   <span data-ttu-id="d7f1a-122">Visual Studio 2012 を開きます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-122">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="d7f1a-123">**ファイルの選択- &gt; 新規作成- &gt; プロジェクト**</span><span class="sxs-lookup"><span data-stu-id="d7f1a-123">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="d7f1a-124">左側のウィンドウで、[ **Visual C \# **] をクリックし、**コンソール**テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-124">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="d7f1a-125">名前として「 **Tphdbfirstsample**」と入力し   ます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-125">Enter **TPHDBFirstSample** as the name.</span></span>
-   <span data-ttu-id="d7f1a-126"> **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-126">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="d7f1a-127">モデルの作成</span><span class="sxs-lookup"><span data-stu-id="d7f1a-127">Create a Model</span></span>

-   <span data-ttu-id="d7f1a-128">ソリューションエクスプローラーでプロジェクト名を右クリックし、[追加]、[ \*\* &gt; 新しい項目\*\*] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-128">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="d7f1a-129">左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-129">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="d7f1a-130">ファイル名として「 **Tphmodel. .edmx** 」と入力し、[ **追加**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-130">Enter **TPHModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="d7f1a-131">[モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[ **次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-131">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="d7f1a-132">[ **新しい接続**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-132">Click **New Connection**.</span></span>
    <span data-ttu-id="d7f1a-133">[接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb) \\ mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School**」と入力し、[    **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-133">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="d7f1a-134">[データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-134">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="d7f1a-135">[データベースオブジェクトの選択] ダイアログボックスの [テーブル] ノードで、[ **Person** ] テーブルを選択します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-135">In the Choose Your Database Objects dialog box, under the Tables node, select the **Person** table.</span></span>
-   <span data-ttu-id="d7f1a-136">[ **完了**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-136">Click **Finish**.</span></span>

<span data-ttu-id="d7f1a-137">モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-137">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span> <span data-ttu-id="d7f1a-138">[データベースオブジェクトの選択] ダイアログボックスで選択したすべてのオブジェクトがモデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-138">All the objects that you selected in the Choose Your Database Objects dialog box are added to the model.</span></span>

<span data-ttu-id="d7f1a-139">これが、 **Person** テーブルでのデータベースの検索方法です。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-139">That is how the **Person** table looks in the database.</span></span>

![Person テーブル](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a><span data-ttu-id="d7f1a-141">階層ごとのテーブル継承の実装</span><span class="sxs-lookup"><span data-stu-id="d7f1a-141">Implement Table-per-Hierarchy Inheritance</span></span>

<span data-ttu-id="d7f1a-142"> **Person**テーブルには**識別子**列があり、"Student" と "インストラクター" の2つの値のいずれかを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-142">The **Person** table has the **Discriminator** column, which can have one of two values: “Student” and “Instructor”.</span></span> <span data-ttu-id="d7f1a-143">値に応じて、 **Person** テーブルは **Student** エンティティまたは **インストラクター** エンティティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-143">Depending on the value the **Person** table will be mapped to the **Student** entity or the **Instructor** entity.</span></span> <span data-ttu-id="d7f1a-144"> **Person**テーブルには、 **HireDate**と EnrollmentDate という2つの列があります。これは、ユーザーが同時   に学生とインストラクターになることはできません (少なくともこのチュートリアルには含まれません)。 **EnrollmentDate** **nullable**</span><span class="sxs-lookup"><span data-stu-id="d7f1a-144">The **Person** table also has two columns, **HireDate** and **EnrollmentDate**, which must be **nullable** because a person cannot be a student and an instructor at the same time (at least not in this walkthrough).</span></span>

### <a name="add-new-entities"></a><span data-ttu-id="d7f1a-145">新しいエンティティの追加</span><span class="sxs-lookup"><span data-stu-id="d7f1a-145">Add new Entities</span></span>

-   <span data-ttu-id="d7f1a-146">新しいエンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-146">Add a new entity.</span></span>
    <span data-ttu-id="d7f1a-147">これを行うには、Entity Framework Designer のデザイン画面の空の領域を右クリックし、[ \*\* &gt; エンティティの追加\*\*] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-147">To do this, right-click on an empty space of the design surface of the Entity Framework Designer, and select **Add-&gt;Entity**.</span></span>
-   <span data-ttu-id="d7f1a-148">エンティティ名として「 **インストラクター**」   と入力し、基本データ **Entity name**    **Person**    **型**のドロップダウンリストから [Person] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-148">Type **Instructor** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>
-   <span data-ttu-id="d7f1a-149"> **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-149">Click **OK**.</span></span>
-   <span data-ttu-id="d7f1a-150">別の新しいエンティティを追加します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-150">Add another new entity.</span></span> <span data-ttu-id="d7f1a-151">エンティティ名として「 **Student**    **Entity name**   」と入力し、基本データ **Person**    **型**のドロップダウンリストから [Person] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-151">Type **Student** for the **Entity name** and select **Person** from the drop-down list for the **Base type**.</span></span>

<span data-ttu-id="d7f1a-152">2つの新しいエンティティ型がデザインサーフェイスに追加されました。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-152">Two new entity types were added to the design surface.</span></span> <span data-ttu-id="d7f1a-153">矢印は、新しいエンティティ型から **person**   エンティティ型に向いています。これは、 **person**   が新しいエンティティ型の基本型であることを示します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-153">An arrow points from the new entity types to the **Person** entity type; this indicates that **Person** is the base type for the new entity types.</span></span>

-   <span data-ttu-id="d7f1a-154">Person エンティティの **HireDate**プロパティを右クリックし    **Person**   ます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-154">Right-click the **HireDate** property of the **Person** entity.</span></span> <span data-ttu-id="d7f1a-155">[ **切り取り** ] を選択します (または Ctrl + X キーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-155">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="d7f1a-156"> **インストラクター**エンティティを右クリック   し、[ **貼り付け**] を選択します (または Ctrl + V キーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-156">Right-click the **Instructor** entity and select **Paste** (or use the Ctrl-V key).</span></span>
-   <span data-ttu-id="d7f1a-157"> **HireDate**プロパティを右クリック   し、[ **プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-157">Right-click the **HireDate** property and select **Properties**.</span></span>
-   <span data-ttu-id="d7f1a-158">[ **プロパティ**   ] ウィンドウで、[ **null 許容**]   プロパティを [ **false**] に設定します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-158">In the **Properties** window, set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="d7f1a-159">Person エンティティの **EnrollmentDate**プロパティを右クリックし    **Person**   ます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-159">Right-click the **EnrollmentDate** property of the **Person** entity.</span></span> <span data-ttu-id="d7f1a-160">[ **切り取り** ] を選択します (または Ctrl + X キーを使用します)。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-160">Select **Cut** (or use the Ctrl-X key).</span></span>
-   <span data-ttu-id="d7f1a-161"> **Student**エンティティを右クリックし、[貼り付け] を選択し **ます (または Ctrl + V キーを使用します)。**</span><span class="sxs-lookup"><span data-stu-id="d7f1a-161">Right-click the **Student** entity and select **Paste(or use the Ctrl-V key).**</span></span>
-   <span data-ttu-id="d7f1a-162"> **EnrollmentDate**   プロパティを選択し、 **Nullable**   プロパティを **false**に設定します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-162">Select the **EnrollmentDate** property and set the **Nullable** property to **false**.</span></span>
-   <span data-ttu-id="d7f1a-163"> **Person**   エンティティ型を選択します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-163">Select the **Person** entity type.</span></span> <span data-ttu-id="d7f1a-164">[ **プロパティ**   ] ウィンドウで、[ **Abstract**]   プロパティを [ **true**] に設定します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-164">In the **Properties** window, set its **Abstract** property to **true**.</span></span>
-   <span data-ttu-id="d7f1a-165">**Person**から**識別子**のプロパティを削除します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-165">Delete the **Discriminator** property from **Person**.</span></span> <span data-ttu-id="d7f1a-166">削除する理由については、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-166">The reason it should be deleted is explained in the following section.</span></span>

### <a name="map-the-entities"></a><span data-ttu-id="d7f1a-167">エンティティのマップ</span><span class="sxs-lookup"><span data-stu-id="d7f1a-167">Map the entities</span></span>

-   <span data-ttu-id="d7f1a-168"> **インストラクター**を右クリックし、[**テーブルマッピング**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-168">Right-click the **Instructor** and select **Table Mapping.**</span></span>
    <span data-ttu-id="d7f1a-169">[マッピングの詳細] ウィンドウで [インストラクター] エンティティが選択されます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-169">The Instructor entity is selected in the Mapping Details window.</span></span>
-   <span data-ttu-id="d7f1a-170">[マッピングの詳細] ウィンドウで、[ \*\* &lt; テーブルまたはビュー &gt; の追加**] をクリックし    **Mapping Details\*\*   ます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-170">Click **&lt;Add a Table or View&gt;** in the **Mapping Details** window.</span></span>
    <span data-ttu-id="d7f1a-171">[ \*\* &lt; テーブルまたはビュー &gt; の追加\*\*] フィールドは、   選択したエンティティをマップできるテーブルまたはビューのドロップダウンリストになります。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-171">The **&lt;Add a Table or View&gt;** field becomes a drop-down list of tables or views to which the selected entity can be mapped.</span></span>
-   <span data-ttu-id="d7f1a-172">ドロップダウンリストから [ **Person**] を選択し   ます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-172">Select **Person** from the drop-down list.</span></span>
-   <span data-ttu-id="d7f1a-173">[ **マッピングの詳細**   ] ウィンドウが更新され、既定の列マッピングと、条件を追加するためのオプションが表示されます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-173">The **Mapping Details** window is updated with default column mappings and an option for adding a condition.</span></span>
-   <span data-ttu-id="d7f1a-174">[ \*\* &lt; 条件 &gt; の追加\*\*] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-174">Click on **&lt;Add a Condition&gt;**.</span></span>
    <span data-ttu-id="d7f1a-175">[ \*\* &lt; &gt; 条件の追加\*\*] フィールドは、条件を   設定できる列のドロップダウンリストになります。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-175">The **&lt;Add a Condition&gt;** field becomes a drop-down list of columns for which conditions can be set.</span></span>
-   <span data-ttu-id="d7f1a-176">ドロップダウンリストから [ **識別子**] を選択し   ます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-176">Select **Discriminator** from the drop-down list.</span></span>
-   <span data-ttu-id="d7f1a-177">[ **Operator**    **マッピングの詳細**] ウィンドウの [演算子] 列で   、ドロップダウンリストから [=] を選択します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-177">In the **Operator** column of the **Mapping Details** window, select = from the drop-down list.</span></span>
-   <span data-ttu-id="d7f1a-178">[ **値/プロパティ** ] 列に「 **インストラクター**」と入力します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-178">In the **Value/Property** column, type **Instructor**.</span></span> <span data-ttu-id="d7f1a-179">最終的な結果は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-179">The end result should look like this:</span></span>

    ![マッピングの詳細](~/ef6/media/mappingdetails2.png)

-   <span data-ttu-id="d7f1a-181"> **Student**エンティティ型についても同じ手順を繰り返し   ますが、条件は**student**の値と同じにしてください。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-181">Repeat these steps for the **Student** entity type, but make the condition equal to **Student** value.</span></span>  
    <span data-ttu-id="d7f1a-182">***識別子**のプロパティを削除する理由は、テーブルの列を複数回マップできないためです。この列は条件付きマッピングに使用されるため、プロパティマッピングにも使用できません。条件で **Is null**   また **は not null**比較が使用されている場合、どちらの場合でも使用できる唯一の方法です   。*</span><span class="sxs-lookup"><span data-stu-id="d7f1a-182">*The reason we wanted to remove the **Discriminator** property, is because you cannot map a table column more than once. This column will be used for conditional mapping, so it cannot be used for property mapping as well. The only way it can be used for both, if a condition uses an **Is Null** or **Is Not Null** comparison.*</span></span>

<span data-ttu-id="d7f1a-183">これで、Table-Per-Hierarchy 継承が実装されました。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-183">Table-per-hierarchy inheritance is now implemented.</span></span>

![最終 TPH](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a><span data-ttu-id="d7f1a-185">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="d7f1a-185">Use the Model</span></span>

<span data-ttu-id="d7f1a-186">**Main**メソッドが定義されている**Program.cs**ファイルを開きます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-186">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="d7f1a-187">**Main**関数に次のコードを貼り付けます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-187">Paste the following code into the **Main** function.</span></span> <span data-ttu-id="d7f1a-188">このコードは、3つのクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-188">The code executes three queries.</span></span> <span data-ttu-id="d7f1a-189">最初のクエリでは、すべての **Person** オブジェクトが返されます。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-189">The first query brings back all **Person** objects.</span></span> <span data-ttu-id="d7f1a-190">2番目のクエリでは、 **OfType** メソッドを使用して、 **インストラクター** オブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-190">The second query uses the **OfType** method to return **Instructor** objects.</span></span> <span data-ttu-id="d7f1a-191">3番目のクエリでは、 **OfType** メソッドを使用して **Student** オブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="d7f1a-191">The third query uses the **OfType** method to return **Student** objects.</span></span>

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
