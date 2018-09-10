---
title: クエリ - EF デザイナー - EF6 を定義します。
author: divega
ms.date: 2016-10-23
ms.assetid: e52a297e-85aa-42f6-a922-ba960f8a4b22
ms.openlocfilehash: 8415a265cdbe078422e0467ee97da955a81b873d
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250973"
---
# <a name="defining-query---ef-designer"></a><span data-ttu-id="548bd-102">クエリ - EF Designer を定義します。</span><span class="sxs-lookup"><span data-stu-id="548bd-102">Defining Query - EF Designer</span></span>
<span data-ttu-id="548bd-103">このチュートリアルは、定義を追加する方法を示しますクエリと対応するエンティティを EF Designer を使用してモデルに入力します。</span><span class="sxs-lookup"><span data-stu-id="548bd-103">This walkthrough demonstrates how to add a defining query and a corresponding entity type to a model using the EF Designer.</span></span> <span data-ttu-id="548bd-104">定義クエリは、データベース ビューによって提供されるのと同様の機能を提供するが、データベースではなく、モデルのビューが定義されます。</span><span class="sxs-lookup"><span data-stu-id="548bd-104">A defining query is commonly used to provide functionality similar to that provided by a database view, but the view is defined in the model, not the database.</span></span> <span data-ttu-id="548bd-105">指定されている SQL ステートメントを実行することができます、定義クエリ、 **DefiningQuery** .edmx ファイルの要素。</span><span class="sxs-lookup"><span data-stu-id="548bd-105">A defining query allows you to execute a SQL statement that is specified in the **DefiningQuery** element of an .edmx file.</span></span> <span data-ttu-id="548bd-106">詳細については、次を参照してください。 **DefiningQuery**で、 [SSDL 仕様](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)します。</span><span class="sxs-lookup"><span data-stu-id="548bd-106">For more information, see **DefiningQuery** in the [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md).</span></span>

<span data-ttu-id="548bd-107">定義クエリを使用する場合は、モデルのエンティティ型を定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="548bd-107">When using defining queries, you also have to define an entity type in your model.</span></span> <span data-ttu-id="548bd-108">定義クエリによって公開されているデータを表示するエンティティ型が使用されます。</span><span class="sxs-lookup"><span data-stu-id="548bd-108">The entity type is used to surface data exposed by the defining query.</span></span> <span data-ttu-id="548bd-109">このエンティティの種類を通じて表示されるデータは読み取り専用ことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="548bd-109">Note that data surfaced through this entity type is read-only.</span></span>

<span data-ttu-id="548bd-110">パラメーター化されたクエリを定義クエリとして実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="548bd-110">Parameterized queries cannot be executed as defining queries.</span></span> <span data-ttu-id="548bd-111">ただし、データを表示するエンティティ型の挿入、更新、および削除関数をストアド プロシージャにマッピングすると、データを更新できます。</span><span class="sxs-lookup"><span data-stu-id="548bd-111">However, the data can be updated by mapping the insert, update, and delete functions of the entity type that surfaces the data to stored procedures.</span></span> <span data-ttu-id="548bd-112">詳細については、次を参照してください。[挿入、更新、およびストアド プロシージャを使用した削除](~/ef6/modeling/designer/stored-procedures/cud.md)します。</span><span class="sxs-lookup"><span data-stu-id="548bd-112">For more information, see [Insert, Update, and Delete with Stored Procedures](~/ef6/modeling/designer/stored-procedures/cud.md).</span></span>

<span data-ttu-id="548bd-113">このトピックでは、次のタスクを実行する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="548bd-113">This topic shows how to perform the following tasks.</span></span>

-   <span data-ttu-id="548bd-114">定義クエリを追加します。</span><span class="sxs-lookup"><span data-stu-id="548bd-114">Add a Defining Query</span></span>
-   <span data-ttu-id="548bd-115">エンティティ型をモデルに追加します。</span><span class="sxs-lookup"><span data-stu-id="548bd-115">Add an Entity Type to the Model</span></span>
-   <span data-ttu-id="548bd-116">定義クエリをエンティティ型にマップします。</span><span class="sxs-lookup"><span data-stu-id="548bd-116">Map the Defining Query to the Entity Type</span></span>

## <a name="prerequisites"></a><span data-ttu-id="548bd-117">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="548bd-117">Prerequisites</span></span>

