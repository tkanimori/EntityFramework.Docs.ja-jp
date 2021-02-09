---
title: クエリ内の null 値との比較
description: Entity Framework Core でクエリ内の null 値の比較を処理する方法に関する情報
author: maumar
ms.date: 11/11/2020
uid: core/querying/null-comparisons
ms.openlocfilehash: fc63d0e0e6aea09e46b1700152312d4b74270219
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983353"
---
# <a name="query-null-semantics"></a><span data-ttu-id="5f85e-103">クエリの null セマンティクス</span><span class="sxs-lookup"><span data-stu-id="5f85e-103">Query null semantics</span></span>

## <a name="introduction"></a><span data-ttu-id="5f85e-104">はじめに</span><span class="sxs-lookup"><span data-stu-id="5f85e-104">Introduction</span></span>

<span data-ttu-id="5f85e-105">SQL データベースで比較が実行される場合、その動作は、C# のブール型ロジックではなく、3 値論理 (`true`、`false`、`null`) に基づいたものとなります。</span><span class="sxs-lookup"><span data-stu-id="5f85e-105">SQL databases operate on 3-valued logic (`true`, `false`, `null`) when performing comparisons, as opposed to the boolean logic of C#.</span></span> <span data-ttu-id="5f85e-106">LINQ クエリを SQL に変換する場合は、EF Core によって、クエリの一部の要素に対して追加の null チェックが導入されて、差異の補正が試みられます。</span><span class="sxs-lookup"><span data-stu-id="5f85e-106">When translating LINQ queries to SQL, EF Core tries to compensate for the difference by introducing additional null checks for some elements of the query.</span></span>
<span data-ttu-id="5f85e-107">これを説明するために、次のエンティティを定義しましょう。</span><span class="sxs-lookup"><span data-stu-id="5f85e-107">To illustrate this, let's define the following entity:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsEntity.cs#Entity)]

<span data-ttu-id="5f85e-108">さらに、いくつかのクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="5f85e-108">and issue several queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#BasicExamples)]

<span data-ttu-id="5f85e-109">最初の 2 つのクエリによって行われるのは、シンプルな比較です。</span><span class="sxs-lookup"><span data-stu-id="5f85e-109">The first two queries produce simple comparisons.</span></span> <span data-ttu-id="5f85e-110">最初のクエリでは、両方の列が null 非許容であるため、null チェックは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5f85e-110">In the first query, both columns are non-nullable so null checks are not needed.</span></span> <span data-ttu-id="5f85e-111">2 番目のクエリでは、`NullableInt` に `null` を含めることはできますが、`Id` は null 非許容です。`null` と null 値以外を比較すると、結果として `null` が生成されます。これは `WHERE` 操作によって除外されます。</span><span class="sxs-lookup"><span data-stu-id="5f85e-111">In the second query, `NullableInt` could contain `null`, but `Id` is non-nullable; comparing `null` to non-null yields `null` as a result, which would be filtered out by `WHERE` operation.</span></span> <span data-ttu-id="5f85e-112">そのため、どちらにも追加の条件は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5f85e-112">So no additional terms are needed either.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[Int]

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[NullableInt]
```

<span data-ttu-id="5f85e-113">3 番目のクエリには、null チェックが導入されています。</span><span class="sxs-lookup"><span data-stu-id="5f85e-113">The third query introduces a null check.</span></span> <span data-ttu-id="5f85e-114">`NullableInt` が `null` である場合、比較 `Id <> NullableInt` を行うと `null` が生成されます。これは `WHERE` 操作によって除外されます。</span><span class="sxs-lookup"><span data-stu-id="5f85e-114">When `NullableInt` is `null` the comparison `Id <> NullableInt` yields `null`, which would be filtered out by `WHERE` operation.</span></span> <span data-ttu-id="5f85e-115">ただし、ブール型のロジックの観点からは、この例は結果の一部として返される必要があります。</span><span class="sxs-lookup"><span data-stu-id="5f85e-115">However, from the boolean logic perspective this case should be returned as part of the result.</span></span> <span data-ttu-id="5f85e-116">したがって、それを確実なものにするために、EF Core によって必要なチェックが追加されます。</span><span class="sxs-lookup"><span data-stu-id="5f85e-116">Hence EF Core adds the necessary check to ensure that.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[Id] <> [e].[NullableInt]) OR [e].[NullableInt] IS NULL
```

