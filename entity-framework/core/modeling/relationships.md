---
title: リレーションシップ-EF Core
description: Entity Framework Core を使用するときにエンティティ型間のリレーションシップを構成する方法
author: AndriySvyryd
ms.date: 11/21/2019
uid: core/modeling/relationships
ms.openlocfilehash: 452169c902d56fda0a65a5c2846a9b42c80640fd
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824765"
---
# <a name="relationships"></a><span data-ttu-id="6e611-103">リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="6e611-103">Relationships</span></span>

<span data-ttu-id="6e611-104">リレーションシップは、2つのエンティティが相互にどのように関連しているかを定義します。</span><span class="sxs-lookup"><span data-stu-id="6e611-104">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="6e611-105">リレーショナルデータベースでは、これは foreign key 制約によって表されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-105">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="6e611-106">この記事のほとんどのサンプルでは、一対多の関係を使用して概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="6e611-106">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="6e611-107">一対一および多対多リレーションシップの例については、記事の最後にある「[その他のリレーションシップパターン](#other-relationship-patterns)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e611-107">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="6e611-108">用語の定義</span><span class="sxs-lookup"><span data-stu-id="6e611-108">Definition of terms</span></span>

<span data-ttu-id="6e611-109">リレーションシップの説明に使用される用語は多数あります。</span><span class="sxs-lookup"><span data-stu-id="6e611-109">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="6e611-110">**依存エンティティ:** これは、外部キープロパティを含むエンティティです。</span><span class="sxs-lookup"><span data-stu-id="6e611-110">**Dependent entity:** This is the entity that contains the foreign key properties.</span></span> <span data-ttu-id="6e611-111">リレーションシップの "子" と呼ばれることもあります。</span><span class="sxs-lookup"><span data-stu-id="6e611-111">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="6e611-112">**プリンシパルエンティティ:** これは、主キーと代替キーのプロパティを含むエンティティです。</span><span class="sxs-lookup"><span data-stu-id="6e611-112">**Principal entity:** This is the entity that contains the primary/alternate key properties.</span></span> <span data-ttu-id="6e611-113">リレーションシップの "親" と呼ばれることもあります。</span><span class="sxs-lookup"><span data-stu-id="6e611-113">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="6e611-114">**外部キー:** 関連エンティティのプリンシパルキー値を格納するために使用される、依存エンティティ内のプロパティ。</span><span class="sxs-lookup"><span data-stu-id="6e611-114">**Foreign key:** The properties in the dependent entity that are used to store the principal key values for the related entity.</span></span>

* <span data-ttu-id="6e611-115">**プリンシパルキー:** プリンシパルエンティティを一意に識別するプロパティです。</span><span class="sxs-lookup"><span data-stu-id="6e611-115">**Principal key:** The properties that uniquely identify the principal entity.</span></span> <span data-ttu-id="6e611-116">これは、主キーまたは代替キーの場合があります。</span><span class="sxs-lookup"><span data-stu-id="6e611-116">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="6e611-117">**ナビゲーションプロパティ:** 関連エンティティを参照するプリンシパルエンティティまたは依存エンティティで定義されたプロパティ。</span><span class="sxs-lookup"><span data-stu-id="6e611-117">**Navigation property:** A property defined on the principal and/or dependent entity that references the related entity.</span></span>

  * <span data-ttu-id="6e611-118">**コレクションナビゲーションプロパティ:** 関連する多くのエンティティへの参照を格納するナビゲーションプロパティ。</span><span class="sxs-lookup"><span data-stu-id="6e611-118">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="6e611-119">**参照ナビゲーションプロパティ:** 単一の関連エンティティへの参照を保持するナビゲーションプロパティ。</span><span class="sxs-lookup"><span data-stu-id="6e611-119">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="6e611-120">**逆ナビゲーションプロパティ:** この用語は、特定のナビゲーションプロパティを説明するときに、リレーションシップのもう一方の端のナビゲーションプロパティを参照します。</span><span class="sxs-lookup"><span data-stu-id="6e611-120">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>
  
