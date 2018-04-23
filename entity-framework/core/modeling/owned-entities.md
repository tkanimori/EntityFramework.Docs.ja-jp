---
title: エンティティ型 – EF コアを所有しています。
author: julielerman
ms.author: divega
ms.date: 2/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
ms.technology: entity-framework-core
uid: core/modeling/owned-entities
ms.openlocfilehash: f2f05499a3e3494f420d916df2db19667a6f1e29
ms.sourcegitcommit: 26f33758c47399ae933f22fec8e1d19fa7d2c0b7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2018
---
# <a name="owned-entity-types"></a><span data-ttu-id="72451-102">所有されているエンティティの種類</span><span class="sxs-lookup"><span data-stu-id="72451-102">Owned Entity Types</span></span>

>[!NOTE]
> <span data-ttu-id="72451-103">この機能は、EF コア 2.0 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="72451-103">This feature is new in EF Core 2.0.</span></span>

<span data-ttu-id="72451-104">EF コアは、その他のエンティティ型のナビゲーション プロパティにだけ表示される可能性がモデルのエンティティ型にできます。</span><span class="sxs-lookup"><span data-stu-id="72451-104">EF Core allows you to model entity types that can only ever appear on navigation properties of other entity types.</span></span> <span data-ttu-id="72451-105">これらと呼ばれます_エンティティ型を所有している_です。</span><span class="sxs-lookup"><span data-stu-id="72451-105">These are called _owned entity types_.</span></span> <span data-ttu-id="72451-106">所有されているエンティティ型を含むエンティティがその_所有者_です。</span><span class="sxs-lookup"><span data-stu-id="72451-106">The entity containing an owned entity type is its _owner_.</span></span>

## <a name="explicit-configuration"></a><span data-ttu-id="72451-107">明示的な構成</span><span class="sxs-lookup"><span data-stu-id="72451-107">Explicit configuration</span></span>

<span data-ttu-id="72451-108">エンティティの種類は含まれません EF のコアでモデルの規約によって所有されています。</span><span class="sxs-lookup"><span data-stu-id="72451-108">Owned entity types are never included by EF Core in the model by convention.</span></span> <span data-ttu-id="72451-109">使用することができます、`OwnsOne`メソッドで`OnModelCreating`を持つ型の注釈を設定または`OwnedAttribute`(EF コア 2.1 の新機能) を所有する型として型を構成します。</span><span class="sxs-lookup"><span data-stu-id="72451-109">You can use the `OwnsOne` method in `OnModelCreating` or annotate the type with `OwnedAttribute` (new in EF Core 2.1) to configure the type as an owned type.</span></span>

<span data-ttu-id="72451-110">この例では、StreetAddress は identity プロパティは含まずに型です。</span><span class="sxs-lookup"><span data-stu-id="72451-110">In this example, StreetAddress is a type with no identity property.</span></span> <span data-ttu-id="72451-111">特定の注文の配送先住所を指定するために、Order 型のプロパティとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="72451-111">It is used as a property of the Order type to specify the shipping address for a particular order.</span></span> <span data-ttu-id="72451-112">`OnModelCreating`、使用して、 `OwnsOne` ShippingAddress プロパティが所有する型のエンティティの順序を指定します。</span><span class="sxs-lookup"><span data-stu-id="72451-112">In `OnModelCreating`, we use the `OwnsOne` method to specify that the ShippingAddress property is an Owned Entity of the Order type.</span></span>

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

