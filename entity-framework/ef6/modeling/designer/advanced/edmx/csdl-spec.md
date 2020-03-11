---
title: CSDL 仕様-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c54255f4-253f-49eb-bec8-ad7927ac2fa3
ms.openlocfilehash: 642e5977ecbbf0c474cac1ceae19d33a135aa875
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415515"
---
# <a name="csdl-specification"></a>CSDL 仕様
概念スキーマ定義言語 (CSDL : Conceptual Schema Definition Language) は、XML ベースの言語であり、データ駆動型アプリケーションの概念モデルを構成するエンティティ、リレーションシップ、および関数を記述します。 この概念モデルは、Entity Framework または WCF Data Services で使用できます。 CSDL で記述されたメタデータは、概念モデルで定義されているエンティティとリレーションシップをデータソースにマップするために、Entity Framework によって使用されます。 詳細については、「 [SSDL 仕様](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)と[MSL 仕様](~/ef6/modeling/designer/advanced/edmx/msl-spec.md)」を参照してください。

CSDL は、Entity Data Model の Entity Framework の実装です。

Entity Framework アプリケーションでは、概念モデルのメタデータは、csdl で記述された .csdl ファイルから EdmItemCollection のインスタンスに読み込まれ、のメソッドを使用してアクセスできます。System.string... メタデータ。 Entity Framework は、概念モデルのメタデータを使用して、概念モデルに対するクエリをデータソース固有のコマンドに変換します。

EF デザイナーは、デザイン時に .edmx ファイルに概念モデル情報を格納します。 EF デザイナーは、ビルド時に .edmx ファイルの情報を使用して、実行時に Entity Framework に必要な .csdl ファイルを作成します。

CSDL のバージョンは、XML 名前空間で区別されます。

| CSDL のバージョン | XML 名前空間                                |
|:-------------|:---------------------------------------------|
| CSDL v1      | https://schemas.microsoft.com/ado/2006/04/edm |
| CSDL v2      | https://schemas.microsoft.com/ado/2008/09/edm |
| CSDL v3      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a>Association 要素 (CSDL)

**Association**要素は、2つのエンティティ型間のリレーションシップを定義します。 アソシエーションでは、リレーションシップに関連するエンティティ型、およびリレーションシップの各 End におけるエンティティ型の数 (多重度と呼ばれる) を指定する必要があります。 アソシエーション end の多重度には、1 (1)、0または 1 (0 ..1)、または多数 (\*) の値を指定できます。 この情報は、2 つの子 End 要素で指定されます。

アソシエーションの一方の End にあるエンティティ型のインスタンスには、それらがエンティティ型で公開されている場合、ナビゲーション プロパティまたは外部キーからアクセスできます。

アプリケーション内で、アソシエーションのインスタンスは、エンティティ型のインスタンスの間の特定のアソシエーションを表します。 アソシエーション インスタンスは、アソシエーション セットに論理的にグループ化されます。

**Association**要素には、次の子要素を含めることができます (順に記載されています)。

-   Documentation (0 個または1個の要素)
-   End (ちょうど2つの要素)
-   すべての要素のインデックス (0 個または1個)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Association**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                        |
|:---------------|:------------|:-----------------------------|
| **名前**       | はい         | アソシエーションの名前。 |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**Association**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、外部キーが**Customer**および**Order**エンティティ型で公開されていない場合に、 **CustomerOrders**関連付けを定義する**association**要素を示しています。 アソシエーションの各**End**の複数**要素**の接続性の値は、**顧客**に複数の**注文**を関連付けることができることを示していますが、**注文**に関連付けることができるのは1つの**顧客**だけです。 また、 **OnDelete**要素は、**顧客**が削除されると、特定の**顧客**に関連付けられていて、ObjectContext に読み込まれているすべての**注文**が削除されることを示します。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

次の例は、 **Customer**および**Order**エンティティ型で外部キーが公開されている場合に、 **CustomerOrders**関連付けを定義する**association**要素を示しています。 外部キーが公開されている場合、エンティティ間のリレーションシップは **、要素を使用して**管理されます。 このアソシエーションをデータ ソースにマップするために、対応する AssociationSetMapping 要素は必要ありません。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
   <ReferentialConstraint>
        <Principal Role="Customer">
            <PropertyRef Name="Id" />
        </Principal>
        <Dependent Role="Order">
             <PropertyRef Name="CustomerId" />
         </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="associationset-element-csdl"></a>AssociationSet 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**AssociationSet**要素は、同じ型のアソシエーションインスタンスの論理コンテナーです。 アソシエーション セットは、複数のアソシエーション インスタンスを 1 つのデータ ソースにマップできるようグループ化する方法を指定します。  

**AssociationSet**要素には、次の子要素を含めることができます (この順序で表示されます)。

-   Documentation (0 個または1個の要素を使用できます)
-   End (2 つの要素が必要です)
-   Annotation 要素 (0 個以上の要素を使用できます)

Association**属性は、アソシエーション**セットに含まれるアソシエーションの種類を指定します。 アソシエーションセットの末尾を構成するエンティティセットは、厳密に2つの子**End**要素で指定されます。

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **AssociationSet**要素に適用できる属性について説明します。

| 属性名  | 必須 | 値                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**        | はい         | エンティティ セットの名前。 **Name**属性の値を**Association**属性の値と同じにすることはできません。                                 |
| **づける** | はい         | 関連付けの完全修飾名。そのインスタンスは、アソシエーション セットに格納されます。 関連付けは、アソシエーション セットと同じ名前空間に存在する必要があります。 |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**AssociationSet**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、2つの**AssociationSet**要素を持つ**EntityContainer**要素を示しています。

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="collectiontype-element-csdl"></a>CollectionType 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**CollectionType**要素は、関数パラメーターまたは関数の戻り値の型がコレクションであることを指定します。 **CollectionType**要素は、Parameter 要素または ReturnType (Function) 要素の子にすることができます。 コレクションの型は、 **type**属性または次のいずれかの子要素を使用して指定できます。

-   **CollectionType**
-   ReferenceType
-   RowType
-   TypeRef

> [!NOTE]
> **型**属性と子要素の両方でコレクションの型が指定されている場合、モデルは検証されません。

 

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **CollectionType**要素に適用できる属性について説明します。 **DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**、および**Collation**属性は、 **edmsimpletypes**のコレクションにのみ適用されることに注意してください。

| 属性名                                                          | 必須 | 値                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**                                                                | いいえ          | コレクションの型。                                                                                                                                                                                                      |
| **NULL 値の使用**                                                            | いいえ          | プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または **False**。 <br/> [!NOTE]                                                                                                                 |
| CSDL v1 で > 複合型プロパティには、`Nullable="False"`が必要です。 |             |                                                                                                                                                                                                                                  |
| **既定**                                                        | いいえ          | プロパティの既定値です。                                                                                                                                                                                               |
| **MaxLength**                                                           | いいえ          | プロパティ値の最大長。                                                                                                                                                                                        |
| **FixedLength**                                                         | いいえ          | プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。                                                                                                                           |
| **[精度]**                                                           | いいえ          | プロパティ値の有効桁数。                                                                                                                                                                                             |
| **スケール**                                                               | いいえ          | プロパティ値の小数点以下桁数。                                                                                                                                                                                                 |
| **SRID**                                                                | いいえ          | 空間システム参照識別子。 空間型のプロパティに対してのみ有効です。   詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx) 」を参照してください。 |
| **Unicode**                                                             | いいえ          | プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。                                                                                                                                |
| **Collation**                                                           | いいえ          | データ ソースで使用される照合順序を指定する文字列。                                                                                                                                                    |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**CollectionType**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **CollectionType**要素を使用して、関数が**Person**エンティティ型のコレクション ( **ElementType**属性で指定) を返すことを指定するモデル定義関数を示しています。

``` xml
 <Function Name="LastNamesAfter">
        <Parameter Name="someString" Type="Edm.String"/>
        <ReturnType>
             <CollectionType  ElementType="SchoolModel.Person"/>
        </ReturnType>
        <DefiningExpression>
             SELECT VALUE p
             FROM SchoolEntities.People AS p
             WHERE p.LastName >= someString
        </DefiningExpression>
 </Function>
```
 

次の例は、 **CollectionType**要素を使用して、 **RowType**要素で指定された行のコレクションを返すことを指定するモデル定義関数を示しています。

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

次の例は、 **CollectionType**要素を使用して、関数が**学科**エンティティ型のコレクションをパラメーターとして受け入れることを指定するモデル定義関数を示しています。

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="complextype-element-csdl"></a>ComplexType 要素 (CSDL)

**ComplexType**要素は、 **edmsimpletype**プロパティまたは他の複合型で構成されるデータ構造を定義します。  複合型は、エンティティ型または別の複合型のプロパティにすることができます。 複合型は、データを定義するという点でエンティティ型に似ています。 ただし、複合型とエンティティ型の間にはいくつかの重要な違いがあります。

