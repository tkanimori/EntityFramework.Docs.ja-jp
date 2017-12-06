---
title: "Microsoft SQL Server データベース プロバイダーのメモリ最適化テーブルの EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2e007c82-c6e4-45bb-8129-851b79ec1a0a
ms.technology: entity-framework-core
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: 83a0decffc5946d036903b8b8add59f0ea31b21f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="7bddb-102">メモリ最適化テーブルで SQL Server EF コア データベース プロバイダーのサポートします。</span><span class="sxs-lookup"><span data-stu-id="7bddb-102">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

> [!NOTE]  
>
> <span data-ttu-id="7bddb-103">この機能は、EF コア 1.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="7bddb-103">This feature was introduced in EF Core 1.1.</span></span>

<span data-ttu-id="7bddb-104">[メモリ最適化テーブル](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables)テーブル全体がメモリに存在する SQL Server の機能です。</span><span class="sxs-lookup"><span data-stu-id="7bddb-104">[Memory-Optimized Tables](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="7bddb-105">ディスク上にはテーブル データの 2 番目のコピーが保持されますが、持続性を実現するためのものにすぎません。</span><span class="sxs-lookup"><span data-stu-id="7bddb-105">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="7bddb-106">データベース復旧中は、メモリ最適化テーブルのデータはディスクからの読み取りのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="7bddb-106">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="7bddb-107">たとえば、サーバー再起動後などの場合です。</span><span class="sxs-lookup"><span data-stu-id="7bddb-107">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="7bddb-108">メモリ最適化テーブルの構成</span><span class="sxs-lookup"><span data-stu-id="7bddb-108">Configuring a memory-optimized table</span></span>

<span data-ttu-id="7bddb-109">エンティティをマップするテーブルがメモリ最適化を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="7bddb-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="7bddb-110">ときに、モデルに基づく EF コアを使用して作成し、データベースを保持 (移行のいずれかまたは`Database.EnsureCreated()`)、メモリ最適化テーブルはこれらのエンティティを作成します。</span><span class="sxs-lookup"><span data-stu-id="7bddb-110">When using EF Core to create and maintain a database based on your model (either with migrations or `Database.EnsureCreated()`), a memory-optimized table will be created for these entities.</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ForSqlServerIsMemoryOptimized();
}
```
