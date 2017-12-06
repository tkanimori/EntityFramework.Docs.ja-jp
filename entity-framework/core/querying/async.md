---
title: "非同期クエリ - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 01/24/2017
ms.assetid: b6429b14-cba0-4af4-878f-b829777c89cb
ms.technology: entity-framework-core
uid: core/querying/async
ms.openlocfilehash: 6554f04d0edfe0ca2ee72ebed8b878a1997a9500
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="asynchronous-queries"></a><span data-ttu-id="855a7-102">非同期クエリ</span><span class="sxs-lookup"><span data-stu-id="855a7-102">Asynchronous Queries</span></span>

<span data-ttu-id="855a7-103">非同期クエリでは、クエリは、データベース内で実行中に、スレッドがブロックされないようにします。</span><span class="sxs-lookup"><span data-stu-id="855a7-103">Asynchronous queries avoid blocking a thread while the query is executed in the database.</span></span> <span data-ttu-id="855a7-104">固定のシック クライアント アプリケーションの UI を防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="855a7-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="855a7-105">非同期操作では、ここで、スレッドを解放して、データベースの操作が完了するまで、他の要求を処理する web アプリケーションでスループットを向上できるもします。</span><span class="sxs-lookup"><span data-stu-id="855a7-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="855a7-106">詳細については、次を参照してください。 [c# での非同期プログラミング](https://docs.microsoft.com/dotnet/csharp/async)です。</span><span class="sxs-lookup"><span data-stu-id="855a7-106">For more information, see [Asynchronous Programming in C#](https://docs.microsoft.com/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="855a7-107">EF コアは、同じコンテキスト インスタンスで実行されている複数の並列操作をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="855a7-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="855a7-108">常に、次の操作を開始する前に完了するための操作を待つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="855a7-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="855a7-109">使用してこれは、通常、`await`各非同期操作ではキーワードです。</span><span class="sxs-lookup"><span data-stu-id="855a7-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="855a7-110">Entity Framework Core では、によって、クエリが実行される LINQ メソッドと返される結果の代わりとして使用できる非同期の拡張メソッドのセットを提供します。</span><span class="sxs-lookup"><span data-stu-id="855a7-110">Entity Framework Core provides a set of asynchronous extension methods that can be used as an alternative to the LINQ methods that cause a query to be executed and results returned.</span></span> <span data-ttu-id="855a7-111">例としては、 `ToListAsync()`、 `ToArrayAsync()`、 `SingleAsync()`, などです。バージョンがあるいない非同期 LINQ 演算子のように`Where(...)`、`OrderBy(...)`などのみこれらのメソッドは LINQ 式ツリーを構築し、データベースで実行するクエリが発生しないためです。</span><span class="sxs-lookup"><span data-stu-id="855a7-111">Examples include `ToListAsync()`, `ToArrayAsync()`, `SingleAsync()`, etc. There are not async versions of LINQ operators such as `Where(...)`, `OrderBy(...)`, etc. because these methods only build up the LINQ expression tree and do not cause the query to be executed in the database.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="855a7-112">EF コア async 拡張メソッドが定義されている、`Microsoft.EntityFrameworkCore`名前空間。</span><span class="sxs-lookup"><span data-stu-id="855a7-112">The EF Core async extension methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="855a7-113">この名前空間は、使用できるメソッドをインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="855a7-113">This namespace must be imported for the methods to be available.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Querying/Async/Sample.cs#Sample)]
