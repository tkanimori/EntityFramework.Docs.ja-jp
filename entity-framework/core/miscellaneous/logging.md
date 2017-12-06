---
title: "ログ - EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
ms.technology: entity-framework-core
uid: core/miscellaneous/logging
ms.openlocfilehash: 807560e563eddfb72d4286353b1403a0d2e2a441
ms.sourcegitcommit: 5367516f063cb42804ec92c31cdf76322554f2b5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2017
---
# <a name="logging"></a>ログ

> [!TIP]  
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)GitHub でします。

## <a name="aspnet-core-applications"></a>ASP.NET Core アプリケーション

ASP.NET Core のログ記録機構と自動的に統合し、EF コアされるたびに`AddDbContext`または`AddDbContextPool`を使用します。 したがって、ASP.NET Core を使用する場合のログ記録する必要があります構成する」の説明に従って、 [ASP.NET Core ドキュメント](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)です。

## <a name="other-applications"></a>他のアプリケーション

EF コアの現在のログ記録には、1 つまたは複数の ILoggerProvider で構成されているは ILoggerFactory が必要です。 次のパッケージには、一般的なプロバイダーが出荷日します。

* [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): 単純なコンソール ロガー。
* [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): サポートする Azure App Services '診断ログ' と 'ログ ストリーム' 機能します。
* [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): System.Diagnostics.Debug.WriteLine() を使用するデバッガー モニターのログです。
* [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): によって Windows イベント ログに記録します。
* [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): EventSource/EventListener をサポートしています。
* [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): System.Diagnostics.TraceSource.TraceEvent() を使用してトレース リスナーにログ。

適切なパッケージをインストールすると、アプリケーションは、LoggerFactory のシングルトン/グローバル インスタンスを作成する必要があります。 たとえば、コンソール ロガーを使用します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

このシングルトン/グローバル インスタンスしに登録する EF コア上、`DbContextOptionsBuilder`です。 例:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> これは、アプリケーションは、コンテキストのインスタンスごとに新しい ILoggerFactory インスタンスを作成しないことが非常に重要です。 これが発生、メモリ リークとパフォーマンスが低下します。

## <a name="filtering-what-is-logged"></a>記録する内容をフィルター処理

記録する内容をフィルター処理する最も簡単な方法では、ILoggerProvider を登録するときにそれを構成します。 例:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

この例では、ログは、メッセージのみを返すフィルターされます。
 * 'Microsoft.EntityFrameworkCore.Database.Command' カテゴリ
 * '情報' レベル

EF core ロガー カテゴリが定義されている、`DbLoggerCategory`単純な文字列を解決するには、カテゴリが、これらを見つけやすいようにするクラス。

基になるのログ記録インフラストラクチャの詳細については含まれて、 [ASP.NET Core ログ ドキュメント](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)です。