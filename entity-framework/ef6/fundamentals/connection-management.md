---
title: EF6 の接続管理
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: ecaa5a27-b19e-4bf9-8142-a3fb00642270
caps.latest.revision: 3
ms.openlocfilehash: 9588ef85435d3c0218defdc098f1e7150fb7ef72
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122762"
---
# <a name="connection-management"></a><span data-ttu-id="73869-102">接続の管理</span><span class="sxs-lookup"><span data-stu-id="73869-102">Connection management</span></span>
<span data-ttu-id="73869-103">このページでは、Entity Framework の動作を記述の接続に渡すコンテキストとの機能に関して、 **Database.Connection.Open()** API。</span><span class="sxs-lookup"><span data-stu-id="73869-103">This page describes the behavior of Entity Framework with regard to passing connections to the context and the functionality of the **Database.Connection.Open()** API.</span></span>  

## <a name="passing-connections-to-the-context"></a><span data-ttu-id="73869-104">コンテキストへの接続を渡す</span><span class="sxs-lookup"><span data-stu-id="73869-104">Passing Connections to the Context</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="73869-105">EF5 と以前のバージョンの動作</span><span class="sxs-lookup"><span data-stu-id="73869-105">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="73869-106">接続を許可する 2 つのコンス トラクターがあります。</span><span class="sxs-lookup"><span data-stu-id="73869-106">There are two constructors which accept connections:</span></span>  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

<span data-ttu-id="73869-107">これらを使用することですが、いくつかの制限を回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="73869-107">It is possible to use these but you have to work around a couple of limitations:</span></span>  

1. <span data-ttu-id="73869-108">開いている接続に渡すこれらのいずれかのフレームワークが、InvalidOperationException がスローされることを使用しようとしています。 初めてしという開くことができません再既に開いている接続。</span><span class="sxs-lookup"><span data-stu-id="73869-108">If you pass an open connection to either of these then the first time the framework attempts to use it an InvalidOperationException is thrown saying it cannot re-open an already open connection.</span></span>  
2. <span data-ttu-id="73869-109">ContextOwnsConnection フラグは、コンテキストが破棄されたときに、基になるストア接続を破棄する必要があるかどうかを意味する解釈されます。</span><span class="sxs-lookup"><span data-stu-id="73869-109">The contextOwnsConnection flag is interpreted to mean whether or not the underlying store connection should be disposed when the context is disposed.</span></span> <span data-ttu-id="73869-110">ただし、その設定に関係なく、コンテキストが破棄されたときにストア接続は閉じ常に。</span><span class="sxs-lookup"><span data-stu-id="73869-110">But, regardless of that setting, the store connection is always closed when the context is disposed.</span></span> <span data-ttu-id="73869-111">同じ接続では、複数の DbContext があればどのコンテキストが破棄された最初は閉じます (同様に、DbContext で既存の ADO.NET 接続を混合する場合は、DbContext は必ず接続を終了破棄されるときに) 接続.</span><span class="sxs-lookup"><span data-stu-id="73869-111">So if you have more than one DbContext with the same connection whichever context is disposed first will close the connection (similarly if you have mixed an existing ADO.NET connection with a DbContext, DbContext will always close the connection when it is disposed).</span></span>  

<span data-ttu-id="73869-112">閉じている接続を渡すと、すべてのコンテキストが作成された後に開くことがコードを実行するだけで上記の最初の制限を回避することができます。</span><span class="sxs-lookup"><span data-stu-id="73869-112">It is possible to work around the first limitation above by passing a closed connection and only executing code that would open it once all contexts have been created:</span></span>  

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

<span data-ttu-id="73869-113">第 2 の制限を終了する接続の準備が整ったら、DbContext オブジェクトのいずれかを破棄しないようにする必要があるということです。</span><span class="sxs-lookup"><span data-stu-id="73869-113">The second limitation just means you need to refrain from disposing any of your DbContext objects until you are ready for the connection to be closed.</span></span>  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="73869-114">EF6 と今後のバージョンでの動作</span><span class="sxs-lookup"><span data-stu-id="73869-114">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="73869-115">EF6 と今後のバージョンでは、DbContext は、同じ 2 つのコンス トラクターがありますは受信したときに、コンス トラクターに渡された接続を閉じるには不要します。</span><span class="sxs-lookup"><span data-stu-id="73869-115">In EF6 and future versions the DbContext has the same two constructors but no longer requires that the connection passed to the constructor be closed when it is received.</span></span> <span data-ttu-id="73869-116">このため、これが可能になりました。</span><span class="sxs-lookup"><span data-stu-id="73869-116">So this is now possible:</span></span>  

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

