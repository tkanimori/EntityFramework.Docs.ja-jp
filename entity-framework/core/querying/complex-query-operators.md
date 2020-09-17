---
title: 複雑なクエリ演算子 - EF Core
description: Entity Framework Core を使用する場合の複雑な LINQ クエリ演算子に関する詳細情報
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/complex-query-operators
ms.openlocfilehash: 57157fa1593c9e5fe54e5fbe6b2c58eca3d4b0e7
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071161"
---
# <a name="complex-query-operators"></a>複雑なクエリ演算子

統合言語クエリ (LINQ) には、複数のデータ ソースを結合したり、複雑な処理を行ったりする、多数の複雑な演算子が含まれています。 すべての LINQ 演算子が、サーバー側で適切に変換されるわけではありません。 あるフォームのクエリがサーバーに変換されることがありますが、結果が同じであっても、別のフォームで記述されている場合は変換されません。 このページでは、複雑な演算子とそのサポートされるバリエーションについていくつか説明します。 今後のリリースで、さらに多くのパターンを認識し、それらに対応する変換を追加する可能性があります。 また、変換のサポートはプロバイダーによって異なることに注意することが重要です。 SqlServer で変換される特定のクエリは、SQLite データベースでは機能しない場合があります。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。

## <a name="join"></a>Join

LINQ Join 演算子を使用すると、各ソースのキー セレクターに基づいて 2 つのデータ ソースを接続でき、キーが一致したときに値のタプルが生成されます。 リレーショナル データベースでは、必然的に `INNER JOIN` に変換されます。 LINQ Join には外部および内部のキー セレクターがありますが、データベースには単一の結合条件が必要です。 したがって、EF Core では、外部キー セレクターと内部キー セレクターが等価であるかどうかを比較して、結合条件を生成します。 さらに、キー セレクターが匿名型である場合、EF Core では、等価要素ごとに比較する結合条件を生成します。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#Join)]

```SQL
SELECT [p].[PersonId], [p].[Name], [p].[PhotoId], [p0].[PersonPhotoId], [p0].[Caption], [p0].[Photo]
FROM [PersonPhoto] AS [p0]
INNER JOIN [Person] AS [p] ON [p0].[PersonPhotoId] = [p].[PhotoId]
```

## <a name="groupjoin"></a>GroupJoin

LINQ GroupJoin 演算子では、Join の場合と同じように 2 つのデータ ソースを接続できますが、外部要素と一致するように内部値のグループが作成されます。 次の例のようなクエリを実行すると、`Blog` & `IEnumerable<Post>` の結果が生成されます。 データベース (特にリレーショナル データベース) にはクライアント側のオブジェクトのコレクションを表す方法がないため、多くの場合、GroupJoin はサーバーに変換されません。 サーバーからすべてのデータを取得し、特別なセレクターを使用せずに GroupJoin を実行する必要があります (以下の最初のクエリ)。 しかし、セレクターでデータの選択が制限されている場合は、サーバーからすべてのデータをフェッチすると、パフォーマンスの問題が発生する可能性があります (以下の 2 番目のクエリ)。 そのため、EF Core では GroupJoin が変換されません。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoin)]

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupJoinComposed)]

## <a name="selectmany"></a>SelectMany

LINQ SelectMany 演算子を使用すると、外部の各要素のコレクション セレクターを列挙し、各データ ソースから値のタプルを生成することができます。 ある意味、これは結合ですが、どの条件も含まれていないため、すべての外部要素がコレクション ソースからの要素に接続されます。 コレクション セレクターが外部データ ソースにどのように関連しているかによって、SelectMany はサーバー側でさまざまなクエリに変換することができます。

### <a name="collection-selector-doesnt-reference-outer"></a>コレクション セレクターで外部参照されない

