---
title: EF Core 1.0 の新機能 - EF Core
description: Entity Framework Core 1.0 での変更点と改善点
author: ajcvickers
ms.date: 10/27/2016
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: 4066dced67a54770664b9b1348adb6ac2622ce1a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062933"
---
# <a name="features-included-in-ef-core-10"></a><span data-ttu-id="30d16-103">EF Core 1.0 に含まれる機能</span><span class="sxs-lookup"><span data-stu-id="30d16-103">Features included in EF Core 1.0</span></span>

## <a name="platforms"></a><span data-ttu-id="30d16-104">プラットフォーム</span><span class="sxs-lookup"><span data-stu-id="30d16-104">Platforms</span></span>

### <a name="net-framework-451"></a><span data-ttu-id="30d16-105">.NET Framework 4.5.1</span><span class="sxs-lookup"><span data-stu-id="30d16-105">.NET Framework 4.5.1</span></span>

<span data-ttu-id="30d16-106">コンソール、WPF、WinForms、ASP.NET 4 などが含まれます。</span><span class="sxs-lookup"><span data-stu-id="30d16-106">Includes Console, WPF, WinForms, ASP.NET 4, etc.</span></span>

### <a name="net-standard-13"></a><span data-ttu-id="30d16-107">.NET Standard 1.3</span><span class="sxs-lookup"><span data-stu-id="30d16-107">.NET Standard 1.3</span></span>

<span data-ttu-id="30d16-108">.NET Framework と、Windows、OSX および Linux での .NET Core の両方を対象にした ASP.NET Core が含まれています。</span><span class="sxs-lookup"><span data-stu-id="30d16-108">Including ASP.NET Core targeting both .NET Framework and .NET Core on Windows, OSX, and Linux.</span></span>

## <a name="modelling"></a><span data-ttu-id="30d16-109">モデリング</span><span class="sxs-lookup"><span data-stu-id="30d16-109">Modelling</span></span>

### <a name="basic-modelling"></a><span data-ttu-id="30d16-110">基本モデリング</span><span class="sxs-lookup"><span data-stu-id="30d16-110">Basic modelling</span></span>

<span data-ttu-id="30d16-111">共通のスカラー型 (`int`、`string` など) の get/set プロパティを持つ POCO エンティティに基づいています。</span><span class="sxs-lookup"><span data-stu-id="30d16-111">Based on POCO entities with get/set properties of common scalar types (`int`, `string`, etc.).</span></span>

### <a name="relationships-and-navigation-properties"></a><span data-ttu-id="30d16-112">リレーションシップとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="30d16-112">Relationships and navigation properties</span></span>

<span data-ttu-id="30d16-113">外部キーに基づく一対多、一対ゼロまたは一対一のリレーションシップをモデルに指定できます。</span><span class="sxs-lookup"><span data-stu-id="30d16-113">One-to-many and One-to-zero-or-one relationships can be specified in the model based on a foreign key.</span></span> <span data-ttu-id="30d16-114">単純なコレクションまたは参照型のナビゲーション プロパティを、これらのリレーションシップに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="30d16-114">Navigation properties of simple collection or reference types can be associated with these relationships.</span></span>

### <a name="built-in-conventions"></a><span data-ttu-id="30d16-115">組み込みの規約</span><span class="sxs-lookup"><span data-stu-id="30d16-115">Built-in conventions</span></span>

<span data-ttu-id="30d16-116">組み込みの規約は、エンティティ クラスの構造に基づいて、初期モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="30d16-116">These build an initial model based on the shape of the entity classes.</span></span>

### <a name="fluent-api"></a><span data-ttu-id="30d16-117">Fluent API</span><span class="sxs-lookup"><span data-stu-id="30d16-117">Fluent API</span></span>

<span data-ttu-id="30d16-118">使用するコンテキストの `OnModelCreating` メソッドをオーバーライドして、規約によって検出されたモデルをさらに構成できます。</span><span class="sxs-lookup"><span data-stu-id="30d16-118">Allows you to override the `OnModelCreating` method on your context to further configure the model that was discovered by convention.</span></span>

### <a name="data-annotations"></a><span data-ttu-id="30d16-119">データの注釈</span><span class="sxs-lookup"><span data-stu-id="30d16-119">Data annotations</span></span>

