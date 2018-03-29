---
title: トランザクションの EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
ms.technology: entity-framework-core
uid: core/saving/transactions
ms.openlocfilehash: fe4c0d6ad7ccb2e97dc94fbf2eb26a41e7fbcb19
ms.sourcegitcommit: 7113e8675f26cbb546200824512078bf360225df
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="using-transactions"></a>トランザクションの使用

トランザクションはアトミックな方法で処理される複数のデータベース操作を許可します。 トランザクションがコミットされた場合は、すべての操作が正常にデータベースに適用します。 トランザクションがロールバックされた場合は、データベースにどの操作も適用されます。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Transactions/)は GitHub で確認できます。

## <a name="default-transaction-behavior"></a>既定のトランザクション動作

既定では、データベース プロバイダーが、トランザクションをサポートしている場合のすべての変更を 1 回の呼び出しで`SaveChanges()`トランザクションに適用されます。 変更が失敗した場合、トランザクションがロールバックされ、どの変更は、データベースに適用します。 つまり、`SaveChanges()`完全に成功、または、データベース エラーが発生した場合は、未変更の状態のままにすることが保証されます。

ほとんどのアプリケーションでこの既定の動作で十分です。 アプリケーションの要件とみなす必要な場合に手動でのみ、トランザクションを制御する必要があります。

## <a name="controlling-transactions"></a>トランザクションを制御します。

使用することができます、`DbContext.Database`はじめに、コミット、API とのトランザクションをロールバックします。 次の例は 2 つ`SaveChanges()`操作と LINQ クエリを単一のトランザクションで実行されています。

すべてのデータベース プロバイダーは、トランザクションをサポートします。 スローする可能性が一部のプロバイダーまたはトランザクションの Api が呼び出されたときに行いません。

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

## <a name="cross-context-transaction-relational-databases-only"></a>クロス コンテキスト トランザクション (リレーショナル データベースのみ)

複数のコンテキストのインスタンス間でトランザクションを共有することもできます。 この機能は、の使用が必要とするために、リレーショナル データベース プロバイダーを使用する場合にのみ使用可能な`DbTransaction`と`DbConnection`、これは、リレーショナル データベースに固有です。

トランザクションを共有するコンテキストを共有両方、`DbConnection`と`DbTransaction`です。

### <a name="allow-connection-to-be-externally-provided"></a>提供される外部接続を許可します。

共有、`DbConnection`を構築するときに、コンテキストに接続を渡すことが必要です。

許可する最も簡単な方法`DbConnection`を外部的に提供するには、使用を停止するが、`DbContext.OnConfiguring`メソッド コンテキストを構成し、外部で作成する`DbContextOptions`コンテキスト コンス トラクターに渡すとします。

> [!TIP]  
> `DbContextOptionsBuilder` 使用される API は、`DbContext.OnConfiguring`コンテキストを構成するようになりましたしようとする外部で使用して作成する`DbContextOptions`です。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

使用を継続するという方法も`DbContext.OnConfiguring`は受け付け、`DbConnection`が保存されで使用して`DbContext.OnConfiguring`です。

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

### <a name="share-connection-and-transaction"></a>共有接続とトランザクション

同じ接続を共有する複数のコンテキストのインスタンスを作成できます。 使用して、 `DbContext.Database.UseTransaction(DbTransaction)` API を両方のコンテキストで、同じトランザクションに参加します。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a>外部 DbTransactions (リレーショナル データベースのみ) を使用します。

リレーショナル データベースへのアクセスを複数のデータ アクセス テクノロジを使用している場合は、これらのさまざまなテクノロジによって実行される操作の間でトランザクションを共有します。

次の例では、同じトランザクションで ADO.NET SqlClient 操作とエンティティ フレームワークの主要な操作を実行する方法を示します。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a>System.Transactions の使用

> [!NOTE]  
> この機能は、EF コア 2.1 の新機能です。

大きいスコープの間で調整する必要がある場合は、アンビエント トランザクションを使用して行うことができます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,24,25,26)]

明示的なトランザクションに参加することもできます。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,13,26,27,28)]

### <a name="limitations-of-systemtransactions"></a>System.Transactions の制限事項  

1. EF コアは、System.Transactions についてサポートを実装するデータベースのプロバイダーに依存します。 サポートは非常に共通の ADO.NET プロバイダーの .NET Framework、API を .NET Core 最近追加されただけし、したがってサポートはできません、広範囲にわたる。 場合は、プロバイダーは、System.Transactions についてサポートを実装していない、可能であればこれらの Api への呼び出しを完全に無視することです。 .NET Core の SqlClient では、2.1 以降からサポートことです。 .NET Core 2.0 SqlClient は例外をスローのしようとする機能を使用します。 

   > [!IMPORTANT]  
   > API が正常に動作プロバイダーでトランザクションを管理するために依存する前にテストすることをお勧めします。 そうでないデータベース プロバイダーの保守管理者に連絡することをお勧めしています。 

2. バージョン 2.1 では、.NET Core での System.Transactions の実装は含まれません分散トランザクションのサポート、したがって使用することはできません`TransactionScope`または`CommitableTransaction`複数のリソース マネージャー間でトランザクションを調整します。 
