---
title: 接続の回復性-EF Core
author: rowanmiller
ms.date: 11/15/2016
ms.assetid: e079d4af-c455-4a14-8e15-a8471516d748
uid: core/miscellaneous/connection-resiliency
ms.openlocfilehash: 07646e6ead845c38537945a03367ac7f50784236
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414135"
---
# <a name="connection-resiliency"></a><span data-ttu-id="9f90e-102">接続の回復</span><span class="sxs-lookup"><span data-stu-id="9f90e-102">Connection Resiliency</span></span>

<span data-ttu-id="9f90e-103">接続の回復性は、失敗したデータベースコマンドを自動的に再試行します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-103">Connection resiliency automatically retries failed database commands.</span></span> <span data-ttu-id="9f90e-104">この機能は、"実行戦略" を指定することによって任意のデータベースで使用できます。これには、エラーを検出してコマンドを再試行するために必要なロジックがカプセル化されています。</span><span class="sxs-lookup"><span data-stu-id="9f90e-104">The feature can be used with any database by supplying an "execution strategy", which encapsulates the logic necessary to detect failures and retry commands.</span></span> <span data-ttu-id="9f90e-105">EF Core プロバイダーは、特定のデータベースエラー条件と最適な再試行ポリシーに合わせて、実行戦略を提供できます。</span><span class="sxs-lookup"><span data-stu-id="9f90e-105">EF Core providers can supply execution strategies tailored to their specific database failure conditions and optimal retry policies.</span></span>

<span data-ttu-id="9f90e-106">例として、SQL Server プロバイダーには、SQL Server (SQL Azure を含む) に特化した実行方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="9f90e-106">As an example, the SQL Server provider includes an execution strategy that is specifically tailored to SQL Server (including SQL Azure).</span></span> <span data-ttu-id="9f90e-107">再試行できる例外の種類を認識しており、最大再試行回数、再試行間隔などの既定値があります。</span><span class="sxs-lookup"><span data-stu-id="9f90e-107">It is aware of the exception types that can be retried and has sensible defaults for maximum retries, delay between retries, etc.</span></span>

<span data-ttu-id="9f90e-108">実行方法は、コンテキストのオプションを構成するときに指定します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-108">An execution strategy is specified when configuring the options for your context.</span></span> <span data-ttu-id="9f90e-109">これは通常、派生コンテキストの `OnConfiguring` メソッドにあります。</span><span class="sxs-lookup"><span data-stu-id="9f90e-109">This is typically in the `OnConfiguring` method of your derived context:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#OnConfiguring)]

<span data-ttu-id="9f90e-110">または、`Startup.cs` で ASP.NET Core アプリケーションの場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9f90e-110">or in `Startup.cs` for an ASP.NET Core application:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<PicnicContext>(
        options => options.UseSqlServer(
            "<connection string>",
            providerOptions => providerOptions.EnableRetryOnFailure()));
}
```

## <a name="custom-execution-strategy"></a><span data-ttu-id="9f90e-111">カスタム実行戦略</span><span class="sxs-lookup"><span data-stu-id="9f90e-111">Custom execution strategy</span></span>

<span data-ttu-id="9f90e-112">既定値を変更する必要がある場合は、独自のカスタム実行戦略を登録するメカニズムがあります。</span><span class="sxs-lookup"><span data-stu-id="9f90e-112">There is a mechanism to register a custom execution strategy of your own if you wish to change any of the defaults.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseMyProvider(
            "<connection string>",
            options => options.ExecutionStrategy(...));
}
```

## <a name="execution-strategies-and-transactions"></a><span data-ttu-id="9f90e-113">実行戦略とトランザクション</span><span class="sxs-lookup"><span data-stu-id="9f90e-113">Execution strategies and transactions</span></span>

<span data-ttu-id="9f90e-114">エラーに対して自動的に再試行を行う実行方法は、失敗した再試行ブロックで各操作を再生できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="9f90e-114">An execution strategy that automatically retries on failures needs to be able to play back each operation in a retry block that fails.</span></span> <span data-ttu-id="9f90e-115">再試行が有効になっている場合、EF Core 経由で実行する各操作が独自の再試行可能な操作になります。</span><span class="sxs-lookup"><span data-stu-id="9f90e-115">When retries are enabled, each operation you perform via EF Core becomes its own retriable operation.</span></span> <span data-ttu-id="9f90e-116">つまり、一時的な障害が発生した場合、各クエリと `SaveChanges()` の各呼び出しが1つの単位として再試行されます。</span><span class="sxs-lookup"><span data-stu-id="9f90e-116">That is, each query and each call to `SaveChanges()` will be retried as a unit if a transient failure occurs.</span></span>

