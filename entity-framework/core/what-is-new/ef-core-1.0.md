---
title: "EF Core 1.0 の新機能 - EF Core"
author: divega
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 20A25111-AEBE-4BC2-83A5-3F651952DF72
ms.technology: entity-framework-core
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: e5b9e57a01ff302b1d7bd0fc5419aa5b8213865e
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="features-included-in-ef-core-10"></a><span data-ttu-id="f1bfa-102">EF Core 1.0 に含まれる機能</span><span class="sxs-lookup"><span data-stu-id="f1bfa-102">Features included in EF Core 1.0</span></span>

## <a name="platforms"></a><span data-ttu-id="f1bfa-103">プラットフォーム</span><span class="sxs-lookup"><span data-stu-id="f1bfa-103">Platforms</span></span>
### <a name="net-framework-451"></a><span data-ttu-id="f1bfa-104">.NET Framework 4.5.1</span><span class="sxs-lookup"><span data-stu-id="f1bfa-104">.NET Framework 4.5.1</span></span>
<span data-ttu-id="f1bfa-105">コンソール、WPF、WinForms、ASP.NET 4 などが含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-105">Includes Console, WPF, WinForms, ASP.NET 4, etc.</span></span>
### <a name="net-standard-13"></a><span data-ttu-id="f1bfa-106">.NET Standard 1.3</span><span class="sxs-lookup"><span data-stu-id="f1bfa-106">.NET Standard 1.3</span></span>
<span data-ttu-id="f1bfa-107">.NET Framework と、Windows、OSX および Linux での .NET Core の両方を対象にした ASP.NET Core が含まれています。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-107">Including ASP.NET Core targeting both .NET Framework and .NET Core on Windows, OSX, and Linux.</span></span>

