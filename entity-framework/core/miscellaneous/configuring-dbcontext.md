---
title: EF Core の DbContext を構成します。
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 0350b25d0d0efe05df7cb9e93a3f4ae2d864fd63
ms.sourcegitcommit: 5280dcac4423acad8b440143433459b18886115b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59363938"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="82cee-102">DbContext の構成</span><span class="sxs-lookup"><span data-stu-id="82cee-102">Configuring a DbContext</span></span>

<span data-ttu-id="82cee-103">この記事では構成するための基本的なパターンを`DbContext`を使用して、`DbContextOptions`特定の EF Core プロバイダーと省略可能な動作を使用してデータベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="82cee-103">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="82cee-104">デザイン時 DbContext の構成</span><span class="sxs-lookup"><span data-stu-id="82cee-104">Design-time DbContext configuration</span></span>

<span data-ttu-id="82cee-105">EF Core のデザイン時ツール[移行](xref:core/managing-schemas/migrations/index)を検出し、作業のインスタンスを作成できる必要があります、`DbContext`型アプリケーションのエンティティ型とデータベース スキーマへの割り当て方法に関する詳細情報を収集するためにします。</span><span class="sxs-lookup"><span data-stu-id="82cee-105">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="82cee-106">このプロセスは、ツールを簡単に作成できる限り、自動で、`DbContext`ことは構成する必要が同様に実行時の構成方法とするようにします。</span><span class="sxs-lookup"><span data-stu-id="82cee-106">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="82cee-107">必要な構成情報を提供する任意のパターンの中に、`DbContext`ランタイムを使用する必要とするツールで作業ができ、`DbContext`デザイン時にのみ使用できますパターンの数に制限します。</span><span class="sxs-lookup"><span data-stu-id="82cee-107">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="82cee-108">これらについては説明について詳しく、[デザイン時のコンテキストの作成](xref:core/miscellaneous/cli/dbcontext-creation)セクション。</span><span class="sxs-lookup"><span data-stu-id="82cee-108">These are covered in more detail in the [Design-Time Context Creation](xref:core/miscellaneous/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="82cee-109">DbContextOptions を構成します。</span><span class="sxs-lookup"><span data-stu-id="82cee-109">Configuring DbContextOptions</span></span>

<span data-ttu-id="82cee-110">`DbContext` インスタンスが必要`DbContextOptions`作業を実行するためにします。</span><span class="sxs-lookup"><span data-stu-id="82cee-110">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="82cee-111">`DbContextOptions`インスタンスなどの構成情報を実行します。</span><span class="sxs-lookup"><span data-stu-id="82cee-111">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="82cee-112">データベース プロバイダーを使用するには、通常などのメソッドを呼び出すことによって選択`UseSqlServer`または`UseSqlite`します。</span><span class="sxs-lookup"><span data-stu-id="82cee-112">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`.</span></span> <span data-ttu-id="82cee-113">これらの拡張メソッドは、対応するプロバイダーのパッケージをなど必要`Microsoft.EntityFrameworkCore.SqlServer`または`Microsoft.EntityFrameworkCore.Sqlite`します。</span><span class="sxs-lookup"><span data-stu-id="82cee-113">These extension methods require the corresponding provider package, such as `Microsoft.EntityFrameworkCore.SqlServer` or `Microsoft.EntityFrameworkCore.Sqlite`.</span></span> <span data-ttu-id="82cee-114">メソッドが定義されている、`Microsoft.EntityFrameworkCore`名前空間。</span><span class="sxs-lookup"><span data-stu-id="82cee-114">The methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span>
- <span data-ttu-id="82cee-115">任意の必要な接続文字列またはデータベースのインスタンスの識別子通常に渡される引数として上記のように、プロバイダーの選択メソッド</span><span class="sxs-lookup"><span data-stu-id="82cee-115">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="82cee-116">通常、プロバイダーの選択メソッドの呼び出しの内部でチェーンも、任意のプロバイダ レベルの省略可能な動作セレクター</span><span class="sxs-lookup"><span data-stu-id="82cee-116">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="82cee-117">通常チェーン プロバイダー セレクター メソッドは前に、または後に、EF Core の一般的な動作セレクターで、</span><span class="sxs-lookup"><span data-stu-id="82cee-117">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="82cee-118">次の例では、`DbContextOptions`に SQL Server プロバイダーを使用する接続が含まれている、`connectionString`変数や、プロバイダー レベルのコマンド タイムアウトで実行されるすべてのクエリを EF Core の動作のセレクター、 `DbContext`[追跡なし](xref:core/querying/tracking#no-tracking-queries)既定。</span><span class="sxs-lookup"><span data-stu-id="82cee-118">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> <span data-ttu-id="82cee-119">プロバイダー セレクター メソッドと上記で説明した他の動作のセレクター メソッドは、拡張メソッドが上`DbContextOptions`またはプロバイダー固有のオプション クラス。</span><span class="sxs-lookup"><span data-stu-id="82cee-119">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="82cee-120">これらの拡張メソッドが、名前空間が存在する必要がありますにアクセスするために (通常`Microsoft.EntityFrameworkCore`) のスコープを設定して、プロジェクトに追加のパッケージの依存関係を含めます。</span><span class="sxs-lookup"><span data-stu-id="82cee-120">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="82cee-121">`DbContextOptions`を指定すると、`DbContext`オーバーライドすることで、`OnConfiguring`メソッドまたはコンス トラクター引数経由で外部から。</span><span class="sxs-lookup"><span data-stu-id="82cee-121">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="82cee-122">両方を使用する場合`OnConfiguring`が最後に適用され、コンス トラクターの引数に指定したオプションを上書きすることができます。</span><span class="sxs-lookup"><span data-stu-id="82cee-122">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="82cee-123">コンス トラクターの引数</span><span class="sxs-lookup"><span data-stu-id="82cee-123">Constructor argument</span></span>

<span data-ttu-id="82cee-124">コンス トラクターを持つコンテキスト コード:</span><span class="sxs-lookup"><span data-stu-id="82cee-124">Context code with constructor:</span></span>

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
> <span data-ttu-id="82cee-125">DbContext の基本コンス トラクターは、非ジェネリック バージョンのも受け入れる`DbContextOptions`が非ジェネリック バージョンを使用しては複数のコンテキストの型を持つアプリケーションにはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="82cee-125">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="82cee-126">コンス トラクターの引数から初期化するためにアプリケーション コード:</span><span class="sxs-lookup"><span data-stu-id="82cee-126">Application code to initialize from constructor argument:</span></span>

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="82cee-127">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="82cee-127">OnConfiguring</span></span>

<span data-ttu-id="82cee-128">コンテキスト コード`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="82cee-128">Context code with `OnConfiguring`:</span></span>

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

<span data-ttu-id="82cee-129">初期化するためにアプリケーション コード、`DbContext`を使用して`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="82cee-129">Application code to initialize a `DbContext` that uses `OnConfiguring`:</span></span>

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="82cee-130">この方法は適していませんテスト、テスト対象のデータベースの完全な場合を除き、します。</span><span class="sxs-lookup"><span data-stu-id="82cee-130">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="82cee-131">DbContext を使用して、依存関係の挿入</span><span class="sxs-lookup"><span data-stu-id="82cee-131">Using DbContext with dependency injection</span></span>

<span data-ttu-id="82cee-132">EF Core のサポートを使用して`DbContext`依存関係の注入コンテナーを使用します。</span><span class="sxs-lookup"><span data-stu-id="82cee-132">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="82cee-133">使用して、サービス コンテナーに DbContext 型を追加することができます、`AddDbContext<TContext>`メソッド。</span><span class="sxs-lookup"><span data-stu-id="82cee-133">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="82cee-134">`AddDbContext<TContext>` 両方の DbContext 型と、`TContext`と、対応する`DbContextOptions<TContext>`サービス コンテナーからのインジェクション用に使用できます。</span><span class="sxs-lookup"><span data-stu-id="82cee-134">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="82cee-135">参照してください[参照](#more-reading)以下の依存関係の挿入の詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="82cee-135">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="82cee-136">追加、`Dbcontext`依存関係の挿入。</span><span class="sxs-lookup"><span data-stu-id="82cee-136">Adding the `Dbcontext` to dependency injection:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="82cee-137">追加する必要があります、[コンス トラクター引数](#constructor-argument)を受け取る DbContext 型を`DbContextOptions<TContext>`します。</span><span class="sxs-lookup"><span data-stu-id="82cee-137">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="82cee-138">コンテキスト コード:</span><span class="sxs-lookup"><span data-stu-id="82cee-138">Context code:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="82cee-139">アプリケーション コード (ASP.NET Core):</span><span class="sxs-lookup"><span data-stu-id="82cee-139">Application code (in ASP.NET Core):</span></span>

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

<span data-ttu-id="82cee-140">アプリケーションのコード (サービス プロバイダーを直接使用、あまり一般的):</span><span class="sxs-lookup"><span data-stu-id="82cee-140">Application code (using ServiceProvider directly, less common):</span></span>

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```
## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="82cee-141">DbContext のスレッド処理の問題を回避します。</span><span class="sxs-lookup"><span data-stu-id="82cee-141">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="82cee-142">Entity Framework Core は、同じ実行されている複数の並列操作をサポートしていません`DbContext`インスタンス。</span><span class="sxs-lookup"><span data-stu-id="82cee-142">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="82cee-143">未定義の動作、アプリケーションのクラッシュおよびデータの破損の可能性への同時アクセスがあります。</span><span class="sxs-lookup"><span data-stu-id="82cee-143">Concurrent access can result in undefined behavior, application crashes and data corruption.</span></span> <span data-ttu-id="82cee-144">このを常に使用することが重要ため分離`DbContext`並列実行される操作のインスタンス。</span><span class="sxs-lookup"><span data-stu-id="82cee-144">Because of this it's important to always use separate `DbContext` instances for operations that execute in parallel.</span></span> 

<span data-ttu-id="82cee-145">Inadvernetly 原因の同時アクセスは、同じことが一般的な誤りがある`DbContext`インスタンス。</span><span class="sxs-lookup"><span data-stu-id="82cee-145">There are common mistakes that can inadvernetly cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="forgetting-to-await-the-completion-of-an-asynchronous-operation-before-starting-any-other-operation-on-the-same-dbcontext"></a><span data-ttu-id="82cee-146">同じ DbContext で他の操作を開始する前に非同期操作の完了を待機することを忘れる</span><span class="sxs-lookup"><span data-stu-id="82cee-146">Forgetting to await the completion of an asynchronous operation before starting any other operation on the same DbContext</span></span>

<span data-ttu-id="82cee-147">非同期メソッドには、非ブロッキング方式で、データベースにアクセスする操作を開始する EF Core が有効にします。</span><span class="sxs-lookup"><span data-stu-id="82cee-147">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="82cee-148">場合は、呼び出し元が、これらのメソッドのいずれかの完了を待機せず、その他の操作を実行に進みますが、`DbContext`の状態、`DbContext`を指定できます (および多くの場合は) 破損しています。</span><span class="sxs-lookup"><span data-stu-id="82cee-148">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span> 

<span data-ttu-id="82cee-149">常に EF Core の非同期メソッドをすぐに待機してください。</span><span class="sxs-lookup"><span data-stu-id="82cee-149">Always await EF Core asynchronous methods immediately.</span></span>  

### <a name="implicitly-sharing-dbcontext-instances-across-multiple-threads-via-dependency-injection"></a><span data-ttu-id="82cee-150">依存関係の挿入を使用して複数のスレッド間で暗黙的に DbContext インスタンスを共有</span><span class="sxs-lookup"><span data-stu-id="82cee-150">Implicitly sharing DbContext instances across multiple threads via dependency injection</span></span>

<span data-ttu-id="82cee-151">[ `AddDbContext` ](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)拡張メソッドを登録`DbContext`型、[有効期間がスコープ](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)既定。</span><span class="sxs-lookup"><span data-stu-id="82cee-151">The [`AddDbContext`](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span> 

<span data-ttu-id="82cee-152">これは、特定の時点で各クライアント要求を実行する 1 つのスレッドがあるため、各要求は、別の依存関係の挿入のスコープを取得するための同時アクセスの問題、ASP.NET Core アプリケーションで安全な (したがって独立した`DbContext`インスタンスの場合)。</span><span class="sxs-lookup"><span data-stu-id="82cee-152">This is safe from concurrent access issues in ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span>

<span data-ttu-id="82cee-153">ただし、明示的に複数のスレッドを並行で実行されるコードを確認する必要がありますを`DbContext`インスタンスは同時に accesed されてこと。</span><span class="sxs-lookup"><span data-stu-id="82cee-153">However any code that explicitly executes multiple threads in paralell should ensure that `DbContext` instances aren't ever accesed concurrently.</span></span>

<span data-ttu-id="82cee-154">依存関係の挿入を使用して、これを行うかが対象とし、作成スコープとコンテキストを登録することによって (を使用して`IServiceScopeFactory`) スレッドごと、または登録することによって、`DbContext`一時的なものとして (のオーバー ロードを使用して`AddDbContext`を受け取ります`ServiceLifetime`パラメーター)。</span><span class="sxs-lookup"><span data-stu-id="82cee-154">Using dependency injection, this can be achieved by either registering the context as scoped and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="82cee-155">複数の読み取り</span><span class="sxs-lookup"><span data-stu-id="82cee-155">More reading</span></span>

* <span data-ttu-id="82cee-156">読み取り[ASP.NET Core の概要](../get-started/aspnetcore/index.md)EF を使用して ASP.NET Core での詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="82cee-156">Read [Getting Started on ASP.NET Core](../get-started/aspnetcore/index.md) for more information on using EF with ASP.NET Core.</span></span>
* <span data-ttu-id="82cee-157">読み取り[依存関係の注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)を DI を使用する方法の詳細を参照してください。</span><span class="sxs-lookup"><span data-stu-id="82cee-157">Read [Dependency Injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
* <span data-ttu-id="82cee-158">読み取り[テスト](testing/index.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="82cee-158">Read [Testing](testing/index.md) for more information.</span></span>
