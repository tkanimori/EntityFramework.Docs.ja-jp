---
title: テーブル分割 - EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 04/10/2019
ms.assetid: 0EC2CCE1-BD55-45D8-9EA9-20634987F094
uid: core/modeling/table-splitting
ms.openlocfilehash: 4a0bfaf017106a0bfdff084b1c472bdc17459a89
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562588"
---
# <a name="table-splitting"></a>テーブル分割

>[!NOTE]
> この機能は、EF Core 2.0 の新機能です。

EF Core は、2 つ以上のエンティティを 1 つの行にマップすることができます。 これは呼び出されます_テーブル分割_または_テーブルの共有_します。

## <a name="configuration"></a>構成

テーブル分割を同じテーブルにマップする必要があるエンティティ型を使用するには、同じ列にマップされている主キーと 1 つのエンティティ型の主キーと同じテーブル内の別の間で構成されている少なくとも 1 つのリレーションシップが。

テーブル分割の一般的なシナリオが使用して、列のサブセットのみテーブルのパフォーマンス以上、カプセル化。

この例では`Order`のサブセットを表す`DetailedOrder`します。

[!code-csharp[Order](../../../samples/core/Modeling/TableSplitting/Order.cs?name=Order)]

[!code-csharp[DetailedOrder](../../../samples/core/Modeling/TableSplitting/DetailedOrder.cs?name=DetailedOrder)]

呼び出すだけでなく、必要な構成`HasBaseType((string)null)`マッピングを回避するために`DetailedOrder`と同じ階層に`Order`します。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=TableSplitting&highlight=3)]

参照してください、[完全なサンプル プロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/TableSplitting)詳細なコンテキスト。

## <a name="usage"></a>使用法

保存して、テーブル分割を使用してエンティティのクエリを実行するその他のエンティティと同じ方法で行われます、唯一の違いは、挿入行の共有のすべてのエンティティを追跡する必要があります。

[!code-csharp[Usage](../../../samples/core/Modeling/TableSplitting/Program.cs?name=Usage)]

## <a name="concurrency-tokens"></a>同時実行トークン

同時実行トークンを持つテーブルを共有するエンティティ型のいずれかの場合は、同じテーブルにマップされているエンティティの 1 つだけが更新されたときに古い同時実行トークンの値を回避するために他のすべてのエンティティ型に含まする必要があります。

使用側コードに公開を避けるためには、シャドウ状態の 1 つ作成します。

[!code-csharp[TableSplittingConfiguration](../../../samples/core/Modeling/TableSplitting/TableSplittingContext.cs?name=ConcurrencyToken&highlight=2)]