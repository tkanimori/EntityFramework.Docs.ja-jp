---
title: イベントカウンター-EF Core
description: .NET イベントカウンターを使用して EF Core パフォーマンスを追跡し、異常を診断する
author: roji
ms.date: 11/17/2020
uid: core/logging-events-diagnostics/event-counters
ms.openlocfilehash: 73d360b266db3d3252defbf4a4035c0eb430e22e
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635823"
---
# <a name="event-counters"></a><span data-ttu-id="661a9-103">イベントカウンター</span><span class="sxs-lookup"><span data-stu-id="661a9-103">Event Counters</span></span>

> [!NOTE]
> <span data-ttu-id="661a9-104">この機能は、EF Core 5.0 で追加されました。</span><span class="sxs-lookup"><span data-stu-id="661a9-104">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="661a9-105">Entity Framework Core (EF Core) は、継続的な数値メトリックを公開します。これにより、プログラムの正常性を正確に示すことができます。</span><span class="sxs-lookup"><span data-stu-id="661a9-105">Entity Framework Core (EF Core) exposes continuous numeric metrics which can provide a good indication of your program's health.</span></span> <span data-ttu-id="661a9-106">これらのメトリックは、次の目的で使用できます。</span><span class="sxs-lookup"><span data-stu-id="661a9-106">These metrics can be used for the following purposes:</span></span>

* <span data-ttu-id="661a9-107">アプリケーションの実行中に、データベースの一般的な負荷をリアルタイムで追跡する</span><span class="sxs-lookup"><span data-stu-id="661a9-107">Track general database load in realtime as the application is running</span></span>
* <span data-ttu-id="661a9-108">パフォーマンスの低下につながる可能性がある問題のあるコーディングプラクティスを公開する</span><span class="sxs-lookup"><span data-stu-id="661a9-108">Expose problematic coding practices which can lead to degraded performance</span></span>
* <span data-ttu-id="661a9-109">異常なプログラムの動作を追跡して特定する</span><span class="sxs-lookup"><span data-stu-id="661a9-109">Track down and isolate anomalous program behavior</span></span>

<span data-ttu-id="661a9-110">EF Core は、標準の .NET イベントカウンター機能を使用してメトリックをレポートします。カウンターの動作の概要については、 [このブログ投稿](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) をお読みになることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="661a9-110">EF Core reports metrics via the standard .NET event counters feature; it's recommended to read [this blog post](https://devblogs.microsoft.com/dotnet/introducing-diagnostics-improvements-in-net-core-3-0/) for a quick overview of how counters work.</span></span>

## <a name="attach-to-a-process-using-dotnet-counters"></a><span data-ttu-id="661a9-111">Dotnet を使用してプロセスにアタッチする</span><span class="sxs-lookup"><span data-stu-id="661a9-111">Attach to a process using dotnet-counters</span></span>

<span data-ttu-id="661a9-112">[Dotnet ツール](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters)を使用すると、実行中のプロセスにアタッチして EF Core イベントカウンターを定期的に報告できます。これらのカウンターを使用できるようにするために、プログラムで特別な操作を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="661a9-112">The [dotnet-counters tool](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) can be used to attach to a running process and report EF Core event counters regularly; nothing special needs to be done in the program for these counters to be available.</span></span>

<span data-ttu-id="661a9-113">まず、ツールをインストール `dotnet-counters` `dotnet tool install --global dotnet-counters` します。</span><span class="sxs-lookup"><span data-stu-id="661a9-113">First, install the `dotnet-counters` tool: `dotnet tool install --global dotnet-counters`.</span></span>

<span data-ttu-id="661a9-114">次に、EF Core アプリケーションを実行している .NET プロセスのプロセス ID (PID) を確認します。</span><span class="sxs-lookup"><span data-stu-id="661a9-114">Next, find the process ID (PID) of the .NET process running your EF Core application:</span></span>

### <a name="windows"></a>[<span data-ttu-id="661a9-115">Windows</span><span class="sxs-lookup"><span data-stu-id="661a9-115">Windows</span></span>](#tab/windows)

1. <span data-ttu-id="661a9-116">タスクバーを右クリックし、[タスクマネージャー] を選択して、Windows タスクマネージャーを開きます。</span><span class="sxs-lookup"><span data-stu-id="661a9-116">Open the Windows Task Manager by right-clicking on the task bar and selecting "Task Manager".</span></span>
2. <span data-ttu-id="661a9-117">ウィンドウの下部にある [詳細] オプションが選択されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="661a9-117">Make sure that the "More details" option is selected at the bottom of the window.</span></span>
3. <span data-ttu-id="661a9-118">[プロセス] タブで列を右クリックし、[PID] 列が有効になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="661a9-118">In the Processes tab, right-click a column and make sure that the PID column is enabled.</span></span>
4. <span data-ttu-id="661a9-119">[プロセス] の一覧でアプリケーションを見つけ、PID 列からプロセス ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="661a9-119">Locate your application in the process list, and get its process ID from the PID column.</span></span>

