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
# <a name="memory-optimized-tables-support-in-sql-server-ef-core-database-provider"></a>SQL Server EF Core データベースプロバイダーでのメモリ最適化テーブルのサポート

[メモリ最適化テーブル](/sql/relational-databases/in-memory-oltp/memory-optimized-tables)は、テーブル全体がメモリ内に存在する SQL Server の機能です。 ディスク上にはテーブル データの 2 番目のコピーが保持されますが、持続性を実現するためのものにすぎません。 データベース復旧中は、メモリ最適化テーブルのデータはディスクからの読み取りのみ行われます。 たとえば、サーバー再起動後などの場合です。

## <a name="configuring-a-memory-optimized-table"></a>メモリ最適化テーブルの構成

エンティティがマップされるテーブルがメモリ最適化されていることを指定できます。 EF Core を使用して、([移行](xref:core/managing-schemas/migrations/index)または[EnsureCreated](/dotnet/api/Microsoft.EntityFrameworkCore.Storage.IDatabaseCreator.EnsureCreated)を使用して) モデルに基づいてデータベースを作成および管理する場合、これらのエンティティに対してメモリ最適化テーブルが作成されます。

[!code-csharp[IsMemoryOptimized](../../../../samples/core/SqlServer/InMemory/InMemoryContext.cs?name=IsMemoryOptimized)]
