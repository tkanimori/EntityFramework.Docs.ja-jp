---
title: CSDL の仕様の EF6
author: divega
ms.date: 10/23/2016
ms.assetid: c54255f4-253f-49eb-bec8-ad7927ac2fa3
ms.openlocfilehash: 438af83b8a1ad51ee8414341181412e950d0e117
ms.sourcegitcommit: 29f928a6116771fe78f306846e6f2d45cbe8d1f4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460151"
---
# <a name="csdl-specification"></a><span data-ttu-id="6b89e-102">CSDL 仕様</span><span class="sxs-lookup"><span data-stu-id="6b89e-102">CSDL Specification</span></span>
<span data-ttu-id="6b89e-103">概念スキーマ定義言語 (CSDL : Conceptual Schema Definition Language) は、XML ベースの言語であり、データ駆動型アプリケーションの概念モデルを構成するエンティティ、リレーションシップ、および関数を記述します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-103">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="6b89e-104">Entity Framework や WCF Data Services では、この概念モデルを使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-104">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="6b89e-105">CSDL で記述されるメタデータは、エンティティおよびデータ ソースに概念モデルで定義されているリレーションシップにマップする Entity Framework によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-105">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="6b89e-106">詳細については、次を参照してください。 [SSDL 仕様](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md)と[MSL 仕様](~/ef6/modeling/designer/advanced/edmx/msl-spec.md)します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-106">For more information, see [SSDL Specification](~/ef6/modeling/designer/advanced/edmx/ssdl-spec.md) and [MSL Specification](~/ef6/modeling/designer/advanced/edmx/msl-spec.md).</span></span>

<span data-ttu-id="6b89e-107">CSDL は、Entity Data Model の Entity Framework の実装です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-107">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="6b89e-108">Entity Framework アプリケーションで概念モデルのメタデータが読み込まれた .csdl ファイル (CSDL で記述) から、System.Data.Metadata.Edm.EdmItemCollection のインスタンスに、アクセスできるメソッドを使用して、System.Data.Metadata.Edm.MetadataWorkspace クラスです。</span><span class="sxs-lookup"><span data-stu-id="6b89e-108">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="6b89e-109">Entity Framework では、概念モデルのメタデータを使用してデータ ソース固有のコマンドを概念モデルに対するクエリに変換します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-109">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="6b89e-110">EF Designer では、デザイン時に .edmx ファイルの概念モデル情報を格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-110">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="6b89e-111">ビルド時に、EF デザイナー情報を使用して .edmx ファイルの実行時に Entity Framework によって必要な .csdl ファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-111">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="6b89e-112">CSDL のバージョンは、XML 名前空間で区別されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-112">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="6b89e-113">CSDL のバージョン</span><span class="sxs-lookup"><span data-stu-id="6b89e-113">CSDL Version</span></span> | <span data-ttu-id="6b89e-114">XML Namespace</span><span class="sxs-lookup"><span data-stu-id="6b89e-114">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="6b89e-115">CSDL v1</span><span class="sxs-lookup"><span data-stu-id="6b89e-115">CSDL v1</span></span>      | http://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="6b89e-116">CSDL v2</span><span class="sxs-lookup"><span data-stu-id="6b89e-116">CSDL v2</span></span>      | http://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="6b89e-117">CSDL v3</span><span class="sxs-lookup"><span data-stu-id="6b89e-117">CSDL v3</span></span>      | http://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="6b89e-118">Association 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-118">Association Element (CSDL)</span></span>

<span data-ttu-id="6b89e-119">**アソシエーション**要素が 2 つのエンティティ型間のリレーションシップを定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-119">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="6b89e-120">アソシエーションでは、リレーションシップに関連するエンティティ型、およびリレーションシップの各 End におけるエンティティ型の数 (多重度と呼ばれる) を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-120">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="6b89e-121">値の 1 つ (1)、0 個または 1 (0..1)、または複数のアソシエーション end の多重度を持つことができます (\*)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-121">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="6b89e-122">この情報は、2 つの子 End 要素で指定されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-122">This information is specified in two child End elements.</span></span>

