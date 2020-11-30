---
title: 関連データの一括読み込み - EF Core
description: Entity Framework Core による関連データの一括読み込み
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 66956fcd85bb21a08c69fa93b93c12382bbfc8eb
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003576"
---
# <a name="eager-loading-of-related-data"></a><span data-ttu-id="daf81-103">関連データの一括読み込み</span><span class="sxs-lookup"><span data-stu-id="daf81-103">Eager Loading of Related Data</span></span>

## <a name="eager-loading"></a><span data-ttu-id="daf81-104">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="daf81-104">Eager loading</span></span>

<span data-ttu-id="daf81-105">`Include` メソッドを使用して、クエリ結果に含める関連データを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="daf81-105">You can use the `Include` method to specify related data to be included in query results.</span></span> <span data-ttu-id="daf81-106">次の例では、結果で返されるブログには `Posts` プロパティに関連する投稿が設定されます。</span><span class="sxs-lookup"><span data-stu-id="daf81-106">In the following example, the blogs that are returned in the results will have their `Posts` property populated with the related posts.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> <span data-ttu-id="daf81-107">Entity Framework Core は、以前にコンテキスト インスタンスに読み込まれた他のエンティティに対して、ナビゲーション プロパティを自動的に修正します。</span><span class="sxs-lookup"><span data-stu-id="daf81-107">Entity Framework Core will automatically fix-up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="daf81-108">そのため、ナビゲーション プロパティのデータを明示的に含めていない場合でも、関連エンティティの一部またはすべてが以前に読み込まれていれば、プロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="daf81-108">So even if you don't explicitly include the data for a navigation property, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

<span data-ttu-id="daf81-109">複数のリレーションシップの関連データを 1 つのクエリに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="daf81-109">You can include related data from multiple relationships in a single query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

> [!CAUTION]
> <span data-ttu-id="daf81-110">コレクション ナビゲーションを 1 つのクエリで一括で読み込むと、パフォーマンスの問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="daf81-110">Eager loading a collection navigation in a single query may cause performance issues.</span></span> <span data-ttu-id="daf81-111">詳細については、[単一クエリと分割クエリ](xref:core/querying/single-split-queries)に関するページをご覧ください。</span><span class="sxs-lookup"><span data-stu-id="daf81-111">For more information, see [Single vs. split queries](xref:core/querying/single-split-queries).</span></span>

## <a name="including-multiple-levels"></a><span data-ttu-id="daf81-112">複数のレベルを含める</span><span class="sxs-lookup"><span data-stu-id="daf81-112">Including multiple levels</span></span>

<span data-ttu-id="daf81-113">`ThenInclude` メソッドを使用して、リレーションシップをドリル ダウンし、複数のレベルの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="daf81-113">You can drill down through relationships to include multiple levels of related data using the `ThenInclude` method.</span></span> <span data-ttu-id="daf81-114">次の例では、すべてのブログ、関連記事、および各投稿の作成者を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="daf81-114">The following example loads all blogs, their related posts, and the author of each post.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

<span data-ttu-id="daf81-115">`ThenInclude` に対して複数の呼び出しを連鎖させて、さらなるレベルの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="daf81-115">You can chain multiple calls to `ThenInclude` to continue including further levels of related data.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

<span data-ttu-id="daf81-116">これらのすべての呼び出しを組み合わせて、複数のレベルと複数のルートの関連データを同じクエリ内に含めることができます。</span><span class="sxs-lookup"><span data-stu-id="daf81-116">You can combine all of the calls to include related data from multiple levels and multiple roots in the same query.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

<span data-ttu-id="daf81-117">含まれているエンティティの 1 つについて複数の関連エンティティを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="daf81-117">You may want to include multiple related entities for one of the entities that is being included.</span></span> <span data-ttu-id="daf81-118">たとえば、`Blogs` をクエリするときに、`Posts` を含め、さらに `Posts` の `Author` と `Tags` の両方を含めたい場合があります。</span><span class="sxs-lookup"><span data-stu-id="daf81-118">For example, when querying `Blogs`, you include `Posts` and then want to include both the `Author` and `Tags` of the `Posts`.</span></span> <span data-ttu-id="daf81-119">両方を含めるには、ルートから始まる各インクルード パスを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="daf81-119">To include both, you need to specify each include path starting at the root.</span></span> <span data-ttu-id="daf81-120">たとえば、`Blog -> Posts -> Author` や `Blog -> Posts -> Tags`す。</span><span class="sxs-lookup"><span data-stu-id="daf81-120">For example, `Blog -> Posts -> Author` and `Blog -> Posts -> Tags`.</span></span> <span data-ttu-id="daf81-121">これで冗長的な結合を実現することにはならず、ほとんどの場合、SQL を生成するときに EF で結合は組み合わされます。</span><span class="sxs-lookup"><span data-stu-id="daf81-121">It doesn't mean you'll get redundant joins; in most cases, EF will combine the joins when generating SQL.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

> [!TIP]
> <span data-ttu-id="daf81-122">1 つの `Include` メソッドを使用して複数のナビゲーションを読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="daf81-122">You can also load multiple navigations using a single `Include` method.</span></span> <span data-ttu-id="daf81-123">これは、すべて参照であるナビゲーション "チェーン" の場合、または 1 つのコレクションで終わる場合に可能です。</span><span class="sxs-lookup"><span data-stu-id="daf81-123">This is possible for navigation "chains" that are all references, or when they end with a single collection.</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeMultipleNavigationsWithSingleInclude)]

## <a name="filtered-include"></a><span data-ttu-id="daf81-124">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="daf81-124">Filtered include</span></span>

