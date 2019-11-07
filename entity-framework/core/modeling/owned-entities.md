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
# <a name="owned-entity-types"></a>所有されているエンティティ型

> [!NOTE]
> この機能は EF Core 2.0 で新たに追加されています。

EF Core を使用すると、他のエンティティ型のナビゲーションプロパティにのみ表示されるエンティティ型をモデル化できます。 これらは、_所有エンティティ型_と呼ばれます。 所有エンティティ型を含むエンティティは、その_所有者_です。

所有されているエンティティは、本質的に所有者の一部であり、存在しない場合は、概念的には[集計](https://martinfowler.com/bliki/DDD_Aggregate.html)に似ています。

## <a name="explicit-configuration"></a>明示的な構成

所有エンティティ型は、規則によってモデル内の EF Core によって含まれることはありません。 `OnModelCreating` で `OwnsOne` メソッドを使用するか `OwnedAttribute` (EF Core 2.1 の新形式) を使用して型に注釈を付け、型を所有型として構成することができます。

この例では、`StreetAddress` は id プロパティのない型です。 特定の注文の配送先住所を指定するために、Order 型のプロパティとして使用されます。

この `OwnedAttribute` を使用すると、別のエンティティ型から参照された場合に所有エンティティとして扱うことができます。

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

`OnModelCreating` の `OwnsOne` メソッドを使用して、`ShippingAddress` プロパティが `Order` エンティティ型の所有エンティティであることを指定し、必要に応じて追加のファセットを構成することもできます。

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

`ShippingAddress` プロパティが `Order` 型でプライベートである場合は、`OwnsOne` メソッドの文字列バージョンを使用できます。

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)を参照してください。

## <a name="implicit-keys"></a>暗黙のキー

`OwnsOne` で構成されている、または参照ナビゲーションで検出された所有型は、常に所有者との一対一のリレーションシップを持ちます。したがって、外部キーの値が一意であるため、独自のキー値は必要ありません。 前の例では、`StreetAddress` 型でキープロパティを定義する必要はありません。  

EF Core がこれらのオブジェクトを追跡する方法を理解するには、主キーが所有型の[シャドウプロパティ](xref:core/modeling/shadow-properties)として作成されていることを把握しておくと便利です。 所有されている型のインスタンスのキーの値は、所有者インスタンスのキーの値と同じになります。

## <a name="collections-of-owned-types"></a>所有型のコレクション

> [!NOTE]
> これは EF Core 2.2 の新機能です。

所有されている型のコレクションを構成するには `OnModelCreating`で `OwnsMany` を使用します。

所有型には主キーが必要です。 .NET 型に適切な候補のプロパティがない場合は、EF Core で作成を試みることができます。 ただし、所有型がコレクションによって定義されている場合は、`OwnsOne`の場合と同様に、外部キーと所有インスタンスの主キーの両方として機能する shadow プロパティを作成するだけでは十分ではありません。それぞれに対して所有される型インスタンスが複数存在する可能性があります。所有者であるため、所有者のキーは、所有しているインスタンスごとに一意の id を提供するのに十分ではありません。

これには、次の2つの最も簡単な解決策があります。

- 所有者を参照する外部キーとは独立して、新しいプロパティに代理主キーを定義する。 含まれている値はすべての所有者間で一意である必要があります (たとえば、親 {1} に子 {1}がある場合、親 {2} に子 {1}を指定することはできません)。そのため、値には固有の意味がありません。 外部キーは主キーの一部ではないため、その値を変更できます。したがって、子をある親から別の親に移動できます。ただし、これは通常、集計のセマンティクスに対して行われます。
- 外部キーと追加のプロパティを複合キーとして使用する。 追加のプロパティ値は、指定された親に対してのみ一意である必要があります (したがって、親 {1} に子 {1,1} がある場合、親 {2} に子 {2,1}を持つことができます)。 主キーの外部キー部分を作成することによって、所有者と所有されているエンティティの間のリレーションシップが変更不可になり、集計セマンティクスがより適切に反映されます。 既定では、これが EF Core になります。

この例では、`Distributor` クラスを使用します。

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

既定では、`ShippingCenters` ナビゲーションプロパティを介して参照される所有型に使用される主キーが `("DistributorId", "Id")` されます。 `"DistributorId"` が FK で、`"Id"` が一意の `int` 値になります。

`HasKey`別の PK 呼び出しを構成するには、次のようにします。

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> EF Core 3.0 より前の `WithOwner()` メソッドが存在しないため、この呼び出しを削除する必要があります。

## <a name="mapping-owned-types-with-table-splitting"></a>所有型のテーブル分割へのマッピング

