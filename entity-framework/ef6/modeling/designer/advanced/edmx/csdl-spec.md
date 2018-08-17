---
title: CSDL の仕様の EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: c54255f4-253f-49eb-bec8-ad7927ac2fa3
caps.latest.revision: 3
ms.openlocfilehash: 0ece73a19fe7ea244905bccb728ab2a104c5179f
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122396"
---
# <a name="csdl-specification"></a>CSDL 仕様
概念スキーマ定義言語 (CSDL : Conceptual Schema Definition Language) は、XML ベースの言語であり、データ駆動型アプリケーションの概念モデルを構成するエンティティ、リレーションシップ、および関数を記述します。 Entity Framework や WCF Data Services では、この概念モデルを使用できます。 CSDL で記述されるメタデータは、エンティティおよびデータ ソースに概念モデルで定義されているリレーションシップにマップする Entity Framework によって使用されます。 詳細については、次を参照してください。 [SSDL 仕様](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)と[MSL 仕様](~/ef6/modeling/designer/advanced/edmx/msl-spec.md)します。

CSDL は、Entity Data Model の Entity Framework の実装です。

Entity Framework アプリケーションで概念モデルのメタデータが読み込まれた .csdl ファイル (CSDL で記述) から、System.Data.Metadata.Edm.EdmItemCollection のインスタンスに、アクセスできるメソッドを使用して、System.Data.Metadata.Edm.MetadataWorkspace クラスです。 Entity Framework では、概念モデルのメタデータを使用してデータ ソース固有のコマンドを概念モデルに対するクエリに変換します。

EF Designer では、デザイン時に .edmx ファイルの概念モデル情報を格納します。 ビルド時に、EF デザイナー情報を使用して .edmx ファイルの実行時に Entity Framework によって必要な .csdl ファイルを作成します。

CSDL のバージョンは、XML 名前空間で区別されます。

| CSDL のバージョン | XML Namespace                                |
|:-------------|:---------------------------------------------|
| CSDL v1      | http://schemas.microsoft.com/ado/2006/04/edm |
| CSDL v2      | http://schemas.microsoft.com/ado/2008/09/edm |
| CSDL v3      | http://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a>Association 要素 (CSDL)

**アソシエーション**要素が 2 つのエンティティ型間のリレーションシップを定義します。 アソシエーションでは、リレーションシップに関連するエンティティ型、およびリレーションシップの各 End におけるエンティティ型の数 (多重度と呼ばれる) を指定する必要があります。 値の 1 つ (1)、0 個または 1 (0..1)、または複数のアソシエーション end の多重度を持つことができます (\*)。 この情報は、2 つの子 End 要素で指定されます。

アソシエーションの一方の End にあるエンティティ型のインスタンスには、それらがエンティティ型で公開されている場合、ナビゲーション プロパティまたは外部キーからアクセスできます。

アプリケーション内で、アソシエーションのインスタンスは、エンティティ型のインスタンスの間の特定のアソシエーションを表します。 アソシエーション インスタンスは、アソシエーション セットに論理的にグループ化されます。

