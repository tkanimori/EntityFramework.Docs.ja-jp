---
title: EF6 の MSL 仕様
author: divega
ms.date: 10/23/2016
ms.assetid: 13ae7bc1-74b4-4ee4-8d73-c337be841467
ms.openlocfilehash: 6bff1f5407bc0546e60b5bee1178be9aa4748bd8
ms.sourcegitcommit: 29f928a6116771fe78f306846e6f2d45cbe8d1f4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460138"
---
# <a name="msl-specification"></a><span data-ttu-id="8176c-102">MSL 仕様</span><span class="sxs-lookup"><span data-stu-id="8176c-102">MSL Specification</span></span>
<span data-ttu-id="8176c-103">マッピング仕様言語 (MSL) は、概念モデルと Entity Framework アプリケーションのストレージ モデル間のマッピングを記述する XML ベースの言語です。</span><span class="sxs-lookup"><span data-stu-id="8176c-103">Mapping specification language (MSL) is an XML-based language that describes the mapping between the conceptual model and storage model of an Entity Framework application.</span></span>

<span data-ttu-id="8176c-104">Entity Framework アプリケーションでは、マッピング メタデータはビルド時に .msl ファイル (MSL で記述) から読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8176c-104">In an Entity Framework application, mapping metadata is loaded from an .msl file (written in MSL) at build time.</span></span> <span data-ttu-id="8176c-105">Entity Framework では、実行時にマッピング メタデータを使用して、ストア固有のコマンドを概念モデルに対するクエリに変換します。</span><span class="sxs-lookup"><span data-stu-id="8176c-105">Entity Framework uses mapping metadata at runtime to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="8176c-106">Entity Framework デザイナー (EF Designer) では、デザイン時に .edmx ファイルのマッピング情報を格納します。</span><span class="sxs-lookup"><span data-stu-id="8176c-106">The Entity Framework Designer (EF Designer) stores mapping information in an .edmx file at design time.</span></span> <span data-ttu-id="8176c-107">ビルド時に、エンティティ デザイナー情報を使用して .edmx ファイルの実行時に Entity Framework によって必要な .msl ファイルを作成するには</span><span class="sxs-lookup"><span data-stu-id="8176c-107">At build time, the Entity Designer uses information in an .edmx file to create the .msl file that is needed by Entity Framework at runtime</span></span>

<span data-ttu-id="8176c-108">MSL で参照されるすべての概念モデル型またはストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-108">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="8176c-109">概念モデル名前空間の名前については、次を参照してください。 [CSDL 仕様](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)します。</span><span class="sxs-lookup"><span data-stu-id="8176c-109">For information about the conceptual model namespace name, see [CSDL Specification](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md).</span></span> <span data-ttu-id="8176c-110">ストレージ モデル名前空間の名前については、次を参照してください。 [SSDL 仕様](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)します。</span><span class="sxs-lookup"><span data-stu-id="8176c-110">For information about the storage model namespace name, see [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md).</span></span>

<span data-ttu-id="8176c-111">MSL のバージョンは、XML 名前空間で区別されます。</span><span class="sxs-lookup"><span data-stu-id="8176c-111">Versions of MSL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="8176c-112">MSL のバージョン</span><span class="sxs-lookup"><span data-stu-id="8176c-112">MSL Version</span></span> | <span data-ttu-id="8176c-113">XML Namespace</span><span class="sxs-lookup"><span data-stu-id="8176c-113">XML Namespace</span></span>                                        |
|:------------|:-----------------------------------------------------|
| <span data-ttu-id="8176c-114">MSL v1</span><span class="sxs-lookup"><span data-stu-id="8176c-114">MSL v1</span></span>      | <span data-ttu-id="8176c-115">urn: スキーマ-microsoft-com:windows:storage:mapping:CS</span><span class="sxs-lookup"><span data-stu-id="8176c-115">urn:schemas-microsoft-com:windows:storage:mapping:CS</span></span> |
| <span data-ttu-id="8176c-116">MSL v2</span><span class="sxs-lookup"><span data-stu-id="8176c-116">MSL v2</span></span>      | http://schemas.microsoft.com/ado/2008/09/mapping/cs  |
| <span data-ttu-id="8176c-117">MSL v3</span><span class="sxs-lookup"><span data-stu-id="8176c-117">MSL v3</span></span>      | http://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a><span data-ttu-id="8176c-118">Alias 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-118">Alias Element (MSL)</span></span>

