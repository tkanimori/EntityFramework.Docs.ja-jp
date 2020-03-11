---
title: InMemory を使用したテスト-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0d0590f1-1ea3-4d5c-8f44-db17395cd3f3
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 18641677098c20d9172136b07868dcb647d189c6
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414027"
---
# <a name="testing-with-inmemory"></a><span data-ttu-id="24336-102">InMemory のテスト</span><span class="sxs-lookup"><span data-stu-id="24336-102">Testing with InMemory</span></span>

<span data-ttu-id="24336-103">InMemory プロバイダーは、実際のデータベース操作のオーバーヘッドを発生させることなく、実際のデータベースへの接続を使用してコンポーネントをテストする場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="24336-103">The InMemory provider is useful when you want to test components using something that approximates connecting to the real database, without the overhead of actual database operations.</span></span>

> [!TIP]  
> <span data-ttu-id="24336-104">この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="24336-104">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing) on GitHub.</span></span>

## <a name="inmemory-is-not-a-relational-database"></a><span data-ttu-id="24336-105">InMemory はリレーショナルデータベースではありません</span><span class="sxs-lookup"><span data-stu-id="24336-105">InMemory is not a relational database</span></span>

<span data-ttu-id="24336-106">EF Core データベースプロバイダーは、リレーショナルデータベースである必要はありません。</span><span class="sxs-lookup"><span data-stu-id="24336-106">EF Core database providers do not have to be relational databases.</span></span> <span data-ttu-id="24336-107">InMemory は、テスト用の汎用データベースとして設計されており、リレーショナルデータベースを模倣するように設計されていません。</span><span class="sxs-lookup"><span data-stu-id="24336-107">InMemory is designed to be a general purpose database for testing, and is not designed to mimic a relational database.</span></span>

<span data-ttu-id="24336-108">たとえば、次のような例があります。</span><span class="sxs-lookup"><span data-stu-id="24336-108">Some examples of this include:</span></span>

* <span data-ttu-id="24336-109">InMemory を使用すると、リレーショナルデータベースの参照整合性制約に違反するデータを保存できます。</span><span class="sxs-lookup"><span data-stu-id="24336-109">InMemory will allow you to save data that would violate referential integrity constraints in a relational database.</span></span>
* <span data-ttu-id="24336-110">モデルのプロパティに DefaultValueSql (string) を使用した場合、これはリレーショナルデータベース API であり、InMemory に対して実行しても効果はありません。</span><span class="sxs-lookup"><span data-stu-id="24336-110">If you use DefaultValueSql(string) for a property in your model, this is a relational database API and will have no effect when running against InMemory.</span></span>
* <span data-ttu-id="24336-111">[タイムスタンプ/行バージョン](xref:core/modeling/concurrency#timestamprowversion)(`[Timestamp]` または `IsRowVersion`) による同時実行はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="24336-111">[Concurrency via Timestamp/row version](xref:core/modeling/concurrency#timestamprowversion) (`[Timestamp]` or `IsRowVersion`) is not supported.</span></span> <span data-ttu-id="24336-112">古い同時実行トークンを使用して更新が行われた場合、 [DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)はスローされません。</span><span class="sxs-lookup"><span data-stu-id="24336-112">No [DbUpdateConcurrencyException](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) will be thrown if an update is done using an old concurrency token.</span></span>

> [!TIP]  
> <span data-ttu-id="24336-113">多くのテスト目的では、これらの違いは関係ありません。</span><span class="sxs-lookup"><span data-stu-id="24336-113">For many test purposes these differences will not matter.</span></span> <span data-ttu-id="24336-114">ただし、真のリレーショナルデータベースのように動作するものに対してテストする場合は、 [SQLite インメモリモード](sqlite.md)の使用を検討してください。</span><span class="sxs-lookup"><span data-stu-id="24336-114">However, if you want to test against something that behaves more like a true relational database, then consider using [SQLite in-memory mode](sqlite.md).</span></span>

## <a name="example-testing-scenario"></a><span data-ttu-id="24336-115">テストシナリオの例</span><span class="sxs-lookup"><span data-stu-id="24336-115">Example testing scenario</span></span>

<span data-ttu-id="24336-116">アプリケーションコードがブログに関連するいくつかの操作を実行できるようにするには、次のサービスを検討してください。</span><span class="sxs-lookup"><span data-stu-id="24336-116">Consider the following service that allows application code to perform some operations related to blogs.</span></span> <span data-ttu-id="24336-117">内部的には、SQL Server データベースに接続する `DbContext` を使用します。</span><span class="sxs-lookup"><span data-stu-id="24336-117">Internally it uses a `DbContext` that connects to a SQL Server database.</span></span> <span data-ttu-id="24336-118">このコンテキストをスワップして、InMemory データベースに接続すると、コードを変更しなくても、このサービスに対して効率的なテストを作成できるようになります。また、コンテキストのテスト double を作成するために大量の作業を行うこともできます。</span><span class="sxs-lookup"><span data-stu-id="24336-118">It would be useful to swap this context to connect to an InMemory database so that we can write efficient tests for this service without having to modify the code, or do a lot of work to create a test double of the context.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BlogService.cs)]

