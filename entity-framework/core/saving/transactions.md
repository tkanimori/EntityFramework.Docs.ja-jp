---
title: トランザクション - EF Core
description: Entity Framework Core でデータを保存する場合の原子性に関するトランザクションの管理
author: roji
ms.date: 9/26/2020
uid: core/saving/transactions
ms.openlocfilehash: b5e1fa2a0bcc466f22f03fee7ecaef9dcea1efaf
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003550"
---
# <a name="using-transactions"></a><span data-ttu-id="8836e-103">トランザクションの使用</span><span class="sxs-lookup"><span data-stu-id="8836e-103">Using Transactions</span></span>

<span data-ttu-id="8836e-104">トランザクションは、複数のデータベース操作をアトミックな方法で処理することを可能にします。</span><span class="sxs-lookup"><span data-stu-id="8836e-104">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="8836e-105">トランザクションがコミットされる場合は、すべての操作がデータベースに正常に適用されます。</span><span class="sxs-lookup"><span data-stu-id="8836e-105">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="8836e-106">トランザクションがロールバックされるる場合、データベースに適用される操作はありません。</span><span class="sxs-lookup"><span data-stu-id="8836e-106">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]
> <span data-ttu-id="8836e-107">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="8836e-107">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="8836e-108">既定のトランザクションの動作</span><span class="sxs-lookup"><span data-stu-id="8836e-108">Default transaction behavior</span></span>

