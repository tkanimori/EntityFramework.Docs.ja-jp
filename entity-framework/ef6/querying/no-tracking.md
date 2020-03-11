---
title: 追跡なしのクエリ-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: f80ac260-c2dc-484d-94a3-3424fd862f8b
ms.openlocfilehash: 44d58e14a2550bd08a8edd68b467237f6f5b5978
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414477"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="5a2fd-102">追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="5a2fd-102">No-Tracking Queries</span></span>
<span data-ttu-id="5a2fd-103">場合によっては、クエリからエンティティを取得する必要がありますが、コンテキストによってそれらのエンティティを追跡することはできません。</span><span class="sxs-lookup"><span data-stu-id="5a2fd-103">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="5a2fd-104">これにより、読み取り専用のシナリオで大量のエンティティを照会するときにパフォーマンスが向上する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5a2fd-104">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="5a2fd-105">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="5a2fd-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="5a2fd-106">新しい拡張メソッド AsNoTracking では、この方法で任意のクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="5a2fd-106">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="5a2fd-107">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5a2fd-107">For example:</span></span>  

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
