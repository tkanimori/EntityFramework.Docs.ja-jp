---
title: キーレス エンティティ型 - EF コア
description: エンティティ フレームワーク コアを使用してキーなしのエンティティ型を構成する方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 9/13/2019
uid: core/modeling/keyless-entity-types
ms.openlocfilehash: 496e1e8983ba2d5e15dbee02607ea3f2c861503e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434215"
---
# <a name="keyless-entity-types"></a><span data-ttu-id="12e50-103">キーなしエンティティ型</span><span class="sxs-lookup"><span data-stu-id="12e50-103">Keyless Entity Types</span></span>

> [!NOTE]
> <span data-ttu-id="12e50-104">この機能は、クエリの種類の名前で EF Core 2.1 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="12e50-104">This feature was added in EF Core 2.1 under the name of query types.</span></span> <span data-ttu-id="12e50-105">EF Core 3.0 では、この概念はキーレス エンティティ型に変更されました。</span><span class="sxs-lookup"><span data-stu-id="12e50-105">In EF Core 3.0 the concept was renamed to keyless entity types.</span></span>

<span data-ttu-id="12e50-106">通常のエンティティ型に加えて、EF Core モデルには_キーなしのエンティティ型_を含めることができます。</span><span class="sxs-lookup"><span data-stu-id="12e50-106">In addition to regular entity types, an EF Core model can contain _keyless entity types_, which can be used to carry out database queries against data that doesn't contain key values.</span></span>

## <a name="defining-keyless-entity-types"></a><span data-ttu-id="12e50-107">キーレス エンティティ型の定義</span><span class="sxs-lookup"><span data-stu-id="12e50-107">Defining Keyless entity types</span></span>

<span data-ttu-id="12e50-108">キーレス エンティティ型は、データ注釈または Fluent API を使用して定義できます。</span><span class="sxs-lookup"><span data-stu-id="12e50-108">Keyless entity types can be defined using either the Data Annotation or the Fluent API:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="12e50-109">データの注釈</span><span class="sxs-lookup"><span data-stu-id="12e50-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Keyless.cs?Name=Keyless&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="12e50-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="12e50-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Keyless.cs?Name=Keyless&highlight=4)]

***

## <a name="keyless-entity-types-characteristics"></a><span data-ttu-id="12e50-111">キーなしエンティティタイプの特性</span><span class="sxs-lookup"><span data-stu-id="12e50-111">Keyless entity types characteristics</span></span>

<span data-ttu-id="12e50-112">キーレス エンティティ型は、継承マッピングやナビゲーション プロパティなど、通常のエンティティ型と同じマッピング機能の多くをサポートします。</span><span class="sxs-lookup"><span data-stu-id="12e50-112">Keyless entity types support many of the same mapping capabilities as regular entity types, like inheritance mapping and navigation properties.</span></span> <span data-ttu-id="12e50-113">リレーショナル ストアでは、fluent API メソッドまたはデータ注釈を使用して、ターゲット データベースのオブジェクトと列を構成できます。</span><span class="sxs-lookup"><span data-stu-id="12e50-113">On relational stores, they can configure the target database objects and columns via fluent API methods or data annotations.</span></span>

<span data-ttu-id="12e50-114">ただし、通常のエンティティ型とは異なります。</span><span class="sxs-lookup"><span data-stu-id="12e50-114">However, they are different from regular entity types in that they:</span></span>

