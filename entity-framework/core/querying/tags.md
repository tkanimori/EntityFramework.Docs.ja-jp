---
title: クエリのタグ - EF Core
description: Entity Framework Core によって出力されるログ メッセージ内の特定のクエリを識別しやすくすることを目的とした、クエリのタグの使用
author: smitpatel
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: f7cd3558682b1c19e03fc6d04957c7112e870734
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065733"
---
# <a name="query-tags"></a>クエリのタグ

クエリのタグは、コード内の LINQ クエリを、ログでキャプチャされる生成済みの SQL クエリと関連付けるのに役立ちます。
新しい `TagWith()` メソッドを使用して LINQ クエリに注釈を付けます。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags)は GitHub で確認できます。

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

この LINQ クエリは、次の SQL ステートメントに変換されます。

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

同じクエリに対して何度も `TagWith()` を呼び出すことが可能です。
クエリのタグは累積されます。
たとえば、次のようなメソッドがあるとします。

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

次のクエリ:

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

これは次のように変換されます。

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

クエリのタグとして複数行の文字列を使用することも可能です。
次に例を示します。

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

これは次の SQL を生成します。

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>既知の制限事項

**クエリのタグはパラ―メーター化できません:** EF Core では、LINQ クエリ内のクエリのタグが常に、生成される SQL に含まれる文字列リテラルとして扱われます。
コンパイルされたクエリがパラメーターとしてクエリのタグを受け取ることはできません。
