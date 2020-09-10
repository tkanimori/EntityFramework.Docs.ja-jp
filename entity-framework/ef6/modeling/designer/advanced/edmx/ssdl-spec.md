---
title: SSDL 仕様-EF6
description: Entity Framework 6 の SSDL 仕様
author: divega
ms.date: 10/23/2016
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
uid: ef6/modeling/designer/advanced/edmx/ssdl-spec
ms.openlocfilehash: ab50579649c2e1b19d113cd127e52be995516e27
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620589"
---
# <a name="ssdl-specification"></a><span data-ttu-id="b5b6b-103">SSDL 仕様</span><span class="sxs-lookup"><span data-stu-id="b5b6b-103">SSDL Specification</span></span>
<span data-ttu-id="b5b6b-104">ストア スキーマ定義言語 (SSDL) は、Entity Framework アプリケーションのストレージ モデルを表す XML ベースの言語です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-104">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="b5b6b-105">Entity Framework アプリケーションでは、ストレージモデルのメタデータは、.ssdl (SSDL で記述された) ファイルから StoreItemCollection のインスタンスに読み込まれます。このメタデータには、クラスのメソッドを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-105">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="b5b6b-106">Entity Framework は、ストレージモデルのメタデータを使用して、概念モデルに対するクエリをストア固有のコマンドに変換します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-106">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="b5b6b-107">Entity Framework Designer (EF デザイナー) は、デザイン時にストレージモデル情報を .edmx ファイルに保存します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-107">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="b5b6b-108">ビルド時に、Entity Designer は .edmx ファイルの情報を使用して、実行時に Entity Framework に必要な .ssdl ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-108">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="b5b6b-109">SSDL のバージョンは、XML 名前空間で区別されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-109">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="b5b6b-110">SSDL バージョン</span><span class="sxs-lookup"><span data-stu-id="b5b6b-110">SSDL Version</span></span> | <span data-ttu-id="b5b6b-111">XML 名前空間</span><span class="sxs-lookup"><span data-stu-id="b5b6b-111">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="b5b6b-112">SSDL v1</span><span class="sxs-lookup"><span data-stu-id="b5b6b-112">SSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="b5b6b-113">SSDL v2</span><span class="sxs-lookup"><span data-stu-id="b5b6b-113">SSDL v2</span></span>      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="b5b6b-114">SSDL v3</span><span class="sxs-lookup"><span data-stu-id="b5b6b-114">SSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="b5b6b-115">Association 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-115">Association Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-116">ストアスキーマ定義言語 (SSDL) の **Association** 要素は、基になるデータベースの foreign key 制約に参加するテーブル列を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-116">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="b5b6b-117">必要な 2 つの子 End 要素 は、アソシエーションの端のテーブルと両端の多重度を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-117">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="b5b6b-118">オプションの ReferentialConstraint 要素 は、アソシエーションのプリンシパル End と依存 End、および参加する列を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-118">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="b5b6b-119">AssociationSetMapping 要素を使用して、関連付けられている列マッピングを指定する必要 **があります** 。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-119">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="b5b6b-120">**Association**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-120">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-121">Documentation (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-121">Documentation (zero or one)</span></span>
-   <span data-ttu-id="b5b6b-122">End (2 個だけ)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-122">End (exactly two)</span></span>
-   <span data-ttu-id="b5b6b-123">ReferentialConstraint (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-123">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="b5b6b-124">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-124">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-125">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-125">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-126">次の表では、 **Association** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-126">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="b5b6b-127">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-127">Attribute Name</span></span> | <span data-ttu-id="b5b6b-128">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-128">Is Required</span></span> | <span data-ttu-id="b5b6b-129">値</span><span class="sxs-lookup"><span data-stu-id="b5b6b-129">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-130">**名前**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-130">**Name**</span></span>       | <span data-ttu-id="b5b6b-131">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-131">Yes</span></span>         | <span data-ttu-id="b5b6b-132">基盤となるデータベースの対応する外部キー制約の名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-132">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="b5b6b-133">任意の数の annotation 属性 (カスタム XML 属性) を **Association** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-133">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="b5b6b-134">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-134">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-135">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-135">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-136">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-136">Example</span></span>

<span data-ttu-id="b5b6b-137">次の例では、 **Association** **要素を使用** して、 **FK \_ CustomerOrders** foreign key 制約に参加する列を指定しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-137">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="associationset-element-ssdl"></a><span data-ttu-id="b5b6b-138">AssociationSet 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-138">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-139">ストアスキーマ定義言語 (SSDL) の **AssociationSet** 要素は、基になるデータベース内の2つのテーブル間の foreign key 制約を表します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-139">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="b5b6b-140">外部キー制約に参加するテーブル列は、Association 要素 で指定されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-140">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="b5b6b-141">指定された**associationset**要素に対応する**association**要素は、 **associationset**要素の**association**属性で指定されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-141">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="b5b6b-142">SSDL アソシエーション セットは AssociationSetMapping 要素 によって CSDL アソシエーション セット にマップされます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-142">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="b5b6b-143">ただし、指定された CSDL アソシエーションセットの CSDL アソシエーションが、AssociationSetMapping 要素を使用して定義されている場合は、対応する**AssociationSetMapping**要素は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-143">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="b5b6b-144">この場合、AssociationSetMapping 要素が存在する場合、それが定義するマッピングは、 **AssociationSetMapping** **要素に**よってオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-144">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="b5b6b-145">**AssociationSet**要素には、次の子要素を含めることができます (この順序で表示されます)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-145">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-146">Documentation (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-146">Documentation (zero or one)</span></span>
-   <span data-ttu-id="b5b6b-147">End (0 または 2 個)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-147">End (zero or two)</span></span>
-   <span data-ttu-id="b5b6b-148">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-148">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-149">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-149">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-150">次の表では、 **AssociationSet** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-150">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="b5b6b-151">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-151">Attribute Name</span></span>  | <span data-ttu-id="b5b6b-152">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-152">Is Required</span></span> | <span data-ttu-id="b5b6b-153">値</span><span class="sxs-lookup"><span data-stu-id="b5b6b-153">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-154">**名前**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-154">**Name**</span></span>        | <span data-ttu-id="b5b6b-155">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-155">Yes</span></span>         | <span data-ttu-id="b5b6b-156">アソシエーション セットが表す外部キー制約の名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-156">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="b5b6b-157">**関連付け**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-157">**Association**</span></span> | <span data-ttu-id="b5b6b-158">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-158">Yes</span></span>         | <span data-ttu-id="b5b6b-159">外部キー制約に参加する列を定義するアソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-159">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="b5b6b-160">任意の数の annotation 属性 (カスタム XML 属性) を **AssociationSet** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-160">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="b5b6b-161">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-161">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-162">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-162">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-163">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-163">Example</span></span>

<span data-ttu-id="b5b6b-164">次の例は、 **AssociationSet** `FK_CustomerOrders` 基になるデータベース内の foreign key 制約を表す AssociationSet 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-164">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="b5b6b-165">CollectionType 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-165">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-166">ストアスキーマ定義言語 (SSDL) の **CollectionType** 要素は、関数の戻り値の型がコレクションであることを指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-166">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="b5b6b-167">**CollectionType**要素は、ReturnType 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-167">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="b5b6b-168">コレクションの型は、RowType 子要素を使用して指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-168">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="b5b6b-169">任意の数の annotation 属性 (カスタム XML 属性) を **CollectionType** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-169">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="b5b6b-170">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-170">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-171">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-171">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-172">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-172">Example</span></span>

<span data-ttu-id="b5b6b-173">次の例は、 **CollectionType** 要素を使用して、関数が行のコレクションを返すことを指定する関数を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-173">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

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

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="b5b6b-174">CommandText 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-174">CommandText Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-175">ストアスキーマ定義言語 (SSDL) の **CommandText** 要素は、データベースで実行される SQL ステートメントを定義できる Function 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-175">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="b5b6b-176">**Commandtext**要素を使用すると、データベース内のストアドプロシージャに似た機能を追加できますが、ストレージモデルでは**commandtext**要素を定義します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-176">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="b5b6b-177">**CommandText**要素に子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-177">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="b5b6b-178">**CommandText**要素の本文は、基になるデータベースの有効な SQL ステートメントである必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-178">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="b5b6b-179">**CommandText**要素に適用できる属性はありません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-179">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-180">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-180">Example</span></span>

<span data-ttu-id="b5b6b-181">次の例は、子の**CommandText**要素を持つ**関数**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-181">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="b5b6b-182">概念モデルにインポートすることで、 **この関数を** ObjectContext のメソッドとして公開します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-182">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

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

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="b5b6b-183">DefiningQuery 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-183">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-184">ストアスキーマ定義言語 (SSDL) の **DefiningQuery** 要素を使用すると、基になるデータベースで SQL ステートメントを直接実行できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-184">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="b5b6b-185">**DefiningQuery**要素はデータベースビューのように一般的に使用されますが、ビューはデータベースではなくストレージモデルで定義されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-185">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="b5b6b-186">**DefiningQuery**要素で定義されたビューは、EntitySetMapping 要素を通じて概念モデルのエンティティ型にマップできます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-186">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="b5b6b-187">このようなマッピングは、読み取り専用です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-187">These mappings are read-only.</span></span>  

<span data-ttu-id="b5b6b-188">次の SSDL 構文は、 **EntitySet** の宣言と、ビューを取得するために使用されるクエリを含む **DefiningQuery** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-188">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

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

<span data-ttu-id="b5b6b-189">Entity Framework のストアドプロシージャを使用して、ビューに対する読み取り/書き込みシナリオを有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-189">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span><span data-ttu-id="b5b6b-190">データソースビューまたは Entity SQL ビューをベーステーブルとして使用して、データを取得したり、ストアドプロシージャによる変更処理を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-190"> You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="b5b6b-191">**DefiningQuery**要素を使用して Microsoft SQL Server Compact 3.5 を対象にすることができます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-191">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="b5b6b-192">SQL Server Compact 3.5 はストアドプロシージャをサポートしていませんが、 **DefiningQuery** 要素と同様の機能を実装できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-192">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="b5b6b-193">他にこの要素が役立つ状況として、ストアド プロシージャを作成してプログラミング言語で使用されているデータ型とデータ ソースで使用されているデータ型の不一致を克服することができます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-193">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="b5b6b-194">特定のパラメーターセットを受け取り、データを削除するストアドプロシージャなど、異なるパラメーターのセットを使用してストアドプロシージャを呼び出す **DefiningQuery** を作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-194">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="b5b6b-195">Dependent 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-195">Dependent Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-196">ストアスキーマ定義言語 (SSDL) の **dependent** 要素は、外部キー制約の依存 end (参照制約とも呼ばれます) を定義する、参照要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-196">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="b5b6b-197">**Dependent**要素は、主キー列 (または列) を参照するテーブルの列 (または列) を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-197">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="b5b6b-198">**Propertyref** 要素は、参照される列を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-198">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="b5b6b-199">Principal 要素は、 **Dependent** 要素で指定された列によって参照される主キー列を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-199">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="b5b6b-200">**Dependent**要素には、次の子要素を含めることができます (順に記載されています)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-200">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-201">PropertyRef (1 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-201">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="b5b6b-202">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-202">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-203">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-203">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-204">次の表では、 **依存** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-204">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="b5b6b-205">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-205">Attribute Name</span></span> | <span data-ttu-id="b5b6b-206">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-206">Is Required</span></span> | <span data-ttu-id="b5b6b-207">[値]</span><span class="sxs-lookup"><span data-stu-id="b5b6b-207">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-208">**ロール**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-208">**Role**</span></span>       | <span data-ttu-id="b5b6b-209">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-209">Yes</span></span>         | <span data-ttu-id="b5b6b-210">対応する End 要素の **Role** 属性と同じ値 (使用されている場合)。それ以外の場合は、参照している列を含むテーブルの名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-210">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="b5b6b-211">任意の数の annotation 属性 (カスタム XML 属性) を **依存** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-211">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="b5b6b-212">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-212">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b5b6b-213">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-213">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-214">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-214">Example</span></span>

<span data-ttu-id="b5b6b-215">次の例では、Association **要素を使用** して、 **FK \_ CustomerOrders** foreign key 制約に参加する列を指定しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-215">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="b5b6b-216">**Dependent**要素は、 **Order**テーブルの**CustomerId**列を制約の依存 end として指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-216">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

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

## <a name="documentation-element-ssdl"></a><span data-ttu-id="b5b6b-217">Documentation 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-217">Documentation Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-218">ストアスキーマ定義言語 (SSDL) の **Documentation** 要素は、親要素で定義されているオブジェクトに関する情報を提供するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-218">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="b5b6b-219">**Documentation**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-219">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-220">**Summary**: 親要素の簡単な説明。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-220">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="b5b6b-221">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-221">(zero or one element)</span></span>
-   <span data-ttu-id="b5b6b-222">**Longdescription**: 親要素の詳細な説明。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-222">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="b5b6b-223">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-223">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-224">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-224">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-225">**Documentation**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-225">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="b5b6b-226">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b5b6b-227">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-228">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-228">Example</span></span>

<span data-ttu-id="b5b6b-229">次の例は、EntityType 要素の子要素としての **Documentation** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-229">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

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

## <a name="end-element-ssdl"></a><span data-ttu-id="b5b6b-230">End 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-230">End Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-231">ストアスキーマ定義言語 (SSDL) の **end** 要素は、基になるデータベースの外部キー制約の一方の端にあるテーブルと行数を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-231">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="b5b6b-232">**End**要素は、Association 要素または AssociationSet 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-232">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="b5b6b-233">それぞれの場合において、使用可能な子要素と該当する属性は異なります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-233">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="b5b6b-234">Association 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="b5b6b-234">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="b5b6b-235">**End**要素 ( **Association**要素の子として) では、外部キー制約の最後に**型**と**多重度**の属性を使用して、テーブルと行の数を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-235">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="b5b6b-236">外部キー制約の End は、SSDL アソシエーションの一部として定義されます。SSDL アソシエーションには End が 2 つ必要です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-236">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="b5b6b-237">**終了**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-237">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-238">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-238">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b5b6b-239">OnDelete (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-239">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="b5b6b-240">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-240">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-241">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-241">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-242">次の表では、 **End** 要素が **Association** 要素の子である場合に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-242">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="b5b6b-243">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-243">Attribute Name</span></span>   | <span data-ttu-id="b5b6b-244">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-244">Is Required</span></span> | <span data-ttu-id="b5b6b-245">[値]</span><span class="sxs-lookup"><span data-stu-id="b5b6b-245">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-246">**Type**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-246">**Type**</span></span>         | <span data-ttu-id="b5b6b-247">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-247">Yes</span></span>         | <span data-ttu-id="b5b6b-248">外部キー制約の End にある SSDL エンティティ セットの完全修飾名。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-248">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="b5b6b-249">**Role**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-249">**Role**</span></span>         | <span data-ttu-id="b5b6b-250">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-250">No</span></span>          | <span data-ttu-id="b5b6b-251">対応する参照エンティティ Alconstraint 要素の Principal または Dependent 要素の **Role** 属性の値 (使用されている場合)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-251">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="b5b6b-252">**カーディナリティ**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-252">**Multiplicity**</span></span> | <span data-ttu-id="b5b6b-253">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-253">Yes</span></span>         | <span data-ttu-id="b5b6b-254">**1**、 **0 ..1**、または **\*** foreign key 制約の最後にある行の数によって異なります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-254">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="b5b6b-255">**1** は、foreign key 制約 end に1行だけ存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-255">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="b5b6b-256">**0 ..1** は、foreign key 制約 end に0行または1行が存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-256">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="b5b6b-257">**\*** 外部キー制約の end に0行、1行、または複数の行が存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-257">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="b5b6b-258">任意の数の annotation 属性 (カスタム XML 属性) を **終了** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-258">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="b5b6b-259">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-259">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b5b6b-260">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-260">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="b5b6b-261">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-261">Example</span></span>

<span data-ttu-id="b5b6b-262">次の例は、 **FK \_ CustomerOrders** foreign key 制約を定義する**Association**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-262">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="b5b6b-263">各**End**要素に指定された複数要素の接続**性**の値は、 **Orders**テーブルの行を**customers**テーブルの行に関連付けることができることを示します。ただし、 **orders**テーブルの行に関連付けることができるのは、 **customers**テーブル内の1つの行だけです。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-263">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="b5b6b-264">また、 **OnDelete**要素は **、customers テーブル**内の行が削除された場合に、 **customers**テーブル内の特定の行を参照する**Orders**テーブル内のすべての行が削除されることを示します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-264">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="b5b6b-265">AssociationSet 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="b5b6b-265">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="b5b6b-266">**終了**要素 ( **AssociationSet**要素の子として) は、基になるデータベース内の foreign key 制約の一方の端にあるテーブルを指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-266">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="b5b6b-267">**終了**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-267">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-268">Documentation (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-268">Documentation (zero or one)</span></span>
-   <span data-ttu-id="b5b6b-269">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-269">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-270">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-270">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-271">次の表では、 **AssociationSet**要素の子である場合に**End**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-271">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="b5b6b-272">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-272">Attribute Name</span></span> | <span data-ttu-id="b5b6b-273">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-273">Is Required</span></span> | <span data-ttu-id="b5b6b-274">[値]</span><span class="sxs-lookup"><span data-stu-id="b5b6b-274">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-275">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-275">**EntitySet**</span></span>  | <span data-ttu-id="b5b6b-276">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-276">Yes</span></span>         | <span data-ttu-id="b5b6b-277">外部キー制約の End にある SSDL エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-277">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="b5b6b-278">**Role**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-278">**Role**</span></span>       | <span data-ttu-id="b5b6b-279">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-279">No</span></span>          | <span data-ttu-id="b5b6b-280">対応する Association 要素の1つの**End**要素に指定された**ロール**属性の1つの値。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-280">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="b5b6b-281">任意の数の annotation 属性 (カスタム XML 属性) を **終了** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-281">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="b5b6b-282">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-282">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b5b6b-283">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-283">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="b5b6b-284">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-284">Example</span></span>

<span data-ttu-id="b5b6b-285">次の例は、2つの**End**要素を持つ**AssociationSet**要素を持つ**EntityContainer**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-285">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

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

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="b5b6b-286">EntityContainer 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-286">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-287">ストアスキーマ定義言語 (SSDL) の **EntityContainer** 要素は、Entity Framework アプリケーション内の基になるデータソースの構造を記述します。 ssdl エンティティセット (EntitySet 要素で定義) は、データベース内のテーブルを表し、ssdl エンティティ型 (EntityType 要素で定義) はテーブル内の行を表し、(AssociationSet 要素で定義された) アソシエーションセットはデータベース内の外部キー</span><span class="sxs-lookup"><span data-stu-id="b5b6b-287">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="b5b6b-288">ストレージ モデルのエンティティ コンテナーは、EntityContainerMapping 要素を通じて概念モデルのエンティティ コンテナーにマップされます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-288">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="b5b6b-289">**EntityContainer**要素には、0個または1個の Documentation 要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-289">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="b5b6b-290">**Documentation**要素が存在する場合は、他のすべての子要素の前に置く必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-290">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="b5b6b-291">**EntityContainer**要素には、次の子要素を0個以上含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-291">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-292">EntitySet</span><span class="sxs-lookup"><span data-stu-id="b5b6b-292">EntitySet</span></span>
-   <span data-ttu-id="b5b6b-293">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="b5b6b-293">AssociationSet</span></span>
-   <span data-ttu-id="b5b6b-294">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="b5b6b-294">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-295">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-295">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-296">次の表は、 **EntityContainer** 要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-296">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="b5b6b-297">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-297">Attribute Name</span></span> | <span data-ttu-id="b5b6b-298">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-298">Is Required</span></span> | <span data-ttu-id="b5b6b-299">値</span><span class="sxs-lookup"><span data-stu-id="b5b6b-299">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-300">**名前**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-300">**Name**</span></span>       | <span data-ttu-id="b5b6b-301">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-301">Yes</span></span>         | <span data-ttu-id="b5b6b-302">エンティティ コンテナー名。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-302">The name of the entity container.</span></span> <span data-ttu-id="b5b6b-303">この名前にピリオド (.) を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-303">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="b5b6b-304">任意の数の annotation 属性 (カスタム XML 属性) を **EntityContainer** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-304">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="b5b6b-305">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-305">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-306">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-306">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-307">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-307">Example</span></span>

<span data-ttu-id="b5b6b-308">次の例は、2つのエンティティセットと1つのアソシエーションセットを定義する **EntityContainer** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-308">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="b5b6b-309">エンティティ型およびアソシエーション型の名前は、概念モデルの名前空間名によって修飾されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-309">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

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

## <a name="entityset-element-ssdl"></a><span data-ttu-id="b5b6b-310">EntitySet 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-310">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-311">ストアスキーマ定義言語 (SSDL) の **EntitySet** 要素は、基になるデータベースのテーブルまたはビューを表します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-311">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="b5b6b-312">SSDL の EntityType 要素は、テーブルまたはビュー内の行を表します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-312">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="b5b6b-313">**EntitySet**要素の**EntityType**属性は、ssdl エンティティセット内の行を表す特定の ssdl エンティティ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-313">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="b5b6b-314">CSDL エンティティ セットと SSDL エンティティ セットの間のマッピングは、EntitySetMapping 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-314">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="b5b6b-315">**EntitySet**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-315">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-316">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-316">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b5b6b-317">DefiningQuery (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-317">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="b5b6b-318">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="b5b6b-318">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-319">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-319">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-320">次の表では、 **EntitySet** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-320">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="b5b6b-321">一部の属性 (ここには記載されていません) は、 **ストア** エイリアスで修飾できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-321">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="b5b6b-322">このような属性は、モデルの更新時にモデルの更新ウィザードで使用されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-322">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="b5b6b-323">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-323">Attribute Name</span></span> | <span data-ttu-id="b5b6b-324">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-324">Is Required</span></span> | <span data-ttu-id="b5b6b-325">値</span><span class="sxs-lookup"><span data-stu-id="b5b6b-325">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-326">**名前**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-326">**Name**</span></span>       | <span data-ttu-id="b5b6b-327">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-327">Yes</span></span>         | <span data-ttu-id="b5b6b-328">エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-328">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="b5b6b-329">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-329">**EntityType**</span></span> | <span data-ttu-id="b5b6b-330">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-330">Yes</span></span>         | <span data-ttu-id="b5b6b-331">エンティティ型の完全修飾名。そのインスタンスは、エンティティ セットに格納されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-331">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="b5b6b-332">**[スキーマ]**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-332">**Schema**</span></span>     | <span data-ttu-id="b5b6b-333">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-333">No</span></span>          | <span data-ttu-id="b5b6b-334">データベース スキーマ。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-334">The database schema.</span></span>                                                                     |
| <span data-ttu-id="b5b6b-335">**Table**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-335">**Table**</span></span>      | <span data-ttu-id="b5b6b-336">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-336">No</span></span>          | <span data-ttu-id="b5b6b-337">データベース テーブル。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-337">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="b5b6b-338">**EntitySet**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-338">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="b5b6b-339">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-339">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-340">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-340">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-341">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-341">Example</span></span>

<span data-ttu-id="b5b6b-342">次の例は、2つの**EntitySet**要素と1つの**AssociationSet**要素を持つ**EntityContainer**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-342">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

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

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="b5b6b-343">EntityType 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-343">EntityType Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-344">ストアスキーマ定義言語 (SSDL) の **EntityType** 要素は、基になるデータベースのテーブルまたはビューの行を表します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-344">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="b5b6b-345">SSDL の EntitySet 要素は、行が発生するテーブルまたはビューを表します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-345">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="b5b6b-346">**EntitySet**要素の**EntityType**属性は、ssdl エンティティセット内の行を表す特定の ssdl エンティティ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-346">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="b5b6b-347">SSDL のエンティティ型と CSDL のエンティティ型 間のマッピングは、EntityTypeMapping 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-347">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="b5b6b-348">**EntityType**要素には、次の子要素を含めることができます (この順序で表示されます)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-348">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-349">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-349">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b5b6b-350">Key (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-350">Key (zero or one element)</span></span>
-   <span data-ttu-id="b5b6b-351">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="b5b6b-351">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-352">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-352">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-353">次の表では、 **EntityType** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-353">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="b5b6b-354">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-354">Attribute Name</span></span> | <span data-ttu-id="b5b6b-355">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-355">Is Required</span></span> | <span data-ttu-id="b5b6b-356">値</span><span class="sxs-lookup"><span data-stu-id="b5b6b-356">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-357">**名前**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-357">**Name**</span></span>       | <span data-ttu-id="b5b6b-358">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-358">Yes</span></span>         | <span data-ttu-id="b5b6b-359">エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-359">The name of the entity type.</span></span> <span data-ttu-id="b5b6b-360">通常、この値は、エンティティ型が行を表すテーブルの名前と同じです。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-360">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="b5b6b-361">この値にピリオド (.) を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-361">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="b5b6b-362">任意の数の annotation 属性 (カスタム XML 属性) を **EntityType** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-362">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="b5b6b-363">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-363">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-364">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-364">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-365">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-365">Example</span></span>

<span data-ttu-id="b5b6b-366">次の例は、2つのプロパティを持つ **EntityType** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-366">The following example shows an **EntityType** element with two properties:</span></span>

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

## <a name="function-element-ssdl"></a><span data-ttu-id="b5b6b-367">Function 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-367">Function Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-368">ストアスキーマ定義言語 (SSDL) の **Function** 要素は、基になるデータベースに存在するストアドプロシージャを指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-368">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="b5b6b-369">**Function**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-369">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-370">Documentation (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-370">Documentation (zero or one)</span></span>
-   <span data-ttu-id="b5b6b-371">Parameter (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-371">Parameter (zero or more)</span></span>
-   <span data-ttu-id="b5b6b-372">CommandText (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-372">CommandText (zero or one)</span></span>
-   <span data-ttu-id="b5b6b-373">ReturnType (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-373">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="b5b6b-374">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-374">Annotation elements (zero or more)</span></span>

<span data-ttu-id="b5b6b-375">関数の戻り値の型は、 **returntype** 要素または **returntype** 属性 (下記参照) のいずれかで指定する必要がありますが、両方を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-375">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="b5b6b-376">ストレージ モデル内で指定されるストアド プロシージャは、アプリケーションの概念モデルにインポートできます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-376">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="b5b6b-377">詳細については、「 [ストアドプロシージャを使用したクエリ](xref:ef6/modeling/designer/stored-procedures/query)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-377">For more information, see [Querying with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/query).</span></span> <span data-ttu-id="b5b6b-378">**Function**要素を使用して、ストレージモデルでカスタム関数を定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-378">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-379">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-379">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-380">次の表では、 **Function** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-380">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="b5b6b-381">一部の属性 (ここには記載されていません) は、 **ストア** エイリアスで修飾できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-381">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="b5b6b-382">このような属性は、モデルの更新時にモデルの更新ウィザードで使用されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-382">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="b5b6b-383">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-383">Attribute Name</span></span>             | <span data-ttu-id="b5b6b-384">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-384">Is Required</span></span> | <span data-ttu-id="b5b6b-385">値</span><span class="sxs-lookup"><span data-stu-id="b5b6b-385">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-386">**名前**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-386">**Name**</span></span>                   | <span data-ttu-id="b5b6b-387">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-387">Yes</span></span>         | <span data-ttu-id="b5b6b-388">ストアド プロシージャの名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-388">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="b5b6b-389">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-389">**ReturnType**</span></span>             | <span data-ttu-id="b5b6b-390">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-390">No</span></span>          | <span data-ttu-id="b5b6b-391">ストアド プロシージャの戻り値の型。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-391">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="b5b6b-392">**集計**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-392">**Aggregate**</span></span>              | <span data-ttu-id="b5b6b-393">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-393">No</span></span>          | <span data-ttu-id="b5b6b-394">ストアドプロシージャから集計値が返される場合は**True**を指定します。それ以外の場合は**False**。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-394">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="b5b6b-395">**BuiltIn**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-395">**BuiltIn**</span></span>                | <span data-ttu-id="b5b6b-396">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-396">No</span></span>          | <span data-ttu-id="b5b6b-397">関数が組み込みの<sup>1</sup>関数の場合は**True** 。それ以外の場合は**False**。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-397">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="b5b6b-398">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-398">**StoreFunctionName**</span></span>      | <span data-ttu-id="b5b6b-399">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-399">No</span></span>          | <span data-ttu-id="b5b6b-400">ストアド プロシージャの名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-400">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="b5b6b-401">**NiladicFunction**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-401">**NiladicFunction**</span></span>        | <span data-ttu-id="b5b6b-402">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-402">No</span></span>          | <span data-ttu-id="b5b6b-403">関数がニラディック<sup>2</sup>関数の場合は**True**を返します。それ以外の場合は**False** 。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-403">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="b5b6b-404">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-404">**IsComposable**</span></span>           | <span data-ttu-id="b5b6b-405">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-405">No</span></span>          | <span data-ttu-id="b5b6b-406">関数がコンポーザブルな<sup>3</sup>関数の場合は**True** 。それ以外の場合は**False** 。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-406">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="b5b6b-407">**ParameterTypeSemantics**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-407">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="b5b6b-408">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-408">No</span></span>          | <span data-ttu-id="b5b6b-409">関数オーバーロードの解決に使用される型のセマンティクスを定義する列挙。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-409">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="b5b6b-410">列挙はプロバイダー マニフェストで関数定義によって定義されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-410">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="b5b6b-411">既定値は **AllowImplicitConversion**です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-411">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="b5b6b-412">**[スキーマ]**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-412">**Schema**</span></span>                 | <span data-ttu-id="b5b6b-413">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-413">No</span></span>          | <span data-ttu-id="b5b6b-414">ストアド プロシージャが定義されているスキーマの名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-414">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="b5b6b-415"><sup>1</sup> 組み込み関数は、データベースで定義されている関数です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-415"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="b5b6b-416">ストレージモデルで定義されている関数の詳細については、「CommandText 要素 (SSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-416">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="b5b6b-417"><sup>2</sup> ニラディック関数は、パラメーターを受け入れず、呼び出されたときにかっこを必要としない関数です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-417"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="b5b6b-418"><sup>3</sup> 2 つの関数の出力を他の関数の入力として使用できる場合、2つの関数はコンポーザブルです。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-418"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="b5b6b-419">**関数**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-419">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="b5b6b-420">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-420">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-421">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-421">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-422">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-422">Example</span></span>

<span data-ttu-id="b5b6b-423">次の例は、 **Updateorderquantity**ストアドプロシージャに対応する**関数**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-423">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="b5b6b-424">このストアド プロシージャは 2 個のパラメーターを受け入れ、値を返しません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-424">The stored procedure accepts two parameters and does not return a value.</span></span>

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

## <a name="key-element-ssdl"></a><span data-ttu-id="b5b6b-425">key 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-425">Key Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-426">ストアスキーマ定義言語 (SSDL) の **key** 要素は、基になるデータベース内のテーブルの主キーを表します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-426">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="b5b6b-427">**Key** は EntityType 要素の子要素であり、テーブル内の行を表します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-427">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="b5b6b-428">主キーは、 **EntityType**要素で定義されている1つ以上の Property 要素を参照することによって、 **key**要素で定義されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-428">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="b5b6b-429">**Key**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-429">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-430">PropertyRef (1 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-430">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="b5b6b-431">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="b5b6b-431">Annotation elements</span></span>

<span data-ttu-id="b5b6b-432">**キー**要素に適用できる属性はありません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-432">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-433">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-433">Example</span></span>

<span data-ttu-id="b5b6b-434">次の例は、1つのプロパティを参照するキーを持つ **EntityType** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-434">The following example shows an **EntityType** element with a key that references one property:</span></span>

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

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="b5b6b-435">OnDelete 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-435">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-436">ストアスキーマ定義言語 (SSDL) の **OnDelete** 要素は、foreign key 制約に参加している行が削除された場合にデータベースの動作を反映します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-436">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="b5b6b-437">アクションが **Cascade**に設定されている場合、削除される行を参照する行も削除されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-437">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="b5b6b-438">アクションが **None**に設定されている場合、削除される行を参照する行も削除されません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-438">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="b5b6b-439">**OnDelete**要素は、End 要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-439">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="b5b6b-440">**OnDelete**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-440">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-441">Documentation (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-441">Documentation (zero or one)</span></span>
-   <span data-ttu-id="b5b6b-442">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-442">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-443">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-443">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-444">次の表では、 **OnDelete** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-444">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="b5b6b-445">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-445">Attribute Name</span></span> | <span data-ttu-id="b5b6b-446">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-446">Is Required</span></span> | <span data-ttu-id="b5b6b-447">[値]</span><span class="sxs-lookup"><span data-stu-id="b5b6b-447">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-448">**操作**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-448">**Action**</span></span>     | <span data-ttu-id="b5b6b-449">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-449">Yes</span></span>         | <span data-ttu-id="b5b6b-450">**Cascade** または **None**。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-450">**Cascade** or **None**.</span></span> <span data-ttu-id="b5b6b-451">( **制限** 値は有効ですが、動作は **None**と同じです)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-451">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="b5b6b-452">**OnDelete**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-452">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="b5b6b-453">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-453">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-454">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-454">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-455">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-455">Example</span></span>

<span data-ttu-id="b5b6b-456">次の例は、 **FK \_ CustomerOrders** foreign key 制約を定義する**Association**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-456">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="b5b6b-457">**OnDelete**要素は **、customers テーブル**内の行が削除された場合に、 **customers**テーブル内の特定の行を参照する**Orders**テーブル内のすべての行が削除されることを示します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-457">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

## <a name="parameter-element-ssdl"></a><span data-ttu-id="b5b6b-458">Parameter 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-458">Parameter Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-459">ストアスキーマ定義言語 (SSDL) の **Parameter** 要素は、データベース内のストアドプロシージャのパラメーターを指定する Function 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-459">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="b5b6b-460">**Parameter**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-460">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-461">Documentation (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-461">Documentation (zero or one)</span></span>
-   <span data-ttu-id="b5b6b-462">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-462">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-463">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-463">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-464">次の表では、 **Parameter** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-464">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="b5b6b-465">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-465">Attribute Name</span></span> | <span data-ttu-id="b5b6b-466">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-466">Is Required</span></span> | <span data-ttu-id="b5b6b-467">値</span><span class="sxs-lookup"><span data-stu-id="b5b6b-467">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-468">**名前**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-468">**Name**</span></span>       | <span data-ttu-id="b5b6b-469">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-469">Yes</span></span>         | <span data-ttu-id="b5b6b-470">パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-470">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="b5b6b-471">**Type**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-471">**Type**</span></span>       | <span data-ttu-id="b5b6b-472">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-472">Yes</span></span>         | <span data-ttu-id="b5b6b-473">パラメーターの型。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-473">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="b5b6b-474">**モード**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-474">**Mode**</span></span>       | <span data-ttu-id="b5b6b-475">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-475">No</span></span>          | <span data-ttu-id="b5b6b-476">**In**、 **Out**、または **InOut** は、パラメーターが入力パラメーター、出力パラメーター、または入力/出力パラメーターのどちらであるかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-476">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="b5b6b-477">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-477">**MaxLength**</span></span>  | <span data-ttu-id="b5b6b-478">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-478">No</span></span>          | <span data-ttu-id="b5b6b-479">パラメーターの最大長。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-479">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="b5b6b-480">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-480">**Precision**</span></span>  | <span data-ttu-id="b5b6b-481">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-481">No</span></span>          | <span data-ttu-id="b5b6b-482">パラメーターの説明</span><span class="sxs-lookup"><span data-stu-id="b5b6b-482">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="b5b6b-483">**スケール**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-483">**Scale**</span></span>      | <span data-ttu-id="b5b6b-484">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-484">No</span></span>          | <span data-ttu-id="b5b6b-485">パラメーターの小数点以下桁数</span><span class="sxs-lookup"><span data-stu-id="b5b6b-485">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="b5b6b-486">**SRID**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-486">**SRID**</span></span>       | <span data-ttu-id="b5b6b-487">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-487">No</span></span>          | <span data-ttu-id="b5b6b-488">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-488">Spatial System Reference Identifier.</span></span> <span data-ttu-id="b5b6b-489">空間型のパラメーターに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-489">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="b5b6b-490">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-490">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="b5b6b-491">**パラメーター**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-491">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="b5b6b-492">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-492">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-493">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-493">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-494">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-494">Example</span></span>

<span data-ttu-id="b5b6b-495">次の例は、入力パラメーターを指定する2つの**パラメーター**要素を持つ**関数**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-495">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

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

## <a name="principal-element-ssdl"></a><span data-ttu-id="b5b6b-496">Principal 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-496">Principal Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-497">ストアスキーマ定義言語 (SSDL) の **principal** 要素は、外部キー制約 (参照制約とも呼ばれます) のプリンシパル end を定義する、参照要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-497">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="b5b6b-498">**Principal**要素は、別の列 (または列) によって参照されるテーブル内の主キー列 (または列) を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-498">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="b5b6b-499">**Propertyref** 要素は、参照される列を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-499">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="b5b6b-500">Dependent 要素は、 **Principal** 要素で指定されている主キー列を参照する列を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-500">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="b5b6b-501">**Principal**要素には、次の子要素を含めることができます (順に記載されています)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-501">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b5b6b-502">PropertyRef (1 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-502">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="b5b6b-503">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-503">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-504">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-504">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-505">次の表では、 **Principal** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-505">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="b5b6b-506">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-506">Attribute Name</span></span> | <span data-ttu-id="b5b6b-507">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-507">Is Required</span></span> | <span data-ttu-id="b5b6b-508">[値]</span><span class="sxs-lookup"><span data-stu-id="b5b6b-508">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-509">**ロール**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-509">**Role**</span></span>       | <span data-ttu-id="b5b6b-510">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-510">Yes</span></span>         | <span data-ttu-id="b5b6b-511">対応する End 要素の **Role** 属性と同じ値 (使用されている場合)。それ以外の場合は、参照先の列を含むテーブルの名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-511">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="b5b6b-512">**Principal**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-512">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="b5b6b-513">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-513">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b5b6b-514">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-514">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-515">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-515">Example</span></span>

<span data-ttu-id="b5b6b-516">次の例では、Association **要素を使用** して、 **FK \_ CustomerOrders** foreign key 制約に参加する列を指定しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-516">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="b5b6b-517">**Principal**要素は、制約のプリンシパル end として**Customer**テーブルの**CustomerId**列を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-517">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

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

## <a name="property-element-ssdl"></a><span data-ttu-id="b5b6b-518">Property 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-518">Property Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-519">ストアスキーマ定義言語 (SSDL) の **Property** 要素は、基になるデータベース内のテーブルの列を表します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-519">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="b5b6b-520">**プロパティ** 要素は、テーブル内の行を表す EntityType 要素の子です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-520">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="b5b6b-521">**EntityType**要素で定義されている各**Property**要素は、列を表します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-521">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="b5b6b-522">**Property**要素に子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-522">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-523">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-523">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-524">次の表では、 **Property** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-524">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="b5b6b-525">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-525">Attribute Name</span></span>            | <span data-ttu-id="b5b6b-526">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-526">Is Required</span></span> | <span data-ttu-id="b5b6b-527">値</span><span class="sxs-lookup"><span data-stu-id="b5b6b-527">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-528">**名前**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-528">**Name**</span></span>                  | <span data-ttu-id="b5b6b-529">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-529">Yes</span></span>         | <span data-ttu-id="b5b6b-530">対応する列の名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-530">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="b5b6b-531">**Type**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-531">**Type**</span></span>                  | <span data-ttu-id="b5b6b-532">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-532">Yes</span></span>         | <span data-ttu-id="b5b6b-533">対応する列の種類。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-533">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="b5b6b-534">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-534">**Nullable**</span></span>              | <span data-ttu-id="b5b6b-535">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-535">No</span></span>          | <span data-ttu-id="b5b6b-536">対応する列が null 値を持つことができるかどうかによって、 **True** (既定値) または**False** 。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-536">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="b5b6b-537">**既定**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-537">**DefaultValue**</span></span>          | <span data-ttu-id="b5b6b-538">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-538">No</span></span>          | <span data-ttu-id="b5b6b-539">対応する列の既定値。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-539">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="b5b6b-540">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-540">**MaxLength**</span></span>             | <span data-ttu-id="b5b6b-541">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-541">No</span></span>          | <span data-ttu-id="b5b6b-542">対応する列の最大長。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-542">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="b5b6b-543">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-543">**FixedLength**</span></span>           | <span data-ttu-id="b5b6b-544">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-544">No</span></span>          | <span data-ttu-id="b5b6b-545">対応する列の値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-545">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="b5b6b-546">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-546">**Precision**</span></span>             | <span data-ttu-id="b5b6b-547">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-547">No</span></span>          | <span data-ttu-id="b5b6b-548">対応する列の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-548">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="b5b6b-549">**スケール**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-549">**Scale**</span></span>                 | <span data-ttu-id="b5b6b-550">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-550">No</span></span>          | <span data-ttu-id="b5b6b-551">対応する列の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-551">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="b5b6b-552">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-552">**Unicode**</span></span>               | <span data-ttu-id="b5b6b-553">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-553">No</span></span>          | <span data-ttu-id="b5b6b-554">対応する列の値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-554">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="b5b6b-555">**Collation**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-555">**Collation**</span></span>             | <span data-ttu-id="b5b6b-556">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-556">No</span></span>          | <span data-ttu-id="b5b6b-557">データ ソースで使用される照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-557">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="b5b6b-558">**SRID**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-558">**SRID**</span></span>                  | <span data-ttu-id="b5b6b-559">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-559">No</span></span>          | <span data-ttu-id="b5b6b-560">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-560">Spatial System Reference Identifier.</span></span> <span data-ttu-id="b5b6b-561">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-561">Valid only for properties of spatial types.</span></span> <span data-ttu-id="b5b6b-562">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-562">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="b5b6b-563">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-563">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="b5b6b-564">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-564">No</span></span>          | <span data-ttu-id="b5b6b-565">**None**、 **identity** (対応する列の値がデータベースで生成された id である場合)、または **計算** された (対応する列の値がデータベースで計算されている場合)。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-565">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="b5b6b-566">RowType プロパティには無効です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-566">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="b5b6b-567">**プロパティ**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-567">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="b5b6b-568">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-568">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-569">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-569">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-570">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-570">Example</span></span>

<span data-ttu-id="b5b6b-571">次の例は、2つの子**プロパティ**要素を持つ**EntityType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-571">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

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

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="b5b6b-572">PropertyRef 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-572">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-573">ストアスキーマ定義言語 (SSDL) の **Propertyref** 要素は、プロパティが次のいずれかのロールを実行することを示すために、EntityType 要素で定義されているプロパティを参照します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-573">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="b5b6b-574">**EntityType**が表すテーブルの主キーの一部です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-574">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="b5b6b-575">1つ以上の **Propertyref** 要素を使用して主キーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-575">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="b5b6b-576">詳細については、「Key 要素」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-576">For more information, see Key element.</span></span>
-   <span data-ttu-id="b5b6b-577">参照に関する制約の依存 End またはプリンシパル End になる。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-577">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="b5b6b-578">詳細については、「ReferentialConstraint 要素」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-578">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="b5b6b-579">**Propertyref**要素には、次の子要素のみを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-579">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="b5b6b-580">Documentation (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-580">Documentation (zero or one)</span></span>
-   <span data-ttu-id="b5b6b-581">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="b5b6b-581">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-582">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-582">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-583">次の表では、 **Propertyref** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-583">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="b5b6b-584">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-584">Attribute Name</span></span> | <span data-ttu-id="b5b6b-585">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-585">Is Required</span></span> | <span data-ttu-id="b5b6b-586">値</span><span class="sxs-lookup"><span data-stu-id="b5b6b-586">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="b5b6b-587">**名前**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-587">**Name**</span></span>       | <span data-ttu-id="b5b6b-588">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-588">Yes</span></span>         | <span data-ttu-id="b5b6b-589">参照されているプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-589">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="b5b6b-590">**Propertyref**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-590">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="b5b6b-591">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b5b6b-592">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-593">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-593">Example</span></span>

<span data-ttu-id="b5b6b-594">次の例は、 **EntityType**要素で定義されているプロパティを参照することによって主キーを定義するために使用される**propertyref**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-594">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

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

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="b5b6b-595">ReferentialConstraint 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-595">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-596">ストアスキーマ定義言語 (SSDL) の参照型の **制約** 要素は、基になるデータベースの foreign key 制約 (参照整合性制約とも呼ばれます) を表します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-596">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="b5b6b-597">制約のプリンシパル End と依存 End は、それぞれ Principal 子要素と Dependent 子要素によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-597">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="b5b6b-598">プリンシパル End と依存 End に参加する列は、PropertyRef 要素で参照します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-598">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="b5b6b-599">この **要素は** 、Association 要素の省略可能な子要素です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-599">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="b5b6b-600">**Association**要素で指定されている外部キー制約をマップするために、AssociationSetMapping 要素が使用されていない場合は、これを行うために、**この要素を**使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-600">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="b5b6b-601">この **要素には、次** の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-601">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="b5b6b-602">Documentation (0 個または 1 個)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-602">Documentation (zero or one)</span></span>
-   <span data-ttu-id="b5b6b-603">Principal (1 個)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-603">Principal (exactly one)</span></span>
-   <span data-ttu-id="b5b6b-604">Dependent (1 個)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-604">Dependent (exactly one)</span></span>
-   <span data-ttu-id="b5b6b-605">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-605">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-606">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-606">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-607">任意の数の annotation 属性 (カスタム XML 属性 **) を、この要素に** 適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-607">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="b5b6b-608">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-608">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-609">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-609">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-610">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-610">Example</span></span>

<span data-ttu-id="b5b6b-611">次の例では、 **Association** **要素を使用** して、 **FK \_ CustomerOrders** foreign key 制約に参加する列を指定しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-611">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="returntype-element-ssdl"></a><span data-ttu-id="b5b6b-612">ReturnType 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-612">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-613">ストアスキーマ定義言語 (SSDL) の **ReturnType** 要素は、 **function** 要素で定義されている関数の戻り値の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-613">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="b5b6b-614">関数の戻り値の型は、 **ReturnType** 属性と共に指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-614">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="b5b6b-615">関数の戻り値の型は、 **type** 属性または **ReturnType** 要素を使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-615">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="b5b6b-616">**ReturnType**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-616">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="b5b6b-617">CollectionType (1)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-617">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="b5b6b-618">任意の数の annotation 属性 (カスタム XML 属性) を **ReturnType** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-618">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="b5b6b-619">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-619">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-620">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-620">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-621">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-621">Example</span></span>

<span data-ttu-id="b5b6b-622">次の例では、行のコレクションを返す **関数** を使用します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-622">The following example uses a **Function** that returns a collection of rows.</span></span>

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


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="b5b6b-623">RowType 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-623">RowType Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-624">ストアスキーマ定義言語 (SSDL) の **RowType** 要素は、名前のない構造体を、ストアで定義されている関数の戻り値の型として定義します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-624">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="b5b6b-625">**RowType**要素は**CollectionType**要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-625">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="b5b6b-626">**RowType**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-626">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="b5b6b-627">Property (1 個以上)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-627">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="b5b6b-628">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-628">Example</span></span>

<span data-ttu-id="b5b6b-629">次の例では、 **CollectionType** 要素を使用して、 **RowType** 要素で指定されているように、関数が行のコレクションを返すことを指定するストア関数を示します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-629">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


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

## <a name="schema-element-ssdl"></a><span data-ttu-id="b5b6b-630">Schema 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-630">Schema Element (SSDL)</span></span>

<span data-ttu-id="b5b6b-631">ストアスキーマ定義言語 (SSDL) の **schema** 要素は、ストレージモデル定義のルート要素です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-631">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="b5b6b-632">ストレージ モデルを構成するオブジェクト、関数、およびコンテナーの定義を格納します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-632">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="b5b6b-633">**Schema**要素には、次の子要素を0個以上含めることができます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-633">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="b5b6b-634">関連付け</span><span class="sxs-lookup"><span data-stu-id="b5b6b-634">Association</span></span>
-   <span data-ttu-id="b5b6b-635">EntityType</span><span class="sxs-lookup"><span data-stu-id="b5b6b-635">EntityType</span></span>
-   <span data-ttu-id="b5b6b-636">EntityContainer 要素</span><span class="sxs-lookup"><span data-stu-id="b5b6b-636">EntityContainer</span></span>
-   <span data-ttu-id="b5b6b-637">関数</span><span class="sxs-lookup"><span data-stu-id="b5b6b-637">Function</span></span>

<span data-ttu-id="b5b6b-638">**Schema**要素は、**名前空間**属性を使用して、ストレージモデル内のエンティティ型とアソシエーションオブジェクトの名前空間を定義します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-638">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="b5b6b-639">1 つの名前空間内で 2 つのオブジェクトが同じ名前を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-639">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="b5b6b-640">ストレージモデルの名前空間は、 **Schema** 要素の XML 名前空間とは異なります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-640">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="b5b6b-641">ストレージモデルの名前空間 ( **名前空間** 属性で定義) は、エンティティ型とアソシエーション型の論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-641">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="b5b6b-642">**Schema**要素の XML 名前空間 ( **xmlns**属性で示されます) は、 **schema**要素の子要素と属性の既定の名前空間です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-642">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="b5b6b-643">フォームの XML 名前空間 https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (YYYY と MM は、それぞれ年と月を表します) は SSDL 用に予約されています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-643">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="b5b6b-644">カスタム要素と属性は、このフォームがある名前空間に存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-644">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b5b6b-645">該当する属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-645">Applicable Attributes</span></span>

<span data-ttu-id="b5b6b-646">次の表では、 **スキーマ** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-646">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="b5b6b-647">属性名</span><span class="sxs-lookup"><span data-stu-id="b5b6b-647">Attribute Name</span></span>            | <span data-ttu-id="b5b6b-648">必須</span><span class="sxs-lookup"><span data-stu-id="b5b6b-648">Is Required</span></span> | <span data-ttu-id="b5b6b-649">値</span><span class="sxs-lookup"><span data-stu-id="b5b6b-649">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-650">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-650">**Namespace**</span></span>             | <span data-ttu-id="b5b6b-651">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-651">Yes</span></span>         | <span data-ttu-id="b5b6b-652">ストレージ モデルの名前空間。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-652">The namespace of the storage model.</span></span> <span data-ttu-id="b5b6b-653">**名前空間**属性の値は、型の完全修飾名を形成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-653">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="b5b6b-654">たとえば、 *Customer*という名前の**entitytype**が examplemodel.store 名前空間にある場合、 **entitytype**の完全修飾名は examplemodel.store になります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-654">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="b5b6b-655">次の文字列は、 **Namespace** 属性の値として使用できません: **System**、 **Transient**、または **Edm**。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-655">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="b5b6b-656">**名前空間**属性の値を、CSDL スキーマ要素の**namespace**属性の値と同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-656">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="b5b6b-657">**Alias**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-657">**Alias**</span></span>                 | <span data-ttu-id="b5b6b-658">いいえ</span><span class="sxs-lookup"><span data-stu-id="b5b6b-658">No</span></span>          | <span data-ttu-id="b5b6b-659">名前空間の名前の代わりに使用される識別子。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-659">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="b5b6b-660">たとえば、 *Customer*という名前の**entitytype**が examplemodel.store 名前空間にあり、 **Alias**属性の値が*StorageModel*の場合、 **entitytype**の完全修飾名として StorageModel を使用できます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-660">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="b5b6b-661">**プロバイダー**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-661">**Provider**</span></span>              | <span data-ttu-id="b5b6b-662">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-662">Yes</span></span>         | <span data-ttu-id="b5b6b-663">データ プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-663">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="b5b6b-664">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-664">**ProviderManifestToken**</span></span> | <span data-ttu-id="b5b6b-665">はい</span><span class="sxs-lookup"><span data-stu-id="b5b6b-665">Yes</span></span>         | <span data-ttu-id="b5b6b-666">返すプロバイダー マニフェストをプロバイダーに示すトークン。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-666">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="b5b6b-667">トークンの形式は定義されていません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-667">No format for the token is defined.</span></span> <span data-ttu-id="b5b6b-668">トークンの値は、プロバイダーで定義されています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-668">Values for the token are defined by the provider.</span></span> <span data-ttu-id="b5b6b-669">SQL Server プロバイダーマニフェストトークンの詳細については、「SqlClient for Entity Framework」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-669">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="b5b6b-670">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-670">Example</span></span>

<span data-ttu-id="b5b6b-671">**EntityContainer**要素、2つの**EntityType**要素、および1つの**Association**要素を含む**スキーマ**要素の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-671">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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

## <a name="annotation-attributes"></a><span data-ttu-id="b5b6b-672">annotation 属性</span><span class="sxs-lookup"><span data-stu-id="b5b6b-672">Annotation Attributes</span></span>

<span data-ttu-id="b5b6b-673">ストア スキーマ定義言語 (SSDL) の annotation 属性は、ストレージ モデルの要素に関する追加のメタデータを指定するストレージ モデルのカスタム XML 属性です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-673">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="b5b6b-674">有効な XML 構造が必要であることに加え、annotation 属性には次の制約が適用されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-674">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="b5b6b-675">annotation 属性は、SSDL 用に予約された XML 名前空間に存在しない。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-675">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="b5b6b-676">2 つの任意の annotation 属性の完全修飾名が同じではない。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-676">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="b5b6b-677">複数の annotation 属性を特定の 1 つの SSDL 要素に適用することができる。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-677">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="b5b6b-678">Annotation 要素に含まれるメタデータには、実行時に、system.string 名前空間のクラスを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-678">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-679">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-679">Example</span></span>

<span data-ttu-id="b5b6b-680">次の例は、 **OrderId** プロパティに annotation 属性が適用されている EntityType 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-680">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="b5b6b-681">この例では、 **EntityType** 要素に追加された annotation 要素も示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-681">The example also show an annotation element added to the **EntityType** element.</span></span>

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

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="b5b6b-682">annotation 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-682">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="b5b6b-683">ストア スキーマ定義言語 (SSDL) の annotation 要素は、ストレージ モデルに関する追加のメタデータを指定するストレージ モデルのカスタム XML 要素です。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-683">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="b5b6b-684">有効な XML 構造が必要であることに加え、annotation 要素には次の制約が適用されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-684">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="b5b6b-685">annotation 要素は、SSDL 用に予約された XML 名前空間内に存在できません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-685">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="b5b6b-686">2 つの annotation 要素の完全修飾名を同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-686">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="b5b6b-687">annotation 要素は、特定の SSDL 要素のその他すべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-687">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="b5b6b-688">複数の annotation 要素を特定の SSDL 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-688">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="b5b6b-689">.NET Framework version 4 以降では、annotation 要素に含まれるメタデータに、実行時に、system.string 名前空間のクラスを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-689">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="b5b6b-690">例</span><span class="sxs-lookup"><span data-stu-id="b5b6b-690">Example</span></span>

<span data-ttu-id="b5b6b-691">Annotation 要素 (**customelement**) を持つ EntityType 要素の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-691">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="b5b6b-692">また、この例では、 **OrderId** プロパティに適用される annotation 属性も示しています。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-692">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

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

## <a name="facets-ssdl"></a><span data-ttu-id="b5b6b-693">ファセット (SSDL)</span><span class="sxs-lookup"><span data-stu-id="b5b6b-693">Facets (SSDL)</span></span>

<span data-ttu-id="b5b6b-694">ストア スキーマ定義言語 (SSDL) のファセットは、Property 要素で指定された列型に対する制約を表します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-694">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="b5b6b-695">ファセットは、 **プロパティ** 要素の XML 属性として実装されます。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-695">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="b5b6b-696">次の表では、SSDL でサポートされるファセットについて説明します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-696">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="b5b6b-697">ファセット</span><span class="sxs-lookup"><span data-stu-id="b5b6b-697">Facet</span></span>           | <span data-ttu-id="b5b6b-698">説明</span><span class="sxs-lookup"><span data-stu-id="b5b6b-698">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b5b6b-699">**Collation**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-699">**Collation**</span></span>   | <span data-ttu-id="b5b6b-700">プロパティの値に対して比較と順序付け操作を行うときに使用する照合シーケンス (または並べ替え順序) を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-700">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="b5b6b-701">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-701">**FixedLength**</span></span> | <span data-ttu-id="b5b6b-702">列値の長さを可変とすることができるかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-702">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="b5b6b-703">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-703">**MaxLength**</span></span>   | <span data-ttu-id="b5b6b-704">列値の最大長を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-704">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="b5b6b-705">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-705">**Precision**</span></span>   | <span data-ttu-id="b5b6b-706">**Decimal**型のプロパティの場合、プロパティ値が持つことができる桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-706">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="b5b6b-707">**Time**、 **DateTime**、および**DateTimeOffset**型のプロパティについては、列の値の秒部分の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-707">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="b5b6b-708">**スケール**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-708">**Scale**</span></span>       | <span data-ttu-id="b5b6b-709">列値の小数点の右側の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-709">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="b5b6b-710">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="b5b6b-710">**Unicode**</span></span>     | <span data-ttu-id="b5b6b-711">列値を Unicode として保存するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="b5b6b-711">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
