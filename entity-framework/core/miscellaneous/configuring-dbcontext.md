---
title: "DbContext - EF Core の構成"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
ms.technology: entity-framework-core
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: 96abf3b94be3e1d19f833644f1c2f6f13fe0e730
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="de59e-102">DbContext を構成します。</span><span class="sxs-lookup"><span data-stu-id="de59e-102">Configuring a DbContext</span></span>

<span data-ttu-id="de59e-103">この記事の内容を構成するためのパターンを示しています、`DbContext`で`DbContextOptions`です。</span><span class="sxs-lookup"><span data-stu-id="de59e-103">This article shows patterns for configuring a `DbContext` with `DbContextOptions`.</span></span> <span data-ttu-id="de59e-104">オプションは、データ ストアの構成を選択して、主に使用されます。</span><span class="sxs-lookup"><span data-stu-id="de59e-104">Options are primarily used to select and configure the data store.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="de59e-105">DbContextOptions を構成します。</span><span class="sxs-lookup"><span data-stu-id="de59e-105">Configuring DbContextOptions</span></span>

<span data-ttu-id="de59e-106">`DbContext`インスタンスがあります`DbContextOptions`を実行するためにします。</span><span class="sxs-lookup"><span data-stu-id="de59e-106">`DbContext` must have an instance of `DbContextOptions` in order to execute.</span></span> <span data-ttu-id="de59e-107">これは、オーバーライドすることによって構成できます`OnConfiguring`、またはコンス トラクターの引数を使用して外部的に提供します。</span><span class="sxs-lookup"><span data-stu-id="de59e-107">This can be configured by overriding `OnConfiguring`, or supplied externally via a constructor argument.</span></span>

<span data-ttu-id="de59e-108">両方を使用すると場合、`OnConfiguring`は付加的なものは、指定されたオプションで実行されるコンス トラクター引数を指定したオプションを上書きします。</span><span class="sxs-lookup"><span data-stu-id="de59e-108">If both are used, `OnConfiguring` is executed on the supplied options, meaning it is additive and can overwrite  options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="de59e-109">コンス トラクターの引数</span><span class="sxs-lookup"><span data-stu-id="de59e-109">Constructor argument</span></span>

<span data-ttu-id="de59e-110">コンス トラクターを持つコンテキスト コード</span><span class="sxs-lookup"><span data-stu-id="de59e-110">Context code with constructor</span></span>

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
> <span data-ttu-id="de59e-111">DbContext の基底コンス トラクターでは、非ジェネリック バージョンも受け入れられる`DbContextOptions`です。</span><span class="sxs-lookup"><span data-stu-id="de59e-111">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`.</span></span> <span data-ttu-id="de59e-112">非ジェネリック バージョンを使用して複数のコンテキストの型を持つアプリケーションには推奨されません。</span><span class="sxs-lookup"><span data-stu-id="de59e-112">Using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="de59e-113">コンス トラクターの引数から初期化するためにアプリケーション コード</span><span class="sxs-lookup"><span data-stu-id="de59e-113">Application code to initialize from constructor argument</span></span>

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="de59e-114">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="de59e-114">OnConfiguring</span></span>

> [!WARNING]  
> <span data-ttu-id="de59e-115">`OnConfiguring`最後に発生したし、DI またはコンス トラクターから取得したオプションを上書きすることができます。</span><span class="sxs-lookup"><span data-stu-id="de59e-115">`OnConfiguring` occurs last and can overwrite options obtained from DI or the constructor.</span></span> <span data-ttu-id="de59e-116">この方法では、(ない場合、完全なデータベースが対象) のテストに適していません。</span><span class="sxs-lookup"><span data-stu-id="de59e-116">This approach does not lend itself to testing (unless you target the full database).</span></span>

<span data-ttu-id="de59e-117">コンテキストのコードを`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="de59e-117">Context code with `OnConfiguring`:</span></span>

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

