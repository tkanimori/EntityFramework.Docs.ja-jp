---
title: トランザクションの操作-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0d0f1824-d781-4cb3-8fda-b7eaefced1cd
ms.openlocfilehash: 7030dc675993339f72c935f6b430cead85fecb7f
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416241"
---
# <a name="working-with-transactions"></a><span data-ttu-id="99418-102">トランザクションの操作</span><span class="sxs-lookup"><span data-stu-id="99418-102">Working with Transactions</span></span>
> [!NOTE]
> <span data-ttu-id="99418-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="99418-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="99418-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="99418-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="99418-105">このドキュメントでは、EF6 でトランザクションを使用する方法について説明します。これには、トランザクションを簡単に操作できるように EF5 以降に追加された機能も含まれます。</span><span class="sxs-lookup"><span data-stu-id="99418-105">This document will describe using transactions in EF6 including the enhancements we have added since EF5 to make working with transactions easy.</span></span>  

## <a name="what-ef-does-by-default"></a><span data-ttu-id="99418-106">既定での EF の動作</span><span class="sxs-lookup"><span data-stu-id="99418-106">What EF does by default</span></span>  

<span data-ttu-id="99418-107">Entity Framework のすべてのバージョンでは、 **SaveChanges ()** を実行してデータベースに対して挿入、更新、または削除を実行するたびに、フレームワークはその操作をトランザクションにラップします。</span><span class="sxs-lookup"><span data-stu-id="99418-107">In all versions of Entity Framework, whenever you execute **SaveChanges()** to insert, update or delete on the database the framework will wrap that operation in a transaction.</span></span> <span data-ttu-id="99418-108">このトランザクションは、操作を実行して完了するまでに十分な時間だけ継続します。</span><span class="sxs-lookup"><span data-stu-id="99418-108">This transaction lasts only long enough to execute the operation and then completes.</span></span> <span data-ttu-id="99418-109">別の操作を実行すると、新しいトランザクションが開始されます。</span><span class="sxs-lookup"><span data-stu-id="99418-109">When you execute another such operation a new transaction is started.</span></span>  

<span data-ttu-id="99418-110">EF6 では、既定では、コマンドがまだ存在していない場合は、そのコマンドがトランザクションにラップされ**ます。**</span><span class="sxs-lookup"><span data-stu-id="99418-110">Starting with EF6 **Database.ExecuteSqlCommand()** by default will wrap the command in a transaction if one was not already present.</span></span> <span data-ttu-id="99418-111">このメソッドのオーバーロードを使用すると、必要に応じてこの動作をオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="99418-111">There are overloads of this method that allow you to override this behavior if you wish.</span></span> <span data-ttu-id="99418-112">また、EF6 **()** などの api によってモデルに含まれるストアドプロシージャの実行も、同じように実行されます (ただし、既定の動作はオーバーライドされません)。</span><span class="sxs-lookup"><span data-stu-id="99418-112">Also in EF6 execution of stored procedures included in the model through APIs such as **ObjectContext.ExecuteFunction()** does the same (except that the default behavior cannot at the moment be overridden).</span></span>  

<span data-ttu-id="99418-113">どちらの場合も、トランザクションの分離レベルは、データベースプロバイダーが既定の設定を考慮している任意の分離レベルです。</span><span class="sxs-lookup"><span data-stu-id="99418-113">In either case, the isolation level of the transaction is whatever isolation level the database provider considers its default setting.</span></span> <span data-ttu-id="99418-114">既定では、SQL Server では READ COMMITTED です。</span><span class="sxs-lookup"><span data-stu-id="99418-114">By default, for instance, on SQL Server this is READ COMMITTED.</span></span>  

<span data-ttu-id="99418-115">Entity Framework は、トランザクション内のクエリをラップしません。</span><span class="sxs-lookup"><span data-stu-id="99418-115">Entity Framework does not wrap queries in a transaction.</span></span>  

<span data-ttu-id="99418-116">この既定の機能は、多数のユーザーに適しているので、EF6 では何もする必要はありません。コードを記述するのは常に同じです。</span><span class="sxs-lookup"><span data-stu-id="99418-116">This default functionality is suitable for a lot of users and if so there is no need to do anything different in EF6; just write the code as you always did.</span></span>  

<span data-ttu-id="99418-117">ただし、ユーザーによっては、トランザクションをより詳細に制御する必要があります。これについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="99418-117">However some users require greater control over their transactions – this is covered in the following sections.</span></span>  