<span data-ttu-id="72451-113">ShippingAddress プロパティが Order type にプライベートである場合は、文字列形式を使用することができます、`OwnsOne`メソッド。</span><span class="sxs-lookup"><span data-stu-id="72451-113">If the ShippingAddress property is private in the Order type, you can use the string version of the `OwnsOne` method:</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(typeof(StreetAddress), "ShippingAddress");
```

<span data-ttu-id="72451-114">この例では使用して、`OwnedAttribute`同じ目標を達成します。</span><span class="sxs-lookup"><span data-stu-id="72451-114">In this example, we use the `OwnedAttribute` to achieve the same goal:</span></span>

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

## <a name="implicit-keys"></a><span data-ttu-id="72451-115">暗黙的なキー</span><span class="sxs-lookup"><span data-stu-id="72451-115">Implicit keys</span></span>

<span data-ttu-id="72451-116">EF コア 2.0 および 2.1 でのみ参照ナビゲーション プロパティは所有されている型を指すことができます。</span><span class="sxs-lookup"><span data-stu-id="72451-116">In EF Core 2.0 and 2.1, only reference navigation properties can point to owned types.</span></span> <span data-ttu-id="72451-117">所有されているデータ型のコレクションはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="72451-117">Collections of owned types are not supported.</span></span> <span data-ttu-id="72451-118">これらの参照は、型がオーナーを使用して常に一対一のリレーションシップを持つ、そのため、不要なそれぞれのキー値を所有します。</span><span class="sxs-lookup"><span data-stu-id="72451-118">These reference owned types always have a one-to-one relationship with the owner, therefore they don't need their own key values.</span></span> <span data-ttu-id="72451-119">前の例では、StreetAddress 型はキー プロパティを定義するのには必要ありません。</span><span class="sxs-lookup"><span data-stu-id="72451-119">In the previous example, the StreetAddress type does not need to define a key property.</span></span>  

<span data-ttu-id="72451-120">EF コアがこれらのオブジェクトを追跡する方法を理解するためにとして主キーが作成されたことを考えるとわかりやすく、[プロパティをシャドウ](xref:core/modeling/shadow-properties)所有されている型。</span><span class="sxs-lookup"><span data-stu-id="72451-120">In order to understanding how EF Core tracks these objects, it is useful to think that a primary key is created as a [shadow property](xref:core/modeling/shadow-properties) for the owned type.</span></span> <span data-ttu-id="72451-121">所有されている型のインスタンスのキーの値は、所有者のインスタンスのキーの値と同じになります。</span><span class="sxs-lookup"><span data-stu-id="72451-121">The value of the key of an instance of the owned type will be the same as the value of the key of the owner instance.</span></span>      

## <a name="mapping-owned-types-with-table-splitting"></a><span data-ttu-id="72451-122">マッピングは、テーブル分割を含む型を所有しています。</span><span class="sxs-lookup"><span data-stu-id="72451-122">Mapping owned types with table splitting</span></span>

<span data-ttu-id="72451-123">リレーショナル データベースを使用する場合の型を所有している慣例にマップされます所有者と同じテーブル。</span><span class="sxs-lookup"><span data-stu-id="72451-123">When using relational databases, by convention owned types are mapped to the same table as the owner.</span></span> <span data-ttu-id="72451-124">2 つのテーブルを分割する必要があります。 一部の列は、所有者のデータの格納に使用すると、所有されているエンティティのデータの格納に使用されるいくつかの列です。</span><span class="sxs-lookup"><span data-stu-id="72451-124">This requires splitting the table in two: some columns will be used to store the data of the owner, and some columns will be used to store data of the owned entity.</span></span> <span data-ttu-id="72451-125">これは、テーブル分割と呼ばれる一般的な機能です。</span><span class="sxs-lookup"><span data-stu-id="72451-125">This is a common feature known as table splitting.</span></span>

> [!TIP]
> <span data-ttu-id="72451-126">テーブル分割と共に格納されている型がある所有されている EF6 で使用する方法の複合型にも同様に非常に使用します。</span><span class="sxs-lookup"><span data-stu-id="72451-126">Owned types stored with table splitting can be used very similarly to how complex types are used in EF6.</span></span>

<span data-ttu-id="72451-127">慣例により、EF コアは列の名前をデータベースのパターンに従って、所有されているエンティティ型のプロパティの_EntityProperty_OwnedEntityProperty_です。</span><span class="sxs-lookup"><span data-stu-id="72451-127">By convention, EF Core will name the database columns for the properties of the owned entity type following the pattern _EntityProperty_OwnedEntityProperty_.</span></span> <span data-ttu-id="72451-128">したがって、StreetAddress プロパティは、ShippingAddress_Street ShippingAddress_City の名前を使用、Orders テーブルに表示されます。</span><span class="sxs-lookup"><span data-stu-id="72451-128">Therefore the StreetAddress properties will appear in the Orders table with the names ShippingAddress_Street and ShippingAddress_City.</span></span>

<span data-ttu-id="72451-129">追加することができます、`HasColumnName`それらの列の名前を変更するメソッド。</span><span class="sxs-lookup"><span data-stu-id="72451-129">You can append the `HasColumnName` method to rename those columns.</span></span> <span data-ttu-id="72451-130">ここで、StreetAddress はパブリック プロパティの場合、マッピングになります</span><span class="sxs-lookup"><span data-stu-id="72451-130">In the case where StreetAddress is a public property, the mappings would be</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(
    o => o.ShippingAddress,
    sa =>
        {
            sa.Property(p=>p.Street).HasColumnName("ShipsToStreet");
            sa.Property(p=>p.City).HasColumnName("ShipsToCity");
        });
```

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a><span data-ttu-id="72451-131">所有されている複数の種類で同じ .NET 型の共有</span><span class="sxs-lookup"><span data-stu-id="72451-131">Sharing the same .NET type among multiple owned types</span></span>

<span data-ttu-id="72451-132">所有されているエンティティ型することができます、所有されているエンティティの他の型と同じ .NET 型したがって .NET 型可能性がありますいない所有されている型を特定することです。</span><span class="sxs-lookup"><span data-stu-id="72451-132">An owned entity type can be of the same .NET type as another owned entity type, therefore the .NET type may not be enough to identify an owned type.</span></span>

