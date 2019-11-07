---
title: リレーションシップ-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
uid: core/modeling/relationships
ms.openlocfilehash: 1e59ce9e19c12aa5564bc8467dcfcb3be8ee8996
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655669"
---
# <a name="relationships"></a><span data-ttu-id="7285e-102">リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="7285e-102">Relationships</span></span>

<span data-ttu-id="7285e-103">リレーションシップは、2つのエンティティが相互にどのように関連しているかを定義します。</span><span class="sxs-lookup"><span data-stu-id="7285e-103">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="7285e-104">リレーショナルデータベースでは、これは foreign key 制約によって表されます。</span><span class="sxs-lookup"><span data-stu-id="7285e-104">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="7285e-105">この記事のほとんどのサンプルでは、一対多の関係を使用して概念を示しています。</span><span class="sxs-lookup"><span data-stu-id="7285e-105">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="7285e-106">一対一および多対多リレーションシップの例については、記事の最後にある「[その他のリレーションシップパターン](#other-relationship-patterns)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7285e-106">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="7285e-107">用語の定義</span><span class="sxs-lookup"><span data-stu-id="7285e-107">Definition of Terms</span></span>

<span data-ttu-id="7285e-108">リレーションシップの説明に使用される用語は多数あります。</span><span class="sxs-lookup"><span data-stu-id="7285e-108">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="7285e-109">**依存エンティティ:** これは、外部キープロパティを含むエンティティです。</span><span class="sxs-lookup"><span data-stu-id="7285e-109">**Dependent entity:** This is the entity that contains the foreign key property(s).</span></span> <span data-ttu-id="7285e-110">リレーションシップの "子" と呼ばれることもあります。</span><span class="sxs-lookup"><span data-stu-id="7285e-110">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="7285e-111">**プリンシパルエンティティ:** これは、主キーと代替キーのプロパティを含むエンティティです。</span><span class="sxs-lookup"><span data-stu-id="7285e-111">**Principal entity:** This is the entity that contains the primary/alternate key property(s).</span></span> <span data-ttu-id="7285e-112">リレーションシップの "親" と呼ばれることもあります。</span><span class="sxs-lookup"><span data-stu-id="7285e-112">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="7285e-113">**外部キー:** エンティティが関連付けられているプリンシパルキープロパティの値を格納するために使用される、依存エンティティ内のプロパティ。</span><span class="sxs-lookup"><span data-stu-id="7285e-113">**Foreign key:** The property(s) in the dependent entity that is used to store the values of the principal key property that the entity is related to.</span></span>

* <span data-ttu-id="7285e-114">**プリンシパルキー:** プリンシパルエンティティを一意に識別するプロパティ。</span><span class="sxs-lookup"><span data-stu-id="7285e-114">**Principal key:** The property(s) that uniquely identifies the principal entity.</span></span> <span data-ttu-id="7285e-115">これは、主キーまたは代替キーの場合があります。</span><span class="sxs-lookup"><span data-stu-id="7285e-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="7285e-116">**ナビゲーションプロパティ:** 関連エンティティへの参照を含む、プリンシパルエンティティまたは依存エンティティで定義されたプロパティ。</span><span class="sxs-lookup"><span data-stu-id="7285e-116">**Navigation property:** A property defined on the principal and/or dependent entity that contains a reference(s) to the related entity(s).</span></span>

  * <span data-ttu-id="7285e-117">**コレクションナビゲーションプロパティ:** 関連する多くのエンティティへの参照を格納するナビゲーションプロパティ。</span><span class="sxs-lookup"><span data-stu-id="7285e-117">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="7285e-118">**参照ナビゲーションプロパティ:** 単一の関連エンティティへの参照を保持するナビゲーションプロパティ。</span><span class="sxs-lookup"><span data-stu-id="7285e-118">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="7285e-119">**逆ナビゲーションプロパティ:** この用語は、特定のナビゲーションプロパティを説明するときに、リレーションシップのもう一方の端のナビゲーションプロパティを参照します。</span><span class="sxs-lookup"><span data-stu-id="7285e-119">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>

<span data-ttu-id="7285e-120">次のコードリストは、`Blog` との間の一対多リレーションシップを示してい `Post`</span><span class="sxs-lookup"><span data-stu-id="7285e-120">The following code listing shows a one-to-many relationship between `Blog` and `Post`</span></span>

* <span data-ttu-id="7285e-121">`Post` が依存エンティティである</span><span class="sxs-lookup"><span data-stu-id="7285e-121">`Post` is the dependent entity</span></span>

* <span data-ttu-id="7285e-122">`Blog` はプリンシパルエンティティです。</span><span class="sxs-lookup"><span data-stu-id="7285e-122">`Blog` is the principal entity</span></span>

* <span data-ttu-id="7285e-123">`Post.BlogId` は外部キーです。</span><span class="sxs-lookup"><span data-stu-id="7285e-123">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="7285e-124">`Blog.BlogId` がプリンシパルキー (この場合は、代替キーではなく主キー) です。</span><span class="sxs-lookup"><span data-stu-id="7285e-124">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="7285e-125">`Post.Blog` は参照ナビゲーションプロパティです</span><span class="sxs-lookup"><span data-stu-id="7285e-125">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="7285e-126">`Blog.Posts` はコレクションナビゲーションプロパティです。</span><span class="sxs-lookup"><span data-stu-id="7285e-126">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="7285e-127">`Post.Blog` は `Blog.Posts` の逆ナビゲーションプロパティ (およびその逆) です。</span><span class="sxs-lookup"><span data-stu-id="7285e-127">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Entities)]