## <a name="how-the-apis-work"></a><span data-ttu-id="99418-118">Api のしくみ</span><span class="sxs-lookup"><span data-stu-id="99418-118">How the APIs work</span></span>  

<span data-ttu-id="99418-119">EF6 より前では、データベース接続自体を開いたときに insisted を Entity Framework しています (既に開いている接続が渡された場合、例外がスローされました)。</span><span class="sxs-lookup"><span data-stu-id="99418-119">Prior to EF6 Entity Framework insisted on opening the database connection itself (it threw an exception if it was passed a connection that was already open).</span></span> <span data-ttu-id="99418-120">トランザクションは開いている接続でのみ開始できるため、ユーザーが複数の操作を1つのトランザクションにラップする唯一の方法は、 [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx)を使用するか、**または、返さ**れた**Entityconnection**オブジェクトに対して**open ()** と**BeginTransaction ()** の呼び出しを直接開始することです。</span><span class="sxs-lookup"><span data-stu-id="99418-120">Since a transaction can only be started on an open connection, this meant that the only way a user could wrap several operations into one transaction was either to use a [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) or use the **ObjectContext.Connection** property and start calling **Open()** and **BeginTransaction()** directly on the returned **EntityConnection** object.</span></span> <span data-ttu-id="99418-121">また、基になるデータベース接続でトランザクションを開始した場合、データベースに接続した API 呼び出しは失敗します。</span><span class="sxs-lookup"><span data-stu-id="99418-121">In addition, API calls which contacted the database would fail if you had started a transaction on the underlying database connection on your own.</span></span>  

