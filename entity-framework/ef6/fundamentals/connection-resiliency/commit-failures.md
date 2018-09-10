---
title: トランザクション コミットの失敗 - EF6 の処理
author: divega
ms.date: 2016-10-23
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: f912777104c2e925122c05046d4d65660de8b8a8
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44250862"
---
# <a name="handling-transaction-commit-failures"></a><span data-ttu-id="b3d85-102">トランザクションのコミット エラーの処理</span><span class="sxs-lookup"><span data-stu-id="b3d85-102">Handling transaction commit failures</span></span>
> [!NOTE]
> <span data-ttu-id="b3d85-103">**EF6.1 以降のみ**-機能、Api、Entity Framework 6.1 で導入されたなどのこのページで説明します。</span><span class="sxs-lookup"><span data-stu-id="b3d85-103">**EF6.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="b3d85-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="b3d85-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="b3d85-105">6.1 の一部として、EF の新しい接続の回復性機能を導入されています: 検出し、一時的な接続エラーがトランザクションのコミットの確認にどのように影響する場合に自動的に回復する機能。</span><span class="sxs-lookup"><span data-stu-id="b3d85-105">As part of 6.1 we are introducing a new connection resiliency feature for EF: the ability to detect and recover automatically when transient connection failures affect the acknowledgement of transaction commits.</span></span> <span data-ttu-id="b3d85-106">シナリオの詳細については、ブログの投稿で説明[SQL データベースの接続と、冪等性問題](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="b3d85-106">The full details of the scenario are best described in the blog post [SQL Database Connectivity and the Idempotency Issue](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx).</span></span>  <span data-ttu-id="b3d85-107">要約すると、シナリオは、トランザクションのコミット中に例外が発生したときに 2 つの原因が考えられます。</span><span class="sxs-lookup"><span data-stu-id="b3d85-107">In summary, the scenario is that when an exception is raised during a transaction commit there are two possible causes:</span></span>  

1. <span data-ttu-id="b3d85-108">サーバーでトランザクションのコミットに失敗しました</span><span class="sxs-lookup"><span data-stu-id="b3d85-108">The transaction commit failed on the server</span></span>
2. <span data-ttu-id="b3d85-109">サーバーでトランザクションのコミットが成功しましたが、接続の問題がクライアントに到達から成功通知できませんでした。</span><span class="sxs-lookup"><span data-stu-id="b3d85-109">The transaction commit succeeded on the server but a connectivity issue prevented the success notification from reaching the client</span></span>  

<span data-ttu-id="b3d85-110">アプリケーションまたはユーザーの 1 つ目の場合、操作を再試行できますが、2 番目の状況が発生した場合の再試行を避ける必要があり、アプリケーションが自動的に回復する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b3d85-110">When the first situation happens the application or the user can retry the operation, but when the second situation occurs retries should be avoided and the application could recover automatically.</span></span> <span data-ttu-id="b3d85-111">課題がない場合のアプリケーション アクションの適切なコースを選択することはできません、コミット時に報告された例外の実際の理由は何でしたかを検出する機能。</span><span class="sxs-lookup"><span data-stu-id="b3d85-111">The challenge is that without the ability to detect what was the actual reason an exception was reported during commit, the application cannot choose the right course of action.</span></span> <span data-ttu-id="b3d85-112">EF 6.1 で新しい機能は、トランザクションが成功し、適切な一連の措置を透過的に実行する場合、データベースを再確認する EF を使用できます。</span><span class="sxs-lookup"><span data-stu-id="b3d85-112">The new feature in EF 6.1 allows EF to double-check with the database if the transaction succeeded and take the right course of action transparently.</span></span>  

## <a name="using-the-feature"></a><span data-ttu-id="b3d85-113">機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="b3d85-113">Using the feature</span></span>  

<span data-ttu-id="b3d85-114">機能を有効にするためへの呼び出しを含める必要があります[SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)のコンス トラクター、 **DbConfiguration**します。</span><span class="sxs-lookup"><span data-stu-id="b3d85-114">In order to enable the feature you need include a call to [SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) in the constructor of your **DbConfiguration**.</span></span> <span data-ttu-id="b3d85-115">慣れていない場合**DbConfiguration**を参照してください[コード ベースの構成](~/ef6/fundamentals/configuring/code-based.md)します。</span><span class="sxs-lookup"><span data-stu-id="b3d85-115">If you are unfamiliar with **DbConfiguration**, see [Code Based Configuration](~/ef6/fundamentals/configuring/code-based.md).</span></span> <span data-ttu-id="b3d85-116">この機能は、トランザクションが実際には、一時的なエラーにより、サーバーにコミットされませんでしたが、この状況で役立つ、EF6 で導入された自動再試行と組み合わせて使用できます。</span><span class="sxs-lookup"><span data-stu-id="b3d85-116">This feature can be used in combination with the automatic retries we introduced in EF6, which help in the situation in which the transaction actually failed to commit on the server due to a transient failure:</span></span>  

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

## <a name="how-transactions-are-tracked"></a><span data-ttu-id="b3d85-117">トランザクションを追跡する方法</span><span class="sxs-lookup"><span data-stu-id="b3d85-117">How transactions are tracked</span></span>  

<span data-ttu-id="b3d85-118">機能を有効にすると、EF が自動的に追加、新しいテーブルと呼ばれるデータベースを **__Transactions**します。</span><span class="sxs-lookup"><span data-stu-id="b3d85-118">When the feature is enabled, EF will automatically add a new table to the database called **__Transactions**.</span></span> <span data-ttu-id="b3d85-119">新しい行は、EF によってトランザクションが作成され、その行がコミット中にトランザクション エラーが発生した場合の存在チェックするたびにこのテーブルに挿入されます。</span><span class="sxs-lookup"><span data-stu-id="b3d85-119">A new row is inserted in this table every time a transaction is created by EF and that row is checked for existence if a transaction failure occurs during commit.</span></span>  

<span data-ttu-id="b3d85-120">EF では、必要としなくなったらときに、テーブルから行を排除する最善の努力を行うは、テーブルは、アプリケーションは、途中で、そのため、場合によっては手動でテーブルを消去する必要がありますが終了した場合に拡張できます。</span><span class="sxs-lookup"><span data-stu-id="b3d85-120">Although EF will do a best effort to prune rows from the table when they aren’t needed anymore, the table can grow if the application exits prematurely and for that reason you may need to purge the table manually in some cases.</span></span>  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a><span data-ttu-id="b3d85-121">以前のバージョンとコミット エラーを処理する方法</span><span class="sxs-lookup"><span data-stu-id="b3d85-121">How to handle commit failures with previous Versions</span></span>

<span data-ttu-id="b3d85-122">EF 6.1 する前に、EF の製品でのコミット エラーを処理するためのメカニズムでした。</span><span class="sxs-lookup"><span data-stu-id="b3d85-122">Before EF 6.1 there was not mechanism to handle commit failures in the EF product.</span></span> <span data-ttu-id="b3d85-123">EF6 の以前のバージョンに適用できるこのような状況に対処する場合にいくつかの方法はあります。</span><span class="sxs-lookup"><span data-stu-id="b3d85-123">There are several ways to dealing with this situation that can be applied to previous versions of EF6:</span></span>  

* <span data-ttu-id="b3d85-124">オプション 1 - 何もしません。</span><span class="sxs-lookup"><span data-stu-id="b3d85-124">Option 1 - Do nothing</span></span>  

  <span data-ttu-id="b3d85-125">この状態が実際に発生した場合にだけが失敗するアプリケーションで許容される可能性があるために、トランザクションのコミット中に接続エラーが発生する可能性は低くなります。</span><span class="sxs-lookup"><span data-stu-id="b3d85-125">The likelihood of a connection failure during transaction commit is low so it may be acceptable for your application to just fail if this condition actually occurs.</span></span>  

* <span data-ttu-id="b3d85-126">オプション 2 - データベースを使用して状態をリセットするには</span><span class="sxs-lookup"><span data-stu-id="b3d85-126">Option 2 - Use the database to reset state</span></span>  

  1. <span data-ttu-id="b3d85-127">現在の DbContext を破棄します。</span><span class="sxs-lookup"><span data-stu-id="b3d85-127">Discard the current DbContext</span></span>  
  2. <span data-ttu-id="b3d85-128">新しい DbContext を作成し、データベースからのアプリケーションの状態を復元</span><span class="sxs-lookup"><span data-stu-id="b3d85-128">Create a new DbContext and restore the state of your application from the database</span></span>  
  3. <span data-ttu-id="b3d85-129">最後の操作が完了していない正常にユーザーに通知します。</span><span class="sxs-lookup"><span data-stu-id="b3d85-129">Inform the user that the last operation might not have been completed successfully</span></span>  

* <span data-ttu-id="b3d85-130">オプション 3 は、トランザクションを手動で追跡</span><span class="sxs-lookup"><span data-stu-id="b3d85-130">Option 3 - Manually track the transaction</span></span>  

  1. <span data-ttu-id="b3d85-131">データベースのトランザクションの状態を追跡するために使用するには、非追跡テーブルを追加します。</span><span class="sxs-lookup"><span data-stu-id="b3d85-131">Add a non-tracked table to the database used to track the status of the transactions.</span></span>  
  2. <span data-ttu-id="b3d85-132">各トランザクションの先頭にあるテーブルに行を挿入します。</span><span class="sxs-lookup"><span data-stu-id="b3d85-132">Insert a row into the table at the beginning of each transaction.</span></span>  
  3. <span data-ttu-id="b3d85-133">コミット中に、接続に失敗した場合、データベース内の対応する行の存在を確認します。</span><span class="sxs-lookup"><span data-stu-id="b3d85-133">If the connection fails during the commit, check for the presence of the corresponding row in the database.</span></span>  
     - <span data-ttu-id="b3d85-134">行が存在する場合は、正常に続行、トランザクションが正常にコミットされたと</span><span class="sxs-lookup"><span data-stu-id="b3d85-134">If the row is present, continue normally, as the transaction was committed successfully</span></span>  
     - <span data-ttu-id="b3d85-135">行が存在しない場合は、実行戦略を使用して、現在の操作を再試行してください。</span><span class="sxs-lookup"><span data-stu-id="b3d85-135">If the row is absent, use an execution strategy to retry the current operation.</span></span>  
  4. <span data-ttu-id="b3d85-136">コミットが成功した場合は、テーブルの増加を回避するために、対応する行を削除します。</span><span class="sxs-lookup"><span data-stu-id="b3d85-136">If the commit is successful, delete the corresponding row to avoid the growth of the table.</span></span>  

<span data-ttu-id="b3d85-137">[このブログの投稿](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)SQL Azure でこれを実現するためのサンプル コードが含まれています。</span><span class="sxs-lookup"><span data-stu-id="b3d85-137">[This blog post](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx) contains sample code for accomplishing this on SQL Azure.</span></span>  
