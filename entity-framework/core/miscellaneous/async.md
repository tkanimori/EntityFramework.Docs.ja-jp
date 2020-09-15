---
title: 非同期プログラミング-EF Core
description: Entity Framework Core を使用したデータの非同期クエリと保存
author: roji
ms.date: 9/2/2020
uid: core/miscellaneous/async
ms.openlocfilehash: cefbe32b34a38ed6d749ef3ddfff210d5db12332
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071901"
---
# <a name="asynchronous-programming"></a>非同期プログラミング

非同期操作では、データベースでクエリが実行されている間、スレッドのブロックを回避します。 非同期操作は、リッチクライアントアプリケーションで応答性の高い UI を維持するために重要です。また、web アプリケーションでスループットを向上させることもできます。これにより、web アプリケーションで他の要求を処理するためにスレッドを解放できます。

.NET standard に従うと、i/o を実行するすべての同期メソッドに対応する非同期の EF Core が提供されます。 これらは同期メソッドと同じ効果があり、C# およびキーワードと共に使用 `async` でき `await` ます。 たとえば、データベース i/o の実行中にスレッドをブロックする DbContext. SaveChanges を使用する代わりに、SaveChangesAsync を使用できます。

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#SaveChangesAsync)]

詳細については、 [C# の非同期プログラミングに関する一般的なドキュメント](/dotnet/csharp/async)を参照してください。

> [!WARNING]
> EF Core では、同じコンテキスト インスタンスで実行される複数の並列操作はサポートされていません。 次の操作を開始する前に、操作が完了するまで常に待機する必要があります。 これは通常、各非同期操作で `await` キーワードを使用することで行われます。

> [!NOTE]
> EF Core は、使用中の基になるデータベースプロバイダー (例: Microsoft. Data. SqlClient) にキャンセルトークンを渡します。 これらのトークンは受け入れられない場合があります。データベースプロバイダーのドキュメントを参照してください。  

## <a name="async-linq-operators"></a>非同期 LINQ 演算子

LINQ クエリの非同期実行をサポートするために、EF Core には、クエリを実行して結果を返す非同期拡張メソッドのセットが用意されています。 これらの対応する標準の同期 LINQ 演算子には、ToListAsync、SingleAsync、AsAsyncEnumerable などがあります。

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#ToListAsync)]

Where や OrderBy などの一部の LINQ 演算子には、LINQ 式ツリーを構築するだけで、クエリをデータベースで実行しないため、非同期バージョンはありません。 クエリの実行原因となる演算子だけが、対応する非同期の演算子を持っています。

> [!IMPORTANT]
> EF Core の非同期拡張メソッドは、`Microsoft.EntityFrameworkCore` 名前空間で定義されています。 メソッドを使用可能にするには、この名前空間がインポートされている必要があります。

## <a name="client-side-async-linq-operators"></a>クライアント側の非同期 LINQ 演算子

上記で説明した非同期 LINQ 演算子は、EF クエリでのみ使用できます。クライアント側の LINQ to Objects クエリでは使用できません。 EF 以外でクライアント側の非同期 LINQ 操作を実行するには、system.string [パッケージ](https://www.nuget.org/packages/System.Interactive.Async)を使用します。このパッケージは、サーバーで評価用に変換できないクライアント上の操作を実行する場合に特に便利です。

残念ながら、system.string を参照すると、EF の DbSets に適用された LINQ 演算子に対して、あいまいな呼び出しコンパイルエラーが発生します。これにより、同じプロジェクト内で EF と system.servicemodel の両方を使用することが難しくなります。 この問題を回避するには、DbSet に AsQueryable を追加します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/AsyncWithSystemInteractive/Program.cs#SystemInteractiveAsync)]