<span data-ttu-id="30d16-120">エンティティのクラスやプロパティに追加できる属性で、EF モデルに影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="30d16-120">Are attributes that can be added to your entity classes/properties and will influence the EF model.</span></span> <span data-ttu-id="30d16-121">たとえば `[Required]` を追加すると、プロパティが必須であることが EF に通知されます。</span><span class="sxs-lookup"><span data-stu-id="30d16-121">For example, adding `[Required]` will let EF know that a property is required.</span></span>

### <a name="relational-table-mapping"></a><span data-ttu-id="30d16-122">リレーショナル テーブルのマッピング</span><span class="sxs-lookup"><span data-stu-id="30d16-122">Relational Table mapping</span></span>

<span data-ttu-id="30d16-123">エンティティをテーブルや列にマップできます。</span><span class="sxs-lookup"><span data-stu-id="30d16-123">Allows entities to be mapped to tables/columns.</span></span>

### <a name="key-value-generation"></a><span data-ttu-id="30d16-124">キー値の生成</span><span class="sxs-lookup"><span data-stu-id="30d16-124">Key value generation</span></span>

<span data-ttu-id="30d16-125">クライアント側での生成と、データベースでの生成が含まれます。</span><span class="sxs-lookup"><span data-stu-id="30d16-125">Including client-side generation and database generation.</span></span>

### <a name="database-generated-values"></a><span data-ttu-id="30d16-126">データベースで生成される値</span><span class="sxs-lookup"><span data-stu-id="30d16-126">Database generated values</span></span>

<span data-ttu-id="30d16-127">データベースでの挿入 (既定値) や更新 (計算列) によって値を生成できます。</span><span class="sxs-lookup"><span data-stu-id="30d16-127">Allows for values to be generated by the database on insert (default values) or update (computed columns).</span></span>

### <a name="sequences-in-sql-server"></a><span data-ttu-id="30d16-128">SQL Server 内のシーケンス</span><span class="sxs-lookup"><span data-stu-id="30d16-128">Sequences in SQL Server</span></span>

<span data-ttu-id="30d16-129">モデルにシーケンス オブジェクトを定義できます。</span><span class="sxs-lookup"><span data-stu-id="30d16-129">Allows for sequence objects to be defined in the model.</span></span>

### <a name="unique-constraints"></a><span data-ttu-id="30d16-130">UNIQUE 制約</span><span class="sxs-lookup"><span data-stu-id="30d16-130">Unique constraints</span></span>

<span data-ttu-id="30d16-131">代替キーを定義し、そのキーを対象とするリレーションシップを定義する機能を利用できます。</span><span class="sxs-lookup"><span data-stu-id="30d16-131">Allows for the definition of alternate keys and the ability to define relationships that target that key.</span></span>

### <a name="indexes"></a><span data-ttu-id="30d16-132">インデックス</span><span class="sxs-lookup"><span data-stu-id="30d16-132">Indexes</span></span>

<span data-ttu-id="30d16-133">モデルにインデックスを定義すると、インデックスが自動的にデータベースに導入されます。</span><span class="sxs-lookup"><span data-stu-id="30d16-133">Defining indexes in the model automatically introduces indexes in the database.</span></span> <span data-ttu-id="30d16-134">一意なインデックスもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="30d16-134">Unique indexes are also supported.</span></span>

### <a name="shadow-state-properties"></a><span data-ttu-id="30d16-135">シャドウ状態プロパティ</span><span class="sxs-lookup"><span data-stu-id="30d16-135">Shadow state properties</span></span>

<span data-ttu-id="30d16-136">.NET クラスで宣言されておらず、このクラスに格納もされていないが、EF Core で追跡および更新できるプロパティをモデルに定義できます。</span><span class="sxs-lookup"><span data-stu-id="30d16-136">Allows for properties to be defined in the model that are not declared and are not stored in the .NET class but can be tracked and updated by EF Core.</span></span> <span data-ttu-id="30d16-137">オブジェクト内の外部キー プロパティを公開することが好ましくない場合に、外部キー プロパティの用途でよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="30d16-137">Commonly used for foreign key properties when exposing these in the object is not desired.</span></span>

