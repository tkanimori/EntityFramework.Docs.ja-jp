---
title: テストサンプルの EF Core-EF Core
description: Entity Framework Core を使用するアプリケーションをテストする方法を示すサンプル
author: ajcvickers
ms.date: 04/22/2020
uid: core/testing/testing-sample
no-loc:
- ':::no-loc(Item):::'
- ':::no-loc(Tag):::'
- ':::no-loc(Items):::'
- ':::no-loc(Tags):::'
- ':::no-loc(items):::'
- ':::no-loc(tags):::'
ms.openlocfilehash: 9666bbde8ae9608dcebbea3ad37c51883960a942
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431329"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="ca434-103">EF Core テストのサンプル</span><span class="sxs-lookup"><span data-stu-id="ca434-103">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="ca434-104">このドキュメントのコードは、実行可能な [サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/)として GitHub にあります。</span><span class="sxs-lookup"><span data-stu-id="ca434-104">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/).</span></span>
> <span data-ttu-id="ca434-105">これらのテストの一部 **は失敗する** ことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ca434-105">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="ca434-106">この理由については、以下で説明します。</span><span class="sxs-lookup"><span data-stu-id="ca434-106">The reasons for this are explained below.</span></span>

<span data-ttu-id="ca434-107">このドキュメントでは、EF Core を使用するコードをテストするサンプルについて説明します。</span><span class="sxs-lookup"><span data-stu-id="ca434-107">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="ca434-108">アプリケーション</span><span class="sxs-lookup"><span data-stu-id="ca434-108">The application</span></span>

<span data-ttu-id="ca434-109">この [サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/) には、2つのプロジェクトが含まれています。</span><span class="sxs-lookup"><span data-stu-id="ca434-109">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/) contains two projects:</span></span>

