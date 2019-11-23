---
title: MSL 仕様-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 13ae7bc1-74b4-4ee4-8d73-c337be841467
ms.openlocfilehash: 8990d1373ea2121ce11337a43dbcdf3b9e1532bd
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182556"
---
# <a name="msl-specification"></a><span data-ttu-id="15101-102">MSL 仕様</span><span class="sxs-lookup"><span data-stu-id="15101-102">MSL Specification</span></span>
<span data-ttu-id="15101-103">マッピング仕様言語 (MSL) は、Entity Framework アプリケーションの概念モデルとストレージモデルの間のマッピングを記述する XML ベースの言語です。</span><span class="sxs-lookup"><span data-stu-id="15101-103">Mapping specification language (MSL) is an XML-based language that describes the mapping between the conceptual model and storage model of an Entity Framework application.</span></span>

<span data-ttu-id="15101-104">Entity Framework アプリケーションでは、マッピングメタデータはビルド時に (MSL で記述された) .msl ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="15101-104">In an Entity Framework application, mapping metadata is loaded from an .msl file (written in MSL) at build time.</span></span> <span data-ttu-id="15101-105">Entity Framework は、実行時にマッピングメタデータを使用して、概念モデルに対するクエリをストア固有のコマンドに変換します。</span><span class="sxs-lookup"><span data-stu-id="15101-105">Entity Framework uses mapping metadata at runtime to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="15101-106">Entity Framework Designer (EF デザイナー) は、デザイン時に、.edmx ファイルにマッピング情報を格納します。</span><span class="sxs-lookup"><span data-stu-id="15101-106">The Entity Framework Designer (EF Designer) stores mapping information in an .edmx file at design time.</span></span> <span data-ttu-id="15101-107">ビルド時に、Entity Designer は .edmx ファイルの情報を使用して、実行時に Entity Framework が必要とする .msl ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="15101-107">At build time, the Entity Designer uses information in an .edmx file to create the .msl file that is needed by Entity Framework at runtime</span></span>

