---
title: 生 SQL クエリ - EF Core
description: Entity Framework Core のクエリに対する生 SQL の使用
author: smitpatel
ms.date: 10/08/2019
uid: core/querying/raw-sql
ms.openlocfilehash: 9c480d13c46c7c84554996bcb581627a1df318dd
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062595"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="f3494-103">生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="f3494-103">Raw SQL Queries</span></span>

<span data-ttu-id="f3494-104">Entity Framework Core を使用すると、リレーショナル データベースを操作するときに生 SQL クエリにドロップ ダウンすることができます。</span><span class="sxs-lookup"><span data-stu-id="f3494-104">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="f3494-105">生 SQL クエリは、必要なクエリが LINQ を使用して表現できない場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="f3494-105">Raw SQL queries are useful if the query you want can't be expressed using LINQ.</span></span> <span data-ttu-id="f3494-106">また、生 SQL クエリは、LINQ クエリを使うと、効率の悪い SQL クエリになる場合にも使用されます。</span><span class="sxs-lookup"><span data-stu-id="f3494-106">Raw SQL queries are also used if using a LINQ query is resulting in an inefficient SQL query.</span></span> <span data-ttu-id="f3494-107">生 SQL クエリは、通常のエンティティ型か、モデルの一部である[キーレス エンティティ型](xref:core/modeling/keyless-entity-types)を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="f3494-107">Raw SQL queries can return regular entity types or [keyless entity types](xref:core/modeling/keyless-entity-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="f3494-108">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RawSQL)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="f3494-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RawSQL) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="f3494-109">基本的な生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="f3494-109">Basic raw SQL queries</span></span>

<span data-ttu-id="f3494-110">`FromSqlRaw` 拡張メソッドを使用して、生 SQL クエリに基づいた LINQ クエリを開始できます。</span><span class="sxs-lookup"><span data-stu-id="f3494-110">You can use the `FromSqlRaw` extension method to begin a LINQ query based on a raw SQL query.</span></span> <span data-ttu-id="f3494-111">`FromSqlRaw` は、`DbSet<>` に直接配置されている、クエリ ルートでのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="f3494-111">`FromSqlRaw` can only be used on query roots, that is directly on the `DbSet<>`.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRaw)]

<span data-ttu-id="f3494-112">生 SQL クエリを使用してストアド プロシージャを実行できます。</span><span class="sxs-lookup"><span data-stu-id="f3494-112">Raw SQL queries can be used to execute a stored procedure.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a><span data-ttu-id="f3494-113">パラメーターを渡す</span><span class="sxs-lookup"><span data-stu-id="f3494-113">Passing parameters</span></span>

> [!WARNING]
> <span data-ttu-id="f3494-114">**生 SQL クエリには常にパラメーター化を使用する**</span><span class="sxs-lookup"><span data-stu-id="f3494-114">**Always use parameterization for raw SQL queries**</span></span>
>
> <span data-ttu-id="f3494-115">生 SQL クエリにユーザー指定の値を採用するときは、SQL インジェクション攻撃を防ぐための注意を払う必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3494-115">When introducing any user-provided values into a raw SQL query, care must be taken to avoid SQL injection attacks.</span></span> <span data-ttu-id="f3494-116">そのような値に無効な文字が含まれていないことを検証するだけでなく、SQL テキストとは別に値を送信するパラメーター化を常に使用してください。</span><span class="sxs-lookup"><span data-stu-id="f3494-116">In addition to validating that such values don't contain invalid characters, always use parameterization which sends the values separate from the SQL text.</span></span>
>
> <span data-ttu-id="f3494-117">特に、検証されていないユーザー指定の値を含む連結された文字列や補間された文字列 (`$""`) を、`FromSqlRaw` や `ExecuteSqlRaw` に渡さないでください。</span><span class="sxs-lookup"><span data-stu-id="f3494-117">In particular, never pass a concatenated or interpolated string (`$""`) with non-validated user-provided values into `FromSqlRaw` or `ExecuteSqlRaw`.</span></span> <span data-ttu-id="f3494-118">`FromSqlInterpolated` と `ExecuteSqlInterpolated` のメソッドでは、SQL インジェクション攻撃から保護されるように文字列補間構文を使用できます。</span><span class="sxs-lookup"><span data-stu-id="f3494-118">The `FromSqlInterpolated` and `ExecuteSqlInterpolated` methods allow using string interpolation syntax in a way that protects against SQL injection attacks.</span></span>

