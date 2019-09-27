---
title: 生 SQL クエリ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: ebec5775770c0f1e297eaaf35bf644c605a69afc
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197773"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="c278c-102">生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="c278c-102">Raw SQL Queries</span></span>

<span data-ttu-id="c278c-103">Entity Framework Core を使用すると、リレーショナル データベースを操作するときに生 SQL クエリにドロップ ダウンすることができます。</span><span class="sxs-lookup"><span data-stu-id="c278c-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="c278c-104">この方法は、実行するクエリが LINQ を使用して表現できない場合や、LINQ クエリを使用すると非効率的な SQL が生成される場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="c278c-104">This can be useful if the query you want to perform can't be expressed using LINQ, or if using a LINQ query is resulting in an inefficient SQL query.</span></span> <span data-ttu-id="c278c-105">生 SQL クエリは、通常のエンティティ型か、モデルの一部である[キーレス エンティティ型](xref:core/modeling/keyless-entity-types)を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="c278c-105">Raw SQL queries can return regular entity types or [keyless entity types](xref:core/modeling/keyless-entity-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="c278c-106">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/Querying/RawSQL/Sample.cs)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="c278c-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/Querying/RawSQL/Sample.cs) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="c278c-107">基本的な生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="c278c-107">Basic raw SQL queries</span></span>

<span data-ttu-id="c278c-108">`FromSqlRaw` 拡張メソッドを使用して、生 SQL クエリに基づいた LINQ クエリを開始できます。</span><span class="sxs-lookup"><span data-stu-id="c278c-108">You can use the `FromSqlRaw` extension method to begin a LINQ query based on a raw SQL query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSqlRaw("SELECT * FROM dbo.Blogs")
    .ToList();
```

<span data-ttu-id="c278c-109">生 SQL クエリを使用してストアド プロシージャを実行できます。</span><span class="sxs-lookup"><span data-stu-id="c278c-109">Raw SQL queries can be used to execute a stored procedure.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a><span data-ttu-id="c278c-110">パラメーターを渡す</span><span class="sxs-lookup"><span data-stu-id="c278c-110">Passing parameters</span></span>

> [!WARNING]
> <span data-ttu-id="c278c-111">**生 SQL クエリには常にパラメーター化を使用する**</span><span class="sxs-lookup"><span data-stu-id="c278c-111">**Always use parameterization for raw SQL queries**</span></span>
>
> <span data-ttu-id="c278c-112">生 SQL クエリにユーザー指定の値を採用するときは、SQL インジェクション攻撃を防ぐための注意を払う必要があります。</span><span class="sxs-lookup"><span data-stu-id="c278c-112">When introducing any user-provided values into a raw SQL query, care must be taken to avoid SQL injection attacks.</span></span> <span data-ttu-id="c278c-113">そのような値に無効な文字が含まれていないことを検証するだけでなく、SQL テキストとは別に値を送信するパラメーター化を常に使用してください。</span><span class="sxs-lookup"><span data-stu-id="c278c-113">In addition to validating that such values don't contain invalid characters, always use parameterization which sends the values separate from the SQL text.</span></span>
>
> <span data-ttu-id="c278c-114">特に、未検証のユーザー指定の値を含む連結された文字列や補間された文字列 (`$""`) を、`FromSqlRaw` または `ExecuteSqlRaw` に渡さないでください。</span><span class="sxs-lookup"><span data-stu-id="c278c-114">In particular, never pass a concatenated or interpolated string (`$""`) with unvalidated user-provided values into `FromSqlRaw` or `ExecuteSqlRaw`.</span></span> <span data-ttu-id="c278c-115">`FromSqlInterpolated` と `ExecuteSqlInterpolated` のメソッドでは、SQL インジェクション攻撃から保護されるように文字列補間構文を使用できます。</span><span class="sxs-lookup"><span data-stu-id="c278c-115">The `FromSqlInterpolated` and `ExecuteSqlInterpolated` methods allow using string interpolation syntax in a way that protects against SQL injection attacks.</span></span>

<span data-ttu-id="c278c-116">次の例では、パラメーターのプレースホルダーを SQL クエリ文字列に含め、追加の引数を指定することによって、ストアド プロシージャに 1 つのパラメーターを渡しています。</span><span class="sxs-lookup"><span data-stu-id="c278c-116">The following example passes a single parameter to a stored procedure by including a parameter placeholder in the SQL query string and providing an additional argument.</span></span> <span data-ttu-id="c278c-117">これは `String.Format` 構文のように見えますが、指定された値は `DbParameter` にラップされ、生成されたパラメーター名は `{0}` プレースホルダーが指定された場所に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="c278c-117">While this may look like `String.Format` syntax, the supplied value is wrapped in a `DbParameter` and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

<span data-ttu-id="c278c-118">文字列補間を安全に使用できるように、`FromSqlRaw` の代わりに `FromSqlInterpolated` を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="c278c-118">As an alternative to `FromSqlRaw`, you can use `FromSqlInterpolated` which allows the safe use of string interpolation.</span></span> <span data-ttu-id="c278c-119">前の例と同様に、値は `DbParameter` に変換されるため、SQL インジェクションに対して脆弱ではありません。</span><span class="sxs-lookup"><span data-stu-id="c278c-119">As with the previous example, the value is converted to a `DbParameter` and is therefore not vulnerable to SQL injection:</span></span>

> [!NOTE]
> <span data-ttu-id="c278c-120">バージョン 3.0 より前では、`FromSqlRaw` と `FromSqlInterpolated` は、`FromSql` という名前の 2 つのオーバーロードでした。</span><span class="sxs-lookup"><span data-stu-id="c278c-120">Prior to version 3.0, `FromSqlRaw` and `FromSqlInterpolated` were two overloads named `FromSql`.</span></span> <span data-ttu-id="c278c-121">詳細については、[前のバージョンのセクション](#previous-versions)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c278c-121">See the [previous versions section](#previous-versions) for more details.</span></span>


<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSqlInterpolated($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

<span data-ttu-id="c278c-122">また、DbParameter を構築し、それをパラメーター値として指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="c278c-122">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="c278c-123">文字列のプレースホルダーではなく、通常の SQL パラメーターのプレースホルダーが使用されるため、`FromSqlRaw` を安全に使用できます。</span><span class="sxs-lookup"><span data-stu-id="c278c-123">Since a regular SQL parameter placeholder is used, rather than a string placeholder, `FromSqlRaw` can be safely used:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

<span data-ttu-id="c278c-124">これにより、SQL クエリ文字列で名前付きパラメーターを使用できるようになります。これはストアド プロシージャに省略可能なパラメーターがある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="c278c-124">This allows you to use named parameters in the SQL query string, which is useful when a stored procedure has optional parameters:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogs @filterByUser=@user", user)
    .ToList();
```