<span data-ttu-id="73869-117">また contextOwnsConnection フラグは、かどうか、接続が閉じられたおよび DbContext を破棄すると破棄を今すぐ制御します。</span><span class="sxs-lookup"><span data-stu-id="73869-117">Also the contextOwnsConnection flag now controls whether or not the connection is both closed and disposed when the DbContext is disposed.</span></span> <span data-ttu-id="73869-118">上記の例では、コンテキストが、接続は閉じられないように以前のバージョンの EF、いたでしょうが、接続自体が破棄されるときではなく、(行 32) を破棄 (40 行)。</span><span class="sxs-lookup"><span data-stu-id="73869-118">So in the above example the connection is not closed when the context is disposed (line 32) as it would have been in previous versions of EF, but rather when the connection itself is disposed (line 40).</span></span>  

<span data-ttu-id="73869-119">もちろん、DbContext (true または他のコンス トラクターのいずれかを使用するセット contextOwnsConnection だけ) の接続を制御することは可能では希望される場合。</span><span class="sxs-lookup"><span data-stu-id="73869-119">Of course it is still possible for the DbContext to take control of the connection (just set contextOwnsConnection to true or use one of the other constructors) if you so wish.</span></span>  

> [!NOTE]
> <span data-ttu-id="73869-120">このモデルで新しいトランザクションを使用する場合は、いくつか追加の考慮事項にもあります。</span><span class="sxs-lookup"><span data-stu-id="73869-120">There are some additional considerations when using transactions with this new model.</span></span> <span data-ttu-id="73869-121">詳細を参照してください[トランザクション操作](~/ef6/saving/transactions.md)します。</span><span class="sxs-lookup"><span data-stu-id="73869-121">For details see [Working with Transactions](~/ef6/saving/transactions.md).</span></span>  

## <a name="databaseconnectionopen"></a><span data-ttu-id="73869-122">Database.Connection.Open()</span><span class="sxs-lookup"><span data-stu-id="73869-122">Database.Connection.Open()</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="73869-123">EF5 と以前のバージョンの動作</span><span class="sxs-lookup"><span data-stu-id="73869-123">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="73869-124">EF5 と以前のバージョンでは、バグを**ObjectContext.Connection.State**基になるストア接続の実際の状態を反映するように更新されませんでした。</span><span class="sxs-lookup"><span data-stu-id="73869-124">In EF5 and earlier versions there is a bug such that the **ObjectContext.Connection.State** was not updated to reflect the true state of the underlying store connection.</span></span> <span data-ttu-id="73869-125">たとえば、次のコードを実行した場合を返されるステータス**Closed**が実際には、基になるストア接続でも**オープン**します。</span><span class="sxs-lookup"><span data-stu-id="73869-125">For example, if you executed the following code you can be returned the status **Closed** even though in fact the underlying store connection is **Open**.</span></span>  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

<span data-ttu-id="73869-126">別に場合 Database.Connection.Open() を呼び出すことによって、データベース接続を開くことが開いて、次回クエリを実行するか、データベースの接続が必要なものがあればを呼び出すまで (後 SaveChanges()) など、保存、基になること接続は閉じられます。</span><span class="sxs-lookup"><span data-stu-id="73869-126">Separately, if you open the database connection by calling Database.Connection.Open() it will be open until the next time you execute a query or call anything which requires a database connection (for example, SaveChanges()) but after that the underlying store connection will be closed.</span></span> <span data-ttu-id="73869-127">コンテキストは、再び開くし、再別のデータベース操作が必要な接続を閉じます。</span><span class="sxs-lookup"><span data-stu-id="73869-127">The context will then re-open and re-close the connection any time another database operation is required:</span></span>  

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

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="73869-128">EF6 と今後のバージョンでの動作</span><span class="sxs-lookup"><span data-stu-id="73869-128">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="73869-129">EF6 と今後のバージョンを思い出させてアプローチを呼び出し元のコードは、呼び出し元のコンテキストで接続を開くことが選択した場合。Database.Connection.Open() し、それがこれを行うための正当な理由と、フレームワークは、接続の開閉に制御が、接続を自動的に閉じる不要になったことと想定されます。</span><span class="sxs-lookup"><span data-stu-id="73869-129">For EF6 and future versions we have taken the approach that if the calling code chooses to open the connection by calling context.Database.Connection.Open() then it has a good reason for doing so and the framework will assume that it wants control over opening and closing of the connection and will no longer close the connection automatically.</span></span>  

> [!NOTE]
> <span data-ttu-id="73869-130">これは、場合は、可能性のある時間は、そのため注意して使用するのに開かれている接続につながります。</span><span class="sxs-lookup"><span data-stu-id="73869-130">This can potentially lead to connections which are open for a long time so use with care.</span></span>  

<span data-ttu-id="73869-131">私たちは ObjectContext.Connection.State ここでは追跡の基になる接続の状態正しくできるように、コードも更新されます。</span><span class="sxs-lookup"><span data-stu-id="73869-131">We also updated the code so that ObjectContext.Connection.State now keeps track of the state of the underlying connection correctly.</span></span>  

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
