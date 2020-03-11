---
title: 複合型-EF デザイナー-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9a8228ef-acfd-4575-860d-769d2c0e18a1
ms.openlocfilehash: a3c5578acee23688c04772d2dd0a2221779af562
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415425"
---
# <a name="complex-types---ef-designer"></a><span data-ttu-id="c43fb-102">複合型-EF デザイナー</span><span class="sxs-lookup"><span data-stu-id="c43fb-102">Complex Types - EF Designer</span></span>
<span data-ttu-id="c43fb-103">このトピックでは、複合型を Entity Framework Designer (EF デザイナー) にマップする方法と、複合型のプロパティを含むエンティティに対してクエリを実行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-103">This topic shows how to map complex types with the Entity Framework Designer (EF Designer) and how to query for entities that contain properties of complex type.</span></span>

<span data-ttu-id="c43fb-104">次の図は、EF デザイナーを操作するときに使用されるメインウィンドウを示しています。</span><span class="sxs-lookup"><span data-stu-id="c43fb-104">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF デザイナー](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="c43fb-106">概念モデルをビルドすると、マップされていないエンティティとアソシエーションについての警告が [エラー一覧] に表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="c43fb-106">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="c43fb-107">モデルからデータベースを生成することを選択すると、エラーが解消されるため、これらの警告は無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="c43fb-107">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="what-is-a-complex-type"></a><span data-ttu-id="c43fb-108">複合型とは</span><span class="sxs-lookup"><span data-stu-id="c43fb-108">What is a Complex Type</span></span>

<span data-ttu-id="c43fb-109">複合型はエンティティ型の非スカラー プロパティで、これによってスカラー プロパティをエンティティ内で整理できます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-109">Complex types are non-scalar properties of entity types that enable scalar properties to be organized within entities.</span></span> <span data-ttu-id="c43fb-110">エンティティと同様に、複合型はスカラー プロパティまたは他の複合型プロパティで構成されています。</span><span class="sxs-lookup"><span data-stu-id="c43fb-110">Like entities, complex types consist of scalar properties or other complex type properties.</span></span>

<span data-ttu-id="c43fb-111">複合型を表すオブジェクトを操作する場合は、次の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="c43fb-111">When you work with objects that represent complex types, be aware of the following:</span></span>

-   <span data-ttu-id="c43fb-112">複合型にはキーがないため、独立して存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="c43fb-112">Complex types do not have keys and therefore cannot exist independently.</span></span> <span data-ttu-id="c43fb-113">複合型は、エンティティ型またはその他の複合型のプロパティとしてのみ存在できます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-113">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="c43fb-114">複合型はアソシエーションに参加できず、ナビゲーションプロパティを含むことはできません。</span><span class="sxs-lookup"><span data-stu-id="c43fb-114">Complex types cannot participate in associations and cannot contain navigation properties.</span></span>
-   <span data-ttu-id="c43fb-115">複合型プロパティを **null**にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="c43fb-115">Complex type properties cannot be **null**.</span></span> <span data-ttu-id="c43fb-116"> **Dbcontext **が呼び出され、null の複合オブジェクトが検出されると、が発生します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-116">An **InvalidOperationException **occurs when **DbContext.SaveChanges** is called and a null complex object is encountered.</span></span> <span data-ttu-id="c43fb-117">複合オブジェクトのスカラープロパティは **null**にすることができます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-117">Scalar properties of complex objects can be **null**.</span></span>
-   <span data-ttu-id="c43fb-118">複合型は他の複合型を継承できません。</span><span class="sxs-lookup"><span data-stu-id="c43fb-118">Complex types cannot inherit from other complex types.</span></span>
-   <span data-ttu-id="c43fb-119">複合型は **クラス**として定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c43fb-119">You must define the complex type as a **class**.</span></span> 
-   <span data-ttu-id="c43fb-120">EF の**変更**が呼び出されると、複合型オブジェクトのメンバーに対する変更が検出されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-120">EF detects changes to members on a complex type object when **DbContext.DetectChanges** is called.</span></span> <span data-ttu-id="c43fb-121">Entity Framework は、次のメンバーが呼び出されたときに、検出された**変更**を自動的に呼び出します: **Dbset. Find**, **dbset. Local**, dbset. **Remove**, dbset **.** **Attach** **,** Dbset. ' dbset. **getvalidationerrors**, **dbset. Entry**, **dbchangetracker.** エントリ。</span><span class="sxs-lookup"><span data-stu-id="c43fb-121">Entity Framework calls **DetectChanges** automatically when the following members are called: **DbSet.Find**, **DbSet.Local**, **DbSet.Remove**, **DbSet.Add**, **DbSet.Attach**, **DbContext.SaveChanges**, **DbContext.GetValidationErrors**, **DbContext.Entry**, **DbChangeTracker.Entries**.</span></span>

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a><span data-ttu-id="c43fb-122">エンティティのプロパティを新しい複合型にリファクタリングする</span><span class="sxs-lookup"><span data-stu-id="c43fb-122">Refactor an Entity’s Properties into New Complex Type</span></span>

<span data-ttu-id="c43fb-123">概念モデルにエンティティが既に存在する場合は、一部のプロパティを複合型プロパティにリファクタリングすることができます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-123">If you already have an entity in your conceptual model you may want to refactor some of the properties into a complex type property.</span></span>

<span data-ttu-id="c43fb-124">デザイナー画面で、エンティティの1つ以上のプロパティ (ナビゲーションプロパティを除く) を選択し、右クリックして [ **リファクター-&gt; 新しい複合型に移動**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-124">On the designer surface, select one or more properties (excluding navigation properties) of an entity, then right-click and select **Refactor -&gt; Move to New Complex Type**.</span></span>

![リファクター](~/ef6/media/refactor.png)

<span data-ttu-id="c43fb-126">選択したプロパティを持つ新しい複合型が**モデルブラウザー**に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-126">A new complex type with the selected properties is added to the **Model Browser**.</span></span> <span data-ttu-id="c43fb-127">複合型には既定の名前が付きます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-127">The complex type is given a default name.</span></span>

<span data-ttu-id="c43fb-128">新しく作成した型の複合プロパティが、選択したプロパティと置き換わります。</span><span class="sxs-lookup"><span data-stu-id="c43fb-128">A complex property of the newly created type replaces the selected properties.</span></span> <span data-ttu-id="c43fb-129">プロパティのマッピングはすべて保持されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-129">All property mappings are preserved.</span></span>

![リファクター2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a><span data-ttu-id="c43fb-131">新しい複合型を作成する</span><span class="sxs-lookup"><span data-stu-id="c43fb-131">Create a New Complex Type</span></span>

<span data-ttu-id="c43fb-132">また、既存のエンティティのプロパティを含まない新しい複合型を作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-132">You can also create a new complex type that does not contain properties of an existing entity.</span></span>

<span data-ttu-id="c43fb-133">モデルブラウザーで **[複合型]** フォルダーを右クリックし、 **[AddNew 複合型...]** をポイントします。</span><span class="sxs-lookup"><span data-stu-id="c43fb-133">Right-click the **Complex Types** folder in the Model Browser, point to **AddNew Complex Type…**.</span></span> <span data-ttu-id="c43fb-134">または、 **[複合型]** フォルダーを選択し、キーボードの [ **Insert** ] キーを押します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-134">Alternatively, you can select the **Complex Types** folder and press the **Insert** key on your keyboard.</span></span>

![新しい複合型の追加](~/ef6/media/addnewcomplextype.png)

<span data-ttu-id="c43fb-136">既定の名前を持つ新しい複合型がフォルダーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-136">A new complex type is added to the folder with a default name.</span></span> <span data-ttu-id="c43fb-137">これで、型にプロパティを追加できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="c43fb-137">You can now add properties to the type.</span></span>

## <a name="add-properties-to-a-complex-type"></a><span data-ttu-id="c43fb-138">複合型へのプロパティの追加</span><span class="sxs-lookup"><span data-stu-id="c43fb-138">Add Properties to a Complex Type</span></span>

<span data-ttu-id="c43fb-139">複合型のプロパティはスカラー型か既存の複合型にすることができます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-139">Properties of a complex type can be scalar types or existing complex types.</span></span> <span data-ttu-id="c43fb-140">ただし、複合型のプロパティに循環参照を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="c43fb-140">However, complex type properties cannot have circular references.</span></span> <span data-ttu-id="c43fb-141">たとえば、複合型 **OnsiteCourseDetails** は、複合型 **OnsiteCourseDetails**のプロパティを持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="c43fb-141">For example, a complex type **OnsiteCourseDetails** cannot have a property of complex type **OnsiteCourseDetails**.</span></span>

<span data-ttu-id="c43fb-142">以下のいずれかの方法で、複合型にプロパティを追加できます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-142">You can add a property to a complex type in any of the ways listed below.</span></span>

-   <span data-ttu-id="c43fb-143">モデルブラウザーで複合型を右クリックし、[ **追加**] をポイントします。次に、[ **スカラープロパティ** または **複合プロパティ**] をポイントし、目的のプロパティの種類を選択します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-143">Right-click a complex type in the Model Browser, point to **Add**, then point to **Scalar Property** or **Complex Property**, then select the desired property type.</span></span> <span data-ttu-id="c43fb-144">または、複合型を選択し、キーボードの [ **Insert** ] キーを押します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-144">Alternatively, you can select a complex type and then press the **Insert** key on your keyboard.</span></span>  

    ![複合型へのプロパティの追加](~/ef6/media/addpropertiestocomplextype.png)

    <span data-ttu-id="c43fb-146">既定の名前を持つ新しいプロパティが複合型に追加されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-146">A new property is added to the complex type with a default name.</span></span>

- <span data-ttu-id="c43fb-147">または -</span><span class="sxs-lookup"><span data-stu-id="c43fb-147">OR -</span></span>

-   <span data-ttu-id="c43fb-148">**EF デザイナー**画面でエンティティプロパティを右クリックし、[ **コピー**] をクリックします。次に、**モデルブラウザー**で複合型を右クリックし、[ **貼り付け**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-148">Right-click an entity property on the **EF  Designer** surface and select **Copy**, then right-click the complex type in the **Model Browser** and select **Paste**.</span></span>

## <a name="rename-a-complex-type"></a><span data-ttu-id="c43fb-149">複合型の名前を変更する</span><span class="sxs-lookup"><span data-stu-id="c43fb-149">Rename a Complex Type</span></span>

<span data-ttu-id="c43fb-150">複合型の名前を変更すると、その型へのすべての参照がプロジェクト全体で更新されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-150">When you rename a complex type, all references to the type are updated throughout the project.</span></span>

-   <span data-ttu-id="c43fb-151">**モデルブラウザー**で複合型をゆっくりとダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="c43fb-151">Slowly double-click a complex type in the **Model Browser**.</span></span>
    <span data-ttu-id="c43fb-152">名前が選択され、編集モードになります。</span><span class="sxs-lookup"><span data-stu-id="c43fb-152">The name will be selected and in edit mode.</span></span>

- <span data-ttu-id="c43fb-153">または -</span><span class="sxs-lookup"><span data-stu-id="c43fb-153">OR -</span></span>

-   <span data-ttu-id="c43fb-154">**モデルブラウザー**で複合型を右クリックし、[ **名前の変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-154">Right-click a complex type in the **Model Browser** and select **Rename**.</span></span>

- <span data-ttu-id="c43fb-155">または -</span><span class="sxs-lookup"><span data-stu-id="c43fb-155">OR -</span></span>

-   <span data-ttu-id="c43fb-156">モデル ブラウザーで複合型を選択し、F2 キーを押します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-156">Select a complex type in the Model Browser and press the F2 key.</span></span>

- <span data-ttu-id="c43fb-157">または -</span><span class="sxs-lookup"><span data-stu-id="c43fb-157">OR -</span></span>

-   <span data-ttu-id="c43fb-158">**モデルブラウザー**で複合型を右クリックし、[ **プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-158">Right-click a complex type in the **Model Browser** and select **Properties**.</span></span> <span data-ttu-id="c43fb-159">[ **プロパティ** ] ウィンドウで名前を編集します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-159">Edit the name in the **Properties** window.</span></span>

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a><span data-ttu-id="c43fb-160">既存の複合型をエンティティに追加し、そのプロパティをテーブル列にマップします。</span><span class="sxs-lookup"><span data-stu-id="c43fb-160">Add an Existing Complex Type to an Entity and Map its Properties to Table Columns</span></span>

1.  <span data-ttu-id="c43fb-161">エンティティを右クリックして [ **新規追加**] をポイントし、[ **複合プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-161">Right-click an entity, point to **Add New**, and select **Complex Property**.</span></span>
    <span data-ttu-id="c43fb-162">既定の名前を持つ複合型プロパティがエンティティに追加されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-162">A complex type property with a default name is added to the entity.</span></span> <span data-ttu-id="c43fb-163">既存の複合型から選択した既定の型がプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-163">A default type (chosen from the existing complex types) is assigned to the property.</span></span>
2.  <span data-ttu-id="c43fb-164">[ **プロパティ** ] ウィンドウで、目的の型をプロパティに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-164">Assign the desired type to the property in the **Properties** window.</span></span>
    <span data-ttu-id="c43fb-165">複合型プロパティをエンティティに追加した後、そのプロパティをテーブル列にマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c43fb-165">After adding a complex type property to an entity, you must map its properties to table columns.</span></span>
3.  <span data-ttu-id="c43fb-166">デザイン画面または **モデルブラウザー**でエンティティ型を右クリックし [ **テーブルマッピング**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-166">Right-click an entity type on the design surface or in the **Model Browser** and select **Table Mappings**.</span></span>
    <span data-ttu-id="c43fb-167">テーブルマッピングは、[マッピングの **詳細** ] ウィンドウに表示されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-167">The table mappings are displayed in the **Mapping Details** window.</span></span>
4.  <span data-ttu-id="c43fb-168">マップ を展開して **&lt;テーブル名&gt;**   ノードに展開します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-168">Expand the **Maps to &lt;Table Name&gt;** node.</span></span>
    <span data-ttu-id="c43fb-169"> **列マッピング** ノードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-169">A **Column Mappings** node appears.</span></span>
5.  <span data-ttu-id="c43fb-170"> **列マッピング** ノードを展開します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-170">Expand the **Column Mappings** node.</span></span>
    <span data-ttu-id="c43fb-171">テーブル内のすべての列の一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-171">A list of all the columns in the table appears.</span></span> <span data-ttu-id="c43fb-172">列マップの既定のプロパティ (存在する場合) は、 **値/プロパティ** 見出しの下に一覧表示されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-172">The default properties (if any) to which the columns map are listed under the **Value/Property** heading.</span></span>
6.  <span data-ttu-id="c43fb-173">マップする列を選択し、対応する **値/プロパティ** フィールドを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="c43fb-173">Select the column you want to map, and then right-click the corresponding **Value/Property** field.</span></span>
    <span data-ttu-id="c43fb-174">すべてのスカラー プロパティのドロップダウン リストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-174">A drop-down list of all the scalar properties is displayed.</span></span>
7.  <span data-ttu-id="c43fb-175">適切なプロパティを選択します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-175">Select the appropriate property.</span></span>

    ![マップの複合型](~/ef6/media/mapcomplextype.png)

8.  <span data-ttu-id="c43fb-177">各テーブル列ごとに、手順 6. と 7. を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-177">Repeat steps 6 and 7 for each table column.</span></span>

>[!NOTE]
> <span data-ttu-id="c43fb-178">列マッピングを削除するには、マップする列を選択し、[ **値/プロパティ** ] フィールドをクリックします。</span><span class="sxs-lookup"><span data-stu-id="c43fb-178">To delete a column mapping, select the column that you want to map, and then click the **Value/Property** field.</span></span> <span data-ttu-id="c43fb-179">次に、ドロップダウンリストから **[削除]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-179">Then, select **Delete** from the drop-down list.</span></span>

## <a name="map-a-function-import-to-a-complex-type"></a><span data-ttu-id="c43fb-180">関数インポートを複合型にマップする</span><span class="sxs-lookup"><span data-stu-id="c43fb-180">Map a Function Import to a Complex Type</span></span>

<span data-ttu-id="c43fb-181">関数インポートは、ストアド プロシージャに基づいています。</span><span class="sxs-lookup"><span data-stu-id="c43fb-181">Function imports are based on stored procedures.</span></span> <span data-ttu-id="c43fb-182">関数インポートを複合型にマップするには、対応するストアド プロシージャが返す列の数が複合型のプロパティの数と一致するようにし、ストレージ型がプロパティ型と互換性があるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c43fb-182">To map a function import to a complex type, the columns returned by the corresponding stored procedure must match the properties of the complex type in number and must have storage types that are compatible with the property types.</span></span>

-   <span data-ttu-id="c43fb-183">複合型にマップするインポートされた関数をダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="c43fb-183">Double-click on an imported function that you want to map to a complex type.</span></span>

    ![関数インポート](~/ef6/media/functionimports.png)

-   <span data-ttu-id="c43fb-185">新しい関数インポートの設定を、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-185">Fill in the settings for the new function import, as follows:</span></span>
    -   <span data-ttu-id="c43fb-186">[ **ストアドプロシージャ名** ] フィールドで、関数インポートを作成するストアドプロシージャを指定します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-186">Specify the stored procedure for which you are creating a function import in the **Stored Procedure Name** field.</span></span> <span data-ttu-id="c43fb-187">このフィールドは、ストレージ モデル内のすべてのストアド プロシージャが表示されるドロップダウン リストです。</span><span class="sxs-lookup"><span data-stu-id="c43fb-187">This field is a drop-down list that displays all the stored procedures in the storage model.</span></span>
    -   <span data-ttu-id="c43fb-188">関数インポートの名前を [ **関数インポート名** ] フィールドに指定します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-188">Specify the name of the function import in the **Function Import Name** field.</span></span>
    -   <span data-ttu-id="c43fb-189">戻り値の型として [ **複合** ] を選択し、ドロップダウンリストから適切な型を選択して、特定の複合戻り値の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-189">Select **Complex** as the return type and then specify the specific complex return type by choosing the appropriate type from the drop-down list.</span></span>

        ![関数インポートの編集](~/ef6/media/editfunctionimport.png)

-   <span data-ttu-id="c43fb-191"> **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="c43fb-191">Click **OK**.</span></span>
    <span data-ttu-id="c43fb-192">関数インポート エントリが概念モデルに作成されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-192">The function import entry is created in the conceptual model.</span></span>

### <a name="customize-column-mapping-for-function-import"></a><span data-ttu-id="c43fb-193">関数インポートの列マッピングのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="c43fb-193">Customize Column Mapping for Function Import</span></span>

-   <span data-ttu-id="c43fb-194">モデルブラウザーで関数インポートを右クリックし、[ **関数インポートマッピング**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-194">Right-click the function import in the Model Browser and select **Function Import Mapping**.</span></span>
    <span data-ttu-id="c43fb-195">[ **マッピングの詳細** ] ウィンドウが表示され、関数インポートの既定のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-195">The **Mapping Details** window appears and shows the default mapping for the function import.</span></span> <span data-ttu-id="c43fb-196">矢印は、列の値とプロパティの値の間のマッピングを示します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-196">Arrows indicate the mappings between column values and property values.</span></span> <span data-ttu-id="c43fb-197">既定では、列名は、複合型のプロパティ名と同じであるものとされます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-197">By default, the column names are assumed to be the same as the complex type's property names.</span></span> <span data-ttu-id="c43fb-198">既定の列名は、グレーのテキストで表示されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-198">The default column names appear in gray text.</span></span>
-   <span data-ttu-id="c43fb-199">必要に応じて、関数インポートに対応するストアド プロシージャにより返される列名と一致するように、列名を変更します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-199">If necessary, change the column names to match the column names that are returned by the stored procedure that corresponds to the function import.</span></span>

## <a name="delete-a-complex-type"></a><span data-ttu-id="c43fb-200">複合型の削除</span><span class="sxs-lookup"><span data-stu-id="c43fb-200">Delete a Complex Type</span></span>

<span data-ttu-id="c43fb-201">複合型を削除すると、概念モデルからその型が削除され、その型のすべてのインスタンスのマッピングが削除されます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-201">When you delete a complex type, the type is deleted from the conceptual model and mappings for all instances of the type are deleted.</span></span> <span data-ttu-id="c43fb-202">ただし、その型への参照は更新されません。</span><span class="sxs-lookup"><span data-stu-id="c43fb-202">However, references to the type are not updated.</span></span> <span data-ttu-id="c43fb-203">たとえば、エンティティに ComplexType1 型の複合型プロパティがあり、ComplexType1 が**モデルブラウザー**で削除されている場合、対応するエンティティプロパティは更新されません。</span><span class="sxs-lookup"><span data-stu-id="c43fb-203">For example, if an entity has a complex type property of type ComplexType1 and ComplexType1 is deleted in the **Model Browser**, the corresponding entity property is not updated.</span></span> <span data-ttu-id="c43fb-204">削除された複合型を参照するエンティティが含まれているため、モデルは検証されません。</span><span class="sxs-lookup"><span data-stu-id="c43fb-204">The model will not validate  because it contains an entity that references a deleted complex type.</span></span> <span data-ttu-id="c43fb-205">エンティティ デザイナーを使用すると、削除された複合型への参照を更新または削除できます。</span><span class="sxs-lookup"><span data-stu-id="c43fb-205">You can update or delete references to deleted complex types by using the Entity Designer.</span></span>

-   <span data-ttu-id="c43fb-206">モデルブラウザーで複合型を右クリックし、[ **削除**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-206">Right-click a complex type in the Model Browser and select **Delete**.</span></span>

- <span data-ttu-id="c43fb-207">または -</span><span class="sxs-lookup"><span data-stu-id="c43fb-207">OR -</span></span>

-   <span data-ttu-id="c43fb-208">モデル ブラウザーで複合型を選択し、キーボードの Del キーを押します。</span><span class="sxs-lookup"><span data-stu-id="c43fb-208">Select a complex type in the Model Browser and press the Delete key on your keyboard.</span></span>

## <a name="query-for-entities-containing-properties-of-complex-type"></a><span data-ttu-id="c43fb-209">複合型のプロパティを含むエンティティのクエリ</span><span class="sxs-lookup"><span data-stu-id="c43fb-209">Query for Entities Containing Properties of Complex Type</span></span>

<span data-ttu-id="c43fb-210">次のコードは、複合型プロパティを含むエンティティ型オブジェクトのコレクションを返すクエリを実行する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="c43fb-210">The following code shows how to execute a query that returns a collection of entity type objects that contain a complex type property.</span></span>

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        var courses =
            from c in context.OnsiteCourses
            order by c.Details.Time
            select c;

        foreach (var c in courses)
        {
            Console.WriteLine("Time: " + c.Details.Time);
            Console.WriteLine("Days: " + c.Details.Days);
            Console.WriteLine("Location: " + c.Details.Location);
        }
    }
```