> [!NOTE]
> <span data-ttu-id="99418-122">閉じられた接続のみを受け入れる制限は Entity Framework 6 で削除されました。</span><span class="sxs-lookup"><span data-stu-id="99418-122">The limitation of only accepting closed connections was removed in Entity Framework 6.</span></span> <span data-ttu-id="99418-123">詳細については、「[接続管理](~/ef6/fundamentals/connection-management.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="99418-123">For details, see [Connection Management](~/ef6/fundamentals/connection-management.md).</span></span>  

<span data-ttu-id="99418-124">EF6 以降では、フレームワークで次の機能が提供されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="99418-124">Starting with EF6 the framework now provides:</span></span>  

1. <span data-ttu-id="99418-125">**BeginTransaction ()** : ユーザーが既存の dbcontext 内でトランザクションを開始して完了するための簡単な方法。同じトランザクション内で複数の操作を組み合わせることができるため、すべてがコミットされるか、またはすべてがロールバックされます。</span><span class="sxs-lookup"><span data-stu-id="99418-125">**Database.BeginTransaction()** : An easier method for a user to start and complete transactions themselves within an existing DbContext – allowing several operations to be combined within the same transaction and hence either all committed or all rolled back as one.</span></span> <span data-ttu-id="99418-126">また、トランザクションの分離レベルをユーザーがより簡単に指定できるようにします。</span><span class="sxs-lookup"><span data-stu-id="99418-126">It also allows the user to more easily specify the isolation level for the transaction.</span></span>  
2. <span data-ttu-id="99418-127">**UseTransaction ()** : dbcontext で、Entity Framework の外部で開始されたトランザクションを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="99418-127">**Database.UseTransaction()** : which allows the DbContext to use a transaction which was started outside of the Entity Framework.</span></span>  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a><span data-ttu-id="99418-128">同じコンテキスト内で複数の操作を1つのトランザクションに結合する</span><span class="sxs-lookup"><span data-stu-id="99418-128">Combining several operations into one transaction within the same context</span></span>  

<span data-ttu-id="99418-129">**BeginTransaction ()** には、2つのオーバーライドがあります。1つは明示的な[IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx)を受け取り、もう1つは引数を取らず、基になるデータベースプロバイダーからの既定の IsolationLevel を使用します。</span><span class="sxs-lookup"><span data-stu-id="99418-129">**Database.BeginTransaction()** has two overrides – one which takes an explicit [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) and one which takes no arguments and uses the default IsolationLevel from the underlying database provider.</span></span> <span data-ttu-id="99418-130">どちらのオーバーライドも、基になるストアトランザクションでコミットとロールバックを実行する**commit ()** メソッドと**rollback ()** メソッドを提供する**dbcontexttransaction**オブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="99418-130">Both overrides return a **DbContextTransaction** object which provides **Commit()** and **Rollback()** methods which perform commit and rollback on the underlying store transaction.</span></span>  

<span data-ttu-id="99418-131">**Dbcontexttransaction**は、コミットまたはロールバックされた後に破棄されることを意図しています。</span><span class="sxs-lookup"><span data-stu-id="99418-131">The **DbContextTransaction** is meant to be disposed once it has been committed or rolled back.</span></span> <span data-ttu-id="99418-132">これを実現する簡単な方法の1つは、 **using (...) {...}**</span><span class="sxs-lookup"><span data-stu-id="99418-132">One easy way to accomplish this is the **using(…) {…}**</span></span> <span data-ttu-id="99418-133">using ブロックが完了すると自動的に**Dispose ()** を呼び出す構文。</span><span class="sxs-lookup"><span data-stu-id="99418-133">syntax which will automatically call **Dispose()** when the using block completes:</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void StartOwnTransactionWithinContext()
        {
            using (var context = new BloggingContext())
            {
                using (var dbContextTransaction = context.Database.BeginTransaction())
                {
                    context.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'"
                        );

                    var query = context.Posts.Where(p => p.Blog.Rating >= 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }

                    context.SaveChanges();

                    dbContextTransaction.Commit();
                }
            }
        }
    }
}
```  

> [!NOTE]
> <span data-ttu-id="99418-134">トランザクションを開始するには、基になるストア接続が開いている必要があります。</span><span class="sxs-lookup"><span data-stu-id="99418-134">Beginning a transaction requires that the underlying store connection is open.</span></span> <span data-ttu-id="99418-135">そのため、BeginTransaction () を呼び出すと、接続が開かれていない場合にその接続が開かれます。</span><span class="sxs-lookup"><span data-stu-id="99418-135">So calling Database.BeginTransaction() will open the connection  if it is not already opened.</span></span> <span data-ttu-id="99418-136">DbContextTransaction が接続を開いた場合、Dispose () が呼び出されると、このトランザクションは閉じられます。</span><span class="sxs-lookup"><span data-stu-id="99418-136">If DbContextTransaction opened the connection then it will close it when Dispose() is called.</span></span>  

### <a name="passing-an-existing-transaction-to-the-context"></a><span data-ttu-id="99418-137">既存のトランザクションをコンテキストに渡す</span><span class="sxs-lookup"><span data-stu-id="99418-137">Passing an existing transaction to the context</span></span>  

<span data-ttu-id="99418-138">場合によっては、スコープ内でさらに広範なトランザクションを使用し、同じデータベースに対する操作や、EF の外部で完全に操作を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="99418-138">Sometimes you would like a transaction which is even broader in scope and which includes operations on the same database but outside of EF completely.</span></span> <span data-ttu-id="99418-139">これを実現するには、接続を開いて、自分でトランザクションを開始してから、既に開かれているデータベース接続を使用するように EF a に指示し、b) その接続で既存のトランザクションを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99418-139">To accomplish this you must open the connection and start the transaction yourself and then tell EF a) to use the already-opened database connection, and b) to use the existing transaction on that connection.</span></span>  

<span data-ttu-id="99418-140">これを行うには、既存の接続パラメーターと ii) contextOwnsConnection ブール値を受け取る DbContext コンストラクターの1つを継承する、コンテキストクラスでコンストラクターを定義して使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99418-140">To do this you must define and use a constructor on your context class which inherits from one of the DbContext constructors which take i) an existing connection parameter and ii) the contextOwnsConnection boolean.</span></span>  

> [!NOTE]
> <span data-ttu-id="99418-141">このシナリオで呼び出される場合は、contextOwnsConnection フラグを false に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="99418-141">The contextOwnsConnection flag must be set to false when called in this scenario.</span></span> <span data-ttu-id="99418-142">これは、接続が終了したときに接続を終了しないことを Entity Framework 通知するために重要です (たとえば、次の4行目を参照)。</span><span class="sxs-lookup"><span data-stu-id="99418-142">This is important as it informs Entity Framework that it should not close the connection when it is done with it (for example, see line 4 below):</span></span>  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

<span data-ttu-id="99418-143">さらに、(既定の設定を避ける必要がある場合は IsolationLevel を含む) 自分でトランザクションを開始し、接続で既に開始されているトランザクションがあることを Entity Framework 確認してください (下記の33行目を参照)。</span><span class="sxs-lookup"><span data-stu-id="99418-143">Furthermore, you must start the transaction yourself (including the IsolationLevel if you want to avoid the default setting) and let Entity Framework know that there is an existing transaction already started on the connection (see line 33 below).</span></span>  

<span data-ttu-id="99418-144">その後、SqlConnection 自体、または DbContext で直接データベース操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="99418-144">Then you are free to execute database operations either directly on the SqlConnection itself, or on the DbContext.</span></span> <span data-ttu-id="99418-145">このような操作はすべて1つのトランザクション内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="99418-145">All such operations are executed within one transaction.</span></span> <span data-ttu-id="99418-146">トランザクションのコミットまたはロールバック、およびそのトランザクションに対する Dispose () の呼び出し、およびデータベース接続の終了と破棄を行います。</span><span class="sxs-lookup"><span data-stu-id="99418-146">You take responsibility for committing or rolling back the transaction and for calling Dispose() on it, as well as for closing and disposing the database connection.</span></span> <span data-ttu-id="99418-147">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="99418-147">For example:</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
     class TransactionsExample
     {
        static void UsingExternalTransaction()
        {
            using (var conn = new SqlConnection("..."))
            {
               conn.Open();

               using (var sqlTxn = conn.BeginTransaction(System.Data.IsolationLevel.Snapshot))
               {
                   var sqlCommand = new SqlCommand();
                   sqlCommand.Connection = conn;
                   sqlCommand.Transaction = sqlTxn;
                   sqlCommand.CommandText =
                       @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'";
                   sqlCommand.ExecuteNonQuery();

                   using (var context =  
                     new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        context.Database.UseTransaction(sqlTxn);

                        var query =  context.Posts.Where(p => p.Blog.Rating >= 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                       context.SaveChanges();
                    }

                    sqlTxn.Commit();
                }
            }
        }
    }
}
```  

