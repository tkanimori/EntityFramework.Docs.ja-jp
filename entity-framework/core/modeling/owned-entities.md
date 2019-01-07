---
title: 所有エンティティ型 - EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 2B0BADCE-E23E-4B28-B8EE-537883E16DF3
uid: core/modeling/owned-entities
ms.openlocfilehash: fe7e07b8bd483fb3f9b672ee78ef7541f06a21a4
ms.sourcegitcommit: e66745c9f91258b2cacf5ff263141be3cba4b09e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2019
ms.locfileid: "54058774"
---
# <a name="owned-entity-types"></a>所有エンティティ型

>[!NOTE]
> この機能は、EF Core 2.0 の新機能です。

EF Core では、その他のエンティティ型のナビゲーション プロパティにのみ表示できるモデルのエンティティ型にできます。 これらと呼ばれます_エンティティ型を所有している_します。 所有エンティティ型を含むエンティティはその_所有者_します。

## <a name="explicit-configuration"></a>明示的な構成

エンティティ型は含まれません EF Core によって、モデルの規則により所有されています。 使用することができます、`OwnsOne`メソッド`OnModelCreating`を持つ型の注釈を設定または`OwnedAttribute`(EF Core 2.1 の新機能) 所有型として型を構成します。

この例で`StreetAddress`は identity プロパティは含まずの一種です。 特定の注文の配送先住所を指定するために、Order 型のプロパティとして使用されます。

使用して、`OwnedAttribute`別のエンティティ型から参照されている場合、所有エンティティとして扱う。

[!code-csharp[StreetAddress](../../../samples/core/Modeling/OwnedEntities/StreetAddress.cs?name=StreetAddress)]

[!code-csharp[Order](../../../samples/core/Modeling/OwnedEntities/Order.cs?name=Order)]

使用することも、`OwnsOne`メソッド`OnModelCreating`ことを指定する、`ShippingAddress`プロパティの所有エンティティは、`Order`エンティティ型と必要な場合、その他のファセットを構成します。

[!code-csharp[OwnsOne](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOne)]

場合、`ShippingAddress`プロパティがプライベートで、`Order`型の文字列バージョンを使用することができます、`OwnsOne`メソッド。

[!code-csharp[OwnsOneString](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneString)]

参照してください、[完全なサンプル プロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/OwnedEntities)詳細なコンテキスト。 

## <a name="implicit-keys"></a>暗黙的なキー

所有型で構成されている`OwnsOne`参照ナビゲーションで検出された、または一対一のリレーションシップの所有者と常に、外部キーの値が一意である独自のキー値を必要なしないためです。 前の例では、`StreetAddress`型がキー プロパティを定義する必要はありません。  

EF Core がこれらのオブジェクトを追跡する方法を理解するためにとして主キーが作成されたことを考えるとわかり、[プロパティをシャドウ](xref:core/modeling/shadow-properties)所有型のです。 所有型のインスタンスのキーの値は、所有者のインスタンスのキーの値と同じになります。

## <a name="collections-of-owned-types"></a>所有型のコレクション

>[!NOTE]
> これは EF Core 2.2 の新機能です。

所有型のコレクションを構成する`OwnsMany`で使用する必要があります`OnModelCreating`します。 ただし、主キーは構成されません規則により、ため、これを明示的に指定する必要があります。 このタイプのエンティティの所有者とシャドウ状態にすることもある追加の固有プロパティへの外部キーを組み込むことの複雑なキーを使用して、一般的には。

[!code-csharp[OwnsMany](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsMany)]

## <a name="mapping-owned-types-with-table-splitting"></a>マッピングは、テーブル分割を持つ型を所有しています。

リレーショナルを使用する場合、データベース規則参照によって所有されている型、所有者と同じテーブルにマップされます。 2 つのテーブルを分割する必要があります。 一部の列は、所有者のデータの格納に使用すると、所有エンティティのデータの格納に使用されるいくつかの列。 これは、テーブル分割と呼ばれる一般的な機能です。

> [!TIP]
> テーブル分割と保存されている種類は、所有する EF6 で使用する方法の複合型にも同様に使用します。

規則により、EF Core は、次のパターンの所有鉛てぃてぃ型のプロパティのデータベース列を名前は_Navigation_OwnedEntityProperty_します。 そのため、`StreetAddress`プロパティ名 'ShippingAddress_Street' と 'ShippingAddress_City' と 'Orders' テーブルに表示されます。

追加することができます、`HasColumnName`それらの列の名前を変更するメソッド。

