---
title: トランザクションの操作-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0d0f1824-d781-4cb3-8fda-b7eaefced1cd
ms.openlocfilehash: 7030dc675993339f72c935f6b430cead85fecb7f
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416241"
---
# <a name="working-with-transactions"></a>トランザクションの操作
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

このドキュメントでは、EF6 でトランザクションを使用する方法について説明します。これには、トランザクションを簡単に操作できるように EF5 以降に追加された機能も含まれます。  

## <a name="what-ef-does-by-default"></a>既定での EF の動作  

Entity Framework のすべてのバージョンでは、 **SaveChanges ()** を実行してデータベースに対して挿入、更新、または削除を実行するたびに、フレームワークはその操作をトランザクションにラップします。 このトランザクションは、操作を実行して完了するまでに十分な時間だけ継続します。 別の操作を実行すると、新しいトランザクションが開始されます。  

EF6 では、既定では、コマンドがまだ存在していない場合は、そのコマンドがトランザクションにラップされ**ます。** このメソッドのオーバーロードを使用すると、必要に応じてこの動作をオーバーライドできます。 また、EF6 **()** などの api によってモデルに含まれるストアドプロシージャの実行も、同じように実行されます (ただし、既定の動作はオーバーライドされません)。  

どちらの場合も、トランザクションの分離レベルは、データベースプロバイダーが既定の設定を考慮している任意の分離レベルです。 既定では、SQL Server では READ COMMITTED です。  

Entity Framework は、トランザクション内のクエリをラップしません。  

この既定の機能は、多数のユーザーに適しているので、EF6 では何もする必要はありません。コードを記述するのは常に同じです。  

ただし、ユーザーによっては、トランザクションをより詳細に制御する必要があります。これについては、次のセクションで説明します。  

## <a name="how-the-apis-work"></a>Api のしくみ  

EF6 より前では、データベース接続自体を開いたときに insisted を Entity Framework しています (既に開いている接続が渡された場合、例外がスローされました)。 トランザクションは開いている接続でのみ開始できるため、ユーザーが複数の操作を1つのトランザクションにラップする唯一の方法は、 [TransactionScope](https://msdn.microsoft.com/library/system.transactions.transactionscope.aspx)を使用するか、**または、返さ**れた**Entityconnection**オブジェクトに対して**open ()** と**BeginTransaction ()** の呼び出しを直接開始することです。 また、基になるデータベース接続でトランザクションを開始した場合、データベースに接続した API 呼び出しは失敗します。  

> [!NOTE]
> 閉じられた接続のみを受け入れる制限は Entity Framework 6 で削除されました。 詳細については、「[接続管理](~/ef6/fundamentals/connection-management.md)」を参照してください。  

EF6 以降では、フレームワークで次の機能が提供されるようになりました。  

1. **BeginTransaction ()** : ユーザーが既存の dbcontext 内でトランザクションを開始して完了するための簡単な方法。同じトランザクション内で複数の操作を組み合わせることができるため、すべてがコミットされるか、またはすべてがロールバックされます。 また、トランザクションの分離レベルをユーザーがより簡単に指定できるようにします。  
2. **UseTransaction ()** : dbcontext で、Entity Framework の外部で開始されたトランザクションを使用できるようにします。  

### <a name="combining-several-operations-into-one-transaction-within-the-same-context"></a>同じコンテキスト内で複数の操作を1つのトランザクションに結合する  

**BeginTransaction ()** には、2つのオーバーライドがあります。1つは明示的な[IsolationLevel](https://msdn.microsoft.com/library/system.data.isolationlevel.aspx)を受け取り、もう1つは引数を取らず、基になるデータベースプロバイダーからの既定の IsolationLevel を使用します。 どちらのオーバーライドも、基になるストアトランザクションでコミットとロールバックを実行する**commit ()** メソッドと**rollback ()** メソッドを提供する**dbcontexttransaction**オブジェクトを返します。  

**Dbcontexttransaction**は、コミットまたはロールバックされた後に破棄されることを意図しています。 これを実現する簡単な方法の1つは、 **using (...) {...}** using ブロックが完了すると自動的に**Dispose ()** を呼び出す構文。  

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
            }
        }
    }
}
```  

> [!NOTE]
> トランザクションを開始するには、基になるストア接続が開いている必要があります。 そのため、BeginTransaction () を呼び出すと、接続が開かれていない場合にその接続が開かれます。 DbContextTransaction が接続を開いた場合、Dispose () が呼び出されると、このトランザクションは閉じられます。  

### <a name="passing-an-existing-transaction-to-the-context"></a>既存のトランザクションをコンテキストに渡す  

場合によっては、スコープ内でさらに広範なトランザクションを使用し、同じデータベースに対する操作や、EF の外部で完全に操作を含めることができます。 これを実現するには、接続を開いて、自分でトランザクションを開始してから、既に開かれているデータベース接続を使用するように EF a に指示し、b) その接続で既存のトランザクションを使用する必要があります。  

これを行うには、既存の接続パラメーターと ii) contextOwnsConnection ブール値を受け取る DbContext コンストラクターの1つを継承する、コンテキストクラスでコンストラクターを定義して使用する必要があります。  

> [!NOTE]
> このシナリオで呼び出される場合は、contextOwnsConnection フラグを false に設定する必要があります。 これは、接続が終了したときに接続を終了しないことを Entity Framework 通知するために重要です (たとえば、次の4行目を参照)。  

``` csharp
using (var conn = new SqlConnection("..."))
{
    conn.Open();
    using (var context = new BloggingContext(conn, contextOwnsConnection: false))
    {
    }
}
```  

さらに、(既定の設定を避ける必要がある場合は IsolationLevel を含む) 自分でトランザクションを開始し、接続で既に開始されているトランザクションがあることを Entity Framework 確認してください (下記の33行目を参照)。  

その後、SqlConnection 自体、または DbContext で直接データベース操作を実行できます。 このような操作はすべて1つのトランザクション内で実行されます。 トランザクションのコミットまたはロールバック、およびそのトランザクションに対する Dispose () の呼び出し、およびデータベース接続の終了と破棄を行います。 次に例を示します。  

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
        static void UsingExternalTransaction()
        {
            using (var conn = new SqlConnection("..."))
            {
               conn.Open();

               using (var sqlTxn = conn.BeginTransaction(System.Data.IsolationLevel.Snapshot))
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
            }
        }
    }
}
```  

