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
# <a name="ssdl-specification"></a><span data-ttu-id="3a946-102">SSDL 仕様</span><span class="sxs-lookup"><span data-stu-id="3a946-102">SSDL Specification</span></span>
<span data-ttu-id="3a946-103">ストア スキーマ定義言語 (SSDL) は、Entity Framework アプリケーションのストレージ モデルを表す XML ベースの言語です。</span><span class="sxs-lookup"><span data-stu-id="3a946-103">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="3a946-104">Entity Framework アプリケーションでストレージ モデルのメタデータが読み込まれた .ssdl ファイル (SSDL で記述) から、System.Data.Metadata.Edm.StoreItemCollection のインスタンスに、アクセスできるメソッドを使用して、System.Data.Metadata.Edm.MetadataWorkspace クラスです。</span><span class="sxs-lookup"><span data-stu-id="3a946-104">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="3a946-105">Entity Framework では、ストレージ モデルのメタデータを使用してストアに固有のコマンドを概念モデルに対するクエリに変換します。</span><span class="sxs-lookup"><span data-stu-id="3a946-105">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="3a946-106">Entity Framework デザイナー (EF Designer) は、デザイン時に .edmx ファイルでストレージ モデル情報を格納します。</span><span class="sxs-lookup"><span data-stu-id="3a946-106">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="3a946-107">ビルド時に、エンティティ デザイナーは、実行時に Entity Framework によって必要な .ssdl ファイルを作成するのに、.edmx ファイルの情報を使用します。</span><span class="sxs-lookup"><span data-stu-id="3a946-107">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="3a946-108">SSDL のバージョンは、XML 名前空間で区別されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-108">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="3a946-109">SSDL のバージョン</span><span class="sxs-lookup"><span data-stu-id="3a946-109">SSDL Version</span></span> | <span data-ttu-id="3a946-110">XML Namespace</span><span class="sxs-lookup"><span data-stu-id="3a946-110">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="3a946-111">SSDL v1</span><span class="sxs-lookup"><span data-stu-id="3a946-111">SSDL v1</span></span>      | http://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="3a946-112">SSDL v2</span><span class="sxs-lookup"><span data-stu-id="3a946-112">SSDL v2</span></span>      | http://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="3a946-113">SSDL v3</span><span class="sxs-lookup"><span data-stu-id="3a946-113">SSDL v3</span></span>      | http://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="3a946-114">Association 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-114">Association Element (SSDL)</span></span>

