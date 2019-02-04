---
title: 接続の回復性と再試行ロジック - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 47d68ac1-927e-4842-ab8c-ed8c8698dff2
ms.openlocfilehash: 7d6aa870cc32a2b344457fbb04525a7c2c8d1c61
ms.sourcegitcommit: 159c2e9afed7745e7512730ffffaf154bcf2ff4a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2019
ms.locfileid: "55668766"
---
# <a name="connection-resiliency-and-retry-logic"></a>接続の回復性と再試行ロジック
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

データベース サーバーに接続するアプリケーションは、バックエンドの障害やネットワークが不安定になるのため、接続の区切りに脆弱性のある常にされました。 ただし、専用のデータベース サーバーに対して作業を行う、LAN ベースの環境でこれらのエラーはまれをこれらのエラーを処理する追加のロジックは多くの場合、必要ありません。 クラウドの登場により、多くの接続の中断が発生する場合、信頼性の低いネットワーク経由で Windows Azure SQL Database などのデータベース サーバーと接続ベース。 クラウド サービスや断続的なタイムアウトおよびその他の一時的なエラーの原因とネットワークが不安定になる接続のスロットル調整などの公平性を保つためにデータベース使用の防テクニックが考えられます。  

接続の回復性は、これらの接続の中断が原因で失敗する任意のコマンドを自動的に再試行する EF の機能を指します。  

## <a name="execution-strategies"></a>実行戦略  

接続の再試行はの IDbExecutionStrategy インターフェイスの実装によって処理されます。 IDbExecutionStrategy の実装は、操作を受け入れると、例外が発生する場合、再試行が適切なかどうかを判断してがある場合の再試行の責任になります。 これには EF に付属する 4 つの実行方法があります。  

1. **DefaultExecutionStrategy**: この実行方法は、すべての操作を再試行しません、sql server 以外のデータベースの既定値になります。  
2. **DefaultSqlExecutionStrategy**: これは既定で使用される内部実行戦略です。 この方法はまったく再試行されず、ただし、接続の回復を有効にすることがユーザーに通知する一時的な可能性がある例外がラップされます。  
3. **DbExecutionStrategy**: このクラスは基底クラスとして、独自のカスタムのものを含む、その他の実行戦略に適しています。 最初の再試行が行われる場所 0 遅延と遅延が増加指数関数的に最大再試行回数がヒットするまで、指数再試行ポリシーを実装します。 このクラスには、どの例外を再試行するかを制御するための派生実行戦略を実装できる抽象 ShouldRetryOn メソッドがあります。  
4. **SqlAzureExecutionStrategy**: この実行戦略が DbExecutionStrategy から継承し、Azure SQL Database を使用する場合に一時的な可能性があることがわかっている例外で再試行されます。

> [!NOTE]
> 実行戦略 2 と 4 では、EntityFramework.SqlServer アセンブリでは、EF に付属する Sql Server プロバイダーに含まれ、SQL Server で動作するように設計されます。  

## <a name="enabling-an-execution-strategy"></a>実行戦略を有効にします。  

EF 実行戦略を使用してに指示する最も簡単な方法は、の SetExecutionStrategy メソッドでは、 [DbConfiguration](~/ef6/fundamentals/configuring/code-based.md)クラス。  

``` csharp
public class MyConfiguration : DbConfiguration
{
    public MyConfiguration()
    {
        SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
    }
}
```  

このコードは、SQL Server に接続するときに、SqlAzureExecutionStrategy を使用する EF に指示します。  

## <a name="configuring-the-execution-strategy"></a>実行戦略を構成します。  

SqlAzureExecutionStrategy のコンス トラクターは、MaxRetryCount と MaxDelay の 2 つのパラメーターを受け取ることができます。 MaxRetry 数は、戦略が再試行する回数の最大数です。 MaxDelay は、実行戦略を使用する再試行間の最大遅延を表す TimeSpan です。  

再試行の最大数を 1 および 30 秒の遅延の上限に設定するには、execue、次の場合。  

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

一時的なエラーが発生した場合、これはか最大再試行制限まで、各再試行間になった遅延は、最初の時間を超えたまたは時間の合計最大遅延をヒットするすぐに、SqlAzureExecutionStrategy を再試行します。  

