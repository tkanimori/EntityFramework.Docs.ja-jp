---
title: リレーションシップの EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
ms.technology: entity-framework-core
uid: core/modeling/relationships
ms.openlocfilehash: 1732d32643effb0f12111191ed4ba3abb4182d93
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26053032"
---
# <a name="relationships"></a><span data-ttu-id="bc298-102">関係</span><span class="sxs-lookup"><span data-stu-id="bc298-102">Relationships</span></span>

<span data-ttu-id="bc298-103">リレーションシップを定義する方法の 2 つのエンティティ互いに関連します。</span><span class="sxs-lookup"><span data-stu-id="bc298-103">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="bc298-104">リレーショナル データベース、これは、外部キー制約で表されます。</span><span class="sxs-lookup"><span data-stu-id="bc298-104">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="bc298-105">この記事のサンプルの大部分では、一対多のリレーションシップを使用して、概念を示します。</span><span class="sxs-lookup"><span data-stu-id="bc298-105">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="bc298-106">一対一および多対多のリレーションシップの例については、[その他のリレーションシップのパターン](#other-relationship-patterns)記事の最後のセクションです。</span><span class="sxs-lookup"><span data-stu-id="bc298-106">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="bc298-107">用語の定義</span><span class="sxs-lookup"><span data-stu-id="bc298-107">Definition of Terms</span></span>

<span data-ttu-id="bc298-108">リレーションシップの記述に使用される用語の数があります。</span><span class="sxs-lookup"><span data-stu-id="bc298-108">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="bc298-109">**依存エンティティ:** これは、外部キー プロパティを含むエンティティです。</span><span class="sxs-lookup"><span data-stu-id="bc298-109">**Dependent entity:** This is the entity that contains the foreign key property(s).</span></span> <span data-ttu-id="bc298-110">リレーションシップの 'child' と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="bc298-110">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="bc298-111">**プリンシパル エンティティ:** これは、プライマリ/代替のキー プロパティを含むエンティティです。</span><span class="sxs-lookup"><span data-stu-id="bc298-111">**Principal entity:** This is the entity that contains the primary/alternate key property(s).</span></span> <span data-ttu-id="bc298-112">リレーションシップの 'parent' と呼ばれることがあります。</span><span class="sxs-lookup"><span data-stu-id="bc298-112">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="bc298-113">**外部キー:** エンティティに関連するプリンシパル キー プロパティの値を格納するために使用される、依存エンティティのプロパティです。</span><span class="sxs-lookup"><span data-stu-id="bc298-113">**Foreign key:** The property(s) in the dependent entity that is used to store the values of the principal key property that the entity is related to.</span></span>

* <span data-ttu-id="bc298-114">**プリンシパル キー:** プリンシパル エンティティを一意に識別するプロパティです。</span><span class="sxs-lookup"><span data-stu-id="bc298-114">**Principal key:** The property(s) that uniquely identifies the principal entity.</span></span> <span data-ttu-id="bc298-115">主キーや代替キーがあります。</span><span class="sxs-lookup"><span data-stu-id="bc298-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="bc298-116">**ナビゲーション プロパティ。** 関連会計主体への参照を格納しているプリンシパルや依存するエンティティで定義されたプロパティ。</span><span class="sxs-lookup"><span data-stu-id="bc298-116">**Navigation property:** A property defined on the principal and/or dependent entity that contains a reference(s) to the related entity(s).</span></span>

  * <span data-ttu-id="bc298-117">**コレクション ナビゲーション プロパティ。** を多くの関連エンティティへの参照を含むナビゲーション プロパティ。</span><span class="sxs-lookup"><span data-stu-id="bc298-117">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="bc298-118">**ナビゲーション プロパティの参照:** 1 つの関連するエンティティへの参照を保持するナビゲーション プロパティ。</span><span class="sxs-lookup"><span data-stu-id="bc298-118">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="bc298-119">**逆ナビゲーション プロパティ。** 特定のナビゲーション プロパティを検討する場合、リレーションシップのもう一方の端のナビゲーション プロパティに関係する用語です。</span><span class="sxs-lookup"><span data-stu-id="bc298-119">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>

<span data-ttu-id="bc298-120">次のコード リストは、一対多関係を示しています`Blog`と。`Post`</span><span class="sxs-lookup"><span data-stu-id="bc298-120">The following code listing shows a one-to-many relationship between `Blog` and `Post`</span></span>

* <span data-ttu-id="bc298-121">`Post`依存エンティティは、します。</span><span class="sxs-lookup"><span data-stu-id="bc298-121">`Post` is the dependent entity</span></span>

* <span data-ttu-id="bc298-122">`Blog`プリンシパル エンティティには</span><span class="sxs-lookup"><span data-stu-id="bc298-122">`Blog` is the principal entity</span></span>

* <span data-ttu-id="bc298-123">`Post.BlogId`外部キーです。</span><span class="sxs-lookup"><span data-stu-id="bc298-123">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="bc298-124">`Blog.BlogId`(この場合は代替キーではなく、主キー)、プリンシパル キーには</span><span class="sxs-lookup"><span data-stu-id="bc298-124">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="bc298-125">`Post.Blog`参照ナビゲーション プロパティは、します。</span><span class="sxs-lookup"><span data-stu-id="bc298-125">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="bc298-126">`Blog.Posts`コレクション ナビゲーション プロパティは、します。</span><span class="sxs-lookup"><span data-stu-id="bc298-126">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="bc298-127">`Post.Blog`逆ナビゲーション プロパティは、 `Blog.Posts` (その逆)</span><span class="sxs-lookup"><span data-stu-id="bc298-127">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs#Entities)]

## <a name="conventions"></a><span data-ttu-id="bc298-128">規則</span><span class="sxs-lookup"><span data-stu-id="bc298-128">Conventions</span></span>

<span data-ttu-id="bc298-129">規則では、リレーションシップが、型上で検出されたナビゲーション プロパティがある場合に、それが作成されます。</span><span class="sxs-lookup"><span data-stu-id="bc298-129">By convention, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="bc298-130">プロパティは、現在のデータベース プロバイダーではスカラー型として指す型がマップされないことができる場合、ナビゲーション プロパティと見なされます。</span><span class="sxs-lookup"><span data-stu-id="bc298-130">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="bc298-131">プリンシパル エンティティの主キー ターゲット規則によって検出されたリレーションシップに常にします。</span><span class="sxs-lookup"><span data-stu-id="bc298-131">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="bc298-132">代替キーをターゲットにするには Fluent API を使用して追加の構成を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bc298-132">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="bc298-133">完全に定義されたリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="bc298-133">Fully Defined Relationships</span></span>

<span data-ttu-id="bc298-134">リレーションシップの最も一般的なパターンでは、リレーションシップと依存するエンティティ クラスで定義されている外部キーのプロパティの両端で定義されているナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="bc298-134">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="bc298-135">2 つの型の間のナビゲーション プロパティのペアが見つかった場合が同じリレーションシップの逆ナビゲーション プロパティとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="bc298-135">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="bc298-136">依存エンティティには、という名前のプロパティが含まれている場合`<primary key property name>`、 `<navigation property name><primary key property name>`、または`<principal entity name><primary key property name>`外部キーとしてように構成されます。</span><span class="sxs-lookup"><span data-stu-id="bc298-136">If the dependent entity contains a property named `<primary key property name>`, `<navigation property name><primary key property name>`, or `<principal entity name><primary key property name>` then it will be configured as the foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> <span data-ttu-id="bc298-137">2 つの型の間で定義されている複数のナビゲーション プロパティがあるかどうか (つまり互いを指すナビゲーションの 1 つ以上の個別ペア) し、リレーションシップは作成されません慣例により、および手動で構成を特定するようにする必要があります、1 組のナビゲーション プロパティ。</span><span class="sxs-lookup"><span data-stu-id="bc298-137">If there are multiple navigation properties defined between two types (i.e. more than one distinct pair of navigations that point to each other), then no relationships will be created by convention and you will need to manually configure them to identify how the navigation properties pair up.</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="bc298-138">外部キー プロパティはありません。</span><span class="sxs-lookup"><span data-stu-id="bc298-138">No Foreign Key Property</span></span>

<span data-ttu-id="bc298-139">推奨されますが、依存エンティティ クラスで定義されている外部キー プロパティを持つ、必須ではありません。</span><span class="sxs-lookup"><span data-stu-id="bc298-139">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="bc298-140">外部キーのプロパティが見つからない場合シャドウ外部キーのプロパティを名前で導入される予定`<navigation property name><principal key property name>`(を参照してください[プロパティをシャドウ](shadow-properties.md)詳細については)。</span><span class="sxs-lookup"><span data-stu-id="bc298-140">If no foreign key property is found, a shadow foreign key property will be introduced with the name `<navigation property name><principal key property name>` (see [Shadow Properties](shadow-properties.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a><span data-ttu-id="bc298-141">単一のナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="bc298-141">Single Navigation Property</span></span>

<span data-ttu-id="bc298-142">1 つのナビゲーション プロパティ (ありません逆のナビゲーションと外部キー プロパティはありません) を含めることは、規約によって定義されたリレーションシップが十分です。</span><span class="sxs-lookup"><span data-stu-id="bc298-142">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="bc298-143">単一のナビゲーション プロパティと外部キーのプロパティもあります。</span><span class="sxs-lookup"><span data-stu-id="bc298-143">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a><span data-ttu-id="bc298-144">連鎖削除</span><span class="sxs-lookup"><span data-stu-id="bc298-144">Cascade Delete</span></span>

<span data-ttu-id="bc298-145">規則では、連鎖削除に設定されます*Cascade*の必要なリレーションシップと*ClientSetNull*省略可能なリレーションシップの場合。</span><span class="sxs-lookup"><span data-stu-id="bc298-145">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="bc298-146">*Cascade*依存エンティティも削除することを意味します。</span><span class="sxs-lookup"><span data-stu-id="bc298-146">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="bc298-147">*ClientSetNull*はメモリに読み込まれていない依存エンティティが維持されることを意味変更せずとする必要があります手動でまたは削除する有効なプリンシパル エンティティを指すように更新します。</span><span class="sxs-lookup"><span data-stu-id="bc298-147">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="bc298-148">メモリに読み込まれているエンティティの EF コアしようと外部キー プロパティを null に設定します。</span><span class="sxs-lookup"><span data-stu-id="bc298-148">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="bc298-149">参照してください、[必須および省略可能なリレーションシップ](#required-and-optional-relationships)必須およびオプションの関係の違いについてのセクションです。</span><span class="sxs-lookup"><span data-stu-id="bc298-149">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="bc298-150">参照してください[連鎖削除](../saving/cascade-delete.md)の詳細については、さまざまな規則が使用する既定の動作とを削除します。</span><span class="sxs-lookup"><span data-stu-id="bc298-150">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="bc298-151">データの注釈</span><span class="sxs-lookup"><span data-stu-id="bc298-151">Data Annotations</span></span>

<span data-ttu-id="bc298-152">、リレーションシップを構成するために使用する 2 つのデータの注釈がある`[ForeignKey]`と`[InverseProperty]`です。</span><span class="sxs-lookup"><span data-stu-id="bc298-152">There are two data annotations that can be used to configure relationships, `[ForeignKey]` and `[InverseProperty]`.</span></span>

### <a name="foreignkey"></a><span data-ttu-id="bc298-153">[ForeignKey]</span><span class="sxs-lookup"><span data-stu-id="bc298-153">[ForeignKey]</span></span>

<span data-ttu-id="bc298-154">データ注釈を使用すると、構成するプロパティは、特定のリレーションシップの外部キー プロパティとして使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bc298-154">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="bc298-155">外部キー プロパティは、規則によって検出されていない場合にこれは通常です。</span><span class="sxs-lookup"><span data-stu-id="bc298-155">This is typically done when the foreign key property is not discovered by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/ForeignKey.cs?name=Entities&highlight=17)]

> [!TIP]  
> <span data-ttu-id="bc298-156">`[ForeignKey]`リレーションシップのいずれかのナビゲーション プロパティに注釈を配置することができます。</span><span class="sxs-lookup"><span data-stu-id="bc298-156">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="bc298-157">ナビゲーションのプロパティ、依存エンティティ クラスに移動する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="bc298-157">It does not need to go on the navigation property in the dependent entity class.</span></span>

### <a name="inverseproperty"></a><span data-ttu-id="bc298-158">[InverseProperty]</span><span class="sxs-lookup"><span data-stu-id="bc298-158">[InverseProperty]</span></span>

<span data-ttu-id="bc298-159">データ注釈を使用して、依存とプリンシパル エンティティのナビゲーション プロパティのペアを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="bc298-159">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="bc298-160">2 つのエンティティ型の間のナビゲーション プロパティの 1 つ以上のペアがある場合にこれは通常です。</span><span class="sxs-lookup"><span data-stu-id="bc298-160">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/InverseProperty.cs?name=Entities&highlight=20,23)]

## <a name="fluent-api"></a><span data-ttu-id="bc298-161">Fluent API</span><span class="sxs-lookup"><span data-stu-id="bc298-161">Fluent API</span></span>

<span data-ttu-id="bc298-162">リレーションシップ Fluent api を構成するのには、リレーションシップを構成するナビゲーション プロパティを識別することによって開始します。</span><span class="sxs-lookup"><span data-stu-id="bc298-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="bc298-163">`HasOne`または`HasMany`の構成を開始するエンティティ型にナビゲーション プロパティを識別します。</span><span class="sxs-lookup"><span data-stu-id="bc298-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="bc298-164">呼び出しをチェーンする`WithOne`または`WithMany`を逆のナビゲーションを識別します。</span><span class="sxs-lookup"><span data-stu-id="bc298-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="bc298-165">`HasOne`/`WithOne`参照ナビゲーション プロパティが使用されると`HasMany` / `WithMany`コレクション ナビゲーション プロパティに対して使用します。</span><span class="sxs-lookup"><span data-stu-id="bc298-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/NoForeignKey.cs?name=Model&highlight=8,9,10)]