<span data-ttu-id="548bd-118">このチュートリアルを完了するための要件を次に示します。</span><span class="sxs-lookup"><span data-stu-id="548bd-118">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="548bd-119">Visual Studio の最新バージョン。</span><span class="sxs-lookup"><span data-stu-id="548bd-119">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="548bd-120">[School サンプル データベース](~/ef6/resources/school-database.md)します。</span><span class="sxs-lookup"><span data-stu-id="548bd-120">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="548bd-121">プロジェクトを設定します。</span><span class="sxs-lookup"><span data-stu-id="548bd-121">Set up the Project</span></span>

<span data-ttu-id="548bd-122">このチュートリアルでは、Visual Studio 2012 以降を使用しています。</span><span class="sxs-lookup"><span data-stu-id="548bd-122">This walkthrough is using Visual Studio 2012 or newer.</span></span>

-   <span data-ttu-id="548bd-123">Visual Studio を開きます。</span><span class="sxs-lookup"><span data-stu-id="548bd-123">Open Visual Studio.</span></span>
-   <span data-ttu-id="548bd-124">**[ファイル]** メニューの **[新規作成]** をポイントし、 **[プロジェクト]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="548bd-124">On the **File** menu, point to **New**, and then click **Project**.</span></span>
-   <span data-ttu-id="548bd-125">左側のウィンドウで次のようにクリックします。 **Visual C\#** を選び、**コンソール アプリケーション**テンプレート。</span><span class="sxs-lookup"><span data-stu-id="548bd-125">In the left pane, click **Visual C\#**, and then select the **Console Application** template.</span></span>
-   <span data-ttu-id="548bd-126">入力**DefiningQuerySample**をクリックして、プロジェクトの名前として**OK**します。</span><span class="sxs-lookup"><span data-stu-id="548bd-126">Enter **DefiningQuerySample** as the name of the project and click **OK**.</span></span>

 

## <a name="create-a-model-based-on-the-school-database"></a><span data-ttu-id="548bd-127">School データベースに基づくモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="548bd-127">Create a Model based on the School Database</span></span>

-   <span data-ttu-id="548bd-128">ソリューション エクスプ ローラーでプロジェクト名を右クリックし、[**追加**、] をクリックし、**新しい項目の**します。</span><span class="sxs-lookup"><span data-stu-id="548bd-128">Right-click the project name in Solution Explorer, point to **Add**, and then click **New Item**.</span></span>
-   <span data-ttu-id="548bd-129">選択**データ**選択し、左側のメニューから**ADO.NET Entity Data Model**テンプレート ペインでします。</span><span class="sxs-lookup"><span data-stu-id="548bd-129">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="548bd-130">入力**DefiningQueryModel.edmx**のファイル名、およびクリック**追加**します。</span><span class="sxs-lookup"><span data-stu-id="548bd-130">Enter **DefiningQueryModel.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="548bd-131">モデルのコンテンツの選択 ダイアログ ボックスで、次のように選択します。**データベースから生成**、 をクリックし、**次**。</span><span class="sxs-lookup"><span data-stu-id="548bd-131">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="548bd-132">[新しい接続] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="548bd-132">Click New Connection.</span></span> <span data-ttu-id="548bd-133">接続のプロパティ ダイアログ ボックスで、サーバー名を入力します (たとえば、 **(localdb)\\mssqllocaldb**) を選択します、認証方式として、型**学校**データベース名、し、。クリックして**OK**します。</span><span class="sxs-lookup"><span data-stu-id="548bd-133">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="548bd-134">データ接続の選択 ダイアログ ボックスは、データベース接続の設定で更新されます。</span><span class="sxs-lookup"><span data-stu-id="548bd-134">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="548bd-135">データベース オブジェクトの選択 ダイアログ ボックスで、確認、**テーブル**ノード。</span><span class="sxs-lookup"><span data-stu-id="548bd-135">In the Choose Your Database Objects dialog box, check the **Tables** node.</span></span> <span data-ttu-id="548bd-136">これをすべてのテーブルに追加されます、**学校**モデル。</span><span class="sxs-lookup"><span data-stu-id="548bd-136">This will add all the tables to the **School** model.</span></span>
-   <span data-ttu-id="548bd-137">**[完了]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="548bd-137">Click **Finish**.</span></span>
-   <span data-ttu-id="548bd-138">ソリューション エクスプ ローラーで右クリックし、 **DefiningQueryModel.edmx**ファイルおよび選択**プログラムから開く...**。</span><span class="sxs-lookup"><span data-stu-id="548bd-138">In Solution Explorer, right-click the **DefiningQueryModel.edmx** file and select **Open With…**.</span></span>
-   <span data-ttu-id="548bd-139">選択**XML (テキスト) エディター**します。</span><span class="sxs-lookup"><span data-stu-id="548bd-139">Select **XML (Text) Editor**.</span></span>

    ![XML エディター](~/ef6/media/xmleditor.png)

