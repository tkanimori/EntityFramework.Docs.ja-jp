---
title: テスト間でのデータベースの共有-EF Core
description: 複数のテスト間でデータベースを共有する方法を示すサンプル
author: ajcvickers
ms.date: 04/25/2020
uid: core/miscellaneous/testing/sharing-databases
ms.openlocfilehash: 96216fb8afd6916402637de3ffab04b79d37e11c
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82564254"
---
# <a name="sharing-databases-between-tests"></a><span data-ttu-id="71080-103">テスト間でのデータベースの共有</span><span class="sxs-lookup"><span data-stu-id="71080-103">Sharing databases between tests</span></span>

<span data-ttu-id="71080-104">[EF Core テストのサンプル](xref:core/miscellaneous/testing/testing-sample)では、さまざまなデータベースシステムに対してアプリケーションをテストする方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="71080-104">The [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) showed how to test applications against different database systems.</span></span>
<span data-ttu-id="71080-105">このサンプルでは、各テストで新しいデータベースが作成されています。</span><span class="sxs-lookup"><span data-stu-id="71080-105">For that sample, each test created a new database.</span></span>
<span data-ttu-id="71080-106">SQLite または EF のメモリ内データベースを使用する場合は、このパターンが適していますが、他のデータベースシステムを使用する場合はかなりのオーバーヘッドが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="71080-106">This is a good pattern when using SQLite or the EF in-memory database, but it can involve significant overhead when using other database systems.</span></span>

<span data-ttu-id="71080-107">このサンプルは、データベースの作成をテストフィクスチャに移行することによって、前のサンプルに基づいています。</span><span class="sxs-lookup"><span data-stu-id="71080-107">This sample builds on the previous sample by moving database creation into a test fixture.</span></span>
<span data-ttu-id="71080-108">これにより、1つの SQL Server データベースを作成し、すべてのテストに1回だけシードすることができます。</span><span class="sxs-lookup"><span data-stu-id="71080-108">This allows a single SQL Server database to be created and seeded only once for all tests.</span></span>

> [!TIP]
> <span data-ttu-id="71080-109">ここで続行する前に、 [EF Core テストのサンプル](xref:core/miscellaneous/testing/testing-sample)を使用するようにしてください。</span><span class="sxs-lookup"><span data-stu-id="71080-109">Make sure to work through the [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) before continuing here.</span></span>

<span data-ttu-id="71080-110">同じデータベースに対して複数のテストを記述するのは困難です。</span><span class="sxs-lookup"><span data-stu-id="71080-110">It's not difficult to write multiple tests against the same database.</span></span>
<span data-ttu-id="71080-111">この方法では、テストを実行するたびにテストが行われないようにします。</span><span class="sxs-lookup"><span data-stu-id="71080-111">The trick is doing it in a way that the tests don't trip over each other as they run.</span></span>
<span data-ttu-id="71080-112">次のことを理解する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71080-112">This requires understanding:</span></span>
* <span data-ttu-id="71080-113">テスト間でオブジェクトを安全に共有する方法</span><span class="sxs-lookup"><span data-stu-id="71080-113">How to safely share objects between tests</span></span>
* <span data-ttu-id="71080-114">テストフレームワークが並列でテストを実行する場合</span><span class="sxs-lookup"><span data-stu-id="71080-114">When the test framework runs tests in parallel</span></span>
* <span data-ttu-id="71080-115">すべてのテストでデータベースをクリーンな状態に保つ方法</span><span class="sxs-lookup"><span data-stu-id="71080-115">How to keep the database in a clean state for every test</span></span>  

## <a name="the-fixture"></a><span data-ttu-id="71080-116">フィクスチャ</span><span class="sxs-lookup"><span data-stu-id="71080-116">The fixture</span></span>