### <a name="single-navigation-property"></a><span data-ttu-id="bc298-166">単一のナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="bc298-166">Single Navigation Property</span></span>

<span data-ttu-id="bc298-167">1 つのナビゲーション プロパティのみがあるかどうかは、パラメーターなしのオーバー ロードの`WithOne`と`WithMany`です。</span><span class="sxs-lookup"><span data-stu-id="bc298-167">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="bc298-168">これは、参照またはコレクションがリレーションシップのもう一方の端に概念的にが、エンティティ クラスに含まれるナビゲーション プロパティが存在しないことを示します。</span><span class="sxs-lookup"><span data-stu-id="bc298-168">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/OneNavigation.cs?name=Model&highlight=10)]

### <a name="foreign-key"></a><span data-ttu-id="bc298-169">外部キー</span><span class="sxs-lookup"><span data-stu-id="bc298-169">Foreign Key</span></span>

<span data-ttu-id="bc298-170">Fluent API を使用すると、構成するプロパティは、特定のリレーションシップの外部キー プロパティとして使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bc298-170">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ForeignKey.cs?name=Model&highlight=11)]

<span data-ttu-id="bc298-171">次のコード リストは、複合外部キーを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="bc298-171">The following code listing shows how to configure a composite foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/CompositeForeignKey.cs?name=Model&highlight=13)]

