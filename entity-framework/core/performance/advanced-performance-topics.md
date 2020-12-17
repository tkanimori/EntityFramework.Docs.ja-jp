---
title: 高度なパフォーマンスのトピック
description: Entity Framework Core のパフォーマンスに関する詳細トピック
author: rick-anderson
ms.author: riande
ms.date: 12/9/2020
uid: core/performance/advanced-performance-topics
ms.openlocfilehash: 3c0340e1b36cbbb96d23db0633cb2eebc04dd970
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657763"
---
# <a name="advanced-performance-topics"></a>高度なパフォーマンスのトピック

## <a name="dbcontext-pooling"></a>DbContext プール

`AddDbContextPool` インスタンスのプールを有効に `DbContext` します。 コンテキストプーリングは、要求ごとに新しいインスタンスを作成するのではなく、コンテキストインスタンスを再利用することで、web サーバーなどの大規模なシナリオでスループットを向上させることができます。

EF Core を使用する ASP.NET Core アプリの一般的なパターンには、カスタム <xref:Microsoft.EntityFrameworkCore.DbContext> 型を [依存関係挿入](/aspnet/core/fundamentals/dependency-injection) コンテナーに登録し、コントローラーまたは Razor Pages のコンストラクターパラメーターを使用してその型のインスタンスを取得する作業が含まれます。 コンストラクターインジェクションを使用して、要求ごとに新しいコンテキストインスタンスが作成されます。

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 再利用可能なコンテキストインスタンスのプールを有効にします。 コンテキストプーリングを使用するには、 `AddDbContextPool` サービスの登録時にではなく、メソッドを使用し `AddDbContext` ます。

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

を使用すると、コンテキストインスタンスが要求されたときに `AddDbContextPool` 、EF は、プール内に使用可能なインスタンスがあるかどうかを最初に確認します。 要求処理が終わると、インスタンス上のあらゆる状態がリセットされ、インスタンス自体はプールに戻ります。

これは、ADO.NET プロバイダーでの接続プールの動作と概念的に似ており、コンテキストインスタンスの初期化にかかるコストを節約できるという利点があります。

`poolSize`のパラメーターは、 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> プールに保持されているインスタンスの最大数を設定します。 を `poolSize` 超えた場合、新しいコンテキストインスタンスはキャッシュされず、EF は必要に応じてインスタンスを作成する非プール動作にフォールバックします。

### <a name="limitations"></a>制限事項

コンテキストの初期化が大幅なコストであることを示すために、アプリのプロファイリングとテストを行う必要があります。

`AddDbContextPool` では、コンテキストのメソッドで実行できる内容にいくつかの制限があり `OnConfiguring` ます。

> [!WARNING]
> 状態を維持するアプリでコンテキストプーリングを使用することは避けてください。 たとえば、要求間で共有しないようなコンテキストのプライベートフィールドです。 EF Core は、コンテキストインスタンスをプールに追加する前に、認識している状態のみをリセットします。

コンテキストプーリングは、要求間で同じコンテキストインスタンスを再利用することによって機能します。 これは、インスタンス自体を使用して、永続化できるように、 [シングルトン](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) として効果的に登録されることを意味します。

コンテキストプーリングは、解決されたサービスを含むコンテキスト構成が要求間で固定されているシナリオを対象としています。 [スコープ](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)が指定されたサービスが必要な場合、または構成を変更する必要がある場合は、プーリングを使用しないでください。 プールからのパフォーマンスの向上は通常、高度に最適化されたシナリオ以外ではごくわずかです。

## <a name="query-caching-and-parameterization"></a>クエリのキャッシュとパラメーター化

EF が実行のために LINQ クエリツリーを受け取ると、そのツリーを SQL クエリに "コンパイル" する必要があります。 これは大きなプロセスであるため、EF はクエリツリー *図形* によってクエリをキャッシュします。同じ構造を持つクエリは、内部キャッシュされたコンパイル出力を再利用し、繰り返しコンパイルをスキップできます。 異なるクエリでは、異なる *値* を参照する場合がありますが、これらの値が適切にパラメーター化されていれば、構造は同じであり、キャッシュは正常に機能します。

次の2つのクエリについて考えてみます。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithConstants)]

