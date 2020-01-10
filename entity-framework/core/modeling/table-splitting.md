---
title: テーブル分割-EF Core
description: Entity Framework Core を使用してテーブル分割を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 01/03/2020
uid: core/modeling/table-splitting
ms.openlocfilehash: c38d3ee0efa82f84a1051017ae40c9f3fdd57f1f
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781171"
---
# <a name="table-splitting"></a>テーブル分割

EF Core を使用すると、複数のエンティティを1つの行にマップできます。 これは、_テーブル分割_または_テーブル共有_と呼ばれます。

## <a name="configuration"></a>の構成

テーブル分割を使用するには、エンティティ型を同じテーブルにマップする必要があります。主キーを同じ列にマップし、少なくとも1つのエンティティ型の主キーと同じテーブル内の別のリレーションシップを構成します。

テーブル分割の一般的なシナリオでは、テーブル内の列のサブセットのみを使用して、パフォーマンスまたはカプセル化を向上させることができます。

この例では `Order` `DetailedOrder`のサブセットを表します。

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

必要な構成に加えて、`Property(o => o.Status).HasColumnName("Status")` を呼び出して、`DetailedOrder.Status` を `Order.Status`と同じ列にマップします。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting)]

> [!TIP]
> 詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)を参照してください。

## <a name="usage"></a>使用状況

テーブル分割を使用したエンティティの保存とクエリは、他のエンティティと同じ方法で実行されます。

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="optional-dependent-entity"></a>省略可能な依存エンティティ

> [!NOTE]
> この機能は EF Core 3.0 で導入されました。

依存エンティティによって使用されているすべての列がデータベースで `NULL` されている場合は、クエリの実行時にインスタンスが作成されません。 これにより、オプションの依存エンティティをモデル化できます。この場合、プリンシパルのリレーションシッププロパティは null になります。 これは、依存しているすべてのプロパティは省略可能であり、`null`に設定されていますが、これは想定されていない可能性があることに注意してください。

## <a name="concurrency-tokens"></a>同時実行トークン

テーブルを共有するいずれかのエンティティ型に同時実行トークンが含まれている場合は、他のすべてのエンティティ型にも含める必要があります。 これは、同じテーブルにマップされたエンティティの1つだけが更新される場合に、古い同時実行トークンの値を回避するために必要です。

コンシューマー側のコードに同時実行トークンが公開されないようにするには、 [shadow プロパティ](xref:core/modeling/shadow-properties)として作成することができます。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]
