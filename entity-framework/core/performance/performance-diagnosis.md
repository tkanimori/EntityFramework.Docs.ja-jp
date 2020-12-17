---
title: パフォーマンスの診断-EF Core
description: Entity Framework Core パフォーマンスの診断とボトルネックの特定
author: roji
ms.date: 12/1/2020
uid: core/performance/performance-diagnosis
ms.openlocfilehash: 9416acf3326056ef7a5d732c4bd456dac751167b
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657721"
---
# <a name="performance-diagnosis"></a>パフォーマンス診断

このセクションでは、EF アプリケーションでパフォーマンスの問題を検出する方法について説明します。問題のある領域が特定されたら、それらをさらに分析して根本的な問題を特定する方法についても説明します。 問題を注意深く診断して調査してから結論にジャンプし、問題の根本点を想定しないようにすることが重要です。

## <a name="identifying-slow-database-commands-via-logging"></a>ログ記録による低速データベースコマンドの識別

1日の終わりに、EF はデータベースに対して実行するコマンドを準備して実行します。リレーショナルデータベースでは、ADO.NET database API を使用して SQL ステートメントを実行します。 特定のクエリの実行に時間がかかりすぎる場合 (インデックスが不足している場合など)、コマンド実行ログを調べ、実際にかかった時間を観察することで、このことが検出される可能性があります。

EF を使用すると、 [単純なログ](xref:core/logging-events-diagnostics/simple-logging) 記録または [拡張機能](xref:core/logging-events-diagnostics/extensions-logging)を使用して、コマンドの実行時間を簡単にキャプチャできます。

### <a name="simple-logging"></a>[シンプルなログ](#tab/simple-logging)

