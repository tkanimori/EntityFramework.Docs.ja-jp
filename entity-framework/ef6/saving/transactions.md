---
title: トランザクション - EF6 の使用
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 0d0f1824-d781-4cb3-8fda-b7eaefced1cd
caps.latest.revision: 3
ms.openlocfilehash: 4238c88cc149458ed11b96a0bf9aaed9aac40b2d
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122792"
---
# <a name="working-with-transactions"></a>トランザクションの使用
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

このドキュメントでは、トランザクションの操作を簡単に EF5 から追加しました機能強化を含め、EF6 でトランザクションを使用して説明します。  

## <a name="what-ef-does-by-default"></a>既定では EF の動作  

すべてのバージョンの Entity Framework では、実行するたびに**SaveChanges()** 挿入、更新、またはフレームワーク、データベースの削除が操作トランザクションでラップします。 このトランザクションは、操作を実行する間だけ持続し、し完了します。 このような別の操作を実行するときに新しいトランザクションが開始されます。  

EF6 で始まる**Database.ExecuteSqlCommand()** 既定ではラップのコマンドは、トランザクションに存在する 1 つがない場合は。 希望する場合は、この動作をオーバーライドすることをこのメソッドのオーバー ロードがあります。 などの Api を使用して、モデルに含まれるストアド プロシージャの実行を EF6 にも**ObjectContext.ExecuteFunction()** も同じ (既定の動作、現時点ではオーバーライドできません)。  

どちらの場合、トランザクションの分離レベルは、分離レベルはどのようなデータベース プロバイダーは既定値に設定します。 既定では、たとえば、SQL Server これは READ COMMITTED です。  

Entity Framework では、トランザクションでクエリをラップしません。  

この既定の機能は、多くのユーザーとかどうかため必要はありません。 EF6 で何かするのに適しています常に行ったのとは、コードを記述だけです。  

ただし、トランザクションをより細かく制御が必要なユーザー – これについては、次のセクションで説明します。  

## <a name="how-the-apis-work"></a>Api のしくみ  

Entity Framework の EF6 の前に、(によって例外が既に開いている接続が渡された場合)、データベース接続自体を開くときにペダルします。 ユーザーがいくつかの操作を 1 つのトランザクションをラップする唯一の方法を使用するいずれかがつまり、トランザクションは、開いている接続でのみ開始できます、ので、 [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx)を使用して、または、 **ObjectContext.Connection**プロパティと開始呼び出し**Open()** と**BeginTransaction()** 、返された上で直接**EntityConnection**オブジェクト。 さらに、独自の基になるデータベース接続でトランザクションを開始していた場合、データベースに接続する API 呼び出しは失敗します。  

> [!NOTE]
> 閉じられた接続の受け入れのみの制限は、Entity Framework 6 で削除されました。 詳細については、次を参照してください。[接続管理](~/ef6/fundamentals/connection-management.md)します。  

これで、フレームワークを ef6 開始を提供します。  

1. **Database.BeginTransaction()** : を簡単にユーザーを起動し、同じトランザクション内で結合するいくつかの操作を行えるように、既存の DbContext – 内トランザクション自体を完了するため、すべてのコミットされたまたはすべていずれかのロール。 また、ユーザーがより簡単に、トランザクションの分離レベルを指定することもできます。  
2. **Database.UseTransaction()** : Entity Framework の外部で開始されたトランザクションを使用する DbContext ことができます。  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a>同じコンテキスト内で 1 つのトランザクションにいくつかの操作を組み合わせること  

**Database.BeginTransaction()** が 2 つの上書き: 1 つは、明示的な[IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx)といずれかの引数はありませんし、基になるデータベース プロバイダーから IsolationLevel の既定値を使用します。 両方のオーバーライドを返す、 **DbContextTransaction**オブジェクトを提供する**Commit()** と**Rollback()** 基になるストアにコミットとロールバックを実行する方法トランザクションです。  

**DbContextTransaction**はコミットまたはロールバック後に破棄するためのものです。 簡単にこれを実現方法の 1 つは、 **using(...){…}** 自動的に呼び出されます構文**Dispose()** 完了ブロックを使用してする場合。  

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
> トランザクションを開始するには、基になるストア接続が開かれている必要があります。 Database.BeginTransaction() を呼び出すのでが開かれていない場合は、接続が開かれます。 DbContextTransaction には、接続が開かれている場合、閉じられます、Dispose() が呼び出されるとします。  

### <a name="passing-an-existing-transaction-to-the-context"></a>既存のトランザクションをコンテキストに渡す  

場合がありますも広範なスコープでは完全に同じデータベースでは、EF の外部での操作を含む、トランザクションを作成するとします。 これを実現するには、接続を開いてしと自分でトランザクションを開始をし)、データベースを既に開いている接続を使用して、その接続で、既存のトランザクションを使用するには b) の EF を指示する必要があります。  

