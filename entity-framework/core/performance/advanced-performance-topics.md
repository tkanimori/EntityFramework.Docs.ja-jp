---
title: 高度なパフォーマンスのトピック
description: Entity Framework Core のパフォーマンスに関する詳細トピック
author: rick-anderson
ms.author: riande
ms.date: 12/9/2020
uid: core/performance/advanced-performance-topics
ms.openlocfilehash: 3c0340e1b36cbbb96d23db0633cb2eebc04dd970
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657763"
---
# <a name="advanced-performance-topics"></a><span data-ttu-id="1d3a9-103">高度なパフォーマンスのトピック</span><span class="sxs-lookup"><span data-stu-id="1d3a9-103">Advanced Performance Topics</span></span>

## <a name="dbcontext-pooling"></a><span data-ttu-id="1d3a9-104">DbContext プール</span><span class="sxs-lookup"><span data-stu-id="1d3a9-104">DbContext pooling</span></span>

<span data-ttu-id="1d3a9-105">`AddDbContextPool` インスタンスのプールを有効に `DbContext` します。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-105">`AddDbContextPool` enables pooling of `DbContext` instances.</span></span> <span data-ttu-id="1d3a9-106">コンテキストプーリングは、要求ごとに新しいインスタンスを作成するのではなく、コンテキストインスタンスを再利用することで、web サーバーなどの大規模なシナリオでスループットを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-106">Context pooling can increase throughput in high-scale scenarios such as web servers by reusing context instances, rather than creating new instances for each request.</span></span>

<span data-ttu-id="1d3a9-107">EF Core を使用する ASP.NET Core アプリの一般的なパターンには、カスタム <xref:Microsoft.EntityFrameworkCore.DbContext> 型を [依存関係挿入](/aspnet/core/fundamentals/dependency-injection) コンテナーに登録し、コントローラーまたは Razor Pages のコンストラクターパラメーターを使用してその型のインスタンスを取得する作業が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-107">The typical pattern in an ASP.NET Core app using EF Core involves registering a custom <xref:Microsoft.EntityFrameworkCore.DbContext> type into the [dependency injection](/aspnet/core/fundamentals/dependency-injection) container and obtaining instances of that type through constructor parameters in controllers or Razor Pages.</span></span> <span data-ttu-id="1d3a9-108">コンストラクターインジェクションを使用して、要求ごとに新しいコンテキストインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-108">Using constructor injection, a new context instance is created for each request.</span></span>

<span data-ttu-id="1d3a9-109"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 再利用可能なコンテキストインスタンスのプールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-109"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> enables a pool of reusable context instances.</span></span> <span data-ttu-id="1d3a9-110">コンテキストプーリングを使用するには、 `AddDbContextPool` サービスの登録時にではなく、メソッドを使用し `AddDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-110">To use context pooling, use the `AddDbContextPool` method instead of `AddDbContext` during service registration:</span></span>

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="1d3a9-111">を使用すると、コンテキストインスタンスが要求されたときに `AddDbContextPool` 、EF は、プール内に使用可能なインスタンスがあるかどうかを最初に確認します。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-111">When `AddDbContextPool` is used, at the time a context instance is requested, EF first checks if there is an instance available in the pool.</span></span> <span data-ttu-id="1d3a9-112">要求処理が終わると、インスタンス上のあらゆる状態がリセットされ、インスタンス自体はプールに戻ります。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-112">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="1d3a9-113">これは、ADO.NET プロバイダーでの接続プールの動作と概念的に似ており、コンテキストインスタンスの初期化にかかるコストを節約できるという利点があります。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-113">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of the context instance.</span></span>

