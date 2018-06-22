---
title: 接続の回復の EF コア
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
ms.locfileid: "26053532"
---
# <a name="connection-resiliency"></a><span data-ttu-id="825ce-102">接続の回復</span><span class="sxs-lookup"><span data-stu-id="825ce-102">Connection Resiliency</span></span>

<span data-ttu-id="825ce-103">接続の回復は、失敗したデータベース コマンドを自動的に再試行します。</span><span class="sxs-lookup"><span data-stu-id="825ce-103">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="825ce-104">機能は、「実行方式」、障害を検出して、コマンドを再試行するためのロジックをカプセル化されているを指定することによって、任意のデータベースで使用できます。</span><span class="sxs-lookup"><span data-stu-id="825ce-104">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="825ce-105">EF コア プロバイダーは、特定のデータベースの障害、最適な再試行ポリシーに合わせて実行ストラテジを指定できます。</span><span class="sxs-lookup"><span data-stu-id="825ce-105">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="825ce-106">たとえば、SQL Server プロバイダーには、SQL Server (SQL Azure を含む) に特別にカスタマイズされている実行方法が含まれています。</span><span class="sxs-lookup"><span data-stu-id="825ce-106">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="825ce-107">再試行できる例外の種類を認識し、最大再試行回数、再試行などの間の遅延の実用的な既定値を持ちます。</span><span class="sxs-lookup"><span data-stu-id="825ce-107">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="825ce-108">実行方法は、コンテキストのオプションを構成するときに指定します。</span><span class="sxs-lookup"><span data-stu-id="825ce-108">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="825ce-109">これは通常、`OnConfiguring`メソッド、または、派生のコンテキストの`Startup.cs`ASP.NET Core アプリケーションです。</span><span class="sxs-lookup"><span data-stu-id="825ce-109">This is typically in the `OnConfiguring` method of your derived context, or in `Startup.cs` for an ASP.NET Core application.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

## <a name="custom-execution-strategy"></a><span data-ttu-id="825ce-110">カスタムの実行方法</span><span class="sxs-lookup"><span data-stu-id="825ce-110">Custom execution strategy</span></span>

<span data-ttu-id="825ce-111">既定値のいずれかを変更する場合、独自のカスタムの実行方法を登録するメカニズムがあります。</span><span class="sxs-lookup"><span data-stu-id="825ce-111">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="825ce-112">実行の戦略とトランザクション</span><span class="sxs-lookup"><span data-stu-id="825ce-112">Execution strategies and transactions</span></span>

<span data-ttu-id="825ce-113">エラー発生時に自動的に再試行する実行方法は、失敗した再試行ブロック内の各操作を再生できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="825ce-113">An execution strategy that automatically retries on failures needs to be able to play back each operation in an retry block that fails.</span></span> <span data-ttu-id="825ce-114">再試行を有効にすると、EF コアを使用して実行する各操作操作になります独自再試行可能なつまり各クエリへの各呼び出し`SaveChanges()`単位として、一時的なエラーが発生した場合は再試行されます。</span><span class="sxs-lookup"><span data-stu-id="825ce-114">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation, i.e. each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="825ce-115">ただし、コードを使用してトランザクションを開始する場合`BeginTransaction()`を単位として扱う必要のある操作のグループを定義する、つまり、トランザクション内のすべて必要がありますを再生できるものと、エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="825ce-115">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, i.e. everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="825ce-116">実行方法を使用するときに実行しようとすると、次のような例外が表示されます。</span><span class="sxs-lookup"><span data-stu-id="825ce-116">You will receive an exception like the following if you attempt to do this when using an execution strategy.</span></span>

