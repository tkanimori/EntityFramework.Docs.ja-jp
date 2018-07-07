---
title: InMemory - EF Core でのテスト
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
ms.technology: entity-framework-core
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: f814c8955e155688bb5e8d34b9c9f6d24dcc6601
ms.sourcegitcommit: fd50ac53b93a03825dcbb42ed2e7ca95ca858d5f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900263"
---
# <a name="testing-with-inmemory"></a><span data-ttu-id="57521-102">InMemory のテスト</span><span class="sxs-lookup"><span data-stu-id="57521-102">Testing with InMemory</span></span>

<span data-ttu-id="57521-103">InMemory プロバイダーは、実際のデータベース操作のオーバーヘッドがなく、実際のデータベースへの接続を近似するものを使用してコンポーネントをテストする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="57521-103">The InMemory provider is useful when you want to test components using something that approximates connecting to the real database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="57521-104">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="57521-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub.</span></span>

## <a name="inmemory-is-not-a-relational-database"></a><span data-ttu-id="57521-105">InMemory はリレーショナル データベースではありません。</span><span class="sxs-lookup"><span data-stu-id="57521-105">InMemory is not a relational database</span></span>

<span data-ttu-id="57521-106">EF Core データベース プロバイダーはリレーショナル データベースではありません。</span><span class="sxs-lookup"><span data-stu-id="57521-106">EF Core database providers do not have to be relational databases.</span></span> <span data-ttu-id="57521-107">InMemory ように、テスト用の汎用データベースを設計し、リレーショナル データベースを模倣するためのものではありません。</span><span class="sxs-lookup"><span data-stu-id="57521-107">InMemory is designed to be a general purpose database for testing, and is not designed to mimic a relational database.</span></span>

<span data-ttu-id="57521-108">次のとおりのいくつかの例では:</span><span class="sxs-lookup"><span data-stu-id="57521-108">Some examples of this include:</span></span>

