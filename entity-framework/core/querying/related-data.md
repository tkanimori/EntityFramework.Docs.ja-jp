---
title: 関連データの読み込み - EF Core
description: Entity Framework Core で関連データを読み込むためのさまざまな方法
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 43b8cbac1e36a37bc85c953319407b3814d7d327
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618920"
---
# <a name="loading-related-data"></a><span data-ttu-id="e61c4-103">関連データの読み込み</span><span class="sxs-lookup"><span data-stu-id="e61c4-103">Loading Related Data</span></span>

<span data-ttu-id="e61c4-104">Entity Framework Core を使用すると、モデル内でナビゲーション プロパティを使用して関連エンティティを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-104">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="e61c4-105">関連データの読み込みに使用される共通の O/RM パターンが 3 つあります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-105">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="e61c4-106">**一括読み込み**。初期クエリの一部としてデータベースから関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e61c4-106">**Eager loading** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="e61c4-107">**明示的読み込み**。後でデータベースから明示的に関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e61c4-107">**Explicit loading** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="e61c4-108">**遅延読み込み**。ナビゲーション プロパティにアクセスしたときに、データベースから透過的に関連データが読み込まれることを意味します。</span><span class="sxs-lookup"><span data-stu-id="e61c4-108">**Lazy loading** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]
> <span data-ttu-id="e61c4-109">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-109">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="eager-loading"></a><span data-ttu-id="e61c4-110">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="e61c4-110">Eager loading</span></span>

<span data-ttu-id="e61c4-111">`Include` メソッドを使用して、クエリ結果に含める関連データを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-111">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="e61c4-112">次の例では、結果で返されるブログには `Posts` プロパティに関連する投稿が設定されます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-112">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="e61c4-113">Entity Framework Core は、以前にコンテキスト インスタンスに読み込まれた他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。</span><span class="sxs-lookup"><span data-stu-id="e61c4-113">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="e61c4-114">そのため、ナビゲーション プロパティのデータを明示的に含めていない場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-114">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="e61c4-115">複数のリレーションシップの関連データを 1 つのクエリに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-115">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a><span data-ttu-id="e61c4-116">複数のレベルを含める</span><span class="sxs-lookup"><span data-stu-id="e61c4-116">Including multiple levels</span></span>

<span data-ttu-id="e61c4-117">`ThenInclude` メソッドを使用して、リレーションシップをドリル ダウンし、複数のレベルの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-117">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="e61c4-118">次の例では、すべてのブログ、関連記事、および各投稿の作成者を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-118">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="e61c4-119">`ThenInclude` に対して複数の呼び出しを連鎖させて、さらなるレベルの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-119">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="e61c4-120">これらのすべての呼び出しを組み合わせて、複数のレベルと複数のルートの関連データを同じクエリ内に含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-120">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="e61c4-121">含まれているエンティティの 1 つについて複数の関連エンティティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-121">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="e61c4-122">たとえば、`Blogs` をクエリするときに、`Posts` を含め、さらに `Posts` の `Author` と `Tags` の両方を含めたい場合があります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-122">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="e61c4-123">両方を含めるには、ルートから始まる各インクルード パスを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-123">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="e61c4-124">たとえば、`Blog -> Posts -> Author` や `Blog -> Posts -> Tags`す。</span><span class="sxs-lookup"><span data-stu-id="e61c4-124">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="e61c4-125">これで冗長的な結合を実現することにはならず、ほとんどの場合、SQL を生成するときに EF で結合は組み合わされます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-125">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="single-and-split-queries"></a><span data-ttu-id="e61c4-126">単一クエリと分割クエリ</span><span class="sxs-lookup"><span data-stu-id="e61c4-126">Single and split queries</span></span>

#### <a name="single-queries"></a><span data-ttu-id="e61c4-127">単一のクエリ</span><span class="sxs-lookup"><span data-stu-id="e61c4-127">Single queries</span></span>

