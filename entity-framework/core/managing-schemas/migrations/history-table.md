---
title: カスタム移行履歴テーブル-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: 0007da7ce3d78fd8f17007ac79a395bb2e6efd86
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655715"
---
# <a name="custom-migrations-history-table"></a><span data-ttu-id="12f32-102">カスタム移行履歴テーブル</span><span class="sxs-lookup"><span data-stu-id="12f32-102">Custom Migrations History Table</span></span>

<span data-ttu-id="12f32-103">既定では、EF Core は、データベースに適用されている移行を `__EFMigrationsHistory`という名前のテーブルに記録することによって追跡します。</span><span class="sxs-lookup"><span data-stu-id="12f32-103">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="12f32-104">さまざまな理由から、ニーズに合わせてこのテーブルをカスタマイズすることもできます。</span><span class="sxs-lookup"><span data-stu-id="12f32-104">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="12f32-105">移行の適用*後*に移行履歴テーブルをカスタマイズする場合は、データベース内の既存のテーブルを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12f32-105">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

## <a name="schema-and-table-name"></a><span data-ttu-id="12f32-106">スキーマとテーブル名</span><span class="sxs-lookup"><span data-stu-id="12f32-106">Schema and table name</span></span>

<span data-ttu-id="12f32-107">スキーマとテーブル名を変更するには、`OnConfiguring()` の `MigrationsHistoryTable()` 方法 (または ASP.NET Core で `ConfigureServices()`) を使用します。</span><span class="sxs-lookup"><span data-stu-id="12f32-107">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="12f32-108">SQL Server EF Core プロバイダーを使用した例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="12f32-108">Here is an example using the SQL Server EF Core provider.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a><span data-ttu-id="12f32-109">その他の変更</span><span class="sxs-lookup"><span data-stu-id="12f32-109">Other changes</span></span>

<span data-ttu-id="12f32-110">テーブルの追加の側面を構成するには、プロバイダー固有の `IHistoryRepository` サービスをオーバーライドして置き換えます。</span><span class="sxs-lookup"><span data-stu-id="12f32-110">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="12f32-111">SQL Server の MigrationId 列名を*Id*に変更する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="12f32-111">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> <span data-ttu-id="12f32-112">`SqlServerHistoryRepository` は内部の名前空間内にあり、今後のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="12f32-112">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
