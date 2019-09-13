---
title: 追跡と追跡なしのクエリ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e17e060c-929f-4180-8883-40c438fbcc01
uid: core/querying/tracking
ms.openlocfilehash: d93be5c2b727d8fbaddd103f8f367c699ae80a7c
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921654"
---
# <a name="tracking-vs-no-tracking-queries"></a><span data-ttu-id="c480c-102">追跡と追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="c480c-102">Tracking vs. No-Tracking Queries</span></span>

<span data-ttu-id="c480c-103">Entity Framework Core が、変更追跡のエンティティ インスタンスに関する情報を保持するかどうかは、追跡の動作によって制御されます。</span><span class="sxs-lookup"><span data-stu-id="c480c-103">Tracking behavior controls whether or not Entity Framework Core will keep information about an entity instance in its change tracker.</span></span> <span data-ttu-id="c480c-104">エンティティが追跡されている場合、エンティティ内で検出された変更はすべて、`SaveChanges()` の間にデータベースに対して永続化されます。</span><span class="sxs-lookup"><span data-stu-id="c480c-104">If an entity is tracked, any changes detected in the entity will be persisted to the database during `SaveChanges()`.</span></span> <span data-ttu-id="c480c-105">また、Entity Framework Core は、追跡クエリから取得されたエンティティと、DbContext インスタンスに以前に読み込まれたエンティティ間のナビゲーション プロパティを修正します。</span><span class="sxs-lookup"><span data-stu-id="c480c-105">Entity Framework Core will also fix-up navigation properties between entities that are obtained from a tracking query and entities that were previously loaded into the DbContext instance.</span></span>

> [!TIP]  
> <span data-ttu-id="c480c-106">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="c480c-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="tracking-queries"></a><span data-ttu-id="c480c-107">追跡クエリ</span><span class="sxs-lookup"><span data-stu-id="c480c-107">Tracking queries</span></span>

<span data-ttu-id="c480c-108">既定では、エンティティ型を返すクエリは、追跡を行います。</span><span class="sxs-lookup"><span data-stu-id="c480c-108">By default, queries that return entity types are tracking.</span></span> <span data-ttu-id="c480c-109">つまり、それらのエンティティ インスタンスに変更を加えて、`SaveChanges()` によって永続化された変更を保持できます。</span><span class="sxs-lookup"><span data-stu-id="c480c-109">This means you can make changes to those entity instances and have those changes persisted by `SaveChanges()`.</span></span>

<span data-ttu-id="c480c-110">次の例では、ブログ評価に対する変更が検出され、`SaveChanges()` の間にデータベースに対して永続化されています。</span><span class="sxs-lookup"><span data-stu-id="c480c-110">In the following example, the change to the blogs rating will be detected and persisted to the database during `SaveChanges()`.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Tracking/Sample.cs)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.SingleOrDefault(b => b.BlogId == 1);
    blog.Rating = 5;
    context.SaveChanges();
}
```

## <a name="no-tracking-queries"></a><span data-ttu-id="c480c-111">追跡なしのクエリ</span><span class="sxs-lookup"><span data-stu-id="c480c-111">No-tracking queries</span></span>

<span data-ttu-id="c480c-112">追跡なしのクエリは、読み取り専用のシナリオで結果が使用される場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="c480c-112">No tracking queries are useful when the results are used in a read-only scenario.</span></span> <span data-ttu-id="c480c-113">変更追跡の情報を設定する必要がないので、これらのクエリは、より迅速に実行されます。</span><span class="sxs-lookup"><span data-stu-id="c480c-113">They are quicker to execute because there is no need to setup change tracking information.</span></span>

<span data-ttu-id="c480c-114">次のように、追跡なしになるように個々のクエリをスワップできます。</span><span class="sxs-lookup"><span data-stu-id="c480c-114">You can swap an individual query to be no-tracking:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Tracking/Sample.cs?highlight=4)] -->
``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs
        .AsNoTracking()
        .ToList();
}
```

<span data-ttu-id="c480c-115">また、コンテキスト インスタンスのレベルで、既定の追跡動作を変更できます。</span><span class="sxs-lookup"><span data-stu-id="c480c-115">You can also change the default tracking behavior at the context instance level:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Tracking/Sample.cs?highlight=3)] -->
``` csharp
using (var context = new BloggingContext())
{
    context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;

    var blogs = context.Blogs.ToList();
}
```

> [!NOTE]  
> <span data-ttu-id="c480c-116">追跡なしのクエリでは、実行しているクエリ内で ID の解決を行います。</span><span class="sxs-lookup"><span data-stu-id="c480c-116">No tracking queries still perform identity resolution within the executing query.</span></span> <span data-ttu-id="c480c-117">結果セットに同じエンティティが複数回含まれている場合、結果セット内での各同時実行に対して、エンティティ クラスの同じインスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="c480c-117">If the result set contains the same entity multiple times, the same instance of the entity class will be returned for each occurrence in the result set.</span></span> <span data-ttu-id="c480c-118">ただし、既に返されたエンティティの追跡を継続するために、弱参照が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c480c-118">However, weak references are used to keep track of entities that have already been returned.</span></span> <span data-ttu-id="c480c-119">同じ ID の前の結果が範囲外になっている場合は、ガベージ コレクションが実行され、新しいエンティティ インスタンスを取得できます。</span><span class="sxs-lookup"><span data-stu-id="c480c-119">If a previous result with the same identity goes out of scope, and garbage collection runs, you may get a new entity instance.</span></span> <span data-ttu-id="c480c-120">詳細については、「[クエリのしくみ](overview.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c480c-120">For more information, see [How Query Works](overview.md).</span></span>

## <a name="tracking-and-projections"></a><span data-ttu-id="c480c-121">追跡と予測</span><span class="sxs-lookup"><span data-stu-id="c480c-121">Tracking and projections</span></span>

<span data-ttu-id="c480c-122">クエリの結果の型がエンティティ型ではない場合でも、結果にエンティティ型が含まれていれば、既定で引き続き追跡されます。</span><span class="sxs-lookup"><span data-stu-id="c480c-122">Even if the result type of the query isn't an entity type, if the result contains entity types they will still be tracked by default.</span></span> <span data-ttu-id="c480c-123">次のクエリでは、匿名の型が返され、結果セット内で `Blog` のインスタンスが追跡されます。</span><span class="sxs-lookup"><span data-stu-id="c480c-123">In the following query, which returns an anonymous type, the instances of `Blog` in the result set will be tracked.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Tracking/Sample.cs?highlight=7)] -->
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

<span data-ttu-id="c480c-124">結果セットにエンティティ型が含まれていない場合、追跡なしのクエリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="c480c-124">If the result set does not contain any entity types, then no tracking is performed.</span></span> <span data-ttu-id="c480c-125">次のクエリでは、エンティティからいくつかの値を持つ (ただし、実際のエンティティ型のインスタンスはない) 匿名の型が返され 、追跡は行われていません。</span><span class="sxs-lookup"><span data-stu-id="c480c-125">In the following query, which returns an anonymous type with some of the values from the entity (but no instances of the actual entity type), there is no tracking performed.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Tracking/Sample.cs)] -->
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
