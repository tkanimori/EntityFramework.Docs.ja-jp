---
title: Microsoft SQL Server データベースプロバイダー-メモリ最適化テーブル-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: 7383e74b3f83172f9b8e0eaf9bd09d4e187e87f8
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813490"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="7d780-102">SQL Server EF Core データベースプロバイダーでのメモリ最適化テーブルのサポート</span><span class="sxs-lookup"><span data-stu-id="7d780-102">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="7d780-103">[メモリ最適化テーブル](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables)は、テーブル全体がメモリ内に存在する SQL Server の機能です。</span><span class="sxs-lookup"><span data-stu-id="7d780-103">[Memory-Optimized Tables](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="7d780-104">ディスク上にはテーブル データの 2 番目のコピーが保持されますが、持続性を実現するためのものにすぎません。</span><span class="sxs-lookup"><span data-stu-id="7d780-104">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="7d780-105">メモリ最適化テーブルのデータは、データベースの復旧中にディスクからのみ読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="7d780-105">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="7d780-106">たとえば、サーバーを再起動した後です。</span><span class="sxs-lookup"><span data-stu-id="7d780-106">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="7d780-107">メモリ最適化テーブルの構成</span><span class="sxs-lookup"><span data-stu-id="7d780-107">Configuring a memory-optimized table</span></span>

<span data-ttu-id="7d780-108">エンティティがマップされるテーブルがメモリ最適化されていることを指定できます。</span><span class="sxs-lookup"><span data-stu-id="7d780-108">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="7d780-109">EF Core を使用して、モデルに基づいてデータベースを作成、メンテナンスする場合 (移行か `Database.EnsureCreated()` のいずれかを使用)、これらのエンティティ用のメモリ最適化テーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7d780-109">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ForSqlServerIsMemoryOptimized();
}
```
