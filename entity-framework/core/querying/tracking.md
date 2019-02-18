---
title: 追跡と追跡なしのクエリ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
uid: core/querying/tracking
ms.openlocfilehash: 6c5d516fcb3950ae168860029660e1b1061546b8
ms.sourcegitcommit: 159c2e9afed7745e7512730ffffaf154bcf2ff4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2019
ms.locfileid: "55668779"
---
# <a name="tracking-vs-no-tracking-queries"></a>追跡と追跡なしのクエリ

Entity Framework Core が、変更追跡のエンティティ インスタンスに関する情報を保持するかどうかは、追跡の動作によって制御されます。 エンティティが追跡されている場合、エンティティ内で検出された変更はすべて、`SaveChanges()` の間にデータベースに対して永続化されます。 また、Entity Framework Core は、追跡クエリから取得されたエンティティと、DbContext インスタンスに以前に読み込まれたエンティティ間のナビゲーション プロパティを修正します。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。

## <a name="tracking-queries"></a>追跡クエリ

既定では、エンティティ型を返すクエリは、追跡を行います。 つまり、それらのエンティティ インスタンスに変更を加えて、`SaveChanges()` によって永続化された変更を保持できます。

次の例では、ブログ評価に対する変更が検出され、`SaveChanges()` の間にデータベースに対して永続化されています。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.SingleOrDefault(b => b.BlogId == 1);
    blog.Rating = 5;
    context.SaveChanges();
}
```

## <a name="no-tracking-queries"></a>追跡なしのクエリ

追跡なしのクエリは、読み取り専用のシナリオで結果が使用される場合に役立ちます。 変更追跡の情報を設定する必要がないので、これらのクエリは、より迅速に実行されます。

次のように、追跡なしになるように個々のクエリをスワップできます。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=4)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .AsNoTracking()
        .ToList();
}
```

また、コンテキスト インスタンスのレベルで、既定の追跡動作を変更できます。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=3)] -->
``` csharp
using (var context = new BloggingContext())
{
    context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;

    var blogs = context.Blogs.ToList();
}
```

> [!NOTE]  
> 追跡なしのクエリでは、実行しているクエリ内で ID の解決を行います。 結果セットに同じエンティティが複数回含まれている場合、結果セット内での各同時実行に対して、エンティティ クラスの同じインスタンスが返されます。 ただし、既に返されたエンティティの追跡を継続するために、弱参照が使用されます。 同じ ID の前の結果が範囲外になっている場合は、ガベージ コレクションが実行され、新しいエンティティ インスタンスを取得できます。 詳細については、「[クエリのしくみ](overview.md)」を参照してください。

## <a name="tracking-and-projections"></a>追跡と予測

クエリの結果の型がエンティティ型ではない場合でも、結果にエンティティ型が含まれていれば、既定で引き続き追跡されます。 次のクエリでは、匿名の型が返され、結果セット内で `Blog` のインスタンスが追跡されます。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=7)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Select(b =>
            new
            {
                Blog = b,
                Posts = b.Posts.Count()
            });
}
```

結果セットにエンティティ型が含まれていない場合、追跡なしのクエリが実行されます。 次のクエリでは、エンティティからいくつかの値を持つ (ただし、実際のエンティティ型のインスタンスはない) 匿名の型が返され 、追跡は行われていません。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs
        .Select(b =>
            new
            {
                Id = b.BlogId,
                Url = b.Url
            });
}
```
