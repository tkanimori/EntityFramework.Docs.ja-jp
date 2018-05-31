---
title: トランザクション - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
ms.technology: entity-framework-core
uid: core/saving/transactions
ms.openlocfilehash: fe4c0d6ad7ccb2e97dc94fbf2eb26a41e7fbcb19
ms.sourcegitcommit: 7113e8675f26cbb546200824512078bf360225df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
ms.locfileid: "30202498"
---
# <a name="using-transactions"></a>トランザクションの使用

トランザクションは、複数のデータベース操作をアトミックな方法で処理することを可能にします。 トランザクションがコミットされる場合は、すべての操作がデータベースに正常に適用されます。 トランザクションがロールバックされるる場合、データベースに適用される操作はありません。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Transactions/)は GitHub で確認できます。

## <a name="default-transaction-behavior"></a>既定のトランザクションの動作

既定では、データベース プロバイダーがトランザクションをサポートしている場合は、`SaveChanges()` への 1 回の呼び出しに含まれるすべての変更がトランザクションに適用されます。 いずれかの変更が失敗した場合、トランザクションはロールバックされ、変更は、データベースにまったく適用されません。 つまり、`SaveChanges()` は、完全に成功するか、エラーが発生した場合はデータベースを未変更のままにすることが保証されます。

ほとんどのアプリケーションでは、この既定の動作で十分です。 アプリケーションの要件を満たすために必要であると考えた場合にのみ、トランザクションを手動で制御する必要があります。

## <a name="controlling-transactions"></a>トランザクションを制御する

トランザクションは、`DbContext.Database` API を使用して、開始、コミット、およびロールバックできます。 次の例は、単一のトランザクションで実行される 2 つの `SaveChanges()` 操作と LINQ クエリを示しています。

すべてのデータベース プロバイダーがトランザクションをサポートしているわけではありません。 トランザクション API が呼び出された場合、一部のプロバイダーでは、例外がスローされるか、何もも実行されないことがあります。

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

## <a name="cross-context-transaction-relational-databases-only"></a>クロスコンテキスト トランザクション (リレーショナル データベースのみ)

複数のコンテキスト インスタンス間でトランザクションを共有することもできます。 この機能は、リレーショナル データベースに固有の `DbTransaction` と `DbConnection` を使用する必要があるため、リレーショナル データベース プロバイダーを使用する場合にのみ利用できます。

トランザクションを共有するには、コンテキストが `DbConnection` と `DbTransaction` の両方を共有する必要があります。

### <a name="allow-connection-to-be-externally-provided"></a>接続の外部提供を可能にする

`DbConnection` の共有では、コンテキストの構築時に、コンテキストに接続を渡すことができるようにする必要があります。

`DbConnection` の外部提供を許可する最も簡単な方法は、`DbContext.OnConfiguring` メソッドを使用するコンテキストの構成を停止し、`DbContextOptions` を外部で作成し、それをコンテキスト コンストラクターに渡すことです。

> [!TIP]  
> `DbContextOptionsBuilder` は、コンテキストを構成する `DbContext.OnConfiguring` 内で使用される API であり、その API を外部で使用して、`DbContextOptions` を作成します。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

別の方法は、`DbContext.OnConfiguring` を引き続き使用しますが、保存された `DbConnection` を受け取って `DbContext.OnConfiguring` で使用することです。

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

### <a name="share-connection-and-transaction"></a>接続とトランザクションの共有

同じ接続を共有する複数のコンテキスト インスタンスを作成できるようになりました。 次に、`DbContext.Database.UseTransaction(DbTransaction)` API を使用して、両方のコンテキストを同じトランザクションに参加させます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a>外部 DbTransactions の使用 (リレーショナル データベースのみ)

複数のデータ アクセス テクノロジを使用してリレーショナル データベースにアクセスしている場合、これらの異なるテクノロジによって実行される操作の間でトランザクションを共有できます。

次の例は、同じトランザクション内で ADO.NET SqlClient 操作と Entity Framework Core 操作を実行する方法を示しています。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a>System.Transactions の使用

> [!NOTE]  
> これは EF Core 2.1 の新機能です。

大規模なスコープで調整を行う必要がある場合は、アンビエント トランザクションを使用できます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,24,25,26)]

明示的なトランザクションに参加することもできます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,13,26,27,28)]

### <a name="limitations-of-systemtransactions"></a>System.Transactions の制限  

1. EF Core は、System.Transactions に対するサポートの実装をデータベース プロバイダーに依存しています。 .NET Framework の ADO.NET プロバイダーの間では、サポートは非常に一般的ですが、この API は .NET Core 最近追加されたため、広い範囲でサポートされているとは言えません。 プロバイダーが System.Transactions のサポートを実装していない場合、これらの API への呼び出しは、完全に無視される可能があります。 SqlClient for .NET Core では、2.1 以降、それをサポートします。 SqlClient for .NET Core 2.0 では、この機能を使用しようとすると、例外がスローされます。 

   > [!IMPORTANT]  
   > この API に依存してトランザクションを管理する前に、お使いのプロバイダーで API が正常に動作することをテストすることをお勧めします。 そうでない場合は、データベース プロバイダーの保守管理者に連絡することが推奨されます。 

2. バージョン 2.1 の時点では、.NET Core での System.Transactions の実装には、分散トランザクションのサポートは含まれていません。したがって、`TransactionScope` または `CommitableTransaction` を使用して複数のリソース マネージャー間でトランザクションを調整することはできません。 
