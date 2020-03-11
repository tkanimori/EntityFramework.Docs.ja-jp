---
title: SSDL 仕様-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
ms.openlocfilehash: b20d1f99f1da9c53a8a164fccc461e07d19c879d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415473"
---
# <a name="ssdl-specification"></a>SSDL 仕様
ストア スキーマ定義言語 (SSDL) は、Entity Framework アプリケーションのストレージ モデルを表す XML ベースの言語です。

Entity Framework アプリケーションでは、ストレージモデルのメタデータは、.ssdl (SSDL で記述された) ファイルから StoreItemCollection のインスタンスに読み込まれ、のメソッドを使用してアクセスできます。System.string... メタデータ。 Entity Framework は、ストレージモデルのメタデータを使用して、概念モデルに対するクエリをストア固有のコマンドに変換します。

Entity Framework Designer (EF デザイナー) は、デザイン時にストレージモデル情報を .edmx ファイルに保存します。 ビルド時に、Entity Designer は .edmx ファイルの情報を使用して、実行時に Entity Framework に必要な .ssdl ファイルを作成します。

SSDL のバージョンは、XML 名前空間で区別されます。

| SSDL バージョン | XML 名前空間                                     |
|:-------------|:--------------------------------------------------|
| SSDL v1      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| SSDL v2      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| SSDL v3      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a>Association 要素 (SSDL)

ストアスキーマ定義言語 (SSDL) の**Association**要素は、基になるデータベースの foreign key 制約に参加するテーブル列を指定します。 必要な 2 つの子 End 要素 は、アソシエーションの端のテーブルと両端の多重度を指定します。 オプションの ReferentialConstraint 要素 は、アソシエーションのプリンシパル End と依存 End、および参加する列を指定します。 AssociationSetMapping 要素を使用して、関連付けられている列マッピングを指定する必要**があります**。

**Association**要素には、次の子要素を含めることができます (一覧の順序)。

-   ドキュメント (0 個または1個)
-   End (2)
-   値のインデックス (0 または 1)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Association**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| **名前**       | はい         | 基盤となるデータベースの対応する外部キー制約の名前。 |

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**Association**要素に適用できます。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例では、 **Association** **要素を使用**して、 **FK\_CustomerOrders** foreign key 制約に参加する列を指定しています。

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

ストアスキーマ定義言語 (SSDL) の**AssociationSet**要素は、基になるデータベース内の2つのテーブル間の foreign key 制約を表します。 外部キー制約に参加するテーブル列は、Association 要素 で指定されます。 指定された**associationset**要素に対応する**association**要素は、 **associationset**要素の**association**属性で指定されます。

SSDL アソシエーション セットは AssociationSetMapping 要素 によって CSDL アソシエーション セット にマップされます。 ただし、指定された CSDL アソシエーションセットの CSDL アソシエーションが、AssociationSetMapping 要素を使用して定義されている場合は、対応する要素は必要ありません。 この場合、AssociationSetMapping 要素が存在する場合、それが定義するマッピングは、 **要素に**よってオーバーライドされます。

**AssociationSet**要素には、次の子要素を含めることができます (この順序で表示されます)。

-   ドキュメント (0 個または1個)
-   End (0 または 2 個)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **AssociationSet**要素に適用できる属性について説明します。

| 属性名  | 必須 | 値                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| **名前**        | はい         | アソシエーション セットが表す外部キー制約の名前。                          |
| **づける** | はい         | 外部キー制約に参加する列を定義するアソシエーションの名前。 |

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**AssociationSet**要素に適用できます。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、基になるデータベース内の `FK_CustomerOrders` foreign key 制約を表す**AssociationSet**要素を示しています。

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a>CollectionType 要素 (SSDL)

ストアスキーマ定義言語 (SSDL) の**CollectionType**要素は、関数の戻り値の型がコレクションであることを指定します。 **CollectionType**要素は、ReturnType 要素の子です。 コレクションの型は、RowType 子要素を使用して指定します。

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**CollectionType**要素に適用できます。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、 **CollectionType**要素を使用して、関数が行のコレクションを返すことを指定する関数を示しています。

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

