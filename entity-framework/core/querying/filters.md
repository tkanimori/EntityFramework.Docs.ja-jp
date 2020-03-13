---
title: グローバル クエリ フィルター - EF Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: 9262ff7970b0502945480c673315071cbc3f44b9
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413763"
---
# <a name="global-query-filters"></a><span data-ttu-id="339ff-102">グローバル クエリ フィルター</span><span class="sxs-lookup"><span data-stu-id="339ff-102">Global Query Filters</span></span>

> [!NOTE]
> <span data-ttu-id="339ff-103">この機能は EF Core 2.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="339ff-103">This feature was introduced in EF Core 2.0.</span></span>

<span data-ttu-id="339ff-104">グローバル クエリ フィルターは、(通常 *OnModelCreating* にある) メタデータ モデルのエンティティ型に適用される、LINQ クエリ述語 (通常 LINQ *Where* クエリ演算子に渡されるブール式) です。</span><span class="sxs-lookup"><span data-stu-id="339ff-104">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="339ff-105">このようなフィルターは、このようなエンティティ型が関係するあらゆる LINQ クエリに自動的に適用されます。これには間接的に参照されるエンティティ型が含まれます。たとえば、Include を利用して参照されます。あるいは、ナビゲーション プロパティが直接参照されます。</span><span class="sxs-lookup"><span data-stu-id="339ff-105">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="339ff-106">この機能の一般的な用途は次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="339ff-106">Some common applications of this feature are:</span></span>

* <span data-ttu-id="339ff-107">**論理削除** - エンティティ型が *IsDeleted* プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="339ff-107">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="339ff-108">**マルチテナント** - エンティティ型が *TenantId* プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="339ff-108">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="339ff-109">例</span><span class="sxs-lookup"><span data-stu-id="339ff-109">Example</span></span>

<span data-ttu-id="339ff-110">次の例では、グローバル クエリ フィルターを使用して論理削除とマルチテナントのクエリ動作を単純なブログ モデルに実装する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="339ff-110">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="339ff-111">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="339ff-111">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) on GitHub.</span></span>

<span data-ttu-id="339ff-112">最初に、エンティティを次のように定義します。</span><span class="sxs-lookup"><span data-stu-id="339ff-112">First, define the entities:</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="339ff-113">_Blog_ エンティティの _tenantId_ フィールドの宣言をメモします。</span><span class="sxs-lookup"><span data-stu-id="339ff-113">Note the declaration of a _tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="339ff-114">これは、固有のテナントと各 Blog インスタンスの関連付けに使用されます。</span><span class="sxs-lookup"><span data-stu-id="339ff-114">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="339ff-115">また、_Post_ エンティティ型の _IsDeleted_ プロパティが定義されています。</span><span class="sxs-lookup"><span data-stu-id="339ff-115">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="339ff-116">これは、_Post_ インスタンスが "論理削除" されたかどうかの追跡を継続するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="339ff-116">This is used to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="339ff-117">つまり、基になるデータを物理的に削除せずに、インスタンスは削除済みとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="339ff-117">That is, the instance is marked as deleted without physically removing the underlying data.</span></span>

<span data-ttu-id="339ff-118">次に、`HasQueryFilter` API を使用して _OnModelCreating_ でクエリ フィルターを構成します。</span><span class="sxs-lookup"><span data-stu-id="339ff-118">Next, configure the query filters in _OnModelCreating_ using the `HasQueryFilter` API.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="339ff-119">_HasQueryFilter_ 呼び出しに渡される述語式は、型に対応するいずれかの LINQ クエリに自動的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="339ff-119">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="339ff-120">DbContext インスタンス レベルのフィールドの使用に注意してください。`_tenantId` は、現在のテナントを設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="339ff-120">Note the use of a DbContext instance level field: `_tenantId` used to set the current tenant.</span></span> <span data-ttu-id="339ff-121">モデルレベル フィルターは、正しいコンテキスト インスタンス (つまり、クエリを実行しているインスタンス) の値を使用します。</span><span class="sxs-lookup"><span data-stu-id="339ff-121">Model-level filters will use the value from the correct context instance (that is, the instance that is executing the query).</span></span>

> [!NOTE]
> <span data-ttu-id="339ff-122">現在、同じエンティティに対して複数のクエリ フィルターを定義することはできません。最後のフィルターのみが適用されます。</span><span class="sxs-lookup"><span data-stu-id="339ff-122">It is currently not possible to define multiple query filters on the same entity - only the last one will be applied.</span></span> <span data-ttu-id="339ff-123">ただし、論理 _AND_ 演算子 ([C# では `&&`](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)) を使用して、複数の条件を持つ単一のフィルターを定義することができます。</span><span class="sxs-lookup"><span data-stu-id="339ff-123">However, you can define a single filter with multiple conditions using the logical _AND_ operator ([`&&` in C#](https://docs.microsoft.com/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).</span></span>

## <a name="disabling-filters"></a><span data-ttu-id="339ff-124">フィルターを無効にする</span><span class="sxs-lookup"><span data-stu-id="339ff-124">Disabling Filters</span></span>

<span data-ttu-id="339ff-125">フィルターは、`IgnoreQueryFilters()` 演算子を使用して、個々の LINQ クエリに対して無効にできます。</span><span class="sxs-lookup"><span data-stu-id="339ff-125">Filters may be disabled for individual LINQ queries by using the `IgnoreQueryFilters()` operator.</span></span>

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="339ff-126">制限事項</span><span class="sxs-lookup"><span data-stu-id="339ff-126">Limitations</span></span>

<span data-ttu-id="339ff-127">グローバル クエリ フィルターには、次の制限があります。</span><span class="sxs-lookup"><span data-stu-id="339ff-127">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="339ff-128">フィルターは、継承階層のルート エンティティ型に対してのみ定義できます。</span><span class="sxs-lookup"><span data-stu-id="339ff-128">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
