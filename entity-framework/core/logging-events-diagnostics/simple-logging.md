---
title: 単純なログ記録-EF Core
description: LogTo を使用した EF Core DbContext からのログ記録
author: ajcvickers
ms.date: 10/03/2020
uid: core/logging-events-diagnostics/simple-logging
ms.openlocfilehash: 5c2dc41122dfa3919d1e6a26b0760883d77ee1a0
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129214"
---
# <a name="simple-logging"></a><span data-ttu-id="80fc5-103">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="80fc5-103">Simple Logging</span></span>

> [!NOTE]
> <span data-ttu-id="80fc5-104">この機能は EF Core 5.0 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="80fc5-104">This feature was introduced in EF Core 5.0.</span></span>

> [!TIP]
> <span data-ttu-id="80fc5-105">[この記事のサンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging/SimpleLogging)は GitHub からダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-105">You can [download this article's sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Logging/SimpleLogging) from GitHub.</span></span>

<span data-ttu-id="80fc5-106">Entity Framework Core (EF Core) 単純なログを使用して、アプリケーションの開発とデバッグを行うときに簡単にログを取得できます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-106">Entity Framework Core (EF Core) simple logging can be used to easily obtain logs while developing and debugging applications.</span></span> <span data-ttu-id="80fc5-107">この形式のログ記録では、最小限の構成と追加の NuGet パッケージは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="80fc5-107">This form of logging requires minimal configuration and no additional NuGet packages.</span></span>

> [!TIP]
> <span data-ttu-id="80fc5-108">また EF Core は、さらに多くの構成を必要とする [が、多く](xref:core/logging-events-diagnostics/extensions-logging)の場合、実稼働アプリケーションでのログ記録に適しています。</span><span class="sxs-lookup"><span data-stu-id="80fc5-108">EF Core also integrates with [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging), which requires more configuration, but is often more suitable for logging in production applications.</span></span>

## <a name="configuration"></a><span data-ttu-id="80fc5-109">構成</span><span class="sxs-lookup"><span data-stu-id="80fc5-109">Configuration</span></span>

