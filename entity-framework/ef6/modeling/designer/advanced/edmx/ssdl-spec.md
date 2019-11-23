---
title: SSDL 仕様-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
ms.openlocfilehash: b20d1f99f1da9c53a8a164fccc461e07d19c879d
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182541"
---
# <a name="ssdl-specification"></a><span data-ttu-id="e6393-102">SSDL 仕様</span><span class="sxs-lookup"><span data-stu-id="e6393-102">SSDL Specification</span></span>
<span data-ttu-id="e6393-103">ストア スキーマ定義言語 (SSDL) は、Entity Framework アプリケーションのストレージ モデルを表す XML ベースの言語です。</span><span class="sxs-lookup"><span data-stu-id="e6393-103">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="e6393-104">Entity Framework アプリケーションでは、ストレージモデルのメタデータは、.ssdl (SSDL で記述された) ファイルから StoreItemCollection のインスタンスに読み込まれ、のメソッドを使用してアクセスできます。System.string... メタデータ。</span><span class="sxs-lookup"><span data-stu-id="e6393-104">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="e6393-105">Entity Framework は、ストレージモデルのメタデータを使用して、概念モデルに対するクエリをストア固有のコマンドに変換します。</span><span class="sxs-lookup"><span data-stu-id="e6393-105">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="e6393-106">Entity Framework Designer (EF デザイナー) は、デザイン時にストレージモデル情報を .edmx ファイルに保存します。</span><span class="sxs-lookup"><span data-stu-id="e6393-106">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="e6393-107">ビルド時に、Entity Designer は .edmx ファイルの情報を使用して、実行時に Entity Framework に必要な .ssdl ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="e6393-107">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="e6393-108">SSDL のバージョンは、XML 名前空間で区別されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-108">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="e6393-109">SSDL バージョン</span><span class="sxs-lookup"><span data-stu-id="e6393-109">SSDL Version</span></span> | <span data-ttu-id="e6393-110">XML 名前空間</span><span class="sxs-lookup"><span data-stu-id="e6393-110">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="e6393-111">SSDL v1</span><span class="sxs-lookup"><span data-stu-id="e6393-111">SSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="e6393-112">SSDL v2</span><span class="sxs-lookup"><span data-stu-id="e6393-112">SSDL v2</span></span>      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="e6393-113">SSDL v3</span><span class="sxs-lookup"><span data-stu-id="e6393-113">SSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="e6393-114">Association 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-114">Association Element (SSDL)</span></span>

