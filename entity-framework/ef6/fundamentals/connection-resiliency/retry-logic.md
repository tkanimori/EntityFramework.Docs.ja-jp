---
title: 接続の回復性と再試行ロジック-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
ms.openlocfilehash: a01216c3399ca4a04943563435eacd0047337a5f
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306572"
---
# <a name="connection-resiliency-and-retry-logic"></a><span data-ttu-id="1b7b8-102">接続の回復性と再試行ロジック</span><span class="sxs-lookup"><span data-stu-id="1b7b8-102">Connection resiliency and retry logic</span></span>
> [!NOTE]
> <span data-ttu-id="1b7b8-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="1b7b8-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="1b7b8-105">データベースサーバーに接続するアプリケーションは、バックエンドの障害やネットワークの不安定性によって接続の中断に対して常に脆弱になります。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-105">Applications connecting to a database server have always been vulnerable to connection breaks due to back-end failures and network instability.</span></span> <span data-ttu-id="1b7b8-106">ただし、専用のデータベースサーバーに対して動作する LAN ベースの環境では、これらのエラーが発生することはほとんどありません。これらのエラーを処理するための追加のロジックが必要になることはほとんどありません。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-106">However, in a LAN based environment working against dedicated database servers these errors are rare enough that extra logic to handle those failures is not often required.</span></span> <span data-ttu-id="1b7b8-107">Windows Azure SQL Database などのクラウドベースのデータベースサーバーや信頼性の低いネットワークでの接続の増加により、接続の中断がより一般的になっています。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-107">With the rise of cloud based database servers such as Windows Azure SQL Database and connections over less reliable networks it is now more common for connection breaks to occur.</span></span> <span data-ttu-id="1b7b8-108">これは、クラウドデータベースがサービスの公平性を確保するために使用する防御手法 (接続の調整など)、または断続的なタイムアウトやその他の一時的なエラーの原因となるネットワークの不安定性を保証するために発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-108">This could be due to defensive techniques that cloud databases use to ensure fairness of service, such as connection throttling, or to instability in the network causing intermittent timeouts and other transient errors.</span></span>  

<span data-ttu-id="1b7b8-109">接続の回復性とは、EF が、これらの接続の中断によって失敗したコマンドを自動的に再試行する機能のことです。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-109">Connection Resiliency refers to the ability for EF to automatically retry any commands that fail due to these connection breaks.</span></span>  

## <a name="execution-strategies"></a><span data-ttu-id="1b7b8-110">実行方法</span><span class="sxs-lookup"><span data-stu-id="1b7b8-110">Execution Strategies</span></span>  

<span data-ttu-id="1b7b8-111">接続の再試行は、IDbExecutionStrategy インターフェイスの実装によって行われます。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-111">Connection retry is taken care of by an implementation of the IDbExecutionStrategy interface.</span></span> <span data-ttu-id="1b7b8-112">IDbExecutionStrategy の実装では、操作を受け入れる必要があります。例外が発生した場合は、再試行が適切かどうかを判断し、実行中であるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-112">Implementations of the IDbExecutionStrategy will be responsible for accepting an operation and, if an exception occurs, determining if a retry is appropriate and retrying if it is.</span></span> <span data-ttu-id="1b7b8-113">EF には、次の4つの実行戦略があります。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-113">There are four execution strategies that ship with EF:</span></span>  

1. <span data-ttu-id="1b7b8-114">**Defaultexecutionstrategy**: この実行方法では、操作は再試行されません。 sql server 以外のデータベースの既定値です。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-114">**DefaultExecutionStrategy**: this execution strategy does not retry any operations, it is the default for databases other than sql server.</span></span>  
2. <span data-ttu-id="1b7b8-115">**Defaultsqlexecutionstrategy**: これは、既定で使用される内部実行戦略です。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-115">**DefaultSqlExecutionStrategy**: this is an internal execution strategy that is used by default.</span></span> <span data-ttu-id="1b7b8-116">ただし、この方法を使用しても再試行されることはありませんが、一時的な例外をラップして、接続の回復性を有効にすることをユーザーに通知することができます。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-116">This strategy does not retry at all, however, it will wrap any exceptions that could be transient to inform users that they might want to enable connection resiliency.</span></span>  
3. <span data-ttu-id="1b7b8-117">**Dbexecutionstrategy**: このクラスは、他の実行戦略 (独自のカスタム属性を含む) の基底クラスとして適しています。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-117">**DbExecutionStrategy**: this class is suitable as a base class for other execution strategies, including your own custom ones.</span></span> <span data-ttu-id="1b7b8-118">指数的な再試行ポリシーを実装します。最初の再試行はゼロの遅延が発生し、遅延は最大再試行回数に達するまで指数関数的に増加します。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-118">It implements an exponential retry policy, where the initial retry happens with zero delay and the delay increases exponentially until the maximum retry count is hit.</span></span> <span data-ttu-id="1b7b8-119">このクラスには、派生された実行方法で実装できる抽象型のメソッドがあり、どの例外を再試行するかを制御できます。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-119">This class has an abstract ShouldRetryOn method that can be implemented in derived execution strategies to control which exceptions should be retried.</span></span>  
4. <span data-ttu-id="1b7b8-120">**SqlAzureExecutionStrategy**: この実行戦略は DbExecutionStrategy から継承され、Azure SQL Database を操作するときに一時的である可能性がある例外に対して再試行します。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-120">**SqlAzureExecutionStrategy**: this execution strategy inherits from DbExecutionStrategy and will retry on exceptions that are known to be possibly transient when working with Azure SQL Database.</span></span>

