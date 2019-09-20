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
# <a name="owned-entity-types"></a>所有エンティティ型

>[!NOTE]
> この機能は EF Core 2.0 で新たに追加されています。

EF Core を使用すると、他のエンティティ型のナビゲーションプロパティにのみ表示されるエンティティ型をモデル化できます。 これらは、_所有エンティティ型_と呼ばれます。 所有エンティティ型を含むエンティティはその_所有者_します。

所有されているエンティティは、本質的に所有者の一部であり、存在しない場合は、概念的には[集計](https://martinfowler.com/bliki/DDD_Aggregate.html)に似ています。

## <a name="explicit-configuration"></a>明示的な構成

所有エンティティ型は、規則によってモデル内の EF Core によって含まれることはありません。 で`OwnsOne` `OwnedAttribute`メソッドを使用するか、(EF Core 2.1 の新) を使用して型に注釈を付け、型を所有型として構成することができます。 `OnModelCreating`

この例では`StreetAddress` 、は id プロパティのない型です。 特定の注文の配送先住所を指定するために、Order 型のプロパティとして使用されます。

別のエンティティ型`OwnedAttribute`から参照された場合は、を使用して所有エンティティとして扱うことができます。

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

また`OwnsOne` 、の`OnModelCreating`メソッドを使用して、 `ShippingAddress`プロパティが`Order`エンティティ型の所有エンティティであることを指定したり、必要に応じて追加のファセットを構成したりすることもできます。

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

プロパティが`Order`型でプライベートである場合は、 `OwnsOne`メソッドの文字列バージョンを使用できます。 `ShippingAddress`

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)を参照してください。 

## <a name="implicit-keys"></a>暗黙のキー

参照ナビゲーションを使用`OwnsOne`して構成された所有型は、常に所有者との一対一のリレーションシップを持ちます。したがって、外部キーの値が一意であるため、独自のキー値は必要ありません。 前の例では、 `StreetAddress`型はキープロパティを定義する必要はありません。  

EF Core がこれらのオブジェクトを追跡する方法を理解するには、主キーが所有型の[シャドウプロパティ](xref:core/modeling/shadow-properties)として作成されていることを把握しておくと便利です。 所有されている型のインスタンスのキーの値は、所有者インスタンスのキーの値と同じになります。

## <a name="collections-of-owned-types"></a>所有型のコレクション

> [!NOTE]
> これは EF Core 2.2 の新機能です。

所有されている型のコレクション`OwnsMany`を`OnModelCreating`構成するには、でを使用します。

所有型には主キーが必要です。 .NET 型に適切な候補のプロパティがない場合は、EF Core で作成を試みることができます。 ただし、所有型がコレクションによって定義されている場合は、外部キーと所有インスタンス`OwnsOne`の主キーの両方として機能する shadow プロパティを作成するだけでは十分ではありません。所有している型のインスタンスは複数存在する可能性があります。各所有者は、所有しているインスタンスごとに一意の id を提供するのに十分ではありません。

これには、次の2つの最も簡単な解決策があります。
- 所有者を参照する外部キーとは独立して、新しいプロパティに代理主キーを定義する。 含まれている値はすべての所有者間で一意である必要{1}があり{1}ます (親{2}が子を{1}持っている場合は、親が子を持つことができます)。したがって、値には固有の意味がありません。 外部キーは主キーの一部ではないため、その値を変更できます。したがって、子をある親から別の親に移動できます。ただし、これは通常、集計のセマンティクスに対して行われます。
- 外部キーと追加のプロパティを複合キーとして使用する。 追加のプロパティ値は、指定された親に対してのみ一意である{1}必要があります{2} (したがって、 {2,1}親に子{1,1}がある場合、親は子を持つことができます)。 主キーの外部キー部分を作成することによって、所有者と所有されているエンティティの間のリレーションシップが変更不可になり、集計セマンティクスがより適切に反映されます。 既定では、これが EF Core になります。

この例では、 `Distributor`クラスを使用します。

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

既定`ShippingCenters`では、ナビゲーションプロパティで参照される所有型に使用される主キー `"DistributorId"` `("DistributorId", "Id")`はになります`"Id"` 。ここで`int` 、は FK で、は一意の値です。

異なる PK 呼び出し`HasKey`を構成するには、次のようにします。

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> EF Core 3.0 `WithOwner()`メソッドが存在しない場合は、この呼び出しを削除する必要があります。

## <a name="mapping-owned-types-with-table-splitting"></a>所有型のテーブル分割へのマッピング

