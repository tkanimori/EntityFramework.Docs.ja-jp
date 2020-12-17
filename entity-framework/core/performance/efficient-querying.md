---
title: 効率的なクエリ-EF Core
description: Entity Framework Core を使用した効率的なクエリのパフォーマンスガイド
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-querying
ms.openlocfilehash: acd5388745e74a42925c8500ce610aef83e75384
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657722"
---
# <a name="efficient-querying"></a>効率的なクエリ

効率的にクエリを実行することは、インデックス、関連するエンティティの読み込み方法、その他多くの要素として、さまざまな主題を対象としています。 このセクションでは、クエリの実行速度を向上させる一般的なテーマと、ユーザーが通常遭遇する落とし穴について詳しく説明します。

## <a name="use-indexes-properly"></a>インデックスを適切に使用する

クエリが高速に実行されるかどうかを判断する主な要因は、適切なインデックスを使用するかどうかを決定することです。データベースは通常、大量のデータを保持するために使用され、テーブル全体をスキャンするクエリは、重大なパフォーマンスの問題の原因になります。 インデックス作成の問題は、特定のクエリでインデックスを使用するかどうかがすぐに明らかにならないため、簡単に見つけることができません。 次に例を示します。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#Indexes)]

インデックス作成の問題を見つけるには、まず、低速クエリを特定し、データベースのお気に入りのツールを使用してクエリプランを調べることをお勧めします。その方法の詳細については、「 [パフォーマンス診断](xref:core/performance/performance-diagnosis) 」ページを参照してください。 クエリプランでは、クエリがテーブル全体を通過するか、インデックスを使用するかが表示されます。

一般的な規則として、インデックスを使用したり、関連するパフォーマンスの問題を診断したりするための特別な EF の知識はありません。インデックスに関連する一般的なデータベースナレッジは、ef を使用しないアプリケーションの場合と同様に、EF アプリケーションに関連しています。 次に、インデックスを使用する場合に留意すべき一般的なガイドラインをいくつか示します。

