---
title: 関数のマッピング-Azure Cosmos DB Provider-EF Core
description: Azure Cosmos DB EF Core プロバイダーの関数マッピング
author: bricelam
ms.date: 1/26/2021
uid: core/providers/cosmos/functions
ms.openlocfilehash: d4d45ba7db50befb5eb775feed0af44468ee3f74
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543589"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a><span data-ttu-id="05ca1-103">Azure Cosmos DB EF Core プロバイダーの関数マッピング</span><span class="sxs-lookup"><span data-stu-id="05ca1-103">Function Mappings of the Azure Cosmos DB EF Core Provider</span></span>

<span data-ttu-id="05ca1-104">このページでは、Azure Cosmos DB プロバイダーの使用時にどの .NET メンバーがどの SQL 関数に変換されるかを示します。</span><span class="sxs-lookup"><span data-stu-id="05ca1-104">This page shows which .NET members are translated into which SQL functions when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="05ca1-105">.NET</span><span class="sxs-lookup"><span data-stu-id="05ca1-105">.NET</span></span>                          | <span data-ttu-id="05ca1-106">SQL</span><span class="sxs-lookup"><span data-stu-id="05ca1-106">SQL</span></span>                              | <span data-ttu-id="05ca1-107">追加されたバージョン:</span><span class="sxs-lookup"><span data-stu-id="05ca1-107">Added in</span></span>
----------------------------- | -------------------------------- | --------
<span data-ttu-id="05ca1-108">表す.Contains (item)</span><span class="sxs-lookup"><span data-stu-id="05ca1-108">collection.Contains(item)</span></span>     | <span data-ttu-id="05ca1-109">@item から @collection</span><span class="sxs-lookup"><span data-stu-id="05ca1-109">@item IN @collection</span></span>
<span data-ttu-id="05ca1-110">EF.Functions。 Random ()</span><span class="sxs-lookup"><span data-stu-id="05ca1-110">EF.Functions.Random()</span></span>         | <span data-ttu-id="05ca1-111">RAND ()</span><span class="sxs-lookup"><span data-stu-id="05ca1-111">RAND()</span></span>                           | <span data-ttu-id="05ca1-112">EF Core 6.0</span><span class="sxs-lookup"><span data-stu-id="05ca1-112">EF Core 6.0</span></span>
<span data-ttu-id="05ca1-113">stringValue。 Contains (値)</span><span class="sxs-lookup"><span data-stu-id="05ca1-113">stringValue.Contains(value)</span></span>   | <span data-ttu-id="05ca1-114">CONTAINS ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="05ca1-114">CONTAINS(@stringValue, @value)</span></span>   | <span data-ttu-id="05ca1-115">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="05ca1-115">EF Core 5.0</span></span>
<span data-ttu-id="05ca1-116">EndsWith (値)</span><span class="sxs-lookup"><span data-stu-id="05ca1-116">stringValue.EndsWith(value)</span></span>   | <span data-ttu-id="05ca1-117">ENDSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="05ca1-117">ENDSWITH(@stringValue, @value)</span></span>   | <span data-ttu-id="05ca1-118">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="05ca1-118">EF Core 5.0</span></span>
<span data-ttu-id="05ca1-119">FirstOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="05ca1-119">stringValue.FirstOrDefault()</span></span>  | <span data-ttu-id="05ca1-120">LEFT ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="05ca1-120">LEFT(@stringValue, 1)</span></span>            | <span data-ttu-id="05ca1-121">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="05ca1-121">EF Core 5.0</span></span>
<span data-ttu-id="05ca1-122">LastOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="05ca1-122">stringValue.LastOrDefault()</span></span>   | <span data-ttu-id="05ca1-123">RIGHT ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="05ca1-123">RIGHT(@stringValue, 1)</span></span>           | <span data-ttu-id="05ca1-124">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="05ca1-124">EF Core 5.0</span></span>
<span data-ttu-id="05ca1-125">StartsWith (値)</span><span class="sxs-lookup"><span data-stu-id="05ca1-125">stringValue.StartsWith(value)</span></span> | <span data-ttu-id="05ca1-126">STARTSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="05ca1-126">STARTSWITH(@stringValue, @value)</span></span> | <span data-ttu-id="05ca1-127">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="05ca1-127">EF Core 5.0</span></span>
