---
title: 所有されているエンティティ型-EF Core
description: Entity Framework Core を使用するときに所有されているエンティティ型または集計を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/owned-entities
ms.openlocfilehash: 69bdd5d5a64983d691b5967f3a64dedccbd7c07f
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672803"
---
# <a name="owned-entity-types"></a>所有されているエンティティ型

EF Core を使用すると、他のエンティティ型のナビゲーションプロパティにのみ表示されるエンティティ型をモデル化できます。 これらは、_所有エンティティ型_と呼ばれます。 所有エンティティ型を含むエンティティは、その_所有者_です。

所有されているエンティティは、本質的に所有者の一部であり、存在しない場合は、概念的には[集計](https://martinfowler.com/bliki/DDD_Aggregate.html)に似ています。 つまり、所有されているエンティティは、所有者とのリレーションシップの依存側の定義によって決まります。

## <a name="explicit-configuration"></a>明示的な構成

所有エンティティ型は、規則によってモデル内の EF Core によって含まれることはありません。 でメソッドを使用する `OwnsOne` `OnModelCreating` か、(EF Core 2.1 の新) を使用して型に注釈を付け、型を `OwnedAttribute` 所有型として構成することができます。

この例で `StreetAddress` は、は id プロパティのない型です。 特定の注文の配送先住所を指定するために、Order 型のプロパティとして使用されます。

別のエンティティ型から参照された場合は、を使用して `OwnedAttribute` 所有エンティティとして扱うことができます。

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

また、のメソッドを使用して、 `OwnsOne` `OnModelCreating` `ShippingAddress` プロパティがエンティティ型の所有エンティティであることを指定したり、必要に応じて `Order` 追加のファセットを構成したりすることもできます。

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

`ShippingAddress`プロパティが型でプライベートである場合は、 `Order` メソッドの文字列バージョンを使用でき `OwnsOne` ます。

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)を参照してください。

## <a name="implicit-keys"></a>暗黙のキー

参照ナビゲーションを使用して構成された所有型 `OwnsOne` は、常に所有者との一対一のリレーションシップを持ちます。したがって、外部キーの値が一意であるため、独自のキー値は必要ありません。 前の例では、 `StreetAddress` 型はキープロパティを定義する必要はありません。  

EF Core がこれらのオブジェクトを追跡する方法を理解するには、主キーが所有型の[シャドウプロパティ](xref:core/modeling/shadow-properties)として作成されていることを把握しておくと便利です。 所有されている型のインスタンスのキーの値は、所有者インスタンスのキーの値と同じになります。

## <a name="collections-of-owned-types"></a>所有型のコレクション

> [!NOTE]
> これは EF Core 2.2 の新機能です。

所有されている型のコレクションを構成するには、でを使用 `OwnsMany` `OnModelCreating` します。

所有型には主キーが必要です。 .NET 型に適切な候補のプロパティがない場合は、EF Core で作成を試みることができます。 ただし、所有する型がコレクションによって定義されている場合は、所有しているインスタンスの外部キーと主キーの両方として機能する shadow プロパティを作成するだけでは十分ではありません `OwnsOne` 。所有者ごとに所有型インスタンスが複数存在する可能性があります

これには、次の2つの最も簡単な解決策があります。

- 所有者を参照する外部キーとは独立して、新しいプロパティに代理主キーを定義する。 含まれている値はすべての所有者間で一意である必要があります (親 {1} が子を持っている場合は {1} 、親が子を持つ {2} ことができます)。したがって {1} 、値には固有の意味がありません。 外部キーは主キーの一部ではないため、その値を変更できます。したがって、子をある親から別の親に移動できます。ただし、これは通常、集計のセマンティクスに対して行われます。
- 外部キーと追加のプロパティを複合キーとして使用する。 追加のプロパティ値は、指定された親に対してのみ一意である必要があります (したがって、親 {1} に子がある場合、 {1,1} 親は子を {2} 持つことができ {2,1} ます)。 主キーの外部キー部分を作成することによって、所有者と所有されているエンティティの間のリレーションシップが変更不可になり、集計セマンティクスがより適切に反映されます。 既定では、これが EF Core になります。

この例では、クラスを使用し `Distributor` ます。

[!code-csharp[Distributor](../../../samples/core/Modeling/OwnedEntities/Distributor.cs?name=Distributor)]

既定では、ナビゲーションプロパティで参照される所有型に使用される主キーはになり `ShippingCenters` `("DistributorId", "Id")` ます。ここで、 `"DistributorId"` は FK で、 `"Id"` は一意の `int` 値です。

異なる PK 呼び出しを構成するには、次のようにし `HasKey` ます。

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

> [!NOTE]
> EF Core 3.0 `WithOwner()` メソッドが存在しない場合は、この呼び出しを削除する必要があります。 また、主キーが自動的に検出されないため、常に指定されています。

## <a name="mapping-owned-types-with-table-splitting"></a>所有型のテーブル分割へのマッピング

リレーショナルデータベースを使用する場合、既定では、参照所有型は、所有者と同じテーブルにマップされます。 そのためには、テーブルを2つの列に分割する必要があります。つまり、所有者のデータを格納するために使用される列と、所有されているエンティティのデータを格納するために使用される列があります。 これは、[テーブル分割](table-splitting.md)と呼ばれる一般的な機能です。

