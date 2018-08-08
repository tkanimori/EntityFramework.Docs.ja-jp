---
title: SSDL 仕様 - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
caps.latest.revision: 3
ms.openlocfilehash: a9977c80d9a9401afdcad2284a705bcb28790fb8
ms.sourcegitcommit: 9ae4473425c5e76337c9d032b0e5dbfedf1fcf57
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "39122723"
---
# <a name="ssdl-specification"></a>SSDL 仕様
ストア スキーマ定義言語 (SSDL) は、Entity Framework アプリケーションのストレージ モデルを表す XML ベースの言語です。

Entity Framework アプリケーションでストレージ モデルのメタデータが読み込まれた .ssdl ファイル (SSDL で記述) から、System.Data.Metadata.Edm.StoreItemCollection のインスタンスに、アクセスできるメソッドを使用して、System.Data.Metadata.Edm.MetadataWorkspace クラスです。 Entity Framework では、ストレージ モデルのメタデータを使用してストアに固有のコマンドを概念モデルに対するクエリに変換します。

Entity Framework デザイナー (EF Designer) は、デザイン時に .edmx ファイルでストレージ モデル情報を格納します。 ビルド時に、エンティティ デザイナーは、実行時に Entity Framework によって必要な .ssdl ファイルを作成するのに、.edmx ファイルの情報を使用します。

SSDL のバージョンは、XML 名前空間で区別されます。

| SSDL のバージョン | XML Namespace                                     |
|:-------------|:--------------------------------------------------|
| SSDL v1      | http://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| SSDL v2      | http://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| SSDL v3      | http://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a>Association 要素 (SSDL)

**アソシエーション**ストア スキーマ定義言語 (SSDL) 内の要素は、基になるデータベースの外部キー制約に参加するテーブル列を指定します。 2 つの必須の子 End 要素は、アソシエーションの end にあるテーブルと各 end の多重度を指定します。 省略可能な ReferentialConstraint 要素には、参加している列と同様に、アソシエーションのプリンシパルと依存 end を指定します。 ない場合は**ReferentialConstraint**要素が存在する、AssociationSetMapping 要素は、アソシエーションの列マッピングを指定するために使用する必要があります。

**アソシエーション**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つ) のドキュメント
-   終了 (正確には 2 つ)
-   ReferentialConstraint (0 または 1)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**アソシエーション**要素。

| 属性名 | 必須 | [値]                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| **Name**       | [はい]         | 基盤となるデータベースの対応する外部キー制約の名前。 |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**アソシエーション**要素。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、**アソシエーション**を使用する要素を**ReferentialConstraint**に参加する列を指定する要素、 **FK\_CustomerOrders**外部キー制約。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="associationset-element-ssdl"></a>AssociationSet 要素 (SSDL)

**AssociationSet**ストア スキーマ定義言語 (SSDL) 内の要素は、基になるデータベース内の 2 つのテーブル間の外部キー制約を表します。 外部キー制約に参加しているテーブルの列は、Association 要素で指定されます。 **アソシエーション**に対応する要素を指定した**AssociationSet**要素が指定されて、**アソシエーション**の属性、 **AssociationSet**要素。

SSDL アソシエーション セットは、AssociationSetMapping 要素によって CSDL アソシエーション セットにマップされます。 対応する、ReferentialConstraint 要素を使用して特定の CSDL アソシエーションの CSDL アソシエーション セットの場合を定義するただし、 **AssociationSetMapping**要素が必要です。 この場合は場合、 **AssociationSetMapping**要素が存在する、定義するマッピングによって上書きされます、 **ReferentialConstraint**要素。

**AssociationSet**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つ) のドキュメント
-   終了 (0 個または 2 つ)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **AssociationSet**要素。

| 属性名  | 必須 | [値]                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| **Name**        | [はい]         | アソシエーション セットが表す外部キー制約の名前。                          |
| **関連付け** | [はい]         | 外部キー制約に参加する列を定義するアソシエーションの名前。 |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **AssociationSet**要素。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、 **AssociationSet**要素を表す、`FK_CustomerOrders`基になるデータベースの外部キー制約。

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a>CollectionType 要素 (SSDL)

**CollectionType**ストア スキーマ定義言語 (SSDL) 内の要素は、関数の戻り値の型がコレクションであるを指定します。 **CollectionType** ReturnType 要素の子である要素。 RowType の子要素を使用して、コレクションの型が指定されます。

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **CollectionType**要素。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例では、使用する関数を**CollectionType**関数によって返される行のコレクションを指定する要素。

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="commandtext-element-ssdl"></a>CommandText 要素 (SSDL)

**CommandText**データベースで実行される SQL ステートメントを定義できるようにする関数要素の子であるストア スキーマ定義言語 (SSDL) 内の要素。 **CommandText**要素では、データベース内のストアド プロシージャのような機能を追加することができますが、定義する、 **CommandText**ストレージ モデル内の要素。

