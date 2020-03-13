---
title: 非同期クエリ - EF Core
author: smitpatel
ms.date: 10/03/2019
ms.assetid: b6429b14-cba0-4af4-878f-b829777c89cb
uid: core/querying/async
ms.openlocfilehash: ce26db32a616dac5edac2a8451014ae63cbfc12d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413781"
---
# <a name="asynchronous-queries"></a>非同期クエリ

非同期クエリでは、クエリがデータベースで実行されている間の、スレッドのブロックを回避します。 非同期クエリは、シッククライアント アプリケーションで応答性の高い UI を維持するために重要です。 また、Web アプリケーションのスループットを向上させることもできます。これにより、Web アプリケーションで他の要求を処理するために、スレッドが解放されます。 詳細については、「[C# での非同期プログラミング](/dotnet/csharp/async)」を参照してください。

> [!WARNING]  
> EF Core では、同じコンテキスト インスタンスで実行される複数の並列操作はサポートされていません。 次の操作を開始する前に、操作が完了するまで常に待機する必要があります。 これは通常、各非同期操作で `await` キーワードを使用することで行われます。

Entity Framework Core には、クエリを実行して結果を返す、LINQ メソッドと同様の非同期拡張メソッドのセットが用意されています。 例として、`ToListAsync()`、`ToArrayAsync()`、`SingleAsync()` があります。 `Where(...)` や `OrderBy(...)` などの一部の LINQ 演算子には非同期バージョンはありません。これらのメソッドでは LINQ 式のツリーのみがビルドされ、データベースでクエリが実行されないためです。

> [!IMPORTANT]  
> EF Core の非同期拡張メソッドは、`Microsoft.EntityFrameworkCore` 名前空間で定義されています。 メソッドを使用可能にするには、この名前空間がインポートされている必要があります。

[!code-csharp[Main](../../../samples/core/Querying/Async/Sample.cs#ToListAsync)]