既定では、EF Core は、 _Navigation_OwnedEntityProperty_のパターンに従って、所有されているエンティティ型のプロパティのデータベース列に名前を指定します。 そのため、' `StreetAddress` Orders ' テーブルには ' ShippingAddress_Street ' と ' ShippingAddress_City ' という名前のプロパティが表示されます。

メソッドを使用して、 `HasColumnName` これらの列の名前を変更できます。

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

> [!NOTE]
> [Ignore](/dotnet/api/microsoft.entityframeworkcore.metadata.builders.ownednavigationbuilder.ignore)などの通常のエンティティ型構成メソッドは、ほとんどの場合、同じ方法で呼び出すことができます。

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>複数の所有型間で同じ .NET 型を共有する

所有されているエンティティ型は、他の所有エンティティ型と同じ .NET 型にすることができます。したがって、.NET 型は所有型を特定するのに十分ではない可能性があります。

そのような場合は、所有者から所有されているエンティティを指すプロパティが、所有されているエンティティ型の_ナビゲーションを定義_するようになります。 EF Core の観点から見ると、定義のナビゲーションは、.NET 型と共に型の id の一部になります。

たとえば、次のクラスで `ShippingAddress` は、との `BillingAddress` 両方が同じ .net 型です `StreetAddress` 。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

EF Core がこれらのオブジェクトの追跡対象インスタンスを区別する方法を理解するために、定義ナビゲーションが、所有者のキーの値と所有型の .NET 型の値と共にインスタンスのキーの一部になることを考えると役立つ場合があります。

## <a name="nested-owned-types"></a>入れ子になった所有型

この例で `OrderDetails` `BillingAddress` `ShippingAddress` は、と (両方の型) を所有して `StreetAddress` います。 また、`OrderDetails` は `DetailedOrder` 型に所有されています。

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

所有型に移動するたびに、完全に独立した構成で個別のエンティティ型が定義されます。

所有されている型は、入れ子になった所有型に加えて、所有されているエンティティが依存側にある限り、所有者または別のエンティティのいずれかになることがある標準エンティティを参照できます。 この機能は、EF6 の複合型とは別に、所有エンティティ型を設定します。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

## <a name="configuring-owned-types"></a>所有する型の構成

`OwnsOne`このモデルを構成するには、fluent 呼び出しでメソッドをチェーンすることができます。

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

`WithOwner`所有者を指すナビゲーションプロパティを定義するための呼び出しが使用されていることに注意してください。 所有権に含まれていない所有者のエンティティ型へのナビゲーションを定義するには、 `WithOwner()` 引数を指定せずにを呼び出す必要があります。

また `OwnedAttribute` 、との両方でを使用して、この結果を実現することもでき `OrderDetails` `StreetAddress` ます。

また、の呼び出しに注意して `Navigation` ください。 EFCore 5.0 では、所有されている型へのナビゲーションプロパティを、所有され[ていないナビゲーションプロパティのため](relationships.md#configuring-navigation-properties)にさらに構成できます。

## <a name="storing-owned-types-in-separate-tables"></a>個別のテーブルへの所有型の格納

また、EF6 複合型とは異なり、所有型は所有者とは別のテーブルに格納できます。 所有型を所有者と同じテーブルにマップする規則をオーバーライドするには、 `ToTable` を呼び出して別のテーブル名を指定するだけです。 次の例では、 `OrderDetails` とその2つのアドレスをから別のテーブルにマップし `DetailedOrder` ます。

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

また、を使用してこれを行うこともできますが、所有されている `TableAttribute` 型に複数のナビゲーションがある場合は、複数のエンティティ型が同じテーブルにマップされるため、これは失敗することに注意してください。

## <a name="querying-owned-types"></a>所有型のクエリ

所有者に問い合わせるとき、所有されている型が既定で含まれます。 所有されている `Include` 型が別のテーブルに格納されている場合でも、メソッドを使用する必要はありません。 前に説明したモデルに基づいて、次のクエリは、データベースから所有されている2つのクエリを取得し `Order` `OrderDetails` `StreetAddresses` ます。

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a>制限事項

これらの制限事項のいくつかは、所有エンティティ型の動作のしくみに関するものですが、他のいくつかは将来のリリースで削除できる可能性がある制限です。

### <a name="by-design-restrictions"></a>設計上の制限

- 所有型のを作成することはできません。 `DbSet<T>`
- `Entity<T>()`で所有型を使用してを呼び出すことはできません。`ModelBuilder`

### <a name="current-shortcomings"></a>現在の欠点

- 所有エンティティ型に継承階層を含めることはできません
- 所有されているエンティティ型への参照ナビゲーションは、所有者から別のテーブルに明示的にマップされていない限り、null にすることはできません
- 所有されているエンティティ型のインスタンスを複数の所有者が共有することはできません (これは、所有エンティティ型を使用して実装できない値オブジェクトの既知のシナリオです)

### <a name="shortcomings-in-previous-versions"></a>以前のバージョンの欠点

- EF Core 2.0 では、所有されているエンティティ型へのナビゲーションは、所有しているエンティティが所有者階層から別のテーブルに明示的にマップされている場合を除き、派生エンティティ型で宣言することはできません。 この制限は EF Core 2.1 で削除されました
- EF Core 2.0 および2.1 では、所有型へのナビゲーションのみがサポートされていました。 この制限は EF Core 2.2 で削除されました