<span data-ttu-id="f3494-119">次の例では、パラメーターのプレースホルダーを SQL クエリ文字列に含め、追加の引数を指定することによって、ストアド プロシージャに 1 つのパラメーターを渡しています。</span><span class="sxs-lookup"><span data-stu-id="f3494-119">The following example passes a single parameter to a stored procedure by including a parameter placeholder in the SQL query string and providing an additional argument.</span></span> <span data-ttu-id="f3494-120">この構文は `String.Format` 構文のように見えるかもしれませんが、提供された値は `DbParameter` にラップされ、生成されたパラメーター名は、`{0}` プレースホルダーが指定された場所に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="f3494-120">While this syntax may look like `String.Format` syntax, the supplied value is wrapped in a `DbParameter` and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedureParameter)]

<span data-ttu-id="f3494-121">`FromSqlInterpolated` は `FromSqlRaw` に似ていますが、文字列補間構文を使用できます。</span><span class="sxs-lookup"><span data-stu-id="f3494-121">`FromSqlInterpolated` is similar to `FromSqlRaw` but allows you to use string interpolation syntax.</span></span> <span data-ttu-id="f3494-122">`FromSqlRaw` と同じように、`FromSqlInterpolated` はクエリ ルートでのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="f3494-122">Just like `FromSqlRaw`, `FromSqlInterpolated` can only be used on query roots.</span></span> <span data-ttu-id="f3494-123">前の例と同様に、値は `DbParameter` に変換され、SQL インジェクションに対して脆弱ではありません。</span><span class="sxs-lookup"><span data-stu-id="f3494-123">As with the previous example, the value is converted to a `DbParameter` and isn't vulnerable to SQL injection.</span></span>

> [!NOTE]
> <span data-ttu-id="f3494-124">バージョン 3.0 より前では、`FromSqlRaw` と `FromSqlInterpolated` は、`FromSql` という名前の 2 つのオーバーロードでした。</span><span class="sxs-lookup"><span data-stu-id="f3494-124">Prior to version 3.0, `FromSqlRaw` and `FromSqlInterpolated` were two overloads named `FromSql`.</span></span> <span data-ttu-id="f3494-125">詳細については、[以前のバージョンに関するセクション](#previous-versions)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="f3494-125">For more information, see the [previous versions section](#previous-versions).</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedStoredProcedureParameter)]

<span data-ttu-id="f3494-126">また、DbParameter を構築し、それをパラメーター値として指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="f3494-126">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="f3494-127">文字列のプレースホルダーではなく、通常の SQL パラメーターのプレースホルダーが使用されるため、`FromSqlRaw` を安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="f3494-127">Since a regular SQL parameter placeholder is used, rather than a string placeholder, `FromSqlRaw` can be safely used:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedureSqlParameter)]

<span data-ttu-id="f3494-128">`FromSqlRaw` では、SQL クエリ文字列で名前付きパラメーターを使用できます。これはストアド プロシージャに省略可能なパラメーターがある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="f3494-128">`FromSqlRaw` allows you to use named parameters in the SQL query string, which is useful when a stored procedure has optional parameters:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

> [!NOTE]
> <span data-ttu-id="f3494-129">**パラメーターの順序付け** `SqlParameter[]` 配列の順序に基づいて、Entity Framework Core によってパラメーターが渡されます。</span><span class="sxs-lookup"><span data-stu-id="f3494-129">**Parameter Ordering** Entity Framework Core passes parameters based on the order of the `SqlParameter[]` array.</span></span> <span data-ttu-id="f3494-130">複数の `SqlParameter` を渡す場合の SQL 文字列内の順序付けは、ストアド プロシージャの定義内のパラメーターの順序と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3494-130">When passing multiple `SqlParameter`s, the ordering in the SQL string must match the order of the parameters in the stored procedure's definition.</span></span> <span data-ttu-id="f3494-131">この手順を行わないと、そのプロシージャが実行されるときに、型変換の例外や予期しない動作が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f3494-131">Failure to do this may result in type conversion exceptions and/or unexpected behavior when the procedure is executed.</span></span>

