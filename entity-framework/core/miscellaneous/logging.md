---
title: ログ記録-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: e8adc39ec01ff75112b03446a488df6199cc7041
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414087"
---
# <a name="logging"></a>ログ記録

> [!TIP]  
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)は GitHub で確認できます。

## <a name="aspnet-core-applications"></a>ASP.NET Core アプリケーション

EF Core は、`AddDbContext` または `AddDbContextPool` が使用されるたびに、ASP.NET Core のログメカニズムと自動的に統合されます。 そのため、ASP.NET Core を使用する場合は、 [ASP.NET Core のドキュメント](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)で説明されているようにログ記録を構成する必要があります。

## <a name="other-applications"></a>他のアプリケーション

EF Core のログ記録には、それ自体が1つ以上のログプロバイダーで構成された ILoggerFactory が必要です。 共通プロバイダーは、次のパッケージに付属しています。

* [Microsoft. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): 単純なコンソールロガー。
* Azure アプリ Services の "診断ログ" 機能と "ログストリーム" 機能をサポートし[ます。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)
* [Microsoft](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/). デバッグ: デバッグ () を使用してデバッガーモニターにログを記録します。
* Windows イベントログにログ[を記録し](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)ます。
* [Microsoft](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/). Logging: Eventsource/eventlistener をサポートします。
* `System.Diagnostics.TraceSource.TraceEvent()`を使用してトレースリスナーにログを記録[し](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)ます。

適切なパッケージをインストールした後、アプリケーションは Server.loggerfactory のシングルトン/グローバルインスタンスを作成する必要があります。 たとえば、コンソールロガーを使用すると、次のようになります。

### <a name="version-3x"></a>[バージョン 3.x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[バージョン 2.x](#tab/v2)

> [!NOTE]
> 次のコードサンプルでは、バージョン2.2 で廃止され、3.0 で置き換えられた `ConsoleLoggerProvider` コンストラクターを使用します。 2\.2 を使用すると、警告を無視して非表示にするのが安全です。

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

このシングルトン/グローバルインスタンスは、`DbContextOptionsBuilder`上の EF Core に登録する必要があります。 次に例を示します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> アプリケーションでは、コンテキストインスタンスごとに新しい ILoggerFactory インスタンスを作成しないことが非常に重要です。 そうすると、メモリリークが発生し、パフォーマンスが低下します。

## <a name="filtering-what-is-logged"></a>ログ記録対象のフィルター処理

アプリケーションでは、ILoggerProvider でフィルターを構成することによって、ログ記録の内容を制御できます。 次に例を示します。

### <a name="version-3x"></a>[バージョン 3.x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[バージョン 2.x](#tab/v2)

> [!NOTE]
> 次のコードサンプルでは、バージョン2.2 で廃止され、3.0 で置き換えられた `ConsoleLoggerProvider` コンストラクターを使用します。 2\.2 を使用すると、警告を無視して非表示にするのが安全です。

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[]
    {
        new ConsoleLoggerProvider((category, level)
            => category == DbLoggerCategory.Database.Command.Name
               && level == LogLevel.Information, true)
    });
```

***

この例では、ログは、メッセージのみを返すようにフィルター処理されています。

* ' Microsoft. EntityFrameworkCore. Database. Command ' カテゴリ
* ' 情報 ' レベル

EF Core の場合、logger カテゴリは `DbLoggerCategory` クラスで定義されるため、カテゴリを簡単に見つけることができますが、単純な文字列に解決されます。

基になるログ記録インフラストラクチャの詳細については、 [ASP.NET Core のログ記録に関するドキュメント](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)を参照してください。