<span data-ttu-id="1d3a9-114">`poolSize`のパラメーターは、 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> プールに保持されているインスタンスの最大数を設定します。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-114">The `poolSize` parameter of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> sets the maximum number of instances retained by the pool.</span></span> <span data-ttu-id="1d3a9-115">を `poolSize` 超えた場合、新しいコンテキストインスタンスはキャッシュされず、EF は必要に応じてインスタンスを作成する非プール動作にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-115">Once `poolSize` is exceeded, new context instances are not cached and  EF falls back to the non-pooling behavior of creating instances on demand.</span></span>

### <a name="limitations"></a><span data-ttu-id="1d3a9-116">制限事項</span><span class="sxs-lookup"><span data-stu-id="1d3a9-116">Limitations</span></span>

<span data-ttu-id="1d3a9-117">コンテキストの初期化が大幅なコストであることを示すために、アプリのプロファイリングとテストを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-117">Apps should be profiled and tested to show that context initialization is a significant cost.</span></span>

<span data-ttu-id="1d3a9-118">`AddDbContextPool` では、コンテキストのメソッドで実行できる内容にいくつかの制限があり `OnConfiguring` ます。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-118">`AddDbContextPool` has a few limitations on what can be done in the `OnConfiguring` method of the context.</span></span>

> [!WARNING]
> <span data-ttu-id="1d3a9-119">状態を維持するアプリでコンテキストプーリングを使用することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-119">Avoid using context pooling in apps that maintain state.</span></span> <span data-ttu-id="1d3a9-120">たとえば、要求間で共有しないようなコンテキストのプライベートフィールドです。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-120">For example, private fields in the context that shouldn't be shared across requests.</span></span> <span data-ttu-id="1d3a9-121">EF Core は、コンテキストインスタンスをプールに追加する前に、認識している状態のみをリセットします。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-121">EF Core only resets the state that it is aware of before adding a context instance to the pool.</span></span>

<span data-ttu-id="1d3a9-122">コンテキストプーリングは、要求間で同じコンテキストインスタンスを再利用することによって機能します。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-122">Context pooling works by reusing the same context instance across requests.</span></span> <span data-ttu-id="1d3a9-123">これは、インスタンス自体を使用して、永続化できるように、 [シングルトン](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) として効果的に登録されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-123">This means that it's effectively registered as a [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) in terms of the instance itself so that it's able to persist.</span></span>

<span data-ttu-id="1d3a9-124">コンテキストプーリングは、解決されたサービスを含むコンテキスト構成が要求間で固定されているシナリオを対象としています。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-124">Context pooling is intended for scenarios where the context configuration, which includes services resolved, is fixed between requests.</span></span> <span data-ttu-id="1d3a9-125">[スコープ](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)が指定されたサービスが必要な場合、または構成を変更する必要がある場合は、プーリングを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-125">For cases where [Scoped](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) services are required, or configuration needs to be changed, don't use pooling.</span></span> <span data-ttu-id="1d3a9-126">プールからのパフォーマンスの向上は通常、高度に最適化されたシナリオ以外ではごくわずかです。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-126">The performance gain from pooling is usually negligible except in highly optimized scenarios.</span></span>

## <a name="query-caching-and-parameterization"></a><span data-ttu-id="1d3a9-127">クエリのキャッシュとパラメーター化</span><span class="sxs-lookup"><span data-stu-id="1d3a9-127">Query caching and parameterization</span></span>

<span data-ttu-id="1d3a9-128">EF が実行のために LINQ クエリツリーを受け取ると、そのツリーを SQL クエリに "コンパイル" する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-128">When EF receives a LINQ query tree for execution, it must first "compile" that tree into a SQL query.</span></span> <span data-ttu-id="1d3a9-129">これは大きなプロセスであるため、EF はクエリツリー *図形* によってクエリをキャッシュします。同じ構造を持つクエリは、内部キャッシュされたコンパイル出力を再利用し、繰り返しコンパイルをスキップできます。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-129">Because this is a heavy process, EF caches queries by the query tree *shape*: queries with the same structure reuse internally-cached compilation outputs, and can skip repeated compilation.</span></span> <span data-ttu-id="1d3a9-130">異なるクエリでは、異なる *値* を参照する場合がありますが、これらの値が適切にパラメーター化されていれば、構造は同じであり、キャッシュは正常に機能します。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-130">The different queries may still reference different *values*, but as long as these values are properly parameterized, the structure is the same and caching will function properly.</span></span>

