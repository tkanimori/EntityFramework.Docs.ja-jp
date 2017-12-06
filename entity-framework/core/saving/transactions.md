---
title: "トランザクションの EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
ms.technology: entity-framework-core
uid: core/saving/transactions
ms.openlocfilehash: a2f890c0af1e83cbcc1d40d68540ff7132a9bafd
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="using-transactions"></a><span data-ttu-id="862a7-102">トランザクションの使用</span><span class="sxs-lookup"><span data-stu-id="862a7-102">Using Transactions</span></span>

<span data-ttu-id="862a7-103">トランザクションはアトミックな方法で処理される複数のデータベース操作を許可します。</span><span class="sxs-lookup"><span data-stu-id="862a7-103">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="862a7-104">トランザクションがコミットされた場合は、すべての操作が正常にデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="862a7-104">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="862a7-105">トランザクションがロールバックされた場合は、データベースにどの操作も適用されます。</span><span class="sxs-lookup"><span data-stu-id="862a7-105">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="862a7-106">この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Transactions/)GitHub でします。</span><span class="sxs-lookup"><span data-stu-id="862a7-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="862a7-107">既定のトランザクション動作</span><span class="sxs-lookup"><span data-stu-id="862a7-107">Default transaction behavior</span></span>

<span data-ttu-id="862a7-108">既定では、データベース プロバイダーが、トランザクションをサポートしている場合のすべての変更を 1 回の呼び出しで`SaveChanges()`トランザクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="862a7-108">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges()` are applied in a transaction.</span></span> <span data-ttu-id="862a7-109">変更が失敗した場合、トランザクションがロールバックされ、どの変更は、データベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="862a7-109">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="862a7-110">つまり、`SaveChanges()`完全に成功、または、データベース エラーが発生した場合は、未変更の状態のままにすることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="862a7-110">This means that `SaveChanges()` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="862a7-111">ほとんどのアプリケーションでこの既定の動作で十分です。</span><span class="sxs-lookup"><span data-stu-id="862a7-111">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="862a7-112">アプリケーションの要件とみなす必要な場合に手動でのみ、トランザクションを制御する必要があります。</span><span class="sxs-lookup"><span data-stu-id="862a7-112">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="862a7-113">トランザクションを制御します。</span><span class="sxs-lookup"><span data-stu-id="862a7-113">Controlling transactions</span></span>

<span data-ttu-id="862a7-114">使用することができます、`DbContext.Database`はじめに、コミット、API とのトランザクションをロールバックします。</span><span class="sxs-lookup"><span data-stu-id="862a7-114">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="862a7-115">次の例は 2 つ`SaveChanges()`操作と LINQ クエリを単一のトランザクションで実行されています。</span><span class="sxs-lookup"><span data-stu-id="862a7-115">The following example shows two `SaveChanges()` operations and a LINQ query being executed in a single transaction.</span></span>

<span data-ttu-id="862a7-116">すべてのデータベース プロバイダーは、トランザクションをサポートします。</span><span class="sxs-lookup"><span data-stu-id="862a7-116">Not all database providers support transactions.</span></span> <span data-ttu-id="862a7-117">スローする可能性が一部のプロバイダーまたはトランザクションの Api が呼び出されたときに行いません。</span><span class="sxs-lookup"><span data-stu-id="862a7-117">Some providers may throw or no-op when transaction APIs are called.</span></span>

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/ControllingTransaction/Sample.cs?highlight=3,17,18,19)] -->
``` csharp
        using (var context = new BloggingContext())
        {
            using (var transaction = context.Database.BeginTransaction())
            {
                try
                {
                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
                    context.SaveChanges();

                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/visualstudio" });
                    context.SaveChanges();

                    var blogs = context.Blogs
                        .OrderBy(b => b.Url)
                        .ToList();

                    // Commit transaction if all commands succeed, transaction will auto-rollback
                    // when disposed if either commands fails
                    transaction.Commit();
                }
                catch (Exception)
                {
                    // TODO: Handle failure
                }
            }
        }