<span data-ttu-id="e6393-115">ストアスキーマ定義言語 (SSDL) の**Association**要素は、基になるデータベースの foreign key 制約に参加するテーブル列を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-115">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="e6393-116">必要な 2 つの子 End 要素 は、アソシエーションの端のテーブルと両端の多重度を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-116">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="e6393-117">オプションの ReferentialConstraint 要素 は、アソシエーションのプリンシパル End と依存 End、および参加する列を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-117">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="e6393-118">AssociationSetMapping 要素を使用して、関連付けられている列マッピングを指定する必要**があります**。</span><span class="sxs-lookup"><span data-stu-id="e6393-118">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="e6393-119">**Association**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="e6393-119">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-120">ドキュメント (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="e6393-120">Documentation (zero or one)</span></span>
-   <span data-ttu-id="e6393-121">End (2)</span><span class="sxs-lookup"><span data-stu-id="e6393-121">End (exactly two)</span></span>
-   <span data-ttu-id="e6393-122">値のインデックス (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="e6393-122">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="e6393-123">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-123">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-124">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-124">Applicable Attributes</span></span>

<span data-ttu-id="e6393-125">次の表では、 **Association**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-125">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="e6393-126">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-126">Attribute Name</span></span> | <span data-ttu-id="e6393-127">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-127">Is Required</span></span> | <span data-ttu-id="e6393-128">値</span><span class="sxs-lookup"><span data-stu-id="e6393-128">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-129">**名前**</span><span class="sxs-lookup"><span data-stu-id="e6393-129">**Name**</span></span>       | <span data-ttu-id="e6393-130">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-130">Yes</span></span>         | <span data-ttu-id="e6393-131">基盤となるデータベースの対応する外部キー制約の名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-131">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="e6393-132">任意の数の annotation 属性 (カスタム XML 属性) を**Association**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-132">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="e6393-133">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-133">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="e6393-134">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-134">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-135">例</span><span class="sxs-lookup"><span data-stu-id="e6393-135">Example</span></span>

<span data-ttu-id="e6393-136">次の例では、 **Association** **要素を使用**して、 **FK\_CustomerOrders** foreign key 制約に参加する列を指定しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-136">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="associationset-element-ssdl"></a><span data-ttu-id="e6393-137">AssociationSet 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-137">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="e6393-138">ストアスキーマ定義言語 (SSDL) の**AssociationSet**要素は、基になるデータベース内の2つのテーブル間の foreign key 制約を表します。</span><span class="sxs-lookup"><span data-stu-id="e6393-138">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="e6393-139">外部キー制約に参加するテーブル列は、Association 要素 で指定されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-139">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="e6393-140">指定された**associationset**要素に対応する**association**要素は、 **associationset**要素の**association**属性で指定されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-140">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="e6393-141">SSDL アソシエーション セットは AssociationSetMapping 要素 によって CSDL アソシエーション セット にマップされます。</span><span class="sxs-lookup"><span data-stu-id="e6393-141">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="e6393-142">ただし、指定された CSDL アソシエーションセットの CSDL アソシエーションが、AssociationSetMapping 要素を使用して定義されている場合は、対応する要素は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e6393-142">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="e6393-143">この場合、AssociationSetMapping 要素が存在する場合、それが定義するマッピングは、 **要素に**よってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="e6393-143">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="e6393-144">**AssociationSet**要素には、次の子要素を含めることができます (この順序で表示されます)。</span><span class="sxs-lookup"><span data-stu-id="e6393-144">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-145">ドキュメント (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="e6393-145">Documentation (zero or one)</span></span>
-   <span data-ttu-id="e6393-146">End (0 または 2 個)</span><span class="sxs-lookup"><span data-stu-id="e6393-146">End (zero or two)</span></span>
-   <span data-ttu-id="e6393-147">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-147">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-148">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-148">Applicable Attributes</span></span>

<span data-ttu-id="e6393-149">次の表では、 **AssociationSet**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-149">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="e6393-150">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-150">Attribute Name</span></span>  | <span data-ttu-id="e6393-151">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-151">Is Required</span></span> | <span data-ttu-id="e6393-152">値</span><span class="sxs-lookup"><span data-stu-id="e6393-152">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-153">**名前**</span><span class="sxs-lookup"><span data-stu-id="e6393-153">**Name**</span></span>        | <span data-ttu-id="e6393-154">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-154">Yes</span></span>         | <span data-ttu-id="e6393-155">アソシエーション セットが表す外部キー制約の名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-155">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="e6393-156">**づける**</span><span class="sxs-lookup"><span data-stu-id="e6393-156">**Association**</span></span> | <span data-ttu-id="e6393-157">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-157">Yes</span></span>         | <span data-ttu-id="e6393-158">外部キー制約に参加する列を定義するアソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-158">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="e6393-159">任意の数の annotation 属性 (カスタム XML 属性) を**AssociationSet**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-159">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="e6393-160">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-160">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="e6393-161">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-161">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-162">例</span><span class="sxs-lookup"><span data-stu-id="e6393-162">Example</span></span>

<span data-ttu-id="e6393-163">次の例は、基になるデータベース内の `FK_CustomerOrders` foreign key 制約を表す**AssociationSet**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-163">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="e6393-164">CollectionType 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-164">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="e6393-165">ストアスキーマ定義言語 (SSDL) の**CollectionType**要素は、関数の戻り値の型がコレクションであることを指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-165">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="e6393-166">**CollectionType**要素は、ReturnType 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="e6393-166">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="e6393-167">コレクションの型は、RowType 子要素を使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-167">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="e6393-168">任意の数の annotation 属性 (カスタム XML 属性) を**CollectionType**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-168">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="e6393-169">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-169">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="e6393-170">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-170">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-171">例</span><span class="sxs-lookup"><span data-stu-id="e6393-171">Example</span></span>

<span data-ttu-id="e6393-172">次の例は、 **CollectionType**要素を使用して、関数が行のコレクションを返すことを指定する関数を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-172">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

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

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="e6393-173">CommandText 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-173">CommandText Element (SSDL)</span></span>

<span data-ttu-id="e6393-174">ストアスキーマ定義言語 (SSDL) の**CommandText**要素は、データベースで実行される SQL ステートメントを定義できる Function 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="e6393-174">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="e6393-175">**Commandtext**要素を使用すると、データベース内のストアドプロシージャに似た機能を追加できますが、ストレージモデルでは**commandtext**要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="e6393-175">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="e6393-176">**CommandText**要素に子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="e6393-176">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="e6393-177">**CommandText**要素の本文は、基になるデータベースの有効な SQL ステートメントである必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-177">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="e6393-178">**CommandText**要素に適用できる属性はありません。</span><span class="sxs-lookup"><span data-stu-id="e6393-178">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-179">例</span><span class="sxs-lookup"><span data-stu-id="e6393-179">Example</span></span>

<span data-ttu-id="e6393-180">次の例は、子の**CommandText**要素を持つ**関数**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-180">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="e6393-181">概念モデルにインポートすることで、**この関数を**ObjectContext のメソッドとして公開します。</span><span class="sxs-lookup"><span data-stu-id="e6393-181">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

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

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="e6393-182">DefiningQuery 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-182">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="e6393-183">ストアスキーマ定義言語 (SSDL) の**DefiningQuery**要素を使用すると、基になるデータベースで SQL ステートメントを直接実行できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-183">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="e6393-184">**DefiningQuery**要素はデータベースビューのように一般的に使用されますが、ビューはデータベースではなくストレージモデルで定義されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-184">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="e6393-185">**DefiningQuery**要素で定義されたビューは、EntitySetMapping 要素を通じて概念モデルのエンティティ型にマップできます。</span><span class="sxs-lookup"><span data-stu-id="e6393-185">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="e6393-186">このようなマッピングは、読み取り専用です。</span><span class="sxs-lookup"><span data-stu-id="e6393-186">These mappings are read-only.</span></span>  

<span data-ttu-id="e6393-187">次の SSDL 構文は、 **EntitySet**の宣言と、ビューを取得するために使用されるクエリを含む**DefiningQuery**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-187">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

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

<span data-ttu-id="e6393-188">Entity Framework のストアドプロシージャを使用して、ビューに対する読み取り/書き込みシナリオを有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="e6393-188">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span><span data-ttu-id="e6393-189"> データソースビューまたは Entity SQL ビューをベーステーブルとして使用して、データを取得したり、ストアドプロシージャによる変更処理を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e6393-189"> You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="e6393-190">**DefiningQuery**要素を使用して Microsoft SQL Server Compact 3.5 を対象にすることができます。</span><span class="sxs-lookup"><span data-stu-id="e6393-190">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="e6393-191">SQL Server Compact 3.5 はストアドプロシージャをサポートしていませんが、 **DefiningQuery**要素と同様の機能を実装できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-191">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="e6393-192">他にこの要素が役立つ状況として、ストアド プロシージャを作成してプログラミング言語で使用されているデータ型とデータ ソースで使用されているデータ型の不一致を克服することができます。</span><span class="sxs-lookup"><span data-stu-id="e6393-192">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="e6393-193">特定のパラメーターセットを受け取り、データを削除するストアドプロシージャなど、異なるパラメーターのセットを使用してストアドプロシージャを呼び出す**DefiningQuery**を作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="e6393-193">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="e6393-194">Dependent 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-194">Dependent Element (SSDL)</span></span>

<span data-ttu-id="e6393-195">ストアスキーマ定義言語 (SSDL) の**dependent**要素は、外部キー制約の依存 end (参照制約とも呼ばれます) を定義する、参照要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="e6393-195">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="e6393-196">**Dependent**要素は、主キー列 (または列) を参照するテーブルの列 (または列) を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-196">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="e6393-197">**Propertyref**要素は、参照される列を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-197">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="e6393-198">Principal 要素は、 **Dependent**要素で指定された列によって参照される主キー列を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-198">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="e6393-199">**Dependent**要素には、次の子要素を含めることができます (順に記載されています)。</span><span class="sxs-lookup"><span data-stu-id="e6393-199">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-200">PropertyRef (1 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-200">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="e6393-201">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-201">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-202">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-202">Applicable Attributes</span></span>

<span data-ttu-id="e6393-203">次の表では、**依存**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-203">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="e6393-204">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-204">Attribute Name</span></span> | <span data-ttu-id="e6393-205">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-205">Is Required</span></span> | <span data-ttu-id="e6393-206">値</span><span class="sxs-lookup"><span data-stu-id="e6393-206">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-207">**ロール**</span><span class="sxs-lookup"><span data-stu-id="e6393-207">**Role**</span></span>       | <span data-ttu-id="e6393-208">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-208">Yes</span></span>         | <span data-ttu-id="e6393-209">対応する End 要素の**Role**属性と同じ値 (使用されている場合)。それ以外の場合は、参照している列を含むテーブルの名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-209">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="e6393-210">任意の数の annotation 属性 (カスタム XML 属性) を**依存**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-210">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="e6393-211">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-211">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e6393-212">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-212">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-213">例</span><span class="sxs-lookup"><span data-stu-id="e6393-213">Example</span></span>

<span data-ttu-id="e6393-214">次の例では、Association**要素を使用**して、 **FK\_CustomerOrders** foreign key 制約に参加する列を指定しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-214">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="e6393-215">**Dependent**要素は、 **Order**テーブルの**CustomerId**列を制約の依存 end として指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-215">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

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

## <a name="documentation-element-ssdl"></a><span data-ttu-id="e6393-216">Documentation 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-216">Documentation Element (SSDL)</span></span>

<span data-ttu-id="e6393-217">ストアスキーマ定義言語 (SSDL) の**Documentation**要素は、親要素で定義されているオブジェクトに関する情報を提供するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-217">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="e6393-218">**Documentation**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="e6393-218">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-219">**Summary**: 親要素の簡単な説明。</span><span class="sxs-lookup"><span data-stu-id="e6393-219">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="e6393-220">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="e6393-220">(zero or one element)</span></span>
-   <span data-ttu-id="e6393-221">**Longdescription**: 親要素の詳細な説明。</span><span class="sxs-lookup"><span data-stu-id="e6393-221">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="e6393-222">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="e6393-222">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-223">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-223">Applicable Attributes</span></span>

<span data-ttu-id="e6393-224">**Documentation**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-224">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="e6393-225">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-225">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e6393-226">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-226">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-227">例</span><span class="sxs-lookup"><span data-stu-id="e6393-227">Example</span></span>

<span data-ttu-id="e6393-228">次の例は、EntityType 要素の子要素としての**Documentation**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-228">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

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

## <a name="end-element-ssdl"></a><span data-ttu-id="e6393-229">End 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-229">End Element (SSDL)</span></span>

<span data-ttu-id="e6393-230">ストアスキーマ定義言語 (SSDL) の**end**要素は、基になるデータベースの外部キー制約の一方の端にあるテーブルと行数を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-230">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="e6393-231">**End**要素は、Association 要素または AssociationSet 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="e6393-231">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="e6393-232">それぞれの場合において、使用可能な子要素と該当する属性は異なります。</span><span class="sxs-lookup"><span data-stu-id="e6393-232">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="e6393-233">Association 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="e6393-233">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="e6393-234">**End**要素 ( **Association**要素の子として) では、外部キー制約の最後に**型**と**多重度**の属性を使用して、テーブルと行の数を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-234">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="e6393-235">外部キー制約の End は、SSDL アソシエーションの一部として定義されます。SSDL アソシエーションには End が 2 つ必要です。</span><span class="sxs-lookup"><span data-stu-id="e6393-235">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="e6393-236">**終了**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="e6393-236">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-237">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="e6393-237">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e6393-238">OnDelete (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="e6393-238">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="e6393-239">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="e6393-239">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="e6393-240">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-240">Applicable Attributes</span></span>

<span data-ttu-id="e6393-241">次の表では、 **End**要素が**Association**要素の子である場合に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-241">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="e6393-242">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-242">Attribute Name</span></span>   | <span data-ttu-id="e6393-243">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-243">Is Required</span></span> | <span data-ttu-id="e6393-244">値</span><span class="sxs-lookup"><span data-stu-id="e6393-244">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-245">**型**</span><span class="sxs-lookup"><span data-stu-id="e6393-245">**Type**</span></span>         | <span data-ttu-id="e6393-246">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-246">Yes</span></span>         | <span data-ttu-id="e6393-247">外部キー制約の End にある SSDL エンティティ セットの完全修飾名。</span><span class="sxs-lookup"><span data-stu-id="e6393-247">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="e6393-248">**ロール**</span><span class="sxs-lookup"><span data-stu-id="e6393-248">**Role**</span></span>         | <span data-ttu-id="e6393-249">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-249">No</span></span>          | <span data-ttu-id="e6393-250">対応する参照エンティティ Alconstraint 要素の Principal または Dependent 要素の**Role**属性の値 (使用されている場合)。</span><span class="sxs-lookup"><span data-stu-id="e6393-250">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="e6393-251">**要素**</span><span class="sxs-lookup"><span data-stu-id="e6393-251">**Multiplicity**</span></span> | <span data-ttu-id="e6393-252">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-252">Yes</span></span>         | <span data-ttu-id="e6393-253">**1**, **0 ..1**,、または、foreign key 制約の最後に配置できる行の数に応じて **\*** します。</span><span class="sxs-lookup"><span data-stu-id="e6393-253">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="e6393-254">**1**は、foreign key 制約 end に1行だけ存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="e6393-254">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="e6393-255">**0 ..1**は、foreign key 制約 end に0行または1行が存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="e6393-255">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="e6393-256">**\*** は、外部キー制約の end に0行、1行、または複数の行が存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="e6393-256">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="e6393-257">任意の数の annotation 属性 (カスタム XML 属性) を**終了**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-257">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="e6393-258">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-258">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e6393-259">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-259">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="e6393-260">例</span><span class="sxs-lookup"><span data-stu-id="e6393-260">Example</span></span>

<span data-ttu-id="e6393-261">次の例は、 **FK\_CustomerOrders** foreign key 制約を定義する**Association**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-261">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="e6393-262">各**End**要素に指定された複数要素の接続**性**の値は、 **Orders**テーブルの行を**customers**テーブルの行に関連付けることができることを示します。ただし、 **orders**テーブルの行に関連付けることができるのは、 **customers**テーブル内の1つの行だけです。</span><span class="sxs-lookup"><span data-stu-id="e6393-262">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="e6393-263">また、 **OnDelete**要素は **、customers テーブル**内の行が削除された場合に、 **customers**テーブル内の特定の行を参照する**Orders**テーブル内のすべての行が削除されることを示します。</span><span class="sxs-lookup"><span data-stu-id="e6393-263">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="e6393-264">AssociationSet 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="e6393-264">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="e6393-265">**終了**要素 ( **AssociationSet**要素の子として) は、基になるデータベース内の foreign key 制約の一方の端にあるテーブルを指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-265">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="e6393-266">**終了**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="e6393-266">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-267">ドキュメント (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="e6393-267">Documentation (zero or one)</span></span>
-   <span data-ttu-id="e6393-268">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-268">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="e6393-269">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-269">Applicable Attributes</span></span>

<span data-ttu-id="e6393-270">次の表では、 **AssociationSet**要素の子である場合に**End**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-270">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="e6393-271">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-271">Attribute Name</span></span> | <span data-ttu-id="e6393-272">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-272">Is Required</span></span> | <span data-ttu-id="e6393-273">値</span><span class="sxs-lookup"><span data-stu-id="e6393-273">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-274">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="e6393-274">**EntitySet**</span></span>  | <span data-ttu-id="e6393-275">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-275">Yes</span></span>         | <span data-ttu-id="e6393-276">外部キー制約の End にある SSDL エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-276">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="e6393-277">**ロール**</span><span class="sxs-lookup"><span data-stu-id="e6393-277">**Role**</span></span>       | <span data-ttu-id="e6393-278">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-278">No</span></span>          | <span data-ttu-id="e6393-279">対応する Association 要素の1つの**End**要素に指定された**ロール**属性の1つの値。</span><span class="sxs-lookup"><span data-stu-id="e6393-279">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="e6393-280">任意の数の annotation 属性 (カスタム XML 属性) を**終了**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-280">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="e6393-281">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-281">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e6393-282">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-282">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="e6393-283">例</span><span class="sxs-lookup"><span data-stu-id="e6393-283">Example</span></span>

<span data-ttu-id="e6393-284">次の例は、2つの**End**要素を持つ**AssociationSet**要素を持つ**EntityContainer**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-284">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

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

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="e6393-285">EntityContainer 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-285">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="e6393-286">ストアスキーマ定義言語 (SSDL) の**EntityContainer**要素は、Entity Framework アプリケーション内の基になるデータソースの構造を記述します。 ssdl エンティティセット (EntitySet 要素で定義) は、データベース内のテーブルを表し、ssdl エンティティ型 (EntityType 要素で定義) はテーブル内の行を表し、(AssociationSet 要素で定義された) アソシエーションセットはデータベース内の外部キー</span><span class="sxs-lookup"><span data-stu-id="e6393-286">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="e6393-287">ストレージ モデルのエンティティ コンテナーは、EntityContainerMapping 要素を通じて概念モデルのエンティティ コンテナーにマップされます。</span><span class="sxs-lookup"><span data-stu-id="e6393-287">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="e6393-288">**EntityContainer**要素には、0個または1個の Documentation 要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e6393-288">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="e6393-289">**Documentation**要素が存在する場合は、他のすべての子要素の前に置く必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-289">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="e6393-290">**EntityContainer**要素には、次の子要素を0個以上含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="e6393-290">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-291">EntitySet</span><span class="sxs-lookup"><span data-stu-id="e6393-291">EntitySet</span></span>
-   <span data-ttu-id="e6393-292">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="e6393-292">AssociationSet</span></span>
-   <span data-ttu-id="e6393-293">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="e6393-293">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-294">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-294">Applicable Attributes</span></span>

<span data-ttu-id="e6393-295">次の表は、 **EntityContainer**要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-295">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="e6393-296">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-296">Attribute Name</span></span> | <span data-ttu-id="e6393-297">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-297">Is Required</span></span> | <span data-ttu-id="e6393-298">値</span><span class="sxs-lookup"><span data-stu-id="e6393-298">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="e6393-299">**名前**</span><span class="sxs-lookup"><span data-stu-id="e6393-299">**Name**</span></span>       | <span data-ttu-id="e6393-300">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-300">Yes</span></span>         | <span data-ttu-id="e6393-301">エンティティ コンテナー名。</span><span class="sxs-lookup"><span data-stu-id="e6393-301">The name of the entity container.</span></span> <span data-ttu-id="e6393-302">この名前にピリオド (.) を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="e6393-302">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="e6393-303">任意の数の annotation 属性 (カスタム XML 属性) を**EntityContainer**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-303">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="e6393-304">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-304">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="e6393-305">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-305">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-306">例</span><span class="sxs-lookup"><span data-stu-id="e6393-306">Example</span></span>

<span data-ttu-id="e6393-307">次の例は、2つのエンティティセットと1つのアソシエーションセットを定義する**EntityContainer**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-307">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="e6393-308">エンティティ型およびアソシエーション型の名前は、概念モデルの名前空間名によって修飾されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-308">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

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

## <a name="entityset-element-ssdl"></a><span data-ttu-id="e6393-309">EntitySet 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-309">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="e6393-310">ストアスキーマ定義言語 (SSDL) の**EntitySet**要素は、基になるデータベースのテーブルまたはビューを表します。</span><span class="sxs-lookup"><span data-stu-id="e6393-310">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="e6393-311">SSDL の EntityType 要素は、テーブルまたはビュー内の行を表します。</span><span class="sxs-lookup"><span data-stu-id="e6393-311">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="e6393-312">**EntitySet**要素の**EntityType**属性は、ssdl エンティティセット内の行を表す特定の ssdl エンティティ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-312">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="e6393-313">CSDL エンティティ セットと SSDL エンティティ セットの間のマッピングは、EntitySetMapping 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-313">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="e6393-314">**EntitySet**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="e6393-314">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-315">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="e6393-315">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e6393-316">DefiningQuery (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="e6393-316">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="e6393-317">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="e6393-317">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-318">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-318">Applicable Attributes</span></span>

<span data-ttu-id="e6393-319">次の表では、 **EntitySet**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-319">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="e6393-320">一部の属性 (ここには記載されていません) は、**ストア**エイリアスで修飾できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-320">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="e6393-321">このような属性は、モデルの更新時にモデルの更新ウィザードで使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-321">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="e6393-322">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-322">Attribute Name</span></span> | <span data-ttu-id="e6393-323">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-323">Is Required</span></span> | <span data-ttu-id="e6393-324">値</span><span class="sxs-lookup"><span data-stu-id="e6393-324">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-325">**名前**</span><span class="sxs-lookup"><span data-stu-id="e6393-325">**Name**</span></span>       | <span data-ttu-id="e6393-326">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-326">Yes</span></span>         | <span data-ttu-id="e6393-327">エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-327">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="e6393-328">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="e6393-328">**EntityType**</span></span> | <span data-ttu-id="e6393-329">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-329">Yes</span></span>         | <span data-ttu-id="e6393-330">エンティティ型の完全修飾名。そのインスタンスは、エンティティ セットに格納されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-330">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="e6393-331">**スキーマ**</span><span class="sxs-lookup"><span data-stu-id="e6393-331">**Schema**</span></span>     | <span data-ttu-id="e6393-332">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-332">No</span></span>          | <span data-ttu-id="e6393-333">データベース スキーマ。</span><span class="sxs-lookup"><span data-stu-id="e6393-333">The database schema.</span></span>                                                                     |
| <span data-ttu-id="e6393-334">**テーブル**</span><span class="sxs-lookup"><span data-stu-id="e6393-334">**Table**</span></span>      | <span data-ttu-id="e6393-335">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-335">No</span></span>          | <span data-ttu-id="e6393-336">データベース テーブル。</span><span class="sxs-lookup"><span data-stu-id="e6393-336">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="e6393-337">**EntitySet**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-337">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="e6393-338">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-338">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="e6393-339">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-339">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-340">例</span><span class="sxs-lookup"><span data-stu-id="e6393-340">Example</span></span>

<span data-ttu-id="e6393-341">次の例は、2つの**EntitySet**要素と1つの**AssociationSet**要素を持つ**EntityContainer**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-341">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

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

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="e6393-342">EntityType 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-342">EntityType Element (SSDL)</span></span>

<span data-ttu-id="e6393-343">ストアスキーマ定義言語 (SSDL) の**EntityType**要素は、基になるデータベースのテーブルまたはビューの行を表します。</span><span class="sxs-lookup"><span data-stu-id="e6393-343">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="e6393-344">SSDL の EntitySet 要素は、行が発生するテーブルまたはビューを表します。</span><span class="sxs-lookup"><span data-stu-id="e6393-344">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="e6393-345">**EntitySet**要素の**EntityType**属性は、ssdl エンティティセット内の行を表す特定の ssdl エンティティ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-345">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="e6393-346">SSDL のエンティティ型と CSDL のエンティティ型 間のマッピングは、EntityTypeMapping 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-346">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="e6393-347">**EntityType**要素には、次の子要素を含めることができます (この順序で表示されます)。</span><span class="sxs-lookup"><span data-stu-id="e6393-347">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-348">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="e6393-348">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="e6393-349">Key (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="e6393-349">Key (zero or one element)</span></span>
-   <span data-ttu-id="e6393-350">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="e6393-350">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-351">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-351">Applicable Attributes</span></span>

<span data-ttu-id="e6393-352">次の表では、 **EntityType**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-352">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="e6393-353">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-353">Attribute Name</span></span> | <span data-ttu-id="e6393-354">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-354">Is Required</span></span> | <span data-ttu-id="e6393-355">値</span><span class="sxs-lookup"><span data-stu-id="e6393-355">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-356">**名前**</span><span class="sxs-lookup"><span data-stu-id="e6393-356">**Name**</span></span>       | <span data-ttu-id="e6393-357">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-357">Yes</span></span>         | <span data-ttu-id="e6393-358">エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-358">The name of the entity type.</span></span> <span data-ttu-id="e6393-359">通常、この値は、エンティティ型が行を表すテーブルの名前と同じです。</span><span class="sxs-lookup"><span data-stu-id="e6393-359">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="e6393-360">この値にピリオド (.) を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="e6393-360">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="e6393-361">任意の数の annotation 属性 (カスタム XML 属性) を**EntityType**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-361">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="e6393-362">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-362">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="e6393-363">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-363">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-364">例</span><span class="sxs-lookup"><span data-stu-id="e6393-364">Example</span></span>

<span data-ttu-id="e6393-365">次の例は、2つのプロパティを持つ**EntityType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-365">The following example shows an **EntityType** element with two properties:</span></span>

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

## <a name="function-element-ssdl"></a><span data-ttu-id="e6393-366">Function 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-366">Function Element (SSDL)</span></span>

<span data-ttu-id="e6393-367">ストアスキーマ定義言語 (SSDL) の**Function**要素は、基になるデータベースに存在するストアドプロシージャを指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-367">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="e6393-368">**Function**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="e6393-368">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-369">ドキュメント (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="e6393-369">Documentation (zero or one)</span></span>
-   <span data-ttu-id="e6393-370">Parameter (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-370">Parameter (zero or more)</span></span>
-   <span data-ttu-id="e6393-371">CommandText (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="e6393-371">CommandText (zero or one)</span></span>
-   <span data-ttu-id="e6393-372">ReturnType (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-372">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="e6393-373">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-373">Annotation elements (zero or more)</span></span>

<span data-ttu-id="e6393-374">関数の戻り値の型は、 **returntype**要素または**returntype**属性 (下記参照) のいずれかで指定する必要がありますが、両方を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="e6393-374">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="e6393-375">ストレージ モデル内で指定されるストアド プロシージャは、アプリケーションの概念モデルにインポートできます。</span><span class="sxs-lookup"><span data-stu-id="e6393-375">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="e6393-376">詳細については、「[ストアドプロシージャを使用したクエリ](~/ef6/modeling/designer/stored-procedures/query.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6393-376">For more information, see [Querying with Stored Procedures](~/ef6/modeling/designer/stored-procedures/query.md).</span></span> <span data-ttu-id="e6393-377">**Function**要素を使用して、ストレージモデルでカスタム関数を定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="e6393-377">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-378">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-378">Applicable Attributes</span></span>

<span data-ttu-id="e6393-379">次の表では、 **Function**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-379">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="e6393-380">一部の属性 (ここには記載されていません) は、**ストア**エイリアスで修飾できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-380">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="e6393-381">このような属性は、モデルの更新時にモデルの更新ウィザードで使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-381">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="e6393-382">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-382">Attribute Name</span></span>             | <span data-ttu-id="e6393-383">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-383">Is Required</span></span> | <span data-ttu-id="e6393-384">値</span><span class="sxs-lookup"><span data-stu-id="e6393-384">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-385">**名前**</span><span class="sxs-lookup"><span data-stu-id="e6393-385">**Name**</span></span>                   | <span data-ttu-id="e6393-386">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-386">Yes</span></span>         | <span data-ttu-id="e6393-387">ストアド プロシージャの名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-387">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="e6393-388">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="e6393-388">**ReturnType**</span></span>             | <span data-ttu-id="e6393-389">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-389">No</span></span>          | <span data-ttu-id="e6393-390">ストアド プロシージャの戻り値の型。</span><span class="sxs-lookup"><span data-stu-id="e6393-390">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="e6393-391">**Aggregate**</span><span class="sxs-lookup"><span data-stu-id="e6393-391">**Aggregate**</span></span>              | <span data-ttu-id="e6393-392">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-392">No</span></span>          | <span data-ttu-id="e6393-393">ストアドプロシージャから集計値が返される場合は**True**を指定します。それ以外の場合は**False**。</span><span class="sxs-lookup"><span data-stu-id="e6393-393">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="e6393-394">**BuiltIn**</span><span class="sxs-lookup"><span data-stu-id="e6393-394">**BuiltIn**</span></span>                | <span data-ttu-id="e6393-395">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-395">No</span></span>          | <span data-ttu-id="e6393-396">関数が組み込みの<sup>1</sup>関数の場合は**True** 。それ以外の場合は**False**。</span><span class="sxs-lookup"><span data-stu-id="e6393-396">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="e6393-397">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="e6393-397">**StoreFunctionName**</span></span>      | <span data-ttu-id="e6393-398">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-398">No</span></span>          | <span data-ttu-id="e6393-399">ストアド プロシージャの名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-399">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="e6393-400">**いる niladicfunction**</span><span class="sxs-lookup"><span data-stu-id="e6393-400">**NiladicFunction**</span></span>        | <span data-ttu-id="e6393-401">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-401">No</span></span>          | <span data-ttu-id="e6393-402">関数がニラディック<sup>2</sup>関数の場合は**True**を返します。それ以外の場合は**False** 。</span><span class="sxs-lookup"><span data-stu-id="e6393-402">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="e6393-403">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="e6393-403">**IsComposable**</span></span>           | <span data-ttu-id="e6393-404">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-404">No</span></span>          | <span data-ttu-id="e6393-405">関数がコンポーザブルな<sup>3</sup>関数の場合は**True** 。それ以外の場合は**False** 。</span><span class="sxs-lookup"><span data-stu-id="e6393-405">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="e6393-406">**ParameterTypeSemantics**</span><span class="sxs-lookup"><span data-stu-id="e6393-406">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="e6393-407">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-407">No</span></span>          | <span data-ttu-id="e6393-408">関数オーバーロードの解決に使用される型のセマンティクスを定義する列挙。</span><span class="sxs-lookup"><span data-stu-id="e6393-408">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="e6393-409">列挙はプロバイダー マニフェストで関数定義によって定義されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-409">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="e6393-410">既定値は**AllowImplicitConversion**です。</span><span class="sxs-lookup"><span data-stu-id="e6393-410">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="e6393-411">**スキーマ**</span><span class="sxs-lookup"><span data-stu-id="e6393-411">**Schema**</span></span>                 | <span data-ttu-id="e6393-412">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-412">No</span></span>          | <span data-ttu-id="e6393-413">ストアド プロシージャが定義されているスキーマの名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-413">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="e6393-414"><sup>1</sup>組み込み関数は、データベースで定義されている関数です。</span><span class="sxs-lookup"><span data-stu-id="e6393-414"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="e6393-415">ストレージモデルで定義されている関数の詳細については、「CommandText 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6393-415">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="e6393-416"><sup>2</sup>ニラディック関数は、パラメーターを受け入れず、呼び出されたときにかっこを必要としない関数です。</span><span class="sxs-lookup"><span data-stu-id="e6393-416"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="e6393-417"><sup>3</sup> 2 つの関数の出力を他の関数の入力として使用できる場合、2つの関数はコンポーザブルです。</span><span class="sxs-lookup"><span data-stu-id="e6393-417"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="e6393-418">**関数**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-418">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="e6393-419">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-419">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="e6393-420">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-420">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-421">例</span><span class="sxs-lookup"><span data-stu-id="e6393-421">Example</span></span>

<span data-ttu-id="e6393-422">次の例は、 **Updateorderquantity**ストアドプロシージャに対応する**関数**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-422">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="e6393-423">このストアド プロシージャは 2 個のパラメーターを受け入れ、値を返しません。</span><span class="sxs-lookup"><span data-stu-id="e6393-423">The stored procedure accepts two parameters and does not return a value.</span></span>

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

## <a name="key-element-ssdl"></a><span data-ttu-id="e6393-424">key 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-424">Key Element (SSDL)</span></span>

<span data-ttu-id="e6393-425">ストアスキーマ定義言語 (SSDL) の**key**要素は、基になるデータベース内のテーブルの主キーを表します。</span><span class="sxs-lookup"><span data-stu-id="e6393-425">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="e6393-426">**Key**は EntityType 要素の子要素であり、テーブル内の行を表します。</span><span class="sxs-lookup"><span data-stu-id="e6393-426">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="e6393-427">主キーは、 **EntityType**要素で定義されている1つ以上の Property 要素を参照することによって、 **key**要素で定義されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-427">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="e6393-428">**Key**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="e6393-428">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-429">PropertyRef (1 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-429">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="e6393-430">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="e6393-430">Annotation elements</span></span>

<span data-ttu-id="e6393-431">**キー**要素に適用できる属性はありません。</span><span class="sxs-lookup"><span data-stu-id="e6393-431">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-432">例</span><span class="sxs-lookup"><span data-stu-id="e6393-432">Example</span></span>

<span data-ttu-id="e6393-433">次の例は、1つのプロパティを参照するキーを持つ**EntityType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-433">The following example shows an **EntityType** element with a key that references one property:</span></span>

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

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="e6393-434">OnDelete 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-434">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="e6393-435">ストアスキーマ定義言語 (SSDL) の**OnDelete**要素は、foreign key 制約に参加している行が削除された場合にデータベースの動作を反映します。</span><span class="sxs-lookup"><span data-stu-id="e6393-435">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="e6393-436">アクションが**Cascade**に設定されている場合、削除される行を参照する行も削除されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-436">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="e6393-437">アクションが**None**に設定されている場合、削除される行を参照する行も削除されません。</span><span class="sxs-lookup"><span data-stu-id="e6393-437">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="e6393-438">**OnDelete**要素は、End 要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="e6393-438">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="e6393-439">**OnDelete**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="e6393-439">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-440">ドキュメント (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="e6393-440">Documentation (zero or one)</span></span>
-   <span data-ttu-id="e6393-441">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-441">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-442">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-442">Applicable Attributes</span></span>

<span data-ttu-id="e6393-443">次の表では、 **OnDelete**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-443">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="e6393-444">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-444">Attribute Name</span></span> | <span data-ttu-id="e6393-445">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-445">Is Required</span></span> | <span data-ttu-id="e6393-446">値</span><span class="sxs-lookup"><span data-stu-id="e6393-446">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-447">**動作**</span><span class="sxs-lookup"><span data-stu-id="e6393-447">**Action**</span></span>     | <span data-ttu-id="e6393-448">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-448">Yes</span></span>         | <span data-ttu-id="e6393-449">**Cascade**または**None**。</span><span class="sxs-lookup"><span data-stu-id="e6393-449">**Cascade** or **None**.</span></span> <span data-ttu-id="e6393-450">(**制限**値は有効ですが、動作は**None**と同じです)。</span><span class="sxs-lookup"><span data-stu-id="e6393-450">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="e6393-451">**OnDelete**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-451">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="e6393-452">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-452">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="e6393-453">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-453">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-454">例</span><span class="sxs-lookup"><span data-stu-id="e6393-454">Example</span></span>

<span data-ttu-id="e6393-455">次の例は、 **FK\_CustomerOrders** foreign key 制約を定義する**Association**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-455">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="e6393-456">**OnDelete**要素は **、customers テーブル**内の行が削除された場合に、 **customers**テーブル内の特定の行を参照する**Orders**テーブル内のすべての行が削除されることを示します。</span><span class="sxs-lookup"><span data-stu-id="e6393-456">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

## <a name="parameter-element-ssdl"></a><span data-ttu-id="e6393-457">Parameter 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-457">Parameter Element (SSDL)</span></span>

<span data-ttu-id="e6393-458">ストアスキーマ定義言語 (SSDL) の**Parameter**要素は、データベース内のストアドプロシージャのパラメーターを指定する Function 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="e6393-458">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="e6393-459">**Parameter**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="e6393-459">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-460">ドキュメント (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="e6393-460">Documentation (zero or one)</span></span>
-   <span data-ttu-id="e6393-461">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-461">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-462">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-462">Applicable Attributes</span></span>

<span data-ttu-id="e6393-463">次の表では、 **Parameter**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-463">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="e6393-464">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-464">Attribute Name</span></span> | <span data-ttu-id="e6393-465">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-465">Is Required</span></span> | <span data-ttu-id="e6393-466">値</span><span class="sxs-lookup"><span data-stu-id="e6393-466">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-467">**名前**</span><span class="sxs-lookup"><span data-stu-id="e6393-467">**Name**</span></span>       | <span data-ttu-id="e6393-468">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-468">Yes</span></span>         | <span data-ttu-id="e6393-469">パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-469">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="e6393-470">**型**</span><span class="sxs-lookup"><span data-stu-id="e6393-470">**Type**</span></span>       | <span data-ttu-id="e6393-471">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-471">Yes</span></span>         | <span data-ttu-id="e6393-472">パラメーターの型。</span><span class="sxs-lookup"><span data-stu-id="e6393-472">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="e6393-473">**モード**</span><span class="sxs-lookup"><span data-stu-id="e6393-473">**Mode**</span></span>       | <span data-ttu-id="e6393-474">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-474">No</span></span>          | <span data-ttu-id="e6393-475">**In**、**アウト**、または**InOut**パラメーターは、入力、出力、または入力/出力パラメーターかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="e6393-475">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="e6393-476">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="e6393-476">**MaxLength**</span></span>  | <span data-ttu-id="e6393-477">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-477">No</span></span>          | <span data-ttu-id="e6393-478">パラメーターの最大長。</span><span class="sxs-lookup"><span data-stu-id="e6393-478">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="e6393-479">**Precision**</span><span class="sxs-lookup"><span data-stu-id="e6393-479">**Precision**</span></span>  | <span data-ttu-id="e6393-480">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-480">No</span></span>          | <span data-ttu-id="e6393-481">パラメーターの有効桁数。</span><span class="sxs-lookup"><span data-stu-id="e6393-481">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="e6393-482">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="e6393-482">**Scale**</span></span>      | <span data-ttu-id="e6393-483">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-483">No</span></span>          | <span data-ttu-id="e6393-484">パラメーターの小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="e6393-484">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="e6393-485">**SRID**</span><span class="sxs-lookup"><span data-stu-id="e6393-485">**SRID**</span></span>       | <span data-ttu-id="e6393-486">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-486">No</span></span>          | <span data-ttu-id="e6393-487">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="e6393-487">Spatial System Reference Identifier.</span></span> <span data-ttu-id="e6393-488">空間型のパラメーターに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="e6393-488">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="e6393-489">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6393-489">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="e6393-490">**パラメーター**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-490">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="e6393-491">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-491">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="e6393-492">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-492">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-493">例</span><span class="sxs-lookup"><span data-stu-id="e6393-493">Example</span></span>

<span data-ttu-id="e6393-494">次の例は、入力パラメーターを指定する2つの**パラメーター**要素を持つ**関数**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-494">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

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

## <a name="principal-element-ssdl"></a><span data-ttu-id="e6393-495">Principal 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-495">Principal Element (SSDL)</span></span>

<span data-ttu-id="e6393-496">ストアスキーマ定義言語 (SSDL) の**principal**要素は、外部キー制約 (参照制約とも呼ばれます) のプリンシパル end を定義する、参照要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="e6393-496">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="e6393-497">**Principal**要素は、別の列 (または列) によって参照されるテーブル内の主キー列 (または列) を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-497">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="e6393-498">**Propertyref**要素は、参照される列を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-498">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="e6393-499">Dependent 要素は、 **Principal**要素で指定されている主キー列を参照する列を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-499">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="e6393-500">**Principal**要素には、次の子要素を含めることができます (順に記載されています)。</span><span class="sxs-lookup"><span data-stu-id="e6393-500">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="e6393-501">PropertyRef (1 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-501">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="e6393-502">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-502">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-503">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-503">Applicable Attributes</span></span>

<span data-ttu-id="e6393-504">次の表では、 **Principal**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-504">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="e6393-505">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-505">Attribute Name</span></span> | <span data-ttu-id="e6393-506">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-506">Is Required</span></span> | <span data-ttu-id="e6393-507">値</span><span class="sxs-lookup"><span data-stu-id="e6393-507">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-508">**ロール**</span><span class="sxs-lookup"><span data-stu-id="e6393-508">**Role**</span></span>       | <span data-ttu-id="e6393-509">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-509">Yes</span></span>         | <span data-ttu-id="e6393-510">対応する End 要素の**Role**属性と同じ値 (使用されている場合)。それ以外の場合は、参照先の列を含むテーブルの名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-510">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="e6393-511">**Principal**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-511">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="e6393-512">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-512">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e6393-513">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-513">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-514">例</span><span class="sxs-lookup"><span data-stu-id="e6393-514">Example</span></span>

<span data-ttu-id="e6393-515">次の例では、Association**要素を使用**して、 **FK\_CustomerOrders** foreign key 制約に参加する列を指定しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-515">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="e6393-516">**Principal**要素は、制約のプリンシパル end として**Customer**テーブルの**CustomerId**列を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-516">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

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

## <a name="property-element-ssdl"></a><span data-ttu-id="e6393-517">Property 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-517">Property Element (SSDL)</span></span>

<span data-ttu-id="e6393-518">ストアスキーマ定義言語 (SSDL) の**Property**要素は、基になるデータベース内のテーブルの列を表します。</span><span class="sxs-lookup"><span data-stu-id="e6393-518">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="e6393-519">**プロパティ**要素は、テーブル内の行を表す EntityType 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="e6393-519">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="e6393-520">**EntityType**要素で定義されている各**Property**要素は、列を表します。</span><span class="sxs-lookup"><span data-stu-id="e6393-520">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="e6393-521">**Property**要素に子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="e6393-521">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-522">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-522">Applicable Attributes</span></span>

<span data-ttu-id="e6393-523">次の表では、 **Property**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-523">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="e6393-524">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-524">Attribute Name</span></span>            | <span data-ttu-id="e6393-525">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-525">Is Required</span></span> | <span data-ttu-id="e6393-526">値</span><span class="sxs-lookup"><span data-stu-id="e6393-526">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-527">**名前**</span><span class="sxs-lookup"><span data-stu-id="e6393-527">**Name**</span></span>                  | <span data-ttu-id="e6393-528">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-528">Yes</span></span>         | <span data-ttu-id="e6393-529">対応する列の名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-529">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="e6393-530">**型**</span><span class="sxs-lookup"><span data-stu-id="e6393-530">**Type**</span></span>                  | <span data-ttu-id="e6393-531">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-531">Yes</span></span>         | <span data-ttu-id="e6393-532">対応する列の種類。</span><span class="sxs-lookup"><span data-stu-id="e6393-532">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="e6393-533">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="e6393-533">**Nullable**</span></span>              | <span data-ttu-id="e6393-534">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-534">No</span></span>          | <span data-ttu-id="e6393-535">対応する列が null 値を持つことができるかどうかによって、 **True** (既定値) または**False** 。</span><span class="sxs-lookup"><span data-stu-id="e6393-535">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="e6393-536">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="e6393-536">**DefaultValue**</span></span>          | <span data-ttu-id="e6393-537">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-537">No</span></span>          | <span data-ttu-id="e6393-538">対応する列の既定値。</span><span class="sxs-lookup"><span data-stu-id="e6393-538">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="e6393-539">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="e6393-539">**MaxLength**</span></span>             | <span data-ttu-id="e6393-540">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-540">No</span></span>          | <span data-ttu-id="e6393-541">対応する列の最大長。</span><span class="sxs-lookup"><span data-stu-id="e6393-541">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="e6393-542">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="e6393-542">**FixedLength**</span></span>           | <span data-ttu-id="e6393-543">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-543">No</span></span>          | <span data-ttu-id="e6393-544">対応する列の値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="e6393-544">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="e6393-545">**Precision**</span><span class="sxs-lookup"><span data-stu-id="e6393-545">**Precision**</span></span>             | <span data-ttu-id="e6393-546">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-546">No</span></span>          | <span data-ttu-id="e6393-547">対応する列の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="e6393-547">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="e6393-548">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="e6393-548">**Scale**</span></span>                 | <span data-ttu-id="e6393-549">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-549">No</span></span>          | <span data-ttu-id="e6393-550">対応する列の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="e6393-550">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="e6393-551">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="e6393-551">**Unicode**</span></span>               | <span data-ttu-id="e6393-552">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-552">No</span></span>          | <span data-ttu-id="e6393-553">対応する列の値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="e6393-553">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="e6393-554">**[照合順序]**</span><span class="sxs-lookup"><span data-stu-id="e6393-554">**Collation**</span></span>             | <span data-ttu-id="e6393-555">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-555">No</span></span>          | <span data-ttu-id="e6393-556">データ ソースで使用する照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="e6393-556">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="e6393-557">**SRID**</span><span class="sxs-lookup"><span data-stu-id="e6393-557">**SRID**</span></span>                  | <span data-ttu-id="e6393-558">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-558">No</span></span>          | <span data-ttu-id="e6393-559">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="e6393-559">Spatial System Reference Identifier.</span></span> <span data-ttu-id="e6393-560">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="e6393-560">Valid only for properties of spatial types.</span></span> <span data-ttu-id="e6393-561">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6393-561">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="e6393-562">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="e6393-562">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="e6393-563">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-563">No</span></span>          | <span data-ttu-id="e6393-564">**None**、 **identity** (対応する列の値がデータベースで生成された id である場合)、または**計算**された (対応する列の値がデータベースで計算されている場合)。</span><span class="sxs-lookup"><span data-stu-id="e6393-564">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="e6393-565">RowType プロパティには無効です。</span><span class="sxs-lookup"><span data-stu-id="e6393-565">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="e6393-566">**プロパティ**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-566">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="e6393-567">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-567">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="e6393-568">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-568">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-569">例</span><span class="sxs-lookup"><span data-stu-id="e6393-569">Example</span></span>

<span data-ttu-id="e6393-570">次の例は、2つの子**プロパティ**要素を持つ**EntityType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-570">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

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

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="e6393-571">PropertyRef 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-571">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="e6393-572">ストアスキーマ定義言語 (SSDL) の**Propertyref**要素は、プロパティが次のいずれかのロールを実行することを示すために、EntityType 要素で定義されているプロパティを参照します。</span><span class="sxs-lookup"><span data-stu-id="e6393-572">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="e6393-573">**EntityType**が表すテーブルの主キーの一部です。</span><span class="sxs-lookup"><span data-stu-id="e6393-573">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="e6393-574">1つ以上の**Propertyref**要素を使用して主キーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-574">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="e6393-575">詳細については、「Key 要素」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6393-575">For more information, see Key element.</span></span>
-   <span data-ttu-id="e6393-576">参照に関する制約の依存 End またはプリンシパル End になる。</span><span class="sxs-lookup"><span data-stu-id="e6393-576">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="e6393-577">詳細については、「ReferentialConstraint 要素」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6393-577">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="e6393-578">**Propertyref**要素には、次の子要素のみを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e6393-578">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="e6393-579">ドキュメント (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="e6393-579">Documentation (zero or one)</span></span>
-   <span data-ttu-id="e6393-580">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="e6393-580">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-581">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-581">Applicable Attributes</span></span>

<span data-ttu-id="e6393-582">次の表では、 **Propertyref**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-582">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="e6393-583">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-583">Attribute Name</span></span> | <span data-ttu-id="e6393-584">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-584">Is Required</span></span> | <span data-ttu-id="e6393-585">値</span><span class="sxs-lookup"><span data-stu-id="e6393-585">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="e6393-586">**名前**</span><span class="sxs-lookup"><span data-stu-id="e6393-586">**Name**</span></span>       | <span data-ttu-id="e6393-587">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-587">Yes</span></span>         | <span data-ttu-id="e6393-588">参照されているプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="e6393-588">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="e6393-589">**Propertyref**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-589">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="e6393-590">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-590">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="e6393-591">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-591">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-592">例</span><span class="sxs-lookup"><span data-stu-id="e6393-592">Example</span></span>

<span data-ttu-id="e6393-593">次の例は、 **EntityType**要素で定義されているプロパティを参照することによって主キーを定義するために使用される**propertyref**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-593">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

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

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="e6393-594">ReferentialConstraint 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-594">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="e6393-595">ストアスキーマ定義言語 (SSDL) の参照型の**制約**要素は、基になるデータベースの foreign key 制約 (参照整合性制約とも呼ばれます) を表します。</span><span class="sxs-lookup"><span data-stu-id="e6393-595">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="e6393-596">制約のプリンシパル End と依存 End は、それぞれ Principal 子要素と Dependent 子要素によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-596">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="e6393-597">プリンシパル End と依存 End に参加する列は、PropertyRef 要素で参照します。</span><span class="sxs-lookup"><span data-stu-id="e6393-597">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="e6393-598">この**要素は**、Association 要素の省略可能な子要素です。</span><span class="sxs-lookup"><span data-stu-id="e6393-598">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="e6393-599">**Association**要素で指定されている外部キー制約をマップするために、AssociationSetMapping 要素が使用されていない場合は、これを行うために、**この要素を**使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-599">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="e6393-600">この**要素には、次**の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e6393-600">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="e6393-601">ドキュメント (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="e6393-601">Documentation (zero or one)</span></span>
-   <span data-ttu-id="e6393-602">Principal (1 個)</span><span class="sxs-lookup"><span data-stu-id="e6393-602">Principal (exactly one)</span></span>
-   <span data-ttu-id="e6393-603">依存 (正確に 1)</span><span class="sxs-lookup"><span data-stu-id="e6393-603">Dependent (exactly one)</span></span>
-   <span data-ttu-id="e6393-604">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-604">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-605">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-605">Applicable Attributes</span></span>

<span data-ttu-id="e6393-606">任意の数の annotation 属性 (カスタム XML 属性 **) を、この要素に**適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-606">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="e6393-607">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-607">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="e6393-608">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-608">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-609">例</span><span class="sxs-lookup"><span data-stu-id="e6393-609">Example</span></span>

<span data-ttu-id="e6393-610">次の例では、 **Association** **要素を使用**して、 **FK\_CustomerOrders** foreign key 制約に参加する列を指定しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-610">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="returntype-element-ssdl"></a><span data-ttu-id="e6393-611">ReturnType 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-611">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="e6393-612">ストアスキーマ定義言語 (SSDL) の**ReturnType**要素は、 **function**要素で定義されている関数の戻り値の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-612">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="e6393-613">関数の戻り値の型は、 **ReturnType**属性と共に指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="e6393-613">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="e6393-614">関数の戻り値の型は、 **type**属性または**ReturnType**要素を使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-614">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="e6393-615">**ReturnType**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e6393-615">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="e6393-616">CollectionType (1)</span><span class="sxs-lookup"><span data-stu-id="e6393-616">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="e6393-617">任意の数の annotation 属性 (カスタム XML 属性) を**ReturnType**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-617">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="e6393-618">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-618">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="e6393-619">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-620">例</span><span class="sxs-lookup"><span data-stu-id="e6393-620">Example</span></span>

<span data-ttu-id="e6393-621">次の例では、行のコレクションを返す**関数**を使用します。</span><span class="sxs-lookup"><span data-stu-id="e6393-621">The following example uses a **Function** that returns a collection of rows.</span></span>

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


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="e6393-622">RowType 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-622">RowType Element (SSDL)</span></span>

<span data-ttu-id="e6393-623">ストアスキーマ定義言語 (SSDL) の**RowType**要素は、名前のない構造体を、ストアで定義されている関数の戻り値の型として定義します。</span><span class="sxs-lookup"><span data-stu-id="e6393-623">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="e6393-624">**RowType**要素は**CollectionType**要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="e6393-624">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="e6393-625">**RowType**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e6393-625">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="e6393-626">Property (1 個以上)</span><span class="sxs-lookup"><span data-stu-id="e6393-626">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="e6393-627">例</span><span class="sxs-lookup"><span data-stu-id="e6393-627">Example</span></span>

<span data-ttu-id="e6393-628">次の例では、 **CollectionType**要素を使用して、 **RowType**要素で指定されているように、関数が行のコレクションを返すことを指定するストア関数を示します。</span><span class="sxs-lookup"><span data-stu-id="e6393-628">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


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

## <a name="schema-element-ssdl"></a><span data-ttu-id="e6393-629">Schema 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-629">Schema Element (SSDL)</span></span>

<span data-ttu-id="e6393-630">ストアスキーマ定義言語 (SSDL) の**schema**要素は、ストレージモデル定義のルート要素です。</span><span class="sxs-lookup"><span data-stu-id="e6393-630">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="e6393-631">ストレージ モデルを構成するオブジェクト、関数、およびコンテナーの定義を格納します。</span><span class="sxs-lookup"><span data-stu-id="e6393-631">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="e6393-632">**Schema**要素には、次の子要素を0個以上含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e6393-632">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="e6393-633">アソシエーション</span><span class="sxs-lookup"><span data-stu-id="e6393-633">Association</span></span>
-   <span data-ttu-id="e6393-634">EntityType</span><span class="sxs-lookup"><span data-stu-id="e6393-634">EntityType</span></span>
-   <span data-ttu-id="e6393-635">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="e6393-635">EntityContainer</span></span>
-   <span data-ttu-id="e6393-636">関数</span><span class="sxs-lookup"><span data-stu-id="e6393-636">Function</span></span>

<span data-ttu-id="e6393-637">**Schema**要素は、**名前空間**属性を使用して、ストレージモデル内のエンティティ型とアソシエーションオブジェクトの名前空間を定義します。</span><span class="sxs-lookup"><span data-stu-id="e6393-637">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="e6393-638">1 つの名前空間内で 2 つのオブジェクトが同じ名前を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="e6393-638">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="e6393-639">ストレージモデルの名前空間は、 **Schema**要素の XML 名前空間とは異なります。</span><span class="sxs-lookup"><span data-stu-id="e6393-639">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="e6393-640">ストレージモデルの名前空間 (**名前空間**属性で定義) は、エンティティ型とアソシエーション型の論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="e6393-640">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="e6393-641">**Schema**要素の XML 名前空間 ( **xmlns**属性で示されます) は、 **schema**要素の子要素と属性の既定の名前空間です。</span><span class="sxs-lookup"><span data-stu-id="e6393-641">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="e6393-642">フォームの XML 名前空間 https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl SSDL 用に予約されています (YYYY と MM 表します年と月それぞれ)。</span><span class="sxs-lookup"><span data-stu-id="e6393-642">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="e6393-643">カスタム要素と属性は、このフォームがある名前空間に存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="e6393-643">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="e6393-644">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="e6393-644">Applicable Attributes</span></span>

<span data-ttu-id="e6393-645">次の表では、**スキーマ**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-645">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="e6393-646">属性名</span><span class="sxs-lookup"><span data-stu-id="e6393-646">Attribute Name</span></span>            | <span data-ttu-id="e6393-647">必須</span><span class="sxs-lookup"><span data-stu-id="e6393-647">Is Required</span></span> | <span data-ttu-id="e6393-648">値</span><span class="sxs-lookup"><span data-stu-id="e6393-648">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-649">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="e6393-649">**Namespace**</span></span>             | <span data-ttu-id="e6393-650">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-650">Yes</span></span>         | <span data-ttu-id="e6393-651">ストレージ モデルの名前空間。</span><span class="sxs-lookup"><span data-stu-id="e6393-651">The namespace of the storage model.</span></span> <span data-ttu-id="e6393-652">**名前空間**属性の値は、型の完全修飾名を形成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-652">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="e6393-653">たとえば、 *Customer*という名前の**entitytype**が examplemodel.store 名前空間にある場合、 **entitytype**の完全修飾名は examplemodel.store になります。</span><span class="sxs-lookup"><span data-stu-id="e6393-653">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="e6393-654">次の文字列は、 **Namespace**属性の値として使用できません: **System**、 **Transient**、または**Edm**。</span><span class="sxs-lookup"><span data-stu-id="e6393-654">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="e6393-655">**名前空間**属性の値を、CSDL スキーマ要素の**namespace**属性の値と同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="e6393-655">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="e6393-656">**Alias**</span><span class="sxs-lookup"><span data-stu-id="e6393-656">**Alias**</span></span>                 | <span data-ttu-id="e6393-657">いいえ</span><span class="sxs-lookup"><span data-stu-id="e6393-657">No</span></span>          | <span data-ttu-id="e6393-658">名前空間の名前の代わりに使用される識別子。</span><span class="sxs-lookup"><span data-stu-id="e6393-658">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="e6393-659">たとえば、 *Customer*という名前の**entitytype**が examplemodel.store 名前空間にあり、 **Alias**属性の値が*StorageModel*の場合、 **entitytype**の完全修飾名として StorageModel を使用できます。</span><span class="sxs-lookup"><span data-stu-id="e6393-659">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="e6393-660">**プロバイダー**</span><span class="sxs-lookup"><span data-stu-id="e6393-660">**Provider**</span></span>              | <span data-ttu-id="e6393-661">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-661">Yes</span></span>         | <span data-ttu-id="e6393-662">データ プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="e6393-662">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="e6393-663">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="e6393-663">**ProviderManifestToken**</span></span> | <span data-ttu-id="e6393-664">はい</span><span class="sxs-lookup"><span data-stu-id="e6393-664">Yes</span></span>         | <span data-ttu-id="e6393-665">返すプロバイダー マニフェストをプロバイダーに示すトークン。</span><span class="sxs-lookup"><span data-stu-id="e6393-665">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="e6393-666">トークンの形式は定義されていません。</span><span class="sxs-lookup"><span data-stu-id="e6393-666">No format for the token is defined.</span></span> <span data-ttu-id="e6393-667">トークンの値は、プロバイダーで定義されています。</span><span class="sxs-lookup"><span data-stu-id="e6393-667">Values for the token are defined by the provider.</span></span> <span data-ttu-id="e6393-668">SQL Server プロバイダーマニフェストトークンの詳細については、「SqlClient for Entity Framework」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e6393-668">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="e6393-669">例</span><span class="sxs-lookup"><span data-stu-id="e6393-669">Example</span></span>

<span data-ttu-id="e6393-670">**EntityContainer**要素、2つの**EntityType**要素、および1つの**Association**要素を含む**スキーマ**要素の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e6393-670">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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

## <a name="annotation-attributes"></a><span data-ttu-id="e6393-671">annotation 属性</span><span class="sxs-lookup"><span data-stu-id="e6393-671">Annotation Attributes</span></span>

<span data-ttu-id="e6393-672">ストア スキーマ定義言語 (SSDL) の annotation 属性は、ストレージ モデルの要素に関する追加のメタデータを指定するストレージ モデルのカスタム XML 属性です。</span><span class="sxs-lookup"><span data-stu-id="e6393-672">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="e6393-673">有効な XML 構造が必要であることに加え、annotation 属性には次の制約が適用されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-673">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="e6393-674">annotation 属性は、SSDL 用に予約された XML 名前空間に存在しない。</span><span class="sxs-lookup"><span data-stu-id="e6393-674">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="e6393-675">2 つの任意の annotation 属性の完全修飾名が同じではない。</span><span class="sxs-lookup"><span data-stu-id="e6393-675">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="e6393-676">複数の annotation 属性を特定の 1 つの SSDL 要素に適用することができる。</span><span class="sxs-lookup"><span data-stu-id="e6393-676">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="e6393-677">Annotation 要素に含まれるメタデータには、実行時に、system.string 名前空間のクラスを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="e6393-677">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-678">例</span><span class="sxs-lookup"><span data-stu-id="e6393-678">Example</span></span>

<span data-ttu-id="e6393-679">次の例は、 **OrderId**プロパティに annotation 属性が適用されている EntityType 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-679">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="e6393-680">この例では、 **EntityType**要素に追加された annotation 要素も示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-680">The example also show an annotation element added to the **EntityType** element.</span></span>

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

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="e6393-681">annotation 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-681">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="e6393-682">ストア スキーマ定義言語 (SSDL) の annotation 要素は、ストレージ モデルに関する追加のメタデータを指定するストレージ モデルのカスタム XML 要素です。</span><span class="sxs-lookup"><span data-stu-id="e6393-682">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="e6393-683">有効な XML 構造が必要であることに加え、annotation 要素には次の制約が適用されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-683">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="e6393-684">annotation 要素は、SSDL 用に予約された XML 名前空間内に存在できません。</span><span class="sxs-lookup"><span data-stu-id="e6393-684">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="e6393-685">2 つの annotation 要素の完全修飾名を同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="e6393-685">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="e6393-686">annotation 要素は、特定の SSDL 要素のその他すべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e6393-686">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="e6393-687">複数の annotation 要素を特定の SSDL 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="e6393-687">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="e6393-688">.NET Framework version 4 以降では、annotation 要素に含まれるメタデータに、実行時に、system.string 名前空間のクラスを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="e6393-688">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="e6393-689">例</span><span class="sxs-lookup"><span data-stu-id="e6393-689">Example</span></span>

<span data-ttu-id="e6393-690">Annotation 要素 (**customelement**) を持つ EntityType 要素の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e6393-690">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="e6393-691">また、この例では、 **OrderId**プロパティに適用される annotation 属性も示しています。</span><span class="sxs-lookup"><span data-stu-id="e6393-691">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

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

## <a name="facets-ssdl"></a><span data-ttu-id="e6393-692">ファセット (SSDL)</span><span class="sxs-lookup"><span data-stu-id="e6393-692">Facets (SSDL)</span></span>

<span data-ttu-id="e6393-693">ストア スキーマ定義言語 (SSDL) のファセットは、Property 要素で指定された列型に対する制約を表します。</span><span class="sxs-lookup"><span data-stu-id="e6393-693">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="e6393-694">ファセットは、**プロパティ**要素の XML 属性として実装されます。</span><span class="sxs-lookup"><span data-stu-id="e6393-694">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="e6393-695">次の表では、SSDL でサポートされるファセットについて説明します。</span><span class="sxs-lookup"><span data-stu-id="e6393-695">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="e6393-696">ファセット</span><span class="sxs-lookup"><span data-stu-id="e6393-696">Facet</span></span>           | <span data-ttu-id="e6393-697">説明</span><span class="sxs-lookup"><span data-stu-id="e6393-697">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="e6393-698">**[照合順序]**</span><span class="sxs-lookup"><span data-stu-id="e6393-698">**Collation**</span></span>   | <span data-ttu-id="e6393-699">プロパティの値に対して比較と順序付け操作を行うときに使用する照合シーケンス (または並べ替え順序) を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-699">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="e6393-700">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="e6393-700">**FixedLength**</span></span> | <span data-ttu-id="e6393-701">列値の長さを可変とすることができるかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-701">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="e6393-702">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="e6393-702">**MaxLength**</span></span>   | <span data-ttu-id="e6393-703">列値の最大長を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-703">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="e6393-704">**Precision**</span><span class="sxs-lookup"><span data-stu-id="e6393-704">**Precision**</span></span>   | <span data-ttu-id="e6393-705">**Decimal**型のプロパティの場合、プロパティ値が持つことができる桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-705">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="e6393-706">**Time**、 **DateTime**、および**DateTimeOffset**型のプロパティについては、列の値の秒部分の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-706">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="e6393-707">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="e6393-707">**Scale**</span></span>       | <span data-ttu-id="e6393-708">列値の小数点の右側の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-708">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="e6393-709">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="e6393-709">**Unicode**</span></span>     | <span data-ttu-id="e6393-710">列値を Unicode として保存するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="e6393-710">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