## <a name="conventions"></a><span data-ttu-id="7285e-128">規約</span><span class="sxs-lookup"><span data-stu-id="7285e-128">Conventions</span></span>

<span data-ttu-id="7285e-129">規則により、型で検出されたナビゲーションプロパティがある場合に、リレーションシップが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7285e-129">By convention, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="7285e-130">プロパティは、参照先の型が現在のデータベースプロバイダーによってスカラー型としてマップされていない場合、ナビゲーションプロパティと見なされます。</span><span class="sxs-lookup"><span data-stu-id="7285e-130">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="7285e-131">規則によって検出されたリレーションシップは、常にプリンシパルエンティティの主キーを対象とします。</span><span class="sxs-lookup"><span data-stu-id="7285e-131">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="7285e-132">代替キーをターゲットにするには、Fluent API を使用して追加の構成を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7285e-132">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="7285e-133">完全に定義されたリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="7285e-133">Fully Defined Relationships</span></span>

<span data-ttu-id="7285e-134">リレーションシップの最も一般的なパターンは、リレーションシップの両端と、依存エンティティクラスで定義されている外部キープロパティの両方でナビゲーションプロパティを定義することです。</span><span class="sxs-lookup"><span data-stu-id="7285e-134">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="7285e-135">2つの型の間にナビゲーションプロパティのペアが見つかった場合、それらは同じリレーションシップの逆ナビゲーションプロパティとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="7285e-135">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="7285e-136">依存エンティティに `<primary key property name>`、`<navigation property name><primary key property name>`、または `<principal entity name><primary key property name>` という名前のプロパティが含まれている場合は、外部キーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="7285e-136">If the dependent entity contains a property named `<primary key property name>`, `<navigation property name><primary key property name>`, or `<principal entity name><primary key property name>` then it will be configured as the foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> <span data-ttu-id="7285e-137">2つの型の間に複数のナビゲーションプロパティが定義されている場合 (つまり、互いをポイントするナビゲーションの複数の異なるペア)、規則によってリレーションシップは作成されず、手動でこれらのプロパティを構成して、ナビゲーションプロパティが上にあります。</span><span class="sxs-lookup"><span data-stu-id="7285e-137">If there are multiple navigation properties defined between two types (that is, more than one distinct pair of navigations that point to each other), then no relationships will be created by convention and you will need to manually configure them to identify how the navigation properties pair up.</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="7285e-138">外部キープロパティがありません</span><span class="sxs-lookup"><span data-stu-id="7285e-138">No Foreign Key Property</span></span>

