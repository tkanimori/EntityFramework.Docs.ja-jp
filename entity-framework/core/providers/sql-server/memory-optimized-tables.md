---
title: Microsoft SQL Server データベース プロバイダー - メモリ最適化テーブル - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: 63d2cbf8b69e4f1945ad60914e284fb42c48e8db
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995803"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="06a73-102">メモリ最適化テーブルでは、SQL Server EF Core データベース プロバイダーでサポートします。</span><span class="sxs-lookup"><span data-stu-id="06a73-102">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

> [!NOTE]  
>
> <span data-ttu-id="06a73-103">この機能は、EF Core 1.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="06a73-103">This feature was introduced in EF Core 1.1.</span></span>

<span data-ttu-id="06a73-104">[メモリ最適化テーブル](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables)はテーブル全体がメモリに存在する SQL Server の機能です。</span><span class="sxs-lookup"><span data-stu-id="06a73-104">[Memory-Optimized Tables](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="06a73-105">ディスク上にあっての持続性の目的でのみ、テーブル データの 2 番目のコピーが維持されます。</span><span class="sxs-lookup"><span data-stu-id="06a73-105">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="06a73-106">メモリ最適化テーブル内のデータは、データベースの復旧中にのみディスクから読み取られます。</span><span class="sxs-lookup"><span data-stu-id="06a73-106">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="06a73-107">たとえば、サーバー後を再起動します。</span><span class="sxs-lookup"><span data-stu-id="06a73-107">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="06a73-108">メモリ最適化テーブルの構成</span><span class="sxs-lookup"><span data-stu-id="06a73-108">Configuring a memory-optimized table</span></span>

<span data-ttu-id="06a73-109">エンティティがマップされるテーブルがメモリ最適化されていることを指定できます。</span><span class="sxs-lookup"><span data-stu-id="06a73-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="06a73-110">EF Core を使用して、モデルに基づいてデータベースを作成、メンテナンスする場合 (移行か `Database.EnsureCreated()` のいずれかを使用)、これらのエンティティ用のメモリ最適化テーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="06a73-110">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ForSqlServerIsMemoryOptimized();
}
```
