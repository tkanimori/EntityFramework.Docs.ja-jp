---
title: クエリのタグ - EF Core
description: Entity Framework Core によって出力されるログ メッセージ内の特定のクエリを識別しやすくすることを目的とした、クエリのタグの使用
author: divega
ms.date: 11/14/2018
ms.assetid: 73C7A627-C8E9-452D-9CD5-AFCC8FEFE395
uid: core/querying/tags
ms.openlocfilehash: bf80057d29160bd5ef6ae26ee6d447fc3c3f0fec
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617484"
---
# <a name="query-tags"></a><span data-ttu-id="b6950-103">クエリのタグ</span><span class="sxs-lookup"><span data-stu-id="b6950-103">Query tags</span></span>

> [!NOTE]
> <span data-ttu-id="b6950-104">これは EF Core 2.2 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="b6950-104">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="b6950-105">この機能は、コード内の LINQ クエリを、ログでキャプチャされる生成済みの SQL クエリと関連付けるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="b6950-105">This feature helps correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="b6950-106">新しい `TagWith()` メソッドを使用して LINQ クエリに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="b6950-106">You annotate a LINQ query using the new `TagWith()` method:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="b6950-107">この LINQ クエリは、次の SQL ステートメントに変換されます。</span><span class="sxs-lookup"><span data-stu-id="b6950-107">This LINQ query is translated to the following SQL statement:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="b6950-108">同じクエリに対して何度も `TagWith()` を呼び出すことが可能です。</span><span class="sxs-lookup"><span data-stu-id="b6950-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="b6950-109">クエリのタグは累積されます。</span><span class="sxs-lookup"><span data-stu-id="b6950-109">Query tags are cumulative.</span></span>
<span data-ttu-id="b6950-110">たとえば、次のようなメソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="b6950-110">For example, given the following methods:</span></span>

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

<span data-ttu-id="b6950-111">次のクエリ:</span><span class="sxs-lookup"><span data-stu-id="b6950-111">The following query:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

<span data-ttu-id="b6950-112">これは次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="b6950-112">Translates to:</span></span>

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="b6950-113">クエリのタグとして複数行の文字列を使用することも可能です。</span><span class="sxs-lookup"><span data-stu-id="b6950-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="b6950-114">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="b6950-114">For example:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

<span data-ttu-id="b6950-115">これは次の SQL を生成します。</span><span class="sxs-lookup"><span data-stu-id="b6950-115">Produces the following SQL:</span></span>

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="b6950-116">既知の制限事項</span><span class="sxs-lookup"><span data-stu-id="b6950-116">Known limitations</span></span>

<span data-ttu-id="b6950-117">**クエリのタグはパラ―メーター化できません:** EF Core では、LINQ クエリ内のクエリのタグが常に、生成される SQL に含まれる文字列リテラルとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="b6950-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="b6950-118">コンパイルされたクエリがパラメーターとしてクエリのタグを受け取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="b6950-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
