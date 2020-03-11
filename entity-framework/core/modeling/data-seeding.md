---
title: データシード処理-EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 5c056c600f696ad1443ddb7b8c95c4b0ead06d21
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414573"
---
# <a name="data-seeding"></a><span data-ttu-id="16e6a-102">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="16e6a-102">Data Seeding</span></span>

<span data-ttu-id="16e6a-103">データシード処理とは、データベースに初期データセットを設定するプロセスのことです。</span><span class="sxs-lookup"><span data-stu-id="16e6a-103">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="16e6a-104">EF Core では、次のいくつかの方法で実現できます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-104">There are several ways this can be accomplished in EF Core:</span></span>

* <span data-ttu-id="16e6a-105">モデルシードデータ</span><span class="sxs-lookup"><span data-stu-id="16e6a-105">Model seed data</span></span>
* <span data-ttu-id="16e6a-106">手動による移行のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="16e6a-106">Manual migration customization</span></span>
* <span data-ttu-id="16e6a-107">カスタム初期化ロジック</span><span class="sxs-lookup"><span data-stu-id="16e6a-107">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="16e6a-108">モデルシードデータ</span><span class="sxs-lookup"><span data-stu-id="16e6a-108">Model seed data</span></span>

> [!NOTE]
> <span data-ttu-id="16e6a-109">これは EF Core 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="16e6a-109">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="16e6a-110">EF6 とは異なり、EF Core では、シード処理データをモデル構成の一部としてエンティティ型に関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-110">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="16e6a-111">その後、EF Core の[移行](xref:core/managing-schemas/migrations/index)では、データベースを新しいバージョンのモデルにアップグレードするときに、挿入、更新、または削除操作をどのように適用する必要があるかを自動的に計算できます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-111">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="16e6a-112">移行では、シードデータを目的の状態に取得するために実行する操作を決定する際に、モデルの変更のみを考慮します。</span><span class="sxs-lookup"><span data-stu-id="16e6a-112">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="16e6a-113">そのため、移行の外部で実行されたデータの変更が失われたり、エラーが発生したりする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="16e6a-113">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="16e6a-114">例として、次のように `OnModelCreating`の `Blog` のシードデータを構成します。</span><span class="sxs-lookup"><span data-stu-id="16e6a-114">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="16e6a-115">リレーションシップを持つエンティティを追加するには、外部キー値を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16e6a-115">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="16e6a-116">エンティティ型のプロパティが shadow 状態の場合、匿名クラスを使用して値を指定できます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-116">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="16e6a-117">所有されているエンティティ型は、同様の方法でシード処理できます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-117">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="16e6a-118">詳細なコンテキストについては、[完全なサンプルプロジェクト](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="16e6a-118">See the [full sample project](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="16e6a-119">データがモデルに追加されたら、[移行](xref:core/managing-schemas/migrations/index)を使用して変更を適用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16e6a-119">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="16e6a-120">自動デプロイの一部として移行を適用する必要がある場合は、実行前にプレビューできる[SQL スクリプトを作成](xref:core/managing-schemas/migrations/index#generate-sql-scripts)できます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-120">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="16e6a-121">または、`context.Database.EnsureCreated()` を使用して、テストデータベースなどのシードデータを含む新しいデータベースを作成したり、メモリ内のプロバイダーや非リレーションシップデータベースを使用したりすることもできます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-121">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="16e6a-122">データベースが既に存在する場合、`EnsureCreated()` によってデータベースのスキーマとシードデータが更新されないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="16e6a-122">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="16e6a-123">リレーショナルデータベースの場合、移行を使用する予定がある場合は `EnsureCreated()` を呼び出さないでください。</span><span class="sxs-lookup"><span data-stu-id="16e6a-123">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

### <a name="limitations-of-model-seed-data"></a><span data-ttu-id="16e6a-124">モデルシードデータの制限事項</span><span class="sxs-lookup"><span data-stu-id="16e6a-124">Limitations of model seed data</span></span>

<span data-ttu-id="16e6a-125">この種類のシードデータは、移行によって管理されます。また、データベースに既に存在するデータを更新するためのスクリプトは、データベースに接続せずに生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16e6a-125">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="16e6a-126">これにはいくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="16e6a-126">This imposes some restrictions:</span></span>

* <span data-ttu-id="16e6a-127">主キーの値は、通常、データベースによって生成される場合でも指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="16e6a-127">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="16e6a-128">移行間のデータ変更を検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-128">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="16e6a-129">以前にシードされたデータは、主キーが何らかの方法で変更されると削除されます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-129">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="16e6a-130">したがって、この機能は、移行の外部で変更されることが想定されておらず、データベース内の他のもの (郵便番号など) に依存しない静的データに対して最も役立ちます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-130">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="16e6a-131">シナリオに以下のいずれかが含まれている場合は、前のセクションで説明したカスタム初期化ロジックを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="16e6a-131">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>

* <span data-ttu-id="16e6a-132">テスト用の一時データ</span><span class="sxs-lookup"><span data-stu-id="16e6a-132">Temporary data for testing</span></span>
* <span data-ttu-id="16e6a-133">データベースの状態に依存するデータ</span><span class="sxs-lookup"><span data-stu-id="16e6a-133">Data that depends on database state</span></span>
* <span data-ttu-id="16e6a-134">Id として代替キーを使用するエンティティを含む、データベースによって生成されるキー値を必要とするデータ</span><span class="sxs-lookup"><span data-stu-id="16e6a-134">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="16e6a-135">一部のパスワードのハッシュなど、カスタム変換を必要とする ([値の変換](xref:core/modeling/value-conversions)によって処理されない) データ</span><span class="sxs-lookup"><span data-stu-id="16e6a-135">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="16e6a-136">ASP.NET Core Id ロールやユーザーの作成など、外部 API の呼び出しを必要とするデータ</span><span class="sxs-lookup"><span data-stu-id="16e6a-136">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="16e6a-137">手動による移行のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="16e6a-137">Manual migration customization</span></span>

<span data-ttu-id="16e6a-138">移行が追加されると、`HasData` で指定されたデータへの変更が `InsertData()`、`UpdateData()`、および `DeleteData()`への呼び出しに変換されます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-138">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="16e6a-139">`HasData` の制限事項を回避する方法の1つとして、これらの呼び出しや[カスタム操作](xref:core/managing-schemas/migrations/operations)を移行に手動で追加することが挙げられます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-139">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="16e6a-140">カスタム初期化ロジック</span><span class="sxs-lookup"><span data-stu-id="16e6a-140">Custom initialization logic</span></span>

<span data-ttu-id="16e6a-141">データシード処理を実行する簡単で強力な方法は、メインアプリケーションロジックの実行が開始される前に[`DbContext.SaveChanges()`](xref:core/saving/index)を使用することです。</span><span class="sxs-lookup"><span data-stu-id="16e6a-141">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="16e6a-142">シード処理コードは、複数のインスタンスが実行されている場合は同時実行の問題が発生する可能性があり、アプリケーションにデータベーススキーマを変更するアクセス許可も必要になるため、通常のアプリの実行には含めないでください。</span><span class="sxs-lookup"><span data-stu-id="16e6a-142">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="16e6a-143">配置の制約に応じて、初期化コードをさまざまな方法で実行できます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-143">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>

* <span data-ttu-id="16e6a-144">初期化アプリをローカルで実行する</span><span class="sxs-lookup"><span data-stu-id="16e6a-144">Running the initialization app locally</span></span>
* <span data-ttu-id="16e6a-145">メインアプリで初期化アプリをデプロイし、初期化ルーチンを呼び出し、初期化アプリを無効化または削除します。</span><span class="sxs-lookup"><span data-stu-id="16e6a-145">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="16e6a-146">これは通常、[発行プロファイル](/aspnet/core/host-and-deploy/visual-studio-publish-profiles)を使用して自動化できます。</span><span class="sxs-lookup"><span data-stu-id="16e6a-146">This can usually be automated by using [publish profiles](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
