---
title: EF6 の MSL 仕様
author: divega
ms.date: 2016-10-23
ms.assetid: 13ae7bc1-74b4-4ee4-8d73-c337be841467
ms.openlocfilehash: 77dc7072c70b104188cd23974f32308960daebb6
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996032"
---
# <a name="msl-specification"></a>MSL 仕様
マッピング仕様言語 (MSL) は、概念モデルと Entity Framework アプリケーションのストレージ モデル間のマッピングを記述する XML ベースの言語です。

Entity Framework アプリケーションでは、マッピング メタデータはビルド時に .msl ファイル (MSL で記述) から読み込まれます。 Entity Framework では、実行時にマッピング メタデータを使用して、ストア固有のコマンドを概念モデルに対するクエリに変換します。

Entity Framework デザイナー (EF Designer) では、デザイン時に .edmx ファイルのマッピング情報を格納します。 ビルド時に、エンティティ デザイナー情報を使用して .edmx ファイルの実行時に Entity Framework によって必要な .msl ファイルを作成するには

MSL で参照されるすべての概念モデル型またはストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。 概念モデル名前空間の名前については、次を参照してください。 [CSDL 仕様](~/ef6/modeling/designer/advanced/edmx/csdl-spec.md)します。 ストレージ モデル名前空間の名前については、次を参照してください。 [SSDL 仕様](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)します。

MSL のバージョンは、XML 名前空間で区別されます。

| MSL のバージョン | XML Namespace                                        |
|:------------|:-----------------------------------------------------|
| MSL v1      | urn: スキーマ-microsoft-com:windows:storage:mapping:CS |
| MSL v2      | http://schemas.microsoft.com/ado/2008/09/mapping/cs  |
| MSL v3      | http://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a>Alias 要素 (MSL)

**エイリアス**マッピング仕様言語 (MSL) の要素は、概念モデルとストレージのモデル名前空間の別名を定義するために使用するマッピング要素の子。 MSL で参照されるすべての概念モデル型またはストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。 概念モデル名前空間の名前については、スキーマ要素 (CSDL) を参照してください。 ストレージ モデル名前空間の名前については、スキーマ要素 (SSDL) を参照してください。

**エイリアス**要素が子要素を含めることはできません。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、**エイリアス**要素。

| 属性名 | 必須 | [値]                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| **Key**        | [はい]         | 指定された名前空間のエイリアス、**値**属性。 |
| **[値]**      | [はい]         | 対象の名前空間の値、**キー**要素は別名です。     |

### <a name="example"></a>例

次の例は、**エイリアス**、エイリアスを定義する要素`c`、概念モデルで定義されている型。

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

## <a name="associationend-element-msl"></a>AssociationEnd 要素 (MSL)

**AssociationEnd**基になるデータベース内のストアド プロシージャにマップされる概念モデルのエンティティ型の変更関数マッピング仕様言語 (MSL) の要素を使用します。 ストアド プロシージャは、関連付けのプロパティに値が保持されているパラメーターの変更の場合、 **AssociationEnd**要素プロパティの値をパラメーターにマップされます。 詳細については、下の例を参照してください。

エンティティ型の変更関数をストアド プロシージャにマップする方法の詳細については、ModificationFunctionMapping 要素 (MSL) とチュートリアルを参照してください。 エンティティをストアド プロシージャにマッピングします。

**AssociationEnd**要素は、次の子要素を持つことができます。

-   ScalarProperty

### <a name="applicable-attributes"></a>適用可能な属性

次の表に、属性に適用される、 **AssociationEnd**要素。

| 属性名     | 必須 | [値]                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **AssociationSet** | [はい]         | マップされるアソシエーションの名前。                                                                                                                                 |
| **From**           | [はい]         | 値、 **FromRole**マップされるアソシエーションに対応するナビゲーション プロパティの属性です。 詳細については、NavigationProperty 要素 (CSDL) を参照してください。 |
| **目的**             | [はい]         | 値、 **ToRole**マップされるアソシエーションに対応するナビゲーション プロパティの属性です。 詳細については、NavigationProperty 要素 (CSDL) を参照してください。   |

### <a name="example"></a>例

次の概念モデルのエンティティ型を考えてみます。

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

さらに、次のストアド プロシージャを考えてみます。

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

Update 関数をマップするために、 `Course` 、このストアド プロシージャにエンティティに値を指定する必要があります、 **DepartmentID**パラメーター。 `DepartmentID` の値はエンティティ型のプロパティには対応せず、独立した関連付け (アソシエーション) に含まれています。ここに、そのマッピングを示します。

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

次のコードは、 **AssociationEnd**要素にマップするために使用、 **DepartmentID**のプロパティ、 **FK\_コース\_部門**関連付け、 **UpdateCourse**ストアド プロシージャ (先の更新関数、**コース**エンティティ型をマップ)。

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

## <a name="associationsetmapping-element-msl"></a>AssociationSetMapping 要素 (MSL)

**AssociationSetMapping**マッピング仕様言語 (MSL) 内の要素は、概念モデルとテーブル列、基になるデータベース内のアソシエーションの間のマッピングを定義します。

概念モデルのアソシエーションとは、プロパティが基になるデータベースの主キー列および外部キー列を表す型のことです。 **AssociationSetMapping**要素では、2 つの EndProperty 要素を使用して、データベースでアソシエーション型プロパティと列の間のマッピングを定義します。 Condition 要素でこれらのマッピングに条件を配置できます。 Insert、update、およびアソシエーションの削除機能を ModificationFunctionMapping 要素を持つデータベースのストアド プロシージャにマップします。 QueryView 要素 Entity SQL 文字列を使用して、アソシエーションとテーブル列の間の読み取り専用のマッピングを定義します。

