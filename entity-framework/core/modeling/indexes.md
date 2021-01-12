---
title: インデックス-EF Core
description: Entity Framework Core モデルでのインデックスの構成
author: roji
ms.date: 12/16/2019
uid: core/modeling/indexes
ms.openlocfilehash: ab81b108c4ff518cf98b7e835da3553c0c41efed
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128538"
---
# <a name="indexes"></a><span data-ttu-id="91d21-103">Indexes</span><span class="sxs-lookup"><span data-stu-id="91d21-103">Indexes</span></span>

<span data-ttu-id="91d21-104">インデックスは、多くのデータストアで共通の概念です。</span><span class="sxs-lookup"><span data-stu-id="91d21-104">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="91d21-105">データストアでの実装は異なる場合がありますが、列または列のセットに基づいて参照を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="91d21-105">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span> <span data-ttu-id="91d21-106">適切なインデックスの使用方法の詳細については、パフォーマンスドキュメントの「 [インデックス」セクション](xref:core/performance/efficient-querying#use-indexes-properly) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91d21-106">See the [indexes section](xref:core/performance/efficient-querying#use-indexes-properly) in the performance documentation for more information on good index usage.</span></span>

<span data-ttu-id="91d21-107">列に対してインデックスを指定するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="91d21-107">You can specify an index over a column as follows:</span></span>

## <a name="data-annotations"></a>[<span data-ttu-id="91d21-108">データの注釈</span><span class="sxs-lookup"><span data-stu-id="91d21-108">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Index.cs?name=Index&highlight=1)]

> [!NOTE]
> <span data-ttu-id="91d21-109">データ注釈を使用したインデックスの構成は、EF Core 5.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="91d21-109">Configuring indexes via Data Annotations has been introduced in EF Core 5.0.</span></span>

## <a name="fluent-api"></a>[<span data-ttu-id="91d21-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="91d21-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

***

> [!NOTE]
> <span data-ttu-id="91d21-111">規則により、外部キーとして使用される各プロパティ (またはプロパティのセット) にインデックスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="91d21-111">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>
>
> <span data-ttu-id="91d21-112">EF Core では、個別のプロパティセットごとに1つのインデックスのみがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="91d21-112">EF Core only supports one index per distinct set of properties.</span></span> <span data-ttu-id="91d21-113">規則または以前の構成によって既にインデックスが定義されている一連のプロパティにインデックスを構成する場合は、そのインデックスの定義を変更します。</span><span class="sxs-lookup"><span data-stu-id="91d21-113">If you configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="91d21-114">これは、規則によって作成されたインデックスをさらに構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="91d21-114">This is useful if you want to further configure an index that was created by convention.</span></span>

## <a name="composite-index"></a><span data-ttu-id="91d21-115">複合インデックス</span><span class="sxs-lookup"><span data-stu-id="91d21-115">Composite index</span></span>

<span data-ttu-id="91d21-116">インデックスは、複数の列にまたがることもできます。</span><span class="sxs-lookup"><span data-stu-id="91d21-116">An index can also span more than one column:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="91d21-117">データの注釈</span><span class="sxs-lookup"><span data-stu-id="91d21-117">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexComposite.cs?name=Composite&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="91d21-118">Fluent API</span><span class="sxs-lookup"><span data-stu-id="91d21-118">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

<span data-ttu-id="91d21-119">\*\*_</span><span class="sxs-lookup"><span data-stu-id="91d21-119">\*\*_</span></span>

<span data-ttu-id="91d21-120">複数の列に対するインデックス (_composite インデックス \* とも呼ばれます) は、インデックスの列をフィルター処理するクエリを高速化しますが、インデックスによってカバーされる *最初* の列に対してのみフィルター処理を行うクエリも行います。</span><span class="sxs-lookup"><span data-stu-id="91d21-120">Indexes over multiple columns, also known as _composite indexes\*, speed up queries which filter on index's columns, but also queries which only filter on the *first* columns covered by the index.</span></span> <span data-ttu-id="91d21-121">詳細については、パフォーマンスに関する [ドキュメント](xref:core/performance/efficient-querying#use-indexes-properly) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="91d21-121">See the [performance docs](xref:core/performance/efficient-querying#use-indexes-properly) for more information.</span></span>

## <a name="index-uniqueness"></a><span data-ttu-id="91d21-122">インデックスの一意性</span><span class="sxs-lookup"><span data-stu-id="91d21-122">Index uniqueness</span></span>

<span data-ttu-id="91d21-123">既定では、インデックスは一意ではありません。複数の行がインデックスの列セットに対して同じ値を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="91d21-123">By default, indexes aren't unique: multiple rows are allowed to have the same value(s) for the index's column set.</span></span> <span data-ttu-id="91d21-124">次のように、インデックスを一意にすることができます。</span><span class="sxs-lookup"><span data-stu-id="91d21-124">You can make an index unique as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="91d21-125">データの注釈</span><span class="sxs-lookup"><span data-stu-id="91d21-125">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexUnique.cs?name=IndexUnique&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="91d21-126">Fluent API</span><span class="sxs-lookup"><span data-stu-id="91d21-126">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

***

<span data-ttu-id="91d21-127">インデックスの列セットに同じ値を持つ複数のエンティティを挿入しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="91d21-127">Attempting to insert more than one entity with the same values for the index's column set will cause an exception to be thrown.</span></span>

## <a name="index-name"></a><span data-ttu-id="91d21-128">インデックス名</span><span class="sxs-lookup"><span data-stu-id="91d21-128">Index name</span></span>

<span data-ttu-id="91d21-129">慣例により、リレーショナルデータベースに作成されたインデックスにはという名前が付けられ `IX_<type name>_<property name>` ます。</span><span class="sxs-lookup"><span data-stu-id="91d21-129">By convention, indexes created in a relational database are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="91d21-130">複合インデックスの場合、は、 `<property name>` アンダースコアで区切られたプロパティ名のリストになります。</span><span class="sxs-lookup"><span data-stu-id="91d21-130">For composite indexes, `<property name>` becomes an underscore separated list of property names.</span></span>

<span data-ttu-id="91d21-131">次のように、データベースに作成されたインデックスの名前を設定できます。</span><span class="sxs-lookup"><span data-stu-id="91d21-131">You can set the name of the index created in the database:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="91d21-132">データの注釈</span><span class="sxs-lookup"><span data-stu-id="91d21-132">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IndexName.cs?name=IndexName&highlight=1)]