<span data-ttu-id="71080-117">テスト間でオブジェクトを共有するためのテストフィクスチャを使用します。</span><span class="sxs-lookup"><span data-stu-id="71080-117">We will use a test fixture for sharing objects between tests.</span></span>
<span data-ttu-id="71080-118">[Xunit ドキュメント](https://xunit.net/docs/shared-context.html)では、1つのテストコンテキストを作成し、クラス内のすべてのテスト間で共有する場合に、クラス内のすべてのテストが終了した後に、フィクスチャを使用する必要があることを示しています。</span><span class="sxs-lookup"><span data-stu-id="71080-118">The [XUnit documentation](https://xunit.net/docs/shared-context.html) states that a fixture should be used "when you want to create a single test context and share it among all the tests in the class, and have it cleaned up after all the tests in the class have finished."</span></span>

> [!TIP]
> <span data-ttu-id="71080-119">このサンプルでは[Xunit](https://xunit.net/)を使用しますが、 [NUnit](https://nunit.org/)などの他のテストフレームワークにも同様の概念があります。</span><span class="sxs-lookup"><span data-stu-id="71080-119">This sample uses [XUnit](https://xunit.net/), but similar concepts exist in other testing frameworks, including [NUnit](https://nunit.org/).</span></span>

<span data-ttu-id="71080-120">これは、データベースの作成とシード処理をフィクスチャクラスに移動する必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="71080-120">This means that we need to move database creation and seeding to a fixture class.</span></span>
<span data-ttu-id="71080-121">次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="71080-121">Here's what it looks like:</span></span>

[!code-csharp[SharedDatabaseFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseFixture.cs?name=SharedDatabaseFixture)]

<span data-ttu-id="71080-122">ここでは、コンストラクターのしくみに注意してください。</span><span class="sxs-lookup"><span data-stu-id="71080-122">For now, notice how the constructor:</span></span>
* <span data-ttu-id="71080-123">フィクスチャの有効期間中、単一のデータベース接続を作成します。</span><span class="sxs-lookup"><span data-stu-id="71080-123">Creates a single database connection for the lifetime of the fixture</span></span>
* <span data-ttu-id="71080-124">`Seed`メソッドを呼び出してそのデータベースを作成し、シードします。</span><span class="sxs-lookup"><span data-stu-id="71080-124">Creates and seeds that database by calling the `Seed` method</span></span> 

<span data-ttu-id="71080-125">ここではロックを無視します。これについては後で説明します。</span><span class="sxs-lookup"><span data-stu-id="71080-125">Ignore the locking for now; we will come back to it later.</span></span>

> [!TIP]
> <span data-ttu-id="71080-126">作成およびシード処理のコードは非同期である必要はありません。</span><span class="sxs-lookup"><span data-stu-id="71080-126">The creation and seeding code does not need to be async.</span></span>
> <span data-ttu-id="71080-127">非同期にすると、コードが複雑になり、テストのパフォーマンスやスループットが向上することはありません。</span><span class="sxs-lookup"><span data-stu-id="71080-127">Making it async will complicate the code and will not improve performance or throughput of tests.</span></span>

<span data-ttu-id="71080-128">データベースを作成するには、まず既存のデータベースを削除してから、新しいデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="71080-128">The database is created by first deleting any existing database and then creating a new database.</span></span>
<span data-ttu-id="71080-129">これにより、前回のテストの実行後にデータベースが変更されていても、データベースが現在の EF モデルと一致するようになります。</span><span class="sxs-lookup"><span data-stu-id="71080-129">This ensures that the database matches the current EF model even if it has been changed since the last test run.</span></span>

> [!TIP]
> <span data-ttu-id="71080-130">既存のデータベースを再作成するので[はなく、](https://jimmybogard.com/tag/respawn/)再作成するのではなく、簡単に "クリーンアップ" できます。</span><span class="sxs-lookup"><span data-stu-id="71080-130">It can be faster to "clean" the existing database using something like [respawn](https://jimmybogard.com/tag/respawn/) rather than re-create it each time.</span></span>
> <span data-ttu-id="71080-131">ただし、この操作を行うときは、EF モデルを使用してデータベーススキーマを最新の状態に保つ必要があります。</span><span class="sxs-lookup"><span data-stu-id="71080-131">However, care must be taken to ensure that the database schema is up-to-date with the EF model when doing this.</span></span>

<span data-ttu-id="71080-132">データベース接続は、フィクスチャが破棄されると破棄されます。</span><span class="sxs-lookup"><span data-stu-id="71080-132">The database connection is disposed when the fixture is disposed.</span></span>
<span data-ttu-id="71080-133">この時点で、テストデータベースの削除を検討することもできます。</span><span class="sxs-lookup"><span data-stu-id="71080-133">You may also consider deleting the test database at this point.</span></span>
<span data-ttu-id="71080-134">ただし、フィクスチャが複数のテストクラスで共有されている場合は、追加のロックと参照カウントが必要になります。</span><span class="sxs-lookup"><span data-stu-id="71080-134">However, this will require additional locking and reference counting if the fixture is being shared by multiple test classes.</span></span>
<span data-ttu-id="71080-135">また、多くの場合、失敗したテストをデバッグするためにテストデータベースを使用できるようにすると便利です。</span><span class="sxs-lookup"><span data-stu-id="71080-135">Also, it is often useful to have the test database still available for debugging failed tests.</span></span>  

## <a name="using-the-fixture"></a><span data-ttu-id="71080-136">フィクスチャの使用</span><span class="sxs-lookup"><span data-stu-id="71080-136">Using the fixture</span></span>

<span data-ttu-id="71080-137">XUnit には、テストフィクスチャをテストのクラスに関連付けるための一般的なパターンがあります。</span><span class="sxs-lookup"><span data-stu-id="71080-137">XUnit has a common pattern for associating a test fixture with a class of tests:</span></span>

[!code-csharp[UsingTheFixture](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=UsingTheFixture)]

<span data-ttu-id="71080-138">これで、XUnit が1つのフィクスチャインスタンスを作成し、テストクラスの各インスタンスに渡します。</span><span class="sxs-lookup"><span data-stu-id="71080-138">XUnit will now create a single fixture instance and pass it to each instance of the test class.</span></span>
<span data-ttu-id="71080-139">(最初のテストの[例](xref:core/miscellaneous/testing/testing-sample)では、xunit がテストを実行するたびに新しいテストクラスのインスタンスを作成することに注意してください)。これは、データベースが作成され、1回シードされると、各テストでこのデータベースが使用されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="71080-139">(Remember from the first [testing sample](xref:core/miscellaneous/testing/testing-sample) that XUnit creates a new test class instance every time it runs a test.) This means that the database will be created and seeded once and then each test will use this database.</span></span>

<span data-ttu-id="71080-140">1つのクラス内のテストは並行して実行されないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="71080-140">Note that tests within a single class will not be run in parallel.</span></span>
<span data-ttu-id="71080-141">つまり、 `DbConnection`オブジェクトがスレッドセーフではない場合でも、各テストで同じデータベース接続を使用するのが安全です。</span><span class="sxs-lookup"><span data-stu-id="71080-141">This means it is safe for each test to use the same database connection, even though the `DbConnection` object is not thread-safe.</span></span>

## <a name="maintaining-database-state"></a><span data-ttu-id="71080-142">データベースの状態の維持</span><span class="sxs-lookup"><span data-stu-id="71080-142">Maintaining database state</span></span>

<span data-ttu-id="71080-143">テストでは、多くの場合、挿入、更新、および削除を使用してテストデータを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="71080-143">Tests often need to mutate the test data with inserts, updates, and deletes.</span></span>
<span data-ttu-id="71080-144">ただし、これらの変更は、クリーンなシード処理されたデータベースを想定している他のテストに影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="71080-144">But these changes will then impact other tests which are expecting a clean, seeded database.</span></span>

<span data-ttu-id="71080-145">これは、トランザクション内で変更テストを実行することで処理できます。</span><span class="sxs-lookup"><span data-stu-id="71080-145">This can be dealt with by running mutating tests inside a transaction.</span></span>
<span data-ttu-id="71080-146">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="71080-146">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/SharedDatabaseTests/SharedDatabaseTest.cs?name=CanAddItem)]

<span data-ttu-id="71080-147">テストが開始され、完了時に破棄されると、トランザクションが作成されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="71080-147">Notice that the transaction is created as the test starts and disposed when it is finished.</span></span>
<span data-ttu-id="71080-148">トランザクションを破棄すると、トランザクションがロールバックされます。そのため、他のテストによって変更が表示されることはありません。</span><span class="sxs-lookup"><span data-stu-id="71080-148">Disposing the transaction causes it to be rolled back, so none of the changes will be seen by other tests.</span></span>

<span data-ttu-id="71080-149">コンテキストを作成するためのヘルパーメソッド (上記のフィクスチャコードを参照) は、このトランザクションを受け入れ、それを使用するように DbContext を指定します。</span><span class="sxs-lookup"><span data-stu-id="71080-149">The helper method for creating a context (see the fixture code above) accepts this transaction and opts the DbContext into using it.</span></span> 

## <a name="sharing-the-fixture"></a><span data-ttu-id="71080-150">フィクスチャの共有</span><span class="sxs-lookup"><span data-stu-id="71080-150">Sharing the fixture</span></span>

<span data-ttu-id="71080-151">データベースの作成とシード処理に関するコードをロックしている可能性があります。</span><span class="sxs-lookup"><span data-stu-id="71080-151">You may have noticed locking code around database creation and seeding.</span></span>
<span data-ttu-id="71080-152">このサンプルではこの方法は必要ありません。これは、テストの1つのクラスのみがフィクスチャを使用するためです。そのため、フィクスチャインスタンスは1つだけ作成されます。</span><span class="sxs-lookup"><span data-stu-id="71080-152">This is not needed for this sample since only one class of tests use the fixture, so only a single fixture instance is created.</span></span>

<span data-ttu-id="71080-153">ただし、複数のクラスのテストで同じフィクスチャを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="71080-153">However, you may want to use the same fixture with multiple classes of tests.</span></span>
<span data-ttu-id="71080-154">XUnit は、これらのクラスごとに1つのフィクスチャインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="71080-154">XUnit will create one fixture instance for each of these classes.</span></span>
<span data-ttu-id="71080-155">これらは、テストを並列で実行するさまざまなスレッドによって使用される場合があります。</span><span class="sxs-lookup"><span data-stu-id="71080-155">These may be used by different threads running tests in parallel.</span></span>
<span data-ttu-id="71080-156">したがって、データベースの作成とシード処理を行うスレッドが1つだけになるように、適切なロックを設定することが重要です。</span><span class="sxs-lookup"><span data-stu-id="71080-156">Therefore, it is important to have appropriate locking to ensure only one thread does the database creation and seeding.</span></span>

> [!TIP]
> <span data-ttu-id="71080-157">ここで`lock`は簡単です。</span><span class="sxs-lookup"><span data-stu-id="71080-157">A simple `lock` is fine here.</span></span>
> <span data-ttu-id="71080-158">ロックフリーのパターンなど、複雑な操作を行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="71080-158">There is no need to attempt anything more complex, such as any lock-free patterns.</span></span>
