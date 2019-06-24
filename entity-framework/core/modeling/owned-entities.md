---
title: 所有エンティティ型 - EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
uid: core/modeling/owned-entities
ms.openlocfilehash: b2d72b08de79939904bf4e726c695440c906a8aa
ms.sourcegitcommit: 06073f8efde97dd5f540dbfb69f574d8380566fe
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67333795"
---
# <a name="owned-entity-types"></a><span data-ttu-id="8b53d-102">所有エンティティ型</span><span class="sxs-lookup"><span data-stu-id="8b53d-102">Owned Entity Types</span></span>

>[!NOTE]
> <span data-ttu-id="8b53d-103">この機能は、EF Core 2.0 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="8b53d-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="8b53d-104">EF Core では、その他のエンティティ型のナビゲーション プロパティにのみ表示できるモデルのエンティティ型にできます。</span><span class="sxs-lookup"><span data-stu-id="8b53d-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="8b53d-105">これらと呼ばれます_エンティティ型を所有している_します。</span><span class="sxs-lookup"><span data-stu-id="8b53d-105">These are called _owned entity types_.</span></span> <span data-ttu-id="8b53d-106">所有エンティティ型を含むエンティティはその_所有者_します。</span><span class="sxs-lookup"><span data-stu-id="8b53d-106">The entity containing an owned entity type is its _owner_.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="8b53d-107">明示的な構成</span><span class="sxs-lookup"><span data-stu-id="8b53d-107">Explicit configuration</span></span>

<span data-ttu-id="8b53d-108">エンティティ型は含まれません EF Core によって、モデルの規則により所有されています。</span><span class="sxs-lookup"><span data-stu-id="8b53d-108">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="8b53d-109">使用することができます、`OwnsOne`メソッド`OnModelCreating`を持つ型の注釈を設定または`OwnedAttribute`(EF Core 2.1 の新機能) 所有型として型を構成します。</span><span class="sxs-lookup"><span data-stu-id="8b53d-109">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="8b53d-110">この例で`StreetAddress`は identity プロパティは含まずの一種です。</span><span class="sxs-lookup"><span data-stu-id="8b53d-110">In this example, `StreetAddress` is a type with no identity property.</span></span> <span data-ttu-id="8b53d-111">特定の注文の配送先住所を指定するために、Order 型のプロパティとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="8b53d-111">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span>

<span data-ttu-id="8b53d-112">使用して、`OwnedAttribute`別のエンティティ型から参照されている場合、所有エンティティとして扱う。</span><span class="sxs-lookup"><span data-stu-id="8b53d-112">We can use the `OwnedAttribute` to treat it as an owned entity when referenced from another entity type:</span></span>

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

<span data-ttu-id="8b53d-113">使用することも、`OwnsOne`メソッド`OnModelCreating`ことを指定する、`ShippingAddress`プロパティの所有エンティティは、`Order`エンティティ型と必要な場合、その他のファセットを構成します。</span><span class="sxs-lookup"><span data-stu-id="8b53d-113">It is also possible to use the `OwnsOne` method in `OnModelCreating` to specify that the `ShippingAddress` property is an Owned Entity of the `Order` entity type and to configure additional facets if needed.</span></span>

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

<span data-ttu-id="8b53d-114">場合、`ShippingAddress`プロパティがプライベートで、`Order`型の文字列バージョンを使用することができます、`OwnsOne`メソッド。</span><span class="sxs-lookup"><span data-stu-id="8b53d-114">If the `ShippingAddress` property is private in the `Order` type, you can use the string version of the `OwnsOne` method:</span></span>

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

<span data-ttu-id="8b53d-115">参照してください、[完全なサンプル プロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)詳細なコンテキスト。</span><span class="sxs-lookup"><span data-stu-id="8b53d-115">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities) for more context.</span></span> 

## <a name="implicit-keys"></a><span data-ttu-id="8b53d-116">暗黙的なキー</span><span class="sxs-lookup"><span data-stu-id="8b53d-116">Implicit keys</span></span>