**CommandText**要素が子要素を含めることはできません。 本体、 **CommandText**要素は、基になるデータベースの有効な SQL ステートメントである必要があります。

適用可能な属性がない、 **CommandText**要素。

### <a name="example"></a>例

次の例は、**関数**子要素を持つ**CommandText**要素。 公開、 **UpdateProductInOrder**概念モデルにインポートすることにより、ObjectContext のメソッドとして機能します。  

``` xml
 <Function Name="UpdateProductInOrder" IsComposable="false">
   <CommandText>
     UPDATE Orders
     SET ProductId = @productId
     WHERE OrderId = @orderId;
   </CommandText>
   <Parameter Name="productId"
              Mode="In"
              Type="int"/>
   <Parameter Name="orderId"
              Mode="In"
              Type="int"/>
 </Function>
```

## <a name="definingquery-element-ssdl"></a>DefiningQuery 要素 (SSDL)

**DefiningQuery**ストア スキーマ定義言語 (SSDL) 内の要素では、基になるデータベースで直接 SQL ステートメントを実行できます。 **DefiningQuery**要素は、データベース ビューのようなよく使用しますが、データベースではなくストレージ モデルのビューが定義されます。 定義されているビューを**DefiningQuery**要素は、EntitySetMapping 要素を使用して、概念モデルのエンティティ型にマップすることができます。 このようなマッピングは、読み取り専用です。  

次の SSDL 構文の宣言を示しています、 **EntitySet**続けて、 **DefiningQuery**ビューを取得するために使用するクエリを含む要素。

``` xml
 <Schema>
     <EntitySet Name="Tables" EntityType="Self.STable">
         <DefiningQuery>
           SELECT  TABLE_CATALOG,
                   'test' as TABLE_SCHEMA,
                   TABLE_NAME
           FROM    INFORMATION_SCHEMA.TABLES
         </DefiningQuery>
     </EntitySet>
 </Schema>
```

Entity Framework でストアド プロシージャを使用すると、ビューに対する読み取り/書き込みシナリオを有効にします。 データを取得、および変更ストアド プロシージャによる処理のベース テーブルとしてデータ ソース ビューまたは Entity SQL ビューのいずれかを使用できます。

使用することができます、 **DefiningQuery**を Microsoft SQL Server Compact 3.5 をターゲット要素。 SQL Server Compact 3.5 がストアド プロシージャをサポートしていない場合と同様の機能を実装することができます、 **DefiningQuery**要素。 他にこの要素が役立つ状況として、ストアド プロシージャを作成してプログラミング言語で使用されているデータ型とデータ ソースで使用されているデータ型の不一致を克服することができます。 作成、 **DefiningQuery**一連のパラメーターを受け取り、データを削除するストアド プロシージャなどのパラメーターの異なるセットでのストアド プロシージャを呼び出しています。

## <a name="dependent-element-ssdl"></a>Dependent 要素 (SSDL)

**依存**ストア スキーマ定義言語 (SSDL) 内の要素は、外部キー制約 (参照に関する制約とも呼ばれます) の依存 end を定義する ReferentialConstraint 要素に子要素。 **依存**要素は、主キー列 (または列) を参照するテーブルの列 (または列) を指定します。 **PropertyRef**のどの列が参照されている要素を指定します。 プリンシパル要素で指定されている列によって参照される主キー列を指定します、**依存**要素。

