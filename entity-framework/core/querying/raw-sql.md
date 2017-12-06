---
title: "生の SQL クエリの EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
ms.technology: entity-framework-core
uid: core/querying/raw-sql
ms.openlocfilehash: ddf3a841800684688d50dcf9323f4d83c851222f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="raw-sql-queries"></a><span data-ttu-id="d980d-102">生の SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="d980d-102">Raw SQL Queries</span></span>

<span data-ttu-id="d980d-103">Entity Framework Core では、リレーショナル データベースを使用する場合、生の SQL クエリをドロップ ダウンすることができます。</span><span class="sxs-lookup"><span data-stu-id="d980d-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="d980d-104">実行するクエリは、LINQ を使用して表現できない場合、またはデータベースに送信される非効率的な SQL での LINQ クエリを使用するが結果として得られる場合、これは役立つあります。</span><span class="sxs-lookup"><span data-stu-id="d980d-104">This can be useful if the query you want to perform can't be expressed using LINQ, or if using a LINQ query is resulting in inefficient SQL being sent to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="d980d-105">この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)GitHub でします。</span><span class="sxs-lookup"><span data-stu-id="d980d-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="limitations"></a><span data-ttu-id="d980d-106">制限事項</span><span class="sxs-lookup"><span data-stu-id="d980d-106">Limitations</span></span>

