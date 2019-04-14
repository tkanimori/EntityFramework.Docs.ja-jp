---
title: SQLite - EF Core でのテスト
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: e8ff204a09d50064b4f0d4376f02b05c8681ac25
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562534"
---
# <a name="testing-with-sqlite"></a><span data-ttu-id="126b0-102">SQLite のテスト</span><span class="sxs-lookup"><span data-stu-id="126b0-102">Testing with SQLite</span></span>

<span data-ttu-id="126b0-103">SQLite は、SQLite を使用して、実際のデータベース操作のオーバーヘッドがなく、リレーショナル データベースに対してテストを記述することができるようにする、インメモリ モードです。</span><span class="sxs-lookup"><span data-stu-id="126b0-103">SQLite has an in-memory mode that allows you to use SQLite to write tests against a relational database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="126b0-104">この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)github</span><span class="sxs-lookup"><span data-stu-id="126b0-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="126b0-105">テスト シナリオの例</span><span class="sxs-lookup"><span data-stu-id="126b0-105">Example testing scenario</span></span>

<span data-ttu-id="126b0-106">ブログに関連するいくつかの操作を実行するアプリケーション コードは、次のサービスを検討してください。</span><span class="sxs-lookup"><span data-stu-id="126b0-106">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="126b0-107">内部的には、 `DbContext` SQL Server データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="126b0-107">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="126b0-108">ように、コードを変更しなくてもこのサービスの効率的なテストを記述またはテストを作成する作業の多くを実行できます、メモリ内の SQLite データベースに接続するには、このコンテキストを交換する便利なことが、コンテキストの二重。</span><span class="sxs-lookup"><span data-stu-id="126b0-108">It would be useful to swap this context to connect to an in-memory SQLite database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="126b0-109">コンテキストを準備します。</span><span class="sxs-lookup"><span data-stu-id="126b0-109">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="126b0-110">2 つのデータベース プロバイダーを構成しない場合</span><span class="sxs-lookup"><span data-stu-id="126b0-110">Avoid configuring two database providers</span></span>

<span data-ttu-id="126b0-111">テストでは、InMemory プロバイダーを使用するコンテキストの外部で構成すること。</span><span class="sxs-lookup"><span data-stu-id="126b0-111">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="126b0-112">オーバーライドすることで、データベース プロバイダーを構成している場合`OnConfiguring`コンテキストでする必要がある 1 つ既に構成されていない場合のみ、データベース プロバイダーを構成することを確認する条件付きコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="126b0-112">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

> [!TIP]  
> <span data-ttu-id="126b0-113">ASP.NET Core を使用している場合、必要はありませんこのコード (Startup.cs) のコンテキスト外で、データベース プロバイダーが構成されているためです。</span><span class="sxs-lookup"><span data-stu-id="126b0-113">If you are using ASP.NET Core, then you should not need this code since your database provider is configured outside of the context (in Startup.cs).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="126b0-114">テストのコンス トラクターを追加します。</span><span class="sxs-lookup"><span data-stu-id="126b0-114">Add a constructor for testing</span></span>

<span data-ttu-id="126b0-115">別のデータベースに対するテストを有効にする最も簡単な方法が変更のコンテキストを受け取るコンス トラクターを公開するには、`DbContextOptions<TContext>`します。</span><span class="sxs-lookup"><span data-stu-id="126b0-115">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="126b0-116">`DbContextOptions<TContext>` すべてに接続するデータベースなど、その設定のコンテキストに指示します。</span><span class="sxs-lookup"><span data-stu-id="126b0-116">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="126b0-117">これは、コンテキストの OnConfiguring メソッドを使用して構築された同じオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="126b0-117">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="126b0-118">テストの記述</span><span class="sxs-lookup"><span data-stu-id="126b0-118">Writing tests</span></span>

<span data-ttu-id="126b0-119">このプロバイダーでのテストにキーをメモリ内データベースのスコープを制御、SQLite を使用してコンテキストを通知する機能があります。</span><span class="sxs-lookup"><span data-stu-id="126b0-119">The key to testing with this provider is the ability to tell the context to use SQLite, and control the scope of the in-memory database.</span></span> <span data-ttu-id="126b0-120">データベースのスコープは、接続の開閉によって制御されます。</span><span class="sxs-lookup"><span data-stu-id="126b0-120">The scope of the database is controlled by opening and closing the connection.</span></span> <span data-ttu-id="126b0-121">データベースは、接続が開かれている期間に制限されます。</span><span class="sxs-lookup"><span data-stu-id="126b0-121">The database is scoped to the duration that the connection is open.</span></span> <span data-ttu-id="126b0-122">通常、クリーンなデータベースは、各テスト メソッドにします。</span><span class="sxs-lookup"><span data-stu-id="126b0-122">Typically you want a clean database for each test method.</span></span>

>[!TIP]
> <span data-ttu-id="126b0-123">使用する`SqliteConnection()`と`.UseSqlite()`参照、NuGet パッケージの拡張メソッドで[Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)します。</span><span class="sxs-lookup"><span data-stu-id="126b0-123">To use `SqliteConnection()` and the `.UseSqlite()` extension method, reference the NuGet package [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
