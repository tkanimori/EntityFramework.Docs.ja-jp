---
title: 追跡なしのクエリ - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: f80ac260-c2dc-484d-94a3-3424fd862f8b
ms.openlocfilehash: dba4127ade9481b40d4fd3c4323532ddfedf6980
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994241"
---
# <a name="no-tracking-queries"></a><span data-ttu-id="8f62b-102">追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="8f62b-102">No-Tracking Queries</span></span>
<span data-ttu-id="8f62b-103">エンティティをクエリから戻るがコンテキストによって追跡されるエンティティはありませんする場合もあります。</span><span class="sxs-lookup"><span data-stu-id="8f62b-103">Sometimes you may want to get entities back from a query but not have those entities be tracked by the context.</span></span> <span data-ttu-id="8f62b-104">多数の読み取り専用のシナリオでのエンティティを照会するときとパフォーマンスを向上させる、この可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8f62b-104">This may result in better performance when querying for large numbers of entities in read-only scenarios.</span></span> <span data-ttu-id="8f62b-105">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="8f62b-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="8f62b-106">新しい拡張メソッド AsNoTracking により、この方法で実行されるクエリです。</span><span class="sxs-lookup"><span data-stu-id="8f62b-106">A new extension method AsNoTracking allows any query to be run in this way.</span></span> <span data-ttu-id="8f62b-107">例えば:</span><span class="sxs-lookup"><span data-stu-id="8f62b-107">For example:</span></span>  

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