これを行うには、定義し、ブール i) 既存の接続パラメーターと contextOwnsConnection ii) を実行する DbContext コンス トラクターのいずれかから継承され、コンテキスト クラスのコンス トラクターを使用する必要があります。  

> [!NOTE]
> ContextOwnsConnection フラグは、このシナリオで呼び出される場合は false に設定する必要があります。 これは、Entity Framework に通知を閉じることはできません、接続には、そのときに重要です。 (たとえば、次の 4 行目を参照)。  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

さらに、自分で (既定の設定を回避する場合、IsolationLevel を含む)、トランザクションを開始して、既存のトランザクション接続を既に開始されている Entity Framework を使用 (33 以下の行を参照してください)。  

SqlConnection 自体で直接、または、DbContext でデータベース操作を実行されます。 このようなすべての操作は、1 つのトランザクション内で実行されます。 責任のコミットやトランザクションをロールバックしで Dispose() を呼び出すため、およびを閉じると、データベース接続の破棄を行います。 例えば:  

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

### <a name="clearing-up-the-transaction"></a>トランザクションをオフにします。

Null を渡す Database.UseTransaction() を現在のトランザクションの Entity Framework のナレッジをオフにすることができます。 Entity Framework は、どちらもコミットもこれを行うときに、既存のトランザクションのロールバックがので注意して使用し、これは、実行することを確認している場合にのみになります。  

### <a name="errors-in-usetransaction"></a>[いいえ] のエラー

トランザクションを渡す場合 Database.UseTransaction() から例外が表示される場合。  
- Entity Framework が既に既存のトランザクション  
- Entity Framework は既に、TransactionScope 内で動作しています。  
- 渡されたトランザクション内の接続オブジェクトが null です。 つまり、トランザクションは接続に関連付けられていない – 通常、これはそのトランザクションが既に完了したサインイン  
- 渡されたトランザクション内の接続オブジェクトでは、Entity Framework の接続は一致しません。  

## <a name="using-transactions-with-other-features"></a>他の機能とトランザクションの使用  

このセクションでは、上記のトランザクションと対話する方法についてください。  

- 接続の復元性  
- 非同期メソッド  
- TransactionScope トランザクション  

### <a name="connection-resiliency"></a>接続の復元性  

新しい接続の回復性機能には、ユーザーによって開始されたトランザクションでは使えません。 詳細については、次を参照してください。[再試行実行戦略の制限事項](~/ef6/fundamentals/connection-resiliency/retry-logic.md#limitations)します。  

### <a name="asynchronous-programming"></a>非同期プログラミング  

前のセクションで説明されているアプローチには、それ以上のオプションや設定を使用する必要ありません、[非同期クエリを実行し、メソッドの保存](~/ef6/fundamentals/async.md
)します。 行う動作によって、非同期メソッド内で、この可能性 – デッドロックまたはブロックは、アプリケーション全体のパフォーマンスを低下が発生することがさらに実行時間の長いトランザクションに注意してください。  

### <a name="transactionscope-transactions"></a>TransactionScope トランザクション  

EF6 の前によりも大きなスコープのトランザクションを提供することをお勧めの方法は、TransactionScope オブジェクトを使用してでした。  

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

SqlConnection と Entity Framework し、TransactionScope アンビエント トランザクションを使用して、したがって一緒にコミットします。  

以降では、.NET 4.5.1 も使用して非同期のメソッドを使用する TransactionScope が更新されました、 [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx)列挙体。  

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

TransactionScope アプローチをいくつかの制限は引き続きがあります。  

- .NET 4.5.1 が必要以上の非同期メソッドを使用します。  
- 1 つだけの接続があることを確認する場合を除いてには、クラウドのシナリオで使用することはできません (クラウドのシナリオでは分散トランザクションはサポートされません)。  
- 前のセクションでは、の Database.UseTransaction() アプローチを組み合わせることはできません。  
- 任意の DDL を発行し、MSDTC サービスを使用した分散トランザクションを有効にしていない場合、例外がスローされます。  

TransactionScope アプローチの利点があります。  

- それが自動的にアップグレード ローカル トランザクションを分散トランザクションに指定されたデータベースには、複数の接続を作成するか、または、同じトランザクション内の別のデータベースへの接続と 1 つのデータベースへの接続を組み合わせる場合 (注: が必要MSDTC サービスではこれが機能する分散トランザクションを許可するように構成します。  
- コーディングの容易さ。 明示的に制御するのではなく、アンビエントとバック グラウンドで暗黙的にで対応するトランザクションを使用する場合、TransactionScope アプローチ可能性がありますに合わせてする向上。  

新しい Database.BeginTransaction() および上記の Database.UseTransaction() Api の概要では、TransactionScope アプローチはほとんどのユーザーに必要ではなくなりました。 引き続き TransactionScope を使用する場合、上記の制限事項に注意してくださいになります。 可能な限り代わりに、前のセクションで紹介するアプローチを使用することをお勧めします。  
