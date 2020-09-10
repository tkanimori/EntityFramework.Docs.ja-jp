---
title: CSDL 仕様-EF6
description: Entity Framework 6 の CSDL 仕様
author: divega
ms.date: 10/23/2016
ms.assetid: c54255f4-253f-49eb-bec8-ad7927ac2fa3
uid: ef6/modeling/designer/advanced/edmx/csdl-spec
ms.openlocfilehash: 0137eba39bd719b8987dad2eb16645475a00d94c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620627"
---
# <a name="csdl-specification"></a><span data-ttu-id="13ce2-103">CSDL 仕様</span><span class="sxs-lookup"><span data-stu-id="13ce2-103">CSDL Specification</span></span>
<span data-ttu-id="13ce2-104">概念スキーマ定義言語 (CSDL : Conceptual Schema Definition Language) は、XML ベースの言語であり、データ駆動型アプリケーションの概念モデルを構成するエンティティ、リレーションシップ、および関数を記述します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-104">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="13ce2-105">この概念モデルは、Entity Framework または WCF Data Services で使用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-105">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="13ce2-106">CSDL で記述されたメタデータは、概念モデルで定義されているエンティティとリレーションシップをデータソースにマップするために、Entity Framework によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-106">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="13ce2-107">詳細については、「 [SSDL 仕様](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) と [MSL 仕様](xref:ef6/modeling/designer/advanced/edmx/msl-spec)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-107">For more information, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) and [MSL Specification](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span></span>

<span data-ttu-id="13ce2-108">CSDL は、Entity Data Model の Entity Framework での実装です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-108">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="13ce2-109">Entity Framework アプリケーションでは、概念モデルのメタデータは、csdl で記述された .csdl ファイルから EdmItemCollection のインスタンスに読み込まれます。このメタデータには、クラスのメソッドを使用してアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-109">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="13ce2-110">Entity Framework は、概念モデルのメタデータを使用して、概念モデルに対するクエリをデータソース固有のコマンドに変換します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-110">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="13ce2-111">EF デザイナーは、デザイン時に .edmx ファイルに概念モデル情報を格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-111">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="13ce2-112">EF デザイナーは、ビルド時に .edmx ファイルの情報を使用して、実行時に Entity Framework に必要な .csdl ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-112">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="13ce2-113">CSDL のバージョンは、XML 名前空間で区別されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-113">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="13ce2-114">CSDL のバージョン</span><span class="sxs-lookup"><span data-stu-id="13ce2-114">CSDL Version</span></span> | <span data-ttu-id="13ce2-115">XML 名前空間</span><span class="sxs-lookup"><span data-stu-id="13ce2-115">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="13ce2-116">CSDL v1</span><span class="sxs-lookup"><span data-stu-id="13ce2-116">CSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="13ce2-117">CSDL v2</span><span class="sxs-lookup"><span data-stu-id="13ce2-117">CSDL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="13ce2-118">CSDL v3</span><span class="sxs-lookup"><span data-stu-id="13ce2-118">CSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="13ce2-119">Association 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-119">Association Element (CSDL)</span></span>

<span data-ttu-id="13ce2-120">**Association**要素は、2つのエンティティ型間のリレーションシップを定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-120">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="13ce2-121">アソシエーションでは、リレーションシップに関連するエンティティ型、およびリレーションシップの各 End におけるエンティティ型の数 (多重度と呼ばれる) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-121">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="13ce2-122">アソシエーション end の複数要素の接続性には、1 (1)、0または 1 (0 ..1)、または many () を指定でき \* ます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-122">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="13ce2-123">この情報は、2 つの子 End 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-123">This information is specified in two child End elements.</span></span>

<span data-ttu-id="13ce2-124">アソシエーションの一方の End にあるエンティティ型のインスタンスには、それらがエンティティ型で公開されている場合、ナビゲーション プロパティまたは外部キーからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-124">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="13ce2-125">アプリケーション内で、アソシエーションのインスタンスは、エンティティ型のインスタンスの間の特定のアソシエーションを表します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-125">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="13ce2-126">アソシエーション インスタンスは、アソシエーション セットに論理的にグループ化されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-126">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="13ce2-127">**Association**要素には、次の子要素を含めることができます (順に記載されています)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-127">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-128">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-128">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-129">End (2 個の要素のみ)</span><span class="sxs-lookup"><span data-stu-id="13ce2-129">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="13ce2-130">ReferentialConstraint (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-130">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-131">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-131">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-132">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-132">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-133">次の表では、 **Association** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-133">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="13ce2-134">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-134">Attribute Name</span></span> | <span data-ttu-id="13ce2-135">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-135">Is Required</span></span> | <span data-ttu-id="13ce2-136">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-136">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="13ce2-137">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-137">**Name**</span></span>       | <span data-ttu-id="13ce2-138">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-138">Yes</span></span>         | <span data-ttu-id="13ce2-139">アソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-139">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-140">任意の数の annotation 属性 (カスタム XML 属性) を **Association** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-140">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="13ce2-141">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-141">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-142">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-142">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-143">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-143">Example</span></span>

<span data-ttu-id="13ce2-144">次の例では、外部キーが**Customer**および**Order**エンティティ型で公開されていない場合に、 **CustomerOrders**関連付けを定義する**association**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-144">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="13ce2-145">アソシエーションの各**End**の複数**要素**の接続性の値は、**顧客**に複数の**注文**を関連付けることができることを示していますが、**注文**に関連付けることができるのは1つの**顧客**だけです。</span><span class="sxs-lookup"><span data-stu-id="13ce2-145">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="13ce2-146">また、 **OnDelete**要素は、**顧客**が削除されると、特定の**顧客**に関連付けられていて、ObjectContext に読み込まれているすべての**注文**が削除されることを示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-146">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="13ce2-147">次の例は、 **Customer**および**Order**エンティティ型で外部キーが公開されている場合に、 **CustomerOrders**関連付けを定義する**association**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-147">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="13ce2-148">外部キーが公開されている場合、エンティティ間のリレーションシップは **、要素を使用して** 管理されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-148">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="13ce2-149">このアソシエーションをデータ ソースにマップするために、対応する AssociationSetMapping 要素は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-149">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

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
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="13ce2-150">AssociationSet 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-150">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="13ce2-151">概念スキーマ定義言語 (CSDL) の **AssociationSet** 要素は、同じ型のアソシエーションインスタンスの論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="13ce2-151">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="13ce2-152">アソシエーション セットは、複数のアソシエーション インスタンスを 1 つのデータ ソースにマップできるようグループ化する方法を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-152">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="13ce2-153">**AssociationSet**要素には、次の子要素を含めることができます (この順序で表示されます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-153">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-154">Documentation (0 または 1 つの要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-154">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="13ce2-155">End (2 つの要素が必要とされます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-155">End (exactly two elements required)</span></span>
-   <span data-ttu-id="13ce2-156">Annotation 要素 (0 個以上の要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-156">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="13ce2-157">Association **属性は、アソシエーション** セットに含まれるアソシエーションの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-157">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="13ce2-158">アソシエーションセットの末尾を構成するエンティティセットは、厳密に2つの子 **End** 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-158">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-159">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-159">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-160">次の表では、 **AssociationSet** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-160">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="13ce2-161">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-161">Attribute Name</span></span>  | <span data-ttu-id="13ce2-162">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-162">Is Required</span></span> | <span data-ttu-id="13ce2-163">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-163">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-164">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-164">**Name**</span></span>        | <span data-ttu-id="13ce2-165">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-165">Yes</span></span>         | <span data-ttu-id="13ce2-166">エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-166">The name of the entity set.</span></span> <span data-ttu-id="13ce2-167">**Name**属性の値を**Association**属性の値と同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-167">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="13ce2-168">**関連付け**</span><span class="sxs-lookup"><span data-stu-id="13ce2-168">**Association**</span></span> | <span data-ttu-id="13ce2-169">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-169">Yes</span></span>         | <span data-ttu-id="13ce2-170">関連付けの完全修飾名。そのインスタンスは、アソシエーション セットに格納されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-170">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="13ce2-171">関連付けは、アソシエーション セットと同じ名前空間に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-171">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-172">任意の数の annotation 属性 (カスタム XML 属性) を **AssociationSet** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-172">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="13ce2-173">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-174">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-175">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-175">Example</span></span>

<span data-ttu-id="13ce2-176">次の例は、2つの**AssociationSet**要素を持つ**EntityContainer**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-176">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

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
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="13ce2-177">CollectionType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-177">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="13ce2-178">概念スキーマ定義言語 (CSDL) の **CollectionType** 要素は、関数パラメーターまたは関数の戻り値の型がコレクションであることを指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-178">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="13ce2-179">**CollectionType**要素は、Parameter 要素または ReturnType (Function) 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-179">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="13ce2-180">コレクションの型は、 **type** 属性または次のいずれかの子要素を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-180">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="13ce2-181">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="13ce2-181">**CollectionType**</span></span>
-   <span data-ttu-id="13ce2-182">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="13ce2-182">ReferenceType</span></span>
-   <span data-ttu-id="13ce2-183">RowType</span><span class="sxs-lookup"><span data-stu-id="13ce2-183">RowType</span></span>
-   <span data-ttu-id="13ce2-184">TypeRef</span><span class="sxs-lookup"><span data-stu-id="13ce2-184">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="13ce2-185">**型**属性と子要素の両方でコレクションの型が指定されている場合、モデルは検証されません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-185">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-186">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-186">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-187">次の表では、 **CollectionType** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-187">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="13ce2-188">**DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**、および**Collation**属性は、 **edmsimpletypes**のコレクションにのみ適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-188">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="13ce2-189">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-189">Attribute Name</span></span>                                                          | <span data-ttu-id="13ce2-190">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-190">Is Required</span></span> | <span data-ttu-id="13ce2-191">[値]</span><span class="sxs-lookup"><span data-stu-id="13ce2-191">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-192">**型**</span><span class="sxs-lookup"><span data-stu-id="13ce2-192">**Type**</span></span>                                                                | <span data-ttu-id="13ce2-193">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-193">No</span></span>          | <span data-ttu-id="13ce2-194">コレクションの型。</span><span class="sxs-lookup"><span data-stu-id="13ce2-194">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="13ce2-195">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="13ce2-195">**Nullable**</span></span>                                                            | <span data-ttu-id="13ce2-196">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-196">No</span></span>          | <span data-ttu-id="13ce2-197">プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または **False**。</span><span class="sxs-lookup"><span data-stu-id="13ce2-197">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="13ce2-198">CSDL v1 で > 複合型プロパティにはが必要 `Nullable="False"` です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-198">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="13ce2-199">**既定**</span><span class="sxs-lookup"><span data-stu-id="13ce2-199">**DefaultValue**</span></span>                                                        | <span data-ttu-id="13ce2-200">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-200">No</span></span>          | <span data-ttu-id="13ce2-201">プロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-201">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="13ce2-202">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-202">**MaxLength**</span></span>                                                           | <span data-ttu-id="13ce2-203">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-203">No</span></span>          | <span data-ttu-id="13ce2-204">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="13ce2-204">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="13ce2-205">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-205">**FixedLength**</span></span>                                                         | <span data-ttu-id="13ce2-206">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-206">No</span></span>          | <span data-ttu-id="13ce2-207">プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-207">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="13ce2-208">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="13ce2-208">**Precision**</span></span>                                                           | <span data-ttu-id="13ce2-209">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-209">No</span></span>          | <span data-ttu-id="13ce2-210">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="13ce2-210">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="13ce2-211">**スケール**</span><span class="sxs-lookup"><span data-stu-id="13ce2-211">**Scale**</span></span>                                                               | <span data-ttu-id="13ce2-212">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-212">No</span></span>          | <span data-ttu-id="13ce2-213">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="13ce2-213">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="13ce2-214">**SRID**</span><span class="sxs-lookup"><span data-stu-id="13ce2-214">**SRID**</span></span>                                                                | <span data-ttu-id="13ce2-215">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-215">No</span></span>          | <span data-ttu-id="13ce2-216">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="13ce2-216">Spatial System Reference Identifier.</span></span> <span data-ttu-id="13ce2-217">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-217">Valid only for properties of spatial types.</span></span><span data-ttu-id="13ce2-218">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-218">   For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="13ce2-219">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="13ce2-219">**Unicode**</span></span>                                                             | <span data-ttu-id="13ce2-220">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-220">No</span></span>          | <span data-ttu-id="13ce2-221">プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-221">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="13ce2-222">**Collation**</span><span class="sxs-lookup"><span data-stu-id="13ce2-222">**Collation**</span></span>                                                           | <span data-ttu-id="13ce2-223">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-223">No</span></span>          | <span data-ttu-id="13ce2-224">データ ソースで使用される照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="13ce2-224">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="13ce2-225">任意の数の annotation 属性 (カスタム XML 属性) を **CollectionType** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-225">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="13ce2-226">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-227">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-228">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-228">Example</span></span>

<span data-ttu-id="13ce2-229">次の例は、 **CollectionType** 要素を使用して、関数が **Person** エンティティ型のコレクション ( **ElementType** 属性で指定) を返すことを指定するモデル定義関数を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-229">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

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
 

<span data-ttu-id="13ce2-230">次の例は、 **CollectionType** 要素を使用して、 **RowType** 要素で指定された行のコレクションを返すことを指定するモデル定義関数を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-230">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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
 

<span data-ttu-id="13ce2-231">次の例は、 **CollectionType** 要素を使用して、関数が **学科** エンティティ型のコレクションをパラメーターとして受け入れることを指定するモデル定義関数を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-231">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

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
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="13ce2-232">ComplexType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-232">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="13ce2-233">**ComplexType**要素は、 **edmsimpletype**プロパティまたは他の複合型で構成されるデータ構造を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-233">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span><span data-ttu-id="13ce2-234">複合型は、エンティティ型または別の複合型のプロパティにすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-234">  A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="13ce2-235">複合型は、データを定義するという点でエンティティ型に似ています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-235">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="13ce2-236">ただし、複合型とエンティティ型の間にはいくつかの重要な違いがあります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-236">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="13ce2-237">複合型には ID (またはキー) がないため、独立して存在することができません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-237">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="13ce2-238">複合型は、エンティティ型またはその他の複合型のプロパティとしてのみ存在できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-238">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="13ce2-239">複合型は、アソシエーションに参加できません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-239">Complex types cannot participate in associations.</span></span> <span data-ttu-id="13ce2-240">アソシエーションのいずれの End にも複合型を指定できないため、複合型にはナビゲーション プロパティを定義できません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-240">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="13ce2-241">複合型のスカラー プロパティはそれぞれ null に設定できますが、複合型のプロパティには null 値を指定できません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-241">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="13ce2-242">**ComplexType**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-242">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-243">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-243">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-244">Property (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-244">Property (zero or more elements)</span></span>
-   <span data-ttu-id="13ce2-245">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-245">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="13ce2-246">次の表では、 **ComplexType** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-246">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="13ce2-247">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-247">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="13ce2-248">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-248">Is Required</span></span> | <span data-ttu-id="13ce2-249">[値]</span><span class="sxs-lookup"><span data-stu-id="13ce2-249">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-250">名前</span><span class="sxs-lookup"><span data-stu-id="13ce2-250">Name</span></span>                                                                                                           | <span data-ttu-id="13ce2-251">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-251">Yes</span></span>         | <span data-ttu-id="13ce2-252">複合型の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-252">The name of the complex type.</span></span> <span data-ttu-id="13ce2-253">複合型の名前は、モデルのスコープ内にある別の複合型、エンティティ型、またはアソシエーションの名前と同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-253">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="13ce2-254">BaseType</span><span class="sxs-lookup"><span data-stu-id="13ce2-254">BaseType</span></span>                                                                                                       | <span data-ttu-id="13ce2-255">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-255">No</span></span>          | <span data-ttu-id="13ce2-256">定義される複合型の基本データ型である、別の複合型の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-256">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="13ce2-257">> この属性は CSDL v1 では適用できません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-257">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="13ce2-258">複合型の継承は、このバージョンではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-258">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="13ce2-259">概要</span><span class="sxs-lookup"><span data-stu-id="13ce2-259">Abstract</span></span>                                                                                                       | <span data-ttu-id="13ce2-260">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-260">No</span></span>          | <span data-ttu-id="13ce2-261">複合型が抽象型であるかどうかに応じて、 **True**または**False** (既定値)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-261">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="13ce2-262">> この属性は CSDL v1 では適用できません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-262">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="13ce2-263">このバージョンの複合型を抽象型にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-263">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="13ce2-264">任意の数の annotation 属性 (カスタム XML 属性) を **ComplexType** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-264">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="13ce2-265">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-265">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-266">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-266">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-267">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-267">Example</span></span>

<span data-ttu-id="13ce2-268">次の例では、 **Edmsimpletype**プロパティ**StreetAddress**、 **City**、 **StateOrProvince**、Country、および**郵便\*\*\*\*番号**を使用して、複合型**Address**を示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-268">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="13ce2-269">エンティティ型のプロパティとして複合型の **アドレス** (上記) を定義するには、エンティティ型の定義でプロパティの型を宣言する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-269">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="13ce2-270">次の例では、エンティティ型 (**パブリッシャー**) の複合型として**Address**プロパティを示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-270">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

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
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="13ce2-271">DefiningExpression 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-271">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="13ce2-272">概念スキーマ定義言語 (CSDL) の definition **ingexpression** 要素には、概念モデルの関数を定義する Entity SQL 式が含まれています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-272">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="13ce2-273">検証のために、 **定義** 要素には任意のコンテンツを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-273">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="13ce2-274">ただし、 **定義** 要素に有効な Entity SQL が含まれていない場合、Entity Framework は実行時に例外をスローします。</span><span class="sxs-lookup"><span data-stu-id="13ce2-274">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-275">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-275">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-276">任意の数の annotation 属性 (カスタム XML 属性 **) を定義** することができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-276">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="13ce2-277">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-277">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-278">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-278">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="13ce2-279">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-279">Example</span></span>

<span data-ttu-id="13ce2-280">次の例では、定義、 **式** を使用して、書籍が発行されてからの年数を返す関数を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-280">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="13ce2-281">**定義**済みの要素の内容は Entity SQL で記述されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-281">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="13ce2-282">Dependent 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-282">Dependent Element (CSDL)</span></span>

<span data-ttu-id="13ce2-283">概念スキーマ定義言語 (CSDL) の **dependent** 要素は、参照に関する制約の依存 end を定義し、参照制約の依存 end を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-283">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="13ce2-284">参照整合性の **制約要素は、リレーショナル** データベースの参照整合性制約に似た機能を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-284">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="13ce2-285">データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティが別のエンティティ型のエンティティ キーを参照できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-285">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="13ce2-286">参照されるエンティティ型は、制約の "*プリンシパル End*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-286">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="13ce2-287">プリンシパル End を参照するエンティティ型は、制約の "*依存 End*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-287">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="13ce2-288">**Propertyref** 要素は、プリンシパル end を参照するキーを指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-288">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="13ce2-289">**Dependent**要素には、次の子要素を含めることができます (順に記載されています)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-289">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-290">PropertyRef (1 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-290">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="13ce2-291">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-291">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-292">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-292">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-293">次の表は、 **依存** 要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-293">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="13ce2-294">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-294">Attribute Name</span></span> | <span data-ttu-id="13ce2-295">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-295">Is Required</span></span> | <span data-ttu-id="13ce2-296">[値]</span><span class="sxs-lookup"><span data-stu-id="13ce2-296">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="13ce2-297">**ロール**</span><span class="sxs-lookup"><span data-stu-id="13ce2-297">**Role**</span></span>       | <span data-ttu-id="13ce2-298">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-298">Yes</span></span>         | <span data-ttu-id="13ce2-299">アソシエーションの依存 End 上のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-299">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-300">任意の数の annotation 属性 (カスタム XML 属性) を **依存** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-300">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="13ce2-301">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-301">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-302">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-302">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-303">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-303">Example</span></span>

<span data-ttu-id="13ce2-304">次の例では、 **publishedby**関連付けの定義の一部として使用されている **、オブジェクトの**場所を示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-304">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="13ce2-305">**Book**エンティティ型の**PublisherId**プロパティは、参照制約の依存 end を構成します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-305">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

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
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="13ce2-306">Documentation 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-306">Documentation Element (CSDL)</span></span>

<span data-ttu-id="13ce2-307">概念スキーマ定義言語 (CSDL) の **Documentation** 要素は、親要素で定義されているオブジェクトに関する情報を提供するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-307">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="13ce2-308">.Edmx ファイルで、 **documentation** 要素が、EF デザイナーのデザイン画面 (エンティティ、アソシエーション、プロパティなど) でオブジェクトとして表示される要素の子である場合、 **documentation** 要素の内容は、そのオブジェクトの Visual Studio の [ **プロパティ** ] ウィンドウに表示されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-308">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="13ce2-309">**Documentation**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-309">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-310">**Summary**: 親要素の簡単な説明。</span><span class="sxs-lookup"><span data-stu-id="13ce2-310">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="13ce2-311">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-311">(zero or one element)</span></span>
-   <span data-ttu-id="13ce2-312">**Longdescription**: 親要素の詳細な説明。</span><span class="sxs-lookup"><span data-stu-id="13ce2-312">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="13ce2-313">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-313">(zero or one element)</span></span>
-   <span data-ttu-id="13ce2-314">annotation 要素</span><span class="sxs-lookup"><span data-stu-id="13ce2-314">Annotation elements.</span></span> <span data-ttu-id="13ce2-315">(0 個以上の要素)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-315">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-316">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-316">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-317">**Documentation**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-317">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="13ce2-318">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-318">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-319">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-319">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="13ce2-320">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-320">Example</span></span>

<span data-ttu-id="13ce2-321">次の例は、EntityType 要素の子要素としての **Documentation** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-321">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="13ce2-322">以下のスニペットが .edmx ファイルの CSDL コンテンツに含まれている場合、エンティティ型をクリックすると、 **Summary**要素と**longdescription**要素の内容が Visual Studio の [ **プロパティ**] ウィンドウに表示されます。 `Customer`</span><span class="sxs-lookup"><span data-stu-id="13ce2-322">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

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
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="13ce2-323">End 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-323">End Element (CSDL)</span></span>

<span data-ttu-id="13ce2-324">概念スキーマ定義言語 (CSDL) の **End** 要素は、Association 要素または AssociationSet 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-324">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="13ce2-325">どちらの場合も、 **End** 要素の役割は異なり、適用可能な属性も異なります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-325">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="13ce2-326">Association 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="13ce2-326">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="13ce2-327">**End**要素 ( **association**要素の子として) は、アソシエーションの一方の end のエンティティ型と、アソシエーションのその end に存在できるエンティティ型のインスタンスの数を識別します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-327">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="13ce2-328">アソシエーション End はアソシエーションの一部として定義され、アソシエーションには必ず 2 つのアソシエーション End が必要です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-328">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="13ce2-329">アソシエーションの 1 つの End にあるエンティティ型インスタンスには、ナビゲーション プロパティまたは外部キーからアクセスできます (エンティティ型で公開されている場合)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-329">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="13ce2-330">**終了**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-330">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-331">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-331">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-332">OnDelete (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-332">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-333">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-333">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-334">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-334">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-335">次の表では、 **End** 要素が **Association** 要素の子である場合に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-335">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="13ce2-336">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-336">Attribute Name</span></span>   | <span data-ttu-id="13ce2-337">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-337">Is Required</span></span> | <span data-ttu-id="13ce2-338">[値]</span><span class="sxs-lookup"><span data-stu-id="13ce2-338">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-339">**Type**</span><span class="sxs-lookup"><span data-stu-id="13ce2-339">**Type**</span></span>         | <span data-ttu-id="13ce2-340">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-340">Yes</span></span>         | <span data-ttu-id="13ce2-341">アソシエーションの一方の End のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-341">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="13ce2-342">**Role**</span><span class="sxs-lookup"><span data-stu-id="13ce2-342">**Role**</span></span>         | <span data-ttu-id="13ce2-343">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-343">No</span></span>          | <span data-ttu-id="13ce2-344">アソシエーション End の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-344">A name for the association end.</span></span> <span data-ttu-id="13ce2-345">名前が指定されない場合、アソシエーション End のエンティティ型の名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-345">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="13ce2-346">**カーディナリティ**</span><span class="sxs-lookup"><span data-stu-id="13ce2-346">**Multiplicity**</span></span> | <span data-ttu-id="13ce2-347">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-347">Yes</span></span>         | <span data-ttu-id="13ce2-348">**1**、 **0 ..1**、または **\*** アソシエーションの最後にあるエンティティ型のインスタンスの数によって異なります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-348">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="13ce2-349">**1** は、アソシエーション end に1つのエンティティ型インスタンスが存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-349">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="13ce2-350">**0 ..1** は、アソシエーション end に0個または1個のエンティティ型インスタンスが存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-350">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="13ce2-351">**\*** アソシエーション end に0個以上のエンティティ型インスタンスが存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-351">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-352">任意の数の annotation 属性 (カスタム XML 属性) を **終了** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-352">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="13ce2-353">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-353">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-354">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-354">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="13ce2-355">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-355">Example</span></span>

<span data-ttu-id="13ce2-356">次の例は、 **CustomerOrders**の関連付けを定義する**association**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-356">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="13ce2-357">アソシエーションの各**End**の複数**要素**の接続性の値は、**顧客**に複数の**注文**を関連付けることができることを示していますが、**注文**に関連付けることができるのは1つの**顧客**だけです。</span><span class="sxs-lookup"><span data-stu-id="13ce2-357">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="13ce2-358">また、 **OnDelete**要素は、**顧客**が削除された場合に、特定の**顧客**に関連付けられているすべての注文と ObjectContext に読み込まれたすべての**注文**を削除することを示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-358">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="13ce2-359">AssociationSet 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="13ce2-359">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="13ce2-360">**End**要素は、アソシエーションセットの1つの端を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-360">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="13ce2-361">**AssociationSet**要素には、2つの**End**要素が含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-361">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="13ce2-362">**End**要素に含まれる情報は、アソシエーションセットをデータソースにマップするときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-362">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="13ce2-363">**終了**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-363">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-364">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-364">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-365">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-365">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="13ce2-366">Annotation 要素は、それ以外のすべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-366">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="13ce2-367">Annotation 要素は、CSDL v2 以降でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-367">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-368">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-368">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-369">次の表では、 **AssociationSet**要素の子である場合に**End**要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-369">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="13ce2-370">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-370">Attribute Name</span></span> | <span data-ttu-id="13ce2-371">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-371">Is Required</span></span> | <span data-ttu-id="13ce2-372">[値]</span><span class="sxs-lookup"><span data-stu-id="13ce2-372">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-373">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="13ce2-373">**EntitySet**</span></span>  | <span data-ttu-id="13ce2-374">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-374">Yes</span></span>         | <span data-ttu-id="13ce2-375">親**AssociationSet**要素の1つの end を定義する**EntitySet**要素の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-375">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="13ce2-376">**EntitySet**要素は、親**AssociationSet**要素と同じエンティティコンテナーで定義されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-376">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="13ce2-377">**Role**</span><span class="sxs-lookup"><span data-stu-id="13ce2-377">**Role**</span></span>       | <span data-ttu-id="13ce2-378">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-378">No</span></span>          | <span data-ttu-id="13ce2-379">アソシエーション セット End の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-379">The name of the association set end.</span></span> <span data-ttu-id="13ce2-380">**Role**属性が使用されていない場合、アソシエーションセット end の名前はエンティティセットの名前になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-380">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="13ce2-381">任意の数の annotation 属性 (カスタム XML 属性) を **終了** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-381">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="13ce2-382">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-382">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-383">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-383">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="13ce2-384">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-384">Example</span></span>

<span data-ttu-id="13ce2-385">次の例は、 **2 つの3つ**の要素を持つ**AssociationSet**要素を2つ持つ**EntityContainer**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-385">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

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
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="13ce2-386">EntityContainer 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-386">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="13ce2-387">概念スキーマ定義言語 (CSDL) の **EntityContainer** 要素は、エンティティセット、アソシエーションセット、および関数インポートの論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="13ce2-387">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="13ce2-388">概念モデルのエンティティ コンテナーは、EntityContainerMapping 要素を通じてストレージ モデルのエンティティ コンテナーにマップされます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-388">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="13ce2-389">ストレージ モデルのエンティティ コンテナーは、データベースの構造を記述します。エンティティ セットはデータベースのテーブル、アソシエーション セットは外部キー、関数インポートはストアド プロシージャをそれぞれ記述します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-389">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="13ce2-390">**EntityContainer**要素には、0個または1個の Documentation 要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-390">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="13ce2-391">**Documentation**要素が存在する場合は、 **EntitySet**、 **AssociationSet**、および**FunctionImport**のすべての要素の前に記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-391">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="13ce2-392">**EntityContainer**要素には、次の子要素を0個以上含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-392">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-393">EntitySet</span><span class="sxs-lookup"><span data-stu-id="13ce2-393">EntitySet</span></span>
-   <span data-ttu-id="13ce2-394">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="13ce2-394">AssociationSet</span></span>
-   <span data-ttu-id="13ce2-395">FunctionImport 要素</span><span class="sxs-lookup"><span data-stu-id="13ce2-395">FunctionImport</span></span>
-   <span data-ttu-id="13ce2-396">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="13ce2-396">Annotation elements</span></span>

<span data-ttu-id="13ce2-397">**Entitycontainer**要素を拡張して、同じ名前空間内にある別の**entitycontainer**の内容を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-397">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="13ce2-398">別の **entitycontainer**の内容を参照する **entitycontainer** 要素に含めるには、 **Extends** 属性の値を、含める **entitycontainer** 要素の名前に設定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-398">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="13ce2-399">含まれている **entitycontainer** 要素のすべての子要素は、参照している **entitycontainer** 要素の子要素として扱われます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-399">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-400">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-400">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-401">次の表は、 **Using** 要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-401">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="13ce2-402">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-402">Attribute Name</span></span> | <span data-ttu-id="13ce2-403">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-403">Is Required</span></span> | <span data-ttu-id="13ce2-404">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-404">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="13ce2-405">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-405">**Name**</span></span>       | <span data-ttu-id="13ce2-406">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-406">Yes</span></span>         | <span data-ttu-id="13ce2-407">エンティティ コンテナー名。</span><span class="sxs-lookup"><span data-stu-id="13ce2-407">The name of the entity container.</span></span>                               |
| <span data-ttu-id="13ce2-408">**拡張**</span><span class="sxs-lookup"><span data-stu-id="13ce2-408">**Extends**</span></span>    | <span data-ttu-id="13ce2-409">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-409">No</span></span>          | <span data-ttu-id="13ce2-410">同じ名前空間内にある別のエンティティ コンテナーの名前 </span><span class="sxs-lookup"><span data-stu-id="13ce2-410">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-411">任意の数の annotation 属性 (カスタム XML 属性) を **EntityContainer** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-411">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="13ce2-412">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-412">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-413">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-413">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-414">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-414">Example</span></span>

<span data-ttu-id="13ce2-415">次の例は、3つのエンティティセットと2つのアソシエーションセットを定義する **EntityContainer** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-415">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

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
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="13ce2-416">EntitySet 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-416">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="13ce2-417">概念スキーマ定義言語の **EntitySet** 要素は、エンティティ型のインスタンスと、そのエンティティ型から派生した任意の型のインスタンスの論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="13ce2-417">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="13ce2-418">エンティティ型とエンティティ セットの間のリレーションシップは、リレーショナル データベースの行とテーブルの間のリレーションシップと似ています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-418">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="13ce2-419">エンティティ型は、行と同様に関連データのセットを定義し、エンティティ セットには、テーブルと同様に、その定義のインスタンスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-419">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="13ce2-420">エンティティ セットは、エンティティ型のインスタンスをグループ化するための構造を提供します。これにより、データ ソース内の関連するデータ構造に、エンティティ型のインスタンスをマッピングできるようになります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-420">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="13ce2-421">特定のエンティティ型に対して複数のエンティティ セットを定義できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-421">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="13ce2-422">EF デザイナーでは、型ごとに複数のエンティティセットを含む概念モデルはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-422">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="13ce2-423">**EntitySet**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-423">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-424">Documentation 要素 (0 個または 1 個の要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-424">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="13ce2-425">Annotation 要素 (0 個以上の要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-425">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-426">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-426">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-427">次の表は、 **EntitySet** 要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-427">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="13ce2-428">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-428">Attribute Name</span></span> | <span data-ttu-id="13ce2-429">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-429">Is Required</span></span> | <span data-ttu-id="13ce2-430">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-430">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-431">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-431">**Name**</span></span>       | <span data-ttu-id="13ce2-432">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-432">Yes</span></span>         | <span data-ttu-id="13ce2-433">エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-433">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="13ce2-434">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="13ce2-434">**EntityType**</span></span> | <span data-ttu-id="13ce2-435">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-435">Yes</span></span>         | <span data-ttu-id="13ce2-436">エンティティ型の完全修飾名。そのインスタンスは、エンティティ セットに格納されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-436">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-437">**EntitySet**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-437">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="13ce2-438">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-438">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-439">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-439">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-440">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-440">Example</span></span>

<span data-ttu-id="13ce2-441">次の例は、3つの**EntitySet**要素を持つ**EntityContainer**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-441">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

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
 

<span data-ttu-id="13ce2-442">型ごとに複数のエンティティ セット (Multiple-Entity-Sets-per-Type: MEST) を定義できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-442">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="13ce2-443">次の例では、 **Book** エンティティ型に対して2つのエンティティセットを持つエンティティコンテナーを定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-443">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

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
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="13ce2-444">EntityType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-444">EntityType Element (CSDL)</span></span>

<span data-ttu-id="13ce2-445">**EntityType**要素は、概念モデルの最上位レベルの概念 (顧客や注文など) の構造を表します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-445">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="13ce2-446">エンティティ型は、アプリケーションのエンティティ型インスタンス用テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="13ce2-446">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="13ce2-447">各テンプレートには、次の情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-447">Each template contains the following information:</span></span>

-   <span data-ttu-id="13ce2-448">一意の名前 </span><span class="sxs-lookup"><span data-stu-id="13ce2-448">A unique name.</span></span> <span data-ttu-id="13ce2-449">(必須) </span><span class="sxs-lookup"><span data-stu-id="13ce2-449">(Required.)</span></span>
-   <span data-ttu-id="13ce2-450">1 つ以上のプロパティにより定義されるエンティティ キー </span><span class="sxs-lookup"><span data-stu-id="13ce2-450">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="13ce2-451">(必須) </span><span class="sxs-lookup"><span data-stu-id="13ce2-451">(Required.)</span></span>
-   <span data-ttu-id="13ce2-452">データ格納用のプロパティ </span><span class="sxs-lookup"><span data-stu-id="13ce2-452">Properties for containing data.</span></span> <span data-ttu-id="13ce2-453">(省略可能) </span><span class="sxs-lookup"><span data-stu-id="13ce2-453">(Optional.)</span></span>
-   <span data-ttu-id="13ce2-454">アソシエーションの 1 つの End から別の End へのナビゲーションを可能にするナビゲーション プロパティ </span><span class="sxs-lookup"><span data-stu-id="13ce2-454">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="13ce2-455">(省略可能) </span><span class="sxs-lookup"><span data-stu-id="13ce2-455">(Optional.)</span></span>

<span data-ttu-id="13ce2-456">アプリケーションでは、エンティティ型のインスタンスが特定のオブジェクト (特定の顧客や注文など) を表します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-456">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="13ce2-457">エンティティ型の各インスタンスに対して、エンティティ セット内のエンティティ キーを一意にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-457">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="13ce2-458">2 つのエンティティ型のインスタンスは、型が同じであり、エンティティ キーの値が等しい場合にのみ、等価のインスタンスと見なされます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-458">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="13ce2-459">**EntityType**要素には、次の子要素を含めることができます (この順序で表示されます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-459">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-460">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-460">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-461">Key (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-461">Key (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-462">Property (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-462">Property (zero or more elements)</span></span>
-   <span data-ttu-id="13ce2-463">NavigationProperty (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-463">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="13ce2-464">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-464">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-465">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-465">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-466">次の表では、 **EntityType** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-466">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="13ce2-467">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-467">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="13ce2-468">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-468">Is Required</span></span> | <span data-ttu-id="13ce2-469">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-469">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-470">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-470">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="13ce2-471">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-471">Yes</span></span>         | <span data-ttu-id="13ce2-472">エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-472">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="13ce2-473">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="13ce2-473">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="13ce2-474">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-474">No</span></span>          | <span data-ttu-id="13ce2-475">定義するエンティティ型の基本の型である別のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-475">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="13ce2-476">**概要**</span><span class="sxs-lookup"><span data-stu-id="13ce2-476">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="13ce2-477">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-477">No</span></span>          | <span data-ttu-id="13ce2-478">エンティティ型が抽象型であるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-478">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="13ce2-479">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="13ce2-479">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="13ce2-480">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-480">No</span></span>          | <span data-ttu-id="13ce2-481">エンティティ型がオープンエンティティ型かどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-481">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="13ce2-482">> **OpenType** 属性は、ADO.NET Data Services で使用される概念モデルで定義されているエンティティ型にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-482">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="13ce2-483">任意の数の annotation 属性 (カスタム XML 属性) を **EntityType** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-483">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="13ce2-484">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-484">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-485">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-485">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-486">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-486">Example</span></span>

<span data-ttu-id="13ce2-487">次の例は、3つの**プロパティ**要素と2つの**NavigationProperty**要素を持つ**EntityType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-487">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

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
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="13ce2-488">EnumType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-488">EnumType Element (CSDL)</span></span>

<span data-ttu-id="13ce2-489">**EnumType**要素は列挙型を表します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-489">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="13ce2-490">**EnumType**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-490">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-491">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-491">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-492">Member (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-492">Member (zero or more elements)</span></span>
-   <span data-ttu-id="13ce2-493">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-493">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-494">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-494">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-495">次の表は、 **EnumType** 要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-495">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="13ce2-496">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-496">Attribute Name</span></span>     | <span data-ttu-id="13ce2-497">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-497">Is Required</span></span> | <span data-ttu-id="13ce2-498">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-498">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-499">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-499">**Name**</span></span>           | <span data-ttu-id="13ce2-500">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-500">Yes</span></span>         | <span data-ttu-id="13ce2-501">エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-501">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="13ce2-502">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="13ce2-502">**IsFlags**</span></span>        | <span data-ttu-id="13ce2-503">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-503">No</span></span>          | <span data-ttu-id="13ce2-504">列挙型をフラグのセットとして使用できるかどうかによって、 **True**または**False**。</span><span class="sxs-lookup"><span data-stu-id="13ce2-504">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="13ce2-505">既定値は **False です**。</span><span class="sxs-lookup"><span data-stu-id="13ce2-505">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="13ce2-506">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="13ce2-506">**UnderlyingType**</span></span> | <span data-ttu-id="13ce2-507">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-507">No</span></span>          | <span data-ttu-id="13ce2-508">**Edm. Byte**, Edm. **Int16**, edm. **Int32**, **Edm. Int64** または **edm. SByte** 型の値の範囲を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-508">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span> <span data-ttu-id="13ce2-509">列挙要素の基になる既定の型は、 **Edm. Int32.** です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-509">  The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-510">**EnumType**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-510">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="13ce2-511">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-511">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-512">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-512">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-513">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-513">Example</span></span>

<span data-ttu-id="13ce2-514">次の例は、3つの**メンバー**要素を持つ**EnumType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-514">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="13ce2-515">Function 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-515">Function Element (CSDL)</span></span>

<span data-ttu-id="13ce2-516">概念スキーマ定義言語 (CSDL) の **Function** 要素は、概念モデルで関数を定義または宣言するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-516">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="13ce2-517">関数は、DefiningExpression 要素を使用して定義されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-517">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="13ce2-518">**Function**要素には、次の子要素を含めることができます (順に記載されています)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-518">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-519">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-519">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-520">Parameter (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-520">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="13ce2-521">DefiningExpression (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-521">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-522">ReturnType (Function) (0 個または1個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-522">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-523">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-523">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="13ce2-524">関数の戻り値の型は、 **returntype** (function) 要素または **returntype** 属性 (下記参照) のいずれかで指定する必要がありますが、両方を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-524">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="13ce2-525">有効な戻り値の型には、EdmSimpleType、エンティティ型、複合型、行型、または参照型 (あるいはこれらの型のいずれかのコレクション) があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-525">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-526">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-526">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-527">次の表では、 **Function** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-527">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="13ce2-528">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-528">Attribute Name</span></span> | <span data-ttu-id="13ce2-529">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-529">Is Required</span></span> | <span data-ttu-id="13ce2-530">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-530">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="13ce2-531">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-531">**Name**</span></span>       | <span data-ttu-id="13ce2-532">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-532">Yes</span></span>         | <span data-ttu-id="13ce2-533">関数の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-533">The name of the function.</span></span>          |
| <span data-ttu-id="13ce2-534">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="13ce2-534">**ReturnType**</span></span> | <span data-ttu-id="13ce2-535">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-535">No</span></span>          | <span data-ttu-id="13ce2-536">関数の戻り値の型。</span><span class="sxs-lookup"><span data-stu-id="13ce2-536">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-537">**関数**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-537">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="13ce2-538">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-538">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-539">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-539">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-540">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-540">Example</span></span>

<span data-ttu-id="13ce2-541">次の例では、 **関数** 要素を使用して、インストラクターが雇用されてからの年数を返す関数を定義しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-541">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="13ce2-542">FunctionImport 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-542">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="13ce2-543">概念スキーマ定義言語 (CSDL) の **FunctionImport** 要素は、データソースで定義されているものの、概念モデルを通じてオブジェクトで使用できる関数を表します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-543">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="13ce2-544">たとえば、ストレージ モデルの Function 要素を使用して、データベースのストアド プロシージャを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-544">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="13ce2-545">概念モデルの **FunctionImport** 要素は、Entity Framework アプリケーション内の対応する関数を表し、FunctionImportMapping 要素を使用してストレージモデル関数にマップされます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-545">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="13ce2-546">関数がアプリケーションで呼び出されると、対応するストアド プロシージャがデータベースで実行されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-546">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="13ce2-547">**FunctionImport**要素には、次の子要素を含めることができます (この順序で表示されます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-547">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-548">Documentation (0 または 1 つの要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-548">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="13ce2-549">Parameter (0 個以上の要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-549">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="13ce2-550">Annotation 要素 (0 個以上の要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-550">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="13ce2-551">ReturnType (FunctionImport) (0 個以上の要素を使用できます)</span><span class="sxs-lookup"><span data-stu-id="13ce2-551">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="13ce2-552">関数が受け入れるパラメーターごとに1つの **パラメーター** 要素を定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-552">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="13ce2-553">関数の戻り値の型は、 **returntype** (FunctionImport) 要素または **returntype** 属性 (下記参照) のいずれかで指定する必要がありますが、両方を指定することはできません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-553">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="13ce2-554">戻り値の型の値は、EdmSimpleType、EntityType、または ComplexType のコレクションである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-554">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-555">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-555">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-556">次の表は、 **FunctionImport** 要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-556">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="13ce2-557">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-557">Attribute Name</span></span>   | <span data-ttu-id="13ce2-558">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-558">Is Required</span></span> | <span data-ttu-id="13ce2-559">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-559">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-560">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-560">**Name**</span></span>         | <span data-ttu-id="13ce2-561">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-561">Yes</span></span>         | <span data-ttu-id="13ce2-562">インポートされる関数の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-562">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="13ce2-563">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="13ce2-563">**ReturnType**</span></span>   | <span data-ttu-id="13ce2-564">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-564">No</span></span>          | <span data-ttu-id="13ce2-565">関数が返す型。</span><span class="sxs-lookup"><span data-stu-id="13ce2-565">The type that the function returns.</span></span> <span data-ttu-id="13ce2-566">関数が値を返さない場合は、この属性を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-566">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="13ce2-567">それ以外の場合、値は ComplexType、EntityType、または EDMSimpleType のコレクションである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-567">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="13ce2-568">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="13ce2-568">**EntitySet**</span></span>    | <span data-ttu-id="13ce2-569">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-569">No</span></span>          | <span data-ttu-id="13ce2-570">関数がエンティティ型のコレクションを返す場合、 **EntitySet** の値は、コレクションが属するエンティティセットである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-570">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="13ce2-571">そうでない場合は、 **EntitySet** 属性を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-571">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="13ce2-572">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="13ce2-572">**IsComposable**</span></span> | <span data-ttu-id="13ce2-573">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-573">No</span></span>          | <span data-ttu-id="13ce2-574">値が true に設定されている場合、関数はコンポーザブル (テーブル値関数) であり、LINQ クエリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-574">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span><span data-ttu-id="13ce2-575">既定値は **false** です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-575">  The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="13ce2-576">任意の数の annotation 属性 (カスタム XML 属性) を **FunctionImport** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-576">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="13ce2-577">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-577">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-578">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-578">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-579">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-579">Example</span></span>

<span data-ttu-id="13ce2-580">次の例は、1つのパラメーターを受け取り、エンティティ型のコレクションを返す **FunctionImport** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-580">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="13ce2-581">key 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-581">Key Element (CSDL)</span></span>

<span data-ttu-id="13ce2-582">**Key**要素は EntityType 要素の子要素であり、*エンティティキー* (id を決定するエンティティ型のプロパティまたはプロパティのセット) を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-582">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="13ce2-583">エンティティ キーを構成するプロパティは、デザイン時に選択されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-583">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="13ce2-584">エンティティ キー プロパティの値は、実行時のエンティティ セット内のエンティティ型のインスタンスを一意に識別する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-584">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="13ce2-585">エンティティ キーを構成するプロパティには、エンティティ セット内のインスタンスの一意性を保証するものを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-585">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="13ce2-586">**Key**要素は、エンティティ型の1つ以上のプロパティを参照することによって、エンティティキーを定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-586">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="13ce2-587">**Key**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-587">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="13ce2-588">PropertyRef (1 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-588">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="13ce2-589">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-589">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-590">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-590">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-591">任意の数の annotation 属性 (カスタム XML 属性) を **キー** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-591">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="13ce2-592">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-592">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-593">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-593">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="13ce2-594">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-594">Example</span></span>

<span data-ttu-id="13ce2-595">次の例では、 **Book**という名前のエンティティ型を定義しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-595">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="13ce2-596">エンティティキーは、エンティティ型の **ISBN** プロパティを参照することによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-596">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="13ce2-597">**Isbn**プロパティは、国際標準書籍番号 (ISBN) が書籍を一意に識別するため、エンティティキーに適した選択肢です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-597">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="13ce2-598">次の例は、**名前**と**アドレス**の2つのプロパティで構成されるエンティティキーを持つエンティティ型 (**Author**) を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-598">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

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
 

<span data-ttu-id="13ce2-599">同じ名前の2人の作成者が同じアドレスに住んでいる可能性が低いため、エンティティキーには **名前** と **アドレス** を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="13ce2-599">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="13ce2-600">ただし、エンティティ キーのこの選択では、エンティティ セット内のエンティティ キーの一意性を絶対的に保証することはできません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-600">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="13ce2-601">この場合は、作成者を一意に識別するために使用できる、 **AuthorId**などのプロパティを追加することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="13ce2-601">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="13ce2-602">Member 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-602">Member Element (CSDL)</span></span>

<span data-ttu-id="13ce2-603">**Member**要素は EnumType 要素の子要素であり、列挙型のメンバーを定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-603">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-604">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-604">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-605">次の表は、 **FunctionImport** 要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-605">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="13ce2-606">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-606">Attribute Name</span></span> | <span data-ttu-id="13ce2-607">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-607">Is Required</span></span> | <span data-ttu-id="13ce2-608">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-608">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-609">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-609">**Name**</span></span>       | <span data-ttu-id="13ce2-610">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-610">Yes</span></span>         | <span data-ttu-id="13ce2-611">メンバーの名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-611">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="13ce2-612">**Value**</span><span class="sxs-lookup"><span data-stu-id="13ce2-612">**Value**</span></span>      | <span data-ttu-id="13ce2-613">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-613">No</span></span>          | <span data-ttu-id="13ce2-614">メンバーの値。</span><span class="sxs-lookup"><span data-stu-id="13ce2-614">The value of the member.</span></span> <span data-ttu-id="13ce2-615">既定では、最初のメンバーの値は0で、連続する各列挙子の値は1ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-615">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="13ce2-616">同じ値を持つ複数のメンバーが存在する場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-616">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-617">任意の数の annotation 属性 (カスタム XML 属性) を **FunctionImport** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-617">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="13ce2-618">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-618">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-619">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-620">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-620">Example</span></span>

<span data-ttu-id="13ce2-621">次の例は、3つの**メンバー**要素を持つ**EnumType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-621">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="13ce2-622">NavigationProperty 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-622">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="13ce2-623">**NavigationProperty**要素は、アソシエーションのもう一方の end への参照を提供するナビゲーションプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-623">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="13ce2-624">Property 要素で定義されるプロパティとは異なり、ナビゲーション プロパティはデータの形と特性を定義しません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-624">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="13ce2-625">ナビゲーション プロパティは、アソシエーション内で 2 つのエンティティ型間を移動するための手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-625">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="13ce2-626">ナビゲーション プロパティは、アソシエーション End の両方のエンティティ型で省略可能です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-626">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="13ce2-627">1 つのアソシエーション End のエンティティ型にナビゲーション プロパティを定義した場合に、そのアソシエーションの他方の End でもエンティティ型にナビゲーション プロパティを定義する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-627">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="13ce2-628">ナビゲーション プロパティによって返されるデータ型は、リモートのアソシエーション End の多重度により決まります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-628">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="13ce2-629">たとえば、ナビゲーションプロパティ **Ordersnavprop**が **customer** エンティティ型に存在し、 **customer** と **Order**の間に一対多のアソシエーションを移動するとします。</span><span class="sxs-lookup"><span data-stu-id="13ce2-629">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="13ce2-630">ナビゲーションプロパティのリモートアソシエーション end は多重度が多 () であるため \* 、そのデータ型はコレクション ( **順序**) です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-630">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="13ce2-631">同様に、ナビゲーションプロパティの Customer navigation **prop**が **Order** エンティティ型に存在する場合、リモート end の多重度は 1 (1) であるため、データ型は **顧客** になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-631">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="13ce2-632">**NavigationProperty**要素には、次の子要素を含めることができます (この順序で表示されます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-632">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-633">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-633">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-634">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-634">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-635">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-635">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-636">次の表では、 **NavigationProperty** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-636">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="13ce2-637">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-637">Attribute Name</span></span>   | <span data-ttu-id="13ce2-638">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-638">Is Required</span></span> | <span data-ttu-id="13ce2-639">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-639">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-640">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-640">**Name**</span></span>         | <span data-ttu-id="13ce2-641">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-641">Yes</span></span>         | <span data-ttu-id="13ce2-642">ナビゲーション プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-642">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="13ce2-643">**リレーションシップ**</span><span class="sxs-lookup"><span data-stu-id="13ce2-643">**Relationship**</span></span> | <span data-ttu-id="13ce2-644">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-644">Yes</span></span>         | <span data-ttu-id="13ce2-645">モデルのスコープ内にあるアソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-645">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="13ce2-646">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="13ce2-646">**ToRole**</span></span>       | <span data-ttu-id="13ce2-647">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-647">Yes</span></span>         | <span data-ttu-id="13ce2-648">ナビゲーションが終了する側のアソシエーション End。</span><span class="sxs-lookup"><span data-stu-id="13ce2-648">The end of the association at which navigation ends.</span></span> <span data-ttu-id="13ce2-649">**Torole**属性の値は、アソシエーション end のいずれかで定義されている**ロール**属性の1つの値と同じである必要があります (associationend 要素で定義されています)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-649">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="13ce2-650">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="13ce2-650">**FromRole**</span></span>     | <span data-ttu-id="13ce2-651">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-651">Yes</span></span>         | <span data-ttu-id="13ce2-652">ナビゲーションが始まる側のアソシエーション End。</span><span class="sxs-lookup"><span data-stu-id="13ce2-652">The end of the association from which navigation begins.</span></span> <span data-ttu-id="13ce2-653">**Fromrole**属性の値は、アソシエーション end のいずれかで定義されているいずれかの**ロール**属性の値と同じである必要があります (associationend 要素で定義されています)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-653">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-654">任意の数の annotation 属性 (カスタム XML 属性) を **NavigationProperty** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-654">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="13ce2-655">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-655">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-656">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-656">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-657">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-657">Example</span></span>

<span data-ttu-id="13ce2-658">次の例では、2つのナビゲーションプロパティ (**publishedby** **writ)** を持つエンティティ型 (**Book**) を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-658">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

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
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="13ce2-659">OnDelete 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-659">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="13ce2-660">概念スキーマ定義言語 (CSDL) の **OnDelete** 要素は、アソシエーションに関連付けられている動作を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-660">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="13ce2-661">**アクション**属性がアソシエーションの一方の End で**Cascade**に設定されている場合、最初の end のエンティティ型が削除されると、アソシエーションのもう一方の end に関連するエンティティ型が削除されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-661">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="13ce2-662">2つのエンティティ型間のアソシエーションが主キーと主キーのリレーションシップである場合、アソシエーションのもう一方の end のプリンシパルオブジェクトが **OnDelete** の仕様に関係なく削除されると、読み込まれた依存オブジェクトが削除されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-662">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="13ce2-663">**OnDelete**要素は、アプリケーションの実行時の動作にのみ影響します。データソースの動作には影響しません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-663">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="13ce2-664">データ ソースで定義された動作は、アプリケーションで定義された動作と同じである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-664">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="13ce2-665">**OnDelete**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-665">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-666">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-666">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-667">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-667">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-668">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-668">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-669">次の表は、 **OnDelete** 要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-669">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="13ce2-670">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-670">Attribute Name</span></span> | <span data-ttu-id="13ce2-671">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-671">Is Required</span></span> | <span data-ttu-id="13ce2-672">[値]</span><span class="sxs-lookup"><span data-stu-id="13ce2-672">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-673">**操作**</span><span class="sxs-lookup"><span data-stu-id="13ce2-673">**Action**</span></span>     | <span data-ttu-id="13ce2-674">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-674">Yes</span></span>         | <span data-ttu-id="13ce2-675">**Cascade** または **None**。</span><span class="sxs-lookup"><span data-stu-id="13ce2-675">**Cascade** or **None**.</span></span> <span data-ttu-id="13ce2-676">**Cascade**を指定すると、プリンシパルエンティティ型が削除されるときに依存エンティティ型が削除されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-676">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="13ce2-677">存在 **しない場合、** プリンシパルエンティティ型が削除されても依存エンティティ型は削除されません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-677">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-678">任意の数の annotation 属性 (カスタム XML 属性) を **Association** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-678">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="13ce2-679">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-679">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-680">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-680">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-681">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-681">Example</span></span>

<span data-ttu-id="13ce2-682">次の例は、 **CustomerOrders**の関連付けを定義する**association**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-682">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="13ce2-683">**OnDelete**要素は、特定の**顧客**に関連付けられているすべての**注文**が ObjectContext に読み込まれていることを示します。これは、**顧客**が削除されると削除されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-683">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="13ce2-684">Parameter 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-684">Parameter Element (CSDL)</span></span>

<span data-ttu-id="13ce2-685">概念スキーマ定義言語 (CSDL) の **Parameter** 要素は、FunctionImport 要素または Function 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-685">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="13ce2-686">FunctionImport 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="13ce2-686">FunctionImport Element Application</span></span>

<span data-ttu-id="13ce2-687">**パラメーター**要素 ( **FunctionImport**要素の子として) は、CSDL で宣言された関数インポートの入力パラメーターと出力パラメーターを定義するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-687">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="13ce2-688">**Parameter**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-688">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-689">Documentation (0 または 1 つの要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-689">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="13ce2-690">Annotation 要素 (0 個以上の要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-690">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-691">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-691">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-692">次の表では、 **Parameter** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-692">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="13ce2-693">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-693">Attribute Name</span></span> | <span data-ttu-id="13ce2-694">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-694">Is Required</span></span> | <span data-ttu-id="13ce2-695">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-695">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-696">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-696">**Name**</span></span>       | <span data-ttu-id="13ce2-697">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-697">Yes</span></span>         | <span data-ttu-id="13ce2-698">パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-698">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="13ce2-699">**Type**</span><span class="sxs-lookup"><span data-stu-id="13ce2-699">**Type**</span></span>       | <span data-ttu-id="13ce2-700">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-700">Yes</span></span>         | <span data-ttu-id="13ce2-701">パラメーターの型。</span><span class="sxs-lookup"><span data-stu-id="13ce2-701">The parameter type.</span></span> <span data-ttu-id="13ce2-702">この値には、 **EDMSimpleType** か、モデルのスコープ内にある複合型を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-702">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="13ce2-703">**モード**</span><span class="sxs-lookup"><span data-stu-id="13ce2-703">**Mode**</span></span>       | <span data-ttu-id="13ce2-704">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-704">No</span></span>          | <span data-ttu-id="13ce2-705">**In**、 **Out**、または **InOut** は、パラメーターが入力パラメーター、出力パラメーター、または入力/出力パラメーターのどちらであるかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-705">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="13ce2-706">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-706">**MaxLength**</span></span>  | <span data-ttu-id="13ce2-707">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-707">No</span></span>          | <span data-ttu-id="13ce2-708">パラメーターの許容される最大長</span><span class="sxs-lookup"><span data-stu-id="13ce2-708">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="13ce2-709">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="13ce2-709">**Precision**</span></span>  | <span data-ttu-id="13ce2-710">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-710">No</span></span>          | <span data-ttu-id="13ce2-711">パラメーターの説明</span><span class="sxs-lookup"><span data-stu-id="13ce2-711">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="13ce2-712">**スケール**</span><span class="sxs-lookup"><span data-stu-id="13ce2-712">**Scale**</span></span>      | <span data-ttu-id="13ce2-713">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-713">No</span></span>          | <span data-ttu-id="13ce2-714">パラメーターの小数点以下桁数</span><span class="sxs-lookup"><span data-stu-id="13ce2-714">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="13ce2-715">**SRID**</span><span class="sxs-lookup"><span data-stu-id="13ce2-715">**SRID**</span></span>       | <span data-ttu-id="13ce2-716">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-716">No</span></span>          | <span data-ttu-id="13ce2-717">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="13ce2-717">Spatial System Reference Identifier.</span></span> <span data-ttu-id="13ce2-718">空間型のパラメーターに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-718">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="13ce2-719">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-719">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-720">**パラメーター**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-720">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="13ce2-721">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-721">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-722">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-722">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="13ce2-723">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-723">Example</span></span>

<span data-ttu-id="13ce2-724">次の例は、1つの**Parameter**子要素を持つ**FunctionImport**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-724">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="13ce2-725">関数は、1 つの入力パラメーターを受け取り、エンティティ型のコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-725">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="13ce2-726">Function 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="13ce2-726">Function Element Application</span></span>

<span data-ttu-id="13ce2-727">**Parameter**要素 ( **Function**要素の子として) は、概念モデルで定義または宣言されている関数のパラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-727">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="13ce2-728">**Parameter**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-728">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-729">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-729">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="13ce2-730">CollectionType (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-730">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="13ce2-731">ReferenceType (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-731">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="13ce2-732">RowType (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-732">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="13ce2-733">**CollectionType**、 **ReferenceType**、または**RowType**要素のいずれか1つだけを**Property**要素の子要素にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-733">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="13ce2-734">Annotation 要素 (0 個以上の要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-734">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="13ce2-735">Annotation 要素は、それ以外のすべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-735">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="13ce2-736">Annotation 要素は、CSDL v2 以降でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-736">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-737">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-737">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-738">次の表では、 **Parameter** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-738">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="13ce2-739">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-739">Attribute Name</span></span>   | <span data-ttu-id="13ce2-740">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-740">Is Required</span></span> | <span data-ttu-id="13ce2-741">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-741">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-742">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-742">**Name**</span></span>         | <span data-ttu-id="13ce2-743">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-743">Yes</span></span>         | <span data-ttu-id="13ce2-744">パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-744">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="13ce2-745">**型**</span><span class="sxs-lookup"><span data-stu-id="13ce2-745">**Type**</span></span>         | <span data-ttu-id="13ce2-746">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-746">No</span></span>          | <span data-ttu-id="13ce2-747">パラメーターの型。</span><span class="sxs-lookup"><span data-stu-id="13ce2-747">The parameter type.</span></span> <span data-ttu-id="13ce2-748">パラメーターには次のいずれかの型 (またはこれらの型のコレクション) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-748">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="13ce2-749">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="13ce2-749">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="13ce2-750">エンティティ型</span><span class="sxs-lookup"><span data-stu-id="13ce2-750">entity type</span></span> <br/> <span data-ttu-id="13ce2-751">複合型</span><span class="sxs-lookup"><span data-stu-id="13ce2-751">complex type</span></span> <br/> <span data-ttu-id="13ce2-752">行型</span><span class="sxs-lookup"><span data-stu-id="13ce2-752">row type</span></span> <br/> <span data-ttu-id="13ce2-753">参照型</span><span class="sxs-lookup"><span data-stu-id="13ce2-753">reference type</span></span>                             |
| <span data-ttu-id="13ce2-754">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="13ce2-754">**Nullable**</span></span>     | <span data-ttu-id="13ce2-755">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-755">No</span></span>          | <span data-ttu-id="13ce2-756">プロパティが**null**値を持つことができるかどうかによって、 **True** (既定値) または**False** 。</span><span class="sxs-lookup"><span data-stu-id="13ce2-756">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="13ce2-757">**既定**</span><span class="sxs-lookup"><span data-stu-id="13ce2-757">**DefaultValue**</span></span> | <span data-ttu-id="13ce2-758">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-758">No</span></span>          | <span data-ttu-id="13ce2-759">プロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-759">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="13ce2-760">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-760">**MaxLength**</span></span>    | <span data-ttu-id="13ce2-761">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-761">No</span></span>          | <span data-ttu-id="13ce2-762">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="13ce2-762">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="13ce2-763">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-763">**FixedLength**</span></span>  | <span data-ttu-id="13ce2-764">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-764">No</span></span>          | <span data-ttu-id="13ce2-765">プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-765">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="13ce2-766">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="13ce2-766">**Precision**</span></span>    | <span data-ttu-id="13ce2-767">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-767">No</span></span>          | <span data-ttu-id="13ce2-768">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="13ce2-768">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="13ce2-769">**スケール**</span><span class="sxs-lookup"><span data-stu-id="13ce2-769">**Scale**</span></span>        | <span data-ttu-id="13ce2-770">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-770">No</span></span>          | <span data-ttu-id="13ce2-771">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="13ce2-771">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="13ce2-772">**SRID**</span><span class="sxs-lookup"><span data-stu-id="13ce2-772">**SRID**</span></span>         | <span data-ttu-id="13ce2-773">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-773">No</span></span>          | <span data-ttu-id="13ce2-774">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="13ce2-774">Spatial System Reference Identifier.</span></span> <span data-ttu-id="13ce2-775">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-775">Valid only for properties of spatial types.</span></span> <span data-ttu-id="13ce2-776">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-776">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="13ce2-777">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="13ce2-777">**Unicode**</span></span>      | <span data-ttu-id="13ce2-778">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-778">No</span></span>          | <span data-ttu-id="13ce2-779">プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-779">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="13ce2-780">**Collation**</span><span class="sxs-lookup"><span data-stu-id="13ce2-780">**Collation**</span></span>    | <span data-ttu-id="13ce2-781">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-781">No</span></span>          | <span data-ttu-id="13ce2-782">データ ソースで使用される照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="13ce2-782">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="13ce2-783">**パラメーター**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-783">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="13ce2-784">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-784">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-785">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-785">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="13ce2-786">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-786">Example</span></span>

<span data-ttu-id="13ce2-787">次の例は、1つの**parameter**子要素を使用して関数パラメーターを定義する**関数**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-787">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a><span data-ttu-id="13ce2-788">Principal 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-788">Principal Element (CSDL)</span></span>

<span data-ttu-id="13ce2-789">概念スキーマ定義言語 (CSDL) の **principal** 要素は、参照制約のプリンシパル end を定義する、参照制約要素の子要素です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-789">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="13ce2-790">参照整合性の **制約要素は、リレーショナル** データベースの参照整合性制約に似た機能を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-790">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="13ce2-791">データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティが別のエンティティ型のエンティティ キーを参照できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-791">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="13ce2-792">参照されるエンティティ型は、制約の "*プリンシパル End*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-792">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="13ce2-793">プリンシパル End を参照するエンティティ型は、制約の "*依存 End*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-793">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="13ce2-794">**Propertyref** 要素は、依存 end によって参照されるキーを指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-794">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="13ce2-795">**Principal**要素には、次の子要素を含めることができます (順に記載されています)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-795">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-796">PropertyRef (1 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-796">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="13ce2-797">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-797">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-798">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-798">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-799">次の表では、 **Principal** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-799">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="13ce2-800">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-800">Attribute Name</span></span> | <span data-ttu-id="13ce2-801">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-801">Is Required</span></span> | <span data-ttu-id="13ce2-802">[値]</span><span class="sxs-lookup"><span data-stu-id="13ce2-802">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="13ce2-803">**ロール**</span><span class="sxs-lookup"><span data-stu-id="13ce2-803">**Role**</span></span>       | <span data-ttu-id="13ce2-804">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-804">Yes</span></span>         | <span data-ttu-id="13ce2-805">アソシエーションのプリンシパル End 上のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-805">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-806">**Principal**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-806">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="13ce2-807">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-807">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-808">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-808">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-809">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-809">Example</span></span>

<span data-ttu-id="13ce2-810">次の例では、 **publishedby**関連付けの定義の一部である **、オブジェクトの場所を示し**ます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-810">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="13ce2-811">参照に関する制約のプリンシパル end は、**パブリッシャー**エンティティ型の**Id**プロパティによって構成されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-811">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

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
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="13ce2-812">Property 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-812">Property Element (CSDL)</span></span>

<span data-ttu-id="13ce2-813">概念スキーマ定義言語 (CSDL) の **Property** 要素は、EntityType 要素、ComplexType 要素、または RowType 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-813">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="13ce2-814">EntityType 要素と ComplexType 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="13ce2-814">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="13ce2-815">**プロパティ** 要素 ( **EntityType** または **ComplexType** 要素の子として) は、エンティティ型インスタンスまたは複合型インスタンスに格納されるデータの構造と特性を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-815">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="13ce2-816">概念モデルのプロパティは、クラスで定義されるプロパティに似ています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-816">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="13ce2-817">クラスのプロパティがクラスの構造を定義し、オブジェクトに関する情報を伝達するのと同様に、概念モデルのプロパティはエンティティ型の構造を定義し、エンティティ型のインスタンスに関する情報を伝達します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-817">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="13ce2-818">**Property**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-818">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-819">Documentation 要素 (0 個または 1 個の要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-819">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="13ce2-820">Annotation 要素 (0 個以上の要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-820">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="13ce2-821">次のファセットは、 **プロパティ** 要素に適用できます。 **Nullable**、 **DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**、 **Collation**、 **ConcurrencyMode**。</span><span class="sxs-lookup"><span data-stu-id="13ce2-821">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="13ce2-822">ファセットは、プロパティ値をデータ ストアに格納する方法に関する情報を提供する XML 属性です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-822">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="13ce2-823">ファセットは、 **Edmsimpletype**型のプロパティにのみ適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-823">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-824">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-824">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-825">次の表では、 **Property** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-825">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="13ce2-826">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-826">Attribute Name</span></span>                                                         | <span data-ttu-id="13ce2-827">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-827">Is Required</span></span> | <span data-ttu-id="13ce2-828">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-828">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-829">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-829">**Name**</span></span>                                                               | <span data-ttu-id="13ce2-830">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-830">Yes</span></span>         | <span data-ttu-id="13ce2-831">プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-831">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="13ce2-832">**Type**</span><span class="sxs-lookup"><span data-stu-id="13ce2-832">**Type**</span></span>                                                               | <span data-ttu-id="13ce2-833">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-833">Yes</span></span>         | <span data-ttu-id="13ce2-834">プロパティ値の型です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-834">The type of the property value.</span></span> <span data-ttu-id="13ce2-835">プロパティ値の型は、 **EDMSimpleType** か、モデルのスコープ内にある (完全修飾名で表された) 複合型を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-835">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="13ce2-836">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="13ce2-836">**Nullable**</span></span>                                                           | <span data-ttu-id="13ce2-837">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-837">No</span></span>          | <span data-ttu-id="13ce2-838">プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または <strong>False</strong>。</span><span class="sxs-lookup"><span data-stu-id="13ce2-838">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="13ce2-839">CSDL v1 の > 複合型プロパティにはが必要 `Nullable="False"` です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-839">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="13ce2-840">**既定**</span><span class="sxs-lookup"><span data-stu-id="13ce2-840">**DefaultValue**</span></span>                                                       | <span data-ttu-id="13ce2-841">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-841">No</span></span>          | <span data-ttu-id="13ce2-842">プロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-842">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="13ce2-843">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-843">**MaxLength**</span></span>                                                          | <span data-ttu-id="13ce2-844">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-844">No</span></span>          | <span data-ttu-id="13ce2-845">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="13ce2-845">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="13ce2-846">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-846">**FixedLength**</span></span>                                                        | <span data-ttu-id="13ce2-847">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-847">No</span></span>          | <span data-ttu-id="13ce2-848">プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-848">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="13ce2-849">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="13ce2-849">**Precision**</span></span>                                                          | <span data-ttu-id="13ce2-850">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-850">No</span></span>          | <span data-ttu-id="13ce2-851">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="13ce2-851">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="13ce2-852">**スケール**</span><span class="sxs-lookup"><span data-stu-id="13ce2-852">**Scale**</span></span>                                                              | <span data-ttu-id="13ce2-853">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-853">No</span></span>          | <span data-ttu-id="13ce2-854">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="13ce2-854">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="13ce2-855">**SRID**</span><span class="sxs-lookup"><span data-stu-id="13ce2-855">**SRID**</span></span>                                                               | <span data-ttu-id="13ce2-856">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-856">No</span></span>          | <span data-ttu-id="13ce2-857">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="13ce2-857">Spatial System Reference Identifier.</span></span> <span data-ttu-id="13ce2-858">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-858">Valid only for properties of spatial types.</span></span> <span data-ttu-id="13ce2-859">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-859">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="13ce2-860">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="13ce2-860">**Unicode**</span></span>                                                            | <span data-ttu-id="13ce2-861">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-861">No</span></span>          | <span data-ttu-id="13ce2-862">プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-862">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="13ce2-863">**Collation**</span><span class="sxs-lookup"><span data-stu-id="13ce2-863">**Collation**</span></span>                                                          | <span data-ttu-id="13ce2-864">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-864">No</span></span>          | <span data-ttu-id="13ce2-865">データ ソースで使用される照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="13ce2-865">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="13ce2-866">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="13ce2-866">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="13ce2-867">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-867">No</span></span>          | <span data-ttu-id="13ce2-868">**None** (既定値) または **Fixed**。</span><span class="sxs-lookup"><span data-stu-id="13ce2-868">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="13ce2-869">値が **Fixed**に設定されている場合、プロパティ値はオプティミスティック コンカレンシーチェックで使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-869">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="13ce2-870">**プロパティ**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-870">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="13ce2-871">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-871">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-872">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-872">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="13ce2-873">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-873">Example</span></span>

<span data-ttu-id="13ce2-874">次の例は、3つの**Property**要素を持つ**EntityType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-874">The following example shows an **EntityType** element with three **Property** elements:</span></span>

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
 

<span data-ttu-id="13ce2-875">次の例は、5つの**Property**要素を持つ**ComplexType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-875">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="13ce2-876">RowType 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="13ce2-876">RowType Element Application</span></span>

<span data-ttu-id="13ce2-877">**RowType**要素の子として使用される**プロパティ**要素は、モデル定義関数に渡される、またはモデル定義関数から返されるデータの構造と特性を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-877">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="13ce2-878">**Property**要素には、次の子要素のうち1つだけを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-878">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="13ce2-879">CollectionType</span><span class="sxs-lookup"><span data-stu-id="13ce2-879">CollectionType</span></span>
-   <span data-ttu-id="13ce2-880">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="13ce2-880">ReferenceType</span></span>
-   <span data-ttu-id="13ce2-881">RowType</span><span class="sxs-lookup"><span data-stu-id="13ce2-881">RowType</span></span>

<span data-ttu-id="13ce2-882">**Property**要素は、任意の数の子注釈要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-882">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="13ce2-883">Annotation 要素は、CSDL v2 以降でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-883">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-884">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-884">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-885">次の表では、 **Property** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-885">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="13ce2-886">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-886">Attribute Name</span></span>                                                     | <span data-ttu-id="13ce2-887">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-887">Is Required</span></span> | <span data-ttu-id="13ce2-888">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-888">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-889">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-889">**Name**</span></span>                                                           | <span data-ttu-id="13ce2-890">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-890">Yes</span></span>         | <span data-ttu-id="13ce2-891">プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-891">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="13ce2-892">**Type**</span><span class="sxs-lookup"><span data-stu-id="13ce2-892">**Type**</span></span>                                                           | <span data-ttu-id="13ce2-893">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-893">Yes</span></span>         | <span data-ttu-id="13ce2-894">プロパティ値の型です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-894">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="13ce2-895">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="13ce2-895">**Nullable**</span></span>                                                       | <span data-ttu-id="13ce2-896">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-896">No</span></span>          | <span data-ttu-id="13ce2-897">プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または **False**。</span><span class="sxs-lookup"><span data-stu-id="13ce2-897">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="13ce2-898">CSDL v1 で > 複合型プロパティにはが必要 `Nullable="False"` です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-898">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="13ce2-899">**既定**</span><span class="sxs-lookup"><span data-stu-id="13ce2-899">**DefaultValue**</span></span>                                                   | <span data-ttu-id="13ce2-900">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-900">No</span></span>          | <span data-ttu-id="13ce2-901">プロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-901">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="13ce2-902">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-902">**MaxLength**</span></span>                                                      | <span data-ttu-id="13ce2-903">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-903">No</span></span>          | <span data-ttu-id="13ce2-904">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="13ce2-904">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="13ce2-905">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-905">**FixedLength**</span></span>                                                    | <span data-ttu-id="13ce2-906">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-906">No</span></span>          | <span data-ttu-id="13ce2-907">プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-907">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="13ce2-908">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="13ce2-908">**Precision**</span></span>                                                      | <span data-ttu-id="13ce2-909">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-909">No</span></span>          | <span data-ttu-id="13ce2-910">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="13ce2-910">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="13ce2-911">**スケール**</span><span class="sxs-lookup"><span data-stu-id="13ce2-911">**Scale**</span></span>                                                          | <span data-ttu-id="13ce2-912">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-912">No</span></span>          | <span data-ttu-id="13ce2-913">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="13ce2-913">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="13ce2-914">**SRID**</span><span class="sxs-lookup"><span data-stu-id="13ce2-914">**SRID**</span></span>                                                           | <span data-ttu-id="13ce2-915">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-915">No</span></span>          | <span data-ttu-id="13ce2-916">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="13ce2-916">Spatial System Reference Identifier.</span></span> <span data-ttu-id="13ce2-917">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-917">Valid only for properties of spatial types.</span></span> <span data-ttu-id="13ce2-918">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-918">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="13ce2-919">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="13ce2-919">**Unicode**</span></span>                                                        | <span data-ttu-id="13ce2-920">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-920">No</span></span>          | <span data-ttu-id="13ce2-921">プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-921">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="13ce2-922">**Collation**</span><span class="sxs-lookup"><span data-stu-id="13ce2-922">**Collation**</span></span>                                                      | <span data-ttu-id="13ce2-923">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-923">No</span></span>          | <span data-ttu-id="13ce2-924">データ ソースで使用される照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="13ce2-924">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="13ce2-925">**プロパティ**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-925">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="13ce2-926">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-926">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-927">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-927">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="13ce2-928">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-928">Example</span></span>

<span data-ttu-id="13ce2-929">次の例は、モデル定義関数の戻り値の型の形状を定義するために使用される **プロパティ** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-929">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

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
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="13ce2-930">PropertyRef 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-930">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="13ce2-931">概念スキーマ定義言語 (CSDL) の **Propertyref** 要素は、プロパティが次のいずれかのロールを実行することを示すために、エンティティ型のプロパティを参照します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-931">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="13ce2-932">エンティティのキー (ID を指定するエンティティ型のプロパティまたは一連のプロパティ) の一部。</span><span class="sxs-lookup"><span data-stu-id="13ce2-932">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="13ce2-933">1つ以上の **Propertyref** 要素を使用して、エンティティキーを定義できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-933">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="13ce2-934">参照制約の依存 End または プリンシパル End。</span><span class="sxs-lookup"><span data-stu-id="13ce2-934">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="13ce2-935">**Propertyref**要素には、子要素として annotation 要素 (0 個以上) のみを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-935">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="13ce2-936">Annotation 要素は、CSDL v2 以降でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-936">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-937">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-937">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-938">次の表では、 **Propertyref** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-938">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="13ce2-939">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-939">Attribute Name</span></span> | <span data-ttu-id="13ce2-940">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-940">Is Required</span></span> | <span data-ttu-id="13ce2-941">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-941">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="13ce2-942">**名前**</span><span class="sxs-lookup"><span data-stu-id="13ce2-942">**Name**</span></span>       | <span data-ttu-id="13ce2-943">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-943">Yes</span></span>         | <span data-ttu-id="13ce2-944">参照されているプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-944">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-945">**Propertyref**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-945">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="13ce2-946">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-946">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-947">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-947">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-948">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-948">Example</span></span>

<span data-ttu-id="13ce2-949">次の例では、エンティティ型 (**Book**) を定義しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-949">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="13ce2-950">エンティティキーは、エンティティ型の **ISBN** プロパティを参照することによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-950">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="13ce2-951">次の例では、2つの **Propertyref** 要素を使用して、2つのプロパティ (**Id** と **PublisherId**) が参照に関する制約のプリンシパルと依存 end であることを示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-951">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

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
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="13ce2-952">ReferenceType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-952">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="13ce2-953">概念スキーマ定義言語 (CSDL) の **ReferenceType** 要素は、エンティティ型への参照を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-953">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="13ce2-954">**ReferenceType**要素は、次の要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-954">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="13ce2-955">ReturnType (関数)</span><span class="sxs-lookup"><span data-stu-id="13ce2-955">ReturnType (Function)</span></span>
-   <span data-ttu-id="13ce2-956">パラメーター</span><span class="sxs-lookup"><span data-stu-id="13ce2-956">Parameter</span></span>
-   <span data-ttu-id="13ce2-957">CollectionType</span><span class="sxs-lookup"><span data-stu-id="13ce2-957">CollectionType</span></span>

<span data-ttu-id="13ce2-958">**ReferenceType**要素は、関数のパラメーターまたは戻り値の型を定義するときに使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-958">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="13ce2-959">**ReferenceType**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-959">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-960">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-960">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-961">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-961">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-962">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-962">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-963">次の表は、 **ReferenceType** 要素に適用できる属性を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-963">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="13ce2-964">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-964">Attribute Name</span></span> | <span data-ttu-id="13ce2-965">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-965">Is Required</span></span> | <span data-ttu-id="13ce2-966">[値]</span><span class="sxs-lookup"><span data-stu-id="13ce2-966">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="13ce2-967">**Type**</span><span class="sxs-lookup"><span data-stu-id="13ce2-967">**Type**</span></span>       | <span data-ttu-id="13ce2-968">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-968">Yes</span></span>         | <span data-ttu-id="13ce2-969">参照先エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-969">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-970">**ReferenceType**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-970">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="13ce2-971">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-971">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-972">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-972">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-973">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-973">Example</span></span>

<span data-ttu-id="13ce2-974">次の例は、 **Person**エンティティ型への参照を受け入れるモデル定義関数で、 **Parameter**要素の子として使用される**ReferenceType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-974">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

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
 

<span data-ttu-id="13ce2-975">次の例は、 **Person**エンティティ型への参照を返すモデル定義関数で、 **ReturnType** (Function) 要素の子として使用される**ReferenceType**要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-975">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

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
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="13ce2-976">ReferentialConstraint 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-976">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="13ce2-977">概念スキーマ定義言語 (CSDL) の参照整合性制約要素は、リレーショナルデータベースの参照整合性制約に似た機能を **定義します** 。</span><span class="sxs-lookup"><span data-stu-id="13ce2-977">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="13ce2-978">データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティが別のエンティティ型のエンティティ キーを参照できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-978">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="13ce2-979">参照されるエンティティ型は、制約の "*プリンシパル End*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-979">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="13ce2-980">プリンシパル End を参照するエンティティ型は、制約の "*依存 End*" と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-980">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="13ce2-981">あるエンティティ型で公開されている外部キーが別のエンティティ型のプロパティを参照している場合、参照要素は2つのエンティティ型の間の関連付けを **定義します** 。</span><span class="sxs-lookup"><span data-stu-id="13ce2-981">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="13ce2-982">参照要素 **は2つのエンティティ** 型の関連付けに関する情報を提供するため、対応する AssociationSetMapping 要素はマッピングスキーマ言語 (MSL) には必要ありません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-982">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="13ce2-983">外部キーが公開されていない2つのエンティティ型間のアソシエーションには、関連情報をデータソースにマップするために、対応する **AssociationSetMapping** 要素が必要です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-983">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="13ce2-984">エンティティ型で外部キーが公開されていない場合 **は、エンティティ** 型と別のエンティティ型との間の主キーと主キーの制約のみを指定できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-984">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="13ce2-985">要素には、次の子 **要素を含める** ことができます (順に表示されます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-985">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-986">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-986">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-987">Principal (1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-987">Principal (exactly one element)</span></span>
-   <span data-ttu-id="13ce2-988">Dependent (1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-988">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="13ce2-989">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-989">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-990">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-990">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-991">この **要素には、** 任意の数の annotation 属性 (カスタム XML 属性) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-991">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="13ce2-992">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-992">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-993">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-993">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="13ce2-994">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-994">Example</span></span>

<span data-ttu-id="13ce2-995">次の例では、 **publishedby**関連付けの定義の一部として使用されている **、オブジェクトの**場所を示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-995">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

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
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="13ce2-996">ReturnType (Function) 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-996">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="13ce2-997">概念スキーマ定義言語 (CSDL) の **ReturnType** (function) 要素は、function 要素で定義されている関数の戻り値の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-997">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="13ce2-998">関数の戻り値の型は、 **ReturnType** 属性と共に指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-998">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="13ce2-999">戻り値の型には、 **Edmsimpletype**、エンティティ型、複合型、行型、参照型、またはこれらの型のいずれかのコレクションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-999">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="13ce2-1000">関数の戻り値の型は、 **ReturnType** (function) 要素の**type**属性か、次のいずれかの子要素を使用して指定できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1000">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="13ce2-1001">CollectionType</span><span class="sxs-lookup"><span data-stu-id="13ce2-1001">CollectionType</span></span>
-   <span data-ttu-id="13ce2-1002">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="13ce2-1002">ReferenceType</span></span>
-   <span data-ttu-id="13ce2-1003">RowType</span><span class="sxs-lookup"><span data-stu-id="13ce2-1003">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="13ce2-1004">**ReturnType** (function) 要素の**type**属性と子要素の両方を持つ関数の戻り値の型を指定した場合、モデルは検証されません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1004">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-1005">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-1005">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-1006">次の表では、 **ReturnType** (Function) 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1006">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="13ce2-1007">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-1007">Attribute Name</span></span> | <span data-ttu-id="13ce2-1008">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-1008">Is Required</span></span> | <span data-ttu-id="13ce2-1009">[値]</span><span class="sxs-lookup"><span data-stu-id="13ce2-1009">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="13ce2-1010">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1010">**ReturnType**</span></span> | <span data-ttu-id="13ce2-1011">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1011">No</span></span>          | <span data-ttu-id="13ce2-1012">関数の戻り値の型。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1012">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-1013">任意の数の annotation 属性 (カスタム XML 属性) を **ReturnType** (Function) 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1013">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="13ce2-1014">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1014">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-1015">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1015">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-1016">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-1016">Example</span></span>

<span data-ttu-id="13ce2-1017">次の例では、 **関数** 要素を使用して、書籍が印刷された年数を返す関数を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1017">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="13ce2-1018">戻り値の型は、 **ReturnType** (Function) 要素の**type**属性によって指定されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1018">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="13ce2-1019">ReturnType (FunctionImport) 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1019">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="13ce2-1020">概念スキーマ定義言語 (CSDL) の **ReturnType** (FunctionImport) 要素は、FunctionImport 要素で定義されている関数の戻り値の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1020">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="13ce2-1021">関数の戻り値の型は、 **ReturnType** 属性と共に指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1021">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="13ce2-1022">戻り値の型は、エンティティ型、複合型、または **Edmsimpletype**の任意のコレクションにすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1022">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="13ce2-1023">関数の戻り値の型は、 **ReturnType** (FunctionImport) 要素の**type**属性と共に指定されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1023">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-1024">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-1024">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-1025">次の表では、 **ReturnType** (FunctionImport) 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1025">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="13ce2-1026">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-1026">Attribute Name</span></span> | <span data-ttu-id="13ce2-1027">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-1027">Is Required</span></span> | <span data-ttu-id="13ce2-1028">[値]</span><span class="sxs-lookup"><span data-stu-id="13ce2-1028">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-1029">**型**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1029">**Type**</span></span>       | <span data-ttu-id="13ce2-1030">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1030">No</span></span>          | <span data-ttu-id="13ce2-1031">関数が返す型。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1031">The type that the function returns.</span></span> <span data-ttu-id="13ce2-1032">値は、ComplexType、EntityType、または EDMSimpleType のコレクションである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1032">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="13ce2-1033">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1033">**EntitySet**</span></span>  | <span data-ttu-id="13ce2-1034">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1034">No</span></span>          | <span data-ttu-id="13ce2-1035">関数がエンティティ型のコレクションを返す場合、 **EntitySet** の値は、コレクションが属するエンティティセットである必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1035">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="13ce2-1036">そうでない場合は、 **EntitySet** 属性を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1036">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-1037">任意の数の annotation 属性 (カスタム XML 属性) を **ReturnType** (FunctionImport) 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1037">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="13ce2-1038">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1038">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-1039">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1039">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-1040">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-1040">Example</span></span>

<span data-ttu-id="13ce2-1041">次の例では、書籍と出版社を返す **FunctionImport** を使用します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1041">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="13ce2-1042">関数は2つの結果セットを返すため、2つの **ReturnType** (FunctionImport) 要素が指定されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1042">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="13ce2-1043">RowType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1043">RowType Element (CSDL)</span></span>

<span data-ttu-id="13ce2-1044">概念スキーマ定義言語 (CSDL) の **RowType** 要素は、概念モデルで定義されている関数のパラメーターまたは戻り値の型として名前のない構造体を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1044">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="13ce2-1045">**RowType**要素は、次の要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1045">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="13ce2-1046">CollectionType</span><span class="sxs-lookup"><span data-stu-id="13ce2-1046">CollectionType</span></span>
-   <span data-ttu-id="13ce2-1047">パラメーター</span><span class="sxs-lookup"><span data-stu-id="13ce2-1047">Parameter</span></span>
-   <span data-ttu-id="13ce2-1048">ReturnType (関数)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1048">ReturnType (Function)</span></span>

<span data-ttu-id="13ce2-1049">**RowType**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1049">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-1050">Property (1 個以上)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1050">Property (one or more)</span></span>
-   <span data-ttu-id="13ce2-1051">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1051">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-1052">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-1052">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-1053">**RowType**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1053">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="13ce2-1054">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1054">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-1055">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1055">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="13ce2-1056">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-1056">Example</span></span>

<span data-ttu-id="13ce2-1057">次の例は、 **CollectionType** 要素を使用して、 **RowType** 要素で指定された行のコレクションを返すことを指定するモデル定義関数を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1057">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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

## <a name="schema-element-csdl"></a><span data-ttu-id="13ce2-1058">Schema 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1058">Schema Element (CSDL)</span></span>

<span data-ttu-id="13ce2-1059">**スキーマ**要素は、概念モデル定義のルート要素です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1059">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="13ce2-1060">概念モデルを構成するオブジェクト、関数、およびコンテナーの定義を格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1060">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="13ce2-1061">**Schema**要素には、次の子要素を0個以上含めることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1061">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="13ce2-1062">使用</span><span class="sxs-lookup"><span data-stu-id="13ce2-1062">Using</span></span>
-   <span data-ttu-id="13ce2-1063">EntityContainer 要素</span><span class="sxs-lookup"><span data-stu-id="13ce2-1063">EntityContainer</span></span>
-   <span data-ttu-id="13ce2-1064">EntityType</span><span class="sxs-lookup"><span data-stu-id="13ce2-1064">EntityType</span></span>
-   <span data-ttu-id="13ce2-1065">EnumType</span><span class="sxs-lookup"><span data-stu-id="13ce2-1065">EnumType</span></span>
-   <span data-ttu-id="13ce2-1066">関連付け</span><span class="sxs-lookup"><span data-stu-id="13ce2-1066">Association</span></span>
-   <span data-ttu-id="13ce2-1067">ComplexType</span><span class="sxs-lookup"><span data-stu-id="13ce2-1067">ComplexType</span></span>
-   <span data-ttu-id="13ce2-1068">関数</span><span class="sxs-lookup"><span data-stu-id="13ce2-1068">Function</span></span>

<span data-ttu-id="13ce2-1069">**スキーマ**要素には、0個または1個の Annotation 要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1069">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="13ce2-1070">**Function**要素と annotation 要素は、CSDL v2 以降でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1070">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="13ce2-1071">**Schema**要素は、**名前空間**属性を使用して、概念モデルのエンティティ型、複合型、およびアソシエーションオブジェクトの名前空間を定義します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1071">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="13ce2-1072">1 つの名前空間内で 2 つのオブジェクトが同じ名前を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1072">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="13ce2-1073">名前空間は、複数の **スキーマ** 要素と複数の .csdl ファイルにまたがることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1073">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="13ce2-1074">概念モデルの名前空間は、 **スキーマ** 要素の XML 名前空間とは異なります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1074">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="13ce2-1075">概念モデルの名前空間 ( **名前空間** 属性で定義) は、エンティティ型、複合型、およびアソシエーション型の論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1075">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="13ce2-1076">**Schema**要素の XML 名前空間 ( **xmlns**属性で示されます) は、 **schema**要素の子要素と属性の既定の名前空間です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1076">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="13ce2-1077">フォームの XML 名前空間 https://schemas.microsoft.com/ado/YYYY/MM/edm (YYYY と MM はそれぞれ年と月を表します) は、CSDL 用に予約されています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1077">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="13ce2-1078">カスタム要素と属性は、このフォームがある名前空間に存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1078">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-1079">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-1079">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-1080">次の表では、 **スキーマ** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1080">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="13ce2-1081">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-1081">Attribute Name</span></span> | <span data-ttu-id="13ce2-1082">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-1082">Is Required</span></span> | <span data-ttu-id="13ce2-1083">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-1083">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-1084">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1084">**Namespace**</span></span>  | <span data-ttu-id="13ce2-1085">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1085">Yes</span></span>         | <span data-ttu-id="13ce2-1086">概念モデルの名前空間。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1086">The namespace of the conceptual model.</span></span> <span data-ttu-id="13ce2-1087">**名前空間**属性の値は、型の完全修飾名を形成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1087">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="13ce2-1088">たとえば、 *Customer*という名前の**entitytype**が Simple. example. Model 名前空間にある場合、 **entitytype**の完全修飾名は simpleexamplemodel.customer です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1088">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="13ce2-1089">次の文字列は、 **Namespace** 属性の値として使用できません: **System**、 **Transient**、または **Edm**。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1089">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="13ce2-1090">**Namespace**属性の値を、SSDL Schema 要素の**namespace**属性の値と同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1090">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="13ce2-1091">**Alias**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1091">**Alias**</span></span>      | <span data-ttu-id="13ce2-1092">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1092">No</span></span>          | <span data-ttu-id="13ce2-1093">名前空間の名前の代わりに使用される識別子。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1093">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="13ce2-1094">たとえば、 *Customer*という名前の**entitytype**が Simple. example. model 名前空間にあり、 **Alias**属性の値が*model*の場合、 **entitytype**の完全修飾名として model. Customer を使用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1094">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="13ce2-1095">**スキーマ**要素には、任意の数の annotation 属性 (カスタム XML 属性) を適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1095">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="13ce2-1096">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1096">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-1097">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1097">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-1098">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-1098">Example</span></span>

<span data-ttu-id="13ce2-1099">**EntityContainer**要素、2つの**EntityType**要素、および1つの**Association**要素を含む**スキーマ**要素の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1099">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="13ce2-1100">TypeRef 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1100">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="13ce2-1101">概念スキーマ定義言語 (CSDL) の **TypeRef** 要素は、既存の名前付きの型への参照を提供します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1101">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="13ce2-1102">**TypeRef**要素は CollectionType 要素の子にすることができます。これは、関数がパラメーターまたは戻り値の型としてコレクションを持つことを指定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1102">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="13ce2-1103">**TypeRef**要素には、次の子要素を含めることができます (一覧の順序)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1103">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="13ce2-1104">Documentation (0 個または 1 個の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1104">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="13ce2-1105">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1105">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-1106">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-1106">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-1107">次の表では、 **TypeRef** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1107">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="13ce2-1108">**DefaultValue**、 **MaxLength**、 **FixedLength**、 **Precision**、 **Scale**、 **Unicode**、および**Collation**属性は、 **edmsimpletypes**にのみ適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1108">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="13ce2-1109">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-1109">Attribute Name</span></span>                                                     | <span data-ttu-id="13ce2-1110">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-1110">Is Required</span></span> | <span data-ttu-id="13ce2-1111">[値]</span><span class="sxs-lookup"><span data-stu-id="13ce2-1111">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-1112">**型**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1112">**Type**</span></span>                                                           | <span data-ttu-id="13ce2-1113">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1113">No</span></span>          | <span data-ttu-id="13ce2-1114">参照先の型の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1114">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="13ce2-1115">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1115">**Nullable**</span></span>                                                       | <span data-ttu-id="13ce2-1116">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1116">No</span></span>          | <span data-ttu-id="13ce2-1117">プロパティに null 値を指定できるかどうかに応じて、**True** (既定値) または **False**。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1117">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="13ce2-1118">CSDL v1 で > 複合型プロパティにはが必要 `Nullable="False"` です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1118">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="13ce2-1119">**既定**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1119">**DefaultValue**</span></span>                                                   | <span data-ttu-id="13ce2-1120">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1120">No</span></span>          | <span data-ttu-id="13ce2-1121">プロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1121">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="13ce2-1122">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1122">**MaxLength**</span></span>                                                      | <span data-ttu-id="13ce2-1123">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1123">No</span></span>          | <span data-ttu-id="13ce2-1124">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1124">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="13ce2-1125">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1125">**FixedLength**</span></span>                                                    | <span data-ttu-id="13ce2-1126">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1126">No</span></span>          | <span data-ttu-id="13ce2-1127">プロパティ値が固定長文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1127">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="13ce2-1128">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1128">**Precision**</span></span>                                                      | <span data-ttu-id="13ce2-1129">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1129">No</span></span>          | <span data-ttu-id="13ce2-1130">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1130">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="13ce2-1131">**スケール**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1131">**Scale**</span></span>                                                          | <span data-ttu-id="13ce2-1132">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1132">No</span></span>          | <span data-ttu-id="13ce2-1133">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1133">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="13ce2-1134">**SRID**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1134">**SRID**</span></span>                                                           | <span data-ttu-id="13ce2-1135">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1135">No</span></span>          | <span data-ttu-id="13ce2-1136">空間システム参照識別子。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1136">Spatial System Reference Identifier.</span></span> <span data-ttu-id="13ce2-1137">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1137">Valid only for properties of spatial types.</span></span> <span data-ttu-id="13ce2-1138">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1138">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="13ce2-1139">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1139">**Unicode**</span></span>                                                        | <span data-ttu-id="13ce2-1140">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1140">No</span></span>          | <span data-ttu-id="13ce2-1141">プロパティ値が Unicode 文字列として格納されるかどうかによって、 **True**または**False**になります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1141">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="13ce2-1142">**Collation**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1142">**Collation**</span></span>                                                      | <span data-ttu-id="13ce2-1143">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1143">No</span></span>          | <span data-ttu-id="13ce2-1144">データ ソースで使用される照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1144">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="13ce2-1145">任意の数の annotation 属性 (カスタム XML 属性) を **CollectionType** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1145">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="13ce2-1146">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1146">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-1147">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1147">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-1148">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-1148">Example</span></span>

<span data-ttu-id="13ce2-1149">次の例は、 **TypeRef** 要素 ( **CollectionType** 要素の子) を使用して、関数が **Department** エンティティ型のコレクションを受け入れることを指定するモデル定義関数を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1149">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

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
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="13ce2-1150">Using 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1150">Using Element (CSDL)</span></span>

<span data-ttu-id="13ce2-1151">概念スキーマ定義言語 (CSDL) の **Using** 要素は、別の名前空間に存在する概念モデルの内容をインポートします。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1151">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="13ce2-1152">**名前空間**属性の値を設定することにより、別の概念モデルで定義されているエンティティ型、複合型、およびアソシエーション型を参照できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1152">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="13ce2-1153">複数の **Using** 要素を **Schema** 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1153">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="13ce2-1154">CSDL の **using** 要素は、プログラミング言語での **using** ステートメントとまったく同じようには機能しません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1154">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="13ce2-1155">プログラミング言語で **using** ステートメントを使用して名前空間をインポートすると、元の名前空間のオブジェクトには影響しません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1155">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="13ce2-1156">CSDL では、インポートされた名前空間に、元の名前空間にあるエンティティ型から派生したエンティティ型が含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1156">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="13ce2-1157">これは、元の名前空間で宣言されたエンティティ セットに影響を及ぼすことがあります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1157">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="13ce2-1158">**Using**要素には、次の子要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1158">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="13ce2-1159">Documentation (0 または 1 つの要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1159">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="13ce2-1160">Annotation 要素 (0 個以上の要素を含めることができます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1160">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="13ce2-1161">該当する属性</span><span class="sxs-lookup"><span data-stu-id="13ce2-1161">Applicable Attributes</span></span>

<span data-ttu-id="13ce2-1162">次の表では、 **Using** 要素に適用できる属性について説明します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1162">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="13ce2-1163">属性名</span><span class="sxs-lookup"><span data-stu-id="13ce2-1163">Attribute Name</span></span> | <span data-ttu-id="13ce2-1164">必須</span><span class="sxs-lookup"><span data-stu-id="13ce2-1164">Is Required</span></span> | <span data-ttu-id="13ce2-1165">値</span><span class="sxs-lookup"><span data-stu-id="13ce2-1165">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-1166">**Namespace**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1166">**Namespace**</span></span>  | <span data-ttu-id="13ce2-1167">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1167">Yes</span></span>         | <span data-ttu-id="13ce2-1168">インポートされる名前空間の名前。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1168">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="13ce2-1169">**Alias**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1169">**Alias**</span></span>      | <span data-ttu-id="13ce2-1170">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1170">Yes</span></span>         | <span data-ttu-id="13ce2-1171">名前空間の名前の代わりに使用される識別子。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1171">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="13ce2-1172">この属性は必須ですが、オブジェクト名を修飾するために名前空間名の代わりに使用することは必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1172">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="13ce2-1173">任意の数の annotation 属性 (カスタム XML 属性) を **Using** 要素に適用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1173">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="13ce2-1174">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1174">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="13ce2-1175">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1175">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="13ce2-1176">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-1176">Example</span></span>

<span data-ttu-id="13ce2-1177">次の例は、他の場所で定義されている名前空間をインポートするために使用される **Using** 要素を示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1177">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="13ce2-1178">表示される **スキーマ** 要素の名前空間はであることに注意 `BooksModel` してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1178">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="13ce2-1179">EntityType のプロパティは、 `Address` `Publisher` **EntityType**名前空間で定義されている複合型です `ExtendedBooksModel` ( **Using**要素を使用してインポートされます)。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1179">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

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
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="13ce2-1180">annotation 属性 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1180">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="13ce2-1181">概念スキーマ定義言語 (CSDL) の annotation 属性は、概念モデルのカスタム XML 属性です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1181">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="13ce2-1182">有効な XML 構造であることに加え、annotation 属性は次の条件を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1182">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="13ce2-1183">annotation 属性は、CSDL 用に予約された XML 名前空間に存在しない。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1183">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="13ce2-1184">複数の annotation 属性を特定の 1 つの CSDL 要素に適用することができる。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1184">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="13ce2-1185">2 つの任意の annotation 属性の完全修飾名が同じではない。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1185">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="13ce2-1186">annotation 属性は、概念モデルの要素に関する追加のメタデータを提供するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1186">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="13ce2-1187">Annotation 要素に含まれるメタデータには、実行時に、system.string 名前空間のクラスを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1187">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="13ce2-1188">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-1188">Example</span></span>

<span data-ttu-id="13ce2-1189">Annotation 属性 (**CustomAttribute**) を持つ**EntityType**要素の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1189">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="13ce2-1190">また、エンティティ型の要素に適用される annotation 要素も示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1190">The example also shows an annotation element applied to the entity type element.</span></span>

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
 

<span data-ttu-id="13ce2-1191">次のコードは、annotation 属性でメタデータを取得し、コンソールに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1191">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

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
 

<span data-ttu-id="13ce2-1192">前のコードでは、`School.csdl` ファイルがプロジェクトの出力ディレクトリにあり、プロジェクトに次の `Imports` および `Using` ステートメントが追加されていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1192">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="13ce2-1193">Annotation 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1193">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="13ce2-1194">概念スキーマ定義言語 (CSDL) の annotation 要素は、概念モデルのカスタム XML 要素です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1194">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="13ce2-1195">有効な XML 構造が必要であることに加え、annotation 要素は次の条件も満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1195">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="13ce2-1196">annotation 要素は、CSDL 用に予約された XML 名前空間内に存在できません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1196">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="13ce2-1197">複数の annotation 要素を特定の CSDL 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1197">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="13ce2-1198">2 つの annotation 要素の完全修飾名を同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1198">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="13ce2-1199">annotation 要素は、特定の CSDL 要素のその他すべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1199">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="13ce2-1200">annotation 要素は、概念モデルの要素に関する追加のメタデータを提供するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1200">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="13ce2-1201">.NET Framework version 4 以降では、annotation 要素に含まれるメタデータに、実行時に、system.string 名前空間のクラスを使用してアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1201">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="13ce2-1202">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-1202">Example</span></span>

<span data-ttu-id="13ce2-1203">Annotation 要素 (**customelement**) を持つ**EntityType**要素の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1203">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="13ce2-1204">また、エンティティ型の要素に適用される annotation 属性も示しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1204">The example also show an annotation attribute applied to the entity type element.</span></span>

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
 

<span data-ttu-id="13ce2-1205">次のコードは、annotation 要素内のメタデータを取得してコンソールに出力します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1205">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

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
 

<span data-ttu-id="13ce2-1206">前のコードでは、School.csdl ファイルがプロジェクトの出力ディレクトリにあり、プロジェクトに次の `Imports` および `Using` ステートメントが追加されていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1206">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="13ce2-1207">概念モデルの型 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1207">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="13ce2-1208">概念スキーマ定義言語 (CSDL) では、概念モデルのプロパティを定義する **Edmsimpletypes**と呼ばれる一連の抽象プリミティブデータ型をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1208">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="13ce2-1209">**Edmsimpletypes** は、ストレージ環境またはホスト環境でサポートされているプリミティブデータ型のプロキシです。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1209">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="13ce2-1210">下の表は、CSDL でサポートされるプリミティブ データ型の一覧を示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1210">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="13ce2-1211">この表には、各 **Edmsimpletype**に適用できるファセットの一覧も含まれています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1211">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="13ce2-1212">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="13ce2-1212">EDMSimpleType</span></span>                    | <span data-ttu-id="13ce2-1213">説明</span><span class="sxs-lookup"><span data-stu-id="13ce2-1213">Description</span></span>                                                | <span data-ttu-id="13ce2-1214">使用できるファセット</span><span class="sxs-lookup"><span data-stu-id="13ce2-1214">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="13ce2-1215">**Edm.Binary**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1215">**Edm.Binary**</span></span>                   | <span data-ttu-id="13ce2-1216">バイナリ データを格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1216">Contains binary data.</span></span>                                      | <span data-ttu-id="13ce2-1217">MaxLength、FixedLength、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1217">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="13ce2-1218">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1218">**Edm.Boolean**</span></span>                  | <span data-ttu-id="13ce2-1219">**True**または**false**の値が含まれています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1219">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="13ce2-1220">Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1220">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="13ce2-1221">**Edm.Byte**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1221">**Edm.Byte**</span></span>                     | <span data-ttu-id="13ce2-1222">符号なし 8 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1222">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="13ce2-1223">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1223">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="13ce2-1224">**Edm.DateTime**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1224">**Edm.DateTime**</span></span>                 | <span data-ttu-id="13ce2-1225">日時を表します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1225">Represents a date and time.</span></span>                                | <span data-ttu-id="13ce2-1226">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1226">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="13ce2-1227">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1227">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="13ce2-1228">GMT からのオフセット (分単位) としての日時を格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1228">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="13ce2-1229">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1229">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="13ce2-1230">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1230">**Edm.Decimal**</span></span>                  | <span data-ttu-id="13ce2-1231">有効桁数と小数点以下桁数が固定長の数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1231">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="13ce2-1232">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1232">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="13ce2-1233">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1233">**Edm.Double**</span></span>                   | <span data-ttu-id="13ce2-1234">15桁の有効桁数を持つ浮動小数点数を格納します</span><span class="sxs-lookup"><span data-stu-id="13ce2-1234">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="13ce2-1235">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1235">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="13ce2-1236">**Edm. Float**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1236">**Edm.Float**</span></span>                    | <span data-ttu-id="13ce2-1237">7 桁の有効桁数を持つ浮動小数点数を格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1237">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="13ce2-1238">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1238">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="13ce2-1239">**Edm.Guid**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1239">**Edm.Guid**</span></span>                     | <span data-ttu-id="13ce2-1240">16 バイトの一意識別子を格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1240">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="13ce2-1241">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1241">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="13ce2-1242">**Edm.Int16**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1242">**Edm.Int16**</span></span>                    | <span data-ttu-id="13ce2-1243">符号付き 16 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1243">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="13ce2-1244">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1244">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="13ce2-1245">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1245">**Edm.Int32**</span></span>                    | <span data-ttu-id="13ce2-1246">符号付き 32 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1246">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="13ce2-1247">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1247">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="13ce2-1248">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1248">**Edm.Int64**</span></span>                    | <span data-ttu-id="13ce2-1249">符号付き 64 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1249">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="13ce2-1250">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1250">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="13ce2-1251">**Edm.SByte**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1251">**Edm.SByte**</span></span>                    | <span data-ttu-id="13ce2-1252">符号付き 8 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1252">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="13ce2-1253">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1253">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="13ce2-1254">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1254">**Edm.String**</span></span>                   | <span data-ttu-id="13ce2-1255">文字データを格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1255">Contains character data.</span></span>                                   | <span data-ttu-id="13ce2-1256">Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="13ce2-1257">**Edm.Time**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1257">**Edm.Time**</span></span>                     | <span data-ttu-id="13ce2-1258">時刻を格納します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1258">Contains a time of day.</span></span>                                    | <span data-ttu-id="13ce2-1259">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="13ce2-1259">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="13ce2-1260">**Edm. Geography**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1260">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="13ce2-1261">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1261">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1262">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1262">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="13ce2-1263">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1263">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1264">**GeographyLineString**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1264">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="13ce2-1265">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1265">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1266">**返される geographypolygon**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1266">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="13ce2-1267">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1267">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1268">**Edm. GeographyMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1268">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="13ce2-1269">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1269">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1270">**返される geographymultilinestring**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1270">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="13ce2-1271">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1271">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1272">**Edm. GeographyMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1272">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="13ce2-1273">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1273">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1274">**Edm. GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1274">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="13ce2-1275">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1275">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1276">**Edm. Geometry**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1276">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="13ce2-1277">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1277">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1278">**返される geometrypoint**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1278">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="13ce2-1279">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1279">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1280">**返される geometrylinestring**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1280">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="13ce2-1281">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1281">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1282">**返される geometrypolygon**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1282">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="13ce2-1283">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1283">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1284">**返される geometrymultipoint**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1284">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="13ce2-1285">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1285">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1286">**返される geometrymultilinestring**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1286">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="13ce2-1287">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1287">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1288">**返される geometrymultipolygon**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1288">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="13ce2-1289">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1289">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="13ce2-1290">**GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1290">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="13ce2-1291">Nullable、Default、SRID</span><span class="sxs-lookup"><span data-stu-id="13ce2-1291">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="13ce2-1292">ファセット (CSDL)</span><span class="sxs-lookup"><span data-stu-id="13ce2-1292">Facets (CSDL)</span></span>

<span data-ttu-id="13ce2-1293">概念スキーマ定義言語 (CSDL) のファセットは、エンティティ型と複合型のプロパティに対する制約を表します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1293">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="13ce2-1294">ファセットは次の CSDL 要素で XML 属性として使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1294">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="13ce2-1295">プロパティ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1295">Property</span></span>
-   <span data-ttu-id="13ce2-1296">TypeRef</span><span class="sxs-lookup"><span data-stu-id="13ce2-1296">TypeRef</span></span>
-   <span data-ttu-id="13ce2-1297">パラメーター</span><span class="sxs-lookup"><span data-stu-id="13ce2-1297">Parameter</span></span>

<span data-ttu-id="13ce2-1298">次の表は、CSDL でサポートされるファセットについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1298">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="13ce2-1299">いずれのファセットもオプションです。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1299">All facets are optional.</span></span> <span data-ttu-id="13ce2-1300">次に示すいくつかのファセットは、概念モデルからデータベースを生成するときに Entity Framework によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1300">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="13ce2-1301">概念モデルのデータ型の詳細については、「概念モデルの型 (CSDL)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1301">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="13ce2-1302">ファセット</span><span class="sxs-lookup"><span data-stu-id="13ce2-1302">Facet</span></span>               | <span data-ttu-id="13ce2-1303">説明</span><span class="sxs-lookup"><span data-stu-id="13ce2-1303">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="13ce2-1304">対象</span><span class="sxs-lookup"><span data-stu-id="13ce2-1304">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="13ce2-1305">データベースの生成に使用</span><span class="sxs-lookup"><span data-stu-id="13ce2-1305">Used for the database generation</span></span> | <span data-ttu-id="13ce2-1306">ランタイムで使用</span><span class="sxs-lookup"><span data-stu-id="13ce2-1306">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="13ce2-1307">**Collation**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1307">**Collation**</span></span>       | <span data-ttu-id="13ce2-1308">プロパティの値に対して比較と順序付け操作を行うときに使用する照合シーケンス (または並べ替え順序) を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1308">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="13ce2-1309">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1309">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="13ce2-1310">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1310">Yes</span></span>                              | <span data-ttu-id="13ce2-1311">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1311">No</span></span>                  |
| <span data-ttu-id="13ce2-1312">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1312">**ConcurrencyMode**</span></span> | <span data-ttu-id="13ce2-1313">プロパティの値をオプティミスティック コンカレンシー チェックに使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1313">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="13ce2-1314">すべての **Edmsimpletype** プロパティ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1314">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="13ce2-1315">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1315">No</span></span>                               | <span data-ttu-id="13ce2-1316">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1316">Yes</span></span>                 |
| <span data-ttu-id="13ce2-1317">**Shared**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1317">**Default**</span></span>         | <span data-ttu-id="13ce2-1318">インスタンス化で値が指定されない場合のプロパティの既定値を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1318">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="13ce2-1319">すべての **Edmsimpletype** プロパティ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1319">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="13ce2-1320">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1320">Yes</span></span>                              | <span data-ttu-id="13ce2-1321">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1321">Yes</span></span>                 |
| <span data-ttu-id="13ce2-1322">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1322">**FixedLength**</span></span>     | <span data-ttu-id="13ce2-1323">プロパティ値の長さを可変とすることができるかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1323">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="13ce2-1324">**Edm. Binary**, **edm. String**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1324">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="13ce2-1325">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1325">Yes</span></span>                              | <span data-ttu-id="13ce2-1326">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1326">No</span></span>                  |
| <span data-ttu-id="13ce2-1327">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1327">**MaxLength**</span></span>       | <span data-ttu-id="13ce2-1328">プロパティ値の最大長を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1328">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="13ce2-1329">**Edm. Binary**, **edm. String**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1329">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="13ce2-1330">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1330">Yes</span></span>                              | <span data-ttu-id="13ce2-1331">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1331">No</span></span>                  |
| <span data-ttu-id="13ce2-1332">**NULL 値の使用**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1332">**Nullable**</span></span>        | <span data-ttu-id="13ce2-1333">プロパティが **null** 値を持つことができるかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1333">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="13ce2-1334">すべての **Edmsimpletype** プロパティ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1334">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="13ce2-1335">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1335">Yes</span></span>                              | <span data-ttu-id="13ce2-1336">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1336">Yes</span></span>                 |
| <span data-ttu-id="13ce2-1337">**[精度]**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1337">**Precision**</span></span>       | <span data-ttu-id="13ce2-1338">**Decimal**型のプロパティの場合、プロパティ値が持つことができる桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1338">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="13ce2-1339">**Time**、 **DateTime**、および**DateTimeOffset**型のプロパティの場合、プロパティ値の秒の小数部の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1339">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="13ce2-1340">**Edm. DateTime**, **edm. DateTimeOffset**, edm. **Decimal**, **edm. Time**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1340">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="13ce2-1341">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1341">Yes</span></span>                              | <span data-ttu-id="13ce2-1342">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1342">No</span></span>                  |
| <span data-ttu-id="13ce2-1343">**スケール**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1343">**Scale**</span></span>           | <span data-ttu-id="13ce2-1344">プロパティ値の小数点の右側の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1344">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="13ce2-1345">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1345">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="13ce2-1346">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1346">Yes</span></span>                              | <span data-ttu-id="13ce2-1347">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1347">No</span></span>                  |
| <span data-ttu-id="13ce2-1348">**SRID**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1348">**SRID**</span></span>            | <span data-ttu-id="13ce2-1349">空間システム参照システム ID を指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1349">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="13ce2-1350">詳細については、「 [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1350">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="13ce2-1351">**GeographyLineString、edm. GeographyPoint、返される geographypolygon、返される geographymultilinestring、Edm. GeographyMultiPolygon、Edm. Geographypoint、Edm. Geometry、返される geometrypoint、、返される geometrylinestring、、、返される geometrypolygon、返される geometrymultipoint、返される geometrymultilinestring、返される geometrymultipolygon、GeometryCollection、、、、、、、、**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1351">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="13ce2-1352">いいえ</span><span class="sxs-lookup"><span data-stu-id="13ce2-1352">No</span></span>                               | <span data-ttu-id="13ce2-1353">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1353">Yes</span></span>                 |
| <span data-ttu-id="13ce2-1354">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1354">**Unicode**</span></span>         | <span data-ttu-id="13ce2-1355">プロパティ値を Unicode として保存するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1355">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="13ce2-1356">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="13ce2-1356">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="13ce2-1357">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1357">Yes</span></span>                              | <span data-ttu-id="13ce2-1358">はい</span><span class="sxs-lookup"><span data-stu-id="13ce2-1358">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="13ce2-1359">概念モデルからデータベースを生成する場合、データベース生成ウィザードでは、**プロパティ**要素が次の名前空間にある場合**は、その属性の**値が認識され https://schemas.microsoft.com/ado/2009/02/edm/annotation ます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1359">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: https://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="13ce2-1360">属性でサポートされている値は、 **id** と **計算**です。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1360">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="13ce2-1361">**Id**値を指定すると、データベースで生成された id 値を持つデータベース列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1361">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="13ce2-1362">**計算**値を指定すると、データベースで計算された値を持つ列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1362">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="13ce2-1363">例</span><span class="sxs-lookup"><span data-stu-id="13ce2-1363">Example</span></span>

<span data-ttu-id="13ce2-1364">エンティティ型のプロパティに適用されるファセットの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="13ce2-1364">The following example shows facets applied to the properties of an entity type:</span></span>

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
