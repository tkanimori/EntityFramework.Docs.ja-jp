---
title: CSDL の仕様の EF6
author: divega
ms.date: 2016-10-23
ms.assetid: c54255f4-253f-49eb-bec8-ad7927ac2fa3
ms.openlocfilehash: 88669cf80f9a792fda7d191d9f6be2b1734691df
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994730"
---
# <a name="csdl-specification"></a><span data-ttu-id="23dd4-102">CSDL 仕様</span><span class="sxs-lookup"><span data-stu-id="23dd4-102">CSDL Specification</span></span>
<span data-ttu-id="23dd4-103">概念スキーマ定義言語 (CSDL : Conceptual Schema Definition Language) は、XML ベースの言語であり、データ駆動型アプリケーションの概念モデルを構成するエンティティ、リレーションシップ、および関数を記述します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-103">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="23dd4-104">Entity Framework や WCF Data Services では、この概念モデルを使用できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-104">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="23dd4-105">CSDL で記述されるメタデータは、エンティティおよびデータ ソースに概念モデルで定義されているリレーションシップにマップする Entity Framework によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-105">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="23dd4-106">詳細については、次を参照してください。 [SSDL 仕様](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)と[MSL 仕様](~/ef6/modeling/designer/advanced/edmx/msl-spec.md)します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-106">For more information, see [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md) and [MSL Specification](~/ef6/modeling/designer/advanced/edmx/msl-spec.md).</span></span>

<span data-ttu-id="23dd4-107">CSDL は、Entity Data Model の Entity Framework の実装です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-107">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="23dd4-108">Entity Framework アプリケーションで概念モデルのメタデータが読み込まれた .csdl ファイル (CSDL で記述) から、System.Data.Metadata.Edm.EdmItemCollection のインスタンスに、アクセスできるメソッドを使用して、System.Data.Metadata.Edm.MetadataWorkspace クラスです。</span><span class="sxs-lookup"><span data-stu-id="23dd4-108">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="23dd4-109">Entity Framework では、概念モデルのメタデータを使用してデータ ソース固有のコマンドを概念モデルに対するクエリに変換します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-109">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="23dd4-110">EF Designer では、デザイン時に .edmx ファイルの概念モデル情報を格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-110">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="23dd4-111">ビルド時に、EF デザイナー情報を使用して .edmx ファイルの実行時に Entity Framework によって必要な .csdl ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-111">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="23dd4-112">CSDL のバージョンは、XML 名前空間で区別されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-112">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="23dd4-113">CSDL のバージョン</span><span class="sxs-lookup"><span data-stu-id="23dd4-113">CSDL Version</span></span> | <span data-ttu-id="23dd4-114">XML Namespace</span><span class="sxs-lookup"><span data-stu-id="23dd4-114">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="23dd4-115">CSDL v1</span><span class="sxs-lookup"><span data-stu-id="23dd4-115">CSDL v1</span></span>      | http://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="23dd4-116">CSDL v2</span><span class="sxs-lookup"><span data-stu-id="23dd4-116">CSDL v2</span></span>      | http://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="23dd4-117">CSDL v3</span><span class="sxs-lookup"><span data-stu-id="23dd4-117">CSDL v3</span></span>      | http://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="23dd4-118">Association 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-118">Association Element (CSDL)</span></span>

<span data-ttu-id="23dd4-119">**アソシエーション**要素が 2 つのエンティティ型間のリレーションシップを定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-119">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="23dd4-120">アソシエーションでは、リレーションシップに関連するエンティティ型、およびリレーションシップの各 End におけるエンティティ型の数 (多重度と呼ばれる) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-120">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="23dd4-121">値の 1 つ (1)、0 個または 1 (0..1)、または複数のアソシエーション end の多重度を持つことができます (\*)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-121">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="23dd4-122">この情報は、2 つの子 End 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-122">This information is specified in two child End elements.</span></span>

