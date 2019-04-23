---
title: リレーションシップ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
uid: core/modeling/relationships
ms.openlocfilehash: 9ef1a9269fc99f5b27a81c11a161ed5f9d74180d
ms.sourcegitcommit: 87fcaba46535aa351db4bdb1231bd14b40e459b9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59929938"
---
# <a name="relationships"></a><span data-ttu-id="1523c-102">関係</span><span class="sxs-lookup"><span data-stu-id="1523c-102">Relationships</span></span>

<span data-ttu-id="1523c-103">2 つのエンティティを定義するリレーションシップを互いに関連します。</span><span class="sxs-lookup"><span data-stu-id="1523c-103">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="1523c-104">リレーショナル データベースで外部キー制約によって表されます。</span><span class="sxs-lookup"><span data-stu-id="1523c-104">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="1523c-105">この記事のサンプルのほとんどは、概念を説明するのに一対多リレーションシップを使用します。</span><span class="sxs-lookup"><span data-stu-id="1523c-105">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="1523c-106">一対一および多対多のリレーションシップの例については、[リレーションシップの他のパターン](#other-relationship-patterns)記事の最後のセクション。</span><span class="sxs-lookup"><span data-stu-id="1523c-106">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="1523c-107">用語の定義</span><span class="sxs-lookup"><span data-stu-id="1523c-107">Definition of Terms</span></span>

<span data-ttu-id="1523c-108">リレーションシップの記述に使用される用語のいくつか</span><span class="sxs-lookup"><span data-stu-id="1523c-108">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="1523c-109">**依存エンティティは:** これは、外部キー プロパティを含むエンティティです。</span><span class="sxs-lookup"><span data-stu-id="1523c-109">**Dependent entity:** This is the entity that contains the foreign key property(s).</span></span> <span data-ttu-id="1523c-110">リレーションシップの「子」とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="1523c-110">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="1523c-111">**プリンシパル エンティティ:** これは、プライマリ/代替キー プロパティを含むエンティティです。</span><span class="sxs-lookup"><span data-stu-id="1523c-111">**Principal entity:** This is the entity that contains the primary/alternate key property(s).</span></span> <span data-ttu-id="1523c-112">リレーションシップの「親」と呼ばれるにあります。</span><span class="sxs-lookup"><span data-stu-id="1523c-112">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="1523c-113">**外部キー:** エンティティに関連するプリンシパルのキー プロパティの値の格納に使用される依存エンティティのプロパティ。</span><span class="sxs-lookup"><span data-stu-id="1523c-113">**Foreign key:** The property(s) in the dependent entity that is used to store the values of the principal key property that the entity is related to.</span></span>

* <span data-ttu-id="1523c-114">**プリンシパルのキー:** プリンシパル エンティティを一意に識別するプロパティ。</span><span class="sxs-lookup"><span data-stu-id="1523c-114">**Principal key:** The property(s) that uniquely identifies the principal entity.</span></span> <span data-ttu-id="1523c-115">主キーや代替キーがあります。</span><span class="sxs-lookup"><span data-stu-id="1523c-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="1523c-116">**ナビゲーション プロパティ:** プロパティは、関連の会計主体にへの参照を格納しているプリンシパルや依存エンティティで定義します。</span><span class="sxs-lookup"><span data-stu-id="1523c-116">**Navigation property:** A property defined on the principal and/or dependent entity that contains a reference(s) to the related entity(s).</span></span>

  * <span data-ttu-id="1523c-117">**コレクション ナビゲーション プロパティ:** 多くの関連エンティティへの参照を含むナビゲーション プロパティ。</span><span class="sxs-lookup"><span data-stu-id="1523c-117">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="1523c-118">**参照ナビゲーション プロパティ:** 1 つの関連エンティティへの参照を保持するナビゲーション プロパティ。</span><span class="sxs-lookup"><span data-stu-id="1523c-118">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="1523c-119">**逆のナビゲーション プロパティ:** 特定のナビゲーション プロパティを説明しながら、この用語は、リレーションシップの他方の end のナビゲーション プロパティを指します。</span><span class="sxs-lookup"><span data-stu-id="1523c-119">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>

<span data-ttu-id="1523c-120">次のコード リストは、一対多の関係を示しています`Blog`と。 `Post`</span><span class="sxs-lookup"><span data-stu-id="1523c-120">The following code listing shows a one-to-many relationship between `Blog` and `Post`</span></span>

* <span data-ttu-id="1523c-121">`Post` 依存エンティティは、します。</span><span class="sxs-lookup"><span data-stu-id="1523c-121">`Post` is the dependent entity</span></span>

* <span data-ttu-id="1523c-122">`Blog` プリンシパル エンティティには</span><span class="sxs-lookup"><span data-stu-id="1523c-122">`Blog` is the principal entity</span></span>

* <span data-ttu-id="1523c-123">`Post.BlogId` 外部キーです。</span><span class="sxs-lookup"><span data-stu-id="1523c-123">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="1523c-124">`Blog.BlogId` プリンシパルのキー (この例では、代替キーではなく、主キー)</span><span class="sxs-lookup"><span data-stu-id="1523c-124">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="1523c-125">`Post.Blog` 参照ナビゲーション プロパティは、します。</span><span class="sxs-lookup"><span data-stu-id="1523c-125">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="1523c-126">`Blog.Posts` コレクション ナビゲーション プロパティは、します。</span><span class="sxs-lookup"><span data-stu-id="1523c-126">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="1523c-127">`Post.Blog` 逆のナビゲーション プロパティは、 `Blog.Posts` (その逆)</span><span class="sxs-lookup"><span data-stu-id="1523c-127">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs#Entities)]

## <a name="conventions"></a><span data-ttu-id="1523c-128">規約</span><span class="sxs-lookup"><span data-stu-id="1523c-128">Conventions</span></span>

<span data-ttu-id="1523c-129">慣例によりは、型上で検出されたナビゲーション プロパティがある場合に、リレーションシップが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1523c-129">By convention, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="1523c-130">現在のデータベース プロバイダーによってスカラー型として指す型がマップしないことができる場合、プロパティはナビゲーション プロパティと見なされます。</span><span class="sxs-lookup"><span data-stu-id="1523c-130">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="1523c-131">規則によって検出されたリレーションシップは、プリンシパル エンティティの主キーを常に対象とします。</span><span class="sxs-lookup"><span data-stu-id="1523c-131">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="1523c-132">代替キーを対象とは、Fluent API を使用して追加の構成を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1523c-132">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="1523c-133">完全に定義されたリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="1523c-133">Fully Defined Relationships</span></span>

<span data-ttu-id="1523c-134">リレーションシップの最も一般的なパターンでは、リレーションシップと依存エンティティ クラスで定義されている外部キー プロパティの両方の end で定義されているナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="1523c-134">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="1523c-135">2 つの型の間のナビゲーション プロパティのペアが見つかった場合同じリレーションシップの逆のナビゲーション プロパティとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="1523c-135">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="1523c-136">依存エンティティには、という名前のプロパティが含まれている場合`<primary key property name>`、 `<navigation property name><primary key property name>`、または`<principal entity name><primary key property name>`し、外部キーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="1523c-136">If the dependent entity contains a property named `<primary key property name>`, `<navigation property name><primary key property name>`, or `<principal entity name><primary key property name>` then it will be configured as the foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> <span data-ttu-id="1523c-137">2 つの型の間で定義されている複数のナビゲーション プロパティがあるかどうか (つまり、互いを指すナビゲーションの 1 つ以上の個別のペア)、し、リレーションシップは作成されません慣例および識別するためにそれらを手動で構成する必要がある方法ナビゲーション プロパティを組み合わせます。</span><span class="sxs-lookup"><span data-stu-id="1523c-137">If there are multiple navigation properties defined between two types (that is, more than one distinct pair of navigations that point to each other), then no relationships will be created by convention and you will need to manually configure them to identify how the navigation properties pair up.</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="1523c-138">外部キー プロパティ</span><span class="sxs-lookup"><span data-stu-id="1523c-138">No Foreign Key Property</span></span>

<span data-ttu-id="1523c-139">依存エンティティ クラスで定義されている外部キー プロパティを持つことをお勧めしますが、これは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="1523c-139">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="1523c-140">名前でシャドウの外部キー プロパティが導入される外部キー プロパティが見つからない場合`<navigation property name><principal key property name>`(を参照してください[プロパティをシャドウ](shadow-properties.md)詳細については)。</span><span class="sxs-lookup"><span data-stu-id="1523c-140">If no foreign key property is found, a shadow foreign key property will be introduced with the name `<navigation property name><principal key property name>` (see [Shadow Properties](shadow-properties.md) for more information).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a><span data-ttu-id="1523c-141">単一のナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="1523c-141">Single Navigation Property</span></span>

<span data-ttu-id="1523c-142">(ありません逆のナビゲーションと外部キー プロパティ) の 1 つだけのナビゲーション プロパティを含めると、規則で定義されたリレーションシップを持つだけです。</span><span class="sxs-lookup"><span data-stu-id="1523c-142">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="1523c-143">単一のナビゲーション プロパティと外部キー プロパティすることもできます。</span><span class="sxs-lookup"><span data-stu-id="1523c-143">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Samples/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a><span data-ttu-id="1523c-144">連鎖削除</span><span class="sxs-lookup"><span data-stu-id="1523c-144">Cascade Delete</span></span>

<span data-ttu-id="1523c-145">設定される連鎖削除規則により、 *Cascade*に必要なリレーションシップと*ClientSetNull*の省略可能なリレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="1523c-145">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="1523c-146">*Cascade*依存エンティティも削除されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="1523c-146">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="1523c-147">*ClientSetNull*メモリに読み込まれていない依存するエンティティが存在することを意味変更なしとする必要があります手動で削除、または有効なプリンシパル エンティティをポイントするように更新します。</span><span class="sxs-lookup"><span data-stu-id="1523c-147">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="1523c-148">メモリに読み込まれるエンティティの場合は、EF Core を外部キー プロパティを null に設定を試みます。</span><span class="sxs-lookup"><span data-stu-id="1523c-148">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="1523c-149">参照してください、[必須および省略可能リレーションシップ](#required-and-optional-relationships)必須および省略可能なリレーションシップの違い」セクション。</span><span class="sxs-lookup"><span data-stu-id="1523c-149">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="1523c-150">参照してください[連鎖削除](../saving/cascade-delete.md)の詳細について、さまざまな動作と規約によって使用される既定値を削除しています。</span><span class="sxs-lookup"><span data-stu-id="1523c-150">See [Cascade Delete](../saving/cascade-delete.md) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="1523c-151">データの注釈</span><span class="sxs-lookup"><span data-stu-id="1523c-151">Data Annotations</span></span>

<span data-ttu-id="1523c-152">リレーションシップを構成するのに使用できる 2 つのデータ注釈がある`[ForeignKey]`と`[InverseProperty]`します。</span><span class="sxs-lookup"><span data-stu-id="1523c-152">There are two data annotations that can be used to configure relationships, `[ForeignKey]` and `[InverseProperty]`.</span></span> <span data-ttu-id="1523c-153">これらで使用できる、`System.ComponentModel.DataAnnotations.Schema`名前空間。</span><span class="sxs-lookup"><span data-stu-id="1523c-153">These are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span>

### <a name="foreignkey"></a><span data-ttu-id="1523c-154">[不変]</span><span class="sxs-lookup"><span data-stu-id="1523c-154">[ForeignKey]</span></span>

<span data-ttu-id="1523c-155">データ注釈を使用すると、構成プロパティは、特定のリレーションシップの外部キー プロパティとして使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1523c-155">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="1523c-156">外部キー プロパティが規則によって検出されないときにこれは通常です。</span><span class="sxs-lookup"><span data-stu-id="1523c-156">This is typically done when the foreign key property is not discovered by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/ForeignKey.cs?highlight=30)]

> [!TIP]  
> <span data-ttu-id="1523c-157">`[ForeignKey]`リレーションシップのいずれかのナビゲーション プロパティに注釈を配置できます。</span><span class="sxs-lookup"><span data-stu-id="1523c-157">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="1523c-158">依存エンティティ クラスでナビゲーション プロパティに移動する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="1523c-158">It does not need to go on the navigation property in the dependent entity class.</span></span>

### <a name="inverseproperty"></a><span data-ttu-id="1523c-159">[InverseProperty]</span><span class="sxs-lookup"><span data-stu-id="1523c-159">[InverseProperty]</span></span>

<span data-ttu-id="1523c-160">依存とプリンシパル エンティティのナビゲーション プロパティを組み合わせる方法を構成するのには、データ注釈を使用できます。</span><span class="sxs-lookup"><span data-stu-id="1523c-160">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="1523c-161">2 つのエンティティ型の間のナビゲーション プロパティの 1 つ以上のペアがある場合にこれは通常です。</span><span class="sxs-lookup"><span data-stu-id="1523c-161">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/Relationships/InverseProperty.cs?highlight=33,36)]