コレクション セレクターで外部ソースから何も参照されていない場合、結果は両方のデータ ソースのデカルト積になります。 リレーショナル データベースでは `CROSS JOIN` に変換されます。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToCrossJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
CROSS JOIN [Posts] AS [p]
```

### <a name="collection-selector-references-outer-in-a-where-clause"></a>where 句でのコレクション セレクターによる外部参照

コレクション セレクターに where 句があり、外部要素が参照される場合、EF Core ではそれがデータベース結合に変換され、述語が結合条件として使用されます。 通常、このケースは、外部要素のコレクション ナビゲーションをコレクション セレクターとして使用する場合に発生します。 外部要素のコレクションが空の場合、その外部要素の結果は生成されません。 しかし、コレクション セレクターに `DefaultIfEmpty` が適用されている場合、外部要素は内部要素の既定値と接続されます。 このような違いから、この種のクエリは、`DefaultIfEmpty` と `LEFT JOIN` がない場合、および `DefaultIfEmpty` が適用されている場合に `INNER JOIN` に変換されます。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
INNER JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

### <a name="collection-selector-references-outer-in-a-non-where-case"></a>where 以外のケースでのコレクション セレクターによる外部参照

コレクション セレクターで、(上記のケースのように) where 句には含まれていない外部要素を参照する場合、データベース結合には変換されません。 そのため、外部の各要素のコレクション セレクターを評価する必要があります。 多くのリレーショナル データベースでは `APPLY` 演算に変換されます。 外部要素のコレクションが空の場合、その外部要素の結果は生成されません。 しかし、コレクション セレクターに `DefaultIfEmpty` が適用されている場合、外部要素は内部要素の既定値と接続されます。 このような違いから、この種のクエリは、`DefaultIfEmpty` と `OUTER APPLY` がない場合、および `DefaultIfEmpty` が適用されている場合に `CROSS APPLY` に変換されます。 SQLite のような特定のデータベースでは `APPLY` 演算子がサポートされないため、この種のクエリが変換されない場合があります。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#SelectManyConvertedToApply)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
CROSS APPLY [Posts] AS [p]

SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], ([b].[Url] + N'=>') + [p].[Title] AS [p]
FROM [Blogs] AS [b]
OUTER APPLY [Posts] AS [p]
```

## <a name="groupby"></a>GroupBy

LINQ GroupBy 演算子では、`IGrouping<TKey, TElement>` 型の結果が作成されす。この場合、`TKey` と `TElement` は任意の型にすることができます。 さらに、`IGrouping` では `IEnumerable<TElement>` が実装されます。これは、グループ化の後に LINQ 演算子を使用して、それを構成できることを意味します。 データベース構造では `IGrouping` を表すことができないため、ほとんどの場合、GroupBy 演算子は変換されません。 各グループに対して、スカラーを返す集約演算子が適用されている場合、リレーショナル データベースでは SQL `GROUP BY` に変換できます。 SQL `GROUP BY` も制限されます。 スカラー値でのみグループ化する必要があります。 プロジェクションには、列に適用される集約またはグループ化キー列のみを含めることができます。 EF Core では、次の例に示すように、このパターンを識別してサーバーに変換します。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupBy)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
```

また、EF Core では、Where または OrderBy (あるいはその他の順序付け) LINQ 演算子で、グループ化の集約演算子が表示されるクエリを変換します。 where 句には、SQL の `HAVING` 句が使用されます。 GroupBy 演算子を適用する前のクエリの部分は、サーバーに変換できる限り、複雑なクエリにすることができます。 さらに、グループ化クエリに集約演算子を適用して、結果のソースからグループ化を削除したら、他のクエリと同様に、それに基づいて構成することができます。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#GroupByFilter)]

```SQL
SELECT [p].[AuthorId] AS [Key], COUNT(*) AS [Count]
FROM [Posts] AS [p]
GROUP BY [p].[AuthorId]
HAVING COUNT(*) > 0
ORDER BY [p].[AuthorId]
```

EF Core でサポートされる集約演算子は次のとおりです

- Average
- Count
- LongCount
- Max
- Min
- SUM

## <a name="left-join"></a>Left Join

Left Join は LINQ 演算子ではありませんが、リレーショナル データベースには、クエリで頻繁に使用される Left Join の概念があります。 LINQ クエリの特定のパターンでは、サーバーで `LEFT JOIN` と同じ結果が得られます。 EF Core ではこのようなパターンを識別し、サーバー側で同等の `LEFT JOIN` を生成します。 このパターンでは、両方のデータソース間に GroupJoin を作成し、その後、内部に関連する要素がない場合は null と一致するように、グループ化ソースに対して DefaultIfEmpty を指定した SelectMany 演算子を使用して、グループ化をフラット化します。 次の例では、パターンがどのように見えるかと、何が生成されるかを示します。

[!code-csharp[Main](../../../samples/core/Querying/ComplexQuery/Sample.cs#LeftJoin)]

```SQL
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Posts] AS [p] ON [b].[BlogId] = [p].[BlogId]
```

上のパターンでは、式ツリーに複雑な構造体が作成されます。 そのため、EF Core では、演算子の直後のステップで GroupJoin 演算子のグループ化結果をフラット化する必要があります。 GroupJoin-DefaultIfEmpty-SelectMany が使用されていても、パターンが異なる場合は、Left Join として識別されない可能性があります。