ストアスキーマ定義言語 (SSDL) の**CommandText**要素は、データベースで実行される SQL ステートメントを定義できる Function 要素の子です。 **Commandtext**要素を使用すると、データベース内のストアドプロシージャに似た機能を追加できますが、ストレージモデルでは**commandtext**要素を定義します。

**CommandText**要素に子要素を含めることはできません。 **CommandText**要素の本文は、基になるデータベースの有効な SQL ステートメントである必要があります。

**CommandText**要素に適用できる属性はありません。

### <a name="example"></a>例

次の例は、子の**CommandText**要素を持つ**関数**要素を示しています。 概念モデルにインポートすることで、**この関数を**ObjectContext のメソッドとして公開します。  

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

ストアスキーマ定義言語 (SSDL) の**DefiningQuery**要素を使用すると、基になるデータベースで SQL ステートメントを直接実行できます。 **DefiningQuery**要素はデータベースビューのように一般的に使用されますが、ビューはデータベースではなくストレージモデルで定義されます。 **DefiningQuery**要素で定義されたビューは、EntitySetMapping 要素を通じて概念モデルのエンティティ型にマップできます。 このようなマッピングは、読み取り専用です。  

次の SSDL 構文は、 **EntitySet**の宣言と、ビューを取得するために使用されるクエリを含む**DefiningQuery**要素を示しています。

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

Entity Framework のストアドプロシージャを使用して、ビューに対する読み取り/書き込みシナリオを有効にすることができます。 データソースビューまたは Entity SQL ビューをベーステーブルとして使用して、データを取得したり、ストアドプロシージャによる変更処理を行うことができます。

**DefiningQuery**要素を使用して Microsoft SQL Server Compact 3.5 を対象にすることができます。 SQL Server Compact 3.5 はストアドプロシージャをサポートしていませんが、 **DefiningQuery**要素と同様の機能を実装できます。 他にこの要素が役立つ状況として、ストアド プロシージャを作成してプログラミング言語で使用されているデータ型とデータ ソースで使用されているデータ型の不一致を克服することができます。 特定のパラメーターセットを受け取り、データを削除するストアドプロシージャなど、異なるパラメーターのセットを使用してストアドプロシージャを呼び出す**DefiningQuery**を作成することもできます。

## <a name="dependent-element-ssdl"></a>Dependent 要素 (SSDL)

ストアスキーマ定義言語 (SSDL) の**dependent**要素は、外部キー制約の依存 end (参照制約とも呼ばれます) を定義する、参照要素の子要素です。 **Dependent**要素は、主キー列 (または列) を参照するテーブルの列 (または列) を指定します。 **Propertyref**要素は、参照される列を指定します。 Principal 要素は、 **Dependent**要素で指定された列によって参照される主キー列を指定します。

**Dependent**要素には、次の子要素を含めることができます (順に記載されています)。

-   PropertyRef (1 個以上)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、**依存**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ロール**       | はい         | 対応する End 要素の**Role**属性と同じ値 (使用されている場合)。それ以外の場合は、参照している列を含むテーブルの名前。 |

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**依存**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例では、Association**要素を使用**して、 **FK\_CustomerOrders** foreign key 制約に参加する列を指定しています。 **Dependent**要素は、 **Order**テーブルの**CustomerId**列を制約の依存 end として指定します。

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

ストアスキーマ定義言語 (SSDL) の**Documentation**要素は、親要素で定義されているオブジェクトに関する情報を提供するために使用できます。

**Documentation**要素には、次の子要素を含めることができます (一覧の順序)。

-   **Summary**: 親要素の簡単な説明。 (0 個または 1 個の要素)。
-   **Longdescription**: 親要素の詳細な説明。 (0 個または 1 個の要素)。

### <a name="applicable-attributes"></a>該当する属性

**Documentation**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、EntityType 要素の子要素としての**Documentation**要素を示しています。

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

ストアスキーマ定義言語 (SSDL) の**end**要素は、基になるデータベースの外部キー制約の一方の端にあるテーブルと行数を指定します。 **End**要素は、Association 要素または AssociationSet 要素の子にすることができます。 それぞれの場合において、使用可能な子要素と該当する属性は異なります。

