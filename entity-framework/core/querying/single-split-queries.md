---
title: 単一クエリと分割クエリ - EF Core
description: Entity Framework Core の SQL での単一クエリと分割クエリへのクエリの変換
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/single-split-queries
ms.openlocfilehash: 1c99d931c01b99de199710ffe661e1aac7a37263
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431188"
---
# <a name="single-vs-split-queries"></a><span data-ttu-id="c918d-103">単一クエリと分割クエリ</span><span class="sxs-lookup"><span data-stu-id="c918d-103">Single vs. split queries</span></span>

## <a name="single-queries"></a><span data-ttu-id="c918d-104">単一のクエリ</span><span class="sxs-lookup"><span data-stu-id="c918d-104">Single queries</span></span>

<span data-ttu-id="c918d-105">リレーショナル データベースの場合、単一クエリで JOIN を使用すると、関連するすべてのエンティティが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="c918d-105">In relational databases, all related entities are loaded by introducing JOINs in single query.</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="c918d-106">一般的なブログに複数の関連する投稿がある場合、これらの投稿の行によってブログの情報が複製されます。</span><span class="sxs-lookup"><span data-stu-id="c918d-106">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information.</span></span> <span data-ttu-id="c918d-107">この重複により、いわゆる "デカルト爆発" の問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="c918d-107">This duplication leads to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="c918d-108">さらに一対多リレーションシップが読み込まれると、重複するデータの量が増加し、アプリケーションのパフォーマンスに悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c918d-108">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span>

## <a name="split-queries"></a><span data-ttu-id="c918d-109">分割クエリ</span><span class="sxs-lookup"><span data-stu-id="c918d-109">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="c918d-110">この機能は EF Core 5.0 で導入されています。</span><span class="sxs-lookup"><span data-stu-id="c918d-110">This feature is introduced in EF Core 5.0.</span></span> <span data-ttu-id="c918d-111">`Include` を使用した場合にのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="c918d-111">It only works when using `Include`.</span></span> <span data-ttu-id="c918d-112">[このイシュー](https://github.com/dotnet/efcore/issues/21234)により、`Include` を使用しないプロジェクションで関連データを読み込むときの分割クエリのサポートが追跡されています。</span><span class="sxs-lookup"><span data-stu-id="c918d-112">[This issue](https://github.com/dotnet/efcore/issues/21234) is tracking support for split query when loading related data in projection without `Include`.</span></span>

<span data-ttu-id="c918d-113">EF では、特定の LINQ クエリを複数の SQL クエリに "*分割*" するように指定できます。</span><span class="sxs-lookup"><span data-stu-id="c918d-113">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="c918d-114">分割クエリを使用すると、JOIN ではなく、含まれているコレクション ナビゲーションごとに追加の SQL クエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c918d-114">Instead of JOINs, split queries generate an additional SQL query for each included collection navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="c918d-115">これにより、次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="c918d-115">It will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> <span data-ttu-id="c918d-116">一対一で関連するエンティティは、パフォーマンスへの影響がないため、常に同じクエリ内で JOIN によって読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="c918d-116">One-to-one related entities are always loaded via JOINs in the same query, as it has no performance impact.</span></span>

## <a name="enabling-split-queries-globally"></a><span data-ttu-id="c918d-117">分割クエリをグローバルに有効にする</span><span class="sxs-lookup"><span data-stu-id="c918d-117">Enabling split queries globally</span></span>

<span data-ttu-id="c918d-118">分割クエリは、アプリケーションのコンテキストで既定値として構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="c918d-118">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="c918d-119">分割クエリが既定値として構成されている場合でも、特定のクエリを単一のクエリとして実行するように構成することができます。</span><span class="sxs-lookup"><span data-stu-id="c918d-119">When split queries are configured as the default, it's still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

