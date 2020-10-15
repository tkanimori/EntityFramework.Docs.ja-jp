---
title: Microsoft SQL Server データベースプロバイダー-Memory-Optimized テーブル-EF Core
description: SQL Server Entity Framework Core データベースプロバイダーで Memory-Optimized テーブルを使用する方法
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: 42b2a2ffafb2234e1ce7a6d0844234509a4b5b94
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063921"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="a2839-103">SQL Server EF Core データベースプロバイダーでの Memory-Optimized テーブルのサポート</span><span class="sxs-lookup"><span data-stu-id="a2839-103">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="a2839-104">[メモリ最適化テーブル](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) は、テーブル全体がメモリ内に存在する SQL Server の機能です。</span><span class="sxs-lookup"><span data-stu-id="a2839-104">[Memory-Optimized Tables](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="a2839-105">ディスク上にはテーブル データの 2 番目のコピーが保持されますが、持続性を実現するためのものにすぎません。</span><span class="sxs-lookup"><span data-stu-id="a2839-105">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="a2839-106">データベース復旧中は、メモリ最適化テーブルのデータはディスクからの読み取りのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="a2839-106">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="a2839-107">たとえば、サーバー再起動後などの場合です。</span><span class="sxs-lookup"><span data-stu-id="a2839-107">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="a2839-108">メモリ最適化テーブルの構成</span><span class="sxs-lookup"><span data-stu-id="a2839-108">Configuring a memory-optimized table</span></span>

<span data-ttu-id="a2839-109">エンティティがマップされるテーブルがメモリ最適化されていることを指定できます。</span><span class="sxs-lookup"><span data-stu-id="a2839-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="a2839-110">EF Core を使用して、( [移行](xref:core/managing-schemas/migrations/index) または [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)を使用して) モデルに基づいてデータベースを作成および管理する場合、これらのエンティティに対してメモリ最適化テーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a2839-110">When using EF Core to create and maintain a database based on your model (either with [migrations](xref:core/managing-schemas/migrations/index) or [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), a memory-optimized table will be created for these entities.</span></span>

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
