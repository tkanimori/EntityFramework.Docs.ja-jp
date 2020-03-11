---
title: リレーションシップ-EF デザイナー-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
ms.openlocfilehash: d429c39dafbf183caabdc85748c188deb8dd6f66
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415077"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="29b41-102">リレーションシップ-EF デザイナー</span><span class="sxs-lookup"><span data-stu-id="29b41-102">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="29b41-103">このページでは、EF デザイナーを使用してモデル内のリレーションシップを設定する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="29b41-103">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="29b41-104">EF のリレーションシップに関する一般的な情報と、リレーションシップを使用してデータにアクセスして操作する方法については、「[リレーションシップ & ナビゲーションプロパティ](~/ef6/fundamentals/relationships.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="29b41-104">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).</span></span>

<span data-ttu-id="29b41-105">アソシエーションは、モデル内のエンティティ型間のリレーションシップを定義します。</span><span class="sxs-lookup"><span data-stu-id="29b41-105">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="29b41-106">このトピックでは、Entity Framework Designer (EF デザイナー) との関連付けをマップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="29b41-106">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="29b41-107">次の図は、EF デザイナーを操作するときに使用されるメインウィンドウを示しています。</span><span class="sxs-lookup"><span data-stu-id="29b41-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF デザイナー](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="29b41-109">概念モデルをビルドすると、マップされていないエンティティとアソシエーションについての警告が [エラー一覧] に表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="29b41-109">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="29b41-110">モデルからデータベースを生成することを選択すると、エラーが解消されるため、これらの警告は無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="29b41-110">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="29b41-111">アソシエーションの概要</span><span class="sxs-lookup"><span data-stu-id="29b41-111">Associations Overview</span></span>

<span data-ttu-id="29b41-112">EF デザイナーを使用してモデルをデザインすると、.edmx ファイルがモデルを表します。</span><span class="sxs-lookup"><span data-stu-id="29b41-112">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="29b41-113">.Edmx ファイルでは、 **Association**要素は2つのエンティティ型間のリレーションシップを定義します。</span><span class="sxs-lookup"><span data-stu-id="29b41-113">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="29b41-114">アソシエーションでは、リレーションシップに関連するエンティティ型、およびリレーションシップの各 End におけるエンティティ型の数 (多重度と呼ばれる) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="29b41-114">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="29b41-115">アソシエーション end の多重度には、1 (1)、0または 1 (0 ..1)、または多数 (\*) の値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="29b41-115">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="29b41-116">この情報は、2つの子**End**要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-116">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="29b41-117">実行時に、アソシエーションの一方の end にあるエンティティ型のインスタンスには、ナビゲーションプロパティまたは外部キーを使用してアクセスできます (エンティティで外部キーを公開する場合)。</span><span class="sxs-lookup"><span data-stu-id="29b41-117">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="29b41-118">外部キーが公開されている場合、エンティティ間のリレーションシップは、オブジェクト ( **Association**要素の子要素 **) を使用**して管理されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-118">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="29b41-119">エンティティ内のリレーションシップに対しては、常に外部キーを公開することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="29b41-119">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="29b41-120">多対多 (\*:\*) では、エンティティに外部キーを追加することはできません。</span><span class="sxs-lookup"><span data-stu-id="29b41-120">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="29b41-121">\*:\* リレーションシップでは、関連付け情報は独立したオブジェクトで管理されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-121">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="29b41-122">CSDL 要素の詳細について**は、「** csdl の[仕様](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md) **」を参照**してください。</span><span class="sxs-lookup"><span data-stu-id="29b41-122">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="29b41-123">関連付けの作成と削除</span><span class="sxs-lookup"><span data-stu-id="29b41-123">Create and Delete Associations</span></span>

<span data-ttu-id="29b41-124">EF デザイナーとの関連付けを作成すると、.edmx ファイルのモデルコンテンツが更新されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-124">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="29b41-125">アソシエーションを作成した後、アソシエーションのマッピングを作成する必要があります (このトピックで後述します)。</span><span class="sxs-lookup"><span data-stu-id="29b41-125">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="29b41-126">このセクションでは、モデル間の関連付けを作成するために必要なエンティティを既に追加していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="29b41-126">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="29b41-127">アソシエーションを作成するには</span><span class="sxs-lookup"><span data-stu-id="29b41-127">To create an association</span></span>