<span data-ttu-id="8b53d-117">所有型で構成されている`OwnsOne`参照ナビゲーションで検出された、または一対一のリレーションシップの所有者と常に、外部キーの値が一意である独自のキー値を必要なしないためです。</span><span class="sxs-lookup"><span data-stu-id="8b53d-117">Owned types configured with `OwnsOne` or discovered through a reference navigation always have a one-to-one relationship with the owner, therefore they don't need their own key values as the foreign key values are unique.</span></span> <span data-ttu-id="8b53d-118">前の例では、`StreetAddress`型がキー プロパティを定義する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8b53d-118">In the previous example, the `StreetAddress` type does not need to define a key property.</span></span>  

<span data-ttu-id="8b53d-119">EF Core がこれらのオブジェクトを追跡する方法を理解するためにとして主キーが作成されたことを考えるとわかり、[プロパティをシャドウ](xref:core/modeling/shadow-properties)所有型のです。</span><span class="sxs-lookup"><span data-stu-id="8b53d-119">In order to understand how EF Core tracks these objects, it is useful to think that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="8b53d-120">所有型のインスタンスのキーの値は、所有者のインスタンスのキーの値と同じになります。</span><span class="sxs-lookup"><span data-stu-id="8b53d-120">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>

## <a name="collections-of-owned-types"></a><span data-ttu-id="8b53d-121">所有型のコレクション</span><span class="sxs-lookup"><span data-stu-id="8b53d-121">Collections of owned types</span></span>

>[!NOTE]
> <span data-ttu-id="8b53d-122">これは EF Core 2.2 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="8b53d-122">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="8b53d-123">所有型のコレクションを構成する`OwnsMany`で使用する必要があります`OnModelCreating`します。</span><span class="sxs-lookup"><span data-stu-id="8b53d-123">To configure a collection of owned types `OwnsMany` should be used in `OnModelCreating`.</span></span> <span data-ttu-id="8b53d-124">ただし、プライマリ キーは構成されません規則により、明示的に指定する必要があるためです。</span><span class="sxs-lookup"><span data-stu-id="8b53d-124">However the primary key will not be configured by convention, so it needs to be specified explicitly.</span></span> <span data-ttu-id="8b53d-125">このタイプのエンティティの所有者とシャドウ状態にすることもある追加の固有プロパティへの外部キーを組み込むことの複雑なキーを使用して、一般的には。</span><span class="sxs-lookup"><span data-stu-id="8b53d-125">It is common to use a complex key for these type of entities incorporating the foreign key to the owner and an additional unique property that can also be in shadow state:</span></span>

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="8b53d-126">マッピングは、テーブル分割を持つ型を所有しています。</span><span class="sxs-lookup"><span data-stu-id="8b53d-126">Mapping owned types with table splitting</span></span>

<span data-ttu-id="8b53d-127">リレーショナルを使用する場合、データベース規則参照によって所有されている型、所有者と同じテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="8b53d-127">When using relational databases, by convention reference owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="8b53d-128">2 つのテーブルを分割する必要があります。 一部の列は、所有者のデータの格納に使用すると、所有エンティティのデータの格納に使用されるいくつかの列。</span><span class="sxs-lookup"><span data-stu-id="8b53d-128">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="8b53d-129">これは、テーブル分割と呼ばれる一般的な機能です。</span><span class="sxs-lookup"><span data-stu-id="8b53d-129">This is a common feature known as table splitting.</span></span>

> [!TIP]
> <span data-ttu-id="8b53d-130">テーブル分割と保存されている種類は、所有する EF6 で使用する方法の複合型にも同様に使用します。</span><span class="sxs-lookup"><span data-stu-id="8b53d-130">Owned types stored with table splitting can be used similarly to how complex types are used in EF6.</span></span>

<span data-ttu-id="8b53d-131">規則により、EF Core は、次のパターンの所有鉛てぃてぃ型のプロパティのデータベース列を名前は_Navigation_OwnedEntityProperty_します。</span><span class="sxs-lookup"><span data-stu-id="8b53d-131">By convention, EF Core will name the database columns for the properties of the owned entity type following the pattern _Navigation_OwnedEntityProperty_.</span></span> <span data-ttu-id="8b53d-132">そのため、`StreetAddress`プロパティ名 'ShippingAddress_Street' と 'ShippingAddress_City' と 'Orders' テーブルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="8b53d-132">Therefore the `StreetAddress` properties will appear in the 'Orders' table with the names 'ShippingAddress_Street' and 'ShippingAddress_City'.</span></span>

