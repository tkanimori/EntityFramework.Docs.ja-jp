---
title: トランザクションのコミットエラーの処理-EF6
description: Entity Framework 6 でのトランザクションのコミットエラーの処理
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-resiliency/commit-failures
ms.openlocfilehash: c817e5e9342d5e0682318dc34fa3b4cdfa756228
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072851"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="31242-103">トランザクションのコミットエラーの処理</span><span class="sxs-lookup"><span data-stu-id="31242-103">Handling transaction commit failures</span></span>

> [!NOTE]
> <span data-ttu-id="31242-104">**Ef 6.1 以降のみ** -このページで説明した機能、api などが Entity Framework 6.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="31242-104">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="31242-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="31242-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="31242-106">6.1 の一部として、EF の新しい接続回復性機能が導入されています。これは、一時的な接続エラーがトランザクションのコミットの受信確認に影響する場合に、自動的に検出して回復する機能です。</span><span class="sxs-lookup"><span data-stu-id="31242-106">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="31242-107">このシナリオの詳細については、ブログ SQL Database 記事「 [接続とべき等の問題](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31242-107">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue).</span></span>  <span data-ttu-id="31242-108">つまり、このシナリオでは、トランザクションのコミット中に例外が発生すると、次の2つの原因が考えられます。</span><span class="sxs-lookup"><span data-stu-id="31242-108">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="31242-109">サーバーでトランザクションのコミットに失敗しました</span><span class="sxs-lookup"><span data-stu-id="31242-109">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="31242-110">サーバーでトランザクションのコミットに成功しましたが、接続の問題により成功通知がクライアントに到達しませんでした</span><span class="sxs-lookup"><span data-stu-id="31242-110">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="31242-111">最初の状況が発生した場合、アプリケーションまたはユーザーは操作を再試行できますが、2番目の状況が発生した場合は、再試行を避け、アプリケーションを自動的に回復できます。</span><span class="sxs-lookup"><span data-stu-id="31242-111">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="31242-112">課題は、コミット中に例外が報告された実際の理由を検出する機能がないということです。アプリケーションは適切な措置を選択できません。</span><span class="sxs-lookup"><span data-stu-id="31242-112">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="31242-113">EF 6.1 の新機能では、トランザクションが成功した場合に EF がデータベースを再確認し、適切な操作を透過的に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="31242-113">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="31242-114">機能の使用</span><span class="sxs-lookup"><span data-stu-id="31242-114">Using the feature</span></span>  

<span data-ttu-id="31242-115">この機能を有効にするには、 **Dbconfiguration**のコンストラクターに[settransactionhandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)を呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="31242-115">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="31242-116">**Dbconfiguration**の詳細については、「[コードベースの構成](xref:ef6/fundamentals/configuring/code-based)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="31242-116">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](xref:ef6/fundamentals/configuring/code-based).</span></span> <span data-ttu-id="31242-117">この機能は、EF6 で導入された自動再試行と組み合わせて使用できます。これは、一時的な障害により、トランザクションが実際にサーバー上でコミットに失敗した状況に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="31242-117">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

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

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="31242-118">トランザクションの追跡方法</span><span class="sxs-lookup"><span data-stu-id="31242-118">How transactions are tracked</span></span>  

<span data-ttu-id="31242-119">この機能を有効にすると、EF によって **__Transactions**というデータベースに新しいテーブルが自動的に追加されます。</span><span class="sxs-lookup"><span data-stu-id="31242-119">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="31242-120">EF によってトランザクションが作成されるたびに、このテーブルに新しい行が挿入されます。コミット中にトランザクションエラーが発生した場合は、その行が存在するかどうかがチェックされます。</span><span class="sxs-lookup"><span data-stu-id="31242-120">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="31242-121">EF は、不要になったテーブルから行を排除するのに最適な作業を行いますが、アプリケーションが途中で終了した場合にテーブルを拡張できます。そのため、場合によっては、テーブルを手動で消去する必要があります。</span><span class="sxs-lookup"><span data-stu-id="31242-121">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="31242-122">以前のバージョンのコミットエラーを処理する方法</span><span class="sxs-lookup"><span data-stu-id="31242-122">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="31242-123">EF 6.1 より前では、EF 製品のコミットエラーを処理するメカニズムがありませんでした。</span><span class="sxs-lookup"><span data-stu-id="31242-123">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="31242-124">以前のバージョンの EF6 に適用できるこのような状況に対処するには、いくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="31242-124">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="31242-125">オプション 1-何もしない</span><span class="sxs-lookup"><span data-stu-id="31242-125">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="31242-126">トランザクションのコミット中に接続エラーが発生する可能性は低いため、この状態が実際に発生した場合にアプリケーションが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="31242-126">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="31242-127">オプション 2-データベースを使用して状態をリセットする</span><span class="sxs-lookup"><span data-stu-id="31242-127">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="31242-128">現在の DbContext を破棄します</span><span class="sxs-lookup"><span data-stu-id="31242-128">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="31242-129">新しい DbContext を作成し、データベースからアプリケーションの状態を復元します。</span><span class="sxs-lookup"><span data-stu-id="31242-129">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="31242-130">最後の操作が正常に完了していない可能性があることをユーザーに通知します</span><span class="sxs-lookup"><span data-stu-id="31242-130">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="31242-131">オプション 3-トランザクションを手動で追跡する</span><span class="sxs-lookup"><span data-stu-id="31242-131">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="31242-132">トランザクションの状態を追跡するために使用するデータベースに、追跡されていないテーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="31242-132">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="31242-133">各トランザクションの開始時に、テーブルに行を挿入します。</span><span class="sxs-lookup"><span data-stu-id="31242-133">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="31242-134">コミット中に接続が失敗した場合は、データベース内の対応する行が存在するかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="31242-134">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     * <span data-ttu-id="31242-135">行が存在する場合は、トランザクションが正常にコミットされたため、通常どおり続行します。</span><span class="sxs-lookup"><span data-stu-id="31242-135">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     * <span data-ttu-id="31242-136">行が存在しない場合は、実行方法を使用して現在の操作を再試行します。</span><span class="sxs-lookup"><span data-stu-id="31242-136">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="31242-137">コミットが成功した場合は、対応する行を削除して、テーブルの増加を回避します。</span><span class="sxs-lookup"><span data-stu-id="31242-137">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="31242-138">[このブログ投稿](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) には、SQL Azure でこれを実現するためのサンプルコードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="31242-138">[This blog post](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) contains sample code for accomplishing this on SQL Azure.</span></span>  