### <a name="linux-or-macos"></a>[<span data-ttu-id="661a9-120">Linux または macOS</span><span class="sxs-lookup"><span data-stu-id="661a9-120">Linux or macOS</span></span>](#tab/fluent-api)

1. <span data-ttu-id="661a9-121">コマンドを使用して、 `ps` ユーザーが実行しているすべてのプロセスを一覧表示します。</span><span class="sxs-lookup"><span data-stu-id="661a9-121">Use the `ps` command to list all processes running for your user.</span></span>
2. <span data-ttu-id="661a9-122">[プロセス] の一覧でアプリケーションを見つけ、PID 列からプロセス ID を取得します。</span><span class="sxs-lookup"><span data-stu-id="661a9-122">Locate your application in the process list, and get its process ID from the PID column.</span></span>

***

<span data-ttu-id="661a9-123">.NET アプリケーション内では、プロセス ID をとして使用できます `Process.GetCurrentProcess().Id` 。これは、起動時に PID を出力する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="661a9-123">Inside your .NET application, the process ID is available as `Process.GetCurrentProcess().Id`; this can be useful for printing the PID upon startup.</span></span>

<span data-ttu-id="661a9-124">最後に、次のように起動し `dotnet-counters` ます。</span><span class="sxs-lookup"><span data-stu-id="661a9-124">Finally, launch `dotnet-counters` as follows:</span></span>

```console
dotnet counters monitor Microsoft.EntityFrameworkCore -p <PID>
```

<span data-ttu-id="661a9-125">`dotnet-counters` 次に、実行中のプロセスにアタッチして、継続的なカウンターデータのレポートを開始します。</span><span class="sxs-lookup"><span data-stu-id="661a9-125">`dotnet-counters` will now attach to your running process and start reporting continuous counter data:</span></span>

```console
Press p to pause, r to resume, q to quit.
 Status: Running

[Microsoft.EntityFrameworkCore]
    Active DbContexts                                               1
    Execution Strategy Operation Failures (Count / 1 sec)           0
    Execution Strategy Operation Failures (Total)                   0
    Optimistic Concurrency Failures (Count / 1 sec)                 0
    Optimistic Concurrency Failures (Total)                         0
    Queries (Count / 1 sec)                                         1
    Queries (Total)                                               189
    Query Cache Hit Rate (%)                                      100
    SaveChanges (Count / 1 sec)                                     0
    SaveChanges (Total)                                             0
```

## <a name="counters-and-their-meaning"></a><span data-ttu-id="661a9-126">カウンターとその意味</span><span class="sxs-lookup"><span data-stu-id="661a9-126">Counters and their meaning</span></span>