<span data-ttu-id="bc298-172">文字列のオーバー ロードを使用する`HasForeignKey(...)`の外部キーとしてシャドウ プロパティの構成 (を参照してください[プロパティをシャドウ](shadow-properties.md)詳細については)。</span><span class="sxs-lookup"><span data-stu-id="bc298-172">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="bc298-173">以下の手順に従って)、外部キーとして使用する前に、モデルに明示的にシャドウ プロパティを追加することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="bc298-173">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="principal-key"></a><span data-ttu-id="bc298-174">プリンシパル キー</span><span class="sxs-lookup"><span data-stu-id="bc298-174">Principal Key</span></span>

<span data-ttu-id="bc298-175">主キー以外のプロパティを参照する外部キーにする場合は、Fluent API を使用して、リレーションシップのプリンシパル キー プロパティを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="bc298-175">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="bc298-176">キーは、プリンシパルとして自動的に構成されているプロパティを代替キーとしてセットアップする (を参照してください[代替キー](alternate-keys.md)詳細については)。</span><span class="sxs-lookup"><span data-stu-id="bc298-176">The property that you configure as the principal key will automatically be setup as an alternate key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/PrincipalKey.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<RecordOfSale>()
            .HasOne(s => s.Car)
            .WithMany(c => c.SaleHistory)
            .HasForeignKey(s => s.CarLicensePlate)
            .HasPrincipalKey(c => c.LicensePlate);
    }
}

