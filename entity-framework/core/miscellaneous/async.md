---
title: 非同期プログラミング-EF Core
description: Entity Framework Core を使用したデータの非同期クエリと保存
author: roji
ms.date: 9/2/2020
uid: core/miscellaneous/async
ms.openlocfilehash: 52b3ac20a50babbed6937ebe3365ac1947dcaef1
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128577"
---
# <a name="asynchronous-programming"></a><span data-ttu-id="07cf8-103">非同期プログラミング</span><span class="sxs-lookup"><span data-stu-id="07cf8-103">Asynchronous Programming</span></span>

<span data-ttu-id="07cf8-104">非同期操作では、データベースでクエリが実行されている間、スレッドのブロックを回避します。</span><span class="sxs-lookup"><span data-stu-id="07cf8-104">Asynchronous operations avoid blocking a thread while the query is executed in the database.</span></span> <span data-ttu-id="07cf8-105">非同期操作は、リッチクライアントアプリケーションで応答性の高い UI を維持するために重要です。また、web アプリケーションでスループットを向上させることもできます。これにより、web アプリケーションで他の要求を処理するためにスレッドを解放できます。</span><span class="sxs-lookup"><span data-stu-id="07cf8-105">Async operations are important for keeping a responsive UI in rich client applications, and can also increase throughput in web applications where they free up the thread to service other requests in web applications.</span></span>

<span data-ttu-id="07cf8-106">.NET standard に従うと、i/o を実行するすべての同期メソッドに対応する非同期の EF Core が提供されます。</span><span class="sxs-lookup"><span data-stu-id="07cf8-106">Following the .NET standard, EF Core provides asynchronous counterparts to all synchronous methods which perform I/O.</span></span> <span data-ttu-id="07cf8-107">これらは同期メソッドと同じ効果があり、C# およびキーワードと共に使用 `async` でき `await` ます。</span><span class="sxs-lookup"><span data-stu-id="07cf8-107">These have the same effects as the sync methods, and can be used with the C# `async` and `await` keywords.</span></span> <span data-ttu-id="07cf8-108">たとえば、データベース i/o の実行中にスレッドをブロックする DbContext. SaveChanges を使用する代わりに、SaveChangesAsync を使用できます。</span><span class="sxs-lookup"><span data-stu-id="07cf8-108">For example, instead of using DbContext.SaveChanges, which will block a thread while database I/O is performed, DbContext.SaveChangesAsync can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#SaveChangesAsync)]

