---
title: クエリのタグ - EF Core
description: Entity Framework Core によって出力されるログ メッセージ内の特定のクエリを識別しやすくすることを目的とした、クエリのタグの使用
author: divega
ms.date: 11/14/2018
uid: core/querying/tags
ms.openlocfilehash: 27f757f4159a36bec324cce56d74b7860e1c3741
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070992"
---
# <a name="query-tags"></a>クエリのタグ

> [!NOTE]
> これは EF Core 2.2 の新機能です。

この機能は、コード内の LINQ クエリを、ログでキャプチャされる生成済みの SQL クエリと関連付けるのに役立ちます。
新しい `TagWith()` メソッドを使用して LINQ クエリに注釈を付けます。

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

この LINQ クエリは、次の SQL ステートメントに変換されます。

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

同じクエリに対して何度も `TagWith()` を呼び出すことが可能です。
クエリのタグは累積されます。
たとえば、次のようなメソッドがあるとします。

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

次のクエリ:

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

これは次のように変換されます。

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

クエリのタグとして複数行の文字列を使用することも可能です。
次に例を示します。

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

これは次の SQL を生成します。

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a>既知の制限事項

**クエリのタグはパラ―メーター化できません:** EF Core では、LINQ クエリ内のクエリのタグが常に、生成される SQL に含まれる文字列リテラルとして扱われます。
コンパイルされたクエリがパラメーターとしてクエリのタグを受け取ることはできません。
