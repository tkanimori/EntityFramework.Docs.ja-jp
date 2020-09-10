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