<span data-ttu-id="8b53d-133">追加することができます、`HasColumnName`それらの列の名前を変更するメソッド。</span><span class="sxs-lookup"><span data-stu-id="8b53d-133">You can append the `HasColumnName` method to rename those columns:</span></span>

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="8b53d-134">複数の所有型の間で同じ .NET 型の共有</span><span class="sxs-lookup"><span data-stu-id="8b53d-134">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="8b53d-135">所有エンティティ型できます別の所有エンティティ型と同じ .NET 型したがって .NET 型できない可能性があります所有型を識別するために十分な。</span><span class="sxs-lookup"><span data-stu-id="8b53d-135">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="8b53d-136">その場合、所有者から所有エンティティを指すプロパティになります、_ナビゲーションを定義する_の所有エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="8b53d-136">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="8b53d-137">EF Core の観点からは、定義となるナビゲーションは、.NET 型と型の id の一部です。</span><span class="sxs-lookup"><span data-stu-id="8b53d-137">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>   

<span data-ttu-id="8b53d-138">たとえば、次のクラスで`ShippingAddress`と`BillingAddress`はどちらも同じの .NET 型の`StreetAddress`:</span><span class="sxs-lookup"><span data-stu-id="8b53d-138">For example, in the following class `ShippingAddress` and `BillingAddress` are both of the same .NET type, `StreetAddress`:</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="8b53d-139">EF Core がこれらのオブジェクトの追跡対象のインスタンスを区別する方法を理解するためには、定義となるナビゲーションが所有者のキーの値と共にインスタンスのキーの一部と所有型の .NET 型になったことを考えるとわかり場合があります。</span><span class="sxs-lookup"><span data-stu-id="8b53d-139">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="8b53d-140">入れ子にされた所有型</span><span class="sxs-lookup"><span data-stu-id="8b53d-140">Nested owned types</span></span>

<span data-ttu-id="8b53d-141">この例では`OrderDetails`所有`BillingAddress`と`ShippingAddress`、これは、どちらも`StreetAddress`型。</span><span class="sxs-lookup"><span data-stu-id="8b53d-141">In this example `OrderDetails` owns `BillingAddress` and `ShippingAddress`, which are both `StreetAddress` types.</span></span> <span data-ttu-id="8b53d-142">また、`OrderDetails` は `DetailedOrder` 型に所有されています。</span><span class="sxs-lookup"><span data-stu-id="8b53d-142">Then `OrderDetails` is owned by the `DetailedOrder` type.</span></span>

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

<span data-ttu-id="8b53d-143">入れ子になったの所有型だけでなく所有型は、正規のエンティティを参照できますでは、所有者または別のエンティティとして所有エンティティは、依存側の。</span><span class="sxs-lookup"><span data-stu-id="8b53d-143">In addition to nested owned types, an owned type can reference a regular entity, it can be either the owner or a different entity as long as the owned entity is on the dependent side.</span></span> <span data-ttu-id="8b53d-144">この機能は、EF6 の複合型とは別の所有エンティティ型を設定します。</span><span class="sxs-lookup"><span data-stu-id="8b53d-144">This capability sets owned entity types apart from complex types in EF6.</span></span>

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

<span data-ttu-id="8b53d-145">チェーンすることができます、`OwnsOne`メソッドでこのモデルを構成する fluent 呼び出し。</span><span class="sxs-lookup"><span data-stu-id="8b53d-145">It is possible to chain the `OwnsOne` method in a fluent call to configure this model:</span></span>

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

<span data-ttu-id="8b53d-146">使用して同じことを実現することも`OwnedAttribute`両方で`OrderDetails`と`StreetAdress`します。</span><span class="sxs-lookup"><span data-stu-id="8b53d-146">It is also possible to achieve the same thing using `OwnedAttribute` on both `OrderDetails` and `StreetAdress`.</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="8b53d-147">別のテーブルに所有型を格納します。</span><span class="sxs-lookup"><span data-stu-id="8b53d-147">Storing owned types in separate tables</span></span>

<span data-ttu-id="8b53d-148">また、EF6 の複合型とは異なりは、所有型を所有者から別のテーブルに格納できます。</span><span class="sxs-lookup"><span data-stu-id="8b53d-148">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="8b53d-149">所有型を所有者と同じテーブルにマップする規則を上書きするために呼び出すだけでできます`ToTable`別のテーブルの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="8b53d-149">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="8b53d-150">次の例はマップ`OrderDetails`と、その 2 つのアドレスから別のテーブルに`DetailedOrder`:</span><span class="sxs-lookup"><span data-stu-id="8b53d-150">The following example will map `OrderDetails` and its two addresses to a separate table from `DetailedOrder`:</span></span>

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

