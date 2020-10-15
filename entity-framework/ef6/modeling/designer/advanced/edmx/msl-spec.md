---
title: MSL 仕様-EF6
description: Entity Framework 6 での MSL 仕様
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/msl-spec
ms.openlocfilehash: 0780a549d4cbb60bf84bbed927d52588e99148e1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066344"
---
# <a name="msl-specification"></a><span data-ttu-id="3a761-103">MSL 仕様</span><span class="sxs-lookup"><span data-stu-id="3a761-103">MSL Specification</span></span>
<span data-ttu-id="3a761-104">マッピング仕様言語 (MSL) は、Entity Framework アプリケーションの概念モデルとストレージモデルの間のマッピングを記述する XML ベースの言語です。</span><span class="sxs-lookup"><span data-stu-id="3a761-104">Mapping specification language (MSL) is an XML-based language that describes the mapping between the conceptual model and storage model of an Entity Framework application.</span></span>

<span data-ttu-id="3a761-105">Entity Framework アプリケーションでは、マッピングメタデータはビルド時に (MSL で記述された) .msl ファイルから読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="3a761-105">In an Entity Framework application, mapping metadata is loaded from an .msl file (written in MSL) at build time.</span></span> <span data-ttu-id="3a761-106">Entity Framework は、実行時にマッピングメタデータを使用して、概念モデルに対するクエリをストア固有のコマンドに変換します。</span><span class="sxs-lookup"><span data-stu-id="3a761-106">Entity Framework uses mapping metadata at runtime to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="3a761-107">Entity Framework Designer (EF デザイナー) は、デザイン時に、.edmx ファイルにマッピング情報を格納します。</span><span class="sxs-lookup"><span data-stu-id="3a761-107">The Entity Framework Designer (EF Designer) stores mapping information in an .edmx file at design time.</span></span> <span data-ttu-id="3a761-108">ビルド時に、Entity Designer は .edmx ファイルの情報を使用して、実行時に Entity Framework が必要とする .msl ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="3a761-108">At build time, the Entity Designer uses information in an .edmx file to create the .msl file that is needed by Entity Framework at runtime</span></span>

