---
title: テーブル分割-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 04/10/2019
ms.assetid: 0EC2CCE1-BD55-45D8-9EA9-20634987F094
uid: core/modeling/table-splitting
ms.openlocfilehash: 684fcfbb66debfd1b89e23c8aaf0a32909378c6b
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149186"
---
# <a name="table-splitting"></a>テーブル分割

>[!NOTE]
> この機能は EF Core 2.0 で新たに追加されています。

EF Core を使用すると、複数のエンティティを1つの行にマップできます。 これは、_テーブル分割_または_テーブル共有_と呼ばれます。

## <a name="configuration"></a>構成

テーブル分割を使用するには、エンティティ型を同じテーブルにマップする必要があります。主キーを同じ列にマップし、少なくとも1つのエンティティ型の主キーと同じテーブル内の別のリレーションシップを構成します。

テーブル分割の一般的なシナリオでは、テーブル内の列のサブセットのみを使用して、パフォーマンスまたはカプセル化を向上させることができます。

この例`Order`では、の`DetailedOrder`サブセットを表します。

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

必要な構成に加えて、を`Property(o => o.Status).HasColumnName("Status")`呼び出して`DetailedOrder.Status` 、と`Order.Status`同じ列にマップします。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

> [!TIP]
> 詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)を参照してください。

## <a name="usage"></a>使用法

テーブル分割を使用したエンティティの保存とクエリは、他のエンティティと同じ方法で実行されます。 EF Core 3.0 以降では、依存エンティティ参照をに`null`することができます。 依存エンティティ`NULL`によって使用されるすべての列がデータベースである場合は、クエリの実行時にインスタンスが作成されません。 また、すべてのプロパティが省略可能であり、がに`null`設定されている場合もありますが、これは想定されていない可能性があります。

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a>同時実行トークン

テーブルを共有するいずれかのエンティティ型が同時実行トークンを持っている場合は、同じテーブルにマップされたエンティティの1つだけが更新されたときに、古い同時実行トークンの値を回避するために、他のすべてのエンティティ型に含める必要があります。

コンシューマー側のコードにそれを公開しないようにするには、シャドウ状態で1つ作成することができます。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]