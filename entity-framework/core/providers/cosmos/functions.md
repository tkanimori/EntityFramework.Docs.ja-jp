---
title: 関数のマッピング-Azure Cosmos DB Provider-EF Core
description: Azure Cosmos DB EF Core プロバイダーの関数マッピング
author: bricelam
ms.date: 10/08/2020
uid: core/providers/cosmos/functions
ms.openlocfilehash: ffcf82fbe57ea16e6fc62f3c9c80c572b3e1ed91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066519"
---
# <a name="function-mappings-of-the-azure-cosmos-db-ef-core-provider"></a><span data-ttu-id="b314a-103">Azure Cosmos DB EF Core プロバイダーの関数マッピング</span><span class="sxs-lookup"><span data-stu-id="b314a-103">Function Mappings of the Azure Cosmos DB EF Core Provider</span></span>

<span data-ttu-id="b314a-104">このページでは、Azure Cosmos DB プロバイダーの使用時にどの .NET メンバーがどの SQL 関数に変換されるかを示します。</span><span class="sxs-lookup"><span data-stu-id="b314a-104">This page shows which .NET members are translated into which SQL functions when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="b314a-105">.NET</span><span class="sxs-lookup"><span data-stu-id="b314a-105">.NET</span></span>                          | <span data-ttu-id="b314a-106">SQL</span><span class="sxs-lookup"><span data-stu-id="b314a-106">SQL</span></span>                              | <span data-ttu-id="b314a-107">追加されたバージョン:</span><span class="sxs-lookup"><span data-stu-id="b314a-107">Added in</span></span>
----------------------------- | -------------------------------- | --------
<span data-ttu-id="b314a-108">表す.Contains (item)</span><span class="sxs-lookup"><span data-stu-id="b314a-108">collection.Contains(item)</span></span>     | <span data-ttu-id="b314a-109">@item から @collection</span><span class="sxs-lookup"><span data-stu-id="b314a-109">@item IN @collection</span></span>
<span data-ttu-id="b314a-110">stringValue。 Contains (値)</span><span class="sxs-lookup"><span data-stu-id="b314a-110">stringValue.Contains(value)</span></span>   | <span data-ttu-id="b314a-111">CONTAINS ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="b314a-111">CONTAINS(@stringValue, @value)</span></span>   | <span data-ttu-id="b314a-112">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="b314a-112">EF Core 5.0</span></span>
<span data-ttu-id="b314a-113">EndsWith (値)</span><span class="sxs-lookup"><span data-stu-id="b314a-113">stringValue.EndsWith(value)</span></span>   | <span data-ttu-id="b314a-114">ENDSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="b314a-114">ENDSWITH(@stringValue, @value)</span></span>   | <span data-ttu-id="b314a-115">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="b314a-115">EF Core 5.0</span></span>
<span data-ttu-id="b314a-116">FirstOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="b314a-116">stringValue.FirstOrDefault()</span></span>  | <span data-ttu-id="b314a-117">LEFT ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="b314a-117">LEFT(@stringValue, 1)</span></span>            | <span data-ttu-id="b314a-118">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="b314a-118">EF Core 5.0</span></span>
<span data-ttu-id="b314a-119">LastOrDefault ()</span><span class="sxs-lookup"><span data-stu-id="b314a-119">stringValue.LastOrDefault()</span></span>   | <span data-ttu-id="b314a-120">RIGHT ( @stringValue , 1)</span><span class="sxs-lookup"><span data-stu-id="b314a-120">RIGHT(@stringValue, 1)</span></span>           | <span data-ttu-id="b314a-121">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="b314a-121">EF Core 5.0</span></span>
<span data-ttu-id="b314a-122">StartsWith (値)</span><span class="sxs-lookup"><span data-stu-id="b314a-122">stringValue.StartsWith(value)</span></span> | <span data-ttu-id="b314a-123">STARTSWITH ( @stringValue , @value )</span><span class="sxs-lookup"><span data-stu-id="b314a-123">STARTSWITH(@stringValue, @value)</span></span> | <span data-ttu-id="b314a-124">EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="b314a-124">EF Core 5.0</span></span>