* <span data-ttu-id="6e611-121">**自己参照リレーションシップ:** 依存エンティティ型とプリンシパルエンティティ型が同じリレーションシップ。</span><span class="sxs-lookup"><span data-stu-id="6e611-121">**Self-referencing relationship:** A relationship in which the dependent and the principal entity types are the same.</span></span>

<span data-ttu-id="6e611-122">次のコードは、`Blog` との間の一対多のリレーションシップを示してい `Post`</span><span class="sxs-lookup"><span data-stu-id="6e611-122">The following code shows a one-to-many relationship between `Blog` and `Post`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Entities)]

* <span data-ttu-id="6e611-123">`Post` が依存エンティティである</span><span class="sxs-lookup"><span data-stu-id="6e611-123">`Post` is the dependent entity</span></span>

* <span data-ttu-id="6e611-124">`Blog` はプリンシパルエンティティです。</span><span class="sxs-lookup"><span data-stu-id="6e611-124">`Blog` is the principal entity</span></span>

* <span data-ttu-id="6e611-125">`Post.BlogId` は外部キーです。</span><span class="sxs-lookup"><span data-stu-id="6e611-125">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="6e611-126">`Blog.BlogId` がプリンシパルキー (この場合は、代替キーではなく主キー) です。</span><span class="sxs-lookup"><span data-stu-id="6e611-126">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="6e611-127">`Post.Blog` は参照ナビゲーションプロパティです</span><span class="sxs-lookup"><span data-stu-id="6e611-127">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="6e611-128">`Blog.Posts` はコレクションナビゲーションプロパティです。</span><span class="sxs-lookup"><span data-stu-id="6e611-128">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="6e611-129">`Post.Blog` は `Blog.Posts` の逆ナビゲーションプロパティ (およびその逆) です。</span><span class="sxs-lookup"><span data-stu-id="6e611-129">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

## <a name="conventions"></a><span data-ttu-id="6e611-130">規約</span><span class="sxs-lookup"><span data-stu-id="6e611-130">Conventions</span></span>

<span data-ttu-id="6e611-131">既定では、型に対してナビゲーションプロパティが検出されると、リレーションシップが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-131">By default, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="6e611-132">プロパティは、参照先の型が現在のデータベースプロバイダーによってスカラー型としてマップされていない場合、ナビゲーションプロパティと見なされます。</span><span class="sxs-lookup"><span data-stu-id="6e611-132">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="6e611-133">規則によって検出されたリレーションシップは、常にプリンシパルエンティティの主キーを対象とします。</span><span class="sxs-lookup"><span data-stu-id="6e611-133">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="6e611-134">代替キーをターゲットにするには、Fluent API を使用して追加の構成を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e611-134">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="6e611-135">完全に定義されたリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="6e611-135">Fully defined relationships</span></span>

<span data-ttu-id="6e611-136">リレーションシップの最も一般的なパターンは、リレーションシップの両端と、依存エンティティクラスで定義されている外部キープロパティの両方でナビゲーションプロパティを定義することです。</span><span class="sxs-lookup"><span data-stu-id="6e611-136">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="6e611-137">2つの型の間にナビゲーションプロパティのペアが見つかった場合、それらは同じリレーションシップの逆ナビゲーションプロパティとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-137">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="6e611-138">依存エンティティに、次のパターンのいずれかの名前を持つプロパティが含まれている場合は、外部キーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-138">If the dependent entity contains a property with a name mathing one of these patterns then it will be configured as the foreign key:</span></span>
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

<span data-ttu-id="6e611-139">この例では、強調表示されたプロパティを使用してリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="6e611-139">In this example the highlighted properties will be used to configure the relationship.</span></span>

> [!NOTE]
> <span data-ttu-id="6e611-140">プロパティが主キーであるか、またはプリンシパルキーと互換性のない型である場合、外部キーとして構成されません。</span><span class="sxs-lookup"><span data-stu-id="6e611-140">If the property is the primary key or is of a type not compatible with the principal key then it won't be configured as the foreign key.</span></span>