<span data-ttu-id="8836e-109">既定では、データベース プロバイダーがトランザクションをサポートしている場合は、`SaveChanges` への 1 回の呼び出しに含まれるすべての変更がトランザクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="8836e-109">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges` are applied in a transaction.</span></span> <span data-ttu-id="8836e-110">いずれかの変更が失敗した場合、トランザクションはロールバックされ、変更は、データベースにまったく適用されません。</span><span class="sxs-lookup"><span data-stu-id="8836e-110">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="8836e-111">つまり、`SaveChanges` は、完全に成功するか、エラーが発生した場合はデータベースを未変更のままにすることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="8836e-111">This means that `SaveChanges` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="8836e-112">ほとんどのアプリケーションでは、この既定の動作で十分です。</span><span class="sxs-lookup"><span data-stu-id="8836e-112">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="8836e-113">アプリケーションの要件を満たすために必要であると考えた場合にのみ、トランザクションを手動で制御する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8836e-113">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="8836e-114">トランザクションを制御する</span><span class="sxs-lookup"><span data-stu-id="8836e-114">Controlling transactions</span></span>

<span data-ttu-id="8836e-115">トランザクションは、`DbContext.Database` API を使用して、開始、コミット、およびロールバックできます。</span><span class="sxs-lookup"><span data-stu-id="8836e-115">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="8836e-116">次の例は、単一のトランザクションで実行される 2 つの `SaveChanges` 操作と LINQ クエリを示しています。</span><span class="sxs-lookup"><span data-stu-id="8836e-116">The following example shows two `SaveChanges` operations and a LINQ query being executed in a single transaction:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction.cs?name=Transaction&highlight=2,16-18)]

<span data-ttu-id="8836e-117">すべてのリレーショナル データベース プロバイダーではトランザクションがサポートされていますが、他のプロバイダーの種類では、トランザクション API が呼び出されたときに例外がスローされたり、何も行われなかったりすることがあります。</span><span class="sxs-lookup"><span data-stu-id="8836e-117">While all relational database providers support transactions, other providers types may throw or no-op when transaction APIs are called.</span></span>

## <a name="savepoints"></a><span data-ttu-id="8836e-118">セーブポイント</span><span class="sxs-lookup"><span data-stu-id="8836e-118">Savepoints</span></span>

> [!NOTE]
> <span data-ttu-id="8836e-119">この機能は EF Core 5.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="8836e-119">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="8836e-120">`SaveChanges` が呼び出され、そのコンテキストでトランザクションが既に進行中である場合、EF ではデータを保存する前に自動的に "*セーブポイント*" が作成されます。</span><span class="sxs-lookup"><span data-stu-id="8836e-120">When `SaveChanges` is invoked and a transaction is already in progress on the context, EF automatically creates a *savepoint* before saving any data.</span></span> <span data-ttu-id="8836e-121">セーブポイントとは、エラーが発生した場合やその他の理由で、後でロールバックする可能性のある、データベース トランザクション内のポイントです。</span><span class="sxs-lookup"><span data-stu-id="8836e-121">Savepoints are points within a database transaction which may later be rolled back to, if an error occurs or for any other reason.</span></span> <span data-ttu-id="8836e-122">`SaveChanges` でエラーが発生した場合、トランザクションは自動的にセーブポイントにロールバックされ、開始されていない場合と同じ状態でトランザクションが残ります。</span><span class="sxs-lookup"><span data-stu-id="8836e-122">If `SaveChanges` encounters any error, it automatically rolls the transaction back to the savepoint, leaving the transaction in the same state as if it had never started.</span></span> <span data-ttu-id="8836e-123">これにより、[オプティミスティック同時実行制御](xref:core/saving/concurrency)に関する問題が発生した場合は特に、問題を修正して保存を再試行できる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8836e-123">This allows you to possibly correct issues and retry saving, in particular when [optimistic concurrency](xref:core/saving/concurrency) issues occur.</span></span>

> [!WARNING]
> <span data-ttu-id="8836e-124">セーブポイントは、SQL Server の複数のアクティブな結果セットと互換性がなく、使用されません。</span><span class="sxs-lookup"><span data-stu-id="8836e-124">Savepoints are incompatible with SQL Server's Multiple Active Result Sets, and are not used.</span></span> <span data-ttu-id="8836e-125">`SaveChanges` の間にエラーが発生した場合、トランザクションは不明な状態のままになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8836e-125">If an error occurs during `SaveChanges`, the transaction may be left in an unknown state.</span></span>

<span data-ttu-id="8836e-126">また、トランザクションの場合と同様に、セーブポイントを手動で管理することもできます。</span><span class="sxs-lookup"><span data-stu-id="8836e-126">It's also possible to manually manage savepoints, just as it is with transactions.</span></span> <span data-ttu-id="8836e-127">次の例では、トランザクション内にセーブポイントを作成し、障害発生時にそこへロールバックします。</span><span class="sxs-lookup"><span data-stu-id="8836e-127">The following example creates a savepoint within a transaction, and rolls back to it on failure:</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ManagingSavepoints.cs?name=Savepoints&highlight=9,19-20)]

## <a name="cross-context-transaction"></a><span data-ttu-id="8836e-128">クロスコンテキスト トランザクション</span><span class="sxs-lookup"><span data-stu-id="8836e-128">Cross-context transaction</span></span>

<span data-ttu-id="8836e-129">複数のコンテキスト インスタンス間でトランザクションを共有することもできます。</span><span class="sxs-lookup"><span data-stu-id="8836e-129">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="8836e-130">この機能は、リレーショナル データベースに固有の `DbTransaction` と `DbConnection` を使用する必要があるため、リレーショナル データベース プロバイダーを使用する場合にのみ利用できます。</span><span class="sxs-lookup"><span data-stu-id="8836e-130">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="8836e-131">トランザクションを共有するには、コンテキストが `DbConnection` と `DbTransaction` の両方を共有する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8836e-131">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="8836e-132">接続の外部提供を可能にする</span><span class="sxs-lookup"><span data-stu-id="8836e-132">Allow connection to be externally provided</span></span>

<span data-ttu-id="8836e-133">`DbConnection` の共有では、コンテキストの構築時に、コンテキストに接続を渡すことができるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8836e-133">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="8836e-134">`DbConnection` の外部提供を許可する最も簡単な方法は、`DbContext.OnConfiguring` メソッドを使用するコンテキストの構成を停止し、`DbContextOptions` を外部で作成し、それをコンテキスト コンストラクターに渡すことです。</span><span class="sxs-lookup"><span data-stu-id="8836e-134">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]
> <span data-ttu-id="8836e-135">`DbContextOptionsBuilder` は、コンテキストを構成する `DbContext.OnConfiguring` 内で使用される API であり、その API を外部で使用して、`DbContextOptions` を作成します。</span><span class="sxs-lookup"><span data-stu-id="8836e-135">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="8836e-136">別の方法は、`DbContext.OnConfiguring` を引き続き使用しますが、保存された `DbConnection` を受け取って `DbContext.OnConfiguring` で使用することです。</span><span class="sxs-lookup"><span data-stu-id="8836e-136">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

```csharp
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