### <a name="end-element-as-a-child-of-the-association-element"></a>Association 要素の子としての End 要素

**End**要素 ( **Association**要素の子として) では、外部キー制約の最後に**型**と**多重度**の属性を使用して、テーブルと行の数を指定します。 外部キー制約の End は、SSDL アソシエーションの一部として定義されます。SSDL アソシエーションには End が 2 つ必要です。

**終了**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation (0 個または1個の要素)
-   OnDelete (0 個または1個の要素)
-   Annotation 要素 (0 個以上の要素)

#### <a name="applicable-attributes"></a>該当する属性

次の表では、 **End**要素が**Association**要素の子である場合に適用できる属性について説明します。

| 属性名   | 必須 | 値                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Type**         | はい         | 外部キー制約の End にある SSDL エンティティ セットの完全修飾名。                                                                                                                                                                                                                                                                                          |
| **ロール**         | いいえ          | 対応する参照エンティティ Alconstraint 要素の Principal または Dependent 要素の**Role**属性の値 (使用されている場合)。                                                                                                                                                                                                                                             |
| **カーディナリティ** | はい         | **1**, **0 ..1**,、または、foreign key 制約の最後に配置できる行の数に応じて **\*** します。 <br/> **1**は、foreign key 制約 end に1行だけ存在することを示します。 <br/> **0 ..1**は、foreign key 制約 end に0行または1行が存在することを示します。 <br/> **\*** は、外部キー制約の end に0行、1行、または複数の行が存在することを示します。 |

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**終了**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

#### <a name="example"></a>例

次の例は、 **FK\_CustomerOrders** foreign key 制約を定義する**Association**要素を示しています。 各**End**要素に指定された複数要素の接続**性**の値は、 **Orders**テーブルの行を**customers**テーブルの行に関連付けることができることを示します。ただし、 **orders**テーブルの行に関連付けることができるのは、 **customers**テーブル内の1つの行だけです。 また、 **OnDelete**要素は **、customers テーブル**内の行が削除された場合に、 **customers**テーブル内の特定の行を参照する**Orders**テーブル内のすべての行が削除されることを示します。

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

**終了**要素 ( **AssociationSet**要素の子として) は、基になるデータベース内の foreign key 制約の一方の端にあるテーブルを指定します。

**終了**要素には、次の子要素を含めることができます (一覧の順序)。

-   ドキュメント (0 個または1個)
-   Annotation 要素 (0 個以上)

#### <a name="applicable-attributes"></a>該当する属性

次の表では、 **AssociationSet**要素の子である場合に**End**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | はい         | 外部キー制約の End にある SSDL エンティティ セットの名前。                                      |
| **ロール**       | いいえ          | 対応する Association 要素の1つの**End**要素に指定された**ロール**属性の1つの値。 |

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**終了**要素に適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

#### <a name="example"></a>例

次の例は、2つの**End**要素を持つ**AssociationSet**要素を持つ**EntityContainer**要素を示しています。

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

ストアスキーマ定義言語 (SSDL) の**EntityContainer**要素は、Entity Framework アプリケーション内の基になるデータソースの構造を記述します。 ssdl エンティティセット (EntitySet 要素で定義) は、データベース内のテーブルを表し、ssdl エンティティ型 (EntityType 要素で定義) はテーブル内の行を表し、(AssociationSet 要素で定義された) アソシエーションセットはデータベース内の外部キー ストレージ モデルのエンティティ コンテナーは、EntityContainerMapping 要素を通じて概念モデルのエンティティ コンテナーにマップされます。

**EntityContainer**要素には、0個または1個の Documentation 要素を含めることができます。 **Documentation**要素が存在する場合は、他のすべての子要素の前に置く必要があります。

**EntityContainer**要素には、次の子要素を0個以上含めることができます (一覧の順序)。

-   EntitySet
-   AssociationSet
-   Annotation 要素

### <a name="applicable-attributes"></a>該当する属性

