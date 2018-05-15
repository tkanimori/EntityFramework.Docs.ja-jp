---
title: 生の SQL クエリの EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
ms.technology: entity-framework-core
uid: core/querying/raw-sql
ms.openlocfilehash: 29b7e20e875bf791a88a92636c1df4bc4e31656b
ms.sourcegitcommit: 038acd91ce2f5a28d76dcd2eab72eeba225e366d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
---
# <a name="raw-sql-queries"></a><span data-ttu-id="2253e-102">生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="2253e-102">Raw SQL Queries</span></span>

<span data-ttu-id="2253e-103">Entity Framework Core では、リレーショナル データベースを使用する場合、生の SQL クエリをドロップ ダウンすることができます。</span><span class="sxs-lookup"><span data-stu-id="2253e-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="2253e-104">実行するクエリは、LINQ を使用して表現できない場合、またはデータベースに送信される非効率的な SQL での LINQ クエリを使用するが結果として得られる場合、これは役立つあります。</span><span class="sxs-lookup"><span data-stu-id="2253e-104">This can be useful if the query you want to perform can't be expressed using LINQ, or if using a LINQ query is resulting in inefficient SQL being sent to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="2253e-105">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="2253e-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="limitations"></a><span data-ttu-id="2253e-106">制限事項</span><span class="sxs-lookup"><span data-stu-id="2253e-106">Limitations</span></span>

