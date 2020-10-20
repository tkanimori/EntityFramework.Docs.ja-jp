---
title: トランザクション - EF Core
description: Entity Framework Core でデータを保存する場合の原子性に関するトランザクションの管理
author: roji
ms.date: 9/26/2020
uid: core/saving/transactions
ms.openlocfilehash: 2cefe23068a40122b7a37c21536213456eef7b66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063622"
---
# <a name="using-transactions"></a>トランザクションの使用

トランザクションは、複数のデータベース操作をアトミックな方法で処理することを可能にします。 トランザクションがコミットされる場合は、すべての操作がデータベースに正常に適用されます。 トランザクションがロールバックされるる場合、データベースに適用される操作はありません。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/)は GitHub で確認できます。

## <a name="default-transaction-behavior"></a>既定のトランザクションの動作

既定では、データベース プロバイダーがトランザクションをサポートしている場合は、`SaveChanges` への 1 回の呼び出しに含まれるすべての変更がトランザクションに適用されます。 いずれかの変更が失敗した場合、トランザクションはロールバックされ、変更は、データベースにまったく適用されません。 つまり、`SaveChanges` は、完全に成功するか、エラーが発生した場合はデータベースを未変更のままにすることが保証されます。

ほとんどのアプリケーションでは、この既定の動作で十分です。 アプリケーションの要件を満たすために必要であると考えた場合にのみ、トランザクションを手動で制御する必要があります。

## <a name="controlling-transactions"></a>トランザクションを制御する

トランザクションは、`DbContext.Database` API を使用して、開始、コミット、およびロールバックできます。 次の例は、単一のトランザクションで実行される 2 つの `SaveChanges` 操作と LINQ クエリを示しています。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction.cs?name=Transaction&highlight=2,16-18)]

すべてのリレーショナル データベース プロバイダーではトランザクションがサポートされていますが、他のプロバイダーの種類では、トランザクション API が呼び出されたときに例外がスローされたり、何も行われなかったりすることがあります。

## <a name="savepoints"></a>セーブポイント

`SaveChanges` が呼び出され、そのコンテキストでトランザクションが既に進行中である場合、EF ではデータを保存する前に自動的に "*セーブポイント*" が作成されます。 セーブポイントとは、エラーが発生した場合やその他の理由で、後でロールバックする可能性のある、データベース トランザクション内のポイントです。 `SaveChanges` でエラーが発生した場合、トランザクションは自動的にセーブポイントにロールバックされ、開始されていない場合と同じ状態でトランザクションが残ります。 これにより、[オプティミスティック同時実行制御](xref:core/saving/concurrency)に関する問題が発生した場合は特に、問題を修正して保存を再試行できる可能性があります。

また、トランザクションの場合と同様に、セーブポイントを手動で管理することもできます。 次の例では、トランザクション内にセーブポイントを作成し、障害発生時にそこへロールバックします。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ManagingSavepoints.cs?name=Savepoints&highlight=9,19-20)]

## <a name="cross-context-transaction"></a>クロスコンテキスト トランザクション

複数のコンテキスト インスタンス間でトランザクションを共有することもできます。 この機能は、リレーショナル データベースに固有の `DbTransaction` と `DbConnection` を使用する必要があるため、リレーショナル データベース プロバイダーを使用する場合にのみ利用できます。

トランザクションを共有するには、コンテキストが `DbConnection` と `DbTransaction` の両方を共有する必要があります。

### <a name="allow-connection-to-be-externally-provided"></a>接続の外部提供を可能にする

`DbConnection` の共有では、コンテキストの構築時に、コンテキストに接続を渡すことができるようにする必要があります。

`DbConnection` の外部提供を許可する最も簡単な方法は、`DbContext.OnConfiguring` メソッドを使用するコンテキストの構成を停止し、`DbContextOptions` を外部で作成し、それをコンテキスト コンストラクターに渡すことです。

> [!TIP]
> `DbContextOptionsBuilder` は、コンテキストを構成する `DbContext.OnConfiguring` 内で使用される API であり、その API を外部で使用して、`DbContextOptions` を作成します。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Context&highlight=3,4,5)]

別の方法は、`DbContext.OnConfiguring` を引き続き使用しますが、保存された `DbConnection` を受け取って `DbContext.OnConfiguring` で使用することです。

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

### <a name="share-connection-and-transaction"></a>接続とトランザクションの共有

同じ接続を共有する複数のコンテキスト インスタンスを作成できるようになりました。 次に、`DbContext.Database.UseTransaction(DbTransaction)` API を使用して、両方のコンテキストを同じトランザクションに参加させます。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction.cs?name=Transaction&highlight=1-3,6,14,21-23)]

## <a name="using-external-dbtransactions-relational-databases-only"></a>外部 DbTransactions の使用 (リレーショナル データベースのみ)

複数のデータ アクセス テクノロジを使用してリレーショナル データベースにアクセスしている場合、これらの異なるテクノロジによって実行される操作の間でトランザクションを共有できます。

次の例は、同じトランザクション内で ADO.NET SqlClient 操作と Entity Framework Core 操作を実行する方法を示しています。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction.cs?name=Transaction&highlight=4,9,20,25-27)]

## <a name="using-systemtransactions"></a>System.Transactions の使用

大規模なスコープで調整を行う必要がある場合は、アンビエント トランザクションを使用できます。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction.cs?name=Transaction&highlight=1,2,3,26-28)]

明示的なトランザクションに参加することもできます。

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction.cs?name=Transaction&highlight=1-2,15,28-30)]

### <a name="limitations-of-systemtransactions"></a>System.Transactions の制限

1. EF Core は、System.Transactions に対するサポートの実装をデータベース プロバイダーに依存しています。 プロバイダーが System.Transactions のサポートを実装していない場合、これらの API への呼び出しは、完全に無視される可能があります。 SqlClient ではこれがサポートされています。

   > [!IMPORTANT]
   > この API に依存してトランザクションを管理する前に、お使いのプロバイダーで API が正常に動作することをテストすることをお勧めします。 そうでない場合は、データベース プロバイダーの保守管理者に連絡することが推奨されます。

2. .NET Core 2.1 の時点では、System.Transactions の実装には、分散トランザクションのサポートは含まれていません。したがって、`TransactionScope` または `CommittableTransaction` を使用して複数のリソース マネージャー間でトランザクションを調整することはできません。