* <span data-ttu-id="57521-109">InMemory はリレーショナル データベースの参照整合性制約に違反するデータを保存できます。</span><span class="sxs-lookup"><span data-stu-id="57521-109">InMemory will allow you to save data that would violate referential integrity constraints in a relational database.</span></span>
* <span data-ttu-id="57521-110">モデルのプロパティを DefaultValueSql(string) を使用する場合は、これは、リレーショナル データベース API であり、効果はありません InMemory に対して実行したとき。</span><span class="sxs-lookup"><span data-stu-id="57521-110">If you use DefaultValueSql(string) for a property in your model, this is a relational database API and will have no effect when running against InMemory.</span></span>
* <span data-ttu-id="57521-111">[タイムスタンプや行のバージョンを使用して同時実行](xref:core/modeling/concurrency#timestamprow-version)(`[Timestamp]`または`IsRowVersion`) はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="57521-111">[Concurrency via Timestamp/row version](xref:core/modeling/concurrency#timestamprow-version) (`[Timestamp]` or `IsRowVersion`) is not supported.</span></span> <span data-ttu-id="57521-112">いいえ[DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)更新が行われた場合にスローされます古いの同時実行トークンを使用します。</span><span class="sxs-lookup"><span data-stu-id="57521-112">No [DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) will be thrown if an update is done using an old concurrency token.</span></span>

> [!TIP]  
> <span data-ttu-id="57521-113">多くのテスト目的でのこれらの違いは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="57521-113">For many test purposes these differences will not matter.</span></span> <span data-ttu-id="57521-114">ただし、真のリレーショナル データベースのように動作するものをテストする場合は、使用を検討して[SQLite メモリ内モード](sqlite.md)します。</span><span class="sxs-lookup"><span data-stu-id="57521-114">However, if you want to test against something that behaves more like a true relational database, then consider using [SQLite in-memory mode](sqlite.md).</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="57521-115">テスト シナリオの例</span><span class="sxs-lookup"><span data-stu-id="57521-115">Example testing scenario</span></span>

<span data-ttu-id="57521-116">ブログに関連するいくつかの操作を実行するアプリケーション コードは、次のサービスを検討してください。</span><span class="sxs-lookup"><span data-stu-id="57521-116">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="57521-117">内部的には、 `DbContext` SQL Server データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="57521-117">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="57521-118">ように、コードを変更しなくてもこのサービスの効率的なテストを記述またはテストを作成する作業の多くを実行できます InMemory データベースに接続するには、このコンテキストを交換する便利なことが、コンテキストの二重。</span><span class="sxs-lookup"><span data-stu-id="57521-118">It would be useful to swap this context to connect to an InMemory database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="57521-119">コンテキストを準備します。</span><span class="sxs-lookup"><span data-stu-id="57521-119">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="57521-120">2 つのデータベース プロバイダーを構成しない場合</span><span class="sxs-lookup"><span data-stu-id="57521-120">Avoid configuring two database providers</span></span>

<span data-ttu-id="57521-121">テストでは、InMemory プロバイダーを使用するコンテキストの外部で構成すること。</span><span class="sxs-lookup"><span data-stu-id="57521-121">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="57521-122">オーバーライドすることで、データベース プロバイダーを構成している場合`OnConfiguring`コンテキストでする必要がある 1 つ既に構成されていない場合のみ、データベース プロバイダーを構成することを確認する条件付きコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="57521-122">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> <span data-ttu-id="57521-123">ASP.NET Core を使用している場合、必要はありませんこのコードは、データベース プロバイダーが既に (Startup.cs) のコンテキスト外で構成されているためです。</span><span class="sxs-lookup"><span data-stu-id="57521-123">If you are using ASP.NET Core, then you should not need this code since your database provider is already configured outside of the context (in Startup.cs).</span></span>

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="57521-124">テストのコンス トラクターを追加します。</span><span class="sxs-lookup"><span data-stu-id="57521-124">Add a constructor for testing</span></span>

<span data-ttu-id="57521-125">別のデータベースに対するテストを有効にする最も簡単な方法が変更のコンテキストを受け取るコンス トラクターを公開するには、`DbContextOptions<TContext>`します。</span><span class="sxs-lookup"><span data-stu-id="57521-125">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="57521-126">`DbContextOptions<TContext>` すべてに接続するデータベースなど、その設定のコンテキストに指示します。</span><span class="sxs-lookup"><span data-stu-id="57521-126">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="57521-127">これは、コンテキストの OnConfiguring メソッドを使用して構築された同じオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="57521-127">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="57521-128">テストの記述</span><span class="sxs-lookup"><span data-stu-id="57521-128">Writing tests</span></span>

<span data-ttu-id="57521-129">このプロバイダーでのテストにキーをメモリ内データベースのスコープを制御、InMemory プロバイダーを使用してコンテキストを通知する機能があります。</span><span class="sxs-lookup"><span data-stu-id="57521-129">The key to testing with this provider is the ability to tell the context to use the InMemory provider, and control the scope of the in-memory database.</span></span> <span data-ttu-id="57521-130">通常、クリーンなデータベースは、各テスト メソッドにします。</span><span class="sxs-lookup"><span data-stu-id="57521-130">Typically you want a clean database for each test method.</span></span>

<span data-ttu-id="57521-131">InMemory データベースを使用するテスト クラスの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="57521-131">Here is an example of a test class that uses the InMemory database.</span></span> <span data-ttu-id="57521-132">各テスト メソッドでは、一意のデータベース名、つまり各メソッドが、独自の InMemory データベースを指定します。</span><span class="sxs-lookup"><span data-stu-id="57521-132">Each test method specifies a unique database name, meaning each method has its own InMemory database.</span></span>

>[!TIP]
> <span data-ttu-id="57521-133">使用する、`.UseInMemoryDatabase()`参照、NuGet パッケージの拡張メソッドで`Microsoft.EntityFrameworkCore.InMemory`します。</span><span class="sxs-lookup"><span data-stu-id="57521-133">To use the `.UseInMemoryDatabase()` extension method, reference the NuGet package `Microsoft.EntityFrameworkCore.InMemory`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