次の表は、 **EntityContainer**要素に適用できる属性を示しています。

| 属性名 | 必須 | 値                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| **名前**       | はい         | エンティティ コンテナー名。 この名前にピリオド (.) を含めることはできません。 |

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**EntityContainer**要素に適用できます。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、2つのエンティティセットと1つのアソシエーションセットを定義する**EntityContainer**要素を示しています。 エンティティ型およびアソシエーション型の名前は、概念モデルの名前空間名によって修飾されます。

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

ストアスキーマ定義言語 (SSDL) の**EntitySet**要素は、基になるデータベースのテーブルまたはビューを表します。 SSDL の EntityType 要素は、テーブルまたはビュー内の行を表します。 **EntitySet**要素の**EntityType**属性は、ssdl エンティティセット内の行を表す特定の ssdl エンティティ型を指定します。 CSDL エンティティ セットと SSDL エンティティ セットの間のマッピングは、EntitySetMapping 要素で指定されます。

**EntitySet**要素には、次の子要素を含めることができます (一覧の順序)。

-   Documentation (0 個または1個の要素)
-   DefiningQuery (0 個または1個の要素)
-   Annotation 要素

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **EntitySet**要素に適用できる属性について説明します。

> [!NOTE]
> 一部の属性 (ここには記載されていません) は、**ストア**エイリアスで修飾できます。 このような属性は、モデルの更新時にモデルの更新ウィザードで使用されます。

| 属性名 | 必須 | 値                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **名前**       | はい         | エンティティ セットの名前。                                                              |
| **EntityType** | はい         | エンティティ型の完全修飾名。そのインスタンスは、エンティティ セットに格納されます。 |
| **[スキーマ]**     | いいえ          | データベース スキーマ。                                                                     |
| **Table**      | いいえ          | データベース テーブル。                                                                      |

> [!NOTE]
> **EntitySet**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、2つの**EntitySet**要素と1つの**AssociationSet**要素を持つ**EntityContainer**要素を示しています。

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

ストアスキーマ定義言語 (SSDL) の**EntityType**要素は、基になるデータベースのテーブルまたはビューの行を表します。 SSDL の EntitySet 要素は、行が発生するテーブルまたはビューを表します。 **EntitySet**要素の**EntityType**属性は、ssdl エンティティセット内の行を表す特定の ssdl エンティティ型を指定します。 SSDL のエンティティ型と CSDL のエンティティ型 間のマッピングは、EntityTypeMapping 要素で指定されます。

**EntityType**要素には、次の子要素を含めることができます (この順序で表示されます)。

-   Documentation (0 個または1個の要素)
-   Key (0 個または 1 個の要素)
-   Annotation 要素

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **EntityType**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**       | はい         | エンティティ型の名前。 通常、この値は、エンティティ型が行を表すテーブルの名前と同じです。 この値にピリオド (.) を含めることはできません。 |

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**EntityType**要素に適用できます。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、2つのプロパティを持つ**EntityType**要素を示しています。

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

ストアスキーマ定義言語 (SSDL) の**Function**要素は、基になるデータベースに存在するストアドプロシージャを指定します。

**Function**要素には、次の子要素を含めることができます (一覧の順序)。

-   ドキュメント (0 個または1個)
-   Parameter (0 個以上)
-   CommandText (0 または 1)
-   ReturnType (0 個以上)
-   Annotation 要素 (0 個以上)

関数の戻り値の型は、 **returntype**要素または**returntype**属性 (下記参照) のいずれかで指定する必要がありますが、両方を指定することはできません。

ストレージ モデル内で指定されるストアド プロシージャは、アプリケーションの概念モデルにインポートできます。 詳細については、「[ストアドプロシージャを使用したクエリ](~/ef6/modeling/designer/stored-procedures/query.md)」を参照してください。 **Function**要素を使用して、ストレージモデルでカスタム関数を定義することもできます。  

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Function**要素に適用できる属性について説明します。

> [!NOTE]
> 一部の属性 (ここには記載されていません) は、**ストア**エイリアスで修飾できます。 このような属性は、モデルの更新時にモデルの更新ウィザードで使用されます。