## <a name="composing-with-linq"></a><span data-ttu-id="c278c-125">LINQ による作成</span><span class="sxs-lookup"><span data-stu-id="c278c-125">Composing with LINQ</span></span>

<span data-ttu-id="c278c-126">データベース内で SQL クエリを作成できる場合は、LINQ 演算子を使用して初期の生 SQL クエリ上に作成することができます。</span><span class="sxs-lookup"><span data-stu-id="c278c-126">If the SQL query can be composed on in the database, then you can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="c278c-127">`SELECT` キーワードで始まる SQL クエリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="c278c-127">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span>

<span data-ttu-id="c278c-128">次の例では、テーブル値関数 (TVF) から選択し、LINQ を使用してフィルター処理と並べ替えを実行する生 SQL クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="c278c-128">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF) and then composes on it using LINQ to perform filtering and sorting.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

<span data-ttu-id="c278c-129">これにより、次の SQL クエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="c278c-129">This will produce the following SQL query:</span></span>

``` sql
SELECT [b].[Id], [b].[Name], [b].[Rating]
        FROM (
            SELECT * FROM dbo.SearchBlogs(@p0)
        ) AS b
        WHERE b."Rating" > 3
        ORDER BY b."Rating" DESC
```

## <a name="change-tracking"></a><span data-ttu-id="c278c-130">変更追跡</span><span class="sxs-lookup"><span data-stu-id="c278c-130">Change Tracking</span></span>

<span data-ttu-id="c278c-131">`FromSql` メソッドを使用するクエリは、EF Core 内の他の LINQ クエリとまったく同じ変更追跡ルールに従います。</span><span class="sxs-lookup"><span data-stu-id="c278c-131">Queries that use the `FromSql` methods follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="c278c-132">たとえば、クエリでエンティティ型を予測する場合、既定で結果は追跡されます。</span><span class="sxs-lookup"><span data-stu-id="c278c-132">For example, if the query projects entity types, the results will be tracked by default.</span></span>

<span data-ttu-id="c278c-133">次の例では、テーブル値関数 (TVF) から選択し、`AsNoTracking` の呼び出しを使用して変更追跡を無効にする生 SQL クエリを使用しています。</span><span class="sxs-lookup"><span data-stu-id="c278c-133">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with the call to `AsNoTracking`:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Query<SearchBlogsDto>()
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .AsNoTracking()
    .ToList();
