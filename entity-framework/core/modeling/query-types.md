---
title: クエリの種類 - EF Core
author: anpete
ms.date: 02/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
uid: core/modeling/query-types
ms.openlocfilehash: 6f0f860c6a4e619e13d55e6207234a8b5261ee09
ms.sourcegitcommit: d1230e34673b8323a227ab37958dfa77f3684728
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68330792"
---
# <a name="query-types"></a><span data-ttu-id="39238-102">クエリの種類</span><span class="sxs-lookup"><span data-stu-id="39238-102">Query Types</span></span>
> [!NOTE]
> <span data-ttu-id="39238-103">この機能は EF Core 2.1 の新機能</span><span class="sxs-lookup"><span data-stu-id="39238-103">This feature is new in EF Core 2.1</span></span>

<span data-ttu-id="39238-104">エンティティ型だけでなく、EF Core モデルを含めることができます_型をクエリ_エンティティ型にマップされていないデータに対してデータベース クエリを実行するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="39238-104">In addition to entity types, an EF Core model can contain _query types_, which can be used to carry out database queries against data that isn't mapped to entity types.</span></span>

## <a name="compare-query-types-to-entity-types"></a><span data-ttu-id="39238-105">エンティティ型にクエリの種類を比較します。</span><span class="sxs-lookup"><span data-stu-id="39238-105">Compare query types to entity types</span></span>

<span data-ttu-id="39238-106">クエリの種類はそのエンティティ型と同様にします。</span><span class="sxs-lookup"><span data-stu-id="39238-106">Query types are like entity types in that they:</span></span>

- <span data-ttu-id="39238-107">いずれかのモデルに追加できますで`OnModelCreating`または派生の"set"プロパティを使用して_DbContext_します。</span><span class="sxs-lookup"><span data-stu-id="39238-107">Can be added to the model either in `OnModelCreating` or via a "set" property on a derived _DbContext_.</span></span>
- <span data-ttu-id="39238-108">継承のマッピングとナビゲーション プロパティと同様に、同じマッピング機能の多くをサポートします。</span><span class="sxs-lookup"><span data-stu-id="39238-108">Support many of the same mapping capabilities, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="39238-109">リレーショナル ストアで、ターゲット データベースのオブジェクトと列 fluent API のメソッドまたはデータの注釈を使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="39238-109">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="39238-110">ただし、それらがエンティティから異なる型にします。</span><span class="sxs-lookup"><span data-stu-id="39238-110">However, they are different from entity types in that they:</span></span>

- <span data-ttu-id="39238-111">定義するキーは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="39238-111">Do not require a key to be defined.</span></span>
- <span data-ttu-id="39238-112">変更追跡は決して、 _DbContext_したがっては決して挿入、更新またはデータベースの削除します。</span><span class="sxs-lookup"><span data-stu-id="39238-112">Are never tracked for changes on the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="39238-113">規則によって検出されたことはありません。</span><span class="sxs-lookup"><span data-stu-id="39238-113">Are never discovered by convention.</span></span>
- <span data-ttu-id="39238-114">具体的には、ナビゲーションのマッピング機能のサブセットをサポートのみ。</span><span class="sxs-lookup"><span data-stu-id="39238-114">Only support a subset of navigation mapping capabilities - Specifically:</span></span>
  - <span data-ttu-id="39238-115">リレーションシップのプリンシパル end として機能しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="39238-115">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="39238-116">エンティティを指す参照ナビゲーション プロパティのみを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="39238-116">They can only contain reference navigation properties pointing to entities.</span></span>
  - <span data-ttu-id="39238-117">エンティティは、クエリの型にナビゲーション プロパティを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="39238-117">Entities cannot contain navigation properties to query types.</span></span>
- <span data-ttu-id="39238-118">対処する、 _ModelBuilder_を使用して、`Query`メソッドではなく、`Entity`メソッド。</span><span class="sxs-lookup"><span data-stu-id="39238-118">Are addressed on the _ModelBuilder_ using the `Query` method rather than the `Entity` method.</span></span>
- <span data-ttu-id="39238-119">マップされて、 _DbContext_型のプロパティを介して`DbQuery<T>`なく `DbSet<T>`</span><span class="sxs-lookup"><span data-stu-id="39238-119">Are mapped on the _DbContext_ through properties of type `DbQuery<T>` rather than `DbSet<T>`</span></span>
- <span data-ttu-id="39238-120">使用してデータベース オブジェクトにマップされて、`ToView`メソッド、なく`ToTable`します。</span><span class="sxs-lookup"><span data-stu-id="39238-120">Are mapped to database objects using the `ToView` method, rather than `ToTable`.</span></span>
- <span data-ttu-id="39238-121">マップすることがあります、_クエリを定義する_- クエリの種類のデータ ソースが機能するモデルで宣言されている 2 番目のクエリは、クエリを定義します。</span><span class="sxs-lookup"><span data-stu-id="39238-121">May be mapped to a _defining query_ - A defining query is a secondary query declared in the model that acts a data source for a query type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="39238-122">使用シナリオ</span><span class="sxs-lookup"><span data-stu-id="39238-122">Usage scenarios</span></span>