## <a name="modelling"></a><span data-ttu-id="f1bfa-108">モデリング</span><span class="sxs-lookup"><span data-stu-id="f1bfa-108">Modelling</span></span>
### <a name="basic-modelling"></a><span data-ttu-id="f1bfa-109">基本モデリング</span><span class="sxs-lookup"><span data-stu-id="f1bfa-109">Basic modelling</span></span>
<span data-ttu-id="f1bfa-110">共通のスカラー型 (`int`、`string` など) の get/set プロパティを持つ POCO エンティティに基づいています。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-110">Based on POCO entities with get/set properties of common scalar types (`int`, `string`, etc.).</span></span>
### <a name="relationships-and-navigation-properties"></a><span data-ttu-id="f1bfa-111">リレーションシップとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="f1bfa-111">Relationships and navigation properties</span></span>
<span data-ttu-id="f1bfa-112">外部キーに基づく一対多、一対ゼロまたは一対一のリレーションシップをモデルに指定できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-112">One-to-many and One-to-zero-or-one relationships can be specified in the model based on a foreign key.</span></span> <span data-ttu-id="f1bfa-113">単純なコレクションまたは参照型のナビゲーション プロパティを、これらのリレーションシップに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-113">Navigation properties of simple collection or reference types can be associated with these relationships.</span></span>
### <a name="built-in-conventions"></a><span data-ttu-id="f1bfa-114">組み込みの規約</span><span class="sxs-lookup"><span data-stu-id="f1bfa-114">Built-in conventions</span></span>
<span data-ttu-id="f1bfa-115">組み込みの規約は、エンティティ クラスの構造に基づいて、初期モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-115">These build an initial model based on the shape of the entity classes.</span></span>
### <a name="fluent-api"></a><span data-ttu-id="f1bfa-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="f1bfa-116">Fluent API</span></span>
<span data-ttu-id="f1bfa-117">使用するコンテキストの `OnModelCreating` メソッドを上書きして、規約によって検出されたモデルをさらに構成できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-117">Allows you to override the `OnModelCreating` method on your context to further configure the model that was discovered by convention.</span></span>
### <a name="data-annotations"></a><span data-ttu-id="f1bfa-118">データの注釈</span><span class="sxs-lookup"><span data-stu-id="f1bfa-118">Data annotations</span></span>
<span data-ttu-id="f1bfa-119">エンティティのクラスやプロパティに追加できる属性で、EF モデルに影響を与えます (たとえば [必須] を追加すると、プロパティが必須であることが EF に通知されます)。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-119">Are attributes that can be added to your entity classes/properties and will influence the EF model (i.e. adding [Required] will let EF know that a property is required).</span></span>
### <a name="relational-table-mapping"></a><span data-ttu-id="f1bfa-120">リレーショナル テーブルのマッピング</span><span class="sxs-lookup"><span data-stu-id="f1bfa-120">Relational Table mapping</span></span>
<span data-ttu-id="f1bfa-121">エンティティをテーブルや列にマップできます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-121">Allows entities to be mapped to tables/columns.</span></span>
### <a name="key-value-generation"></a><span data-ttu-id="f1bfa-122">キー値の生成</span><span class="sxs-lookup"><span data-stu-id="f1bfa-122">Key value generation</span></span>
<span data-ttu-id="f1bfa-123">クライアント側での生成と、データベースでの生成が含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-123">Including client-side generation and database generation.</span></span>
### <a name="database-generated-values"></a><span data-ttu-id="f1bfa-124">データベースで生成される値</span><span class="sxs-lookup"><span data-stu-id="f1bfa-124">Database generated values</span></span>
<span data-ttu-id="f1bfa-125">データベースでの挿入 (既定値) や更新 (計算列) によって値を生成できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-125">Allows for values to be generated by the database on insert (default values) or update (computed columns).</span></span>
### <a name="sequences-in-sql-server"></a><span data-ttu-id="f1bfa-126">SQL Server 内のシーケンス</span><span class="sxs-lookup"><span data-stu-id="f1bfa-126">Sequences in SQL Server</span></span>
<span data-ttu-id="f1bfa-127">モデルにシーケンス オブジェクトを定義できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-127">Allows for sequence objects to be defined in the model.</span></span>
### <a name="unique-constraints"></a><span data-ttu-id="f1bfa-128">UNIQUE 制約</span><span class="sxs-lookup"><span data-stu-id="f1bfa-128">Unique constraints</span></span>
<span data-ttu-id="f1bfa-129">代替キーを定義し、そのキーを対象とするリレーションシップを定義する機能を利用できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-129">Allows for the definition of alternate keys and the ability to define relationships that target that key.</span></span>
### <a name="indexes"></a><span data-ttu-id="f1bfa-130">インデックス</span><span class="sxs-lookup"><span data-stu-id="f1bfa-130">Indexes</span></span>
<span data-ttu-id="f1bfa-131">モデルにインデックスを定義すると、インデックスが自動的にデータベースに導入されます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-131">Defining indexes in the model automatically introduces indexes in the database.</span></span> <span data-ttu-id="f1bfa-132">一意なインデックスもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-132">Unique indexes are also supported.</span></span>
### <a name="shadow-state-properties"></a><span data-ttu-id="f1bfa-133">シャドウ状態プロパティ</span><span class="sxs-lookup"><span data-stu-id="f1bfa-133">Shadow state properties</span></span>
<span data-ttu-id="f1bfa-134">.NET クラスで宣言されておらず、このクラスに格納もされていないが、EF Core で追跡および更新できるプロパティをモデルに定義できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-134">Allows for properties to be defined in the model that are not declared and are not stored in the .NET class but can be tracked and updated by EF Core.</span></span> <span data-ttu-id="f1bfa-135">オブジェクト内の外部キー プロパティを公開することが好ましくない場合に、外部キー プロパティの用途でよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-135">Commonly used for foreign key properties when exposing these in the object is not desired.</span></span>
### <a name="table-per-hierarchy-inheritance-pattern"></a><span data-ttu-id="f1bfa-136">Table-per-Hierarchy 継承パターン</span><span class="sxs-lookup"><span data-stu-id="f1bfa-136">Table-Per-Hierarchy inheritance pattern</span></span>
<span data-ttu-id="f1bfa-137">データベース内の特定レコードのエンティティの種類を識別する識別子の列を使用して、継承階層内のエンティティを 1 つのテーブルに保存できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-137">Allows entities in an inheritance hierarchy to be saved to a single table using a discriminator column to identify they entity type for a given record in the database.</span></span>
### <a name="model-validation"></a><span data-ttu-id="f1bfa-138">モデルの検証</span><span class="sxs-lookup"><span data-stu-id="f1bfa-138">Model validation</span></span>
<span data-ttu-id="f1bfa-139">モデル内の無効なパターンを検出し、有用なエラー メッセージを示します。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-139">Detects invalid patterns in the model and provides helpful error messages.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="f1bfa-140">Change tracking</span><span class="sxs-lookup"><span data-stu-id="f1bfa-140">Change tracking</span></span>
### <a name="snapshot-change-tracking"></a><span data-ttu-id="f1bfa-141">スナップショットによる変更の追跡</span><span class="sxs-lookup"><span data-stu-id="f1bfa-141">Snapshot change tracking</span></span>
<span data-ttu-id="f1bfa-142">現在の状態を元の状態のコピー (スナップショット) と比較することで、エンティティの変更を自動的に検出できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-142">Allows changes in entities to be detected automatically by comparing current state against a copy (snapshot) of the original state.</span></span>
### <a name="notification-change-tracking"></a><span data-ttu-id="f1bfa-143">通知による変更の追跡</span><span class="sxs-lookup"><span data-stu-id="f1bfa-143">Notification change tracking</span></span>
<span data-ttu-id="f1bfa-144">プロパティの値が変更されると、エンティティから変更トラッカーに通知できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-144">Allows your entities to notify the change tracker when property values are modified.</span></span>
### <a name="accessing-tracked-state"></a><span data-ttu-id="f1bfa-145">追跡対象の状態へのアクセス</span><span class="sxs-lookup"><span data-stu-id="f1bfa-145">Accessing tracked state</span></span>
<span data-ttu-id="f1bfa-146">`DbContext.Entry` と `DbContext.ChangeTracker` を使用します。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-146">Via `DbContext.Entry` and `DbContext.ChangeTracker`.</span></span>
### <a name="attaching-detached-entitiesgraphs"></a><span data-ttu-id="f1bfa-147">デタッチされたエンティティやグラフのアタッチ</span><span class="sxs-lookup"><span data-stu-id="f1bfa-147">Attaching detached entities/graphs</span></span>
<span data-ttu-id="f1bfa-148">この新しい `DbContext.AttachGraph` API では、新しいまたは変更されたエンティティを保存するために、コンテキストにエンティティを再アタッチできます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-148">The new `DbContext.AttachGraph` API helps re-attach entities to a context in order to save new/modified entities.</span></span>