### <a name="table-per-hierarchy-inheritance-pattern"></a><span data-ttu-id="30d16-138">Table-per-Hierarchy 継承パターン</span><span class="sxs-lookup"><span data-stu-id="30d16-138">Table-Per-Hierarchy inheritance pattern</span></span>

<span data-ttu-id="30d16-139">データベース内の特定レコードのエンティティの種類を識別する識別子の列を使用して、継承階層内のエンティティを 1 つのテーブルに保存できます。</span><span class="sxs-lookup"><span data-stu-id="30d16-139">Allows entities in an inheritance hierarchy to be saved to a single table using a discriminator column to identify they entity type for a given record in the database.</span></span>

### <a name="model-validation"></a><span data-ttu-id="30d16-140">モデルの検証</span><span class="sxs-lookup"><span data-stu-id="30d16-140">Model validation</span></span>

<span data-ttu-id="30d16-141">モデル内の無効なパターンを検出し、有用なエラー メッセージを示します。</span><span class="sxs-lookup"><span data-stu-id="30d16-141">Detects invalid patterns in the model and provides helpful error messages.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="30d16-142">変更の追跡</span><span class="sxs-lookup"><span data-stu-id="30d16-142">Change tracking</span></span>

### <a name="snapshot-change-tracking"></a><span data-ttu-id="30d16-143">スナップショットによる変更の追跡</span><span class="sxs-lookup"><span data-stu-id="30d16-143">Snapshot change tracking</span></span>

<span data-ttu-id="30d16-144">現在の状態を元の状態のコピー (スナップショット) と比較することで、エンティティの変更を自動的に検出できます。</span><span class="sxs-lookup"><span data-stu-id="30d16-144">Allows changes in entities to be detected automatically by comparing current state against a copy (snapshot) of the original state.</span></span>

### <a name="notification-change-tracking"></a><span data-ttu-id="30d16-145">通知による変更の追跡</span><span class="sxs-lookup"><span data-stu-id="30d16-145">Notification change tracking</span></span>

<span data-ttu-id="30d16-146">プロパティの値が変更されると、エンティティから変更トラッカーに通知できます。</span><span class="sxs-lookup"><span data-stu-id="30d16-146">Allows your entities to notify the change tracker when property values are modified.</span></span>

### <a name="accessing-tracked-state"></a><span data-ttu-id="30d16-147">追跡対象の状態へのアクセス</span><span class="sxs-lookup"><span data-stu-id="30d16-147">Accessing tracked state</span></span>

<span data-ttu-id="30d16-148">`DbContext.Entry` と `DbContext.ChangeTracker` を使用します。</span><span class="sxs-lookup"><span data-stu-id="30d16-148">Via `DbContext.Entry` and `DbContext.ChangeTracker`.</span></span>

### <a name="attaching-detached-entitiesgraphs"></a><span data-ttu-id="30d16-149">デタッチされたエンティティやグラフのアタッチ</span><span class="sxs-lookup"><span data-stu-id="30d16-149">Attaching detached entities/graphs</span></span>

<span data-ttu-id="30d16-150">この新しい `DbContext.AttachGraph` API では、新しいまたは変更されたエンティティを保存するために、コンテキストにエンティティを再アタッチできます。</span><span class="sxs-lookup"><span data-stu-id="30d16-150">The new `DbContext.AttachGraph` API helps re-attach entities to a context in order to save new/modified entities.</span></span>

## <a name="saving-data"></a><span data-ttu-id="30d16-151">データの保存</span><span class="sxs-lookup"><span data-stu-id="30d16-151">Saving data</span></span>

### <a name="basic-save-functionality"></a><span data-ttu-id="30d16-152">基本の保存機能</span><span class="sxs-lookup"><span data-stu-id="30d16-152">Basic save functionality</span></span>

<span data-ttu-id="30d16-153">エンティティ インスタンスへの変更をデータベースに保存できます。</span><span class="sxs-lookup"><span data-stu-id="30d16-153">Allows changes to entity instances to be persisted to the database.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="30d16-154">オプティミスティック コンカレンシー</span><span class="sxs-lookup"><span data-stu-id="30d16-154">Optimistic Concurrency</span></span>

<span data-ttu-id="30d16-155">データベースからデータがフェッチされた後、別のユーザーが行った変更が上書きされないようにします。</span><span class="sxs-lookup"><span data-stu-id="30d16-155">Protects against overwriting changes made by another user since data was fetched from the database.</span></span>

