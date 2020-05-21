---
title: ''
description: ''
author: ''
ms.date: ''
uid: ''
no-loc:
- Item
- Tag
- Items
- Tags
ms.openlocfilehash: ae073fc0b3a99fb9de07a3e0a42c638fe0838a5a
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672819"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="4a884-101">EF Core テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="4a884-101">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="4a884-102">このドキュメントのコードは、実行可能な[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)として GitHub にあります。</span><span class="sxs-lookup"><span data-stu-id="4a884-102">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span></span>
> <span data-ttu-id="4a884-103">これらのテストの一部**は失敗する**ことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="4a884-103">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="4a884-104">この理由については、以下で説明します。</span><span class="sxs-lookup"><span data-stu-id="4a884-104">The reasons for this are explained below.</span></span> 

<span data-ttu-id="4a884-105">このドキュメントでは、EF Core を使用するコードをテストするサンプルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4a884-105">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="4a884-106">アプリケーション</span><span class="sxs-lookup"><span data-stu-id="4a884-106">The application</span></span>

<span data-ttu-id="4a884-107">この[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)には、2つのプロジェクトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="4a884-107">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contains two projects:</span></span>
- <span data-ttu-id="4a884-108">ItemsWebApi: 1 つのコントローラーで[ASP.NET Core によってサポート](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio)される非常に単純な Web API</span><span class="sxs-lookup"><span data-stu-id="4a884-108">ItemsWebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) with a single controller</span></span>
- <span data-ttu-id="4a884-109">テスト: コントローラーをテストする[Xunit](https://xunit.net/)テストプロジェクト</span><span class="sxs-lookup"><span data-stu-id="4a884-109">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="4a884-110">モデルとビジネスルール</span><span class="sxs-lookup"><span data-stu-id="4a884-110">The model and business rules</span></span>

<span data-ttu-id="4a884-111">この API をサポートするモデルには、とという2つのエンティティ型があり Items Tags ます。</span><span class="sxs-lookup"><span data-stu-id="4a884-111">The model backing this API has two entity types: Items and Tags.</span></span>

* Items<span data-ttu-id="4a884-112">大文字と小文字を区別する名前とのコレクションを指定 Tags します。</span><span class="sxs-lookup"><span data-stu-id="4a884-112"> have a case-sensitive name and a collection of Tags.</span></span>
* <span data-ttu-id="4a884-113">各に Tag はラベルと、に適用された回数を表すカウントがあり Item ます。</span><span class="sxs-lookup"><span data-stu-id="4a884-113">Each Tag has a label and a count representing the number of times it has been applied to the Item.</span></span>
* <span data-ttu-id="4a884-114">各 Item Tag には、指定されたラベルを持つが1つだけ必要です。</span><span class="sxs-lookup"><span data-stu-id="4a884-114">Each Item should only have one Tag with a given label.</span></span>
  * <span data-ttu-id="4a884-115">同じラベルの付いた項目に複数回タグが付けられている場合、そのラベルを持つ既存のタグの数が、新しいタグではなくインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="4a884-115">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span> 
* <span data-ttu-id="4a884-116">を削除 Item すると、関連付けられているすべてのが削除 Tags します。</span><span class="sxs-lookup"><span data-stu-id="4a884-116">Deleting an Item should delete all associated Tags.</span></span>

#### <a name="the-item-entity-type"></a><span data-ttu-id="4a884-117">Itemエンティティ型</span><span class="sxs-lookup"><span data-stu-id="4a884-117">The Item entity type</span></span>

<span data-ttu-id="4a884-118">`Item`エンティティ型:</span><span class="sxs-lookup"><span data-stu-id="4a884-118">The `Item` entity type:</span></span>

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

<span data-ttu-id="4a884-119">とその構成 `DbContext.OnModelCreating` は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4a884-119">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

<span data-ttu-id="4a884-120">エンティティ型は、ドメインモデルとビジネスルールを反映するために使用できる方法に制約があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="4a884-120">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="4a884-121">特に次の点に違いがあります。</span><span class="sxs-lookup"><span data-stu-id="4a884-121">In particular:</span></span>
- <span data-ttu-id="4a884-122">主キーはフィールドに直接マップされ、 `_id` 一般公開されません。</span><span class="sxs-lookup"><span data-stu-id="4a884-122">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="4a884-123">EF は、主キーの値と名前を受け入れるプライベートコンストラクターを検出して使用します。</span><span class="sxs-lookup"><span data-stu-id="4a884-123">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="4a884-124">`Name`プロパティは読み取り専用で、コンストラクターでのみ設定されます。</span><span class="sxs-lookup"><span data-stu-id="4a884-124">The `Name` property is read-only and set only in the constructor.</span></span> 
- Tags<span data-ttu-id="4a884-125">は、任意の `IReadOnlyList<Tag>` 変更を防ぐためにとして公開されます。</span><span class="sxs-lookup"><span data-stu-id="4a884-125"> are exposed as a `IReadOnlyList<Tag>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="4a884-126">EF は、 `Tags` `_tags` 名前を一致させることによって、プロパティをバッキングフィールドに関連付けます。</span><span class="sxs-lookup"><span data-stu-id="4a884-126">EF associates the `Tags` property with the `_tags` backing field by matching their names.</span></span> 
  - <span data-ttu-id="4a884-127">メソッドは、 `AddTag` タグラベルを受け取り、上で説明したビジネスルールを実装します。</span><span class="sxs-lookup"><span data-stu-id="4a884-127">The `AddTag` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="4a884-128">つまり、タグは新しいラベルに対してのみ追加されます。</span><span class="sxs-lookup"><span data-stu-id="4a884-128">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="4a884-129">それ以外の場合は、既存のラベルのカウントがインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="4a884-129">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="4a884-130">`Tags`ナビゲーションプロパティは多対一のリレーションシップ用に構成されています。</span><span class="sxs-lookup"><span data-stu-id="4a884-130">The `Tags` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="4a884-131">からへのナビゲーションプロパティは必要ありませ Tag ん Item 。そのため、このプロパティは含まれません。</span><span class="sxs-lookup"><span data-stu-id="4a884-131">There is no need for a navigation property from Tag to Item, so it is not included.</span></span>
  - <span data-ttu-id="4a884-132">また、で Tag は外部キープロパティが定義されていません。</span><span class="sxs-lookup"><span data-stu-id="4a884-132">Also, Tag does not define a foreign key property.</span></span>
    <span data-ttu-id="4a884-133">代わりに、EF によってプロパティがシャドウ状態で作成および管理されます。</span><span class="sxs-lookup"><span data-stu-id="4a884-133">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-tag-entity-type"></a><span data-ttu-id="4a884-134">Tagエンティティ型</span><span class="sxs-lookup"><span data-stu-id="4a884-134">The Tag entity type</span></span>

<span data-ttu-id="4a884-135">`Tag`エンティティ型:</span><span class="sxs-lookup"><span data-stu-id="4a884-135">The `Tag` entity type:</span></span>

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

<span data-ttu-id="4a884-136">とその構成 `DbContext.OnModelCreating` は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="4a884-136">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

<span data-ttu-id="4a884-137">と同様に Item 、では Tag 主キーが非表示になり、プロパティは読み取り専用になり `Label` ます。</span><span class="sxs-lookup"><span data-stu-id="4a884-137">Similarly to Item, Tag hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-itemscontroller"></a><span data-ttu-id="4a884-138">ItemsController</span><span class="sxs-lookup"><span data-stu-id="4a884-138">The ItemsController</span></span>

<span data-ttu-id="4a884-139">Web API コントローラーは非常に基本的なものです。</span><span class="sxs-lookup"><span data-stu-id="4a884-139">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="4a884-140">`DbContext`コンストラクターの挿入を通じて、依存関係の挿入コンテナーからを取得します。</span><span class="sxs-lookup"><span data-stu-id="4a884-140">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

<span data-ttu-id="4a884-141">このメソッドには Items 、指定された名前を持つすべてのまたはを取得するメソッドがあり Item ます。</span><span class="sxs-lookup"><span data-stu-id="4a884-141">It has methods to get all Items or an Item with a given name:</span></span>

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

<span data-ttu-id="4a884-142">これには、新しいを追加するメソッドがあり Item ます。</span><span class="sxs-lookup"><span data-stu-id="4a884-142">It has a method to add a new Item:</span></span>

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

<span data-ttu-id="4a884-143">ラベルを使用してにタグを付けるメソッド Item 。</span><span class="sxs-lookup"><span data-stu-id="4a884-143">A method to tag an Item with a label:</span></span>

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

<span data-ttu-id="4a884-144">とのすべての関連を削除するメソッドを Item Tags 次に示します。</span><span class="sxs-lookup"><span data-stu-id="4a884-144">And a method to delete an Item and all associated Tags:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

<span data-ttu-id="4a884-145">ほとんどの検証とエラー処理は、煩雑さを減らすために削除されています。</span><span class="sxs-lookup"><span data-stu-id="4a884-145">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="4a884-146">テスト</span><span class="sxs-lookup"><span data-stu-id="4a884-146">The Tests</span></span>

<span data-ttu-id="4a884-147">テストは、複数のデータベースプロバイダー構成を使用して実行するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="4a884-147">The tests are organized to run with multiple database provider configurations:</span></span>
* <span data-ttu-id="4a884-148">アプリケーションによって使用されるプロバイダーである SQL Server プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4a884-148">The SQL Server provider, which is the provider used by the application</span></span>
* <span data-ttu-id="4a884-149">SQLite プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4a884-149">The SQLite provider</span></span>
* <span data-ttu-id="4a884-150">メモリ内 SQLite データベースを使用する SQLite プロバイダー</span><span class="sxs-lookup"><span data-stu-id="4a884-150">The SQLite provider using in-memory SQLite databases</span></span>
* <span data-ttu-id="4a884-151">EF のメモリ内データベースプロバイダー</span><span class="sxs-lookup"><span data-stu-id="4a884-151">The EF in-memory database provider</span></span>

<span data-ttu-id="4a884-152">これを実現するには、すべてのテストを基本クラスに配置し、これを継承して、各プロバイダーでテストします。</span><span class="sxs-lookup"><span data-stu-id="4a884-152">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="4a884-153">LocalDB を使用していない場合は、SQL Server 接続文字列を変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4a884-153">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>

> [!TIP]
> <span data-ttu-id="4a884-154">メモリ内テストで SQLite を使用する方法については、「 [sqlite を使用したテスト](xref:core/miscellaneous/testing/sqlite)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4a884-154">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span> 

<span data-ttu-id="4a884-155">次の2つのテストが失敗することが予想されます。</span><span class="sxs-lookup"><span data-stu-id="4a884-155">The following two tests are expected to fail:</span></span>
* <span data-ttu-id="4a884-156">`Can_remove_item_and_all_associated_tags`EF インメモリデータベースプロバイダーを使用して実行する場合</span><span class="sxs-lookup"><span data-stu-id="4a884-156">`Can_remove_item_and_all_associated_tags` when running with the EF in-memory database provider</span></span>
* <span data-ttu-id="4a884-157">`Can_add_item_differing_only_by_case`SQL Server プロバイダーで実行する場合</span><span class="sxs-lookup"><span data-stu-id="4a884-157">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="4a884-158">詳細については、以下で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="4a884-158">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="4a884-159">データベースの設定とシード処理</span><span class="sxs-lookup"><span data-stu-id="4a884-159">Setting up and seeding the database</span></span>

<span data-ttu-id="4a884-160">XUnit は、ほとんどのテストフレームワークと同様に、テストの実行ごとに新しいテストクラスのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="4a884-160">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="4a884-161">また、XUnit は、指定されたテストクラス内のテストを並行して実行しません。</span><span class="sxs-lookup"><span data-stu-id="4a884-161">Also, XUnit will not run tests within a given test class in parallel.</span></span> <span data-ttu-id="4a884-162">これは、テストコンストラクターでデータベースをセットアップして構成できることを意味し、各テストの既知の状態になります。</span><span class="sxs-lookup"><span data-stu-id="4a884-162">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="4a884-163">このサンプルでは、テストごとにデータベースを再作成します。</span><span class="sxs-lookup"><span data-stu-id="4a884-163">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="4a884-164">これは SQLite および EF のメモリ内データベースのテストに適していますが、SQL Server を含む他のデータベースシステムでは大きなオーバーヘッドが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4a884-164">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="4a884-165">このオーバーヘッドを軽減するためのアプローチについては、「[複数のテストでデータベースを共有](xref:core/miscellaneous/testing/sharing-databases)する</span><span class="sxs-lookup"><span data-stu-id="4a884-165">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/miscellaneous/testing/sharing-databases).</span></span>

<span data-ttu-id="4a884-166">各テストが実行されたとき:</span><span class="sxs-lookup"><span data-stu-id="4a884-166">When each test is run:</span></span>
* <span data-ttu-id="4a884-167">DbContextOptions は、使用中のプロバイダーに対して構成され、基底クラスのコンストラクターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="4a884-167">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  * <span data-ttu-id="4a884-168">これらのオプションはプロパティに格納され、DbContext インスタンスを作成するためのテスト全体で使用されます。</span><span class="sxs-lookup"><span data-stu-id="4a884-168">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
* <span data-ttu-id="4a884-169">シードメソッドが呼び出され、データベースを作成およびシードします。</span><span class="sxs-lookup"><span data-stu-id="4a884-169">A Seed method is called to create and seed the database</span></span>
  * <span data-ttu-id="4a884-170">Seed メソッドは、データベースを削除してから再作成することにより、データベースをクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="4a884-170">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  * <span data-ttu-id="4a884-171">よく知られているいくつかのテストエンティティが作成され、データベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="4a884-171">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

<span data-ttu-id="4a884-172">各具象テストクラスは、このから継承します。</span><span class="sxs-lookup"><span data-stu-id="4a884-172">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="4a884-173">たとえば、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="4a884-173">For example:</span></span>

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="4a884-174">テスト構造</span><span class="sxs-lookup"><span data-stu-id="4a884-174">Test structure</span></span>

<span data-ttu-id="4a884-175">アプリケーションで依存関係の挿入が使用されている場合でも、テストは行われません。</span><span class="sxs-lookup"><span data-stu-id="4a884-175">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="4a884-176">ここでは依存関係の挿入を使用することはできますが、必要な追加のコードはほとんど価値がありません。</span><span class="sxs-lookup"><span data-stu-id="4a884-176">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="4a884-177">代わりに、を使用して DbContext が作成され、その `new` 後、依存関係としてコントローラーに直接渡されます。</span><span class="sxs-lookup"><span data-stu-id="4a884-177">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="4a884-178">各テストでは、コントローラーでテスト対象のメソッドが実行され、結果が想定どおりにアサートされます。</span><span class="sxs-lookup"><span data-stu-id="4a884-178">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="4a884-179">たとえば、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="4a884-179">For example:</span></span>

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

<span data-ttu-id="4a884-180">データベースのシードとテストの実行には、異なる DbContext インスタンスが使用されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="4a884-180">Notice that different DbContext instances are used to seed the database and run the tests.</span></span> <span data-ttu-id="4a884-181">これにより、シード処理の際に、コンテキストによって追跡されるエンティティがテストで使用されないようになります。</span><span class="sxs-lookup"><span data-stu-id="4a884-181">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="4a884-182">また、web アプリとサービスの動作にも適しています。</span><span class="sxs-lookup"><span data-stu-id="4a884-182">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="4a884-183">データベースを変化させるテストでは、同様の理由により、テストで2番目の DbContext インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="4a884-183">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="4a884-184">つまり、変更がデータベースに保存されたことを確認するために、新しいコンテキストを作成し、それをデータベースから読み取ります。</span><span class="sxs-lookup"><span data-stu-id="4a884-184">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span> <span data-ttu-id="4a884-185">たとえば、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="4a884-185">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

<span data-ttu-id="4a884-186">もう少し複雑なテストでは、タグの追加に関するビジネスロジックについて説明します。</span><span class="sxs-lookup"><span data-stu-id="4a884-186">Two slightly more involved tests cover the business logic around adding tags.</span></span>

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="4a884-187">さまざまなデータベースプロバイダーを使用する場合の問題</span><span class="sxs-lookup"><span data-stu-id="4a884-187">Issues using different database providers</span></span>

<span data-ttu-id="4a884-188">実稼働アプリケーションで使用されているものとは異なるデータベースシステムを使用してテストすると、問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4a884-188">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="4a884-189">これらの詳細については、 [EF Core を使用するコードのテスト](xref:core/miscellaneous/testing/index)に関する概念レベルで説明されています。</span><span class="sxs-lookup"><span data-stu-id="4a884-189">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  
<span data-ttu-id="4a884-190">以下のセクションでは、このサンプルのテストで示されている問題の2つの例について説明します。</span><span class="sxs-lookup"><span data-stu-id="4a884-190">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="4a884-191">アプリケーションが破損したときにテストに合格する</span><span class="sxs-lookup"><span data-stu-id="4a884-191">Test passes when the application is broken</span></span>

<span data-ttu-id="4a884-192">アプリケーションの要件の1つは、" Items 大文字と小文字を区別した名前とのコレクションを持つ" ということです Tags 。</span><span class="sxs-lookup"><span data-stu-id="4a884-192">One of the requirements for our application is that "Items have a case-sensitive name and a collection of Tags."</span></span>
<span data-ttu-id="4a884-193">これは非常に簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="4a884-193">This is pretty simple to test:</span></span>

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

<span data-ttu-id="4a884-194">EF インメモリデータベースに対してこのテストを実行すると、すべての問題があることが示されます。</span><span class="sxs-lookup"><span data-stu-id="4a884-194">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="4a884-195">SQLite を使用すると、すべての問題が解決します。</span><span class="sxs-lookup"><span data-stu-id="4a884-195">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="4a884-196">ただし、SQL Server に対して実行した場合、テストは失敗します。</span><span class="sxs-lookup"><span data-stu-id="4a884-196">But the test fails when run against SQL Server!</span></span>

```
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="4a884-197">これは、EF のメモリ内データベースと SQLite データベースの両方で既定で大文字と小文字が区別されるためです。</span><span class="sxs-lookup"><span data-stu-id="4a884-197">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="4a884-198">一方、SQL Server では、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="4a884-198">SQL Server, on the other hand, is case-insensitive!</span></span> 

<span data-ttu-id="4a884-199">EF Core では、大文字と小文字の区別の変更によってパフォーマンスが大幅に低下する可能性があるため、仕様により、これらの動作は変更されません。</span><span class="sxs-lookup"><span data-stu-id="4a884-199">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="4a884-200">これが問題であることを確認したら、アプリケーションを修正し、テストを補うことができます。</span><span class="sxs-lookup"><span data-stu-id="4a884-200">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="4a884-201">ただし、EF のインメモリデータベースまたは SQLite プロバイダーでテストする場合には、このバグが失われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4a884-201">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="4a884-202">アプリケーションが正しい場合にテストが失敗する</span><span class="sxs-lookup"><span data-stu-id="4a884-202">Test fails when the application is correct</span></span> 

<span data-ttu-id="4a884-203">アプリケーションの要件の1つは、「を削除すると、 Item 関連するすべてのものを削除する必要がある」ということです Tags 。</span><span class="sxs-lookup"><span data-stu-id="4a884-203">Another of the requirements for our application is that "deleting an Item should delete all associated Tags."</span></span>
<span data-ttu-id="4a884-204">ここでも、簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="4a884-204">Again, easy to test:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

<span data-ttu-id="4a884-205">このテストは SQL Server と SQLite で成功しますが、EF インメモリデータベースでは失敗します。</span><span class="sxs-lookup"><span data-stu-id="4a884-205">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

<span data-ttu-id="4a884-206">この場合、SQL Server は[連鎖削除](xref:core/saving/cascade-delete)をサポートしているので、アプリケーションが正常に動作しています。</span><span class="sxs-lookup"><span data-stu-id="4a884-206">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span> <span data-ttu-id="4a884-207">SQLite は、ほとんどのリレーショナルデータベースと同様に連鎖削除もサポートしているため、SQLite でこれをテストします。</span><span class="sxs-lookup"><span data-stu-id="4a884-207">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="4a884-208">一方、EF のメモリ内データベースは、[連鎖削除をサポートしていません](https://github.com/dotnet/efcore/issues/3924)。</span><span class="sxs-lookup"><span data-stu-id="4a884-208">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="4a884-209">つまり、アプリケーションのこの部分は、EF インメモリデータベースプロバイダーではテストできません。</span><span class="sxs-lookup"><span data-stu-id="4a884-209">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
