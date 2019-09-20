---
title: Azure Cosmos DB プロバイダー-制限事項-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 09/12/2019
ms.assetid: 9d02a2cd-484e-4687-b8a8-3748ba46dbc9
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 8dcc82a68c89e21ad1902a0bbbce8ebbc3535801
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71150765"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>EF Core Azure Cosmos DB プロバイダーの制限事項

Cosmos プロバイダーにはいくつかの制限があります。 これらの制限の多くは、基になる Cosmos データベースエンジンの制限の結果であり、EF に固有のものではありません。 しかし、ほとんどの場合、[まだ実装されて](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)いません。

## <a name="temporary-limitations"></a>一時的な制限事項

- コンテナーにマップされた継承を持たないエンティティ型が1つしかない場合でも、識別子プロパティは保持されます。
- パーティションキーを持つエンティティ型が、一部のシナリオで正しく機能しない
- `Include`呼び出しはサポートされていません
- `Join`呼び出しはサポートされていません

## <a name="azure-cosmos-db-sdk-limitations"></a>Azure Cosmos DB SDK の制限事項

- 非同期メソッドのみが提供されます

> [!WARNING]
> EF Core 依存する低レベルのメソッドの同期バージョンがないため、対応する機能は、返さ`.Wait()` `Task`れたに対してを呼び出すことによって現在実装されています。 つまり`SaveChanges`、、、または`ToList`非同期のメソッドを使用すると、アプリケーションでデッドロックが発生する可能性があります。

## <a name="azure-cosmos-db-limitations"></a>Azure Cosmos DB の制限事項

[Azure Cosmos DB サポートされている機能](https://docs.microsoft.com/en-us/azure/cosmos-db/modeling-data)の完全な概要については、リレーショナルデータベースと比較した場合の最も重要な違いを次に示します。

- クライアントによって開始されたトランザクションはサポートされていません
- パーティション分割されたクエリの中には、サポートされていないものや、関係する演算子によっては処理速度が遅いものがあります