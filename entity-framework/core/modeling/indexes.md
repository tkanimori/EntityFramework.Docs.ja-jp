---
title: インデックス-EF Core
author: roji
ms.date: 12/16/2019
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
uid: core/modeling/indexes
ms.openlocfilehash: 810fccc0c6b035f515107601b245811f7b4118a6
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413937"
---
# <a name="indexes"></a><span data-ttu-id="611ee-102">インデックス</span><span class="sxs-lookup"><span data-stu-id="611ee-102">Indexes</span></span>

<span data-ttu-id="611ee-103">インデックスは、多くのデータストアで共通の概念です。</span><span class="sxs-lookup"><span data-stu-id="611ee-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="611ee-104">データストアでの実装は異なる場合がありますが、列または列のセットに基づいて参照を作成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="611ee-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

<span data-ttu-id="611ee-105">データ注釈を使用してインデックスを作成することはできません。</span><span class="sxs-lookup"><span data-stu-id="611ee-105">Indexes cannot be created using data annotations.</span></span> <span data-ttu-id="611ee-106">Fluent API を使用して、次のように1つの列にインデックスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="611ee-106">You can use the Fluent API to specify an index on a single column as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Index.cs?name=Index&highlight=4)]

<span data-ttu-id="611ee-107">複数の列に対してインデックスを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="611ee-107">You can also specify an index over more than one column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexComposite.cs?name=Composite&highlight=4)]

> [!NOTE]
> <span data-ttu-id="611ee-108">規則により、外部キーとして使用される各プロパティ (またはプロパティのセット) にインデックスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="611ee-108">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>
>
> <span data-ttu-id="611ee-109">EF Core では、個別のプロパティセットごとに1つのインデックスのみがサポートされます。</span><span class="sxs-lookup"><span data-stu-id="611ee-109">EF Core only supports one index per distinct set of properties.</span></span> <span data-ttu-id="611ee-110">Fluent API を使用して、既にインデックスが定義されている一連のプロパティ (規則または以前の構成) のインデックスを構成する場合は、そのインデックスの定義を変更します。</span><span class="sxs-lookup"><span data-stu-id="611ee-110">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="611ee-111">これは、規則によって作成されたインデックスをさらに構成する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="611ee-111">This is useful if you want to further configure an index that was created by convention.</span></span>

## <a name="index-uniqueness"></a><span data-ttu-id="611ee-112">インデックスの一意性</span><span class="sxs-lookup"><span data-stu-id="611ee-112">Index uniqueness</span></span>

<span data-ttu-id="611ee-113">既定では、インデックスは一意ではありません。複数の行がインデックスの列セットに対して同じ値を持つことができます。</span><span class="sxs-lookup"><span data-stu-id="611ee-113">By default, indexes aren't unique: multiple rows are allowed to have the same value(s) for the index's column set.</span></span> <span data-ttu-id="611ee-114">次のように、インデックスを一意にすることができます。</span><span class="sxs-lookup"><span data-stu-id="611ee-114">You can make an index unique as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexUnique.cs?name=IndexUnique&highlight=5)]

<span data-ttu-id="611ee-115">インデックスの列セットに同じ値を持つ複数のエンティティを挿入しようとすると、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="611ee-115">Attempting to insert more than one entity with the same values for the index's column set will cause an exception to be thrown.</span></span>

## <a name="index-name"></a><span data-ttu-id="611ee-116">インデックス名</span><span class="sxs-lookup"><span data-stu-id="611ee-116">Index name</span></span>

<span data-ttu-id="611ee-117">規則により、リレーショナルデータベースに作成されたインデックスの名前は `IX_<type name>_<property name>`になります。</span><span class="sxs-lookup"><span data-stu-id="611ee-117">By convention, indexes created in a relational database are named `IX_<type name>_<property name>`.</span></span> <span data-ttu-id="611ee-118">複合インデックスの場合、`<property name>` は、アンダースコアで区切られたプロパティ名のリストになります。</span><span class="sxs-lookup"><span data-stu-id="611ee-118">For composite indexes, `<property name>` becomes an underscore separated list of property names.</span></span>

<span data-ttu-id="611ee-119">Fluent API を使用して、データベースに作成されたインデックスの名前を設定できます。</span><span class="sxs-lookup"><span data-stu-id="611ee-119">You can use the Fluent API to set the name of the index created in the database:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexName.cs?name=IndexName&highlight=5)]

