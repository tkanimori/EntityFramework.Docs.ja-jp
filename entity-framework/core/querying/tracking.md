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
# <a name="tracking-vs-no-tracking-queries"></a><span data-ttu-id="07446-102">Vs を追跡します。No 追跡クエリ</span><span class="sxs-lookup"><span data-stu-id="07446-102">Tracking vs. No-Tracking Queries</span></span>

<span data-ttu-id="07446-103">Entity Framework Core は、変更トラッカーのエンティティのインスタンスに関する情報を保持するかどうかは、コントロールの動作を追跡します。</span><span class="sxs-lookup"><span data-stu-id="07446-103">Tracking behavior controls whether or not Entity Framework Core will keep information about an entity instance in its change tracker.</span></span> <span data-ttu-id="07446-104">エンティティで検出されたすべての変更を中にデータベースに永続化のエンティティを管理している場合`SaveChanges()`です。</span><span class="sxs-lookup"><span data-stu-id="07446-104">If an entity is tracked, any changes detected in the entity will be persisted to the database during `SaveChanges()`.</span></span> <span data-ttu-id="07446-105">追跡クエリから取得されたエンティティと DbContext インスタンスに以前に読み込まれたエンティティの間エンティティ Framework Core もフィックス アップ ナビゲーション プロパティ。</span><span class="sxs-lookup"><span data-stu-id="07446-105">Entity Framework Core will also fix-up navigation properties between entities that are obtained from a tracking query and entities that were previously loaded into the DbContext instance.</span></span>

> [!TIP]  
> <span data-ttu-id="07446-106">この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)GitHub でします。</span><span class="sxs-lookup"><span data-stu-id="07446-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="tracking-queries"></a><span data-ttu-id="07446-107">追跡クエリ</span><span class="sxs-lookup"><span data-stu-id="07446-107">Tracking queries</span></span>

<span data-ttu-id="07446-108">既定では、エンティティ型を返すクエリを追跡します。</span><span class="sxs-lookup"><span data-stu-id="07446-108">By default, queries that return entity types are tracking.</span></span> <span data-ttu-id="07446-109">つまり、これらのエンティティのインスタンスに変更を加えることができますが、によって保存されたこれらの変更`SaveChanges()`です。</span><span class="sxs-lookup"><span data-stu-id="07446-109">This means you can make changes to those entity instances and have those changes persisted by `SaveChanges()`.</span></span>

<span data-ttu-id="07446-110">次の例では、ブログの規制への変更が検出され、中にデータベースに永続化`SaveChanges()`です。</span><span class="sxs-lookup"><span data-stu-id="07446-110">In the following example, the change to the blogs rating will be detected and persisted to the database during `SaveChanges()`.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.SingleOrDefault(b => b.BlogId == 1);
    blog.Rating = 5;
    context.SaveChanges();
}
```

## <a name="no-tracking-queries"></a><span data-ttu-id="07446-111">No 追跡クエリ</span><span class="sxs-lookup"><span data-stu-id="07446-111">No-tracking queries</span></span>

<span data-ttu-id="07446-112">追跡クエリはありません便利結果を読み取り専用のシナリオで使用されます。</span><span class="sxs-lookup"><span data-stu-id="07446-112">No tracking queries are useful when the results are used in a read-only scenario.</span></span> <span data-ttu-id="07446-113">これらは、セットアップの変更情報を追跡する必要はありませんので、実行する方が手軽です。</span><span class="sxs-lookup"><span data-stu-id="07446-113">They are quicker to execute because there is no need to setup change tracking information.</span></span>

<span data-ttu-id="07446-114">なしの追跡に、個々 のクエリを交換することができます。</span><span class="sxs-lookup"><span data-stu-id="07446-114">You can swap an individual query to be no-tracking:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=4)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .AsNoTracking()
        .ToList();
}
```

<span data-ttu-id="07446-115">既定のインスタンス レベルのコンテキストの動作を追跡を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="07446-115">You can also change the default tracking behavior at the context instance level:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/Tracking/Sample.cs?highlight=3)] -->
``` csharp
using (var context = new BloggingContext())
{
    context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;

    var blogs = context.Blogs.ToList();
}
```

> [!NOTE]  
> <span data-ttu-id="07446-116">追跡クエリには引き続き実行クエリ内の id 解決を実行しません。</span><span class="sxs-lookup"><span data-stu-id="07446-116">No tracking queries still perform identity resolution within the excuting query.</span></span> <span data-ttu-id="07446-117">結果セットには、複数回には、同じエンティティが含まれています場合、は、結果セットに発生するたびに、そのエンティティ クラスの同じインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="07446-117">If the result set contains the same entity multiple times, the same instance of the entity class will be returned for each occurrence in the result set.</span></span> <span data-ttu-id="07446-118">ただし、弱い参照は、返されたエンティティの追跡に使用されます。</span><span class="sxs-lookup"><span data-stu-id="07446-118">However, weak references are used to keep track of entities that have already been returned.</span></span> <span data-ttu-id="07446-119">同じ id を持つ前の結果がスコープ外に出るし、ガベージ コレクションの実行する場合、は、エンティティの新しいインスタンスを取得可能性があります。</span><span class="sxs-lookup"><span data-stu-id="07446-119">If a previous result with the same identity goes out of scope, and garbage collection runs, you may get a new entity instance.</span></span> <span data-ttu-id="07446-120">詳細については、次を参照してください。[クエリのしくみ](overview.md)です。</span><span class="sxs-lookup"><span data-stu-id="07446-120">For more information, see [How Query Works](overview.md).</span></span>

## <a name="tracking-and-projections"></a><span data-ttu-id="07446-121">追跡と予測</span><span class="sxs-lookup"><span data-stu-id="07446-121">Tracking and projections</span></span>

<span data-ttu-id="07446-122">結果には、エンティティ型が含まれている場合、クエリの結果の型に、エンティティ型がされていない場合でも引き続き追跡される既定です。</span><span class="sxs-lookup"><span data-stu-id="07446-122">Even if the result type of the query isn't an entity type, if the result contains entity types they will still be tracked by default.</span></span> <span data-ttu-id="07446-123">次のクエリで、匿名型のインスタンスが返されます`Blog`結果セットの追跡に使用されます。</span><span class="sxs-lookup"><span data-stu-id="07446-123">In the following query, which returns an anonymous type, the instances of `Blog` in the result set will be tracked.</span></span>

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

<span data-ttu-id="07446-124">結果セットに、エンティティ型が含まれていない場合は、追跡は行われません。</span><span class="sxs-lookup"><span data-stu-id="07446-124">If the result set does not contain any entity types, then no tracking is performed.</span></span> <span data-ttu-id="07446-125">匿名型が返されます次のクエリでは、エンティティ (ただし、実際のエンティティ型のインスタンスがない) からの値の一部ではありません追跡実行されます。</span><span class="sxs-lookup"><span data-stu-id="07446-125">In the following query, which returns an anonymous type with some of the values from the entity (but no instances of the actual entity type), there is no tracking performed.</span></span>

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
