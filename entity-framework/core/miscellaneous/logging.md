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
# <a name="logging"></a><span data-ttu-id="fee1c-103">ログの記録</span><span class="sxs-lookup"><span data-stu-id="fee1c-103">Logging</span></span>

> [!TIP]
> <span data-ttu-id="fee1c-104">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="fee1c-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging) on GitHub.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="fee1c-105">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="fee1c-105">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="fee1c-106">この機能は EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="fee1c-106">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="fee1c-107">Entity Framework Core (EF Core) では、クエリの実行やデータベースへの変更の保存などの操作について、ログメッセージが生成されます。</span><span class="sxs-lookup"><span data-stu-id="fee1c-107">Entity Framework Core (EF Core) generates log messages for operations such as executing a query or saving changes to the database.</span></span> <span data-ttu-id="fee1c-108">これらは、 [Logto を使用することに](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)より、あらゆる種類のアプリケーションからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="fee1c-108">These can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="fee1c-109">[DbContext インスタンスを構成](xref:core/miscellaneous/configuring-dbcontext)する場合。</span><span class="sxs-lookup"><span data-stu-id="fee1c-109">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="fee1c-110">この構成は、通常、のオーバーライドで実行され <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="fee1c-110">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="fee1c-111">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="fee1c-111">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="fee1c-112">この概念は、EF6 のに似てい <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="fee1c-112">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="fee1c-113">詳細については、「 [簡易ログ](xref:core/miscellaneous/events/simple-logging) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fee1c-113">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="fee1c-114">ASP.NET Core アプリケーション</span><span class="sxs-lookup"><span data-stu-id="fee1c-114">ASP.NET Core applications</span></span>

<span data-ttu-id="fee1c-115">EF Core `AddDbContext` は、またはが使用されるたびに、ASP.NET Core のログメカニズムと自動的に統合 `AddDbContextPool` されます。</span><span class="sxs-lookup"><span data-stu-id="fee1c-115">EF Core integrates automatically with the logging mechanisms of ASP.NET Core whenever `AddDbContext` or `AddDbContextPool` is used.</span></span> <span data-ttu-id="fee1c-116">そのため、ASP.NET Core を使用する場合は、 [ASP.NET Core のドキュメント](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)で説明されているようにログ記録を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fee1c-116">Therefore, when using ASP.NET Core, logging should be configured as described in the [ASP.NET Core documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>

## <a name="other-applications"></a><span data-ttu-id="fee1c-117">他のアプリケーション</span><span class="sxs-lookup"><span data-stu-id="fee1c-117">Other applications</span></span>

<span data-ttu-id="fee1c-118">EF Core のログ記録には、それ自体が1つ以上のログプロバイダーで構成された ILoggerFactory が必要です。</span><span class="sxs-lookup"><span data-stu-id="fee1c-118">EF Core logging requires an ILoggerFactory which is itself configured with one or more logging providers.</span></span> <span data-ttu-id="fee1c-119">共通プロバイダーは、次のパッケージに付属しています。</span><span class="sxs-lookup"><span data-stu-id="fee1c-119">Common providers are shipped in the following packages:</span></span>

* <span data-ttu-id="fee1c-120">[Microsoft. Logging. console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): 単純なコンソールロガー。</span><span class="sxs-lookup"><span data-stu-id="fee1c-120">[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/): A simple console logger.</span></span>
* <span data-ttu-id="fee1c-121">Azure アプリ Services の "診断ログ" 機能と "ログストリーム" 機能をサポートし[ます。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/)</span><span class="sxs-lookup"><span data-stu-id="fee1c-121">[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices/): Supports Azure App Services 'Diagnostics logs' and 'Log stream' features.</span></span>
* <span data-ttu-id="fee1c-122">[Microsoft.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/).. デバッグ: デバッグ () を使用してデバッガーモニターにログを記録します。</span><span class="sxs-lookup"><span data-stu-id="fee1c-122">[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/): Logs to a debugger monitor using System.Diagnostics.Debug.WriteLine().</span></span>
* <span data-ttu-id="fee1c-123">Windows イベントログにログ[を記録し](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/)ます。</span><span class="sxs-lookup"><span data-stu-id="fee1c-123">[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog/): Logs to Windows Event Log.</span></span>
* <span data-ttu-id="fee1c-124">[Microsoft.](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/).. Logging: Eventsource/eventlistener をサポートします。</span><span class="sxs-lookup"><span data-stu-id="fee1c-124">[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource/): Supports EventSource/EventListener.</span></span>
* <span data-ttu-id="fee1c-125">を使用してトレースリスナーにログを[記録します](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/) `System.Diagnostics.TraceSource.TraceEvent()` 。</span><span class="sxs-lookup"><span data-stu-id="fee1c-125">[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource/): Logs to a trace listener using `System.Diagnostics.TraceSource.TraceEvent()`.</span></span>