- <span data-ttu-id="12e50-115">キーを定義することはできません。</span><span class="sxs-lookup"><span data-stu-id="12e50-115">Cannot have a key defined.</span></span>
- <span data-ttu-id="12e50-116">_DbContext_での変更を追跡しないため、データベースに対して挿入、更新、または削除されることはありません。</span><span class="sxs-lookup"><span data-stu-id="12e50-116">Are never tracked for changes in the _DbContext_ and therefore are never inserted, updated or deleted on the database.</span></span>
- <span data-ttu-id="12e50-117">慣例によって発見されることはありません。</span><span class="sxs-lookup"><span data-stu-id="12e50-117">Are never discovered by convention.</span></span>
- <span data-ttu-id="12e50-118">具体的には、ナビゲーション マッピング機能のサブセットのみをサポートします。</span><span class="sxs-lookup"><span data-stu-id="12e50-118">Only support a subset of navigation mapping capabilities, specifically:</span></span>
  - <span data-ttu-id="12e50-119">彼らは決して関係の主要な終わりとして機能しないかもしれません。</span><span class="sxs-lookup"><span data-stu-id="12e50-119">They may never act as the principal end of a relationship.</span></span>
  - <span data-ttu-id="12e50-120">所有エンティティへのナビゲーションがない場合があります。</span><span class="sxs-lookup"><span data-stu-id="12e50-120">They may not have navigations to owned entities</span></span>
  - <span data-ttu-id="12e50-121">通常のエンティティを指す参照ナビゲーション プロパティのみを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="12e50-121">They can only contain reference navigation properties pointing to regular entities.</span></span>
  - <span data-ttu-id="12e50-122">エンティティには、キーなしのエンティティ型へのナビゲーション プロパティを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="12e50-122">Entities cannot contain navigation properties to keyless entity types.</span></span>
- <span data-ttu-id="12e50-123">データ注釈またはメソッド呼び`[Keyless]`出しを使用`.HasNoKey()`して構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="12e50-123">Need to be configured with a `[Keyless]` data annotation or a `.HasNoKey()` method call.</span></span>
- <span data-ttu-id="12e50-124">_定義_クエリ にマップされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="12e50-124">May be mapped to a _defining query_.</span></span> <span data-ttu-id="12e50-125">定義クエリは、キーなしエンティティ型のデータ ソースとして機能するモデルで宣言されたクエリです。</span><span class="sxs-lookup"><span data-stu-id="12e50-125">A defining query is a query declared in the model that acts as a data source for a keyless entity type.</span></span>

## <a name="usage-scenarios"></a><span data-ttu-id="12e50-126">使用シナリオ</span><span class="sxs-lookup"><span data-stu-id="12e50-126">Usage scenarios</span></span>

<span data-ttu-id="12e50-127">キーレス エンティティタイプの主な使用シナリオには、次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="12e50-127">Some of the main usage scenarios for keyless entity types are:</span></span>

- <span data-ttu-id="12e50-128">生の[SQL クエリ](xref:core/querying/raw-sql)の戻り値の型として使用する:</span><span class="sxs-lookup"><span data-stu-id="12e50-128">Serving as the return type for [raw SQL queries](xref:core/querying/raw-sql).</span></span>
- <span data-ttu-id="12e50-129">主キーを含まないデータベース ビューへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="12e50-129">Mapping to database views that do not contain a primary key.</span></span>
- <span data-ttu-id="12e50-130">主キーが定義されていないテーブルへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="12e50-130">Mapping to tables that do not have a primary key defined.</span></span>
- <span data-ttu-id="12e50-131">モデルで定義されているクエリへのマッピング。</span><span class="sxs-lookup"><span data-stu-id="12e50-131">Mapping to queries defined in the model.</span></span>

## <a name="mapping-to-database-objects"></a><span data-ttu-id="12e50-132">データベース オブジェクトへのマッピング</span><span class="sxs-lookup"><span data-stu-id="12e50-132">Mapping to database objects</span></span>