<span data-ttu-id="80fc5-110">[DbContext インスタンスの構成](xref:core/dbcontext-configuration/index)時に <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> を使用すると、任意の種類のアプリケーションから EF Core ログにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-110">EF Core logs can be accessed from any type of application through the use of <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> when [configuring a DbContext instance](xref:core/dbcontext-configuration/index).</span></span> <span data-ttu-id="80fc5-111">この構成は、通常、<xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> のオーバーライドで行われます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-111">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="80fc5-112">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="80fc5-112">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="80fc5-113">`LogTo`または、の一部として、 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> または <xref:Microsoft.EntityFrameworkCore.DbContextOptions> コンストラクターに渡すインスタンスを作成するときに、を呼び出すことができ `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-113">Alternately, `LogTo` can be called as part of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> or when creating a <xref:Microsoft.EntityFrameworkCore.DbContextOptions> instance to pass to the `DbContext` constructor.</span></span>

> [!TIP]
> <span data-ttu-id="80fc5-114">AddDbContext が使用されている場合、または DbContext コンストラクターに DbContextOptions インスタンスが渡された場合、OnConfiguring は引き続き呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-114">OnConfiguring is still called when AddDbContext is used or a DbContextOptions instance is passed to the DbContext constructor.</span></span> <span data-ttu-id="80fc5-115">これにより、DbContext の構築方法に関係なく、コンテキスト構成を適用するための理想的な場所となります。</span><span class="sxs-lookup"><span data-stu-id="80fc5-115">This makes it the ideal place to apply context configuration regardless of how the DbContext is constructed.</span></span>

## <a name="directing-the-logs"></a><span data-ttu-id="80fc5-116">ログの指示</span><span class="sxs-lookup"><span data-stu-id="80fc5-116">Directing the logs</span></span>

### <a name="logging-to-the-console"></a><span data-ttu-id="80fc5-117">コンソールへのログ記録</span><span class="sxs-lookup"><span data-stu-id="80fc5-117">Logging to the console</span></span>

<span data-ttu-id="80fc5-118">`LogTo`<xref:System.Action%601>文字列を受け入れるデリゲートが必要です。</span><span class="sxs-lookup"><span data-stu-id="80fc5-118">`LogTo` requires an <xref:System.Action%601> delegate that accepts a string.</span></span> <span data-ttu-id="80fc5-119">EF Core は、生成されたログメッセージごとに文字列を使用して、このデリゲートを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="80fc5-119">EF Core will call this delegate with a string for each log message generated.</span></span> <span data-ttu-id="80fc5-120">次に、指定されたメッセージで何かを実行するデリゲートが作成されます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-120">It is then up to the delegate to do something with the given message.</span></span>

<span data-ttu-id="80fc5-121"><xref:System.Console.WriteLine%2A?displayProperty=nameWithType>このデリゲートには、前述のようにメソッドがよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-121">The <xref:System.Console.WriteLine%2A?displayProperty=nameWithType> method is often used for this delegate, as shown above.</span></span> <span data-ttu-id="80fc5-122">この結果、各ログメッセージがコンソールに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-122">This results in each log message being written to the console.</span></span>

### <a name="logging-to-the-debug-window"></a><span data-ttu-id="80fc5-123">[デバッグ] ウィンドウへのログ記録</span><span class="sxs-lookup"><span data-stu-id="80fc5-123">Logging to the debug window</span></span>

<span data-ttu-id="80fc5-124"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> は、Visual Studio または他の Ide のデバッグウィンドウに出力を送信するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-124"><xref:System.Diagnostics.Debug.WriteLine%2A?displayProperty=nameWithType> can be used to send output to the Debug window in Visual Studio or other IDEs.</span></span> <span data-ttu-id="80fc5-125">この場合、クラスはリリースビルドからコンパイルされるため、[ラムダ構文](/dotnet/csharp/language-reference/operators/lambda-expressions)を使用する必要があり `Debug` ます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-125">[Lambda syntax](/dotnet/csharp/language-reference/operators/lambda-expressions) must be used in this case because the `Debug` class is compiled out of release builds.</span></span> <span data-ttu-id="80fc5-126">例:</span><span class="sxs-lookup"><span data-stu-id="80fc5-126">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(message => Debug.WriteLine(message));
-->
[!code-csharp[LogToDebug](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToDebug)]

### <a name="logging-to-a-file"></a><span data-ttu-id="80fc5-127">ファイルへのログ記録</span><span class="sxs-lookup"><span data-stu-id="80fc5-127">Logging to a file</span></span>

<span data-ttu-id="80fc5-128">ファイルに書き込むに <xref:System.IO.StreamWriter> は、ファイルのまたは同様のを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="80fc5-128">Writing to a file requires creating a <xref:System.IO.StreamWriter> or similar for the file.</span></span> <span data-ttu-id="80fc5-129">この <xref:System.IO.StreamWriter.WriteLine%2A> メソッドは、上記の他の例のように使用できます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-129">The <xref:System.IO.StreamWriter.WriteLine%2A> method can then be used as in the other examples above.</span></span> <span data-ttu-id="80fc5-130">コンテキストが破棄されたときにライターを破棄することによって、ファイルが正常に閉じられていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="80fc5-130">Remember to ensure the file is closed cleanly by disposing the writer when the context is disposed.</span></span> <span data-ttu-id="80fc5-131">例:</span><span class="sxs-lookup"><span data-stu-id="80fc5-131">For example:</span></span>

<!--
    private readonly StreamWriter _logStream = new StreamWriter("mylog.txt", append: true);

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(_logStream.WriteLine);

    public override void Dispose()
    {
        base.Dispose();
        _logStream.Dispose();
    }

    public override async ValueTask DisposeAsync()
    {
        await base.DisposeAsync();
        await _logStream.DisposeAsync();
    }
-->
[!code-csharp[LogToFile](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToFile)]

> [!TIP]
> <span data-ttu-id="80fc5-132">実稼働アプリケーションのファイルへのログ記録には、 [Microsoft の拡張子](/aspnet/core/fundamentals/logging) を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="80fc5-132">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for logging to files in production applications.</span></span>

## <a name="getting-detailed-messages"></a><span data-ttu-id="80fc5-133">詳細なメッセージを取得する</span><span class="sxs-lookup"><span data-stu-id="80fc5-133">Getting detailed messages</span></span>

### <a name="sensitive-data"></a><span data-ttu-id="80fc5-134">機微なデータ</span><span class="sxs-lookup"><span data-stu-id="80fc5-134">Sensitive data</span></span>

<span data-ttu-id="80fc5-135">既定では、EF Core には、例外メッセージ内のデータの値は含まれません。</span><span class="sxs-lookup"><span data-stu-id="80fc5-135">By default, EF Core will not include the values of any data in exception messages.</span></span> <span data-ttu-id="80fc5-136">これは、このようなデータは機密情報である可能性があるため、例外が処理されない場合に実稼働環境でも明らかになる可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="80fc5-136">This is because such data may be confidential, and could be revealed in production use if an exception is not handled.</span></span>

<span data-ttu-id="80fc5-137">ただし、特にキーのデータ値を把握することは、デバッグ時に非常に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-137">However, knowing data values, especially for keys, can be very helpful when debugging.</span></span> <span data-ttu-id="80fc5-138">これは、を呼び出すことによって EF Core で有効にすることができ <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging> ます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-138">This can be enabled in EF Core by calling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging>.</span></span> <span data-ttu-id="80fc5-139">例:</span><span class="sxs-lookup"><span data-stu-id="80fc5-139">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
-->
[!code-csharp[SensitiveDataLogging](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SensitiveDataLogging)]

### <a name="detailed-query-exceptions"></a><span data-ttu-id="80fc5-140">詳細なクエリの例外</span><span class="sxs-lookup"><span data-stu-id="80fc5-140">Detailed query exceptions</span></span>

<span data-ttu-id="80fc5-141">パフォーマンス上の理由により、EF Core は、try-catch ブロック内のデータベースプロバイダーから値を読み取る各呼び出しをラップしません。</span><span class="sxs-lookup"><span data-stu-id="80fc5-141">For performance reasons, EF Core does not wrap each call to read a value from the database provider in a try-catch block.</span></span> <span data-ttu-id="80fc5-142">ただし、これにより、特に、モデルで許可されていない場合にデータベースが NULL を返す場合に、診断が困難な例外が発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="80fc5-142">However, this sometimes results in exceptions that are hard to diagnose, especially when the database returns a NULL when not allowed by the model.</span></span>

<span data-ttu-id="80fc5-143">オンに <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> すると、EF によってこれらの try-catch ブロックが導入されるため、より詳細なエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="80fc5-143">Turning on <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A> will cause EF to introduce these try-catch blocks and thereby provide more detailed errors.</span></span> <span data-ttu-id="80fc5-144">例:</span><span class="sxs-lookup"><span data-stu-id="80fc5-144">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine)
            .EnableDetailedErrors();
-->
[!code-csharp[EnableDetailedErrors](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EnableDetailedErrors)]

## <a name="filtering"></a><span data-ttu-id="80fc5-145">フィルター処理</span><span class="sxs-lookup"><span data-stu-id="80fc5-145">Filtering</span></span>

### <a name="log-levels"></a><span data-ttu-id="80fc5-146">ログ レベル</span><span class="sxs-lookup"><span data-stu-id="80fc5-146">Log levels</span></span>

<span data-ttu-id="80fc5-147">すべての EF Core ログメッセージは、列挙型によって定義されたレベルに割り当てられ <xref:Microsoft.Extensions.Logging.LogLevel> ます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-147">Every EF Core log message is assigned to a level defined by the <xref:Microsoft.Extensions.Logging.LogLevel> enum.</span></span> <span data-ttu-id="80fc5-148">既定では EF Core 簡易ログ記録には、レベル以上のすべてのメッセージが含まれ `Debug` ます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-148">By default, EF Core simple logging includes every message at `Debug` level or above.</span></span> <span data-ttu-id="80fc5-149">`LogTo` は、一部のメッセージをフィルターで除外するために、より高いレベルを渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-149">`LogTo` can be passed a higher minimum level to filter out some messages.</span></span> <span data-ttu-id="80fc5-150">たとえば、を渡す `Information` と、データベースアクセスと一部のハウスキーピングメッセージに限定されたログの最小セットが得られます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-150">For example, passing `Information` results in a minimal set of logs limited to database access and some housekeeping messages.</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[InfoOnly](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=InfoOnly)]

### <a name="specific-messages"></a><span data-ttu-id="80fc5-151">特定のメッセージ</span><span class="sxs-lookup"><span data-stu-id="80fc5-151">Specific messages</span></span>

<span data-ttu-id="80fc5-152">すべてのログメッセージにはが割り当てられ <xref:Microsoft.Extensions.Logging.EventId> ます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-152">Every log message is assigned an <xref:Microsoft.Extensions.Logging.EventId>.</span></span> <span data-ttu-id="80fc5-153">これらの Id <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> には、クラスから、または <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> リレーショナル固有のメッセージのクラスからアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-153">These IDs can be accessed from the <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> class or the <xref:Microsoft.EntityFrameworkCore.Diagnostics.RelationalEventId> class for relational-specific messages.</span></span> <span data-ttu-id="80fc5-154">データベースプロバイダーには、同様のクラスのプロバイダー固有の Id が含まれる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="80fc5-154">A database provider may also have provider-specific IDs in a similar class.</span></span> <span data-ttu-id="80fc5-155">たとえば、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> SQL Server プロバイダーの場合はです。</span><span class="sxs-lookup"><span data-stu-id="80fc5-155">For example, <xref:Microsoft.EntityFrameworkCore.Diagnostics.SqlServerEventId> for the SQL Server provider.</span></span>

<span data-ttu-id="80fc5-156">`LogTo` は、1つまたは複数のイベント Id に関連付けられたメッセージのみをログに記録するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-156">`LogTo` can be configured to only log the messages associated with one or more event IDs.</span></span> <span data-ttu-id="80fc5-157">たとえば、初期化または破棄されたコンテキストのメッセージのみをログに記録するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="80fc5-157">For example, to log only messages for the context being initialized or disposed:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { CoreEventId.ContextDisposed, CoreEventId.ContextInitialized });
-->
[!code-csharp[EventIds](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=EventIds)]

### <a name="message-categories"></a><span data-ttu-id="80fc5-158">メッセージの種類</span><span class="sxs-lookup"><span data-stu-id="80fc5-158">Message categories</span></span>

<span data-ttu-id="80fc5-159">すべてのログメッセージは、名前付きの階層 logger カテゴリに割り当てられます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-159">Every log message is assigned to a named hierarchical logger category.</span></span> <span data-ttu-id="80fc5-160">カテゴリは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="80fc5-160">The categories are:</span></span>

| <span data-ttu-id="80fc5-161">カテゴリ</span><span class="sxs-lookup"><span data-stu-id="80fc5-161">Category</span></span>                                             | <span data-ttu-id="80fc5-162">メッセージ</span><span class="sxs-lookup"><span data-stu-id="80fc5-162">Messages</span></span>
|:-----------------------------------------------------|-------------------------------------------------
| <span data-ttu-id="80fc5-163">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="80fc5-163">Microsoft.EntityFrameworkCore</span></span>                        | <span data-ttu-id="80fc5-164">すべての EF Core メッセージ</span><span class="sxs-lookup"><span data-stu-id="80fc5-164">All EF Core messages</span></span>
| <span data-ttu-id="80fc5-165">Microsoft EntityFrameworkCore. データベース</span><span class="sxs-lookup"><span data-stu-id="80fc5-165">Microsoft.EntityFrameworkCore.Database</span></span>               | <span data-ttu-id="80fc5-166">すべてのデータベースの相互作用</span><span class="sxs-lookup"><span data-stu-id="80fc5-166">All database interactions</span></span>
| <span data-ttu-id="80fc5-167">Microsoft EntityFrameworkCore. Database. Connection</span><span class="sxs-lookup"><span data-stu-id="80fc5-167">Microsoft.EntityFrameworkCore.Database.Connection</span></span>    | <span data-ttu-id="80fc5-168">データベース接続の使用</span><span class="sxs-lookup"><span data-stu-id="80fc5-168">Uses of a database connection</span></span>
| <span data-ttu-id="80fc5-169">Microsoft EntityFrameworkCore. Database. Command</span><span class="sxs-lookup"><span data-stu-id="80fc5-169">Microsoft.EntityFrameworkCore.Database.Command</span></span>       | <span data-ttu-id="80fc5-170">データベースコマンドの使用</span><span class="sxs-lookup"><span data-stu-id="80fc5-170">Uses of a database command</span></span>
| <span data-ttu-id="80fc5-171">Microsoft EntityFrameworkCore. Database. Transaction</span><span class="sxs-lookup"><span data-stu-id="80fc5-171">Microsoft.EntityFrameworkCore.Database.Transaction</span></span>   | <span data-ttu-id="80fc5-172">データベーストランザクションの使用</span><span class="sxs-lookup"><span data-stu-id="80fc5-172">Uses of a database transaction</span></span>
| <span data-ttu-id="80fc5-173">Microsoft EntityFrameworkCore. 更新プログラム</span><span class="sxs-lookup"><span data-stu-id="80fc5-173">Microsoft.EntityFrameworkCore.Update</span></span>                 | <span data-ttu-id="80fc5-174">エンティティの保存、データベースとのやり取りの除外</span><span class="sxs-lookup"><span data-stu-id="80fc5-174">Saving entities, excluding database interactions</span></span>
| <span data-ttu-id="80fc5-175">Microsoft EntityFrameworkCore. Model</span><span class="sxs-lookup"><span data-stu-id="80fc5-175">Microsoft.EntityFrameworkCore.Model</span></span>                  | <span data-ttu-id="80fc5-176">すべてのモデルとメタデータの相互作用</span><span class="sxs-lookup"><span data-stu-id="80fc5-176">All model and metadata interactions</span></span>
| <span data-ttu-id="80fc5-177">Microsoft EntityFrameworkCore。検証</span><span class="sxs-lookup"><span data-stu-id="80fc5-177">Microsoft.EntityFrameworkCore.Model.Validation</span></span>       | <span data-ttu-id="80fc5-178">モデルの検証</span><span class="sxs-lookup"><span data-stu-id="80fc5-178">Model validation</span></span>
| <span data-ttu-id="80fc5-179">Microsoft EntityFrameworkCore. Query</span><span class="sxs-lookup"><span data-stu-id="80fc5-179">Microsoft.EntityFrameworkCore.Query</span></span>                  | <span data-ttu-id="80fc5-180">クエリ、データベースとのやり取りの除外</span><span class="sxs-lookup"><span data-stu-id="80fc5-180">Queries, excluding database interactions</span></span>
| <span data-ttu-id="80fc5-181">Microsoft EntityFrameworkCore インフラストラクチャ</span><span class="sxs-lookup"><span data-stu-id="80fc5-181">Microsoft.EntityFrameworkCore.Infrastructure</span></span>         | <span data-ttu-id="80fc5-182">一般的なイベント (コンテキストの作成など)</span><span class="sxs-lookup"><span data-stu-id="80fc5-182">General events, such as context creation</span></span>
| <span data-ttu-id="80fc5-183">Microsoft EntityFrameworkCore. スキャフォールディング</span><span class="sxs-lookup"><span data-stu-id="80fc5-183">Microsoft.EntityFrameworkCore.Scaffolding</span></span>            | <span data-ttu-id="80fc5-184">データベースのリバースエンジニアリング</span><span class="sxs-lookup"><span data-stu-id="80fc5-184">Database reverse engineering</span></span>
| <span data-ttu-id="80fc5-185">Microsoft EntityFrameworkCore. 移行</span><span class="sxs-lookup"><span data-stu-id="80fc5-185">Microsoft.EntityFrameworkCore.Migrations</span></span>             | <span data-ttu-id="80fc5-186">移行</span><span class="sxs-lookup"><span data-stu-id="80fc5-186">Migrations</span></span>
| <span data-ttu-id="80fc5-187">Microsoft EntityFrameworkCore. ChangeTracking</span><span class="sxs-lookup"><span data-stu-id="80fc5-187">Microsoft.EntityFrameworkCore.ChangeTracking</span></span>         | <span data-ttu-id="80fc5-188">変更の追跡の相互作用</span><span class="sxs-lookup"><span data-stu-id="80fc5-188">Change tracking interactions</span></span>

<span data-ttu-id="80fc5-189">`LogTo` は、1つまたは複数のカテゴリからのメッセージのみをログに記録するように構成できます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-189">`LogTo` can be configured to only log the messages from one or more categories.</span></span> <span data-ttu-id="80fc5-190">たとえば、データベースの相互作用のみをログに記録するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="80fc5-190">For example, to log only database interactions:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(Console.WriteLine, new[] { DbLoggerCategory.Database.Name });
-->
[!code-csharp[DatabaseCategory](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseCategory)]

<span data-ttu-id="80fc5-191">クラスは、 <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> カテゴリを検索するための階層的な API を提供し、文字列をハードコーディングする必要がないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="80fc5-191">Notice that the <xref:Microsoft.EntityFrameworkCore.DbLoggerCategory> class provides a hierarchical API for finding a category and avoids the need to hard-code strings.</span></span>

<span data-ttu-id="80fc5-192">カテゴリは階層構造であるため、カテゴリを使用するこの例に `Database` は、サブカテゴリのすべてのメッセージ (、、および) が含まれ `Database.Connection` `Database.Command` `Database.Transaction` ます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-192">Since categories are hierarchical, this example using the `Database` category will include all messages for the subcategories `Database.Connection`, `Database.Command`, and `Database.Transaction`.</span></span>

### <a name="custom-filters"></a><span data-ttu-id="80fc5-193">カスタム フィルター</span><span class="sxs-lookup"><span data-stu-id="80fc5-193">Custom filters</span></span>

<span data-ttu-id="80fc5-194">`LogTo` 上のフィルターオプションが十分でない場合に、カスタムフィルターを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="80fc5-194">`LogTo` allows a custom filter to be used for cases where none of the filtering options above are sufficient.</span></span> <span data-ttu-id="80fc5-195">たとえば、レベル以上のメッセージ、 `Information` および接続を開いたり閉じたりするためのメッセージをログに記録する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="80fc5-195">For example, to log any message at level `Information` or above, as well as messages for opening and closing a connection:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .LogTo(
                Console.WriteLine,
                (eventId, logLevel) => logLevel >= LogLevel.Information
                                       || eventId == RelationalEventId.ConnectionOpened
                                       || eventId == RelationalEventId.ConnectionClosed);
-->
[!code-csharp[CustomFilter](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=CustomFilter)]

> [!TIP]
> <span data-ttu-id="80fc5-196">カスタムフィルターを使用したフィルター処理、またはここに示されている他のオプションの使用は、デリゲートでのフィルター処理よりも効率的です `LogTo` 。</span><span class="sxs-lookup"><span data-stu-id="80fc5-196">Filtering using custom filters or using any of the other options shown here is more efficient than filtering in the `LogTo` delegate.</span></span> <span data-ttu-id="80fc5-197">これは、フィルターによってメッセージがログに記録されないと判断された場合、ログメッセージは作成されません。</span><span class="sxs-lookup"><span data-stu-id="80fc5-197">This is because if the filter determines the message should not be logged, then the log message is not even created.</span></span>

## <a name="configuration-for-specific-messages"></a><span data-ttu-id="80fc5-198">特定のメッセージの構成</span><span class="sxs-lookup"><span data-stu-id="80fc5-198">Configuration for specific messages</span></span>

<span data-ttu-id="80fc5-199">EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API を使用すると、特定のイベントが発生したときの動作をアプリケーションで変更できます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-199">The EF Core <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A> API allows applications to change what happens when a specific event is encountered.</span></span> <span data-ttu-id="80fc5-200">これは次の場合に使用できます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-200">This can be used to:</span></span>

* <span data-ttu-id="80fc5-201">イベントがログに記録されるログレベルを変更する</span><span class="sxs-lookup"><span data-stu-id="80fc5-201">Change the log level at which the event is logged</span></span>
* <span data-ttu-id="80fc5-202">イベントのログ記録をスキップする</span><span class="sxs-lookup"><span data-stu-id="80fc5-202">Skip logging the event altogether</span></span>
* <span data-ttu-id="80fc5-203">イベントが発生したときに例外をスローする</span><span class="sxs-lookup"><span data-stu-id="80fc5-203">Throw an exception when the event occurs</span></span>

### <a name="changing-the-log-level-for-an-event"></a><span data-ttu-id="80fc5-204">イベントのログレベルの変更</span><span class="sxs-lookup"><span data-stu-id="80fc5-204">Changing the log level for an event</span></span>

<span data-ttu-id="80fc5-205">前の例では、カスタムフィルターを使用して、 `LogLevel.Information` のすべてのメッセージと、に対して定義されている2つのイベントをログに記録して `LogLevel.Debug` います。</span><span class="sxs-lookup"><span data-stu-id="80fc5-205">The previous example used a custom filter to log every message at `LogLevel.Information` as well as two events defined for `LogLevel.Debug`.</span></span> <span data-ttu-id="80fc5-206">次の2つのイベントのログレベルをに変更することで、同じことを実現でき `Debug` `Information` ます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-206">The same can be achieved by changing the log level of the two `Debug` events to `Information`:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Log(
                (RelationalEventId.ConnectionOpened, LogLevel.Information),
                (RelationalEventId.ConnectionClosed, LogLevel.Information)))
            .LogTo(Console.WriteLine, LogLevel.Information);
-->
[!code-csharp[ChangeLogLevel](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ChangeLogLevel)]

### <a name="suppress-logging-an-event"></a><span data-ttu-id="80fc5-207">イベントのログ記録を抑制する</span><span class="sxs-lookup"><span data-stu-id="80fc5-207">Suppress logging an event</span></span>

<span data-ttu-id="80fc5-208">同様に、個々のイベントをログに記録しないようにすることもできます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-208">In a similar way, an individual event can be suppressed from logging.</span></span> <span data-ttu-id="80fc5-209">これは、確認して理解した警告を無視する場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="80fc5-209">This is particularly useful for ignoring a warning that has been reviewed and understood.</span></span> <span data-ttu-id="80fc5-210">例:</span><span class="sxs-lookup"><span data-stu-id="80fc5-210">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Ignore(CoreEventId.DetachedLazyLoadingWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[SuppressMessage](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SuppressMessage)]

### <a name="throw-for-an-event"></a><span data-ttu-id="80fc5-211">イベントに対してスローする</span><span class="sxs-lookup"><span data-stu-id="80fc5-211">Throw for an event</span></span>

<span data-ttu-id="80fc5-212">最後に、特定のイベントに対してをスローするように EF Core を構成できます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-212">Finally, EF Core can be configured to throw for a given event.</span></span> <span data-ttu-id="80fc5-213">これは、警告をエラーに変更する場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="80fc5-213">This is particularly useful for changing a warning into an error.</span></span> <span data-ttu-id="80fc5-214">(実際には、これはメソッドの本来の目的であるため、という `ConfigureWarnings` 名前です)。例えば：</span><span class="sxs-lookup"><span data-stu-id="80fc5-214">(Indeed, this was the original purpose of `ConfigureWarnings` method, hence the name.) For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder
            .ConfigureWarnings(b => b.Throw(RelationalEventId.MultipleCollectionIncludeWarning))
            .LogTo(Console.WriteLine);
-->
[!code-csharp[ThrowForEvent](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=ThrowForEvent)]

## <a name="message-contents-and-formatting"></a><span data-ttu-id="80fc5-215">メッセージの内容と書式設定</span><span class="sxs-lookup"><span data-stu-id="80fc5-215">Message contents and formatting</span></span>

<span data-ttu-id="80fc5-216">の既定の内容 `LogTo` は、複数の行にわたって書式設定されます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-216">The default content from `LogTo` is formatted across multiple lines.</span></span> <span data-ttu-id="80fc5-217">最初の行には、メッセージのメタデータが含まれています。</span><span class="sxs-lookup"><span data-stu-id="80fc5-217">The first line contains message metadata:</span></span>

* <span data-ttu-id="80fc5-218"><xref:Microsoft.Extensions.Logging.LogLevel>4 文字のプレフィックスとしての</span><span class="sxs-lookup"><span data-stu-id="80fc5-218">The <xref:Microsoft.Extensions.Logging.LogLevel> as a four-character prefix</span></span>
* <span data-ttu-id="80fc5-219">現在のカルチャに対して書式設定されたローカルタイムスタンプ</span><span class="sxs-lookup"><span data-stu-id="80fc5-219">A local timestamp, formatted for the current culture</span></span>
* <span data-ttu-id="80fc5-220"><xref:Microsoft.Extensions.Logging.EventId>コピー/貼り付けを使用して、 <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> または他のいずれかのクラスからのメンバーを取得し、 `EventId` 生の ID 値を含む、フォーム内の。</span><span class="sxs-lookup"><span data-stu-id="80fc5-220">The <xref:Microsoft.Extensions.Logging.EventId> in the form that can be copy/pasted to get the member from <xref:Microsoft.EntityFrameworkCore.Diagnostics.CoreEventId> or one of the other `EventId` classes, plus the raw ID value</span></span>
* <span data-ttu-id="80fc5-221">前に説明したように、イベントカテゴリ。</span><span class="sxs-lookup"><span data-stu-id="80fc5-221">The event category, as described above.</span></span>

<span data-ttu-id="80fc5-222">例:</span><span class="sxs-lookup"><span data-stu-id="80fc5-222">For example:</span></span>

```output
info: 10/6/2020 10:52:45.581 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 10/6/2020 10:52:45.582 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 10/6/2020 10:52:45.585 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

