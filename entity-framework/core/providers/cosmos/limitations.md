---
title: Azure Cosmos DB プロバイダー-制限事項-EF Core
description: 他のプロバイダーと比較した Entity Framework Core Azure Cosmos DB プロバイダーの制限事項
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: f7517954b854c8ea1020c039e490b4fb0dea7181
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430197"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>EF Core Azure Cosmos DB プロバイダーの制限事項

Cosmos プロバイダーにはいくつかの制限があります。 これらの制限の多くは、基になる Cosmos データベースエンジンの制限の結果であり、EF に固有のものではありません。 しかし、ほとんどの場合、 [まだ実装されて](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)いません。

## <a name="temporary-limitations"></a>一時的な制限事項

- `Include` 呼び出しはサポートされていません
- `Join` 呼び出しはサポートされていません

## <a name="azure-cosmos-db-sdk-limitations"></a>Azure Cosmos DB SDK の制限事項

- 非同期メソッドのみが提供されます

> [!WARNING]
> EF Core 依存する低レベルのメソッドの同期バージョンがないため、対応する機能は、返されたに対してを呼び出すことによって現在実装されてい `.Wait()` `Task` ます。 つまり `SaveChanges` 、、、または非同期のメソッドを使用すると、 `ToList` アプリケーションでデッドロックが発生する可能性があります。

## <a name="azure-cosmos-db-limitations"></a>Azure Cosmos DB の制限事項

[Azure Cosmos DB サポートされている機能](/azure/cosmos-db/modeling-data)の完全な概要については、リレーショナルデータベースと比較した場合の最も重要な違いを次に示します。

- クライアントによって開始されたトランザクションはサポートされていません
- パーティション分割されたクエリの中には、関連する演算子 (やなど) によって速度が低下するものがあります `Skip/Take` `OFFSET LIMIT` 。
