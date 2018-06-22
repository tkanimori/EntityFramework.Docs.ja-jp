---
title: DbContext - EF Core の構成
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
ms.technology: entity-framework-core
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 6980acd53b0a74055af7a1e04b476f4625c327c9
ms.sourcegitcommit: d2434edbfa6fbcee7287e33b4915033b796e417e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2018
ms.locfileid: "29152391"
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="0fbc1-102">DbContext を構成します。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-102">Configuring a DbContext</span></span>

<span data-ttu-id="0fbc1-103">この記事の内容を構成するための基本的なパターンを示しています、`DbContext`を介して、`DbContextOptions`特定 EF コア プロバイダーと省略可能な動作を使用してデータベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-103">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="0fbc1-104">デザイン時 DbContext 構成</span><span class="sxs-lookup"><span data-stu-id="0fbc1-104">Design-time DbContext configuration</span></span>

<span data-ttu-id="0fbc1-105">などの EF コア デザイン時ツール[移行](xref:core/managing-schemas/migrations/index)を検出しての作業のインスタンスを作成する必要がある、`DbContext`型アプリケーションのエンティティ型とデータベース スキーマへのマップ方法に関する詳細情報を収集するためにします。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-105">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="0fbc1-106">ツールを簡単に作成できる限り、このプロセスは自動可能、`DbContext`をそれが構成されます同様に実行時の構成方法とするようにします。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-106">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at run-time.</span></span>

