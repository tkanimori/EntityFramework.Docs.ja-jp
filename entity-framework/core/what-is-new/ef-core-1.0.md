---
title: EF Core 1.0 の新機能 - EF Core
author: divega
ms.date: 10/27/2016
ms.assetid: 20A25111-AEBE-4BC2-83A5-3F651952DF72
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: 2cd2a54d75ed3f0caa8b674dfb56babcfcc13592
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413595"
---
# <a name="features-included-in-ef-core-10"></a><span data-ttu-id="765ec-102">EF Core 1.0 に含まれる機能</span><span class="sxs-lookup"><span data-stu-id="765ec-102">Features included in EF Core 1.0</span></span>

## <a name="platforms"></a><span data-ttu-id="765ec-103">プラットフォーム</span><span class="sxs-lookup"><span data-stu-id="765ec-103">Platforms</span></span>

### <a name="net-framework-451"></a><span data-ttu-id="765ec-104">.NET Framework 4.5.1</span><span class="sxs-lookup"><span data-stu-id="765ec-104">.NET Framework 4.5.1</span></span>

<span data-ttu-id="765ec-105">コンソール、WPF、WinForms、ASP.NET 4 などが含まれます。</span><span class="sxs-lookup"><span data-stu-id="765ec-105">Includes Console, WPF, WinForms, ASP.NET 4, etc.</span></span>

### <a name="net-standard-13"></a><span data-ttu-id="765ec-106">.NET Standard 1.3</span><span class="sxs-lookup"><span data-stu-id="765ec-106">.NET Standard 1.3</span></span>

<span data-ttu-id="765ec-107">.NET Framework と、Windows、OSX および Linux での .NET Core の両方を対象にした ASP.NET Core が含まれています。</span><span class="sxs-lookup"><span data-stu-id="765ec-107">Including ASP.NET Core targeting both .NET Framework and .NET Core on Windows, OSX, and Linux.</span></span>

## <a name="modelling"></a><span data-ttu-id="765ec-108">モデリング</span><span class="sxs-lookup"><span data-stu-id="765ec-108">Modelling</span></span>

### <a name="basic-modelling"></a><span data-ttu-id="765ec-109">基本モデリング</span><span class="sxs-lookup"><span data-stu-id="765ec-109">Basic modelling</span></span>

<span data-ttu-id="765ec-110">共通のスカラー型 (`int`、`string` など) の get/set プロパティを持つ POCO エンティティに基づいています。</span><span class="sxs-lookup"><span data-stu-id="765ec-110">Based on POCO entities with get/set properties of common scalar types (`int`, `string`, etc.).</span></span>

### <a name="relationships-and-navigation-properties"></a><span data-ttu-id="765ec-111">リレーションシップとナビゲーション プロパティ</span><span class="sxs-lookup"><span data-stu-id="765ec-111">Relationships and navigation properties</span></span>

<span data-ttu-id="765ec-112">外部キーに基づく一対多、一対ゼロまたは一対一のリレーションシップをモデルに指定できます。</span><span class="sxs-lookup"><span data-stu-id="765ec-112">One-to-many and One-to-zero-or-one relationships can be specified in the model based on a foreign key.</span></span> <span data-ttu-id="765ec-113">単純なコレクションまたは参照型のナビゲーション プロパティを、これらのリレーションシップに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="765ec-113">Navigation properties of simple collection or reference types can be associated with these relationships.</span></span>

### <a name="built-in-conventions"></a><span data-ttu-id="765ec-114">組み込みの規約</span><span class="sxs-lookup"><span data-stu-id="765ec-114">Built-in conventions</span></span>

<span data-ttu-id="765ec-115">組み込みの規約は、エンティティ クラスの構造に基づいて、初期モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="765ec-115">These build an initial model based on the shape of the entity classes.</span></span>

### <a name="fluent-api"></a><span data-ttu-id="765ec-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="765ec-116">Fluent API</span></span>

<span data-ttu-id="765ec-117">使用するコンテキストの `OnModelCreating` メソッドをオーバーライドして、規約によって検出されたモデルをさらに構成できます。</span><span class="sxs-lookup"><span data-stu-id="765ec-117">Allows you to override the `OnModelCreating` method on your context to further configure the model that was discovered by convention.</span></span>

### <a name="data-annotations"></a><span data-ttu-id="765ec-118">データの注釈</span><span class="sxs-lookup"><span data-stu-id="765ec-118">Data annotations</span></span>

