---
title: 所有エンティティ型 - EF Core
author: julielerman
ms.date: 02/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
uid: core/modeling/owned-entities
ms.openlocfilehash: 1104a8a9a4540e33624fad69c47f2f950c6669bf
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489415"
---
# <a name="owned-entity-types"></a><span data-ttu-id="52880-102">所有エンティティ型</span><span class="sxs-lookup"><span data-stu-id="52880-102">Owned Entity Types</span></span>

>[!NOTE]
> <span data-ttu-id="52880-103">この機能は、EF Core 2.0 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="52880-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="52880-104">EF Core では、その他のエンティティ型のナビゲーション プロパティにのみ表示できるモデルのエンティティ型にできます。</span><span class="sxs-lookup"><span data-stu-id="52880-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="52880-105">これらと呼ばれます_エンティティ型を所有している_します。</span><span class="sxs-lookup"><span data-stu-id="52880-105">These are called _owned entity types_.</span></span> <span data-ttu-id="52880-106">所有エンティティ型を含むエンティティはその_所有者_します。</span><span class="sxs-lookup"><span data-stu-id="52880-106">The entity containing an owned entity type is its _owner_.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="52880-107">明示的な構成</span><span class="sxs-lookup"><span data-stu-id="52880-107">Explicit configuration</span></span>

<span data-ttu-id="52880-108">エンティティ型は含まれません EF Core によって、モデルの規則により所有されています。</span><span class="sxs-lookup"><span data-stu-id="52880-108">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="52880-109">使用することができます、`OwnsOne`メソッド`OnModelCreating`を持つ型の注釈を設定または`OwnedAttribute`(EF Core 2.1 の新機能) 所有型として型を構成します。</span><span class="sxs-lookup"><span data-stu-id="52880-109">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="52880-110">この例では、StreetAddress は identity プロパティは含まずの一種です。</span><span class="sxs-lookup"><span data-stu-id="52880-110">In this example, StreetAddress is a type with no identity property.</span></span> <span data-ttu-id="52880-111">特定の注文の配送先住所を指定するために、Order 型のプロパティとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="52880-111">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span> <span data-ttu-id="52880-112">`OnModelCreating`を使用して、 `OwnsOne` ShippingAddress プロパティが、Order 型の所有エンティティであることを指定するメソッド。</span><span class="sxs-lookup"><span data-stu-id="52880-112">In `OnModelCreating`, we use the `OwnsOne` method to specify that the ShippingAddress property is an Owned Entity of the Order type.</span></span>

``` csharp
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

// OnModelCreating
modelBuilder.Entity<Order>().OwnsOne(p => p.ShippingAddress);
```

