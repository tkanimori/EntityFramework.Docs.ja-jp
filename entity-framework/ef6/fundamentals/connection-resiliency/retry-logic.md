---
title: 接続の回復性と再試行ロジック - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
caps.latest.revision: 3
ms.openlocfilehash: 4c6e296ecb8b43468408d359f44fd1d1a6edf864
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "39122572"
---
# <a name="connection-resiliency-and-retry-logic"></a><span data-ttu-id="5d8f9-102">接続の回復性と再試行ロジック</span><span class="sxs-lookup"><span data-stu-id="5d8f9-102">Connection resiliency and retry logic</span></span>
> [!NOTE]
> <span data-ttu-id="5d8f9-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="5d8f9-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="5d8f9-105">データベース サーバーに接続するアプリケーションは、バックエンドの障害やネットワークが不安定になるのため、接続の区切りに脆弱性のある常にされました。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-105">Applications connecting to a database server have always been vulnerable to connection breaks due to back-end failures and network instability.</span></span> <span data-ttu-id="5d8f9-106">ただし、専用のデータベース サーバーに対して作業を行う、LAN ベースの環境でこれらのエラーはまれをこれらのエラーを処理する追加のロジックは多くの場合、必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-106">However, in a LAN based environment working against dedicated database servers these errors are rare enough that extra logic to handle those failures is not often required.</span></span> <span data-ttu-id="5d8f9-107">クラウドの登場により、多くの接続の中断が発生する場合、信頼性の低いネットワーク経由で Windows Azure SQL Database などのデータベース サーバーと接続ベース。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-107">With the rise of cloud based database servers such as Windows Azure SQL Database and connections over less reliable networks it is now more common for connection breaks to occur.</span></span> <span data-ttu-id="5d8f9-108">クラウド サービスや断続的なタイムアウトおよびその他の一時的なエラーの原因とネットワークが不安定になる接続のスロットル調整などの公平性を保つためにデータベース使用の防テクニックが考えられます。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-108">This could be due to defensive techniques that cloud databases use to ensure fairness of service, such as connection throttling, or to instability in the network causing intermittent timeouts and other transient errors.</span></span>  

<span data-ttu-id="5d8f9-109">接続の回復性は、これらの接続の中断が原因で失敗する任意のコマンドを自動的に再試行する EF の機能を指します。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-109">Connection Resiliency refers to the ability for EF to automatically retry any commands that fail due to these connection breaks.</span></span>  

## <a name="execution-strategies"></a><span data-ttu-id="5d8f9-110">実行戦略</span><span class="sxs-lookup"><span data-stu-id="5d8f9-110">Execution Strategies</span></span>  

<span data-ttu-id="5d8f9-111">接続の再試行はの IDbExecutionStrategy インターフェイスの実装によって処理されます。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-111">Connection retry is taken care of by an implementation of the IDbExecutionStrategy interface.</span></span> <span data-ttu-id="5d8f9-112">IDbExecutionStrategy の実装は、操作を受け入れると、例外が発生する場合、再試行が適切なかどうかを判断してがある場合の再試行の責任になります。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-112">Implementations of the IDbExecutionStrategy will be responsible for accepting an operation and, if an exception occurs, determining if a retry is appropriate and retrying if it is.</span></span> <span data-ttu-id="5d8f9-113">これには EF に付属する 4 つの実行方法があります。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-113">There are four execution strategies that ship with EF:</span></span>  

1. <span data-ttu-id="5d8f9-114">**DefaultExecutionStrategy**: この実行方法は、すべての操作を再試行しません、sql server 以外のデータベースの既定値になります。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-114">**DefaultExecutionStrategy**: this execution strategy does not retry any operations, it is the default for databases other than sql server.</span></span>  
2. <span data-ttu-id="5d8f9-115">**DefaultSqlExecutionStrategy**: これは既定で使用される内部実行戦略です。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-115">**DefaultSqlExecutionStrategy**: this is an internal execution strategy that is used by default.</span></span> <span data-ttu-id="5d8f9-116">この方法はまったく再試行されず、ただし、接続の回復を有効にすることがユーザーに通知する一時的な可能性がある例外がラップされます。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-116">This strategy does not retry at all, however, it will wrap any exceptions that could be transient to inform users that they might want to enable connection resiliency.</span></span>  
3. <span data-ttu-id="5d8f9-117">**DbExecutionStrategy**: このクラスは基底クラスとして、独自のカスタムのものを含む、その他の実行戦略に適しています。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-117">**DbExecutionStrategy**: this class is suitable as a base class for other execution strategies, including your own custom ones.</span></span> <span data-ttu-id="5d8f9-118">最初の再試行が行われる場所 0 遅延と遅延が増加指数関数的に最大再試行回数がヒットするまで、指数再試行ポリシーを実装します。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-118">It implements an exponential retry policy, where the initial retry happens with zero delay and the delay increases exponentially until the maximum retry count is hit.</span></span> <span data-ttu-id="5d8f9-119">このクラスには、どの例外を再試行するかを制御するための派生実行戦略を実装できる抽象 ShouldRetryOn メソッドがあります。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-119">This class has an abstract ShouldRetryOn method that can be implemented in derived execution strategies to control which exceptions should be retried.</span></span>  
4. <span data-ttu-id="5d8f9-120">**SqlAzureExecutionStrategy**: この実行戦略が DbExecutionStrategy から継承し、Azure SQL Database を使用する場合に一時的な可能性があることがわかっている例外で再試行されます。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-120">**SqlAzureExecutionStrategy**: this execution strategy inherits from DbExecutionStrategy and will retry on exceptions that are known to be possibly transient when working with Azure SQL Database.</span></span>

