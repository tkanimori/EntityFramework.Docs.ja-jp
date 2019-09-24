---
title: デザイン時 DbContext の作成-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: f83d4b16227d114a1cac1514667484a908fea4ac
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197578"
---
<a name="design-time-dbcontext-creation"></a><span data-ttu-id="b3e1d-102">デザイン時 DbContext 作成</span><span class="sxs-lookup"><span data-stu-id="b3e1d-102">Design-time DbContext Creation</span></span>
==============================
<span data-ttu-id="b3e1d-103">一部の EF Core ツールコマンド (たとえば、[移行][1]コマンド) では、アプリケーションのエンティティ`DbContext`型に関する詳細情報を収集し、データベーススキーマにマップする方法に基づいて、デザイン時に派生インスタンスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-103">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="b3e1d-104">ほとんどの場合、作成されるは実行`DbContext`時[に構成][2]するのと同様の方法で構成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-104">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="b3e1d-105">ツールでは、さまざまな方法で`DbContext`を作成します。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-105">There are various ways the tools try to create the `DbContext`:</span></span>

<a name="from-application-services"></a><span data-ttu-id="b3e1d-106">アプリケーションサービスから</span><span class="sxs-lookup"><span data-stu-id="b3e1d-106">From application services</span></span>
-------------------------
<span data-ttu-id="b3e1d-107">スタートアッププロジェクトで[ASP.NET Core Web ホスト][3]または[.Net Core 汎用ホスト][4]が使用されている場合、ツールはアプリケーションのサービスプロバイダーから dbcontext オブジェクトを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-107">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="b3e1d-108">ツールは、まず`Program.CreateHostBuilder()`を呼び出し、を呼び出し`Build()`て、 `Services`プロパティにアクセスすることで、サービスプロバイダーを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-108">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

``` csharp
public class Program
{
    public static void Main(string[] args)
        => CreateHostBuilder(args).Build().Run();

    // EF Core uses this method at design time to access the DbContext
    public static IHostBuilder CreateHostBuilder(string[] args)
        => Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(
                webBuilder => webBuilder.UseStartup<Startup>());
}

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
        => services.AddDbContext<ApplicationDbContext>();
}

public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}
```

> [!NOTE]
> <span data-ttu-id="b3e1d-109">新しい ASP.NET Core アプリケーションを作成すると、このフックが既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-109">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="b3e1d-110">自身`DbContext`とそのコンストラクター内の依存関係は、アプリケーションのサービスプロバイダーにサービスとして登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-110">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="b3e1d-111">これは[、の`DbContext` `DbContextOptions<TContext>`インスタンスを引数][5]と[ `AddDbContext<TContext>` ][6]して受け取り、メソッドを使用して、にコンストラクターを設定することによって簡単に実現できます。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-111">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

<a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="b3e1d-112">パラメーターのないコンストラクターを使用する</span><span class="sxs-lookup"><span data-stu-id="b3e1d-112">Using a constructor with no parameters</span></span>
--------------------------------------
<span data-ttu-id="b3e1d-113">Dbcontext をアプリケーションサービスプロバイダーから取得できない場合、ツールはプロジェクト内で派生`DbContext`型を検索します。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-113">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="b3e1d-114">次に、パラメーターのないコンストラクターを使用してインスタンスを作成しようとします。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-114">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="b3e1d-115">`DbContext`が[メソッド`OnConfiguring`][7]を使用して構成されている場合、これは既定のコンストラクターになることがあります。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-115">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

<a name="from-a-design-time-factory"></a><span data-ttu-id="b3e1d-116">デザイン時のファクトリから</span><span class="sxs-lookup"><span data-stu-id="b3e1d-116">From a design-time factory</span></span>
--------------------------
<span data-ttu-id="b3e1d-117">また、 `IDesignTimeDbContextFactory<TContext>`インターフェイスを実装することで、dbcontext を作成する方法をツールに指示することもできます。このインターフェイスを実装しているクラスが、派生`DbContext`プロジェクトまたはアプリケーションのスタートアッププロジェクトと同じプロジェクトに存在する場合、これらのツールは dbcontext を作成する他の方法をバイパスし、代わりにデザイン時のファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-117">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Design;
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

> [!NOTE]
> <span data-ttu-id="b3e1d-118">パラメーター `args`は現在使用されていません。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-118">The `args` parameter is currently unused.</span></span> <span data-ttu-id="b3e1d-119">ツールからデザイン時引数を指定する機能の追跡には[問題][8]があります。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-119">There is [an issue][8] tracking the ability to specify design-time arguments from the tools.</span></span>

<span data-ttu-id="b3e1d-120">デザイン時のファクトリは、実行時とは異なるデザイン時に dbcontext を別の方法で構成する必要がある場合、 `DbContext`コンストラクターが di に登録されていない場合、di を使用していない場合、または理由は、ASP.NET Core アプリケーションの`BuildWebHost` `Main`クラスにメソッドを使用しないことです。</span><span class="sxs-lookup"><span data-stu-id="b3e1d-120">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's `Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