リレーショナルデータベースを使用する場合、既定では、参照所有型は、所有者と同じテーブルにマップされます。 そのためには、テーブルを2つの列に分割する必要があります。つまり、所有者のデータを格納するために使用される列と、所有されているエンティティのデータを格納するために使用される列があります。 これは、[テーブル分割](table-splitting.md)と呼ばれる一般的な機能です。

既定では、EF Core は、 _Navigation_OwnedEntityProperty_パターンに従って、所有されているエンティティ型のプロパティのデータベース列に名前を設定します。 そのため、`StreetAddress` のプロパティは、' ShippingAddress_Street ' と ' ShippingAddress_City ' という名前の ' Orders ' テーブルに表示されます。

`HasColumnName` メソッドを使用して、これらの列の名前を変更できます。

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>複数の所有型間で同じ .NET 型を共有する

所有されているエンティティ型は、他の所有エンティティ型と同じ .NET 型にすることができます。したがって、.NET 型は所有型を特定するのに十分ではない可能性があります。

そのような場合は、所有者から所有されているエンティティを指すプロパティが、所有されているエンティティ型の_ナビゲーションを定義_するようになります。 EF Core の観点から見ると、定義のナビゲーションは、.NET 型と共に型の id の一部になります。

たとえば、次のクラスの `ShippingAddress` と `BillingAddress` は両方とも同じ .NET 型 `StreetAddress`ます。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

EF Core がこれらのオブジェクトの追跡対象インスタンスを区別する方法を理解するために、定義ナビゲーションが、所有者のキーの値と所有型の .NET 型の値と共にインスタンスのキーの一部になることを考えると役立つ場合があります。

## <a name="nested-owned-types"></a>入れ子になった所有型

この例では `OrderDetails` `BillingAddress` と `ShippingAddress`を所有しています。これらの型は両方とも `StreetAddress` です。 また、`OrderDetails` は `DetailedOrder` 型に所有されています。

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

入れ子になった所有型に加えて、所有型は標準エンティティを参照できます。所有されているエンティティが依存側にある限り、所有者または別のエンティティのいずれかになります。 この機能は、EF6 の複合型とは別に、所有エンティティ型を設定します。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

Fluent 呼び出しで `OwnsOne` メソッドをチェーンして、このモデルを構成することができます。

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

所有者を指すナビゲーションプロパティを構成するために使用される `WithOwner` の呼び出しに注意してください。

`OrderDetails` と `StreetAdress`の両方で `OwnedAttribute` を使用して結果を得ることができます。

## <a name="storing-owned-types-in-separate-tables"></a>個別のテーブルへの所有型の格納

また、EF6 複合型とは異なり、所有型は所有者とは別のテーブルに格納できます。 所有型を所有者と同じテーブルにマップする規則をオーバーライドするには、単に `ToTable` を呼び出して、別のテーブル名を指定します。 次の例では、`OrderDetails` とその2つのアドレスを `DetailedOrder`から別のテーブルにマップします。

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

## <a name="querying-owned-types"></a>所有型のクエリ

所有者に問い合わせるとき、所有されている型が既定で含まれます。 所有型が別のテーブルに格納されている場合でも、`Include` メソッドを使用する必要はありません。 次のクエリでは、前に説明したモデルに基づいて、`Order`、`OrderDetails`、およびデータベースから所有されている2つの `StreetAddresses` を取得します。

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a>制限事項

これらの制限事項のいくつかは、所有エンティティ型の動作のしくみに関するものですが、他のいくつかは将来のリリースで削除できる可能性がある制限です。

### <a name="by-design-restrictions"></a>設計上の制限

- 所有型の `DbSet<T>` を作成することはできません。
- `ModelBuilder` で所有型を使用して `Entity<T>()` を呼び出すことはできません

### <a name="current-shortcomings"></a>現在の欠点

- 所有エンティティ型を含む継承階層はサポートされていません
- 所有されているエンティティ型への参照ナビゲーションは、所有者から別のテーブルに明示的にマップされていない限り、null にすることはできません
- 所有されているエンティティ型のインスタンスを複数の所有者が共有することはできません (これは、所有エンティティ型を使用して実装できない値オブジェクトの既知のシナリオです)

### <a name="shortcomings-in-previous-versions"></a>以前のバージョンの欠点

- EF Core 2.0 では、所有されているエンティティ型へのナビゲーションは、所有しているエンティティが所有者階層から別のテーブルに明示的にマップされている場合を除き、派生エンティティ型で宣言することはできません。 この制限は EF Core 2.1 で削除されました
- EF Core 2.0 および2.1 では、所有型へのナビゲーションのみがサポートされていました。 この制限は EF Core 2.2 で削除されました
