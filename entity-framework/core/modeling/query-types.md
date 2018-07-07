---
title: クエリの種類 - EF Core
author: anpete
ms.author: anpete
ms.date: 2/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
ms.technology: entity-framework-core
uid: core/modeling/query-types
ms.openlocfilehash: 89f5be356654dc02e353441a83e34c90fc727593
ms.sourcegitcommit: fd50ac53b93a03825dcbb42ed2e7ca95ca858d5f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900305"
---
# <a name="query-types"></a><span data-ttu-id="031c5-102">クエリの種類</span><span class="sxs-lookup"><span data-stu-id="031c5-102">Query Types</span></span>
> [!NOTE]
> <span data-ttu-id="031c5-103">この機能は EF Core 2.1 の新機能</span><span class="sxs-lookup"><span data-stu-id="031c5-103">This feature is new in EF Core 2.1</span></span>

<span data-ttu-id="031c5-104">エンティティ型だけでなく、EF Core モデルを含めることができます_型をクエリ_エンティティ型にマップされていないデータに対してデータベース クエリを実行するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="031c5-104">In addition to entity types, an EF Core model can contain _query types_, which can be used to carry out database queries against data that isn't mapped to entity types.</span></span>

<span data-ttu-id="031c5-105">クエリの種類では、エンティティの種類と多くの類似点があります。</span><span class="sxs-lookup"><span data-stu-id="031c5-105">Query types have many similarities with entity types:</span></span>

- <span data-ttu-id="031c5-106">追加することも、モデルにいずれかで`OnModelCreating`、または派生の"set"プロパティを使用して_DbContext_します。</span><span class="sxs-lookup"><span data-stu-id="031c5-106">They can also be added to the model either in `OnModelCreating`, or via a "set" property on a derived _DbContext_.</span></span>
- <span data-ttu-id="031c5-107">継承のマッピング、ナビゲーションのプロパティ (次の制限事項を参照してください) など、リレーショナル ストアで、ターゲット データベースのオブジェクトと fluent API のメソッドまたはデータの注釈を使用して列を構成する機能は同じのマッピング機能の多くをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="031c5-107">They support many of the same mapping capabilities, like inheritance mapping, navigation properties (see limitations below) and, on relational stores, the ability to configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="031c5-108">ただしそれらがエンティティから異なる種類で。</span><span class="sxs-lookup"><span data-stu-id="031c5-108">However they are different from entity types in that they:</span></span>

- <span data-ttu-id="031c5-109">定義するキーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="031c5-109">Do not require a key to be defined.</span></span>
- <span data-ttu-id="031c5-110">変更追跡は決して、 _DbContext_したがっては決して挿入、更新またはデータベースの削除します。</span><span class="sxs-lookup"><span data-stu-id="031c5-110">Are never tracked for changes on the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="031c5-111">規則によって検出されたことはありません。</span><span class="sxs-lookup"><span data-stu-id="031c5-111">Are never discovered by convention.</span></span>
- <span data-ttu-id="031c5-112">具体的には、ナビゲーションのマッピング機能のサブセットをサポートのみ。</span><span class="sxs-lookup"><span data-stu-id="031c5-112">Only support a subset of navigation mapping capabilities - Specifically:</span></span>
  - <span data-ttu-id="031c5-113">リレーションシップのプリンシパル end として機能しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="031c5-113">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="031c5-114">エンティティを指す参照ナビゲーション プロパティのみを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="031c5-114">They can only contain reference navigation properties pointing to entities.</span></span>
  - <span data-ttu-id="031c5-115">エンティティは、クエリの型にナビゲーション プロパティを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="031c5-115">Entities cannot contain navigation properties to query types.</span></span>
- <span data-ttu-id="031c5-116">対処する、 _ModelBuilder_を使用して、`Query`メソッドではなく、`Entity`メソッド。</span><span class="sxs-lookup"><span data-stu-id="031c5-116">Are addressed on the _ModelBuilder_ using the `Query` method rather than the `Entity` method.</span></span>
- <span data-ttu-id="031c5-117">マップされて、 _DbContext_型のプロパティを介して`DbQuery<T>`なく `DbSet<T>`</span><span class="sxs-lookup"><span data-stu-id="031c5-117">Are mapped on the _DbContext_ through properties of type `DbQuery<T>` rather than `DbSet<T>`</span></span>
- <span data-ttu-id="031c5-118">使用してデータベース オブジェクトにマップされて、`ToView`メソッド、なく`ToTable`します。</span><span class="sxs-lookup"><span data-stu-id="031c5-118">Are mapped to database objects using the `ToView` method, rather than `ToTable`.</span></span>
- <span data-ttu-id="031c5-119">マップすることがあります、_クエリを定義する_- クエリの種類のデータ ソースが機能するモデルで宣言されている 2 番目のクエリは、クエリを定義します。</span><span class="sxs-lookup"><span data-stu-id="031c5-119">May be mapped to a _defining query_ - A defining query is a secondary query declared in the model that acts a data source for a query type.</span></span>