<span data-ttu-id="3a761-109">MSL で参照されるすべての概念モデル型またはストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-109">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="3a761-110">概念モデルの名前空間の名前については、「 [CSDL の仕様](xref:ef6/modeling/designer/advanced/edmx/csdl-spec)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-110">For information about the conceptual model namespace name, see [CSDL Specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span> <span data-ttu-id="3a761-111">ストレージモデルの名前空間の名前については、「 [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-111">For information about the storage model namespace name, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="3a761-112">MSL のバージョンは、XML 名前空間で区別されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-112">Versions of MSL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="3a761-113">MSL バージョン</span><span class="sxs-lookup"><span data-stu-id="3a761-113">MSL Version</span></span> | <span data-ttu-id="3a761-114">XML 名前空間</span><span class="sxs-lookup"><span data-stu-id="3a761-114">XML Namespace</span></span>                                        |
|:------------|:-----------------------------------------------------|
| <span data-ttu-id="3a761-115">MSL v1</span><span class="sxs-lookup"><span data-stu-id="3a761-115">MSL v1</span></span>      | <span data-ttu-id="3a761-116">urn: スキーマ-microsoft-com: windows: storage: mapping: CS</span><span class="sxs-lookup"><span data-stu-id="3a761-116">urn:schemas-microsoft-com:windows:storage:mapping:CS</span></span> |
| <span data-ttu-id="3a761-117">MSL v2</span><span class="sxs-lookup"><span data-stu-id="3a761-117">MSL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| <span data-ttu-id="3a761-118">MSL v3</span><span class="sxs-lookup"><span data-stu-id="3a761-118">MSL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a><span data-ttu-id="3a761-119">Alias 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-119">Alias Element (MSL)</span></span>

<span data-ttu-id="3a761-120">マッピングスキーマ言語 (MSL) の **Alias** 要素は、概念モデルおよびストレージモデルの名前空間のエイリアスを定義するために使用される mapping 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="3a761-120">The **Alias** element in mapping specification language (MSL) is a child of the Mapping element that is used to define aliases for conceptual model and storage model namespaces.</span></span> <span data-ttu-id="3a761-121">MSL で参照されるすべての概念モデル型またはストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-121">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="3a761-122">概念モデルの名前空間の名前については、「Schema 要素 (CSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-122">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="3a761-123">ストレージモデルの名前空間の名前については、「Schema 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-123">For information about the storage model namespace name, see Schema Element (SSDL).</span></span>

<span data-ttu-id="3a761-124">**Alias**要素に子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="3a761-124">The **Alias** element cannot have child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-125">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-125">Applicable Attributes</span></span>

<span data-ttu-id="3a761-126">次の表では、 **Alias** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-126">The table below describes the attributes that can be applied to the **Alias** element.</span></span>

| <span data-ttu-id="3a761-127">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-127">Attribute Name</span></span> | <span data-ttu-id="3a761-128">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-128">Is Required</span></span> | <span data-ttu-id="3a761-129">値</span><span class="sxs-lookup"><span data-stu-id="3a761-129">Value</span></span>                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| <span data-ttu-id="3a761-130">**キー**</span><span class="sxs-lookup"><span data-stu-id="3a761-130">**Key**</span></span>        | <span data-ttu-id="3a761-131">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-131">Yes</span></span>         | <span data-ttu-id="3a761-132">**Value**属性によって指定された名前空間のエイリアス。</span><span class="sxs-lookup"><span data-stu-id="3a761-132">The alias for the namespace that is specified by the **Value** attribute.</span></span> |
| <span data-ttu-id="3a761-133">**Value**</span><span class="sxs-lookup"><span data-stu-id="3a761-133">**Value**</span></span>      | <span data-ttu-id="3a761-134">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-134">Yes</span></span>         | <span data-ttu-id="3a761-135">**キー**要素の値がエイリアスである名前空間。</span><span class="sxs-lookup"><span data-stu-id="3a761-135">The namespace for which the value of the **Key** element is an alias.</span></span>     |

### <a name="example"></a><span data-ttu-id="3a761-136">例</span><span class="sxs-lookup"><span data-stu-id="3a761-136">Example</span></span>

<span data-ttu-id="3a761-137">次の例は、概念モデルで定義されている型のエイリアスを定義する **alias** 要素を示して `c` います。</span><span class="sxs-lookup"><span data-stu-id="3a761-137">The following example shows an **Alias** element that defines an alias, `c`, for types that are defined in the conceptual model.</span></span>

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

## <a name="associationend-element-msl"></a><span data-ttu-id="3a761-138">AssociationEnd 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-138">AssociationEnd Element (MSL)</span></span>

<span data-ttu-id="3a761-139">マッピングスキーマ言語 (MSL) の **Associationend** 要素は、概念モデルのエンティティ型の変更関数が基になるデータベースのストアドプロシージャにマップされるときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-139">The **AssociationEnd** element in mapping specification language (MSL) is used when the modification functions of an entity type in the conceptual model are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="3a761-140">変更ストアドプロシージャが、その値が関連付けプロパティに保持されているパラメーターを受け取る場合、 **Associationend** 要素はプロパティ値をパラメーターにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-140">If a modification stored procedure takes a parameter whose value is held in an association property, the **AssociationEnd** element maps the property value to the parameter.</span></span> <span data-ttu-id="3a761-141">詳細については、下の例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-141">For more information, see the example below.</span></span>

<span data-ttu-id="3a761-142">エンティティ型の変更関数をストアドプロシージャにマップする方法の詳細については、「ModificationFunctionMapping 要素 (MSL)」および「チュートリアル: ストアドプロシージャへのエンティティのマッピング」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-142">For more information about mapping modification functions of entity types to stored procedures, see ModificationFunctionMapping Element (MSL) and Walkthrough: Mapping an Entity to Stored Procedures.</span></span>

<span data-ttu-id="3a761-143">**Associationend**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-143">The **AssociationEnd** element can have the following child elements:</span></span>

-   <span data-ttu-id="3a761-144">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="3a761-144">ScalarProperty</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-145">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-145">Applicable Attributes</span></span>

<span data-ttu-id="3a761-146">次の表では、 **Associationend** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-146">The following table describes the attributes that are applicable to the **AssociationEnd** element.</span></span>

| <span data-ttu-id="3a761-147">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-147">Attribute Name</span></span>     | <span data-ttu-id="3a761-148">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-148">Is Required</span></span> | <span data-ttu-id="3a761-149">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-149">Value</span></span>                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-150">**AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="3a761-150">**AssociationSet**</span></span> | <span data-ttu-id="3a761-151">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-151">Yes</span></span>         | <span data-ttu-id="3a761-152">マップされるアソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-152">The name of the association that is being mapped.</span></span>                                                                                                                                 |
| <span data-ttu-id="3a761-153">**From**</span><span class="sxs-lookup"><span data-stu-id="3a761-153">**From**</span></span>           | <span data-ttu-id="3a761-154">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-154">Yes</span></span>         | <span data-ttu-id="3a761-155">マップされるアソシエーションに対応するナビゲーションプロパティの **Fromrole** 属性の値。</span><span class="sxs-lookup"><span data-stu-id="3a761-155">The value of the **FromRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="3a761-156">詳細については、「NavigationProperty 要素 (CSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-156">For more information, see NavigationProperty Element (CSDL).</span></span> |
| <span data-ttu-id="3a761-157">**To**</span><span class="sxs-lookup"><span data-stu-id="3a761-157">**To**</span></span>             | <span data-ttu-id="3a761-158">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-158">Yes</span></span>         | <span data-ttu-id="3a761-159">マップされるアソシエーションに対応するナビゲーションプロパティの **Torole** 属性の値。</span><span class="sxs-lookup"><span data-stu-id="3a761-159">The value of the **ToRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="3a761-160">詳細については、「NavigationProperty 要素 (CSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-160">For more information, see NavigationProperty Element (CSDL).</span></span>   |

### <a name="example"></a><span data-ttu-id="3a761-161">例</span><span class="sxs-lookup"><span data-stu-id="3a761-161">Example</span></span>

<span data-ttu-id="3a761-162">次の概念モデルのエンティティ型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="3a761-162">Consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="3a761-163">さらに、次のストアド プロシージャを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="3a761-163">Also consider the following stored procedure:</span></span>

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

<span data-ttu-id="3a761-164">エンティティの更新関数をこのストアドプロシージャにマップするには、 `Course` **DepartmentID** パラメーターに値を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-164">In order to map the update function of the `Course` entity to this stored procedure, you must supply a value to the **DepartmentID** parameter.</span></span> <span data-ttu-id="3a761-165">`DepartmentID` の値はエンティティ型のプロパティには対応せず、独立した関連付け (アソシエーション) に含まれています。ここに、そのマッピングを示します。</span><span class="sxs-lookup"><span data-stu-id="3a761-165">The value for `DepartmentID` does not correspond to a property on the entity type; it is contained in an independent association whose mapping is shown here:</span></span>

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

<span data-ttu-id="3a761-166">次のコードは、 **FK \_ course \_ Department**アソシエーションの**DepartmentID**プロパティを**updatecourse**ストアドプロシージャにマップするために使用される**associationend**要素を示しています (これは、 **course**エンティティ型の更新関数がマップされています)。</span><span class="sxs-lookup"><span data-stu-id="3a761-166">The following code shows the **AssociationEnd** element used to map the **DepartmentID** property of the **FK\_Course\_Department** association to the **UpdateCourse** stored procedure (to which the update function of the **Course** entity type is mapped):</span></span>

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

## <a name="associationsetmapping-element-msl"></a><span data-ttu-id="3a761-167">AssociationSetMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-167">AssociationSetMapping Element (MSL)</span></span>

<span data-ttu-id="3a761-168">マッピング仕様言語 (MSL) の **AssociationSetMapping** 要素は、概念モデルのアソシエーションと基になるデータベースのテーブル列の間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-168">The **AssociationSetMapping** element in mapping specification language (MSL) defines the mapping between an association in the conceptual model and table columns in the underlying database.</span></span>

<span data-ttu-id="3a761-169">概念モデルのアソシエーションとは、プロパティが基になるデータベースの主キー列および外部キー列を表す型のことです。</span><span class="sxs-lookup"><span data-stu-id="3a761-169">Associations in the conceptual model are types whose properties represent primary and foreign key columns in the underlying database.</span></span> <span data-ttu-id="3a761-170">**AssociationSetMapping**要素は、2つの endproperty 要素を使用して、データベース内のアソシエーション型のプロパティと列の間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-170">The **AssociationSetMapping** element uses two EndProperty elements to define the mappings between association type properties and columns in the database.</span></span> <span data-ttu-id="3a761-171">Condition 要素を使用して、これらのマッピングに条件を指定できます。</span><span class="sxs-lookup"><span data-stu-id="3a761-171">You can place conditions on these mappings with the Condition element.</span></span> <span data-ttu-id="3a761-172">ModificationFunctionMapping 要素を使用して、アソシエーションの挿入関数、更新関数、および削除関数をデータベース内のストアド プロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-172">Map the insert, update, and delete functions for associations to stored procedures in the database with the ModificationFunctionMapping element.</span></span> <span data-ttu-id="3a761-173">QueryView 要素内の Entity SQL 文字列を使用して、アソシエーションとテーブル列の間の読み取り専用マッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-173">Define read-only mappings between associations and table columns by using an Entity SQL string in a QueryView element.</span></span>

> [!NOTE]
> <span data-ttu-id="3a761-174">概念モデルのアソシエーションに対して参照制約が定義されている場合、アソシエーションを **AssociationSetMapping** 要素にマップする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="3a761-174">If a referential constraint is defined for an association in the conceptual model, the association does not need to be mapped with an **AssociationSetMapping** element.</span></span> <span data-ttu-id="3a761-175">参照に関する制約を持つアソシエーションの **AssociationSetMapping** 要素が存在する場合、 **AssociationSetMapping** 要素で定義されているマッピングは無視されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-175">If an **AssociationSetMapping** element is present for an association that has a referential constraint, the mappings defined in the **AssociationSetMapping** element will be ignored.</span></span> <span data-ttu-id="3a761-176">詳細については、「参照要素の参照」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-176">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="3a761-177">**AssociationSetMapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-177">The **AssociationSetMapping** element can have the following child elements</span></span>

-   <span data-ttu-id="3a761-178">QueryView (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="3a761-178">QueryView (zero or one)</span></span>
-   <span data-ttu-id="3a761-179">EndProperty (0 個または 2 個)</span><span class="sxs-lookup"><span data-stu-id="3a761-179">EndProperty (zero or two)</span></span>
-   <span data-ttu-id="3a761-180">Condition (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-180">Condition (zero or more)</span></span>
-   <span data-ttu-id="3a761-181">ModificationFunctionMapping (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="3a761-181">ModificationFunctionMapping (zero or one)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-182">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-182">Applicable Attributes</span></span>

<span data-ttu-id="3a761-183">次の表では、 **AssociationSetMapping** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-183">The following table describes the attributes that can be applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="3a761-184">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-184">Attribute Name</span></span>     | <span data-ttu-id="3a761-185">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-185">Is Required</span></span> | <span data-ttu-id="3a761-186">値</span><span class="sxs-lookup"><span data-stu-id="3a761-186">Value</span></span>                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-187">**名前**</span><span class="sxs-lookup"><span data-stu-id="3a761-187">**Name**</span></span>           | <span data-ttu-id="3a761-188">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-188">Yes</span></span>         | <span data-ttu-id="3a761-189">マップされる概念モデルのアソシエーション セットの名前です。</span><span class="sxs-lookup"><span data-stu-id="3a761-189">The name of the conceptual model association set that is being mapped.</span></span>                      |
| <span data-ttu-id="3a761-190">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="3a761-190">**TypeName**</span></span>       | <span data-ttu-id="3a761-191">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-191">No</span></span>          | <span data-ttu-id="3a761-192">マッピングされている概念モデルのアソシエーション型の名前空間修飾名です。</span><span class="sxs-lookup"><span data-stu-id="3a761-192">The namespace-qualified name of the conceptual model association type that is being mapped.</span></span> |
| <span data-ttu-id="3a761-193">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="3a761-193">**StoreEntitySet**</span></span> | <span data-ttu-id="3a761-194">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-194">No</span></span>          | <span data-ttu-id="3a761-195">マップされるテーブルの名前です。</span><span class="sxs-lookup"><span data-stu-id="3a761-195">The name of the table that is being mapped.</span></span>                                                 |

### <a name="example"></a><span data-ttu-id="3a761-196">例</span><span class="sxs-lookup"><span data-stu-id="3a761-196">Example</span></span>

<span data-ttu-id="3a761-197">次の例では、概念モデルの**FK \_ course \_ Department**アソシエーションセットがデータベースの**Course**テーブルにマップされている**AssociationSetMapping**要素を示します。</span><span class="sxs-lookup"><span data-stu-id="3a761-197">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association set in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="3a761-198">アソシエーション型のプロパティとテーブル列の間のマッピングは、子 **Endproperty** 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-198">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

## <a name="complexproperty-element-msl"></a><span data-ttu-id="3a761-199">ComplexProperty 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-199">ComplexProperty Element (MSL)</span></span>

<span data-ttu-id="3a761-200">マッピング仕様言語 (MSL) の **Complexproperty** 要素は、概念モデルのエンティティ型の複合型プロパティと基になるデータベースのテーブル列との間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-200">A **ComplexProperty** element in mapping specification language (MSL) defines the mapping between a complex type property on a conceptual model entity type and table columns in the underlying database.</span></span> <span data-ttu-id="3a761-201">プロパティ列のマッピングは、子 ScalarProperty 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-201">The property-column mappings are specified in child ScalarProperty elements.</span></span>

<span data-ttu-id="3a761-202">**ComplexType**プロパティ要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-202">The **ComplexType** property element can have the following child elements:</span></span>

-   <span data-ttu-id="3a761-203">ScalarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-203">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="3a761-204">**Complexproperty** (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-204">**ComplexProperty** (zero or more)</span></span>
-   <span data-ttu-id="3a761-205">ComplextTypeMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-205">ComplextTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="3a761-206">Condition (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-206">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-207">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-207">Applicable Attributes</span></span>

<span data-ttu-id="3a761-208">次の表では、 **Complexproperty** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-208">The following table describes the attributes that are applicable to the **ComplexProperty** element:</span></span>

| <span data-ttu-id="3a761-209">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-209">Attribute Name</span></span> | <span data-ttu-id="3a761-210">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-210">Is Required</span></span> | <span data-ttu-id="3a761-211">値</span><span class="sxs-lookup"><span data-stu-id="3a761-211">Value</span></span>                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-212">**名前**</span><span class="sxs-lookup"><span data-stu-id="3a761-212">**Name**</span></span>       | <span data-ttu-id="3a761-213">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-213">Yes</span></span>         | <span data-ttu-id="3a761-214">マップされる概念モデルのエンティティ型の複合プロパティ名。</span><span class="sxs-lookup"><span data-stu-id="3a761-214">The name of the complex property of an entity type in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="3a761-215">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="3a761-215">**TypeName**</span></span>   | <span data-ttu-id="3a761-216">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-216">No</span></span>          | <span data-ttu-id="3a761-217">概念モデルのプロパティ型の名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="3a761-217">The namespace-qualified name of the conceptual model property type.</span></span>                              |

### <a name="example"></a><span data-ttu-id="3a761-218">例</span><span class="sxs-lookup"><span data-stu-id="3a761-218">Example</span></span>

<span data-ttu-id="3a761-219">次の例は、School モデル に基づいています。</span><span class="sxs-lookup"><span data-stu-id="3a761-219">The following example is based on the School model.</span></span> <span data-ttu-id="3a761-220">次の複合型は概念モデルに追加されました。</span><span class="sxs-lookup"><span data-stu-id="3a761-220">The following complex type has been added to the conceptual model:</span></span>

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

<span data-ttu-id="3a761-221">**Person**エンティティ型の**LastName**プロパティと**FirstName**プロパティは、1つの複合プロパティ**Name**に置き換えられました。</span><span class="sxs-lookup"><span data-stu-id="3a761-221">The **LastName** and **FirstName** properties of the **Person** entity type have been replaced with one complex property, **Name**:</span></span>

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

<span data-ttu-id="3a761-222">次の MSL は、 **Name**プロパティを基になるデータベースの列にマップするために使用される**complexproperty**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-222">The following MSL shows the **ComplexProperty** element used to map the **Name** property to columns in the underlying database:</span></span>

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

## <a name="complextypemapping-element-msl"></a><span data-ttu-id="3a761-223">ComplexTypeMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-223">ComplexTypeMapping Element (MSL)</span></span>

<span data-ttu-id="3a761-224">マッピング仕様言語 (MSL) の **Complextypemapping** 要素は、resultmapping 要素の子であり、次の条件に該当する場合に、概念モデルの関数インポートと基になるデータベースのストアドプロシージャの間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-224">The **ComplexTypeMapping** element in mapping specification language (MSL) is a child of the ResultMapping element and defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="3a761-225">関数インポートが概念の複合型を返す場合</span><span class="sxs-lookup"><span data-stu-id="3a761-225">The function import returns a conceptual complex type.</span></span>
-   <span data-ttu-id="3a761-226">ストアド プロシージャが返す列の名前が、複合型のプロパティの名前と完全には一致しない場合</span><span class="sxs-lookup"><span data-stu-id="3a761-226">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the complex type.</span></span>

<span data-ttu-id="3a761-227">既定では、ストアド プロシージャが返す列と複合型とのマッピングは、列およびプロパティの名前に基づいています。</span><span class="sxs-lookup"><span data-stu-id="3a761-227">By default, the mapping between the columns returned by a stored procedure and a complex type is based on column and property names.</span></span> <span data-ttu-id="3a761-228">列名がプロパティ名と完全に一致しない場合は、 **Complextypemapping** 要素を使用してマッピングを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-228">If column names do not exactly match property names, you must use the **ComplexTypeMapping** element to define the mapping.</span></span> <span data-ttu-id="3a761-229">既定のマッピングの例については、「FunctionImportMapping 要素 (MSL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-229">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="3a761-230">**Complextypemapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-230">The **ComplexTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="3a761-231">ScalarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-231">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-232">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-232">Applicable Attributes</span></span>

<span data-ttu-id="3a761-233">次の表では、 **Complextypemapping** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-233">The following table describes the attributes that are applicable to the **ComplexTypeMapping** element.</span></span>

| <span data-ttu-id="3a761-234">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-234">Attribute Name</span></span> | <span data-ttu-id="3a761-235">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-235">Is Required</span></span> | <span data-ttu-id="3a761-236">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-236">Value</span></span>                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| <span data-ttu-id="3a761-237">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="3a761-237">**TypeName**</span></span>   | <span data-ttu-id="3a761-238">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-238">Yes</span></span>         | <span data-ttu-id="3a761-239">マッピングされている複合型の名前空間修飾名です。</span><span class="sxs-lookup"><span data-stu-id="3a761-239">The namespace-qualified name of the complex type that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="3a761-240">例</span><span class="sxs-lookup"><span data-stu-id="3a761-240">Example</span></span>

<span data-ttu-id="3a761-241">次のストアド プロシージャについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="3a761-241">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="3a761-242">また、次の概念モデルの複合型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="3a761-242">Also consider the following conceptual model complex type:</span></span>

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

<span data-ttu-id="3a761-243">前の複合型のインスタンスを返す関数インポートを作成するには、ストアドプロシージャによって返される列とエンティティ型の間のマッピングを **Complextypemapping** 要素で定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-243">In order to create a function import that returns instances of the previous complex type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ComplexTypeMapping** element:</span></span>

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

## <a name="condition-element-msl"></a><span data-ttu-id="3a761-244">Condition 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-244">Condition Element (MSL)</span></span>

<span data-ttu-id="3a761-245">マッピングスキーマ言語 (MSL) の **Condition** 要素は、概念モデルと基になるデータベースとの間のマッピングに関する条件を配置します。</span><span class="sxs-lookup"><span data-stu-id="3a761-245">The **Condition** element in mapping specification language (MSL) places conditions on mappings between the conceptual model and the underlying database.</span></span> <span data-ttu-id="3a761-246">XML ノード内で定義されているマッピングは、子 **条件** 要素で指定されているすべての条件が満たされている場合に有効です。</span><span class="sxs-lookup"><span data-stu-id="3a761-246">The mapping that is defined within an XML node is valid if all conditions, as specified in child **Condition** elements, are met.</span></span> <span data-ttu-id="3a761-247">それ以外の場合、マッピングは無効です。</span><span class="sxs-lookup"><span data-stu-id="3a761-247">Otherwise, the mapping is not valid.</span></span> <span data-ttu-id="3a761-248">たとえば、MappingFragment 要素に1つ以上の**条件**子要素が含まれている場合、その子**条件**要素のすべての条件が満たされた場合にのみ、 **mappingfragment**ノード内で定義されたマッピングが有効になります。</span><span class="sxs-lookup"><span data-stu-id="3a761-248">For example, if a MappingFragment element contains one or more **Condition** child elements, the mapping defined within the **MappingFragment** node will only be valid if all the conditions of the child **Condition** elements are met.</span></span>

<span data-ttu-id="3a761-249">各条件は、 **名前** (概念モデルエンティティプロパティ **の名前)** 、または **columnname** ( **columnname** 属性で指定されたデータベース内の列の名前) のいずれかに適用できます。</span><span class="sxs-lookup"><span data-stu-id="3a761-249">Each condition can apply to either a **Name** (the name of a conceptual model entity property, specified by the **Name** attribute), or a **ColumnName** (the name of a column in the database, specified by the **ColumnName** attribute).</span></span> <span data-ttu-id="3a761-250">**Name**属性を設定すると、エンティティのプロパティ値に対して条件がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="3a761-250">When the **Name** attribute is set, the condition is checked against an entity property value.</span></span> <span data-ttu-id="3a761-251">**ColumnName**属性を設定すると、列の値に対して条件がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="3a761-251">When the **ColumnName** attribute is set, the condition is checked against a column value.</span></span> <span data-ttu-id="3a761-252">**Condition**要素では、 **Name**または**ColumnName**属性のいずれか1つだけを指定できます。</span><span class="sxs-lookup"><span data-stu-id="3a761-252">Only one of the **Name** or **ColumnName** attribute can be specified in a **Condition** element.</span></span>

> [!NOTE]
> <span data-ttu-id="3a761-253">FunctionImportMapping 要素内で **Condition** 要素が使用されている場合、 **Name** 属性だけが適用されません。</span><span class="sxs-lookup"><span data-stu-id="3a761-253">When the **Condition** element is used within a FunctionImportMapping element, only the **Name** attribute is not applicable.</span></span>

<span data-ttu-id="3a761-254">**Condition**要素は、次の要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-254">The **Condition** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="3a761-255">AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="3a761-255">AssociationSetMapping</span></span>
-   <span data-ttu-id="3a761-256">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="3a761-256">ComplexProperty</span></span>
-   <span data-ttu-id="3a761-257">EntitySetMapping</span><span class="sxs-lookup"><span data-stu-id="3a761-257">EntitySetMapping</span></span>
-   <span data-ttu-id="3a761-258">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="3a761-258">MappingFragment</span></span>
-   <span data-ttu-id="3a761-259">EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="3a761-259">EntityTypeMapping</span></span>

<span data-ttu-id="3a761-260">**Condition**要素に子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="3a761-260">The **Condition** element can have no child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-261">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-261">Applicable Attributes</span></span>

<span data-ttu-id="3a761-262">次の表では、 **Condition** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-262">The following table describes the attributes that are applicable to the **Condition** element:</span></span>

| <span data-ttu-id="3a761-263">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-263">Attribute Name</span></span> | <span data-ttu-id="3a761-264">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-264">Is Required</span></span> | <span data-ttu-id="3a761-265">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-265">Value</span></span>                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-266">**[ColumnName]**</span><span class="sxs-lookup"><span data-stu-id="3a761-266">**ColumnName**</span></span> | <span data-ttu-id="3a761-267">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-267">No</span></span>          | <span data-ttu-id="3a761-268">条件の評価に使用される値のテーブル列の名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-268">The name of the table column whose value is used to evaluate the condition.</span></span>                                                                                                                                                                                                                   |
| <span data-ttu-id="3a761-269">**IsNull**</span><span class="sxs-lookup"><span data-stu-id="3a761-269">**IsNull**</span></span>     | <span data-ttu-id="3a761-270">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-270">No</span></span>          | <span data-ttu-id="3a761-271">**True** または **False** です。</span><span class="sxs-lookup"><span data-stu-id="3a761-271">**True** or **False**.</span></span> <span data-ttu-id="3a761-272">値が **true** で列の値が **null**である場合、または値が **False** で列の値が **null**でない場合、条件は true になります。</span><span class="sxs-lookup"><span data-stu-id="3a761-272">If the value is **True** and the column value is **null**, or if the value is **False** and the column value is not **null**, the condition is true.</span></span> <span data-ttu-id="3a761-273">それ以外の場合、条件は False です。</span><span class="sxs-lookup"><span data-stu-id="3a761-273">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="3a761-274">**IsNull**属性と**Value**属性は同時に使用できません。</span><span class="sxs-lookup"><span data-stu-id="3a761-274">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span> |
| <span data-ttu-id="3a761-275">**Value**</span><span class="sxs-lookup"><span data-stu-id="3a761-275">**Value**</span></span>      | <span data-ttu-id="3a761-276">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-276">No</span></span>          | <span data-ttu-id="3a761-277">列値と比較される値。</span><span class="sxs-lookup"><span data-stu-id="3a761-277">The value with which the column value is compared.</span></span> <span data-ttu-id="3a761-278">値が同じ場合、条件は True です。</span><span class="sxs-lookup"><span data-stu-id="3a761-278">If the values are the same, the condition is true.</span></span> <span data-ttu-id="3a761-279">それ以外の場合、条件は False です。</span><span class="sxs-lookup"><span data-stu-id="3a761-279">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="3a761-280">**IsNull**属性と**Value**属性は同時に使用できません。</span><span class="sxs-lookup"><span data-stu-id="3a761-280">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span>                                                                       |
| <span data-ttu-id="3a761-281">**名前**</span><span class="sxs-lookup"><span data-stu-id="3a761-281">**Name**</span></span>       | <span data-ttu-id="3a761-282">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-282">No</span></span>          | <span data-ttu-id="3a761-283">条件の評価に使用される値を持つ概念モデルのエンティティ プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-283">The name of the conceptual model entity property whose value is used to evaluate the condition.</span></span> <br/> <span data-ttu-id="3a761-284">**Condition**要素が FunctionImportMapping 要素内で使用されている場合、この属性は適用されません。</span><span class="sxs-lookup"><span data-stu-id="3a761-284">This attribute is not applicable if the **Condition** element is used within a FunctionImportMapping element.</span></span>                                                                           |

### <a name="example"></a><span data-ttu-id="3a761-285">例</span><span class="sxs-lookup"><span data-stu-id="3a761-285">Example</span></span>

<span data-ttu-id="3a761-286">次の例では、 **Mappingfragment**要素の子として**Condition**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-286">The following example shows **Condition** elements as children of **MappingFragment** elements.</span></span> <span data-ttu-id="3a761-287">**HireDate**が null でなく、 **EnrollmentDate**が Null の場合、 **SchoolModel**型と**Person**テーブルの**PersonID**および**HireDate**列の間でデータがマップされます。</span><span class="sxs-lookup"><span data-stu-id="3a761-287">When **HireDate** is not null and **EnrollmentDate** is null, data is mapped between the **SchoolModel.Instructor** type and the **PersonID** and **HireDate** columns of the **Person** table.</span></span> <span data-ttu-id="3a761-288">**EnrollmentDate**が null でなく、 **HireDate**が Null の場合、 **Person**テーブルの**SchoolModel**型と**PersonID**列および**Enrollment**列の間でデータがマップされます。</span><span class="sxs-lookup"><span data-stu-id="3a761-288">When **EnrollmentDate** is not null and **HireDate** is null, data is mapped between the **SchoolModel.Student** type and the **PersonID** and **Enrollment** columns of the **Person** table.</span></span>

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

## <a name="deletefunction-element-msl"></a><span data-ttu-id="3a761-289">DeleteFunction 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-289">DeleteFunction Element (MSL)</span></span>

<span data-ttu-id="3a761-290">マッピングスキーマ言語 (MSL) の **deletefunction** 要素は、概念モデルのエンティティ型またはアソシエーションの delete 関数を、基になるデータベースのストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-290">The **DeleteFunction** element in mapping specification language (MSL) maps the delete function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="3a761-291">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-291">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="3a761-292">詳しくは、「Function 要素 (SSDL)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3a761-292">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="3a761-293">エンティティ型の3つの挿入、更新、または削除操作をすべてストアドプロシージャにマップしない場合、実行時にマップされていない操作は失敗し、UpdateException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="3a761-293">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

### <a name="deletefunction-applied-to-entitytypemapping"></a><span data-ttu-id="3a761-294">EntityTypeMapping への DeleteFunction の適用</span><span class="sxs-lookup"><span data-stu-id="3a761-294">DeleteFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="3a761-295">EntityTypeMapping 要素に適用される場合、 **Deletefunction** 要素は、概念モデルのエンティティ型の削除関数をストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-295">When applied to the EntityTypeMapping element, the **DeleteFunction** element maps the delete function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="3a761-296">**Deletefunction**要素には、 **entitytypemapping**要素に適用するときに次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-296">The **DeleteFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="3a761-297">AssociationEnd (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-297">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="3a761-298">ComplexProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-298">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="3a761-299">ScarlarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-299">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="3a761-300">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-300">Applicable Attributes</span></span>

<span data-ttu-id="3a761-301">次の表では、 **Entitytypemapping**要素に適用されるときに**deletefunction**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-301">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="3a761-302">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-302">Attribute Name</span></span>            | <span data-ttu-id="3a761-303">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-303">Is Required</span></span> | <span data-ttu-id="3a761-304">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-304">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-305">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="3a761-305">**FunctionName**</span></span>          | <span data-ttu-id="3a761-306">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-306">Yes</span></span>         | <span data-ttu-id="3a761-307">削除関数のマップ先となるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="3a761-307">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="3a761-308">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-308">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="3a761-309">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="3a761-309">**RowsAffectedParameter**</span></span> | <span data-ttu-id="3a761-310">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-310">No</span></span>          | <span data-ttu-id="3a761-311">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-311">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="3a761-312">例</span><span class="sxs-lookup"><span data-stu-id="3a761-312">Example</span></span>

<span data-ttu-id="3a761-313">次の例は、School モデルに基づいています。また、 **Person**エンティティ型の delete 関数を**deletefunction**ストアドプロシージャにマップする**deletefunction**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-313">The following example is based on the School model and shows the **DeleteFunction** element mapping the delete function of the **Person** entity type to the **DeletePerson** stored procedure.</span></span> <span data-ttu-id="3a761-314">**Deleteperson**ストアドプロシージャは、ストレージモデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="3a761-314">The **DeletePerson** stored procedure is declared in the storage model.</span></span>

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

### <a name="deletefunction-applied-to-associationsetmapping"></a><span data-ttu-id="3a761-315">AssociationSetMapping への DeleteFunction の適用</span><span class="sxs-lookup"><span data-stu-id="3a761-315">DeleteFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="3a761-316">AssociationSetMapping 要素に適用した場合、 **Deletefunction** 要素は、概念モデルのアソシエーションの delete 関数をストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-316">When applied to the AssociationSetMapping element, the **DeleteFunction** element maps the delete function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="3a761-317">**Deletefunction**要素には、 **AssociationSetMapping**要素に適用するときに次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-317">The **DeleteFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="3a761-318">EndProperty</span><span class="sxs-lookup"><span data-stu-id="3a761-318">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="3a761-319">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-319">Applicable Attributes</span></span>

<span data-ttu-id="3a761-320">次の表では、 **AssociationSetMapping**要素に適用されるときに**deletefunction**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-320">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="3a761-321">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-321">Attribute Name</span></span>            | <span data-ttu-id="3a761-322">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-322">Is Required</span></span> | <span data-ttu-id="3a761-323">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-323">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-324">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="3a761-324">**FunctionName**</span></span>          | <span data-ttu-id="3a761-325">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-325">Yes</span></span>         | <span data-ttu-id="3a761-326">削除関数のマップ先となるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="3a761-326">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="3a761-327">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-327">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="3a761-328">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="3a761-328">**RowsAffectedParameter**</span></span> | <span data-ttu-id="3a761-329">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-329">No</span></span>          | <span data-ttu-id="3a761-330">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-330">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="3a761-331">例</span><span class="sxs-lookup"><span data-stu-id="3a761-331">Example</span></span>

<span data-ttu-id="3a761-332">次の例は、School モデルに基づいており、 **CourseInstructor** association の delete 関数を**DeleteCourseInstructor**ストアドプロシージャにマップするために使用される**deletefunction**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-332">The following example is based on the School model and shows the **DeleteFunction** element used to map delete function of the **CourseInstructor** association to the **DeleteCourseInstructor** stored procedure.</span></span> <span data-ttu-id="3a761-333">**DeleteCourseInstructor**ストアドプロシージャは、ストレージモデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="3a761-333">The **DeleteCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="endproperty-element-msl"></a><span data-ttu-id="3a761-334">EndProperty 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-334">EndProperty Element (MSL)</span></span>

<span data-ttu-id="3a761-335">マッピングスキーマ言語 (MSL) の **Endproperty** 要素は、概念モデルのアソシエーションの end または変更関数と基になるデータベースとの間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-335">The **EndProperty** element in mapping specification language (MSL) defines the mapping between an end or a modification function of a conceptual model association and the underlying database.</span></span> <span data-ttu-id="3a761-336">プロパティ列のマッピングは子 ScalarProperty 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-336">The property-column mapping is specified in a child ScalarProperty element.</span></span>

<span data-ttu-id="3a761-337">**Endproperty**要素を使用して、概念モデルのアソシエーションの end のマッピングを定義すると、AssociationSetMapping 要素の子になります。</span><span class="sxs-lookup"><span data-stu-id="3a761-337">When an **EndProperty** element is used to define the mapping for the end of a conceptual model association, it is a child of an AssociationSetMapping element.</span></span> <span data-ttu-id="3a761-338">**Endproperty**要素を使用して、概念モデルの関連付けの変更関数のマッピングを定義すると、insertfunction 要素または deletefunction 要素の子になります。</span><span class="sxs-lookup"><span data-stu-id="3a761-338">When the **EndProperty** element is used to define the mapping for a modification function of a conceptual model association, it is a child of an InsertFunction element or DeleteFunction element.</span></span>

<span data-ttu-id="3a761-339">**Endproperty**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-339">The **EndProperty** element can have the following child elements:</span></span>

-   <span data-ttu-id="3a761-340">ScalarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-340">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-341">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-341">Applicable Attributes</span></span>

<span data-ttu-id="3a761-342">次の表では、 **Endproperty** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-342">The following table describes the attributes that are applicable to the **EndProperty** element:</span></span>

| <span data-ttu-id="3a761-343">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-343">Attribute Name</span></span> | <span data-ttu-id="3a761-344">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-344">Is Required</span></span> | <span data-ttu-id="3a761-345">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-345">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="3a761-346">名前</span><span class="sxs-lookup"><span data-stu-id="3a761-346">Name</span></span>           | <span data-ttu-id="3a761-347">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-347">Yes</span></span>         | <span data-ttu-id="3a761-348">マップされるアソシエーション端の名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-348">The name of the association end that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="3a761-349">例</span><span class="sxs-lookup"><span data-stu-id="3a761-349">Example</span></span>

<span data-ttu-id="3a761-350">次の例では、概念モデルの**FK \_ course \_ Department**アソシエーションがデータベースの**Course**テーブルにマップされている**AssociationSetMapping**要素を示します。</span><span class="sxs-lookup"><span data-stu-id="3a761-350">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="3a761-351">アソシエーション型のプロパティとテーブル列の間のマッピングは、子 **Endproperty** 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-351">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

### <a name="example"></a><span data-ttu-id="3a761-352">例</span><span class="sxs-lookup"><span data-stu-id="3a761-352">Example</span></span>

<span data-ttu-id="3a761-353">次の例は、関連付け (**CourseInstructor**) の挿入関数と削除関数を基になるデータベースのストアドプロシージャにマップする**endproperty**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-353">The following example shows the **EndProperty** element mapping the insert and delete functions of an association (**CourseInstructor**) to stored procedures in the underlying database.</span></span> <span data-ttu-id="3a761-354">ストレージ モデルにマップされて宣言されている関数。</span><span class="sxs-lookup"><span data-stu-id="3a761-354">The functions that are mapped to are declared in the storage model.</span></span>

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

## <a name="entitycontainermapping-element-msl"></a><span data-ttu-id="3a761-355">EntityContainerMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-355">EntityContainerMapping Element (MSL)</span></span>

<span data-ttu-id="3a761-356">マッピング仕様言語 (MSL) の **EntityContainerMapping** 要素は、概念モデルのエンティティコンテナーをストレージモデルのエンティティコンテナーにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-356">The **EntityContainerMapping** element in mapping specification language (MSL) maps the entity container in the conceptual model to the entity container in the storage model.</span></span> <span data-ttu-id="3a761-357">**EntityContainerMapping**要素は、Mapping 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="3a761-357">The **EntityContainerMapping** element is a child of the Mapping element.</span></span>

<span data-ttu-id="3a761-358">**EntityContainerMapping**要素には、次の子要素を含めることができます (順に表示されます)。</span><span class="sxs-lookup"><span data-stu-id="3a761-358">The **EntityContainerMapping** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a761-359">EntitySetMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-359">EntitySetMapping (zero or more)</span></span>
-   <span data-ttu-id="3a761-360">AssociationSetMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-360">AssociationSetMapping (zero or more)</span></span>
-   <span data-ttu-id="3a761-361">FunctionImportMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-361">FunctionImportMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-362">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-362">Applicable Attributes</span></span>

<span data-ttu-id="3a761-363">次の表では、 **EntityContainerMapping** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-363">The following table describes the attributes that can be applied to the **EntityContainerMapping** element.</span></span>

| <span data-ttu-id="3a761-364">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-364">Attribute Name</span></span>            | <span data-ttu-id="3a761-365">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-365">Is Required</span></span> | <span data-ttu-id="3a761-366">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-366">Value</span></span>                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-367">**StorageModelContainer**</span><span class="sxs-lookup"><span data-stu-id="3a761-367">**StorageModelContainer**</span></span> | <span data-ttu-id="3a761-368">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-368">Yes</span></span>         | <span data-ttu-id="3a761-369">マップされるストレージ モデルのエンティティ コンテナーの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-369">The name of the storage model entity container that is being mapped.</span></span>                                                                                                                                                                                     |
| <span data-ttu-id="3a761-370">**CdmEntityContainer**</span><span class="sxs-lookup"><span data-stu-id="3a761-370">**CdmEntityContainer**</span></span>    | <span data-ttu-id="3a761-371">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-371">Yes</span></span>         | <span data-ttu-id="3a761-372">マップされる概念モデルのエンティティ コンテナーの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-372">The name of the conceptual model entity container that is being mapped.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="3a761-373">**GenerateUpdateViews**</span><span class="sxs-lookup"><span data-stu-id="3a761-373">**GenerateUpdateViews**</span></span>   | <span data-ttu-id="3a761-374">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-374">No</span></span>          | <span data-ttu-id="3a761-375">**True** または **False** です。</span><span class="sxs-lookup"><span data-stu-id="3a761-375">**True** or **False**.</span></span> <span data-ttu-id="3a761-376">**False**の場合、更新ビューは生成されません。</span><span class="sxs-lookup"><span data-stu-id="3a761-376">If **False**, no update views are generated.</span></span> <span data-ttu-id="3a761-377">データが正常にラウンドトリップしない可能性があるため、読み取り専用マッピングが無効である場合は、この属性を **False** に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-377">This attribute should be set to **False** when you have a read-only mapping that would be invalid because data may not round-trip successfully.</span></span> <br/> <span data-ttu-id="3a761-378">既定値は **True** です。</span><span class="sxs-lookup"><span data-stu-id="3a761-378">The default value is **True**.</span></span> |

### <a name="example"></a><span data-ttu-id="3a761-379">例</span><span class="sxs-lookup"><span data-stu-id="3a761-379">Example</span></span>

<span data-ttu-id="3a761-380">次の例は、 **SchoolModelEntities**コンテナー (概念モデルのエンティティコンテナー) を**SchoolModelStoreContainer**コンテナー (ストレージモデルのエンティティコンテナー) にマップする**EntityContainerMapping**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-380">The following example shows an **EntityContainerMapping** element that maps the **SchoolModelEntities** container (the conceptual model entity container) to the **SchoolModelStoreContainer** container (the storage model entity container):</span></span>

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

## <a name="entitysetmapping-element-msl"></a><span data-ttu-id="3a761-381">EntitySetMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-381">EntitySetMapping Element (MSL)</span></span>

<span data-ttu-id="3a761-382">マッピング仕様言語 (MSL) の **EntitySetMapping** 要素は、概念モデルエンティティセット内のすべての型をストレージモデルのエンティティセットにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-382">The **EntitySetMapping** element in mapping specification language (MSL) maps all types in a conceptual model entity set to entity sets in the storage model.</span></span> <span data-ttu-id="3a761-383">概念モデルのエンティティ セットは、同じ型のエンティティのインスタンス (および派生型) の論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="3a761-383">An entity set in the conceptual model is a logical container for instances of entities of the same type (and derived types).</span></span> <span data-ttu-id="3a761-384">ストレージ モデルのエンティティ セットは、基になるデータベースのテーブルまたはビューを表します。</span><span class="sxs-lookup"><span data-stu-id="3a761-384">An entity set in the storage model represents a table or view in the underlying database.</span></span> <span data-ttu-id="3a761-385">概念モデルのエンティティセットは、 **EntitySetMapping**要素の**Name**属性の値によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-385">The conceptual model entity set is specified by the value of the **Name** attribute of the **EntitySetMapping** element.</span></span> <span data-ttu-id="3a761-386">マップ先のテーブルまたはビューは、各子 MappingFragment 要素または**EntitySetMapping**要素自体の**storeentityset**属性によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-386">The mapped-to table or view is specified by the **StoreEntitySet** attribute in each child MappingFragment element or in the **EntitySetMapping** element itself.</span></span>

<span data-ttu-id="3a761-387">**EntitySetMapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-387">The **EntitySetMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="3a761-388">EntityTypeMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-388">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="3a761-389">QueryView (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="3a761-389">QueryView (zero or one)</span></span>
-   <span data-ttu-id="3a761-390">MappingFragment (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-390">MappingFragment (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-391">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-391">Applicable Attributes</span></span>

<span data-ttu-id="3a761-392">次の表では、 **EntitySetMapping** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-392">The following table describes the attributes that can be applied to the **EntitySetMapping** element.</span></span>

| <span data-ttu-id="3a761-393">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-393">Attribute Name</span></span>           | <span data-ttu-id="3a761-394">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-394">Is Required</span></span> | <span data-ttu-id="3a761-395">値</span><span class="sxs-lookup"><span data-stu-id="3a761-395">Value</span></span>                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-396">**名前**</span><span class="sxs-lookup"><span data-stu-id="3a761-396">**Name**</span></span>                 | <span data-ttu-id="3a761-397">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-397">Yes</span></span>         | <span data-ttu-id="3a761-398">マッピングされている概念モデルのエンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-398">The name of the conceptual model entity set that is being mapped.</span></span>                                                                                                                                                             |
| <span data-ttu-id="3a761-399">**TypeName** **1**</span><span class="sxs-lookup"><span data-stu-id="3a761-399">**TypeName** **1**</span></span>       | <span data-ttu-id="3a761-400">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-400">No</span></span>          | <span data-ttu-id="3a761-401">マッピングされている概念モデルのエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-401">The name of the conceptual model entity type that is being mapped.</span></span>                                                                                                                                                            |
| <span data-ttu-id="3a761-402">**Storeentityset** **1**</span><span class="sxs-lookup"><span data-stu-id="3a761-402">**StoreEntitySet** **1**</span></span> | <span data-ttu-id="3a761-403">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-403">No</span></span>          | <span data-ttu-id="3a761-404">マップ先のストレージ モデルのエンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-404">The name of the storage model entity set that is being mapped to.</span></span>                                                                                                                                                             |
| <span data-ttu-id="3a761-405">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="3a761-405">**MakeColumnsDistinct**</span></span>  | <span data-ttu-id="3a761-406">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-406">No</span></span>          | <span data-ttu-id="3a761-407">個別の行のみが返されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="3a761-407">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="3a761-408">この属性が **True**に設定されている場合は、EntityContainerMapping 要素の **generateupdateviews** 属性が **False**に設定されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-408">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

 

<span data-ttu-id="3a761-409">**1** 単一のエンティティ型を1つのテーブルにマップするために、TypeName 属性と storeentityset 子要素の代わりに **TypeName** 属性と **storeentityset** 属性を使用できます。</span><span class="sxs-lookup"><span data-stu-id="3a761-409">**1** The **TypeName** and **StoreEntitySet** attributes can be used in place of the EntityTypeMapping and MappingFragment child elements to map a single entity type to a single table.</span></span>

### <a name="example"></a><span data-ttu-id="3a761-410">例</span><span class="sxs-lookup"><span data-stu-id="3a761-410">Example</span></span>

<span data-ttu-id="3a761-411">次の例は、概念モデルの course エンティティセットに含まれる 3**つの型**(基本型と2つの派生型) を、基になるデータベース内の3つの異なるテーブルにマップする**EntitySetMapping**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-411">The following example shows an **EntitySetMapping** element that maps three types (a base type and two derived types) in the **Courses** entity set of the conceptual model to three different tables in the underlying database.</span></span> <span data-ttu-id="3a761-412">テーブルは、各**Mappingfragment**要素の**storeentityset**属性によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-412">The tables are specified by the **StoreEntitySet** attribute in each **MappingFragment** element.</span></span>

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

## <a name="entitytypemapping-element-msl"></a><span data-ttu-id="3a761-413">EntityTypeMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-413">EntityTypeMapping Element (MSL)</span></span>

<span data-ttu-id="3a761-414">マッピング仕様言語 (MSL) の **Entitytypemapping** 要素は、概念モデルのエンティティ型と、基になるデータベースのテーブルまたはビューの間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-414">The **EntityTypeMapping** element in mapping specification language (MSL) defines the mapping between an entity type in the conceptual model and tables or views in the underlying database.</span></span> <span data-ttu-id="3a761-415">概念モデルのエンティティ型および基になるデータベース テーブルまたはビューの詳細については、「EntityType 要素 (CSDL)」および「EntitySet 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-415">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="3a761-416">マッピングされている概念モデルのエンティティ型は、 **Entitytypemapping**要素の**TypeName**属性によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-416">The conceptual model entity type that is being mapped is specified by the **TypeName** attribute of the **EntityTypeMapping** element.</span></span> <span data-ttu-id="3a761-417">マップされるテーブルまたはビューは、子 MappingFragment 要素の **Storeentityset** 属性によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-417">The table or view that is being mapped is specified by the **StoreEntitySet** attribute of the child MappingFragment element.</span></span>

<span data-ttu-id="3a761-418">ModificationFunctionMapping 子要素を使用すると、エンティティ型の挿入関数、更新関数、または削除関数をデータベース内のストアド プロシージャにマッピングできます。</span><span class="sxs-lookup"><span data-stu-id="3a761-418">The ModificationFunctionMapping child element can be used to map the insert, update, or delete functions of entity types to stored procedures in the database.</span></span>

<span data-ttu-id="3a761-419">**Entitytypemapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-419">The **EntityTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="3a761-420">MappingFragment (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-420">MappingFragment (zero or more)</span></span>
-   <span data-ttu-id="3a761-421">ModificationFunctionMapping (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="3a761-421">ModificationFunctionMapping (zero or one)</span></span>
-   <span data-ttu-id="3a761-422">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="3a761-422">ScalarProperty</span></span>
-   <span data-ttu-id="3a761-423">条件</span><span class="sxs-lookup"><span data-stu-id="3a761-423">Condition</span></span>

> [!NOTE]
> <span data-ttu-id="3a761-424">**Mappingfragment** 要素と **ModificationFunctionMapping** 要素は、同時に **entitytypemapping** 要素の子要素にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="3a761-424">**MappingFragment** and **ModificationFunctionMapping** elements cannot be child elements of the **EntityTypeMapping** element at the same time.</span></span>


> [!NOTE]
> <span data-ttu-id="3a761-425">**ScalarProperty**要素と**Condition**要素は、functionimportmapping 要素内で使用されている場合にのみ、 **entitytypemapping**要素の子要素にすることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-425">The **ScalarProperty** and **Condition** elements can only be child elements of the **EntityTypeMapping** element when it is used within a FunctionImportMapping element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-426">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-426">Applicable Attributes</span></span>

<span data-ttu-id="3a761-427">次の表では、 **Entitytypemapping** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-427">The following table describes the attributes that can be applied to the **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="3a761-428">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-428">Attribute Name</span></span> | <span data-ttu-id="3a761-429">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-429">Is Required</span></span> | <span data-ttu-id="3a761-430">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-430">Value</span></span>                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-431">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="3a761-431">**TypeName**</span></span>   | <span data-ttu-id="3a761-432">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-432">Yes</span></span>         | <span data-ttu-id="3a761-433">マッピングされている概念モデルのエンティティ型の名前空間修飾名です。</span><span class="sxs-lookup"><span data-stu-id="3a761-433">The namespace-qualified name of the conceptual model entity type that is being mapped.</span></span> <br/> <span data-ttu-id="3a761-434">型が抽象型または派生型である場合、値は `IsOfType(Namespace-qualified_type_name)` になる必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-434">If the type is abstract or a derived type, the value must be `IsOfType(Namespace-qualified_type_name)`.</span></span> |

### <a name="example"></a><span data-ttu-id="3a761-435">例</span><span class="sxs-lookup"><span data-stu-id="3a761-435">Example</span></span>

<span data-ttu-id="3a761-436">次の例は、2つの子 **Entitytypemapping** 要素を持つ EntitySetMapping 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-436">The following example shows an EntitySetMapping element with two child **EntityTypeMapping** elements.</span></span> <span data-ttu-id="3a761-437">最初の **Entitytypemapping** 要素では、 **SchoolModel** エンティティ型が **person** テーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="3a761-437">In the first **EntityTypeMapping** element, the **SchoolModel.Person** entity type is mapped to the **Person** table.</span></span> <span data-ttu-id="3a761-438">2番目の **Entitytypemapping** 要素では、 **SchoolModel** 型の更新機能が、データベース内のストアドプロシージャ **updateperson**にマップされます。</span><span class="sxs-lookup"><span data-stu-id="3a761-438">In the second **EntityTypeMapping** element, the update functionality of the **SchoolModel.Person** type is mapped to a stored procedure, **UpdatePerson**, in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="3a761-439">例</span><span class="sxs-lookup"><span data-stu-id="3a761-439">Example</span></span>

<span data-ttu-id="3a761-440">次の例は、ルート型が抽象である型階層のマッピングを示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-440">The next example shows the mapping of a type hierarchy in which the root type is abstract.</span></span> <span data-ttu-id="3a761-441">TypeName 属性の構文を使用することに注意して `IsOfType` ください。 **TypeName**</span><span class="sxs-lookup"><span data-stu-id="3a761-441">Note the use of the `IsOfType` syntax for the **TypeName** attributes.</span></span>

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

## <a name="functionimportmapping-element-msl"></a><span data-ttu-id="3a761-442">FunctionImportMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-442">FunctionImportMapping Element (MSL)</span></span>

<span data-ttu-id="3a761-443">マッピングスキーマ言語 (MSL) の **Functionimportmapping** 要素は、概念モデルの関数インポートと、基になるデータベースのストアドプロシージャまたは関数との間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-443">The **FunctionImportMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure or function in the underlying database.</span></span> <span data-ttu-id="3a761-444">関数インポートは概念モデル内で、ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-444">Function imports must be declared in the conceptual model and stored procedures must be declared in the storage model.</span></span> <span data-ttu-id="3a761-445">詳細については、「FunctionImport 要素 (CSDL)」および「Function 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-445">For more information, see FunctionImport Element (CSDL) and Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="3a761-446">既定では、関数インポートが概念モデル エンティティ型または複合型を返す場合、基になるストアド プロシージャが返す列の名前が概念モデル型のプロパティの名前と正確に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-446">By default, if a function import returns a conceptual model entity type or complex type, then the names of the columns returned by the underlying stored procedure must exactly match the names of the properties on the conceptual model type.</span></span> <span data-ttu-id="3a761-447">列名がプロパティ名と完全に一致しない場合は、ResultMapping 要素でマッピングを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-447">If the column names do not exactly match the property names, the mapping must be defined in a ResultMapping element.</span></span>

<span data-ttu-id="3a761-448">**Functionimportmapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-448">The **FunctionImportMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="3a761-449">ResultMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-449">ResultMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-450">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-450">Applicable Attributes</span></span>

<span data-ttu-id="3a761-451">次の表では、 **Functionimportmapping** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-451">The following table describes the attributes that are applicable to the **FunctionImportMapping** element:</span></span>

| <span data-ttu-id="3a761-452">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-452">Attribute Name</span></span>         | <span data-ttu-id="3a761-453">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-453">Is Required</span></span> | <span data-ttu-id="3a761-454">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-454">Value</span></span>                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-455">**FunctionImportName**</span><span class="sxs-lookup"><span data-stu-id="3a761-455">**FunctionImportName**</span></span> | <span data-ttu-id="3a761-456">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-456">Yes</span></span>         | <span data-ttu-id="3a761-457">マップされる概念モデルの関数インポートの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-457">The name of the function import in the conceptual model that is being mapped.</span></span>           |
| <span data-ttu-id="3a761-458">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="3a761-458">**FunctionName**</span></span>       | <span data-ttu-id="3a761-459">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-459">Yes</span></span>         | <span data-ttu-id="3a761-460">マップされるストレージ モデルの関数の名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="3a761-460">The namespace-qualified name of the function in the storage model that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="3a761-461">例</span><span class="sxs-lookup"><span data-stu-id="3a761-461">Example</span></span>

<span data-ttu-id="3a761-462">次の例は、School モデル に基づいています。</span><span class="sxs-lookup"><span data-stu-id="3a761-462">The following example is based on the School model.</span></span> <span data-ttu-id="3a761-463">ストレージ モデルの次の関数について考えます。</span><span class="sxs-lookup"><span data-stu-id="3a761-463">Consider the following function in the storage model:</span></span>

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

<span data-ttu-id="3a761-464">また、概念モデルのこの関数インポートについて考えます。</span><span class="sxs-lookup"><span data-stu-id="3a761-464">Also consider this function import in the conceptual model:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

<span data-ttu-id="3a761-465">次の例は、関数と関数のインポートを相互にマップするために使用される **Functionimportmapping** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-465">The following example show a **FunctionImportMapping** element used to map the function and function import above to each other:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a><span data-ttu-id="3a761-466">InsertFunction 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-466">InsertFunction Element (MSL)</span></span>

<span data-ttu-id="3a761-467">マッピング仕様言語 (MSL) の **insertfunction** 要素は、概念モデルのエンティティ型またはアソシエーションの挿入関数を、基になるデータベースのストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-467">The **InsertFunction** element in mapping specification language (MSL) maps the insert function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="3a761-468">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-468">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="3a761-469">詳しくは、「Function 要素 (SSDL)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3a761-469">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="3a761-470">エンティティ型の3つの挿入、更新、または削除操作をすべてストアドプロシージャにマップしない場合、実行時にマップされていない操作は失敗し、UpdateException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="3a761-470">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="3a761-471">**Insertfunction**要素は ModificationFunctionMapping 要素の子にすることができ、entitytypemapping 要素または AssociationSetMapping 要素に適用されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-471">The **InsertFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

### <a name="insertfunction-applied-to-entitytypemapping"></a><span data-ttu-id="3a761-472">EntityTypeMapping への InsertFunction の適用</span><span class="sxs-lookup"><span data-stu-id="3a761-472">InsertFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="3a761-473">EntityTypeMapping 要素に適用される場合、 **Insertfunction** 要素は、概念モデルのエンティティ型の挿入関数をストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-473">When applied to the EntityTypeMapping element, the **InsertFunction** element maps the insert function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="3a761-474">**Insertfunction**要素には、 **entitytypemapping**要素に適用するときに次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-474">The **InsertFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="3a761-475">AssociationEnd (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-475">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="3a761-476">ComplexProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-476">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="3a761-477">ResultBinding (0 または 1 個)</span><span class="sxs-lookup"><span data-stu-id="3a761-477">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="3a761-478">ScarlarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-478">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="3a761-479">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-479">Applicable Attributes</span></span>

<span data-ttu-id="3a761-480">次の表では、 **Entitytypemapping**要素に適用するときに**insertfunction**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-480">The following table describes the attributes that can be applied to the **InsertFunction** element when applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="3a761-481">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-481">Attribute Name</span></span>            | <span data-ttu-id="3a761-482">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-482">Is Required</span></span> | <span data-ttu-id="3a761-483">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-483">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-484">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="3a761-484">**FunctionName**</span></span>          | <span data-ttu-id="3a761-485">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-485">Yes</span></span>         | <span data-ttu-id="3a761-486">挿入関数のマップ先であるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="3a761-486">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="3a761-487">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-487">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="3a761-488">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="3a761-488">**RowsAffectedParameter**</span></span> | <span data-ttu-id="3a761-489">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-489">No</span></span>          | <span data-ttu-id="3a761-490">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-490">The name of the output parameter that returns the number of affected rows.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="3a761-491">例</span><span class="sxs-lookup"><span data-stu-id="3a761-491">Example</span></span>

<span data-ttu-id="3a761-492">次の例は、School モデルに基づいており、Person エンティティ型の insert 関数を**Insertfunction**ストアドプロシージャにマップするために使用される**insertfunction**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-492">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the Person entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="3a761-493">**Insertperson**ストアドプロシージャは、ストレージモデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="3a761-493">The **InsertPerson** stored procedure is declared in the storage model.</span></span>

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
### <a name="insertfunction-applied-to-associationsetmapping"></a><span data-ttu-id="3a761-494">AssociationSetMapping への InsertFunction の適用</span><span class="sxs-lookup"><span data-stu-id="3a761-494">InsertFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="3a761-495">**Insertfunction**要素を AssociationSetMapping 要素に適用すると、概念モデルのアソシエーションの挿入関数をストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-495">When applied to the AssociationSetMapping element, the **InsertFunction** element maps the insert function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="3a761-496">**Insertfunction**要素には、 **AssociationSetMapping**要素に適用するときに次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-496">The **InsertFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="3a761-497">EndProperty</span><span class="sxs-lookup"><span data-stu-id="3a761-497">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="3a761-498">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-498">Applicable Attributes</span></span>

<span data-ttu-id="3a761-499">次の表では、 **Insertfunction** 要素が **AssociationSetMapping** 要素に適用されるときに適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-499">The following table describes the attributes that can be applied to the **InsertFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="3a761-500">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-500">Attribute Name</span></span>            | <span data-ttu-id="3a761-501">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-501">Is Required</span></span> | <span data-ttu-id="3a761-502">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-502">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-503">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="3a761-503">**FunctionName**</span></span>          | <span data-ttu-id="3a761-504">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-504">Yes</span></span>         | <span data-ttu-id="3a761-505">挿入関数のマップ先であるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="3a761-505">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="3a761-506">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-506">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="3a761-507">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="3a761-507">**RowsAffectedParameter**</span></span> | <span data-ttu-id="3a761-508">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-508">No</span></span>          | <span data-ttu-id="3a761-509">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-509">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="3a761-510">例</span><span class="sxs-lookup"><span data-stu-id="3a761-510">Example</span></span>

<span data-ttu-id="3a761-511">次の例は、School モデルに基づいており、 **CourseInstructor** association の insert 関数を**InsertCourseInstructor**ストアドプロシージャにマップするために使用される**insertfunction**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-511">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the **CourseInstructor** association to the **InsertCourseInstructor** stored procedure.</span></span> <span data-ttu-id="3a761-512">**InsertCourseInstructor**ストアドプロシージャは、ストレージモデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="3a761-512">The **InsertCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="mapping-element-msl"></a><span data-ttu-id="3a761-513">Mapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-513">Mapping Element (MSL)</span></span>

<span data-ttu-id="3a761-514">マッピングスキーマ言語 (MSL) の **mapping** 要素には、概念モデルで定義されているオブジェクトをデータベースにマップするための情報が含まれています (ストレージモデルの説明を参照)。</span><span class="sxs-lookup"><span data-stu-id="3a761-514">The **Mapping** element in mapping specification language (MSL) contains information for mapping objects that are defined in a conceptual model to a database (as described in a storage model).</span></span> <span data-ttu-id="3a761-515">詳細については、「CSDL 仕様」と「SSDL 仕様」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-515">For more information, see CSDL Specification and SSDL Specification.</span></span>

<span data-ttu-id="3a761-516">**Mapping**要素は、マッピングスキーマのルート要素です。</span><span class="sxs-lookup"><span data-stu-id="3a761-516">The **Mapping** element is the root element for a mapping specification.</span></span> <span data-ttu-id="3a761-517">マッピング仕様の XML 名前空間は https://schemas.microsoft.com/ado/2009/11/mapping/cs です。</span><span class="sxs-lookup"><span data-stu-id="3a761-517">The XML namespace for mapping specifications is https://schemas.microsoft.com/ado/2009/11/mapping/cs.</span></span>

<span data-ttu-id="3a761-518">マッピング要素には、次の子要素をここに示す順序で含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-518">The mapping element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a761-519">Alias(0 個以上)。</span><span class="sxs-lookup"><span data-stu-id="3a761-519">Alias (zero or more)</span></span>
-   <span data-ttu-id="3a761-520">EntityContainerMapping (1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="3a761-520">EntityContainerMapping (exactly one)</span></span>

<span data-ttu-id="3a761-521">MSL で参照される概念モデル型およびストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-521">Names of conceptual and storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="3a761-522">概念モデルの名前空間の名前については、「Schema 要素 (CSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-522">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="3a761-523">ストレージモデルの名前空間の名前については、「Schema 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-523">For information about the storage model namespace name, see Schema Element (SSDL).</span></span> <span data-ttu-id="3a761-524">MSL で使用される名前空間の別名は、Alias 要素で定義できます。</span><span class="sxs-lookup"><span data-stu-id="3a761-524">Aliases for namespaces that are used in MSL can be defined with the Alias element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-525">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-525">Applicable Attributes</span></span>

<span data-ttu-id="3a761-526">次の表では、 **マッピング** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-526">The table below describes the attributes that can be applied to the **Mapping** element.</span></span>

| <span data-ttu-id="3a761-527">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-527">Attribute Name</span></span> | <span data-ttu-id="3a761-528">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-528">Is Required</span></span> | <span data-ttu-id="3a761-529">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-529">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="3a761-530">**行間**</span><span class="sxs-lookup"><span data-stu-id="3a761-530">**Space**</span></span>      | <span data-ttu-id="3a761-531">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-531">Yes</span></span>         | <span data-ttu-id="3a761-532">**C-S**。</span><span class="sxs-lookup"><span data-stu-id="3a761-532">**C-S**.</span></span> <span data-ttu-id="3a761-533">これは固定値で、変更できません。</span><span class="sxs-lookup"><span data-stu-id="3a761-533">This is a fixed value and cannot be changed.</span></span> |

### <a name="example"></a><span data-ttu-id="3a761-534">例</span><span class="sxs-lookup"><span data-stu-id="3a761-534">Example</span></span>

<span data-ttu-id="3a761-535">次の例は、School モデルの一部に基づく **マッピング** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-535">The following example shows a **Mapping** element that is based on part of the School model.</span></span> <span data-ttu-id="3a761-536">School モデルの詳細については、「クイックスタート (Entity Framework)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-536">For more information about the School model, see Quickstart (Entity Framework):</span></span>

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

## <a name="mappingfragment-element-msl"></a><span data-ttu-id="3a761-537">MappingFragment 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-537">MappingFragment Element (MSL)</span></span>

<span data-ttu-id="3a761-538">マッピングスキーマ言語 (MSL) の **Mappingfragment** 要素は、概念モデルのエンティティ型のプロパティと、データベース内のテーブルまたはビューの間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-538">The **MappingFragment** element in mapping specification language (MSL) defines the mapping between the properties of a conceptual model entity type and a table or view in the database.</span></span> <span data-ttu-id="3a761-539">概念モデルのエンティティ型および基になるデータベース テーブルまたはビューの詳細については、「EntityType 要素 (CSDL)」および「EntitySet 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-539">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="3a761-540">**Mappingfragment**は、entitytypemapping 要素または EntitySetMapping 要素の子要素にすることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-540">The **MappingFragment** can be a child element of the EntityTypeMapping element or the EntitySetMapping element.</span></span>

<span data-ttu-id="3a761-541">**Mappingfragment**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-541">The **MappingFragment** element can have the following child elements:</span></span>

-   <span data-ttu-id="3a761-542">ComplexType (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-542">ComplexType (zero or more)</span></span>
-   <span data-ttu-id="3a761-543">ScalarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-543">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="3a761-544">Condition (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-544">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-545">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-545">Applicable Attributes</span></span>

<span data-ttu-id="3a761-546">次の表では、 **Mappingfragment** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-546">The following table describes the attributes that can be applied to the **MappingFragment** element.</span></span>

| <span data-ttu-id="3a761-547">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-547">Attribute Name</span></span>          | <span data-ttu-id="3a761-548">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-548">Is Required</span></span> | <span data-ttu-id="3a761-549">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-549">Value</span></span>                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-550">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="3a761-550">**StoreEntitySet**</span></span>      | <span data-ttu-id="3a761-551">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-551">Yes</span></span>         | <span data-ttu-id="3a761-552">マップされるテーブルまたはビューの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-552">The name of the table or view that is being mapped.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="3a761-553">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="3a761-553">**MakeColumnsDistinct**</span></span> | <span data-ttu-id="3a761-554">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-554">No</span></span>          | <span data-ttu-id="3a761-555">個別の行のみが返されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="3a761-555">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="3a761-556">この属性が **True**に設定されている場合は、EntityContainerMapping 要素の **generateupdateviews** 属性が **False**に設定されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-556">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

### <a name="example"></a><span data-ttu-id="3a761-557">例</span><span class="sxs-lookup"><span data-stu-id="3a761-557">Example</span></span>

<span data-ttu-id="3a761-558">次の例では、 **Entitytypemapping**要素の子として**mappingfragment**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-558">The following example shows a **MappingFragment** element as the child of an **EntityTypeMapping** element.</span></span> <span data-ttu-id="3a761-559">この例では、概念モデルの **コース** の種類のプロパティは、データベース内の **course** テーブルの列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="3a761-559">In this example, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="3a761-560">例</span><span class="sxs-lookup"><span data-stu-id="3a761-560">Example</span></span>

<span data-ttu-id="3a761-561">次の例は、 **EntitySetMapping**要素の子としての**mappingfragment**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-561">The following example shows a **MappingFragment** element as the child of an **EntitySetMapping** element.</span></span> <span data-ttu-id="3a761-562">上の例のように、概念モデルの **コース** の種類のプロパティは、データベース内の **course** テーブルの列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="3a761-562">As in the example above, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

## <a name="modificationfunctionmapping-element-msl"></a><span data-ttu-id="3a761-563">ModificationFunctionMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-563">ModificationFunctionMapping Element (MSL)</span></span>

<span data-ttu-id="3a761-564">マッピング仕様言語 (MSL) の **ModificationFunctionMapping** 要素は、概念モデルのエンティティ型の挿入、更新、および削除の各関数を、基になるデータベースのストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-564">The **ModificationFunctionMapping** element in mapping specification language (MSL) maps the insert, update, and delete functions of a conceptual model entity type to stored procedures in the underlying database.</span></span> <span data-ttu-id="3a761-565">**ModificationFunctionMapping**要素は、概念モデル内の多対多のアソシエーションの挿入関数と削除関数を、基になるデータベースのストアドプロシージャにマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="3a761-565">The **ModificationFunctionMapping** element can also map the insert and delete functions for many-to-many associations in the conceptual model to stored procedures in the underlying database.</span></span> <span data-ttu-id="3a761-566">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-566">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="3a761-567">詳しくは、「Function 要素 (SSDL)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3a761-567">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="3a761-568">エンティティ型の3つの挿入、更新、または削除操作をすべてストアドプロシージャにマップしない場合、実行時にマップされていない操作は失敗し、UpdateException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="3a761-568">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>


> [!NOTE]
> <span data-ttu-id="3a761-569">継承階層の 1 つのエンティティの変更関数をストアド プロシージャにマップした場合、階層内のすべての型の変更関数がストアド プロシージャにマップされる必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-569">If the modification functions for one entity in an inheritance hierarchy are mapped to stored procedures, then modification functions for all types in the hierarchy must be mapped to stored procedures.</span></span>

<span data-ttu-id="3a761-570">**ModificationFunctionMapping**要素は、entitytypemapping 要素または AssociationSetMapping 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-570">The **ModificationFunctionMapping** element can be a child of the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

<span data-ttu-id="3a761-571">**ModificationFunctionMapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-571">The **ModificationFunctionMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="3a761-572">DeleteFunction (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="3a761-572">DeleteFunction (zero or one)</span></span>
-   <span data-ttu-id="3a761-573">InsertFunction (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="3a761-573">InsertFunction (zero or one)</span></span>
-   <span data-ttu-id="3a761-574">UpdateFunction (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="3a761-574">UpdateFunction (zero or one)</span></span>

<span data-ttu-id="3a761-575">**ModificationFunctionMapping**要素に適用できる属性はありません。</span><span class="sxs-lookup"><span data-stu-id="3a761-575">No attributes are applicable to the **ModificationFunctionMapping** element.</span></span>

### <a name="example"></a><span data-ttu-id="3a761-576">例</span><span class="sxs-lookup"><span data-stu-id="3a761-576">Example</span></span>

<span data-ttu-id="3a761-577">次の例は、School モデルの **People** エンティティセットのエンティティセットマッピングを示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-577">The following example shows the entity set mapping for the **People** entity set in the School model.</span></span> <span data-ttu-id="3a761-578">**Person**エンティティ型の列マッピングに加えて、 **person**型の挿入関数、更新関数、および削除関数のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-578">In addition to the column mapping for the **Person** entity type, the mapping of the insert, update, and delete functions of the **Person** type are shown.</span></span> <span data-ttu-id="3a761-579">ストレージ モデルにマップされて宣言されている関数。</span><span class="sxs-lookup"><span data-stu-id="3a761-579">The functions that are mapped to are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="3a761-580">例</span><span class="sxs-lookup"><span data-stu-id="3a761-580">Example</span></span>

<span data-ttu-id="3a761-581">次の例は、School モデルの **CourseInstructor** association セットのアソシエーションセットマッピングを示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-581">The following example shows the association set mapping for the **CourseInstructor** association set in the School model.</span></span> <span data-ttu-id="3a761-582">**CourseInstructor** association の列マッピングに加えて、 **CourseInstructor**アソシエーションの挿入関数と削除関数のマッピングが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-582">In addition to the column mapping for the **CourseInstructor** association, the mapping of the insert and delete functions of the **CourseInstructor** association are shown.</span></span> <span data-ttu-id="3a761-583">ストレージ モデルにマップされて宣言されている関数。</span><span class="sxs-lookup"><span data-stu-id="3a761-583">The functions that are mapped to are declared in the storage model.</span></span>

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
 

 

## <a name="queryview-element-msl"></a><span data-ttu-id="3a761-584">QueryView 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-584">QueryView Element (MSL)</span></span>

<span data-ttu-id="3a761-585">マッピング仕様言語 (MSL) の **QueryView** 要素は、概念モデルのエンティティ型またはアソシエーションと基になるデータベース内のテーブルとの間の読み取り専用のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-585">The **QueryView** element in mapping specification language (MSL) defines a read-only mapping between an entity type or association in the conceptual model and a table in the underlying database.</span></span> <span data-ttu-id="3a761-586">マッピングは、ストレージモデルに対して評価される Entity SQL クエリで定義され、結果セットを概念モデルのエンティティまたはアソシエーションの観点から表現します。</span><span class="sxs-lookup"><span data-stu-id="3a761-586">The mapping is defined with an Entity SQL query that is evaluated against the storage model, and you express the result set in terms of an entity or association in the conceptual model.</span></span> <span data-ttu-id="3a761-587">クエリ ビューは読み取り専用であるため、標準の更新コマンドを使用してクエリ ビューで定義された型を更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="3a761-587">Because query views are read-only, you cannot use standard update commands to update types that are defined by query views.</span></span> <span data-ttu-id="3a761-588">型の更新を行うには、変更関数を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-588">You can make updates to these types by using modification functions.</span></span> <span data-ttu-id="3a761-589">詳細については、「方法: 変更関数をストアドプロシージャにマップする」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-589">For more information, see How to: Map Modification Functions to Stored Procedures.</span></span>

> [!NOTE]
> <span data-ttu-id="3a761-590">**QueryView**要素では、 **GroupBy**、group 集計、またはナビゲーションプロパティを含む Entity SQL 式はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="3a761-590">In the **QueryView** element, Entity SQL expressions that contain **GroupBy**, group aggregates, or navigation properties are not supported.</span></span>

 

<span data-ttu-id="3a761-591">**QueryView**要素は、EntitySetMapping 要素または AssociationSetMapping 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-591">The **QueryView** element can be a child of the EntitySetMapping element or the AssociationSetMapping element.</span></span> <span data-ttu-id="3a761-592">前者の場合、クエリ ビューは、概念モデルのエンティティの読み取り専用マッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-592">In the former case, the query view defines a read-only mapping for an entity in the conceptual model.</span></span> <span data-ttu-id="3a761-593">後者の場合、クエリ ビューは、概念モデルのアソシエーションの読み取り専用マッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-593">In the latter case, the query view defines a read-only mapping for an association in the conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="3a761-594">**AssociationSetMapping**要素が参照制約との関連付けの場合、 **AssociationSetMapping**要素は無視されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-594">If the **AssociationSetMapping** element is for an association with a referential constraint, the **AssociationSetMapping** element is ignored.</span></span> <span data-ttu-id="3a761-595">詳細については、「参照要素の参照」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-595">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="3a761-596">**QueryView**要素には、子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="3a761-596">The **QueryView** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-597">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-597">Applicable Attributes</span></span>

<span data-ttu-id="3a761-598">次の表では、 **QueryView** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-598">The following table describes the attributes that can be applied to the **QueryView** element.</span></span>

| <span data-ttu-id="3a761-599">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-599">Attribute Name</span></span> | <span data-ttu-id="3a761-600">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-600">Is Required</span></span> | <span data-ttu-id="3a761-601">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-601">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-602">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="3a761-602">**TypeName**</span></span>   | <span data-ttu-id="3a761-603">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-603">No</span></span>          | <span data-ttu-id="3a761-604">クエリ ビューによってマップされる概念モデル型の名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-604">The name of the conceptual model type that is being mapped by the query view.</span></span> |

### <a name="example"></a><span data-ttu-id="3a761-605">例</span><span class="sxs-lookup"><span data-stu-id="3a761-605">Example</span></span>

<span data-ttu-id="3a761-606">次の例では、 **EntitySetMapping**要素の子として**QueryView**要素を示し、School モデルの**Department**エンティティ型に対するクエリビューマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-606">The following example shows the **QueryView** element as a child of the **EntitySetMapping** element and defines a query view mapping for the **Department** entity type in the School Model.</span></span>

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

<span data-ttu-id="3a761-607">このクエリでは、ストレージモデルの **department** 型のメンバーのサブセットのみが返されるため、School モデルの **学科** の種類は、このマッピングに基づいて次のように変更されています。</span><span class="sxs-lookup"><span data-stu-id="3a761-607">Because the query only returns a subset of the members of the **Department** type in the storage model, the **Department** type in the School model has been modified based on this mapping as follows:</span></span>

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

### <a name="example"></a><span data-ttu-id="3a761-608">例</span><span class="sxs-lookup"><span data-stu-id="3a761-608">Example</span></span>

<span data-ttu-id="3a761-609">次の例では、 **AssociationSetMapping**要素の子として**QueryView**要素を示し、School モデルのアソシエーションの読み取り専用マッピングを定義し `FK_Course_Department` ます。</span><span class="sxs-lookup"><span data-stu-id="3a761-609">The next example shows the **QueryView** element as the child of an **AssociationSetMapping** element and defines a read-only mapping for the `FK_Course_Department` association in the School model.</span></span>

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
 
### <a name="comments"></a><span data-ttu-id="3a761-610">備考</span><span class="sxs-lookup"><span data-stu-id="3a761-610">Comments</span></span>

<span data-ttu-id="3a761-611">クエリ ビューを定義して、次のシナリオを実現できます。</span><span class="sxs-lookup"><span data-stu-id="3a761-611">You can define query views to enable the following scenarios:</span></span>

-   <span data-ttu-id="3a761-612">ストレージ モデルのエンティティのプロパティの一部を含まない、概念モデルのエンティティを定義する。</span><span class="sxs-lookup"><span data-stu-id="3a761-612">Define an entity in the conceptual model that doesn't include all the properties of the entity in the storage model.</span></span> <span data-ttu-id="3a761-613">これには、既定値を持たず、 **null** 値をサポートしていないプロパティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="3a761-613">This includes properties that do not have default values and do not support **null** values.</span></span>
-   <span data-ttu-id="3a761-614">ストレージ モデルの計算列を概念モデルのエンティティ型のプロパティにマップする。</span><span class="sxs-lookup"><span data-stu-id="3a761-614">Map computed columns in the storage model to properties of entity types in the conceptual model.</span></span>
-   <span data-ttu-id="3a761-615">概念モデルのエンティティをパーティション分割するために使用される条件が等価性に基づかないマッピングを定義する。</span><span class="sxs-lookup"><span data-stu-id="3a761-615">Define a mapping where conditions used to partition entities in the conceptual model are not based on equality.</span></span> <span data-ttu-id="3a761-616">**Condition**要素を使用して条件付きマッピングを指定する場合、指定された条件は指定した値と同じである必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-616">When you specify a conditional mapping using the **Condition** element, the supplied condition must equal the specified value.</span></span> <span data-ttu-id="3a761-617">詳細については、「Condition 要素 (MSL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-617">For more information, see Condition Element (MSL).</span></span>
-   <span data-ttu-id="3a761-618">ストレージ モデルの同一の列を概念モデルの複数の型にマップする。</span><span class="sxs-lookup"><span data-stu-id="3a761-618">Map the same column in the storage model to multiple types in the conceptual model.</span></span>
-   <span data-ttu-id="3a761-619">複数の型を同じテーブルにマップする。</span><span class="sxs-lookup"><span data-stu-id="3a761-619">Map multiple types to the same table.</span></span>
-   <span data-ttu-id="3a761-620">リレーショナル スキーマの外部キーに基づかない概念モデルのアソシエーションを定義する。</span><span class="sxs-lookup"><span data-stu-id="3a761-620">Define associations in the conceptual model that are not based on foreign keys in the relational schema.</span></span>
-   <span data-ttu-id="3a761-621">カスタム ビジネス ロジックを使用して、概念モデルのプロパティの値を設定する。</span><span class="sxs-lookup"><span data-stu-id="3a761-621">Use custom business logic to set the value of properties in the conceptual model.</span></span> <span data-ttu-id="3a761-622">たとえば、概念モデルでは、データソースの文字列値 "T" をブール値の **true**にマップできます。</span><span class="sxs-lookup"><span data-stu-id="3a761-622">For example, you could map the string value "T" in the data source to a value of **true**, a Boolean, in the conceptual model.</span></span>
-   <span data-ttu-id="3a761-623">クエリ結果に対する条件付きフィルターを定義する。</span><span class="sxs-lookup"><span data-stu-id="3a761-623">Define conditional filters for query results.</span></span>
-   <span data-ttu-id="3a761-624">概念モデルのデータに適用する制限をストレージ モデルよりも少なくする。</span><span class="sxs-lookup"><span data-stu-id="3a761-624">Enforce fewer restrictions on data in the conceptual model than in the storage model.</span></span> <span data-ttu-id="3a761-625">たとえば、概念モデルのプロパティを、マップ先の列で **null**値がサポートされていない場合でも、null 許容にすることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-625">For example, you could make a property in the conceptual model nullable even if the column to which it is mapped does not support **null**values.</span></span>

<span data-ttu-id="3a761-626">エンティティのクエリ ビューを定義する際は、次の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-626">The following considerations apply when you define query views for entities:</span></span>

-   <span data-ttu-id="3a761-627">クエリ ビューは読み取り専用です。</span><span class="sxs-lookup"><span data-stu-id="3a761-627">Query views are read-only.</span></span> <span data-ttu-id="3a761-628">エンティティの更新を行うには、変更関数を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-628">You can only make updates to entities by using modification functions.</span></span>
-   <span data-ttu-id="3a761-629">クエリ ビューでエンティティ型を定義する場合、すべての関連エンティティもクエリ ビューで定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-629">When you define an entity type by a query view, you must also define all related entities by query views.</span></span>
-   <span data-ttu-id="3a761-630">リレーショナルスキーマのリンクテーブルを表すストレージモデル内のエンティティに多対多のアソシエーションをマップする場合は、このリンクテーブルの**AssociationSetMapping**要素に**QueryView**要素を定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-630">When you map a many-to-many association to an entity in the storage model that represents a link table in the relational schema, you must define a **QueryView** element in the **AssociationSetMapping** element for this link table.</span></span>
-   <span data-ttu-id="3a761-631">クエリ ビューは、型階層のすべての型に対して定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-631">Query views must be defined for all types in a type hierarchy.</span></span> <span data-ttu-id="3a761-632">これは、次の方法で行うことができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-632">You can do this in the following ways:</span></span>
-   -   <span data-ttu-id="3a761-633">1つの **QueryView** 要素を使用して、階層内のすべてのエンティティ型の和集合を返す単一の Entity SQL クエリを指定します。</span><span class="sxs-lookup"><span data-stu-id="3a761-633">With a single **QueryView** element that specifies a single Entity SQL query that returns a union of all of the entity types in the hierarchy.</span></span>
    -   <span data-ttu-id="3a761-634">1つの **QueryView** 要素で、CASE 演算子を使用して特定の条件に基づいて階層内の特定のエンティティ型を返す単一の Entity SQL クエリを指定します。</span><span class="sxs-lookup"><span data-stu-id="3a761-634">With a single **QueryView** element that specifies a single Entity SQL query that uses the CASE operator to return a specific entity type in the hierarchy based on a specific condition.</span></span>
    -   <span data-ttu-id="3a761-635">階層内の特定の型に対して追加の **QueryView** 要素を使用します。</span><span class="sxs-lookup"><span data-stu-id="3a761-635">With an additional **QueryView** element for a specific type in the hierarchy.</span></span> <span data-ttu-id="3a761-636">この場合は、 **QueryView**要素の**TypeName**属性を使用して、各ビューのエンティティ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a761-636">In this case, use the **TypeName** attribute of the **QueryView** element to specify the entity type for each view.</span></span>
-   <span data-ttu-id="3a761-637">クエリビューが定義されている場合、 **EntitySetMapping**要素で**StorageSetName**属性を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="3a761-637">When a query view is defined, you cannot specify the **StorageSetName** attribute on the **EntitySetMapping** element.</span></span>
-   <span data-ttu-id="3a761-638">クエリビューが定義されている場合、 **EntitySetMapping**要素に **プロパティ** マッピングを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="3a761-638">When a query view is defined, the **EntitySetMapping**element cannot also contain **Property** mappings.</span></span>

## <a name="resultbinding-element-msl"></a><span data-ttu-id="3a761-639">ResultBinding 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-639">ResultBinding Element (MSL)</span></span>

<span data-ttu-id="3a761-640">マッピングスキーマ言語 (MSL) の **Resultbinding** 要素は、エンティティ型の変更関数が基になるデータベースのストアドプロシージャにマップされている場合に、ストアドプロシージャによって返される列の値を概念モデルのエンティティプロパティにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-640">The **ResultBinding** element in mapping specification language (MSL) maps column values that are returned by stored procedures to entity properties in the conceptual model when entity type modification functions are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="3a761-641">たとえば、id 列の値が insert ストアドプロシージャによって返される場合、 **Resultbinding** 要素は、返された値を概念モデルのエンティティ型プロパティにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-641">For example, when the value of an identity column is returned by an insert stored procedure, the **ResultBinding** element maps the returned value to an entity type property in the conceptual model.</span></span>

<span data-ttu-id="3a761-642">**Resultbinding**要素は、insertfunction 要素または updatefunction 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-642">The **ResultBinding** element can be child of the InsertFunction element or the UpdateFunction element.</span></span>

<span data-ttu-id="3a761-643">**Resultbinding**要素に子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="3a761-643">The **ResultBinding** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-644">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-644">Applicable Attributes</span></span>

<span data-ttu-id="3a761-645">次の表では、 **Resultbinding** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-645">The following table describes the attributes that are applicable to the **ResultBinding** element:</span></span>

| <span data-ttu-id="3a761-646">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-646">Attribute Name</span></span> | <span data-ttu-id="3a761-647">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-647">Is Required</span></span> | <span data-ttu-id="3a761-648">値</span><span class="sxs-lookup"><span data-stu-id="3a761-648">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-649">**名前**</span><span class="sxs-lookup"><span data-stu-id="3a761-649">**Name**</span></span>       | <span data-ttu-id="3a761-650">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-650">Yes</span></span>         | <span data-ttu-id="3a761-651">マップされる概念モデルのエンティティ プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-651">The name of the entity property in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="3a761-652">**[ColumnName]**</span><span class="sxs-lookup"><span data-stu-id="3a761-652">**ColumnName**</span></span> | <span data-ttu-id="3a761-653">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-653">Yes</span></span>         | <span data-ttu-id="3a761-654">マップされる列の名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-654">The name of the column being mapped.</span></span>                                          |

### <a name="example"></a><span data-ttu-id="3a761-655">例</span><span class="sxs-lookup"><span data-stu-id="3a761-655">Example</span></span>

<span data-ttu-id="3a761-656">次の例は、School モデルに基づいており、 **Person**エンティティ型の挿入関数を**insertfunction**ストアドプロシージャにマップするために使用される**insertfunction**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-656">The following example is based on the School model and shows an **InsertFunction** element used to map the insert function of the **Person** entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="3a761-657">( **Insertperson** ストアドプロシージャは次のようになり、ストレージモデルで宣言されています)。 **Resultbinding** 要素は、ストアドプロシージャ (**NewPersonID**) によって返される列の値をエンティティ型のプロパティ (**PersonID**) にマップするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-657">(The **InsertPerson** stored procedure is shown below and is declared in the storage model.) A **ResultBinding** element is used to map a column value that is returned by the stored procedure (**NewPersonID**) to an entity type property (**PersonID**).</span></span>

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

<span data-ttu-id="3a761-658">次の Transact-sql では、 **Insertperson** ストアドプロシージャについて説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-658">The following Transact-SQL describes the **InsertPerson** stored procedure:</span></span>

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

## <a name="resultmapping-element-msl"></a><span data-ttu-id="3a761-659">ResultMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-659">ResultMapping Element (MSL)</span></span>

<span data-ttu-id="3a761-660">マッピングスキーマ言語 (MSL) の **Resultmapping** 要素は、次の条件に該当する場合に、概念モデルの関数インポートと基になるデータベースのストアドプロシージャとの間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="3a761-660">The **ResultMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="3a761-661">関数インポートが概念モデルのエンティティ型または複合型を返す場合</span><span class="sxs-lookup"><span data-stu-id="3a761-661">The function import returns a conceptual model entity type or complex type.</span></span>
-   <span data-ttu-id="3a761-662">ストアド プロシージャが返す列の名前が、エンティティ型または複合型のプロパティの名前と完全には一致しない場合</span><span class="sxs-lookup"><span data-stu-id="3a761-662">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the entity type or complex type.</span></span>

<span data-ttu-id="3a761-663">既定では、ストアド プロシージャが返す列とエンティティ型または複合型とのマッピングは、列およびプロパティの名前に基づいています。</span><span class="sxs-lookup"><span data-stu-id="3a761-663">By default, the mapping between the columns returned by a stored procedure and an entity type or complex type is based on column and property names.</span></span> <span data-ttu-id="3a761-664">列名がプロパティ名と完全に一致しない場合は、 **resultmapping** 要素を使用してマッピングを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-664">If column names do not exactly match property names, you must use the **ResultMapping** element to define the mapping.</span></span> <span data-ttu-id="3a761-665">既定のマッピングの例については、「FunctionImportMapping 要素 (MSL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a761-665">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="3a761-666">**Resultmapping**要素は、FunctionImportMapping 要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="3a761-666">The **ResultMapping** element is a child element of the FunctionImportMapping element.</span></span>

<span data-ttu-id="3a761-667">**Resultmapping**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-667">The **ResultMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="3a761-668">EntityTypeMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-668">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="3a761-669">ComplexTypeMapping</span><span class="sxs-lookup"><span data-stu-id="3a761-669">ComplexTypeMapping</span></span>

<span data-ttu-id="3a761-670">**Resultmapping**要素に適用できる属性はありません。</span><span class="sxs-lookup"><span data-stu-id="3a761-670">No attributes are applicable to the **ResultMapping** Element.</span></span>

### <a name="example"></a><span data-ttu-id="3a761-671">例</span><span class="sxs-lookup"><span data-stu-id="3a761-671">Example</span></span>

<span data-ttu-id="3a761-672">次のストアド プロシージャについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="3a761-672">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="3a761-673">また、次の概念モデルのエンティティ型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="3a761-673">Also consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="3a761-674">以前のエンティティ型のインスタンスを返す関数インポートを作成するには、ストアドプロシージャによって返された列とエンティティ型の間のマッピングが **Resultmapping** 要素で定義されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-674">In order to create a function import that returns instances of the previous entity type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ResultMapping** element:</span></span>

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

## <a name="scalarproperty-element-msl"></a><span data-ttu-id="3a761-675">ScalarProperty 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-675">ScalarProperty Element (MSL)</span></span>

<span data-ttu-id="3a761-676">マッピング仕様言語 (MSL) の **ScalarProperty** 要素は、概念モデルのエンティティ型、複合型、またはアソシエーションのプロパティを、基になるデータベースのテーブル列またはストアドプロシージャパラメーターにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-676">The **ScalarProperty** element in mapping specification language (MSL) maps a property on a conceptual model entity type, complex type, or association to a table column or stored procedure parameter in the underlying database.</span></span>

> [!NOTE]
> <span data-ttu-id="3a761-677">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-677">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="3a761-678">詳しくは、「Function 要素 (SSDL)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3a761-678">For more information, see Function Element (SSDL).</span></span>

<span data-ttu-id="3a761-679">**ScalarProperty**要素は、次の要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-679">The **ScalarProperty** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="3a761-680">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="3a761-680">MappingFragment</span></span>
-   <span data-ttu-id="3a761-681">InsertFunction</span><span class="sxs-lookup"><span data-stu-id="3a761-681">InsertFunction</span></span>
-   <span data-ttu-id="3a761-682">UpdateFunction</span><span class="sxs-lookup"><span data-stu-id="3a761-682">UpdateFunction</span></span>
-   <span data-ttu-id="3a761-683">DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="3a761-683">DeleteFunction</span></span>
-   <span data-ttu-id="3a761-684">EndProperty</span><span class="sxs-lookup"><span data-stu-id="3a761-684">EndProperty</span></span>
-   <span data-ttu-id="3a761-685">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="3a761-685">ComplexProperty</span></span>
-   <span data-ttu-id="3a761-686">ResultMapping</span><span class="sxs-lookup"><span data-stu-id="3a761-686">ResultMapping</span></span>

<span data-ttu-id="3a761-687">**Mappingfragment**、 **complexproperty**、または**endproperty**要素の子として、 **ScalarProperty**要素は、概念モデルのプロパティをデータベースの列にマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-687">As a child of the **MappingFragment**, **ComplexProperty**, or **EndProperty** element, the **ScalarProperty** element maps a property in the conceptual model to a column in the database.</span></span> <span data-ttu-id="3a761-688">**Insertfunction**、 **updatefunction**、または**deletefunction**要素の子として、 **ScalarProperty**要素は、概念モデルのプロパティをストアドプロシージャパラメーターにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-688">As a child of the **InsertFunction**, **UpdateFunction**, or **DeleteFunction** element, the **ScalarProperty** element maps a property in the conceptual model to a stored procedure parameter.</span></span>

<span data-ttu-id="3a761-689">**ScalarProperty**要素には、子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="3a761-689">The **ScalarProperty** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-690">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-690">Applicable Attributes</span></span>

<span data-ttu-id="3a761-691">**ScalarProperty**要素に適用される属性は、要素の役割によって異なります。</span><span class="sxs-lookup"><span data-stu-id="3a761-691">The attributes that apply to the **ScalarProperty** element differ depending on the role of the element.</span></span>

<span data-ttu-id="3a761-692">次の表では、 **ScalarProperty** 要素を使用して、概念モデルのプロパティをデータベースの列にマップするときに適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-692">The following table describes the attributes that are applicable when the **ScalarProperty** element is used to map a conceptual model property to a column in the database:</span></span>

| <span data-ttu-id="3a761-693">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-693">Attribute Name</span></span> | <span data-ttu-id="3a761-694">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-694">Is Required</span></span> | <span data-ttu-id="3a761-695">値</span><span class="sxs-lookup"><span data-stu-id="3a761-695">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="3a761-696">**名前**</span><span class="sxs-lookup"><span data-stu-id="3a761-696">**Name**</span></span>       | <span data-ttu-id="3a761-697">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-697">Yes</span></span>         | <span data-ttu-id="3a761-698">マップされる概念モデルのプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-698">The name of the conceptual model property that is being mapped.</span></span> |
| <span data-ttu-id="3a761-699">**[ColumnName]**</span><span class="sxs-lookup"><span data-stu-id="3a761-699">**ColumnName**</span></span> | <span data-ttu-id="3a761-700">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-700">Yes</span></span>         | <span data-ttu-id="3a761-701">マップされるテーブル列の名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-701">The name of the table column that is being mapped.</span></span>              |

<span data-ttu-id="3a761-702">次の表では、 **ScalarProperty** 要素を使用して、概念モデルのプロパティをストアドプロシージャのパラメーターにマップするときに適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-702">The following table describes the attributes that are applicable to the **ScalarProperty** element when it is used to map a conceptual model property to a stored procedure parameter:</span></span>

| <span data-ttu-id="3a761-703">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-703">Attribute Name</span></span>    | <span data-ttu-id="3a761-704">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-704">Is Required</span></span> | <span data-ttu-id="3a761-705">値</span><span class="sxs-lookup"><span data-stu-id="3a761-705">Value</span></span>                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-706">**名前**</span><span class="sxs-lookup"><span data-stu-id="3a761-706">**Name**</span></span>          | <span data-ttu-id="3a761-707">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-707">Yes</span></span>         | <span data-ttu-id="3a761-708">マップされる概念モデルのプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-708">The name of the conceptual model property that is being mapped.</span></span>                                                                                 |
| <span data-ttu-id="3a761-709">**ParameterName**</span><span class="sxs-lookup"><span data-stu-id="3a761-709">**ParameterName**</span></span> | <span data-ttu-id="3a761-710">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-710">Yes</span></span>         | <span data-ttu-id="3a761-711">マップされるパラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-711">The name of the parameter that is being mapped.</span></span>                                                                                                 |
| <span data-ttu-id="3a761-712">**バージョン**</span><span class="sxs-lookup"><span data-stu-id="3a761-712">**Version**</span></span>       | <span data-ttu-id="3a761-713">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-713">No</span></span>          | <span data-ttu-id="3a761-714">現在の値またはプロパティの元の値を同時実行チェックに使用するかどうかによって、 **current**または**original** 。</span><span class="sxs-lookup"><span data-stu-id="3a761-714">**Current** or **Original** depending on whether the current value or the original value of the property should be used for concurrency checks.</span></span> |

### <a name="example"></a><span data-ttu-id="3a761-715">例</span><span class="sxs-lookup"><span data-stu-id="3a761-715">Example</span></span>

<span data-ttu-id="3a761-716">次の例は、2つの方法で使用される **ScalarProperty** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-716">The following example shows the **ScalarProperty** element used in two ways:</span></span>

-   <span data-ttu-id="3a761-717">**Person エンティティ型**のプロパティを**person**テーブルの列にマップする。</span><span class="sxs-lookup"><span data-stu-id="3a761-717">To map the properties of the **Person** entity type to the columns of the **Person**table.</span></span>
-   <span data-ttu-id="3a761-718">**Person**エンティティ型のプロパティを**updateperson**ストアドプロシージャのパラメーターにマップする。</span><span class="sxs-lookup"><span data-stu-id="3a761-718">To map the properties of the **Person** entity type to the parameters of the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="3a761-719">ストアド プロシージャはストレージ モデル内で宣言されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-719">The stored procedures are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="3a761-720">例</span><span class="sxs-lookup"><span data-stu-id="3a761-720">Example</span></span>

<span data-ttu-id="3a761-721">次の例は、概念モデルのアソシエーションの挿入関数と削除関数をデータベース内のストアドプロシージャにマップするために使用される **ScalarProperty** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-721">The next example shows the **ScalarProperty** element used to map the insert and delete functions of a conceptual model association to stored procedures in the database.</span></span> <span data-ttu-id="3a761-722">ストアド プロシージャはストレージ モデル内で宣言されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-722">The stored procedures are declared in the storage model.</span></span>

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

## <a name="updatefunction-element-msl"></a><span data-ttu-id="3a761-723">UpdateFunction 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="3a761-723">UpdateFunction Element (MSL)</span></span>

<span data-ttu-id="3a761-724">マッピングスキーマ言語 (MSL) の **updatefunction** 要素は、概念モデルのエンティティ型の更新関数を、基になるデータベースのストアドプロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a761-724">The **UpdateFunction** element in mapping specification language (MSL) maps the update function of an entity type in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="3a761-725">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-725">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="3a761-726">詳しくは、「Function 要素 (SSDL)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="3a761-726">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
>  <span data-ttu-id="3a761-727">エンティティ型の3つの挿入、更新、または削除操作をすべてストアドプロシージャにマップしない場合、実行時にマップされていない操作は失敗し、UpdateException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="3a761-727">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="3a761-728">**Updatefunction**要素は ModificationFunctionMapping 要素の子にすることができ、entitytypemapping 要素に適用されます。</span><span class="sxs-lookup"><span data-stu-id="3a761-728">The **UpdateFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element.</span></span>

<span data-ttu-id="3a761-729">**Updatefunction**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a761-729">The **UpdateFunction** element can have the following child elements:</span></span>

-   <span data-ttu-id="3a761-730">AssociationEnd (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-730">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="3a761-731">ComplexProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-731">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="3a761-732">ResultBinding (0 または 1 個)</span><span class="sxs-lookup"><span data-stu-id="3a761-732">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="3a761-733">ScarlarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a761-733">ScarlarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a761-734">該当する属性</span><span class="sxs-lookup"><span data-stu-id="3a761-734">Applicable Attributes</span></span>

<span data-ttu-id="3a761-735">次の表では、 **Updatefunction** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="3a761-735">The following table describes the attributes that can be applied to the **UpdateFunction** element.</span></span>

| <span data-ttu-id="3a761-736">属性名</span><span class="sxs-lookup"><span data-stu-id="3a761-736">Attribute Name</span></span>            | <span data-ttu-id="3a761-737">必須</span><span class="sxs-lookup"><span data-stu-id="3a761-737">Is Required</span></span> | <span data-ttu-id="3a761-738">[値]</span><span class="sxs-lookup"><span data-stu-id="3a761-738">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a761-739">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="3a761-739">**FunctionName**</span></span>          | <span data-ttu-id="3a761-740">はい</span><span class="sxs-lookup"><span data-stu-id="3a761-740">Yes</span></span>         | <span data-ttu-id="3a761-741">更新関数のマップ先となるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="3a761-741">The namespace-qualified name of the stored procedure to which the update function is mapped.</span></span> <span data-ttu-id="3a761-742">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a761-742">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="3a761-743">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="3a761-743">**RowsAffectedParameter**</span></span> | <span data-ttu-id="3a761-744">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a761-744">No</span></span>          | <span data-ttu-id="3a761-745">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="3a761-745">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

### <a name="example"></a><span data-ttu-id="3a761-746">例</span><span class="sxs-lookup"><span data-stu-id="3a761-746">Example</span></span>

<span data-ttu-id="3a761-747">次の例は、School モデルに基づいており、 **Person**エンティティ型の update 関数を**updatefunction**ストアドプロシージャにマップするために使用される**updatefunction**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="3a761-747">The following example is based on the School model and shows the **UpdateFunction** element used to map update function of the **Person** entity type to the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="3a761-748">**Updateperson**ストアドプロシージャは、ストレージモデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="3a761-748">The **UpdatePerson** stored procedure is declared in the storage model.</span></span>

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