### <a name="clearing-up-the-transaction"></a>トランザクションを消去しています

UseTransaction () に null を渡すことにより、現在のトランザクションに関する Entity Framework の知識を消去できます。 この操作を行っても、Entity Framework によって既存のトランザクションがコミットまたはロールバックされることはありません。そのため、この操作を行う必要がある場合にのみ、慎重に使用してください。  

### <a name="errors-in-usetransaction"></a>UseTransaction のエラー

次の場合にトランザクションを渡すと、UseTransaction () の例外が表示されます。  
- Entity Framework 既存のトランザクションが既に存在します  
- Entity Framework は TransactionScope 内で既に動作しています  
- 渡されたトランザクション内の接続オブジェクトが null です。 つまり、トランザクションは接続に関連付けられていません。通常、これはトランザクションが既に完了していることを示す符号です。  
- 渡されたトランザクションの接続オブジェクトが Entity Framework の接続と一致しません。  

## <a name="using-transactions-with-other-features"></a>他の機能でのトランザクションの使用  

このセクションでは、上記のトランザクションとの相互作用について詳しく説明します。  

- 接続の回復性  
- 非同期メソッド  
- TransactionScope トランザクション  

### <a name="connection-resiliency"></a>接続の回復  

新しい接続の回復性機能は、ユーザーが開始したトランザクションでは機能しません。 詳細については、「[実行方法の再試行](~/ef6/fundamentals/connection-resiliency/retry-logic.md#user-initiated-transactions-are-not-supported)」を参照してください。  

### <a name="asynchronous-programming"></a>非同期プログラミング  

前のセクションで説明した方法では、[非同期クエリおよび保存メソッド](~/ef6/fundamentals/async.md
)を操作するためのその他のオプションや設定は必要ありません。 ただし、非同期メソッドの実行内容によっては、トランザクションが長時間実行される可能性があることに注意してください。これにより、アプリケーション全体のパフォーマンスを低下させるデッドロックやブロッキングが発生する可能性があります。  

### <a name="transactionscope-transactions"></a>TransactionScope トランザクション  

EF6 より前の場合は、より大きなスコープのトランザクションを提供するために、TransactionScope オブジェクトを使用することをお勧めします。  

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

SqlConnection と Entity Framework はどちらもアンビエント TransactionScope トランザクションを使用するため、一緒にコミットされます。  

.NET 4.5.1 以降の TransactionScope は、 [TransactionScopeAsyncFlowOption](https://msdn.microsoft.com/library/system.transactions.transactionscopeasyncflowoption.aspx)列挙体を使用して非同期メソッドでも動作するように更新されました。  

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

TransactionScope アプローチにはまだいくつかの制限があります。  

- 非同期メソッドを操作するには、.NET 4.5.1 以降が必要です。  
- 接続が1つしかない (クラウドシナリオでは分散トランザクションがサポートされていない) 場合を除き、クラウドシナリオでは使用できません。  
- 前のセクションの UseTransaction () アプローチと組み合わせることはできません。  
- DDL を発行し、MSDTC サービスを介して分散トランザクションが有効になっていない場合は、例外がスローされます。  

TransactionScope アプローチの利点は次のとおりです。  

- 特定のデータベースに対して複数の接続を作成したり、同じトランザクション内の別のデータベースへの接続を使用して1つのデータベースに接続を結合したりすると、ローカルトランザクションは分散トランザクションに自動的にアップグレードされます (注:分散トランザクションが機能するように MSDTC サービスが構成されています)。  
- コーディングの容易さ。 トランザクションがアンビエントで、明示的に制御するのではなく、バックグラウンドで暗黙的に処理されるようにする場合は、TransactionScope アプローチの方が適している可能性があります。  

要約すると、上記の新しい BeginTransaction () Api と UseTransaction () Api を使用すると、ほとんどのユーザーに対して TransactionScope アプローチは不要になります。 引き続き TransactionScope を使用する場合は、上記の制限事項に注意してください。 可能であれば、前のセクションで説明した方法を使用することをお勧めします。  
