---
title: 接続の回復 - EF Core
author: rowanmiller
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: d6e31cf2b9b783ea503703536d159b34bf2e18c0
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997191"
---
# <a name="connection-resiliency"></a>接続の復元性

接続の回復性は、失敗したデータベース コマンドを自動的に再試行します。 機能は、「実行戦略」、障害を検出し、コマンドを再試行するのに必要なロジックをカプセル化されているを指定することで、任意のデータベースで使用できます。 EF Core プロバイダーは、特定のデータベース エラー状態と最適な再試行ポリシーに合わせて調整するという実行ストラテジを指定できます。

たとえば、SQL Server プロバイダーには、SQL Server (SQL Azure を含む) に合わせた具体的には、実行戦略が含まれています。 再試行できる例外の種類を認識し、最大再試行回数、再試行などの間の遅延の実用的な既定値を持ちます。

実行戦略は、コンテキストのオプションを構成するときに指定します。 通常、これは、`OnConfiguring`メソッド、または、派生コンテキストの`Startup.cs`ASP.NET Core アプリケーション。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

## <a name="custom-execution-strategy"></a>カスタムの実行方法

これには、既定値を変更する場合、独自のカスタム実行戦略を登録するメカニズムがあります。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a>実行戦略とトランザクション

したときにエラーを自動的に再試行実行戦略は、失敗した再試行ブロック内の各操作を再生できるようにする必要があります。 再試行が有効な場合、EF Core を使用して実行する各操作は、独自の再試行可能な操作になります。 つまり、各クエリ、呼び出しごとに`SaveChanges()`一時的なエラーが発生した場合、ユニットとして再試行されます。

ただし、コードを使用してトランザクションを開始する場合、`BeginTransaction()`の単位として扱う必要のある操作のグループを定義して、トランザクション内ですべてを再生できるものと、エラーが発生する必要があります。 実行戦略を使用するときに実行しようとすると、次のような例外が表示されます。

> InvalidOperationException: 構成された実行戦略 'SqlServerRetryingExecutionStrategy' はユーザーが開始したトランザクションをサポートしていません。 'DbContext.Database.CreateExecutionStrategy()' から返された実行戦略を使用して、再試行可能なユニットとしてトランザクション内のすべての操作を実行します。

ソリューションでは、手動で実行する必要があるすべてを表すデリゲートの実行戦略を呼び出します。 一時的なエラーが発生した場合、実行戦略によってデリゲートが再び呼び出されます。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a>トランザクション コミットの失敗、冪等性の問題

一般に、接続障害が発生したときに、現在のトランザクションがロールバックされます。 ただし、トランザクション中に、接続が切断される場合がコミットされた、その結果、トランザクションの状態は不明です。 これを参照してください[ブログの投稿](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)の詳細。

既定では、実行戦略は、操作を再試行、トランザクションがロールバックがそうでない場合この例外が発生する場合は、新しいデータベースの状態は互換性がないかになる可能性が**データの破損**場合、操作は、自動生成されたキー値を持つ新しい行を挿入するときなど、特定の状態に依存しません。

これに対処するいくつかの方法はあります。

### <a name="option-1---do-almost-nothing"></a>オプション 1 - Do nothing (ほぼ)

この状態が実際に発生した場合にだけが失敗するアプリケーションで許容される可能性があるために、トランザクションのコミット中に接続エラーが発生する可能性は低くなります。

ただし、重複する行を追加する代わりに、例外がスローされることを確認するためにストア生成キーを使用しないようにする必要があります。 クライアントで生成された GUID 値またはクライアント側の値ジェネレーターを使用してください。

### <a name="option-2---rebuild-application-state"></a>オプション 2 - アプリケーションの状態を再構築

1. 現在の破棄`DbContext`します。
2. 新規作成`DbContext`と、データベースからのアプリケーションの状態を復元します。
3. 最後の操作が完了していない正常にユーザーに通知します。

### <a name="option-3---add-state-verification"></a>オプション 3 - 状態の検証を追加します

ほとんどのデータベースの状態を変更する操作が成功したかどうかをチェックするコードを追加すること勧めします。 EF - 簡単に確認する拡張メソッドを提供する`IExecutionStrategy.ExecuteInTransaction`します。

このメソッドは、開始し、トランザクションをコミットし、内の関数を受け入れることも、`verifySucceeded`トランザクションのコミット中に一時的なエラーが発生したときに呼び出されるパラメーター。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> ここで`SaveChanges`が呼び出されると`acceptAllChangesOnSuccess`に設定`false`の状態の変更を回避するために、`Blog`エンティティ`Unchanged`場合`SaveChanges`が成功するとします。 これにより、コミットに失敗した場合、トランザクションがロールバックは、同じ操作を再試行してください。

### <a name="option-4---manually-track-the-transaction"></a>オプション 4 は、トランザクションを手動で追跡

ストア生成キーを使用して、または実行された操作に依存しない汎用コミットでのエラー処理の方法を必要する必要がある場合は、各トランザクションはコミットに失敗した場合にチェックされている ID 割り当てでした。

1. トランザクションの状態を追跡するために使用するデータベースにテーブルを追加します。
2. 各トランザクションの先頭にあるテーブルに行を挿入します。
3. コミット中に、接続に失敗した場合、データベース内の対応する行の存在を確認します。
4. コミットが成功した場合は、テーブルの増加を回避するために、対応する行を削除します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> 検証に使用するコンテキストのように、接続がトランザクションのコミット中に失敗した場合、検証中にもう一度失敗する可能性が定義されて、実行戦略であることを確認します。