> [!NOTE]
> <span data-ttu-id="1b7b8-121">実行戦略2および4は、EF に付属している Sql Server プロバイダーに含まれています。これは、EntityFramework. SqlServer アセンブリにあり、SQL Server で動作するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-121">Execution strategies 2 and 4 are included in the Sql Server provider that ships with EF, which is in the EntityFramework.SqlServer assembly and are designed to work with SQL Server.</span></span>  

## <a name="enabling-an-execution-strategy"></a><span data-ttu-id="1b7b8-122">実行戦略を有効にする</span><span class="sxs-lookup"><span data-stu-id="1b7b8-122">Enabling an Execution Strategy</span></span>  

<span data-ttu-id="1b7b8-123">実行戦略を使用するように EF に指示する最も簡単な方法は、 [Dbconfiguration](~/ef6/fundamentals/configuring/code-based.md)クラスの setexecutionstrategy メソッドを使用することです。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-123">The easiest way to tell EF to use an execution strategy is with the SetExecutionStrategy method of the [DbConfiguration](~/ef6/fundamentals/configuring/code-based.md) class:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

<span data-ttu-id="1b7b8-124">このコードは、SQL Server に接続するときに SqlAzureExecutionStrategy を使用するように EF に指示します。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-124">This code tells EF to use the SqlAzureExecutionStrategy when connecting to SQL Server.</span></span>  

## <a name="configuring-the-execution-strategy"></a><span data-ttu-id="1b7b8-125">実行戦略の構成</span><span class="sxs-lookup"><span data-stu-id="1b7b8-125">Configuring the Execution Strategy</span></span>  

<span data-ttu-id="1b7b8-126">SqlAzureExecutionStrategy のコンストラクターは、MaxRetryCount と MaxDelay の2つのパラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-126">The constructor of SqlAzureExecutionStrategy can accept two parameters, MaxRetryCount and MaxDelay.</span></span> <span data-ttu-id="1b7b8-127">MaxRetry count は、戦略が再試行する最大回数です。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-127">MaxRetry count is the maximum number of times that the strategy will retry.</span></span> <span data-ttu-id="1b7b8-128">MaxDelay は、実行戦略が使用する再試行間の最大遅延時間を表す TimeSpan です。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-128">The MaxDelay is a TimeSpan representing the maximum delay between retries that the execution strategy will use.</span></span>  