> [!NOTE]
> <span data-ttu-id="6e611-141">3\.0 より EF Core 前では、プリンシパルキープロパティとまったく同じ名前のプロパティが、[外部キーとも一致](https://github.com/aspnet/EntityFrameworkCore/issues/13274)していました。</span><span class="sxs-lookup"><span data-stu-id="6e611-141">Before EF Core 3.0 the property named exactly the same as the principal key property [was also matched as the foreign key](https://github.com/aspnet/EntityFrameworkCore/issues/13274)</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="6e611-142">外部キープロパティがありません</span><span class="sxs-lookup"><span data-stu-id="6e611-142">No foreign key property</span></span>

<span data-ttu-id="6e611-143">依存エンティティクラスで外部キープロパティを定義することをお勧めしますが、必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="6e611-143">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="6e611-144">外部キープロパティが見つからない場合は、`<navigation property name><principal key property name>` という名前の[シャドウ外部キープロパティ](shadow-properties.md)が導入されます。依存する型にナビゲーションが存在しない場合は `<principal entity name><principal key property name>` ます。</span><span class="sxs-lookup"><span data-stu-id="6e611-144">If no foreign key property is found, a [shadow foreign key property](shadow-properties.md) will be introduced with the name `<navigation property name><principal key property name>` or `<principal entity name><principal key property name>` if no navigation is present on the dependent type.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

<span data-ttu-id="6e611-145">この例では、ナビゲーション名の前に冗長があるため、シャドウ外部キーが `BlogId` ます。</span><span class="sxs-lookup"><span data-stu-id="6e611-145">In this example the shadow foreign key is `BlogId` because prepending the navigation name would be redundant.</span></span>

> [!NOTE]
> <span data-ttu-id="6e611-146">同じ名前のプロパティが既に存在する場合は、シャドウプロパティ名の後に数字が付加されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-146">If a property with the same name already exists then the shadow property name will be suffixed with a number.</span></span>

### <a name="single-navigation-property"></a><span data-ttu-id="6e611-147">単一ナビゲーションプロパティ</span><span class="sxs-lookup"><span data-stu-id="6e611-147">Single navigation property</span></span>

<span data-ttu-id="6e611-148">ナビゲーションプロパティを1つだけ含め (逆のナビゲーションも、外部キープロパティも含まない)、規約によってリレーションシップを定義するには十分です。</span><span class="sxs-lookup"><span data-stu-id="6e611-148">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="6e611-149">また、1つのナビゲーションプロパティと外部キープロパティを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="6e611-149">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="limitations"></a><span data-ttu-id="6e611-150">制限事項</span><span class="sxs-lookup"><span data-stu-id="6e611-150">Limitations</span></span>

<span data-ttu-id="6e611-151">2つの型の間に複数のナビゲーションプロパティが定義されている場合 (つまり、互いをポイントするナビゲーションの1対のペアだけを超えている場合)、ナビゲーションプロパティによって表されるリレーションシップがあいまいになります。</span><span class="sxs-lookup"><span data-stu-id="6e611-151">When there are multiple navigation properties defined between two types (that is, more than just one pair of navigations that point to each other) the relationships represented by the navigation properties are ambiguous.</span></span> <span data-ttu-id="6e611-152">あいまいさを解決するには、手動で構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e611-152">You will need to manually configure them to resolve the ambiguity.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="6e611-153">連鎖削除</span><span class="sxs-lookup"><span data-stu-id="6e611-153">Cascade delete</span></span>

<span data-ttu-id="6e611-154">慣例により、cascade delete は必要なリレーションシップの場合は*cascade*に、オプションのリレーションシップの場合は*clientsetnull*に設定されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-154">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="6e611-155">*Cascade*は、依存エンティティも削除されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="6e611-155">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="6e611-156">*Clientsetnull*は、メモリに読み込まれていない依存エンティティは変更されず、手動で削除するか、有効なプリンシパルエンティティを指すように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e611-156">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="6e611-157">メモリに読み込まれるエンティティの場合、EF Core は外部キーのプロパティを null に設定しようとします。</span><span class="sxs-lookup"><span data-stu-id="6e611-157">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="6e611-158">必須リレーションシップとオプションリレーションシップの違いについては、「[必須リレーションシップ」と「オプションのリレーションシップ](#required-and-optional-relationships)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e611-158">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="6e611-159">さまざまな削除動作と規約で使用される既定値の詳細については、「 [Cascade delete](../saving/cascade-delete.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e611-159">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="manual-configuration"></a><span data-ttu-id="6e611-160">手動構成</span><span class="sxs-lookup"><span data-stu-id="6e611-160">Manual configuration</span></span>

#### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="6e611-161">Fluent API</span><span class="sxs-lookup"><span data-stu-id="6e611-161">Fluent API</span></span>](#tab/fluent-api)

<span data-ttu-id="6e611-162">Fluent API でリレーションシップを構成するには、まず、リレーションシップを構成するナビゲーションプロパティを特定します。</span><span class="sxs-lookup"><span data-stu-id="6e611-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="6e611-163">`HasOne` または `HasMany` によって、構成を開始するエンティティ型のナビゲーションプロパティが識別されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="6e611-164">次に、`WithOne` または `WithMany` への呼び出しを連結して、逆のナビゲーションを識別します。</span><span class="sxs-lookup"><span data-stu-id="6e611-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="6e611-165">`HasOne`/`WithOne` は参照ナビゲーションプロパティに使用され、`HasMany`/`WithMany` コレクションのナビゲーションプロパティに使用されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?highlight=14-16)]

#### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="6e611-166">データの注釈</span><span class="sxs-lookup"><span data-stu-id="6e611-166">Data annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="6e611-167">データ注釈を使用すると、依存エンティティとプリンシパルエンティティのナビゲーションプロパティをどのように組み合わせるかを構成できます。</span><span class="sxs-lookup"><span data-stu-id="6e611-167">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="6e611-168">これは通常、2つのエンティティ型の間に複数のナビゲーションプロパティのペアがある場合に実行されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-168">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?highlight=33,36)]

> [!NOTE]
> <span data-ttu-id="6e611-169">依存エンティティのプロパティに対してのみ [必須] を使用すると、リレーションシップの requiredness に影響を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="6e611-169">You can only use [Required] on properties on the dependent entity to impact the requiredness of the relationship.</span></span> <span data-ttu-id="6e611-170">[必須] プリンシパルエンティティからのナビゲーションは通常は無視されますが、エンティティが依存するエンティティになる場合があります。</span><span class="sxs-lookup"><span data-stu-id="6e611-170">[Required] on the navigation from the principal entity is usually ignored, but it may cause the entity to become the dependent one.</span></span>

> [!NOTE]
> <span data-ttu-id="6e611-171">データ注釈 `[ForeignKey]` と `[InverseProperty]` は `System.ComponentModel.DataAnnotations.Schema` 名前空間で使用できます。</span><span class="sxs-lookup"><span data-stu-id="6e611-171">The data annotations `[ForeignKey]` and `[InverseProperty]` are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span> <span data-ttu-id="6e611-172">`[Required]` は、`System.ComponentModel.DataAnnotations` 名前空間で使用できます。</span><span class="sxs-lookup"><span data-stu-id="6e611-172">`[Required]` is available in the `System.ComponentModel.DataAnnotations` namespace.</span></span>

---

### <a name="single-navigation-property"></a><span data-ttu-id="6e611-173">単一ナビゲーションプロパティ</span><span class="sxs-lookup"><span data-stu-id="6e611-173">Single navigation property</span></span>

<span data-ttu-id="6e611-174">ナビゲーションプロパティが1つしかない場合は、`WithOne` と `WithMany`のパラメーターなしのオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="6e611-174">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="6e611-175">これは、リレーションシップのもう一方の端には概念的に参照またはコレクションが存在するが、エンティティクラスにはナビゲーションプロパティが含まれていないことを示します。</span><span class="sxs-lookup"><span data-stu-id="6e611-175">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?highlight=14-16)]

