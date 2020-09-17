---
title: 複雑なクエリ演算子 - EF Core
description: Entity Framework Core を使用する場合の複雑な LINQ クエリ演算子に関する詳細情報
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/complex-query-operators
ms.openlocfilehash: 57157fa1593c9e5fe54e5fbe6b2c58eca3d4b0e7
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071161"
---
# <a name="complex-query-operators"></a><span data-ttu-id="0cb3b-103">複雑なクエリ演算子</span><span class="sxs-lookup"><span data-stu-id="0cb3b-103">Complex Query Operators</span></span>

<span data-ttu-id="0cb3b-104">統合言語クエリ (LINQ) には、複数のデータ ソースを結合したり、複雑な処理を行ったりする、多数の複雑な演算子が含まれています。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-104">Language Integrated Query (LINQ) contains many complex operators, which combine multiple data sources or does complex processing.</span></span> <span data-ttu-id="0cb3b-105">すべての LINQ 演算子が、サーバー側で適切に変換されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-105">Not all LINQ operators have suitable translations on the server side.</span></span> <span data-ttu-id="0cb3b-106">あるフォームのクエリがサーバーに変換されることがありますが、結果が同じであっても、別のフォームで記述されている場合は変換されません。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-106">Sometimes, a query in one form translates to the server but if written in a different form doesn't translate even if the result is the same.</span></span> <span data-ttu-id="0cb3b-107">このページでは、複雑な演算子とそのサポートされるバリエーションについていくつか説明します。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-107">This page describes some of the complex operators and their supported variations.</span></span> <span data-ttu-id="0cb3b-108">今後のリリースで、さらに多くのパターンを認識し、それらに対応する変換を追加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-108">In future releases, we may recognize more patterns and add their corresponding translations.</span></span> <span data-ttu-id="0cb3b-109">また、変換のサポートはプロバイダーによって異なることに注意することが重要です。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-109">It's also important to keep in mind that translation support varies between providers.</span></span> <span data-ttu-id="0cb3b-110">SqlServer で変換される特定のクエリは、SQLite データベースでは機能しない場合があります。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-110">A particular query, which is translated in SqlServer, may not work for SQLite databases.</span></span>

> [!TIP]
> <span data-ttu-id="0cb3b-111">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-111">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="join"></a><span data-ttu-id="0cb3b-112">Join</span><span class="sxs-lookup"><span data-stu-id="0cb3b-112">Join</span></span>

<span data-ttu-id="0cb3b-113">LINQ Join 演算子を使用すると、各ソースのキー セレクターに基づいて 2 つのデータ ソースを接続でき、キーが一致したときに値のタプルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-113">The LINQ Join operator allows you to connect two data sources based on the key selector for each source, generating a tuple of values when the key matches.</span></span> <span data-ttu-id="0cb3b-114">リレーショナル データベースでは、必然的に `INNER JOIN` に変換されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-114">It naturally translates to `INNER JOIN` on relational databases.</span></span> <span data-ttu-id="0cb3b-115">LINQ Join には外部および内部のキー セレクターがありますが、データベースには単一の結合条件が必要です。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-115">While the LINQ Join has outer and inner key selectors, the database requires a single join condition.</span></span> <span data-ttu-id="0cb3b-116">したがって、EF Core では、外部キー セレクターと内部キー セレクターが等価であるかどうかを比較して、結合条件を生成します。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-116">So EF Core generates a join condition by comparing the outer key selector to the inner key selector for equality.</span></span> <span data-ttu-id="0cb3b-117">さらに、キー セレクターが匿名型である場合、EF Core では、等価要素ごとに比較する結合条件を生成します。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-117">Further, if the key selectors are anonymous types, EF Core generates a join condition to compare equality component wise.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#Join)]