<span data-ttu-id="8176c-119">**エイリアス**マッピング仕様言語 (MSL) の要素は、概念モデルとストレージのモデル名前空間の別名を定義するために使用するマッピング要素の子。</span><span class="sxs-lookup"><span data-stu-id="8176c-119">The **Alias** element in mapping specification language (MSL) is a child of the Mapping element that is used to define aliases for conceptual model and storage model namespaces.</span></span> <span data-ttu-id="8176c-120">MSL で参照されるすべての概念モデル型またはストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-120">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="8176c-121">概念モデル名前空間の名前については、スキーマ要素 (CSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-121">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="8176c-122">ストレージ モデル名前空間の名前については、スキーマ要素 (SSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-122">For information about the storage model namespace name, see Schema Element (SSDL).</span></span>

<span data-ttu-id="8176c-123">**エイリアス**要素が子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="8176c-123">The **Alias** element cannot have child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-124">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-124">Applicable Attributes</span></span>

<span data-ttu-id="8176c-125">次の表に適用できる属性の説明、**エイリアス**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-125">The table below describes the attributes that can be applied to the **Alias** element.</span></span>

| <span data-ttu-id="8176c-126">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-126">Attribute Name</span></span> | <span data-ttu-id="8176c-127">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-127">Is Required</span></span> | <span data-ttu-id="8176c-128">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-128">Value</span></span>                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| <span data-ttu-id="8176c-129">**Key**</span><span class="sxs-lookup"><span data-stu-id="8176c-129">**Key**</span></span>        | <span data-ttu-id="8176c-130">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-130">Yes</span></span>         | <span data-ttu-id="8176c-131">指定された名前空間のエイリアス、**値**属性。</span><span class="sxs-lookup"><span data-stu-id="8176c-131">The alias for the namespace that is specified by the **Value** attribute.</span></span> |
| <span data-ttu-id="8176c-132">**[値]**</span><span class="sxs-lookup"><span data-stu-id="8176c-132">**Value**</span></span>      | <span data-ttu-id="8176c-133">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-133">Yes</span></span>         | <span data-ttu-id="8176c-134">対象の名前空間の値、**キー**要素は別名です。</span><span class="sxs-lookup"><span data-stu-id="8176c-134">The namespace for which the value of the **Key** element is an alias.</span></span>     |

### <a name="example"></a><span data-ttu-id="8176c-135">例</span><span class="sxs-lookup"><span data-stu-id="8176c-135">Example</span></span>

<span data-ttu-id="8176c-136">次の例は、**エイリアス**、エイリアスを定義する要素`c`、概念モデルで定義されている型。</span><span class="sxs-lookup"><span data-stu-id="8176c-136">The following example shows an **Alias** element that defines an alias, `c`, for types that are defined in the conceptual model.</span></span>

``` xml
 <Mapping Space="C-S"
          xmlns="http://schemas.microsoft.com/ado/2009/11/mapping/cs">
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

## <a name="associationend-element-msl"></a><span data-ttu-id="8176c-137">AssociationEnd 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-137">AssociationEnd Element (MSL)</span></span>

<span data-ttu-id="8176c-138">**AssociationEnd**基になるデータベース内のストアド プロシージャにマップされる概念モデルのエンティティ型の変更関数マッピング仕様言語 (MSL) の要素を使用します。</span><span class="sxs-lookup"><span data-stu-id="8176c-138">The **AssociationEnd** element in mapping specification language (MSL) is used when the modification functions of an entity type in the conceptual model are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="8176c-139">ストアド プロシージャは、関連付けのプロパティに値が保持されているパラメーターの変更の場合、 **AssociationEnd**要素プロパティの値をパラメーターにマップされます。</span><span class="sxs-lookup"><span data-stu-id="8176c-139">If a modification stored procedure takes a parameter whose value is held in an association property, the **AssociationEnd** element maps the property value to the parameter.</span></span> <span data-ttu-id="8176c-140">詳細については、下の例を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-140">For more information, see the example below.</span></span>

<span data-ttu-id="8176c-141">エンティティ型の変更関数をストアド プロシージャにマップする方法の詳細については、ModificationFunctionMapping 要素 (MSL) とチュートリアルを参照してください。 エンティティをストアド プロシージャにマッピングします。</span><span class="sxs-lookup"><span data-stu-id="8176c-141">For more information about mapping modification functions of entity types to stored procedures, see ModificationFunctionMapping Element (MSL) and Walkthrough: Mapping an Entity to Stored Procedures.</span></span>

<span data-ttu-id="8176c-142">**AssociationEnd**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-142">The **AssociationEnd** element can have the following child elements:</span></span>

-   <span data-ttu-id="8176c-143">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="8176c-143">ScalarProperty</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-144">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-144">Applicable Attributes</span></span>

<span data-ttu-id="8176c-145">次の表に、属性に適用される、 **AssociationEnd**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-145">The following table describes the attributes that are applicable to the **AssociationEnd** element.</span></span>

| <span data-ttu-id="8176c-146">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-146">Attribute Name</span></span>     | <span data-ttu-id="8176c-147">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-147">Is Required</span></span> | <span data-ttu-id="8176c-148">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-148">Value</span></span>                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-149">**AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="8176c-149">**AssociationSet**</span></span> | <span data-ttu-id="8176c-150">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-150">Yes</span></span>         | <span data-ttu-id="8176c-151">マップされるアソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-151">The name of the association that is being mapped.</span></span>                                                                                                                                 |
| <span data-ttu-id="8176c-152">**From**</span><span class="sxs-lookup"><span data-stu-id="8176c-152">**From**</span></span>           | <span data-ttu-id="8176c-153">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-153">Yes</span></span>         | <span data-ttu-id="8176c-154">値、 **FromRole**マップされるアソシエーションに対応するナビゲーション プロパティの属性です。</span><span class="sxs-lookup"><span data-stu-id="8176c-154">The value of the **FromRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="8176c-155">詳細については、NavigationProperty 要素 (CSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-155">For more information, see NavigationProperty Element (CSDL).</span></span> |
| <span data-ttu-id="8176c-156">**目的**</span><span class="sxs-lookup"><span data-stu-id="8176c-156">**To**</span></span>             | <span data-ttu-id="8176c-157">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-157">Yes</span></span>         | <span data-ttu-id="8176c-158">値、 **ToRole**マップされるアソシエーションに対応するナビゲーション プロパティの属性です。</span><span class="sxs-lookup"><span data-stu-id="8176c-158">The value of the **ToRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="8176c-159">詳細については、NavigationProperty 要素 (CSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-159">For more information, see NavigationProperty Element (CSDL).</span></span>   |

### <a name="example"></a><span data-ttu-id="8176c-160">例</span><span class="sxs-lookup"><span data-stu-id="8176c-160">Example</span></span>

<span data-ttu-id="8176c-161">次の概念モデルのエンティティ型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8176c-161">Consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="8176c-162">さらに、次のストアド プロシージャを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8176c-162">Also consider the following stored procedure:</span></span>

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

<span data-ttu-id="8176c-163">Update 関数をマップするために、 `Course` 、このストアド プロシージャにエンティティに値を指定する必要があります、 **DepartmentID**パラメーター。</span><span class="sxs-lookup"><span data-stu-id="8176c-163">In order to map the update function of the `Course` entity to this stored procedure, you must supply a value to the **DepartmentID** parameter.</span></span> <span data-ttu-id="8176c-164">`DepartmentID` の値はエンティティ型のプロパティには対応せず、独立した関連付け (アソシエーション) に含まれています。ここに、そのマッピングを示します。</span><span class="sxs-lookup"><span data-stu-id="8176c-164">The value for `DepartmentID` does not correspond to a property on the entity type; it is contained in an independent association whose mapping is shown here:</span></span>

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

<span data-ttu-id="8176c-165">次のコードは、 **AssociationEnd**要素にマップするために使用、 **DepartmentID**のプロパティ、 **FK\_コース\_部門**関連付け、 **UpdateCourse**ストアド プロシージャ (先の更新関数、**コース**エンティティ型をマップ)。</span><span class="sxs-lookup"><span data-stu-id="8176c-165">The following code shows the **AssociationEnd** element used to map the **DepartmentID** property of the **FK\_Course\_Department** association to the **UpdateCourse** stored procedure (to which the update function of the **Course** entity type is mapped):</span></span>

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

## <a name="associationsetmapping-element-msl"></a><span data-ttu-id="8176c-166">AssociationSetMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-166">AssociationSetMapping Element (MSL)</span></span>

<span data-ttu-id="8176c-167">**AssociationSetMapping**マッピング仕様言語 (MSL) 内の要素は、概念モデルとテーブル列、基になるデータベース内のアソシエーションの間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="8176c-167">The **AssociationSetMapping** element in mapping specification language (MSL) defines the mapping between an association in the conceptual model and table columns in the underlying database.</span></span>

<span data-ttu-id="8176c-168">概念モデルのアソシエーションとは、プロパティが基になるデータベースの主キー列および外部キー列を表す型のことです。</span><span class="sxs-lookup"><span data-stu-id="8176c-168">Associations in the conceptual model are types whose properties represent primary and foreign key columns in the underlying database.</span></span> <span data-ttu-id="8176c-169">**AssociationSetMapping**要素では、2 つの EndProperty 要素を使用して、データベースでアソシエーション型プロパティと列の間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="8176c-169">The **AssociationSetMapping** element uses two EndProperty elements to define the mappings between association type properties and columns in the database.</span></span> <span data-ttu-id="8176c-170">Condition 要素でこれらのマッピングに条件を配置できます。</span><span class="sxs-lookup"><span data-stu-id="8176c-170">You can place conditions on these mappings with the Condition element.</span></span> <span data-ttu-id="8176c-171">Insert、update、およびアソシエーションの削除機能を ModificationFunctionMapping 要素を持つデータベースのストアド プロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="8176c-171">Map the insert, update, and delete functions for associations to stored procedures in the database with the ModificationFunctionMapping element.</span></span> <span data-ttu-id="8176c-172">QueryView 要素 Entity SQL 文字列を使用して、アソシエーションとテーブル列の間の読み取り専用のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="8176c-172">Define read-only mappings between associations and table columns by using an Entity SQL string in a QueryView element.</span></span>

> [!NOTE]
> <span data-ttu-id="8176c-173">概念モデルのアソシエーションの参照に関する制約が定義されている場合、関連付けする必要はありませんがマップされること、 **AssociationSetMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-173">If a referential constraint is defined for an association in the conceptual model, the association does not need to be mapped with an **AssociationSetMapping** element.</span></span> <span data-ttu-id="8176c-174">場合、 **AssociationSetMapping**要素は、参照に関する制約のあるアソシエーションの存在でのマッピングが定義されている、 **AssociationSetMapping**要素は無視されます。</span><span class="sxs-lookup"><span data-stu-id="8176c-174">If an **AssociationSetMapping** element is present for an association that has a referential constraint, the mappings defined in the **AssociationSetMapping** element will be ignored.</span></span> <span data-ttu-id="8176c-175">詳細については、ReferentialConstraint 要素 (CSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-175">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="8176c-176">**AssociationSetMapping**要素は、次の子要素を持つことができます</span><span class="sxs-lookup"><span data-stu-id="8176c-176">The **AssociationSetMapping** element can have the following child elements</span></span>

-   <span data-ttu-id="8176c-177">QueryView (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="8176c-177">QueryView (zero or one)</span></span>
-   <span data-ttu-id="8176c-178">EndProperty (0 または 2)</span><span class="sxs-lookup"><span data-stu-id="8176c-178">EndProperty (zero or two)</span></span>
-   <span data-ttu-id="8176c-179">条件 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-179">Condition (zero or more)</span></span>
-   <span data-ttu-id="8176c-180">ModificationFunctionMapping (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="8176c-180">ModificationFunctionMapping (zero or one)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-181">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-181">Applicable Attributes</span></span>

<span data-ttu-id="8176c-182">次の表に適用できる属性、 **AssociationSetMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-182">The following table describes the attributes that can be applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="8176c-183">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-183">Attribute Name</span></span>     | <span data-ttu-id="8176c-184">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-184">Is Required</span></span> | <span data-ttu-id="8176c-185">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-185">Value</span></span>                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-186">**Name**</span><span class="sxs-lookup"><span data-stu-id="8176c-186">**Name**</span></span>           | <span data-ttu-id="8176c-187">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-187">Yes</span></span>         | <span data-ttu-id="8176c-188">マップされる概念モデルのアソシエーション セットの名前です。</span><span class="sxs-lookup"><span data-stu-id="8176c-188">The name of the conceptual model association set that is being mapped.</span></span>                      |
| <span data-ttu-id="8176c-189">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="8176c-189">**TypeName**</span></span>       | <span data-ttu-id="8176c-190">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-190">No</span></span>          | <span data-ttu-id="8176c-191">マッピングされている概念モデルのアソシエーション型の名前空間修飾名です。</span><span class="sxs-lookup"><span data-stu-id="8176c-191">The namespace-qualified name of the conceptual model association type that is being mapped.</span></span> |
| <span data-ttu-id="8176c-192">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="8176c-192">**StoreEntitySet**</span></span> | <span data-ttu-id="8176c-193">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-193">No</span></span>          | <span data-ttu-id="8176c-194">マップされるテーブルの名前です。</span><span class="sxs-lookup"><span data-stu-id="8176c-194">The name of the table that is being mapped.</span></span>                                                 |

### <a name="example"></a><span data-ttu-id="8176c-195">例</span><span class="sxs-lookup"><span data-stu-id="8176c-195">Example</span></span>

<span data-ttu-id="8176c-196">次の例は、 **AssociationSetMapping**要素、 **FK\_コース\_部門**概念モデルの関連付けセットは、にマップされます**コース**データベース内のテーブル。</span><span class="sxs-lookup"><span data-stu-id="8176c-196">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association set in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="8176c-197">アソシエーション型のプロパティとテーブルの列間のマッピングが子で指定された**EndProperty**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-197">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

## <a name="complexproperty-element-msl"></a><span data-ttu-id="8176c-198">ComplexProperty 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-198">ComplexProperty Element (MSL)</span></span>

<span data-ttu-id="8176c-199">A **ComplexProperty**マッピング仕様言語 (MSL) の要素は、基になるデータベースの概念モデルのエンティティ型とテーブル列に複合型プロパティ間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="8176c-199">A **ComplexProperty** element in mapping specification language (MSL) defines the mapping between a complex type property on a conceptual model entity type and table columns in the underlying database.</span></span> <span data-ttu-id="8176c-200">プロパティ列のマッピングは、ScalarProperty 要素の子で指定されます。</span><span class="sxs-lookup"><span data-stu-id="8176c-200">The property-column mappings are specified in child ScalarProperty elements.</span></span>

<span data-ttu-id="8176c-201">**ComplexType** property 要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-201">The **ComplexType** property element can have the following child elements:</span></span>

-   <span data-ttu-id="8176c-202">ScalarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-202">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="8176c-203">**ComplexProperty** (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-203">**ComplexProperty** (zero or more)</span></span>
-   <span data-ttu-id="8176c-204">ComplextTypeMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-204">ComplextTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="8176c-205">条件 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-205">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-206">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-206">Applicable Attributes</span></span>

<span data-ttu-id="8176c-207">次の表に、属性に適用される、 **ComplexProperty**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-207">The following table describes the attributes that are applicable to the **ComplexProperty** element:</span></span>

| <span data-ttu-id="8176c-208">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-208">Attribute Name</span></span> | <span data-ttu-id="8176c-209">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-209">Is Required</span></span> | <span data-ttu-id="8176c-210">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-210">Value</span></span>                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-211">**Name**</span><span class="sxs-lookup"><span data-stu-id="8176c-211">**Name**</span></span>       | <span data-ttu-id="8176c-212">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-212">Yes</span></span>         | <span data-ttu-id="8176c-213">マップされる概念モデルのエンティティ型の複合プロパティ名。</span><span class="sxs-lookup"><span data-stu-id="8176c-213">The name of the complex property of an entity type in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="8176c-214">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="8176c-214">**TypeName**</span></span>   | <span data-ttu-id="8176c-215">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-215">No</span></span>          | <span data-ttu-id="8176c-216">概念モデルのプロパティ型の名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="8176c-216">The namespace-qualified name of the conceptual model property type.</span></span>                              |

### <a name="example"></a><span data-ttu-id="8176c-217">例</span><span class="sxs-lookup"><span data-stu-id="8176c-217">Example</span></span>

<span data-ttu-id="8176c-218">次の例は、School モデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="8176c-218">The following example is based on the School model.</span></span> <span data-ttu-id="8176c-219">次の複合型は概念モデルに追加されました。</span><span class="sxs-lookup"><span data-stu-id="8176c-219">The following complex type has been added to the conceptual model:</span></span>

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

<span data-ttu-id="8176c-220">**LastName**と**FirstName**のプロパティ、 **Person**エンティティ型が 1 つの複合プロパティに置き換えられました**名前**:</span><span class="sxs-lookup"><span data-stu-id="8176c-220">The **LastName** and **FirstName** properties of the **Person** entity type have been replaced with one complex property, **Name**:</span></span>

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

<span data-ttu-id="8176c-221">次の MSL 示します、 **ComplexProperty**要素にマップするために使用、**名前**基になるデータベース内の列にプロパティ。</span><span class="sxs-lookup"><span data-stu-id="8176c-221">The following MSL shows the **ComplexProperty** element used to map the **Name** property to columns in the underlying database:</span></span>

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

## <a name="complextypemapping-element-msl"></a><span data-ttu-id="8176c-222">ComplexTypeMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-222">ComplexTypeMapping Element (MSL)</span></span>

<span data-ttu-id="8176c-223">**ComplexTypeMapping**マッピング仕様言語 (MSL) 内の要素が ResultMapping 要素の子であるし、基になる概念モデルの関数インポートとストアド プロシージャ間のマッピングを定義します。次に該当する場合は、データベース:</span><span class="sxs-lookup"><span data-stu-id="8176c-223">The **ComplexTypeMapping** element in mapping specification language (MSL) is a child of the ResultMapping element and defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="8176c-224">関数インポートが概念の複合型を返す場合</span><span class="sxs-lookup"><span data-stu-id="8176c-224">The function import returns a conceptual complex type.</span></span>
-   <span data-ttu-id="8176c-225">ストアド プロシージャが返す列の名前が、複合型のプロパティの名前と完全には一致しない場合</span><span class="sxs-lookup"><span data-stu-id="8176c-225">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the complex type.</span></span>

<span data-ttu-id="8176c-226">既定では、ストアド プロシージャが返す列と複合型とのマッピングは、列およびプロパティの名前に基づいています。</span><span class="sxs-lookup"><span data-stu-id="8176c-226">By default, the mapping between the columns returned by a stored procedure and a complex type is based on column and property names.</span></span> <span data-ttu-id="8176c-227">使用する必要がある列の名前がプロパティの名前一致も一致しない場合、 **ComplexTypeMapping**マッピングを定義する要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-227">If column names do not exactly match property names, you must use the **ComplexTypeMapping** element to define the mapping.</span></span> <span data-ttu-id="8176c-228">既定のマッピングの例は、FunctionImportMapping 要素 (MSL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-228">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="8176c-229">**ComplexTypeMapping**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-229">The **ComplexTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="8176c-230">ScalarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-230">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-231">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-231">Applicable Attributes</span></span>

<span data-ttu-id="8176c-232">次の表に、属性に適用される、 **ComplexTypeMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-232">The following table describes the attributes that are applicable to the **ComplexTypeMapping** element.</span></span>

| <span data-ttu-id="8176c-233">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-233">Attribute Name</span></span> | <span data-ttu-id="8176c-234">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-234">Is Required</span></span> | <span data-ttu-id="8176c-235">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-235">Value</span></span>                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| <span data-ttu-id="8176c-236">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="8176c-236">**TypeName**</span></span>   | <span data-ttu-id="8176c-237">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-237">Yes</span></span>         | <span data-ttu-id="8176c-238">マッピングされている複合型の名前空間修飾名です。</span><span class="sxs-lookup"><span data-stu-id="8176c-238">The namespace-qualified name of the complex type that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="8176c-239">例</span><span class="sxs-lookup"><span data-stu-id="8176c-239">Example</span></span>

<span data-ttu-id="8176c-240">次のストアド プロシージャを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8176c-240">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="8176c-241">また、次の概念モデルの複合型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8176c-241">Also consider the following conceptual model complex type:</span></span>

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

<span data-ttu-id="8176c-242">以前の複合型のインスタンスを返す関数インポートを作成するためには、ストアド プロシージャによって返される列間のマッピングとでエンティティ型を定義する必要があります、 **ComplexTypeMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-242">In order to create a function import that returns instances of the previous complex type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ComplexTypeMapping** element:</span></span>

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

## <a name="condition-element-msl"></a><span data-ttu-id="8176c-243">Condition 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-243">Condition Element (MSL)</span></span>

<span data-ttu-id="8176c-244">**条件**マッピング仕様言語 (MSL) 内の要素が条件を概念モデルと基になるデータベース間のマッピングを設定します。</span><span class="sxs-lookup"><span data-stu-id="8176c-244">The **Condition** element in mapping specification language (MSL) places conditions on mappings between the conceptual model and the underlying database.</span></span> <span data-ttu-id="8176c-245">XML ノードに定義されているマッピングは有効の場合は、すべての条件で指定された子**条件**要素が満たされています。</span><span class="sxs-lookup"><span data-stu-id="8176c-245">The mapping that is defined within an XML node is valid if all conditions, as specified in child **Condition** elements, are met.</span></span> <span data-ttu-id="8176c-246">それ以外の場合、マッピングは無効です。</span><span class="sxs-lookup"><span data-stu-id="8176c-246">Otherwise, the mapping is not valid.</span></span> <span data-ttu-id="8176c-247">MappingFragment 要素は、1 つ以上含まれている場合の例の**条件**、子要素内のマッピングが定義されている、 **MappingFragment**ノードが有効なは、すべての場合はだけ子の条件**条件**要素が満たされています。</span><span class="sxs-lookup"><span data-stu-id="8176c-247">For example, if a MappingFragment element contains one or more **Condition** child elements, the mapping defined within the **MappingFragment** node will only be valid if all the conditions of the child **Condition** elements are met.</span></span>

<span data-ttu-id="8176c-248">各条件がいずれかに適用できる、**名前**(で指定された概念モデルのエンティティ プロパティの名前、**名前**属性)、または**ColumnName** (内の列の名前指定される、データベース、 **ColumnName**属性)。</span><span class="sxs-lookup"><span data-stu-id="8176c-248">Each condition can apply to either a **Name** (the name of a conceptual model entity property, specified by the **Name** attribute), or a **ColumnName** (the name of a column in the database, specified by the **ColumnName** attribute).</span></span> <span data-ttu-id="8176c-249">ときに、**名前**属性が設定されており、エンティティのプロパティの値に対して条件がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="8176c-249">When the **Name** attribute is set, the condition is checked against an entity property value.</span></span> <span data-ttu-id="8176c-250">ときに、 **ColumnName**属性が設定されて、列の値に対して条件がチェックされます。</span><span class="sxs-lookup"><span data-stu-id="8176c-250">When the **ColumnName** attribute is set, the condition is checked against a column value.</span></span> <span data-ttu-id="8176c-251">1 つだけ、**名前**または**ColumnName**で属性を指定できます、**条件**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-251">Only one of the **Name** or **ColumnName** attribute can be specified in a **Condition** element.</span></span>

> [!NOTE]
> <span data-ttu-id="8176c-252">ときに、**条件**のみ FunctionImportMapping 要素内の要素が使用される、**名前**属性は適用されません。</span><span class="sxs-lookup"><span data-stu-id="8176c-252">When the **Condition** element is used within a FunctionImportMapping element, only the **Name** attribute is not applicable.</span></span>

<span data-ttu-id="8176c-253">**条件**要素は、次の要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-253">The **Condition** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="8176c-254">AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="8176c-254">AssociationSetMapping</span></span>
-   <span data-ttu-id="8176c-255">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="8176c-255">ComplexProperty</span></span>
-   <span data-ttu-id="8176c-256">EntitySetMapping</span><span class="sxs-lookup"><span data-stu-id="8176c-256">EntitySetMapping</span></span>
-   <span data-ttu-id="8176c-257">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="8176c-257">MappingFragment</span></span>
-   <span data-ttu-id="8176c-258">EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="8176c-258">EntityTypeMapping</span></span>

<span data-ttu-id="8176c-259">**条件**要素が子要素がありません。</span><span class="sxs-lookup"><span data-stu-id="8176c-259">The **Condition** element can have no child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-260">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-260">Applicable Attributes</span></span>

<span data-ttu-id="8176c-261">次の表に、属性に適用される、**条件**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-261">The following table describes the attributes that are applicable to the **Condition** element:</span></span>

| <span data-ttu-id="8176c-262">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-262">Attribute Name</span></span> | <span data-ttu-id="8176c-263">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-263">Is Required</span></span> | <span data-ttu-id="8176c-264">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-264">Value</span></span>                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-265">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="8176c-265">**ColumnName**</span></span> | <span data-ttu-id="8176c-266">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-266">No</span></span>          | <span data-ttu-id="8176c-267">条件の評価に使用される値のテーブル列の名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-267">The name of the table column whose value is used to evaluate the condition.</span></span>                                                                                                                                                                                                                   |
| <span data-ttu-id="8176c-268">**IsNull**</span><span class="sxs-lookup"><span data-stu-id="8176c-268">**IsNull**</span></span>     | <span data-ttu-id="8176c-269">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-269">No</span></span>          | <span data-ttu-id="8176c-270">**True**または**False**します。</span><span class="sxs-lookup"><span data-stu-id="8176c-270">**True** or **False**.</span></span> <span data-ttu-id="8176c-271">値の場合**True**列の値が、 **null**、値の場合、または**False**および列の値がない**null**条件が true.</span><span class="sxs-lookup"><span data-stu-id="8176c-271">If the value is **True** and the column value is **null**, or if the value is **False** and the column value is not **null**, the condition is true.</span></span> <span data-ttu-id="8176c-272">それ以外の場合、条件は False です。</span><span class="sxs-lookup"><span data-stu-id="8176c-272">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="8176c-273">**IsNull**と**値**と同時に属性を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="8176c-273">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span> |
| <span data-ttu-id="8176c-274">**[値]**</span><span class="sxs-lookup"><span data-stu-id="8176c-274">**Value**</span></span>      | <span data-ttu-id="8176c-275">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-275">No</span></span>          | <span data-ttu-id="8176c-276">列値と比較される値。</span><span class="sxs-lookup"><span data-stu-id="8176c-276">The value with which the column value is compared.</span></span> <span data-ttu-id="8176c-277">値が同じ場合、条件は True です。</span><span class="sxs-lookup"><span data-stu-id="8176c-277">If the values are the same, the condition is true.</span></span> <span data-ttu-id="8176c-278">それ以外の場合、条件は False です。</span><span class="sxs-lookup"><span data-stu-id="8176c-278">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="8176c-279">**IsNull**と**値**と同時に属性を使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="8176c-279">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span>                                                                       |
| <span data-ttu-id="8176c-280">**Name**</span><span class="sxs-lookup"><span data-stu-id="8176c-280">**Name**</span></span>       | <span data-ttu-id="8176c-281">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-281">No</span></span>          | <span data-ttu-id="8176c-282">条件の評価に使用される値を持つ概念モデルのエンティティ プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-282">The name of the conceptual model entity property whose value is used to evaluate the condition.</span></span> <br/> <span data-ttu-id="8176c-283">この属性が適用可能でない場合、**条件**FunctionImportMapping 要素内で要素を使用します。</span><span class="sxs-lookup"><span data-stu-id="8176c-283">This attribute is not applicable if the **Condition** element is used within a FunctionImportMapping element.</span></span>                                                                           |

### <a name="example"></a><span data-ttu-id="8176c-284">例</span><span class="sxs-lookup"><span data-stu-id="8176c-284">Example</span></span>

<span data-ttu-id="8176c-285">次の例は**条件**要素の子として**MappingFragment**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-285">The following example shows **Condition** elements as children of **MappingFragment** elements.</span></span> <span data-ttu-id="8176c-286">ときに**HireDate**が null でないと**EnrollmentDate**が null の場合、データがマップされる間、 **SchoolModel.Instructor**型と**PersonID**と**HireDate**の列、 **Person**テーブル。</span><span class="sxs-lookup"><span data-stu-id="8176c-286">When **HireDate** is not null and **EnrollmentDate** is null, data is mapped between the **SchoolModel.Instructor** type and the **PersonID** and **HireDate** columns of the **Person** table.</span></span> <span data-ttu-id="8176c-287">ときに**EnrollmentDate**が null でないと**HireDate**が null の場合、データがマップされる間、 **SchoolModel.Student**型と**PersonID\*\*\*\*登録**の列、 **Person**テーブル。</span><span class="sxs-lookup"><span data-stu-id="8176c-287">When **EnrollmentDate** is not null and **HireDate** is null, data is mapped between the **SchoolModel.Student** type and the **PersonID** and **Enrollment** columns of the **Person** table.</span></span>

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

## <a name="deletefunction-element-msl"></a><span data-ttu-id="8176c-288">DeleteFunction 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-288">DeleteFunction Element (MSL)</span></span>

<span data-ttu-id="8176c-289">**DeleteFunction**マッピング仕様言語 (MSL) 内の要素は、エンティティ型または概念モデルのアソシエーションの削除関数を基になるデータベース内のストアド プロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="8176c-289">The **DeleteFunction** element in mapping specification language (MSL) maps the delete function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="8176c-290">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-290">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="8176c-291">詳細については、関数要素 (SSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-291">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="8176c-292">マップしない場合は、3 つすべての挿入、更新、または削除操作をストアド プロシージャにエンティティ型の実行時に実行された場合にマップされていない操作は失敗および UpdateException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="8176c-292">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

### <a name="deletefunction-applied-to-entitytypemapping"></a><span data-ttu-id="8176c-293">EntityTypeMapping への DeleteFunction の適用</span><span class="sxs-lookup"><span data-stu-id="8176c-293">DeleteFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="8176c-294">EntityTypeMapping 要素に適用すると、 **DeleteFunction**要素は、概念モデルのエンティティ型の削除関数をストアド プロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="8176c-294">When applied to the EntityTypeMapping element, the **DeleteFunction** element maps the delete function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="8176c-295">**DeleteFunction**要素は次の子要素に適用される場合があることができます、 **EntityTypeMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-295">The **DeleteFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="8176c-296">AssociationEnd (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-296">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="8176c-297">ComplexProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-297">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="8176c-298">ScarlarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-298">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="8176c-299">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-299">Applicable Attributes</span></span>

<span data-ttu-id="8176c-300">次の表に適用できる属性、 **DeleteFunction**要素に適用されたときに、 **EntityTypeMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-300">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="8176c-301">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-301">Attribute Name</span></span>            | <span data-ttu-id="8176c-302">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-302">Is Required</span></span> | <span data-ttu-id="8176c-303">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-303">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-304">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="8176c-304">**FunctionName**</span></span>          | <span data-ttu-id="8176c-305">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-305">Yes</span></span>         | <span data-ttu-id="8176c-306">削除関数のマップ先となるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="8176c-306">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="8176c-307">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-307">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="8176c-308">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="8176c-308">**RowsAffectedParameter**</span></span> | <span data-ttu-id="8176c-309">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-309">No</span></span>          | <span data-ttu-id="8176c-310">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-310">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="8176c-311">例</span><span class="sxs-lookup"><span data-stu-id="8176c-311">Example</span></span>

<span data-ttu-id="8176c-312">次の例は、School モデルに基づいておりを示しています、 **DeleteFunction**要素の削除関数のマッピング、 **Person**エンティティ型を**DeletePerson**ストアド プロシージャです。</span><span class="sxs-lookup"><span data-stu-id="8176c-312">The following example is based on the School model and shows the **DeleteFunction** element mapping the delete function of the **Person** entity type to the **DeletePerson** stored procedure.</span></span> <span data-ttu-id="8176c-313">**DeletePerson**ストアド プロシージャはストレージ モデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="8176c-313">The **DeletePerson** stored procedure is declared in the storage model.</span></span>

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

### <a name="deletefunction-applied-to-associationsetmapping"></a><span data-ttu-id="8176c-314">AssociationSetMapping への DeleteFunction の適用</span><span class="sxs-lookup"><span data-stu-id="8176c-314">DeleteFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="8176c-315">AssociationSetMapping 要素に適用すると、 **DeleteFunction**要素は、概念モデルのアソシエーションの削除関数をストアド プロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="8176c-315">When applied to the AssociationSetMapping element, the **DeleteFunction** element maps the delete function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="8176c-316">**DeleteFunction**要素は次の子要素に適用される場合があることができます、 **AssociationSetMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-316">The **DeleteFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="8176c-317">EndProperty</span><span class="sxs-lookup"><span data-stu-id="8176c-317">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="8176c-318">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-318">Applicable Attributes</span></span>

<span data-ttu-id="8176c-319">次の表に適用できる属性、 **DeleteFunction**要素に適用されたときに、 **AssociationSetMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-319">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="8176c-320">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-320">Attribute Name</span></span>            | <span data-ttu-id="8176c-321">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-321">Is Required</span></span> | <span data-ttu-id="8176c-322">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-322">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-323">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="8176c-323">**FunctionName**</span></span>          | <span data-ttu-id="8176c-324">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-324">Yes</span></span>         | <span data-ttu-id="8176c-325">削除関数のマップ先となるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="8176c-325">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="8176c-326">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-326">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="8176c-327">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="8176c-327">**RowsAffectedParameter**</span></span> | <span data-ttu-id="8176c-328">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-328">No</span></span>          | <span data-ttu-id="8176c-329">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-329">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="8176c-330">例</span><span class="sxs-lookup"><span data-stu-id="8176c-330">Example</span></span>

<span data-ttu-id="8176c-331">次の例は、School モデルに基づいておりを示しています、 **DeleteFunction**要素の削除関数をマップするために使用、 **CourseInstructor**への関連付け、 **DeleteCourseInstructor**ストアド プロシージャ。</span><span class="sxs-lookup"><span data-stu-id="8176c-331">The following example is based on the School model and shows the **DeleteFunction** element used to map delete function of the **CourseInstructor** association to the **DeleteCourseInstructor** stored procedure.</span></span> <span data-ttu-id="8176c-332">**DeleteCourseInstructor**ストアド プロシージャはストレージ モデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="8176c-332">The **DeleteCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="endproperty-element-msl"></a><span data-ttu-id="8176c-333">EndProperty 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-333">EndProperty Element (MSL)</span></span>

<span data-ttu-id="8176c-334">**EndProperty**マッピング仕様言語 (MSL) の要素は、端または概念モデル アソシエーションと基になるデータベースの変更関数の間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="8176c-334">The **EndProperty** element in mapping specification language (MSL) defines the mapping between an end or a modification function of a conceptual model association and the underlying database.</span></span> <span data-ttu-id="8176c-335">プロパティ列のマッピングは、子の ScalarProperty 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="8176c-335">The property-column mapping is specified in a child ScalarProperty element.</span></span>

<span data-ttu-id="8176c-336">ときに、 **EndProperty**要素を使用して概念モデル アソシエーションの端のマッピングを定義、AssociationSetMapping 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="8176c-336">When an **EndProperty** element is used to define the mapping for the end of a conceptual model association, it is a child of an AssociationSetMapping element.</span></span> <span data-ttu-id="8176c-337">ときに、 **EndProperty**変更関数の概念モデル アソシエーションのマッピングを定義する要素が使用される、InsertFunction 要素または DeleteFunction 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="8176c-337">When the **EndProperty** element is used to define the mapping for a modification function of a conceptual model association, it is a child of an InsertFunction element or DeleteFunction element.</span></span>

<span data-ttu-id="8176c-338">**EndProperty**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-338">The **EndProperty** element can have the following child elements:</span></span>

-   <span data-ttu-id="8176c-339">ScalarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-339">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-340">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-340">Applicable Attributes</span></span>

<span data-ttu-id="8176c-341">次の表に、属性に適用される、 **EndProperty**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-341">The following table describes the attributes that are applicable to the **EndProperty** element:</span></span>

| <span data-ttu-id="8176c-342">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-342">Attribute Name</span></span> | <span data-ttu-id="8176c-343">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-343">Is Required</span></span> | <span data-ttu-id="8176c-344">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-344">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="8176c-345">名前</span><span class="sxs-lookup"><span data-stu-id="8176c-345">Name</span></span>           | <span data-ttu-id="8176c-346">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-346">Yes</span></span>         | <span data-ttu-id="8176c-347">マップされるアソシエーション端の名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-347">The name of the association end that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="8176c-348">例</span><span class="sxs-lookup"><span data-stu-id="8176c-348">Example</span></span>

<span data-ttu-id="8176c-349">次の例は、 **AssociationSetMapping**要素、 **FK\_コース\_部門**に概念モデルのアソシエーションをマップ**コース**データベース内のテーブル。</span><span class="sxs-lookup"><span data-stu-id="8176c-349">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="8176c-350">アソシエーション型のプロパティとテーブルの列間のマッピングが子で指定された**EndProperty**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-350">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

### <a name="example"></a><span data-ttu-id="8176c-351">例</span><span class="sxs-lookup"><span data-stu-id="8176c-351">Example</span></span>

<span data-ttu-id="8176c-352">次の例は、 **EndProperty**アソシエーションの挿入および削除関数のマップ要素 (**CourseInstructor**) を基になるデータベースでストアド プロシージャにします。</span><span class="sxs-lookup"><span data-stu-id="8176c-352">The following example shows the **EndProperty** element mapping the insert and delete functions of an association (**CourseInstructor**) to stored procedures in the underlying database.</span></span> <span data-ttu-id="8176c-353">ストレージ モデルにマップされて宣言されている関数。</span><span class="sxs-lookup"><span data-stu-id="8176c-353">The functions that are mapped to are declared in the storage model.</span></span>

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

## <a name="entitycontainermapping-element-msl"></a><span data-ttu-id="8176c-354">EntityContainerMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-354">EntityContainerMapping Element (MSL)</span></span>

<span data-ttu-id="8176c-355">**EntityContainerMapping**マッピング仕様言語 (MSL) の要素には、ストレージ モデルのエンティティ コンテナーに、概念モデルのエンティティ コンテナーがマップされます。</span><span class="sxs-lookup"><span data-stu-id="8176c-355">The **EntityContainerMapping** element in mapping specification language (MSL) maps the entity container in the conceptual model to the entity container in the storage model.</span></span> <span data-ttu-id="8176c-356">**EntityContainerMapping**要素は、マッピング要素の子。</span><span class="sxs-lookup"><span data-stu-id="8176c-356">The **EntityContainerMapping** element is a child of the Mapping element.</span></span>

<span data-ttu-id="8176c-357">**EntityContainerMapping**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-357">The **EntityContainerMapping** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8176c-358">EntitySetMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-358">EntitySetMapping (zero or more)</span></span>
-   <span data-ttu-id="8176c-359">AssociationSetMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-359">AssociationSetMapping (zero or more)</span></span>
-   <span data-ttu-id="8176c-360">FunctionImportMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-360">FunctionImportMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-361">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-361">Applicable Attributes</span></span>

<span data-ttu-id="8176c-362">次の表に適用できる属性、 **EntityContainerMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-362">The following table describes the attributes that can be applied to the **EntityContainerMapping** element.</span></span>

| <span data-ttu-id="8176c-363">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-363">Attribute Name</span></span>            | <span data-ttu-id="8176c-364">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-364">Is Required</span></span> | <span data-ttu-id="8176c-365">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-365">Value</span></span>                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-366">**StorageModelContainer**</span><span class="sxs-lookup"><span data-stu-id="8176c-366">**StorageModelContainer**</span></span> | <span data-ttu-id="8176c-367">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-367">Yes</span></span>         | <span data-ttu-id="8176c-368">マップされるストレージ モデルのエンティティ コンテナーの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-368">The name of the storage model entity container that is being mapped.</span></span>                                                                                                                                                                                     |
| <span data-ttu-id="8176c-369">**CdmEntityContainer**</span><span class="sxs-lookup"><span data-stu-id="8176c-369">**CdmEntityContainer**</span></span>    | <span data-ttu-id="8176c-370">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-370">Yes</span></span>         | <span data-ttu-id="8176c-371">マップされる概念モデルのエンティティ コンテナーの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-371">The name of the conceptual model entity container that is being mapped.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="8176c-372">**GenerateUpdateViews**</span><span class="sxs-lookup"><span data-stu-id="8176c-372">**GenerateUpdateViews**</span></span>   | <span data-ttu-id="8176c-373">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-373">No</span></span>          | <span data-ttu-id="8176c-374">**True**または**False**します。</span><span class="sxs-lookup"><span data-stu-id="8176c-374">**True** or **False**.</span></span> <span data-ttu-id="8176c-375">場合**False**、更新プログラム ビューは生成されません。</span><span class="sxs-lookup"><span data-stu-id="8176c-375">If **False**, no update views are generated.</span></span> <span data-ttu-id="8176c-376">この属性に設定する必要があります**False**を無効になるデータが正常にラウンドト リップしないため、読み取り専用マッピングがある場合。</span><span class="sxs-lookup"><span data-stu-id="8176c-376">This attribute should be set to **False** when you have a read-only mapping that would be invalid because data may not round-trip successfully.</span></span> <br/> <span data-ttu-id="8176c-377">既定値は**True**します。</span><span class="sxs-lookup"><span data-stu-id="8176c-377">The default value is **True**.</span></span> |

### <a name="example"></a><span data-ttu-id="8176c-378">例</span><span class="sxs-lookup"><span data-stu-id="8176c-378">Example</span></span>

<span data-ttu-id="8176c-379">次の例は、 **EntityContainerMapping**マップされる要素、 **SchoolModelEntities**コンテナー (概念モデルのエンティティ コンテナー) を**SchoolModelStoreContainer**コンテナー (ストレージ モデルのエンティティ コンテナー)。</span><span class="sxs-lookup"><span data-stu-id="8176c-379">The following example shows an **EntityContainerMapping** element that maps the **SchoolModelEntities** container (the conceptual model entity container) to the **SchoolModelStoreContainer** container (the storage model entity container):</span></span>

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

## <a name="entitysetmapping-element-msl"></a><span data-ttu-id="8176c-380">EntitySetMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-380">EntitySetMapping Element (MSL)</span></span>

<span data-ttu-id="8176c-381">**EntitySetMapping**ストレージ モデルのマッピング仕様言語 (MSL) のマップが概念モデルのエンティティのすべての型がエンティティに設定内の要素を設定します。</span><span class="sxs-lookup"><span data-stu-id="8176c-381">The **EntitySetMapping** element in mapping specification language (MSL) maps all types in a conceptual model entity set to entity sets in the storage model.</span></span> <span data-ttu-id="8176c-382">概念モデル内のエンティティ セットの論理的なコンテナーは、同じ種類 (と派生型) のエンティティのインスタンス。</span><span class="sxs-lookup"><span data-stu-id="8176c-382">An entity set in the conceptual model is a logical container for instances of entities of the same type (and derived types).</span></span> <span data-ttu-id="8176c-383">ストレージ モデル内のエンティティ セットは、テーブルまたは基になるデータベースのビューを表します。</span><span class="sxs-lookup"><span data-stu-id="8176c-383">An entity set in the storage model represents a table or view in the underlying database.</span></span> <span data-ttu-id="8176c-384">値によって、概念モデルのエンティティ セットが指定されて、**名前**の属性、 **EntitySetMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-384">The conceptual model entity set is specified by the value of the **Name** attribute of the **EntitySetMapping** element.</span></span> <span data-ttu-id="8176c-385">マップ先のテーブルまたはビューがで指定された、 **StoreEntitySet**各子 MappingFragment 要素または属性、 **EntitySetMapping**要素自体です。</span><span class="sxs-lookup"><span data-stu-id="8176c-385">The mapped-to table or view is specified by the **StoreEntitySet** attribute in each child MappingFragment element or in the **EntitySetMapping** element itself.</span></span>

<span data-ttu-id="8176c-386">**EntitySetMapping**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-386">The **EntitySetMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="8176c-387">EntityTypeMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-387">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="8176c-388">QueryView (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="8176c-388">QueryView (zero or one)</span></span>
-   <span data-ttu-id="8176c-389">MappingFragment (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-389">MappingFragment (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-390">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-390">Applicable Attributes</span></span>

<span data-ttu-id="8176c-391">次の表に適用できる属性、 **EntitySetMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-391">The following table describes the attributes that can be applied to the **EntitySetMapping** element.</span></span>

| <span data-ttu-id="8176c-392">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-392">Attribute Name</span></span>           | <span data-ttu-id="8176c-393">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-393">Is Required</span></span> | <span data-ttu-id="8176c-394">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-394">Value</span></span>                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-395">**Name**</span><span class="sxs-lookup"><span data-stu-id="8176c-395">**Name**</span></span>                 | <span data-ttu-id="8176c-396">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-396">Yes</span></span>         | <span data-ttu-id="8176c-397">マッピングされている概念モデルのエンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-397">The name of the conceptual model entity set that is being mapped.</span></span>                                                                                                                                                             |
| <span data-ttu-id="8176c-398">**TypeName** **1**</span><span class="sxs-lookup"><span data-stu-id="8176c-398">**TypeName** **1**</span></span>       | <span data-ttu-id="8176c-399">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-399">No</span></span>          | <span data-ttu-id="8176c-400">マッピングされている概念モデルのエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-400">The name of the conceptual model entity type that is being mapped.</span></span>                                                                                                                                                            |
| <span data-ttu-id="8176c-401">**StoreEntitySet** **1**</span><span class="sxs-lookup"><span data-stu-id="8176c-401">**StoreEntitySet** **1**</span></span> | <span data-ttu-id="8176c-402">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-402">No</span></span>          | <span data-ttu-id="8176c-403">マップ先のストレージ モデルのエンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-403">The name of the storage model entity set that is being mapped to.</span></span>                                                                                                                                                             |
| <span data-ttu-id="8176c-404">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="8176c-404">**MakeColumnsDistinct**</span></span>  | <span data-ttu-id="8176c-405">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-405">No</span></span>          | <span data-ttu-id="8176c-406">**True**または**False**個別の行のみが返されるかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="8176c-406">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="8176c-407">この属性設定されている場合**True**、 **GenerateUpdateViews**に EntityContainerMapping 要素の属性を設定する必要があります**False**します。</span><span class="sxs-lookup"><span data-stu-id="8176c-407">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

 

<span data-ttu-id="8176c-408">**1** 、 **TypeName**と**StoreEntitySet**属性は、1 つのテーブルに 1 つのエンティティ型をマップする EntityTypeMapping と MappingFragment 子要素の代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="8176c-408">**1** The **TypeName** and **StoreEntitySet** attributes can be used in place of the EntityTypeMapping and MappingFragment child elements to map a single entity type to a single table.</span></span>

### <a name="example"></a><span data-ttu-id="8176c-409">例</span><span class="sxs-lookup"><span data-stu-id="8176c-409">Example</span></span>

<span data-ttu-id="8176c-410">次の例は、 **EntitySetMapping**で 3 つの型 (基本データ型と 2 つの派生型) をマップする要素、**コース**で 3 つのテーブルに、概念モデルのエンティティ セット、基になるデータベース。</span><span class="sxs-lookup"><span data-stu-id="8176c-410">The following example shows an **EntitySetMapping** element that maps three types (a base type and two derived types) in the **Courses** entity set of the conceptual model to three different tables in the underlying database.</span></span> <span data-ttu-id="8176c-411">テーブルがで指定された、 **StoreEntitySet**各属性**MappingFragment**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-411">The tables are specified by the **StoreEntitySet** attribute in each **MappingFragment** element.</span></span>

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

## <a name="entitytypemapping-element-msl"></a><span data-ttu-id="8176c-412">EntityTypeMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-412">EntityTypeMapping Element (MSL)</span></span>

<span data-ttu-id="8176c-413">**EntityTypeMapping**マッピング仕様言語 (MSL) 要素は、基になるデータベース、概念モデルとテーブルのエンティティ型またはビュー間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="8176c-413">The **EntityTypeMapping** element in mapping specification language (MSL) defines the mapping between an entity type in the conceptual model and tables or views in the underlying database.</span></span> <span data-ttu-id="8176c-414">概念モデルのエンティティ型と基になるデータベース テーブルまたはビューについては、EntityType 要素 (CSDL) と EntitySet 要素 (SSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-414">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="8176c-415">マップされる概念モデルのエンティティ型がで指定された、 **TypeName**の属性、 **EntityTypeMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-415">The conceptual model entity type that is being mapped is specified by the **TypeName** attribute of the **EntityTypeMapping** element.</span></span> <span data-ttu-id="8176c-416">テーブルまたはビューがマップされているがで指定された、 **StoreEntitySet**子 MappingFragment 要素の属性。</span><span class="sxs-lookup"><span data-stu-id="8176c-416">The table or view that is being mapped is specified by the **StoreEntitySet** attribute of the child MappingFragment element.</span></span>

<span data-ttu-id="8176c-417">ModificationFunctionMapping の子要素を使用して、挿入をマップできますでは、更新、または、データベース内のストアド プロシージャにエンティティ型の関数を削除します。</span><span class="sxs-lookup"><span data-stu-id="8176c-417">The ModificationFunctionMapping child element can be used to map the insert, update, or delete functions of entity types to stored procedures in the database.</span></span>

<span data-ttu-id="8176c-418">**EntityTypeMapping**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-418">The **EntityTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="8176c-419">MappingFragment (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-419">MappingFragment (zero or more)</span></span>
-   <span data-ttu-id="8176c-420">ModificationFunctionMapping (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="8176c-420">ModificationFunctionMapping (zero or one)</span></span>
-   <span data-ttu-id="8176c-421">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="8176c-421">ScalarProperty</span></span>
-   <span data-ttu-id="8176c-422">条件</span><span class="sxs-lookup"><span data-stu-id="8176c-422">Condition</span></span>

> [!NOTE]
> <span data-ttu-id="8176c-423">**MappingFragment**と**ModificationFunctionMapping**要素の子要素をすることはできません、 **EntityTypeMapping**と同時にある要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-423">**MappingFragment** and **ModificationFunctionMapping** elements cannot be child elements of the **EntityTypeMapping** element at the same time.</span></span>


> [!NOTE]
> <span data-ttu-id="8176c-424">**ScalarProperty**と**条件**の子要素が要素にできる、 **EntityTypeMapping** FunctionImportMapping 要素内で使用した場合の要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-424">The **ScalarProperty** and **Condition** elements can only be child elements of the **EntityTypeMapping** element when it is used within a FunctionImportMapping element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-425">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-425">Applicable Attributes</span></span>

<span data-ttu-id="8176c-426">次の表に適用できる属性、 **EntityTypeMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-426">The following table describes the attributes that can be applied to the **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="8176c-427">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-427">Attribute Name</span></span> | <span data-ttu-id="8176c-428">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-428">Is Required</span></span> | <span data-ttu-id="8176c-429">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-429">Value</span></span>                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-430">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="8176c-430">**TypeName**</span></span>   | <span data-ttu-id="8176c-431">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-431">Yes</span></span>         | <span data-ttu-id="8176c-432">マッピングされている概念モデルのエンティティ型の名前空間修飾名です。</span><span class="sxs-lookup"><span data-stu-id="8176c-432">The namespace-qualified name of the conceptual model entity type that is being mapped.</span></span> <br/> <span data-ttu-id="8176c-433">型が抽象型または派生型である場合、値は `IsOfType(Namespace-qualified_type_name)` になる必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-433">If the type is abstract or a derived type, the value must be `IsOfType(Namespace-qualified_type_name)`.</span></span> |

### <a name="example"></a><span data-ttu-id="8176c-434">例</span><span class="sxs-lookup"><span data-stu-id="8176c-434">Example</span></span>

<span data-ttu-id="8176c-435">次の例では、2 つの子を持つ EntitySetMapping 要素**EntityTypeMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-435">The following example shows an EntitySetMapping element with two child **EntityTypeMapping** elements.</span></span> <span data-ttu-id="8176c-436">最初の**EntityTypeMapping**要素、 **SchoolModel.Person**エンティティ型にマッピングされますが、 **Person**テーブル。</span><span class="sxs-lookup"><span data-stu-id="8176c-436">In the first **EntityTypeMapping** element, the **SchoolModel.Person** entity type is mapped to the **Person** table.</span></span> <span data-ttu-id="8176c-437">2 番目の**EntityTypeMapping**要素では、更新機能は、 **SchoolModel.Person**型は、ストアド プロシージャにマッピング**UpdatePerson**データベースで.</span><span class="sxs-lookup"><span data-stu-id="8176c-437">In the second **EntityTypeMapping** element, the update functionality of the **SchoolModel.Person** type is mapped to a stored procedure, **UpdatePerson**, in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="8176c-438">例</span><span class="sxs-lookup"><span data-stu-id="8176c-438">Example</span></span>

<span data-ttu-id="8176c-439">次の例は、ルート型が抽象である型階層のマッピングを示しています。</span><span class="sxs-lookup"><span data-stu-id="8176c-439">The next example shows the mapping of a type hierarchy in which the root type is abstract.</span></span> <span data-ttu-id="8176c-440">使用に注意してください、`IsOfType`の構文、 **TypeName**属性。</span><span class="sxs-lookup"><span data-stu-id="8176c-440">Note the use of the `IsOfType` syntax for the **TypeName** attributes.</span></span>

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

## <a name="functionimportmapping-element-msl"></a><span data-ttu-id="8176c-441">FunctionImportMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-441">FunctionImportMapping Element (MSL)</span></span>

<span data-ttu-id="8176c-442">**FunctionImportMapping**マッピング仕様言語 (MSL) 要素は、基になるデータベース、概念モデルとストアド プロシージャでの関数インポートまたは関数の間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="8176c-442">The **FunctionImportMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure or function in the underlying database.</span></span> <span data-ttu-id="8176c-443">関数インポートは概念モデル内で、ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-443">Function imports must be declared in the conceptual model and stored procedures must be declared in the storage model.</span></span> <span data-ttu-id="8176c-444">詳細については、FunctionImport 要素 (CSDL) と関数要素 (SSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-444">For more information, see FunctionImport Element (CSDL) and Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="8176c-445">既定では、関数インポートが概念モデル エンティティ型または複合型を返す場合、基になるストアド プロシージャが返す列の名前が概念モデル型のプロパティの名前と正確に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-445">By default, if a function import returns a conceptual model entity type or complex type, then the names of the columns returned by the underlying stored procedure must exactly match the names of the properties on the conceptual model type.</span></span> <span data-ttu-id="8176c-446">列名は一致しない場合、プロパティ名、マッピングが ResultMapping 要素で定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-446">If the column names do not exactly match the property names, the mapping must be defined in a ResultMapping element.</span></span>

<span data-ttu-id="8176c-447">**FunctionImportMapping**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-447">The **FunctionImportMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="8176c-448">ResultMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-448">ResultMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-449">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-449">Applicable Attributes</span></span>

<span data-ttu-id="8176c-450">次の表に、属性に適用される、 **FunctionImportMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-450">The following table describes the attributes that are applicable to the **FunctionImportMapping** element:</span></span>

| <span data-ttu-id="8176c-451">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-451">Attribute Name</span></span>         | <span data-ttu-id="8176c-452">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-452">Is Required</span></span> | <span data-ttu-id="8176c-453">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-453">Value</span></span>                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-454">**FunctionImportName**</span><span class="sxs-lookup"><span data-stu-id="8176c-454">**FunctionImportName**</span></span> | <span data-ttu-id="8176c-455">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-455">Yes</span></span>         | <span data-ttu-id="8176c-456">マップされる概念モデルの関数インポートの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-456">The name of the function import in the conceptual model that is being mapped.</span></span>           |
| <span data-ttu-id="8176c-457">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="8176c-457">**FunctionName**</span></span>       | <span data-ttu-id="8176c-458">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-458">Yes</span></span>         | <span data-ttu-id="8176c-459">マップされるストレージ モデルの関数の名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="8176c-459">The namespace-qualified name of the function in the storage model that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="8176c-460">例</span><span class="sxs-lookup"><span data-stu-id="8176c-460">Example</span></span>

<span data-ttu-id="8176c-461">次の例は、School モデルに基づきます。</span><span class="sxs-lookup"><span data-stu-id="8176c-461">The following example is based on the School model.</span></span> <span data-ttu-id="8176c-462">ストレージ モデルの次の関数について考えます。</span><span class="sxs-lookup"><span data-stu-id="8176c-462">Consider the following function in the storage model:</span></span>

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

<span data-ttu-id="8176c-463">また、概念モデルのこの関数インポートについて考えます。</span><span class="sxs-lookup"><span data-stu-id="8176c-463">Also consider this function import in the conceptual model:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

<span data-ttu-id="8176c-464">次の例に示す、 **FunctionImportMapping**関数をマップし、関数の上に互いのインポートに使用する要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-464">The following example show a **FunctionImportMapping** element used to map the function and function import above to each other:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a><span data-ttu-id="8176c-465">InsertFunction 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-465">InsertFunction Element (MSL)</span></span>

<span data-ttu-id="8176c-466">**InsertFunction**マッピング仕様言語 (MSL) 内の要素は、エンティティ型または概念モデルのアソシエーションの挿入関数を基になるデータベース内のストアド プロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="8176c-466">The **InsertFunction** element in mapping specification language (MSL) maps the insert function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="8176c-467">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-467">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="8176c-468">詳細については、関数要素 (SSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-468">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="8176c-469">マップしない場合は、3 つすべての挿入、更新、または削除操作をストアド プロシージャにエンティティ型の実行時に実行された場合にマップされていない操作は失敗および UpdateException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="8176c-469">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="8176c-470">**InsertFunction**要素 ModificationFunctionMapping 要素の子にできるし、EntityTypeMapping 要素または AssociationSetMapping 要素に適用します。</span><span class="sxs-lookup"><span data-stu-id="8176c-470">The **InsertFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

### <a name="insertfunction-applied-to-entitytypemapping"></a><span data-ttu-id="8176c-471">EntityTypeMapping への InsertFunction の適用</span><span class="sxs-lookup"><span data-stu-id="8176c-471">InsertFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="8176c-472">EntityTypeMapping 要素に適用すると、 **InsertFunction**要素は、概念モデルのエンティティ型の挿入関数をストアド プロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="8176c-472">When applied to the EntityTypeMapping element, the **InsertFunction** element maps the insert function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="8176c-473">**InsertFunction**要素は次の子要素に適用される場合があることができます、 **EntityTypeMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-473">The **InsertFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="8176c-474">AssociationEnd (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-474">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="8176c-475">ComplexProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-475">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="8176c-476">ResultBinding (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="8176c-476">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="8176c-477">ScarlarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-477">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="8176c-478">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-478">Applicable Attributes</span></span>

<span data-ttu-id="8176c-479">次の表に適用できる属性、 **InsertFunction**要素に適用すると、 **EntityTypeMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-479">The following table describes the attributes that can be applied to the **InsertFunction** element when applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="8176c-480">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-480">Attribute Name</span></span>            | <span data-ttu-id="8176c-481">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-481">Is Required</span></span> | <span data-ttu-id="8176c-482">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-482">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-483">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="8176c-483">**FunctionName**</span></span>          | <span data-ttu-id="8176c-484">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-484">Yes</span></span>         | <span data-ttu-id="8176c-485">挿入関数のマップ先であるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="8176c-485">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="8176c-486">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-486">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="8176c-487">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="8176c-487">**RowsAffectedParameter**</span></span> | <span data-ttu-id="8176c-488">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-488">No</span></span>          | <span data-ttu-id="8176c-489">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-489">The name of the output parameter that returns the number of affected rows.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="8176c-490">例</span><span class="sxs-lookup"><span data-stu-id="8176c-490">Example</span></span>

<span data-ttu-id="8176c-491">次の例は、School モデルに基づいておりを示しています、 **InsertFunction** Person エンティティ型の挿入関数をマップするための要素、 **InsertPerson**ストアド プロシージャ。</span><span class="sxs-lookup"><span data-stu-id="8176c-491">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the Person entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="8176c-492">**InsertPerson**ストアド プロシージャはストレージ モデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="8176c-492">The **InsertPerson** stored procedure is declared in the storage model.</span></span>

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
### <a name="insertfunction-applied-to-associationsetmapping"></a><span data-ttu-id="8176c-493">AssociationSetMapping への InsertFunction の適用</span><span class="sxs-lookup"><span data-stu-id="8176c-493">InsertFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="8176c-494">AssociationSetMapping 要素に適用すると、 **InsertFunction**要素は、概念モデルのアソシエーションの挿入関数をストアド プロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="8176c-494">When applied to the AssociationSetMapping element, the **InsertFunction** element maps the insert function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="8176c-495">**InsertFunction**要素は次の子要素に適用される場合があることができます、 **AssociationSetMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-495">The **InsertFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="8176c-496">EndProperty</span><span class="sxs-lookup"><span data-stu-id="8176c-496">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="8176c-497">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-497">Applicable Attributes</span></span>

<span data-ttu-id="8176c-498">次の表に適用できる属性、 **InsertFunction**要素に適用されたときに、 **AssociationSetMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-498">The following table describes the attributes that can be applied to the **InsertFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="8176c-499">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-499">Attribute Name</span></span>            | <span data-ttu-id="8176c-500">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-500">Is Required</span></span> | <span data-ttu-id="8176c-501">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-501">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-502">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="8176c-502">**FunctionName**</span></span>          | <span data-ttu-id="8176c-503">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-503">Yes</span></span>         | <span data-ttu-id="8176c-504">挿入関数のマップ先であるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="8176c-504">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="8176c-505">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-505">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="8176c-506">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="8176c-506">**RowsAffectedParameter**</span></span> | <span data-ttu-id="8176c-507">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-507">No</span></span>          | <span data-ttu-id="8176c-508">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-508">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="8176c-509">例</span><span class="sxs-lookup"><span data-stu-id="8176c-509">Example</span></span>

<span data-ttu-id="8176c-510">次の例は、School モデルに基づいておりを示しています、 **InsertFunction**要素の挿入関数をマップするために使用、 **CourseInstructor**への関連付け、 **InsertCourseInstructor**ストアド プロシージャ。</span><span class="sxs-lookup"><span data-stu-id="8176c-510">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the **CourseInstructor** association to the **InsertCourseInstructor** stored procedure.</span></span> <span data-ttu-id="8176c-511">**InsertCourseInstructor**ストアド プロシージャはストレージ モデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="8176c-511">The **InsertCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="mapping-element-msl"></a><span data-ttu-id="8176c-512">Mapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-512">Mapping Element (MSL)</span></span>

<span data-ttu-id="8176c-513">**マッピング**マッピング仕様言語 (MSL) 内の要素には (ストレージ モデルで説明した) ように、概念モデルをデータベースで定義されているオブジェクトのマッピングの情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="8176c-513">The **Mapping** element in mapping specification language (MSL) contains information for mapping objects that are defined in a conceptual model to a database (as described in a storage model).</span></span> <span data-ttu-id="8176c-514">詳細については、CSDL の仕様と SSDL 仕様を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-514">For more information, see CSDL Specification and SSDL Specification.</span></span>

<span data-ttu-id="8176c-515">**マッピング**要素はマッピング スキーマのルート要素です。</span><span class="sxs-lookup"><span data-stu-id="8176c-515">The **Mapping** element is the root element for a mapping specification.</span></span> <span data-ttu-id="8176c-516">マッピング仕様の XML 名前空間は http://schemas.microsoft.com/ado/2009/11/mapping/cs します。</span><span class="sxs-lookup"><span data-stu-id="8176c-516">The XML namespace for mapping specifications is http://schemas.microsoft.com/ado/2009/11/mapping/cs.</span></span>

<span data-ttu-id="8176c-517">マッピング要素には、次の子要素をここに示す順序で含めることができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-517">The mapping element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="8176c-518">エイリアス (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-518">Alias (zero or more)</span></span>
-   <span data-ttu-id="8176c-519">EntityContainerMapping (正確には 1 つ)</span><span class="sxs-lookup"><span data-stu-id="8176c-519">EntityContainerMapping (exactly one)</span></span>

<span data-ttu-id="8176c-520">MSL で参照される概念モデル型およびストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-520">Names of conceptual and storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="8176c-521">概念モデル名前空間の名前については、スキーマ要素 (CSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-521">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="8176c-522">ストレージ モデル名前空間の名前については、スキーマ要素 (SSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-522">For information about the storage model namespace name, see Schema Element (SSDL).</span></span> <span data-ttu-id="8176c-523">Alias 要素では、MSL で使用される名前空間の別名を定義できます。</span><span class="sxs-lookup"><span data-stu-id="8176c-523">Aliases for namespaces that are used in MSL can be defined with the Alias element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-524">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-524">Applicable Attributes</span></span>

<span data-ttu-id="8176c-525">次の表に適用できる属性の説明、**マッピング**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-525">The table below describes the attributes that can be applied to the **Mapping** element.</span></span>

| <span data-ttu-id="8176c-526">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-526">Attribute Name</span></span> | <span data-ttu-id="8176c-527">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-527">Is Required</span></span> | <span data-ttu-id="8176c-528">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-528">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="8176c-529">**スペース**</span><span class="sxs-lookup"><span data-stu-id="8176c-529">**Space**</span></span>      | <span data-ttu-id="8176c-530">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-530">Yes</span></span>         | <span data-ttu-id="8176c-531">**C-S**します。</span><span class="sxs-lookup"><span data-stu-id="8176c-531">**C-S**.</span></span> <span data-ttu-id="8176c-532">これは固定値で、変更できません。</span><span class="sxs-lookup"><span data-stu-id="8176c-532">This is a fixed value and cannot be changed.</span></span> |

### <a name="example"></a><span data-ttu-id="8176c-533">例</span><span class="sxs-lookup"><span data-stu-id="8176c-533">Example</span></span>

<span data-ttu-id="8176c-534">次の例は、**マッピング**School モデルの一部に基づいている要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-534">The following example shows a **Mapping** element that is based on part of the School model.</span></span> <span data-ttu-id="8176c-535">School モデルの詳細については、クイック スタート (Entity Framework) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-535">For more information about the School model, see Quickstart (Entity Framework):</span></span>

``` xml
 <Mapping Space="C-S"
          xmlns="http://schemas.microsoft.com/ado/2009/11/mapping/cs">
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

## <a name="mappingfragment-element-msl"></a><span data-ttu-id="8176c-536">MappingFragment 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-536">MappingFragment Element (MSL)</span></span>

<span data-ttu-id="8176c-537">**MappingFragment**マッピング仕様言語 (MSL) 内の要素が概念モデルのエンティティ型とテーブルまたはデータベース内のビューのプロパティの間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="8176c-537">The **MappingFragment** element in mapping specification language (MSL) defines the mapping between the properties of a conceptual model entity type and a table or view in the database.</span></span> <span data-ttu-id="8176c-538">概念モデルのエンティティ型と基になるデータベース テーブルまたはビューについては、EntityType 要素 (CSDL) と EntitySet 要素 (SSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-538">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="8176c-539">**MappingFragment** EntityTypeMapping 要素または EntitySetMapping 要素の子要素であることができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-539">The **MappingFragment** can be a child element of the EntityTypeMapping element or the EntitySetMapping element.</span></span>

<span data-ttu-id="8176c-540">**MappingFragment**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-540">The **MappingFragment** element can have the following child elements:</span></span>

-   <span data-ttu-id="8176c-541">ComplexType (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-541">ComplexType (zero or more)</span></span>
-   <span data-ttu-id="8176c-542">ScalarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-542">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="8176c-543">条件 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-543">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-544">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-544">Applicable Attributes</span></span>

<span data-ttu-id="8176c-545">次の表に適用できる属性、 **MappingFragment**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-545">The following table describes the attributes that can be applied to the **MappingFragment** element.</span></span>

| <span data-ttu-id="8176c-546">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-546">Attribute Name</span></span>          | <span data-ttu-id="8176c-547">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-547">Is Required</span></span> | <span data-ttu-id="8176c-548">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-548">Value</span></span>                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-549">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="8176c-549">**StoreEntitySet**</span></span>      | <span data-ttu-id="8176c-550">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-550">Yes</span></span>         | <span data-ttu-id="8176c-551">マップされるテーブルまたはビューの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-551">The name of the table or view that is being mapped.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="8176c-552">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="8176c-552">**MakeColumnsDistinct**</span></span> | <span data-ttu-id="8176c-553">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-553">No</span></span>          | <span data-ttu-id="8176c-554">**True**または**False**個別の行のみが返されるかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="8176c-554">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="8176c-555">この属性設定されている場合**True**、 **GenerateUpdateViews**に EntityContainerMapping 要素の属性を設定する必要があります**False**します。</span><span class="sxs-lookup"><span data-stu-id="8176c-555">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

### <a name="example"></a><span data-ttu-id="8176c-556">例</span><span class="sxs-lookup"><span data-stu-id="8176c-556">Example</span></span>

<span data-ttu-id="8176c-557">次の例は、 **MappingFragment**要素の子として、 **EntityTypeMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-557">The following example shows a **MappingFragment** element as the child of an **EntityTypeMapping** element.</span></span> <span data-ttu-id="8176c-558">この例では、プロパティでは、**コース**概念モデル型の列にマップ、**コース**データベース内のテーブル。</span><span class="sxs-lookup"><span data-stu-id="8176c-558">In this example, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="8176c-559">例</span><span class="sxs-lookup"><span data-stu-id="8176c-559">Example</span></span>

<span data-ttu-id="8176c-560">次の例は、 **MappingFragment**要素の子として、 **EntitySetMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-560">The following example shows a **MappingFragment** element as the child of an **EntitySetMapping** element.</span></span> <span data-ttu-id="8176c-561">上記のプロパティの例のように、**コース**概念モデル型の列にマップ、**コース**データベース内のテーブル。</span><span class="sxs-lookup"><span data-stu-id="8176c-561">As in the example above, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

## <a name="modificationfunctionmapping-element-msl"></a><span data-ttu-id="8176c-562">ModificationFunctionMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-562">ModificationFunctionMapping Element (MSL)</span></span>

<span data-ttu-id="8176c-563">**ModificationFunctionMapping**マッピング仕様言語 (MSL) 内の要素のマップ、挿入、更新、および基になるデータベース内のストアド プロシージャを概念モデルのエンティティ型の関数を削除します。</span><span class="sxs-lookup"><span data-stu-id="8176c-563">The **ModificationFunctionMapping** element in mapping specification language (MSL) maps the insert, update, and delete functions of a conceptual model entity type to stored procedures in the underlying database.</span></span> <span data-ttu-id="8176c-564">**ModificationFunctionMapping**要素が挿入のマップも、および基になるデータベース内のストアド プロシージャを概念モデルでの多対多のアソシエーションの関数を削除できます。</span><span class="sxs-lookup"><span data-stu-id="8176c-564">The **ModificationFunctionMapping** element can also map the insert and delete functions for many-to-many associations in the conceptual model to stored procedures in the underlying database.</span></span> <span data-ttu-id="8176c-565">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-565">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="8176c-566">詳細については、関数要素 (SSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-566">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="8176c-567">マップしない場合は、3 つすべての挿入、更新、または削除操作をストアド プロシージャにエンティティ型の実行時に実行された場合にマップされていない操作は失敗および UpdateException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="8176c-567">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>


> [!NOTE]
> <span data-ttu-id="8176c-568">継承階層の 1 つのエンティティの変更関数をストアド プロシージャにマップした場合、階層内のすべての型の変更関数がストアド プロシージャにマップされる必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-568">If the modification functions for one entity in an inheritance hierarchy are mapped to stored procedures, then modification functions for all types in the hierarchy must be mapped to stored procedures.</span></span>

<span data-ttu-id="8176c-569">**ModificationFunctionMapping**要素が EntityTypeMapping 要素または AssociationSetMapping 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-569">The **ModificationFunctionMapping** element can be a child of the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

<span data-ttu-id="8176c-570">**ModificationFunctionMapping**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-570">The **ModificationFunctionMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="8176c-571">DeleteFunction (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="8176c-571">DeleteFunction (zero or one)</span></span>
-   <span data-ttu-id="8176c-572">InsertFunction (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="8176c-572">InsertFunction (zero or one)</span></span>
-   <span data-ttu-id="8176c-573">UpdateFunction (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="8176c-573">UpdateFunction (zero or one)</span></span>

<span data-ttu-id="8176c-574">適用可能な属性がない、 **ModificationFunctionMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-574">No attributes are applicable to the **ModificationFunctionMapping** element.</span></span>

### <a name="example"></a><span data-ttu-id="8176c-575">例</span><span class="sxs-lookup"><span data-stu-id="8176c-575">Example</span></span>

<span data-ttu-id="8176c-576">次の例は、エンティティ セット マッピングを**人**School モデルのエンティティ セットします。</span><span class="sxs-lookup"><span data-stu-id="8176c-576">The following example shows the entity set mapping for the **People** entity set in the School model.</span></span> <span data-ttu-id="8176c-577">列マッピングに加えて、 **Person**エンティティ型、マッピングの挿入、更新、および削除関数の**Person**型が表示されます。</span><span class="sxs-lookup"><span data-stu-id="8176c-577">In addition to the column mapping for the **Person** entity type, the mapping of the insert, update, and delete functions of the **Person** type are shown.</span></span> <span data-ttu-id="8176c-578">ストレージ モデルにマップされて宣言されている関数。</span><span class="sxs-lookup"><span data-stu-id="8176c-578">The functions that are mapped to are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="8176c-579">例</span><span class="sxs-lookup"><span data-stu-id="8176c-579">Example</span></span>

<span data-ttu-id="8176c-580">次の例は、アソシエーション セット マッピングを**CourseInstructor** School モデルの関連付けセットします。</span><span class="sxs-lookup"><span data-stu-id="8176c-580">The following example shows the association set mapping for the **CourseInstructor** association set in the School model.</span></span> <span data-ttu-id="8176c-581">列マッピングに加えて、 **CourseInstructor**アソシエーションの挿入および削除関数のマッピング、 **CourseInstructor**の関連付けが表示されます。</span><span class="sxs-lookup"><span data-stu-id="8176c-581">In addition to the column mapping for the **CourseInstructor** association, the mapping of the insert and delete functions of the **CourseInstructor** association are shown.</span></span> <span data-ttu-id="8176c-582">ストレージ モデルにマップされて宣言されている関数。</span><span class="sxs-lookup"><span data-stu-id="8176c-582">The functions that are mapped to are declared in the storage model.</span></span>

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
 

 

## <a name="queryview-element-msl"></a><span data-ttu-id="8176c-583">QueryView 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-583">QueryView Element (MSL)</span></span>

<span data-ttu-id="8176c-584">**QueryView**マッピング仕様言語 (MSL) 内の要素は、概念モデルと基になるデータベース テーブルにエンティティ型またはアソシエーション間の読み取り専用マッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="8176c-584">The **QueryView** element in mapping specification language (MSL) defines a read-only mapping between an entity type or association in the conceptual model and a table in the underlying database.</span></span> <span data-ttu-id="8176c-585">マッピングは、ストレージ モデルに対して評価される Entity SQL クエリを使用して定義され、結果セットのエンティティまたはアソシエーションが概念モデルの観点からの express。</span><span class="sxs-lookup"><span data-stu-id="8176c-585">The mapping is defined with an Entity SQL query that is evaluated against the storage model, and you express the result set in terms of an entity or association in the conceptual model.</span></span> <span data-ttu-id="8176c-586">クエリ ビューは読み取り専用であるため、標準の更新コマンドを使用してクエリ ビューで定義された型を更新することはできません。</span><span class="sxs-lookup"><span data-stu-id="8176c-586">Because query views are read-only, you cannot use standard update commands to update types that are defined by query views.</span></span> <span data-ttu-id="8176c-587">型の更新を行うには、変更関数を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-587">You can make updates to these types by using modification functions.</span></span> <span data-ttu-id="8176c-588">詳細については、次を参照してください。 方法: ストアド プロシージャを変更関数をマップします。</span><span class="sxs-lookup"><span data-stu-id="8176c-588">For more information, see How to: Map Modification Functions to Stored Procedures.</span></span>

> [!NOTE]
> <span data-ttu-id="8176c-589">**QueryView**要素では、Entity SQL 式が含まれている**GroupBy**、グループ集計、またはナビゲーション プロパティはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="8176c-589">In the **QueryView** element, Entity SQL expressions that contain **GroupBy**, group aggregates, or navigation properties are not supported.</span></span>

 

<span data-ttu-id="8176c-590">**QueryView**要素 EntitySetMapping 要素または AssociationSetMapping 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-590">The **QueryView** element can be a child of the EntitySetMapping element or the AssociationSetMapping element.</span></span> <span data-ttu-id="8176c-591">前者の場合、クエリ ビューは、概念モデルのエンティティの読み取り専用マッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="8176c-591">In the former case, the query view defines a read-only mapping for an entity in the conceptual model.</span></span> <span data-ttu-id="8176c-592">後者の場合、クエリ ビューは、概念モデルのアソシエーションの読み取り専用マッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="8176c-592">In the latter case, the query view defines a read-only mapping for an association in the conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="8176c-593">場合、 **AssociationSetMapping**要素は、参照に関する制約のあるアソシエーションには、 **AssociationSetMapping**要素は無視されます。</span><span class="sxs-lookup"><span data-stu-id="8176c-593">If the **AssociationSetMapping** element is for an association with a referential constraint, the **AssociationSetMapping** element is ignored.</span></span> <span data-ttu-id="8176c-594">詳細については、ReferentialConstraint 要素 (CSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-594">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="8176c-595">**QueryView**要素は、すべての子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="8176c-595">The **QueryView** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-596">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-596">Applicable Attributes</span></span>

<span data-ttu-id="8176c-597">次の表に適用できる属性、 **QueryView**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-597">The following table describes the attributes that can be applied to the **QueryView** element.</span></span>

| <span data-ttu-id="8176c-598">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-598">Attribute Name</span></span> | <span data-ttu-id="8176c-599">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-599">Is Required</span></span> | <span data-ttu-id="8176c-600">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-600">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-601">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="8176c-601">**TypeName**</span></span>   | <span data-ttu-id="8176c-602">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-602">No</span></span>          | <span data-ttu-id="8176c-603">クエリ ビューによってマップされる概念モデル型の名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-603">The name of the conceptual model type that is being mapped by the query view.</span></span> |

### <a name="example"></a><span data-ttu-id="8176c-604">例</span><span class="sxs-lookup"><span data-stu-id="8176c-604">Example</span></span>

<span data-ttu-id="8176c-605">次の例は、 **QueryView**要素の子として、 **EntitySetMapping**要素のクエリ ビュー マッピングを定義し、**部門**内のエンティティ型、School モデル。</span><span class="sxs-lookup"><span data-stu-id="8176c-605">The following example shows the **QueryView** element as a child of the **EntitySetMapping** element and defines a query view mapping for the **Department** entity type in the School Model.</span></span>

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

<span data-ttu-id="8176c-606">クエリでは、メンバーのサブセットのみを返すため、**部門**、ストレージ モデル内の型、**部門**次のように、このマッピングに基づいて、School モデルの種類が変更されました。</span><span class="sxs-lookup"><span data-stu-id="8176c-606">Because the query only returns a subset of the members of the **Department** type in the storage model, the **Department** type in the School model has been modified based on this mapping as follows:</span></span>

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

### <a name="example"></a><span data-ttu-id="8176c-607">例</span><span class="sxs-lookup"><span data-stu-id="8176c-607">Example</span></span>

<span data-ttu-id="8176c-608">例を次に示します、 **QueryView**要素の子として、 **AssociationSetMapping**要素の読み取り専用マッピングを定義し、 `FK_Course_Department` School モデルのアソシエーション。</span><span class="sxs-lookup"><span data-stu-id="8176c-608">The next example shows the **QueryView** element as the child of an **AssociationSetMapping** element and defines a read-only mapping for the `FK_Course_Department` association in the School model.</span></span>

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
 
### <a name="comments"></a><span data-ttu-id="8176c-609">コメント</span><span class="sxs-lookup"><span data-stu-id="8176c-609">Comments</span></span>

<span data-ttu-id="8176c-610">クエリ ビューを定義して、次のシナリオを実現できます。</span><span class="sxs-lookup"><span data-stu-id="8176c-610">You can define query views to enable the following scenarios:</span></span>

-   <span data-ttu-id="8176c-611">ストレージ モデルのエンティティのプロパティの一部を含まない、概念モデルのエンティティを定義する。</span><span class="sxs-lookup"><span data-stu-id="8176c-611">Define an entity in the conceptual model that doesn't include all the properties of the entity in the storage model.</span></span> <span data-ttu-id="8176c-612">既定値がないと、サポートしないプロパティが含まれます**null**値。</span><span class="sxs-lookup"><span data-stu-id="8176c-612">This includes properties that do not have default values and do not support **null** values.</span></span>
-   <span data-ttu-id="8176c-613">ストレージ モデルの計算列を概念モデルのエンティティ型のプロパティにマップする。</span><span class="sxs-lookup"><span data-stu-id="8176c-613">Map computed columns in the storage model to properties of entity types in the conceptual model.</span></span>
-   <span data-ttu-id="8176c-614">概念モデルのエンティティをパーティション分割するために使用される条件が等価性に基づかないマッピングを定義する。</span><span class="sxs-lookup"><span data-stu-id="8176c-614">Define a mapping where conditions used to partition entities in the conceptual model are not based on equality.</span></span> <span data-ttu-id="8176c-615">使用して、条件付きのマッピングを指定すると、**条件**要素を指定された条件が指定された値と等しく必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-615">When you specify a conditional mapping using the **Condition** element, the supplied condition must equal the specified value.</span></span> <span data-ttu-id="8176c-616">詳細については、条件の要素 (MSL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-616">For more information, see Condition Element (MSL).</span></span>
-   <span data-ttu-id="8176c-617">ストレージ モデルの同一の列を概念モデルの複数の型にマップする。</span><span class="sxs-lookup"><span data-stu-id="8176c-617">Map the same column in the storage model to multiple types in the conceptual model.</span></span>
-   <span data-ttu-id="8176c-618">複数の型を同じテーブルにマップする。</span><span class="sxs-lookup"><span data-stu-id="8176c-618">Map multiple types to the same table.</span></span>
-   <span data-ttu-id="8176c-619">リレーショナル スキーマの外部キーに基づかない概念モデルのアソシエーションを定義する。</span><span class="sxs-lookup"><span data-stu-id="8176c-619">Define associations in the conceptual model that are not based on foreign keys in the relational schema.</span></span>
-   <span data-ttu-id="8176c-620">カスタム ビジネス ロジックを使用して、概念モデルのプロパティの値を設定する。</span><span class="sxs-lookup"><span data-stu-id="8176c-620">Use custom business logic to set the value of properties in the conceptual model.</span></span> <span data-ttu-id="8176c-621">文字列値"T"の値にデータ ソースをマップするなど、 **true**、概念モデルでのブール値。</span><span class="sxs-lookup"><span data-stu-id="8176c-621">For example, you could map the string value "T" in the data source to a value of **true**, a Boolean, in the conceptual model.</span></span>
-   <span data-ttu-id="8176c-622">クエリ結果に対する条件付きフィルターを定義する。</span><span class="sxs-lookup"><span data-stu-id="8176c-622">Define conditional filters for query results.</span></span>
-   <span data-ttu-id="8176c-623">概念モデルのデータに適用する制限をストレージ モデルよりも少なくする。</span><span class="sxs-lookup"><span data-stu-id="8176c-623">Enforce fewer restrictions on data in the conceptual model than in the storage model.</span></span> <span data-ttu-id="8176c-624">たとえば、行うことができます、プロパティ、概念モデルで null 許容がマップされている列がサポートしていない場合でも**null**値。</span><span class="sxs-lookup"><span data-stu-id="8176c-624">For example, you could make a property in the conceptual model nullable even if the column to which it is mapped does not support **null**values.</span></span>

<span data-ttu-id="8176c-625">エンティティのクエリ ビューを定義する際は、次の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-625">The following considerations apply when you define query views for entities:</span></span>

-   <span data-ttu-id="8176c-626">クエリ ビューは読み取り専用です。</span><span class="sxs-lookup"><span data-stu-id="8176c-626">Query views are read-only.</span></span> <span data-ttu-id="8176c-627">エンティティの更新を行うには、変更関数を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-627">You can only make updates to entities by using modification functions.</span></span>
-   <span data-ttu-id="8176c-628">クエリ ビューでエンティティ型を定義する場合、すべての関連エンティティもクエリ ビューで定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-628">When you define an entity type by a query view, you must also define all related entities by query views.</span></span>
-   <span data-ttu-id="8176c-629">定義する必要があります、リレーショナル スキーマでリンク テーブルを表すストレージ モデルのエンティティに多対多のアソシエーションをマップするときに、 **QueryView**内の要素、 **AssociationSetMapping**このリンク テーブルの要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-629">When you map a many-to-many association to an entity in the storage model that represents a link table in the relational schema, you must define a **QueryView** element in the **AssociationSetMapping** element for this link table.</span></span>
-   <span data-ttu-id="8176c-630">クエリ ビューは、型階層のすべての型に対して定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-630">Query views must be defined for all types in a type hierarchy.</span></span> <span data-ttu-id="8176c-631">これは、次の方法で行うことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-631">You can do this in the following ways:</span></span>
-   -   <span data-ttu-id="8176c-632">1 つ**QueryView**を階層内のすべてのエンティティ型の和集合を返す 1 つの Entity SQL クエリを指定する要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-632">With a single **QueryView** element that specifies a single Entity SQL query that returns a union of all of the entity types in the hierarchy.</span></span>
    -   <span data-ttu-id="8176c-633">1 つ**QueryView**特定の条件に基づいて、階層内の特定のエンティティ型を取得するケースの演算子を使用する 1 つの Entity SQL クエリを指定する要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-633">With a single **QueryView** element that specifies a single Entity SQL query that uses the CASE operator to return a specific entity type in the hierarchy based on a specific condition.</span></span>
    -   <span data-ttu-id="8176c-634">追加された**QueryView**階層内の特定の型の要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-634">With an additional **QueryView** element for a specific type in the hierarchy.</span></span> <span data-ttu-id="8176c-635">ここでは、使用して、 **TypeName**の属性、 **QueryView**それぞれのビューのエンティティの種類を指定する要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-635">In this case, use the **TypeName** attribute of the **QueryView** element to specify the entity type for each view.</span></span>
-   <span data-ttu-id="8176c-636">クエリ ビューが定義されている場合は指定できません、 **StorageSetName**属性を**EntitySetMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-636">When a query view is defined, you cannot specify the **StorageSetName** attribute on the **EntitySetMapping** element.</span></span>
-   <span data-ttu-id="8176c-637">クエリ ビューが定義されている場合、 **EntitySetMapping**要素に含めることはできませんも**プロパティ**マッピングします。</span><span class="sxs-lookup"><span data-stu-id="8176c-637">When a query view is defined, the **EntitySetMapping**element cannot also contain **Property** mappings.</span></span>

## <a name="resultbinding-element-msl"></a><span data-ttu-id="8176c-638">ResultBinding 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-638">ResultBinding Element (MSL)</span></span>

<span data-ttu-id="8176c-639">**ResultBinding**マッピング仕様言語 (MSL) 要素は、列の値によって返されるストアド プロシージャを概念モデルのエンティティのプロパティのエンティティ型の変更関数がマップされるときにストアドをマップします。基になるデータベースでの手順です。</span><span class="sxs-lookup"><span data-stu-id="8176c-639">The **ResultBinding** element in mapping specification language (MSL) maps column values that are returned by stored procedures to entity properties in the conceptual model when entity type modification functions are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="8176c-640">ストアド プロシージャの id 列の値が insert ステートメントで返される場合など、 **ResultBinding**要素には、概念モデルのエンティティ型プロパティに返される値がマップされます。</span><span class="sxs-lookup"><span data-stu-id="8176c-640">For example, when the value of an identity column is returned by an insert stored procedure, the **ResultBinding** element maps the returned value to an entity type property in the conceptual model.</span></span>

<span data-ttu-id="8176c-641">**ResultBinding**要素は InsertFunction 要素または UpdateFunction 要素の子であることができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-641">The **ResultBinding** element can be child of the InsertFunction element or the UpdateFunction element.</span></span>

<span data-ttu-id="8176c-642">**ResultBinding**要素は、すべての子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="8176c-642">The **ResultBinding** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-643">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-643">Applicable Attributes</span></span>

<span data-ttu-id="8176c-644">次の表に、属性に適用される、 **ResultBinding**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-644">The following table describes the attributes that are applicable to the **ResultBinding** element:</span></span>

| <span data-ttu-id="8176c-645">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-645">Attribute Name</span></span> | <span data-ttu-id="8176c-646">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-646">Is Required</span></span> | <span data-ttu-id="8176c-647">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-647">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-648">**Name**</span><span class="sxs-lookup"><span data-stu-id="8176c-648">**Name**</span></span>       | <span data-ttu-id="8176c-649">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-649">Yes</span></span>         | <span data-ttu-id="8176c-650">マップされる概念モデルのエンティティ プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-650">The name of the entity property in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="8176c-651">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="8176c-651">**ColumnName**</span></span> | <span data-ttu-id="8176c-652">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-652">Yes</span></span>         | <span data-ttu-id="8176c-653">マップされる列の名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-653">The name of the column being mapped.</span></span>                                          |

### <a name="example"></a><span data-ttu-id="8176c-654">例</span><span class="sxs-lookup"><span data-stu-id="8176c-654">Example</span></span>

<span data-ttu-id="8176c-655">次の例は、School モデルに基づいておりを示しています、 **InsertFunction**要素の挿入関数をマップするために使用、 **Person**エンティティ型を**InsertPerson**ストアド プロシージャです。</span><span class="sxs-lookup"><span data-stu-id="8176c-655">The following example is based on the School model and shows an **InsertFunction** element used to map the insert function of the **Person** entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="8176c-656">(、 **InsertPerson**ストアド プロシージャを次に示し、ストレージ モデルで宣言されています)。A **ResultBinding**要素を使用して、ストアド プロシージャによって返される列の値をマップ (**NewPersonID**) エンティティ型プロパティ (**PersonID**)。</span><span class="sxs-lookup"><span data-stu-id="8176c-656">(The **InsertPerson** stored procedure is shown below and is declared in the storage model.) A **ResultBinding** element is used to map a column value that is returned by the stored procedure (**NewPersonID**) to an entity type property (**PersonID**).</span></span>

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

<span data-ttu-id="8176c-657">次の TRANSACT-SQL について説明します、 **InsertPerson**ストアド プロシージャ。</span><span class="sxs-lookup"><span data-stu-id="8176c-657">The following Transact-SQL describes the **InsertPerson** stored procedure:</span></span>

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

## <a name="resultmapping-element-msl"></a><span data-ttu-id="8176c-658">ResultMapping 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-658">ResultMapping Element (MSL)</span></span>

<span data-ttu-id="8176c-659">**ResultMapping**マッピング仕様言語 (MSL) 要素は、次に該当する場合、基になるデータベースで概念モデルの関数インポートとストアド プロシージャ間のマッピングを定義します。</span><span class="sxs-lookup"><span data-stu-id="8176c-659">The **ResultMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="8176c-660">関数インポートが概念モデルのエンティティ型または複合型を返す場合</span><span class="sxs-lookup"><span data-stu-id="8176c-660">The function import returns a conceptual model entity type or complex type.</span></span>
-   <span data-ttu-id="8176c-661">ストアド プロシージャが返す列の名前が、エンティティ型または複合型のプロパティの名前と完全には一致しない場合</span><span class="sxs-lookup"><span data-stu-id="8176c-661">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the entity type or complex type.</span></span>

<span data-ttu-id="8176c-662">既定では、ストアド プロシージャが返す列とエンティティ型または複合型とのマッピングは、列およびプロパティの名前に基づいています。</span><span class="sxs-lookup"><span data-stu-id="8176c-662">By default, the mapping between the columns returned by a stored procedure and an entity type or complex type is based on column and property names.</span></span> <span data-ttu-id="8176c-663">使用する必要がある列の名前がプロパティの名前一致も一致しない場合、 **ResultMapping**マッピングを定義する要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-663">If column names do not exactly match property names, you must use the **ResultMapping** element to define the mapping.</span></span> <span data-ttu-id="8176c-664">既定のマッピングの例は、FunctionImportMapping 要素 (MSL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-664">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="8176c-665">**ResultMapping**要素は FunctionImportMapping 要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="8176c-665">The **ResultMapping** element is a child element of the FunctionImportMapping element.</span></span>

<span data-ttu-id="8176c-666">**ResultMapping**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-666">The **ResultMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="8176c-667">EntityTypeMapping (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-667">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="8176c-668">ComplexTypeMapping</span><span class="sxs-lookup"><span data-stu-id="8176c-668">ComplexTypeMapping</span></span>

<span data-ttu-id="8176c-669">適用可能な属性がない、 **ResultMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-669">No attributes are applicable to the **ResultMapping** Element.</span></span>

### <a name="example"></a><span data-ttu-id="8176c-670">例</span><span class="sxs-lookup"><span data-stu-id="8176c-670">Example</span></span>

<span data-ttu-id="8176c-671">次のストアド プロシージャを考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8176c-671">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="8176c-672">また、次の概念モデルのエンティティ型を考えてみます。</span><span class="sxs-lookup"><span data-stu-id="8176c-672">Also consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="8176c-673">以前のエンティティ型のインスタンスを返す関数インポートを作成するためには、ストアド プロシージャによって返される列間のマッピングとでエンティティ型を定義する必要があります、 **ResultMapping**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-673">In order to create a function import that returns instances of the previous entity type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ResultMapping** element:</span></span>

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

## <a name="scalarproperty-element-msl"></a><span data-ttu-id="8176c-674">ScalarProperty 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-674">ScalarProperty Element (MSL)</span></span>

<span data-ttu-id="8176c-675">**ScalarProperty**マッピング仕様言語 (MSL) 要素は、概念モデルのエンティティ型、複合型、またはアソシエーションのプロパティをテーブルの列または基になるデータベースでストアド プロシージャのパラメーターにマップします。</span><span class="sxs-lookup"><span data-stu-id="8176c-675">The **ScalarProperty** element in mapping specification language (MSL) maps a property on a conceptual model entity type, complex type, or association to a table column or stored procedure parameter in the underlying database.</span></span>

> [!NOTE]
> <span data-ttu-id="8176c-676">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-676">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="8176c-677">詳細については、関数要素 (SSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-677">For more information, see Function Element (SSDL).</span></span>

<span data-ttu-id="8176c-678">**ScalarProperty**要素は、次の要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-678">The **ScalarProperty** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="8176c-679">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="8176c-679">MappingFragment</span></span>
-   <span data-ttu-id="8176c-680">InsertFunction</span><span class="sxs-lookup"><span data-stu-id="8176c-680">InsertFunction</span></span>
-   <span data-ttu-id="8176c-681">UpdateFunction</span><span class="sxs-lookup"><span data-stu-id="8176c-681">UpdateFunction</span></span>
-   <span data-ttu-id="8176c-682">DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="8176c-682">DeleteFunction</span></span>
-   <span data-ttu-id="8176c-683">EndProperty</span><span class="sxs-lookup"><span data-stu-id="8176c-683">EndProperty</span></span>
-   <span data-ttu-id="8176c-684">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="8176c-684">ComplexProperty</span></span>
-   <span data-ttu-id="8176c-685">ResultMapping</span><span class="sxs-lookup"><span data-stu-id="8176c-685">ResultMapping</span></span>

<span data-ttu-id="8176c-686">子として、 **MappingFragment**、 **ComplexProperty**、または**EndProperty**要素、 **ScalarProperty**要素のプロパティをマップします。データベース内の列を概念モデル。</span><span class="sxs-lookup"><span data-stu-id="8176c-686">As a child of the **MappingFragment**, **ComplexProperty**, or **EndProperty** element, the **ScalarProperty** element maps a property in the conceptual model to a column in the database.</span></span> <span data-ttu-id="8176c-687">子として、 **InsertFunction**、 **UpdateFunction**、または**DeleteFunction**要素、 **ScalarProperty**要素のプロパティをマップします。ストアド プロシージャのパラメーターに、概念モデル。</span><span class="sxs-lookup"><span data-stu-id="8176c-687">As a child of the **InsertFunction**, **UpdateFunction**, or **DeleteFunction** element, the **ScalarProperty** element maps a property in the conceptual model to a stored procedure parameter.</span></span>

<span data-ttu-id="8176c-688">**ScalarProperty**要素は、すべての子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="8176c-688">The **ScalarProperty** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-689">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-689">Applicable Attributes</span></span>

<span data-ttu-id="8176c-690">適用される属性、 **ScalarProperty**要素が要素の役割によって異なります。</span><span class="sxs-lookup"><span data-stu-id="8176c-690">The attributes that apply to the **ScalarProperty** element differ depending on the role of the element.</span></span>

<span data-ttu-id="8176c-691">次の表に、適用されるときに、属性、 **ScalarProperty**要素を使用して概念モデルのプロパティをデータベース内の列にマップします。</span><span class="sxs-lookup"><span data-stu-id="8176c-691">The following table describes the attributes that are applicable when the **ScalarProperty** element is used to map a conceptual model property to a column in the database:</span></span>

| <span data-ttu-id="8176c-692">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-692">Attribute Name</span></span> | <span data-ttu-id="8176c-693">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-693">Is Required</span></span> | <span data-ttu-id="8176c-694">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-694">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="8176c-695">**Name**</span><span class="sxs-lookup"><span data-stu-id="8176c-695">**Name**</span></span>       | <span data-ttu-id="8176c-696">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-696">Yes</span></span>         | <span data-ttu-id="8176c-697">マップされる概念モデルのプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-697">The name of the conceptual model property that is being mapped.</span></span> |
| <span data-ttu-id="8176c-698">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="8176c-698">**ColumnName**</span></span> | <span data-ttu-id="8176c-699">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-699">Yes</span></span>         | <span data-ttu-id="8176c-700">マップされるテーブル列の名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-700">The name of the table column that is being mapped.</span></span>              |

<span data-ttu-id="8176c-701">次の表に、属性に適用される、 **ScalarProperty**概念モデルのプロパティをストアド プロシージャのパラメーターにマップする際の要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-701">The following table describes the attributes that are applicable to the **ScalarProperty** element when it is used to map a conceptual model property to a stored procedure parameter:</span></span>

| <span data-ttu-id="8176c-702">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-702">Attribute Name</span></span>    | <span data-ttu-id="8176c-703">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-703">Is Required</span></span> | <span data-ttu-id="8176c-704">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-704">Value</span></span>                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-705">**Name**</span><span class="sxs-lookup"><span data-stu-id="8176c-705">**Name**</span></span>          | <span data-ttu-id="8176c-706">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-706">Yes</span></span>         | <span data-ttu-id="8176c-707">マップされる概念モデルのプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-707">The name of the conceptual model property that is being mapped.</span></span>                                                                                 |
| <span data-ttu-id="8176c-708">**ParameterName**</span><span class="sxs-lookup"><span data-stu-id="8176c-708">**ParameterName**</span></span> | <span data-ttu-id="8176c-709">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-709">Yes</span></span>         | <span data-ttu-id="8176c-710">マップされるパラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-710">The name of the parameter that is being mapped.</span></span>                                                                                                 |
| <span data-ttu-id="8176c-711">**Version**</span><span class="sxs-lookup"><span data-stu-id="8176c-711">**Version**</span></span>       | <span data-ttu-id="8176c-712">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-712">No</span></span>          | <span data-ttu-id="8176c-713">**現在**または**元**現在の値またはプロパティの元の値を同時実行制御チェックを使用する必要があるかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="8176c-713">**Current** or **Original** depending on whether the current value or the original value of the property should be used for concurrency checks.</span></span> |

### <a name="example"></a><span data-ttu-id="8176c-714">例</span><span class="sxs-lookup"><span data-stu-id="8176c-714">Example</span></span>

<span data-ttu-id="8176c-715">次の例は、 **ScalarProperty** 2 つの方法で使用される要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-715">The following example shows the **ScalarProperty** element used in two ways:</span></span>

-   <span data-ttu-id="8176c-716">プロパティをマップする、 **Person**エンティティ型の列を**Person**テーブル。</span><span class="sxs-lookup"><span data-stu-id="8176c-716">To map the properties of the **Person** entity type to the columns of the **Person**table.</span></span>
-   <span data-ttu-id="8176c-717">プロパティをマップする、 **Person**エンティティ型のパラメーターを**UpdatePerson**ストアド プロシージャ。</span><span class="sxs-lookup"><span data-stu-id="8176c-717">To map the properties of the **Person** entity type to the parameters of the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="8176c-718">ストアド プロシージャはストレージ モデル内で宣言されます。</span><span class="sxs-lookup"><span data-stu-id="8176c-718">The stored procedures are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="8176c-719">例</span><span class="sxs-lookup"><span data-stu-id="8176c-719">Example</span></span>

<span data-ttu-id="8176c-720">例を次に示します、 **ScalarProperty**要素の挿入をマップし、データベース内のストアド プロシージャを概念モデル アソシエーションの関数を削除するために使用します。</span><span class="sxs-lookup"><span data-stu-id="8176c-720">The next example shows the **ScalarProperty** element used to map the insert and delete functions of a conceptual model association to stored procedures in the database.</span></span> <span data-ttu-id="8176c-721">ストアド プロシージャはストレージ モデル内で宣言されます。</span><span class="sxs-lookup"><span data-stu-id="8176c-721">The stored procedures are declared in the storage model.</span></span>

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

## <a name="updatefunction-element-msl"></a><span data-ttu-id="8176c-722">UpdateFunction 要素 (MSL)</span><span class="sxs-lookup"><span data-stu-id="8176c-722">UpdateFunction Element (MSL)</span></span>

<span data-ttu-id="8176c-723">**UpdateFunction**マッピング仕様言語 (MSL) 内の要素は、概念モデルのエンティティ型の更新関数を基になるデータベース内のストアド プロシージャにマップします。</span><span class="sxs-lookup"><span data-stu-id="8176c-723">The **UpdateFunction** element in mapping specification language (MSL) maps the update function of an entity type in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="8176c-724">変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-724">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="8176c-725">詳細については、関数要素 (SSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8176c-725">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
>  <span data-ttu-id="8176c-726">マップしない場合は、3 つすべての挿入、更新、または削除操作をストアド プロシージャにエンティティ型の実行時に実行された場合にマップされていない操作は失敗および UpdateException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="8176c-726">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="8176c-727">**UpdateFunction**要素 ModificationFunctionMapping 要素の子にできるし、EntityTypeMapping 要素に適用します。</span><span class="sxs-lookup"><span data-stu-id="8176c-727">The **UpdateFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element.</span></span>

<span data-ttu-id="8176c-728">**UpdateFunction**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="8176c-728">The **UpdateFunction** element can have the following child elements:</span></span>

-   <span data-ttu-id="8176c-729">AssociationEnd (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-729">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="8176c-730">ComplexProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-730">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="8176c-731">ResultBinding (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="8176c-731">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="8176c-732">ScarlarProperty (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="8176c-732">ScarlarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="8176c-733">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="8176c-733">Applicable Attributes</span></span>

<span data-ttu-id="8176c-734">次の表に適用できる属性、 **UpdateFunction**要素。</span><span class="sxs-lookup"><span data-stu-id="8176c-734">The following table describes the attributes that can be applied to the **UpdateFunction** element.</span></span>

| <span data-ttu-id="8176c-735">属性名</span><span class="sxs-lookup"><span data-stu-id="8176c-735">Attribute Name</span></span>            | <span data-ttu-id="8176c-736">必須</span><span class="sxs-lookup"><span data-stu-id="8176c-736">Is Required</span></span> | <span data-ttu-id="8176c-737">[値]</span><span class="sxs-lookup"><span data-stu-id="8176c-737">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="8176c-738">**FunctionName**</span><span class="sxs-lookup"><span data-stu-id="8176c-738">**FunctionName**</span></span>          | <span data-ttu-id="8176c-739">はい</span><span class="sxs-lookup"><span data-stu-id="8176c-739">Yes</span></span>         | <span data-ttu-id="8176c-740">更新関数のマップ先となるストアド プロシージャの名前空間修飾名。</span><span class="sxs-lookup"><span data-stu-id="8176c-740">The namespace-qualified name of the stored procedure to which the update function is mapped.</span></span> <span data-ttu-id="8176c-741">ストアド プロシージャはストレージ モデル内で宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8176c-741">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="8176c-742">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="8176c-742">**RowsAffectedParameter**</span></span> | <span data-ttu-id="8176c-743">いいえ</span><span class="sxs-lookup"><span data-stu-id="8176c-743">No</span></span>          | <span data-ttu-id="8176c-744">影響を受ける行の数を返す出力パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="8176c-744">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

### <a name="example"></a><span data-ttu-id="8176c-745">例</span><span class="sxs-lookup"><span data-stu-id="8176c-745">Example</span></span>

<span data-ttu-id="8176c-746">次の例は、School モデルに基づいておりを示しています、 **UpdateFunction**の update 関数をマップするための要素、 **Person**エンティティ型を**UpdatePerson**ストアド プロシージャです。</span><span class="sxs-lookup"><span data-stu-id="8176c-746">The following example is based on the School model and shows the **UpdateFunction** element used to map update function of the **Person** entity type to the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="8176c-747">**UpdatePerson**ストアド プロシージャはストレージ モデルで宣言されています。</span><span class="sxs-lookup"><span data-stu-id="8176c-747">The **UpdatePerson** stored procedure is declared in the storage model.</span></span>

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
