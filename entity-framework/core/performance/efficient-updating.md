---
title: 効率的な更新-EF Core
description: Entity Framework Core を使用した効率的な更新のパフォーマンスガイド
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-updating
ms.openlocfilehash: f8e222991af52cd7cae6089e95ad6634b6b949f8
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543134"
---
# <a name="efficient-updating"></a>効率的な更新

## <a name="batching"></a>バッチ処理

EF Core は、すべての更新を1回のラウンドトリップで自動的にバッチ処理することで、ラウンドトリップを最小限に抑えるのに役立ちます。 以下、具体例に沿って説明します。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#SaveChangesBatching)]

上の例では、データベースからブログを読み込み、その URL を変更して、2つの新しいブログを追加しています。これを適用するには、2つの SQL INSERT ステートメントと1つの UPDATE ステートメントをデータベースに送信します。 ブログのインスタンスが追加されると、これらの変更が1つずつ送信されるのではなく、EF Core によって内部的に追跡され、が呼び出されると、1回のラウンドトリップで実行 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> されます。

EF が1回のラウンドトリップでバッチを処理するステートメントの数は、使用されているデータベースプロバイダーによって異なります。 たとえば、パフォーマンス分析では、4つ未満のステートメントが関係している場合に、SQL Server の効率が低下するようにバッチ処理が行われています。 同様に、バッチ処理の利点は SQL Server に対して40ステートメントの後で低下するので、EF Core は既定では1つのバッチで最大42ステートメントのみを実行し、別のラウンドトリップで追加のステートメントを実行します。

ユーザーは、これらのしきい値を調整してパフォーマンスを向上させることができますが、これらを変更する前に慎重にベンチマークすることもできます。

[!code-csharp[Main](../../../samples/core/Performance/BatchTweakingContext.cs#BatchTweaking)]

## <a name="bulk-updates"></a>一括更新

すべての従業員に対して発生させたいと考えてみましょう。 この EF Core の一般的な実装は、次のようになります。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithoutBulk)]

これは完全に有効なコードですが、パフォーマンスの観点から何が実行されているかを分析してみましょう。

* すべての関連する従業員を読み込むために、データベースのラウンドトリップが実行されます。これにより、給与のみが必要な場合でも、従業員のすべての行データがクライアントに取り込まれることに注意してください。
* EF Core の変更の追跡では、エンティティの読み込み時にスナップショットを作成し、それらのスナップショットをインスタンスと比較して、どのプロパティが変更されたかを確認します。
* すべての変更を保存するために、2つ目のデータベースのラウンドトリップが実行されます。 バッチ処理によってすべての変更が1回のラウンドトリップで実行されますが、EF Core は、データベースによって実行される必要がある、従業員ごとに UPDATE ステートメントを送信します。

リレーショナルデータベースでは *一括更新* もサポートされているため、上記のは次の1つの SQL ステートメントとして書き換えることができます。

```sql
UPDATE [Employees] SET [Salary] = [Salary] + 1000;
```

これにより、すべての操作が1回のラウンドトリップで実行されます。データベースに実際のデータが読み込まれたり送信されたりすることはなく、EF の変更追跡メカニズムを使用しなくても、オーバーヘッドが増加します。

残念ながら、現在、EF では一括更新を実行するための Api は提供されていません。 これらが導入されるまで、生の SQL を使用して、パフォーマンスが重要な操作を実行できます。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithBulk)]
