---
title: クエリ内の null 値との比較
description: Entity Framework Core でクエリ内の null 値の比較を処理する方法に関する情報
author: maumar
ms.date: 11/11/2020
uid: core/querying/null-comparisons
ms.openlocfilehash: fc63d0e0e6aea09e46b1700152312d4b74270219
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983353"
---
# <a name="query-null-semantics"></a>クエリの null セマンティクス

## <a name="introduction"></a>はじめに

SQL データベースで比較が実行される場合、その動作は、C# のブール型ロジックではなく、3 値論理 (`true`、`false`、`null`) に基づいたものとなります。 LINQ クエリを SQL に変換する場合は、EF Core によって、クエリの一部の要素に対して追加の null チェックが導入されて、差異の補正が試みられます。
これを説明するために、次のエンティティを定義しましょう。

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsEntity.cs#Entity)]

さらに、いくつかのクエリを発行します。

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#BasicExamples)]

最初の 2 つのクエリによって行われるのは、シンプルな比較です。 最初のクエリでは、両方の列が null 非許容であるため、null チェックは必要ありません。 2 番目のクエリでは、`NullableInt` に `null` を含めることはできますが、`Id` は null 非許容です。`null` と null 値以外を比較すると、結果として `null` が生成されます。これは `WHERE` 操作によって除外されます。 そのため、どちらにも追加の条件は必要ありません。

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[Int]

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[Id] = [e].[NullableInt]
```

3 番目のクエリには、null チェックが導入されています。 `NullableInt` が `null` である場合、比較 `Id <> NullableInt` を行うと `null` が生成されます。これは `WHERE` 操作によって除外されます。 ただし、ブール型のロジックの観点からは、この例は結果の一部として返される必要があります。 したがって、それを確実なものにするために、EF Core によって必要なチェックが追加されます。

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[Id] <> [e].[NullableInt]) OR [e].[NullableInt] IS NULL
```

4 番目と 5 番目のクエリで示されているのは、両方の列が null 許容である場合のパターンです。 注目すべき点は、`<>` 操作を実行すると、`==` 操作よりも複雑な (そして遅くなる可能性がある) クエリが生成されるということです。

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] = [e].[String2]) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL)

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE (([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)
```

## <a name="treatment-of-nullable-values-in-functions"></a>関数での null 許容値の処理

引数の一部が `null` である場合、SQL の関数の多くで `null` の結果のみを返すことができます。 EF Core ではこれを活用して、より効率的なクエリを生成します。
次のクエリで最適化の例を示します。

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#Functions)]

生成される SQL は次のようになります (`SUBSTRING` 関数を評価する必要はありません。引数のいずれかが null 値である場合は null 値にしかならないからです)。

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE [e].[String1] IS NULL OR [e].[String2] IS NULL
```

最適化はユーザー定義関数にも使用できます。 詳細については、「[ユーザー定義関数のマッピング](xref:core/querying/user-defined-function-mapping#configuring-nullability-of-user-defined-function-based-on-its-arguments)」ページを参照してください。

## <a name="writing-performant-queries"></a>パフォーマンスの高いクエリの作成

- null 非許容列の比較は、Null 許容列の比較よりもシンプルで高速です。 可能な限り、列を null 非許容としてマークすることを検討してください。

- 等価性 (`==`) のチェックは、非等価性 (`!=`) のチェックよりもシンプルで高速です。`null` と `false` の結果をクエリで区別する必要がないからです。 可能な場合は、等価比較を使用してください。 ただし、単に `==` 比較を否定することは事実上 `!=` と同じであるため、パフォーマンスの向上にはつながりません。

- 場合によっては、列から `null` 値を明示的に除外することによって複雑な比較を簡略化することができます。たとえば、`null` 値が存在しない場合や、これらの値が結果に関係しない場合などです。 次に例を示します。

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/Program.cs#ManualOptimization)]

これらのクエリにより、次の SQL が生成されます。

```sql
SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ((([e].[String1] <> [e].[String2]) OR ([e].[String1] IS NULL OR [e].[String2] IS NULL)) AND ([e].[String1] IS NOT NULL OR [e].[String2] IS NOT NULL)) OR ((CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)) OR ([e].[String1] IS NULL AND [e].[String2] IS NULL))

SELECT [e].[Id], [e].[Int], [e].[NullableInt], [e].[String1], [e].[String2]
FROM [Entities] AS [e]
WHERE ([e].[String1] IS NOT NULL AND [e].[String2] IS NOT NULL) AND (([e].[String1] <> [e].[String2]) OR (CAST(LEN([e].[String1]) AS int) = CAST(LEN([e].[String2]) AS int)))
```

2 番目のクエリでは、`null` の結果が `String1` 列から明示的に除外されています。 EF Core により、比較中は `String1` 列が null 非許容として確実に処理されるので、クエリが簡略化されます。

## <a name="using-relational-null-semantics"></a>リレーショナル null セマンティクスの使用

null 値の比較補正を無効にして、リレーショナル null セマンティクスを直接使用することができます。 これを行うには、`OnConfiguring` メソッド内のオプション ビルダー上で `UseRelationalNulls(true)` メソッドを呼び出します。

[!code-csharp[Main](../../../samples/core/Querying/NullSemantics/NullSemanticsContext.cs#UseRelationalNulls)]

> [!WARNING]
> リレーショナル null セマンティクスを使用すると、LINQ クエリは C# の場合と同じ意味を持たなくなり、予想とは異なる結果が生成される可能性があります。 このモードを使用する際は注意が必要です。
