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
