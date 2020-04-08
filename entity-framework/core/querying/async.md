---
title: 非同期クエリ - EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: b6429b14-cba0-4af4-878f-b829777c89cb
uid: core/querying/async
ms.openlocfilehash: ce26db32a616dac5edac2a8451014ae63cbfc12d
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413781"
---
# <a name="asynchronous-queries"></a><span data-ttu-id="fd660-102">非同期クエリ</span><span class="sxs-lookup"><span data-stu-id="fd660-102">Asynchronous Queries</span></span>

<span data-ttu-id="fd660-103">非同期クエリでは、クエリがデータベースで実行されている間の、スレッドのブロックを回避します。</span><span class="sxs-lookup"><span data-stu-id="fd660-103">Asynchronous queries avoid blocking a thread while the query is executed in the database.</span></span> <span data-ttu-id="fd660-104">非同期クエリは、シッククライアント アプリケーションで応答性の高い UI を維持するために重要です。</span><span class="sxs-lookup"><span data-stu-id="fd660-104">Async queries are important for keeping a responsive UI in thick-client applications.</span></span> <span data-ttu-id="fd660-105">また、Web アプリケーションのスループットを向上させることもできます。これにより、Web アプリケーションで他の要求を処理するために、スレッドが解放されます。</span><span class="sxs-lookup"><span data-stu-id="fd660-105">They can also increase throughput in web applications where they free up the thread to service other requests in web applications.</span></span> <span data-ttu-id="fd660-106">詳細については、「[C# での非同期プログラミング](/dotnet/csharp/async)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fd660-106">For more information, see [Asynchronous Programming in C#](/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="fd660-107">EF Core では、同じコンテキスト インスタンスで実行される複数の並列操作はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="fd660-107">EF Core doesn't support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="fd660-108">次の操作を開始する前に、操作が完了するまで常に待機する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd660-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="fd660-109">これは通常、各非同期操作で `await` キーワードを使用することで行われます。</span><span class="sxs-lookup"><span data-stu-id="fd660-109">This is typically done by using the `await` keyword on each async operation.</span></span>

<span data-ttu-id="fd660-110">Entity Framework Core には、クエリを実行して結果を返す、LINQ メソッドと同様の非同期拡張メソッドのセットが用意されています。</span><span class="sxs-lookup"><span data-stu-id="fd660-110">Entity Framework Core provides a set of async extension methods similar to the LINQ methods, which execute a query and return results.</span></span> <span data-ttu-id="fd660-111">例として、`ToListAsync()`、`ToArrayAsync()`、`SingleAsync()` があります。</span><span class="sxs-lookup"><span data-stu-id="fd660-111">Examples include `ToListAsync()`, `ToArrayAsync()`, `SingleAsync()`.</span></span> <span data-ttu-id="fd660-112">`Where(...)` や `OrderBy(...)` などの一部の LINQ 演算子には非同期バージョンはありません。これらのメソッドでは LINQ 式のツリーのみがビルドされ、データベースでクエリが実行されないためです。</span><span class="sxs-lookup"><span data-stu-id="fd660-112">There are no async versions of some LINQ operators such as `Where(...)` or `OrderBy(...)` because these methods only build up the LINQ expression tree and don't cause the query to be executed in the database.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="fd660-113">EF Core の非同期拡張メソッドは、`Microsoft.EntityFrameworkCore` 名前空間で定義されています。</span><span class="sxs-lookup"><span data-stu-id="fd660-113">The EF Core async extension methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="fd660-114">メソッドを使用可能にするには、この名前空間がインポートされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="fd660-114">This namespace must be imported for the methods to be available.</span></span>

[!code-csharp[Main](../../../samples/core/Querying/Async/Sample.cs#ToListAsync)]
