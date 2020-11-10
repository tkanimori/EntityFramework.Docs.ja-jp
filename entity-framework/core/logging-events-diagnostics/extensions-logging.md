---
title: EF Core を使用する
description: ASP.NET Core とその他のアプリケーションの種類での EF Core を使用したログ記録
author: ajcvickers
ms.date: 10/15/2020
uid: core/logging-events-diagnostics/extensions-logging
ms.openlocfilehash: 3732bda0ef29b05672a2dfb83405da802f899191
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431294"
---
# <a name="using-microsoftextensionslogging-in-ef-core"></a><span data-ttu-id="eac21-103">EF Core での Microsoft 拡張子の使用</span><span class="sxs-lookup"><span data-stu-id="eac21-103">Using Microsoft.Extensions.Logging in EF Core</span></span>

<span data-ttu-id="eac21-104">[Microsoft](/dotnet/core/extensions/logging) では、多くの一般的なログ記録システムに対応したプラグインプロバイダーを使用した拡張可能なログメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="eac21-104">[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) is an extensible logging mechanism with plug-in providers for many common logging systems.</span></span> <span data-ttu-id="eac21-105">Microsoft から提供されているプラグイン (例: [microsoft の拡張](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)機能) とサードパーティのプラグイン (たとえば、 [Serilog. 拡張子](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) は、NuGet パッケージとして入手できます。</span><span class="sxs-lookup"><span data-stu-id="eac21-105">Both Microsoft-supplied plug-ins (e.g [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)) and third-party plug-ins (e.g. [Serilog.Extensions.Logging](https://www.nuget.org/packages/Serilog.Extensions.Logging/)) are available as NuGet packages.</span></span>

<span data-ttu-id="eac21-106">Entity Framework Core (EF Core) はと完全に統合さ `Microsoft.Extensions.Logging` れています。</span><span class="sxs-lookup"><span data-stu-id="eac21-106">Entity Framework Core (EF Core) fully integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="eac21-107">ただし、特に依存関係の挿入を使用しないアプリケーションの場合は、 [単純](xref:core/logging-events-diagnostics/simple-logging) なログ記録を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="eac21-107">However, consider using [simple logging](xref:core/logging-events-diagnostics/simple-logging) for a simpler way to log, especially for applications that don't use dependency injection.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="eac21-108">ASP.NET Core アプリケーション</span><span class="sxs-lookup"><span data-stu-id="eac21-108">ASP.NET Core applications</span></span>

<span data-ttu-id="eac21-109">`Microsoft.Extensions.Logging` は [ASP.NET Core アプリケーションで既定で使用さ](/aspnet/core/fundamentals/logging)れます。</span><span class="sxs-lookup"><span data-stu-id="eac21-109">`Microsoft.Extensions.Logging` is [used by default in ASP.NET Core applications](/aspnet/core/fundamentals/logging).</span></span> <span data-ttu-id="eac21-110"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>または <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eac21-110">Calling <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A>.</span></span>

## <a name="other-application-types"></a><span data-ttu-id="eac21-111">その他のアプリケーションの種類</span><span class="sxs-lookup"><span data-stu-id="eac21-111">Other application types</span></span>

<span data-ttu-id="eac21-112">その他のアプリケーションの種類では、 [Generichost](/dotnet/core/extensions/generic-host) を使用して、ASP.NET Core で使用されているものと同じ依存関係挿入パターンを取得できます。</span><span class="sxs-lookup"><span data-stu-id="eac21-112">Other application types can use the [GenericHost](/dotnet/core/extensions/generic-host) to get the same dependency injection patterns as are used in ASP.NET Core.</span></span> <span data-ttu-id="eac21-113">AddDbContext または AddDbContextPool は、ASP.NET Core アプリケーションと同様に使用できます。</span><span class="sxs-lookup"><span data-stu-id="eac21-113">AddDbContext or AddDbContextPool can then be used just like in ASP.NET Core applications.</span></span>

<span data-ttu-id="eac21-114">`Microsoft.Extensions.Logging` は、依存関係の挿入を使用しないアプリケーションにも使用できます。ただし、 [単純なログ記録](xref:core/logging-events-diagnostics/simple-logging) は簡単に設定できます。</span><span class="sxs-lookup"><span data-stu-id="eac21-114">`Microsoft.Extensions.Logging` can also be used for applications that don't use dependency injection, although [simple logging](xref:core/logging-events-diagnostics/simple-logging) can be easier to set up.</span></span>

<span data-ttu-id="eac21-115">`Microsoft.Extensions.Logging` の作成が必要です <xref:Microsoft.Extensions.Logging.LoggerFactory> 。</span><span class="sxs-lookup"><span data-stu-id="eac21-115">`Microsoft.Extensions.Logging` requires creation of a <xref:Microsoft.Extensions.Logging.LoggerFactory>.</span></span> <span data-ttu-id="eac21-116">このファクトリは、任意の場所に静的/グローバルインスタンスとして格納し、DbContext が作成されるたびに使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eac21-116">This factory should be stored as a static/global instance somewhere and used each time a DbContext is created.</span></span> <span data-ttu-id="eac21-117">たとえば、ロガーファクトリは、DbContext の静的プロパティとして格納するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="eac21-117">For example, it is common to store the logger factory as a static property on the DbContext.</span></span>

### <a name="ef-core-30-and-above"></a>[<span data-ttu-id="eac21-118">3.0 以降の EF Core</span><span class="sxs-lookup"><span data-stu-id="eac21-118">EF Core 3.0 and above</span></span>](#tab/v3)

<!--
        public static readonly ILoggerFactory MyLoggerFactory
            = LoggerFactory.Create(builder => { builder.AddConsole(); });
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#DefineLoggerFactory)]

### <a name="ef-core-21"></a>[<span data-ttu-id="eac21-119">EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="eac21-119">EF Core 2.1</span></span>](#tab/v2)

```csharp
public static readonly LoggerFactory MyLoggerFactory
    = new LoggerFactory(new[] { new ConsoleLoggerProvider((_, __) => true, true) });
```

> [!WARNING]
> <span data-ttu-id="eac21-120">EF Core 2.1 では、アプリケーションで DbContext インスタンスごとに新しい Server.loggerfactory インスタンスが作成されないことが非常に重要です。</span><span class="sxs-lookup"><span data-stu-id="eac21-120">In EF Core 2.1, It is very important that applications do not create a new LoggerFactory instance for each DbContext instance.</span></span> <span data-ttu-id="eac21-121">そうすると、メモリリークが発生し、パフォーマンスが低下します。</span><span class="sxs-lookup"><span data-stu-id="eac21-121">Doing so will result in a memory leak and poor performance.</span></span> <span data-ttu-id="eac21-122">これは EF Core 3.0 以降で修正されました。</span><span class="sxs-lookup"><span data-stu-id="eac21-122">This has been fixed in EF Core 3.0 and above.</span></span>

***

<span data-ttu-id="eac21-123">このシングルトン/グローバルインスタンスは、の EF Core に登録する必要があり <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> ます。</span><span class="sxs-lookup"><span data-stu-id="eac21-123">This singleton/global instance should then be registered with EF Core on the <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="eac21-124">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eac21-124">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .UseLoggerFactory(MyLoggerFactory)
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFLogging;ConnectRetryCount=0");
-->
[!code-csharp[Main](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs#RegisterLoggerFactory)]

## <a name="getting-detailed-messages"></a><span data-ttu-id="eac21-125">詳細なメッセージを取得する</span><span class="sxs-lookup"><span data-stu-id="eac21-125">Getting detailed messages</span></span>

> [!TIP]
> <span data-ttu-id="eac21-126">AddDbContext が使用されている場合、または DbContext コンストラクターに DbContextOptions インスタンスが渡された場合、OnConfiguring は引き続き呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="eac21-126">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="eac21-127">これにより、DbContext の構築方法に関係なく、コンテキスト構成を適用するための理想的な場所となります。</span><span class="sxs-lookup"><span data-stu-id="eac21-127">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="eac21-128">機微なデータ</span><span class="sxs-lookup"><span data-stu-id="eac21-128">Sensitive data</span></span>

<span data-ttu-id="eac21-129">既定では、EF Core には、例外メッセージ内のデータの値は含まれません。</span><span class="sxs-lookup"><span data-stu-id="eac21-129">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="eac21-130">これは、このようなデータは機密情報である可能性があるため、例外が処理されない場合に実稼働環境でも明らかになる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="eac21-130">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="eac21-131">ただし、特にキーのデータ値を把握することは、デバッグ時に非常に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="eac21-131">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="eac21-132">これは、を呼び出すことによって EF Core で有効にすることができ <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> ます。</span><span class="sxs-lookup"><span data-stu-id="eac21-132">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="eac21-133">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eac21-133">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableSensitiveDataLogging();
-->
[!code-csharp[EnableSensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableSensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="eac21-134">詳細なクエリの例外</span><span class="sxs-lookup"><span data-stu-id="eac21-134">Detailed query exceptions</span></span>

<span data-ttu-id="eac21-135">パフォーマンス上の理由により、EF Core は、try-catch ブロック内のデータベースプロバイダーから値を読み取る各呼び出しをラップしません。</span><span class="sxs-lookup"><span data-stu-id="eac21-135">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="eac21-136">ただし、これにより、特に、モデルで許可されていない場合にデータベースが NULL を返す場合に、診断が困難な例外が発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="eac21-136">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="eac21-137">オンに <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> すると、EF によってこれらの try-catch ブロックが導入されるため、より詳細なエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="eac21-137">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="eac21-138">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eac21-138">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=EnableDetailedErrors)]

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="eac21-139">特定のメッセージの構成</span><span class="sxs-lookup"><span data-stu-id="eac21-139">Configuration for specific messages</span></span>

<span data-ttu-id="eac21-140">EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API を使用すると、特定のイベントが発生したときの動作をアプリケーションで変更できます。</span><span class="sxs-lookup"><span data-stu-id="eac21-140">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="eac21-141">これは次の場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="eac21-141">This can be used to:</span></span>

* <span data-ttu-id="eac21-142">イベントがログに記録されるログレベルを変更する</span><span class="sxs-lookup"><span data-stu-id="eac21-142">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="eac21-143">イベントのログ記録をスキップする</span><span class="sxs-lookup"><span data-stu-id="eac21-143">Skip logging the event altogether</span></span>
* <span data-ttu-id="eac21-144">イベントが発生したときに例外をスローする</span><span class="sxs-lookup"><span data-stu-id="eac21-144">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="eac21-145">イベントのログレベルの変更</span><span class="sxs-lookup"><span data-stu-id="eac21-145">Changing the log level for an event</span></span>

<span data-ttu-id="eac21-146">イベントの定義済みログレベルを変更すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="eac21-146">Sometimes it can be useful to change the pre-defined log level for an event.</span></span> <span data-ttu-id="eac21-147">たとえば、次の2つのイベントをからに昇格させるために使用でき `LogLevel.Debug` `LogLevel.Information` ます。</span><span class="sxs-lookup"><span data-stu-id="eac21-147">For example, this can be used to promote two additional events from `LogLevel.Debug` to `LogLevel.Information`:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Log(
                    (RelationalEventId.ConnectionOpened, LogLevel.Information),
                    (RelationalEventId.ConnectionClosed, LogLevel.Information)));
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="eac21-148">イベントのログ記録を抑制する</span><span class="sxs-lookup"><span data-stu-id="eac21-148">Suppress logging an event</span></span>

<span data-ttu-id="eac21-149">同様に、個々のイベントをログに記録しないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="eac21-149">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="eac21-150">これは、確認して理解した警告を無視する場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="eac21-150">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="eac21-151">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eac21-151">For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning));
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="eac21-152">イベントに対してスローする</span><span class="sxs-lookup"><span data-stu-id="eac21-152">Throw for an event</span></span>

<span data-ttu-id="eac21-153">最後に、特定のイベントに対してをスローするように EF Core を構成できます。</span><span class="sxs-lookup"><span data-stu-id="eac21-153">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="eac21-154">これは、警告をエラーに変更する場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="eac21-154">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="eac21-155">(実際には、これはメソッドの本来の目的であるため、という `ConfigureWarnings` 名前です)。例えば：</span><span class="sxs-lookup"><span data-stu-id="eac21-155">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder
                .ConfigureWarnings(b => b.Throw(RelationalEventId.QueryPossibleUnintendedUseOfEqualsWarning));
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/Logging/BloggingContext.cs?name=ThrowForEvent)]

## <a name="filtering-and-other-configuration"></a><span data-ttu-id="eac21-156">フィルター処理とその他の構成</span><span class="sxs-lookup"><span data-stu-id="eac21-156">Filtering and other configuration</span></span>

<span data-ttu-id="eac21-157">ログフィルターとその他の構成については、「 [.net でのログ記録](/dotnet/core/extensions/logging) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="eac21-157">See [Logging in .NET](/dotnet/core/extensions/logging) for guidance on log filtering and other configuration.</span></span>

<span data-ttu-id="eac21-158">EF Core ログイベントは、次のいずれかの方法で定義されます。</span><span class="sxs-lookup"><span data-stu-id="eac21-158">EF Core logging events are defined in one of:</span></span>

* <span data-ttu-id="eac21-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> すべての EF Core データベースプロバイダーに共通のイベントの場合</span><span class="sxs-lookup"><span data-stu-id="eac21-159"><xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> for events common to all EF Core database providers</span></span>
* <span data-ttu-id="eac21-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> すべてのリレーショナルデータベースプロバイダーに共通のイベントの場合</span><span class="sxs-lookup"><span data-stu-id="eac21-160"><xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> for events common to all relational database providers</span></span>
* <span data-ttu-id="eac21-161">現在のデータベースプロバイダーに固有のイベントの類似クラス。</span><span class="sxs-lookup"><span data-stu-id="eac21-161">A similar class for events specific to the current database provider.</span></span> <span data-ttu-id="eac21-162">たとえば、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> SQL Server プロバイダーの場合はです。</span><span class="sxs-lookup"><span data-stu-id="eac21-162">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="eac21-163">これらの定義には、によって使用される各イベントのイベント Id、ログレベル、およびカテゴリが含まれ `Microsoft.Extensions.Logging` ます。</span><span class="sxs-lookup"><span data-stu-id="eac21-163">These definitions contain the event IDs, log level, and category for each event, as used by `Microsoft.Extensions.Logging`.</span></span>