<span data-ttu-id="80fc5-223">このコンテンツは <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> 、次のセクションに示すように、から値を渡すことによってカスタマイズできます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-223">This content can be customized by passing values from <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions>, as shown in the following sections.</span></span>

> [!TIP]
> <span data-ttu-id="80fc5-224">ログの書式設定をより詳細に制御するには、 [Microsoft の拡張子](/aspnet/core/fundamentals/logging) を使用することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="80fc5-224">Consider using [Microsoft.Extensions.Logging](/aspnet/core/fundamentals/logging) for more control over log formatting.</span></span>

### <a name="using-utc-time"></a><span data-ttu-id="80fc5-225">UTC 時刻の使用</span><span class="sxs-lookup"><span data-stu-id="80fc5-225">Using UTC time</span></span>

<span data-ttu-id="80fc5-226">既定では、タイムスタンプはデバッグ中にローカルで使用するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="80fc5-226">By default, timestamps are designed for local consumption while debugging.</span></span> <span data-ttu-id="80fc5-227"><xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType>代わりに、カルチャに依存しない UTC タイムスタンプを使用するためにを使用します。ただし、その他はすべて同じままにしておきます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-227">Use <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.DefaultWithUtcTime?displayProperty=nameWithType> to use culture-agnostic UTC timestamps instead, but keep everything else the same.</span></span> <span data-ttu-id="80fc5-228">例:</span><span class="sxs-lookup"><span data-stu-id="80fc5-228">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithUtcTime);
-->
[!code-csharp[Utc](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=Utc)]

