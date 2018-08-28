---
title: トランザクション コミットの失敗 - EF6 の処理
author: divega
ms.date: 2016-10-23
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: a22a651851bc46e2bf1fe652b3b9a921ec22b70b
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996838"
---
# <a name="handling-transaction-commit-failures"></a>トランザクションのコミット エラーの処理
> [!NOTE]
> **EF6.1 以降のみ**-機能、Api、Entity Framework 6.1 で導入されたなどのこのページで説明します。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

6.1 の一部として、EF の新しい接続の回復性機能を導入されています: 検出し、一時的な接続エラーがトランザクションのコミットの確認にどのように影響する場合に自動的に回復する機能。 シナリオの詳細については、ブログの投稿で説明[SQL データベースの接続と、冪等性問題](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)します。  要約すると、シナリオは、トランザクションのコミット中に例外が発生したときに 2 つの原因が考えられます。  

1. サーバーでトランザクションのコミットに失敗しました
2. サーバーでトランザクションのコミットが成功しましたが、接続の問題がクライアントに到達から成功通知できませんでした。  

アプリケーションまたはユーザーの 1 つ目の場合、操作を再試行できますが、2 番目の状況が発生した場合の再試行を避ける必要があり、アプリケーションが自動的に回復する可能性があります。 課題がない場合のアプリケーション アクションの適切なコースを選択することはできません、コミット時に報告された例外の実際の理由は何でしたかを検出する機能。 EF 6.1 で新しい機能は、トランザクションが成功し、適切な一連の措置を透過的に実行する場合、データベースを再確認する EF を使用できます。  

## <a name="using-the-feature"></a>機能を使用します。  

機能を有効にするためへの呼び出しを含める必要があります[SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)のコンス トラクター、 **DbConfiguration**します。 慣れていない場合**DbConfiguration**を参照してください[コード ベースの構成](~/ef6/fundamentals/configuring/code-based.md)します。 この機能は、トランザクションが実際には、一時的なエラーにより、サーバーにコミットされませんでしたが、この状況で役立つ、EF6 で導入された自動再試行と組み合わせて使用できます。  

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

## <a name="how-transactions-are-tracked"></a>トランザクションを追跡する方法  

機能を有効にすると、EF が自動的に追加、新しいテーブルと呼ばれるデータベースを **__Transactions**します。 新しい行は、EF によってトランザクションが作成され、その行がコミット中にトランザクション エラーが発生した場合の存在チェックするたびにこのテーブルに挿入されます。  

EF では、必要としなくなったらときに、テーブルから行を排除する最善の努力を行うは、テーブルは、アプリケーションは、途中で、そのため、場合によっては手動でテーブルを消去する必要がありますが終了した場合に拡張できます。  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>以前のバージョンとコミット エラーを処理する方法

EF 6.1 する前に、EF の製品でのコミット エラーを処理するためのメカニズムでした。 EF6 の以前のバージョンに適用できるこのような状況に対処する場合にいくつかの方法はあります。  

### <a name="option-1---do-nothing"></a>オプション 1 - 何もしません。  

この状態が実際に発生した場合にだけが失敗するアプリケーションで許容される可能性があるために、トランザクションのコミット中に接続エラーが発生する可能性は低くなります。  

## <a name="option-2---use-the-database-to-reset-state"></a>オプション 2 - データベースを使用して状態をリセットするには  

1. 現在の DbContext を破棄します。  
2. 新しい DbContext を作成し、データベースからのアプリケーションの状態を復元  
3. 最後の操作が完了していない正常にユーザーに通知します。  

## <a name="option-3---manually-track-the-transaction"></a>オプション 3 は、トランザクションを手動で追跡  

1. データベースのトランザクションの状態を追跡するために使用するには、非追跡テーブルを追加します。  
2. 各トランザクションの先頭にあるテーブルに行を挿入します。  
3. コミット中に、接続に失敗した場合、データベース内の対応する行の存在を確認します。  
    - 行が存在する場合は、正常に続行、トランザクションが正常にコミットされたと  
    - 行が存在しない場合は、実行戦略を使用して、現在の操作を再試行してください。  
4. コミットが成功した場合は、テーブルの増加を回避するために、対応する行を削除します。  

[このブログの投稿](http://blogs.msdn.com/b/adonet/archive/2013/03/11/sql-database-connectivity-and-the-idempotency-issue.aspx)SQL Azure でこれを実現するためのサンプル コードが含まれています。  