## <a name="saving-data"></a><span data-ttu-id="f1bfa-149">データの保存</span><span class="sxs-lookup"><span data-stu-id="f1bfa-149">Saving data</span></span>
### <a name="basic-save-functionality"></a><span data-ttu-id="f1bfa-150">基本の保存機能</span><span class="sxs-lookup"><span data-stu-id="f1bfa-150">Basic save functionality</span></span>
<span data-ttu-id="f1bfa-151">エンティティ インスタンスへの変更をデータベースに保存できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-151">Allows changes to entity instances to be persisted to the database.</span></span>
### <a name="optimistic-concurrency"></a><span data-ttu-id="f1bfa-152">オプティミスティック同時実行制御</span><span class="sxs-lookup"><span data-stu-id="f1bfa-152">Optimistic Concurrency</span></span>
<span data-ttu-id="f1bfa-153">データベースからデータがフェッチされた後、別のユーザーが行った変更が上書きされないようにします。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-153">Protects against overwriting changes made by another user since data was fetched from the database.</span></span>
### <a name="async-savechanges"></a><span data-ttu-id="f1bfa-154">非同期 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="f1bfa-154">Async SaveChanges</span></span>
<span data-ttu-id="f1bfa-155">データベースで `SaveChanges` から発行されたコマンドを処理している間に他の要求を処理できるように、現在のスレッドを解放できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-155">Can free up the current thread to process other requests while the database processes the commands issued from `SaveChanges`.</span></span>
### <a name="database-transactions"></a><span data-ttu-id="f1bfa-156">データベース トランザクション</span><span class="sxs-lookup"><span data-stu-id="f1bfa-156">Database Transactions</span></span>
<span data-ttu-id="f1bfa-157">`SaveChanges` が常にアトミックであることを示します。つまり、完全に成功するか、データベースが変更されないかどちらかです。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-157">Means that `SaveChanges` is always atomic (meaning it either completely succeeds, or no changes are made to the database).</span></span> <span data-ttu-id="f1bfa-158">コンテキスト インスタンスなどの間でトランザクションを共有できる、トランザクションに関連した API もあります。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-158">There are also transaction related APIs to allow sharing transactions between context instances etc.</span></span>
### <a name="relational-batching-of-statements"></a><span data-ttu-id="f1bfa-159">リレーショナル: ステートメントのバッチ処理</span><span class="sxs-lookup"><span data-stu-id="f1bfa-159">Relational: Batching of statements</span></span>
<span data-ttu-id="f1bfa-160">複数の INSERT/UPDATE/DELETE コマンドを 1 度のラウンドトリップでデータベースにバッチ更新することにより、パフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-160">Provides better performance by batching up multiple INSERT/UPDATE/DELETE commands into a single roundtrip to the database.</span></span>