<span data-ttu-id="661a9-127">カウンター名</span><span class="sxs-lookup"><span data-stu-id="661a9-127">Counter name</span></span>                          | <span data-ttu-id="661a9-128">説明</span><span class="sxs-lookup"><span data-stu-id="661a9-128">Description</span></span>
------------------------------------- | ----
<span data-ttu-id="661a9-129">アクティブな DbContexts</span><span class="sxs-lookup"><span data-stu-id="661a9-129">Active DbContexts</span></span>                     | <span data-ttu-id="661a9-130">現在アプリケーション内にある、アクティブな破棄解除済みの DbContext インスタンスの数。</span><span class="sxs-lookup"><span data-stu-id="661a9-130">The number of active, undisposed DbContext instances currently in your application.</span></span> <span data-ttu-id="661a9-131">この数が継続的に増加している場合は、DbContext インスタンスが正しく破棄されていないためにリークが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="661a9-131">If this number grows continuously, you may have a leak because DbContext instances aren't being properly disposed.</span></span> <span data-ttu-id="661a9-132">[コンテキストプーリング](xref:core/performance/advanced-performance-topics#dbcontext-pooling)が有効になっている場合、この数には、現在使用されていないプール済み dbcontext インスタンスが含まれていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="661a9-132">Note that if [context pooling](xref:core/performance/advanced-performance-topics#dbcontext-pooling) is enabled, this number includes pooled DbContext instances not currently in use.</span></span>
<span data-ttu-id="661a9-133">実行戦略操作の失敗</span><span class="sxs-lookup"><span data-stu-id="661a9-133">Execution Strategy Operation Failures</span></span> | <span data-ttu-id="661a9-134">データベース操作の実行に失敗した回数。</span><span class="sxs-lookup"><span data-stu-id="661a9-134">The number of times a database operation failed to execute.</span></span> <span data-ttu-id="661a9-135">再試行中の実行戦略が有効になっている場合は、同じ操作に対して複数回試行されるたびに個々のエラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="661a9-135">If a retrying execution strategy is enabled, this includes each individual failure within multiple attempts on the same operation.</span></span> <span data-ttu-id="661a9-136">これは、インフラストラクチャに関する一時的な問題を検出するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="661a9-136">This can be used to detect transient issues with your infrastructure.</span></span>
<span data-ttu-id="661a9-137">オプティミスティック同時実行制御エラー</span><span class="sxs-lookup"><span data-stu-id="661a9-137">Optimistic Concurrency Failures</span></span>       | <span data-ttu-id="661a9-138">`SaveChanges`オプティミスティック同時実行制御エラーのために失敗した回数。データストア内のデータは、コードの読み込み後に変更されています。</span><span class="sxs-lookup"><span data-stu-id="661a9-138">The number of times `SaveChanges` failed because of an optimistic concurrency error, because data in the data store was changed since your code loaded it.</span></span> <span data-ttu-id="661a9-139">これは、 <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> スローされるに対応します。</span><span class="sxs-lookup"><span data-stu-id="661a9-139">This corresponds to a <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> being thrown.</span></span>
<span data-ttu-id="661a9-140">クエリ</span><span class="sxs-lookup"><span data-stu-id="661a9-140">Queries</span></span>                               | <span data-ttu-id="661a9-141">実行されたクエリの数。</span><span class="sxs-lookup"><span data-stu-id="661a9-141">The number of queries executed.</span></span>
<span data-ttu-id="661a9-142">クエリキャッシュヒット率 (%)</span><span class="sxs-lookup"><span data-stu-id="661a9-142">Query Cache Hit Rate (%)</span></span>              | <span data-ttu-id="661a9-143">クエリキャッシュヒットが失敗した割合。</span><span class="sxs-lookup"><span data-stu-id="661a9-143">The ratio of query cache hits to misses.</span></span> <span data-ttu-id="661a9-144">指定された LINQ クエリが EF Core (パラメーターを除く) によって初めて実行された場合は、比較的負荷の大きいプロセスでコンパイルする必要があります。</span><span class="sxs-lookup"><span data-stu-id="661a9-144">The first time a given LINQ query is executed by EF Core (excluding parameters), it must be compiled in what is a relatively heavy process.</span></span> <span data-ttu-id="661a9-145">通常のアプリケーションでは、すべてのクエリが再利用されます。また、クエリキャッシュヒット率は、初期ウォームアップ期間後の100% で安定している必要があります。</span><span class="sxs-lookup"><span data-stu-id="661a9-145">In a normal application, all queries are reused, and the query cache hit rate should be stable at 100% after an initial warmup period.</span></span> <span data-ttu-id="661a9-146">この数値が時間の経過と共に100% 未満の場合は、コンパイルが繰り返されるため、パフォーマンスが低下する可能性があります。これは、動的なクエリ生成が最適ではない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="661a9-146">If this number is less than 100% over time, you may experience degraded perf due to repeated compilations, which could be a result of suboptimal dynamic query generation.</span></span>
<span data-ttu-id="661a9-147">SaveChanges</span><span class="sxs-lookup"><span data-stu-id="661a9-147">SaveChanges</span></span>                           | <span data-ttu-id="661a9-148">`SaveChanges`が呼び出された回数。</span><span class="sxs-lookup"><span data-stu-id="661a9-148">The number of times `SaveChanges` has been called.</span></span> <span data-ttu-id="661a9-149">では `SaveChanges` 複数の変更が1つのバッチに保存されるため、これは必ずしも1つのエンティティに対して行われた個々の更新を表すとは限りません。</span><span class="sxs-lookup"><span data-stu-id="661a9-149">Note that `SaveChanges` saves multiple changes in a single batch, so this doesn't necessarily represent each individual update done on a single entity.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="661a9-150">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="661a9-150">Additional resources</span></span>

* [<span data-ttu-id="661a9-151">イベントカウンターに関する .NET ドキュメント</span><span class="sxs-lookup"><span data-stu-id="661a9-151">.NET documentation on event counters</span></span>](https://docs.microsoft.com/dotnet/core/diagnostics/event-counters)