| 属性名             | 必須 | 値                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**                   | はい         | ストアド プロシージャの名前です。                                                                                                                                                                                  |
| **ReturnType**             | いいえ          | ストアド プロシージャの戻り値の型。                                                                                                                                                                           |
| **集計**              | いいえ          | ストアドプロシージャから集計値が返される場合は**True**を指定します。それ以外の場合は**False**。                                                                                                                                  |
| **BuiltIn**                | いいえ          | 関数が組み込みの<sup>1</sup>関数の場合は**True** 。それ以外の場合は**False**。                                                                                                                                  |
| **StoreFunctionName**      | いいえ          | ストアド プロシージャの名前です。                                                                                                                                                                                  |
| **いる niladicfunction**        | いいえ          | 関数がニラディック<sup>2</sup>関数の場合は**True**を返します。それ以外の場合は**False** 。                                                                                                                                   |
| **IsComposable**           | いいえ          | 関数がコンポーザブルな<sup>3</sup>関数の場合は**True** 。それ以外の場合は**False** 。                                                                                                                                |
| **ParameterTypeSemantics** | いいえ          | 関数オーバーロードの解決に使用される型のセマンティクスを定義する列挙。 列挙はプロバイダー マニフェストで関数定義によって定義されます。 既定値は**AllowImplicitConversion**です。 |
| **[スキーマ]**                 | いいえ          | ストアド プロシージャが定義されているスキーマの名前。                                                                                                                                                   |

<sup>1</sup>組み込み関数は、データベースで定義されている関数です。 ストレージモデルで定義されている関数の詳細については、「CommandText 要素 (SSDL)」を参照してください。

<sup>2</sup>ニラディック関数は、パラメーターを受け入れず、呼び出されたときにかっこを必要としない関数です。

<sup>3</sup> 2 つの関数の出力を他の関数の入力として使用できる場合、2つの関数はコンポーザブルです。

> [!NOTE]
> **関数**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、 **Updateorderquantity**ストアドプロシージャに対応する**関数**要素を示しています。 このストアド プロシージャは 2 個のパラメーターを受け入れ、値を返しません。

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

ストアスキーマ定義言語 (SSDL) の**key**要素は、基になるデータベース内のテーブルの主キーを表します。 **Key**は EntityType 要素の子要素であり、テーブル内の行を表します。 主キーは、 **EntityType**要素で定義されている1つ以上の Property 要素を参照することによって、 **key**要素で定義されます。

**Key**要素には、次の子要素を含めることができます (一覧の順序)。

-   PropertyRef (1 個以上)
-   Annotation 要素

**キー**要素に適用できる属性はありません。

### <a name="example"></a>例

次の例は、1つのプロパティを参照するキーを持つ**EntityType**要素を示しています。

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

ストアスキーマ定義言語 (SSDL) の**OnDelete**要素は、foreign key 制約に参加している行が削除された場合にデータベースの動作を反映します。 アクションが**Cascade**に設定されている場合、削除される行を参照する行も削除されます。 アクションが**None**に設定されている場合、削除される行を参照する行も削除されません。 **OnDelete**要素は、End 要素の子要素です。

**OnDelete**要素には、次の子要素を含めることができます (一覧の順序)。

-   ドキュメント (0 個または1個)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **OnDelete**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| **操作**     | はい         | **Cascade**または**None**。 (**制限**値は有効ですが、動作は**None**と同じです)。 |

> [!NOTE]
> **OnDelete**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、 **FK\_CustomerOrders** foreign key 制約を定義する**Association**要素を示しています。 **OnDelete**要素は **、customers テーブル**内の行が削除された場合に、 **customers**テーブル内の特定の行を参照する**Orders**テーブル内のすべての行が削除されることを示します。

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

ストアスキーマ定義言語 (SSDL) の**Parameter**要素は、データベース内のストアドプロシージャのパラメーターを指定する Function 要素の子です。

**Parameter**要素には、次の子要素を含めることができます (一覧の順序)。

