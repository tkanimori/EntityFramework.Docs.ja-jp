---
title: 所有エンティティ型 - EF Core
description: Entity Framework Core を使用するときに所有されているエンティティ型または集計を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: da4a459fbc40010fc14190204c8ed66fe0495b84
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413985"
---
# <a name="owned-entity-types"></a><span data-ttu-id="e45e3-103">所有エンティティ型</span><span class="sxs-lookup"><span data-stu-id="e45e3-103">Owned Entity Types</span></span>

<span data-ttu-id="e45e3-104">EF Core を使用すると、他のエンティティ型のナビゲーションプロパティにのみ表示されるエンティティ型をモデル化できます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="e45e3-105">これらは、_所有エンティティ型_と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-105">These are called _owned entity types_.</span></span> <span data-ttu-id="e45e3-106">所有エンティティ型を含むエンティティは、その_所有者_です。</span><span class="sxs-lookup"><span data-stu-id="e45e3-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="e45e3-107">所有されているエンティティは、本質的に所有者の一部であり、存在しない場合は、概念的には[集計](https://martinfowler.com/bliki/DDD_Aggregate.html)に似ています。</span><span class="sxs-lookup"><span data-stu-id="e45e3-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span> <span data-ttu-id="e45e3-108">つまり、所有されているエンティティは、所有者とのリレーションシップの依存側の定義によって決まります。</span><span class="sxs-lookup"><span data-stu-id="e45e3-108">This means that the owned entity is by definition on the dependent side of the relationship with the owner.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="e45e3-109">明示的な構成</span><span class="sxs-lookup"><span data-stu-id="e45e3-109">Explicit configuration</span></span>

<span data-ttu-id="e45e3-110">所有エンティティ型は、規則によってモデル内の EF Core によって含まれることはありません。</span><span class="sxs-lookup"><span data-stu-id="e45e3-110">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="e45e3-111">`OnModelCreating` で `OwnsOne` メソッドを使用するか `OwnedAttribute` (EF Core 2.1 の新形式) を使用して型に注釈を付け、型を所有型として構成することができます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-111">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="e45e3-112">この例では、`StreetAddress` は id プロパティのない型です。</span><span class="sxs-lookup"><span data-stu-id="e45e3-112">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="e45e3-113">特定の注文の配送先住所を指定するために、Order 型のプロパティとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-113">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="e45e3-114">この `OwnedAttribute` を使用すると、別のエンティティ型から参照された場合に所有エンティティとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-114">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="e45e3-115">`OnModelCreating` の `OwnsOne` メソッドを使用して、`ShippingAddress` プロパティが `Order` エンティティ型の所有エンティティであることを指定し、必要に応じて追加のファセットを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-115">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="e45e3-116">`ShippingAddress` プロパティが `Order` 型でプライベートである場合は、`OwnsOne` メソッドの文字列バージョンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-116">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="e45e3-117">詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e45e3-117">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span>

## <a name="implicit-keys"></a><span data-ttu-id="e45e3-118">暗黙のキー</span><span class="sxs-lookup"><span data-stu-id="e45e3-118">Implicit keys</span></span>

<span data-ttu-id="e45e3-119">`OwnsOne` で構成されている、または参照ナビゲーションで検出された所有型は、常に所有者との一対一のリレーションシップを持ちます。したがって、外部キーの値が一意であるため、独自のキー値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e45e3-119">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="e45e3-120">前の例では、`StreetAddress` 型でキープロパティを定義する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e45e3-120">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="e45e3-121">EF Core がこれらのオブジェクトを追跡する方法を理解するには、主キーが所有型の[シャドウプロパティ](xref:core/modeling/shadow-properties)として作成されていることを把握しておくと便利です。</span><span class="sxs-lookup"><span data-stu-id="e45e3-121">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="e45e3-122">所有されている型のインスタンスのキーの値は、所有者インスタンスのキーの値と同じになります。</span><span class="sxs-lookup"><span data-stu-id="e45e3-122">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="e45e3-123">所有型のコレクション</span><span class="sxs-lookup"><span data-stu-id="e45e3-123">Collections of owned types</span></span>

> [!NOTE]
> <span data-ttu-id="e45e3-124">これは EF Core 2.2 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="e45e3-124">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="e45e3-125">所有されている型のコレクションを構成するには `OnModelCreating`で `OwnsMany` を使用します。</span><span class="sxs-lookup"><span data-stu-id="e45e3-125">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="e45e3-126">所有型には主キーが必要です。</span><span class="sxs-lookup"><span data-stu-id="e45e3-126">Owned types need a primary key.</span></span> <span data-ttu-id="e45e3-127">.NET 型に適切な候補のプロパティがない場合は、EF Core で作成を試みることができます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-127">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="e45e3-128">ただし、所有型がコレクションによって定義されている場合は、所有しているインスタンスの所有者と主キーの両方として機能する shadow プロパティを作成するだけでは十分ではありません。所有者ごとに所有する型インスタンスが複数存在する可能性があります。したがっ `OwnsOne`て、所有しているインスタンスごとに一意の id</span><span class="sxs-lookup"><span data-stu-id="e45e3-128">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="e45e3-129">これには、次の2つの最も簡単な解決策があります。</span><span class="sxs-lookup"><span data-stu-id="e45e3-129">The two most straightforward solutions to this are:</span></span>

- <span data-ttu-id="e45e3-130">所有者を参照する外部キーとは独立して、新しいプロパティに代理主キーを定義する。</span><span class="sxs-lookup"><span data-stu-id="e45e3-130">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="e45e3-131">含まれている値はすべての所有者間で一意である必要があります (たとえば、親 {1} に子 {1}がある場合、親 {2} に子 {1}を指定することはできません)。そのため、値には固有の意味がありません。</span><span class="sxs-lookup"><span data-stu-id="e45e3-131">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="e45e3-132">外部キーは主キーの一部ではないため、その値を変更できます。したがって、子をある親から別の親に移動できます。ただし、これは通常、集計のセマンティクスに対して行われます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-132">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="e45e3-133">外部キーと追加のプロパティを複合キーとして使用する。</span><span class="sxs-lookup"><span data-stu-id="e45e3-133">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="e45e3-134">追加のプロパティ値は、指定された親に対してのみ一意である必要があります (したがって、親 {1} に子 {1,1} がある場合、親 {2} に子 {2,1}を持つことができます)。</span><span class="sxs-lookup"><span data-stu-id="e45e3-134">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="e45e3-135">主キーの外部キー部分を作成することによって、所有者と所有されているエンティティの間のリレーションシップが変更不可になり、集計セマンティクスがより適切に反映されます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-135">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="e45e3-136">既定では、これが EF Core になります。</span><span class="sxs-lookup"><span data-stu-id="e45e3-136">This is what EF Core does by default.</span></span>

<span data-ttu-id="e45e3-137">この例では、`Distributor` クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="e45e3-137">In this example we'll use the `Distributor` class:</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="e45e3-138">既定では、`ShippingCenters` ナビゲーションプロパティを介して参照される所有型に使用される主キーが `("DistributorId", "Id")` されます。 `"DistributorId"` が FK で、`"Id"` が一意の `int` 値になります。</span><span class="sxs-lookup"><span data-stu-id="e45e3-138">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="e45e3-139">`HasKey`別の PK 呼び出しを構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="e45e3-139">To configure a different PK call `HasKey`:</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> <span data-ttu-id="e45e3-140">EF Core 3.0 より前の `WithOwner()` メソッドが存在しないため、この呼び出しを削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45e3-140">Before EF Core 3.0 `WithOwner()` method didn't exist so this call should be removed.</span></span> <span data-ttu-id="e45e3-141">また、主キーが自動的に検出されないため、常に指定されています。</span><span class="sxs-lookup"><span data-stu-id="e45e3-141">Also the primary key was not discovered automatically so it always had be specified.</span></span>

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="e45e3-142">所有型のテーブル分割へのマッピング</span><span class="sxs-lookup"><span data-stu-id="e45e3-142">Mapping owned types with table splitting</span></span>

<span data-ttu-id="e45e3-143">リレーショナルデータベースを使用する場合、既定では、参照所有型は、所有者と同じテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-143">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="e45e3-144">そのためには、テーブルを2つの列に分割する必要があります。つまり、所有者のデータを格納するために使用される列と、所有されているエンティティのデータを格納するために使用される列があります。</span><span class="sxs-lookup"><span data-stu-id="e45e3-144">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="e45e3-145">これは、[テーブル分割](table-splitting.md)と呼ばれる一般的な機能です。</span><span class="sxs-lookup"><span data-stu-id="e45e3-145">This is a common feature known as [table splitting](table-splitting.md).</span></span>

<span data-ttu-id="e45e3-146">既定では、EF Core は、 _Navigation_OwnedEntityProperty_のパターンに従って、所有されているエンティティ型のプロパティのデータベース列に名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="e45e3-146">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="e45e3-147">そのため、`StreetAddress` のプロパティは、' ShippingAddress_Street ' と ' ShippingAddress_City ' という名前の ' Orders ' テーブルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-147">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="e45e3-148">`HasColumnName` メソッドを使用して、これらの列の名前を変更できます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-148">You can use the `HasColumnName` method to rename those columns:</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> <span data-ttu-id="e45e3-149">[Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore)などの通常のエンティティ型構成メソッドは、ほとんどの場合、同じ方法で呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-149">Most of the normal entity type configuration methods like [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) can be called in the same way.</span></span>

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="e45e3-150">複数の所有型間で同じ .NET 型を共有する</span><span class="sxs-lookup"><span data-stu-id="e45e3-150">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="e45e3-151">所有エンティティ型できます別の所有エンティティ型と同じ .NET 型したがって .NET 型できない可能性があります所有型を識別するために十分な。</span><span class="sxs-lookup"><span data-stu-id="e45e3-151">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="e45e3-152">そのような場合は、所有者から所有されているエンティティを指すプロパティが、所有されているエンティティ型の_ナビゲーションを定義_するようになります。</span><span class="sxs-lookup"><span data-stu-id="e45e3-152">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="e45e3-153">EF Core の観点から見ると、定義のナビゲーションは、.NET 型と共に型の id の一部になります。</span><span class="sxs-lookup"><span data-stu-id="e45e3-153">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>

<span data-ttu-id="e45e3-154">たとえば、次のクラスの `ShippingAddress` と `BillingAddress` は両方とも同じ .NET 型 `StreetAddress`ます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-154">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`:</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="e45e3-155">EF Core がこれらのオブジェクトの追跡対象インスタンスを区別する方法を理解するために、定義ナビゲーションが、所有者のキーの値と所有型の .NET 型の値と共にインスタンスのキーの一部になることを考えると役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="e45e3-155">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="e45e3-156">入れ子になった所有型</span><span class="sxs-lookup"><span data-stu-id="e45e3-156">Nested owned types</span></span>

<span data-ttu-id="e45e3-157">この例では `OrderDetails` `BillingAddress` と `ShippingAddress`を所有しています。これらの型は両方とも `StreetAddress` です。</span><span class="sxs-lookup"><span data-stu-id="e45e3-157">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="e45e3-158">また、`OrderDetails` は `DetailedOrder` 型に所有されています。</span><span class="sxs-lookup"><span data-stu-id="e45e3-158">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="e45e3-159">所有型に移動するたびに、完全に独立した構成で個別のエンティティ型が定義されます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-159">Each navigation to an owned type defines a separate entity type with completely independent configuration.</span></span>

<span data-ttu-id="e45e3-160">入れ子になった所有型に加えて、所有型は標準エンティティを参照できます。所有されているエンティティが依存側にある限り、所有者または別のエンティティのいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="e45e3-160">In addition to nested owned types, an owned type can reference a regular entity, it can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="e45e3-161">この機能は、EF6 の複合型とは別の所有エンティティ型を設定します。</span><span class="sxs-lookup"><span data-stu-id="e45e3-161">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="e45e3-162">Fluent 呼び出しで `OwnsOne` メソッドをチェーンして、このモデルを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-162">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="e45e3-163">所有者を指すナビゲーションプロパティを構成するために使用される `WithOwner` の呼び出しに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e45e3-163">Notice the `WithOwner` call used to configure the navigation property pointing back at the owner.</span></span> <span data-ttu-id="e45e3-164">所有権の関係に含まれていない所有者のエンティティ型へのナビゲーションを構成するには `WithOwner()` を引数なしで呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45e3-164">To configure a navigation to the owner entity type that's not part of the ownership relationship `WithOwner()` should be called without any arguments.</span></span>

<span data-ttu-id="e45e3-165">`OrderDetails` と `StreetAddress`の両方で `OwnedAttribute` を使用して結果を得ることができます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-165">It is possible to achieve the result using `OwnedAttribute` on both `OrderDetails` and `StreetAddress`.</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="e45e3-166">個別のテーブルへの所有型の格納</span><span class="sxs-lookup"><span data-stu-id="e45e3-166">Storing owned types in separate tables</span></span>

<span data-ttu-id="e45e3-167">また、EF6 複合型とは異なり、所有型は所有者とは別のテーブルに格納できます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-167">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="e45e3-168">所有型を所有者と同じテーブルにマップする規則をオーバーライドするには、単に `ToTable` を呼び出して、別のテーブル名を指定します。</span><span class="sxs-lookup"><span data-stu-id="e45e3-168">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="e45e3-169">次の例では、`OrderDetails` とその2つのアドレスを `DetailedOrder`から別のテーブルにマップします。</span><span class="sxs-lookup"><span data-stu-id="e45e3-169">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

<span data-ttu-id="e45e3-170">また、`TableAttribute` を使用してこれを行うこともできますが、所有されている型に複数のナビゲーションがある場合は、複数のエンティティ型が同じテーブルにマップされるため、この操作は失敗することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e45e3-170">It is also possible to use the `TableAttribute` to accomplish this, but note that this would fail if there are multiple navigations to the owned type since in that case multiple entity types would be mapped to the same table.</span></span>

## <a name="querying-owned-types"></a><span data-ttu-id="e45e3-171">所有型のクエリ</span><span class="sxs-lookup"><span data-stu-id="e45e3-171">Querying owned types</span></span>

<span data-ttu-id="e45e3-172">所有者に問い合わせるとき、所有されている型が既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="e45e3-172">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="e45e3-173">所有型が別のテーブルに格納されている場合でも、`Include` メソッドを使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e45e3-173">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="e45e3-174">次のクエリでは、前に説明したモデルに基づいて、`Order`、`OrderDetails`、およびデータベースから所有されている2つの `StreetAddresses` を取得します。</span><span class="sxs-lookup"><span data-stu-id="e45e3-174">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="e45e3-175">制限事項</span><span class="sxs-lookup"><span data-stu-id="e45e3-175">Limitations</span></span>

<span data-ttu-id="e45e3-176">これらの制限事項のいくつかは、所有エンティティ型の動作のしくみに関するものですが、他のいくつかは将来のリリースで削除できる可能性がある制限です。</span><span class="sxs-lookup"><span data-stu-id="e45e3-176">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="e45e3-177">設計上の制限</span><span class="sxs-lookup"><span data-stu-id="e45e3-177">By-design restrictions</span></span>

- <span data-ttu-id="e45e3-178">所有型の `DbSet<T>` を作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="e45e3-178">You cannot create a `DbSet<T>` for an owned type</span></span>
- <span data-ttu-id="e45e3-179">`ModelBuilder` で所有型を使用して `Entity<T>()` を呼び出すことはできません</span><span class="sxs-lookup"><span data-stu-id="e45e3-179">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="e45e3-180">現在の欠点</span><span class="sxs-lookup"><span data-stu-id="e45e3-180">Current shortcomings</span></span>

- <span data-ttu-id="e45e3-181">所有エンティティ型に継承階層を含めることはできません</span><span class="sxs-lookup"><span data-stu-id="e45e3-181">Owned entity types cannot have inheritance hierarchies</span></span>
- <span data-ttu-id="e45e3-182">所有されているエンティティ型への参照ナビゲーションは、所有者から別のテーブルに明示的にマップされていない限り、null にすることはできません</span><span class="sxs-lookup"><span data-stu-id="e45e3-182">Reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="e45e3-183">(これは、所有エンティティ型を使用して実装することはできません。 値オブジェクトのよく知られているシナリオ) 複数の所有者で所有エンティティ型のインスタンスを共有することはできません。</span><span class="sxs-lookup"><span data-stu-id="e45e3-183">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="e45e3-184">以前のバージョンの欠点</span><span class="sxs-lookup"><span data-stu-id="e45e3-184">Shortcomings in previous versions</span></span>

- <span data-ttu-id="e45e3-185">EF Core 2.0 では、所有されているエンティティ型へのナビゲーションは、所有しているエンティティが所有者階層から別のテーブルに明示的にマップされている場合を除き、派生エンティティ型で宣言することはできません。</span><span class="sxs-lookup"><span data-stu-id="e45e3-185">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="e45e3-186">この制限は EF Core 2.1 で削除されました</span><span class="sxs-lookup"><span data-stu-id="e45e3-186">This limitation has been removed in EF Core 2.1</span></span>
- <span data-ttu-id="e45e3-187">EF Core 2.0 および2.1 では、所有型へのナビゲーションのみがサポートされていました。</span><span class="sxs-lookup"><span data-stu-id="e45e3-187">In EF Core 2.0 and 2.1 only reference navigations to owned types were supported.</span></span> <span data-ttu-id="e45e3-188">この制限は EF Core 2.2 で削除されました</span><span class="sxs-lookup"><span data-stu-id="e45e3-188">This limitation has been removed in EF Core 2.2</span></span>