<span data-ttu-id="1d3a9-131">次の2つのクエリについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-131">Consider the following two queries:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithConstants)]

<span data-ttu-id="1d3a9-132">式ツリーには異なる定数が含まれているため、式ツリーは異なり、これらの各クエリは EF Core によって個別にコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-132">Since the expression trees contains different constants, the expression tree differs and each of these queries will be compiled separately by EF Core.</span></span> <span data-ttu-id="1d3a9-133">さらに、各クエリでは、若干異なる SQL コマンドが生成されます。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-133">In addition, each query produces a slightly different SQL command:</span></span>

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog1'

SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog2'
```

<span data-ttu-id="1d3a9-134">SQL が異なるため、データベースサーバーでは、同じプランを再利用するのではなく、両方のクエリに対してクエリプランを生成することが必要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-134">Because the SQL differs, your database server will likely also need to produce a query plan for both queries, rather than reusing the same plan.</span></span>

<span data-ttu-id="1d3a9-135">クエリにわずかな変更を加えると、大幅に変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-135">A small modification to your queries can change things considerably:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithParameterization)]

<span data-ttu-id="1d3a9-136">ブログ名が *パラメーター化* されるようになったので、両方のクエリのツリー構造が同じで、EF だけをコンパイルする必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-136">Since the blog name is now *parameterized*, both queries have the same tree shape, and EF only needs to be compiled once.</span></span> <span data-ttu-id="1d3a9-137">生成された SQL もパラメーター化されるため、データベースは同じクエリプランを再利用できます。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-137">The SQL produced is also parameterized, allowing the database to reuse the same query plan:</span></span>

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = @__blogName_0
```