**依存**要素は、(指定した順序で次の子要素を持つことができます。

-   PropertyRef (1 つまたは複数)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**依存**要素。

| 属性名 | 必須 | [値]                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ロール**       | [はい]         | 同じ値、**ロール**(使用) する場合の対応する終了要素の属性。 それ以外の場合、テーブルの名前を格納した参照元の列。 |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**依存**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例を使用する Association 要素を示しています、 **ReferentialConstraint**に参加する列を指定する要素、 **FK\_CustomerOrders**外部キー制約です。 **依存**要素を指定します、 **CustomerId**の列、**順序**テーブル制約の依存 end として。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="documentation-element-ssdl"></a>Documentation 要素 (SSDL)

**ドキュメント**親要素で定義されているオブジェクトに関する情報を提供するストア スキーマ定義言語 (SSDL) 内の要素を使用できます。

**ドキュメント**要素は、(指定した順序で次の子要素を持つことができます。

-   **概要**: 親要素の簡単な説明。 (0 個または 1 個の要素)。
-   **LongDescription**: 親要素の説明を提供します。 (0 個または 1 個の要素)。

### <a name="applicable-attributes"></a>適用可能な属性

Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**ドキュメント**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、**ドキュメント**EntityType 要素の子要素としての要素。

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="end-element-ssdl"></a>End 要素 (SSDL)

**エンド**ストア スキーマ定義言語 (SSDL) 内の要素が基になるデータベース テーブルと外部キー制約の一端にある行の数を指定します。 **エンド**要素は Association 要素または AssociationSet 要素の子にすることができます。 それぞれの場合において、使用可能な子要素と該当する属性は異なります。

### <a name="end-element-as-a-child-of-the-association-element"></a>Association 要素の子としての End 要素

**エンド**要素 (の子として、**アソシエーション**要素) と外部キー制約の最後の行の数とテーブルを指定します、**型**と**多重度**それぞれの属性します。 外部キー制約の End は、SSDL アソシエーションの一部として定義されます。SSDL アソシエーションには End が 2 つ必要です。

**エンド**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   OnDelete (0 個または 1 つの要素)
-   Annotation 要素 (0 個以上の要素)

#### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**エンド**要素の子である場合に、**アソシエーション**要素。

| 属性名   | 必須 | [値]                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**         | [はい]         | 外部キー制約の end にある SSDL エンティティ セットの完全修飾名。                                                                                                                                                                                                                                                                                          |
| **ロール**         | いいえ          | 値、**ロール**(使用) する場合、対応する ReferentialConstraint 要素のプリンシパル、または依存のいずれかの要素の属性します。                                                                                                                                                                                                                                             |
| **多重度** | [はい]         | **1**、 **0..1**、または**\*** 外部キー制約の end に存在できる行の数によって異なります。 <br/> **1**その 1 つの行が外部キー制約の end に存在することを示します。 <br/> **0..1**ことを示します、外部キー制約の end に 0 個または 1 つの行が存在します。 <br/> **\*** 0、1 つまたは複数の行が、外部キー制約の end が存在することを示します。 |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**エンド**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

#### <a name="example"></a>例

次の例は、**アソシエーション**を定義する要素、 **FK\_CustomerOrders**外部キー制約。 **多重度**それぞれで指定した値**エンド**要素で多くの行を示すため、**注文**テーブルの行に関連付けることができます、**顧客**テーブルが 1 つのみの行、**顧客**テーブルの行に関連付けることができます、**注文**テーブル。 さらに、 **OnDelete**要素は、のすべての行を示します、**注文**の特定の行を参照するテーブル、**顧客**場合は、テーブルを削除するが内の行**顧客**テーブルを削除します。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

### <a name="end-element-as-a-child-of-the-associationset-element"></a>AssociationSet 要素の子としての End 要素

**エンド**要素 (の子として、 **AssociationSet**要素)、基になるデータベースで外部キー制約の一方の end でテーブルを指定します。

**エンド**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つ) のドキュメント
-   Annotation 要素 (0 個以上)

#### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**エンド**要素の子である場合に、 **AssociationSet**要素。

| 属性名 | 必須 | [値]                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | [はい]         | 外部キー制約の end にある SSDL エンティティ セットの名前。                                      |
| **ロール**       | いいえ          | いずれかの値、**ロール**いずれかで指定された属性**エンド**対応する Association 要素の要素。 |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**エンド**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

#### <a name="example"></a>例

次の例は、 **EntityContainer**を持つ要素を**AssociationSet**要素を持つ**エンド**要素。

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitycontainer-element-ssdl"></a>EntityContainer 要素 (SSDL)

**EntityContainer**ストア スキーマ定義言語 (SSDL) 内の要素が Entity Framework アプリケーションで基になるデータ ソースの構造について説明します (EntitySet の要素で定義されている) SSDL エンティティ セット内のテーブルを表す。データベース、テーブル内の行を表す SSDL のエンティティ型 (EntityType 要素で定義されている) と (AssociationSet 要素で定義されている) のアソシエーション セットは、データベースの外部キー制約を表します。 EntityContainerMapping 要素を通じて概念モデルのエンティティ コンテナー、ストレージ モデルのエンティティ コンテナーにマップします。

**EntityContainer**要素は、0 個または 1 つのドキュメント要素を持つことができます。 場合、**ドキュメント**要素が存在する、他のすべての子要素が前に指定する必要があります。

**EntityContainer**要素 (表示順) で、次の子要素の 0 個以上を持つことができます。

-   EntitySet
-   AssociationSet
-   Annotation 要素

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **EntityContainer**要素。

| 属性名 | 必須 | [値]                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| **Name**       | [はい]         | エンティティ コンテナー名。 この名前にピリオド (.) を含めることはできません。 |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntityContainer**要素。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、 **EntityContainer** 2 つのエンティティ セットと 1 つのアソシエーション セットを定義する要素。 エンティティ型およびアソシエーション型の名前は、概念モデルの名前空間名によって修飾されます。

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entityset-element-ssdl"></a>EntitySet 要素 (SSDL)

**EntitySet**ストア スキーマ定義言語 (SSDL) 内の要素は、テーブルまたは基になるデータベースのビューを表します。 Ssdl EntityType 要素は、テーブルまたはビュー内の行を表します。 **EntityType**の属性、 **EntitySet**要素を SSDL エンティティ セット内の行を表す特定の SSDL エンティティ型を指定します。 CSDL のエンティティ セットと SSDL エンティティ セット間のマッピングは、EntitySetMapping 要素で指定されます。

**EntitySet**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   DefiningQuery (0 個または 1 つの要素)
-   Annotation 要素

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **EntitySet**要素。

> [!NOTE]
> (ここでは示しません) いくつかの属性で修飾できます、**格納**エイリアス。 これらの属性は、モデルを更新するときに、モデルの更新ウィザードによって使用されます。

| 属性名 | 必須 | [値]                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **Name**       | [はい]         | エンティティ セットの名前。                                                              |
| **EntityType** | [はい]         | エンティティ型の完全修飾名。そのインスタンスは、エンティティ セットに格納されます。 |
| **[スキーマ]**     | いいえ          | データベース スキーマ。                                                                     |
| **テーブル**      | いいえ          | データベース テーブル。                                                                      |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntitySet**要素。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、 **EntityContainer** 2 つ含まれる要素**EntitySet**要素と 1 つ**AssociationSet**要素。

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitytype-element-ssdl"></a>EntityType 要素 (SSDL)

**EntityType**ストア スキーマ定義言語 (SSDL) 内の要素は、テーブルまたはビューの基になるデータベース内の行を表します。 テーブルまたはビューの行が発生する SSDL の EntitySet 要素を表します。 **EntityType**の属性、 **EntitySet**要素を SSDL エンティティ セット内の行を表す特定の SSDL エンティティ型を指定します。 SSDL エンティティ型と CSDL のエンティティ型間のマッピングは、EntityTypeMapping 要素で指定されます。

**EntityType**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つの要素) のドキュメント
-   キー (0 個または 1 つの要素)
-   Annotation 要素

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **EntityType**要素。

