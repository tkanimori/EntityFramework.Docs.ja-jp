---
title: ログ記録-EF Core
description: Entity Framework Core を使用したログ記録の構成
author: ajcvickers
ms.date: 10/06/2020
uid: core/miscellaneous/logging
ms.openlocfilehash: 389834b3822aeeaefb8c085538bc6359ccfa7094
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063011"
---
# <a name="logging"></a>ログの記録

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)は GitHub で確認できます。

## <a name="simple-logging"></a>シンプルなログ

> [!NOTE]
> この機能は EF Core 5.0 で追加されました。

Entity Framework Core (EF Core) では、クエリの実行やデータベースへの変更の保存などの操作について、ログメッセージが生成されます。 これらは、 [Logto を使用することに](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)より、あらゆる種類のアプリケーションからアクセスできます。 <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> [DbContext インスタンスを構成](xref:core/miscellaneous/configuring-dbcontext)する場合。 この構成は、通常、のオーバーライドで実行され <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> ます。 次に例を示します。

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

この概念は、EF6 のに似てい <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> ます。

詳細については、「 [簡易ログ](xref:core/miscellaneous/events/simple-logging) 」を参照してください。

## <a name="aspnet-core-applications"></a>ASP.NET Core アプリケーション

EF Core `AddDbContext` は、またはが使用されるたびに、ASP.NET Core のログメカニズムと自動的に統合 `AddDbContextPool` されます。 そのため、ASP.NET Core を使用する場合は、 [ASP.NET Core のドキュメント](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)で説明されているようにログ記録を構成する必要があります。

## <a name="other-applications"></a>他のアプリケーション

EF Core のログ記録には、それ自体が1つ以上のログプロバイダーで構成された ILoggerFactory が必要です。 共通プロバイダーは、次のパッケージに付属しています。

* [Microsoft. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): 単純なコンソールロガー。
* Azure アプリ Services の "診断ログ" 機能と "ログストリーム" 機能をサポートし[ます。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)
* [Microsoft.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/).. デバッグ: デバッグ () を使用してデバッガーモニターにログを記録します。
* Windows イベントログにログ[を記録し](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)ます。
* [Microsoft.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/).. Logging: Eventsource/eventlistener をサポートします。
* を使用してトレースリスナーにログを[記録します](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/) `System.Diagnostics.TraceSource.TraceEvent()` 。

適切なパッケージをインストールした後、アプリケーションは Server.loggerfactory のシングルトン/グローバルインスタンスを作成する必要があります。 たとえば、コンソールロガーを使用すると、次のようになります。

### <a name="version-3x"></a>[バージョン 3.x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[バージョン 2.x](#tab/v2)

> [!NOTE]
> 次のコードサンプルでは、 `ConsoleLoggerProvider` バージョン2.2 で廃止され、3.0 で置き換えられたコンストラクターを使用しています。 2.2 を使用すると、警告を無視して非表示にするのが安全です。

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

このシングルトン/グローバルインスタンスは、の EF Core に登録する必要があり `DbContextOptionsBuilder` ます。 次に例を示します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> アプリケーションでは、コンテキストインスタンスごとに新しい ILoggerFactory インスタンスを作成しないことが非常に重要です。 そうすると、メモリリークが発生し、パフォーマンスが低下します。

## <a name="filtering-what-is-logged"></a>ログ記録対象のフィルター処理

アプリケーションでは、ILoggerProvider でフィルターを構成することによって、ログ記録の内容を制御できます。 次に例を示します。

### <a name="version-3x"></a>[バージョン 3.x](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[バージョン 2.x](#tab/v2)

> [!NOTE]
> 次のコードサンプルでは、 `ConsoleLoggerProvider` バージョン2.2 で廃止され、3.0 で置き換えられたコンストラクターを使用しています。 2.2 を使用すると、警告を無視して非表示にするのが安全です。

```csharp
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

EF Core の場合、logger カテゴリはクラスで定義されるため、 `DbLoggerCategory` カテゴリを簡単に見つけることができますが、単純な文字列に解決されます。

基になるログ記録インフラストラクチャの詳細については、 [ASP.NET Core のログ記録に関するドキュメント](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)を参照してください。