### <a name="clearing-up-the-transaction"></a><span data-ttu-id="99418-148">トランザクションを消去しています</span><span class="sxs-lookup"><span data-stu-id="99418-148">Clearing up the transaction</span></span>

<span data-ttu-id="99418-149">UseTransaction () に null を渡すことにより、現在のトランザクションに関する Entity Framework の知識を消去できます。</span><span class="sxs-lookup"><span data-stu-id="99418-149">You can pass null to Database.UseTransaction() to clear Entity Framework’s knowledge of the current transaction.</span></span> <span data-ttu-id="99418-150">この操作を行っても、Entity Framework によって既存のトランザクションがコミットまたはロールバックされることはありません。そのため、この操作を行う必要がある場合にのみ、慎重に使用してください。</span><span class="sxs-lookup"><span data-stu-id="99418-150">Entity Framework will neither commit nor rollback the existing transaction when you do this, so use with care and only if you’re sure this is what you want to do.</span></span>  

### <a name="errors-in-usetransaction"></a><span data-ttu-id="99418-151">UseTransaction のエラー</span><span class="sxs-lookup"><span data-stu-id="99418-151">Errors in UseTransaction</span></span>

<span data-ttu-id="99418-152">次の場合にトランザクションを渡すと、UseTransaction () の例外が表示されます。</span><span class="sxs-lookup"><span data-stu-id="99418-152">You will see an exception from Database.UseTransaction() if you pass a transaction when:</span></span>  
- <span data-ttu-id="99418-153">Entity Framework 既存のトランザクションが既に存在します</span><span class="sxs-lookup"><span data-stu-id="99418-153">Entity Framework already has an existing transaction</span></span>  
- <span data-ttu-id="99418-154">Entity Framework は TransactionScope 内で既に動作しています</span><span class="sxs-lookup"><span data-stu-id="99418-154">Entity Framework is already operating within a TransactionScope</span></span>  
- <span data-ttu-id="99418-155">渡されたトランザクション内の接続オブジェクトが null です。</span><span class="sxs-lookup"><span data-stu-id="99418-155">The connection object in the transaction passed is null.</span></span> <span data-ttu-id="99418-156">つまり、トランザクションは接続に関連付けられていません。通常、これはトランザクションが既に完了していることを示す符号です。</span><span class="sxs-lookup"><span data-stu-id="99418-156">That is, the transaction is not associated with a connection – usually this is a sign that that transaction has already completed</span></span>  
- <span data-ttu-id="99418-157">渡されたトランザクションの接続オブジェクトが Entity Framework の接続と一致しません。</span><span class="sxs-lookup"><span data-stu-id="99418-157">The connection object in the transaction passed does not match the Entity Framework’s connection.</span></span>  

