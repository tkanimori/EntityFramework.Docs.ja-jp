---
title: ログ記録-EF Core
description: Entity Framework Core を使用したログ記録の構成
author: rowanmiller
ms.date: 10/27/2016
uid: core/miscellaneous/logging
ms.openlocfilehash: 0fd1c83f01989095a813727390179db2327b610d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071668"
---
# <a name="logging"></a><span data-ttu-id="bf088-103">ログの記録</span><span class="sxs-lookup"><span data-stu-id="bf088-103">Logging</span></span>

> [!TIP]  
> <span data-ttu-id="bf088-104">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="bf088-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="bf088-105">ASP.NET Core アプリケーション</span><span class="sxs-lookup"><span data-stu-id="bf088-105">ASP.NET Core applications</span></span>

<span data-ttu-id="bf088-106">EF Core `AddDbContext` は、またはが使用されるたびに、ASP.NET Core のログメカニズムと自動的に統合 `AddDbContextPool` されます。</span><span class="sxs-lookup"><span data-stu-id="bf088-106">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="bf088-107">そのため、ASP.NET Core を使用する場合は、 [ASP.NET Core のドキュメント](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)で説明されているようにログ記録を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf088-107">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="bf088-108">他のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="bf088-108">Other applications</span></span>

<span data-ttu-id="bf088-109">EF Core のログ記録には、それ自体が1つ以上のログプロバイダーで構成された ILoggerFactory が必要です。</span><span class="sxs-lookup"><span data-stu-id="bf088-109">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="bf088-110">共通プロバイダーは、次のパッケージに付属しています。</span><span class="sxs-lookup"><span data-stu-id="bf088-110">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="bf088-111">[Microsoft. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): 単純なコンソールロガー。</span><span class="sxs-lookup"><span data-stu-id="bf088-111">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="bf088-112">Azure アプリ Services の "診断ログ" 機能と "ログストリーム" 機能をサポートし[ます。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)</span><span class="sxs-lookup"><span data-stu-id="bf088-112">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="bf088-113">[Microsoft.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/).. デバッグ: デバッグ () を使用してデバッガーモニターにログを記録します。</span><span class="sxs-lookup"><span data-stu-id="bf088-113">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="bf088-114">Windows イベントログにログ[を記録し](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)ます。</span><span class="sxs-lookup"><span data-stu-id="bf088-114">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="bf088-115">[Microsoft.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/).. Logging: Eventsource/eventlistener をサポートします。</span><span class="sxs-lookup"><span data-stu-id="bf088-115">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="bf088-116">を使用してトレースリスナーにログを[記録します](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/) `System.Diagnostics.TraceSource.TraceEvent()` 。</span><span class="sxs-lookup"><span data-stu-id="bf088-116">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="bf088-117">適切なパッケージをインストールした後、アプリケーションは Server.loggerfactory のシングルトン/グローバルインスタンスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf088-117">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="bf088-118">たとえば、コンソールロガーを使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="bf088-118">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="bf088-119">バージョン 3.x</span><span class="sxs-lookup"><span data-stu-id="bf088-119">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="bf088-120">バージョン 2.x</span><span class="sxs-lookup"><span data-stu-id="bf088-120">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="bf088-121">次のコードサンプルでは、 `ConsoleLoggerProvider` バージョン2.2 で廃止され、3.0 で置き換えられたコンストラクターを使用しています。</span><span class="sxs-lookup"><span data-stu-id="bf088-121">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="bf088-122">2.2 を使用すると、警告を無視して非表示にするのが安全です。</span><span class="sxs-lookup"><span data-stu-id="bf088-122">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

``` csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="bf088-123">このシングルトン/グローバルインスタンスは、の EF Core に登録する必要があり `DbContextOptionsBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="bf088-123">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="bf088-124">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="bf088-124">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="bf088-125">アプリケーションでは、コンテキストインスタンスごとに新しい ILoggerFactory インスタンスを作成しないことが非常に重要です。</span><span class="sxs-lookup"><span data-stu-id="bf088-125">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="bf088-126">そうすると、メモリリークが発生し、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="bf088-126">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="bf088-127">ログ記録対象のフィルター処理</span><span class="sxs-lookup"><span data-stu-id="bf088-127">Filtering what is logged</span></span>

<span data-ttu-id="bf088-128">アプリケーションでは、ILoggerProvider でフィルターを構成することによって、ログ記録の内容を制御できます。</span><span class="sxs-lookup"><span data-stu-id="bf088-128">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="bf088-129">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="bf088-129">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="bf088-130">バージョン 3.x</span><span class="sxs-lookup"><span data-stu-id="bf088-130">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="bf088-131">バージョン 2.x</span><span class="sxs-lookup"><span data-stu-id="bf088-131">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="bf088-132">次のコードサンプルでは、 `ConsoleLoggerProvider` バージョン2.2 で廃止され、3.0 で置き換えられたコンストラクターを使用しています。</span><span class="sxs-lookup"><span data-stu-id="bf088-132">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="bf088-133">2.2 を使用すると、警告を無視して非表示にするのが安全です。</span><span class="sxs-lookup"><span data-stu-id="bf088-133">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

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

<span data-ttu-id="bf088-134">この例では、ログは、メッセージのみを返すようにフィルター処理されています。</span><span class="sxs-lookup"><span data-stu-id="bf088-134">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="bf088-135">' Microsoft. EntityFrameworkCore. Database. Command ' カテゴリ</span><span class="sxs-lookup"><span data-stu-id="bf088-135">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="bf088-136">' 情報 ' レベル</span><span class="sxs-lookup"><span data-stu-id="bf088-136">at the 'Information' level</span></span>

<span data-ttu-id="bf088-137">EF Core の場合、logger カテゴリはクラスで定義されるため、 `DbLoggerCategory` カテゴリを簡単に見つけることができますが、単純な文字列に解決されます。</span><span class="sxs-lookup"><span data-stu-id="bf088-137">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="bf088-138">基になるログ記録インフラストラクチャの詳細については、 [ASP.NET Core のログ記録に関するドキュメント](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bf088-138">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