## <a name="index-filter"></a><span data-ttu-id="611ee-120">インデックスフィルター</span><span class="sxs-lookup"><span data-stu-id="611ee-120">Index filter</span></span>

<span data-ttu-id="611ee-121">一部のリレーショナルデータベースでは、フィルター処理されたインデックスまたは部分的なインデックスを指定できます。</span><span class="sxs-lookup"><span data-stu-id="611ee-121">Some relational databases allow you to specify a filtered or partial index.</span></span> <span data-ttu-id="611ee-122">これにより、列の値のサブセットにのみインデックスを作成し、インデックスのサイズを小さくして、パフォーマンスとディスク領域の使用率を向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="611ee-122">This allows you to index only a subset of a column's values, reducing the index's size and improving both performance and disk space usage.</span></span> <span data-ttu-id="611ee-123">フィルター選択されたインデックス SQL Server の詳細については、[のドキュメントを参照してください](https://docs.microsoft.com/sql/relational-databases/indexes/create-filtered-indexes)。</span><span class="sxs-lookup"><span data-stu-id="611ee-123">For more information on SQL Server filtered indexes, [see the documentation](https://docs.microsoft.com/sql/relational-databases/indexes/create-filtered-indexes).</span></span>

<span data-ttu-id="611ee-124">Fluent API を使用して、SQL 式として指定されたインデックスにフィルターを指定できます。</span><span class="sxs-lookup"><span data-stu-id="611ee-124">You can use the Fluent API to specify a filter on an index, provided as a SQL expression:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexFilter.cs?name=IndexFilter&highlight=5)]

<span data-ttu-id="611ee-125">SQL Server プロバイダー EF を使用すると、一意のインデックスの一部である null 許容型のすべての列に対して `'IS NOT NULL'` フィルターが追加されます。</span><span class="sxs-lookup"><span data-stu-id="611ee-125">When using the SQL Server provider EF adds an `'IS NOT NULL'` filter for all nullable columns that are part of a unique index.</span></span> <span data-ttu-id="611ee-126">この規則をオーバーライドするには、`null` 値を指定します。</span><span class="sxs-lookup"><span data-stu-id="611ee-126">To override this convention you can supply a `null` value.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexNoFilter.cs?name=IndexNoFilter&highlight=6)]

## <a name="included-columns"></a><span data-ttu-id="611ee-127">[付加列]</span><span class="sxs-lookup"><span data-stu-id="611ee-127">Included columns</span></span>

<span data-ttu-id="611ee-128">一部のリレーショナルデータベースでは、インデックスに含まれるが、"キー" の一部ではない列のセットを構成できます。</span><span class="sxs-lookup"><span data-stu-id="611ee-128">Some relational databases allow you to configure a set of columns which get included in the index, but aren't part of its "key".</span></span> <span data-ttu-id="611ee-129">これにより、テーブル自体にアクセスする必要がないため、クエリ内のすべての列がキー列または非キー列としてインデックスに含まれる場合、クエリのパフォーマンスが大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="611ee-129">This can significantly improve query performance when all columns in the query are included in the index either as key or nonkey columns, as the table itself doesn't need to be accessed.</span></span> <span data-ttu-id="611ee-130">付加列 SQL Server の詳細については、[のドキュメントを参照してください](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns)。</span><span class="sxs-lookup"><span data-stu-id="611ee-130">For more information on SQL Server included columns, [see the documentation](https://docs.microsoft.com/sql/relational-databases/indexes/create-indexes-with-included-columns).</span></span>

<span data-ttu-id="611ee-131">次の例では、`Url` 列がインデックスキーの一部であるため、その列に対するクエリフィルター処理でインデックスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="611ee-131">In the following example, the `Url` column is part of the index key, so any query filtering on that column can use the index.</span></span> <span data-ttu-id="611ee-132">さらに、`Title` および `PublishedOn` 列のみにアクセスするクエリは、テーブルにアクセスする必要がなく、より効率的に実行されます。</span><span class="sxs-lookup"><span data-stu-id="611ee-132">But in addition, queries accessing only the `Title` and `PublishedOn` columns will not need to access the table and will run more efficiently:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexInclude.cs?name=IndexInclude&highlight=5-9)]
