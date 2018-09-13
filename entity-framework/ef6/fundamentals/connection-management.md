---
title: EF6 の接続管理
author: divega
ms.date: 10/23/2016
ms.assetid: ecaa5a27-b19e-4bf9-8142-a3fb00642270
ms.openlocfilehash: a6352bbbc38c38bd5f30536736ec969056df2c7d
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489337"
---
# <a name="connection-management"></a>接続の管理
このページでは、Entity Framework の動作を記述の接続に渡すコンテキストとの機能に関して、 **Database.Connection.Open()** API。  

## <a name="passing-connections-to-the-context"></a>コンテキストへの接続を渡す  

### <a name="behavior-for-ef5-and-earlier-versions"></a>EF5 と以前のバージョンの動作  

接続を許可する 2 つのコンス トラクターがあります。  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

これらを使用することですが、いくつかの制限を回避する必要があります。  

1. 開いている接続に渡すこれらのいずれかのフレームワークが、InvalidOperationException がスローされることを使用しようとしています。 初めてしという開くことができません再既に開いている接続。  
2. ContextOwnsConnection フラグは、コンテキストが破棄されたときに、基になるストア接続を破棄する必要があるかどうかを意味する解釈されます。 ただし、その設定に関係なく、コンテキストが破棄されたときにストア接続は閉じ常に。 同じ接続では、複数の DbContext があればどのコンテキストが破棄された最初は閉じます (同様に、DbContext で既存の ADO.NET 接続を混合する場合は、DbContext は必ず接続を終了破棄されるときに) 接続.  

閉じている接続を渡すと、すべてのコンテキストが作成された後に開くことがコードを実行するだけで上記の最初の制限を回避することができます。  

``` csharp
using System.Collections.Generic;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Linq;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExampleEF5
    {         
        public static void TwoDbContextsOneConnection()
        {
            using (var context1 = new BloggingContext())
            {
                var conn =
                    ((EntityConnection)  
                        ((IObjectContextAdapter)context1).ObjectContext.Connection)  
                            .StoreConnection;

                using (var context2 = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    context2.Database.ExecuteSqlCommand(
                        @"UPDATE Blogs SET Rating = 5" +
                        " WHERE Name LIKE '%Entity Framework%'");

                    var query = context1.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context1.SaveChanges();
                }
            }
        }
    }
}
```  

第 2 の制限を終了する接続の準備が整ったら、DbContext オブジェクトのいずれかを破棄しないようにする必要があるということです。  

### <a name="behavior-in-ef6-and-future-versions"></a>EF6 と今後のバージョンでの動作  

EF6 と今後のバージョンでは、DbContext は、同じ 2 つのコンス トラクターがありますは受信したときに、コンス トラクターに渡された接続を閉じるには不要します。 このため、これが可能になりました。  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.SqlClient;
using System.Linq;
using System.Transactions;

namespace ConnectionManagementExamples
{
    class ConnectionManagementExample
    {
        public static void PassingAnOpenConnection()
        {
            using (var conn = new SqlConnection("{connectionString}"))
            {
                conn.Open();

                var sqlCommand = new SqlCommand();
                sqlCommand.Connection = conn;
                sqlCommand.CommandText =
                    @"UPDATE Blogs SET Rating = 5" +
                     " WHERE Name LIKE '%Entity Framework%'";
                sqlCommand.ExecuteNonQuery();

                using (var context = new BloggingContext(conn, contextOwnsConnection: false))
                {
                    var query = context.Posts.Where(p => p.Blog.Rating > 5);
                    foreach (var post in query)
                    {
                        post.Title += "[Cool Blog]";
                    }
                    context.SaveChanges();
                }

                var sqlCommand2 = new SqlCommand();
                sqlCommand2.Connection = conn;
                sqlCommand2.CommandText =
                    @"UPDATE Blogs SET Rating = 7" +
                     " WHERE Name LIKE '%Entity Framework Rocks%'";
                sqlCommand2.ExecuteNonQuery();
            }
        }
    }
}
```  

また contextOwnsConnection フラグは、かどうか、接続が閉じられたおよび DbContext を破棄すると破棄を今すぐ制御します。 上記の例では、コンテキストが、接続は閉じられないように以前のバージョンの EF、いたでしょうが、接続自体が破棄されるときではなく、(行 32) を破棄 (40 行)。  

もちろん、DbContext (true または他のコンス トラクターのいずれかを使用するセット contextOwnsConnection だけ) の接続を制御することは可能では希望される場合。  

> [!NOTE]
> このモデルで新しいトランザクションを使用する場合は、いくつか追加の考慮事項にもあります。 詳細を参照してください[トランザクション操作](~/ef6/saving/transactions.md)します。  

## <a name="databaseconnectionopen"></a>Database.Connection.Open()  

### <a name="behavior-for-ef5-and-earlier-versions"></a>EF5 と以前のバージョンの動作  

EF5 と以前のバージョンでは、バグを**ObjectContext.Connection.State**基になるストア接続の実際の状態を反映するように更新されませんでした。 たとえば、次のコードを実行した場合を返されるステータス**Closed**が実際には、基になるストア接続でも**オープン**します。  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

別に場合 Database.Connection.Open() を呼び出すことによって、データベース接続を開くことが開いて、次回クエリを実行するか、データベースの接続が必要なものがあればを呼び出すまで (後 SaveChanges()) など、保存、基になること接続は閉じられます。 コンテキストは、再び開くし、再別のデータベース操作が必要な接続を閉じます。  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;

namespace ConnectionManagementExamples
{
    public class DatabaseOpenConnectionBehaviorEF5
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open  
                // (though ObjectContext.Connection.State will report closed)

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);

                // The underlying store connection is still open  

                context.SaveChanges();

                // After SaveChanges() the underlying store connection is closed  
                // Each SaveChanges() / query etc now opens and immediately closes
                // the underlying store connection

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();
            }
        }
    }
}
```  

### <a name="behavior-in-ef6-and-future-versions"></a>EF6 と今後のバージョンでの動作  

EF6 と今後のバージョンを思い出させてアプローチを呼び出し元のコードは、呼び出し元のコンテキストで接続を開くことが選択した場合。Database.Connection.Open() し、それがこれを行うための正当な理由と、フレームワークは、接続の開閉に制御が、接続を自動的に閉じる不要になったことと想定されます。  

> [!NOTE]
> これは、場合は、可能性のある時間は、そのため注意して使用するのに開かれている接続につながります。  

私たちは ObjectContext.Connection.State ここでは追跡の基になる接続の状態正しくできるように、コードも更新されます。  

``` csharp
using System;
using System.Data;
using System.Data.Entity;
using System.Data.Entity.Core.EntityClient;
using System.Data.Entity.Infrastructure;

namespace ConnectionManagementExamples
{
    internal class DatabaseOpenConnectionBehaviorEF6
    {
        public static void DatabaseOpenConnectionBehavior()
        {
            using (var context = new BloggingContext())
            {
                // At this point the underlying store connection is closed

                context.Database.Connection.Open();

                // Now the underlying store connection is open and the
                // ObjectContext.Connection.State correctly reports open too

                var blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection remains open for the next operation  

                blog = new Blog { /* Blog’s properties */ };
                context.Blogs.Add(blog);
                context.SaveChanges();

                // The underlying store connection is still open

           } // The context is disposed – so now the underlying store connection is closed
        }
    }
}
```  
