---
title: グローバル クエリ フィルター - EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: f6c59bcbab31edcbed22079a1320c060ce08c6f7
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2020
ms.locfileid: "84664131"
---
# <a name="global-query-filters"></a><span data-ttu-id="3b546-102">グローバル クエリ フィルター</span><span class="sxs-lookup"><span data-stu-id="3b546-102">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="3b546-103">この機能は EF Core 2.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="3b546-103">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="3b546-104">グローバル クエリ フィルターは、(通常 *OnModelCreating* にある) メタデータ モデルのエンティティ型に適用される、LINQ クエリ述語 (通常 LINQ *Where* クエリ演算子に渡されるブール式) です。</span><span class="sxs-lookup"><span data-stu-id="3b546-104">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="3b546-105">このようなフィルターは、このようなエンティティ型が関係するあらゆる LINQ クエリに自動的に適用されます。これには間接的に参照されるエンティティ型が含まれます。たとえば、Include を利用して参照されます。あるいは、ナビゲーション プロパティが直接参照されます。</span><span class="sxs-lookup"><span data-stu-id="3b546-105">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="3b546-106">この機能の一般的な用途は次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="3b546-106">Some common applications of this feature are:</span></span>

* <span data-ttu-id="3b546-107">**論理削除** - エンティティ型が *IsDeleted* プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="3b546-107">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="3b546-108">**マルチテナント** - エンティティ型が *TenantId* プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="3b546-108">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="3b546-109">例</span><span class="sxs-lookup"><span data-stu-id="3b546-109">Example</span></span>

<span data-ttu-id="3b546-110">次の例では、グローバル クエリ フィルターを使用して論理削除とマルチテナントのクエリ動作を単純なブログ モデルに実装する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="3b546-110">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="3b546-111">GitHub 上の[マルチテナント サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters)および[ナビゲーションを使用したサンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations)を確認できます。</span><span class="sxs-lookup"><span data-stu-id="3b546-111">You can view a [multi-tenancy sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) and [samples using navigations](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) on GitHub.</span></span> 

<span data-ttu-id="3b546-112">最初に、エンティティを次のように定義します。</span><span class="sxs-lookup"><span data-stu-id="3b546-112">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="3b546-113">_Blog_ エンティティの _tenantId_ フィールドの宣言をメモします。</span><span class="sxs-lookup"><span data-stu-id="3b546-113">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="3b546-114">これは、固有のテナントと各 Blog インスタンスの関連付けに使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b546-114">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="3b546-115">また、_Post_ エンティティ型の _IsDeleted_ プロパティが定義されています。</span><span class="sxs-lookup"><span data-stu-id="3b546-115">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="3b546-116">これは、_Post_ インスタンスが "論理削除" されたかどうかの追跡を継続するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b546-116">This is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="3b546-117">つまり、基になるデータを物理的に削除せずに、インスタンスは削除済みとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="3b546-117">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="3b546-118">次に、`HasQueryFilter` API を使用して _OnModelCreating_ でクエリフィルターを構成します。</span><span class="sxs-lookup"><span data-stu-id="3b546-118">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="3b546-119">_HasQueryFilter_ の呼び出しに渡される predicate 式は、これらの型の LINQ クエリに自動的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="3b546-119">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="3b546-120">DbContext インスタンス レベルのフィールドの使用に注意してください。`_tenantId` は、現在のテナントを設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b546-120">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="3b546-121">モデルレベル フィルターは、正しいコンテキスト インスタンス (つまり、クエリを実行しているインスタンス) の値を使用します。</span><span class="sxs-lookup"><span data-stu-id="3b546-121">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="3b546-122">現在、同じエンティティに対して複数のクエリ フィルターを定義することはできません。最後のフィルターのみが適用されます。</span><span class="sxs-lookup"><span data-stu-id="3b546-122">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="3b546-123">ただし、論理 _AND_ 演算子 ([C# では `&&`](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)) を使用して、複数の条件を持つ単一のフィルターを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="3b546-123">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="use-of-navigations"></a><span data-ttu-id="3b546-124">ナビゲーションの使用</span><span class="sxs-lookup"><span data-stu-id="3b546-124">Use of navigations</span></span>

<span data-ttu-id="3b546-125">グローバル クエリ フィルターを定義するとき、ナビゲーションを使用できます。</span><span class="sxs-lookup"><span data-stu-id="3b546-125">Navigations can be used when defining global query filters.</span></span> <span data-ttu-id="3b546-126">それらは再帰的に適用されます。クエリ フィルター内で使用されているナビゲーションが変換されると、参照先エンティティに対して定義されているクエリ フィルターも適用されるため、ナビゲーションがさらに追加される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3b546-126">They are applied recursively - when navigations used in query filters are translated, query filters defined on referenced entities are also applied, potentially adding more navigations.</span></span>

> [!NOTE]
> <span data-ttu-id="3b546-127">現在、グローバル クエリ フィルター定義内のサイクルは EF Core によって検出されないため、それらを定義する際には注意が必要です。</span><span class="sxs-lookup"><span data-stu-id="3b546-127">Currently EF Core does not detect cycles in global query filter definitions, so you should be careful when defining them.</span></span> <span data-ttu-id="3b546-128">正しく指定されていない場合、クエリの変換中に無限ループが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3b546-128">If specified incorrectly, this could lead to infinite loops during query translation.</span></span>

