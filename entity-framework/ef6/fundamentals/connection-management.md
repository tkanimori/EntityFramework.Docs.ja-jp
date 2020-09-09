---
title: 接続管理-EF6
description: Entity Framework 6 での接続管理
author: divega
ms.date: 10/23/2016
ms.assetid: ecaa5a27-b19e-4bf9-8142-a3fb00642270
uid: ef6/fundamentals/connection-management
ms.openlocfilehash: c352e761a9891b5c275f32752f10de13222bf48e
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617224"
---
# <a name="connection-management"></a><span data-ttu-id="7037d-103">接続管理</span><span class="sxs-lookup"><span data-stu-id="7037d-103">Connection management</span></span>
<span data-ttu-id="7037d-104">このページでは、コンテキストへの接続の受け渡しと、 **データベース. Connection. Open ()** API の機能に関して、Entity Framework の動作について説明します。</span><span class="sxs-lookup"><span data-stu-id="7037d-104">This page describes the behavior of Entity Framework with regard to passing connections to the context and the functionality of the **Database.Connection.Open()** API.</span></span>  

## <a name="passing-connections-to-the-context"></a><span data-ttu-id="7037d-105">コンテキストへの接続の引き渡し</span><span class="sxs-lookup"><span data-stu-id="7037d-105">Passing Connections to the Context</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="7037d-106">EF5 以前のバージョンの動作</span><span class="sxs-lookup"><span data-stu-id="7037d-106">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="7037d-107">接続を受け入れるコンストラクターは2つあります。</span><span class="sxs-lookup"><span data-stu-id="7037d-107">There are two constructors which accept connections:</span></span>  

``` csharp
public DbContext(DbConnection existingConnection, bool contextOwnsConnection)
public DbContext(DbConnection existingConnection, DbCompiledModel model, bool contextOwnsConnection)
```  

<span data-ttu-id="7037d-108">これらを使用することはできますが、いくつかの制限事項に対処する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7037d-108">It is possible to use these but you have to work around a couple of limitations:</span></span>  

1. <span data-ttu-id="7037d-109">開いている接続をこれらのいずれかに渡すと、フレームワークが初めて使用しようとしたときに、既に開いている接続を再び開くことができないということを示す InvalidOperationException がスローされます。</span><span class="sxs-lookup"><span data-stu-id="7037d-109">If you pass an open connection to either of these then the first time the framework attempts to use it an InvalidOperationException is thrown saying it cannot re-open an already open connection.</span></span>  
2. <span data-ttu-id="7037d-110">ContextOwnsConnection フラグは、コンテキストが破棄されるときに、基になるストア接続を破棄する必要があるかどうかを示すために解釈されます。</span><span class="sxs-lookup"><span data-stu-id="7037d-110">The contextOwnsConnection flag is interpreted to mean whether or not the underlying store connection should be disposed when the context is disposed.</span></span> <span data-ttu-id="7037d-111">ただし、その設定に関係なく、コンテキストが破棄されると、ストア接続は常に閉じられます。</span><span class="sxs-lookup"><span data-stu-id="7037d-111">But, regardless of that setting, the store connection is always closed when the context is disposed.</span></span> <span data-ttu-id="7037d-112">そのため、同じ接続を持つ複数の DbContext を使用している場合は、最初に破棄されたコンテキストによって接続が閉じられます (同様に、DbContext を使用して既存の ADO.NET 接続を混在させた場合、DbContext は常に破棄された接続を閉じます)。</span><span class="sxs-lookup"><span data-stu-id="7037d-112">So if you have more than one DbContext with the same connection whichever context is disposed first will close the connection (similarly if you have mixed an existing ADO.NET connection with a DbContext, DbContext will always close the connection when it is disposed).</span></span>  

<span data-ttu-id="7037d-113">閉じられた接続を渡し、すべてのコンテキストを作成した後でそれを開くコードのみを実行することで、上記の最初の制限を回避することができます。</span><span class="sxs-lookup"><span data-stu-id="7037d-113">It is possible to work around the first limitation above by passing a closed connection and only executing code that would open it once all contexts have been created:</span></span>  

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

<span data-ttu-id="7037d-114">2つ目の制限は、接続を閉じる準備が整うまで、DbContext オブジェクトの破棄を避ける必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="7037d-114">The second limitation just means you need to refrain from disposing any of your DbContext objects until you are ready for the connection to be closed.</span></span>  

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="7037d-115">EF6 および将来のバージョンでの動作</span><span class="sxs-lookup"><span data-stu-id="7037d-115">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="7037d-116">EF6 および将来のバージョンでは、DbContext は同じ2つのコンストラクターを持ちますが、コンストラクターに渡された接続を受信時に閉じる必要がなくなりました。</span><span class="sxs-lookup"><span data-stu-id="7037d-116">In EF6 and future versions the DbContext has the same two constructors but no longer requires that the connection passed to the constructor be closed when it is received.</span></span> <span data-ttu-id="7037d-117">これで、次のことが可能になりました。</span><span class="sxs-lookup"><span data-stu-id="7037d-117">So this is now possible:</span></span>  

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