public class Car
{
    public int CarId { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }

    public List<RecordOfSale> SaleHistory { get; set; }
}

public class RecordOfSale
{
    public int RecordOfSaleId { get; set; }
    public DateTime DateSold { get; set; }
    public decimal Price { get; set; }

    public string CarLicensePlate { get; set; }
    public Car Car { get; set; }
}
```

<span data-ttu-id="bc298-177">次のコード リストは、複合主キーを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="bc298-177">The following code listing shows how to configure a composite principal key.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/CompositePrincipalKey.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<RecordOfSale>()
            .HasOne(s => s.Car)
            .WithMany(c => c.SaleHistory)
            .HasForeignKey(s => new { s.CarState, s.CarLicensePlate })
            .HasPrincipalKey(c => new { c.State, c.LicensePlate });
    }
}

public class Car
{
    public int CarId { get; set; }
    public string State { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }

    public List<RecordOfSale> SaleHistory { get; set; }
}

public class RecordOfSale
{
    public int RecordOfSaleId { get; set; }
    public DateTime DateSold { get; set; }
    public decimal Price { get; set; }

    public string CarState { get; set; }
    public string CarLicensePlate { get; set; }
    public Car Car { get; set; }
}
```

> [!WARNING]  
> <span data-ttu-id="bc298-178">プリンシパル キー プロパティを指定した順序は、外部キーの指定された順序と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bc298-178">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