<span data-ttu-id="e61c4-128">リレーショナル データベースでは、JOIN を導入するとすべての関連エンティティが既定で読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-128">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="e61c4-129">一般的なブログに複数の関連する投稿がある場合、これらの投稿の行によってブログの情報が複製され、いわゆる "デカルト爆発" の問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="e61c4-129">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="e61c4-130">さらに一対多リレーションシップが読み込まれると、重複するデータの量が増加し、アプリケーションのパフォーマンスに悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-130">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span> <span data-ttu-id="e61c4-131">既定で EF Core では、パフォーマンスの問題の原因となる可能性があるコレクション インクルードを読み込んでいるクエリが検出されると、警告が出力されます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-131">By default, EF Core emits a warning if it detects queries loading collection includes that may cause performance issues.</span></span>

#### <a name="split-queries"></a><span data-ttu-id="e61c4-132">分割クエリ</span><span class="sxs-lookup"><span data-stu-id="e61c4-132">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="e61c4-133">この機能は EF Core 5.0 で導入されています。</span><span class="sxs-lookup"><span data-stu-id="e61c4-133">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="e61c4-134">EF では、特定の LINQ クエリを複数の SQL クエリに "*分割*" するように指定できます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-134">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="e61c4-135">JOIN の代わりに、分割クエリでは、含まれている一対多のナビゲーションごとに追加の SQL クエリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-135">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="e61c4-136">これにより、次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-136">It will produce the following SQL:</span></span>

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
> <span data-ttu-id="e61c4-137">一対一の関連エンティティは、常に同じクエリ内で JOIN によって読み込まれます。パフォーマンス上の影響がないためです。</span><span class="sxs-lookup"><span data-stu-id="e61c4-137">One-to-one related entities are always loaded via JOINs in the same query, as this has no performance impact.</span></span>

#### <a name="enabling-split-queries-globally"></a><span data-ttu-id="e61c4-138">分割クエリをグローバルに有効にする</span><span class="sxs-lookup"><span data-stu-id="e61c4-138">Enabling split queries globally</span></span>

<span data-ttu-id="e61c4-139">分割クエリは、アプリケーションのコンテキストで既定値として構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-139">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="e61c4-140">分割クエリが既定値として構成されている場合でも、特定のクエリを単一のクエリとして実行するように構成することができます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-140">When split queries are configured as the default, it is still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="e61c4-141">クエリ分割モードが明示的に指定されておらず (グローバルでも、クエリ上でも)、単一のクエリによって複数のコレクション インクルードが読み込まれることが EF Core で検出された場合は、結果として生じる可能性のあるパフォーマンスの問題に注意するよう警告が出力されます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-141">If the query splitting mode isn't explicitly specified - neither globally nor on the query - and EF Core detects that a single query loads multiple collection includes, a warning is emitted to draw attention to the potential resulting performance issues.</span></span> <span data-ttu-id="e61c4-142">クエリ モードを SingleQuery に設定すると、警告は生成されません。</span><span class="sxs-lookup"><span data-stu-id="e61c4-142">Setting the query mode to SingleQuery will cause the warning not to be generated.</span></span>

#### <a name="characteristics-of-split-queries"></a><span data-ttu-id="e61c4-143">分割クエリの特性</span><span class="sxs-lookup"><span data-stu-id="e61c4-143">Characteristics of split queries</span></span>