<span data-ttu-id="2253e-107">生の SQL クエリを使用する場合の注意すべきいくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="2253e-107">There are a few limitations to be aware of when using raw SQL queries:</span></span>
* <span data-ttu-id="2253e-108">SQL クエリは、モデルの一部であるエンティティ型を返すにのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="2253e-108">SQL queries can only be used to return entity types that are part of your model.</span></span> <span data-ttu-id="2253e-109">バックログに機能強化が[生の SQL クエリからアドホック型を返す有効](https://github.com/aspnet/EntityFramework/issues/1862)です。</span><span class="sxs-lookup"><span data-stu-id="2253e-109">There is an enhancement on our backlog to [enable returning ad-hoc types from raw SQL queries](https://github.com/aspnet/EntityFramework/issues/1862).</span></span>

* <span data-ttu-id="2253e-110">SQL クエリでは、エンティティまたはクエリの種類のすべてのプロパティのデータを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="2253e-110">The SQL query must return data for all properties of the entity or query type.</span></span>

* <span data-ttu-id="2253e-111">結果セット内の列名は、プロパティにマップする列名と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2253e-111">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="2253e-112">これとは異なる SQL クエリの生のプロパティ/列マッピングは無視されました、結果セット列名は、プロパティの名前と一致する必要がある EF6 に注意してください。</span><span class="sxs-lookup"><span data-stu-id="2253e-112">Note this is different from EF6 where property/column mapping was ignored for raw SQL queries and result set column names had to match the property names.</span></span>

* <span data-ttu-id="2253e-113">SQL クエリでは、関連するデータを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="2253e-113">The SQL query cannot contain related data.</span></span> <span data-ttu-id="2253e-114">ただし、多くの場合を組み込むことができますを使用して、クエリの上に、`Include`関連データを返す演算子 (を参照してください[関連データを含む](#including-related-data))。</span><span class="sxs-lookup"><span data-stu-id="2253e-114">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

* <span data-ttu-id="2253e-115">`SELECT` このメソッドに渡されたステートメント一般的には、コンポーザブル: EF 中核となる場合は、サーバー上の他のクエリ演算子を評価する必要があります (例: 変換 LINQ 演算子の後に適用する`FromSql`)、指定された SQL はサブクエリとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="2253e-115">`SELECT` statements passed to this method should generally be composable: If EF Core needs to evaluate additional query operators on the server (e.g. to translate LINQ operators applied after `FromSql`), the supplied SQL will be treated as a subquery.</span></span> <span data-ttu-id="2253e-116">これは、渡された SQL では、文字またはなど、サブクエリに無効なオプションを含める必要がありますいないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="2253e-116">This means that the SQL passed should not contain any characters or options that are not valid on a subquery, such as:</span></span>
  * <span data-ttu-id="2253e-117">最後のセミコロン</span><span class="sxs-lookup"><span data-stu-id="2253e-117">a trailing semicolon</span></span>
  * <span data-ttu-id="2253e-118">SQL Server で、後続のクエリ レベル ヒント、例。 `OPTION (HASH JOIN)`</span><span class="sxs-lookup"><span data-stu-id="2253e-118">On SQL Server, a trailing query-level hint, e.g. `OPTION (HASH JOIN)`</span></span>
  * <span data-ttu-id="2253e-119">SQL Server 上、`ORDER BY`の伴わない句`TOP 100 PERCENT`で、`SELECT`句</span><span class="sxs-lookup"><span data-stu-id="2253e-119">On SQL Server, an `ORDER BY` clause that is not accompanied of `TOP 100 PERCENT` in the `SELECT` clause</span></span>

* <span data-ttu-id="2253e-120">以外の SQL ステートメント`SELECT`コンポーザブルでないとして自動的に認識されます。</span><span class="sxs-lookup"><span data-stu-id="2253e-120">SQL statements other than `SELECT` are recognized automatically as non-composable.</span></span> <span data-ttu-id="2253e-121">その結果、ストアド プロシージャの完全な結果は常に、クライアントに返されます、後に、LINQ 演算子が適用される`FromSql`メモリ内に評価されます。</span><span class="sxs-lookup"><span data-stu-id="2253e-121">As a consequence, the full results of stored procedures are always returned to the client and any LINQ operators applied after `FromSql` are evaluated in-memory.</span></span> 

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="2253e-122">基本的な生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="2253e-122">Basic raw SQL queries</span></span>

<span data-ttu-id="2253e-123">使用することができます、 *FromSql*生 SQL クエリに基づく LINQ クエリを開始する拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="2253e-123">You can use the *FromSql* extension method to begin a LINQ query based on a raw SQL query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

<span data-ttu-id="2253e-124">生の SQL クエリは、ストアド プロシージャの実行に使用できます。</span><span class="sxs-lookup"><span data-stu-id="2253e-124">Raw SQL queries can be used to execute a stored procedure.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a><span data-ttu-id="2253e-125">パラメーターを渡す</span><span class="sxs-lookup"><span data-stu-id="2253e-125">Passing parameters</span></span>

<span data-ttu-id="2253e-126">SQL を受け入れる任意の API と同様には、ユーザーの SQL インジェクション攻撃を防ぐために入力をパラメーター化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2253e-126">As with any API that accepts SQL, it is important to parameterize any user input to protect against a SQL injection attack.</span></span> <span data-ttu-id="2253e-127">SQL クエリ文字列にパラメーターのプレース ホルダーを含めるし、追加の引数とパラメーター値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="2253e-127">You can include parameter placeholders in the SQL query string and then supply parameter values as additional arguments.</span></span> <span data-ttu-id="2253e-128">指定するパラメーター値が自動的に変換する、`DbParameter`です。</span><span class="sxs-lookup"><span data-stu-id="2253e-128">Any parameter values you supply will automatically be converted to a `DbParameter`.</span></span>

<span data-ttu-id="2253e-129">次の例では、1 つのパラメーターをストアド プロシージャに渡します。</span><span class="sxs-lookup"><span data-stu-id="2253e-129">The following example passes a single parameter to a stored procedure.</span></span> <span data-ttu-id="2253e-130">同様に見えるかもしれませんがこの`String.Format`構文に指定された値がラップされてパラメーターと、生成されたパラメーター名は、where を挿入、`{0}`プレース ホルダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="2253e-130">While this may look like `String.Format` syntax, the supplied value is wrapped in a parameter and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

<span data-ttu-id="2253e-131">これは、同じクエリが EF コア 2.0 以降がサポートされている文字列の補間の構文を使用しています。</span><span class="sxs-lookup"><span data-stu-id="2253e-131">This is the same query but using string interpolation syntax, which is supported in EF Core 2.0 and above:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

<span data-ttu-id="2253e-132">また、DbParameter を構築し、パラメーター値として指定できます。</span><span class="sxs-lookup"><span data-stu-id="2253e-132">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="2253e-133">これにより、SQL クエリ文字列の名前付きパラメーターを使用するには</span><span class="sxs-lookup"><span data-stu-id="2253e-133">This allows you to use named parameters in the SQL query string</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

## <a name="composing-with-linq"></a><span data-ttu-id="2253e-134">LINQ で構成します。</span><span class="sxs-lookup"><span data-stu-id="2253e-134">Composing with LINQ</span></span>

<span data-ttu-id="2253e-135">場合は、データベース内の SQL クエリを組み込むことのできる、LINQ の演算子を使用して最初の生の SQL クエリの上に作成することができます。</span><span class="sxs-lookup"><span data-stu-id="2253e-135">If the SQL query can be composed on in the database, then you can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="2253e-136">SQL クエリの中で構成できる、`SELECT`キーワード。</span><span class="sxs-lookup"><span data-stu-id="2253e-136">SQL queries that can be composed on being with the `SELECT` keyword.</span></span>

<span data-ttu-id="2253e-137">次の例では、LINQ を使用してフィルター処理および並べ替えを実行することでから、テーブル値関数 (TVF) を選択し、合成を生 SQL クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="2253e-137">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF) and then composes on it using LINQ to perform filtering and sorting.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

### <a name="including-related-data"></a><span data-ttu-id="2253e-138">関連するデータを含む</span><span class="sxs-lookup"><span data-stu-id="2253e-138">Including related data</span></span>

<span data-ttu-id="2253e-139">クエリに関連するデータを含める LINQ 演算子を含む構成を使用できます。</span><span class="sxs-lookup"><span data-stu-id="2253e-139">Composing with LINQ operators can be used to include related data in the query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

> [!WARNING]  
> <span data-ttu-id="2253e-140">**常に生の SQL クエリをパラメーター化を使用:** 生 SQL を受け取る Api に文字列など`FromSql`と`ExecuteSqlCommand`パラメーターとして簡単に渡される値を許可します。</span><span class="sxs-lookup"><span data-stu-id="2253e-140">**Always use parameterization for raw SQL queries:** APIs that accept a raw SQL string such as `FromSql` and `ExecuteSqlCommand` allow values to be easily passed as parameters.</span></span> <span data-ttu-id="2253e-141">ユーザーの入力だけでなく、常に生 SQL クエリ/コマンドで使用される任意の値のパラメーター化を使用します。</span><span class="sxs-lookup"><span data-stu-id="2253e-141">In addition to validating user input, always use parameterization for any values used in a raw SQL query/command.</span></span> <span data-ttu-id="2253e-142">使用している文字列の連結するには、SQL インジェクション攻撃を防ぐために任意の入力を検証、クエリ文字列の一部を動的に構築する場合</span><span class="sxs-lookup"><span data-stu-id="2253e-142">If you are using string concatenation to dynamically build any part of the query string then you are responsible for validating any input to protect against SQL injection attacks.</span></span>
