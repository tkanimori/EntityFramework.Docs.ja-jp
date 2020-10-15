---
title: キーなしエンティティ型-EF Core
description: Entity Framework Core を使用してキーなしエンティティ型を構成する方法
author: AndriySvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: cb4ce44526ada77e37eb4dceb9986a670ea3656b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063804"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="5a5fc-103">キーなしエンティティ型</span><span class="sxs-lookup"><span data-stu-id="5a5fc-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="5a5fc-104">この機能は、クエリの種類の名前の下に追加されました。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-104">This feature was added under the name of query types.</span></span> <span data-ttu-id="5a5fc-105">EF Core 3.0 では、概念はキーなしエンティティ型に名前変更されました。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span> <span data-ttu-id="5a5fc-106">`[Keyless]`データ注釈は、EFCore 5.0 で使用できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-106">The `[Keyless]` Data Annotation became available in EFCore 5.0.</span></span>

<span data-ttu-id="5a5fc-107">EF Core モデルには、通常のエンティティ型に加えて、 _キーなしエンティティ型_を含めることができます。これを使用すると、キー値が含まれていないデータに対してデータベースクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-107">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="5a5fc-108">キーなしエンティティ型の定義</span><span class="sxs-lookup"><span data-stu-id="5a5fc-108">Defining Keyless entity types</span></span>

<span data-ttu-id="5a5fc-109">キーなしエンティティ型は、データ注釈または Fluent API を使用して定義できます。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-109">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="5a5fc-110">データの注釈</span><span class="sxs-lookup"><span data-stu-id="5a5fc-110">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="5a5fc-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="5a5fc-111">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="5a5fc-112">キーなしエンティティ型の特性</span><span class="sxs-lookup"><span data-stu-id="5a5fc-112">Keyless entity types characteristics</span></span>

<span data-ttu-id="5a5fc-113">キーなしエンティティ型は、継承マッピングやナビゲーションプロパティなど、通常のエンティティ型と同じマッピング機能の多くをサポートしています。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-113">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="5a5fc-114">リレーショナルストアでは、fluent API メソッドまたはデータ注釈を使用して、対象のデータベースオブジェクトと列を構成できます。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-114">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="5a5fc-115">ただし、これらは通常のエンティティ型とは異なります。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-115">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="5a5fc-116">キーを定義することはできません。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-116">Cannot have a key defined.</span></span>
- <span data-ttu-id="5a5fc-117">は _Dbcontext_ の変更に対して追跡されないため、データベースで挿入、更新、または削除されることはありません。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-117">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="5a5fc-118">は規約によって検出されません。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-118">Are never discovered by convention.</span></span>
- <span data-ttu-id="5a5fc-119">では、ナビゲーションマッピング機能のサブセットのみをサポートしています。具体的には、</span><span class="sxs-lookup"><span data-stu-id="5a5fc-119">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="5a5fc-120">これらは、リレーションシップのプリンシパル end としては機能しません。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-120">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="5a5fc-121">所有しているエンティティに対するナビゲーションが含まれていない可能性があります</span><span class="sxs-lookup"><span data-stu-id="5a5fc-121">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="5a5fc-122">これらには、標準エンティティを指す参照ナビゲーションプロパティのみを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-122">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="5a5fc-123">エンティティには、キーなしエンティティ型へのナビゲーションプロパティを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-123">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="5a5fc-124">`[Keyless]`データ注釈またはメソッド呼び出しを使用して構成する必要があり `.HasNoKey()` ます。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-124">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="5a5fc-125">を _定義するクエリ_にマップできます。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-125">May be mapped to a _defining query_.</span></span> <span data-ttu-id="5a5fc-126">定義クエリは、キーなしエンティティ型のデータソースとして機能するモデルで宣言されたクエリです。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-126">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="5a5fc-127">使用シナリオ</span><span class="sxs-lookup"><span data-stu-id="5a5fc-127">Usage scenarios</span></span>

<span data-ttu-id="5a5fc-128">キーなしエンティティ型の主な使用シナリオの一部を次に示します。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-128">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="5a5fc-129">[生の SQL クエリ](xref:core/querying/raw-sql)の戻り値の型としてサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-129">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="5a5fc-130">主キーが含まれていないデータベースビューへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-130">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="5a5fc-131">主キーが定義されていないテーブルへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-131">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="5a5fc-132">モデルで定義されているクエリへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-132">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="5a5fc-133">データベースオブジェクトへのマッピング</span><span class="sxs-lookup"><span data-stu-id="5a5fc-133">Mapping to database objects</span></span>

<span data-ttu-id="5a5fc-134">キーなしエンティティ型のデータベースオブジェクトへのマッピングは、または fluent API を使用して実現され `ToTable` `ToView` ます。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-134">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="5a5fc-135">EF Core の観点から見ると、このメソッドで指定されるデータベースオブジェクトは _ビュー_であり、読み取り専用のクエリソースとして扱われ、update、insert、delete の各操作の対象にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-135">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="5a5fc-136">ただし、これは、データベースオブジェクトが実際にデータベースビューである必要があることを意味するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-136">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="5a5fc-137">または、読み取り専用として扱われるデータベーステーブルにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-137">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="5a5fc-138">逆に、通常のエンティティ型の場合、EF Core は、メソッドで指定されたデータベースオブジェクトをテーブルとして扱うことができることを前提としてい `ToTable` ます。つまり、クエリソースとして使用できますが、update、delete、および insert 操作の対象にすることができます。 _table_</span><span class="sxs-lookup"><span data-stu-id="5a5fc-138">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="5a5fc-139">実際には、でデータベースビューの名前を指定でき `ToTable` ます。また、ビューがデータベースで更新可能になるように構成されていれば、すべてが正常に動作します。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-139">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="5a5fc-140">`ToView` は、オブジェクトが既にデータベースに存在し、移行によって作成されないことを前提としています。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-140">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="5a5fc-141">例</span><span class="sxs-lookup"><span data-stu-id="5a5fc-141">Example</span></span>

<span data-ttu-id="5a5fc-142">次の例では、キーなしエンティティ型を使用してデータベースビューに対してクエリを実行する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-142">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="5a5fc-143">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-143">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="5a5fc-144">まず、単純なブログと Post モデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-144">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="5a5fc-145">次に、各ブログに関連付けられている投稿の数を照会できる単純なデータベースビューを定義します。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-145">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="5a5fc-146">次に、データベースビューの結果を保持するクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-146">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="5a5fc-147">次に、API を使用して、 _Onmodelcreating_ エンティティ型を構成し `HasNoKey` ます。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-147">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="5a5fc-148">Fluent 構成 API を使用して、キーなしエンティティ型のマッピングを構成します。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-148">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="5a5fc-149">次に、を `DbContext` 含めるようにを構成し `DbSet<T>` ます。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-149">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="5a5fc-150">最後に、標準的な方法でデータベースビューに対してクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-150">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="5a5fc-151">また、この型に対するクエリのルートとして機能するコンテキストレベルのクエリプロパティ (DbSet) も定義されています。</span><span class="sxs-lookup"><span data-stu-id="5a5fc-151">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
