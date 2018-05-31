---
title: 非同期保存 - EF Core
author: rowanmiller
ms.author: divega
ms.date: 01/24/2017
ms.assetid: b64a606e-ecd9-4807-829a-b6ec05ade33f
ms.technology: entity-framework-core
uid: core/saving/async
ms.openlocfilehash: 640e5f131b0e9ef4e5028d1dcaf80f3e5bbd9d9b
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052622"
---
# <a name="asynchronous-saving"></a><span data-ttu-id="17dac-102">非同期保存</span><span class="sxs-lookup"><span data-stu-id="17dac-102">Asynchronous Saving</span></span>

<span data-ttu-id="17dac-103">非同期保存は、データベースへの変更の書き込み中にスレッドがブロックされることを回避します。</span><span class="sxs-lookup"><span data-stu-id="17dac-103">Asynchronous saving avoids blocking a thread while the changes are written to the database.</span></span> <span data-ttu-id="17dac-104">これは、シック クライアント アプリケーションの UI の凍結を防止するために役立つ場合があります。</span><span class="sxs-lookup"><span data-stu-id="17dac-104">This can be useful to avoid freezing the UI of a thick-client application.</span></span> <span data-ttu-id="17dac-105">また、非同期操作では、データベース操作が完了している間に、他の要求を処理するためにスレッドを解放できるので、Web アプリケーションでのスループットを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="17dac-105">Asynchronous operations can also increase throughput in a web application, where the thread can be freed up to service other requests while the database operation completes.</span></span> <span data-ttu-id="17dac-106">詳細については、「[C# での非同期プログラミング](https://docs.microsoft.com/dotnet/csharp/async)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="17dac-106">For more information, see [Asynchronous Programming in C#](https://docs.microsoft.com/dotnet/csharp/async).</span></span>

> [!WARNING]  
> <span data-ttu-id="17dac-107">EF Core は、同じコンテキスト インスタンス上での複数の並列操作の実行をサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="17dac-107">EF Core does not support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="17dac-108">次の操作を開始する前に、操作が完了するまで常に待機する必要があります。</span><span class="sxs-lookup"><span data-stu-id="17dac-108">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="17dac-109">これは通常、各同期操作において `await` キーワードを使用することで行われます。</span><span class="sxs-lookup"><span data-stu-id="17dac-109">This is typically done by using the `await` keyword on each asynchronous operation.</span></span>

<span data-ttu-id="17dac-110">Entity Framework Core では、`DbContext.SaveChanges()` に対する非同期の代替として `DbContext.SaveChangesAsync()`を提供しています。</span><span class="sxs-lookup"><span data-stu-id="17dac-110">Entity Framework Core provides `DbContext.SaveChangesAsync()` as an asynchronous alternative to `DbContext.SaveChanges()`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Async/Sample.cs#Sample)]