<span data-ttu-id="39238-123">クエリの種類の主な使用シナリオを次に示します。</span><span class="sxs-lookup"><span data-stu-id="39238-123">Some of the main usage scenarios for query types are:</span></span>

- <span data-ttu-id="39238-124">アドホックの戻り値の型として使用される`FromSql()`クエリ。</span><span class="sxs-lookup"><span data-stu-id="39238-124">Serving as the return type for ad hoc `FromSql()` queries.</span></span>
- <span data-ttu-id="39238-125">データベース ビューへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="39238-125">Mapping to database views.</span></span>
- <span data-ttu-id="39238-126">定義された主キーがないテーブルへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="39238-126">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="39238-127">モデルで定義されているクエリへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="39238-127">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="39238-128">データベース オブジェクトへのマッピング</span><span class="sxs-lookup"><span data-stu-id="39238-128">Mapping to database objects</span></span>

<span data-ttu-id="39238-129">使用して実現は、データベース オブジェクトにクエリ型のマッピング、 `ToView` fluent API。</span><span class="sxs-lookup"><span data-stu-id="39238-129">Mapping a query type to a database object is achieved using the `ToView` fluent API.</span></span> <span data-ttu-id="39238-130">このメソッドで指定されたデータベース オブジェクトは、EF Core の観点から、_ビュー_、読み取り専用クエリのソースとして扱われることを意味し、更新プログラムの対象となる、挿入または削除できません操作。</span><span class="sxs-lookup"><span data-stu-id="39238-130">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="39238-131">ただし、これは限りませんするデータベース オブジェクトがあるデータベース ビューを実際に必要 - 読み取り専用として扱われるデータベース テーブルにもできます。</span><span class="sxs-lookup"><span data-stu-id="39238-131">However, this does not mean that the database object is actually required to be a database view - It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="39238-132">逆に、エンティティ型の場合は、EF Core と想定でデータベース オブジェクトが指定されている、`ToTable`としてメソッドを処理できる、_テーブル_、つまりクエリのソースとして使用できますが、削除、更新プログラムも対象となる、および挿入操作です。</span><span class="sxs-lookup"><span data-stu-id="39238-132">Conversely, for entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="39238-133">実際には、データベース ビューの名前を指定できます`ToTable`データベースに対して更新可能にするビューが構成されている限り問題なく機能すべて。</span><span class="sxs-lookup"><span data-stu-id="39238-133">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

## <a name="example"></a><span data-ttu-id="39238-134">例</span><span class="sxs-lookup"><span data-stu-id="39238-134">Example</span></span>

<span data-ttu-id="39238-135">次の例は、クエリの種類を使用して、データベース ビューをクエリする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="39238-135">The following example shows how to use Query Type to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="39238-136">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryTypes)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="39238-136">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/QueryTypes) on GitHub.</span></span>

<span data-ttu-id="39238-137">最初に、ブログや投稿の単純なモデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="39238-137">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Entities)]

<span data-ttu-id="39238-138">次に、各ブログに関連する投稿の数を照会することを許可する単純なデータベース ビューを定義します。</span><span class="sxs-lookup"><span data-stu-id="39238-138">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#View)]

<span data-ttu-id="39238-139">次に、データベース ビューから結果を保持するクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="39238-139">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#QueryType)]

<span data-ttu-id="39238-140">次に、クエリの種類で構成_OnModelCreating_を使用して、 `modelBuilder.Query<T>` API。</span><span class="sxs-lookup"><span data-stu-id="39238-140">Next, we configure the query type in _OnModelCreating_ using the `modelBuilder.Query<T>` API.</span></span>
<span data-ttu-id="39238-141">クエリの種類のマッピングを構成するのに標準の fluent 構成 Api を使用します。</span><span class="sxs-lookup"><span data-stu-id="39238-141">We use standard fluent configuration APIs to configure the mapping for the Query Type:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Configuration)]

<span data-ttu-id="39238-142">次に、を`DbContext` `DbQuery<T>`含めるようにを構成します。[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#DbQuery)]</span><span class="sxs-lookup"><span data-stu-id="39238-142">Next, we configure the `DbContext` to include the `DbQuery<T>`: [!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#DbQuery)]</span></span>

<span data-ttu-id="39238-143">最後に、標準的な方法で、データベース ビューを照会できます。</span><span class="sxs-lookup"><span data-stu-id="39238-143">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/QueryTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="39238-144">この型に対するクエリのルートとして機能するには、(DbQuery) コンテキスト レベルのクエリ プロパティも定義しておいたに注意してください。</span><span class="sxs-lookup"><span data-stu-id="39238-144">Note we have also defined a context level query property (DbQuery) to act as a root for queries against this type.</span></span>
