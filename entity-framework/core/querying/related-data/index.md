---
title: 関連データの読み込み - EF Core
description: Entity Framework Core で関連データを読み込むためのさまざまな方法
author: roji
ms.date: 9/11/2020
uid: core/querying/related-data
ms.openlocfilehash: a86cae91dbfbf5ebcf820c6afb72d812e278dc66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063674"
---
# <a name="loading-related-data"></a><span data-ttu-id="2d2af-103">関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="2d2af-103">Loading Related Data</span></span>

<span data-ttu-id="2d2af-104">Entity Framework Core を使用すると、モデル内でナビゲーション プロパティを使用して関連エンティティを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="2d2af-104">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="2d2af-105">関連データの読み込みに使用される共通の O/RM パターンが 3 つあります。</span><span class="sxs-lookup"><span data-stu-id="2d2af-105">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="2d2af-106">**[一括読み込み](xref:core/querying/related-data/eager)** 。初期クエリの一部としてデータベースから関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="2d2af-106">**[Eager loading](xref:core/querying/related-data/eager)** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="2d2af-107">**[明示的読み込み](xref:core/querying/related-data/explicit)** 。後でデータベースから明示的に関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="2d2af-107">**[Explicit loading](xref:core/querying/related-data/explicit)** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="2d2af-108">**[遅延読み込み](xref:core/querying/related-data/lazy)** 。ナビゲーション プロパティにアクセスしたときに、データベースから透過的に関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="2d2af-108">**[Lazy loading](xref:core/querying/related-data/lazy)** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]
> <span data-ttu-id="2d2af-109">GitHub 上でこのセクションの下から[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RelatedData)を参照できます。</span><span class="sxs-lookup"><span data-stu-id="2d2af-109">You can view the [samples](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RelatedData) under this section on GitHub.</span></span>
