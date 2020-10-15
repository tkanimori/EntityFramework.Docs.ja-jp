---
title: Microsoft SQL Server データベースプロバイダー-Azure SQL Database オプション-EF Core
description: SQL Server Entity Framework Core データベースプロバイダを使用して Azure SQL Database のサービス階層とパフォーマンスレベルを指定する方法
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: 9b2cc5e0790beb41295dcea6ae30c4ad15b537fa
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065369"
---
# <a name="specifying-azure-sql-database-options"></a>Azure SQL Database オプションの指定

>[!NOTE]
> この API は EF Core 3.1 で新しく追加されたものです。

Azure SQL Database には、通常、Azure Portal で構成される [さまざまな価格オプション](https://azure.microsoft.com/pricing/details/sql-database/single/) が用意されています。 ただし、 [EF Core 移行](xref:core/managing-schemas/migrations/index) を使用してスキーマを管理している場合は、モデル自体で必要なオプションを指定できます。

[Hasservicetier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier)を使用して、データベース (エディション) のサービスレベルを指定できます。

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

[Hasdatabasemaxsize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize)を使用して、データベースの最大サイズを指定できます。

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

[HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel)を使用して、データベースのパフォーマンスレベル (SERVICE_OBJECTIVE) を指定できます。

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

値が文字列リテラルではないため、 [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) を使用してエラスティックプールを構成します。

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]

>[!TIP]
> サポートされているすべての値については、 [ALTER database のドキュメント](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true)を参照してください。
