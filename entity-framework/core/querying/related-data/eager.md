---
title: 関連データの一括読み込み - EF Core
description: Entity Framework Core による関連データの一括読み込み
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: f7e33895293e1d7b8b32a135d60b58dd1f050621
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078878"
---
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="55820-103">関連データの一括読み込み</span><span class="sxs-lookup"><span data-stu-id="55820-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="55820-104">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="55820-104">Eager loading</span></span>

<span data-ttu-id="55820-105">`Include` メソッドを使用して、クエリ結果に含める関連データを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="55820-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="55820-106">次の例では、結果で返されるブログには `Posts` プロパティに関連する投稿が設定されます。</span><span class="sxs-lookup"><span data-stu-id="55820-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="55820-107">Entity Framework Core は、以前にコンテキスト インスタンスに読み込まれた他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。</span><span class="sxs-lookup"><span data-stu-id="55820-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="55820-108">そのため、ナビゲーション プロパティのデータを明示的に含めていない場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="55820-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="55820-109">複数のリレーションシップの関連データを 1 つのクエリに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="55820-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a><span data-ttu-id="55820-110">複数のレベルを含める</span><span class="sxs-lookup"><span data-stu-id="55820-110">Including multiple levels</span></span>

<span data-ttu-id="55820-111">`ThenInclude` メソッドを使用して、リレーションシップをドリル ダウンし、複数のレベルの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="55820-111">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="55820-112">次の例では、すべてのブログ、関連記事、および各投稿の作成者を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="55820-112">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#SingleThenInclude)]

<span data-ttu-id="55820-113">`ThenInclude` に対して複数の呼び出しを連鎖させて、さらなるレベルの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="55820-113">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

<span data-ttu-id="55820-114">これらのすべての呼び出しを組み合わせて、複数のレベルと複数のルートの関連データを同じクエリ内に含めることができます。</span><span class="sxs-lookup"><span data-stu-id="55820-114">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#IncludeTree)]

<span data-ttu-id="55820-115">含まれているエンティティの 1 つについて複数の関連エンティティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="55820-115">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="55820-116">たとえば、`Blogs` をクエリするときに、`Posts` を含め、さらに `Posts` の `Author` と `Tags` の両方を含めたい場合があります。</span><span class="sxs-lookup"><span data-stu-id="55820-116">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="55820-117">両方を含めるには、ルートから始まる各インクルード パスを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="55820-117">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="55820-118">たとえば、`Blog -> Posts -> Author` や `Blog -> Posts -> Tags`す。</span><span class="sxs-lookup"><span data-stu-id="55820-118">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="55820-119">これで冗長的な結合を実現することにはならず、ほとんどの場合、SQL を生成するときに EF で結合は組み合わされます。</span><span class="sxs-lookup"><span data-stu-id="55820-119">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a><span data-ttu-id="55820-120">単一クエリと分割クエリ</span><span class="sxs-lookup"><span data-stu-id="55820-120">Single and split queries</span></span>

### <a name="single-queries"></a><span data-ttu-id="55820-121">単一のクエリ</span><span class="sxs-lookup"><span data-stu-id="55820-121">Single queries</span></span>

<span data-ttu-id="55820-122">リレーショナル データベースでは、JOIN を導入するとすべての関連エンティティが既定で読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="55820-122">In relational databases, all related entities are by default loaded by introducing JOINs:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="55820-123">一般的なブログに複数の関連する投稿がある場合、これらの投稿の行によってブログの情報が複製され、いわゆる "デカルト爆発" の問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="55820-123">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information, leading to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="55820-124">さらに一対多リレーションシップが読み込まれると、重複するデータの量が増加し、アプリケーションのパフォーマンスに悪影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="55820-124">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span> <span data-ttu-id="55820-125">既定で EF Core では、パフォーマンスの問題の原因となる可能性があるコレクション インクルードを読み込んでいるクエリが検出されると、警告が出力されます。</span><span class="sxs-lookup"><span data-stu-id="55820-125">By default, EF Core emits a warning if it detects queries loading collection includes that may cause performance issues.</span></span>