-   <span data-ttu-id="548bd-141">クリックして**はい**次のメッセージが表示された場合。</span><span class="sxs-lookup"><span data-stu-id="548bd-141">Click **Yes** if prompted with the following message:</span></span>

    ![2 の警告](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a><span data-ttu-id="548bd-143">定義クエリを追加します。</span><span class="sxs-lookup"><span data-stu-id="548bd-143">Add a Defining Query</span></span>

<span data-ttu-id="548bd-144">使用するこの手順では、定義を追加する XML エディターのクエリし、エンティティが .edmx ファイルの SSDL セクションに入力します。</span><span class="sxs-lookup"><span data-stu-id="548bd-144">In this step we will use the XML Editor to add a defining query and an entity type to the SSDL section of the .edmx file.</span></span> 

-   <span data-ttu-id="548bd-145">追加、 **EntitySet**要素を .edmx ファイル (13 から 5 行目) の SSDL セクション。</span><span class="sxs-lookup"><span data-stu-id="548bd-145">Add an **EntitySet** element to the SSDL section of the .edmx file (line 5 thru 13).</span></span> <span data-ttu-id="548bd-146">次を指定します。</span><span class="sxs-lookup"><span data-stu-id="548bd-146">Specify the following:</span></span>
    -   <span data-ttu-id="548bd-147">のみ、**名前**と**EntityType**の属性、 **EntitySet**要素が指定されています。</span><span class="sxs-lookup"><span data-stu-id="548bd-147">Only the **Name** and **EntityType** attributes of the **EntitySet** element are specified.</span></span>
    -   <span data-ttu-id="548bd-148">エンティティ型の完全修飾名が使用される、 **EntityType**属性。</span><span class="sxs-lookup"><span data-stu-id="548bd-148">The fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="548bd-149">実行される SQL ステートメントが指定された、 **DefiningQuery**要素。</span><span class="sxs-lookup"><span data-stu-id="548bd-149">The SQL statement to be executed is specified in the **DefiningQuery** element.</span></span>

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

-   <span data-ttu-id="548bd-150">追加、 **EntityType** .edmx ファイルの SSDL セクションに要素。</span><span class="sxs-lookup"><span data-stu-id="548bd-150">Add the **EntityType** element to the SSDL section of the .edmx.</span></span> <span data-ttu-id="548bd-151">ファイルに示すように以下です。</span><span class="sxs-lookup"><span data-stu-id="548bd-151">file as shown below.</span></span> <span data-ttu-id="548bd-152">次の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="548bd-152">Note the following:</span></span>
    -   <span data-ttu-id="548bd-153">値、**名前**属性の値に対応して、 **EntityType**属性、 **EntitySet** 、上記の要素がの完全修飾名、エンティティ型がで使用される、 **EntityType**属性。</span><span class="sxs-lookup"><span data-stu-id="548bd-153">The value of the **Name** attribute corresponds to the value of the **EntityType** attribute in the **EntitySet** element above, although the fully-qualified name of the entity type is used in the **EntityType** attribute.</span></span>
    -   <span data-ttu-id="548bd-154">プロパティ名で SQL ステートメントによって返される列名に対応、 **DefiningQuery**要素 (上記)。</span><span class="sxs-lookup"><span data-stu-id="548bd-154">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element (above).</span></span>
    -   <span data-ttu-id="548bd-155">この例では、キー値が一意になるように、エンティティ キーは 3 つのプロパティで構成されています。</span><span class="sxs-lookup"><span data-stu-id="548bd-155">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

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
> <span data-ttu-id="548bd-156">以降を実行する場合、**モデルの更新ウィザード**ダイアログ ボックスで、定義クエリなど、ストレージ モデルに加えられた変更は上書きされます。</span><span class="sxs-lookup"><span data-stu-id="548bd-156">If later you run the **Update Model Wizard** dialog, any changes made to the storage model, including defining queries, will be overwritten.</span></span>

 

## <a name="add-an-entity-type-to-the-model"></a><span data-ttu-id="548bd-157">エンティティ型をモデルに追加します。</span><span class="sxs-lookup"><span data-stu-id="548bd-157">Add an Entity Type to the Model</span></span>

<span data-ttu-id="548bd-158">この手順では、EF デザイナーを使用して概念モデルに、エンティティ型を追加します。</span><span class="sxs-lookup"><span data-stu-id="548bd-158">In this step we will add the entity type to the conceptual model using the EF Designer.</span></span>  <span data-ttu-id="548bd-159">次の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="548bd-159">Note the following:</span></span>

-   <span data-ttu-id="548bd-160">**名前**の値に対応するエンティティの**EntityType**属性、 **EntitySet**上の要素。</span><span class="sxs-lookup"><span data-stu-id="548bd-160">The **Name** of the entity corresponds to the value of the **EntityType** attribute in the **EntitySet** element above.</span></span>
-   <span data-ttu-id="548bd-161">プロパティ名で SQL ステートメントによって返される列名に対応、 **DefiningQuery**上の要素。</span><span class="sxs-lookup"><span data-stu-id="548bd-161">The property names correspond to the column names returned by the SQL statement in the **DefiningQuery** element above.</span></span>
-   <span data-ttu-id="548bd-162">この例では、キー値が一意になるように、エンティティ キーは 3 つのプロパティで構成されています。</span><span class="sxs-lookup"><span data-stu-id="548bd-162">In this example, the entity key is composed of three properties to ensure a unique key value.</span></span>

<span data-ttu-id="548bd-163">EF デザイナーでモデルを開きます。</span><span class="sxs-lookup"><span data-stu-id="548bd-163">Open the model in the EF Designer.</span></span>

-   <span data-ttu-id="548bd-164">DefiningQueryModel.edmx をダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="548bd-164">Double-click the DefiningQueryModel.edmx.</span></span>
-   <span data-ttu-id="548bd-165">たとえば**はい**に、次のメッセージ。</span><span class="sxs-lookup"><span data-stu-id="548bd-165">Say **Yes** to the following message:</span></span>

    ![2 の警告](~/ef6/media/warning2.png)

 

<span data-ttu-id="548bd-167">モデルを編集するため、デザイン サーフェイスを提供するエンティティ デザイナーが表示されます。</span><span class="sxs-lookup"><span data-stu-id="548bd-167">The Entity Designer, which provides a design surface for editing your model, is displayed.</span></span>

-   <span data-ttu-id="548bd-168">デザイナー サーフェスと選択を右クリックして**新規追加**-&gt;**エンティティ**.</span><span class="sxs-lookup"><span data-stu-id="548bd-168">Right-click the designer surface and select **Add New**-&gt;**Entity…**.</span></span>
-   <span data-ttu-id="548bd-169">指定**GradeReport**エンティティ名と**CourseID**の**キー プロパティ**します。</span><span class="sxs-lookup"><span data-stu-id="548bd-169">Specify **GradeReport** for the entity name and **CourseID** for the **Key Property**.</span></span>
-   <span data-ttu-id="548bd-170">右クリックし、 **GradeReport**エンティティと選択**新規追加**- &gt; **スカラー プロパティ**します。</span><span class="sxs-lookup"><span data-stu-id="548bd-170">Right-click the **GradeReport** entity and select **Add New**-&gt; **Scalar Property**.</span></span>
-   <span data-ttu-id="548bd-171">プロパティの既定の名前を変更する**FirstName**します。</span><span class="sxs-lookup"><span data-stu-id="548bd-171">Change the default name of the property to **FirstName**.</span></span>
-   <span data-ttu-id="548bd-172">もう 1 つのスカラー プロパティを追加し、指定**LastName**名。</span><span class="sxs-lookup"><span data-stu-id="548bd-172">Add another scalar property and specify **LastName** for the name.</span></span>
-   <span data-ttu-id="548bd-173">もう 1 つのスカラー プロパティを追加し、指定**グレード**名。</span><span class="sxs-lookup"><span data-stu-id="548bd-173">Add another scalar property and specify **Grade** for the name.</span></span>
-   <span data-ttu-id="548bd-174">**プロパティ**ウィンドウで、変更、**グレード**の**型**プロパティを**Decimal**します。</span><span class="sxs-lookup"><span data-stu-id="548bd-174">In the **Properties** window, change the **Grade**’s **Type** property to **Decimal**.</span></span>
-   <span data-ttu-id="548bd-175">選択、 **FirstName**と**LastName**プロパティ。</span><span class="sxs-lookup"><span data-stu-id="548bd-175">Select the **FirstName** and **LastName** properties.</span></span>
-   <span data-ttu-id="548bd-176">**プロパティ**ウィンドウで、変更、 **EntityKey**プロパティの値を**True**します。</span><span class="sxs-lookup"><span data-stu-id="548bd-176">In the **Properties** window, change the **EntityKey** property value to **True**.</span></span>

<span data-ttu-id="548bd-177">次の要素が追加された結果として、 **CSDL** .edmx ファイルのセクション。</span><span class="sxs-lookup"><span data-stu-id="548bd-177">As a result, the following elements were added to the **CSDL** section of the .edmx file.</span></span>

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a><span data-ttu-id="548bd-178">定義クエリをエンティティ型にマップします。</span><span class="sxs-lookup"><span data-stu-id="548bd-178">Map the Defining Query to the Entity Type</span></span>

<span data-ttu-id="548bd-179">この手順では、概念にマップするマッピングの詳細 ウィンドウとストレージ エンティティ型使用しています。</span><span class="sxs-lookup"><span data-stu-id="548bd-179">In this step, we will use the Mapping Details window to map the conceptual and storage entity types.</span></span>

-   <span data-ttu-id="548bd-180">右クリックし、 **GradeReport**サーフェスと選択の設計上のエンティティに**テーブル マッピング**します。</span><span class="sxs-lookup"><span data-stu-id="548bd-180">Right-click the **GradeReport** entity on the design surface and select **Table Mapping**.</span></span>  
    <span data-ttu-id="548bd-181">**マッピングの詳細**ウィンドウが表示されます。</span><span class="sxs-lookup"><span data-stu-id="548bd-181">The **Mapping Details** window is displayed.</span></span>
-   <span data-ttu-id="548bd-182">選択**GradeReport**から、 **&lt;テーブルまたはビューの追加&gt;** ドロップダウン リスト (下にある**テーブル**秒)。</span><span class="sxs-lookup"><span data-stu-id="548bd-182">Select **GradeReport** from the **&lt;Add a Table or View&gt;** dropdown list (located under **Table**s).</span></span>  
    <span data-ttu-id="548bd-183">既定の概念の間のマッピングと記憶域**GradeReport**エンティティ型が表示されます。</span><span class="sxs-lookup"><span data-stu-id="548bd-183">Default mappings between the conceptual and storage **GradeReport** entity type appear.</span></span>  
    <span data-ttu-id="548bd-184">![マッピング Details3](~/ef6/media/mappingdetails.png)</span><span class="sxs-lookup"><span data-stu-id="548bd-184">![Mapping Details3](~/ef6/media/mappingdetails.png)</span></span>

<span data-ttu-id="548bd-185">結果として、 **EntitySetMapping**要素は、.edmx ファイルのマッピング セクションに追加されます。</span><span class="sxs-lookup"><span data-stu-id="548bd-185">As a result, the **EntitySetMapping** element is added to the mapping section of the .edmx file.</span></span> 

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

-   <span data-ttu-id="548bd-186">アプリケーションをコンパイルします。</span><span class="sxs-lookup"><span data-stu-id="548bd-186">Compile the application.</span></span>

 

## <a name="call-the-defining-query-in-your-code"></a><span data-ttu-id="548bd-187">コードで定義クエリを呼び出す</span><span class="sxs-lookup"><span data-stu-id="548bd-187">Call the Defining Query in your Code</span></span>

<span data-ttu-id="548bd-188">使用して、定義クエリを実行できるようになりました、 **GradeReport**エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="548bd-188">You can now execute the defining query by using the **GradeReport** entity type.</span></span> 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
