---
title: "グローバル クエリのフィルター]-[EF コア"
author: anpete
ms.author: anpete
ms.date: 11/03/2017
ms.technology: entity-framework-core
uid: core/querying/filters
ms.openlocfilehash: 4e3c3c99d155f69e00fed99c415f519808ea1a68
ms.sourcegitcommit: 6e379265e4f087fb7cf180c824722c81750554dc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="global-query-filters"></a><span data-ttu-id="c7535-102">グローバル クエリのフィルター</span><span class="sxs-lookup"><span data-stu-id="c7535-102">Global Query Filters</span></span>

<span data-ttu-id="c7535-103">グローバル クエリのフィルターは、LINQ クエリの述語 (ブール式は通常、LINQ に渡される*場所*クエリ演算子) メタデータ モデルのエンティティ型に適用される (通常の*OnModelCreating*)。</span><span class="sxs-lookup"><span data-stu-id="c7535-103">Global query filters are LINQ query predicates (a boolean expression typically passed to the LINQ *Where* query operator) applied to Entity Types in the metadata model (usually in *OnModelCreating*).</span></span> <span data-ttu-id="c7535-104">このようなフィルターは、これらのエンティティ型、エンティティ型のインクルードを使用して間接的に、ように参照または直接のナビゲーション プロパティの参照の追加に関連する LINQ クエリを自動的に適用されます。</span><span class="sxs-lookup"><span data-stu-id="c7535-104">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="c7535-105">この機能の一般的なアプリケーションは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="c7535-105">Some common applications of this feature are:</span></span>

* <span data-ttu-id="c7535-106">**論理削除**-エンティティの種類の定義、 *IsDeleted*プロパティです。</span><span class="sxs-lookup"><span data-stu-id="c7535-106">**Soft delete** - An Entity Type defines an *IsDeleted* property.</span></span>
* <span data-ttu-id="c7535-107">**マルチ テナント**-エンティティの種類の定義、 *TenantId*プロパティです。</span><span class="sxs-lookup"><span data-stu-id="c7535-107">**Multi-tenancy** - An Entity Type defines a *TenantId* property.</span></span>

## <a name="example"></a><span data-ttu-id="c7535-108">例</span><span class="sxs-lookup"><span data-stu-id="c7535-108">Example</span></span>

<span data-ttu-id="c7535-109">次の例では、グローバル クエリ フィルターを使用して単純なブログ モデルでマルチ テナント機能およびソフト削除クエリの動作を実装する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c7535-109">The following example shows how to use Global Query Filters to implement soft-delete and multi-tenancy query behaviors in a simple blogging model.</span></span>

> [!TIP]
> <span data-ttu-id="c7535-110">この記事を表示する[サンプル](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryFilters)GitHub でします。</span><span class="sxs-lookup"><span data-stu-id="c7535-110">You can view this article's [sample](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryFilters) on GitHub.</span></span>

<span data-ttu-id="c7535-111">最初に、エンティティを定義します。</span><span class="sxs-lookup"><span data-stu-id="c7535-111">First, define the entities:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#Entities)]

<span data-ttu-id="c7535-112">_ の宣言に注意してください_tenantId_フィールドに、_ブログ_エンティティです。</span><span class="sxs-lookup"><span data-stu-id="c7535-112">Note the declaration of a __tenantId_ field on the _Blog_ entity.</span></span> <span data-ttu-id="c7535-113">各ブログ インスタンスを特定のテナントに関連付けるために使用されます。</span><span class="sxs-lookup"><span data-stu-id="c7535-113">This will be used to associate each Blog instance with a specific tenant.</span></span> <span data-ttu-id="c7535-114">定義されても、 _IsDeleted_プロパティを_Post_エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="c7535-114">Also defined is an _IsDeleted_ property on the _Post_ entity type.</span></span> <span data-ttu-id="c7535-115">これは、使用するかどうかを把握しておく、 _Post_インスタンスは、「論理削除済み」されました。</span><span class="sxs-lookup"><span data-stu-id="c7535-115">This is used this to keep track of whether a _Post_ instance has been "soft-deleted".</span></span> <span data-ttu-id="c7535-116">例。インスタンスは、基になるデータを物理的に削除する削除済み withouth としてマークされます。</span><span class="sxs-lookup"><span data-stu-id="c7535-116">I.e. The instance is marked as deleted withouth physically removing the underlying data.</span></span>

<span data-ttu-id="c7535-117">次に、フィルターを構成、クエリで_OnModelCreating_を使用して、 ```HasQueryFilter``` API です。</span><span class="sxs-lookup"><span data-stu-id="c7535-117">Next, configure the query filters in _OnModelCreating_ using the ```HasQueryFilter``` API.</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#Configuration)]

<span data-ttu-id="c7535-118">渡される述語の式、 _HasQueryFilter_それらの型の任意の LINQ クエリに自動的に適用されるようになりました呼び出しです。</span><span class="sxs-lookup"><span data-stu-id="c7535-118">The predicate expressions passed to the _HasQueryFilter_ calls will now automatically be applied to any LINQ queries for those types.</span></span>

> [!TIP]
> <span data-ttu-id="c7535-119">DbContext インスタンス レベルのフィールドの使用に注意してください:```_tenantId```現在のテナントを設定するために使用します。</span><span class="sxs-lookup"><span data-stu-id="c7535-119">Note the use of a DbContext instance level field: ```_tenantId``` used to set the current tenant.</span></span> <span data-ttu-id="c7535-120">モデル レベルのフィルターでは、正しいコンテキスト インスタンスから値を使用します。</span><span class="sxs-lookup"><span data-stu-id="c7535-120">Model-level filters will use the value from the correct context instance.</span></span> <span data-ttu-id="c7535-121">例。クエリを実行しているインスタンス。</span><span class="sxs-lookup"><span data-stu-id="c7535-121">I.e. The instance that is executing the query.</span></span>

## <a name="disabling-filters"></a><span data-ttu-id="c7535-122">フィルターを無効にします。</span><span class="sxs-lookup"><span data-stu-id="c7535-122">Disabling Filters</span></span>

<span data-ttu-id="c7535-123">フィルターを無効にする個々 の LINQ クエリを使用して、```IgnoreQueryFilters()```演算子。</span><span class="sxs-lookup"><span data-stu-id="c7535-123">Filters may be disabled for individual LINQ queries by using the ```IgnoreQueryFilters()``` operator.</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a><span data-ttu-id="c7535-124">制限事項</span><span class="sxs-lookup"><span data-stu-id="c7535-124">Limitations</span></span>

<span data-ttu-id="c7535-125">グローバル クエリのフィルターには、次の制限があります。</span><span class="sxs-lookup"><span data-stu-id="c7535-125">Global query filters have the following limitations:</span></span>

* <span data-ttu-id="c7535-126">フィルターは、ナビゲーション プロパティへの参照を含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="c7535-126">Filters cannot contain references to navigation properties.</span></span>
* <span data-ttu-id="c7535-127">フィルターは、継承階層のエンティティ型のルートに対してのみ定義できます。</span><span class="sxs-lookup"><span data-stu-id="c7535-127">Filters can only be defined for the root Entity Type of an inheritance hierarchy.</span></span>
