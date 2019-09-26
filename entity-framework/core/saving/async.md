---
title: 非同期保存 - EF Core
author: rowanmiller
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
uid: core/saving/async
ms.openlocfilehash: 0823b86f0579dd3e42f6bd2aebfb433d3cbe00ab
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197819"
---
# <a name="asynchronous-saving"></a><span data-ttu-id="8629b-102">非同期保存</span><span class="sxs-lookup"><span data-stu-id="8629b-102">Asynchronous Saving</span></span>

<span data-ttu-id="8629b-103">非同期保存は、データベースへの変更の書き込み中にスレッドがブロックされることを回避します。</span><span class="sxs-lookup"><span data-stu-id="8629b-103">Asynchronous saving avoids blocking a thread while the changes are written to the database.</span></span> <span data-ttu-id="8629b-104">これは、シック クライアント アプリケーションの UI の凍結を防止するために役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="8629b-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="8629b-105">また、非同期操作では、データベース操作が完了している間に、他の要求を処理するためにスレッドを解放できるので、Web アプリケーションでのスループットを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="8629b-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="8629b-106">詳細については、「[C# での非同期プログラミング](https://docs.microsoft.com/dotnet/csharp/async)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8629b-106">For more information, see [Asynchronous Programming in C#](https://docs.microsoft.com/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="8629b-107">EF Core は、同じコンテキスト インスタンス上での複数の並列操作の実行をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="8629b-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="8629b-108">次の操作を開始する前に、操作が完了するまで常に待機する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8629b-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="8629b-109">これは通常、各同期操作において `await` キーワードを使用することで行われます。</span><span class="sxs-lookup"><span data-stu-id="8629b-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="8629b-110">Entity Framework Core では、`DbContext.SaveChanges()` に対する非同期の代替として `DbContext.SaveChangesAsync()`を提供しています。</span><span class="sxs-lookup"><span data-stu-id="8629b-110">Entity Framework Core provides `DbContext.SaveChangesAsync()` as an asynchronous alternative to `DbContext.SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Async/Sample.cs#Sample)]