<span data-ttu-id="1d3a9-138">すべてのクエリをパラメーター化する必要がないことに注意してください。定数を持つクエリをいくつか作成することは十分です。実際には、データベース (および EF) は、クエリがパラメーター化されるときには不可能な定数に対して特定の最適化を実行することがあります。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-138">Note that there is no need to parameterize each and every query: it's perfectly fine to have some queries with constants, and indeed, databases (and EF) can sometimes perform certain optimization around constants which aren't possible when the query is parameterized.</span></span> <span data-ttu-id="1d3a9-139">適切なパラメーター化が重要な例については、 [動的に作成](#dynamically-constructed-queries) されたクエリに関するセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-139">See the section on [dynamically-constructed queries](#dynamically-constructed-queries) for an example where proper parameterization is crucial.</span></span>

> [!NOTE]
> <span data-ttu-id="1d3a9-140">EF Core の [イベントカウンター](xref:core/logging-events-diagnostics/event-counters) は、クエリキャッシュのヒット率を報告します。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-140">EF Core's [event counters](xref:core/logging-events-diagnostics/event-counters) report the Query Cache Hit Rate.</span></span> <span data-ttu-id="1d3a9-141">通常のアプリケーションでは、ほとんどのクエリが少なくとも1回実行されると、プログラムの起動後すぐにこのカウンターが100% に達します。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-141">In a normal application, this counter reaches 100% soon after program startup, once most queries have executed at least once.</span></span> <span data-ttu-id="1d3a9-142">このカウンターの値が100% 未満の場合は、アプリケーションがクエリキャッシュを損なう原因になっている可能性があることを示しています。これを調査することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-142">If this counter remains stable below 100%, that is an indication that your application may be doing something which defeats the query cache - it's a good idea to investigate that.</span></span>

> [!NOTE]
> <span data-ttu-id="1d3a9-143">データベースがキャッシュを管理する方法クエリプランはデータベースに依存します。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-143">How the database manages caches query plans is database-dependent.</span></span> <span data-ttu-id="1d3a9-144">たとえば、SQL Server は、暗黙的に LRU クエリプランキャッシュを保持しますが、PostgreSQL では使用できません (ただし、準備されたステートメントでは非常に類似した終了効果が生成される可能性があります)。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-144">For example, SQL Server implicitly maintains an LRU query plan cache, whereas PostgreSQL does not (but prepared statements can produce a very similar end effect).</span></span> <span data-ttu-id="1d3a9-145">詳細については、データベースのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-145">Consult your database documentation for more details.</span></span>

## <a name="dynamically-constructed-queries"></a><span data-ttu-id="1d3a9-146">動的に作成されたクエリ</span><span class="sxs-lookup"><span data-stu-id="1d3a9-146">Dynamically-constructed queries</span></span>

<span data-ttu-id="1d3a9-147">場合によっては、ソースコードで完全に指定するのではなく、LINQ クエリを動的に構築する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-147">In some situations, it is necessary to dynamically construct LINQ queries rather than specifying them outright in source code.</span></span> <span data-ttu-id="1d3a9-148">これは、たとえば、クライアントから任意のクエリの詳細を受け取る web サイトで、開いているクエリ演算子 (並べ替え、フィルター処理、ページングなど) を使用して発生する可能性があります。原則として、動的に構築されたクエリは、通常のクエリと同じように効率的に実行できます (ただし、動的クエリでコンパイル済みのクエリを使用することはできません)。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-148">This can happen, for example, in a website which receives arbitrary query details from a client, with open-ended query operators (sorting, filtering, paging...). In principle, if done correctly, dynamically-constructed queries can be just as efficient as regular ones (although it's not possible to use the compiled query optimization with dynamic queries).</span></span> <span data-ttu-id="1d3a9-149">ただし、実際にはパフォーマンスの問題の原因になることがよくあります。これは、常に異なる形状を持つ式ツリーを誤って生成することが簡単であるためです。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-149">In practice, however, they are frequently the source of performance issues, since it's easy to accidentally produce expression trees with shapes that differ every time.</span></span>

<span data-ttu-id="1d3a9-150">次の例では、2つの手法を使用してクエリを動的に作成します。 `Where` 指定されたパラメーターが null でない場合にのみ、クエリに演算子を追加します。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-150">The following example uses two techniques to dynamically construct a query; we add a `Where` operator to the query only if the given parameter is not null.</span></span> <span data-ttu-id="1d3a9-151">この方法は、クエリを動的に作成する場合には適していませんが、わかりやすくするために使用しています。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-151">Note that this isn't a good use case for dynamically constructing a query - but we're using it for simplicity:</span></span>

### <a name="with-constant"></a>[<span data-ttu-id="1d3a9-152">定数を含む</span><span class="sxs-lookup"><span data-stu-id="1d3a9-152">With constant</span></span>](#tab/with-constant)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithConstant&highlight=14-24)]

### <a name="with-parameter"></a>[<span data-ttu-id="1d3a9-153">パラメーター付き</span><span class="sxs-lookup"><span data-stu-id="1d3a9-153">With parameter</span></span>](#tab/with-parameter)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithParameter&highlight=14)]

***

<span data-ttu-id="1d3a9-154">これらの2つの手法のベンチマークを実行すると、次の結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-154">Benchmarking these two techniques gives the following results:</span></span>

|        <span data-ttu-id="1d3a9-155">Method</span><span class="sxs-lookup"><span data-stu-id="1d3a9-155">Method</span></span> |       <span data-ttu-id="1d3a9-156">平均</span><span class="sxs-lookup"><span data-stu-id="1d3a9-156">Mean</span></span> |    <span data-ttu-id="1d3a9-157">エラー</span><span class="sxs-lookup"><span data-stu-id="1d3a9-157">Error</span></span> |    <span data-ttu-id="1d3a9-158">StdDev</span><span class="sxs-lookup"><span data-stu-id="1d3a9-158">StdDev</span></span> |   <span data-ttu-id="1d3a9-159">Gen 0</span><span class="sxs-lookup"><span data-stu-id="1d3a9-159">Gen 0</span></span> |  <span data-ttu-id="1d3a9-160">Gen 1</span><span class="sxs-lookup"><span data-stu-id="1d3a9-160">Gen 1</span></span> | <span data-ttu-id="1d3a9-161">Gen 2</span><span class="sxs-lookup"><span data-stu-id="1d3a9-161">Gen 2</span></span> | <span data-ttu-id="1d3a9-162">Allocated</span><span class="sxs-lookup"><span data-stu-id="1d3a9-162">Allocated</span></span> |
|-------------- |-----------:|---------:|----------:|--------:|-------:|------:|----------:|
|  <span data-ttu-id="1d3a9-163">WithConstant</span><span class="sxs-lookup"><span data-stu-id="1d3a9-163">WithConstant</span></span> | <span data-ttu-id="1d3a9-164">1096.7 us</span><span class="sxs-lookup"><span data-stu-id="1d3a9-164">1,096.7 us</span></span> | <span data-ttu-id="1d3a9-165">12.54 us</span><span class="sxs-lookup"><span data-stu-id="1d3a9-165">12.54 us</span></span> |  <span data-ttu-id="1d3a9-166">11.12 us</span><span class="sxs-lookup"><span data-stu-id="1d3a9-166">11.12 us</span></span> | <span data-ttu-id="1d3a9-167">13.6719</span><span class="sxs-lookup"><span data-stu-id="1d3a9-167">13.6719</span></span> | <span data-ttu-id="1d3a9-168">1.9531</span><span class="sxs-lookup"><span data-stu-id="1d3a9-168">1.9531</span></span> |     - |  <span data-ttu-id="1d3a9-169">83.91 KB</span><span class="sxs-lookup"><span data-stu-id="1d3a9-169">83.91 KB</span></span> |
| <span data-ttu-id="1d3a9-170">WithParameter</span><span class="sxs-lookup"><span data-stu-id="1d3a9-170">WithParameter</span></span> |   <span data-ttu-id="1d3a9-171">570.8 us</span><span class="sxs-lookup"><span data-stu-id="1d3a9-171">570.8 us</span></span> | <span data-ttu-id="1d3a9-172">42.43 us</span><span class="sxs-lookup"><span data-stu-id="1d3a9-172">42.43 us</span></span> | <span data-ttu-id="1d3a9-173">124.43 us</span><span class="sxs-lookup"><span data-stu-id="1d3a9-173">124.43 us</span></span> |  <span data-ttu-id="1d3a9-174">5.8594</span><span class="sxs-lookup"><span data-stu-id="1d3a9-174">5.8594</span></span> |      - |     - |  <span data-ttu-id="1d3a9-175">37.16 KB</span><span class="sxs-lookup"><span data-stu-id="1d3a9-175">37.16 KB</span></span> |

<span data-ttu-id="1d3a9-176">1ミリ秒の差が小さいように思われる場合でも、定数のバージョンは継続的にキャッシュを汚染し、他のクエリを再コンパイルすることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-176">Even if the sub-millisecond difference seems small, keep in mind that the constant version continuously pollutes the cache and causes other queries to be re-compiled, slowing them down as well.</span></span>

> [!NOTE]
> <span data-ttu-id="1d3a9-177">本当に必要な場合を除き、式ツリー API を使用してクエリを作成することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-177">Avoid constructing queries with the expression tree API unless you really need to.</span></span> <span data-ttu-id="1d3a9-178">API の複雑さを除けば、これらを使用すると、非常に簡単にパフォーマンスの問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="1d3a9-178">Aside from the API's complexity, it's very easy to inadvertently cause significant performance issues when using them.</span></span>
