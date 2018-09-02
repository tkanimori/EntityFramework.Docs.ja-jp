---
title: 基本的なクエリ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ab6e35f1-397f-41c0-9ef4-85aec5466377
uid: core/querying/basic
ms.openlocfilehash: 6a381f419cb0958ea0835070e22fe7a3212457d7
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993706"
---
# <a name="basic-queries"></a><span data-ttu-id="c8f68-102">基本的なクエリ</span><span class="sxs-lookup"><span data-stu-id="c8f68-102">Basic Queries</span></span>

<span data-ttu-id="c8f68-103">言語統合クエリ (LINQ) を使用して、データベースからエンティティを読み込む方法を説明します。</span><span class="sxs-lookup"><span data-stu-id="c8f68-103">Learn how to load entities from the database using Language Integrated Query (LINQ).</span></span>

> [!TIP]  
> <span data-ttu-id="c8f68-104">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="c8f68-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="101-linq-samples"></a><span data-ttu-id="c8f68-105">101 個の LINQ サンプル</span><span class="sxs-lookup"><span data-stu-id="c8f68-105">101 LINQ samples</span></span>

<span data-ttu-id="c8f68-106">このページでは、Entity Framework Core で一般的なタスクを実現するいくつかの例を示します。</span><span class="sxs-lookup"><span data-stu-id="c8f68-106">This page shows a few examples to achieve common tasks with Entity Framework Core.</span></span> <span data-ttu-id="c8f68-107">LINQ で何ができるかを示す広範囲なサンプルについては、「[101 LINQ Samples](https://code.msdn.microsoft.com/101-LINQ-Samples-3fb9811b)」(101 個の LINQ サンプル) 参照してください。</span><span class="sxs-lookup"><span data-stu-id="c8f68-107">For an extensive set of samples showing what is possible with LINQ, see [101 LINQ Samples](https://code.msdn.microsoft.com/101-LINQ-Samples-3fb9811b).</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="c8f68-108">すべてのデータの読み込み</span><span class="sxs-lookup"><span data-stu-id="c8f68-108">Loading all data</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.ToList();
}
```

## <a name="loading-a-single-entity"></a><span data-ttu-id="c8f68-109">単一のエンティティの読み込み</span><span class="sxs-lookup"><span data-stu-id="c8f68-109">Loading a single entity</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Single(b => b.BlogId == 1);
}
```

## <a name="filtering"></a><span data-ttu-id="c8f68-110">フィルター処理</span><span class="sxs-lookup"><span data-stu-id="c8f68-110">Filtering</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Basics/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .Where(b => b.Url.Contains("dotnet"))
        .ToList();
}
```