### <a name="split-queries"></a><span data-ttu-id="55820-126">分割クエリ</span><span class="sxs-lookup"><span data-stu-id="55820-126">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="55820-127">この機能は EF Core 5.0 で導入されています。</span><span class="sxs-lookup"><span data-stu-id="55820-127">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="55820-128">EF では、特定の LINQ クエリを複数の SQL クエリに "*分割*" するように指定できます。</span><span class="sxs-lookup"><span data-stu-id="55820-128">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="55820-129">JOIN の代わりに、分割クエリでは、含まれている一対多のナビゲーションごとに追加の SQL クエリが実行されます。</span><span class="sxs-lookup"><span data-stu-id="55820-129">Instead of JOINs, split queries perform an additional SQL query for each included one-to-many navigation:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="55820-130">これにより、次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="55820-130">It will produce the following SQL:</span></span>

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
> <span data-ttu-id="55820-131">一対一の関連エンティティは、常に同じクエリ内で JOIN によって読み込まれます。パフォーマンス上の影響がないためです。</span><span class="sxs-lookup"><span data-stu-id="55820-131">One-to-one related entities are always loaded via JOINs in the same query, as this has no performance impact.</span></span>

### <a name="enabling-split-queries-globally"></a><span data-ttu-id="55820-132">分割クエリをグローバルに有効にする</span><span class="sxs-lookup"><span data-stu-id="55820-132">Enabling split queries globally</span></span>

<span data-ttu-id="55820-133">分割クエリは、アプリケーションのコンテキストで既定値として構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="55820-133">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="55820-134">分割クエリが既定値として構成されている場合でも、特定のクエリを単一のクエリとして実行するように構成することができます。</span><span class="sxs-lookup"><span data-stu-id="55820-134">When split queries are configured as the default, it is still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="55820-135">クエリ分割モードが明示的に指定されておらず (グローバルでも、クエリ上でも)、単一のクエリによって複数のコレクション インクルードが読み込まれることが EF Core で検出された場合は、結果として生じる可能性のあるパフォーマンスの問題に注意するよう警告が出力されます。</span><span class="sxs-lookup"><span data-stu-id="55820-135">If the query splitting mode isn't explicitly specified - neither globally nor on the query - and EF Core detects that a single query loads multiple collection includes, a warning is emitted to draw attention to the potential resulting performance issues.</span></span> <span data-ttu-id="55820-136">クエリ モードを SingleQuery に設定すると、警告は生成されません。</span><span class="sxs-lookup"><span data-stu-id="55820-136">Setting the query mode to SingleQuery will cause the warning not to be generated.</span></span>

### <a name="characteristics-of-split-queries"></a><span data-ttu-id="55820-137">分割クエリの特性</span><span class="sxs-lookup"><span data-stu-id="55820-137">Characteristics of split queries</span></span>

<span data-ttu-id="55820-138">分割クエリによって JOIN とデカルト爆発に関連するパフォーマンス上の問題が回避されますが、いくつかの欠点もあります。</span><span class="sxs-lookup"><span data-stu-id="55820-138">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

