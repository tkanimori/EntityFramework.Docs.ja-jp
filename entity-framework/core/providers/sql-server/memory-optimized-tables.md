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
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>SQL Server EF Core データベースプロバイダーでの Memory-Optimized テーブルのサポート

[メモリ最適化テーブル](/sql/relational-databases/in-memory-oltp/memory-optimized-tables) は、テーブル全体がメモリ内に存在する SQL Server の機能です。 ディスク上にはテーブル データの 2 番目のコピーが保持されますが、持続性を実現するためのものにすぎません。 データベース復旧中は、メモリ最適化テーブルのデータはディスクからの読み取りのみ行われます。 たとえば、サーバー再起動後などの場合です。

## <a name="configuring-a-memory-optimized-table"></a>メモリ最適化テーブルの構成

エンティティがマップされるテーブルがメモリ最適化されていることを指定できます。 EF Core を使用して、( [移行](xref:core/managing-schemas/migrations/index) または [EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)を使用して) モデルに基づいてデータベースを作成および管理する場合、これらのエンティティに対してメモリ最適化テーブルが作成されます。

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