## <a name="accessing-entity-with-query-filter-using-reqiured-navigation"></a><span data-ttu-id="3b546-129">必須のナビゲーションを使用したクエリ フィルターを含むエンティティにアクセスする</span><span class="sxs-lookup"><span data-stu-id="3b546-129">Accessing entity with query filter using reqiured navigation</span></span>

> [!CAUTION]
> <span data-ttu-id="3b546-130">グローバル クエリ フィルターが定義されているエンティティにアクセスする場合に必須のナビゲーションを使用すると、予期しない結果が生じる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3b546-130">Using required navigation to access entity which has global query filter defined may lead to unexpected results.</span></span> 

<span data-ttu-id="3b546-131">必須のナビゲーションでは、関連エンティティが常に存在している必要があります。</span><span class="sxs-lookup"><span data-stu-id="3b546-131">Required navigation expects the related entity to always be present.</span></span> <span data-ttu-id="3b546-132">必須の関連エンティティがクエリ フィルターによって除外されると、親エンティティが予期しない状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3b546-132">If required related entity is filtered out by the query filter, the parent entity could end up in unexpected state.</span></span> <span data-ttu-id="3b546-133">これにより、返される要素が予想よりも少なくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3b546-133">This may result in returning fewer elements than expected.</span></span> 

<span data-ttu-id="3b546-134">この問題を説明するために、上で指定した `Blog` および `Post` エンティティと、次の _OnModelCreating_ メソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="3b546-134">To illustrate the problem, we can use the `Blog` and `Post` entities specified above and the following _OnModelCreating_ method:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="3b546-135">このモデルは、次のデータを使用してシード処理することができます。</span><span class="sxs-lookup"><span data-stu-id="3b546-135">The model can be seeded with the following data:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

<span data-ttu-id="3b546-136">次の 2 つのクエリを実行すると、問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="3b546-136">The problem can be observed when executing two queries:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

<span data-ttu-id="3b546-137">この設定の場合、最初のクエリでは 6 個の `Post` すべてが返されますが、2 番目のクエリで返されるのは 3 個だけです。</span><span class="sxs-lookup"><span data-stu-id="3b546-137">With this setup, the first query returns all 6 `Post`s, however the second query only returns 3.</span></span> <span data-ttu-id="3b546-138">これは、2 番目のクエリ内の _Include_ メソッドによって、関連する `Blog` エンティティが読み込まれるために発生します。</span><span class="sxs-lookup"><span data-stu-id="3b546-138">This happens because _Include_ method in the second query loads the related `Blog` entities.</span></span> <span data-ttu-id="3b546-139">`Blog` と `Post` 間のナビゲーションは必須であるため、EF Core ではクエリの作成時に `INNER JOIN` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="3b546-139">Since the navigation between `Blog` and `Post` is required, EF Core uses `INNER JOIN` when constructing the query:</span></span>

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

<span data-ttu-id="3b546-140">`INNER JOIN` を使用すると、関連する `Blog` がグローバル クエリ フィルターによって削除されたすべての `Post` が除外されます。</span><span class="sxs-lookup"><span data-stu-id="3b546-140">Use of the `INNER JOIN` filters out all `Post`s whose related `Blog`s have been removed by a global query filter.</span></span> 

<span data-ttu-id="3b546-141">そのアドレス指定は、必須ではなく、オプションのナビゲーションを使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="3b546-141">It can be addressed by using optional navigation instead of required.</span></span> <span data-ttu-id="3b546-142">このように、最初のクエリは以前と同じままですが、2 番目のクエリでは `LEFT JOIN` が生成され、6 個の結果が返されるようになります。</span><span class="sxs-lookup"><span data-stu-id="3b546-142">This way the first query stays the same as before, however the second query will now generate `LEFT JOIN` and return 6 results.</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

<span data-ttu-id="3b546-143">別の方法として、`Blog` と `Post` の両方のエンティティに対して一貫したフィルターを指定する方法があります。</span><span class="sxs-lookup"><span data-stu-id="3b546-143">Alternative approach is to specify consistent filters on both `Blog` and `Post` entities.</span></span>
<span data-ttu-id="3b546-144">これにより、一致するフィルターが `Blog` と `Post` の両方に適用されます。</span><span class="sxs-lookup"><span data-stu-id="3b546-144">This way matching filters are applied to both `Blog` and `Post`.</span></span> <span data-ttu-id="3b546-145">予期しない状態になる可能性がある `Post` は削除され、両方のクエリとも 3 個の結果が返されます。</span><span class="sxs-lookup"><span data-stu-id="3b546-145">`Post`s that could end up in unexpected state are removed and both queries return 3 results.</span></span> 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a><span data-ttu-id="3b546-146">フィルターを無効にする</span><span class="sxs-lookup"><span data-stu-id="3b546-146">Disabling Filters</span></span>

<span data-ttu-id="3b546-147">フィルターは、`IgnoreQueryFilters()` 演算子を使用することで、個々の LINQ クエリに対して無効にできます。</span><span class="sxs-lookup"><span data-stu-id="3b546-147">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="3b546-148">制限事項</span><span class="sxs-lookup"><span data-stu-id="3b546-148">Limitations</span></span>

<span data-ttu-id="3b546-149">グローバル クエリ フィルターには、次の制限があります。</span><span class="sxs-lookup"><span data-stu-id="3b546-149">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="3b546-150">フィルターは、継承階層のルート エンティティ型に対してのみ定義できます。</span><span class="sxs-lookup"><span data-stu-id="3b546-150">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