<span data-ttu-id="1b7b8-129">再試行の最大回数を1に設定し、最大遅延を30秒に設定するには、次のように実行します。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-129">To set the maximum number of retries to 1 and the maximum delay to 30 seconds you would execute the following:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy(
            "System.Data.SqlClient",
            () => new SqlAzureExecutionStrategy(1, TimeSpan.FromSeconds(30)));
    }
}
```  

<span data-ttu-id="1b7b8-130">SqlAzureExecutionStrategy は、一時的なエラーが最初に発生したときに直ちに再試行されますが、最大再試行回数を超えた場合、または合計時間が最大遅延に達した場合は、再試行の間隔が長くなります。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-130">The SqlAzureExecutionStrategy will retry instantly the first time a transient failure occurs, but will delay longer between each retry until either the max retry limit is exceeded or the total time hits the max delay.</span></span>  

<span data-ttu-id="1b7b8-131">実行戦略は、限られた数の例外のみを再試行します。通常は一時的なもので、他のエラーも処理し、エラーが一時的でない場合や、解決に時間がかかりすぎる場合に RetryLimitExceeded 例外をキャッチする必要があります。自分自身.</span><span class="sxs-lookup"><span data-stu-id="1b7b8-131">The execution strategies will only retry a limited number of exceptions that are usually transient, you will still need to handle other errors as well as catching the RetryLimitExceeded exception for the case where an error is not transient or takes too long to resolve itself.</span></span>  

<span data-ttu-id="1b7b8-132">再試行の実行方法を使用する場合、いくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-132">There are some known of limitations when using a retrying execution strategy:</span></span>  

## <a name="streaming-queries-are-not-supported"></a><span data-ttu-id="1b7b8-133">ストリーミングクエリはサポートされていません</span><span class="sxs-lookup"><span data-stu-id="1b7b8-133">Streaming queries are not supported</span></span>  

<span data-ttu-id="1b7b8-134">既定では、EF6 以降のバージョンでは、クエリ結果をストリーミングするのではなく、バッファーに格納します。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-134">By default, EF6 and later version will buffer query results rather than streaming them.</span></span> <span data-ttu-id="1b7b8-135">結果をストリーミングする場合は、AsStreaming メソッドを使用して、LINQ to Entities クエリをストリーミングに変更できます。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-135">If you want to have results streamed you can use the AsStreaming method to change a LINQ to Entities query to streaming.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

<span data-ttu-id="1b7b8-136">再試行の実行戦略が登録されている場合、ストリーミングはサポートされません。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-136">Streaming is not supported when a retrying execution strategy is registered.</span></span> <span data-ttu-id="1b7b8-137">この制限が存在するのは、返される結果に対して接続が部分的に削除される可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-137">This limitation exists because the connection could drop part way through the results being returned.</span></span> <span data-ttu-id="1b7b8-138">この場合、EF はクエリ全体を再実行する必要がありますが、既に返された結果を知るための信頼できる方法はありません (最初のクエリが送信された後にデータが変更された可能性があり、結果が別の順序で返され、結果に一意の識別子がない可能性があります)など)。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-138">When this occurs, EF needs to re-run the entire query but has no reliable way of knowing which results have already been returned (data may have changed since the initial query was sent, results may come back in a different order, results may not have a unique identifier, etc.).</span></span>  

## <a name="user-initiated-transactions-are-not-supported"></a><span data-ttu-id="1b7b8-139">ユーザーが開始したトランザクションはサポートされていません</span><span class="sxs-lookup"><span data-stu-id="1b7b8-139">User initiated transactions are not supported</span></span>  

<span data-ttu-id="1b7b8-140">再試行を行う実行方法を構成した場合、トランザクションの使用に関していくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-140">When you have configured an execution strategy that results in retries, there are some limitations around the use of transactions.</span></span>  

<span data-ttu-id="1b7b8-141">既定では、EF はトランザクション内で任意のデータベースの更新を実行します。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-141">By default, EF will perform any database updates within a transaction.</span></span> <span data-ttu-id="1b7b8-142">これを有効にするために何もする必要はありません。 EF は常にこれを自動的に実行します。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-142">You don’t need to do anything to enable this, EF always does this automatically.</span></span>  

<span data-ttu-id="1b7b8-143">たとえば、次のコードでは、SaveChanges はトランザクション内で自動的に実行されます。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-143">For example, in the following code SaveChanges is automatically performed within a transaction.</span></span> <span data-ttu-id="1b7b8-144">新しいサイトのいずれかを挿入した後に SaveChanges が失敗した場合、トランザクションはロールバックされ、変更はデータベースに適用されません。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-144">If SaveChanges were to fail after inserting one of the new Site’s then the transaction would be rolled back and no changes applied to the database.</span></span> <span data-ttu-id="1b7b8-145">コンテキストも、変更の適用を再試行するために SaveChanges を再度呼び出すことができる状態のままです。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-145">The context is also left in a state that allows SaveChanges to be called again to retry applying the changes.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

<span data-ttu-id="1b7b8-146">再試行の実行戦略を使用しない場合は、複数の操作を1つのトランザクションにラップできます。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-146">When not using a retrying execution strategy you can wrap multiple operations in a single transaction.</span></span> <span data-ttu-id="1b7b8-147">たとえば、次のコードでは、1つのトランザクションで2つの SaveChanges 呼び出しをラップしています。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-147">For example, the following code wraps two SaveChanges calls in a single transaction.</span></span> <span data-ttu-id="1b7b8-148">いずれかの操作が失敗した場合、変更は適用されません。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-148">If any part of either operation fails then none of the changes are applied.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Site { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }
}
```  

<span data-ttu-id="1b7b8-149">これは、EF が以前の操作を認識しておらず、再試行する方法が認識されないため、再試行の実行方法を使用する場合はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-149">This is not supported when using a retrying execution strategy because EF isn’t aware of any previous operations and how to retry them.</span></span> <span data-ttu-id="1b7b8-150">たとえば、2番目の SaveChanges が失敗した場合、EF は最初の SaveChanges 呼び出しを再試行するために必要な情報を持っていません。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-150">For example, if the second SaveChanges failed then EF no longer has the required information to retry the first SaveChanges call.</span></span>  

