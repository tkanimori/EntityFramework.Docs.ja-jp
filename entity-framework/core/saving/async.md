---
title: "非同期の保存 - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
ms.technology: entity-framework-core
uid: core/saving/async
ms.openlocfilehash: 640e5f131b0e9ef4e5028d1dcaf80f3e5bbd9d9b
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="asynchronous-saving"></a><span data-ttu-id="881d5-102">非同期の保存</span><span class="sxs-lookup"><span data-stu-id="881d5-102">Asynchronous Saving</span></span>

<span data-ttu-id="881d5-103">非同期の保存は、変更がデータベースに書き込まれるときに、スレッドがブロックを回避できます。</span><span class="sxs-lookup"><span data-stu-id="881d5-103">Asynchronous saving avoids blocking a thread while the changes are written to the database.</span></span> <span data-ttu-id="881d5-104">固定のシック クライアント アプリケーションの UI を防ぐことができます。</span><span class="sxs-lookup"><span data-stu-id="881d5-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="881d5-105">非同期操作では、ここで、スレッドを解放して、データベースの操作が完了するまで、他の要求を処理する web アプリケーションでスループットを向上できるもします。</span><span class="sxs-lookup"><span data-stu-id="881d5-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="881d5-106">詳細については、次を参照してください。 [c# での非同期プログラミング](https://docs.microsoft.com/dotnet/csharp/async)です。</span><span class="sxs-lookup"><span data-stu-id="881d5-106">For more information, see [Asynchronous Programming in C#](https://docs.microsoft.com/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="881d5-107">EF コアは、同じコンテキスト インスタンスで実行されている複数の並列操作をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="881d5-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="881d5-108">常に、次の操作を開始する前に完了するための操作を待つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="881d5-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="881d5-109">使用してこれは、通常、`await`各非同期操作ではキーワードです。</span><span class="sxs-lookup"><span data-stu-id="881d5-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="881d5-110">Entity Framework Core 提供`DbContext.SaveChangesAsync()`する代わりに非同期`DbContext.SaveChanges()`です。</span><span class="sxs-lookup"><span data-stu-id="881d5-110">Entity Framework Core provides `DbContext.SaveChangesAsync()` as an asynchronous alternative to `DbContext.SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Async/Sample.cs#Sample)]
