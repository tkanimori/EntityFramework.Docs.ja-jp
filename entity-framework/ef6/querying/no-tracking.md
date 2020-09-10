---
title: 追跡なしのクエリ-EF6
description: Entity Framework 6 の追跡クエリはありません
author: divega
ms.date: 10/23/2016
ms.assetid: f80ac260-c2dc-484d-94a3-3424fd862f8b
uid: ef6/querying/no-tracking
ms.openlocfilehash: eb9e29c219e0cdf1e379cf8bb925f4226b1434a9
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620296"
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
