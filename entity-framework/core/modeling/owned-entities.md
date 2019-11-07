---
title: 所有されているエンティティ型-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
uid: core/modeling/owned-entities
ms.openlocfilehash: a0665bfa27134b8dc3eba854ff3f7b1af4b69217
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655937"
---
# <a name="owned-entity-types"></a><span data-ttu-id="51d0b-102">所有されているエンティティ型</span><span class="sxs-lookup"><span data-stu-id="51d0b-102">Owned Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="51d0b-103">この機能は EF Core 2.0 で新たに追加されています。</span><span class="sxs-lookup"><span data-stu-id="51d0b-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="51d0b-104">EF Core を使用すると、他のエンティティ型のナビゲーションプロパティにのみ表示されるエンティティ型をモデル化できます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="51d0b-105">これらは、_所有エンティティ型_と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-105">These are called _owned entity types_.</span></span> <span data-ttu-id="51d0b-106">所有エンティティ型を含むエンティティは、その_所有者_です。</span><span class="sxs-lookup"><span data-stu-id="51d0b-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="51d0b-107">所有されているエンティティは、本質的に所有者の一部であり、存在しない場合は、概念的には[集計](https://martinfowler.com/bliki/DDD_Aggregate.html)に似ています。</span><span class="sxs-lookup"><span data-stu-id="51d0b-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="51d0b-108">明示的な構成</span><span class="sxs-lookup"><span data-stu-id="51d0b-108">Explicit configuration</span></span>

<span data-ttu-id="51d0b-109">所有エンティティ型は、規則によってモデル内の EF Core によって含まれることはありません。</span><span class="sxs-lookup"><span data-stu-id="51d0b-109">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="51d0b-110">`OnModelCreating` で `OwnsOne` メソッドを使用するか `OwnedAttribute` (EF Core 2.1 の新形式) を使用して型に注釈を付け、型を所有型として構成することができます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-110">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="51d0b-111">この例では、`StreetAddress` は id プロパティのない型です。</span><span class="sxs-lookup"><span data-stu-id="51d0b-111">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="51d0b-112">特定の注文の配送先住所を指定するために、Order 型のプロパティとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-112">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="51d0b-113">この `OwnedAttribute` を使用すると、別のエンティティ型から参照された場合に所有エンティティとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-113">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="51d0b-114">`OnModelCreating` の `OwnsOne` メソッドを使用して、`ShippingAddress` プロパティが `Order` エンティティ型の所有エンティティであることを指定し、必要に応じて追加のファセットを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-114">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="51d0b-115">`ShippingAddress` プロパティが `Order` 型でプライベートである場合は、`OwnsOne` メソッドの文字列バージョンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-115">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="51d0b-116">詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="51d0b-116">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span>

## <a name="implicit-keys"></a><span data-ttu-id="51d0b-117">暗黙のキー</span><span class="sxs-lookup"><span data-stu-id="51d0b-117">Implicit keys</span></span>

<span data-ttu-id="51d0b-118">`OwnsOne` で構成されている、または参照ナビゲーションで検出された所有型は、常に所有者との一対一のリレーションシップを持ちます。したがって、外部キーの値が一意であるため、独自のキー値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="51d0b-118">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="51d0b-119">前の例では、`StreetAddress` 型でキープロパティを定義する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="51d0b-119">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="51d0b-120">EF Core がこれらのオブジェクトを追跡する方法を理解するには、主キーが所有型の[シャドウプロパティ](xref:core/modeling/shadow-properties)として作成されていることを把握しておくと便利です。</span><span class="sxs-lookup"><span data-stu-id="51d0b-120">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="51d0b-121">所有されている型のインスタンスのキーの値は、所有者インスタンスのキーの値と同じになります。</span><span class="sxs-lookup"><span data-stu-id="51d0b-121">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="51d0b-122">所有型のコレクション</span><span class="sxs-lookup"><span data-stu-id="51d0b-122">Collections of owned types</span></span>

