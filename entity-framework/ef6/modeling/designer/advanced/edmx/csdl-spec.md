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
# <a name="csdl-specification"></a><span data-ttu-id="ba61d-102">CSDL 仕様</span><span class="sxs-lookup"><span data-stu-id="ba61d-102">CSDL Specification</span></span>
<span data-ttu-id="ba61d-103">概念スキーマ定義言語 (CSDL : Conceptual Schema Definition Language) は、XML ベースの言語であり、データ駆動型アプリケーションの概念モデルを構成するエンティティ、リレーションシップ、および関数を記述します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-103">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="ba61d-104">この概念モデルは、Entity Framework または WCF Data Services で使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-104">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="ba61d-105">CSDL で記述されたメタデータは、概念モデルで定義されているエンティティとリレーションシップをデータソースにマップするために、Entity Framework によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-105">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="ba61d-106">詳細については、「 [SSDL 仕様](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)と[MSL 仕様](~/ef6/modeling/designer/advanced/edmx/msl-spec.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-106">For more information, see [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md) and [MSL Specification](~/ef6/modeling/designer/advanced/edmx/msl-spec.md).</span></span>

<span data-ttu-id="ba61d-107">CSDL は、Entity Data Model の Entity Framework の実装です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-107">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="ba61d-108">Entity Framework アプリケーションでは、概念モデルのメタデータは、csdl で記述された .csdl ファイルから EdmItemCollection のインスタンスに読み込まれ、のメソッドを使用してアクセスできます。System.string... メタデータ。</span><span class="sxs-lookup"><span data-stu-id="ba61d-108">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="ba61d-109">Entity Framework は、概念モデルのメタデータを使用して、概念モデルに対するクエリをデータソース固有のコマンドに変換します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-109">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="ba61d-110">EF デザイナーは、デザイン時に .edmx ファイルに概念モデル情報を格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-110">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="ba61d-111">EF デザイナーは、ビルド時に .edmx ファイルの情報を使用して、実行時に Entity Framework に必要な .csdl ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-111">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="ba61d-112">CSDL のバージョンは、XML 名前空間で区別されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-112">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="ba61d-113">CSDL のバージョン</span><span class="sxs-lookup"><span data-stu-id="ba61d-113">CSDL Version</span></span> | <span data-ttu-id="ba61d-114">XML 名前空間</span><span class="sxs-lookup"><span data-stu-id="ba61d-114">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="ba61d-115">CSDL v1</span><span class="sxs-lookup"><span data-stu-id="ba61d-115">CSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="ba61d-116">CSDL v2</span><span class="sxs-lookup"><span data-stu-id="ba61d-116">CSDL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="ba61d-117">CSDL v3</span><span class="sxs-lookup"><span data-stu-id="ba61d-117">CSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="ba61d-118">Association 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-118">Association Element (CSDL)</span></span>

<span data-ttu-id="ba61d-119">**Association**要素は、2つのエンティティ型間のリレーションシップを定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-119">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="ba61d-120">アソシエーションでは、リレーションシップに関連するエンティティ型、およびリレーションシップの各 End におけるエンティティ型の数 (多重度と呼ばれる) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-120">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="ba61d-121">アソシエーション end の多重度には、1 (1)、0または 1 (0 ..1)、または多数 (\*) の値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-121">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="ba61d-122">この情報は、2 つの子 End 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-122">This information is specified in two child End elements.</span></span>

<span data-ttu-id="ba61d-123">アソシエーションの一方の End にあるエンティティ型のインスタンスには、それらがエンティティ型で公開されている場合、ナビゲーション プロパティまたは外部キーからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-123">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="ba61d-124">アプリケーション内で、アソシエーションのインスタンスは、エンティティ型のインスタンスの間の特定のアソシエーションを表します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-124">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="ba61d-125">アソシエーション インスタンスは、アソシエーション セットに論理的にグループ化されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-125">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="ba61d-126">**Association**要素には、次の子要素を含めることができます (順に記載されています)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-126">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-127">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-127">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-128">End (ちょうど2つの要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-128">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="ba61d-129">すべての要素のインデックス (0 個または1個)</span><span class="sxs-lookup"><span data-stu-id="ba61d-129">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-130">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-130">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-131">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-131">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-132">次の表では、 **Association**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-132">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="ba61d-133">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-133">Attribute Name</span></span> | <span data-ttu-id="ba61d-134">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-134">Is Required</span></span> | <span data-ttu-id="ba61d-135">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-135">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="ba61d-136">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-136">**Name**</span></span>       | <span data-ttu-id="ba61d-137">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-137">Yes</span></span>         | <span data-ttu-id="ba61d-138">アソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-138">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-139">任意の数の annotation 属性 (カスタム XML 属性) を**Association**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-139">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="ba61d-140">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-140">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-141">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-141">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-142">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-142">Example</span></span>

