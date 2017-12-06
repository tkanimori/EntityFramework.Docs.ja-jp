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
# <a name="asynchronous-queries"></a>非同期クエリ

非同期クエリでは、クエリは、データベース内で実行中に、スレッドがブロックされないようにします。 固定のシック クライアント アプリケーションの UI を防ぐことができます。 非同期操作では、ここで、スレッドを解放して、データベースの操作が完了するまで、他の要求を処理する web アプリケーションでスループットを向上できるもします。 詳細については、次を参照してください。 [c# での非同期プログラミング](https://docs.microsoft.com/dotnet/csharp/async)です。

> [!WARNING]  
> EF コアは、同じコンテキスト インスタンスで実行されている複数の並列操作をサポートしていません。 常に、次の操作を開始する前に完了するための操作を待つ必要があります。 使用してこれは、通常、`await`各非同期操作ではキーワードです。

Entity Framework Core では、によって、クエリが実行される LINQ メソッドと返される結果の代わりとして使用できる非同期の拡張メソッドのセットを提供します。 例としては、 `ToListAsync()`、 `ToArrayAsync()`、 `SingleAsync()`, などです。バージョンがあるいない非同期 LINQ 演算子のように`Where(...)`、`OrderBy(...)`などのみこれらのメソッドは LINQ 式ツリーを構築し、データベースで実行するクエリが発生しないためです。

> [!IMPORTANT]  
> EF コア async 拡張メソッドが定義されている、`Microsoft.EntityFrameworkCore`名前空間。 この名前空間は、使用できるメソッドをインポートする必要があります。

[!code-csharp[Main](../../../samples/core/Querying/Querying/Async/Sample.cs#Sample)]