<span data-ttu-id="7285e-139">依存エンティティクラスで外部キープロパティを定義することをお勧めしますが、必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="7285e-139">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="7285e-140">外部キープロパティが見つからない場合は、`<navigation property name><principal key property name>` という名前のシャドウ外部キープロパティが導入されます (詳細については、「[シャドウプロパティ](shadow-properties.md)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="7285e-140">If no foreign key property is found, a shadow foreign key property will be introduced with the name `<navigation property name><principal key property name>` (see [Shadow Properties](shadow-properties.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a><span data-ttu-id="7285e-141">単一ナビゲーションプロパティ</span><span class="sxs-lookup"><span data-stu-id="7285e-141">Single Navigation Property</span></span>

<span data-ttu-id="7285e-142">ナビゲーションプロパティを1つだけ含め (逆のナビゲーションも、外部キープロパティも含まない)、規約によってリレーションシップを定義するには十分です。</span><span class="sxs-lookup"><span data-stu-id="7285e-142">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="7285e-143">また、1つのナビゲーションプロパティと外部キープロパティを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="7285e-143">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a><span data-ttu-id="7285e-144">連鎖削除</span><span class="sxs-lookup"><span data-stu-id="7285e-144">Cascade Delete</span></span>

<span data-ttu-id="7285e-145">慣例により、cascade delete は必要なリレーションシップの場合は*cascade*に、オプションのリレーションシップの場合は*clientsetnull*に設定されます。</span><span class="sxs-lookup"><span data-stu-id="7285e-145">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="7285e-146">*Cascade*は、依存エンティティも削除されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="7285e-146">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="7285e-147">*Clientsetnull*は、メモリに読み込まれていない依存エンティティは変更されず、手動で削除するか、有効なプリンシパルエンティティを指すように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7285e-147">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="7285e-148">メモリに読み込まれるエンティティの場合、EF Core は外部キーのプロパティを null に設定しようとします。</span><span class="sxs-lookup"><span data-stu-id="7285e-148">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="7285e-149">必須リレーションシップとオプションリレーションシップの違いについては、「[必須リレーションシップ」と「オプションのリレーションシップ](#required-and-optional-relationships)」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="7285e-149">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="7285e-150">さまざまな削除動作と規約で使用される既定値の詳細については、「 [Cascade delete](../saving/cascade-delete.md) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7285e-150">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="7285e-151">データの注釈</span><span class="sxs-lookup"><span data-stu-id="7285e-151">Data Annotations</span></span>

<span data-ttu-id="7285e-152">リレーションシップの構成に使用できるデータ注釈には、`[ForeignKey]` と `[InverseProperty]`の2つがあります。</span><span class="sxs-lookup"><span data-stu-id="7285e-152">There are two data annotations that can be used to configure relationships, `[ForeignKey]` and `[InverseProperty]`.</span></span> <span data-ttu-id="7285e-153">これらは、`System.ComponentModel.DataAnnotations.Schema` 名前空間で使用できます。</span><span class="sxs-lookup"><span data-stu-id="7285e-153">These are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span>

### <a name="foreignkey"></a><span data-ttu-id="7285e-154">[ForeignKey]</span><span class="sxs-lookup"><span data-stu-id="7285e-154">[ForeignKey]</span></span>

<span data-ttu-id="7285e-155">データ注釈を使用して、特定のリレーションシップの外部キープロパティとして使用するプロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7285e-155">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="7285e-156">これは通常、外部キープロパティが規則によって検出されない場合に実行されます。</span><span class="sxs-lookup"><span data-stu-id="7285e-156">This is typically done when the foreign key property is not discovered by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?highlight=30)]

> [!TIP]  
> <span data-ttu-id="7285e-157">`[ForeignKey]` 注釈は、リレーションシップのいずれかのナビゲーションプロパティに配置できます。</span><span class="sxs-lookup"><span data-stu-id="7285e-157">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="7285e-158">依存エンティティクラスのナビゲーションプロパティに移動する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7285e-158">It does not need to go on the navigation property in the dependent entity class.</span></span>

### <a name="inverseproperty"></a><span data-ttu-id="7285e-159">[InverseProperty]</span><span class="sxs-lookup"><span data-stu-id="7285e-159">[InverseProperty]</span></span>

<span data-ttu-id="7285e-160">データ注釈を使用すると、依存エンティティとプリンシパルエンティティのナビゲーションプロパティをどのように組み合わせるかを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7285e-160">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="7285e-161">これは通常、2つのエンティティ型の間に複数のナビゲーションプロパティのペアがある場合に実行されます。</span><span class="sxs-lookup"><span data-stu-id="7285e-161">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?highlight=33,36)]

## <a name="fluent-api"></a><span data-ttu-id="7285e-162">Fluent API</span><span class="sxs-lookup"><span data-stu-id="7285e-162">Fluent API</span></span>

<span data-ttu-id="7285e-163">Fluent API でリレーションシップを構成するには、まず、リレーションシップを構成するナビゲーションプロパティを特定します。</span><span class="sxs-lookup"><span data-stu-id="7285e-163">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="7285e-164">`HasOne` または `HasMany` によって、構成を開始するエンティティ型のナビゲーションプロパティが識別されます。</span><span class="sxs-lookup"><span data-stu-id="7285e-164">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="7285e-165">次に、`WithOne` または `WithMany` への呼び出しを連結して、逆のナビゲーションを識別します。</span><span class="sxs-lookup"><span data-stu-id="7285e-165">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="7285e-166">`HasOne`/`WithOne` は参照ナビゲーションプロパティに使用され、`HasMany`/`WithMany` コレクションのナビゲーションプロパティに使用されます。</span><span class="sxs-lookup"><span data-stu-id="7285e-166">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?highlight=14-16)]

### <a name="single-navigation-property"></a><span data-ttu-id="7285e-167">単一ナビゲーションプロパティ</span><span class="sxs-lookup"><span data-stu-id="7285e-167">Single Navigation Property</span></span>

<span data-ttu-id="7285e-168">ナビゲーションプロパティが1つしかない場合は、`WithOne` と `WithMany`のパラメーターなしのオーバーロードがあります。</span><span class="sxs-lookup"><span data-stu-id="7285e-168">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="7285e-169">これは、リレーションシップのもう一方の端には概念的に参照またはコレクションが存在するが、エンティティクラスにはナビゲーションプロパティが含まれていないことを示します。</span><span class="sxs-lookup"><span data-stu-id="7285e-169">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?highlight=14-16)]

