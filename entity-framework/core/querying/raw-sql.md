---
title: 生 SQL クエリ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: 5bddddfbc2fe8d0ba99914f03b28bde4076fae42
ms.sourcegitcommit: e66745c9f91258b2cacf5ff263141be3cba4b09e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2019
ms.locfileid: "54058715"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="8ee11-102">生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="8ee11-102">Raw SQL Queries</span></span>

<span data-ttu-id="8ee11-103">Entity Framework Core を使用すると、リレーショナル データベースを操作するときに生 SQL クエリにドロップ ダウンすることができます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="8ee11-104">この方法は、実行するクエリが LINQ を使用して表現できない場合や、LINQ クエリを使用すると非効率的な SQL が生成される場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-104">This can be useful if the query you want to perform can't be expressed using LINQ, or if using a LINQ query is resulting in inefficient SQL queries.</span></span> <span data-ttu-id="8ee11-105">生 SQL クエリは、エンティティ型か、モデルの一部である[クエリ型](xref:core/modeling/query-types) (EF Core 2.1 以降) を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-105">Raw SQL queries can return entity types or, starting with EF Core 2.1, [query types](xref:core/modeling/query-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="8ee11-106">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="limitations"></a><span data-ttu-id="8ee11-107">制限事項</span><span class="sxs-lookup"><span data-stu-id="8ee11-107">Limitations</span></span>

<span data-ttu-id="8ee11-108">生 SQL クエリを使用する場合、注意が必要な制限事項がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="8ee11-108">There are a few limitations to be aware of when using raw SQL queries:</span></span>

* <span data-ttu-id="8ee11-109">SQL クエリは、エンティティ型またはクエリ型のすべてのプロパティのデータを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ee11-109">The SQL query must return data for all properties of the entity or query type.</span></span>

* <span data-ttu-id="8ee11-110">結果セットの列名は、プロパティがマップされている列名と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ee11-110">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="8ee11-111">これは EF6 と異なる点です。EF6 では、生 SQL クエリのプロパティ/列のマッピングは無視され、結果セットの列名はプロパティ名と一致する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="8ee11-111">Note this is different from EF6 where property/column mapping was ignored for raw SQL queries and result set column names had to match the property names.</span></span>

* <span data-ttu-id="8ee11-112">SQL クエリに関連データを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="8ee11-112">The SQL query cannot contain related data.</span></span> <span data-ttu-id="8ee11-113">ただし、多くの場合、`Include` 演算子を使用して関連データを返すクエリを作成することができます (「[関連データを含める](#including-related-data)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="8ee11-113">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

* <span data-ttu-id="8ee11-114">このメソッドに渡された `SELECT` ステートメントは、一般的にコンポーザブルである必要があります。EF Core がサーバー上で追加のクエリ演算子を評価する必要がある場合 (たとえば、`FromSql` の後に適用される LINQ 演算子を変換する場合)、指定された SQL はサブクエリとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-114">`SELECT` statements passed to this method should generally be composable: If EF Core needs to evaluate additional query operators on the server (for example, to translate LINQ operators applied after `FromSql`), the supplied SQL will be treated as a subquery.</span></span> <span data-ttu-id="8ee11-115">これは、渡される SQL に、次のようなサブクエリでは無効な文字またはオプションを含めてはならないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="8ee11-115">This means that the SQL passed should not contain any characters or options that are not valid on a subquery, such as:</span></span>
  * <span data-ttu-id="8ee11-116">末尾のセミコロン</span><span class="sxs-lookup"><span data-stu-id="8ee11-116">a trailing semicolon</span></span>
  * <span data-ttu-id="8ee11-117">SQL Server では、末尾のクエリ レベル ヒント (例: `OPTION (HASH JOIN)`)</span><span class="sxs-lookup"><span data-stu-id="8ee11-117">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
  * <span data-ttu-id="8ee11-118">SQL Server では、`SELECT` 句の `TOP 100 PERCENT` を伴わない `ORDER BY` 句</span><span class="sxs-lookup"><span data-stu-id="8ee11-118">On SQL Server, an `ORDER BY` clause that is not accompanied of `TOP 100 PERCENT` in the `SELECT` clause</span></span>

* <span data-ttu-id="8ee11-119">`SELECT` 以外の SQL ステートメントは、自動的に非コンポーザブルと認識されます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-119">SQL statements other than `SELECT` are recognized automatically as non-composable.</span></span> <span data-ttu-id="8ee11-120">その結果、ストアド プロシージャのすべての結果が常にクライアントに返され、`FromSql` の後に適用されたすべての LINQ 演算子はメモリ内で評価されます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-120">As a consequence, the full results of stored procedures are always returned to the client and any LINQ operators applied after `FromSql` are evaluated in-memory.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="8ee11-121">基本的な生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="8ee11-121">Basic raw SQL queries</span></span>

<span data-ttu-id="8ee11-122">*FromSql* 拡張メソッドを使用して、生 SQL クエリに基づいた LINQ クエリを開始できます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-122">You can use the *FromSql* extension method to begin a LINQ query based on a raw SQL query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

<span data-ttu-id="8ee11-123">生 SQL クエリを使用してストアド プロシージャを実行できます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-123">Raw SQL queries can be used to execute a stored procedure.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a><span data-ttu-id="8ee11-124">パラメーターを渡す</span><span class="sxs-lookup"><span data-stu-id="8ee11-124">Passing parameters</span></span>

<span data-ttu-id="8ee11-125">SQL を受け取る API の場合と同様に、SQL インジェクション攻撃から保護するには、ユーザー入力をパラメーター化することが重要です。</span><span class="sxs-lookup"><span data-stu-id="8ee11-125">As with any API that accepts SQL, it is important to parameterize any user input to protect against a SQL injection attack.</span></span> <span data-ttu-id="8ee11-126">SQL クエリ文字列にパラメーターのプレースホルダーを含めて、追加の引数としてパラメーター値を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-126">You can include parameter placeholders in the SQL query string and then supply parameter values as additional arguments.</span></span> <span data-ttu-id="8ee11-127">指定したパラメーター値は自動的に `DbParameter` に変換されます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-127">Any parameter values you supply will automatically be converted to a `DbParameter`.</span></span>

<span data-ttu-id="8ee11-128">次の例では、ストアド プロシージャに 1 つのパラメーターを渡しています。</span><span class="sxs-lookup"><span data-stu-id="8ee11-128">The following example passes a single parameter to a stored procedure.</span></span> <span data-ttu-id="8ee11-129">これは `String.Format` 構文のように見えますが、指定された値はパラメーターにラップされ、生成されたパラメーター名は `{0}` プレースホルダーが指定された場所に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-129">While this may look like `String.Format` syntax, the supplied value is wrapped in a parameter and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

<span data-ttu-id="8ee11-130">これは同じクエリですが、EF Core 2.0 以降でサポートされている文字列補間構文を使用しています。</span><span class="sxs-lookup"><span data-stu-id="8ee11-130">This is the same query but using string interpolation syntax, which is supported in EF Core 2.0 and above:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

<span data-ttu-id="8ee11-131">また、DbParameter を構築し、それをパラメーター値として指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-131">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="8ee11-132">こうすることで、SQL クエリ文字列に名前付きパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-132">This allows you to use named parameters in the SQL query string.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

## <a name="composing-with-linq"></a><span data-ttu-id="8ee11-133">LINQ による作成</span><span class="sxs-lookup"><span data-stu-id="8ee11-133">Composing with LINQ</span></span>

<span data-ttu-id="8ee11-134">データベース内で SQL クエリを作成できる場合は、LINQ 演算子を使用して初期の生 SQL クエリ上に作成することができます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-134">If the SQL query can be composed on in the database, then you can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="8ee11-135">`SELECT` キーワードで始まる SQL クエリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-135">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span>

<span data-ttu-id="8ee11-136">次の例では、テーブル値関数 (TVF) から選択し、LINQ を使用してフィルター処理と並べ替えを実行する生 SQL クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="8ee11-136">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF) and then composes on it using LINQ to perform filtering and sorting.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

### <a name="including-related-data"></a><span data-ttu-id="8ee11-137">関連データを含める</span><span class="sxs-lookup"><span data-stu-id="8ee11-137">Including related data</span></span>

<span data-ttu-id="8ee11-138">LINQ 演算子による作成を使用して、関連データをクエリに含めることができます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-138">Composing with LINQ operators can be used to include related data in the query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

> [!WARNING]  
> <span data-ttu-id="8ee11-139">**生 SQL クエリには常にパラメーター化を使用する:** `FromSql` や `ExecuteSqlCommand` のような生 SQL 文字列を受け取る API では、値をパラメーターとして簡単に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="8ee11-139">**Always use parameterization for raw SQL queries:** APIs that accept a raw SQL string such as `FromSql` and `ExecuteSqlCommand` allow values to be easily passed as parameters.</span></span> <span data-ttu-id="8ee11-140">ユーザーの入力を検証するだけでなく、生 SQL クエリ/コマンドで使用される値には常にパラメーター化を使用してください。</span><span class="sxs-lookup"><span data-stu-id="8ee11-140">In addition to validating user input, always use parameterization for any values used in a raw SQL query/command.</span></span> <span data-ttu-id="8ee11-141">文字列の連結を使用してクエリ文字列の一部を動的に構築する場合は、SQL インジェクション攻撃から保護するために入力を検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8ee11-141">If you are using string concatenation to dynamically build any part of the query string then you are responsible for validating any input to protect against SQL injection attacks.</span></span>