> [!NOTE]
> 概念モデルのアソシエーションの参照に関する制約が定義されている場合、関連付けする必要はありませんがマップされること、 **AssociationSetMapping**要素。 場合、 **AssociationSetMapping**要素は、参照に関する制約のあるアソシエーションの存在でのマッピングが定義されている、 **AssociationSetMapping**要素は無視されます。 詳細については、ReferentialConstraint 要素 (CSDL) を参照してください。

**AssociationSetMapping**要素は、次の子要素を持つことができます

-   QueryView (0 または 1)
-   EndProperty (0 または 2)
-   条件 (0 個以上)
-   ModificationFunctionMapping (0 または 1)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **AssociationSetMapping**要素。

| 属性名     | 必須 | [値]                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| **Name**           | [はい]         | マップされる概念モデルのアソシエーション セットの名前です。                      |
| **TypeName**       | いいえ          | マッピングされている概念モデルのアソシエーション型の名前空間修飾名です。 |
| **StoreEntitySet** | いいえ          | マップされるテーブルの名前です。                                                 |

### <a name="example"></a>例

次の例は、 **AssociationSetMapping**要素、 **FK\_コース\_部門**概念モデルの関連付けセットは、にマップされます**コース**データベース内のテーブル。 アソシエーション型のプロパティとテーブルの列間のマッピングが子で指定された**EndProperty**要素。

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

## <a name="complexproperty-element-msl"></a>ComplexProperty 要素 (MSL)

A **ComplexProperty**マッピング仕様言語 (MSL) の要素は、基になるデータベースの概念モデルのエンティティ型とテーブル列に複合型プロパティ間のマッピングを定義します。 プロパティ列のマッピングは、ScalarProperty 要素の子で指定されます。

**ComplexType** property 要素は、次の子要素を持つことができます。