<span data-ttu-id="0fbc1-107">必要な構成情報を提供する任意のパターンの中に、`DbContext`ランタイムの使用を必要とするツールで作業ができ、`DbContext`デザイン時にのみ使用できますパターンの数に制限します。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-107">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="0fbc1-108">これらについては説明でより詳しく、[コンテキストの作成にデザイン時](xref:core/miscellaneous/cli/dbcontext-creation)セクションです。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-108">These are covered in more detail in the [Design-Time Context Creation](xref:core/miscellaneous/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="0fbc1-109">DbContextOptions を構成します。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-109">Configuring DbContextOptions</span></span>

<span data-ttu-id="0fbc1-110">`DbContext` インスタンスがあります`DbContextOptions`のすべての作業を実行します。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-110">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="0fbc1-111">`DbContextOptions`インスタンスなどの構成情報を実行します。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-111">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="0fbc1-112">データベース プロバイダーを使用するのには、通常などのメソッドを呼び出すことによって選択`UseSqlServer`または `UseSqlite`</span><span class="sxs-lookup"><span data-stu-id="0fbc1-112">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`</span></span>
- <span data-ttu-id="0fbc1-113">任意の必要な接続文字列またはデータベースのインスタンスの識別子通常に渡される引数として上記プロバイダーの選択メソッド</span><span class="sxs-lookup"><span data-stu-id="0fbc1-113">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="0fbc1-114">通常、プロバイダーの選択メソッドの呼び出しの内部チェーンも、任意のプロバイダー レベル オプションの動作セレクター</span><span class="sxs-lookup"><span data-stu-id="0fbc1-114">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="0fbc1-115">通常チェーン プロバイダー セレクター メソッドは前に、または後に [全般]、EF コア動作セレクター</span><span class="sxs-lookup"><span data-stu-id="0fbc1-115">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="0fbc1-116">次の例では、構成、`DbContextOptions`に使用するには、SQL Server プロバイダーの接続が含まれている、`connectionString`変数、プロバイダ レベルのコマンドのタイムアウトとで実行されるすべてのクエリを使用する EF コア動作セレクター、 `DbContext`[いいえ追跡](xref:core/querying/tracking#no-tracking-queries)既定では。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-116">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> <span data-ttu-id="0fbc1-117">プロバイダー セレクター メソッドとその他の動作セレクター メソッド上で説明したは、拡張メソッドで`DbContextOptions`またはプロバイダーに固有のオプション クラス。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-117">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="0fbc1-118">これらの拡張メソッドが、名前空間が存在する必要がありますにアクセスするために (通常`Microsoft.EntityFrameworkCore`) のスコープを設定して、プロジェクトに追加のパッケージの依存関係を含めます。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-118">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="0fbc1-119">`DbContextOptions`を指定すると、`DbContext`オーバーライドすることで、`OnConfiguring`メソッドまたはコンス トラクターの引数を使用して外部的です。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-119">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="0fbc1-120">両方を使用すると場合、`OnConfiguring`が最後に適用され、コンス トラクター引数を指定したオプションを上書きすることができます。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-120">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="0fbc1-121">コンス トラクターの引数</span><span class="sxs-lookup"><span data-stu-id="0fbc1-121">Constructor argument</span></span>

<span data-ttu-id="0fbc1-122">コンス トラクターを持つコンテキスト コード:</span><span class="sxs-lookup"><span data-stu-id="0fbc1-122">Context code with constructor:</span></span>

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
> <span data-ttu-id="0fbc1-123">DbContext の基底コンス トラクターでは、非ジェネリック バージョンも受け入れられる`DbContextOptions`が非ジェネリック バージョンを使用しては複数のコンテキストの型を持つアプリケーションにはお勧めしません。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-123">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="0fbc1-124">コンス トラクターの引数から初期化するためにアプリケーション コード:</span><span class="sxs-lookup"><span data-stu-id="0fbc1-124">Application code to initialize from constructor argument:</span></span>

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="0fbc1-125">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="0fbc1-125">OnConfiguring</span></span>

<span data-ttu-id="0fbc1-126">コンテキストのコードを`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="0fbc1-126">Context code with `OnConfiguring`:</span></span>

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

<span data-ttu-id="0fbc1-127">初期化するためにアプリケーション コード、`DbContext`を使用して`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="0fbc1-127">Application code to initialize a `DbContext` that uses `OnConfiguring`:</span></span>

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="0fbc1-128">このアプローチに適していないテスト、テスト対象のデータベースの完全しない限り、します。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-128">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="0fbc1-129">依存関係の挿入で DbContext の使用</span><span class="sxs-lookup"><span data-stu-id="0fbc1-129">Using DbContext with dependency injection</span></span>

<span data-ttu-id="0fbc1-130">使用して EF コア サポート`DbContext`依存性の注入コンテナーにします。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-130">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="0fbc1-131">使用して、DbContext 型をサービス コンテナーに追加することができます、`AddDbContext<TContext>`メソッドです。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-131">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="0fbc1-132">`AddDbContext<TContext>` 両方、DbContext 型と、`TContext`と、対応する`DbContextOptions<TContext>`サービス コンテナーからの挿入用に使用できます。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-132">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="0fbc1-133">参照してください[読み取り多く](#more-reading)下依存関係の挿入の詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-133">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="0fbc1-134">追加する、`Dbcontext`依存関係の挿入に。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-134">Adding the `Dbcontext` to dependency injection:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="0fbc1-135">追加する必要があります、[コンス トラクター引数](#constructor-argument)を受け入れる、DbContext 型に`DbContextOptions<TContext>`です。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-135">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="0fbc1-136">コンテキスト コードに示します。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-136">Context code:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="0fbc1-137">アプリケーション コードの ASP.NET Core):</span><span class="sxs-lookup"><span data-stu-id="0fbc1-137">Application code (in ASP.NET Core):</span></span>

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

<span data-ttu-id="0fbc1-138">アプリケーション コードが (サービス プロバイダーを直接使用する、一般的な方法で):</span><span class="sxs-lookup"><span data-stu-id="0fbc1-138">Application code (using ServiceProvider directly, less common):</span></span>

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="more-reading"></a><span data-ttu-id="0fbc1-139">複数の読み取り</span><span class="sxs-lookup"><span data-stu-id="0fbc1-139">More reading</span></span>

* <span data-ttu-id="0fbc1-140">読み取り[ASP.NET Core の概要](../get-started/aspnetcore/index.md)EF を ASP.NET Core の使用方法に関する詳細。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-140">Read [Getting Started on ASP.NET Core](../get-started/aspnetcore/index.md) for more information on using EF with ASP.NET Core.</span></span>
* <span data-ttu-id="0fbc1-141">読み取り[依存性の注入](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)を DI の使用に関する詳細を参照してください。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-141">Read [Dependency Injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
* <span data-ttu-id="0fbc1-142">読み取り[テスト](testing/index.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="0fbc1-142">Read [Testing](testing/index.md) for more information.</span></span>