### <a name="fluent-api"></a>[<span data-ttu-id="91d21-133">Fluent API</span><span class="sxs-lookup"><span data-stu-id="91d21-133">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

***

## <a name="index-filter"></a><span data-ttu-id="91d21-134">インデックスフィルター</span><span class="sxs-lookup"><span data-stu-id="91d21-134">Index filter</span></span>

<span data-ttu-id="91d21-135">一部のリレーショナルデータベースでは、フィルター処理されたインデックスまたは部分的なインデックスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="91d21-135">Some relational databases allow you to specify a filtered or partial index.</span></span> <span data-ttu-id="91d21-136">これにより、列の値のサブセットにのみインデックスを作成し、インデックスのサイズを小さくして、パフォーマンスとディスク領域の使用率を向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="91d21-136">This allows you to index only a subset of a column's values, reducing the index's size and improving both performance and disk space usage.</span></span> <span data-ttu-id="91d21-137">フィルター選択されたインデックス SQL Server の詳細については、 [のドキュメントを参照してください](/sql/relational-databases/indexes/create-filtered-indexes)。</span><span class="sxs-lookup"><span data-stu-id="91d21-137">For more information on SQL Server filtered indexes, [see the documentation](/sql/relational-databases/indexes/create-filtered-indexes).</span></span>

<span data-ttu-id="91d21-138">Fluent API を使用して、SQL 式として指定されたインデックスにフィルターを指定できます。</span><span class="sxs-lookup"><span data-stu-id="91d21-138">You can use the Fluent API to specify a filter on an index, provided as a SQL expression:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

<span data-ttu-id="91d21-139">SQL Server プロバイダー EF を使用すると、 `'IS NOT NULL'` 一意のインデックスの一部である null 許容型のすべての列に対してフィルターが追加されます。</span><span class="sxs-lookup"><span data-stu-id="91d21-139">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="91d21-140">この規則をオーバーライドするには、 `null` 値を指定します。</span><span class="sxs-lookup"><span data-stu-id="91d21-140">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a><span data-ttu-id="91d21-141">[付加列]</span><span class="sxs-lookup"><span data-stu-id="91d21-141">Included columns</span></span>

<span data-ttu-id="91d21-142">一部のリレーショナルデータベースでは、インデックスに含まれるが、"キー" の一部ではない列のセットを構成できます。</span><span class="sxs-lookup"><span data-stu-id="91d21-142">Some relational databases allow you to configure a set of columns which get included in the index, but aren't part of its "key".</span></span> <span data-ttu-id="91d21-143">これにより、テーブル自体にアクセスする必要がないため、クエリ内のすべての列がキー列または非キー列としてインデックスに含まれる場合、クエリのパフォーマンスが大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="91d21-143">This can significantly improve query performance when all columns in the query are included in the index either as key or nonkey columns, as the table itself doesn't need to be accessed.</span></span> <span data-ttu-id="91d21-144">付加列 SQL Server の詳細については、 [のドキュメントを参照してください](/sql/relational-databases/indexes/create-indexes-with-included-columns)。</span><span class="sxs-lookup"><span data-stu-id="91d21-144">For more information on SQL Server included columns, [see the documentation](/sql/relational-databases/indexes/create-indexes-with-included-columns).</span></span>

<span data-ttu-id="91d21-145">次の例では、 `Url` 列がインデックスキーの一部であるため、その列に対するクエリフィルター処理でインデックスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="91d21-145">In the following example, the `Url` column is part of the index key, so any query filtering on that column can use the index.</span></span> <span data-ttu-id="91d21-146">さらに、 `Title` 列と列にのみアクセス `PublishedOn` するクエリは、テーブルにアクセスする必要がなく、より効率的に実行されます。</span><span class="sxs-lookup"><span data-stu-id="91d21-146">But in addition, queries accessing only the `Title` and `PublishedOn` columns will not need to access the table and will run more efficiently:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