## <a name="using-transactions-with-other-features"></a><span data-ttu-id="99418-158">他の機能でのトランザクションの使用</span><span class="sxs-lookup"><span data-stu-id="99418-158">Using transactions with other features</span></span>  

<span data-ttu-id="99418-159">このセクションでは、上記のトランザクションとの相互作用について詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="99418-159">This section details how the above transactions interact with:</span></span>  

- <span data-ttu-id="99418-160">接続の回復性</span><span class="sxs-lookup"><span data-stu-id="99418-160">Connection resiliency</span></span>  
- <span data-ttu-id="99418-161">非同期メソッド</span><span class="sxs-lookup"><span data-stu-id="99418-161">Asynchronous methods</span></span>  
- <span data-ttu-id="99418-162">TransactionScope トランザクション</span><span class="sxs-lookup"><span data-stu-id="99418-162">TransactionScope transactions</span></span>  

### <a name="connection-resiliency"></a><span data-ttu-id="99418-163">接続の回復</span><span class="sxs-lookup"><span data-stu-id="99418-163">Connection Resiliency</span></span>  

<span data-ttu-id="99418-164">新しい接続の回復性機能は、ユーザーが開始したトランザクションでは機能しません。</span><span class="sxs-lookup"><span data-stu-id="99418-164">The new Connection Resiliency feature does not work with user-initiated transactions.</span></span> <span data-ttu-id="99418-165">詳細については、「[実行方法の再試行](~/ef6/fundamentals/connection-resiliency/retry-logic.md#user-initiated-transactions-are-not-supported)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="99418-165">For details, see [Retrying Execution Strategies](~/ef6/fundamentals/connection-resiliency/retry-logic.md#user-initiated-transactions-are-not-supported).</span></span>  

### <a name="asynchronous-programming"></a><span data-ttu-id="99418-166">非同期プログラミング</span><span class="sxs-lookup"><span data-stu-id="99418-166">Asynchronous Programming</span></span>  

<span data-ttu-id="99418-167">前のセクションで説明した方法では、[非同期クエリおよび保存メソッド](~/ef6/fundamentals/async.md
)を操作するためのその他のオプションや設定は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="99418-167">The approach outlined in the previous sections needs no further options or settings to work with the [asynchronous query and save methods](~/ef6/fundamentals/async.md
).</span></span> <span data-ttu-id="99418-168">ただし、非同期メソッドの実行内容によっては、トランザクションが長時間実行される可能性があることに注意してください。これにより、アプリケーション全体のパフォーマンスを低下させるデッドロックやブロッキングが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="99418-168">But be aware that, depending on what you do within the asynchronous methods, this may result in long-running transactions – which can in turn cause deadlocks or blocking which is bad for the performance of the overall application.</span></span>  

### <a name="transactionscope-transactions"></a><span data-ttu-id="99418-169">TransactionScope トランザクション</span><span class="sxs-lookup"><span data-stu-id="99418-169">TransactionScope Transactions</span></span>  

<span data-ttu-id="99418-170">EF6 より前の場合は、より大きなスコープのトランザクションを提供するために、TransactionScope オブジェクトを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="99418-170">Prior to EF6 the recommended way of providing larger scope transactions was to use a TransactionScope object:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        static void UsingTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeOption.Required))
            {
                using (var conn = new SqlConnection("..."))
                {
                    conn.Open();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    sqlCommand.ExecuteNonQuery();

                    using (var context =
                        new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }
                        context.SaveChanges();
                    }
                }

                scope.Complete();
            }
        }
    }
}
```  

<span data-ttu-id="99418-171">SqlConnection と Entity Framework はどちらもアンビエント TransactionScope トランザクションを使用するため、一緒にコミットされます。</span><span class="sxs-lookup"><span data-stu-id="99418-171">The SqlConnection and Entity Framework would both use the ambient TransactionScope transaction and hence be committed together.</span></span>  

<span data-ttu-id="99418-172">.NET 4.5.1 以降の TransactionScope は、 [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx)列挙体を使用して非同期メソッドでも動作するように更新されました。</span><span class="sxs-lookup"><span data-stu-id="99418-172">Starting with .NET 4.5.1 TransactionScope has been updated to also work with asynchronous methods via the use of the [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) enumeration:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace TransactionsExamples
{
    class TransactionsExample
    {
        public static void AsyncTransactionScope()
        {
            using (var scope = new TransactionScope(TransactionScopeAsyncFlowOption.Enabled))
            {
                using (var conn = new SqlConnection("..."))
                {
                    await conn.OpenAsync();

                    var sqlCommand = new SqlCommand();
                    sqlCommand.Connection = conn;
                    sqlCommand.CommandText =
                        @"UPDATE Blogs SET Rating = 5" +
                            " WHERE Name LIKE '%Entity Framework%'";
                    await sqlCommand.ExecuteNonQueryAsync();

                    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                    {
                        var query = context.Posts.Where(p => p.Blog.Rating > 5);
                        foreach (var post in query)
                        {
                            post.Title += "[Cool Blog]";
                        }

                        await context.SaveChangesAsync();
                    }
                }
            }
        }
    }
}
```  