## <a name="fluent-api"></a><span data-ttu-id="1523c-162">Fluent API</span><span class="sxs-lookup"><span data-stu-id="1523c-162">Fluent API</span></span>

<span data-ttu-id="1523c-163">Fluent API でのリレーションシップを構成するには、まず、リレーションシップを作成するナビゲーション プロパティを識別します。</span><span class="sxs-lookup"><span data-stu-id="1523c-163">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="1523c-164">`HasOne` または`HasMany`の構成を開始するエンティティ型にナビゲーション プロパティを識別します。</span><span class="sxs-lookup"><span data-stu-id="1523c-164">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="1523c-165">呼び出しをチェーンする`WithOne`または`WithMany`逆ナビゲーションを識別するためにします。</span><span class="sxs-lookup"><span data-stu-id="1523c-165">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="1523c-166">`HasOne`/`WithOne` 参照ナビゲーション プロパティに対して使用し、 `HasMany` / `WithMany`コレクション ナビゲーション プロパティに対して使用します。</span><span class="sxs-lookup"><span data-stu-id="1523c-166">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/NoForeignKey.cs?highlight=14-16)]

### <a name="single-navigation-property"></a><span data-ttu-id="1523c-167">単一のナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="1523c-167">Single Navigation Property</span></span>

<span data-ttu-id="1523c-168">1 つのナビゲーション プロパティのみがある場合のパラメーターなしのオーバー ロードがあります`WithOne`と`WithMany`します。</span><span class="sxs-lookup"><span data-stu-id="1523c-168">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="1523c-169">これは、参照またはコレクションがリレーションシップのもう一方の端に概念的にが、エンティティ クラスに含まれるナビゲーション プロパティがないことを示します。</span><span class="sxs-lookup"><span data-stu-id="1523c-169">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/OneNavigation.cs?highlight=14-16)]