<span data-ttu-id="52880-113">ShippingAddress プロパティが Order type でプライベートの場合は、文字列形式を使用することができます、`OwnsOne`メソッド。</span><span class="sxs-lookup"><span data-stu-id="52880-113">If the ShippingAddress property is private in the Order type, you can use the string version of the `OwnsOne` method:</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(typeof(StreetAddress), "ShippingAddress");
```

<span data-ttu-id="52880-114">この例では使用して、`OwnedAttribute`同じ目標を達成します。</span><span class="sxs-lookup"><span data-stu-id="52880-114">In this example, we use the `OwnedAttribute` to achieve the same goal:</span></span>

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="implicit-keys"></a><span data-ttu-id="52880-115">暗黙的なキー</span><span class="sxs-lookup"><span data-stu-id="52880-115">Implicit keys</span></span>

<span data-ttu-id="52880-116">EF Core 2.0 および 2.1 では、参照ナビゲーション プロパティのみが所有型を指定できます。</span><span class="sxs-lookup"><span data-stu-id="52880-116">In EF Core 2.0 and 2.1, only reference navigation properties can point to owned types.</span></span> <span data-ttu-id="52880-117">所有型のコレクションがサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="52880-117">Collections of owned types are not supported.</span></span> <span data-ttu-id="52880-118">これらの参照は所有型が常に一対一のリレーションシップを持つ所有者と、そのため、独自のキー値が不要です。</span><span class="sxs-lookup"><span data-stu-id="52880-118">These reference owned types always have a one-to-one relationship with the owner, therefore they don't need their own key values.</span></span> <span data-ttu-id="52880-119">前の例ではキー プロパティを定義する、StreetAddress 型は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="52880-119">In the previous example, the StreetAddress type does not need to define a key property.</span></span>  

<span data-ttu-id="52880-120">EF Core がこれらのオブジェクトを追跡する方法を理解するためにとして主キーが作成されたことを考えるとわかり、[プロパティをシャドウ](xref:core/modeling/shadow-properties)所有型のです。</span><span class="sxs-lookup"><span data-stu-id="52880-120">In order to understand how EF Core tracks these objects, it is useful to think that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="52880-121">所有型のインスタンスのキーの値は、所有者のインスタンスのキーの値と同じになります。</span><span class="sxs-lookup"><span data-stu-id="52880-121">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>      

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="52880-122">マッピングは、テーブル分割を持つ型を所有しています。</span><span class="sxs-lookup"><span data-stu-id="52880-122">Mapping owned types with table splitting</span></span>

<span data-ttu-id="52880-123">リレーショナル データベースを使用して、慣例所有型にマップされます、所有者と同じテーブル。</span><span class="sxs-lookup"><span data-stu-id="52880-123">When using relational databases, by convention owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="52880-124">2 つのテーブルを分割する必要があります。 一部の列は、所有者のデータの格納に使用すると、所有エンティティのデータの格納に使用されるいくつかの列。</span><span class="sxs-lookup"><span data-stu-id="52880-124">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="52880-125">これは、テーブル分割と呼ばれる一般的な機能です。</span><span class="sxs-lookup"><span data-stu-id="52880-125">This is a common feature known as table splitting.</span></span>

> [!TIP]
> <span data-ttu-id="52880-126">テーブル分割と保存されている種類は、所有する EF6 で使用する方法の複合型にも同様に非常に使用します。</span><span class="sxs-lookup"><span data-stu-id="52880-126">Owned types stored with table splitting can be used very similarly to how complex types are used in EF6.</span></span>

<span data-ttu-id="52880-127">規則により、EF Core は、次のパターンの所有エンティティ型のプロパティのデータベース列を名前は_EntityProperty_OwnedEntityProperty_します。</span><span class="sxs-lookup"><span data-stu-id="52880-127">By convention, EF Core will name the database columns for the properties of the owned entity type following the pattern _EntityProperty_OwnedEntityProperty_.</span></span> <span data-ttu-id="52880-128">そのため、StreetAddress プロパティは、ShippingAddress_Street と ShippingAddress_City 名前と Orders テーブルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="52880-128">Therefore the StreetAddress properties will appear in the Orders table with the names ShippingAddress_Street and ShippingAddress_City.</span></span>

<span data-ttu-id="52880-129">追加することができます、`HasColumnName`それらの列の名前を変更するメソッド。</span><span class="sxs-lookup"><span data-stu-id="52880-129">You can append the `HasColumnName` method to rename those columns.</span></span> <span data-ttu-id="52880-130">StreetAddress のパブリック プロパティがある場合は、マッピングになります</span><span class="sxs-lookup"><span data-stu-id="52880-130">In the case where StreetAddress is a public property, the mappings would be</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(
    o => o.ShippingAddress,
    sa =>
        {
            sa.Property(p=>p.Street).HasColumnName("ShipsToStreet");
            sa.Property(p=>p.City).HasColumnName("ShipsToCity");
        });
```

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="52880-131">複数の所有型の間で同じ .NET 型の共有</span><span class="sxs-lookup"><span data-stu-id="52880-131">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="52880-132">所有エンティティ型できます別の所有エンティティ型と同じ .NET 型したがって .NET 型できない可能性があります所有型を識別するために十分な。</span><span class="sxs-lookup"><span data-stu-id="52880-132">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="52880-133">その場合、所有者から所有エンティティを指すプロパティになります、_ナビゲーションを定義する_の所有エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="52880-133">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="52880-134">EF Core の観点からは、定義となるナビゲーションは、.NET 型と型の id の一部です。</span><span class="sxs-lookup"><span data-stu-id="52880-134">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>   

<span data-ttu-id="52880-135">たとえば、次のクラスで ShippingAddress および BillingAddress は同じ .NET 型、StreetAddress いずれです。</span><span class="sxs-lookup"><span data-stu-id="52880-135">For example, in the following class, ShippingAddress and BillingAddress are both of the same .NET type, StreetAddress:</span></span>

