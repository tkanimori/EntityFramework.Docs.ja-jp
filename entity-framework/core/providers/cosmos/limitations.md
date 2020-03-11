---
title: Azure Cosmos DB プロバイダー-制限事項-EF Core
description: Entity Framework Core Azure Cosmos DB プロバイダーの制限事項
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 2631526b152d6ddcacf25173c8d51e4e3cb24500
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414561"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a>EF Core Azure Cosmos DB プロバイダーの制限事項

Cosmos プロバイダーにはいくつかの制限があります。 これらの制限の多くは、基になる Cosmos データベースエンジンの制限の結果であり、EF に固有のものではありません。 しかし、ほとんどの場合、[まだ実装されて](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)いません。

## <a name="temporary-limitations"></a>一時的な制限事項

- コンテナーにマップされた継承を持たないエンティティ型が1つしかない場合でも、識別子プロパティは保持されます。
- パーティションキーを持つエンティティ型が、一部のシナリオで正しく機能しない
- `Include` の呼び出しはサポートされていません
- `Join` の呼び出しはサポートされていません

## <a name="azure-cosmos-db-sdk-limitations"></a>Azure Cosmos DB SDK の制限事項

- 非同期メソッドのみが提供されます

> [!WARNING]
> EF Core 依存する低レベルのメソッドの同期バージョンは存在しないため、対応する機能は、返された `Task`で `.Wait()` を呼び出すことによって現在実装されています。 これは、`SaveChanges`のようなメソッドを使用する場合や、非同期の対応するメソッドを使用する場合 `ToList` は、アプリケーションでデッドロックが発生する可能性があることを意味します。

## <a name="azure-cosmos-db-limitations"></a>Azure Cosmos DB の制限事項

[Azure Cosmos DB サポートされている機能](/azure/cosmos-db/modeling-data)の完全な概要については、リレーショナルデータベースと比較した場合の最も重要な違いを次に示します。

- クライアントによって開始されたトランザクションはサポートされていません
- パーティション分割されたクエリの中には、サポートされていないものや、関係する演算子によっては処理速度が遅いものがあります
