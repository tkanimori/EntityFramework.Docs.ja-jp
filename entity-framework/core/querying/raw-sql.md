---
title: 生 SQL クエリ - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: 0ad9731840c5f72064f2f66932b9867a0144f437
ms.sourcegitcommit: 2da6f9b05e1ce3a46491e5cc68f17758bdeb6b02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53006870"
---
# <a name="raw-sql-queries"></a>生 SQL クエリ

Entity Framework Core を使用すると、リレーショナル データベースを操作するときに生 SQL クエリにドロップ ダウンすることができます。 この方法は、実行するクエリが LINQ を使用して表現できない場合や、LINQ クエリを使用すると非効率的な SQL が生成される場合に役立ちます。 生 SQL クエリは、エンティティ型か、モデルの一部である[クエリ型](xref:core/modeling/query-types) (EF Core 2.1 以降) を返すことができます。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。

## <a name="limitations"></a>制限事項

生 SQL クエリを使用する場合、注意が必要な制限事項がいくつかあります。

* SQL クエリは、エンティティ型またはクエリ型のすべてのプロパティのデータを返す必要があります。

* 結果セットの列名は、プロパティがマップされている列名と一致する必要があります。 これは EF6 と異なる点です。EF6 では、生 SQL クエリのプロパティ/列のマッピングは無視され、結果セットの列名はプロパティ名と一致する必要がありました。

* SQL クエリに関連データを含めることはできません。 ただし、多くの場合、`Include` 演算子を使用して関連データを返すクエリを作成することができます (「[関連データを含める](#including-related-data)」を参照してください)。

* このメソッドに渡された `SELECT` ステートメントは、一般的にコンポーザブルである必要があります。EF Core がサーバー上で追加のクエリ演算子を評価する必要がある場合 (たとえば、`FromSql` の後に適用される LINQ 演算子を変換する場合)、指定された SQL はサブクエリとして扱われます。 これは、渡される SQL に、次のようなサブクエリでは無効な文字またはオプションを含めてはならないことを意味します。
  * 末尾のセミコロン
  * SQL Server では、末尾のクエリ レベル ヒント (例: `OPTION (HASH JOIN)`)
  * SQL Server では、`SELECT` 句の `TOP 100 PERCENT` を伴わない `ORDER BY` 句

* `SELECT` 以外の SQL ステートメントは、自動的に非コンポーザブルと認識されます。 その結果、ストアド プロシージャのすべての結果が常にクライアントに返され、`FromSql` の後に適用されたすべての LINQ 演算子はメモリ内で評価されます。

## <a name="basic-raw-sql-queries"></a>基本的な生 SQL クエリ

*FromSql* 拡張メソッドを使用して、生 SQL クエリに基づいた LINQ クエリを開始できます。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

生 SQL クエリを使用してストアド プロシージャを実行できます。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a>パラメーターを渡す

SQL を受け取る API の場合と同様に、SQL インジェクション攻撃から保護するには、ユーザー入力をパラメーター化することが重要です。 SQL クエリ文字列にパラメーターのプレースホルダーを含めて、追加の引数としてパラメーター値を指定することができます。 指定したパラメーター値は自動的に `DbParameter` に変換されます。

次の例では、ストアド プロシージャに 1 つのパラメーターを渡しています。 これは `String.Format` 構文のように見えますが、指定された値はパラメーターにラップされ、生成されたパラメーター名は `{0}` プレースホルダーが指定された場所に挿入されます。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

これは同じクエリですが、EF Core 2.0 以降でサポートされている文字列補間構文を使用しています。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

また、DbParameter を構築し、それをパラメーター値として指定することもできます。 こうすることで、SQL クエリ文字列に名前付きパラメーターを使用できます

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

## <a name="composing-with-linq"></a>LINQ による作成

データベース内で SQL クエリを作成できる場合は、LINQ 演算子を使用して初期の生 SQL クエリ上に作成することができます。 `SELECT` キーワードで始まる SQL クエリを作成できます。

次の例では、テーブル値関数 (TVF) から選択し、LINQ を使用してフィルター処理と並べ替えを実行する生 SQL クエリを使用します。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

### <a name="including-related-data"></a>関連データを含める

LINQ 演算子による作成を使用して、関連データをクエリに含めることができます。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

> [!WARNING]  
> **生 SQL クエリには常にパラメーター化を使用する:** `FromSql` や `ExecuteSqlCommand` のような生 SQL 文字列を受け取る API では、値をパラメーターとして簡単に渡すことができます。 ユーザーの入力を検証するだけでなく、生 SQL クエリ/コマンドで使用される値には常にパラメーター化を使用してください。 文字列の連結を使用してクエリ文字列の一部を動的に構築する場合は、SQL インジェクション攻撃から保護するために入力を検証する必要があります。
