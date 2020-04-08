---
title: クエリのタグ - EF Core
author: divega
ms.date: 11/14/2018
ms.assetid: 73C7A627-C8E9-452D-9CD5-AFCC8FEFE395
uid: core/querying/tags
ms.openlocfilehash: e8415b237df45ce652dcd152013f4f12a992aed7
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413829"
---
# <a name="query-tags"></a><span data-ttu-id="01d23-102">クエリのタグ</span><span class="sxs-lookup"><span data-stu-id="01d23-102">Query tags</span></span>

> [!NOTE]
> <span data-ttu-id="01d23-103">これは EF Core 2.2 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="01d23-103">This feature is new in EF Core 2.2.</span></span>

<span data-ttu-id="01d23-104">この機能は、コード内の LINQ クエリを、ログでキャプチャされる生成済みの SQL クエリと関連付けるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="01d23-104">This feature helps correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="01d23-105">新しい `TagWith()` メソッドを使用して LINQ クエリに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="01d23-105">You annotate a LINQ query using the new `TagWith()` method:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith("This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="01d23-106">この LINQ クエリは、次の SQL ステートメントに変換されます。</span><span class="sxs-lookup"><span data-stu-id="01d23-106">This LINQ query is translated to the following SQL statement:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="01d23-107">同じクエリに対して何度も `TagWith()` を呼び出すことが可能です。</span><span class="sxs-lookup"><span data-stu-id="01d23-107">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="01d23-108">クエリのタグは累積されます。</span><span class="sxs-lookup"><span data-stu-id="01d23-108">Query tags are cumulative.</span></span>
<span data-ttu-id="01d23-109">たとえば、次のようなメソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="01d23-109">For example, given the following methods:</span></span>

``` csharp
IQueryable<Friend> GetNearestFriends(Point myLocation) =>
    from f in context.Friends.TagWith("GetNearestFriends")
    orderby f.Location.Distance(myLocation) descending
    select f;

IQueryable<T> Limit<T>(IQueryable<T> source, int limit) =>
    source.TagWith("Limit").Take(limit);
```

<span data-ttu-id="01d23-110">次のクエリ:</span><span class="sxs-lookup"><span data-stu-id="01d23-110">The following query:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).ToList();
```

<span data-ttu-id="01d23-111">これは次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="01d23-111">Translates to:</span></span>

``` sql
-- GetNearestFriends

-- Limit

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="01d23-112">クエリのタグとして複数行の文字列を使用することも可能です。</span><span class="sxs-lookup"><span data-stu-id="01d23-112">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="01d23-113">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="01d23-113">For example:</span></span>

``` csharp
var results = Limit(GetNearestFriends(myLocation), 25).TagWith(
@"This is a multi-line
string").ToList();
```

<span data-ttu-id="01d23-114">これは次の SQL を生成します。</span><span class="sxs-lookup"><span data-stu-id="01d23-114">Produces the following SQL:</span></span>

``` sql
-- GetNearestFriends

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="01d23-115">既知の制限事項</span><span class="sxs-lookup"><span data-stu-id="01d23-115">Known limitations</span></span>

<span data-ttu-id="01d23-116">**クエリのタグはパラ―メーター化できません:** EF Core では、LINQ クエリ内のクエリのタグが常に、生成される SQL に含まれる文字列リテラルとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="01d23-116">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="01d23-117">コンパイルされたクエリがパラメーターとしてクエリのタグを受け取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="01d23-117">Compiled queries that take query tags as parameters aren't allowed.</span></span>