> [!NOTE]
> <span data-ttu-id="51d0b-123">これは EF Core 2.2 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="51d0b-123">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="51d0b-124">所有されている型のコレクションを構成するには `OnModelCreating`で `OwnsMany` を使用します。</span><span class="sxs-lookup"><span data-stu-id="51d0b-124">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="51d0b-125">所有型には主キーが必要です。</span><span class="sxs-lookup"><span data-stu-id="51d0b-125">Owned types need a primary key.</span></span> <span data-ttu-id="51d0b-126">.NET 型に適切な候補のプロパティがない場合は、EF Core で作成を試みることができます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-126">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="51d0b-127">ただし、所有型がコレクションによって定義されている場合は、`OwnsOne`の場合と同様に、外部キーと所有インスタンスの主キーの両方として機能する shadow プロパティを作成するだけでは十分ではありません。それぞれに対して所有される型インスタンスが複数存在する可能性があります。所有者であるため、所有者のキーは、所有しているインスタンスごとに一意の id を提供するのに十分ではありません。</span><span class="sxs-lookup"><span data-stu-id="51d0b-127">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="51d0b-128">これには、次の2つの最も簡単な解決策があります。</span><span class="sxs-lookup"><span data-stu-id="51d0b-128">The two most straightforward solutions to this are:</span></span>

- <span data-ttu-id="51d0b-129">所有者を参照する外部キーとは独立して、新しいプロパティに代理主キーを定義する。</span><span class="sxs-lookup"><span data-stu-id="51d0b-129">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="51d0b-130">含まれている値はすべての所有者間で一意である必要があります (たとえば、親 {1} に子 {1}がある場合、親 {2} に子 {1}を指定することはできません)。そのため、値には固有の意味がありません。</span><span class="sxs-lookup"><span data-stu-id="51d0b-130">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="51d0b-131">外部キーは主キーの一部ではないため、その値を変更できます。したがって、子をある親から別の親に移動できます。ただし、これは通常、集計のセマンティクスに対して行われます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-131">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="51d0b-132">外部キーと追加のプロパティを複合キーとして使用する。</span><span class="sxs-lookup"><span data-stu-id="51d0b-132">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="51d0b-133">追加のプロパティ値は、指定された親に対してのみ一意である必要があります (したがって、親 {1} に子 {1,1} がある場合、親 {2} に子 {2,1}を持つことができます)。</span><span class="sxs-lookup"><span data-stu-id="51d0b-133">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="51d0b-134">主キーの外部キー部分を作成することによって、所有者と所有されているエンティティの間のリレーションシップが変更不可になり、集計セマンティクスがより適切に反映されます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-134">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="51d0b-135">既定では、これが EF Core になります。</span><span class="sxs-lookup"><span data-stu-id="51d0b-135">This is what EF Core does by default.</span></span>

<span data-ttu-id="51d0b-136">この例では、`Distributor` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="51d0b-136">In this example we'll use the `Distributor` class:</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="51d0b-137">既定では、`ShippingCenters` ナビゲーションプロパティを介して参照される所有型に使用される主キーが `("DistributorId", "Id")` されます。 `"DistributorId"` が FK で、`"Id"` が一意の `int` 値になります。</span><span class="sxs-lookup"><span data-stu-id="51d0b-137">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="51d0b-138">`HasKey`別の PK 呼び出しを構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="51d0b-138">To configure a different PK call `HasKey`:</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> <span data-ttu-id="51d0b-139">EF Core 3.0 より前の `WithOwner()` メソッドが存在しないため、この呼び出しを削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="51d0b-139">Before EF Core 3.0 `WithOwner()` method didn't exist so this call should be removed.</span></span>

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="51d0b-140">所有型のテーブル分割へのマッピング</span><span class="sxs-lookup"><span data-stu-id="51d0b-140">Mapping owned types with table splitting</span></span>

<span data-ttu-id="51d0b-141">リレーショナルデータベースを使用する場合、既定では、参照所有型は、所有者と同じテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-141">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="51d0b-142">そのためには、テーブルを2つの列に分割する必要があります。つまり、所有者のデータを格納するために使用される列と、所有されているエンティティのデータを格納するために使用される列があります。</span><span class="sxs-lookup"><span data-stu-id="51d0b-142">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="51d0b-143">これは、[テーブル分割](table-splitting.md)と呼ばれる一般的な機能です。</span><span class="sxs-lookup"><span data-stu-id="51d0b-143">This is a common feature known as [table splitting](table-splitting.md).</span></span>

