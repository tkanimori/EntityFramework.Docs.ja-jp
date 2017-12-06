---
title: "クライアントとします。サーバー上の評価の EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
ms.technology: entity-framework-core
uid: core/querying/client-eval
ms.openlocfilehash: e1852b780041e9e92fb4d25129175346e3a601a3
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="client-vs-server-evaluation"></a><span data-ttu-id="b2043-102">クライアントとします。サーバーの評価</span><span class="sxs-lookup"><span data-stu-id="b2043-102">Client vs. Server Evaluation</span></span>

<span data-ttu-id="b2043-103">Entity Framework のコアには、クライアントとのデータベースにプッシュされる部分で評価されるクエリの一部がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b2043-103">Entity Framework Core supports parts of the query being evaluated on the client and parts of it being pushed to the database.</span></span> <span data-ttu-id="b2043-104">クエリのどの部分は、データベースで評価されるかどうか、データベース プロバイダーの責任です。</span><span class="sxs-lookup"><span data-stu-id="b2043-104">It is up to the database provider to determine which parts of the query will be evaluated in the database.</span></span>

> [!TIP]  
> <span data-ttu-id="b2043-105">この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)GitHub でします。</span><span class="sxs-lookup"><span data-stu-id="b2043-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="client-evaluation"></a><span data-ttu-id="b2043-106">クライアントの評価</span><span class="sxs-lookup"><span data-stu-id="b2043-106">Client evaluation</span></span>

<span data-ttu-id="b2043-107">次の例では、ヘルパー メソッドを SQL Server データベースから返されるブログの Url を標準化するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="b2043-107">In the following example a helper method is used to standardize URLs for blogs that are returned from a SQL Server database.</span></span> <span data-ttu-id="b2043-108">SQL Server プロバイダーにこのメソッドを実装する方法に関する洞察があるないために、SQL に変換することはできません。</span><span class="sxs-lookup"><span data-stu-id="b2043-108">Because the SQL Server provider has no insight into how this method is implemented, it is not possible to translate it into SQL.</span></span> <span data-ttu-id="b2043-109">クエリの他のすべての側面が評価される、データベースで、渡す、返された`URL`このメソッドでは、クライアント上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="b2043-109">All other aspects of the query are evaluated in the database, but passing the returned `URL` through this method is performed on the client.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/Sample.cs?highlight=6)] -->
``` csharp
var blogs = context.Blogs
    .OrderByDescending(blog => blog.Rating)
    .Select(blog => new
    {
        Id = blog.BlogId,
        Url = StandardizeUrl(blog.Url)
    })
    .ToList();
```

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/Sample.cs)] -->
``` csharp
public static string StandardizeUrl(string url)
{
    url = url.ToLower();

    if (!url.StartsWith("http://"))
    {
        url = string.Concat("http://", url);
    }

    return url;
}
```

## <a name="disabling-client-evaluation"></a><span data-ttu-id="b2043-110">クライアントの評価を無効にします。</span><span class="sxs-lookup"><span data-stu-id="b2043-110">Disabling client evaluation</span></span>

<span data-ttu-id="b2043-111">クライアントの評価は、場合によってはパフォーマンスが低下する可能性が非常に便利ですが。</span><span class="sxs-lookup"><span data-stu-id="b2043-111">While client evaluation can be very useful, in some instances it can result in poor performance.</span></span> <span data-ttu-id="b2043-112">ヘルパー メソッドが現在使用されているフィルターで、次のクエリを検討してください。</span><span class="sxs-lookup"><span data-stu-id="b2043-112">Consider the following query, where the helper method is now used in a filter.</span></span> <span data-ttu-id="b2043-113">これは、データベースで実行することはできません、ため、クライアントでデータがメモリとし、フィルターに取り込まれたすべてが適用されます。</span><span class="sxs-lookup"><span data-stu-id="b2043-113">Because this can't be performed in the database, all the data is pulled into memory and then the filter is applied on the client.</span></span> <span data-ttu-id="b2043-114">データ、およびそのデータの量はフィルターで除外額、に応じてこの結果、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="b2043-114">Depending on the amount of data, and how much of that data is filtered out, this could result in poor performance.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .Where(blog => StandardizeUrl(blog.Url).Contains("dotnet"))
    .ToList();
```

<span data-ttu-id="b2043-115">既定では、クライアントの評価が実行されるときに、EF コアは警告を記録します。</span><span class="sxs-lookup"><span data-stu-id="b2043-115">By default, EF Core will log a warning when client evaluation is performed.</span></span> <span data-ttu-id="b2043-116">参照してください[ログ](../miscellaneous/logging.md)ログ出力を表示する方法の詳細についてです。</span><span class="sxs-lookup"><span data-stu-id="b2043-116">See [Logging](../miscellaneous/logging.md) for more information on viewing logging output.</span></span> <span data-ttu-id="b2043-117">スローまたは何もしないクライアントの評価が発生した場合は、動作を変更できます。</span><span class="sxs-lookup"><span data-stu-id="b2043-117">You can change the behavior when client evaluation occurs to either throw or do nothing.</span></span> <span data-ttu-id="b2043-118">では通常、コンテキストでのオプションを設定するときにこれは、 `DbContext.OnConfiguring`、または`Startup.cs`ASP.NET Core を使用している場合。</span><span class="sxs-lookup"><span data-stu-id="b2043-118">This is done when setting up the options for your context - typically in `DbContext.OnConfiguring`, or in `Startup.cs` if you are using ASP.NET Core.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/ThrowOnClientEval/BloggingContext.cs?highlight=5)] -->
``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