### <a name="required-and-optional-relationships"></a><span data-ttu-id="bc298-179">必須およびオプションの関係</span><span class="sxs-lookup"><span data-stu-id="bc298-179">Required and Optional Relationships</span></span>

<span data-ttu-id="bc298-180">Fluent API を使用して、リレーションシップが必須またはオプションであるかどうかを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="bc298-180">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="bc298-181">最終的に外部キー プロパティが必須またはオプションかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="bc298-181">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="bc298-182">これは、機能は、シャドウ状態の外部キーを使用しているときに最も便利です。</span><span class="sxs-lookup"><span data-stu-id="bc298-182">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="bc298-183">外部キーのプロパティがエンティティ クラスであるかどうかは、リレーションシップの requiredness は外部キー プロパティが必須またはオプションかどうかに基づいて決定されます (を参照してください[必須および省略可能なプロパティ](required-optional.md)の詳細情報)。</span><span class="sxs-lookup"><span data-stu-id="bc298-183">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

<!-- [!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/Required.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .IsRequired();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog { get; set; }
}
```

### <a name="cascade-delete"></a><span data-ttu-id="bc298-184">連鎖削除</span><span class="sxs-lookup"><span data-stu-id="bc298-184">Cascade Delete</span></span>

<span data-ttu-id="bc298-185">Fluent API を使用すると、特定のリレーションシップの連鎖削除の動作を明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="bc298-185">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="bc298-186">参照してください[連鎖削除](../saving/cascade-delete.md)各オプションの詳細については、データの保存セクションでします。</span><span class="sxs-lookup"><span data-stu-id="bc298-186">See [Cascade Delete](../saving/cascade-delete.md) on the Saving Data section for a detailed discussion of each option.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/CascadeDelete.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .OnDelete(DeleteBehavior.Cascade);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public List<Post> Posts { get; set; }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

## <a name="other-relationship-patterns"></a><span data-ttu-id="bc298-187">その他のリレーションシップのパターン</span><span class="sxs-lookup"><span data-stu-id="bc298-187">Other Relationship Patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="bc298-188">一対一</span><span class="sxs-lookup"><span data-stu-id="bc298-188">One-to-one</span></span>

<span data-ttu-id="bc298-189">一対一のリレーションシップでは、両方の側で参照ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="bc298-189">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="bc298-190">一対多リレーションシップと同じ規則に従いますが、各プリンシパルに関連する 1 つだけの依存することを確認する外部キー プロパティに一意のインデックスが導入されました。</span><span class="sxs-lookup"><span data-stu-id="bc298-190">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/Relationships/OneToOne.cs?highlight=6,15,16)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogImage BlogImage { get; set; }
}

public class BlogImage
{
    public int BlogImageId { get; set; }
    public byte[] Image { get; set; }
    public string Caption { get; set; }

    public int BlogId { get; set; }
    public Blog Blog { get; set; }
}
```

