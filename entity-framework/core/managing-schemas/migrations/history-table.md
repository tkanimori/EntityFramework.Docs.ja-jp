---
title: カスタム移行履歴テーブル-EF Core
description: Entity Framework Core を使用した移行に使用する履歴テーブルのカスタマイズ
author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: 1f6dcb2fbb20e7dafed17160832ccec453839aa3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062309"
---
# <a name="custom-migrations-history-table"></a>カスタム移行履歴テーブル

既定では、EF Core は、データベースに適用されている移行をという名前のテーブルに記録することによって追跡し `__EFMigrationsHistory` ます。 さまざまな理由から、ニーズに合わせてこのテーブルをカスタマイズすることもできます。

> [!IMPORTANT]
> 移行の適用 *後* に移行履歴テーブルをカスタマイズする場合は、データベース内の既存のテーブルを更新する必要があります。

## <a name="schema-and-table-name"></a>スキーマとテーブル名

`MigrationsHistoryTable()`のメソッド `OnConfiguring()` (または ASP.NET Core) を使用して、スキーマとテーブル名を変更でき `ConfigureServices()` ます。 SQL Server EF Core プロバイダーを使用した例を次に示します。

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a>その他の変更点

テーブルの追加の側面を構成するには、プロバイダー固有のサービスをオーバーライドして置き換え `IHistoryRepository` ます。 SQL Server の MigrationId 列名を *Id* に変更する例を次に示します。

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> `SqlServerHistoryRepository` は内部名前空間内にあり、今後のリリースで変更される可能性があります。

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
