---
title: EF Core のログ記録
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: f6e35c6d-45b7-4258-be1d-87c1bb67438d
uid: core/miscellaneous/logging
ms.openlocfilehash: 65501b5ac03ae544c51b7fc1a07fa9eea849f1e3
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022146"
---
# <a name="logging"></a><span data-ttu-id="d62ca-102">ログの記録</span><span class="sxs-lookup"><span data-stu-id="d62ca-102">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="d62ca-103">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="d62ca-103">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="d62ca-104">ASP.NET Core アプリケーション</span><span class="sxs-lookup"><span data-stu-id="d62ca-104">ASP.NET Core applications</span></span>

<span data-ttu-id="d62ca-105">ASP.NET Core のログ記録メカニズムと EF Core を自動的に統合されるたびに`AddDbContext`または`AddDbContextPool`使用されます。</span><span class="sxs-lookup"><span data-stu-id="d62ca-105">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="d62ca-106">したがって、ASP.NET Core を使用して、ログ記録する必要があります構成」の説明に従って、 [ASP.NET Core ドキュメント](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)します。</span><span class="sxs-lookup"><span data-stu-id="d62ca-106">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="d62ca-107">他のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="d62ca-107">Other applications</span></span>

<span data-ttu-id="d62ca-108">EF Core の現在ログ記録では、1 つまたは複数の ILoggerProvider で構成されている自体が ILoggerFactory が必要です。</span><span class="sxs-lookup"><span data-stu-id="d62ca-108">EF Core logging currently requires an ILoggerFactory which is itself configured with one or more ILoggerProvider.</span></span> <span data-ttu-id="d62ca-109">一般的なプロバイダーは、次のパッケージで出荷されます。</span><span class="sxs-lookup"><span data-stu-id="d62ca-109">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="d62ca-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): 単純なコンソール ロガー。</span><span class="sxs-lookup"><span data-stu-id="d62ca-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="d62ca-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): サポートする Azure App Service '診断ログ' と 'ログ ストリーム' 機能します。</span><span class="sxs-lookup"><span data-stu-id="d62ca-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="d62ca-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): System.Diagnostics.Debug.WriteLine() を使用してデバッガー モニターのログ。</span><span class="sxs-lookup"><span data-stu-id="d62ca-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="d62ca-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Windows イベント ログに記録します。</span><span class="sxs-lookup"><span data-stu-id="d62ca-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="d62ca-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): EventSource/EventListener をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="d62ca-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="d62ca-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): System.Diagnostics.TraceSource.TraceEvent() を使用してトレース リスナーにログ。</span><span class="sxs-lookup"><span data-stu-id="d62ca-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using System.Diagnostics.TraceSource.TraceEvent().</span></span>

<span data-ttu-id="d62ca-116">適切なパッケージをインストールした後、アプリケーションは、LoggerFactory のシングルトン/グローバル インスタンスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d62ca-116">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="d62ca-117">たとえば、コンソール ロガーを使用します。</span><span class="sxs-lookup"><span data-stu-id="d62ca-117">For example, using the console logger:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

<span data-ttu-id="d62ca-118">EF Core のこのシングルトン/グローバル インスタンスが登録し、必要がある、`DbContextOptionsBuilder`します。</span><span class="sxs-lookup"><span data-stu-id="d62ca-118">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="d62ca-119">例えば:</span><span class="sxs-lookup"><span data-stu-id="d62ca-119">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="d62ca-120">アプリケーションは、コンテキスト インスタンスごとに新しい ILoggerFactory インスタンスを作成しないことが非常に重要です。</span><span class="sxs-lookup"><span data-stu-id="d62ca-120">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="d62ca-121">これは、メモリ リークとパフォーマンスの低下になります。</span><span class="sxs-lookup"><span data-stu-id="d62ca-121">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="d62ca-122">記録内容がフィルター処理</span><span class="sxs-lookup"><span data-stu-id="d62ca-122">Filtering what is logged</span></span>

<span data-ttu-id="d62ca-123">記録内容がフィルター処理する最も簡単な方法では、ILoggerProvider を登録するときに、これを構成します。</span><span class="sxs-lookup"><span data-stu-id="d62ca-123">The easiest way to filter what is logged is to configure it when registering the ILoggerProvider.</span></span> <span data-ttu-id="d62ca-124">例えば:</span><span class="sxs-lookup"><span data-stu-id="d62ca-124">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

<span data-ttu-id="d62ca-125">この例では、メッセージのみを返すため、ログをフィルター処理します。</span><span class="sxs-lookup"><span data-stu-id="d62ca-125">In this example, the log is filtered to return only messages:</span></span>
 * <span data-ttu-id="d62ca-126">'Microsoft.EntityFrameworkCore.Database.Command' カテゴリ</span><span class="sxs-lookup"><span data-stu-id="d62ca-126">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
 * <span data-ttu-id="d62ca-127">'情報' レベルで</span><span class="sxs-lookup"><span data-stu-id="d62ca-127">at the 'Information' level</span></span>

<span data-ttu-id="d62ca-128">EF Core 用ロガー カテゴリが定義されている、`DbLoggerCategory`単純な文字列を解決するには、カテゴリが、これらを見つけやすいようにするクラス。</span><span class="sxs-lookup"><span data-stu-id="d62ca-128">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="d62ca-129">詳細については、基になるログ記録インフラストラクチャが記載されて、 [ASP.NET Core のログ記録に関するドキュメント](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)します。</span><span class="sxs-lookup"><span data-stu-id="d62ca-129">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