-   ScalarProperty (0 個以上)
-   **ComplexProperty** (0 個以上)
-   ComplextTypeMapping (0 個以上)
-   条件 (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に、属性に適用される、 **ComplexProperty**要素。

| 属性名 | 必須 | [値]                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| **Name**       | [はい]         | マップされる概念モデルのエンティティ型の複合プロパティ名。 |
| **TypeName**   | いいえ          | 概念モデルのプロパティ型の名前空間修飾名。                              |

### <a name="example"></a>例

次の例は、School モデルに基づきます。 次の複合型は概念モデルに追加されました。

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

**LastName**と**FirstName**のプロパティ、 **Person**エンティティ型が 1 つの複合プロパティに置き換えられました**名前**:

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

次の MSL 示します、 **ComplexProperty**要素にマップするために使用、**名前**基になるデータベース内の列にプロパティ。

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

## <a name="complextypemapping-element-msl"></a>ComplexTypeMapping 要素 (MSL)

**ComplexTypeMapping**マッピング仕様言語 (MSL) 内の要素が ResultMapping 要素の子であるし、基になる概念モデルの関数インポートとストアド プロシージャ間のマッピングを定義します。次に該当する場合は、データベース:

-   関数インポートが概念の複合型を返す場合
-   ストアド プロシージャが返す列の名前が、複合型のプロパティの名前と完全には一致しない場合

既定では、ストアド プロシージャが返す列と複合型とのマッピングは、列およびプロパティの名前に基づいています。 使用する必要がある列の名前がプロパティの名前一致も一致しない場合、 **ComplexTypeMapping**マッピングを定義する要素。 既定のマッピングの例は、FunctionImportMapping 要素 (MSL) を参照してください。

**ComplexTypeMapping**要素は、次の子要素を持つことができます。

-   ScalarProperty (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に、属性に適用される、 **ComplexTypeMapping**要素。

| 属性名 | 必須 | [値]                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| **TypeName**   | [はい]         | マッピングされている複合型の名前空間修飾名です。 |

### <a name="example"></a>例

次のストアド プロシージャを考えてみます。

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

また、次の概念モデルの複合型を考えてみます。

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

以前の複合型のインスタンスを返す関数インポートを作成するためには、ストアド プロシージャによって返される列間のマッピングとでエンティティ型を定義する必要があります、 **ComplexTypeMapping**要素。

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

## <a name="condition-element-msl"></a>Condition 要素 (MSL)

**条件**マッピング仕様言語 (MSL) 内の要素が条件を概念モデルと基になるデータベース間のマッピングを設定します。 XML ノードに定義されているマッピングは有効の場合は、すべての条件で指定された子**条件**要素が満たされています。 それ以外の場合、マッピングは無効です。 MappingFragment 要素は、1 つ以上含まれている場合の例の**条件**、子要素内のマッピングが定義されている、 **MappingFragment**ノードが有効なは、すべての場合はだけ子の条件**条件**要素が満たされています。

各条件がいずれかに適用できる、**名前**(で指定された概念モデルのエンティティ プロパティの名前、**名前**属性)、または**ColumnName** (内の列の名前指定される、データベース、 **ColumnName**属性)。 ときに、**名前**属性が設定されており、エンティティのプロパティの値に対して条件がチェックされます。 ときに、 **ColumnName**属性が設定されて、列の値に対して条件がチェックされます。 1 つだけ、**名前**または**ColumnName**で属性を指定できます、**条件**要素。

> [!NOTE]
> ときに、**条件**のみ FunctionImportMapping 要素内の要素が使用される、**名前**属性は適用されません。

**条件**要素は、次の要素の子にすることができます。

-   AssociationSetMapping
-   ComplexProperty
-   EntitySetMapping
-   MappingFragment
-   EntityTypeMapping

**条件**要素が子要素がありません。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に、属性に適用される、**条件**要素。

| 属性名 | 必須 | [値]                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ColumnName** | いいえ          | 条件の評価に使用される値のテーブル列の名前。                                                                                                                                                                                                                   |
| **IsNull**     | いいえ          | **True**または**False**します。 値の場合**True**列の値が、 **null**、値の場合、または**False**および列の値がない**null**条件が true. それ以外の場合、条件は False です。 <br/> **IsNull**と**値**と同時に属性を使用することはできません。 |
| **[値]**      | いいえ          | 列値と比較される値。 値が同じ場合、条件は True です。 それ以外の場合、条件は False です。 <br/> **IsNull**と**値**と同時に属性を使用することはできません。                                                                       |
| **Name**       | いいえ          | 条件の評価に使用される値を持つ概念モデルのエンティティ プロパティの名前。 <br/> この属性が適用可能でない場合、**条件**FunctionImportMapping 要素内で要素を使用します。                                                                           |

### <a name="example"></a>例

次の例は**条件**要素の子として**MappingFragment**要素。 ときに**HireDate**が null でないと**EnrollmentDate**が null の場合、データがマップされる間、 **SchoolModel.Instructor**型と**PersonID**と**HireDate**の列、 **Person**テーブル。 ときに**EnrollmentDate**が null でないと**HireDate**が null の場合、データがマップされる間、 **SchoolModel.Student**型と**PersonID****登録**の列、 **Person**テーブル。

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

## <a name="deletefunction-element-msl"></a>DeleteFunction 要素 (MSL)

**DeleteFunction**マッピング仕様言語 (MSL) 内の要素は、エンティティ型または概念モデルのアソシエーションの削除関数を基になるデータベース内のストアド プロシージャにマップします。 変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。 詳細については、関数要素 (SSDL) を参照してください。

> [!NOTE]
> マップしない場合は、3 つすべての挿入、更新、または削除操作をストアド プロシージャにエンティティ型の実行時に実行された場合にマップされていない操作は失敗および UpdateException がスローされます。

### <a name="deletefunction-applied-to-entitytypemapping"></a>EntityTypeMapping への DeleteFunction の適用

EntityTypeMapping 要素に適用すると、 **DeleteFunction**要素は、概念モデルのエンティティ型の削除関数をストアド プロシージャにマップします。

**DeleteFunction**要素は次の子要素に適用される場合があることができます、 **EntityTypeMapping**要素。

-   AssociationEnd (0 個以上)
-   ComplexProperty (0 個以上)
-   ScarlarProperty (0 個以上)

#### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **DeleteFunction**要素に適用されたときに、 **EntityTypeMapping**要素。

| 属性名            | 必須 | [値]                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | [はい]         | 削除関数のマップ先となるストアド プロシージャの名前空間修飾名。 ストアド プロシージャはストレージ モデル内で宣言する必要があります。 |
| **RowsAffectedParameter** | いいえ          | 影響を受ける行の数を返す出力パラメーターの名前。                                                                               |

#### <a name="example"></a>例

次の例は、School モデルに基づいておりを示しています、 **DeleteFunction**要素の削除関数のマッピング、 **Person**エンティティ型を**DeletePerson**ストアド プロシージャです。 **DeletePerson**ストアド プロシージャはストレージ モデルで宣言されています。

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

### <a name="deletefunction-applied-to-associationsetmapping"></a>AssociationSetMapping への DeleteFunction の適用

AssociationSetMapping 要素に適用すると、 **DeleteFunction**要素は、概念モデルのアソシエーションの削除関数をストアド プロシージャにマップします。

**DeleteFunction**要素は次の子要素に適用される場合があることができます、 **AssociationSetMapping**要素。

-   EndProperty

#### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **DeleteFunction**要素に適用されたときに、 **AssociationSetMapping**要素。

| 属性名            | 必須 | [値]                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | [はい]         | 削除関数のマップ先となるストアド プロシージャの名前空間修飾名。 ストアド プロシージャはストレージ モデル内で宣言する必要があります。 |
| **RowsAffectedParameter** | いいえ          | 影響を受ける行の数を返す出力パラメーターの名前。                                                                               |

#### <a name="example"></a>例

次の例は、School モデルに基づいておりを示しています、 **DeleteFunction**要素の削除関数をマップするために使用、 **CourseInstructor**への関連付け、 **DeleteCourseInstructor**ストアド プロシージャ。 **DeleteCourseInstructor**ストアド プロシージャはストレージ モデルで宣言されています。

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

## <a name="endproperty-element-msl"></a>EndProperty 要素 (MSL)

**EndProperty**マッピング仕様言語 (MSL) の要素は、端または概念モデル アソシエーションと基になるデータベースの変更関数の間のマッピングを定義します。 プロパティ列のマッピングは、子の ScalarProperty 要素で指定されます。

ときに、 **EndProperty**要素を使用して概念モデル アソシエーションの端のマッピングを定義、AssociationSetMapping 要素の子です。 ときに、 **EndProperty**変更関数の概念モデル アソシエーションのマッピングを定義する要素が使用される、InsertFunction 要素または DeleteFunction 要素の子です。

**EndProperty**要素は、次の子要素を持つことができます。

-   ScalarProperty (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に、属性に適用される、 **EndProperty**要素。

| 属性名 | 必須 | [値]                                                 |
|:---------------|:------------|:------------------------------------------------------|
| name           | [はい]         | マップされるアソシエーション端の名前。 |

### <a name="example"></a>例

次の例は、 **AssociationSetMapping**要素、 **FK\_コース\_部門**に概念モデルのアソシエーションをマップ**コース**データベース内のテーブル。 アソシエーション型のプロパティとテーブルの列間のマッピングが子で指定された**EndProperty**要素。

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

### <a name="example"></a>例

次の例は、 **EndProperty**アソシエーションの挿入および削除関数のマップ要素 (**CourseInstructor**) を基になるデータベースでストアド プロシージャにします。 ストレージ モデルにマップされて宣言されている関数。

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

## <a name="entitycontainermapping-element-msl"></a>EntityContainerMapping 要素 (MSL)

**EntityContainerMapping**マッピング仕様言語 (MSL) の要素には、ストレージ モデルのエンティティ コンテナーに、概念モデルのエンティティ コンテナーがマップされます。 **EntityContainerMapping**要素は、マッピング要素の子。

**EntityContainerMapping**要素は、(指定した順序で次の子要素を持つことができます。

-   EntitySetMapping (0 個以上)
-   AssociationSetMapping (0 個以上)
-   FunctionImportMapping (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **EntityContainerMapping**要素。

| 属性名            | 必須 | [値]                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StorageModelContainer** | [はい]         | マップされるストレージ モデルのエンティティ コンテナーの名前。                                                                                                                                                                                     |
| **CdmEntityContainer**    | [はい]         | マップされる概念モデルのエンティティ コンテナーの名前。                                                                                                                                                                                  |
| **GenerateUpdateViews**   | いいえ          | **True**または**False**します。 場合**False**、更新プログラム ビューは生成されません。 この属性に設定する必要があります**False**を無効になるデータが正常にラウンドト リップしないため、読み取り専用マッピングがある場合。 <br/> 既定値は**True**します。 |

### <a name="example"></a>例

次の例は、 **EntityContainerMapping**マップされる要素、 **SchoolModelEntities**コンテナー (概念モデルのエンティティ コンテナー) を**SchoolModelStoreContainer**コンテナー (ストレージ モデルのエンティティ コンテナー)。

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

## <a name="entitysetmapping-element-msl"></a>EntitySetMapping 要素 (MSL)

**EntitySetMapping**ストレージ モデルのマッピング仕様言語 (MSL) のマップが概念モデルのエンティティのすべての型がエンティティに設定内の要素を設定します。 概念モデル内のエンティティ セットの論理的なコンテナーは、同じ種類 (と派生型) のエンティティのインスタンス。 ストレージ モデル内のエンティティ セットは、テーブルまたは基になるデータベースのビューを表します。 値によって、概念モデルのエンティティ セットが指定されて、**名前**の属性、 **EntitySetMapping**要素。 マップ先のテーブルまたはビューがで指定された、 **StoreEntitySet**各子 MappingFragment 要素または属性、 **EntitySetMapping**要素自体です。

**EntitySetMapping**要素は、次の子要素を持つことができます。

-   EntityTypeMapping (0 個以上)
-   QueryView (0 または 1)
-   MappingFragment (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **EntitySetMapping**要素。

| 属性名           | 必須 | [値]                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**                 | [はい]         | マッピングされている概念モデルのエンティティ セットの名前。                                                                                                                                                             |
| **TypeName** **1**       | いいえ          | マッピングされている概念モデルのエンティティ型の名前。                                                                                                                                                            |
| **StoreEntitySet** **1** | いいえ          | マップ先のストレージ モデルのエンティティ セットの名前。                                                                                                                                                             |
| **MakeColumnsDistinct**  | いいえ          | **True**または**False**個別の行のみが返されるかどうかによって異なります。 <br/> この属性設定されている場合**True**、 **GenerateUpdateViews**に EntityContainerMapping 要素の属性を設定する必要があります**False**します。 |

 

**1** 、 **TypeName**と**StoreEntitySet**属性は、1 つのテーブルに 1 つのエンティティ型をマップする EntityTypeMapping と MappingFragment 子要素の代わりに使用できます。

### <a name="example"></a>例

次の例は、 **EntitySetMapping**で 3 つの型 (基本データ型と 2 つの派生型) をマップする要素、**コース**で 3 つのテーブルに、概念モデルのエンティティ セット、基になるデータベース。 テーブルがで指定された、 **StoreEntitySet**各属性**MappingFragment**要素。

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

## <a name="entitytypemapping-element-msl"></a>EntityTypeMapping 要素 (MSL)

**EntityTypeMapping**マッピング仕様言語 (MSL) 要素は、基になるデータベース、概念モデルとテーブルのエンティティ型またはビュー間のマッピングを定義します。 概念モデルのエンティティ型と基になるデータベース テーブルまたはビューについては、EntityType 要素 (CSDL) と EntitySet 要素 (SSDL) を参照してください。 マップされる概念モデルのエンティティ型がで指定された、 **TypeName**の属性、 **EntityTypeMapping**要素。 テーブルまたはビューがマップされているがで指定された、 **StoreEntitySet**子 MappingFragment 要素の属性。

ModificationFunctionMapping の子要素を使用して、挿入をマップできますでは、更新、または、データベース内のストアド プロシージャにエンティティ型の関数を削除します。

**EntityTypeMapping**要素は、次の子要素を持つことができます。

-   MappingFragment (0 個以上)
-   ModificationFunctionMapping (0 または 1)
-   ScalarProperty
-   条件

> [!NOTE]
> **MappingFragment**と**ModificationFunctionMapping**要素の子要素をすることはできません、 **EntityTypeMapping**と同時にある要素。


> [!NOTE]
> **ScalarProperty**と**条件**の子要素が要素にできる、 **EntityTypeMapping** FunctionImportMapping 要素内で使用した場合の要素。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **EntityTypeMapping**要素。

| 属性名 | 必須 | [値]                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **TypeName**   | [はい]         | マッピングされている概念モデルのエンティティ型の名前空間修飾名です。 <br/> 型が抽象型または派生型である場合、値は `IsOfType(Namespace-qualified_type_name)` になる必要があります。 |

### <a name="example"></a>例

次の例では、2 つの子を持つ EntitySetMapping 要素**EntityTypeMapping**要素。 最初の**EntityTypeMapping**要素、 **SchoolModel.Person**エンティティ型にマッピングされますが、 **Person**テーブル。 2 番目の**EntityTypeMapping**要素では、更新機能は、 **SchoolModel.Person**型は、ストアド プロシージャにマッピング**UpdatePerson**データベースで.

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

### <a name="example"></a>例

次の例は、ルート型が抽象である型階層のマッピングを示しています。 使用に注意してください、`IsOfType`の構文、 **TypeName**属性。

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

## <a name="functionimportmapping-element-msl"></a>FunctionImportMapping 要素 (MSL)

**FunctionImportMapping**マッピング仕様言語 (MSL) 要素は、基になるデータベース、概念モデルとストアド プロシージャでの関数インポートまたは関数の間のマッピングを定義します。 関数インポートは概念モデル内で、ストアド プロシージャはストレージ モデル内で宣言する必要があります。 詳細については、FunctionImport 要素 (CSDL) と関数要素 (SSDL) を参照してください。

> [!NOTE]
> 既定では、関数インポートが概念モデル エンティティ型または複合型を返す場合、基になるストアド プロシージャが返す列の名前が概念モデル型のプロパティの名前と正確に一致する必要があります。 列名は一致しない場合、プロパティ名、マッピングが ResultMapping 要素で定義する必要があります。

**FunctionImportMapping**要素は、次の子要素を持つことができます。

-   ResultMapping (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に、属性に適用される、 **FunctionImportMapping**要素。

| 属性名         | 必須 | [値]                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| **FunctionImportName** | [はい]         | マップされる概念モデルの関数インポートの名前。           |
| **FunctionName**       | [はい]         | マップされるストレージ モデルの関数の名前空間修飾名。 |

### <a name="example"></a>例

次の例は、School モデルに基づきます。 ストレージ モデルの次の関数について考えます。

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

また、概念モデルのこの関数インポートについて考えます。

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

次の例に示す、 **FunctionImportMapping**関数をマップし、関数の上に互いのインポートに使用する要素。

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a>InsertFunction 要素 (MSL)

**InsertFunction**マッピング仕様言語 (MSL) 内の要素は、エンティティ型または概念モデルのアソシエーションの挿入関数を基になるデータベース内のストアド プロシージャにマップします。 変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。 詳細については、関数要素 (SSDL) を参照してください。

> [!NOTE]
> マップしない場合は、3 つすべての挿入、更新、または削除操作をストアド プロシージャにエンティティ型の実行時に実行された場合にマップされていない操作は失敗および UpdateException がスローされます。

**InsertFunction**要素 ModificationFunctionMapping 要素の子にできるし、EntityTypeMapping 要素または AssociationSetMapping 要素に適用します。

### <a name="insertfunction-applied-to-entitytypemapping"></a>EntityTypeMapping への InsertFunction の適用

EntityTypeMapping 要素に適用すると、 **InsertFunction**要素は、概念モデルのエンティティ型の挿入関数をストアド プロシージャにマップします。

**InsertFunction**要素は次の子要素に適用される場合があることができます、 **EntityTypeMapping**要素。

-   AssociationEnd (0 個以上)
-   ComplexProperty (0 個以上)
-   ResultBinding (0 または 1)
-   ScarlarProperty (0 個以上)

#### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **InsertFunction**要素に適用すると、 **EntityTypeMapping**要素。

| 属性名            | 必須 | [値]                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | [はい]         | 挿入関数のマップ先であるストアド プロシージャの名前空間修飾名。 ストアド プロシージャはストレージ モデル内で宣言する必要があります。 |
| **RowsAffectedParameter** | いいえ          | 影響を受ける行の数を返す出力パラメーターの名前。                                                                               |

#### <a name="example"></a>例

次の例は、School モデルに基づいておりを示しています、 **InsertFunction** Person エンティティ型の挿入関数をマップするための要素、 **InsertPerson**ストアド プロシージャ。 **InsertPerson**ストアド プロシージャはストレージ モデルで宣言されています。

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
### <a name="insertfunction-applied-to-associationsetmapping"></a>AssociationSetMapping への InsertFunction の適用

AssociationSetMapping 要素に適用すると、 **InsertFunction**要素は、概念モデルのアソシエーションの挿入関数をストアド プロシージャにマップします。

**InsertFunction**要素は次の子要素に適用される場合があることができます、 **AssociationSetMapping**要素。

-   EndProperty

#### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **InsertFunction**要素に適用されたときに、 **AssociationSetMapping**要素。

| 属性名            | 必須 | [値]                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | [はい]         | 挿入関数のマップ先であるストアド プロシージャの名前空間修飾名。 ストアド プロシージャはストレージ モデル内で宣言する必要があります。 |
| **RowsAffectedParameter** | いいえ          | 影響を受ける行の数を返す出力パラメーターの名前。                                                                               |

#### <a name="example"></a>例

次の例は、School モデルに基づいておりを示しています、 **InsertFunction**要素の挿入関数をマップするために使用、 **CourseInstructor**への関連付け、 **InsertCourseInstructor**ストアド プロシージャ。 **InsertCourseInstructor**ストアド プロシージャはストレージ モデルで宣言されています。

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

## <a name="mapping-element-msl"></a>Mapping 要素 (MSL)

**マッピング**マッピング仕様言語 (MSL) 内の要素には (ストレージ モデルで説明した) ように、概念モデルをデータベースで定義されているオブジェクトのマッピングの情報が含まれています。 詳細については、CSDL の仕様と SSDL 仕様を参照してください。

**マッピング**要素はマッピング スキーマのルート要素です。 マッピング仕様の XML 名前空間は http://schemas.microsoft.com/ado/2009/11/mapping/cs します。

マッピング要素には、次の子要素をここに示す順序で含めることができます。

-   エイリアス (0 個以上)
-   EntityContainerMapping (正確には 1 つ)

MSL で参照される概念モデル型およびストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。 概念モデル名前空間の名前については、スキーマ要素 (CSDL) を参照してください。 ストレージ モデル名前空間の名前については、スキーマ要素 (SSDL) を参照してください。 Alias 要素では、MSL で使用される名前空間の別名を定義できます。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、**マッピング**要素。

| 属性名 | 必須 | [値]                                                 |
|:---------------|:------------|:------------------------------------------------------|
| **スペース**      | [はい]         | **C-S**します。 これは固定値で、変更できません。 |

### <a name="example"></a>例

次の例は、**マッピング**School モデルの一部に基づいている要素。 School モデルの詳細については、クイック スタート (Entity Framework) を参照してください。

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

## <a name="mappingfragment-element-msl"></a>MappingFragment 要素 (MSL)

**MappingFragment**マッピング仕様言語 (MSL) 内の要素が概念モデルのエンティティ型とテーブルまたはデータベース内のビューのプロパティの間のマッピングを定義します。 概念モデルのエンティティ型と基になるデータベース テーブルまたはビューについては、EntityType 要素 (CSDL) と EntitySet 要素 (SSDL) を参照してください。 **MappingFragment** EntityTypeMapping 要素または EntitySetMapping 要素の子要素であることができます。

**MappingFragment**要素は、次の子要素を持つことができます。

-   ComplexType (0 個以上)
-   ScalarProperty (0 個以上)
-   条件 (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **MappingFragment**要素。

| 属性名          | 必須 | [値]                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StoreEntitySet**      | [はい]         | マップされるテーブルまたはビューの名前。                                                                                                                                                                           |
| **MakeColumnsDistinct** | いいえ          | **True**または**False**個別の行のみが返されるかどうかによって異なります。 <br/> この属性設定されている場合**True**、 **GenerateUpdateViews**に EntityContainerMapping 要素の属性を設定する必要があります**False**します。 |

### <a name="example"></a>例

次の例は、 **MappingFragment**要素の子として、 **EntityTypeMapping**要素。 この例では、プロパティでは、**コース**概念モデル型の列にマップ、**コース**データベース内のテーブル。

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

### <a name="example"></a>例

次の例は、 **MappingFragment**要素の子として、 **EntitySetMapping**要素。 上記のプロパティの例のように、**コース**概念モデル型の列にマップ、**コース**データベース内のテーブル。

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

## <a name="modificationfunctionmapping-element-msl"></a>ModificationFunctionMapping 要素 (MSL)

**ModificationFunctionMapping**マッピング仕様言語 (MSL) 内の要素のマップ、挿入、更新、および基になるデータベース内のストアド プロシージャを概念モデルのエンティティ型の関数を削除します。 **ModificationFunctionMapping**要素が挿入のマップも、および基になるデータベース内のストアド プロシージャを概念モデルでの多対多のアソシエーションの関数を削除できます。 変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。 詳細については、関数要素 (SSDL) を参照してください。

> [!NOTE]
> マップしない場合は、3 つすべての挿入、更新、または削除操作をストアド プロシージャにエンティティ型の実行時に実行された場合にマップされていない操作は失敗および UpdateException がスローされます。


> [!NOTE]
> 継承階層の 1 つのエンティティの変更関数をストアド プロシージャにマップした場合、階層内のすべての型の変更関数がストアド プロシージャにマップされる必要があります。

**ModificationFunctionMapping**要素が EntityTypeMapping 要素または AssociationSetMapping 要素の子にすることができます。

**ModificationFunctionMapping**要素は、次の子要素を持つことができます。

-   DeleteFunction (0 または 1)
-   InsertFunction (0 または 1)
-   UpdateFunction (0 または 1)

適用可能な属性がない、 **ModificationFunctionMapping**要素。

### <a name="example"></a>例

次の例は、エンティティ セット マッピングを**人**School モデルのエンティティ セットします。 列マッピングに加えて、 **Person**エンティティ型、マッピングの挿入、更新、および削除関数の**Person**型が表示されます。 ストレージ モデルにマップされて宣言されている関数。

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

### <a name="example"></a>例

次の例は、アソシエーション セット マッピングを**CourseInstructor** School モデルの関連付けセットします。 列マッピングに加えて、 **CourseInstructor**アソシエーションの挿入および削除関数のマッピング、 **CourseInstructor**の関連付けが表示されます。 ストレージ モデルにマップされて宣言されている関数。

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
 

 

## <a name="queryview-element-msl"></a>QueryView 要素 (MSL)

**QueryView**マッピング仕様言語 (MSL) 内の要素は、概念モデルと基になるデータベース テーブルにエンティティ型またはアソシエーション間の読み取り専用マッピングを定義します。 マッピングは、ストレージ モデルに対して評価される Entity SQL クエリを使用して定義され、結果セットのエンティティまたはアソシエーションが概念モデルの観点からの express。 クエリ ビューは読み取り専用であるため、標準の更新コマンドを使用してクエリ ビューで定義された型を更新することはできません。 型の更新を行うには、変更関数を使用する必要があります。 詳細については、次を参照してください。 方法: ストアド プロシージャを変更関数をマップします。

> [!NOTE]
> **QueryView**要素では、Entity SQL 式が含まれている**GroupBy**、グループ集計、またはナビゲーション プロパティはサポートされていません。

 

**QueryView**要素 EntitySetMapping 要素または AssociationSetMapping 要素の子にすることができます。 前者の場合、クエリ ビューは、概念モデルのエンティティの読み取り専用マッピングを定義します。 後者の場合、クエリ ビューは、概念モデルのアソシエーションの読み取り専用マッピングを定義します。

> [!NOTE]
> 場合、 **AssociationSetMapping**要素は、参照に関する制約のあるアソシエーションには、 **AssociationSetMapping**要素は無視されます。 詳細については、ReferentialConstraint 要素 (CSDL) を参照してください。

**QueryView**要素は、すべての子要素を含めることはできません。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **QueryView**要素。

| 属性名 | 必須 | [値]                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **TypeName**   | いいえ          | クエリ ビューによってマップされる概念モデル型の名前。 |

### <a name="example"></a>例

次の例は、 **QueryView**要素の子として、 **EntitySetMapping**要素のクエリ ビュー マッピングを定義し、**部門**内のエンティティ型、School モデル。

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

クエリでは、メンバーのサブセットのみを返すため、**部門**、ストレージ モデル内の型、**部門**次のように、このマッピングに基づいて、School モデルの種類が変更されました。

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

### <a name="example"></a>例

例を次に示します、 **QueryView**要素の子として、 **AssociationSetMapping**要素の読み取り専用マッピングを定義し、 `FK_Course_Department` School モデルのアソシエーション。

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
 
### <a name="comments"></a>コメント

クエリ ビューを定義して、次のシナリオを実現できます。

-   ストレージ モデルのエンティティのプロパティの一部を含まない、概念モデルのエンティティを定義する。 既定値がないと、サポートしないプロパティが含まれます**null**値。
-   ストレージ モデルの計算列を概念モデルのエンティティ型のプロパティにマップする。
-   概念モデルのエンティティをパーティション分割するために使用される条件が等価性に基づかないマッピングを定義する。 使用して、条件付きのマッピングを指定すると、**条件**要素を指定された条件が指定された値と等しく必要があります。 詳細については、条件の要素 (MSL) を参照してください。
-   ストレージ モデルの同一の列を概念モデルの複数の型にマップする。
-   複数の型を同じテーブルにマップする。
-   リレーショナル スキーマの外部キーに基づかない概念モデルのアソシエーションを定義する。
-   カスタム ビジネス ロジックを使用して、概念モデルのプロパティの値を設定する。 文字列値"T"の値にデータ ソースをマップするなど、 **true**、概念モデルでのブール値。
-   クエリ結果に対する条件付きフィルターを定義する。
-   概念モデルのデータに適用する制限をストレージ モデルよりも少なくする。 たとえば、行うことができます、プロパティ、概念モデルで null 許容がマップされている列がサポートしていない場合でも**null**値。

エンティティのクエリ ビューを定義する際は、次の点に注意してください。

-   クエリ ビューは読み取り専用です。 エンティティの更新を行うには、変更関数を使用する必要があります。
-   クエリ ビューでエンティティ型を定義する場合、すべての関連エンティティもクエリ ビューで定義する必要があります。
-   定義する必要があります、リレーショナル スキーマでリンク テーブルを表すストレージ モデルのエンティティに多対多のアソシエーションをマップするときに、 **QueryView**内の要素、 **AssociationSetMapping**このリンク テーブルの要素。
-   クエリ ビューは、型階層のすべての型に対して定義する必要があります。 これは、次の方法で行うことができます。
-   -   1 つ**QueryView**を階層内のすべてのエンティティ型の和集合を返す 1 つの Entity SQL クエリを指定する要素。
    -   1 つ**QueryView**特定の条件に基づいて、階層内の特定のエンティティ型を取得するケースの演算子を使用する 1 つの Entity SQL クエリを指定する要素。
    -   追加された**QueryView**階層内の特定の型の要素。 ここでは、使用して、 **TypeName**の属性、 **QueryView**それぞれのビューのエンティティの種類を指定する要素。
-   クエリ ビューが定義されている場合は指定できません、 **StorageSetName**属性を**EntitySetMapping**要素。
-   クエリ ビューが定義されている場合、 **EntitySetMapping**要素に含めることはできませんも**プロパティ**マッピングします。

## <a name="resultbinding-element-msl"></a>ResultBinding 要素 (MSL)

**ResultBinding**マッピング仕様言語 (MSL) 要素は、列の値によって返されるストアド プロシージャを概念モデルのエンティティのプロパティのエンティティ型の変更関数がマップされるときにストアドをマップします。基になるデータベースでの手順です。 ストアド プロシージャの id 列の値が insert ステートメントで返される場合など、 **ResultBinding**要素には、概念モデルのエンティティ型プロパティに返される値がマップされます。

**ResultBinding**要素は InsertFunction 要素または UpdateFunction 要素の子であることができます。

**ResultBinding**要素は、すべての子要素を含めることはできません。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に、属性に適用される、 **ResultBinding**要素。

| 属性名 | 必須 | [値]                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **Name**       | [はい]         | マップされる概念モデルのエンティティ プロパティの名前。 |
| **ColumnName** | [はい]         | マップされる列の名前。                                          |

### <a name="example"></a>例

次の例は、School モデルに基づいておりを示しています、 **InsertFunction**要素の挿入関数をマップするために使用、 **Person**エンティティ型を**InsertPerson**ストアド プロシージャです。 (、 **InsertPerson**ストアド プロシージャを次に示し、ストレージ モデルで宣言されています)。A **ResultBinding**要素を使用して、ストアド プロシージャによって返される列の値をマップ (**NewPersonID**) エンティティ型プロパティ (**PersonID**)。

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

次の TRANSACT-SQL について説明します、 **InsertPerson**ストアド プロシージャ。

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

## <a name="resultmapping-element-msl"></a>ResultMapping 要素 (MSL)

**ResultMapping**マッピング仕様言語 (MSL) 要素は、次に該当する場合、基になるデータベースで概念モデルの関数インポートとストアド プロシージャ間のマッピングを定義します。

-   関数インポートが概念モデルのエンティティ型または複合型を返す場合
-   ストアド プロシージャが返す列の名前が、エンティティ型または複合型のプロパティの名前と完全には一致しない場合

既定では、ストアド プロシージャが返す列とエンティティ型または複合型とのマッピングは、列およびプロパティの名前に基づいています。 使用する必要がある列の名前がプロパティの名前一致も一致しない場合、 **ResultMapping**マッピングを定義する要素。 既定のマッピングの例は、FunctionImportMapping 要素 (MSL) を参照してください。

**ResultMapping**要素は FunctionImportMapping 要素の子要素です。

**ResultMapping**要素は、次の子要素を持つことができます。

-   EntityTypeMapping (0 個以上)
-   ComplexTypeMapping

適用可能な属性がない、 **ResultMapping**要素。

### <a name="example"></a>例

次のストアド プロシージャを考えてみます。

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

また、次の概念モデルのエンティティ型を考えてみます。

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

以前のエンティティ型のインスタンスを返す関数インポートを作成するためには、ストアド プロシージャによって返される列間のマッピングとでエンティティ型を定義する必要があります、 **ResultMapping**要素。

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

## <a name="scalarproperty-element-msl"></a>ScalarProperty 要素 (MSL)

**ScalarProperty**マッピング仕様言語 (MSL) 要素は、概念モデルのエンティティ型、複合型、またはアソシエーションのプロパティをテーブルの列または基になるデータベースでストアド プロシージャのパラメーターにマップします。

> [!NOTE]
> 変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。 詳細については、関数要素 (SSDL) を参照してください。

**ScalarProperty**要素は、次の要素の子にすることができます。

-   MappingFragment
-   InsertFunction
-   UpdateFunction
-   DeleteFunction
-   EndProperty
-   ComplexProperty
-   ResultMapping

子として、 **MappingFragment**、 **ComplexProperty**、または**EndProperty**要素、 **ScalarProperty**要素のプロパティをマップします。データベース内の列を概念モデル。 子として、 **InsertFunction**、 **UpdateFunction**、または**DeleteFunction**要素、 **ScalarProperty**要素のプロパティをマップします。ストアド プロシージャのパラメーターに、概念モデル。

**ScalarProperty**要素は、すべての子要素を含めることはできません。

### <a name="applicable-attributes"></a>適用可能な属性

適用される属性、 **ScalarProperty**要素が要素の役割によって異なります。

次の表に、適用されるときに、属性、 **ScalarProperty**要素を使用して概念モデルのプロパティをデータベース内の列にマップします。

| 属性名 | 必須 | [値]                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **Name**       | [はい]         | マップされる概念モデルのプロパティの名前。 |
| **ColumnName** | [はい]         | マップされるテーブル列の名前。              |

次の表に、属性に適用される、 **ScalarProperty**概念モデルのプロパティをストアド プロシージャのパラメーターにマップする際の要素。

| 属性名    | 必須 | [値]                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**          | [はい]         | マップされる概念モデルのプロパティの名前。                                                                                 |
| **ParameterName** | [はい]         | マップされるパラメーターの名前。                                                                                                 |
| **Version**       | いいえ          | **現在**または**元**現在の値またはプロパティの元の値を同時実行制御チェックを使用する必要があるかどうかによって異なります。 |

### <a name="example"></a>例

次の例は、 **ScalarProperty** 2 つの方法で使用される要素。

-   プロパティをマップする、 **Person**エンティティ型の列を**Person**テーブル。
-   プロパティをマップする、 **Person**エンティティ型のパラメーターを**UpdatePerson**ストアド プロシージャ。 ストアド プロシージャはストレージ モデル内で宣言されます。

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

### <a name="example"></a>例

例を次に示します、 **ScalarProperty**要素の挿入をマップし、データベース内のストアド プロシージャを概念モデル アソシエーションの関数を削除するために使用します。 ストアド プロシージャはストレージ モデル内で宣言されます。

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

## <a name="updatefunction-element-msl"></a>UpdateFunction 要素 (MSL)

**UpdateFunction**マッピング仕様言語 (MSL) 内の要素は、概念モデルのエンティティ型の更新関数を基になるデータベース内のストアド プロシージャにマップします。 変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。 詳細については、関数要素 (SSDL) を参照してください。

> [!NOTE]
>  マップしない場合は、3 つすべての挿入、更新、または削除操作をストアド プロシージャにエンティティ型の実行時に実行された場合にマップされていない操作は失敗および UpdateException がスローされます。

**UpdateFunction**要素 ModificationFunctionMapping 要素の子にできるし、EntityTypeMapping 要素に適用します。

**UpdateFunction**要素は、次の子要素を持つことができます。

-   AssociationEnd (0 個以上)
-   ComplexProperty (0 個以上)
-   ResultBinding (0 または 1)
-   ScarlarProperty (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **UpdateFunction**要素。

| 属性名            | 必須 | [値]                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | [はい]         | 更新関数のマップ先となるストアド プロシージャの名前空間修飾名。 ストアド プロシージャはストレージ モデル内で宣言する必要があります。 |
| **RowsAffectedParameter** | いいえ          | 影響を受ける行の数を返す出力パラメーターの名前。                                                                               |

### <a name="example"></a>例

次の例は、School モデルに基づいておりを示しています、 **UpdateFunction**の update 関数をマップするための要素、 **Person**エンティティ型を**UpdatePerson**ストアド プロシージャです。 **UpdatePerson**ストアド プロシージャはストレージ モデルで宣言されています。

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