### <a name="async-savechanges"></a><span data-ttu-id="30d16-156">非同期 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="30d16-156">Async SaveChanges</span></span>

<span data-ttu-id="30d16-157">データベースで `SaveChanges` から発行されたコマンドを処理している間に他の要求を処理できるように、現在のスレッドを解放できます。</span><span class="sxs-lookup"><span data-stu-id="30d16-157">Can free up the current thread to process other requests while the database processes the commands issued from `SaveChanges`.</span></span>

### <a name="database-transactions"></a><span data-ttu-id="30d16-158">データベース トランザクション</span><span class="sxs-lookup"><span data-stu-id="30d16-158">Database Transactions</span></span>

<span data-ttu-id="30d16-159">`SaveChanges` が常にアトミックであることを示します。つまり、完全に成功するか、データベースが変更されないかどちらかです。</span><span class="sxs-lookup"><span data-stu-id="30d16-159">Means that `SaveChanges` is always atomic (meaning it either completely succeeds, or no changes are made to the database).</span></span> <span data-ttu-id="30d16-160">コンテキスト インスタンスなどの間でトランザクションを共有できる、トランザクションに関連した API もあります。</span><span class="sxs-lookup"><span data-stu-id="30d16-160">There are also transaction related APIs to allow sharing transactions between context instances etc.</span></span>

### <a name="relational-batching-of-statements"></a><span data-ttu-id="30d16-161">リレーショナル: ステートメントのバッチ処理</span><span class="sxs-lookup"><span data-stu-id="30d16-161">Relational: Batching of statements</span></span>

<span data-ttu-id="30d16-162">複数の INSERT/UPDATE/DELETE コマンドを 1 度のラウンドトリップでデータベースにバッチ更新することにより、パフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="30d16-162">Provides better performance by batching up multiple INSERT/UPDATE/DELETE commands into a single roundtrip to the database.</span></span>

## <a name="query"></a><span data-ttu-id="30d16-163">クエリ</span><span class="sxs-lookup"><span data-stu-id="30d16-163">Query</span></span>

### <a name="basic-linq-support"></a><span data-ttu-id="30d16-164">LINQ の基本的なサポート</span><span class="sxs-lookup"><span data-stu-id="30d16-164">Basic LINQ support</span></span>

<span data-ttu-id="30d16-165">LINQ を使用してデータベースからデータを取得する機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="30d16-165">Provides the ability to use LINQ to retrieve data from the database.</span></span>

### <a name="mixed-clientserver-evaluation"></a><span data-ttu-id="30d16-166">混合クライアント/サーバーの評価</span><span class="sxs-lookup"><span data-stu-id="30d16-166">Mixed client/server evaluation</span></span>

<span data-ttu-id="30d16-167">データベース内で評価できないためにデータをメモリに取得してから評価する必要のあるロジックをクエリに組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="30d16-167">Enables queries to contain logic that cannot be evaluated in the database, and must therefore be evaluated after the data is retrieved into memory.</span></span>

### <a name="notracking"></a><span data-ttu-id="30d16-168">NoTracking</span><span class="sxs-lookup"><span data-stu-id="30d16-168">NoTracking</span></span>

<span data-ttu-id="30d16-169">コンテキストでエンティティ インスタンスへの変更を監視する必要がない場合には、クエリをより速く実行できます (これは結果が読み取り専用である場合に便利です)。</span><span class="sxs-lookup"><span data-stu-id="30d16-169">Queries enables quicker query execution when the context does not need to monitor for changes to the entity instances (this is useful if the results are read-only).</span></span>

### <a name="eager-loading"></a><span data-ttu-id="30d16-170">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="30d16-170">Eager loading</span></span>

<span data-ttu-id="30d16-171">クエリ実行時にフェッチも必要な関連データを識別する `Include` および `ThenInclude` メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="30d16-171">Provides the `Include` and `ThenInclude` methods to identify related data that should also be fetched when querying.</span></span>

### <a name="async-query"></a><span data-ttu-id="30d16-172">非同期クエリ</span><span class="sxs-lookup"><span data-stu-id="30d16-172">Async query</span></span>

