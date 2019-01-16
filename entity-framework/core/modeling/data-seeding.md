---
title: データのシード処理 - EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/data-seeding
ms.openlocfilehash: 1c450b142573368d043430f55a3144b6696a8691
ms.sourcegitcommit: b4a5ed177b86bf7f81602106dab6b4acc18dfc18
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2019
ms.locfileid: "54316635"
---
# <a name="data-seeding"></a><span data-ttu-id="75688-102">データのシード処理</span><span class="sxs-lookup"><span data-stu-id="75688-102">Data Seeding</span></span>

<span data-ttu-id="75688-103">データのシード処理は、データの初期セットでのデータベースの設定のプロセスです。</span><span class="sxs-lookup"><span data-stu-id="75688-103">Data seeding is the process of populating a database with an initial set of data.</span></span>

<span data-ttu-id="75688-104">いくつかの方法を EF Core でこれを実現できます。</span><span class="sxs-lookup"><span data-stu-id="75688-104">There are several ways this can be accomplished in EF Core:</span></span>
* <span data-ttu-id="75688-105">モデルのシード データ</span><span class="sxs-lookup"><span data-stu-id="75688-105">Model seed data</span></span>
* <span data-ttu-id="75688-106">手動移行のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="75688-106">Manual migration customization</span></span>
* <span data-ttu-id="75688-107">カスタムの初期化ロジック</span><span class="sxs-lookup"><span data-stu-id="75688-107">Custom initialization logic</span></span>

## <a name="model-seed-data"></a><span data-ttu-id="75688-108">モデルのシード データ</span><span class="sxs-lookup"><span data-stu-id="75688-108">Model seed data</span></span>

> [!NOTE]
> <span data-ttu-id="75688-109">これは EF Core 2.1 の新機能です。</span><span class="sxs-lookup"><span data-stu-id="75688-109">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="75688-110">異なり、EF Core での EF6 でデータをシード処理関連付けることができますモデル構成の一部としてエンティティ型。</span><span class="sxs-lookup"><span data-stu-id="75688-110">Unlike in EF6, in EF Core, seeding data can be associated with an entity type as part of the model configuration.</span></span> <span data-ttu-id="75688-111">EF Core し[移行](xref:core/managing-schemas/migrations/index)どのような挿入、更新または削除、モデルの新しいバージョンにデータベースのアップグレード時に適用される操作の必要性を自動的に計算できます。</span><span class="sxs-lookup"><span data-stu-id="75688-111">Then EF Core [migrations](xref:core/managing-schemas/migrations/index) can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

> [!NOTE]
> <span data-ttu-id="75688-112">移行では、目的の状態にシード データを取得するは、どのような操作を実行するかを決定するときにモデルの変更のみ考慮されます。</span><span class="sxs-lookup"><span data-stu-id="75688-112">Migrations only considers model changes when determining what operation should be performed to get the seed data into the desired state.</span></span> <span data-ttu-id="75688-113">したがって移行の外部で実行するデータに対する変更は失われるまたはエラーが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="75688-113">Thus any changes to the data performed outside of migrations might be lost or cause an error.</span></span>

