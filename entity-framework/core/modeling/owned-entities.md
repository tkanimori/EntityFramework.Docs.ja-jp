---
title: 所有エンティティ型 - EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
uid: core/modeling/owned-entities
ms.openlocfilehash: f69bae2de28156876e0aa57376b5dfac053adb9c
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149142"
---
# <a name="owned-entity-types"></a><span data-ttu-id="68c55-102">所有エンティティ型</span><span class="sxs-lookup"><span data-stu-id="68c55-102">Owned Entity Types</span></span>

>[!NOTE]
> <span data-ttu-id="68c55-103">この機能は EF Core 2.0 で新たに追加されています。</span><span class="sxs-lookup"><span data-stu-id="68c55-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="68c55-104">EF Core を使用すると、他のエンティティ型のナビゲーションプロパティにのみ表示されるエンティティ型をモデル化できます。</span><span class="sxs-lookup"><span data-stu-id="68c55-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="68c55-105">これらは、_所有エンティティ型_と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="68c55-105">These are called _owned entity types_.</span></span> <span data-ttu-id="68c55-106">所有エンティティ型を含むエンティティはその_所有者_します。</span><span class="sxs-lookup"><span data-stu-id="68c55-106">The entity containing an owned entity type is its _owner_.</span></span>

<span data-ttu-id="68c55-107">所有されているエンティティは、本質的に所有者の一部であり、存在しない場合は、概念的には[集計](https://martinfowler.com/bliki/DDD_Aggregate.html)に似ています。</span><span class="sxs-lookup"><span data-stu-id="68c55-107">Owned entities are essentially a part of the owner and cannot exist without it, they are conceptually similar to [aggregates](https://martinfowler.com/bliki/DDD_Aggregate.html).</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="68c55-108">明示的な構成</span><span class="sxs-lookup"><span data-stu-id="68c55-108">Explicit configuration</span></span>

<span data-ttu-id="68c55-109">所有エンティティ型は、規則によってモデル内の EF Core によって含まれることはありません。</span><span class="sxs-lookup"><span data-stu-id="68c55-109">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="68c55-110">で`OwnsOne` `OwnedAttribute`メソッドを使用するか、(EF Core 2.1 の新) を使用して型に注釈を付け、型を所有型として構成することができます。 `OnModelCreating`</span><span class="sxs-lookup"><span data-stu-id="68c55-110">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="68c55-111">この例では`StreetAddress` 、は id プロパティのない型です。</span><span class="sxs-lookup"><span data-stu-id="68c55-111">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="68c55-112">特定の注文の配送先住所を指定するために、Order 型のプロパティとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="68c55-112">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="68c55-113">別のエンティティ型`OwnedAttribute`から参照された場合は、を使用して所有エンティティとして扱うことができます。</span><span class="sxs-lookup"><span data-stu-id="68c55-113">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="68c55-114">また`OwnsOne` 、の`OnModelCreating`メソッドを使用して、 `ShippingAddress`プロパティが`Order`エンティティ型の所有エンティティであることを指定したり、必要に応じて追加のファセットを構成したりすることもできます。</span><span class="sxs-lookup"><span data-stu-id="68c55-114">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="68c55-115">プロパティが`Order`型でプライベートである場合は、 `OwnsOne`メソッドの文字列バージョンを使用できます。 `ShippingAddress`</span><span class="sxs-lookup"><span data-stu-id="68c55-115">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="68c55-116">詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="68c55-116">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span> 

## <a name="implicit-keys"></a><span data-ttu-id="68c55-117">暗黙のキー</span><span class="sxs-lookup"><span data-stu-id="68c55-117">Implicit keys</span></span>

<span data-ttu-id="68c55-118">参照ナビゲーションを使用`OwnsOne`して構成された所有型は、常に所有者との一対一のリレーションシップを持ちます。したがって、外部キーの値が一意であるため、独自のキー値は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="68c55-118">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="68c55-119">前の例では、 `StreetAddress`型はキープロパティを定義する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="68c55-119">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="68c55-120">EF Core がこれらのオブジェクトを追跡する方法を理解するには、主キーが所有型の[シャドウプロパティ](xref:core/modeling/shadow-properties)として作成されていることを把握しておくと便利です。</span><span class="sxs-lookup"><span data-stu-id="68c55-120">In order to understand how EF Core tracks these objects, it is useful to know that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="68c55-121">所有されている型のインスタンスのキーの値は、所有者インスタンスのキーの値と同じになります。</span><span class="sxs-lookup"><span data-stu-id="68c55-121">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="68c55-122">所有型のコレクション</span><span class="sxs-lookup"><span data-stu-id="68c55-122">Collections of owned types</span></span>

> [!NOTE]
> <span data-ttu-id="68c55-123">これは EF Core 2.2 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="68c55-123">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="68c55-124">所有されている型のコレクション`OwnsMany`を`OnModelCreating`構成するには、でを使用します。</span><span class="sxs-lookup"><span data-stu-id="68c55-124">To configure a collection of owned types use `OwnsMany` in `OnModelCreating`.</span></span>

<span data-ttu-id="68c55-125">所有型には主キーが必要です。</span><span class="sxs-lookup"><span data-stu-id="68c55-125">Owned types need a primary key.</span></span> <span data-ttu-id="68c55-126">.NET 型に適切な候補のプロパティがない場合は、EF Core で作成を試みることができます。</span><span class="sxs-lookup"><span data-stu-id="68c55-126">If there are no good candidates properties on the .NET type, EF Core can try to create one.</span></span> <span data-ttu-id="68c55-127">ただし、所有型がコレクションによって定義されている場合は、外部キーと所有インスタンス`OwnsOne`の主キーの両方として機能する shadow プロパティを作成するだけでは十分ではありません。所有している型のインスタンスは複数存在する可能性があります。各所有者は、所有しているインスタンスごとに一意の id を提供するのに十分ではありません。</span><span class="sxs-lookup"><span data-stu-id="68c55-127">However, when owned types are defined through a collection, it isn't enough to just create a shadow property to act as both the foreign key into the owner and the primary key of the owned instance, as we do for `OwnsOne`: there can be multiple owned type instances for each owner, and hence the key of the owner isn't enough to provide a unique identity for each owned instance.</span></span>

<span data-ttu-id="68c55-128">これには、次の2つの最も簡単な解決策があります。</span><span class="sxs-lookup"><span data-stu-id="68c55-128">The two most straightforward solutions to this are:</span></span>
- <span data-ttu-id="68c55-129">所有者を参照する外部キーとは独立して、新しいプロパティに代理主キーを定義する。</span><span class="sxs-lookup"><span data-stu-id="68c55-129">Defining a surrogate primary key on a new property independent of the foreign key that points to the owner.</span></span> <span data-ttu-id="68c55-130">含まれている値はすべての所有者間で一意である必要{1}があり{1}ます (親{2}が子を{1}持っている場合は、親が子を持つことができます)。したがって、値には固有の意味がありません。</span><span class="sxs-lookup"><span data-stu-id="68c55-130">The contained values would need to be unique across all owners (e.g. if Parent {1} has Child {1}, then Parent {2} cannot have Child {1}), so the value doesn't have any inherent meaning.</span></span> <span data-ttu-id="68c55-131">外部キーは主キーの一部ではないため、その値を変更できます。したがって、子をある親から別の親に移動できます。ただし、これは通常、集計のセマンティクスに対して行われます。</span><span class="sxs-lookup"><span data-stu-id="68c55-131">Since the foreign key is not part of the primary key its values can be changed, so you could move a child from one parent to another one, however this usually goes against aggregate semantics.</span></span>
- <span data-ttu-id="68c55-132">外部キーと追加のプロパティを複合キーとして使用する。</span><span class="sxs-lookup"><span data-stu-id="68c55-132">Using the foreign key and an additional property as a composite key.</span></span> <span data-ttu-id="68c55-133">追加のプロパティ値は、指定された親に対してのみ一意である{1}必要があります{2} (したがって、 {2,1}親に子{1,1}がある場合、親は子を持つことができます)。</span><span class="sxs-lookup"><span data-stu-id="68c55-133">The additional property value now only needs to be unique for a given parent (so if Parent {1} has Child {1,1} then Parent {2} can still have Child {2,1}).</span></span> <span data-ttu-id="68c55-134">主キーの外部キー部分を作成することによって、所有者と所有されているエンティティの間のリレーションシップが変更不可になり、集計セマンティクスがより適切に反映されます。</span><span class="sxs-lookup"><span data-stu-id="68c55-134">By making the foreign key part of the primary key the relationship between the owner and the owned entity becomes immutable and reflects aggregate semantics better.</span></span> <span data-ttu-id="68c55-135">既定では、これが EF Core になります。</span><span class="sxs-lookup"><span data-stu-id="68c55-135">This is what EF Core does by default.</span></span>

<span data-ttu-id="68c55-136">この例では、 `Distributor`クラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="68c55-136">In this example we'll use the `Distributor` class:</span></span>

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

<span data-ttu-id="68c55-137">既定`ShippingCenters`では、ナビゲーションプロパティで参照される所有型に使用される主キー `"DistributorId"` `("DistributorId", "Id")`はになります`"Id"` 。ここで`int` 、は FK で、は一意の値です。</span><span class="sxs-lookup"><span data-stu-id="68c55-137">By default the primary key used for the owned type referenced through the `ShippingCenters` navigation property will be `("DistributorId", "Id")` where `"DistributorId"` is the FK and `"Id"` is a unique `int` value.</span></span>

<span data-ttu-id="68c55-138">異なる PK 呼び出し`HasKey`を構成するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="68c55-138">To configure a different PK call `HasKey`:</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> <span data-ttu-id="68c55-139">EF Core 3.0 `WithOwner()`メソッドが存在しない場合は、この呼び出しを削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="68c55-139">Before EF Core 3.0 `WithOwner()` method didn't exist so this call should be removed.</span></span>

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="68c55-140">所有型のテーブル分割へのマッピング</span><span class="sxs-lookup"><span data-stu-id="68c55-140">Mapping owned types with table splitting</span></span>

<span data-ttu-id="68c55-141">リレーショナルデータベースを使用する場合、既定では、参照所有型は、所有者と同じテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="68c55-141">When using relational databases, by default reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="68c55-142">そのためには、テーブルを2つの列に分割する必要があります。つまり、所有者のデータを格納するために使用される列と、所有されているエンティティのデータを格納するために使用される列があります。</span><span class="sxs-lookup"><span data-stu-id="68c55-142">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="68c55-143">これは、[テーブル分割](table-splitting.md)と呼ばれる一般的な機能です。</span><span class="sxs-lookup"><span data-stu-id="68c55-143">This is a common feature known as [table splitting](table-splitting.md).</span></span>

<span data-ttu-id="68c55-144">既定では、EF Core は、 _Navigation_OwnedEntityProperty_パターンに従って、所有されているエンティティ型のプロパティのデータベース列に名前を設定します。</span><span class="sxs-lookup"><span data-stu-id="68c55-144">By default, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="68c55-145">そのため、' Orders ' テーブルには、' ShippingAddress_Street ' および ' ShippingAddress_City ' という名前のプロパティが表示されます。`StreetAddress`</span><span class="sxs-lookup"><span data-stu-id="68c55-145">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="68c55-146">メソッドを使用し`HasColumnName`て、これらの列の名前を変更できます。</span><span class="sxs-lookup"><span data-stu-id="68c55-146">You can use the `HasColumnName` method to rename those columns:</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="68c55-147">複数の所有型間で同じ .NET 型を共有する</span><span class="sxs-lookup"><span data-stu-id="68c55-147">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="68c55-148">所有エンティティ型できます別の所有エンティティ型と同じ .NET 型したがって .NET 型できない可能性があります所有型を識別するために十分な。</span><span class="sxs-lookup"><span data-stu-id="68c55-148">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="68c55-149">その場合、所有者から所有エンティティを指すプロパティになります、_ナビゲーションを定義する_の所有エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="68c55-149">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="68c55-150">EF Core の観点から見ると、定義のナビゲーションは、.NET 型と共に型の id の一部になります。</span><span class="sxs-lookup"><span data-stu-id="68c55-150">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>   

<span data-ttu-id="68c55-151">たとえば、次のクラス`ShippingAddress`では、と`BillingAddress`の両方が同じ .net 型`StreetAddress`です。</span><span class="sxs-lookup"><span data-stu-id="68c55-151">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`:</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="68c55-152">EF Core がこれらのオブジェクトの追跡対象インスタンスを区別する方法を理解するために、定義ナビゲーションが、所有者のキーの値と所有型の .NET 型の値と共にインスタンスのキーの一部になることを考えると役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="68c55-152">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="68c55-153">入れ子になった所有型</span><span class="sxs-lookup"><span data-stu-id="68c55-153">Nested owned types</span></span>

<span data-ttu-id="68c55-154">この例`OrderDetails`では、 `ShippingAddress`と (両方`StreetAddress`の型) を所有`BillingAddress`しています。</span><span class="sxs-lookup"><span data-stu-id="68c55-154">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="68c55-155">また、`OrderDetails` は `DetailedOrder` 型に所有されています。</span><span class="sxs-lookup"><span data-stu-id="68c55-155">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="68c55-156">入れ子になった所有型に加えて、所有型は標準エンティティを参照できます。所有されているエンティティが依存側にある限り、所有者または別のエンティティのいずれかになります。</span><span class="sxs-lookup"><span data-stu-id="68c55-156">In addition to nested owned types, an owned type can reference a regular entity, it can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="68c55-157">この機能は、EF6 の複合型とは別の所有エンティティ型を設定します。</span><span class="sxs-lookup"><span data-stu-id="68c55-157">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="68c55-158">このモデルを構成するに`OwnsOne`は、fluent 呼び出しでメソッドをチェーンすることができます。</span><span class="sxs-lookup"><span data-stu-id="68c55-158">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="68c55-159">所有者`WithOwner`を指すナビゲーションプロパティを構成するための呼び出しが使用されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="68c55-159">Notice the `WithOwner` call used to configure the navigation property pointing back at the owner.</span></span>

<span data-ttu-id="68c55-160">と`OwnedAttribute` `OrderDetails`の両方でを使用して結果を実現することができます。 `StreetAdress`</span><span class="sxs-lookup"><span data-stu-id="68c55-160">It is possible to achieve the result using `OwnedAttribute` on both `OrderDetails` and `StreetAdress`.</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="68c55-161">個別のテーブルへの所有型の格納</span><span class="sxs-lookup"><span data-stu-id="68c55-161">Storing owned types in separate tables</span></span>

<span data-ttu-id="68c55-162">また、EF6 複合型とは異なり、所有型は所有者とは別のテーブルに格納できます。</span><span class="sxs-lookup"><span data-stu-id="68c55-162">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="68c55-163">所有型を所有者と同じテーブルにマップする規則をオーバーライドするには、を呼び出し`ToTable`て別のテーブル名を指定するだけです。</span><span class="sxs-lookup"><span data-stu-id="68c55-163">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="68c55-164">次の例では`OrderDetails` 、とその2つのアドレスをから`DetailedOrder`別のテーブルにマップします。</span><span class="sxs-lookup"><span data-stu-id="68c55-164">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

## <a name="querying-owned-types"></a><span data-ttu-id="68c55-165">所有型のクエリ</span><span class="sxs-lookup"><span data-stu-id="68c55-165">Querying owned types</span></span>

<span data-ttu-id="68c55-166">所有者に問い合わせるとき、所有されている型が既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="68c55-166">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="68c55-167">所有されている型が`Include`別のテーブルに格納されている場合でも、メソッドを使用する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="68c55-167">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="68c55-168">前に説明したモデルに基づいて、次の`Order`クエリ`OrderDetails`は、データベースから`StreetAddresses`所有されている2つのクエリを取得します。</span><span class="sxs-lookup"><span data-stu-id="68c55-168">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="68c55-169">制限事項</span><span class="sxs-lookup"><span data-stu-id="68c55-169">Limitations</span></span>

<span data-ttu-id="68c55-170">これらの制限事項のいくつかは、所有エンティティ型の動作のしくみに関するものですが、他のいくつかは将来のリリースで削除できる可能性がある制限です。</span><span class="sxs-lookup"><span data-stu-id="68c55-170">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="68c55-171">設計上の制限</span><span class="sxs-lookup"><span data-stu-id="68c55-171">By-design restrictions</span></span>
- <span data-ttu-id="68c55-172">所有型`DbSet<T>`のを作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="68c55-172">You cannot create a `DbSet<T>` for an owned type</span></span>
- <span data-ttu-id="68c55-173">で所有型`Entity<T>()`を使用してを呼び出すことはできません。`ModelBuilder`</span><span class="sxs-lookup"><span data-stu-id="68c55-173">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="68c55-174">現在の欠点</span><span class="sxs-lookup"><span data-stu-id="68c55-174">Current shortcomings</span></span>
- <span data-ttu-id="68c55-175">所有エンティティ型を含む継承階層はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="68c55-175">Inheritance hierarchies that include owned entity types are not supported</span></span>
- <span data-ttu-id="68c55-176">所有されているエンティティ型への参照ナビゲーションは、所有者から別のテーブルに明示的にマップされていない限り、null にすることはできません</span><span class="sxs-lookup"><span data-stu-id="68c55-176">Reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="68c55-177">(これは、所有エンティティ型を使用して実装することはできません。 値オブジェクトのよく知られているシナリオ) 複数の所有者で所有エンティティ型のインスタンスを共有することはできません。</span><span class="sxs-lookup"><span data-stu-id="68c55-177">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="68c55-178">以前のバージョンの欠点</span><span class="sxs-lookup"><span data-stu-id="68c55-178">Shortcomings in previous versions</span></span>
- <span data-ttu-id="68c55-179">EF Core 2.0 では、所有されているエンティティ型へのナビゲーションは、所有しているエンティティが所有者階層から別のテーブルに明示的にマップされている場合を除き、派生エンティティ型で宣言することはできません。</span><span class="sxs-lookup"><span data-stu-id="68c55-179">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="68c55-180">この制限は EF Core 2.1 で削除されました</span><span class="sxs-lookup"><span data-stu-id="68c55-180">This limitation has been removed in EF Core 2.1</span></span>
- <span data-ttu-id="68c55-181">EF Core 2.0 および2.1 では、所有型へのナビゲーションのみがサポートされていました。</span><span class="sxs-lookup"><span data-stu-id="68c55-181">In EF Core 2.0 and 2.1 only reference navigations to owned types were supported.</span></span> <span data-ttu-id="68c55-182">この制限は EF Core 2.2 で削除されました</span><span class="sxs-lookup"><span data-stu-id="68c55-182">This limitation has been removed in EF Core 2.2</span></span>
