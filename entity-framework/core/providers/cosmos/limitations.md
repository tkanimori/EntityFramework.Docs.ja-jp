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
# <a name="ef-core-azure-cosmos-db-provider-limitations"></a><span data-ttu-id="74e46-102">EF Core Azure Cosmos DB プロバイダーの制限事項</span><span class="sxs-lookup"><span data-stu-id="74e46-102">EF Core Azure Cosmos DB Provider Limitations</span></span>

<span data-ttu-id="74e46-103">Cosmos プロバイダーにはいくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="74e46-103">The Cosmos provider has a number of limitations.</span></span> <span data-ttu-id="74e46-104">これらの制限の多くは、基になる Cosmos データベースエンジンの制限の結果であり、EF に固有のものではありません。</span><span class="sxs-lookup"><span data-stu-id="74e46-104">Many of these limitations are a result of limitations in the underlying Cosmos database engine and are not specific to EF.</span></span> <span data-ttu-id="74e46-105">しかし、ほとんどの場合、[まだ実装されて](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc)いません。</span><span class="sxs-lookup"><span data-stu-id="74e46-105">But most simply [haven't been implemented yet](https://github.com/aspnet/EntityFrameworkCore/issues?page=1&q=is%3Aissue+is%3Aopen+Cosmos+in%3Atitle+label%3Atype-enhancement+sort%3Areactions-%2B1-desc).</span></span>

## <a name="temporary-limitations"></a><span data-ttu-id="74e46-106">一時的な制限事項</span><span class="sxs-lookup"><span data-stu-id="74e46-106">Temporary limitations</span></span>

- <span data-ttu-id="74e46-107">コンテナーにマップされた継承を持たないエンティティ型が1つしかない場合でも、識別子プロパティは保持されます。</span><span class="sxs-lookup"><span data-stu-id="74e46-107">Even if there is only one entity type without inheritance mapped to a container it still has a discriminator property.</span></span>
- <span data-ttu-id="74e46-108">パーティションキーを持つエンティティ型が、一部のシナリオで正しく機能しない</span><span class="sxs-lookup"><span data-stu-id="74e46-108">Entity types with partition keys don't work correctly in some scenarios</span></span>
- <span data-ttu-id="74e46-109">`Include`呼び出しはサポートされていません</span><span class="sxs-lookup"><span data-stu-id="74e46-109">`Include` calls are not supported</span></span>
- <span data-ttu-id="74e46-110">`Join`呼び出しはサポートされていません</span><span class="sxs-lookup"><span data-stu-id="74e46-110">`Join` calls are not supported</span></span>

## <a name="azure-cosmos-db-sdk-limitations"></a><span data-ttu-id="74e46-111">Azure Cosmos DB SDK の制限事項</span><span class="sxs-lookup"><span data-stu-id="74e46-111">Azure Cosmos DB SDK limitations</span></span>

- <span data-ttu-id="74e46-112">非同期メソッドのみが提供されます</span><span class="sxs-lookup"><span data-stu-id="74e46-112">Only async methods are provided</span></span>

> [!WARNING]
> <span data-ttu-id="74e46-113">EF Core 依存する低レベルのメソッドの同期バージョンがないため、対応する機能は、返さ`.Wait()` `Task`れたに対してを呼び出すことによって現在実装されています。</span><span class="sxs-lookup"><span data-stu-id="74e46-113">Since there are no sync versions of the low level methods EF Core relies on, the corresponding functionality is currently implemented by calling `.Wait()` on the returned `Task`.</span></span> <span data-ttu-id="74e46-114">つまり`SaveChanges`、、、または`ToList`非同期のメソッドを使用すると、アプリケーションでデッドロックが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="74e46-114">This means that using methods like `SaveChanges`, or `ToList` instead of their async counterparts could lead to a deadlock in your application</span></span>

## <a name="azure-cosmos-db-limitations"></a><span data-ttu-id="74e46-115">Azure Cosmos DB の制限事項</span><span class="sxs-lookup"><span data-stu-id="74e46-115">Azure Cosmos DB limitations</span></span>

<span data-ttu-id="74e46-116">[Azure Cosmos DB サポートされている機能](https://docs.microsoft.com/en-us/azure/cosmos-db/modeling-data)の完全な概要については、リレーショナルデータベースと比較した場合の最も重要な違いを次に示します。</span><span class="sxs-lookup"><span data-stu-id="74e46-116">You can see the full overview of [Azure Cosmos DB supported features](https://docs.microsoft.com/en-us/azure/cosmos-db/modeling-data), these are the most notable differences compared to a relational database:</span></span>

- <span data-ttu-id="74e46-117">クライアントによって開始されたトランザクションはサポートされていません</span><span class="sxs-lookup"><span data-stu-id="74e46-117">Client-initiated transactions are not supported</span></span>
- <span data-ttu-id="74e46-118">パーティション分割されたクエリの中には、サポートされていないものや、関係する演算子によっては処理速度が遅いものがあります</span><span class="sxs-lookup"><span data-stu-id="74e46-118">Some cross-partition queries are either not supported or much slower depending on the operators involved</span></span>