## <a name="query"></a><span data-ttu-id="f1bfa-161">クエリ</span><span class="sxs-lookup"><span data-stu-id="f1bfa-161">Query</span></span>
### <a name="basic-linq-support"></a><span data-ttu-id="f1bfa-162">LINQ の基本的なサポート</span><span class="sxs-lookup"><span data-stu-id="f1bfa-162">Basic LINQ support</span></span>
<span data-ttu-id="f1bfa-163">LINQ を使用してデータベースからデータを取得する機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-163">Provides the ability to use LINQ to retrieve data from the database.</span></span>
### <a name="mixed-clientserver-evaluation"></a><span data-ttu-id="f1bfa-164">混合クライアント/サーバーの評価</span><span class="sxs-lookup"><span data-stu-id="f1bfa-164">Mixed client/server evaluation</span></span>
<span data-ttu-id="f1bfa-165">データベース内で評価できないためにデータをメモリに取得してから評価する必要のあるロジックをクエリに組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-165">Enables queries to contain logic that cannot be evaluated in the database, and must therefore be evaluated after the data is retrieved into memory.</span></span>
### <a name="notracking"></a><span data-ttu-id="f1bfa-166">NoTracking</span><span class="sxs-lookup"><span data-stu-id="f1bfa-166">NoTracking</span></span>
<span data-ttu-id="f1bfa-167">コンテキストでエンティティ インスタンスへの変更を監視する必要がない場合には (結果が読み取りのみの場合など)、クエリをより速く実行できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-167">Queries enables quicker query execution when the context does not need to monitor for changes to the entity instances (i.e. the results are read-only).</span></span>
### <a name="eager-loading"></a><span data-ttu-id="f1bfa-168">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="f1bfa-168">Eager loading</span></span>
<span data-ttu-id="f1bfa-169">クエリ実行時にフェッチも必要な関連データを識別する `Include` および `ThenInclude` メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-169">Provides the `Include` and `ThenInclude` methods to identify related data that should also be fetched when querying.</span></span>
### <a name="async-query"></a><span data-ttu-id="f1bfa-170">非同期クエリ</span><span class="sxs-lookup"><span data-stu-id="f1bfa-170">Async query</span></span>
<span data-ttu-id="f1bfa-171">データベースでクエリを処理している間に他の要求を処理できるように、現在のスレッド (とそれに関連するリソース) を解放できます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-171">Can free up the current thread (and it's associated resources) to process other requests while the database processes the query.</span></span>
### <a name="raw-sql-queries"></a><span data-ttu-id="f1bfa-172">生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="f1bfa-172">Raw SQL queries</span></span>
<span data-ttu-id="f1bfa-173">生 SQL クエリを使用してデータをフェッチする `DbSet.FromSql` メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-173">Provides the `DbSet.FromSql` method to use raw SQL queries to fetch data.</span></span> <span data-ttu-id="f1bfa-174">これらのクエリは、LINQ を使用して構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-174">These queries can also be composed on using LINQ.</span></span>

