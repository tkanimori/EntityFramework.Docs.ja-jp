---
title: 接続の回復 - EF Core
author: rowanmiller
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: d5101d0622ddc2c90ddded16b9ec6cc4eb814c36
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283837"
---
# <a name="connection-resiliency"></a><span data-ttu-id="07058-102">接続の復元性</span><span class="sxs-lookup"><span data-stu-id="07058-102">Connection Resiliency</span></span>

<span data-ttu-id="07058-103">接続の回復性は、失敗したデータベース コマンドを自動的に再試行します。</span><span class="sxs-lookup"><span data-stu-id="07058-103">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="07058-104">機能は、「実行戦略」、障害を検出し、コマンドを再試行するのに必要なロジックをカプセル化されているを指定することで、任意のデータベースで使用できます。</span><span class="sxs-lookup"><span data-stu-id="07058-104">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="07058-105">EF Core プロバイダーは、特定のデータベース エラー状態と最適な再試行ポリシーに合わせて調整するという実行ストラテジを指定できます。</span><span class="sxs-lookup"><span data-stu-id="07058-105">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="07058-106">たとえば、SQL Server プロバイダーには、SQL Server (SQL Azure を含む) に合わせた具体的には、実行戦略が含まれています。</span><span class="sxs-lookup"><span data-stu-id="07058-106">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="07058-107">再試行できる例外の種類を認識し、最大再試行回数、再試行などの間の遅延の実用的な既定値を持ちます。</span><span class="sxs-lookup"><span data-stu-id="07058-107">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="07058-108">実行戦略は、コンテキストのオプションを構成するときに指定します。</span><span class="sxs-lookup"><span data-stu-id="07058-108">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="07058-109">通常、これは、`OnConfiguring`メソッド、または、派生コンテキストの`Startup.cs`ASP.NET Core アプリケーション。</span><span class="sxs-lookup"><span data-stu-id="07058-109">This is typically in the `OnConfiguring` method of your derived context, or in `Startup.cs` for an ASP.NET Core application.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

## <a name="custom-execution-strategy"></a><span data-ttu-id="07058-110">カスタムの実行方法</span><span class="sxs-lookup"><span data-stu-id="07058-110">Custom execution strategy</span></span>

<span data-ttu-id="07058-111">これには、既定値を変更する場合、独自のカスタム実行戦略を登録するメカニズムがあります。</span><span class="sxs-lookup"><span data-stu-id="07058-111">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="07058-112">実行戦略とトランザクション</span><span class="sxs-lookup"><span data-stu-id="07058-112">Execution strategies and transactions</span></span>

<span data-ttu-id="07058-113">したときにエラーを自動的に再試行実行戦略は、失敗した再試行ブロック内の各操作を再生できるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="07058-113">An execution strategy that automatically retries on failures needs to be able to play back each operation in a retry block that fails.</span></span> <span data-ttu-id="07058-114">再試行が有効な場合、EF Core を使用して実行する各操作は、独自の再試行可能な操作になります。</span><span class="sxs-lookup"><span data-stu-id="07058-114">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation.</span></span> <span data-ttu-id="07058-115">つまり、各クエリ、呼び出しごとに`SaveChanges()`一時的なエラーが発生した場合、ユニットとして再試行されます。</span><span class="sxs-lookup"><span data-stu-id="07058-115">That is, each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="07058-116">ただし、コードを使用してトランザクションを開始する場合、`BeginTransaction()`の単位として扱う必要のある操作のグループを定義して、トランザクション内ですべてを再生できるものと、エラーが発生する必要があります。</span><span class="sxs-lookup"><span data-stu-id="07058-116">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, and everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="07058-117">実行戦略を使用するときに実行しようとすると、次のような例外が表示されます。</span><span class="sxs-lookup"><span data-stu-id="07058-117">You will receive an exception like the following if you attempt to do this when using an execution strategy:</span></span>

