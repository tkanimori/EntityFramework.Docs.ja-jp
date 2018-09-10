---
title: リレーションシップの EF デザイナー - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 402fe960-754b-470f-976b-e5de3e9986b5
ms.openlocfilehash: e1912a5e00e51b4f07b1ac83848fdbe0aa4755aa
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250713"
---
# <a name="relationships---ef-designer"></a><span data-ttu-id="f9bee-102">リレーションシップの EF デザイナー</span><span class="sxs-lookup"><span data-stu-id="f9bee-102">Relationships - EF Designer</span></span>
> [!NOTE]
> <span data-ttu-id="f9bee-103">このページは、EF Designer を使用して、モデル内の関係を設定する方法についての情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-103">This page provides information about setting up relationships in your model using the EF Designer.</span></span> <span data-ttu-id="f9bee-104">EF とアクセスし、リレーションシップを使用してデータを操作する方法に関係の詳細については、次を参照してください。[リレーションシップとナビゲーション プロパティ](~/ef6/fundamentals/relationships.md)します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-104">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).</span></span>

<span data-ttu-id="f9bee-105">アソシエーションは、モデルのエンティティ型間のリレーションシップを定義します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-105">Associations define relationships between entity types in a model.</span></span> <span data-ttu-id="f9bee-106">このトピックでは、Entity Framework デザイナー (EF Designer) とのアソシエーションをマップする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-106">This topic shows how to map associations with the Entity Framework Designer (EF Designer).</span></span> <span data-ttu-id="f9bee-107">次の図は、EF Designer を使用する場合に使用される主なウィンドウを示します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-107">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF デザイナー](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="f9bee-109">概念モデルをビルドすると、エラー一覧にマップされていないエンティティとアソシエーションに関する警告があります。</span><span class="sxs-lookup"><span data-stu-id="f9bee-109">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="f9bee-110">エラーが解消されますモデルからデータベースを生成することを選択した後ために、これらの警告を無視できます。</span><span class="sxs-lookup"><span data-stu-id="f9bee-110">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="associations-overview"></a><span data-ttu-id="f9bee-111">アソシエーションの概要</span><span class="sxs-lookup"><span data-stu-id="f9bee-111">Associations Overview</span></span>

<span data-ttu-id="f9bee-112">EF デザイナーを使用してモデルを設計するときに、.edmx ファイルは、モデルを表します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-112">When you design your model using the EF Designer, an .edmx file represents your model.</span></span> <span data-ttu-id="f9bee-113">.Edmx ファイルで、**アソシエーション**要素が 2 つのエンティティ型間のリレーションシップを定義します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-113">In the .edmx file, an **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="f9bee-114">アソシエーションでは、リレーションシップに関連するエンティティ型、およびリレーションシップの各 End におけるエンティティ型の数 (多重度と呼ばれる) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f9bee-114">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="f9bee-115">値の 1 つ (1)、0 個または 1 (0..1)、または複数のアソシエーション end の多重度を持つことができます (\*)。</span><span class="sxs-lookup"><span data-stu-id="f9bee-115">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="f9bee-116">この情報が 2 つの子で指定された**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="f9bee-116">This information is specified in two child **End** elements.</span></span>

<span data-ttu-id="f9bee-117">実行時に (選択した場合、エンティティ内の外部キーを公開する) にアソシエーションの一方の end のエンティティ型のインスタンスのナビゲーション プロパティまたは外部キーからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="f9bee-117">At run time, entity type instances at one end of an association can be accessed through navigation properties or foreign keys (if you choose to expose foreign keys in your entities).</span></span> <span data-ttu-id="f9bee-118">公開されている外部キーを持つ、エンティティ間のリレーションシップが管理されている、 **ReferentialConstraint**要素 (子要素の**アソシエーション**要素)。</span><span class="sxs-lookup"><span data-stu-id="f9bee-118">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element (a child element of the **Association** element).</span></span> <span data-ttu-id="f9bee-119">常に、エンティティ内のリレーションシップの外部キーを公開することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f9bee-119">It is recommended that you always expose foreign keys for relationships in your entities.</span></span>

> [!NOTE]
> <span data-ttu-id="f9bee-120">多対多で (\*:\*) エンティティに外部キーを追加することはできません。</span><span class="sxs-lookup"><span data-stu-id="f9bee-120">In many-to-many (\*:\*) you cannot add foreign keys to the entities.</span></span> <span data-ttu-id="f9bee-121">\*:\*リレーションシップ、関連付け情報は、独立したオブジェクトで管理されます。</span><span class="sxs-lookup"><span data-stu-id="f9bee-121">In a \*:\* relationship, the association information is managed with an independent object.</span></span>