1.  <span data-ttu-id="29b41-128">デザイン画面の空の領域を右クリックし、[ **新規追加**] をポイントして、[ **関連付け...** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-128">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="29b41-129">**[関連付けの追加]** ダイアログボックスで、アソシエーションの設定を入力します。</span><span class="sxs-lookup"><span data-stu-id="29b41-129">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![関連付けの追加](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="29b41-131">ナビゲーションプロパティを **クリアし、 **外部キープロパティをエンティティ型のエンティティ **型 &lt;の名前&gt; エンティティチェックボックスに追加 **することによって、ナビゲーションプロパティまたは外部キープロパティをアソシエーションの end のエンティティに追加しないことを選択できます。</span><span class="sxs-lookup"><span data-stu-id="29b41-131">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the **Navigation Property **and **Add foreign key properties to the &lt;entity type name&gt; Entity **checkboxes.</span></span> <span data-ttu-id="29b41-132">ナビゲーション プロパティを 1 つだけ追加する場合、アソシエーションの検査は一方向のみ可能になります。</span><span class="sxs-lookup"><span data-stu-id="29b41-132">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="29b41-133">ナビゲーション プロパティを追加しない場合、アソシエーションの End にあるエンティティにアクセスするには、外部キー プロパティを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="29b41-133">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="29b41-134"> **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="29b41-134">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="29b41-135">アソシエーションを削除するには</span><span class="sxs-lookup"><span data-stu-id="29b41-135">To delete an association</span></span>

<span data-ttu-id="29b41-136">関連付けを削除するには、次のいずれかの操作を行います。</span><span class="sxs-lookup"><span data-stu-id="29b41-136">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="29b41-137">EF デザイナー画面でアソシエーションを右クリックし、[ **削除**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-137">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="29b41-138">または -</span><span class="sxs-lookup"><span data-stu-id="29b41-138">OR -</span></span>

-   <span data-ttu-id="29b41-139">1 つまたは複数のアソシエーションを選択し、{1}Del{2} キーを押します。</span><span class="sxs-lookup"><span data-stu-id="29b41-139">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="29b41-140">エンティティに外部キーのプロパティを含める (参照に関する制約)</span><span class="sxs-lookup"><span data-stu-id="29b41-140">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="29b41-141">エンティティ内のリレーションシップに対しては、常に外部キーを公開することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="29b41-141">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="29b41-142">Entity Framework は、参照に関する制約を使用して、プロパティがリレーションシップの外部キーとして機能することを識別します。</span><span class="sxs-lookup"><span data-stu-id="29b41-142">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="29b41-143">リレーションシップの作成時に [***外部キーのプロパティを &lt;エンティティ型の名前&gt; エンティティに追加する***] チェックボックスをオンにした場合は、この参照に関する制約が追加されています。</span><span class="sxs-lookup"><span data-stu-id="29b41-143">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="29b41-144">EF デザイナーを使用して参照に関する制約を追加または編集すると、EF デザイナーによって、.edmx ファイルの CSDL コンテンツ**の 要素**が追加または変更されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-144">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="29b41-145">編集するアソシエーションをダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="29b41-145">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="29b41-146">[ **参照制約**の ] ダイアログボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-146">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="29b41-147">[ **プリンシパル** ] ドロップダウンリストから、参照制約のプリンシパルエンティティを選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-147">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="29b41-148">エンティティのキープロパティがダイアログボックスの [ **プリンシパルキー** ] の一覧に追加されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-148">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="29b41-149">[ **依存** ] ドロップダウンリストから、参照制約の依存エンティティを選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-149">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="29b41-150">依存キーを持つプリンシパルキーごとに、[ **依存キー** ] 列のドロップダウンリストから対応する依存キーを選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-150">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![Ref 制約](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="29b41-152"> **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="29b41-152">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="29b41-153">アソシエーションのマッピングを作成および編集する</span><span class="sxs-lookup"><span data-stu-id="29b41-153">Create and Edit Association Mappings</span></span>

<span data-ttu-id="29b41-154">EF デザイナーの [**マッピングの詳細** ] ウィンドウで、アソシエーションをデータベースにマップする方法を指定できます。</span><span class="sxs-lookup"><span data-stu-id="29b41-154">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="29b41-155">参照制約が指定されていないアソシエーションについてのみ、詳細をマップできます。</span><span class="sxs-lookup"><span data-stu-id="29b41-155">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="29b41-156">参照制約が指定されている場合は、外部キープロパティがエンティティに含まれます。また、エンティティのマッピング詳細を使用して、外部キーのマップ先の列を制御できます。</span><span class="sxs-lookup"><span data-stu-id="29b41-156">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="29b41-157">アソシエーションのマッピングを作成する</span><span class="sxs-lookup"><span data-stu-id="29b41-157">Create an association mapping</span></span>

-   <span data-ttu-id="29b41-158">デザイン画面でアソシエーションを右クリックし、[ **テーブルマッピング**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-158">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="29b41-159">これにより、[マッピングの **詳細** ] ウィンドウにアソシエーションのマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-159">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="29b41-160">[ **テーブルまたはビューの追加] を**クリックします。</span><span class="sxs-lookup"><span data-stu-id="29b41-160">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="29b41-161">ストレージ モデル内のすべてのテーブルを示すドロップダウン リストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-161">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="29b41-162">アソシエーションがマップされるテーブルを選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-162">Select the table to which the association will map.</span></span>
    <span data-ttu-id="29b41-163">[ **マッピングの詳細** ] ウィンドウには、アソシエーションの両端と、各**End**のエンティティ型のキープロパティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-163">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="29b41-164">キープロパティごとに、[ **列** ] フィールドをクリックし、プロパティがマップされる列を選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-164">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![マッピングの詳細4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="29b41-166">アソシエーションのマッピングを編集する</span><span class="sxs-lookup"><span data-stu-id="29b41-166">Edit an association mapping</span></span>

-   <span data-ttu-id="29b41-167">デザイン画面でアソシエーションを右クリックし、[ **テーブルマッピング**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-167">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="29b41-168">これにより、[マッピングの **詳細** ] ウィンドウにアソシエーションのマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-168">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="29b41-169">[マップ] をクリックして **、テーブル名&gt;&lt;** します。</span><span class="sxs-lookup"><span data-stu-id="29b41-169">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="29b41-170">ストレージ モデル内のすべてのテーブルを示すドロップダウン リストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-170">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="29b41-171">アソシエーションがマップされるテーブルを選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-171">Select the table to which the association will map.</span></span>
    <span data-ttu-id="29b41-172">[ **マッピングの詳細** ] ウィンドウには、アソシエーションの両端と、各 End のエンティティ型のキープロパティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-172">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="29b41-173">キープロパティごとに、[ **列** ] フィールドをクリックし、プロパティがマップされる列を選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-173">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="29b41-174">ナビゲーションプロパティの編集と削除</span><span class="sxs-lookup"><span data-stu-id="29b41-174">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="29b41-175">ナビゲーションプロパティは、モデル内のアソシエーションの end でエンティティを検索するために使用されるショートカットプロパティです。</span><span class="sxs-lookup"><span data-stu-id="29b41-175">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="29b41-176">ナビゲーション プロパティは、2 つのエンティティ型の間のアソシエーションを作成するときに作成できます。</span><span class="sxs-lookup"><span data-stu-id="29b41-176">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="29b41-177">ナビゲーションプロパティを編集するには</span><span class="sxs-lookup"><span data-stu-id="29b41-177">To edit navigation properties</span></span>

-   <span data-ttu-id="29b41-178">EF デザイナー画面でナビゲーションプロパティを選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-178">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="29b41-179">ナビゲーションプロパティに関する情報は、Visual Studio の [ **プロパティ** ] ウィンドウに表示されます。</span><span class="sxs-lookup"><span data-stu-id="29b41-179">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="29b41-180">[ **プロパティ** ] ウィンドウでプロパティの設定を変更します。</span><span class="sxs-lookup"><span data-stu-id="29b41-180">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="29b41-181">ナビゲーションプロパティを削除するには</span><span class="sxs-lookup"><span data-stu-id="29b41-181">To delete navigation properties</span></span>

-   <span data-ttu-id="29b41-182">外部キーが概念モデルのエンティティ型で公開されない場合、ナビゲーション プロパティを削除すると、対応するアソシエーションの検査が一方向のみ可能になるか、またはまったく検査できなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="29b41-182">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="29b41-183">EF デザイナー画面でナビゲーションプロパティを右クリックし、[ **削除**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="29b41-183">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>