> <span data-ttu-id="07058-118">InvalidOperationException: 構成された実行戦略 'SqlServerRetryingExecutionStrategy' はユーザーが開始したトランザクションをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="07058-118">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="07058-119">'DbContext.Database.CreateExecutionStrategy()' から返された実行戦略を使用して、再試行可能なユニットとしてトランザクション内のすべての操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="07058-119">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="07058-120">ソリューションでは、手動で実行する必要があるすべてを表すデリゲートの実行戦略を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="07058-120">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="07058-121">一時的なエラーが発生した場合、実行戦略によってデリゲートが再び呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="07058-121">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="07058-122">トランザクション コミットの失敗、冪等性の問題</span><span class="sxs-lookup"><span data-stu-id="07058-122">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="07058-123">一般に、接続障害が発生したときに、現在のトランザクションがロールバックされます。</span><span class="sxs-lookup"><span data-stu-id="07058-123">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="07058-124">ただし、トランザクション中に、接続が切断される場合がコミットされた、その結果、トランザクションの状態は不明です。</span><span class="sxs-lookup"><span data-stu-id="07058-124">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> <span data-ttu-id="07058-125">これを参照してください[ブログの投稿](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)の詳細。</span><span class="sxs-lookup"><span data-stu-id="07058-125">See this [blog post](https://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) for more details.</span></span>

<span data-ttu-id="07058-126">既定では、実行戦略は、操作を再試行、トランザクションがロールバックがそうでない場合この例外が発生する場合は、新しいデータベースの状態は互換性がないかになる可能性が**データの破損**場合、操作は、自動生成されたキー値を持つ新しい行を挿入するときなど、特定の状態に依存しません。</span><span class="sxs-lookup"><span data-stu-id="07058-126">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="07058-127">これに対処するいくつかの方法はあります。</span><span class="sxs-lookup"><span data-stu-id="07058-127">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="07058-128">オプション 1 - Do nothing (ほぼ)</span><span class="sxs-lookup"><span data-stu-id="07058-128">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="07058-129">この状態が実際に発生した場合にだけが失敗するアプリケーションで許容される可能性があるために、トランザクションのコミット中に接続エラーが発生する可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="07058-129">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="07058-130">ただし、重複する行を追加する代わりに、例外がスローされることを確認するためにストア生成キーを使用しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="07058-130">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="07058-131">クライアントで生成された GUID 値またはクライアント側の値ジェネレーターを使用してください。</span><span class="sxs-lookup"><span data-stu-id="07058-131">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="07058-132">オプション 2 - アプリケーションの状態を再構築</span><span class="sxs-lookup"><span data-stu-id="07058-132">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="07058-133">現在の破棄`DbContext`します。</span><span class="sxs-lookup"><span data-stu-id="07058-133">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="07058-134">新規作成`DbContext`と、データベースからのアプリケーションの状態を復元します。</span><span class="sxs-lookup"><span data-stu-id="07058-134">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="07058-135">最後の操作が完了していない正常にユーザーに通知します。</span><span class="sxs-lookup"><span data-stu-id="07058-135">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="07058-136">オプション 3 - 状態の検証を追加します</span><span class="sxs-lookup"><span data-stu-id="07058-136">Option 3 - Add state verification</span></span>

<span data-ttu-id="07058-137">ほとんどのデータベースの状態を変更する操作が成功したかどうかをチェックするコードを追加すること勧めします。</span><span class="sxs-lookup"><span data-stu-id="07058-137">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="07058-138">EF - 簡単に確認する拡張メソッドを提供する`IExecutionStrategy.ExecuteInTransaction`します。</span><span class="sxs-lookup"><span data-stu-id="07058-138">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="07058-139">このメソッドは、開始し、トランザクションをコミットし、内の関数を受け入れることも、`verifySucceeded`トランザクションのコミット中に一時的なエラーが発生したときに呼び出されるパラメーター。</span><span class="sxs-lookup"><span data-stu-id="07058-139">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="07058-140">ここで`SaveChanges`が呼び出されると`acceptAllChangesOnSuccess`に設定`false`の状態の変更を回避するために、`Blog`エンティティ`Unchanged`場合`SaveChanges`が成功するとします。</span><span class="sxs-lookup"><span data-stu-id="07058-140">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="07058-141">これにより、コミットに失敗した場合、トランザクションがロールバックは、同じ操作を再試行してください。</span><span class="sxs-lookup"><span data-stu-id="07058-141">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="07058-142">オプション 4 は、トランザクションを手動で追跡</span><span class="sxs-lookup"><span data-stu-id="07058-142">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="07058-143">ストア生成キーを使用して、または実行された操作に依存しない汎用コミットでのエラー処理の方法を必要する必要がある場合は、各トランザクションはコミットに失敗した場合にチェックされている ID 割り当てでした。</span><span class="sxs-lookup"><span data-stu-id="07058-143">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="07058-144">トランザクションの状態を追跡するために使用するデータベースにテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="07058-144">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="07058-145">各トランザクションの先頭にあるテーブルに行を挿入します。</span><span class="sxs-lookup"><span data-stu-id="07058-145">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="07058-146">コミット中に、接続に失敗した場合、データベース内の対応する行の存在を確認します。</span><span class="sxs-lookup"><span data-stu-id="07058-146">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="07058-147">コミットが成功した場合は、テーブルの増加を回避するために、対応する行を削除します。</span><span class="sxs-lookup"><span data-stu-id="07058-147">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="07058-148">検証に使用するコンテキストのように、接続がトランザクションのコミット中に失敗した場合、検証中にもう一度失敗する可能性が定義されて、実行戦略であることを確認します。</span><span class="sxs-lookup"><span data-stu-id="07058-148">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
