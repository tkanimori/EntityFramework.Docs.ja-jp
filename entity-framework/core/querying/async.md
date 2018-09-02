---
title: 非同期クエリ - EF Core
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b6429b14-cba0-4af4-878f-b829777c89cb
uid: core/querying/async
ms.openlocfilehash: de00e25279e29355a4eb3e55597a8578ceccecb6
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993566"
---
# <a name="asynchronous-queries"></a><span data-ttu-id="46173-102">非同期クエリ</span><span class="sxs-lookup"><span data-stu-id="46173-102">Asynchronous Queries</span></span>

<span data-ttu-id="46173-103">非同期クエリでは、クエリがデータベースで実行されている間の、スレッドのブロックを回避します。</span><span class="sxs-lookup"><span data-stu-id="46173-103">Asynchronous queries avoid blocking a thread while the query is executed in the database.</span></span> <span data-ttu-id="46173-104">これは、シック クライアント アプリケーションの UI の凍結を防止するために役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="46173-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="46173-105">また、非同期操作では、データベース操作が完了している間に、他の要求を処理するためにスレッドを解放できるので、Web アプリケーションでのスループットを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="46173-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="46173-106">詳細については、「[C# での非同期プログラミング](https://docs.microsoft.com/dotnet/csharp/async)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="46173-106">For more information, see [Asynchronous Programming in C#](https://docs.microsoft.com/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="46173-107">EF Core は、同じコンテキスト インスタンス上での複数の並列操作の実行をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="46173-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="46173-108">次の操作を開始する前に、操作が完了するまで常に待機する必要があります。</span><span class="sxs-lookup"><span data-stu-id="46173-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="46173-109">これは通常、各同期操作において `await` キーワードを使用することで行われます。</span><span class="sxs-lookup"><span data-stu-id="46173-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="46173-110">Entity Framework Core では、クエリを実行して結果を返すための LINQ メソッドの代替として使用できる、一連の非同期拡張メソッドを提供しています。</span><span class="sxs-lookup"><span data-stu-id="46173-110">Entity Framework Core provides a set of asynchronous extension methods that can be used as an alternative to the LINQ methods that cause a query to be executed and results returned.</span></span> <span data-ttu-id="46173-111">例として、`ToListAsync()`、`ToArrayAsync()`、`SingleAsync()` などがあります。`Where(...)`、`OrderBy(...)` などの LINQ 演算子の非同期バージョンはありません。これらのメソッドでは LINQ 式のツリーのみをビルドし、データベースでのクエリの実行は発生しないためです。</span><span class="sxs-lookup"><span data-stu-id="46173-111">Examples include `ToListAsync()`, `ToArrayAsync()`, `SingleAsync()`, etc. There are not async versions of LINQ operators such as `Where(...)`, `OrderBy(...)`, etc. because these methods only build up the LINQ expression tree and do not cause the query to be executed in the database.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="46173-112">EF Core の非同期拡張メソッドは、`Microsoft.EntityFrameworkCore` 名前空間で定義されています。</span><span class="sxs-lookup"><span data-stu-id="46173-112">The EF Core async extension methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="46173-113">メソッドを使用可能にするには、この名前空間がインポートされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="46173-113">This namespace must be imported for the methods to be available.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/Async/Sample.cs#Sample)]