<span data-ttu-id="d980d-107">いくつかのような制限の生の SQL クエリを使用する場合があります。</span><span class="sxs-lookup"><span data-stu-id="d980d-107">There are a couple of limitations to be aware of when using raw SQL queries:</span></span>
* <span data-ttu-id="d980d-108">SQL クエリは、モデルの一部であるエンティティ型を返すにのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="d980d-108">SQL queries can only be used to return entity types that are part of your model.</span></span> <span data-ttu-id="d980d-109">バックログに機能強化が[生の SQL クエリからアドホック型を返す有効](https://github.com/aspnet/EntityFramework/issues/1862)です。</span><span class="sxs-lookup"><span data-stu-id="d980d-109">There is an enhancement on our backlog to [enable returning ad-hoc types from raw SQL queries](https://github.com/aspnet/EntityFramework/issues/1862).</span></span>

* <span data-ttu-id="d980d-110">SQL クエリでは、エンティティ型のすべてのプロパティのデータを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="d980d-110">The SQL query must return data for all properties of the entity type.</span></span>

* <span data-ttu-id="d980d-111">結果セット内の列名は、プロパティにマップする列名と一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d980d-111">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="d980d-112">これとは異なる SQL クエリの生のプロパティ/列マッピングは無視されました、結果セット列名は、プロパティの名前と一致する必要がある EF6 に注意してください。</span><span class="sxs-lookup"><span data-stu-id="d980d-112">Note this is different from EF6 where property/column mapping was ignored for raw SQL queries and result set column names had to match the property names.</span></span>

* <span data-ttu-id="d980d-113">SQL クエリでは、関連するデータを含めることはできません。</span><span class="sxs-lookup"><span data-stu-id="d980d-113">The SQL query cannot contain related data.</span></span> <span data-ttu-id="d980d-114">ただし、多くの場合を組み込むことができますを使用して、クエリの上に、`Include`関連データを返す演算子 (を参照してください[関連データを含む](#including-related-data))。</span><span class="sxs-lookup"><span data-stu-id="d980d-114">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="d980d-115">基本的な生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="d980d-115">Basic raw SQL queries</span></span>

<span data-ttu-id="d980d-116">使用することができます、 *FromSql*生 SQL クエリに基づく LINQ クエリを開始する拡張メソッド。</span><span class="sxs-lookup"><span data-stu-id="d980d-116">You can use the *FromSql* extension method to begin a LINQ query based on a raw SQL query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

<span data-ttu-id="d980d-117">生の SQL クエリは、ストアド プロシージャの実行に使用できます。</span><span class="sxs-lookup"><span data-stu-id="d980d-117">Raw SQL queries can be used to execute a stored procedure.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a><span data-ttu-id="d980d-118">パラメーターを渡す</span><span class="sxs-lookup"><span data-stu-id="d980d-118">Passing parameters</span></span>

<span data-ttu-id="d980d-119">SQL を受け入れる任意の API と同様には、ユーザーの SQL インジェクション攻撃を防ぐために入力をパラメーター化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d980d-119">As with any API that accepts SQL, it is important to parameterize any user input to protect against a SQL injection attack.</span></span> <span data-ttu-id="d980d-120">SQL クエリ文字列にパラメーターのプレース ホルダーを含めるし、追加の引数とパラメーター値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="d980d-120">You can include parameter placeholders in the SQL query string and then supply parameter values as additional arguments.</span></span> <span data-ttu-id="d980d-121">指定するパラメーター値が自動的に変換する、`DbParameter`です。</span><span class="sxs-lookup"><span data-stu-id="d980d-121">Any parameter values you supply will automatically be converted to a `DbParameter`.</span></span>

<span data-ttu-id="d980d-122">次の例では、1 つのパラメーターをストアド プロシージャに渡します。</span><span class="sxs-lookup"><span data-stu-id="d980d-122">The following example passes a single parameter to a stored procedure.</span></span> <span data-ttu-id="d980d-123">同様に見えるかもしれませんがこの`String.Format`構文に指定された値がラップされてパラメーターと、生成されたパラメーター名は、where を挿入、`{0}`プレース ホルダーを指定します。</span><span class="sxs-lookup"><span data-stu-id="d980d-123">While this may look like `String.Format` syntax, the supplied value is wrapped in a parameter and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

<span data-ttu-id="d980d-124">これは、同じクエリが EF コア 2.0 以降がサポートされている文字列の補間の構文を使用しています。</span><span class="sxs-lookup"><span data-stu-id="d980d-124">This is the same query but using string interpolation syntax, which is supported in EF Core 2.0 and above:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

<span data-ttu-id="d980d-125">また、DbParameter を構築し、パラメーター値として指定できます。</span><span class="sxs-lookup"><span data-stu-id="d980d-125">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="d980d-126">これにより、SQL クエリ文字列の名前付きパラメーターを使用するには</span><span class="sxs-lookup"><span data-stu-id="d980d-126">This allows you to use named parameters in the SQL query string</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

## <a name="composing-with-linq"></a><span data-ttu-id="d980d-127">LINQ で構成します。</span><span class="sxs-lookup"><span data-stu-id="d980d-127">Composing with LINQ</span></span>

<span data-ttu-id="d980d-128">場合は、データベース内の SQL クエリを組み込むことのできる、LINQ の演算子を使用して最初の生の SQL クエリの上に作成することができます。</span><span class="sxs-lookup"><span data-stu-id="d980d-128">If the SQL query can be composed on in the database, then you can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="d980d-129">SQL クエリの中で構成できる、`SELECT`キーワード。</span><span class="sxs-lookup"><span data-stu-id="d980d-129">SQL queries that can be composed on being with the `SELECT` keyword.</span></span>

<span data-ttu-id="d980d-130">次の例では、LINQ を使用してフィルター処理および並べ替えを実行することでから、テーブル値関数 (TVF) を選択し、合成を生 SQL クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="d980d-130">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF) and then composes on it using LINQ to perform filtering and sorting.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

### <a name="including-related-data"></a><span data-ttu-id="d980d-131">関連するデータを含む</span><span class="sxs-lookup"><span data-stu-id="d980d-131">Including related data</span></span>

<span data-ttu-id="d980d-132">クエリに関連するデータを含める LINQ 演算子を含む構成を使用できます。</span><span class="sxs-lookup"><span data-stu-id="d980d-132">Composing with LINQ operators can be used to include related data in the query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

> [!WARNING]  
> <span data-ttu-id="d980d-133">**常に生の SQL クエリをパラメーター化を使用:**生 SQL を受け取る Api に文字列など`FromSql`と`ExecuteSqlCommand`パラメーターとして簡単に渡される値を許可します。</span><span class="sxs-lookup"><span data-stu-id="d980d-133">**Always use parameterization for raw SQL queries:** APIs that accept a raw SQL string such as `FromSql` and `ExecuteSqlCommand` allow values to be easily passed as parameters.</span></span> <span data-ttu-id="d980d-134">ユーザーの入力だけでなく、常に生 SQL クエリ/コマンドで使用される任意の値のパラメーター化を使用します。</span><span class="sxs-lookup"><span data-stu-id="d980d-134">In addition to validating user input, always use parameterization for any values used in a raw SQL query/command.</span></span> <span data-ttu-id="d980d-135">使用している文字列の連結するには、SQL インジェクション攻撃を防ぐために任意の入力を検証、クエリ文字列の一部を動的に構築する場合</span><span class="sxs-lookup"><span data-stu-id="d980d-135">If you are using string concatenation to dynamically build any part of the query string then you are responsible for validating any input to protect against SQL injection attacks.</span></span>