```

## <a name="including-related-data"></a><span data-ttu-id="c278c-134">関連データを含める</span><span class="sxs-lookup"><span data-stu-id="c278c-134">Including related data</span></span>

<span data-ttu-id="c278c-135">`Include` メソッドを使用して、他の LINQ クエリと同様に、関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="c278c-135">The `Include` method can be used to include related data, just like with any other LINQ query:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

<span data-ttu-id="c278c-136">この場合、生 SQL クエリがコンポーザブルである必要があることに注意してください。特に、ストアド プロシージャの呼び出しでは機能しません。</span><span class="sxs-lookup"><span data-stu-id="c278c-136">Note that this requires your raw SQL query to be composable; it will notably not work with stored procedure calls.</span></span> <span data-ttu-id="c278c-137">「[制限事項](#limitations)」のコンポーザビリティに関するメモを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c278c-137">See notes on composability under [Limitations](#limitations)).</span></span>

## <a name="limitations"></a><span data-ttu-id="c278c-138">制限事項</span><span class="sxs-lookup"><span data-stu-id="c278c-138">Limitations</span></span>

<span data-ttu-id="c278c-139">生 SQL クエリを使用する場合、注意が必要な制限事項がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="c278c-139">There are a few limitations to be aware of when using raw SQL queries:</span></span>

* <span data-ttu-id="c278c-140">SQL クエリは、エンティティ型のすべてのプロパティのデータを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="c278c-140">The SQL query must return data for all properties of the entity type.</span></span>

* <span data-ttu-id="c278c-141">結果セットの列名は、プロパティがマップされている列名と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c278c-141">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="c278c-142">これは EF6 と異なる点です。EF6 では、生 SQL クエリのプロパティ/列のマッピングは無視され、結果セットの列名はプロパティ名と一致する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="c278c-142">Note this is different from EF6 where property/column mapping was ignored for raw SQL queries and result set column names had to match the property names.</span></span>

* <span data-ttu-id="c278c-143">SQL クエリに関連データを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="c278c-143">The SQL query cannot contain related data.</span></span> <span data-ttu-id="c278c-144">ただし、多くの場合、`Include` 演算子を使用して関連データを返すクエリを作成することができます (「[関連データを含める](#including-related-data)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="c278c-144">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

* <span data-ttu-id="c278c-145">このメソッドに渡された `SELECT` ステートメントは、一般的にコンポーザブルである必要があります。EF Core がサーバー上で追加のクエリ演算子を評価する必要がある場合 (たとえば、`FromSql` メソッドの後に適用される LINQ 演算子を変換する場合)、指定された SQL はサブクエリとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="c278c-145">`SELECT` statements passed to this method should generally be composable: If EF Core needs to evaluate additional query operators on the server (for example, to translate LINQ operators applied after `FromSql` methods), the supplied SQL will be treated as a subquery.</span></span> <span data-ttu-id="c278c-146">これは、渡される SQL に、次のようなサブクエリでは無効な文字またはオプションを含めてはならないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="c278c-146">This means that the SQL passed should not contain any characters or options that are not valid on a subquery, such as:</span></span>
  * <span data-ttu-id="c278c-147">末尾のセミコロン</span><span class="sxs-lookup"><span data-stu-id="c278c-147">A trailing semicolon</span></span>
  * <span data-ttu-id="c278c-148">SQL Server では、末尾のクエリ レベル ヒント (例: `OPTION (HASH JOIN)`)</span><span class="sxs-lookup"><span data-stu-id="c278c-148">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
  * <span data-ttu-id="c278c-149">SQL Server で、`SELECT` 句の `OFFSET 0` または `TOP 100 PERCENT` を伴わない `ORDER BY` 句</span><span class="sxs-lookup"><span data-stu-id="c278c-149">On SQL Server, an `ORDER BY` clause that is not accompanied of `OFFSET 0` OR `TOP 100 PERCENT` in the `SELECT` clause</span></span>

* <span data-ttu-id="c278c-150">SQL Server ではストアド プロシージャ呼び出しを構成することができないため、そのような呼び出しに追加のクエリ演算子を適用しようとすると、無効な SQL が発生します。</span><span class="sxs-lookup"><span data-stu-id="c278c-150">Note that SQL Server does not allow composing over stored procedure calls, so any attempt to apply additional query operators to such a call will result in invalid SQL.</span></span> <span data-ttu-id="c278c-151">クエリ演算子は、クライアント評価のため、`AsEnumerable()` の後に導入される場合があります。</span><span class="sxs-lookup"><span data-stu-id="c278c-151">Query operators may be introduced after `AsEnumerable()` for client evaluation.</span></span>

# <a name="previous-versions"></a><span data-ttu-id="c278c-152">以前のバージョン</span><span class="sxs-lookup"><span data-stu-id="c278c-152">Previous versions</span></span>

<span data-ttu-id="c278c-153">EF Core バージョン 2.2 以前では、`FromSql` という名前の 2 つのオーバーロードがありました。これは新しい `FromSqlRaw` や `FromSqlInterpolated` と同じように動作しました。</span><span class="sxs-lookup"><span data-stu-id="c278c-153">EF Core version 2.2 and earlier had two overloads named `FromSql` which behaved in the same way as the newer `FromSqlRaw` and `FromSqlInterpolated`.</span></span> <span data-ttu-id="c278c-154">この場合、挿入文字列メソッドを呼び出すつもりが、誤って raw 文字列メソッドを非常に簡単に呼び出せてしまいます。その逆も同様です。</span><span class="sxs-lookup"><span data-stu-id="c278c-154">This made it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span> <span data-ttu-id="c278c-155">これは、クエリをパラメーター化する必要があるときに、パラメーター化されない結果になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="c278c-155">This could result in queries not being parameterized when they should have been.</span></span>