<span data-ttu-id="5f85e-117">4 番目と 5 番目のクエリで示されているのは、両方の列が null 許容である場合のパターンです。</span><span class="sxs-lookup"><span data-stu-id="5f85e-117">Queries four and five show the pattern when both columns are nullable.</span></span> <span data-ttu-id="5f85e-118">注目すべき点は、`<>` 操作を実行すると、`==` 操作よりも複雑な (そして遅くなる可能性がある) クエリが生成されるということです。</span><span class="sxs-lookup"><span data-stu-id="5f85e-118">It's worth noting that the `<>` operation produces more complicated (and potentially slower) query than the `==` operation.</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] = [e].[String2]) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL)

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE (([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)
```

## <a name="treatment-of-nullable-values-in-functions"></a><span data-ttu-id="5f85e-119">関数での null 許容値の処理</span><span class="sxs-lookup"><span data-stu-id="5f85e-119">Treatment of nullable values in functions</span></span>

<span data-ttu-id="5f85e-120">引数の一部が `null` である場合、SQL の関数の多くで `null` の結果のみを返すことができます。</span><span class="sxs-lookup"><span data-stu-id="5f85e-120">Many functions in SQL can only return a `null` result if some of their arguments are `null`.</span></span> <span data-ttu-id="5f85e-121">EF Core ではこれを活用して、より効率的なクエリを生成します。</span><span class="sxs-lookup"><span data-stu-id="5f85e-121">EF Core takes advantage of this to produce more efficient queries.</span></span>
<span data-ttu-id="5f85e-122">次のクエリで最適化の例を示します。</span><span class="sxs-lookup"><span data-stu-id="5f85e-122">The query below illustrates the optimization:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#Functions)]

<span data-ttu-id="5f85e-123">生成される SQL は次のようになります (`SUBSTRING` 関数を評価する必要はありません。引数のいずれかが null 値である場合は null 値にしかならないからです)。</span><span class="sxs-lookup"><span data-stu-id="5f85e-123">The generated SQL is as follows (we don't need to evaluate the `SUBSTRING` function since it will be only null when either of the arguments to it is null.):</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[String1] IS NULL OR [e].[String2] IS NULL
```

<span data-ttu-id="5f85e-124">最適化はユーザー定義関数にも使用できます。</span><span class="sxs-lookup"><span data-stu-id="5f85e-124">The optimization can also be used for user-defined functions.</span></span> <span data-ttu-id="5f85e-125">詳細については、「[ユーザー定義関数のマッピング](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments)」ページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="5f85e-125">See [user defined function mapping](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments) page for more details.</span></span>

## <a name="writing-performant-queries"></a><span data-ttu-id="5f85e-126">パフォーマンスの高いクエリの作成</span><span class="sxs-lookup"><span data-stu-id="5f85e-126">Writing performant queries</span></span>

- <span data-ttu-id="5f85e-127">null 非許容列の比較は、Null 許容列の比較よりもシンプルで高速です。</span><span class="sxs-lookup"><span data-stu-id="5f85e-127">Comparing non-nullable columns is simpler and faster than comparing nullable columns.</span></span> <span data-ttu-id="5f85e-128">可能な限り、列を null 非許容としてマークすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="5f85e-128">Consider marking columns as non-nullable whenever possible.</span></span>

- <span data-ttu-id="5f85e-129">等価性 (`==`) のチェックは、非等価性 (`!=`) のチェックよりもシンプルで高速です。`null` と `false` の結果をクエリで区別する必要がないからです。</span><span class="sxs-lookup"><span data-stu-id="5f85e-129">Checking for equality (`==`) is simpler and faster than checking for non-equality (`!=`), because query doesn't need to distinguish between `null` and `false` result.</span></span> <span data-ttu-id="5f85e-130">可能な場合は、等価比較を使用してください。</span><span class="sxs-lookup"><span data-stu-id="5f85e-130">Use equality comparison whenever possible.</span></span> <span data-ttu-id="5f85e-131">ただし、単に `==` 比較を否定することは事実上 `!=` と同じであるため、パフォーマンスの向上にはつながりません。</span><span class="sxs-lookup"><span data-stu-id="5f85e-131">However, simply negating `==` comparison is effectively the same as `!=`, so it doesn't result in performance improvement.</span></span>

- <span data-ttu-id="5f85e-132">場合によっては、列から `null` 値を明示的に除外することによって複雑な比較を簡略化することができます。たとえば、`null` 値が存在しない場合や、これらの値が結果に関係しない場合などです。</span><span class="sxs-lookup"><span data-stu-id="5f85e-132">In some cases, it is possible to simplify a complex comparison by filtering out `null` values from a column explicitly - for example when no `null` values are present or these values are not relevant in the result.</span></span> <span data-ttu-id="5f85e-133">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="5f85e-133">Consider the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#ManualOptimization)]

<span data-ttu-id="5f85e-134">これらのクエリにより、次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="5f85e-134">These queries produce the following SQL:</span></span>

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ((([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)) OR ((CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL))

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] IS NOT NULL AND [e].[String2] IS NOT NULL) AND (([e].[String1] <> [e].[String2]) OR (CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)))
```

<span data-ttu-id="5f85e-135">2 番目のクエリでは、`null` の結果が `String1` 列から明示的に除外されています。</span><span class="sxs-lookup"><span data-stu-id="5f85e-135">In the second query, `null` results are filtered out from `String1` column explicitly.</span></span> <span data-ttu-id="5f85e-136">EF Core により、比較中は `String1` 列が null 非許容として確実に処理されるので、クエリが簡略化されます。</span><span class="sxs-lookup"><span data-stu-id="5f85e-136">EF Core can safely treat the `String1` column as non-nullable during comparison, resulting in a simpler query.</span></span>

## <a name="using-relational-null-semantics"></a><span data-ttu-id="5f85e-137">リレーショナル null セマンティクスの使用</span><span class="sxs-lookup"><span data-stu-id="5f85e-137">Using relational null semantics</span></span>

<span data-ttu-id="5f85e-138">null 値の比較補正を無効にして、リレーショナル null セマンティクスを直接使用することができます。</span><span class="sxs-lookup"><span data-stu-id="5f85e-138">It's possible to disable the null comparison compensation and use relational null semantics directly.</span></span> <span data-ttu-id="5f85e-139">これを行うには、`OnConfiguring` メソッド内のオプション ビルダー上で `UseRelationalNulls(true)` メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="5f85e-139">This can be done by calling `UseRelationalNulls(true)` method on the options builder inside `OnConfiguring` method:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsContext.cs#UseRelationalNulls)]

> [!WARNING]
> <span data-ttu-id="5f85e-140">リレーショナル null セマンティクスを使用すると、LINQ クエリは C# の場合と同じ意味を持たなくなり、予想とは異なる結果が生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="5f85e-140">When using relational null semantics, your LINQ queries no longer have the same meaning as they do in C#, and may yield different results than expected.</span></span> <span data-ttu-id="5f85e-141">このモードを使用する際は注意が必要です。</span><span class="sxs-lookup"><span data-stu-id="5f85e-141">Exercise caution when using this mode.</span></span>
