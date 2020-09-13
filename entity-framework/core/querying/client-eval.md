---
title: クライアントとサーバーの評価 - EF Core
description: Entity Framework Core を使用した、クライアントおよびサーバーでのクエリの評価
author: smitpatel
ms.date: 10/03/2019
ms.assetid: 8b6697cc-7067-4dc2-8007-85d80503d123
uid: core/querying/client-eval
ms.openlocfilehash: 61a9f4d69b78f6cb42f4d367948f93230370d7f2
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616467"
---
# <a name="client-vs-server-evaluation"></a>クライアントとサーバーの評価

一般に、Entity Framework Core では、可能な限りサーバーでのクエリの評価を試みます。 EF Core ではクエリの一部をパラメーターに変換し、クライアント側で評価することができます。 クエリの残りの部分 (生成されたパラメーターを含む) は、データベース プロバイダーに与えられ、サーバー上で評価する同等のデータベース クエリが判別されます。 EF Core では、最上位レベルのプロジェクション (基本的には、`Select()` への最後の呼び出し) で部分的なクライアント評価がサポートされています。 クエリの最上位レベルのプロジェクションをサーバーに変換できない場合、EF Core ではサーバーから必要なデータをフェッチし、クライアントでクエリの残りの部分を評価します。 EF Core では、サーバーに変換できない最上位レベルのプロジェクション以外の場所で式が検出された場合、ランタイム例外がスローされます。 EF Core でサーバーに変換できないものを判別する方法については、[クエリのしくみ](xref:core/querying/how-query-works)に関するページを参照してください。

> [!NOTE]
> バージョン 3.0 より前では、Entity Framework Core で、クエリ内の任意の場所でのクライアント評価がサポートされていました。 詳細については、[以前のバージョンに関するセクション](#previous-versions)を参照してください。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying)は GitHub で確認できます。

## <a name="client-evaluation-in-the-top-level-projection"></a>最上位レベルのプロジェクションでのクライアント評価

次の例では、SQL Server データベースから返される、ブログの URL を標準化するために、ヘルパー メソッドが使用されています。 SQL Server プロバイダーではこのメソッドの実装方法が把握されないため、SQL に変換することはできません。 クエリの他の側面はすべてデータベースで評価されますが、このメソッド経由で返された `URL` の受け渡しはクライアント上で行われます。

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientMethod)]

## <a name="unsupported-client-evaluation"></a>サポートされていないクライアント評価

クライアント評価は便利ですが、パフォーマンスが低下する可能性があります。 次のクエリについて考えてみます。ここでは、ヘルパー メソッドが where フィルターで使用されています。 フィルターはデータベースで適用できないため、クライアントにフィルターを適用するには、すべてのデータをメモリに取り込む必要があります。 フィルターとサーバー上のデータの量に基づいて、クライアント評価でパフォーマンスが低下する可能性があります。 そのため、Entity Framework Core ではこのようなクライアント評価をブロックし、ランタイム例外をスローします。

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ClientWhere)]

## <a name="explicit-client-evaluation"></a>明示的なクライアント評価

次のような特定のケースでは、クライアント評価を明示的に強制することが必要な場合があります

- データの量が少ないため、クライアントでの評価によってパフォーマンスが大幅に低下することがない。
- 使用されている LINQ 演算子がサーバー側で変換されない。

このような場合は、`AsEnumerable` または `ToList` (非同期の場合は `AsAsyncEnumerable` または `ToListAsync`) のようなメソッドを呼び出すことにより、クライアント評価を明示的に選択することができます。 `AsEnumerable` を使用すると、結果をストリーミングすることになりますが、`ToList` を使用すると、リストを作成することによってバッファリングが発生し、追加のメモリも必要になります。 複数回列挙する場合でも、データベースに対するクエリは 1 つのみであるため、結果をリストに格納するのがより有効です。 特定の使用方法に応じて、ケースにより有効なメソッドを評価する必要があります。

[!code-csharp[Main](../../../samples/core/Querying/ClientEval/Sample.cs#ExplicitClientEval)]

## <a name="potential-memory-leak-in-client-evaluation"></a>クライアント評価での潜在的なメモリ リーク

クエリの変換とコンパイルにはコストがかかるため、EF Core ではコンパイル済みのクエリ プランをキャッシュします。 キャッシュされたデリゲートでは、最上位レベルのプロジェクションのクライアント評価を行うときに、クライアント コードを使用する場合があります。 EF Core では、クライアントによって評価されるツリーの部分のパラメーターが生成され、パラメーター値を置き換えることによってクエリ プランが再利用されます。 しかし、式ツリー内の特定の定数をパラメーターに変換することはできません。 キャッシュされたデリゲートにこのような定数が含まれている場合、それらのオブジェクトは、まだ参照されているため、ガベージ コレクションを行うことはできません。 このようなオブジェクトに DbContext またはその他のサービスが含まれている場合、アプリのメモリ使用量が時間の経過と共に増加する可能性があります。 通常、この動作はメモリ リークの兆候です。 現在のデータベース プロバイダーを使用してマップできない型の定数が見つかるたびに、EF Core で例外がスローされます。 一般的な原因とその解決策は次のとおりです。

- **インスタンス メソッドを使用する**:クライアント プロジェクションでインスタンス メソッドを使用する場合、式ツリーにはインスタンスの定数が含まれます。 メソッドでインスタンスのデータを使用しない場合は、メソッドを静的にすることを検討してください。 メソッド本体にインスタンス データが必要な場合は、特定のデータを引数としてメソッドに渡します。
- **定数引数をメソッドに渡す**:このケースは通常、クライアント メソッドに対する引数で `this` を使用することで発生します。 引数を複数のスカラー引数に分割することを検討してください。これは、データベース プロバイダーでマップできます。
- **その他の定数**:その他のケースで定数が見つかった場合は、定数が処理で必要かどうかを評価できます。 定数を指定する必要がある場合、または上記のケースの解決策を使用できない場合は、値を格納するローカル変数を作成し、クエリでローカル変数を使用します。 EF Core では、ローカル変数をパラメーターに変換します。

## <a name="previous-versions"></a>以前のバージョン

次のセクションは、3.0 より前のバージョンの EF Core に適用されます。

以前の EF Core バージョンでは、最上位レベルのプロジェクションだけでなく、クエリのどの部分でもクライアント評価がサポートされていました。 そのため、[サポートされていないクライアント評価](#unsupported-client-evaluation)に関するセクションで示されているものと同様のクエリが正常に動作していました。 この動作によってパフォーマンスの問題が発生する可能性があるため、EF Core ではクライアント評価の警告がログに記録されていました。 ログ記録出力の表示について詳しくは、「[ログの記録](xref:core/miscellaneous/logging)」を参照してください。

必要に応じて、EF Core では既定の動作を変更し、クライアントの評価を行うときに例外をスローするか、何も実行しないようにすることができました (プロジェクションの場合を除く)。 例外をスローする動作は、3.0 の動作と似たようなものになります。 動作を変更するには、(通常は `DbContext.OnConfiguring` で、または ASP.NET Core を使用している場合は `Startup.cs` で) コンテキストのオプションを設定するときに警告を構成する必要があります。

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFQuerying;Trusted_Connection=True;")
        .ConfigureWarnings(warnings => warnings.Throw(RelationalEventId.QueryClientEvaluationWarning));
}
```
