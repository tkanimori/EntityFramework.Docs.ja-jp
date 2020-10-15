---
title: トランザクションのコミットエラーの処理-EF6
description: Entity Framework 6 でのトランザクションのコミットエラーの処理
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-resiliency/commit-failures
ms.openlocfilehash: ecc9daf568f12e3bf93af8c93e543c12dfddbb06
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065590"
---
# <a name="handling-transaction-commit-failures"></a>トランザクションのコミットエラーの処理

> [!NOTE]
> **Ef 6.1 以降のみ** -このページで説明した機能、api などが Entity Framework 6.1 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

6.1 の一部として、EF の新しい接続回復性機能が導入されています。これは、一時的な接続エラーがトランザクションのコミットの受信確認に影響する場合に、自動的に検出して回復する機能です。 このシナリオの詳細については、ブログ SQL Database 記事「 [接続とべき等の問題](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)」を参照してください。  つまり、このシナリオでは、トランザクションのコミット中に例外が発生すると、次の2つの原因が考えられます。  

1. サーバーでトランザクションのコミットに失敗しました
2. サーバーでトランザクションのコミットに成功しましたが、接続の問題により成功通知がクライアントに到達しませんでした  

最初の状況が発生した場合、アプリケーションまたはユーザーは操作を再試行できますが、2番目の状況が発生した場合は、再試行を避け、アプリケーションを自動的に回復できます。 課題は、コミット中に例外が報告された実際の理由を検出する機能がないということです。アプリケーションは適切な措置を選択できません。 EF 6.1 の新機能では、トランザクションが成功した場合に EF がデータベースを再確認し、適切な操作を透過的に行うことができます。  

## <a name="using-the-feature"></a>機能の使用  

この機能を有効にするには、 **Dbconfiguration**のコンストラクターに[settransactionhandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)を呼び出す必要があります。 **Dbconfiguration**の詳細については、「[コードベースの構成](xref:ef6/fundamentals/configuring/code-based)」を参照してください。 この機能は、EF6 で導入された自動再試行と組み合わせて使用できます。これは、一時的な障害により、トランザクションが実際にサーバー上でコミットに失敗した状況に役立ちます。  

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

この機能を有効にすると、EF によって **__Transactions**というデータベースに新しいテーブルが自動的に追加されます。 EF によってトランザクションが作成されるたびに、このテーブルに新しい行が挿入されます。コミット中にトランザクションエラーが発生した場合は、その行が存在するかどうかがチェックされます。  

EF は、不要になったテーブルから行を排除するのに最適な作業を行いますが、アプリケーションが途中で終了した場合にテーブルを拡張できます。そのため、場合によっては、テーブルを手動で消去する必要があります。  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>以前のバージョンのコミットエラーを処理する方法

EF 6.1 より前では、EF 製品のコミットエラーを処理するメカニズムがありませんでした。 以前のバージョンの EF6 に適用できるこのような状況に対処するには、いくつかの方法があります。  

* オプション 1-何もしない  

  トランザクションのコミット中に接続エラーが発生する可能性は低いため、この状態が実際に発生した場合にアプリケーションが失敗する可能性があります。  

* オプション 2-データベースを使用して状態をリセットする  

  1. 現在の DbContext を破棄します  
  2. 新しい DbContext を作成し、データベースからアプリケーションの状態を復元します。  
  3. 最後の操作が正常に完了していない可能性があることをユーザーに通知します  

* オプション 3-トランザクションを手動で追跡する  

  1. トランザクションの状態を追跡するために使用するデータベースに、追跡されていないテーブルを追加します。  
  2. 各トランザクションの開始時に、テーブルに行を挿入します。  
  3. コミット中に接続が失敗した場合は、データベース内の対応する行が存在するかどうかを確認します。  
     * 行が存在する場合は、トランザクションが正常にコミットされたため、通常どおり続行します。  
     * 行が存在しない場合は、実行方法を使用して現在の操作を再試行します。  
  4. コミットが成功した場合は、対応する行を削除して、テーブルの増加を回避します。  

[このブログ投稿](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) には、SQL Azure でこれを実現するためのサンプルコードが含まれています。  