<span data-ttu-id="3a946-115">**アソシエーション**ストア スキーマ定義言語 (SSDL) 内の要素は、基になるデータベースの外部キー制約に参加するテーブル列を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-115">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="3a946-116">2 つの必須の子 End 要素は、アソシエーションの end にあるテーブルと各 end の多重度を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-116">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="3a946-117">省略可能な ReferentialConstraint 要素には、参加している列と同様に、アソシエーションのプリンシパルと依存 end を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-117">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="3a946-118">ない場合は**ReferentialConstraint**要素が存在する、AssociationSetMapping 要素は、アソシエーションの列マッピングを指定するために使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-118">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="3a946-119">**アソシエーション**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-119">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-120">(0 個または 1 つ) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3a946-120">Documentation (zero or one)</span></span>
-   <span data-ttu-id="3a946-121">終了 (正確には 2 つ)</span><span class="sxs-lookup"><span data-stu-id="3a946-121">End (exactly two)</span></span>
-   <span data-ttu-id="3a946-122">ReferentialConstraint (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="3a946-122">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="3a946-123">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a946-123">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-124">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-124">Applicable Attributes</span></span>

<span data-ttu-id="3a946-125">次の表に適用できる属性、**アソシエーション**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-125">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="3a946-126">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-126">Attribute Name</span></span> | <span data-ttu-id="3a946-127">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-127">Is Required</span></span> | <span data-ttu-id="3a946-128">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-128">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-129">**Name**</span><span class="sxs-lookup"><span data-stu-id="3a946-129">**Name**</span></span>       | <span data-ttu-id="3a946-130">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-130">Yes</span></span>         | <span data-ttu-id="3a946-131">基盤となるデータベースの対応する外部キー制約の名前。</span><span class="sxs-lookup"><span data-stu-id="3a946-131">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="3a946-132">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**アソシエーション**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-132">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="3a946-133">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-133">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="3a946-134">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-134">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-135">例</span><span class="sxs-lookup"><span data-stu-id="3a946-135">Example</span></span>

<span data-ttu-id="3a946-136">次の例は、**アソシエーション**を使用する要素を**ReferentialConstraint**に参加する列を指定する要素、 **FK\_CustomerOrders**外部キー制約。</span><span class="sxs-lookup"><span data-stu-id="3a946-136">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="associationset-element-ssdl"></a><span data-ttu-id="3a946-137">AssociationSet 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-137">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="3a946-138">**AssociationSet**ストア スキーマ定義言語 (SSDL) 内の要素は、基になるデータベース内の 2 つのテーブル間の外部キー制約を表します。</span><span class="sxs-lookup"><span data-stu-id="3a946-138">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="3a946-139">外部キー制約に参加しているテーブルの列は、Association 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-139">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="3a946-140">**アソシエーション**に対応する要素を指定した**AssociationSet**要素が指定されて、**アソシエーション**の属性、 **AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-140">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="3a946-141">SSDL アソシエーション セットは、AssociationSetMapping 要素によって CSDL アソシエーション セットにマップされます。</span><span class="sxs-lookup"><span data-stu-id="3a946-141">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="3a946-142">対応する、ReferentialConstraint 要素を使用して特定の CSDL アソシエーションの CSDL アソシエーション セットの場合を定義するただし、 **AssociationSetMapping**要素が必要です。</span><span class="sxs-lookup"><span data-stu-id="3a946-142">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="3a946-143">この場合は場合、 **AssociationSetMapping**要素が存在する、定義するマッピングによって上書きされます、 **ReferentialConstraint**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-143">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="3a946-144">**AssociationSet**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-144">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-145">(0 個または 1 つ) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3a946-145">Documentation (zero or one)</span></span>
-   <span data-ttu-id="3a946-146">終了 (0 個または 2 つ)</span><span class="sxs-lookup"><span data-stu-id="3a946-146">End (zero or two)</span></span>
-   <span data-ttu-id="3a946-147">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a946-147">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-148">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-148">Applicable Attributes</span></span>

<span data-ttu-id="3a946-149">次の表に適用できる属性、 **AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-149">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="3a946-150">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-150">Attribute Name</span></span>  | <span data-ttu-id="3a946-151">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-151">Is Required</span></span> | <span data-ttu-id="3a946-152">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-152">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-153">**Name**</span><span class="sxs-lookup"><span data-stu-id="3a946-153">**Name**</span></span>        | <span data-ttu-id="3a946-154">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-154">Yes</span></span>         | <span data-ttu-id="3a946-155">アソシエーション セットが表す外部キー制約の名前。</span><span class="sxs-lookup"><span data-stu-id="3a946-155">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="3a946-156">**関連付け**</span><span class="sxs-lookup"><span data-stu-id="3a946-156">**Association**</span></span> | <span data-ttu-id="3a946-157">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-157">Yes</span></span>         | <span data-ttu-id="3a946-158">外部キー制約に参加する列を定義するアソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="3a946-158">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="3a946-159">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-159">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="3a946-160">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-160">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="3a946-161">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-161">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-162">例</span><span class="sxs-lookup"><span data-stu-id="3a946-162">Example</span></span>

<span data-ttu-id="3a946-163">次の例は、 **AssociationSet**要素を表す、`FK_CustomerOrders`基になるデータベースの外部キー制約。</span><span class="sxs-lookup"><span data-stu-id="3a946-163">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="3a946-164">CollectionType 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-164">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="3a946-165">**CollectionType**ストア スキーマ定義言語 (SSDL) 内の要素は、関数の戻り値の型がコレクションであるを指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-165">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="3a946-166">**CollectionType** ReturnType 要素の子である要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-166">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="3a946-167">RowType の子要素を使用して、コレクションの型が指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-167">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="3a946-168">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **CollectionType**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-168">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="3a946-169">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-169">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="3a946-170">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-170">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-171">例</span><span class="sxs-lookup"><span data-stu-id="3a946-171">Example</span></span>

<span data-ttu-id="3a946-172">次の例では、使用する関数を**CollectionType**関数によって返される行のコレクションを指定する要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-172">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

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

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="3a946-173">CommandText 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-173">CommandText Element (SSDL)</span></span>

<span data-ttu-id="3a946-174">**CommandText**データベースで実行される SQL ステートメントを定義できるようにする関数要素の子であるストア スキーマ定義言語 (SSDL) 内の要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-174">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="3a946-175">**CommandText**要素では、データベース内のストアド プロシージャのような機能を追加することができますが、定義する、 **CommandText**ストレージ モデル内の要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-175">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="3a946-176">**CommandText**要素が子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="3a946-176">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="3a946-177">本体、 **CommandText**要素は、基になるデータベースの有効な SQL ステートメントである必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-177">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="3a946-178">適用可能な属性がない、 **CommandText**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-178">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-179">例</span><span class="sxs-lookup"><span data-stu-id="3a946-179">Example</span></span>

<span data-ttu-id="3a946-180">次の例は、**関数**子要素を持つ**CommandText**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-180">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="3a946-181">公開、 **UpdateProductInOrder**概念モデルにインポートすることにより、ObjectContext のメソッドとして機能します。</span><span class="sxs-lookup"><span data-stu-id="3a946-181">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

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

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="3a946-182">DefiningQuery 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-182">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="3a946-183">**DefiningQuery**ストア スキーマ定義言語 (SSDL) 内の要素では、基になるデータベースで直接 SQL ステートメントを実行できます。</span><span class="sxs-lookup"><span data-stu-id="3a946-183">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="3a946-184">**DefiningQuery**要素は、データベース ビューのようなよく使用しますが、データベースではなくストレージ モデルのビューが定義されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-184">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="3a946-185">定義されているビューを**DefiningQuery**要素は、EntitySetMapping 要素を使用して、概念モデルのエンティティ型にマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-185">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="3a946-186">このようなマッピングは、読み取り専用です。</span><span class="sxs-lookup"><span data-stu-id="3a946-186">These mappings are read-only.</span></span>  

<span data-ttu-id="3a946-187">次の SSDL 構文の宣言を示しています、 **EntitySet**続けて、 **DefiningQuery**ビューを取得するために使用するクエリを含む要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-187">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

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

<span data-ttu-id="3a946-188">Entity Framework でストアド プロシージャを使用すると、ビューに対する読み取り/書き込みシナリオを有効にします。</span><span class="sxs-lookup"><span data-stu-id="3a946-188">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span> <span data-ttu-id="3a946-189">データを取得、および変更ストアド プロシージャによる処理のベース テーブルとしてデータ ソース ビューまたは Entity SQL ビューのいずれかを使用できます。</span><span class="sxs-lookup"><span data-stu-id="3a946-189">You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="3a946-190">使用することができます、 **DefiningQuery**を Microsoft SQL Server Compact 3.5 をターゲット要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-190">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="3a946-191">SQL Server Compact 3.5 がストアド プロシージャをサポートしていない場合と同様の機能を実装することができます、 **DefiningQuery**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-191">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="3a946-192">他にこの要素が役立つ状況として、ストアド プロシージャを作成してプログラミング言語で使用されているデータ型とデータ ソースで使用されているデータ型の不一致を克服することができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-192">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="3a946-193">作成、 **DefiningQuery**一連のパラメーターを受け取り、データを削除するストアド プロシージャなどのパラメーターの異なるセットでのストアド プロシージャを呼び出しています。</span><span class="sxs-lookup"><span data-stu-id="3a946-193">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="3a946-194">Dependent 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-194">Dependent Element (SSDL)</span></span>

<span data-ttu-id="3a946-195">**依存**ストア スキーマ定義言語 (SSDL) 内の要素は、外部キー制約 (参照に関する制約とも呼ばれます) の依存 end を定義する ReferentialConstraint 要素に子要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-195">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="3a946-196">**依存**要素は、主キー列 (または列) を参照するテーブルの列 (または列) を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-196">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="3a946-197">**PropertyRef**のどの列が参照されている要素を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-197">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="3a946-198">プリンシパル要素で指定されている列によって参照される主キー列を指定します、**依存**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-198">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="3a946-199">**依存**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-199">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-200">PropertyRef (1 つまたは複数)</span><span class="sxs-lookup"><span data-stu-id="3a946-200">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="3a946-201">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a946-201">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-202">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-202">Applicable Attributes</span></span>

<span data-ttu-id="3a946-203">次の表に適用できる属性、**依存**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-203">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="3a946-204">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-204">Attribute Name</span></span> | <span data-ttu-id="3a946-205">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-205">Is Required</span></span> | <span data-ttu-id="3a946-206">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-206">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-207">**ロール**</span><span class="sxs-lookup"><span data-stu-id="3a946-207">**Role**</span></span>       | <span data-ttu-id="3a946-208">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-208">Yes</span></span>         | <span data-ttu-id="3a946-209">同じ値、**ロール**(使用) する場合の対応する終了要素の属性。 それ以外の場合、テーブルの名前を格納した参照元の列。</span><span class="sxs-lookup"><span data-stu-id="3a946-209">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="3a946-210">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**依存**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-210">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="3a946-211">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-211">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="3a946-212">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-212">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-213">例</span><span class="sxs-lookup"><span data-stu-id="3a946-213">Example</span></span>

<span data-ttu-id="3a946-214">次の例を使用する Association 要素を示しています、 **ReferentialConstraint**に参加する列を指定する要素、 **FK\_CustomerOrders**外部キー制約です。</span><span class="sxs-lookup"><span data-stu-id="3a946-214">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="3a946-215">**依存**要素を指定します、 **CustomerId**の列、**順序**テーブル制約の依存 end として。</span><span class="sxs-lookup"><span data-stu-id="3a946-215">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

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

## <a name="documentation-element-ssdl"></a><span data-ttu-id="3a946-216">Documentation 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-216">Documentation Element (SSDL)</span></span>

<span data-ttu-id="3a946-217">**ドキュメント**親要素で定義されているオブジェクトに関する情報を提供するストア スキーマ定義言語 (SSDL) 内の要素を使用できます。</span><span class="sxs-lookup"><span data-stu-id="3a946-217">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="3a946-218">**ドキュメント**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-218">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-219">**概要**: 親要素の簡単な説明。</span><span class="sxs-lookup"><span data-stu-id="3a946-219">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="3a946-220">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="3a946-220">(zero or one element)</span></span>
-   <span data-ttu-id="3a946-221">**LongDescription**: 親要素の説明を提供します。</span><span class="sxs-lookup"><span data-stu-id="3a946-221">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="3a946-222">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="3a946-222">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-223">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-223">Applicable Attributes</span></span>

<span data-ttu-id="3a946-224">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**ドキュメント**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-224">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="3a946-225">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-225">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="3a946-226">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-226">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-227">例</span><span class="sxs-lookup"><span data-stu-id="3a946-227">Example</span></span>

<span data-ttu-id="3a946-228">次の例は、**ドキュメント**EntityType 要素の子要素としての要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-228">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

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

## <a name="end-element-ssdl"></a><span data-ttu-id="3a946-229">End 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-229">End Element (SSDL)</span></span>

<span data-ttu-id="3a946-230">**エンド**ストア スキーマ定義言語 (SSDL) 内の要素が基になるデータベース テーブルと外部キー制約の一端にある行の数を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-230">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="3a946-231">**エンド**要素は Association 要素または AssociationSet 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-231">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="3a946-232">それぞれの場合において、使用可能な子要素と該当する属性は異なります。</span><span class="sxs-lookup"><span data-stu-id="3a946-232">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="3a946-233">Association 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="3a946-233">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="3a946-234">**エンド**要素 (の子として、**アソシエーション**要素) と外部キー制約の最後の行の数とテーブルを指定します、**型**と**多重度**それぞれの属性します。</span><span class="sxs-lookup"><span data-stu-id="3a946-234">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="3a946-235">外部キー制約の End は、SSDL アソシエーションの一部として定義されます。SSDL アソシエーションには End が 2 つ必要です。</span><span class="sxs-lookup"><span data-stu-id="3a946-235">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="3a946-236">**エンド**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-236">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-237">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3a946-237">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="3a946-238">OnDelete (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="3a946-238">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="3a946-239">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="3a946-239">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="3a946-240">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-240">Applicable Attributes</span></span>

<span data-ttu-id="3a946-241">次の表に適用できる属性、**エンド**要素の子である場合に、**アソシエーション**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-241">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="3a946-242">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-242">Attribute Name</span></span>   | <span data-ttu-id="3a946-243">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-243">Is Required</span></span> | <span data-ttu-id="3a946-244">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-244">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-245">**Type**</span><span class="sxs-lookup"><span data-stu-id="3a946-245">**Type**</span></span>         | <span data-ttu-id="3a946-246">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-246">Yes</span></span>         | <span data-ttu-id="3a946-247">外部キー制約の end にある SSDL エンティティ セットの完全修飾名。</span><span class="sxs-lookup"><span data-stu-id="3a946-247">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="3a946-248">**ロール**</span><span class="sxs-lookup"><span data-stu-id="3a946-248">**Role**</span></span>         | <span data-ttu-id="3a946-249">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-249">No</span></span>          | <span data-ttu-id="3a946-250">値、**ロール**(使用) する場合、対応する ReferentialConstraint 要素のプリンシパル、または依存のいずれかの要素の属性します。</span><span class="sxs-lookup"><span data-stu-id="3a946-250">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="3a946-251">**多重度**</span><span class="sxs-lookup"><span data-stu-id="3a946-251">**Multiplicity**</span></span> | <span data-ttu-id="3a946-252">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-252">Yes</span></span>         | <span data-ttu-id="3a946-253">**1**、 **0..1**、または**\*** 外部キー制約の end に存在できる行の数によって異なります。</span><span class="sxs-lookup"><span data-stu-id="3a946-253">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="3a946-254">**1**その 1 つの行が外部キー制約の end に存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="3a946-254">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="3a946-255">**0..1**ことを示します、外部キー制約の end に 0 個または 1 つの行が存在します。</span><span class="sxs-lookup"><span data-stu-id="3a946-255">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="3a946-256">**\*** 0、1 つまたは複数の行が、外部キー制約の end が存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="3a946-256">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="3a946-257">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-257">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="3a946-258">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-258">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="3a946-259">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-259">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="3a946-260">例</span><span class="sxs-lookup"><span data-stu-id="3a946-260">Example</span></span>

<span data-ttu-id="3a946-261">次の例は、**アソシエーション**を定義する要素、 **FK\_CustomerOrders**外部キー制約。</span><span class="sxs-lookup"><span data-stu-id="3a946-261">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="3a946-262">**多重度**それぞれで指定した値**エンド**要素で多くの行を示すため、**注文**テーブルの行に関連付けることができます、**顧客**テーブルが 1 つのみの行、**顧客**テーブルの行に関連付けることができます、**注文**テーブル。</span><span class="sxs-lookup"><span data-stu-id="3a946-262">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="3a946-263">さらに、 **OnDelete**要素は、のすべての行を示します、**注文**の特定の行を参照するテーブル、**顧客**場合は、テーブルを削除するが内の行**顧客**テーブルを削除します。</span><span class="sxs-lookup"><span data-stu-id="3a946-263">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="3a946-264">AssociationSet 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="3a946-264">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="3a946-265">**エンド**要素 (の子として、 **AssociationSet**要素)、基になるデータベースで外部キー制約の一方の end でテーブルを指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-265">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="3a946-266">**エンド**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-266">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-267">(0 個または 1 つ) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3a946-267">Documentation (zero or one)</span></span>
-   <span data-ttu-id="3a946-268">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a946-268">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="3a946-269">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-269">Applicable Attributes</span></span>

<span data-ttu-id="3a946-270">次の表に適用できる属性、**エンド**要素の子である場合に、 **AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-270">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="3a946-271">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-271">Attribute Name</span></span> | <span data-ttu-id="3a946-272">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-272">Is Required</span></span> | <span data-ttu-id="3a946-273">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-273">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-274">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="3a946-274">**EntitySet**</span></span>  | <span data-ttu-id="3a946-275">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-275">Yes</span></span>         | <span data-ttu-id="3a946-276">外部キー制約の end にある SSDL エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="3a946-276">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="3a946-277">**ロール**</span><span class="sxs-lookup"><span data-stu-id="3a946-277">**Role**</span></span>       | <span data-ttu-id="3a946-278">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-278">No</span></span>          | <span data-ttu-id="3a946-279">いずれかの値、**ロール**いずれかで指定された属性**エンド**対応する Association 要素の要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-279">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="3a946-280">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-280">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="3a946-281">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-281">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="3a946-282">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-282">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="3a946-283">例</span><span class="sxs-lookup"><span data-stu-id="3a946-283">Example</span></span>

<span data-ttu-id="3a946-284">次の例は、 **EntityContainer**を持つ要素を**AssociationSet**要素を持つ**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-284">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

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

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="3a946-285">EntityContainer 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-285">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="3a946-286">**EntityContainer**ストア スキーマ定義言語 (SSDL) 内の要素が Entity Framework アプリケーションで基になるデータ ソースの構造について説明します (EntitySet の要素で定義されている) SSDL エンティティ セット内のテーブルを表す。データベース、テーブル内の行を表す SSDL のエンティティ型 (EntityType 要素で定義されている) と (AssociationSet 要素で定義されている) のアソシエーション セットは、データベースの外部キー制約を表します。</span><span class="sxs-lookup"><span data-stu-id="3a946-286">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="3a946-287">EntityContainerMapping 要素を通じて概念モデルのエンティティ コンテナー、ストレージ モデルのエンティティ コンテナーにマップします。</span><span class="sxs-lookup"><span data-stu-id="3a946-287">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="3a946-288">**EntityContainer**要素は、0 個または 1 つのドキュメント要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-288">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="3a946-289">場合、**ドキュメント**要素が存在する、他のすべての子要素が前に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-289">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="3a946-290">**EntityContainer**要素 (表示順) で、次の子要素の 0 個以上を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-290">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-291">EntitySet</span><span class="sxs-lookup"><span data-stu-id="3a946-291">EntitySet</span></span>
-   <span data-ttu-id="3a946-292">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="3a946-292">AssociationSet</span></span>
-   <span data-ttu-id="3a946-293">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="3a946-293">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-294">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-294">Applicable Attributes</span></span>

<span data-ttu-id="3a946-295">次の表に適用できる属性の説明、 **EntityContainer**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-295">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="3a946-296">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-296">Attribute Name</span></span> | <span data-ttu-id="3a946-297">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-297">Is Required</span></span> | <span data-ttu-id="3a946-298">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-298">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="3a946-299">**Name**</span><span class="sxs-lookup"><span data-stu-id="3a946-299">**Name**</span></span>       | <span data-ttu-id="3a946-300">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-300">Yes</span></span>         | <span data-ttu-id="3a946-301">エンティティ コンテナー名。</span><span class="sxs-lookup"><span data-stu-id="3a946-301">The name of the entity container.</span></span> <span data-ttu-id="3a946-302">この名前にピリオド (.) を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="3a946-302">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="3a946-303">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntityContainer**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-303">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="3a946-304">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-304">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="3a946-305">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-305">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-306">例</span><span class="sxs-lookup"><span data-stu-id="3a946-306">Example</span></span>

<span data-ttu-id="3a946-307">次の例は、 **EntityContainer** 2 つのエンティティ セットと 1 つのアソシエーション セットを定義する要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-307">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="3a946-308">エンティティ型およびアソシエーション型の名前は、概念モデルの名前空間名によって修飾されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-308">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

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

## <a name="entityset-element-ssdl"></a><span data-ttu-id="3a946-309">EntitySet 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-309">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="3a946-310">**EntitySet**ストア スキーマ定義言語 (SSDL) 内の要素は、テーブルまたは基になるデータベースのビューを表します。</span><span class="sxs-lookup"><span data-stu-id="3a946-310">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="3a946-311">Ssdl EntityType 要素は、テーブルまたはビュー内の行を表します。</span><span class="sxs-lookup"><span data-stu-id="3a946-311">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="3a946-312">**EntityType**の属性、 **EntitySet**要素を SSDL エンティティ セット内の行を表す特定の SSDL エンティティ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-312">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="3a946-313">CSDL のエンティティ セットと SSDL エンティティ セット間のマッピングは、EntitySetMapping 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-313">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="3a946-314">**EntitySet**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-314">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-315">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3a946-315">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="3a946-316">DefiningQuery (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="3a946-316">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="3a946-317">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="3a946-317">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-318">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-318">Applicable Attributes</span></span>

<span data-ttu-id="3a946-319">次の表に適用できる属性、 **EntitySet**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-319">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="3a946-320">(ここでは示しません) いくつかの属性で修飾できます、**格納**エイリアス。</span><span class="sxs-lookup"><span data-stu-id="3a946-320">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="3a946-321">これらの属性は、モデルを更新するときに、モデルの更新ウィザードによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-321">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="3a946-322">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-322">Attribute Name</span></span> | <span data-ttu-id="3a946-323">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-323">Is Required</span></span> | <span data-ttu-id="3a946-324">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-324">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-325">**Name**</span><span class="sxs-lookup"><span data-stu-id="3a946-325">**Name**</span></span>       | <span data-ttu-id="3a946-326">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-326">Yes</span></span>         | <span data-ttu-id="3a946-327">エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="3a946-327">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="3a946-328">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="3a946-328">**EntityType**</span></span> | <span data-ttu-id="3a946-329">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-329">Yes</span></span>         | <span data-ttu-id="3a946-330">エンティティ型の完全修飾名。そのインスタンスは、エンティティ セットに格納されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-330">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="3a946-331">**[スキーマ]**</span><span class="sxs-lookup"><span data-stu-id="3a946-331">**Schema**</span></span>     | <span data-ttu-id="3a946-332">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-332">No</span></span>          | <span data-ttu-id="3a946-333">データベース スキーマ。</span><span class="sxs-lookup"><span data-stu-id="3a946-333">The database schema.</span></span>                                                                     |
| <span data-ttu-id="3a946-334">**テーブル**</span><span class="sxs-lookup"><span data-stu-id="3a946-334">**Table**</span></span>      | <span data-ttu-id="3a946-335">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-335">No</span></span>          | <span data-ttu-id="3a946-336">データベース テーブル。</span><span class="sxs-lookup"><span data-stu-id="3a946-336">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="3a946-337">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntitySet**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-337">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="3a946-338">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-338">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="3a946-339">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-339">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-340">例</span><span class="sxs-lookup"><span data-stu-id="3a946-340">Example</span></span>

<span data-ttu-id="3a946-341">次の例は、 **EntityContainer** 2 つ含まれる要素**EntitySet**要素と 1 つ**AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-341">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

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

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="3a946-342">EntityType 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-342">EntityType Element (SSDL)</span></span>

<span data-ttu-id="3a946-343">**EntityType**ストア スキーマ定義言語 (SSDL) 内の要素は、テーブルまたはビューの基になるデータベース内の行を表します。</span><span class="sxs-lookup"><span data-stu-id="3a946-343">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="3a946-344">テーブルまたはビューの行が発生する SSDL の EntitySet 要素を表します。</span><span class="sxs-lookup"><span data-stu-id="3a946-344">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="3a946-345">**EntityType**の属性、 **EntitySet**要素を SSDL エンティティ セット内の行を表す特定の SSDL エンティティ型を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-345">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="3a946-346">SSDL エンティティ型と CSDL のエンティティ型間のマッピングは、EntityTypeMapping 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-346">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="3a946-347">**EntityType**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-347">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-348">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3a946-348">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="3a946-349">キー (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="3a946-349">Key (zero or one element)</span></span>
-   <span data-ttu-id="3a946-350">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="3a946-350">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-351">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-351">Applicable Attributes</span></span>

<span data-ttu-id="3a946-352">次の表に適用できる属性の説明、 **EntityType**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-352">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="3a946-353">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-353">Attribute Name</span></span> | <span data-ttu-id="3a946-354">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-354">Is Required</span></span> | <span data-ttu-id="3a946-355">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-355">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-356">**Name**</span><span class="sxs-lookup"><span data-stu-id="3a946-356">**Name**</span></span>       | <span data-ttu-id="3a946-357">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-357">Yes</span></span>         | <span data-ttu-id="3a946-358">エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="3a946-358">The name of the entity type.</span></span> <span data-ttu-id="3a946-359">通常、この値は、エンティティ型が行を表すテーブルの名前と同じです。</span><span class="sxs-lookup"><span data-stu-id="3a946-359">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="3a946-360">この値にピリオド (.) を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="3a946-360">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="3a946-361">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntityType**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-361">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="3a946-362">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-362">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="3a946-363">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-363">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-364">例</span><span class="sxs-lookup"><span data-stu-id="3a946-364">Example</span></span>

<span data-ttu-id="3a946-365">次の例は、 **EntityType** 2 つのプロパティを持つ要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-365">The following example shows an **EntityType** element with two properties:</span></span>

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

## <a name="function-element-ssdl"></a><span data-ttu-id="3a946-366">Function 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-366">Function Element (SSDL)</span></span>

<span data-ttu-id="3a946-367">**関数**ストア スキーマ定義言語 (SSDL) 内の要素が基になるデータベースに存在するストアド プロシージャを指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-367">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="3a946-368">**関数**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-368">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-369">(0 個または 1 つ) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3a946-369">Documentation (zero or one)</span></span>
-   <span data-ttu-id="3a946-370">パラメーター (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a946-370">Parameter (zero or more)</span></span>
-   <span data-ttu-id="3a946-371">CommandText (0 または 1)</span><span class="sxs-lookup"><span data-stu-id="3a946-371">CommandText (zero or one)</span></span>
-   <span data-ttu-id="3a946-372">ReturnType (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a946-372">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="3a946-373">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a946-373">Annotation elements (zero or more)</span></span>

<span data-ttu-id="3a946-374">いずれかの関数の型を指定する戻り、 **ReturnType**要素、または**ReturnType**両方ではなく属性 (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="3a946-374">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="3a946-375">ストレージ モデル内で指定されるストアド プロシージャは、アプリケーションの概念モデルにインポートできます。</span><span class="sxs-lookup"><span data-stu-id="3a946-375">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="3a946-376">詳細については、次を参照してください。[ストアド プロシージャを使用したクエリ](~/ef6/modeling/designer/stored-procedures/query.md)します。</span><span class="sxs-lookup"><span data-stu-id="3a946-376">For more information, see [Querying with Stored Procedures](~/ef6/modeling/designer/stored-procedures/query.md).</span></span> <span data-ttu-id="3a946-377">**関数**要素が、ストレージ モデルでカスタム関数を定義することもできます。</span><span class="sxs-lookup"><span data-stu-id="3a946-377">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-378">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-378">Applicable Attributes</span></span>

<span data-ttu-id="3a946-379">次の表に適用できる属性、**関数**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-379">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="3a946-380">(ここでは示しません) いくつかの属性で修飾できます、**格納**エイリアス。</span><span class="sxs-lookup"><span data-stu-id="3a946-380">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="3a946-381">これらの属性は、モデルを更新するときに、モデルの更新ウィザードによって使用されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-381">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="3a946-382">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-382">Attribute Name</span></span>             | <span data-ttu-id="3a946-383">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-383">Is Required</span></span> | <span data-ttu-id="3a946-384">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-384">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-385">**Name**</span><span class="sxs-lookup"><span data-stu-id="3a946-385">**Name**</span></span>                   | <span data-ttu-id="3a946-386">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-386">Yes</span></span>         | <span data-ttu-id="3a946-387">ストアド プロシージャの名前。</span><span class="sxs-lookup"><span data-stu-id="3a946-387">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="3a946-388">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="3a946-388">**ReturnType**</span></span>             | <span data-ttu-id="3a946-389">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-389">No</span></span>          | <span data-ttu-id="3a946-390">ストアド プロシージャの戻り値の型。</span><span class="sxs-lookup"><span data-stu-id="3a946-390">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="3a946-391">**Aggregate**</span><span class="sxs-lookup"><span data-stu-id="3a946-391">**Aggregate**</span></span>              | <span data-ttu-id="3a946-392">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-392">No</span></span>          | <span data-ttu-id="3a946-393">**True**ストアド プロシージャは、集計値を返す場合それ以外の場合**False**します。</span><span class="sxs-lookup"><span data-stu-id="3a946-393">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="3a946-394">**組み込み**</span><span class="sxs-lookup"><span data-stu-id="3a946-394">**BuiltIn**</span></span>                | <span data-ttu-id="3a946-395">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-395">No</span></span>          | <span data-ttu-id="3a946-396">**True**関数が組み込み場合<sup>1</sup>関数です。 それ以外の場合**False**します。</span><span class="sxs-lookup"><span data-stu-id="3a946-396">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="3a946-397">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="3a946-397">**StoreFunctionName**</span></span>      | <span data-ttu-id="3a946-398">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-398">No</span></span>          | <span data-ttu-id="3a946-399">ストアド プロシージャの名前。</span><span class="sxs-lookup"><span data-stu-id="3a946-399">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="3a946-400">**NiladicFunction**</span><span class="sxs-lookup"><span data-stu-id="3a946-400">**NiladicFunction**</span></span>        | <span data-ttu-id="3a946-401">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-401">No</span></span>          | <span data-ttu-id="3a946-402">**True**関数がニラディック場合<sup>2</sup>関数。**False**それ以外の場合。</span><span class="sxs-lookup"><span data-stu-id="3a946-402">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="3a946-403">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="3a946-403">**IsComposable**</span></span>           | <span data-ttu-id="3a946-404">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-404">No</span></span>          | <span data-ttu-id="3a946-405">**True**関数がコンポーザブルの場合<sup>3</sup>関数。**False**それ以外の場合。</span><span class="sxs-lookup"><span data-stu-id="3a946-405">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="3a946-406">**ParameterTypeSemantics**</span><span class="sxs-lookup"><span data-stu-id="3a946-406">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="3a946-407">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-407">No</span></span>          | <span data-ttu-id="3a946-408">関数オーバーロードの解決に使用される型のセマンティクスを定義する列挙。</span><span class="sxs-lookup"><span data-stu-id="3a946-408">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="3a946-409">列挙はプロバイダー マニフェストで関数定義によって定義されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-409">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="3a946-410">既定値は**AllowImplicitConversion**します。</span><span class="sxs-lookup"><span data-stu-id="3a946-410">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="3a946-411">**[スキーマ]**</span><span class="sxs-lookup"><span data-stu-id="3a946-411">**Schema**</span></span>                 | <span data-ttu-id="3a946-412">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-412">No</span></span>          | <span data-ttu-id="3a946-413">ストアド プロシージャが定義されているスキーマの名前。</span><span class="sxs-lookup"><span data-stu-id="3a946-413">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="3a946-414"><sup>1</sup>組み込み関数は、データベースで定義されている関数。</span><span class="sxs-lookup"><span data-stu-id="3a946-414"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="3a946-415">ストレージ モデルで定義されている関数については、CommandText 要素 (SSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a946-415">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="3a946-416"><sup>2</sup>ニラディック関数が関数パラメーターを受け取らず、呼び出されると、かっこは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="3a946-416"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="3a946-417"><sup>3</sup> 2 つの関数がコンポーザブルの場合は 1 つの関数の出力は、他の関数の入力を指定できます。</span><span class="sxs-lookup"><span data-stu-id="3a946-417"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="3a946-418">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**関数**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-418">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="3a946-419">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-419">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="3a946-420">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-420">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-421">例</span><span class="sxs-lookup"><span data-stu-id="3a946-421">Example</span></span>

<span data-ttu-id="3a946-422">次の例は、**関数**に対応する要素、 **UpdateOrderQuantity**ストアド プロシージャ。</span><span class="sxs-lookup"><span data-stu-id="3a946-422">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="3a946-423">このストアド プロシージャは 2 個のパラメーターを受け入れ、値を返しません。</span><span class="sxs-lookup"><span data-stu-id="3a946-423">The stored procedure accepts two parameters and does not return a value.</span></span>

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

## <a name="key-element-ssdl"></a><span data-ttu-id="3a946-424">key 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-424">Key Element (SSDL)</span></span>

<span data-ttu-id="3a946-425">**キー**ストア スキーマ定義言語 (SSDL) 内の要素は、基になるデータベース内のテーブルの主キーを表します。</span><span class="sxs-lookup"><span data-stu-id="3a946-425">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="3a946-426">**キー**テーブルの行を表す EntityType 要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="3a946-426">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="3a946-427">主キーが定義されている、**キー**要素で定義されている 1 つまたは複数のプロパティ要素を参照することによって、 **EntityType**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-427">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="3a946-428">**キー**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-428">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-429">PropertyRef (1 つまたは複数)</span><span class="sxs-lookup"><span data-stu-id="3a946-429">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="3a946-430">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="3a946-430">Annotation elements</span></span>

<span data-ttu-id="3a946-431">適用可能な属性がない、**キー**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-431">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-432">例</span><span class="sxs-lookup"><span data-stu-id="3a946-432">Example</span></span>

<span data-ttu-id="3a946-433">次の例は、 **EntityType** 1 つのプロパティを参照するキーを持つ要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-433">The following example shows an **EntityType** element with a key that references one property:</span></span>

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

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="3a946-434">OnDelete 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-434">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="3a946-435">**OnDelete**ストア スキーマ定義言語 (SSDL) 内の要素では、外部キー制約に参加している行が削除された場合、データベースの動作が反映されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-435">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="3a946-436">アクションに設定されている場合**Cascade**、削除される行を参照する行も削除されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-436">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="3a946-437">アクションに設定されている場合**None**、削除される行を参照する行も削除されません。</span><span class="sxs-lookup"><span data-stu-id="3a946-437">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="3a946-438">**OnDelete**要素は、最後の要素の子要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-438">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="3a946-439">**OnDelete**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-439">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-440">(0 個または 1 つ) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3a946-440">Documentation (zero or one)</span></span>
-   <span data-ttu-id="3a946-441">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a946-441">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-442">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-442">Applicable Attributes</span></span>

<span data-ttu-id="3a946-443">次の表に適用できる属性、 **OnDelete**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-443">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="3a946-444">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-444">Attribute Name</span></span> | <span data-ttu-id="3a946-445">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-445">Is Required</span></span> | <span data-ttu-id="3a946-446">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-446">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-447">**動作**</span><span class="sxs-lookup"><span data-stu-id="3a946-447">**Action**</span></span>     | <span data-ttu-id="3a946-448">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-448">Yes</span></span>         | <span data-ttu-id="3a946-449">**Cascade**または**None**します。</span><span class="sxs-lookup"><span data-stu-id="3a946-449">**Cascade** or **None**.</span></span> <span data-ttu-id="3a946-450">(値**Restricted**は有効ですと同じ動作が**None**)。</span><span class="sxs-lookup"><span data-stu-id="3a946-450">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="3a946-451">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **OnDelete**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-451">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="3a946-452">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-452">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="3a946-453">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-453">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-454">例</span><span class="sxs-lookup"><span data-stu-id="3a946-454">Example</span></span>

<span data-ttu-id="3a946-455">次の例は、**アソシエーション**を定義する要素、 **FK\_CustomerOrders**外部キー制約。</span><span class="sxs-lookup"><span data-stu-id="3a946-455">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="3a946-456">**OnDelete**要素は、のすべての行を示します、**注文**の特定の行を参照するテーブル、**顧客**場合は、テーブルを削除するが、内の行**顧客**テーブルを削除します。</span><span class="sxs-lookup"><span data-stu-id="3a946-456">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

## <a name="parameter-element-ssdl"></a><span data-ttu-id="3a946-457">Parameter 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-457">Parameter Element (SSDL)</span></span>

<span data-ttu-id="3a946-458">**パラメーター**ストア スキーマ定義言語 (SSDL) 内の要素は、データベースでストアド プロシージャのパラメーターを指定する Function 要素の子。</span><span class="sxs-lookup"><span data-stu-id="3a946-458">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="3a946-459">**パラメーター**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-459">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-460">(0 個または 1 つ) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3a946-460">Documentation (zero or one)</span></span>
-   <span data-ttu-id="3a946-461">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a946-461">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-462">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-462">Applicable Attributes</span></span>

<span data-ttu-id="3a946-463">次の表に適用できる属性の説明、**パラメーター**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-463">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="3a946-464">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-464">Attribute Name</span></span> | <span data-ttu-id="3a946-465">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-465">Is Required</span></span> | <span data-ttu-id="3a946-466">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-466">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-467">**Name**</span><span class="sxs-lookup"><span data-stu-id="3a946-467">**Name**</span></span>       | <span data-ttu-id="3a946-468">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-468">Yes</span></span>         | <span data-ttu-id="3a946-469">パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="3a946-469">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="3a946-470">**Type**</span><span class="sxs-lookup"><span data-stu-id="3a946-470">**Type**</span></span>       | <span data-ttu-id="3a946-471">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-471">Yes</span></span>         | <span data-ttu-id="3a946-472">パラメーターの型。</span><span class="sxs-lookup"><span data-stu-id="3a946-472">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="3a946-473">**モード**</span><span class="sxs-lookup"><span data-stu-id="3a946-473">**Mode**</span></span>       | <span data-ttu-id="3a946-474">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-474">No</span></span>          | <span data-ttu-id="3a946-475">**In**、**アウト**、または**InOut**パラメーターは、入力、出力、または入力/出力パラメーターかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="3a946-475">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="3a946-476">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="3a946-476">**MaxLength**</span></span>  | <span data-ttu-id="3a946-477">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-477">No</span></span>          | <span data-ttu-id="3a946-478">パラメーターの最大長。</span><span class="sxs-lookup"><span data-stu-id="3a946-478">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="3a946-479">**精度**</span><span class="sxs-lookup"><span data-stu-id="3a946-479">**Precision**</span></span>  | <span data-ttu-id="3a946-480">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-480">No</span></span>          | <span data-ttu-id="3a946-481">パラメーターの有効桁数。</span><span class="sxs-lookup"><span data-stu-id="3a946-481">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="3a946-482">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="3a946-482">**Scale**</span></span>      | <span data-ttu-id="3a946-483">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-483">No</span></span>          | <span data-ttu-id="3a946-484">パラメーターの小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="3a946-484">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="3a946-485">**SRID**</span><span class="sxs-lookup"><span data-stu-id="3a946-485">**SRID**</span></span>       | <span data-ttu-id="3a946-486">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-486">No</span></span>          | <span data-ttu-id="3a946-487">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="3a946-487">Spatial System Reference Identifier.</span></span> <span data-ttu-id="3a946-488">空間型のパラメーターに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="3a946-488">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="3a946-489">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="3a946-489">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="3a946-490">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**パラメーター**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-490">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="3a946-491">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-491">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="3a946-492">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-492">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-493">例</span><span class="sxs-lookup"><span data-stu-id="3a946-493">Example</span></span>

<span data-ttu-id="3a946-494">次の例は、**関数**2 つ含まれる要素**パラメーター**入力パラメーターを指定する要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-494">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

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

## <a name="principal-element-ssdl"></a><span data-ttu-id="3a946-495">Principal 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-495">Principal Element (SSDL)</span></span>

<span data-ttu-id="3a946-496">**プリンシパル**ストア スキーマ定義言語 (SSDL) 内の要素は、外部キー制約 (参照に関する制約とも呼ばれます) のプリンシパル end を定義する ReferentialConstraint 要素に子要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-496">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="3a946-497">**プリンシパル**要素が別の列 (または列) によって参照されるテーブルの主キー列 (または列) を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-497">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="3a946-498">**PropertyRef**のどの列が参照されている要素を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-498">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="3a946-499">Dependent 要素で指定されている主キー列を参照する列を指定します、**プリンシパル**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-499">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="3a946-500">**プリンシパル**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-500">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="3a946-501">PropertyRef (1 つまたは複数)</span><span class="sxs-lookup"><span data-stu-id="3a946-501">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="3a946-502">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a946-502">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-503">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-503">Applicable Attributes</span></span>

<span data-ttu-id="3a946-504">次の表に適用できる属性、**プリンシパル**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-504">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="3a946-505">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-505">Attribute Name</span></span> | <span data-ttu-id="3a946-506">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-506">Is Required</span></span> | <span data-ttu-id="3a946-507">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-507">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-508">**ロール**</span><span class="sxs-lookup"><span data-stu-id="3a946-508">**Role**</span></span>       | <span data-ttu-id="3a946-509">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-509">Yes</span></span>         | <span data-ttu-id="3a946-510">同じ値、**ロール**(使用) する場合の対応する終了要素の属性。 それ以外の場合、テーブルの名前を格納した参照先の列。</span><span class="sxs-lookup"><span data-stu-id="3a946-510">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="3a946-511">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プリンシパル**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-511">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="3a946-512">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-512">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="3a946-513">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-513">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-514">例</span><span class="sxs-lookup"><span data-stu-id="3a946-514">Example</span></span>

<span data-ttu-id="3a946-515">次の例を使用する Association 要素を示しています、 **ReferentialConstraint**に参加する列を指定する要素、 **FK\_CustomerOrders**外部キー制約です。</span><span class="sxs-lookup"><span data-stu-id="3a946-515">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="3a946-516">**プリンシパル**要素を指定します、 **CustomerId**の列、**顧客**テーブル制約のプリンシパル end として。</span><span class="sxs-lookup"><span data-stu-id="3a946-516">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

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

## <a name="property-element-ssdl"></a><span data-ttu-id="3a946-517">Property 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-517">Property Element (SSDL)</span></span>

<span data-ttu-id="3a946-518">**プロパティ**ストア スキーマ定義言語 (SSDL) 内の要素は、基になるデータベース内のテーブルの列を表します。</span><span class="sxs-lookup"><span data-stu-id="3a946-518">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="3a946-519">**プロパティ**テーブル内の行を表す EntityType 要素の子である要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-519">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="3a946-520">各**プロパティ**要素で定義されている、 **EntityType**要素は、列を表します。</span><span class="sxs-lookup"><span data-stu-id="3a946-520">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="3a946-521">A**プロパティ**要素は、すべての子要素を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="3a946-521">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-522">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-522">Applicable Attributes</span></span>

<span data-ttu-id="3a946-523">次の表に適用できる属性、**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-523">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="3a946-524">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-524">Attribute Name</span></span>            | <span data-ttu-id="3a946-525">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-525">Is Required</span></span> | <span data-ttu-id="3a946-526">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-526">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-527">**Name**</span><span class="sxs-lookup"><span data-stu-id="3a946-527">**Name**</span></span>                  | <span data-ttu-id="3a946-528">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-528">Yes</span></span>         | <span data-ttu-id="3a946-529">対応する列の名前。</span><span class="sxs-lookup"><span data-stu-id="3a946-529">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="3a946-530">**Type**</span><span class="sxs-lookup"><span data-stu-id="3a946-530">**Type**</span></span>                  | <span data-ttu-id="3a946-531">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-531">Yes</span></span>         | <span data-ttu-id="3a946-532">対応する列の種類。</span><span class="sxs-lookup"><span data-stu-id="3a946-532">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="3a946-533">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="3a946-533">**Nullable**</span></span>              | <span data-ttu-id="3a946-534">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-534">No</span></span>          | <span data-ttu-id="3a946-535">**True** (既定値) または**False**対応する列が null 値を持っているかどうかによって。</span><span class="sxs-lookup"><span data-stu-id="3a946-535">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="3a946-536">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="3a946-536">**DefaultValue**</span></span>          | <span data-ttu-id="3a946-537">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-537">No</span></span>          | <span data-ttu-id="3a946-538">対応する列の既定値。</span><span class="sxs-lookup"><span data-stu-id="3a946-538">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="3a946-539">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="3a946-539">**MaxLength**</span></span>             | <span data-ttu-id="3a946-540">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-540">No</span></span>          | <span data-ttu-id="3a946-541">対応する列の最大長。</span><span class="sxs-lookup"><span data-stu-id="3a946-541">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="3a946-542">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="3a946-542">**FixedLength**</span></span>           | <span data-ttu-id="3a946-543">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-543">No</span></span>          | <span data-ttu-id="3a946-544">**True**または**False**固定長の文字列として、対応する列の値を格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="3a946-544">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="3a946-545">**精度**</span><span class="sxs-lookup"><span data-stu-id="3a946-545">**Precision**</span></span>             | <span data-ttu-id="3a946-546">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-546">No</span></span>          | <span data-ttu-id="3a946-547">対応する列の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="3a946-547">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="3a946-548">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="3a946-548">**Scale**</span></span>                 | <span data-ttu-id="3a946-549">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-549">No</span></span>          | <span data-ttu-id="3a946-550">対応する列の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="3a946-550">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="3a946-551">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="3a946-551">**Unicode**</span></span>               | <span data-ttu-id="3a946-552">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-552">No</span></span>          | <span data-ttu-id="3a946-553">**True**または**False** Unicode 文字列として、対応する列の値を格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="3a946-553">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="3a946-554">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="3a946-554">**Collation**</span></span>             | <span data-ttu-id="3a946-555">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-555">No</span></span>          | <span data-ttu-id="3a946-556">データ ソースで使用する照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="3a946-556">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="3a946-557">**SRID**</span><span class="sxs-lookup"><span data-stu-id="3a946-557">**SRID**</span></span>                  | <span data-ttu-id="3a946-558">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-558">No</span></span>          | <span data-ttu-id="3a946-559">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="3a946-559">Spatial System Reference Identifier.</span></span> <span data-ttu-id="3a946-560">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="3a946-560">Valid only for properties of spatial types.</span></span> <span data-ttu-id="3a946-561">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="3a946-561">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="3a946-562">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="3a946-562">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="3a946-563">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-563">No</span></span>          | <span data-ttu-id="3a946-564">**None**、 **Identity** (対応する列の値は、データベースで生成される id)、または**計算済み**(かどうか、対応する列の値は、データベースで計算)。</span><span class="sxs-lookup"><span data-stu-id="3a946-564">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="3a946-565">RowType プロパティのない有効期間は。</span><span class="sxs-lookup"><span data-stu-id="3a946-565">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="3a946-566">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-566">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="3a946-567">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-567">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="3a946-568">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-568">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-569">例</span><span class="sxs-lookup"><span data-stu-id="3a946-569">Example</span></span>

<span data-ttu-id="3a946-570">次の例は、 **EntityType** 2 つの子を持つ要素**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-570">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

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

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="3a946-571">PropertyRef 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-571">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="3a946-572">**PropertyRef**ストア スキーマ定義言語 (SSDL) 内の要素は、プロパティは実行は、次のロールのいずれかを示す EntityType 要素で定義されたプロパティを参照します。</span><span class="sxs-lookup"><span data-stu-id="3a946-572">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="3a946-573">テーブルの主キーの一部を**EntityType**を表します。</span><span class="sxs-lookup"><span data-stu-id="3a946-573">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="3a946-574">1 つまたは複数**PropertyRef**プライマリ キーを定義する要素を使用できます。</span><span class="sxs-lookup"><span data-stu-id="3a946-574">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="3a946-575">詳細については、キー要素を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a946-575">For more information, see Key element.</span></span>
-   <span data-ttu-id="3a946-576">参照に関する制約の依存 End またはプリンシパル End になる。</span><span class="sxs-lookup"><span data-stu-id="3a946-576">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="3a946-577">詳細については、ReferentialConstraint 要素を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a946-577">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="3a946-578">**PropertyRef**要素は、次の子要素を持つことができますのみ。</span><span class="sxs-lookup"><span data-stu-id="3a946-578">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="3a946-579">(0 個または 1 つ) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3a946-579">Documentation (zero or one)</span></span>
-   <span data-ttu-id="3a946-580">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="3a946-580">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-581">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-581">Applicable Attributes</span></span>

<span data-ttu-id="3a946-582">次の表に適用できる属性の説明、 **PropertyRef**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-582">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="3a946-583">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-583">Attribute Name</span></span> | <span data-ttu-id="3a946-584">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-584">Is Required</span></span> | <span data-ttu-id="3a946-585">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-585">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="3a946-586">**Name**</span><span class="sxs-lookup"><span data-stu-id="3a946-586">**Name**</span></span>       | <span data-ttu-id="3a946-587">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-587">Yes</span></span>         | <span data-ttu-id="3a946-588">参照されているプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="3a946-588">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="3a946-589">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **PropertyRef**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-589">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="3a946-590">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-590">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="3a946-591">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-591">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-592">例</span><span class="sxs-lookup"><span data-stu-id="3a946-592">Example</span></span>

<span data-ttu-id="3a946-593">次の例は、 **PropertyRef**要素で定義されているプロパティを参照することによって主キーを定義するために使用する**EntityType**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-593">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

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

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="3a946-594">ReferentialConstraint 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-594">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="3a946-595">**ReferentialConstraint**ストア スキーマ定義言語 (SSDL) 内の要素は、基になるデータベースで外部キー制約 (参照整合性制約とも呼ばれます) を表します。</span><span class="sxs-lookup"><span data-stu-id="3a946-595">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="3a946-596">制約のプリンシパルと依存 end がそれぞれのプリンシパルと依存する子要素によって指定されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-596">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="3a946-597">PropertyRef 要素を持つ、プリンシパル end と依存 end に参加する列が参照されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-597">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="3a946-598">**ReferentialConstraint**要素は Association 要素の省略可能な子要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-598">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="3a946-599">場合、 **ReferentialConstraint**要素は、マップで指定されている外部キー制約には使用されず、**アソシエーション**要素、AssociationSetMapping 要素は、これを行うために使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-599">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="3a946-600">**ReferentialConstraint**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-600">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="3a946-601">(0 個または 1 つ) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="3a946-601">Documentation (zero or one)</span></span>
-   <span data-ttu-id="3a946-602">プリンシパル (正確には 1 つ)</span><span class="sxs-lookup"><span data-stu-id="3a946-602">Principal (exactly one)</span></span>
-   <span data-ttu-id="3a946-603">依存 (正確には 1 つ)</span><span class="sxs-lookup"><span data-stu-id="3a946-603">Dependent (exactly one)</span></span>
-   <span data-ttu-id="3a946-604">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="3a946-604">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-605">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-605">Applicable Attributes</span></span>

<span data-ttu-id="3a946-606">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReferentialConstraint**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-606">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="3a946-607">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-607">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="3a946-608">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-608">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-609">例</span><span class="sxs-lookup"><span data-stu-id="3a946-609">Example</span></span>

<span data-ttu-id="3a946-610">次の例は、**アソシエーション**を使用する要素を**ReferentialConstraint**に参加する列を指定する要素、 **FK\_CustomerOrders**外部キー制約。</span><span class="sxs-lookup"><span data-stu-id="3a946-610">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="returntype-element-ssdl"></a><span data-ttu-id="3a946-611">ReturnType 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-611">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="3a946-612">**ReturnType**ストア スキーマ定義言語 (SSDL) 内の要素で定義されている関数の戻り値の型を指定します、**関数**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-612">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="3a946-613">関数の戻り値の型を指定することも、 **ReturnType**属性。</span><span class="sxs-lookup"><span data-stu-id="3a946-613">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="3a946-614">関数の戻り値の型を指定した、**型**属性または**ReturnType**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-614">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="3a946-615">**ReturnType**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-615">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="3a946-616">CollectionType (1 つ)</span><span class="sxs-lookup"><span data-stu-id="3a946-616">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="3a946-617">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReturnType**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-617">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="3a946-618">ただし、カスタム属性は SSDL 用に予約されたどの XML 名前空間にも属さない場合があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-618">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="3a946-619">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-620">例</span><span class="sxs-lookup"><span data-stu-id="3a946-620">Example</span></span>

<span data-ttu-id="3a946-621">次の例では、**関数**行のコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="3a946-621">The following example uses a **Function** that returns a collection of rows.</span></span>

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


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="3a946-622">RowType 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-622">RowType Element (SSDL)</span></span>

<span data-ttu-id="3a946-623">A **RowType**ストア スキーマ定義言語 (SSDL) 要素は、戻り値として、名前のない構造体を定義します。 ストアで定義されている関数の型。</span><span class="sxs-lookup"><span data-stu-id="3a946-623">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="3a946-624">A **RowType**要素の子要素は、 **CollectionType**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-624">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="3a946-625">A **RowType**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-625">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="3a946-626">(1 つまたは複数) のプロパティ</span><span class="sxs-lookup"><span data-stu-id="3a946-626">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="3a946-627">例</span><span class="sxs-lookup"><span data-stu-id="3a946-627">Example</span></span>

<span data-ttu-id="3a946-628">次の例を使用するストア関数を示しています、 **CollectionType**関数によって返される行のコレクションを指定する要素 (で指定されている、 **RowType**要素)。</span><span class="sxs-lookup"><span data-stu-id="3a946-628">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


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

## <a name="schema-element-ssdl"></a><span data-ttu-id="3a946-629">Schema 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-629">Schema Element (SSDL)</span></span>

<span data-ttu-id="3a946-630">**スキーマ**ストア スキーマ定義言語 (SSDL) での要素はストレージ モデルの定義のルート要素です。</span><span class="sxs-lookup"><span data-stu-id="3a946-630">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="3a946-631">ストレージ モデルを構成するオブジェクト、関数、およびコンテナーの定義を格納します。</span><span class="sxs-lookup"><span data-stu-id="3a946-631">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="3a946-632">**スキーマ**要素は、次の子要素の 0 個以上を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-632">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="3a946-633">関連付け</span><span class="sxs-lookup"><span data-stu-id="3a946-633">Association</span></span>
-   <span data-ttu-id="3a946-634">EntityType</span><span class="sxs-lookup"><span data-stu-id="3a946-634">EntityType</span></span>
-   <span data-ttu-id="3a946-635">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="3a946-635">EntityContainer</span></span>
-   <span data-ttu-id="3a946-636">関数</span><span class="sxs-lookup"><span data-stu-id="3a946-636">Function</span></span>

<span data-ttu-id="3a946-637">**スキーマ**要素を使用して、 **Namespace**ストレージ モデルのエンティティ型とアソシエーション オブジェクトの名前空間を定義する属性。</span><span class="sxs-lookup"><span data-stu-id="3a946-637">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="3a946-638">1 つの名前空間内で 2 つのオブジェクトが同じ名前を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="3a946-638">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="3a946-639">ストレージ モデル名前空間は、別の XML 名前空間から、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-639">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="3a946-640">ストレージ モデル名前空間 (で定義されている、 **Namespace**属性) はエンティティ型とアソシエーション型の論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="3a946-640">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="3a946-641">XML 名前空間 (によって示される、 **xmlns**属性) の**スキーマ**要素が子要素と属性の既定の名前空間、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-641">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="3a946-642">フォームの XML 名前空間 http://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl SSDL 用に予約されています (YYYY と MM 表します年と月それぞれ)。</span><span class="sxs-lookup"><span data-stu-id="3a946-642">XML namespaces of the form http://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="3a946-643">カスタム要素と属性は、このフォームがある名前空間に存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="3a946-643">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="3a946-644">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="3a946-644">Applicable Attributes</span></span>

<span data-ttu-id="3a946-645">次の表の説明属性に適用できる、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-645">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="3a946-646">属性名</span><span class="sxs-lookup"><span data-stu-id="3a946-646">Attribute Name</span></span>            | <span data-ttu-id="3a946-647">必須</span><span class="sxs-lookup"><span data-stu-id="3a946-647">Is Required</span></span> | <span data-ttu-id="3a946-648">[値]</span><span class="sxs-lookup"><span data-stu-id="3a946-648">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-649">**名前空間**</span><span class="sxs-lookup"><span data-stu-id="3a946-649">**Namespace**</span></span>             | <span data-ttu-id="3a946-650">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-650">Yes</span></span>         | <span data-ttu-id="3a946-651">ストレージ モデルの名前空間。</span><span class="sxs-lookup"><span data-stu-id="3a946-651">The namespace of the storage model.</span></span> <span data-ttu-id="3a946-652">値、 **Namespace**属性が型の完全修飾名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-652">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="3a946-653">たとえば場合、 **EntityType**という名前の*顧客*ExampleModel.Store 名前空間の完全修飾名では、 **EntityType**はExamplemodel.store.customer です。</span><span class="sxs-lookup"><span data-stu-id="3a946-653">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="3a946-654">値として、次の文字列を使用することはできません、 **Namespace**属性:**システム**、**一時的な**、または**Edm**します。</span><span class="sxs-lookup"><span data-stu-id="3a946-654">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="3a946-655">値、 **Namespace**属性の値と同じにすることはできません、 **Namespace** CSDL スキーマ要素の属性。</span><span class="sxs-lookup"><span data-stu-id="3a946-655">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="3a946-656">**Alias**</span><span class="sxs-lookup"><span data-stu-id="3a946-656">**Alias**</span></span>                 | <span data-ttu-id="3a946-657">いいえ</span><span class="sxs-lookup"><span data-stu-id="3a946-657">No</span></span>          | <span data-ttu-id="3a946-658">名前空間の名前の代わりに使用される識別子。</span><span class="sxs-lookup"><span data-stu-id="3a946-658">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="3a946-659">たとえば場合、 **EntityType**という名前の*顧客*ExampleModel.Store 名前空間にありの値は、**エイリアス**属性が*StorageModel*、StorageModel.Customer の完全修飾名として使用することができます、 **EntityType。**</span><span class="sxs-lookup"><span data-stu-id="3a946-659">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="3a946-660">**Provider**</span><span class="sxs-lookup"><span data-stu-id="3a946-660">**Provider**</span></span>              | <span data-ttu-id="3a946-661">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-661">Yes</span></span>         | <span data-ttu-id="3a946-662">データ プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="3a946-662">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="3a946-663">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="3a946-663">**ProviderManifestToken**</span></span> | <span data-ttu-id="3a946-664">[はい]</span><span class="sxs-lookup"><span data-stu-id="3a946-664">Yes</span></span>         | <span data-ttu-id="3a946-665">返すプロバイダー マニフェストをプロバイダーに示すトークン。</span><span class="sxs-lookup"><span data-stu-id="3a946-665">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="3a946-666">トークンの形式は定義されていません。</span><span class="sxs-lookup"><span data-stu-id="3a946-666">No format for the token is defined.</span></span> <span data-ttu-id="3a946-667">トークンの値は、プロバイダーで定義されています。</span><span class="sxs-lookup"><span data-stu-id="3a946-667">Values for the token are defined by the provider.</span></span> <span data-ttu-id="3a946-668">SQL Server プロバイダー マニフェスト トークンについては、Entity Framework 用 SqlClient を参照してください。</span><span class="sxs-lookup"><span data-stu-id="3a946-668">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="3a946-669">例</span><span class="sxs-lookup"><span data-stu-id="3a946-669">Example</span></span>

<span data-ttu-id="3a946-670">次の例は、**スキーマ**要素を含む、 **EntityContainer**要素、2 つ**EntityType**要素、および 1 つ**の関連付け**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-670">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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

## <a name="annotation-attributes"></a><span data-ttu-id="3a946-671">annotation 属性</span><span class="sxs-lookup"><span data-stu-id="3a946-671">Annotation Attributes</span></span>

<span data-ttu-id="3a946-672">ストア スキーマ定義言語 (SSDL) の annotation 属性は、ストレージ モデルの要素に関する追加のメタデータを指定するストレージ モデルのカスタム XML 属性です。</span><span class="sxs-lookup"><span data-stu-id="3a946-672">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="3a946-673">有効な XML 構造が必要であることに加え、annotation 属性には次の制約が適用されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-673">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="3a946-674">annotation 属性は、SSDL 用に予約された XML 名前空間に存在しない。</span><span class="sxs-lookup"><span data-stu-id="3a946-674">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="3a946-675">2 つの任意の annotation 属性の完全修飾名が同じではない。</span><span class="sxs-lookup"><span data-stu-id="3a946-675">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="3a946-676">複数の annotation 属性を特定の 1 つの SSDL 要素に適用することができる。</span><span class="sxs-lookup"><span data-stu-id="3a946-676">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="3a946-677">Annotation 要素に含まれるメタデータは、System.Data.Metadata.Edm 名前空間のクラスを使用して、実行時にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="3a946-677">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-678">例</span><span class="sxs-lookup"><span data-stu-id="3a946-678">Example</span></span>

<span data-ttu-id="3a946-679">次の例では、EntityType 要素に適用される annotation 属性を持つ、 **OrderId**プロパティ。</span><span class="sxs-lookup"><span data-stu-id="3a946-679">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="3a946-680">追加された注釈要素も示して、 **EntityType**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-680">The example also show an annotation element added to the **EntityType** element.</span></span>

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

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="3a946-681">annotation 要素 (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-681">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="3a946-682">ストア スキーマ定義言語 (SSDL) の annotation 要素は、ストレージ モデルに関する追加のメタデータを指定するストレージ モデルのカスタム XML 要素です。</span><span class="sxs-lookup"><span data-stu-id="3a946-682">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="3a946-683">有効な XML 構造が必要であることに加え、annotation 要素には次の制約が適用されます。</span><span class="sxs-lookup"><span data-stu-id="3a946-683">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="3a946-684">annotation 要素は、SSDL 用に予約された XML 名前空間内に存在できません。</span><span class="sxs-lookup"><span data-stu-id="3a946-684">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="3a946-685">2 つの annotation 要素の完全修飾名を同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="3a946-685">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="3a946-686">annotation 要素は、特定の SSDL 要素のその他すべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3a946-686">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="3a946-687">複数の annotation 要素を特定の SSDL 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="3a946-687">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="3a946-688">以降、.NET Framework version 4 では、annotation 要素に含まれるメタデータにアクセスできるランタイム System.Data.Metadata.Edm 名前空間のクラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="3a946-688">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="3a946-689">例</span><span class="sxs-lookup"><span data-stu-id="3a946-689">Example</span></span>

<span data-ttu-id="3a946-690">次の例は、annotation 要素を持つ EntityType 要素 (**CustomElement**)。</span><span class="sxs-lookup"><span data-stu-id="3a946-690">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="3a946-691">適用される annotation 属性も示します、 **OrderId**プロパティ。</span><span class="sxs-lookup"><span data-stu-id="3a946-691">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

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

## <a name="facets-ssdl"></a><span data-ttu-id="3a946-692">ファセット (SSDL)</span><span class="sxs-lookup"><span data-stu-id="3a946-692">Facets (SSDL)</span></span>

<span data-ttu-id="3a946-693">ストア スキーマ定義言語 (SSDL) でのファセットは、プロパティ要素で指定されている列の型に対する制約を表します。</span><span class="sxs-lookup"><span data-stu-id="3a946-693">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="3a946-694">ファセットは、上の XML 属性として実装されます**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="3a946-694">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="3a946-695">次の表では、SSDL でサポートされるファセットについて説明します。</span><span class="sxs-lookup"><span data-stu-id="3a946-695">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="3a946-696">ファセット</span><span class="sxs-lookup"><span data-stu-id="3a946-696">Facet</span></span>           | <span data-ttu-id="3a946-697">説明</span><span class="sxs-lookup"><span data-stu-id="3a946-697">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="3a946-698">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="3a946-698">**Collation**</span></span>   | <span data-ttu-id="3a946-699">プロパティの値に対して比較と順序付け操作を行うときに使用する照合シーケンス (または並べ替え順序) を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-699">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="3a946-700">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="3a946-700">**FixedLength**</span></span> | <span data-ttu-id="3a946-701">列値の長さを可変とすることができるかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-701">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="3a946-702">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="3a946-702">**MaxLength**</span></span>   | <span data-ttu-id="3a946-703">列値の最大長を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-703">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="3a946-704">**精度**</span><span class="sxs-lookup"><span data-stu-id="3a946-704">**Precision**</span></span>   | <span data-ttu-id="3a946-705">型のプロパティの**Decimal**桁に指定できるプロパティの値の数を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-705">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="3a946-706">型のプロパティの**時間**、 **DateTime**、および**DateTimeOffset**列の値の秒の小数部の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-706">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="3a946-707">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="3a946-707">**Scale**</span></span>       | <span data-ttu-id="3a946-708">列値の小数点の右側の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-708">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="3a946-709">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="3a946-709">**Unicode**</span></span>     | <span data-ttu-id="3a946-710">列値を Unicode として保存するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="3a946-710">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