> [!NOTE]
> <span data-ttu-id="daf81-125">この機能は EF Core 5.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="daf81-125">This feature was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="daf81-126">インクルードを適用して関連データを読み込む場合は、インクルードされているコレクション ナビゲーションに特定の列挙可能な操作を追加できます。これにより、結果のフィルター処理と並べ替えを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="daf81-126">When applying Include to load related data, you can add certain enumerable operations to the included collection navigation, which allows for filtering and sorting of the results.</span></span>

<span data-ttu-id="daf81-127">サポートされている操作は、`Where`、`OrderBy`、`OrderByDescending`、`ThenBy`、`ThenByDescending`、`Skip`、`Take` です。</span><span class="sxs-lookup"><span data-stu-id="daf81-127">Supported operations are: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip`, and `Take`.</span></span>

<span data-ttu-id="daf81-128">このような操作は、次の例に示すように、インクルード メソッドに渡されるラムダのコレクション ナビゲーションに適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="daf81-128">Such operations should be applied on the collection navigation in the lambda passed to the Include method, as shown in example below:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

<span data-ttu-id="daf81-129">インクルードされている各ナビゲーションでは、フィルター操作の一意のセットが 1 つだけ許可されます。</span><span class="sxs-lookup"><span data-stu-id="daf81-129">Each included navigation allows only one unique set of filter operations.</span></span> <span data-ttu-id="daf81-130">特定のコレクション ナビゲーション (次の例では `blog.Posts`) に複数のインクルード操作が適用されている場合、フィルター操作はそのいずれかでのみ指定できます。</span><span class="sxs-lookup"><span data-stu-id="daf81-130">In cases where multiple Include operations are applied for a given collection navigation (`blog.Posts` in the examples below), filter operations can only be specified on one of them:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

<span data-ttu-id="daf81-131">代わりに、複数回インクルードされているナビゲーションごとに、同一の操作を適用することもできます。</span><span class="sxs-lookup"><span data-stu-id="daf81-131">Instead, identical operations can be applied for each navigation that is included multiple times:</span></span>

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> <span data-ttu-id="daf81-132">クエリの追跡では、[ナビゲーション修正](xref:core/querying/tracking)により、フィルター処理されたインクルードの結果が予期しないものになることがあります。</span><span class="sxs-lookup"><span data-stu-id="daf81-132">In case of tracking queries, results of Filtered Include may be unexpected due to [navigation fixup](xref:core/querying/tracking).</span></span> <span data-ttu-id="daf81-133">以前にクエリされ、変更トラッカーに格納されていた関連エンティティはすべて、フィルターの要件を満たしていなくても、フィルター処理されたインクルードのクエリの結果に表示されます。</span><span class="sxs-lookup"><span data-stu-id="daf81-133">All relevant entities that have been queried for previously and have been stored in the Change Tracker will be present in the results of Filtered Include query, even if they don't meet the requirements of the filter.</span></span> <span data-ttu-id="daf81-134">そのような状況でフィルター処理されたインクルードを使用する場合は、`NoTracking` クエリを使用するか、DbContext を再作成することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="daf81-134">Consider using `NoTracking` queries or re-create the DbContext when using Filtered Include in those situations.</span></span>

<span data-ttu-id="daf81-135">例:</span><span class="sxs-lookup"><span data-stu-id="daf81-135">Example:</span></span>

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

> [!NOTE]
> <span data-ttu-id="daf81-136">追跡クエリの場合は、フィルター処理されたインクルードが適用されたナビゲーションが読み込まれると見なされます。</span><span class="sxs-lookup"><span data-stu-id="daf81-136">In case of tracking queries, the navigation on which filtered include was applied is considered to be loaded.</span></span> <span data-ttu-id="daf81-137">つまり、EF Core では、一部の要素が欠落してる可能性があっても、[明示的読み込み](xref:core/querying/related-data/explicit)または[遅延読み込み](xref:core/querying/related-data/lazy)を使用した値の再読み込みが試みられません。</span><span class="sxs-lookup"><span data-stu-id="daf81-137">This means that EF Core will not attempt to re-load it's values using [explicit loading](xref:core/querying/related-data/explicit) or [lazy loading](xref:core/querying/related-data/lazy), even though some elements could still be missing.</span></span>

## <a name="include-on-derived-types"></a><span data-ttu-id="daf81-138">派生型に対するインクルード</span><span class="sxs-lookup"><span data-stu-id="daf81-138">Include on derived types</span></span>

<span data-ttu-id="daf81-139">`Include` と `ThenInclude` を使用して、派生型にのみ定義されているナビゲーションの関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="daf81-139">You can include related data from navigation defined only on a derived type using `Include` and `ThenInclude`.</span></span>

<span data-ttu-id="daf81-140">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="daf81-140">Given the following model:</span></span>

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

<span data-ttu-id="daf81-141">生徒である全ユーザーの `School` ナビゲーションの内容を一括して読み込むには、いくつかのパターンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="daf81-141">Contents of `School` navigation of all People who are Students can be eagerly loaded using a number of patterns:</span></span>

* <span data-ttu-id="daf81-142">キャストを使用する</span><span class="sxs-lookup"><span data-stu-id="daf81-142">using cast</span></span>

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* <span data-ttu-id="daf81-143">`as` 演算子を使用する</span><span class="sxs-lookup"><span data-stu-id="daf81-143">using `as` operator</span></span>

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* <span data-ttu-id="daf81-144">型 `string` のパラメーターを受け取る `Include` のオーバーロードを使用する</span><span class="sxs-lookup"><span data-stu-id="daf81-144">using overload of `Include` that takes parameter of type `string`</span></span>

  ```csharp
  context.People.Include("School").ToList()
  ```
