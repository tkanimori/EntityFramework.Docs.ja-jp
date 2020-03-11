---
title: DbContext の構成-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 3ab90d46b7a4476044e5ea38eaf04f995708e7bf
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414159"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="6be24-102">DbContext の構成</span><span class="sxs-lookup"><span data-stu-id="6be24-102">Configuring a DbContext</span></span>

<span data-ttu-id="6be24-103">この記事では、`DbContextOptions` 経由で `DbContext` を構成して、特定の EF Core プロバイダーとオプションの動作を使用してデータベースに接続するための基本的なパターンについて説明します。</span><span class="sxs-lookup"><span data-stu-id="6be24-103">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="6be24-104">デザイン時の DbContext 構成</span><span class="sxs-lookup"><span data-stu-id="6be24-104">Design-time DbContext configuration</span></span>

<span data-ttu-id="6be24-105">[移行](xref:core/managing-schemas/migrations/index)などのデザイン時ツールでは、アプリケーションのエンティティ型に関する詳細情報を収集し、データベーススキーマにマップする方法について、`DbContext` の種類の作業インスタンスを検出して作成できる必要があり EF Core。</span><span class="sxs-lookup"><span data-stu-id="6be24-105">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="6be24-106">このプロセスは、実行時に構成するのと同じように構成されるように、ツールが簡単に `DbContext` を作成できる限り、自動的に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="6be24-106">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="6be24-107">`DbContext` に必要な構成情報を提供するパターンは実行時に動作しますが、デザイン時に `DbContext` を使用する必要があるツールは、限られた数のパターンでのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="6be24-107">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="6be24-108">これらの詳細については、「[デザイン時コンテキストの作成](xref:core/miscellaneous/cli/dbcontext-creation)」セクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="6be24-108">These are covered in more detail in the [Design-Time Context Creation](xref:core/miscellaneous/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="6be24-109">DbContextOptions の構成</span><span class="sxs-lookup"><span data-stu-id="6be24-109">Configuring DbContextOptions</span></span>

<span data-ttu-id="6be24-110">`DbContext` には、すべての作業を実行するために `DbContextOptions` のインスタンスが必要です。</span><span class="sxs-lookup"><span data-stu-id="6be24-110">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="6be24-111">`DbContextOptions` インスタンスは、次のような構成情報を保持します。</span><span class="sxs-lookup"><span data-stu-id="6be24-111">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="6be24-112">使用するデータベースプロバイダー。通常は、`UseSqlServer` や `UseSqlite`などのメソッドを呼び出すことによって選択されます。</span><span class="sxs-lookup"><span data-stu-id="6be24-112">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="6be24-113">これらの拡張メソッドには、`Microsoft.EntityFrameworkCore.SqlServer` や `Microsoft.EntityFrameworkCore.Sqlite`などの、対応するプロバイダーパッケージが必要です。</span><span class="sxs-lookup"><span data-stu-id="6be24-113">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="6be24-114">メソッドは `Microsoft.EntityFrameworkCore` 名前空間で定義されています。</span><span class="sxs-lookup"><span data-stu-id="6be24-114">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="6be24-115">データベースインスタンスの必要な接続文字列または識別子。通常は、上で説明したプロバイダー選択メソッドに引数として渡されます。</span><span class="sxs-lookup"><span data-stu-id="6be24-115">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="6be24-116">プロバイダーレベルの任意の動作セレクター。通常は、プロバイダーの選択メソッドの呼び出しの内側にもチェーンされます。</span><span class="sxs-lookup"><span data-stu-id="6be24-116">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="6be24-117">一般的な EF Core 動作セレクター。通常は、プロバイダーセレクターメソッドの後または前にチェーンされます。</span><span class="sxs-lookup"><span data-stu-id="6be24-117">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="6be24-118">次の例では、SQL Server プロバイダー、`connectionString` 変数に含まれる接続、プロバイダーレベルのコマンドタイムアウト、および既定ですべてのクエリを `DbContext`[なし](xref:core/querying/tracking#no-tracking-queries)で実行するための EF Core 動作セレクターを使用するように `DbContextOptions` を構成します。</span><span class="sxs-lookup"><span data-stu-id="6be24-118">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> <span data-ttu-id="6be24-119">前に説明したプロバイダーセレクターメソッドとその他の動作セレクターメソッドは、`DbContextOptions` またはプロバイダー固有のオプションクラスの拡張メソッドです。</span><span class="sxs-lookup"><span data-stu-id="6be24-119">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="6be24-120">これらの拡張メソッドにアクセスできるようにするには、スコープ内に名前空間 (通常は `Microsoft.EntityFrameworkCore`) が必要であり、プロジェクトにパッケージの依存関係が追加されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="6be24-120">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="6be24-121">`DbContextOptions` は、`OnConfiguring` メソッドをオーバーライドするか、コンストラクター引数を使用して外部で `DbContext` に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="6be24-121">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="6be24-122">両方を使用すると `OnConfiguring` が最後に適用され、コンストラクター引数に指定されたオプションを上書きできます。</span><span class="sxs-lookup"><span data-stu-id="6be24-122">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="6be24-123">コンストラクター引数</span><span class="sxs-lookup"><span data-stu-id="6be24-123">Constructor argument</span></span>

<span data-ttu-id="6be24-124">コンストラクターは、次のように単に `DbContextOptions` を受け入れることができます。</span><span class="sxs-lookup"><span data-stu-id="6be24-124">Your constructor can simply accept a `DbContextOptions` as follows:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]  
> <span data-ttu-id="6be24-125">DbContext の基本コンストラクターは、非ジェネリックバージョンの `DbContextOptions`も受け入れますが、複数のコンテキストタイプを持つアプリケーションでは、非ジェネリックバージョンを使用することは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="6be24-125">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="6be24-126">これで、アプリケーションは、コンテキストをインスタンス化するときに、次のように `DbContextOptions` を渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="6be24-126">Your application can now pass the `DbContextOptions` when instantiating a context, as follows:</span></span>

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="6be24-127">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="6be24-127">OnConfiguring</span></span>

<span data-ttu-id="6be24-128">コンテキスト自体で `DbContextOptions` を初期化することもできます。</span><span class="sxs-lookup"><span data-stu-id="6be24-128">You can also initialize the `DbContextOptions` within the context itself.</span></span> <span data-ttu-id="6be24-129">この手法は基本的な構成に使用できますが、通常は、データベース接続文字列など、外部から特定の構成の詳細を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6be24-129">While you can use this technique for basic configuration, you will typically still need to get certain configuration details from the outside, e.g. a database connection string.</span></span> <span data-ttu-id="6be24-130">これは、構成 API またはその他の方法で行うことができます。</span><span class="sxs-lookup"><span data-stu-id="6be24-130">This can be done with a configuration API or any other means.</span></span>

<span data-ttu-id="6be24-131">コンテキスト内で `DbContextOptions` を初期化するには、`OnConfiguring` メソッドをオーバーライドし、指定された `DbContextOptionsBuilder`に対してメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="6be24-131">To initialize `DbContextOptions` within the context, override the `OnConfiguring` method and call the methods on the provided `DbContextOptionsBuilder`:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

<span data-ttu-id="6be24-132">アプリケーションは、コンストラクターに何も渡さずにこのようなコンテキストをインスタンス化できます。</span><span class="sxs-lookup"><span data-stu-id="6be24-132">An application can simply instantiate such a context without passing anything to its constructor:</span></span>

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="6be24-133">この方法は、テストが完全なデータベースを対象としている場合を除き、テストには適していません。</span><span class="sxs-lookup"><span data-stu-id="6be24-133">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="6be24-134">依存関係の挿入での DbContext の使用</span><span class="sxs-lookup"><span data-stu-id="6be24-134">Using DbContext with dependency injection</span></span>

<span data-ttu-id="6be24-135">EF Core は、依存関係挿入コンテナーでの `DbContext` の使用をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="6be24-135">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="6be24-136">DbContext 型は、`AddDbContext<TContext>` メソッドを使用してサービスコンテナーに追加できます。</span><span class="sxs-lookup"><span data-stu-id="6be24-136">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="6be24-137">`AddDbContext<TContext>` によって、DbContext 型、`TContext`、および対応する `DbContextOptions<TContext>` の両方がサービスコンテナーからの挿入に使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="6be24-137">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="6be24-138">依存関係の挿入の詳細については[、以下を](#more-reading)参照してください。</span><span class="sxs-lookup"><span data-stu-id="6be24-138">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="6be24-139">依存関係の挿入に `DbContext` を追加します。</span><span class="sxs-lookup"><span data-stu-id="6be24-139">Adding the `DbContext` to dependency injection:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="6be24-140">そのためには、`DbContextOptions<TContext>`を受け入れる[コンストラクター引数](#constructor-argument)を dbcontext 型に追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="6be24-140">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="6be24-141">コンテキストコード:</span><span class="sxs-lookup"><span data-stu-id="6be24-141">Context code:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="6be24-142">アプリケーションコード (ASP.NET Core):</span><span class="sxs-lookup"><span data-stu-id="6be24-142">Application code (in ASP.NET Core):</span></span>

``` csharp
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

<span data-ttu-id="6be24-143">アプリケーションコード (ServiceProvider を直接、あまり一般的ではない):</span><span class="sxs-lookup"><span data-stu-id="6be24-143">Application code (using ServiceProvider directly, less common):</span></span>

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="6be24-144">DbContext スレッドの問題の回避</span><span class="sxs-lookup"><span data-stu-id="6be24-144">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="6be24-145">Entity Framework Core では、同じ `DbContext` インスタンスで実行されている複数の並列操作はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="6be24-145">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="6be24-146">これには、非同期クエリの並列実行と、複数のスレッドからの明示的な同時使用の両方が含まれます。</span><span class="sxs-lookup"><span data-stu-id="6be24-146">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="6be24-147">したがって、常に非同期呼び出しを直ちに `await` するか、並列で実行される操作に個別の `DbContext` インスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="6be24-147">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="6be24-148">EF Core が `DbContext` インスタンスを同時に使用しようとしたことを検出すると、次のようなメッセージが `InvalidOperationException` 表示されます。</span><span class="sxs-lookup"><span data-stu-id="6be24-148">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="6be24-149">前の操作が完了する前に、このコンテキストで2番目の操作が開始されました。</span><span class="sxs-lookup"><span data-stu-id="6be24-149">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="6be24-150">これは通常、同じ DbContext のインスタンスを使用する異なるスレッドによって発生しますが、インスタンスメンバーはスレッドセーフであるとは限りません。</span><span class="sxs-lookup"><span data-stu-id="6be24-150">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="6be24-151">同時アクセスが検出されない場合は、未定義の動作、アプリケーションのクラッシュ、データの破損が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6be24-151">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="6be24-152">同じ `DbContext` インスタンスに対する同時アクセスが誤って発生する可能性がある一般的な誤りがあります。</span><span class="sxs-lookup"><span data-stu-id="6be24-152">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="6be24-153">同じ DbContext で他の操作を開始する前に、非同期操作の完了を待機していません</span><span class="sxs-lookup"><span data-stu-id="6be24-153">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="6be24-154">非同期メソッドを使用すると、ブロックしない方法でデータベースにアクセスする操作を開始 EF Core ことができます。</span><span class="sxs-lookup"><span data-stu-id="6be24-154">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="6be24-155">ただし、呼び出し元がこれらのメソッドのいずれかの完了を待機せず、`DbContext`に対して他の操作を実行しようとした場合、`DbContext` の状態は (おそらく) 破損している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6be24-155">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="6be24-156">常に非同期メソッド EF Core 待機します。</span><span class="sxs-lookup"><span data-stu-id="6be24-156">Always await EF Core asynchronous methods immediately.</span></span>  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="6be24-157">依存関係の挿入によって複数のスレッド間で DbContext インスタンスを暗黙的に共有する</span><span class="sxs-lookup"><span data-stu-id="6be24-157">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="6be24-158">[`AddDbContext`](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)拡張メソッドは、既定でスコープを持つ[有効期間](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)の `DbContext` 型を登録します。</span><span class="sxs-lookup"><span data-stu-id="6be24-158">The [`AddDbContext`](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="6be24-159">これは、特定の時間に各クライアント要求を実行するスレッドが1つだけであるため、ASP.NET Core アプリケーションの同時アクセスの問題から安全です。また、各要求は個別の依存関係挿入スコープ (したがって、別の `DbContext` インスタンス) を取得するためです。</span><span class="sxs-lookup"><span data-stu-id="6be24-159">This is safe from concurrent access issues in ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span>

<span data-ttu-id="6be24-160">ただし、複数のスレッドを明示的に並列実行するコードでは、`DbContext` インスタンスに同時にアクセスしないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="6be24-160">However any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="6be24-161">依存関係の挿入を使用すると、コンテキストをスコープとして登録し、各スレッドに対して (`IServiceScopeFactory`を使用して) スコープを作成するか、`DbContext` を一時的に登録する (`ServiceLifetime` パラメーターを受け取る `AddDbContext` のオーバーロードを使用) ことによって実現できます。</span><span class="sxs-lookup"><span data-stu-id="6be24-161">Using dependency injection, this can be achieved by either registering the context as scoped and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="6be24-162">その他の参考資料</span><span class="sxs-lookup"><span data-stu-id="6be24-162">More reading</span></span>

- <span data-ttu-id="6be24-163">DI の使用方法の詳細については、「[依存関係の挿入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6be24-163">Read [Dependency Injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="6be24-164">詳細については、「[テスト](testing/index.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="6be24-164">Read [Testing](testing/index.md) for more information.</span></span>
