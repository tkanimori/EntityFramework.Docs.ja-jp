---
title: Microsoft SQL Server データベースプロバイダー-メモリ最適化テーブル-EF Core
description: SQL Server Entity Framework Core データベースプロバイダーでメモリ最適化テーブルを使用する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/memory-optimized-tables
ms.openlocfilehash: a504fb3487aea6dd36abf204a7427095e3d29118
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414765"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a><span data-ttu-id="c6d79-103">SQL Server EF Core データベースプロバイダーでのメモリ最適化テーブルのサポート</span><span class="sxs-lookup"><span data-stu-id="c6d79-103">Memory-Optimized Tables support in SQL Server EF Core Database Provider</span></span>

<span data-ttu-id="c6d79-104">[メモリ最適化テーブル](/sql/relational-databases/in-memory-oltp/memory-optimized-tables)は、テーブル全体がメモリ内に存在する SQL Server の機能です。</span><span class="sxs-lookup"><span data-stu-id="c6d79-104">[Memory-Optimized Tables](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) are a feature of SQL Server where the entire table resides in memory.</span></span> <span data-ttu-id="c6d79-105">ディスク上にはテーブル データの 2 番目のコピーが保持されますが、持続性を実現するためのものにすぎません。</span><span class="sxs-lookup"><span data-stu-id="c6d79-105">A second copy of the table data is maintained on disk, but only for durability purposes.</span></span> <span data-ttu-id="c6d79-106">データベース復旧中は、メモリ最適化テーブルのデータはディスクからの読み取りのみ行われます。</span><span class="sxs-lookup"><span data-stu-id="c6d79-106">Data in memory-optimized tables is only read from disk during database recovery.</span></span> <span data-ttu-id="c6d79-107">たとえば、サーバー再起動後などの場合です。</span><span class="sxs-lookup"><span data-stu-id="c6d79-107">For example, after a server restart.</span></span>

## <a name="configuring-a-memory-optimized-table"></a><span data-ttu-id="c6d79-108">メモリ最適化テーブルの構成</span><span class="sxs-lookup"><span data-stu-id="c6d79-108">Configuring a memory-optimized table</span></span>

<span data-ttu-id="c6d79-109">エンティティがマップされるテーブルがメモリ最適化されていることを指定できます。</span><span class="sxs-lookup"><span data-stu-id="c6d79-109">You can specify that the table an entity is mapped to is memory-optimized.</span></span> <span data-ttu-id="c6d79-110">EF Core を使用して、([移行](xref:core/managing-schemas/migrations/index)または[EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)を使用して) モデルに基づいてデータベースを作成および管理する場合、これらのエンティティに対してメモリ最適化テーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="c6d79-110">When using EF Core to create and maintain a database based on your model (either with [migrations](xref:core/managing-schemas/migrations/index) or [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)), a memory-optimized table will be created for these entities.</span></span>

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
