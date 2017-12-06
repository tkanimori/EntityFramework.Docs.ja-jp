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
# <a name="asynchronous-saving"></a>非同期の保存

非同期の保存は、変更がデータベースに書き込まれるときに、スレッドがブロックを回避できます。 固定のシック クライアント アプリケーションの UI を防ぐことができます。 非同期操作では、ここで、スレッドを解放して、データベースの操作が完了するまで、他の要求を処理する web アプリケーションでスループットを向上できるもします。 詳細については、次を参照してください。 [c# での非同期プログラミング](https://docs.microsoft.com/dotnet/csharp/async)です。

> [!WARNING]  
> EF コアは、同じコンテキスト インスタンスで実行されている複数の並列操作をサポートしていません。 常に、次の操作を開始する前に完了するための操作を待つ必要があります。 使用してこれは、通常、`await`各非同期操作ではキーワードです。

Entity Framework Core 提供`DbContext.SaveChangesAsync()`する代わりに非同期`DbContext.SaveChanges()`です。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Async/Sample.cs#Sample)]