| 属性名 | 必須 | [値]                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**       | [はい]         | エンティティ型の名前。 通常、この値は、エンティティ型が行を表すテーブルの名前と同じです。 この値にピリオド (.) を含めることはできません。 |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntityType**要素。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、 **EntityType** 2 つのプロパティを持つ要素。

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="function-element-ssdl"></a>Function 要素 (SSDL)

**関数**ストア スキーマ定義言語 (SSDL) 内の要素が基になるデータベースに存在するストアド プロシージャを指定します。

**関数**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つ) のドキュメント
-   パラメーター (0 個以上)
-   CommandText (0 または 1)
-   ReturnType (0 個以上)
-   Annotation 要素 (0 個以上)

いずれかの関数の型を指定する戻り、 **ReturnType**要素、または**ReturnType**両方ではなく属性 (下記参照)。

ストレージ モデル内で指定されるストアド プロシージャは、アプリケーションの概念モデルにインポートできます。 詳細については、次を参照してください。[ストアド プロシージャを使用したクエリ](~/ef6/modeling/designer/stored-procedures/query.md)します。 **関数**要素が、ストレージ モデルでカスタム関数を定義することもできます。  

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**関数**要素。

> [!NOTE]
> (ここでは示しません) いくつかの属性で修飾できます、**格納**エイリアス。 これらの属性は、モデルを更新するときに、モデルの更新ウィザードによって使用されます。

| 属性名             | 必須 | [値]                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**                   | [はい]         | ストアド プロシージャの名前。                                                                                                                                                                                  |
| **ReturnType**             | いいえ          | ストアド プロシージャの戻り値の型。                                                                                                                                                                           |
| **Aggregate**              | いいえ          | **True**ストアド プロシージャは、集計値を返す場合それ以外の場合**False**します。                                                                                                                                  |
| **組み込み**                | いいえ          | **True**関数が組み込み場合<sup>1</sup>関数です。 それ以外の場合**False**します。                                                                                                                                  |
| **StoreFunctionName**      | いいえ          | ストアド プロシージャの名前。                                                                                                                                                                                  |
| **NiladicFunction**        | いいえ          | **True**関数がニラディック場合<sup>2</sup>関数。**False**それ以外の場合。                                                                                                                                   |
| **IsComposable**           | いいえ          | **True**関数がコンポーザブルの場合<sup>3</sup>関数。**False**それ以外の場合。                                                                                                                                |
| **ParameterTypeSemantics** | いいえ          | 関数オーバーロードの解決に使用される型のセマンティクスを定義する列挙。 列挙はプロバイダー マニフェストで関数定義によって定義されます。 既定値は**AllowImplicitConversion**します。 |
| **[スキーマ]**                 | いいえ          | ストアド プロシージャが定義されているスキーマの名前。                                                                                                                                                   |

<sup>1</sup>組み込み関数は、データベースで定義されている関数。 ストレージ モデルで定義されている関数については、CommandText 要素 (SSDL) を参照してください。

