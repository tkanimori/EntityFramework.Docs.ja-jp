---
title: テーブル分割-EF Core
description: Entity Framework Core を使用してテーブル分割を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: e7428bc516a69310b6a6f521acc49aee0ba9f802
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526500"
---
# <a name="table-splitting"></a>テーブル分割

EF Core を使用すると、複数のエンティティを1つの行にマップできます。 これは、_テーブル分割_または_テーブル共有_と呼ばれます。

## <a name="configuration"></a>構成

テーブル分割を使用するには、エンティティ型を同じテーブルにマップする必要があります。主キーを同じ列にマップし、少なくとも1つのエンティティ型の主キーと同じテーブル内の別のリレーションシップを構成します。

テーブル分割の一般的なシナリオでは、テーブル内の列のサブセットのみを使用して、パフォーマンスまたはカプセル化を向上させることができます。

この例で `Order` は、のサブセットを表し `DetailedOrder` ます。

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

必要な構成に加え `Property(o => o.Status).HasColumnName("Status")` て、を呼び出して、 `DetailedOrder.Status` と同じ列にマップし `Order.Status` ます。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> 詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)を参照してください。

## <a name="usage"></a>使用法

テーブル分割を使用したエンティティの保存とクエリは、他のエンティティと同じ方法で実行されます。

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a>省略可能な依存エンティティ

> [!NOTE]
> この機能は EF Core 3.0 で導入されました。

依存エンティティによって使用されているすべての列が `NULL` データベース内に存在する場合、クエリの実行時にそのインスタンスのインスタンスは作成されません。 これにより、オプションの依存エンティティをモデル化できます。この場合、プリンシパルのリレーションシッププロパティは null になります。 これは、依存するすべてのプロパティが省略可能で、がに設定されている場合にも発生しますが、これは `null` 想定されていない可能性があります。

## <a name="concurrency-tokens"></a>コンカレンシー トークン

テーブルを共有するいずれかのエンティティ型に同時実行トークンが含まれている場合は、他のすべてのエンティティ型にも含める必要があります。 これは、同じテーブルにマップされたエンティティの1つだけが更新される場合に、古い同時実行トークンの値を回避するために必要です。

コンシューマー側のコードに同時実行トークンが公開されないようにするには、 [shadow プロパティ](xref:core/modeling/shadow-properties)として作成することができます。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
