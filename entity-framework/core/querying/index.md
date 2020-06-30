---
title: データのクエリ - EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
uid: core/querying/index
ms.openlocfilehash: 82f8a2e8e78e9d82f46f2fc6ced6dd9141329a22
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370341"
---
# <a name="querying-data"></a><span data-ttu-id="25d3d-102">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="25d3d-102">Querying Data</span></span>

<span data-ttu-id="25d3d-103">Entity Framework Core では統合言語クエリ (LINQ) を使用し、データベースにデータを問い合わせます。</span><span class="sxs-lookup"><span data-stu-id="25d3d-103">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="25d3d-104">LINQ では C# (あるいは自分で選んだ .NET 言語) を使用して、厳密に型指定されたクエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="25d3d-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="25d3d-105">派生コンテキストとエンティティ クラスを使用して、データベース オブジェクトが参照されます。</span><span class="sxs-lookup"><span data-stu-id="25d3d-105">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="25d3d-106">EF Core は、LINQ クエリの表現をデータベース プロバイダーに渡します。</span><span class="sxs-lookup"><span data-stu-id="25d3d-106">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="25d3d-107">その後、データベース プロバイダーがこれをデータベース固有のクエリ言語 (リレーショナル データベースの SQL など) に変換します。</span><span class="sxs-lookup"><span data-stu-id="25d3d-107">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span>

> [!TIP]
> <span data-ttu-id="25d3d-108">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="25d3d-108">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

<span data-ttu-id="25d3d-109">次のスニペットは、Entity Framework Core で一般的なタスクを実現する方法の例を示しています。</span><span class="sxs-lookup"><span data-stu-id="25d3d-109">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="25d3d-110">すべてのデータの読み込み</span><span class="sxs-lookup"><span data-stu-id="25d3d-110">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="25d3d-111">単一のエンティティの読み込み</span><span class="sxs-lookup"><span data-stu-id="25d3d-111">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="25d3d-112">フィルター処理</span><span class="sxs-lookup"><span data-stu-id="25d3d-112">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="25d3d-113">参考資料</span><span class="sxs-lookup"><span data-stu-id="25d3d-113">Further readings</span></span>

- <span data-ttu-id="25d3d-114">[LINQ クエリ式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)について参照してください</span><span class="sxs-lookup"><span data-stu-id="25d3d-114">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="25d3d-115">EF Core でクエリが処理されるしくみについては、「[クエリのしくみ](xref:core/querying/how-query-works)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="25d3d-115">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
