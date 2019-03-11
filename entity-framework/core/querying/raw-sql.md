---
title: 生 SQL クエリ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: ad7ac3099cfd4c49b88acfbbff61f2af9294b6ec
ms.sourcegitcommit: a013e243a14f384999ceccaf9c779b8c1ae3b936
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463244"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="aa611-102">生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="aa611-102">Raw SQL Queries</span></span>

<span data-ttu-id="aa611-103">Entity Framework Core を使用すると、リレーショナル データベースを操作するときに生 SQL クエリにドロップ ダウンすることができます。</span><span class="sxs-lookup"><span data-stu-id="aa611-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="aa611-104">この方法は、実行するクエリが LINQ を使用して表現できない場合や、LINQ クエリを使用すると非効率的な SQL が生成される場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="aa611-104">This can be useful if the query you want to perform can't be expressed using LINQ, or if using a LINQ query is resulting in inefficient SQL queries.</span></span> <span data-ttu-id="aa611-105">生 SQL クエリは、エンティティ型か、モデルの一部である[クエリ型](xref:core/modeling/query-types) (EF Core 2.1 以降) を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="aa611-105">Raw SQL queries can return entity types or, starting with EF Core 2.1, [query types](xref:core/modeling/query-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="aa611-106">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="aa611-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="aa611-107">基本的な生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="aa611-107">Basic raw SQL queries</span></span>

<span data-ttu-id="aa611-108">*FromSql* 拡張メソッドを使用して、生 SQL クエリに基づいた LINQ クエリを開始できます。</span><span class="sxs-lookup"><span data-stu-id="aa611-108">You can use the *FromSql* extension method to begin a LINQ query based on a raw SQL query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

<span data-ttu-id="aa611-109">生 SQL クエリを使用してストアド プロシージャを実行できます。</span><span class="sxs-lookup"><span data-stu-id="aa611-109">Raw SQL queries can be used to execute a stored procedure.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a><span data-ttu-id="aa611-110">パラメーターを渡す</span><span class="sxs-lookup"><span data-stu-id="aa611-110">Passing parameters</span></span>

<span data-ttu-id="aa611-111">SQL を受け取る API の場合と同様に、SQL インジェクション攻撃から保護するには、ユーザー入力をパラメーター化することが重要です。</span><span class="sxs-lookup"><span data-stu-id="aa611-111">As with any API that accepts SQL, it is important to parameterize any user input to protect against a SQL injection attack.</span></span> <span data-ttu-id="aa611-112">SQL クエリ文字列にパラメーターのプレースホルダーを含めて、追加の引数としてパラメーター値を指定することができます。</span><span class="sxs-lookup"><span data-stu-id="aa611-112">You can include parameter placeholders in the SQL query string and then supply parameter values as additional arguments.</span></span> <span data-ttu-id="aa611-113">指定したパラメーター値は自動的に `DbParameter` に変換されます。</span><span class="sxs-lookup"><span data-stu-id="aa611-113">Any parameter values you supply will automatically be converted to a `DbParameter`.</span></span>

<span data-ttu-id="aa611-114">次の例では、ストアド プロシージャに 1 つのパラメーターを渡しています。</span><span class="sxs-lookup"><span data-stu-id="aa611-114">The following example passes a single parameter to a stored procedure.</span></span> <span data-ttu-id="aa611-115">これは `String.Format` 構文のように見えますが、指定された値はパラメーターにラップされ、生成されたパラメーター名は `{0}` プレースホルダーが指定された場所に挿入されます。</span><span class="sxs-lookup"><span data-stu-id="aa611-115">While this may look like `String.Format` syntax, the supplied value is wrapped in a parameter and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

<span data-ttu-id="aa611-116">これは同じクエリですが、EF Core 2.0 以降でサポートされている文字列補間構文を使用しています。</span><span class="sxs-lookup"><span data-stu-id="aa611-116">This is the same query but using string interpolation syntax, which is supported in EF Core 2.0 and above:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

