---
title: グローバル クエリ フィルター - EF Core
description: Entity Framework Core で結果をフィルター処理することを目的としたグローバル クエリ フィルターの使用
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: d5793760ea2e61111416284db8d5a8102dd51a41
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616390"
---
# <a name="global-query-filters"></a><span data-ttu-id="89ca4-103">グローバル クエリ フィルター</span><span class="sxs-lookup"><span data-stu-id="89ca4-103">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="89ca4-104">この機能は EF Core 2.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="89ca4-104">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="89ca4-105">グローバル クエリ フィルターは、(通常 *OnModelCreating* にある) メタデータ モデルのエンティティ型に適用される LINQ クエリ述語です。</span><span class="sxs-lookup"><span data-stu-id="89ca4-105">Global query filters are LINQ query predicates applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="89ca4-106">クエリ述語は、通常、LINQ の *Where* クエリ演算子に渡されるブール式です。</span><span class="sxs-lookup"><span data-stu-id="89ca4-106">A query predicate is a boolean expression typically passed to the LINQ *Where* query operator.</span></span>  <span data-ttu-id="89ca4-107">このようなフィルターは、EF Core によって、これらのエンティティ型に関係する LINQ クエリに自動的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-107">EF Core applies such filters automatically to any LINQ queries involving those Entity Types.</span></span>  <span data-ttu-id="89ca4-108">また、EF Core によって、Include またはナビゲーション プロパティを使用して間接的に参照されるエンティティ型にも適用されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-108">EF Core also applies them to Entity Types, referenced indirectly through use of Include or navigation property.</span></span> <span data-ttu-id="89ca4-109">この機能の一般的な用途は次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="89ca4-109">Some common applications of this feature are:</span></span>

* <span data-ttu-id="89ca4-110">**論理削除** - エンティティ型が *IsDeleted* プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="89ca4-110">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="89ca4-111">**マルチテナント** - エンティティ型が *TenantId* プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="89ca4-111">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="89ca4-112">例</span><span class="sxs-lookup"><span data-stu-id="89ca4-112">Example</span></span>

<span data-ttu-id="89ca4-113">次の例では、グローバル クエリ フィルターを使用して、マルチテナントおよび論理的な削除のクエリ動作を単純なブログ モデルに実装する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="89ca4-113">The following example shows how to use Global Query Filters to implement multi-tenancy and soft-delete query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="89ca4-114">GitHub 上の[マルチテナント サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters)および[ナビゲーションを使用したサンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations)を確認できます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-114">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span>

