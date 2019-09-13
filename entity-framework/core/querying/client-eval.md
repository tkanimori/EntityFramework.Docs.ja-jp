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
# <a name="client-vs-server-evaluation"></a>クライアントとサーバーの評価

Entity Framework Core では、クライアント上でクエリの一部を評価し、一部をデータベースにプッシュする機能をサポートしています。 クエリのどの部分がデータベースで評価されるかの識別は、データベース プロバイダーが行います。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。

## <a name="client-evaluation"></a>クライアントの評価

次の例では、SQL Server データベースから返されるブログの URL を標準化するために、ヘルパー メソッドが使用されています。 SQL Server プロバイダーはこのメソッドの実装方法に関する分析情報を保持していないため、このメソッドを SQL に変換することはできません。 クエリの他の側面はすべてデータベースで評価されますが、このメソッド経由で返された `URL` の受け渡しは、クライアント上で実行されます。

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

## <a name="client-evaluation-performance-issues"></a>クライアントの評価に伴うパフォーマンスの問題

クライアントの評価は非常に便利な場合もありますが、一部の例では、パフォーマンスが低下する恐れがあります。 次のクエリを検討してください。このクエリでは現在、ヘルパー メソッドがフィルター内で使用されています。 データベース内ではこれを実行できないので、すべてのデータがメモリ内にプルされて、クライアント上でフィルターが適用されます。 データ量と、そのデータがフィルターで除外される量によっては、このことがパフォーマンス低下につながります。

<!-- [!code-csharp[Main](samples/core/Querying/ClientEval/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .Where(blog => StandardizeUrl(blog.Url).Contains("dotnet"))
    .ToList();
```

## <a name="client-evaluation-logging"></a>クライアントの評価の記録

既定では、クライアントの評価が実行されると、EF Core は警告を記録します。 ログ記録の出力の表示に関する詳細については、[ログ記録](../miscellaneous/logging.md)に関するページを参照してください。 

## <a name="optional-behavior-throw-an-exception-for-client-evaluation"></a>オプションの動作: クライアントの評価の例外をスローする

クライアントの評価が行われる場合の動作を、エラーをスローするか、または何もしないかに変更できます。 ASP.NET Core を使用している場合、通常は `DbContext.OnConfiguring` または `Startup.cs` でコンテキストのオプションを設定するときに、この変更が行われます。

<!-- [!code-csharp[Main](samples/core/Querying/ClientEval/ThrowOnClientEval/BloggingContext.cs?highlight=5)] -->
``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