<span data-ttu-id="aa611-117">また、DbParameter を構築し、それをパラメーター値として指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="aa611-117">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="aa611-118">こうすることで、SQL クエリ文字列に名前付きパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="aa611-118">This allows you to use named parameters in the SQL query string.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

## <a name="composing-with-linq"></a><span data-ttu-id="aa611-119">LINQ による作成</span><span class="sxs-lookup"><span data-stu-id="aa611-119">Composing with LINQ</span></span>

<span data-ttu-id="aa611-120">データベース内で SQL クエリを作成できる場合は、LINQ 演算子を使用して初期の生 SQL クエリ上に作成することができます。</span><span class="sxs-lookup"><span data-stu-id="aa611-120">If the SQL query can be composed on in the database, then you can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="aa611-121">`SELECT` キーワードで始まる SQL クエリを作成できます。</span><span class="sxs-lookup"><span data-stu-id="aa611-121">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span>

<span data-ttu-id="aa611-122">次の例では、テーブル値関数 (TVF) から選択し、LINQ を使用してフィルター処理と並べ替えを実行する生 SQL クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="aa611-122">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF) and then composes on it using LINQ to perform filtering and sorting.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

## <a name="change-tracking"></a><span data-ttu-id="aa611-123">変更追跡</span><span class="sxs-lookup"><span data-stu-id="aa611-123">Change Tracking</span></span>

<span data-ttu-id="aa611-124">`FromSql()` を使用するクエリは、EF Core 内の他の LINQ クエリとまったく同じ変更追跡ルールに従います。</span><span class="sxs-lookup"><span data-stu-id="aa611-124">Queries that use the `FromSql()` follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="aa611-125">たとえば、クエリでエンティティ型を予測する場合、既定で結果は追跡されます。</span><span class="sxs-lookup"><span data-stu-id="aa611-125">For example, if the query projects entity types, the results will be tracked by default.</span></span>  

<span data-ttu-id="aa611-126">次の例では、テーブル値関数 (TVF) から選択し、.AsNoTracking() の呼び出しを使用して変更追跡を無効にする生の SQL クエリを使用しています。</span><span class="sxs-lookup"><span data-stu-id="aa611-126">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with teh call to .AsNoTracking():</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Query<SearchBlogsDto>()
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .AsNoTracking()
    .ToList();