### <a name="share-connection-and-transaction"></a><span data-ttu-id="8836e-137">接続とトランザクションの共有</span><span class="sxs-lookup"><span data-stu-id="8836e-137">Share connection and transaction</span></span>

<span data-ttu-id="8836e-138">同じ接続を共有する複数のコンテキスト インスタンスを作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8836e-138">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="8836e-139">次に、`DbContext.Database.UseTransaction(DbTransaction)` API を使用して、両方のコンテキストを同じトランザクションに参加させます。</span><span class="sxs-lookup"><span data-stu-id="8836e-139">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Transaction&highlight=1-3,6,14,21-23)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="8836e-140">外部 DbTransactions の使用 (リレーショナル データベースのみ)</span><span class="sxs-lookup"><span data-stu-id="8836e-140">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="8836e-141">複数のデータ アクセス テクノロジを使用してリレーショナル データベースにアクセスしている場合、これらの異なるテクノロジによって実行される操作の間でトランザクションを共有できます。</span><span class="sxs-lookup"><span data-stu-id="8836e-141">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="8836e-142">次の例は、同じトランザクション内で ADO.NET SqlClient 操作と Entity Framework Core 操作を実行する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="8836e-142">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction.cs?name=Transaction&highlight=4,9,20,25-27)]

## <a name="using-systemtransactions"></a><span data-ttu-id="8836e-143">System.Transactions の使用</span><span class="sxs-lookup"><span data-stu-id="8836e-143">Using System.Transactions</span></span>

<span data-ttu-id="8836e-144">大規模なスコープで調整を行う必要がある場合は、アンビエント トランザクションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8836e-144">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction.cs?name=Transaction&highlight=1,2,3,26-28)]

<span data-ttu-id="8836e-145">明示的なトランザクションに参加することもできます。</span><span class="sxs-lookup"><span data-stu-id="8836e-145">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction.cs?name=Transaction&highlight=1-2,15,28-30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="8836e-146">System.Transactions の制限</span><span class="sxs-lookup"><span data-stu-id="8836e-146">Limitations of System.Transactions</span></span>

1. <span data-ttu-id="8836e-147">EF Core は、System.Transactions に対するサポートの実装をデータベース プロバイダーに依存しています。</span><span class="sxs-lookup"><span data-stu-id="8836e-147">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="8836e-148">プロバイダーが System.Transactions のサポートを実装していない場合、これらの API への呼び出しは、完全に無視される可能があります。</span><span class="sxs-lookup"><span data-stu-id="8836e-148">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="8836e-149">SqlClient ではこれがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="8836e-149">SqlClient supports it.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="8836e-150">この API に依存してトランザクションを管理する前に、お使いのプロバイダーで API が正常に動作することをテストすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8836e-150">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="8836e-151">そうでない場合は、データベース プロバイダーの保守管理者に連絡することが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="8836e-151">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="8836e-152">.NET Core 2.1 の時点では、System.Transactions の実装には、分散トランザクションのサポートは含まれていません。したがって、`TransactionScope` または `CommittableTransaction` を使用して複数のリソース マネージャー間でトランザクションを調整することはできません。</span><span class="sxs-lookup"><span data-stu-id="8836e-152">As of .NET Core 2.1, the System.Transactions implementation does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span>
