---
title: 追跡なしのクエリ - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: f80ac260-c2dc-484d-94a3-3424fd862f8b
ms.openlocfilehash: 44d58e14a2550bd08a8edd68b467237f6f5b5978
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490124"
---
# <a name="no-tracking-queries"></a>追跡なしのクエリ
エンティティをクエリから戻るがコンテキストによって追跡されるエンティティはありませんする場合もあります。 多数の読み取り専用のシナリオでのエンティティを照会するときとパフォーマンスを向上させる、この可能性があります。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

新しい拡張メソッド AsNoTracking により、この方法で実行されるクエリです。 例えば:  

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
