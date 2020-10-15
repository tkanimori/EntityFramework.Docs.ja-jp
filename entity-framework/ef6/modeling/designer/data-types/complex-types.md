---
title: 複合型-EF デザイナー-EF6
description: 複合型-Entity Framework 6 の EF デザイナー
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/complex-types
ms.openlocfilehash: abacbdd375c2871e6c4205e0084764029b149a40
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065044"
---
# <a name="complex-types---ef-designer"></a><span data-ttu-id="525d7-103">複合型-EF デザイナー</span><span class="sxs-lookup"><span data-stu-id="525d7-103">Complex Types - EF Designer</span></span>
<span data-ttu-id="525d7-104">このトピックでは、複合型を Entity Framework Designer (EF デザイナー) にマップする方法と、複合型のプロパティを含むエンティティに対してクエリを実行する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="525d7-104">This topic shows how to map complex types with the Entity Framework Designer (EF Designer) and how to query for entities that contain properties of complex type.</span></span>

<span data-ttu-id="525d7-105">次の図は、EF デザイナーを操作するときに使用されるメインウィンドウを示しています。</span><span class="sxs-lookup"><span data-stu-id="525d7-105">The following image shows the main windows that are used when working with the EF Designer.</span></span>

![EF デザイナー](~/ef6/media/efdesigner.png)

> [!NOTE]
> <span data-ttu-id="525d7-107">概念モデルをビルドすると、マップされていないエンティティとアソシエーションについての警告が [エラー一覧] に表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="525d7-107">When you build the conceptual model, warnings about unmapped entities and associations may appear in the Error List.</span></span> <span data-ttu-id="525d7-108">モデルからデータベースを生成することを選択すると、エラーが解消されるため、これらの警告は無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="525d7-108">You can ignore these warnings because after you choose to generate the database from the model, the errors will go away.</span></span>

## <a name="what-is-a-complex-type"></a><span data-ttu-id="525d7-109">複合型とは</span><span class="sxs-lookup"><span data-stu-id="525d7-109">What is a Complex Type</span></span>

<span data-ttu-id="525d7-110">複合型はエンティティ型の非スカラー プロパティで、これによってスカラー プロパティをエンティティ内で整理できます。</span><span class="sxs-lookup"><span data-stu-id="525d7-110">Complex types are non-scalar properties of entity types that enable scalar properties to be organized within entities.</span></span> <span data-ttu-id="525d7-111">エンティティと同様に、複合型はスカラー プロパティまたは他の複合型プロパティで構成されています。</span><span class="sxs-lookup"><span data-stu-id="525d7-111">Like entities, complex types consist of scalar properties or other complex type properties.</span></span>

<span data-ttu-id="525d7-112">複合型を表すオブジェクトを操作する場合は、次の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="525d7-112">When you work with objects that represent complex types, be aware of the following:</span></span>