<span data-ttu-id="765ec-119">エンティティのクラスやプロパティに追加できる属性で、EF モデルに影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="765ec-119">Are attributes that can be added to your entity classes/properties and will influence the EF model.</span></span> <span data-ttu-id="765ec-120">たとえば `[Required]` を追加すると、プロパティが必須であることが EF に通知されます。</span><span class="sxs-lookup"><span data-stu-id="765ec-120">For example, adding `[Required]` will let EF know that a property is required.</span></span>

### <a name="relational-table-mapping"></a><span data-ttu-id="765ec-121">リレーショナル テーブルのマッピング</span><span class="sxs-lookup"><span data-stu-id="765ec-121">Relational Table mapping</span></span>

<span data-ttu-id="765ec-122">エンティティをテーブルや列にマップできます。</span><span class="sxs-lookup"><span data-stu-id="765ec-122">Allows entities to be mapped to tables/columns.</span></span>

### <a name="key-value-generation"></a><span data-ttu-id="765ec-123">キー値の生成</span><span class="sxs-lookup"><span data-stu-id="765ec-123">Key value generation</span></span>

<span data-ttu-id="765ec-124">クライアント側での生成と、データベースでの生成が含まれます。</span><span class="sxs-lookup"><span data-stu-id="765ec-124">Including client-side generation and database generation.</span></span>

### <a name="database-generated-values"></a><span data-ttu-id="765ec-125">データベースで生成される値</span><span class="sxs-lookup"><span data-stu-id="765ec-125">Database generated values</span></span>

<span data-ttu-id="765ec-126">データベースでの挿入 (既定値) や更新 (計算列) によって値を生成できます。</span><span class="sxs-lookup"><span data-stu-id="765ec-126">Allows for values to be generated by the database on insert (default values) or update (computed columns).</span></span>

### <a name="sequences-in-sql-server"></a><span data-ttu-id="765ec-127">SQL Server 内のシーケンス</span><span class="sxs-lookup"><span data-stu-id="765ec-127">Sequences in SQL Server</span></span>

<span data-ttu-id="765ec-128">モデルにシーケンス オブジェクトを定義できます。</span><span class="sxs-lookup"><span data-stu-id="765ec-128">Allows for sequence objects to be defined in the model.</span></span>

### <a name="unique-constraints"></a><span data-ttu-id="765ec-129">UNIQUE 制約</span><span class="sxs-lookup"><span data-stu-id="765ec-129">Unique constraints</span></span>

<span data-ttu-id="765ec-130">代替キーを定義し、そのキーを対象とするリレーションシップを定義する機能を利用できます。</span><span class="sxs-lookup"><span data-stu-id="765ec-130">Allows for the definition of alternate keys and the ability to define relationships that target that key.</span></span>

### <a name="indexes"></a><span data-ttu-id="765ec-131">インデックス</span><span class="sxs-lookup"><span data-stu-id="765ec-131">Indexes</span></span>

<span data-ttu-id="765ec-132">モデルにインデックスを定義すると、インデックスが自動的にデータベースに導入されます。</span><span class="sxs-lookup"><span data-stu-id="765ec-132">Defining indexes in the model automatically introduces indexes in the database.</span></span> <span data-ttu-id="765ec-133">一意なインデックスもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="765ec-133">Unique indexes are also supported.</span></span>

### <a name="shadow-state-properties"></a><span data-ttu-id="765ec-134">シャドウ状態プロパティ</span><span class="sxs-lookup"><span data-stu-id="765ec-134">Shadow state properties</span></span>

<span data-ttu-id="765ec-135">.NET クラスで宣言されておらず、このクラスに格納もされていないが、EF Core で追跡および更新できるプロパティをモデルに定義できます。</span><span class="sxs-lookup"><span data-stu-id="765ec-135">Allows for properties to be defined in the model that are not declared and are not stored in the .NET class but can be tracked and updated by EF Core.</span></span> <span data-ttu-id="765ec-136">オブジェクト内の外部キー プロパティを公開することが好ましくない場合に、外部キー プロパティの用途でよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="765ec-136">Commonly used for foreign key properties when exposing these in the object is not desired.</span></span>

### <a name="table-per-hierarchy-inheritance-pattern"></a><span data-ttu-id="765ec-137">Table-per-Hierarchy 継承パターン</span><span class="sxs-lookup"><span data-stu-id="765ec-137">Table-Per-Hierarchy inheritance pattern</span></span>

