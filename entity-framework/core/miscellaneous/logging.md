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
# <a name="logging"></a><span data-ttu-id="744bd-102">ログ</span><span class="sxs-lookup"><span data-stu-id="744bd-102">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="744bd-103">この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)GitHub でします。</span><span class="sxs-lookup"><span data-stu-id="744bd-103">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="744bd-104">ASP.NET Core アプリケーション</span><span class="sxs-lookup"><span data-stu-id="744bd-104">ASP.NET Core applications</span></span>

<span data-ttu-id="744bd-105">ASP.NET Core のログ記録機構と自動的に統合し、EF コアされるたびに`AddDbContext`または`AddDbContextPool`を使用します。</span><span class="sxs-lookup"><span data-stu-id="744bd-105">EF Core integrates automatically with the logging mechanims of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="744bd-106">したがって、ASP.NET Core を使用する場合のログ記録する必要があります構成する」の説明に従って、 [ASP.NET Core ドキュメント](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)です。</span><span class="sxs-lookup"><span data-stu-id="744bd-106">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="744bd-107">他のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="744bd-107">Other applications</span></span>

<span data-ttu-id="744bd-108">EF コアの現在のログ記録には、1 つまたは複数の ILoggerProvider で構成されているは ILoggerFactory が必要です。</span><span class="sxs-lookup"><span data-stu-id="744bd-108">EF Core logging currently requires an ILoggerFactory which is itself configured with one or more ILoggerProvider.</span></span> <span data-ttu-id="744bd-109">次のパッケージには、一般的なプロバイダーが出荷日します。</span><span class="sxs-lookup"><span data-stu-id="744bd-109">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="744bd-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): 単純なコンソール ロガー。</span><span class="sxs-lookup"><span data-stu-id="744bd-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="744bd-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): サポートする Azure App Services '診断ログ' と 'ログ ストリーム' 機能します。</span><span class="sxs-lookup"><span data-stu-id="744bd-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="744bd-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): System.Diagnostics.Debug.WriteLine() を使用するデバッガー モニターのログです。</span><span class="sxs-lookup"><span data-stu-id="744bd-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="744bd-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): によって Windows イベント ログに記録します。</span><span class="sxs-lookup"><span data-stu-id="744bd-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="744bd-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): EventSource/EventListener をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="744bd-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="744bd-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): System.Diagnostics.TraceSource.TraceEvent() を使用してトレース リスナーにログ。</span><span class="sxs-lookup"><span data-stu-id="744bd-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using System.Diagnostics.TraceSource.TraceEvent().</span></span>

<span data-ttu-id="744bd-116">適切なパッケージをインストールすると、アプリケーションは、LoggerFactory のシングルトン/グローバル インスタンスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="744bd-116">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="744bd-117">たとえば、コンソール ロガーを使用します。</span><span class="sxs-lookup"><span data-stu-id="744bd-117">For example, using the console logger:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

<span data-ttu-id="744bd-118">このシングルトン/グローバル インスタンスしに登録する EF コア上、`DbContextOptionsBuilder`です。</span><span class="sxs-lookup"><span data-stu-id="744bd-118">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="744bd-119">例:</span><span class="sxs-lookup"><span data-stu-id="744bd-119">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="744bd-120">これは、アプリケーションは、コンテキストのインスタンスごとに新しい ILoggerFactory インスタンスを作成しないことが非常に重要です。</span><span class="sxs-lookup"><span data-stu-id="744bd-120">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="744bd-121">これが発生、メモリ リークとパフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="744bd-121">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="744bd-122">記録する内容をフィルター処理</span><span class="sxs-lookup"><span data-stu-id="744bd-122">Filtering what is logged</span></span>

<span data-ttu-id="744bd-123">記録する内容をフィルター処理する最も簡単な方法では、ILoggerProvider を登録するときにそれを構成します。</span><span class="sxs-lookup"><span data-stu-id="744bd-123">The easiest way to filter what is logged is to configure it when registering the ILoggerProvider.</span></span> <span data-ttu-id="744bd-124">例:</span><span class="sxs-lookup"><span data-stu-id="744bd-124">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

<span data-ttu-id="744bd-125">この例では、ログは、メッセージのみを返すフィルターされます。</span><span class="sxs-lookup"><span data-stu-id="744bd-125">In this example, the log is filtered to return only messages:</span></span>
 * <span data-ttu-id="744bd-126">'Microsoft.EntityFrameworkCore.Database.Command' カテゴリ</span><span class="sxs-lookup"><span data-stu-id="744bd-126">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
 * <span data-ttu-id="744bd-127">'情報' レベル</span><span class="sxs-lookup"><span data-stu-id="744bd-127">at the 'Information' level</span></span>

<span data-ttu-id="744bd-128">EF core ロガー カテゴリが定義されている、`DbLoggerCategory`単純な文字列を解決するには、カテゴリが、これらを見つけやすいようにするクラス。</span><span class="sxs-lookup"><span data-stu-id="744bd-128">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="744bd-129">基になるのログ記録インフラストラクチャの詳細については含まれて、 [ASP.NET Core ログ ドキュメント](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)です。</span><span class="sxs-lookup"><span data-stu-id="744bd-129">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>