> [!NOTE]
> <span data-ttu-id="5d8f9-121">実行戦略 2 と 4 では、EntityFramework.SqlServer アセンブリでは、EF に付属する Sql Server プロバイダーに含まれ、SQL Server で動作するように設計されます。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-121">Execution strategies 2 and 4 are included in the Sql Server provider that ships with EF, which is in the EntityFramework.SqlServer assembly and are designed to work with SQL Server.</span></span>  

## <a name="enabling-an-execution-strategy"></a><span data-ttu-id="5d8f9-122">実行戦略を有効にします。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-122">Enabling an Execution Strategy</span></span>  

<span data-ttu-id="5d8f9-123">EF 実行戦略を使用してに指示する最も簡単な方法は、の SetExecutionStrategy メソッドでは、 [DbConfiguration](~/ef6/fundamentals/configuring/code-based.md)クラス。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-123">The easiest way to tell EF to use an execution strategy is with the SetExecutionStrategy method of the [DbConfiguration](~/ef6/fundamentals/configuring/code-based.md) class:</span></span>  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

<span data-ttu-id="5d8f9-124">このコードは、SQL Server に接続するときに、SqlAzureExecutionStrategy を使用する EF に指示します。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-124">This code tells EF to use the SqlAzureExecutionStrategy when connecting to SQL Server.</span></span>  

## <a name="configuring-the-execution-strategy"></a><span data-ttu-id="5d8f9-125">実行戦略を構成します。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-125">Configuring the Execution Strategy</span></span>  

<span data-ttu-id="5d8f9-126">SqlAzureExecutionStrategy のコンス トラクターは、MaxRetryCount と MaxDelay の 2 つのパラメーターを受け取ることができます。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-126">The constructor of SqlAzureExecutionStrategy can accept two parameters, MaxRetryCount and MaxDelay.</span></span> <span data-ttu-id="5d8f9-127">MaxRetry 数は、戦略が再試行する回数の最大数です。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-127">MaxRetry count is the maximum number of times that the strategy will retry.</span></span> <span data-ttu-id="5d8f9-128">MaxDelay は、実行戦略を使用する再試行間の最大遅延を表す TimeSpan です。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-128">The MaxDelay is a TimeSpan representing the maximum delay between retries that the execution strategy will use.</span></span>  

<span data-ttu-id="5d8f9-129">再試行の最大数を 1 および 30 秒の遅延の上限に設定するには、execue、次の場合。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-129">To set the maximum number of retries to 1 and the maximum delay to 30 seconds you would execue the following:</span></span>  

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

<span data-ttu-id="5d8f9-130">一時的なエラーが発生した場合、これはか最大再試行制限まで、各再試行間になった遅延は、最初の時間を超えたまたは時間の合計最大遅延をヒットするすぐに、SqlAzureExecutionStrategy を再試行します。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-130">The SqlAzureExecutionStrategy will retry instantly the first time a transient failure occurs, but will delay longer between each retry until either the max retry limit is exceeded or the total time hits the max delay.</span></span>  

<span data-ttu-id="5d8f9-131">実行戦略は tansient では、通常、例外の数に制限を再試行してのみ、他のエラーと解決するのにはエラーが一時的でないか、時間がかかりすぎる場合 RetryLimitExceeded 例外をキャッチするを処理する必要があります。自体。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-131">The execution strategies will only retry a limited number of exceptions that are usually tansient, you will still need to handle other errors as well as catching the RetryLimitExceeded exception for the case where an error is not transient or takes too long to resolve itself.</span></span>  