-   複合型には ID (またはキー) がないため、独立して存在することができません。 複合型は、エンティティ型またはその他の複合型のプロパティとしてのみ存在できます。
-   複合型は、アソシエーションに参加できません。 アソシエーションのいずれの End にも複合型を指定できないため、複合型にはナビゲーション プロパティを定義できません。
-   複合型のスカラー プロパティはそれぞれ null に設定できますが、複合型のプロパティには null 値を指定できません。

**ComplexType**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation (0 個または1個の要素)
-   Property (0 個以上の要素)
-   Annotation 要素 (0 個以上の要素)

次の表では、 **ComplexType**要素に適用できる属性について説明します。

| 属性名                                                                                                 | 必須 | 値                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Name                                                                                                           | はい         | 複合型の名前。 複合型の名前は、モデルのスコープ内にある別の複合型、エンティティ型、またはアソシエーションの名前と同じにすることはできません。 |
| BaseType                                                                                                       | いいえ          | 定義される複合型の基本データ型である、別の複合型の名前。 <br/> [!NOTE]                                                                     |
| > この属性は CSDL v1 では適用できません。 複合型の継承は、このバージョンではサポートされません。 |             |                                                                                                                                                                                     |
| 概要                                                                                                       | いいえ          | 複合型が抽象型であるかどうかに応じて、 **True**または**False** (既定値)。 <br/> [!NOTE]                                                                  |
| > この属性は CSDL v1 では適用できません。 このバージョンの複合型を抽象型にすることはできません。         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**ComplexType**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、 **Edmsimpletype**プロパティ**StreetAddress**、 **City**、 **StateOrProvince**、Country、および**郵便** **番号**を使用して、複合型**Address**を示します。

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

エンティティ型のプロパティとして複合型の**アドレス**(上記) を定義するには、エンティティ型の定義でプロパティの型を宣言する必要があります。 次の例では、エンティティ型 (**パブリッシャー**) の複合型として**Address**プロパティを示しています。

``` xml
 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BooksModel.Address" Name="Address" Nullable="false" />
       <NavigationProperty Name="Books" Relationship="BooksModel.PublishedBy"
                           FromRole="Publisher" ToRole="Book" />
     </EntityType>
```
 

 

## <a name="definingexpression-element-csdl"></a>DefiningExpression 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の definition **ingexpression**要素には、概念モデルの関数を定義する Entity SQL 式が含まれています。  

> [!NOTE]
> 検証のために、**定義**要素には任意のコンテンツを含めることができます。 ただし、**定義**要素に有効な Entity SQL が含まれていない場合、Entity Framework は実行時に例外をスローします。

 

### <a name="applicable-attributes"></a>該当する属性

任意の数の annotation 属性 (カスタム XML 属性 **) を定義**することができます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例では、定義、**式**を使用して、書籍が発行されてからの年数を返す関数を定義します。 **定義**済みの要素の内容は Entity SQL で記述されます。

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a>Dependent 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**dependent**要素は、参照に関する制約の依存 end を定義し、参照制約の依存 end を定義します。 参照整合性の**制約要素は、リレーショナル**データベースの参照整合性制約に似た機能を定義します。 データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティは別のエンティティ型のエンティティ キーを参照できます。 参照されるエンティティ型は、制約の*プリンシパル end*と呼ばれます。 プリンシパル end を参照するエンティティ型は、制約の*依存 end*と呼ばれます。 **Propertyref**要素は、プリンシパル end を参照するキーを指定するために使用されます。

**Dependent**要素には、次の子要素を含めることができます (順に記載されています)。

