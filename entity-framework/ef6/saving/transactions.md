---
title: トランザクション - EF6 の使用
author: divega
ms.date: 2016-10-23
ms.assetid: 0d0f1824-d781-4cb3-8fda-b7eaefced1cd
ms.openlocfilehash: 20b63c88c41c10b5a69660d5027097c647c7eedd
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997552"
---
# <a name="working-with-transactions"></a><span data-ttu-id="a61a6-102">トランザクションの使用</span><span class="sxs-lookup"><span data-stu-id="a61a6-102">Working with Transactions</span></span>
> [!NOTE]
> <span data-ttu-id="a61a6-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="a61a6-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="a61a6-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="a61a6-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="a61a6-105">このドキュメントでは、トランザクションの操作を簡単に EF5 から追加しました機能強化を含め、EF6 でトランザクションを使用して説明します。</span><span class="sxs-lookup"><span data-stu-id="a61a6-105">This document will describe using transactions in EF6 including the enhancements we have added since EF5 to make working with transactions easy.</span></span>  

## <a name="what-ef-does-by-default"></a><span data-ttu-id="a61a6-106">既定では EF の動作</span><span class="sxs-lookup"><span data-stu-id="a61a6-106">What EF does by default</span></span>  

<span data-ttu-id="a61a6-107">すべてのバージョンの Entity Framework では、実行するたびに**SaveChanges()** 挿入、更新、またはフレームワーク、データベースの削除が操作トランザクションでラップします。</span><span class="sxs-lookup"><span data-stu-id="a61a6-107">In all versions of Entity Framework, whenever you execute **SaveChanges()** to insert, update or delete on the database the framework will wrap that operation in a transaction.</span></span> <span data-ttu-id="a61a6-108">このトランザクションは、操作を実行する間だけ持続し、し完了します。</span><span class="sxs-lookup"><span data-stu-id="a61a6-108">This transaction lasts only long enough to execute the operation and then completes.</span></span> <span data-ttu-id="a61a6-109">このような別の操作を実行するときに新しいトランザクションが開始されます。</span><span class="sxs-lookup"><span data-stu-id="a61a6-109">When you execute another such operation a new transaction is started.</span></span>  

<span data-ttu-id="a61a6-110">EF6 で始まる**Database.ExecuteSqlCommand()** 既定ではラップのコマンドは、トランザクションに存在する 1 つがない場合は。</span><span class="sxs-lookup"><span data-stu-id="a61a6-110">Starting with EF6 **Database.ExecuteSqlCommand()** by default will wrap the command in a transaction if one was not already present.</span></span> <span data-ttu-id="a61a6-111">希望する場合は、この動作をオーバーライドすることをこのメソッドのオーバー ロードがあります。</span><span class="sxs-lookup"><span data-stu-id="a61a6-111">There are overloads of this method that allow you to override this behavior if you wish.</span></span> <span data-ttu-id="a61a6-112">などの Api を使用して、モデルに含まれるストアド プロシージャの実行を EF6 にも**ObjectContext.ExecuteFunction()** も同じ (既定の動作、現時点ではオーバーライドできません)。</span><span class="sxs-lookup"><span data-stu-id="a61a6-112">Also in EF6 execution of stored procedures included in the model through APIs such as **ObjectContext.ExecuteFunction()** does the same (except that the default behavior cannot at the moment be overridden).</span></span>  

<span data-ttu-id="a61a6-113">どちらの場合、トランザクションの分離レベルは、分離レベルはどのようなデータベース プロバイダーは既定値に設定します。</span><span class="sxs-lookup"><span data-stu-id="a61a6-113">In either case, the isolation level of the transaction is whatever isolation level the database provider considers its default setting.</span></span> <span data-ttu-id="a61a6-114">既定では、たとえば、SQL Server これは READ COMMITTED です。</span><span class="sxs-lookup"><span data-stu-id="a61a6-114">By default, for instance, on SQL Server this is READ COMMITTED.</span></span>  

<span data-ttu-id="a61a6-115">Entity Framework では、トランザクションでクエリをラップしません。</span><span class="sxs-lookup"><span data-stu-id="a61a6-115">Entity Framework does not wrap queries in a transaction.</span></span>  