<span data-ttu-id="fee1c-126">適切なパッケージをインストールした後、アプリケーションは Server.loggerfactory のシングルトン/グローバルインスタンスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fee1c-126">After installing the appropriate package(s), the application should create a singleton/global instance of a LoggerFactory.</span></span> <span data-ttu-id="fee1c-127">たとえば、コンソールロガーを使用すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="fee1c-127">For example, using the console logger:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="fee1c-128">バージョン 3.x</span><span class="sxs-lookup"><span data-stu-id="fee1c-128">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="fee1c-129">バージョン 2.x</span><span class="sxs-lookup"><span data-stu-id="fee1c-129">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="fee1c-130">次のコードサンプルでは、 `ConsoleLoggerProvider` バージョン2.2 で廃止され、3.0 で置き換えられたコンストラクターを使用しています。</span><span class="sxs-lookup"><span data-stu-id="fee1c-130">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="fee1c-131">2.2 を使用すると、警告を無視して非表示にするのが安全です。</span><span class="sxs-lookup"><span data-stu-id="fee1c-131">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

***

<span data-ttu-id="fee1c-132">このシングルトン/グローバルインスタンスは、の EF Core に登録する必要があり `DbContextOptionsBuilder` ます。</span><span class="sxs-lookup"><span data-stu-id="fee1c-132">This singleton/global instance should then be registered with EF Core on the `DbContextOptionsBuilder`.</span></span> <span data-ttu-id="fee1c-133">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="fee1c-133">For example:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

> [!WARNING]
> <span data-ttu-id="fee1c-134">アプリケーションでは、コンテキストインスタンスごとに新しい ILoggerFactory インスタンスを作成しないことが非常に重要です。</span><span class="sxs-lookup"><span data-stu-id="fee1c-134">It is very important that applications do not create a new ILoggerFactory instance for each context instance.</span></span> <span data-ttu-id="fee1c-135">そうすると、メモリリークが発生し、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="fee1c-135">Doing so will result in a memory leak and poor performance.</span></span>

## <a name="filtering-what-is-logged"></a><span data-ttu-id="fee1c-136">ログ記録対象のフィルター処理</span><span class="sxs-lookup"><span data-stu-id="fee1c-136">Filtering what is logged</span></span>

<span data-ttu-id="fee1c-137">アプリケーションでは、ILoggerProvider でフィルターを構成することによって、ログ記録の内容を制御できます。</span><span class="sxs-lookup"><span data-stu-id="fee1c-137">The application can control what is logged by configuring a filter on the ILoggerProvider.</span></span> <span data-ttu-id="fee1c-138">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="fee1c-138">For example:</span></span>

### <a name="version-3x"></a>[<span data-ttu-id="fee1c-139">バージョン 3.x</span><span class="sxs-lookup"><span data-stu-id="fee1c-139">Version 3.x</span></span>](#tab/v3)

[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContextWithFiltering.cs#DefineLoggerFactory)]

### <a name="version-2x"></a>[<span data-ttu-id="fee1c-140">バージョン 2.x</span><span class="sxs-lookup"><span data-stu-id="fee1c-140">Version 2.x</span></span>](#tab/v2)

> [!NOTE]
> <span data-ttu-id="fee1c-141">次のコードサンプルでは、 `ConsoleLoggerProvider` バージョン2.2 で廃止され、3.0 で置き換えられたコンストラクターを使用しています。</span><span class="sxs-lookup"><span data-stu-id="fee1c-141">The following code sample uses a `ConsoleLoggerProvider` constructor that has been obsoleted in version 2.2 and replaced in 3.0.</span></span> <span data-ttu-id="fee1c-142">2.2 を使用すると、警告を無視して非表示にするのが安全です。</span><span class="sxs-lookup"><span data-stu-id="fee1c-142">It is safe to ignore and suppress the warnings when using 2.2.</span></span>

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

<span data-ttu-id="fee1c-143">この例では、ログは、メッセージのみを返すようにフィルター処理されています。</span><span class="sxs-lookup"><span data-stu-id="fee1c-143">In this example, the log is filtered to only return messages:</span></span>

* <span data-ttu-id="fee1c-144">' Microsoft. EntityFrameworkCore. Database. Command ' カテゴリ</span><span class="sxs-lookup"><span data-stu-id="fee1c-144">in the 'Microsoft.EntityFrameworkCore.Database.Command' category</span></span>
* <span data-ttu-id="fee1c-145">' 情報 ' レベル</span><span class="sxs-lookup"><span data-stu-id="fee1c-145">at the 'Information' level</span></span>

<span data-ttu-id="fee1c-146">EF Core の場合、logger カテゴリはクラスで定義されるため、 `DbLoggerCategory` カテゴリを簡単に見つけることができますが、単純な文字列に解決されます。</span><span class="sxs-lookup"><span data-stu-id="fee1c-146">For EF Core, logger categories are defined in the `DbLoggerCategory` class to make it easy to find categories, but these resolve to simple strings.</span></span>

<span data-ttu-id="fee1c-147">基になるログ記録インフラストラクチャの詳細については、 [ASP.NET Core のログ記録に関するドキュメント](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fee1c-147">More details on the underlying logging infrastructure can be found in the [ASP.NET Core logging documentation](/aspnet/core/fundamentals/logging?tabs=aspnetcore2x).</span></span>