<span data-ttu-id="ba61d-143">次の例では、外部キーが**Customer**および**Order**エンティティ型で公開されていない場合に、 **CustomerOrders**関連付けを定義する**association**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-143">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="ba61d-144">アソシエーションの各**End**の複数**要素**の接続性の値は、**顧客**に複数の**注文**を関連付けることができることを示していますが、**注文**に関連付けることができるのは1つの**顧客**だけです。</span><span class="sxs-lookup"><span data-stu-id="ba61d-144">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="ba61d-145">また、 **OnDelete**要素は、**顧客**が削除されると、特定の**顧客**に関連付けられていて、ObjectContext に読み込まれているすべての**注文**が削除されることを示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-145">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="ba61d-146">次の例は、 **Customer**および**Order**エンティティ型で外部キーが公開されている場合に、 **CustomerOrders**関連付けを定義する**association**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-146">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="ba61d-147">外部キーが公開されている場合、エンティティ間のリレーションシップは **、要素を使用して**管理されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-147">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="ba61d-148">このアソシエーションをデータ ソースにマップするために、対応する AssociationSetMapping 要素は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-148">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

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
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="ba61d-149">AssociationSet 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-149">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="ba61d-150">概念スキーマ定義言語 (CSDL) の**AssociationSet**要素は、同じ型のアソシエーションインスタンスの論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="ba61d-150">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="ba61d-151">アソシエーション セットは、複数のアソシエーション インスタンスを 1 つのデータ ソースにマップできるようグループ化する方法を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-151">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="ba61d-152">**AssociationSet**要素には、次の子要素を含めることができます (この順序で表示されます)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-152">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-153">Documentation (0 個または1個の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="ba61d-153">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="ba61d-154">End (2 つの要素が必要です)</span><span class="sxs-lookup"><span data-stu-id="ba61d-154">End (exactly two elements required)</span></span>
-   <span data-ttu-id="ba61d-155">Annotation 要素 (0 個以上の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="ba61d-155">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="ba61d-156">Association**属性は、アソシエーション**セットに含まれるアソシエーションの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-156">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="ba61d-157">アソシエーションセットの末尾を構成するエンティティセットは、厳密に2つの子**End**要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-157">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-158">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-158">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-159">次の表では、 **AssociationSet**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-159">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="ba61d-160">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-160">Attribute Name</span></span>  | <span data-ttu-id="ba61d-161">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-161">Is Required</span></span> | <span data-ttu-id="ba61d-162">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-162">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-163">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-163">**Name**</span></span>        | <span data-ttu-id="ba61d-164">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-164">Yes</span></span>         | <span data-ttu-id="ba61d-165">エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-165">The name of the entity set.</span></span> <span data-ttu-id="ba61d-166">**Name**属性の値を**Association**属性の値と同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-166">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="ba61d-167">**づける**</span><span class="sxs-lookup"><span data-stu-id="ba61d-167">**Association**</span></span> | <span data-ttu-id="ba61d-168">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-168">Yes</span></span>         | <span data-ttu-id="ba61d-169">関連付けの完全修飾名。そのインスタンスは、アソシエーション セットに格納されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-169">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="ba61d-170">関連付けは、アソシエーション セットと同じ名前空間に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-170">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-171">任意の数の annotation 属性 (カスタム XML 属性) を**AssociationSet**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-171">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="ba61d-172">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-172">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-173">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-173">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-174">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-174">Example</span></span>

<span data-ttu-id="ba61d-175">次の例は、2つの**AssociationSet**要素を持つ**EntityContainer**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-175">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

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
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="ba61d-176">CollectionType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-176">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="ba61d-177">概念スキーマ定義言語 (CSDL) の**CollectionType**要素は、関数パラメーターまたは関数の戻り値の型がコレクションであることを指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-177">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="ba61d-178">**CollectionType**要素は、Parameter 要素または ReturnType (Function) 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-178">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="ba61d-179">コレクションの型は、 **type**属性または次のいずれかの子要素を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-179">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="ba61d-180">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="ba61d-180">**CollectionType**</span></span>
-   <span data-ttu-id="ba61d-181">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="ba61d-181">ReferenceType</span></span>
-   <span data-ttu-id="ba61d-182">RowType</span><span class="sxs-lookup"><span data-stu-id="ba61d-182">RowType</span></span>
-   <span data-ttu-id="ba61d-183">TypeRef</span><span class="sxs-lookup"><span data-stu-id="ba61d-183">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="ba61d-184">**型**属性と子要素の両方でコレクションの型が指定されている場合、モデルは検証されません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-184">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-185">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-185">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-186">次の表では、 **CollectionType**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-186">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="ba61d-187">**DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**、および**Collation**属性は、 **edmsimpletypes**のコレクションにのみ適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-187">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="ba61d-188">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-188">Attribute Name</span></span>                                                          | <span data-ttu-id="ba61d-189">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-189">Is Required</span></span> | <span data-ttu-id="ba61d-190">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-190">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-191">**Type**</span><span class="sxs-lookup"><span data-stu-id="ba61d-191">**Type**</span></span>                                                                | <span data-ttu-id="ba61d-192">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-192">No</span></span>          | <span data-ttu-id="ba61d-193">コレクションの型。</span><span class="sxs-lookup"><span data-stu-id="ba61d-193">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="ba61d-194">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="ba61d-194">**Nullable**</span></span>                                                            | <span data-ttu-id="ba61d-195">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-195">No</span></span>          | <span data-ttu-id="ba61d-196">プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または **False**。</span><span class="sxs-lookup"><span data-stu-id="ba61d-196">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="ba61d-197">CSDL v1 で > 複合型プロパティには、`Nullable="False"`が必要です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-197">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="ba61d-198">**既定**</span><span class="sxs-lookup"><span data-stu-id="ba61d-198">**DefaultValue**</span></span>                                                        | <span data-ttu-id="ba61d-199">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-199">No</span></span>          | <span data-ttu-id="ba61d-200">プロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-200">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="ba61d-201">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-201">**MaxLength**</span></span>                                                           | <span data-ttu-id="ba61d-202">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-202">No</span></span>          | <span data-ttu-id="ba61d-203">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="ba61d-203">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="ba61d-204">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-204">**FixedLength**</span></span>                                                         | <span data-ttu-id="ba61d-205">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-205">No</span></span>          | <span data-ttu-id="ba61d-206">プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-206">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="ba61d-207">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="ba61d-207">**Precision**</span></span>                                                           | <span data-ttu-id="ba61d-208">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-208">No</span></span>          | <span data-ttu-id="ba61d-209">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="ba61d-209">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="ba61d-210">**スケール**</span><span class="sxs-lookup"><span data-stu-id="ba61d-210">**Scale**</span></span>                                                               | <span data-ttu-id="ba61d-211">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-211">No</span></span>          | <span data-ttu-id="ba61d-212">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="ba61d-212">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="ba61d-213">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ba61d-213">**SRID**</span></span>                                                                | <span data-ttu-id="ba61d-214">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-214">No</span></span>          | <span data-ttu-id="ba61d-215">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="ba61d-215">Spatial System Reference Identifier.</span></span> <span data-ttu-id="ba61d-216">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-216">Valid only for properties of spatial types.</span></span><span data-ttu-id="ba61d-217">   詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-217">   For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="ba61d-218">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="ba61d-218">**Unicode**</span></span>                                                             | <span data-ttu-id="ba61d-219">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-219">No</span></span>          | <span data-ttu-id="ba61d-220">プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-220">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="ba61d-221">**Collation**</span><span class="sxs-lookup"><span data-stu-id="ba61d-221">**Collation**</span></span>                                                           | <span data-ttu-id="ba61d-222">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-222">No</span></span>          | <span data-ttu-id="ba61d-223">データ ソースで使用される照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="ba61d-223">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="ba61d-224">任意の数の annotation 属性 (カスタム XML 属性) を**CollectionType**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-224">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="ba61d-225">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-225">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-226">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-226">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-227">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-227">Example</span></span>

<span data-ttu-id="ba61d-228">次の例は、 **CollectionType**要素を使用して、関数が**Person**エンティティ型のコレクション ( **ElementType**属性で指定) を返すことを指定するモデル定義関数を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-228">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

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
 

<span data-ttu-id="ba61d-229">次の例は、 **CollectionType**要素を使用して、 **RowType**要素で指定された行のコレクションを返すことを指定するモデル定義関数を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-229">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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
 

<span data-ttu-id="ba61d-230">次の例は、 **CollectionType**要素を使用して、関数が**学科**エンティティ型のコレクションをパラメーターとして受け入れることを指定するモデル定義関数を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-230">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

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
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="ba61d-231">ComplexType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-231">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="ba61d-232">**ComplexType**要素は、 **edmsimpletype**プロパティまたは他の複合型で構成されるデータ構造を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-232">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span><span data-ttu-id="ba61d-233">  複合型は、エンティティ型または別の複合型のプロパティにすることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-233">  A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="ba61d-234">複合型は、データを定義するという点でエンティティ型に似ています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-234">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="ba61d-235">ただし、複合型とエンティティ型の間にはいくつかの重要な違いがあります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-235">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="ba61d-236">複合型には ID (またはキー) がないため、独立して存在することができません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-236">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="ba61d-237">複合型は、エンティティ型またはその他の複合型のプロパティとしてのみ存在できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-237">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="ba61d-238">複合型は、アソシエーションに参加できません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-238">Complex types cannot participate in associations.</span></span> <span data-ttu-id="ba61d-239">アソシエーションのいずれの End にも複合型を指定できないため、複合型にはナビゲーション プロパティを定義できません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-239">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="ba61d-240">複合型のスカラー プロパティはそれぞれ null に設定できますが、複合型のプロパティには null 値を指定できません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-240">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="ba61d-241">**ComplexType**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-241">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-242">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-242">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-243">Property (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-243">Property (zero or more elements)</span></span>
-   <span data-ttu-id="ba61d-244">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-244">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="ba61d-245">次の表では、 **ComplexType**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-245">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="ba61d-246">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-246">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="ba61d-247">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-247">Is Required</span></span> | <span data-ttu-id="ba61d-248">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-248">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-249">Name</span><span class="sxs-lookup"><span data-stu-id="ba61d-249">Name</span></span>                                                                                                           | <span data-ttu-id="ba61d-250">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-250">Yes</span></span>         | <span data-ttu-id="ba61d-251">複合型の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-251">The name of the complex type.</span></span> <span data-ttu-id="ba61d-252">複合型の名前は、モデルのスコープ内にある別の複合型、エンティティ型、またはアソシエーションの名前と同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-252">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="ba61d-253">BaseType</span><span class="sxs-lookup"><span data-stu-id="ba61d-253">BaseType</span></span>                                                                                                       | <span data-ttu-id="ba61d-254">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-254">No</span></span>          | <span data-ttu-id="ba61d-255">定義される複合型の基本データ型である、別の複合型の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-255">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="ba61d-256">> この属性は CSDL v1 では適用できません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-256">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="ba61d-257">複合型の継承は、このバージョンではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-257">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="ba61d-258">概要</span><span class="sxs-lookup"><span data-stu-id="ba61d-258">Abstract</span></span>                                                                                                       | <span data-ttu-id="ba61d-259">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-259">No</span></span>          | <span data-ttu-id="ba61d-260">複合型が抽象型であるかどうかに応じて、 **True**または**False** (既定値)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-260">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="ba61d-261">> この属性は CSDL v1 では適用できません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-261">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="ba61d-262">このバージョンの複合型を抽象型にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-262">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="ba61d-263">任意の数の annotation 属性 (カスタム XML 属性) を**ComplexType**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-263">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="ba61d-264">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-264">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-265">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-265">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-266">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-266">Example</span></span>

<span data-ttu-id="ba61d-267">次の例では、 **Edmsimpletype**プロパティ**StreetAddress**、 **City**、 **StateOrProvince**、Country、および**郵便** **番号**を使用して、複合型**Address**を示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-267">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="ba61d-268">エンティティ型のプロパティとして複合型の**アドレス**(上記) を定義するには、エンティティ型の定義でプロパティの型を宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-268">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="ba61d-269">次の例では、エンティティ型 (**パブリッシャー**) の複合型として**Address**プロパティを示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-269">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

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
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="ba61d-270">DefiningExpression 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-270">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="ba61d-271">概念スキーマ定義言語 (CSDL) の definition **ingexpression**要素には、概念モデルの関数を定義する Entity SQL 式が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-271">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="ba61d-272">検証のために、**定義**要素には任意のコンテンツを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-272">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="ba61d-273">ただし、**定義**要素に有効な Entity SQL が含まれていない場合、Entity Framework は実行時に例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="ba61d-273">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-274">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-274">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-275">任意の数の annotation 属性 (カスタム XML 属性 **) を定義**することができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-275">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="ba61d-276">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-276">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-277">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-277">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="ba61d-278">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-278">Example</span></span>

<span data-ttu-id="ba61d-279">次の例では、定義、**式**を使用して、書籍が発行されてからの年数を返す関数を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-279">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="ba61d-280">**定義**済みの要素の内容は Entity SQL で記述されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-280">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="ba61d-281">Dependent 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-281">Dependent Element (CSDL)</span></span>

<span data-ttu-id="ba61d-282">概念スキーマ定義言語 (CSDL) の**dependent**要素は、参照に関する制約の依存 end を定義し、参照制約の依存 end を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-282">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="ba61d-283">参照整合性の**制約要素は、リレーショナル**データベースの参照整合性制約に似た機能を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-283">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="ba61d-284">データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティは別のエンティティ型のエンティティ キーを参照できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-284">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="ba61d-285">参照されるエンティティ型は、制約の*プリンシパル end*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-285">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="ba61d-286">プリンシパル end を参照するエンティティ型は、制約の*依存 end*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-286">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="ba61d-287">**Propertyref**要素は、プリンシパル end を参照するキーを指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-287">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="ba61d-288">**Dependent**要素には、次の子要素を含めることができます (順に記載されています)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-288">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-289">PropertyRef (1 つ以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-289">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="ba61d-290">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-290">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-291">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-291">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-292">次の表は、**依存**要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-292">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="ba61d-293">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-293">Attribute Name</span></span> | <span data-ttu-id="ba61d-294">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-294">Is Required</span></span> | <span data-ttu-id="ba61d-295">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-295">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="ba61d-296">**ロール**</span><span class="sxs-lookup"><span data-stu-id="ba61d-296">**Role**</span></span>       | <span data-ttu-id="ba61d-297">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-297">Yes</span></span>         | <span data-ttu-id="ba61d-298">アソシエーションの依存 End 上のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-298">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-299">任意の数の annotation 属性 (カスタム XML 属性) を**依存**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-299">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="ba61d-300">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-300">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-301">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-301">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-302">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-302">Example</span></span>

<span data-ttu-id="ba61d-303">次の例では、 **publishedby**関連付けの定義の一部として使用されている **、オブジェクトの**場所を示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-303">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="ba61d-304">**Book**エンティティ型の**PublisherId**プロパティは、参照制約の依存 end を構成します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-304">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

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
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="ba61d-305">Documentation 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-305">Documentation Element (CSDL)</span></span>

<span data-ttu-id="ba61d-306">概念スキーマ定義言語 (CSDL) の**Documentation**要素は、親要素で定義されているオブジェクトに関する情報を提供するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-306">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="ba61d-307">.Edmx ファイルで、 **documentation**要素が、EF デザイナーのデザイン画面 (エンティティ、アソシエーション、プロパティなど) でオブジェクトとして表示される要素の子である場合、 **documentation**要素の内容は、そのオブジェクトの Visual Studio の [ **プロパティ**] ウィンドウに表示されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-307">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="ba61d-308">**Documentation**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-308">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-309">**Summary**: 親要素の簡単な説明。</span><span class="sxs-lookup"><span data-stu-id="ba61d-309">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="ba61d-310">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-310">(zero or one element)</span></span>
-   <span data-ttu-id="ba61d-311">**Longdescription**: 親要素の詳細な説明。</span><span class="sxs-lookup"><span data-stu-id="ba61d-311">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="ba61d-312">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-312">(zero or one element)</span></span>
-   <span data-ttu-id="ba61d-313">annotation 要素</span><span class="sxs-lookup"><span data-stu-id="ba61d-313">Annotation elements.</span></span> <span data-ttu-id="ba61d-314">(0 個以上の要素)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-314">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-315">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-315">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-316">**Documentation**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-316">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="ba61d-317">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-317">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-318">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-318">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="ba61d-319">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-319">Example</span></span>

<span data-ttu-id="ba61d-320">次の例は、EntityType 要素の子要素としての**Documentation**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-320">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="ba61d-321">以下のスニペットが .edmx ファイルの CSDL コンテンツに含まれている場合、[`Customer`] エンティティ型をクリックすると、Visual Studio の [ **プロパティ**] ウィンドウに**Summary**要素と**longdescription**要素の内容が表示されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-321">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

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
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="ba61d-322">End 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-322">End Element (CSDL)</span></span>

<span data-ttu-id="ba61d-323">概念スキーマ定義言語 (CSDL) の**End**要素は、Association 要素または AssociationSet 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-323">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="ba61d-324">どちらの場合も、 **End**要素の役割は異なり、適用可能な属性も異なります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-324">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="ba61d-325">Association 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="ba61d-325">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="ba61d-326">**End**要素 ( **association**要素の子として) は、アソシエーションの一方の end のエンティティ型と、アソシエーションのその end に存在できるエンティティ型のインスタンスの数を識別します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-326">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="ba61d-327">アソシエーション End はアソシエーションの一部として定義され、アソシエーションには必ず 2 つのアソシエーション End が必要です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-327">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="ba61d-328">アソシエーションの 1 つの End にあるエンティティ型のインスタンスには、エンティティ型で公開されている場合、ナビゲーション プロパティまたは外部キーからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-328">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="ba61d-329">**終了**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-329">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-330">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-330">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-331">OnDelete (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-331">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-332">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-332">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-333">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-333">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-334">次の表では、 **End**要素が**Association**要素の子である場合に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-334">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="ba61d-335">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-335">Attribute Name</span></span>   | <span data-ttu-id="ba61d-336">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-336">Is Required</span></span> | <span data-ttu-id="ba61d-337">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-337">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-338">**Type**</span><span class="sxs-lookup"><span data-stu-id="ba61d-338">**Type**</span></span>         | <span data-ttu-id="ba61d-339">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-339">Yes</span></span>         | <span data-ttu-id="ba61d-340">アソシエーションの一方の End のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-340">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="ba61d-341">**ロール**</span><span class="sxs-lookup"><span data-stu-id="ba61d-341">**Role**</span></span>         | <span data-ttu-id="ba61d-342">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-342">No</span></span>          | <span data-ttu-id="ba61d-343">アソシエーション End の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-343">A name for the association end.</span></span> <span data-ttu-id="ba61d-344">名前が指定されない場合、アソシエーション End のエンティティ型の名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-344">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="ba61d-345">**カーディナリティ**</span><span class="sxs-lookup"><span data-stu-id="ba61d-345">**Multiplicity**</span></span> | <span data-ttu-id="ba61d-346">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-346">Yes</span></span>         | <span data-ttu-id="ba61d-347">**1**、 **0 ..1**、またはアソシエーションの最後にあるエンティティ型のインスタンスの数に応じて **\*** ます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-347">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="ba61d-348">**1**は、アソシエーション end に1つのエンティティ型インスタンスが存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-348">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="ba61d-349">**0 ..1**は、アソシエーション end に0個または1個のエンティティ型インスタンスが存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-349">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="ba61d-350">**\*** は、アソシエーション end に0個以上のエンティティ型インスタンスが存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-350">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-351">任意の数の annotation 属性 (カスタム XML 属性) を**終了**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-351">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="ba61d-352">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-352">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-353">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-353">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="ba61d-354">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-354">Example</span></span>

<span data-ttu-id="ba61d-355">次の例は、 **CustomerOrders**の関連付けを定義する**association**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-355">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="ba61d-356">アソシエーションの各**End**の複数**要素**の接続性の値は、**顧客**に複数の**注文**を関連付けることができることを示していますが、**注文**に関連付けることができるのは1つの**顧客**だけです。</span><span class="sxs-lookup"><span data-stu-id="ba61d-356">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="ba61d-357">また、 **OnDelete**要素は、**顧客**が削除された場合に、特定の**顧客**に関連付けられているすべての注文と ObjectContext に読み込まれたすべての**注文**を削除することを示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-357">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="ba61d-358">AssociationSet 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="ba61d-358">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="ba61d-359">**End**要素は、アソシエーションセットの1つの端を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-359">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="ba61d-360">**AssociationSet**要素には、2つの**End**要素が含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-360">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="ba61d-361">**End**要素に含まれる情報は、アソシエーションセットをデータソースにマップするときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-361">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="ba61d-362">**終了**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-362">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-363">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-363">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-364">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-364">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="ba61d-365">Annotation 要素は、それ以外のすべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-365">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="ba61d-366">Annotation 要素は、CSDL v2 以降でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-366">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-367">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-367">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-368">次の表では、 **AssociationSet**要素の子である場合に**End**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-368">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="ba61d-369">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-369">Attribute Name</span></span> | <span data-ttu-id="ba61d-370">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-370">Is Required</span></span> | <span data-ttu-id="ba61d-371">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-371">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-372">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="ba61d-372">**EntitySet**</span></span>  | <span data-ttu-id="ba61d-373">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-373">Yes</span></span>         | <span data-ttu-id="ba61d-374">親**AssociationSet**要素の1つの end を定義する**EntitySet**要素の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-374">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="ba61d-375">**EntitySet**要素は、親**AssociationSet**要素と同じエンティティコンテナーで定義されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-375">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="ba61d-376">**ロール**</span><span class="sxs-lookup"><span data-stu-id="ba61d-376">**Role**</span></span>       | <span data-ttu-id="ba61d-377">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-377">No</span></span>          | <span data-ttu-id="ba61d-378">アソシエーション セット End の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-378">The name of the association set end.</span></span> <span data-ttu-id="ba61d-379">**Role**属性が使用されていない場合、アソシエーションセット end の名前はエンティティセットの名前になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-379">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="ba61d-380">任意の数の annotation 属性 (カスタム XML 属性) を**終了**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-380">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="ba61d-381">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-381">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-382">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-382">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="ba61d-383">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-383">Example</span></span>

<span data-ttu-id="ba61d-384">次の例は、 **2 つの3つ**の要素を持つ**AssociationSet**要素を2つ持つ**EntityContainer**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-384">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

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
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="ba61d-385">EntityContainer 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-385">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="ba61d-386">概念スキーマ定義言語 (CSDL) の**EntityContainer**要素は、エンティティセット、アソシエーションセット、および関数インポートの論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="ba61d-386">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="ba61d-387">概念モデルのエンティティ コンテナーは、EntityContainerMapping 要素を通じてストレージ モデルのエンティティ コンテナーにマップされます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-387">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="ba61d-388">ストレージ モデルのエンティティ コンテナーは、データベースの構造を記述します。エンティティ セットはデータベースのテーブル、アソシエーション セットは外部キー、関数インポートはストアド プロシージャをそれぞれ記述します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-388">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="ba61d-389">**EntityContainer**要素には、0個または1個の Documentation 要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-389">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="ba61d-390">**Documentation**要素が存在する場合は、 **EntitySet**、 **AssociationSet**、および**FunctionImport**のすべての要素の前に記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-390">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="ba61d-391">**EntityContainer**要素には、次の子要素を0個以上含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-391">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-392">EntitySet</span><span class="sxs-lookup"><span data-stu-id="ba61d-392">EntitySet</span></span>
-   <span data-ttu-id="ba61d-393">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="ba61d-393">AssociationSet</span></span>
-   <span data-ttu-id="ba61d-394">FunctionImport 要素</span><span class="sxs-lookup"><span data-stu-id="ba61d-394">FunctionImport</span></span>
-   <span data-ttu-id="ba61d-395">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="ba61d-395">Annotation elements</span></span>

<span data-ttu-id="ba61d-396">**Entitycontainer**要素を拡張して、同じ名前空間内にある別の**entitycontainer**の内容を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-396">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="ba61d-397">別の**entitycontainer**の内容を参照する**entitycontainer**要素に含めるには、 **Extends**属性の値を、含める**entitycontainer**要素の名前に設定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-397">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="ba61d-398">含まれている**entitycontainer**要素のすべての子要素は、参照している**entitycontainer**要素の子要素として扱われます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-398">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-399">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-399">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-400">次の表は、 **Using**要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-400">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="ba61d-401">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-401">Attribute Name</span></span> | <span data-ttu-id="ba61d-402">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-402">Is Required</span></span> | <span data-ttu-id="ba61d-403">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-403">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="ba61d-404">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-404">**Name**</span></span>       | <span data-ttu-id="ba61d-405">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-405">Yes</span></span>         | <span data-ttu-id="ba61d-406">エンティティ コンテナー名。</span><span class="sxs-lookup"><span data-stu-id="ba61d-406">The name of the entity container.</span></span>                               |
| <span data-ttu-id="ba61d-407">**拡張**</span><span class="sxs-lookup"><span data-stu-id="ba61d-407">**Extends**</span></span>    | <span data-ttu-id="ba61d-408">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-408">No</span></span>          | <span data-ttu-id="ba61d-409">同じ名前空間内にある別のエンティティ コンテナーの名前</span><span class="sxs-lookup"><span data-stu-id="ba61d-409">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-410">任意の数の annotation 属性 (カスタム XML 属性) を**EntityContainer**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-410">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="ba61d-411">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-411">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-412">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-412">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-413">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-413">Example</span></span>

<span data-ttu-id="ba61d-414">次の例は、3つのエンティティセットと2つのアソシエーションセットを定義する**EntityContainer**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-414">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

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
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="ba61d-415">EntitySet 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-415">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="ba61d-416">概念スキーマ定義言語の**EntitySet**要素は、エンティティ型のインスタンスと、そのエンティティ型から派生した任意の型のインスタンスの論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="ba61d-416">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="ba61d-417">エンティティ型とエンティティ セットの間のリレーションシップは、リレーショナル データベースの行とテーブルの間のリレーションシップと似ています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-417">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="ba61d-418">エンティティ型は、行と同様に関連データのセットを定義し、エンティティ セットには、テーブルと同様に、その定義のインスタンスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-418">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="ba61d-419">エンティティ セットは、エンティティ型のインスタンスをグループ化するための構造を提供します。これにより、データ ソース内の関連するデータ構造に、エンティティ型のインスタンスをマッピングできるようになります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-419">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="ba61d-420">特定のエンティティ型に対して複数のエンティティ セットを定義できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-420">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="ba61d-421">EF デザイナーでは、型ごとに複数のエンティティセットを含む概念モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-421">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="ba61d-422">**EntitySet**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-422">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-423">Documentation 要素 (0 個または1個の要素が許可されています)</span><span class="sxs-lookup"><span data-stu-id="ba61d-423">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="ba61d-424">Annotation 要素 (0 個以上の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="ba61d-424">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-425">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-425">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-426">次の表は、 **EntitySet**要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-426">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="ba61d-427">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-427">Attribute Name</span></span> | <span data-ttu-id="ba61d-428">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-428">Is Required</span></span> | <span data-ttu-id="ba61d-429">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-429">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-430">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-430">**Name**</span></span>       | <span data-ttu-id="ba61d-431">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-431">Yes</span></span>         | <span data-ttu-id="ba61d-432">エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-432">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="ba61d-433">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="ba61d-433">**EntityType**</span></span> | <span data-ttu-id="ba61d-434">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-434">Yes</span></span>         | <span data-ttu-id="ba61d-435">エンティティ型の完全修飾名。そのインスタンスは、エンティティ セットに格納されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-435">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-436">**EntitySet**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-436">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="ba61d-437">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-437">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-438">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-438">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-439">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-439">Example</span></span>

<span data-ttu-id="ba61d-440">次の例は、3つの**EntitySet**要素を持つ**EntityContainer**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-440">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

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
 

<span data-ttu-id="ba61d-441">型ごとに複数のエンティティ セット (Multiple-Entity-Sets-per-Type: MEST) を定義できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-441">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="ba61d-442">次の例では、 **Book**エンティティ型に対して2つのエンティティセットを持つエンティティコンテナーを定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-442">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

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
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="ba61d-443">EntityType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-443">EntityType Element (CSDL)</span></span>

<span data-ttu-id="ba61d-444">**EntityType**要素は、概念モデルの最上位レベルの概念 (顧客や注文など) の構造を表します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-444">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="ba61d-445">エンティティ型は、アプリケーションのエンティティ型インスタンス用テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="ba61d-445">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="ba61d-446">各テンプレートには、次の情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-446">Each template contains the following information:</span></span>

-   <span data-ttu-id="ba61d-447">一意の名前</span><span class="sxs-lookup"><span data-stu-id="ba61d-447">A unique name.</span></span> <span data-ttu-id="ba61d-448">(必須)</span><span class="sxs-lookup"><span data-stu-id="ba61d-448">(Required.)</span></span>
-   <span data-ttu-id="ba61d-449">1 つ以上のプロパティにより定義されるエンティティ キー</span><span class="sxs-lookup"><span data-stu-id="ba61d-449">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="ba61d-450">(必須)</span><span class="sxs-lookup"><span data-stu-id="ba61d-450">(Required.)</span></span>
-   <span data-ttu-id="ba61d-451">データ格納用のプロパティ</span><span class="sxs-lookup"><span data-stu-id="ba61d-451">Properties for containing data.</span></span> <span data-ttu-id="ba61d-452">(省略可能)</span><span class="sxs-lookup"><span data-stu-id="ba61d-452">(Optional.)</span></span>
-   <span data-ttu-id="ba61d-453">アソシエーションの 1 つの End から別の End へのナビゲーションを可能にするナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="ba61d-453">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="ba61d-454">(省略可能)</span><span class="sxs-lookup"><span data-stu-id="ba61d-454">(Optional.)</span></span>

<span data-ttu-id="ba61d-455">アプリケーションでは、エンティティ型のインスタンスが特定のオブジェクト (特定の顧客や注文など) を表します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-455">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="ba61d-456">エンティティ型の各インスタンスに対して、エンティティ セット内のエンティティ キーを一意にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-456">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="ba61d-457">2 つのエンティティ型のインスタンスは、型が同じであり、エンティティ キーの値が等しい場合にのみ、等価のインスタンスと見なされます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-457">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="ba61d-458">**EntityType**要素には、次の子要素を含めることができます (この順序で表示されます)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-458">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-459">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-459">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-460">Key (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-460">Key (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-461">Property (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-461">Property (zero or more elements)</span></span>
-   <span data-ttu-id="ba61d-462">NavigationProperty (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-462">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="ba61d-463">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-463">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-464">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-464">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-465">次の表では、 **EntityType**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-465">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="ba61d-466">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-466">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="ba61d-467">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-467">Is Required</span></span> | <span data-ttu-id="ba61d-468">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-468">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-469">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-469">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="ba61d-470">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-470">Yes</span></span>         | <span data-ttu-id="ba61d-471">エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-471">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="ba61d-472">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="ba61d-472">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="ba61d-473">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-473">No</span></span>          | <span data-ttu-id="ba61d-474">定義するエンティティ型の基本の型である別のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-474">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="ba61d-475">**抽出**</span><span class="sxs-lookup"><span data-stu-id="ba61d-475">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="ba61d-476">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-476">No</span></span>          | <span data-ttu-id="ba61d-477">エンティティ型が抽象型であるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-477">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="ba61d-478">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="ba61d-478">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="ba61d-479">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-479">No</span></span>          | <span data-ttu-id="ba61d-480">エンティティ型がオープンエンティティ型かどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-480">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="ba61d-481">> **OpenType**属性は、ADO.NET Data Services で使用される概念モデルで定義されているエンティティ型にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-481">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="ba61d-482">任意の数の annotation 属性 (カスタム XML 属性) を**EntityType**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-482">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="ba61d-483">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-483">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-484">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-484">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-485">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-485">Example</span></span>

<span data-ttu-id="ba61d-486">次の例は、3つの**プロパティ**要素と2つの**NavigationProperty**要素を持つ**EntityType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-486">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

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
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="ba61d-487">EnumType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-487">EnumType Element (CSDL)</span></span>

<span data-ttu-id="ba61d-488">**EnumType**要素は列挙型を表します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-488">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="ba61d-489">**EnumType**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-489">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-490">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-490">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-491">Member (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-491">Member (zero or more elements)</span></span>
-   <span data-ttu-id="ba61d-492">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-492">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-493">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-493">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-494">次の表は、 **EnumType**要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-494">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="ba61d-495">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-495">Attribute Name</span></span>     | <span data-ttu-id="ba61d-496">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-496">Is Required</span></span> | <span data-ttu-id="ba61d-497">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-497">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-498">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-498">**Name**</span></span>           | <span data-ttu-id="ba61d-499">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-499">Yes</span></span>         | <span data-ttu-id="ba61d-500">エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-500">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="ba61d-501">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="ba61d-501">**IsFlags**</span></span>        | <span data-ttu-id="ba61d-502">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-502">No</span></span>          | <span data-ttu-id="ba61d-503">列挙型をフラグのセットとして使用できるかどうかによって、 **True**または**False**。</span><span class="sxs-lookup"><span data-stu-id="ba61d-503">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="ba61d-504">既定値は**False です**。</span><span class="sxs-lookup"><span data-stu-id="ba61d-504">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="ba61d-505">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="ba61d-505">**UnderlyingType**</span></span> | <span data-ttu-id="ba61d-506">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-506">No</span></span>          | <span data-ttu-id="ba61d-507">**Edm. Byte**, Edm. **Int16**, edm. **Int32**, **Edm. Int64**または**edm. SByte**型の値の範囲を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-507">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span> <span data-ttu-id="ba61d-508">  列挙要素の基になる既定の型は、 **Edm. Int32.** です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-508">  The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-509">**EnumType**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-509">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="ba61d-510">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-510">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-511">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-511">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-512">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-512">Example</span></span>

<span data-ttu-id="ba61d-513">次の例は、3つの**メンバー**要素を持つ**EnumType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-513">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="ba61d-514">Function 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-514">Function Element (CSDL)</span></span>

<span data-ttu-id="ba61d-515">概念スキーマ定義言語 (CSDL) の**Function**要素は、概念モデルで関数を定義または宣言するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-515">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="ba61d-516">関数は、DefiningExpression 要素を使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-516">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="ba61d-517">**Function**要素には、次の子要素を含めることができます (順に記載されています)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-517">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-518">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-518">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-519">Parameter (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-519">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="ba61d-520">DefiningExpression (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-520">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-521">ReturnType (Function) (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-521">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-522">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-522">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="ba61d-523">関数の戻り値の型は、 **returntype** (function) 要素または**returntype**属性 (下記参照) のいずれかで指定する必要がありますが、両方を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-523">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="ba61d-524">有効な戻り値の型には、EdmSimpleType、エンティティ型、複合型、行型、または参照型 (あるいはこれらの型のいずれかのコレクション) があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-524">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-525">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-525">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-526">次の表では、 **Function**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-526">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="ba61d-527">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-527">Attribute Name</span></span> | <span data-ttu-id="ba61d-528">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-528">Is Required</span></span> | <span data-ttu-id="ba61d-529">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-529">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="ba61d-530">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-530">**Name**</span></span>       | <span data-ttu-id="ba61d-531">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-531">Yes</span></span>         | <span data-ttu-id="ba61d-532">関数の名前です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-532">The name of the function.</span></span>          |
| <span data-ttu-id="ba61d-533">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="ba61d-533">**ReturnType**</span></span> | <span data-ttu-id="ba61d-534">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-534">No</span></span>          | <span data-ttu-id="ba61d-535">関数の戻り値の型。</span><span class="sxs-lookup"><span data-stu-id="ba61d-535">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-536">**関数**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-536">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="ba61d-537">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-537">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-538">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-538">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-539">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-539">Example</span></span>

<span data-ttu-id="ba61d-540">次の例では、**関数**要素を使用して、インストラクターが雇用されてからの年数を返す関数を定義しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-540">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="ba61d-541">FunctionImport 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-541">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="ba61d-542">概念スキーマ定義言語 (CSDL) の**FunctionImport**要素は、データソースで定義されているものの、概念モデルを通じてオブジェクトで使用できる関数を表します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-542">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="ba61d-543">たとえば、ストレージ モデルの Function 要素を使用して、データベースのストアド プロシージャを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-543">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="ba61d-544">概念モデルの**FunctionImport**要素は、Entity Framework アプリケーション内の対応する関数を表し、FunctionImportMapping 要素を使用してストレージモデル関数にマップされます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-544">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="ba61d-545">関数がアプリケーションで呼び出されると、対応するストアド プロシージャがデータベースで実行されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-545">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="ba61d-546">**FunctionImport**要素には、次の子要素を含めることができます (この順序で表示されます)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-546">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-547">Documentation (0 個または1個の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="ba61d-547">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="ba61d-548">Parameter (0 個以上の要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-548">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="ba61d-549">Annotation 要素 (0 個以上の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="ba61d-549">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="ba61d-550">ReturnType (FunctionImport) (0 個以上の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="ba61d-550">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="ba61d-551">関数が受け入れるパラメーターごとに1つの**パラメーター**要素を定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-551">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="ba61d-552">関数の戻り値の型は、 **returntype** (FunctionImport) 要素または**returntype**属性 (下記参照) のいずれかで指定する必要がありますが、両方を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-552">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="ba61d-553">戻り値の型の値は、EdmSimpleType、EntityType、または ComplexType のコレクションである必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-553">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-554">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-554">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-555">次の表は、 **FunctionImport**要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-555">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="ba61d-556">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-556">Attribute Name</span></span>   | <span data-ttu-id="ba61d-557">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-557">Is Required</span></span> | <span data-ttu-id="ba61d-558">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-558">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-559">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-559">**Name**</span></span>         | <span data-ttu-id="ba61d-560">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-560">Yes</span></span>         | <span data-ttu-id="ba61d-561">インポートされる関数の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-561">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="ba61d-562">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="ba61d-562">**ReturnType**</span></span>   | <span data-ttu-id="ba61d-563">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-563">No</span></span>          | <span data-ttu-id="ba61d-564">関数が返す型。</span><span class="sxs-lookup"><span data-stu-id="ba61d-564">The type that the function returns.</span></span> <span data-ttu-id="ba61d-565">関数が値を返さない場合は、この属性を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-565">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="ba61d-566">それ以外の場合、値は ComplexType、EntityType、または EDMSimpleType のコレクションである必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-566">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="ba61d-567">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="ba61d-567">**EntitySet**</span></span>    | <span data-ttu-id="ba61d-568">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-568">No</span></span>          | <span data-ttu-id="ba61d-569">関数がエンティティ型のコレクションを返す場合、 **EntitySet**の値は、コレクションが属するエンティティセットである必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-569">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="ba61d-570">そうでない場合は、 **EntitySet** 属性を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-570">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="ba61d-571">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="ba61d-571">**IsComposable**</span></span> | <span data-ttu-id="ba61d-572">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-572">No</span></span>          | <span data-ttu-id="ba61d-573">値が true に設定されている場合、関数はコンポーザブル (テーブル値関数) であり、LINQ クエリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-573">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span><span data-ttu-id="ba61d-574">  既定値は**false**です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-574">  The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="ba61d-575">任意の数の annotation 属性 (カスタム XML 属性) を**FunctionImport**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-575">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="ba61d-576">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-576">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-577">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-577">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-578">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-578">Example</span></span>

<span data-ttu-id="ba61d-579">次の例は、1つのパラメーターを受け取り、エンティティ型のコレクションを返す**FunctionImport**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-579">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="ba61d-580">key 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-580">Key Element (CSDL)</span></span>

<span data-ttu-id="ba61d-581">**Key**要素は EntityType 要素の子要素であり、*エンティティキー* (id を決定するエンティティ型のプロパティまたはプロパティのセット) を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-581">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="ba61d-582">エンティティ キーを構成するプロパティは、デザイン時に選択されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-582">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="ba61d-583">エンティティ キー プロパティの値は、実行時にエンティティ セット内でエンティティ型のインスタンスを一意に識別する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-583">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="ba61d-584">エンティティ キーを構成するプロパティには、エンティティ セット内のインスタンスの一意性を保証するものを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-584">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="ba61d-585">**Key**要素は、エンティティ型の1つ以上のプロパティを参照することによって、エンティティキーを定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-585">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="ba61d-586">**Key**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-586">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="ba61d-587">PropertyRef (1 つ以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-587">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="ba61d-588">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-588">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-589">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-589">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-590">任意の数の annotation 属性 (カスタム XML 属性) を**キー**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-590">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="ba61d-591">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-592">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="ba61d-593">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-593">Example</span></span>

<span data-ttu-id="ba61d-594">次の例では、 **Book**という名前のエンティティ型を定義しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-594">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="ba61d-595">エンティティキーは、エンティティ型の**ISBN**プロパティを参照することによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-595">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="ba61d-596">**Isbn**プロパティは、国際標準書籍番号 (ISBN) が書籍を一意に識別するため、エンティティキーに適した選択肢です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-596">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="ba61d-597">次の例は、**名前**と**アドレス**の2つのプロパティで構成されるエンティティキーを持つエンティティ型 (**Author**) を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-597">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

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
 

<span data-ttu-id="ba61d-598">同じ名前の2人の作成者が同じアドレスに住んでいる可能性が低いため、エンティティキーには**名前**と**アドレス**を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ba61d-598">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="ba61d-599">ただし、エンティティ キーのこの選択では、エンティティ セット内のエンティティ キーの一意性を絶対的に保証することはできません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-599">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="ba61d-600">この場合は、作成者を一意に識別するために使用できる、 **AuthorId**などのプロパティを追加することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ba61d-600">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="ba61d-601">Member 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-601">Member Element (CSDL)</span></span>

<span data-ttu-id="ba61d-602">**Member**要素は EnumType 要素の子要素であり、列挙型のメンバーを定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-602">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-603">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-603">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-604">次の表は、 **FunctionImport**要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-604">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="ba61d-605">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-605">Attribute Name</span></span> | <span data-ttu-id="ba61d-606">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-606">Is Required</span></span> | <span data-ttu-id="ba61d-607">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-607">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-608">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-608">**Name**</span></span>       | <span data-ttu-id="ba61d-609">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-609">Yes</span></span>         | <span data-ttu-id="ba61d-610">メンバーの名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-610">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="ba61d-611">**Value**</span><span class="sxs-lookup"><span data-stu-id="ba61d-611">**Value**</span></span>      | <span data-ttu-id="ba61d-612">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-612">No</span></span>          | <span data-ttu-id="ba61d-613">メンバーの値。</span><span class="sxs-lookup"><span data-stu-id="ba61d-613">The value of the member.</span></span> <span data-ttu-id="ba61d-614">既定では、最初のメンバーの値は0で、連続する各列挙子の値は1ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-614">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="ba61d-615">同じ値を持つ複数のメンバーが存在する場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-615">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-616">任意の数の annotation 属性 (カスタム XML 属性) を**FunctionImport**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-616">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="ba61d-617">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-617">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-618">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-618">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-619">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-619">Example</span></span>

<span data-ttu-id="ba61d-620">次の例は、3つの**メンバー**要素を持つ**EnumType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-620">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="ba61d-621">NavigationProperty 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-621">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="ba61d-622">**NavigationProperty**要素は、アソシエーションのもう一方の end への参照を提供するナビゲーションプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-622">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="ba61d-623">Property 要素で定義されるプロパティとは異なり、ナビゲーション プロパティはデータの形と特性を定義しません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-623">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="ba61d-624">ナビゲーション プロパティは、アソシエーション内で 2 つのエンティティ型間を移動するための手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-624">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="ba61d-625">ナビゲーション プロパティは、アソシエーション End の両方のエンティティ型で省略可能です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-625">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="ba61d-626">1 つのアソシエーション End のエンティティ型にナビゲーション プロパティを定義した場合に、そのアソシエーションの他方の End でもエンティティ型にナビゲーション プロパティを定義する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-626">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="ba61d-627">ナビゲーション プロパティによって返されるデータ型は、リモートのアソシエーション End の多重度により決まります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-627">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="ba61d-628">たとえば、ナビゲーションプロパティ**Ordersnavprop**が**customer**エンティティ型に存在し、 **customer**と**Order**の間に一対多のアソシエーションを移動するとします。</span><span class="sxs-lookup"><span data-stu-id="ba61d-628">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="ba61d-629">ナビゲーションプロパティのリモートアソシエーション end は多重度が多 (\*) であるため、そのデータ型はコレクション (**順序**) です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-629">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="ba61d-630">同様に、ナビゲーションプロパティの Customer navigation **prop**が**Order**エンティティ型に存在する場合、リモート end の多重度は 1 (1) であるため、データ型は**顧客**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-630">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="ba61d-631">**NavigationProperty**要素には、次の子要素を含めることができます (この順序で表示されます)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-631">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-632">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-632">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-633">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-633">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-634">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-634">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-635">次の表では、 **NavigationProperty**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-635">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="ba61d-636">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-636">Attribute Name</span></span>   | <span data-ttu-id="ba61d-637">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-637">Is Required</span></span> | <span data-ttu-id="ba61d-638">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-638">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-639">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-639">**Name**</span></span>         | <span data-ttu-id="ba61d-640">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-640">Yes</span></span>         | <span data-ttu-id="ba61d-641">ナビゲーション プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-641">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="ba61d-642">**リレーションシップ**</span><span class="sxs-lookup"><span data-stu-id="ba61d-642">**Relationship**</span></span> | <span data-ttu-id="ba61d-643">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-643">Yes</span></span>         | <span data-ttu-id="ba61d-644">モデルのスコープ内にあるアソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-644">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="ba61d-645">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="ba61d-645">**ToRole**</span></span>       | <span data-ttu-id="ba61d-646">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-646">Yes</span></span>         | <span data-ttu-id="ba61d-647">ナビゲーションが終了する側のアソシエーション End。</span><span class="sxs-lookup"><span data-stu-id="ba61d-647">The end of the association at which navigation ends.</span></span> <span data-ttu-id="ba61d-648">**Torole**属性の値は、アソシエーション end のいずれかで定義されている**ロール**属性の1つの値と同じである必要があります (associationend 要素で定義されています)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-648">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="ba61d-649">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="ba61d-649">**FromRole**</span></span>     | <span data-ttu-id="ba61d-650">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-650">Yes</span></span>         | <span data-ttu-id="ba61d-651">ナビゲーションが始まる側のアソシエーション End。</span><span class="sxs-lookup"><span data-stu-id="ba61d-651">The end of the association from which navigation begins.</span></span> <span data-ttu-id="ba61d-652">**Fromrole**属性の値は、アソシエーション end のいずれかで定義されているいずれかの**ロール**属性の値と同じである必要があります (associationend 要素で定義されています)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-652">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-653">任意の数の annotation 属性 (カスタム XML 属性) を**NavigationProperty**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-653">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="ba61d-654">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-654">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-655">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-655">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-656">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-656">Example</span></span>

<span data-ttu-id="ba61d-657">次の例では、2つのナビゲーションプロパティ (**publishedby** **writ)** を持つエンティティ型 (**Book**) を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-657">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

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
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="ba61d-658">OnDelete 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-658">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="ba61d-659">概念スキーマ定義言語 (CSDL) の**OnDelete**要素は、アソシエーションに関連付けられている動作を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-659">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="ba61d-660">**アクション**属性がアソシエーションの一方の End で**Cascade**に設定されている場合、最初の end のエンティティ型が削除されると、アソシエーションのもう一方の end に関連するエンティティ型が削除されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-660">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="ba61d-661">2つのエンティティ型間のアソシエーションが主キーと主キーのリレーションシップである場合、アソシエーションのもう一方の end のプリンシパルオブジェクトが**OnDelete**の仕様に関係なく削除されると、読み込まれた依存オブジェクトが削除されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-661">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="ba61d-662">**OnDelete**要素は、アプリケーションの実行時の動作にのみ影響します。データソースの動作には影響しません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-662">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="ba61d-663">データ ソースで定義された動作は、アプリケーションで定義された動作と同じである必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-663">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="ba61d-664">**OnDelete**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-664">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-665">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-665">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-666">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-666">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-667">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-667">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-668">次の表は、 **OnDelete**要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-668">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="ba61d-669">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-669">Attribute Name</span></span> | <span data-ttu-id="ba61d-670">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-670">Is Required</span></span> | <span data-ttu-id="ba61d-671">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-671">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-672">**操作**</span><span class="sxs-lookup"><span data-stu-id="ba61d-672">**Action**</span></span>     | <span data-ttu-id="ba61d-673">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-673">Yes</span></span>         | <span data-ttu-id="ba61d-674">**Cascade**または**None**。</span><span class="sxs-lookup"><span data-stu-id="ba61d-674">**Cascade** or **None**.</span></span> <span data-ttu-id="ba61d-675">**Cascade**を指定すると、プリンシパルエンティティ型が削除されるときに依存エンティティ型が削除されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-675">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="ba61d-676">存在**しない場合、** プリンシパルエンティティ型が削除されても依存エンティティ型は削除されません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-676">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-677">任意の数の annotation 属性 (カスタム XML 属性) を**Association**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-677">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="ba61d-678">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-678">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-679">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-679">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-680">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-680">Example</span></span>

<span data-ttu-id="ba61d-681">次の例は、 **CustomerOrders**の関連付けを定義する**association**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-681">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="ba61d-682">**OnDelete**要素は、特定の**顧客**に関連付けられているすべての**注文**が ObjectContext に読み込まれていることを示します。これは、**顧客**が削除されると削除されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-682">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="ba61d-683">Parameter 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-683">Parameter Element (CSDL)</span></span>

<span data-ttu-id="ba61d-684">概念スキーマ定義言語 (CSDL) の**Parameter**要素は、FunctionImport 要素または Function 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-684">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="ba61d-685">FunctionImport 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="ba61d-685">FunctionImport Element Application</span></span>

<span data-ttu-id="ba61d-686">**パラメーター**要素 ( **FunctionImport**要素の子として) は、CSDL で宣言された関数インポートの入力パラメーターと出力パラメーターを定義するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-686">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="ba61d-687">**Parameter**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-687">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-688">Documentation (0 個または1個の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="ba61d-688">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="ba61d-689">Annotation 要素 (0 個以上の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="ba61d-689">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-690">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-690">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-691">次の表では、 **Parameter**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-691">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="ba61d-692">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-692">Attribute Name</span></span> | <span data-ttu-id="ba61d-693">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-693">Is Required</span></span> | <span data-ttu-id="ba61d-694">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-694">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-695">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-695">**Name**</span></span>       | <span data-ttu-id="ba61d-696">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-696">Yes</span></span>         | <span data-ttu-id="ba61d-697">パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-697">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="ba61d-698">**Type**</span><span class="sxs-lookup"><span data-stu-id="ba61d-698">**Type**</span></span>       | <span data-ttu-id="ba61d-699">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-699">Yes</span></span>         | <span data-ttu-id="ba61d-700">パラメーターの型。</span><span class="sxs-lookup"><span data-stu-id="ba61d-700">The parameter type.</span></span> <span data-ttu-id="ba61d-701">この値には、 **EDMSimpleType** か、モデルのスコープ内にある複合型を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-701">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="ba61d-702">**モード**</span><span class="sxs-lookup"><span data-stu-id="ba61d-702">**Mode**</span></span>       | <span data-ttu-id="ba61d-703">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-703">No</span></span>          | <span data-ttu-id="ba61d-704">**In**、 **Out**、または**InOut**は、パラメーターが入力パラメーター、出力パラメーター、または入力/出力パラメーターのどちらであるかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-704">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="ba61d-705">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-705">**MaxLength**</span></span>  | <span data-ttu-id="ba61d-706">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-706">No</span></span>          | <span data-ttu-id="ba61d-707">パラメーターの許容される最大長</span><span class="sxs-lookup"><span data-stu-id="ba61d-707">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="ba61d-708">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="ba61d-708">**Precision**</span></span>  | <span data-ttu-id="ba61d-709">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-709">No</span></span>          | <span data-ttu-id="ba61d-710">パラメーターの説明</span><span class="sxs-lookup"><span data-stu-id="ba61d-710">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="ba61d-711">**スケール**</span><span class="sxs-lookup"><span data-stu-id="ba61d-711">**Scale**</span></span>      | <span data-ttu-id="ba61d-712">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-712">No</span></span>          | <span data-ttu-id="ba61d-713">パラメーターの小数点以下桁数</span><span class="sxs-lookup"><span data-stu-id="ba61d-713">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="ba61d-714">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ba61d-714">**SRID**</span></span>       | <span data-ttu-id="ba61d-715">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-715">No</span></span>          | <span data-ttu-id="ba61d-716">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="ba61d-716">Spatial System Reference Identifier.</span></span> <span data-ttu-id="ba61d-717">空間型のパラメーターに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-717">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="ba61d-718">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-718">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-719">**パラメーター**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-719">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="ba61d-720">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-720">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-721">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-721">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="ba61d-722">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-722">Example</span></span>

<span data-ttu-id="ba61d-723">次の例は、1つの**Parameter**子要素を持つ**FunctionImport**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-723">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="ba61d-724">関数は、1 つの入力パラメーターを受け取り、エンティティ型のコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-724">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="ba61d-725">Function 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="ba61d-725">Function Element Application</span></span>

<span data-ttu-id="ba61d-726">**Parameter**要素 ( **Function**要素の子として) は、概念モデルで定義または宣言されている関数のパラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-726">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="ba61d-727">**Parameter**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-727">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-728">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-728">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="ba61d-729">CollectionType (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-729">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="ba61d-730">ReferenceType (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-730">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="ba61d-731">RowType (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-731">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="ba61d-732">**CollectionType**、 **ReferenceType**、または**RowType**要素のいずれか1つだけを**Property**要素の子要素にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-732">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="ba61d-733">Annotation 要素 (0 個以上の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="ba61d-733">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="ba61d-734">Annotation 要素は、それ以外のすべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-734">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="ba61d-735">Annotation 要素は、CSDL v2 以降でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-735">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-736">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-736">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-737">次の表では、 **Parameter**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-737">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="ba61d-738">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-738">Attribute Name</span></span>   | <span data-ttu-id="ba61d-739">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-739">Is Required</span></span> | <span data-ttu-id="ba61d-740">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-740">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-741">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-741">**Name**</span></span>         | <span data-ttu-id="ba61d-742">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-742">Yes</span></span>         | <span data-ttu-id="ba61d-743">パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-743">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="ba61d-744">**Type**</span><span class="sxs-lookup"><span data-stu-id="ba61d-744">**Type**</span></span>         | <span data-ttu-id="ba61d-745">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-745">No</span></span>          | <span data-ttu-id="ba61d-746">パラメーターの型。</span><span class="sxs-lookup"><span data-stu-id="ba61d-746">The parameter type.</span></span> <span data-ttu-id="ba61d-747">パラメーターには次のいずれかの型 (またはこれらの型のコレクション) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-747">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="ba61d-748">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="ba61d-748">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="ba61d-749">エンティティ型</span><span class="sxs-lookup"><span data-stu-id="ba61d-749">entity type</span></span> <br/> <span data-ttu-id="ba61d-750">複合型</span><span class="sxs-lookup"><span data-stu-id="ba61d-750">complex type</span></span> <br/> <span data-ttu-id="ba61d-751">行型</span><span class="sxs-lookup"><span data-stu-id="ba61d-751">row type</span></span> <br/> <span data-ttu-id="ba61d-752">参照型</span><span class="sxs-lookup"><span data-stu-id="ba61d-752">reference type</span></span>                             |
| <span data-ttu-id="ba61d-753">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="ba61d-753">**Nullable**</span></span>     | <span data-ttu-id="ba61d-754">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-754">No</span></span>          | <span data-ttu-id="ba61d-755">プロパティが**null**値を持つことができるかどうかによって、 **True** (既定値) または**False** 。</span><span class="sxs-lookup"><span data-stu-id="ba61d-755">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="ba61d-756">**既定**</span><span class="sxs-lookup"><span data-stu-id="ba61d-756">**DefaultValue**</span></span> | <span data-ttu-id="ba61d-757">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-757">No</span></span>          | <span data-ttu-id="ba61d-758">プロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-758">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="ba61d-759">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-759">**MaxLength**</span></span>    | <span data-ttu-id="ba61d-760">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-760">No</span></span>          | <span data-ttu-id="ba61d-761">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="ba61d-761">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="ba61d-762">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-762">**FixedLength**</span></span>  | <span data-ttu-id="ba61d-763">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-763">No</span></span>          | <span data-ttu-id="ba61d-764">プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-764">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="ba61d-765">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="ba61d-765">**Precision**</span></span>    | <span data-ttu-id="ba61d-766">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-766">No</span></span>          | <span data-ttu-id="ba61d-767">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="ba61d-767">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="ba61d-768">**スケール**</span><span class="sxs-lookup"><span data-stu-id="ba61d-768">**Scale**</span></span>        | <span data-ttu-id="ba61d-769">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-769">No</span></span>          | <span data-ttu-id="ba61d-770">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="ba61d-770">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="ba61d-771">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ba61d-771">**SRID**</span></span>         | <span data-ttu-id="ba61d-772">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-772">No</span></span>          | <span data-ttu-id="ba61d-773">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="ba61d-773">Spatial System Reference Identifier.</span></span> <span data-ttu-id="ba61d-774">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-774">Valid only for properties of spatial types.</span></span> <span data-ttu-id="ba61d-775">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-775">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="ba61d-776">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="ba61d-776">**Unicode**</span></span>      | <span data-ttu-id="ba61d-777">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-777">No</span></span>          | <span data-ttu-id="ba61d-778">プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-778">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="ba61d-779">**Collation**</span><span class="sxs-lookup"><span data-stu-id="ba61d-779">**Collation**</span></span>    | <span data-ttu-id="ba61d-780">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-780">No</span></span>          | <span data-ttu-id="ba61d-781">データ ソースで使用される照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="ba61d-781">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="ba61d-782">**パラメーター**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-782">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="ba61d-783">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-783">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-784">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-784">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="ba61d-785">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-785">Example</span></span>

<span data-ttu-id="ba61d-786">次の例は、1つの**parameter**子要素を使用して関数パラメーターを定義する**関数**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-786">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a><span data-ttu-id="ba61d-787">Principal 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-787">Principal Element (CSDL)</span></span>

<span data-ttu-id="ba61d-788">概念スキーマ定義言語 (CSDL) の**principal**要素は、参照制約のプリンシパル end を定義する、参照制約要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-788">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="ba61d-789">参照整合性の**制約要素は、リレーショナル**データベースの参照整合性制約に似た機能を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-789">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="ba61d-790">データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティは別のエンティティ型のエンティティ キーを参照できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-790">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="ba61d-791">参照されるエンティティ型は、制約の*プリンシパル end*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-791">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="ba61d-792">プリンシパル end を参照するエンティティ型は、制約の*依存 end*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-792">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="ba61d-793">**Propertyref**要素は、依存 end によって参照されるキーを指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-793">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="ba61d-794">**Principal**要素には、次の子要素を含めることができます (順に記載されています)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-794">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-795">PropertyRef (1 つ以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-795">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="ba61d-796">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-796">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-797">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-797">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-798">次の表では、 **Principal**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-798">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="ba61d-799">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-799">Attribute Name</span></span> | <span data-ttu-id="ba61d-800">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-800">Is Required</span></span> | <span data-ttu-id="ba61d-801">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-801">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="ba61d-802">**ロール**</span><span class="sxs-lookup"><span data-stu-id="ba61d-802">**Role**</span></span>       | <span data-ttu-id="ba61d-803">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-803">Yes</span></span>         | <span data-ttu-id="ba61d-804">アソシエーションのプリンシパル End 上のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-804">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-805">**Principal**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-805">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="ba61d-806">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-806">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-807">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-807">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-808">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-808">Example</span></span>

<span data-ttu-id="ba61d-809">次の例では、 **publishedby**関連付けの定義の一部である **、オブジェクトの場所を示し**ます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-809">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="ba61d-810">参照に関する制約のプリンシパル end は、**パブリッシャー**エンティティ型の**Id**プロパティによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-810">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

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
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="ba61d-811">Property 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-811">Property Element (CSDL)</span></span>

<span data-ttu-id="ba61d-812">概念スキーマ定義言語 (CSDL) の**Property**要素は、EntityType 要素、ComplexType 要素、または RowType 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-812">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="ba61d-813">EntityType 要素と ComplexType 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="ba61d-813">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="ba61d-814">**プロパティ**要素 ( **EntityType**または**ComplexType**要素の子として) は、エンティティ型インスタンスまたは複合型インスタンスに格納されるデータの構造と特性を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-814">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="ba61d-815">概念モデルのプロパティは、クラスで定義されるプロパティに似ています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-815">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="ba61d-816">クラスのプロパティがクラスの構造を定義し、オブジェクトに関する情報を伝達するのと同様に、概念モデルのプロパティはエンティティ型の構造を定義し、エンティティ型のインスタンスに関する情報を伝達します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-816">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="ba61d-817">**Property**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-817">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-818">Documentation 要素 (0 個または1個の要素が許可されています)</span><span class="sxs-lookup"><span data-stu-id="ba61d-818">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="ba61d-819">Annotation 要素 (0 個以上の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="ba61d-819">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="ba61d-820">次のファセットは、**プロパティ**要素に適用できます。 **Nullable**、 **DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**、 **Collation**、 **ConcurrencyMode**。</span><span class="sxs-lookup"><span data-stu-id="ba61d-820">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="ba61d-821">ファセットは、プロパティ値をデータ ストアに格納する方法に関する情報を提供する XML 属性です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-821">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="ba61d-822">ファセットは、 **Edmsimpletype**型のプロパティにのみ適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-822">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-823">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-823">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-824">次の表では、 **Property**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-824">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="ba61d-825">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-825">Attribute Name</span></span>                                                         | <span data-ttu-id="ba61d-826">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-826">Is Required</span></span> | <span data-ttu-id="ba61d-827">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-827">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-828">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-828">**Name**</span></span>                                                               | <span data-ttu-id="ba61d-829">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-829">Yes</span></span>         | <span data-ttu-id="ba61d-830">プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-830">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="ba61d-831">**Type**</span><span class="sxs-lookup"><span data-stu-id="ba61d-831">**Type**</span></span>                                                               | <span data-ttu-id="ba61d-832">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-832">Yes</span></span>         | <span data-ttu-id="ba61d-833">プロパティ値の型です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-833">The type of the property value.</span></span> <span data-ttu-id="ba61d-834">プロパティ値の型は、 **EDMSimpleType** か、モデルのスコープ内にある (完全修飾名で表された) 複合型を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-834">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="ba61d-835">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="ba61d-835">**Nullable**</span></span>                                                           | <span data-ttu-id="ba61d-836">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-836">No</span></span>          | <span data-ttu-id="ba61d-837">プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または <strong>False</strong>。</span><span class="sxs-lookup"><span data-stu-id="ba61d-837">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="ba61d-838">CSDL v1 の > 複合型プロパティには、`Nullable="False"`が必要です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-838">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="ba61d-839">**既定**</span><span class="sxs-lookup"><span data-stu-id="ba61d-839">**DefaultValue**</span></span>                                                       | <span data-ttu-id="ba61d-840">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-840">No</span></span>          | <span data-ttu-id="ba61d-841">プロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-841">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="ba61d-842">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-842">**MaxLength**</span></span>                                                          | <span data-ttu-id="ba61d-843">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-843">No</span></span>          | <span data-ttu-id="ba61d-844">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="ba61d-844">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="ba61d-845">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-845">**FixedLength**</span></span>                                                        | <span data-ttu-id="ba61d-846">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-846">No</span></span>          | <span data-ttu-id="ba61d-847">プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-847">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="ba61d-848">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="ba61d-848">**Precision**</span></span>                                                          | <span data-ttu-id="ba61d-849">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-849">No</span></span>          | <span data-ttu-id="ba61d-850">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="ba61d-850">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="ba61d-851">**スケール**</span><span class="sxs-lookup"><span data-stu-id="ba61d-851">**Scale**</span></span>                                                              | <span data-ttu-id="ba61d-852">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-852">No</span></span>          | <span data-ttu-id="ba61d-853">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="ba61d-853">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="ba61d-854">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ba61d-854">**SRID**</span></span>                                                               | <span data-ttu-id="ba61d-855">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-855">No</span></span>          | <span data-ttu-id="ba61d-856">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="ba61d-856">Spatial System Reference Identifier.</span></span> <span data-ttu-id="ba61d-857">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-857">Valid only for properties of spatial types.</span></span> <span data-ttu-id="ba61d-858">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-858">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="ba61d-859">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="ba61d-859">**Unicode**</span></span>                                                            | <span data-ttu-id="ba61d-860">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-860">No</span></span>          | <span data-ttu-id="ba61d-861">プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-861">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="ba61d-862">**Collation**</span><span class="sxs-lookup"><span data-stu-id="ba61d-862">**Collation**</span></span>                                                          | <span data-ttu-id="ba61d-863">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-863">No</span></span>          | <span data-ttu-id="ba61d-864">データ ソースで使用される照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="ba61d-864">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="ba61d-865">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="ba61d-865">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="ba61d-866">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-866">No</span></span>          | <span data-ttu-id="ba61d-867">**None** (既定値) または **Fixed**。</span><span class="sxs-lookup"><span data-stu-id="ba61d-867">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="ba61d-868">値が **Fixed**に設定されている場合、プロパティ値はオプティミスティック コンカレンシーチェックで使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-868">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="ba61d-869">**プロパティ**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-869">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="ba61d-870">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-870">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-871">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-871">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="ba61d-872">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-872">Example</span></span>

<span data-ttu-id="ba61d-873">次の例は、3つの**Property**要素を持つ**EntityType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-873">The following example shows an **EntityType** element with three **Property** elements:</span></span>

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
 

<span data-ttu-id="ba61d-874">次の例は、5つの**Property**要素を持つ**ComplexType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-874">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="ba61d-875">RowType 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="ba61d-875">RowType Element Application</span></span>

<span data-ttu-id="ba61d-876">**RowType**要素の子として使用される**プロパティ**要素は、モデル定義関数に渡される、またはモデル定義関数から返されるデータの構造と特性を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-876">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="ba61d-877">**Property**要素には、次の子要素のうち1つだけを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-877">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="ba61d-878">CollectionType</span><span class="sxs-lookup"><span data-stu-id="ba61d-878">CollectionType</span></span>
-   <span data-ttu-id="ba61d-879">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="ba61d-879">ReferenceType</span></span>
-   <span data-ttu-id="ba61d-880">RowType</span><span class="sxs-lookup"><span data-stu-id="ba61d-880">RowType</span></span>

<span data-ttu-id="ba61d-881">**Property**要素は、任意の数の子注釈要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-881">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="ba61d-882">Annotation 要素は、CSDL v2 以降でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-882">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-883">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-883">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-884">次の表では、 **Property**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-884">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="ba61d-885">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-885">Attribute Name</span></span>                                                     | <span data-ttu-id="ba61d-886">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-886">Is Required</span></span> | <span data-ttu-id="ba61d-887">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-887">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-888">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-888">**Name**</span></span>                                                           | <span data-ttu-id="ba61d-889">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-889">Yes</span></span>         | <span data-ttu-id="ba61d-890">プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-890">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="ba61d-891">**Type**</span><span class="sxs-lookup"><span data-stu-id="ba61d-891">**Type**</span></span>                                                           | <span data-ttu-id="ba61d-892">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-892">Yes</span></span>         | <span data-ttu-id="ba61d-893">プロパティ値の型です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-893">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="ba61d-894">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="ba61d-894">**Nullable**</span></span>                                                       | <span data-ttu-id="ba61d-895">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-895">No</span></span>          | <span data-ttu-id="ba61d-896">プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または **False**。</span><span class="sxs-lookup"><span data-stu-id="ba61d-896">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="ba61d-897">CSDL v1 で > 複合型プロパティには、`Nullable="False"`が必要です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-897">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="ba61d-898">**既定**</span><span class="sxs-lookup"><span data-stu-id="ba61d-898">**DefaultValue**</span></span>                                                   | <span data-ttu-id="ba61d-899">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-899">No</span></span>          | <span data-ttu-id="ba61d-900">プロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-900">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="ba61d-901">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-901">**MaxLength**</span></span>                                                      | <span data-ttu-id="ba61d-902">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-902">No</span></span>          | <span data-ttu-id="ba61d-903">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="ba61d-903">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="ba61d-904">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-904">**FixedLength**</span></span>                                                    | <span data-ttu-id="ba61d-905">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-905">No</span></span>          | <span data-ttu-id="ba61d-906">プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-906">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="ba61d-907">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="ba61d-907">**Precision**</span></span>                                                      | <span data-ttu-id="ba61d-908">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-908">No</span></span>          | <span data-ttu-id="ba61d-909">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="ba61d-909">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="ba61d-910">**スケール**</span><span class="sxs-lookup"><span data-stu-id="ba61d-910">**Scale**</span></span>                                                          | <span data-ttu-id="ba61d-911">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-911">No</span></span>          | <span data-ttu-id="ba61d-912">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="ba61d-912">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="ba61d-913">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ba61d-913">**SRID**</span></span>                                                           | <span data-ttu-id="ba61d-914">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-914">No</span></span>          | <span data-ttu-id="ba61d-915">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="ba61d-915">Spatial System Reference Identifier.</span></span> <span data-ttu-id="ba61d-916">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-916">Valid only for properties of spatial types.</span></span> <span data-ttu-id="ba61d-917">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-917">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="ba61d-918">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="ba61d-918">**Unicode**</span></span>                                                        | <span data-ttu-id="ba61d-919">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-919">No</span></span>          | <span data-ttu-id="ba61d-920">プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-920">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="ba61d-921">**Collation**</span><span class="sxs-lookup"><span data-stu-id="ba61d-921">**Collation**</span></span>                                                      | <span data-ttu-id="ba61d-922">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-922">No</span></span>          | <span data-ttu-id="ba61d-923">データ ソースで使用される照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="ba61d-923">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="ba61d-924">**プロパティ**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-924">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="ba61d-925">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-925">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-926">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-926">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="ba61d-927">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-927">Example</span></span>

<span data-ttu-id="ba61d-928">次の例は、モデル定義関数の戻り値の型の形状を定義するために使用される**プロパティ**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-928">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

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
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="ba61d-929">PropertyRef 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-929">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="ba61d-930">概念スキーマ定義言語 (CSDL) の**Propertyref**要素は、プロパティが次のいずれかのロールを実行することを示すために、エンティティ型のプロパティを参照します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-930">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="ba61d-931">エンティティのキー (ID を指定するエンティティ型のプロパティまたは一連のプロパティ) の一部。</span><span class="sxs-lookup"><span data-stu-id="ba61d-931">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="ba61d-932">1つ以上の**Propertyref**要素を使用して、エンティティキーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-932">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="ba61d-933">参照制約の依存 End または プリンシパル End。</span><span class="sxs-lookup"><span data-stu-id="ba61d-933">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="ba61d-934">**Propertyref**要素には、子要素として annotation 要素 (0 個以上) のみを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-934">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="ba61d-935">Annotation 要素は、CSDL v2 以降でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-935">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-936">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-936">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-937">次の表では、 **Propertyref**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-937">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="ba61d-938">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-938">Attribute Name</span></span> | <span data-ttu-id="ba61d-939">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-939">Is Required</span></span> | <span data-ttu-id="ba61d-940">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-940">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="ba61d-941">**名前**</span><span class="sxs-lookup"><span data-stu-id="ba61d-941">**Name**</span></span>       | <span data-ttu-id="ba61d-942">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-942">Yes</span></span>         | <span data-ttu-id="ba61d-943">参照されているプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-943">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-944">**Propertyref**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-944">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="ba61d-945">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-945">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-946">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-946">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-947">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-947">Example</span></span>

<span data-ttu-id="ba61d-948">次の例では、エンティティ型 (**Book**) を定義しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-948">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="ba61d-949">エンティティキーは、エンティティ型の**ISBN**プロパティを参照することによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-949">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="ba61d-950">次の例では、2つの**Propertyref**要素を使用して、2つのプロパティ (**Id**と**PublisherId**) が参照に関する制約のプリンシパルと依存 end であることを示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-950">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

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
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="ba61d-951">ReferenceType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-951">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="ba61d-952">概念スキーマ定義言語 (CSDL) の**ReferenceType**要素は、エンティティ型への参照を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-952">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="ba61d-953">**ReferenceType**要素は、次の要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-953">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="ba61d-954">ReturnType (関数)</span><span class="sxs-lookup"><span data-stu-id="ba61d-954">ReturnType (Function)</span></span>
-   <span data-ttu-id="ba61d-955">パラメーター</span><span class="sxs-lookup"><span data-stu-id="ba61d-955">Parameter</span></span>
-   <span data-ttu-id="ba61d-956">CollectionType</span><span class="sxs-lookup"><span data-stu-id="ba61d-956">CollectionType</span></span>

<span data-ttu-id="ba61d-957">**ReferenceType**要素は、関数のパラメーターまたは戻り値の型を定義するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-957">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="ba61d-958">**ReferenceType**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-958">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-959">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-959">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-960">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-960">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-961">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-961">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-962">次の表は、 **ReferenceType**要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-962">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="ba61d-963">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-963">Attribute Name</span></span> | <span data-ttu-id="ba61d-964">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-964">Is Required</span></span> | <span data-ttu-id="ba61d-965">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-965">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="ba61d-966">**Type**</span><span class="sxs-lookup"><span data-stu-id="ba61d-966">**Type**</span></span>       | <span data-ttu-id="ba61d-967">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-967">Yes</span></span>         | <span data-ttu-id="ba61d-968">参照先エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-968">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-969">**ReferenceType**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-969">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="ba61d-970">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-970">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-971">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-971">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-972">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-972">Example</span></span>

<span data-ttu-id="ba61d-973">次の例は、 **Person**エンティティ型への参照を受け入れるモデル定義関数で、 **Parameter**要素の子として使用される**ReferenceType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-973">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

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
 

<span data-ttu-id="ba61d-974">次の例は、 **Person**エンティティ型への参照を返すモデル定義関数で、 **ReturnType** (Function) 要素の子として使用される**ReferenceType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-974">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

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
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="ba61d-975">ReferentialConstraint 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-975">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="ba61d-976">概念スキーマ定義言語 (CSDL) の参照整合性制約要素は、リレーショナルデータベースの参照整合性制約に似た機能を**定義します**。</span><span class="sxs-lookup"><span data-stu-id="ba61d-976">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="ba61d-977">データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティは別のエンティティ型のエンティティ キーを参照できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-977">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="ba61d-978">参照されるエンティティ型は、制約の*プリンシパル end*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-978">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="ba61d-979">プリンシパル end を参照するエンティティ型は、制約の*依存 end*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-979">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="ba61d-980">あるエンティティ型で公開されている外部キーが別のエンティティ型のプロパティを参照している場合、参照要素は2つのエンティティ型の間の関連付けを**定義します**。</span><span class="sxs-lookup"><span data-stu-id="ba61d-980">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="ba61d-981">参照要素**は2つのエンティティ**型の関連付けに関する情報を提供するため、対応する AssociationSetMapping 要素はマッピングスキーマ言語 (MSL) には必要ありません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-981">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="ba61d-982">外部キーが公開されていない2つのエンティティ型間のアソシエーションには、関連情報をデータソースにマップするために、対応する**AssociationSetMapping**要素が必要です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-982">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="ba61d-983">エンティティ型で外部キーが公開されていない場合**は、エンティティ**型と別のエンティティ型との間の主キーと主キーの制約のみを指定できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-983">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="ba61d-984">要素には、次の子**要素を含める**ことができます (順に表示されます)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-984">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-985">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-985">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-986">Principal (1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-986">Principal (exactly one element)</span></span>
-   <span data-ttu-id="ba61d-987">Dependent (1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-987">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="ba61d-988">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-988">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-989">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-989">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-990">この**要素には、** 任意の数の annotation 属性 (カスタム XML 属性) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-990">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="ba61d-991">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-991">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-992">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-992">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="ba61d-993">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-993">Example</span></span>

<span data-ttu-id="ba61d-994">次の例では、 **publishedby**関連付けの定義の一部として使用されている **、オブジェクトの**場所を示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-994">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

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
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="ba61d-995">ReturnType (Function) 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-995">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="ba61d-996">概念スキーマ定義言語 (CSDL) の**ReturnType** (function) 要素は、function 要素で定義されている関数の戻り値の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-996">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="ba61d-997">関数の戻り値の型は、 **ReturnType**属性と共に指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-997">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="ba61d-998">戻り値の型には、 **Edmsimpletype**、エンティティ型、複合型、行型、参照型、またはこれらの型のいずれかのコレクションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-998">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="ba61d-999">関数の戻り値の型は、 **ReturnType** (function) 要素の**type**属性か、次のいずれかの子要素を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-999">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="ba61d-1000">CollectionType</span><span class="sxs-lookup"><span data-stu-id="ba61d-1000">CollectionType</span></span>
-   <span data-ttu-id="ba61d-1001">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="ba61d-1001">ReferenceType</span></span>
-   <span data-ttu-id="ba61d-1002">RowType</span><span class="sxs-lookup"><span data-stu-id="ba61d-1002">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="ba61d-1003">**ReturnType** (function) 要素の**type**属性と子要素の両方を持つ関数の戻り値の型を指定した場合、モデルは検証されません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1003">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-1004">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-1004">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-1005">次の表では、 **ReturnType** (Function) 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1005">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="ba61d-1006">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-1006">Attribute Name</span></span> | <span data-ttu-id="ba61d-1007">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-1007">Is Required</span></span> | <span data-ttu-id="ba61d-1008">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-1008">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="ba61d-1009">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1009">**ReturnType**</span></span> | <span data-ttu-id="ba61d-1010">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1010">No</span></span>          | <span data-ttu-id="ba61d-1011">関数の戻り値の型。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1011">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-1012">任意の数の annotation 属性 (カスタム XML 属性) を**ReturnType** (Function) 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1012">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="ba61d-1013">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1013">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-1014">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1014">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-1015">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-1015">Example</span></span>

<span data-ttu-id="ba61d-1016">次の例では、**関数**要素を使用して、書籍が印刷された年数を返す関数を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1016">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="ba61d-1017">戻り値の型は、 **ReturnType** (Function) 要素の**type**属性によって指定されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1017">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="ba61d-1018">ReturnType (FunctionImport) 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1018">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="ba61d-1019">概念スキーマ定義言語 (CSDL) の**ReturnType** (FunctionImport) 要素は、FunctionImport 要素で定義されている関数の戻り値の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1019">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="ba61d-1020">関数の戻り値の型は、 **ReturnType**属性と共に指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1020">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="ba61d-1021">戻り値の型は、エンティティ型、複合型、または**Edmsimpletype**の任意のコレクションにすることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1021">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="ba61d-1022">関数の戻り値の型は、 **ReturnType** (FunctionImport) 要素の**type**属性と共に指定されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1022">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-1023">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-1023">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-1024">次の表では、 **ReturnType** (FunctionImport) 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1024">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="ba61d-1025">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-1025">Attribute Name</span></span> | <span data-ttu-id="ba61d-1026">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-1026">Is Required</span></span> | <span data-ttu-id="ba61d-1027">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-1027">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-1028">**Type**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1028">**Type**</span></span>       | <span data-ttu-id="ba61d-1029">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1029">No</span></span>          | <span data-ttu-id="ba61d-1030">関数が返す型。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1030">The type that the function returns.</span></span> <span data-ttu-id="ba61d-1031">値は、ComplexType、EntityType、または EDMSimpleType のコレクションである必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1031">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="ba61d-1032">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1032">**EntitySet**</span></span>  | <span data-ttu-id="ba61d-1033">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1033">No</span></span>          | <span data-ttu-id="ba61d-1034">関数がエンティティ型のコレクションを返す場合、 **EntitySet**の値は、コレクションが属するエンティティセットである必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1034">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="ba61d-1035">そうでない場合は、 **EntitySet** 属性を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1035">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-1036">任意の数の annotation 属性 (カスタム XML 属性) を**ReturnType** (FunctionImport) 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1036">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="ba61d-1037">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1037">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-1038">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1038">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-1039">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-1039">Example</span></span>

<span data-ttu-id="ba61d-1040">次の例では、書籍と出版社を返す**FunctionImport**を使用します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1040">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="ba61d-1041">関数は2つの結果セットを返すため、2つの**ReturnType** (FunctionImport) 要素が指定されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1041">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="ba61d-1042">RowType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1042">RowType Element (CSDL)</span></span>

<span data-ttu-id="ba61d-1043">概念スキーマ定義言語 (CSDL) の**RowType**要素は、概念モデルで定義されている関数のパラメーターまたは戻り値の型として名前のない構造体を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1043">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="ba61d-1044">**RowType**要素は、次の要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1044">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="ba61d-1045">CollectionType</span><span class="sxs-lookup"><span data-stu-id="ba61d-1045">CollectionType</span></span>
-   <span data-ttu-id="ba61d-1046">パラメーター</span><span class="sxs-lookup"><span data-stu-id="ba61d-1046">Parameter</span></span>
-   <span data-ttu-id="ba61d-1047">ReturnType (関数)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1047">ReturnType (Function)</span></span>

<span data-ttu-id="ba61d-1048">**RowType**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1048">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-1049">Property (1 個以上)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1049">Property (one or more)</span></span>
-   <span data-ttu-id="ba61d-1050">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1050">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-1051">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-1051">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-1052">**RowType**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1052">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="ba61d-1053">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1053">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-1054">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1054">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="ba61d-1055">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-1055">Example</span></span>

<span data-ttu-id="ba61d-1056">次の例は、 **CollectionType**要素を使用して、 **RowType**要素で指定された行のコレクションを返すことを指定するモデル定義関数を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1056">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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

## <a name="schema-element-csdl"></a><span data-ttu-id="ba61d-1057">Schema 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1057">Schema Element (CSDL)</span></span>

<span data-ttu-id="ba61d-1058">**スキーマ**要素は、概念モデル定義のルート要素です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1058">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="ba61d-1059">概念モデルを構成するオブジェクト、関数、およびコンテナーの定義を格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1059">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="ba61d-1060">**Schema**要素には、次の子要素を0個以上含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1060">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="ba61d-1061">使用</span><span class="sxs-lookup"><span data-stu-id="ba61d-1061">Using</span></span>
-   <span data-ttu-id="ba61d-1062">EntityContainer 要素</span><span class="sxs-lookup"><span data-stu-id="ba61d-1062">EntityContainer</span></span>
-   <span data-ttu-id="ba61d-1063">EntityType</span><span class="sxs-lookup"><span data-stu-id="ba61d-1063">EntityType</span></span>
-   <span data-ttu-id="ba61d-1064">EnumType</span><span class="sxs-lookup"><span data-stu-id="ba61d-1064">EnumType</span></span>
-   <span data-ttu-id="ba61d-1065">アソシエーション</span><span class="sxs-lookup"><span data-stu-id="ba61d-1065">Association</span></span>
-   <span data-ttu-id="ba61d-1066">ComplexType 要素</span><span class="sxs-lookup"><span data-stu-id="ba61d-1066">ComplexType</span></span>
-   <span data-ttu-id="ba61d-1067">Function</span><span class="sxs-lookup"><span data-stu-id="ba61d-1067">Function</span></span>

<span data-ttu-id="ba61d-1068">**スキーマ**要素には、0個または1個の Annotation 要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1068">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="ba61d-1069">**Function**要素と annotation 要素は、CSDL v2 以降でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1069">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="ba61d-1070">**Schema**要素は、**名前空間**属性を使用して、概念モデルのエンティティ型、複合型、およびアソシエーションオブジェクトの名前空間を定義します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1070">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="ba61d-1071">1 つの名前空間内で 2 つのオブジェクトが同じ名前を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1071">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="ba61d-1072">名前空間は、複数の**スキーマ**要素と複数の .csdl ファイルにまたがることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1072">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="ba61d-1073">概念モデルの名前空間は、**スキーマ**要素の XML 名前空間とは異なります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1073">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="ba61d-1074">概念モデルの名前空間 (**名前空間**属性で定義) は、エンティティ型、複合型、およびアソシエーション型の論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1074">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="ba61d-1075">**Schema**要素の XML 名前空間 ( **xmlns**属性で示されます) は、 **schema**要素の子要素と属性の既定の名前空間です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1075">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="ba61d-1076">https://schemas.microsoft.com/ado/YYYY/MM/edm 形式の XML 名前空間 (YYYY と MM はそれぞれ年と月を表します) は CSDL 用に予約されています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1076">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="ba61d-1077">カスタム要素と属性は、このフォームがある名前空間に存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1077">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-1078">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-1078">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-1079">次の表では、**スキーマ**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1079">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="ba61d-1080">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-1080">Attribute Name</span></span> | <span data-ttu-id="ba61d-1081">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-1081">Is Required</span></span> | <span data-ttu-id="ba61d-1082">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-1082">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-1083">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1083">**Namespace**</span></span>  | <span data-ttu-id="ba61d-1084">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1084">Yes</span></span>         | <span data-ttu-id="ba61d-1085">概念モデルの名前空間。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1085">The namespace of the conceptual model.</span></span> <span data-ttu-id="ba61d-1086">**名前空間**属性の値は、型の完全修飾名を形成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1086">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="ba61d-1087">たとえば、 *Customer*という名前の**entitytype**が Simple. example. Model 名前空間にある場合、 **entitytype**の完全修飾名は simpleexamplemodel.customer です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1087">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="ba61d-1088">次の文字列は、 **Namespace**属性の値として使用できません: **System**、 **Transient**、または**Edm**。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1088">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="ba61d-1089">**Namespace**属性の値を、SSDL Schema 要素の**namespace**属性の値と同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1089">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="ba61d-1090">**エイリアス**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1090">**Alias**</span></span>      | <span data-ttu-id="ba61d-1091">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1091">No</span></span>          | <span data-ttu-id="ba61d-1092">名前空間の名前の代わりに使用される識別子。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1092">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="ba61d-1093">たとえば、 *Customer*という名前の**entitytype**が Simple. example. model 名前空間にあり、 **Alias**属性の値が*model*の場合、 **entitytype**の完全修飾名として model. Customer を使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1093">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="ba61d-1094">**スキーマ**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1094">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="ba61d-1095">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1095">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-1096">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1096">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-1097">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-1097">Example</span></span>

<span data-ttu-id="ba61d-1098">**EntityContainer**要素、2つの**EntityType**要素、および1つの**Association**要素を含む**スキーマ**要素の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1098">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="ba61d-1099">TypeRef 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1099">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="ba61d-1100">概念スキーマ定義言語 (CSDL) の**TypeRef**要素は、既存の名前付きの型への参照を提供します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1100">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="ba61d-1101">**TypeRef**要素は CollectionType 要素の子にすることができます。これは、関数がパラメーターまたは戻り値の型としてコレクションを持つことを指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1101">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="ba61d-1102">**TypeRef**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1102">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="ba61d-1103">Documentation (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1103">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="ba61d-1104">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1104">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-1105">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-1105">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-1106">次の表では、 **TypeRef**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1106">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="ba61d-1107">**DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**、および**Collation**属性は、 **edmsimpletypes**にのみ適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1107">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="ba61d-1108">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-1108">Attribute Name</span></span>                                                     | <span data-ttu-id="ba61d-1109">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-1109">Is Required</span></span> | <span data-ttu-id="ba61d-1110">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-1110">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-1111">**Type**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1111">**Type**</span></span>                                                           | <span data-ttu-id="ba61d-1112">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1112">No</span></span>          | <span data-ttu-id="ba61d-1113">参照先の型の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1113">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="ba61d-1114">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1114">**Nullable**</span></span>                                                       | <span data-ttu-id="ba61d-1115">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1115">No</span></span>          | <span data-ttu-id="ba61d-1116">プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または **False**。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1116">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="ba61d-1117">CSDL v1 で > 複合型プロパティには、`Nullable="False"`が必要です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1117">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="ba61d-1118">**既定**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1118">**DefaultValue**</span></span>                                                   | <span data-ttu-id="ba61d-1119">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1119">No</span></span>          | <span data-ttu-id="ba61d-1120">プロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1120">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="ba61d-1121">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1121">**MaxLength**</span></span>                                                      | <span data-ttu-id="ba61d-1122">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1122">No</span></span>          | <span data-ttu-id="ba61d-1123">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1123">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="ba61d-1124">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1124">**FixedLength**</span></span>                                                    | <span data-ttu-id="ba61d-1125">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1125">No</span></span>          | <span data-ttu-id="ba61d-1126">プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1126">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="ba61d-1127">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1127">**Precision**</span></span>                                                      | <span data-ttu-id="ba61d-1128">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1128">No</span></span>          | <span data-ttu-id="ba61d-1129">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1129">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="ba61d-1130">**スケール**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1130">**Scale**</span></span>                                                          | <span data-ttu-id="ba61d-1131">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1131">No</span></span>          | <span data-ttu-id="ba61d-1132">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1132">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="ba61d-1133">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1133">**SRID**</span></span>                                                           | <span data-ttu-id="ba61d-1134">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1134">No</span></span>          | <span data-ttu-id="ba61d-1135">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1135">Spatial System Reference Identifier.</span></span> <span data-ttu-id="ba61d-1136">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1136">Valid only for properties of spatial types.</span></span> <span data-ttu-id="ba61d-1137">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1137">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="ba61d-1138">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1138">**Unicode**</span></span>                                                        | <span data-ttu-id="ba61d-1139">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1139">No</span></span>          | <span data-ttu-id="ba61d-1140">プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1140">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="ba61d-1141">**Collation**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1141">**Collation**</span></span>                                                      | <span data-ttu-id="ba61d-1142">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1142">No</span></span>          | <span data-ttu-id="ba61d-1143">データ ソースで使用される照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1143">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="ba61d-1144">任意の数の annotation 属性 (カスタム XML 属性) を**CollectionType**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1144">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="ba61d-1145">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1145">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-1146">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1146">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-1147">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-1147">Example</span></span>

<span data-ttu-id="ba61d-1148">次の例は、 **TypeRef**要素 ( **CollectionType**要素の子) を使用して、関数が**Department**エンティティ型のコレクションを受け入れることを指定するモデル定義関数を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1148">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

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
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="ba61d-1149">Using 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1149">Using Element (CSDL)</span></span>

<span data-ttu-id="ba61d-1150">概念スキーマ定義言語 (CSDL) の**Using**要素は、別の名前空間に存在する概念モデルの内容をインポートします。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1150">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="ba61d-1151">**名前空間**属性の値を設定することにより、別の概念モデルで定義されているエンティティ型、複合型、およびアソシエーション型を参照できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1151">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="ba61d-1152">複数の**Using**要素を**Schema**要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1152">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="ba61d-1153">CSDL の**using**要素は、プログラミング言語での**using**ステートメントとまったく同じようには機能しません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1153">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="ba61d-1154">プログラミング言語で**using**ステートメントを使用して名前空間をインポートすると、元の名前空間のオブジェクトには影響しません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1154">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="ba61d-1155">CSDL では、インポートされた名前空間に、元の名前空間にあるエンティティ型から派生したエンティティ型が含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1155">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="ba61d-1156">これは、元の名前空間で宣言されたエンティティ セットに影響を及ぼすことがあります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1156">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="ba61d-1157">**Using**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1157">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="ba61d-1158">Documentation (0 個または1個の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1158">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="ba61d-1159">Annotation 要素 (0 個以上の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1159">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="ba61d-1160">該当する属性</span><span class="sxs-lookup"><span data-stu-id="ba61d-1160">Applicable Attributes</span></span>

<span data-ttu-id="ba61d-1161">次の表では、 **Using**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1161">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="ba61d-1162">属性名</span><span class="sxs-lookup"><span data-stu-id="ba61d-1162">Attribute Name</span></span> | <span data-ttu-id="ba61d-1163">必須</span><span class="sxs-lookup"><span data-stu-id="ba61d-1163">Is Required</span></span> | <span data-ttu-id="ba61d-1164">値</span><span class="sxs-lookup"><span data-stu-id="ba61d-1164">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-1165">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1165">**Namespace**</span></span>  | <span data-ttu-id="ba61d-1166">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1166">Yes</span></span>         | <span data-ttu-id="ba61d-1167">インポートされる名前空間の名前。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1167">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="ba61d-1168">**エイリアス**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1168">**Alias**</span></span>      | <span data-ttu-id="ba61d-1169">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1169">Yes</span></span>         | <span data-ttu-id="ba61d-1170">名前空間の名前の代わりに使用される識別子。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1170">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="ba61d-1171">この属性は必須ですが、オブジェクト名を修飾するために名前空間名の代わりに使用することは必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1171">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="ba61d-1172">任意の数の annotation 属性 (カスタム XML 属性) を**Using**要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1172">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="ba61d-1173">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="ba61d-1174">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="ba61d-1175">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-1175">Example</span></span>

<span data-ttu-id="ba61d-1176">次の例は、他の場所で定義されている名前空間をインポートするために使用される**Using**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1176">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="ba61d-1177">表示される**スキーマ**要素の名前空間は `BooksModel`であることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1177">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="ba61d-1178">`Publisher`**EntityType**の `Address` プロパティは、`ExtendedBooksModel` 名前空間で定義されている複合型です ( **Using**要素と共にインポートされます)。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1178">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

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
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="ba61d-1179">annotation 属性 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1179">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="ba61d-1180">概念スキーマ定義言語 (CSDL) の annotation 属性は、概念モデルのカスタム XML 属性です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1180">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="ba61d-1181">有効な XML 構造であることに加え、annotation 属性は次の条件を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1181">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="ba61d-1182">annotation 属性は、CSDL 用に予約された XML 名前空間に存在しない。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1182">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="ba61d-1183">複数の annotation 属性を特定の 1 つの CSDL 要素に適用することができる。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1183">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="ba61d-1184">2 つの任意の annotation 属性の完全修飾名が同じではない。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1184">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="ba61d-1185">annotation 属性は、概念モデルの要素に関する追加のメタデータを提供するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1185">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="ba61d-1186">Annotation 要素に含まれるメタデータには、実行時に、system.string 名前空間のクラスを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1186">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="ba61d-1187">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-1187">Example</span></span>

<span data-ttu-id="ba61d-1188">Annotation 属性 (**CustomAttribute**) を持つ**EntityType**要素の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1188">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="ba61d-1189">また、エンティティ型の要素に適用される annotation 要素も示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1189">The example also shows an annotation element applied to the entity type element.</span></span>

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
 

<span data-ttu-id="ba61d-1190">次のコードは、annotation 属性でメタデータを取得し、コンソールに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1190">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

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
 

<span data-ttu-id="ba61d-1191">前のコードでは、`School.csdl` ファイルがプロジェクトの出力ディレクトリにあり、プロジェクトに次の `Imports` および `Using` ステートメントが追加されていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1191">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="ba61d-1192">Annotation 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1192">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="ba61d-1193">概念スキーマ定義言語 (CSDL) の annotation 要素は、概念モデルのカスタム XML 要素です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1193">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="ba61d-1194">有効な XML 構造が必要であることに加え、annotation 要素は次の条件も満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1194">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="ba61d-1195">annotation 要素は、CSDL 用に予約された XML 名前空間内に存在できません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1195">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="ba61d-1196">複数の annotation 要素を特定の CSDL 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1196">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="ba61d-1197">2 つの annotation 要素の完全修飾名を同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1197">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="ba61d-1198">annotation 要素は、特定の CSDL 要素のその他すべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1198">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="ba61d-1199">annotation 要素は、概念モデルの要素に関する追加のメタデータを提供するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1199">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="ba61d-1200">.NET Framework version 4 以降では、annotation 要素に含まれるメタデータに、実行時に、system.string 名前空間のクラスを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1200">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="ba61d-1201">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-1201">Example</span></span>

<span data-ttu-id="ba61d-1202">Annotation 要素 (**customelement**) を持つ**EntityType**要素の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1202">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="ba61d-1203">また、エンティティ型の要素に適用される annotation 属性も示しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1203">The example also show an annotation attribute applied to the entity type element.</span></span>

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
 

<span data-ttu-id="ba61d-1204">次のコードは、annotation 要素内のメタデータを取得してコンソールに出力します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1204">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

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
 

<span data-ttu-id="ba61d-1205">前のコードでは、School.csdl ファイルがプロジェクトの出力ディレクトリにあり、プロジェクトに次の `Imports` および `Using` ステートメントが追加されていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1205">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="ba61d-1206">概念モデルの型 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1206">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="ba61d-1207">概念スキーマ定義言語 (CSDL) では、概念モデルのプロパティを定義する**Edmsimpletypes**と呼ばれる一連の抽象プリミティブデータ型をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1207">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="ba61d-1208">**Edmsimpletypes**は、ストレージ環境またはホスト環境でサポートされているプリミティブデータ型のプロキシです。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1208">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="ba61d-1209">下の表は、CSDL でサポートされるプリミティブ データ型の一覧を示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1209">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="ba61d-1210">この表には、各**Edmsimpletype**に適用できるファセットの一覧も含まれています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1210">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="ba61d-1211">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="ba61d-1211">EDMSimpleType</span></span>                    | <span data-ttu-id="ba61d-1212">Description</span><span class="sxs-lookup"><span data-stu-id="ba61d-1212">Description</span></span>                                                | <span data-ttu-id="ba61d-1213">使用できるファセット</span><span class="sxs-lookup"><span data-stu-id="ba61d-1213">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="ba61d-1214">**Edm. Binary**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1214">**Edm.Binary**</span></span>                   | <span data-ttu-id="ba61d-1215">バイナリ データを格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1215">Contains binary data.</span></span>                                      | <span data-ttu-id="ba61d-1216">MaxLength、FixedLength、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1216">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="ba61d-1217">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1217">**Edm.Boolean**</span></span>                  | <span data-ttu-id="ba61d-1218">**True**または**false**の値が含まれています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1218">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="ba61d-1219">Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1219">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="ba61d-1220">**Edm. Byte**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1220">**Edm.Byte**</span></span>                     | <span data-ttu-id="ba61d-1221">符号なし 8 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1221">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="ba61d-1222">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1222">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ba61d-1223">**Edm. DateTime**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1223">**Edm.DateTime**</span></span>                 | <span data-ttu-id="ba61d-1224">日時を表します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1224">Represents a date and time.</span></span>                                | <span data-ttu-id="ba61d-1225">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1225">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ba61d-1226">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1226">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="ba61d-1227">GMT からのオフセット値の日時 (分単位) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1227">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="ba61d-1228">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1228">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ba61d-1229">**Edm. Decimal**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1229">**Edm.Decimal**</span></span>                  | <span data-ttu-id="ba61d-1230">有効桁数と小数点以下桁数が固定長の数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1230">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="ba61d-1231">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1231">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ba61d-1232">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1232">**Edm.Double**</span></span>                   | <span data-ttu-id="ba61d-1233">15桁の有効桁数を持つ浮動小数点数を格納します</span><span class="sxs-lookup"><span data-stu-id="ba61d-1233">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="ba61d-1234">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1234">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ba61d-1235">**Edm. Float**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1235">**Edm.Float**</span></span>                    | <span data-ttu-id="ba61d-1236">7 桁の有効桁数を持つ浮動小数点数を格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1236">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="ba61d-1237">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1237">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ba61d-1238">**Edm. Guid**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1238">**Edm.Guid**</span></span>                     | <span data-ttu-id="ba61d-1239">16 バイトの一意識別子を格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1239">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="ba61d-1240">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1240">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ba61d-1241">**Edm. Int16**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1241">**Edm.Int16**</span></span>                    | <span data-ttu-id="ba61d-1242">符号付き 16 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1242">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="ba61d-1243">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1243">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ba61d-1244">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1244">**Edm.Int32**</span></span>                    | <span data-ttu-id="ba61d-1245">符号付き 32 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1245">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="ba61d-1246">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1246">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ba61d-1247">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1247">**Edm.Int64**</span></span>                    | <span data-ttu-id="ba61d-1248">符号付き 64 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1248">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="ba61d-1249">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1249">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ba61d-1250">**Edm. SByte**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1250">**Edm.SByte**</span></span>                    | <span data-ttu-id="ba61d-1251">符号付き 8 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1251">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="ba61d-1252">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1252">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ba61d-1253">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1253">**Edm.String**</span></span>                   | <span data-ttu-id="ba61d-1254">文字データを格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1254">Contains character data.</span></span>                                   | <span data-ttu-id="ba61d-1255">Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1255">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="ba61d-1256">**Edm. Time**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1256">**Edm.Time**</span></span>                     | <span data-ttu-id="ba61d-1257">時刻を格納します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1257">Contains a time of day.</span></span>                                    | <span data-ttu-id="ba61d-1258">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="ba61d-1258">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="ba61d-1259">**Edm. Geography**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1259">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="ba61d-1260">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1260">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1261">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1261">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="ba61d-1262">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1262">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1263">**GeographyLineString**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1263">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="ba61d-1264">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1264">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1265">**返される geographypolygon**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1265">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="ba61d-1266">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1266">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1267">**Edm. GeographyMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1267">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="ba61d-1268">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1268">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1269">**返される geographymultilinestring**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1269">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="ba61d-1270">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1270">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1271">**Edm. GeographyMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1271">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="ba61d-1272">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1272">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1273">**Edm. GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1273">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="ba61d-1274">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1274">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1275">**Edm. Geometry**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1275">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="ba61d-1276">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1276">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1277">**返される geometrypoint**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1277">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="ba61d-1278">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1278">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1279">**返される geometrylinestring**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1279">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="ba61d-1280">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1280">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1281">**返される geometrypolygon**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1281">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="ba61d-1282">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1282">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1283">**返される geometrymultipoint**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1283">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="ba61d-1284">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1284">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1285">**返される geometrymultilinestring**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1285">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="ba61d-1286">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1286">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1287">**返される geometrymultipolygon**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1287">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="ba61d-1288">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1288">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="ba61d-1289">**GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1289">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="ba61d-1290">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="ba61d-1290">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="ba61d-1291">ファセット (CSDL)</span><span class="sxs-lookup"><span data-stu-id="ba61d-1291">Facets (CSDL)</span></span>

<span data-ttu-id="ba61d-1292">概念スキーマ定義言語 (CSDL) のファセットは、エンティティ型と複合型のプロパティに対する制約を表します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1292">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="ba61d-1293">ファセットは次の CSDL 要素で XML 属性として使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1293">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="ba61d-1294">プロパティ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1294">Property</span></span>
-   <span data-ttu-id="ba61d-1295">TypeRef</span><span class="sxs-lookup"><span data-stu-id="ba61d-1295">TypeRef</span></span>
-   <span data-ttu-id="ba61d-1296">パラメーター</span><span class="sxs-lookup"><span data-stu-id="ba61d-1296">Parameter</span></span>

<span data-ttu-id="ba61d-1297">次の表は、CSDL でサポートされるファセットについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1297">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="ba61d-1298">いずれのファセットもオプションです。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1298">All facets are optional.</span></span> <span data-ttu-id="ba61d-1299">次に示すいくつかのファセットは、概念モデルからデータベースを生成するときに Entity Framework によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1299">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="ba61d-1300">概念モデルのデータ型の詳細については、「概念モデルの型 (CSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1300">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="ba61d-1301">ファセット</span><span class="sxs-lookup"><span data-stu-id="ba61d-1301">Facet</span></span>               | <span data-ttu-id="ba61d-1302">Description</span><span class="sxs-lookup"><span data-stu-id="ba61d-1302">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="ba61d-1303">適用対象</span><span class="sxs-lookup"><span data-stu-id="ba61d-1303">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="ba61d-1304">データベースの生成に使用</span><span class="sxs-lookup"><span data-stu-id="ba61d-1304">Used for the database generation</span></span> | <span data-ttu-id="ba61d-1305">ランタイムで使用</span><span class="sxs-lookup"><span data-stu-id="ba61d-1305">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="ba61d-1306">**Collation**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1306">**Collation**</span></span>       | <span data-ttu-id="ba61d-1307">プロパティの値に対して比較と順序付け操作を行うときに使用する照合シーケンス (または並べ替え順序) を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1307">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="ba61d-1308">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1308">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="ba61d-1309">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1309">Yes</span></span>                              | <span data-ttu-id="ba61d-1310">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1310">No</span></span>                  |
| <span data-ttu-id="ba61d-1311">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1311">**ConcurrencyMode**</span></span> | <span data-ttu-id="ba61d-1312">プロパティの値をオプティミスティック コンカレンシー チェックに使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1312">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="ba61d-1313">すべての**Edmsimpletype**プロパティ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1313">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="ba61d-1314">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1314">No</span></span>                               | <span data-ttu-id="ba61d-1315">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1315">Yes</span></span>                 |
| <span data-ttu-id="ba61d-1316">**[Default]**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1316">**Default**</span></span>         | <span data-ttu-id="ba61d-1317">インスタンス化で値が指定されない場合のプロパティの既定値を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1317">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="ba61d-1318">すべての**Edmsimpletype**プロパティ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1318">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="ba61d-1319">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1319">Yes</span></span>                              | <span data-ttu-id="ba61d-1320">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1320">Yes</span></span>                 |
| <span data-ttu-id="ba61d-1321">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1321">**FixedLength**</span></span>     | <span data-ttu-id="ba61d-1322">プロパティ値の長さを可変とすることができるかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1322">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="ba61d-1323">**Edm. Binary**, **edm. String**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1323">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="ba61d-1324">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1324">Yes</span></span>                              | <span data-ttu-id="ba61d-1325">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1325">No</span></span>                  |
| <span data-ttu-id="ba61d-1326">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1326">**MaxLength**</span></span>       | <span data-ttu-id="ba61d-1327">プロパティ値の最大長を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1327">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="ba61d-1328">**Edm. Binary**, **edm. String**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1328">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="ba61d-1329">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1329">Yes</span></span>                              | <span data-ttu-id="ba61d-1330">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1330">No</span></span>                  |
| <span data-ttu-id="ba61d-1331">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1331">**Nullable**</span></span>        | <span data-ttu-id="ba61d-1332">プロパティが**null**値を持つことができるかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1332">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="ba61d-1333">すべての**Edmsimpletype**プロパティ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1333">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="ba61d-1334">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1334">Yes</span></span>                              | <span data-ttu-id="ba61d-1335">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1335">Yes</span></span>                 |
| <span data-ttu-id="ba61d-1336">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1336">**Precision**</span></span>       | <span data-ttu-id="ba61d-1337">**Decimal**型のプロパティの場合、プロパティ値が持つことができる桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1337">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="ba61d-1338">**Time**、 **DateTime**、および**DateTimeOffset**型のプロパティの場合、プロパティ値の秒の小数部の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1338">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="ba61d-1339">**Edm. DateTime**, **edm. DateTimeOffset**, edm. **Decimal**, **edm. Time**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1339">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="ba61d-1340">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1340">Yes</span></span>                              | <span data-ttu-id="ba61d-1341">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1341">No</span></span>                  |
| <span data-ttu-id="ba61d-1342">**スケール**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1342">**Scale**</span></span>           | <span data-ttu-id="ba61d-1343">プロパティ値の小数点の右側の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1343">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="ba61d-1344">**Edm. Decimal**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1344">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="ba61d-1345">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1345">Yes</span></span>                              | <span data-ttu-id="ba61d-1346">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1346">No</span></span>                  |
| <span data-ttu-id="ba61d-1347">**SRID**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1347">**SRID**</span></span>            | <span data-ttu-id="ba61d-1348">空間システム参照システム ID を指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1348">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="ba61d-1349">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1349">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="ba61d-1350">**GeographyLineString、edm. GeographyPoint、返される geographypolygon、edm. Geographypoint、返される geographymultilinestring、edm. GeographyMultiPolygon、edm. Geographypoint、edm. Geometry、返される geometrypoint,、および,、およびです。返される geometrylinestring、返される geometrypolygon、返される geometrymultipoint、edm. 返される geometrymultilinestring、、返される geometrymultipolygon、Edm. GeometryCollection。**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1350">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="ba61d-1351">いいえ</span><span class="sxs-lookup"><span data-stu-id="ba61d-1351">No</span></span>                               | <span data-ttu-id="ba61d-1352">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1352">Yes</span></span>                 |
| <span data-ttu-id="ba61d-1353">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1353">**Unicode**</span></span>         | <span data-ttu-id="ba61d-1354">プロパティ値を Unicode として保存するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1354">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="ba61d-1355">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="ba61d-1355">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="ba61d-1356">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1356">Yes</span></span>                              | <span data-ttu-id="ba61d-1357">はい</span><span class="sxs-lookup"><span data-stu-id="ba61d-1357">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="ba61d-1358">概念モデルからデータベースを生成する場合、データベース生成ウィザードでは、**プロパティ**要素が次の名前空間にある**場合、その属性の**値が認識されます: https://schemas.microsoft.com/ado/2009/02/edm/annotation。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1358">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: https://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="ba61d-1359">属性でサポートされている値は、 **id**と**計算**です。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1359">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="ba61d-1360">**Id**値を指定すると、データベースで生成された id 値を持つデータベース列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1360">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="ba61d-1361">**計算**値を指定すると、データベースで計算された値を持つ列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1361">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="ba61d-1362">例</span><span class="sxs-lookup"><span data-stu-id="ba61d-1362">Example</span></span>

<span data-ttu-id="ba61d-1363">エンティティ型のプロパティに適用されるファセットの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="ba61d-1363">The following example shows facets applied to the properties of an entity type:</span></span>

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
