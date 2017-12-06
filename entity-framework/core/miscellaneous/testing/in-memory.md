---
title: "InMemory - EF Core でのテスト"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
ms.technology: entity-framework-core
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: c5c48c575e9fd693d1f28d1a6d10eb83ebbc9d70
ms.sourcegitcommit: 860ec5d047342fbc4063a0de881c9861cc1f8813
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2017
---
# <a name="testing-with-inmemory"></a><span data-ttu-id="10ca2-102">InMemory でテストします。</span><span class="sxs-lookup"><span data-stu-id="10ca2-102">Testing with InMemory</span></span>

<span data-ttu-id="10ca2-103">InMemory プロバイダーは、実際のデータベース操作のオーバーヘッドがなく、実際のデータベースへの接続を概算するためのものを使用してコンポーネントをテストする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="10ca2-103">The InMemory provider is useful when you want to test components using something that approximates connecting to the real database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="10ca2-104">この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)GitHub でします。</span><span class="sxs-lookup"><span data-stu-id="10ca2-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub.</span></span>

## <a name="inmemory-is-not-a-relational-database"></a><span data-ttu-id="10ca2-105">InMemory はリレーショナル データベースではありません。</span><span class="sxs-lookup"><span data-stu-id="10ca2-105">InMemory is not a relational database</span></span>

<span data-ttu-id="10ca2-106">EF コア データベース プロバイダーはリレーショナル データベースではありません。</span><span class="sxs-lookup"><span data-stu-id="10ca2-106">EF Core database providers do not have to be relational databases.</span></span> <span data-ttu-id="10ca2-107">InMemory テスト、汎用データベースであるように設計されたされており、リレーショナル データベースを模倣するためにします。</span><span class="sxs-lookup"><span data-stu-id="10ca2-107">InMemory is designed to be a general purpose database for testing, and is not designed to mimic a relational database.</span></span>

<span data-ttu-id="10ca2-108">この以下の例をいくつか:</span><span class="sxs-lookup"><span data-stu-id="10ca2-108">Some examples of this include:</span></span>
* <span data-ttu-id="10ca2-109">InMemory を使用すると、リレーショナル データベースの参照整合性制約に違反するデータを保存できます。</span><span class="sxs-lookup"><span data-stu-id="10ca2-109">InMemory will allow you to save data that would violate referential integrity constraints in a relational database.</span></span>

* <span data-ttu-id="10ca2-110">DefaultValueSql(string) プロパティのモデル内で使用する場合は、これはリレーショナル データベース API であり、効果はありません InMemory に対して実行する場合。</span><span class="sxs-lookup"><span data-stu-id="10ca2-110">If you use DefaultValueSql(string) for a property in your model, this is a relational database API and will have no effect when running against InMemory.</span></span>

> [!TIP]  
> <span data-ttu-id="10ca2-111">多くのテスト目的でのこれらの相違点は関係ありません。</span><span class="sxs-lookup"><span data-stu-id="10ca2-111">For many test purposes these differences will not matter.</span></span> <span data-ttu-id="10ca2-112">ただし、場合は true。 リレーショナル データベースのように動作しているものに対してテストする場合は、使用を検討して[SQLite インメモリ モード](sqlite.md)です。</span><span class="sxs-lookup"><span data-stu-id="10ca2-112">However, if you want to test against something that behaves more like a true relational database, then consider using [SQLite in-memory mode](sqlite.md).</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="10ca2-113">テスト シナリオの例</span><span class="sxs-lookup"><span data-stu-id="10ca2-113">Example testing scenario</span></span>