<span data-ttu-id="a61a6-116">この既定の機能は、多くのユーザーとかどうかため必要はありません。 EF6 で何かするのに適しています常に行ったのとは、コードを記述だけです。</span><span class="sxs-lookup"><span data-stu-id="a61a6-116">This default functionality is suitable for a lot of users and if so there is no need to do anything different in EF6; just write the code as you always did.</span></span>  

<span data-ttu-id="a61a6-117">ただし、トランザクションをより細かく制御が必要なユーザー – これについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="a61a6-117">However some users require greater control over their transactions – this is covered in the following sections.</span></span>  

## <a name="how-the-apis-work"></a><span data-ttu-id="a61a6-118">Api のしくみ</span><span class="sxs-lookup"><span data-stu-id="a61a6-118">How the APIs work</span></span>  

<span data-ttu-id="a61a6-119">Entity Framework の EF6 の前に、(によって例外が既に開いている接続が渡された場合)、データベース接続自体を開くときにペダルします。</span><span class="sxs-lookup"><span data-stu-id="a61a6-119">Prior to EF6 Entity Framework insisted on opening the database connection itself (it threw an exception if it was passed a connection that was already open).</span></span> <span data-ttu-id="a61a6-120">ユーザーがいくつかの操作を 1 つのトランザクションをラップする唯一の方法を使用するいずれかがつまり、トランザクションは、開いている接続でのみ開始できます、ので、 [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx)を使用して、または、 **ObjectContext.Connection**プロパティと開始呼び出し**Open()** と**BeginTransaction()** 、返された上で直接**EntityConnection**オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="a61a6-120">Since a transaction can only be started on an open connection, this meant that the only way a user could wrap several operations into one transaction was either to use a [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx) or use the **ObjectContext.Connection** property and start calling **Open()** and **BeginTransaction()** directly on the returned **EntityConnection** object.</span></span> <span data-ttu-id="a61a6-121">さらに、独自の基になるデータベース接続でトランザクションを開始していた場合、データベースに接続する API 呼び出しは失敗します。</span><span class="sxs-lookup"><span data-stu-id="a61a6-121">In addition, API calls which contacted the database would fail if you had started a transaction on the underlying database connection on your own.</span></span>  

> [!NOTE]
> <span data-ttu-id="a61a6-122">閉じられた接続の受け入れのみの制限は、Entity Framework 6 で削除されました。</span><span class="sxs-lookup"><span data-stu-id="a61a6-122">The limitation of only accepting closed connections was removed in Entity Framework 6.</span></span> <span data-ttu-id="a61a6-123">詳細については、次を参照してください。[接続管理](~/ef6/fundamentals/connection-management.md)します。</span><span class="sxs-lookup"><span data-stu-id="a61a6-123">For details, see [Connection Management](~/ef6/fundamentals/connection-management.md).</span></span>  

<span data-ttu-id="a61a6-124">これで、フレームワークを ef6 開始を提供します。</span><span class="sxs-lookup"><span data-stu-id="a61a6-124">Starting with EF6 the framework now provides:</span></span>  

1. <span data-ttu-id="a61a6-125">**Database.BeginTransaction()** : を簡単にユーザーを起動し、同じトランザクション内で結合するいくつかの操作を行えるように、既存の DbContext – 内トランザクション自体を完了するため、すべてのコミットされたまたはすべていずれかのロール。</span><span class="sxs-lookup"><span data-stu-id="a61a6-125">**Database.BeginTransaction()** : An easier method for a user to start and complete transactions themselves within an existing DbContext – allowing several operations to be combined within the same transaction and hence either all committed or all rolled back as one.</span></span> <span data-ttu-id="a61a6-126">また、ユーザーがより簡単に、トランザクションの分離レベルを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="a61a6-126">It also allows the user to more easily specify the isolation level for the transaction.</span></span>  
2. <span data-ttu-id="a61a6-127">**Database.UseTransaction()** : Entity Framework の外部で開始されたトランザクションを使用する DbContext ことができます。</span><span class="sxs-lookup"><span data-stu-id="a61a6-127">**Database.UseTransaction()** : which allows the DbContext to use a transaction which was started outside of the Entity Framework.</span></span>  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a><span data-ttu-id="a61a6-128">同じコンテキスト内で 1 つのトランザクションにいくつかの操作を組み合わせること</span><span class="sxs-lookup"><span data-stu-id="a61a6-128">Combining several operations into one transaction within the same context</span></span>  