<span data-ttu-id="e61c4-144">分割クエリによって JOIN とデカルト爆発に関連するパフォーマンス上の問題が回避されますが、いくつかの欠点もあります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-144">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="e61c4-145">ほとんどのデータベースでは単一クエリに対してデータの整合性が保証されますが、複数クエリに対してこのような保証は存在しません。</span><span class="sxs-lookup"><span data-stu-id="e61c4-145">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="e61c4-146">クエリの実行と同時にデータベースが更新された場合、生成されるデータの整合性が失われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-146">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="e61c4-147">これはシリアル化可能なトランザクションまたはスナップショット トランザクションでクエリをラップすることで軽減できますが、これにより、それ自体のパフォーマンス上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-147">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="e61c4-148">詳細については、ご利用のデータベースのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e61c4-148">For more information, see your database's documentation.</span></span>
* <span data-ttu-id="e61c4-149">現在、各クエリは、データベースに対する追加のネットワーク ラウンドトリップを意味します。</span><span class="sxs-lookup"><span data-stu-id="e61c4-149">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="e61c4-150">特にデータベースの待機時間が長い場合 (クラウド サービスなど)、複数のネットワーク ラウンドトリップによってパフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-150">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
* <span data-ttu-id="e61c4-151">一部のデータベースでは、複数のクエリの結果を同時に使用することが許可されていますが (SQL Server と MARS、Sqlite)、ほとんどの場合、特定の時点でアクティブにできるクエリは 1 つだけです。</span><span class="sxs-lookup"><span data-stu-id="e61c4-151">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="e61c4-152">そのため、後のクエリを実行する前に、前のクエリの結果をすべてアプリケーションのメモリにバッファーする必要があります。これにより、メモリ要件が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-152">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="e61c4-153">残念ながら、すべてのシナリオに適合する関連エンティティの読み込み方法はありません。</span><span class="sxs-lookup"><span data-stu-id="e61c4-153">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="e61c4-154">単一クエリと分割クエリの長所と短所を慎重に検討し、ご自身のニーズに合うものを選択してください。</span><span class="sxs-lookup"><span data-stu-id="e61c4-154">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

### <a name="filtered-include"></a><span data-ttu-id="e61c4-155">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="e61c4-155">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="e61c4-156">この機能は EF Core 5.0 で導入されています。</span><span class="sxs-lookup"><span data-stu-id="e61c4-156">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="e61c4-157">インクルードを適用して関連データを読み込む場合は、インクルードされているコレクション ナビゲーションに特定の列挙可能な操作を適用できます。これにより、結果のフィルター処理と並べ替えが可能になります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-157">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="e61c4-158">サポートされている操作は、`Where`、`OrderBy`、`OrderByDescending`、`ThenBy`、`ThenByDescending`、`Skip`、`Take` です。</span><span class="sxs-lookup"><span data-stu-id="e61c4-158">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="e61c4-159">このような操作は、次の例に示すように、インクルード メソッドに渡されるラムダのコレクション ナビゲーションに適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-159">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="e61c4-160">インクルードされている各ナビゲーションでは、フィルター操作の一意のセットが 1 つだけ許可されます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-160">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="e61c4-161">特定のコレクション ナビゲーション (次の例では `blog.Posts`) に複数のインクルード操作が適用されている場合、フィルター操作はそのいずれかでのみ指定できます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-161">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="e61c4-162">代わりに、複数回インクルードされているナビゲーションごとに、同一の操作を適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-162">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="e61c4-163">クエリの追跡では、[ナビゲーション修正](xref:core/querying/tracking)により、フィルター処理されたインクルードの結果が予期しないものになることがあります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-163">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="e61c4-164">以前にクエリされ、変更トラッカーに格納されていた関連エンティティはすべて、フィルターの要件を満たしていなくても、フィルター処理されたインクルードのクエリの結果に表示されます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-164">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="e61c4-165">そのような状況でフィルター処理されたインクルードを使用する場合は、`NoTracking` クエリを使用するか、DbContext を再作成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="e61c4-165">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="e61c4-166">例:</span><span class="sxs-lookup"><span data-stu-id="e61c4-166">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

### <a name="include-on-derived-types"></a><span data-ttu-id="e61c4-167">派生型に対するインクルード</span><span class="sxs-lookup"><span data-stu-id="e61c4-167">Include on derived types</span></span>