<span data-ttu-id="80fc5-229">この例では、次のようにログの書式が設定されます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-229">This example results in the following log formatting:</span></span>

```output
info: 2020-10-06T17:55:39.0333701Z RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE "Blogs" (
          "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,
          "Name" INTEGER NOT NULL
      );
dbug: 2020-10-06T17:55:39.0333892Z RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committing transaction.
dbug: 2020-10-06T17:55:39.0351684Z RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction)
      Committed transaction.
```

### <a name="single-line-logging"></a><span data-ttu-id="80fc5-230">単一行のログ記録</span><span class="sxs-lookup"><span data-stu-id="80fc5-230">Single line logging</span></span>

<span data-ttu-id="80fc5-231">ログメッセージごとに1行だけを取得すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="80fc5-231">Sometimes it is useful to get exactly one line per log message.</span></span> <span data-ttu-id="80fc5-232">これは、で有効にすることができ <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType> ます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-232">This can be enabled by <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions.SingleLine?displayProperty=nameWithType>.</span></span> <span data-ttu-id="80fc5-233">例:</span><span class="sxs-lookup"><span data-stu-id="80fc5-233">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.DefaultWithLocalTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[SingleLine](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=SingleLine)]

<span data-ttu-id="80fc5-234">この例では、次のようにログの書式が設定されます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-234">This example results in the following log formatting:</span></span>

