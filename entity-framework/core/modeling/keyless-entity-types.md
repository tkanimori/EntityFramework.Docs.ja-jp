---
title: キーなしエンティティ型-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 02/26/2018
ms.assetid: 9F4450C5-1A3F-4BB6-AC19-9FAC64292AAD
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 3dbc2700fc9bb277eb90885dfc2506c250ae21f1
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445932"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="3d3c1-102">キーなしエンティティ型</span><span class="sxs-lookup"><span data-stu-id="3d3c1-102">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="3d3c1-103">この機能は、EF Core 2.1 で、クエリの種類の名前の下に追加されました。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-103">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="3d3c1-104">EF Core 3.0 では、概念はキーなしエンティティ型に名前変更されました。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-104">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span>

<span data-ttu-id="3d3c1-105">EF Core モデルには、通常のエンティティ型に加えて、_キーなしエンティティ型_を含めることができます。これを使用すると、キー値が含まれていないデータに対してデータベースクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-105">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="3d3c1-106">キーなしエンティティ型の特性</span><span class="sxs-lookup"><span data-stu-id="3d3c1-106">Keyless entity types characteristics</span></span>

<span data-ttu-id="3d3c1-107">キーなしエンティティ型は、継承マッピングやナビゲーションプロパティなど、通常のエンティティ型と同じマッピング機能の多くをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-107">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="3d3c1-108">リレーショナルストアでは、fluent API メソッドまたはデータ注釈を使用して、対象のデータベースオブジェクトと列を構成できます。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-108">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="3d3c1-109">ただし、これらは通常のエンティティ型とは異なります。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-109">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="3d3c1-110">キーを定義することはできません。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-110">Cannot have a key defined.</span></span>
- <span data-ttu-id="3d3c1-111">は_Dbcontext_の変更に対して追跡されないため、データベースで挿入、更新、または削除されることはありません。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-111">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="3d3c1-112">は規約によって検出されません。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-112">Are never discovered by convention.</span></span>
- <span data-ttu-id="3d3c1-113">では、ナビゲーションマッピング機能のサブセットのみをサポートしています。具体的には、</span><span class="sxs-lookup"><span data-stu-id="3d3c1-113">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="3d3c1-114">これらは、リレーションシップのプリンシパル end としては機能しません。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-114">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="3d3c1-115">所有しているエンティティに対するナビゲーションが含まれていない可能性があります</span><span class="sxs-lookup"><span data-stu-id="3d3c1-115">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="3d3c1-116">これらには、標準エンティティを指す参照ナビゲーションプロパティのみを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-116">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="3d3c1-117">エンティティには、キーなしエンティティ型へのナビゲーションプロパティを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-117">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="3d3c1-118">@No__t 0 のメソッド呼び出しで構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-118">Need to be configured with `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="3d3c1-119">を_定義するクエリ_にマップできます。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-119">May be mapped to a _defining query_.</span></span> <span data-ttu-id="3d3c1-120">定義クエリは、キーなしエンティティ型のデータソースとして機能するモデルで宣言されたクエリです。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-120">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="3d3c1-121">使用シナリオ</span><span class="sxs-lookup"><span data-stu-id="3d3c1-121">Usage scenarios</span></span>

<span data-ttu-id="3d3c1-122">キーなしエンティティ型の主な使用シナリオの一部を次に示します。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-122">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="3d3c1-123">[生の SQL クエリ](xref:core/querying/raw-sql)の戻り値の型としてサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-123">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="3d3c1-124">主キーが含まれていないデータベースビューへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-124">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="3d3c1-125">主キーが定義されていないテーブルへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-125">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="3d3c1-126">モデルで定義されているクエリへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-126">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="3d3c1-127">データベースオブジェクトへのマッピング</span><span class="sxs-lookup"><span data-stu-id="3d3c1-127">Mapping to database objects</span></span>

<span data-ttu-id="3d3c1-128">キーなしエンティティ型をデータベースオブジェクトにマップするには、`ToTable` または `ToView` fluent API を使用します。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-128">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="3d3c1-129">EF Core の観点から見ると、このメソッドで指定されるデータベースオブジェクトは_ビュー_であり、読み取り専用のクエリソースとして扱われ、update、insert、delete の各操作の対象にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-129">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="3d3c1-130">ただし、これは、データベースオブジェクトが実際にデータベースビューである必要があることを意味するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-130">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="3d3c1-131">または、読み取り専用として扱われるデータベーステーブルにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-131">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="3d3c1-132">逆に、通常のエンティティ型の場合、EF Core では、`ToTable` メソッドで指定されたデータベースオブジェクトを_テーブル_として扱うことができます。つまり、クエリソースとして使用できますが、update、delete、および insert 操作の対象にすることができます。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-132">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="3d3c1-133">実際には `ToTable` でデータベースビューの名前を指定できます。ビューがデータベースで更新可能になるように構成されていれば、すべてが正常に動作します。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-133">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="3d3c1-134">`ToView` は、オブジェクトが既にデータベースに存在し、移行によって作成されないことを前提としています。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-134">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="3d3c1-135">例</span><span class="sxs-lookup"><span data-stu-id="3d3c1-135">Example</span></span>

<span data-ttu-id="3d3c1-136">次の例では、キーなしエンティティ型を使用してデータベースビューに対してクエリを実行する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-136">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="3d3c1-137">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-137">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="3d3c1-138">まず、単純なブログと Post モデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-138">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="3d3c1-139">次に、各ブログに関連付けられている投稿の数を照会できる単純なデータベースビューを定義します。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-139">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="3d3c1-140">次に、データベースビューの結果を保持するクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-140">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="3d3c1-141">次に、`HasNoKey` API を使用して、 _Onmodelcreating_エンティティ型を構成します。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-141">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="3d3c1-142">Fluent 構成 API を使用して、キーなしエンティティ型のマッピングを構成します。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-142">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="3d3c1-143">次に、`DbSet<T>` を含めるように `DbContext` を構成します。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-143">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="3d3c1-144">最後に、標準的な方法でデータベースビューに対してクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-144">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="3d3c1-145">また、この型に対するクエリのルートとして機能するコンテキストレベルのクエリプロパティ (DbSet) も定義されています。</span><span class="sxs-lookup"><span data-stu-id="3d3c1-145">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