<span data-ttu-id="e61c4-168">`Include` と `ThenInclude` を使用して、派生型にのみ定義されているナビゲーションの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-168">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="e61c4-169">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="e61c4-169">Given the following model:</span></span>

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

<span data-ttu-id="e61c4-170">生徒である全ユーザーの `School` ナビゲーションの内容を一括して読み込むには、いくつかのパターンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-170">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="e61c4-171">キャストを使用する</span><span class="sxs-lookup"><span data-stu-id="e61c4-171">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="e61c4-172">`as` 演算子を使用する</span><span class="sxs-lookup"><span data-stu-id="e61c4-172">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="e61c4-173">型 `string` のパラメーターを受け取る `Include` のオーバーロードを使用する</span><span class="sxs-lookup"><span data-stu-id="e61c4-173">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```

## <a name="explicit-loading"></a><span data-ttu-id="e61c4-174">明示的読み込み</span><span class="sxs-lookup"><span data-stu-id="e61c4-174">Explicit loading</span></span>

<span data-ttu-id="e61c4-175">`DbContext.Entry(...)` API を使用してナビゲーション プロパティを明示的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-175">You can explicitly load a navigation property via the `DbContext.Entry(...)` API.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#Eager)]

<span data-ttu-id="e61c4-176">また、関連エンティティを返す個別のクエリを実行することで、ナビゲーション プロパティを明示的に読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-176">You can also explicitly load a navigation property by executing a separate query that returns the related entities.</span></span> <span data-ttu-id="e61c4-177">変更の追跡が有効な場合、クエリによってエンティティが具現化されると、EF Core では、既に読み込まれているエンティティがあれば参照するように、新しく読み込まれたエンティティのナビゲーション プロパティが自動的に設定されます。また、新しく読み込まれたエンティティを参照するように、既に読み込まれているエンティティのナビゲーション プロパティが自動的に設定されます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-177">If change tracking is enabled, then when query materializes an entity, EF Core will automatically set the navigation properties of the newly loaded entity to refer to any entities already loaded, and set the navigation properties of the already-loaded entities to refer to the newly loaded entity.</span></span>

### <a name="querying-related-entities"></a><span data-ttu-id="e61c4-178">関連エンティティのクエリ</span><span class="sxs-lookup"><span data-stu-id="e61c4-178">Querying related entities</span></span>

<span data-ttu-id="e61c4-179">また、ナビゲーション プロパティの内容を表す LINQ クエリを取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-179">You can also get a LINQ query that represents the contents of a navigation property.</span></span>

<span data-ttu-id="e61c4-180">これにより、クエリに追加の演算子を適用できます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-180">It allows you to apply additional operators over the query.</span></span> <span data-ttu-id="e61c4-181">たとえば、関連エンティティをメモリに読み込まずに集計演算子を適用できます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-181">Foe example applying an aggregate operator over the related entities without loading them into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

<span data-ttu-id="e61c4-182">関連エンティティがメモリに読み込まれるようにフィルター処理することもできます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-182">You can also filter which related entities are loaded into memory.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a><span data-ttu-id="e61c4-183">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="e61c4-183">Lazy loading</span></span>

<span data-ttu-id="e61c4-184">遅延読み込みを使用するには、[Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) パッケージをインストールし、`UseLazyLoadingProxies` を呼び出して有効にする方法が最も簡単です。</span><span class="sxs-lookup"><span data-stu-id="e61c4-184">The simplest way to use lazy-loading is by installing the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package and enabling it with a call to `UseLazyLoadingProxies`.</span></span> <span data-ttu-id="e61c4-185">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e61c4-185">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseLazyLoadingProxies()
        .UseSqlServer(myConnectionString);
```

<span data-ttu-id="e61c4-186">または、AddDbContext を使用する場合:</span><span class="sxs-lookup"><span data-stu-id="e61c4-186">Or when using AddDbContext:</span></span>

```csharp
.AddDbContext<BloggingContext>(
    b => b.UseLazyLoadingProxies()
          .UseSqlServer(myConnectionString));
