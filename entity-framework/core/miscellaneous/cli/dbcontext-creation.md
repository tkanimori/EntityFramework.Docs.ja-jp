---
title: "デザイン時 DbContext 作成 - EF コア"
author: bricelam
ms.author: bricelam
ms.date: 10/27/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 5fcd9e362d76127e7acadd9e552ef3ac90967a37
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
<a name="design-time-dbcontext-creation"></a><span data-ttu-id="2f6cf-102">デザイン時 DbContext 作成</span><span class="sxs-lookup"><span data-stu-id="2f6cf-102">Design-time DbContext Creation</span></span>
==============================
<span data-ttu-id="2f6cf-103">コマンドには、デザイン時に作成 DbContext インスタンスが必要がある、EF ツールの一部は時間 (たとえば、移行のコマンドを実行している) 場合。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-103">Some of the EF Tools commands require a DbContext instance to be created at design time (for example, when running Migrations commands).</span></span> <span data-ttu-id="2f6cf-104">ツールを作成しようとするさまざまな方法があります。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-104">There are various ways the tools try to create it.</span></span>

<a name="from-application-services"></a><span data-ttu-id="2f6cf-105">アプリケーション サービスから</span><span class="sxs-lookup"><span data-stu-id="2f6cf-105">From application services</span></span>
-------------------------
<span data-ttu-id="2f6cf-106">スタートアップ プロジェクトが ASP.NET Core アプリケーションの場合は、ツールは、アプリケーションのサービス プロバイダーから DbContext オブジェクトを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-106">If your startup project is an ASP.NET Core app, the tools try to obtain the DbContext object from the application's service provider.</span></span> <span data-ttu-id="2f6cf-107">呼び出すことによって取得する`Program.BuildWebHost()`にアクセスして、`IWebHost.Services`プロパティです。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-107">They obtain it by invoking `Program.BuildWebHost()` and accessing the `IWebHost.Services` property.</span></span> <span data-ttu-id="2f6cf-108">使用して、DbContext 登録`IServiceCollection.AddDbContext<TContext>()`見つかった、この方法で作成されたことができます。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-108">Any DbContext registered using `IServiceCollection.AddDbContext<TContext>()` can be found and created this way.</span></span> <span data-ttu-id="2f6cf-109">このパターンが[ASP.NET Core 2.0 で導入されました][1]</span><span class="sxs-lookup"><span data-stu-id="2f6cf-109">This pattern was [introduced in ASP.NET Core 2.0][1]</span></span>

<a name="using-the-default-constructor"></a><span data-ttu-id="2f6cf-110">既定のコンス トラクターを使用します。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-110">Using the default constructor</span></span>
-----------------------------
<span data-ttu-id="2f6cf-111">DbContext をアプリケーション サービス プロバイダーから取得できない場合、ツールは、プロジェクト内、DbContext 型を探します。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-111">If the DbContext can't be obtained from the application service provider, the tools look for the DbContext type inside the project.</span></span> <span data-ttu-id="2f6cf-112">既定のコンス トラクターを使用して作成しようとするとします。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-112">They try to create it using its default constructor.</span></span>

<a name="from-a-design-time-factory"></a><span data-ttu-id="2f6cf-113">デザイン時のファクトリから</span><span class="sxs-lookup"><span data-stu-id="2f6cf-113">From a design-time factory</span></span>
--------------------------
<span data-ttu-id="2f6cf-114">見分けることができます、ツールを実装することによって、DbContext を作成する方法`IDesignTimeDbContextFactory`です。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-114">You can also tell the tools how to create your DbContext by implementing `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="2f6cf-115">このインターフェイスを実装するクラスがプロジェクト内で見つかった場合、ツールは DbContext を作成するその他の方法をバイパスします。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-115">If a class implementing this interface is found inside your project, the tools bypass the other ways of creating the DbContext.</span></span>
<span data-ttu-id="2f6cf-116">常に、これらは、デザイン時に、ファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-116">They always use the factory at design time.</span></span> <span data-ttu-id="2f6cf-117">ファクトリは、ランタイムではなくデザイン時の DbContext を異なる方法で構成する必要がある場合に特に便利です。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-117">A factory is especially useful if you need to configure the DbContext differently for design time than at runtime.</span></span>

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

> [!NOTE]
> <span data-ttu-id="2f6cf-118">`args`パラメーターは現在使用されていません。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-118">The `args` parameter is currently unused.</span></span> <span data-ttu-id="2f6cf-119">ある[問題][ 2]追跡ツールからのデザイン時の引数を指定する機能。</span><span class="sxs-lookup"><span data-stu-id="2f6cf-119">There is [an issue][2] tracking the ability to specify design-time arguments from the tools.</span></span>

  [1]: https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/#update-main-method-in-programcs
  [2]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