* <span data-ttu-id="55820-139">ほとんどのデータベースでは単一クエリに対してデータの整合性が保証されますが、複数クエリに対してこのような保証は存在しません。</span><span class="sxs-lookup"><span data-stu-id="55820-139">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="55820-140">クエリの実行と同時にデータベースが更新された場合、生成されるデータの整合性が失われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="55820-140">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="55820-141">これはシリアル化可能なトランザクションまたはスナップショット トランザクションでクエリをラップすることで軽減できますが、これにより、それ自体のパフォーマンス上の問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="55820-141">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="55820-142">詳細については、ご利用のデータベースのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="55820-142">For more information, see your database's documentation.</span></span>
* <span data-ttu-id="55820-143">現在、各クエリは、データベースに対する追加のネットワーク ラウンドトリップを意味します。</span><span class="sxs-lookup"><span data-stu-id="55820-143">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="55820-144">特にデータベースの待機時間が長い場合 (クラウド サービスなど)、複数のネットワーク ラウンドトリップによってパフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="55820-144">Multiple network roundtrip can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
* <span data-ttu-id="55820-145">一部のデータベースでは、複数のクエリの結果を同時に使用することが許可されていますが (SQL Server と MARS、Sqlite)、ほとんどの場合、特定の時点でアクティブにできるクエリは 1 つだけです。</span><span class="sxs-lookup"><span data-stu-id="55820-145">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="55820-146">そのため、後のクエリを実行する前に、前のクエリの結果をすべてアプリケーションのメモリにバッファーする必要があります。これにより、メモリ要件が大きくなります。</span><span class="sxs-lookup"><span data-stu-id="55820-146">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="55820-147">残念ながら、すべてのシナリオに適合する関連エンティティの読み込み方法はありません。</span><span class="sxs-lookup"><span data-stu-id="55820-147">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="55820-148">単一クエリと分割クエリの長所と短所を慎重に検討し、ご自身のニーズに合うものを選択してください。</span><span class="sxs-lookup"><span data-stu-id="55820-148">Carefully consider the advantages and disadvantages of single and split queries, and select the one that fits your needs.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="55820-149">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="55820-149">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="55820-150">この機能は EF Core 5.0 で導入されています。</span><span class="sxs-lookup"><span data-stu-id="55820-150">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="55820-151">インクルードを適用して関連データを読み込む場合は、インクルードされているコレクション ナビゲーションに特定の列挙可能な操作を適用できます。これにより、結果のフィルター処理と並べ替えが可能になります。</span><span class="sxs-lookup"><span data-stu-id="55820-151">When applying Include to load related data, you can apply certain enumerable operations on the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="55820-152">サポートされている操作は、`Where`、`OrderBy`、`OrderByDescending`、`ThenBy`、`ThenByDescending`、`Skip`、`Take` です。</span><span class="sxs-lookup"><span data-stu-id="55820-152">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="55820-153">このような操作は、次の例に示すように、インクルード メソッドに渡されるラムダのコレクション ナビゲーションに適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="55820-153">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#FilteredInclude)]

<span data-ttu-id="55820-154">インクルードされている各ナビゲーションでは、フィルター操作の一意のセットが 1 つだけ許可されます。</span><span class="sxs-lookup"><span data-stu-id="55820-154">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="55820-155">特定のコレクション ナビゲーション (次の例では `blog.Posts`) に複数のインクルード操作が適用されている場合、フィルター操作はそのいずれかでのみ指定できます。</span><span class="sxs-lookup"><span data-stu-id="55820-155">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="55820-156">代わりに、複数回インクルードされているナビゲーションごとに、同一の操作を適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="55820-156">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Sample.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="55820-157">クエリの追跡では、[ナビゲーション修正](xref:core/querying/tracking)により、フィルター処理されたインクルードの結果が予期しないものになることがあります。</span><span class="sxs-lookup"><span data-stu-id="55820-157">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="55820-158">以前にクエリされ、変更トラッカーに格納されていた関連エンティティはすべて、フィルターの要件を満たしていなくても、フィルター処理されたインクルードのクエリの結果に表示されます。</span><span class="sxs-lookup"><span data-stu-id="55820-158">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="55820-159">そのような状況でフィルター処理されたインクルードを使用する場合は、`NoTracking` クエリを使用するか、DbContext を再作成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="55820-159">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="55820-160">例:</span><span class="sxs-lookup"><span data-stu-id="55820-160">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a><span data-ttu-id="55820-161">派生型に対するインクルード</span><span class="sxs-lookup"><span data-stu-id="55820-161">Include on derived types</span></span>

<span data-ttu-id="55820-162">`Include` と `ThenInclude` を使用して、派生型にのみ定義されているナビゲーションの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="55820-162">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="55820-163">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="55820-163">Given the following model:</span></span>

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

<span data-ttu-id="55820-164">生徒である全ユーザーの `School` ナビゲーションの内容を一括して読み込むには、いくつかのパターンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="55820-164">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="55820-165">キャストを使用する</span><span class="sxs-lookup"><span data-stu-id="55820-165">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="55820-166">`as` 演算子を使用する</span><span class="sxs-lookup"><span data-stu-id="55820-166">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="55820-167">型 `string` のパラメーターを受け取る `Include` のオーバーロードを使用する</span><span class="sxs-lookup"><span data-stu-id="55820-167">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
  