<span data-ttu-id="89ca4-115">最初に、エンティティを次のように定義します。</span><span class="sxs-lookup"><span data-stu-id="89ca4-115">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="89ca4-116">_Blog_ エンティティの _tenantId_ フィールドの宣言をメモします。</span><span class="sxs-lookup"><span data-stu-id="89ca4-116">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="89ca4-117">このフィールドは、各 Blog インスタンスを固有のテナントに関連付けるために使用されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-117">This field will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="89ca4-118">また、_Post_ エンティティ型の _IsDeleted_ プロパティが定義されています。</span><span class="sxs-lookup"><span data-stu-id="89ca4-118">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="89ca4-119">このプロパティは、_Post_ インスタンスが "論理的に削除" されたかどうかの追跡を継続するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-119">This property is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="89ca4-120">つまり、基になるデータを物理的に削除せずに、インスタンスは削除済みとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-120">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="89ca4-121">次に、`HasQueryFilter` API を使用して _OnModelCreating_ でクエリフィルターを構成します。</span><span class="sxs-lookup"><span data-stu-id="89ca4-121">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="89ca4-122">_HasQueryFilter_ の呼び出しに渡される predicate 式は、これらの型の LINQ クエリに自動的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-122">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="89ca4-123">DbContext インスタンス レベルのフィールドの使用に注意してください。`_tenantId` は、現在のテナントを設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-123">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="89ca4-124">モデルレベル フィルターは、正しいコンテキスト インスタンス (つまり、クエリを実行しているインスタンス) の値を使用します。</span><span class="sxs-lookup"><span data-stu-id="89ca4-124">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="89ca4-125">現在、同じエンティティに対して複数のクエリ フィルターを定義することはできません。最後のフィルターのみが適用されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-125">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="89ca4-126">ただし、論理 _AND_ 演算子 ([C# では `&&`](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)) を使用して、複数の条件を持つ単一のフィルターを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-126">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="89ca4-127">ナビゲーションの使用</span><span class="sxs-lookup"><span data-stu-id="89ca4-127">Use of navigations</span></span>

<span data-ttu-id="89ca4-128">グローバル クエリ フィルターを定義するときにも、ナビゲーションを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-128">You can also use navigations in defining global query filters.</span></span> <span data-ttu-id="89ca4-129">クエリ フィルターでナビゲーションを使用すると、クエリ フィルターが再帰的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-129">Using navigations in query filter will cause query filters to be applied recursively.</span></span> <span data-ttu-id="89ca4-130">クエリ フィルターで使用されているナビゲーションが EF Core によって拡張されると、参照先エンティティで定義されているクエリ フィルターも適用されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-130">When EF Core expands navigations used in query filters, it will also apply query filters defined on referenced entities.</span></span>

> [!NOTE]
> <span data-ttu-id="89ca4-131">現在、グローバル クエリ フィルター定義内のサイクルは EF Core によって検出されないため、それらを定義する際には注意が必要です。</span><span class="sxs-lookup"><span data-stu-id="89ca4-131">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="89ca4-132">正しく指定されていない場合は、クエリの変換中にサイクルが無限ループになる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="89ca4-132">If specified incorrectly, cycles could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a><span data-ttu-id="89ca4-133">必須のナビゲーションを使用したクエリ フィルターを含むエンティティへのアクセス</span><span class="sxs-lookup"><span data-stu-id="89ca4-133">Accessing entity with query filter using required navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="89ca4-134">グローバル クエリ フィルターが定義されているエンティティにアクセスする場合に必須のナビゲーションを使用すると、予期しない結果が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="89ca4-134">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span>

<span data-ttu-id="89ca4-135">必須のナビゲーションでは、関連エンティティが常に存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="89ca4-135">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="89ca4-136">必要な関連エンティティがクエリ フィルターによって除外されると、親エンティティも結果に存在しなくなります。</span><span class="sxs-lookup"><span data-stu-id="89ca4-136">If necessary related entity is filtered out by the query filter, the parent entity wouldn't be in result either.</span></span> <span data-ttu-id="89ca4-137">そのため、結果で取得できる要素が予想よりも少ない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="89ca4-137">So you may get fewer elements than expected in result.</span></span>

<span data-ttu-id="89ca4-138">この問題を説明するために、上で指定した `Blog` および `Post` エンティティと、次の _OnModelCreating_ メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-138">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="89ca4-139">このモデルは、次のデータを使用してシード処理することができます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-139">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="89ca4-140">次の 2 つのクエリを実行すると、問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="89ca4-140">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="89ca4-141">上記の設定の場合、最初のクエリでは 6 つの `Post` がすべて返されますが、2 つめのクエリで返されるのは 3 つのみです。</span><span class="sxs-lookup"><span data-stu-id="89ca4-141">With above setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="89ca4-142">このような不一致は、2 つめのクエリ内の _Include_ メソッドによって、関連する `Blog` エンティティが読み込まれるために発生します。</span><span class="sxs-lookup"><span data-stu-id="89ca4-142">This mismatch happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="89ca4-143">`Blog` と `Post` 間のナビゲーションは必須であるため、EF Core ではクエリの作成時に `INNER JOIN` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-143">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="89ca4-144">`INNER JOIN` を使用すると、関連する `Blog` がグローバル クエリ フィルターによって削除されたすべての `Post` が除外されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-144">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span>

<span data-ttu-id="89ca4-145">そのアドレス指定は、必須ではなく、オプションのナビゲーションを使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-145">It can be addressed by using optional navigation instead of required.</span></span>
<span data-ttu-id="89ca4-146">このように、最初のクエリは以前と同じままですが、2 番目のクエリでは `LEFT JOIN` が生成され、6 個の結果が返されるようになります。</span><span class="sxs-lookup"><span data-stu-id="89ca4-146">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="89ca4-147">別の方法として、`Blog` と `Post` の両方のエンティティに対して一貫したフィルターを指定する方法があります。</span><span class="sxs-lookup"><span data-stu-id="89ca4-147">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="89ca4-148">これにより、一致するフィルターが `Blog` と `Post` の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-148">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="89ca4-149">予期しない状態になる可能性がある `Post` は削除され、両方のクエリとも 3 個の結果が返されます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-149">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="89ca4-150">フィルターを無効にする</span><span class="sxs-lookup"><span data-stu-id="89ca4-150">Disabling Filters</span></span>

<span data-ttu-id="89ca4-151">フィルターは、`IgnoreQueryFilters()` 演算子を使用することで、個々の LINQ クエリに対して無効にできます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-151">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="89ca4-152">制限事項</span><span class="sxs-lookup"><span data-stu-id="89ca4-152">Limitations</span></span>

<span data-ttu-id="89ca4-153">グローバル クエリ フィルターには、次の制限があります。</span><span class="sxs-lookup"><span data-stu-id="89ca4-153">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="89ca4-154">フィルターは、継承階層のルート エンティティ型に対してのみ定義できます。</span><span class="sxs-lookup"><span data-stu-id="89ca4-154">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
