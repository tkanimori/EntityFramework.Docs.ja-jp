---
title: カスタム移行履歴テーブル-EF Core
description: Entity Framework Core を使用した移行に使用する履歴テーブルのカスタマイズ
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: deed86b2023557036eb14ca5a66f04ab5bb4139f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617978"
---
# <a name="custom-migrations-history-table"></a><span data-ttu-id="880b3-103">カスタム移行履歴テーブル</span><span class="sxs-lookup"><span data-stu-id="880b3-103">Custom Migrations History Table</span></span>

<span data-ttu-id="880b3-104">既定では、EF Core は、データベースに適用されている移行をという名前のテーブルに記録することによって追跡し `__EFMigrationsHistory` ます。</span><span class="sxs-lookup"><span data-stu-id="880b3-104">By default, EF Core keeps track of which migrations have been applied to the database by recording them in a table named `__EFMigrationsHistory`.</span></span> <span data-ttu-id="880b3-105">さまざまな理由から、ニーズに合わせてこのテーブルをカスタマイズすることもできます。</span><span class="sxs-lookup"><span data-stu-id="880b3-105">For various reasons, you may want to customize this table to better suit your needs.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="880b3-106">移行の適用 *後* に移行履歴テーブルをカスタマイズする場合は、データベース内の既存のテーブルを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="880b3-106">If you customize the Migrations history table *after* applying migrations, you are responsible for updating the existing table in the database.</span></span>

## <a name="schema-and-table-name"></a><span data-ttu-id="880b3-107">スキーマとテーブル名</span><span class="sxs-lookup"><span data-stu-id="880b3-107">Schema and table name</span></span>

<span data-ttu-id="880b3-108">`MigrationsHistoryTable()`のメソッド `OnConfiguring()` (または ASP.NET Core) を使用して、スキーマとテーブル名を変更でき `ConfigureServices()` ます。</span><span class="sxs-lookup"><span data-stu-id="880b3-108">You can change the schema and table name using the `MigrationsHistoryTable()` method in `OnConfiguring()` (or `ConfigureServices()` on ASP.NET Core).</span></span> <span data-ttu-id="880b3-109">SQL Server EF Core プロバイダーを使用した例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="880b3-109">Here is an example using the SQL Server EF Core provider.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a><span data-ttu-id="880b3-110">その他の変更点</span><span class="sxs-lookup"><span data-stu-id="880b3-110">Other changes</span></span>

<span data-ttu-id="880b3-111">テーブルの追加の側面を構成するには、プロバイダー固有のサービスをオーバーライドして置き換え `IHistoryRepository` ます。</span><span class="sxs-lookup"><span data-stu-id="880b3-111">To configure additional aspects of the table, override and replace the provider-specific `IHistoryRepository` service.</span></span> <span data-ttu-id="880b3-112">SQL Server の MigrationId 列名を *Id* に変更する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="880b3-112">Here is an example of changing the MigrationId column name to *Id* on SQL Server.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> <span data-ttu-id="880b3-113">`SqlServerHistoryRepository` は内部名前空間内にあり、今後のリリースで変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="880b3-113">`SqlServerHistoryRepository` is inside an internal namespace and may change in future releases.</span></span>

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