### <a name="foreign-key"></a><span data-ttu-id="1523c-170">外部キー</span><span class="sxs-lookup"><span data-stu-id="1523c-170">Foreign Key</span></span>

<span data-ttu-id="1523c-171">Fluent API を使用すると、構成プロパティは、特定のリレーションシップの外部キー プロパティとして使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1523c-171">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ForeignKey.cs?highlight=17)]

<span data-ttu-id="1523c-172">次のコード リストは、複合外部キーを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="1523c-172">The following code listing shows how to configure a composite foreign key.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/CompositeForeignKey.cs?highlight=20)]

<span data-ttu-id="1523c-173">文字列のオーバー ロードを使用する`HasForeignKey(...)`外部キーとしてシャドウ プロパティを構成する (を参照してください[プロパティをシャドウ](shadow-properties.md)詳細については)。</span><span class="sxs-lookup"><span data-stu-id="1523c-173">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](shadow-properties.md) for more information).</span></span> <span data-ttu-id="1523c-174">(下図参照)、外部キーとして使用する前に、モデルを明示的にシャドウ プロパティを追加することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1523c-174">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="principal-key"></a><span data-ttu-id="1523c-175">プリンシパル キー</span><span class="sxs-lookup"><span data-stu-id="1523c-175">Principal Key</span></span>

