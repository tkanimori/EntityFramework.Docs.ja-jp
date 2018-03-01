---
title: "クエリの種類]-[EF コア"
author: anpete
ms.author: anpete
ms.date: 2/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
ms.technology: entity-framework-core
uid: core/modeling/query-types
ms.openlocfilehash: d03c4b1d5635530e63b93e051cb69583718deb4e
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="query-types"></a><span data-ttu-id="7fd7e-102">クエリの種類</span><span class="sxs-lookup"><span data-stu-id="7fd7e-102">Query Types</span></span>
> [!NOTE]
> <span data-ttu-id="7fd7e-103">この機能は、EF コア 2.1 の新機能</span><span class="sxs-lookup"><span data-stu-id="7fd7e-103">This feature is new in EF Core 2.1</span></span>

<span data-ttu-id="7fd7e-104">クエリの種類は、EF の主要なモデルに追加できる読み取り専用のクエリの結果型です。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-104">Query Types are read-only query result types that can be added to the EF Core model.</span></span> <span data-ttu-id="7fd7e-105">クエリの型 (匿名型の場合) などのアドホック クエリを有効にするがより柔軟なマッピングの構成に指定できるためです。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-105">Query Types enable ad-hoc querying (like anonymous types), but are more flexible because they can have mapping configuration specified.</span></span>

<span data-ttu-id="7fd7e-106">そのエンティティ型に概念的に似ています。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-106">They are conceptually similar to Entity Types in that:</span></span>

- <span data-ttu-id="7fd7e-107">POCO (C#) のいずれかと、モデルに追加される型では```OnModelCreating```を使用して、```ModelBuilder.Query```メソッド、または DbContext「セット」プロパティを介して (クエリがこのようなプロパティを型として型指定されて```DbQuery<T>```ではなく```DbSet<T>```)。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-107">They are POCO C# types that are added to the model, either in ```OnModelCreating``` using the ```ModelBuilder.Query``` method, or via a DbContext "set" property (for query types such a property is typed as ```DbQuery<T>``` rather that ```DbSet<T>```).</span></span>
- <span data-ttu-id="7fd7e-108">正規のエンティティ型と同じマッピング機能の多くをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-108">They support much of the same mapping capabilities as regular entity types.</span></span> <span data-ttu-id="7fd7e-109">たとえば、継承の割り当て、ナビゲーション (limitiations 以下を参照してください) と、リレーショナル ストアを使用してターゲット データベースのスキーマ オブジェクトを構成する機能の```ToTable```、 ```HasColumn``` fluent api のメソッド (またはデータ注釈)。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-109">For example, inheritance mapping, navigations (see limitiations below) and, on relational stores, the ability to configure the target database schema objects via ```ToTable```, ```HasColumn``` fluent-api methods (or data annotations).</span></span>

<span data-ttu-id="7fd7e-110">クエリの種類はエンティティを異なるの型します。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-110">Query Types are different from entity types in that they:</span></span>

- <span data-ttu-id="7fd7e-111">定義するキーは不要です。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-111">Do not require a key to be defined.</span></span>
- <span data-ttu-id="7fd7e-112">変更トラッカーによって追跡されてことはありません。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-112">Are never tracked by the Change Tracker.</span></span>
- <span data-ttu-id="7fd7e-113">規則によって検出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-113">Are never discovered by convention.</span></span>
- <span data-ttu-id="7fd7e-114">サブセットのみがサポートのナビゲーションのマッピング機能 - 具体的には、リレーションシップのプリンシパル end として機能しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-114">Only support a subset of navigation mapping capabilities - Specifically, they may never act as the principal end of a relationship.</span></span>
- <span data-ttu-id="7fd7e-115">マップすることがあります、_クエリを定義する_-A を定義するクエリはセカンダリのクエリは、データ ソースがクエリの種類の機能です。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-115">May be mapped to a _defining query_ - A Defining Query is a secondary query that acts a data source for a Query Type.</span></span>

<span data-ttu-id="7fd7e-116">クエリの種類の主な使用シナリオを次に示します。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-116">Some of the main usage scenarios for query types are:</span></span>

- <span data-ttu-id="7fd7e-117">データベース ビューへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-117">Mapping to database views.</span></span>
- <span data-ttu-id="7fd7e-118">定義された主キーがないテーブルへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-118">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="7fd7e-119">戻り値の型として機能しているアドホック```FromSql()```クエリ。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-119">Serving as the return type for ad hoc ```FromSql()``` queries.</span></span>
- <span data-ttu-id="7fd7e-120">モデルで定義されているクエリへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-120">Mapping to queries defined in the model.</span></span>

> [!TIP]
> <span data-ttu-id="7fd7e-121">使用して実現は、データベース ビューをクエリの種類のマッピング、 ```ToTable``` fluent API です。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-121">Mapping a query type to a database view is achieved using the ```ToTable``` fluent API.</span></span>

## <a name="example"></a><span data-ttu-id="7fd7e-122">例</span><span class="sxs-lookup"><span data-stu-id="7fd7e-122">Example</span></span>

<span data-ttu-id="7fd7e-123">次の例では、クエリの種類を使用して、データベース ビューをクエリする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-123">The following example shows how to use Query Type to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="7fd7e-124">この記事の[サンプル](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-124">You can view this article's [sample](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes) on GitHub.</span></span>

<span data-ttu-id="7fd7e-125">最初に、単純なブログと Post のモデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-125">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Entities)]

<span data-ttu-id="7fd7e-126">次に、各ブログに関連する投稿の数を照会できる単純なデータベース ビューを定義します。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-126">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#View)]

<span data-ttu-id="7fd7e-127">クエリの種類を次に、構成_OnModelCreating_を使用して、 ```modelBuilder.Query<T>``` API です。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-127">Next, we configure the query type in _OnModelCreating_ using the ```modelBuilder.Query<T>``` API.</span></span>
<span data-ttu-id="7fd7e-128">標準 fluent 構成 Api を使用して、クエリの種類のマッピングを構成します。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-128">We use standard fluent configuration APIs to configure the mapping for the Query Type:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Configuration)]

<span data-ttu-id="7fd7e-129">最後に、データベース ビューをクエリして標準の方法で。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-129">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="7fd7e-130">この型に対するクエリのルートとして機能するには、(DbQuery) コンテキスト レベルのクエリ プロパティを定義していますも注意してください。</span><span class="sxs-lookup"><span data-stu-id="7fd7e-130">Note we have also defined a context level query property (DbQuery) to act as a root for queries against this type.</span></span>