## <a name="database-schema-management"></a><span data-ttu-id="f1bfa-175">データベース スキーマの管理</span><span class="sxs-lookup"><span data-stu-id="f1bfa-175">Database schema management</span></span>       
### <a name="database-creationdeletion-apis"></a><span data-ttu-id="f1bfa-176">データベース作成/削除 API</span><span class="sxs-lookup"><span data-stu-id="f1bfa-176">Database creation/deletion APIs</span></span>
<span data-ttu-id="f1bfa-177">これらの API の大半は、データベースを移行せずにすばやく作成または削除する必要のあるテスト向けに設計されています。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-177">Are mostly designed for testing where you want to quickly create/delete the database without using migrations.</span></span>
### <a name="relational-database-migrations"></a><span data-ttu-id="f1bfa-178">リレーショナル データベースの移行</span><span class="sxs-lookup"><span data-stu-id="f1bfa-178">Relational database migrations</span></span>
<span data-ttu-id="f1bfa-179">リレーショナル データベース スキーマを、モデルの変化に合わせて長期間かけて進化させることができます。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-179">Allow a relational database schema to evolve overtime as your model changes.</span></span>
### <a name="reverse-engineer-from-database"></a><span data-ttu-id="f1bfa-180">データベースからのリバース エンジニアリング</span><span class="sxs-lookup"><span data-stu-id="f1bfa-180">Reverse engineer from database</span></span>
<span data-ttu-id="f1bfa-181">既存のリレーショナル データベース スキーマに基づいて、EF モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-181">Scaffolds an EF model based on an existing relational database schema.</span></span>

## <a name="database-providers"></a><span data-ttu-id="f1bfa-182">データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f1bfa-182">Database providers</span></span>
### <a name="sql-server"></a><span data-ttu-id="f1bfa-183">SQL Server</span><span class="sxs-lookup"><span data-stu-id="f1bfa-183">SQL Server</span></span>
<span data-ttu-id="f1bfa-184">Microsoft SQL Server 2008 以降に接続します。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-184">Connects to Microsoft SQL Server 2008 onwards.</span></span>
### <a name="sqlite"></a><span data-ttu-id="f1bfa-185">SQLite</span><span class="sxs-lookup"><span data-stu-id="f1bfa-185">SQLite</span></span>
<span data-ttu-id="f1bfa-186">SQLite 3 データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-186">Connects to a SQLite 3 database.</span></span>
### <a name="in-memory"></a><span data-ttu-id="f1bfa-187">In-Memory</span><span class="sxs-lookup"><span data-stu-id="f1bfa-187">In-Memory</span></span>
<span data-ttu-id="f1bfa-188">実際のデータベースに接続せずに簡単にテストできるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-188">Is designed to easily enable testing without connecting to a real database.</span></span>
### <a name="3rd-party-providers"></a><span data-ttu-id="f1bfa-189">サード パーティ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="f1bfa-189">3rd party providers</span></span>
<span data-ttu-id="f1bfa-190">他のデータベース エンジンで使用できるプロバイダーもあります。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-190">Several providers are available for other database engines.</span></span> <span data-ttu-id="f1bfa-191">すべてのプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="f1bfa-191">See [Database Providers](../providers/index.md) for a complete list.</span></span>