<span data-ttu-id="a61a6-129">**Database.BeginTransaction()** が 2 つの上書き: 1 つは、明示的な[IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx)といずれかの引数はありませんし、基になるデータベース プロバイダーから IsolationLevel の既定値を使用します。</span><span class="sxs-lookup"><span data-stu-id="a61a6-129">**Database.BeginTransaction()** has two overrides – one which takes an explicit [IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx) and one which takes no arguments and uses the default IsolationLevel from the underlying database provider.</span></span> <span data-ttu-id="a61a6-130">両方のオーバーライドを返す、 **DbContextTransaction**オブジェクトを提供する**Commit()** と**Rollback()** 基になるストアにコミットとロールバックを実行する方法トランザクションです。</span><span class="sxs-lookup"><span data-stu-id="a61a6-130">Both overrides return a **DbContextTransaction** object which provides **Commit()** and **Rollback()** methods which perform commit and rollback on the underlying store transaction.</span></span>  

<span data-ttu-id="a61a6-131">**DbContextTransaction**はコミットまたはロールバック後に破棄するためのものです。</span><span class="sxs-lookup"><span data-stu-id="a61a6-131">The **DbContextTransaction** is meant to be disposed once it has been committed or rolled back.</span></span> <span data-ttu-id="a61a6-132">簡単にこれを実現方法の 1 つは、 **using(...){…}**</span><span class="sxs-lookup"><span data-stu-id="a61a6-132">One easy way to accomplish this is the **using(…) {…}**</span></span> <span data-ttu-id="a61a6-133">自動的に呼び出されます構文**Dispose()** 完了ブロックを使用してする場合。</span><span class="sxs-lookup"><span data-stu-id="a61a6-133">syntax which will automatically call **Dispose()** when the using block completes:</span></span>  

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
                    try
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
                    catch (Exception)
                    {
                        dbContextTransaction.Rollback();
                    }
                }
            }
        }
    }
}
```  

> [!NOTE]
> <span data-ttu-id="a61a6-134">トランザクションを開始するには、基になるストア接続が開かれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="a61a6-134">Beginning a transaction requires that the underlying store connection is open.</span></span> <span data-ttu-id="a61a6-135">Database.BeginTransaction() を呼び出すのでが開かれていない場合は、接続が開かれます。</span><span class="sxs-lookup"><span data-stu-id="a61a6-135">So calling Database.BeginTransaction() will open the connection  if it is not already opened.</span></span> <span data-ttu-id="a61a6-136">DbContextTransaction には、接続が開かれている場合、閉じられます、Dispose() が呼び出されるとします。</span><span class="sxs-lookup"><span data-stu-id="a61a6-136">If DbContextTransaction opened the connection then it will close it when Dispose() is called.</span></span>  

### <a name="passing-an-existing-transaction-to-the-context"></a><span data-ttu-id="a61a6-137">既存のトランザクションをコンテキストに渡す</span><span class="sxs-lookup"><span data-stu-id="a61a6-137">Passing an existing transaction to the context</span></span>  

<span data-ttu-id="a61a6-138">場合がありますも広範なスコープでは完全に同じデータベースでは、EF の外部での操作を含む、トランザクションを作成するとします。</span><span class="sxs-lookup"><span data-stu-id="a61a6-138">Sometimes you would like a transaction which is even broader in scope and which includes operations on the same database but outside of EF completely.</span></span> <span data-ttu-id="a61a6-139">これを実現するには、接続を開いてしと自分でトランザクションを開始をし)、データベースを既に開いている接続を使用して、その接続で、既存のトランザクションを使用するには b) の EF を指示する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a61a6-139">To accomplish this you must open the connection and start the transaction yourself and then tell EF a) to use the already-opened database connection, and b) to use the existing transaction on that connection.</span></span>  

<span data-ttu-id="a61a6-140">これを行うには、定義し、ブール i) 既存の接続パラメーターと contextOwnsConnection ii) を実行する DbContext コンス トラクターのいずれかから継承され、コンテキスト クラスのコンス トラクターを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a61a6-140">To do this you must define and use a constructor on your context class which inherits from one of the DbContext constructors which take i) an existing connection parameter and ii) the contextOwnsConnection boolean.</span></span>  

> [!NOTE]
> <span data-ttu-id="a61a6-141">ContextOwnsConnection フラグは、このシナリオで呼び出される場合は false に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a61a6-141">The contextOwnsConnection flag must be set to false when called in this scenario.</span></span> <span data-ttu-id="a61a6-142">これは、Entity Framework に通知を閉じることはできません、接続には、そのときに重要です。 (たとえば、次の 4 行目を参照)。</span><span class="sxs-lookup"><span data-stu-id="a61a6-142">This is important as it informs Entity Framework that it should not close the connection when it is done with it (for example, see line 4 below):</span></span>  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

<span data-ttu-id="a61a6-143">さらに、自分で (既定の設定を回避する場合、IsolationLevel を含む)、トランザクションを開始して、既存のトランザクション接続を既に開始されている Entity Framework を使用 (33 以下の行を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="a61a6-143">Furthermore, you must start the transaction yourself (including the IsolationLevel if you want to avoid the default setting) and let Entity Framework know that there is an existing transaction already started on the connection (see line 33 below).</span></span>  

<span data-ttu-id="a61a6-144">SqlConnection 自体で直接、または、DbContext でデータベース操作を実行されます。</span><span class="sxs-lookup"><span data-stu-id="a61a6-144">Then you are free to execute database operations either directly on the SqlConnection itself, or on the DbContext.</span></span> <span data-ttu-id="a61a6-145">このようなすべての操作は、1 つのトランザクション内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="a61a6-145">All such operations are executed within one transaction.</span></span> <span data-ttu-id="a61a6-146">責任のコミットやトランザクションをロールバックしで Dispose() を呼び出すため、およびを閉じると、データベース接続の破棄を行います。</span><span class="sxs-lookup"><span data-stu-id="a61a6-146">You take responsibility for committing or rolling back the transaction and for calling Dispose() on it, as well as for closing and disposing the database connection.</span></span> <span data-ttu-id="a61a6-147">例えば:</span><span class="sxs-lookup"><span data-stu-id="a61a6-147">For example:</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
sing System.Transactions;

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
                   try
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
                    catch (Exception)
                    {
                        sqlTxn.Rollback();
                    }
                }
            }
        }
    }
}
```  