### <a name="foreign-key"></a><span data-ttu-id="6e611-176">外部キー</span><span class="sxs-lookup"><span data-stu-id="6e611-176">Foreign key</span></span>

#### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="6e611-177">Fluent API</span><span class="sxs-lookup"><span data-stu-id="6e611-177">Fluent API</span></span>](#tab/fluent-api)

<span data-ttu-id="6e611-178">Fluent API を使用して、特定のリレーションシップの外部キープロパティとして使用するプロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="6e611-178">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?highlight=17)]

#### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="6e611-179">データの注釈</span><span class="sxs-lookup"><span data-stu-id="6e611-179">Data annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="6e611-180">データ注釈を使用して、特定のリレーションシップの外部キープロパティとして使用するプロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="6e611-180">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="6e611-181">これは通常、外部キープロパティが規則によって検出されない場合に実行されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-181">This is typically done when the foreign key property is not discovered by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?highlight=30)]

> [!TIP]  
> <span data-ttu-id="6e611-182">`[ForeignKey]` 注釈は、リレーションシップのいずれかのナビゲーションプロパティに配置できます。</span><span class="sxs-lookup"><span data-stu-id="6e611-182">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="6e611-183">依存エンティティクラスのナビゲーションプロパティに移動する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6e611-183">It does not need to go on the navigation property in the dependent entity class.</span></span>

> [!NOTE]
> <span data-ttu-id="6e611-184">ナビゲーションプロパティで `[ForeignKey]` を使用して指定されたプロパティは、依存する型を存在する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6e611-184">The property specified using `[ForeignKey]` on a navigation property doesn't need to exist the the dependent type.</span></span> <span data-ttu-id="6e611-185">この場合、指定した名前を使用して、シャドウ外部キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-185">In this case the specified name will be used to create a shadow foreign key.</span></span>

---

<span data-ttu-id="6e611-186">次のコードは、複合外部キーを構成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6e611-186">The following code shows how to configure a composite foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?highlight=20)]

<span data-ttu-id="6e611-187">`HasForeignKey(...)` の文字列オーバーロードを使用して、shadow プロパティを外部キーとして構成できます (詳細については、「 [Shadow Properties](shadow-properties.md) 」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="6e611-187">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="6e611-188">次に示すように、外部キーとして使用する前に、シャドウプロパティをモデルに明示的に追加することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="6e611-188">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="without-navigation-property"></a><span data-ttu-id="6e611-189">ナビゲーションプロパティなし</span><span class="sxs-lookup"><span data-stu-id="6e611-189">Without navigation property</span></span>

<span data-ttu-id="6e611-190">必ずしもナビゲーションプロパティを指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="6e611-190">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="6e611-191">リレーションシップの一方の側に外部キーを指定するだけです。</span><span class="sxs-lookup"><span data-stu-id="6e611-191">You can simply provide a foreign key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?highlight=14-17)]

### <a name="principal-key"></a><span data-ttu-id="6e611-192">プリンシパルキー</span><span class="sxs-lookup"><span data-stu-id="6e611-192">Principal key</span></span>

<span data-ttu-id="6e611-193">外部キーで主キー以外のプロパティを参照する場合は、Fluent API を使用してリレーションシップのプリンシパルキープロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="6e611-193">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="6e611-194">プリンシパルキーとして構成したプロパティは、自動的に[代替キー](alternate-keys.md)として設定されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-194">The property that you configure as the principal key will automatically be setup as an [alternate key](alternate-keys.md).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

<span data-ttu-id="6e611-195">次のコードは、複合プリンシパルキーを構成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6e611-195">The following code shows how to configure a composite principal key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=Composite&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="6e611-196">プリンシパルキープロパティを指定する順序は、外部キーに指定されている順序と一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e611-196">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

### <a name="required-and-optional-relationships"></a><span data-ttu-id="6e611-197">必須およびオプションのリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="6e611-197">Required and optional relationships</span></span>