## <a name="querying-owned-types"></a><span data-ttu-id="8b53d-151">所有型を問い合わせています</span><span class="sxs-lookup"><span data-stu-id="8b53d-151">Querying owned types</span></span>

<span data-ttu-id="8b53d-152">所有者に問い合わせるとき、所有されている型が既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="8b53d-152">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="8b53d-153">使用する必要はありません、`Include`所有型が別のテーブルに格納されている場合でも、メソッド。</span><span class="sxs-lookup"><span data-stu-id="8b53d-153">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="8b53d-154">次のクエリによって取得される前に説明したモデルに基づいて、 `Order`、`OrderDetails`と所有している 2 つ`StreetAddresses`データベースから。</span><span class="sxs-lookup"><span data-stu-id="8b53d-154">Based on the model described before, the following query will get `Order`, `OrderDetails` and the two owned `StreetAddresses` from the database:</span></span>

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a><span data-ttu-id="8b53d-155">制限事項</span><span class="sxs-lookup"><span data-stu-id="8b53d-155">Limitations</span></span>

<span data-ttu-id="8b53d-156">これらの制限の一部は所有されているどのエンティティ型の作業を基本的な機能がいくつか他のユーザー制限は今後のリリースを削除するいただく可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8b53d-156">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="8b53d-157">設計による制限</span><span class="sxs-lookup"><span data-stu-id="8b53d-157">By-design restrictions</span></span>
- <span data-ttu-id="8b53d-158">作成することはできません、`DbSet<T>`所有型の</span><span class="sxs-lookup"><span data-stu-id="8b53d-158">You cannot create a `DbSet<T>` for an owned type</span></span>
- <span data-ttu-id="8b53d-159">呼び出すことはできません`Entity<T>()`所有型の使用 `ModelBuilder`</span><span class="sxs-lookup"><span data-stu-id="8b53d-159">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="8b53d-160">現在の欠点</span><span class="sxs-lookup"><span data-stu-id="8b53d-160">Current shortcomings</span></span>
- <span data-ttu-id="8b53d-161">継承階層を含む所有エンティティ型はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="8b53d-161">Inheritance hierarchies that include owned entity types are not supported</span></span>
- <span data-ttu-id="8b53d-162">参照のナビゲーションは、明示的に、所有者から別のテーブルにマップされていないエンティティ型を null にすることはできませんを所有しています。</span><span class="sxs-lookup"><span data-stu-id="8b53d-162">Reference navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="8b53d-163">(これは、所有エンティティ型を使用して実装することはできません。 値オブジェクトのよく知られているシナリオ) 複数の所有者で所有エンティティ型のインスタンスを共有することはできません。</span><span class="sxs-lookup"><span data-stu-id="8b53d-163">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="8b53d-164">以前のバージョンの欠点</span><span class="sxs-lookup"><span data-stu-id="8b53d-164">Shortcomings in previous versions</span></span>
- <span data-ttu-id="8b53d-165">EF Core 2.0 では、ナビゲーションには、所有エンティティが別のテーブルに所有者の階層から明示的にマップされていない限り、派生エンティティ型のエンティティ型を宣言できませんを所有します。</span><span class="sxs-lookup"><span data-stu-id="8b53d-165">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="8b53d-166">EF Core 2.1 でこの制限が削除されました</span><span class="sxs-lookup"><span data-stu-id="8b53d-166">This limitation has been removed in EF Core 2.1</span></span>
- <span data-ttu-id="8b53d-167">EF Core 2.0 および 2.1 の唯一の参照では、所有型にナビゲーションがサポートされていました。</span><span class="sxs-lookup"><span data-stu-id="8b53d-167">In EF Core 2.0 and 2.1 only reference navigations to owned types were supported.</span></span> <span data-ttu-id="8b53d-168">EF Core 2.2 でこの制限が削除されました</span><span class="sxs-lookup"><span data-stu-id="8b53d-168">This limitation has been removed in EF Core 2.2</span></span>
