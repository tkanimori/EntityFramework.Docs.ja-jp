---
title: DbContext の構成 - EF Core
description: Entity Framework Core を使用して DbContext を構成するための戦略
author: ajcvickers
ms.date: 10/27/2016
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 179cb1e4ff7a433c13677ec3f1e457de96004489
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431132"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="b90b6-103">DbContext の構成</span><span class="sxs-lookup"><span data-stu-id="b90b6-103">Configuring a DbContext</span></span>

<span data-ttu-id="b90b6-104">この記事では、特定の EF Core プロバイダーとオプションの動作を使用してデータベースに接続するために、`DbContextOptions` を使用して `DbContext` を構成するための基本パターンについて説明します。</span><span class="sxs-lookup"><span data-stu-id="b90b6-104">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="b90b6-105">デザイン時の DbContext の構成</span><span class="sxs-lookup"><span data-stu-id="b90b6-105">Design-time DbContext configuration</span></span>

<span data-ttu-id="b90b6-106">[移行](xref:core/managing-schemas/migrations/index)などの EF Core デザイン時ツールでは、アプリケーションのエンティティ型とそれらがデータベース スキーマにどのようにマップされるかに関する詳細情報を収集するため、`DbContext` 型の作業インスタンスを検出および作成できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="b90b6-106">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="b90b6-107">このプロセスは、実行時に構成される場合と同様の方法で構成されるように `DbContext` をツールで簡単に作成できる限り、自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-107">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="b90b6-108">`DbContext` に必要な構成情報を提供するパターンは実行時に動作しますが、デザイン時に `DbContext` を使用する必要があるツールは、限られた数のパターンでのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="b90b6-108">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="b90b6-109">これらについては、[「Design-Time Context Creation」(デザイン時のコンテキストの作成)](xref:core/cli/dbcontext-creation) で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="b90b6-109">These are covered in more detail in the [Design-Time Context Creation](xref:core/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="b90b6-110">DbContextOptions の構成</span><span class="sxs-lookup"><span data-stu-id="b90b6-110">Configuring DbContextOptions</span></span>