### <a name="clearing-up-the-transaction"></a><span data-ttu-id="a61a6-148">トランザクションをオフにします。</span><span class="sxs-lookup"><span data-stu-id="a61a6-148">Clearing up the transaction</span></span>

<span data-ttu-id="a61a6-149">Null を渡す Database.UseTransaction() を現在のトランザクションの Entity Framework のナレッジをオフにすることができます。</span><span class="sxs-lookup"><span data-stu-id="a61a6-149">You can pass null to Database.UseTransaction() to clear Entity Framework’s knowledge of the current transaction.</span></span> <span data-ttu-id="a61a6-150">Entity Framework は、どちらもコミットもこれを行うときに、既存のトランザクションのロールバックがので注意して使用し、これは、実行することを確認している場合にのみになります。</span><span class="sxs-lookup"><span data-stu-id="a61a6-150">Entity Framework will neither commit nor rollback the existing transaction when you do this, so use with care and only if you’re sure this is what you want to do.</span></span>  

### <a name="errors-in-usetransaction"></a><span data-ttu-id="a61a6-151">[いいえ] のエラー</span><span class="sxs-lookup"><span data-stu-id="a61a6-151">Errors in UseTransaction</span></span>

<span data-ttu-id="a61a6-152">トランザクションを渡す場合 Database.UseTransaction() から例外が表示される場合。</span><span class="sxs-lookup"><span data-stu-id="a61a6-152">You will see an exception from Database.UseTransaction() if you pass a transaction when:</span></span>  
- <span data-ttu-id="a61a6-153">Entity Framework が既に既存のトランザクション</span><span class="sxs-lookup"><span data-stu-id="a61a6-153">Entity Framework already has an existing transaction</span></span>  
- <span data-ttu-id="a61a6-154">Entity Framework は既に、TransactionScope 内で動作しています。</span><span class="sxs-lookup"><span data-stu-id="a61a6-154">Entity Framework is already operating within a TransactionScope</span></span>  
- <span data-ttu-id="a61a6-155">渡されたトランザクション内の接続オブジェクトが null です。</span><span class="sxs-lookup"><span data-stu-id="a61a6-155">The connection object in the transaction passed is null.</span></span> <span data-ttu-id="a61a6-156">つまり、トランザクションは接続に関連付けられていない – 通常、これはそのトランザクションが既に完了したサインイン</span><span class="sxs-lookup"><span data-stu-id="a61a6-156">That is, the transaction is not associated with a connection – usually this is a sign that that transaction has already completed</span></span>  
- <span data-ttu-id="a61a6-157">渡されたトランザクション内の接続オブジェクトでは、Entity Framework の接続は一致しません。</span><span class="sxs-lookup"><span data-stu-id="a61a6-157">The connection object in the transaction passed does not match the Entity Framework’s connection.</span></span>  