<sup>2</sup>ニラディック関数が関数パラメーターを受け取らず、呼び出されると、かっこは必要ありません。

<sup>3</sup> 2 つの関数がコンポーザブルの場合は 1 つの関数の出力は、他の関数の入力を指定できます。

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**関数**要素。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、**関数**に対応する要素、 **UpdateOrderQuantity**ストアド プロシージャ。 このストアド プロシージャは 2 個のパラメーターを受け入れ、値を返しません。

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="key-element-ssdl"></a>key 要素 (SSDL)

**キー**ストア スキーマ定義言語 (SSDL) 内の要素は、基になるデータベース内のテーブルの主キーを表します。 **キー**テーブルの行を表す EntityType 要素の子要素です。 主キーが定義されている、**キー**要素で定義されている 1 つまたは複数のプロパティ要素を参照することによって、 **EntityType**要素。

**キー**要素は、(指定した順序で次の子要素を持つことができます。

-   PropertyRef (1 つまたは複数)
-   Annotation 要素

適用可能な属性がない、**キー**要素。

### <a name="example"></a>例

次の例は、 **EntityType** 1 つのプロパティを参照するキーを持つ要素。

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="ondelete-element-ssdl"></a>OnDelete 要素 (SSDL)

**OnDelete**ストア スキーマ定義言語 (SSDL) 内の要素では、外部キー制約に参加している行が削除された場合、データベースの動作が反映されます。 アクションに設定されている場合**Cascade**、削除される行を参照する行も削除されます。 アクションに設定されている場合**None**、削除される行を参照する行も削除されません。 **OnDelete**要素は、最後の要素の子要素。

**OnDelete**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つ) のドキュメント
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、 **OnDelete**要素。

| 属性名 | 必須 | [値]                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| **動作**     | [はい]         | **Cascade**または**None**します。 (値**Restricted**は有効ですと同じ動作が**None**)。 |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **OnDelete**要素。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、**アソシエーション**を定義する要素、 **FK\_CustomerOrders**外部キー制約。 **OnDelete**要素は、のすべての行を示します、**注文**の特定の行を参照するテーブル、**顧客**場合は、テーブルを削除するが、内の行**顧客**テーブルを削除します。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="parameter-element-ssdl"></a>Parameter 要素 (SSDL)

**パラメーター**ストア スキーマ定義言語 (SSDL) 内の要素は、データベースでストアド プロシージャのパラメーターを指定する Function 要素の子。