<span data-ttu-id="031c5-120">クエリの種類の主な使用シナリオを次に示します。</span><span class="sxs-lookup"><span data-stu-id="031c5-120">Some of the main usage scenarios for query types are:</span></span>

- <span data-ttu-id="031c5-121">アドホックの戻り値の型として使用される`FromSql()`クエリ。</span><span class="sxs-lookup"><span data-stu-id="031c5-121">Serving as the return type for ad hoc `FromSql()` queries.</span></span>
- <span data-ttu-id="031c5-122">データベース ビューへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="031c5-122">Mapping to database views.</span></span>
- <span data-ttu-id="031c5-123">定義された主キーがないテーブルへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="031c5-123">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="031c5-124">モデルで定義されているクエリへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="031c5-124">Mapping to queries defined in the model.</span></span>

> [!TIP]
> <span data-ttu-id="031c5-125">使用して実現は、データベース オブジェクトにクエリ型のマッピング、 `ToView` fluent API。</span><span class="sxs-lookup"><span data-stu-id="031c5-125">Mapping a query type to a database object is achieved using the `ToView` fluent API.</span></span> <span data-ttu-id="031c5-126">このメソッドで指定されたデータベース オブジェクトは、EF Core の観点から、_ビュー_、読み取り専用クエリのソースとして扱われることを意味し、更新プログラムの対象となる、挿入または削除できません操作。</span><span class="sxs-lookup"><span data-stu-id="031c5-126">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="031c5-127">ただし、これは限りませんするデータベース オブジェクトがあるデータベース ビューを実際に必要 - 読み取り専用として扱われるデータベース テーブルにもできます。</span><span class="sxs-lookup"><span data-stu-id="031c5-127">However, this does not mean that the database object is actually required to be a database view - It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="031c5-128">逆に、エンティティ型の場合は、EF Core と想定でデータベース オブジェクトが指定されている、`ToTable`としてメソッドを処理できる、_テーブル_、つまりクエリのソースとして使用できますが、削除、更新プログラムも対象となる、および挿入操作です。</span><span class="sxs-lookup"><span data-stu-id="031c5-128">Conversely, for entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="031c5-129">実際には、データベース ビューの名前を指定できます`ToTable`データベースに対して更新可能にするビューが構成されている限り問題なく機能すべて。</span><span class="sxs-lookup"><span data-stu-id="031c5-129">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

## <a name="example"></a><span data-ttu-id="031c5-130">例</span><span class="sxs-lookup"><span data-stu-id="031c5-130">Example</span></span>

<span data-ttu-id="031c5-131">次の例は、クエリの種類を使用して、データベース ビューをクエリする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="031c5-131">The following example shows how to use Query Type to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="031c5-132">この記事の[サンプル](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="031c5-132">You can view this article's [sample](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes) on GitHub.</span></span>

<span data-ttu-id="031c5-133">最初に、ブログや投稿の単純なモデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="031c5-133">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../efcore-repo/samples/QueryTypes/Program.cs#Entities)]

<span data-ttu-id="031c5-134">次に、各ブログに関連する投稿の数を照会することを許可する単純なデータベース ビューを定義します。</span><span class="sxs-lookup"><span data-stu-id="031c5-134">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../efcore-repo/samples/QueryTypes/Program.cs#View)]

<span data-ttu-id="031c5-135">次に、データベース ビューから結果を保持するクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="031c5-135">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../efcore-repo/samples/QueryTypes/Program.cs#QueryType)]

<span data-ttu-id="031c5-136">次に、クエリの種類で構成_OnModelCreating_を使用して、 `modelBuilder.Query<T>` API。</span><span class="sxs-lookup"><span data-stu-id="031c5-136">Next, we configure the query type in _OnModelCreating_ using the `modelBuilder.Query<T>` API.</span></span>
<span data-ttu-id="031c5-137">クエリの種類のマッピングを構成するのに標準の fluent 構成 Api を使用します。</span><span class="sxs-lookup"><span data-stu-id="031c5-137">We use standard fluent configuration APIs to configure the mapping for the Query Type:</span></span>

[!code-csharp[Main](../../../efcore-repo/samples/QueryTypes/Program.cs#Configuration)]

<span data-ttu-id="031c5-138">最後に、標準的な方法で、データベース ビューを照会できます。</span><span class="sxs-lookup"><span data-stu-id="031c5-138">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../efcore-repo/samples/QueryTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="031c5-139">この型に対するクエリのルートとして機能するには、(DbQuery) コンテキスト レベルのクエリ プロパティも定義しておいたに注意してください。</span><span class="sxs-lookup"><span data-stu-id="031c5-139">Note we have also defined a context level query property (DbQuery) to act as a root for queries against this type.</span></span>
