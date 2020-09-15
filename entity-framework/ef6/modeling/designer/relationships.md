---
title: リレーションシップ-EF デザイナー-EF6
description: リレーションシップ-Entity Framework 6 の EF デザイナー
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: 65960dcfcafc239b5ecd8b396a52dababbb4ab08
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073293"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="dc795-103">リレーションシップ-EF デザイナー</span><span class="sxs-lookup"><span data-stu-id="dc795-103">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="dc795-104">このページでは、EF デザイナーを使用してモデル内のリレーションシップを設定する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="dc795-104">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="dc795-105">EF のリレーションシップに関する一般的な情報と、リレーションシップを使用してデータにアクセスして操作する方法については、「 [リレーションシップ & ナビゲーションプロパティ](xref:ef6/fundamentals/relationships)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="dc795-105">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).</span></span>

<span data-ttu-id="dc795-106">アソシエーションは、モデル内のエンティティ型間のリレーションシップを定義します。</span><span class="sxs-lookup"><span data-stu-id="dc795-106">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="dc795-107">このトピックでは、Entity Framework Designer (EF デザイナー) との関連付けをマップする方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="dc795-107">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="dc795-108">次の図は、EF デザイナーを操作するときに使用されるメインウィンドウを示しています。</span><span class="sxs-lookup"><span data-stu-id="dc795-108">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF デザイナー](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="dc795-110">概念モデルをビルドすると、マップされていないエンティティとアソシエーションについての警告が [エラー一覧] に表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="dc795-110">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="dc795-111">モデルからデータベースを生成することを選択すると、エラーが解消されるため、これらの警告は無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="dc795-111">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="dc795-112">アソシエーションの概要</span><span class="sxs-lookup"><span data-stu-id="dc795-112">Associations Overview</span></span>

<span data-ttu-id="dc795-113">EF デザイナーを使用してモデルをデザインすると、.edmx ファイルがモデルを表します。</span><span class="sxs-lookup"><span data-stu-id="dc795-113">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="dc795-114">.Edmx ファイルでは、 **Association** 要素は2つのエンティティ型間のリレーションシップを定義します。</span><span class="sxs-lookup"><span data-stu-id="dc795-114">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="dc795-115">アソシエーションでは、リレーションシップに関連するエンティティ型、およびリレーションシップの各 End におけるエンティティ型の数 (多重度と呼ばれる) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dc795-115">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="dc795-116">アソシエーション end の複数要素の接続性には、1 (1)、0または 1 (0 ..1)、または many () を指定でき \* ます。</span><span class="sxs-lookup"><span data-stu-id="dc795-116">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="dc795-117">この情報は、2つの子 **End** 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="dc795-117">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="dc795-118">実行時に、アソシエーションの一方の end にあるエンティティ型のインスタンスには、ナビゲーションプロパティまたは外部キーを使用してアクセスできます (エンティティで外部キーを公開する場合)。</span><span class="sxs-lookup"><span data-stu-id="dc795-118">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="dc795-119">外部キーが公開されている場合、エンティティ間のリレーションシップは、オブジェクト ( **Association**要素の子要素 **) を使用**して管理されます。</span><span class="sxs-lookup"><span data-stu-id="dc795-119">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="dc795-120">エンティティ内のリレーションシップに対しては、常に外部キーを公開することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dc795-120">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="dc795-121">多対多 ( \* :) では \* 、エンティティに外部キーを追加することはできません。</span><span class="sxs-lookup"><span data-stu-id="dc795-121">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="dc795-122">\*: リレーションシップで \* は、関連付け情報は独立したオブジェクトで管理されます。</span><span class="sxs-lookup"><span data-stu-id="dc795-122">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="dc795-123">CSDL 要素の詳細について**は、「** csdl の[仕様](xref:ef6/modeling/designer/advanced/edmx/csdl-spec) **」を参照**してください。</span><span class="sxs-lookup"><span data-stu-id="dc795-123">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="dc795-124">関連付けの作成と削除</span><span class="sxs-lookup"><span data-stu-id="dc795-124">Create and Delete Associations</span></span>

<span data-ttu-id="dc795-125">EF デザイナーとの関連付けを作成すると、.edmx ファイルのモデルコンテンツが更新されます。</span><span class="sxs-lookup"><span data-stu-id="dc795-125">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="dc795-126">アソシエーションを作成した後、アソシエーションのマッピングを作成する必要があります (このトピックで後述します)。</span><span class="sxs-lookup"><span data-stu-id="dc795-126">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="dc795-127">このセクションでは、モデル間の関連付けを作成するために必要なエンティティを既に追加していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="dc795-127">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="dc795-128">アソシエーションを作成するには</span><span class="sxs-lookup"><span data-stu-id="dc795-128">To create an association</span></span>

1.  <span data-ttu-id="dc795-129">デザイン画面の空の領域を右クリックし、[ **新規追加**] をポイントして、[ **関連付け...**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="dc795-129">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="dc795-130">[ **関連付けの追加** ] ダイアログボックスで、アソシエーションの設定を入力します。</span><span class="sxs-lookup"><span data-stu-id="dc795-130">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![関連付けの追加](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="dc795-132">ナビゲーションプロパティを **クリアし、 ** **外部キープロパティを &lt; エンティティ型名 &gt; エンティティ **のチェックボックスに追加することで、アソシエーションの end のエンティティにナビゲーションプロパティまたは外部キープロパティを追加しないように選択できます。</span><span class="sxs-lookup"><span data-stu-id="dc795-132">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the **Navigation Property **and **Add foreign key properties to the &lt;entity type name&gt; Entity **checkboxes.</span></span> <span data-ttu-id="dc795-133">ナビゲーション プロパティを 1 つだけ追加する場合、アソシエーションの検査は一方向のみ可能になります。</span><span class="sxs-lookup"><span data-stu-id="dc795-133">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="dc795-134">ナビゲーション プロパティを追加しない場合、アソシエーションの End にあるエンティティにアクセスするには、外部キー プロパティを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="dc795-134">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="dc795-135"> **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dc795-135">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="dc795-136">アソシエーションを削除するには</span><span class="sxs-lookup"><span data-stu-id="dc795-136">To delete an association</span></span>

<span data-ttu-id="dc795-137">関連付けを削除するには、次のいずれかの操作を行います。</span><span class="sxs-lookup"><span data-stu-id="dc795-137">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="dc795-138">EF デザイナー画面でアソシエーションを右クリックし、[ **削除**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="dc795-138">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="dc795-139">または -</span><span class="sxs-lookup"><span data-stu-id="dc795-139">OR -</span></span>

-   <span data-ttu-id="dc795-140">1 つまたは複数のアソシエーションを選択し、Del キーを押します。</span><span class="sxs-lookup"><span data-stu-id="dc795-140">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="dc795-141">エンティティに外部キーのプロパティを含める (参照に関する制約)</span><span class="sxs-lookup"><span data-stu-id="dc795-141">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="dc795-142">エンティティ内のリレーションシップに対しては、常に外部キーを公開することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="dc795-142">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="dc795-143">Entity Framework は、参照に関する制約を使用して、プロパティがリレーションシップの外部キーとして機能することを識別します。</span><span class="sxs-lookup"><span data-stu-id="dc795-143">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="dc795-144">リレーションシップの作成時に [ ***外部キーのプロパティを &lt; エンティティ型名 &gt; エンティティに追加する*** ] チェックボックスをオンにした場合は、この参照制約が追加されています。</span><span class="sxs-lookup"><span data-stu-id="dc795-144">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="dc795-145">EF デザイナーを使用して参照に関する制約を追加または編集すると、EF デザイナーによって .edmx ファイルの CSDL コンテンツの参照先要素**が追加または変更**されます。  </span><span class="sxs-lookup"><span data-stu-id="dc795-145">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="dc795-146">編集するアソシエーションをダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="dc795-146">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="dc795-147">[ **参照に関する制約**   ] ダイアログボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="dc795-147">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="dc795-148">[ **プリンシパル**   ] ドロップダウンリストから、参照制約のプリンシパルエンティティを選択します。</span><span class="sxs-lookup"><span data-stu-id="dc795-148">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="dc795-149">エンティティのキープロパティがダイアログボックスの [ **プリンシパルキー**] の一覧に追加され   ます。</span><span class="sxs-lookup"><span data-stu-id="dc795-149">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="dc795-150">[ **依存**   ] ドロップダウンリストから、参照に関する制約の依存エンティティを選択します。</span><span class="sxs-lookup"><span data-stu-id="dc795-150">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="dc795-151">依存キーを持つ各プリンシパルキーについて、 **依存**キー列のドロップダウンリストから対応する依存キーを選択し   ます。</span><span class="sxs-lookup"><span data-stu-id="dc795-151">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![Ref 制約](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="dc795-153"> **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dc795-153">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="dc795-154">アソシエーションのマッピングを作成および編集する</span><span class="sxs-lookup"><span data-stu-id="dc795-154">Create and Edit Association Mappings</span></span>

<span data-ttu-id="dc795-155">EF デザイナーの [**マッピングの詳細**] ウィンドウで、アソシエーションがデータベースにどのようにマップされるかを指定でき   ます。</span><span class="sxs-lookup"><span data-stu-id="dc795-155">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="dc795-156">参照制約が指定されていないアソシエーションについてのみ、詳細をマップできます。</span><span class="sxs-lookup"><span data-stu-id="dc795-156">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="dc795-157">参照制約が指定されている場合は、外部キープロパティがエンティティに含まれます。また、エンティティのマッピング詳細を使用して、外部キーのマップ先の列を制御できます。</span><span class="sxs-lookup"><span data-stu-id="dc795-157">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="dc795-158">アソシエーションのマッピングを作成する</span><span class="sxs-lookup"><span data-stu-id="dc795-158">Create an association mapping</span></span>

-   <span data-ttu-id="dc795-159">デザイン画面でアソシエーションを右クリックし、[ **テーブルマッピング**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="dc795-159">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="dc795-160">これにより、[マッピングの **詳細**] ウィンドウにアソシエーションのマッピングが表示され   ます。</span><span class="sxs-lookup"><span data-stu-id="dc795-160">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="dc795-161">[ **テーブルまたはビューの追加] を**クリックします。</span><span class="sxs-lookup"><span data-stu-id="dc795-161">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="dc795-162">ストレージ モデル内のすべてのテーブルを示すドロップダウン リストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="dc795-162">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="dc795-163">アソシエーションがマップされるテーブルを選択します。</span><span class="sxs-lookup"><span data-stu-id="dc795-163">Select the table to which the association will map.</span></span>
    <span data-ttu-id="dc795-164">[ **マッピングの詳細**   ] ウィンドウには、アソシエーションの両端と、各**End**のエンティティ型のキープロパティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="dc795-164">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="dc795-165">キープロパティごとに、 **列**   フィールドをクリックし、プロパティがマップされる列を選択します。</span><span class="sxs-lookup"><span data-stu-id="dc795-165">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![マッピングの詳細4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="dc795-167">アソシエーションのマッピングを編集する</span><span class="sxs-lookup"><span data-stu-id="dc795-167">Edit an association mapping</span></span>

-   <span data-ttu-id="dc795-168">デザイン画面でアソシエーションを右クリックし、[ **テーブルマッピング**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="dc795-168">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="dc795-169">これにより、[マッピングの **詳細**] ウィンドウにアソシエーションのマッピングが表示され   ます。</span><span class="sxs-lookup"><span data-stu-id="dc795-169">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="dc795-170">[ \*\* &lt; テーブル名 &gt; にマップ] を\*\*クリックします。</span><span class="sxs-lookup"><span data-stu-id="dc795-170">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="dc795-171">ストレージ モデル内のすべてのテーブルを示すドロップダウン リストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="dc795-171">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="dc795-172">アソシエーションがマップされるテーブルを選択します。</span><span class="sxs-lookup"><span data-stu-id="dc795-172">Select the table to which the association will map.</span></span>
    <span data-ttu-id="dc795-173">[ **マッピングの詳細**   ] ウィンドウには、アソシエーションの両端と、各 End のエンティティ型のキープロパティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="dc795-173">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="dc795-174">キープロパティごとに、 **列**   フィールドをクリックし、プロパティがマップされる列を選択します。</span><span class="sxs-lookup"><span data-stu-id="dc795-174">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="dc795-175">ナビゲーションプロパティの編集と削除</span><span class="sxs-lookup"><span data-stu-id="dc795-175">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="dc795-176">ナビゲーションプロパティは、モデル内のアソシエーションの end でエンティティを検索するために使用されるショートカットプロパティです。</span><span class="sxs-lookup"><span data-stu-id="dc795-176">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="dc795-177">ナビゲーション プロパティは、2 つのエンティティ型の間のアソシエーションを作成するときに作成できます。</span><span class="sxs-lookup"><span data-stu-id="dc795-177">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="dc795-178">ナビゲーションプロパティを編集するには</span><span class="sxs-lookup"><span data-stu-id="dc795-178">To edit navigation properties</span></span>

-   <span data-ttu-id="dc795-179">EF デザイナー画面でナビゲーションプロパティを選択します。</span><span class="sxs-lookup"><span data-stu-id="dc795-179">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="dc795-180">ナビゲーションプロパティに関する情報は、Visual Studio の [ **プロパティ**] ウィンドウに表示され   ます。</span><span class="sxs-lookup"><span data-stu-id="dc795-180">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="dc795-181">[ **プロパティ**] ウィンドウでプロパティの設定を変更し   ます。</span><span class="sxs-lookup"><span data-stu-id="dc795-181">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="dc795-182">ナビゲーションプロパティを削除するには</span><span class="sxs-lookup"><span data-stu-id="dc795-182">To delete navigation properties</span></span>

-   <span data-ttu-id="dc795-183">外部キーが概念モデルのエンティティ型で公開されない場合、ナビゲーション プロパティを削除すると、対応するアソシエーションの検査が一方向のみ可能になるか、またはまったく検査できなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="dc795-183">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="dc795-184">EF デザイナー画面でナビゲーションプロパティを右クリックし、[ **削除**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="dc795-184">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>