- <span data-ttu-id="ca434-110">:::no-loc(Items):::WebApi: 1 つのコントローラーで [ASP.NET Core によってサポート](/aspnet/core/tutorials/first-web-api) される非常に単純な Web API</span><span class="sxs-lookup"><span data-stu-id="ca434-110">:::no-loc(Items):::WebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api) with a single controller</span></span>
- <span data-ttu-id="ca434-111">テスト: コントローラーをテストする [Xunit](https://xunit.net/) テストプロジェクト</span><span class="sxs-lookup"><span data-stu-id="ca434-111">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="ca434-112">モデルとビジネスルール</span><span class="sxs-lookup"><span data-stu-id="ca434-112">The model and business rules</span></span>

<span data-ttu-id="ca434-113">この API をサポートするモデルには、とという2つのエンティティ型があり :::no-loc(Items)::: :::no-loc(Tags)::: ます。</span><span class="sxs-lookup"><span data-stu-id="ca434-113">The model backing this API has two entity types: :::no-loc(Items)::: and :::no-loc(Tags):::.</span></span>

- <span data-ttu-id="ca434-114">:::no-loc(Items)::: 大文字と小文字を区別する名前とのコレクションを指定 :::no-loc(Tags)::: します。</span><span class="sxs-lookup"><span data-stu-id="ca434-114">:::no-loc(Items)::: have a case-sensitive name and a collection of :::no-loc(Tags):::.</span></span>
- <span data-ttu-id="ca434-115">各に :::no-loc(Tag)::: はラベルと、に適用された回数を表すカウントがあり :::no-loc(Item)::: ます。</span><span class="sxs-lookup"><span data-stu-id="ca434-115">Each :::no-loc(Tag)::: has a label and a count representing the number of times it has been applied to the :::no-loc(Item):::.</span></span>
- <span data-ttu-id="ca434-116">各 :::no-loc(Item)::: :::no-loc(Tag)::: には、指定されたラベルを持つが1つだけ必要です。</span><span class="sxs-lookup"><span data-stu-id="ca434-116">Each :::no-loc(Item)::: should only have one :::no-loc(Tag)::: with a given label.</span></span>
  - <span data-ttu-id="ca434-117">同じラベルの付いた項目に複数回タグが付けられている場合、そのラベルを持つ既存のタグの数が、新しいタグではなくインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="ca434-117">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span>
- <span data-ttu-id="ca434-118">を削除 :::no-loc(Item)::: すると、関連付けられているすべてのが削除 :::no-loc(Tags)::: します。</span><span class="sxs-lookup"><span data-stu-id="ca434-118">Deleting an :::no-loc(Item)::: should delete all associated :::no-loc(Tags):::.</span></span>

#### <a name="the-no-locitem-entity-type"></a><span data-ttu-id="ca434-119">:::no-loc(Item):::エンティティ型</span><span class="sxs-lookup"><span data-stu-id="ca434-119">The :::no-loc(Item)::: entity type</span></span>

<span data-ttu-id="ca434-120">`:::no-loc(Item):::`エンティティ型:</span><span class="sxs-lookup"><span data-stu-id="ca434-120">The `:::no-loc(Item):::` entity type:</span></span>

[!code-csharp[:::no-loc(Item):::EntityType](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/:::no-loc(Item):::.cs?name=:::no-loc(Item):::EntityType)]

<span data-ttu-id="ca434-121">とその構成 `DbContext.OnModelCreating` は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ca434-121">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[Configure:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/:::no-loc(Items):::Context.cs?name=Configure:::no-loc(Item):::)]

<span data-ttu-id="ca434-122">エンティティ型は、ドメインモデルとビジネスルールを反映するために使用できる方法に制約があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ca434-122">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="ca434-123">特に次の点に違いがあります。</span><span class="sxs-lookup"><span data-stu-id="ca434-123">In particular:</span></span>

- <span data-ttu-id="ca434-124">主キーはフィールドに直接マップされ、 `_id` 一般公開されません。</span><span class="sxs-lookup"><span data-stu-id="ca434-124">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="ca434-125">EF は、主キーの値と名前を受け入れるプライベートコンストラクターを検出して使用します。</span><span class="sxs-lookup"><span data-stu-id="ca434-125">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="ca434-126">`Name`プロパティは読み取り専用で、コンストラクターでのみ設定されます。</span><span class="sxs-lookup"><span data-stu-id="ca434-126">The `Name` property is read-only and set only in the constructor.</span></span>
- <span data-ttu-id="ca434-127">:::no-loc(Tags)::: は、任意の `IReadOnlyList<:::no-loc(Tag):::>` 変更を防ぐためにとして公開されます。</span><span class="sxs-lookup"><span data-stu-id="ca434-127">:::no-loc(Tags)::: are exposed as a `IReadOnlyList<:::no-loc(Tag):::>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="ca434-128">EF は、 `:::no-loc(Tags):::` `_:::no-loc(tags):::` 名前を一致させることによって、プロパティをバッキングフィールドに関連付けます。</span><span class="sxs-lookup"><span data-stu-id="ca434-128">EF associates the `:::no-loc(Tags):::` property with the `_:::no-loc(tags):::` backing field by matching their names.</span></span>
  - <span data-ttu-id="ca434-129">メソッドは、 `Add:::no-loc(Tag):::` タグラベルを受け取り、上で説明したビジネスルールを実装します。</span><span class="sxs-lookup"><span data-stu-id="ca434-129">The `Add:::no-loc(Tag):::` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="ca434-130">つまり、タグは新しいラベルに対してのみ追加されます。</span><span class="sxs-lookup"><span data-stu-id="ca434-130">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="ca434-131">それ以外の場合は、既存のラベルのカウントがインクリメントされます。</span><span class="sxs-lookup"><span data-stu-id="ca434-131">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="ca434-132">`:::no-loc(Tags):::`ナビゲーションプロパティは多対一のリレーションシップ用に構成されています。</span><span class="sxs-lookup"><span data-stu-id="ca434-132">The `:::no-loc(Tags):::` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="ca434-133">からへのナビゲーションプロパティは必要ありませ :::no-loc(Tag)::: ん :::no-loc(Item)::: 。そのため、このプロパティは含まれません。</span><span class="sxs-lookup"><span data-stu-id="ca434-133">There is no need for a navigation property from :::no-loc(Tag)::: to :::no-loc(Item):::, so it is not included.</span></span>
  - <span data-ttu-id="ca434-134">また、で :::no-loc(Tag)::: は外部キープロパティが定義されていません。</span><span class="sxs-lookup"><span data-stu-id="ca434-134">Also, :::no-loc(Tag)::: does not define a foreign key property.</span></span>
    <span data-ttu-id="ca434-135">代わりに、EF によってプロパティがシャドウ状態で作成および管理されます。</span><span class="sxs-lookup"><span data-stu-id="ca434-135">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-no-loctag-entity-type"></a><span data-ttu-id="ca434-136">:::no-loc(Tag):::エンティティ型</span><span class="sxs-lookup"><span data-stu-id="ca434-136">The :::no-loc(Tag)::: entity type</span></span>

<span data-ttu-id="ca434-137">`:::no-loc(Tag):::`エンティティ型:</span><span class="sxs-lookup"><span data-stu-id="ca434-137">The `:::no-loc(Tag):::` entity type:</span></span>

[!code-csharp[:::no-loc(Tag):::EntityType](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/:::no-loc(Tag):::.cs?name=:::no-loc(Tag):::EntityType)]

<span data-ttu-id="ca434-138">とその構成 `DbContext.OnModelCreating` は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="ca434-138">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[Configure:::no-loc(Tag):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/:::no-loc(Items):::Context.cs?name=Configure:::no-loc(Tag):::)]

<span data-ttu-id="ca434-139">と同様に :::no-loc(Item)::: 、では :::no-loc(Tag)::: 主キーが非表示になり、プロパティは読み取り専用になり `Label` ます。</span><span class="sxs-lookup"><span data-stu-id="ca434-139">Similarly to :::no-loc(Item):::, :::no-loc(Tag)::: hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-no-locitemscontroller"></a><span data-ttu-id="ca434-140">:::no-loc(Items):::コントローラー</span><span class="sxs-lookup"><span data-stu-id="ca434-140">The :::no-loc(Items):::Controller</span></span>

<span data-ttu-id="ca434-141">Web API コントローラーは非常に基本的なものです。</span><span class="sxs-lookup"><span data-stu-id="ca434-141">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="ca434-142">`DbContext`コンストラクターの挿入を通じて、依存関係の挿入コンテナーからを取得します。</span><span class="sxs-lookup"><span data-stu-id="ca434-142">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Constructor)]

<span data-ttu-id="ca434-143">このメソッドには :::no-loc(Items)::: 、指定された名前を持つすべてのまたはを取得するメソッドがあり :::no-loc(Item)::: ます。</span><span class="sxs-lookup"><span data-stu-id="ca434-143">It has methods to get all :::no-loc(Items)::: or an :::no-loc(Item)::: with a given name:</span></span>

[!code-csharp[Get](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Get)]

<span data-ttu-id="ca434-144">これには、新しいを追加するメソッドがあり :::no-loc(Item)::: ます。</span><span class="sxs-lookup"><span data-stu-id="ca434-144">It has a method to add a new :::no-loc(Item)::::</span></span>

[!code-csharp[Post:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Post:::no-loc(Item):::)]

<span data-ttu-id="ca434-145">ラベルを使用してにタグを付けるメソッド :::no-loc(Item)::: 。</span><span class="sxs-lookup"><span data-stu-id="ca434-145">A method to tag an :::no-loc(Item)::: with a label:</span></span>

[!code-csharp[Post:::no-loc(Tag):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Post:::no-loc(Tag):::)]

<span data-ttu-id="ca434-146">とのすべての関連を削除するメソッドを :::no-loc(Item)::: :::no-loc(Tags)::: 次に示します。</span><span class="sxs-lookup"><span data-stu-id="ca434-146">And a method to delete an :::no-loc(Item)::: and all associated :::no-loc(Tags)::::</span></span>

[!code-csharp[Delete:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/:::no-loc(Items):::WebApi/Controllers/:::no-loc(Items):::Controller.cs?name=Delete:::no-loc(Item):::)]

<span data-ttu-id="ca434-147">ほとんどの検証とエラー処理は、煩雑さを減らすために削除されています。</span><span class="sxs-lookup"><span data-stu-id="ca434-147">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="ca434-148">テスト</span><span class="sxs-lookup"><span data-stu-id="ca434-148">The Tests</span></span>

<span data-ttu-id="ca434-149">テストは、複数のデータベースプロバイダー構成を使用して実行するように構成されています。</span><span class="sxs-lookup"><span data-stu-id="ca434-149">The tests are organized to run with multiple database provider configurations:</span></span>

- <span data-ttu-id="ca434-150">アプリケーションによって使用されるプロバイダーである SQL Server プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ca434-150">The SQL Server provider, which is the provider used by the application</span></span>
- <span data-ttu-id="ca434-151">SQLite プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ca434-151">The SQLite provider</span></span>
- <span data-ttu-id="ca434-152">メモリ内 SQLite データベースを使用する SQLite プロバイダー</span><span class="sxs-lookup"><span data-stu-id="ca434-152">The SQLite provider using in-memory SQLite databases</span></span>
- <span data-ttu-id="ca434-153">EF のメモリ内データベースプロバイダー</span><span class="sxs-lookup"><span data-stu-id="ca434-153">The EF in-memory database provider</span></span>

<span data-ttu-id="ca434-154">これを実現するには、すべてのテストを基本クラスに配置し、これを継承して、各プロバイダーでテストします。</span><span class="sxs-lookup"><span data-stu-id="ca434-154">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="ca434-155">LocalDB を使用していない場合は、SQL Server 接続文字列を変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca434-155">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>
> <span data-ttu-id="ca434-156">メモリ内テストで SQLite を使用する方法については、「 [sqlite を使用したテスト](xref:core/testing/sqlite) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ca434-156">See [Testing with SQLite](xref:core/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span>

<span data-ttu-id="ca434-157">次の2つのテストが失敗することが予想されます。</span><span class="sxs-lookup"><span data-stu-id="ca434-157">The following two tests are expected to fail:</span></span>

- <span data-ttu-id="ca434-158">`Can_remove_item_and_all_associated_:::no-loc(tags):::` EF インメモリデータベースプロバイダーを使用して実行する場合</span><span class="sxs-lookup"><span data-stu-id="ca434-158">`Can_remove_item_and_all_associated_:::no-loc(tags):::` when running with the EF in-memory database provider</span></span>
- <span data-ttu-id="ca434-159">`Can_add_item_differing_only_by_case` SQL Server プロバイダーで実行する場合</span><span class="sxs-lookup"><span data-stu-id="ca434-159">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="ca434-160">詳細については、以下で詳しく説明します。</span><span class="sxs-lookup"><span data-stu-id="ca434-160">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="ca434-161">データベースの設定とシード処理</span><span class="sxs-lookup"><span data-stu-id="ca434-161">Setting up and seeding the database</span></span>

<span data-ttu-id="ca434-162">XUnit は、ほとんどのテストフレームワークと同様に、テストの実行ごとに新しいテストクラスのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="ca434-162">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="ca434-163">また、XUnit は、指定されたテストクラス内のテストを並行して実行しません。</span><span class="sxs-lookup"><span data-stu-id="ca434-163">Also, XUnit will not run tests within a given test class in parallel.</span></span>
<span data-ttu-id="ca434-164">これは、テストコンストラクターでデータベースをセットアップして構成できることを意味し、各テストの既知の状態になります。</span><span class="sxs-lookup"><span data-stu-id="ca434-164">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="ca434-165">このサンプルでは、テストごとにデータベースを再作成します。</span><span class="sxs-lookup"><span data-stu-id="ca434-165">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="ca434-166">これは SQLite および EF のメモリ内データベースのテストに適していますが、SQL Server を含む他のデータベースシステムでは大きなオーバーヘッドが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ca434-166">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="ca434-167">このオーバーヘッドを軽減するためのアプローチについては、「 [複数のテストでデータベースを共有](xref:core/testing/sharing-databases)する</span><span class="sxs-lookup"><span data-stu-id="ca434-167">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/testing/sharing-databases).</span></span>

<span data-ttu-id="ca434-168">各テストが実行されたとき:</span><span class="sxs-lookup"><span data-stu-id="ca434-168">When each test is run:</span></span>

- <span data-ttu-id="ca434-169">DbContextOptions は、使用中のプロバイダーに対して構成され、基底クラスのコンストラクターに渡されます。</span><span class="sxs-lookup"><span data-stu-id="ca434-169">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  - <span data-ttu-id="ca434-170">これらのオプションはプロパティに格納され、DbContext インスタンスを作成するためのテスト全体で使用されます。</span><span class="sxs-lookup"><span data-stu-id="ca434-170">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
- <span data-ttu-id="ca434-171">シードメソッドが呼び出され、データベースを作成およびシードします。</span><span class="sxs-lookup"><span data-stu-id="ca434-171">A Seed method is called to create and seed the database</span></span>
  - <span data-ttu-id="ca434-172">Seed メソッドは、データベースを削除してから再作成することにより、データベースをクリーンアップします。</span><span class="sxs-lookup"><span data-stu-id="ca434-172">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  - <span data-ttu-id="ca434-173">よく知られているいくつかのテストエンティティが作成され、データベースに保存されます。</span><span class="sxs-lookup"><span data-stu-id="ca434-173">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=Seeding)]

<span data-ttu-id="ca434-174">各具象テストクラスは、このから継承します。</span><span class="sxs-lookup"><span data-stu-id="ca434-174">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="ca434-175">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ca434-175">For example:</span></span>

[!code-csharp[Sqlite:::no-loc(Items):::ControllerTest](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/Sqlite:::no-loc(Items):::ControllerTest.cs?name=Sqlite:::no-loc(Items):::ControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="ca434-176">テスト構造</span><span class="sxs-lookup"><span data-stu-id="ca434-176">Test structure</span></span>

<span data-ttu-id="ca434-177">アプリケーションで依存関係の挿入が使用されている場合でも、テストは行われません。</span><span class="sxs-lookup"><span data-stu-id="ca434-177">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="ca434-178">ここでは依存関係の挿入を使用することはできますが、必要な追加のコードはほとんど価値がありません。</span><span class="sxs-lookup"><span data-stu-id="ca434-178">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="ca434-179">代わりに、を使用して DbContext が作成され、その `new` 後、依存関係としてコントローラーに直接渡されます。</span><span class="sxs-lookup"><span data-stu-id="ca434-179">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="ca434-180">各テストでは、コントローラーでテスト対象のメソッドが実行され、結果が想定どおりにアサートされます。</span><span class="sxs-lookup"><span data-stu-id="ca434-180">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="ca434-181">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ca434-181">For example:</span></span>

[!code-csharp[CanGet:::no-loc(Items):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanGet:::no-loc(Items):::)]

<span data-ttu-id="ca434-182">データベースのシードとテストの実行には、異なる DbContext インスタンスが使用されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ca434-182">Notice that different DbContext instances are used to seed the database and run the tests.</span></span>
<span data-ttu-id="ca434-183">これにより、シード処理の際に、コンテキストによって追跡されるエンティティがテストで使用されないようになります。</span><span class="sxs-lookup"><span data-stu-id="ca434-183">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="ca434-184">また、web アプリとサービスの動作にも適しています。</span><span class="sxs-lookup"><span data-stu-id="ca434-184">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="ca434-185">データベースを変化させるテストでは、同様の理由により、テストで2番目の DbContext インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="ca434-185">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="ca434-186">つまり、変更がデータベースに保存されたことを確認するために、新しいコンテキストを作成し、それをデータベースから読み取ります。</span><span class="sxs-lookup"><span data-stu-id="ca434-186">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span>
<span data-ttu-id="ca434-187">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ca434-187">For example:</span></span>

[!code-csharp[CanAdd:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanAdd:::no-loc(Item):::)]

<span data-ttu-id="ca434-188">もう少し複雑なテストでは、追加のビジネスロジックについて説明し :::no-loc(tags)::: ます。</span><span class="sxs-lookup"><span data-stu-id="ca434-188">Two slightly more involved tests cover the business logic around adding :::no-loc(tags):::.</span></span>

[!code-csharp[CanAdd:::no-loc(Tag):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanAdd:::no-loc(Tag):::)]

[!code-csharp[CanUp:::no-loc(Tag):::Count](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanUp:::no-loc(Tag):::Count)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="ca434-189">さまざまなデータベースプロバイダーを使用する場合の問題</span><span class="sxs-lookup"><span data-stu-id="ca434-189">Issues using different database providers</span></span>

<span data-ttu-id="ca434-190">実稼働アプリケーションで使用されているものとは異なるデータベースシステムを使用してテストすると、問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ca434-190">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="ca434-191">これらの詳細については、 [EF Core を使用するコードのテスト](xref:core/testing/index)に関する概念レベルで説明されています。</span><span class="sxs-lookup"><span data-stu-id="ca434-191">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/testing/index).</span></span>
<span data-ttu-id="ca434-192">以下のセクションでは、このサンプルのテストで示されている問題の2つの例について説明します。</span><span class="sxs-lookup"><span data-stu-id="ca434-192">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="ca434-193">アプリケーションが破損したときにテストに合格する</span><span class="sxs-lookup"><span data-stu-id="ca434-193">Test passes when the application is broken</span></span>

<span data-ttu-id="ca434-194">アプリケーションの要件の1つは、" :::no-loc(Items)::: 大文字と小文字を区別した名前とのコレクションを持つ" ということです :::no-loc(Tags)::: 。</span><span class="sxs-lookup"><span data-stu-id="ca434-194">One of the requirements for our application is that ":::no-loc(Items)::: have a case-sensitive name and a collection of :::no-loc(Tags):::."</span></span>
<span data-ttu-id="ca434-195">これは非常に簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="ca434-195">This is pretty simple to test:</span></span>

[!code-csharp[CanAdd:::no-loc(Item):::CaseInsensitive](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=CanAdd:::no-loc(Item):::CaseInsensitive)]

<span data-ttu-id="ca434-196">EF インメモリデータベースに対してこのテストを実行すると、すべての問題があることが示されます。</span><span class="sxs-lookup"><span data-stu-id="ca434-196">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="ca434-197">SQLite を使用すると、すべての問題が解決します。</span><span class="sxs-lookup"><span data-stu-id="ca434-197">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="ca434-198">ただし、SQL Server に対して実行した場合、テストは失敗します。</span><span class="sxs-lookup"><span data-stu-id="ca434-198">But the test fails when run against SQL Server!</span></span>

```output
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.:::no-loc(Items):::ControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="ca434-199">これは、EF のメモリ内データベースと SQLite データベースの両方で既定で大文字と小文字が区別されるためです。</span><span class="sxs-lookup"><span data-stu-id="ca434-199">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="ca434-200">一方、SQL Server では、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="ca434-200">SQL Server, on the other hand, is case-insensitive!</span></span>

<span data-ttu-id="ca434-201">EF Core では、大文字と小文字の区別の変更によってパフォーマンスが大幅に低下する可能性があるため、仕様により、これらの動作は変更されません。</span><span class="sxs-lookup"><span data-stu-id="ca434-201">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="ca434-202">これが問題であることを確認したら、アプリケーションを修正し、テストを補うことができます。</span><span class="sxs-lookup"><span data-stu-id="ca434-202">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="ca434-203">ただし、EF のインメモリデータベースまたは SQLite プロバイダーでテストする場合には、このバグが失われる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ca434-203">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="ca434-204">アプリケーションが正しい場合にテストが失敗する</span><span class="sxs-lookup"><span data-stu-id="ca434-204">Test fails when the application is correct</span></span>

<span data-ttu-id="ca434-205">アプリケーションの要件の1つは、「を削除すると、 :::no-loc(Item)::: 関連するすべてのものを削除する必要がある」ということです :::no-loc(Tags)::: 。</span><span class="sxs-lookup"><span data-stu-id="ca434-205">Another of the requirements for our application is that "deleting an :::no-loc(Item)::: should delete all associated :::no-loc(Tags):::."</span></span>
<span data-ttu-id="ca434-206">ここでも、簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="ca434-206">Again, easy to test:</span></span>

[!code-csharp[Delete:::no-loc(Item):::](../../../samples/core/Miscellaneous/Testing/:::no-loc(Items):::WebApi/Tests/:::no-loc(Items):::ControllerTest.cs?name=Delete:::no-loc(Item):::)]

<span data-ttu-id="ca434-207">このテストは SQL Server と SQLite で成功しますが、EF インメモリデータベースでは失敗します。</span><span class="sxs-lookup"><span data-stu-id="ca434-207">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```output
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.:::no-loc(Items):::ControllerTest.Can_remove_item_and_all_associated_:::no-loc(tags):::()
```

<span data-ttu-id="ca434-208">この場合、SQL Server は [連鎖削除](xref:core/saving/cascade-delete)をサポートしているので、アプリケーションが正常に動作しています。</span><span class="sxs-lookup"><span data-stu-id="ca434-208">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span>
<span data-ttu-id="ca434-209">SQLite は、ほとんどのリレーショナルデータベースと同様に連鎖削除もサポートしているため、SQLite でこれをテストします。</span><span class="sxs-lookup"><span data-stu-id="ca434-209">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="ca434-210">一方、EF のメモリ内データベースは、 [連鎖削除をサポートしていません](https://github.com/dotnet/efcore/issues/3924)。</span><span class="sxs-lookup"><span data-stu-id="ca434-210">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="ca434-211">つまり、アプリケーションのこの部分は、EF インメモリデータベースプロバイダーではテストできません。</span><span class="sxs-lookup"><span data-stu-id="ca434-211">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>