``` csharp
public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
    public StreetAddress BillingAddress { get; set; }
}
```

<span data-ttu-id="52880-136">EF Core がこれらのオブジェクトの追跡対象のインスタンスを区別する方法を理解するためには、定義となるナビゲーションが所有者のキーの値と共にインスタンスのキーの一部と所有型の .NET 型になったことを考えるとわかり場合があります。</span><span class="sxs-lookup"><span data-stu-id="52880-136">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="52880-137">入れ子にされた所有型</span><span class="sxs-lookup"><span data-stu-id="52880-137">Nested owned types</span></span>

<span data-ttu-id="52880-138">この例では、OrderDetails は、BillingAddress と ShippingAddress、これらは両方の StreetAddress 型を所有します。</span><span class="sxs-lookup"><span data-stu-id="52880-138">In this example OrderDetails owns BillingAddress and ShippingAddress, which are both StreetAddress types.</span></span> <span data-ttu-id="52880-139">OrderDetails Order type が所有します。</span><span class="sxs-lookup"><span data-stu-id="52880-139">Then OrderDetails is owned by the Order type.</span></span>

``` csharp
public class Order
{
    public int Id { get; set; }
    public OrderDetails OrderDetails { get; set; }
    public OrderStatus Status { get; set; }
}

public class OrderDetails
{
    public StreetAddress BillingAddress { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

public enum OrderStatus
{
    Pending,
    Shipped
}

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```

<span data-ttu-id="52880-140">チェーンすることができます、`OwnsOne`メソッドでこのモデルを構成する fluent マッピング。</span><span class="sxs-lookup"><span data-stu-id="52880-140">It is possible to chain the `OwnsOne` method in a fluent mapping to configure this model:</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, od =>
    {
        od.OwnsOne(c => c.BillingAddress);
        od.OwnsOne(c => c.ShippingAddress);
    });
```

<span data-ttu-id="52880-141">使用して同じことを実現することは`OwnedAttribute`OrderDetails と StreetAdress の両方でします。</span><span class="sxs-lookup"><span data-stu-id="52880-141">It is possible to achieve the same thing using `OwnedAttribute` on both OrderDetails and StreetAdress.</span></span>

<span data-ttu-id="52880-142">入れ子になったの所有型だけでなく、所有型は正規のエンティティを参照できます。</span><span class="sxs-lookup"><span data-stu-id="52880-142">In addition to nested owned types, an owned type can reference a regular entity.</span></span> <span data-ttu-id="52880-143">次の例では、国は、通常の非所有エンティティは。</span><span class="sxs-lookup"><span data-stu-id="52880-143">In the following example, Country is a regular non-owned entity:</span></span>

``` csharp
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
    public Country Country { get; set; }
}
```

<span data-ttu-id="52880-144">この機能は、EF6 の複合型とは別の所有エンティティ型を設定します。</span><span class="sxs-lookup"><span data-stu-id="52880-144">This capability sets owned entity types apart from complex types in EF6.</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="52880-145">別のテーブルに所有型を格納します。</span><span class="sxs-lookup"><span data-stu-id="52880-145">Storing owned types in separate tables</span></span>

<span data-ttu-id="52880-146">また、EF6 の複合型とは異なりは、所有型を所有者から別のテーブルに格納できます。</span><span class="sxs-lookup"><span data-stu-id="52880-146">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="52880-147">所有型を所有者と同じテーブルにマップする規則を上書きするために呼び出すだけでできます`ToTable`別のテーブルの名前を指定します。</span><span class="sxs-lookup"><span data-stu-id="52880-147">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="52880-148">次の例では、OrderDetails と、その 2 つのアドレスにマップされます、別のテーブルの順序。</span><span class="sxs-lookup"><span data-stu-id="52880-148">The following example will map OrderDetails and its two addresses to a separate table from Order:</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, od =>
    {
        od.OwnsOne(c => c.BillingAddress);
        od.OwnsOne(c => c.ShippingAddress);
        od.ToTable("OrderDetails");
    });
```

## <a name="querying-owned-types"></a><span data-ttu-id="52880-149">所有型を問い合わせています</span><span class="sxs-lookup"><span data-stu-id="52880-149">Querying owned types</span></span>

