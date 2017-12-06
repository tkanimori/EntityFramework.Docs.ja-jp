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
# <a name="client-vs-server-evaluation"></a>クライアントとします。サーバーの評価

Entity Framework のコアには、クライアントとのデータベースにプッシュされる部分で評価されるクエリの一部がサポートされています。 クエリのどの部分は、データベースで評価されるかどうか、データベース プロバイダーの責任です。

> [!TIP]  
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)GitHub でします。

## <a name="client-evaluation"></a>クライアントの評価

次の例では、ヘルパー メソッドを SQL Server データベースから返されるブログの Url を標準化するために使用されます。 SQL Server プロバイダーにこのメソッドを実装する方法に関する洞察があるないために、SQL に変換することはできません。 クエリの他のすべての側面が評価される、データベースで、渡す、返された`URL`このメソッドでは、クライアント上で実行されます。

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

## <a name="disabling-client-evaluation"></a>クライアントの評価を無効にします。

クライアントの評価は、場合によってはパフォーマンスが低下する可能性が非常に便利ですが。 ヘルパー メソッドが現在使用されているフィルターで、次のクエリを検討してください。 これは、データベースで実行することはできません、ため、クライアントでデータがメモリとし、フィルターに取り込まれたすべてが適用されます。 データ、およびそのデータの量はフィルターで除外額、に応じてこの結果、パフォーマンスが低下します。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .Where(blog => StandardizeUrl(blog.Url).Contains("dotnet"))
    .ToList();
```

既定では、クライアントの評価が実行されるときに、EF コアは警告を記録します。 参照してください[ログ](../miscellaneous/logging.md)ログ出力を表示する方法の詳細についてです。 スローまたは何もしないクライアントの評価が発生した場合は、動作を変更できます。 では通常、コンテキストでのオプションを設定するときにこれは、 `DbContext.OnConfiguring`、または`Startup.cs`ASP.NET Core を使用している場合。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/ClientEval/ThrowOnClientEval/BloggingContext.cs?highlight=5)] -->
``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
