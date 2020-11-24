---
title: DbContext の有効期間、構成、および初期化 - EF Core
description: 依存関係の挿入がある場合とない場合の DbContext インスタンスを作成および管理するパターン
author: ajcvickers
ms.date: 11/07/2020
uid: core/dbcontext-configuration/index
ms.openlocfilehash: f4d51e10555844b5a14000a464f86d3440d5749e
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003290"
---
# <a name="dbcontext-lifetime-configuration-and-initialization"></a><span data-ttu-id="7d2c2-103">DbContext の有効期間、構成、および初期化</span><span class="sxs-lookup"><span data-stu-id="7d2c2-103">DbContext Lifetime, Configuration, and Initialization</span></span>

<span data-ttu-id="7d2c2-104">この記事では、<xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスの初期化と構成の基本的なパターンについて説明します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-104">This article shows basic patterns for initialization and configuration of a <xref:Microsoft.EntityFrameworkCore.DbContext> instance.</span></span>

## <a name="the-dbcontext-lifetime"></a><span data-ttu-id="7d2c2-105">DbContext の有効期間</span><span class="sxs-lookup"><span data-stu-id="7d2c2-105">The DbContext lifetime</span></span>

<span data-ttu-id="7d2c2-106">`DbContext` の有効期間は、インスタンスが作成された時点で開始し、インスタンスが[破棄された](/dotnet/standard/garbage-collection/unmanaged)時点で終了します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-106">The lifetime of a `DbContext` begins when the instance is created and ends when the instance is [disposed](/dotnet/standard/garbage-collection/unmanaged).</span></span> <span data-ttu-id="7d2c2-107">`DbContext` インスタンスは、"_単一_" の[作業単位](https://www.martinfowler.com/eaaCatalog/unitOfWork.html)で使用するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-107">A `DbContext` instance is designed to be used for a _single_ [unit-of-work](https://www.martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="7d2c2-108">これは、`DbContext` インスタンスの有効期間が通常は非常に短いことを意味します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-108">This means that the lifetime of a `DbContext` instance is usually very short.</span></span>

> [!TIP]
> <span data-ttu-id="7d2c2-109">上のリンクから Martin Fowler 氏の言葉を引用します。「作業単位によって、データベースに影響する可能性があるビジネス トランザクションの間に実行した処理がすべて追跡されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-109">To quote Martin Fowler from the link above, "A Unit of Work keeps track of everything you do during a business transaction that can affect the database.</span></span> <span data-ttu-id="7d2c2-110">完了時に、作業の結果としてデータベースを変更するために何を実行する必要があるかがすべて示されます。」</span><span class="sxs-lookup"><span data-stu-id="7d2c2-110">When you're done, it figures out everything that needs to be done to alter the database as a result of your work."</span></span>

<span data-ttu-id="7d2c2-111">Entity Framework Core (EF Core) を使用する場合の一般的な作業単位には、次のものがあります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-111">A typical unit-of-work when using Entity Framework Core (EF Core) involves:</span></span>

- <span data-ttu-id="7d2c2-112">`DbContext` インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-112">Creation of a `DbContext` instance</span></span>
- <span data-ttu-id="7d2c2-113">コンテキストによってエンティティ インスタンスが追跡されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-113">Tracking of entity instances by the context.</span></span> <span data-ttu-id="7d2c2-114">エンティティは次の場合に追跡されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-114">Entities become tracked by</span></span>
  - <span data-ttu-id="7d2c2-115">[クエリから返された](xref:core/querying/tracking)場合</span><span class="sxs-lookup"><span data-stu-id="7d2c2-115">Being [returned from a query](xref:core/querying/tracking)</span></span>
  - <span data-ttu-id="7d2c2-116">[コンテキストに追加またはアタッチされた](xref:core/saving/disconnected-entities)場合</span><span class="sxs-lookup"><span data-stu-id="7d2c2-116">Being [added or attached to the context](xref:core/saving/disconnected-entities)</span></span>
- <span data-ttu-id="7d2c2-117">ビジネス ルールを実装するために、必要に応じて追跡対象エンティティに変更が加えられます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-117">Changes are made to the tracked entities as needed to implement the business rule</span></span>
- <span data-ttu-id="7d2c2-118"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> または <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-118"><xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> is called.</span></span> <span data-ttu-id="7d2c2-119">行われた変更が EF Core によって検出されてデータベースに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-119">EF Core detects the changes made and writes them to the database.</span></span>
- <span data-ttu-id="7d2c2-120">`DbContext` インスタンスが破棄されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-120">The `DbContext` instance is disposed</span></span>

> [!IMPORTANT]
>
> - <span data-ttu-id="7d2c2-121"><xref:Microsoft.EntityFrameworkCore.DbContext> を使用後に破棄することは、非常に重要です。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-121">It is very important to dispose the <xref:Microsoft.EntityFrameworkCore.DbContext> after use.</span></span> <span data-ttu-id="7d2c2-122">これにより、インスタンスが参照されたままの場合のメモリ リークを防ぐため、すべてのアンマネージド リソースが解放されることと、すべてのイベントまたは他のフックが登録解除されることの両方が確実に行われます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-122">This ensures both that any unmanaged resources are freed, and that any events or other hooks are unregistered so as to prevent memory leaks in case the instance remains referenced.</span></span>
> - <span data-ttu-id="7d2c2-123">[DbContext は **スレッドセーフではありません**](#avoiding-dbcontext-threading-issues)。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-123">[DbContext is **not thread-safe**](#avoiding-dbcontext-threading-issues).</span></span> <span data-ttu-id="7d2c2-124">スレッド間でコンテキストを共有しないでください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-124">Do not share contexts between threads.</span></span> <span data-ttu-id="7d2c2-125">コンテキスト インスタンスの使用を継続する前に、すべての非同期呼び出しを必ず[待機](/dotnet/csharp/language-reference/operators/await)するようにしてください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-125">Make sure to [await](/dotnet/csharp/language-reference/operators/await) all async calls before continuing to use the context instance.</span></span>
> - <span data-ttu-id="7d2c2-126">EF Core コードによってスローされた <xref:System.InvalidOperationException> によって、コンテキストが回復不能な状態になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-126">An <xref:System.InvalidOperationException> thrown by EF Core code can put the context into an unrecoverable state.</span></span> <span data-ttu-id="7d2c2-127">このような例外はプログラムのエラーを示すものであり、回復が行われるようには設計されていません。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-127">Such exceptions indicate a program error and are not designed to be recovered from.</span></span>

## <a name="dbcontext-in-dependency-injection-for-aspnet-core"></a><span data-ttu-id="7d2c2-128">ASP.NET Core の依存関係の挿入における DbContext</span><span class="sxs-lookup"><span data-stu-id="7d2c2-128">DbContext in dependency injection for ASP.NET Core</span></span>

<span data-ttu-id="7d2c2-129">多くの Web アプリケーションでは、各 HTTP 要求は 1 つの作業単位に対応します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-129">In many web applications, each HTTP request corresponds to a single unit-of-work.</span></span> <span data-ttu-id="7d2c2-130">このため、コンテキストの有効期間を要求のものに結び付けることが、Web アプリケーションの適切な既定値になります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-130">This makes tying the context lifetime to that of the request a good default for web applications.</span></span>

<span data-ttu-id="7d2c2-131">ASP.NET Core アプリケーションは、[依存関係の挿入を使用して構成されます](/aspnet/core/fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-131">ASP.NET Core applications are [configured using dependency injection](/aspnet/core/fundamentals/startup).</span></span> <span data-ttu-id="7d2c2-132">EF Core は、`Startup.cs` の [`ConfigurureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) メソッド内で <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> を使用することで、この構成に追加することができます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-132">EF Core can be added to this configuration using <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> in the [`ConfigurureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) method of `Startup.cs`.</span></span> <span data-ttu-id="7d2c2-133">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-133">For example:</span></span>

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();
            
            services.AddDbContext<ApplicationDbContext>(
                options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Startup.cs?name=ConfigureServices)]

<span data-ttu-id="7d2c2-134">この例では、`ApplicationDbContext` と呼ばれる `DbContext` サブクラスが、ASP.NET Core アプリケーション サービス プロバイダーにスコープ サービスとして登録されます (または</span><span class="sxs-lookup"><span data-stu-id="7d2c2-134">This example registers a `DbContext` subclass called `ApplicationDbContext` as a scoped service in the ASP.NET Core application service provider (a.k.a.</span></span> <span data-ttu-id="7d2c2-135">依存関係の挿入コントローラーとも呼ばれます)。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-135">the dependency injection container).</span></span> <span data-ttu-id="7d2c2-136">コンテキストは SQL Server データベース プロバイダーを使用するように構成されます。接続文字列は ASP.NET Core 構成から読み取られます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-136">The context is configured to use the SQL Server database provider and will read the connection string from ASP.NET Core configuration.</span></span> <span data-ttu-id="7d2c2-137">通常、`AddDbContext` の呼び出しが `ConfigureServices` の "_どこで_" 行われるかは問題にはなりません。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-137">It typically does not matter _where_ in `ConfigureServices` the call to `AddDbContext` is made.</span></span>

<span data-ttu-id="7d2c2-138">`ApplicationDbContext` クラスによって、`DbContextOptions<ApplicationDbContext>` パラメーターが指定されたパブリック コンストラクターが公開される必要があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-138">The `ApplicationDbContext` class must expose a public constructor with a `DbContextOptions<ApplicationDbContext>` parameter.</span></span> <span data-ttu-id="7d2c2-139">これは、`AddDbContext` からのコンテキスト構成を `DbContext` に渡す方法です。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-139">This is how context configuration from `AddDbContext` is passed to the `DbContext`.</span></span> <span data-ttu-id="7d2c2-140">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-140">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="7d2c2-141">`ApplicationDbContext` はその後、コンストラクターの挿入を介して、ASP.NET Core コントローラーまたはその他のサービスで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-141">`ApplicationDbContext` can then be used in ASP.NET Core controllers or other services through constructor injection.</span></span> <span data-ttu-id="7d2c2-142">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-142">For example:</span></span>

<!--
    public class MyController
    {
        private readonly ApplicationDbContext _context;

        public MyController(ApplicationDbContext context)
        {
            _context = context;
        }
    }
-->
[!code-csharp[MyController](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Controllers/MyController.cs?name=MyController)]

<span data-ttu-id="7d2c2-143">最終的な結果は、要求ごとに作成され、要求の終了時に破棄される前に作業単位を実行するためにコントローラーに渡される、`ApplicationDbContext` インスタンスです。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-143">The final result is an `ApplicationDbContext` instance created for each request and passed to the controller to perform a unit-of-work before being disposed when the request ends.</span></span>

<span data-ttu-id="7d2c2-144">構成オプションの詳細については、この記事を読み進めてください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-144">Read further in this article to learn more about configuration options.</span></span> <span data-ttu-id="7d2c2-145">また、ASP.NET Core の構成と依存関係の挿入の詳細については、「[ASP.NET Core でのアプリケーションのスタートアップ](/aspnet/core/fundamentals/startup)」と「[ASP.NET Core での依存関係の挿入](/aspnet/core/fundamentals/dependency-injection)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-145">In addition, see [App startup in ASP.NET Core](/aspnet/core/fundamentals/startup) and [Dependency injection in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection) for more information on configuration and dependency injection in ASP.NET Core.</span></span>

<!-- See also [Using Dependency Injection](TODO) for advanced dependency injection configuration with EF Core. -->

## <a name="simple-dbcontext-initialization-with-new"></a><span data-ttu-id="7d2c2-146">'new' の使用による単純な DbContext の初期化</span><span class="sxs-lookup"><span data-stu-id="7d2c2-146">Simple DbContext initialization with 'new'</span></span>

<span data-ttu-id="7d2c2-147">`DbContext` インスタンスは、たとえば C# の `new` を使用するなど、通常の .NET 方式で構築できます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-147">`DbContext` instances can be constructed in the normal .NET way, for example with `new` in C#.</span></span> <span data-ttu-id="7d2c2-148">構成を実行するには、`OnConfiguring` メソッドをオーバーライドするか、またはコンストラクターにオプションを渡します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-148">Configuration can be performed by overriding the `OnConfiguring` method, or by passing options to the constructor.</span></span> <span data-ttu-id="7d2c2-149">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-149">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="7d2c2-150">また、このパターンを使用すると、`DbContext` コンストラクターを介して接続文字列のような構成を簡単に渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-150">This pattern also makes it easy to pass configuration like the connection string via the `DbContext` constructor.</span></span> <span data-ttu-id="7d2c2-151">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-151">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        private readonly string _connectionString;

        public ApplicationDbContext(string connectionString)
        {
            _connectionString = connectionString;
        }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(_connectionString);
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNewAndArgs/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="7d2c2-152">または、`DbContextOptionsBuilder` を使用して `DbContextOptions` オブジェクトを作成し、それを `DbContext` コンストラクターに渡すこともできます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-152">Alternately, `DbContextOptionsBuilder` can be used to create a `DbContextOptions` object that is then passed to the `DbContext` constructor.</span></span> <span data-ttu-id="7d2c2-153">これにより、依存関係の挿入用に構成された `DbContext` も明示的に構築することができます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-153">This allows a `DbContext` configured for dependency injection to also be constructed explicitly.</span></span> <span data-ttu-id="7d2c2-154">たとえば、上述の ASP.NET Core Web アプリ用に定義された `ApplicationDbContext` を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-154">For example, when using `ApplicationDbContext` defined for ASP.NET Core web apps above:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="7d2c2-155">`DbContextOptions` を作成し、コンストラクターを明示的に呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-155">The `DbContextOptions` can be created and the constructor can be called explicitly:</span></span>

<!--
            var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                .Options;

            using var context = new ApplicationDbContext(contextOptions);
-->
[!code-csharp[UseNewForWebApp](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/UseNewForWebApp.cs?name=UseNewForWebApp)]

## <a name="using-a-dbcontext-factory-eg-for-blazor"></a><span data-ttu-id="7d2c2-156">DbContext ファクトリの使用 (例: Blazor)</span><span class="sxs-lookup"><span data-stu-id="7d2c2-156">Using a DbContext factory (e.g. for Blazor)</span></span>

<span data-ttu-id="7d2c2-157">一部のアプリケーションの種類 ([ASP.NET Core Blazor](/aspnet/core/blazor/) など) では、依存関係の挿入が使用されますが、目的の `DbContext` の有効期間に合わせたサービス スコープが作成されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-157">Some application types (e.g. [ASP.NET Core Blazor](/aspnet/core/blazor/)) use dependency injection but do not create a service scope that aligns with the desired `DbContext` lifetime.</span></span> <span data-ttu-id="7d2c2-158">このような調整が行われる場合であっても、このスコープ内で複数の作業単位がアプリケーションによって実行されなければならない場合があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-158">Even where such an alignment does exist, the application may need to perform multiple units-of-work within this scope.</span></span> <span data-ttu-id="7d2c2-159">たとえば、1 つの HTTP 要求内に複数の作業単位がある場合です。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-159">For example, multiple units-of-work within a single HTTP request.</span></span>

<span data-ttu-id="7d2c2-160">このような場合、<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> を使用して、`DbContext` インスタンスを作成するためのファクトリを登録できます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-160">In these cases, <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> can be used to register a factory for creation of `DbContext` instances.</span></span> <span data-ttu-id="7d2c2-161">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-161">For example:</span></span>

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContextFactory<ApplicationDbContext>(options =>
                options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/FactoryServicesExample.cs?name=ConfigureServices)]

<span data-ttu-id="7d2c2-162">`ApplicationDbContext` クラスによって、`DbContextOptions<ApplicationDbContext>` パラメーターが指定されたパブリック コンストラクターが公開される必要があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-162">The `ApplicationDbContext` class must expose a public constructor with a `DbContextOptions<ApplicationDbContext>` parameter.</span></span> <span data-ttu-id="7d2c2-163">これは、上述の従来の ASP.NET Core に関するセクションで使用したパターンと同じものです。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-163">This is the same pattern as used in the traditional ASP.NET Core section above.</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="7d2c2-164">`DbContextFactory` ファクトリはその後、コンストラクターの挿入を介して他のサービスで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-164">The `DbContextFactory` factory can then be used in other services through constructor injection.</span></span> <span data-ttu-id="7d2c2-165">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-165">For example:</span></span>

<!--
        private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

        public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
        {
            _contextFactory = contextFactory;
        }
-->
[!code-csharp[Construct](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=Construct)]

<span data-ttu-id="7d2c2-166">挿入されたファクトリはその後、サービス コード内で DbContext インスタンスを構築するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-166">The injected factory can then be used to construct DbContext instances in the service code.</span></span> <span data-ttu-id="7d2c2-167">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-167">For example:</span></span>

<!--
        public void DoSomething()
        {
            using (var context = _contextFactory.CreateDbContext())
            {
                // ...
            }
        }
-->
[!code-csharp[DoSomething](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=DoSomething)]

<span data-ttu-id="7d2c2-168">なお、この方法で作成された `DbContext` インスタンスは、アプリケーションのサービス プロバイダーには管理 "**されず**"、従ってアプリケーションによって破棄される必要があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-168">Notice that the `DbContext` instances created in this way are **not** managed by the application's service provider and therefore must be disposed by the application.</span></span>

<span data-ttu-id="7d2c2-169">Blazor での EF Core の使用に関する詳細については、「[ASP.NET Core Blazor Server と Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-169">See [ASP.NET Core Blazor Server with Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core) for more information on using EF Core with Blazor.</span></span>

## <a name="dbcontextoptions"></a><span data-ttu-id="7d2c2-170">DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="7d2c2-170">DbContextOptions</span></span>

<span data-ttu-id="7d2c2-171">すべての `DbContext` 構成の開始地点は、<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> です。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-171">The starting point for all `DbContext` configuration is <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>.</span></span> <span data-ttu-id="7d2c2-172">このビルダーを入手するには、次の 3 つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-172">There are three ways to get this builder:</span></span>

- <span data-ttu-id="7d2c2-173">`AddDbContext` および関連するメソッド</span><span class="sxs-lookup"><span data-stu-id="7d2c2-173">In `AddDbContext` and related methods</span></span>
- <span data-ttu-id="7d2c2-174">`OnConfiguring`</span><span class="sxs-lookup"><span data-stu-id="7d2c2-174">In `OnConfiguring`</span></span>
- <span data-ttu-id="7d2c2-175">`new`の使用による明示的な構築</span><span class="sxs-lookup"><span data-stu-id="7d2c2-175">Constructed explicitly with `new`</span></span>

<span data-ttu-id="7d2c2-176">これらのそれぞれの例については、これまでのセクションに示されています。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-176">Examples of each of these are shown in the preceding sections.</span></span> <span data-ttu-id="7d2c2-177">ビルダーの取得元に関係なく、同じ構成を適用できます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-177">The same configuration can be applied regardless of where the builder comes from.</span></span> <span data-ttu-id="7d2c2-178">また、コンテキストの構築方法に関係なく、常に `OnConfiguring` が呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-178">In addition, `OnConfiguring` is always called regardless of how the context is constructed.</span></span> <span data-ttu-id="7d2c2-179">これは、`AddDbContext` が使用されている場合でも、`OnConfiguring` を使用して追加の構成を実行できることを意味します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-179">This means `OnConfiguring` can be used to perform additional configuration even when `AddDbContext` is being used.</span></span>

### <a name="configuring-the-database-provider"></a><span data-ttu-id="7d2c2-180">データベースプロバイダーの構成</span><span class="sxs-lookup"><span data-stu-id="7d2c2-180">Configuring the database provider</span></span>

<span data-ttu-id="7d2c2-181">各 `DbContext` インスタンスは、1 つのデータベース プロバイダーのみを使用するように構成する必要があります</span><span class="sxs-lookup"><span data-stu-id="7d2c2-181">Each `DbContext` instance must be configured to use one and only one database provider.</span></span> <span data-ttu-id="7d2c2-182">(`DbContext` サブタイプの異なるインスタンスは、異なるデータベース プロバイダーで使用できますが、1 つのインスタンスで使用できるのは 1 つだけです)。データベース プロバイダーは、特定の `Use*`" 呼び出しを使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-182">(Different instances of a `DbContext` subtype can be used with different database providers, but a single instance must only use one.) A database provider is configured using a specific `Use*`" call.</span></span> <span data-ttu-id="7d2c2-183">たとえば、SQL Server データベース プロバイダーを使用するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-183">For example, to use the SQL Server database provider:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithNew/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="7d2c2-184">これらの `Use*`" メソッドは、データベース プロバイダーによって実装される拡張メソッドです。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-184">These `Use*`" methods are extension methods implemented by the database provider.</span></span> <span data-ttu-id="7d2c2-185">これは、拡張メソッドを使用する前に、データベース プロバイダーの NuGet パッケージをインストールする必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-185">This means that the database provider NuGet package must be installed before the extension method can be used.</span></span>

> [!TIP]
> <span data-ttu-id="7d2c2-186">[拡張メソッド](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)は EF Core データベース プロバイダーによって広く使用されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-186">EF Core database providers make extensive use of [extension methods](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods).</span></span> <span data-ttu-id="7d2c2-187">メソッドが見つからないことがコンパイラによって示された場合は、プロバイダーの NuGet パッケージがインストールされていることと、コードに `using Microsoft.EntityFrameworkCore;` が記述されていることを確認してください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-187">If the compiler indicates that a method cannot be found, then make sure that the provider's NuGet package is installed and that you have `using Microsoft.EntityFrameworkCore;` in your code.</span></span>

<span data-ttu-id="7d2c2-188">次の表に、一般的なデータベース プロバイダーの例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-188">The following table contains examples for common database providers.</span></span>

| <span data-ttu-id="7d2c2-189">データベース システム</span><span class="sxs-lookup"><span data-stu-id="7d2c2-189">Database system</span></span>              | <span data-ttu-id="7d2c2-190">構成例</span><span class="sxs-lookup"><span data-stu-id="7d2c2-190">Example configuration</span></span>                         | <span data-ttu-id="7d2c2-191">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="7d2c2-191">NuGet package</span></span>
|:-----------------------------|-----------------------------------------------|--------------
| <span data-ttu-id="7d2c2-192">SQL Server または Azure SQL</span><span class="sxs-lookup"><span data-stu-id="7d2c2-192">SQL Server or Azure SQL</span></span>      | `.UseSqlServer(connectionString)`             | [<span data-ttu-id="7d2c2-193">Microsoft.EntityFrameworkCore.SqlServer</span><span class="sxs-lookup"><span data-stu-id="7d2c2-193">Microsoft.EntityFrameworkCore.SqlServer</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
| <span data-ttu-id="7d2c2-194">Azure Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="7d2c2-194">Azure Cosmos DB</span></span>              | `.UseCosmos(connectionString, databaseName)`  | [<span data-ttu-id="7d2c2-195">Microsoft.EntityFrameworkCore.Cosmos</span><span class="sxs-lookup"><span data-stu-id="7d2c2-195">Microsoft.EntityFrameworkCore.Cosmos</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)
| <span data-ttu-id="7d2c2-196">SQLite</span><span class="sxs-lookup"><span data-stu-id="7d2c2-196">SQLite</span></span>                       | `.UseSqlite(connectionString)`                | [<span data-ttu-id="7d2c2-197">Microsoft.EntityFrameworkCore.Sqlite</span><span class="sxs-lookup"><span data-stu-id="7d2c2-197">Microsoft.EntityFrameworkCore.Sqlite</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)
| <span data-ttu-id="7d2c2-198">EF Core In-Memory データベース</span><span class="sxs-lookup"><span data-stu-id="7d2c2-198">EF Core in-memory database</span></span>   | `.UseInMemoryDatabase(databaseName)`          | [<span data-ttu-id="7d2c2-199">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="7d2c2-199">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)
| <span data-ttu-id="7d2c2-200">PostgreSQL\*</span><span class="sxs-lookup"><span data-stu-id="7d2c2-200">PostgreSQL\*</span></span>                  | `.UseNpgsql(connectionString)`                | [<span data-ttu-id="7d2c2-201">Npgsql.EntityFrameworkCore.PostgreSQL</span><span class="sxs-lookup"><span data-stu-id="7d2c2-201">Npgsql.EntityFrameworkCore.PostgreSQL</span></span>](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
| <span data-ttu-id="7d2c2-202">MySQL/MariaDB\*</span><span class="sxs-lookup"><span data-stu-id="7d2c2-202">MySQL/MariaDB\*</span></span>               | `.UseMySql((connectionString)`                | [<span data-ttu-id="7d2c2-203">Pomelo.EntityFrameworkCore.MySql</span><span class="sxs-lookup"><span data-stu-id="7d2c2-203">Pomelo.EntityFrameworkCore.MySql</span></span>](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)
| <span data-ttu-id="7d2c2-204">Oracle\*</span><span class="sxs-lookup"><span data-stu-id="7d2c2-204">Oracle\*</span></span>                      | `.UseOracle(connectionString)`                | [<span data-ttu-id="7d2c2-205">Oracle.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="7d2c2-205">Oracle.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)

<span data-ttu-id="7d2c2-206">\* Microsoft はこれらのデータベース プロバイダーを出荷していません。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-206">\*These database providers are not shipped by Microsoft.</span></span> <span data-ttu-id="7d2c2-207">データベース プロバイダーの詳細については、「[データベース プロバイダー](xref:core/providers/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-207">See [Database Providers](xref:core/providers/index) for more information about database providers.</span></span>

> [!WARNING]
> <span data-ttu-id="7d2c2-208">EF Core インメモリ データベースは、運用環境で使用するように設計されていません。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-208">The EF Core in-memory database is not designed for production use.</span></span> <span data-ttu-id="7d2c2-209">また、テスト用にも最適な選択肢ではない場合があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-209">In addition, it may not be the best choice even for testing.</span></span> <span data-ttu-id="7d2c2-210">詳細については、「[EF Core を使用するコードのテスト](xref:core/testing/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-210">See [Testing Code That Uses EF Core](xref:core/testing/index) for more information.</span></span>

<span data-ttu-id="7d2c2-211">EF Core で接続文字列を使用する方法の詳細については、「[接続文字列](xref:core/miscellaneous/connection-strings)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-211">See [Connection Strings](xref:core/miscellaneous/connection-strings) for more information on using connection strings with EF Core.</span></span>

<span data-ttu-id="7d2c2-212">データベース プロバイダーに固有の省略可能な構成は、追加のプロバイダー固有のビルダーで実行されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-212">Optional configuration specific to the database provider is performed in an additional provider-specific builder.</span></span> <span data-ttu-id="7d2c2-213">たとえば、<xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> を使用して、Azure SQL に接続するときに接続の回復性の再試行を構成します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-213">For example, using <xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> to configure retries for connection resiliency when connecting to Azure SQL:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .UseSqlServer(
                    @"Server=(localdb)\mssqllocaldb;Database=Test",
                    providerOptions =>
                        {
                            providerOptions.EnableRetryOnFailure();
                        });
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalProviderConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

> [!TIP]
> <span data-ttu-id="7d2c2-214">SQL Server と Azure SQL には、同じデータベース プロバイダーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-214">The same database provider is used for SQL Server and Azure SQL.</span></span> <span data-ttu-id="7d2c2-215">ただし、SQL Azure に接続するときは[接続の回復性](xref:core/miscellaneous/connection-resiliency)を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-215">However, it is recommended that [connection resiliency](xref:core/miscellaneous/connection-resiliency) be used when connecting to SQL Azure.</span></span>

<span data-ttu-id="7d2c2-216">プロバイダー固有の構成の詳細については、「[データベース プロバイダー](xref:core/providers/index)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-216">See [Database Providers](xref:core/providers/index) for more information on provider-specific configuration.</span></span>

### <a name="other-dbcontext-configuration"></a><span data-ttu-id="7d2c2-217">その他の DbContext の構成</span><span class="sxs-lookup"><span data-stu-id="7d2c2-217">Other DbContext configuration</span></span>

<span data-ttu-id="7d2c2-218">その他の `DbContext` の構成は、`Use*` 呼び出しの前または後のいずれかにチェーンすることができます (どちらでも違いはありません)。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-218">Other `DbContext` configuration can be chained either before or after (it makes no difference which) the `Use*` call.</span></span> <span data-ttu-id="7d2c2-219">たとえば、機密データのログ記録を有効にするには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-219">For example, to turn on sensitive-data logging:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder
                .EnableSensitiveDataLogging()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test");
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/AdditionalConfiguration/ApplicationDbContext.cs?name=ApplicationDbContext)]

<span data-ttu-id="7d2c2-220">次の表に、`DbContextOptionsBuilder` で呼び出される一般的なメソッドの例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-220">The following table contains examples of common methods called on `DbContextOptionsBuilder`.</span></span>

| <span data-ttu-id="7d2c2-221">DbContextOptionsBuilder メソッド</span><span class="sxs-lookup"><span data-stu-id="7d2c2-221">DbContextOptionsBuilder method</span></span>                                                             | <span data-ttu-id="7d2c2-222">実行内容</span><span class="sxs-lookup"><span data-stu-id="7d2c2-222">What it does</span></span>                                                | <span data-ttu-id="7d2c2-223">詳細情報</span><span class="sxs-lookup"><span data-stu-id="7d2c2-223">Learn more</span></span>
|:-------------------------------------------------------------------------------------------|-------------------------------------------------------------|--------------
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseQueryTrackingBehavior%2A>   | <span data-ttu-id="7d2c2-224">クエリの既定の追跡動作が設定されます</span><span class="sxs-lookup"><span data-stu-id="7d2c2-224">Sets the default tracking behavior for queries</span></span>              | [<span data-ttu-id="7d2c2-225">クエリの追跡動作</span><span class="sxs-lookup"><span data-stu-id="7d2c2-225">Query Tracking Behavior</span></span>](xref:core/querying/tracking)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A>                      | <span data-ttu-id="7d2c2-226">EF Core ログを取得するシンプルな方法 (EF Core 5.0 以降)</span><span class="sxs-lookup"><span data-stu-id="7d2c2-226">A simple way to get EF Core logs (EF Core 5.0 and later)</span></span>    | [<span data-ttu-id="7d2c2-227">ログ、イベント、診断</span><span class="sxs-lookup"><span data-stu-id="7d2c2-227">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseLoggerFactory%2A>           | <span data-ttu-id="7d2c2-228">`Micrsofot.Extensions.Logging` ファクトリが登録されます</span><span class="sxs-lookup"><span data-stu-id="7d2c2-228">Registers an `Micrsofot.Extensions.Logging` factory</span></span>         | [<span data-ttu-id="7d2c2-229">ログ、イベント、診断</span><span class="sxs-lookup"><span data-stu-id="7d2c2-229">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> | <span data-ttu-id="7d2c2-230">例外とログにアプリケーション データが含められます</span><span class="sxs-lookup"><span data-stu-id="7d2c2-230">Includes application data in exceptions and logging</span></span>         | [<span data-ttu-id="7d2c2-231">ログ、イベント、診断</span><span class="sxs-lookup"><span data-stu-id="7d2c2-231">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A>       | <span data-ttu-id="7d2c2-232">より詳細なクエリ エラー (パフォーマンスの低下と引き換え)</span><span class="sxs-lookup"><span data-stu-id="7d2c2-232">More detailed query errors (at the expense of performance)</span></span>  | [<span data-ttu-id="7d2c2-233">ログ、イベント、診断</span><span class="sxs-lookup"><span data-stu-id="7d2c2-233">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A>          | <span data-ttu-id="7d2c2-234">警告やその他のイベントが無視またはスローされます</span><span class="sxs-lookup"><span data-stu-id="7d2c2-234">Ignore or throw for warnings and other events</span></span>               | [<span data-ttu-id="7d2c2-235">ログ、イベント、診断</span><span class="sxs-lookup"><span data-stu-id="7d2c2-235">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A>            | <span data-ttu-id="7d2c2-236">EF Core インターセプターが登録されます</span><span class="sxs-lookup"><span data-stu-id="7d2c2-236">Registers EF Core interceptors</span></span>                              | [<span data-ttu-id="7d2c2-237">ログ、イベント、診断</span><span class="sxs-lookup"><span data-stu-id="7d2c2-237">Logging, Events, and Diagnostics</span></span>](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A>            | <span data-ttu-id="7d2c2-238">遅延読み込みのために動的プロキシが使用されます</span><span class="sxs-lookup"><span data-stu-id="7d2c2-238">Use dynamic proxies for lazy-loading</span></span>                        | [<span data-ttu-id="7d2c2-239">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="7d2c2-239">Lazy Loading</span></span>](xref:core/querying/related-data/lazy)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>         | <span data-ttu-id="7d2c2-240">変更追跡のために動的プロキシが使用されます</span><span class="sxs-lookup"><span data-stu-id="7d2c2-240">Use dynamic proxies for change-tracking</span></span>                     | <span data-ttu-id="7d2c2-241">近日公開予定...</span><span class="sxs-lookup"><span data-stu-id="7d2c2-241">Coming soon...</span></span>

> [!NOTE]
> <span data-ttu-id="7d2c2-242"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> と <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> は、[Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet パッケージの拡張メソッドです。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-242"><xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> and <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> are extension methods from the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet package.</span></span> <span data-ttu-id="7d2c2-243">この種の ".UseXXX()" 呼び出しは、他のパッケージに含まれている EF Core の拡張機能を構成または使用するための推奨される方法です。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-243">This kind of ".UseSomething()" call is the recommended way to configure and/or use EF Core extensions contained in other packages.</span></span>

### <a name="dbcontextoptions-verses-dbcontextoptionstcontext"></a><span data-ttu-id="7d2c2-244">`DbContextOptions` の `DbContextOptions<TContext>` への変更</span><span class="sxs-lookup"><span data-stu-id="7d2c2-244">`DbContextOptions` verses `DbContextOptions<TContext>`</span></span>

<span data-ttu-id="7d2c2-245">`DbContextOptions` を受け入れるほとんどの `DbContext` サブクラスでは、[ジェネリック](/dotnet/csharp/programming-guide/generics/)の `DbContextOptions<TContext>` バリエーションを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-245">Most `DbContext` subclasses that accept a `DbContextOptions` should use the [generic](/dotnet/csharp/programming-guide/generics/) `DbContextOptions<TContext>` variation.</span></span> <span data-ttu-id="7d2c2-246">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-246">For example:</span></span>

<!--
    public sealed class SealedApplicationDbContext : DbContext
    {
        public SealedApplicationDbContext(DbContextOptions<SealedApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[SealedApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=SealedApplicationDbContext)]

<span data-ttu-id="7d2c2-247">これにより、複数の `DbContext` サブタイプが登録されている場合でも、特定の `DbContext` サブタイプの正しいオプションが依存関係の挿入から解決されるようになります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-247">This ensures that the correct options for the specific `DbContext` subtype are resolved from dependency injection, even when multiple `DbContext` subtypes are registered.</span></span>

> [!TIP]
> <span data-ttu-id="7d2c2-248">DbContext はシールされる必要はありませんが、シールは継承するように設計されていないクラスに関してそうするためのベスト プラクティスです。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-248">Your DbContext does not need to be sealed, but sealing is best practice to do so for classes not designed to be inherited from.</span></span>

<span data-ttu-id="7d2c2-249">ただし、`DbContext` サブタイプ自体が継承するよう意図されている場合は、非ジェネリックの `DbContextOptions` を受け取る保護されたコンストラクターを公開する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-249">However, if the `DbContext` subtype is itself intended to be inherited from, then it should expose a protected constructor taking a non-generic `DbContextOptions`.</span></span> <span data-ttu-id="7d2c2-250">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-250">For example:</span></span>

<!--
    public abstract class ApplicationDbContextBase : DbContext
    {
        protected ApplicationDbContextBase(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContextBase](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContextBase)]

<span data-ttu-id="7d2c2-251">これにより、複数の具象サブクラスが、それぞれの異なるジェネリックの `DbContextOptions<TContext>` インスタンスを使用してこの基本コンストラクターを呼び出すことができるようになります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-251">This allows multiple concrete subclasses to call this base constructor using their different generic `DbContextOptions<TContext>` instances.</span></span> <span data-ttu-id="7d2c2-252">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-252">For example:</span></span>

<!--
    public sealed class ApplicationDbContext1 : ApplicationDbContextBase
    {
        public ApplicationDbContext1(DbContextOptions<ApplicationDbContext1> contextOptions)
            : base(contextOptions)
        {
        }
    }

    public sealed class ApplicationDbContext2 : ApplicationDbContextBase
    {
        public ApplicationDbContext2(DbContextOptions<ApplicationDbContext2> contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext12](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext12)]

<span data-ttu-id="7d2c2-253">これは、`DbContext` から直接継承する場合とまったく同じパターンであることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-253">Notice that this is exactly the same pattern as when inheriting from `DbContext` directly.</span></span> <span data-ttu-id="7d2c2-254">つまり、これが理由で、非ジェネリックの `DbContextOptions` が `DbContext` コンストラクター自体によって受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-254">That is, the `DbContext` constructor itself accepts a non-generic `DbContextOptions` for this reason.</span></span>

<span data-ttu-id="7d2c2-255">インスタンス化されることと継承されることの両方が意図されている `DbContext` サブクラスの場合、両方の形式のコンストラクターを公開する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-255">A `DbContext` subclass intended to be both instantiated and inherited from should expose both forms of constructor.</span></span> <span data-ttu-id="7d2c2-256">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-256">For example:</span></span>

<!--
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> contextOptions)
            : base(contextOptions)
        {
        }

        protected ApplicationDbContext(DbContextOptions contextOptions)
            : base(contextOptions)
        {
        }
    }
-->
[!code-csharp[ApplicationDbContext](../../../samples/core/Miscellaneous/ConfiguringDbContext/InheritDbContext/ApplicationDbContext.cs?name=ApplicationDbContext)]

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="7d2c2-257">デザイン時の DbContext の構成</span><span class="sxs-lookup"><span data-stu-id="7d2c2-257">Design-time DbContext configuration</span></span>

<span data-ttu-id="7d2c2-258">[EF Core 移行](xref:core/managing-schemas/migrations/index)用などの EF Core デザイン時ツールでは、アプリケーションのエンティティ型とそれらがデータベース スキーマにどのようにマップされるかに関する詳細情報を収集するため、`DbContext` 型の作業インスタンスを検出および作成できる必要があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-258">EF Core design-time tools such as those for [EF Core migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="7d2c2-259">このプロセスは、実行時に構成される場合と同様の方法で構成されるように `DbContext` をツールで簡単に作成できる限り、自動的に行われます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-259">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="7d2c2-260">`DbContext` に必要な構成情報を提供するパターンは実行時に動作しますが、デザイン時に `DbContext` を使用する必要があるツールは、限られた数のパターンでのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-260">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="7d2c2-261">これらについては、[デザイン時のコンテキストの作成](xref:core/cli/dbcontext-creation)に関する記事で詳しく説明されています。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-261">These are covered in more detail in [Design-Time Context Creation](xref:core/cli/dbcontext-creation).</span></span>

## <a name="avoiding-dbcontext-threading-issues"></a><span data-ttu-id="7d2c2-262">DbContext のスレッド処理の問題を回避する</span><span class="sxs-lookup"><span data-stu-id="7d2c2-262">Avoiding DbContext threading issues</span></span>

<span data-ttu-id="7d2c2-263">Entity Framework Core では、同じ `DbContext` インスタンス上での複数の並列操作の実行がサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-263">Entity Framework Core does not support multiple parallel operations being run on the same `DbContext` instance.</span></span> <span data-ttu-id="7d2c2-264">これには、非同期クエリの並列実行と、複数のスレッドからの明示的な同時使用の両方が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-264">This includes both parallel execution of async queries and any explicit concurrent use from multiple threads.</span></span> <span data-ttu-id="7d2c2-265">そのため、常に非同期呼び出しを直ちに `await` するか、並列実行される操作に対して個別の `DbContext` インスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-265">Therefore, always `await` async calls immediately, or use separate `DbContext` instances for operations that execute in parallel.</span></span>

<span data-ttu-id="7d2c2-266">`DbContext` インスタンスを同時に使用しようとしたことが EF Core によって検出されると、次のようなメッセージとともに `InvalidOperationException` が表示されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-266">When EF Core detects an attempt to use a `DbContext` instance concurrently, you'll see an `InvalidOperationException` with a message like this:</span></span>

> <span data-ttu-id="7d2c2-267">前の操作が完了する前に、このコンテキストで 2 つ目の操作が開始されました。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-267">A second operation started on this context before a previous operation completed.</span></span> <span data-ttu-id="7d2c2-268">これは通常、異なるスレッドで DbContext の同じインスタンスが使用された場合に発生しますが、インスタンスのメンバーがスレッド セーフである保証はありません。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-268">This is usually caused by different threads using the same instance of DbContext, however instance members are not guaranteed to be thread safe.</span></span>

<span data-ttu-id="7d2c2-269">同時アクセスが検出されない場合は、未定義の動作、アプリケーションのクラッシュ、データの破損が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-269">When concurrent access goes undetected, it can result in undefined behavior, application crashes and data corruption.</span></span>

<span data-ttu-id="7d2c2-270">同じ `DbContext` インスタンスに誤って同時にアクセスする原因として、一般に次のような間違いがあります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-270">There are common mistakes that can inadvertently cause concurrent access on the same `DbContext` instance:</span></span>

### <a name="asynchronous-operation-pitfalls"></a><span data-ttu-id="7d2c2-271">非同期操作の落とし穴</span><span class="sxs-lookup"><span data-stu-id="7d2c2-271">Asynchronous operation pitfalls</span></span>

<span data-ttu-id="7d2c2-272">EF Core で非同期メソッドを使用すると、非ブロッキング モードでデータベースにアクセスする操作を開始できます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-272">Asynchronous methods enable EF Core to initiate operations that access the database in a non-blocking way.</span></span> <span data-ttu-id="7d2c2-273">しかし、呼び出し元がこれらのメソッドのいずれかの完了を待機せず、`DbContext` に対して他の操作を実行しようとした場合、`DbContext` の状態は (非常に高い確率で) 破損する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-273">But if a caller does not await the completion of one of these methods, and proceeds to perform other operations on the `DbContext`, the state of the `DbContext` can be, (and very likely will be) corrupted.</span></span>

<span data-ttu-id="7d2c2-274">常に EF Core の非同期メソッドを直ちに待機してください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-274">Always await EF Core asynchronous methods immediately.</span></span>

### <a name="implicitly-sharing-dbcontext-instances-via-dependency-injection"></a><span data-ttu-id="7d2c2-275">依存関係の挿入によって DbContext インスタンスを暗黙的に共有する</span><span class="sxs-lookup"><span data-stu-id="7d2c2-275">Implicitly sharing DbContext instances via dependency injection</span></span>

<span data-ttu-id="7d2c2-276">[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 拡張メソッドを使用すると、[スコープ付きの有効期間](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)で `DbContext` 型が登録されます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-276">The [`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) extension method registers `DbContext` types with a [scoped lifetime](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) by default.</span></span>

<span data-ttu-id="7d2c2-277">この場合、ある時点で個々のクライアント要求を実行するスレッドは 1 つしかなく、各要求が別個の依存関係挿入スコープ (したがって、別個の `DbContext` インスタンス) を取得するため、ほとんどの ASP.NET Core アプリケーションでは同時アクセスの問題は発生しません。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-277">This is safe from concurrent access issues in most ASP.NET Core applications because there is only one thread executing each client request at a given time, and because each request gets a separate dependency injection scope (and therefore a separate `DbContext` instance).</span></span> <span data-ttu-id="7d2c2-278">Blazor Server ホスティング モデルでは、Blazor ユーザー回線を維持するために 1 つの論理要求が使用されるため、既定の挿入スコープが使用されている場合は、ユーザー回線ごとにスコープ付きの DbContext インスタンスを 1 つだけ使用できます。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-278">For Blazor Server hosting model, one logical request is used for maintaining the Blazor user circuit, and thus only one scoped DbContext instance is available per user circuit if the default injection scope is used.</span></span>

<span data-ttu-id="7d2c2-279">複数のスレッドを明示的に並列実行するコードでは、`DbContext` インスタンスが同時にアクセスされないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-279">Any code that explicitly executes multiple threads in parallel should ensure that `DbContext` instances aren't ever accessed concurrently.</span></span>

<span data-ttu-id="7d2c2-280">依存関係の挿入を使用してこれを実現するには、コンテキストをスコープ付きとして登録し、スレッドごとに (`IServiceScopeFactory` を使用して) スコープを作成するか、または (`ServiceLifetime` パラメーターを取る `AddDbContext` のオーバーロードを使用して) `DbContext` を一時的として登録します。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-280">Using dependency injection, this can be achieved by either registering the context as scoped, and creating scopes (using `IServiceScopeFactory`) for each thread, or by registering the `DbContext` as transient (using the overload of `AddDbContext` which takes a `ServiceLifetime` parameter).</span></span>

## <a name="more-reading"></a><span data-ttu-id="7d2c2-281">関連項目</span><span class="sxs-lookup"><span data-stu-id="7d2c2-281">More reading</span></span>

- <span data-ttu-id="7d2c2-282">DI の使用の詳細については、[依存関係の挿入](/aspnet/core/fundamentals/dependency-injection)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-282">Read [Dependency Injection](/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
- <span data-ttu-id="7d2c2-283">テストの詳細については、[テスト](xref:core/testing/index)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="7d2c2-283">Read [Testing](xref:core/testing/index) for more information.</span></span>