<span data-ttu-id="de59e-118">アプリケーション コードを使用して初期化`OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="de59e-118">Application code to initialize with `OnConfiguring`:</span></span>

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

## <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="de59e-119">依存関係の挿入で DbContext の使用</span><span class="sxs-lookup"><span data-stu-id="de59e-119">Using DbContext with dependency injection</span></span>

<span data-ttu-id="de59e-120">使用して EF サポート`DbContext`依存性の注入コンテナーにします。</span><span class="sxs-lookup"><span data-stu-id="de59e-120">EF supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="de59e-121">使用して、DbContext 型をサービス コンテナーに追加することができます`AddDbContext<TContext>`です。</span><span class="sxs-lookup"><span data-stu-id="de59e-121">Your DbContext type can be added to the service container by using `AddDbContext<TContext>`.</span></span>

<span data-ttu-id="de59e-122">`AddDbContext`両方、DbContext 型と、 `TContext`、および`DbContextOptions<TContext>`サービス コンテナーからの挿入用に使用できます。</span><span class="sxs-lookup"><span data-stu-id="de59e-122">`AddDbContext` will make both your DbContext type, `TContext`, and `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="de59e-123">参照してください[読み取り多く](#more-reading)の下の依存関係の挿入に関する情報。</span><span class="sxs-lookup"><span data-stu-id="de59e-123">See [more reading](#more-reading) below for information on dependency injection.</span></span>

<span data-ttu-id="de59e-124">依存関係の挿入に dbcontext を追加します。</span><span class="sxs-lookup"><span data-stu-id="de59e-124">Adding dbcontext to dependency injection</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="de59e-125">追加する必要があります、[コンス トラクター引数](#constructor-argument)を受け入れる、DbContext 型に`DbContextOptions`です。</span><span class="sxs-lookup"><span data-stu-id="de59e-125">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions`.</span></span>

<span data-ttu-id="de59e-126">コンテキスト コードに示します。</span><span class="sxs-lookup"><span data-stu-id="de59e-126">Context code:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="de59e-127">アプリケーション コードの ASP.NET Core):</span><span class="sxs-lookup"><span data-stu-id="de59e-127">Application code (in ASP.NET Core):</span></span>

``` csharp
public MyController(BloggingContext context)
```

<span data-ttu-id="de59e-128">アプリケーション コードが (サービス プロバイダーを直接使用する、一般的な方法で):</span><span class="sxs-lookup"><span data-stu-id="de59e-128">Application code (using ServiceProvider directly, less common):</span></span>

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="using-idesigntimedbcontextfactorytcontext"></a><span data-ttu-id="de59e-129">使用します。`IDesignTimeDbContextFactory<TContext>`</span><span class="sxs-lookup"><span data-stu-id="de59e-129">Using `IDesignTimeDbContextFactory<TContext>`</span></span>

<span data-ttu-id="de59e-130">上記のオプションを代わりにの実装を指定することも`IDesignTimeDbContextFactory<TContext>`します。</span><span class="sxs-lookup"><span data-stu-id="de59e-130">As an alternative to the options above, you may also provide an implementation of `IDesignTimeDbContextFactory<TContext>`.</span></span> <span data-ttu-id="de59e-131">EF ツールは、このファクトリを使用して、DbContext のインスタンスを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="de59e-131">EF tools can use this factory to create an instance of your DbContext.</span></span> <span data-ttu-id="de59e-132">移行など特定のデザイン時のエクスペリエンスを有効にするために必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="de59e-132">This may be required in order to enable specific design-time experiences such as migrations.</span></span>

<span data-ttu-id="de59e-133">パブリックの既定のコンス トラクターを持たないコンテキスト型のデザイン時のサービスを有効にするには、このインターフェイスを実装します。</span><span class="sxs-lookup"><span data-stu-id="de59e-133">Implement this interface to enable design-time services for context types that do not have a public default constructor.</span></span> <span data-ttu-id="de59e-134">デザイン時のサービスでは、派生のコンテキストと同じアセンブリ内にあるこのインターフェイスの実装を自動的に検出されます。</span><span class="sxs-lookup"><span data-stu-id="de59e-134">Design-time services will automatically discover implementations of this interface that are in the same assembly as the derived context.</span></span>

<span data-ttu-id="de59e-135">例:</span><span class="sxs-lookup"><span data-stu-id="de59e-135">Example:</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Infrastructure;

namespace MyProject
{
    public class BloggingContextFactory : IDesignTimeDbContextFactory<BloggingContext>
    {
        public BloggingContext CreateDbContext(string[] args)
        {
            var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
            optionsBuilder.UseSqlite("Data Source=blog.db");

            return new BloggingContext(optionsBuilder.Options);
        }
    }
}
```

## <a name="more-reading"></a><span data-ttu-id="de59e-136">複数の読み取り</span><span class="sxs-lookup"><span data-stu-id="de59e-136">More reading</span></span>

* <span data-ttu-id="de59e-137">読み取り[ASP.NET Core の概要](../get-started/aspnetcore/index.md)EF を ASP.NET Core の使用方法に関する詳細。</span><span class="sxs-lookup"><span data-stu-id="de59e-137">Read [Getting Started on ASP.NET Core](../get-started/aspnetcore/index.md) for more information on using EF with ASP.NET Core.</span></span>
* <span data-ttu-id="de59e-138">読み取り[依存性の注入](https://docs.asp.net/en/latest/fundamentals/dependency-injection.html)を DI の使用に関する詳細を参照してください。</span><span class="sxs-lookup"><span data-stu-id="de59e-138">Read [Dependency Injection](https://docs.asp.net/en/latest/fundamentals/dependency-injection.html) to learn more about using DI.</span></span>
* <span data-ttu-id="de59e-139">読み取り[テスト](testing/index.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="de59e-139">Read [Testing](testing/index.md) for more information.</span></span>