### <a name="foreign-key"></a><span data-ttu-id="7285e-170">外部キー</span><span class="sxs-lookup"><span data-stu-id="7285e-170">Foreign Key</span></span>

<span data-ttu-id="7285e-171">Fluent API を使用して、特定のリレーションシップの外部キープロパティとして使用するプロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7285e-171">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?highlight=17)]

<span data-ttu-id="7285e-172">次のコードリストは、複合外部キーを構成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7285e-172">The following code listing shows how to configure a composite foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?highlight=20)]

<span data-ttu-id="7285e-173">`HasForeignKey(...)` の文字列オーバーロードを使用して、shadow プロパティを外部キーとして構成できます (詳細については、「 [Shadow Properties](shadow-properties.md) 」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="7285e-173">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="7285e-174">次に示すように、外部キーとして使用する前に、シャドウプロパティをモデルに明示的に追加することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7285e-174">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="without-navigation-property"></a><span data-ttu-id="7285e-175">ナビゲーションプロパティなし</span><span class="sxs-lookup"><span data-stu-id="7285e-175">Without Navigation Property</span></span>

<span data-ttu-id="7285e-176">必ずしもナビゲーションプロパティを指定する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="7285e-176">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="7285e-177">リレーションシップの一方の側に外部キーを指定するだけです。</span><span class="sxs-lookup"><span data-stu-id="7285e-177">You can simply provide a Foreign Key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?highlight=14-17)]

### <a name="principal-key"></a><span data-ttu-id="7285e-178">プリンシパルキー</span><span class="sxs-lookup"><span data-stu-id="7285e-178">Principal Key</span></span>

<span data-ttu-id="7285e-179">外部キーで主キー以外のプロパティを参照する場合は、Fluent API を使用してリレーションシップのプリンシパルキープロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7285e-179">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="7285e-180">プリンシパルキーとして構成したプロパティは、代替キーとして自動的に設定されます (詳細については、「[代替キー](alternate-keys.md) 」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="7285e-180">The property that you configure as the principal key will automatically be setup as an alternate key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