<span data-ttu-id="9f90e-117">ただし、コードがを使用してトランザクションを開始する場合 `BeginTransaction()`、1つの単位として処理する必要がある独自の操作グループを定義する場合、トランザクション内のすべてのものを再生する必要がある場合は、エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-117">However, if your code initiates a transaction using `BeginTransaction()` you are defining your own group of operations that need to be treated as a unit, and everything inside the transaction would need to be played back shall a failure occur.</span></span> <span data-ttu-id="9f90e-118">実行戦略を使用しているときに、次のような例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-118">You will receive an exception like the following if you attempt to do this when using an execution strategy:</span></span>

> <span data-ttu-id="9f90e-119">InvalidOperationException: 構成された実行方法 ' SqlServerRetryingExecutionStrategy ' は、ユーザーが開始したトランザクションをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="9f90e-119">InvalidOperationException: The configured execution strategy 'SqlServerRetryingExecutionStrategy' does not support user initiated transactions.</span></span> <span data-ttu-id="9f90e-120">'DbContext.Database.CreateExecutionStrategy()' から返された実行戦略を使用して、再試行可能なユニットとしてトランザクション内のすべての操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-120">Use the execution strategy returned by 'DbContext.Database.CreateExecutionStrategy()' to execute all the operations in the transaction as a retriable unit.</span></span>

<span data-ttu-id="9f90e-121">これを解決するには、実行する必要があるすべてを表すデリゲートを使用して、手動で実行戦略を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-121">The solution is to manually invoke the execution strategy with a delegate representing everything that needs to be executed.</span></span> <span data-ttu-id="9f90e-122">一時的な障害が発生した場合、実行戦略は、デリゲートをもう一度呼び出します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-122">If a transient failure occurs, the execution strategy will invoke the delegate again.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#ManualTransaction)]

<span data-ttu-id="9f90e-123">この方法は、アンビエントトランザクションと共に使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="9f90e-123">This approach can also be used with ambient transactions.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#AmbientTransaction)]

## <a name="transaction-commit-failure-and-the-idempotency-issue"></a><span data-ttu-id="9f90e-124">トランザクションのコミットエラーとべき等の問題</span><span class="sxs-lookup"><span data-stu-id="9f90e-124">Transaction commit failure and the idempotency issue</span></span>

<span data-ttu-id="9f90e-125">一般に、接続エラーが発生した場合は、現在のトランザクションがロールバックされます。</span><span class="sxs-lookup"><span data-stu-id="9f90e-125">In general, when there is a connection failure the current transaction is rolled back.</span></span> <span data-ttu-id="9f90e-126">ただし、トランザクションのコミット中に接続が切断された場合、トランザクションの結果の状態は不明になります。</span><span class="sxs-lookup"><span data-stu-id="9f90e-126">However, if the connection is dropped while the transaction is being committed the resulting state of the transaction is unknown.</span></span> 

<span data-ttu-id="9f90e-127">既定では、トランザクションがロールバックされた場合と同様に操作が再試行されますが、その場合、新しいデータベースの状態に互換性がない場合、または自動生成されたキー値を使用して新しい行を挿入する場合など、操作が特定の状態に依存してい**ない場合は**、例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-127">By default, the execution strategy will retry the operation as if the transaction was rolled back, but if it's not the case this will result in an exception if the new database state is incompatible or could lead to **data corruption** if the operation does not rely on a particular state, for example when inserting a new row with auto-generated key values.</span></span>

<span data-ttu-id="9f90e-128">これに対処するには、いくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="9f90e-128">There are several ways to deal with this.</span></span>

### <a name="option-1---do-almost-nothing"></a><span data-ttu-id="9f90e-129">オプション 1-(ほぼ) 何もしない</span><span class="sxs-lookup"><span data-stu-id="9f90e-129">Option 1 - Do (almost) nothing</span></span>

<span data-ttu-id="9f90e-130">トランザクションのコミット中に接続エラーが発生する可能性は低いため、この状態が実際に発生した場合にアプリケーションが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="9f90e-130">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>

<span data-ttu-id="9f90e-131">ただし、重複する行を追加するのではなく、例外がスローされるようにするために、ストアによって生成されたキーを使用しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9f90e-131">However, you need to avoid using store-generated keys in order to ensure that an exception is thrown instead of adding a duplicate row.</span></span> <span data-ttu-id="9f90e-132">クライアントによって生成された GUID 値またはクライアント側の値ジェネレーターを使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="9f90e-132">Consider using a client-generated GUID value or a client-side value generator.</span></span>