<span data-ttu-id="51d0b-144">既定では、EF Core は、 _Navigation_OwnedEntityProperty_パターンに従って、所有されているエンティティ型のプロパティのデータベース列に名前を設定します。</span><span class="sxs-lookup"><span data-stu-id="51d0b-144">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="51d0b-145">そのため、`StreetAddress` のプロパティは、' ShippingAddress_Street ' と ' ShippingAddress_City ' という名前の ' Orders ' テーブルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-145">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="51d0b-146">`HasColumnName` メソッドを使用して、これらの列の名前を変更できます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-146">You can use the `HasColumnName` method to rename those columns:</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="51d0b-147">複数の所有型間で同じ .NET 型を共有する</span><span class="sxs-lookup"><span data-stu-id="51d0b-147">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="51d0b-148">所有されているエンティティ型は、他の所有エンティティ型と同じ .NET 型にすることができます。したがって、.NET 型は所有型を特定するのに十分ではない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="51d0b-148">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="51d0b-149">そのような場合は、所有者から所有されているエンティティを指すプロパティが、所有されているエンティティ型の_ナビゲーションを定義_するようになります。</span><span class="sxs-lookup"><span data-stu-id="51d0b-149">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="51d0b-150">EF Core の観点から見ると、定義のナビゲーションは、.NET 型と共に型の id の一部になります。</span><span class="sxs-lookup"><span data-stu-id="51d0b-150">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>

<span data-ttu-id="51d0b-151">たとえば、次のクラスの `ShippingAddress` と `BillingAddress` は両方とも同じ .NET 型 `StreetAddress`ます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-151">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`:</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="51d0b-152">EF Core がこれらのオブジェクトの追跡対象インスタンスを区別する方法を理解するために、定義ナビゲーションが、所有者のキーの値と所有型の .NET 型の値と共にインスタンスのキーの一部になることを考えると役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="51d0b-152">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="51d0b-153">入れ子になった所有型</span><span class="sxs-lookup"><span data-stu-id="51d0b-153">Nested owned types</span></span>

<span data-ttu-id="51d0b-154">この例では `OrderDetails` `BillingAddress` と `ShippingAddress`を所有しています。これらの型は両方とも `StreetAddress` です。</span><span class="sxs-lookup"><span data-stu-id="51d0b-154">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="51d0b-155">また、`OrderDetails` は `DetailedOrder` 型に所有されています。</span><span class="sxs-lookup"><span data-stu-id="51d0b-155">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="51d0b-156">入れ子になった所有型に加えて、所有型は標準エンティティを参照できます。所有されているエンティティが依存側にある限り、所有者または別のエンティティのいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="51d0b-156">In addition to nested owned types, an owned type can reference a regular entity, it can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="51d0b-157">この機能は、EF6 の複合型とは別に、所有エンティティ型を設定します。</span><span class="sxs-lookup"><span data-stu-id="51d0b-157">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="51d0b-158">Fluent 呼び出しで `OwnsOne` メソッドをチェーンして、このモデルを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-158">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="51d0b-159">所有者を指すナビゲーションプロパティを構成するために使用される `WithOwner` の呼び出しに注意してください。</span><span class="sxs-lookup"><span data-stu-id="51d0b-159">Notice the `WithOwner` call used to configure the navigation property pointing back at the owner.</span></span>

<span data-ttu-id="51d0b-160">`OrderDetails` と `StreetAdress`の両方で `OwnedAttribute` を使用して結果を得ることができます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-160">It is possible to achieve the result using `OwnedAttribute` on both `OrderDetails` and `StreetAdress`.</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="51d0b-161">個別のテーブルへの所有型の格納</span><span class="sxs-lookup"><span data-stu-id="51d0b-161">Storing owned types in separate tables</span></span>

