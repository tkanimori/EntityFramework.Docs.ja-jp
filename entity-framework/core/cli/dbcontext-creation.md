---
title: デザイン時 DbContext の作成-EF Core
description: Entity Framework Core を使用したデザイン時の DbContext の作成方法
author: bricelam
ms.date: 10/27/2020
uid: core/cli/dbcontext-creation
ms.openlocfilehash: 144ed26dcf605dc29d53519ad2ea9cea58fb4e44
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431318"
---
# <a name="design-time-dbcontext-creation"></a><span data-ttu-id="e3f58-103">デザイン時 DbContext 作成</span><span class="sxs-lookup"><span data-stu-id="e3f58-103">Design-time DbContext Creation</span></span>

<span data-ttu-id="e3f58-104">一部の EF Core ツールコマンド (たとえば、 [移行][1] コマンド) では、 `DbContext` アプリケーションのエンティティ型に関する詳細情報を収集し、データベーススキーマにマップする方法に基づいて、デザイン時に派生インスタンスを作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e3f58-104">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="e3f58-105">ほとんどの場合、 `DbContext` 作成されるは実行時 [に構成][2]するのと同様の方法で構成することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e3f58-105">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="e3f58-106">ツールでは、さまざまな方法でを作成し `DbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="e3f58-106">There are various ways the tools try to create the `DbContext`:</span></span>

## <a name="from-application-services"></a><span data-ttu-id="e3f58-107">アプリケーションサービスから</span><span class="sxs-lookup"><span data-stu-id="e3f58-107">From application services</span></span>

<span data-ttu-id="e3f58-108">スタートアッププロジェクトで [ASP.NET Core Web ホスト][3] または [.Net Core 汎用ホスト][4]が使用されている場合、ツールはアプリケーションのサービスプロバイダーから dbcontext オブジェクトを取得しようとします。</span><span class="sxs-lookup"><span data-stu-id="e3f58-108">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="e3f58-109">ツールは、まずを呼び出し、を呼び出して、プロパティにアクセスすることで、サービスプロバイダーを取得しようとし `Program.CreateHostBuilder()` `Build()` `Services` ます。</span><span class="sxs-lookup"><span data-stu-id="e3f58-109">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs#ApplicationService)]

> [!NOTE]
> <span data-ttu-id="e3f58-110">新しい ASP.NET Core アプリケーションを作成すると、このフックが既定で含まれます。</span><span class="sxs-lookup"><span data-stu-id="e3f58-110">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="e3f58-111">`DbContext`自身とそのコンストラクター内の依存関係は、アプリケーションのサービスプロバイダーにサービスとして登録する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e3f58-111">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="e3f58-112">これは[、 `DbContext` のインスタンスを `DbContextOptions<TContext>` 引数として受け取り][5]、 [ `AddDbContext<TContext>` メソッド][6]を使用して、にコンストラクターを設定することによって簡単に実現できます。</span><span class="sxs-lookup"><span data-stu-id="e3f58-112">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

## <a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="e3f58-113">パラメーターのないコンストラクターを使用する</span><span class="sxs-lookup"><span data-stu-id="e3f58-113">Using a constructor with no parameters</span></span>

<span data-ttu-id="e3f58-114">DbContext をアプリケーションサービスプロバイダーから取得できない場合、ツールは `DbContext` プロジェクト内で派生型を検索します。</span><span class="sxs-lookup"><span data-stu-id="e3f58-114">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="e3f58-115">次に、パラメーターのないコンストラクターを使用してインスタンスを作成しようとします。</span><span class="sxs-lookup"><span data-stu-id="e3f58-115">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="e3f58-116">がメソッドを使用して構成されている場合、これは既定のコンストラクターになることがあり `DbContext` [`OnConfiguring`][7] ます。</span><span class="sxs-lookup"><span data-stu-id="e3f58-116">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

## <a name="from-a-design-time-factory"></a><span data-ttu-id="e3f58-117">デザイン時のファクトリから</span><span class="sxs-lookup"><span data-stu-id="e3f58-117">From a design-time factory</span></span>

<span data-ttu-id="e3f58-118">また、インターフェイスを実装することによって DbContext を作成する方法を指定することもでき `IDesignTimeDbContextFactory<TContext>` ます。このインターフェイスを実装するクラスが、派生プロジェクトまたはアプリケーションのスタートアッププロジェクトと同じプロジェクトに存在する場合 `DbContext` 、ツールは dbcontext を作成する他の方法をバイパスし、代わりにデザイン時のファクトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="e3f58-118">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs#BloggingContextFactory)]

> [!NOTE]
> <span data-ttu-id="e3f58-119">EFCore 5.0 より前では、 `args` パラメーターは使用されていませんでした ( [この問題][8]を参照してください)。</span><span class="sxs-lookup"><span data-stu-id="e3f58-119">Prior to EFCore 5.0 the `args` parameter was unused (see [this issue][8]).</span></span>
> <span data-ttu-id="e3f58-120">これは EFCore 5.0 で修正され、追加のデザイン時引数は、そのパラメーターを通じてアプリケーションに渡されます。</span><span class="sxs-lookup"><span data-stu-id="e3f58-120">This is fixed in EFCore 5.0 and any additional design-time arguments are passed into the application through that parameter.</span></span>

<span data-ttu-id="e3f58-121">デザイン時のファクトリは、実行時とは異なる方法で DbContext をデザイン時に構成する必要がある場合、コンストラクターが DI に登録されていない場合、 `DbContext` di を使用していない場合、または何らかの理由で `CreateHostBuilder` ASP.NET Core アプリケーションのクラスにメソッドを使用しない場合に特に便利です `Main` 。</span><span class="sxs-lookup"><span data-stu-id="e3f58-121">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `CreateHostBuilder` method in your ASP.NET Core application's `Main` class.</span></span>

## <a name="args"></a><span data-ttu-id="e3f58-122">Args</span><span class="sxs-lookup"><span data-stu-id="e3f58-122">Args</span></span>

<span data-ttu-id="e3f58-123">IDesignTimeDbContextFactory. CreateDbContext とプログラム Createdbcontext の両方がコマンドライン引数を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="e3f58-123">Both IDesignTimeDbContextFactory.CreateDbContext and Program.CreateHostBuilder accept command line arguments.</span></span>

<span data-ttu-id="e3f58-124">EF Core 5.0 以降では、ツールから次の引数を指定できます。</span><span class="sxs-lookup"><span data-stu-id="e3f58-124">Starting in EF Core 5.0, you can specify these arguments from the tools:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="e3f58-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e3f58-125">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update -- --environment Production
```

<span data-ttu-id="e3f58-126">この `--` トークンは、 `dotnet ef` 後続のすべてのものを引数として処理し、オプションとして解析しないように指示します。</span><span class="sxs-lookup"><span data-stu-id="e3f58-126">The `--` token directs `dotnet ef` to treat everything that follows as an argument and not try to parse them as options.</span></span> <span data-ttu-id="e3f58-127">によって使用されていない追加 `dotnet ef` の引数は、アプリに転送されます。</span><span class="sxs-lookup"><span data-stu-id="e3f58-127">Any extra arguments not used by `dotnet ef` are forwarded to the app.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="e3f58-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e3f58-128">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database -Args '--environment Production'
```

***

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/dbcontext-configuration/index
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/dbcontext-configuration/index#constructor-argument
  [6]: xref:core/dbcontext-configuration/index#using-dbcontext-with-dependency-injection
  [7]: xref:core/dbcontext-configuration/index#onconfiguring
  [8]: https://github.com/dotnet/efcore/issues/8332