> [!NOTE]  
> <span data-ttu-id="bc298-191">EF は外部キーのプロパティを検出するには、その機能に基づく依存するエンティティのいずれかを選択します。</span><span class="sxs-lookup"><span data-stu-id="bc298-191">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="bc298-192">従属変数として間違ったエンティティを選択した場合は、これを修正する Fluent API を使用できます。</span><span class="sxs-lookup"><span data-stu-id="bc298-192">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="bc298-193">使用するリレーションシップ Fluent api を構成する場合、`HasOne`と`WithOne`メソッドです。</span><span class="sxs-lookup"><span data-stu-id="bc298-193">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="bc298-194">-依存エンティティの種類を指定する必要があります。 外部キーを構成するときに提供されるジェネリック パラメーターに注意してください`HasForeignKey`の下の一覧にします。</span><span class="sxs-lookup"><span data-stu-id="bc298-194">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="bc298-195">一対多リレーションシップでは、オフにしている参照ナビゲーションを持つエンティティが依存するコレクションを使用して 1 つは、プリンシパルです。</span><span class="sxs-lookup"><span data-stu-id="bc298-195">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="bc298-196">はありませんが、一対一リレーションシップのためにを明示的に定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bc298-196">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/OneToOne.cs?highlight=11)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<BlogImage> BlogImages { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasOne(p => p.BlogImage)
            .WithOne(i => i.Blog)
            .HasForeignKey<BlogImage>(b => b.BlogForeignKey);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogImage BlogImage { get; set; }
}

public class BlogImage
{
    public int BlogImageId { get; set; }
    public byte[] Image { get; set; }
    public string Caption { get; set; }

    public int BlogForeignKey { get; set; }
    public Blog Blog { get; set; }
}
```

### <a name="many-to-many"></a><span data-ttu-id="bc298-197">[多対多]</span><span class="sxs-lookup"><span data-stu-id="bc298-197">Many-to-many</span></span>

<span data-ttu-id="bc298-198">結合テーブルを表すエンティティ クラスを持たない多対多リレーションシップはまだサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="bc298-198">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="bc298-199">ただし、結合テーブルとのマッピング 2 つの独立した一対多リレーションシップのエンティティ クラスを含めることによって、多対多リレーションシップを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="bc298-199">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Relationships/ManyToMany.cs?highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<PostTag>()
            .HasKey(t => new { t.PostId, t.TagId });

        modelBuilder.Entity<PostTag>()
            .HasOne(pt => pt.Post)
            .WithMany(p => p.PostTags)
            .HasForeignKey(pt => pt.PostId);

        modelBuilder.Entity<PostTag>()
            .HasOne(pt => pt.Tag)
            .WithMany(t => t.PostTags)
            .HasForeignKey(pt => pt.TagId);
    }
}

public class Post
{
    public int PostId { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public List<PostTag> PostTags { get; set; }
}

public class Tag
{
    public string TagId { get; set; }

    public List<PostTag> PostTags { get; set; }
}

public class PostTag
{
    public int PostId { get; set; }
    public Post Post { get; set; }

    public string TagId { get; set; }
    public Tag Tag { get; set; }
}
```
