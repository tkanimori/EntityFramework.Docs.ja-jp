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
# <a name="handling-transaction-commit-failures"></a>トランザクションのコミット失敗の処理
> [!NOTE]
> **EF6.1 以降のみ**- このページで説明する機能、API などは、Entity Framework 6.1 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

6.1 の一部として、EF の新しい接続復元機能を導入しています: 一時的な接続の失敗がトランザクション コミットの受信確認に影響を与えたときに自動的に検出し、回復する機能。 シナリオの詳細については、ブログ記事[「SQL データベース接続」と「デンデポティニティ](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)問題」で説明します。  要約すると、シナリオは、トランザクションのコミット中に例外が発生したときに、次の 2 つの原因が考えられます。  

1. トランザクションのコミットがサーバーで失敗しました
2. トランザクションのコミットはサーバーで成功しましたが、接続の問題により、成功通知がクライアントに届かないようにしました  

最初の状況が発生した場合、アプリケーションまたはユーザーは操作を再試行できますが、2 番目の状況が発生した場合は再試行を回避し、アプリケーションが自動的に回復する可能性があります。 課題は、コミット中に例外が報告された実際の理由を検出する機能がなければ、アプリケーションは適切なアクション・コースを選択できないことです。 EF 6.1 の新機能により、トランザクションが成功した場合に EF がデータベースを再確認し、適切なアクションを透過的に実行できます。  

## <a name="using-the-feature"></a>機能の使用  

この機能を有効にするには **、DbConfiguration**のコンストラクターに[SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)の呼び出しを含める必要があります。 **DbConfiguration**についてよく知らない場合は、「[コード ベースの構成](~/ef6/fundamentals/configuring/code-based.md)」を参照してください。 この機能は、EF6 で導入された自動再試行と組み合わせて使用できます。  

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

## <a name="how-transactions-are-tracked"></a>トランザクションの追跡方法  

この機能が有効になると、EF は新しいテーブルを **__Transactions**と呼ばれるデータベースに自動的に追加します。 トランザクションが EF によって作成されるたびに、このテーブルに新しい行が挿入され、コミット中にトランザクションの失敗が発生した場合にその行が存在するかどうかがチェックされます。  

EF は、不要になったテーブルから行を削除するために最善の努力をしますが、アプリケーションが途中で終了するとテーブルが大きくなる可能性があり、そのためテーブルを手動で削除する必要がある場合があります。  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>以前のバージョンでコミットエラーを処理する方法

EF 6.1 より前の EF 製品でコミットエラーを処理するメカニズムはありませんでした。 この状況に対処するには、EF6 の以前のバージョンに適用できるいくつかの方法があります。  

* オプション 1 - 何もしない  

  トランザクションコミット中に接続が失敗する可能性は低いため、この条件が実際に発生した場合にアプリケーションが失敗しても問題ありません。  

* オプション 2 - データベースを使用して状態をリセットする  

  1. 現在の Db コンテキストを破棄します。  
  2. 新しい DbContext を作成し、データベースからアプリケーションの状態を復元します。  
  3. 最後の操作が正常に完了していない可能性があることをユーザーに通知します。  

* オプション 3 - トランザクションを手動で追跡する  

  1. トランザクションの状態を追跡するために使用されるデータベースに、追跡されていないテーブルを追加します。  
  2. 各トランザクションの先頭にあるテーブルに行を挿入します。  
  3. コミット中に接続が失敗した場合は、データベース内に対応する行が存在するかどうか確認します。  
     - 行が存在する場合は、トランザクションが正常にコミットされた後、通常どおり続行します。  
     - 行が存在しない場合は、実行方法を使用して現在の操作を再試行します。  
  4. コミットが成功した場合は、対応する行を削除して、表の増大を回避します。  

[このブログの投稿](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)には、SQL Azure でこれを実現するためのサンプル コードが含まれています。  