```

## <a name="including-related-data"></a><span data-ttu-id="aa611-127">関連データを含める</span><span class="sxs-lookup"><span data-stu-id="aa611-127">Including related data</span></span>

<span data-ttu-id="aa611-128">`Include()` メソッドを使用して、他の LINQ クエリと同様に、関連データを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="aa611-128">The `Include()` method can be used to include related data, just like with any other LINQ query:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

## <a name="limitations"></a><span data-ttu-id="aa611-129">制限事項</span><span class="sxs-lookup"><span data-stu-id="aa611-129">Limitations</span></span>

<span data-ttu-id="aa611-130">生 SQL クエリを使用する場合、注意が必要な制限事項がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="aa611-130">There are a few limitations to be aware of when using raw SQL queries:</span></span>

* <span data-ttu-id="aa611-131">SQL クエリは、エンティティ型またはクエリ型のすべてのプロパティのデータを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="aa611-131">The SQL query must return data for all properties of the entity or query type.</span></span>

* <span data-ttu-id="aa611-132">結果セットの列名は、プロパティがマップされている列名と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aa611-132">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="aa611-133">これは EF6 と異なる点です。EF6 では、生 SQL クエリのプロパティ/列のマッピングは無視され、結果セットの列名はプロパティ名と一致する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="aa611-133">Note this is different from EF6 where property/column mapping was ignored for raw SQL queries and result set column names had to match the property names.</span></span>

* <span data-ttu-id="aa611-134">SQL クエリに関連データを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="aa611-134">The SQL query cannot contain related data.</span></span> <span data-ttu-id="aa611-135">ただし、多くの場合、`Include` 演算子を使用して関連データを返すクエリを作成することができます (「[関連データを含める](#including-related-data)」を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="aa611-135">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

* <span data-ttu-id="aa611-136">このメソッドに渡された `SELECT` ステートメントは、一般的にコンポーザブルである必要があります。EF Core がサーバー上で追加のクエリ演算子を評価する必要がある場合 (たとえば、`FromSql` の後に適用される LINQ 演算子を変換する場合)、指定された SQL はサブクエリとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="aa611-136">`SELECT` statements passed to this method should generally be composable: If EF Core needs to evaluate additional query operators on the server (for example, to translate LINQ operators applied after `FromSql`), the supplied SQL will be treated as a subquery.</span></span> <span data-ttu-id="aa611-137">これは、渡される SQL に、次のようなサブクエリでは無効な文字またはオプションを含めてはならないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="aa611-137">This means that the SQL passed should not contain any characters or options that are not valid on a subquery, such as:</span></span>
  * <span data-ttu-id="aa611-138">末尾のセミコロン</span><span class="sxs-lookup"><span data-stu-id="aa611-138">a trailing semicolon</span></span>
  * <span data-ttu-id="aa611-139">SQL Server では、末尾のクエリ レベル ヒント (例: `OPTION (HASH JOIN)`)</span><span class="sxs-lookup"><span data-stu-id="aa611-139">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
  * <span data-ttu-id="aa611-140">SQL Server では、`SELECT` 句の `TOP 100 PERCENT` を伴わない `ORDER BY` 句</span><span class="sxs-lookup"><span data-stu-id="aa611-140">On SQL Server, an `ORDER BY` clause that is not accompanied of `TOP 100 PERCENT` in the `SELECT` clause</span></span>

* <span data-ttu-id="aa611-141">`SELECT` 以外の SQL ステートメントは、自動的に非コンポーザブルと認識されます。</span><span class="sxs-lookup"><span data-stu-id="aa611-141">SQL statements other than `SELECT` are recognized automatically as non-composable.</span></span> <span data-ttu-id="aa611-142">その結果、ストアド プロシージャのすべての結果が常にクライアントに返され、`FromSql` の後に適用されたすべての LINQ 演算子はメモリ内で評価されます。</span><span class="sxs-lookup"><span data-stu-id="aa611-142">As a consequence, the full results of stored procedures are always returned to the client and any LINQ operators applied after `FromSql` are evaluated in-memory.</span></span>

> [!WARNING]  
> <span data-ttu-id="aa611-143">**生 SQL クエリには常にパラメーター化を使用する:** ユーザーの入力を検証するだけでなく、生 SQL クエリ/コマンドで使用される値には常にパラメーター化を使用してください。</span><span class="sxs-lookup"><span data-stu-id="aa611-143">**Always use parameterization for raw SQL queries:** In addition to validating user input, always use parameterization for any values used in a raw SQL query/command.</span></span> <span data-ttu-id="aa611-144">`FromSql` や `ExecuteSqlCommand` のような生 SQL 文字列を受け取る API では、値をパラメーターとして簡単に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="aa611-144">APIs that accept a raw SQL string such as `FromSql` and `ExecuteSqlCommand` allow values to be easily passed as parameters.</span></span> <span data-ttu-id="aa611-145">FormattableString を受け入れる `FromSql` と `ExecuteSqlCommand` のオーバーロードでも、SQL インジェクション攻撃からの保護に役立つ方法での文字列補間の使用が許可されます。</span><span class="sxs-lookup"><span data-stu-id="aa611-145">Overloads of `FromSql` and `ExecuteSqlCommand` that accept FormattableString also allow using string interpolation syntaxt in a way that helps protect against SQL injection attacks.</span></span> 
> 
> <span data-ttu-id="aa611-146">文字列連結または補間を使用して、クエリ文字列のいずれかの部分を動的に構築する場合、または動的 SQL としてユーザー入力を実行できるステートメントまたはストアド プロシージャにその入力を渡す場合は、SQL インジェクション攻撃から保護するためにご自身で入力を検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="aa611-146">If you are using string concatenation or interpolation to dynamically build any part of the query string, or passing user input to statements or stored procedures that can execute those inputs as dynamic SQL, then you are responsible for validating any input to protect against SQL injection attacks.</span></span>