**アソシエーション**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   終了 (正確に 2 つの要素)
-   ReferentialConstraint (0 個または 1 つの要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、**アソシエーション**要素。

| 属性名 | 必須 | [値]                        |
|:---------------|:------------|:-----------------------------|
| **Name**       | [はい]         | アソシエーションの名前。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**アソシエーション**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、**アソシエーション**を定義する要素、 **CustomerOrders**関連付けの外部キーが公開されていないときに、**顧客**と**順序**エンティティ型。 **多重度**値はそれぞれ**エンド**の関連付けを示す多く**注文**と関連付けることができます、**顧客**が、1 つだけ**顧客**関連付けることができる、**順序**します。 さらに、 **OnDelete**要素には、ことを示しますすべて**注文**に関連する特定の**顧客**に読み込まれ、ObjectContext は削除されます場合、**顧客**は削除されます。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

次の例は、**アソシエーション**を定義する要素、 **CustomerOrders**アソシエーションで外部キーが公開されているときに、**顧客**と**順序**エンティティ型。 公開されている外部キーを持つ、エンティティ間のリレーションシップが管理されている、 **ReferentialConstraint**要素。 対応する AssociationSetMapping 要素は、このアソシエーションをデータ ソースにマップする必要はありません。

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

**AssociationSet**概念スキーマ定義言語 (CSDL) で要素は、同じ型のアソシエーション インスタンス用の論理コンテナーです。 アソシエーション セットは、複数のアソシエーション インスタンスを 1 つのデータ ソースにマップできるようグループ化する方法を指定します。  

**AssociationSet**要素は、(指定した順序で次の子要素を持つことができます。

-   ドキュメント (0 個または 1 つの要素を許可する場合)
-   終了 (2 つの要素が必要な場合)
-   Annotation 要素 (0 個以上の要素を許可する場合)

**アソシエーション**属性は、アソシエーション セットに含まれる関連付けの種類を指定します。 アソシエーション セットの両端を構成するエンティティ セットが正確に 2 つの子で指定された**エンド**要素。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **AssociationSet**要素。

| 属性名  | 必須 | [値]                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**        | [はい]         | エンティティ セットの名前。 値、**名前**属性の値と同じにすることはできません、**アソシエーション**属性。                                 |
| **関連付け** | [はい]         | 関連付けの完全修飾名。そのインスタンスは、アソシエーション セットに格納されます。 関連付けは、アソシエーション セットと同じ名前空間に存在する必要があります。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **AssociationSet**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **EntityContainer**要素を持つ**AssociationSet**要素。

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

**CollectionType**概念スキーマ定義言語 (CSDL) 内の要素のことを関数パラメーターまたは関数の戻り型がコレクションを指定します。 **CollectionType**要素 (関数) の ReturnType 要素またはパラメーターの要素の子にすることができます。 いずれかを使用してコレクションの型を指定することができます、**型**属性または子要素を次のいずれか。

-   **CollectionType**
-   ReferenceType
-   RowType
-   TypeRef

> [!NOTE]
> 両方のコレクションの型が指定されている場合、モデルは検証されません、**型**属性と子要素。

 

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **CollectionType**要素。 なお、 **DefaultValue**、 **MaxLength**、 **FixedLength**、**精度**、**スケール**、 **Unicode**、および**照合順序**のコレクションに適用可能な属性は**EDMSimpleTypes**します。

| 属性名                                                          | 必須 | [値]                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**                                                                | いいえ          | コレクションの型。                                                                                                                                                                                                      |
| **Null 許容型**                                                            | いいえ          | **True** (既定値) または**False**プロパティが null 値を持っているかどうかによって。 <br/> [!NOTE]                                                                                                                 |
| > 複合型プロパティがありますで CSDL v1`Nullable="False"`します。 |             |                                                                                                                                                                                                                                  |
| **DefaultValue**                                                        | いいえ          | プロパティの既定値。                                                                                                                                                                                               |
| **MaxLength**                                                           | いいえ          | プロパティ値の最大長。                                                                                                                                                                                        |
| **FixedLength**                                                         | いいえ          | **True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。                                                                                                                           |
| **精度**                                                           | いいえ          | プロパティ値の有効桁数。                                                                                                                                                                                             |
| **拡大縮小**                                                               | いいえ          | プロパティ値の小数点以下桁数。                                                                                                                                                                                                 |
| **SRID**                                                                | いいえ          | システムの空間参照識別子です。 空間型のプロパティに対してのみ有効です。   詳細については、次を参照してください [SRID](http://en.wikipedia.org/wiki/SRID) と[SRID (SQL Server) 。](https://msdn.microsoft.com/library/bb964707.aspx) |
| **Unicode**                                                             | いいえ          | **True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。                                                                                                                                |
| **照合順序**                                                           | いいえ          | データ ソースで使用する照合順序を指定する文字列。                                                                                                                                                    |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **CollectionType**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、モデル定義の関数を使用して、 **CollectionType**関数でのコレクションを返すことを指定する要素**Person**エンティティ型 (、に指定された**ElementType**属性)。

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
 

次の例では、使用するモデル定義関数、 **CollectionType**関数によって返される行のコレクションを指定する要素 (で指定されている、 **RowType**要素)。

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
 

次の例では、使用するモデル定義関数、 **CollectionType**要素のコレクションのパラメーターとして、関数が受け入れる指定を**部門**エンティティ型。

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

A **ComplexType**要素から成るデータ構造を定義する**EdmSimpleType**プロパティまたはその他の複合型。  複合型は、エンティティ型または別の複合型のプロパティにすることができます。 複合型は、データを定義するという点でエンティティ型に似ています。 ただし、複合型とエンティティ型の間にはいくつかの重要な違いがあります。

-   複合型には ID (またはキー) がないため、独立して存在することができません。 複合型は、エンティティ型またはその他の複合型のプロパティとしてのみ存在できます。
-   複合型は、アソシエーションに参加できません。 どちらも、アソシエーションの end が複合型を指定でき、したがって複合型のナビゲーション プロパティを定義することはできません。
-   複合型のスカラー プロパティはそれぞれ null に設定できますが、複合型のプロパティには null 値を指定できません。

A **ComplexType**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   プロパティ (0 個以上の要素)
-   Annotation 要素 (0 個以上の要素)

次の表に適用できる属性の説明、 **ComplexType**要素。

| 属性名                                                                                                 | 必須 | [値]                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name                                                                                                           | [はい]         | 複合型の名前。 複合型の名前は、モデルのスコープ内にある別の複合型、エンティティ型、またはアソシエーションの名前と同じにすることはできません。 |
| BaseType                                                                                                       | いいえ          | 定義される複合型の基本データ型である、別の複合型の名前。 <br/> [!NOTE]                                                                     |
| > この属性では、CSDL v1 で適用されません。 複合型の継承は、このバージョンではサポートされません。 |             |                                                                                                                                                                                     |
| 抽象                                                                                                       | いいえ          | **True**または**False** (既定値)、複合型が抽象型がかどうかによって異なります。 <br/> [!NOTE]                                                                  |
| > この属性では、CSDL v1 で適用されません。 このバージョンの複合型を抽象型にすることはできません。         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ComplexType**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、複合型、**アドレス**で、 **EdmSimpleType**プロパティ**StreetAddress**、**市区町村**、 **StateOrProvince**、**国**、および**PostalCode**します。

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

複合型を定義する**アドレス**(上)、エンティティ型のプロパティとしてする必要がありますを宣言するエンティティ型の定義のプロパティの型。 次の例は、**アドレス**プロパティとして複合型のエンティティ型である (**パブリッシャー**)。

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

**DefiningExpression**概念スキーマ定義言語 (CSDL) 内の要素には、概念モデルで関数を定義する Entity SQL 式が含まれています。  

> [!NOTE]
> 検証のため、 **DefiningExpression**要素は、任意のコンテンツを含めることができます。 ただし、Entity Framework は例外をスロー時、 **DefiningExpression**要素に有効な Entity SQL が含まれていません。

 

### <a name="applicable-attributes"></a>適用可能な属性

Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **DefiningExpression**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例では、 **DefiningExpression**書籍が発行されて以来、年数を返す関数を定義する要素。 コンテンツ、 **DefiningExpression** Entity SQL で要素が書き込まれます。

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a>Dependent 要素 (CSDL)

**依存**概念スキーマ定義言語 (CSDL) 内の要素は ReferentialConstraint 要素に子要素であり参照に関する制約の依存 end を定義します。 A **ReferentialConstraint**要素は、リレーショナル データベースで参照整合性制約と同様の機能を定義します。 データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティが別のエンティティ型のエンティティ キーを参照できます。 参照されるエンティティ型が呼び出される、*プリンシパル end*の制約。 プリンシパル end を参照するエンティティ型が呼び出される、*依存 end*の制約。 **PropertyRef**要素を使用すると、プリンシパル end を参照するキーを指定します。

**依存**要素は、(指定した順序で次の子要素を持つことができます。

-   PropertyRef (1 つまたは複数の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、**依存**要素。

| 属性名 | 必須 | [値]                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **ロール**       | [はい]         | アソシエーションの依存 End 上のエンティティ型の名前。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**依存**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **ReferentialConstraint**要素の定義の一部として使用されている、 **PublishedBy**アソシエーション。 **PublisherId**のプロパティ、**帳**参照に関する制約の依存 end のエンティティ型を使用します。

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

**ドキュメント**親要素で定義されているオブジェクトに関する情報を提供する概念スキーマ定義言語 (CSDL) 内の要素を使用できます。 .Edmx ファイルの場合、**ドキュメント**要素の内容 (エンティティ、アソシエーション、プロパティなど)、EF デザイナーのデザイン画面上のオブジェクトとして表示される要素の子である、**ドキュメント**要素は、Visual Studio に表示されます**プロパティ**ウィンドウで、オブジェクト。

**ドキュメント**要素は、(指定した順序で次の子要素を持つことができます。

-   **概要**: 親要素の簡単な説明。 (0 個または 1 個の要素)。
-   **LongDescription**: 親要素の説明を提供します。 (0 個または 1 個の要素)。
-   Annotation 要素。 (0 個以上の要素)。

### <a name="applicable-attributes"></a>適用可能な属性

Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**ドキュメント**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、**ドキュメント**EntityType 要素の子要素としての要素。 次のスニペットにあった場合の CSDL は、コンテンツの .edmx ファイルの内容、**概要**と**LongDescription**要素が、Visual Studio に含まれる**プロパティ**ウィンドウをクリックすると、`Customer`エンティティ型。

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

**エンド**概念スキーマ定義言語 (CSDL) の要素は Association 要素または AssociationSet 要素の子にすることができます。 各ケースでは、役割で、**エンド**要素が異なると、該当する属性が異なります。

### <a name="end-element-as-a-child-of-the-association-element"></a>Association 要素の子としての End 要素

**エンド**要素 (の子として、**アソシエーション**要素) のアソシエーションの一方の端とのアソシエーションの end に存在できるエンティティ型のインスタンスの数のエンティティ型を識別します。 アソシエーション End はアソシエーションの一部として定義され、アソシエーションには必ず 2 つのアソシエーション End が必要です。 アソシエーションの 1 つの End にあるエンティティ型インスタンスには、ナビゲーション プロパティまたは外部キーからアクセスできます (エンティティ型で公開されている場合)。  

**エンド**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   OnDelete (0 個または 1 つの要素)
-   Annotation 要素 (0 個以上の要素)

#### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**エンド**要素の子である場合に、**アソシエーション**要素。

| 属性名   | 必須 | [値]                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**         | [はい]         | アソシエーションの一方の End のエンティティ型の名前。                                                                                                                                                                                                                                                                                                                                                         |
| **ロール**         | いいえ          | アソシエーション End の名前。 名前が指定されない場合、アソシエーション End のエンティティ型の名前が使用されます。                                                                                                                                                                                                                                                                                           |
| **多重度** | [はい]         | **1**、 **0..1**、または**\*** アソシエーションの end に存在できるエンティティ型のインスタンスの数によって異なります。 <br/> **1**その 1 つのエンティティ型のインスタンスは、アソシエーション end に存在することを示します。 <br/> **0..1**アソシエーション end に 0 個または 1 つのエンティティ型のインスタンスが存在することを示します。 <br/> **\*** アソシエーション end に 0、1、または複数のエンティティ型のインスタンスが存在することを示します。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**エンド**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

#### <a name="example"></a>例

次の例は、**アソシエーション**を定義する要素、 **CustomerOrders**アソシエーション。 **多重度**値はそれぞれ**エンド**の関連付けを示す多く**注文**と関連付けることができます、**顧客**が、1 つだけ**顧客**関連付けることができる、**順序**します。 さらに、 **OnDelete**要素には、ことを示しますすべて**注文**に関連する特定の**顧客**に読み込まれたとは ObjectContext をします。削除された場合、**顧客**は削除されます。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a>AssociationSet 要素の子としての End 要素

**エンド**要素は、アソシエーション セットの一方の end を指定します。 **AssociationSet**要素には、2 つ含める必要があります**エンド**要素。 含まれる情報を**エンド**要素は、アソシエーション セットのデータ ソースへのマッピングで使用します。

**エンド**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   Annotation 要素 (0 個以上の要素)

> [!NOTE]
> Annotation 要素は、それ以外のすべての子要素より後に指定する必要があります。 Annotation 要素は、CSDL v2 以降にのみ使用できます。

 

#### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**エンド**要素の子である場合に、 **AssociationSet**要素。

| 属性名 | 必須 | [値]                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | [はい]         | 名前、 **EntitySet** 、親の一方の end を定義する要素**AssociationSet**要素。 **EntitySet**で親と同じエンティティ コンテナー要素を定義する必要があります**AssociationSet**要素。 |
| **ロール**       | いいえ          | アソシエーション セット End の名前。 場合、**ロール**属性が使用されない場合、アソシエーション セット end の名前はエンティティ セットの名前になります。                                                                   |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**エンド**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

#### <a name="example"></a>例

次の例は、 **EntityContainer**要素を持つ**AssociationSet**それぞれに 2 つの要素、**エンド**要素。

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

**EntityContainer**概念スキーマ定義言語 (CSDL) での要素はエンティティ セット、アソシエーション セット、および関数インポートの論理コンテナーです。 EntityContainerMapping 要素を通じてストレージ モデルのエンティティ コンテナーに、概念モデルのエンティティ コンテナーがマップされます。 ストレージ モデルのエンティティ コンテナーは、データベースの構造を記述します。エンティティ セットはデータベースのテーブル、アソシエーション セットは外部キー、関数インポートはストアド プロシージャをそれぞれ記述します。

**EntityContainer**要素は、0 個または 1 つのドキュメント要素を持つことができます。 場合、**ドキュメント**要素が存在する、すべての前にする必要があります、 **EntitySet**、 **AssociationSet**、および**FunctionImport**要素。

**EntityContainer**要素 (表示順) で、次の子要素の 0 個以上を持つことができます。

-   EntitySet
-   AssociationSet
-   FunctionImport
-   Annotation 要素

拡張することができます、 **EntityContainer**要素別の内容を含める**EntityContainer**同じ名前空間内にあります。 別のコンテンツを含める**EntityContainer**を参照することで**EntityContainer**要素の値の設定、**拡張**属性、の名前を**EntityContainer**に含める要素。 含まれるすべての子要素**EntityContainer**要素を参照する子要素として扱われます**EntityContainer**要素。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **Using**要素。

| 属性名 | 必須 | [値]                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **Name**       | [はい]         | エンティティ コンテナー名。                               |
| **拡張**    | いいえ          | 同じ名前空間内にある別のエンティティ コンテナーの名前  |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntityContainer**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **EntityContainer** 3 つのエンティティ セットと 2 つのアソシエーション セットを定義する要素。

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

**EntitySet**概念スキーマ定義言語要素は、エンティティ型のインスタンスとそのエンティティ型から派生した任意の型のインスタンスの論理コンテナーです。 エンティティ型とエンティティ セットの間のリレーションシップは、リレーショナル データベースの行とテーブルの間のリレーションシップと似ています。 エンティティ型は、行と同様に関連データのセットを定義し、エンティティ セットには、テーブルと同様に、その定義のインスタンスが含まれます。 エンティティ セットは、エンティティ型のインスタンスをグループ化するための構造を提供します。これにより、データ ソース内の関連するデータ構造に、エンティティ型のインスタンスをマッピングできるようになります。  

特定のエンティティ型に対して複数のエンティティ セットを定義できます。

> [!NOTE]
> EF デザイナーは、型ごとに複数のエンティティ セットを含んでいる概念モデルをサポートしていません。

 

**EntitySet**要素は、(指定した順序で次の子要素を持つことができます。

-   Documentation 要素 (0 個または 1 つの要素を許可する場合)
-   Annotation 要素 (0 個以上の要素を許可する場合)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **EntitySet**要素。

| 属性名 | 必須 | [値]                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **Name**       | [はい]         | エンティティ セットの名前。                                                              |
| **EntityType** | [はい]         | エンティティ型の完全修飾名。そのインスタンスは、エンティティ セットに格納されます。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntitySet**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **EntityContainer** 3 つの要素**EntitySet**要素。

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
 

型ごとに複数のエンティティ セット (Multiple-Entity-Sets-per-Type: MEST) を定義できます。 次の例の 2 つのエンティティ セットを持つエンティティ コンテナーを定義する、**帳**エンティティの種類。

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

**EntityType**要素は、顧客や、概念モデルでの注文などの最上位の概念の構造を表します。 エンティティ型は、アプリケーションのエンティティ型インスタンス用テンプレートです。 各テンプレートには、次の情報が含まれています。

-   一意の名前  (必須) 
-   1 つ以上のプロパティにより定義されるエンティティ キー  (必須) 
-   データ格納用のプロパティ  (省略可能) 
-   アソシエーションの 1 つの End から別の End へのナビゲーションを可能にするナビゲーション プロパティ  (省略可能) 

アプリケーションでは、エンティティ型のインスタンスが特定のオブジェクト (特定の顧客や注文など) を表します。 エンティティ型の各インスタンスに対して、エンティティ セット内のエンティティ キーを一意にする必要があります。

2 つのエンティティ型のインスタンスは、型が同じであり、エンティティ キーの値が等しい場合にのみ、等価のインスタンスと見なされます。

**EntityType**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   キー (0 個または 1 つの要素)
-   プロパティ (0 個以上の要素)
-   NavigationProperty (0 個以上の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **EntityType**要素。

| 属性名                                                                                                                                  | 必須 | [値]                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| **Name**                                                                                                                                        | [はい]         | エンティティ型の名前。                                                                     |
| **BaseType**                                                                                                                                    | いいえ          | 別のエンティティ型の名前。この型が定義中のエンティティ型の基本型です。  |
| **抽象**                                                                                                                                    | いいえ          | **True**または**False**エンティティ型が抽象型がかどうかによって異なります。                 |
| **OpenType**                                                                                                                                    | いいえ          | **True**または**False**エンティティ型がオープンなエンティティ型がかどうかによって異なります。 <br/> [!NOTE] |
| >、 **OpenType**属性は、ADO.NET Data Services で使用される概念モデルで定義されているエンティティ型に適用されるのみです。 |             |                                                                                                  |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntityType**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **EntityType** 3 つの要素**プロパティ**要素と 2 つ**NavigationProperty**要素。

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

**EnumType**要素は、列挙型を表します。

**EnumType**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   メンバー (0 個以上の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **EnumType**要素。

| 属性名     | 必須 | [値]                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**           | [はい]         | エンティティ型の名前。                                                                                                                                                                  |
| **IsFlags**        | いいえ          | **True**または**False**フラグのセットとして列挙型を使用するかどうかによって異なります。 既定値は**False。** します。                                                                     |
| **UnderlyingType** | いいえ          | **Edm.Byte**、 **Edm.Int16**、 **Edm.Int32**、 **Edm.Int64**または**Edm.SByte**型の値の範囲を定義します。   基になる列挙体の要素の型の既定値は**Edm.Int32。** します。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EnumType**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **EnumType** 3 つの要素**メンバー**要素。

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a>Function 要素 (CSDL)

**関数**概念スキーマ定義言語 (CSDL) での要素を使用して関数を概念モデルで宣言または定義します。 DefiningExpression 要素を使用して、関数が定義されます。  

A**関数**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   パラメーター (0 個以上の要素)
-   DefiningExpression (0 個または 1 つの要素)
-   ReturnType (Function) (0 個または 1 つの要素)
-   Annotation 要素 (0 個以上の要素)

いずれかの関数の型を指定する戻り、 **ReturnType** (関数) の要素または**ReturnType**両方ではなく属性 (下記参照)。 有効な戻り値の型には、EdmSimpleType、エンティティ型、複合型、行型、または参照型 (あるいはこれらの型のいずれかのコレクション) があります。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、**関数**要素。

| 属性名 | 必須 | [値]                              |
|:---------------|:------------|:-----------------------------------|
| **Name**       | [はい]         | 関数の名前。          |
| **ReturnType** | いいえ          | 関数の戻り値の型。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**関数**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、**関数**インストラクターが雇用されてからの年数を返す関数を定義する要素。

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a>FunctionImport 要素 (CSDL)

**FunctionImport**概念スキーマ定義言語 (CSDL) 内の要素は、データ ソースで定義されているが、概念モデルでのオブジェクトで使用できる関数を表します。 たとえば、データベースのストアド プロシージャを表すストレージ モデルの関数の要素を使用できます。 A **FunctionImport**概念モデル内の要素は、Entity Framework アプリケーションに対応する関数を表し、FunctionImportMapping 要素を使用して、ストレージ モデル関数にマップされます。 関数がアプリケーションで呼び出されると、対応するストアド プロシージャがデータベースで実行されます。

**FunctionImport**要素は、(指定した順序で次の子要素を持つことができます。

-   ドキュメント (0 個または 1 つの要素を許可する場合)
-   パラメーター (0 個以上の要素を許可する場合)
-   Annotation 要素 (0 個以上の要素を許可する場合)
-   ReturnType (FunctionImport) (0 個以上の要素を許可する場合)

1 つ**パラメーター**関数が受け取る各パラメーターの要素を定義する必要があります。

いずれかの関数の型を指定する戻り、 **ReturnType** (FunctionImport) 要素または**ReturnType**両方ではなく属性 (下記参照)。 戻り値の型の値は EdmSimpleType、EntityType、または ComplexType のコレクションである必要があります。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **FunctionImport**要素。

| 属性名   | 必須 | [値]                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**         | [はい]         | インポートされる関数の名前。                                                                                                                                                                    |
| **ReturnType**   | いいえ          | 関数が返す型。 関数が値を返さない場合には、この属性を使用しないでください。 それ以外の場合、値には、ComplexType、EntityType、EDMSimpleType のコレクションがあります。        |
| **EntitySet**    | いいえ          | 関数は、エンティティのコレクションを返す場合の型の値、 **EntitySet**する必要がありますが、エンティティ セット、コレクションが属する。 それ以外の場合、 **EntitySet**属性を使用しない必要があります。 |
| **IsComposable** | いいえ          | 値が設定されている場合 true の場合、関数がコンポーザブルな (テーブル値関数) と、LINQ クエリで使用できます。  既定値は **false** です。                                                           |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **FunctionImport**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **FunctionImport**要素を 1 つのパラメーターを受け取り、エンティティ型のコレクションを返します。

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a>key 要素 (CSDL)

**キー**要素は、EntityType 要素の子要素であり定義、*エンティティ キー* (プロパティまたは一連の id を指定するエンティティ型のプロパティ)。 エンティティ キーを構成するプロパティは、デザイン時に選択されます。 エンティティ キー プロパティの値は、実行時のエンティティ セット内のエンティティ型のインスタンスを一意に識別する必要があります。 エンティティ キーを構成するプロパティには、エンティティ セット内のインスタンスの一意性を保証するものを選択する必要があります。 **キー**要素が 1 つまたは複数のエンティティ型のプロパティを参照することで、エンティティ キーを定義します。

**キー**要素は、次の子要素を持つことができます。

-   PropertyRef (1 つまたは複数の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>適用可能な属性

Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**キー**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、という名前のエンティティ型を定義します。**帳**します。 エンティティ キーが参照することで定義されている、 **ISBN**エンティティ型のプロパティ。

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
 

**ISBN**国際標準図書番号 (ISBN) は、書籍を一意に識別するため、プロパティがエンティティ キーとして適しています。

次の例では、エンティティ型 (**作成者**) の 2 つのプロパティで構成されるエンティティ キーを持つ**名前**と**アドレス**します。

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
 

使用して**名前**と**アドレス**同じ名前の 2 つの作成者は、同じアドレスでライブに可能性がないため、エンティティのキーが妥当な選択が。 ただし、エンティティ キーのこの選択では、エンティティ セット内のエンティティ キーの一意性を絶対的に保証することはできません。 などのプロパティの追加**著者 Id**を一意に識別するために使用される、著者がここで推奨されます。

 

## <a name="member-element-csdl"></a>Member 要素 (CSDL)

**メンバー**要素は EnumType 要素の子要素であり、列挙型のメンバーを定義します。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **FunctionImport**要素。

| 属性名 | 必須 | [値]                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**       | [はい]         | メンバーの名前。                                                                                                                                                                  |
| **[値]**      | いいえ          | メンバーの値。 既定では、最初のメンバーが値 0、および後続の列挙子の値が 1 ずつインクリメントされます。 同じ値を持つ複数のメンバーが存在します。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **FunctionImport**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **EnumType** 3 つの要素**メンバー**要素。

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a>NavigationProperty 要素 (CSDL)

A **NavigationProperty**要素は、アソシエーションの他方の end への参照を提供するナビゲーション プロパティを定義します。 プロパティ要素で定義されたプロパティとは異なり、図形とデータの特性のナビゲーション プロパティを定義しません。 ナビゲーション プロパティは、アソシエーション内で 2 つのエンティティ型間を移動するための手段を提供します。

ナビゲーション プロパティは、アソシエーション End の両方のエンティティ型で省略可能です。 1 つのアソシエーション End のエンティティ型にナビゲーション プロパティを定義した場合に、そのアソシエーションの他方の End でもエンティティ型にナビゲーション プロパティを定義する必要はありません。

ナビゲーション プロパティによって返されるデータ型は、リモートのアソシエーション End の多重度により決まります。 たとえば、ナビゲーション プロパティ、 **OrdersNavProp**、上に存在する、**顧客**エンティティ型間の一対多のアソシエーションをナビゲートして**顧客**と**順序**します。 ナビゲーション プロパティのリモートのアソシエーション end がある多重度多くあるため (\*)、そのデータ型は、コレクション (の**順序**)。 同様に、ナビゲーション プロパティの場合は、 **CustomerNavProp**、上に存在する、**順序**エンティティ型の場合は、そのデータ型になります**顧客**ので、リモート end の多重度1 つ (1)。

A **NavigationProperty**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **NavigationProperty**要素。

| 属性名   | 必須 | [値]                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**         | [はい]         | ナビゲーション プロパティの名前。                                                                                                                                                                                                             |
| **リレーションシップ** | [はい]         | モデルのスコープ内にあるアソシエーションの名前。                                                                                                                                                                                |
| **ToRole**       | [はい]         | ナビゲーションが終了する側のアソシエーション End。 値、 **ToRole**属性には、いずれかの値と同じである必要があります、**ロール**(AssociationEnd 要素で定義されている) のアソシエーション end のいずれかで定義されている属性。       |
| **FromRole**     | [はい]         | ナビゲーションが始まる側のアソシエーション End。 値、 **FromRole**属性には、いずれかの値と同じである必要があります、**ロール**(AssociationEnd 要素で定義されている) のアソシエーション end のいずれかで定義されている属性。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **NavigationProperty**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、エンティティ型を定義します (**帳**) 2 つのナビゲーション プロパティを持つ (**PublishedBy**と**WrittenBy**)。

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

**OnDelete**概念スキーマ定義言語 (CSDL) 内の要素の関連付けが接続されている動作を定義します。 場合、**アクション**属性に設定されて**Cascade**アソシエーションの一方の end に関連、アソシエーションの他方の end でエンティティ型は最初の end でエンティティ型が削除されたときに削除されます。 2 つのエンティティ型間の関連付けは、プライマリ キーと主キー リレーションシップに関係なく、アソシエーションの他方の end にあるプリンシパル オブジェクトが削除されたときに読み込まれた依存オブジェクトが削除された場合、 **OnDelete**指定。  

> [!NOTE]
> **OnDelete**要素にのみ影響するアプリケーションのランタイム動作は、データ ソース内の動作は影響しません。 データ ソースで定義された動作は、アプリケーションで定義された動作と同じである必要があります。

 

**OnDelete**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **OnDelete**要素。

| 属性名 | 必須 | [値]                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **動作**     | [はい]         | **Cascade**または**None**します。 場合**Cascade**、依存エンティティ型は、プリンシパル エンティティ型が削除されたときに削除されます。 場合**None**、プリンシパル エンティティ型が削除されたときに、依存エンティティ型は削除されません。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**アソシエーション**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、**アソシエーション**を定義する要素、 **CustomerOrders**アソシエーション。 **OnDelete**要素には、ことを示しますすべて**注文**に関連する特定の**顧客**と ObjectContext に読み込まれたときに削除されます、 **顧客**は削除されます。

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a>Parameter 要素 (CSDL)

**パラメーター**概念スキーマ定義言語 (CSDL) で要素が、FunctionImport 要素または Function 要素の子にすることができます。

### <a name="functionimport-element-application"></a>FunctionImport 要素のアプリケーション

A**パラメーター**要素 (の子として、 **FunctionImport**要素) は、CSDL で宣言された関数インポートの入力と出力のパラメーターを定義するために使用します。

**パラメーター**要素は、(指定した順序で次の子要素を持つことができます。

-   ドキュメント (0 個または 1 つの要素を許可する場合)
-   Annotation 要素 (0 個以上の要素を許可する場合)

#### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**パラメーター**要素。

| 属性名 | 必須 | [値]                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**       | [はい]         | パラメーターの名前。                                                                                                                                                                                                      |
| **Type**       | [はい]         | パラメーターの型。 値がある必要があります、 **EDMSimpleType**またはモデルのスコープ内にある複合型。                                                                                                             |
| **モード**       | いいえ          | **In**、**アウト**、または**InOut**パラメーターは、入力、出力、または入力/出力パラメーターかどうかによって異なります。                                                                                                                |
| **MaxLength**  | いいえ          | パラメーターの許容最大長。                                                                                                                                                                                    |
| **精度**  | いいえ          | パラメーターの有効桁数。                                                                                                                                                                                                 |
| **拡大縮小**      | いいえ          | パラメーターの小数点以下桁数。                                                                                                                                                                                                     |
| **SRID**       | いいえ          | システムの空間参照識別子です。 空間型のパラメーターに対してのみ有効です。 詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**パラメーター**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

#### <a name="example"></a>例

次の例は、 **FunctionImport**要素を 1 つ**パラメーター**子要素。 関数は、1 つの入力パラメーターを受け取り、エンティティ型のコレクションを返します。

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a>Function 要素のアプリケーション

A**パラメーター**要素 (の子として、**関数**要素)、概念モデルで定義または宣言される関数のパラメーターを定義します。

**パラメーター**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   CollectionType (0 個または 1 つの要素)
-   ReferenceType (0 個または 1 つの要素)
-   RowType (0 個または 1 つの要素)

> [!NOTE]
> 1 つだけ、 **CollectionType**、 **ReferenceType**、または**RowType**要素の子要素であることができます、**プロパティ**要素。

 

-   Annotation 要素 (0 個以上の要素を許可する場合)

> [!NOTE]
> Annotation 要素は、それ以外のすべての子要素より後に指定する必要があります。 Annotation 要素は、CSDL v2 以降にのみ使用できます。

 

#### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**パラメーター**要素。

| 属性名   | 必須 | [値]                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**         | [はい]         | パラメーターの名前。                                                                                                                                                                                                      |
| **Type**         | いいえ          | パラメーターの型。 パラメーターには次のいずれかの型 (またはこれらの型のコレクション) を使用できます。 <br/> **EdmSimpleType** <br/> エンティティ型 <br/> 複合型 <br/> 行型 <br/> 参照型                             |
| **Null 許容型**     | いいえ          | **True** (既定値) または**False**プロパティを持つことができるかどうかに応じて、 **null**値。                                                                                                                          |
| **DefaultValue** | いいえ          | プロパティの既定値。                                                                                                                                                                                              |
| **MaxLength**    | いいえ          | プロパティ値の最大長。                                                                                                                                                                                       |
| **FixedLength**  | いいえ          | **True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。                                                                                                                          |
| **精度**    | いいえ          | プロパティ値の有効桁数。                                                                                                                                                                                            |
| **拡大縮小**        | いいえ          | プロパティ値の小数点以下桁数。                                                                                                                                                                                                |
| **SRID**         | いいえ          | システムの空間参照識別子です。 空間型のプロパティに対してのみ有効です。 詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。 |
| **Unicode**      | いいえ          | **True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。                                                                                                                               |
| **照合順序**    | いいえ          | データ ソースで使用する照合順序を指定する文字列。                                                                                                                                                   |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**パラメーター**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

#### <a name="example"></a>例

次の例は、**関数**いずれかを使用する要素**パラメーター**関数パラメーターを定義する子要素。

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
``` 

 

## <a name="principal-element-csdl"></a>Principal 要素 (CSDL)

**プリンシパル**概念スキーマ定義言語 (CSDL) での要素は参照に関する制約のプリンシパル end を定義する ReferentialConstraint 要素に子要素です。 A **ReferentialConstraint**要素は、リレーショナル データベースで参照整合性制約と同様の機能を定義します。 データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティが別のエンティティ型のエンティティ キーを参照できます。 参照されるエンティティ型が呼び出される、*プリンシパル end*の制約。 プリンシパル end を参照するエンティティ型が呼び出される、*依存 end*の制約。 **PropertyRef**要素を使用すると、依存 end により参照されるキーを指定します。

**プリンシパル**要素は、(指定した順序で次の子要素を持つことができます。

-   PropertyRef (1 つまたは複数の要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、**プリンシパル**要素。

| 属性名 | 必須 | [値]                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **ロール**       | [はい]         | アソシエーションのプリンシパル End 上のエンティティ型の名前。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プリンシパル**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **ReferentialConstraint**要素の定義の一部である、 **PublishedBy**アソシエーション。 **Id**のプロパティ、**パブリッシャー**参照に関する制約のプリンシパル end のエンティティ型を使用します。

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

**プロパティ**概念スキーマ定義言語 (CSDL) の要素は、EntityType 要素、複合型の要素または RowType 要素の子にすることができます。

### <a name="entitytype-and-complextype-element-applications"></a>EntityType 要素と ComplexType 要素のアプリケーション

**プロパティ**要素 (の子として**EntityType**または**ComplexType**要素) 図形と、エンティティ型または複合型のインスタンスに含まれるデータの特性を定義. 概念モデルのプロパティは、クラスで定義されるプロパティに似ています。 クラスのプロパティがクラスの構造を定義し、オブジェクトに関する情報を伝達するのと同様に、概念モデルのプロパティはエンティティ型の構造を定義し、エンティティ型のインスタンスに関する情報を伝達します。

**プロパティ**要素は、(指定した順序で次の子要素を持つことができます。

-   Documentation 要素 (0 個または 1 つの要素を許可する場合)
-   Annotation 要素 (0 個以上の要素を許可する場合)

次のファセットに適用できる、**プロパティ**要素: **Nullable**、 **DefaultValue**、 **MaxLength**、 **FixedLength**、**精度**、**スケール**、 **Unicode**、**照合**、 **ConcurrencyMode**します。 ファセットは、プロパティ値をデータ ストアに格納する方法に関する情報を提供する XML 属性です。

> [!NOTE]
> ファセットは型のプロパティにのみ適用できます**EDMSimpleType**します。

 

#### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**プロパティ**要素。

| 属性名                                                         | 必須 | [値]                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**                                                               | [はい]         | プロパティの名前。                                                                                                                                                                                                       |
| **Type**                                                               | [はい]         | プロパティ値の型。 プロパティ値の型である必要があります、 **EDMSimpleType**またはモデルのスコープ内にある複合型 (完全修飾名で示されます)。                                                 |
| **Null 許容型**                                                           | いいえ          | **True** (既定値) または<strong>False</strong>プロパティが null 値を持っているかどうかによって。 <br/> [!NOTE]                                                                                                   |
| > 複合型プロパティがありますで CSDL v1`Nullable="False"`します。 |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                       | いいえ          | プロパティの既定値。                                                                                                                                                                                              |
| **MaxLength**                                                          | いいえ          | プロパティ値の最大長。                                                                                                                                                                                       |
| **FixedLength**                                                        | いいえ          | **True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。                                                                                                                          |
| **精度**                                                          | いいえ          | プロパティ値の有効桁数。                                                                                                                                                                                            |
| **拡大縮小**                                                              | いいえ          | プロパティ値の小数点以下桁数。                                                                                                                                                                                                |
| **SRID**                                                               | いいえ          | システムの空間参照識別子です。 空間型のプロパティに対してのみ有効です。 詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。 |
| **Unicode**                                                            | いいえ          | **True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。                                                                                                                               |
| **照合順序**                                                          | いいえ          | データ ソースで使用する照合順序を指定する文字列。                                                                                                                                                   |
| **ConcurrencyMode**                                                    | いいえ          | **None** (既定値) または**固定**します。 値が設定されている場合**固定**プロパティの値は、オプティミスティック同時実行制御チェックで使用されます。                                                                                  |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プロパティ**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

#### <a name="example"></a>例

次の例は、 **EntityType** 3 つの要素**プロパティ**要素。

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
 

次の例は、 **ComplexType** 5 つの要素**プロパティ**要素。

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

**プロパティ**要素 (の子として、 **RowType**要素) に渡されるしたりできる、モデル定義関数から返されるデータの特性と図形を定義します。  

**プロパティ**要素は、次の子要素の 1 つだけであることができます。

-   CollectionType
-   ReferenceType
-   RowType

**プロパティ**要素は、数値の子注釈要素を含めることができます。

> [!NOTE]
> Annotation 要素は、CSDL v2 以降にのみ使用できます。

 

#### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**プロパティ**要素。

| 属性名                                                     | 必須 | [値]                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**                                                           | [はい]         | プロパティの名前。                                                                                                                                                                                                       |
| **Type**                                                           | [はい]         | プロパティ値の型。                                                                                                                                                                                                 |
| **Null 許容型**                                                       | いいえ          | **True** (既定値) または**False**プロパティが null 値を持っているかどうかによって。 <br/> [!NOTE]                                                                                                                |
| > 複合型プロパティがありますで CSDL v1`Nullable="False"`します。 |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | いいえ          | プロパティの既定値。                                                                                                                                                                                              |
| **MaxLength**                                                      | いいえ          | プロパティ値の最大長。                                                                                                                                                                                       |
| **FixedLength**                                                    | いいえ          | **True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。                                                                                                                          |
| **精度**                                                      | いいえ          | プロパティ値の有効桁数。                                                                                                                                                                                            |
| **拡大縮小**                                                          | いいえ          | プロパティ値の小数点以下桁数。                                                                                                                                                                                                |
| **SRID**                                                           | いいえ          | システムの空間参照識別子です。 空間型のプロパティに対してのみ有効です。 詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。 |
| **Unicode**                                                        | いいえ          | **True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。                                                                                                                               |
| **照合順序**                                                      | いいえ          | データ ソースで使用する照合順序を指定する文字列。                                                                                                                                                   |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プロパティ**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

#### <a name="example"></a>例

次の例は**プロパティ**要素、モデル定義関数の戻り値の型の形状を定義するために使用します。

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

**PropertyRef**概念スキーマ定義言語 (CSDL) 内の要素は、プロパティは実行は、次のロールのいずれかを示すエンティティ型のプロパティを参照します。

-   エンティティのキー (ID を指定するエンティティ型のプロパティまたは一連のプロパティ) の一部。 1 つまたは複数**PropertyRef**要素は、エンティティ キーを定義するために使用できます。
-   参照制約の依存 End または プリンシパル End。

**PropertyRef**要素が子要素としての annotation 要素 (0 個以上) があることができますのみです。

> [!NOTE]
> Annotation 要素は、CSDL v2 以降にのみ使用できます。

 

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **PropertyRef**要素。

| 属性名 | 必須 | [値]                                |
|:---------------|:------------|:-------------------------------------|
| **Name**       | [はい]         | 参照されているプロパティの名前。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **PropertyRef**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、エンティティ型を定義します (**帳**)。 エンティティ キーが参照することで定義されている、 **ISBN**エンティティ型のプロパティ。

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
 

次の例では、2 つ**PropertyRef**要素を使用すると、その 2 つを示すプロパティ (**Id**と**PublisherId**) は、プリンシパル end と依存 end の参照制約です。

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

**ReferenceType**概念スキーマ定義言語 (CSDL) 内の要素をエンティティ型への参照を指定します。 **ReferenceType**要素は、次の要素の子にすることができます。

-   ReturnType (関数)
-   パラメーター
-   CollectionType

**ReferenceType**要素は、関数のパラメーターまたは戻り値の型を定義するときに使用します。

A **ReferenceType**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **ReferenceType**要素。

| 属性名 | 必須 | [値]                                         |
|:---------------|:------------|:----------------------------------------------|
| **Type**       | [はい]         | 参照先エンティティ型の名前。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReferenceType**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、 **ReferenceType**要素の子として使用される、**パラメーター**への参照を受け取るモデル定義関数内の要素を**Person**エンティティ種類:

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
 

次の例は、 **ReferenceType**要素の子として使用される、 **ReturnType** (関数) の要素への参照を返すモデル定義関数で、 **Person**エンティティの種類。

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

A **ReferentialConstraint**概念スキーマ定義言語 (CSDL) 内の要素は、リレーショナル データベースで参照整合性制約と同様の機能を定義します。 データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティが別のエンティティ型のエンティティ キーを参照できます。 参照されるエンティティ型が呼び出される、*プリンシパル end*の制約。 プリンシパル end を参照するエンティティ型が呼び出される、*依存 end*の制約。

1 つのエンティティ型で公開されている外部キーが別のエンティティ型のプロパティを参照している場合、 **ReferentialConstraint**要素が 2 つのエンティティ型間のアソシエーションを定義します。 **ReferentialConstraint** AssociationSetMapping の対応する要素はマッピング仕様言語 (MSL) では必要ありません、2 つのエンティティ型に関する情報が関連要素を提供します。 公開された外部キーがない 2 つのエンティティ型間のアソシエーションの対応する必要があります**AssociationSetMapping**要素の関連付け情報をデータ ソースにマップするためにします。

外部キーは、エンティティ型で公開されていない場合、 **ReferentialConstraint**要素は、エンティティ型と別のエンティティ型の間に主キーと主キー制約にのみ定義できます。

A **ReferentialConstraint**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   プリンシパル (正確に 1 つの要素)
-   依存 (正確に 1 つの要素)
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>適用可能な属性

**ReferentialConstraint**要素は、任意の数の annotation 属性 (カスタム XML 属性) を持つことができます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、 **ReferentialConstraint**要素の定義の一部として使用されている、 **PublishedBy**アソシエーション。

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
 

 

## <a name="returntype-function-element-csdl"></a>(関数) の ReturnType 要素 (CSDL)

**ReturnType**概念スキーマ定義言語 (CSDL) で (関数) の要素が Function 要素で定義されている関数の戻り値の型を指定します。 関数の戻り値の型を指定することも、 **ReturnType**属性。

返す型には、いずれかを指定できる**EdmSimpleType**エンティティ型、複合型、行型、ref 型、またはこれらの型のいずれかのコレクション。

いずれかで、関数の戻り値の型を指定することができます、**型**の属性、 **ReturnType** (関数) 要素では、次の子要素のいずれか。

-   CollectionType
-   ReferenceType
-   RowType

> [!NOTE]
> 関数の戻り値の両方を持つ型を指定する場合、モデルは検証されません、**型**の属性、 **ReturnType** (関数) 要素と子要素の 1 つ。

 

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **ReturnType** (関数) の要素。

| 属性名 | 必須 | [値]                              |
|:---------------|:------------|:-----------------------------------|
| **ReturnType** | いいえ          | 関数の戻り値の型。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReturnType** (関数) の要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、**関数**書籍が出版されて年数を返す関数を定義する要素。 戻り値の型がで指定されたに注意してください、**型**の属性を**ReturnType** (関数) の要素。

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

**ReturnType**概念スキーマ定義言語 (CSDL) で (FunctionImport) 要素は、FunctionImport 要素で定義されている関数の戻り値の型を指定します。 関数の戻り値の型を指定することも、 **ReturnType**属性。

型では、エンティティ型、複合型の任意のコレクションを返すまたは**EdmSimpleType**、

関数の戻り値の型を指定した、**型**の属性、 **ReturnType** (FunctionImport) 要素。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **ReturnType** (FunctionImport) 要素。

| 属性名 | 必須 | [値]                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**       | いいえ          | 関数が返す型。 値は、ComplexType、EntityType、EDMSimpleType のコレクションである必要があります。                                                                                      |
| **EntitySet**  | いいえ          | 関数は、エンティティのコレクションを返す場合の型の値、 **EntitySet**する必要がありますが、エンティティ セット、コレクションが属する。 それ以外の場合、 **EntitySet**属性を使用しない必要があります。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReturnType** (FunctionImport) 要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、 **FunctionImport**ブックとパブリッシャーを返します。 2 つの結果セットと 2 つのために、関数が返すことに注意してください。 **ReturnType** (FunctionImport) の要素を指定します。

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a>RowType 要素 (CSDL)

A **RowType**概念スキーマ定義言語 (CSDL) の要素は、パラメーターまたは概念モデルで定義された関数の戻り値の型として無名の構造体を定義します。

A **RowType**要素は、次の要素の子であることができます。

-   CollectionType
-   パラメーター
-   ReturnType (関数)

A **RowType**要素は、(指定した順序で次の子要素を持つことができます。

-   (1 つまたは複数) のプロパティ
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **RowType**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例では、使用するモデル定義関数、 **CollectionType**関数によって返される行のコレクションを指定する要素 (で指定されている、 **RowType**要素)。

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

**スキーマ**要素は概念モデルの定義のルート要素です。 概念モデルを構成するオブジェクト、関数、およびコンテナーの定義を格納します。

**スキーマ**要素は、次の子要素の 0 個以上を含めることができます。

-   Using
-   EntityContainer
-   EntityType
-   EnumType
-   関連付け
-   ComplexType
-   関数

A**スキーマ**要素は、0 個または 1 つの Annotation 要素を含めることができます。

> [!NOTE]
> **関数**要素と annotation 要素はできるの CSDL v2 以降。

 

**スキーマ**要素を使用して、 **Namespace**概念モデルのエンティティ型、複合型、およびアソシエーション オブジェクトの名前空間を定義する属性。 1 つの名前空間内で 2 つのオブジェクトが同じ名前を持つことはできません。 名前空間にわたる複数**スキーマ**要素と複数の .csdl ファイル。

概念モデル名前空間とは別の XML 名前空間から、**スキーマ**要素。 概念モデル名前空間 (で定義されている、 **Namespace**属性) はエンティティ型、複合型、およびアソシエーション型の論理コンテナーです。 XML 名前空間 (によって示される、 **xmlns**属性) の**スキーマ**要素が子要素と属性の既定の名前空間、**スキーマ**要素。 フォームの XML 名前空間 http://schemas.microsoft.com/ado/YYYY/MM/edm (YYYY と MM 表します年と月それぞれ) に CSDL 用に予約されています。 カスタム要素と属性は、このフォームがある名前空間に存在することはできません。

### <a name="applicable-attributes"></a>適用可能な属性

次の表の説明属性に適用できる、**スキーマ**要素。

| 属性名 | 必須 | [値]                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前空間**  | [はい]         | 概念モデルの名前空間。 値、 **Namespace**属性が型の完全修飾名に使用されます。 たとえば場合、 **EntityType**という名前の*顧客*Simple.Example.Model 名前空間の完全修飾名では、 **EntityType**はSimpleexamplemodel.customer です。 <br/> 値として、次の文字列を使用することはできません、 **Namespace**属性:**システム**、**一時的な**、または**Edm**します。 値、 **Namespace**属性の値と同じにすることはできません、 **Namespace** SSDL スキーマ要素の属性。 |
| **Alias**      | いいえ          | 名前空間の名前の代わりに使用される識別子。 たとえば場合、 **EntityType**という名前*顧客*Simple.Example.Model 名前空間にありの値は、**エイリアス**属性が*モデル*、Model.Customer の完全修飾名として使用することができます、 **EntityType。**                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**スキーマ**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例は、**スキーマ**要素を含む、 **EntityContainer**要素、2 つ**EntityType**要素、および 1 つ**の関連付け**要素。

``` xml
 <Schema xmlns="http://schemas.microsoft.com/ado/2009/11/edm"
      xmlns:cg="http://schemas.microsoft.com/ado/2009/11/codegeneration"
      xmlns:store="http://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
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

**TypeRef**概念スキーマ定義言語 (CSDL) 内の要素は、既存の名前付きの型への参照を提供します。 **TypeRef**要素が関数にパラメーターまたは戻り値の型としてのコレクションがあることを指定するために使用 CollectionType 要素の子にすることができます。

A **TypeRef**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   Annotation 要素 (0 個以上の要素)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **TypeRef**要素。 なお、 **DefaultValue**、 **MaxLength**、 **FixedLength**、**精度**、**スケール**、 **Unicode**、および**照合**属性にのみ適用されます**EDMSimpleTypes**します。

| 属性名                                                     | 必須 | [値]                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**                                                           | いいえ          | 参照先の型の名前。                                                                                                                                                                                          |
| **Null 許容型**                                                       | いいえ          | **True** (既定値) または**False**プロパティが null 値を持っているかどうかによって。 <br/> [!NOTE]                                                                                                                |
| > 複合型プロパティがありますで CSDL v1`Nullable="False"`します。 |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | いいえ          | プロパティの既定値。                                                                                                                                                                                              |
| **MaxLength**                                                      | いいえ          | プロパティ値の最大長。                                                                                                                                                                                       |
| **FixedLength**                                                    | いいえ          | **True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。                                                                                                                          |
| **精度**                                                      | いいえ          | プロパティ値の有効桁数。                                                                                                                                                                                            |
| **拡大縮小**                                                          | いいえ          | プロパティ値の小数点以下桁数。                                                                                                                                                                                                |
| **SRID**                                                           | いいえ          | システムの空間参照識別子です。 空間型のプロパティに対してのみ有効です。 詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。 |
| **Unicode**                                                        | いいえ          | **True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。                                                                                                                               |
| **照合順序**                                                      | いいえ          | データ ソースで使用する照合順序を指定する文字列。                                                                                                                                                   |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **CollectionType**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例では、使用するモデル定義関数、 **TypeRef**要素 (の子として、 **CollectionType**要素) のコレクションを関数が受け取ることを指定する**部門**エンティティ型。

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

**Using**概念スキーマ定義言語 (CSDL) 内の要素が別の名前空間に存在する概念モデルのコンテンツをインポートします。 値を設定して、 **Namespace**属性、エンティティ型、複合型、および別の概念モデルで定義されているアソシエーション型を参照することができます。 1 つ以上**Using**要素の子にすることができます、**スキーマ**要素。

> [!NOTE]
> **Using** CSDL 内の要素と同様に機能しません、**を使用して**のプログラミング言語でステートメント。 持つ名前空間をインポートすることによって、**を使用して**ステートメントで、プログラミング言語をするには影響しません、元の名前空間内のオブジェクト。 CSDL では、インポートされた名前空間に、元の名前空間にあるエンティティ型から派生したエンティティ型が含まれている場合があります。 これは、元の名前空間で宣言されたエンティティ セットに影響を及ぼすことがあります。

 

**Using**要素は、次の子要素を持つことができます。

-   ドキュメント (0 個または 1 つの要素を許可する場合)
-   Annotation 要素 (0 個以上の要素を許可する場合)

### <a name="applicable-attributes"></a>適用可能な属性

次の表は、属性を示しますに適用できる、 **Using**要素。

| 属性名 | 必須 | [値]                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前空間**  | [はい]         | インポートされる名前空間の名前。                                                                                                                                                |
| **Alias**      | [はい]         | 名前空間の名前の代わりに使用される識別子。 この属性は必須ですが、オブジェクト名を修飾するために名前空間名の代わりに使用することは必須ではありません。 |

 

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **Using**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

 

### <a name="example"></a>例

次の例で、 **Using**は名前空間をインポートするために使用されている要素では、別の場所で定義されています。 注意名前空間を**スキーマ**に示す要素が`BooksModel`します。 `Address`プロパティを`Publisher` **EntityType**複合型で定義されているは、`ExtendedBooksModel`名前空間 (と共にインポート、 **Using**要素)。

``` xml
 <Schema xmlns="http://schemas.microsoft.com/ado/2009/11/edm"
           xmlns:cg="http://schemas.microsoft.com/ado/2009/11/codegeneration"
           xmlns:store="http://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
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

annotation 属性は、概念モデルの要素に関する追加のメタデータを提供するために使用できます。 Annotation 要素に含まれるメタデータは、System.Data.Metadata.Edm 名前空間のクラスを使用して、実行時にアクセスできます。

### <a name="example"></a>例

次の例は、 **EntityType** annotation 属性を持つ要素 (**CustomAttribute**)。 また、エンティティ型の要素に適用される annotation 要素も示しています。

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="http://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="http://schemas.microsoft.com/ado/2009/11/edm">
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

annotation 要素は、概念モデルの要素に関する追加のメタデータを提供するために使用できます。 以降、.NET Framework version 4 では、annotation 要素に含まれるメタデータにアクセスできるランタイム System.Data.Metadata.Edm 名前空間のクラスを使用しています。

### <a name="example"></a>例

次の例は、 **EntityType** annotation 要素を持つ要素 (**CustomElement**)。 また、エンティティ型の要素に適用される annotation 属性も示しています。

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="http://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="http://schemas.microsoft.com/ado/2009/11/edm">
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

概念スキーマ定義言語 (CSDL) と呼ばれる抽象プリミティブ データ型のセットがサポートする**EDMSimpleTypes**、概念モデルのプロパティを定義します。 **EDMSimpleTypes**のストレージ環境またはホスティング環境でサポートされるプリミティブ データ型のプロキシします。

下の表は、CSDL でサポートされるプリミティブ データ型の一覧を示します。 テーブルは、それぞれに適用できるファセットも一覧表示されます。 **EDMSimpleType**します。

| EDMSimpleType                    | 説明                                                | 使用できるファセット                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| **Edm.binary です。**                   | バイナリ データを格納します。                                      | MaxLength、FixedLength、Nullable、Default                                |
| **Edm.Boolean**                  | 値を含む**true**または**false**します。                  | Nullable、Default                                                        |
| **Edm.Byte**                     | 符号なし 8 ビット整数値を格納します。                  | Precision、Nullable、Default                                             |
| **Edm.DateTime**                 | 日時を表します。                                | Precision、Nullable、Default                                             |
| **Edm.DateTimeOffset**           | GMT からのオフセット (分単位) としての日時を格納します。 | Precision、Nullable、Default                                             |
| **Edm.Decimal**                  | 有効桁数と小数点以下桁数が固定長の数値を格納します。   | Precision、Nullable、Default                                             |
| **Edm.Double**                   | 持つ浮動小数点数 15 桁の有効桁数が含まれています   | Precision、Nullable、Default                                             |
| **Edm.Float**                    | 7 桁の有効桁数を持つ浮動小数点数を格納します。   | Precision、Nullable、Default                                             |
| **Edm.Guid**                     | 16 バイトの一意識別子を格納します。                      | Precision、Nullable、Default                                             |
| **Edm.Int16**                    | 符号付き 16 ビット整数値を格納します。                    | Precision、Nullable、Default                                             |
| **Edm.Int32**                    | 符号付き 32 ビット整数値を格納します。                    | Precision、Nullable、Default                                             |
| **Edm.Int64**                    | 符号付き 64 ビット整数値を格納します。                    | Precision、Nullable、Default                                             |
| **Edm.SByte**                    | 符号付き 8 ビット整数値を格納します。                     | Precision、Nullable、Default                                             |
| **Edm.String**                   | 文字データを格納します。                                   | Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default |
| **Edm.time です。**                     | 時刻を格納します。                                    | Precision、Nullable、Default                                             |
| **Edm.Geography**                |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeographyPoint**           |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeographyLineString**      |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeographyPolygon**         |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeographyMultiPoint**      |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeographyMultiLineString** |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeographyMultiPolygon**    |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeographyCollection**      |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.Geometry**                 |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeometryPoint**            |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeometryLineString**       |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeometryPolygon**          |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeometryMultiPoint**       |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeometryMultiLineString**  |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeometryMultiPolygon**     |                                                            | Null を許容すると、既定では、SRID                                                  |
| **Edm.GeometryCollection**       |                                                            | Null を許容すると、既定では、SRID                                                  |

## <a name="facets-csdl"></a>ファセット (CSDL)

概念スキーマ定義言語 (CSDL) のファセットは、エンティティ型と複合型のプロパティに対する制約を表します。 ファセットは次の CSDL 要素で XML 属性として使用されます。

-   プロパティ
-   TypeRef
-   パラメーター

次の表は、CSDL でサポートされるファセットについて説明しています。 いずれのファセットもオプションです。 次に示すいくつかのファセットは、概念モデルからデータベースを生成するときに、Entity Framework によって使用されます。

> [!NOTE]
> 概念モデルのデータ型については、概念モデルの型 (CSDL) を参照してください。

| ファセット               | 説明                                                                                                                                                                                                                                                   | 対象                                                                                                                                                                                                                                                                                                                                                                           | データベースの生成に使用 | ランタイムで使用 |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| **照合順序**       | プロパティの値に対して比較と順序付け操作を行うときに使用する照合シーケンス (または並べ替え順序) を指定します。                                                                                                               | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | [はい]                              | いいえ                  |
| **ConcurrencyMode** | プロパティの値をオプティミスティック同時実行制御チェックに使用することを指定します。                                                                                                                                                                    | すべて**EDMSimpleType**プロパティ                                                                                                                                                                                                                                                                                                                                                     | いいえ                               | [はい]                 |
| **既定値**         | インスタンス化で値が指定されない場合のプロパティの既定値を指定します。                                                                                                                                                                       | すべて**EDMSimpleType**プロパティ                                                                                                                                                                                                                                                                                                                                                     | [はい]                              | [はい]                 |
| **FixedLength**     | プロパティ値の長さを可変とすることができるかどうかを指定します。                                                                                                                                                                                                  | **Edm.Binary**、 **Edm.String**                                                                                                                                                                                                                                                                                                                                                       | [はい]                              | いいえ                  |
| **MaxLength**       | プロパティ値の最大長を指定します。                                                                                                                                                                                                           | **Edm.Binary**、 **Edm.String**                                                                                                                                                                                                                                                                                                                                                       | [はい]                              | いいえ                  |
| **Null 許容型**        | プロパティを設定できるかどうかを指定する**null**値。                                                                                                                                                                                                     | すべて**EDMSimpleType**プロパティ                                                                                                                                                                                                                                                                                                                                                     | [はい]                              | [はい]                 |
| **精度**       | 型のプロパティの**Decimal**桁に指定できるプロパティの値の数を指定します。 型のプロパティの**時間**、 **DateTime**、および**DateTimeOffset**プロパティ値の秒の小数部の桁数を指定します。 | **Edm.DateTime**、 **Edm.DateTimeOffset**、 **Edm.Decimal**、 **edm.time です**                                                                                                                                                                                                                                                                                                              | [はい]                              | いいえ                  |
| **拡大縮小**           | プロパティ値の小数点の右側の桁数を指定します。                                                                                                                                                                      | **Edm.Decimal**                                                                                                                                                                                                                                                                                                                                                                      | [はい]                              | いいえ                  |
| **SRID**            | システムの空間参照システムの ID を指定します 詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。                                                              | **Edm.Geography、Edm.GeographyPoint、Edm.GeographyLineString、Edm.GeographyPolygon、Edm.GeographyMultiPoint、Edm.GeographyMultiLineString、Edm.GeographyMultiPolygon、Edm.GeographyCollection、Edm.Geometry、Edm.GeometryPoint、Edm.GeometryLineString、Edm.GeometryPolygon、Edm.GeometryMultiPoint、Edm.GeometryMultiLineString、Edm.GeometryMultiPolygon、Edm.GeometryCollection** | いいえ                               | [はい]                 |
| **Unicode**         | プロパティ値を Unicode として保存するかどうかを指定します。                                                                                                                                                                                                    | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | [はい]                              | [はい]                 |

>[!NOTE]
> データベース生成ウィザードがの値を認識する概念モデルからデータベースを生成するときに、 **StoreGeneratedPattern**属性を**プロパティ**場合は、次の要素名前空間: http://schemas.microsoft.com/ado/2009/02/edm/annotation します。 属性のサポートされる値は**Identity**と**計算済み**します。 値**Identity**データベースで生成される id 値を持つデータベース列が生成されます。 値**計算済み**データベースで計算される値を持つ列が生成されます。

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
    xmlns:a="http://schemas.microsoft.com/ado/2009/02/edm/annotation" />
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