<span data-ttu-id="23dd4-123">アソシエーションの一方の End にあるエンティティ型のインスタンスには、それらがエンティティ型で公開されている場合、ナビゲーション プロパティまたは外部キーからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-123">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="23dd4-124">アプリケーション内で、アソシエーションのインスタンスは、エンティティ型のインスタンスの間の特定のアソシエーションを表します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-124">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="23dd4-125">アソシエーション インスタンスは、アソシエーション セットに論理的にグループ化されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-125">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="23dd4-126">**アソシエーション**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-126">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-127">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-127">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-128">終了 (正確に 2 つの要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-128">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="23dd4-129">ReferentialConstraint (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-129">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-130">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-130">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-131">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-131">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-132">次の表に適用できる属性の説明、**アソシエーション**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-132">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="23dd4-133">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-133">Attribute Name</span></span> | <span data-ttu-id="23dd4-134">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-134">Is Required</span></span> | <span data-ttu-id="23dd4-135">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-135">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="23dd4-136">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-136">**Name**</span></span>       | <span data-ttu-id="23dd4-137">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-137">Yes</span></span>         | <span data-ttu-id="23dd4-138">アソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-138">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-139">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**アソシエーション**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-139">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="23dd4-140">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-140">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-141">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-141">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-142">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-142">Example</span></span>

<span data-ttu-id="23dd4-143">次の例は、**アソシエーション**を定義する要素、 **CustomerOrders**関連付けの外部キーが公開されていないときに、**顧客**と**順序**エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-143">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="23dd4-144">**多重度**値はそれぞれ**エンド**の関連付けを示す多く**注文**と関連付けることができます、**顧客**が、1 つだけ**顧客**関連付けることができる、**順序**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-144">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="23dd4-145">さらに、 **OnDelete**要素には、ことを示しますすべて**注文**に関連する特定の**顧客**に読み込まれ、ObjectContext は削除されます場合、**顧客**は削除されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-145">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="23dd4-146">次の例は、**アソシエーション**を定義する要素、 **CustomerOrders**アソシエーションで外部キーが公開されているときに、**顧客**と**順序**エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-146">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="23dd4-147">公開されている外部キーを持つ、エンティティ間のリレーションシップが管理されている、 **ReferentialConstraint**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-147">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="23dd4-148">対応する AssociationSetMapping 要素は、このアソシエーションをデータ ソースにマップする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-148">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

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
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="23dd4-149">AssociationSet 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-149">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="23dd4-150">**AssociationSet**概念スキーマ定義言語 (CSDL) で要素は、同じ型のアソシエーション インスタンス用の論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="23dd4-150">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="23dd4-151">アソシエーション セットは、複数のアソシエーション インスタンスを 1 つのデータ ソースにマップできるようグループ化する方法を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-151">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="23dd4-152">**AssociationSet**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-152">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-153">ドキュメント (0 個または 1 つの要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-153">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="23dd4-154">終了 (2 つの要素が必要な場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-154">End (exactly two elements required)</span></span>
-   <span data-ttu-id="23dd4-155">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-155">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="23dd4-156">**アソシエーション**属性は、アソシエーション セットに含まれる関連付けの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-156">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="23dd4-157">アソシエーション セットの両端を構成するエンティティ セットが正確に 2 つの子で指定された**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-157">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-158">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-158">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-159">次の表に適用できる属性の説明、 **AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-159">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="23dd4-160">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-160">Attribute Name</span></span>  | <span data-ttu-id="23dd4-161">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-161">Is Required</span></span> | <span data-ttu-id="23dd4-162">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-162">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-163">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-163">**Name**</span></span>        | <span data-ttu-id="23dd4-164">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-164">Yes</span></span>         | <span data-ttu-id="23dd4-165">エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-165">The name of the entity set.</span></span> <span data-ttu-id="23dd4-166">値、**名前**属性の値と同じにすることはできません、**アソシエーション**属性。</span><span class="sxs-lookup"><span data-stu-id="23dd4-166">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="23dd4-167">**関連付け**</span><span class="sxs-lookup"><span data-stu-id="23dd4-167">**Association**</span></span> | <span data-ttu-id="23dd4-168">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-168">Yes</span></span>         | <span data-ttu-id="23dd4-169">関連付けの完全修飾名。そのインスタンスは、アソシエーション セットに格納されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-169">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="23dd4-170">関連付けは、アソシエーション セットと同じ名前空間に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-170">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-171">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-171">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="23dd4-172">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-172">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-173">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-173">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-174">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-174">Example</span></span>

<span data-ttu-id="23dd4-175">次の例は、 **EntityContainer**要素を持つ**AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-175">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

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
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="23dd4-176">CollectionType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-176">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="23dd4-177">**CollectionType**概念スキーマ定義言語 (CSDL) 内の要素のことを関数パラメーターまたは関数の戻り型がコレクションを指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-177">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="23dd4-178">**CollectionType**要素 (関数) の ReturnType 要素またはパラメーターの要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-178">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="23dd4-179">いずれかを使用してコレクションの型を指定することができます、**型**属性または子要素を次のいずれか。</span><span class="sxs-lookup"><span data-stu-id="23dd4-179">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="23dd4-180">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="23dd4-180">**CollectionType**</span></span>
-   <span data-ttu-id="23dd4-181">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="23dd4-181">ReferenceType</span></span>
-   <span data-ttu-id="23dd4-182">RowType</span><span class="sxs-lookup"><span data-stu-id="23dd4-182">RowType</span></span>
-   <span data-ttu-id="23dd4-183">TypeRef</span><span class="sxs-lookup"><span data-stu-id="23dd4-183">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="23dd4-184">両方のコレクションの型が指定されている場合、モデルは検証されません、**型**属性と子要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-184">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-185">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-185">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-186">次の表に適用できる属性、 **CollectionType**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-186">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="23dd4-187">なお、 **DefaultValue**、 **MaxLength**、 **FixedLength**、**精度**、**スケール**、 **Unicode**、および**照合順序**のコレクションに適用可能な属性は**EDMSimpleTypes**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-187">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="23dd4-188">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-188">Attribute Name</span></span>                                                          | <span data-ttu-id="23dd4-189">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-189">Is Required</span></span> | <span data-ttu-id="23dd4-190">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-190">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-191">**Type**</span><span class="sxs-lookup"><span data-stu-id="23dd4-191">**Type**</span></span>                                                                | <span data-ttu-id="23dd4-192">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-192">No</span></span>          | <span data-ttu-id="23dd4-193">コレクションの型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-193">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="23dd4-194">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="23dd4-194">**Nullable**</span></span>                                                            | <span data-ttu-id="23dd4-195">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-195">No</span></span>          | <span data-ttu-id="23dd4-196">**True** (既定値) または**False**プロパティが null 値を持っているかどうかによって。</span><span class="sxs-lookup"><span data-stu-id="23dd4-196">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="23dd4-197">> 複合型プロパティがありますで CSDL v1`Nullable="False"`します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-197">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="23dd4-198">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="23dd4-198">**DefaultValue**</span></span>                                                        | <span data-ttu-id="23dd4-199">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-199">No</span></span>          | <span data-ttu-id="23dd4-200">プロパティの既定値。</span><span class="sxs-lookup"><span data-stu-id="23dd4-200">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="23dd4-201">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-201">**MaxLength**</span></span>                                                           | <span data-ttu-id="23dd4-202">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-202">No</span></span>          | <span data-ttu-id="23dd4-203">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="23dd4-203">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="23dd4-204">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-204">**FixedLength**</span></span>                                                         | <span data-ttu-id="23dd4-205">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-205">No</span></span>          | <span data-ttu-id="23dd4-206">**True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-206">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="23dd4-207">**精度**</span><span class="sxs-lookup"><span data-stu-id="23dd4-207">**Precision**</span></span>                                                           | <span data-ttu-id="23dd4-208">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-208">No</span></span>          | <span data-ttu-id="23dd4-209">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="23dd4-209">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="23dd4-210">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="23dd4-210">**Scale**</span></span>                                                               | <span data-ttu-id="23dd4-211">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-211">No</span></span>          | <span data-ttu-id="23dd4-212">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="23dd4-212">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="23dd4-213">**SRID**</span><span class="sxs-lookup"><span data-stu-id="23dd4-213">**SRID**</span></span>                                                                | <span data-ttu-id="23dd4-214">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-214">No</span></span>          | <span data-ttu-id="23dd4-215">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-215">Spatial System Reference Identifier.</span></span> <span data-ttu-id="23dd4-216">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-216">Valid only for properties of spatial types.</span></span>   <span data-ttu-id="23dd4-217">詳細については、次を参照してください [SRID](http://en.wikipedia.org/wiki/SRID) と[SRID (SQL Server) 。](https://msdn.microsoft.com/library/bb964707.aspx)</span><span class="sxs-lookup"><span data-stu-id="23dd4-217">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="23dd4-218">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="23dd4-218">**Unicode**</span></span>                                                             | <span data-ttu-id="23dd4-219">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-219">No</span></span>          | <span data-ttu-id="23dd4-220">**True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-220">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="23dd4-221">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="23dd4-221">**Collation**</span></span>                                                           | <span data-ttu-id="23dd4-222">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-222">No</span></span>          | <span data-ttu-id="23dd4-223">データ ソースで使用する照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="23dd4-223">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="23dd4-224">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **CollectionType**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-224">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="23dd4-225">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-225">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-226">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-226">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-227">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-227">Example</span></span>

<span data-ttu-id="23dd4-228">次の例では、モデル定義の関数を使用して、 **CollectionType**関数でのコレクションを返すことを指定する要素**Person**エンティティ型 (、に指定された**ElementType**属性)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-228">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

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
 

<span data-ttu-id="23dd4-229">次の例では、使用するモデル定義関数、 **CollectionType**関数によって返される行のコレクションを指定する要素 (で指定されている、 **RowType**要素)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-229">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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
 

<span data-ttu-id="23dd4-230">次の例では、使用するモデル定義関数、 **CollectionType**要素のコレクションのパラメーターとして、関数が受け入れる指定を**部門**エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-230">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

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
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="23dd4-231">ComplexType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-231">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="23dd4-232">A **ComplexType**要素から成るデータ構造を定義する**EdmSimpleType**プロパティまたはその他の複合型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-232">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span>  <span data-ttu-id="23dd4-233">複合型は、エンティティ型または別の複合型のプロパティにすることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-233">A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="23dd4-234">複合型は、データを定義するという点でエンティティ型に似ています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-234">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="23dd4-235">ただし、複合型とエンティティ型の間にはいくつかの重要な違いがあります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-235">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="23dd4-236">複合型には ID (またはキー) がないため、独立して存在することができません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-236">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="23dd4-237">複合型は、エンティティ型またはその他の複合型のプロパティとしてのみ存在できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-237">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="23dd4-238">複合型は、アソシエーションに参加できません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-238">Complex types cannot participate in associations.</span></span> <span data-ttu-id="23dd4-239">どちらも、アソシエーションの end が複合型を指定でき、したがって複合型のナビゲーション プロパティを定義することはできません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-239">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="23dd4-240">複合型のスカラー プロパティはそれぞれ null に設定できますが、複合型のプロパティには null 値を指定できません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-240">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="23dd4-241">A **ComplexType**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-241">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-242">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-242">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-243">プロパティ (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-243">Property (zero or more elements)</span></span>
-   <span data-ttu-id="23dd4-244">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-244">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="23dd4-245">次の表に適用できる属性の説明、 **ComplexType**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-245">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="23dd4-246">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-246">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="23dd4-247">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-247">Is Required</span></span> | <span data-ttu-id="23dd4-248">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-248">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-249">name</span><span class="sxs-lookup"><span data-stu-id="23dd4-249">Name</span></span>                                                                                                           | <span data-ttu-id="23dd4-250">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-250">Yes</span></span>         | <span data-ttu-id="23dd4-251">複合型の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-251">The name of the complex type.</span></span> <span data-ttu-id="23dd4-252">複合型の名前は、モデルのスコープ内にある別の複合型、エンティティ型、またはアソシエーションの名前と同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-252">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="23dd4-253">BaseType</span><span class="sxs-lookup"><span data-stu-id="23dd4-253">BaseType</span></span>                                                                                                       | <span data-ttu-id="23dd4-254">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-254">No</span></span>          | <span data-ttu-id="23dd4-255">定義される複合型の基本データ型である、別の複合型の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-255">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="23dd4-256">> この属性では、CSDL v1 で適用されません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-256">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="23dd4-257">複合型の継承は、このバージョンではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-257">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="23dd4-258">抽象</span><span class="sxs-lookup"><span data-stu-id="23dd4-258">Abstract</span></span>                                                                                                       | <span data-ttu-id="23dd4-259">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-259">No</span></span>          | <span data-ttu-id="23dd4-260">**True**または**False** (既定値)、複合型が抽象型がかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-260">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="23dd4-261">> この属性では、CSDL v1 で適用されません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-261">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="23dd4-262">このバージョンの複合型を抽象型にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-262">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="23dd4-263">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ComplexType**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-263">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="23dd4-264">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-264">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-265">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-265">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-266">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-266">Example</span></span>

<span data-ttu-id="23dd4-267">次の例では、複合型、**アドレス**で、 **EdmSimpleType**プロパティ**StreetAddress**、**市区町村**、 **StateOrProvince**、**国**、および**PostalCode**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-267">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="23dd4-268">複合型を定義する**アドレス**(上)、エンティティ型のプロパティとしてする必要がありますを宣言するエンティティ型の定義のプロパティの型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-268">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="23dd4-269">次の例は、**アドレス**プロパティとして複合型のエンティティ型である (**パブリッシャー**)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-269">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

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
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="23dd4-270">DefiningExpression 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-270">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="23dd4-271">**DefiningExpression**概念スキーマ定義言語 (CSDL) 内の要素には、概念モデルで関数を定義する Entity SQL 式が含まれています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-271">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="23dd4-272">検証のため、 **DefiningExpression**要素は、任意のコンテンツを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-272">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="23dd4-273">ただし、Entity Framework は例外をスロー時、 **DefiningExpression**要素に有効な Entity SQL が含まれていません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-273">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-274">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-274">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-275">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **DefiningExpression**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-275">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="23dd4-276">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-276">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-277">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-277">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="23dd4-278">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-278">Example</span></span>

<span data-ttu-id="23dd4-279">次の例では、 **DefiningExpression**書籍が発行されて以来、年数を返す関数を定義する要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-279">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="23dd4-280">コンテンツ、 **DefiningExpression** Entity SQL で要素が書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-280">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="23dd4-281">Dependent 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-281">Dependent Element (CSDL)</span></span>

<span data-ttu-id="23dd4-282">**依存**概念スキーマ定義言語 (CSDL) 内の要素は ReferentialConstraint 要素に子要素であり参照に関する制約の依存 end を定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-282">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="23dd4-283">A **ReferentialConstraint**要素は、リレーショナル データベースで参照整合性制約と同様の機能を定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-283">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="23dd4-284">データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティが別のエンティティ型のエンティティ キーを参照できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-284">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="23dd4-285">参照されるエンティティ型が呼び出される、*プリンシパル end*の制約。</span><span class="sxs-lookup"><span data-stu-id="23dd4-285">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="23dd4-286">プリンシパル end を参照するエンティティ型が呼び出される、*依存 end*の制約。</span><span class="sxs-lookup"><span data-stu-id="23dd4-286">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="23dd4-287">**PropertyRef**要素を使用すると、プリンシパル end を参照するキーを指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-287">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="23dd4-288">**依存**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-288">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-289">PropertyRef (1 つまたは複数の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-289">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="23dd4-290">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-290">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-291">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-291">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-292">次の表に適用できる属性の説明、**依存**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-292">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="23dd4-293">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-293">Attribute Name</span></span> | <span data-ttu-id="23dd4-294">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-294">Is Required</span></span> | <span data-ttu-id="23dd4-295">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-295">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="23dd4-296">**ロール**</span><span class="sxs-lookup"><span data-stu-id="23dd4-296">**Role**</span></span>       | <span data-ttu-id="23dd4-297">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-297">Yes</span></span>         | <span data-ttu-id="23dd4-298">アソシエーションの依存 End 上のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-298">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-299">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**依存**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-299">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="23dd4-300">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-300">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-301">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-301">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-302">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-302">Example</span></span>

<span data-ttu-id="23dd4-303">次の例は、 **ReferentialConstraint**要素の定義の一部として使用されている、 **PublishedBy**アソシエーション。</span><span class="sxs-lookup"><span data-stu-id="23dd4-303">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="23dd4-304">**PublisherId**のプロパティ、**帳**参照に関する制約の依存 end のエンティティ型を使用します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-304">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

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
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="23dd4-305">Documentation 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-305">Documentation Element (CSDL)</span></span>

<span data-ttu-id="23dd4-306">**ドキュメント**親要素で定義されているオブジェクトに関する情報を提供する概念スキーマ定義言語 (CSDL) 内の要素を使用できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-306">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="23dd4-307">.Edmx ファイルの場合、**ドキュメント**要素の内容 (エンティティ、アソシエーション、プロパティなど)、EF デザイナーのデザイン画面上のオブジェクトとして表示される要素の子である、**ドキュメント**要素は、Visual Studio に表示されます**プロパティ**ウィンドウで、オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="23dd4-307">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="23dd4-308">**ドキュメント**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-308">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-309">**概要**: 親要素の簡単な説明。</span><span class="sxs-lookup"><span data-stu-id="23dd4-309">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="23dd4-310">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-310">(zero or one element)</span></span>
-   <span data-ttu-id="23dd4-311">**LongDescription**: 親要素の説明を提供します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-311">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="23dd4-312">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-312">(zero or one element)</span></span>
-   <span data-ttu-id="23dd4-313">Annotation 要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-313">Annotation elements.</span></span> <span data-ttu-id="23dd4-314">(0 個以上の要素)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-314">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-315">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-315">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-316">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**ドキュメント**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-316">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="23dd4-317">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-317">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-318">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-318">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="23dd4-319">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-319">Example</span></span>

<span data-ttu-id="23dd4-320">次の例は、**ドキュメント**EntityType 要素の子要素としての要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-320">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="23dd4-321">次のスニペットにあった場合の CSDL は、コンテンツの .edmx ファイルの内容、**概要**と**LongDescription**要素が、Visual Studio に含まれる**プロパティ**ウィンドウをクリックすると、`Customer`エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-321">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

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
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="23dd4-322">End 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-322">End Element (CSDL)</span></span>

<span data-ttu-id="23dd4-323">**エンド**概念スキーマ定義言語 (CSDL) の要素は Association 要素または AssociationSet 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-323">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="23dd4-324">各ケースでは、役割で、**エンド**要素が異なると、該当する属性が異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-324">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="23dd4-325">Association 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="23dd4-325">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="23dd4-326">**エンド**要素 (の子として、**アソシエーション**要素) のアソシエーションの一方の端とのアソシエーションの end に存在できるエンティティ型のインスタンスの数のエンティティ型を識別します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-326">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="23dd4-327">アソシエーション End はアソシエーションの一部として定義され、アソシエーションには必ず 2 つのアソシエーション End が必要です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-327">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="23dd4-328">アソシエーションの 1 つの End にあるエンティティ型インスタンスには、ナビゲーション プロパティまたは外部キーからアクセスできます (エンティティ型で公開されている場合)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-328">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="23dd4-329">**エンド**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-329">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-330">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-330">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-331">OnDelete (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-331">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-332">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-332">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-333">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-333">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-334">次の表に適用できる属性、**エンド**要素の子である場合に、**アソシエーション**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-334">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="23dd4-335">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-335">Attribute Name</span></span>   | <span data-ttu-id="23dd4-336">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-336">Is Required</span></span> | <span data-ttu-id="23dd4-337">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-337">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-338">**Type**</span><span class="sxs-lookup"><span data-stu-id="23dd4-338">**Type**</span></span>         | <span data-ttu-id="23dd4-339">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-339">Yes</span></span>         | <span data-ttu-id="23dd4-340">アソシエーションの一方の End のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-340">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="23dd4-341">**ロール**</span><span class="sxs-lookup"><span data-stu-id="23dd4-341">**Role**</span></span>         | <span data-ttu-id="23dd4-342">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-342">No</span></span>          | <span data-ttu-id="23dd4-343">アソシエーション End の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-343">A name for the association end.</span></span> <span data-ttu-id="23dd4-344">名前が指定されない場合、アソシエーション End のエンティティ型の名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-344">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="23dd4-345">**多重度**</span><span class="sxs-lookup"><span data-stu-id="23dd4-345">**Multiplicity**</span></span> | <span data-ttu-id="23dd4-346">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-346">Yes</span></span>         | <span data-ttu-id="23dd4-347">**1**、 **0..1**、または**\*** アソシエーションの end に存在できるエンティティ型のインスタンスの数によって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-347">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="23dd4-348">**1**その 1 つのエンティティ型のインスタンスは、アソシエーション end に存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-348">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="23dd4-349">**0..1**アソシエーション end に 0 個または 1 つのエンティティ型のインスタンスが存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-349">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="23dd4-350">**\*** アソシエーション end に 0、1、または複数のエンティティ型のインスタンスが存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-350">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-351">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-351">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="23dd4-352">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-352">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-353">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-353">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="23dd4-354">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-354">Example</span></span>

<span data-ttu-id="23dd4-355">次の例は、**アソシエーション**を定義する要素、 **CustomerOrders**アソシエーション。</span><span class="sxs-lookup"><span data-stu-id="23dd4-355">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="23dd4-356">**多重度**値はそれぞれ**エンド**の関連付けを示す多く**注文**と関連付けることができます、**顧客**が、1 つだけ**顧客**関連付けることができる、**順序**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-356">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="23dd4-357">さらに、 **OnDelete**要素には、ことを示しますすべて**注文**に関連する特定の**顧客**に読み込まれたとは ObjectContext をします。削除された場合、**顧客**は削除されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-357">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="23dd4-358">AssociationSet 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="23dd4-358">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="23dd4-359">**エンド**要素は、アソシエーション セットの一方の end を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-359">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="23dd4-360">**AssociationSet**要素には、2 つ含める必要があります**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-360">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="23dd4-361">含まれる情報を**エンド**要素は、アソシエーション セットのデータ ソースへのマッピングで使用します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-361">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="23dd4-362">**エンド**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-362">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-363">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-363">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-364">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-364">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="23dd4-365">Annotation 要素は、それ以外のすべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-365">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="23dd4-366">Annotation 要素は、CSDL v2 以降にのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-366">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-367">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-367">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-368">次の表に適用できる属性、**エンド**要素の子である場合に、 **AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-368">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="23dd4-369">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-369">Attribute Name</span></span> | <span data-ttu-id="23dd4-370">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-370">Is Required</span></span> | <span data-ttu-id="23dd4-371">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-371">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-372">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="23dd4-372">**EntitySet**</span></span>  | <span data-ttu-id="23dd4-373">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-373">Yes</span></span>         | <span data-ttu-id="23dd4-374">名前、 **EntitySet** 、親の一方の end を定義する要素**AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-374">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="23dd4-375">**EntitySet**で親と同じエンティティ コンテナー要素を定義する必要があります**AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-375">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="23dd4-376">**ロール**</span><span class="sxs-lookup"><span data-stu-id="23dd4-376">**Role**</span></span>       | <span data-ttu-id="23dd4-377">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-377">No</span></span>          | <span data-ttu-id="23dd4-378">アソシエーション セット End の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-378">The name of the association set end.</span></span> <span data-ttu-id="23dd4-379">場合、**ロール**属性が使用されない場合、アソシエーション セット end の名前はエンティティ セットの名前になります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-379">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="23dd4-380">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-380">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="23dd4-381">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-381">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-382">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-382">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="23dd4-383">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-383">Example</span></span>

<span data-ttu-id="23dd4-384">次の例は、 **EntityContainer**要素を持つ**AssociationSet**それぞれに 2 つの要素、**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-384">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

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
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="23dd4-385">EntityContainer 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-385">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="23dd4-386">**EntityContainer**概念スキーマ定義言語 (CSDL) での要素はエンティティ セット、アソシエーション セット、および関数インポートの論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="23dd4-386">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="23dd4-387">EntityContainerMapping 要素を通じてストレージ モデルのエンティティ コンテナーに、概念モデルのエンティティ コンテナーがマップされます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-387">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="23dd4-388">ストレージ モデルのエンティティ コンテナーは、データベースの構造を記述します。エンティティ セットはデータベースのテーブル、アソシエーション セットは外部キー、関数インポートはストアド プロシージャをそれぞれ記述します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-388">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="23dd4-389">**EntityContainer**要素は、0 個または 1 つのドキュメント要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-389">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="23dd4-390">場合、**ドキュメント**要素が存在する、すべての前にする必要があります、 **EntitySet**、 **AssociationSet**、および**FunctionImport**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-390">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="23dd4-391">**EntityContainer**要素 (表示順) で、次の子要素の 0 個以上を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-391">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-392">EntitySet</span><span class="sxs-lookup"><span data-stu-id="23dd4-392">EntitySet</span></span>
-   <span data-ttu-id="23dd4-393">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="23dd4-393">AssociationSet</span></span>
-   <span data-ttu-id="23dd4-394">FunctionImport</span><span class="sxs-lookup"><span data-stu-id="23dd4-394">FunctionImport</span></span>
-   <span data-ttu-id="23dd4-395">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="23dd4-395">Annotation elements</span></span>

<span data-ttu-id="23dd4-396">拡張することができます、 **EntityContainer**要素別の内容を含める**EntityContainer**同じ名前空間内にあります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-396">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="23dd4-397">別のコンテンツを含める**EntityContainer**を参照することで**EntityContainer**要素の値の設定、**拡張**属性、の名前を**EntityContainer**に含める要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-397">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="23dd4-398">含まれるすべての子要素**EntityContainer**要素を参照する子要素として扱われます**EntityContainer**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-398">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-399">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-399">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-400">次の表に適用できる属性の説明、 **Using**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-400">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="23dd4-401">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-401">Attribute Name</span></span> | <span data-ttu-id="23dd4-402">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-402">Is Required</span></span> | <span data-ttu-id="23dd4-403">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-403">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="23dd4-404">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-404">**Name**</span></span>       | <span data-ttu-id="23dd4-405">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-405">Yes</span></span>         | <span data-ttu-id="23dd4-406">エンティティ コンテナー名。</span><span class="sxs-lookup"><span data-stu-id="23dd4-406">The name of the entity container.</span></span>                               |
| <span data-ttu-id="23dd4-407">**拡張**</span><span class="sxs-lookup"><span data-stu-id="23dd4-407">**Extends**</span></span>    | <span data-ttu-id="23dd4-408">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-408">No</span></span>          | <span data-ttu-id="23dd4-409">同じ名前空間内にある別のエンティティ コンテナーの名前 </span><span class="sxs-lookup"><span data-stu-id="23dd4-409">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-410">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntityContainer**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-410">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="23dd4-411">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-411">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-412">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-412">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-413">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-413">Example</span></span>

<span data-ttu-id="23dd4-414">次の例は、 **EntityContainer** 3 つのエンティティ セットと 2 つのアソシエーション セットを定義する要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-414">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

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
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="23dd4-415">EntitySet 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-415">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="23dd4-416">**EntitySet**概念スキーマ定義言語要素は、エンティティ型のインスタンスとそのエンティティ型から派生した任意の型のインスタンスの論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="23dd4-416">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="23dd4-417">エンティティ型とエンティティ セットの間のリレーションシップは、リレーショナル データベースの行とテーブルの間のリレーションシップと似ています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-417">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="23dd4-418">エンティティ型は、行と同様に関連データのセットを定義し、エンティティ セットには、テーブルと同様に、その定義のインスタンスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-418">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="23dd4-419">エンティティ セットは、エンティティ型のインスタンスをグループ化するための構造を提供します。これにより、データ ソース内の関連するデータ構造に、エンティティ型のインスタンスをマッピングできるようになります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-419">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="23dd4-420">特定のエンティティ型に対して複数のエンティティ セットを定義できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-420">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="23dd4-421">EF デザイナーは、型ごとに複数のエンティティ セットを含んでいる概念モデルをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-421">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="23dd4-422">**EntitySet**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-422">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-423">Documentation 要素 (0 個または 1 つの要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-423">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="23dd4-424">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-424">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-425">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-425">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-426">次の表に適用できる属性の説明、 **EntitySet**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-426">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="23dd4-427">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-427">Attribute Name</span></span> | <span data-ttu-id="23dd4-428">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-428">Is Required</span></span> | <span data-ttu-id="23dd4-429">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-429">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-430">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-430">**Name**</span></span>       | <span data-ttu-id="23dd4-431">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-431">Yes</span></span>         | <span data-ttu-id="23dd4-432">エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-432">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="23dd4-433">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="23dd4-433">**EntityType**</span></span> | <span data-ttu-id="23dd4-434">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-434">Yes</span></span>         | <span data-ttu-id="23dd4-435">エンティティ型の完全修飾名。そのインスタンスは、エンティティ セットに格納されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-435">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-436">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntitySet**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-436">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="23dd4-437">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-437">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-438">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-438">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-439">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-439">Example</span></span>

<span data-ttu-id="23dd4-440">次の例は、 **EntityContainer** 3 つの要素**EntitySet**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-440">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

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
 

<span data-ttu-id="23dd4-441">型ごとに複数のエンティティ セット (Multiple-Entity-Sets-per-Type: MEST) を定義できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-441">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="23dd4-442">次の例の 2 つのエンティティ セットを持つエンティティ コンテナーを定義する、**帳**エンティティの種類。</span><span class="sxs-lookup"><span data-stu-id="23dd4-442">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

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
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="23dd4-443">EntityType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-443">EntityType Element (CSDL)</span></span>

<span data-ttu-id="23dd4-444">**EntityType**要素は、顧客や、概念モデルでの注文などの最上位の概念の構造を表します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-444">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="23dd4-445">エンティティ型は、アプリケーションのエンティティ型インスタンス用テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="23dd4-445">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="23dd4-446">各テンプレートには、次の情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-446">Each template contains the following information:</span></span>

-   <span data-ttu-id="23dd4-447">一意の名前 </span><span class="sxs-lookup"><span data-stu-id="23dd4-447">A unique name.</span></span> <span data-ttu-id="23dd4-448">(必須) </span><span class="sxs-lookup"><span data-stu-id="23dd4-448">(Required.)</span></span>
-   <span data-ttu-id="23dd4-449">1 つ以上のプロパティにより定義されるエンティティ キー </span><span class="sxs-lookup"><span data-stu-id="23dd4-449">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="23dd4-450">(必須) </span><span class="sxs-lookup"><span data-stu-id="23dd4-450">(Required.)</span></span>
-   <span data-ttu-id="23dd4-451">データ格納用のプロパティ </span><span class="sxs-lookup"><span data-stu-id="23dd4-451">Properties for containing data.</span></span> <span data-ttu-id="23dd4-452">(省略可能) </span><span class="sxs-lookup"><span data-stu-id="23dd4-452">(Optional.)</span></span>
-   <span data-ttu-id="23dd4-453">アソシエーションの 1 つの End から別の End へのナビゲーションを可能にするナビゲーション プロパティ </span><span class="sxs-lookup"><span data-stu-id="23dd4-453">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="23dd4-454">(省略可能) </span><span class="sxs-lookup"><span data-stu-id="23dd4-454">(Optional.)</span></span>

<span data-ttu-id="23dd4-455">アプリケーションでは、エンティティ型のインスタンスが特定のオブジェクト (特定の顧客や注文など) を表します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-455">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="23dd4-456">エンティティ型の各インスタンスに対して、エンティティ セット内のエンティティ キーを一意にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-456">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="23dd4-457">2 つのエンティティ型のインスタンスは、型が同じであり、エンティティ キーの値が等しい場合にのみ、等価のインスタンスと見なされます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-457">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="23dd4-458">**EntityType**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-458">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-459">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-459">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-460">キー (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-460">Key (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-461">プロパティ (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-461">Property (zero or more elements)</span></span>
-   <span data-ttu-id="23dd4-462">NavigationProperty (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-462">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="23dd4-463">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-463">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-464">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-464">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-465">次の表に適用できる属性の説明、 **EntityType**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-465">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="23dd4-466">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-466">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="23dd4-467">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-467">Is Required</span></span> | <span data-ttu-id="23dd4-468">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-468">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-469">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-469">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="23dd4-470">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-470">Yes</span></span>         | <span data-ttu-id="23dd4-471">エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-471">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="23dd4-472">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="23dd4-472">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="23dd4-473">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-473">No</span></span>          | <span data-ttu-id="23dd4-474">別のエンティティ型の名前。この型が定義中のエンティティ型の基本型です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-474">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="23dd4-475">**抽象**</span><span class="sxs-lookup"><span data-stu-id="23dd4-475">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="23dd4-476">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-476">No</span></span>          | <span data-ttu-id="23dd4-477">**True**または**False**エンティティ型が抽象型がかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-477">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="23dd4-478">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="23dd4-478">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="23dd4-479">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-479">No</span></span>          | <span data-ttu-id="23dd4-480">**True**または**False**エンティティ型がオープンなエンティティ型がかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-480">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="23dd4-481">>、 **OpenType**属性は、ADO.NET Data Services で使用される概念モデルで定義されているエンティティ型に適用されるのみです。</span><span class="sxs-lookup"><span data-stu-id="23dd4-481">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="23dd4-482">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntityType**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-482">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="23dd4-483">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-483">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-484">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-484">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-485">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-485">Example</span></span>

<span data-ttu-id="23dd4-486">次の例は、 **EntityType** 3 つの要素**プロパティ**要素と 2 つ**NavigationProperty**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-486">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

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
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="23dd4-487">EnumType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-487">EnumType Element (CSDL)</span></span>

<span data-ttu-id="23dd4-488">**EnumType**要素は、列挙型を表します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-488">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="23dd4-489">**EnumType**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-489">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-490">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-490">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-491">メンバー (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-491">Member (zero or more elements)</span></span>
-   <span data-ttu-id="23dd4-492">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-492">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-493">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-493">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-494">次の表に適用できる属性の説明、 **EnumType**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-494">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="23dd4-495">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-495">Attribute Name</span></span>     | <span data-ttu-id="23dd4-496">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-496">Is Required</span></span> | <span data-ttu-id="23dd4-497">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-497">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-498">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-498">**Name**</span></span>           | <span data-ttu-id="23dd4-499">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-499">Yes</span></span>         | <span data-ttu-id="23dd4-500">エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-500">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="23dd4-501">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="23dd4-501">**IsFlags**</span></span>        | <span data-ttu-id="23dd4-502">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-502">No</span></span>          | <span data-ttu-id="23dd4-503">**True**または**False**フラグのセットとして列挙型を使用するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-503">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="23dd4-504">既定値は**False。** します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-504">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="23dd4-505">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="23dd4-505">**UnderlyingType**</span></span> | <span data-ttu-id="23dd4-506">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-506">No</span></span>          | <span data-ttu-id="23dd4-507">**Edm.Byte**、 **Edm.Int16**、 **Edm.Int32**、 **Edm.Int64**または**Edm.SByte**型の値の範囲を定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-507">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span>   <span data-ttu-id="23dd4-508">基になる列挙体の要素の型の既定値は**Edm.Int32。** します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-508">The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-509">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EnumType**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-509">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="23dd4-510">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-510">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-511">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-511">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-512">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-512">Example</span></span>

<span data-ttu-id="23dd4-513">次の例は、 **EnumType** 3 つの要素**メンバー**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-513">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="23dd4-514">Function 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-514">Function Element (CSDL)</span></span>

<span data-ttu-id="23dd4-515">**関数**概念スキーマ定義言語 (CSDL) での要素を使用して関数を概念モデルで宣言または定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-515">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="23dd4-516">DefiningExpression 要素を使用して、関数が定義されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-516">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="23dd4-517">A**関数**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-517">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-518">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-518">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-519">パラメーター (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-519">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="23dd4-520">DefiningExpression (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-520">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-521">ReturnType (Function) (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-521">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-522">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-522">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="23dd4-523">いずれかの関数の型を指定する戻り、 **ReturnType** (関数) の要素または**ReturnType**両方ではなく属性 (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-523">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="23dd4-524">有効な戻り値の型には、EdmSimpleType、エンティティ型、複合型、行型、または参照型 (あるいはこれらの型のいずれかのコレクション) があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-524">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-525">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-525">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-526">次の表に適用できる属性の説明、**関数**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-526">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="23dd4-527">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-527">Attribute Name</span></span> | <span data-ttu-id="23dd4-528">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-528">Is Required</span></span> | <span data-ttu-id="23dd4-529">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-529">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="23dd4-530">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-530">**Name**</span></span>       | <span data-ttu-id="23dd4-531">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-531">Yes</span></span>         | <span data-ttu-id="23dd4-532">関数の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-532">The name of the function.</span></span>          |
| <span data-ttu-id="23dd4-533">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="23dd4-533">**ReturnType**</span></span> | <span data-ttu-id="23dd4-534">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-534">No</span></span>          | <span data-ttu-id="23dd4-535">関数の戻り値の型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-535">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-536">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**関数**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-536">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="23dd4-537">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-537">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-538">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-538">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-539">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-539">Example</span></span>

<span data-ttu-id="23dd4-540">次の例では、**関数**インストラクターが雇用されてからの年数を返す関数を定義する要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-540">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="23dd4-541">FunctionImport 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-541">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="23dd4-542">**FunctionImport**概念スキーマ定義言語 (CSDL) 内の要素は、データ ソースで定義されているが、概念モデルでのオブジェクトで使用できる関数を表します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-542">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="23dd4-543">たとえば、データベースのストアド プロシージャを表すストレージ モデルの関数の要素を使用できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-543">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="23dd4-544">A **FunctionImport**概念モデル内の要素は、Entity Framework アプリケーションに対応する関数を表し、FunctionImportMapping 要素を使用して、ストレージ モデル関数にマップされます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-544">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="23dd4-545">関数がアプリケーションで呼び出されると、対応するストアド プロシージャがデータベースで実行されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-545">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="23dd4-546">**FunctionImport**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-546">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-547">ドキュメント (0 個または 1 つの要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-547">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="23dd4-548">パラメーター (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-548">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="23dd4-549">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-549">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="23dd4-550">ReturnType (FunctionImport) (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-550">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="23dd4-551">1 つ**パラメーター**関数が受け取る各パラメーターの要素を定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-551">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="23dd4-552">いずれかの関数の型を指定する戻り、 **ReturnType** (FunctionImport) 要素または**ReturnType**両方ではなく属性 (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-552">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="23dd4-553">戻り値の型の値は EdmSimpleType、EntityType、または ComplexType のコレクションである必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-553">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-554">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-554">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-555">次の表に適用できる属性の説明、 **FunctionImport**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-555">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="23dd4-556">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-556">Attribute Name</span></span>   | <span data-ttu-id="23dd4-557">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-557">Is Required</span></span> | <span data-ttu-id="23dd4-558">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-558">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-559">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-559">**Name**</span></span>         | <span data-ttu-id="23dd4-560">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-560">Yes</span></span>         | <span data-ttu-id="23dd4-561">インポートされる関数の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-561">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="23dd4-562">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="23dd4-562">**ReturnType**</span></span>   | <span data-ttu-id="23dd4-563">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-563">No</span></span>          | <span data-ttu-id="23dd4-564">関数が返す型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-564">The type that the function returns.</span></span> <span data-ttu-id="23dd4-565">関数が値を返さない場合には、この属性を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="23dd4-565">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="23dd4-566">それ以外の場合、値には、ComplexType、EntityType、EDMSimpleType のコレクションがあります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-566">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="23dd4-567">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="23dd4-567">**EntitySet**</span></span>    | <span data-ttu-id="23dd4-568">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-568">No</span></span>          | <span data-ttu-id="23dd4-569">関数は、エンティティのコレクションを返す場合の型の値、 **EntitySet**する必要がありますが、エンティティ セット、コレクションが属する。</span><span class="sxs-lookup"><span data-stu-id="23dd4-569">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="23dd4-570">それ以外の場合、 **EntitySet**属性を使用しない必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-570">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="23dd4-571">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="23dd4-571">**IsComposable**</span></span> | <span data-ttu-id="23dd4-572">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-572">No</span></span>          | <span data-ttu-id="23dd4-573">値が設定されている場合 true の場合、関数がコンポーザブルな (テーブル値関数) と、LINQ クエリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-573">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span>  <span data-ttu-id="23dd4-574">既定値は **false** です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-574">The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="23dd4-575">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **FunctionImport**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-575">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="23dd4-576">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-576">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-577">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-577">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-578">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-578">Example</span></span>

<span data-ttu-id="23dd4-579">次の例は、 **FunctionImport**要素を 1 つのパラメーターを受け取り、エンティティ型のコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-579">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="23dd4-580">key 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-580">Key Element (CSDL)</span></span>

<span data-ttu-id="23dd4-581">**キー**要素は、EntityType 要素の子要素であり定義、*エンティティ キー* (プロパティまたは一連の id を指定するエンティティ型のプロパティ)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-581">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="23dd4-582">エンティティ キーを構成するプロパティは、デザイン時に選択されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-582">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="23dd4-583">エンティティ キー プロパティの値は、実行時のエンティティ セット内のエンティティ型のインスタンスを一意に識別する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-583">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="23dd4-584">エンティティ キーを構成するプロパティには、エンティティ セット内のインスタンスの一意性を保証するものを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-584">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="23dd4-585">**キー**要素が 1 つまたは複数のエンティティ型のプロパティを参照することで、エンティティ キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-585">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="23dd4-586">**キー**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-586">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="23dd4-587">PropertyRef (1 つまたは複数の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-587">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="23dd4-588">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-588">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-589">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-589">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-590">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**キー**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-590">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="23dd4-591">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-592">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="23dd4-593">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-593">Example</span></span>

<span data-ttu-id="23dd4-594">次の例は、という名前のエンティティ型を定義します。**帳**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-594">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="23dd4-595">エンティティ キーが参照することで定義されている、 **ISBN**エンティティ型のプロパティ。</span><span class="sxs-lookup"><span data-stu-id="23dd4-595">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="23dd4-596">**ISBN**国際標準図書番号 (ISBN) は、書籍を一意に識別するため、プロパティがエンティティ キーとして適しています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-596">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="23dd4-597">次の例では、エンティティ型 (**作成者**) の 2 つのプロパティで構成されるエンティティ キーを持つ**名前**と**アドレス**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-597">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

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
 

<span data-ttu-id="23dd4-598">使用して**名前**と**アドレス**同じ名前の 2 つの作成者は、同じアドレスでライブに可能性がないため、エンティティのキーが妥当な選択が。</span><span class="sxs-lookup"><span data-stu-id="23dd4-598">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="23dd4-599">ただし、エンティティ キーのこの選択では、エンティティ セット内のエンティティ キーの一意性を絶対的に保証することはできません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-599">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="23dd4-600">などのプロパティの追加**著者 Id**を一意に識別するために使用される、著者がここで推奨されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-600">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="23dd4-601">Member 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-601">Member Element (CSDL)</span></span>

<span data-ttu-id="23dd4-602">**メンバー**要素は EnumType 要素の子要素であり、列挙型のメンバーを定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-602">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-603">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-603">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-604">次の表に適用できる属性の説明、 **FunctionImport**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-604">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="23dd4-605">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-605">Attribute Name</span></span> | <span data-ttu-id="23dd4-606">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-606">Is Required</span></span> | <span data-ttu-id="23dd4-607">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-607">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-608">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-608">**Name**</span></span>       | <span data-ttu-id="23dd4-609">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-609">Yes</span></span>         | <span data-ttu-id="23dd4-610">メンバーの名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-610">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="23dd4-611">**[値]**</span><span class="sxs-lookup"><span data-stu-id="23dd4-611">**Value**</span></span>      | <span data-ttu-id="23dd4-612">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-612">No</span></span>          | <span data-ttu-id="23dd4-613">メンバーの値。</span><span class="sxs-lookup"><span data-stu-id="23dd4-613">The value of the member.</span></span> <span data-ttu-id="23dd4-614">既定では、最初のメンバーが値 0、および後続の列挙子の値が 1 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-614">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="23dd4-615">同じ値を持つ複数のメンバーが存在します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-615">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-616">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **FunctionImport**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-616">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="23dd4-617">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-617">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-618">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-618">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-619">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-619">Example</span></span>

<span data-ttu-id="23dd4-620">次の例は、 **EnumType** 3 つの要素**メンバー**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-620">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="23dd4-621">NavigationProperty 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-621">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="23dd4-622">A **NavigationProperty**要素は、アソシエーションの他方の end への参照を提供するナビゲーション プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-622">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="23dd4-623">プロパティ要素で定義されたプロパティとは異なり、図形とデータの特性のナビゲーション プロパティを定義しません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-623">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="23dd4-624">ナビゲーション プロパティは、アソシエーション内で 2 つのエンティティ型間を移動するための手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-624">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="23dd4-625">ナビゲーション プロパティは、アソシエーション End の両方のエンティティ型で省略可能です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-625">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="23dd4-626">1 つのアソシエーション End のエンティティ型にナビゲーション プロパティを定義した場合に、そのアソシエーションの他方の End でもエンティティ型にナビゲーション プロパティを定義する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-626">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="23dd4-627">ナビゲーション プロパティによって返されるデータ型は、リモートのアソシエーション End の多重度により決まります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-627">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="23dd4-628">たとえば、ナビゲーション プロパティ、 **OrdersNavProp**、上に存在する、**顧客**エンティティ型間の一対多のアソシエーションをナビゲートして**顧客**と**順序**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-628">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="23dd4-629">ナビゲーション プロパティのリモートのアソシエーション end がある多重度多くあるため (\*)、そのデータ型は、コレクション (の**順序**)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-629">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="23dd4-630">同様に、ナビゲーション プロパティの場合は、 **CustomerNavProp**、上に存在する、**順序**エンティティ型の場合は、そのデータ型になります**顧客**ので、リモート end の多重度1 つ (1)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-630">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="23dd4-631">A **NavigationProperty**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-631">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-632">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-632">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-633">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-633">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-634">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-634">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-635">次の表に適用できる属性の説明、 **NavigationProperty**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-635">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="23dd4-636">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-636">Attribute Name</span></span>   | <span data-ttu-id="23dd4-637">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-637">Is Required</span></span> | <span data-ttu-id="23dd4-638">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-638">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-639">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-639">**Name**</span></span>         | <span data-ttu-id="23dd4-640">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-640">Yes</span></span>         | <span data-ttu-id="23dd4-641">ナビゲーション プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-641">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="23dd4-642">**リレーションシップ**</span><span class="sxs-lookup"><span data-stu-id="23dd4-642">**Relationship**</span></span> | <span data-ttu-id="23dd4-643">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-643">Yes</span></span>         | <span data-ttu-id="23dd4-644">モデルのスコープ内にあるアソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-644">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="23dd4-645">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="23dd4-645">**ToRole**</span></span>       | <span data-ttu-id="23dd4-646">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-646">Yes</span></span>         | <span data-ttu-id="23dd4-647">ナビゲーションが終了する側のアソシエーション End。</span><span class="sxs-lookup"><span data-stu-id="23dd4-647">The end of the association at which navigation ends.</span></span> <span data-ttu-id="23dd4-648">値、 **ToRole**属性には、いずれかの値と同じである必要があります、**ロール**(AssociationEnd 要素で定義されている) のアソシエーション end のいずれかで定義されている属性。</span><span class="sxs-lookup"><span data-stu-id="23dd4-648">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="23dd4-649">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="23dd4-649">**FromRole**</span></span>     | <span data-ttu-id="23dd4-650">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-650">Yes</span></span>         | <span data-ttu-id="23dd4-651">ナビゲーションが始まる側のアソシエーション End。</span><span class="sxs-lookup"><span data-stu-id="23dd4-651">The end of the association from which navigation begins.</span></span> <span data-ttu-id="23dd4-652">値、 **FromRole**属性には、いずれかの値と同じである必要があります、**ロール**(AssociationEnd 要素で定義されている) のアソシエーション end のいずれかで定義されている属性。</span><span class="sxs-lookup"><span data-stu-id="23dd4-652">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-653">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **NavigationProperty**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-653">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="23dd4-654">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-654">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-655">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-655">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-656">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-656">Example</span></span>

<span data-ttu-id="23dd4-657">次の例では、エンティティ型を定義します (**帳**) 2 つのナビゲーション プロパティを持つ (**PublishedBy**と**WrittenBy**)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-657">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

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
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="23dd4-658">OnDelete 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-658">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="23dd4-659">**OnDelete**概念スキーマ定義言語 (CSDL) 内の要素の関連付けが接続されている動作を定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-659">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="23dd4-660">場合、**アクション**属性に設定されて**Cascade**アソシエーションの一方の end に関連、アソシエーションの他方の end でエンティティ型は最初の end でエンティティ型が削除されたときに削除されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-660">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="23dd4-661">2 つのエンティティ型間の関連付けは、プライマリ キーと主キー リレーションシップに関係なく、アソシエーションの他方の end にあるプリンシパル オブジェクトが削除されたときに読み込まれた依存オブジェクトが削除された場合、 **OnDelete**指定。</span><span class="sxs-lookup"><span data-stu-id="23dd4-661">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="23dd4-662">**OnDelete**要素にのみ影響するアプリケーションのランタイム動作は、データ ソース内の動作は影響しません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-662">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="23dd4-663">データ ソースで定義された動作は、アプリケーションで定義された動作と同じである必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-663">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="23dd4-664">**OnDelete**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-664">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-665">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-665">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-666">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-666">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-667">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-667">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-668">次の表に適用できる属性の説明、 **OnDelete**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-668">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="23dd4-669">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-669">Attribute Name</span></span> | <span data-ttu-id="23dd4-670">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-670">Is Required</span></span> | <span data-ttu-id="23dd4-671">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-671">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-672">**動作**</span><span class="sxs-lookup"><span data-stu-id="23dd4-672">**Action**</span></span>     | <span data-ttu-id="23dd4-673">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-673">Yes</span></span>         | <span data-ttu-id="23dd4-674">**Cascade**または**None**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-674">**Cascade** or **None**.</span></span> <span data-ttu-id="23dd4-675">場合**Cascade**、依存エンティティ型は、プリンシパル エンティティ型が削除されたときに削除されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-675">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="23dd4-676">場合**None**、プリンシパル エンティティ型が削除されたときに、依存エンティティ型は削除されません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-676">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-677">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**アソシエーション**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-677">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="23dd4-678">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-678">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-679">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-679">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-680">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-680">Example</span></span>

<span data-ttu-id="23dd4-681">次の例は、**アソシエーション**を定義する要素、 **CustomerOrders**アソシエーション。</span><span class="sxs-lookup"><span data-stu-id="23dd4-681">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="23dd4-682">**OnDelete**要素には、ことを示しますすべて**注文**に関連する特定の**顧客**と ObjectContext に読み込まれたときに削除されます、 **顧客**は削除されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-682">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="23dd4-683">Parameter 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-683">Parameter Element (CSDL)</span></span>

<span data-ttu-id="23dd4-684">**パラメーター**概念スキーマ定義言語 (CSDL) で要素が、FunctionImport 要素または Function 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-684">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="23dd4-685">FunctionImport 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="23dd4-685">FunctionImport Element Application</span></span>

<span data-ttu-id="23dd4-686">A**パラメーター**要素 (の子として、 **FunctionImport**要素) は、CSDL で宣言された関数インポートの入力と出力のパラメーターを定義するために使用します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-686">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="23dd4-687">**パラメーター**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-687">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-688">ドキュメント (0 個または 1 つの要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-688">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="23dd4-689">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-689">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-690">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-690">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-691">次の表に適用できる属性、**パラメーター**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-691">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="23dd4-692">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-692">Attribute Name</span></span> | <span data-ttu-id="23dd4-693">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-693">Is Required</span></span> | <span data-ttu-id="23dd4-694">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-694">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-695">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-695">**Name**</span></span>       | <span data-ttu-id="23dd4-696">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-696">Yes</span></span>         | <span data-ttu-id="23dd4-697">パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-697">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="23dd4-698">**Type**</span><span class="sxs-lookup"><span data-stu-id="23dd4-698">**Type**</span></span>       | <span data-ttu-id="23dd4-699">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-699">Yes</span></span>         | <span data-ttu-id="23dd4-700">パラメーターの型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-700">The parameter type.</span></span> <span data-ttu-id="23dd4-701">値がある必要があります、 **EDMSimpleType**またはモデルのスコープ内にある複合型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-701">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="23dd4-702">**モード**</span><span class="sxs-lookup"><span data-stu-id="23dd4-702">**Mode**</span></span>       | <span data-ttu-id="23dd4-703">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-703">No</span></span>          | <span data-ttu-id="23dd4-704">**In**、**アウト**、または**InOut**パラメーターは、入力、出力、または入力/出力パラメーターかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-704">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="23dd4-705">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-705">**MaxLength**</span></span>  | <span data-ttu-id="23dd4-706">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-706">No</span></span>          | <span data-ttu-id="23dd4-707">パラメーターの許容最大長。</span><span class="sxs-lookup"><span data-stu-id="23dd4-707">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="23dd4-708">**精度**</span><span class="sxs-lookup"><span data-stu-id="23dd4-708">**Precision**</span></span>  | <span data-ttu-id="23dd4-709">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-709">No</span></span>          | <span data-ttu-id="23dd4-710">パラメーターの有効桁数。</span><span class="sxs-lookup"><span data-stu-id="23dd4-710">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="23dd4-711">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="23dd4-711">**Scale**</span></span>      | <span data-ttu-id="23dd4-712">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-712">No</span></span>          | <span data-ttu-id="23dd4-713">パラメーターの小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="23dd4-713">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="23dd4-714">**SRID**</span><span class="sxs-lookup"><span data-stu-id="23dd4-714">**SRID**</span></span>       | <span data-ttu-id="23dd4-715">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-715">No</span></span>          | <span data-ttu-id="23dd4-716">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-716">Spatial System Reference Identifier.</span></span> <span data-ttu-id="23dd4-717">空間型のパラメーターに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-717">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="23dd4-718">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-718">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-719">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**パラメーター**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-719">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="23dd4-720">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-720">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-721">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-721">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="23dd4-722">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-722">Example</span></span>

<span data-ttu-id="23dd4-723">次の例は、 **FunctionImport**要素を 1 つ**パラメーター**子要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-723">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="23dd4-724">関数は、1 つの入力パラメーターを受け取り、エンティティ型のコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-724">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="23dd4-725">Function 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="23dd4-725">Function Element Application</span></span>

<span data-ttu-id="23dd4-726">A**パラメーター**要素 (の子として、**関数**要素)、概念モデルで定義または宣言される関数のパラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-726">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="23dd4-727">**パラメーター**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-727">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-728">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-728">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="23dd4-729">CollectionType (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-729">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="23dd4-730">ReferenceType (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-730">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="23dd4-731">RowType (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-731">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="23dd4-732">1 つだけ、 **CollectionType**、 **ReferenceType**、または**RowType**要素の子要素であることができます、**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-732">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="23dd4-733">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-733">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="23dd4-734">Annotation 要素は、それ以外のすべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-734">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="23dd4-735">Annotation 要素は、CSDL v2 以降にのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-735">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-736">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-736">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-737">次の表に適用できる属性、**パラメーター**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-737">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="23dd4-738">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-738">Attribute Name</span></span>   | <span data-ttu-id="23dd4-739">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-739">Is Required</span></span> | <span data-ttu-id="23dd4-740">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-740">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-741">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-741">**Name**</span></span>         | <span data-ttu-id="23dd4-742">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-742">Yes</span></span>         | <span data-ttu-id="23dd4-743">パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-743">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="23dd4-744">**Type**</span><span class="sxs-lookup"><span data-stu-id="23dd4-744">**Type**</span></span>         | <span data-ttu-id="23dd4-745">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-745">No</span></span>          | <span data-ttu-id="23dd4-746">パラメーターの型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-746">The parameter type.</span></span> <span data-ttu-id="23dd4-747">パラメーターには次のいずれかの型 (またはこれらの型のコレクション) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-747">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="23dd4-748">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="23dd4-748">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="23dd4-749">エンティティ型</span><span class="sxs-lookup"><span data-stu-id="23dd4-749">entity type</span></span> <br/> <span data-ttu-id="23dd4-750">複合型</span><span class="sxs-lookup"><span data-stu-id="23dd4-750">complex type</span></span> <br/> <span data-ttu-id="23dd4-751">行型</span><span class="sxs-lookup"><span data-stu-id="23dd4-751">row type</span></span> <br/> <span data-ttu-id="23dd4-752">参照型</span><span class="sxs-lookup"><span data-stu-id="23dd4-752">reference type</span></span>                             |
| <span data-ttu-id="23dd4-753">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="23dd4-753">**Nullable**</span></span>     | <span data-ttu-id="23dd4-754">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-754">No</span></span>          | <span data-ttu-id="23dd4-755">**True** (既定値) または**False**プロパティを持つことができるかどうかに応じて、 **null**値。</span><span class="sxs-lookup"><span data-stu-id="23dd4-755">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="23dd4-756">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="23dd4-756">**DefaultValue**</span></span> | <span data-ttu-id="23dd4-757">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-757">No</span></span>          | <span data-ttu-id="23dd4-758">プロパティの既定値。</span><span class="sxs-lookup"><span data-stu-id="23dd4-758">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="23dd4-759">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-759">**MaxLength**</span></span>    | <span data-ttu-id="23dd4-760">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-760">No</span></span>          | <span data-ttu-id="23dd4-761">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="23dd4-761">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="23dd4-762">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-762">**FixedLength**</span></span>  | <span data-ttu-id="23dd4-763">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-763">No</span></span>          | <span data-ttu-id="23dd4-764">**True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-764">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="23dd4-765">**精度**</span><span class="sxs-lookup"><span data-stu-id="23dd4-765">**Precision**</span></span>    | <span data-ttu-id="23dd4-766">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-766">No</span></span>          | <span data-ttu-id="23dd4-767">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="23dd4-767">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="23dd4-768">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="23dd4-768">**Scale**</span></span>        | <span data-ttu-id="23dd4-769">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-769">No</span></span>          | <span data-ttu-id="23dd4-770">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="23dd4-770">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="23dd4-771">**SRID**</span><span class="sxs-lookup"><span data-stu-id="23dd4-771">**SRID**</span></span>         | <span data-ttu-id="23dd4-772">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-772">No</span></span>          | <span data-ttu-id="23dd4-773">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-773">Spatial System Reference Identifier.</span></span> <span data-ttu-id="23dd4-774">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-774">Valid only for properties of spatial types.</span></span> <span data-ttu-id="23dd4-775">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-775">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="23dd4-776">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="23dd4-776">**Unicode**</span></span>      | <span data-ttu-id="23dd4-777">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-777">No</span></span>          | <span data-ttu-id="23dd4-778">**True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-778">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="23dd4-779">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="23dd4-779">**Collation**</span></span>    | <span data-ttu-id="23dd4-780">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-780">No</span></span>          | <span data-ttu-id="23dd4-781">データ ソースで使用する照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="23dd4-781">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="23dd4-782">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**パラメーター**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-782">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="23dd4-783">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-783">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-784">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-784">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="23dd4-785">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-785">Example</span></span>

<span data-ttu-id="23dd4-786">次の例は、**関数**いずれかを使用する要素**パラメーター**関数パラメーターを定義する子要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-786">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
``` 

 

## <a name="principal-element-csdl"></a><span data-ttu-id="23dd4-787">Principal 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-787">Principal Element (CSDL)</span></span>

<span data-ttu-id="23dd4-788">**プリンシパル**概念スキーマ定義言語 (CSDL) での要素は参照に関する制約のプリンシパル end を定義する ReferentialConstraint 要素に子要素です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-788">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="23dd4-789">A **ReferentialConstraint**要素は、リレーショナル データベースで参照整合性制約と同様の機能を定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-789">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="23dd4-790">データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティが別のエンティティ型のエンティティ キーを参照できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-790">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="23dd4-791">参照されるエンティティ型が呼び出される、*プリンシパル end*の制約。</span><span class="sxs-lookup"><span data-stu-id="23dd4-791">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="23dd4-792">プリンシパル end を参照するエンティティ型が呼び出される、*依存 end*の制約。</span><span class="sxs-lookup"><span data-stu-id="23dd4-792">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="23dd4-793">**PropertyRef**要素を使用すると、依存 end により参照されるキーを指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-793">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="23dd4-794">**プリンシパル**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-794">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-795">PropertyRef (1 つまたは複数の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-795">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="23dd4-796">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-796">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-797">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-797">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-798">次の表に適用できる属性の説明、**プリンシパル**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-798">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="23dd4-799">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-799">Attribute Name</span></span> | <span data-ttu-id="23dd4-800">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-800">Is Required</span></span> | <span data-ttu-id="23dd4-801">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-801">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="23dd4-802">**ロール**</span><span class="sxs-lookup"><span data-stu-id="23dd4-802">**Role**</span></span>       | <span data-ttu-id="23dd4-803">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-803">Yes</span></span>         | <span data-ttu-id="23dd4-804">アソシエーションのプリンシパル End 上のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-804">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-805">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プリンシパル**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-805">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="23dd4-806">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-806">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-807">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-807">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-808">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-808">Example</span></span>

<span data-ttu-id="23dd4-809">次の例は、 **ReferentialConstraint**要素の定義の一部である、 **PublishedBy**アソシエーション。</span><span class="sxs-lookup"><span data-stu-id="23dd4-809">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="23dd4-810">**Id**のプロパティ、**パブリッシャー**参照に関する制約のプリンシパル end のエンティティ型を使用します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-810">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

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
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="23dd4-811">Property 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-811">Property Element (CSDL)</span></span>

<span data-ttu-id="23dd4-812">**プロパティ**概念スキーマ定義言語 (CSDL) の要素は、EntityType 要素、複合型の要素または RowType 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-812">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="23dd4-813">EntityType 要素と ComplexType 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="23dd4-813">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="23dd4-814">**プロパティ**要素 (の子として**EntityType**または**ComplexType**要素) 図形と、エンティティ型または複合型のインスタンスに含まれるデータの特性を定義.</span><span class="sxs-lookup"><span data-stu-id="23dd4-814">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="23dd4-815">概念モデルのプロパティは、クラスで定義されるプロパティに似ています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-815">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="23dd4-816">クラスのプロパティがクラスの構造を定義し、オブジェクトに関する情報を伝達するのと同様に、概念モデルのプロパティはエンティティ型の構造を定義し、エンティティ型のインスタンスに関する情報を伝達します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-816">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="23dd4-817">**プロパティ**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-817">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-818">Documentation 要素 (0 個または 1 つの要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-818">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="23dd4-819">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-819">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="23dd4-820">次のファセットに適用できる、**プロパティ**要素: **Nullable**、 **DefaultValue**、 **MaxLength**、 **FixedLength**、**精度**、**スケール**、 **Unicode**、**照合**、 **ConcurrencyMode**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-820">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="23dd4-821">ファセットは、プロパティ値をデータ ストアに格納する方法に関する情報を提供する XML 属性です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-821">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="23dd4-822">ファセットは型のプロパティにのみ適用できます**EDMSimpleType**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-822">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-823">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-823">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-824">次の表に適用できる属性、**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-824">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="23dd4-825">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-825">Attribute Name</span></span>                                                         | <span data-ttu-id="23dd4-826">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-826">Is Required</span></span> | <span data-ttu-id="23dd4-827">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-827">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-828">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-828">**Name**</span></span>                                                               | <span data-ttu-id="23dd4-829">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-829">Yes</span></span>         | <span data-ttu-id="23dd4-830">プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-830">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="23dd4-831">**Type**</span><span class="sxs-lookup"><span data-stu-id="23dd4-831">**Type**</span></span>                                                               | <span data-ttu-id="23dd4-832">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-832">Yes</span></span>         | <span data-ttu-id="23dd4-833">プロパティ値の型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-833">The type of the property value.</span></span> <span data-ttu-id="23dd4-834">プロパティ値の型である必要があります、 **EDMSimpleType**またはモデルのスコープ内にある複合型 (完全修飾名で示されます)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-834">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="23dd4-835">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="23dd4-835">**Nullable**</span></span>                                                           | <span data-ttu-id="23dd4-836">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-836">No</span></span>          | <span data-ttu-id="23dd4-837">**True** (既定値) または<strong>False</strong>プロパティが null 値を持っているかどうかによって。</span><span class="sxs-lookup"><span data-stu-id="23dd4-837">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="23dd4-838">> 複合型プロパティがありますで CSDL v1`Nullable="False"`します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-838">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="23dd4-839">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="23dd4-839">**DefaultValue**</span></span>                                                       | <span data-ttu-id="23dd4-840">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-840">No</span></span>          | <span data-ttu-id="23dd4-841">プロパティの既定値。</span><span class="sxs-lookup"><span data-stu-id="23dd4-841">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="23dd4-842">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-842">**MaxLength**</span></span>                                                          | <span data-ttu-id="23dd4-843">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-843">No</span></span>          | <span data-ttu-id="23dd4-844">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="23dd4-844">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="23dd4-845">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-845">**FixedLength**</span></span>                                                        | <span data-ttu-id="23dd4-846">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-846">No</span></span>          | <span data-ttu-id="23dd4-847">**True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-847">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="23dd4-848">**精度**</span><span class="sxs-lookup"><span data-stu-id="23dd4-848">**Precision**</span></span>                                                          | <span data-ttu-id="23dd4-849">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-849">No</span></span>          | <span data-ttu-id="23dd4-850">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="23dd4-850">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="23dd4-851">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="23dd4-851">**Scale**</span></span>                                                              | <span data-ttu-id="23dd4-852">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-852">No</span></span>          | <span data-ttu-id="23dd4-853">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="23dd4-853">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="23dd4-854">**SRID**</span><span class="sxs-lookup"><span data-stu-id="23dd4-854">**SRID**</span></span>                                                               | <span data-ttu-id="23dd4-855">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-855">No</span></span>          | <span data-ttu-id="23dd4-856">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-856">Spatial System Reference Identifier.</span></span> <span data-ttu-id="23dd4-857">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-857">Valid only for properties of spatial types.</span></span> <span data-ttu-id="23dd4-858">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-858">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="23dd4-859">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="23dd4-859">**Unicode**</span></span>                                                            | <span data-ttu-id="23dd4-860">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-860">No</span></span>          | <span data-ttu-id="23dd4-861">**True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-861">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="23dd4-862">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="23dd4-862">**Collation**</span></span>                                                          | <span data-ttu-id="23dd4-863">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-863">No</span></span>          | <span data-ttu-id="23dd4-864">データ ソースで使用する照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="23dd4-864">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="23dd4-865">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="23dd4-865">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="23dd4-866">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-866">No</span></span>          | <span data-ttu-id="23dd4-867">**None** (既定値) または**固定**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-867">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="23dd4-868">値が設定されている場合**固定**プロパティの値は、オプティミスティック同時実行制御チェックで使用されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-868">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="23dd4-869">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-869">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="23dd4-870">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-870">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-871">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-871">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="23dd4-872">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-872">Example</span></span>

<span data-ttu-id="23dd4-873">次の例は、 **EntityType** 3 つの要素**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-873">The following example shows an **EntityType** element with three **Property** elements:</span></span>

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
 

<span data-ttu-id="23dd4-874">次の例は、 **ComplexType** 5 つの要素**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-874">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="23dd4-875">RowType 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="23dd4-875">RowType Element Application</span></span>

<span data-ttu-id="23dd4-876">**プロパティ**要素 (の子として、 **RowType**要素) に渡されるしたりできる、モデル定義関数から返されるデータの特性と図形を定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-876">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="23dd4-877">**プロパティ**要素は、次の子要素の 1 つだけであることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-877">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="23dd4-878">CollectionType</span><span class="sxs-lookup"><span data-stu-id="23dd4-878">CollectionType</span></span>
-   <span data-ttu-id="23dd4-879">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="23dd4-879">ReferenceType</span></span>
-   <span data-ttu-id="23dd4-880">RowType</span><span class="sxs-lookup"><span data-stu-id="23dd4-880">RowType</span></span>

<span data-ttu-id="23dd4-881">**プロパティ**要素は、数値の子注釈要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-881">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="23dd4-882">Annotation 要素は、CSDL v2 以降にのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-882">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-883">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-883">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-884">次の表に適用できる属性、**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-884">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="23dd4-885">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-885">Attribute Name</span></span>                                                     | <span data-ttu-id="23dd4-886">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-886">Is Required</span></span> | <span data-ttu-id="23dd4-887">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-887">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-888">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-888">**Name**</span></span>                                                           | <span data-ttu-id="23dd4-889">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-889">Yes</span></span>         | <span data-ttu-id="23dd4-890">プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-890">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="23dd4-891">**Type**</span><span class="sxs-lookup"><span data-stu-id="23dd4-891">**Type**</span></span>                                                           | <span data-ttu-id="23dd4-892">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-892">Yes</span></span>         | <span data-ttu-id="23dd4-893">プロパティ値の型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-893">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="23dd4-894">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="23dd4-894">**Nullable**</span></span>                                                       | <span data-ttu-id="23dd4-895">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-895">No</span></span>          | <span data-ttu-id="23dd4-896">**True** (既定値) または**False**プロパティが null 値を持っているかどうかによって。</span><span class="sxs-lookup"><span data-stu-id="23dd4-896">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="23dd4-897">> 複合型プロパティがありますで CSDL v1`Nullable="False"`します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-897">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="23dd4-898">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="23dd4-898">**DefaultValue**</span></span>                                                   | <span data-ttu-id="23dd4-899">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-899">No</span></span>          | <span data-ttu-id="23dd4-900">プロパティの既定値。</span><span class="sxs-lookup"><span data-stu-id="23dd4-900">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="23dd4-901">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-901">**MaxLength**</span></span>                                                      | <span data-ttu-id="23dd4-902">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-902">No</span></span>          | <span data-ttu-id="23dd4-903">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="23dd4-903">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="23dd4-904">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-904">**FixedLength**</span></span>                                                    | <span data-ttu-id="23dd4-905">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-905">No</span></span>          | <span data-ttu-id="23dd4-906">**True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-906">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="23dd4-907">**精度**</span><span class="sxs-lookup"><span data-stu-id="23dd4-907">**Precision**</span></span>                                                      | <span data-ttu-id="23dd4-908">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-908">No</span></span>          | <span data-ttu-id="23dd4-909">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="23dd4-909">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="23dd4-910">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="23dd4-910">**Scale**</span></span>                                                          | <span data-ttu-id="23dd4-911">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-911">No</span></span>          | <span data-ttu-id="23dd4-912">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="23dd4-912">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="23dd4-913">**SRID**</span><span class="sxs-lookup"><span data-stu-id="23dd4-913">**SRID**</span></span>                                                           | <span data-ttu-id="23dd4-914">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-914">No</span></span>          | <span data-ttu-id="23dd4-915">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-915">Spatial System Reference Identifier.</span></span> <span data-ttu-id="23dd4-916">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-916">Valid only for properties of spatial types.</span></span> <span data-ttu-id="23dd4-917">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-917">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="23dd4-918">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="23dd4-918">**Unicode**</span></span>                                                        | <span data-ttu-id="23dd4-919">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-919">No</span></span>          | <span data-ttu-id="23dd4-920">**True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-920">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="23dd4-921">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="23dd4-921">**Collation**</span></span>                                                      | <span data-ttu-id="23dd4-922">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-922">No</span></span>          | <span data-ttu-id="23dd4-923">データ ソースで使用する照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="23dd4-923">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="23dd4-924">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-924">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="23dd4-925">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-925">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-926">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-926">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="23dd4-927">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-927">Example</span></span>

<span data-ttu-id="23dd4-928">次の例は**プロパティ**要素、モデル定義関数の戻り値の型の形状を定義するために使用します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-928">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

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
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="23dd4-929">PropertyRef 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-929">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="23dd4-930">**PropertyRef**概念スキーマ定義言語 (CSDL) 内の要素は、プロパティは実行は、次のロールのいずれかを示すエンティティ型のプロパティを参照します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-930">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="23dd4-931">エンティティのキー (ID を指定するエンティティ型のプロパティまたは一連のプロパティ) の一部。</span><span class="sxs-lookup"><span data-stu-id="23dd4-931">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="23dd4-932">1 つまたは複数**PropertyRef**要素は、エンティティ キーを定義するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-932">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="23dd4-933">参照制約の依存 End または プリンシパル End。</span><span class="sxs-lookup"><span data-stu-id="23dd4-933">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="23dd4-934">**PropertyRef**要素が子要素としての annotation 要素 (0 個以上) があることができますのみです。</span><span class="sxs-lookup"><span data-stu-id="23dd4-934">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="23dd4-935">Annotation 要素は、CSDL v2 以降にのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-935">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-936">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-936">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-937">次の表に適用できる属性の説明、 **PropertyRef**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-937">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="23dd4-938">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-938">Attribute Name</span></span> | <span data-ttu-id="23dd4-939">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-939">Is Required</span></span> | <span data-ttu-id="23dd4-940">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-940">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="23dd4-941">**Name**</span><span class="sxs-lookup"><span data-stu-id="23dd4-941">**Name**</span></span>       | <span data-ttu-id="23dd4-942">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-942">Yes</span></span>         | <span data-ttu-id="23dd4-943">参照されているプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-943">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-944">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **PropertyRef**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-944">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="23dd4-945">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-945">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-946">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-946">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-947">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-947">Example</span></span>

<span data-ttu-id="23dd4-948">次の例は、エンティティ型を定義します (**帳**)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-948">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="23dd4-949">エンティティ キーが参照することで定義されている、 **ISBN**エンティティ型のプロパティ。</span><span class="sxs-lookup"><span data-stu-id="23dd4-949">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="23dd4-950">次の例では、2 つ**PropertyRef**要素を使用すると、その 2 つを示すプロパティ (**Id**と**PublisherId**) は、プリンシパル end と依存 end の参照制約です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-950">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

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
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="23dd4-951">ReferenceType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-951">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="23dd4-952">**ReferenceType**概念スキーマ定義言語 (CSDL) 内の要素をエンティティ型への参照を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-952">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="23dd4-953">**ReferenceType**要素は、次の要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-953">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="23dd4-954">ReturnType (関数)</span><span class="sxs-lookup"><span data-stu-id="23dd4-954">ReturnType (Function)</span></span>
-   <span data-ttu-id="23dd4-955">パラメーター</span><span class="sxs-lookup"><span data-stu-id="23dd4-955">Parameter</span></span>
-   <span data-ttu-id="23dd4-956">CollectionType</span><span class="sxs-lookup"><span data-stu-id="23dd4-956">CollectionType</span></span>

<span data-ttu-id="23dd4-957">**ReferenceType**要素は、関数のパラメーターまたは戻り値の型を定義するときに使用します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-957">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="23dd4-958">A **ReferenceType**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-958">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-959">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-959">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-960">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-960">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-961">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-961">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-962">次の表に適用できる属性の説明、 **ReferenceType**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-962">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="23dd4-963">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-963">Attribute Name</span></span> | <span data-ttu-id="23dd4-964">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-964">Is Required</span></span> | <span data-ttu-id="23dd4-965">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-965">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="23dd4-966">**Type**</span><span class="sxs-lookup"><span data-stu-id="23dd4-966">**Type**</span></span>       | <span data-ttu-id="23dd4-967">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-967">Yes</span></span>         | <span data-ttu-id="23dd4-968">参照先エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-968">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-969">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReferenceType**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-969">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="23dd4-970">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-970">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-971">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-971">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-972">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-972">Example</span></span>

<span data-ttu-id="23dd4-973">次の例は、 **ReferenceType**要素の子として使用される、**パラメーター**への参照を受け取るモデル定義関数内の要素を**Person**エンティティ種類:</span><span class="sxs-lookup"><span data-stu-id="23dd4-973">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

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
 

<span data-ttu-id="23dd4-974">次の例は、 **ReferenceType**要素の子として使用される、 **ReturnType** (関数) の要素への参照を返すモデル定義関数で、 **Person**エンティティの種類。</span><span class="sxs-lookup"><span data-stu-id="23dd4-974">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

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
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="23dd4-975">ReferentialConstraint 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-975">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="23dd4-976">A **ReferentialConstraint**概念スキーマ定義言語 (CSDL) 内の要素は、リレーショナル データベースで参照整合性制約と同様の機能を定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-976">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="23dd4-977">データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティが別のエンティティ型のエンティティ キーを参照できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-977">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="23dd4-978">参照されるエンティティ型が呼び出される、*プリンシパル end*の制約。</span><span class="sxs-lookup"><span data-stu-id="23dd4-978">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="23dd4-979">プリンシパル end を参照するエンティティ型が呼び出される、*依存 end*の制約。</span><span class="sxs-lookup"><span data-stu-id="23dd4-979">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="23dd4-980">1 つのエンティティ型で公開されている外部キーが別のエンティティ型のプロパティを参照している場合、 **ReferentialConstraint**要素が 2 つのエンティティ型間のアソシエーションを定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-980">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="23dd4-981">**ReferentialConstraint** AssociationSetMapping の対応する要素はマッピング仕様言語 (MSL) では必要ありません、2 つのエンティティ型に関する情報が関連要素を提供します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-981">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="23dd4-982">公開された外部キーがない 2 つのエンティティ型間のアソシエーションの対応する必要があります**AssociationSetMapping**要素の関連付け情報をデータ ソースにマップするためにします。</span><span class="sxs-lookup"><span data-stu-id="23dd4-982">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="23dd4-983">外部キーは、エンティティ型で公開されていない場合、 **ReferentialConstraint**要素は、エンティティ型と別のエンティティ型の間に主キーと主キー制約にのみ定義できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-983">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="23dd4-984">A **ReferentialConstraint**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-984">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-985">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-985">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-986">プリンシパル (正確に 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-986">Principal (exactly one element)</span></span>
-   <span data-ttu-id="23dd4-987">依存 (正確に 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-987">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="23dd4-988">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-988">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-989">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-989">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-990">**ReferentialConstraint**要素は、任意の数の annotation 属性 (カスタム XML 属性) を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-990">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="23dd4-991">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-991">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-992">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-992">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="23dd4-993">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-993">Example</span></span>

<span data-ttu-id="23dd4-994">次の例は、 **ReferentialConstraint**要素の定義の一部として使用されている、 **PublishedBy**アソシエーション。</span><span class="sxs-lookup"><span data-stu-id="23dd4-994">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

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
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="23dd4-995">(関数) の ReturnType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-995">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="23dd4-996">**ReturnType**概念スキーマ定義言語 (CSDL) で (関数) の要素が Function 要素で定義されている関数の戻り値の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-996">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="23dd4-997">関数の戻り値の型を指定することも、 **ReturnType**属性。</span><span class="sxs-lookup"><span data-stu-id="23dd4-997">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="23dd4-998">返す型には、いずれかを指定できる**EdmSimpleType**エンティティ型、複合型、行型、ref 型、またはこれらの型のいずれかのコレクション。</span><span class="sxs-lookup"><span data-stu-id="23dd4-998">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="23dd4-999">いずれかで、関数の戻り値の型を指定することができます、**型**の属性、 **ReturnType** (関数) 要素では、次の子要素のいずれか。</span><span class="sxs-lookup"><span data-stu-id="23dd4-999">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="23dd4-1000">CollectionType</span><span class="sxs-lookup"><span data-stu-id="23dd4-1000">CollectionType</span></span>
-   <span data-ttu-id="23dd4-1001">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="23dd4-1001">ReferenceType</span></span>
-   <span data-ttu-id="23dd4-1002">RowType</span><span class="sxs-lookup"><span data-stu-id="23dd4-1002">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="23dd4-1003">関数の戻り値の両方を持つ型を指定する場合、モデルは検証されません、**型**の属性、 **ReturnType** (関数) 要素と子要素の 1 つ。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1003">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-1004">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-1004">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-1005">次の表に適用できる属性、 **ReturnType** (関数) の要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1005">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="23dd4-1006">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-1006">Attribute Name</span></span> | <span data-ttu-id="23dd4-1007">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-1007">Is Required</span></span> | <span data-ttu-id="23dd4-1008">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1008">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="23dd4-1009">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1009">**ReturnType**</span></span> | <span data-ttu-id="23dd4-1010">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1010">No</span></span>          | <span data-ttu-id="23dd4-1011">関数の戻り値の型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1011">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-1012">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReturnType** (関数) の要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1012">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="23dd4-1013">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1013">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-1014">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1014">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-1015">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-1015">Example</span></span>

<span data-ttu-id="23dd4-1016">次の例では、**関数**書籍が出版されて年数を返す関数を定義する要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1016">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="23dd4-1017">戻り値の型がで指定されたに注意してください、**型**の属性を**ReturnType** (関数) の要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1017">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="23dd4-1018">ReturnType (FunctionImport) 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1018">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="23dd4-1019">**ReturnType**概念スキーマ定義言語 (CSDL) で (FunctionImport) 要素は、FunctionImport 要素で定義されている関数の戻り値の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1019">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="23dd4-1020">関数の戻り値の型を指定することも、 **ReturnType**属性。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1020">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="23dd4-1021">型では、エンティティ型、複合型の任意のコレクションを返すまたは**EdmSimpleType**、</span><span class="sxs-lookup"><span data-stu-id="23dd4-1021">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="23dd4-1022">関数の戻り値の型を指定した、**型**の属性、 **ReturnType** (FunctionImport) 要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1022">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-1023">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-1023">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-1024">次の表に適用できる属性、 **ReturnType** (FunctionImport) 要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1024">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="23dd4-1025">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-1025">Attribute Name</span></span> | <span data-ttu-id="23dd4-1026">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-1026">Is Required</span></span> | <span data-ttu-id="23dd4-1027">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1027">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-1028">**Type**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1028">**Type**</span></span>       | <span data-ttu-id="23dd4-1029">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1029">No</span></span>          | <span data-ttu-id="23dd4-1030">関数が返す型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1030">The type that the function returns.</span></span> <span data-ttu-id="23dd4-1031">値は、ComplexType、EntityType、EDMSimpleType のコレクションである必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1031">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="23dd4-1032">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1032">**EntitySet**</span></span>  | <span data-ttu-id="23dd4-1033">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1033">No</span></span>          | <span data-ttu-id="23dd4-1034">関数は、エンティティのコレクションを返す場合の型の値、 **EntitySet**する必要がありますが、エンティティ セット、コレクションが属する。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1034">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="23dd4-1035">それ以外の場合、 **EntitySet**属性を使用しない必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1035">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-1036">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReturnType** (FunctionImport) 要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1036">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="23dd4-1037">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1037">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-1038">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1038">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-1039">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-1039">Example</span></span>

<span data-ttu-id="23dd4-1040">次の例では、 **FunctionImport**ブックとパブリッシャーを返します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1040">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="23dd4-1041">2 つの結果セットと 2 つのために、関数が返すことに注意してください。 **ReturnType** (FunctionImport) の要素を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1041">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="23dd4-1042">RowType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1042">RowType Element (CSDL)</span></span>

<span data-ttu-id="23dd4-1043">A **RowType**概念スキーマ定義言語 (CSDL) の要素は、パラメーターまたは概念モデルで定義された関数の戻り値の型として無名の構造体を定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1043">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="23dd4-1044">A **RowType**要素は、次の要素の子であることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1044">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="23dd4-1045">CollectionType</span><span class="sxs-lookup"><span data-stu-id="23dd4-1045">CollectionType</span></span>
-   <span data-ttu-id="23dd4-1046">パラメーター</span><span class="sxs-lookup"><span data-stu-id="23dd4-1046">Parameter</span></span>
-   <span data-ttu-id="23dd4-1047">ReturnType (関数)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1047">ReturnType (Function)</span></span>

<span data-ttu-id="23dd4-1048">A **RowType**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1048">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-1049">(1 つまたは複数) のプロパティ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1049">Property (one or more)</span></span>
-   <span data-ttu-id="23dd4-1050">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1050">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-1051">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-1051">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-1052">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **RowType**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1052">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="23dd4-1053">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1053">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-1054">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1054">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="23dd4-1055">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-1055">Example</span></span>

<span data-ttu-id="23dd4-1056">次の例では、使用するモデル定義関数、 **CollectionType**関数によって返される行のコレクションを指定する要素 (で指定されている、 **RowType**要素)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1056">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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

## <a name="schema-element-csdl"></a><span data-ttu-id="23dd4-1057">Schema 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1057">Schema Element (CSDL)</span></span>

<span data-ttu-id="23dd4-1058">**スキーマ**要素は概念モデルの定義のルート要素です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1058">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="23dd4-1059">概念モデルを構成するオブジェクト、関数、およびコンテナーの定義を格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1059">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="23dd4-1060">**スキーマ**要素は、次の子要素の 0 個以上を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1060">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="23dd4-1061">Using</span><span class="sxs-lookup"><span data-stu-id="23dd4-1061">Using</span></span>
-   <span data-ttu-id="23dd4-1062">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="23dd4-1062">EntityContainer</span></span>
-   <span data-ttu-id="23dd4-1063">EntityType</span><span class="sxs-lookup"><span data-stu-id="23dd4-1063">EntityType</span></span>
-   <span data-ttu-id="23dd4-1064">EnumType</span><span class="sxs-lookup"><span data-stu-id="23dd4-1064">EnumType</span></span>
-   <span data-ttu-id="23dd4-1065">関連付け</span><span class="sxs-lookup"><span data-stu-id="23dd4-1065">Association</span></span>
-   <span data-ttu-id="23dd4-1066">ComplexType</span><span class="sxs-lookup"><span data-stu-id="23dd4-1066">ComplexType</span></span>
-   <span data-ttu-id="23dd4-1067">関数</span><span class="sxs-lookup"><span data-stu-id="23dd4-1067">Function</span></span>

<span data-ttu-id="23dd4-1068">A**スキーマ**要素は、0 個または 1 つの Annotation 要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1068">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="23dd4-1069">**関数**要素と annotation 要素はできるの CSDL v2 以降。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1069">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="23dd4-1070">**スキーマ**要素を使用して、 **Namespace**概念モデルのエンティティ型、複合型、およびアソシエーション オブジェクトの名前空間を定義する属性。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1070">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="23dd4-1071">1 つの名前空間内で 2 つのオブジェクトが同じ名前を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1071">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="23dd4-1072">名前空間にわたる複数**スキーマ**要素と複数の .csdl ファイル。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1072">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="23dd4-1073">概念モデル名前空間とは別の XML 名前空間から、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1073">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="23dd4-1074">概念モデル名前空間 (で定義されている、 **Namespace**属性) はエンティティ型、複合型、およびアソシエーション型の論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1074">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="23dd4-1075">XML 名前空間 (によって示される、 **xmlns**属性) の**スキーマ**要素が子要素と属性の既定の名前空間、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1075">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="23dd4-1076">フォームの XML 名前空間 http://schemas.microsoft.com/ado/YYYY/MM/edm (YYYY と MM 表します年と月それぞれ) に CSDL 用に予約されています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1076">XML namespaces of the form http://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="23dd4-1077">カスタム要素と属性は、このフォームがある名前空間に存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1077">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-1078">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-1078">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-1079">次の表の説明属性に適用できる、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1079">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="23dd4-1080">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-1080">Attribute Name</span></span> | <span data-ttu-id="23dd4-1081">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-1081">Is Required</span></span> | <span data-ttu-id="23dd4-1082">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1082">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-1083">**名前空間**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1083">**Namespace**</span></span>  | <span data-ttu-id="23dd4-1084">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1084">Yes</span></span>         | <span data-ttu-id="23dd4-1085">概念モデルの名前空間。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1085">The namespace of the conceptual model.</span></span> <span data-ttu-id="23dd4-1086">値、 **Namespace**属性が型の完全修飾名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1086">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="23dd4-1087">たとえば場合、 **EntityType**という名前の*顧客*Simple.Example.Model 名前空間の完全修飾名では、 **EntityType**はSimpleexamplemodel.customer です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1087">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="23dd4-1088">値として、次の文字列を使用することはできません、 **Namespace**属性:**システム**、**一時的な**、または**Edm**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1088">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="23dd4-1089">値、 **Namespace**属性の値と同じにすることはできません、 **Namespace** SSDL スキーマ要素の属性。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1089">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="23dd4-1090">**Alias**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1090">**Alias**</span></span>      | <span data-ttu-id="23dd4-1091">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1091">No</span></span>          | <span data-ttu-id="23dd4-1092">名前空間の名前の代わりに使用される識別子。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1092">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="23dd4-1093">たとえば場合、 **EntityType**という名前*顧客*Simple.Example.Model 名前空間にありの値は、**エイリアス**属性が*モデル*、Model.Customer の完全修飾名として使用することができます、 **EntityType。**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1093">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="23dd4-1094">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1094">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="23dd4-1095">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1095">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-1096">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1096">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-1097">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-1097">Example</span></span>

<span data-ttu-id="23dd4-1098">次の例は、**スキーマ**要素を含む、 **EntityContainer**要素、2 つ**EntityType**要素、および 1 つ**の関連付け**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1098">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="23dd4-1099">TypeRef 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1099">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="23dd4-1100">**TypeRef**概念スキーマ定義言語 (CSDL) 内の要素は、既存の名前付きの型への参照を提供します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1100">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="23dd4-1101">**TypeRef**要素が関数にパラメーターまたは戻り値の型としてのコレクションがあることを指定するために使用 CollectionType 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1101">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="23dd4-1102">A **TypeRef**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1102">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="23dd4-1103">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="23dd4-1103">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="23dd4-1104">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1104">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-1105">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-1105">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-1106">次の表に適用できる属性、 **TypeRef**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1106">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="23dd4-1107">なお、 **DefaultValue**、 **MaxLength**、 **FixedLength**、**精度**、**スケール**、 **Unicode**、および**照合**属性にのみ適用されます**EDMSimpleTypes**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1107">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="23dd4-1108">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-1108">Attribute Name</span></span>                                                     | <span data-ttu-id="23dd4-1109">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-1109">Is Required</span></span> | <span data-ttu-id="23dd4-1110">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1110">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-1111">**Type**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1111">**Type**</span></span>                                                           | <span data-ttu-id="23dd4-1112">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1112">No</span></span>          | <span data-ttu-id="23dd4-1113">参照先の型の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1113">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="23dd4-1114">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1114">**Nullable**</span></span>                                                       | <span data-ttu-id="23dd4-1115">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1115">No</span></span>          | <span data-ttu-id="23dd4-1116">**True** (既定値) または**False**プロパティが null 値を持っているかどうかによって。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1116">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="23dd4-1117">> 複合型プロパティがありますで CSDL v1`Nullable="False"`します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1117">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="23dd4-1118">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1118">**DefaultValue**</span></span>                                                   | <span data-ttu-id="23dd4-1119">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1119">No</span></span>          | <span data-ttu-id="23dd4-1120">プロパティの既定値。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1120">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="23dd4-1121">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1121">**MaxLength**</span></span>                                                      | <span data-ttu-id="23dd4-1122">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1122">No</span></span>          | <span data-ttu-id="23dd4-1123">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1123">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="23dd4-1124">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1124">**FixedLength**</span></span>                                                    | <span data-ttu-id="23dd4-1125">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1125">No</span></span>          | <span data-ttu-id="23dd4-1126">**True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1126">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="23dd4-1127">**精度**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1127">**Precision**</span></span>                                                      | <span data-ttu-id="23dd4-1128">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1128">No</span></span>          | <span data-ttu-id="23dd4-1129">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1129">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="23dd4-1130">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1130">**Scale**</span></span>                                                          | <span data-ttu-id="23dd4-1131">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1131">No</span></span>          | <span data-ttu-id="23dd4-1132">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1132">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="23dd4-1133">**SRID**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1133">**SRID**</span></span>                                                           | <span data-ttu-id="23dd4-1134">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1134">No</span></span>          | <span data-ttu-id="23dd4-1135">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1135">Spatial System Reference Identifier.</span></span> <span data-ttu-id="23dd4-1136">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1136">Valid only for properties of spatial types.</span></span> <span data-ttu-id="23dd4-1137">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1137">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="23dd4-1138">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1138">**Unicode**</span></span>                                                        | <span data-ttu-id="23dd4-1139">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1139">No</span></span>          | <span data-ttu-id="23dd4-1140">**True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1140">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="23dd4-1141">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1141">**Collation**</span></span>                                                      | <span data-ttu-id="23dd4-1142">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1142">No</span></span>          | <span data-ttu-id="23dd4-1143">データ ソースで使用する照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1143">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="23dd4-1144">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **CollectionType**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1144">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="23dd4-1145">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1145">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-1146">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1146">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-1147">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-1147">Example</span></span>

<span data-ttu-id="23dd4-1148">次の例では、使用するモデル定義関数、 **TypeRef**要素 (の子として、 **CollectionType**要素) のコレクションを関数が受け取ることを指定する**部門**エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1148">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

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
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="23dd4-1149">Using 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1149">Using Element (CSDL)</span></span>

<span data-ttu-id="23dd4-1150">**Using**概念スキーマ定義言語 (CSDL) 内の要素が別の名前空間に存在する概念モデルのコンテンツをインポートします。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1150">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="23dd4-1151">値を設定して、 **Namespace**属性、エンティティ型、複合型、および別の概念モデルで定義されているアソシエーション型を参照することができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1151">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="23dd4-1152">1 つ以上**Using**要素の子にすることができます、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1152">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="23dd4-1153">**Using** CSDL 内の要素と同様に機能しません、**を使用して**のプログラミング言語でステートメント。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1153">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="23dd4-1154">持つ名前空間をインポートすることによって、**を使用して**ステートメントで、プログラミング言語をするには影響しません、元の名前空間内のオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1154">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="23dd4-1155">CSDL では、インポートされた名前空間に、元の名前空間にあるエンティティ型から派生したエンティティ型が含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1155">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="23dd4-1156">これは、元の名前空間で宣言されたエンティティ セットに影響を及ぼすことがあります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1156">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="23dd4-1157">**Using**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1157">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="23dd4-1158">ドキュメント (0 個または 1 つの要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1158">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="23dd4-1159">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1159">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="23dd4-1160">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="23dd4-1160">Applicable Attributes</span></span>

<span data-ttu-id="23dd4-1161">次の表は、属性を示しますに適用できる、 **Using**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1161">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="23dd4-1162">属性名</span><span class="sxs-lookup"><span data-stu-id="23dd4-1162">Attribute Name</span></span> | <span data-ttu-id="23dd4-1163">必須</span><span class="sxs-lookup"><span data-stu-id="23dd4-1163">Is Required</span></span> | <span data-ttu-id="23dd4-1164">[値]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1164">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-1165">**名前空間**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1165">**Namespace**</span></span>  | <span data-ttu-id="23dd4-1166">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1166">Yes</span></span>         | <span data-ttu-id="23dd4-1167">インポートされる名前空間の名前。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1167">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="23dd4-1168">**Alias**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1168">**Alias**</span></span>      | <span data-ttu-id="23dd4-1169">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1169">Yes</span></span>         | <span data-ttu-id="23dd4-1170">名前空間の名前の代わりに使用される識別子。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1170">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="23dd4-1171">この属性は必須ですが、オブジェクト名を修飾するために名前空間名の代わりに使用することは必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1171">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="23dd4-1172">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **Using**要素。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1172">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="23dd4-1173">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="23dd4-1174">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="23dd4-1175">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-1175">Example</span></span>

<span data-ttu-id="23dd4-1176">次の例で、 **Using**は名前空間をインポートするために使用されている要素では、別の場所で定義されています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1176">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="23dd4-1177">注意名前空間を**スキーマ**に示す要素が`BooksModel`します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1177">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="23dd4-1178">`Address`プロパティを`Publisher` **EntityType**複合型で定義されているは、`ExtendedBooksModel`名前空間 (と共にインポート、 **Using**要素)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1178">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

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
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="23dd4-1179">annotation 属性 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1179">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="23dd4-1180">概念スキーマ定義言語 (CSDL) の annotation 属性は、概念モデルのカスタム XML 属性です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1180">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="23dd4-1181">有効な XML 構造であることに加え、annotation 属性は次の条件を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1181">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="23dd4-1182">annotation 属性は、CSDL 用に予約された XML 名前空間に存在しない。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1182">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="23dd4-1183">複数の annotation 属性を特定の 1 つの CSDL 要素に適用することができる。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1183">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="23dd4-1184">2 つの任意の annotation 属性の完全修飾名が同じではない。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1184">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="23dd4-1185">annotation 属性は、概念モデルの要素に関する追加のメタデータを提供するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1185">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="23dd4-1186">Annotation 要素に含まれるメタデータは、System.Data.Metadata.Edm 名前空間のクラスを使用して、実行時にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1186">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="23dd4-1187">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-1187">Example</span></span>

<span data-ttu-id="23dd4-1188">次の例は、 **EntityType** annotation 属性を持つ要素 (**CustomAttribute**)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1188">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="23dd4-1189">また、エンティティ型の要素に適用される annotation 要素も示しています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1189">The example also shows an annotation element applied to the entity type element.</span></span>

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
 

<span data-ttu-id="23dd4-1190">次のコードは、annotation 属性でメタデータを取得し、コンソールに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1190">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

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
 

<span data-ttu-id="23dd4-1191">前のコードでは、`School.csdl` ファイルがプロジェクトの出力ディレクトリにあり、プロジェクトに次の `Imports` および `Using` ステートメントが追加されていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1191">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="23dd4-1192">Annotation 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1192">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="23dd4-1193">概念スキーマ定義言語 (CSDL) の annotation 要素は、概念モデルのカスタム XML 要素です。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1193">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="23dd4-1194">有効な XML 構造が必要であることに加え、annotation 要素は次の条件も満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1194">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="23dd4-1195">annotation 要素は、CSDL 用に予約された XML 名前空間内に存在できません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1195">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="23dd4-1196">複数の annotation 要素を特定の CSDL 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1196">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="23dd4-1197">2 つの annotation 要素の完全修飾名を同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1197">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="23dd4-1198">annotation 要素は、特定の CSDL 要素のその他すべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1198">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="23dd4-1199">annotation 要素は、概念モデルの要素に関する追加のメタデータを提供するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1199">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="23dd4-1200">以降、.NET Framework version 4 では、annotation 要素に含まれるメタデータにアクセスできるランタイム System.Data.Metadata.Edm 名前空間のクラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1200">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="23dd4-1201">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-1201">Example</span></span>

<span data-ttu-id="23dd4-1202">次の例は、 **EntityType** annotation 要素を持つ要素 (**CustomElement**)。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1202">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="23dd4-1203">また、エンティティ型の要素に適用される annotation 属性も示しています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1203">The example also show an annotation attribute applied to the entity type element.</span></span>

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
 

<span data-ttu-id="23dd4-1204">次のコードは、annotation 要素内のメタデータを取得してコンソールに出力します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1204">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

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
 

<span data-ttu-id="23dd4-1205">前のコードでは、School.csdl ファイルがプロジェクトの出力ディレクトリにあり、プロジェクトに次の `Imports` および `Using` ステートメントが追加されていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1205">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="23dd4-1206">概念モデルの型 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1206">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="23dd4-1207">概念スキーマ定義言語 (CSDL) と呼ばれる抽象プリミティブ データ型のセットがサポートする**EDMSimpleTypes**、概念モデルのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1207">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="23dd4-1208">**EDMSimpleTypes**のストレージ環境またはホスティング環境でサポートされるプリミティブ データ型のプロキシします。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1208">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="23dd4-1209">下の表は、CSDL でサポートされるプリミティブ データ型の一覧を示します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1209">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="23dd4-1210">テーブルは、それぞれに適用できるファセットも一覧表示されます。 **EDMSimpleType**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1210">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="23dd4-1211">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="23dd4-1211">EDMSimpleType</span></span>                    | <span data-ttu-id="23dd4-1212">説明</span><span class="sxs-lookup"><span data-stu-id="23dd4-1212">Description</span></span>                                                | <span data-ttu-id="23dd4-1213">使用できるファセット</span><span class="sxs-lookup"><span data-stu-id="23dd4-1213">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="23dd4-1214">**Edm.binary です。**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1214">**Edm.Binary**</span></span>                   | <span data-ttu-id="23dd4-1215">バイナリ データを格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1215">Contains binary data.</span></span>                                      | <span data-ttu-id="23dd4-1216">MaxLength、FixedLength、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1216">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="23dd4-1217">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1217">**Edm.Boolean**</span></span>                  | <span data-ttu-id="23dd4-1218">値を含む**true**または**false**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1218">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="23dd4-1219">Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1219">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="23dd4-1220">**Edm.Byte**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1220">**Edm.Byte**</span></span>                     | <span data-ttu-id="23dd4-1221">符号なし 8 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1221">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="23dd4-1222">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1222">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="23dd4-1223">**Edm.DateTime**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1223">**Edm.DateTime**</span></span>                 | <span data-ttu-id="23dd4-1224">日時を表します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1224">Represents a date and time.</span></span>                                | <span data-ttu-id="23dd4-1225">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1225">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="23dd4-1226">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1226">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="23dd4-1227">GMT からのオフセット (分単位) としての日時を格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1227">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="23dd4-1228">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1228">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="23dd4-1229">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1229">**Edm.Decimal**</span></span>                  | <span data-ttu-id="23dd4-1230">有効桁数と小数点以下桁数が固定長の数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1230">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="23dd4-1231">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1231">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="23dd4-1232">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1232">**Edm.Double**</span></span>                   | <span data-ttu-id="23dd4-1233">持つ浮動小数点数 15 桁の有効桁数が含まれています</span><span class="sxs-lookup"><span data-stu-id="23dd4-1233">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="23dd4-1234">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1234">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="23dd4-1235">**Edm.Float**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1235">**Edm.Float**</span></span>                    | <span data-ttu-id="23dd4-1236">7 桁の有効桁数を持つ浮動小数点数を格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1236">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="23dd4-1237">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1237">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="23dd4-1238">**Edm.Guid**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1238">**Edm.Guid**</span></span>                     | <span data-ttu-id="23dd4-1239">16 バイトの一意識別子を格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1239">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="23dd4-1240">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1240">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="23dd4-1241">**Edm.Int16**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1241">**Edm.Int16**</span></span>                    | <span data-ttu-id="23dd4-1242">符号付き 16 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1242">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="23dd4-1243">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1243">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="23dd4-1244">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1244">**Edm.Int32**</span></span>                    | <span data-ttu-id="23dd4-1245">符号付き 32 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1245">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="23dd4-1246">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1246">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="23dd4-1247">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1247">**Edm.Int64**</span></span>                    | <span data-ttu-id="23dd4-1248">符号付き 64 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1248">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="23dd4-1249">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1249">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="23dd4-1250">**Edm.SByte**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1250">**Edm.SByte**</span></span>                    | <span data-ttu-id="23dd4-1251">符号付き 8 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1251">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="23dd4-1252">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1252">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="23dd4-1253">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1253">**Edm.String**</span></span>                   | <span data-ttu-id="23dd4-1254">文字データを格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1254">Contains character data.</span></span>                                   | <span data-ttu-id="23dd4-1255">Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1255">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="23dd4-1256">**Edm.time です。**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1256">**Edm.Time**</span></span>                     | <span data-ttu-id="23dd4-1257">時刻を格納します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1257">Contains a time of day.</span></span>                                    | <span data-ttu-id="23dd4-1258">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="23dd4-1258">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="23dd4-1259">**Edm.Geography**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1259">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="23dd4-1260">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1260">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1261">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1261">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="23dd4-1262">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1262">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1263">**Edm.GeographyLineString**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1263">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="23dd4-1264">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1264">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1265">**Edm.GeographyPolygon**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1265">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="23dd4-1266">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1266">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1267">**Edm.GeographyMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1267">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="23dd4-1268">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1268">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1269">**Edm.GeographyMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1269">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="23dd4-1270">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1270">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1271">**Edm.GeographyMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1271">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="23dd4-1272">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1272">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1273">**Edm.GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1273">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="23dd4-1274">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1274">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1275">**Edm.Geometry**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1275">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="23dd4-1276">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1276">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1277">**Edm.GeometryPoint**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1277">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="23dd4-1278">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1278">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1279">**Edm.GeometryLineString**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1279">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="23dd4-1280">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1280">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1281">**Edm.GeometryPolygon**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1281">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="23dd4-1282">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1282">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1283">**Edm.GeometryMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1283">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="23dd4-1284">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1284">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1285">**Edm.GeometryMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1285">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="23dd4-1286">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1286">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1287">**Edm.GeometryMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1287">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="23dd4-1288">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1288">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="23dd4-1289">**Edm.GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1289">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="23dd4-1290">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="23dd4-1290">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="23dd4-1291">ファセット (CSDL)</span><span class="sxs-lookup"><span data-stu-id="23dd4-1291">Facets (CSDL)</span></span>

<span data-ttu-id="23dd4-1292">概念スキーマ定義言語 (CSDL) のファセットは、エンティティ型と複合型のプロパティに対する制約を表します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1292">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="23dd4-1293">ファセットは次の CSDL 要素で XML 属性として使用されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1293">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="23dd4-1294">プロパティ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1294">Property</span></span>
-   <span data-ttu-id="23dd4-1295">TypeRef</span><span class="sxs-lookup"><span data-stu-id="23dd4-1295">TypeRef</span></span>
-   <span data-ttu-id="23dd4-1296">パラメーター</span><span class="sxs-lookup"><span data-stu-id="23dd4-1296">Parameter</span></span>

<span data-ttu-id="23dd4-1297">次の表は、CSDL でサポートされるファセットについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1297">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="23dd4-1298">いずれのファセットもオプションです。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1298">All facets are optional.</span></span> <span data-ttu-id="23dd4-1299">次に示すいくつかのファセットは、概念モデルからデータベースを生成するときに、Entity Framework によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1299">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="23dd4-1300">概念モデルのデータ型については、概念モデルの型 (CSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1300">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="23dd4-1301">ファセット</span><span class="sxs-lookup"><span data-stu-id="23dd4-1301">Facet</span></span>               | <span data-ttu-id="23dd4-1302">説明</span><span class="sxs-lookup"><span data-stu-id="23dd4-1302">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="23dd4-1303">対象</span><span class="sxs-lookup"><span data-stu-id="23dd4-1303">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="23dd4-1304">データベースの生成に使用</span><span class="sxs-lookup"><span data-stu-id="23dd4-1304">Used for the database generation</span></span> | <span data-ttu-id="23dd4-1305">ランタイムで使用</span><span class="sxs-lookup"><span data-stu-id="23dd4-1305">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="23dd4-1306">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1306">**Collation**</span></span>       | <span data-ttu-id="23dd4-1307">プロパティの値に対して比較と順序付け操作を行うときに使用する照合シーケンス (または並べ替え順序) を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1307">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="23dd4-1308">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1308">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="23dd4-1309">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1309">Yes</span></span>                              | <span data-ttu-id="23dd4-1310">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1310">No</span></span>                  |
| <span data-ttu-id="23dd4-1311">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1311">**ConcurrencyMode**</span></span> | <span data-ttu-id="23dd4-1312">プロパティの値をオプティミスティック同時実行制御チェックに使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1312">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="23dd4-1313">すべて**EDMSimpleType**プロパティ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1313">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="23dd4-1314">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1314">No</span></span>                               | <span data-ttu-id="23dd4-1315">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1315">Yes</span></span>                 |
| <span data-ttu-id="23dd4-1316">**既定値**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1316">**Default**</span></span>         | <span data-ttu-id="23dd4-1317">インスタンス化で値が指定されない場合のプロパティの既定値を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1317">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="23dd4-1318">すべて**EDMSimpleType**プロパティ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1318">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="23dd4-1319">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1319">Yes</span></span>                              | <span data-ttu-id="23dd4-1320">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1320">Yes</span></span>                 |
| <span data-ttu-id="23dd4-1321">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1321">**FixedLength**</span></span>     | <span data-ttu-id="23dd4-1322">プロパティ値の長さを可変とすることができるかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1322">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="23dd4-1323">**Edm.Binary**、 **Edm.String**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1323">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="23dd4-1324">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1324">Yes</span></span>                              | <span data-ttu-id="23dd4-1325">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1325">No</span></span>                  |
| <span data-ttu-id="23dd4-1326">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1326">**MaxLength**</span></span>       | <span data-ttu-id="23dd4-1327">プロパティ値の最大長を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1327">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="23dd4-1328">**Edm.Binary**、 **Edm.String**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1328">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="23dd4-1329">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1329">Yes</span></span>                              | <span data-ttu-id="23dd4-1330">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1330">No</span></span>                  |
| <span data-ttu-id="23dd4-1331">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1331">**Nullable**</span></span>        | <span data-ttu-id="23dd4-1332">プロパティを設定できるかどうかを指定する**null**値。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1332">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="23dd4-1333">すべて**EDMSimpleType**プロパティ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1333">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="23dd4-1334">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1334">Yes</span></span>                              | <span data-ttu-id="23dd4-1335">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1335">Yes</span></span>                 |
| <span data-ttu-id="23dd4-1336">**精度**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1336">**Precision**</span></span>       | <span data-ttu-id="23dd4-1337">型のプロパティの**Decimal**桁に指定できるプロパティの値の数を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1337">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="23dd4-1338">型のプロパティの**時間**、 **DateTime**、および**DateTimeOffset**プロパティ値の秒の小数部の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1338">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="23dd4-1339">**Edm.DateTime**、 **Edm.DateTimeOffset**、 **Edm.Decimal**、 **edm.time です**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1339">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="23dd4-1340">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1340">Yes</span></span>                              | <span data-ttu-id="23dd4-1341">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1341">No</span></span>                  |
| <span data-ttu-id="23dd4-1342">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1342">**Scale**</span></span>           | <span data-ttu-id="23dd4-1343">プロパティ値の小数点の右側の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1343">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="23dd4-1344">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1344">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="23dd4-1345">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1345">Yes</span></span>                              | <span data-ttu-id="23dd4-1346">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1346">No</span></span>                  |
| <span data-ttu-id="23dd4-1347">**SRID**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1347">**SRID**</span></span>            | <span data-ttu-id="23dd4-1348">システムの空間参照システムの ID を指定します</span><span class="sxs-lookup"><span data-stu-id="23dd4-1348">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="23dd4-1349">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1349">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="23dd4-1350">**Edm.Geography、Edm.GeographyPoint、Edm.GeographyLineString、Edm.GeographyPolygon、Edm.GeographyMultiPoint、Edm.GeographyMultiLineString、Edm.GeographyMultiPolygon、Edm.GeographyCollection、Edm.Geometry、Edm.GeometryPoint、Edm.GeometryLineString、Edm.GeometryPolygon、Edm.GeometryMultiPoint、Edm.GeometryMultiLineString、Edm.GeometryMultiPolygon、Edm.GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1350">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="23dd4-1351">いいえ</span><span class="sxs-lookup"><span data-stu-id="23dd4-1351">No</span></span>                               | <span data-ttu-id="23dd4-1352">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1352">Yes</span></span>                 |
| <span data-ttu-id="23dd4-1353">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1353">**Unicode**</span></span>         | <span data-ttu-id="23dd4-1354">プロパティ値を Unicode として保存するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1354">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="23dd4-1355">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="23dd4-1355">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="23dd4-1356">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1356">Yes</span></span>                              | <span data-ttu-id="23dd4-1357">[はい]</span><span class="sxs-lookup"><span data-stu-id="23dd4-1357">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="23dd4-1358">データベース生成ウィザードがの値を認識する概念モデルからデータベースを生成するときに、 **StoreGeneratedPattern**属性を**プロパティ**場合は、次の要素名前空間: http://schemas.microsoft.com/ado/2009/02/edm/annotation します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1358">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: http://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="23dd4-1359">属性のサポートされる値は**Identity**と**計算済み**します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1359">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="23dd4-1360">値**Identity**データベースで生成される id 値を持つデータベース列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1360">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="23dd4-1361">値**計算済み**データベースで計算される値を持つ列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1361">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="23dd4-1362">例</span><span class="sxs-lookup"><span data-stu-id="23dd4-1362">Example</span></span>

<span data-ttu-id="23dd4-1363">エンティティ型のプロパティに適用されるファセットの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="23dd4-1363">The following example shows facets applied to the properties of an entity type:</span></span>

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