-   PropertyRef (1 つ以上の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>該当する属性

次の表は、**依存**要素に適用できる属性を示しています。

| 属性名 | 必須 | 値                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **ロール**       | はい         | アソシエーションの依存 End 上のエンティティ型の名前。 |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**依存**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、 **publishedby**関連付けの定義の一部として使用されている **、オブジェクトの**場所を示します。 **Book**エンティティ型の**PublisherId**プロパティは、参照制約の依存 end を構成します。

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="documentation-element-csdl"></a>Documentation 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**Documentation**要素は、親要素で定義されているオブジェクトに関する情報を提供するために使用できます。 .Edmx ファイルで、 **documentation**要素が、EF デザイナーのデザイン画面 (エンティティ、アソシエーション、プロパティなど) でオブジェクトとして表示される要素の子である場合、 **documentation**要素の内容は、そのオブジェクトの Visual Studio の [ **プロパティ**] ウィンドウに表示されます。

**Documentation**要素には、次の子要素を含めることができます (一覧の順序)。

-   **Summary**: 親要素の簡単な説明。 (0 個または 1 個の要素)。
-   **Longdescription**: 親要素の詳細な説明。 (0 個または 1 個の要素)。
-   annotation 要素 (0 個以上の要素)。

### <a name="applicable-attributes"></a>該当する属性

**Documentation**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、EntityType 要素の子要素としての**Documentation**要素を示しています。 以下のスニペットが .edmx ファイルの CSDL コンテンツに含まれている場合、[`Customer`] エンティティ型をクリックすると、Visual Studio の [ **プロパティ**] ウィンドウに**Summary**要素と**longdescription**要素の内容が表示されます。

``` xml
 <EntityType Name="Customer">
    <Documentation>
      <Summary>Summary here.</Summary>
      <LongDescription>Long description here.</LongDescription>
    </Documentation>
    <Key>
      <PropertyRef Name="CustomerId" />
    </Key>
    <Property Type="Int32" Name="CustomerId" Nullable="false" />
    <Property Type="String" Name="Name" Nullable="false" />
 </EntityType>
```
 

 

## <a name="end-element-csdl"></a>End 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**End**要素は、Association 要素または AssociationSet 要素の子にすることができます。 どちらの場合も、 **End**要素の役割は異なり、適用可能な属性も異なります。

### <a name="end-element-as-a-child-of-the-association-element"></a>Association 要素の子としての End 要素

**End**要素 ( **association**要素の子として) は、アソシエーションの一方の end のエンティティ型と、アソシエーションのその end に存在できるエンティティ型のインスタンスの数を識別します。 アソシエーション End はアソシエーションの一部として定義され、アソシエーションには必ず 2 つのアソシエーション End が必要です。 アソシエーションの 1 つの End にあるエンティティ型のインスタンスには、エンティティ型で公開されている場合、ナビゲーション プロパティまたは外部キーからアクセスできます。  

**終了**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation (0 個または1個の要素)
-   OnDelete (0 個または1個の要素)
-   Annotation 要素 (0 個以上の要素)

#### <a name="applicable-attributes"></a>該当する属性

次の表では、 **End**要素が**Association**要素の子である場合に適用できる属性について説明します。

| 属性名   | 必須 | 値                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**         | はい         | アソシエーションの一方の End のエンティティ型の名前。                                                                                                                                                                                                                                                                                                                                                         |
| **ロール**         | いいえ          | アソシエーション End の名前。 名前が指定されない場合、アソシエーション End のエンティティ型の名前が使用されます。                                                                                                                                                                                                                                                                                           |
| **カーディナリティ** | はい         | **1**、 **0 ..1**、またはアソシエーションの最後にあるエンティティ型のインスタンスの数に応じて **\*** ます。 <br/> **1**は、アソシエーション end に1つのエンティティ型インスタンスが存在することを示します。 <br/> **0 ..1**は、アソシエーション end に0個または1個のエンティティ型インスタンスが存在することを示します。 <br/> **\*** は、アソシエーション end に0個以上のエンティティ型インスタンスが存在することを示します。 |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**終了**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

#### <a name="example"></a>例

次の例は、 **CustomerOrders**の関連付けを定義する**association**要素を示しています。 アソシエーションの各**End**の複数**要素**の接続性の値は、**顧客**に複数の**注文**を関連付けることができることを示していますが、**注文**に関連付けることができるのは1つの**顧客**だけです。 また、 **OnDelete**要素は、**顧客**が削除された場合に、特定の**顧客**に関連付けられているすべての注文と ObjectContext に読み込まれたすべての**注文**を削除することを示します。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a>AssociationSet 要素の子としての End 要素

**End**要素は、アソシエーションセットの1つの端を指定します。 **AssociationSet**要素には、2つの**End**要素が含まれている必要があります。 **End**要素に含まれる情報は、アソシエーションセットをデータソースにマップするときに使用されます。

**終了**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation (0 個または1個の要素)
-   Annotation 要素 (0 個以上の要素)

> [!NOTE]
> Annotation 要素は、それ以外のすべての子要素より後に指定する必要があります。 Annotation 要素は、CSDL v2 以降でのみ使用できます。

 

#### <a name="applicable-attributes"></a>該当する属性

次の表では、 **AssociationSet**要素の子である場合に**End**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | はい         | 親**AssociationSet**要素の1つの end を定義する**EntitySet**要素の名前。 **EntitySet**要素は、親**AssociationSet**要素と同じエンティティコンテナーで定義されている必要があります。 |
| **ロール**       | いいえ          | アソシエーション セット End の名前。 **Role**属性が使用されていない場合、アソシエーションセット end の名前はエンティティセットの名前になります。                                                                   |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**終了**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

#### <a name="example"></a>例

次の例は、 **2 つの3つ**の要素を持つ**AssociationSet**要素を2つ持つ**EntityContainer**要素を示しています。

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitycontainer-element-csdl"></a>EntityContainer 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**EntityContainer**要素は、エンティティセット、アソシエーションセット、および関数インポートの論理コンテナーです。 概念モデルのエンティティ コンテナーは、EntityContainerMapping 要素を通じてストレージ モデルのエンティティ コンテナーにマップされます。 ストレージ モデルのエンティティ コンテナーは、データベースの構造を記述します。エンティティ セットはデータベースのテーブル、アソシエーション セットは外部キー、関数インポートはストアド プロシージャをそれぞれ記述します。

**EntityContainer**要素には、0個または1個の Documentation 要素を含めることができます。 **Documentation**要素が存在する場合は、 **EntitySet**、 **AssociationSet**、および**FunctionImport**のすべての要素の前に記述する必要があります。

**EntityContainer**要素には、次の子要素を0個以上含めることができます (一覧の順序)。

-   EntitySet
-   AssociationSet
-   FunctionImport 要素
-   Annotation 要素

**Entitycontainer**要素を拡張して、同じ名前空間内にある別の**entitycontainer**の内容を含めることができます。 別の**entitycontainer**の内容を参照する**entitycontainer**要素に含めるには、 **Extends**属性の値を、含める**entitycontainer**要素の名前に設定します。 含まれている**entitycontainer**要素のすべての子要素は、参照している**entitycontainer**要素の子要素として扱われます。

### <a name="applicable-attributes"></a>該当する属性

次の表は、 **Using**要素に適用できる属性を示しています。

| 属性名 | 必須 | 値                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **名前**       | はい         | エンティティ コンテナー名。                               |
| **拡張**    | いいえ          | 同じ名前空間内にある別のエンティティ コンテナーの名前 |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**EntityContainer**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、3つのエンティティセットと2つのアソシエーションセットを定義する**EntityContainer**要素を示しています。

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entityset-element-csdl"></a>EntitySet 要素 (CSDL)

概念スキーマ定義言語の**EntitySet**要素は、エンティティ型のインスタンスと、そのエンティティ型から派生した任意の型のインスタンスの論理コンテナーです。 エンティティ型とエンティティ セットの間のリレーションシップは、リレーショナル データベースの行とテーブルの間のリレーションシップと似ています。 エンティティ型は、行と同様に関連データのセットを定義し、エンティティ セットには、テーブルと同様に、その定義のインスタンスが含まれます。 エンティティ セットは、エンティティ型のインスタンスをグループ化するための構造を提供します。これにより、データ ソース内の関連するデータ構造に、エンティティ型のインスタンスをマッピングできるようになります。  

特定のエンティティ型に対して複数のエンティティ セットを定義できます。

> [!NOTE]
> EF デザイナーでは、型ごとに複数のエンティティセットを含む概念モデルはサポートされていません。

 

**EntitySet**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation 要素 (0 個または1個の要素が許可されています)
-   Annotation 要素 (0 個以上の要素を使用できます)

### <a name="applicable-attributes"></a>該当する属性

次の表は、 **EntitySet**要素に適用できる属性を示しています。

| 属性名 | 必須 | 値                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **名前**       | はい         | エンティティ セットの名前。                                                              |
| **EntityType** | はい         | エンティティ型の完全修飾名。そのインスタンスは、エンティティ セットに格納されます。 |

 

> [!NOTE]
> **EntitySet**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、3つの**EntitySet**要素を持つ**EntityContainer**要素を示しています。

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

型ごとに複数のエンティティ セット (Multiple-Entity-Sets-per-Type: MEST) を定義できます。 次の例では、 **Book**エンティティ型に対して2つのエンティティセットを持つエンティティコンテナーを定義します。

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="FictionBooks" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="BookAuthor" Association="BooksModel.BookAuthor">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitytype-element-csdl"></a>EntityType 要素 (CSDL)

**EntityType**要素は、概念モデルの最上位レベルの概念 (顧客や注文など) の構造を表します。 エンティティ型は、アプリケーションのエンティティ型インスタンス用テンプレートです。 各テンプレートには、次の情報が含まれています。

-   一意の名前 (必須)
-   1 つ以上のプロパティにより定義されるエンティティ キー (必須)
-   データ格納用のプロパティ (省略可能)
-   アソシエーションの 1 つの End から別の End へのナビゲーションを可能にするナビゲーション プロパティ (省略可能)

アプリケーションでは、エンティティ型のインスタンスが特定のオブジェクト (特定の顧客や注文など) を表します。 エンティティ型の各インスタンスに対して、エンティティ セット内のエンティティ キーを一意にする必要があります。

2 つのエンティティ型のインスタンスは、型が同じであり、エンティティ キーの値が等しい場合にのみ、等価のインスタンスと見なされます。

**EntityType**要素には、次の子要素を含めることができます (この順序で表示されます)。

-   Documentation (0 個または1個の要素)
-   Key (0 個または 1 個の要素)
-   Property (0 個以上の要素)
-   NavigationProperty (0 個以上の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **EntityType**要素に適用できる属性について説明します。

| 属性名                                                                                                                                  | 必須 | 値                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| **名前**                                                                                                                                        | はい         | エンティティ型の名前。                                                                     |
| **BaseType**                                                                                                                                    | いいえ          | 定義するエンティティ型の基本の型である別のエンティティ型の名前。  |
| **抽出**                                                                                                                                    | いいえ          | エンティティ型が抽象型であるかどうかによって、 **True**または**False**になります。                 |
| **OpenType**                                                                                                                                    | いいえ          | エンティティ型がオープンエンティティ型かどうかによって、 **True**または**False**になります。 <br/> [!NOTE] |
| > **OpenType**属性は、ADO.NET Data Services で使用される概念モデルで定義されているエンティティ型にのみ適用されます。 |             |                                                                                                  |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**EntityType**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、3つの**プロパティ**要素と2つの**NavigationProperty**要素を持つ**EntityType**要素を示しています。

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="enumtype-element-csdl"></a>EnumType 要素 (CSDL)

**EnumType**要素は列挙型を表します。

**EnumType**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation (0 個または1個の要素)
-   Member (0 個以上の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>該当する属性

次の表は、 **EnumType**要素に適用できる属性を示しています。

| 属性名     | 必須 | 値                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**           | はい         | エンティティ型の名前。                                                                                                                                                                  |
| **IsFlags**        | いいえ          | 列挙型をフラグのセットとして使用できるかどうかによって、 **True**または**False**。 既定値は**False です**。                                                                     |
| **UnderlyingType** | いいえ          | **Edm. Byte**, Edm. **Int16**, edm. **Int32**, **Edm. Int64**または**edm. SByte**型の値の範囲を定義します。   列挙要素の基になる既定の型は、 **Edm. Int32.** です。 |

 

> [!NOTE]
> **EnumType**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、3つの**メンバー**要素を持つ**EnumType**要素を示しています。

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a>Function 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**Function**要素は、概念モデルで関数を定義または宣言するために使用されます。 関数は、DefiningExpression 要素を使用して定義されます。  

**Function**要素には、次の子要素を含めることができます (順に記載されています)。

-   Documentation (0 個または1個の要素)
-   Parameter (0 個以上の要素)
-   DefiningExpression (0 個または 1 個の要素)
-   ReturnType (Function) (0 個または1個の要素)
-   Annotation 要素 (0 個以上の要素)

関数の戻り値の型は、 **returntype** (function) 要素または**returntype**属性 (下記参照) のいずれかで指定する必要がありますが、両方を指定することはできません。 有効な戻り値の型には、EdmSimpleType、エンティティ型、複合型、行型、または参照型 (あるいはこれらの型のいずれかのコレクション) があります。

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Function**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                              |
|:---------------|:------------|:-----------------------------------|
| **名前**       | はい         | 関数の名前です。          |
| **ReturnType** | いいえ          | 関数の戻り値の型。 |

 

> [!NOTE]
> **関数**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、**関数**要素を使用して、インストラクターが雇用されてからの年数を返す関数を定義しています。

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a>FunctionImport 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**FunctionImport**要素は、データソースで定義されているものの、概念モデルを通じてオブジェクトで使用できる関数を表します。 たとえば、ストレージ モデルの Function 要素を使用して、データベースのストアド プロシージャを表すことができます。 概念モデルの**FunctionImport**要素は、Entity Framework アプリケーション内の対応する関数を表し、FunctionImportMapping 要素を使用してストレージモデル関数にマップされます。 関数がアプリケーションで呼び出されると、対応するストアド プロシージャがデータベースで実行されます。

**FunctionImport**要素には、次の子要素を含めることができます (この順序で表示されます)。

-   Documentation (0 個または1個の要素を使用できます)
-   Parameter (0 個以上の要素を含めることができます)。
-   Annotation 要素 (0 個以上の要素を使用できます)
-   ReturnType (FunctionImport) (0 個以上の要素を使用できます)

関数が受け入れるパラメーターごとに1つの**パラメーター**要素を定義する必要があります。

関数の戻り値の型は、 **returntype** (FunctionImport) 要素または**returntype**属性 (下記参照) のいずれかで指定する必要がありますが、両方を指定することはできません。 戻り値の型の値は、EdmSimpleType、EntityType、または ComplexType のコレクションである必要があります。

### <a name="applicable-attributes"></a>該当する属性

次の表は、 **FunctionImport**要素に適用できる属性を示しています。

| 属性名   | 必須 | 値                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**         | はい         | インポートされる関数の名前。                                                                                                                                                                    |
| **ReturnType**   | いいえ          | 関数が返す型。 関数が値を返さない場合は、この属性を使用しないでください。 それ以外の場合、値は ComplexType、EntityType、または EDMSimpleType のコレクションである必要があります。        |
| **EntitySet**    | いいえ          | 関数がエンティティ型のコレクションを返す場合、 **EntitySet**の値は、コレクションが属するエンティティセットである必要があります。 そうでない場合は、 **EntitySet** 属性を使用しないでください。 |
| **IsComposable** | いいえ          | 値が true に設定されている場合、関数はコンポーザブル (テーブル値関数) であり、LINQ クエリで使用できます。  既定値は**false**です。                                                           |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**FunctionImport**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、1つのパラメーターを受け取り、エンティティ型のコレクションを返す**FunctionImport**要素を示しています。

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a>key 要素 (CSDL)

**Key**要素は EntityType 要素の子要素であり、*エンティティキー* (id を決定するエンティティ型のプロパティまたはプロパティのセット) を定義します。 エンティティ キーを構成するプロパティは、デザイン時に選択されます。 エンティティ キー プロパティの値は、実行時にエンティティ セット内でエンティティ型のインスタンスを一意に識別する必要があります。 エンティティ キーを構成するプロパティには、エンティティ セット内のインスタンスの一意性を保証するものを選択する必要があります。 **Key**要素は、エンティティ型の1つ以上のプロパティを参照することによって、エンティティキーを定義します。

**Key**要素には、次の子要素を含めることができます。

-   PropertyRef (1 つ以上の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>該当する属性

任意の数の annotation 属性 (カスタム XML 属性) を**キー**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例では、 **Book**という名前のエンティティ型を定義しています。 エンティティキーは、エンティティ型の**ISBN**プロパティを参照することによって定義されます。

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

**Isbn**プロパティは、国際標準書籍番号 (ISBN) が書籍を一意に識別するため、エンティティキーに適した選択肢です。

次の例は、**名前**と**アドレス**の2つのプロパティで構成されるエンティティキーを持つエンティティ型 (**Author**) を示しています。

``` xml
 <EntityType Name="Author">
   <Key>
     <PropertyRef Name="Name" />
     <PropertyRef Name="Address" />
   </Key>
   <Property Type="String" Name="Name" Nullable="false" />
   <Property Type="String" Name="Address" Nullable="false" />
   <NavigationProperty Name="Books" Relationship="BooksModel.WrittenBy"
                       FromRole="Author" ToRole="Book" />
 </EntityType>
```
 

同じ名前の2人の作成者が同じアドレスに住んでいる可能性が低いため、エンティティキーには**名前**と**アドレス**を使用することをお勧めします。 ただし、エンティティ キーのこの選択では、エンティティ セット内のエンティティ キーの一意性を絶対的に保証することはできません。 この場合は、作成者を一意に識別するために使用できる、 **AuthorId**などのプロパティを追加することをお勧めします。

 

## <a name="member-element-csdl"></a>Member 要素 (CSDL)

**Member**要素は EnumType 要素の子要素であり、列挙型のメンバーを定義します。

### <a name="applicable-attributes"></a>該当する属性

次の表は、 **FunctionImport**要素に適用できる属性を示しています。

| 属性名 | 必須 | 値                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**       | はい         | メンバーの名前。                                                                                                                                                                  |
| **Value**      | いいえ          | メンバーの値。 既定では、最初のメンバーの値は0で、連続する各列挙子の値は1ずつインクリメントされます。 同じ値を持つ複数のメンバーが存在する場合があります。 |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**FunctionImport**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、3つの**メンバー**要素を持つ**EnumType**要素を示しています。

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a>NavigationProperty 要素 (CSDL)

**NavigationProperty**要素は、アソシエーションのもう一方の end への参照を提供するナビゲーションプロパティを定義します。 Property 要素で定義されるプロパティとは異なり、ナビゲーション プロパティはデータの形と特性を定義しません。 ナビゲーション プロパティは、アソシエーション内で 2 つのエンティティ型間を移動するための手段を提供します。

ナビゲーション プロパティは、アソシエーション End の両方のエンティティ型で省略可能です。 1 つのアソシエーション End のエンティティ型にナビゲーション プロパティを定義した場合に、そのアソシエーションの他方の End でもエンティティ型にナビゲーション プロパティを定義する必要はありません。

ナビゲーション プロパティによって返されるデータ型は、リモートのアソシエーション End の多重度により決まります。 たとえば、ナビゲーションプロパティ**Ordersnavprop**が**customer**エンティティ型に存在し、 **customer**と**Order**の間に一対多のアソシエーションを移動するとします。 ナビゲーションプロパティのリモートアソシエーション end は多重度が多 (\*) であるため、そのデータ型はコレクション (**順序**) です。 同様に、ナビゲーションプロパティの Customer navigation **prop**が**Order**エンティティ型に存在する場合、リモート end の多重度は 1 (1) であるため、データ型は**顧客**になります。

**NavigationProperty**要素には、次の子要素を含めることができます (この順序で表示されます)。

-   Documentation (0 個または1個の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **NavigationProperty**要素に適用できる属性について説明します。

| 属性名   | 必須 | 値                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**         | はい         | ナビゲーション プロパティの名前。                                                                                                                                                                                                             |
| **リレーションシップ** | はい         | モデルのスコープ内にあるアソシエーションの名前。                                                                                                                                                                                |
| **ToRole**       | はい         | ナビゲーションが終了する側のアソシエーション End。 **Torole**属性の値は、アソシエーション end のいずれかで定義されている**ロール**属性の1つの値と同じである必要があります (associationend 要素で定義されています)。       |
| **FromRole**     | はい         | ナビゲーションが始まる側のアソシエーション End。 **Fromrole**属性の値は、アソシエーション end のいずれかで定義されているいずれかの**ロール**属性の値と同じである必要があります (associationend 要素で定義されています)。 |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**NavigationProperty**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、2つのナビゲーションプロパティ (**publishedby** **writ)** を持つエンティティ型 (**Book**) を定義します。

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="ondelete-element-csdl"></a>OnDelete 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**OnDelete**要素は、アソシエーションに関連付けられている動作を定義します。 **アクション**属性がアソシエーションの一方の End で**Cascade**に設定されている場合、最初の end のエンティティ型が削除されると、アソシエーションのもう一方の end に関連するエンティティ型が削除されます。 2つのエンティティ型間のアソシエーションが主キーと主キーのリレーションシップである場合、アソシエーションのもう一方の end のプリンシパルオブジェクトが**OnDelete**の仕様に関係なく削除されると、読み込まれた依存オブジェクトが削除されます。  

> [!NOTE]
> **OnDelete**要素は、アプリケーションの実行時の動作にのみ影響します。データソースの動作には影響しません。 データ ソースで定義された動作は、アプリケーションで定義された動作と同じである必要があります。

 

**OnDelete**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation (0 個または1個の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>該当する属性

次の表は、 **OnDelete**要素に適用できる属性を示しています。

| 属性名 | 必須 | 値                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **操作**     | はい         | **Cascade**または**None**。 **Cascade**を指定すると、プリンシパルエンティティ型が削除されるときに依存エンティティ型が削除されます。 存在**しない場合、** プリンシパルエンティティ型が削除されても依存エンティティ型は削除されません。 |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**Association**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **CustomerOrders**の関連付けを定義する**association**要素を示しています。 **OnDelete**要素は、特定の**顧客**に関連付けられているすべての**注文**が ObjectContext に読み込まれていることを示します。これは、**顧客**が削除されると削除されます。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a>Parameter 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**Parameter**要素は、FunctionImport 要素または Function 要素の子にすることができます。

### <a name="functionimport-element-application"></a>FunctionImport 要素のアプリケーション

**パラメーター**要素 ( **FunctionImport**要素の子として) は、CSDL で宣言された関数インポートの入力パラメーターと出力パラメーターを定義するために使用されます。

**Parameter**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation (0 個または1個の要素を使用できます)
-   Annotation 要素 (0 個以上の要素を使用できます)

#### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Parameter**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**       | はい         | パラメーターの名前。                                                                                                                                                                                                      |
| **Type**       | はい         | パラメーターの型。 この値には、 **EDMSimpleType** か、モデルのスコープ内にある複合型を指定する必要があります。                                                                                                             |
| **モード**       | いいえ          | **In**、 **Out**、または**InOut**は、パラメーターが入力パラメーター、出力パラメーター、または入力/出力パラメーターのどちらであるかによって異なります。                                                                                                                |
| **MaxLength**  | いいえ          | パラメーターの許容される最大長                                                                                                                                                                                    |
| **[精度]**  | いいえ          | パラメーターの説明                                                                                                                                                                                                 |
| **スケール**      | いいえ          | パラメーターの小数点以下桁数                                                                                                                                                                                                     |
| **SRID**       | いいえ          | 空間システム参照識別子。 空間型のパラメーターに対してのみ有効です。 詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。 |

 

> [!NOTE]
> **パラメーター**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

#### <a name="example"></a>例

次の例は、1つの**Parameter**子要素を持つ**FunctionImport**要素を示しています。 関数は、1 つの入力パラメーターを受け取り、エンティティ型のコレクションを返します。

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a>Function 要素のアプリケーション

**Parameter**要素 ( **Function**要素の子として) は、概念モデルで定義または宣言されている関数のパラメーターを定義します。

**Parameter**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation (0 個または1個の要素)
-   CollectionType (0 個または1個の要素)
-   ReferenceType (0 個または1個の要素)
-   RowType (0 個または1個の要素)

> [!NOTE]
> **CollectionType**、 **ReferenceType**、または**RowType**要素のいずれか1つだけを**Property**要素の子要素にすることができます。

 

-   Annotation 要素 (0 個以上の要素を使用できます)

> [!NOTE]
> Annotation 要素は、それ以外のすべての子要素より後に指定する必要があります。 Annotation 要素は、CSDL v2 以降でのみ使用できます。

 

#### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Parameter**要素に適用できる属性について説明します。

| 属性名   | 必須 | 値                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**         | はい         | パラメーターの名前。                                                                                                                                                                                                      |
| **Type**         | いいえ          | パラメーターの型。 パラメーターには次のいずれかの型 (またはこれらの型のコレクション) を使用できます。 <br/> **EdmSimpleType** <br/> エンティティ型 <br/> 複合型 <br/> 行型 <br/> 参照型                             |
| **NULL 値の使用**     | いいえ          | プロパティが**null**値を持つことができるかどうかによって、 **True** (既定値) または**False** 。                                                                                                                          |
| **既定** | いいえ          | プロパティの既定値です。                                                                                                                                                                                              |
| **MaxLength**    | いいえ          | プロパティ値の最大長。                                                                                                                                                                                       |
| **FixedLength**  | いいえ          | プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。                                                                                                                          |
| **[精度]**    | いいえ          | プロパティ値の有効桁数。                                                                                                                                                                                            |
| **スケール**        | いいえ          | プロパティ値の小数点以下桁数。                                                                                                                                                                                                |
| **SRID**         | いいえ          | 空間システム参照識別子。 空間型のプロパティに対してのみ有効です。 詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。 |
| **Unicode**      | いいえ          | プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。                                                                                                                               |
| **Collation**    | いいえ          | データ ソースで使用される照合順序を指定する文字列。                                                                                                                                                   |

 

> [!NOTE]
> **パラメーター**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

#### <a name="example"></a>例

次の例は、1つの**parameter**子要素を使用して関数パラメーターを定義する**関数**要素を示しています。

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a>Principal 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**principal**要素は、参照制約のプリンシパル end を定義する、参照制約要素の子要素です。 参照整合性の**制約要素は、リレーショナル**データベースの参照整合性制約に似た機能を定義します。 データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティは別のエンティティ型のエンティティ キーを参照できます。 参照されるエンティティ型は、制約の*プリンシパル end*と呼ばれます。 プリンシパル end を参照するエンティティ型は、制約の*依存 end*と呼ばれます。 **Propertyref**要素は、依存 end によって参照されるキーを指定するために使用されます。

**Principal**要素には、次の子要素を含めることができます (順に記載されています)。

-   PropertyRef (1 つ以上の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Principal**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **ロール**       | はい         | アソシエーションのプリンシパル End 上のエンティティ型の名前。 |

 

> [!NOTE]
> **Principal**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、 **publishedby**関連付けの定義の一部である **、オブジェクトの場所を示し**ます。 参照に関する制約のプリンシパル end は、**パブリッシャー**エンティティ型の**Id**プロパティによって構成されます。

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="property-element-csdl"></a>Property 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**Property**要素は、EntityType 要素、ComplexType 要素、または RowType 要素の子にすることができます。

### <a name="entitytype-and-complextype-element-applications"></a>EntityType 要素と ComplexType 要素のアプリケーション

**プロパティ**要素 ( **EntityType**または**ComplexType**要素の子として) は、エンティティ型インスタンスまたは複合型インスタンスに格納されるデータの構造と特性を定義します。 概念モデルのプロパティは、クラスで定義されるプロパティに似ています。 クラスのプロパティがクラスの構造を定義し、オブジェクトに関する情報を伝達するのと同様に、概念モデルのプロパティはエンティティ型の構造を定義し、エンティティ型のインスタンスに関する情報を伝達します。

**Property**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation 要素 (0 個または1個の要素が許可されています)
-   Annotation 要素 (0 個以上の要素を使用できます)

次のファセットは、**プロパティ**要素に適用できます。 **Nullable**、 **DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**、 **Collation**、 **ConcurrencyMode**。 ファセットは、プロパティ値をデータ ストアに格納する方法に関する情報を提供する XML 属性です。

> [!NOTE]
> ファセットは、 **Edmsimpletype**型のプロパティにのみ適用できます。

 

#### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Property**要素に適用できる属性について説明します。

| 属性名                                                         | 必須 | 値                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**                                                               | はい         | プロパティの名前。                                                                                                                                                                                                       |
| **Type**                                                               | はい         | プロパティ値の型です。 プロパティ値の型は、 **EDMSimpleType** か、モデルのスコープ内にある (完全修飾名で表された) 複合型を指定する必要があります。                                                 |
| **NULL 値の使用**                                                           | いいえ          | プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または <strong>False</strong>。 <br/> [!NOTE]                                                                                                   |
| CSDL v1 の > 複合型プロパティには、`Nullable="False"`が必要です。 |             |                                                                                                                                                                                                                                 |
| **既定**                                                       | いいえ          | プロパティの既定値です。                                                                                                                                                                                              |
| **MaxLength**                                                          | いいえ          | プロパティ値の最大長。                                                                                                                                                                                       |
| **FixedLength**                                                        | いいえ          | プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。                                                                                                                          |
| **[精度]**                                                          | いいえ          | プロパティ値の有効桁数。                                                                                                                                                                                            |
| **スケール**                                                              | いいえ          | プロパティ値の小数点以下桁数。                                                                                                                                                                                                |
| **SRID**                                                               | いいえ          | 空間システム参照識別子。 空間型のプロパティに対してのみ有効です。 詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。 |
| **Unicode**                                                            | いいえ          | プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。                                                                                                                               |
| **Collation**                                                          | いいえ          | データ ソースで使用される照合順序を指定する文字列。                                                                                                                                                   |
| **ConcurrencyMode**                                                    | いいえ          | **None** (既定値) または **Fixed**。 値が **Fixed**に設定されている場合、プロパティ値はオプティミスティック コンカレンシーチェックで使用されます。                                                                                  |

 

> [!NOTE]
> **プロパティ**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

#### <a name="example"></a>例

次の例は、3つの**Property**要素を持つ**EntityType**要素を示しています。

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

次の例は、5つの**Property**要素を持つ**ComplexType**要素を示しています。

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a>RowType 要素のアプリケーション

**RowType**要素の子として使用される**プロパティ**要素は、モデル定義関数に渡される、またはモデル定義関数から返されるデータの構造と特性を定義します。  

**Property**要素には、次の子要素のうち1つだけを含めることができます。

-   CollectionType
-   ReferenceType
-   RowType

**Property**要素は、任意の数の子注釈要素を持つことができます。

> [!NOTE]
> Annotation 要素は、CSDL v2 以降でのみ使用できます。

 

#### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Property**要素に適用できる属性について説明します。

| 属性名                                                     | 必須 | 値                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**                                                           | はい         | プロパティの名前。                                                                                                                                                                                                       |
| **Type**                                                           | はい         | プロパティ値の型です。                                                                                                                                                                                                 |
| **NULL 値の使用**                                                       | いいえ          | プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または **False**。 <br/> [!NOTE]                                                                                                                |
| CSDL v1 で > 複合型プロパティには、`Nullable="False"`が必要です。 |             |                                                                                                                                                                                                                                 |
| **既定**                                                   | いいえ          | プロパティの既定値です。                                                                                                                                                                                              |
| **MaxLength**                                                      | いいえ          | プロパティ値の最大長。                                                                                                                                                                                       |
| **FixedLength**                                                    | いいえ          | プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。                                                                                                                          |
| **[精度]**                                                      | いいえ          | プロパティ値の有効桁数。                                                                                                                                                                                            |
| **スケール**                                                          | いいえ          | プロパティ値の小数点以下桁数。                                                                                                                                                                                                |
| **SRID**                                                           | いいえ          | 空間システム参照識別子。 空間型のプロパティに対してのみ有効です。 詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。 |
| **Unicode**                                                        | いいえ          | プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。                                                                                                                               |
| **Collation**                                                      | いいえ          | データ ソースで使用される照合順序を指定する文字列。                                                                                                                                                   |

 

> [!NOTE]
> **プロパティ**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

#### <a name="example"></a>例

次の例は、モデル定義関数の戻り値の型の形状を定義するために使用される**プロパティ**要素を示しています。

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

 

## <a name="propertyref-element-csdl"></a>PropertyRef 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**Propertyref**要素は、プロパティが次のいずれかのロールを実行することを示すために、エンティティ型のプロパティを参照します。

-   エンティティのキー (ID を指定するエンティティ型のプロパティまたは一連のプロパティ) の一部。 1つ以上の**Propertyref**要素を使用して、エンティティキーを定義できます。
-   参照制約の依存 End または プリンシパル End。

**Propertyref**要素には、子要素として annotation 要素 (0 個以上) のみを含めることができます。

> [!NOTE]
> Annotation 要素は、CSDL v2 以降でのみ使用できます。

 

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Propertyref**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                |
|:---------------|:------------|:-------------------------------------|
| **名前**       | はい         | 参照されているプロパティの名前。 |

 

> [!NOTE]
> **Propertyref**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、エンティティ型 (**Book**) を定義しています。 エンティティキーは、エンティティ型の**ISBN**プロパティを参照することによって定義されます。

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

次の例では、2つの**Propertyref**要素を使用して、2つのプロパティ (**Id**と**PublisherId**) が参照に関する制約のプリンシパルと依存 end であることを示しています。

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="referencetype-element-csdl"></a>ReferenceType 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**ReferenceType**要素は、エンティティ型への参照を指定します。 **ReferenceType**要素は、次の要素の子にすることができます。

-   ReturnType (関数)
-   パラメーター
-   CollectionType

**ReferenceType**要素は、関数のパラメーターまたは戻り値の型を定義するときに使用されます。

**ReferenceType**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation (0 個または1個の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>該当する属性

次の表は、 **ReferenceType**要素に適用できる属性を示しています。

| 属性名 | 必須 | 値                                         |
|:---------------|:------------|:----------------------------------------------|
| **Type**       | はい         | 参照先エンティティ型の名前。 |

 

> [!NOTE]
> **ReferenceType**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **Person**エンティティ型への参照を受け入れるモデル定義関数で、 **Parameter**要素の子として使用される**ReferenceType**要素を示しています。

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
   <Parameter Name="instructor">
     <ReferenceType Type="SchoolModel.Person" />
   </Parameter>
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

次の例は、 **Person**エンティティ型への参照を返すモデル定義関数で、 **ReturnType** (Function) 要素の子として使用される**ReferenceType**要素を示しています。

``` xml
 <Function Name="GetPersonReference">
     <Parameter Name="p" Type="SchoolModel.Person" />
     <ReturnType>
         <ReferenceType Type="SchoolModel.Person" />
     </ReturnType>
     <DefiningExpression>
           REF(p)
     </DefiningExpression>
 </Function>
```
 

 

## <a name="referentialconstraint-element-csdl"></a>ReferentialConstraint 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の参照整合性制約要素は、リレーショナルデータベースの参照整合性制約に似た機能を**定義します**。 データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティは別のエンティティ型のエンティティ キーを参照できます。 参照されるエンティティ型は、制約の*プリンシパル end*と呼ばれます。 プリンシパル end を参照するエンティティ型は、制約の*依存 end*と呼ばれます。

あるエンティティ型で公開されている外部キーが別のエンティティ型のプロパティを参照している場合、参照要素は2つのエンティティ型の間の関連付けを**定義します**。 参照要素**は2つのエンティティ**型の関連付けに関する情報を提供するため、対応する AssociationSetMapping 要素はマッピングスキーマ言語 (MSL) には必要ありません。 外部キーが公開されていない2つのエンティティ型間のアソシエーションには、関連情報をデータソースにマップするために、対応する**AssociationSetMapping**要素が必要です。

エンティティ型で外部キーが公開されていない場合**は、エンティティ**型と別のエンティティ型との間の主キーと主キーの制約のみを指定できます。

要素には、次の子**要素を含める**ことができます (順に表示されます)。

-   Documentation (0 個または1個の要素)
-   Principal (1 つの要素)
-   Dependent (1 個の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>該当する属性

この**要素には、** 任意の数の annotation 属性 (カスタム XML 属性) を指定できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例では、 **publishedby**関連付けの定義の一部として使用されている **、オブジェクトの**場所を示します。

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="returntype-function-element-csdl"></a>ReturnType (Function) 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**ReturnType** (function) 要素は、function 要素で定義されている関数の戻り値の型を指定します。 関数の戻り値の型は、 **ReturnType**属性と共に指定することもできます。

戻り値の型には、 **Edmsimpletype**、エンティティ型、複合型、行型、参照型、またはこれらの型のいずれかのコレクションを指定できます。

関数の戻り値の型は、 **ReturnType** (function) 要素の**type**属性か、次のいずれかの子要素を使用して指定できます。

-   CollectionType
-   ReferenceType
-   RowType

> [!NOTE]
> **ReturnType** (function) 要素の**type**属性と子要素の両方を持つ関数の戻り値の型を指定した場合、モデルは検証されません。

 

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **ReturnType** (Function) 要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                              |
|:---------------|:------------|:-----------------------------------|
| **ReturnType** | いいえ          | 関数の戻り値の型。 |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**ReturnType** (Function) 要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、**関数**要素を使用して、書籍が印刷された年数を返す関数を定義します。 戻り値の型は、 **ReturnType** (Function) 要素の**type**属性によって指定されていることに注意してください。

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a>ReturnType (FunctionImport) 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**ReturnType** (FunctionImport) 要素は、FunctionImport 要素で定義されている関数の戻り値の型を指定します。 関数の戻り値の型は、 **ReturnType**属性と共に指定することもできます。

戻り値の型は、エンティティ型、複合型、または**Edmsimpletype**の任意のコレクションにすることができます。

関数の戻り値の型は、 **ReturnType** (FunctionImport) 要素の**type**属性と共に指定されます。

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **ReturnType** (FunctionImport) 要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**       | いいえ          | 関数が返す型。 値は、ComplexType、EntityType、または EDMSimpleType のコレクションである必要があります。                                                                                      |
| **EntitySet**  | いいえ          | 関数がエンティティ型のコレクションを返す場合、 **EntitySet**の値は、コレクションが属するエンティティセットである必要があります。 そうでない場合は、 **EntitySet** 属性を使用しないでください。 |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**ReturnType** (FunctionImport) 要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、書籍と出版社を返す**FunctionImport**を使用します。 関数は2つの結果セットを返すため、2つの**ReturnType** (FunctionImport) 要素が指定されていることに注意してください。

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a>RowType 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**RowType**要素は、概念モデルで定義されている関数のパラメーターまたは戻り値の型として名前のない構造体を定義します。

**RowType**要素は、次の要素の子にすることができます。

-   CollectionType
-   パラメーター
-   ReturnType (関数)

**RowType**要素には、次の子要素を含めることができます (一覧の順序)。

-   Property (1 個以上)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

**RowType**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、 **CollectionType**要素を使用して、 **RowType**要素で指定された行のコレクションを返すことを指定するモデル定義関数を示しています。

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```

## <a name="schema-element-csdl"></a>Schema 要素 (CSDL)

**スキーマ**要素は、概念モデル定義のルート要素です。 概念モデルを構成するオブジェクト、関数、およびコンテナーの定義を格納します。

**Schema**要素には、次の子要素を0個以上含めることができます。

-   使用
-   EntityContainer 要素
-   EntityType
-   EnumType
-   アソシエーション
-   ComplexType 要素
-   Function

**スキーマ**要素には、0個または1個の Annotation 要素を含めることができます。

> [!NOTE]
> **Function**要素と annotation 要素は、CSDL v2 以降でのみ使用できます。

 

**Schema**要素は、**名前空間**属性を使用して、概念モデルのエンティティ型、複合型、およびアソシエーションオブジェクトの名前空間を定義します。 1 つの名前空間内で 2 つのオブジェクトが同じ名前を持つことはできません。 名前空間は、複数の**スキーマ**要素と複数の .csdl ファイルにまたがることができます。

概念モデルの名前空間は、**スキーマ**要素の XML 名前空間とは異なります。 概念モデルの名前空間 (**名前空間**属性で定義) は、エンティティ型、複合型、およびアソシエーション型の論理コンテナーです。 **Schema**要素の XML 名前空間 ( **xmlns**属性で示されます) は、 **schema**要素の子要素と属性の既定の名前空間です。 https://schemas.microsoft.com/ado/YYYY/MM/edm 形式の XML 名前空間 (YYYY と MM はそれぞれ年と月を表します) は CSDL 用に予約されています。 カスタム要素と属性は、このフォームがある名前空間に存在することはできません。

### <a name="applicable-attributes"></a>該当する属性

次の表では、**スキーマ**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Namespace**  | はい         | 概念モデルの名前空間。 **名前空間**属性の値は、型の完全修飾名を形成するために使用されます。 たとえば、 *Customer*という名前の**entitytype**が Simple. example. Model 名前空間にある場合、 **entitytype**の完全修飾名は simpleexamplemodel.customer です。 <br/> 次の文字列は、 **Namespace**属性の値として使用できません: **System**、 **Transient**、または**Edm**。 **Namespace**属性の値を、SSDL Schema 要素の**namespace**属性の値と同じにすることはできません。 |
| **エイリアス**      | いいえ          | 名前空間の名前の代わりに使用される識別子。 たとえば、 *Customer*という名前の**entitytype**が Simple. example. model 名前空間にあり、 **Alias**属性の値が*model*の場合、 **entitytype**の完全修飾名として model. Customer を使用できます。                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> **スキーマ**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

**EntityContainer**要素、2つの**EntityType**要素、および1つの**Association**要素を含む**スキーマ**要素の例を次に示します。

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
      xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
      xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
       Namespace="ExampleModel" Alias="Self">
         <EntityContainer Name="ExampleModelContainer">
           <EntitySet Name="Customers"
                      EntityType="ExampleModel.Customer" />
           <EntitySet Name="Orders" EntityType="ExampleModel.Order" />
           <AssociationSet
                       Name="CustomerOrder"
                       Association="ExampleModel.CustomerOrders">
             <End Role="Customer" EntitySet="Customers" />
             <End Role="Order" EntitySet="Orders" />
           </AssociationSet>
         </EntityContainer>
         <EntityType Name="Customer">
           <Key>
             <PropertyRef Name="CustomerId" />
           </Key>
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
           <Property Type="String" Name="Name" Nullable="false" />
           <NavigationProperty
                    Name="Orders"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Customer" ToRole="Order" />
         </EntityType>
         <EntityType Name="Order">
           <Key>
             <PropertyRef Name="OrderId" />
           </Key>
           <Property Type="Int32" Name="OrderId" Nullable="false" />
           <Property Type="Int32" Name="ProductId" Nullable="false" />
           <Property Type="Int32" Name="Quantity" Nullable="false" />
           <NavigationProperty
                    Name="Customer"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Order" ToRole="Customer" />
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
         </EntityType>
         <Association Name="CustomerOrders">
           <End Type="ExampleModel.Customer"
                Role="Customer" Multiplicity="1" />
           <End Type="ExampleModel.Order"
                Role="Order" Multiplicity="*" />
           <ReferentialConstraint>
             <Principal Role="Customer">
               <PropertyRef Name="CustomerId" />
             </Principal>
             <Dependent Role="Order">
               <PropertyRef Name="CustomerId" />
             </Dependent>
           </ReferentialConstraint>
         </Association>
       </Schema>
```
 

 

## <a name="typeref-element-csdl"></a>TypeRef 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**TypeRef**要素は、既存の名前付きの型への参照を提供します。 **TypeRef**要素は CollectionType 要素の子にすることができます。これは、関数がパラメーターまたは戻り値の型としてコレクションを持つことを指定するために使用されます。

**TypeRef**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation (0 個または1個の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **TypeRef**要素に適用できる属性について説明します。 **DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**、および**Collation**属性は、 **edmsimpletypes**にのみ適用されることに注意してください。

| 属性名                                                     | 必須 | 値                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**                                                           | いいえ          | 参照先の型の名前。                                                                                                                                                                                          |
| **NULL 値の使用**                                                       | いいえ          | プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または **False**。 <br/> [!NOTE]                                                                                                                |
| CSDL v1 で > 複合型プロパティには、`Nullable="False"`が必要です。 |             |                                                                                                                                                                                                                                 |
| **既定**                                                   | いいえ          | プロパティの既定値です。                                                                                                                                                                                              |
| **MaxLength**                                                      | いいえ          | プロパティ値の最大長。                                                                                                                                                                                       |
| **FixedLength**                                                    | いいえ          | プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。                                                                                                                          |
| **[精度]**                                                      | いいえ          | プロパティ値の有効桁数。                                                                                                                                                                                            |
| **スケール**                                                          | いいえ          | プロパティ値の小数点以下桁数。                                                                                                                                                                                                |
| **SRID**                                                           | いいえ          | 空間システム参照識別子。 空間型のプロパティに対してのみ有効です。 詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。 |
| **Unicode**                                                        | いいえ          | プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。                                                                                                                               |
| **Collation**                                                      | いいえ          | データ ソースで使用される照合順序を指定する文字列。                                                                                                                                                   |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**CollectionType**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **TypeRef**要素 ( **CollectionType**要素の子) を使用して、関数が**Department**エンティティ型のコレクションを受け入れることを指定するモデル定義関数を示しています。

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="using-element-csdl"></a>Using 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の**Using**要素は、別の名前空間に存在する概念モデルの内容をインポートします。 **名前空間**属性の値を設定することにより、別の概念モデルで定義されているエンティティ型、複合型、およびアソシエーション型を参照できます。 複数の**Using**要素を**Schema**要素の子にすることができます。

> [!NOTE]
> CSDL の**using**要素は、プログラミング言語での**using**ステートメントとまったく同じようには機能しません。 プログラミング言語で**using**ステートメントを使用して名前空間をインポートすると、元の名前空間のオブジェクトには影響しません。 CSDL では、インポートされた名前空間に、元の名前空間にあるエンティティ型から派生したエンティティ型が含まれている場合があります。 これは、元の名前空間で宣言されたエンティティ セットに影響を及ぼすことがあります。

 

**Using**要素には、次の子要素を含めることができます。

-   Documentation (0 個または1個の要素を使用できます)
-   Annotation 要素 (0 個以上の要素を使用できます)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Using**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Namespace**  | はい         | インポートされる名前空間の名前。                                                                                                                                                |
| **エイリアス**      | はい         | 名前空間の名前の代わりに使用される識別子。 この属性は必須ですが、オブジェクト名を修飾するために名前空間名の代わりに使用することは必須ではありません。 |

 

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**Using**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、他の場所で定義されている名前空間をインポートするために使用される**Using**要素を示しています。 表示される**スキーマ**要素の名前空間は `BooksModel`であることに注意してください。 `Publisher`**EntityType**の `Address` プロパティは、`ExtendedBooksModel` 名前空間で定義されている複合型です ( **Using**要素と共にインポートされます)。

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
           xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
           xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
           Namespace="BooksModel" Alias="Self">

     <Using Namespace="BooksModel.Extended" Alias="BMExt" />

 <EntityContainer Name="BooksContainer" >
       <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
     </EntityContainer>

 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BMExt.Address" Name="Address" Nullable="false" />
     </EntityType>

 </Schema>
```
 

 

## <a name="annotation-attributes-csdl"></a>annotation 属性 (CSDL)

概念スキーマ定義言語 (CSDL) の annotation 属性は、概念モデルのカスタム XML 属性です。 有効な XML 構造であることに加え、annotation 属性は次の条件を満たしている必要があります。

-   annotation 属性は、CSDL 用に予約された XML 名前空間に存在しない。
-   複数の annotation 属性を特定の 1 つの CSDL 要素に適用することができる。
-   2 つの任意の annotation 属性の完全修飾名が同じではない。

annotation 属性は、概念モデルの要素に関する追加のメタデータを提供するために使用できます。 Annotation 要素に含まれるメタデータには、実行時に、system.string 名前空間のクラスを使用してアクセスできます。

### <a name="example"></a>例

Annotation 属性 (**CustomAttribute**) を持つ**EntityType**要素の例を次に示します。 また、エンティティ型の要素に適用される annotation 要素も示しています。

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

次のコードは、annotation 属性でメタデータを取得し、コンソールに書き込みます。

``` xml
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomAttribute"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomAttribute"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

前のコードでは、`School.csdl` ファイルがプロジェクトの出力ディレクトリにあり、プロジェクトに次の `Imports` および `Using` ステートメントが追加されていることを前提としています。

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a>Annotation 要素 (CSDL)

概念スキーマ定義言語 (CSDL) の annotation 要素は、概念モデルのカスタム XML 要素です。 有効な XML 構造が必要であることに加え、annotation 要素は次の条件も満たしている必要があります。

-   annotation 要素は、CSDL 用に予約された XML 名前空間内に存在できません。
-   複数の annotation 要素を特定の CSDL 要素の子にすることができます。
-   2 つの annotation 要素の完全修飾名を同じにすることはできません。
-   annotation 要素は、特定の CSDL 要素のその他すべての子要素より後に指定する必要があります。

annotation 要素は、概念モデルの要素に関する追加のメタデータを提供するために使用できます。 .NET Framework version 4 以降では、annotation 要素に含まれるメタデータに、実行時に、system.string 名前空間のクラスを使用してアクセスできます。

### <a name="example"></a>例

Annotation 要素 (**customelement**) を持つ**EntityType**要素の例を次に示します。 また、エンティティ型の要素に適用される annotation 属性も示しています。

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

次のコードは、annotation 要素内のメタデータを取得してコンソールに出力します。

``` csharp
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomElement"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomElement"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

前のコードでは、School.csdl ファイルがプロジェクトの出力ディレクトリにあり、プロジェクトに次の `Imports` および `Using` ステートメントが追加されていることを前提としています。

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a>概念モデルの型 (CSDL)

概念スキーマ定義言語 (CSDL) では、概念モデルのプロパティを定義する**Edmsimpletypes**と呼ばれる一連の抽象プリミティブデータ型をサポートしています。 **Edmsimpletypes**は、ストレージ環境またはホスト環境でサポートされているプリミティブデータ型のプロキシです。

下の表は、CSDL でサポートされるプリミティブ データ型の一覧を示します。 この表には、各**Edmsimpletype**に適用できるファセットの一覧も含まれています。

| EDMSimpleType                    | Description                                                | 使用できるファセット                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| **Edm. Binary**                   | バイナリ データを格納します。                                      | MaxLength、FixedLength、Nullable、Default                                |
| **Edm.Boolean**                  | **True**または**false**の値が含まれています。                  | Nullable、Default                                                        |
| **Edm. Byte**                     | 符号なし 8 ビット整数値を格納します。                  | Precision、Nullable、Default                                             |
| **Edm. DateTime**                 | 日時を表します。                                | Precision、Nullable、Default                                             |
| **Edm.DateTimeOffset**           | GMT からのオフセット値の日時 (分単位) が含まれます。 | Precision、Nullable、Default                                             |
| **Edm. Decimal**                  | 有効桁数と小数点以下桁数が固定長の数値を格納します。   | Precision、Nullable、Default                                             |
| **Edm.Double**                   | 15桁の有効桁数を持つ浮動小数点数を格納します   | Precision、Nullable、Default                                             |
| **Edm. Float**                    | 7 桁の有効桁数を持つ浮動小数点数を格納します。   | Precision、Nullable、Default                                             |
| **Edm. Guid**                     | 16 バイトの一意識別子を格納します。                      | Precision、Nullable、Default                                             |
| **Edm. Int16**                    | 符号付き 16 ビット整数値を格納します。                    | Precision、Nullable、Default                                             |
| **Edm.Int32**                    | 符号付き 32 ビット整数値を格納します。                    | Precision、Nullable、Default                                             |
| **Edm.Int64**                    | 符号付き 64 ビット整数値を格納します。                    | Precision、Nullable、Default                                             |
| **Edm. SByte**                    | 符号付き 8 ビット整数値を格納します。                     | Precision、Nullable、Default                                             |
| **Edm.String**                   | 文字データを格納します。                                   | Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default |
| **Edm. Time**                     | 時刻を格納します。                                    | Precision、Nullable、Default                                             |
| **Edm. Geography**                |                                                            | Nullable、Default、SRID                                                  |
| **Edm.GeographyPoint**           |                                                            | Nullable、Default、SRID                                                  |
| **GeographyLineString**      |                                                            | Nullable、Default、SRID                                                  |
| **返される geographypolygon**         |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeographyMultiPoint**      |                                                            | Nullable、Default、SRID                                                  |
| **返される geographymultilinestring** |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeographyMultiPolygon**    |                                                            | Nullable、Default、SRID                                                  |
| **Edm. GeographyCollection**      |                                                            | Nullable、Default、SRID                                                  |
| **Edm. Geometry**                 |                                                            | Nullable、Default、SRID                                                  |
| **返される geometrypoint**            |                                                            | Nullable、Default、SRID                                                  |
| **返される geometrylinestring**       |                                                            | Nullable、Default、SRID                                                  |
| **返される geometrypolygon**          |                                                            | Nullable、Default、SRID                                                  |
| **返される geometrymultipoint**       |                                                            | Nullable、Default、SRID                                                  |
| **返される geometrymultilinestring**  |                                                            | Nullable、Default、SRID                                                  |
| **返される geometrymultipolygon**     |                                                            | Nullable、Default、SRID                                                  |
| **GeometryCollection**       |                                                            | Nullable、Default、SRID                                                  |

## <a name="facets-csdl"></a>ファセット (CSDL)

概念スキーマ定義言語 (CSDL) のファセットは、エンティティ型と複合型のプロパティに対する制約を表します。 ファセットは次の CSDL 要素で XML 属性として使用されます。

-   プロパティ
-   TypeRef
-   パラメーター

次の表は、CSDL でサポートされるファセットについて説明しています。 いずれのファセットもオプションです。 次に示すいくつかのファセットは、概念モデルからデータベースを生成するときに Entity Framework によって使用されます。

> [!NOTE]
> 概念モデルのデータ型の詳細については、「概念モデルの型 (CSDL)」を参照してください。

| ファセット               | Description                                                                                                                                                                                                                                                   | 適用対象                                                                                                                                                                                                                                                                                                                                                                           | データベースの生成に使用 | ランタイムで使用 |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| **Collation**       | プロパティの値に対して比較と順序付け操作を行うときに使用する照合シーケンス (または並べ替え順序) を指定します。                                                                                                               | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | はい                              | いいえ                  |
| **ConcurrencyMode** | プロパティの値をオプティミスティック コンカレンシー チェックに使用することを指定します。                                                                                                                                                                    | すべての**Edmsimpletype**プロパティ                                                                                                                                                                                                                                                                                                                                                     | いいえ                               | はい                 |
| **[Default]**         | インスタンス化で値が指定されない場合のプロパティの既定値を指定します。                                                                                                                                                                       | すべての**Edmsimpletype**プロパティ                                                                                                                                                                                                                                                                                                                                                     | はい                              | はい                 |
| **FixedLength**     | プロパティ値の長さを可変とすることができるかどうかを指定します。                                                                                                                                                                                                  | **Edm. Binary**, **edm. String**                                                                                                                                                                                                                                                                                                                                                       | はい                              | いいえ                  |
| **MaxLength**       | プロパティ値の最大長を指定します。                                                                                                                                                                                                           | **Edm. Binary**, **edm. String**                                                                                                                                                                                                                                                                                                                                                       | はい                              | いいえ                  |
| **NULL 値の使用**        | プロパティが**null**値を持つことができるかどうかを指定します。                                                                                                                                                                                                     | すべての**Edmsimpletype**プロパティ                                                                                                                                                                                                                                                                                                                                                     | はい                              | はい                 |
| **[精度]**       | **Decimal**型のプロパティの場合、プロパティ値が持つことができる桁数を指定します。 **Time**、 **DateTime**、および**DateTimeOffset**型のプロパティの場合、プロパティ値の秒の小数部の桁数を指定します。 | **Edm. DateTime**, **edm. DateTimeOffset**, edm. **Decimal**, **edm. Time**                                                                                                                                                                                                                                                                                                              | はい                              | いいえ                  |
| **スケール**           | プロパティ値の小数点の右側の桁数を指定します。                                                                                                                                                                      | **Edm. Decimal**                                                                                                                                                                                                                                                                                                                                                                      | はい                              | いいえ                  |
| **SRID**            | 空間システム参照システム ID を指定します。 詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。                                                              | **GeographyLineString、edm. GeographyPoint、返される geographypolygon、edm. Geographypoint、返される geographymultilinestring、edm. GeographyMultiPolygon、edm. Geographypoint、edm. Geometry、返される geometrypoint,、および,、およびです。返される geometrylinestring、返される geometrypolygon、返される geometrymultipoint、edm. 返される geometrymultilinestring、、返される geometrymultipolygon、Edm. GeometryCollection。** | いいえ                               | はい                 |
| **Unicode**         | プロパティ値を Unicode として保存するかどうかを指定します。                                                                                                                                                                                                    | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | はい                              | はい                 |

>[!NOTE]
> 概念モデルからデータベースを生成する場合、データベース生成ウィザードでは、**プロパティ**要素が次の名前空間にある**場合、その属性の**値が認識されます: https://schemas.microsoft.com/ado/2009/02/edm/annotation。 属性でサポートされている値は、 **id**と**計算**です。 **Id**値を指定すると、データベースで生成された id 値を持つデータベース列が生成されます。 **計算**値を指定すると、データベースで計算された値を持つ列が生成されます。

### <a name="example"></a>例

エンティティ型のプロパティに適用されるファセットの例を次に示します。

``` xml
 <EntityType Name="Product">
   <Key>
     <PropertyRef Name="ProductId" />
   </Key>
   <Property Type="Int32"
             Name="ProductId" Nullable="false"
             a:StoreGeneratedPattern="Identity"
    xmlns:a="https://schemas.microsoft.com/ado/2009/02/edm/annotation" />
   <Property Type="String"
             Name="ProductName"
             Nullable="false"
             MaxLength="50" />
   <Property Type="String"
             Name="Location"
             Nullable="true"
             MaxLength="25" />
 </EntityType>
```