```

<span data-ttu-id="e61c4-187">EF Core は、オーバーライド可能なナビゲーション プロパティの場合に遅延読み込みを有効にします。つまり `virtual` であり、継承可能なクラスのナビゲーション プロパティである必要があります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-187">EF Core will then enable lazy loading for any navigation property that can be overridden--that is, it must be `virtual` and on a class that can be inherited from.</span></span> <span data-ttu-id="e61c4-188">たとえば、次のエンティティでは、`Post.Blog` および `Blog.Posts` ナビゲーション プロパティの遅延読み込みが実行されます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-188">For example, in the following entities, the `Post.Blog` and `Blog.Posts` navigation properties will be lazy-loaded.</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}

public class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public virtual Blog Blog { get; set; }
}
```

### <a name="lazy-loading-without-proxies"></a><span data-ttu-id="e61c4-189">プロキシを使用しない遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="e61c4-189">Lazy loading without proxies</span></span>

<span data-ttu-id="e61c4-190">遅延読み込みプロキシは、[エンティティ型コンストラクター](xref:core/modeling/constructors)に関するページで説明されているように、エンティティに `ILazyLoader` サービスを挿入することで機能します。</span><span class="sxs-lookup"><span data-stu-id="e61c4-190">Lazy-loading proxies work by injecting the `ILazyLoader` service into an entity, as described in [Entity Type Constructors](xref:core/modeling/constructors).</span></span> <span data-ttu-id="e61c4-191">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e61c4-191">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(ILazyLoader lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private ILazyLoader LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="e61c4-192">このメソッドでは、エンティティ型を継承したり、ナビゲーション プロパティを仮想にしたりする必要はありません。コンテキストにアタッチされたときに、`new` で作成されたエンティティ インスタンスの遅延読み込みを実行することができます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-192">This method doesn't require entity types to be inherited from or navigation properties to be virtual, and allows entity instances created with `new` to lazy-load once attached to a context.</span></span> <span data-ttu-id="e61c4-193">ただし、これには、[Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) パッケージで定義される `ILazyLoader` サービスが必要です。</span><span class="sxs-lookup"><span data-stu-id="e61c4-193">However, it requires a reference to the `ILazyLoader` service, which is defined in the [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/) package.</span></span> <span data-ttu-id="e61c4-194">このパッケージに含まれる型のセットは、それに依存してもほとんど影響がないように、最低限のセットになっています。</span><span class="sxs-lookup"><span data-stu-id="e61c4-194">This package contains a minimal set of types so that there is little impact in depending on it.</span></span> <span data-ttu-id="e61c4-195">しかし、エンティティ型で EF Core パッケージへの依存を完全に回避するには、デリゲートとして `ILazyLoader.Load` メソッドを挿入することが可能です。</span><span class="sxs-lookup"><span data-stu-id="e61c4-195">However, to completely avoid depending on any EF Core packages in the entity types, it's possible to inject the `ILazyLoader.Load` method as a delegate.</span></span> <span data-ttu-id="e61c4-196">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e61c4-196">For example:</span></span>

```csharp
public class Blog
{
    private ICollection<Post> _posts;

    public Blog()
    {
    }

    private Blog(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Name { get; set; }

    public ICollection<Post> Posts
    {
        get => LazyLoader.Load(this, ref _posts);
        set => _posts = value;
    }
}

public class Post
{
    private Blog _blog;

    public Post()
    {
    }

    private Post(Action<object, string> lazyLoader)
    {
        LazyLoader = lazyLoader;
    }

    private Action<object, string> LazyLoader { get; set; }

    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }

    public Blog Blog
    {
        get => LazyLoader.Load(this, ref _blog);
        set => _blog = value;
    }
}
```