<span data-ttu-id="75688-114">たとえば、シード データに設定されます、`Blog`で`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="75688-114">As an example, this will configure seed data for a `Blog` in `OnModelCreating`:</span></span>

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

<span data-ttu-id="75688-115">リレーションシップの外部キーの値を持つエンティティを追加するを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="75688-115">To add entities that have a relationship the foreign key values need to be specified:</span></span>

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

<span data-ttu-id="75688-116">エンティティ型に任意のプロパティがある場合は、シャドウ状態では、値を提供する匿名のクラスを使用できます。</span><span class="sxs-lookup"><span data-stu-id="75688-116">If the entity type has any properties in shadow state an anonymous class can be used to provide the values:</span></span>

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

<span data-ttu-id="75688-117">所有エンティティの型は、同様の方法でシード処理できます。</span><span class="sxs-lookup"><span data-stu-id="75688-117">Owned entity types can be seeded in a similar fashion:</span></span>

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

<span data-ttu-id="75688-118">参照してください、[完全なサンプル プロジェクト](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding)詳細なコンテキスト。</span><span class="sxs-lookup"><span data-stu-id="75688-118">See the [full sample project](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) for more context.</span></span>

<span data-ttu-id="75688-119">データがモデルに追加されたら[移行](xref:core/managing-schemas/migrations/index)変更を適用するために使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="75688-119">Once the data has been added to the model, [migrations](xref:core/managing-schemas/migrations/index) should be used to apply the changes.</span></span>

> [!TIP]
> <span data-ttu-id="75688-120">自動化された展開の一部として移行を適用する必要がある場合は、 [SQL スクリプトを作成する](xref:core/managing-schemas/migrations/index#generate-sql-scripts)を実行する前にプレビューを表示できます。</span><span class="sxs-lookup"><span data-stu-id="75688-120">If you need to apply migrations as part of an automated deployment you can [create a SQL script](xref:core/managing-schemas/migrations/index#generate-sql-scripts) that can be previewed before execution.</span></span>

<span data-ttu-id="75688-121">使用する代わりに、`context.Database.EnsureCreated()`テスト データベースの例を使用するか、メモリ内プロバイダーまたは関係以外の任意のデータベースを使用して、シード データを含む新しいデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="75688-121">Alternatively, you can use `context.Database.EnsureCreated()` to create a new database containing the seed data, for example for a test database or when using the in-memory provider or any non-relation database.</span></span> <span data-ttu-id="75688-122">されている場合、データベースが既に存在する`EnsureCreated()`どちらも、データベース内のスキーマとシード データが更新されます。</span><span class="sxs-lookup"><span data-stu-id="75688-122">Note that if the database already exists, `EnsureCreated()` will neither update the schema nor seed data in the database.</span></span> <span data-ttu-id="75688-123">リレーショナル データベースに対して呼び出すべきではない`EnsureCreated()`Migrations を使用する場合。</span><span class="sxs-lookup"><span data-stu-id="75688-123">For relational databases you shouldn't call `EnsureCreated()` if you plan to use Migrations.</span></span>

### <a name="limitations-of-model-seed-data"></a><span data-ttu-id="75688-124">モデルのシード データの制限事項</span><span class="sxs-lookup"><span data-stu-id="75688-124">Limitations of model seed data</span></span>

<span data-ttu-id="75688-125">この種類のシード データは移行によって管理され、データベースに既にあるデータを更新するスクリプトは、データベースに接続しなくても生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="75688-125">This type of seed data is managed by migrations and the script to update the data that's already in the database needs to be generated without connecting to the database.</span></span> <span data-ttu-id="75688-126">これで、いくつかの制限が課せられます。</span><span class="sxs-lookup"><span data-stu-id="75688-126">This imposes some restrictions:</span></span>
* <span data-ttu-id="75688-127">主キーの値は、通常、データベースによって生成される場合でも指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="75688-127">The primary key value needs to be specified even if it's usually generated by the database.</span></span> <span data-ttu-id="75688-128">これは、移行の間のデータの変更を検出するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="75688-128">It will be used to detect data changes between migrations.</span></span>
* <span data-ttu-id="75688-129">以前の主キーが何らかの方法で変更された場合、シードされたデータは削除されます。</span><span class="sxs-lookup"><span data-stu-id="75688-129">Previously seeded data will be removed if the primary key is changed in any way.</span></span>

<span data-ttu-id="75688-130">したがってこの機能は、静的なデータ移行の外部で変更するのには想定されていませんが、データベースでは、郵便番号などの他の要素に依存しないに最も適しています。</span><span class="sxs-lookup"><span data-stu-id="75688-130">Therefore this feature is most useful for static data that's not expected to change outside of migrations and does not depend on anything else in the database, for example ZIP codes.</span></span>

<span data-ttu-id="75688-131">自分のシナリオでは、次のいずれかが含まれる場合は、最後のセクションで説明されているカスタムの初期化ロジックを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="75688-131">If your scenario includes any of the following it is recommended to use custom initialization logic described in the last section:</span></span>
* <span data-ttu-id="75688-132">テスト用の一時的なデータ</span><span class="sxs-lookup"><span data-stu-id="75688-132">Temporary data for testing</span></span>
* <span data-ttu-id="75688-133">データベースの状態に依存するデータ</span><span class="sxs-lookup"><span data-stu-id="75688-133">Data that depends on database state</span></span>
* <span data-ttu-id="75688-134">キーの値を id として代替キーを使用してエンティティを含む、データベースが生成する必要があるデータ</span><span class="sxs-lookup"><span data-stu-id="75688-134">Data that needs key values to be generated by the database, including entities that use alternate keys as the identity</span></span>
* <span data-ttu-id="75688-135">カスタム変換を必要とするデータ (によって処理されない[値変換](xref:core/modeling/value-conversions))、いくつかのパスワード ハッシュなど</span><span class="sxs-lookup"><span data-stu-id="75688-135">Data that requires custom transformation (that is not handled by [value conversions](xref:core/modeling/value-conversions)), such as some password hashing</span></span>
* <span data-ttu-id="75688-136">ASP.NET Core Id のロールとユーザーの作成などの外部 API の呼び出しを必要とするデータ</span><span class="sxs-lookup"><span data-stu-id="75688-136">Data that requires calls to external API, such as ASP.NET Core Identity roles and users creation</span></span>

## <a name="manual-migration-customization"></a><span data-ttu-id="75688-137">手動移行のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="75688-137">Manual migration customization</span></span>

<span data-ttu-id="75688-138">移行が追加されたときの変更で指定されたデータに`HasData`への呼び出しに変換される`InsertData()`、 `UpdateData()`、および`DeleteData()`します。</span><span class="sxs-lookup"><span data-stu-id="75688-138">When a migration is added the changes to the data specified with `HasData` are transformed to calls to `InsertData()`, `UpdateData()`, and `DeleteData()`.</span></span> <span data-ttu-id="75688-139">いくつかの制限を回避する方法の 1 つ`HasData`は、手動でこれらの呼び出しを追加するまたは[カスタム操作](xref:core/managing-schemas/migrations/operations)移行に代わりにします。</span><span class="sxs-lookup"><span data-stu-id="75688-139">One way of working around some of the limitations of `HasData` is to manually add these calls or [custom operations](xref:core/managing-schemas/migrations/operations) to the migration instead.</span></span>

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a><span data-ttu-id="75688-140">カスタムの初期化ロジック</span><span class="sxs-lookup"><span data-stu-id="75688-140">Custom initialization logic</span></span>

<span data-ttu-id="75688-141">データのシード処理を実行する簡単で強力な方法は使用する[ `DbContext.SaveChanges()` ](xref:core/saving/index)ロジックは、メイン アプリケーションの前に実行を開始します。</span><span class="sxs-lookup"><span data-stu-id="75688-141">A straightforward and powerful way to perform data seeding is to use [`DbContext.SaveChanges()`](xref:core/saving/index) before the main application logic begins execution.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> <span data-ttu-id="75688-142">シード処理のコードは、通常のアプリの実行の一部をしないでように複数のインスタンスを実行しているし、データベース スキーマを変更する権限のあるアプリも必要になりますと同時実行の問題が発生します。</span><span class="sxs-lookup"><span data-stu-id="75688-142">The seeding code should not be part of the normal app execution as this can cause concurrency issues when multiple instances are running and would also require the app having permission to modify the database schema.</span></span>

<span data-ttu-id="75688-143">配置の制約に応じて、さまざまな方法で初期化コードを実行できます。</span><span class="sxs-lookup"><span data-stu-id="75688-143">Depending on the constraints of your deployment the initialization code can be executed in different ways:</span></span>
* <span data-ttu-id="75688-144">初期化のアプリのローカル実行</span><span class="sxs-lookup"><span data-stu-id="75688-144">Running the initialization app locally</span></span>
* <span data-ttu-id="75688-145">初期化ルーチンを呼び出すと無効化または初期化アプリの削除は、メイン アプリケーションの初期化のアプリを展開します。</span><span class="sxs-lookup"><span data-stu-id="75688-145">Deploying the initialization app with the main app, invoking the initialization routine and disabling or removing the initialization app.</span></span>

<span data-ttu-id="75688-146">これを使用して、通常は自動化できます[発行プロファイル](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/visual-studio-publish-profiles)します。</span><span class="sxs-lookup"><span data-stu-id="75688-146">This can usually be automated by using [publish profiles](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/visual-studio-publish-profiles).</span></span>