### <a name="workaround-suspend-execution-strategy"></a><span data-ttu-id="1b7b8-151">回避策:実行戦略の中断</span><span class="sxs-lookup"><span data-stu-id="1b7b8-151">Workaround: Suspend Execution Strategy</span></span>  

<span data-ttu-id="1b7b8-152">考えられる回避策の1つは、ユーザーが開始したトランザクションを使用する必要があるコードについて、再試行の実行戦略を中断することです。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-152">One possible workaround is to suspend the retrying execution strategy for the piece of code that needs to use a user initiated transaction.</span></span> <span data-ttu-id="1b7b8-153">これを行う最も簡単な方法は、コードベースの構成クラスに SuspendExecutionStrategy フラグを追加して、フラグが設定されたときに既定の (retying ない) 実行方法を返すように実行戦略のラムダを変更することです。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-153">The easiest way to do this is to add a SuspendExecutionStrategy flag to your code based configuration class and change the execution strategy lambda to return the default (non-retying) execution strategy when the flag is set.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;
using System.Runtime.Remoting.Messaging;

namespace Demo
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            this.SetExecutionStrategy("System.Data.SqlClient", () => SuspendExecutionStrategy
              ? (IDbExecutionStrategy)new DefaultExecutionStrategy()
              : new SqlAzureExecutionStrategy());
        }

        public static bool SuspendExecutionStrategy
        {
            get
            {
                return (bool?)CallContext.LogicalGetData("SuspendExecutionStrategy") ?? false;
            }
            set
            {
                CallContext.LogicalSetData("SuspendExecutionStrategy", value);
            }
        }
    }
}
```  

<span data-ttu-id="1b7b8-154">CallContext を使用してフラグ値を格納していることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-154">Note that we are using CallContext to store the flag value.</span></span> <span data-ttu-id="1b7b8-155">これにより、スレッドローカルストレージと同様の機能が提供されますが、非同期クエリや Entity Framework による保存などの非同期コードと共に安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-155">This provides similar functionality to thread local storage but is safe to use with asynchronous code - including async query and save with Entity Framework.</span></span>  

<span data-ttu-id="1b7b8-156">ユーザーが開始したトランザクションを使用するコードのセクションの実行戦略を中断できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-156">We can now suspend the execution strategy for the section of code that uses a user initiated transaction.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    MyConfiguration.SuspendExecutionStrategy = true;

    using (var trn = db.Database.BeginTransaction())
    {
        db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
        db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
        db.SaveChanges();

        db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
        db.SaveChanges();

        trn.Commit();
    }

    MyConfiguration.SuspendExecutionStrategy = false;
}
```  

### <a name="workaround-manually-call-execution-strategy"></a><span data-ttu-id="1b7b8-157">回避策:実行戦略の手動呼び出し</span><span class="sxs-lookup"><span data-stu-id="1b7b8-157">Workaround: Manually Call Execution Strategy</span></span>  

<span data-ttu-id="1b7b8-158">もう1つの方法として、実行方法を手動で使用して、実行するロジックのセット全体を指定することもできます。これにより、いずれかの操作が失敗した場合にすべての操作を再試行できます。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-158">Another option is to manually use the execution strategy and give it the entire set of logic to be run, so that it can retry everything if one of the operations fails.</span></span> <span data-ttu-id="1b7b8-159">それでも、前に示した手法を使用して実行戦略を中断する必要があります。これにより、再試行可能なコードブロック内で使用されているすべてのコンテキストが再試行を試行しません。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-159">We still need to suspend the execution strategy - using the technique shown above - so that any contexts used inside the retryable code block do not attempt to retry.</span></span>  

<span data-ttu-id="1b7b8-160">再試行するコードブロック内にコンテキストを構築する必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-160">Note that any contexts should be constructed within the code block to be retried.</span></span> <span data-ttu-id="1b7b8-161">これにより、再試行のたびにクリーンな状態で開始されます。</span><span class="sxs-lookup"><span data-stu-id="1b7b8-161">This ensures that we are starting with a clean state for each retry.</span></span>  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

MyConfiguration.SuspendExecutionStrategy = true;

executionStrategy.Execute(
    () =>
    {
        using (var db = new BloggingContext())
        {
            using (var trn = db.Database.BeginTransaction())
            {
                db.Blogs.Add(new Blog { Url = "http://msdn.com/data/ef" });
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                db.SaveChanges();

                db.Blogs.Add(new Blog { Url = "http://twitter.com/efmagicunicorns" });
                db.SaveChanges();

                trn.Commit();
            }
        }
    });

MyConfiguration.SuspendExecutionStrategy = false;
```  
