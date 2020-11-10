---
title: EF Core を使用する
description: ASP.NET Core とその他のアプリケーションの種類での EF Core を使用したログ記録
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/extensions-logging
ms.openlocfilehash: 3732bda0ef29b05672a2dfb83405da802f899191
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431294"
---
# <a name="using-microsoftextensionslogging-in-ef-core"></a>EF Core での Microsoft 拡張子の使用

[Microsoft](/dotnet/core/extensions/logging) では、多くの一般的なログ記録システムに対応したプラグインプロバイダーを使用した拡張可能なログメカニズムです。 Microsoft から提供されているプラグイン (例: [microsoft の拡張](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)機能) とサードパーティのプラグイン (たとえば、 [Serilog. 拡張子](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) は、NuGet パッケージとして入手できます。

Entity Framework Core (EF Core) はと完全に統合さ `Microsoft.Extensions.Logging` れています。 ただし、特に依存関係の挿入を使用しないアプリケーションの場合は、 [単純](xref:core/logging-events-diagnostics/simple-logging) なログ記録を使用することをお勧めします。

## <a name="aspnet-core-applications"></a>ASP.NET Core アプリケーション

`Microsoft.Extensions.Logging` は [ASP.NET Core アプリケーションで既定で使用さ](/aspnet/core/fundamentals/logging)れます。 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>または <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> を呼び出します。

## <a name="other-application-types"></a>その他のアプリケーションの種類

その他のアプリケーションの種類では、 [Generichost](/dotnet/core/extensions/generic-host) を使用して、ASP.NET Core で使用されているものと同じ依存関係挿入パターンを取得できます。 AddDbContext または AddDbContextPool は、ASP.NET Core アプリケーションと同様に使用できます。

`Microsoft.Extensions.Logging` は、依存関係の挿入を使用しないアプリケーションにも使用できます。ただし、 [単純なログ記録](xref:core/logging-events-diagnostics/simple-logging) は簡単に設定できます。

`Microsoft.Extensions.Logging` の作成が必要です <xref:Microsoft.Extensions.Logging.LoggerFactory> 。 このファクトリは、任意の場所に静的/グローバルインスタンスとして格納し、DbContext が作成されるたびに使用する必要があります。 たとえば、ロガーファクトリは、DbContext の静的プロパティとして格納するのが一般的です。

### <a name="ef-core-30-and-above"></a>[3.0 以降の EF Core](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[EF Core 2.1](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> EF Core 2.1 では、アプリケーションで DbContext インスタンスごとに新しい Server.loggerfactory インスタンスが作成されないことが非常に重要です。 そうすると、メモリリークが発生し、パフォーマンスが低下します。 これは EF Core 3.0 以降で修正されました。

***

このシングルトン/グローバルインスタンスは、の EF Core に登録する必要があり <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> ます。 次に例を示します。

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a>詳細なメッセージを取得する

> [!TIP]
> AddDbContext が使用されている場合、または DbContext コンストラクターに DbContextOptions インスタンスが渡された場合、OnConfiguring は引き続き呼び出されます。 これにより、DbContext の構築方法に関係なく、コンテキスト構成を適用するための理想的な場所となります。

### <a name="sensitive-data"></a>機微なデータ

既定では、EF Core には、例外メッセージ内のデータの値は含まれません。 これは、このようなデータは機密情報である可能性があるため、例外が処理されない場合に実稼働環境でも明らかになる可能性があるためです。

ただし、特にキーのデータ値を把握することは、デバッグ時に非常に役立ちます。 これは、を呼び出すことによって EF Core で有効にすることができ <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> ます。 次に例を示します。

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a>詳細なクエリの例外

パフォーマンス上の理由により、EF Core は、try-catch ブロック内のデータベースプロバイダーから値を読み取る各呼び出しをラップしません。 ただし、これにより、特に、モデルで許可されていない場合にデータベースが NULL を返す場合に、診断が困難な例外が発生することがあります。

オンに <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> すると、EF によってこれらの try-catch ブロックが導入されるため、より詳細なエラーが発生します。 次に例を示します。

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a>特定のメッセージの構成

EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API を使用すると、特定のイベントが発生したときの動作をアプリケーションで変更できます。 これは次の場合に使用できます。

* イベントがログに記録されるログレベルを変更する
* イベントのログ記録をスキップする
* イベントが発生したときに例外をスローする

### <a name="changing-the-log-level-for-an-event"></a>イベントのログレベルの変更

イベントの定義済みログレベルを変更すると便利な場合があります。 たとえば、次の2つのイベントをからに昇格させるために使用でき `LogLevel.Debug` `LogLevel.Information` ます。

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a>イベントのログ記録を抑制する

同様に、個々のイベントをログに記録しないようにすることもできます。 これは、確認して理解した警告を無視する場合に特に便利です。 次に例を示します。

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a>イベントに対してスローする

最後に、特定のイベントに対してをスローするように EF Core を構成できます。 これは、警告をエラーに変更する場合に特に便利です。 (実際には、これはメソッドの本来の目的であるため、という `ConfigureWarnings` 名前です)。例えば：

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a>フィルター処理とその他の構成

ログフィルターとその他の構成については、「 [.net でのログ記録](/dotnet/core/extensions/logging) 」を参照してください。

EF Core ログイベントは、次のいずれかの方法で定義されます。

* <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> すべての EF Core データベースプロバイダーに共通のイベントの場合
* <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> すべてのリレーショナルデータベースプロバイダーに共通のイベントの場合
* 現在のデータベースプロバイダーに固有のイベントの類似クラス。 たとえば、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> SQL Server プロバイダーの場合はです。

これらの定義には、によって使用される各イベントのイベント Id、ログレベル、およびカテゴリが含まれ `Microsoft.Extensions.Logging` ます。
