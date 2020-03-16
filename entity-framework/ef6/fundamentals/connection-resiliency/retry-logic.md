---
title: 接続の回復性と再試行ロジック-EF6
author: AndriySvyryd
ms.date: 11/20/2019
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
ms.openlocfilehash: 50e65bed32d0cfcf42746da0d632f9e990424b97
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2020
ms.locfileid: "79402109"
---
# <a name="connection-resiliency-and-retry-logic"></a>接続の回復性と再試行ロジック
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

データベースサーバーに接続するアプリケーションは、バックエンドの障害やネットワークの不安定性によって接続の中断に対して常に脆弱になります。 ただし、専用のデータベースサーバーに対して動作する LAN ベースの環境では、これらのエラーが発生することはほとんどありません。これらのエラーを処理するための追加のロジックが必要になることはほとんどありません。 Windows Azure SQL Database などのクラウドベースのデータベースサーバーや信頼性の低いネットワークでの接続の増加により、接続の中断がより一般的になっています。 これは、クラウドデータベースがサービスの公平性を確保するために使用する防御手法 (接続の調整など)、または断続的なタイムアウトやその他の一時的なエラーの原因となるネットワークの不安定性を保証するために発生する可能性があります。  

接続の回復性とは、EF が、これらの接続の中断によって失敗したコマンドを自動的に再試行する機能のことです。  

## <a name="execution-strategies"></a>実行方法  

接続の再試行は、IDbExecutionStrategy インターフェイスの実装によって行われます。 IDbExecutionStrategy の実装では、操作を受け入れる必要があります。例外が発生した場合は、再試行が適切かどうかを判断し、実行中であるかどうかを確認します。 EF には、次の4つの実行戦略があります。  

1. **Defaultexecutionstrategy**: この実行方法では、操作は再試行されません。 sql server 以外のデータベースの既定値です。  
2. **Defaultsqlexecutionstrategy**: これは、既定で使用される内部実行戦略です。 ただし、この方法を使用しても再試行されることはありませんが、一時的な例外をラップして、接続の回復性を有効にすることをユーザーに通知することができます。  
3. **Dbexecutionstrategy**: このクラスは、他の実行戦略 (独自のカスタム属性を含む) の基底クラスとして適しています。 指数的な再試行ポリシーを実装します。最初の再試行はゼロの遅延が発生し、遅延は最大再試行回数に達するまで指数関数的に増加します。 このクラスには、派生された実行方法で実装できる抽象型のメソッドがあり、どの例外を再試行するかを制御できます。  
4. **SqlAzureExecutionStrategy**: この実行戦略は DbExecutionStrategy から継承され、Azure SQL Database を操作するときに一時的である可能性がある例外に対して再試行します。

> [!NOTE]
> 実行戦略2および4は、EF に付属している Sql Server プロバイダーに含まれています。これは、EntityFramework. SqlServer アセンブリにあり、SQL Server で動作するように設計されています。  

## <a name="enabling-an-execution-strategy"></a>実行戦略を有効にする  

実行戦略を使用するように EF に指示する最も簡単な方法は、 [Dbconfiguration](~/ef6/fundamentals/configuring/code-based.md)クラスの setexecutionstrategy メソッドを使用することです。  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

このコードは、SQL Server に接続するときに SqlAzureExecutionStrategy を使用するように EF に指示します。  

## <a name="configuring-the-execution-strategy"></a>実行戦略の構成  

SqlAzureExecutionStrategy のコンストラクターは、MaxRetryCount と MaxDelay の2つのパラメーターを受け取ることができます。 MaxRetry count は、戦略が再試行する最大回数です。 MaxDelay は、実行戦略が使用する再試行間の最大遅延時間を表す TimeSpan です。  

再試行の最大回数を1に設定し、最大遅延を30秒に設定するには、次のように実行します。  

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

SqlAzureExecutionStrategy は、一時的なエラーが最初に発生したときに直ちに再試行されますが、最大再試行回数を超えた場合、または合計時間が最大遅延に達した場合は、再試行の間隔が長くなります。  

実行戦略は、限られた数の例外のみを再試行します。通常は一時的なもので、他のエラーも処理し、エラーが一時的でない場合や、解決に時間がかかりすぎる場合に RetryLimitExceeded 例外をキャッチする必要があります。自分自身.  

再試行の実行方法を使用する場合、いくつかの制限があります。  

## <a name="streaming-queries-are-not-supported"></a>ストリーミングクエリはサポートされていません  

既定では、EF6 以降のバージョンでは、クエリ結果をストリーミングするのではなく、バッファーに格納します。 結果をストリーミングする場合は、AsStreaming メソッドを使用して、LINQ to Entities クエリをストリーミングに変更できます。  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

再試行の実行戦略が登録されている場合、ストリーミングはサポートされません。 この制限が存在するのは、返される結果に対して接続が部分的に削除される可能性があるためです。 この場合、EF はクエリ全体を再実行する必要がありますが、既に返された結果を知るための信頼できる方法はありません (最初のクエリが送信された後にデータが変更された可能性があり、結果が別の順序で返され、結果に一意の識別子がない可能性があります)など)。  

## <a name="user-initiated-transactions-are-not-supported"></a>ユーザーが開始したトランザクションはサポートされていません  

再試行を行う実行方法を構成した場合、トランザクションの使用に関していくつかの制限があります。  

既定では、EF はトランザクション内で任意のデータベースの更新を実行します。 これを有効にするために何もする必要はありません。 EF は常にこれを自動的に実行します。  

たとえば、次のコードでは、SaveChanges はトランザクション内で自動的に実行されます。 新しいサイトのいずれかを挿入した後に SaveChanges が失敗した場合、トランザクションはロールバックされ、変更はデータベースに適用されません。 コンテキストも、変更の適用を再試行するために SaveChanges を再度呼び出すことができる状態のままです。  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

再試行の実行戦略を使用しない場合は、複数の操作を1つのトランザクションにラップできます。 たとえば、次のコードでは、1つのトランザクションで2つの SaveChanges 呼び出しをラップしています。 いずれかの操作が失敗した場合、変更は適用されません。  

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

これは、EF が以前の操作を認識しておらず、再試行する方法が認識されないため、再試行の実行方法を使用する場合はサポートされません。 たとえば、2番目の SaveChanges が失敗した場合、EF は最初の SaveChanges 呼び出しを再試行するために必要な情報を持っていません。  

### <a name="solution-manually-call-execution-strategy"></a>解決策: 実行方法を手動で呼び出す  

これを解決するには、実行方法を手動で使用し、実行するロジックのセット全体を指定します。これにより、いずれかの操作が失敗した場合にすべての操作を再試行できます。 DbExecutionStrategy から派生した実行戦略が実行されている場合は、SaveChanges で使用される暗黙的な実行戦略が中断されます。  

再試行するコードブロック内にコンテキストを構築する必要があることに注意してください。 これにより、再試行のたびにクリーンな状態で開始されます。  

``` csharp
var executionStrategy = new SqlAzureExecutionStrategy();

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
```  