-   ドキュメント (0 個または1個)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Parameter**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**       | はい         | パラメーターの名前。                                                                                                                                                                                                      |
| **Type**       | はい         | パラメーターの型。                                                                                                                                                                                                             |
| **モード**       | いいえ          | **In**、 **Out**、または**InOut**は、パラメーターが入力パラメーター、出力パラメーター、または入力/出力パラメーターのどちらであるかによって異なります。                                                                                                                |
| **MaxLength**  | いいえ          | パラメーターの最大長。                                                                                                                                                                                            |
| **[精度]**  | いいえ          | パラメーターの説明                                                                                                                                                                                                 |
| **スケール**      | いいえ          | パラメーターの小数点以下桁数                                                                                                                                                                                                     |
| **SRID**       | いいえ          | 空間システム参照識別子。 空間型のパラメーターに対してのみ有効です。 詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。 |

> [!NOTE]
> **パラメーター**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、入力パラメーターを指定する2つの**パラメーター**要素を持つ**関数**要素を示しています。

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

ストアスキーマ定義言語 (SSDL) の**principal**要素は、外部キー制約 (参照制約とも呼ばれます) のプリンシパル end を定義する、参照要素の子要素です。 **Principal**要素は、別の列 (または列) によって参照されるテーブル内の主キー列 (または列) を指定します。 **Propertyref**要素は、参照される列を指定します。 Dependent 要素は、 **Principal**要素で指定されている主キー列を参照する列を指定します。

**Principal**要素には、次の子要素を含めることができます (順に記載されています)。

-   PropertyRef (1 個以上)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Principal**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ロール**       | はい         | 対応する End 要素の**Role**属性と同じ値 (使用されている場合)。それ以外の場合は、参照先の列を含むテーブルの名前。 |

> [!NOTE]
> **Principal**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例では、Association**要素を使用**して、 **FK\_CustomerOrders** foreign key 制約に参加する列を指定しています。 **Principal**要素は、制約のプリンシパル end として**Customer**テーブルの**CustomerId**列を指定します。

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

ストアスキーマ定義言語 (SSDL) の**Property**要素は、基になるデータベース内のテーブルの列を表します。 **プロパティ**要素は、テーブル内の行を表す EntityType 要素の子です。 **EntityType**要素で定義されている各**Property**要素は、列を表します。

**Property**要素に子要素を含めることはできません。

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Property**要素に適用できる属性について説明します。

| 属性名            | 必須 | 値                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **名前**                  | はい         | 対応する列の名前。                                                                                                                                                                                           |
| **Type**                  | はい         | 対応する列の種類。                                                                                                                                                                                           |
| **NULL 値の使用**              | いいえ          | 対応する列が null 値を持つことができるかどうかによって、 **True** (既定値) または**False** 。                                                                                                                  |
| **既定**          | いいえ          | 対応する列の既定値。                                                                                                                                                                                  |
| **MaxLength**             | いいえ          | 対応する列の最大長。                                                                                                                                                                                 |
| **FixedLength**           | いいえ          | 対応する列の値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。                                                                                                              |
| **[精度]**             | いいえ          | 対応する列の有効桁数。                                                                                                                                                                                      |
| **スケール**                 | いいえ          | 対応する列の小数点以下桁数。                                                                                                                                                                                          |
| **Unicode**               | いいえ          | 対応する列の値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。                                                                                                                   |
| **Collation**             | いいえ          | データ ソースで使用される照合順序を指定する文字列。                                                                                                                                                   |
| **SRID**                  | いいえ          | 空間システム参照識別子。 空間型のプロパティに対してのみ有効です。 詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。 |
| **StoreGeneratedPattern** | いいえ          | **None**、 **identity** (対応する列の値がデータベースで生成された id である場合)、または**計算**された (対応する列の値がデータベースで計算されている場合)。 RowType プロパティには無効です。 |

> [!NOTE]
> **プロパティ**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、2つの子**プロパティ**要素を持つ**EntityType**要素を示しています。

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

ストアスキーマ定義言語 (SSDL) の**Propertyref**要素は、プロパティが次のいずれかのロールを実行することを示すために、EntityType 要素で定義されているプロパティを参照します。