```

## <a name="cross-context-transaction-relational-databases-only"></a><span data-ttu-id="862a7-118">クロス コンテキスト トランザクション (リレーショナル データベースのみ)</span><span class="sxs-lookup"><span data-stu-id="862a7-118">Cross-context transaction (relational databases only)</span></span>

<span data-ttu-id="862a7-119">複数のコンテキストのインスタンス間でトランザクションを共有することもできます。</span><span class="sxs-lookup"><span data-stu-id="862a7-119">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="862a7-120">この機能は、の使用が必要とするために、リレーショナル データベース プロバイダーを使用する場合にのみ使用可能な`DbTransaction`と`DbConnection`、これは、リレーショナル データベースに固有です。</span><span class="sxs-lookup"><span data-stu-id="862a7-120">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="862a7-121">トランザクションを共有するコンテキストを共有両方、`DbConnection`と`DbTransaction`です。</span><span class="sxs-lookup"><span data-stu-id="862a7-121">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="862a7-122">提供される外部接続を許可します。</span><span class="sxs-lookup"><span data-stu-id="862a7-122">Allow connection to be externally provided</span></span>

<span data-ttu-id="862a7-123">共有、`DbConnection`を構築するときに、コンテキストに接続を渡すことが必要です。</span><span class="sxs-lookup"><span data-stu-id="862a7-123">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="862a7-124">許可する最も簡単な方法`DbConnection`を外部的に提供するには、使用を停止するが、`DbContext.OnConfiguring`メソッド コンテキストを構成し、外部で作成する`DbContextOptions`コンテキスト コンス トラクターに渡すとします。</span><span class="sxs-lookup"><span data-stu-id="862a7-124">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]  
> <span data-ttu-id="862a7-125">`DbContextOptionsBuilder`使用される API は、`DbContext.OnConfiguring`コンテキストを構成するようになりましたしようとする外部で使用して作成する`DbContextOptions`です。</span><span class="sxs-lookup"><span data-stu-id="862a7-125">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?highlight=3,4,5)] -->
``` csharp
    public class BloggingContext : DbContext
    {
        public BloggingContext(DbContextOptions<BloggingContext> options)
            : base(options)
        { }

        public DbSet<Blog> Blogs { get; set; }
    }
```

<span data-ttu-id="862a7-126">使用を継続するという方法も`DbContext.OnConfiguring`は受け付け、`DbConnection`が保存されで使用して`DbContext.OnConfiguring`です。</span><span class="sxs-lookup"><span data-stu-id="862a7-126">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    private DbConnection _connection;

    public BloggingContext(DbConnection connection)
    {
      _connection = connection;
    }

    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(_connection);
    }
}
```

### <a name="share-connection-and-transaction"></a><span data-ttu-id="862a7-127">共有接続とトランザクション</span><span class="sxs-lookup"><span data-stu-id="862a7-127">Share connection and transaction</span></span>

<span data-ttu-id="862a7-128">同じ接続を共有する複数のコンテキストのインスタンスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="862a7-128">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="862a7-129">使用して、 `DbContext.Database.UseTransaction(DbTransaction)` API を両方のコンテキストで、同じトランザクションに参加します。</span><span class="sxs-lookup"><span data-stu-id="862a7-129">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?highlight=1,2,3,7,16,23,24,25)] -->
``` csharp
        var options = new DbContextOptionsBuilder<BloggingContext>()
            .UseSqlServer(new SqlConnection(connectionString))
            .Options;

        using (var context1 = new BloggingContext(options))
        {
            using (var transaction = context1.Database.BeginTransaction())
            {
                try
                {
                    context1.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
                    context1.SaveChanges();

                    using (var context2 = new BloggingContext(options))
                    {
                        context2.Database.UseTransaction(transaction.GetDbTransaction());

                        var blogs = context2.Blogs
                            .OrderBy(b => b.Url)
                            .ToList();
                    }

                    // Commit transaction if all commands succeed, transaction will auto-rollback
                    // when disposed if either commands fails
                    transaction.Commit();
                }
                catch (Exception)
                {
                    // TODO: Handle failure
                }
            }
        }
```

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="862a7-130">外部 DbTransactions (リレーショナル データベースのみ) を使用します。</span><span class="sxs-lookup"><span data-stu-id="862a7-130">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="862a7-131">リレーショナル データベースへのアクセスを複数のデータ アクセス テクノロジを使用している場合は、これらのさまざまなテクノロジによって実行される操作の間でトランザクションを共有します。</span><span class="sxs-lookup"><span data-stu-id="862a7-131">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="862a7-132">次の例では、同じトランザクションで ADO.NET SqlClient 操作とエンティティ フレームワークの主要な操作を実行する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="862a7-132">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/ExternalDbTransaction/Sample.cs?highlight=4,10,21,26,27,28)] -->
``` csharp
        var connection = new SqlConnection(connectionString);
        connection.Open();

        using (var transaction = connection.BeginTransaction())
        {
            try
            {
                // Run raw ADO.NET command in the transaction
                var command = connection.CreateCommand();
                command.Transaction = transaction;
                command.CommandText = "DELETE FROM dbo.Blogs";
                command.ExecuteNonQuery();

                // Run an EF Core command in the transaction
                var options = new DbContextOptionsBuilder<BloggingContext>()
                    .UseSqlServer(connection)
                    .Options;

                using (var context = new BloggingContext(options))
                {
                    context.Database.UseTransaction(transaction);
                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
                    context.SaveChanges();
                }

                // Commit transaction if all commands succeed, transaction will auto-rollback
                // when disposed if either commands fails
                transaction.Commit();
            }
            catch (System.Exception)
            {
                // TODO: Handle failure
            }
```