<span data-ttu-id="7285e-181">次のコードリストは、複合プリンシパルキーを構成する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="7285e-181">The following code listing shows how to configure a composite principal key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=Composite&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="7285e-182">プリンシパルキープロパティを指定する順序は、外部キーに指定されている順序と一致している必要があります。</span><span class="sxs-lookup"><span data-stu-id="7285e-182">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

### <a name="required-and-optional-relationships"></a><span data-ttu-id="7285e-183">必須およびオプションのリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="7285e-183">Required and Optional Relationships</span></span>

<span data-ttu-id="7285e-184">Fluent API を使用して、リレーションシップが必須か省略可能かを構成できます。</span><span class="sxs-lookup"><span data-stu-id="7285e-184">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="7285e-185">これは最終的に、外部キープロパティが必須か省略可能かを制御します。</span><span class="sxs-lookup"><span data-stu-id="7285e-185">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="7285e-186">これは、シャドウ状態の外部キーを使用している場合に最も役立ちます。</span><span class="sxs-lookup"><span data-stu-id="7285e-186">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="7285e-187">エンティティクラスに外部キープロパティがある場合、リレーションシップの requiredness は、外部キープロパティが必須か省略可能かに基づいて決定されます (詳細については、[必須プロパティと省略可能なプロパティ](required-optional.md)を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="7285e-187">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=11)]

### <a name="cascade-delete"></a><span data-ttu-id="7285e-188">連鎖削除</span><span class="sxs-lookup"><span data-stu-id="7285e-188">Cascade Delete</span></span>

<span data-ttu-id="7285e-189">Fluent API を使用して、特定のリレーションシップに対して連鎖削除動作を明示的に構成することができます。</span><span class="sxs-lookup"><span data-stu-id="7285e-189">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="7285e-190">各オプションの詳細については、「データの保存」セクションの「[連鎖削除](../saving/cascade-delete.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7285e-190">See [Cascade Delete](../saving/cascade-delete.md) on the Saving Data section for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=11)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="7285e-191">その他のリレーションシップパターン</span><span class="sxs-lookup"><span data-stu-id="7285e-191">Other Relationship Patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="7285e-192">一対一</span><span class="sxs-lookup"><span data-stu-id="7285e-192">One-to-one</span></span>

<span data-ttu-id="7285e-193">一対一のリレーションシップには、両側の参照ナビゲーションプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="7285e-193">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="7285e-194">これらは一対多のリレーションシップと同じ規則に従いますが、依存関係が各プリンシパルに関連付けられるように、外部キープロパティに一意のインデックスが導入されます。</span><span class="sxs-lookup"><span data-stu-id="7285e-194">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=Property&highlight=6,15,16)]

> [!NOTE]  
> <span data-ttu-id="7285e-195">EF は、外部キープロパティを検出する機能に基づいて、依存するエンティティの1つを選択します。</span><span class="sxs-lookup"><span data-stu-id="7285e-195">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="7285e-196">依存関係として間違ったエンティティが選択されている場合は、Fluent API を使用してこれを修正できます。</span><span class="sxs-lookup"><span data-stu-id="7285e-196">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="7285e-197">Fluent API との関係を構成する場合は、`HasOne` メソッドと `WithOne` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="7285e-197">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="7285e-198">外部キーを構成するときに、依存エンティティ型を指定する必要があります。次の一覧で `HasForeignKey` に提供されているジェネリックパラメーターに注意してください。</span><span class="sxs-lookup"><span data-stu-id="7285e-198">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="7285e-199">一対多のリレーションシップでは、参照ナビゲーションを持つエンティティが依存しており、コレクションを持つエンティティがプリンシパルであることが明らかです。</span><span class="sxs-lookup"><span data-stu-id="7285e-199">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="7285e-200">ただし、これは一対一のリレーションシップではないため、明示的に定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7285e-200">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a><span data-ttu-id="7285e-201">多対多</span><span class="sxs-lookup"><span data-stu-id="7285e-201">Many-to-many</span></span>

<span data-ttu-id="7285e-202">結合テーブルを表すエンティティクラスのない多対多リレーションシップは、まだサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="7285e-202">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="7285e-203">ただし、結合テーブルのエンティティクラスを含め、2つの個別の一対多リレーションシップをマップすることで、多対多リレーションシップを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="7285e-203">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)]
