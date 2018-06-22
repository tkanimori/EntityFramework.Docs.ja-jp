---
title: Microsoft SQL Server データベース プロバイダーのメモリ最適化テーブルの EF コア
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
ms.locfileid: "26052642"
---
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>メモリ最適化テーブルで SQL Server EF コア データベース プロバイダーのサポートします。

> [!NOTE]  
>
> この機能は、EF コア 1.1 で導入されました。

[メモリ最適化テーブル](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/memory-optimized-tables)テーブル全体がメモリに存在する SQL Server の機能です。 ディスク上にはテーブル データの 2 番目のコピーが保持されますが、持続性を実現するためのものにすぎません。 データベース復旧中は、メモリ最適化テーブルのデータはディスクからの読み取りのみ行われます。 たとえば、サーバー再起動後などの場合です。

## <a name="configuring-a-memory-optimized-table"></a>メモリ最適化テーブルの構成

エンティティをマップするテーブルがメモリ最適化を指定することができます。 ときに、モデルに基づく EF コアを使用して作成し、データベースを保持 (移行のいずれかまたは`Database.EnsureCreated()`)、メモリ最適化テーブルはこれらのエンティティを作成します。

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .ForSqlServerIsMemoryOptimized();
}
```