-   <span data-ttu-id="525d7-113">複合型にはキーがないため、独立して存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="525d7-113">Complex types do not have keys and therefore cannot exist independently.</span></span> <span data-ttu-id="525d7-114">複合型は、エンティティ型またはその他の複合型のプロパティとしてのみ存在できます。</span><span class="sxs-lookup"><span data-stu-id="525d7-114">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="525d7-115">複合型はアソシエーションに参加できず、ナビゲーションプロパティを含むことはできません。</span><span class="sxs-lookup"><span data-stu-id="525d7-115">Complex types cannot participate in associations and cannot contain navigation properties.</span></span>
-   <span data-ttu-id="525d7-116">複合型プロパティを **null**にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="525d7-116">Complex type properties cannot be **null**.</span></span> <span data-ttu-id="525d7-117">**Dbcontext**が呼び出され、 **InvalidOperationException **   null の複合オブジェクトが検出されると、InvalidOperationException が発生します。</span><span class="sxs-lookup"><span data-stu-id="525d7-117">An **InvalidOperationException **occurs when **DbContext.SaveChanges** is called and a null complex object is encountered.</span></span> <span data-ttu-id="525d7-118">複合オブジェクトのスカラープロパティは **null**にすることができます。</span><span class="sxs-lookup"><span data-stu-id="525d7-118">Scalar properties of complex objects can be **null**.</span></span>
-   <span data-ttu-id="525d7-119">複合型は他の複合型を継承できません。</span><span class="sxs-lookup"><span data-stu-id="525d7-119">Complex types cannot inherit from other complex types.</span></span>
-   <span data-ttu-id="525d7-120">複合型は **クラス**として定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="525d7-120">You must define the complex type as a **class**.</span></span> 
-   <span data-ttu-id="525d7-121">EF の **変更** が呼び出されると、複合型オブジェクトのメンバーに対する変更が検出されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-121">EF detects changes to members on a complex type object when **DbContext.DetectChanges** is called.</span></span> <span data-ttu-id="525d7-122">Entity Framework は、次のメンバーが呼び出されたときに、検出された **変更** を自動的に呼び出します: **Dbset. Find**, **dbset. Local**, dbset. **Remove**, dbset **.** **Attach** **,** Dbset. ' dbset. **getvalidationerrors**, **dbset. Entry**, **dbchangetracker.** エントリ。</span><span class="sxs-lookup"><span data-stu-id="525d7-122">Entity Framework calls **DetectChanges** automatically when the following members are called: **DbSet.Find**, **DbSet.Local**, **DbSet.Remove**, **DbSet.Add**, **DbSet.Attach**, **DbContext.SaveChanges**, **DbContext.GetValidationErrors**, **DbContext.Entry**, **DbChangeTracker.Entries**.</span></span>

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a><span data-ttu-id="525d7-123">エンティティのプロパティを新しい複合型にリファクタリングする</span><span class="sxs-lookup"><span data-stu-id="525d7-123">Refactor an Entity’s Properties into New Complex Type</span></span>

<span data-ttu-id="525d7-124">概念モデルにエンティティが既に存在する場合は、一部のプロパティを複合型プロパティにリファクタリングすることができます。</span><span class="sxs-lookup"><span data-stu-id="525d7-124">If you already have an entity in your conceptual model you may want to refactor some of the properties into a complex type property.</span></span>

