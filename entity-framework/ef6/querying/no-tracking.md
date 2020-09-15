---
title: 追跡なしのクエリ-EF6
description: Entity Framework 6 の追跡クエリはありません
author: divega
ms.date: 10/23/2016
uid: ef6/querying/no-tracking
ms.openlocfilehash: ea4f05eb7a9b95fba55f70f249876bc9c5630f18
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073900"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="17c68-103">追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="17c68-103">No-Tracking Queries</span></span>
<span data-ttu-id="17c68-104">場合によっては、クエリからエンティティを取得する必要がありますが、コンテキストによってそれらのエンティティを追跡することはできません。</span><span class="sxs-lookup"><span data-stu-id="17c68-104">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="17c68-105">これにより、読み取り専用のシナリオで大量のエンティティを照会するときにパフォーマンスが向上する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="17c68-105">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="17c68-106">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="17c68-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="17c68-107">新しい拡張メソッド AsNoTracking では、この方法で任意のクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="17c68-107">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="17c68-108">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="17c68-108">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs without tracking them
    var blogs1 = context.Blogs.AsNoTracking();

    // Query for some blogs without tracking them
    var blogs2 = context.Blogs
                        .Where(b => b.Name.Contains(".NET"))
                        .AsNoTracking()
                        .ToList();
}
```  
