---
title: トランザクションコミットエラーの処理 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: cf2722496e207a8ecaa9cfaa4ca61e7248e5e58f
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434137"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="8da64-102">トランザクションのコミット失敗の処理</span><span class="sxs-lookup"><span data-stu-id="8da64-102">Handling transaction commit failures</span></span>
> [!NOTE]
> <span data-ttu-id="8da64-103">**EF6.1 以降のみ**- このページで説明する機能、API などは、Entity Framework 6.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="8da64-103">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="8da64-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="8da64-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="8da64-105">6.1 の一部として、EF の新しい接続復元機能を導入しています: 一時的な接続の失敗がトランザクション コミットの受信確認に影響を与えたときに自動的に検出し、回復する機能。</span><span class="sxs-lookup"><span data-stu-id="8da64-105">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="8da64-106">シナリオの詳細については、ブログ記事[「SQL データベース接続」と「デンデポティニティ](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)問題」で説明します。</span><span class="sxs-lookup"><span data-stu-id="8da64-106">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span></span>  <span data-ttu-id="8da64-107">要約すると、シナリオは、トランザクションのコミット中に例外が発生したときに、次の 2 つの原因が考えられます。</span><span class="sxs-lookup"><span data-stu-id="8da64-107">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="8da64-108">トランザクションのコミットがサーバーで失敗しました</span><span class="sxs-lookup"><span data-stu-id="8da64-108">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="8da64-109">トランザクションのコミットはサーバーで成功しましたが、接続の問題により、成功通知がクライアントに届かないようにしました</span><span class="sxs-lookup"><span data-stu-id="8da64-109">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="8da64-110">最初の状況が発生した場合、アプリケーションまたはユーザーは操作を再試行できますが、2 番目の状況が発生した場合は再試行を回避し、アプリケーションが自動的に回復する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8da64-110">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="8da64-111">課題は、コミット中に例外が報告された実際の理由を検出する機能がなければ、アプリケーションは適切なアクション・コースを選択できないことです。</span><span class="sxs-lookup"><span data-stu-id="8da64-111">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="8da64-112">EF 6.1 の新機能により、トランザクションが成功した場合に EF がデータベースを再確認し、適切なアクションを透過的に実行できます。</span><span class="sxs-lookup"><span data-stu-id="8da64-112">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="8da64-113">機能の使用</span><span class="sxs-lookup"><span data-stu-id="8da64-113">Using the feature</span></span>  

<span data-ttu-id="8da64-114">この機能を有効にするには **、DbConfiguration**のコンストラクターに[SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)の呼び出しを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="8da64-114">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="8da64-115">**DbConfiguration**についてよく知らない場合は、「[コード ベースの構成](~/ef6/fundamentals/configuring/code-based.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8da64-115">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](~/ef6/fundamentals/configuring/code-based.md).</span></span> <span data-ttu-id="8da64-116">この機能は、EF6 で導入された自動再試行と組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="8da64-116">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

public class MyConfiguration : DbConfiguration  
{
  public MyConfiguration()  
  {  
    SetTransactionHandler(SqlProviderServices.ProviderInvariantName, () => new CommitFailureHandler());  
    SetExecutionStrategy(SqlProviderServices.ProviderInvariantName, () => new SqlAzureExecutionStrategy());  
  }  
}
```  

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="8da64-117">トランザクションの追跡方法</span><span class="sxs-lookup"><span data-stu-id="8da64-117">How transactions are tracked</span></span>  

<span data-ttu-id="8da64-118">この機能が有効になると、EF は新しいテーブルを **__Transactions**と呼ばれるデータベースに自動的に追加します。</span><span class="sxs-lookup"><span data-stu-id="8da64-118">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="8da64-119">トランザクションが EF によって作成されるたびに、このテーブルに新しい行が挿入され、コミット中にトランザクションの失敗が発生した場合にその行が存在するかどうかがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="8da64-119">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="8da64-120">EF は、不要になったテーブルから行を削除するために最善の努力をしますが、アプリケーションが途中で終了するとテーブルが大きくなる可能性があり、そのためテーブルを手動で削除する必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="8da64-120">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="8da64-121">以前のバージョンでコミットエラーを処理する方法</span><span class="sxs-lookup"><span data-stu-id="8da64-121">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="8da64-122">EF 6.1 より前の EF 製品でコミットエラーを処理するメカニズムはありませんでした。</span><span class="sxs-lookup"><span data-stu-id="8da64-122">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="8da64-123">この状況に対処するには、EF6 の以前のバージョンに適用できるいくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="8da64-123">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="8da64-124">オプション 1 - 何もしない</span><span class="sxs-lookup"><span data-stu-id="8da64-124">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="8da64-125">トランザクションコミット中に接続が失敗する可能性は低いため、この条件が実際に発生した場合にアプリケーションが失敗しても問題ありません。</span><span class="sxs-lookup"><span data-stu-id="8da64-125">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="8da64-126">オプション 2 - データベースを使用して状態をリセットする</span><span class="sxs-lookup"><span data-stu-id="8da64-126">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="8da64-127">現在の Db コンテキストを破棄します。</span><span class="sxs-lookup"><span data-stu-id="8da64-127">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="8da64-128">新しい DbContext を作成し、データベースからアプリケーションの状態を復元します。</span><span class="sxs-lookup"><span data-stu-id="8da64-128">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="8da64-129">最後の操作が正常に完了していない可能性があることをユーザーに通知します。</span><span class="sxs-lookup"><span data-stu-id="8da64-129">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="8da64-130">オプション 3 - トランザクションを手動で追跡する</span><span class="sxs-lookup"><span data-stu-id="8da64-130">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="8da64-131">トランザクションの状態を追跡するために使用されるデータベースに、追跡されていないテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="8da64-131">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="8da64-132">各トランザクションの先頭にあるテーブルに行を挿入します。</span><span class="sxs-lookup"><span data-stu-id="8da64-132">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="8da64-133">コミット中に接続が失敗した場合は、データベース内に対応する行が存在するかどうか確認します。</span><span class="sxs-lookup"><span data-stu-id="8da64-133">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     - <span data-ttu-id="8da64-134">行が存在する場合は、トランザクションが正常にコミットされた後、通常どおり続行します。</span><span class="sxs-lookup"><span data-stu-id="8da64-134">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     - <span data-ttu-id="8da64-135">行が存在しない場合は、実行方法を使用して現在の操作を再試行します。</span><span class="sxs-lookup"><span data-stu-id="8da64-135">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="8da64-136">コミットが成功した場合は、対応する行を削除して、表の増大を回避します。</span><span class="sxs-lookup"><span data-stu-id="8da64-136">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="8da64-137">[このブログの投稿](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)には、SQL Azure でこれを実現するためのサンプル コードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8da64-137">[This blog post](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contains sample code for accomplishing this on SQL Azure.</span></span>  