[!code-csharp[Main](../../../samples/core/Performance/BloggingContext.cs#SimpleLogging)]

### <a name="microsoftextensionslogging"></a>[Microsoft.Extensions.Logging](#tab/microsoft-extensions-logging)

[!code-csharp[Main](../../../samples/core/Performance/ExtensionsLoggingContext.cs#ExtensionsLogging)]

***

ログ記録レベルがに設定されている場合 `LogLevel.Information` 、EF は、コマンドの実行ごとにログメッセージを出力します。

```log
info: 06/12/2020 09:12:36.117 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (4ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT [b].[Id], [b].[Name]
      FROM [Blogs] AS [b]
      WHERE [b].[Name] = N'foo'
```

上記のコマンドでは、4ミリ秒かかりました。 特定のコマンドの実行に予想以上の時間がかかる場合は、パフォーマンスの問題の原因が考えられ、それに焦点を当てることで、パフォーマンスの低下の原因を把握できるようになりました。 コマンドログを使用すると、予期しないデータベースのラウンドトリップが行われているケースを明らかにすることもできます。これは、1つだけが必要な複数のコマンドとして表示されます。

> [!WARNING]
> 実稼働環境でコマンド実行ログを有効にしたままにすることは、通常は正しくありません。 ログ自体によってアプリケーションの速度が低下し、サーバーのディスクにいっぱいになる大きなログファイルをすばやく作成できます。 アプリケーションを慎重に監視しながら、または実稼働前システムのログデータをキャプチャするために、短時間でログ記録を保持することをお勧めします。

## <a name="correlating-database-commands-to-linq-queries"></a>LINQ クエリへのデータベースコマンドの関連付け

コマンド実行ログの問題の1つとして、SQL クエリと LINQ クエリを関連付けることが困難な場合があります。 EF によって実行される SQL コマンドは、生成元の LINQ クエリと大きく異なる可能性があります。 この問題を解決するには、EF の [クエリタグ](xref:core/querying/tags) 機能を使用します。これにより、SQL クエリに小さな識別コメントを挿入できます。

[!code-csharp[Main](../../../samples/core/Querying/Tags/Program.cs#BasicQueryTag)]

タグがログに表示されます。

```sql
-- This is my spatial query!

SELECT TOP(@__p_1) [p].[Id], [p].[Location]
FROM [People] AS [p]
ORDER BY [p].[Location].STDistance(@__myLocation_0) DESC
```

多くの場合、この方法でアプリケーションの主要なクエリにタグを付けて、コマンドの実行ログをすぐに読み取ることができるようにします。

## <a name="other-interfaces-for-capturing-performance-data"></a>パフォーマンスデータをキャプチャするためのその他のインターフェイス

EF のログ記録機能には、コマンドの実行時間をキャプチャするためのさまざまな選択肢があります。これは、より強力です。 データベースには、通常、独自のトレースおよびパフォーマンス分析ツールが付属しています。これらのツールは、通常、単純な実行時間を超えて、より豊富でデータベース固有の情報を提供します。実際のセットアップ、機能、使用状況は、データベースによって大きく異なります。

たとえば、 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) は、SQL Server インスタンスに接続し、重要な管理とパフォーマンスの情報を提供できる強力なクライアントです。 詳細については、このセクションでは説明しませんが、注意が必要な2つの機能は、サーバーアクティビティのライブダッシュボード (最もコストの高いクエリを含む) と[拡張イベント (XEvent)](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)機能を提供する[利用状況モニター](/sql/relational-databases/performance-monitor/open-activity-monitor-sql-server-management-studio)です。これにより、正確なニーズに合わせて調整できる任意のデータキャプチャセッションを定義できます。 [監視に関する SQL Server のドキュメント](/sql/relational-databases/performance/monitor-and-tune-for-performance) では、これらの機能に加え、他の機能についても詳しく説明しています。

パフォーマンスデータをキャプチャするもう1つの方法は、EF またはデータベースドライバーによって自動的に生成された情報をインターフェイス経由で収集 `DiagnosticSource` し、そのデータを分析してダッシュボードに表示することです。 Azure を使用している場合、 [Azure アプリケーション Insights](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-performance) では、このような強力な監視機能がすぐに利用可能になり、web 要求の処理速度の分析において、データベースのパフォーマンスとクエリの実行時間が統合されます。 詳細については、 [Application Insights パフォーマンスのチュートリアル](/azure/azure-monitor/learn/tutorial-performance)と [Azure SQL analytics のページ](/azure/azure-monitor/insights/azure-sql)を参照してください。

## <a name="inspecting-query-execution-plans"></a>クエリ実行プランの検査

最適化が必要な問題のあるクエリを特定したら、通常はクエリの *実行プラン* を分析します。 データベースが SQL ステートメントを受け取ると、通常、そのプランの実行方法のプランが生成されます。これには、インデックスが定義されているかどうか、テーブルにどのくらいのデータが存在するかに基づいて、複雑な意思決定が必要になることがあります (ちなみに、最適なパフォーマンスを実現するために、通常はサーバーでプランそのものをキャッシュする必要があります)。 通常、リレーショナルデータベースは、クエリのさまざまな部分に対して計算されたコストと共に、ユーザーがクエリプランを表示する方法を提供します。これは、クエリを改善するために非常に重要です。

SQL Server の概要については、 [クエリ実行プラン](/sql/relational-databases/performance/execution-plans)に関するドキュメントを参照してください。 一般的な分析ワークフローでは、 [SQL Server Management Studio](/sql/relational-databases/performance/display-an-actual-execution-plan)を使用し、上記のいずれかの方法で特定された低速クエリの SQL を貼り付け、 [グラフィカルな実行プランを生成](/sql/relational-databases/performance/display-an-actual-execution-plan)します。

![SQL Server 実行プランを表示する](_static/actualexecplan.png)

実行プランは一見複雑に思えるかもしれませんが、理解を深めるには少し時間がかかります。 特に重要なのは、プランの各ノードに関連付けられているコストを書き留め、さまざまなノードでインデックスがどのように使用されているか (またはない) を特定することです。

上記の情報は SQL Server に固有のものですが、他のデータベースでは通常、同様の視覚エフェクトを持つ同じ種類のツールが提供されます。

> [!IMPORTANT]
> データベースでは、データベース内の実際のデータに応じて異なるクエリプランが生成されることがあります。 たとえば、テーブルに少数の行しか含まれていない場合、データベースはそのテーブルのインデックスを使用しないことを選択できますが、フルテーブルスキャンを実行するように選択できます。 テストデータベースでクエリプランを分析する場合は、実稼働システムと同様のデータが含まれていることを必ず確認してください。

## <a name="event-counters"></a>イベント カウンター

上記のセクションでは、コマンドに関する情報を取得する方法と、これらのコマンドをデータベースで実行する方法に重点を置いています。 さらに、EF では、EF 自体の内部で起きていることと、アプリケーションでどのように使用されているかについて、より低いレベルの情報を提供する一連の *イベントカウンター* を公開しています。 これらのカウンターは、特定のパフォーマンスの問題とパフォーマンスの異常を診断するのに非常に役立ちます。たとえば、定数の再コンパイルや、破棄されていない DbContext リークなどを引き起こす [クエリキャッシュの問題](xref:core/performance/advanced-performance-topics#dynamically-constructed-queries) などです。

詳細については、 [EF のイベントカウンター](xref:core/logging-events-diagnostics/event-counters) の専用ページを参照してください。

## <a name="benchmarking-with-ef-core"></a>EF Core を使用したベンチマーク

1日の終わりに、クエリの書き込みまたは実行の特定の方法が他より高速であるかどうかを知る必要がある場合があります。 回答を想定したり speculate したりすることは決して重要ではありません。答えを得るためのクイックベンチマークを非常に簡単にまとめることができます。 ベンチマークを作成する場合は、よく知られた [BenchmarkDotNet](https://benchmarkdotnet.org/index.html) ライブラリを使用することを強くお勧めします。これは、ユーザーが独自のベンチマークを作成するときに発生する多くの落とし穴を処理します。ウォームアップイテレーションをいくつか実行したことを確認できます。 ベンチマークの実際の実行回数と、その理由 EF Core のベンチマークの外観を見てみましょう。

> [!TIP]
> 以下のソースの完全なベンチマークプロジェクトは、 [こちら](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/AverageBlogRanking.cs)から入手できます。 これをコピーして、独自のベンチマークのテンプレートとして使用することをお勧めします。

単純なベンチマークシナリオとして、データベース内のすべてのブログの平均順位を計算する次のような方法を比較してみましょう。

* すべてのエンティティを読み込んで、個々のランキングを合計し、平均を計算します。
* 上記と同じですが、非追跡クエリのみを使用します。 Id の解決は実行されず、エンティティが変更の追跡の目的でスナップショットを使用していないため、この処理がより高速になります。
* 順位付けのみを射影することにより、ブログエンティティインスタンス全体をまったく読み込まないようにします。 によって、ブログエンティティ型の他の不要な列が転送されるのを防ぐことができます。
* クエリの一部にすることで、データベース内の平均を計算します。 すべてがデータベースで計算され、結果だけがクライアントに返されるため、これは最速の方法です。

BenchmarkDotNet では、単純なメソッドとしてアプリケーションとしてコードを記述します。単体テストと BenchmarkDotNet では、各メソッドが十分な反復回数だけ自動的に実行され、どれだけの時間とメモリが割り当てられるかが確実に測定されます。 さまざまな方法を次に示します ([完全なベンチマークコードをここで見ることができ](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/AverageBlogRanking.cs)ます)。

### <a name="load-entities"></a>[エンティティの読み込み](#tab/load-entities)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=LoadEntities)]

### <a name="load-entities-no-tracking"></a>[エンティティの読み込み、追跡なし](#tab/load-entities-no-tracking)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=LoadEntitiesNoTracking)]

### <a name="project-only-ranking"></a>[プロジェクトのみの順位付け](#tab/project-only-ranking)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=ProjectOnlyRanking)]

### <a name="calculate-in-database"></a>[データベースで計算](#tab/calculate-in-database)

[!code-csharp[Main](../../../samples/core/Benchmarks/AverageBlogRanking.cs?name=CalculateInDatabase)]

***

BenchmarkDotNet によって出力された結果は次のようになります。

|                 Method |       平均 |    エラー |   StdDev |     中央値 | 比率 | RatioSD |    Gen 0 |   Gen 1 | Gen 2 |  Allocated |
|----------------------- |-----------:|---------:|---------:|-----------:|------:|--------:|---------:|--------:|------:|-----------:|
|           LoadEntities | 2860.4 us | 54.31 us | 93.68 us | 2844.5 us |  4.55 |    0.33 | 210.9375 | 70.3125 |     - | 1309.56 KB |
| LoadEntitiesNoTracking | 1353.0 us | 21.26 us | 18.85 us | 1355.6 us |  2.10 |    0.14 |  87.8906 |  3.9063 |     - |  540.09 KB |
|     この順位付け |   910.9 us | 20.91 us | 61.65 us |   892.9 us |  1.46 |    0.14 |  41.0156 |  0.9766 |     - |  252.08 KB |
|    CalculateInDatabase |   627.1 us | 14.58 us | 42.54 us |   626.4 us |  1.00 |    0.00 |   4.8828 |       - |     - |   33.27 KB |

> [!NOTE]
> メソッドがメソッド内でコンテキストをインスタンス化および破棄すると、これらの操作はベンチマークに対してカウントされますが、厳密にはクエリプロセスには含まれません。 これは、2つの代替方法を比較することが目的である場合 (コンテキストのインスタンス化と破棄は同じであるため)、操作全体に対してより包括的な測定を行うことであるかどうかに関係ありません。

BenchmarkDotNet の1つの制限は、指定したメソッドの単純な単一スレッドパフォーマンスを測定するため、同時実行シナリオのベンチマークには適していません。

> [!IMPORTANT]
> ベンチマークの実行時には、実稼働データと同様のデータをデータベースに保持するようにしてください。そうしないと、ベンチマーク結果が実稼働環境での実際のパフォーマンスを表していない可能性があります。