<span data-ttu-id="15101-108">MSL で参照されるすべての概念モデル型またはストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-108">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="15101-109">概念モデルの名前空間の名前については、「 [CSDL の仕様](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-109">For information about the conceptual model namespace name, see [CSDL Specification](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md).</span></span> <span data-ttu-id="15101-110">ストレージモデルの名前空間の名前については、「 [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-110">For information about the storage model namespace name, see [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md).</span></span>

<span data-ttu-id="15101-111">MSL のバージョンは、XML 名前空間で区別されます。</span><span class="sxs-lookup"><span data-stu-id="15101-111">Versions of MSL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="15101-112">MSL バージョン</span><span class="sxs-lookup"><span data-stu-id="15101-112">MSL Version</span></span> | <span data-ttu-id="15101-113">XML 名前空間</span><span class="sxs-lookup"><span data-stu-id="15101-113">XML Namespace</span></span>                                        |
|:------------|:-----------------------------------------------------|
| <span data-ttu-id="15101-114">MSL v1</span><span class="sxs-lookup"><span data-stu-id="15101-114">MSL v1</span></span>      | <span data-ttu-id="15101-115">urn: スキーマ-microsoft-com: windows: storage: mapping: CS</span><span class="sxs-lookup"><span data-stu-id="15101-115">urn:schemas-microsoft-com:windows:storage:mapping:CS</span></span> |
| <span data-ttu-id="15101-116">MSL v2</span><span class="sxs-lookup"><span data-stu-id="15101-116">MSL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| <span data-ttu-id="15101-117">MSL v3</span><span class="sxs-lookup"><span data-stu-id="15101-117">MSL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a><span data-ttu-id="15101-118">Alias 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-118">Alias Element (MSL)</span></span>

<span data-ttu-id="15101-119">マッピングスキーマ言語 (MSL) の**Alias**要素は、概念モデルおよびストレージモデルの名前空間のエイリアスを定義するために使用される mapping 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="15101-119">The **Alias** element in mapping specification language (MSL) is a child of the Mapping element that is used to define aliases for conceptual model and storage model namespaces.</span></span> <span data-ttu-id="15101-120">MSL で参照されるすべての概念モデル型またはストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-120">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="15101-121">概念モデルの名前空間の名前については、「Schema 要素 (CSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-121">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="15101-122">ストレージモデルの名前空間の名前については、「Schema 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-122">For information about the storage model namespace name, see Schema Element (SSDL).</span></span>

<span data-ttu-id="15101-123">**Alias**要素に子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="15101-123">The **Alias** element cannot have child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-124">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-124">Applicable Attributes</span></span>

<span data-ttu-id="15101-125">次の表では、 **Alias**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-125">The table below describes the attributes that can be applied to the **Alias** element.</span></span>

| <span data-ttu-id="15101-126">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-126">Attribute Name</span></span> | <span data-ttu-id="15101-127">必須</span><span class="sxs-lookup"><span data-stu-id="15101-127">Is Required</span></span> | <span data-ttu-id="15101-128">値</span><span class="sxs-lookup"><span data-stu-id="15101-128">Value</span></span>                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| <span data-ttu-id="15101-129">**[キー]**</span><span class="sxs-lookup"><span data-stu-id="15101-129">**Key**</span></span>        | <span data-ttu-id="15101-130">はい</span><span class="sxs-lookup"><span data-stu-id="15101-130">Yes</span></span>         | <span data-ttu-id="15101-131">**Value**属性によって指定された名前空間のエイリアス。</span><span class="sxs-lookup"><span data-stu-id="15101-131">The alias for the namespace that is specified by the **Value** attribute.</span></span> |
| <span data-ttu-id="15101-132">**[値]**</span><span class="sxs-lookup"><span data-stu-id="15101-132">**Value**</span></span>      | <span data-ttu-id="15101-133">はい</span><span class="sxs-lookup"><span data-stu-id="15101-133">Yes</span></span>         | <span data-ttu-id="15101-134">**キー**要素の値がエイリアスである名前空間。</span><span class="sxs-lookup"><span data-stu-id="15101-134">The namespace for which the value of the **Key** element is an alias.</span></span>     |

### <a name="example"></a><span data-ttu-id="15101-135">例</span><span class="sxs-lookup"><span data-stu-id="15101-135">Example</span></span>

<span data-ttu-id="15101-136">次の例は、概念モデルで定義されている型に対して、`c`エイリアスを定義する**alias**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-136">The following example shows an **Alias** element that defines an alias, `c`, for types that are defined in the conceptual model.</span></span>

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="associationend-element-msl"></a><span data-ttu-id="15101-137">AssociationEnd 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-137">AssociationEnd Element (MSL)</span></span>

<span data-ttu-id="15101-138">マッピングスキーマ言語 (MSL) の**Associationend**要素は、概念モデルのエンティティ型の変更関数が基になるデータベースのストアドプロシージャにマップされるときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="15101-138">The **AssociationEnd** element in mapping specification language (MSL) is used when the modification functions of an entity type in the conceptual model are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="15101-139">変更ストアドプロシージャが、その値が関連付けプロパティに保持されているパラメーターを受け取る場合、 **Associationend**要素はプロパティ値をパラメーターにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-139">If a modification stored procedure takes a parameter whose value is held in an association property, the **AssociationEnd** element maps the property value to the parameter.</span></span> <span data-ttu-id="15101-140">詳細については、下の例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-140">For more information, see the example below.</span></span>

<span data-ttu-id="15101-141">エンティティ型の変更関数をストアドプロシージャにマップする方法の詳細については、「ModificationFunctionMapping 要素 (MSL)」および「チュートリアル: ストアドプロシージャへのエンティティのマッピング」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-141">For more information about mapping modification functions of entity types to stored procedures, see ModificationFunctionMapping Element (MSL) and Walkthrough: Mapping an Entity to Stored Procedures.</span></span>

<span data-ttu-id="15101-142">**Associationend**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-142">The **AssociationEnd** element can have the following child elements:</span></span>

-   <span data-ttu-id="15101-143">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="15101-143">ScalarProperty</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-144">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-144">Applicable Attributes</span></span>

<span data-ttu-id="15101-145">次の表では、 **Associationend**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-145">The following table describes the attributes that are applicable to the **AssociationEnd** element.</span></span>

| <span data-ttu-id="15101-146">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-146">Attribute Name</span></span>     | <span data-ttu-id="15101-147">必須</span><span class="sxs-lookup"><span data-stu-id="15101-147">Is Required</span></span> | <span data-ttu-id="15101-148">値</span><span class="sxs-lookup"><span data-stu-id="15101-148">Value</span></span>                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-149">**AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="15101-149">**AssociationSet**</span></span> | <span data-ttu-id="15101-150">はい</span><span class="sxs-lookup"><span data-stu-id="15101-150">Yes</span></span>         | <span data-ttu-id="15101-151">マップされるアソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-151">The name of the association that is being mapped.</span></span>                                                                                                                                 |
| <span data-ttu-id="15101-152">**From**</span><span class="sxs-lookup"><span data-stu-id="15101-152">**From**</span></span>           | <span data-ttu-id="15101-153">はい</span><span class="sxs-lookup"><span data-stu-id="15101-153">Yes</span></span>         | <span data-ttu-id="15101-154">マップされるアソシエーションに対応するナビゲーションプロパティの**Fromrole**属性の値。</span><span class="sxs-lookup"><span data-stu-id="15101-154">The value of the **FromRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="15101-155">詳細については、「NavigationProperty 要素 (CSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-155">For more information, see NavigationProperty Element (CSDL).</span></span> |
| <span data-ttu-id="15101-156">**To**</span><span class="sxs-lookup"><span data-stu-id="15101-156">**To**</span></span>             | <span data-ttu-id="15101-157">はい</span><span class="sxs-lookup"><span data-stu-id="15101-157">Yes</span></span>         | <span data-ttu-id="15101-158">マップされるアソシエーションに対応するナビゲーションプロパティの**Torole**属性の値。</span><span class="sxs-lookup"><span data-stu-id="15101-158">The value of the **ToRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="15101-159">詳細については、「NavigationProperty 要素 (CSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-159">For more information, see NavigationProperty Element (CSDL).</span></span>   |

### <a name="example"></a><span data-ttu-id="15101-160">例</span><span class="sxs-lookup"><span data-stu-id="15101-160">Example</span></span>

<span data-ttu-id="15101-161">次の概念モデルのエンティティ型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="15101-161">Consider the following conceptual model entity type:</span></span>

``` xml
 <EntityType Name="Course">
   <Key>
     <PropertyRef Name="CourseID" />
   </Key>
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" MaxLength="100"
             FixedLength="false" Unicode="true" />
   <Property Type="Int32" Name="Credits" Nullable="false" />
   <NavigationProperty Name="Department"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Course" ToRole="Department" />
 </EntityType>
```

<span data-ttu-id="15101-162">さらに、次のストアド プロシージャを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="15101-162">Also consider the following stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[UpdateCourse]
                                @CourseID int,
                                @Title nvarchar(50),
                                @Credits int,
                                @DepartmentID int
                                AS
                                UPDATE Course SET Title=@Title,
                                                              Credits=@Credits,
                                                              DepartmentID=@DepartmentID
                                WHERE CourseID=@CourseID;
```

<span data-ttu-id="15101-163">`Course` エンティティの更新関数をこのストアドプロシージャにマップするには、 **DepartmentID**パラメーターに値を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-163">In order to map the update function of the `Course` entity to this stored procedure, you must supply a value to the **DepartmentID** parameter.</span></span> <span data-ttu-id="15101-164">`DepartmentID` の値はエンティティ型のプロパティには対応せず、独立した関連付け (アソシエーション) に含まれています。ここに、そのマッピングを示します。</span><span class="sxs-lookup"><span data-stu-id="15101-164">The value for `DepartmentID` does not correspond to a property on the entity type; it is contained in an independent association whose mapping is shown here:</span></span>

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
 </AssociationSetMapping>
```

<span data-ttu-id="15101-165">次のコードは、 **\_Department\_course**の**DepartmentID**プロパティを**updatecourse**ストアドプロシージャにマップするために使用される**associationend**要素を示しています (これは、 **course**エンティティ型の更新関数がマップされています)。</span><span class="sxs-lookup"><span data-stu-id="15101-165">The following code shows the **AssociationEnd** element used to map the **DepartmentID** property of the **FK\_Course\_Department** association to the **UpdateCourse** stored procedure (to which the update function of the **Course** entity type is mapped):</span></span>

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdateCourse">
         <AssociationEnd AssociationSet="FK_Course_Department"
                         From="Course" To="Department">
           <ScalarProperty Name="DepartmentID"
                           ParameterName="DepartmentID"
                           Version="Current" />
         </AssociationEnd>
         <ScalarProperty Name="Credits" ParameterName="Credits"
                         Version="Current" />
         <ScalarProperty Name="Title" ParameterName="Title"
                         Version="Current" />
         <ScalarProperty Name="CourseID" ParameterName="CourseID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="associationsetmapping-element-msl"></a><span data-ttu-id="15101-166">AssociationSetMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-166">AssociationSetMapping Element (MSL)</span></span>

<span data-ttu-id="15101-167">マッピング仕様言語 (MSL) の**AssociationSetMapping**要素は、概念モデルのアソシエーションと基になるデータベースのテーブル列の間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-167">The **AssociationSetMapping** element in mapping specification language (MSL) defines the mapping between an association in the conceptual model and table columns in the underlying database.</span></span>

<span data-ttu-id="15101-168">概念モデルのアソシエーションとは、プロパティが基になるデータベースの主キー列および外部キー列を表す型のことです。</span><span class="sxs-lookup"><span data-stu-id="15101-168">Associations in the conceptual model are types whose properties represent primary and foreign key columns in the underlying database.</span></span> <span data-ttu-id="15101-169">**AssociationSetMapping**要素は、2つの endproperty 要素を使用して、データベース内のアソシエーション型のプロパティと列の間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-169">The **AssociationSetMapping** element uses two EndProperty elements to define the mappings between association type properties and columns in the database.</span></span> <span data-ttu-id="15101-170">Condition 要素を使用して、これらのマッピングに条件を指定できます。</span><span class="sxs-lookup"><span data-stu-id="15101-170">You can place conditions on these mappings with the Condition element.</span></span> <span data-ttu-id="15101-171">ModificationFunctionMapping 要素を使用して、アソシエーションの挿入関数、更新関数、および削除関数をデータベース内のストアド プロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-171">Map the insert, update, and delete functions for associations to stored procedures in the database with the ModificationFunctionMapping element.</span></span> <span data-ttu-id="15101-172">QueryView 要素内の Entity SQL 文字列を使用して、アソシエーションとテーブル列の間の読み取り専用マッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-172">Define read-only mappings between associations and table columns by using an Entity SQL string in a QueryView element.</span></span>

> [!NOTE]
> <span data-ttu-id="15101-173">概念モデルのアソシエーションに対して参照制約が定義されている場合、アソシエーションを**AssociationSetMapping**要素にマップする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="15101-173">If a referential constraint is defined for an association in the conceptual model, the association does not need to be mapped with an **AssociationSetMapping** element.</span></span> <span data-ttu-id="15101-174">参照に関する制約を持つアソシエーションの**AssociationSetMapping**要素が存在する場合、 **AssociationSetMapping**要素で定義されているマッピングは無視されます。</span><span class="sxs-lookup"><span data-stu-id="15101-174">If an **AssociationSetMapping** element is present for an association that has a referential constraint, the mappings defined in the **AssociationSetMapping** element will be ignored.</span></span> <span data-ttu-id="15101-175">詳細については、「参照要素の参照」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-175">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="15101-176">**AssociationSetMapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-176">The **AssociationSetMapping** element can have the following child elements</span></span>

-   <span data-ttu-id="15101-177">QueryView (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="15101-177">QueryView (zero or one)</span></span>
-   <span data-ttu-id="15101-178">EndProperty (0 個または 2 個)</span><span class="sxs-lookup"><span data-stu-id="15101-178">EndProperty (zero or two)</span></span>
-   <span data-ttu-id="15101-179">条件 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-179">Condition (zero or more)</span></span>
-   <span data-ttu-id="15101-180">ModificationFunctionMapping (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="15101-180">ModificationFunctionMapping (zero or one)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-181">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-181">Applicable Attributes</span></span>

<span data-ttu-id="15101-182">次の表では、 **AssociationSetMapping**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-182">The following table describes the attributes that can be applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="15101-183">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-183">Attribute Name</span></span>     | <span data-ttu-id="15101-184">必須</span><span class="sxs-lookup"><span data-stu-id="15101-184">Is Required</span></span> | <span data-ttu-id="15101-185">値</span><span class="sxs-lookup"><span data-stu-id="15101-185">Value</span></span>                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-186">**名前**</span><span class="sxs-lookup"><span data-stu-id="15101-186">**Name**</span></span>           | <span data-ttu-id="15101-187">はい</span><span class="sxs-lookup"><span data-stu-id="15101-187">Yes</span></span>         | <span data-ttu-id="15101-188">マップされる概念モデルのアソシエーション セットの名前です。</span><span class="sxs-lookup"><span data-stu-id="15101-188">The name of the conceptual model association set that is being mapped.</span></span>                      |
| <span data-ttu-id="15101-189">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="15101-189">**TypeName**</span></span>       | <span data-ttu-id="15101-190">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-190">No</span></span>          | <span data-ttu-id="15101-191">マッピングされている概念モデルのアソシエーション型の名前空間修飾名です。</span><span class="sxs-lookup"><span data-stu-id="15101-191">The namespace-qualified name of the conceptual model association type that is being mapped.</span></span> |
| <span data-ttu-id="15101-192">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="15101-192">**StoreEntitySet**</span></span> | <span data-ttu-id="15101-193">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-193">No</span></span>          | <span data-ttu-id="15101-194">マップされるテーブルの名前です。</span><span class="sxs-lookup"><span data-stu-id="15101-194">The name of the table that is being mapped.</span></span>                                                 |

### <a name="example"></a><span data-ttu-id="15101-195">例</span><span class="sxs-lookup"><span data-stu-id="15101-195">Example</span></span>

<span data-ttu-id="15101-196">次の例は、概念モデルで **\_Department\_course**をデータベース内の**course**テーブルにマップする、 **AssociationSetMapping**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-196">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association set in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="15101-197">アソシエーション型のプロパティとテーブル列の間のマッピングは、子**Endproperty**要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="15101-197">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

## <a name="complexproperty-element-msl"></a><span data-ttu-id="15101-198">ComplexProperty 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-198">ComplexProperty Element (MSL)</span></span>

<span data-ttu-id="15101-199">マッピング仕様言語 (MSL) の**Complexproperty**要素は、概念モデルのエンティティ型の複合型プロパティと基になるデータベースのテーブル列との間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-199">A **ComplexProperty** element in mapping specification language (MSL) defines the mapping between a complex type property on a conceptual model entity type and table columns in the underlying database.</span></span> <span data-ttu-id="15101-200">プロパティ列のマッピングは、子 ScalarProperty 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="15101-200">The property-column mappings are specified in child ScalarProperty elements.</span></span>

<span data-ttu-id="15101-201">**ComplexType**プロパティ要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-201">The **ComplexType** property element can have the following child elements:</span></span>

-   <span data-ttu-id="15101-202">ScalarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-202">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="15101-203">**Complexproperty** (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-203">**ComplexProperty** (zero or more)</span></span>
-   <span data-ttu-id="15101-204">ComplextTypeMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-204">ComplextTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="15101-205">条件 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-205">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-206">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-206">Applicable Attributes</span></span>

<span data-ttu-id="15101-207">次の表では、 **Complexproperty**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-207">The following table describes the attributes that are applicable to the **ComplexProperty** element:</span></span>

| <span data-ttu-id="15101-208">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-208">Attribute Name</span></span> | <span data-ttu-id="15101-209">必須</span><span class="sxs-lookup"><span data-stu-id="15101-209">Is Required</span></span> | <span data-ttu-id="15101-210">値</span><span class="sxs-lookup"><span data-stu-id="15101-210">Value</span></span>                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-211">**名前**</span><span class="sxs-lookup"><span data-stu-id="15101-211">**Name**</span></span>       | <span data-ttu-id="15101-212">はい</span><span class="sxs-lookup"><span data-stu-id="15101-212">Yes</span></span>         | <span data-ttu-id="15101-213">マップされる概念モデルのエンティティ型の複合プロパティ名。</span><span class="sxs-lookup"><span data-stu-id="15101-213">The name of the complex property of an entity type in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="15101-214">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="15101-214">**TypeName**</span></span>   | <span data-ttu-id="15101-215">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-215">No</span></span>          | <span data-ttu-id="15101-216">概念モデルのプロパティ型の名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="15101-216">The namespace-qualified name of the conceptual model property type.</span></span>                              |

### <a name="example"></a><span data-ttu-id="15101-217">例</span><span class="sxs-lookup"><span data-stu-id="15101-217">Example</span></span>

<span data-ttu-id="15101-218">次の例は、School モデル に基づいています。</span><span class="sxs-lookup"><span data-stu-id="15101-218">The following example is based on the School model.</span></span> <span data-ttu-id="15101-219">次の複合型は概念モデルに追加されました。</span><span class="sxs-lookup"><span data-stu-id="15101-219">The following complex type has been added to the conceptual model:</span></span>

``` xml
 <ComplexType Name="FullName">
   <Property Type="String" Name="LastName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
   <Property Type="String" Name="FirstName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
 </ComplexType>
```

<span data-ttu-id="15101-220">**Person**エンティティ型の**LastName**プロパティと**FirstName**プロパティは、1つの複合プロパティ**Name**に置き換えられました。</span><span class="sxs-lookup"><span data-stu-id="15101-220">The **LastName** and **FirstName** properties of the **Person** entity type have been replaced with one complex property, **Name**:</span></span>

``` xml
 <EntityType Name="Person">
   <Key>
     <PropertyRef Name="PersonID" />
   </Key>
   <Property Name="PersonID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="HireDate" Type="DateTime" />
   <Property Name="EnrollmentDate" Type="DateTime" />
   <Property Name="Name" Type="SchoolModel.FullName" Nullable="false" />
 </EntityType>
```

<span data-ttu-id="15101-221">次の MSL は、 **Name**プロパティを基になるデータベースの列にマップするために使用される**complexproperty**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-221">The following MSL shows the **ComplexProperty** element used to map the **Name** property to columns in the underlying database:</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
       <ComplexProperty Name="Name" TypeName="SchoolModel.FullName">
         <ScalarProperty Name="FirstName" ColumnName="FirstName" />
         <ScalarProperty Name="LastName" ColumnName="LastName" />  
       </ComplexProperty>
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="complextypemapping-element-msl"></a><span data-ttu-id="15101-222">ComplexTypeMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-222">ComplexTypeMapping Element (MSL)</span></span>

<span data-ttu-id="15101-223">マッピング仕様言語 (MSL) の**Complextypemapping**要素は、resultmapping 要素の子であり、次の条件に該当する場合に、概念モデルの関数インポートと基になるデータベースのストアドプロシージャの間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-223">The **ComplexTypeMapping** element in mapping specification language (MSL) is a child of the ResultMapping element and defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="15101-224">関数インポートが概念の複合型を返す場合</span><span class="sxs-lookup"><span data-stu-id="15101-224">The function import returns a conceptual complex type.</span></span>
-   <span data-ttu-id="15101-225">ストアド プロシージャが返す列の名前が、複合型のプロパティの名前と完全には一致しない場合</span><span class="sxs-lookup"><span data-stu-id="15101-225">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the complex type.</span></span>

<span data-ttu-id="15101-226">既定では、ストアド プロシージャが返す列と複合型とのマッピングは、列およびプロパティの名前に基づいています。</span><span class="sxs-lookup"><span data-stu-id="15101-226">By default, the mapping between the columns returned by a stored procedure and a complex type is based on column and property names.</span></span> <span data-ttu-id="15101-227">列名がプロパティ名と完全に一致しない場合は、 **Complextypemapping**要素を使用してマッピングを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-227">If column names do not exactly match property names, you must use the **ComplexTypeMapping** element to define the mapping.</span></span> <span data-ttu-id="15101-228">既定のマッピングの例については、「FunctionImportMapping 要素 (MSL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-228">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="15101-229">**Complextypemapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-229">The **ComplexTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="15101-230">ScalarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-230">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-231">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-231">Applicable Attributes</span></span>

<span data-ttu-id="15101-232">次の表では、 **Complextypemapping**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-232">The following table describes the attributes that are applicable to the **ComplexTypeMapping** element.</span></span>

| <span data-ttu-id="15101-233">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-233">Attribute Name</span></span> | <span data-ttu-id="15101-234">必須</span><span class="sxs-lookup"><span data-stu-id="15101-234">Is Required</span></span> | <span data-ttu-id="15101-235">値</span><span class="sxs-lookup"><span data-stu-id="15101-235">Value</span></span>                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| <span data-ttu-id="15101-236">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="15101-236">**TypeName**</span></span>   | <span data-ttu-id="15101-237">はい</span><span class="sxs-lookup"><span data-stu-id="15101-237">Yes</span></span>         | <span data-ttu-id="15101-238">マッピングされている複合型の名前空間修飾名です。</span><span class="sxs-lookup"><span data-stu-id="15101-238">The namespace-qualified name of the complex type that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="15101-239">例</span><span class="sxs-lookup"><span data-stu-id="15101-239">Example</span></span>

<span data-ttu-id="15101-240">次のストアド プロシージャを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="15101-240">Consider the following stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

<span data-ttu-id="15101-241">また、次の概念モデルの複合型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="15101-241">Also consider the following conceptual model complex type:</span></span>

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

<span data-ttu-id="15101-242">前の複合型のインスタンスを返す関数インポートを作成するには、ストアドプロシージャによって返される列とエンティティ型の間のマッピングを**Complextypemapping**要素で定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-242">In order to create a function import that returns instances of the previous complex type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ComplexTypeMapping** element:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <ComplexTypeMapping TypeName="SchoolModel.GradeInfo">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </ComplexTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="condition-element-msl"></a><span data-ttu-id="15101-243">Condition 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-243">Condition Element (MSL)</span></span>

<span data-ttu-id="15101-244">マッピングスキーマ言語 (MSL) の**Condition**要素は、概念モデルと基になるデータベースとの間のマッピングに関する条件を配置します。</span><span class="sxs-lookup"><span data-stu-id="15101-244">The **Condition** element in mapping specification language (MSL) places conditions on mappings between the conceptual model and the underlying database.</span></span> <span data-ttu-id="15101-245">XML ノード内で定義されているマッピングは、子**条件**要素で指定されているすべての条件が満たされている場合に有効です。</span><span class="sxs-lookup"><span data-stu-id="15101-245">The mapping that is defined within an XML node is valid if all conditions, as specified in child **Condition** elements, are met.</span></span> <span data-ttu-id="15101-246">それ以外の場合、マッピングは無効です。</span><span class="sxs-lookup"><span data-stu-id="15101-246">Otherwise, the mapping is not valid.</span></span> <span data-ttu-id="15101-247">たとえば、MappingFragment 要素に1つ以上の**条件**子要素が含まれている場合、その子**条件**要素のすべての条件が満たされた場合にのみ、 **mappingfragment**ノード内で定義されたマッピングが有効になります。</span><span class="sxs-lookup"><span data-stu-id="15101-247">For example, if a MappingFragment element contains one or more **Condition** child elements, the mapping defined within the **MappingFragment** node will only be valid if all the conditions of the child **Condition** elements are met.</span></span>

<span data-ttu-id="15101-248">各条件は、**名前**(概念モデルエンティティプロパティ**の名前)** 、または**columnname** ( **columnname**属性で指定されたデータベース内の列の名前) のいずれかに適用できます。</span><span class="sxs-lookup"><span data-stu-id="15101-248">Each condition can apply to either a **Name** (the name of a conceptual model entity property, specified by the **Name** attribute), or a **ColumnName** (the name of a column in the database, specified by the **ColumnName** attribute).</span></span> <span data-ttu-id="15101-249">**Name**属性を設定すると、エンティティのプロパティ値に対して条件がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="15101-249">When the **Name** attribute is set, the condition is checked against an entity property value.</span></span> <span data-ttu-id="15101-250">**ColumnName**属性を設定すると、列の値に対して条件がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="15101-250">When the **ColumnName** attribute is set, the condition is checked against a column value.</span></span> <span data-ttu-id="15101-251">**Condition**要素では、 **Name**または**ColumnName**属性のいずれか1つだけを指定できます。</span><span class="sxs-lookup"><span data-stu-id="15101-251">Only one of the **Name** or **ColumnName** attribute can be specified in a **Condition** element.</span></span>

> [!NOTE]
> <span data-ttu-id="15101-252">FunctionImportMapping 要素内で**Condition**要素が使用されている場合、 **Name**属性だけが適用されません。</span><span class="sxs-lookup"><span data-stu-id="15101-252">When the **Condition** element is used within a FunctionImportMapping element, only the **Name** attribute is not applicable.</span></span>

<span data-ttu-id="15101-253">**Condition**要素は、次の要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-253">The **Condition** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="15101-254">AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="15101-254">AssociationSetMapping</span></span>
-   <span data-ttu-id="15101-255">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="15101-255">ComplexProperty</span></span>
-   <span data-ttu-id="15101-256">EntitySetMapping</span><span class="sxs-lookup"><span data-stu-id="15101-256">EntitySetMapping</span></span>
-   <span data-ttu-id="15101-257">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="15101-257">MappingFragment</span></span>
-   <span data-ttu-id="15101-258">EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="15101-258">EntityTypeMapping</span></span>

<span data-ttu-id="15101-259">**Condition**要素に子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="15101-259">The **Condition** element can have no child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-260">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-260">Applicable Attributes</span></span>

<span data-ttu-id="15101-261">次の表では、 **Condition**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-261">The following table describes the attributes that are applicable to the **Condition** element:</span></span>

| <span data-ttu-id="15101-262">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-262">Attribute Name</span></span> | <span data-ttu-id="15101-263">必須</span><span class="sxs-lookup"><span data-stu-id="15101-263">Is Required</span></span> | <span data-ttu-id="15101-264">値</span><span class="sxs-lookup"><span data-stu-id="15101-264">Value</span></span>                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-265">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="15101-265">**ColumnName**</span></span> | <span data-ttu-id="15101-266">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-266">No</span></span>          | <span data-ttu-id="15101-267">条件の評価に使用される値のテーブル列の名前。</span><span class="sxs-lookup"><span data-stu-id="15101-267">The name of the table column whose value is used to evaluate the condition.</span></span>                                                                                                                                                                                                                   |
| <span data-ttu-id="15101-268">**IsNull**</span><span class="sxs-lookup"><span data-stu-id="15101-268">**IsNull**</span></span>     | <span data-ttu-id="15101-269">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-269">No</span></span>          | <span data-ttu-id="15101-270">**True**または**False**。</span><span class="sxs-lookup"><span data-stu-id="15101-270">**True** or **False**.</span></span> <span data-ttu-id="15101-271">値が**true**で列の値が**null**である場合、または値が**False**で列の値が**null**でない場合、条件は true になります。</span><span class="sxs-lookup"><span data-stu-id="15101-271">If the value is **True** and the column value is **null**, or if the value is **False** and the column value is not **null**, the condition is true.</span></span> <span data-ttu-id="15101-272">それ以外の場合、条件は False です。</span><span class="sxs-lookup"><span data-stu-id="15101-272">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="15101-273">**IsNull**属性と**Value**属性は同時に使用できません。</span><span class="sxs-lookup"><span data-stu-id="15101-273">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span> |
| <span data-ttu-id="15101-274">**[値]**</span><span class="sxs-lookup"><span data-stu-id="15101-274">**Value**</span></span>      | <span data-ttu-id="15101-275">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-275">No</span></span>          | <span data-ttu-id="15101-276">列値と比較される値。</span><span class="sxs-lookup"><span data-stu-id="15101-276">The value with which the column value is compared.</span></span> <span data-ttu-id="15101-277">値が同じ場合、条件は True です。</span><span class="sxs-lookup"><span data-stu-id="15101-277">If the values are the same, the condition is true.</span></span> <span data-ttu-id="15101-278">それ以外の場合、条件は False です。</span><span class="sxs-lookup"><span data-stu-id="15101-278">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="15101-279">**IsNull**属性と**Value**属性は同時に使用できません。</span><span class="sxs-lookup"><span data-stu-id="15101-279">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span>                                                                       |
| <span data-ttu-id="15101-280">**名前**</span><span class="sxs-lookup"><span data-stu-id="15101-280">**Name**</span></span>       | <span data-ttu-id="15101-281">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-281">No</span></span>          | <span data-ttu-id="15101-282">条件の評価に使用される値を持つ概念モデルのエンティティ プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-282">The name of the conceptual model entity property whose value is used to evaluate the condition.</span></span> <br/> <span data-ttu-id="15101-283">**Condition**要素が FunctionImportMapping 要素内で使用されている場合、この属性は適用されません。</span><span class="sxs-lookup"><span data-stu-id="15101-283">This attribute is not applicable if the **Condition** element is used within a FunctionImportMapping element.</span></span>                                                                           |

### <a name="example"></a><span data-ttu-id="15101-284">例</span><span class="sxs-lookup"><span data-stu-id="15101-284">Example</span></span>

<span data-ttu-id="15101-285">次の例では、 **Mappingfragment**要素の子として**Condition**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-285">The following example shows **Condition** elements as children of **MappingFragment** elements.</span></span> <span data-ttu-id="15101-286">**HireDate**が null でなく、 **EnrollmentDate**が Null の場合、 **SchoolModel**型と**Person**テーブルの**PersonID**および**HireDate**列の間でデータがマップされます。</span><span class="sxs-lookup"><span data-stu-id="15101-286">When **HireDate** is not null and **EnrollmentDate** is null, data is mapped between the **SchoolModel.Instructor** type and the **PersonID** and **HireDate** columns of the **Person** table.</span></span> <span data-ttu-id="15101-287">**EnrollmentDate**が null でなく、 **HireDate**が Null の場合、 **Person**テーブルの**SchoolModel**型と**PersonID**列および**Enrollment**列の間でデータがマップされます。</span><span class="sxs-lookup"><span data-stu-id="15101-287">When **EnrollmentDate** is not null and **HireDate** is null, data is mapped between the **SchoolModel.Student** type and the **PersonID** and **Enrollment** columns of the **Person** table.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="deletefunction-element-msl"></a><span data-ttu-id="15101-288">DeleteFunction 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-288">DeleteFunction Element (MSL)</span></span>

<span data-ttu-id="15101-289">マッピングスキーマ言語 (MSL) の**deletefunction**要素は、概念モデルのエンティティ型またはアソシエーションの delete 関数を、基になるデータベースのストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-289">The **DeleteFunction** element in mapping specification language (MSL) maps the delete function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="15101-290">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-290">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="15101-291">詳細については、「Function 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-291">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="15101-292">エンティティ型の3つの挿入、更新、または削除操作をすべてストアドプロシージャにマップしない場合、実行時にマップされていない操作は失敗し、UpdateException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="15101-292">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

### <a name="deletefunction-applied-to-entitytypemapping"></a><span data-ttu-id="15101-293">EntityTypeMapping への DeleteFunction の適用</span><span class="sxs-lookup"><span data-stu-id="15101-293">DeleteFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="15101-294">EntityTypeMapping 要素に適用される場合、 **Deletefunction**要素は、概念モデルのエンティティ型の削除関数をストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-294">When applied to the EntityTypeMapping element, the **DeleteFunction** element maps the delete function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="15101-295">**Deletefunction**要素には、 **entitytypemapping**要素に適用するときに次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-295">The **DeleteFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="15101-296">AssociationEnd (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-296">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="15101-297">ComplexProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-297">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="15101-298">ScarlarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-298">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="15101-299">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-299">Applicable Attributes</span></span>

<span data-ttu-id="15101-300">次の表では、 **Entitytypemapping**要素に適用されるときに**deletefunction**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-300">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="15101-301">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-301">Attribute Name</span></span>            | <span data-ttu-id="15101-302">必須</span><span class="sxs-lookup"><span data-stu-id="15101-302">Is Required</span></span> | <span data-ttu-id="15101-303">値</span><span class="sxs-lookup"><span data-stu-id="15101-303">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-304">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="15101-304">**FunctionName**</span></span>          | <span data-ttu-id="15101-305">はい</span><span class="sxs-lookup"><span data-stu-id="15101-305">Yes</span></span>         | <span data-ttu-id="15101-306">削除関数のマップ先となるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="15101-306">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="15101-307">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-307">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="15101-308">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="15101-308">**RowsAffectedParameter**</span></span> | <span data-ttu-id="15101-309">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-309">No</span></span>          | <span data-ttu-id="15101-310">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-310">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="15101-311">例</span><span class="sxs-lookup"><span data-stu-id="15101-311">Example</span></span>

<span data-ttu-id="15101-312">次の例は、School モデルに基づいています。また、 **Person**エンティティ型の delete 関数を**deletefunction**ストアドプロシージャにマップする**deletefunction**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-312">The following example is based on the School model and shows the **DeleteFunction** element mapping the delete function of the **Person** entity type to the **DeletePerson** stored procedure.</span></span> <span data-ttu-id="15101-313">**Deleteperson**ストアドプロシージャは、ストレージモデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="15101-313">The **DeletePerson** stored procedure is declared in the storage model.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="deletefunction-applied-to-associationsetmapping"></a><span data-ttu-id="15101-314">AssociationSetMapping への DeleteFunction の適用</span><span class="sxs-lookup"><span data-stu-id="15101-314">DeleteFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="15101-315">AssociationSetMapping 要素に適用した場合、 **Deletefunction**要素は、概念モデルのアソシエーションの delete 関数をストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-315">When applied to the AssociationSetMapping element, the **DeleteFunction** element maps the delete function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="15101-316">**Deletefunction**要素には、 **AssociationSetMapping**要素に適用するときに次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-316">The **DeleteFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="15101-317">EndProperty</span><span class="sxs-lookup"><span data-stu-id="15101-317">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="15101-318">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-318">Applicable Attributes</span></span>

<span data-ttu-id="15101-319">次の表では、 **AssociationSetMapping**要素に適用されるときに**deletefunction**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-319">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="15101-320">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-320">Attribute Name</span></span>            | <span data-ttu-id="15101-321">必須</span><span class="sxs-lookup"><span data-stu-id="15101-321">Is Required</span></span> | <span data-ttu-id="15101-322">値</span><span class="sxs-lookup"><span data-stu-id="15101-322">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-323">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="15101-323">**FunctionName**</span></span>          | <span data-ttu-id="15101-324">はい</span><span class="sxs-lookup"><span data-stu-id="15101-324">Yes</span></span>         | <span data-ttu-id="15101-325">削除関数のマップ先となるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="15101-325">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="15101-326">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-326">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="15101-327">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="15101-327">**RowsAffectedParameter**</span></span> | <span data-ttu-id="15101-328">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-328">No</span></span>          | <span data-ttu-id="15101-329">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-329">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="15101-330">例</span><span class="sxs-lookup"><span data-stu-id="15101-330">Example</span></span>

<span data-ttu-id="15101-331">次の例は、School モデルに基づいており、 **CourseInstructor** association の delete 関数を**DeleteCourseInstructor**ストアドプロシージャにマップするために使用される**deletefunction**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-331">The following example is based on the School model and shows the **DeleteFunction** element used to map delete function of the **CourseInstructor** association to the **DeleteCourseInstructor** stored procedure.</span></span> <span data-ttu-id="15101-332">**DeleteCourseInstructor**ストアドプロシージャは、ストレージモデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="15101-332">The **DeleteCourseInstructor** stored procedure is declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="endproperty-element-msl"></a><span data-ttu-id="15101-333">EndProperty 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-333">EndProperty Element (MSL)</span></span>

<span data-ttu-id="15101-334">マッピングスキーマ言語 (MSL) の**Endproperty**要素は、概念モデルのアソシエーションの end または変更関数と基になるデータベースとの間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-334">The **EndProperty** element in mapping specification language (MSL) defines the mapping between an end or a modification function of a conceptual model association and the underlying database.</span></span> <span data-ttu-id="15101-335">プロパティ列のマッピングは子 ScalarProperty 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="15101-335">The property-column mapping is specified in a child ScalarProperty element.</span></span>

<span data-ttu-id="15101-336">**Endproperty**要素を使用して、概念モデルのアソシエーションの end のマッピングを定義すると、AssociationSetMapping 要素の子になります。</span><span class="sxs-lookup"><span data-stu-id="15101-336">When an **EndProperty** element is used to define the mapping for the end of a conceptual model association, it is a child of an AssociationSetMapping element.</span></span> <span data-ttu-id="15101-337">**Endproperty**要素を使用して、概念モデルの関連付けの変更関数のマッピングを定義すると、insertfunction 要素または deletefunction 要素の子になります。</span><span class="sxs-lookup"><span data-stu-id="15101-337">When the **EndProperty** element is used to define the mapping for a modification function of a conceptual model association, it is a child of an InsertFunction element or DeleteFunction element.</span></span>

<span data-ttu-id="15101-338">**Endproperty**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-338">The **EndProperty** element can have the following child elements:</span></span>

-   <span data-ttu-id="15101-339">ScalarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-339">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-340">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-340">Applicable Attributes</span></span>

<span data-ttu-id="15101-341">次の表では、 **Endproperty**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-341">The following table describes the attributes that are applicable to the **EndProperty** element:</span></span>

| <span data-ttu-id="15101-342">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-342">Attribute Name</span></span> | <span data-ttu-id="15101-343">必須</span><span class="sxs-lookup"><span data-stu-id="15101-343">Is Required</span></span> | <span data-ttu-id="15101-344">値</span><span class="sxs-lookup"><span data-stu-id="15101-344">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="15101-345">Name</span><span class="sxs-lookup"><span data-stu-id="15101-345">Name</span></span>           | <span data-ttu-id="15101-346">はい</span><span class="sxs-lookup"><span data-stu-id="15101-346">Yes</span></span>         | <span data-ttu-id="15101-347">マップされるアソシエーション端の名前。</span><span class="sxs-lookup"><span data-stu-id="15101-347">The name of the association end that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="15101-348">例</span><span class="sxs-lookup"><span data-stu-id="15101-348">Example</span></span>

<span data-ttu-id="15101-349">次の例は、概念モデルで **\_Department\_course**をデータベースの**course**テーブルにマップする**AssociationSetMapping**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-349">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="15101-350">アソシエーション型のプロパティとテーブル列の間のマッピングは、子**Endproperty**要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="15101-350">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

### <a name="example"></a><span data-ttu-id="15101-351">例</span><span class="sxs-lookup"><span data-stu-id="15101-351">Example</span></span>

<span data-ttu-id="15101-352">次の例は、関連付け (**CourseInstructor**) の挿入関数と削除関数を基になるデータベースのストアドプロシージャにマップする**endproperty**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-352">The following example shows the **EndProperty** element mapping the insert and delete functions of an association (**CourseInstructor**) to stored procedures in the underlying database.</span></span> <span data-ttu-id="15101-353">ストレージ モデルにマップされて宣言されている関数。</span><span class="sxs-lookup"><span data-stu-id="15101-353">The functions that are mapped to are declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="entitycontainermapping-element-msl"></a><span data-ttu-id="15101-354">EntityContainerMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-354">EntityContainerMapping Element (MSL)</span></span>

<span data-ttu-id="15101-355">マッピング仕様言語 (MSL) の**EntityContainerMapping**要素は、概念モデルのエンティティコンテナーをストレージモデルのエンティティコンテナーにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-355">The **EntityContainerMapping** element in mapping specification language (MSL) maps the entity container in the conceptual model to the entity container in the storage model.</span></span> <span data-ttu-id="15101-356">**EntityContainerMapping**要素は、Mapping 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="15101-356">The **EntityContainerMapping** element is a child of the Mapping element.</span></span>

<span data-ttu-id="15101-357">**EntityContainerMapping**要素には、次の子要素を含めることができます (順に表示されます)。</span><span class="sxs-lookup"><span data-stu-id="15101-357">The **EntityContainerMapping** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="15101-358">EntitySetMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-358">EntitySetMapping (zero or more)</span></span>
-   <span data-ttu-id="15101-359">AssociationSetMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-359">AssociationSetMapping (zero or more)</span></span>
-   <span data-ttu-id="15101-360">FunctionImportMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-360">FunctionImportMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-361">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-361">Applicable Attributes</span></span>

<span data-ttu-id="15101-362">次の表では、 **EntityContainerMapping**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-362">The following table describes the attributes that can be applied to the **EntityContainerMapping** element.</span></span>

| <span data-ttu-id="15101-363">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-363">Attribute Name</span></span>            | <span data-ttu-id="15101-364">必須</span><span class="sxs-lookup"><span data-stu-id="15101-364">Is Required</span></span> | <span data-ttu-id="15101-365">値</span><span class="sxs-lookup"><span data-stu-id="15101-365">Value</span></span>                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-366">**StorageModelContainer**</span><span class="sxs-lookup"><span data-stu-id="15101-366">**StorageModelContainer**</span></span> | <span data-ttu-id="15101-367">はい</span><span class="sxs-lookup"><span data-stu-id="15101-367">Yes</span></span>         | <span data-ttu-id="15101-368">マップされるストレージ モデルのエンティティ コンテナーの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-368">The name of the storage model entity container that is being mapped.</span></span>                                                                                                                                                                                     |
| <span data-ttu-id="15101-369">**CdmEntityContainer**</span><span class="sxs-lookup"><span data-stu-id="15101-369">**CdmEntityContainer**</span></span>    | <span data-ttu-id="15101-370">はい</span><span class="sxs-lookup"><span data-stu-id="15101-370">Yes</span></span>         | <span data-ttu-id="15101-371">マップされる概念モデルのエンティティ コンテナーの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-371">The name of the conceptual model entity container that is being mapped.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="15101-372">**GenerateUpdateViews**</span><span class="sxs-lookup"><span data-stu-id="15101-372">**GenerateUpdateViews**</span></span>   | <span data-ttu-id="15101-373">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-373">No</span></span>          | <span data-ttu-id="15101-374">**True**または**False**。</span><span class="sxs-lookup"><span data-stu-id="15101-374">**True** or **False**.</span></span> <span data-ttu-id="15101-375">**False**の場合、更新ビューは生成されません。</span><span class="sxs-lookup"><span data-stu-id="15101-375">If **False**, no update views are generated.</span></span> <span data-ttu-id="15101-376">データが正常にラウンドトリップしない可能性があるため、読み取り専用マッピングが無効である場合は、この属性を**False**に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-376">This attribute should be set to **False** when you have a read-only mapping that would be invalid because data may not round-trip successfully.</span></span> <br/> <span data-ttu-id="15101-377">既定値は **True** です。</span><span class="sxs-lookup"><span data-stu-id="15101-377">The default value is **True**.</span></span> |

### <a name="example"></a><span data-ttu-id="15101-378">例</span><span class="sxs-lookup"><span data-stu-id="15101-378">Example</span></span>

<span data-ttu-id="15101-379">次の例は、 **SchoolModelEntities**コンテナー (概念モデルのエンティティコンテナー) を**SchoolModelStoreContainer**コンテナー (ストレージモデルのエンティティコンテナー) にマップする**EntityContainerMapping**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-379">The following example shows an **EntityContainerMapping** element that maps the **SchoolModelEntities** container (the conceptual model entity container) to the **SchoolModelStoreContainer** container (the storage model entity container):</span></span>

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolModelEntities">
   <EntitySetMapping Name="Courses">
     <EntityTypeMapping TypeName="c.Course">
       <MappingFragment StoreEntitySet="Course">
         <ScalarProperty Name="CourseID" ColumnName="CourseID" />
         <ScalarProperty Name="Title" ColumnName="Title" />
         <ScalarProperty Name="Credits" ColumnName="Credits" />
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments">
     <EntityTypeMapping TypeName="c.Department">
       <MappingFragment StoreEntitySet="Department">
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         <ScalarProperty Name="Name" ColumnName="Name" />
         <ScalarProperty Name="Budget" ColumnName="Budget" />
         <ScalarProperty Name="StartDate" ColumnName="StartDate" />
         <ScalarProperty Name="Administrator" ColumnName="Administrator" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
 </EntityContainerMapping>
```

## <a name="entitysetmapping-element-msl"></a><span data-ttu-id="15101-380">EntitySetMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-380">EntitySetMapping Element (MSL)</span></span>

<span data-ttu-id="15101-381">マッピング仕様言語 (MSL) の**EntitySetMapping**要素は、概念モデルエンティティセット内のすべての型をストレージモデルのエンティティセットにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-381">The **EntitySetMapping** element in mapping specification language (MSL) maps all types in a conceptual model entity set to entity sets in the storage model.</span></span> <span data-ttu-id="15101-382">概念モデルのエンティティ セットは、同じ型のエンティティのインスタンス (および派生型) の論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="15101-382">An entity set in the conceptual model is a logical container for instances of entities of the same type (and derived types).</span></span> <span data-ttu-id="15101-383">ストレージ モデルのエンティティ セットは、基になるデータベースのテーブルまたはビューを表します。</span><span class="sxs-lookup"><span data-stu-id="15101-383">An entity set in the storage model represents a table or view in the underlying database.</span></span> <span data-ttu-id="15101-384">概念モデルのエンティティセットは、 **EntitySetMapping**要素の**Name**属性の値によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="15101-384">The conceptual model entity set is specified by the value of the **Name** attribute of the **EntitySetMapping** element.</span></span> <span data-ttu-id="15101-385">マップ先のテーブルまたはビューは、各子 MappingFragment 要素または**EntitySetMapping**要素自体の**storeentityset**属性によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="15101-385">The mapped-to table or view is specified by the **StoreEntitySet** attribute in each child MappingFragment element or in the **EntitySetMapping** element itself.</span></span>

<span data-ttu-id="15101-386">**EntitySetMapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-386">The **EntitySetMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="15101-387">EntityTypeMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-387">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="15101-388">QueryView (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="15101-388">QueryView (zero or one)</span></span>
-   <span data-ttu-id="15101-389">MappingFragment (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-389">MappingFragment (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-390">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-390">Applicable Attributes</span></span>

<span data-ttu-id="15101-391">次の表では、 **EntitySetMapping**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-391">The following table describes the attributes that can be applied to the **EntitySetMapping** element.</span></span>

| <span data-ttu-id="15101-392">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-392">Attribute Name</span></span>           | <span data-ttu-id="15101-393">必須</span><span class="sxs-lookup"><span data-stu-id="15101-393">Is Required</span></span> | <span data-ttu-id="15101-394">値</span><span class="sxs-lookup"><span data-stu-id="15101-394">Value</span></span>                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-395">**名前**</span><span class="sxs-lookup"><span data-stu-id="15101-395">**Name**</span></span>                 | <span data-ttu-id="15101-396">はい</span><span class="sxs-lookup"><span data-stu-id="15101-396">Yes</span></span>         | <span data-ttu-id="15101-397">マッピングされている概念モデルのエンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-397">The name of the conceptual model entity set that is being mapped.</span></span>                                                                                                                                                             |
| <span data-ttu-id="15101-398">**TypeName** **1**</span><span class="sxs-lookup"><span data-stu-id="15101-398">**TypeName** **1**</span></span>       | <span data-ttu-id="15101-399">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-399">No</span></span>          | <span data-ttu-id="15101-400">マッピングされている概念モデルのエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="15101-400">The name of the conceptual model entity type that is being mapped.</span></span>                                                                                                                                                            |
| <span data-ttu-id="15101-401">**Storeentityset** **1**</span><span class="sxs-lookup"><span data-stu-id="15101-401">**StoreEntitySet** **1**</span></span> | <span data-ttu-id="15101-402">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-402">No</span></span>          | <span data-ttu-id="15101-403">マップ先のストレージ モデルのエンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-403">The name of the storage model entity set that is being mapped to.</span></span>                                                                                                                                                             |
| <span data-ttu-id="15101-404">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="15101-404">**MakeColumnsDistinct**</span></span>  | <span data-ttu-id="15101-405">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-405">No</span></span>          | <span data-ttu-id="15101-406">個別の行のみが返されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="15101-406">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="15101-407">この属性が**True**に設定されている場合は、EntityContainerMapping 要素の**generateupdateviews**属性が**False**に設定されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-407">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

 

<span data-ttu-id="15101-408">**1**単一のエンティティ型を1つのテーブルにマップするために、TypeName 属性と storeentityset 子要素の代わりに**TypeName**属性と**storeentityset**属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="15101-408">**1** The **TypeName** and **StoreEntitySet** attributes can be used in place of the EntityTypeMapping and MappingFragment child elements to map a single entity type to a single table.</span></span>

### <a name="example"></a><span data-ttu-id="15101-409">例</span><span class="sxs-lookup"><span data-stu-id="15101-409">Example</span></span>

<span data-ttu-id="15101-410">次の例は、概念モデルの course エンティティセットに含まれる 3**つの型**(基本型と2つの派生型) を、基になるデータベース内の3つの異なるテーブルにマップする**EntitySetMapping**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-410">The following example shows an **EntitySetMapping** element that maps three types (a base type and two derived types) in the **Courses** entity set of the conceptual model to three different tables in the underlying database.</span></span> <span data-ttu-id="15101-411">テーブルは、各**Mappingfragment**要素の**storeentityset**属性によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="15101-411">The tables are specified by the **StoreEntitySet** attribute in each **MappingFragment** element.</span></span>

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.Course)">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnlineCourse)">
     <MappingFragment StoreEntitySet="OnlineCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="URL" ColumnName="URL" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnsiteCourse)">
     <MappingFragment StoreEntitySet="OnsiteCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Time" ColumnName="Time" />
       <ScalarProperty Name="Days" ColumnName="Days" />
       <ScalarProperty Name="Location" ColumnName="Location" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="entitytypemapping-element-msl"></a><span data-ttu-id="15101-412">EntityTypeMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-412">EntityTypeMapping Element (MSL)</span></span>

<span data-ttu-id="15101-413">マッピング仕様言語 (MSL) の**Entitytypemapping**要素は、概念モデルのエンティティ型と、基になるデータベースのテーブルまたはビューの間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-413">The **EntityTypeMapping** element in mapping specification language (MSL) defines the mapping between an entity type in the conceptual model and tables or views in the underlying database.</span></span> <span data-ttu-id="15101-414">概念モデルのエンティティ型および基になるデータベース テーブルまたはビューの詳細については、「EntityType 要素 (CSDL)」および「EntitySet 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-414">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="15101-415">マッピングされている概念モデルのエンティティ型は、 **Entitytypemapping**要素の**TypeName**属性によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="15101-415">The conceptual model entity type that is being mapped is specified by the **TypeName** attribute of the **EntityTypeMapping** element.</span></span> <span data-ttu-id="15101-416">マップされるテーブルまたはビューは、子 MappingFragment 要素の**Storeentityset**属性によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="15101-416">The table or view that is being mapped is specified by the **StoreEntitySet** attribute of the child MappingFragment element.</span></span>

<span data-ttu-id="15101-417">ModificationFunctionMapping 子要素を使用すると、エンティティ型の挿入関数、更新関数、または削除関数をデータベース内のストアド プロシージャにマッピングできます。</span><span class="sxs-lookup"><span data-stu-id="15101-417">The ModificationFunctionMapping child element can be used to map the insert, update, or delete functions of entity types to stored procedures in the database.</span></span>

<span data-ttu-id="15101-418">**Entitytypemapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-418">The **EntityTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="15101-419">MappingFragment (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-419">MappingFragment (zero or more)</span></span>
-   <span data-ttu-id="15101-420">ModificationFunctionMapping (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="15101-420">ModificationFunctionMapping (zero or one)</span></span>
-   <span data-ttu-id="15101-421">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="15101-421">ScalarProperty</span></span>
-   <span data-ttu-id="15101-422">状態</span><span class="sxs-lookup"><span data-stu-id="15101-422">Condition</span></span>

> [!NOTE]
> <span data-ttu-id="15101-423">**Mappingfragment**要素と**ModificationFunctionMapping**要素は、同時に**entitytypemapping**要素の子要素にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="15101-423">**MappingFragment** and **ModificationFunctionMapping** elements cannot be child elements of the **EntityTypeMapping** element at the same time.</span></span>


> [!NOTE]
> <span data-ttu-id="15101-424">**ScalarProperty**要素と**Condition**要素は、functionimportmapping 要素内で使用されている場合にのみ、 **entitytypemapping**要素の子要素にすることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-424">The **ScalarProperty** and **Condition** elements can only be child elements of the **EntityTypeMapping** element when it is used within a FunctionImportMapping element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-425">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-425">Applicable Attributes</span></span>

<span data-ttu-id="15101-426">次の表では、 **Entitytypemapping**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-426">The following table describes the attributes that can be applied to the **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="15101-427">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-427">Attribute Name</span></span> | <span data-ttu-id="15101-428">必須</span><span class="sxs-lookup"><span data-stu-id="15101-428">Is Required</span></span> | <span data-ttu-id="15101-429">値</span><span class="sxs-lookup"><span data-stu-id="15101-429">Value</span></span>                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-430">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="15101-430">**TypeName**</span></span>   | <span data-ttu-id="15101-431">はい</span><span class="sxs-lookup"><span data-stu-id="15101-431">Yes</span></span>         | <span data-ttu-id="15101-432">マッピングされている概念モデルのエンティティ型の名前空間修飾名です。</span><span class="sxs-lookup"><span data-stu-id="15101-432">The namespace-qualified name of the conceptual model entity type that is being mapped.</span></span> <br/> <span data-ttu-id="15101-433">型が抽象型または派生型である場合、値は `IsOfType(Namespace-qualified_type_name)` になる必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-433">If the type is abstract or a derived type, the value must be `IsOfType(Namespace-qualified_type_name)`.</span></span> |

### <a name="example"></a><span data-ttu-id="15101-434">例</span><span class="sxs-lookup"><span data-stu-id="15101-434">Example</span></span>

<span data-ttu-id="15101-435">次の例は、2つの子**Entitytypemapping**要素を持つ EntitySetMapping 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-435">The following example shows an EntitySetMapping element with two child **EntityTypeMapping** elements.</span></span> <span data-ttu-id="15101-436">最初の**Entitytypemapping**要素では、 **SchoolModel**エンティティ型が**person**テーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="15101-436">In the first **EntityTypeMapping** element, the **SchoolModel.Person** entity type is mapped to the **Person** table.</span></span> <span data-ttu-id="15101-437">2番目の**Entitytypemapping**要素では、 **SchoolModel**型の更新機能が、データベース内のストアドプロシージャ**updateperson**にマップされます。</span><span class="sxs-lookup"><span data-stu-id="15101-437">In the second **EntityTypeMapping** element, the update functionality of the **SchoolModel.Person** type is mapped to a stored procedure, **UpdatePerson**, in the database.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate" ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="15101-438">例</span><span class="sxs-lookup"><span data-stu-id="15101-438">Example</span></span>

<span data-ttu-id="15101-439">次の例は、ルート型が抽象である型階層のマッピングを示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-439">The next example shows the mapping of a type hierarchy in which the root type is abstract.</span></span> <span data-ttu-id="15101-440">**TypeName**属性の `IsOfType` 構文の使用に注意してください。</span><span class="sxs-lookup"><span data-stu-id="15101-440">Note the use of the `IsOfType` syntax for the **TypeName** attributes.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="functionimportmapping-element-msl"></a><span data-ttu-id="15101-441">FunctionImportMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-441">FunctionImportMapping Element (MSL)</span></span>

<span data-ttu-id="15101-442">マッピングスキーマ言語 (MSL) の**Functionimportmapping**要素は、概念モデルの関数インポートと、基になるデータベースのストアドプロシージャまたは関数との間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-442">The **FunctionImportMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure or function in the underlying database.</span></span> <span data-ttu-id="15101-443">関数インポートは概念モデル内で、ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-443">Function imports must be declared in the conceptual model and stored procedures must be declared in the storage model.</span></span> <span data-ttu-id="15101-444">詳細については、「FunctionImport 要素 (CSDL)」および「Function 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-444">For more information, see FunctionImport Element (CSDL) and Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="15101-445">既定では、関数インポートが概念モデル エンティティ型または複合型を返す場合、基になるストアド プロシージャが返す列の名前が概念モデル型のプロパティの名前と正確に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-445">By default, if a function import returns a conceptual model entity type or complex type, then the names of the columns returned by the underlying stored procedure must exactly match the names of the properties on the conceptual model type.</span></span> <span data-ttu-id="15101-446">列名がプロパティ名と完全に一致しない場合は、ResultMapping 要素でマッピングを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-446">If the column names do not exactly match the property names, the mapping must be defined in a ResultMapping element.</span></span>

<span data-ttu-id="15101-447">**Functionimportmapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-447">The **FunctionImportMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="15101-448">ResultMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-448">ResultMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-449">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-449">Applicable Attributes</span></span>

<span data-ttu-id="15101-450">次の表では、 **Functionimportmapping**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-450">The following table describes the attributes that are applicable to the **FunctionImportMapping** element:</span></span>

| <span data-ttu-id="15101-451">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-451">Attribute Name</span></span>         | <span data-ttu-id="15101-452">必須</span><span class="sxs-lookup"><span data-stu-id="15101-452">Is Required</span></span> | <span data-ttu-id="15101-453">値</span><span class="sxs-lookup"><span data-stu-id="15101-453">Value</span></span>                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-454">**FunctionImportName**</span><span class="sxs-lookup"><span data-stu-id="15101-454">**FunctionImportName**</span></span> | <span data-ttu-id="15101-455">はい</span><span class="sxs-lookup"><span data-stu-id="15101-455">Yes</span></span>         | <span data-ttu-id="15101-456">マップされる概念モデルの関数インポートの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-456">The name of the function import in the conceptual model that is being mapped.</span></span>           |
| <span data-ttu-id="15101-457">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="15101-457">**FunctionName**</span></span>       | <span data-ttu-id="15101-458">はい</span><span class="sxs-lookup"><span data-stu-id="15101-458">Yes</span></span>         | <span data-ttu-id="15101-459">マップされるストレージ モデルの関数の名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="15101-459">The namespace-qualified name of the function in the storage model that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="15101-460">例</span><span class="sxs-lookup"><span data-stu-id="15101-460">Example</span></span>

<span data-ttu-id="15101-461">次の例は、School モデル に基づいています。</span><span class="sxs-lookup"><span data-stu-id="15101-461">The following example is based on the School model.</span></span> <span data-ttu-id="15101-462">ストレージ モデルの次の関数について考えます。</span><span class="sxs-lookup"><span data-stu-id="15101-462">Consider the following function in the storage model:</span></span>

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

<span data-ttu-id="15101-463">また、概念モデルのこの関数インポートについて考えます。</span><span class="sxs-lookup"><span data-stu-id="15101-463">Also consider this function import in the conceptual model:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

<span data-ttu-id="15101-464">次の例は、関数と関数のインポートを相互にマップするために使用される**Functionimportmapping**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-464">The following example show a **FunctionImportMapping** element used to map the function and function import above to each other:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a><span data-ttu-id="15101-465">InsertFunction 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-465">InsertFunction Element (MSL)</span></span>

<span data-ttu-id="15101-466">マッピング仕様言語 (MSL) の**insertfunction**要素は、概念モデルのエンティティ型またはアソシエーションの挿入関数を、基になるデータベースのストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-466">The **InsertFunction** element in mapping specification language (MSL) maps the insert function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="15101-467">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-467">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="15101-468">詳細については、「Function 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-468">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="15101-469">エンティティ型の3つの挿入、更新、または削除操作をすべてストアドプロシージャにマップしない場合、実行時にマップされていない操作は失敗し、UpdateException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="15101-469">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="15101-470">**Insertfunction**要素は ModificationFunctionMapping 要素の子にすることができ、entitytypemapping 要素または AssociationSetMapping 要素に適用されます。</span><span class="sxs-lookup"><span data-stu-id="15101-470">The **InsertFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

### <a name="insertfunction-applied-to-entitytypemapping"></a><span data-ttu-id="15101-471">EntityTypeMapping への InsertFunction の適用</span><span class="sxs-lookup"><span data-stu-id="15101-471">InsertFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="15101-472">EntityTypeMapping 要素に適用される場合、 **Insertfunction**要素は、概念モデルのエンティティ型の挿入関数をストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-472">When applied to the EntityTypeMapping element, the **InsertFunction** element maps the insert function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="15101-473">**Insertfunction**要素には、 **entitytypemapping**要素に適用するときに次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-473">The **InsertFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="15101-474">AssociationEnd (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-474">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="15101-475">ComplexProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-475">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="15101-476">ResultBinding (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="15101-476">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="15101-477">ScarlarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-477">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="15101-478">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-478">Applicable Attributes</span></span>

<span data-ttu-id="15101-479">次の表では、 **Entitytypemapping**要素に適用するときに**insertfunction**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-479">The following table describes the attributes that can be applied to the **InsertFunction** element when applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="15101-480">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-480">Attribute Name</span></span>            | <span data-ttu-id="15101-481">必須</span><span class="sxs-lookup"><span data-stu-id="15101-481">Is Required</span></span> | <span data-ttu-id="15101-482">値</span><span class="sxs-lookup"><span data-stu-id="15101-482">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-483">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="15101-483">**FunctionName**</span></span>          | <span data-ttu-id="15101-484">はい</span><span class="sxs-lookup"><span data-stu-id="15101-484">Yes</span></span>         | <span data-ttu-id="15101-485">挿入関数のマップ先であるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="15101-485">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="15101-486">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-486">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="15101-487">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="15101-487">**RowsAffectedParameter**</span></span> | <span data-ttu-id="15101-488">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-488">No</span></span>          | <span data-ttu-id="15101-489">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-489">The name of the output parameter that returns the number of affected rows.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="15101-490">例</span><span class="sxs-lookup"><span data-stu-id="15101-490">Example</span></span>

<span data-ttu-id="15101-491">次の例は、School モデルに基づいており、Person エンティティ型の insert 関数を**Insertfunction**ストアドプロシージャにマップするために使用される**insertfunction**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-491">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the Person entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="15101-492">**Insertperson**ストアドプロシージャは、ストレージモデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="15101-492">The **InsertPerson** stored procedure is declared in the storage model.</span></span>

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
### <a name="insertfunction-applied-to-associationsetmapping"></a><span data-ttu-id="15101-493">AssociationSetMapping への InsertFunction の適用</span><span class="sxs-lookup"><span data-stu-id="15101-493">InsertFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="15101-494">**Insertfunction**要素を AssociationSetMapping 要素に適用すると、概念モデルのアソシエーションの挿入関数をストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-494">When applied to the AssociationSetMapping element, the **InsertFunction** element maps the insert function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="15101-495">**Insertfunction**要素には、 **AssociationSetMapping**要素に適用するときに次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-495">The **InsertFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="15101-496">EndProperty</span><span class="sxs-lookup"><span data-stu-id="15101-496">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="15101-497">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-497">Applicable Attributes</span></span>

<span data-ttu-id="15101-498">次の表では、 **Insertfunction**要素が**AssociationSetMapping**要素に適用されるときに適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-498">The following table describes the attributes that can be applied to the **InsertFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="15101-499">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-499">Attribute Name</span></span>            | <span data-ttu-id="15101-500">必須</span><span class="sxs-lookup"><span data-stu-id="15101-500">Is Required</span></span> | <span data-ttu-id="15101-501">値</span><span class="sxs-lookup"><span data-stu-id="15101-501">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-502">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="15101-502">**FunctionName**</span></span>          | <span data-ttu-id="15101-503">はい</span><span class="sxs-lookup"><span data-stu-id="15101-503">Yes</span></span>         | <span data-ttu-id="15101-504">挿入関数のマップ先であるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="15101-504">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="15101-505">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-505">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="15101-506">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="15101-506">**RowsAffectedParameter**</span></span> | <span data-ttu-id="15101-507">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-507">No</span></span>          | <span data-ttu-id="15101-508">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-508">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="15101-509">例</span><span class="sxs-lookup"><span data-stu-id="15101-509">Example</span></span>

<span data-ttu-id="15101-510">次の例は、School モデルに基づいており、 **CourseInstructor** association の insert 関数を**InsertCourseInstructor**ストアドプロシージャにマップするために使用される**insertfunction**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-510">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the **CourseInstructor** association to the **InsertCourseInstructor** stored procedure.</span></span> <span data-ttu-id="15101-511">**InsertCourseInstructor**ストアドプロシージャは、ストレージモデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="15101-511">The **InsertCourseInstructor** stored procedure is declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="mapping-element-msl"></a><span data-ttu-id="15101-512">Mapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-512">Mapping Element (MSL)</span></span>

<span data-ttu-id="15101-513">マッピングスキーマ言語 (MSL) の**mapping**要素には、概念モデルで定義されているオブジェクトをデータベースにマップするための情報が含まれています (ストレージモデルの説明を参照)。</span><span class="sxs-lookup"><span data-stu-id="15101-513">The **Mapping** element in mapping specification language (MSL) contains information for mapping objects that are defined in a conceptual model to a database (as described in a storage model).</span></span> <span data-ttu-id="15101-514">詳細については、「CSDL 仕様」と「SSDL 仕様」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-514">For more information, see CSDL Specification and SSDL Specification.</span></span>

<span data-ttu-id="15101-515">**Mapping**要素は、マッピングスキーマのルート要素です。</span><span class="sxs-lookup"><span data-stu-id="15101-515">The **Mapping** element is the root element for a mapping specification.</span></span> <span data-ttu-id="15101-516">マッピング仕様の XML 名前空間は https://schemas.microsoft.com/ado/2009/11/mapping/cs します。</span><span class="sxs-lookup"><span data-stu-id="15101-516">The XML namespace for mapping specifications is https://schemas.microsoft.com/ado/2009/11/mapping/cs.</span></span>

<span data-ttu-id="15101-517">マッピング要素には、次の子要素をここに示す順序で含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-517">The mapping element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="15101-518">Alias (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-518">Alias (zero or more)</span></span>
-   <span data-ttu-id="15101-519">EntityContainerMapping (正確に 1)</span><span class="sxs-lookup"><span data-stu-id="15101-519">EntityContainerMapping (exactly one)</span></span>

<span data-ttu-id="15101-520">MSL で参照される概念モデル型およびストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-520">Names of conceptual and storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="15101-521">概念モデルの名前空間の名前については、「Schema 要素 (CSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-521">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="15101-522">ストレージモデルの名前空間の名前については、「Schema 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-522">For information about the storage model namespace name, see Schema Element (SSDL).</span></span> <span data-ttu-id="15101-523">MSL で使用される名前空間の別名は、Alias 要素で定義できます。</span><span class="sxs-lookup"><span data-stu-id="15101-523">Aliases for namespaces that are used in MSL can be defined with the Alias element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-524">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-524">Applicable Attributes</span></span>

<span data-ttu-id="15101-525">次の表では、**マッピング**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-525">The table below describes the attributes that can be applied to the **Mapping** element.</span></span>

| <span data-ttu-id="15101-526">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-526">Attribute Name</span></span> | <span data-ttu-id="15101-527">必須</span><span class="sxs-lookup"><span data-stu-id="15101-527">Is Required</span></span> | <span data-ttu-id="15101-528">値</span><span class="sxs-lookup"><span data-stu-id="15101-528">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="15101-529">**スペース**</span><span class="sxs-lookup"><span data-stu-id="15101-529">**Space**</span></span>      | <span data-ttu-id="15101-530">はい</span><span class="sxs-lookup"><span data-stu-id="15101-530">Yes</span></span>         | <span data-ttu-id="15101-531">**C-S**。</span><span class="sxs-lookup"><span data-stu-id="15101-531">**C-S**.</span></span> <span data-ttu-id="15101-532">これは固定値で、変更できません。</span><span class="sxs-lookup"><span data-stu-id="15101-532">This is a fixed value and cannot be changed.</span></span> |

### <a name="example"></a><span data-ttu-id="15101-533">例</span><span class="sxs-lookup"><span data-stu-id="15101-533">Example</span></span>

<span data-ttu-id="15101-534">次の例は、School モデルの一部に基づく**マッピング**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-534">The following example shows a **Mapping** element that is based on part of the School model.</span></span> <span data-ttu-id="15101-535">School モデルの詳細については、「クイックスタート (Entity Framework)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-535">For more information about the School model, see Quickstart (Entity Framework):</span></span>

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="mappingfragment-element-msl"></a><span data-ttu-id="15101-536">MappingFragment 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-536">MappingFragment Element (MSL)</span></span>

<span data-ttu-id="15101-537">マッピングスキーマ言語 (MSL) の**Mappingfragment**要素は、概念モデルのエンティティ型のプロパティと、データベース内のテーブルまたはビューの間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-537">The **MappingFragment** element in mapping specification language (MSL) defines the mapping between the properties of a conceptual model entity type and a table or view in the database.</span></span> <span data-ttu-id="15101-538">概念モデルのエンティティ型および基になるデータベース テーブルまたはビューの詳細については、「EntityType 要素 (CSDL)」および「EntitySet 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-538">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="15101-539">**Mappingfragment**は、entitytypemapping 要素または EntitySetMapping 要素の子要素にすることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-539">The **MappingFragment** can be a child element of the EntityTypeMapping element or the EntitySetMapping element.</span></span>

<span data-ttu-id="15101-540">**Mappingfragment**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-540">The **MappingFragment** element can have the following child elements:</span></span>

-   <span data-ttu-id="15101-541">ComplexType (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-541">ComplexType (zero or more)</span></span>
-   <span data-ttu-id="15101-542">ScalarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-542">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="15101-543">条件 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-543">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-544">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-544">Applicable Attributes</span></span>

<span data-ttu-id="15101-545">次の表では、 **Mappingfragment**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-545">The following table describes the attributes that can be applied to the **MappingFragment** element.</span></span>

| <span data-ttu-id="15101-546">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-546">Attribute Name</span></span>          | <span data-ttu-id="15101-547">必須</span><span class="sxs-lookup"><span data-stu-id="15101-547">Is Required</span></span> | <span data-ttu-id="15101-548">値</span><span class="sxs-lookup"><span data-stu-id="15101-548">Value</span></span>                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-549">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="15101-549">**StoreEntitySet**</span></span>      | <span data-ttu-id="15101-550">はい</span><span class="sxs-lookup"><span data-stu-id="15101-550">Yes</span></span>         | <span data-ttu-id="15101-551">マップされるテーブルまたはビューの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-551">The name of the table or view that is being mapped.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="15101-552">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="15101-552">**MakeColumnsDistinct**</span></span> | <span data-ttu-id="15101-553">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-553">No</span></span>          | <span data-ttu-id="15101-554">個別の行のみが返されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="15101-554">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="15101-555">この属性が**True**に設定されている場合は、EntityContainerMapping 要素の**generateupdateviews**属性が**False**に設定されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-555">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

### <a name="example"></a><span data-ttu-id="15101-556">例</span><span class="sxs-lookup"><span data-stu-id="15101-556">Example</span></span>

<span data-ttu-id="15101-557">次の例では、 **Entitytypemapping**要素の子として**mappingfragment**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-557">The following example shows a **MappingFragment** element as the child of an **EntityTypeMapping** element.</span></span> <span data-ttu-id="15101-558">この例では、概念モデルの**コース**の種類のプロパティは、データベース内の**course**テーブルの列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="15101-558">In this example, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="15101-559">例</span><span class="sxs-lookup"><span data-stu-id="15101-559">Example</span></span>

<span data-ttu-id="15101-560">次の例は、 **EntitySetMapping**要素の子としての**mappingfragment**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-560">The following example shows a **MappingFragment** element as the child of an **EntitySetMapping** element.</span></span> <span data-ttu-id="15101-561">上の例のように、概念モデルの**コース**の種類のプロパティは、データベース内の**course**テーブルの列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="15101-561">As in the example above, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

``` xml
 <EntitySetMapping Name="Courses" TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
 </EntitySetMapping>
```

## <a name="modificationfunctionmapping-element-msl"></a><span data-ttu-id="15101-562">ModificationFunctionMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-562">ModificationFunctionMapping Element (MSL)</span></span>

<span data-ttu-id="15101-563">マッピング仕様言語 (MSL) の**ModificationFunctionMapping**要素は、概念モデルのエンティティ型の挿入、更新、および削除の各関数を、基になるデータベースのストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-563">The **ModificationFunctionMapping** element in mapping specification language (MSL) maps the insert, update, and delete functions of a conceptual model entity type to stored procedures in the underlying database.</span></span> <span data-ttu-id="15101-564">**ModificationFunctionMapping**要素は、概念モデル内の多対多のアソシエーションの挿入関数と削除関数を、基になるデータベースのストアドプロシージャにマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="15101-564">The **ModificationFunctionMapping** element can also map the insert and delete functions for many-to-many associations in the conceptual model to stored procedures in the underlying database.</span></span> <span data-ttu-id="15101-565">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-565">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="15101-566">詳細については、「Function 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-566">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="15101-567">エンティティ型の3つの挿入、更新、または削除操作をすべてストアドプロシージャにマップしない場合、実行時にマップされていない操作は失敗し、UpdateException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="15101-567">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>


> [!NOTE]
> <span data-ttu-id="15101-568">継承階層の 1 つのエンティティの変更関数をストアド プロシージャにマップした場合、階層内のすべての型の変更関数がストアド プロシージャにマップされる必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-568">If the modification functions for one entity in an inheritance hierarchy are mapped to stored procedures, then modification functions for all types in the hierarchy must be mapped to stored procedures.</span></span>

<span data-ttu-id="15101-569">**ModificationFunctionMapping**要素は、entitytypemapping 要素または AssociationSetMapping 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-569">The **ModificationFunctionMapping** element can be a child of the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

<span data-ttu-id="15101-570">**ModificationFunctionMapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-570">The **ModificationFunctionMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="15101-571">DeleteFunction (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="15101-571">DeleteFunction (zero or one)</span></span>
-   <span data-ttu-id="15101-572">InsertFunction (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="15101-572">InsertFunction (zero or one)</span></span>
-   <span data-ttu-id="15101-573">UpdateFunction (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="15101-573">UpdateFunction (zero or one)</span></span>

<span data-ttu-id="15101-574">**ModificationFunctionMapping**要素に適用できる属性はありません。</span><span class="sxs-lookup"><span data-stu-id="15101-574">No attributes are applicable to the **ModificationFunctionMapping** element.</span></span>

### <a name="example"></a><span data-ttu-id="15101-575">例</span><span class="sxs-lookup"><span data-stu-id="15101-575">Example</span></span>

<span data-ttu-id="15101-576">次の例は、School モデルの**People**エンティティセットのエンティティセットマッピングを示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-576">The following example shows the entity set mapping for the **People** entity set in the School model.</span></span> <span data-ttu-id="15101-577">**Person**エンティティ型の列マッピングに加えて、 **person**型の挿入関数、更新関数、および削除関数のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="15101-577">In addition to the column mapping for the **Person** entity type, the mapping of the insert, update, and delete functions of the **Person** type are shown.</span></span> <span data-ttu-id="15101-578">ストレージ モデルにマップされて宣言されている関数。</span><span class="sxs-lookup"><span data-stu-id="15101-578">The functions that are mapped to are declared in the storage model.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="15101-579">例</span><span class="sxs-lookup"><span data-stu-id="15101-579">Example</span></span>

<span data-ttu-id="15101-580">次の例は、School モデルの**CourseInstructor** association セットのアソシエーションセットマッピングを示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-580">The following example shows the association set mapping for the **CourseInstructor** association set in the School model.</span></span> <span data-ttu-id="15101-581">**CourseInstructor** association の列マッピングに加えて、 **CourseInstructor**アソシエーションの挿入関数と削除関数のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="15101-581">In addition to the column mapping for the **CourseInstructor** association, the mapping of the insert and delete functions of the **CourseInstructor** association are shown.</span></span> <span data-ttu-id="15101-582">ストレージ モデルにマップされて宣言されている関数。</span><span class="sxs-lookup"><span data-stu-id="15101-582">The functions that are mapped to are declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```
 

 

## <a name="queryview-element-msl"></a><span data-ttu-id="15101-583">QueryView 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-583">QueryView Element (MSL)</span></span>

<span data-ttu-id="15101-584">マッピング仕様言語 (MSL) の**QueryView**要素は、概念モデルのエンティティ型またはアソシエーションと基になるデータベース内のテーブルとの間の読み取り専用のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-584">The **QueryView** element in mapping specification language (MSL) defines a read-only mapping between an entity type or association in the conceptual model and a table in the underlying database.</span></span> <span data-ttu-id="15101-585">マッピングは、ストレージモデルに対して評価される Entity SQL クエリで定義され、結果セットを概念モデルのエンティティまたはアソシエーションの観点から表現します。</span><span class="sxs-lookup"><span data-stu-id="15101-585">The mapping is defined with an Entity SQL query that is evaluated against the storage model, and you express the result set in terms of an entity or association in the conceptual model.</span></span> <span data-ttu-id="15101-586">クエリ ビューは読み取り専用であるため、標準の更新コマンドを使用してクエリ ビューで定義された型を更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="15101-586">Because query views are read-only, you cannot use standard update commands to update types that are defined by query views.</span></span> <span data-ttu-id="15101-587">型の更新を行うには、変更関数を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-587">You can make updates to these types by using modification functions.</span></span> <span data-ttu-id="15101-588">詳細については、「方法: 変更関数をストアドプロシージャにマップする」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-588">For more information, see How to: Map Modification Functions to Stored Procedures.</span></span>

> [!NOTE]
> <span data-ttu-id="15101-589">**QueryView**要素では、 **GroupBy**、group 集計、またはナビゲーションプロパティを含む Entity SQL 式はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="15101-589">In the **QueryView** element, Entity SQL expressions that contain **GroupBy**, group aggregates, or navigation properties are not supported.</span></span>

 

<span data-ttu-id="15101-590">**QueryView**要素は、EntitySetMapping 要素または AssociationSetMapping 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-590">The **QueryView** element can be a child of the EntitySetMapping element or the AssociationSetMapping element.</span></span> <span data-ttu-id="15101-591">前者の場合、クエリ ビューは、概念モデルのエンティティの読み取り専用マッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-591">In the former case, the query view defines a read-only mapping for an entity in the conceptual model.</span></span> <span data-ttu-id="15101-592">後者の場合、クエリ ビューは、概念モデルのアソシエーションの読み取り専用マッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-592">In the latter case, the query view defines a read-only mapping for an association in the conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="15101-593">**AssociationSetMapping**要素が参照制約との関連付けの場合、 **AssociationSetMapping**要素は無視されます。</span><span class="sxs-lookup"><span data-stu-id="15101-593">If the **AssociationSetMapping** element is for an association with a referential constraint, the **AssociationSetMapping** element is ignored.</span></span> <span data-ttu-id="15101-594">詳細については、「参照要素の参照」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-594">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="15101-595">**QueryView**要素には、子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="15101-595">The **QueryView** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-596">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-596">Applicable Attributes</span></span>

<span data-ttu-id="15101-597">次の表では、 **QueryView**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-597">The following table describes the attributes that can be applied to the **QueryView** element.</span></span>

| <span data-ttu-id="15101-598">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-598">Attribute Name</span></span> | <span data-ttu-id="15101-599">必須</span><span class="sxs-lookup"><span data-stu-id="15101-599">Is Required</span></span> | <span data-ttu-id="15101-600">値</span><span class="sxs-lookup"><span data-stu-id="15101-600">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="15101-601">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="15101-601">**TypeName**</span></span>   | <span data-ttu-id="15101-602">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-602">No</span></span>          | <span data-ttu-id="15101-603">クエリ ビューによってマップされる概念モデル型の名前。</span><span class="sxs-lookup"><span data-stu-id="15101-603">The name of the conceptual model type that is being mapped by the query view.</span></span> |

### <a name="example"></a><span data-ttu-id="15101-604">例</span><span class="sxs-lookup"><span data-stu-id="15101-604">Example</span></span>

<span data-ttu-id="15101-605">次の例では、 **EntitySetMapping**要素の子として**QueryView**要素を示し、School モデルの**Department**エンティティ型に対するクエリビューマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-605">The following example shows the **QueryView** element as a child of the **EntitySetMapping** element and defines a query view mapping for the **Department** entity type in the School Model.</span></span>

``` xml
 <EntitySetMapping Name="Departments" >
   <QueryView>
     SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                         d.Name,
                                         d.Budget,
                                         d.StartDate)
     FROM SchoolModelStoreContainer.Department AS d
     WHERE d.Budget > 150000
   </QueryView>
 </EntitySetMapping>
```

<span data-ttu-id="15101-606">このクエリでは、ストレージモデルの**department**型のメンバーのサブセットのみが返されるため、School モデルの**学科**の種類は、このマッピングに基づいて次のように変更されています。</span><span class="sxs-lookup"><span data-stu-id="15101-606">Because the query only returns a subset of the members of the **Department** type in the storage model, the **Department** type in the School model has been modified based on this mapping as follows:</span></span>

``` xml
 <EntityType Name="Department">
   <Key>
     <PropertyRef Name="DepartmentID" />
   </Key>
   <Property Type="Int32" Name="DepartmentID" Nullable="false" />
   <Property Type="String" Name="Name" Nullable="false"
             MaxLength="50" FixedLength="false" Unicode="true" />
   <Property Type="Decimal" Name="Budget" Nullable="false"
             Precision="19" Scale="4" />
   <Property Type="DateTime" Name="StartDate" Nullable="false" />
   <NavigationProperty Name="Courses"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Department" ToRole="Course" />
 </EntityType>
```

### <a name="example"></a><span data-ttu-id="15101-607">例</span><span class="sxs-lookup"><span data-stu-id="15101-607">Example</span></span>

<span data-ttu-id="15101-608">次の例では、 **AssociationSetMapping**要素の子として**QueryView**要素を示し、School モデルの `FK_Course_Department` アソシエーションの読み取り専用マッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-608">The next example shows the **QueryView** element as the child of an **AssociationSetMapping** element and defines a read-only mapping for the `FK_Course_Department` association in the School model.</span></span>

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolEntities">
   <EntitySetMapping Name="Courses" >
     <QueryView>
       SELECT VALUE SchoolModel.Course(c.CourseID,
                                       c.Title,
                                       c.Credits)
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments" >
     <QueryView>
       SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                           d.Name,
                                           d.Budget,
                                           d.StartDate)
       FROM SchoolModelStoreContainer.Department AS d
       WHERE d.Budget > 150000
     </QueryView>
   </EntitySetMapping>
   <AssociationSetMapping Name="FK_Course_Department" >
     <QueryView>
       SELECT VALUE SchoolModel.FK_Course_Department(
         CREATEREF(SchoolEntities.Departments, row(c.DepartmentID), SchoolModel.Department),
         CREATEREF(SchoolEntities.Courses, row(c.CourseID)) )
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </AssociationSetMapping>
 </EntityContainerMapping>
```
 
### <a name="comments"></a><span data-ttu-id="15101-609">コメント</span><span class="sxs-lookup"><span data-stu-id="15101-609">Comments</span></span>

<span data-ttu-id="15101-610">クエリ ビューを定義して、次のシナリオを実現できます。</span><span class="sxs-lookup"><span data-stu-id="15101-610">You can define query views to enable the following scenarios:</span></span>

-   <span data-ttu-id="15101-611">ストレージ モデルのエンティティのプロパティの一部を含まない、概念モデルのエンティティを定義する。</span><span class="sxs-lookup"><span data-stu-id="15101-611">Define an entity in the conceptual model that doesn't include all the properties of the entity in the storage model.</span></span> <span data-ttu-id="15101-612">これには、既定値を持たず、 **null**値をサポートしていないプロパティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="15101-612">This includes properties that do not have default values and do not support **null** values.</span></span>
-   <span data-ttu-id="15101-613">ストレージ モデルの計算列を概念モデルのエンティティ型のプロパティにマップする。</span><span class="sxs-lookup"><span data-stu-id="15101-613">Map computed columns in the storage model to properties of entity types in the conceptual model.</span></span>
-   <span data-ttu-id="15101-614">概念モデルのエンティティをパーティション分割するために使用される条件が等価性に基づかないマッピングを定義する。</span><span class="sxs-lookup"><span data-stu-id="15101-614">Define a mapping where conditions used to partition entities in the conceptual model are not based on equality.</span></span> <span data-ttu-id="15101-615">**Condition**要素を使用して条件付きマッピングを指定する場合、指定された条件は指定した値と同じである必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-615">When you specify a conditional mapping using the **Condition** element, the supplied condition must equal the specified value.</span></span> <span data-ttu-id="15101-616">詳細については、「Condition 要素 (MSL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-616">For more information, see Condition Element (MSL).</span></span>
-   <span data-ttu-id="15101-617">ストレージ モデルの同一の列を概念モデルの複数の型にマップする。</span><span class="sxs-lookup"><span data-stu-id="15101-617">Map the same column in the storage model to multiple types in the conceptual model.</span></span>
-   <span data-ttu-id="15101-618">複数の型を同じテーブルにマップする。</span><span class="sxs-lookup"><span data-stu-id="15101-618">Map multiple types to the same table.</span></span>
-   <span data-ttu-id="15101-619">リレーショナル スキーマの外部キーに基づかない概念モデルのアソシエーションを定義する。</span><span class="sxs-lookup"><span data-stu-id="15101-619">Define associations in the conceptual model that are not based on foreign keys in the relational schema.</span></span>
-   <span data-ttu-id="15101-620">カスタム ビジネス ロジックを使用して、概念モデルのプロパティの値を設定する。</span><span class="sxs-lookup"><span data-stu-id="15101-620">Use custom business logic to set the value of properties in the conceptual model.</span></span> <span data-ttu-id="15101-621">たとえば、概念モデルでは、データソースの文字列値 "T" をブール値の**true**にマップできます。</span><span class="sxs-lookup"><span data-stu-id="15101-621">For example, you could map the string value "T" in the data source to a value of **true**, a Boolean, in the conceptual model.</span></span>
-   <span data-ttu-id="15101-622">クエリ結果に対する条件付きフィルターを定義する。</span><span class="sxs-lookup"><span data-stu-id="15101-622">Define conditional filters for query results.</span></span>
-   <span data-ttu-id="15101-623">概念モデルのデータに適用する制限をストレージ モデルよりも少なくする。</span><span class="sxs-lookup"><span data-stu-id="15101-623">Enforce fewer restrictions on data in the conceptual model than in the storage model.</span></span> <span data-ttu-id="15101-624">たとえば、概念モデルのプロパティを、マップ先の列で**null**値がサポートされていない場合でも、null 許容にすることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-624">For example, you could make a property in the conceptual model nullable even if the column to which it is mapped does not support **null**values.</span></span>

<span data-ttu-id="15101-625">エンティティのクエリ ビューを定義する際は、次の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="15101-625">The following considerations apply when you define query views for entities:</span></span>

-   <span data-ttu-id="15101-626">クエリ ビューは読み取り専用です。</span><span class="sxs-lookup"><span data-stu-id="15101-626">Query views are read-only.</span></span> <span data-ttu-id="15101-627">エンティティの更新を行うには、変更関数を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-627">You can only make updates to entities by using modification functions.</span></span>
-   <span data-ttu-id="15101-628">クエリ ビューでエンティティ型を定義する場合、すべての関連エンティティもクエリ ビューで定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-628">When you define an entity type by a query view, you must also define all related entities by query views.</span></span>
-   <span data-ttu-id="15101-629">リレーショナルスキーマのリンクテーブルを表すストレージモデル内のエンティティに多対多のアソシエーションをマップする場合は、このリンクテーブルの**AssociationSetMapping**要素に**QueryView**要素を定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-629">When you map a many-to-many association to an entity in the storage model that represents a link table in the relational schema, you must define a **QueryView** element in the **AssociationSetMapping** element for this link table.</span></span>
-   <span data-ttu-id="15101-630">クエリ ビューは、型階層のすべての型に対して定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-630">Query views must be defined for all types in a type hierarchy.</span></span> <span data-ttu-id="15101-631">これは、次の方法で行うことができます。</span><span class="sxs-lookup"><span data-stu-id="15101-631">You can do this in the following ways:</span></span>
-   -   <span data-ttu-id="15101-632">1つの**QueryView**要素を使用して、階層内のすべてのエンティティ型の和集合を返す単一の Entity SQL クエリを指定します。</span><span class="sxs-lookup"><span data-stu-id="15101-632">With a single **QueryView** element that specifies a single Entity SQL query that returns a union of all of the entity types in the hierarchy.</span></span>
    -   <span data-ttu-id="15101-633">1つの**QueryView**要素で、CASE 演算子を使用して特定の条件に基づいて階層内の特定のエンティティ型を返す単一の Entity SQL クエリを指定します。</span><span class="sxs-lookup"><span data-stu-id="15101-633">With a single **QueryView** element that specifies a single Entity SQL query that uses the CASE operator to return a specific entity type in the hierarchy based on a specific condition.</span></span>
    -   <span data-ttu-id="15101-634">階層内の特定の型に対して追加の**QueryView**要素を使用します。</span><span class="sxs-lookup"><span data-stu-id="15101-634">With an additional **QueryView** element for a specific type in the hierarchy.</span></span> <span data-ttu-id="15101-635">この場合は、 **QueryView**要素の**TypeName**属性を使用して、各ビューのエンティティ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="15101-635">In this case, use the **TypeName** attribute of the **QueryView** element to specify the entity type for each view.</span></span>
-   <span data-ttu-id="15101-636">クエリビューが定義されている場合、 **EntitySetMapping**要素で**StorageSetName**属性を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="15101-636">When a query view is defined, you cannot specify the **StorageSetName** attribute on the **EntitySetMapping** element.</span></span>
-   <span data-ttu-id="15101-637">クエリビューが定義されている場合、 **EntitySetMapping**要素に**プロパティ**マッピングを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="15101-637">When a query view is defined, the **EntitySetMapping**element cannot also contain **Property** mappings.</span></span>

## <a name="resultbinding-element-msl"></a><span data-ttu-id="15101-638">ResultBinding 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-638">ResultBinding Element (MSL)</span></span>

<span data-ttu-id="15101-639">マッピングスキーマ言語 (MSL) の**Resultbinding**要素は、エンティティ型の変更関数が基になるデータベースのストアドプロシージャにマップされている場合に、ストアドプロシージャによって返される列の値を概念モデルのエンティティプロパティにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-639">The **ResultBinding** element in mapping specification language (MSL) maps column values that are returned by stored procedures to entity properties in the conceptual model when entity type modification functions are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="15101-640">たとえば、id 列の値が insert ストアドプロシージャによって返される場合、 **Resultbinding**要素は、返された値を概念モデルのエンティティ型プロパティにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-640">For example, when the value of an identity column is returned by an insert stored procedure, the **ResultBinding** element maps the returned value to an entity type property in the conceptual model.</span></span>

<span data-ttu-id="15101-641">**Resultbinding**要素は、insertfunction 要素または updatefunction 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-641">The **ResultBinding** element can be child of the InsertFunction element or the UpdateFunction element.</span></span>

<span data-ttu-id="15101-642">**Resultbinding**要素に子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="15101-642">The **ResultBinding** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-643">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-643">Applicable Attributes</span></span>

<span data-ttu-id="15101-644">次の表では、 **Resultbinding**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-644">The following table describes the attributes that are applicable to the **ResultBinding** element:</span></span>

| <span data-ttu-id="15101-645">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-645">Attribute Name</span></span> | <span data-ttu-id="15101-646">必須</span><span class="sxs-lookup"><span data-stu-id="15101-646">Is Required</span></span> | <span data-ttu-id="15101-647">値</span><span class="sxs-lookup"><span data-stu-id="15101-647">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="15101-648">**名前**</span><span class="sxs-lookup"><span data-stu-id="15101-648">**Name**</span></span>       | <span data-ttu-id="15101-649">はい</span><span class="sxs-lookup"><span data-stu-id="15101-649">Yes</span></span>         | <span data-ttu-id="15101-650">マップされる概念モデルのエンティティ プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-650">The name of the entity property in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="15101-651">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="15101-651">**ColumnName**</span></span> | <span data-ttu-id="15101-652">はい</span><span class="sxs-lookup"><span data-stu-id="15101-652">Yes</span></span>         | <span data-ttu-id="15101-653">マップされる列の名前。</span><span class="sxs-lookup"><span data-stu-id="15101-653">The name of the column being mapped.</span></span>                                          |

### <a name="example"></a><span data-ttu-id="15101-654">例</span><span class="sxs-lookup"><span data-stu-id="15101-654">Example</span></span>

<span data-ttu-id="15101-655">次の例は、School モデルに基づいており、 **Person**エンティティ型の挿入関数を**insertfunction**ストアドプロシージャにマップするために使用される**insertfunction**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-655">The following example is based on the School model and shows an **InsertFunction** element used to map the insert function of the **Person** entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="15101-656">( **Insertperson**ストアドプロシージャは次のようになり、ストレージモデルで宣言されています)。**Resultbinding**要素は、ストアドプロシージャ (**NewPersonID**) によって返される列の値をエンティティ型のプロパティ (**PersonID**) にマップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="15101-656">(The **InsertPerson** stored procedure is shown below and is declared in the storage model.) A **ResultBinding** element is used to map a column value that is returned by the stored procedure (**NewPersonID**) to an entity type property (**PersonID**).</span></span>

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```

<span data-ttu-id="15101-657">次の Transact-sql では、 **Insertperson**ストアドプロシージャについて説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-657">The following Transact-SQL describes the **InsertPerson** stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[InsertPerson]
                                @LastName nvarchar(50),
                                @FirstName nvarchar(50),
                                @HireDate datetime,
                                @EnrollmentDate datetime
                                AS
                                INSERT INTO dbo.Person (LastName,
                                                                             FirstName,
                                                                             HireDate,
                                                                             EnrollmentDate)
                                VALUES (@LastName,
                                               @FirstName,
                                               @HireDate,
                                               @EnrollmentDate);
                                SELECT SCOPE_IDENTITY() as NewPersonID;
```

## <a name="resultmapping-element-msl"></a><span data-ttu-id="15101-658">ResultMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-658">ResultMapping Element (MSL)</span></span>

<span data-ttu-id="15101-659">マッピングスキーマ言語 (MSL) の**Resultmapping**要素は、次の条件に該当する場合に、概念モデルの関数インポートと基になるデータベースのストアドプロシージャとの間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="15101-659">The **ResultMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="15101-660">関数インポートが概念モデルのエンティティ型または複合型を返す場合</span><span class="sxs-lookup"><span data-stu-id="15101-660">The function import returns a conceptual model entity type or complex type.</span></span>
-   <span data-ttu-id="15101-661">ストアド プロシージャが返す列の名前が、エンティティ型または複合型のプロパティの名前と完全には一致しない場合</span><span class="sxs-lookup"><span data-stu-id="15101-661">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the entity type or complex type.</span></span>

<span data-ttu-id="15101-662">既定では、ストアド プロシージャが返す列とエンティティ型または複合型とのマッピングは、列およびプロパティの名前に基づいています。</span><span class="sxs-lookup"><span data-stu-id="15101-662">By default, the mapping between the columns returned by a stored procedure and an entity type or complex type is based on column and property names.</span></span> <span data-ttu-id="15101-663">列名がプロパティ名と完全に一致しない場合は、 **resultmapping**要素を使用してマッピングを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-663">If column names do not exactly match property names, you must use the **ResultMapping** element to define the mapping.</span></span> <span data-ttu-id="15101-664">既定のマッピングの例については、「FunctionImportMapping 要素 (MSL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-664">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="15101-665">**Resultmapping**要素は、FunctionImportMapping 要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="15101-665">The **ResultMapping** element is a child element of the FunctionImportMapping element.</span></span>

<span data-ttu-id="15101-666">**Resultmapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-666">The **ResultMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="15101-667">EntityTypeMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-667">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="15101-668">ComplexTypeMapping</span><span class="sxs-lookup"><span data-stu-id="15101-668">ComplexTypeMapping</span></span>

<span data-ttu-id="15101-669">**Resultmapping**要素に適用できる属性はありません。</span><span class="sxs-lookup"><span data-stu-id="15101-669">No attributes are applicable to the **ResultMapping** Element.</span></span>

### <a name="example"></a><span data-ttu-id="15101-670">例</span><span class="sxs-lookup"><span data-stu-id="15101-670">Example</span></span>

<span data-ttu-id="15101-671">次のストアド プロシージャを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="15101-671">Consider the following stored procedure:</span></span>

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

<span data-ttu-id="15101-672">また、次の概念モデルのエンティティ型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="15101-672">Also consider the following conceptual model entity type:</span></span>

``` xml
 <EntityType Name="StudentGrade">
   <Key>
     <PropertyRef Name="EnrollmentID" />
   </Key>
   <Property Name="EnrollmentID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="CourseID" Type="Int32" Nullable="false" />
   <Property Name="StudentID" Type="Int32" Nullable="false" />
   <Property Name="Grade" Type="Decimal" Precision="3" Scale="2" />
 </EntityType>
```

<span data-ttu-id="15101-673">以前のエンティティ型のインスタンスを返す関数インポートを作成するには、ストアドプロシージャによって返された列とエンティティ型の間のマッピングが**Resultmapping**要素で定義されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-673">In order to create a function import that returns instances of the previous entity type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ResultMapping** element:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <EntityTypeMapping TypeName="SchoolModel.StudentGrade">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </EntityTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="scalarproperty-element-msl"></a><span data-ttu-id="15101-674">ScalarProperty 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-674">ScalarProperty Element (MSL)</span></span>

<span data-ttu-id="15101-675">マッピング仕様言語 (MSL) の**ScalarProperty**要素は、概念モデルのエンティティ型、複合型、またはアソシエーションのプロパティを、基になるデータベースのテーブル列またはストアドプロシージャパラメーターにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-675">The **ScalarProperty** element in mapping specification language (MSL) maps a property on a conceptual model entity type, complex type, or association to a table column or stored procedure parameter in the underlying database.</span></span>

> [!NOTE]
> <span data-ttu-id="15101-676">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-676">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="15101-677">詳細については、「Function 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-677">For more information, see Function Element (SSDL).</span></span>

<span data-ttu-id="15101-678">**ScalarProperty**要素は、次の要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-678">The **ScalarProperty** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="15101-679">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="15101-679">MappingFragment</span></span>
-   <span data-ttu-id="15101-680">InsertFunction</span><span class="sxs-lookup"><span data-stu-id="15101-680">InsertFunction</span></span>
-   <span data-ttu-id="15101-681">UpdateFunction</span><span class="sxs-lookup"><span data-stu-id="15101-681">UpdateFunction</span></span>
-   <span data-ttu-id="15101-682">DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="15101-682">DeleteFunction</span></span>
-   <span data-ttu-id="15101-683">EndProperty</span><span class="sxs-lookup"><span data-stu-id="15101-683">EndProperty</span></span>
-   <span data-ttu-id="15101-684">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="15101-684">ComplexProperty</span></span>
-   <span data-ttu-id="15101-685">ResultMapping</span><span class="sxs-lookup"><span data-stu-id="15101-685">ResultMapping</span></span>

<span data-ttu-id="15101-686">**Mappingfragment**、 **complexproperty**、または**endproperty**要素の子として、 **ScalarProperty**要素は、概念モデルのプロパティをデータベースの列にマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-686">As a child of the **MappingFragment**, **ComplexProperty**, or **EndProperty** element, the **ScalarProperty** element maps a property in the conceptual model to a column in the database.</span></span> <span data-ttu-id="15101-687">**Insertfunction**、 **updatefunction**、または**deletefunction**要素の子として、 **ScalarProperty**要素は、概念モデルのプロパティをストアドプロシージャパラメーターにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-687">As a child of the **InsertFunction**, **UpdateFunction**, or **DeleteFunction** element, the **ScalarProperty** element maps a property in the conceptual model to a stored procedure parameter.</span></span>

<span data-ttu-id="15101-688">**ScalarProperty**要素には、子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="15101-688">The **ScalarProperty** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-689">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-689">Applicable Attributes</span></span>

<span data-ttu-id="15101-690">**ScalarProperty**要素に適用される属性は、要素の役割によって異なります。</span><span class="sxs-lookup"><span data-stu-id="15101-690">The attributes that apply to the **ScalarProperty** element differ depending on the role of the element.</span></span>

<span data-ttu-id="15101-691">次の表では、 **ScalarProperty**要素を使用して、概念モデルのプロパティをデータベースの列にマップするときに適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-691">The following table describes the attributes that are applicable when the **ScalarProperty** element is used to map a conceptual model property to a column in the database:</span></span>

| <span data-ttu-id="15101-692">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-692">Attribute Name</span></span> | <span data-ttu-id="15101-693">必須</span><span class="sxs-lookup"><span data-stu-id="15101-693">Is Required</span></span> | <span data-ttu-id="15101-694">値</span><span class="sxs-lookup"><span data-stu-id="15101-694">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="15101-695">**名前**</span><span class="sxs-lookup"><span data-stu-id="15101-695">**Name**</span></span>       | <span data-ttu-id="15101-696">はい</span><span class="sxs-lookup"><span data-stu-id="15101-696">Yes</span></span>         | <span data-ttu-id="15101-697">マップされる概念モデルのプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-697">The name of the conceptual model property that is being mapped.</span></span> |
| <span data-ttu-id="15101-698">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="15101-698">**ColumnName**</span></span> | <span data-ttu-id="15101-699">はい</span><span class="sxs-lookup"><span data-stu-id="15101-699">Yes</span></span>         | <span data-ttu-id="15101-700">マップされるテーブル列の名前。</span><span class="sxs-lookup"><span data-stu-id="15101-700">The name of the table column that is being mapped.</span></span>              |

<span data-ttu-id="15101-701">次の表では、 **ScalarProperty**要素を使用して、概念モデルのプロパティをストアドプロシージャのパラメーターにマップするときに適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-701">The following table describes the attributes that are applicable to the **ScalarProperty** element when it is used to map a conceptual model property to a stored procedure parameter:</span></span>

| <span data-ttu-id="15101-702">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-702">Attribute Name</span></span>    | <span data-ttu-id="15101-703">必須</span><span class="sxs-lookup"><span data-stu-id="15101-703">Is Required</span></span> | <span data-ttu-id="15101-704">値</span><span class="sxs-lookup"><span data-stu-id="15101-704">Value</span></span>                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-705">**名前**</span><span class="sxs-lookup"><span data-stu-id="15101-705">**Name**</span></span>          | <span data-ttu-id="15101-706">はい</span><span class="sxs-lookup"><span data-stu-id="15101-706">Yes</span></span>         | <span data-ttu-id="15101-707">マップされる概念モデルのプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-707">The name of the conceptual model property that is being mapped.</span></span>                                                                                 |
| <span data-ttu-id="15101-708">**ParameterName**</span><span class="sxs-lookup"><span data-stu-id="15101-708">**ParameterName**</span></span> | <span data-ttu-id="15101-709">はい</span><span class="sxs-lookup"><span data-stu-id="15101-709">Yes</span></span>         | <span data-ttu-id="15101-710">マップされるパラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-710">The name of the parameter that is being mapped.</span></span>                                                                                                 |
| <span data-ttu-id="15101-711">**バージョン**</span><span class="sxs-lookup"><span data-stu-id="15101-711">**Version**</span></span>       | <span data-ttu-id="15101-712">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-712">No</span></span>          | <span data-ttu-id="15101-713">現在の値またはプロパティの元の値を同時実行チェックに使用するかどうかによって、 **current**または**original** 。</span><span class="sxs-lookup"><span data-stu-id="15101-713">**Current** or **Original** depending on whether the current value or the original value of the property should be used for concurrency checks.</span></span> |

### <a name="example"></a><span data-ttu-id="15101-714">例</span><span class="sxs-lookup"><span data-stu-id="15101-714">Example</span></span>

<span data-ttu-id="15101-715">次の例は、2つの方法で使用される**ScalarProperty**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-715">The following example shows the **ScalarProperty** element used in two ways:</span></span>

-   <span data-ttu-id="15101-716">**Person エンティティ型**のプロパティを**person**テーブルの列にマップする。</span><span class="sxs-lookup"><span data-stu-id="15101-716">To map the properties of the **Person** entity type to the columns of the **Person**table.</span></span>
-   <span data-ttu-id="15101-717">**Person**エンティティ型のプロパティを**updateperson**ストアドプロシージャのパラメーターにマップする。</span><span class="sxs-lookup"><span data-stu-id="15101-717">To map the properties of the **Person** entity type to the parameters of the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="15101-718">ストアド プロシージャはストレージ モデル内で宣言されます。</span><span class="sxs-lookup"><span data-stu-id="15101-718">The stored procedures are declared in the storage model.</span></span>

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a><span data-ttu-id="15101-719">例</span><span class="sxs-lookup"><span data-stu-id="15101-719">Example</span></span>

<span data-ttu-id="15101-720">次の例は、概念モデルのアソシエーションの挿入関数と削除関数をデータベース内のストアドプロシージャにマップするために使用される**ScalarProperty**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-720">The next example shows the **ScalarProperty** element used to map the insert and delete functions of a conceptual model association to stored procedures in the database.</span></span> <span data-ttu-id="15101-721">ストアド プロシージャはストレージ モデル内で宣言されます。</span><span class="sxs-lookup"><span data-stu-id="15101-721">The stored procedures are declared in the storage model.</span></span>

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="updatefunction-element-msl"></a><span data-ttu-id="15101-722">UpdateFunction 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="15101-722">UpdateFunction Element (MSL)</span></span>

<span data-ttu-id="15101-723">マッピングスキーマ言語 (MSL) の**updatefunction**要素は、概念モデルのエンティティ型の更新関数を、基になるデータベースのストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="15101-723">The **UpdateFunction** element in mapping specification language (MSL) maps the update function of an entity type in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="15101-724">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-724">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="15101-725">詳細については、「Function 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="15101-725">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
>  <span data-ttu-id="15101-726">エンティティ型の3つの挿入、更新、または削除操作をすべてストアドプロシージャにマップしない場合、実行時にマップされていない操作は失敗し、UpdateException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="15101-726">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="15101-727">**Updatefunction**要素は ModificationFunctionMapping 要素の子にすることができ、entitytypemapping 要素に適用されます。</span><span class="sxs-lookup"><span data-stu-id="15101-727">The **UpdateFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element.</span></span>

<span data-ttu-id="15101-728">**Updatefunction**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="15101-728">The **UpdateFunction** element can have the following child elements:</span></span>

-   <span data-ttu-id="15101-729">AssociationEnd (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-729">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="15101-730">ComplexProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-730">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="15101-731">ResultBinding (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="15101-731">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="15101-732">ScarlarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="15101-732">ScarlarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="15101-733">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="15101-733">Applicable Attributes</span></span>

<span data-ttu-id="15101-734">次の表では、 **Updatefunction**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="15101-734">The following table describes the attributes that can be applied to the **UpdateFunction** element.</span></span>

| <span data-ttu-id="15101-735">属性名</span><span class="sxs-lookup"><span data-stu-id="15101-735">Attribute Name</span></span>            | <span data-ttu-id="15101-736">必須</span><span class="sxs-lookup"><span data-stu-id="15101-736">Is Required</span></span> | <span data-ttu-id="15101-737">値</span><span class="sxs-lookup"><span data-stu-id="15101-737">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="15101-738">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="15101-738">**FunctionName**</span></span>          | <span data-ttu-id="15101-739">はい</span><span class="sxs-lookup"><span data-stu-id="15101-739">Yes</span></span>         | <span data-ttu-id="15101-740">更新関数のマップ先となるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="15101-740">The namespace-qualified name of the stored procedure to which the update function is mapped.</span></span> <span data-ttu-id="15101-741">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="15101-741">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="15101-742">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="15101-742">**RowsAffectedParameter**</span></span> | <span data-ttu-id="15101-743">いいえ</span><span class="sxs-lookup"><span data-stu-id="15101-743">No</span></span>          | <span data-ttu-id="15101-744">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="15101-744">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

### <a name="example"></a><span data-ttu-id="15101-745">例</span><span class="sxs-lookup"><span data-stu-id="15101-745">Example</span></span>

<span data-ttu-id="15101-746">次の例は、School モデルに基づいており、 **Person**エンティティ型の update 関数を**updatefunction**ストアドプロシージャにマップするために使用される**updatefunction**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="15101-746">The following example is based on the School model and shows the **UpdateFunction** element used to map update function of the **Person** entity type to the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="15101-747">**Updateperson**ストアドプロシージャは、ストレージモデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="15101-747">The **UpdatePerson** stored procedure is declared in the storage model.</span></span>

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