<span data-ttu-id="765ec-138">データベース内の特定レコードのエンティティの種類を識別する識別子の列を使用して、継承階層内のエンティティを 1 つのテーブルに保存できます。</span><span class="sxs-lookup"><span data-stu-id="765ec-138">Allows entities in an inheritance hierarchy to be saved to a single table using a discriminator column to identify they entity type for a given record in the database.</span></span>

### <a name="model-validation"></a><span data-ttu-id="765ec-139">モデルの検証</span><span class="sxs-lookup"><span data-stu-id="765ec-139">Model validation</span></span>

<span data-ttu-id="765ec-140">モデル内の無効なパターンを検出し、有用なエラー メッセージを示します。</span><span class="sxs-lookup"><span data-stu-id="765ec-140">Detects invalid patterns in the model and provides helpful error messages.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="765ec-141">変更の追跡</span><span class="sxs-lookup"><span data-stu-id="765ec-141">Change tracking</span></span>

### <a name="snapshot-change-tracking"></a><span data-ttu-id="765ec-142">スナップショットによる変更の追跡</span><span class="sxs-lookup"><span data-stu-id="765ec-142">Snapshot change tracking</span></span>

<span data-ttu-id="765ec-143">現在の状態を元の状態のコピー (スナップショット) と比較することで、エンティティの変更を自動的に検出できます。</span><span class="sxs-lookup"><span data-stu-id="765ec-143">Allows changes in entities to be detected automatically by comparing current state against a copy (snapshot) of the original state.</span></span>

### <a name="notification-change-tracking"></a><span data-ttu-id="765ec-144">通知による変更の追跡</span><span class="sxs-lookup"><span data-stu-id="765ec-144">Notification change tracking</span></span>

<span data-ttu-id="765ec-145">プロパティの値が変更されると、エンティティから変更トラッカーに通知できます。</span><span class="sxs-lookup"><span data-stu-id="765ec-145">Allows your entities to notify the change tracker when property values are modified.</span></span>

### <a name="accessing-tracked-state"></a><span data-ttu-id="765ec-146">追跡対象の状態へのアクセス</span><span class="sxs-lookup"><span data-stu-id="765ec-146">Accessing tracked state</span></span>

<span data-ttu-id="765ec-147">`DbContext.Entry` と `DbContext.ChangeTracker` を使用します。</span><span class="sxs-lookup"><span data-stu-id="765ec-147">Via `DbContext.Entry` and `DbContext.ChangeTracker`.</span></span>

### <a name="attaching-detached-entitiesgraphs"></a><span data-ttu-id="765ec-148">デタッチされたエンティティやグラフのアタッチ</span><span class="sxs-lookup"><span data-stu-id="765ec-148">Attaching detached entities/graphs</span></span>

<span data-ttu-id="765ec-149">この新しい `DbContext.AttachGraph` API では、新しいまたは変更されたエンティティを保存するために、コンテキストにエンティティを再アタッチできます。</span><span class="sxs-lookup"><span data-stu-id="765ec-149">The new `DbContext.AttachGraph` API helps re-attach entities to a context in order to save new/modified entities.</span></span>

## <a name="saving-data"></a><span data-ttu-id="765ec-150">データの保存</span><span class="sxs-lookup"><span data-stu-id="765ec-150">Saving data</span></span>

### <a name="basic-save-functionality"></a><span data-ttu-id="765ec-151">基本の保存機能</span><span class="sxs-lookup"><span data-stu-id="765ec-151">Basic save functionality</span></span>

<span data-ttu-id="765ec-152">エンティティ インスタンスへの変更をデータベースに保存できます。</span><span class="sxs-lookup"><span data-stu-id="765ec-152">Allows changes to entity instances to be persisted to the database.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="765ec-153">オプティミスティック コンカレンシー</span><span class="sxs-lookup"><span data-stu-id="765ec-153">Optimistic Concurrency</span></span>

<span data-ttu-id="765ec-154">データベースからデータがフェッチされた後、別のユーザーが行った変更が上書きされないようにします。</span><span class="sxs-lookup"><span data-stu-id="765ec-154">Protects against overwriting changes made by another user since data was fetched from the database.</span></span>