<span data-ttu-id="6e611-198">Fluent API を使用して、リレーションシップが必須か省略可能かを構成できます。</span><span class="sxs-lookup"><span data-stu-id="6e611-198">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="6e611-199">これは最終的に、外部キープロパティが必須か省略可能かを制御します。</span><span class="sxs-lookup"><span data-stu-id="6e611-199">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="6e611-200">これは、シャドウ状態の外部キーを使用している場合に最も役立ちます。</span><span class="sxs-lookup"><span data-stu-id="6e611-200">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="6e611-201">エンティティクラスに外部キープロパティがある場合、リレーションシップの requiredness は、外部キープロパティが必須か省略可能かに基づいて決定されます (詳細については、[必須プロパティと省略可能なプロパティ](required-optional.md)を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="6e611-201">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=11)]

> [!NOTE]
> <span data-ttu-id="6e611-202">`IsRequired(false)` を呼び出すと、他の方法で構成しない限り、外部キープロパティも省略可能になります。</span><span class="sxs-lookup"><span data-stu-id="6e611-202">Calling `IsRequired(false)` also makes the foreign key property optional unless it's configured otherwise.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="6e611-203">連鎖削除</span><span class="sxs-lookup"><span data-stu-id="6e611-203">Cascade delete</span></span>

<span data-ttu-id="6e611-204">Fluent API を使用して、特定のリレーションシップに対して連鎖削除動作を明示的に構成することができます。</span><span class="sxs-lookup"><span data-stu-id="6e611-204">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="6e611-205">各オプションの詳細については、「[連鎖削除](../saving/cascade-delete.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6e611-205">See [Cascade Delete](../saving/cascade-delete.md) for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=11)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="6e611-206">その他のリレーションシップパターン</span><span class="sxs-lookup"><span data-stu-id="6e611-206">Other relationship patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="6e611-207">一対一</span><span class="sxs-lookup"><span data-stu-id="6e611-207">One-to-one</span></span>

<span data-ttu-id="6e611-208">一対一のリレーションシップには、両側の参照ナビゲーションプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="6e611-208">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="6e611-209">これらは一対多のリレーションシップと同じ規則に従いますが、依存関係が各プリンシパルに関連付けられるように、外部キープロパティに一意のインデックスが導入されます。</span><span class="sxs-lookup"><span data-stu-id="6e611-209">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=Property&highlight=6,15,16)]

> [!NOTE]  
> <span data-ttu-id="6e611-210">EF は、外部キープロパティを検出する機能に基づいて、依存するエンティティの1つを選択します。</span><span class="sxs-lookup"><span data-stu-id="6e611-210">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="6e611-211">依存関係として間違ったエンティティが選択されている場合は、Fluent API を使用してこれを修正できます。</span><span class="sxs-lookup"><span data-stu-id="6e611-211">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="6e611-212">Fluent API との関係を構成する場合は、`HasOne` メソッドと `WithOne` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="6e611-212">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="6e611-213">外部キーを構成するときに、依存エンティティ型を指定する必要があります。次の一覧で `HasForeignKey` に提供されているジェネリックパラメーターに注意してください。</span><span class="sxs-lookup"><span data-stu-id="6e611-213">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="6e611-214">一対多のリレーションシップでは、参照ナビゲーションを持つエンティティが依存しており、コレクションを持つエンティティがプリンシパルであることが明らかです。</span><span class="sxs-lookup"><span data-stu-id="6e611-214">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="6e611-215">ただし、これは一対一のリレーションシップではないため、明示的に定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6e611-215">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a><span data-ttu-id="6e611-216">多対多</span><span class="sxs-lookup"><span data-stu-id="6e611-216">Many-to-many</span></span>

<span data-ttu-id="6e611-217">結合テーブルを表すエンティティクラスのない多対多リレーションシップは、まだサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="6e611-217">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="6e611-218">ただし、結合テーブルのエンティティクラスを含め、2つの個別の一対多リレーションシップをマップすることで、多対多リレーションシップを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="6e611-218">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)]
