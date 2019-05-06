---
title: デザイン時 DbContext 作成 - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/27/2017
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 66fec7605b6ac2da0af1e801f8a1dca0789aea35
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993719"
---
<a name="design-time-dbcontext-creation"></a><span data-ttu-id="69462-102">デザイン時 DbContext 作成</span><span class="sxs-lookup"><span data-stu-id="69462-102">Design-time DbContext Creation</span></span>
==============================
<span data-ttu-id="69462-103">EF Core ツールのコマンドの一部 (たとえば、[移行][ 1]コマンド) 派生を必要と`DbContext`アプリケーションの詳細を収集するためにデザイン時に作成されるインスタンスエンティティ型とデータベース スキーマへの割り当て方法。</span><span class="sxs-lookup"><span data-stu-id="69462-103">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="69462-104">ほとんどの場合は望ましいを`DbContext`作成されたことがどのようになりますと同様の方法で構成されている[実行時に構成されている][2]。</span><span class="sxs-lookup"><span data-stu-id="69462-104">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="69462-105">さまざまな方法で作成しようとしているツール、 `DbContext`:</span><span class="sxs-lookup"><span data-stu-id="69462-105">There are various ways the tools try to create the `DbContext`:</span></span>

<a name="from-application-services"></a><span data-ttu-id="69462-106">アプリケーション サービスから</span><span class="sxs-lookup"><span data-stu-id="69462-106">From application services</span></span>
-------------------------
<span data-ttu-id="69462-107">ASP.NET Core アプリをスタートアップ プロジェクトには、ツールは、アプリケーションのサービス プロバイダーから DbContext オブジェクトを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="69462-107">If your startup project is an ASP.NET Core app, the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="69462-108">呼び出すことによって、サービス プロバイダーを取得するツールがまず`Program.BuildWebHost()`にアクセスして、`IWebHost.Services`プロパティ。</span><span class="sxs-lookup"><span data-stu-id="69462-108">The tools first try to obtain the service provider by invoking `Program.BuildWebHost()` and accessing the `IWebHost.Services` property.</span></span>

> [!NOTE]
> <span data-ttu-id="69462-109">新しい ASP.NET Core 2.0 アプリケーションを作成するときに、このフックは既定で含まれています。</span><span class="sxs-lookup"><span data-stu-id="69462-109">When you create a new ASP.NET Core 2.0 application, this hook is included by default.</span></span> <span data-ttu-id="69462-110">EF Core と ASP.NET Core の以前のバージョンでツールが起動しよう`Startup.ConfigureServices`不要になったアプリケーションのサービスのプロバイダーがこのパターンを取得するために正常に ASP.NET Core 2.0 アプリケーションで直接します。</span><span class="sxs-lookup"><span data-stu-id="69462-110">In previous versions of EF Core and ASP.NET Core, the tools try to invoke `Startup.ConfigureServices` directly in order to obtain the application's service provider, but this pattern no longer works correctly in ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="69462-111">ASP.NET Core 1.x アプリケーションを 2.0 にアップグレードする場合は、[変更、`Program`新しいパターンに従うクラス][3]します。</span><span class="sxs-lookup"><span data-stu-id="69462-111">If you are upgrading an ASP.NET Core 1.x application to 2.0, you can [modify your `Program` class to follow the new pattern][3].</span></span>

<span data-ttu-id="69462-112">`DbContext`自体、およびそのコンストラクターで依存関係は、アプリケーションのサービス プロバイダーのサービスとして登録されている必要があります。</span><span class="sxs-lookup"><span data-stu-id="69462-112">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="69462-113">ことによって簡単に達成この[コンストラクター、`DbContext`のインスタンスを受け取る`DbContextOptions<TContext>`を引数として][ 4]を使用して、 [ `AddDbContext<TContext>` メソッド][5].</span><span class="sxs-lookup"><span data-stu-id="69462-113">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][4] and using the [`AddDbContext<TContext>` method][5].</span></span>

<a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="69462-114">パラメーターなしのコンストラクターを使用します。</span><span class="sxs-lookup"><span data-stu-id="69462-114">Using a constructor with no parameters</span></span>
--------------------------------------
<span data-ttu-id="69462-115">DbContext をアプリケーションのサービス プロバイダーから取得できない場合、ツールを探して、派生`DbContext`プロジェクト内の型。</span><span class="sxs-lookup"><span data-stu-id="69462-115">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="69462-116">パラメーターなしのコンストラクターを使用してインスタンスを作成、再試行してください。</span><span class="sxs-lookup"><span data-stu-id="69462-116">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="69462-117">場合、既定のコンストラクターができます、`DbContext`を使用して、構成、 [ `OnConfiguring` ] [ 6]メソッド。</span><span class="sxs-lookup"><span data-stu-id="69462-117">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][6] method.</span></span>

<a name="from-a-design-time-factory"></a><span data-ttu-id="69462-118">デザイン時のファクトリから</span><span class="sxs-lookup"><span data-stu-id="69462-118">From a design-time factory</span></span>
--------------------------
<span data-ttu-id="69462-119">見分けることができます、ツールを実装することで、DbContext を作成する方法、`IDesignTimeDbContextFactory<TContext>`インターフェイス: このインターフェイスを実装するクラスを派生と同じプロジェクトのいずれかで見つかった場合`DbContext`またはアプリケーションのスタートアップ プロジェクトで、ツールのバイパス他の方法の代わりに、DbContext と使用、デザイン時のファクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="69462-119">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

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
> <span data-ttu-id="69462-120">`args`パラメーターは現在使用されていません。</span><span class="sxs-lookup"><span data-stu-id="69462-120">The `args` parameter is currently unused.</span></span> <span data-ttu-id="69462-121">ある[問題][ 7]追跡ツールからのデザイン時の引数を指定する機能。</span><span class="sxs-lookup"><span data-stu-id="69462-121">There is [an issue][7] tracking the ability to specify design-time arguments from the tools.</span></span>

<span data-ttu-id="69462-122">デザイン時のファクトリは、場合、実行時によりも、デザイン時の異なる方法で DbContext を構成する必要がある場合に特に役立ちます、 `DbContext` DI をまったく使用しない場合に、追加のパラメーターは、DI に登録されていないコンストラクターがまたはのいくつかの場合理由を行わない、`BuildWebHost`メソッドで、ASP.NET Core アプリケーションの`Main`クラス。</span><span class="sxs-lookup"><span data-stu-id="69462-122">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's `Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/#update-main-method-in-programcs
  [4]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [5]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [6]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [7]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