-   **EntityType**が表すテーブルの主キーの一部です。 1つ以上の**Propertyref**要素を使用して主キーを定義できます。 詳細については、「Key 要素」を参照してください。
-   参照に関する制約の依存 End またはプリンシパル End になる。 詳細については、「ReferentialConstraint 要素」を参照してください。

**Propertyref**要素には、次の子要素のみを含めることができます。

-   ドキュメント (0 個または1個)
-   Annotation 要素

### <a name="applicable-attributes"></a>該当する属性

次の表では、 **Propertyref**要素に適用できる属性について説明します。

| 属性名 | 必須 | 値                                |
|:---------------|:------------|:-------------------------------------|
| **名前**       | はい         | 参照されているプロパティの名前。 |

> [!NOTE]
> **Propertyref**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。 ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例は、 **EntityType**要素で定義されているプロパティを参照することによって主キーを定義するために使用される**propertyref**要素を示しています。

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

ストアスキーマ定義言語 (SSDL) の参照型の**制約**要素は、基になるデータベースの foreign key 制約 (参照整合性制約とも呼ばれます) を表します。 制約のプリンシパル End と依存 End は、それぞれ Principal 子要素と Dependent 子要素によって指定されます。 プリンシパル End と依存 End に参加する列は、PropertyRef 要素で参照します。

この**要素は**、Association 要素の省略可能な子要素です。 **Association**要素で指定されている外部キー制約をマップするために、AssociationSetMapping 要素が使用されていない場合は、これを行うために、**この要素を**使用する必要があります。

この**要素には、次**の子要素を含めることができます。

-   ドキュメント (0 個または1個)
-   Principal (1 個)
-   依存 (正確に 1)
-   Annotation 要素 (0 個以上)

### <a name="applicable-attributes"></a>該当する属性

任意の数の annotation 属性 (カスタム XML 属性 **) を、この要素に**適用できます。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例では、 **Association** **要素を使用**して、 **FK\_CustomerOrders** foreign key 制約に参加する列を指定しています。

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

ストアスキーマ定義言語 (SSDL) の**ReturnType**要素は、 **function**要素で定義されている関数の戻り値の型を指定します。 関数の戻り値の型は、 **ReturnType**属性と共に指定することもできます。

関数の戻り値の型は、 **type**属性または**ReturnType**要素を使用して指定されます。

**ReturnType**要素には、次の子要素を含めることができます。

- CollectionType (1)  

> [!NOTE]
> 任意の数の annotation 属性 (カスタム XML 属性) を**ReturnType**要素に適用できます。 ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。 カスタム属性の完全修飾名は一意である必要があります。

### <a name="example"></a>例

次の例では、行のコレクションを返す**関数**を使用します。

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

ストアスキーマ定義言語 (SSDL) の**RowType**要素は、名前のない構造体を、ストアで定義されている関数の戻り値の型として定義します。

**RowType**要素は**CollectionType**要素の子要素です。

**RowType**要素には、次の子要素を含めることができます。

- Property (1 個以上)  

### <a name="example"></a>例

次の例では、 **CollectionType**要素を使用して、 **RowType**要素で指定されているように、関数が行のコレクションを返すことを指定するストア関数を示します。


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

ストアスキーマ定義言語 (SSDL) の**schema**要素は、ストレージモデル定義のルート要素です。 ストレージ モデルを構成するオブジェクト、関数、およびコンテナーの定義を格納します。

**Schema**要素には、次の子要素を0個以上含めることができます。

-   アソシエーション
-   EntityType
-   EntityContainer 要素
-   Function

**Schema**要素は、**名前空間**属性を使用して、ストレージモデル内のエンティティ型とアソシエーションオブジェクトの名前空間を定義します。 1 つの名前空間内で 2 つのオブジェクトが同じ名前を持つことはできません。

ストレージモデルの名前空間は、 **Schema**要素の XML 名前空間とは異なります。 ストレージモデルの名前空間 (**名前空間**属性で定義) は、エンティティ型とアソシエーション型の論理コンテナーです。 **Schema**要素の XML 名前空間 ( **xmlns**属性で示されます) は、 **schema**要素の子要素と属性の既定の名前空間です。 https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl 形式の XML 名前空間 (YYYY と MM はそれぞれ年と月を表します) は SSDL 用に予約されています。 カスタム要素と属性は、このフォームがある名前空間に存在することはできません。

