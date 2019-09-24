---
title: ログ記録-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 6a8499f9f0220087e76f2e0b3a75ce551c4ddb80
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197506"
---
# <a name="logging"></a>ログの記録

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)は GitHub で確認できます。

## <a name="aspnet-core-applications"></a>ASP.NET Core アプリケーション

EF Core は、また`AddDbContext` `AddDbContextPool`はが使用されるたびに、ASP.NET Core のログメカニズムと自動的に統合されます。 そのため、ASP.NET Core を使用する場合は、 [ASP.NET Core のドキュメント](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)で説明されているようにログ記録を構成する必要があります。

## <a name="other-applications"></a>その他のアプリケーション

EF Core のログ記録には、それ自体が1つ以上のログプロバイダーで構成された ILoggerFactory が必要です。 共通プロバイダーは、次のパッケージに付属しています。

* [Microsoft.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).Logging. コンソール:単純なコンソールロガー。
* [Microsoft.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)..... service:では、Azure アプリ Services の "診断ログ" 機能と "ログストリーム" 機能がサポートされています。
* [Microsoft. 拡張子。デバッグ](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/):システムの診断 () を使用してデバッガーモニターにログを記録します。
* [Microsoft. 拡張子. Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/):Windows イベントログにログを記録します。
* [Microsoft.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/).Logging:EventSource/EventListener をサポートします。
* ... [Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/):を使用して`System.Diagnostics.TraceSource.TraceEvent()`トレースリスナーにログを記録します。

適切なパッケージをインストールした後、アプリケーションは Server.loggerfactory のシングルトン/グローバルインスタンスを作成する必要があります。 たとえば、コンソールロガーを使用すると、次のようになります。

# <a name="version-30tabv3"></a>[バージョン3.0](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

# <a name="version-2xtabv2"></a>[バージョン2.x](#tab/v2)

> [!NOTE]
> 次のコードサンプルでは`ConsoleLoggerProvider` 、バージョン2.2 で廃止され、3.0 で置き換えられたコンストラクターを使用しています。 2\.2 を使用すると、警告を無視して非表示にするのが安全です。

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

このシングルトン/グローバルインスタンスは、の`DbContextOptionsBuilder`EF Core に登録する必要があります。 次に例を示します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> アプリケーションでは、コンテキストインスタンスごとに新しい ILoggerFactory インスタンスを作成しないことが非常に重要です。 そうすると、メモリリークが発生し、パフォーマンスが低下します。

## <a name="filtering-what-is-logged"></a>ログ記録対象のフィルター処理

アプリケーションでは、ILoggerProvider でフィルターを構成することによって、ログ記録の内容を制御できます。 次に例を示します。

# <a name="version-30tabv3"></a>[バージョン3.0](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

# <a name="version-2xtabv2"></a>[バージョン2.x](#tab/v2)

> [!NOTE]
> 次のコードサンプルでは`ConsoleLoggerProvider` 、バージョン2.2 で廃止され、3.0 で置き換えられたコンストラクターを使用しています。 2\.2 を使用すると、警告を無視して非表示にするのが安全です。

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

EF Core の場合、logger カテゴリは`DbLoggerCategory`クラスで定義されるため、カテゴリを簡単に見つけることができますが、単純な文字列に解決されます。

基になるログ記録インフラストラクチャの詳細については、 [ASP.NET Core のログ記録に関するドキュメント](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)を参照してください。
