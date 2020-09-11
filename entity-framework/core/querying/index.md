---
title: データのクエリ - EF Core
description: Entity Framework Core でのクエリに関する情報の概要
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 7c65ec3e-46c8-48f8-8232-9e31f96c277b
uid: core/querying/index
ms.openlocfilehash: 028c640a17c4946158c86bdf1a663a4050f55921
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616327"
---
# <a name="querying-data"></a><span data-ttu-id="0ec4f-103">データのクエリ</span><span class="sxs-lookup"><span data-stu-id="0ec4f-103">Querying Data</span></span>

<span data-ttu-id="0ec4f-104">Entity Framework Core では統合言語クエリ (LINQ) を使用し、データベースにデータを問い合わせます。</span><span class="sxs-lookup"><span data-stu-id="0ec4f-104">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="0ec4f-105">LINQ では C# (あるいは自分で選んだ .NET 言語) を使用して、厳密に型指定されたクエリを記述できます。</span><span class="sxs-lookup"><span data-stu-id="0ec4f-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries.</span></span> <span data-ttu-id="0ec4f-106">派生コンテキストとエンティティ クラスを使用して、データベース オブジェクトが参照されます。</span><span class="sxs-lookup"><span data-stu-id="0ec4f-106">It uses your derived context and entity classes to reference database objects.</span></span> <span data-ttu-id="0ec4f-107">EF Core は、LINQ クエリの表現をデータベース プロバイダーに渡します。</span><span class="sxs-lookup"><span data-stu-id="0ec4f-107">EF Core passes a representation of the LINQ query to the database provider.</span></span> <span data-ttu-id="0ec4f-108">その後、データベース プロバイダーがこれをデータベース固有のクエリ言語 (リレーショナル データベースの SQL など) に変換します。</span><span class="sxs-lookup"><span data-stu-id="0ec4f-108">Database providers in turn translate it to database-specific query language (for example, SQL for a relational database).</span></span>

> [!TIP]
> <span data-ttu-id="0ec4f-109">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="0ec4f-109">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) on GitHub.</span></span>

<span data-ttu-id="0ec4f-110">次のスニペットは、Entity Framework Core で一般的なタスクを実現する方法の例を示しています。</span><span class="sxs-lookup"><span data-stu-id="0ec4f-110">The following snippets show a few examples of how to achieve common tasks with Entity Framework Core.</span></span>

## <a name="loading-all-data"></a><span data-ttu-id="0ec4f-111">すべてのデータの読み込み</span><span class="sxs-lookup"><span data-stu-id="0ec4f-111">Loading all data</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingAllData)]

## <a name="loading-a-single-entity"></a><span data-ttu-id="0ec4f-112">単一のエンティティの読み込み</span><span class="sxs-lookup"><span data-stu-id="0ec4f-112">Loading a single entity</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#LoadingSingleEntity)]

## <a name="filtering"></a><span data-ttu-id="0ec4f-113">フィルター処理</span><span class="sxs-lookup"><span data-stu-id="0ec4f-113">Filtering</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Basics/Sample.cs#Filtering)]

## <a name="further-readings"></a><span data-ttu-id="0ec4f-114">参考資料</span><span class="sxs-lookup"><span data-stu-id="0ec4f-114">Further readings</span></span>

- <span data-ttu-id="0ec4f-115">[LINQ クエリ式](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)について参照してください</span><span class="sxs-lookup"><span data-stu-id="0ec4f-115">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
- <span data-ttu-id="0ec4f-116">EF Core でクエリが処理されるしくみについては、「[クエリのしくみ](xref:core/querying/how-query-works)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0ec4f-116">For more detailed information on how a query is processed in EF Core, see [How queries Work](xref:core/querying/how-query-works).</span></span>