<span data-ttu-id="7037d-118">また、contextOwnsConnection フラグは、DbContext が破棄されるときに接続が閉じられ、破棄されるかどうかを制御するようになりました。</span><span class="sxs-lookup"><span data-stu-id="7037d-118">Also the contextOwnsConnection flag now controls whether or not the connection is both closed and disposed when the DbContext is disposed.</span></span> <span data-ttu-id="7037d-119">このため、上記の例では、コンテキストが破棄されたとき (32 行目) に接続が閉じられません。これは、以前のバージョンの EF と同じですが、接続自体が破棄される (40 行目) ことになります。</span><span class="sxs-lookup"><span data-stu-id="7037d-119">So in the above example the connection is not closed when the context is disposed (line 32) as it would have been in previous versions of EF, but rather when the connection itself is disposed (line 40).</span></span>  

<span data-ttu-id="7037d-120">もちろん、DbContext で接続を制御することもできます (contextOwnsConnection を true に設定するか、他のコンストラクターのいずれかを使用するだけで済みます)。</span><span class="sxs-lookup"><span data-stu-id="7037d-120">Of course it is still possible for the DbContext to take control of the connection (just set contextOwnsConnection to true or use one of the other constructors) if you so wish.</span></span>  

> [!NOTE]
> <span data-ttu-id="7037d-121">この新しいモデルでトランザクションを使用する場合は、追加の考慮事項がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="7037d-121">There are some additional considerations when using transactions with this new model.</span></span> <span data-ttu-id="7037d-122">詳細については、「 [トランザクションの](xref:ef6/saving/transactions)使用」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7037d-122">For details see [Working with Transactions](xref:ef6/saving/transactions).</span></span>  

## <a name="databaseconnectionopen"></a><span data-ttu-id="7037d-123">Database. Connection. Open ()</span><span class="sxs-lookup"><span data-stu-id="7037d-123">Database.Connection.Open()</span></span>  

### <a name="behavior-for-ef5-and-earlier-versions"></a><span data-ttu-id="7037d-124">EF5 以前のバージョンの動作</span><span class="sxs-lookup"><span data-stu-id="7037d-124">Behavior for EF5 and earlier versions</span></span>  

<span data-ttu-id="7037d-125">EF5 以前のバージョンでは、基になるストア接続の真の状態を反映するために、 **ObjectContext** が更新されていないというバグがあります。</span><span class="sxs-lookup"><span data-stu-id="7037d-125">In EF5 and earlier versions there is a bug such that the **ObjectContext.Connection.State** was not updated to reflect the true state of the underlying store connection.</span></span> <span data-ttu-id="7037d-126">たとえば、次のコードを実行した場合、実際には基になるストア接続が**開か**れているにもかかわらず、状態を**閉じる**ことができます。</span><span class="sxs-lookup"><span data-stu-id="7037d-126">For example, if you executed the following code you can be returned the status **Closed** even though in fact the underlying store connection is **Open**.</span></span>  

``` csharp
((IObjectContextAdapter)context).ObjectContext.Connection.State
```  

<span data-ttu-id="7037d-127">別の方法として、データベース接続を開いた場合、データベース接続を開くと、次にクエリを実行するか、データベース接続を必要とする任意のものを呼び出す (たとえば、SaveChanges ()) まで、基になるストア接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="7037d-127">Separately, if you open the database connection by calling Database.Connection.Open() it will be open until the next time you execute a query or call anything which requires a database connection (for example, SaveChanges()) but after that the underlying store connection will be closed.</span></span> <span data-ttu-id="7037d-128">その後、別のデータベース操作が必要になったときに、コンテキストは再起動し、接続を再度閉じます。</span><span class="sxs-lookup"><span data-stu-id="7037d-128">The context will then re-open and re-close the connection any time another database operation is required:</span></span>  

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

### <a name="behavior-in-ef6-and-future-versions"></a><span data-ttu-id="7037d-129">EF6 および将来のバージョンでの動作</span><span class="sxs-lookup"><span data-stu-id="7037d-129">Behavior in EF6 and future versions</span></span>  

<span data-ttu-id="7037d-130">EF6 および将来のバージョンでは、呼び出し元のコードがコンテキストを呼び出すことによって接続を開くことを選択した場合のアプローチを採用しました。Database. Connection. Open () では、そのような理由があるので、フレームワークは接続の開閉を制御する必要があり、接続を自動的に閉じることを想定しています。</span><span class="sxs-lookup"><span data-stu-id="7037d-130">For EF6 and future versions we have taken the approach that if the calling code chooses to open the connection by calling context.Database.Connection.Open() then it has a good reason for doing so and the framework will assume that it wants control over opening and closing of the connection and will no longer close the connection automatically.</span></span>  

> [!NOTE]
> <span data-ttu-id="7037d-131">このため、接続が長時間開かれているため、注意して使用することができます。</span><span class="sxs-lookup"><span data-stu-id="7037d-131">This can potentially lead to connections which are open for a long time so use with care.</span></span>  

<span data-ttu-id="7037d-132">また、コードを更新して、ObjectContext が基になる接続の状態を正しく追跡できるようにしました。</span><span class="sxs-lookup"><span data-stu-id="7037d-132">We also updated the code so that ObjectContext.Connection.State now keeps track of the state of the underlying connection correctly.</span></span>  

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
