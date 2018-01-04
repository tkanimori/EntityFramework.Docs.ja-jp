---
title: "生の SQL クエリの EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
ms.technology: entity-framework-core
uid: core/querying/raw-sql
ms.openlocfilehash: 79894c7b9fd9e40cdf14460abf5d872ee2f4b9f0
ms.sourcegitcommit: ced2637bf8cc5964c6daa6c7fcfce501bf9ef6e8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2017
---
# <a name="raw-sql-queries"></a>生の SQL クエリ

Entity Framework Core では、リレーショナル データベースを使用する場合、生の SQL クエリをドロップ ダウンすることができます。 実行するクエリは、LINQ を使用して表現できない場合、またはデータベースに送信される非効率的な SQL での LINQ クエリを使用するが結果として得られる場合、これは役立つあります。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。

## <a name="limitations"></a>制限事項

いくつかのような制限の生の SQL クエリを使用する場合があります。
* SQL クエリは、モデルの一部であるエンティティ型を返すにのみ使用できます。 バックログに機能強化が[生の SQL クエリからアドホック型を返す有効](https://github.com/aspnet/EntityFramework/issues/1862)です。

* SQL クエリでは、エンティティ型のすべてのプロパティのデータを返す必要があります。

* 結果セット内の列名は、プロパティにマップする列名と一致する必要があります。 これとは異なる SQL クエリの生のプロパティ/列マッピングは無視されました、結果セット列名は、プロパティの名前と一致する必要がある EF6 に注意してください。

* SQL クエリでは、関連するデータを含めることはできません。 ただし、多くの場合を組み込むことができますを使用して、クエリの上に、`Include`関連データを返す演算子 (を参照してください[関連データを含む](#including-related-data))。

* `SELECT`このメソッドに渡されたステートメント一般的には、コンポーザブル: EF 中核となる場合は、サーバー上の他のクエリ演算子を評価する必要があります (例: 変換 LINQ 演算子の後に適用する`FromSql`)、指定された SQL はサブクエリとして扱われます。 これは、渡された SQL では、文字またはなど、サブクエリに無効なオプションを含める必要がありますいないことを意味します。
  * 最後のセミコロン
  * SQL Server で、後続のクエリ レベル ヒント、例。`OPTION (HASH JOIN)`
  * SQL Server 上、`ORDER BY`の伴わない句`TOP 100 PERCENT`で、`SELECT`句

* 以外の SQL ステートメント`SELECT`コンポーザブルでないとして自動的に認識されます。 その結果、ストアド プロシージャの完全な結果は常に、クライアントに返されます、後に、LINQ 演算子が適用される`FromSql`メモリ内に評価されます。 

## <a name="basic-raw-sql-queries"></a>基本的な生 SQL クエリ

使用することができます、 *FromSql*生 SQL クエリに基づく LINQ クエリを開始する拡張メソッド。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

生の SQL クエリは、ストアド プロシージャの実行に使用できます。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a>パラメーターを渡す

SQL を受け入れる任意の API と同様には、ユーザーの SQL インジェクション攻撃を防ぐために入力をパラメーター化する必要があります。 SQL クエリ文字列にパラメーターのプレース ホルダーを含めるし、追加の引数とパラメーター値を指定できます。 指定するパラメーター値が自動的に変換する、`DbParameter`です。

次の例では、1 つのパラメーターをストアド プロシージャに渡します。 同様に見えるかもしれませんがこの`String.Format`構文に指定された値がラップされてパラメーターと、生成されたパラメーター名は、where を挿入、`{0}`プレース ホルダーを指定します。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

これは、同じクエリが EF コア 2.0 以降がサポートされている文字列の補間の構文を使用しています。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

また、DbParameter を構築し、パラメーター値として指定できます。 これにより、SQL クエリ文字列の名前付きパラメーターを使用するには

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

## <a name="composing-with-linq"></a>LINQ で構成します。

場合は、データベース内の SQL クエリを組み込むことのできる、LINQ の演算子を使用して最初の生の SQL クエリの上に作成することができます。 SQL クエリの中で構成できる、`SELECT`キーワード。

次の例では、LINQ を使用してフィルター処理および並べ替えを実行することでから、テーブル値関数 (TVF) を選択し、合成を生 SQL クエリを使用します。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

### <a name="including-related-data"></a>関連するデータを含む

クエリに関連するデータを含める LINQ 演算子を含む構成を使用できます。

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

> [!WARNING]  
> **常に生の SQL クエリをパラメーター化を使用:**生 SQL を受け取る Api に文字列など`FromSql`と`ExecuteSqlCommand`パラメーターとして簡単に渡される値を許可します。 ユーザーの入力だけでなく、常に生 SQL クエリ/コマンドで使用される任意の値のパラメーター化を使用します。 使用している文字列の連結するには、SQL インジェクション攻撃を防ぐために任意の入力を検証、クエリ文字列の一部を動的に構築する場合