### <a name="async-savechanges"></a><span data-ttu-id="765ec-155">非同期 SaveChanges</span><span class="sxs-lookup"><span data-stu-id="765ec-155">Async SaveChanges</span></span>

<span data-ttu-id="765ec-156">データベースで `SaveChanges` から発行されたコマンドを処理している間に他の要求を処理できるように、現在のスレッドを解放できます。</span><span class="sxs-lookup"><span data-stu-id="765ec-156">Can free up the current thread to process other requests while the database processes the commands issued from `SaveChanges`.</span></span>

### <a name="database-transactions"></a><span data-ttu-id="765ec-157">データベース トランザクション</span><span class="sxs-lookup"><span data-stu-id="765ec-157">Database Transactions</span></span>

<span data-ttu-id="765ec-158">`SaveChanges` が常にアトミックであることを示します。つまり、完全に成功するか、データベースが変更されないかどちらかです。</span><span class="sxs-lookup"><span data-stu-id="765ec-158">Means that `SaveChanges` is always atomic (meaning it either completely succeeds, or no changes are made to the database).</span></span> <span data-ttu-id="765ec-159">コンテキスト インスタンスなどの間でトランザクションを共有できる、トランザクションに関連した API もあります。</span><span class="sxs-lookup"><span data-stu-id="765ec-159">There are also transaction related APIs to allow sharing transactions between context instances etc.</span></span>

### <a name="relational-batching-of-statements"></a><span data-ttu-id="765ec-160">リレーショナル: ステートメントのバッチ処理</span><span class="sxs-lookup"><span data-stu-id="765ec-160">Relational: Batching of statements</span></span>

<span data-ttu-id="765ec-161">複数の INSERT/UPDATE/DELETE コマンドを 1 度のラウンドトリップでデータベースにバッチ更新することにより、パフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="765ec-161">Provides better performance by batching up multiple INSERT/UPDATE/DELETE commands into a single roundtrip to the database.</span></span>

## <a name="query"></a><span data-ttu-id="765ec-162">クエリ</span><span class="sxs-lookup"><span data-stu-id="765ec-162">Query</span></span>

### <a name="basic-linq-support"></a><span data-ttu-id="765ec-163">LINQ の基本的なサポート</span><span class="sxs-lookup"><span data-stu-id="765ec-163">Basic LINQ support</span></span>

<span data-ttu-id="765ec-164">LINQ を使用してデータベースからデータを取得する機能を提供します。</span><span class="sxs-lookup"><span data-stu-id="765ec-164">Provides the ability to use LINQ to retrieve data from the database.</span></span>

### <a name="mixed-clientserver-evaluation"></a><span data-ttu-id="765ec-165">混合クライアント/サーバーの評価</span><span class="sxs-lookup"><span data-stu-id="765ec-165">Mixed client/server evaluation</span></span>

<span data-ttu-id="765ec-166">データベース内で評価できないためにデータをメモリに取得してから評価する必要のあるロジックをクエリに組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="765ec-166">Enables queries to contain logic that cannot be evaluated in the database, and must therefore be evaluated after the data is retrieved into memory.</span></span>

### <a name="notracking"></a><span data-ttu-id="765ec-167">NoTracking</span><span class="sxs-lookup"><span data-stu-id="765ec-167">NoTracking</span></span>

<span data-ttu-id="765ec-168">コンテキストでエンティティ インスタンスへの変更を監視する必要がない場合には、クエリをより速く実行できます (これは結果が読み取り専用である場合に便利です)。</span><span class="sxs-lookup"><span data-stu-id="765ec-168">Queries enables quicker query execution when the context does not need to monitor for changes to the entity instances (this is useful if the results are read-only).</span></span>

### <a name="eager-loading"></a><span data-ttu-id="765ec-169">一括読み込み</span><span class="sxs-lookup"><span data-stu-id="765ec-169">Eager loading</span></span>

<span data-ttu-id="765ec-170">クエリ実行時にフェッチも必要な関連データを識別する `Include` および `ThenInclude` メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="765ec-170">Provides the `Include` and `ThenInclude` methods to identify related data that should also be fetched when querying.</span></span>

### <a name="async-query"></a><span data-ttu-id="765ec-171">非同期クエリ</span><span class="sxs-lookup"><span data-stu-id="765ec-171">Async query</span></span>