<span data-ttu-id="99418-173">TransactionScope アプローチにはまだいくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="99418-173">There are still some limitations to the TransactionScope approach:</span></span>  

- <span data-ttu-id="99418-174">非同期メソッドを操作するには、.NET 4.5.1 以降が必要です。</span><span class="sxs-lookup"><span data-stu-id="99418-174">Requires .NET 4.5.1 or greater to work with asynchronous methods.</span></span>  
- <span data-ttu-id="99418-175">接続が1つしかない (クラウドシナリオでは分散トランザクションがサポートされていない) 場合を除き、クラウドシナリオでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="99418-175">It cannot be used in cloud scenarios unless you are sure you have one and only one connection (cloud scenarios do not support distributed transactions).</span></span>  
- <span data-ttu-id="99418-176">前のセクションの UseTransaction () アプローチと組み合わせることはできません。</span><span class="sxs-lookup"><span data-stu-id="99418-176">It cannot be combined with the Database.UseTransaction() approach of the previous sections.</span></span>  
- <span data-ttu-id="99418-177">DDL を発行し、MSDTC サービスを介して分散トランザクションが有効になっていない場合は、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="99418-177">It will throw exceptions if you issue any DDL and have not enabled distributed transactions through the MSDTC Service.</span></span>  

<span data-ttu-id="99418-178">TransactionScope アプローチの利点は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="99418-178">Advantages of the TransactionScope approach:</span></span>  

- <span data-ttu-id="99418-179">特定のデータベースに対して複数の接続を作成したり、同じトランザクション内の別のデータベースへの接続を使用して1つのデータベースに接続を結合したりすると、ローカルトランザクションは分散トランザクションに自動的にアップグレードされます (注:分散トランザクションが機能するように MSDTC サービスが構成されています)。</span><span class="sxs-lookup"><span data-stu-id="99418-179">It will automatically upgrade a local transaction to a distributed transaction if you make more than one connection to a given database or combine a connection to one database with a connection to a different database within the same transaction (note: you must have the MSDTC service configured to allow distributed transactions for this to work).</span></span>  
- <span data-ttu-id="99418-180">コーディングの容易さ。</span><span class="sxs-lookup"><span data-stu-id="99418-180">Ease of coding.</span></span> <span data-ttu-id="99418-181">トランザクションがアンビエントで、明示的に制御するのではなく、バックグラウンドで暗黙的に処理されるようにする場合は、TransactionScope アプローチの方が適している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="99418-181">If you prefer the transaction to be ambient and dealt with implicitly in the background rather than explicitly under you control then the TransactionScope approach may suit you better.</span></span>  

<span data-ttu-id="99418-182">要約すると、上記の新しい BeginTransaction () Api と UseTransaction () Api を使用すると、ほとんどのユーザーに対して TransactionScope アプローチは不要になります。</span><span class="sxs-lookup"><span data-stu-id="99418-182">In summary, with the new Database.BeginTransaction() and Database.UseTransaction() APIs above, the TransactionScope approach is no longer necessary for most users.</span></span> <span data-ttu-id="99418-183">引き続き TransactionScope を使用する場合は、上記の制限事項に注意してください。</span><span class="sxs-lookup"><span data-stu-id="99418-183">If you do continue to use TransactionScope then be aware of the above limitations.</span></span> <span data-ttu-id="99418-184">可能であれば、前のセクションで説明した方法を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="99418-184">We recommend using the approach outlined in the previous sections instead where possible.</span></span>  