[!code-csharp[ColumnNames](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=ColumnNames)]

## <a name="sharing-the-same-net-type-among-multiple-owned-types"></a>複数の所有型の間で同じ .NET 型の共有

所有エンティティ型できます別の所有エンティティ型と同じ .NET 型したがって .NET 型できない可能性があります所有型を識別するために十分な。

その場合、所有者から所有エンティティを指すプロパティになります、_ナビゲーションを定義する_の所有エンティティ型。 EF Core の観点からは、定義となるナビゲーションは、.NET 型と型の id の一部です。   

たとえば、次のクラスで`ShippingAddress`と`BillingAddress`はどちらも同じの .NET 型の`StreetAddress`:

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

EF Core がこれらのオブジェクトの追跡対象のインスタンスを区別する方法を理解するためには、定義となるナビゲーションが所有者のキーの値と共にインスタンスのキーの一部と所有型の .NET 型になったことを考えるとわかり場合があります。

## <a name="nested-owned-types"></a>入れ子にされた所有型

この例では`OrderDetails`所有`BillingAddress`と`ShippingAddress`、これは、どちらも`StreetAddress`型。 また、`OrderDetails` は `DetailedOrder` 型に所有されています。

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/OwnedEntities/DetailedOrder.cs?name=DetailedOrder)]

[!code-csharp[OrderStatus](../../../samples/core/Modeling/OwnedEntities/OrderStatus.cs?name=OrderStatus)]

入れ子になったの所有型だけでなく所有型は、正規のエンティティを参照できますでは、所有者または別のエンティティとして所有エンティティは、依存側の。 この機能は、EF6 の複合型とは別の所有エンティティ型を設定します。

[!code-csharp[OrderDetails](../../../samples/core/Modeling/OwnedEntities/OrderDetails.cs?name=OrderDetails)]

チェーンすることができます、`OwnsOne`メソッドでこのモデルを構成する fluent 呼び出し。

[!code-csharp[OwnsOneNested](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneNested)]

使用して同じことを実現することも`OwnedAttribute`両方で`OrderDetails`と`StreetAdress`します。

## <a name="storing-owned-types-in-separate-tables"></a>別のテーブルに所有型を格納します。

また、EF6 の複合型とは異なりは、所有型を所有者から別のテーブルに格納できます。 所有型を所有者と同じテーブルにマップする規則を上書きするために呼び出すだけでできます`ToTable`別のテーブルの名前を指定します。 次の例はマップ`OrderDetails`と、その 2 つのアドレスから別のテーブルに`DetailedOrder`:

[!code-csharp[OwnsOneTable](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=OwnsOneTable)]

## <a name="querying-owned-types"></a>所有型を問い合わせています

所有者に問い合わせるとき、所有されている型が既定で含まれます。 使用する必要はありません、`Include`所有型が別のテーブルに格納されている場合でも、メソッド。 次のクエリによって取得される前に説明したモデルに基づいて、 `Order`、`OrderDetails`と所有している 2 つ`StreetAddresses`データベースから。

[!code-csharp[DetailedOrderQuery](../../../samples/core/Modeling/OwnedEntities/Program.cs?name=DetailedOrderQuery)]

## <a name="limitations"></a>制限事項

これらの制限の一部は所有されているどのエンティティ型の作業を基本的な機能がいくつか他のユーザー制限は今後のリリースを削除するいただく可能性があります。

### <a name="by-design-restrictions"></a>設計による制限
- 作成することはできません、`DbSet<T>`所有型の
- 呼び出すことはできません`Entity<T>()`所有型の使用 `ModelBuilder`

### <a name="current-shortcomings"></a>現在の欠点
- 継承階層を含む所有エンティティ型はサポートされていません
- 参照のナビゲーションは、明示的に、所有者から別のテーブルにマップされていないエンティティ型を null にすることはできませんを所有しています。
- (これは、所有エンティティ型を使用して実装することはできません。 値オブジェクトのよく知られているシナリオ) 複数の所有者で所有エンティティ型のインスタンスを共有することはできません。

### <a name="shortcomings-in-previous-versions"></a>以前のバージョンの欠点
- EF Core 2.0 では、ナビゲーションには、所有エンティティが別のテーブルに所有者の階層から明示的にマップされていない限り、派生エンティティ型のエンティティ型を宣言できませんを所有します。 EF Core 2.1 でこの制限が削除されました
- EF Core 2.0 および 2.1 の唯一の参照では、所有型にナビゲーションがサポートされていました。 EF Core 2.2 でこの制限が削除されました