<span data-ttu-id="07cf8-109">詳細については、 [C# の非同期プログラミングに関する一般的なドキュメント](/dotnet/csharp/async)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="07cf8-109">For more information, see [the general C# asynchronous programming docs](/dotnet/csharp/async).</span></span>

> [!WARNING]
> <span data-ttu-id="07cf8-110">EF Core では、同じコンテキスト インスタンスで実行される複数の並列操作はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="07cf8-110">EF Core doesn't support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="07cf8-111">次の操作を開始する前に、操作が完了するまで常に待機する必要があります。</span><span class="sxs-lookup"><span data-stu-id="07cf8-111">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="07cf8-112">これは通常、各非同期操作で `await` キーワードを使用することで行われます。</span><span class="sxs-lookup"><span data-stu-id="07cf8-112">This is typically done by using the `await` keyword on each async operation.</span></span>

> [!WARNING]
> <span data-ttu-id="07cf8-113">[Microsoft](https://github.com/dotnet/SqlClient)の非同期実装には、既知の問題がいくつかあります ( [#593](https://github.com/dotnet/SqlClient/issues/593)、 [#601](https://github.com/dotnet/SqlClient/issues/601)など)。</span><span class="sxs-lookup"><span data-stu-id="07cf8-113">The async implementation of [Microsoft.Data.SqlClient](https://github.com/dotnet/SqlClient) unfortunately has some known issues (e.g. [#593](https://github.com/dotnet/SqlClient/issues/593), [#601](https://github.com/dotnet/SqlClient/issues/601), and others).</span></span>

> [!NOTE]
> <span data-ttu-id="07cf8-114">EF Core は、使用中の基になるデータベースプロバイダー (例: Microsoft. Data. SqlClient) にキャンセルトークンを渡します。</span><span class="sxs-lookup"><span data-stu-id="07cf8-114">EF Core passes cancellation tokens down to the underlying database provider in use (e.g. Microsoft.Data.SqlClient).</span></span> <span data-ttu-id="07cf8-115">これらのトークンは受け入れられない場合があります。データベースプロバイダーのドキュメントを参照してください。</span><span class="sxs-lookup"><span data-stu-id="07cf8-115">These tokens may or may not be honored - consult your database provider's documentation.</span></span>

## <a name="async-linq-operators"></a><span data-ttu-id="07cf8-116">非同期 LINQ 演算子</span><span class="sxs-lookup"><span data-stu-id="07cf8-116">Async LINQ operators</span></span>

<span data-ttu-id="07cf8-117">LINQ クエリの非同期実行をサポートするために、EF Core には、クエリを実行して結果を返す非同期拡張メソッドのセットが用意されています。</span><span class="sxs-lookup"><span data-stu-id="07cf8-117">In order to support executing LINQ queries asynchronously, EF Core provides a set of async extension methods which execute the query and return results.</span></span> <span data-ttu-id="07cf8-118">これらの対応する標準の同期 LINQ 演算子には、ToListAsync、SingleAsync、AsAsyncEnumerable などがあります。</span><span class="sxs-lookup"><span data-stu-id="07cf8-118">These counterparts to the standard, synchronous LINQ operators include ToListAsync, SingleAsync, AsAsyncEnumerable, etc.:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#ToListAsync)]

<span data-ttu-id="07cf8-119">Where や OrderBy などの一部の LINQ 演算子には、LINQ 式ツリーを構築するだけで、クエリをデータベースで実行しないため、非同期バージョンはありません。</span><span class="sxs-lookup"><span data-stu-id="07cf8-119">Note that there are no async versions of some LINQ operators such as Where or OrderBy, because these only build up the LINQ expression tree and don't cause the query to be executed in the database.</span></span> <span data-ttu-id="07cf8-120">クエリの実行原因となる演算子だけが、対応する非同期の演算子を持っています。</span><span class="sxs-lookup"><span data-stu-id="07cf8-120">Only operators which cause query execution have async counterparts.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="07cf8-121">EF Core の非同期拡張メソッドは、`Microsoft.EntityFrameworkCore` 名前空間で定義されています。</span><span class="sxs-lookup"><span data-stu-id="07cf8-121">The EF Core async extension methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="07cf8-122">メソッドを使用可能にするには、この名前空間がインポートされている必要があります。</span><span class="sxs-lookup"><span data-stu-id="07cf8-122">This namespace must be imported for the methods to be available.</span></span>

## <a name="client-side-async-linq-operators"></a><span data-ttu-id="07cf8-123">クライアント側の非同期 LINQ 演算子</span><span class="sxs-lookup"><span data-stu-id="07cf8-123">Client-side async LINQ operators</span></span>

<span data-ttu-id="07cf8-124">上記で説明した非同期 LINQ 演算子は、EF クエリでのみ使用できます。クライアント側の LINQ to Objects クエリでは使用できません。</span><span class="sxs-lookup"><span data-stu-id="07cf8-124">The async LINQ operators discussed above can only be used on EF queries - you cannot use them with client-side LINQ to Objects query.</span></span> <span data-ttu-id="07cf8-125">EF 以外でクライアント側の非同期 LINQ 操作を実行するには、system.string [パッケージ](https://www.nuget.org/packages/System.Interactive.Async)を使用します。このパッケージは、サーバーで評価用に変換できないクライアント上の操作を実行する場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="07cf8-125">To perform client-side async LINQ operations outside of EF, use the [System.Interactive.Async package](https://www.nuget.org/packages/System.Interactive.Async); this package can be especially useful for performing operations on the client that cannot be translated for evaluation at the server.</span></span>

<span data-ttu-id="07cf8-126">残念ながら、system.string を参照すると、EF の DbSets に適用された LINQ 演算子に対して、あいまいな呼び出しコンパイルエラーが発生します。これにより、同じプロジェクト内で EF と system.servicemodel の両方を使用することが難しくなります。</span><span class="sxs-lookup"><span data-stu-id="07cf8-126">Unfortunately, referencing System.Interactive.Async causes ambiguous invocation compilation errors on LINQ operators applied to EF's DbSets; this makes it hard to use both EF and System.Interactive.Async in the same project.</span></span> <span data-ttu-id="07cf8-127">この問題を回避するには、DbSet に AsQueryable を追加します。</span><span class="sxs-lookup"><span data-stu-id="07cf8-127">To work around this issue, add AsQueryable to your DbSet:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/AsyncWithSystemInteractive/Program.cs#SystemInteractiveAsync)]
