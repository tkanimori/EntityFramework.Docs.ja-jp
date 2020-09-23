---
title: 関連データの明示的読み込み - EF Core
description: Entity Framework Core による関連データの明示的読み込み
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/explicit
ms.openlocfilehash: 4cfc11237b498f5357476ee4ad96fdc279cd3fee
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078881"
---
# <a name="explicit-loading-of-related-data"></a><span data-ttu-id="47b11-103">関連データの明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="47b11-103">Explicit Loading of Related Data</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="47b11-104">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="47b11-104">Explicit loading</span></span>

<span data-ttu-id="47b11-105">`DbContext.Entry(...)` API を使用してナビゲーション プロパティを明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="47b11-105">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="47b11-106">また、関連エンティティを返す個別のクエリを実行することで、ナビゲーション プロパティを明示的に読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="47b11-106">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="47b11-107">変更の追跡が有効な場合、クエリによってエンティティが具現化されると、EF Core では、既に読み込まれているエンティティがあれば参照するように、新しく読み込まれたエンティティのナビゲーション プロパティが自動的に設定されます。また、新しく読み込まれたエンティティを参照するように、既に読み込まれているエンティティのナビゲーション プロパティが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="47b11-107">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

## <a name="querying-related-entities"></a><span data-ttu-id="47b11-108">関連エンティティのクエリ</span><span class="sxs-lookup"><span data-stu-id="47b11-108">Querying related entities</span></span>

<span data-ttu-id="47b11-109">また、ナビゲーション プロパティの内容を表す LINQ クエリを取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="47b11-109">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="47b11-110">これにより、クエリに追加の演算子を適用できます。</span><span class="sxs-lookup"><span data-stu-id="47b11-110">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="47b11-111">たとえば、関連エンティティをメモリに読み込まずに集計演算子を適用できます。</span><span class="sxs-lookup"><span data-stu-id="47b11-111">Foe example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="47b11-112">関連エンティティがメモリに読み込まれるようにフィルター処理することもできます。</span><span class="sxs-lookup"><span data-stu-id="47b11-112">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]
