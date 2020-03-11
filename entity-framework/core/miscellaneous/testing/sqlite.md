---
title: SQLite を使用したテスト-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 7a2b75e2-1875-4487-9877-feff0651b5a6
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: f7f847d8c766c0d4d7577ea6760ee72a17f84933
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414633"
---
# <a name="testing-with-sqlite"></a><span data-ttu-id="68b2e-102">SQLite のテスト</span><span class="sxs-lookup"><span data-stu-id="68b2e-102">Testing with SQLite</span></span>

<span data-ttu-id="68b2e-103">SQLite にはメモリ内モードがあり、これを使用すると、実際のデータベース操作のオーバーヘッドを発生させることなく、リレーショナルデータベースに対して SQLite を使用してテストを作成することができます。</span><span class="sxs-lookup"><span data-stu-id="68b2e-103">SQLite has an in-memory mode that allows you to use SQLite to write tests against a relational database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="68b2e-104">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)は GitHub でご覧いただけます。</span><span class="sxs-lookup"><span data-stu-id="68b2e-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="68b2e-105">テストシナリオの例</span><span class="sxs-lookup"><span data-stu-id="68b2e-105">Example testing scenario</span></span>

<span data-ttu-id="68b2e-106">アプリケーションコードがブログに関連するいくつかの操作を実行できるようにするには、次のサービスを検討してください。</span><span class="sxs-lookup"><span data-stu-id="68b2e-106">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="68b2e-107">内部的には、SQL Server データベースに接続する `DbContext` を使用します。</span><span class="sxs-lookup"><span data-stu-id="68b2e-107">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="68b2e-108">このコンテキストをスワップしてメモリ内の SQLite データベースに接続すると、コードを変更しなくても効率的なテストを作成できるようになります。また、コンテキストのテスト double を作成するために作業を行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="68b2e-108">It would be useful to swap this context to connect to an in-memory SQLite database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="68b2e-109">コンテキストの準備</span><span class="sxs-lookup"><span data-stu-id="68b2e-109">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="68b2e-110">2つのデータベースプロバイダーの構成を回避する</span><span class="sxs-lookup"><span data-stu-id="68b2e-110">Avoid configuring two database providers</span></span>

<span data-ttu-id="68b2e-111">テストでは、InMemory プロバイダーを使用するようにコンテキストを外部で構成しようとしています。</span><span class="sxs-lookup"><span data-stu-id="68b2e-111">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="68b2e-112">コンテキストで `OnConfiguring` をオーバーライドしてデータベースプロバイダーを構成する場合は、データベースプロバイダーがまだ構成されていない場合にのみ構成するように、いくつかの条件付きコードを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="68b2e-112">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

> [!TIP]  
> <span data-ttu-id="68b2e-113">ASP.NET Core を使用している場合は、このコードは必要ありません。これは、データベースプロバイダーがコンテキスト (Startup.cs) の外部で構成されているためです。</span><span class="sxs-lookup"><span data-stu-id="68b2e-113">If you are using ASP.NET Core, then you should not need this code since your database provider is configured outside of the context (in Startup.cs).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="68b2e-114">テスト用のコンストラクターを追加する</span><span class="sxs-lookup"><span data-stu-id="68b2e-114">Add a constructor for testing</span></span>

<span data-ttu-id="68b2e-115">別のデータベースに対してテストを有効にする最も簡単な方法は、コンテキストを変更して、`DbContextOptions<TContext>`を受け入れるコンストラクターを公開することです。</span><span class="sxs-lookup"><span data-stu-id="68b2e-115">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="68b2e-116">`DbContextOptions<TContext>` は、接続先のデータベースなど、すべての設定をコンテキストに伝えます。</span><span class="sxs-lookup"><span data-stu-id="68b2e-116">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="68b2e-117">これは、コンテキストで OnConfiguring メソッドを実行して作成されたオブジェクトと同じです。</span><span class="sxs-lookup"><span data-stu-id="68b2e-117">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="68b2e-118">テストの作成</span><span class="sxs-lookup"><span data-stu-id="68b2e-118">Writing tests</span></span>

<span data-ttu-id="68b2e-119">このプロバイダーを使用してテストするための鍵は、SQLite を使用するようにコンテキストに指示し、インメモリデータベースのスコープを制御する機能です。</span><span class="sxs-lookup"><span data-stu-id="68b2e-119">The key to testing with this provider is the ability to tell the context to use SQLite, and control the scope of the in-memory database.</span></span> <span data-ttu-id="68b2e-120">データベースのスコープは、接続を開いたり閉じたりすることによって制御されます。</span><span class="sxs-lookup"><span data-stu-id="68b2e-120">The scope of the database is controlled by opening and closing the connection.</span></span> <span data-ttu-id="68b2e-121">データベースのスコープは、接続が開いている期間です。</span><span class="sxs-lookup"><span data-stu-id="68b2e-121">The database is scoped to the duration that the connection is open.</span></span> <span data-ttu-id="68b2e-122">通常は、各テストメソッドにクリーンなデータベースが必要です。</span><span class="sxs-lookup"><span data-stu-id="68b2e-122">Typically you want a clean database for each test method.</span></span>

>[!TIP]
> <span data-ttu-id="68b2e-123">`SqliteConnection()` と `.UseSqlite()` 拡張メソッドを使用するには、 [NuGet パッケージを](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/)参照してください。</span><span class="sxs-lookup"><span data-stu-id="68b2e-123">To use `SqliteConnection()` and the `.UseSqlite()` extension method, reference the NuGet package [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/SQLite/BlogServiceTests.cs)]
