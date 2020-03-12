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
# <a name="logging"></a><span data-ttu-id="7421b-102">ログ記録</span><span class="sxs-lookup"><span data-stu-id="7421b-102">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="7421b-103">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="7421b-103">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="7421b-104">ASP.NET Core アプリケーション</span><span class="sxs-lookup"><span data-stu-id="7421b-104">ASP.NET Core applications</span></span>

<span data-ttu-id="7421b-105">EF Core は、`AddDbContext` または `AddDbContextPool` が使用されるたびに、ASP.NET Core のログメカニズムと自動的に統合されます。</span><span class="sxs-lookup"><span data-stu-id="7421b-105">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="7421b-106">そのため、ASP.NET Core を使用する場合は、 [ASP.NET Core のドキュメント](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)で説明されているようにログ記録を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7421b-106">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="7421b-107">他のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="7421b-107">Other applications</span></span>

<span data-ttu-id="7421b-108">EF Core のログ記録には、それ自体が1つ以上のログプロバイダーで構成された ILoggerFactory が必要です。</span><span class="sxs-lookup"><span data-stu-id="7421b-108">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="7421b-109">共通プロバイダーは、次のパッケージに付属しています。</span><span class="sxs-lookup"><span data-stu-id="7421b-109">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="7421b-110">[Microsoft. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): 単純なコンソールロガー。</span><span class="sxs-lookup"><span data-stu-id="7421b-110">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="7421b-111">Azure アプリ Services の "診断ログ" 機能と "ログストリーム" 機能をサポートし[ます。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)</span><span class="sxs-lookup"><span data-stu-id="7421b-111">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="7421b-112">[Microsoft](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/). デバッグ: デバッグ () を使用してデバッガーモニターにログを記録します。</span><span class="sxs-lookup"><span data-stu-id="7421b-112">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="7421b-113">Windows イベントログにログ[を記録し](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)ます。</span><span class="sxs-lookup"><span data-stu-id="7421b-113">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="7421b-114">[Microsoft](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/). Logging: Eventsource/eventlistener をサポートします。</span><span class="sxs-lookup"><span data-stu-id="7421b-114">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="7421b-115">`System.Diagnostics.TraceSource.TraceEvent()`を使用してトレースリスナーにログを記録[し](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/)ます。</span><span class="sxs-lookup"><span data-stu-id="7421b-115">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="7421b-116">適切なパッケージをインストールした後、アプリケーションは Server.loggerfactory のシングルトン/グローバルインスタンスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7421b-116">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="7421b-117">たとえば、コンソールロガーを使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="7421b-117">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="7421b-118">バージョン 3.x</span><span class="sxs-lookup"><span data-stu-id="7421b-118">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="7421b-119">バージョン 2.x</span><span class="sxs-lookup"><span data-stu-id="7421b-119">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="7421b-120">次のコードサンプルでは、バージョン2.2 で廃止され、3.0 で置き換えられた `ConsoleLoggerProvider` コンストラクターを使用します。</span><span class="sxs-lookup"><span data-stu-id="7421b-120">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="7421b-121">2\.2 を使用すると、警告を無視して非表示にするのが安全です。</span><span class="sxs-lookup"><span data-stu-id="7421b-121">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="7421b-122">このシングルトン/グローバルインスタンスは、`DbContextOptionsBuilder`上の EF Core に登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7421b-122">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="7421b-123">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7421b-123">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="7421b-124">アプリケーションでは、コンテキストインスタンスごとに新しい ILoggerFactory インスタンスを作成しないことが非常に重要です。</span><span class="sxs-lookup"><span data-stu-id="7421b-124">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="7421b-125">そうすると、メモリリークが発生し、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="7421b-125">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="7421b-126">ログ記録対象のフィルター処理</span><span class="sxs-lookup"><span data-stu-id="7421b-126">Filtering what is logged</span></span>

<span data-ttu-id="7421b-127">アプリケーションでは、ILoggerProvider でフィルターを構成することによって、ログ記録の内容を制御できます。</span><span class="sxs-lookup"><span data-stu-id="7421b-127">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="7421b-128">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7421b-128">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="7421b-129">バージョン 3.x</span><span class="sxs-lookup"><span data-stu-id="7421b-129">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="7421b-130">バージョン 2.x</span><span class="sxs-lookup"><span data-stu-id="7421b-130">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="7421b-131">次のコードサンプルでは、バージョン2.2 で廃止され、3.0 で置き換えられた `ConsoleLoggerProvider` コンストラクターを使用します。</span><span class="sxs-lookup"><span data-stu-id="7421b-131">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="7421b-132">2\.2 を使用すると、警告を無視して非表示にするのが安全です。</span><span class="sxs-lookup"><span data-stu-id="7421b-132">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

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

<span data-ttu-id="7421b-133">この例では、ログは、メッセージのみを返すようにフィルター処理されています。</span><span class="sxs-lookup"><span data-stu-id="7421b-133">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="7421b-134">' Microsoft. EntityFrameworkCore. Database. Command ' カテゴリ</span><span class="sxs-lookup"><span data-stu-id="7421b-134">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="7421b-135">' 情報 ' レベル</span><span class="sxs-lookup"><span data-stu-id="7421b-135">at the 'Information' level</span></span>

<span data-ttu-id="7421b-136">EF Core の場合、logger カテゴリは `DbLoggerCategory` クラスで定義されるため、カテゴリを簡単に見つけることができますが、単純な文字列に解決されます。</span><span class="sxs-lookup"><span data-stu-id="7421b-136">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="7421b-137">基になるログ記録インフラストラクチャの詳細については、 [ASP.NET Core のログ記録に関するドキュメント](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7421b-137">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
