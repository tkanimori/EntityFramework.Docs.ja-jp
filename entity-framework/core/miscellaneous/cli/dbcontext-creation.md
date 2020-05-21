---
title: デザイン時 DbContext の作成-EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 0b0271dcabea63a2529c091cc14cb9059d56ac8d
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672965"
---
# <a name="design-time-dbcontext-creation"></a><span data-ttu-id="3104d-102">デザイン時 DbContext 作成</span><span class="sxs-lookup"><span data-stu-id="3104d-102">Design-time DbContext Creation</span></span>

<span data-ttu-id="3104d-103">一部の EF Core ツールコマンド (たとえば、[移行][1]コマンド) では、 `DbContext` アプリケーションのエンティティ型に関する詳細情報を収集し、データベーススキーマにマップする方法に基づいて、デザイン時に派生インスタンスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3104d-103">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="3104d-104">ほとんどの場合、 `DbContext` 作成されるは実行時[に構成][2]するのと同様の方法で構成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="3104d-104">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="3104d-105">ツールでは、さまざまな方法でを作成し `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="3104d-105">There are various ways the tools try to create the `DbContext`:</span></span>

## <a name="from-application-services"></a><span data-ttu-id="3104d-106">アプリケーションサービスから</span><span class="sxs-lookup"><span data-stu-id="3104d-106">From application services</span></span>

<span data-ttu-id="3104d-107">スタートアッププロジェクトで[ASP.NET Core Web ホスト][3]または[.Net Core 汎用ホスト][4]が使用されている場合、ツールはアプリケーションのサービスプロバイダーから dbcontext オブジェクトを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="3104d-107">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="3104d-108">ツールは、まずを呼び出し、を呼び出して、プロパティにアクセスすることで、サービスプロバイダーを取得しようとし `Program.CreateHostBuilder()` `Build()` `Services` ます。</span><span class="sxs-lookup"><span data-stu-id="3104d-108">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> <span data-ttu-id="3104d-109">新しい ASP.NET Core アプリケーションを作成すると、このフックが既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="3104d-109">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="3104d-110">`DbContext`自身とそのコンストラクター内の依存関係は、アプリケーションのサービスプロバイダーにサービスとして登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3104d-110">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="3104d-111">これは[、 `DbContext` のインスタンスを `DbContextOptions<TContext>` 引数として受け取り][5]、 [ `AddDbContext<TContext>` メソッド][6]を使用して、にコンストラクターを設定することによって簡単に実現できます。</span><span class="sxs-lookup"><span data-stu-id="3104d-111">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

## <a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="3104d-112">パラメーターのないコンストラクターを使用する</span><span class="sxs-lookup"><span data-stu-id="3104d-112">Using a constructor with no parameters</span></span>

<span data-ttu-id="3104d-113">DbContext をアプリケーションサービスプロバイダーから取得できない場合、ツールは `DbContext` プロジェクト内で派生型を検索します。</span><span class="sxs-lookup"><span data-stu-id="3104d-113">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="3104d-114">次に、パラメーターのないコンストラクターを使用してインスタンスを作成しようとします。</span><span class="sxs-lookup"><span data-stu-id="3104d-114">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="3104d-115">がメソッドを使用して構成されている場合、これは既定のコンストラクターになることがあり `DbContext` [`OnConfiguring`][7] ます。</span><span class="sxs-lookup"><span data-stu-id="3104d-115">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

## <a name="from-a-design-time-factory"></a><span data-ttu-id="3104d-116">デザイン時のファクトリから</span><span class="sxs-lookup"><span data-stu-id="3104d-116">From a design-time factory</span></span>

<span data-ttu-id="3104d-117">また、インターフェイスを実装することによって DbContext を作成する方法を指定することもでき `IDesignTimeDbContextFactory<TContext>` ます。このインターフェイスを実装するクラスが、派生プロジェクトまたはアプリケーションのスタートアッププロジェクトと同じプロジェクトに存在する場合 `DbContext` 、ツールは dbcontext を作成する他の方法をバイパスし、代わりにデザイン時のファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="3104d-117">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> <span data-ttu-id="3104d-118">EFCore 5.0 より前では、 `args` パラメーターは使用されていませんでした ([この問題][8]を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="3104d-118">Prior to EFCore 5.0 the `args` parameter was unused (see [this issue][8]).</span></span>
> <span data-ttu-id="3104d-119">これは EFCore 5.0 で修正され、追加のデザイン時引数は、そのパラメーターを通じてアプリケーションに渡されます。</span><span class="sxs-lookup"><span data-stu-id="3104d-119">This is fixed in EFCore 5.0 and any additional design-time arguments are passed into the application through that parameter.</span></span>

<span data-ttu-id="3104d-120">デザイン時のファクトリは、実行時とは異なる方法で DbContext をデザイン時に構成する必要がある場合、コンストラクターが DI に登録されていない場合、 `DbContext` di を使用していない場合、または何らかの理由で `BuildWebHost` ASP.NET Core アプリケーションのクラスにメソッドを使用しない場合に特に便利です `Main` 。</span><span class="sxs-lookup"><span data-stu-id="3104d-120">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's `Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
