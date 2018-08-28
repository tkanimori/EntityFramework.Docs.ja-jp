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
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>メモリ最適化テーブルでは、SQL Server EF Core データベース プロバイダーでサポートします。

> [!NOTE]  
>
> この機能は、EF Core 1.1 で導入されました。

[メモリ最適化テーブル](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables)はテーブル全体がメモリに存在する SQL Server の機能です。 ディスク上にあっての持続性の目的でのみ、テーブル データの 2 番目のコピーが維持されます。 メモリ最適化テーブル内のデータは、データベースの復旧中にのみディスクから読み取られます。 たとえば、サーバー後を再起動します。

## <a name="configuring-a-memory-optimized-table"></a>メモリ最適化テーブルの構成

エンティティがマップされるテーブルがメモリ最適化されていることを指定できます。 EF Core を使用して、モデルに基づいてデータベースを作成、メンテナンスする場合 (移行か `Database.EnsureCreated()` のいずれかを使用)、これらのエンティティ用のメモリ最適化テーブルが作成されます。

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ForSqlServerIsMemoryOptimized();
}
```