リレーショナルデータベースを使用する場合、既定では、参照所有型は、所有者と同じテーブルにマップされます。 そのためには、テーブルを2つの列に分割する必要があります。つまり、所有者のデータを格納するために使用される列と、所有されているエンティティのデータを格納するために使用される列があります。 これは、[テーブル分割](table-splitting.md)と呼ばれる一般的な機能です。

既定では、EF Core は、 _Navigation_OwnedEntityProperty_パターンに従って、所有されているエンティティ型のプロパティのデータベース列に名前を設定します。 そのため、' Orders ' テーブルには、' ShippingAddress_Street ' および ' ShippingAddress_City ' という名前のプロパティが表示されます。`StreetAddress`

メソッドを使用し`HasColumnName`て、これらの列の名前を変更できます。

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>複数の所有型間で同じ .NET 型を共有する

所有エンティティ型できます別の所有エンティティ型と同じ .NET 型したがって .NET 型できない可能性があります所有型を識別するために十分な。

その場合、所有者から所有エンティティを指すプロパティになります、_ナビゲーションを定義する_の所有エンティティ型。 EF Core の観点から見ると、定義のナビゲーションは、.NET 型と共に型の id の一部になります。   

たとえば、次のクラス`ShippingAddress`では、と`BillingAddress`の両方が同じ .net 型`StreetAddress`です。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

EF Core がこれらのオブジェクトの追跡対象インスタンスを区別する方法を理解するために、定義ナビゲーションが、所有者のキーの値と所有型の .NET 型の値と共にインスタンスのキーの一部になることを考えると役立つ場合があります。

## <a name="nested-owned-types"></a>入れ子になった所有型

この例`OrderDetails`では、 `ShippingAddress`と (両方`StreetAddress`の型) を所有`BillingAddress`しています。 また、`OrderDetails` は `DetailedOrder` 型に所有されています。

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

入れ子になった所有型に加えて、所有型は標準エンティティを参照できます。所有されているエンティティが依存側にある限り、所有者または別のエンティティのいずれかになります。 この機能は、EF6 の複合型とは別の所有エンティティ型を設定します。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

このモデルを構成するに`OwnsOne`は、fluent 呼び出しでメソッドをチェーンすることができます。

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

所有者`WithOwner`を指すナビゲーションプロパティを構成するための呼び出しが使用されていることに注意してください。

と`OwnedAttribute` `OrderDetails`の両方でを使用して結果を実現することができます。 `StreetAdress`

## <a name="storing-owned-types-in-separate-tables"></a>個別のテーブルへの所有型の格納

また、EF6 複合型とは異なり、所有型は所有者とは別のテーブルに格納できます。 所有型を所有者と同じテーブルにマップする規則をオーバーライドするには、を呼び出し`ToTable`て別のテーブル名を指定するだけです。 次の例では`OrderDetails` 、とその2つのアドレスをから`DetailedOrder`別のテーブルにマップします。

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

## <a name="querying-owned-types"></a>所有型のクエリ

所有者に問い合わせるとき、所有されている型が既定で含まれます。 所有されている型が`Include`別のテーブルに格納されている場合でも、メソッドを使用する必要はありません。 前に説明したモデルに基づいて、次の`Order`クエリ`OrderDetails`は、データベースから`StreetAddresses`所有されている2つのクエリを取得します。

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a>制限事項

これらの制限事項のいくつかは、所有エンティティ型の動作のしくみに関するものですが、他のいくつかは将来のリリースで削除できる可能性がある制限です。

### <a name="by-design-restrictions"></a>設計上の制限
- 所有型`DbSet<T>`のを作成することはできません。
- で所有型`Entity<T>()`を使用してを呼び出すことはできません。`ModelBuilder`

### <a name="current-shortcomings"></a>現在の欠点
- 所有エンティティ型を含む継承階層はサポートされていません
- 所有されているエンティティ型への参照ナビゲーションは、所有者から別のテーブルに明示的にマップされていない限り、null にすることはできません
- (これは、所有エンティティ型を使用して実装することはできません。 値オブジェクトのよく知られているシナリオ) 複数の所有者で所有エンティティ型のインスタンスを共有することはできません。

### <a name="shortcomings-in-previous-versions"></a>以前のバージョンの欠点
- EF Core 2.0 では、所有されているエンティティ型へのナビゲーションは、所有しているエンティティが所有者階層から別のテーブルに明示的にマップされている場合を除き、派生エンティティ型で宣言することはできません。 この制限は EF Core 2.1 で削除されました
- EF Core 2.0 および2.1 では、所有型へのナビゲーションのみがサポートされていました。 この制限は EF Core 2.2 で削除されました