<span data-ttu-id="72451-133">ような場合、所有者から所有されているエンティティを指すプロパティになります、_ナビゲーションを定義する_所有されているエンティティ型のです。</span><span class="sxs-lookup"><span data-stu-id="72451-133">In those cases, the property pointing from the owner to the owned entity becomes the _defining navigation_ of the owned entity type.</span></span> <span data-ttu-id="72451-134">EF コアの観点からは、定義のナビゲーションは、.NET 型と共に、その型の id の一部です。</span><span class="sxs-lookup"><span data-stu-id="72451-134">From the perspective of EF Core, the defining navigation is part of the type's identity alongside the .NET type.</span></span>   

<span data-ttu-id="72451-135">たとえば、次のクラスで ShippingAddress と BillingAddress が両方とも同じ .NET 型、StreetAddress の。</span><span class="sxs-lookup"><span data-stu-id="72451-135">For example, in the following class, ShippingAddress and BillingAddress are both of the same .NET type, StreetAddress:</span></span>

``` csharp
public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
    public StreetAddress BillingAddress { get; set; }
}
```

<span data-ttu-id="72451-136">EF コアがこれらのオブジェクトの追跡対象のインスタンスを区別する方法を理解するのには、所有者のキーの値と共に、インスタンスのキーの一部と、所有されている型の .NET 型定義のナビゲーションがなくなっていることを考えるとわかりやすく場合があります。</span><span class="sxs-lookup"><span data-stu-id="72451-136">In order to understand how EF Core will distinguish tracked instances of these objects, it may be useful to think that the defining navigation has become part of the key of the instance alongside the value of the key of the owner and the .NET type of the owned type.</span></span>

## <a name="nested-owned-types"></a><span data-ttu-id="72451-137">入れ子になった所有されている型</span><span class="sxs-lookup"><span data-stu-id="72451-137">Nested owned types</span></span>

<span data-ttu-id="72451-138">この例では、OrderDetails は BillingAddress と両方 StreetAddress 型である ShippingAddress を所有します。</span><span class="sxs-lookup"><span data-stu-id="72451-138">In this example OrderDetails owns BillingAddress and ShippingAddress, which are both StreetAddress types.</span></span> <span data-ttu-id="72451-139">OrderDetails Order type が所有します。</span><span class="sxs-lookup"><span data-stu-id="72451-139">Then OrderDetails is owned by the Order type.</span></span>

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

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```

<span data-ttu-id="72451-140">チェーンに可能であれば、 `OwnsOne` fluent のマッピングをこのモデルを構成する方法。</span><span class="sxs-lookup"><span data-stu-id="72451-140">It is possible to chain the `OwnsOne` method in a fluent mapping to configure this model:</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, od =>
    {
        od.OwnsOne(c => c.BillingAddress);
        od.OwnsOne(c => c.ShippingAddress);
    });
```

<span data-ttu-id="72451-141">使用して同じことを実現するために可能であれば`OwnedAttribute`OrderDetails と StreetAdress の両方でします。</span><span class="sxs-lookup"><span data-stu-id="72451-141">It is possible to achieve the same thing using `OwnedAttribute` on both OrderDetails and StreetAdress.</span></span>

<span data-ttu-id="72451-142">入れ子になった所有されている種類に加えて、所有されている型は、正規のエンティティを参照できます。</span><span class="sxs-lookup"><span data-stu-id="72451-142">In addition to nested owned types, an owned type can reference a regular entity.</span></span> <span data-ttu-id="72451-143">次の例では、国は、正規の (つまり以外が所有するエンティティを示します。</span><span class="sxs-lookup"><span data-stu-id="72451-143">In the following example, Country is a regular (i.e. non-owned) entity:</span></span>

``` csharp
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
    public Country Country { get; set; }
}
```

<span data-ttu-id="72451-144">この機能は、EF6 の複合型とは別に所有されているエンティティ型を設定します。</span><span class="sxs-lookup"><span data-stu-id="72451-144">This capability sets owned entity types apart from complex types in EF6.</span></span>

## <a name="storing-owned-types-in-separate-tables"></a><span data-ttu-id="72451-145">別のテーブルに所有されている種類のファイルを格納します。</span><span class="sxs-lookup"><span data-stu-id="72451-145">Storing owned types in separate tables</span></span>

<span data-ttu-id="72451-146">また、EF6 複合型とは異なりは、所有されている型を所有者から別のテーブルに格納できます。</span><span class="sxs-lookup"><span data-stu-id="72451-146">Also unlike EF6 complex types, owned types can be stored in a separate table from the owner.</span></span> <span data-ttu-id="72451-147">所有者と同じテーブルを所有している型にマップする規則を無効にするために呼び出すことができますだけ`ToTable`別のテーブル名を指定します。</span><span class="sxs-lookup"><span data-stu-id="72451-147">In order to override the convention that maps an owned type to the same table as the owner, you can simply call `ToTable` and provide a different table name.</span></span> <span data-ttu-id="72451-148">次の例はマップ OrderDetails と、その 2 つのアドレスを別のテーブルに注文から。</span><span class="sxs-lookup"><span data-stu-id="72451-148">The following example will map OrderDetails and its two addresses to a separate table from Order:</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, od =>
    {
        od.OwnsOne(c => c.BillingAddress);
        od.OwnsOne(c => c.ShippingAddress);
    }).ToTable("OrderDetails");
