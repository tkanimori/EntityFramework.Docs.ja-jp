---
title: クライアントとサーバーの評価 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
uid: core/querying/client-eval
ms.openlocfilehash: cb207d9e1b1004a4084dd6fc66712183b5bdd5dc
ms.sourcegitcommit: b2b9468de2cf930687f8b85c3ce54ff8c449f644
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921701"
---
# <a name="client-vs-server-evaluation"></a><span data-ttu-id="82c99-102">クライアントとサーバーの評価</span><span class="sxs-lookup"><span data-stu-id="82c99-102">Client vs. Server Evaluation</span></span>

<span data-ttu-id="82c99-103">Entity Framework Core では、クライアント上でクエリの一部を評価し、一部をデータベースにプッシュする機能をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="82c99-103">Entity Framework Core supports parts of the query being evaluated on the client and parts of it being pushed to the database.</span></span> <span data-ttu-id="82c99-104">クエリのどの部分がデータベースで評価されるかの識別は、データベース プロバイダーが行います。</span><span class="sxs-lookup"><span data-stu-id="82c99-104">It is up to the database provider to determine which parts of the query will be evaluated in the database.</span></span>

> [!TIP]  
> <span data-ttu-id="82c99-105">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="82c99-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

## <a name="client-evaluation"></a><span data-ttu-id="82c99-106">クライアントの評価</span><span class="sxs-lookup"><span data-stu-id="82c99-106">Client evaluation</span></span>

<span data-ttu-id="82c99-107">次の例では、SQL Server データベースから返されるブログの URL を標準化するために、ヘルパー メソッドが使用されています。</span><span class="sxs-lookup"><span data-stu-id="82c99-107">In the following example a helper method is used to standardize URLs for blogs that are returned from a SQL Server database.</span></span> <span data-ttu-id="82c99-108">SQL Server プロバイダーはこのメソッドの実装方法に関する分析情報を保持していないため、このメソッドを SQL に変換することはできません。</span><span class="sxs-lookup"><span data-stu-id="82c99-108">Because the SQL Server provider has no insight into how this method is implemented, it is not possible to translate it into SQL.</span></span> <span data-ttu-id="82c99-109">クエリの他の側面はすべてデータベースで評価されますが、このメソッド経由で返された `URL` の受け渡しは、クライアント上で実行されます。</span><span class="sxs-lookup"><span data-stu-id="82c99-109">All other aspects of the query are evaluated in the database, but passing the returned `URL` through this method is performed on the client.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/ClientEval/Sample.cs?highlight=6)] -->
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

<!-- [!code-csharp[Main](samples/core/Querying/ClientEval/Sample.cs)] -->
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

## <a name="client-evaluation-performance-issues"></a><span data-ttu-id="82c99-110">クライアントの評価に伴うパフォーマンスの問題</span><span class="sxs-lookup"><span data-stu-id="82c99-110">Client evaluation performance issues</span></span>

<span data-ttu-id="82c99-111">クライアントの評価は非常に便利な場合もありますが、一部の例では、パフォーマンスが低下する恐れがあります。</span><span class="sxs-lookup"><span data-stu-id="82c99-111">While client evaluation can be very useful, in some instances it can result in poor performance.</span></span> <span data-ttu-id="82c99-112">次のクエリを検討してください。このクエリでは現在、ヘルパー メソッドがフィルター内で使用されています。</span><span class="sxs-lookup"><span data-stu-id="82c99-112">Consider the following query, where the helper method is now used in a filter.</span></span> <span data-ttu-id="82c99-113">データベース内ではこれを実行できないので、すべてのデータがメモリ内にプルされて、クライアント上でフィルターが適用されます。</span><span class="sxs-lookup"><span data-stu-id="82c99-113">Because this can't be performed in the database, all the data is pulled into memory and then the filter is applied on the client.</span></span> <span data-ttu-id="82c99-114">データ量と、そのデータがフィルターで除外される量によっては、このことがパフォーマンス低下につながります。</span><span class="sxs-lookup"><span data-stu-id="82c99-114">Depending on the amount of data, and how much of that data is filtered out, this could result in poor performance.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/ClientEval/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .Where(blog => StandardizeUrl(blog.Url).Contains("dotnet"))
    .ToList();
```

## <a name="client-evaluation-logging"></a><span data-ttu-id="82c99-115">クライアントの評価の記録</span><span class="sxs-lookup"><span data-stu-id="82c99-115">Client evaluation logging</span></span>

<span data-ttu-id="82c99-116">既定では、クライアントの評価が実行されると、EF Core は警告を記録します。</span><span class="sxs-lookup"><span data-stu-id="82c99-116">By default, EF Core will log a warning when client evaluation is performed.</span></span> <span data-ttu-id="82c99-117">ログ記録の出力の表示に関する詳細については、[ログ記録](../miscellaneous/logging.md)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="82c99-117">See [Logging](../miscellaneous/logging.md) for more information on viewing logging output.</span></span> 

## <a name="optional-behavior-throw-an-exception-for-client-evaluation"></a><span data-ttu-id="82c99-118">オプションの動作: クライアントの評価の例外をスローする</span><span class="sxs-lookup"><span data-stu-id="82c99-118">Optional behavior: throw an exception for client evaluation</span></span>

<span data-ttu-id="82c99-119">クライアントの評価が行われる場合の動作を、エラーをスローするか、または何もしないかに変更できます。</span><span class="sxs-lookup"><span data-stu-id="82c99-119">You can change the behavior when client evaluation occurs to either throw or do nothing.</span></span> <span data-ttu-id="82c99-120">ASP.NET Core を使用している場合、通常は `DbContext.OnConfiguring` または `Startup.cs` でコンテキストのオプションを設定するときに、この変更が行われます。</span><span class="sxs-lookup"><span data-stu-id="82c99-120">This is done when setting up the options for your context - typically in `DbContext.OnConfiguring`, or in `Startup.cs` if you are using ASP.NET Core.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/ClientEval/ThrowOnClientEval/BloggingContext.cs?highlight=5)] -->
``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
