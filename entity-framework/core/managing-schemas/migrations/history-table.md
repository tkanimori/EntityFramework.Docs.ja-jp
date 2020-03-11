---
title: カスタム移行履歴テーブル-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/history-table
ms.openlocfilehash: 0007da7ce3d78fd8f17007ac79a395bb2e6efd86
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414303"
---
# <a name="custom-migrations-history-table"></a>カスタム移行履歴テーブル

既定では、EF Core は、データベースに適用されている移行を `__EFMigrationsHistory`という名前のテーブルに記録することによって追跡します。 さまざまな理由から、ニーズに合わせてこのテーブルをカスタマイズすることもできます。

> [!IMPORTANT]
> 移行の適用*後*に移行履歴テーブルをカスタマイズする場合は、データベース内の既存のテーブルを更新する必要があります。

## <a name="schema-and-table-name"></a>スキーマとテーブル名

スキーマとテーブル名を変更するには、`OnConfiguring()` の `MigrationsHistoryTable()` 方法 (または ASP.NET Core で `ConfigureServices()`) を使用します。 SQL Server EF Core プロバイダーを使用した例を次に示します。

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MigrationTableNameContext.cs#TableNameContext)]

## <a name="other-changes"></a>その他の変更点

テーブルの追加の側面を構成するには、プロバイダー固有の `IHistoryRepository` サービスをオーバーライドして置き換えます。 SQL Server の MigrationId 列名を*Id*に変更する例を次に示します。

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepositoryContext)]

> [!WARNING]
> `SqlServerHistoryRepository` は内部の名前空間内にあり、今後のリリースで変更される可能性があります。

[!code-csharp[Main](../../../../samples/core/Schemas/Migrations/MyHistoryRepository.cs#HistoryRepository)]
