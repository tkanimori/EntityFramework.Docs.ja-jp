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
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>SQL Server EF Core データベースプロバイダーでのメモリ最適化テーブルのサポート

[メモリ最適化テーブル](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables)は、テーブル全体がメモリ内に存在する SQL Server の機能です。 ディスク上にはテーブル データの 2 番目のコピーが保持されますが、持続性を実現するためのものにすぎません。 メモリ最適化テーブルのデータは、データベースの復旧中にディスクからのみ読み込まれます。 たとえば、サーバーを再起動した後です。

## <a name="configuring-a-memory-optimized-table"></a>メモリ最適化テーブルの構成

エンティティがマップされるテーブルがメモリ最適化されていることを指定できます。 EF Core を使用して、モデルに基づいてデータベースを作成、メンテナンスする場合 (移行か `Database.EnsureCreated()` のいずれかを使用)、これらのエンティティ用のメモリ最適化テーブルが作成されます。

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ForSqlServerIsMemoryOptimized();
}
```