## <a name="limitations"></a><span data-ttu-id="5d8f9-132">制限事項</span><span class="sxs-lookup"><span data-stu-id="5d8f9-132">Limitations</span></span>  

<span data-ttu-id="5d8f9-133">再試行実行戦略を使用する場合は、いくつかの既知の制限事項があります。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-133">There are some known of limitations when using a retrying execution strategy:</span></span>  

### <a name="streaming-queries-are-not-supported"></a><span data-ttu-id="5d8f9-134">ストリーミング クエリがサポートされていません</span><span class="sxs-lookup"><span data-stu-id="5d8f9-134">Streaming queries are not supported</span></span>  

<span data-ttu-id="5d8f9-135">既定では、EF6 とそれ以降のバージョンはそれらをストリーミングするのではなく、クエリの結果が可能ではバッファーです。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-135">By default, EF6 and later version will buffer query results rather than streaming them.</span></span> <span data-ttu-id="5d8f9-136">結果ストリームを用意する場合 AsStreaming メソッドを使用してストリーミングするエンティティのクエリに LINQ を変更することができます。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-136">If you want to have results streamed you can use the AsStreaming method to change a LINQ to Entities query to streaming.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

<span data-ttu-id="5d8f9-137">再試行実行戦略を登録すると、ストリーミングはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-137">Streaming is not supported when a retrying execution strategy is registered.</span></span> <span data-ttu-id="5d8f9-138">返される結果を接続が途中削除でしたので、この制限が存在します。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-138">This limitation exists because the connection could drop part way through the results being returned.</span></span> <span data-ttu-id="5d8f9-139">この場合、EF がクエリ全体を再実行する必要がありますが既に返された結果を知ることの確実な方法がありません (データが変更されている最初のクエリが送信された、結果が異なる順序で返される可能性があります、結果は、一意識別子がないため、など。)。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-139">When this occurs, EF needs to re-run the entire query but has no reliable way of knowing which results have already been returned (data may have changed since the initial query was sent, results may come back in a different order, results may not have a unique identifier, etc.).</span></span>  

### <a name="user-initiated-transactions-not-supported"></a><span data-ttu-id="5d8f9-140">ユーザーがサポートされていないトランザクションを開始</span><span class="sxs-lookup"><span data-stu-id="5d8f9-140">User initiated transactions not supported</span></span>  

<span data-ttu-id="5d8f9-141">再試行回数になる実行戦略を構成しているときにトランザクションの使用に関するいくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-141">When you have configured an execution strategy that results in retries, there are some limitations around the use of transactions.</span></span>  

#### <a name="whats-supported-efs-default-transaction-behavior"></a><span data-ttu-id="5d8f9-142">サポートされている内容: EF の既定のトランザクションの動作</span><span class="sxs-lookup"><span data-stu-id="5d8f9-142">What's Supported: EF's default transaction behavior</span></span>  

<span data-ttu-id="5d8f9-143">既定では、EF は、トランザクション内ですべてのデータベース更新を実行します。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-143">By default, EF will perform any database updates within a transaction.</span></span> <span data-ttu-id="5d8f9-144">これを有効にすることが何もする必要はありませんが、EF は、自動的にこれは常に。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-144">You don’t need to do anything to enable this, EF always does this automatically.</span></span>  

<span data-ttu-id="5d8f9-145">たとえば、次のコードで SaveChanges が自動的に実行のトランザクション内で。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-145">For example, in the following code SaveChanges is automatically performed within a transaction.</span></span> <span data-ttu-id="5d8f9-146">SaveChanges 後、トランザクションをロールバックは、新しいサイトのいずれかの挿入と変更がデータベースに適用せずに失敗する場合。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-146">If SaveChanges were to fail after inserting one of the new Site’s then the transaction would be rolled back and no changes applied to the database.</span></span> <span data-ttu-id="5d8f9-147">コンテキストのまま変更を適用する再試行をもう一度呼び出す SaveChanges を許可する状態にします。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-147">The context is also left in a state that allows SaveChanges to be called again to retry applying the changes.</span></span>  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

#### <a name="whats-not-supported-user-initiated-transactions"></a><span data-ttu-id="5d8f9-148">どのような操作はサポートされていませんユーザー トランザクションを開始する。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-148">What’s not supported: User initiated transactions</span></span>  

