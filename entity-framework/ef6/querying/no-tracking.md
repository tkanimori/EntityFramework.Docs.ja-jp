---
title: クエリの No-Tracking-EF6
description: Entity Framework 6 でのクエリの No-Tracking
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/querying/no-tracking
ms.openlocfilehash: 74826d3052cf6a249796db2845fa6b96e5ecb8f9
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065863"
---
# <a name="no-tracking-queries"></a>追跡なしのクエリ
場合によっては、クエリからエンティティを取得する必要がありますが、コンテキストによってそれらのエンティティを追跡することはできません。 これにより、読み取り専用のシナリオで大量のエンティティを照会するときにパフォーマンスが向上する可能性があります。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

新しい拡張メソッド AsNoTracking では、この方法で任意のクエリを実行できます。 次に例を示します。  

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