```output
info: 10/6/2020 10:52:45.723 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command) -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
dbug: 10/6/2020 10:52:45.723 RelationalEventId.TransactionCommitting[20210] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committing transaction.
dbug: 10/6/2020 10:52:45.725 RelationalEventId.TransactionCommitted[20202] (Microsoft.EntityFrameworkCore.Database.Transaction) -> Committed transaction.
```

### <a name="other-content-options"></a><span data-ttu-id="80fc5-235">その他のコンテンツオプション</span><span class="sxs-lookup"><span data-stu-id="80fc5-235">Other content options</span></span>

<span data-ttu-id="80fc5-236">の他のフラグを <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> 使用して、ログに含まれるメタデータの量を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-236">Other flags in <xref:Microsoft.EntityFrameworkCore.Diagnostics.DbContextLoggerOptions> can be used to trim down the amount of metadata included in the log.</span></span> <span data-ttu-id="80fc5-237">これは、単一行ログと組み合わせて使用すると便利です。</span><span class="sxs-lookup"><span data-stu-id="80fc5-237">This is can be useful in conjunction with single-line logging.</span></span> <span data-ttu-id="80fc5-238">例:</span><span class="sxs-lookup"><span data-stu-id="80fc5-238">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(
            Console.WriteLine,
            LogLevel.Debug,
            DbContextLoggerOptions.UtcTime | DbContextLoggerOptions.SingleLine);