**パラメーター**要素は、(指定した順序で次の子要素を持つことができます。

-   (0 個または 1 つ) のドキュメント
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、**パラメーター**要素。

| 属性名 | 必須 | [値]                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**       | [はい]         | パラメーターの名前。                                                                                                                                                                                                      |
| **Type**       | [はい]         | パラメーターの型。                                                                                                                                                                                                             |
| **モード**       | いいえ          | **In**、**アウト**、または**InOut**パラメーターは、入力、出力、または入力/出力パラメーターかどうかによって異なります。                                                                                                                |
| **MaxLength**  | いいえ          | パラメーターの最大長。                                                                                                                                                                                            |
| **精度**  | いいえ          | パラメーターの有効桁数。                                                                                                                                                                                                 |
| **拡大縮小**      | いいえ          | パラメーターの小数点以下桁数。                                                                                                                                                                                                     |
| **SRID**       | いいえ          | システムの空間参照識別子です。 空間型のパラメーターに対してのみ有効です。 詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。 |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**パラメーター**要素。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、**関数**2 つ含まれる要素**パラメーター**入力パラメーターを指定する要素。

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="principal-element-ssdl"></a>Principal 要素 (SSDL)

**プリンシパル**ストア スキーマ定義言語 (SSDL) 内の要素は、外部キー制約 (参照に関する制約とも呼ばれます) のプリンシパル end を定義する ReferentialConstraint 要素に子要素。 **プリンシパル**要素が別の列 (または列) によって参照されるテーブルの主キー列 (または列) を指定します。 **PropertyRef**のどの列が参照されている要素を指定します。 Dependent 要素で指定されている主キー列を参照する列を指定します、**プリンシパル**要素。

**プリンシパル**要素は、(指定した順序で次の子要素を持つことができます。

-   PropertyRef (1 つまたは複数)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**プリンシパル**要素。

| 属性名 | 必須 | [値]                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ロール**       | [はい]         | 同じ値、**ロール**(使用) する場合の対応する終了要素の属性。 それ以外の場合、テーブルの名前を格納した参照先の列。 |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プリンシパル**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例を使用する Association 要素を示しています、 **ReferentialConstraint**に参加する列を指定する要素、 **FK\_CustomerOrders**外部キー制約です。 **プリンシパル**要素を指定します、 **CustomerId**の列、**顧客**テーブル制約のプリンシパル end として。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="property-element-ssdl"></a>Property 要素 (SSDL)

**プロパティ**ストア スキーマ定義言語 (SSDL) 内の要素は、基になるデータベース内のテーブルの列を表します。 **プロパティ**テーブル内の行を表す EntityType 要素の子である要素。 各**プロパティ**要素で定義されている、 **EntityType**要素は、列を表します。

A**プロパティ**要素は、すべての子要素を含めることはできません。

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性、**プロパティ**要素。

| 属性名            | 必須 | [値]                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**                  | [はい]         | 対応する列の名前。                                                                                                                                                                                           |
| **Type**                  | [はい]         | 対応する列の種類。                                                                                                                                                                                           |
| **Null 許容型**              | いいえ          | **True** (既定値) または**False**対応する列が null 値を持っているかどうかによって。                                                                                                                  |
| **DefaultValue**          | いいえ          | 対応する列の既定値。                                                                                                                                                                                  |
| **MaxLength**             | いいえ          | 対応する列の最大長。                                                                                                                                                                                 |
| **FixedLength**           | いいえ          | **True**または**False**固定長の文字列として、対応する列の値を格納するかどうかによって異なります。                                                                                                              |
| **精度**             | いいえ          | 対応する列の有効桁数。                                                                                                                                                                                      |
| **拡大縮小**                 | いいえ          | 対応する列の小数点以下桁数。                                                                                                                                                                                          |
| **Unicode**               | いいえ          | **True**または**False** Unicode 文字列として、対応する列の値を格納するかどうかによって異なります。                                                                                                                   |
| **照合順序**             | いいえ          | データ ソースで使用する照合順序を指定する文字列。                                                                                                                                                   |
| **SRID**                  | いいえ          | システムの空間参照識別子です。 空間型のプロパティに対してのみ有効です。 詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。 |
| **StoreGeneratedPattern** | いいえ          | **None**、 **Identity** (対応する列の値は、データベースで生成される id)、または**計算済み**(かどうか、対応する列の値は、データベースで計算)。 RowType プロパティのない有効期間は。 |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プロパティ**要素。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、 **EntityType** 2 つの子を持つ要素**プロパティ**要素。

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="propertyref-element-ssdl"></a>PropertyRef 要素 (SSDL)

**PropertyRef**ストア スキーマ定義言語 (SSDL) 内の要素は、プロパティは実行は、次のロールのいずれかを示す EntityType 要素で定義されたプロパティを参照します。

-   テーブルの主キーの一部を**EntityType**を表します。 1 つまたは複数**PropertyRef**プライマリ キーを定義する要素を使用できます。 詳細については、キー要素を参照してください。
-   参照に関する制約の依存 End またはプリンシパル End になる。 詳細については、ReferentialConstraint 要素を参照してください。

**PropertyRef**要素は、次の子要素を持つことができますのみ。

-   (0 個または 1 つ) のドキュメント
-   Annotation 要素

### <a name="applicable-attributes"></a>適用可能な属性

次の表に適用できる属性の説明、 **PropertyRef**要素。

| 属性名 | 必須 | [値]                                |
|:---------------|:------------|:-------------------------------------|
| **Name**       | [はい]         | 参照されているプロパティの名前。 |

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **PropertyRef**要素。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、 **PropertyRef**要素で定義されているプロパティを参照することによって主キーを定義するために使用する**EntityType**要素。

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="referentialconstraint-element-ssdl"></a>ReferentialConstraint 要素 (SSDL)

**ReferentialConstraint**ストア スキーマ定義言語 (SSDL) 内の要素は、基になるデータベースで外部キー制約 (参照整合性制約とも呼ばれます) を表します。 制約のプリンシパルと依存 end がそれぞれのプリンシパルと依存する子要素によって指定されます。 PropertyRef 要素を持つ、プリンシパル end と依存 end に参加する列が参照されます。

**ReferentialConstraint**要素は Association 要素の省略可能な子要素。 場合、 **ReferentialConstraint**要素は、マップで指定されている外部キー制約には使用されず、**アソシエーション**要素、AssociationSetMapping 要素は、これを行うために使用する必要があります。

**ReferentialConstraint**要素は、次の子要素を持つことができます。

-   (0 個または 1 つ) のドキュメント
-   プリンシパル (正確には 1 つ)
-   依存 (正確には 1 つ)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>適用可能な属性

Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReferentialConstraint**要素。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、**アソシエーション**を使用する要素を**ReferentialConstraint**に参加する列を指定する要素、 **FK\_CustomerOrders**外部キー制約。

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="returntype-element-ssdl"></a>ReturnType 要素 (SSDL)

**ReturnType**ストア スキーマ定義言語 (SSDL) 内の要素で定義されている関数の戻り値の型を指定します、**関数**要素。 関数の戻り値の型を指定することも、 **ReturnType**属性。

関数の戻り値の型を指定した、**型**属性または**ReturnType**要素。

**ReturnType**要素は、次の子要素を持つことができます。

- CollectionType (1 つ)  

> [!NOTE]
> Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReturnType**要素。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例では、**関数**行のコレクションを返します。

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```


## <a name="rowtype-element-ssdl"></a>RowType 要素 (SSDL)

A **RowType**ストア スキーマ定義言語 (SSDL) 要素は、戻り値として、名前のない構造体を定義します。 ストアで定義されている関数の型。

A **RowType**要素の子要素は、 **CollectionType**要素。

A **RowType**要素は、次の子要素を持つことができます。

- (1 つまたは複数) のプロパティ  

### <a name="example"></a>例

次の例を使用するストア関数を示しています、 **CollectionType**関数によって返される行のコレクションを指定する要素 (で指定されている、 **RowType**要素)。


``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="schema-element-ssdl"></a>Schema 要素 (SSDL)

**スキーマ**ストア スキーマ定義言語 (SSDL) での要素はストレージ モデルの定義のルート要素です。 ストレージ モデルを構成するオブジェクト、関数、およびコンテナーの定義を格納します。

**スキーマ**要素は、次の子要素の 0 個以上を含めることができます。

-   関連付け
-   EntityType
-   EntityContainer
-   関数

**スキーマ**要素を使用して、 **Namespace**ストレージ モデルのエンティティ型とアソシエーション オブジェクトの名前空間を定義する属性。 1 つの名前空間内で 2 つのオブジェクトが同じ名前を持つことはできません。

ストレージ モデル名前空間は、別の XML 名前空間から、**スキーマ**要素。 ストレージ モデル名前空間 (で定義されている、 **Namespace**属性) はエンティティ型とアソシエーション型の論理コンテナーです。 XML 名前空間 (によって示される、 **xmlns**属性) の**スキーマ**要素が子要素と属性の既定の名前空間、**スキーマ**要素。 フォームの XML 名前空間 http://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl SSDL 用に予約されています (YYYY と MM 表します年と月それぞれ)。 カスタム要素と属性は、このフォームがある名前空間に存在することはできません。

### <a name="applicable-attributes"></a>適用可能な属性

次の表の説明属性に適用できる、**スキーマ**要素。

| 属性名            | 必須 | [値]                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前空間**             | [はい]         | ストレージ モデルの名前空間。 値、 **Namespace**属性が型の完全修飾名に使用されます。 たとえば場合、 **EntityType**という名前の*顧客*ExampleModel.Store 名前空間の完全修飾名では、 **EntityType**はExamplemodel.store.customer です。 <br/> 値として、次の文字列を使用することはできません、 **Namespace**属性:**システム**、**一時的な**、または**Edm**します。 値、 **Namespace**属性の値と同じにすることはできません、 **Namespace** CSDL スキーマ要素の属性。 |
| **Alias**                 | いいえ          | 名前空間の名前の代わりに使用される識別子。 たとえば場合、 **EntityType**という名前の*顧客*ExampleModel.Store 名前空間にありの値は、**エイリアス**属性が*StorageModel*、StorageModel.Customer の完全修飾名として使用することができます、 **EntityType。**                                                                                                                                                                                                                                                                                    |
| **Provider**              | [はい]         | データ プロバイダー。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **ProviderManifestToken** | [はい]         | 返すプロバイダー マニフェストをプロバイダーに示すトークン。 トークンの形式は定義されていません。 トークンの値は、プロバイダーで定義されています。 SQL Server プロバイダー マニフェスト トークンについては、Entity Framework 用 SqlClient を参照してください。                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a>例

次の例は、**スキーマ**要素を含む、 **EntityContainer**要素、2 つ**EntityType**要素、および 1 つ**の関連付け**要素。

``` xml
 <Schema Namespace="ExampleModel.Store"
       Alias="Self" Provider="System.Data.SqlClient"
       ProviderManifestToken="2008"
       xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
   <EntityContainer Name="ExampleModelStoreContainer">
     <EntitySet Name="Customers"
                EntityType="ExampleModel.Store.Customers"
                Schema="dbo" />
     <EntitySet Name="Orders"
                EntityType="ExampleModel.Store.Orders"
                Schema="dbo" />
     <AssociationSet Name="FK_CustomerOrders"
                     Association="ExampleModel.Store.FK_CustomerOrders">
       <End Role="Customers" EntitySet="Customers" />
       <End Role="Orders" EntitySet="Orders" />
     </AssociationSet>
   </EntityContainer>
   <EntityType Name="Customers">
     <Documentation>
       <Summary>Summary here.</Summary>
       <LongDescription>Long description here.</LongDescription>
     </Documentation>
     <Key>
       <PropertyRef Name="CustomerId" />
     </Key>
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
   </EntityType>
   <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
     <Key>
       <PropertyRef Name="OrderId" />
     </Key>
     <Property Name="OrderId" Type="int" Nullable="false"
               c:CustomAttribute="someValue"/>
     <Property Name="ProductId" Type="int" Nullable="false" />
     <Property Name="Quantity" Type="int" Nullable="false" />
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <c:CustomElement>
       Custom data here.
     </c:CustomElement>
   </EntityType>
   <Association Name="FK_CustomerOrders">
     <End Role="Customers"
          Type="ExampleModel.Store.Customers" Multiplicity="1">
       <OnDelete Action="Cascade" />
     </End>
     <End Role="Orders"
          Type="ExampleModel.Store.Orders" Multiplicity="*" />
     <ReferentialConstraint>
       <Principal Role="Customers">
         <PropertyRef Name="CustomerId" />
       </Principal>
       <Dependent Role="Orders">
         <PropertyRef Name="CustomerId" />
       </Dependent>
     </ReferentialConstraint>
   </Association>
   <Function Name="UpdateOrderQuantity"
             Aggregate="false"
             BuiltIn="false"
             NiladicFunction="false"
             IsComposable="false"
             ParameterTypeSemantics="AllowImplicitConversion"
             Schema="dbo">
     <Parameter Name="orderId" Type="int" Mode="In" />
     <Parameter Name="newQuantity" Type="int" Mode="In" />
   </Function>
   <Function Name="UpdateProductInOrder" IsComposable="false">
     <CommandText>
       UPDATE Orders
       SET ProductId = @productId
       WHERE OrderId = @orderId;
     </CommandText>
     <Parameter Name="productId"
                Mode="In"
                Type="int"/>
     <Parameter Name="orderId"
                Mode="In"
                Type="int"/>
   </Function>
 </Schema>
```

## <a name="annotation-attributes"></a>annotation 属性

ストア スキーマ定義言語 (SSDL) の annotation 属性は、ストレージ モデルの要素に関する追加のメタデータを指定するストレージ モデルのカスタム XML 属性です。 有効な XML 構造が必要であることに加え、annotation 属性には次の制約が適用されます。

-   annotation 属性は、SSDL 用に予約された XML 名前空間に存在しない。
-   2 つの任意の annotation 属性の完全修飾名が同じではない。

複数の annotation 属性を特定の 1 つの SSDL 要素に適用することができる。 Annotation 要素に含まれるメタデータは、System.Data.Metadata.Edm 名前空間のクラスを使用して、実行時にアクセスできます。

### <a name="example"></a>例

次の例では、EntityType 要素に適用される annotation 属性を持つ、 **OrderId**プロパティ。 追加された注釈要素も示して、 **EntityType**要素。

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="annotation-elements-ssdl"></a>annotation 要素 (SSDL)

ストア スキーマ定義言語 (SSDL) の annotation 要素は、ストレージ モデルに関する追加のメタデータを指定するストレージ モデルのカスタム XML 要素です。 有効な XML 構造が必要であることに加え、annotation 要素には次の制約が適用されます。

-   annotation 要素は、SSDL 用に予約された XML 名前空間内に存在できません。
-   2 つの annotation 要素の完全修飾名を同じにすることはできません。
-   annotation 要素は、特定の SSDL 要素のその他すべての子要素より後に指定する必要があります。

複数の annotation 要素を特定の SSDL 要素の子にすることができます。 以降、.NET Framework version 4 では、annotation 要素に含まれるメタデータにアクセスできるランタイム System.Data.Metadata.Edm 名前空間のクラスを使用しています。

### <a name="example"></a>例

次の例は、annotation 要素を持つ EntityType 要素 (**CustomElement**)。 適用される annotation 属性も示します、 **OrderId**プロパティ。

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="facets-ssdl"></a>ファセット (SSDL)

ストア スキーマ定義言語 (SSDL) でのファセットは、プロパティ要素で指定されている列の型に対する制約を表します。 ファセットは、上の XML 属性として実装されます**プロパティ**要素。

次の表では、SSDL でサポートされるファセットについて説明します。

| ファセット           | 説明                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **照合順序**   | プロパティの値に対して比較と順序付け操作を行うときに使用する照合シーケンス (または並べ替え順序) を指定します。                                                                                                             |
| **FixedLength** | 列値の長さを可変とすることができるかどうかを指定します。                                                                                                                                                                                                  |
| **MaxLength**   | 列値の最大長を指定します。                                                                                                                                                                                                           |
| **精度**   | 型のプロパティの**Decimal**桁に指定できるプロパティの値の数を指定します。 型のプロパティの**時間**、 **DateTime**、および**DateTimeOffset**列の値の秒の小数部の桁数を指定します。 |
| **拡大縮小**       | 列値の小数点の右側の桁数を指定します。                                                                                                                                                                      |
| **Unicode**     | 列値を Unicode として保存するかどうかを指定します。                                                                                                                                                                                                    |