```SQL
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a><span data-ttu-id="0cb3b-118">GroupJoin</span><span class="sxs-lookup"><span data-stu-id="0cb3b-118">GroupJoin</span></span>

<span data-ttu-id="0cb3b-119">LINQ GroupJoin 演算子では、Join の場合と同じように 2 つのデータ ソースを接続できますが、外部要素と一致するように内部値のグループが作成されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-119">The LINQ GroupJoin operator allows you to connect two data sources similar to Join, but it creates a group of inner values for matching outer elements.</span></span> <span data-ttu-id="0cb3b-120">次の例のようなクエリを実行すると、`Blog` & `IEnumerable<Post>` の結果が生成されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-120">Executing a query like the following example generates a result of `Blog` & `IEnumerable<Post>`.</span></span> <span data-ttu-id="0cb3b-121">データベース (特にリレーショナル データベース) にはクライアント側のオブジェクトのコレクションを表す方法がないため、多くの場合、GroupJoin はサーバーに変換されません。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-121">Since databases (especially relational databases) don't have a way to represent a collection of client-side objects, GroupJoin doesn't translate to the server in many cases.</span></span> <span data-ttu-id="0cb3b-122">サーバーからすべてのデータを取得し、特別なセレクターを使用せずに GroupJoin を実行する必要があります (以下の最初のクエリ)。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-122">It requires you to get all of the data from the server to do GroupJoin without a special selector (first query below).</span></span> <span data-ttu-id="0cb3b-123">しかし、セレクターでデータの選択が制限されている場合は、サーバーからすべてのデータをフェッチすると、パフォーマンスの問題が発生する可能性があります (以下の 2 番目のクエリ)。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-123">But if the selector is limiting data being selected then fetching all of the data from the server may cause performance issues (second query below).</span></span> <span data-ttu-id="0cb3b-124">そのため、EF Core では GroupJoin が変換されません。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-124">That's why EF Core doesn't translate GroupJoin.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoinComposed)]

## <a name="selectmany"></a><span data-ttu-id="0cb3b-125">SelectMany</span><span class="sxs-lookup"><span data-stu-id="0cb3b-125">SelectMany</span></span>

<span data-ttu-id="0cb3b-126">LINQ SelectMany 演算子を使用すると、外部の各要素のコレクション セレクターを列挙し、各データ ソースから値のタプルを生成することができます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-126">The LINQ SelectMany operator allows you to enumerate over a collection selector for each outer element and generate tuples of values from each data source.</span></span> <span data-ttu-id="0cb3b-127">ある意味、これは結合ですが、どの条件も含まれていないため、すべての外部要素がコレクション ソースからの要素に接続されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-127">In a way, it's a join but without any condition so every outer element is connected with an element from the collection source.</span></span> <span data-ttu-id="0cb3b-128">コレクション セレクターが外部データ ソースにどのように関連しているかによって、SelectMany はサーバー側でさまざまなクエリに変換することができます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-128">Depending on how the collection selector is related to the outer data source, SelectMany can translate into various different queries on the server side.</span></span>

### <a name="collection-selector-doesnt-reference-outer"></a><span data-ttu-id="0cb3b-129">コレクション セレクターで外部参照されない</span><span class="sxs-lookup"><span data-stu-id="0cb3b-129">Collection selector doesn't reference outer</span></span>

<span data-ttu-id="0cb3b-130">コレクション セレクターで外部ソースから何も参照されていない場合、結果は両方のデータ ソースのデカルト積になります。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-130">When the collection selector isn't referencing anything from the outer source, the result is a cartesian product of both data sources.</span></span> <span data-ttu-id="0cb3b-131">リレーショナル データベースでは `CROSS JOIN` に変換されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-131">It translates to `CROSS JOIN` in relational databases.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToCrossJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a><span data-ttu-id="0cb3b-132">where 句でのコレクション セレクターによる外部参照</span><span class="sxs-lookup"><span data-stu-id="0cb3b-132">Collection selector references outer in a where clause</span></span>

<span data-ttu-id="0cb3b-133">コレクション セレクターに where 句があり、外部要素が参照される場合、EF Core ではそれがデータベース結合に変換され、述語が結合条件として使用されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-133">When the collection selector has a where clause, which references the outer element, then EF Core translates it to a database join and uses the predicate as the join condition.</span></span> <span data-ttu-id="0cb3b-134">通常、このケースは、外部要素のコレクション ナビゲーションをコレクション セレクターとして使用する場合に発生します。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-134">Normally this case arises when using collection navigation on the outer element as the collection selector.</span></span> <span data-ttu-id="0cb3b-135">外部要素のコレクションが空の場合、その外部要素の結果は生成されません。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-135">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="0cb3b-136">しかし、コレクション セレクターに `DefaultIfEmpty` が適用されている場合、外部要素は内部要素の既定値と接続されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-136">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="0cb3b-137">このような違いから、この種のクエリは、`DefaultIfEmpty` と `LEFT JOIN` がない場合、および `DefaultIfEmpty` が適用されている場合に `INNER JOIN` に変換されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-137">Because of this distinction, this kind of queries translates to `INNER JOIN` in the absence of `DefaultIfEmpty` and `LEFT JOIN` when `DefaultIfEmpty` is applied.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a><span data-ttu-id="0cb3b-138">where 以外のケースでのコレクション セレクターによる外部参照</span><span class="sxs-lookup"><span data-stu-id="0cb3b-138">Collection selector references outer in a non-where case</span></span>

<span data-ttu-id="0cb3b-139">コレクション セレクターで、(上記のケースのように) where 句には含まれていない外部要素を参照する場合、データベース結合には変換されません。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-139">When the collection selector references the outer element, which isn't in a where clause (as the case above), it doesn't translate to a database join.</span></span> <span data-ttu-id="0cb3b-140">そのため、外部の各要素のコレクション セレクターを評価する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-140">That's why we need to evaluate the collection selector for each outer element.</span></span> <span data-ttu-id="0cb3b-141">多くのリレーショナル データベースでは `APPLY` 演算に変換されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-141">It translates to `APPLY` operations in many relational databases.</span></span> <span data-ttu-id="0cb3b-142">外部要素のコレクションが空の場合、その外部要素の結果は生成されません。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-142">If the collection is empty for an outer element, then no results would be generated for that outer element.</span></span> <span data-ttu-id="0cb3b-143">しかし、コレクション セレクターに `DefaultIfEmpty` が適用されている場合、外部要素は内部要素の既定値と接続されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-143">But if `DefaultIfEmpty` is applied on the collection selector then the outer element will be connected with a default value of the inner element.</span></span> <span data-ttu-id="0cb3b-144">このような違いから、この種のクエリは、`DefaultIfEmpty` と `OUTER APPLY` がない場合、および `DefaultIfEmpty` が適用されている場合に `CROSS APPLY` に変換されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-144">Because of this distinction, this kind of queries translates to `CROSS APPLY` in the absence of `DefaultIfEmpty` and `OUTER APPLY` when `DefaultIfEmpty` is applied.</span></span> <span data-ttu-id="0cb3b-145">SQLite のような特定のデータベースでは `APPLY` 演算子がサポートされないため、この種のクエリが変換されない場合があります。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-145">Certain databases like SQLite don't support `APPLY` operators so this kind of query may not be translated.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToApply)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a><span data-ttu-id="0cb3b-146">GroupBy</span><span class="sxs-lookup"><span data-stu-id="0cb3b-146">GroupBy</span></span>

<span data-ttu-id="0cb3b-147">LINQ GroupBy 演算子では、`IGrouping<TKey, TElement>` 型の結果が作成されす。この場合、`TKey` と `TElement` は任意の型にすることができます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-147">LINQ GroupBy operators create a result of type `IGrouping<TKey, TElement>` where `TKey` and `TElement` could be any arbitrary type.</span></span> <span data-ttu-id="0cb3b-148">さらに、`IGrouping` では `IEnumerable<TElement>` が実装されます。これは、グループ化の後に LINQ 演算子を使用して、それを構成できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-148">Furthermore, `IGrouping` implements `IEnumerable<TElement>`, which means you can compose over it using any LINQ operator after the grouping.</span></span> <span data-ttu-id="0cb3b-149">データベース構造では `IGrouping` を表すことができないため、ほとんどの場合、GroupBy 演算子は変換されません。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-149">Since no database structure can represent an `IGrouping`, GroupBy operators have no translation in most cases.</span></span> <span data-ttu-id="0cb3b-150">各グループに対して、スカラーを返す集約演算子が適用されている場合、リレーショナル データベースでは SQL `GROUP BY` に変換できます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-150">When an aggregate operator is applied to each group, which returns a scalar, it can be translated to SQL `GROUP BY` in relational databases.</span></span> <span data-ttu-id="0cb3b-151">SQL `GROUP BY` も制限されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-151">The SQL `GROUP BY` is restrictive too.</span></span> <span data-ttu-id="0cb3b-152">スカラー値でのみグループ化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-152">It requires you to group only by scalar values.</span></span> <span data-ttu-id="0cb3b-153">プロジェクションには、列に適用される集約またはグループ化キー列のみを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-153">The projection can only contain grouping key columns or any aggregate applied over a column.</span></span> <span data-ttu-id="0cb3b-154">EF Core では、次の例に示すように、このパターンを識別してサーバーに変換します。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-154">EF Core identifies this pattern and translates it to the server, as in the following example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupBy)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

<span data-ttu-id="0cb3b-155">また、EF Core では、Where または OrderBy (あるいはその他の順序付け) LINQ 演算子で、グループ化の集約演算子が表示されるクエリを変換します。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-155">EF Core also translates queries where an aggregate operator on the grouping appears in a Where or OrderBy (or other ordering) LINQ operator.</span></span> <span data-ttu-id="0cb3b-156">where 句には、SQL の `HAVING` 句が使用されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-156">It uses `HAVING` clause in SQL for the where clause.</span></span> <span data-ttu-id="0cb3b-157">GroupBy 演算子を適用する前のクエリの部分は、サーバーに変換できる限り、複雑なクエリにすることができます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-157">The part of the query before applying the GroupBy operator can be any complex query as long as it can be translated to server.</span></span> <span data-ttu-id="0cb3b-158">さらに、グループ化クエリに集約演算子を適用して、結果のソースからグループ化を削除したら、他のクエリと同様に、それに基づいて構成することができます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-158">Furthermore, once you apply aggregate operators on a grouping query to remove groupings from the resulting source, you can compose on top of it like any other query.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupByFilter)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

<span data-ttu-id="0cb3b-159">EF Core でサポートされる集約演算子は次のとおりです</span><span class="sxs-lookup"><span data-stu-id="0cb3b-159">The aggregate operators EF Core supports are as follows</span></span>

- <span data-ttu-id="0cb3b-160">Average</span><span class="sxs-lookup"><span data-stu-id="0cb3b-160">Average</span></span>
- <span data-ttu-id="0cb3b-161">Count</span><span class="sxs-lookup"><span data-stu-id="0cb3b-161">Count</span></span>
- <span data-ttu-id="0cb3b-162">LongCount</span><span class="sxs-lookup"><span data-stu-id="0cb3b-162">LongCount</span></span>
- <span data-ttu-id="0cb3b-163">Max</span><span class="sxs-lookup"><span data-stu-id="0cb3b-163">Max</span></span>
- <span data-ttu-id="0cb3b-164">Min</span><span class="sxs-lookup"><span data-stu-id="0cb3b-164">Min</span></span>
- <span data-ttu-id="0cb3b-165">SUM</span><span class="sxs-lookup"><span data-stu-id="0cb3b-165">Sum</span></span>

## <a name="left-join"></a><span data-ttu-id="0cb3b-166">Left Join</span><span class="sxs-lookup"><span data-stu-id="0cb3b-166">Left Join</span></span>

<span data-ttu-id="0cb3b-167">Left Join は LINQ 演算子ではありませんが、リレーショナル データベースには、クエリで頻繁に使用される Left Join の概念があります。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-167">While Left Join isn't a LINQ operator, relational databases have the concept of a Left Join which is frequently used in queries.</span></span> <span data-ttu-id="0cb3b-168">LINQ クエリの特定のパターンでは、サーバーで `LEFT JOIN` と同じ結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-168">A particular pattern in LINQ queries gives the same result as a `LEFT JOIN` on the server.</span></span> <span data-ttu-id="0cb3b-169">EF Core ではこのようなパターンを識別し、サーバー側で同等の `LEFT JOIN` を生成します。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-169">EF Core identifies such patterns and generates the equivalent `LEFT JOIN` on the server side.</span></span> <span data-ttu-id="0cb3b-170">このパターンでは、両方のデータソース間に GroupJoin を作成し、その後、内部に関連する要素がない場合は null と一致するように、グループ化ソースに対して DefaultIfEmpty を指定した SelectMany 演算子を使用して、グループ化をフラット化します。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-170">The pattern involves creating a GroupJoin between both the data sources and then flattening out the grouping by using the SelectMany operator with DefaultIfEmpty on the grouping source to match null when the inner doesn't have a related element.</span></span> <span data-ttu-id="0cb3b-171">次の例では、パターンがどのように見えるかと、何が生成されるかを示します。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-171">The following example shows what that pattern looks like and what it generates.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#LeftJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

<span data-ttu-id="0cb3b-172">上のパターンでは、式ツリーに複雑な構造体が作成されます。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-172">The above pattern creates a complex structure in the expression tree.</span></span> <span data-ttu-id="0cb3b-173">そのため、EF Core では、演算子の直後のステップで GroupJoin 演算子のグループ化結果をフラット化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-173">Because of that, EF Core requires you to flatten out the grouping results of the GroupJoin operator in a step immediately following the operator.</span></span> <span data-ttu-id="0cb3b-174">GroupJoin-DefaultIfEmpty-SelectMany が使用されていても、パターンが異なる場合は、Left Join として識別されない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="0cb3b-174">Even if the GroupJoin-DefaultIfEmpty-SelectMany is used but in a different pattern, we may not identify it as a Left Join.</span></span>