## <a name="composing-with-linq"></a><span data-ttu-id="f3494-132">LINQ による作成</span><span class="sxs-lookup"><span data-stu-id="f3494-132">Composing with LINQ</span></span>

<span data-ttu-id="f3494-133">LINQ 演算子を使用して、最初の生 SQL クエリに基づいて構成することができます。</span><span class="sxs-lookup"><span data-stu-id="f3494-133">You can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="f3494-134">EF Core では、これをサブクエリとして扱い、データベースで構成します。</span><span class="sxs-lookup"><span data-stu-id="f3494-134">EF Core will treat it as subquery and compose over it in the database.</span></span> <span data-ttu-id="f3494-135">次の例では、テーブル値関数 (TVF) から選択する生 SQL クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="f3494-135">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF).</span></span> <span data-ttu-id="f3494-136">その後、LINQ を使用してそれを構成し、フィルター処理と並べ替えを行います。</span><span class="sxs-lookup"><span data-stu-id="f3494-136">And then composes on it using LINQ to do filtering and sorting.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedComposed)]

<span data-ttu-id="f3494-137">上記のクエリでは、次の SQL が生成されます。</span><span class="sxs-lookup"><span data-stu-id="f3494-137">Above query generates following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM (
    SELECT * FROM dbo.SearchBlogs(@p0)
) AS [b]
WHERE [b].[Rating] > 3
ORDER BY [b].[Rating] DESC
```

### <a name="including-related-data"></a><span data-ttu-id="f3494-138">関連データを含める</span><span class="sxs-lookup"><span data-stu-id="f3494-138">Including related data</span></span>

<span data-ttu-id="f3494-139">`Include` メソッドを使用して、他の LINQ クエリと同様に、関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="f3494-139">The `Include` method can be used to include related data, just like with any other LINQ query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedInclude)]

<span data-ttu-id="f3494-140">EF Core では提供された SQL がサブクエリとして扱われるため、LINQ を使用して構成するには、生 SQL クエリがコンポーザブルである必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3494-140">Composing with LINQ requires your raw SQL query to be composable since EF Core will treat the supplied SQL as a subquery.</span></span> <span data-ttu-id="f3494-141">`SELECT` キーワードで始まる SQL クエリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="f3494-141">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span> <span data-ttu-id="f3494-142">さらに、次のような、サブクエリでは無効な文字やオプションを、渡される SQL に含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="f3494-142">Further, SQL passed shouldn't contain any characters or options that aren't valid on a subquery, such as:</span></span>

- <span data-ttu-id="f3494-143">末尾のセミコロン</span><span class="sxs-lookup"><span data-stu-id="f3494-143">A trailing semicolon</span></span>
- <span data-ttu-id="f3494-144">SQL Server では、末尾のクエリ レベル ヒント (例: `OPTION (HASH JOIN)`)</span><span class="sxs-lookup"><span data-stu-id="f3494-144">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
- <span data-ttu-id="f3494-145">SQL Server では、`SELECT` 句の `OFFSET 0` または `TOP 100 PERCENT` と共に使用されない `ORDER BY` 句</span><span class="sxs-lookup"><span data-stu-id="f3494-145">On SQL Server, an `ORDER BY` clause that isn't used with `OFFSET 0` OR `TOP 100 PERCENT` in the `SELECT` clause</span></span>

<span data-ttu-id="f3494-146">SQL Server ではストアド プロシージャ呼び出しを構成することができないため、そのような呼び出しに追加のクエリ演算子を適用しようとすると、無効な SQL が発生します。</span><span class="sxs-lookup"><span data-stu-id="f3494-146">SQL Server doesn't allow composing over stored procedure calls, so any attempt to apply additional query operators to such a call will result in invalid SQL.</span></span> <span data-ttu-id="f3494-147">EF Core でストアド プロシージャの構成が試行されないようにするには、`AsEnumerable` または `AsAsyncEnumerable` メソッドの直後に `FromSqlRaw` または `FromSqlInterpolated` メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="f3494-147">Use `AsEnumerable` or `AsAsyncEnumerable` method right after `FromSqlRaw` or `FromSqlInterpolated` methods to make sure that EF Core doesn't try to compose over a stored procedure.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="f3494-148">変更の追跡</span><span class="sxs-lookup"><span data-stu-id="f3494-148">Change Tracking</span></span>

<span data-ttu-id="f3494-149">`FromSqlRaw` または `FromSqlInterpolated` メソッドを使用するクエリでは、EF Core 内の他の LINQ クエリとまったく同じ変更追跡ルールに従います。</span><span class="sxs-lookup"><span data-stu-id="f3494-149">Queries that use the `FromSqlRaw` or `FromSqlInterpolated` methods follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="f3494-150">たとえば、クエリでエンティティ型を予測する場合、既定で結果は追跡されます。</span><span class="sxs-lookup"><span data-stu-id="f3494-150">For example, if the query projects entity types, the results will be tracked by default.</span></span>

<span data-ttu-id="f3494-151">次の例では、テーブル値関数 (TVF) から選択し、`AsNoTracking` の呼び出しを使用して変更追跡を無効にする生 SQL クエリを使用しています。</span><span class="sxs-lookup"><span data-stu-id="f3494-151">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with the call to `AsNoTracking`:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Program.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a><span data-ttu-id="f3494-152">制限事項</span><span class="sxs-lookup"><span data-stu-id="f3494-152">Limitations</span></span>

<span data-ttu-id="f3494-153">生 SQL クエリを使用する場合、注意が必要な制限事項がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="f3494-153">There are a few limitations to be aware of when using raw SQL queries:</span></span>

- <span data-ttu-id="f3494-154">SQL クエリは、エンティティ型のすべてのプロパティのデータを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3494-154">The SQL query must return data for all properties of the entity type.</span></span>
- <span data-ttu-id="f3494-155">結果セットの列名は、プロパティがマップされている列名と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f3494-155">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="f3494-156">この動作は EF6 とは異なることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="f3494-156">Note this behavior is different from EF6.</span></span> <span data-ttu-id="f3494-157">EF6 では、生 SQL クエリのプロパティの列へのマッピングは無視され、結果セットの列名はプロパティ名と一致する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="f3494-157">EF6 ignored property to column mapping for raw SQL queries and result set column names had to match the property names.</span></span>
- <span data-ttu-id="f3494-158">SQL クエリに関連データを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="f3494-158">The SQL query can't contain related data.</span></span> <span data-ttu-id="f3494-159">ただし、多くの場合、`Include` 演算子を使用して関連データを返すクエリを作成することができます (「[関連データを含める](#including-related-data)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="f3494-159">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

## <a name="previous-versions"></a><span data-ttu-id="f3494-160">以前のバージョン</span><span class="sxs-lookup"><span data-stu-id="f3494-160">Previous versions</span></span>

<span data-ttu-id="f3494-161">EF Core バージョン 2.2 以前では、`FromSql` というメソッドの 2 つのオーバーロードがありました。これは新しい `FromSqlRaw` や `FromSqlInterpolated` と同じように動作しました。</span><span class="sxs-lookup"><span data-stu-id="f3494-161">EF Core version 2.2 and earlier had two overloads of method named `FromSql`, which behaved in the same way as the newer `FromSqlRaw` and `FromSqlInterpolated`.</span></span> <span data-ttu-id="f3494-162">挿入文字列メソッドを呼び出すつもりが、誤って生文字列メソッドを簡単に呼び出せてしまいました。その逆も同様です。</span><span class="sxs-lookup"><span data-stu-id="f3494-162">It was easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span> <span data-ttu-id="f3494-163">誤って間違ったオーバーロードを呼び出すと、クエリがパラメーター化される必要があるときにそうならない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f3494-163">Calling wrong overload accidentally could result in queries not being parameterized when they should have been.</span></span>
