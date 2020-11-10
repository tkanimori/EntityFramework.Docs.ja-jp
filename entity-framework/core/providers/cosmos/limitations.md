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
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="1506b-103">EF Core Azure Cosmos DB プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="1506b-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="1506b-104">Cosmos プロバイダーにはいくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="1506b-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="1506b-105">これらの制限の多くは、基になる Cosmos データベースエンジンの制限の結果であり、EF に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="1506b-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="1506b-106">しかし、ほとんどの場合、 [まだ実装されて](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)いません。</span><span class="sxs-lookup"><span data-stu-id="1506b-106">But most simply [haven't been implemented yet](https://github.com/dotnet/efcore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="1506b-107">一時的な制限事項</span><span class="sxs-lookup"><span data-stu-id="1506b-107">Temporary limitations</span></span>

- <span data-ttu-id="1506b-108">`Include` 呼び出しはサポートされていません</span><span class="sxs-lookup"><span data-stu-id="1506b-108">`Include` calls are not supported</span></span>
- <span data-ttu-id="1506b-109">`Join` 呼び出しはサポートされていません</span><span class="sxs-lookup"><span data-stu-id="1506b-109">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="1506b-110">Azure Cosmos DB SDK の制限事項</span><span class="sxs-lookup"><span data-stu-id="1506b-110">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="1506b-111">非同期メソッドのみが提供されます</span><span class="sxs-lookup"><span data-stu-id="1506b-111">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="1506b-112">EF Core 依存する低レベルのメソッドの同期バージョンがないため、対応する機能は、返されたに対してを呼び出すことによって現在実装されてい `.Wait()` `Task` ます。</span><span class="sxs-lookup"><span data-stu-id="1506b-112">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="1506b-113">つまり `SaveChanges` 、、、または非同期のメソッドを使用すると、 `ToList` アプリケーションでデッドロックが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1506b-113">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="1506b-114">Azure Cosmos DB の制限事項</span><span class="sxs-lookup"><span data-stu-id="1506b-114">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="1506b-115">[Azure Cosmos DB サポートされている機能](/azure/cosmos-db/modeling-data)の完全な概要については、リレーショナルデータベースと比較した場合の最も重要な違いを次に示します。</span><span class="sxs-lookup"><span data-stu-id="1506b-115">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="1506b-116">クライアントによって開始されたトランザクションはサポートされていません</span><span class="sxs-lookup"><span data-stu-id="1506b-116">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="1506b-117">パーティション分割されたクエリの中には、関連する演算子 (やなど) によって速度が低下するものがあります `Skip/Take` `OFFSET LIMIT` 。</span><span class="sxs-lookup"><span data-stu-id="1506b-117">Some cross-partition queries are slower depending on the operators involved (for example `Skip/Take` or `OFFSET LIMIT`)</span></span>