<span data-ttu-id="c918d-120">どのような構成も存在しない場合は、単一クエリ モードが EF Core によって既定で使用されます。</span><span class="sxs-lookup"><span data-stu-id="c918d-120">EF Core uses single query mode by default in the absence of any configuration.</span></span> <span data-ttu-id="c918d-121">パフォーマンスの問題が発生する可能性があるため、次の条件が満たされるときは常に EF Core によって警告が生成されます。</span><span class="sxs-lookup"><span data-stu-id="c918d-121">Since it may cause performance issues, EF Core generates a warning whenever following conditions are met:</span></span>

- <span data-ttu-id="c918d-122">クエリで複数のコレクションが読み込まれることが EF Core によって検出された。</span><span class="sxs-lookup"><span data-stu-id="c918d-122">EF Core detects that the query loads multiple collections.</span></span>
- <span data-ttu-id="c918d-123">ユーザーがクエリ分割モードをグローバルに構成していない。</span><span class="sxs-lookup"><span data-stu-id="c918d-123">User hasn't configured query splitting mode globally.</span></span>
- <span data-ttu-id="c918d-124">ユーザーがクエリで `AsSingleQuery`/`AsSplitQuery` 演算子を使用していない。</span><span class="sxs-lookup"><span data-stu-id="c918d-124">User hasn't used `AsSingleQuery`/`AsSplitQuery` operator on the query.</span></span>

<span data-ttu-id="c918d-125">警告をオフにするには、クエリ分割モードをグローバルに、またはクエリ レベルで、適切な値に構成します。</span><span class="sxs-lookup"><span data-stu-id="c918d-125">To turn off the warning, configure query splitting mode globally or at the query level to an appropriate value.</span></span>

## <a name="characteristics-of-split-queries"></a><span data-ttu-id="c918d-126">分割クエリの特性</span><span class="sxs-lookup"><span data-stu-id="c918d-126">Characteristics of split queries</span></span>

<span data-ttu-id="c918d-127">分割クエリによって JOIN とデカルト爆発に関連するパフォーマンス上の問題が回避されますが、いくつかの欠点もあります。</span><span class="sxs-lookup"><span data-stu-id="c918d-127">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

- <span data-ttu-id="c918d-128">ほとんどのデータベースでは単一クエリに対してデータの整合性が保証されますが、複数クエリに対してこのような保証は存在しません。</span><span class="sxs-lookup"><span data-stu-id="c918d-128">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="c918d-129">クエリの実行と同時にデータベースが更新された場合、生成されるデータの整合性が失われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c918d-129">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="c918d-130">これはシリアル化可能なトランザクションまたはスナップショット トランザクションでクエリをラップすることで軽減できますが、これにより、それ自体のパフォーマンス上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c918d-130">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="c918d-131">詳細については、ご利用のデータベースのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c918d-131">For more information, see your database's documentation.</span></span>
- <span data-ttu-id="c918d-132">現在、各クエリは、データベースに対する追加のネットワーク ラウンドトリップを意味します。</span><span class="sxs-lookup"><span data-stu-id="c918d-132">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="c918d-133">特にデータベースの待機時間が長い場合 (クラウド サービスなど)、複数のネットワーク ラウンドトリップによってパフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c918d-133">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
- <span data-ttu-id="c918d-134">一部のデータベースでは、複数のクエリの結果を同時に使用することが許可されていますが (SQL Server と MARS、Sqlite)、ほとんどの場合、特定の時点でアクティブにできるクエリは 1 つだけです。</span><span class="sxs-lookup"><span data-stu-id="c918d-134">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="c918d-135">そのため、後のクエリを実行する前に、前のクエリの結果をすべてアプリケーションのメモリにバッファーする必要があります。これにより、メモリ要件が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="c918d-135">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="c918d-136">残念ながら、すべてのシナリオに適合する関連エンティティの読み込み方法はありません。</span><span class="sxs-lookup"><span data-stu-id="c918d-136">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="c918d-137">単一クエリと分割クエリの長所と短所を慎重に検討し、ニーズに合うものを選択してください。</span><span class="sxs-lookup"><span data-stu-id="c918d-137">Carefully consider the advantages and disadvantages of single and split queries to select the one that fits your needs.</span></span>
