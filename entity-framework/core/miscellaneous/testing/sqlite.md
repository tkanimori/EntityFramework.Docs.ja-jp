---
title: SQLite - EF Core でのテスト
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
ms.technology: entity-framework-core
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: 8fcb4b1a37da6f52219ebe3c672160627fe28fab
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052702"
---
# <a name="testing-with-sqlite"></a><span data-ttu-id="4bcf9-102">SQLite でテストします。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-102">Testing with SQLite</span></span>

<span data-ttu-id="4bcf9-103">SQLite は、SQLite を使用して実際のデータベース操作のオーバーヘッドがなく、リレーショナル データベースに対してテストを記述できるようにする、インメモリ モードです。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-103">SQLite has an in-memory mode that allows you to use SQLite to write tests against a relational database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="4bcf9-104">この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)github</span><span class="sxs-lookup"><span data-stu-id="4bcf9-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="4bcf9-105">テスト シナリオの例</span><span class="sxs-lookup"><span data-stu-id="4bcf9-105">Example testing scenario</span></span>

<span data-ttu-id="4bcf9-106">ブログに関連するいくつかの操作を実行するアプリケーション コードは、次のサービスを検討してください。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-106">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="4bcf9-107">内部的に使用して、 `DbContext` SQL Server データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-107">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="4bcf9-108">コードを変更しなくてもこのサービスの効率的なテストを記述またはテストを作成する作業の多くを実行できるようにする、インメモリ SQLite データベースに接続するには、このコンテキストを交換すると便利だコンテキストの二重です。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-108">It would be useful to swap this context to connect to an in-memory SQLite database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="4bcf9-109">コンテキストを整える</span><span class="sxs-lookup"><span data-stu-id="4bcf9-109">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="4bcf9-110">2 つのデータベース プロバイダーを構成しません。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-110">Avoid configuring two database providers</span></span>

<span data-ttu-id="4bcf9-111">テストにしようとする InMemory プロバイダーを使用するコンテキストの外部で構成します。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-111">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="4bcf9-112">オーバーライドすることで、データベース プロバイダーを構成する場合`OnConfiguring`コンテキストにする必要がいずれかが既に構成されていない場合のみ、データベース プロバイダーを構成することを確認する条件付きコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-112">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

> [!TIP]  
> <span data-ttu-id="4bcf9-113">ASP.NET Core を使用している場合、必要はありませんこのコード (Startup.cs) のコンテキスト外で、データベース プロバイダーが構成されているためです。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-113">If you are using ASP.NET Core, then you should not need this code since your database provider is configured outside of the context (in Startup.cs).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="4bcf9-114">テストするためのコンス トラクターを追加します。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-114">Add a constructor for testing</span></span>

<span data-ttu-id="4bcf9-115">別のデータベースに対してテストを有効にする最も簡単な方法は、コンテキストを受け取るコンス トラクターを公開するを変更する、`DbContextOptions<TContext>`です。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-115">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="4bcf9-116">`DbContextOptions<TContext>`すべてに接続するデータベースなど、その設定のコンテキストに指示します。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-116">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="4bcf9-117">これは、同じオブジェクトのコンテキストで OnConfiguring メソッドを実行して組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-117">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="4bcf9-118">テストの記述</span><span class="sxs-lookup"><span data-stu-id="4bcf9-118">Writing tests</span></span>

<span data-ttu-id="4bcf9-119">このプロバイダーでのテストに、キーは、SQLite を使用して、メモリ内のデータベースのスコープを制御するコンテキストを確認する機能です。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-119">The key to testing with this provider is the ability to tell the context to use SQLite, and control the scope of the in-memory database.</span></span> <span data-ttu-id="4bcf9-120">データベースのスコープは、接続の開閉によって制御されます。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-120">The scope of the database is controlled by opening and closing the connection.</span></span> <span data-ttu-id="4bcf9-121">データベースは、接続が開いている間に制限されます。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-121">The database is scoped to the duration that the connection is open.</span></span> <span data-ttu-id="4bcf9-122">通常、クリーンなデータベースは各テスト メソッドにします。</span><span class="sxs-lookup"><span data-stu-id="4bcf9-122">Typically you want a clean database for each test method.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
