---
title: 生 SQL クエリ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: d8f52edfdf4bd7776ab8d81185c867cbfd7bcf44
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813598"
---
# <a name="raw-sql-queries"></a>生 SQL クエリ

Entity Framework Core を使用すると、リレーショナル データベースを操作するときに生 SQL クエリにドロップ ダウンすることができます。 この方法は、実行するクエリが LINQ を使用して表現できない場合や、LINQ クエリを使用すると非効率的な SQL が生成される場合に役立ちます。 生 SQL クエリは、通常のエンティティ型か、モデルの一部である[キーレス エンティティ型](xref:core/modeling/keyless-entity-types)を返すことができます。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/Querying/RawSQL/Sample.cs)は GitHub で確認できます。

## <a name="basic-raw-sql-queries"></a>基本的な生 SQL クエリ

`FromSqlRaw` 拡張メソッドを使用して、生 SQL クエリに基づいた LINQ クエリを開始できます。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSqlRaw("SELECT * FROM dbo.Blogs")
    .ToList();
```

生 SQL クエリを使用してストアド プロシージャを実行できます。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a>パラメーターを渡す

> [!WARNING]
> **生 SQL クエリには常にパラメーター化を使用する**
>
> 生 SQL クエリにユーザー指定の値を採用するときは、SQL インジェクション攻撃を防ぐための注意を払う必要があります。 そのような値に無効な文字が含まれていないことを検証するだけでなく、SQL テキストとは別に値を送信するパラメーター化を常に使用してください。
>
> 特に、未検証のユーザー指定の値を含む連結された文字列や補間された文字列 (`$""`) を、`FromSqlRaw` または `ExecuteSqlRaw` に渡さないでください。 `FromSqlInterpolated` と `ExecuteSqlInterpolated` のメソッドでは、SQL インジェクション攻撃から保護されるように文字列補間構文を使用できます。

次の例では、パラメーターのプレースホルダーを SQL クエリ文字列に含め、追加の引数を指定することによって、ストアド プロシージャに 1 つのパラメーターを渡しています。 これは `String.Format` 構文のように見えますが、指定された値は `DbParameter` にラップされ、生成されたパラメーター名は `{0}` プレースホルダーが指定された場所に挿入されます。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

文字列補間を安全に使用できるように、`FromSqlRaw` の代わりに `FromSqlInterpolated` を使用することもできます。 前の例と同様に、値は `DbParameter` に変換されるため、SQL インジェクションに対して脆弱ではありません。

> [!NOTE]
> バージョン 3.0 より前では、`FromSqlRaw` と `FromSqlInterpolated` は、`FromSql` という名前の 2 つのオーバーロードでした。 詳細については、[前のバージョンのセクション](#previous-versions)を参照してください。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSqlInterpolated($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

また、DbParameter を構築し、それをパラメーター値として指定することもできます。 文字列のプレースホルダーではなく、通常の SQL パラメーターのプレースホルダーが使用されるため、`FromSqlRaw` を安全に使用できます。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

これにより、SQL クエリ文字列で名前付きパラメーターを使用できるようになります。これはストアド プロシージャに省略可能なパラメーターがある場合に便利です。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogs @filterByUser=@user", user)
    .ToList();
```

## <a name="composing-with-linq"></a>LINQ による作成

データベース内で SQL クエリを作成できる場合は、LINQ 演算子を使用して初期の生 SQL クエリ上に作成することができます。 `SELECT` キーワードで始まる SQL クエリを作成できます。

次の例では、テーブル値関数 (TVF) から選択し、LINQ を使用してフィルター処理と並べ替えを実行する生 SQL クエリを使用します。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

これにより、次の SQL クエリが生成されます。

``` sql
SELECT [b].[Id], [b].[Name], [b].[Rating]
        FROM (
            SELECT * FROM dbo.SearchBlogs(@p0)
        ) AS b
        WHERE b."Rating" > 3
        ORDER BY b."Rating" DESC
```

## <a name="change-tracking"></a>変更追跡

`FromSql` メソッドを使用するクエリは、EF Core 内の他の LINQ クエリとまったく同じ変更追跡ルールに従います。 たとえば、クエリでエンティティ型を予測する場合、既定で結果は追跡されます。

次の例では、テーブル値関数 (TVF) から選択し、`AsNoTracking` の呼び出しを使用して変更追跡を無効にする生 SQL クエリを使用しています。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Query<SearchBlogsDto>()
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .AsNoTracking()
    .ToList();
```

## <a name="including-related-data"></a>関連データを含める

`Include` メソッドを使用して、他の LINQ クエリと同様に、関連データを含めることができます。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

この場合、生 SQL クエリがコンポーザブルである必要があることに注意してください。特に、ストアド プロシージャの呼び出しでは機能しません。 「[制限事項](#limitations)」のコンポーザビリティに関するメモを参照してください。

## <a name="limitations"></a>制限事項

生 SQL クエリを使用する場合、注意が必要な制限事項がいくつかあります。

* SQL クエリは、エンティティ型のすべてのプロパティのデータを返す必要があります。

* 結果セットの列名は、プロパティがマップされている列名と一致する必要があります。 これは EF6 と異なる点です。EF6 では、生 SQL クエリのプロパティ/列のマッピングは無視され、結果セットの列名はプロパティ名と一致する必要がありました。

* SQL クエリに関連データを含めることはできません。 ただし、多くの場合、`Include` 演算子を使用して関連データを返すクエリを作成することができます (「[関連データを含める](#including-related-data)」を参照してください)。

* このメソッドに渡された `SELECT` ステートメントは、一般的にコンポーザブルである必要があります。EF Core がサーバー上で追加のクエリ演算子を評価する必要がある場合 (たとえば、`FromSql` メソッドの後に適用される LINQ 演算子を変換する場合)、指定された SQL はサブクエリとして扱われます。 これは、渡される SQL に、次のようなサブクエリでは無効な文字またはオプションを含めてはならないことを意味します。
  * 末尾のセミコロン
  * SQL Server では、末尾のクエリ レベル ヒント (例: `OPTION (HASH JOIN)`)
  * SQL Server で、`SELECT` 句の `OFFSET 0` または `TOP 100 PERCENT` を伴わない `ORDER BY` 句

* SQL Server ではストアド プロシージャ呼び出しを構成することができないため、そのような呼び出しに追加のクエリ演算子を適用しようとすると、無効な SQL が発生します。 クエリ演算子は、クライアント評価のため、`AsEnumerable()` の後に導入される場合があります。

## <a name="previous-versions"></a>以前のバージョン

EF Core バージョン 2.2 以前では、`FromSql` という名前の 2 つのオーバーロードがありました。これは新しい `FromSqlRaw` や `FromSqlInterpolated` と同じように動作しました。 この場合、挿入文字列メソッドを呼び出すつもりが、誤って raw 文字列メソッドを非常に簡単に呼び出せてしまいます。その逆も同様です。 これは、クエリをパラメーター化する必要があるときに、パラメーター化されない結果になる場合があります。
