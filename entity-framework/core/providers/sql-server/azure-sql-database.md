---
title: Microsoft SQL Server データベースプロバイダー-Azure SQL Database オプション-EF Core
description: SQL Server Entity Framework Core データベースプロバイダを使用して Azure SQL Database のサービス階層とパフォーマンスレベルを指定する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/sql-server/azure-sql-database
ms.openlocfilehash: c4f7b91110a0e700ed06130661e611cf45bee05f
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824786"
---
# <a name="specifying-azure-sql-database-options"></a><span data-ttu-id="f6855-103">Azure SQL Database オプションの指定</span><span class="sxs-lookup"><span data-stu-id="f6855-103">Specifying Azure SQL Database Options</span></span>

>[!NOTE]
> <span data-ttu-id="f6855-104">この API は EF Core 3.1 で新しく追加されたものです。</span><span class="sxs-lookup"><span data-stu-id="f6855-104">This API is new in EF Core 3.1.</span></span>

<span data-ttu-id="f6855-105">Azure SQL Database には、通常、Azure Portal で構成される[さまざまな価格オプション](https://azure.microsoft.com/pricing/details/sql-database/single/)が用意されています。</span><span class="sxs-lookup"><span data-stu-id="f6855-105">Azure SQL Database provides [a variety of pricing options](https://azure.microsoft.com/pricing/details/sql-database/single/) that are usually configured through the Azure Portal.</span></span> <span data-ttu-id="f6855-106">ただし、 [EF Core 移行](xref:core/managing-schemas/migrations/index)を使用してスキーマを管理している場合は、モデル自体で必要なオプションを指定できます。</span><span class="sxs-lookup"><span data-stu-id="f6855-106">However if you are managing the schema using [EF Core migrations](xref:core/managing-schemas/migrations/index) you can specify the desired options in the model itself.</span></span>

<span data-ttu-id="f6855-107">[Hasservicetier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier)を使用して、データベース (エディション) のサービスレベルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="f6855-107">You can specify the service tier of the database (EDITION) using [HasServiceTier](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasServiceTier):</span></span>

[!code-csharp[HasServiceTier](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasServiceTier)]

<span data-ttu-id="f6855-108">[Hasdatabasemaxsize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize)を使用して、データベースの最大サイズを指定できます。</span><span class="sxs-lookup"><span data-stu-id="f6855-108">You can specify the maximum size of the database using [HasDatabaseMaxSize](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasDatabaseMaxSize):</span></span>

[!code-csharp[HasDatabaseMaxSize](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasDatabaseMaxSize)]

<span data-ttu-id="f6855-109">[HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel)を使用して、データベースのパフォーマンスレベル (SERVICE_OBJECTIVE) を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f6855-109">You can specify the performance level of the database (SERVICE_OBJECTIVE) using [HasPerformanceLevel](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevel):</span></span>

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevel)]

<span data-ttu-id="f6855-110">値が文字列リテラルではないため、 [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql)を使用してエラスティックプールを構成します。</span><span class="sxs-lookup"><span data-stu-id="f6855-110">Use [HasPerformanceLevelSql](/dotnet/api/Microsoft.EntityFrameworkCore.SqlServerModelBuilderExtensions.HasPerformanceLevelSql) to configure the elastic pool, since the value is not a string literal:</span></span>

[!code-csharp[HasPerformanceLevel](../../../../samples/core/SqlServer/AzureDatabase/AzureSqlContext.cs?name=HasPerformanceLevelSql)]


>[!TIP]
> <span data-ttu-id="f6855-111">サポートされているすべての値については、 [ALTER database のドキュメント](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f6855-111">You can find all the supported values in the [ALTER DATABASE documentation](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).</span></span>