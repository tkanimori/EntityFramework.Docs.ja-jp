---
title: トランザクション - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
uid: core/saving/transactions
ms.openlocfilehash: 390d89398ebfdf015804749e71ff0b61d3f278d3
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413619"
---
# <a name="using-transactions"></a><span data-ttu-id="817ad-102">トランザクションの使用</span><span class="sxs-lookup"><span data-stu-id="817ad-102">Using Transactions</span></span>

<span data-ttu-id="817ad-103">トランザクションは、複数のデータベース操作をアトミックな方法で処理することを可能にします。</span><span class="sxs-lookup"><span data-stu-id="817ad-103">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="817ad-104">トランザクションがコミットされる場合は、すべての操作がデータベースに正常に適用されます。</span><span class="sxs-lookup"><span data-stu-id="817ad-104">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="817ad-105">トランザクションがロールバックされる場合、データベースに適用される操作はありません。</span><span class="sxs-lookup"><span data-stu-id="817ad-105">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="817ad-106">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="817ad-106">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="817ad-107">既定のトランザクションの動作</span><span class="sxs-lookup"><span data-stu-id="817ad-107">Default transaction behavior</span></span>

<span data-ttu-id="817ad-108">既定では、データベース プロバイダーがトランザクションをサポートしている場合は、`SaveChanges()` への 1 回の呼び出しに含まれるすべての変更がトランザクションに適用されます。</span><span class="sxs-lookup"><span data-stu-id="817ad-108">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges()` are applied in a transaction.</span></span> <span data-ttu-id="817ad-109">いずれかの変更が失敗した場合、トランザクションはロールバックされ、変更は、データベースにまったく適用されません。</span><span class="sxs-lookup"><span data-stu-id="817ad-109">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="817ad-110">つまり、`SaveChanges()` は、完全に成功するか、エラーが発生した場合はデータベースを未変更のままにすることが保証されます。</span><span class="sxs-lookup"><span data-stu-id="817ad-110">This means that `SaveChanges()` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="817ad-111">ほとんどのアプリケーションでは、この既定の動作で十分です。</span><span class="sxs-lookup"><span data-stu-id="817ad-111">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="817ad-112">アプリケーションの要件を満たすために必要であると考えた場合にのみ、トランザクションを手動で制御する必要があります。</span><span class="sxs-lookup"><span data-stu-id="817ad-112">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="817ad-113">トランザクションを制御する</span><span class="sxs-lookup"><span data-stu-id="817ad-113">Controlling transactions</span></span>

<span data-ttu-id="817ad-114">トランザクションは、`DbContext.Database` API を使用して、開始、コミット、およびロールバックできます。</span><span class="sxs-lookup"><span data-stu-id="817ad-114">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="817ad-115">次の例は、単一のトランザクションで実行される 2 つの `SaveChanges()` 操作と LINQ クエリを示しています。</span><span class="sxs-lookup"><span data-stu-id="817ad-115">The following example shows two `SaveChanges()` operations and a LINQ query being executed in a single transaction.</span></span>

<span data-ttu-id="817ad-116">すべてのデータベース プロバイダーがトランザクションをサポートしているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="817ad-116">Not all database providers support transactions.</span></span> <span data-ttu-id="817ad-117">トランザクション API が呼び出された場合、一部のプロバイダーでは、例外がスローされるか、何も実行されないことがあります。</span><span class="sxs-lookup"><span data-stu-id="817ad-117">Some providers may throw or no-op when transaction APIs are called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction/Sample.cs?name=Transaction&highlight=3,17,18,19)]

## <a name="cross-context-transaction-relational-databases-only"></a><span data-ttu-id="817ad-118">クロスコンテキスト トランザクション (リレーショナル データベースのみ)</span><span class="sxs-lookup"><span data-stu-id="817ad-118">Cross-context transaction (relational databases only)</span></span>

<span data-ttu-id="817ad-119">複数のコンテキスト インスタンス間でトランザクションを共有することもできます。</span><span class="sxs-lookup"><span data-stu-id="817ad-119">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="817ad-120">この機能は、リレーショナル データベースに固有の `DbTransaction` と `DbConnection` を使用する必要があるため、リレーショナル データベース プロバイダーを使用する場合にのみ利用できます。</span><span class="sxs-lookup"><span data-stu-id="817ad-120">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="817ad-121">トランザクションを共有するには、コンテキストが `DbConnection` と `DbTransaction` の両方を共有する必要があります。</span><span class="sxs-lookup"><span data-stu-id="817ad-121">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="817ad-122">接続の外部提供を可能にする</span><span class="sxs-lookup"><span data-stu-id="817ad-122">Allow connection to be externally provided</span></span>

<span data-ttu-id="817ad-123">`DbConnection` の共有では、コンテキストの構築時に、コンテキストに接続を渡すことができるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="817ad-123">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="817ad-124">`DbConnection` の外部提供を許可する最も簡単な方法は、`DbContext.OnConfiguring` メソッドを使用するコンテキストの構成を停止し、`DbContextOptions` を外部で作成し、それをコンテキスト コンストラクターに渡すことです。</span><span class="sxs-lookup"><span data-stu-id="817ad-124">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]  
> <span data-ttu-id="817ad-125">`DbContextOptionsBuilder` は、コンテキストを構成する `DbContext.OnConfiguring` 内で使用される API であり、その API を外部で使用して、`DbContextOptions` を作成します。</span><span class="sxs-lookup"><span data-stu-id="817ad-125">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="817ad-126">別の方法は、`DbContext.OnConfiguring` を引き続き使用しますが、保存された `DbConnection` を受け取って `DbContext.OnConfiguring` で使用することです。</span><span class="sxs-lookup"><span data-stu-id="817ad-126">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

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

### <a name="share-connection-and-transaction"></a><span data-ttu-id="817ad-127">接続とトランザクションの共有</span><span class="sxs-lookup"><span data-stu-id="817ad-127">Share connection and transaction</span></span>

<span data-ttu-id="817ad-128">同じ接続を共有する複数のコンテキスト インスタンスを作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="817ad-128">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="817ad-129">次に、`DbContext.Database.UseTransaction(DbTransaction)` API を使用して、両方のコンテキストを同じトランザクションに参加させます。</span><span class="sxs-lookup"><span data-stu-id="817ad-129">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="817ad-130">外部 DbTransactions の使用 (リレーショナル データベースのみ)</span><span class="sxs-lookup"><span data-stu-id="817ad-130">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="817ad-131">複数のデータ アクセス テクノロジを使用してリレーショナル データベースにアクセスしている場合、これらの異なるテクノロジによって実行される操作の間でトランザクションを共有できます。</span><span class="sxs-lookup"><span data-stu-id="817ad-131">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="817ad-132">次の例は、同じトランザクション内で ADO.NET SqlClient 操作と Entity Framework Core 操作を実行する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="817ad-132">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a><span data-ttu-id="817ad-133">System.Transactions の使用</span><span class="sxs-lookup"><span data-stu-id="817ad-133">Using System.Transactions</span></span>

> [!NOTE]  
> <span data-ttu-id="817ad-134">これは EF Core 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="817ad-134">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="817ad-135">大規模なスコープで調整を行う必要がある場合は、アンビエント トランザクションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="817ad-135">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,2,3,26,27,28)]

<span data-ttu-id="817ad-136">明示的なトランザクションに参加することもできます。</span><span class="sxs-lookup"><span data-stu-id="817ad-136">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,15,28,29,30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="817ad-137">System.Transactions の制限</span><span class="sxs-lookup"><span data-stu-id="817ad-137">Limitations of System.Transactions</span></span>  

1. <span data-ttu-id="817ad-138">EF Core は、System.Transactions に対するサポートの実装をデータベース プロバイダーに依存しています。</span><span class="sxs-lookup"><span data-stu-id="817ad-138">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="817ad-139">.NET Framework の ADO.NET プロバイダーの間では、サポートは非常に一般的ですが、この API は .NET Core 最近追加されたため、広い範囲でサポートされているとは言えません。</span><span class="sxs-lookup"><span data-stu-id="817ad-139">Although support is quite common among ADO.NET providers for .NET Framework, the API has only been recently added to .NET Core and hence support is not as widespread.</span></span> <span data-ttu-id="817ad-140">プロバイダーが System.Transactions のサポートを実装していない場合、これらの API への呼び出しは、完全に無視される可能があります。</span><span class="sxs-lookup"><span data-stu-id="817ad-140">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="817ad-141">SqlClient for .NET Core では、2.1 以降、それをサポートします。</span><span class="sxs-lookup"><span data-stu-id="817ad-141">SqlClient for .NET Core does support it from 2.1 onwards.</span></span> <span data-ttu-id="817ad-142">SqlClient for .NET Core 2.0 では、この機能を使用しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="817ad-142">SqlClient for .NET Core 2.0 will throw an exception if you attempt to use the feature.</span></span>

   > [!IMPORTANT]  
   > <span data-ttu-id="817ad-143">この API に依存してトランザクションを管理する前に、お使いのプロバイダーで API が正常に動作することをテストすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="817ad-143">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="817ad-144">そうでない場合は、データベース プロバイダーの保守管理者に連絡することが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="817ad-144">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="817ad-145">バージョン 2.1 の時点では、.NET Core での System.Transactions の実装には、分散トランザクションのサポートは含まれていません。したがって、`TransactionScope` または `CommittableTransaction` を使用して複数のリソース マネージャー間でトランザクションを調整することはできません。</span><span class="sxs-lookup"><span data-stu-id="817ad-145">As of version 2.1, the System.Transactions implementation in .NET Core does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span>