<span data-ttu-id="5d8f9-149">再試行実行戦略を使用しない場合は、単一のトランザクションで複数の操作をラップできます。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-149">When not using a retrying execution strategy you can wrap multiple operations in a single transaction.</span></span> <span data-ttu-id="5d8f9-150">たとえば、次のコードでは、単一のトランザクションで 2 つの SaveChanges 呼び出しをラップします。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-150">For example, the following code wraps two SaveChanges calls in a single transaction.</span></span> <span data-ttu-id="5d8f9-151">いずれかの操作の一部が失敗した場合、どの変更が適用されます。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-151">If any part of either operation fails then none of the changes are applied.</span></span>  

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

<span data-ttu-id="5d8f9-152">EF が認識し、それ以前の操作を再試行する方法がないために、再試行実行戦略を使用する場合、この操作はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-152">This is not supported when using a retrying execution strategy because EF isn’t aware of any previous operations and how to retry them.</span></span> <span data-ttu-id="5d8f9-153">たとえば、2 つ目の SaveChanges が失敗した場合、EF されなくが最初の SaveChanges 呼び出しを再試行する必要な情報」とあります。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-153">For example, if the second SaveChanges failed then EF no longer has the required information to retry the first SaveChanges call.</span></span>  

#### <a name="possible-workarounds"></a><span data-ttu-id="5d8f9-154">可能な回避策</span><span class="sxs-lookup"><span data-stu-id="5d8f9-154">Possible workarounds</span></span>  

##### <a name="suspend-execution-strategy"></a><span data-ttu-id="5d8f9-155">実行戦略を中断します。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-155">Suspend Execution Strategy</span></span>  

<span data-ttu-id="5d8f9-156">1 つの考えられる回避策は、ユーザーを使用する必要があるコードの一部の再試行実行戦略を中断するトランザクションを開始します。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-156">One possible workaround is to suspend the retrying execution strategy for the piece of code that needs to use a user initiated transaction.</span></span> <span data-ttu-id="5d8f9-157">これを行う最も簡単な方法では、コードに SuspendExecutionStrategy フラグは、構成クラスをベースし、フラグが設定されている場合、既定の (非 retying) 実行戦略を返す実行戦略のラムダの変更を追加します。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-157">The easiest way to do this is to add a SuspendExecutionStrategy flag to your code based configuration class and change the execution strategy lambda to return the default (non-retying) execution strategy when the flag is set.</span></span>  

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
                return (bool?)CallContext.LogicalGetData("SuspendExecutionStrategy")  false;
            }
            set
            {
                CallContext.LogicalSetData("SuspendExecutionStrategy", value);
            }
        }
    }
}
```  

<span data-ttu-id="5d8f9-158">フラグの値を格納する CallContext 使用していることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-158">Note that we are using CallContext to store the flag value.</span></span> <span data-ttu-id="5d8f9-159">これにより、スレッド ローカル ストレージと同様の機能を提供しますが、非同期クエリを含む - 非同期コードを使用して Entity Framework で保存しても安全です。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-159">This provides similar functionality to thread local storage but is safe to use with asynchronous code - including async query and save with Entity Framework.</span></span>  

<span data-ttu-id="5d8f9-160">ユーザーが開始したトランザクションを使用するコードのセクションの実行戦略を中断しましたできますようになりました。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-160">We can now suspend the execution strategy for the section of code that uses a user initiated transaction.</span></span>  

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

##### <a name="manually-call-execution-strategy"></a><span data-ttu-id="5d8f9-161">手動で実行戦略を呼び出す</span><span class="sxs-lookup"><span data-stu-id="5d8f9-161">Manually Call Execution Strategy</span></span>  

<span data-ttu-id="5d8f9-162">手動で実行戦略を使用し、セット全体は、実行ロジックのいずれかの操作が失敗した場合に再試行すべてできるようにする別の方法です。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-162">Another option is to manually use the execution strategy and give it the entire set of logic to be run, so that it can retry everything if one of the operations fails.</span></span> <span data-ttu-id="5d8f9-163">技法を使用して、実行戦略を中断する必要がありますの上に表示されるため、再試行可能なコード ブロック内で使用される任意のコンテキストは再試行しようとはしないでください。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-163">We still need to suspend the execution strategy - using the technique shown above - so that any contexts used inside the retryable code block do not attempt to retry.</span></span>  

<span data-ttu-id="5d8f9-164">再試行するコード ブロック内でコンテキストを作成することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-164">Note that any contexts should be constructed within the code block to be retried.</span></span> <span data-ttu-id="5d8f9-165">これにより、各再試行のクリーンな状態で開始します。</span><span class="sxs-lookup"><span data-stu-id="5d8f9-165">This ensures that we are starting with a clean state for each retry.</span></span>  

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