* インデックスはクエリを高速化しますが、最新の状態に保つ必要があるため、更新の速度も低下します。 不要なインデックスを定義しないでください。インデックス [フィルター](xref:core/modeling/indexes#index-filter) を使用してインデックスを行のサブセットに制限することを検討してください。これにより、このオーバーヘッドが減少します。
* 複合インデックスを使用すると、複数の列をフィルター処理するクエリを高速化できますが、順序によっては、インデックスのすべての列に対してフィルター処理を行わないクエリを高速化することもできます。 たとえば、a 列と b 列のインデックスを使用すると、a と B によってフィルター処理されるクエリだけでなく、によってのみフィルター処理が行われますが、クエリのフィルター処理は B だけで済みます。
* クエリが列に対する式によってフィルター処理される場合 (例: `price / 2` )、単純なインデックスは使用できません。 ただし、 [格納されている保存済みの永続化列](xref:core/modeling/generated-properties#computed-columns) を式に対して定義し、それに対してインデックスを作成することができます。 一部のデータベースでは、式のインデックスもサポートされており、式によってフィルター処理を高速化するために直接使用できます。
* 異なるデータベースを使用すると、さまざまな方法でインデックスを構成できます。多くの場合、EF Core プロバイダーは Fluent API を使用してこれらを公開します。 たとえば、SQL Server プロバイダーでは、インデックスが [クラスター化](xref:core/providers/sql-server/indexes#clustering)されているかどうかを構成したり、 [FILL FACTOR](xref:core/providers/sql-server/indexes#fill-factor)を設定したりできます。 詳細については、プロバイダーのドキュメントを参照してください。

## <a name="project-only-properties-you-need"></a>必要なプロパティのみプロジェクト

EF Core を使用すると、エンティティインスタンスのクエリを非常に簡単に実行できるようになり、そのインスタンスをコードで使用できます。 ただし、エンティティインスタンスに対してクエリを実行すると、データベースから必要以上に多くのデータが返されることがよくあります。 以下、具体例に沿って説明します。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#ProjectEntities)]

このコードでは、実際には各ブログのプロパティが必要です `Url` が、ブログエンティティ全体がフェッチされ、不要な列がデータベースから転送されます。

```sql
SELECT [b].[BlogId], [b].[CreationDate], [b].[Name], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
```

これを最適化するには、を使用して、 `Select` プロジェクトを除外する列を EF に指示します。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#ProjectSingleProperty)]

結果の SQL では、必要な列だけが返されます。

```csharp
SELECT [b].[Url]
FROM [Blogs] AS [b]
```

複数の列を射影する必要がある場合は、必要なプロパティを持つ C# 匿名型に射影します。

この手法は読み取り専用クエリには非常に便利ですが、EF の変更の追跡はエンティティインスタンスでのみ機能するため、フェッチされたブログを *更新* する必要がある場合は、さらに複雑になります。 変更したブログインスタンスをアタッチし、変更されたプロパティを EF に指示することにより、エンティティ全体を読み込まずに更新を実行することができますが、これはより高度な手法であり、価値がない場合があります。

## <a name="limit-the-resultset-size"></a>Resultset のサイズを制限する

既定では、クエリはフィルターに一致するすべての行を返します。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#NoLimit)]

返される行の数はデータベース内の実際のデータによって異なるため、データベースから読み込まれるデータの量、結果によってどの程度のメモリが使用されるか、およびこれらの結果を処理するときに生成される追加の負荷 (ネットワーク経由でユーザーブラウザーに送信するなど) を知ることはできません。 とてもでは、テストデータベースにはほとんどのデータが含まれていないため、テスト中にすべて正常に機能しますが、クエリが実際のデータで実行を開始したときにパフォーマンスの問題が突然表示され、多くの行が返されます。

結果として、通常、結果の数を制限することになります。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#Limit25)]

少なくとも、データベースにより多くの行が存在する可能性があることを示すメッセージが UI に表示される場合があります (他の方法でも取得できます)。 本格的なソリューションでは、 *ページング* を実装します。 UI では一度に特定の数の行のみが表示され、ユーザーは必要に応じて次のページに進むことができます。通常、この操作では、 <xref:System.Linq.Enumerable.Take%2A> 演算子と演算子を組み合わせて、 <xref:System.Linq.Enumerable.Skip%2A> 結果セット内の特定の範囲を選択します。

## <a name="avoid-cartesian-explosion-when-loading-related-entities"></a>関連エンティティの読み込み時にデカルト爆発を回避する

リレーショナル データベースの場合、単一クエリで JOIN を使用すると、関連するすべてのエンティティが読み込まれます。

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

一般的なブログに複数の関連する投稿がある場合、これらの投稿の行によってブログの情報が複製されます。 この重複により、いわゆる "デカルト爆発" の問題が発生します。 さらに一対多リレーションシップが読み込まれると、重複するデータの量が増加し、アプリケーションのパフォーマンスに悪影響を及ぼす可能性があります。

EF では、"split queries" を使用してこの影響を回避できます。これにより、個別のクエリを介して関連エンティティが読み込まれます。 詳細については、 [split と single クエリに関するドキュメント](xref:core/querying/single-split-queries)を参照してください。

> [!NOTE]
> [分割クエリ](xref:core/querying/single-split-queries)の現在の実装では、各クエリに対してラウンドトリップを実行します。 今後、この機能強化を予定しており、すべてのクエリを1回のラウンドトリップで実行します。

## <a name="load-related-entities-eagerly-when-possible"></a>可能な場合は、関連エンティティの集中的を読み込む

このセクションを続行する前に [、関連エンティティの専用ページ](xref:core/querying/related-data) を読むことをお勧めします。

関連エンティティを扱うときは、通常、読み込む必要があることを事前に把握しています。一般的な例として、特定の一連のブログとすべての投稿が読み込まれます。 これらのシナリオでは、常に [一括読み込み](xref:core/querying/related-data/eager)を使用することをお勧めします。これにより、EF が1つのラウンドトリップで必要なすべてのデータをフェッチできるようになります。 EF Core 5.0 で導入されたフィルターされた [インクルード](xref:core/querying/related-data/eager#filtered-include) 機能では、読み込みプロセスを集中的に維持し、1回のラウンドトリップで取り上げことができるように、読み込む関連エンティティを制限することもできます。

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

他のシナリオでは、プリンシパルエンティティを取得する前に、どの関連エンティティが必要になるかわからない場合があります。 たとえば、いくつかのブログを読み込んだときに、そのブログの投稿に関心があるかどうかを知るために、他のデータソース (場合によっては webservice) を参照することが必要になる場合があります。 このような場合は、 [明示的](xref:core/querying/related-data/explicit) または [遅延](xref:core/querying/related-data/lazy) 読み込みを使用して関連エンティティを個別に取得し、ブログの投稿のナビゲーションを設定できます。 これらの方法はあまり使用されないため、データベースへの追加のラウンドトリップが必要であることに注意してください。これは、速度の低下の原因になります。特定のシナリオによっては、追加のラウンドトリップを実行するだけでなく、必要な投稿だけを選択的に取得するよりも、常にすべての投稿を読み込む方が効率的です。

### <a name="beware-of-lazy-loading"></a>遅延読み込みに注意してください

[遅延読み込み](xref:core/querying/related-data/lazy) は、コードによってアクセスされるときにデータベースから関連エンティティが自動的に読み込まれる EF Core ため、データベースロジックを記述する非常に便利な方法のように思われます。 これにより、必要のない ( [明示的な読み込み](xref:core/querying/related-data/explicit)などの) 関連エンティティが読み込まれなくなるため、プログラマは関連エンティティを完全に処理する必要がなくなります。 ただし、遅延読み込みは特に不要な余分なラウンドトリップを生成することが原因で、アプリケーションの処理速度が低下する可能性があります。

以下、具体例に沿って説明します。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#NPlusOne)]

この一見無害なコードは、すべてのブログとその投稿を反復処理して、それを印刷します。EF Core の [ステートメントログ記録](xref:core/logging-events-diagnostics/index) をオンにすると、次のようになります。

```console
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      SELECT [b].[BlogId], [b].[Rating], [b].[Url]
      FROM [Blogs] AS [b]
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (5ms) [Parameters=[@__p_0='1'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[@__p_0='2'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (1ms) [Parameters=[@__p_0='3'], CommandType='Text', CommandTimeout='30']
      SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[Title]
      FROM [Post] AS [p]
      WHERE [p].[BlogId] = @__p_0

... and so on
```

どうなっているのでしょうか? 上記の単純なループに対してこれらのクエリがすべて送信されるのはなぜですか。 遅延読み込みを使用すると、投稿のプロパティにアクセスしたときにのみ、ブログの投稿が読み込まれます。その結果、内部 foreach の各反復処理では、独自のラウンドトリップで、追加のデータベースクエリがトリガーされます。 その結果、最初のクエリによってすべてのブログが読み込まれた後、 *ブログごと* に別のクエリが作成され、すべての投稿が読み込まれます。これは、 *N + 1* の問題と呼ばれることもあり、非常に重大なパフォーマンスの問題が発生する可能性があります。

ブログの投稿をすべて必要としている場合は、代わりに一括読み込みを使用するのが理にかなっています。 [Include](xref:core/querying/related-data/eager#eager-loading)演算子を使用して読み込みを実行できますが、必要なのはブログの url だけであるため、必要なもののみを[読み込む](xref:core/performance/efficient-updating#project-only-properties-you-need)必要があるためです。 そのため、代わりに射影を使用します。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#EagerlyLoadRelatedAndProject)]

これにより、すべてのブログとその投稿を1つのクエリで取得 EF Core ようになります。 場合によっては、 [分割クエリ](xref:core/querying/single-split-queries)を使用してデカルト爆発効果を回避すると便利な場合もあります。

> [!WARNING]
> 遅延読み込みでは、N + 1 問題を誤ってトリガーすることが非常に簡単であるため、この問題を回避することをお勧めします。 一括読み込みまたは明示的な読み込みでは、データベースのラウンドトリップが発生したときにソースコードが非常に明確になります。

## <a name="buffering-and-streaming"></a>バッファリングとストリーミング

バッファリングとは、すべてのクエリ結果をメモリに読み込むことを指します。一方、ストリーミングでは、EF はアプリケーションを毎回1つの結果にすることを意味し、メモリ内に結果セット全体が含まれることはありません。 原則として、ストリーミングクエリのメモリ要件は固定されています。クエリが1行または1000を返すかどうかは同じです。一方、バッファリングクエリでは、より多くのメモリが必要になるため、より多くの行が返されます。 大きな結果セットを生成するクエリの場合、これは重要なパフォーマンス要因になることがあります。

クエリのバッファーまたはストリームがどのように評価されるかによって異なります。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#BufferingAndStreaming)]

クエリから返される結果がわずかである場合は、これについて心配する必要はありません。 ただし、クエリで大量の行が返される可能性がある場合は、バッファー処理ではなくストリーミングすることを検討してください。

> [!NOTE]
> <xref:System.Linq.Enumerable.ToList%2A>結果に対して別の LINQ 演算子を使用する場合は、またはを使用しない <xref:System.Linq.Enumerable.ToArray%2A> でください。これにより、すべての結果が不必要にメモリにバッファーされます。 代わりに、<xref:System.Linq.Enumerable.AsEnumerable%2A> を使用してください。

### <a name="internal-buffering-by-ef"></a>EF による内部バッファリング

特定の状況では、EF はクエリの評価方法に関係なく、内部的に結果セットをバッファーします。 この問題が発生する2つのケースを次に示します。

* 再試行の実行戦略が適用されている場合。 これは、後でクエリが再試行された場合に同じ結果が返されるようにするために行われます。
* [Split クエリ](xref:core/querying/single-split-queries)を使用すると、SQL SERVER で MARS が有効になっていない限り、最後のクエリ以外のすべての結果セットがバッファーされます。 これは、通常、複数のクエリの結果セットを同時にアクティブにすることができないためです。

この内部バッファリングは、LINQ 演算子を介して発生するバッファリングに加えて発生することに注意してください。 たとえば、クエリでを使用し、再試行中の実行戦略を設定した場合、 <xref:System.Linq.Enumerable.ToList%2A> 結果セットは *2 回*(EF によって内部的に1回、によって) メモリに読み込まれ <xref:System.Linq.Enumerable.ToList%2A> ます。

## <a name="tracking-no-tracking-and-identity-resolution"></a>追跡、追跡なし、および id 解決

このセクションを続行する前に [、追跡と追跡なしの専用ページ](xref:core/querying/tracking) を読むことをお勧めします。

EF は、が呼び出されたときに、エンティティインスタンスの変更が検出され、永続化されるように、既定でエンティティインスタンスを追跡 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> します。 追跡クエリのもう1つの影響として、EF では、インスタンスが既にデータ用に読み込まれているかどうかが検出され、新しいインスタンスを返さずに、追跡対象のインスタンスが自動的に返されます。これは、 *id 解決* と呼ばれます。 パフォーマンスの観点から見ると、変更の追跡は次のことを意味します。

* EF は、内部的に追跡対象インスタンスのディクショナリを保持します。 新しいデータが読み込まれると、EF は、そのエンティティのキー (id 解決) に対して既にインスタンスが追跡されているかどうかを確認するために、ディクショナリをチェックします。 ディクショナリのメンテナンスと参照は、クエリの結果を読み込むときに時間がかかります。
* 読み込まれたインスタンスをアプリケーションに渡す前に、EF はインスタンスを *スナップショット* に保持し、スナップショットを内部に保持します。 が呼び出されると、 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> アプリケーションのインスタンスとスナップショットが比較され、永続化される変更が検出されます。 スナップショットはより多くのメモリを占有し、スナップショットプロセス自体には時間がかかります。場合によっては、 [値の比較](xref:core/modeling/value-comparers)によって、より効率的なスナップショット動作を指定したり、変更追跡プロキシを使用してスナップショットプロセスを完全にバイパスしたりすることができます (ただし、これには独自の欠点があります)。

変更がデータベースに保存されない読み取り専用のシナリオでは、 [追跡なしのクエリ](xref:core/querying/tracking#no-tracking-queries)を使用して上記のオーバーヘッドを回避できます。 ただし、追跡なしのクエリは id 解決を実行しないため、他の複数の読み込まれた行によって参照されているデータベース行は、異なるインスタンスとして具体化されます。

例として、データベースから多数の投稿を読み込んでいることと、各投稿で参照されているブログを読み込んでいるとします。 同じブログを参照するために100の投稿が発生した場合、追跡クエリは id 解決によってこれを検出し、すべての Post インスタンスは同じ重複除去されていないブログインスタンスを参照します。 これに対して、非追跡クエリは同じブログ100回を複製し、それに応じてアプリケーションコードを記述する必要があります。

次に示すのは、クエリの追跡と追跡なしの動作を比較するベンチマークの結果であり、20件の投稿を含む10個のブログを読み込んでいます。 [ソースコードはこちらで入手でき](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Benchmarks/QueryTrackingBehavior.cs)ます。独自の測定値のベースとして自由に使用できます。

|       Method | NumBlogs | NumPostsPerBlog |       平均 |    エラー |   StdDev |     中央値 | 比率 | RatioSD |   Gen 0 |   Gen 1 | Gen 2 | Allocated |
|------------- |--------- |---------------- |-----------:|---------:|---------:|-----------:|------:|--------:|--------:|--------:|------:|----------:|
|   AsTracking |       10 |              20 | 1414.7 us | 27.20 us | 45.44 us | 1405.5 us |  1.00 |    0.00 | 60.5469 | 13.6719 |     - | 380.11 KB |
| AsNoTracking |       10 |              20 |   993.3 us | 24.04 us | 65.40 us |   966.2 us |  0.71 |    0.05 | 37.1094 |  6.8359 |     - | 232.89 KB |

最後に、追跡なしのクエリを使用し、返されたインスタンスをコンテキストにアタッチして、変更を行うかを指定することによって、変更の追跡のオーバーヘッドなしに更新を実行することができます。 これにより、変更の追跡の負荷が EF からユーザーに転送されます。また、プロファイルまたはベンチマークによって変更追跡のオーバーヘッドが許容されない場合にのみ、この作業を行う必要があります。

## <a name="using-raw-sql"></a>生の SQL の使用

場合によっては、クエリに対してより最適化された SQL が存在し、EF では生成されません。 これは、SQL コンストラクトが、サポートされていないデータベースに固有の拡張機能である場合、または EF がまだ変換していない場合にのみ発生する可能性があります。 このような場合、手動で SQL を記述することでパフォーマンスを大幅に向上させることができ、EF ではこれを行うためのいくつかの方法がサポートされています。

* 生の SQL は、などの [クエリで直接](xref:core/querying/raw-sql)使用します <xref:Microsoft.EntityFrameworkCore.RelationalQueryableExtensions.FromSqlRaw%2A> 。 EF では、通常の LINQ クエリを使用して生の SQL を構成することもできます。これにより、未加工の SQL でクエリの一部だけを表現できます。 これは、コードベースの1つのクエリで、生の SQL のみを使用する必要がある場合に適した手法です。
* [ユーザー定義関数](xref:core/querying/database-functions)(UDF) を定義し、クエリからそれを呼び出します。 5.0 以降、EF では、Udf が完全な結果セットを返すことができることに注意してください。これはテーブル値関数 (Tvf) と呼ばれています。また、を関数にマップして、別のテーブルと同じように表示することもでき `DbSet` ます。
* クエリでデータベースビューとクエリを定義します。 関数とは異なり、ビューではパラメーターを使用できないことに注意してください。

> [!NOTE]
> 通常、未加工の SQL は最後の手段として使用する必要があります。 EF が必要な SQL を生成できないことを確認した後、特定のクエリに対してパフォーマンスが重要であることを確認してください。 生の SQL を使用すると、大幅なメンテナンスの欠点が生じます。

## <a name="asynchronous-programming"></a>非同期プログラミング

一般的な規則として、アプリケーションをスケーラブルにするためには、同期型ではなく非同期 Api (など) を常に使用することが重要です <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 。 同期 Api は、データベース i/o が実行されている間、スレッドをブロックします。これにより、スレッドの必要性と発生する必要があるスレッドコンテキストスイッチの数が増加します。

詳細については、「 [非同期プログラミング](xref:core/miscellaneous/async)」のページを参照してください。

> [!WARNING]
> 同一のアプリケーションに同期コードと非同期コードを混在させないでください。誤ってスレッドプール不足の問題が発生した場合は、非常に簡単にトリガーできます。
