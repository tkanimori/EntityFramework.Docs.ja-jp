---
title: 生 SQL クエリ - EF Core
author: smitpatel
ms.date: 10/08/2019
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: b7087771f1a9e8ee5e044cfea367d74a0b1c1d35
ms.sourcegitcommit: 37d0e0fd1703467918665a64837dc54ad2ec7484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445926"
---
# <a name="raw-sql-queries"></a>生 SQL クエリ

Entity Framework Core を使用すると、リレーショナル データベースを操作するときに生 SQL クエリにドロップ ダウンすることができます。 生 SQL クエリは、必要なクエリが LINQ を使用して表現できない場合に便利です。 また、生 SQL クエリは、LINQ クエリを使うと、効率の悪い SQL クエリになる場合にも使用されます。 生 SQL クエリは、通常のエンティティ型か、モデルの一部である[キーレス エンティティ型](xref:core/modeling/keyless-entity-types)を返すことができます。

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/)は GitHub で確認できます。

## <a name="basic-raw-sql-queries"></a>基本的な生 SQL クエリ

`FromSqlRaw` 拡張メソッドを使用して、生 SQL クエリに基づいた LINQ クエリを開始できます。 `FromSqlRaw` は、`DbSet<>` に直接配置されている、クエリ ルートでのみ使用できます。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRaw)]

生 SQL クエリを使用してストアド プロシージャを実行できます。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedure)]

## <a name="passing-parameters"></a>パラメーターを渡す

> [!WARNING]
> **生 SQL クエリには常にパラメーター化を使用する**
>
> 生 SQL クエリにユーザー指定の値を採用するときは、SQL インジェクション攻撃を防ぐための注意を払う必要があります。 そのような値に無効な文字が含まれていないことを検証するだけでなく、SQL テキストとは別に値を送信するパラメーター化を常に使用してください。
>
> 特に、検証されていないユーザー指定の値を含む連結された文字列や補間された文字列 (`$""`) を、`FromSqlRaw` や `ExecuteSqlRaw` に渡さないでください。 `FromSqlInterpolated` と `ExecuteSqlInterpolated` のメソッドでは、SQL インジェクション攻撃から保護されるように文字列補間構文を使用できます。

次の例では、パラメーターのプレースホルダーを SQL クエリ文字列に含め、追加の引数を指定することによって、ストアド プロシージャに 1 つのパラメーターを渡しています。 この構文は `String.Format` 構文のように見えるかもしれませんが、提供された値は `DbParameter` にラップされ、生成されたパラメーター名は、`{0}` プレースホルダーが指定された場所に挿入されます。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureParameter)]

`FromSqlInterpolated` は `FromSqlRaw` に似ていますが、文字列補間構文を使用できます。 `FromSqlRaw` と同じように、`FromSqlInterpolated` はクエリ ルートでのみ使用できます。 前の例と同様に、値は `DbParameter` に変換され、SQL インジェクションに対して脆弱ではありません。

> [!NOTE]
> バージョン 3.0 より前では、`FromSqlRaw` と `FromSqlInterpolated` は、`FromSql` という名前の 2 つのオーバーロードでした。 詳細については、[以前のバージョンに関するセクション](#previous-versions)を参照してください。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedStoredProcedureParameter)]

また、DbParameter を構築し、それをパラメーター値として指定することもできます。 文字列のプレースホルダーではなく、通常の SQL パラメーターのプレースホルダーが使用されるため、`FromSqlRaw` を安全に使用できます。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureSqlParameter)]

`FromSqlRaw` では、SQL クエリ文字列で名前付きパラメーターを使用できます。これはストアド プロシージャに省略可能なパラメーターがある場合に便利です。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlRawStoredProcedureNamedSqlParameter)]

## <a name="composing-with-linq"></a>LINQ による作成

LINQ 演算子を使用して、最初の生 SQL クエリに基づいて構成することができます。 EF Core では、これをサブクエリとして扱い、データベースで構成します。 次の例では、テーブル値関数 (TVF) から選択する生 SQL クエリを使用します。 その後、LINQ を使用してそれを構成し、フィルター処理と並べ替えを行います。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedComposed)]

上記のクエリでは、次の SQL が生成されます。

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM (
    SELECT * FROM dbo.SearchBlogs(@p0)
) AS [b]
WHERE [b].[Rating] > 3
ORDER BY [b].[Rating] DESC
```

### <a name="including-related-data"></a>関連データを含める

`Include` メソッドを使用して、他の LINQ クエリと同様に、関連データを含めることができます。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedInclude)]

EF Core では提供された SQL がサブクエリとして扱われるため、LINQ を使用して構成するには、生 SQL クエリがコンポーザブルである必要があります。 `SELECT` キーワードで始まる SQL クエリを作成できます。 さらに、次のような、サブクエリでは無効な文字やオプションを、渡される SQL に含めることはできません。

- 末尾のセミコロン
- SQL Server では、末尾のクエリ レベル ヒント (例: `OPTION (HASH JOIN)`)
- SQL Server では、`SELECT` 句の `OFFSET 0` または `TOP 100 PERCENT` と共に使用されない `ORDER BY` 句

SQL Server ではストアド プロシージャ呼び出しを構成することができないため、そのような呼び出しに追加のクエリ演算子を適用しようとすると、無効な SQL が発生します。 EF Core でストアド プロシージャの構成が試行されないようにするには、`AsEnumerable` または `AsAsyncEnumerable` メソッドの直後に `FromSqlRaw` または `FromSqlInterpolated` メソッドを使用します。

## <a name="change-tracking"></a>変更追跡

`FromSqlRaw` または `FromSqlInterpolated` メソッドを使用するクエリでは、EF Core 内の他の LINQ クエリとまったく同じ変更追跡ルールに従います。 たとえば、クエリでエンティティ型を予測する場合、既定で結果は追跡されます。

次の例では、テーブル値関数 (TVF) から選択し、`AsNoTracking` の呼び出しを使用して変更追跡を無効にする生 SQL クエリを使用しています。

[!code-csharp[Main](../../../samples/core/Querying/RawSQL/Sample.cs#FromSqlInterpolatedAsNoTracking)]

## <a name="limitations"></a>制限事項

生 SQL クエリを使用する場合、注意が必要な制限事項がいくつかあります。

- SQL クエリは、エンティティ型のすべてのプロパティのデータを返す必要があります。
- 結果セットの列名は、プロパティがマップされている列名と一致する必要があります。 この動作は EF6 とは異なることに注意してください。 EF6 では、生 SQL クエリのプロパティの列へのマッピングは無視され、結果セットの列名はプロパティ名と一致する必要がありました。
- SQL クエリに関連データを含めることはできません。 ただし、多くの場合、`Include` 演算子を使用して関連データを返すクエリを作成することができます (「[関連データを含める](#including-related-data)」を参照してください)。

## <a name="previous-versions"></a>以前のバージョン

EF Core バージョン 2.2 以前では、`FromSql` というメソッドの 2 つのオーバーロードがありました。これは新しい `FromSqlRaw` や `FromSqlInterpolated` と同じように動作しました。 挿入文字列メソッドを呼び出すつもりが、誤って生文字列メソッドを簡単に呼び出せてしまいました。その逆も同様です。 誤って間違ったオーバーロードを呼び出すと、クエリがパラメーター化される必要があるときにそうならない可能性があります。
