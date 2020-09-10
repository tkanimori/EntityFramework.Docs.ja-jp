---
title: クエリの定義-EF デザイナー-EF6
description: Entity Framework 6 でのクエリ EF デザイナーの定義
author: divega
ms.date: 10/23/2016
ms.assetid: e52a297e-85aa-42f6-a922-ba960f8a4b22
uid: ef6/modeling/designer/advanced/defining-query
ms.openlocfilehash: e1962a80c3a94fb17bacf28969629ce949b117a0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616839"
---
# <a name="defining-query---ef-designer"></a><span data-ttu-id="1dc45-103">クエリの定義-EF デザイナー</span><span class="sxs-lookup"><span data-stu-id="1dc45-103">Defining Query - EF Designer</span></span>
<span data-ttu-id="1dc45-104">このチュートリアルでは、EF デザイナーを使用して、定義クエリとそれに対応するエンティティ型をモデルに追加する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-104">This walkthrough demonstrates how to add a defining query and a corresponding entity type to a model using the EF Designer.</span></span> <span data-ttu-id="1dc45-105">定義クエリは、データベースビューと同様の機能を提供するために一般的に使用されますが、ビューはデータベースではなくモデルで定義されます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-105">A defining query is commonly used to provide functionality similar to that provided by a database view, but the view is defined in the model, not the database.</span></span> <span data-ttu-id="1dc45-106">定義クエリを使用すると、.edmx ファイルの **DefiningQuery**要素で指定された SQL ステートメントを実行できます   。</span><span class="sxs-lookup"><span data-stu-id="1dc45-106">A defining query allows you to execute a SQL statement that is specified in the **DefiningQuery** element of an .edmx file.</span></span> <span data-ttu-id="1dc45-107">詳細については、「 **DefiningQuery** In The [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1dc45-107">For more information, see **DefiningQuery** in the [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="1dc45-108">クエリの定義を使用する場合は、モデルでエンティティ型を定義する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="1dc45-108">When using defining queries, you also have to define an entity type in your model.</span></span> <span data-ttu-id="1dc45-109">エンティティ型は、定義クエリによって公開されるデータを表示するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-109">The entity type is used to surface data exposed by the defining query.</span></span> <span data-ttu-id="1dc45-110">このエンティティ型を通じて表示されるデータは読み取り専用であることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="1dc45-110">Note that data surfaced through this entity type is read-only.</span></span>

<span data-ttu-id="1dc45-111">パラメーター化されたクエリを定義クエリとして実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="1dc45-111">Parameterized queries cannot be executed as defining queries.</span></span> <span data-ttu-id="1dc45-112">ただし、データを表示するエンティティ型の挿入、更新、および削除関数をストアド プロシージャにマッピングすると、データを更新できます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-112">However, the data can be updated by mapping the insert, update, and delete functions of the entity type that surfaces the data to stored procedures.</span></span> <span data-ttu-id="1dc45-113">詳細については、「 [ストアドプロシージャを使用した挿入、更新、および削除](xref:ef6/modeling/designer/stored-procedures/cud)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1dc45-113">For more information, see [Insert, Update, and Delete with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/cud).</span></span>

<span data-ttu-id="1dc45-114">このトピックでは、次のタスクを実行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-114">This topic shows how to perform the following tasks.</span></span>

-   <span data-ttu-id="1dc45-115">定義クエリの追加</span><span class="sxs-lookup"><span data-stu-id="1dc45-115">Add a Defining Query</span></span>
-   <span data-ttu-id="1dc45-116">モデルへのエンティティ型の追加</span><span class="sxs-lookup"><span data-stu-id="1dc45-116">Add an Entity Type to the Model</span></span>
-   <span data-ttu-id="1dc45-117">定義クエリをエンティティ型にマップする</span><span class="sxs-lookup"><span data-stu-id="1dc45-117">Map the Defining Query to the Entity Type</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1dc45-118">前提条件</span><span class="sxs-lookup"><span data-stu-id="1dc45-118">Prerequisites</span></span>

<span data-ttu-id="1dc45-119">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-119">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="1dc45-120">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="1dc45-120">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="1dc45-121">[School サンプルデータベース](xref:ef6/resources/school-database)。</span><span class="sxs-lookup"><span data-stu-id="1dc45-121">The [School sample database](xref:ef6/resources/school-database).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="1dc45-122">プロジェクトを設定する</span><span class="sxs-lookup"><span data-stu-id="1dc45-122">Set up the Project</span></span>

<span data-ttu-id="1dc45-123">このチュートリアルでは、Visual Studio 2012 以降を使用しています。</span><span class="sxs-lookup"><span data-stu-id="1dc45-123">This walkthrough is using Visual Studio 2012 or newer.</span></span>

-   <span data-ttu-id="1dc45-124">Visual Studio を開きます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-124">Open Visual Studio.</span></span>
-   <span data-ttu-id="1dc45-125">**[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-125">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="1dc45-126">左側のウィンドウで、[ **Visual C \# **] をクリックし、[**コンソールアプリケーション**] テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-126">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="1dc45-127">プロジェクトの名前として「名前」 **と入力し** 、[ **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-127">Enter **DefiningQuerySample** as the name of the project and click **OK**.</span></span>

 

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="1dc45-128">School データベースに基づくモデルを作成する</span><span class="sxs-lookup"><span data-stu-id="1dc45-128">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="1dc45-129">ソリューションエクスプローラーでプロジェクト名を右クリックして [ **追加**] をポイントし、[ **新しい項目**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-129">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="1dc45-130">左側のメニューから [ **データ** ] を選択し、[テンプレート] ペインで [ **ADO.NET Entity Data Model** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="1dc45-131">ファイル名として「 **名前」と入力し** 、[ **追加**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-131">Enter **DefiningQueryModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="1dc45-132">[モデルのコンテンツの選択] ダイアログボックスで、[ **データベースから生成**] を選択し、[ **次へ**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-132">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="1dc45-133">[新しい接続] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-133">Click New Connection.</span></span> <span data-ttu-id="1dc45-134">[接続プロパティ] ダイアログボックスで、サーバー名 (たとえば、 **(localdb) \\ mssqllocaldb**) を入力し、認証方法を選択します。データベース名として「 **School**」と入力し、[    **OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-134">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="1dc45-135">[データ接続の選択] ダイアログボックスが、データベース接続の設定によって更新されます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-135">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="1dc45-136">[データベースオブジェクトの選択] ダイアログボックスで、[**テーブル**] ノードを確認し   ます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-136">In the Choose Your Database Objects dialog box, check the **Tables** node.</span></span> <span data-ttu-id="1dc45-137">これにより、すべてのテーブルが **School** モデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-137">This will add all the tables to the **School** model.</span></span>
-   <span data-ttu-id="1dc45-138">[ **完了**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-138">Click **Finish**.</span></span>
-   <span data-ttu-id="1dc45-139">ソリューションエクスプローラーで、[型の **クエリ** ] を右クリックし、[ファイル **を開くアプリケーション**の選択] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-139">In Solution Explorer, right-click the **DefiningQueryModel.edmx** file and select **Open With…**.</span></span>
-   <span data-ttu-id="1dc45-140">**XML (テキスト) エディター**を選択します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-140">Select **XML (Text) Editor**.</span></span>

    ![XML エディター](~/ef6/media/xmleditor.png)

-   <span data-ttu-id="1dc45-142">次のメッセージが表示されたら、[ **はい]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-142">Click **Yes** if prompted with the following message:</span></span>

    ![警告2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a><span data-ttu-id="1dc45-144">定義クエリの追加</span><span class="sxs-lookup"><span data-stu-id="1dc45-144">Add a Defining Query</span></span>

<span data-ttu-id="1dc45-145">この手順では、XML エディターを使用して、定義クエリとエンティティ型を .edmx ファイルの SSDL セクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-145">In this step we will use the XML Editor to add a defining query and an entity type to the SSDL section of the .edmx file.</span></span> 

-   <span data-ttu-id="1dc45-146"> **EntitySet**   .EDMX ファイルの SSDL セクションに EntitySet 要素を追加します (行 5 ~ 13)。</span><span class="sxs-lookup"><span data-stu-id="1dc45-146">Add an **EntitySet** element to the SSDL section of the .edmx file (line 5 thru 13).</span></span> <span data-ttu-id="1dc45-147">次の指定を行います。</span><span class="sxs-lookup"><span data-stu-id="1dc45-147">Specify the following:</span></span>
    -   <span data-ttu-id="1dc45-148"> **Name**    **EntityType**    **EntitySet**要素の Name 属性と EntityType 属性のみが指定されてい   ます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-148">Only the **Name** and **EntityType** attributes of the **EntitySet** element are specified.</span></span>
    -   <span data-ttu-id="1dc45-149">エンティティ型の完全修飾名は、 **EntityType**属性で使用され   ます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-149">The fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="1dc45-150">実行される SQL ステートメントは、 **DefiningQuery**要素で指定され   ます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-150">The SQL statement to be executed is specified in the **DefiningQuery** element.</span></span>

``` xml
    <!-- SSDL content -->
    <edmx:StorageModels>
      <Schema Namespace="SchoolModel.Store" Alias="Self" Provider="System.Data.SqlClient" ProviderManifestToken="2008" xmlns:store="http://schemas.microsoft.com/ado/2007/12/edm/EntityStoreSchemaGenerator" xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
        <EntityContainer Name="SchoolModelStoreContainer">
           <EntitySet Name="GradeReport" EntityType="SchoolModel.Store.GradeReport">
              <DefiningQuery>
                SELECT CourseID, Grade, FirstName, LastName
                FROM StudentGrade
                JOIN
                (SELECT * FROM Person WHERE EnrollmentDate IS NOT NULL) AS p
                ON StudentID = p.PersonID
              </DefiningQuery>
          </EntitySet>
          <EntitySet Name="Course" EntityType="SchoolModel.Store.Course" store:Type="Tables" Schema="dbo" />
```

-   <span data-ttu-id="1dc45-151">**EntityType**要素を .EDMX の SSDL セクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-151">Add the **EntityType** element to the SSDL section of the .edmx.</span></span> <span data-ttu-id="1dc45-152">次のようにファイルを表示します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-152">file as shown below.</span></span> <span data-ttu-id="1dc45-153">次のことを考慮してください。</span><span class="sxs-lookup"><span data-stu-id="1dc45-153">Note the following:</span></span>
    -   <span data-ttu-id="1dc45-154">**Name**属性の値は、上の**EntitySet**要素の**entitytype**属性の値に対応します。ただし、 **entitytype**属性ではエンティティ型の完全修飾名が使用されます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-154">The value of the **Name** attribute corresponds to the value of the **EntityType** attribute in the **EntitySet** element above, although the fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="1dc45-155">プロパティ名は、 **DefiningQuery** 要素 (上記) の SQL ステートメントによって返される列名に対応しています。</span><span class="sxs-lookup"><span data-stu-id="1dc45-155">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element (above).</span></span>
    -   <span data-ttu-id="1dc45-156">この例では、キー値が一意になるように、エンティティ キーは 3 つのプロパティで構成されています。</span><span class="sxs-lookup"><span data-stu-id="1dc45-156">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

``` xml
    <EntityType Name="GradeReport">
      <Key>
        <PropertyRef Name="CourseID" />
        <PropertyRef Name="FirstName" />
        <PropertyRef Name="LastName" />
      </Key>
      <Property Name="CourseID"
                Type="int"
                Nullable="false" />
      <Property Name="Grade"
                Type="decimal"
                Precision="3"
                Scale="2" />
      <Property Name="FirstName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
      <Property Name="LastName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
    </EntityType>
```

>[!NOTE]
> <span data-ttu-id="1dc45-157">後で [モデルの **更新ウィザード** ] ダイアログを実行すると、クエリの定義など、ストレージモデルに加えられたすべての変更が上書きされます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-157">If later you run the **Update Model Wizard** dialog, any changes made to the storage model, including defining queries, will be overwritten.</span></span>

 

## <a name="add-an-entity-type-to-the-model"></a><span data-ttu-id="1dc45-158">モデルへのエンティティ型の追加</span><span class="sxs-lookup"><span data-stu-id="1dc45-158">Add an Entity Type to the Model</span></span>

<span data-ttu-id="1dc45-159">この手順では、EF デザイナーを使用して、エンティティ型を概念モデルに追加します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-159">In this step we will add the entity type to the conceptual model using the EF Designer.</span></span> <span data-ttu-id="1dc45-160">次のことを考慮してください。</span><span class="sxs-lookup"><span data-stu-id="1dc45-160"> Note the following:</span></span>

-   <span data-ttu-id="1dc45-161">エンティティの**名前**は、上記の**EntitySet**要素の**EntityType**属性の値に対応します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-161">The **Name** of the entity corresponds to the value of the **EntityType** attribute in the **EntitySet** element above.</span></span>
-   <span data-ttu-id="1dc45-162">プロパティ名は、上の **DefiningQuery** 要素の SQL ステートメントによって返される列名に対応しています。</span><span class="sxs-lookup"><span data-stu-id="1dc45-162">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element above.</span></span>
-   <span data-ttu-id="1dc45-163">この例では、キー値が一意になるように、エンティティ キーは 3 つのプロパティで構成されています。</span><span class="sxs-lookup"><span data-stu-id="1dc45-163">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

<span data-ttu-id="1dc45-164">EF デザイナーでモデルを開きます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-164">Open the model in the EF Designer.</span></span>

-   <span data-ttu-id="1dc45-165">[型のクエリ] をダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-165">Double-click the DefiningQueryModel.edmx.</span></span>
-   <span data-ttu-id="1dc45-166">次のメッセージが表示され **たら、[はい]** を言います。</span><span class="sxs-lookup"><span data-stu-id="1dc45-166">Say **Yes** to the following message:</span></span>

    ![警告2](~/ef6/media/warning2.png)

 

<span data-ttu-id="1dc45-168">モデルを編集するためのデザイン画面を提供する Entity Designer が表示されます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-168">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

-   <span data-ttu-id="1dc45-169">デザイナー画面を右クリックし、[**新しい** - &gt; **エンティティ**の追加] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-169">Right-click the designer surface and select **Add New**-&gt;**Entity…**.</span></span>
-   <span data-ttu-id="1dc45-170">エンティティ名には**GradeReport**を、**キープロパティ**には**CourseID**を指定します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-170">Specify **GradeReport** for the entity name and **CourseID** for the **Key Property**.</span></span>
-   <span data-ttu-id="1dc45-171">**GradeReport**エンティティを右クリックし、[**新しい** - &gt; **スカラープロパティ**の追加] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-171">Right-click the **GradeReport** entity and select **Add New**-&gt; **Scalar Property**.</span></span>
-   <span data-ttu-id="1dc45-172">プロパティの既定の名前を **FirstName**に変更します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-172">Change the default name of the property to **FirstName**.</span></span>
-   <span data-ttu-id="1dc45-173">別のスカラープロパティを追加し、名前に **LastName** を指定します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-173">Add another scalar property and specify **LastName** for the name.</span></span>
-   <span data-ttu-id="1dc45-174">別のスカラープロパティを追加し、名前に **グレード** を指定します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-174">Add another scalar property and specify **Grade** for the name.</span></span>
-   <span data-ttu-id="1dc45-175">[ **プロパティ** ] ウィンドウで、 **グレード**の **Type** プロパティを **Decimal**に変更します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-175">In the **Properties** window, change the **Grade**’s **Type** property to **Decimal**.</span></span>
-   <span data-ttu-id="1dc45-176">**FirstName**プロパティと**LastName**プロパティを選択します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-176">Select the **FirstName** and **LastName** properties.</span></span>
-   <span data-ttu-id="1dc45-177">[ **プロパティ** ] ウィンドウで、 **EntityKey** プロパティ値を **True**に変更します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-177">In the **Properties** window, change the **EntityKey** property value to **True**.</span></span>

<span data-ttu-id="1dc45-178">その結果、.edmx ファイルの **CSDL** セクションに次の要素が追加されました。</span><span class="sxs-lookup"><span data-stu-id="1dc45-178">As a result, the following elements were added to the **CSDL** section of the .edmx file.</span></span>

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a><span data-ttu-id="1dc45-179">定義クエリをエンティティ型にマップする</span><span class="sxs-lookup"><span data-stu-id="1dc45-179">Map the Defining Query to the Entity Type</span></span>

<span data-ttu-id="1dc45-180">この手順では、[マッピングの詳細] ウィンドウを使用して、概念エンティティ型とストレージエンティティ型をマップします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-180">In this step, we will use the Mapping Details window to map the conceptual and storage entity types.</span></span>

-   <span data-ttu-id="1dc45-181">デザイン画面で **GradeReport** エンティティを右クリックし、[ **テーブルマッピング**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="1dc45-181">Right-click the **GradeReport** entity on the design surface and select **Table Mapping**.</span></span>  
    <span data-ttu-id="1dc45-182">[ **マッピングの詳細** ] ウィンドウが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-182">The **Mapping Details** window is displayed.</span></span>
-   <span data-ttu-id="1dc45-183">[テーブル\*\* &lt; またはビュー &gt; の追加**] ドロップダウンリストから [ **GradeReport** ] を選択します ([**テーブル\*\*s] の下にあります)。</span><span class="sxs-lookup"><span data-stu-id="1dc45-183">Select **GradeReport** from the **&lt;Add a Table or View&gt;** dropdown list (located under **Table**s).</span></span>  
    <span data-ttu-id="1dc45-184">概念とストレージの **GradeReport** エンティティ型の間の既定のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-184">Default mappings between the conceptual and storage **GradeReport** entity type appear.</span></span>  
    <span data-ttu-id="1dc45-185">![Details3 のマッピング](~/ef6/media/mappingdetails.png)</span><span class="sxs-lookup"><span data-stu-id="1dc45-185">![Mapping Details3](~/ef6/media/mappingdetails.png)</span></span>

<span data-ttu-id="1dc45-186">その結果、 **EntitySetMapping**   要素が .edmx ファイルのマッピングセクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="1dc45-186">As a result, the **EntitySetMapping** element is added to the mapping section of the .edmx file.</span></span> 

``` xml
    <EntitySetMapping Name="GradeReports">
      <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.GradeReport)">
        <MappingFragment StoreEntitySet="GradeReport">
          <ScalarProperty Name="LastName" ColumnName="LastName" />
          <ScalarProperty Name="FirstName" ColumnName="FirstName" />
          <ScalarProperty Name="Grade" ColumnName="Grade" />
          <ScalarProperty Name="CourseID" ColumnName="CourseID" />
        </MappingFragment>
      </EntityTypeMapping>
    </EntitySetMapping>
```

-   <span data-ttu-id="1dc45-187">アプリケーションをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="1dc45-187">Compile the application.</span></span>

 

## <a name="call-the-defining-query-in-your-code"></a><span data-ttu-id="1dc45-188">コード内で定義クエリを呼び出す</span><span class="sxs-lookup"><span data-stu-id="1dc45-188">Call the Defining Query in your Code</span></span>

<span data-ttu-id="1dc45-189">**GradeReport**エンティティ型を使用して、定義クエリを実行できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="1dc45-189">You can now execute the defining query by using the **GradeReport** entity type.</span></span> 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