<span data-ttu-id="525d7-125">デザイナー画面で、エンティティの1つ以上のプロパティ (ナビゲーションプロパティを除く) を選択し、右クリックして [ **リファクター- &gt; 新しい複合型に移動**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="525d7-125">On the designer surface, select one or more properties (excluding navigation properties) of an entity, then right-click and select **Refactor -&gt; Move to New Complex Type**.</span></span>

![新しい複合型へのリファクター](~/ef6/media/refactor.png)

<span data-ttu-id="525d7-127">選択したプロパティを持つ新しい複合型が **モデルブラウザー**に追加されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-127">A new complex type with the selected properties is added to the **Model Browser**.</span></span> <span data-ttu-id="525d7-128">複合型には既定の名前が付きます。</span><span class="sxs-lookup"><span data-stu-id="525d7-128">The complex type is given a default name.</span></span>

<span data-ttu-id="525d7-129">新しく作成した型の複合プロパティが、選択したプロパティと置き換わります。</span><span class="sxs-lookup"><span data-stu-id="525d7-129">A complex property of the newly created type replaces the selected properties.</span></span> <span data-ttu-id="525d7-130">プロパティのマッピングはすべて保持されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-130">All property mappings are preserved.</span></span>

![新しい複合型へのリファクター2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a><span data-ttu-id="525d7-132">新しい複合型を作成する</span><span class="sxs-lookup"><span data-stu-id="525d7-132">Create a New Complex Type</span></span>

<span data-ttu-id="525d7-133">また、既存のエンティティのプロパティを含まない新しい複合型を作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="525d7-133">You can also create a new complex type that does not contain properties of an existing entity.</span></span>

<span data-ttu-id="525d7-134">モデルブラウザーで [ **複合型** ] フォルダーを右クリックし、[ **AddNew 複合型...**] をポイントします。</span><span class="sxs-lookup"><span data-stu-id="525d7-134">Right-click the **Complex Types** folder in the Model Browser, point to **AddNew Complex Type…**.</span></span> <span data-ttu-id="525d7-135">または、[**複合型**] フォルダーを選択し、 **Insert**   キーボードの ins キーを押します。</span><span class="sxs-lookup"><span data-stu-id="525d7-135">Alternatively, you can select the **Complex Types** folder and press the **Insert** key on your keyboard.</span></span>

![新しい複合型の追加](~/ef6/media/addnewcomplextype.png)

<span data-ttu-id="525d7-137">既定の名前を持つ新しい複合型がフォルダーに追加されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-137">A new complex type is added to the folder with a default name.</span></span> <span data-ttu-id="525d7-138">これで、型にプロパティを追加できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="525d7-138">You can now add properties to the type.</span></span>

## <a name="add-properties-to-a-complex-type"></a><span data-ttu-id="525d7-139">複合型へのプロパティの追加</span><span class="sxs-lookup"><span data-stu-id="525d7-139">Add Properties to a Complex Type</span></span>

<span data-ttu-id="525d7-140">複合型のプロパティはスカラー型か既存の複合型にすることができます。</span><span class="sxs-lookup"><span data-stu-id="525d7-140">Properties of a complex type can be scalar types or existing complex types.</span></span> <span data-ttu-id="525d7-141">ただし、複合型のプロパティに循環参照を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="525d7-141">However, complex type properties cannot have circular references.</span></span> <span data-ttu-id="525d7-142">たとえば、複合型 **OnsiteCourseDetails**は、   複合型 **OnsiteCourseDetails**のプロパティを持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="525d7-142">For example, a complex type **OnsiteCourseDetails** cannot have a property of complex type **OnsiteCourseDetails**.</span></span>

<span data-ttu-id="525d7-143">以下のいずれかの方法で、複合型にプロパティを追加できます。</span><span class="sxs-lookup"><span data-stu-id="525d7-143">You can add a property to a complex type in any of the ways listed below.</span></span>

-   <span data-ttu-id="525d7-144">モデルブラウザーで複合型を右クリックし、[ **追加**] をポイントします。次に、[ **スカラープロパティ**   ] または [ **複合プロパティ**] をポイントし、目的のプロパティの種類を選択します。</span><span class="sxs-lookup"><span data-stu-id="525d7-144">Right-click a complex type in the Model Browser, point to **Add**, then point to **Scalar Property** or **Complex Property**, then select the desired property type.</span></span> <span data-ttu-id="525d7-145">または、複合型を選択し、 **Insert**   キーボードの ins キーを押します。</span><span class="sxs-lookup"><span data-stu-id="525d7-145">Alternatively, you can select a complex type and then press the **Insert** key on your keyboard.</span></span>  

    ![複合型へのプロパティの追加](~/ef6/media/addpropertiestocomplextype.png)

    <span data-ttu-id="525d7-147">既定の名前を持つ新しいプロパティが複合型に追加されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-147">A new property is added to the complex type with a default name.</span></span>

- <span data-ttu-id="525d7-148">または -</span><span class="sxs-lookup"><span data-stu-id="525d7-148">OR -</span></span>

-   <span data-ttu-id="525d7-149">**EF デザイナー**画面でエンティティプロパティを右クリックし、[ **コピー**] をクリックします。次に、**モデルブラウザー**で複合型を右クリックし、[ **貼り付け**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="525d7-149">Right-click an entity property on the **EF  Designer** surface and select **Copy**, then right-click the complex type in the **Model Browser** and select **Paste**.</span></span>

## <a name="rename-a-complex-type"></a><span data-ttu-id="525d7-150">複合型の名前を変更する</span><span class="sxs-lookup"><span data-stu-id="525d7-150">Rename a Complex Type</span></span>

<span data-ttu-id="525d7-151">複合型の名前を変更すると、その型へのすべての参照がプロジェクト全体で更新されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-151">When you rename a complex type, all references to the type are updated throughout the project.</span></span>

-   <span data-ttu-id="525d7-152">**モデルブラウザー**で複合型をゆっくりとダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="525d7-152">Slowly double-click a complex type in the **Model Browser**.</span></span>
    <span data-ttu-id="525d7-153">名前が選択され、編集モードになります。</span><span class="sxs-lookup"><span data-stu-id="525d7-153">The name will be selected and in edit mode.</span></span>

- <span data-ttu-id="525d7-154">または -</span><span class="sxs-lookup"><span data-stu-id="525d7-154">OR -</span></span>

-   <span data-ttu-id="525d7-155">**モデルブラウザー**で複合型を右クリックし、[ **名前の変更**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="525d7-155">Right-click a complex type in the **Model Browser** and select **Rename**.</span></span>

- <span data-ttu-id="525d7-156">または -</span><span class="sxs-lookup"><span data-stu-id="525d7-156">OR -</span></span>

-   <span data-ttu-id="525d7-157">モデル ブラウザーで複合型を選択し、F2 キーを押します。</span><span class="sxs-lookup"><span data-stu-id="525d7-157">Select a complex type in the Model Browser and press the F2 key.</span></span>

- <span data-ttu-id="525d7-158">または -</span><span class="sxs-lookup"><span data-stu-id="525d7-158">OR -</span></span>

-   <span data-ttu-id="525d7-159">**モデルブラウザー**で複合型を右クリックし、[ **プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="525d7-159">Right-click a complex type in the **Model Browser** and select **Properties**.</span></span> <span data-ttu-id="525d7-160">[ **プロパティ**] ウィンドウで名前を編集し   ます。</span><span class="sxs-lookup"><span data-stu-id="525d7-160">Edit the name in the **Properties** window.</span></span>

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a><span data-ttu-id="525d7-161">既存の複合型をエンティティに追加し、そのプロパティをテーブル列にマップします。</span><span class="sxs-lookup"><span data-stu-id="525d7-161">Add an Existing Complex Type to an Entity and Map its Properties to Table Columns</span></span>

1.  <span data-ttu-id="525d7-162">エンティティを右クリックして [ **新規追加**] をポイントし、[ **複合プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="525d7-162">Right-click an entity, point to **Add New**, and select **Complex Property**.</span></span>
    <span data-ttu-id="525d7-163">既定の名前を持つ複合型プロパティがエンティティに追加されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-163">A complex type property with a default name is added to the entity.</span></span> <span data-ttu-id="525d7-164">既存の複合型から選択した既定の型がプロパティに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="525d7-164">A default type (chosen from the existing complex types) is assigned to the property.</span></span>
2.  <span data-ttu-id="525d7-165">[ **プロパティ**] ウィンドウで、目的の型をプロパティに割り当て   ます。</span><span class="sxs-lookup"><span data-stu-id="525d7-165">Assign the desired type to the property in the **Properties** window.</span></span>
    <span data-ttu-id="525d7-166">複合型プロパティをエンティティに追加した後、そのプロパティをテーブル列にマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="525d7-166">After adding a complex type property to an entity, you must map its properties to table columns.</span></span>
3.  <span data-ttu-id="525d7-167">デザイン画面または **モデルブラウザー**でエンティティ型を右クリック   し、[ **テーブルマッピング**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="525d7-167">Right-click an entity type on the design surface or in the **Model Browser** and select **Table Mappings**.</span></span>
    <span data-ttu-id="525d7-168">[ **マッピングの詳細**] ウィンドウにテーブルマッピングが表示され   ます。</span><span class="sxs-lookup"><span data-stu-id="525d7-168">The table mappings are displayed in the **Mapping Details** window.</span></span>
4.  <span data-ttu-id="525d7-169">[ \*\*マップ先 &lt; テーブル名 &gt; \*\*] ノードを展開し   ます。</span><span class="sxs-lookup"><span data-stu-id="525d7-169">Expand the **Maps to &lt;Table Name&gt;** node.</span></span>
    <span data-ttu-id="525d7-170"> **列マッピング**   ノードが表示されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-170">A **Column Mappings** node appears.</span></span>
5.  <span data-ttu-id="525d7-171">[ **列マッピング**] ノードを展開し   ます。</span><span class="sxs-lookup"><span data-stu-id="525d7-171">Expand the **Column Mappings** node.</span></span>
    <span data-ttu-id="525d7-172">テーブル内のすべての列の一覧が表示されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-172">A list of all the columns in the table appears.</span></span> <span data-ttu-id="525d7-173">列マップの既定のプロパティ (存在する場合) は、[ **値/プロパティ**] 見出しの下に一覧表示され   ます。</span><span class="sxs-lookup"><span data-stu-id="525d7-173">The default properties (if any) to which the columns map are listed under the **Value/Property** heading.</span></span>
6.  <span data-ttu-id="525d7-174">マップする列を選択し、対応する [ **値/プロパティ**] フィールドを右クリックし   ます。</span><span class="sxs-lookup"><span data-stu-id="525d7-174">Select the column you want to map, and then right-click the corresponding **Value/Property** field.</span></span>
    <span data-ttu-id="525d7-175">すべてのスカラー プロパティのドロップダウン リストが表示されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-175">A drop-down list of all the scalar properties is displayed.</span></span>
7.  <span data-ttu-id="525d7-176">適切なプロパティを選択します。</span><span class="sxs-lookup"><span data-stu-id="525d7-176">Select the appropriate property.</span></span>

    ![マップの複合型](~/ef6/media/mapcomplextype.png)

8.  <span data-ttu-id="525d7-178">各テーブル列ごとに、手順 6. と 7. を繰り返します。</span><span class="sxs-lookup"><span data-stu-id="525d7-178">Repeat steps 6 and 7 for each table column.</span></span>

>[!NOTE]
> <span data-ttu-id="525d7-179">列マッピングを削除するには、マップする列を選択し、[ **値/プロパティ**   ] フィールドをクリックします。</span><span class="sxs-lookup"><span data-stu-id="525d7-179">To delete a column mapping, select the column that you want to map, and then click the **Value/Property** field.</span></span> <span data-ttu-id="525d7-180">次に、ドロップダウンリストから [ **削除** ] を選択します。</span><span class="sxs-lookup"><span data-stu-id="525d7-180">Then, select **Delete** from the drop-down list.</span></span>

## <a name="map-a-function-import-to-a-complex-type"></a><span data-ttu-id="525d7-181">関数インポートを複合型にマップする</span><span class="sxs-lookup"><span data-stu-id="525d7-181">Map a Function Import to a Complex Type</span></span>

<span data-ttu-id="525d7-182">関数インポートは、ストアド プロシージャに基づいています。</span><span class="sxs-lookup"><span data-stu-id="525d7-182">Function imports are based on stored procedures.</span></span> <span data-ttu-id="525d7-183">関数インポートを複合型にマップするには、対応するストアド プロシージャが返す列の数が複合型のプロパティの数と一致するようにし、ストレージ型がプロパティ型と互換性があるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="525d7-183">To map a function import to a complex type, the columns returned by the corresponding stored procedure must match the properties of the complex type in number and must have storage types that are compatible with the property types.</span></span>

-   <span data-ttu-id="525d7-184">複合型にマップするインポートされた関数をダブルクリックします。</span><span class="sxs-lookup"><span data-stu-id="525d7-184">Double-click on an imported function that you want to map to a complex type.</span></span>

    ![関数インポート](~/ef6/media/functionimports.png)

-   <span data-ttu-id="525d7-186">新しい関数インポートの設定を、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="525d7-186">Fill in the settings for the new function import, as follows:</span></span>
    -   <span data-ttu-id="525d7-187">[ **ストアドプロシージャ名**] フィールドで、関数インポートを作成するストアドプロシージャを指定し   ます。</span><span class="sxs-lookup"><span data-stu-id="525d7-187">Specify the stored procedure for which you are creating a function import in the **Stored Procedure Name** field.</span></span> <span data-ttu-id="525d7-188">このフィールドは、ストレージ モデル内のすべてのストアド プロシージャが表示されるドロップダウン リストです。</span><span class="sxs-lookup"><span data-stu-id="525d7-188">This field is a drop-down list that displays all the stored procedures in the storage model.</span></span>
    -   <span data-ttu-id="525d7-189">関数インポートの名前を [ **関数インポート名**   ] フィールドに指定します。</span><span class="sxs-lookup"><span data-stu-id="525d7-189">Specify the name of the function import in the **Function Import Name** field.</span></span>
    -   <span data-ttu-id="525d7-190"> **Complex**   戻り値の型として [複合] を選択し、ドロップダウンリストから適切な型を選択して、特定の複合戻り値の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="525d7-190">Select **Complex** as the return type and then specify the specific complex return type by choosing the appropriate type from the drop-down list.</span></span>

        ![関数インポートの編集](~/ef6/media/editfunctionimport.png)

-   <span data-ttu-id="525d7-192"> **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="525d7-192">Click **OK**.</span></span>
    <span data-ttu-id="525d7-193">関数インポート エントリが概念モデルに作成されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-193">The function import entry is created in the conceptual model.</span></span>

### <a name="customize-column-mapping-for-function-import"></a><span data-ttu-id="525d7-194">関数インポートの列マッピングのカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="525d7-194">Customize Column Mapping for Function Import</span></span>

-   <span data-ttu-id="525d7-195">モデルブラウザーで関数インポートを右クリックし、[ **関数インポートマッピング**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="525d7-195">Right-click the function import in the Model Browser and select **Function Import Mapping**.</span></span>
    <span data-ttu-id="525d7-196">[ **マッピングの詳細**   ] ウィンドウが表示され、関数インポートの既定のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-196">The **Mapping Details** window appears and shows the default mapping for the function import.</span></span> <span data-ttu-id="525d7-197">矢印は、列の値とプロパティの値の間のマッピングを示します。</span><span class="sxs-lookup"><span data-stu-id="525d7-197">Arrows indicate the mappings between column values and property values.</span></span> <span data-ttu-id="525d7-198">既定では、列名は、複合型のプロパティ名と同じであるものとされます。</span><span class="sxs-lookup"><span data-stu-id="525d7-198">By default, the column names are assumed to be the same as the complex type's property names.</span></span> <span data-ttu-id="525d7-199">既定の列名は、グレーのテキストで表示されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-199">The default column names appear in gray text.</span></span>
-   <span data-ttu-id="525d7-200">必要に応じて、関数インポートに対応するストアド プロシージャにより返される列名と一致するように、列名を変更します。</span><span class="sxs-lookup"><span data-stu-id="525d7-200">If necessary, change the column names to match the column names that are returned by the stored procedure that corresponds to the function import.</span></span>

## <a name="delete-a-complex-type"></a><span data-ttu-id="525d7-201">複合型の削除</span><span class="sxs-lookup"><span data-stu-id="525d7-201">Delete a Complex Type</span></span>

<span data-ttu-id="525d7-202">複合型を削除すると、概念モデルからその型が削除され、その型のすべてのインスタンスのマッピングが削除されます。</span><span class="sxs-lookup"><span data-stu-id="525d7-202">When you delete a complex type, the type is deleted from the conceptual model and mappings for all instances of the type are deleted.</span></span> <span data-ttu-id="525d7-203">ただし、その型への参照は更新されません。</span><span class="sxs-lookup"><span data-stu-id="525d7-203">However, references to the type are not updated.</span></span> <span data-ttu-id="525d7-204">たとえば、エンティティに ComplexType1 型の複合型プロパティがあり、ComplexType1 が **モデルブラウザー**で削除されている場合、対応するエンティティプロパティは更新されません。</span><span class="sxs-lookup"><span data-stu-id="525d7-204">For example, if an entity has a complex type property of type ComplexType1 and ComplexType1 is deleted in the **Model Browser**, the corresponding entity property is not updated.</span></span> <span data-ttu-id="525d7-205">削除された複合型を参照するエンティティが含まれているため、モデルは検証されません。</span><span class="sxs-lookup"><span data-stu-id="525d7-205">The model will not validate  because it contains an entity that references a deleted complex type.</span></span> <span data-ttu-id="525d7-206">エンティティ デザイナーを使用すると、削除された複合型への参照を更新または削除できます。</span><span class="sxs-lookup"><span data-stu-id="525d7-206">You can update or delete references to deleted complex types by using the Entity Designer.</span></span>

-   <span data-ttu-id="525d7-207">モデルブラウザーで複合型を右クリックし、[ **削除**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="525d7-207">Right-click a complex type in the Model Browser and select **Delete**.</span></span>

- <span data-ttu-id="525d7-208">または -</span><span class="sxs-lookup"><span data-stu-id="525d7-208">OR -</span></span>

-   <span data-ttu-id="525d7-209">モデル ブラウザーで複合型を選択し、キーボードの Del キーを押します。</span><span class="sxs-lookup"><span data-stu-id="525d7-209">Select a complex type in the Model Browser and press the Delete key on your keyboard.</span></span>

## <a name="query-for-entities-containing-properties-of-complex-type"></a><span data-ttu-id="525d7-210">複合型のプロパティを含むエンティティのクエリ</span><span class="sxs-lookup"><span data-stu-id="525d7-210">Query for Entities Containing Properties of Complex Type</span></span>

<span data-ttu-id="525d7-211">次のコードは、複合型プロパティを含むエンティティ型オブジェクトのコレクションを返すクエリを実行する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="525d7-211">The following code shows how to execute a query that returns a collection of entity type objects that contain a complex type property.</span></span>

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
