---
title: クエリの種類]-[EF コア
author: anpete
ms.author: anpete
ms.date: 2/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
ms.technology: entity-framework-core
uid: core/modeling/query-types
ms.openlocfilehash: f16e3a130f3a4f92b2bf6014f2df0ca4eec56a25
ms.sourcegitcommit: 038acd91ce2f5a28d76dcd2eab72eeba225e366d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34163175"
---
# <a name="query-types"></a><span data-ttu-id="a5f3c-102">クエリの種類</span><span class="sxs-lookup"><span data-stu-id="a5f3c-102">Query Types</span></span>
> [!NOTE]
> <span data-ttu-id="a5f3c-103">この機能は、EF コア 2.1 の新機能</span><span class="sxs-lookup"><span data-stu-id="a5f3c-103">This feature is new in EF Core 2.1</span></span>

<span data-ttu-id="a5f3c-104">エンティティ型だけでなく、EF の主要なモデルを含めることができます_クエリ タイプ_エンティティ型にマップされていないデータに対するデータベース クエリを実行するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-104">In addition to entity types, an EF Core model can contain _query types_, which can be used to carry out database queries against data that isn't mapped to entity types.</span></span>

<span data-ttu-id="a5f3c-105">クエリの種類では、エンティティの種類と多くの類似点があります。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-105">Query types have many similarities with entity types:</span></span>

- <span data-ttu-id="a5f3c-106">追加することも、モデルにいずれかで`OnModelCreating`、または、派生の「設定」プロパティを介して_DbContext_です。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-106">They can also be added to the model either in `OnModelCreating`, or via a "set" property on a derived _DbContext_.</span></span>
- <span data-ttu-id="a5f3c-107">多くのマッピング、(以下の制限事項を参照してください) のナビゲーション プロパティの継承と同様に、リレーショナル ストア、ターゲット データベースのオブジェクトと fluent API のメソッドまたはデータ注釈を使用して列を構成する機能は同じのマッピング機能をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-107">They support many of the same mapping capabilities, like inheritance mapping, navigation properties (see limitations below) and, on relational stores, the ability to configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="a5f3c-108">ただしはエンティティを異なる型です。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-108">However they are different from entity types in that they:</span></span>

- <span data-ttu-id="a5f3c-109">定義するキーは不要です。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-109">Do not require a key to be defined.</span></span>
- <span data-ttu-id="a5f3c-110">変更追跡されることはありません、 _DbContext_とそのためことはありません挿入、更新または削除、データベースでします。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-110">Are never tracked for changes on the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="a5f3c-111">規則によって検出されることはありません。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-111">Are never discovered by convention.</span></span>
- <span data-ttu-id="a5f3c-112">具体的には、ナビゲーションのマッピング機能 - のサブセットをサポートのみ。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-112">Only support a subset of navigation mapping capabilities - Specifically:</span></span>
  - <span data-ttu-id="a5f3c-113">リレーションシップのプリンシパル end として機能する可能性があることはありません。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-113">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="a5f3c-114">エンティティを指す参照ナビゲーション プロパティのみを含み、ことができます。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-114">They can only contain reference navigation properties pointing to entities.</span></span>
  - <span data-ttu-id="a5f3c-115">エンティティは、クエリの型にナビゲーション プロパティを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-115">Entities cannot contain navigation properties to query types.</span></span>
- <span data-ttu-id="a5f3c-116">対処する、 _ModelBuilder_を使用して、`Query`メソッドではなく、`Entity`メソッドです。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-116">Are addressed on the _ModelBuilder_ using the `Query` method rather than the `Entity` method.</span></span>
- <span data-ttu-id="a5f3c-117">マップされて、 _DbContext_型のプロパティを介して`DbQuery<T>`なく `DbSet<T>`</span><span class="sxs-lookup"><span data-stu-id="a5f3c-117">Are mapped on the _DbContext_ through properties of type `DbQuery<T>` rather than `DbSet<T>`</span></span>
- <span data-ttu-id="a5f3c-118">使用してデータベース オブジェクトにマップされて、`ToView`メソッド、なく`ToTable`です。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-118">Are mapped to database objects using the `ToView` method, rather than `ToTable`.</span></span>
- <span data-ttu-id="a5f3c-119">マップすることがあります、_クエリを定義する_- クエリの種類のデータ ソースは、機能、モデルで宣言されているセカンダリのクエリは、クエリを定義します。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-119">May be mapped to a _defining query_ - A defining query is a secondary query declared in the model that acts a data source for a query type.</span></span>

<span data-ttu-id="a5f3c-120">クエリの種類の主な使用シナリオを次に示します。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-120">Some of the main usage scenarios for query types are:</span></span>

- <span data-ttu-id="a5f3c-121">戻り値の型として機能しているアドホック`FromSql()`クエリ。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-121">Serving as the return type for ad hoc `FromSql()` queries.</span></span>
- <span data-ttu-id="a5f3c-122">データベース ビューへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-122">Mapping to database views.</span></span>
- <span data-ttu-id="a5f3c-123">定義された主キーがないテーブルへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-123">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="a5f3c-124">モデルで定義されているクエリへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-124">Mapping to queries defined in the model.</span></span>

> [!TIP]
> <span data-ttu-id="a5f3c-125">使用して実現は、データベース オブジェクトへのクエリの種類のマッピング、 `ToView` fluent API です。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-125">Mapping a query type to a database object is achieved using the `ToView` fluent API.</span></span> <span data-ttu-id="a5f3c-126">このメソッドで指定されたデータベース オブジェクトは、EF コアの観点から、_ビュー_、読み取り専用のクエリのソースとして扱われることを意味し、更新プログラムの対象となる、挿入または削除できません操作します。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-126">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="a5f3c-127">ただし、これはいないというデータベース オブジェクトが実際には、データベース ビューを指定するために必要 - 読み取り専用として扱われるデータベース テーブルに代わりにできること。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-127">However, this does not mean that the database object is actually required to be a database view - It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="a5f3c-128">逆に、エンティティ型の EF コア前提としていますにデータベース オブジェクトが指定されている、`ToTable`としてメソッドを処理できる、_テーブル_クエリのソースとして使用できますけれども、削除、更新プログラムも対象となることを意味、および挿入操作です。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-128">Conversely, for entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="a5f3c-129">実際には、データベース ビューの名前を指定できます`ToTable`うまく動作するでしょうデータベースで更新できるビューが構成されている限り、します。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-129">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

## <a name="example"></a><span data-ttu-id="a5f3c-130">例</span><span class="sxs-lookup"><span data-stu-id="a5f3c-130">Example</span></span>

<span data-ttu-id="a5f3c-131">次の例では、クエリの種類を使用して、データベース ビューをクエリする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-131">The following example shows how to use Query Type to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="a5f3c-132">この記事の[サンプル](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-132">You can view this article's [sample](https://github.com/aspnet/EntityFrameworkCore/tree/dev/samples/QueryTypes) on GitHub.</span></span>

<span data-ttu-id="a5f3c-133">最初に、単純なブログと Post のモデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-133">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Entities)]

<span data-ttu-id="a5f3c-134">次に、各ブログに関連する投稿の数を照会できる単純なデータベース ビューを定義します。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-134">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#View)]

<span data-ttu-id="a5f3c-135">次に、データベース ビューから結果を保持するクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-135">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#QueryType)]

<span data-ttu-id="a5f3c-136">クエリの種類を次に、構成_OnModelCreating_を使用して、 `modelBuilder.Query<T>` API です。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-136">Next, we configure the query type in _OnModelCreating_ using the `modelBuilder.Query<T>` API.</span></span>
<span data-ttu-id="a5f3c-137">標準 fluent 構成 Api を使用して、クエリの種類のマッピングを構成します。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-137">We use standard fluent configuration APIs to configure the mapping for the Query Type:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Configuration)]

<span data-ttu-id="a5f3c-138">最後に、データベース ビューをクエリして標準の方法で。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-138">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../efcore-dev/samples/QueryTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="a5f3c-139">この型に対するクエリのルートとして機能するには、(DbQuery) コンテキスト レベルのクエリ プロパティを定義していますも注意してください。</span><span class="sxs-lookup"><span data-stu-id="a5f3c-139">Note we have also defined a context level query property (DbQuery) to act as a root for queries against this type.</span></span>