<span data-ttu-id="6b89e-123">アソシエーションの一方の End にあるエンティティ型のインスタンスには、それらがエンティティ型で公開されている場合、ナビゲーション プロパティまたは外部キーからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-123">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="6b89e-124">アプリケーション内で、アソシエーションのインスタンスは、エンティティ型のインスタンスの間の特定のアソシエーションを表します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-124">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="6b89e-125">アソシエーション インスタンスは、アソシエーション セットに論理的にグループ化されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-125">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="6b89e-126">**アソシエーション**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-126">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-127">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-127">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-128">終了 (正確に 2 つの要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-128">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="6b89e-129">ReferentialConstraint (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-129">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-130">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-130">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-131">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-131">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-132">次の表に適用できる属性の説明、**アソシエーション**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-132">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="6b89e-133">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-133">Attribute Name</span></span> | <span data-ttu-id="6b89e-134">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-134">Is Required</span></span> | <span data-ttu-id="6b89e-135">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-135">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="6b89e-136">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-136">**Name**</span></span>       | <span data-ttu-id="6b89e-137">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-137">Yes</span></span>         | <span data-ttu-id="6b89e-138">アソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-138">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-139">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**アソシエーション**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-139">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="6b89e-140">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-140">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-141">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-141">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-142">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-142">Example</span></span>

<span data-ttu-id="6b89e-143">次の例は、**アソシエーション**を定義する要素、 **CustomerOrders**関連付けの外部キーが公開されていないときに、**顧客**と**順序**エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-143">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="6b89e-144">**多重度**値はそれぞれ**エンド**の関連付けを示す多く**注文**と関連付けることができます、**顧客**が、1 つだけ**顧客**関連付けることができる、**順序**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-144">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="6b89e-145">さらに、 **OnDelete**要素には、ことを示しますすべて**注文**に関連する特定の**顧客**に読み込まれ、ObjectContext は削除されます場合、**顧客**は削除されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-145">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="6b89e-146">次の例は、**アソシエーション**を定義する要素、 **CustomerOrders**アソシエーションで外部キーが公開されているときに、**顧客**と**順序**エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-146">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="6b89e-147">公開されている外部キーを持つ、エンティティ間のリレーションシップが管理されている、 **ReferentialConstraint**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-147">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="6b89e-148">対応する AssociationSetMapping 要素は、このアソシエーションをデータ ソースにマップする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-148">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

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
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="6b89e-149">AssociationSet 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-149">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="6b89e-150">**AssociationSet**概念スキーマ定義言語 (CSDL) で要素は、同じ型のアソシエーション インスタンス用の論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="6b89e-150">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="6b89e-151">アソシエーション セットは、複数のアソシエーション インスタンスを 1 つのデータ ソースにマップできるようグループ化する方法を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-151">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="6b89e-152">**AssociationSet**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-152">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-153">ドキュメント (0 個または 1 つの要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-153">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="6b89e-154">終了 (2 つの要素が必要な場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-154">End (exactly two elements required)</span></span>
-   <span data-ttu-id="6b89e-155">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-155">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="6b89e-156">**アソシエーション**属性は、アソシエーション セットに含まれる関連付けの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-156">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="6b89e-157">アソシエーション セットの両端を構成するエンティティ セットが正確に 2 つの子で指定された**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-157">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-158">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-158">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-159">次の表に適用できる属性の説明、 **AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-159">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="6b89e-160">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-160">Attribute Name</span></span>  | <span data-ttu-id="6b89e-161">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-161">Is Required</span></span> | <span data-ttu-id="6b89e-162">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-162">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-163">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-163">**Name**</span></span>        | <span data-ttu-id="6b89e-164">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-164">Yes</span></span>         | <span data-ttu-id="6b89e-165">エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-165">The name of the entity set.</span></span> <span data-ttu-id="6b89e-166">値、**名前**属性の値と同じにすることはできません、**アソシエーション**属性。</span><span class="sxs-lookup"><span data-stu-id="6b89e-166">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="6b89e-167">**関連付け**</span><span class="sxs-lookup"><span data-stu-id="6b89e-167">**Association**</span></span> | <span data-ttu-id="6b89e-168">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-168">Yes</span></span>         | <span data-ttu-id="6b89e-169">関連付けの完全修飾名。そのインスタンスは、アソシエーション セットに格納されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-169">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="6b89e-170">関連付けは、アソシエーション セットと同じ名前空間に存在する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-170">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-171">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-171">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="6b89e-172">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-172">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-173">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-173">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-174">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-174">Example</span></span>

<span data-ttu-id="6b89e-175">次の例は、 **EntityContainer**要素を持つ**AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-175">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

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
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="6b89e-176">CollectionType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-176">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="6b89e-177">**CollectionType**概念スキーマ定義言語 (CSDL) 内の要素のことを関数パラメーターまたは関数の戻り型がコレクションを指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-177">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="6b89e-178">**CollectionType**要素 (関数) の ReturnType 要素またはパラメーターの要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-178">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="6b89e-179">いずれかを使用してコレクションの型を指定することができます、**型**属性または子要素を次のいずれか。</span><span class="sxs-lookup"><span data-stu-id="6b89e-179">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="6b89e-180">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="6b89e-180">**CollectionType**</span></span>
-   <span data-ttu-id="6b89e-181">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="6b89e-181">ReferenceType</span></span>
-   <span data-ttu-id="6b89e-182">RowType</span><span class="sxs-lookup"><span data-stu-id="6b89e-182">RowType</span></span>
-   <span data-ttu-id="6b89e-183">TypeRef</span><span class="sxs-lookup"><span data-stu-id="6b89e-183">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="6b89e-184">両方のコレクションの型が指定されている場合、モデルは検証されません、**型**属性と子要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-184">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-185">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-185">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-186">次の表に適用できる属性、 **CollectionType**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-186">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="6b89e-187">なお、 **DefaultValue**、 **MaxLength**、 **FixedLength**、**精度**、**スケール**、 **Unicode**、および**照合順序**のコレクションに適用可能な属性は**EDMSimpleTypes**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-187">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="6b89e-188">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-188">Attribute Name</span></span>                                                          | <span data-ttu-id="6b89e-189">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-189">Is Required</span></span> | <span data-ttu-id="6b89e-190">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-190">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-191">**Type**</span><span class="sxs-lookup"><span data-stu-id="6b89e-191">**Type**</span></span>                                                                | <span data-ttu-id="6b89e-192">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-192">No</span></span>          | <span data-ttu-id="6b89e-193">コレクションの型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-193">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="6b89e-194">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="6b89e-194">**Nullable**</span></span>                                                            | <span data-ttu-id="6b89e-195">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-195">No</span></span>          | <span data-ttu-id="6b89e-196">**True** (既定値) または**False**プロパティが null 値を持っているかどうかによって。</span><span class="sxs-lookup"><span data-stu-id="6b89e-196">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="6b89e-197">> 複合型プロパティがありますで CSDL v1`Nullable="False"`します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-197">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="6b89e-198">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="6b89e-198">**DefaultValue**</span></span>                                                        | <span data-ttu-id="6b89e-199">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-199">No</span></span>          | <span data-ttu-id="6b89e-200">プロパティの既定値。</span><span class="sxs-lookup"><span data-stu-id="6b89e-200">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="6b89e-201">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-201">**MaxLength**</span></span>                                                           | <span data-ttu-id="6b89e-202">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-202">No</span></span>          | <span data-ttu-id="6b89e-203">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="6b89e-203">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="6b89e-204">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-204">**FixedLength**</span></span>                                                         | <span data-ttu-id="6b89e-205">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-205">No</span></span>          | <span data-ttu-id="6b89e-206">**True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-206">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="6b89e-207">**精度**</span><span class="sxs-lookup"><span data-stu-id="6b89e-207">**Precision**</span></span>                                                           | <span data-ttu-id="6b89e-208">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-208">No</span></span>          | <span data-ttu-id="6b89e-209">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="6b89e-209">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="6b89e-210">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="6b89e-210">**Scale**</span></span>                                                               | <span data-ttu-id="6b89e-211">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-211">No</span></span>          | <span data-ttu-id="6b89e-212">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="6b89e-212">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="6b89e-213">**SRID**</span><span class="sxs-lookup"><span data-stu-id="6b89e-213">**SRID**</span></span>                                                                | <span data-ttu-id="6b89e-214">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-214">No</span></span>          | <span data-ttu-id="6b89e-215">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-215">Spatial System Reference Identifier.</span></span> <span data-ttu-id="6b89e-216">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-216">Valid only for properties of spatial types.</span></span>   <span data-ttu-id="6b89e-217">詳細については、次を参照してください [SRID](http://en.wikipedia.org/wiki/SRID) と[SRID (SQL Server) 。](https://msdn.microsoft.com/library/bb964707.aspx)</span><span class="sxs-lookup"><span data-stu-id="6b89e-217">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="6b89e-218">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="6b89e-218">**Unicode**</span></span>                                                             | <span data-ttu-id="6b89e-219">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-219">No</span></span>          | <span data-ttu-id="6b89e-220">**True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-220">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="6b89e-221">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="6b89e-221">**Collation**</span></span>                                                           | <span data-ttu-id="6b89e-222">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-222">No</span></span>          | <span data-ttu-id="6b89e-223">データ ソースで使用する照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="6b89e-223">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="6b89e-224">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **CollectionType**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-224">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="6b89e-225">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-225">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-226">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-226">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-227">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-227">Example</span></span>

<span data-ttu-id="6b89e-228">次の例では、モデル定義の関数を使用して、 **CollectionType**関数でのコレクションを返すことを指定する要素**Person**エンティティ型 (、に指定された**ElementType**属性)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-228">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

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
 

<span data-ttu-id="6b89e-229">次の例では、使用するモデル定義関数、 **CollectionType**関数によって返される行のコレクションを指定する要素 (で指定されている、 **RowType**要素)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-229">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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
 

<span data-ttu-id="6b89e-230">次の例では、使用するモデル定義関数、 **CollectionType**要素のコレクションのパラメーターとして、関数が受け入れる指定を**部門**エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-230">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

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
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="6b89e-231">ComplexType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-231">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="6b89e-232">A **ComplexType**要素から成るデータ構造を定義する**EdmSimpleType**プロパティまたはその他の複合型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-232">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span>  <span data-ttu-id="6b89e-233">複合型は、エンティティ型または別の複合型のプロパティにすることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-233">A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="6b89e-234">複合型は、データを定義するという点でエンティティ型に似ています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-234">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="6b89e-235">ただし、複合型とエンティティ型の間にはいくつかの重要な違いがあります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-235">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="6b89e-236">複合型には ID (またはキー) がないため、独立して存在することができません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-236">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="6b89e-237">複合型は、エンティティ型またはその他の複合型のプロパティとしてのみ存在できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-237">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="6b89e-238">複合型は、アソシエーションに参加できません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-238">Complex types cannot participate in associations.</span></span> <span data-ttu-id="6b89e-239">どちらも、アソシエーションの end が複合型を指定でき、したがって複合型のナビゲーション プロパティを定義することはできません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-239">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="6b89e-240">複合型のスカラー プロパティはそれぞれ null に設定できますが、複合型のプロパティには null 値を指定できません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-240">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="6b89e-241">A **ComplexType**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-241">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-242">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-242">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-243">プロパティ (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-243">Property (zero or more elements)</span></span>
-   <span data-ttu-id="6b89e-244">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-244">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="6b89e-245">次の表に適用できる属性の説明、 **ComplexType**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-245">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="6b89e-246">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-246">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="6b89e-247">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-247">Is Required</span></span> | <span data-ttu-id="6b89e-248">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-248">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-249">名前</span><span class="sxs-lookup"><span data-stu-id="6b89e-249">Name</span></span>                                                                                                           | <span data-ttu-id="6b89e-250">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-250">Yes</span></span>         | <span data-ttu-id="6b89e-251">複合型の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-251">The name of the complex type.</span></span> <span data-ttu-id="6b89e-252">複合型の名前は、モデルのスコープ内にある別の複合型、エンティティ型、またはアソシエーションの名前と同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-252">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="6b89e-253">BaseType</span><span class="sxs-lookup"><span data-stu-id="6b89e-253">BaseType</span></span>                                                                                                       | <span data-ttu-id="6b89e-254">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-254">No</span></span>          | <span data-ttu-id="6b89e-255">定義される複合型の基本データ型である、別の複合型の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-255">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="6b89e-256">> この属性では、CSDL v1 で適用されません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-256">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="6b89e-257">複合型の継承は、このバージョンではサポートされません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-257">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="6b89e-258">抽象</span><span class="sxs-lookup"><span data-stu-id="6b89e-258">Abstract</span></span>                                                                                                       | <span data-ttu-id="6b89e-259">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-259">No</span></span>          | <span data-ttu-id="6b89e-260">**True**または**False** (既定値)、複合型が抽象型がかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-260">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="6b89e-261">> この属性では、CSDL v1 で適用されません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-261">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="6b89e-262">このバージョンの複合型を抽象型にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-262">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="6b89e-263">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ComplexType**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-263">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="6b89e-264">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-264">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-265">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-265">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-266">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-266">Example</span></span>

<span data-ttu-id="6b89e-267">次の例では、複合型、**アドレス**で、 **EdmSimpleType**プロパティ**StreetAddress**、**市区町村**、 **StateOrProvince**、**国**、および**PostalCode**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-267">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="6b89e-268">複合型を定義する**アドレス**(上)、エンティティ型のプロパティとしてする必要がありますを宣言するエンティティ型の定義のプロパティの型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-268">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="6b89e-269">次の例は、**アドレス**プロパティとして複合型のエンティティ型である (**パブリッシャー**)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-269">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

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
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="6b89e-270">DefiningExpression 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-270">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="6b89e-271">**DefiningExpression**概念スキーマ定義言語 (CSDL) 内の要素には、概念モデルで関数を定義する Entity SQL 式が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-271">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="6b89e-272">検証のため、 **DefiningExpression**要素は、任意のコンテンツを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-272">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="6b89e-273">ただし、Entity Framework は例外をスロー時、 **DefiningExpression**要素に有効な Entity SQL が含まれていません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-273">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-274">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-274">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-275">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **DefiningExpression**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-275">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="6b89e-276">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-276">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-277">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-277">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="6b89e-278">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-278">Example</span></span>

<span data-ttu-id="6b89e-279">次の例では、 **DefiningExpression**書籍が発行されて以来、年数を返す関数を定義する要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-279">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="6b89e-280">コンテンツ、 **DefiningExpression** Entity SQL で要素が書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-280">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="6b89e-281">Dependent 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-281">Dependent Element (CSDL)</span></span>

<span data-ttu-id="6b89e-282">**依存**概念スキーマ定義言語 (CSDL) 内の要素は ReferentialConstraint 要素に子要素であり参照に関する制約の依存 end を定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-282">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="6b89e-283">A **ReferentialConstraint**要素は、リレーショナル データベースで参照整合性制約と同様の機能を定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-283">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="6b89e-284">データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティが別のエンティティ型のエンティティ キーを参照できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-284">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="6b89e-285">参照されるエンティティ型が呼び出される、*プリンシパル end*の制約。</span><span class="sxs-lookup"><span data-stu-id="6b89e-285">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="6b89e-286">プリンシパル end を参照するエンティティ型が呼び出される、*依存 end*の制約。</span><span class="sxs-lookup"><span data-stu-id="6b89e-286">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="6b89e-287">**PropertyRef**要素を使用すると、プリンシパル end を参照するキーを指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-287">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="6b89e-288">**依存**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-288">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-289">PropertyRef (1 つまたは複数の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-289">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="6b89e-290">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-290">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-291">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-291">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-292">次の表に適用できる属性の説明、**依存**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-292">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="6b89e-293">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-293">Attribute Name</span></span> | <span data-ttu-id="6b89e-294">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-294">Is Required</span></span> | <span data-ttu-id="6b89e-295">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-295">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="6b89e-296">**ロール**</span><span class="sxs-lookup"><span data-stu-id="6b89e-296">**Role**</span></span>       | <span data-ttu-id="6b89e-297">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-297">Yes</span></span>         | <span data-ttu-id="6b89e-298">アソシエーションの依存 End 上のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-298">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-299">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**依存**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-299">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="6b89e-300">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-300">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-301">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-301">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-302">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-302">Example</span></span>

<span data-ttu-id="6b89e-303">次の例は、 **ReferentialConstraint**要素の定義の一部として使用されている、 **PublishedBy**アソシエーション。</span><span class="sxs-lookup"><span data-stu-id="6b89e-303">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="6b89e-304">**PublisherId**のプロパティ、**帳**参照に関する制約の依存 end のエンティティ型を使用します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-304">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

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
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="6b89e-305">Documentation 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-305">Documentation Element (CSDL)</span></span>

<span data-ttu-id="6b89e-306">**ドキュメント**親要素で定義されているオブジェクトに関する情報を提供する概念スキーマ定義言語 (CSDL) 内の要素を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-306">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="6b89e-307">.Edmx ファイルの場合、**ドキュメント**要素の内容 (エンティティ、アソシエーション、プロパティなど)、EF デザイナーのデザイン画面上のオブジェクトとして表示される要素の子である、**ドキュメント**要素は、Visual Studio に表示されます**プロパティ**ウィンドウで、オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="6b89e-307">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="6b89e-308">**ドキュメント**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-308">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-309">**概要**: 親要素の簡単な説明。</span><span class="sxs-lookup"><span data-stu-id="6b89e-309">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="6b89e-310">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-310">(zero or one element)</span></span>
-   <span data-ttu-id="6b89e-311">**LongDescription**: 親要素の説明を提供します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-311">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="6b89e-312">(0 個または 1 個の要素)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-312">(zero or one element)</span></span>
-   <span data-ttu-id="6b89e-313">Annotation 要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-313">Annotation elements.</span></span> <span data-ttu-id="6b89e-314">(0 個以上の要素)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-314">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-315">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-315">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-316">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**ドキュメント**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-316">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="6b89e-317">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-317">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-318">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-318">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="6b89e-319">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-319">Example</span></span>

<span data-ttu-id="6b89e-320">次の例は、**ドキュメント**EntityType 要素の子要素としての要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-320">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="6b89e-321">次のスニペットにあった場合の CSDL は、コンテンツの .edmx ファイルの内容、**概要**と**LongDescription**要素が、Visual Studio に含まれる**プロパティ**ウィンドウをクリックすると、`Customer`エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-321">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

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
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="6b89e-322">End 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-322">End Element (CSDL)</span></span>

<span data-ttu-id="6b89e-323">**エンド**概念スキーマ定義言語 (CSDL) の要素は Association 要素または AssociationSet 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-323">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="6b89e-324">各ケースでは、役割で、**エンド**要素が異なると、該当する属性が異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-324">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="6b89e-325">Association 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="6b89e-325">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="6b89e-326">**エンド**要素 (の子として、**アソシエーション**要素) のアソシエーションの一方の端とのアソシエーションの end に存在できるエンティティ型のインスタンスの数のエンティティ型を識別します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-326">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="6b89e-327">アソシエーション End はアソシエーションの一部として定義され、アソシエーションには必ず 2 つのアソシエーション End が必要です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-327">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="6b89e-328">アソシエーションの 1 つの End にあるエンティティ型インスタンスには、ナビゲーション プロパティまたは外部キーからアクセスできます (エンティティ型で公開されている場合)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-328">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="6b89e-329">**エンド**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-329">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-330">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-330">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-331">OnDelete (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-331">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-332">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-332">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-333">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-333">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-334">次の表に適用できる属性、**エンド**要素の子である場合に、**アソシエーション**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-334">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="6b89e-335">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-335">Attribute Name</span></span>   | <span data-ttu-id="6b89e-336">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-336">Is Required</span></span> | <span data-ttu-id="6b89e-337">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-337">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-338">**Type**</span><span class="sxs-lookup"><span data-stu-id="6b89e-338">**Type**</span></span>         | <span data-ttu-id="6b89e-339">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-339">Yes</span></span>         | <span data-ttu-id="6b89e-340">アソシエーションの一方の End のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-340">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="6b89e-341">**ロール**</span><span class="sxs-lookup"><span data-stu-id="6b89e-341">**Role**</span></span>         | <span data-ttu-id="6b89e-342">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-342">No</span></span>          | <span data-ttu-id="6b89e-343">アソシエーション End の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-343">A name for the association end.</span></span> <span data-ttu-id="6b89e-344">名前が指定されない場合、アソシエーション End のエンティティ型の名前が使用されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-344">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="6b89e-345">**多重度**</span><span class="sxs-lookup"><span data-stu-id="6b89e-345">**Multiplicity**</span></span> | <span data-ttu-id="6b89e-346">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-346">Yes</span></span>         | <span data-ttu-id="6b89e-347">**1**、 **0..1**、または**\*** アソシエーションの end に存在できるエンティティ型のインスタンスの数によって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-347">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="6b89e-348">**1**その 1 つのエンティティ型のインスタンスは、アソシエーション end に存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-348">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="6b89e-349">**0..1**アソシエーション end に 0 個または 1 つのエンティティ型のインスタンスが存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-349">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="6b89e-350">**\*** アソシエーション end に 0、1、または複数のエンティティ型のインスタンスが存在することを示します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-350">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-351">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-351">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="6b89e-352">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-352">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-353">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-353">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="6b89e-354">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-354">Example</span></span>

<span data-ttu-id="6b89e-355">次の例は、**アソシエーション**を定義する要素、 **CustomerOrders**アソシエーション。</span><span class="sxs-lookup"><span data-stu-id="6b89e-355">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="6b89e-356">**多重度**値はそれぞれ**エンド**の関連付けを示す多く**注文**と関連付けることができます、**顧客**が、1 つだけ**顧客**関連付けることができる、**順序**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-356">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="6b89e-357">さらに、 **OnDelete**要素には、ことを示しますすべて**注文**に関連する特定の**顧客**に読み込まれたとは ObjectContext をします。削除された場合、**顧客**は削除されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-357">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="6b89e-358">AssociationSet 要素の子としての End 要素</span><span class="sxs-lookup"><span data-stu-id="6b89e-358">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="6b89e-359">**エンド**要素は、アソシエーション セットの一方の end を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-359">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="6b89e-360">**AssociationSet**要素には、2 つ含める必要があります**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-360">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="6b89e-361">含まれる情報を**エンド**要素は、アソシエーション セットのデータ ソースへのマッピングで使用します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-361">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="6b89e-362">**エンド**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-362">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-363">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-363">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-364">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-364">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="6b89e-365">Annotation 要素は、それ以外のすべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-365">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="6b89e-366">Annotation 要素は、CSDL v2 以降にのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-366">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-367">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-367">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-368">次の表に適用できる属性、**エンド**要素の子である場合に、 **AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-368">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="6b89e-369">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-369">Attribute Name</span></span> | <span data-ttu-id="6b89e-370">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-370">Is Required</span></span> | <span data-ttu-id="6b89e-371">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-371">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-372">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="6b89e-372">**EntitySet**</span></span>  | <span data-ttu-id="6b89e-373">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-373">Yes</span></span>         | <span data-ttu-id="6b89e-374">名前、 **EntitySet** 、親の一方の end を定義する要素**AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-374">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="6b89e-375">**EntitySet**で親と同じエンティティ コンテナー要素を定義する必要があります**AssociationSet**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-375">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="6b89e-376">**ロール**</span><span class="sxs-lookup"><span data-stu-id="6b89e-376">**Role**</span></span>       | <span data-ttu-id="6b89e-377">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-377">No</span></span>          | <span data-ttu-id="6b89e-378">アソシエーション セット End の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-378">The name of the association set end.</span></span> <span data-ttu-id="6b89e-379">場合、**ロール**属性が使用されない場合、アソシエーション セット end の名前はエンティティ セットの名前になります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-379">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="6b89e-380">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-380">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="6b89e-381">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-381">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-382">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-382">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="6b89e-383">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-383">Example</span></span>

<span data-ttu-id="6b89e-384">次の例は、 **EntityContainer**要素を持つ**AssociationSet**それぞれに 2 つの要素、**エンド**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-384">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

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
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="6b89e-385">EntityContainer 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-385">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="6b89e-386">**EntityContainer**概念スキーマ定義言語 (CSDL) での要素はエンティティ セット、アソシエーション セット、および関数インポートの論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="6b89e-386">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="6b89e-387">EntityContainerMapping 要素を通じてストレージ モデルのエンティティ コンテナーに、概念モデルのエンティティ コンテナーがマップされます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-387">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="6b89e-388">ストレージ モデルのエンティティ コンテナーは、データベースの構造を記述します。エンティティ セットはデータベースのテーブル、アソシエーション セットは外部キー、関数インポートはストアド プロシージャをそれぞれ記述します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-388">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="6b89e-389">**EntityContainer**要素は、0 個または 1 つのドキュメント要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-389">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="6b89e-390">場合、**ドキュメント**要素が存在する、すべての前にする必要があります、 **EntitySet**、 **AssociationSet**、および**FunctionImport**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-390">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="6b89e-391">**EntityContainer**要素 (表示順) で、次の子要素の 0 個以上を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-391">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-392">EntitySet</span><span class="sxs-lookup"><span data-stu-id="6b89e-392">EntitySet</span></span>
-   <span data-ttu-id="6b89e-393">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="6b89e-393">AssociationSet</span></span>
-   <span data-ttu-id="6b89e-394">FunctionImport</span><span class="sxs-lookup"><span data-stu-id="6b89e-394">FunctionImport</span></span>
-   <span data-ttu-id="6b89e-395">Annotation 要素</span><span class="sxs-lookup"><span data-stu-id="6b89e-395">Annotation elements</span></span>

<span data-ttu-id="6b89e-396">拡張することができます、 **EntityContainer**要素別の内容を含める**EntityContainer**同じ名前空間内にあります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-396">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="6b89e-397">別のコンテンツを含める**EntityContainer**を参照することで**EntityContainer**要素の値の設定、**拡張**属性、の名前を**EntityContainer**に含める要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-397">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="6b89e-398">含まれるすべての子要素**EntityContainer**要素を参照する子要素として扱われます**EntityContainer**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-398">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-399">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-399">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-400">次の表に適用できる属性の説明、 **Using**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-400">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="6b89e-401">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-401">Attribute Name</span></span> | <span data-ttu-id="6b89e-402">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-402">Is Required</span></span> | <span data-ttu-id="6b89e-403">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-403">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="6b89e-404">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-404">**Name**</span></span>       | <span data-ttu-id="6b89e-405">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-405">Yes</span></span>         | <span data-ttu-id="6b89e-406">エンティティ コンテナー名。</span><span class="sxs-lookup"><span data-stu-id="6b89e-406">The name of the entity container.</span></span>                               |
| <span data-ttu-id="6b89e-407">**拡張**</span><span class="sxs-lookup"><span data-stu-id="6b89e-407">**Extends**</span></span>    | <span data-ttu-id="6b89e-408">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-408">No</span></span>          | <span data-ttu-id="6b89e-409">同じ名前空間内にある別のエンティティ コンテナーの名前 </span><span class="sxs-lookup"><span data-stu-id="6b89e-409">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-410">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntityContainer**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-410">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="6b89e-411">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-411">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-412">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-412">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-413">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-413">Example</span></span>

<span data-ttu-id="6b89e-414">次の例は、 **EntityContainer** 3 つのエンティティ セットと 2 つのアソシエーション セットを定義する要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-414">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

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
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="6b89e-415">EntitySet 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-415">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="6b89e-416">**EntitySet**概念スキーマ定義言語要素は、エンティティ型のインスタンスとそのエンティティ型から派生した任意の型のインスタンスの論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="6b89e-416">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="6b89e-417">エンティティ型とエンティティ セットの間のリレーションシップは、リレーショナル データベースの行とテーブルの間のリレーションシップと似ています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-417">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="6b89e-418">エンティティ型は、行と同様に関連データのセットを定義し、エンティティ セットには、テーブルと同様に、その定義のインスタンスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-418">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="6b89e-419">エンティティ セットは、エンティティ型のインスタンスをグループ化するための構造を提供します。これにより、データ ソース内の関連するデータ構造に、エンティティ型のインスタンスをマッピングできるようになります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-419">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="6b89e-420">特定のエンティティ型に対して複数のエンティティ セットを定義できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-420">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="6b89e-421">EF デザイナーは、型ごとに複数のエンティティ セットを含んでいる概念モデルをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-421">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="6b89e-422">**EntitySet**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-422">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-423">Documentation 要素 (0 個または 1 つの要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-423">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="6b89e-424">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-424">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-425">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-425">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-426">次の表に適用できる属性の説明、 **EntitySet**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-426">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="6b89e-427">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-427">Attribute Name</span></span> | <span data-ttu-id="6b89e-428">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-428">Is Required</span></span> | <span data-ttu-id="6b89e-429">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-429">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-430">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-430">**Name**</span></span>       | <span data-ttu-id="6b89e-431">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-431">Yes</span></span>         | <span data-ttu-id="6b89e-432">エンティティ セットの名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-432">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="6b89e-433">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="6b89e-433">**EntityType**</span></span> | <span data-ttu-id="6b89e-434">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-434">Yes</span></span>         | <span data-ttu-id="6b89e-435">エンティティ型の完全修飾名。そのインスタンスは、エンティティ セットに格納されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-435">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-436">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntitySet**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-436">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="6b89e-437">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-437">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-438">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-438">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-439">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-439">Example</span></span>

<span data-ttu-id="6b89e-440">次の例は、 **EntityContainer** 3 つの要素**EntitySet**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-440">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

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
 

<span data-ttu-id="6b89e-441">型ごとに複数のエンティティ セット (Multiple-Entity-Sets-per-Type: MEST) を定義できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-441">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="6b89e-442">次の例の 2 つのエンティティ セットを持つエンティティ コンテナーを定義する、**帳**エンティティの種類。</span><span class="sxs-lookup"><span data-stu-id="6b89e-442">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

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
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="6b89e-443">EntityType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-443">EntityType Element (CSDL)</span></span>

<span data-ttu-id="6b89e-444">**EntityType**要素は、顧客や、概念モデルでの注文などの最上位の概念の構造を表します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-444">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="6b89e-445">エンティティ型は、アプリケーションのエンティティ型インスタンス用テンプレートです。</span><span class="sxs-lookup"><span data-stu-id="6b89e-445">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="6b89e-446">各テンプレートには、次の情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-446">Each template contains the following information:</span></span>

-   <span data-ttu-id="6b89e-447">一意の名前 </span><span class="sxs-lookup"><span data-stu-id="6b89e-447">A unique name.</span></span> <span data-ttu-id="6b89e-448">(必須) </span><span class="sxs-lookup"><span data-stu-id="6b89e-448">(Required.)</span></span>
-   <span data-ttu-id="6b89e-449">1 つ以上のプロパティにより定義されるエンティティ キー </span><span class="sxs-lookup"><span data-stu-id="6b89e-449">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="6b89e-450">(必須) </span><span class="sxs-lookup"><span data-stu-id="6b89e-450">(Required.)</span></span>
-   <span data-ttu-id="6b89e-451">データ格納用のプロパティ </span><span class="sxs-lookup"><span data-stu-id="6b89e-451">Properties for containing data.</span></span> <span data-ttu-id="6b89e-452">(省略可能) </span><span class="sxs-lookup"><span data-stu-id="6b89e-452">(Optional.)</span></span>
-   <span data-ttu-id="6b89e-453">アソシエーションの 1 つの End から別の End へのナビゲーションを可能にするナビゲーション プロパティ </span><span class="sxs-lookup"><span data-stu-id="6b89e-453">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="6b89e-454">(省略可能) </span><span class="sxs-lookup"><span data-stu-id="6b89e-454">(Optional.)</span></span>

<span data-ttu-id="6b89e-455">アプリケーションでは、エンティティ型のインスタンスが特定のオブジェクト (特定の顧客や注文など) を表します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-455">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="6b89e-456">エンティティ型の各インスタンスに対して、エンティティ セット内のエンティティ キーを一意にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-456">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="6b89e-457">2 つのエンティティ型のインスタンスは、型が同じであり、エンティティ キーの値が等しい場合にのみ、等価のインスタンスと見なされます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-457">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="6b89e-458">**EntityType**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-458">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-459">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-459">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-460">キー (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-460">Key (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-461">プロパティ (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-461">Property (zero or more elements)</span></span>
-   <span data-ttu-id="6b89e-462">NavigationProperty (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-462">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="6b89e-463">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-463">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-464">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-464">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-465">次の表に適用できる属性の説明、 **EntityType**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-465">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="6b89e-466">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-466">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="6b89e-467">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-467">Is Required</span></span> | <span data-ttu-id="6b89e-468">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-468">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-469">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-469">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="6b89e-470">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-470">Yes</span></span>         | <span data-ttu-id="6b89e-471">エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-471">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="6b89e-472">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="6b89e-472">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="6b89e-473">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-473">No</span></span>          | <span data-ttu-id="6b89e-474">別のエンティティ型の名前。この型が定義中のエンティティ型の基本型です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-474">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="6b89e-475">**抽象**</span><span class="sxs-lookup"><span data-stu-id="6b89e-475">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="6b89e-476">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-476">No</span></span>          | <span data-ttu-id="6b89e-477">**True**または**False**エンティティ型が抽象型がかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-477">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="6b89e-478">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="6b89e-478">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="6b89e-479">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-479">No</span></span>          | <span data-ttu-id="6b89e-480">**True**または**False**エンティティ型がオープンなエンティティ型がかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-480">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="6b89e-481">>、 **OpenType**属性は、ADO.NET Data Services で使用される概念モデルで定義されているエンティティ型に適用されるのみです。</span><span class="sxs-lookup"><span data-stu-id="6b89e-481">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="6b89e-482">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EntityType**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-482">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="6b89e-483">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-483">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-484">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-484">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-485">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-485">Example</span></span>

<span data-ttu-id="6b89e-486">次の例は、 **EntityType** 3 つの要素**プロパティ**要素と 2 つ**NavigationProperty**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-486">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

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
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="6b89e-487">EnumType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-487">EnumType Element (CSDL)</span></span>

<span data-ttu-id="6b89e-488">**EnumType**要素は、列挙型を表します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-488">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="6b89e-489">**EnumType**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-489">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-490">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-490">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-491">メンバー (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-491">Member (zero or more elements)</span></span>
-   <span data-ttu-id="6b89e-492">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-492">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-493">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-493">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-494">次の表に適用できる属性の説明、 **EnumType**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-494">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="6b89e-495">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-495">Attribute Name</span></span>     | <span data-ttu-id="6b89e-496">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-496">Is Required</span></span> | <span data-ttu-id="6b89e-497">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-497">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-498">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-498">**Name**</span></span>           | <span data-ttu-id="6b89e-499">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-499">Yes</span></span>         | <span data-ttu-id="6b89e-500">エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-500">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="6b89e-501">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="6b89e-501">**IsFlags**</span></span>        | <span data-ttu-id="6b89e-502">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-502">No</span></span>          | <span data-ttu-id="6b89e-503">**True**または**False**フラグのセットとして列挙型を使用するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-503">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="6b89e-504">既定値は**False。** します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-504">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="6b89e-505">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="6b89e-505">**UnderlyingType**</span></span> | <span data-ttu-id="6b89e-506">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-506">No</span></span>          | <span data-ttu-id="6b89e-507">**Edm.Byte**、 **Edm.Int16**、 **Edm.Int32**、 **Edm.Int64**または**Edm.SByte**型の値の範囲を定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-507">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span>   <span data-ttu-id="6b89e-508">基になる列挙体の要素の型の既定値は**Edm.Int32。** します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-508">The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-509">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **EnumType**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-509">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="6b89e-510">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-510">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-511">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-511">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-512">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-512">Example</span></span>

<span data-ttu-id="6b89e-513">次の例は、 **EnumType** 3 つの要素**メンバー**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-513">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="6b89e-514">Function 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-514">Function Element (CSDL)</span></span>

<span data-ttu-id="6b89e-515">**関数**概念スキーマ定義言語 (CSDL) での要素を使用して関数を概念モデルで宣言または定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-515">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="6b89e-516">DefiningExpression 要素を使用して、関数が定義されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-516">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="6b89e-517">A**関数**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-517">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-518">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-518">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-519">パラメーター (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-519">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="6b89e-520">DefiningExpression (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-520">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-521">ReturnType (Function) (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-521">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-522">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-522">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="6b89e-523">いずれかの関数の型を指定する戻り、 **ReturnType** (関数) の要素または**ReturnType**両方ではなく属性 (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-523">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="6b89e-524">有効な戻り値の型には、EdmSimpleType、エンティティ型、複合型、行型、または参照型 (あるいはこれらの型のいずれかのコレクション) があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-524">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-525">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-525">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-526">次の表に適用できる属性の説明、**関数**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-526">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="6b89e-527">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-527">Attribute Name</span></span> | <span data-ttu-id="6b89e-528">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-528">Is Required</span></span> | <span data-ttu-id="6b89e-529">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-529">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="6b89e-530">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-530">**Name**</span></span>       | <span data-ttu-id="6b89e-531">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-531">Yes</span></span>         | <span data-ttu-id="6b89e-532">関数の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-532">The name of the function.</span></span>          |
| <span data-ttu-id="6b89e-533">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="6b89e-533">**ReturnType**</span></span> | <span data-ttu-id="6b89e-534">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-534">No</span></span>          | <span data-ttu-id="6b89e-535">関数の戻り値の型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-535">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-536">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**関数**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-536">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="6b89e-537">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-537">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-538">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-538">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-539">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-539">Example</span></span>

<span data-ttu-id="6b89e-540">次の例では、**関数**インストラクターが雇用されてからの年数を返す関数を定義する要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-540">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="6b89e-541">FunctionImport 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-541">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="6b89e-542">**FunctionImport**概念スキーマ定義言語 (CSDL) 内の要素は、データ ソースで定義されているが、概念モデルでのオブジェクトで使用できる関数を表します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-542">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="6b89e-543">たとえば、データベースのストアド プロシージャを表すストレージ モデルの関数の要素を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-543">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="6b89e-544">A **FunctionImport**概念モデル内の要素は、Entity Framework アプリケーションに対応する関数を表し、FunctionImportMapping 要素を使用して、ストレージ モデル関数にマップされます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-544">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="6b89e-545">関数がアプリケーションで呼び出されると、対応するストアド プロシージャがデータベースで実行されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-545">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="6b89e-546">**FunctionImport**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-546">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-547">ドキュメント (0 個または 1 つの要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-547">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="6b89e-548">パラメーター (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-548">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="6b89e-549">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-549">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="6b89e-550">ReturnType (FunctionImport) (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-550">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="6b89e-551">1 つ**パラメーター**関数が受け取る各パラメーターの要素を定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-551">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="6b89e-552">いずれかの関数の型を指定する戻り、 **ReturnType** (FunctionImport) 要素または**ReturnType**両方ではなく属性 (下記参照)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-552">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="6b89e-553">戻り値の型の値は EdmSimpleType、EntityType、または ComplexType のコレクションである必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-553">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-554">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-554">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-555">次の表に適用できる属性の説明、 **FunctionImport**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-555">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="6b89e-556">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-556">Attribute Name</span></span>   | <span data-ttu-id="6b89e-557">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-557">Is Required</span></span> | <span data-ttu-id="6b89e-558">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-558">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-559">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-559">**Name**</span></span>         | <span data-ttu-id="6b89e-560">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-560">Yes</span></span>         | <span data-ttu-id="6b89e-561">インポートされる関数の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-561">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="6b89e-562">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="6b89e-562">**ReturnType**</span></span>   | <span data-ttu-id="6b89e-563">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-563">No</span></span>          | <span data-ttu-id="6b89e-564">関数が返す型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-564">The type that the function returns.</span></span> <span data-ttu-id="6b89e-565">関数が値を返さない場合には、この属性を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="6b89e-565">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="6b89e-566">それ以外の場合、値には、ComplexType、EntityType、EDMSimpleType のコレクションがあります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-566">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="6b89e-567">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="6b89e-567">**EntitySet**</span></span>    | <span data-ttu-id="6b89e-568">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-568">No</span></span>          | <span data-ttu-id="6b89e-569">関数は、エンティティのコレクションを返す場合の型の値、 **EntitySet**する必要がありますが、エンティティ セット、コレクションが属する。</span><span class="sxs-lookup"><span data-stu-id="6b89e-569">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="6b89e-570">それ以外の場合、 **EntitySet**属性を使用しない必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-570">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="6b89e-571">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="6b89e-571">**IsComposable**</span></span> | <span data-ttu-id="6b89e-572">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-572">No</span></span>          | <span data-ttu-id="6b89e-573">値が設定されている場合 true の場合、関数がコンポーザブルな (テーブル値関数) と、LINQ クエリで使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-573">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span>  <span data-ttu-id="6b89e-574">既定値は **false** です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-574">The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="6b89e-575">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **FunctionImport**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-575">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="6b89e-576">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-576">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-577">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-577">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-578">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-578">Example</span></span>

<span data-ttu-id="6b89e-579">次の例は、 **FunctionImport**要素を 1 つのパラメーターを受け取り、エンティティ型のコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-579">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="6b89e-580">key 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-580">Key Element (CSDL)</span></span>

<span data-ttu-id="6b89e-581">**キー**要素は、EntityType 要素の子要素であり定義、*エンティティ キー* (プロパティまたは一連の id を指定するエンティティ型のプロパティ)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-581">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="6b89e-582">エンティティ キーを構成するプロパティは、デザイン時に選択されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-582">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="6b89e-583">エンティティ キー プロパティの値は、実行時のエンティティ セット内のエンティティ型のインスタンスを一意に識別する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-583">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="6b89e-584">エンティティ キーを構成するプロパティには、エンティティ セット内のインスタンスの一意性を保証するものを選択する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-584">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="6b89e-585">**キー**要素が 1 つまたは複数のエンティティ型のプロパティを参照することで、エンティティ キーを定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-585">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="6b89e-586">**キー**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-586">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="6b89e-587">PropertyRef (1 つまたは複数の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-587">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="6b89e-588">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-588">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-589">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-589">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-590">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**キー**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-590">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="6b89e-591">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-592">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="6b89e-593">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-593">Example</span></span>

<span data-ttu-id="6b89e-594">次の例は、という名前のエンティティ型を定義します。**帳**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-594">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="6b89e-595">エンティティ キーが参照することで定義されている、 **ISBN**エンティティ型のプロパティ。</span><span class="sxs-lookup"><span data-stu-id="6b89e-595">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="6b89e-596">**ISBN**国際標準図書番号 (ISBN) は、書籍を一意に識別するため、プロパティがエンティティ キーとして適しています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-596">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="6b89e-597">次の例では、エンティティ型 (**作成者**) の 2 つのプロパティで構成されるエンティティ キーを持つ**名前**と**アドレス**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-597">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

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
 

<span data-ttu-id="6b89e-598">使用して**名前**と**アドレス**同じ名前の 2 つの作成者は、同じアドレスでライブに可能性がないため、エンティティのキーが妥当な選択が。</span><span class="sxs-lookup"><span data-stu-id="6b89e-598">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="6b89e-599">ただし、エンティティ キーのこの選択では、エンティティ セット内のエンティティ キーの一意性を絶対的に保証することはできません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-599">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="6b89e-600">などのプロパティの追加**著者 Id**を一意に識別するために使用される、著者がここで推奨されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-600">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="6b89e-601">Member 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-601">Member Element (CSDL)</span></span>

<span data-ttu-id="6b89e-602">**メンバー**要素は EnumType 要素の子要素であり、列挙型のメンバーを定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-602">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-603">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-603">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-604">次の表に適用できる属性の説明、 **FunctionImport**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-604">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="6b89e-605">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-605">Attribute Name</span></span> | <span data-ttu-id="6b89e-606">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-606">Is Required</span></span> | <span data-ttu-id="6b89e-607">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-607">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-608">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-608">**Name**</span></span>       | <span data-ttu-id="6b89e-609">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-609">Yes</span></span>         | <span data-ttu-id="6b89e-610">メンバーの名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-610">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="6b89e-611">**[値]**</span><span class="sxs-lookup"><span data-stu-id="6b89e-611">**Value**</span></span>      | <span data-ttu-id="6b89e-612">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-612">No</span></span>          | <span data-ttu-id="6b89e-613">メンバーの値。</span><span class="sxs-lookup"><span data-stu-id="6b89e-613">The value of the member.</span></span> <span data-ttu-id="6b89e-614">既定では、最初のメンバーが値 0、および後続の列挙子の値が 1 ずつインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-614">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="6b89e-615">同じ値を持つ複数のメンバーが存在します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-615">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-616">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **FunctionImport**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-616">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="6b89e-617">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-617">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-618">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-618">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-619">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-619">Example</span></span>

<span data-ttu-id="6b89e-620">次の例は、 **EnumType** 3 つの要素**メンバー**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-620">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="6b89e-621">NavigationProperty 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-621">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="6b89e-622">A **NavigationProperty**要素は、アソシエーションの他方の end への参照を提供するナビゲーション プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-622">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="6b89e-623">プロパティ要素で定義されたプロパティとは異なり、図形とデータの特性のナビゲーション プロパティを定義しません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-623">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="6b89e-624">ナビゲーション プロパティは、アソシエーション内で 2 つのエンティティ型間を移動するための手段を提供します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-624">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="6b89e-625">ナビゲーション プロパティは、アソシエーション End の両方のエンティティ型で省略可能です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-625">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="6b89e-626">1 つのアソシエーション End のエンティティ型にナビゲーション プロパティを定義した場合に、そのアソシエーションの他方の End でもエンティティ型にナビゲーション プロパティを定義する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-626">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="6b89e-627">ナビゲーション プロパティによって返されるデータ型は、リモートのアソシエーション End の多重度により決まります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-627">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="6b89e-628">たとえば、ナビゲーション プロパティ、 **OrdersNavProp**、上に存在する、**顧客**エンティティ型間の一対多のアソシエーションをナビゲートして**顧客**と**順序**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-628">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="6b89e-629">ナビゲーション プロパティのリモートのアソシエーション end がある多重度多くあるため (\*)、そのデータ型は、コレクション (の**順序**)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-629">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="6b89e-630">同様に、ナビゲーション プロパティの場合は、 **CustomerNavProp**、上に存在する、**順序**エンティティ型の場合は、そのデータ型になります**顧客**ので、リモート end の多重度1 つ (1)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-630">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="6b89e-631">A **NavigationProperty**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-631">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-632">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-632">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-633">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-633">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-634">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-634">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-635">次の表に適用できる属性の説明、 **NavigationProperty**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-635">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="6b89e-636">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-636">Attribute Name</span></span>   | <span data-ttu-id="6b89e-637">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-637">Is Required</span></span> | <span data-ttu-id="6b89e-638">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-638">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-639">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-639">**Name**</span></span>         | <span data-ttu-id="6b89e-640">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-640">Yes</span></span>         | <span data-ttu-id="6b89e-641">ナビゲーション プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-641">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="6b89e-642">**リレーションシップ**</span><span class="sxs-lookup"><span data-stu-id="6b89e-642">**Relationship**</span></span> | <span data-ttu-id="6b89e-643">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-643">Yes</span></span>         | <span data-ttu-id="6b89e-644">モデルのスコープ内にあるアソシエーションの名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-644">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="6b89e-645">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="6b89e-645">**ToRole**</span></span>       | <span data-ttu-id="6b89e-646">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-646">Yes</span></span>         | <span data-ttu-id="6b89e-647">ナビゲーションが終了する側のアソシエーション End。</span><span class="sxs-lookup"><span data-stu-id="6b89e-647">The end of the association at which navigation ends.</span></span> <span data-ttu-id="6b89e-648">値、 **ToRole**属性には、いずれかの値と同じである必要があります、**ロール**(AssociationEnd 要素で定義されている) のアソシエーション end のいずれかで定義されている属性。</span><span class="sxs-lookup"><span data-stu-id="6b89e-648">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="6b89e-649">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="6b89e-649">**FromRole**</span></span>     | <span data-ttu-id="6b89e-650">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-650">Yes</span></span>         | <span data-ttu-id="6b89e-651">ナビゲーションが始まる側のアソシエーション End。</span><span class="sxs-lookup"><span data-stu-id="6b89e-651">The end of the association from which navigation begins.</span></span> <span data-ttu-id="6b89e-652">値、 **FromRole**属性には、いずれかの値と同じである必要があります、**ロール**(AssociationEnd 要素で定義されている) のアソシエーション end のいずれかで定義されている属性。</span><span class="sxs-lookup"><span data-stu-id="6b89e-652">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-653">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **NavigationProperty**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-653">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="6b89e-654">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-654">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-655">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-655">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-656">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-656">Example</span></span>

<span data-ttu-id="6b89e-657">次の例では、エンティティ型を定義します (**帳**) 2 つのナビゲーション プロパティを持つ (**PublishedBy**と**WrittenBy**)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-657">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

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
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="6b89e-658">OnDelete 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-658">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="6b89e-659">**OnDelete**概念スキーマ定義言語 (CSDL) 内の要素の関連付けが接続されている動作を定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-659">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="6b89e-660">場合、**アクション**属性に設定されて**Cascade**アソシエーションの一方の end に関連、アソシエーションの他方の end でエンティティ型は最初の end でエンティティ型が削除されたときに削除されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-660">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="6b89e-661">2 つのエンティティ型間の関連付けは、プライマリ キーと主キー リレーションシップに関係なく、アソシエーションの他方の end にあるプリンシパル オブジェクトが削除されたときに読み込まれた依存オブジェクトが削除された場合、 **OnDelete**指定。</span><span class="sxs-lookup"><span data-stu-id="6b89e-661">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="6b89e-662">**OnDelete**要素にのみ影響するアプリケーションのランタイム動作は、データ ソース内の動作は影響しません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-662">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="6b89e-663">データ ソースで定義された動作は、アプリケーションで定義された動作と同じである必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-663">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="6b89e-664">**OnDelete**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-664">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-665">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-665">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-666">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-666">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-667">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-667">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-668">次の表に適用できる属性の説明、 **OnDelete**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-668">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="6b89e-669">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-669">Attribute Name</span></span> | <span data-ttu-id="6b89e-670">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-670">Is Required</span></span> | <span data-ttu-id="6b89e-671">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-671">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-672">**動作**</span><span class="sxs-lookup"><span data-stu-id="6b89e-672">**Action**</span></span>     | <span data-ttu-id="6b89e-673">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-673">Yes</span></span>         | <span data-ttu-id="6b89e-674">**Cascade**または**None**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-674">**Cascade** or **None**.</span></span> <span data-ttu-id="6b89e-675">場合**Cascade**、依存エンティティ型は、プリンシパル エンティティ型が削除されたときに削除されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-675">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="6b89e-676">場合**None**、プリンシパル エンティティ型が削除されたときに、依存エンティティ型は削除されません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-676">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-677">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**アソシエーション**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-677">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="6b89e-678">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-678">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-679">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-679">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-680">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-680">Example</span></span>

<span data-ttu-id="6b89e-681">次の例は、**アソシエーション**を定義する要素、 **CustomerOrders**アソシエーション。</span><span class="sxs-lookup"><span data-stu-id="6b89e-681">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="6b89e-682">**OnDelete**要素には、ことを示しますすべて**注文**に関連する特定の**顧客**と ObjectContext に読み込まれたときに削除されます、 **顧客**は削除されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-682">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="6b89e-683">Parameter 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-683">Parameter Element (CSDL)</span></span>

<span data-ttu-id="6b89e-684">**パラメーター**概念スキーマ定義言語 (CSDL) で要素が、FunctionImport 要素または Function 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-684">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="6b89e-685">FunctionImport 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="6b89e-685">FunctionImport Element Application</span></span>

<span data-ttu-id="6b89e-686">A**パラメーター**要素 (の子として、 **FunctionImport**要素) は、CSDL で宣言された関数インポートの入力と出力のパラメーターを定義するために使用します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-686">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="6b89e-687">**パラメーター**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-687">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-688">ドキュメント (0 個または 1 つの要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-688">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="6b89e-689">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-689">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-690">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-690">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-691">次の表に適用できる属性、**パラメーター**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-691">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="6b89e-692">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-692">Attribute Name</span></span> | <span data-ttu-id="6b89e-693">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-693">Is Required</span></span> | <span data-ttu-id="6b89e-694">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-694">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-695">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-695">**Name**</span></span>       | <span data-ttu-id="6b89e-696">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-696">Yes</span></span>         | <span data-ttu-id="6b89e-697">パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-697">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="6b89e-698">**Type**</span><span class="sxs-lookup"><span data-stu-id="6b89e-698">**Type**</span></span>       | <span data-ttu-id="6b89e-699">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-699">Yes</span></span>         | <span data-ttu-id="6b89e-700">パラメーターの型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-700">The parameter type.</span></span> <span data-ttu-id="6b89e-701">値がある必要があります、 **EDMSimpleType**またはモデルのスコープ内にある複合型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-701">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="6b89e-702">**モード**</span><span class="sxs-lookup"><span data-stu-id="6b89e-702">**Mode**</span></span>       | <span data-ttu-id="6b89e-703">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-703">No</span></span>          | <span data-ttu-id="6b89e-704">**In**、**アウト**、または**InOut**パラメーターは、入力、出力、または入力/出力パラメーターかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-704">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="6b89e-705">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-705">**MaxLength**</span></span>  | <span data-ttu-id="6b89e-706">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-706">No</span></span>          | <span data-ttu-id="6b89e-707">パラメーターの許容最大長。</span><span class="sxs-lookup"><span data-stu-id="6b89e-707">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="6b89e-708">**精度**</span><span class="sxs-lookup"><span data-stu-id="6b89e-708">**Precision**</span></span>  | <span data-ttu-id="6b89e-709">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-709">No</span></span>          | <span data-ttu-id="6b89e-710">パラメーターの有効桁数。</span><span class="sxs-lookup"><span data-stu-id="6b89e-710">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="6b89e-711">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="6b89e-711">**Scale**</span></span>      | <span data-ttu-id="6b89e-712">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-712">No</span></span>          | <span data-ttu-id="6b89e-713">パラメーターの小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="6b89e-713">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="6b89e-714">**SRID**</span><span class="sxs-lookup"><span data-stu-id="6b89e-714">**SRID**</span></span>       | <span data-ttu-id="6b89e-715">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-715">No</span></span>          | <span data-ttu-id="6b89e-716">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-716">Spatial System Reference Identifier.</span></span> <span data-ttu-id="6b89e-717">空間型のパラメーターに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-717">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="6b89e-718">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-718">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-719">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**パラメーター**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-719">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="6b89e-720">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-720">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-721">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-721">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="6b89e-722">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-722">Example</span></span>

<span data-ttu-id="6b89e-723">次の例は、 **FunctionImport**要素を 1 つ**パラメーター**子要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-723">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="6b89e-724">関数は、1 つの入力パラメーターを受け取り、エンティティ型のコレクションを返します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-724">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="6b89e-725">Function 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="6b89e-725">Function Element Application</span></span>

<span data-ttu-id="6b89e-726">A**パラメーター**要素 (の子として、**関数**要素)、概念モデルで定義または宣言される関数のパラメーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-726">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="6b89e-727">**パラメーター**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-727">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-728">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-728">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="6b89e-729">CollectionType (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-729">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="6b89e-730">ReferenceType (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-730">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="6b89e-731">RowType (0 個または 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-731">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="6b89e-732">1 つだけ、 **CollectionType**、 **ReferenceType**、または**RowType**要素の子要素であることができます、**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-732">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="6b89e-733">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-733">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="6b89e-734">Annotation 要素は、それ以外のすべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-734">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="6b89e-735">Annotation 要素は、CSDL v2 以降にのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-735">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-736">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-736">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-737">次の表に適用できる属性、**パラメーター**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-737">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="6b89e-738">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-738">Attribute Name</span></span>   | <span data-ttu-id="6b89e-739">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-739">Is Required</span></span> | <span data-ttu-id="6b89e-740">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-740">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-741">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-741">**Name**</span></span>         | <span data-ttu-id="6b89e-742">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-742">Yes</span></span>         | <span data-ttu-id="6b89e-743">パラメーターの名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-743">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="6b89e-744">**Type**</span><span class="sxs-lookup"><span data-stu-id="6b89e-744">**Type**</span></span>         | <span data-ttu-id="6b89e-745">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-745">No</span></span>          | <span data-ttu-id="6b89e-746">パラメーターの型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-746">The parameter type.</span></span> <span data-ttu-id="6b89e-747">パラメーターには次のいずれかの型 (またはこれらの型のコレクション) を使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-747">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="6b89e-748">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="6b89e-748">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="6b89e-749">エンティティ型</span><span class="sxs-lookup"><span data-stu-id="6b89e-749">entity type</span></span> <br/> <span data-ttu-id="6b89e-750">複合型</span><span class="sxs-lookup"><span data-stu-id="6b89e-750">complex type</span></span> <br/> <span data-ttu-id="6b89e-751">行型</span><span class="sxs-lookup"><span data-stu-id="6b89e-751">row type</span></span> <br/> <span data-ttu-id="6b89e-752">参照型</span><span class="sxs-lookup"><span data-stu-id="6b89e-752">reference type</span></span>                             |
| <span data-ttu-id="6b89e-753">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="6b89e-753">**Nullable**</span></span>     | <span data-ttu-id="6b89e-754">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-754">No</span></span>          | <span data-ttu-id="6b89e-755">**True** (既定値) または**False**プロパティを持つことができるかどうかに応じて、 **null**値。</span><span class="sxs-lookup"><span data-stu-id="6b89e-755">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="6b89e-756">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="6b89e-756">**DefaultValue**</span></span> | <span data-ttu-id="6b89e-757">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-757">No</span></span>          | <span data-ttu-id="6b89e-758">プロパティの既定値。</span><span class="sxs-lookup"><span data-stu-id="6b89e-758">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="6b89e-759">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-759">**MaxLength**</span></span>    | <span data-ttu-id="6b89e-760">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-760">No</span></span>          | <span data-ttu-id="6b89e-761">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="6b89e-761">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="6b89e-762">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-762">**FixedLength**</span></span>  | <span data-ttu-id="6b89e-763">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-763">No</span></span>          | <span data-ttu-id="6b89e-764">**True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-764">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="6b89e-765">**精度**</span><span class="sxs-lookup"><span data-stu-id="6b89e-765">**Precision**</span></span>    | <span data-ttu-id="6b89e-766">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-766">No</span></span>          | <span data-ttu-id="6b89e-767">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="6b89e-767">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="6b89e-768">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="6b89e-768">**Scale**</span></span>        | <span data-ttu-id="6b89e-769">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-769">No</span></span>          | <span data-ttu-id="6b89e-770">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="6b89e-770">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="6b89e-771">**SRID**</span><span class="sxs-lookup"><span data-stu-id="6b89e-771">**SRID**</span></span>         | <span data-ttu-id="6b89e-772">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-772">No</span></span>          | <span data-ttu-id="6b89e-773">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-773">Spatial System Reference Identifier.</span></span> <span data-ttu-id="6b89e-774">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-774">Valid only for properties of spatial types.</span></span> <span data-ttu-id="6b89e-775">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-775">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="6b89e-776">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="6b89e-776">**Unicode**</span></span>      | <span data-ttu-id="6b89e-777">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-777">No</span></span>          | <span data-ttu-id="6b89e-778">**True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-778">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="6b89e-779">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="6b89e-779">**Collation**</span></span>    | <span data-ttu-id="6b89e-780">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-780">No</span></span>          | <span data-ttu-id="6b89e-781">データ ソースで使用する照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="6b89e-781">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="6b89e-782">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**パラメーター**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-782">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="6b89e-783">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-783">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-784">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-784">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="6b89e-785">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-785">Example</span></span>

<span data-ttu-id="6b89e-786">次の例は、**関数**いずれかを使用する要素**パラメーター**関数パラメーターを定義する子要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-786">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a><span data-ttu-id="6b89e-787">Principal 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-787">Principal Element (CSDL)</span></span>

<span data-ttu-id="6b89e-788">**プリンシパル**概念スキーマ定義言語 (CSDL) での要素は参照に関する制約のプリンシパル end を定義する ReferentialConstraint 要素に子要素です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-788">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="6b89e-789">A **ReferentialConstraint**要素は、リレーショナル データベースで参照整合性制約と同様の機能を定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-789">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="6b89e-790">データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティが別のエンティティ型のエンティティ キーを参照できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-790">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="6b89e-791">参照されるエンティティ型が呼び出される、*プリンシパル end*の制約。</span><span class="sxs-lookup"><span data-stu-id="6b89e-791">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="6b89e-792">プリンシパル end を参照するエンティティ型が呼び出される、*依存 end*の制約。</span><span class="sxs-lookup"><span data-stu-id="6b89e-792">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="6b89e-793">**PropertyRef**要素を使用すると、依存 end により参照されるキーを指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-793">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="6b89e-794">**プリンシパル**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-794">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-795">PropertyRef (1 つまたは複数の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-795">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="6b89e-796">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-796">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-797">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-797">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-798">次の表に適用できる属性の説明、**プリンシパル**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-798">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="6b89e-799">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-799">Attribute Name</span></span> | <span data-ttu-id="6b89e-800">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-800">Is Required</span></span> | <span data-ttu-id="6b89e-801">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-801">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="6b89e-802">**ロール**</span><span class="sxs-lookup"><span data-stu-id="6b89e-802">**Role**</span></span>       | <span data-ttu-id="6b89e-803">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-803">Yes</span></span>         | <span data-ttu-id="6b89e-804">アソシエーションのプリンシパル End 上のエンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-804">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-805">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プリンシパル**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-805">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="6b89e-806">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-806">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-807">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-807">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-808">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-808">Example</span></span>

<span data-ttu-id="6b89e-809">次の例は、 **ReferentialConstraint**要素の定義の一部である、 **PublishedBy**アソシエーション。</span><span class="sxs-lookup"><span data-stu-id="6b89e-809">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="6b89e-810">**Id**のプロパティ、**パブリッシャー**参照に関する制約のプリンシパル end のエンティティ型を使用します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-810">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

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
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="6b89e-811">Property 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-811">Property Element (CSDL)</span></span>

<span data-ttu-id="6b89e-812">**プロパティ**概念スキーマ定義言語 (CSDL) の要素は、EntityType 要素、複合型の要素または RowType 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-812">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="6b89e-813">EntityType 要素と ComplexType 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="6b89e-813">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="6b89e-814">**プロパティ**要素 (の子として**EntityType**または**ComplexType**要素) 図形と、エンティティ型または複合型のインスタンスに含まれるデータの特性を定義.</span><span class="sxs-lookup"><span data-stu-id="6b89e-814">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="6b89e-815">概念モデルのプロパティは、クラスで定義されるプロパティに似ています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-815">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="6b89e-816">クラスのプロパティがクラスの構造を定義し、オブジェクトに関する情報を伝達するのと同様に、概念モデルのプロパティはエンティティ型の構造を定義し、エンティティ型のインスタンスに関する情報を伝達します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-816">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="6b89e-817">**プロパティ**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-817">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-818">Documentation 要素 (0 個または 1 つの要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-818">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="6b89e-819">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-819">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="6b89e-820">次のファセットに適用できる、**プロパティ**要素: **Nullable**、 **DefaultValue**、 **MaxLength**、 **FixedLength**、**精度**、**スケール**、 **Unicode**、**照合**、 **ConcurrencyMode**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-820">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="6b89e-821">ファセットは、プロパティ値をデータ ストアに格納する方法に関する情報を提供する XML 属性です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-821">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="6b89e-822">ファセットは型のプロパティにのみ適用できます**EDMSimpleType**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-822">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-823">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-823">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-824">次の表に適用できる属性、**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-824">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="6b89e-825">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-825">Attribute Name</span></span>                                                         | <span data-ttu-id="6b89e-826">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-826">Is Required</span></span> | <span data-ttu-id="6b89e-827">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-827">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-828">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-828">**Name**</span></span>                                                               | <span data-ttu-id="6b89e-829">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-829">Yes</span></span>         | <span data-ttu-id="6b89e-830">プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-830">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="6b89e-831">**Type**</span><span class="sxs-lookup"><span data-stu-id="6b89e-831">**Type**</span></span>                                                               | <span data-ttu-id="6b89e-832">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-832">Yes</span></span>         | <span data-ttu-id="6b89e-833">プロパティ値の型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-833">The type of the property value.</span></span> <span data-ttu-id="6b89e-834">プロパティ値の型である必要があります、 **EDMSimpleType**またはモデルのスコープ内にある複合型 (完全修飾名で示されます)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-834">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="6b89e-835">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="6b89e-835">**Nullable**</span></span>                                                           | <span data-ttu-id="6b89e-836">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-836">No</span></span>          | <span data-ttu-id="6b89e-837">**True** (既定値) または<strong>False</strong>プロパティが null 値を持っているかどうかによって。</span><span class="sxs-lookup"><span data-stu-id="6b89e-837">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="6b89e-838">> 複合型プロパティがありますで CSDL v1`Nullable="False"`します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-838">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="6b89e-839">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="6b89e-839">**DefaultValue**</span></span>                                                       | <span data-ttu-id="6b89e-840">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-840">No</span></span>          | <span data-ttu-id="6b89e-841">プロパティの既定値。</span><span class="sxs-lookup"><span data-stu-id="6b89e-841">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="6b89e-842">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-842">**MaxLength**</span></span>                                                          | <span data-ttu-id="6b89e-843">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-843">No</span></span>          | <span data-ttu-id="6b89e-844">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="6b89e-844">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="6b89e-845">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-845">**FixedLength**</span></span>                                                        | <span data-ttu-id="6b89e-846">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-846">No</span></span>          | <span data-ttu-id="6b89e-847">**True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-847">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="6b89e-848">**精度**</span><span class="sxs-lookup"><span data-stu-id="6b89e-848">**Precision**</span></span>                                                          | <span data-ttu-id="6b89e-849">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-849">No</span></span>          | <span data-ttu-id="6b89e-850">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="6b89e-850">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="6b89e-851">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="6b89e-851">**Scale**</span></span>                                                              | <span data-ttu-id="6b89e-852">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-852">No</span></span>          | <span data-ttu-id="6b89e-853">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="6b89e-853">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="6b89e-854">**SRID**</span><span class="sxs-lookup"><span data-stu-id="6b89e-854">**SRID**</span></span>                                                               | <span data-ttu-id="6b89e-855">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-855">No</span></span>          | <span data-ttu-id="6b89e-856">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-856">Spatial System Reference Identifier.</span></span> <span data-ttu-id="6b89e-857">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-857">Valid only for properties of spatial types.</span></span> <span data-ttu-id="6b89e-858">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-858">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="6b89e-859">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="6b89e-859">**Unicode**</span></span>                                                            | <span data-ttu-id="6b89e-860">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-860">No</span></span>          | <span data-ttu-id="6b89e-861">**True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-861">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="6b89e-862">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="6b89e-862">**Collation**</span></span>                                                          | <span data-ttu-id="6b89e-863">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-863">No</span></span>          | <span data-ttu-id="6b89e-864">データ ソースで使用する照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="6b89e-864">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="6b89e-865">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="6b89e-865">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="6b89e-866">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-866">No</span></span>          | <span data-ttu-id="6b89e-867">**None** (既定値) または**固定**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-867">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="6b89e-868">値が設定されている場合**固定**プロパティの値は、オプティミスティック同時実行制御チェックで使用されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-868">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="6b89e-869">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-869">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="6b89e-870">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-870">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-871">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-871">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="6b89e-872">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-872">Example</span></span>

<span data-ttu-id="6b89e-873">次の例は、 **EntityType** 3 つの要素**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-873">The following example shows an **EntityType** element with three **Property** elements:</span></span>

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
 

<span data-ttu-id="6b89e-874">次の例は、 **ComplexType** 5 つの要素**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-874">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="6b89e-875">RowType 要素のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="6b89e-875">RowType Element Application</span></span>

<span data-ttu-id="6b89e-876">**プロパティ**要素 (の子として、 **RowType**要素) に渡されるしたりできる、モデル定義関数から返されるデータの特性と図形を定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-876">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="6b89e-877">**プロパティ**要素は、次の子要素の 1 つだけであることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-877">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="6b89e-878">CollectionType</span><span class="sxs-lookup"><span data-stu-id="6b89e-878">CollectionType</span></span>
-   <span data-ttu-id="6b89e-879">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="6b89e-879">ReferenceType</span></span>
-   <span data-ttu-id="6b89e-880">RowType</span><span class="sxs-lookup"><span data-stu-id="6b89e-880">RowType</span></span>

<span data-ttu-id="6b89e-881">**プロパティ**要素は、数値の子注釈要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-881">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="6b89e-882">Annotation 要素は、CSDL v2 以降にのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-882">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-883">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-883">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-884">次の表に適用できる属性、**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-884">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="6b89e-885">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-885">Attribute Name</span></span>                                                     | <span data-ttu-id="6b89e-886">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-886">Is Required</span></span> | <span data-ttu-id="6b89e-887">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-887">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-888">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-888">**Name**</span></span>                                                           | <span data-ttu-id="6b89e-889">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-889">Yes</span></span>         | <span data-ttu-id="6b89e-890">プロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-890">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="6b89e-891">**Type**</span><span class="sxs-lookup"><span data-stu-id="6b89e-891">**Type**</span></span>                                                           | <span data-ttu-id="6b89e-892">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-892">Yes</span></span>         | <span data-ttu-id="6b89e-893">プロパティ値の型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-893">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="6b89e-894">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="6b89e-894">**Nullable**</span></span>                                                       | <span data-ttu-id="6b89e-895">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-895">No</span></span>          | <span data-ttu-id="6b89e-896">**True** (既定値) または**False**プロパティが null 値を持っているかどうかによって。</span><span class="sxs-lookup"><span data-stu-id="6b89e-896">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="6b89e-897">> 複合型プロパティがありますで CSDL v1`Nullable="False"`します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-897">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="6b89e-898">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="6b89e-898">**DefaultValue**</span></span>                                                   | <span data-ttu-id="6b89e-899">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-899">No</span></span>          | <span data-ttu-id="6b89e-900">プロパティの既定値。</span><span class="sxs-lookup"><span data-stu-id="6b89e-900">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="6b89e-901">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-901">**MaxLength**</span></span>                                                      | <span data-ttu-id="6b89e-902">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-902">No</span></span>          | <span data-ttu-id="6b89e-903">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="6b89e-903">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="6b89e-904">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-904">**FixedLength**</span></span>                                                    | <span data-ttu-id="6b89e-905">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-905">No</span></span>          | <span data-ttu-id="6b89e-906">**True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-906">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="6b89e-907">**精度**</span><span class="sxs-lookup"><span data-stu-id="6b89e-907">**Precision**</span></span>                                                      | <span data-ttu-id="6b89e-908">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-908">No</span></span>          | <span data-ttu-id="6b89e-909">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="6b89e-909">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="6b89e-910">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="6b89e-910">**Scale**</span></span>                                                          | <span data-ttu-id="6b89e-911">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-911">No</span></span>          | <span data-ttu-id="6b89e-912">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="6b89e-912">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="6b89e-913">**SRID**</span><span class="sxs-lookup"><span data-stu-id="6b89e-913">**SRID**</span></span>                                                           | <span data-ttu-id="6b89e-914">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-914">No</span></span>          | <span data-ttu-id="6b89e-915">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-915">Spatial System Reference Identifier.</span></span> <span data-ttu-id="6b89e-916">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-916">Valid only for properties of spatial types.</span></span> <span data-ttu-id="6b89e-917">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-917">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="6b89e-918">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="6b89e-918">**Unicode**</span></span>                                                        | <span data-ttu-id="6b89e-919">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-919">No</span></span>          | <span data-ttu-id="6b89e-920">**True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-920">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="6b89e-921">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="6b89e-921">**Collation**</span></span>                                                      | <span data-ttu-id="6b89e-922">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-922">No</span></span>          | <span data-ttu-id="6b89e-923">データ ソースで使用する照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="6b89e-923">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="6b89e-924">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**プロパティ**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-924">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="6b89e-925">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-925">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-926">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-926">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="6b89e-927">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-927">Example</span></span>

<span data-ttu-id="6b89e-928">次の例は**プロパティ**要素、モデル定義関数の戻り値の型の形状を定義するために使用します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-928">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

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
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="6b89e-929">PropertyRef 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-929">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="6b89e-930">**PropertyRef**概念スキーマ定義言語 (CSDL) 内の要素は、プロパティは実行は、次のロールのいずれかを示すエンティティ型のプロパティを参照します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-930">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="6b89e-931">エンティティのキー (ID を指定するエンティティ型のプロパティまたは一連のプロパティ) の一部。</span><span class="sxs-lookup"><span data-stu-id="6b89e-931">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="6b89e-932">1 つまたは複数**PropertyRef**要素は、エンティティ キーを定義するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-932">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="6b89e-933">参照制約の依存 End または プリンシパル End。</span><span class="sxs-lookup"><span data-stu-id="6b89e-933">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="6b89e-934">**PropertyRef**要素が子要素としての annotation 要素 (0 個以上) があることができますのみです。</span><span class="sxs-lookup"><span data-stu-id="6b89e-934">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="6b89e-935">Annotation 要素は、CSDL v2 以降にのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-935">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-936">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-936">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-937">次の表に適用できる属性の説明、 **PropertyRef**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-937">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="6b89e-938">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-938">Attribute Name</span></span> | <span data-ttu-id="6b89e-939">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-939">Is Required</span></span> | <span data-ttu-id="6b89e-940">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-940">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="6b89e-941">**Name**</span><span class="sxs-lookup"><span data-stu-id="6b89e-941">**Name**</span></span>       | <span data-ttu-id="6b89e-942">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-942">Yes</span></span>         | <span data-ttu-id="6b89e-943">参照されているプロパティの名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-943">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-944">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **PropertyRef**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-944">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="6b89e-945">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-945">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-946">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-946">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-947">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-947">Example</span></span>

<span data-ttu-id="6b89e-948">次の例は、エンティティ型を定義します (**帳**)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-948">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="6b89e-949">エンティティ キーが参照することで定義されている、 **ISBN**エンティティ型のプロパティ。</span><span class="sxs-lookup"><span data-stu-id="6b89e-949">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="6b89e-950">次の例では、2 つ**PropertyRef**要素を使用すると、その 2 つを示すプロパティ (**Id**と**PublisherId**) は、プリンシパル end と依存 end の参照制約です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-950">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

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
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="6b89e-951">ReferenceType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-951">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="6b89e-952">**ReferenceType**概念スキーマ定義言語 (CSDL) 内の要素をエンティティ型への参照を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-952">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="6b89e-953">**ReferenceType**要素は、次の要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-953">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="6b89e-954">ReturnType (関数)</span><span class="sxs-lookup"><span data-stu-id="6b89e-954">ReturnType (Function)</span></span>
-   <span data-ttu-id="6b89e-955">パラメーター</span><span class="sxs-lookup"><span data-stu-id="6b89e-955">Parameter</span></span>
-   <span data-ttu-id="6b89e-956">CollectionType</span><span class="sxs-lookup"><span data-stu-id="6b89e-956">CollectionType</span></span>

<span data-ttu-id="6b89e-957">**ReferenceType**要素は、関数のパラメーターまたは戻り値の型を定義するときに使用します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-957">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="6b89e-958">A **ReferenceType**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-958">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-959">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-959">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-960">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-960">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-961">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-961">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-962">次の表に適用できる属性の説明、 **ReferenceType**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-962">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="6b89e-963">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-963">Attribute Name</span></span> | <span data-ttu-id="6b89e-964">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-964">Is Required</span></span> | <span data-ttu-id="6b89e-965">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-965">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="6b89e-966">**Type**</span><span class="sxs-lookup"><span data-stu-id="6b89e-966">**Type**</span></span>       | <span data-ttu-id="6b89e-967">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-967">Yes</span></span>         | <span data-ttu-id="6b89e-968">参照先エンティティ型の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-968">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-969">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReferenceType**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-969">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="6b89e-970">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-970">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-971">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-971">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-972">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-972">Example</span></span>

<span data-ttu-id="6b89e-973">次の例は、 **ReferenceType**要素の子として使用される、**パラメーター**への参照を受け取るモデル定義関数内の要素を**Person**エンティティ種類:</span><span class="sxs-lookup"><span data-stu-id="6b89e-973">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

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
 

<span data-ttu-id="6b89e-974">次の例は、 **ReferenceType**要素の子として使用される、 **ReturnType** (関数) の要素への参照を返すモデル定義関数で、 **Person**エンティティの種類。</span><span class="sxs-lookup"><span data-stu-id="6b89e-974">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

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
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="6b89e-975">ReferentialConstraint 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-975">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="6b89e-976">A **ReferentialConstraint**概念スキーマ定義言語 (CSDL) 内の要素は、リレーショナル データベースで参照整合性制約と同様の機能を定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-976">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="6b89e-977">データベース テーブルの列が別のテーブルの主キーを参照できるのと同じように、エンティティ型のプロパティが別のエンティティ型のエンティティ キーを参照できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-977">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="6b89e-978">参照されるエンティティ型が呼び出される、*プリンシパル end*の制約。</span><span class="sxs-lookup"><span data-stu-id="6b89e-978">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="6b89e-979">プリンシパル end を参照するエンティティ型が呼び出される、*依存 end*の制約。</span><span class="sxs-lookup"><span data-stu-id="6b89e-979">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="6b89e-980">1 つのエンティティ型で公開されている外部キーが別のエンティティ型のプロパティを参照している場合、 **ReferentialConstraint**要素が 2 つのエンティティ型間のアソシエーションを定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-980">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="6b89e-981">**ReferentialConstraint** AssociationSetMapping の対応する要素はマッピング仕様言語 (MSL) では必要ありません、2 つのエンティティ型に関する情報が関連要素を提供します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-981">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="6b89e-982">公開された外部キーがない 2 つのエンティティ型間のアソシエーションの対応する必要があります**AssociationSetMapping**要素の関連付け情報をデータ ソースにマップするためにします。</span><span class="sxs-lookup"><span data-stu-id="6b89e-982">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="6b89e-983">外部キーは、エンティティ型で公開されていない場合、 **ReferentialConstraint**要素は、エンティティ型と別のエンティティ型の間に主キーと主キー制約にのみ定義できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-983">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="6b89e-984">A **ReferentialConstraint**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-984">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-985">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-985">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-986">プリンシパル (正確に 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-986">Principal (exactly one element)</span></span>
-   <span data-ttu-id="6b89e-987">依存 (正確に 1 つの要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-987">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="6b89e-988">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-988">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-989">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-989">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-990">**ReferentialConstraint**要素は、任意の数の annotation 属性 (カスタム XML 属性) を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-990">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="6b89e-991">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-991">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-992">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-992">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="6b89e-993">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-993">Example</span></span>

<span data-ttu-id="6b89e-994">次の例は、 **ReferentialConstraint**要素の定義の一部として使用されている、 **PublishedBy**アソシエーション。</span><span class="sxs-lookup"><span data-stu-id="6b89e-994">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

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
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="6b89e-995">(関数) の ReturnType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-995">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="6b89e-996">**ReturnType**概念スキーマ定義言語 (CSDL) で (関数) の要素が Function 要素で定義されている関数の戻り値の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-996">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="6b89e-997">関数の戻り値の型を指定することも、 **ReturnType**属性。</span><span class="sxs-lookup"><span data-stu-id="6b89e-997">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="6b89e-998">返す型には、いずれかを指定できる**EdmSimpleType**エンティティ型、複合型、行型、ref 型、またはこれらの型のいずれかのコレクション。</span><span class="sxs-lookup"><span data-stu-id="6b89e-998">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="6b89e-999">いずれかで、関数の戻り値の型を指定することができます、**型**の属性、 **ReturnType** (関数) 要素では、次の子要素のいずれか。</span><span class="sxs-lookup"><span data-stu-id="6b89e-999">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="6b89e-1000">CollectionType</span><span class="sxs-lookup"><span data-stu-id="6b89e-1000">CollectionType</span></span>
-   <span data-ttu-id="6b89e-1001">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="6b89e-1001">ReferenceType</span></span>
-   <span data-ttu-id="6b89e-1002">RowType</span><span class="sxs-lookup"><span data-stu-id="6b89e-1002">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="6b89e-1003">関数の戻り値の両方を持つ型を指定する場合、モデルは検証されません、**型**の属性、 **ReturnType** (関数) 要素と子要素の 1 つ。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1003">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-1004">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-1004">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-1005">次の表に適用できる属性、 **ReturnType** (関数) の要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1005">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="6b89e-1006">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-1006">Attribute Name</span></span> | <span data-ttu-id="6b89e-1007">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-1007">Is Required</span></span> | <span data-ttu-id="6b89e-1008">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-1008">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="6b89e-1009">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1009">**ReturnType**</span></span> | <span data-ttu-id="6b89e-1010">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1010">No</span></span>          | <span data-ttu-id="6b89e-1011">関数の戻り値の型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1011">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-1012">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReturnType** (関数) の要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1012">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="6b89e-1013">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1013">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-1014">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1014">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-1015">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-1015">Example</span></span>

<span data-ttu-id="6b89e-1016">次の例では、**関数**書籍が出版されて年数を返す関数を定義する要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1016">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="6b89e-1017">戻り値の型がで指定されたに注意してください、**型**の属性を**ReturnType** (関数) の要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1017">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="6b89e-1018">ReturnType (FunctionImport) 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1018">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="6b89e-1019">**ReturnType**概念スキーマ定義言語 (CSDL) で (FunctionImport) 要素は、FunctionImport 要素で定義されている関数の戻り値の型を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1019">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="6b89e-1020">関数の戻り値の型を指定することも、 **ReturnType**属性。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1020">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="6b89e-1021">型では、エンティティ型、複合型の任意のコレクションを返すまたは**EdmSimpleType**、</span><span class="sxs-lookup"><span data-stu-id="6b89e-1021">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="6b89e-1022">関数の戻り値の型を指定した、**型**の属性、 **ReturnType** (FunctionImport) 要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1022">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-1023">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-1023">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-1024">次の表に適用できる属性、 **ReturnType** (FunctionImport) 要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1024">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="6b89e-1025">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-1025">Attribute Name</span></span> | <span data-ttu-id="6b89e-1026">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-1026">Is Required</span></span> | <span data-ttu-id="6b89e-1027">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-1027">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-1028">**Type**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1028">**Type**</span></span>       | <span data-ttu-id="6b89e-1029">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1029">No</span></span>          | <span data-ttu-id="6b89e-1030">関数が返す型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1030">The type that the function returns.</span></span> <span data-ttu-id="6b89e-1031">値は、ComplexType、EntityType、EDMSimpleType のコレクションである必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1031">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="6b89e-1032">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1032">**EntitySet**</span></span>  | <span data-ttu-id="6b89e-1033">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1033">No</span></span>          | <span data-ttu-id="6b89e-1034">関数は、エンティティのコレクションを返す場合の型の値、 **EntitySet**する必要がありますが、エンティティ セット、コレクションが属する。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1034">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="6b89e-1035">それ以外の場合、 **EntitySet**属性を使用しない必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1035">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-1036">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **ReturnType** (FunctionImport) 要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1036">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="6b89e-1037">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1037">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-1038">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1038">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-1039">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-1039">Example</span></span>

<span data-ttu-id="6b89e-1040">次の例では、 **FunctionImport**ブックとパブリッシャーを返します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1040">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="6b89e-1041">2 つの結果セットと 2 つのために、関数が返すことに注意してください。 **ReturnType** (FunctionImport) の要素を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1041">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="6b89e-1042">RowType 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1042">RowType Element (CSDL)</span></span>

<span data-ttu-id="6b89e-1043">A **RowType**概念スキーマ定義言語 (CSDL) の要素は、パラメーターまたは概念モデルで定義された関数の戻り値の型として無名の構造体を定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1043">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="6b89e-1044">A **RowType**要素は、次の要素の子であることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1044">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="6b89e-1045">CollectionType</span><span class="sxs-lookup"><span data-stu-id="6b89e-1045">CollectionType</span></span>
-   <span data-ttu-id="6b89e-1046">パラメーター</span><span class="sxs-lookup"><span data-stu-id="6b89e-1046">Parameter</span></span>
-   <span data-ttu-id="6b89e-1047">ReturnType (関数)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1047">ReturnType (Function)</span></span>

<span data-ttu-id="6b89e-1048">A **RowType**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1048">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-1049">(1 つまたは複数) のプロパティ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1049">Property (one or more)</span></span>
-   <span data-ttu-id="6b89e-1050">Annotation 要素 (0 個以上)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1050">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-1051">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-1051">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-1052">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **RowType**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1052">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="6b89e-1053">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1053">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-1054">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1054">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="6b89e-1055">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-1055">Example</span></span>

<span data-ttu-id="6b89e-1056">次の例では、使用するモデル定義関数、 **CollectionType**関数によって返される行のコレクションを指定する要素 (で指定されている、 **RowType**要素)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1056">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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

## <a name="schema-element-csdl"></a><span data-ttu-id="6b89e-1057">Schema 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1057">Schema Element (CSDL)</span></span>

<span data-ttu-id="6b89e-1058">**スキーマ**要素は概念モデルの定義のルート要素です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1058">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="6b89e-1059">概念モデルを構成するオブジェクト、関数、およびコンテナーの定義を格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1059">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="6b89e-1060">**スキーマ**要素は、次の子要素の 0 個以上を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1060">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="6b89e-1061">Using</span><span class="sxs-lookup"><span data-stu-id="6b89e-1061">Using</span></span>
-   <span data-ttu-id="6b89e-1062">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="6b89e-1062">EntityContainer</span></span>
-   <span data-ttu-id="6b89e-1063">EntityType</span><span class="sxs-lookup"><span data-stu-id="6b89e-1063">EntityType</span></span>
-   <span data-ttu-id="6b89e-1064">EnumType</span><span class="sxs-lookup"><span data-stu-id="6b89e-1064">EnumType</span></span>
-   <span data-ttu-id="6b89e-1065">関連付け</span><span class="sxs-lookup"><span data-stu-id="6b89e-1065">Association</span></span>
-   <span data-ttu-id="6b89e-1066">ComplexType</span><span class="sxs-lookup"><span data-stu-id="6b89e-1066">ComplexType</span></span>
-   <span data-ttu-id="6b89e-1067">関数</span><span class="sxs-lookup"><span data-stu-id="6b89e-1067">Function</span></span>

<span data-ttu-id="6b89e-1068">A**スキーマ**要素は、0 個または 1 つの Annotation 要素を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1068">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="6b89e-1069">**関数**要素と annotation 要素はできるの CSDL v2 以降。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1069">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="6b89e-1070">**スキーマ**要素を使用して、 **Namespace**概念モデルのエンティティ型、複合型、およびアソシエーション オブジェクトの名前空間を定義する属性。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1070">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="6b89e-1071">1 つの名前空間内で 2 つのオブジェクトが同じ名前を持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1071">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="6b89e-1072">名前空間にわたる複数**スキーマ**要素と複数の .csdl ファイル。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1072">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="6b89e-1073">概念モデル名前空間とは別の XML 名前空間から、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1073">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="6b89e-1074">概念モデル名前空間 (で定義されている、 **Namespace**属性) はエンティティ型、複合型、およびアソシエーション型の論理コンテナーです。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1074">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="6b89e-1075">XML 名前空間 (によって示される、 **xmlns**属性) の**スキーマ**要素が子要素と属性の既定の名前空間、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1075">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="6b89e-1076">フォームの XML 名前空間 http://schemas.microsoft.com/ado/YYYY/MM/edm (YYYY と MM 表します年と月それぞれ) に CSDL 用に予約されています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1076">XML namespaces of the form http://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="6b89e-1077">カスタム要素と属性は、このフォームがある名前空間に存在することはできません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1077">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-1078">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-1078">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-1079">次の表の説明属性に適用できる、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1079">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="6b89e-1080">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-1080">Attribute Name</span></span> | <span data-ttu-id="6b89e-1081">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-1081">Is Required</span></span> | <span data-ttu-id="6b89e-1082">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-1082">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-1083">**名前空間**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1083">**Namespace**</span></span>  | <span data-ttu-id="6b89e-1084">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1084">Yes</span></span>         | <span data-ttu-id="6b89e-1085">概念モデルの名前空間。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1085">The namespace of the conceptual model.</span></span> <span data-ttu-id="6b89e-1086">値、 **Namespace**属性が型の完全修飾名に使用されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1086">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="6b89e-1087">たとえば場合、 **EntityType**という名前の*顧客*Simple.Example.Model 名前空間の完全修飾名では、 **EntityType**はSimpleexamplemodel.customer です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1087">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="6b89e-1088">値として、次の文字列を使用することはできません、 **Namespace**属性:**システム**、**一時的な**、または**Edm**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1088">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="6b89e-1089">値、 **Namespace**属性の値と同じにすることはできません、 **Namespace** SSDL スキーマ要素の属性。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1089">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="6b89e-1090">**Alias**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1090">**Alias**</span></span>      | <span data-ttu-id="6b89e-1091">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1091">No</span></span>          | <span data-ttu-id="6b89e-1092">名前空間の名前の代わりに使用される識別子。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1092">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="6b89e-1093">たとえば場合、 **EntityType**という名前*顧客*Simple.Example.Model 名前空間にありの値は、**エイリアス**属性が*モデル*、Model.Customer の完全修飾名として使用することができます、 **EntityType。**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1093">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="6b89e-1094">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1094">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="6b89e-1095">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1095">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-1096">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1096">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-1097">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-1097">Example</span></span>

<span data-ttu-id="6b89e-1098">次の例は、**スキーマ**要素を含む、 **EntityContainer**要素、2 つ**EntityType**要素、および 1 つ**の関連付け**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1098">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="6b89e-1099">TypeRef 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1099">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="6b89e-1100">**TypeRef**概念スキーマ定義言語 (CSDL) 内の要素は、既存の名前付きの型への参照を提供します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1100">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="6b89e-1101">**TypeRef**要素が関数にパラメーターまたは戻り値の型としてのコレクションがあることを指定するために使用 CollectionType 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1101">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="6b89e-1102">A **TypeRef**要素は、(指定した順序で次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1102">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="6b89e-1103">(0 個または 1 つの要素) のドキュメント</span><span class="sxs-lookup"><span data-stu-id="6b89e-1103">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="6b89e-1104">Annotation 要素 (0 個以上の要素)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1104">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-1105">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-1105">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-1106">次の表に適用できる属性、 **TypeRef**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1106">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="6b89e-1107">なお、 **DefaultValue**、 **MaxLength**、 **FixedLength**、**精度**、**スケール**、 **Unicode**、および**照合**属性にのみ適用されます**EDMSimpleTypes**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1107">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="6b89e-1108">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-1108">Attribute Name</span></span>                                                     | <span data-ttu-id="6b89e-1109">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-1109">Is Required</span></span> | <span data-ttu-id="6b89e-1110">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-1110">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-1111">**Type**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1111">**Type**</span></span>                                                           | <span data-ttu-id="6b89e-1112">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1112">No</span></span>          | <span data-ttu-id="6b89e-1113">参照先の型の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1113">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="6b89e-1114">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1114">**Nullable**</span></span>                                                       | <span data-ttu-id="6b89e-1115">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1115">No</span></span>          | <span data-ttu-id="6b89e-1116">**True** (既定値) または**False**プロパティが null 値を持っているかどうかによって。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1116">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="6b89e-1117">> 複合型プロパティがありますで CSDL v1`Nullable="False"`します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1117">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="6b89e-1118">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1118">**DefaultValue**</span></span>                                                   | <span data-ttu-id="6b89e-1119">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1119">No</span></span>          | <span data-ttu-id="6b89e-1120">プロパティの既定値。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1120">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="6b89e-1121">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1121">**MaxLength**</span></span>                                                      | <span data-ttu-id="6b89e-1122">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1122">No</span></span>          | <span data-ttu-id="6b89e-1123">プロパティ値の最大長。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1123">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="6b89e-1124">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1124">**FixedLength**</span></span>                                                    | <span data-ttu-id="6b89e-1125">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1125">No</span></span>          | <span data-ttu-id="6b89e-1126">**True**または**False**プロパティの値が固定長の文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1126">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="6b89e-1127">**精度**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1127">**Precision**</span></span>                                                      | <span data-ttu-id="6b89e-1128">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1128">No</span></span>          | <span data-ttu-id="6b89e-1129">プロパティ値の有効桁数。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1129">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="6b89e-1130">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1130">**Scale**</span></span>                                                          | <span data-ttu-id="6b89e-1131">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1131">No</span></span>          | <span data-ttu-id="6b89e-1132">プロパティ値の小数点以下桁数。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1132">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="6b89e-1133">**SRID**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1133">**SRID**</span></span>                                                           | <span data-ttu-id="6b89e-1134">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1134">No</span></span>          | <span data-ttu-id="6b89e-1135">システムの空間参照識別子です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1135">Spatial System Reference Identifier.</span></span> <span data-ttu-id="6b89e-1136">空間型のプロパティに対してのみ有効です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1136">Valid only for properties of spatial types.</span></span> <span data-ttu-id="6b89e-1137">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1137">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="6b89e-1138">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1138">**Unicode**</span></span>                                                        | <span data-ttu-id="6b89e-1139">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1139">No</span></span>          | <span data-ttu-id="6b89e-1140">**True**または**False**プロパティの値を Unicode 文字列として格納するかどうかによって異なります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1140">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="6b89e-1141">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1141">**Collation**</span></span>                                                      | <span data-ttu-id="6b89e-1142">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1142">No</span></span>          | <span data-ttu-id="6b89e-1143">データ ソースで使用する照合順序を指定する文字列。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1143">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="6b89e-1144">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **CollectionType**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1144">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="6b89e-1145">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1145">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-1146">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1146">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-1147">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-1147">Example</span></span>

<span data-ttu-id="6b89e-1148">次の例では、使用するモデル定義関数、 **TypeRef**要素 (の子として、 **CollectionType**要素) のコレクションを関数が受け取ることを指定する**部門**エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1148">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

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
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="6b89e-1149">Using 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1149">Using Element (CSDL)</span></span>

<span data-ttu-id="6b89e-1150">**Using**概念スキーマ定義言語 (CSDL) 内の要素が別の名前空間に存在する概念モデルのコンテンツをインポートします。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1150">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="6b89e-1151">値を設定して、 **Namespace**属性、エンティティ型、複合型、および別の概念モデルで定義されているアソシエーション型を参照することができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1151">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="6b89e-1152">1 つ以上**Using**要素の子にすることができます、**スキーマ**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1152">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="6b89e-1153">**Using** CSDL 内の要素と同様に機能しません、**を使用して**のプログラミング言語でステートメント。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1153">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="6b89e-1154">持つ名前空間をインポートすることによって、**を使用して**ステートメントで、プログラミング言語をするには影響しません、元の名前空間内のオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1154">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="6b89e-1155">CSDL では、インポートされた名前空間に、元の名前空間にあるエンティティ型から派生したエンティティ型が含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1155">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="6b89e-1156">これは、元の名前空間で宣言されたエンティティ セットに影響を及ぼすことがあります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1156">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="6b89e-1157">**Using**要素は、次の子要素を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1157">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="6b89e-1158">ドキュメント (0 個または 1 つの要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1158">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="6b89e-1159">Annotation 要素 (0 個以上の要素を許可する場合)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1159">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="6b89e-1160">適用可能な属性</span><span class="sxs-lookup"><span data-stu-id="6b89e-1160">Applicable Attributes</span></span>

<span data-ttu-id="6b89e-1161">次の表は、属性を示しますに適用できる、 **Using**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1161">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="6b89e-1162">属性名</span><span class="sxs-lookup"><span data-stu-id="6b89e-1162">Attribute Name</span></span> | <span data-ttu-id="6b89e-1163">必須</span><span class="sxs-lookup"><span data-stu-id="6b89e-1163">Is Required</span></span> | <span data-ttu-id="6b89e-1164">[値]</span><span class="sxs-lookup"><span data-stu-id="6b89e-1164">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-1165">**名前空間**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1165">**Namespace**</span></span>  | <span data-ttu-id="6b89e-1166">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1166">Yes</span></span>         | <span data-ttu-id="6b89e-1167">インポートされる名前空間の名前。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1167">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="6b89e-1168">**Alias**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1168">**Alias**</span></span>      | <span data-ttu-id="6b89e-1169">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1169">Yes</span></span>         | <span data-ttu-id="6b89e-1170">名前空間の名前の代わりに使用される識別子。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1170">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="6b89e-1171">この属性は必須ですが、オブジェクト名を修飾するために名前空間名の代わりに使用することは必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1171">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="6b89e-1172">Annotation 属性 (カスタム XML 属性) の任意の数に適用されます、 **Using**要素。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1172">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="6b89e-1173">ただし、カスタム属性は CSDL 用に予約されたどの XML 名前空間にも属していない場合があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="6b89e-1174">カスタム属性の完全修飾名は一意である必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="6b89e-1175">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-1175">Example</span></span>

<span data-ttu-id="6b89e-1176">次の例で、 **Using**は名前空間をインポートするために使用されている要素では、別の場所で定義されています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1176">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="6b89e-1177">注意名前空間を**スキーマ**に示す要素が`BooksModel`します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1177">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="6b89e-1178">`Address`プロパティを`Publisher` **EntityType**複合型で定義されているは、`ExtendedBooksModel`名前空間 (と共にインポート、 **Using**要素)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1178">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

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
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="6b89e-1179">annotation 属性 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1179">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="6b89e-1180">概念スキーマ定義言語 (CSDL) の annotation 属性は、概念モデルのカスタム XML 属性です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1180">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="6b89e-1181">有効な XML 構造であることに加え、annotation 属性は次の条件を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1181">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="6b89e-1182">annotation 属性は、CSDL 用に予約された XML 名前空間に存在しない。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1182">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="6b89e-1183">複数の annotation 属性を特定の 1 つの CSDL 要素に適用することができる。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1183">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="6b89e-1184">2 つの任意の annotation 属性の完全修飾名が同じではない。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1184">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="6b89e-1185">annotation 属性は、概念モデルの要素に関する追加のメタデータを提供するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1185">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="6b89e-1186">Annotation 要素に含まれるメタデータは、System.Data.Metadata.Edm 名前空間のクラスを使用して、実行時にアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1186">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="6b89e-1187">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-1187">Example</span></span>

<span data-ttu-id="6b89e-1188">次の例は、 **EntityType** annotation 属性を持つ要素 (**CustomAttribute**)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1188">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="6b89e-1189">また、エンティティ型の要素に適用される annotation 要素も示しています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1189">The example also shows an annotation element applied to the entity type element.</span></span>

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
 

<span data-ttu-id="6b89e-1190">次のコードは、annotation 属性でメタデータを取得し、コンソールに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1190">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

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
 

<span data-ttu-id="6b89e-1191">前のコードでは、`School.csdl` ファイルがプロジェクトの出力ディレクトリにあり、プロジェクトに次の `Imports` および `Using` ステートメントが追加されていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1191">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="6b89e-1192">Annotation 要素 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1192">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="6b89e-1193">概念スキーマ定義言語 (CSDL) の annotation 要素は、概念モデルのカスタム XML 要素です。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1193">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="6b89e-1194">有効な XML 構造が必要であることに加え、annotation 要素は次の条件も満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1194">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="6b89e-1195">annotation 要素は、CSDL 用に予約された XML 名前空間内に存在できません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1195">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="6b89e-1196">複数の annotation 要素を特定の CSDL 要素の子にすることができます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1196">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="6b89e-1197">2 つの annotation 要素の完全修飾名を同じにすることはできません。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1197">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="6b89e-1198">annotation 要素は、特定の CSDL 要素のその他すべての子要素より後に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1198">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="6b89e-1199">annotation 要素は、概念モデルの要素に関する追加のメタデータを提供するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1199">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="6b89e-1200">以降、.NET Framework version 4 では、annotation 要素に含まれるメタデータにアクセスできるランタイム System.Data.Metadata.Edm 名前空間のクラスを使用しています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1200">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="6b89e-1201">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-1201">Example</span></span>

<span data-ttu-id="6b89e-1202">次の例は、 **EntityType** annotation 要素を持つ要素 (**CustomElement**)。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1202">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="6b89e-1203">また、エンティティ型の要素に適用される annotation 属性も示しています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1203">The example also show an annotation attribute applied to the entity type element.</span></span>

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
 

<span data-ttu-id="6b89e-1204">次のコードは、annotation 要素内のメタデータを取得してコンソールに出力します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1204">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

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
 

<span data-ttu-id="6b89e-1205">前のコードでは、School.csdl ファイルがプロジェクトの出力ディレクトリにあり、プロジェクトに次の `Imports` および `Using` ステートメントが追加されていることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1205">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="6b89e-1206">概念モデルの型 (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1206">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="6b89e-1207">概念スキーマ定義言語 (CSDL) と呼ばれる抽象プリミティブ データ型のセットがサポートする**EDMSimpleTypes**、概念モデルのプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1207">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="6b89e-1208">**EDMSimpleTypes**のストレージ環境またはホスティング環境でサポートされるプリミティブ データ型のプロキシします。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1208">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="6b89e-1209">下の表は、CSDL でサポートされるプリミティブ データ型の一覧を示します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1209">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="6b89e-1210">テーブルは、それぞれに適用できるファセットも一覧表示されます。 **EDMSimpleType**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1210">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="6b89e-1211">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="6b89e-1211">EDMSimpleType</span></span>                    | <span data-ttu-id="6b89e-1212">説明</span><span class="sxs-lookup"><span data-stu-id="6b89e-1212">Description</span></span>                                                | <span data-ttu-id="6b89e-1213">使用できるファセット</span><span class="sxs-lookup"><span data-stu-id="6b89e-1213">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="6b89e-1214">**Edm.binary です。**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1214">**Edm.Binary**</span></span>                   | <span data-ttu-id="6b89e-1215">バイナリ データを格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1215">Contains binary data.</span></span>                                      | <span data-ttu-id="6b89e-1216">MaxLength、FixedLength、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1216">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="6b89e-1217">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1217">**Edm.Boolean**</span></span>                  | <span data-ttu-id="6b89e-1218">値を含む**true**または**false**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1218">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="6b89e-1219">Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1219">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="6b89e-1220">**Edm.Byte**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1220">**Edm.Byte**</span></span>                     | <span data-ttu-id="6b89e-1221">符号なし 8 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1221">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="6b89e-1222">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1222">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="6b89e-1223">**Edm.DateTime**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1223">**Edm.DateTime**</span></span>                 | <span data-ttu-id="6b89e-1224">日時を表します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1224">Represents a date and time.</span></span>                                | <span data-ttu-id="6b89e-1225">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1225">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="6b89e-1226">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1226">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="6b89e-1227">GMT からのオフセット (分単位) としての日時を格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1227">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="6b89e-1228">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1228">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="6b89e-1229">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1229">**Edm.Decimal**</span></span>                  | <span data-ttu-id="6b89e-1230">有効桁数と小数点以下桁数が固定長の数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1230">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="6b89e-1231">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1231">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="6b89e-1232">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1232">**Edm.Double**</span></span>                   | <span data-ttu-id="6b89e-1233">持つ浮動小数点数 15 桁の有効桁数が含まれています</span><span class="sxs-lookup"><span data-stu-id="6b89e-1233">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="6b89e-1234">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1234">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="6b89e-1235">**Edm.Float**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1235">**Edm.Float**</span></span>                    | <span data-ttu-id="6b89e-1236">7 桁の有効桁数を持つ浮動小数点数を格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1236">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="6b89e-1237">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1237">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="6b89e-1238">**Edm.Guid**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1238">**Edm.Guid**</span></span>                     | <span data-ttu-id="6b89e-1239">16 バイトの一意識別子を格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1239">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="6b89e-1240">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1240">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="6b89e-1241">**Edm.Int16**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1241">**Edm.Int16**</span></span>                    | <span data-ttu-id="6b89e-1242">符号付き 16 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1242">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="6b89e-1243">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1243">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="6b89e-1244">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1244">**Edm.Int32**</span></span>                    | <span data-ttu-id="6b89e-1245">符号付き 32 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1245">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="6b89e-1246">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1246">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="6b89e-1247">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1247">**Edm.Int64**</span></span>                    | <span data-ttu-id="6b89e-1248">符号付き 64 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1248">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="6b89e-1249">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1249">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="6b89e-1250">**Edm.SByte**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1250">**Edm.SByte**</span></span>                    | <span data-ttu-id="6b89e-1251">符号付き 8 ビット整数値を格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1251">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="6b89e-1252">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1252">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="6b89e-1253">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1253">**Edm.String**</span></span>                   | <span data-ttu-id="6b89e-1254">文字データを格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1254">Contains character data.</span></span>                                   | <span data-ttu-id="6b89e-1255">Unicode、FixedLength、MaxLength、Collation、Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1255">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="6b89e-1256">**Edm.time です。**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1256">**Edm.Time**</span></span>                     | <span data-ttu-id="6b89e-1257">時刻を格納します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1257">Contains a time of day.</span></span>                                    | <span data-ttu-id="6b89e-1258">Precision、Nullable、Default</span><span class="sxs-lookup"><span data-stu-id="6b89e-1258">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="6b89e-1259">**Edm.Geography**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1259">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="6b89e-1260">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1260">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1261">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1261">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="6b89e-1262">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1262">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1263">**Edm.GeographyLineString**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1263">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="6b89e-1264">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1264">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1265">**Edm.GeographyPolygon**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1265">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="6b89e-1266">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1266">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1267">**Edm.GeographyMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1267">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="6b89e-1268">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1268">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1269">**Edm.GeographyMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1269">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="6b89e-1270">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1270">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1271">**Edm.GeographyMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1271">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="6b89e-1272">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1272">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1273">**Edm.GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1273">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="6b89e-1274">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1274">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1275">**Edm.Geometry**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1275">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="6b89e-1276">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1276">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1277">**Edm.GeometryPoint**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1277">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="6b89e-1278">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1278">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1279">**Edm.GeometryLineString**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1279">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="6b89e-1280">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1280">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1281">**Edm.GeometryPolygon**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1281">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="6b89e-1282">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1282">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1283">**Edm.GeometryMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1283">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="6b89e-1284">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1284">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1285">**Edm.GeometryMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1285">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="6b89e-1286">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1286">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1287">**Edm.GeometryMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1287">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="6b89e-1288">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1288">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="6b89e-1289">**Edm.GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1289">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="6b89e-1290">Null を許容すると、既定では、SRID</span><span class="sxs-lookup"><span data-stu-id="6b89e-1290">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="6b89e-1291">ファセット (CSDL)</span><span class="sxs-lookup"><span data-stu-id="6b89e-1291">Facets (CSDL)</span></span>

<span data-ttu-id="6b89e-1292">概念スキーマ定義言語 (CSDL) のファセットは、エンティティ型と複合型のプロパティに対する制約を表します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1292">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="6b89e-1293">ファセットは次の CSDL 要素で XML 属性として使用されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1293">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="6b89e-1294">プロパティ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1294">Property</span></span>
-   <span data-ttu-id="6b89e-1295">TypeRef</span><span class="sxs-lookup"><span data-stu-id="6b89e-1295">TypeRef</span></span>
-   <span data-ttu-id="6b89e-1296">パラメーター</span><span class="sxs-lookup"><span data-stu-id="6b89e-1296">Parameter</span></span>

<span data-ttu-id="6b89e-1297">次の表は、CSDL でサポートされるファセットについて説明しています。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1297">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="6b89e-1298">いずれのファセットもオプションです。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1298">All facets are optional.</span></span> <span data-ttu-id="6b89e-1299">次に示すいくつかのファセットは、概念モデルからデータベースを生成するときに、Entity Framework によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1299">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="6b89e-1300">概念モデルのデータ型については、概念モデルの型 (CSDL) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1300">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="6b89e-1301">ファセット</span><span class="sxs-lookup"><span data-stu-id="6b89e-1301">Facet</span></span>               | <span data-ttu-id="6b89e-1302">説明</span><span class="sxs-lookup"><span data-stu-id="6b89e-1302">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="6b89e-1303">対象</span><span class="sxs-lookup"><span data-stu-id="6b89e-1303">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="6b89e-1304">データベースの生成に使用</span><span class="sxs-lookup"><span data-stu-id="6b89e-1304">Used for the database generation</span></span> | <span data-ttu-id="6b89e-1305">ランタイムで使用</span><span class="sxs-lookup"><span data-stu-id="6b89e-1305">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="6b89e-1306">**照合順序**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1306">**Collation**</span></span>       | <span data-ttu-id="6b89e-1307">プロパティの値に対して比較と順序付け操作を行うときに使用する照合シーケンス (または並べ替え順序) を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1307">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="6b89e-1308">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1308">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="6b89e-1309">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1309">Yes</span></span>                              | <span data-ttu-id="6b89e-1310">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1310">No</span></span>                  |
| <span data-ttu-id="6b89e-1311">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1311">**ConcurrencyMode**</span></span> | <span data-ttu-id="6b89e-1312">プロパティの値をオプティミスティック コンカレンシー チェックに使用することを指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1312">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="6b89e-1313">すべて**EDMSimpleType**プロパティ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1313">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="6b89e-1314">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1314">No</span></span>                               | <span data-ttu-id="6b89e-1315">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1315">Yes</span></span>                 |
| <span data-ttu-id="6b89e-1316">**既定値**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1316">**Default**</span></span>         | <span data-ttu-id="6b89e-1317">インスタンス化で値が指定されない場合のプロパティの既定値を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1317">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="6b89e-1318">すべて**EDMSimpleType**プロパティ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1318">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="6b89e-1319">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1319">Yes</span></span>                              | <span data-ttu-id="6b89e-1320">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1320">Yes</span></span>                 |
| <span data-ttu-id="6b89e-1321">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1321">**FixedLength**</span></span>     | <span data-ttu-id="6b89e-1322">プロパティ値の長さを可変とすることができるかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1322">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="6b89e-1323">**Edm.Binary**、 **Edm.String**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1323">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="6b89e-1324">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1324">Yes</span></span>                              | <span data-ttu-id="6b89e-1325">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1325">No</span></span>                  |
| <span data-ttu-id="6b89e-1326">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1326">**MaxLength**</span></span>       | <span data-ttu-id="6b89e-1327">プロパティ値の最大長を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1327">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="6b89e-1328">**Edm.Binary**、 **Edm.String**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1328">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="6b89e-1329">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1329">Yes</span></span>                              | <span data-ttu-id="6b89e-1330">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1330">No</span></span>                  |
| <span data-ttu-id="6b89e-1331">**Null 許容型**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1331">**Nullable**</span></span>        | <span data-ttu-id="6b89e-1332">プロパティを設定できるかどうかを指定する**null**値。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1332">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="6b89e-1333">すべて**EDMSimpleType**プロパティ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1333">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="6b89e-1334">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1334">Yes</span></span>                              | <span data-ttu-id="6b89e-1335">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1335">Yes</span></span>                 |
| <span data-ttu-id="6b89e-1336">**精度**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1336">**Precision**</span></span>       | <span data-ttu-id="6b89e-1337">型のプロパティの**Decimal**桁に指定できるプロパティの値の数を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1337">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="6b89e-1338">型のプロパティの**時間**、 **DateTime**、および**DateTimeOffset**プロパティ値の秒の小数部の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1338">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="6b89e-1339">**Edm.DateTime**、 **Edm.DateTimeOffset**、 **Edm.Decimal**、 **edm.time です**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1339">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="6b89e-1340">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1340">Yes</span></span>                              | <span data-ttu-id="6b89e-1341">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1341">No</span></span>                  |
| <span data-ttu-id="6b89e-1342">**拡大縮小**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1342">**Scale**</span></span>           | <span data-ttu-id="6b89e-1343">プロパティ値の小数点の右側の桁数を指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1343">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="6b89e-1344">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1344">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="6b89e-1345">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1345">Yes</span></span>                              | <span data-ttu-id="6b89e-1346">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1346">No</span></span>                  |
| <span data-ttu-id="6b89e-1347">**SRID**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1347">**SRID**</span></span>            | <span data-ttu-id="6b89e-1348">システムの空間参照システムの ID を指定します</span><span class="sxs-lookup"><span data-stu-id="6b89e-1348">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="6b89e-1349">詳細については、次を参照してください。 [SRID](http://en.wikipedia.org/wiki/SRID)と[SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1349">For more information, see [SRID](http://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="6b89e-1350">**Edm.Geography、Edm.GeographyPoint、Edm.GeographyLineString、Edm.GeographyPolygon、Edm.GeographyMultiPoint、Edm.GeographyMultiLineString、Edm.GeographyMultiPolygon、Edm.GeographyCollection、Edm.Geometry、Edm.GeometryPoint、Edm.GeometryLineString、Edm.GeometryPolygon、Edm.GeometryMultiPoint、Edm.GeometryMultiLineString、Edm.GeometryMultiPolygon、Edm.GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1350">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="6b89e-1351">いいえ</span><span class="sxs-lookup"><span data-stu-id="6b89e-1351">No</span></span>                               | <span data-ttu-id="6b89e-1352">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1352">Yes</span></span>                 |
| <span data-ttu-id="6b89e-1353">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1353">**Unicode**</span></span>         | <span data-ttu-id="6b89e-1354">プロパティ値を Unicode として保存するかどうかを指定します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1354">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="6b89e-1355">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="6b89e-1355">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="6b89e-1356">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1356">Yes</span></span>                              | <span data-ttu-id="6b89e-1357">はい</span><span class="sxs-lookup"><span data-stu-id="6b89e-1357">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="6b89e-1358">データベース生成ウィザードがの値を認識する概念モデルからデータベースを生成するときに、 **StoreGeneratedPattern**属性を**プロパティ**場合は、次の要素名前空間: http://schemas.microsoft.com/ado/2009/02/edm/annotation します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1358">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: http://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="6b89e-1359">属性のサポートされる値は**Identity**と**計算済み**します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1359">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="6b89e-1360">値**Identity**データベースで生成される id 値を持つデータベース列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1360">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="6b89e-1361">値**計算済み**データベースで計算される値を持つ列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1361">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="6b89e-1362">例</span><span class="sxs-lookup"><span data-stu-id="6b89e-1362">Example</span></span>

<span data-ttu-id="6b89e-1363">エンティティ型のプロパティに適用されるファセットの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="6b89e-1363">The following example shows facets applied to the properties of an entity type:</span></span>

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