-->
[!code-csharp[TerseLogs](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=TerseLogs)]

<span data-ttu-id="80fc5-239">この例では、次のようにログの書式が設定されます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-239">This example results in the following log formatting:</span></span>

```output
2020-10-06T17:52:45.7320362Z -> Executed DbCommand (0ms) [Parameters=[], CommandType='Text', CommandTimeout='30']CREATE TABLE "Blogs" (    "Id" INTEGER NOT NULL CONSTRAINT "PK_Blogs" PRIMARY KEY AUTOINCREMENT,    "Name" INTEGER NOT NULL);
2020-10-06T17:52:45.7320531Z -> Committing transaction.
2020-10-06T17:52:45.7339441Z -> Committed transaction.
```

## <a name="moving-from-ef6"></a><span data-ttu-id="80fc5-240">EF6 からの移動</span><span class="sxs-lookup"><span data-stu-id="80fc5-240">Moving from EF6</span></span>

<span data-ttu-id="80fc5-241">EF Core 単純なログ記録は、 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> 次の2つの重要な点で EF6 で異なります。</span><span class="sxs-lookup"><span data-stu-id="80fc5-241">EF Core simple logging differs from <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6 in two important ways:</span></span>

* <span data-ttu-id="80fc5-242">ログメッセージは、データベースとの対話のみに制限されません。</span><span class="sxs-lookup"><span data-stu-id="80fc5-242">Log messages are not limited to only database interactions</span></span>
* <span data-ttu-id="80fc5-243">ログは、コンテキストの初期化時に構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="80fc5-243">The logging must be configured at context initialization time</span></span>

<span data-ttu-id="80fc5-244">最初の違いとして、上記のフィルター処理を使用して、ログに記録するメッセージを制限できます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-244">For the first difference, the filtering described above can be used to limit which messages are logged.</span></span>

<span data-ttu-id="80fc5-245">2番目の違いは、不要になったログメッセージを生成しないことで、パフォーマンスを向上させるための意図的な変更です。</span><span class="sxs-lookup"><span data-stu-id="80fc5-245">The second difference is an intentional change to improve performance by not generating log messages when they are not needed.</span></span> <span data-ttu-id="80fc5-246">ただし、でプロパティを作成し、設定されている場合にのみ使用することで、EF6 と同様の動作を実現でき `Log` `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="80fc5-246">However, it is still possible to get a similar behavior to EF6 by creating a `Log` property on your `DbContext` and then using it only when it has been set.</span></span> <span data-ttu-id="80fc5-247">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="80fc5-247">For example:</span></span>

<!--
    public Action<string> Log { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(s => Log?.Invoke(s));
-->
[!code-csharp[DatabaseLog](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=DatabaseLog)]