<span data-ttu-id="e61c4-197">上記のコードでは、デリゲートの使用をビット クリーナーにするために、拡張メソッド `Load` を使用しています。</span><span class="sxs-lookup"><span data-stu-id="e61c4-197">The code above uses a `Load` extension method to make using the delegate a bit cleaner:</span></span>

```csharp
public static class PocoLoadingExtensions
{
    public static TRelated Load<TRelated>(
        this Action<object, string> loader,
        object entity,
        ref TRelated navigationField,
        [CallerMemberName] string navigationName = null)
        where TRelated : class
    {
        loader?.Invoke(entity, navigationName);

        return navigationField;
    }
}
```

> [!NOTE]
> <span data-ttu-id="e61c4-198">遅延読み込みデリゲートのコンストラクター パラメーターは、"lazyLoader" と指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-198">The constructor parameter for the lazy-loading delegate must be called "lazyLoader".</span></span> <span data-ttu-id="e61c4-199">これとは別の名前を使用する構成が今後のリリースで計画されています。</span><span class="sxs-lookup"><span data-stu-id="e61c4-199">Configuration to use a different name than this is planned for a future release.</span></span>

## <a name="related-data-and-serialization"></a><span data-ttu-id="e61c4-200">関連データとシリアル化</span><span class="sxs-lookup"><span data-stu-id="e61c4-200">Related data and serialization</span></span>

<span data-ttu-id="e61c4-201">EF Core ではナビゲーション プロパティの修復が自動的に行われるので、最終的にオブジェクト グラフの循環が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-201">Because EF Core automatically does fix-up of navigation properties, you can end up with cycles in your object graph.</span></span> <span data-ttu-id="e61c4-202">たとえば、ブログとその関連する投稿を読み込むと、投稿のコレクションを参照するブログ オブジェクトになります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-202">For example, loading a blog and its related posts will result in a blog object that references a collection of posts.</span></span> <span data-ttu-id="e61c4-203">これらの各投稿には元のブログへの参照が含まれることになります。</span><span class="sxs-lookup"><span data-stu-id="e61c4-203">Each of those posts will have a reference back to the blog.</span></span>

<span data-ttu-id="e61c4-204">一部のシリアル化フレームワークでは、このような循環は許可されていません。</span><span class="sxs-lookup"><span data-stu-id="e61c4-204">Some serialization frameworks don't allow such cycles.</span></span> <span data-ttu-id="e61c4-205">たとえば、Json.NET では、循環が発生した場合に次の例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-205">For example, Json.NET will throw the following exception if a cycle is found.</span></span>

> <span data-ttu-id="e61c4-206">Newtonsoft.Json.JsonSerializationException:Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span><span class="sxs-lookup"><span data-stu-id="e61c4-206">Newtonsoft.Json.JsonSerializationException: Self referencing loop detected for property 'Blog' with type 'MyApplication.Models.Blog'.</span></span>

<span data-ttu-id="e61c4-207">ASP.NET Core を使用している場合は、オブジェクト グラフで見つかった循環を無視するように Json.NET を構成できます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-207">If you're using ASP.NET Core, you can configure Json.NET to ignore cycles that it finds in the object graph.</span></span> <span data-ttu-id="e61c4-208">この構成は、`Startup.cs` の `ConfigureServices(...)` メソッドで行われます。</span><span class="sxs-lookup"><span data-stu-id="e61c4-208">This configuration is done in the `ConfigureServices(...)` method in `Startup.cs`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```

<span data-ttu-id="e61c4-209">別の方法は、いずれかのナビゲーション プロパティを `[JsonIgnore]` 属性で装飾し、シリアル化中にそのナビゲーション プロパティを走査しないように Json.NET に指示することです。</span><span class="sxs-lookup"><span data-stu-id="e61c4-209">Another alternative is to decorate one of the navigation properties with the `[JsonIgnore]` attribute, which instructs Json.NET to not traverse that navigation property while serializing.</span></span>
