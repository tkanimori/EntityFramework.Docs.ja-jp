---
title: データのクエリ - EF Core
description: Entity Framework Core でのクエリに関する情報の概要
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/index
ms.openlocfilehash: da5177dda4f2df6537ee9133edf4f1240a4b5e94
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430119"
---
# <a name="querying-data"></a><span data-ttu-id="cd1ef-103">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="cd1ef-103">Querying Data</span></span>

<span data-ttu-id="cd1ef-104">Entity Framework Core では統合言語クエリ (LINQ) を使用し、データベースにデータを問い合わせます。</span><span class="sxs-lookup"><span data-stu-id="cd1ef-104">Entity Framework Core uses Language-Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="cd1ef-105">LINQ では C# (あるいは自分で選んだ .NET 言語) を使用して、厳密に型指定されたクエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="cd1ef-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="cd1ef-106">派生コンテキストとエンティティ クラスを使用して、データベース オブジェクトが参照されます。</span><span class="sxs-lookup"><span data-stu-id="cd1ef-106">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="cd1ef-107">EF Core は、LINQ クエリの表現をデータベース プロバイダーに渡します。</span><span class="sxs-lookup"><span data-stu-id="cd1ef-107">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="cd1ef-108">その後、データベース プロバイダーがこれをデータベース固有のクエリ言語 (リレーショナル データベースの SQL など) に変換します。</span><span class="sxs-lookup"><span data-stu-id="cd1ef-108">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span> <span data-ttu-id="cd1ef-109">結果で返されたエンティティが既にコンテキストに存在する場合でも、クエリは常にデータベースに対して実行されます。</span><span class="sxs-lookup"><span data-stu-id="cd1ef-109">Queries are always executed against the database even if the entities returned in the result already exist in the context.</span></span>

> [!TIP]
> <span data-ttu-id="cd1ef-110">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="cd1ef-110">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Overview) on GitHub.</span></span>

<span data-ttu-id="cd1ef-111">次のスニペットは、Entity Framework Core で一般的なタスクを実現する方法の例を示しています。</span><span class="sxs-lookup"><span data-stu-id="cd1ef-111">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="cd1ef-112">すべてのデータの読み込み</span><span class="sxs-lookup"><span data-stu-id="cd1ef-112">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="cd1ef-113">単一のエンティティの読み込み</span><span class="sxs-lookup"><span data-stu-id="cd1ef-113">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="cd1ef-114">フィルター処理</span><span class="sxs-lookup"><span data-stu-id="cd1ef-114">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Overview/Program.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="cd1ef-115">参考資料</span><span class="sxs-lookup"><span data-stu-id="cd1ef-115">Further readings</span></span>

- <span data-ttu-id="cd1ef-116">[LINQ クエリ式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)について参照してください</span><span class="sxs-lookup"><span data-stu-id="cd1ef-116">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="cd1ef-117">EF Core でクエリが処理されるしくみについては、「[クエリのしくみ](xref:core/querying/how-query-works)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cd1ef-117">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
