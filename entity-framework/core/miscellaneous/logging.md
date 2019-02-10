---
title: EF Core のログ記録
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 0a996403afdbe076b1690c98eeb305b40c4d1f4a
ms.sourcegitcommit: 109a16478de498b65717a6e09be243647e217fb3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2019
ms.locfileid: "55985575"
---
# <a name="logging"></a>ログの記録

> [!TIP]  
> この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)は GitHub で確認できます。

## <a name="aspnet-core-applications"></a>ASP.NET Core アプリケーション

ASP.NET Core のログ記録メカニズムと EF Core を自動的に統合されるたびに`AddDbContext`または`AddDbContextPool`使用されます。 したがって、ASP.NET Core を使用して、ログ記録する必要があります構成」の説明に従って、 [ASP.NET Core ドキュメント](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)します。

## <a name="other-applications"></a>他のアプリケーション

EF Core の現在ログ記録では、1 つまたは複数の ILoggerProvider で構成されている自体が ILoggerFactory が必要です。 一般的なプロバイダーは、次のパッケージで出荷されます。

* [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/):単純なコンソール ロガー。
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/):Azure App Service '診断ログ' と 'ログ ストリーム' 機能をサポートしています。
* [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/):デバッガーにログを System.Diagnostics.Debug.WriteLine() を使用して監視します。
* [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/):Windows イベント ログに記録します。
* [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/):EventSource/EventListener をサポートしています。
* [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/):System.Diagnostics.TraceSource.TraceEvent() を使用してトレース リスナーにログ。

> [!NOTE]
> 次のコード例では、 `ConsoleLoggerProvider` version 2.2 で廃止されましたがコンス トラクター。 古い形式のログ記録 Api 用の適切な置換は、バージョン 3.0 で使用可能になります。 それまでは、無視し、警告を抑制しても安全です。

適切なパッケージをインストールした後、アプリケーションは、LoggerFactory のシングルトン/グローバル インスタンスを作成する必要があります。 たとえば、コンソール ロガーを使用します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

EF Core のこのシングルトン/グローバル インスタンスが登録し、必要がある、`DbContextOptionsBuilder`します。 例:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> アプリケーションは、コンテキスト インスタンスごとに新しい ILoggerFactory インスタンスを作成しないことが非常に重要です。 これは、メモリ リークとパフォーマンスの低下になります。

## <a name="filtering-what-is-logged"></a>記録内容がフィルター処理

> [!NOTE]
> 次のコード例では、 `ConsoleLoggerProvider` version 2.2 で廃止されましたがコンス トラクター。 古い形式のログ記録 Api 用の適切な置換は、バージョン 3.0 で使用可能になります。 それまでは、無視し、警告を抑制しても安全です。

記録内容がフィルター処理する最も簡単な方法では、ILoggerProvider を登録するときに、これを構成します。 例:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

この例では、メッセージのみを返すため、ログをフィルター処理します。
 * 'Microsoft.EntityFrameworkCore.Database.Command' カテゴリ
 * '情報' レベルで

EF Core 用ロガー カテゴリが定義されている、`DbLoggerCategory`単純な文字列を解決するには、カテゴリが、これらを見つけやすいようにするクラス。

詳細については、基になるログ記録インフラストラクチャが記載されて、 [ASP.NET Core のログ記録に関するドキュメント](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)します。
