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
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="a91a5-103">EF Core Azure Cosmos DB プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="a91a5-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="a91a5-104">Cosmos プロバイダーにはいくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="a91a5-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="a91a5-105">これらの制限の多くは、基になる Cosmos データベースエンジンの制限の結果であり、EF に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="a91a5-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="a91a5-106">しかし、ほとんどの場合、 [まだ実装されて](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)いません。</span><span class="sxs-lookup"><span data-stu-id="a91a5-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

<span data-ttu-id="a91a5-107">一般的に要求される機能の一部を次に示します。</span><span class="sxs-lookup"><span data-stu-id="a91a5-107">These are some of the commonly requested features:</span></span>

- [<span data-ttu-id="a91a5-108">`Include` ご</span><span class="sxs-lookup"><span data-stu-id="a91a5-108">`Include` support</span></span>](https://github.com/dotnet/efcore/issues/16920)
- [<span data-ttu-id="a91a5-109">`Join` ご</span><span class="sxs-lookup"><span data-stu-id="a91a5-109">`Join` support</span></span>](https://github.com/dotnet/efcore/issues/17314)
- [<span data-ttu-id="a91a5-110">プリミティブ型のコレクションのサポート</span><span class="sxs-lookup"><span data-stu-id="a91a5-110">Collections of primitive types support</span></span>](https://github.com/dotnet/efcore/issues/14762)

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="a91a5-111">Azure Cosmos DB SDK の制限事項</span><span class="sxs-lookup"><span data-stu-id="a91a5-111">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="a91a5-112">非同期メソッドのみが提供されます</span><span class="sxs-lookup"><span data-stu-id="a91a5-112">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="a91a5-113">EF Core 依存する低レベルのメソッドの同期バージョンがないため、対応する機能は、返されたに対してを呼び出すことによって現在実装されてい `.Wait()` `Task` ます。</span><span class="sxs-lookup"><span data-stu-id="a91a5-113">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="a91a5-114">つまり `SaveChanges` 、、、または非同期のメソッドを使用すると、 `ToList` アプリケーションでデッドロックが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a91a5-114">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="a91a5-115">Azure Cosmos DB の制限事項</span><span class="sxs-lookup"><span data-stu-id="a91a5-115">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="a91a5-116">[Azure Cosmos DB サポートされている機能](/azure/cosmos-db/modeling-data)の完全な概要については、リレーショナルデータベースと比較した場合の最も重要な違いを次に示します。</span><span class="sxs-lookup"><span data-stu-id="a91a5-116">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="a91a5-117">クライアントによって開始されたトランザクションはサポートされていません</span><span class="sxs-lookup"><span data-stu-id="a91a5-117">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="a91a5-118">パーティション分割されたクエリの中には、関連する演算子 (やなど) によって速度が低下するものがあります `Skip/Take` `OFFSET LIMIT` 。</span><span class="sxs-lookup"><span data-stu-id="a91a5-118">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