<span data-ttu-id="30d16-173">データベースでクエリを処理している間に他の要求を処理できるように、現在のスレッド (とそれに関連するリソース) を解放できます。</span><span class="sxs-lookup"><span data-stu-id="30d16-173">Can free up the current thread (and it's associated resources) to process other requests while the database processes the query.</span></span>

### <a name="raw-sql-queries"></a><span data-ttu-id="30d16-174">生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="30d16-174">Raw SQL queries</span></span>

<span data-ttu-id="30d16-175">生 SQL クエリを使用してデータをフェッチする `DbSet.FromSql` メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="30d16-175">Provides the `DbSet.FromSql` method to use raw SQL queries to fetch data.</span></span> <span data-ttu-id="30d16-176">これらのクエリは、LINQ を使用して構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="30d16-176">These queries can also be composed on using LINQ.</span></span>

## <a name="database-schema-management"></a><span data-ttu-id="30d16-177">データベース スキーマの管理</span><span class="sxs-lookup"><span data-stu-id="30d16-177">Database schema management</span></span>

### <a name="database-creationdeletion-apis"></a><span data-ttu-id="30d16-178">データベース作成/削除 API</span><span class="sxs-lookup"><span data-stu-id="30d16-178">Database creation/deletion APIs</span></span>

<span data-ttu-id="30d16-179">これらの API の大半は、データベースを移行せずにすばやく作成または削除する必要のあるテスト向けに設計されています。</span><span class="sxs-lookup"><span data-stu-id="30d16-179">Are mostly designed for testing where you want to quickly create/delete the database without using migrations.</span></span>

### <a name="relational-database-migrations"></a><span data-ttu-id="30d16-180">リレーショナル データベースの移行</span><span class="sxs-lookup"><span data-stu-id="30d16-180">Relational database migrations</span></span>

<span data-ttu-id="30d16-181">リレーショナル データベース スキーマを、モデルの変化に合わせて長期間かけて進化させることができます。</span><span class="sxs-lookup"><span data-stu-id="30d16-181">Allow a relational database schema to evolve overtime as your model changes.</span></span>

### <a name="reverse-engineer-from-database"></a><span data-ttu-id="30d16-182">データベースからのリバース エンジニアリング</span><span class="sxs-lookup"><span data-stu-id="30d16-182">Reverse engineer from database</span></span>

<span data-ttu-id="30d16-183">既存のリレーショナル データベース スキーマに基づいて、EF モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="30d16-183">Scaffolds an EF model based on an existing relational database schema.</span></span>

## <a name="database-providers"></a><span data-ttu-id="30d16-184">データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="30d16-184">Database providers</span></span>

### <a name="sql-server"></a><span data-ttu-id="30d16-185">SQL Server</span><span class="sxs-lookup"><span data-stu-id="30d16-185">SQL Server</span></span>

<span data-ttu-id="30d16-186">Microsoft SQL Server 2008 以降に接続します。</span><span class="sxs-lookup"><span data-stu-id="30d16-186">Connects to Microsoft SQL Server 2008 onwards.</span></span>

### <a name="sqlite"></a><span data-ttu-id="30d16-187">SQLite</span><span class="sxs-lookup"><span data-stu-id="30d16-187">SQLite</span></span>

<span data-ttu-id="30d16-188">SQLite 3 データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="30d16-188">Connects to a SQLite 3 database.</span></span>

### <a name="in-memory"></a><span data-ttu-id="30d16-189">インメモリ</span><span class="sxs-lookup"><span data-stu-id="30d16-189">In-Memory</span></span>

<span data-ttu-id="30d16-190">実際のデータベースに接続せずに簡単にテストできるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="30d16-190">Is designed to easily enable testing without connecting to a real database.</span></span>

### <a name="3rd-party-providers"></a><span data-ttu-id="30d16-191">サード パーティ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="30d16-191">3rd party providers</span></span>

<span data-ttu-id="30d16-192">他のデータベース エンジンで使用できるプロバイダーもあります。</span><span class="sxs-lookup"><span data-stu-id="30d16-192">Several providers are available for other database engines.</span></span> <span data-ttu-id="30d16-193">すべてのプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](xref:core/providers/index)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="30d16-193">See [Database Providers](xref:core/providers/index) for a complete list.</span></span>