<span data-ttu-id="52880-150">所有者に問い合わせるとき、所有されている型が既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="52880-150">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="52880-151">使用する必要はありません、`Include`所有型が別のテーブルに格納されている場合でも、メソッド。</span><span class="sxs-lookup"><span data-stu-id="52880-151">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="52880-152">前に説明したモデルに基づいて、次のクエリは Order、OrderDetails およびデータベースからすべての保留中の注文の 2 つの所有 StreetAddresses をプルします。</span><span class="sxs-lookup"><span data-stu-id="52880-152">Based on the model described before, the following query will pull Order, OrderDetails and the two owned StreetAddresses for all pending orders from the database:</span></span>

``` csharp
var orders = context.Orders.Where(o => o.Status == OrderStatus.Pending);
```  

## <a name="limitations"></a><span data-ttu-id="52880-153">制限事項</span><span class="sxs-lookup"><span data-stu-id="52880-153">Limitations</span></span>

<span data-ttu-id="52880-154">これらの制限の一部は所有されているどのエンティティ型の作業を基本的な機能がいくつか他のユーザー制限は今後のリリースを削除するいただく可能性があります。</span><span class="sxs-lookup"><span data-stu-id="52880-154">Some of these limitations are fundamental to how owned entity types work, but some others are restrictions that we may be able to remove in future releases:</span></span>

### <a name="shortcomings-in-previous-versions"></a><span data-ttu-id="52880-155">以前のバージョンの欠点</span><span class="sxs-lookup"><span data-stu-id="52880-155">Shortcomings in previous versions</span></span>
- <span data-ttu-id="52880-156">EF Core 2.0 では、ナビゲーションには、所有エンティティが別のテーブルに所有者の階層から明示的にマップされていない限り、派生エンティティ型のエンティティ型を宣言できませんを所有します。</span><span class="sxs-lookup"><span data-stu-id="52880-156">In EF Core 2.0, navigations to owned entity types cannot be declared in derived entity types unless the owned entities are explicitly mapped to a separate table from the owner hierarchy.</span></span> <span data-ttu-id="52880-157">EF Core 2.1 でこの制限が削除されました</span><span class="sxs-lookup"><span data-stu-id="52880-157">This limitation has been removed in EF Core 2.1</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="52880-158">現在の欠点</span><span class="sxs-lookup"><span data-stu-id="52880-158">Current shortcomings</span></span>
- <span data-ttu-id="52880-159">継承階層を含む所有エンティティ型はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="52880-159">Inheritance hierarchies that include owned entity types are not supported</span></span>
- <span data-ttu-id="52880-160">所有エンティティ型がで、コレクション ナビゲーション プロパティ (ナビゲーションは現在サポートされている唯一の参照) によって指されることはできません。</span><span class="sxs-lookup"><span data-stu-id="52880-160">Owned entity types cannot be pointed at by a collection navigation property (only reference navigations are currently supported)</span></span>
- <span data-ttu-id="52880-161">明示的に、所有者から別のテーブルにマップされていないエンティティ型を null にすることはできませんを所有する可能性のあるナビゲーション</span><span class="sxs-lookup"><span data-stu-id="52880-161">Navigations to owned entity types cannot be null unless they are explicitly mapped to a separate table from the owner</span></span>
- <span data-ttu-id="52880-162">(これは、所有エンティティ型を使用して実装することはできません。 値オブジェクトのよく知られているシナリオ) 複数の所有者で所有エンティティ型のインスタンスを共有することはできません。</span><span class="sxs-lookup"><span data-stu-id="52880-162">Instances of owned entity types cannot be shared by multiple owners (this is a well-known scenario for value objects that cannot be implemented using owned entity types)</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="52880-163">設計による制限</span><span class="sxs-lookup"><span data-stu-id="52880-163">By-design restrictions</span></span>
- <span data-ttu-id="52880-164">作成することはできません、 `DbSet<T>`</span><span class="sxs-lookup"><span data-stu-id="52880-164">You cannot create a `DbSet<T>`</span></span>
- <span data-ttu-id="52880-165">呼び出すことはできません`Entity<T>()`所有型の使用 `ModelBuilder`</span><span class="sxs-lookup"><span data-stu-id="52880-165">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>
