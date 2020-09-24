---
title: DbContext プーリング
description: Entity Framework Core での DbContext プーリング
author: rick-anderson
ms.author: riande
ms.date: 9/19/2020
uid: core/miscellaneous/context-pooling
ms.openlocfilehash: fd5f53ff97a73895f0c4239439730dd8cb3ecc29
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2020
ms.locfileid: "91215584"
---
# <a name="dbcontext-pooling"></a><span data-ttu-id="3f351-103">DbContext プール</span><span class="sxs-lookup"><span data-stu-id="3f351-103">DbContext pooling</span></span>

<span data-ttu-id="3f351-104">`AddDbContextPool` インスタンスのプールを有効に `DbContext` します。</span><span class="sxs-lookup"><span data-stu-id="3f351-104">`AddDbContextPool` enables pooling of `DbContext` instances.</span></span> <span data-ttu-id="3f351-105">コンテキストプーリングは、要求ごとに新しいインスタンスを作成するのではなく、コンテキストインスタンスを再利用することで、web サーバーなどの大規模なシナリオでスループットを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="3f351-105">Context pooling can increase throughput in high-scale scenarios such as web servers by reusing context instances, rather than creating new instances for each request.</span></span>

<span data-ttu-id="3f351-106">EF Core を使用する ASP.NET Core アプリの一般的なパターンには、カスタム <xref:Microsoft.EntityFrameworkCore.DbContext> 型を [依存関係挿入](/aspnet/core/fundamentals/dependency-injection) コンテナーに登録し、コントローラーまたは Razor Pages のコンストラクターパラメーターを使用してその型のインスタンスを取得する作業が含まれます。</span><span class="sxs-lookup"><span data-stu-id="3f351-106">The typical pattern in an ASP.NET Core app using EF Core involves registering a custom <xref:Microsoft.EntityFrameworkCore.DbContext> type into the [dependency injection](/aspnet/core/fundamentals/dependency-injection) container and obtaining instances of that type through constructor parameters in controllers or Razor Pages.</span></span> <span data-ttu-id="3f351-107">コンストラクターインジェクションを使用して、要求ごとに新しいコンテキストインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="3f351-107">Using constructor injection, a new context instance is created for each request.</span></span>

<span data-ttu-id="3f351-108"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 再利用可能なコンテキストインスタンスのプールを有効にします。</span><span class="sxs-lookup"><span data-stu-id="3f351-108"><xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> enables a pool of reusable context instances.</span></span> <span data-ttu-id="3f351-109">コンテキストプーリングを使用するには、 `AddDbContextPool` サービスの登録時にではなく、メソッドを使用し `AddDbContext` ます。</span><span class="sxs-lookup"><span data-stu-id="3f351-109">To use context pooling, use the `AddDbContextPool` method instead of `AddDbContext` during service registration:</span></span>

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="3f351-110">を使用すると、コンテキストインスタンスが要求されたときに `AddDbContextPool` 、EF は、プール内に使用可能なインスタンスがあるかどうかを最初に確認します。</span><span class="sxs-lookup"><span data-stu-id="3f351-110">When `AddDbContextPool` is used, at the time a context instance is requested, EF first checks if there is an instance available in the pool.</span></span> <span data-ttu-id="3f351-111">要求処理が終わると、インスタンス上のあらゆる状態がリセットされ、インスタンス自体はプールに戻ります。</span><span class="sxs-lookup"><span data-stu-id="3f351-111">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="3f351-112">これは、ADO.NET プロバイダーでの接続プールの動作と概念的に似ており、コンテキストインスタンスの初期化にかかるコストを節約できるという利点があります。</span><span class="sxs-lookup"><span data-stu-id="3f351-112">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of the context instance.</span></span>

<span data-ttu-id="3f351-113">`poolSize`のパラメーターは、 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> プールに保持されているインスタンスの最大数を設定します。</span><span class="sxs-lookup"><span data-stu-id="3f351-113">The `poolSize` parameter of <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> sets the maximum number of instances retained by the pool.</span></span> <span data-ttu-id="3f351-114">を `poolSize` 超えた場合、新しいコンテキストインスタンスはキャッシュされず、EF は必要に応じてインスタンスを作成する非プール動作にフォールバックします。</span><span class="sxs-lookup"><span data-stu-id="3f351-114">Once `poolSize` is exceeded, new context instances are not cached and  EF falls back to the non-pooling behavior of creating instances on demand.</span></span>

## <a name="limitations"></a><span data-ttu-id="3f351-115">制限事項</span><span class="sxs-lookup"><span data-stu-id="3f351-115">Limitations</span></span>

<span data-ttu-id="3f351-116">コンテキストの初期化が大幅なコストであることを示すために、アプリのプロファイリングとテストを行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="3f351-116">Apps should be profiled and tested to show that context initialization is a significant cost.</span></span>

<span data-ttu-id="3f351-117">`AddDbContextPool` では、コンテキストのメソッドで実行できる内容にいくつかの制限があり `OnConfiguring` ます。</span><span class="sxs-lookup"><span data-stu-id="3f351-117">`AddDbContextPool` has a few limitations on what can be done in the `OnConfiguring` method of the context.</span></span>

> [!WARNING]  
> <span data-ttu-id="3f351-118">状態を維持するアプリでコンテキストプーリングを使用することは避けてください。</span><span class="sxs-lookup"><span data-stu-id="3f351-118">Avoid using context pooling in apps that maintain state.</span></span> <span data-ttu-id="3f351-119">たとえば、要求間で共有しないようなコンテキストのプライベートフィールドです。</span><span class="sxs-lookup"><span data-stu-id="3f351-119">For example, private fields in the context that shouldn't be shared across requests.</span></span> <span data-ttu-id="3f351-120">EF Core は、コンテキストインスタンスをプールに追加する前に、認識している状態のみをリセットします。</span><span class="sxs-lookup"><span data-stu-id="3f351-120">EF Core only resets the state that it is aware of before adding a context instance to the pool.</span></span>

<span data-ttu-id="3f351-121">コンテキストプーリングは、要求間で同じコンテキストインスタンスを再利用することによって機能します。</span><span class="sxs-lookup"><span data-stu-id="3f351-121">Context pooling works by reusing the same context instance across requests.</span></span> <span data-ttu-id="3f351-122">これは、インスタンス自体を使用して、永続化できるように、 [シングルトン](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) として効果的に登録されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="3f351-122">This means that it's effectively registered as a [Singleton](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) in terms of the instance itself so that it's able to persist.</span></span>

<span data-ttu-id="3f351-123">コンテキストプーリングは、解決されたサービスを含むコンテキスト構成が要求間で固定されているシナリオを対象としています。</span><span class="sxs-lookup"><span data-stu-id="3f351-123">Context pooling is intended for scenarios where the context configuration, which includes services resolved, is fixed between requests.</span></span> <span data-ttu-id="3f351-124">[スコープ](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)が指定されたサービスが必要な場合、または構成を変更する必要がある場合は、プーリングを使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="3f351-124">For cases where [Scoped](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) services are required, or configuration needs to be changed, don't use pooling.</span></span> <span data-ttu-id="3f351-125">プールからのパフォーマンスの向上は通常、高度に最適化されたシナリオ以外ではごくわずかです。</span><span class="sxs-lookup"><span data-stu-id="3f351-125">The performance gain from pooling is usually negligible except in highly optimized scenarios.</span></span>
