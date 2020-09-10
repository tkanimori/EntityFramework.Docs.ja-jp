---
title: Azure Cosmos DB プロバイダー-制限事項-EF Core
description: 他のプロバイダーと比較した Entity Framework Core Azure Cosmos DB プロバイダーの制限事項
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/05/2019
uid: core/providers/cosmos/limitations
ms.openlocfilehash: 0b8edd9ac5fa3d684498efe222ce11a0356040b8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619277"
---
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="5c6f2-103">EF Core Azure Cosmos DB プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="5c6f2-103">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="5c6f2-104">Cosmos プロバイダーにはいくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="5c6f2-104">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="5c6f2-105">これらの制限の多くは、基になる Cosmos データベースエンジンの制限の結果であり、EF に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="5c6f2-105">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="5c6f2-106">しかし、ほとんどの場合、 [まだ実装されて](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)いません。</span><span class="sxs-lookup"><span data-stu-id="5c6f2-106">But most simply [haven't been implemented yet](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="5c6f2-107">一時的な制限事項</span><span class="sxs-lookup"><span data-stu-id="5c6f2-107">Temporary limitations</span></span>

- <span data-ttu-id="5c6f2-108">コンテナーにマップされた継承を持たないエンティティ型が1つしかない場合でも、識別子プロパティは保持されます。</span><span class="sxs-lookup"><span data-stu-id="5c6f2-108">Even if there is only one entity type without inheritance mapped to a container it still has a discriminator property.</span></span>
- <span data-ttu-id="5c6f2-109">パーティションキーを持つエンティティ型が、一部のシナリオで正しく機能しない</span><span class="sxs-lookup"><span data-stu-id="5c6f2-109">Entity types with partition keys don't work correctly in some scenarios</span></span>
- <span data-ttu-id="5c6f2-110">`Include` 呼び出しはサポートされていません</span><span class="sxs-lookup"><span data-stu-id="5c6f2-110">`Include` calls are not supported</span></span>
- <span data-ttu-id="5c6f2-111">`Join` 呼び出しはサポートされていません</span><span class="sxs-lookup"><span data-stu-id="5c6f2-111">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="5c6f2-112">Azure Cosmos DB SDK の制限事項</span><span class="sxs-lookup"><span data-stu-id="5c6f2-112">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="5c6f2-113">非同期メソッドのみが提供されます</span><span class="sxs-lookup"><span data-stu-id="5c6f2-113">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="5c6f2-114">EF Core 依存する低レベルのメソッドの同期バージョンがないため、対応する機能は、返されたに対してを呼び出すことによって現在実装されてい `.Wait()` `Task` ます。</span><span class="sxs-lookup"><span data-stu-id="5c6f2-114">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="5c6f2-115">つまり `SaveChanges` 、、、または非同期のメソッドを使用すると、 `ToList` アプリケーションでデッドロックが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5c6f2-115">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="5c6f2-116">Azure Cosmos DB の制限事項</span><span class="sxs-lookup"><span data-stu-id="5c6f2-116">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="5c6f2-117">[Azure Cosmos DB サポートされている機能](/azure/cosmos-db/modeling-data)の完全な概要については、リレーショナルデータベースと比較した場合の最も重要な違いを次に示します。</span><span class="sxs-lookup"><span data-stu-id="5c6f2-117">You can see the full overview of [Azure Cosmos DB supported features](/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="5c6f2-118">クライアントによって開始されたトランザクションはサポートされていません</span><span class="sxs-lookup"><span data-stu-id="5c6f2-118">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="5c6f2-119">パーティション分割されたクエリの中には、サポートされていないものや、関係する演算子によっては処理速度が遅いものがあります</span><span class="sxs-lookup"><span data-stu-id="5c6f2-119">Some cross-partition queries are either not supported or much slower depending on the operators involved</span></span>
