---
title: MSL 仕様-EF6
description: Entity Framework 6 での MSL 仕様
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/msl-spec
ms.openlocfilehash: 36527b9827a7576f73c9ef476462d9392b601984
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070113"
---
# <a name="msl-specification"></a>MSL 仕様
マッピング仕様言語 (MSL) は、Entity Framework アプリケーションの概念モデルとストレージモデルの間のマッピングを記述する XML ベースの言語です。

Entity Framework アプリケーションでは、マッピングメタデータはビルド時に (MSL で記述された) .msl ファイルから読み込まれます。 Entity Framework は、実行時にマッピングメタデータを使用して、概念モデルに対するクエリをストア固有のコマンドに変換します。

Entity Framework Designer (EF デザイナー) は、デザイン時に、.edmx ファイルにマッピング情報を格納します。 ビルド時に、Entity Designer は .edmx ファイルの情報を使用して、実行時に Entity Framework が必要とする .msl ファイルを作成します。

MSL で参照されるすべての概念モデル型またはストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。 概念モデルの名前空間の名前については、「 [CSDL の仕様](xref:ef6/modeling/designer/advanced/edmx/csdl-spec)」を参照してください。 ストレージモデルの名前空間の名前については、「 [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec)」を参照してください。

MSL のバージョンは、XML 名前空間で区別されます。

| MSL バージョン | XML 名前空間                                        |
|:------------|:-----------------------------------------------------|
| MSL v1      | urn: スキーマ-microsoft-com: windows: storage: mapping: CS |
| MSL v2      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| MSL v3      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a>Alias 要素 (MSL)

マッピングスキーマ言語 (MSL) の **Alias** 要素は、概念モデルおよびストレージモデルの名前空間のエイリアスを定義するために使用される mapping 要素の子です。 MSL で参照されるすべての概念モデル型またはストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。 概念モデルの名前空間の名前については、「Schema 要素 (CSDL)」を参照してください。 ストレージモデルの名前空間の名前については、「Schema 要素 (SSDL)」を参照してください。

**Alias**要素に子要素を含めることはできません。

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Alias** 要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| **キー**        | はい         | **Value**属性によって指定された名前空間のエイリアス。 |
| **Value**      | はい         | **キー**要素の値がエイリアスである名前空間。     |

### <a name="example"></a>例

次の例は、概念モデルで定義されている型のエイリアスを定義する **alias** 要素を示して `c` います。

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

## <a name="associationend-element-msl"></a>AssociationEnd 要素 (MSL)

マッピングスキーマ言語 (MSL) の **Associationend** 要素は、概念モデルのエンティティ型の変更関数が基になるデータベースのストアドプロシージャにマップされるときに使用されます。 変更ストアドプロシージャが、その値が関連付けプロパティに保持されているパラメーターを受け取る場合、 **Associationend** 要素はプロパティ値をパラメーターにマップします。 詳細については、下の例を参照してください。

エンティティ型の変更関数をストアドプロシージャにマップする方法の詳細については、「ModificationFunctionMapping 要素 (MSL)」および「チュートリアル: ストアドプロシージャへのエンティティのマッピング」を参照してください。

**Associationend**要素には、次の子要素を含めることができます。

-   ScalarProperty

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Associationend** 要素に適用できる属性について説明します。

| 属性名     | 必須 | 値                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **AssociationSet** | はい         | マップされるアソシエーションの名前。                                                                                                                                 |
| **From**           | はい         | マップされるアソシエーションに対応するナビゲーションプロパティの **Fromrole** 属性の値。 詳細については、「NavigationProperty 要素 (CSDL)」を参照してください。 |
| **To**             | はい         | マップされるアソシエーションに対応するナビゲーションプロパティの **Torole** 属性の値。 詳細については、「NavigationProperty 要素 (CSDL)」を参照してください。   |

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

エンティティの更新関数をこのストアドプロシージャにマップするには、 `Course` **DepartmentID** パラメーターに値を指定する必要があります。 `DepartmentID` の値はエンティティ型のプロパティには対応せず、独立した関連付け (アソシエーション) に含まれています。ここに、そのマッピングを示します。

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

次のコードは、 **FK \_ course \_ Department**アソシエーションの**DepartmentID**プロパティを**updatecourse**ストアドプロシージャにマップするために使用される**associationend**要素を示しています (これは、 **course**エンティティ型の更新関数がマップされています)。

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

マッピング仕様言語 (MSL) の **AssociationSetMapping** 要素は、概念モデルのアソシエーションと基になるデータベースのテーブル列の間のマッピングを定義します。

概念モデルのアソシエーションとは、プロパティが基になるデータベースの主キー列および外部キー列を表す型のことです。 **AssociationSetMapping**要素は、2つの endproperty 要素を使用して、データベース内のアソシエーション型のプロパティと列の間のマッピングを定義します。 Condition 要素を使用して、これらのマッピングに条件を指定できます。 ModificationFunctionMapping 要素を使用して、アソシエーションの挿入関数、更新関数、および削除関数をデータベース内のストアド プロシージャにマップします。 QueryView 要素内の Entity SQL 文字列を使用して、アソシエーションとテーブル列の間の読み取り専用マッピングを定義します。