> <span data-ttu-id="825ce-117">InvalidOperationException: 構成済みの実行方法 'SqlServerRetryingExecutionStrategy' はユーザーが開始したトランザクションをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="825ce-117">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="825ce-118">'DbContext.Database.CreateExecutionStrategy()' によって返される実行方法を使用して、再試行可能な単位としてトランザクション内のすべての操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="825ce-118">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="825ce-119">ソリューションでは、手動で実行する必要があるすべてのものを表すデリゲートを使用して、実行方法を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="825ce-119">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="825ce-120">一時的な障害が発生した場合、実行方法は、デリゲートをもう一度呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="825ce-120">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="825ce-121">トランザクションのコミット エラーおよびべき等問題</span><span class="sxs-lookup"><span data-stu-id="825ce-121">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="825ce-122">一般に、接続エラーが発生したときに、現在のトランザクションはロールバックされます。</span><span class="sxs-lookup"><span data-stu-id="825ce-122">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="825ce-123">ただし場合は、トランザクション中に、接続が削除されたがコミット、その結果、トランザクションの状態が不明です。</span><span class="sxs-lookup"><span data-stu-id="825ce-123">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> <span data-ttu-id="825ce-124">これを参照してください[ブログの投稿](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="825ce-124">See this [blog post](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) for more details.</span></span>

<span data-ttu-id="825ce-125">既定では、実行方法、操作を再試行、トランザクションがロールバックがケースではない場合この例外が発生する場合は、新しいデータベースの状態は、互換性がないか、発生する可能性**データの破損**場合、操作は、たとえば、自動生成されたキー値を持つ新しい行を挿入するときに、特定の状態に依存しません。</span><span class="sxs-lookup"><span data-stu-id="825ce-125">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="825ce-126">これに対処するいくつかの方法はあります。</span><span class="sxs-lookup"><span data-stu-id="825ce-126">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="825ce-127">オプション 1 - Do nothing (ほとんど)</span><span class="sxs-lookup"><span data-stu-id="825ce-127">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="825ce-128">トランザクションのコミット中に接続エラーの可能性は低いに失敗し、この条件が実際に発生した場合にアプリケーションで許容される場合があります。</span><span class="sxs-lookup"><span data-stu-id="825ce-128">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="825ce-129">ただし、重複する行を追加する代わりに例外がスローされることを確認するためにストア生成のキーを使用しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="825ce-129">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="825ce-130">クライアントで生成された GUID 値またはクライアント側の値ジェネレーターを使用してください。</span><span class="sxs-lookup"><span data-stu-id="825ce-130">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="825ce-131">オプション 2 - アプリケーションの状態を再構築</span><span class="sxs-lookup"><span data-stu-id="825ce-131">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="825ce-132">現在の破棄`DbContext`です。</span><span class="sxs-lookup"><span data-stu-id="825ce-132">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="825ce-133">新しい`DbContext`とデータベースからのアプリケーションの状態を復元します。</span><span class="sxs-lookup"><span data-stu-id="825ce-133">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="825ce-134">最後の操作が完了していないが正常にユーザーに通知します。</span><span class="sxs-lookup"><span data-stu-id="825ce-134">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="825ce-135">オプション 3 - 状態の検証を追加します</span><span class="sxs-lookup"><span data-stu-id="825ce-135">Option 3 - Add state verification</span></span>

<span data-ttu-id="825ce-136">データベースの状態を変更する操作のほとんどの成功したかどうかを確認するコードを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="825ce-136">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="825ce-137">これを簡単に拡張メソッドを提供する EF`IExecutionStrategy.ExecuteInTransaction`です。</span><span class="sxs-lookup"><span data-stu-id="825ce-137">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="825ce-138">このメソッドは、開始し、トランザクションをコミットし、関数で指定することも、`verifySucceeded`トランザクションのコミット中に一時的なエラーが発生したときに呼び出されるパラメーター。</span><span class="sxs-lookup"><span data-stu-id="825ce-138">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="825ce-139">ここで`SaveChanges`が呼び出されると`acceptAllChangesOnSuccess`に設定`false`の状態が変更されないように、`Blog`エンティティを`Unchanged`場合`SaveChanges`は成功します。</span><span class="sxs-lookup"><span data-stu-id="825ce-139">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="825ce-140">これにより、コミットが失敗するし、トランザクションはロールバック、同じ操作を再試行してください。</span><span class="sxs-lookup"><span data-stu-id="825ce-140">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="825ce-141">オプション 4 - 手動でトランザクションの追跡</span><span class="sxs-lookup"><span data-stu-id="825ce-141">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="825ce-142">ストア生成のキーを使用するか、実行される操作に依存せずにコミットのエラー処理の一般的な方法を必要する必要がある場合の各トランザクションはコミットに失敗したときにチェックインされている ID 割り当てでした。</span><span class="sxs-lookup"><span data-stu-id="825ce-142">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="825ce-143">トランザクションの状態を追跡するために使用されるデータベースにテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="825ce-143">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="825ce-144">各トランザクションの先頭にテーブルに行を挿入します。</span><span class="sxs-lookup"><span data-stu-id="825ce-144">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="825ce-145">コミット中に、接続に失敗した場合は、データベース内の対応する行の存在を確認します。</span><span class="sxs-lookup"><span data-stu-id="825ce-145">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="825ce-146">コミットが成功した場合は、テーブルの増加を避けるために、対応する行を削除します。</span><span class="sxs-lookup"><span data-stu-id="825ce-146">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="825ce-147">検証に使用するコンテキストに、接続がトランザクションのコミット中に失敗した場合、検証時に再び失敗する可能性が定義されている実行方法があることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="825ce-147">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