## <a name="get-your-context-ready"></a><span data-ttu-id="24336-119">コンテキストの準備</span><span class="sxs-lookup"><span data-stu-id="24336-119">Get your context ready</span></span>

### <a name="avoid-configuring-two-database-providers"></a><span data-ttu-id="24336-120">2つのデータベースプロバイダーの構成を回避する</span><span class="sxs-lookup"><span data-stu-id="24336-120">Avoid configuring two database providers</span></span>

<span data-ttu-id="24336-121">テストでは、InMemory プロバイダーを使用するようにコンテキストを外部で構成しようとしています。</span><span class="sxs-lookup"><span data-stu-id="24336-121">In your tests you are going to externally configure the context to use the InMemory provider.</span></span> <span data-ttu-id="24336-122">コンテキストで `OnConfiguring` をオーバーライドしてデータベースプロバイダーを構成する場合は、データベースプロバイダーがまだ構成されていない場合にのみ構成するように、いくつかの条件付きコードを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="24336-122">If you are configuring a database provider by overriding `OnConfiguring` in your context, then you need to add some conditional code to ensure that you only configure the database provider if one has not already been configured.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#OnConfiguring)]

> [!TIP]  
> <span data-ttu-id="24336-123">ASP.NET Core を使用している場合、このコードは必要ありません。これは、データベースプロバイダーがコンテキストの外部 (Startup.cs) で既に構成されているためです。</span><span class="sxs-lookup"><span data-stu-id="24336-123">If you are using ASP.NET Core, then you should not need this code since your database provider is already configured outside of the context (in Startup.cs).</span></span>

### <a name="add-a-constructor-for-testing"></a><span data-ttu-id="24336-124">テスト用のコンストラクターを追加する</span><span class="sxs-lookup"><span data-stu-id="24336-124">Add a constructor for testing</span></span>

<span data-ttu-id="24336-125">別のデータベースに対してテストを有効にする最も簡単な方法は、コンテキストを変更して、`DbContextOptions<TContext>`を受け入れるコンストラクターを公開することです。</span><span class="sxs-lookup"><span data-stu-id="24336-125">The simplest way to enable testing against a different database is to modify your context to expose a constructor that accepts a `DbContextOptions<TContext>`.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/BusinessLogic/BloggingContext.cs#Constructors)]

> [!TIP]  
> <span data-ttu-id="24336-126">`DbContextOptions<TContext>` は、接続先のデータベースなど、すべての設定をコンテキストに伝えます。</span><span class="sxs-lookup"><span data-stu-id="24336-126">`DbContextOptions<TContext>` tells the context all of its settings, such as which database to connect to.</span></span> <span data-ttu-id="24336-127">これは、コンテキストで OnConfiguring メソッドを実行して作成されたオブジェクトと同じです。</span><span class="sxs-lookup"><span data-stu-id="24336-127">This is the same object that is built by running the OnConfiguring method in your context.</span></span>

## <a name="writing-tests"></a><span data-ttu-id="24336-128">テストの作成</span><span class="sxs-lookup"><span data-stu-id="24336-128">Writing tests</span></span>

<span data-ttu-id="24336-129">このプロバイダーを使用してテストするための鍵は、InMemory プロバイダーを使用するようにコンテキストに指示し、インメモリデータベースのスコープを制御する機能です。</span><span class="sxs-lookup"><span data-stu-id="24336-129">The key to testing with this provider is the ability to tell the context to use the InMemory provider, and control the scope of the in-memory database.</span></span> <span data-ttu-id="24336-130">通常は、各テストメソッドにクリーンなデータベースが必要です。</span><span class="sxs-lookup"><span data-stu-id="24336-130">Typically you want a clean database for each test method.</span></span>

<span data-ttu-id="24336-131">次に、InMemory データベースを使用するテストクラスの例を示します。</span><span class="sxs-lookup"><span data-stu-id="24336-131">Here is an example of a test class that uses the InMemory database.</span></span> <span data-ttu-id="24336-132">各テストメソッドには一意のデータベース名が指定されています。つまり、それぞれの方法に独自の InMemory データベースがあります。</span><span class="sxs-lookup"><span data-stu-id="24336-132">Each test method specifies a unique database name, meaning each method has its own InMemory database.</span></span>

>[!TIP]
> <span data-ttu-id="24336-133">`.UseInMemoryDatabase()` 拡張メソッドを使用するには、NuGet パッケージの " [Microsoft. EntityFrameworkCore. InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/)" を参照します。</span><span class="sxs-lookup"><span data-stu-id="24336-133">To use the `.UseInMemoryDatabase()` extension method, reference the NuGet package [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/Testing/TestProject/InMemory/BlogServiceTests.cs)]