> [!NOTE]
> 概念モデルのアソシエーションに対して参照制約が定義されている場合、アソシエーションを **AssociationSetMapping** 要素にマップする必要はありません。 参照に関する制約を持つアソシエーションの **AssociationSetMapping** 要素が存在する場合、 **AssociationSetMapping** 要素で定義されているマッピングは無視されます。 詳細については、「参照要素の参照」を参照してください。

**AssociationSetMapping**要素には、次の子要素を含めることができます。

-   QueryView (0 個または 1 個)
-   EndProperty (0 個または 2 個)
-   Condition (0 個以上)
-   ModificationFunctionMapping (0 個または 1 個)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **AssociationSetMapping** 要素に適用できる属性について説明します。

| 属性名     | 必須 | 値                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| **名前**           | はい         | マップされる概念モデルのアソシエーション セットの名前です。                      |
| **TypeName**       | いいえ          | マッピングされている概念モデルのアソシエーション型の名前空間修飾名です。 |
| **StoreEntitySet** | いいえ          | マップされるテーブルの名前です。                                                 |

### <a name="example"></a>例

次の例では、概念モデルの**FK \_ course \_ Department**アソシエーションセットがデータベースの**Course**テーブルにマップされている**AssociationSetMapping**要素を示します。 アソシエーション型のプロパティとテーブル列の間のマッピングは、子 **Endproperty** 要素で指定されます。

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

マッピング仕様言語 (MSL) の **Complexproperty** 要素は、概念モデルのエンティティ型の複合型プロパティと基になるデータベースのテーブル列との間のマッピングを定義します。 プロパティ列のマッピングは、子 ScalarProperty 要素で指定されます。

**ComplexType**プロパティ要素には、次の子要素を含めることができます。

