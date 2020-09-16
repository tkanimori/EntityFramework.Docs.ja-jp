---
title: 関連データの読み込み - EF Core
description: Entity Framework Core で関連データを読み込むためのさまざまな方法
author: roji
ms.date: 9/11/2020
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 5c1addac6735de39d5c72f447d5d06280411a21e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078851"
---
# <a name="loading-related-data"></a><span data-ttu-id="68189-103">関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="68189-103">Loading Related Data</span></span>

<span data-ttu-id="68189-104">Entity Framework Core を使用すると、モデル内でナビゲーション プロパティを使用して関連エンティティを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="68189-104">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="68189-105">関連データの読み込みに使用される共通の O/RM パターンが 3 つあります。</span><span class="sxs-lookup"><span data-stu-id="68189-105">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="68189-106">**[一括読み込み](xref:core/querying/related-data/eager)** 。初期クエリの一部としてデータベースから関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="68189-106">**[Eager loading](xref:core/querying/related-data/eager)** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="68189-107">**[明示的読み込み](xref:core/querying/related-data/explicit)** 。後でデータベースから明示的に関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="68189-107">**[Explicit loading](xref:core/querying/related-data/explicit)** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="68189-108">**[遅延読み込み](xref:core/querying/related-data/lazy)** 。ナビゲーション プロパティにアクセスしたときに、データベースから透過的に関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="68189-108">**[Lazy loading](xref:core/querying/related-data/lazy)** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]
> <span data-ttu-id="68189-109">GitHub 上でこのセクションの下から[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)を参照できます。</span><span class="sxs-lookup"><span data-stu-id="68189-109">You can view the [samples](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) under this section on GitHub.</span></span>
