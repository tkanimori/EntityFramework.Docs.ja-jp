---
title: 所有されているエンティティ型-EF Core
description: Entity Framework Core を使用するときに所有されているエンティティ型または集計を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: f65c07c79daf38e733c76f328843c90466c657f5
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619328"
---
# <a name="owned-entity-types"></a><span data-ttu-id="a3e09-103">所有されているエンティティ型</span><span class="sxs-lookup"><span data-stu-id="a3e09-103">Owned Entity Types</span></span>

<span data-ttu-id="a3e09-104">EF Core を使用すると、他のエンティティ型のナビゲーションプロパティにのみ表示されるエンティティ型をモデル化できます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="a3e09-105">これらは、 _所有エンティティ型_と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-105">These are called _owned entity types_.</span></span> <span data-ttu-id="a3e09-106">所有エンティティ型を含むエンティティは、その _所有者_です。</span><span class="sxs-lookup"><span data-stu-id="a3e09-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="a3e09-107">所有されているエンティティは、本質的に所有者の一部であり、存在しない場合は、概念的には [集計](https://martinfowler.com/bliki/DDD_Aggregate.html)に似ています。</span><span class="sxs-lookup"><span data-stu-id="a3e09-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span> <span data-ttu-id="a3e09-108">つまり、所有されているエンティティは、所有者とのリレーションシップの依存側の定義によって決まります。</span><span class="sxs-lookup"><span data-stu-id="a3e09-108">This means that the owned entity is by definition on the dependent side of the relationship with the owner.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="a3e09-109">明示的な構成</span><span class="sxs-lookup"><span data-stu-id="a3e09-109">Explicit configuration</span></span>

<span data-ttu-id="a3e09-110">所有エンティティ型は、規則によってモデル内の EF Core によって含まれることはありません。</span><span class="sxs-lookup"><span data-stu-id="a3e09-110">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="a3e09-111">でメソッドを使用する `OwnsOne` `OnModelCreating` か、(EF Core 2.1 の新) を使用して型に注釈を付け、型を `OwnedAttribute` 所有型として構成することができます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-111">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="a3e09-112">この例で `StreetAddress` は、は id プロパティのない型です。</span><span class="sxs-lookup"><span data-stu-id="a3e09-112">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="a3e09-113">特定の注文の配送先住所を指定するために、Order 型のプロパティとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-113">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="a3e09-114">別のエンティティ型から参照された場合は、を使用して `OwnedAttribute` 所有エンティティとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-114">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="a3e09-115">また、のメソッドを使用して、 `OwnsOne` `OnModelCreating` `ShippingAddress` プロパティがエンティティ型の所有エンティティであることを指定したり、必要に応じて `Order` 追加のファセットを構成したりすることもできます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-115">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="a3e09-116">`ShippingAddress`プロパティが型でプライベートである場合は、 `Order` メソッドの文字列バージョンを使用でき `OwnsOne` ます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-116">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="a3e09-117">詳細なコンテキストについては、 [完全なサンプルプロジェクト](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a3e09-117">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span>

## <a name="implicit-keys"></a><span data-ttu-id="a3e09-118">暗黙のキー</span><span class="sxs-lookup"><span data-stu-id="a3e09-118">Implicit keys</span></span>

<span data-ttu-id="a3e09-119">参照ナビゲーションを使用して構成された所有型 `OwnsOne` は、常に所有者との一対一のリレーションシップを持ちます。したがって、外部キーの値が一意であるため、独自のキー値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a3e09-119">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="a3e09-120">前の例では、 `StreetAddress` 型はキープロパティを定義する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a3e09-120">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="a3e09-121">EF Core がこれらのオブジェクトを追跡する方法を理解するには、主キーが所有型の [シャドウプロパティ](xref:core/modeling/shadow-properties) として作成されていることを把握しておくと便利です。</span><span class="sxs-lookup"><span data-stu-id="a3e09-121">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="a3e09-122">所有されている型のインスタンスのキーの値は、所有者インスタンスのキーの値と同じになります。</span><span class="sxs-lookup"><span data-stu-id="a3e09-122">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="a3e09-123">所有型のコレクション</span><span class="sxs-lookup"><span data-stu-id="a3e09-123">Collections of owned types</span></span>

> [!NOTE]
> <span data-ttu-id="a3e09-124">これは EF Core 2.2 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="a3e09-124">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="a3e09-125">所有されている型のコレクションを構成するには、でを使用 `OwnsMany` `OnModelCreating` します。</span><span class="sxs-lookup"><span data-stu-id="a3e09-125">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="a3e09-126">所有型には主キーが必要です。</span><span class="sxs-lookup"><span data-stu-id="a3e09-126">Owned types need a primary key.</span></span> <span data-ttu-id="a3e09-127">.NET 型に適切な候補のプロパティがない場合は、EF Core で作成を試みることができます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-127">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="a3e09-128">ただし、所有する型がコレクションによって定義されている場合は、所有しているインスタンスの外部キーと主キーの両方として機能する shadow プロパティを作成するだけでは十分ではありません `OwnsOne` 。所有者ごとに所有型インスタンスが複数存在する可能性があります</span><span class="sxs-lookup"><span data-stu-id="a3e09-128">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="a3e09-129">これには、次の2つの最も簡単な解決策があります。</span><span class="sxs-lookup"><span data-stu-id="a3e09-129">The two most straightforward solutions to this are:</span></span>

- <span data-ttu-id="a3e09-130">所有者を参照する外部キーとは独立して、新しいプロパティに代理主キーを定義する。</span><span class="sxs-lookup"><span data-stu-id="a3e09-130">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="a3e09-131">含まれている値はすべての所有者間で一意である必要があります (親 {1} が子を持っている場合は {1} 、親が子を持つ {2} ことができます)。したがって {1} 、値には固有の意味がありません。</span><span class="sxs-lookup"><span data-stu-id="a3e09-131">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="a3e09-132">外部キーは主キーの一部ではないため、その値を変更できます。したがって、子をある親から別の親に移動できます。ただし、これは通常、集計のセマンティクスに対して行われます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-132">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="a3e09-133">外部キーと追加のプロパティを複合キーとして使用する。</span><span class="sxs-lookup"><span data-stu-id="a3e09-133">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="a3e09-134">追加のプロパティ値は、指定された親に対してのみ一意である必要があります (したがって、親 {1} に子がある場合、 {1,1} 親は子を {2} 持つことができ {2,1} ます)。</span><span class="sxs-lookup"><span data-stu-id="a3e09-134">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="a3e09-135">主キーの外部キー部分を作成することによって、所有者と所有されているエンティティの間のリレーションシップが変更不可になり、集計セマンティクスがより適切に反映されます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-135">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="a3e09-136">既定では、これが EF Core になります。</span><span class="sxs-lookup"><span data-stu-id="a3e09-136">This is what EF Core does by default.</span></span>

<span data-ttu-id="a3e09-137">この例では、クラスを使用し `Distributor` ます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-137">In this example we'll use the `Distributor` class:</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="a3e09-138">既定では、ナビゲーションプロパティで参照される所有型に使用される主キーはになり `ShippingCenters` `("DistributorId", "Id")` ます。ここで、 `"DistributorId"` は FK で、 `"Id"` は一意の `int` 値です。</span><span class="sxs-lookup"><span data-stu-id="a3e09-138">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="a3e09-139">異なる PK 呼び出しを構成するには、次のようにし `HasKey` ます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-139">To configure a different PK call `HasKey`:</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> <span data-ttu-id="a3e09-140">EF Core 3.0 `WithOwner()` メソッドが存在しない場合は、この呼び出しを削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a3e09-140">Before EF Core 3.0 `WithOwner()` method didn't exist so this call should be removed.</span></span> <span data-ttu-id="a3e09-141">また、主キーが自動的に検出されないため、常に指定する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="a3e09-141">Also the primary key was not discovered automatically so it always had to be specified.</span></span>

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="a3e09-142">所有型のテーブル分割へのマッピング</span><span class="sxs-lookup"><span data-stu-id="a3e09-142">Mapping owned types with table splitting</span></span>

<span data-ttu-id="a3e09-143">リレーショナルデータベースを使用する場合、既定では、参照所有型は、所有者と同じテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-143">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="a3e09-144">そのためには、テーブルを2つの列に分割する必要があります。つまり、所有者のデータを格納するために使用される列と、所有されているエンティティのデータを格納するために使用される列があります。</span><span class="sxs-lookup"><span data-stu-id="a3e09-144">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="a3e09-145">これは、 [テーブル分割](xref:core/modeling/table-splitting)と呼ばれる一般的な機能です。</span><span class="sxs-lookup"><span data-stu-id="a3e09-145">This is a common feature known as [table splitting](xref:core/modeling/table-splitting).</span></span>

<span data-ttu-id="a3e09-146">既定では、EF Core は、 _Navigation_OwnedEntityProperty_のパターンに従って、所有されているエンティティ型のプロパティのデータベース列に名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="a3e09-146">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="a3e09-147">そのため、' `StreetAddress` Orders ' テーブルには ' ShippingAddress_Street ' と ' ShippingAddress_City ' という名前のプロパティが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-147">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="a3e09-148">メソッドを使用して、 `HasColumnName` これらの列の名前を変更できます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-148">You can use the `HasColumnName` method to rename those columns:</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> <span data-ttu-id="a3e09-149">[Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore)などの通常のエンティティ型構成メソッドは、ほとんどの場合、同じ方法で呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-149">Most of the normal entity type configuration methods like [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore) can be called in the same way.</span></span>

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="a3e09-150">複数の所有型間で同じ .NET 型を共有する</span><span class="sxs-lookup"><span data-stu-id="a3e09-150">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="a3e09-151">所有されているエンティティ型は、他の所有エンティティ型と同じ .NET 型にすることができます。したがって、.NET 型は所有型を特定するのに十分ではない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a3e09-151">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="a3e09-152">そのような場合は、所有者から所有されているエンティティを指すプロパティが、所有されているエンティティ型の _ナビゲーションを定義_ するようになります。</span><span class="sxs-lookup"><span data-stu-id="a3e09-152">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="a3e09-153">EF Core の観点から見ると、定義のナビゲーションは、.NET 型と共に型の id の一部になります。</span><span class="sxs-lookup"><span data-stu-id="a3e09-153">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>

<span data-ttu-id="a3e09-154">たとえば、次のクラスで `ShippingAddress` は、との `BillingAddress` 両方が同じ .net 型です `StreetAddress` 。</span><span class="sxs-lookup"><span data-stu-id="a3e09-154">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`:</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="a3e09-155">EF Core がこれらのオブジェクトの追跡対象インスタンスを区別する方法を理解するために、定義ナビゲーションが、所有者のキーの値と所有型の .NET 型の値と共にインスタンスのキーの一部になることを考えると役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="a3e09-155">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="a3e09-156">入れ子になった所有型</span><span class="sxs-lookup"><span data-stu-id="a3e09-156">Nested owned types</span></span>

<span data-ttu-id="a3e09-157">この例で `OrderDetails` `BillingAddress` `ShippingAddress` は、と (両方の型) を所有して `StreetAddress` います。</span><span class="sxs-lookup"><span data-stu-id="a3e09-157">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="a3e09-158">また、`OrderDetails` は `DetailedOrder` 型に所有されています。</span><span class="sxs-lookup"><span data-stu-id="a3e09-158">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="a3e09-159">所有型に移動するたびに、完全に独立した構成で個別のエンティティ型が定義されます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-159">Each navigation to an owned type defines a separate entity type with completely independent configuration.</span></span>

<span data-ttu-id="a3e09-160">所有されている型は、入れ子になった所有型に加えて、所有されているエンティティが依存側にある限り、所有者または別のエンティティのいずれかになることがある標準エンティティを参照できます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-160">In addition to nested owned types, an owned type can reference a regular entity which can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="a3e09-161">この機能は、EF6 の複合型とは別に、所有エンティティ型を設定します。</span><span class="sxs-lookup"><span data-stu-id="a3e09-161">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

## <a name="configuring-owned-types"></a><span data-ttu-id="a3e09-162">所有する型の構成</span><span class="sxs-lookup"><span data-stu-id="a3e09-162">Configuring Owned Types</span></span>

<span data-ttu-id="a3e09-163">`OwnsOne`このモデルを構成するには、fluent 呼び出しでメソッドをチェーンすることができます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-163">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="a3e09-164">`WithOwner`所有者を指すナビゲーションプロパティを定義するための呼び出しが使用されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a3e09-164">Notice the `WithOwner` call used to define the navigation property pointing back at the owner.</span></span> <span data-ttu-id="a3e09-165">所有権に含まれていない所有者のエンティティ型へのナビゲーションを定義するには、 `WithOwner()` 引数を指定せずにを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="a3e09-165">To define a navigation to the owner entity type that's not part of the ownership relationship `WithOwner()` should be called without any arguments.</span></span>

<span data-ttu-id="a3e09-166">また `OwnedAttribute` 、との両方でを使用して、この結果を実現することもでき `OrderDetails` `StreetAddress` ます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-166">It is also possible to achieve this result using `OwnedAttribute` on both `OrderDetails` and `StreetAddress`.</span></span>

<span data-ttu-id="a3e09-167">また、の呼び出しに注意して `Navigation` ください。</span><span class="sxs-lookup"><span data-stu-id="a3e09-167">In addition, notice the `Navigation` call.</span></span> <span data-ttu-id="a3e09-168">EFCore 5.0 では、所有されている型へのナビゲーションプロパティを、所有され [ていないナビゲーションプロパティのため](xref:core/modeling/relationships#configuring-navigation-properties)にさらに構成できます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-168">In EFCore 5.0, navigation properties to owned types can be further configured [as for non-owned navigation properties](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="a3e09-169">個別のテーブルへの所有型の格納</span><span class="sxs-lookup"><span data-stu-id="a3e09-169">Storing owned types in separate tables</span></span>

<span data-ttu-id="a3e09-170">また、EF6 複合型とは異なり、所有型は所有者とは別のテーブルに格納できます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-170">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="a3e09-171">所有型を所有者と同じテーブルにマップする規則をオーバーライドするには、 `ToTable` を呼び出して別のテーブル名を指定するだけです。</span><span class="sxs-lookup"><span data-stu-id="a3e09-171">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="a3e09-172">次の例では、 `OrderDetails` とその2つのアドレスをから別のテーブルにマップし `DetailedOrder` ます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-172">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

<span data-ttu-id="a3e09-173">また、を使用してこれを行うこともできますが、所有されている `TableAttribute` 型に複数のナビゲーションがある場合は、複数のエンティティ型が同じテーブルにマップされるため、これは失敗することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a3e09-173">It is also possible to use the `TableAttribute` to accomplish this, but note that this would fail if there are multiple navigations to the owned type since in that case multiple entity types would be mapped to the same table.</span></span>

## <a name="querying-owned-types"></a><span data-ttu-id="a3e09-174">所有型のクエリ</span><span class="sxs-lookup"><span data-stu-id="a3e09-174">Querying owned types</span></span>

<span data-ttu-id="a3e09-175">所有者に問い合わせるとき、所有されている型が既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-175">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="a3e09-176">所有されている `Include` 型が別のテーブルに格納されている場合でも、メソッドを使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a3e09-176">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="a3e09-177">前に説明したモデルに基づいて、次のクエリは、データベースから所有されている2つのクエリを取得し `Order` `OrderDetails` `StreetAddresses` ます。</span><span class="sxs-lookup"><span data-stu-id="a3e09-177">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="a3e09-178">制限事項</span><span class="sxs-lookup"><span data-stu-id="a3e09-178">Limitations</span></span>

<span data-ttu-id="a3e09-179">これらの制限事項のいくつかは、所有エンティティ型の動作のしくみに関するものですが、他のいくつかは将来のリリースで削除できる可能性がある制限です。</span><span class="sxs-lookup"><span data-stu-id="a3e09-179">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="a3e09-180">設計上の制限</span><span class="sxs-lookup"><span data-stu-id="a3e09-180">By-design restrictions</span></span>

- <span data-ttu-id="a3e09-181">所有型のを作成することはできません。 `DbSet<T>`</span><span class="sxs-lookup"><span data-stu-id="a3e09-181">You cannot create a `DbSet<T>` for an owned type</span></span>
- <span data-ttu-id="a3e09-182">`Entity<T>()`で所有型を使用してを呼び出すことはできません。`ModelBuilder`</span><span class="sxs-lookup"><span data-stu-id="a3e09-182">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="a3e09-183">現在の欠点</span><span class="sxs-lookup"><span data-stu-id="a3e09-183">Current shortcomings</span></span>

- <span data-ttu-id="a3e09-184">所有エンティティ型に継承階層を含めることはできません</span><span class="sxs-lookup"><span data-stu-id="a3e09-184">Owned entity types cannot have inheritance hierarchies</span></span>
- <span data-ttu-id="a3e09-185">所有されているエンティティ型への参照ナビゲーションは、所有者から別のテーブルに明示的にマップされていない限り、null にすることはできません</span><span class="sxs-lookup"><span data-stu-id="a3e09-185">Reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="a3e09-186">所有されているエンティティ型のインスタンスを複数の所有者が共有することはできません (これは、所有エンティティ型を使用して実装できない値オブジェクトの既知のシナリオです)</span><span class="sxs-lookup"><span data-stu-id="a3e09-186">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="a3e09-187">以前のバージョンの欠点</span><span class="sxs-lookup"><span data-stu-id="a3e09-187">Shortcomings in previous versions</span></span>

- <span data-ttu-id="a3e09-188">EF Core 2.0 では、所有されているエンティティ型へのナビゲーションは、所有しているエンティティが所有者階層から別のテーブルに明示的にマップされている場合を除き、派生エンティティ型で宣言することはできません。</span><span class="sxs-lookup"><span data-stu-id="a3e09-188">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="a3e09-189">この制限は EF Core 2.1 で削除されました</span><span class="sxs-lookup"><span data-stu-id="a3e09-189">This limitation has been removed in EF Core 2.1</span></span>
- <span data-ttu-id="a3e09-190">EF Core 2.0 および2.1 では、所有型へのナビゲーションのみがサポートされていました。</span><span class="sxs-lookup"><span data-stu-id="a3e09-190">In EF Core 2.0 and 2.1 only reference navigations to owned types were supported.</span></span> <span data-ttu-id="a3e09-191">この制限は EF Core 2.2 で削除されました</span><span class="sxs-lookup"><span data-stu-id="a3e09-191">This limitation has been removed in EF Core 2.2</span></span>
