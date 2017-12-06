---
title: "接続の回復の EF コア"
author: rowanmiller
ms.author: divega
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
ms.technology: entity-framework-core
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: aec69577cd4b19fdebedb33ed6fd8f2665b0a032
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
---
# <a name="connection-resiliency"></a>接続の回復

接続の回復は、失敗したデータベース コマンドを自動的に再試行します。 機能は、「実行方式」、障害を検出して、コマンドを再試行するためのロジックをカプセル化されているを指定することによって、任意のデータベースで使用できます。 EF コア プロバイダーは、特定のデータベースの障害、最適な再試行ポリシーに合わせて実行ストラテジを指定できます。

たとえば、SQL Server プロバイダーには、SQL Server (SQL Azure を含む) に特別にカスタマイズされている実行方法が含まれています。 再試行できる例外の種類を認識し、最大再試行回数、再試行などの間の遅延の実用的な既定値を持ちます。

実行方法は、コンテキストのオプションを構成するときに指定します。 これは通常、`OnConfiguring`メソッド、または、派生のコンテキストの`Startup.cs`ASP.NET Core アプリケーションです。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

## <a name="custom-execution-strategy"></a>カスタムの実行方法

既定値のいずれかを変更する場合、独自のカスタムの実行方法を登録するメカニズムがあります。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a>実行の戦略とトランザクション

エラー発生時に自動的に再試行する実行方法は、失敗した再試行ブロック内の各操作を再生できる必要があります。 再試行を有効にすると、EF コアを使用して実行する各操作操作になります独自再試行可能なつまり各クエリへの各呼び出し`SaveChanges()`単位として、一時的なエラーが発生した場合は再試行されます。

ただし、コードを使用してトランザクションを開始する場合`BeginTransaction()`を単位として扱う必要のある操作のグループを定義する、つまり、トランザクション内のすべて必要がありますを再生できるものと、エラーが発生します。 実行方法を使用するときに実行しようとすると、次のような例外が表示されます。

> InvalidOperationException: 構成済みの実行方法 'SqlServerRetryingExecutionStrategy' はユーザーが開始したトランザクションをサポートしていません。 'DbContext.Database.CreateExecutionStrategy()' によって返される実行方法を使用して、再試行可能な単位としてトランザクション内のすべての操作を実行します。

ソリューションでは、手動で実行する必要があるすべてのものを表すデリゲートを使用して、実行方法を呼び出します。 一時的な障害が発生した場合、実行方法は、デリゲートをもう一度呼び出されます。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a>トランザクションのコミット エラーおよびべき等問題

一般に、接続エラーが発生したときに、現在のトランザクションはロールバックされます。 ただし場合は、トランザクション中に、接続が削除されたがコミット、その結果、トランザクションの状態が不明です。 これを参照してください[ブログの投稿](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)詳細についてはします。

既定では、実行方法、操作を再試行、トランザクションがロールバックがケースではない場合この例外が発生する場合は、新しいデータベースの状態は、互換性がないか、発生する可能性**データの破損**場合、操作は、たとえば、自動生成されたキー値を持つ新しい行を挿入するときに、特定の状態に依存しません。

これに対処するいくつかの方法はあります。

### <a name="option-1---do-almost-nothing"></a>オプション 1 - Do nothing (ほとんど)

トランザクションのコミット中に接続エラーの可能性は低いに失敗し、この条件が実際に発生した場合にアプリケーションで許容される場合があります。

ただし、重複する行を追加する代わりに例外がスローされることを確認するためにストア生成のキーを使用しないようにする必要があります。 クライアントで生成された GUID 値またはクライアント側の値ジェネレーターを使用してください。

### <a name="option-2---rebuild-application-state"></a>オプション 2 - アプリケーションの状態を再構築

1. 現在の破棄`DbContext`です。
2. 新しい`DbContext`とデータベースからのアプリケーションの状態を復元します。
3. 最後の操作が完了していないが正常にユーザーに通知します。

### <a name="option-3---add-state-verification"></a>オプション 3 - 状態の検証を追加します

データベースの状態を変更する操作のほとんどの成功したかどうかを確認するコードを追加することができます。 これを簡単に拡張メソッドを提供する EF`IExecutionStrategy.ExecuteInTransaction`です。

このメソッドは、開始し、トランザクションをコミットし、関数で指定することも、`verifySucceeded`トランザクションのコミット中に一時的なエラーが発生したときに呼び出されるパラメーター。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> ここで`SaveChanges`が呼び出されると`acceptAllChangesOnSuccess`に設定`false`の状態が変更されないように、`Blog`エンティティを`Unchanged`場合`SaveChanges`は成功します。 これにより、コミットが失敗するし、トランザクションはロールバック、同じ操作を再試行してください。

### <a name="option-4---manually-track-the-transaction"></a>オプション 4 - 手動でトランザクションの追跡

ストア生成のキーを使用するか、実行される操作に依存せずにコミットのエラー処理の一般的な方法を必要する必要がある場合の各トランザクションはコミットに失敗したときにチェックインされている ID 割り当てでした。

1. トランザクションの状態を追跡するために使用されるデータベースにテーブルを追加します。
2. 各トランザクションの先頭にテーブルに行を挿入します。
3. コミット中に、接続に失敗した場合は、データベース内の対応する行の存在を確認します。
4. コミットが成功した場合は、テーブルの増加を避けるために、対応する行を削除します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> 検証に使用するコンテキストに、接続がトランザクションのコミット中に失敗した場合、検証時に再び失敗する可能性が定義されている実行方法があることを確認してください。