### <a name="applicable-attributes"></a>該当する属性

次の表では、**スキーマ**要素に適用できる属性について説明します。

| 属性名            | 必須 | 値                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Namespace**             | はい         | ストレージ モデルの名前空間。 **名前空間**属性の値は、型の完全修飾名を形成するために使用されます。 たとえば、 *Customer*という名前の**entitytype**が examplemodel.store 名前空間にある場合、 **entitytype**の完全修飾名は examplemodel.store になります。 <br/> 次の文字列は、 **Namespace**属性の値として使用できません: **System**、 **Transient**、または**Edm**。 **名前空間**属性の値を、CSDL スキーマ要素の**namespace**属性の値と同じにすることはできません。 |
| **エイリアス**                 | いいえ          | 名前空間の名前の代わりに使用される識別子。 たとえば、 *Customer*という名前の**entitytype**が examplemodel.store 名前空間にあり、 **Alias**属性の値が*StorageModel*の場合、 **entitytype**の完全修飾名として StorageModel を使用できます。                                                                                                                                                                                                                                                                                    |
| **プロバイダー**              | はい         | データ プロバイダー。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **ProviderManifestToken** | はい         | 返すプロバイダー マニフェストをプロバイダーに示すトークン。 トークンの形式は定義されていません。 トークンの値は、プロバイダーで定義されています。 SQL Server プロバイダーマニフェストトークンの詳細については、「SqlClient for Entity Framework」を参照してください。                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a>例

**EntityContainer**要素、2つの**EntityType**要素、および1つの**Association**要素を含む**スキーマ**要素の例を次に示します。

``` xml
 <Schema Namespace="ExampleModel.Store"
       Alias="Self" Provider="System.Data.SqlClient"
       ProviderManifestToken="2008"
       xmlns="https://schemas.microsoft.com/ado/2009/11/edm/ssdl">
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

複数の annotation 属性を特定の 1 つの SSDL 要素に適用することができる。 Annotation 要素に含まれるメタデータには、実行時に、system.string 名前空間のクラスを使用してアクセスできます。

### <a name="example"></a>例

次の例は、 **OrderId**プロパティに annotation 属性が適用されている EntityType 要素を示しています。 この例では、 **EntityType**要素に追加された annotation 要素も示しています。

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

複数の annotation 要素を特定の SSDL 要素の子にすることができます。 .NET Framework version 4 以降では、annotation 要素に含まれるメタデータに、実行時に、system.string 名前空間のクラスを使用してアクセスできます。

### <a name="example"></a>例

Annotation 要素 (**customelement**) を持つ EntityType 要素の例を次に示します。 また、この例では、 **OrderId**プロパティに適用される annotation 属性も示しています。

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

ストア スキーマ定義言語 (SSDL) のファセットは、Property 要素で指定された列型に対する制約を表します。 ファセットは、**プロパティ**要素の XML 属性として実装されます。

次の表では、SSDL でサポートされるファセットについて説明します。

| ファセット           | Description                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Collation**   | プロパティの値に対して比較と順序付け操作を行うときに使用する照合シーケンス (または並べ替え順序) を指定します。                                                                                                             |
| **FixedLength** | 列値の長さを可変とすることができるかどうかを指定します。                                                                                                                                                                                                  |
| **MaxLength**   | 列値の最大長を指定します。                                                                                                                                                                                                           |
| **[精度]**   | **Decimal**型のプロパティの場合、プロパティ値が持つことができる桁数を指定します。 **Time**、 **DateTime**、および**DateTimeOffset**型のプロパティについては、列の値の秒部分の桁数を指定します。 |
| **スケール**       | 列値の小数点の右側の桁数を指定します。                                                                                                                                                                      |
| **Unicode**     | 列値を Unicode として保存するかどうかを指定します。                                                                                                                                                                                                    |