<span data-ttu-id="b90b6-111">`DbContext` で何らかの作業を実行するには、`DbContextOptions` のインスタンスが必要です。</span><span class="sxs-lookup"><span data-stu-id="b90b6-111">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="b90b6-112">`DbContextOptions` インスタンスには、次のような構成情報が格納されます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-112">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="b90b6-113">使用するデータベース プロバイダー。通常は、`UseSqlServer` や `UseSqlite` などのメソッドを呼び出すことで選択されます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-113">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="b90b6-114">これらの拡張メソッドには、`Microsoft.EntityFrameworkCore.SqlServer` や `Microsoft.EntityFrameworkCore.Sqlite` などの対応するプロバイダー パッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="b90b6-114">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="b90b6-115">これらのメソッドは `Microsoft.EntityFrameworkCore` 名前空間で定義されています。</span><span class="sxs-lookup"><span data-stu-id="b90b6-115">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="b90b6-116">データベース インスタンスの必要な接続文字列または識別子。通常は、上記のプロバイダー選択メソッドに引数として渡されます</span><span class="sxs-lookup"><span data-stu-id="b90b6-116">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="b90b6-117">プロバイダー レベルのオプションの動作セレクター。通常は、プロバイダー選択メソッドの呼び出しの内部にもチェーンされます</span><span class="sxs-lookup"><span data-stu-id="b90b6-117">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="b90b6-118">一般的な EF Core の動作セレクター。通常は、プロバイダー セレクター メソッドの呼び出しの前または後にチェーンされます</span><span class="sxs-lookup"><span data-stu-id="b90b6-118">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="b90b6-119">次の例では、SQL Server プロバイダー、`connectionString` 変数に含まれる接続、プロバイダーレベルのコマンド タイムアウト、および `DbContext` で実行されるすべてのクエリを既定で[追跡なし](xref:core/querying/tracking#no-tracking-queries)にする EF Core の動作セレクターを使用するように、`DbContextOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="b90b6-119">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

```csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]
> <span data-ttu-id="b90b6-120">上記のプロバイダー セレクター メソッドとその他の動作セレクター メソッドは、`DbContextOptions` またはプロバイダー固有のオプション クラスの拡張メソッドです。</span><span class="sxs-lookup"><span data-stu-id="b90b6-120">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="b90b6-121">これらの拡張メソッドにアクセスできるようにするには、スコープ内に名前空間 (通常は `Microsoft.EntityFrameworkCore`) があり、プロジェクトに追加のパッケージの依存関係が含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="b90b6-121">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="b90b6-122">`DbContextOptions` を `DbContext` に渡すには、`OnConfiguring` メソッドをオーバーライドするか、外部でコンストラクター引数を使用します。</span><span class="sxs-lookup"><span data-stu-id="b90b6-122">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="b90b6-123">両方を使用すると、`OnConfiguring` の方が後で適用され、コンストラクター引数に渡されたオプションをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-123">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="b90b6-124">コンストラクター引数</span><span class="sxs-lookup"><span data-stu-id="b90b6-124">Constructor argument</span></span>

<span data-ttu-id="b90b6-125">コンストラクターは、次のように `DbContextOptions` をそのまま受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-125">Your constructor can simply accept a `DbContextOptions` as follows:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]
> <span data-ttu-id="b90b6-126">DbContext の基本コンストラクターは非ジェネリック バージョンの `DbContextOptions` も受け入れますが、複数のコンテキスト型を持つアプリケーションで非ジェネリック バージョンを使用することはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="b90b6-126">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="b90b6-127">これで、アプリケーションはコンテキストをインスタンス化するときに、次のように `DbContextOptions` を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-127">Your application can now pass the `DbContextOptions` when instantiating a context, as follows:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="b90b6-128">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="b90b6-128">OnConfiguring</span></span>

<span data-ttu-id="b90b6-129">コンテキスト自体の内部で `DbContextOptions` を初期化することもできます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-129">You can also initialize the `DbContextOptions` within the context itself.</span></span> <span data-ttu-id="b90b6-130">この手法は基本的な構成で使用できますが、その場合でも、通常は外部から特定の構成の詳細 (データベース接続文字列など) を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b90b6-130">While you can use this technique for basic configuration, you will typically still need to get certain configuration details from the outside, e.g. a database connection string.</span></span> <span data-ttu-id="b90b6-131">これは、構成 API またはその他の手段を使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-131">This can be done with a configuration API or any other means.</span></span>

<span data-ttu-id="b90b6-132">コンテキスト内で `DbContextOptions` を初期化するには、`OnConfiguring` メソッドをオーバーライドし、指定された `DbContextOptionsBuilder` のメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="b90b6-132">To initialize `DbContextOptions` within the context, override the `OnConfiguring` method and call the methods on the provided `DbContextOptionsBuilder`:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

<span data-ttu-id="b90b6-133">アプリケーションは、コンストラクターに何も渡さずにこのようなコンテキストをインスタンス化できます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-133">An application can simply instantiate such a context without passing anything to its constructor:</span></span>

```csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="b90b6-134">この方法は、テストが完全なデータベースを対象としている場合を除き、テストには適していません。</span><span class="sxs-lookup"><span data-stu-id="b90b6-134">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="b90b6-135">依存関係の挿入での DbContext の使用</span><span class="sxs-lookup"><span data-stu-id="b90b6-135">Using DbContext with dependency injection</span></span>

<span data-ttu-id="b90b6-136">EF Core では、依存関係挿入コンテナーでの `DbContext` の使用がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="b90b6-136">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="b90b6-137">DbContext 型をサービス コンテナーに追加するには、`AddDbContext<TContext>` を使用します。</span><span class="sxs-lookup"><span data-stu-id="b90b6-137">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="b90b6-138">`AddDbContext<TContext>` を使用すると、DbContext 型 `TContext` と対応する `DbContextOptions<TContext>` の両方をサービス コンテナーからの挿入に使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="b90b6-138">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="b90b6-139">依存関係の挿入の詳細については、下記の「[関連項目](#more-reading)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="b90b6-139">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="b90b6-140">`DbContext` を依存関係の挿入に追加します。</span><span class="sxs-lookup"><span data-stu-id="b90b6-140">Adding the `DbContext` to dependency injection:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="b90b6-141">そのためには、`DbContextOptions<TContext>` を受け入れる DbContext 型に[コンストラクター引数](#constructor-argument)を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b90b6-141">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="b90b6-142">コード コンテキスト:</span><span class="sxs-lookup"><span data-stu-id="b90b6-142">Context code:</span></span>

```csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="b90b6-143">アプリケーション コード (ASP.NET Core 内):</span><span class="sxs-lookup"><span data-stu-id="b90b6-143">Application code (in ASP.NET Core):</span></span>

```csharp
public class MyController
{
    private readonly BloggingContext _context;

    public MyController(BloggingContext context)
    {
      _context = context;
    }

    ...
}
```

<span data-ttu-id="b90b6-144">アプリケーション コード (ServiceProvider を直接使用、あまり一般的ではない):</span><span class="sxs-lookup"><span data-stu-id="b90b6-144">Application code (using ServiceProvider directly, less common):</span></span>

```csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="b90b6-145">DbContext のスレッド処理の問題を回避する</span><span class="sxs-lookup"><span data-stu-id="b90b6-145">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="b90b6-146">Entity Framework Core では、同じ `DbContext` インスタンス上での複数の並列操作の実行がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="b90b6-146">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="b90b6-147">これには、非同期クエリの並列実行と、複数のスレッドからの明示的な同時使用の両方が含まれます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-147">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="b90b6-148">そのため、常に非同期呼び出しを直ちに `await` するか、並列実行される操作に対して個別の `DbContext` インスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="b90b6-148">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="b90b6-149">`DbContext` インスタンスを同時に使用しようとしたことが EF Core によって検出されると、次のようなメッセージとともに `InvalidOperationException` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-149">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="b90b6-150">前の操作が完了する前に、このコンテキストで 2 つ目の操作が開始されました。</span><span class="sxs-lookup"><span data-stu-id="b90b6-150">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="b90b6-151">これは通常、異なるスレッドで DbContext の同じインスタンスが使用された場合に発生しますが、インスタンスのメンバーがスレッド セーフである保証はありません。</span><span class="sxs-lookup"><span data-stu-id="b90b6-151">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="b90b6-152">同時アクセスが検出されない場合は、未定義の動作、アプリケーションのクラッシュ、データの破損が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b90b6-152">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="b90b6-153">同じ `DbContext` インスタンスに誤って同時にアクセスする原因として、一般に次のような間違いがあります。</span><span class="sxs-lookup"><span data-stu-id="b90b6-153">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="b90b6-154">同じ DbContext で他の操作を開始する前に、非同期操作の完了を待機していない</span><span class="sxs-lookup"><span data-stu-id="b90b6-154">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="b90b6-155">EF Core で非同期メソッドを使用すると、非ブロッキング モードでデータベースにアクセスする操作を開始できます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-155">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="b90b6-156">しかし、呼び出し元がこれらのメソッドのいずれかの完了を待機せず、`DbContext` に対して他の操作を実行しようとした場合、`DbContext` の状態は (非常に高い確率で) 破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="b90b6-156">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="b90b6-157">常に EF Core の非同期メソッドを直ちに待機してください。</span><span class="sxs-lookup"><span data-stu-id="b90b6-157">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="b90b6-158">依存関係の挿入によって複数のスレッド間で DbContext インスタンスを暗黙的に共有する</span><span class="sxs-lookup"><span data-stu-id="b90b6-158">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="b90b6-159">[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 拡張メソッドを使用すると、[スコープ付きの有効期間](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)で `DbContext` 型が登録されます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-159">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="b90b6-160">この場合、ある時点で個々のクライアント要求を実行するスレッドは 1 つしかなく、各要求が別個の依存関係挿入スコープ (したがって、別個の `DbContext` インスタンス) を取得するため、ほとんどの ASP.NET Core アプリケーションでは同時アクセスの問題は発生しません。</span><span class="sxs-lookup"><span data-stu-id="b90b6-160">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="b90b6-161">Blazor Server ホスティング モデルでは、Blazor ユーザー回線を維持するために 1 つの論理要求が使用されるため、既定の挿入スコープが使用されている場合は、ユーザー回線ごとにスコープ付きの DbContext インスタンスを 1 つだけ使用できます。</span><span class="sxs-lookup"><span data-stu-id="b90b6-161">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="b90b6-162">複数のスレッドを明示的に並列実行するコードでは、`DbContext` インスタンスが同時にアクセスされないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="b90b6-162">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="b90b6-163">依存関係の挿入を使用してこれを実現するには、コンテキストをスコープ付きとして登録し、スレッドごとに (`IServiceScopeFactory` を使用して) スコープを作成するか、または (`ServiceLifetime` パラメーターを取る `AddDbContext` のオーバーロードを使用して) `DbContext` を一時的として登録します。</span><span class="sxs-lookup"><span data-stu-id="b90b6-163">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="b90b6-164">関連項目</span><span class="sxs-lookup"><span data-stu-id="b90b6-164">More reading</span></span>

- <span data-ttu-id="b90b6-165">DI の使用の詳細については、[依存関係の挿入](/aspnet/core/fundamentals/dependency-injection)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b90b6-165">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="b90b6-166">テストの詳細については、[テスト](xref:core/testing/index)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="b90b6-166">Read [Testing](xref:core/testing/index) for more information.</span></span>