<span data-ttu-id="51d0b-162">また、EF6 複合型とは異なり、所有型は所有者とは別のテーブルに格納できます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-162">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="51d0b-163">所有型を所有者と同じテーブルにマップする規則をオーバーライドするには、単に `ToTable` を呼び出して、別のテーブル名を指定します。</span><span class="sxs-lookup"><span data-stu-id="51d0b-163">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="51d0b-164">次の例では、`OrderDetails` とその2つのアドレスを `DetailedOrder`から別のテーブルにマップします。</span><span class="sxs-lookup"><span data-stu-id="51d0b-164">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

## <a name="querying-owned-types"></a><span data-ttu-id="51d0b-165">所有型のクエリ</span><span class="sxs-lookup"><span data-stu-id="51d0b-165">Querying owned types</span></span>

<span data-ttu-id="51d0b-166">所有者に問い合わせるとき、所有されている型が既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="51d0b-166">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="51d0b-167">所有型が別のテーブルに格納されている場合でも、`Include` メソッドを使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="51d0b-167">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="51d0b-168">次のクエリでは、前に説明したモデルに基づいて、`Order`、`OrderDetails`、およびデータベースから所有されている2つの `StreetAddresses` を取得します。</span><span class="sxs-lookup"><span data-stu-id="51d0b-168">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="51d0b-169">制限事項</span><span class="sxs-lookup"><span data-stu-id="51d0b-169">Limitations</span></span>

<span data-ttu-id="51d0b-170">これらの制限事項のいくつかは、所有エンティティ型の動作のしくみに関するものですが、他のいくつかは将来のリリースで削除できる可能性がある制限です。</span><span class="sxs-lookup"><span data-stu-id="51d0b-170">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="51d0b-171">設計上の制限</span><span class="sxs-lookup"><span data-stu-id="51d0b-171">By-design restrictions</span></span>

- <span data-ttu-id="51d0b-172">所有型の `DbSet<T>` を作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="51d0b-172">You cannot create a `DbSet<T>` for an owned type</span></span>
- <span data-ttu-id="51d0b-173">`ModelBuilder` で所有型を使用して `Entity<T>()` を呼び出すことはできません</span><span class="sxs-lookup"><span data-stu-id="51d0b-173">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="51d0b-174">現在の欠点</span><span class="sxs-lookup"><span data-stu-id="51d0b-174">Current shortcomings</span></span>

- <span data-ttu-id="51d0b-175">所有エンティティ型を含む継承階層はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="51d0b-175">Inheritance hierarchies that include owned entity types are not supported</span></span>
- <span data-ttu-id="51d0b-176">所有されているエンティティ型への参照ナビゲーションは、所有者から別のテーブルに明示的にマップされていない限り、null にすることはできません</span><span class="sxs-lookup"><span data-stu-id="51d0b-176">Reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="51d0b-177">所有されているエンティティ型のインスタンスを複数の所有者が共有することはできません (これは、所有エンティティ型を使用して実装できない値オブジェクトの既知のシナリオです)</span><span class="sxs-lookup"><span data-stu-id="51d0b-177">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="51d0b-178">以前のバージョンの欠点</span><span class="sxs-lookup"><span data-stu-id="51d0b-178">Shortcomings in previous versions</span></span>

- <span data-ttu-id="51d0b-179">EF Core 2.0 では、所有されているエンティティ型へのナビゲーションは、所有しているエンティティが所有者階層から別のテーブルに明示的にマップされている場合を除き、派生エンティティ型で宣言することはできません。</span><span class="sxs-lookup"><span data-stu-id="51d0b-179">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="51d0b-180">この制限は EF Core 2.1 で削除されました</span><span class="sxs-lookup"><span data-stu-id="51d0b-180">This limitation has been removed in EF Core 2.1</span></span>
- <span data-ttu-id="51d0b-181">EF Core 2.0 および2.1 では、所有型へのナビゲーションのみがサポートされていました。</span><span class="sxs-lookup"><span data-stu-id="51d0b-181">In EF Core 2.0 and 2.1 only reference navigations to owned types were supported.</span></span> <span data-ttu-id="51d0b-182">この制限は EF Core 2.2 で削除されました</span><span class="sxs-lookup"><span data-stu-id="51d0b-182">This limitation has been removed in EF Core 2.2</span></span>