<span data-ttu-id="1523c-176">主キー以外のプロパティを参照する外部キーを設定する場合は、リレーションシップのプリンシパルのキー プロパティを構成する Fluent API を使用できます。</span><span class="sxs-lookup"><span data-stu-id="1523c-176">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="1523c-177">プリンシパルは、キーとして自動的に構成されているプロパティである代替キーとしてのセットアップ (を参照してください[代替キー](alternate-keys.md)詳細については)。</span><span class="sxs-lookup"><span data-stu-id="1523c-177">The property that you configure as the principal key will automatically be setup as an alternate key (see [Alternate Keys](alternate-keys.md) for more information).</span></span>

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

<span data-ttu-id="1523c-178">次のコード リストは、複合主キーを構成する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="1523c-178">The following code listing shows how to configure a composite principal key.</span></span>

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
> <span data-ttu-id="1523c-179">プリンシパルのキー プロパティを指定した順序は、外部キーの指定された順序と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1523c-179">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

### <a name="required-and-optional-relationships"></a><span data-ttu-id="1523c-180">必須およびオプションの関係</span><span class="sxs-lookup"><span data-stu-id="1523c-180">Required and Optional Relationships</span></span>

<span data-ttu-id="1523c-181">Fluent API を使用して、リレーションシップが必須か省略可能かどうかを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="1523c-181">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="1523c-182">最終的に外部キー プロパティが必須または省略可能なのかどうかを制御します。</span><span class="sxs-lookup"><span data-stu-id="1523c-182">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="1523c-183">これは、シャドウ状態の外部キーを使用しているときに最も役立ちます。</span><span class="sxs-lookup"><span data-stu-id="1523c-183">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="1523c-184">外部キー プロパティがエンティティ クラスであるかどうかは、リレーションシップの requiredness が外部キー プロパティが必須または省略可能なのかどうかに基づいて決定されます (を参照してください[必須および省略可能なプロパティ](required-optional.md)詳細情報)。</span><span class="sxs-lookup"><span data-stu-id="1523c-184">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](required-optional.md) for more information).</span></span>

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

