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
# <a name="asynchronous-saving"></a>非同期保存

非同期保存は、データベースへの変更の書き込み中にスレッドがブロックされることを回避します。 これは、シック クライアント アプリケーションの UI の凍結を防止するために役立つ場合があります。 また、非同期操作では、データベース操作が完了している間に、他の要求を処理するためにスレッドを解放できるので、Web アプリケーションでのスループットを向上させることができます。 詳細については、「[C# での非同期プログラミング](https://docs.microsoft.com/dotnet/csharp/async)」を参照してください。

> [!WARNING]  
> EF Core は、同じコンテキスト インスタンス上での複数の並列操作の実行をサポートしていません。 次の操作を開始する前に、操作が完了するまで常に待機する必要があります。 これは通常、各同期操作において `await` キーワードを使用することで行われます。

Entity Framework Core では、`DbContext.SaveChanges()` に対する非同期の代替として `DbContext.SaveChangesAsync()`を提供しています。

[!code-csharp[Main](../../../samples/core/Saving/Saving/Async/Sample.cs#Sample)]