<span data-ttu-id="765ec-172">データベースでクエリを処理している間に他の要求を処理できるように、現在のスレッド (とそれに関連するリソース) を解放できます。</span><span class="sxs-lookup"><span data-stu-id="765ec-172">Can free up the current thread (and it's associated resources) to process other requests while the database processes the query.</span></span>

### <a name="raw-sql-queries"></a><span data-ttu-id="765ec-173">生 SQL クエリ</span><span class="sxs-lookup"><span data-stu-id="765ec-173">Raw SQL queries</span></span>

<span data-ttu-id="765ec-174">生 SQL クエリを使用してデータをフェッチする `DbSet.FromSql` メソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="765ec-174">Provides the `DbSet.FromSql` method to use raw SQL queries to fetch data.</span></span> <span data-ttu-id="765ec-175">これらのクエリは、LINQ を使用して構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="765ec-175">These queries can also be composed on using LINQ.</span></span>

## <a name="database-schema-management"></a><span data-ttu-id="765ec-176">データベース スキーマの管理</span><span class="sxs-lookup"><span data-stu-id="765ec-176">Database schema management</span></span>

### <a name="database-creationdeletion-apis"></a><span data-ttu-id="765ec-177">データベース作成/削除 API</span><span class="sxs-lookup"><span data-stu-id="765ec-177">Database creation/deletion APIs</span></span>

<span data-ttu-id="765ec-178">これらの API の大半は、データベースを移行せずにすばやく作成または削除する必要のあるテスト向けに設計されています。</span><span class="sxs-lookup"><span data-stu-id="765ec-178">Are mostly designed for testing where you want to quickly create/delete the database without using migrations.</span></span>

### <a name="relational-database-migrations"></a><span data-ttu-id="765ec-179">リレーショナル データベースの移行</span><span class="sxs-lookup"><span data-stu-id="765ec-179">Relational database migrations</span></span>

<span data-ttu-id="765ec-180">リレーショナル データベース スキーマを、モデルの変化に合わせて長期間かけて進化させることができます。</span><span class="sxs-lookup"><span data-stu-id="765ec-180">Allow a relational database schema to evolve overtime as your model changes.</span></span>

### <a name="reverse-engineer-from-database"></a><span data-ttu-id="765ec-181">データベースからのリバース エンジニアリング</span><span class="sxs-lookup"><span data-stu-id="765ec-181">Reverse engineer from database</span></span>

<span data-ttu-id="765ec-182">既存のリレーショナル データベース スキーマに基づいて、EF モデルをスキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="765ec-182">Scaffolds an EF model based on an existing relational database schema.</span></span>

## <a name="database-providers"></a><span data-ttu-id="765ec-183">データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="765ec-183">Database providers</span></span>

### <a name="sql-server"></a><span data-ttu-id="765ec-184">SQL Server</span><span class="sxs-lookup"><span data-stu-id="765ec-184">SQL Server</span></span>

<span data-ttu-id="765ec-185">Microsoft SQL Server 2008 以降に接続します。</span><span class="sxs-lookup"><span data-stu-id="765ec-185">Connects to Microsoft SQL Server 2008 onwards.</span></span>

### <a name="sqlite"></a><span data-ttu-id="765ec-186">SQLite</span><span class="sxs-lookup"><span data-stu-id="765ec-186">SQLite</span></span>

<span data-ttu-id="765ec-187">SQLite 3 データベースに接続します。</span><span class="sxs-lookup"><span data-stu-id="765ec-187">Connects to a SQLite 3 database.</span></span>

### <a name="in-memory"></a><span data-ttu-id="765ec-188">インメモリ</span><span class="sxs-lookup"><span data-stu-id="765ec-188">In-Memory</span></span>

<span data-ttu-id="765ec-189">実際のデータベースに接続せずに簡単にテストできるように設計されています。</span><span class="sxs-lookup"><span data-stu-id="765ec-189">Is designed to easily enable testing without connecting to a real database.</span></span>

### <a name="3rd-party-providers"></a><span data-ttu-id="765ec-190">サード パーティ プロバイダー</span><span class="sxs-lookup"><span data-stu-id="765ec-190">3rd party providers</span></span>

<span data-ttu-id="765ec-191">他のデータベース エンジンで使用できるプロバイダーもあります。</span><span class="sxs-lookup"><span data-stu-id="765ec-191">Several providers are available for other database engines.</span></span> <span data-ttu-id="765ec-192">すべてのプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="765ec-192">See [Database Providers](../providers/index.md) for a complete list.</span></span>