### <a name="cascade-delete"></a><span data-ttu-id="1523c-185">連鎖削除</span><span class="sxs-lookup"><span data-stu-id="1523c-185">Cascade Delete</span></span>

<span data-ttu-id="1523c-186">Fluent API を使用して、明示的に指定したリレーションシップの連鎖削除の動作を構成することができます。</span><span class="sxs-lookup"><span data-stu-id="1523c-186">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="1523c-187">参照してください[連鎖削除](../saving/cascade-delete.md)各オプションの詳細についてはデータの保存 セクションでします。</span><span class="sxs-lookup"><span data-stu-id="1523c-187">See [Cascade Delete](../saving/cascade-delete.md) on the Saving Data section for a detailed discussion of each option.</span></span>

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

## <a name="other-relationship-patterns"></a><span data-ttu-id="1523c-188">その他のリレーションシップのパターン</span><span class="sxs-lookup"><span data-stu-id="1523c-188">Other Relationship Patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="1523c-189">一対一</span><span class="sxs-lookup"><span data-stu-id="1523c-189">One-to-one</span></span>

<span data-ttu-id="1523c-190">一対一のリレーションシップでは、両方の側での参照ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="1523c-190">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="1523c-191">一対多のリレーションシップと同じ規則に従ってがプリンシパルごとに 1 つだけの依存が関連することを確認する場合は、外部キー プロパティに一意のインデックスが導入されています。</span><span class="sxs-lookup"><span data-stu-id="1523c-191">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

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
> <span data-ttu-id="1523c-192">EF はベースの外部キー プロパティを検出する機能に依存するエンティティのいずれかを選択します。</span><span class="sxs-lookup"><span data-stu-id="1523c-192">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="1523c-193">依存として間違ったエンティティを選択した場合は、これを修正する Fluent API を使用できます。</span><span class="sxs-lookup"><span data-stu-id="1523c-193">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="1523c-194">使用する Fluent API を使用した、リレーションシップを構成するときに、`HasOne`と`WithOne`メソッド。</span><span class="sxs-lookup"><span data-stu-id="1523c-194">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="1523c-195">-依存エンティティ型を指定する必要がある外部キーを構成するときに提供されるジェネリック パラメーターに注意してください`HasForeignKey`で以下のリスト。</span><span class="sxs-lookup"><span data-stu-id="1523c-195">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="1523c-196">一対多リレーションシップでは、参照ナビゲーションを使用したエンティティは、の依存しており、コレクションを使用して 1 つは、プリンシパルには明らかです。</span><span class="sxs-lookup"><span data-stu-id="1523c-196">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="1523c-197">はありませんが、一対一リレーションシップのためを明示的に定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1523c-197">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

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

### <a name="many-to-many"></a><span data-ttu-id="1523c-198">多対多</span><span class="sxs-lookup"><span data-stu-id="1523c-198">Many-to-many</span></span>

<span data-ttu-id="1523c-199">結合テーブルを表すエンティティ クラスを持たない多対多リレーションシップはまだサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="1523c-199">Many-to-many relationships without an entity class to represent the join table are not yet supported.</span></span> <span data-ttu-id="1523c-200">ただし、結合テーブルとのマッピングで独立した 2 つの一対多リレーションシップのエンティティ クラスを含めることによって、多対多リレーションシップを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="1523c-200">However, you can represent a many-to-many relationship by including an entity class for the join table and mapping two separate one-to-many relationships.</span></span>

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