## <a name="using-transactions-with-other-features"></a><span data-ttu-id="a61a6-158">他の機能とトランザクションの使用</span><span class="sxs-lookup"><span data-stu-id="a61a6-158">Using transactions with other features</span></span>  

<span data-ttu-id="a61a6-159">このセクションでは、上記のトランザクションと対話する方法についてください。</span><span class="sxs-lookup"><span data-stu-id="a61a6-159">This section details how the above transactions interact with:</span></span>  

- <span data-ttu-id="a61a6-160">接続の復元性</span><span class="sxs-lookup"><span data-stu-id="a61a6-160">Connection resiliency</span></span>  
- <span data-ttu-id="a61a6-161">非同期メソッド</span><span class="sxs-lookup"><span data-stu-id="a61a6-161">Asynchronous methods</span></span>  
- <span data-ttu-id="a61a6-162">TransactionScope トランザクション</span><span class="sxs-lookup"><span data-stu-id="a61a6-162">TransactionScope transactions</span></span>  

### <a name="connection-resiliency"></a><span data-ttu-id="a61a6-163">接続の復元性</span><span class="sxs-lookup"><span data-stu-id="a61a6-163">Connection Resiliency</span></span>  

<span data-ttu-id="a61a6-164">新しい接続の回復性機能には、ユーザーによって開始されたトランザクションでは使えません。</span><span class="sxs-lookup"><span data-stu-id="a61a6-164">The new Connection Resiliency feature does not work with user-initiated transactions.</span></span> <span data-ttu-id="a61a6-165">詳細については、次を参照してください。[再試行実行戦略の制限事項](~/ef6/fundamentals/connection-resiliency/retry-logic.md#limitations)します。</span><span class="sxs-lookup"><span data-stu-id="a61a6-165">For details, see [Limitations with Retrying Execution Strategies](~/ef6/fundamentals/connection-resiliency/retry-logic.md#limitations).</span></span>  

### <a name="asynchronous-programming"></a><span data-ttu-id="a61a6-166">非同期プログラミング</span><span class="sxs-lookup"><span data-stu-id="a61a6-166">Asynchronous Programming</span></span>  

<span data-ttu-id="a61a6-167">前のセクションで説明されているアプローチには、それ以上のオプションや設定を使用する必要ありません、[非同期クエリを実行し、メソッドの保存](~/ef6/fundamentals/async.md
)します。</span><span class="sxs-lookup"><span data-stu-id="a61a6-167">The approach outlined in the previous sections needs no further options or settings to work with the [asynchronous query and save methods](~/ef6/fundamentals/async.md
).</span></span> <span data-ttu-id="a61a6-168">行う動作によって、非同期メソッド内で、この可能性 – デッドロックまたはブロックは、アプリケーション全体のパフォーマンスを低下が発生することがさらに実行時間の長いトランザクションに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a61a6-168">But be aware that, depending on what you do within the asynchronous methods, this may result in long-running transactions – which can in turn cause deadlocks or blocking which is bad for the performance of the overall application.</span></span>  

### <a name="transactionscope-transactions"></a><span data-ttu-id="a61a6-169">TransactionScope トランザクション</span><span class="sxs-lookup"><span data-stu-id="a61a6-169">TransactionScope Transactions</span></span>  

<span data-ttu-id="a61a6-170">EF6 の前によりも大きなスコープのトランザクションを提供することをお勧めの方法は、TransactionScope オブジェクトを使用してでした。</span><span class="sxs-lookup"><span data-stu-id="a61a6-170">Prior to EF6 the recommended way of providing larger scope transactions was to use a TransactionScope object:</span></span>  

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

<span data-ttu-id="a61a6-171">SqlConnection と Entity Framework し、TransactionScope アンビエント トランザクションを使用して、したがって一緒にコミットします。</span><span class="sxs-lookup"><span data-stu-id="a61a6-171">The SqlConnection and Entity Framework would both use the ambient TransactionScope transaction and hence be committed together.</span></span>  

<span data-ttu-id="a61a6-172">以降では、.NET 4.5.1 も使用して非同期のメソッドを使用する TransactionScope が更新されました、 [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx)列挙体。</span><span class="sxs-lookup"><span data-stu-id="a61a6-172">Starting with .NET 4.5.1 TransactionScope has been updated to also work with asynchronous methods via the use of the [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx) enumeration:</span></span>  

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

<span data-ttu-id="a61a6-173">TransactionScope アプローチをいくつかの制限は引き続きがあります。</span><span class="sxs-lookup"><span data-stu-id="a61a6-173">There are still some limitations to the TransactionScope approach:</span></span>  

- <span data-ttu-id="a61a6-174">.NET 4.5.1 が必要以上の非同期メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="a61a6-174">Requires .NET 4.5.1 or greater to work with asynchronous methods.</span></span>  
- <span data-ttu-id="a61a6-175">1 つだけの接続があることを確認する場合を除いてには、クラウドのシナリオで使用することはできません (クラウドのシナリオでは分散トランザクションはサポートされません)。</span><span class="sxs-lookup"><span data-stu-id="a61a6-175">It cannot be used in cloud scenarios unless you are sure you have one and only one connection (cloud scenarios do not support distributed transactions).</span></span>  
- <span data-ttu-id="a61a6-176">前のセクションでは、の Database.UseTransaction() アプローチを組み合わせることはできません。</span><span class="sxs-lookup"><span data-stu-id="a61a6-176">It cannot be combined with the Database.UseTransaction() approach of the previous sections.</span></span>  
- <span data-ttu-id="a61a6-177">任意の DDL を発行し、MSDTC サービスを使用した分散トランザクションを有効にしていない場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="a61a6-177">It will throw exceptions if you issue any DDL and have not enabled distributed transactions through the MSDTC Service.</span></span>  

<span data-ttu-id="a61a6-178">TransactionScope アプローチの利点があります。</span><span class="sxs-lookup"><span data-stu-id="a61a6-178">Advantages of the TransactionScope approach:</span></span>  

- <span data-ttu-id="a61a6-179">それが自動的にアップグレード ローカル トランザクションを分散トランザクションに指定されたデータベースには、複数の接続を作成するか、または、同じトランザクション内の別のデータベースへの接続と 1 つのデータベースへの接続を組み合わせる場合 (注: が必要MSDTC サービスではこれが機能する分散トランザクションを許可するように構成します。</span><span class="sxs-lookup"><span data-stu-id="a61a6-179">It will automatically upgrade a local transaction to a distributed transaction if you make more than one connection to a given database or combine a connection to one database with a connection to a different database within the same transaction (note: you must have the MSDTC service configured to allow distributed transactions for this to work).</span></span>  
- <span data-ttu-id="a61a6-180">コーディングの容易さ。</span><span class="sxs-lookup"><span data-stu-id="a61a6-180">Ease of coding.</span></span> <span data-ttu-id="a61a6-181">明示的に制御するのではなく、アンビエントとバック グラウンドで暗黙的にで対応するトランザクションを使用する場合、TransactionScope アプローチ可能性がありますに合わせてする向上。</span><span class="sxs-lookup"><span data-stu-id="a61a6-181">If you prefer the transaction to be ambient and dealt with implicitly in the background rather than explicitly under you control then the TransactionScope approach may suit you better.</span></span>  

<span data-ttu-id="a61a6-182">新しい Database.BeginTransaction() および上記の Database.UseTransaction() Api の概要では、TransactionScope アプローチはほとんどのユーザーに必要ではなくなりました。</span><span class="sxs-lookup"><span data-stu-id="a61a6-182">In summary, with the new Database.BeginTransaction() and Database.UseTransaction() APIs above, the TransactionScope approach is no longer necessary for most users.</span></span> <span data-ttu-id="a61a6-183">引き続き TransactionScope を使用する場合、上記の制限事項に注意してくださいになります。</span><span class="sxs-lookup"><span data-stu-id="a61a6-183">If you do continue to use TransactionScope then be aware of the above limitations.</span></span> <span data-ttu-id="a61a6-184">可能な限り代わりに、前のセクションで紹介するアプローチを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a61a6-184">We recommend using the approach outlined in the previous sections instead where possible.</span></span>  