<span data-ttu-id="10ca2-114">ブログに関連するいくつかの操作を実行するアプリケーション コードは、次のサービスを検討してください。</span><span class="sxs-lookup"><span data-stu-id="10ca2-114">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="10ca2-115">内部的に使用して、 `DbContext` SQL Server データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="10ca2-115">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="10ca2-116">データベースに接続する InMemory おコードを変更しなくてもこのサービスの効率的なテストを記述またはテストを作成する作業の多くを実行できるようにするには、このコンテキストを交換すると便利だコンテキストの二重です。</span><span class="sxs-lookup"><span data-stu-id="10ca2-116">It would be useful to swap this context to connect to an InMemory database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="10ca2-117">コンテキストを整える</span><span class="sxs-lookup"><span data-stu-id="10ca2-117">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="10ca2-118">2 つのデータベース プロバイダーを構成しません。</span><span class="sxs-lookup"><span data-stu-id="10ca2-118">Avoid configuring two database providers</span></span>

<span data-ttu-id="10ca2-119">テストにしようとする InMemory プロバイダーを使用するコンテキストの外部で構成します。</span><span class="sxs-lookup"><span data-stu-id="10ca2-119">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="10ca2-120">オーバーライドすることで、データベース プロバイダーを構成する場合`OnConfiguring`コンテキストにする必要がいずれかが既に構成されていない場合のみ、データベース プロバイダーを構成することを確認する条件付きコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="10ca2-120">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> <span data-ttu-id="10ca2-121">ASP.NET Core を使用している場合、必要はありませんこのコード (Startup.cs) のコンテキスト外で、データベース プロバイダーが既に構成されているためです。</span><span class="sxs-lookup"><span data-stu-id="10ca2-121">If you are using ASP.NET Core, then you should not need this code since your database provider is already configured outside of the context (in Startup.cs).</span></span>

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="10ca2-122">テストするためのコンス トラクターを追加します。</span><span class="sxs-lookup"><span data-stu-id="10ca2-122">Add a constructor for testing</span></span>

<span data-ttu-id="10ca2-123">別のデータベースに対してテストを有効にする最も簡単な方法は、コンテキストを受け取るコンス トラクターを公開するを変更する、`DbContextOptions<TContext>`です。</span><span class="sxs-lookup"><span data-stu-id="10ca2-123">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="10ca2-124">`DbContextOptions<TContext>`すべてに接続するデータベースなど、その設定のコンテキストに指示します。</span><span class="sxs-lookup"><span data-stu-id="10ca2-124">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="10ca2-125">これは、同じオブジェクトのコンテキストで OnConfiguring メソッドを実行して組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="10ca2-125">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="10ca2-126">テストの記述</span><span class="sxs-lookup"><span data-stu-id="10ca2-126">Writing tests</span></span>

<span data-ttu-id="10ca2-127">このプロバイダーでのテストに、キーは、InMemory プロバイダーを使用して、メモリ内のデータベースのスコープを制御するコンテキストを確認する機能です。</span><span class="sxs-lookup"><span data-stu-id="10ca2-127">The key to testing with this provider is the ability to tell the context to use the InMemory provider, and control the scope of the in-memory database.</span></span> <span data-ttu-id="10ca2-128">通常、クリーンなデータベースは各テスト メソッドにします。</span><span class="sxs-lookup"><span data-stu-id="10ca2-128">Typically you want a clean database for each test method.</span></span>

<span data-ttu-id="10ca2-129">InMemory データベースを使用するテスト クラスの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="10ca2-129">Here is an example of a test class that uses the InMemory database.</span></span> <span data-ttu-id="10ca2-130">各テスト メソッドでは、各方法に独自の InMemory データベースつまり、一意のデータベース名を指定します。</span><span class="sxs-lookup"><span data-stu-id="10ca2-130">Each test method specifies a unique database name, meaning each method has its own InMemory database.</span></span>

>[!TIP]
> <span data-ttu-id="10ca2-131">使用する、 `.UseInMemoryDatabase()` Nuget パッケージの参照の拡張メソッドで`Microsoft.EntityFrameworkCore.InMemory`です。</span><span class="sxs-lookup"><span data-stu-id="10ca2-131">To use the `.UseInMemoryDatabase()` extension method, reference the Nuget package `Microsoft.EntityFrameworkCore.InMemory`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