式ツリーには異なる定数が含まれているため、式ツリーは異なり、これらの各クエリは EF Core によって個別にコンパイルされます。 さらに、各クエリでは、若干異なる SQL コマンドが生成されます。

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog1'

SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = N'blog2'
```

SQL が異なるため、データベースサーバーでは、同じプランを再利用するのではなく、両方のクエリに対してクエリプランを生成することが必要になる場合があります。

クエリにわずかな変更を加えると、大幅に変更される可能性があります。

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#QueriesWithParameterization)]

ブログ名が *パラメーター化* されるようになったので、両方のクエリのツリー構造が同じで、EF だけをコンパイルする必要があります。 生成された SQL もパラメーター化されるため、データベースは同じクエリプランを再利用できます。

```sql
SELECT TOP(1) [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
WHERE [b].[Name] = @__blogName_0
```

すべてのクエリをパラメーター化する必要がないことに注意してください。定数を持つクエリをいくつか作成することは十分です。実際には、データベース (および EF) は、クエリがパラメーター化されるときには不可能な定数に対して特定の最適化を実行することがあります。 適切なパラメーター化が重要な例については、 [動的に作成](#dynamically-constructed-queries) されたクエリに関するセクションを参照してください。

> [!NOTE]
> EF Core の [イベントカウンター](xref:core/logging-events-diagnostics/event-counters) は、クエリキャッシュのヒット率を報告します。 通常のアプリケーションでは、ほとんどのクエリが少なくとも1回実行されると、プログラムの起動後すぐにこのカウンターが100% に達します。 このカウンターの値が100% 未満の場合は、アプリケーションがクエリキャッシュを損なう原因になっている可能性があることを示しています。これを調査することをお勧めします。

> [!NOTE]
> データベースがキャッシュを管理する方法クエリプランはデータベースに依存します。 たとえば、SQL Server は、暗黙的に LRU クエリプランキャッシュを保持しますが、PostgreSQL では使用できません (ただし、準備されたステートメントでは非常に類似した終了効果が生成される可能性があります)。 詳細については、データベースのドキュメントを参照してください。

## <a name="dynamically-constructed-queries"></a>動的に作成されたクエリ

場合によっては、ソースコードで完全に指定するのではなく、LINQ クエリを動的に構築する必要があります。 これは、たとえば、クライアントから任意のクエリの詳細を受け取る web サイトで、開いているクエリ演算子 (並べ替え、フィルター処理、ページングなど) を使用して発生する可能性があります。原則として、動的に構築されたクエリは、通常のクエリと同じように効率的に実行できます (ただし、動的クエリでコンパイル済みのクエリを使用することはできません)。 ただし、実際にはパフォーマンスの問題の原因になることがよくあります。これは、常に異なる形状を持つ式ツリーを誤って生成することが簡単であるためです。

次の例では、2つの手法を使用してクエリを動的に作成します。 `Where` 指定されたパラメーターが null でない場合にのみ、クエリに演算子を追加します。 この方法は、クエリを動的に作成する場合には適していませんが、わかりやすくするために使用しています。

### <a name="with-constant"></a>[定数を含む](#tab/with-constant)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithConstant&highlight=14-24)]

### <a name="with-parameter"></a>[パラメーター付き](#tab/with-parameter)

[!code-csharp[Main](../../../samples/core/Benchmarks/DynamicallyConstructedQueries.cs?name=WithParameter&highlight=14)]

***

これらの2つの手法のベンチマークを実行すると、次の結果が得られます。

|        Method |       平均 |    エラー |    StdDev |   Gen 0 |  Gen 1 | Gen 2 | Allocated |
|-------------- |-----------:|---------:|----------:|--------:|-------:|------:|----------:|
|  WithConstant | 1096.7 us | 12.54 us |  11.12 us | 13.6719 | 1.9531 |     - |  83.91 KB |
| WithParameter |   570.8 us | 42.43 us | 124.43 us |  5.8594 |      - |     - |  37.16 KB |

1ミリ秒の差が小さいように思われる場合でも、定数のバージョンは継続的にキャッシュを汚染し、他のクエリを再コンパイルすることに注意してください。

> [!NOTE]
> 本当に必要な場合を除き、式ツリー API を使用してクエリを作成することは避けてください。 API の複雑さを除けば、これらを使用すると、非常に簡単にパフォーマンスの問題が発生する可能性があります。
