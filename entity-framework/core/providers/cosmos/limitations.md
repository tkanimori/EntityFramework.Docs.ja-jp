---
title: Azure Cosmos DB プロバイダー-制限事項-EF Core
description: 他のプロバイダーと比較した Entity Framework Core Azure Cosmos DB プロバイダーの制限事項
author: AndriySvyryd
ms.date: 11/05/2020
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 088f593dddd9b5691d87566d7e31a699ba90d7c5
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635719"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>EF Core Azure Cosmos DB プロバイダーの制限事項

Cosmos プロバイダーにはいくつかの制限があります。 これらの制限の多くは、基になる Cosmos データベースエンジンの制限の結果であり、EF に固有のものではありません。 しかし、ほとんどの場合、 [まだ実装されて](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)いません。

一般的に要求される機能の一部を次に示します。

- [`Include` ご](https://github.com/dotnet/efcore/issues/16920)
- [`Join` ご](https://github.com/dotnet/efcore/issues/17314)
- [プリミティブ型のコレクションのサポート](https://github.com/dotnet/efcore/issues/14762)

## <a name="azure-cosmos-db-sdk-limitations"></a>Azure Cosmos DB SDK の制限事項

- 非同期メソッドのみが提供されます

> [!WARNING]
> EF Core 依存する低レベルのメソッドの同期バージョンがないため、対応する機能は、返されたに対してを呼び出すことによって現在実装されてい `.Wait()` `Task` ます。 つまり `SaveChanges` 、、、または非同期のメソッドを使用すると、 `ToList` アプリケーションでデッドロックが発生する可能性があります。

## <a name="azure-cosmos-db-limitations"></a>Azure Cosmos DB の制限事項

[Azure Cosmos DB サポートされている機能](/azure/cosmos-db/modeling-data)の完全な概要については、リレーショナルデータベースと比較した場合の最も重要な違いを次に示します。

- クライアントによって開始されたトランザクションはサポートされていません
- パーティション分割されたクエリの中には、関連する演算子 (やなど) によって速度が低下するものがあります `Skip/Take` `OFFSET LIMIT` 。