### <a name="option-2---rebuild-application-state"></a><span data-ttu-id="9f90e-133">オプション 2-アプリケーションの状態を再構築する</span><span class="sxs-lookup"><span data-stu-id="9f90e-133">Option 2 - Rebuild application state</span></span>

1. <span data-ttu-id="9f90e-134">現在の `DbContext`を破棄します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-134">Discard the current `DbContext`.</span></span>
2. <span data-ttu-id="9f90e-135">新しい `DbContext` を作成し、データベースからアプリケーションの状態を復元します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-135">Create a new `DbContext` and restore the state of your application from the database.</span></span>
3. <span data-ttu-id="9f90e-136">最後の操作が正常に完了していない可能性があることをユーザーに通知します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-136">Inform the user that the last operation might not have been completed successfully.</span></span>

### <a name="option-3---add-state-verification"></a><span data-ttu-id="9f90e-137">オプション 3-状態検証の追加</span><span class="sxs-lookup"><span data-stu-id="9f90e-137">Option 3 - Add state verification</span></span>

<span data-ttu-id="9f90e-138">データベースの状態を変更するほとんどの操作では、成功したかどうかを確認するコードを追加できます。</span><span class="sxs-lookup"><span data-stu-id="9f90e-138">For most of the operations that change the database state it is possible to add code that checks whether it succeeded.</span></span> <span data-ttu-id="9f90e-139">EF には、これを簡単に `IExecutionStrategy.ExecuteInTransaction`できる拡張メソッドが用意されています。</span><span class="sxs-lookup"><span data-stu-id="9f90e-139">EF provides an extension method to make this easier - `IExecutionStrategy.ExecuteInTransaction`.</span></span>

<span data-ttu-id="9f90e-140">このメソッドは、トランザクションを開始してコミットします。また、トランザクションのコミット中に一時的なエラーが発生したときに呼び出される `verifySucceeded` パラメーターの関数も受け入れます。</span><span class="sxs-lookup"><span data-stu-id="9f90e-140">This method begins and commits a transaction and also accepts a function in the `verifySucceeded` parameter that is invoked when a transient error occurs during the transaction commit.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Verification)]

> [!NOTE]
> <span data-ttu-id="9f90e-141">ここでは、`Unchanged` が成功した場合に `Blog` エンティティの状態が `SaveChanges` に変更されないように、`acceptAllChangesOnSuccess` を `false` に設定して `SaveChanges` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="9f90e-141">Here `SaveChanges` is invoked with `acceptAllChangesOnSuccess` set to `false` to avoid changing the state of the `Blog` entity to `Unchanged` if `SaveChanges` succeeds.</span></span> <span data-ttu-id="9f90e-142">これにより、コミットが失敗し、トランザクションがロールバックされた場合に、が同じ操作を再試行することができます。</span><span class="sxs-lookup"><span data-stu-id="9f90e-142">This allows to retry the same operation if the commit fails and the transaction is rolled back.</span></span>

### <a name="option-4---manually-track-the-transaction"></a><span data-ttu-id="9f90e-143">オプション 4-トランザクションを手動で追跡する</span><span class="sxs-lookup"><span data-stu-id="9f90e-143">Option 4 - Manually track the transaction</span></span>

<span data-ttu-id="9f90e-144">ストアによって生成されたキーを使用する必要がある場合、または実行された操作に依存しないコミットエラーを処理する汎用的な方法が必要な場合は、コミットが失敗したときにチェックされる ID を各トランザクションに割り当てることができます。</span><span class="sxs-lookup"><span data-stu-id="9f90e-144">If you need to use store-generated keys or need a generic way of handling commit failures that doesn't depend on the operation performed each transaction could be assigned an ID that is checked when the commit fails.</span></span>

1. <span data-ttu-id="9f90e-145">トランザクションの状態を追跡するために使用するデータベースにテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-145">Add a table to the database used to track the status of the transactions.</span></span>
2. <span data-ttu-id="9f90e-146">各トランザクションの開始時に、テーブルに行を挿入します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-146">Insert a row into the table at the beginning of each transaction.</span></span>
3. <span data-ttu-id="9f90e-147">コミット中に接続が失敗した場合は、データベース内の対応する行が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-147">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>
4. <span data-ttu-id="9f90e-148">コミットが成功した場合は、対応する行を削除して、テーブルの増加を回避します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-148">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/ConnectionResiliency/Program.cs#Tracking)]

> [!NOTE]
> <span data-ttu-id="9f90e-149">検証に使用されるコンテキストに、トランザクションのコミット中に失敗した場合の検証中に再び失敗する可能性があるという実行方法が定義されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9f90e-149">Make sure that the context used for the verification has an execution strategy defined as the connection is likely to fail again during verification if it failed during transaction commit.</span></span>
