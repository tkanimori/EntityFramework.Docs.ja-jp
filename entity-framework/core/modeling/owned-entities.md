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
# <a name="owned-entity-types"></a>所有されているエンティティの種類

>[!NOTE]
> この機能は、EF コア 2.0 の新機能です。

EF コアは、その他のエンティティ型のナビゲーション プロパティにだけ表示される可能性がモデルのエンティティ型にできます。 これらと呼ばれます_エンティティ型を所有している_です。 所有されているエンティティ型を含むエンティティがその_所有者_です。

## <a name="explicit-configuration"></a>明示的な構成

エンティティの種類は含まれません EF のコアでモデルの規約によって所有されています。 使用することができます、`OwnsOne`メソッドで`OnModelCreating`を持つ型の注釈を設定または`OwnedAttribute`(EF コア 2.1 の新機能) を所有する型として型を構成します。

この例では、StreetAddress は identity プロパティは含まずに型です。 特定の注文の配送先住所を指定するために、Order 型のプロパティとして使用されます。 `OnModelCreating`、使用して、 `OwnsOne` ShippingAddress プロパティが所有する型のエンティティの順序を指定します。

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

ShippingAddress プロパティが Order type にプライベートである場合は、文字列形式を使用することができます、`OwnsOne`メソッド。

``` csharp
modelBuilder.Entity<Order>().OwnsOne(typeof(StreetAddress), "ShippingAddress");
```

この例では使用して、`OwnedAttribute`同じ目標を達成します。

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

## <a name="implicit-keys"></a>暗黙的なキー

EF コア 2.0 および 2.1 でのみ参照ナビゲーション プロパティは所有されている型を指すことができます。 所有されているデータ型のコレクションはサポートされていません。 これらの参照は、型がオーナーを使用して常に一対一のリレーションシップを持つ、そのため、不要なそれぞれのキー値を所有します。 前の例では、StreetAddress 型はキー プロパティを定義するのには必要ありません。  

EF コアがこれらのオブジェクトを追跡する方法を理解するためにとして主キーが作成されたことを考えるとわかりやすく、[プロパティをシャドウ](xref:core/modeling/shadow-properties)所有されている型。 所有されている型のインスタンスのキーの値は、所有者のインスタンスのキーの値と同じになります。      

## <a name="mapping-owned-types-with-table-splitting"></a>マッピングは、テーブル分割を含む型を所有しています。

リレーショナル データベースを使用する場合の型を所有している慣例にマップされます所有者と同じテーブル。 2 つのテーブルを分割する必要があります。 一部の列は、所有者のデータの格納に使用すると、所有されているエンティティのデータの格納に使用されるいくつかの列です。 これは、テーブル分割と呼ばれる一般的な機能です。

> [!TIP]
> テーブル分割と共に格納されている型がある所有されている EF6 で使用する方法の複合型にも同様に非常に使用します。

慣例により、EF コアは列の名前をデータベースのパターンに従って、所有されているエンティティ型のプロパティの_EntityProperty_OwnedEntityProperty_です。 したがって、StreetAddress プロパティは、ShippingAddress_Street ShippingAddress_City の名前を使用、Orders テーブルに表示されます。

追加することができます、`HasColumnName`それらの列の名前を変更するメソッド。 ここで、StreetAddress はパブリック プロパティの場合、マッピングになります

``` csharp
modelBuilder.Entity<Order>().OwnsOne(
    o => o.ShippingAddress,
    sa =>
        {
            sa.Property(p=>p.Street).HasColumnName("ShipsToStreet");
            sa.Property(p=>p.City).HasColumnName("ShipsToCity");
        });
```

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>所有されている複数の種類で同じ .NET 型の共有

所有されているエンティティ型することができます、所有されているエンティティの他の型と同じ .NET 型したがって .NET 型可能性がありますいない所有されている型を特定することです。

ような場合、所有者から所有されているエンティティを指すプロパティになります、_ナビゲーションを定義する_所有されているエンティティ型のです。 EF コアの観点からは、定義のナビゲーションは、.NET 型と共に、その型の id の一部です。   

たとえば、次のクラスで ShippingAddress と BillingAddress が両方とも同じ .NET 型、StreetAddress の。

``` csharp
public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
    public StreetAddress BillingAddress { get; set; }
}
```

EF コアがこれらのオブジェクトの追跡対象のインスタンスを区別する方法を理解するのには、所有者のキーの値と共に、インスタンスのキーの一部と、所有されている型の .NET 型定義のナビゲーションがなくなっていることを考えるとわかりやすく場合があります。

## <a name="nested-owned-types"></a>入れ子になった所有されている型

この例では、OrderDetails は BillingAddress と両方 StreetAddress 型である ShippingAddress を所有します。 OrderDetails Order type が所有します。

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

チェーンに可能であれば、 `OwnsOne` fluent のマッピングをこのモデルを構成する方法。

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, od =>
    {
        od.OwnsOne(c => c.BillingAddress);
        od.OwnsOne(c => c.ShippingAddress);
    });
```

使用して同じことを実現するために可能であれば`OwnedAttribute`OrderDetails と StreetAdress の両方でします。

入れ子になった所有されている種類に加えて、所有されている型は、正規のエンティティを参照できます。 次の例では、国は、正規の (つまり以外が所有するエンティティを示します。

``` csharp
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
    public Country Country { get; set; }
}
```

この機能は、EF6 の複合型とは別に所有されているエンティティ型を設定します。

## <a name="storing-owned-types-in-separate-tables"></a>別のテーブルに所有されている種類のファイルを格納します。

また、EF6 複合型とは異なりは、所有されている型を所有者から別のテーブルに格納できます。 所有者と同じテーブルを所有している型にマップする規則を無効にするために呼び出すことができますだけ`ToTable`別のテーブル名を指定します。 次の例はマップ OrderDetails と、その 2 つのアドレスを別のテーブルに注文から。

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, od =>
    {
        od.OwnsOne(c => c.BillingAddress);
        od.OwnsOne(c => c.ShippingAddress);
    }).ToTable("OrderDetails");
```

## <a name="querying-owned-types"></a>所有されている種類のクエリを実行します。

所有者に問い合わせるとき、所有されている型が既定で含まれます。 使用する必要はありません、`Include`メソッド、所有されている型が別のテーブルに格納されている場合でもです。 前に説明したモデルに基づいて、次のクエリはプル順序、OrderDetails と保留中のすべての注文がデータベースの 2 つの所有されている StreeAddresses:

``` csharp
var orders = context.Orders.Where(o => o.Status == OrderStatus.Pending);
```  

## <a name="limitations"></a>制限事項

所有されているエンティティ型の制限事項を示します。 これらの制限事項の一部は、所有されている方法の種類の作業に基本的ないくつかもあれば、将来のリリースを削除したい時点での制限。

### <a name="current-shortcomings"></a>現在の欠点
- 所有されている型の継承はサポートされていません
- 所有されている型はコレクション ナビゲーション プロパティを指すことはできません。
- テーブル分割を使用しているので、既定では、型も、別のテーブルを明示的にマップされている場合を除きには次の制限があります所有しています。
   - 派生型で所有していることはできません。
   - 定義するナビゲーション プロパティを設定することはできませんを null に (つまり所有するテーブルが同じでは常に必要)

### <a name="by-design-restrictions"></a>デザインにより制限
- 作成することはできません、 `DbSet<T>`
- 呼び出すことはできません`Entity<T>()`に所有されている型 `ModelBuilder`
