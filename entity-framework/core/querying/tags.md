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
# <a name="query-tags"></a><span data-ttu-id="48e3a-103">クエリのタグ</span><span class="sxs-lookup"><span data-stu-id="48e3a-103">Query tags</span></span>

<span data-ttu-id="48e3a-104">クエリのタグは、コード内の LINQ クエリを、ログでキャプチャされる生成済みの SQL クエリと関連付けるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="48e3a-104">Query tags help correlate LINQ queries in code with generated SQL queries captured in logs.</span></span>
<span data-ttu-id="48e3a-105">新しい `TagWith()` メソッドを使用して LINQ クエリに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="48e3a-105">You annotate a LINQ query using the new `TagWith()` method:</span></span>

> [!TIP]
> <span data-ttu-id="48e3a-106">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="48e3a-106">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tags) on GitHub.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

<span data-ttu-id="48e3a-107">この LINQ クエリは、次の SQL ステートメントに変換されます。</span><span class="sxs-lookup"><span data-stu-id="48e3a-107">This LINQ query is translated to the following SQL statement:</span></span>

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="48e3a-108">同じクエリに対して何度も `TagWith()` を呼び出すことが可能です。</span><span class="sxs-lookup"><span data-stu-id="48e3a-108">It's possible to call `TagWith()` many times on the same query.</span></span>
<span data-ttu-id="48e3a-109">クエリのタグは累積されます。</span><span class="sxs-lookup"><span data-stu-id="48e3a-109">Query tags are cumulative.</span></span>
<span data-ttu-id="48e3a-110">たとえば、次のようなメソッドがあるとします。</span><span class="sxs-lookup"><span data-stu-id="48e3a-110">For example, given the following methods:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#QueryableMethods)]

<span data-ttu-id="48e3a-111">次のクエリ:</span><span class="sxs-lookup"><span data-stu-id="48e3a-111">The following query:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#ChainedQueryTags)]

<span data-ttu-id="48e3a-112">これは次のように変換されます。</span><span class="sxs-lookup"><span data-stu-id="48e3a-112">Translates to:</span></span>

```sql
-- GetNearestPeople

-- Limit

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="48e3a-113">クエリのタグとして複数行の文字列を使用することも可能です。</span><span class="sxs-lookup"><span data-stu-id="48e3a-113">It's also possible to use multi-line strings as query tags.</span></span>
<span data-ttu-id="48e3a-114">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="48e3a-114">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#MultilineQueryTag)]

<span data-ttu-id="48e3a-115">これは次の SQL を生成します。</span><span class="sxs-lookup"><span data-stu-id="48e3a-115">Produces the following SQL:</span></span>

```sql
-- GetNearestPeople

-- Limit

-- This is a multi-line
-- string

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

## <a name="known-limitations"></a><span data-ttu-id="48e3a-116">既知の制限事項</span><span class="sxs-lookup"><span data-stu-id="48e3a-116">Known limitations</span></span>

<span data-ttu-id="48e3a-117">**クエリのタグはパラ―メーター化できません:** EF Core では、LINQ クエリ内のクエリのタグが常に、生成される SQL に含まれる文字列リテラルとして扱われます。</span><span class="sxs-lookup"><span data-stu-id="48e3a-117">**Query tags aren't parameterizable:** EF Core always treats query tags in the LINQ query as string literals that are included in the generated SQL.</span></span>
<span data-ttu-id="48e3a-118">コンパイルされたクエリがパラメーターとしてクエリのタグを受け取ることはできません。</span><span class="sxs-lookup"><span data-stu-id="48e3a-118">Compiled queries that take query tags as parameters aren't allowed.</span></span>
