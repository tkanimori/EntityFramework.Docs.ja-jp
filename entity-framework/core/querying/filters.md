---
title: グローバル クエリ フィルター - EF Core
author: anpete
ms.author: anpete
ms.date: 11/03/2017
ms.technology: entity-framework-core
uid: core/querying/filters
ms.openlocfilehash: 4e3c3c99d155f69e00fed99c415f519808ea1a68
ms.sourcegitcommit: 6e379265e4f087fb7cf180c824722c81750554dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
ms.locfileid: "26053902"
---
# <a name="global-query-filters"></a><span data-ttu-id="9e194-102">グローバル クエリ フィルター</span><span class="sxs-lookup"><span data-stu-id="9e194-102">Global Query Filters</span></span>

<span data-ttu-id="9e194-103">グローバル クエリ フィルターは、(通常 *OnModelCreating* にある) メタデータ モデルのエンティティ型に適用される、LINQ クエリ述語 (通常 LINQ *Where* クエリ演算子に渡されるブール式) です。</span><span class="sxs-lookup"><span data-stu-id="9e194-103">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="9e194-104">このようなフィルターは、このようなエンティティ型が関係するあらゆる LINQ クエリに自動的に適用されます。これには間接的に参照されるエンティティ型が含まれます。たとえば、Include を利用して参照されます。あるいは、ナビゲーション プロパティが直接参照されます。</span><span class="sxs-lookup"><span data-stu-id="9e194-104">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="9e194-105">この機能の一般的な用途は次のようになっています。</span><span class="sxs-lookup"><span data-stu-id="9e194-105">Some common applications of this feature are:</span></span>

* <span data-ttu-id="9e194-106">**論理削除** - エンティティ型が *IsDeleted* プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="9e194-106">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="9e194-107">**マルチテナント** - エンティティ型が *TenantId* プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="9e194-107">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="9e194-108">例</span><span class="sxs-lookup"><span data-stu-id="9e194-108">Example</span></span>

<span data-ttu-id="9e194-109">次の例では、グローバル クエリ フィルターを使用して論理削除とマルチテナントのクエリ動作を単純なブログ モデルに実装する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="9e194-109">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="9e194-110">この記事の[サンプル](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryFilters)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="9e194-110">You can view this article's [sample](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryFilters) on GitHub.</span></span>

<span data-ttu-id="9e194-111">最初に、エンティティを次のように定義します。</span><span class="sxs-lookup"><span data-stu-id="9e194-111">First, define the entities:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="9e194-112">_Blog_ エンティティの __tenantId_ フィールドの宣言をメモします。</span><span class="sxs-lookup"><span data-stu-id="9e194-112">Note the declaration of a __tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="9e194-113">これは、固有のテナントと各 Blog インスタンスの関連付けに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e194-113">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="9e194-114">また、_Post_ エンティティ型の _IsDeleted_ プロパティが定義されています。</span><span class="sxs-lookup"><span data-stu-id="9e194-114">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="9e194-115">これは、_Post_ インスタンスが "論理削除" されたかどうかの追跡を継続するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e194-115">This is used this to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="9e194-116">つまり、基になるデータを物理的に削除せずに、インスタンスは削除済みとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="9e194-116">I.e. The instance is marked as deleted withouth physically removing the underlying data.</span></span>

<span data-ttu-id="9e194-117">次に、```HasQueryFilter``` API を使用して _OnModelCreating_ でクエリ フィルターを構成します。</span><span class="sxs-lookup"><span data-stu-id="9e194-117">Next, configure the query filters in _OnModelCreating_ using the ```HasQueryFilter``` API.</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="9e194-118">_HasQueryFilter_ 呼び出しに渡される述語式は、型に対応するいずれかの LINQ クエリに自動的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="9e194-118">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="9e194-119">DbContext インスタンス レベルのフィールドの使用に注意してください。```_tenantId``` は、現在のテナントを設定するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e194-119">Note the use of a DbContext instance level field: ```_tenantId``` used to set the current tenant.</span></span> <span data-ttu-id="9e194-120">モデルレベル フィルターでは、適切なコンテキスト インスタンスからの値が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9e194-120">Model-level filters will use the value from the correct context instance.</span></span> <span data-ttu-id="9e194-121">つまり、クエリを実行しているインスタンスです。</span><span class="sxs-lookup"><span data-stu-id="9e194-121">I.e. The instance that is executing the query.</span></span>

## <a name="disabling-filters"></a><span data-ttu-id="9e194-122">フィルターを無効にする</span><span class="sxs-lookup"><span data-stu-id="9e194-122">Disabling Filters</span></span>

<span data-ttu-id="9e194-123">フィルターは、```IgnoreQueryFilters()``` 演算子を使用して、個々の LINQ クエリに対して無効にできます。</span><span class="sxs-lookup"><span data-stu-id="9e194-123">Filters may be disabled for individual LINQ queries by using the ```IgnoreQueryFilters()``` operator.</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="9e194-124">制限事項</span><span class="sxs-lookup"><span data-stu-id="9e194-124">Limitations</span></span>

<span data-ttu-id="9e194-125">グローバル クエリ フィルターには、次の制限があります。</span><span class="sxs-lookup"><span data-stu-id="9e194-125">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="9e194-126">フィルターは、ナビゲーション プロパティへの参照を含むことはできません。</span><span class="sxs-lookup"><span data-stu-id="9e194-126">Filters cannot contain references to navigation properties.</span></span>
* <span data-ttu-id="9e194-127">フィルターは、継承階層のルート エンティティ型に対してのみ定義できます。</span><span class="sxs-lookup"><span data-stu-id="9e194-127">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