実行戦略は tansient では、通常、例外の数に制限を再試行してのみ、他のエラーと解決するのにはエラーが一時的でないか、時間がかかりすぎる場合 RetryLimitExceeded 例外をキャッチするを処理する必要があります。自体。  

再試行実行戦略を使用する場合は、いくつかの既知の制限事項があります。  

## <a name="streaming-queries-are-not-supported"></a>ストリーミング クエリがサポートされていません  

既定では、EF6 とそれ以降のバージョンはそれらをストリーミングするのではなく、クエリの結果が可能ではバッファーです。 結果ストリームを用意する場合 AsStreaming メソッドを使用してストリーミングするエンティティのクエリに LINQ を変更することができます。  

``` csharp
using (var db = new BloggingContext())
{
    var query = (from b in db.Blogs
                orderby b.Url
                select b).AsStreaming();
    }
}
```  

再試行実行戦略を登録すると、ストリーミングはサポートされていません。 返される結果を接続が途中削除でしたので、この制限が存在します。 この場合、EF がクエリ全体を再実行する必要がありますが既に返された結果を知ることの確実な方法がありません (データが変更されている最初のクエリが送信された、結果が異なる順序で返される可能性があります、結果は、一意識別子がないため、など。)。  

## <a name="user-initiated-transactions-are-not-supported"></a>ユーザーが開始したトランザクションはサポートされていません  

再試行回数になる実行戦略を構成しているときにトランザクションの使用に関するいくつかの制限があります。  

既定では、EF は、トランザクション内ですべてのデータベース更新を実行します。 これを有効にすることが何もする必要はありませんが、EF は、自動的にこれは常に。  

たとえば、次のコードで SaveChanges が自動的に実行のトランザクション内で。 SaveChanges 後、トランザクションをロールバックは、新しいサイトのいずれかの挿入と変更がデータベースに適用せずに失敗する場合。 コンテキストのまま変更を適用する再試行をもう一度呼び出す SaveChanges を許可する状態にします。  

``` csharp
using (var db = new BloggingContext())
{
    db.Blogs.Add(new Site { Url = "http://msdn.com/data/ef" });
    db.Blogs.Add(new Site { Url = "http://blogs.msdn.com/adonet" });
    db.SaveChanges();
}
```  

再試行実行戦略を使用しない場合は、単一のトランザクションで複数の操作をラップできます。 たとえば、次のコードでは、単一のトランザクションで 2 つの SaveChanges 呼び出しをラップします。 いずれかの操作の一部が失敗した場合、どの変更が適用されます。  

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

EF が認識し、それ以前の操作を再試行する方法がないために、再試行実行戦略を使用する場合、この操作はサポートされません。 たとえば、2 つ目の SaveChanges が失敗した場合、EF されなくが最初の SaveChanges 呼び出しを再試行する必要な情報」とあります。  

### <a name="workaround-suspend-execution-strategy"></a>回避策:実行戦略を中断します。  

1 つの考えられる回避策は、ユーザーを使用する必要があるコードの一部の再試行実行戦略を中断するトランザクションを開始します。 これを行う最も簡単な方法では、コードに SuspendExecutionStrategy フラグは、構成クラスをベースし、フラグが設定されている場合、既定の (非 retying) 実行戦略を返す実行戦略のラムダの変更を追加します。  

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
                return (bool?)CallContext.LogicalGetData("SuspendExecutionStrategy") ?? false;
            }
            set
            {
                CallContext.LogicalSetData("SuspendExecutionStrategy", value);
            }
        }
    }
}
```  

フラグの値を格納する CallContext 使用していることに注意してください。 これにより、スレッド ローカル ストレージと同様の機能を提供しますが、非同期クエリを含む - 非同期コードを使用して Entity Framework で保存しても安全です。  

ユーザーが開始したトランザクションを使用するコードのセクションの実行戦略を中断しましたできますようになりました。  

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

### <a name="workaround-manually-call-execution-strategy"></a>回避策:手動で実行戦略を呼び出す  

手動で実行戦略を使用し、セット全体は、実行ロジックのいずれかの操作が失敗した場合に再試行すべてできるようにする別の方法です。 技法を使用して、実行戦略を中断する必要がありますの上に表示されるため、再試行可能なコード ブロック内で使用される任意のコンテキストは再試行しようとはしないでください。  

再試行するコード ブロック内でコンテキストを作成することに注意してください。 これにより、各再試行のクリーンな状態で開始します。  

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
