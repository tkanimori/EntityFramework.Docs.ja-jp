---
title: "Vs を追跡します。No 追跡クエリの EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
ms.technology: entity-framework-core
uid: core/querying/tracking
ms.openlocfilehash: 9a22c893f3b1e9991560e25e0252287a2844b39e
ms.sourcegitcommit: 3b6159db8a6c0653f13c7b528367b4e69ac3d51e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
---
# <a name="tracking-vs-no-tracking-queries"></a>Vs を追跡します。No 追跡クエリ

Entity Framework Core は、変更トラッカーのエンティティのインスタンスに関する情報を保持するかどうかは、コントロールの動作を追跡します。 エンティティで検出されたすべての変更を中にデータベースに永続化のエンティティを管理している場合`SaveChanges()`です。 追跡クエリから取得されたエンティティと DbContext インスタンスに以前に読み込まれたエンティティの間エンティティ Framework Core もフィックス アップ ナビゲーション プロパティ。

> [!TIP]  
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)GitHub でします。

## <a name="tracking-queries"></a>追跡クエリ

既定では、エンティティ型を返すクエリを追跡します。 つまり、これらのエンティティのインスタンスに変更を加えることができますが、によって保存されたこれらの変更`SaveChanges()`です。

次の例では、ブログの規制への変更が検出され、中にデータベースに永続化`SaveChanges()`です。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.SingleOrDefault(b => b.BlogId == 1);
    blog.Rating = 5;
    context.SaveChanges();
}
```

## <a name="no-tracking-queries"></a>No 追跡クエリ

追跡クエリはありません便利結果を読み取り専用のシナリオで使用されます。 これらは、セットアップの変更情報を追跡する必要はありませんので、実行する方が手軽です。

なしの追跡に、個々 のクエリを交換することができます。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=4)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .AsNoTracking()
        .ToList();
}
```

既定のインスタンス レベルのコンテキストの動作を追跡を変更することもできます。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=3)] -->
``` csharp
using (var context = new BloggingContext())
{
    context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;

    var blogs = context.Blogs.ToList();
}
```

> [!NOTE]  
> 追跡クエリには引き続き実行クエリ内の id 解決を実行しません。 結果セットには、複数回には、同じエンティティが含まれています場合、は、結果セットに発生するたびに、そのエンティティ クラスの同じインスタンスが返されます。 ただし、弱い参照は、返されたエンティティの追跡に使用されます。 同じ id を持つ前の結果がスコープ外に出るし、ガベージ コレクションの実行する場合、は、エンティティの新しいインスタンスを取得可能性があります。 詳細については、次を参照してください。[クエリのしくみ](overview.md)です。

## <a name="tracking-and-projections"></a>追跡と予測

結果には、エンティティ型が含まれている場合、クエリの結果の型に、エンティティ型がされていない場合でも引き続き追跡される既定です。 次のクエリで、匿名型のインスタンスが返されます`Blog`結果セットの追跡に使用されます。

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

結果セットに、エンティティ型が含まれていない場合は、追跡は行われません。 匿名型が返されます次のクエリでは、エンティティ (ただし、実際のエンティティ型のインスタンスがない) からの値の一部ではありません追跡実行されます。

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
