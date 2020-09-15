---
title: 接続管理-EF6
description: Entity Framework 6 での接続管理
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/connection-management
ms.openlocfilehash: e2097f5ed0471d309292625990cc5f9a598c5b2a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070563"
---
# <a name="connection-management"></a>接続管理
このページでは、コンテキストへの接続の受け渡しと、 **データベース. Connection. Open ()** API の機能に関して、Entity Framework の動作について説明します。  

## <a name="passing-connections-to-the-context"></a>コンテキストへの接続の引き渡し  

### <a name="behavior-for-ef5-and-earlier-versions"></a>EF5 以前のバージョンの動作  

接続を受け入れるコンストラクターは2つあります。  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

これらを使用することはできますが、いくつかの制限事項に対処する必要があります。  

1. 開いている接続をこれらのいずれかに渡すと、フレームワークが初めて使用しようとしたときに、既に開いている接続を再び開くことができないということを示す InvalidOperationException がスローされます。  
2. ContextOwnsConnection フラグは、コンテキストが破棄されるときに、基になるストア接続を破棄する必要があるかどうかを示すために解釈されます。 ただし、その設定に関係なく、コンテキストが破棄されると、ストア接続は常に閉じられます。 そのため、同じ接続を持つ複数の DbContext を使用している場合は、最初に破棄されたコンテキストによって接続が閉じられます (同様に、DbContext を使用して既存の ADO.NET 接続を混在させた場合、DbContext は常に破棄された接続を閉じます)。  

閉じられた接続を渡し、すべてのコンテキストを作成した後でそれを開くコードのみを実行することで、上記の最初の制限を回避することができます。  

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

2つ目の制限は、接続を閉じる準備が整うまで、DbContext オブジェクトの破棄を避ける必要があることを意味します。  

### <a name="behavior-in-ef6-and-future-versions"></a>EF6 および将来のバージョンでの動作  

EF6 および将来のバージョンでは、DbContext は同じ2つのコンストラクターを持ちますが、コンストラクターに渡された接続を受信時に閉じる必要がなくなりました。 これで、次のことが可能になりました。  

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

また、contextOwnsConnection フラグは、DbContext が破棄されるときに接続が閉じられ、破棄されるかどうかを制御するようになりました。 このため、上記の例では、コンテキストが破棄されたとき (32 行目) に接続が閉じられません。これは、以前のバージョンの EF と同じですが、接続自体が破棄される (40 行目) ことになります。  

もちろん、DbContext で接続を制御することもできます (contextOwnsConnection を true に設定するか、他のコンストラクターのいずれかを使用するだけで済みます)。  

> [!NOTE]
> この新しいモデルでトランザクションを使用する場合は、追加の考慮事項がいくつかあります。 詳細については、「 [トランザクションの](xref:ef6/saving/transactions)使用」を参照してください。  

## <a name="databaseconnectionopen"></a>Database. Connection. Open ()  

### <a name="behavior-for-ef5-and-earlier-versions"></a>EF5 以前のバージョンの動作  

EF5 以前のバージョンでは、基になるストア接続の真の状態を反映するために、 **ObjectContext** が更新されていないというバグがあります。 たとえば、次のコードを実行した場合、実際には基になるストア接続が**開か**れているにもかかわらず、状態を**閉じる**ことができます。  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

別の方法として、データベース接続を開いた場合、データベース接続を開くと、次にクエリを実行するか、データベース接続を必要とする任意のものを呼び出す (たとえば、SaveChanges ()) まで、基になるストア接続が閉じられます。 その後、別のデータベース操作が必要になったときに、コンテキストは再起動し、接続を再度閉じます。  

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

### <a name="behavior-in-ef6-and-future-versions"></a>EF6 および将来のバージョンでの動作  

EF6 および将来のバージョンでは、呼び出し元のコードがコンテキストを呼び出すことによって接続を開くことを選択した場合のアプローチを採用しました。Database. Connection. Open () では、そのような理由があるので、フレームワークは接続の開閉を制御する必要があり、接続を自動的に閉じることを想定しています。  

> [!NOTE]
> このため、接続が長時間開かれているため、注意して使用することができます。  

また、コードを更新して、ObjectContext が基になる接続の状態を正しく追跡できるようにしました。  

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