```

## <a name="querying-owned-types"></a><span data-ttu-id="72451-149">所有されている種類のクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="72451-149">Querying owned types</span></span>

<span data-ttu-id="72451-150">所有者に問い合わせるとき、所有されている型が既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="72451-150">When querying the owner the owned types will be included by default.</span></span> <span data-ttu-id="72451-151">使用する必要はありません、`Include`メソッド、所有されている型が別のテーブルに格納されている場合でもです。</span><span class="sxs-lookup"><span data-stu-id="72451-151">It is not necessary to use the `Include` method, even if the owned types are stored in a separate table.</span></span> <span data-ttu-id="72451-152">前に説明したモデルに基づいて、次のクエリはプル順序、OrderDetails と保留中のすべての注文がデータベースの 2 つの所有されている StreeAddresses:</span><span class="sxs-lookup"><span data-stu-id="72451-152">Based on the model described before, the following query will pull Order, OrderDetails and the two owned StreeAddresses for all pending orders from the database:</span></span>

``` csharp
var orders = context.Orders.Where(o => o.Status == OrderStatus.Pending);
```  

## <a name="limitations"></a><span data-ttu-id="72451-153">制限事項</span><span class="sxs-lookup"><span data-stu-id="72451-153">Limitations</span></span>

<span data-ttu-id="72451-154">所有されているエンティティ型の制限事項を示します。</span><span class="sxs-lookup"><span data-stu-id="72451-154">Here are some limitations of owned entity types.</span></span> <span data-ttu-id="72451-155">これらの制限事項の一部は、所有されている方法の種類の作業に基本的ないくつかもあれば、将来のリリースを削除したい時点での制限。</span><span class="sxs-lookup"><span data-stu-id="72451-155">Some of these limitations are fundamental to how owned types work, but some others are point-in-time restrictions that we would like to remove in future releases:</span></span>

### <a name="current-shortcomings"></a><span data-ttu-id="72451-156">現在の欠点</span><span class="sxs-lookup"><span data-stu-id="72451-156">Current shortcomings</span></span>
- <span data-ttu-id="72451-157">所有されている型の継承はサポートされていません</span><span class="sxs-lookup"><span data-stu-id="72451-157">Inheritance of owned types is not supported</span></span>
- <span data-ttu-id="72451-158">所有されている型はコレクション ナビゲーション プロパティを指すことはできません。</span><span class="sxs-lookup"><span data-stu-id="72451-158">Owned types cannot be pointed at by a collection navigation property</span></span>
- <span data-ttu-id="72451-159">テーブル分割を使用しているので、既定では、型も、別のテーブルを明示的にマップされている場合を除きには次の制限があります所有しています。</span><span class="sxs-lookup"><span data-stu-id="72451-159">Since they use table splitting by default owned types also have the following restrictions unless explicitly mapped to a different table:</span></span>
   - <span data-ttu-id="72451-160">派生型で所有していることはできません。</span><span class="sxs-lookup"><span data-stu-id="72451-160">They cannot be owned by a derived type</span></span>
   - <span data-ttu-id="72451-161">定義するナビゲーション プロパティを設定することはできませんを null に (つまり所有するテーブルが同じでは常に必要)</span><span class="sxs-lookup"><span data-stu-id="72451-161">The defining navigation property cannot be set to null (i.e. owned types on the same table are always required)</span></span>

### <a name="by-design-restrictions"></a><span data-ttu-id="72451-162">デザインにより制限</span><span class="sxs-lookup"><span data-stu-id="72451-162">By-design restrictions</span></span>
- <span data-ttu-id="72451-163">作成することはできません、 `DbSet<T>`</span><span class="sxs-lookup"><span data-stu-id="72451-163">You cannot create a `DbSet<T>`</span></span>
- <span data-ttu-id="72451-164">呼び出すことはできません`Entity<T>()`に所有されている型 `ModelBuilder`</span><span class="sxs-lookup"><span data-stu-id="72451-164">You cannot call `Entity<T>()` with an owned type on `ModelBuilder`</span></span>
