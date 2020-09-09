---
title: 接続の回復性-EF Core
description: 接続の回復性を使用して、失敗したコマンドを Entity Framework Core で自動的に再試行する
author: rowanmiller
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: 6dd3d3eadb218ab32f373e44e2013d017e2966d8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617760"
---
# <a name="connection-resiliency"></a>接続の回復

接続の回復性は、失敗したデータベースコマンドを自動的に再試行します。 この機能は、"実行戦略" を指定することによって任意のデータベースで使用できます。これには、エラーを検出してコマンドを再試行するために必要なロジックがカプセル化されています。 EF Core プロバイダーは、特定のデータベースエラー条件と最適な再試行ポリシーに合わせて、実行戦略を提供できます。

例として、SQL Server プロバイダーには、SQL Server (SQL Azure を含む) に特化した実行方法が用意されています。 再試行できる例外の種類を認識しており、最大再試行回数、再試行間隔などの既定値があります。

実行方法は、コンテキストのオプションを構成するときに指定します。 これは通常、 `OnConfiguring` 派生コンテキストのメソッドにあります。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

`Startup.cs`ASP.NET Core アプリケーションの場合:

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a>カスタム実行戦略

既定値を変更する必要がある場合は、独自のカスタム実行戦略を登録するメカニズムがあります。

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

エラーに対して自動的に再試行を行う実行方法は、失敗した再試行ブロックで各操作を再生できる必要があります。 再試行が有効になっている場合、EF Core 経由で実行する各操作が独自の再試行可能な操作になります。 つまり、一時的な障害が発生した場合、各クエリとの各呼び出し `SaveChanges()` が1つの単位として再試行されます。

ただし、コードがを使用してトランザクションを開始する場合、 `BeginTransaction()` 1 つの単位として処理する必要がある独自の操作グループを定義する場合は、トランザクション内のすべてのものを再生して、エラーが発生する必要があります。 実行戦略を使用しているときに、次のような例外が発生します。

> InvalidOperationException: 構成された実行方法 ' SqlServerRetryingExecutionStrategy ' は、ユーザーが開始したトランザクションをサポートしていません。 'DbContext.Database.CreateExecutionStrategy()' から返された実行戦略を使用して、再試行可能なユニットとしてトランザクション内のすべての操作を実行します。

これを解決するには、実行する必要があるすべてを表すデリゲートを使用して、手動で実行戦略を呼び出します。 一時的な障害が発生した場合、実行戦略は、デリゲートをもう一度呼び出します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

この方法は、アンビエントトランザクションと共に使用することもできます。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a>トランザクションのコミットエラーとべき等の問題

一般に、接続エラーが発生した場合は、現在のトランザクションがロールバックされます。 ただし、トランザクションのコミット中に接続が切断された場合、トランザクションの結果の状態は不明になります。 

既定では、トランザクションがロールバックされた場合と同様に操作が再試行されますが、その場合、新しいデータベースの状態に互換性がない場合、または自動生成されたキー値を使用して新しい行を挿入する場合など、操作が特定の状態に依存してい **ない場合は** 、例外が発生します。

これに対処するには、いくつかの方法があります。

### <a name="option-1---do-almost-nothing"></a>オプション 1-(ほぼ) 何もしない

トランザクションのコミット中に接続エラーが発生する可能性は低いため、この状態が実際に発生した場合にアプリケーションが失敗する可能性があります。

ただし、重複する行を追加するのではなく、例外がスローされるようにするために、ストアによって生成されたキーを使用しないようにする必要があります。 クライアントによって生成された GUID 値またはクライアント側の値ジェネレーターを使用することを検討してください。

### <a name="option-2---rebuild-application-state"></a>オプション 2-アプリケーションの状態を再構築する

1. 現在のを破棄 `DbContext` します。
2. 新しいを作成 `DbContext` し、データベースからアプリケーションの状態を復元します。
3. 最後の操作が正常に完了していない可能性があることをユーザーに通知します。

### <a name="option-3---add-state-verification"></a>オプション 3-状態検証の追加

データベースの状態を変更するほとんどの操作では、成功したかどうかを確認するコードを追加できます。 EF には、これを簡単にするための拡張メソッドが用意されて `IExecutionStrategy.ExecuteInTransaction` います。

このメソッドは、トランザクションを開始してコミットし `verifySucceeded` ます。また、トランザクションのコミット中に一時的なエラーが発生したときに呼び出されるパラメーターの関数も受け入れます。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> `SaveChanges`が `acceptAllChangesOnSuccess` に設定されて `false` いる場合は、エンティティの状態が `Blog` `Unchanged` 成功した場合にに変更されないように、をに設定し `SaveChanges` ます。 これにより、コミットが失敗し、トランザクションがロールバックされた場合に、が同じ操作を再試行することができます。

### <a name="option-4---manually-track-the-transaction"></a>オプション 4-トランザクションを手動で追跡する

ストアによって生成されたキーを使用する必要がある場合、または実行された操作に依存しないコミットエラーを処理する汎用的な方法が必要な場合は、コミットが失敗したときにチェックされる ID を各トランザクションに割り当てることができます。

1. トランザクションの状態を追跡するために使用するデータベースにテーブルを追加します。
2. 各トランザクションの開始時に、テーブルに行を挿入します。
3. コミット中に接続が失敗した場合は、データベース内の対応する行が存在するかどうかを確認します。
4. コミットが成功した場合は、対応する行を削除して、テーブルの増加を回避します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> 検証に使用されるコンテキストに、トランザクションのコミット中に失敗した場合の検証中に再び失敗する可能性があるという実行方法が定義されていることを確認します。