<span data-ttu-id="f9bee-122">CSDL 要素に関する情報 (**ReferentialConstraint**、**アソシエーション**など) を参照してください、 [CSDL 仕様](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-122">For information about CSDL elements (**ReferentialConstraint**, **Association**, etc.) see the [CSDL specification](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md).</span></span>

## <a name="create-and-delete-associations"></a><span data-ttu-id="f9bee-123">作成し、関連付けを削除します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-123">Create and Delete Associations</span></span>

<span data-ttu-id="f9bee-124">.Edmx ファイルのモデル コンテンツは EF Designer の更新プログラムとの関連付けを作成します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-124">Creating an association with the EF Designer updates the model content of the .edmx file.</span></span> <span data-ttu-id="f9bee-125">アソシエーションを作成した後 (このトピックで後述します)、アソシエーションのマッピングを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f9bee-125">After creating an association, you must create the mappings for the association (discussed later in this topic).</span></span>

> [!NOTE]
> <span data-ttu-id="f9bee-126">このセクションでは、モデル間のアソシエーションを作成するエンティティを既に追加したことを前提としています。</span><span class="sxs-lookup"><span data-stu-id="f9bee-126">This section assumes that you already added the entities you wish to create an association between to your model.</span></span>

### <a name="to-create-an-association"></a><span data-ttu-id="f9bee-127">アソシエーションを作成するには</span><span class="sxs-lookup"><span data-stu-id="f9bee-127">To create an association</span></span>

1.  <span data-ttu-id="f9bee-128">デザイン サーフェイスの空の領域を右クリックし、 をポイント**新規追加**、選択と**関連しています...**。</span><span class="sxs-lookup"><span data-stu-id="f9bee-128">Right-click an empty area of the design surface, point to **Add New**, and select **Association…**.</span></span>
2.  <span data-ttu-id="f9bee-129">関連付けの設定を入力、**の関連付けの追加**ダイアログ。</span><span class="sxs-lookup"><span data-stu-id="f9bee-129">Fill in the settings for the association in the **Add Association** dialog.</span></span>

    ![関連付けを追加します。](~/ef6/media/addassociation.png)

    > [!NOTE]
    > <span data-ttu-id="f9bee-131">いないアソシエーションの end にあるエンティティにオフにするとナビゲーション プロパティまたは外部キー プロパティを追加することができます、\* \* ナビゲーション プロパティ \* \* と \* \* を外部キー プロパティを追加、&lt;エンティティ型の名前&gt;エンティティ \* \*チェック ボックスをオンします。</span><span class="sxs-lookup"><span data-stu-id="f9bee-131">You can choose to not add navigation properties or foreign key properties to the entities at the ends of the association by clearing the \*\*Navigation Property \*\*and \*\*Add foreign key properties to the &lt;entity type name&gt; Entity \*\*checkboxes.</span></span> <span data-ttu-id="f9bee-132">ナビゲーション プロパティを 1 つだけ追加する場合、アソシエーションの検査は一方向のみ可能になります。</span><span class="sxs-lookup"><span data-stu-id="f9bee-132">If you add only one navigation property, the association will be traversable in only one direction.</span></span> <span data-ttu-id="f9bee-133">ナビゲーション プロパティを追加しない場合、アソシエーションの End にあるエンティティにアクセスするには、外部キー プロパティを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f9bee-133">If you add no navigation properties, you must choose to add foreign key properties in order to access entities at the ends of the association.</span></span>
    
3.  <span data-ttu-id="f9bee-134">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f9bee-134">Click **OK**.</span></span>

### <a name="to-delete-an-association"></a><span data-ttu-id="f9bee-135">アソシエーションを削除するには</span><span class="sxs-lookup"><span data-stu-id="f9bee-135">To delete an association</span></span>

<span data-ttu-id="f9bee-136">次のいずれかの関連操作を削除します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-136">To delete an association do one of the following:</span></span>

-   <span data-ttu-id="f9bee-137">EF デザイナー サーフェスと選択のアソシエーションを右クリックして**削除**します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-137">Right-click the association on the EF Designer surface and select **Delete**.</span></span>

- <span data-ttu-id="f9bee-138">または</span><span class="sxs-lookup"><span data-stu-id="f9bee-138">OR -</span></span>

-   <span data-ttu-id="f9bee-139">1 つまたは複数のアソシエーションを選択し、Del キーを押します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-139">Select one or more associations and press the DELETE key.</span></span>

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a><span data-ttu-id="f9bee-140">(参照制約)、エンティティ内の外部キー プロパティを含める</span><span class="sxs-lookup"><span data-stu-id="f9bee-140">Include Foreign Key Properties in Your Entities (Referential Constraints)</span></span>

<span data-ttu-id="f9bee-141">常に、エンティティ内のリレーションシップの外部キーを公開することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="f9bee-141">It is recommended that you always expose foreign keys for relationships in your entities.</span></span> <span data-ttu-id="f9bee-142">Entity Framework は、プロパティがリレーションシップの外部キーとして機能することを識別するために、参照に関する制約を使用します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-142">Entity Framework uses a referential constraint to identify that a property acts as the foreign key for a relationship.</span></span>

<span data-ttu-id="f9bee-143">オンにした場合、***に外部キー プロパティを追加、&lt;エンティティ型の名前&gt;エンティティ***チェック ボックスがこの参照に関する制約が追加されたリレーションシップを作成する場合。</span><span class="sxs-lookup"><span data-stu-id="f9bee-143">If you checked the ***Add foreign key properties to the &lt;entity type name&gt; Entity*** checkbox when creating a relationship, this referential constraint was added for you.</span></span>

<span data-ttu-id="f9bee-144">EF Designer を追加または変更を追加または編集、参照に関する制約 EF Designer を使用すると、 **ReferentialConstraint** .edmx ファイルの CSDL の内容の要素。</span><span class="sxs-lookup"><span data-stu-id="f9bee-144">When you use the EF Designer to add or edit a referential constraint, the EF Designer adds or modifies a **ReferentialConstraint** element in the CSDL content of the .edmx file.</span></span>

-   <span data-ttu-id="f9bee-145">編集するアソシエーションをダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="f9bee-145">Double-click the association that you want to edit.</span></span>
    <span data-ttu-id="f9bee-146">**参照に関する制約** ダイアログ ボックスが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f9bee-146">The **Referential Constraint** dialog box appears.</span></span>
-   <span data-ttu-id="f9bee-147">**プリンシパル**ドロップダウン リストで、参照に関する制約のプリンシパル エンティティを選択します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-147">From the **Principal** drop-down list, select the principal entity in the referential constraint.</span></span>
    <span data-ttu-id="f9bee-148">エンティティのキー プロパティに追加されます、**プリンシパル キー**ダイアログ ボックスの一覧。</span><span class="sxs-lookup"><span data-stu-id="f9bee-148">The entity's key properties are added to the **Principal Key** list in the dialog box.</span></span>
-   <span data-ttu-id="f9bee-149">**依存**ドロップダウン リストで、参照に関する制約の依存エンティティを選択します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-149">From the **Dependent** drop-down list, select the dependent entity in the referential constraint.</span></span>
-   <span data-ttu-id="f9bee-150">依存キーを持つ各プリンシパル キーで、ドロップダウン リストから対応する依存キーを選択します。、**依存キー**列。</span><span class="sxs-lookup"><span data-stu-id="f9bee-150">For each principal key that has a dependent key, select a corresponding dependent key from the drop-down lists in the **Dependent Key** column.</span></span>

    ![Ref 制約](~/ef6/media/refconstraint.png)

-   <span data-ttu-id="f9bee-152">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="f9bee-152">Click **OK**.</span></span>

## <a name="create-and-edit-association-mappings"></a><span data-ttu-id="f9bee-153">作成し、アソシエーションのマッピングの編集</span><span class="sxs-lookup"><span data-stu-id="f9bee-153">Create and Edit Association Mappings</span></span>

<span data-ttu-id="f9bee-154">アソシエーションがデータベースにマップする方法を指定することができます、**マッピングの詳細**EF デザイナーのウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="f9bee-154">You can specify how an association maps to the database in the **Mapping Details** window of the EF Designer.</span></span>

> [!NOTE]
> <span data-ttu-id="f9bee-155">指定された参照に関する制約がない関連付けの詳細のみマップできます。</span><span class="sxs-lookup"><span data-stu-id="f9bee-155">You can only map details for the associations that do not have a referential constraint specified.</span></span> <span data-ttu-id="f9bee-156">参照に関する制約が指定されている場合、エンティティの外部キー プロパティが含まれているし、コントロールの外部キーにマッピングする列にエンティティのマッピングの詳細を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="f9bee-156">If a referential constraint is specified then a foreign key property is included in the entity and you can use the Mapping Details for the entity to control which column the foreign key maps to.</span></span>

### <a name="create-an-association-mapping"></a><span data-ttu-id="f9bee-157">アソシエーションのマッピングを作成します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-157">Create an association mapping</span></span>

-   <span data-ttu-id="f9bee-158">デザイン サーフェスと選択のアソシエーションを右クリックして**テーブル マッピング**します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-158">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="f9bee-159">アソシエーションのマッピングが表示されます、**マッピングの詳細**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="f9bee-159">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="f9bee-160">クリックして**テーブルまたはビューの追加**します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-160">Click **Add a Table or View**.</span></span>
    <span data-ttu-id="f9bee-161">ストレージ モデル内のすべてのテーブルを示すドロップダウン リストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f9bee-161">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="f9bee-162">アソシエーションがマップされるテーブルを選択します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-162">Select the table to which the association will map.</span></span>
    <span data-ttu-id="f9bee-163">**マッピングの詳細**ウィンドウが表示されます、アソシエーション、エンティティ型のキー プロパティの両方の end の各**エンド**します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-163">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each **End**.</span></span>
-   <span data-ttu-id="f9bee-164">各キーのプロパティをクリックして、**列**フィールドし、プロパティのマップ先の列を選択します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-164">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

    ![4 マッピングの詳細](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a><span data-ttu-id="f9bee-166">アソシエーションのマッピングを編集します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-166">Edit an association mapping</span></span>

-   <span data-ttu-id="f9bee-167">デザイン サーフェスと選択のアソシエーションを右クリックして**テーブル マッピング**します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-167">Right-click an association in the design surface and select **Table Mapping**.</span></span>
    <span data-ttu-id="f9bee-168">アソシエーションのマッピングが表示されます、**マッピングの詳細**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="f9bee-168">This displays the association mapping in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="f9bee-169">クリックして**マップ&lt;テーブル名&gt;** します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-169">Click **Maps to &lt;Table Name&gt;**.</span></span>
    <span data-ttu-id="f9bee-170">ストレージ モデル内のすべてのテーブルを示すドロップダウン リストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f9bee-170">A drop-down list appears that includes all the tables in the storage model.</span></span>
-   <span data-ttu-id="f9bee-171">アソシエーションがマップされるテーブルを選択します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-171">Select the table to which the association will map.</span></span>
    <span data-ttu-id="f9bee-172">**マッピングの詳細**ウィンドウには、両方の End で関連付けとエンティティ型のキー プロパティの両方の end が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f9bee-172">The **Mapping Details** window displays both ends of the association and the key properties for the entity type at each End.</span></span>
-   <span data-ttu-id="f9bee-173">各キーのプロパティをクリックして、**列**フィールドし、プロパティのマップ先の列を選択します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-173">For each key property, click the **Column** field, and select the column to which the property will map.</span></span>

## <a name="edit-and-delete-navigation-properties"></a><span data-ttu-id="f9bee-174">編集およびナビゲーション プロパティの削除</span><span class="sxs-lookup"><span data-stu-id="f9bee-174">Edit and Delete Navigation Properties</span></span>

<span data-ttu-id="f9bee-175">ナビゲーション プロパティには、モデル内で、アソシエーションの末尾にあるエンティティを検索するために使用するショートカット プロパティです。</span><span class="sxs-lookup"><span data-stu-id="f9bee-175">Navigation properties are shortcut properties that are used to locate the entities at the ends of an association in a model.</span></span> <span data-ttu-id="f9bee-176">ナビゲーション プロパティは、2 つのエンティティ型の間のアソシエーションを作成するときに作成できます。</span><span class="sxs-lookup"><span data-stu-id="f9bee-176">Navigation properties can be created when you create an association between two entity types.</span></span>

#### <a name="to-edit-navigation-properties"></a><span data-ttu-id="f9bee-177">ナビゲーション プロパティを編集するには</span><span class="sxs-lookup"><span data-stu-id="f9bee-177">To edit navigation properties</span></span>

-   <span data-ttu-id="f9bee-178">EF デザイナー画面でナビゲーション プロパティを選択します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-178">Select a navigation property on the EF Designer surface.</span></span>
    <span data-ttu-id="f9bee-179">Visual Studio でのナビゲーション プロパティに関する情報が表示されます**プロパティ**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="f9bee-179">Information about the navigation property is displayed in the Visual Studio **Properties** window.</span></span>
-   <span data-ttu-id="f9bee-180">プロパティの設定を変更、**プロパティ**ウィンドウ。</span><span class="sxs-lookup"><span data-stu-id="f9bee-180">Change the property settings in the **Properties** window.</span></span>

#### <a name="to-delete-navigation-properties"></a><span data-ttu-id="f9bee-181">ナビゲーション プロパティを削除するには</span><span class="sxs-lookup"><span data-stu-id="f9bee-181">To delete navigation properties</span></span>

-   <span data-ttu-id="f9bee-182">外部キーが概念モデルのエンティティ型で公開されない場合、ナビゲーション プロパティを削除すると、対応するアソシエーションの検査が一方向のみ可能になるか、またはまったく検査できなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f9bee-182">If foreign keys are not exposed on entity types in the conceptual model, deleting a navigation property may make the corresponding association traversable in only one direction or not traversable at all.</span></span>
-   <span data-ttu-id="f9bee-183">ナビゲーション プロパティを EF デザイナー サーフェスと選択を右クリックして**削除**します。</span><span class="sxs-lookup"><span data-stu-id="f9bee-183">Right-click a navigation property on the EF Designer surface and select **Delete**.</span></span>