<span data-ttu-id="12e50-133">キーなしのエンティティ型をデータベース オブジェクトにマッピングするには、`ToTable`または`ToView`fluent API を使用します。</span><span class="sxs-lookup"><span data-stu-id="12e50-133">Mapping a keyless entity type to a database object is achieved using the `ToTable` or `ToView` fluent API.</span></span> <span data-ttu-id="12e50-134">EF Core の観点からは、このメソッドで指定されたデータベース オブジェクトは_ビュー_であり、読み取り専用のクエリ ソースとして扱われ、更新、挿入、または削除操作の対象になることはできません。</span><span class="sxs-lookup"><span data-stu-id="12e50-134">From the perspective of EF Core, the database object specified in this method is a _view_, meaning that it is treated as a read-only query source and cannot be the target of update, insert or delete operations.</span></span> <span data-ttu-id="12e50-135">ただし、データベース オブジェクトが実際にデータベース ビューである必要があるという意味ではありません。</span><span class="sxs-lookup"><span data-stu-id="12e50-135">However, this does not mean that the database object is actually required to be a database view.</span></span> <span data-ttu-id="12e50-136">また、読み取り専用として扱われるデータベーステーブルを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="12e50-136">It can alternatively be a database table that will be treated as read-only.</span></span> <span data-ttu-id="12e50-137">逆に、通常のエンティティ型の場合、EF Core は`ToTable`、メソッドで指定されたデータベース オブジェクトを_テーブル_として扱うことができると見なします。</span><span class="sxs-lookup"><span data-stu-id="12e50-137">Conversely, for regular entity types, EF Core assumes that a database object specified in the `ToTable` method can be treated as a _table_, meaning that it can be used as a query source but also targeted by update, delete and insert operations.</span></span> <span data-ttu-id="12e50-138">実際には、データベース ビューの名前を指定すると`ToTable`、データベースで更新可能なビューが構成されている限り、すべてが正常に動作するはずです。</span><span class="sxs-lookup"><span data-stu-id="12e50-138">In fact, you can specify the name of a database view in `ToTable` and everything should work fine as long as the view is configured to be updatable on the database.</span></span>

> [!NOTE]
> <span data-ttu-id="12e50-139">`ToView`オブジェクトはデータベースに既に存在し、移行によって作成されないことを前提としています。</span><span class="sxs-lookup"><span data-stu-id="12e50-139">`ToView` assumes that the object already exists in the database and it won't be created by migrations.</span></span>

## <a name="example"></a><span data-ttu-id="12e50-140">例</span><span class="sxs-lookup"><span data-stu-id="12e50-140">Example</span></span>

<span data-ttu-id="12e50-141">次の例は、キーなしのエンティティ型を使用してデータベース ビューを照会する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="12e50-141">The following example shows how to use keyless entity types to query a database view.</span></span>

> [!TIP]
> <span data-ttu-id="12e50-142">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="12e50-142">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/KeylessEntityTypes) on GitHub.</span></span>

<span data-ttu-id="12e50-143">まず、単純なブログと投稿モデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="12e50-143">First, we define a simple Blog and Post model:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Entities)]

<span data-ttu-id="12e50-144">次に、各ブログに関連付けられた投稿数を照会できる単純なデータベースビューを定義します。</span><span class="sxs-lookup"><span data-stu-id="12e50-144">Next, we define a simple database view that will allow us to query the number of posts associated with each blog:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#View)]

<span data-ttu-id="12e50-145">次に、データベース ビューからの結果を保持するクラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="12e50-145">Next, we define a class to hold the result from the database view:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#KeylessEntityType)]

<span data-ttu-id="12e50-146">次に、API を使用して_OnModelCreating_でキー`HasNoKey`レス エンティティ型を構成します。</span><span class="sxs-lookup"><span data-stu-id="12e50-146">Next, we configure the keyless entity type in _OnModelCreating_ using the `HasNoKey` API.</span></span>
<span data-ttu-id="12e50-147">Fluent 構成 API を使用して、キーレス エンティティ型のマッピングを構成します。</span><span class="sxs-lookup"><span data-stu-id="12e50-147">We use fluent configuration API to configure the mapping for the keyless entity type:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Configuration)]

<span data-ttu-id="12e50-148">次に、 を`DbContext`含むようにを`DbSet<T>`構成します。</span><span class="sxs-lookup"><span data-stu-id="12e50-148">Next, we configure the `DbContext` to include the `DbSet<T>`:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#DbSet)]

<span data-ttu-id="12e50-149">最後に、標準的な方法でデータベース ビューを照会できます。</span><span class="sxs-lookup"><span data-stu-id="12e50-149">Finally, we can query the database view in the standard way:</span></span>

[!code-csharp[Main](../../../samples/core/KeylessEntityTypes/Program.cs#Query)]

> [!TIP]
> <span data-ttu-id="12e50-150">この型に対するクエリのルートとして機能するコンテキスト レベルのクエリ プロパティ (DbSet) も定義しています。</span><span class="sxs-lookup"><span data-stu-id="12e50-150">Note we have also defined a context level query property (DbSet) to act as a root for queries against this type.</span></span>