-   ScalarProperty (0 個以上)
-   **Complexproperty** (0 個以上)
-   ComplextTypeMapping (0 個以上)
-   Condition (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Complexproperty** 要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| **名前**       | はい         | マップされる概念モデルのエンティティ型の複合プロパティ名。 |
| **TypeName**   | いいえ          | 概念モデルのプロパティ型の名前空間修飾名。                              |

### <a name="example"></a>例

次の例は、School モデル に基づいています。 次の複合型は概念モデルに追加されました。

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

**Person**エンティティ型の**LastName**プロパティと**FirstName**プロパティは、1つの複合プロパティ**Name**に置き換えられました。

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

次の MSL は、 **Name**プロパティを基になるデータベースの列にマップするために使用される**complexproperty**要素を示しています。

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

マッピング仕様言語 (MSL) の **Complextypemapping** 要素は、resultmapping 要素の子であり、次の条件に該当する場合に、概念モデルの関数インポートと基になるデータベースのストアドプロシージャの間のマッピングを定義します。

-   関数インポートが概念の複合型を返す場合
-   ストアド プロシージャが返す列の名前が、複合型のプロパティの名前と完全には一致しない場合

既定では、ストアド プロシージャが返す列と複合型とのマッピングは、列およびプロパティの名前に基づいています。 列名がプロパティ名と完全に一致しない場合は、 **Complextypemapping** 要素を使用してマッピングを定義する必要があります。 既定のマッピングの例については、「FunctionImportMapping 要素 (MSL)」を参照してください。

**Complextypemapping**要素には、次の子要素を含めることができます。

-   ScalarProperty (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Complextypemapping** 要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| **TypeName**   | はい         | マッピングされている複合型の名前空間修飾名です。 |

### <a name="example"></a>例

次のストアド プロシージャについて考えてみます。

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

前の複合型のインスタンスを返す関数インポートを作成するには、ストアドプロシージャによって返される列とエンティティ型の間のマッピングを **Complextypemapping** 要素で定義する必要があります。

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

マッピングスキーマ言語 (MSL) の **Condition** 要素は、概念モデルと基になるデータベースとの間のマッピングに関する条件を配置します。 XML ノード内で定義されているマッピングは、子 **条件** 要素で指定されているすべての条件が満たされている場合に有効です。 それ以外の場合、マッピングは無効です。 たとえば、MappingFragment 要素に1つ以上の**条件**子要素が含まれている場合、その子**条件**要素のすべての条件が満たされた場合にのみ、 **mappingfragment**ノード内で定義されたマッピングが有効になります。

各条件は、 **名前** (概念モデルエンティティプロパティ **の名前)** 、または **columnname** ( **columnname** 属性で指定されたデータベース内の列の名前) のいずれかに適用できます。 **Name**属性を設定すると、エンティティのプロパティ値に対して条件がチェックされます。 **ColumnName**属性を設定すると、列の値に対して条件がチェックされます。 **Condition**要素では、 **Name**または**ColumnName**属性のいずれか1つだけを指定できます。

> [!NOTE]
> FunctionImportMapping 要素内で **Condition** 要素が使用されている場合、 **Name** 属性だけが適用されません。

**Condition**要素は、次の要素の子にすることができます。

-   AssociationSetMapping
-   ComplexProperty
-   EntitySetMapping
-   MappingFragment
-   EntityTypeMapping

**Condition**要素に子要素を含めることはできません。

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Condition** 要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **[ColumnName]** | いいえ          | 条件の評価に使用される値のテーブル列の名前。                                                                                                                                                                                                                   |
| **IsNull**     | いいえ          | **True** または **False** です。 値が **true** で列の値が **null**である場合、または値が **False** で列の値が **null**でない場合、条件は true になります。 それ以外の場合、条件は False です。 <br/> **IsNull**属性と**Value**属性は同時に使用できません。 |
| **Value**      | いいえ          | 列値と比較される値。 値が同じ場合、条件は True です。 それ以外の場合、条件は False です。 <br/> **IsNull**属性と**Value**属性は同時に使用できません。                                                                       |
| **名前**       | いいえ          | 条件の評価に使用される値を持つ概念モデルのエンティティ プロパティの名前。 <br/> **Condition**要素が FunctionImportMapping 要素内で使用されている場合、この属性は適用されません。                                                                           |

### <a name="example"></a>例

次の例では、 **Mappingfragment**要素の子として**Condition**要素を示しています。 **HireDate**が null でなく、 **EnrollmentDate**が Null の場合、 **SchoolModel**型と**Person**テーブルの**PersonID**および**HireDate**列の間でデータがマップされます。 **EnrollmentDate**が null でなく、 **HireDate**が Null の場合、 **Person**テーブルの**SchoolModel**型と**PersonID**列および**Enrollment**列の間でデータがマップされます。

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

マッピングスキーマ言語 (MSL) の **deletefunction** 要素は、概念モデルのエンティティ型またはアソシエーションの delete 関数を、基になるデータベースのストアドプロシージャにマップします。 変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。 詳しくは、「Function 要素 (SSDL)」をご覧ください。

> [!NOTE]
> エンティティ型の3つの挿入、更新、または削除操作をすべてストアドプロシージャにマップしない場合、実行時にマップされていない操作は失敗し、UpdateException がスローされます。

### <a name="deletefunction-applied-to-entitytypemapping"></a>EntityTypeMapping への DeleteFunction の適用

EntityTypeMapping 要素に適用される場合、 **Deletefunction** 要素は、概念モデルのエンティティ型の削除関数をストアドプロシージャにマップします。

**Deletefunction**要素には、 **entitytypemapping**要素に適用するときに次の子要素を含めることができます。

-   AssociationEnd (0 個以上)
-   ComplexProperty (0 個以上)
-   ScarlarProperty (0 個以上)

#### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Entitytypemapping**要素に適用されるときに**deletefunction**要素に適用できる属性について説明します。

| 属性名            | 必須 | 値                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | はい         | 削除関数のマップ先となるストアド プロシージャの名前空間修飾名。 ストアド プロシージャはストレージ モデル内で宣言する必要があります。 |
| **RowsAffectedParameter** | いいえ          | 影響を受ける行の数を返す出力パラメーターの名前。                                                                               |

#### <a name="example"></a>例

次の例は、School モデルに基づいています。また、 **Person**エンティティ型の delete 関数を**deletefunction**ストアドプロシージャにマップする**deletefunction**要素を示しています。 **Deleteperson**ストアドプロシージャは、ストレージモデルで宣言されています。

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

AssociationSetMapping 要素に適用した場合、 **Deletefunction** 要素は、概念モデルのアソシエーションの delete 関数をストアドプロシージャにマップします。

**Deletefunction**要素には、 **AssociationSetMapping**要素に適用するときに次の子要素を含めることができます。

-   EndProperty

#### <a name="applicable-attributes"></a>該当する属性

次の表では、 **AssociationSetMapping**要素に適用されるときに**deletefunction**要素に適用できる属性について説明します。

| 属性名            | 必須 | 値                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | はい         | 削除関数のマップ先となるストアド プロシージャの名前空間修飾名。 ストアド プロシージャはストレージ モデル内で宣言する必要があります。 |
| **RowsAffectedParameter** | いいえ          | 影響を受ける行の数を返す出力パラメーターの名前。                                                                               |

#### <a name="example"></a>例

次の例は、School モデルに基づいており、 **CourseInstructor** association の delete 関数を**DeleteCourseInstructor**ストアドプロシージャにマップするために使用される**deletefunction**要素を示しています。 **DeleteCourseInstructor**ストアドプロシージャは、ストレージモデルで宣言されています。

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

マッピングスキーマ言語 (MSL) の **Endproperty** 要素は、概念モデルのアソシエーションの end または変更関数と基になるデータベースとの間のマッピングを定義します。 プロパティ列のマッピングは子 ScalarProperty 要素で指定されます。

**Endproperty**要素を使用して、概念モデルのアソシエーションの end のマッピングを定義すると、AssociationSetMapping 要素の子になります。 **Endproperty**要素を使用して、概念モデルの関連付けの変更関数のマッピングを定義すると、insertfunction 要素または deletefunction 要素の子になります。

**Endproperty**要素には、次の子要素を含めることができます。

-   ScalarProperty (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Endproperty** 要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                 |
|:---------------|:------------|:------------------------------------------------------|
| 名前           | はい         | マップされるアソシエーション端の名前。 |

### <a name="example"></a>例

次の例では、概念モデルの**FK \_ course \_ Department**アソシエーションがデータベースの**Course**テーブルにマップされている**AssociationSetMapping**要素を示します。 アソシエーション型のプロパティとテーブル列の間のマッピングは、子 **Endproperty** 要素で指定されます。

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

次の例は、関連付け (**CourseInstructor**) の挿入関数と削除関数を基になるデータベースのストアドプロシージャにマップする**endproperty**要素を示しています。 ストレージ モデルにマップされて宣言されている関数。

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

マッピング仕様言語 (MSL) の **EntityContainerMapping** 要素は、概念モデルのエンティティコンテナーをストレージモデルのエンティティコンテナーにマップします。 **EntityContainerMapping**要素は、Mapping 要素の子です。

**EntityContainerMapping**要素には、次の子要素を含めることができます (順に表示されます)。

-   EntitySetMapping (0 個以上)
-   AssociationSetMapping (0 個以上)
-   FunctionImportMapping (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **EntityContainerMapping** 要素に適用できる属性について説明します。

| 属性名            | 必須 | 値                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StorageModelContainer** | はい         | マップされるストレージ モデルのエンティティ コンテナーの名前。                                                                                                                                                                                     |
| **CdmEntityContainer**    | はい         | マップされる概念モデルのエンティティ コンテナーの名前。                                                                                                                                                                                  |
| **GenerateUpdateViews**   | いいえ          | **True** または **False** です。 **False**の場合、更新ビューは生成されません。 データが正常にラウンドトリップしない可能性があるため、読み取り専用マッピングが無効である場合は、この属性を **False** に設定する必要があります。 <br/> 既定値は **True** です。 |

### <a name="example"></a>例

次の例は、 **SchoolModelEntities**コンテナー (概念モデルのエンティティコンテナー) を**SchoolModelStoreContainer**コンテナー (ストレージモデルのエンティティコンテナー) にマップする**EntityContainerMapping**要素を示しています。

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

マッピング仕様言語 (MSL) の **EntitySetMapping** 要素は、概念モデルエンティティセット内のすべての型をストレージモデルのエンティティセットにマップします。 概念モデルのエンティティ セットは、同じ型のエンティティのインスタンス (および派生型) の論理コンテナーです。 ストレージ モデルのエンティティ セットは、基になるデータベースのテーブルまたはビューを表します。 概念モデルのエンティティセットは、 **EntitySetMapping**要素の**Name**属性の値によって指定されます。 マップ先のテーブルまたはビューは、各子 MappingFragment 要素または**EntitySetMapping**要素自体の**storeentityset**属性によって指定されます。

**EntitySetMapping**要素には、次の子要素を含めることができます。

-   EntityTypeMapping (0 個以上)
-   QueryView (0 個または 1 個)
-   MappingFragment (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **EntitySetMapping** 要素に適用できる属性について説明します。

| 属性名           | 必須 | 値                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**                 | はい         | マッピングされている概念モデルのエンティティ セットの名前。                                                                                                                                                             |
| **TypeName** **1**       | いいえ          | マッピングされている概念モデルのエンティティ型の名前。                                                                                                                                                            |
| **Storeentityset** **1** | いいえ          | マップ先のストレージ モデルのエンティティ セットの名前。                                                                                                                                                             |
| **MakeColumnsDistinct**  | いいえ          | 個別の行のみが返されるかどうかによって、 **True**または**False**になります。 <br/> この属性が **True**に設定されている場合は、EntityContainerMapping 要素の **generateupdateviews** 属性が **False**に設定されている必要があります。 |

 

**1** 単一のエンティティ型を1つのテーブルにマップするために、TypeName 属性と storeentityset 子要素の代わりに **TypeName** 属性と **storeentityset** 属性を使用できます。

### <a name="example"></a>例

次の例は、概念モデルの course エンティティセットに含まれる 3**つの型**(基本型と2つの派生型) を、基になるデータベース内の3つの異なるテーブルにマップする**EntitySetMapping**要素を示しています。 テーブルは、各**Mappingfragment**要素の**storeentityset**属性によって指定されます。

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

マッピング仕様言語 (MSL) の **Entitytypemapping** 要素は、概念モデルのエンティティ型と、基になるデータベースのテーブルまたはビューの間のマッピングを定義します。 概念モデルのエンティティ型および基になるデータベース テーブルまたはビューの詳細については、「EntityType 要素 (CSDL)」および「EntitySet 要素 (SSDL)」を参照してください。 マッピングされている概念モデルのエンティティ型は、 **Entitytypemapping**要素の**TypeName**属性によって指定されます。 マップされるテーブルまたはビューは、子 MappingFragment 要素の **Storeentityset** 属性によって指定されます。

ModificationFunctionMapping 子要素を使用すると、エンティティ型の挿入関数、更新関数、または削除関数をデータベース内のストアド プロシージャにマッピングできます。

**Entitytypemapping**要素には、次の子要素を含めることができます。

-   MappingFragment (0 個以上)
-   ModificationFunctionMapping (0 個または 1 個)
-   ScalarProperty
-   条件

> [!NOTE]
> **Mappingfragment** 要素と **ModificationFunctionMapping** 要素は、同時に **entitytypemapping** 要素の子要素にすることはできません。


> [!NOTE]
> **ScalarProperty**要素と**Condition**要素は、functionimportmapping 要素内で使用されている場合にのみ、 **entitytypemapping**要素の子要素にすることができます。

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Entitytypemapping** 要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **TypeName**   | はい         | マッピングされている概念モデルのエンティティ型の名前空間修飾名です。 <br/> 型が抽象型または派生型である場合、値は `IsOfType(Namespace-qualified_type_name)` になる必要があります。 |

### <a name="example"></a>例

次の例は、2つの子 **Entitytypemapping** 要素を持つ EntitySetMapping 要素を示しています。 最初の **Entitytypemapping** 要素では、 **SchoolModel** エンティティ型が **person** テーブルにマップされます。 2番目の **Entitytypemapping** 要素では、 **SchoolModel** 型の更新機能が、データベース内のストアドプロシージャ **updateperson**にマップされます。

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

次の例は、ルート型が抽象である型階層のマッピングを示しています。 TypeName 属性の構文を使用することに注意して `IsOfType` ください。 **TypeName**

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

マッピングスキーマ言語 (MSL) の **Functionimportmapping** 要素は、概念モデルの関数インポートと、基になるデータベースのストアドプロシージャまたは関数との間のマッピングを定義します。 関数インポートは概念モデル内で、ストアド プロシージャはストレージ モデル内で宣言する必要があります。 詳細については、「FunctionImport 要素 (CSDL)」および「Function 要素 (SSDL)」を参照してください。

> [!NOTE]
> 既定では、関数インポートが概念モデル エンティティ型または複合型を返す場合、基になるストアド プロシージャが返す列の名前が概念モデル型のプロパティの名前と正確に一致する必要があります。 列名がプロパティ名と完全に一致しない場合は、ResultMapping 要素でマッピングを定義する必要があります。

**Functionimportmapping**要素には、次の子要素を含めることができます。

-   ResultMapping (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Functionimportmapping** 要素に適用できる属性について説明します。

| 属性名         | 必須 | 値                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| **FunctionImportName** | はい         | マップされる概念モデルの関数インポートの名前。           |
| **FunctionName**       | はい         | マップされるストレージ モデルの関数の名前空間修飾名。 |

### <a name="example"></a>例

次の例は、School モデル に基づいています。 ストレージ モデルの次の関数について考えます。

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

次の例は、関数と関数のインポートを相互にマップするために使用される **Functionimportmapping** 要素を示しています。

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a>InsertFunction 要素 (MSL)

マッピング仕様言語 (MSL) の **insertfunction** 要素は、概念モデルのエンティティ型またはアソシエーションの挿入関数を、基になるデータベースのストアドプロシージャにマップします。 変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。 詳しくは、「Function 要素 (SSDL)」をご覧ください。

> [!NOTE]
> エンティティ型の3つの挿入、更新、または削除操作をすべてストアドプロシージャにマップしない場合、実行時にマップされていない操作は失敗し、UpdateException がスローされます。

**Insertfunction**要素は ModificationFunctionMapping 要素の子にすることができ、entitytypemapping 要素または AssociationSetMapping 要素に適用されます。

### <a name="insertfunction-applied-to-entitytypemapping"></a>EntityTypeMapping への InsertFunction の適用

EntityTypeMapping 要素に適用される場合、 **Insertfunction** 要素は、概念モデルのエンティティ型の挿入関数をストアドプロシージャにマップします。

**Insertfunction**要素には、 **entitytypemapping**要素に適用するときに次の子要素を含めることができます。

-   AssociationEnd (0 個以上)
-   ComplexProperty (0 個以上)
-   ResultBinding (0 または 1 個)
-   ScarlarProperty (0 個以上)

#### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Entitytypemapping**要素に適用するときに**insertfunction**要素に適用できる属性について説明します。

| 属性名            | 必須 | 値                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | はい         | 挿入関数のマップ先であるストアド プロシージャの名前空間修飾名。 ストアド プロシージャはストレージ モデル内で宣言する必要があります。 |
| **RowsAffectedParameter** | いいえ          | 影響を受ける行の数を返す出力パラメーターの名前。                                                                               |

#### <a name="example"></a>例

次の例は、School モデルに基づいており、Person エンティティ型の insert 関数を**Insertfunction**ストアドプロシージャにマップするために使用される**insertfunction**要素を示しています。 **Insertperson**ストアドプロシージャは、ストレージモデルで宣言されています。

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

**Insertfunction**要素を AssociationSetMapping 要素に適用すると、概念モデルのアソシエーションの挿入関数をストアドプロシージャにマップします。

**Insertfunction**要素には、 **AssociationSetMapping**要素に適用するときに次の子要素を含めることができます。

-   EndProperty

#### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Insertfunction** 要素が **AssociationSetMapping** 要素に適用されるときに適用できる属性について説明します。

| 属性名            | 必須 | 値                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | はい         | 挿入関数のマップ先であるストアド プロシージャの名前空間修飾名。 ストアド プロシージャはストレージ モデル内で宣言する必要があります。 |
| **RowsAffectedParameter** | いいえ          | 影響を受ける行の数を返す出力パラメーターの名前。                                                                               |

#### <a name="example"></a>例

次の例は、School モデルに基づいており、 **CourseInstructor** association の insert 関数を**InsertCourseInstructor**ストアドプロシージャにマップするために使用される**insertfunction**要素を示しています。 **InsertCourseInstructor**ストアドプロシージャは、ストレージモデルで宣言されています。

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

マッピングスキーマ言語 (MSL) の **mapping** 要素には、概念モデルで定義されているオブジェクトをデータベースにマップするための情報が含まれています (ストレージモデルの説明を参照)。 詳細については、「CSDL 仕様」と「SSDL 仕様」を参照してください。

**Mapping**要素は、マッピングスキーマのルート要素です。 マッピング仕様の XML 名前空間は https://schemas.microsoft.com/ado/2009/11/mapping/cs です。

マッピング要素には、次の子要素をここに示す順序で含めることができます。

-   Alias(0 個以上)。
-   EntityContainerMapping (1 個の要素)

MSL で参照される概念モデル型およびストレージ モデル型の名前は、それぞれの名前空間名で修飾する必要があります。 概念モデルの名前空間の名前については、「Schema 要素 (CSDL)」を参照してください。 ストレージモデルの名前空間の名前については、「Schema 要素 (SSDL)」を参照してください。 MSL で使用される名前空間の別名は、Alias 要素で定義できます。

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **マッピング** 要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                 |
|:---------------|:------------|:------------------------------------------------------|
| **行間**      | はい         | **C-S**。 これは固定値で、変更できません。 |

### <a name="example"></a>例

次の例は、School モデルの一部に基づく **マッピング** 要素を示しています。 School モデルの詳細については、「クイックスタート (Entity Framework)」を参照してください。

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

## <a name="mappingfragment-element-msl"></a>MappingFragment 要素 (MSL)

マッピングスキーマ言語 (MSL) の **Mappingfragment** 要素は、概念モデルのエンティティ型のプロパティと、データベース内のテーブルまたはビューの間のマッピングを定義します。 概念モデルのエンティティ型および基になるデータベース テーブルまたはビューの詳細については、「EntityType 要素 (CSDL)」および「EntitySet 要素 (SSDL)」を参照してください。 **Mappingfragment**は、entitytypemapping 要素または EntitySetMapping 要素の子要素にすることができます。

**Mappingfragment**要素には、次の子要素を含めることができます。

-   ComplexType (0 個以上)
-   ScalarProperty (0 個以上)
-   Condition (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Mappingfragment** 要素に適用できる属性について説明します。

| 属性名          | 必須 | 値                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StoreEntitySet**      | はい         | マップされるテーブルまたはビューの名前。                                                                                                                                                                           |
| **MakeColumnsDistinct** | いいえ          | 個別の行のみが返されるかどうかによって、 **True**または**False**になります。 <br/> この属性が **True**に設定されている場合は、EntityContainerMapping 要素の **generateupdateviews** 属性が **False**に設定されている必要があります。 |

### <a name="example"></a>例

次の例では、 **Entitytypemapping**要素の子として**mappingfragment**要素を示しています。 この例では、概念モデルの **コース** の種類のプロパティは、データベース内の **course** テーブルの列にマップされます。

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

次の例は、 **EntitySetMapping**要素の子としての**mappingfragment**要素を示しています。 上の例のように、概念モデルの **コース** の種類のプロパティは、データベース内の **course** テーブルの列にマップされます。

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

マッピング仕様言語 (MSL) の **ModificationFunctionMapping** 要素は、概念モデルのエンティティ型の挿入、更新、および削除の各関数を、基になるデータベースのストアドプロシージャにマップします。 **ModificationFunctionMapping**要素は、概念モデル内の多対多のアソシエーションの挿入関数と削除関数を、基になるデータベースのストアドプロシージャにマップすることもできます。 変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。 詳しくは、「Function 要素 (SSDL)」をご覧ください。

> [!NOTE]
> エンティティ型の3つの挿入、更新、または削除操作をすべてストアドプロシージャにマップしない場合、実行時にマップされていない操作は失敗し、UpdateException がスローされます。


> [!NOTE]
> 継承階層の 1 つのエンティティの変更関数をストアド プロシージャにマップした場合、階層内のすべての型の変更関数がストアド プロシージャにマップされる必要があります。

**ModificationFunctionMapping**要素は、entitytypemapping 要素または AssociationSetMapping 要素の子にすることができます。

**ModificationFunctionMapping**要素には、次の子要素を含めることができます。

-   DeleteFunction (0 個または 1 個)
-   InsertFunction (0 個または 1 個)
-   UpdateFunction (0 個または 1 個)

**ModificationFunctionMapping**要素に適用できる属性はありません。

### <a name="example"></a>例

次の例は、School モデルの **People** エンティティセットのエンティティセットマッピングを示しています。 **Person**エンティティ型の列マッピングに加えて、 **person**型の挿入関数、更新関数、および削除関数のマッピングが表示されます。 ストレージ モデルにマップされて宣言されている関数。

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

次の例は、School モデルの **CourseInstructor** association セットのアソシエーションセットマッピングを示しています。 **CourseInstructor** association の列マッピングに加えて、 **CourseInstructor**アソシエーションの挿入関数と削除関数のマッピングが表示されます。 ストレージ モデルにマップされて宣言されている関数。

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

マッピング仕様言語 (MSL) の **QueryView** 要素は、概念モデルのエンティティ型またはアソシエーションと基になるデータベース内のテーブルとの間の読み取り専用のマッピングを定義します。 マッピングは、ストレージモデルに対して評価される Entity SQL クエリで定義され、結果セットを概念モデルのエンティティまたはアソシエーションの観点から表現します。 クエリ ビューは読み取り専用であるため、標準の更新コマンドを使用してクエリ ビューで定義された型を更新することはできません。 型の更新を行うには、変更関数を使用する必要があります。 詳細については、「方法: 変更関数をストアドプロシージャにマップする」を参照してください。

> [!NOTE]
> **QueryView**要素では、 **GroupBy**、group 集計、またはナビゲーションプロパティを含む Entity SQL 式はサポートされていません。

 

**QueryView**要素は、EntitySetMapping 要素または AssociationSetMapping 要素の子にすることができます。 前者の場合、クエリ ビューは、概念モデルのエンティティの読み取り専用マッピングを定義します。 後者の場合、クエリ ビューは、概念モデルのアソシエーションの読み取り専用マッピングを定義します。

> [!NOTE]
> **AssociationSetMapping**要素が参照制約との関連付けの場合、 **AssociationSetMapping**要素は無視されます。 詳細については、「参照要素の参照」を参照してください。

**QueryView**要素には、子要素を含めることはできません。

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **QueryView** 要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **TypeName**   | いいえ          | クエリ ビューによってマップされる概念モデル型の名前。 |

### <a name="example"></a>例

次の例では、 **EntitySetMapping**要素の子として**QueryView**要素を示し、School モデルの**Department**エンティティ型に対するクエリビューマッピングを定義します。

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

このクエリでは、ストレージモデルの **department** 型のメンバーのサブセットのみが返されるため、School モデルの **学科** の種類は、このマッピングに基づいて次のように変更されています。

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

次の例では、 **AssociationSetMapping**要素の子として**QueryView**要素を示し、School モデルのアソシエーションの読み取り専用マッピングを定義し `FK_Course_Department` ます。

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

-   ストレージ モデルのエンティティのプロパティの一部を含まない、概念モデルのエンティティを定義する。 これには、既定値を持たず、 **null** 値をサポートしていないプロパティが含まれます。
-   ストレージ モデルの計算列を概念モデルのエンティティ型のプロパティにマップする。
-   概念モデルのエンティティをパーティション分割するために使用される条件が等価性に基づかないマッピングを定義する。 **Condition**要素を使用して条件付きマッピングを指定する場合、指定された条件は指定した値と同じである必要があります。 詳細については、「Condition 要素 (MSL)」を参照してください。
-   ストレージ モデルの同一の列を概念モデルの複数の型にマップする。
-   複数の型を同じテーブルにマップする。
-   リレーショナル スキーマの外部キーに基づかない概念モデルのアソシエーションを定義する。
-   カスタム ビジネス ロジックを使用して、概念モデルのプロパティの値を設定する。 たとえば、概念モデルでは、データソースの文字列値 "T" をブール値の **true**にマップできます。
-   クエリ結果に対する条件付きフィルターを定義する。
-   概念モデルのデータに適用する制限をストレージ モデルよりも少なくする。 たとえば、概念モデルのプロパティを、マップ先の列で **null**値がサポートされていない場合でも、null 許容にすることができます。

エンティティのクエリ ビューを定義する際は、次の点に注意してください。

-   クエリ ビューは読み取り専用です。 エンティティの更新を行うには、変更関数を使用する必要があります。
-   クエリ ビューでエンティティ型を定義する場合、すべての関連エンティティもクエリ ビューで定義する必要があります。
-   リレーショナルスキーマのリンクテーブルを表すストレージモデル内のエンティティに多対多のアソシエーションをマップする場合は、このリンクテーブルの**AssociationSetMapping**要素に**QueryView**要素を定義する必要があります。
-   クエリ ビューは、型階層のすべての型に対して定義する必要があります。 これは、次の方法で行うことができます。
-   -   1つの **QueryView** 要素を使用して、階層内のすべてのエンティティ型の和集合を返す単一の Entity SQL クエリを指定します。
    -   1つの **QueryView** 要素で、CASE 演算子を使用して特定の条件に基づいて階層内の特定のエンティティ型を返す単一の Entity SQL クエリを指定します。
    -   階層内の特定の型に対して追加の **QueryView** 要素を使用します。 この場合は、 **QueryView**要素の**TypeName**属性を使用して、各ビューのエンティティ型を指定します。
-   クエリビューが定義されている場合、 **EntitySetMapping**要素で**StorageSetName**属性を指定することはできません。
-   クエリビューが定義されている場合、 **EntitySetMapping**要素に **プロパティ** マッピングを含めることはできません。

## <a name="resultbinding-element-msl"></a>ResultBinding 要素 (MSL)

マッピングスキーマ言語 (MSL) の **Resultbinding** 要素は、エンティティ型の変更関数が基になるデータベースのストアドプロシージャにマップされている場合に、ストアドプロシージャによって返される列の値を概念モデルのエンティティプロパティにマップします。 たとえば、id 列の値が insert ストアドプロシージャによって返される場合、 **Resultbinding** 要素は、返された値を概念モデルのエンティティ型プロパティにマップします。

**Resultbinding**要素は、insertfunction 要素または updatefunction 要素の子にすることができます。

**Resultbinding**要素に子要素を含めることはできません。

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Resultbinding** 要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **名前**       | はい         | マップされる概念モデルのエンティティ プロパティの名前。 |
| **[ColumnName]** | はい         | マップされる列の名前。                                          |

### <a name="example"></a>例

次の例は、School モデルに基づいており、 **Person**エンティティ型の挿入関数を**insertfunction**ストアドプロシージャにマップするために使用される**insertfunction**要素を示しています。 ( **Insertperson** ストアドプロシージャは次のようになり、ストレージモデルで宣言されています)。 **Resultbinding** 要素は、ストアドプロシージャ (**NewPersonID**) によって返される列の値をエンティティ型のプロパティ (**PersonID**) にマップするために使用されます。

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

次の Transact-sql では、 **Insertperson** ストアドプロシージャについて説明します。

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

マッピングスキーマ言語 (MSL) の **Resultmapping** 要素は、次の条件に該当する場合に、概念モデルの関数インポートと基になるデータベースのストアドプロシージャとの間のマッピングを定義します。

-   関数インポートが概念モデルのエンティティ型または複合型を返す場合
-   ストアド プロシージャが返す列の名前が、エンティティ型または複合型のプロパティの名前と完全には一致しない場合

既定では、ストアド プロシージャが返す列とエンティティ型または複合型とのマッピングは、列およびプロパティの名前に基づいています。 列名がプロパティ名と完全に一致しない場合は、 **resultmapping** 要素を使用してマッピングを定義する必要があります。 既定のマッピングの例については、「FunctionImportMapping 要素 (MSL)」を参照してください。

**Resultmapping**要素は、FunctionImportMapping 要素の子要素です。

**Resultmapping**要素には、次の子要素を含めることができます。

-   EntityTypeMapping (0 個以上)
-   ComplexTypeMapping

**Resultmapping**要素に適用できる属性はありません。

### <a name="example"></a>例

次のストアド プロシージャについて考えてみます。

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

以前のエンティティ型のインスタンスを返す関数インポートを作成するには、ストアドプロシージャによって返された列とエンティティ型の間のマッピングが **Resultmapping** 要素で定義されている必要があります。

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

マッピング仕様言語 (MSL) の **ScalarProperty** 要素は、概念モデルのエンティティ型、複合型、またはアソシエーションのプロパティを、基になるデータベースのテーブル列またはストアドプロシージャパラメーターにマップします。

> [!NOTE]
> 変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。 詳しくは、「Function 要素 (SSDL)」をご覧ください。

**ScalarProperty**要素は、次の要素の子にすることができます。

-   MappingFragment
-   InsertFunction
-   UpdateFunction
-   DeleteFunction
-   EndProperty
-   ComplexProperty
-   ResultMapping

**Mappingfragment**、 **complexproperty**、または**endproperty**要素の子として、 **ScalarProperty**要素は、概念モデルのプロパティをデータベースの列にマップします。 **Insertfunction**、 **updatefunction**、または**deletefunction**要素の子として、 **ScalarProperty**要素は、概念モデルのプロパティをストアドプロシージャパラメーターにマップします。

**ScalarProperty**要素には、子要素を含めることはできません。

### <a name="applicable-attributes"></a>該当する属性

**ScalarProperty**要素に適用される属性は、要素の役割によって異なります。

次の表では、 **ScalarProperty** 要素を使用して、概念モデルのプロパティをデータベースの列にマップするときに適用できる属性について説明します。

| 属性名 | 必須 | 値                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **名前**       | はい         | マップされる概念モデルのプロパティの名前。 |
| **[ColumnName]** | はい         | マップされるテーブル列の名前。              |

次の表では、 **ScalarProperty** 要素を使用して、概念モデルのプロパティをストアドプロシージャのパラメーターにマップするときに適用できる属性について説明します。

| 属性名    | 必須 | 値                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**          | はい         | マップされる概念モデルのプロパティの名前。                                                                                 |
| **ParameterName** | はい         | マップされるパラメーターの名前。                                                                                                 |
| **Version**       | いいえ          | 現在の値またはプロパティの元の値を同時実行チェックに使用するかどうかによって、 **current**または**original** 。 |

### <a name="example"></a>例

次の例は、2つの方法で使用される **ScalarProperty** 要素を示しています。

-   **Person エンティティ型**のプロパティを**person**テーブルの列にマップする。
-   **Person**エンティティ型のプロパティを**updateperson**ストアドプロシージャのパラメーターにマップする。 ストアド プロシージャはストレージ モデル内で宣言されます。

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

次の例は、概念モデルのアソシエーションの挿入関数と削除関数をデータベース内のストアドプロシージャにマップするために使用される **ScalarProperty** 要素を示しています。 ストアド プロシージャはストレージ モデル内で宣言されます。

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

マッピングスキーマ言語 (MSL) の **updatefunction** 要素は、概念モデルのエンティティ型の更新関数を、基になるデータベースのストアドプロシージャにマップします。 変更関数のマップ先であるストアド プロシージャは、ストレージ モデル内で宣言する必要があります。 詳しくは、「Function 要素 (SSDL)」をご覧ください。

> [!NOTE]
>  エンティティ型の3つの挿入、更新、または削除操作をすべてストアドプロシージャにマップしない場合、実行時にマップされていない操作は失敗し、UpdateException がスローされます。

**Updatefunction**要素は ModificationFunctionMapping 要素の子にすることができ、entitytypemapping 要素に適用されます。

**Updatefunction**要素には、次の子要素を含めることができます。

-   AssociationEnd (0 個以上)
-   ComplexProperty (0 個以上)
-   ResultBinding (0 または 1 個)
-   ScarlarProperty (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Updatefunction** 要素に適用できる属性について説明します。

| 属性名            | 必須 | 値                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **FunctionName**          | はい         | 更新関数のマップ先となるストアド プロシージャの名前空間修飾名。 ストアド プロシージャはストレージ モデル内で宣言する必要があります。 |
| **RowsAffectedParameter** | いいえ          | 影響を受ける行の数を返す出力パラメーターの名前。                                                                               |

### <a name="example"></a>例

次の例は、School モデルに基づいており、 **Person**エンティティ型の update 関数を**updatefunction**ストアドプロシージャにマップするために使用される**updatefunction**要素を示しています。 **Updateperson**ストアドプロシージャは、ストレージモデルで宣言されています。

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
