---
title: DbContext の有効期間、構成、および初期化 - EF Core
description: 依存関係の挿入がある場合とない場合の DbContext インスタンスを作成および管理するパターン
author: ajcvickers
ms.date: 11/07/2020
uid: core/dbcontext-configuration/index
ms.openlocfilehash: 5b3143edbcfb82312b8026fb09c96dac85427831
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983522"
---
# <a name="dbcontext-lifetime-configuration-and-initialization"></a>DbContext の有効期間、構成、および初期化

この記事では、<xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスの初期化と構成の基本的なパターンについて説明します。

## <a name="the-dbcontext-lifetime"></a>DbContext の有効期間

`DbContext` の有効期間は、インスタンスが作成された時点で開始し、インスタンスが[破棄された](/dotnet/standard/garbage-collection/unmanaged)時点で終了します。 `DbContext` インスタンスは、"_単一_" の [作業単位](https://www.martinfowler.com/eaaCatalog/unitOfWork.html)で使用するように設計されています。 これは、`DbContext` インスタンスの有効期間が通常は非常に短いことを意味します。

> [!TIP]
> 上のリンクから Martin Fowler 氏の言葉を引用します。「作業単位によって、データベースに影響する可能性があるビジネス トランザクションの間に実行した処理がすべて追跡されます。 完了時に、作業の結果としてデータベースを変更するために何を実行する必要があるかがすべて示されます。」

Entity Framework Core (EF Core) を使用する場合の一般的な作業単位には、次のものがあります。

- `DbContext` インスタンスが作成されます。
- コンテキストによってエンティティ インスタンスが追跡されます。 エンティティは次の場合に追跡されます。
  - [クエリから返された](xref:core/querying/tracking)場合
  - [コンテキストに追加またはアタッチされた](xref:core/saving/disconnected-entities)場合
- ビジネス ルールを実装するために、必要に応じて追跡対象エンティティに変更が加えられます。
- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> または <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> が呼び出されます。 行われた変更が EF Core によって検出されてデータベースに書き込まれます。
- `DbContext` インスタンスが破棄されます。

> [!IMPORTANT]
>
> - <xref:Microsoft.EntityFrameworkCore.DbContext> を使用後に破棄することは、非常に重要です。 これにより、インスタンスが参照されたままの場合のメモリ リークを防ぐため、すべてのアンマネージド リソースが解放されることと、すべてのイベントまたは他のフックが登録解除されることの両方が確実に行われます。
> - [DbContext は **スレッドセーフではありません**](#avoiding-dbcontext-threading-issues)。 スレッド間でコンテキストを共有しないでください。 コンテキスト インスタンスの使用を継続する前に、すべての非同期呼び出しを必ず[待機](/dotnet/csharp/language-reference/operators/await)するようにしてください。
> - EF Core コードによってスローされた <xref:System.InvalidOperationException> によって、コンテキストが回復不能な状態になる可能性があります。 このような例外はプログラムのエラーを示すものであり、回復が行われるようには設計されていません。

## <a name="dbcontext-in-dependency-injection-for-aspnet-core"></a>ASP.NET Core の依存関係の挿入における DbContext

多くの Web アプリケーションでは、各 HTTP 要求は 1 つの作業単位に対応します。 このため、コンテキストの有効期間を要求のものに結び付けることが、Web アプリケーションの適切な既定値になります。

ASP.NET Core アプリケーションは、[依存関係の挿入を使用して構成されます](/aspnet/core/fundamentals/startup)。 EF Core は、`Startup.cs` の [`ConfigureServices`](/aspnet/core/fundamentals/startup#the-configureservices-method) メソッド内で <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> を使用することで、この構成に追加することができます。 次に例を示します。

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddControllers();

            services.AddDbContext<ApplicationDbContext>(
                options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/Startup.cs?name=ConfigureServices)]

この例では、`ApplicationDbContext` と呼ばれる `DbContext` サブクラスが、ASP.NET Core アプリケーション サービス プロバイダーにスコープ サービスとして登録されます (または 依存関係の挿入コントローラーとも呼ばれます)。 コンテキストは SQL Server データベース プロバイダーを使用するように構成されます。接続文字列は ASP.NET Core 構成から読み取られます。 通常、`AddDbContext` の呼び出しが `ConfigureServices` の "_どこで_" 行われるかは問題にはなりません。

`ApplicationDbContext` クラスによって、`DbContextOptions<ApplicationDbContext>` パラメーターが指定されたパブリック コンストラクターが公開される必要があります。 これは、`AddDbContext` からのコンテキスト構成を `DbContext` に渡す方法です。 次に例を示します。

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

`ApplicationDbContext` はその後、コンストラクターの挿入を介して、ASP.NET Core コントローラーまたはその他のサービスで使用できるようになります。 次に例を示します。

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

最終的な結果は、要求ごとに作成され、要求の終了時に破棄される前に作業単位を実行するためにコントローラーに渡される、`ApplicationDbContext` インスタンスです。

構成オプションの詳細については、この記事を読み進めてください。 また、ASP.NET Core の構成と依存関係の挿入の詳細については、「[ASP.NET Core でのアプリケーションのスタートアップ](/aspnet/core/fundamentals/startup)」と「[ASP.NET Core での依存関係の挿入](/aspnet/core/fundamentals/dependency-injection)」を参照してください。

<!-- See also [Using Dependency Injection](TODO) for advanced dependency injection configuration with EF Core. -->

## <a name="simple-dbcontext-initialization-with-new"></a>'new' の使用による単純な DbContext の初期化

`DbContext` インスタンスは、たとえば C# の `new` を使用するなど、通常の .NET 方式で構築できます。 構成を実行するには、`OnConfiguring` メソッドをオーバーライドするか、またはコンストラクターにオプションを渡します。 次に例を示します。

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

また、このパターンを使用すると、`DbContext` コンストラクターを介して接続文字列のような構成を簡単に渡すことができます。 次に例を示します。

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

または、`DbContextOptionsBuilder` を使用して `DbContextOptions` オブジェクトを作成し、それを `DbContext` コンストラクターに渡すこともできます。 これにより、依存関係の挿入用に構成された `DbContext` も明示的に構築することができます。 たとえば、上述の ASP.NET Core Web アプリ用に定義された `ApplicationDbContext` を使用する場合は、次のようになります。

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

`DbContextOptions` を作成し、コンストラクターを明示的に呼び出すことができます。

<!--
            var contextOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
                .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test")
                .Options;

            using var context = new ApplicationDbContext(contextOptions);
-->
[!code-csharp[UseNewForWebApp](../../../samples/core/Miscellaneous/ConfiguringDbContext/WebApp/UseNewForWebApp.cs?name=UseNewForWebApp)]

## <a name="using-a-dbcontext-factory-eg-for-blazor"></a>DbContext ファクトリの使用 (例: Blazor)

一部のアプリケーションの種類 ([ASP.NET Core Blazor](/aspnet/core/blazor/) など) では、依存関係の挿入が使用されますが、目的の `DbContext` の有効期間に合わせたサービス スコープが作成されるわけではありません。 このような調整が行われる場合であっても、このスコープ内で複数の作業単位がアプリケーションによって実行されなければならない場合があります。 たとえば、1 つの HTTP 要求内に複数の作業単位がある場合です。

このような場合、<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextFactory%2A> を使用して、`DbContext` インスタンスを作成するためのファクトリを登録できます。 次に例を示します。

<!--
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddDbContextFactory<ApplicationDbContext>(options =>
                options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
        }
-->
[!code-csharp[ConfigureServices](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/FactoryServicesExample.cs?name=ConfigureServices)]

`ApplicationDbContext` クラスによって、`DbContextOptions<ApplicationDbContext>` パラメーターが指定されたパブリック コンストラクターが公開される必要があります。 これは、上述の従来の ASP.NET Core に関するセクションで使用したパターンと同じものです。

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

`DbContextFactory` ファクトリはその後、コンストラクターの挿入を介して他のサービスで使用できるようになります。 次に例を示します。

<!--
        private readonly IDbContextFactory<ApplicationDbContext> _contextFactory;

        public MyController(IDbContextFactory<ApplicationDbContext> contextFactory)
        {
            _contextFactory = contextFactory;
        }
-->
[!code-csharp[Construct](../../../samples/core/Miscellaneous/ConfiguringDbContext/WithContextFactory/MyController.cs?name=Construct)]

挿入されたファクトリはその後、サービス コード内で DbContext インスタンスを構築するために使用できます。 次に例を示します。

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

なお、この方法で作成された `DbContext` インスタンスは、アプリケーションのサービス プロバイダーには管理 "**されず**"、従ってアプリケーションによって破棄される必要があります。

Blazor での EF Core の使用に関する詳細については、「[ASP.NET Core Blazor Server と Entity Framework Core](/aspnet/core/blazor/blazor-server-ef-core)」を参照してください。

## <a name="dbcontextoptions"></a>DbContextOptions

すべての `DbContext` 構成の開始地点は、<xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder> です。 このビルダーを入手するには、次の 3 つの方法があります。

- `AddDbContext` および関連するメソッド
- `OnConfiguring`
- `new`の使用による明示的な構築

これらのそれぞれの例については、これまでのセクションに示されています。 ビルダーの取得元に関係なく、同じ構成を適用できます。 また、コンテキストの構築方法に関係なく、常に `OnConfiguring` が呼び出されます。 これは、`AddDbContext` が使用されている場合でも、`OnConfiguring` を使用して追加の構成を実行できることを意味します。

### <a name="configuring-the-database-provider"></a>データベースプロバイダーの構成

各 `DbContext` インスタンスは、1 つのデータベース プロバイダーのみを使用するように構成する必要があります (`DbContext` サブタイプの異なるインスタンスは、異なるデータベース プロバイダーで使用できますが、1 つのインスタンスで使用できるのは 1 つだけです)。データベース プロバイダーは、特定の `Use*`" 呼び出しを使用して構成されます。 たとえば、SQL Server データベース プロバイダーを使用するには、次のようにします。

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

これらの `Use*`" メソッドは、データベース プロバイダーによって実装される拡張メソッドです。 これは、拡張メソッドを使用する前に、データベース プロバイダーの NuGet パッケージをインストールする必要があることを意味します。

> [!TIP]
> [拡張メソッド](/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)は EF Core データベース プロバイダーによって広く使用されます。 メソッドが見つからないことがコンパイラによって示された場合は、プロバイダーの NuGet パッケージがインストールされていることと、コードに `using Microsoft.EntityFrameworkCore;` が記述されていることを確認してください。

次の表に、一般的なデータベース プロバイダーの例を示します。

| データベース システム              | 構成例                         | NuGet パッケージ
|:-----------------------------|-----------------------------------------------|--------------
| SQL Server または Azure SQL      | `.UseSqlServer(connectionString)`             | [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)
| Azure Cosmos DB              | `.UseCosmos(connectionString, databaseName)`  | [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)
| SQLite                       | `.UseSqlite(connectionString)`                | [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)
| EF Core In-Memory データベース   | `.UseInMemoryDatabase(databaseName)`          | [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)
| PostgreSQL*                  | `.UseNpgsql(connectionString)`                | [Npgsql.EntityFrameworkCore.PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL/)
| MySQL/MariaDB*               | `.UseMySql((connectionString)`                | [Pomelo.EntityFrameworkCore.MySql](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql/)
| Oracle*                      | `.UseOracle(connectionString)`                | [Oracle.EntityFrameworkCore](https://www.nuget.org/packages/Oracle.EntityFrameworkCore/)

\* Microsoft はこれらのデータベース プロバイダーを出荷していません。 データベース プロバイダーの詳細については、「[データベース プロバイダー](xref:core/providers/index)」を参照してください。

> [!WARNING]
> EF Core インメモリ データベースは、運用環境で使用するように設計されていません。 また、テスト用にも最適な選択肢ではない場合があります。 詳細については、「[EF Core を使用するコードのテスト](xref:core/testing/index)」を参照してください。

EF Core で接続文字列を使用する方法の詳細については、「[接続文字列](xref:core/miscellaneous/connection-strings)」を参照してください。

データベース プロバイダーに固有の省略可能な構成は、追加のプロバイダー固有のビルダーで実行されます。 たとえば、<xref:Microsoft.EntityFrameworkCore.Infrastructure.SqlServerDbContextOptionsBuilder.EnableRetryOnFailure%2A> を使用して、Azure SQL に接続するときに接続の回復性の再試行を構成します。

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
> SQL Server と Azure SQL には、同じデータベース プロバイダーが使用されます。 ただし、SQL Azure に接続するときは[接続の回復性](xref:core/miscellaneous/connection-resiliency)を使用することをお勧めします。

プロバイダー固有の構成の詳細については、「[データベース プロバイダー](xref:core/providers/index)」を参照してください。

### <a name="other-dbcontext-configuration"></a>その他の DbContext の構成

その他の `DbContext` の構成は、`Use*` 呼び出しの前または後のいずれかにチェーンすることができます (どちらでも違いはありません)。 たとえば、機密データのログ記録を有効にするには、次のようにします。

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

次の表に、`DbContextOptionsBuilder` で呼び出される一般的なメソッドの例を示します。

| DbContextOptionsBuilder メソッド                                                             | 実行内容                                                | 詳細情報
|:-------------------------------------------------------------------------------------------|-------------------------------------------------------------|--------------
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseQueryTrackingBehavior%2A>   | クエリの既定の追跡動作が設定されます              | [クエリの追跡動作](xref:core/querying/tracking)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A>                      | EF Core ログを取得するシンプルな方法 (EF Core 5.0 以降)    | [ログ、イベント、診断](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.UseLoggerFactory%2A>           | `Microsoft.Extensions.Logging` ファクトリが登録されます         | [ログ、イベント、診断](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> | 例外とログにアプリケーション データが含められます         | [ログ、イベント、診断](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableDetailedErrors%2A>       | より詳細なクエリ エラー (パフォーマンスの低下と引き換え)  | [ログ、イベント、診断](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.ConfigureWarnings%2A>          | 警告やその他のイベントが無視またはスローされます               | [ログ、イベント、診断](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.AddInterceptors%2A>            | EF Core インターセプターが登録されます                              | [ログ、イベント、診断](xref:core/logging-events-diagnostics/index)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A>            | 遅延読み込みのために動的プロキシが使用されます                        | [遅延読み込み](xref:core/querying/related-data/lazy)
| <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A>         | 変更追跡のために動的プロキシが使用されます                     | 近日公開予定...

> [!NOTE]
> <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies%2A> と <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> は、[Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) NuGet パッケージの拡張メソッドです。 この種の ".UseXXX()" 呼び出しは、他のパッケージに含まれている EF Core の拡張機能を構成または使用するための推奨される方法です。

### <a name="dbcontextoptions-versus-dbcontextoptionstcontext"></a>`DbContextOptions` と `DbContextOptions<TContext>`

`DbContextOptions` を受け入れるほとんどの `DbContext` サブクラスでは、[ジェネリック](/dotnet/csharp/programming-guide/generics/)の `DbContextOptions<TContext>` バリエーションを使用する必要があります。 次に例を示します。

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

これにより、複数の `DbContext` サブタイプが登録されている場合でも、特定の `DbContext` サブタイプの正しいオプションが依存関係の挿入から解決されるようになります。

> [!TIP]
> DbContext はシールされる必要はありませんが、シールは継承するように設計されていないクラスに関してそうするためのベスト プラクティスです。

ただし、`DbContext` サブタイプ自体が継承するよう意図されている場合は、非ジェネリックの `DbContextOptions` を受け取る保護されたコンストラクターを公開する必要があります。 次に例を示します。

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

これにより、複数の具象サブクラスが、それぞれの異なるジェネリックの `DbContextOptions<TContext>` インスタンスを使用してこの基本コンストラクターを呼び出すことができるようになります。 次に例を示します。

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

これは、`DbContext` から直接継承する場合とまったく同じパターンであることに注目してください。 つまり、これが理由で、非ジェネリックの `DbContextOptions` が `DbContext` コンストラクター自体によって受け入れられます。

インスタンス化されることと継承されることの両方が意図されている `DbContext` サブクラスの場合、両方の形式のコンストラクターを公開する必要があります。 次に例を示します。

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

## <a name="design-time-dbcontext-configuration"></a>デザイン時の DbContext の構成

[EF Core 移行](xref:core/managing-schemas/migrations/index)用などの EF Core デザイン時ツールでは、アプリケーションのエンティティ型とそれらがデータベース スキーマにどのようにマップされるかに関する詳細情報を収集するため、`DbContext` 型の作業インスタンスを検出および作成できる必要があります。 このプロセスは、実行時に構成される場合と同様の方法で構成されるように `DbContext` をツールで簡単に作成できる限り、自動的に行われます。

`DbContext` に必要な構成情報を提供するパターンは実行時に動作しますが、デザイン時に `DbContext` を使用する必要があるツールは、限られた数のパターンでのみ機能します。 これらについては、[デザイン時のコンテキストの作成](xref:core/cli/dbcontext-creation)に関する記事で詳しく説明されています。

## <a name="avoiding-dbcontext-threading-issues"></a>DbContext のスレッド処理の問題を回避する

Entity Framework Core では、同じ `DbContext` インスタンス上での複数の並列操作の実行がサポートされていません。 これには、非同期クエリの並列実行と、複数のスレッドからの明示的な同時使用の両方が含まれます。 そのため、常に非同期呼び出しを直ちに `await` するか、並列実行される操作に対して個別の `DbContext` インスタンスを使用します。

`DbContext` インスタンスを同時に使用しようとしたことが EF Core によって検出されると、次のようなメッセージとともに `InvalidOperationException` が表示されます。

> 前の操作が完了する前に、このコンテキストで 2 つ目の操作が開始されました。 これは通常、異なるスレッドで DbContext の同じインスタンスが使用された場合に発生しますが、インスタンスのメンバーがスレッド セーフである保証はありません。

同時アクセスが検出されない場合は、未定義の動作、アプリケーションのクラッシュ、データの破損が発生する可能性があります。

同じ `DbContext` インスタンスに誤って同時にアクセスする原因として、一般に次のような間違いがあります。

### <a name="asynchronous-operation-pitfalls"></a>非同期操作の落とし穴

EF Core で非同期メソッドを使用すると、非ブロッキング モードでデータベースにアクセスする操作を開始できます。 しかし、呼び出し元がこれらのメソッドのいずれかの完了を待機せず、`DbContext` に対して他の操作を実行しようとした場合、`DbContext` の状態は (非常に高い確率で) 破損する可能性があります。

常に EF Core の非同期メソッドを直ちに待機してください。

### <a name="implicitly-sharing-dbcontext-instances-via-dependency-injection"></a>依存関係の挿入によって DbContext インスタンスを暗黙的に共有する

[`AddDbContext`](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) 拡張メソッドを使用すると、[スコープ付きの有効期間](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)で `DbContext` 型が登録されます。

この場合、ある時点で個々のクライアント要求を実行するスレッドは 1 つしかなく、各要求が別個の依存関係挿入スコープ (したがって、別個の `DbContext` インスタンス) を取得するため、ほとんどの ASP.NET Core アプリケーションでは同時アクセスの問題は発生しません。 Blazor Server ホスティング モデルでは、Blazor ユーザー回線を維持するために 1 つの論理要求が使用されるため、既定の挿入スコープが使用されている場合は、ユーザー回線ごとにスコープ付きの DbContext インスタンスを 1 つだけ使用できます。

複数のスレッドを明示的に並列実行するコードでは、`DbContext` インスタンスが同時にアクセスされないようにする必要があります。

依存関係の挿入を使用してこれを実現するには、コンテキストをスコープ付きとして登録し、スレッドごとに (`IServiceScopeFactory` を使用して) スコープを作成するか、または (`ServiceLifetime` パラメーターを取る `AddDbContext` のオーバーロードを使用して) `DbContext` を一時的として登録します。

## <a name="more-reading"></a>関連項目

- DI の使用の詳細については、[依存関係の挿入](/aspnet/core/fundamentals/dependency-injection)に関する記事をご覧ください。
- テストの詳細については、[テスト](xref:core/testing/index)に関する記事をご覧ください。
