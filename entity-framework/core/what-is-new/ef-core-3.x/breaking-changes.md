---
title: EF Core 3.x での破壊的変更 - EF Core
description: Entity Framework Core 3.x に導入された破壊的変更の完全な一覧
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: bfcfb7257091d1b6889f7c0af00ddab10e0e12e3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429313"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a><span data-ttu-id="85585-103">EF Core 3.x に含まれる破壊的変更</span><span class="sxs-lookup"><span data-stu-id="85585-103">Breaking changes included in EF Core 3.x</span></span>

<span data-ttu-id="85585-104">次の API および動作の変更により、3.x へのアップグレード時に、既存のアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85585-104">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.x.</span></span>
<span data-ttu-id="85585-105">データベース プロバイダーにのみ影響することが予想される変更については、[プロバイダーの変更](xref:core/providers/provider-log)に関するページに記載されています。</span><span class="sxs-lookup"><span data-stu-id="85585-105">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="85585-106">まとめ</span><span class="sxs-lookup"><span data-stu-id="85585-106">Summary</span></span>

| <span data-ttu-id="85585-107">**重大な変更**</span><span class="sxs-lookup"><span data-stu-id="85585-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="85585-108">**影響**</span><span class="sxs-lookup"><span data-stu-id="85585-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="85585-109">LINQ クエリがクライアントで評価されなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="85585-110">高</span><span class="sxs-lookup"><span data-stu-id="85585-110">High</span></span>       |
| [<span data-ttu-id="85585-111">EF Core のコマンドライン ツールである dotnet ef が .NET Core SDK の一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-111">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="85585-112">高</span><span class="sxs-lookup"><span data-stu-id="85585-112">High</span></span>      |
| [<span data-ttu-id="85585-113">DetectChanges でストア生成キーの値が優先される</span><span class="sxs-lookup"><span data-stu-id="85585-113">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="85585-114">高</span><span class="sxs-lookup"><span data-stu-id="85585-114">High</span></span>      |
| [<span data-ttu-id="85585-115">FromSql、ExecuteSql、および ExecuteSqlAsync の名前変更</span><span class="sxs-lookup"><span data-stu-id="85585-115">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="85585-116">高</span><span class="sxs-lookup"><span data-stu-id="85585-116">High</span></span>      |
| [<span data-ttu-id="85585-117">クエリ型がエンティティ型と統合される</span><span class="sxs-lookup"><span data-stu-id="85585-117">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="85585-118">高</span><span class="sxs-lookup"><span data-stu-id="85585-118">High</span></span>      |
| [<span data-ttu-id="85585-119">Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-119">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="85585-120">Medium</span><span class="sxs-lookup"><span data-stu-id="85585-120">Medium</span></span>      |
| [<span data-ttu-id="85585-121">既定で連鎖削除がすぐに行われるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-121">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="85585-122">Medium</span><span class="sxs-lookup"><span data-stu-id="85585-122">Medium</span></span>      |
| [<span data-ttu-id="85585-123">関連エンティティの一括読み込みが 1 つのクエリで行われるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-123">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="85585-124">Medium</span><span class="sxs-lookup"><span data-stu-id="85585-124">Medium</span></span>      |
| [<span data-ttu-id="85585-125">DeleteBehavior.Restrict のセマンティクスがクリーンになりました</span><span class="sxs-lookup"><span data-stu-id="85585-125">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="85585-126">Medium</span><span class="sxs-lookup"><span data-stu-id="85585-126">Medium</span></span>      |
| [<span data-ttu-id="85585-127">所有型のリレーションシップ用の構成 API が変更された</span><span class="sxs-lookup"><span data-stu-id="85585-127">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="85585-128">Medium</span><span class="sxs-lookup"><span data-stu-id="85585-128">Medium</span></span>      |
| [<span data-ttu-id="85585-129">各プロパティで独立したメモリ内整数キー生成が使用される</span><span class="sxs-lookup"><span data-stu-id="85585-129">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="85585-130">Medium</span><span class="sxs-lookup"><span data-stu-id="85585-130">Medium</span></span>      |
| [<span data-ttu-id="85585-131">追跡なしのクエリでは、識別子の解決が実行されなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-131">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="85585-132">Medium</span><span class="sxs-lookup"><span data-stu-id="85585-132">Medium</span></span>      |
| [<span data-ttu-id="85585-133">メタデータ API の変更点</span><span class="sxs-lookup"><span data-stu-id="85585-133">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="85585-134">Medium</span><span class="sxs-lookup"><span data-stu-id="85585-134">Medium</span></span>      |
| [<span data-ttu-id="85585-135">プロバイダー固有のメタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="85585-135">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="85585-136">Medium</span><span class="sxs-lookup"><span data-stu-id="85585-136">Medium</span></span>      |
| [<span data-ttu-id="85585-137">UseRowNumberForPaging の削除</span><span class="sxs-lookup"><span data-stu-id="85585-137">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="85585-138">Medium</span><span class="sxs-lookup"><span data-stu-id="85585-138">Medium</span></span>      |
| [<span data-ttu-id="85585-139">FromSql メソッドをストアド プロシージャと共に使用して構成することができない</span><span class="sxs-lookup"><span data-stu-id="85585-139">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="85585-140">Medium</span><span class="sxs-lookup"><span data-stu-id="85585-140">Medium</span></span>      |
| [<span data-ttu-id="85585-141">FromSql メソッドはクエリのルートでのみ指定できる</span><span class="sxs-lookup"><span data-stu-id="85585-141">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="85585-142">低</span><span class="sxs-lookup"><span data-stu-id="85585-142">Low</span></span>      |
| [<span data-ttu-id="85585-143">一時キーの値がエンティティ インスタンスに設定されなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-143">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="85585-144">低</span><span class="sxs-lookup"><span data-stu-id="85585-144">Low</span></span>      |
| [<span data-ttu-id="85585-145">プリンシパルとテーブルを共有する依存エンティティが省略可能になりました</span><span class="sxs-lookup"><span data-stu-id="85585-145">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="85585-146">低</span><span class="sxs-lookup"><span data-stu-id="85585-146">Low</span></span>      |
| [<span data-ttu-id="85585-147">同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-147">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="85585-148">低</span><span class="sxs-lookup"><span data-stu-id="85585-148">Low</span></span>      |
| [<span data-ttu-id="85585-149">所有エンティティは、所有者がいないと、追跡クエリを使用してクエリを実行することができない</span><span class="sxs-lookup"><span data-stu-id="85585-149">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="85585-150">低</span><span class="sxs-lookup"><span data-stu-id="85585-150">Low</span></span>      |
| [<span data-ttu-id="85585-151">マップされていない型から継承されたプロパティは、すべての派生型の 1 つの列にマップされるようになりました</span><span class="sxs-lookup"><span data-stu-id="85585-151">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="85585-152">低</span><span class="sxs-lookup"><span data-stu-id="85585-152">Low</span></span>      |
| [<span data-ttu-id="85585-153">外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-153">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="85585-154">低</span><span class="sxs-lookup"><span data-stu-id="85585-154">Low</span></span>      |
| [<span data-ttu-id="85585-155">データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになりました</span><span class="sxs-lookup"><span data-stu-id="85585-155">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="85585-156">低</span><span class="sxs-lookup"><span data-stu-id="85585-156">Low</span></span>      |
| [<span data-ttu-id="85585-157">バッキング フィールドが既定で使用される</span><span class="sxs-lookup"><span data-stu-id="85585-157">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="85585-158">低</span><span class="sxs-lookup"><span data-stu-id="85585-158">Low</span></span>      |
| [<span data-ttu-id="85585-159">複数の互換性があるバッキング フィールドが見つかった場合にスローされる</span><span class="sxs-lookup"><span data-stu-id="85585-159">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="85585-160">低</span><span class="sxs-lookup"><span data-stu-id="85585-160">Low</span></span>      |
| [<span data-ttu-id="85585-161">フィールド専用プロパティの名前はフィールドの名前に一致する必要があります</span><span class="sxs-lookup"><span data-stu-id="85585-161">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="85585-162">低</span><span class="sxs-lookup"><span data-stu-id="85585-162">Low</span></span>      |
| [<span data-ttu-id="85585-163">AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなりました</span><span class="sxs-lookup"><span data-stu-id="85585-163">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="85585-164">低</span><span class="sxs-lookup"><span data-stu-id="85585-164">Low</span></span>      |
| [<span data-ttu-id="85585-165">AddEntityFramework\* により、サイズ制限がある IMemoryCache が追加される</span><span class="sxs-lookup"><span data-stu-id="85585-165">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="85585-166">低</span><span class="sxs-lookup"><span data-stu-id="85585-166">Low</span></span>      |
| [<span data-ttu-id="85585-167">DbContext.Entry でローカルの DetectChanges が実行されるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-167">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="85585-168">低</span><span class="sxs-lookup"><span data-stu-id="85585-168">Low</span></span>      |
| [<span data-ttu-id="85585-169">文字列とバイト配列のキーが既定でクライアントによって生成されない</span><span class="sxs-lookup"><span data-stu-id="85585-169">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="85585-170">低</span><span class="sxs-lookup"><span data-stu-id="85585-170">Low</span></span>      |
| [<span data-ttu-id="85585-171">ILoggerFactory がスコープ サービスになった</span><span class="sxs-lookup"><span data-stu-id="85585-171">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="85585-172">低</span><span class="sxs-lookup"><span data-stu-id="85585-172">Low</span></span>      |
| [<span data-ttu-id="85585-173">遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-173">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="85585-174">低</span><span class="sxs-lookup"><span data-stu-id="85585-174">Low</span></span>      |
| [<span data-ttu-id="85585-175">内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-175">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="85585-176">低</span><span class="sxs-lookup"><span data-stu-id="85585-176">Low</span></span>      |
| [<span data-ttu-id="85585-177">1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-177">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="85585-178">低</span><span class="sxs-lookup"><span data-stu-id="85585-178">Low</span></span>      |
| [<span data-ttu-id="85585-179">いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更されました</span><span class="sxs-lookup"><span data-stu-id="85585-179">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="85585-180">低</span><span class="sxs-lookup"><span data-stu-id="85585-180">Low</span></span>      |
| [<span data-ttu-id="85585-181">Relational:TypeMapping 注釈が単に TypeMapping となった</span><span class="sxs-lookup"><span data-stu-id="85585-181">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="85585-182">低</span><span class="sxs-lookup"><span data-stu-id="85585-182">Low</span></span>      |
| [<span data-ttu-id="85585-183">派生型の ToTable で例外がスローされる</span><span class="sxs-lookup"><span data-stu-id="85585-183">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="85585-184">低</span><span class="sxs-lookup"><span data-stu-id="85585-184">Low</span></span>      |
| [<span data-ttu-id="85585-185">EF Core で SQLite FK を適用するためのプラグマが送信されなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-185">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="85585-186">低</span><span class="sxs-lookup"><span data-stu-id="85585-186">Low</span></span>      |
| [<span data-ttu-id="85585-187">Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった</span><span class="sxs-lookup"><span data-stu-id="85585-187">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="85585-188">低</span><span class="sxs-lookup"><span data-stu-id="85585-188">Low</span></span>      |
| [<span data-ttu-id="85585-189">GUID の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-189">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="85585-190">低</span><span class="sxs-lookup"><span data-stu-id="85585-190">Low</span></span>      |
| [<span data-ttu-id="85585-191">Char の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-191">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="85585-192">低</span><span class="sxs-lookup"><span data-stu-id="85585-192">Low</span></span>      |
| [<span data-ttu-id="85585-193">移行 ID がインバリアント カルチャの暦を使用して生成されるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-193">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="85585-194">低</span><span class="sxs-lookup"><span data-stu-id="85585-194">Low</span></span>      |
| [<span data-ttu-id="85585-195">IDbContextOptionsExtension から拡張機能の情報/メタデータを削除</span><span class="sxs-lookup"><span data-stu-id="85585-195">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="85585-196">低</span><span class="sxs-lookup"><span data-stu-id="85585-196">Low</span></span>      |
| [<span data-ttu-id="85585-197">LogQueryPossibleExceptionWithAggregateOperator の名前が変更された</span><span class="sxs-lookup"><span data-stu-id="85585-197">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="85585-198">低</span><span class="sxs-lookup"><span data-stu-id="85585-198">Low</span></span>      |
| [<span data-ttu-id="85585-199">外部キー制約名の API が明確化された</span><span class="sxs-lookup"><span data-stu-id="85585-199">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="85585-200">低</span><span class="sxs-lookup"><span data-stu-id="85585-200">Low</span></span>      |
| [<span data-ttu-id="85585-201">IRelationalDatabaseCreator.HasTables/HasTablesAsync をパブリックに変更</span><span class="sxs-lookup"><span data-stu-id="85585-201">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="85585-202">低</span><span class="sxs-lookup"><span data-stu-id="85585-202">Low</span></span>      |
| [<span data-ttu-id="85585-203">Microsoft.EntityFrameworkCore.Design を DevelopmentDependency パッケージに変更</span><span class="sxs-lookup"><span data-stu-id="85585-203">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="85585-204">低</span><span class="sxs-lookup"><span data-stu-id="85585-204">Low</span></span>      |
| [<span data-ttu-id="85585-205">SQLitePCL.raw のバージョン 2.0.0 への更新</span><span class="sxs-lookup"><span data-stu-id="85585-205">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="85585-206">低</span><span class="sxs-lookup"><span data-stu-id="85585-206">Low</span></span>      |
| [<span data-ttu-id="85585-207">NetTopologySuite のバージョン 2.0.0 への更新</span><span class="sxs-lookup"><span data-stu-id="85585-207">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="85585-208">低</span><span class="sxs-lookup"><span data-stu-id="85585-208">Low</span></span>      |
| [<span data-ttu-id="85585-209">System.Data.SqlClient ではなく Microsoft.Data.SqlClient が使用される</span><span class="sxs-lookup"><span data-stu-id="85585-209">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="85585-210">低</span><span class="sxs-lookup"><span data-stu-id="85585-210">Low</span></span>      |
| [<span data-ttu-id="85585-211">複数のあいまいな自己参照リレーションシップを構成する必要がある</span><span class="sxs-lookup"><span data-stu-id="85585-211">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="85585-212">低</span><span class="sxs-lookup"><span data-stu-id="85585-212">Low</span></span>      |
| [<span data-ttu-id="85585-213">DbFunction.Schema が null または空の文字列である場合、モデルの既定のスキーマに構成される</span><span class="sxs-lookup"><span data-stu-id="85585-213">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="85585-214">低</span><span class="sxs-lookup"><span data-stu-id="85585-214">Low</span></span>      |
| [<span data-ttu-id="85585-215">~~EF Core 3.0 では .NET Standard 2.0 ではなく .NET Standard 2.1 がターゲットになる~~が元に戻される</span><span class="sxs-lookup"><span data-stu-id="85585-215">~~EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0~~ Reverted</span></span>](#netstandard21) | |
| [<span data-ttu-id="85585-216">~~クエリの実行がデバッグ レベルでログに記録される~~ 元に戻されます</span><span class="sxs-lookup"><span data-stu-id="85585-216">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | |

## <a name="high-impact-changes"></a><span data-ttu-id="85585-217">影響が大きい変更</span><span class="sxs-lookup"><span data-stu-id="85585-217">High-impact changes</span></span>

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="85585-218">LINQ クエリがクライアントで評価されなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-218">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="85585-219">[問題 #14935 の追跡](https://github.com/dotnet/efcore/issues/14935)
[問題 #12795 も参照](https://github.com/dotnet/efcore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="85585-219">[Tracking Issue #14935](https://github.com/dotnet/efcore/issues/14935)
[Also see issue #12795](https://github.com/dotnet/efcore/issues/12795)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="85585-220">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-220">Old behavior</span></span>

<span data-ttu-id="85585-221">3\.0 より前では、EF Core では、SQL またはパラメーターに対するクエリの一部だった式を変換できない場合、クライアントで自動的に式が評価されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-221">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="85585-222">既定では、コストの高い式のクライアント評価でのみ警告がトリガーされました。</span><span class="sxs-lookup"><span data-stu-id="85585-222">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-223">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-223">New behavior</span></span>

<span data-ttu-id="85585-224">3\.0 以降では、クライアントで評価される最上位のプロジェクション (クエリの最後の `Select()` 呼び出し) の式のみが EF Core で許可されます。</span><span class="sxs-lookup"><span data-stu-id="85585-224">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="85585-225">クエリの他の部分の式を SQL やパラメーターに変換できない場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="85585-225">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-226">理由</span><span class="sxs-lookup"><span data-stu-id="85585-226">Why</span></span>

<span data-ttu-id="85585-227">クエリの自動クライアント評価では、多くのクエリを実行できます。これらの重要な部分を変換できない場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="85585-227">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="85585-228">この動作が原因で、予期しない損害を与える動作となる可能性があります。この動作は、運用環境でのみ見られる場合があります。</span><span class="sxs-lookup"><span data-stu-id="85585-228">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="85585-229">たとえば、変換できない `Where()` 呼び出しの条件が原因で、テーブルのすべての行がデータベース サーバーから転送できるようになり、クライアントでフィルターを適用できるようになる場合があります。</span><span class="sxs-lookup"><span data-stu-id="85585-229">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="85585-230">このような状況は、開発時にテーブルにわずかな行が含まれている場合は、簡単に検出される可能性があります。しかし、テーブルに数百万もの行が含まれる、運用環境にアプリケーションを移行する場合は難しくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85585-230">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="85585-231">クライアント評価の警告では、開発中にあまりに簡単に無視されることも示されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-231">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="85585-232">これだけでなく、自動クライアント評価は、特定の式のクエリ変換の改善が、リリース間の予期しない破壊的変更の原因となる問題につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85585-232">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-233">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-233">Mitigations</span></span>

<span data-ttu-id="85585-234">クエリを完全に変換できない場合は、変換できる形式でクエリを書き直すか、`AsEnumerable()`、`ToList()`、または同様のものを使用して、LINQ-to-Objects を使ってさらに処理できるクライアントに明示的にデータを戻します。</span><span class="sxs-lookup"><span data-stu-id="85585-234">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="no-longer"></a>

## <a name="medium-impact-changes"></a><span data-ttu-id="85585-235">影響が中程度の変更</span><span class="sxs-lookup"><span data-stu-id="85585-235">Medium-impact changes</span></span>

### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="85585-236">Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-236">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="85585-237">問題 Announcements#325 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-237">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

#### <a name="old-behavior"></a><span data-ttu-id="85585-238">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-238">Old behavior</span></span>

<span data-ttu-id="85585-239">ASP.NET Core 3.0 より前では、パッケージ参照を `Microsoft.AspNetCore.App` または `Microsoft.AspNetCore.All` に追加したときに、EF Core と、SQL Server プロバイダーのような EF Core データ プロバイダーの一部が含まれました。</span><span class="sxs-lookup"><span data-stu-id="85585-239">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-240">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-240">New behavior</span></span>

<span data-ttu-id="85585-241">3\.0 以降では、ASP.NET Core 共有フレームワークには、EF Core も、いずれの EF Core データ プロバイダーも含まれません。</span><span class="sxs-lookup"><span data-stu-id="85585-241">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-242">理由</span><span class="sxs-lookup"><span data-stu-id="85585-242">Why</span></span>

<span data-ttu-id="85585-243">この変更の前では、アプリケーションのターゲットが ASP.NET Core であるか SQL Server であるかに応じて、EF Core を取得するには異なる手順が必要でした。</span><span class="sxs-lookup"><span data-stu-id="85585-243">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span>
<span data-ttu-id="85585-244">また、ASP.NET Core のアップグレードでは、必ずしも適切だとは言えない、EF Core と SQL Server プロバイダーのアップグレードが強制されました。</span><span class="sxs-lookup"><span data-stu-id="85585-244">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="85585-245">この変更では、EF Core の取得のエクスペリエンスは、.NET の実装とアプリケーションの種類がサポートされる、すべてのプロバイダーで同じになります。</span><span class="sxs-lookup"><span data-stu-id="85585-245">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="85585-246">また、開発者は、EF Core と EF Core データ プロバイダーのアップグレードのタイミングを正確に制御できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85585-246">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-247">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-247">Mitigations</span></span>

<span data-ttu-id="85585-248">ASP.NET Core 3.0 アプリケーションまたはその他のサポートされるアプリケーションで EF Core を使用するには、アプリケーションで使用される EF Core データベース プロバイダーにパッケージ参照を明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="85585-248">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>

### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="85585-249">EF Core のコマンドライン ツールである dotnet ef が .NET Core SDK の一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-249">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="85585-250">問題 #14016 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-250">Tracking Issue #14016</span></span>](https://github.com/dotnet/efcore/issues/14016)

#### <a name="old-behavior"></a><span data-ttu-id="85585-251">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-251">Old behavior</span></span>

<span data-ttu-id="85585-252">3\.0 より前は `dotnet ef` ツールが .NET Core SDK に含まれており、追加の手順を必要とせずに、任意のプロジェクトのコマンド ラインから簡単に使用できました。</span><span class="sxs-lookup"><span data-stu-id="85585-252">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-253">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-253">New behavior</span></span>

<span data-ttu-id="85585-254">3\.0 以降は .NET SDK に `dotnet ef` ツールが含まれないため、これを使用するにはローカルまたはグローバルなツールとして明示的にインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-254">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-255">理由</span><span class="sxs-lookup"><span data-stu-id="85585-255">Why</span></span>

<span data-ttu-id="85585-256">この変更により、`dotnet ef` を通常の .NET CLI ツールとして NuGet 上で配信したり更新したりできるようになります。これは EF Core 3.0 も常に NuGet パッケージとして配信されるという事実と一致します。</span><span class="sxs-lookup"><span data-stu-id="85585-256">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-257">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-257">Mitigations</span></span>

<span data-ttu-id="85585-258">移行の管理や `DbContext` のスキャフォールディングを行えるようにするには、`dotnet-ef` をグローバル ツールとしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="85585-258">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="85585-259">[ツール マニフェスト ファイル](/dotnet/core/tools/global-tools#install-a-local-tool)を使用してツールの依存関係として宣言するプロジェクトの依存関係を復元するときに、ローカルなツールとして取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="85585-259">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="85585-260">影響が小さい変更</span><span class="sxs-lookup"><span data-stu-id="85585-260">Low-impact changes</span></span>

<a name="fromsql"></a>

### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="85585-261">FromSql、ExecuteSql、および ExecuteSqlAsync の名前変更</span><span class="sxs-lookup"><span data-stu-id="85585-261">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="85585-262">問題 #10996 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-262">Tracking Issue #10996</span></span>](https://github.com/dotnet/efcore/issues/10996)

#### <a name="old-behavior"></a><span data-ttu-id="85585-263">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-263">Old behavior</span></span>

<span data-ttu-id="85585-264">EF Core 3.0 より前のバージョンでは、通常の文字列または SQL およびパラメーターに挿入する必要がある文字列で使用するために、これらのメソッド名がオーバーロードされました。</span><span class="sxs-lookup"><span data-stu-id="85585-264">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-265">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-265">New behavior</span></span>

<span data-ttu-id="85585-266">EF Core 3.0 以降では、`FromSqlRaw`、`ExecuteSqlRaw`、および `ExecuteSqlRawAsync` を使用して、パラメーターがクエリ文字列とは別に渡される、パラメーター化クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="85585-266">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="85585-267">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-267">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="85585-268">`FromSqlInterpolated`、`ExecuteSqlInterpolated`、および `ExecuteSqlInterpolatedAsync` を使用して、パラメーターが挿入クエリ文字列の一部として渡されるパラメーター化クエリを作成します。</span><span class="sxs-lookup"><span data-stu-id="85585-268">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="85585-269">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-269">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="85585-270">上記のクエリはどちらも、同じ SQL パラメーターを持つ同じパラメーター化 SQL が生成されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="85585-270">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-271">理由</span><span class="sxs-lookup"><span data-stu-id="85585-271">Why</span></span>

<span data-ttu-id="85585-272">このようなメソッド オーバーロードは、挿入文字列メソッドを呼び出すつもりが、誤って raw 文字列メソッドを非常に簡単に呼び出せてしまいます。その逆も同様です。</span><span class="sxs-lookup"><span data-stu-id="85585-272">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="85585-273">これは、クエリをパラメーター化する必要があるときに、パラメーター化されない結果になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="85585-273">This could result in queries not being parameterized when they should have been.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-274">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-274">Mitigations</span></span>

<span data-ttu-id="85585-275">新しいメソッド名を使用するように切り替えます。</span><span class="sxs-lookup"><span data-stu-id="85585-275">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>

### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="85585-276">FromSql メソッドをストアド プロシージャと共に使用して構成することができない</span><span class="sxs-lookup"><span data-stu-id="85585-276">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="85585-277">問題 #15392 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-277">Tracking Issue #15392</span></span>](https://github.com/dotnet/efcore/issues/15392)

#### <a name="old-behavior"></a><span data-ttu-id="85585-278">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-278">Old behavior</span></span>

<span data-ttu-id="85585-279">3\.0 EF Core より前では、FromSql メソッドにより、渡された SQL を構成できるかどうかの検出が試行されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-279">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="85585-280">SQL がストアド プロシージャのように非コンポーザブルである場合、クライアント評価が行われていました。</span><span class="sxs-lookup"><span data-stu-id="85585-280">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="85585-281">次のクエリは、サーバー上でストアド プロシージャを実行し、クライアント側で FirstOrDefault を実行することで機能していました。</span><span class="sxs-lookup"><span data-stu-id="85585-281">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

#### <a name="new-behavior"></a><span data-ttu-id="85585-282">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-282">New behavior</span></span>

<span data-ttu-id="85585-283">EF Core 3.0 以降では、SQL の解析は試行されません。</span><span class="sxs-lookup"><span data-stu-id="85585-283">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="85585-284">そのため、FromSqlRaw/FromSqlInterpolated の後に構成する場合、EF Core では、サブ クエリを実行することで SQL が構成されます。</span><span class="sxs-lookup"><span data-stu-id="85585-284">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="85585-285">したがって、構成でストアド プロシージャを使用している場合は、無効な SQL 構文の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="85585-285">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-286">理由</span><span class="sxs-lookup"><span data-stu-id="85585-286">Why</span></span>

<span data-ttu-id="85585-287">EF Core 3.0 では、自動クライアント評価はサポートされていません。これは、[ここ](#linq-queries-are-no-longer-evaluated-on-the-client)で説明するように、エラーが発生しやすいためです。</span><span class="sxs-lookup"><span data-stu-id="85585-287">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-288">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-288">Mitigations</span></span>

<span data-ttu-id="85585-289">FromSqlRaw/FromSqlInterpolated でストアド プロシージャを使用している場合は、それを構成できないことがわかっているので、サーバー側での構成を回避するために、FromSql メソッド呼び出しの直後に __AsEnumerable/AsAsyncEnumerable__ を追加します。</span><span class="sxs-lookup"><span data-stu-id="85585-289">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="85585-290">FromSql メソッドはクエリのルートでのみ指定できる</span><span class="sxs-lookup"><span data-stu-id="85585-290">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="85585-291">問題 #15704 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-291">Tracking Issue #15704</span></span>](https://github.com/dotnet/efcore/issues/15704)

#### <a name="old-behavior"></a><span data-ttu-id="85585-292">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-292">Old behavior</span></span>

<span data-ttu-id="85585-293">EF Core 3.0 以前は、クエリ内の任意の場所で `FromSql` メソッドを指定できました。</span><span class="sxs-lookup"><span data-stu-id="85585-293">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-294">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-294">New behavior</span></span>

<span data-ttu-id="85585-295">EF Core 3.0 以降、新しい `FromSqlRaw` および `FromSqlInterpolated` メソッド (`FromSql` の置き換え) は、クエリのルート上でのみ (つまり `DbSet<>` で直接) 指定できます。</span><span class="sxs-lookup"><span data-stu-id="85585-295">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="85585-296">他の場所でそれらを指定しようとすると、コンパイル エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="85585-296">Attempting to specify them anywhere else will result in a compilation error.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-297">理由</span><span class="sxs-lookup"><span data-stu-id="85585-297">Why</span></span>

<span data-ttu-id="85585-298">`DbSet` 以外の任意の場所で `FromSql` を指定しても、さらなる意味や価値が追加されることはなく、特定のシナリオではあいまいさの原因となる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85585-298">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-299">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-299">Mitigations</span></span>

<span data-ttu-id="85585-300">`FromSql` の呼び出し場所を移動して、それらが適用される `DbSet` 上で直接実行されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-300">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>

### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="85585-301">追跡なしのクエリでは、識別子の解決が実行されなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-301">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="85585-302">問題 #13518 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-302">Tracking Issue #13518</span></span>](https://github.com/dotnet/efcore/issues/13518)

#### <a name="old-behavior"></a><span data-ttu-id="85585-303">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-303">Old behavior</span></span>

<span data-ttu-id="85585-304">EF Core 3.0 以前の場合は、指定した型と ID を持つエンティティが出現するたびに同じエンティティ インスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="85585-304">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="85585-305">これは、追跡クエリの動作と一致します。</span><span class="sxs-lookup"><span data-stu-id="85585-305">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="85585-306">たとえば、このクエリは</span><span class="sxs-lookup"><span data-stu-id="85585-306">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```

<span data-ttu-id="85585-307">指定したカテゴリに関連付けられている各 `Category` に対して同じ `Product` インスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="85585-307">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-308">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-308">New behavior</span></span>

<span data-ttu-id="85585-309">EF Core 3.0 以降では、指定した型と ID を持つエンティティが、返されたグラフ内の異なる場所で検出されると、それぞれ異なるエンティティ インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="85585-309">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="85585-310">たとえば、上記のクエリでは、2 つの製品が同じカテゴリに関連付けられている場合でも、各 `Product` に対して新しい `Category` インスタンスが返されるようになります。</span><span class="sxs-lookup"><span data-stu-id="85585-310">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-311">理由</span><span class="sxs-lookup"><span data-stu-id="85585-311">Why</span></span>

<span data-ttu-id="85585-312">識別子の解決 (つまり、エンティティの型と ID が以前に検出されたエンティティと同じであることを確認する) を行う場合は、パフォーマンスおよびメモリのオーバーヘッドが増大します。</span><span class="sxs-lookup"><span data-stu-id="85585-312">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="85585-313">これは、通常、最初の場所で追跡なしのクエリが使用されている理由に矛盾しています。</span><span class="sxs-lookup"><span data-stu-id="85585-313">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="85585-314">また、識別子の解決は有用なこともありますが、エンティティをシリアル化してクライアントに送信する場合 (追跡なしのクエリでは一般的な処理) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="85585-314">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-315">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-315">Mitigations</span></span>

<span data-ttu-id="85585-316">識別子の解決が必要な場合は、追跡クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="85585-316">Use a tracking query if identity resolution is required.</span></span>

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="85585-317">一時キーの値がエンティティ インスタンスに設定されなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-317">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="85585-318">問題 #12378 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-318">Tracking Issue #12378</span></span>](https://github.com/dotnet/efcore/issues/12378)

#### <a name="old-behavior"></a><span data-ttu-id="85585-319">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-319">Old behavior</span></span>

<span data-ttu-id="85585-320">EF Core 3.0 より前では、後で実際の値がデータベースによって生成される、すべてのキー プロパティに一時的な値が割り当てられていました。</span><span class="sxs-lookup"><span data-stu-id="85585-320">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="85585-321">通常、これらの一時的な値は大きい負の数値でした。</span><span class="sxs-lookup"><span data-stu-id="85585-321">Usually these temporary values were large negative numbers.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-322">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-322">New behavior</span></span>

<span data-ttu-id="85585-323">3\.0 以降では、EF Core で、エンティティの追跡情報の一部として一時キーの値が格納され、キー プロパティ自体はそのままになります。</span><span class="sxs-lookup"><span data-stu-id="85585-323">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-324">理由</span><span class="sxs-lookup"><span data-stu-id="85585-324">Why</span></span>

<span data-ttu-id="85585-325">この変更は、何らかの `DbContext` インスタンスによって以前に追跡されたエンティティが、別の `DbContext` インスタンスに移動されるときに、一時キーの値が誤って永続的なものにならないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-325">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-326">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-326">Mitigations</span></span>

<span data-ttu-id="85585-327">主キーの値を外部キーに割り当てて、エンティティ間の関連付けを形成するアプリケーションは、主キーがストアで生成されており、`Added` 状態のエンティティに属している場合、以前の動作に依存する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85585-327">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="85585-328">これは、次のようにして回避できます。</span><span class="sxs-lookup"><span data-stu-id="85585-328">This can be avoided by:</span></span>

* <span data-ttu-id="85585-329">ストア生成キーを使用しない。</span><span class="sxs-lookup"><span data-stu-id="85585-329">Not using store-generated keys.</span></span>
* <span data-ttu-id="85585-330">外部キーの値を設定するのではなく、ナビゲーション プロパティを設定してリレーションシップを形成する。</span><span class="sxs-lookup"><span data-stu-id="85585-330">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="85585-331">エンティティの追跡情報から実際の一時キーの値を取得する。</span><span class="sxs-lookup"><span data-stu-id="85585-331">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="85585-332">たとえば、`context.Entry(blog).Property(e => e.Id).CurrentValue` では、`blog.Id` 自体が設定されていない場合でも、一時的な値が返されます。</span><span class="sxs-lookup"><span data-stu-id="85585-332">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="85585-333">DetectChanges でストア生成キーの値が優先される</span><span class="sxs-lookup"><span data-stu-id="85585-333">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="85585-334">問題 #14616 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-334">Tracking Issue #14616</span></span>](https://github.com/dotnet/efcore/issues/14616)

#### <a name="old-behavior"></a><span data-ttu-id="85585-335">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-335">Old behavior</span></span>

<span data-ttu-id="85585-336">EF Core 3.0 より前では、`DetectChanges` によって検出された追跡対象外のエンティティが `Added` 状態で追跡され、`SaveChanges` が呼び出されたときに新しい行として挿入されました。</span><span class="sxs-lookup"><span data-stu-id="85585-336">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-337">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-337">New behavior</span></span>

<span data-ttu-id="85585-338">EF Core 3.0 以降では、エンティティで生成されたキーの値を使用し、何らかのキーの値が設定されている場合、そのエンティティは `Modified` 状態で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85585-338">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="85585-339">つまり、エンティティの行が存在していると見なされ、`SaveChanges` の呼び出し時に更新されます。</span><span class="sxs-lookup"><span data-stu-id="85585-339">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="85585-340">キーの値が設定されていないか、エンティティ型で生成されたキーが使用されない場合、新しいエンティティは引き続き、以前のバージョンと同様に `Added` として追跡されます。</span><span class="sxs-lookup"><span data-stu-id="85585-340">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-341">理由</span><span class="sxs-lookup"><span data-stu-id="85585-341">Why</span></span>

<span data-ttu-id="85585-342">この変更は、ストア生成キーの使用中に、切り離されたエンティティ グラフをより簡単に一貫して操作できるようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-342">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-343">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-343">Mitigations</span></span>

<span data-ttu-id="85585-344">エンティティ型が生成されたキーを使用するように構成されているものの、キーの値が新しいインスタンスに対して明示的に設定されている場合、この変更によってアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85585-344">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="85585-345">この問題を解決するには、キー プロパティで生成された値が使用されないように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="85585-345">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="85585-346">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="85585-346">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="85585-347">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="85585-347">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

<a name="cascade"></a>

### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="85585-348">既定で連鎖削除がすぐに行われるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-348">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="85585-349">問題 #10114 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-349">Tracking Issue #10114</span></span>](https://github.com/dotnet/efcore/issues/10114)

#### <a name="old-behavior"></a><span data-ttu-id="85585-350">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-350">Old behavior</span></span>

<span data-ttu-id="85585-351">3\.0 より前では、EF Core で適用された連鎖操作 (必要なプリンシパルが削除されたか、必要なプリンシパルへのリレーションシップが切断されたときに依存エンティティを削除する) は、SaveChanges が呼び出されるまで行われませんでした。</span><span class="sxs-lookup"><span data-stu-id="85585-351">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-352">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-352">New behavior</span></span>

<span data-ttu-id="85585-353">3\.0 以降では、EF Core で、トリガー条件が検出されるとすぐに連鎖操作が適用されます。</span><span class="sxs-lookup"><span data-stu-id="85585-353">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="85585-354">たとえば、プリンシパル エンティティを削除するために `context.Remove()` を呼び出すと、すべての追跡対象の関連する必要な依存関係もすぐに `Deleted` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="85585-354">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-355">理由</span><span class="sxs-lookup"><span data-stu-id="85585-355">Why</span></span>

<span data-ttu-id="85585-356">この変更は、`SaveChanges` が呼び出される _前に_ どのエンティティが削除されるかを把握することが重要である、データ バインディングおよび監査シナリオのエクスペリエンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-356">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-357">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-357">Mitigations</span></span>

<span data-ttu-id="85585-358">`context.ChangeTracker` の設定を使用して、以前の動作を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="85585-358">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="85585-359">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-359">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

<a name="eager-loading-single-query"></a>

### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="85585-360">関連エンティティの一括読み込みが 1 つのクエリで行われるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-360">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="85585-361">問題 #18022 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-361">Tracking issue #18022</span></span>](https://github.com/dotnet/efcore/issues/18022)

#### <a name="old-behavior"></a><span data-ttu-id="85585-362">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-362">Old behavior</span></span>

<span data-ttu-id="85585-363">3\.0 より前では、`Include` 演算子を使用してコレクション ナビゲーションを一括で読み込むと、関連するエンティティ型ごとに 1 つずつ、リレーショナル データベースで複数のクエリが生成されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-363">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-364">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-364">New behavior</span></span>

<span data-ttu-id="85585-365">3\.0 以降の EF Core では、リレーショナル データベースで結合を使用した単一のクエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="85585-365">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-366">理由</span><span class="sxs-lookup"><span data-stu-id="85585-366">Why</span></span>

<span data-ttu-id="85585-367">単一の LINQ クエリを実装するために複数のクエリを実行すると、多くの問題が発生していました。これらの問題には、複数のデータベース ラウンドトリップが必要になった際のパフォーマンスの低下や、各クエリによってデータベースの異なる状態が観察された際のデータの一貫性の問題などが含まれます。</span><span class="sxs-lookup"><span data-stu-id="85585-367">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-368">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-368">Mitigations</span></span>

<span data-ttu-id="85585-369">技術的に、これは破壊的変更ではありませんが、コレクション ナビゲーションで単一のクエリに多数の `Include` 演算子が含まれている場合、アプリケーションのパフォーマンスに大きな影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85585-369">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="85585-370">詳細情報、およびより効率的な方法でクエリを再記述する方法については、[こちらのコメントを参照してください](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085)。</span><span class="sxs-lookup"><span data-stu-id="85585-370">[See this comment](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>

### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="85585-371">DeleteBehavior.Restrict のセマンティクスがクリーンになりました</span><span class="sxs-lookup"><span data-stu-id="85585-371">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="85585-372">問題 #12661 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-372">Tracking Issue #12661</span></span>](https://github.com/dotnet/efcore/issues/12661)

#### <a name="old-behavior"></a><span data-ttu-id="85585-373">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-373">Old behavior</span></span>

<span data-ttu-id="85585-374">3\.0 以前は、`DeleteBehavior.Restrict` により `Restrict` セマンティクスでデータベースに外部キーが作成されましたが、内部の修正がどのように変更されたのかがはっきりしませんでした。</span><span class="sxs-lookup"><span data-stu-id="85585-374">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-375">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-375">New behavior</span></span>

<span data-ttu-id="85585-376">3\.0 以降では、`DeleteBehavior.Restrict` により `Restrict` セマンティクスで外部キーが作成されます。つまり、カスケードがありません。EF 内部修正には影響を出さず、制約違反で例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="85585-376">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-377">理由</span><span class="sxs-lookup"><span data-stu-id="85585-377">Why</span></span>

<span data-ttu-id="85585-378">この変更は、副作用を出さず、直観的に `DeleteBehavior` を使用するために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-378">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-379">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-379">Mitigations</span></span>

<span data-ttu-id="85585-380">`DeleteBehavior.ClientNoAction` を使用し、以前の動作を復元できます。</span><span class="sxs-lookup"><span data-stu-id="85585-380">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>

### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="85585-381">クエリ型がエンティティ型と統合される</span><span class="sxs-lookup"><span data-stu-id="85585-381">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="85585-382">問題 #14194 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-382">Tracking Issue #14194</span></span>](https://github.com/dotnet/efcore/issues/14194)

#### <a name="old-behavior"></a><span data-ttu-id="85585-383">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-383">Old behavior</span></span>

<span data-ttu-id="85585-384">EF Core 3.0 より前では、[クエリ型](xref:core/modeling/keyless-entity-types)は、構造化された方法で主キーが定義されないデータのクエリを実行するための手段でした。</span><span class="sxs-lookup"><span data-stu-id="85585-384">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="85585-385">つまり、クエリ型は、キーが利用できるときに (テーブルからの可能性が高くなりますが、ビューからの可能性もあります) 通常のエンティティ型が使用された場合に、キーなしの (ビューからの可能性が高くなりますが、テーブルからの可能性もあります) エンティティ型をマップするために使用されました。</span><span class="sxs-lookup"><span data-stu-id="85585-385">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-386">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-386">New behavior</span></span>

<span data-ttu-id="85585-387">クエリ型は、現在、主キーなしの単なるエンティティ型になります。</span><span class="sxs-lookup"><span data-stu-id="85585-387">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="85585-388">キーなしのエンティティ型の機能は、以前のバージョンのクエリ型と同じです。</span><span class="sxs-lookup"><span data-stu-id="85585-388">Keyless entity types have the same functionality as query types in previous versions.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-389">理由</span><span class="sxs-lookup"><span data-stu-id="85585-389">Why</span></span>

<span data-ttu-id="85585-390">この変更は、クエリ型の目的に関する混乱を減らすために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-390">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="85585-391">つまり、これらはキーなしのエンティティ型であるため、本質的には読み取り専用となりますが、単にエンティティ型を読み取り専用にする必要があるという理由で使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="85585-391">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="85585-392">同様に、これらは多くの場合、ビューにマップされますが、これは、単にビューでは多くの場合、キーが定義されないためです。</span><span class="sxs-lookup"><span data-stu-id="85585-392">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-393">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-393">Mitigations</span></span>

<span data-ttu-id="85585-394">API の以下の部分は使用されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="85585-394">The following parts of the API are now obsolete:</span></span>

* <span data-ttu-id="85585-395">**`ModelBuilder.Query<>()`** - 代わりに `ModelBuilder.Entity<>().HasNoKey()` を呼び出して、エンティティ型をキーなしとしてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-395">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="85585-396">これは、規則により、主キーは必要であるが、規則と一致しない場合に構成エラーを回避するようにはまだ構成されません。</span><span class="sxs-lookup"><span data-stu-id="85585-396">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="85585-397">**`DbQuery<>`** - 代わりに `DbSet<>` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-397">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="85585-398">**`DbContext.Query<>()`** - 代わりに `DbContext.Set<>()` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-398">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>
* <span data-ttu-id="85585-399">**`IQueryTypeConfiguration<TQuery>`** - 代わりに `IEntityTypeConfiguration<TEntity>` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-399">**`IQueryTypeConfiguration<TQuery>`** - Instead `IEntityTypeConfiguration<TEntity>` should be used.</span></span>

> [!NOTE]
> <span data-ttu-id="85585-400">プロパティがすべて `null` に設定されているキーなしエンティティのクエリを実行する場合に発生する [3.x の問題](https://github.com/dotnet/efcore/issues/19537)により、エンティティではなく `null` が返されます。この問題がご自身のシナリオに当てはまる場合は、ロジックを追加して、結果に含まれる `null` の処理も行ってください。</span><span class="sxs-lookup"><span data-stu-id="85585-400">Due to [an issue in 3.x](https://github.com/dotnet/efcore/issues/19537) when querying keyless entities that have all properties set to `null` a `null` will be returned instead of an entity, if this issue is applicable to your scenario also add logic to handle `null` in results.</span></span>

<a name="config"></a>

### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="85585-401">所有型のリレーションシップ用の構成 API が変更された</span><span class="sxs-lookup"><span data-stu-id="85585-401">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="85585-402">[問題 #12444 の追跡](https://github.com/dotnet/efcore/issues/12444)
[問題 #9148 の追跡](https://github.com/dotnet/efcore/issues/9148)
[問題 #14153 の追跡](https://github.com/dotnet/efcore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="85585-402">[Tracking Issue #12444](https://github.com/dotnet/efcore/issues/12444)
[Tracking Issue #9148](https://github.com/dotnet/efcore/issues/9148)
[Tracking Issue #14153](https://github.com/dotnet/efcore/issues/14153)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="85585-403">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-403">Old behavior</span></span>

<span data-ttu-id="85585-404">EF Core 3.0 より前では、所有リレーションシップの構成は、`OwnsOne` または `OwnsMany` の呼び出し直後に実行されました。</span><span class="sxs-lookup"><span data-stu-id="85585-404">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-405">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-405">New behavior</span></span>

<span data-ttu-id="85585-406">EF Core 3.0 以降では、fluent API で、`WithOwner()` を使用して、所有者に対してナビゲーション プロパティが構成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85585-406">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="85585-407">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-407">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="85585-408">現在、所有者と所有型間のリレーションシップに関する構成は、他のリレーションシップの構成方法と同様、`WithOwner()` 後にチェーンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-408">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="85585-409">一方、所有型自体の構成は引き続き、`OwnsOne()/OwnsMany()` 後にチェーンされます。</span><span class="sxs-lookup"><span data-stu-id="85585-409">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="85585-410">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-410">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");

        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

<span data-ttu-id="85585-411">さらに所有型ターゲットでの `Entity()`、`HasOne()`、または `Set()` の呼び出しでは、例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="85585-411">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-412">理由</span><span class="sxs-lookup"><span data-stu-id="85585-412">Why</span></span>

<span data-ttu-id="85585-413">この変更は、所有型自体と、所有型 _へのリレーションシップ_ の構成を明確に分離するために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-413">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="85585-414">これにより、`HasForeignKey` のようなメソッドに関するあいまいさと混乱はなくなります。</span><span class="sxs-lookup"><span data-stu-id="85585-414">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-415">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-415">Mitigations</span></span>

<span data-ttu-id="85585-416">上記の例で示すように、新しい API サーフェスを使用するように、所有型リレーションシップの構成を変更します。</span><span class="sxs-lookup"><span data-stu-id="85585-416">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="85585-417">プリンシパルとテーブルを共有する依存エンティティが省略可能になりました</span><span class="sxs-lookup"><span data-stu-id="85585-417">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="85585-418">問題 #9005 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-418">Tracking Issue #9005</span></span>](https://github.com/dotnet/efcore/issues/9005)

#### <a name="old-behavior"></a><span data-ttu-id="85585-419">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-419">Old behavior</span></span>

<span data-ttu-id="85585-420">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="85585-420">Consider the following model:</span></span>

```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

<span data-ttu-id="85585-421">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、新しい `Order` を追加する場合には常に `OrderDetails` インスタンスが必要でした。</span><span class="sxs-lookup"><span data-stu-id="85585-421">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-422">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-422">New behavior</span></span>

<span data-ttu-id="85585-423">EF Core 3.0 以降では、`OrderDetails` なしで `Order` を追加することができ、主キー以外のすべての `OrderDetails` プロパティが NULL 値が許可される列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="85585-423">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="85585-424">`OrderDetails` は、必要なプロパティのいずれにも値がない場合、または主キー以外に必要なプロパティがなく、すべてのプロパティが `null` の場合、EF Core のクエリの実行時に `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="85585-424">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-425">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-425">Mitigations</span></span>

<span data-ttu-id="85585-426">モデルに省略可能なすべての列と依存関係を共有するテーブルがあるが、それを指すナビゲーションが `null` になることが想定されていない場合は、ナビゲーションが `null` の場合のケースを処理するようにアプリケーションを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-426">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="85585-427">これができない場合は、必要なプロパティをエンティティ型に追加するか、少なくとも 1 つのプロパティに割り当てられている値が `null` 以外である必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-427">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="85585-428">同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-428">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="85585-429">問題 #14154 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-429">Tracking Issue #14154</span></span>](https://github.com/dotnet/efcore/issues/14154)

#### <a name="old-behavior"></a><span data-ttu-id="85585-430">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-430">Old behavior</span></span>

<span data-ttu-id="85585-431">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="85585-431">Consider the following model:</span></span>

```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```

<span data-ttu-id="85585-432">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、`OrderDetails` だけを更新してもクライアント上の `Version` 値は更新されず、次回の更新が失敗します。</span><span class="sxs-lookup"><span data-stu-id="85585-432">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-433">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-433">New behavior</span></span>

<span data-ttu-id="85585-434">EF Core 3.0 以降では、新しい `Version` 値が `OrderDetails` を所有している場合には `Order` に伝達されます。</span><span class="sxs-lookup"><span data-stu-id="85585-434">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="85585-435">それ以外の場合は、モデルの検証中に例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="85585-435">Otherwise an exception is thrown during model validation.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-436">理由</span><span class="sxs-lookup"><span data-stu-id="85585-436">Why</span></span>

<span data-ttu-id="85585-437">この変更は、エンティティの 1 つだけが同じテーブルにマップされている場合に、古い同時実行トークン値が更新されるのを回避するために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-437">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-438">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-438">Mitigations</span></span>

<span data-ttu-id="85585-439">テーブルを共有するすべてのエンティティには、同時実行トークン列にマップされるプロパティを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-439">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="85585-440">シャドウ状態で作成することができます。</span><span class="sxs-lookup"><span data-stu-id="85585-440">It's possible the create one in shadow-state:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="85585-441">所有エンティティは、所有者がいないと、追跡クエリを使用してクエリを実行することができない</span><span class="sxs-lookup"><span data-stu-id="85585-441">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="85585-442">問題 #18876 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-442">Tracking Issue #18876</span></span>](https://github.com/dotnet/efcore/issues/18876)

#### <a name="old-behavior"></a><span data-ttu-id="85585-443">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-443">Old behavior</span></span>

<span data-ttu-id="85585-444">EF Core 3.0 より前のバージョンでは、所有エンティティに対しても他のナビゲーションと同じようにクエリを実行することができました。</span><span class="sxs-lookup"><span data-stu-id="85585-444">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

#### <a name="new-behavior"></a><span data-ttu-id="85585-445">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-445">New behavior</span></span>

<span data-ttu-id="85585-446">EF Core 3.0 以降では、追跡クエリで所有者のいない所有エンティティが提示されると、スローされます。</span><span class="sxs-lookup"><span data-stu-id="85585-446">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-447">理由</span><span class="sxs-lookup"><span data-stu-id="85585-447">Why</span></span>

<span data-ttu-id="85585-448">所有エンティティは所有者なしでは操作できません。そのため、このようにクエリを実行するほとんどのケースでは、エラーになります。</span><span class="sxs-lookup"><span data-stu-id="85585-448">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-449">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-449">Mitigations</span></span>

<span data-ttu-id="85585-450">所有エンティティを後で変更するように追跡する必要がある場合は、所有者をクエリに含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-450">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="85585-451">それ以外の場合は、`AsNoTracking()` の呼び出しを追加します。</span><span class="sxs-lookup"><span data-stu-id="85585-451">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="85585-452">マップされていない型から継承されたプロパティは、すべての派生型の 1 つの列にマップされるようになりました</span><span class="sxs-lookup"><span data-stu-id="85585-452">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="85585-453">問題 #13998 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-453">Tracking Issue #13998</span></span>](https://github.com/dotnet/efcore/issues/13998)

#### <a name="old-behavior"></a><span data-ttu-id="85585-454">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-454">Old behavior</span></span>

<span data-ttu-id="85585-455">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="85585-455">Consider the following model:</span></span>

```csharp
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

<span data-ttu-id="85585-456">EF Core 3.0 より前のバージョンでは、`ShippingAddress` プロパティは既定で `BulkOrder` と `Order` の個別の列にマップされていました。</span><span class="sxs-lookup"><span data-stu-id="85585-456">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-457">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-457">New behavior</span></span>

<span data-ttu-id="85585-458">EF Core 3.0 以降では、`ShippingAddress` に対して 1 つの列だけが作成されます。</span><span class="sxs-lookup"><span data-stu-id="85585-458">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-459">理由</span><span class="sxs-lookup"><span data-stu-id="85585-459">Why</span></span>

<span data-ttu-id="85585-460">以前の動作は予期しないものでした。</span><span class="sxs-lookup"><span data-stu-id="85585-460">The old behavoir was unexpected.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-461">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-461">Mitigations</span></span>

<span data-ttu-id="85585-462">プロパティは、派生型の個別の列に引き続き明示的にマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="85585-462">The property can still be explicitly mapped to separate column on the derived types:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="85585-463">外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-463">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="85585-464">問題 #13274 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-464">Tracking Issue #13274</span></span>](https://github.com/dotnet/efcore/issues/13274)

#### <a name="old-behavior"></a><span data-ttu-id="85585-465">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-465">Old behavior</span></span>

<span data-ttu-id="85585-466">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="85585-466">Consider the following model:</span></span>

```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

<span data-ttu-id="85585-467">EF Core 3.0 より前では、規則により、外部キーで `CustomerId` プロパティが使用されました。</span><span class="sxs-lookup"><span data-stu-id="85585-467">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="85585-468">しかし、`Order` が所有型である場合、`CustomerId` も主キーとマークされ、これは通常、期待されることではありません。</span><span class="sxs-lookup"><span data-stu-id="85585-468">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-469">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-469">New behavior</span></span>

<span data-ttu-id="85585-470">EF Core 3.0 以降では、プリンシパル プロパティと同じ名前である場合、規則により、外部キーに対するプロパティの使用は試行されません。</span><span class="sxs-lookup"><span data-stu-id="85585-470">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="85585-471">プリンシパル プロパティ名と連結されたプリンシパル型名、およびプリンシパル プロパティ名パターンと連結されたナビゲーション名は、引き続き一致します。</span><span class="sxs-lookup"><span data-stu-id="85585-471">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="85585-472">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-472">For example:</span></span>

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

#### <a name="why"></a><span data-ttu-id="85585-473">理由</span><span class="sxs-lookup"><span data-stu-id="85585-473">Why</span></span>

<span data-ttu-id="85585-474">この変更は、所有型に主キー プロパティが誤って定義されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-474">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-475">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-475">Mitigations</span></span>

<span data-ttu-id="85585-476">プロパティを外部キーにする、したがって、主キーの一部にする予定だった場合は、そのように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="85585-476">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="85585-477">データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになりました</span><span class="sxs-lookup"><span data-stu-id="85585-477">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="85585-478">問題 #14218 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-478">Tracking Issue #14218</span></span>](https://github.com/dotnet/efcore/issues/14218)

#### <a name="old-behavior"></a><span data-ttu-id="85585-479">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-479">Old behavior</span></span>

<span data-ttu-id="85585-480">EF Core 3.0 より前のバージョンでは、コンテキストにより `TransactionScope` 内部で接続が開かれると、現在の `TransactionScope` がアクティブの間、接続が開いたままになります。</span><span class="sxs-lookup"><span data-stu-id="85585-480">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point

        var categories = context.ProductCategories().ToList();
    }
}
```

#### <a name="new-behavior"></a><span data-ttu-id="85585-481">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-481">New behavior</span></span>

<span data-ttu-id="85585-482">EF Core 3.0 以降では、使用が終了したらすぐに接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="85585-482">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-483">理由</span><span class="sxs-lookup"><span data-stu-id="85585-483">Why</span></span>

<span data-ttu-id="85585-484">この変更により、同じ `TransactionScope` で複数のコンテキストを使用できます。</span><span class="sxs-lookup"><span data-stu-id="85585-484">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="85585-485">新しい動作は、EF6 にも一致します。</span><span class="sxs-lookup"><span data-stu-id="85585-485">The new behavior also matches EF6.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-486">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-486">Mitigations</span></span>

<span data-ttu-id="85585-487">接続を開いたままにする必要がある場合は、`OpenConnection()` を明示的に呼び出して、EF Core が途中で閉じないようにします。</span><span class="sxs-lookup"><span data-stu-id="85585-487">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="85585-488">各プロパティで独立したメモリ内整数キー生成が使用される</span><span class="sxs-lookup"><span data-stu-id="85585-488">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="85585-489">問題 #6872 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-489">Tracking Issue #6872</span></span>](https://github.com/dotnet/efcore/issues/6872)

#### <a name="old-behavior"></a><span data-ttu-id="85585-490">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-490">Old behavior</span></span>

<span data-ttu-id="85585-491">EF Core 3.0 より前では、1 つの共有値ジェネレーターが、すべてのメモリ内整数キー プロパティで使用されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-491">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-492">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-492">New behavior</span></span>

<span data-ttu-id="85585-493">EF Core 3.0 以降では、各整数キー プロパティで、メモリ内データベースを使用するときに独自の値ジェネレーターが取得されます。</span><span class="sxs-lookup"><span data-stu-id="85585-493">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="85585-494">また、データベースが削除された場合は、すべてのテーブルに対してキーの生成がリセットされます。</span><span class="sxs-lookup"><span data-stu-id="85585-494">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-495">理由</span><span class="sxs-lookup"><span data-stu-id="85585-495">Why</span></span>

<span data-ttu-id="85585-496">この変更は、実際のデータベース キー生成により近くなるようにメモリ内キー生成を調整するため、また、メモリ内データベースを使用するときに互いのテストを分離させる機能を向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-496">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-497">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-497">Mitigations</span></span>

<span data-ttu-id="85585-498">これにより、設定される特定のメモリ内キー値に依存しているアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85585-498">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="85585-499">代わりに特定のキーの値に依存しないようにするか、あるいは新しい動作と一致するように更新することを検討します。</span><span class="sxs-lookup"><span data-stu-id="85585-499">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="85585-500">バッキング フィールドが既定で使用される</span><span class="sxs-lookup"><span data-stu-id="85585-500">Backing fields are used by default</span></span>

[<span data-ttu-id="85585-501">問題 #12430 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-501">Tracking Issue #12430</span></span>](https://github.com/dotnet/efcore/issues/12430)

#### <a name="old-behavior"></a><span data-ttu-id="85585-502">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-502">Old behavior</span></span>

<span data-ttu-id="85585-503">3\.0 より前では、プロパティのバッキング フィールドがわかっていた場合でも、EF Core では引き続き、既定でプロパティの getter および setter メソッドを使用して、プロパティ値の読み取りと書き込みが行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-503">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="85585-504">ただし、バッキング フィールドが直接設定されることがわかっている場合のクエリの実行は例外です。</span><span class="sxs-lookup"><span data-stu-id="85585-504">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-505">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-505">New behavior</span></span>

<span data-ttu-id="85585-506">EF Core 3.0 以降では、プロパティのバッキング フィールドがわかっている場合、バッキング フィールドを使用して、常に EF Core がそのプロパティの読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="85585-506">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="85585-507">アプリケーションが getter および setter メソッドにコード化された追加動作に依存している場合、これによりアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85585-507">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-508">理由</span><span class="sxs-lookup"><span data-stu-id="85585-508">Why</span></span>

<span data-ttu-id="85585-509">この変更は、EF Core が、エンティティに関するデータベース操作の実行時に、既定でビジネス ロジックを誤ってトリガーしないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-509">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-510">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-510">Mitigations</span></span>

<span data-ttu-id="85585-511">3\.0 より前の動作は、`ModelBuilder` のプロパティ アクセス モードの構成を通じて復元できます。</span><span class="sxs-lookup"><span data-stu-id="85585-511">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="85585-512">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-512">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="85585-513">複数の互換性があるバッキング フィールドが見つかった場合にスローされる</span><span class="sxs-lookup"><span data-stu-id="85585-513">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="85585-514">問題 #12523 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-514">Tracking Issue #12523</span></span>](https://github.com/dotnet/efcore/issues/12523)

#### <a name="old-behavior"></a><span data-ttu-id="85585-515">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-515">Old behavior</span></span>

<span data-ttu-id="85585-516">EF Core 3.0 より前では、複数のフィールドが、プロパティのバッキング フィールドを見つけるための規則と一致した場合、何らかの優先順位に基づいて、1 つのフィールドが選択されました。</span><span class="sxs-lookup"><span data-stu-id="85585-516">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="85585-517">これにより、あいまいなケースで不適切なフィールドが使用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85585-517">This could cause the wrong field to be used in ambiguous cases.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-518">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-518">New behavior</span></span>

<span data-ttu-id="85585-519">EF Core 3.0 以降では、複数のフィールドが同じプロパティと一致した場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="85585-519">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-520">理由</span><span class="sxs-lookup"><span data-stu-id="85585-520">Why</span></span>

<span data-ttu-id="85585-521">この変更は、1 つのフィールドのみが適切である可能性がある場合に、他のものより優先して暗黙的に 1 つのフィールドが使用されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-521">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-522">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-522">Mitigations</span></span>

<span data-ttu-id="85585-523">あいまいなバッキング フィールドがあるプロパティでは、使用するフィールドを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-523">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="85585-524">たとえば、fluent API を使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="85585-524">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="85585-525">フィールド専用プロパティの名前はフィールドの名前に一致する必要があります</span><span class="sxs-lookup"><span data-stu-id="85585-525">Field-only property names should match the field name</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="85585-526">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-526">Old behavior</span></span>

<span data-ttu-id="85585-527">EF Core 3.0 以前では、プロパティは文字列値により指定できました。 .NET 型でその名前のプロパティが見つからなかった場合、EF Core では、一般的な規則を使ってそれとフィールドの照合が試行されました。</span><span class="sxs-lookup"><span data-stu-id="85585-527">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

```csharp
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

#### <a name="new-behavior"></a><span data-ttu-id="85585-528">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-528">New behavior</span></span>

<span data-ttu-id="85585-529">EF Core 3.0 以降では、フィールド専用プロパティはフィールドの名前に厳密に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-529">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

#### <a name="why"></a><span data-ttu-id="85585-530">理由</span><span class="sxs-lookup"><span data-stu-id="85585-530">Why</span></span>

<span data-ttu-id="85585-531">この変更は、同じような名前が付けられた 2 つのプロパティに同じフィールドが使用されるのを回避する目的で行われました。また、フィールド専用プロパティの照合規則を、CLR プロパティにマッピングされているプロパティの場合と同じにします。</span><span class="sxs-lookup"><span data-stu-id="85585-531">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-532">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-532">Mitigations</span></span>

<span data-ttu-id="85585-533">フィールド専用プロパティには、それがマッピングされるフィールドと同じ名前を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-533">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="85585-534">EF Core 3.0 以降の今後のリリースでは、プロパティ名とは異なるフィールド名を明示的に構成できるように戻す予定です (問題 [#15307](https://github.com/dotnet/efcore/issues/15307) を参照)。</span><span class="sxs-lookup"><span data-stu-id="85585-534">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/dotnet/efcore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="85585-535">AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなりました</span><span class="sxs-lookup"><span data-stu-id="85585-535">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="85585-536">問題 #14756 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-536">Tracking Issue #14756</span></span>](https://github.com/dotnet/efcore/issues/14756)

#### <a name="old-behavior"></a><span data-ttu-id="85585-537">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-537">Old behavior</span></span>

<span data-ttu-id="85585-538">EF Core 3.0 より前のバージョンでは、`AddDbContext` または `AddDbContextPool` を呼び出すと、[AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) および [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) への呼び出しを通じて、DI を使ってログ記録とメモリ キャッシュ サービスも登録されました。</span><span class="sxs-lookup"><span data-stu-id="85585-538">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-539">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-539">New behavior</span></span>

<span data-ttu-id="85585-540">EF Core 3.0 以降、`AddDbContext` と `AddDbContextPool` では、依存関係の挿入 (DI) を使ってこれらのサービスを登録しなくなります。</span><span class="sxs-lookup"><span data-stu-id="85585-540">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

#### <a name="why"></a><span data-ttu-id="85585-541">理由</span><span class="sxs-lookup"><span data-stu-id="85585-541">Why</span></span>

<span data-ttu-id="85585-542">EF Core 3.0 では、これらのサービスをアプリケーションの DI コンテナーに含める必要がありません。</span><span class="sxs-lookup"><span data-stu-id="85585-542">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="85585-543">ただし、`ILoggerFactory` がアプリケーションの DI コンテナーに登録された場合、それは引き続き EF Core によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="85585-543">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-544">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-544">Mitigations</span></span>

<span data-ttu-id="85585-545">ご自身のアプリケーションでこれらのサービスが必要な場合は、[AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) または [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) を使って、DI コンテナーで明示的にそれらを登録します。</span><span class="sxs-lookup"><span data-stu-id="85585-545">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="85585-546">AddEntityFramework\* により、サイズ制限がある IMemoryCache が追加される</span><span class="sxs-lookup"><span data-stu-id="85585-546">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="85585-547">問題 #12905 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-547">Tracking Issue #12905</span></span>](https://github.com/dotnet/efcore/issues/12905)

#### <a name="old-behavior"></a><span data-ttu-id="85585-548">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-548">Old behavior</span></span>

<span data-ttu-id="85585-549">EF Core 3.0 より前のバージョンでは、`AddEntityFramework*` メソッドを呼び出すと、サイズ制限のないメモリ キャッシュ サービスも DI に登録されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-549">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-550">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-550">New behavior</span></span>

<span data-ttu-id="85585-551">EF Core 3.0 以降では、`AddEntityFramework*` によって IMemoryCache サービスがサイズ制限付きで登録されます。</span><span class="sxs-lookup"><span data-stu-id="85585-551">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="85585-552">その後に追加された他のサービスが IMemoryCache に依存している場合は、既定の制限にすぐに達して、例外やパフォーマンスの低下が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85585-552">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-553">理由</span><span class="sxs-lookup"><span data-stu-id="85585-553">Why</span></span>

<span data-ttu-id="85585-554">クエリ キャッシュ ロジックにバグがある場合、またはクエリが動的に生成される場合は、制限なしで IMemoryCache を使用すると、メモリ使用量が制御されなくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="85585-554">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="85585-555">既定の制限を設定することで、DoS 攻撃の可能性を軽減できます。</span><span class="sxs-lookup"><span data-stu-id="85585-555">Having a default limit mitigates a potential DoS attack.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-556">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-556">Mitigations</span></span>

<span data-ttu-id="85585-557">`AddDbContext` または `AddDbContextPool` も呼び出された場合、ほとんどの場合、`AddEntityFramework*` を呼び出す必要はありません。</span><span class="sxs-lookup"><span data-stu-id="85585-557">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="85585-558">したがって、最善の軽減策は、`AddEntityFramework*` の呼び出しを削除することです。</span><span class="sxs-lookup"><span data-stu-id="85585-558">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="85585-559">ご自身のアプリケーションでこれらのサービスが必要な場合は、[AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) を使用する前に、IMemoryCache の実装を DI コンテナーに明示的に登録します。</span><span class="sxs-lookup"><span data-stu-id="85585-559">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="85585-560">DbContext.Entry でローカルの DetectChanges が実行されるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-560">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="85585-561">問題 #13552 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-561">Tracking Issue #13552</span></span>](https://github.com/dotnet/efcore/issues/13552)

#### <a name="old-behavior"></a><span data-ttu-id="85585-562">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-562">Old behavior</span></span>

<span data-ttu-id="85585-563">EF Core 3.0 より前では、`DbContext.Entry` を呼び出すと、追跡対象のすべてのエンティティで変更が検出されました。</span><span class="sxs-lookup"><span data-stu-id="85585-563">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="85585-564">これにより、`EntityEntry` で示された状態が確実に最新のものとなりました。</span><span class="sxs-lookup"><span data-stu-id="85585-564">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-565">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-565">New behavior</span></span>

<span data-ttu-id="85585-566">EF Core 3.0 以降では、`DbContext.Entry` の呼び出しで、特定のエンティティと、それに関連する追跡対象のプリンシパル エンティティでの変更のみの検出が試行されるようになります。</span><span class="sxs-lookup"><span data-stu-id="85585-566">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="85585-567">つまり、他の場所での変更は、このメソッドの呼び出しで検出されなかった可能性があり、アプリケーション状態に影響する場合があります。</span><span class="sxs-lookup"><span data-stu-id="85585-567">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="85585-568">`ChangeTracker.AutoDetectChangesEnabled` が `false` に設定されている場合、このローカル変更の検出も無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="85585-568">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="85585-569">`ChangeTracker.Entries` や `SaveChanges` など、変更の検出の原因となる他のメソッドは引き続き、すべての追跡対象エンティティの完全な `DetectChanges` の原因となります。</span><span class="sxs-lookup"><span data-stu-id="85585-569">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-570">理由</span><span class="sxs-lookup"><span data-stu-id="85585-570">Why</span></span>

<span data-ttu-id="85585-571">この変更は、`context.Entry` を使用する既定のパフォーマンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-571">This change was made to improve the default performance of using `context.Entry`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-572">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-572">Mitigations</span></span>

<span data-ttu-id="85585-573">3\.0 より前の動作を確保するには、`Entry` を呼び出す前に明示的に `ChangeTracker.DetectChanges()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="85585-573">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="85585-574">文字列とバイト配列のキーが既定でクライアントによって生成されない</span><span class="sxs-lookup"><span data-stu-id="85585-574">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="85585-575">問題 #14617 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-575">Tracking Issue #14617</span></span>](https://github.com/dotnet/efcore/issues/14617)

#### <a name="old-behavior"></a><span data-ttu-id="85585-576">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-576">Old behavior</span></span>

<span data-ttu-id="85585-577">EF Core 3.0 より前では、`string` と `byte[]` のキー プロパティは、null 以外の値を明示的に設定しなくても使用できました。</span><span class="sxs-lookup"><span data-stu-id="85585-577">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="85585-578">このような場合、キーの値は GUID としてクライアントで生成され、`byte[]` のバイトにシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="85585-578">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-579">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-579">New behavior</span></span>

<span data-ttu-id="85585-580">EF Core 3.0 以降では、キー値が設定されていないことを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="85585-580">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-581">理由</span><span class="sxs-lookup"><span data-stu-id="85585-581">Why</span></span>

<span data-ttu-id="85585-582">この変更は、クライアントで生成された `string`/`byte[]` 値が一般的に役に立たず、既定の動作では一般的な方法で生成されたキー値について判断するのが難しくなったため、行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-582">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-583">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-583">Mitigations</span></span>

<span data-ttu-id="85585-584">3\.0 より前の動作は、他の null 以外の値が設定されていない場合に、キー プロパティで生成された値を使用するように明示的に指定することで取得できます。</span><span class="sxs-lookup"><span data-stu-id="85585-584">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="85585-585">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="85585-585">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="85585-586">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="85585-586">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="85585-587">ILoggerFactory がスコープ サービスになった</span><span class="sxs-lookup"><span data-stu-id="85585-587">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="85585-588">問題 #14698 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-588">Tracking Issue #14698</span></span>](https://github.com/dotnet/efcore/issues/14698)

#### <a name="old-behavior"></a><span data-ttu-id="85585-589">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-589">Old behavior</span></span>

<span data-ttu-id="85585-590">EF Core 3.0 より前では、`ILoggerFactory` はシングルトン サービスとして登録されました。</span><span class="sxs-lookup"><span data-stu-id="85585-590">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-591">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-591">New behavior</span></span>

<span data-ttu-id="85585-592">EF Core 3.0 以降では、`ILoggerFactory` がスコープ化されたものとして登録されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85585-592">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-593">理由</span><span class="sxs-lookup"><span data-stu-id="85585-593">Why</span></span>

<span data-ttu-id="85585-594">この変更は、`DbContext` インスタンスでのロガーの関連付けを許可し、他の機能を有効にし、内部サービス プロバイダーの急増などの異常な動作の一部のケースをなくすために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-594">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-595">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-595">Mitigations</span></span>

<span data-ttu-id="85585-596">この変更は、EF Core の内部サービス プロバイダーでカスタム サービスを登録して使用しない限り、アプリケーション コードに影響しないはずです。</span><span class="sxs-lookup"><span data-stu-id="85585-596">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="85585-597">これは一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="85585-597">This isn't common.</span></span>
<span data-ttu-id="85585-598">このような場合、ほとんどのものが引き続き機能しますが、`ILoggerFactory` に依存していたシングルトン サービスは、異なる方法で `ILoggerFactory` を取得するように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-598">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="85585-599">このような状況になった場合は、[EF Core GitHub の問題追跡ツール](https://github.com/dotnet/efcore/issues)で問題を提出し、`ILoggerFactory` の使用方法をお知らせください。これにより、Microsoft では、今後、再びこのように中断しない方法についてよりよく理解できます。</span><span class="sxs-lookup"><span data-stu-id="85585-599">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/dotnet/efcore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="85585-600">遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-600">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="85585-601">問題 #12780 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-601">Tracking Issue #12780</span></span>](https://github.com/dotnet/efcore/issues/12780)

#### <a name="old-behavior"></a><span data-ttu-id="85585-602">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-602">Old behavior</span></span>

<span data-ttu-id="85585-603">EF Core 3.0 より前では、`DbContext` が破棄されると、そのコンテキストから取得されたエンティティの特定のナビゲーション プロパティが完全に読み込まれたかどうかを知る方法はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="85585-603">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="85585-604">プロキシでは、代わりに、null 以外の値がある場合は参照ナビゲーションが読み込まれ、空でない場合はコレクション ナビゲーションが読み込まれたと見なされます。</span><span class="sxs-lookup"><span data-stu-id="85585-604">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="85585-605">このような場合、遅延読み込みを試みても操作なしとなります。</span><span class="sxs-lookup"><span data-stu-id="85585-605">In these cases, attempting to lazy-load would be a no-op.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-606">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-606">New behavior</span></span>

<span data-ttu-id="85585-607">EF Core 3.0 以降では、プロキシで、ナビゲーション プロパティが読み込まれたかどうかの追跡が行われます。</span><span class="sxs-lookup"><span data-stu-id="85585-607">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="85585-608">つまり、コンテキストが破棄された後に読み込まれたナビゲーション プロパティにアクセスしようとしても、通常は操作なしとなります。読み込まれたナビゲーションが空でも null であっても同様です。</span><span class="sxs-lookup"><span data-stu-id="85585-608">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="85585-609">逆に、コンテキストが破棄された場合、読み込まれなかったナビゲーション プロパティにアクセスしようとすると例外がスローされます。ナビゲーション プロパティが空ではないコレクションである場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="85585-609">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="85585-610">このような状況が発生した場合、アプリケーション コードで無効な時間に遅延読み込みの使用が試行されていることを意味し、アプリケーションを変更して、このようにならないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-610">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-611">理由</span><span class="sxs-lookup"><span data-stu-id="85585-611">Why</span></span>

<span data-ttu-id="85585-612">この変更は、破棄された `DbContext` インスタンスで遅延読み込みが試行されたときの動作を一貫した正しいものにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-612">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-613">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-613">Mitigations</span></span>

<span data-ttu-id="85585-614">破棄されたコンテキストで遅延読み込みが試行されないようにアプリケーション コードを更新するか、例外メッセージの説明に従って、これを操作なしとなるように構成します。</span><span class="sxs-lookup"><span data-stu-id="85585-614">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="85585-615">内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-615">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="85585-616">問題 #10236 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-616">Tracking Issue #10236</span></span>](https://github.com/dotnet/efcore/issues/10236)

#### <a name="old-behavior"></a><span data-ttu-id="85585-617">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-617">Old behavior</span></span>

<span data-ttu-id="85585-618">EF Core 3.0 より前では、異常な数の内部サービス プロバイダーを作成するアプリケーションに対する警告がログに記録されました。</span><span class="sxs-lookup"><span data-stu-id="85585-618">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-619">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-619">New behavior</span></span>

<span data-ttu-id="85585-620">EF Core 3.0 以降では、この警告はエラーと見なされるようになり、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="85585-620">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-621">理由</span><span class="sxs-lookup"><span data-stu-id="85585-621">Why</span></span>

<span data-ttu-id="85585-622">この変更は、この異常なケースをより明示的に示すことで、アプリケーション コードの改善を促進するために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-622">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-623">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-623">Mitigations</span></span>

<span data-ttu-id="85585-624">このエラーの発生時の最も適切なアクションは、根本原因を理解し、非常に多くの内部サービス プロバイダーを作成しないようにすることです。</span><span class="sxs-lookup"><span data-stu-id="85585-624">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="85585-625">しかし、`DbContextOptionsBuilder` での構成を使用して、エラーを警告に戻す (または無視する) ことがことができます。</span><span class="sxs-lookup"><span data-stu-id="85585-625">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="85585-626">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-626">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="85585-627">1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-627">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="85585-628">問題 #9171 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-628">Tracking Issue #9171</span></span>](https://github.com/dotnet/efcore/issues/9171)

#### <a name="old-behavior"></a><span data-ttu-id="85585-629">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-629">Old behavior</span></span>

<span data-ttu-id="85585-630">EF Core 3.0 以前では、1 つの文字列と共に `HasOne` または `HasMany` が呼び出されるコードは、わかりにくい方法で解釈されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-630">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="85585-631">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-631">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="85585-632">このコードは、プライベートである可能性がある `Entrance` ナビゲーション プロパティを使って、`Samurai` を他のエンティティ型に関連付けているように見えます。</span><span class="sxs-lookup"><span data-stu-id="85585-632">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="85585-633">実際には、このコードは、ナビゲーション プロパティなしで、`Entrance` と呼ばれるエンティティ型に対してリレーションシップを作成しようとしています。</span><span class="sxs-lookup"><span data-stu-id="85585-633">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-634">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-634">New behavior</span></span>

<span data-ttu-id="85585-635">EF Core 3.0 以降では、上記のコードは、前にそれが実行すべきであるように見えたことを実行するようになりました。</span><span class="sxs-lookup"><span data-stu-id="85585-635">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-636">理由</span><span class="sxs-lookup"><span data-stu-id="85585-636">Why</span></span>

<span data-ttu-id="85585-637">以前の動作は、特に構成コードを読み取ってエラーを探す場合は、非常にわかりにくいものでした。</span><span class="sxs-lookup"><span data-stu-id="85585-637">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-638">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-638">Mitigations</span></span>

<span data-ttu-id="85585-639">これが中断させるのは、型名の文字列を使って明示的にリレーションシップを構成し、かつ明示的にナビゲーション プロパティを指定しないアプリケーションのみです。</span><span class="sxs-lookup"><span data-stu-id="85585-639">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="85585-640">これは一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="85585-640">This is not common.</span></span>
<span data-ttu-id="85585-641">前の動作は、ナビゲーション プロパティ名に対して明示的に `null` を渡すことで実現できます。</span><span class="sxs-lookup"><span data-stu-id="85585-641">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="85585-642">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-642">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="85585-643">いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更されました</span><span class="sxs-lookup"><span data-stu-id="85585-643">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="85585-644">問題 #15184 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-644">Tracking Issue #15184</span></span>](https://github.com/dotnet/efcore/issues/15184)

#### <a name="old-behavior"></a><span data-ttu-id="85585-645">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-645">Old behavior</span></span>

<span data-ttu-id="85585-646">次の非同期メソッドでは、以前は `Task<T>` が返されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-646">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="85585-647">`ValueGenerator.NextValueAsync()` (および派生クラス)</span><span class="sxs-lookup"><span data-stu-id="85585-647">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-648">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-648">New behavior</span></span>

<span data-ttu-id="85585-649">前述のメソッドは、以前と同じ `T` に対して `ValueTask<T>` を返すようになりました。</span><span class="sxs-lookup"><span data-stu-id="85585-649">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-650">理由</span><span class="sxs-lookup"><span data-stu-id="85585-650">Why</span></span>

<span data-ttu-id="85585-651">この変更により、これらのメソッドを呼び出すときに発生するヒープ割り当ての数を削減して全般的なパフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="85585-651">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-652">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-652">Mitigations</span></span>

<span data-ttu-id="85585-653">上記の API を待機しているだけのアプリケーションのみを再コンパイルする必要があります。ソースの変更は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="85585-653">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="85585-654">より複雑な使用方法 (返された `Task` を `Task.WhenAny()` に渡すなど) では通常、返された `ValueTask<T>` を `AsTask()` を呼び出すことによって `Task<T>` に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-654">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="85585-655">これにより、この変更による割り当ての削減が無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="85585-655">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="85585-656">Relational:TypeMapping 注釈が単に TypeMapping となった</span><span class="sxs-lookup"><span data-stu-id="85585-656">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="85585-657">問題 #9913 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-657">Tracking Issue #9913</span></span>](https://github.com/dotnet/efcore/issues/9913)

#### <a name="old-behavior"></a><span data-ttu-id="85585-658">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-658">Old behavior</span></span>

<span data-ttu-id="85585-659">型マッピング注釈の注釈名は "Relational:TypeMapping" でした。</span><span class="sxs-lookup"><span data-stu-id="85585-659">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-660">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-660">New behavior</span></span>

<span data-ttu-id="85585-661">型マッピング注釈の注釈名は、"TypeMapping" となりました。</span><span class="sxs-lookup"><span data-stu-id="85585-661">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

#### <a name="why"></a><span data-ttu-id="85585-662">理由</span><span class="sxs-lookup"><span data-stu-id="85585-662">Why</span></span>

<span data-ttu-id="85585-663">型マッピングが、リレーショナル データベース プロバイダー以外でも使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85585-663">Type mappings are now used for more than just relational database providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-664">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-664">Mitigations</span></span>

<span data-ttu-id="85585-665">これにより、一般的ではない、注釈として直接型マッピングにアクセスするアプリケーションのみが中断されるようになります。</span><span class="sxs-lookup"><span data-stu-id="85585-665">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="85585-666">問題を解決するための最も適切なアクションは、注釈を直接使用するのではなく、API サーフェスを使用して型マッピングにアクセスすることです。</span><span class="sxs-lookup"><span data-stu-id="85585-666">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="85585-667">派生型の ToTable で例外がスローされる</span><span class="sxs-lookup"><span data-stu-id="85585-667">ToTable on a derived type throws an exception</span></span>

[<span data-ttu-id="85585-668">問題 #11811 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-668">Tracking Issue #11811</span></span>](https://github.com/dotnet/efcore/issues/11811)

#### <a name="old-behavior"></a><span data-ttu-id="85585-669">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-669">Old behavior</span></span>

<span data-ttu-id="85585-670">EF Core 3.0 より前では、唯一の継承マッピング方法が TPH であり、これが有効でない場合には、派生型で呼び出された `ToTable()` が無視されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-670">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-671">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-671">New behavior</span></span>

<span data-ttu-id="85585-672">EF Core 3.0 以降、および今後のリリースでの TPT と TPC のサポートの追加準備中には、派生型で呼び出された `ToTable()` で、今後の予期されないマッピング変更を回避するために例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="85585-672">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-673">理由</span><span class="sxs-lookup"><span data-stu-id="85585-673">Why</span></span>

<span data-ttu-id="85585-674">現在、派生型を別のテーブルにマップすることは有効ではありません。</span><span class="sxs-lookup"><span data-stu-id="85585-674">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="85585-675">この変更により、今後、これを行うことが有効になった場合に中断されなくなります。</span><span class="sxs-lookup"><span data-stu-id="85585-675">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-676">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-676">Mitigations</span></span>

<span data-ttu-id="85585-677">派生型を他のテーブルにマップしないようにします。</span><span class="sxs-lookup"><span data-stu-id="85585-677">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="85585-678">ForSqlServerHasIndex が HasIndex に置き換えられた</span><span class="sxs-lookup"><span data-stu-id="85585-678">ForSqlServerHasIndex replaced with HasIndex</span></span>

[<span data-ttu-id="85585-679">問題 #12366 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-679">Tracking Issue #12366</span></span>](https://github.com/dotnet/efcore/issues/12366)

#### <a name="old-behavior"></a><span data-ttu-id="85585-680">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-680">Old behavior</span></span>

<span data-ttu-id="85585-681">EF Core 3.0 より前では、`ForSqlServerHasIndex().ForSqlServerInclude()` によって、`INCLUDE` で使用される列を構成する方法が提供されました。</span><span class="sxs-lookup"><span data-stu-id="85585-681">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-682">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-682">New behavior</span></span>

<span data-ttu-id="85585-683">EF Core 3.0 以降では、インデックスでの `Include` の使用が、リレーショナル レベルでサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85585-683">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="85585-684">`HasIndex().ForSqlServerInclude()` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="85585-684">Use `HasIndex().ForSqlServerInclude()`.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-685">理由</span><span class="sxs-lookup"><span data-stu-id="85585-685">Why</span></span>

<span data-ttu-id="85585-686">この変更は、すべてのデータ プロバイダーのために `Include` でインデックス用の API を 1 か所に統合するために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-686">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-687">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-687">Mitigations</span></span>

<span data-ttu-id="85585-688">上記のように、新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="85585-688">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="85585-689">メタデータ API の変更点</span><span class="sxs-lookup"><span data-stu-id="85585-689">Metadata API changes</span></span>

[<span data-ttu-id="85585-690">問題 #214 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-690">Tracking Issue #214</span></span>](https://github.com/dotnet/efcore/issues/214)

#### <a name="new-behavior"></a><span data-ttu-id="85585-691">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-691">New behavior</span></span>

<span data-ttu-id="85585-692">次のプロパティは拡張メソッドに変換されました。</span><span class="sxs-lookup"><span data-stu-id="85585-692">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

#### <a name="why"></a><span data-ttu-id="85585-693">理由</span><span class="sxs-lookup"><span data-stu-id="85585-693">Why</span></span>

<span data-ttu-id="85585-694">この変更により、前述のインターフェイスの実装が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="85585-694">This change simplifies the implementation of the aforementioned interfaces.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-695">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-695">Mitigations</span></span>

<span data-ttu-id="85585-696">新しい拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="85585-696">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="85585-697">プロバイダー固有のメタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="85585-697">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="85585-698">問題 #214 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-698">Tracking Issue #214</span></span>](https://github.com/dotnet/efcore/issues/214)

#### <a name="new-behavior"></a><span data-ttu-id="85585-699">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-699">New behavior</span></span>

<span data-ttu-id="85585-700">プロバイダー固有の拡張メソッドがフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="85585-700">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

#### <a name="why"></a><span data-ttu-id="85585-701">理由</span><span class="sxs-lookup"><span data-stu-id="85585-701">Why</span></span>

<span data-ttu-id="85585-702">この変更により、前述の拡張メソッドの実装が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="85585-702">This change simplifies the implementation of the aforementioned extension methods.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-703">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-703">Mitigations</span></span>

<span data-ttu-id="85585-704">新しい拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="85585-704">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="85585-705">EF Core で SQLite FK を適用するためのプラグマが送信されなくなった</span><span class="sxs-lookup"><span data-stu-id="85585-705">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="85585-706">問題 #12151 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-706">Tracking Issue #12151</span></span>](https://github.com/dotnet/efcore/issues/12151)

#### <a name="old-behavior"></a><span data-ttu-id="85585-707">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-707">Old behavior</span></span>

<span data-ttu-id="85585-708">EF Core 3.0 より前では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されました。</span><span class="sxs-lookup"><span data-stu-id="85585-708">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-709">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-709">New behavior</span></span>

<span data-ttu-id="85585-710">EF Core 3.0 以降では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="85585-710">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-711">理由</span><span class="sxs-lookup"><span data-stu-id="85585-711">Why</span></span>

<span data-ttu-id="85585-712">この変更は、既定で EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用され、その後、FK の適用が既定で有効になり、接続が開かれるたびに明示的に有効にする必要がないため、行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-712">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-713">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-713">Mitigations</span></span>

<span data-ttu-id="85585-714">外部キーは、EF Core に対して既定で使用される、SQLitePCLRaw.bundle_e_sqlite3 で既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="85585-714">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="85585-715">それ以外の場合は、接続文字列で `Foreign Keys=True` を指定することで、外部キーを有効にできます。</span><span class="sxs-lookup"><span data-stu-id="85585-715">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="85585-716">Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった</span><span class="sxs-lookup"><span data-stu-id="85585-716">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="85585-717">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-717">Old behavior</span></span>

<span data-ttu-id="85585-718">EF Core 3.0 より前では、EF Core では `SQLitePCLRaw.bundle_green` が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-718">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-719">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-719">New behavior</span></span>

<span data-ttu-id="85585-720">EF Core 3.0 以降では、EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="85585-720">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-721">理由</span><span class="sxs-lookup"><span data-stu-id="85585-721">Why</span></span>

<span data-ttu-id="85585-722">この変更は、iOS 上で使用される SQLite のバージョンを、他のプラットフォームと一致させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="85585-722">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-723">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-723">Mitigations</span></span>

<span data-ttu-id="85585-724">iOS でネイティブの SQLite バージョンを使用するには、別の `SQLitePCLRaw` バンドルを使用するように `Microsoft.Data.Sqlite` を構成します。</span><span class="sxs-lookup"><span data-stu-id="85585-724">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="85585-725">GUID の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-725">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="85585-726">問題 #15078 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-726">Tracking Issue #15078</span></span>](https://github.com/dotnet/efcore/issues/15078)

#### <a name="old-behavior"></a><span data-ttu-id="85585-727">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-727">Old behavior</span></span>

<span data-ttu-id="85585-728">GUID の値は、以前は SQLite に BLOB 値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-728">Guid values were previously stored as BLOB values on SQLite.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-729">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-729">New behavior</span></span>

<span data-ttu-id="85585-730">GUID の値はテキストとして格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85585-730">Guid values are now stored as TEXT.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-731">理由</span><span class="sxs-lookup"><span data-stu-id="85585-731">Why</span></span>

<span data-ttu-id="85585-732">GUID のバイナリ形式は標準化されていません。</span><span class="sxs-lookup"><span data-stu-id="85585-732">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="85585-733">値をテキストとして格納する方が、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="85585-733">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-734">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-734">Mitigations</span></span>

<span data-ttu-id="85585-735">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="85585-735">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

```sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

<span data-ttu-id="85585-736">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="85585-736">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="85585-737">Microsoft.Data.Sqlite は引き続き、BLOB とテキストの両方の列から GUID 値を読み取ることができます。ただし、パラメーターと定数の既定の形式が変更されているため、GUID が含まれる多くのシナリオではアクションが必要になります。</span><span class="sxs-lookup"><span data-stu-id="85585-737">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="85585-738">Char の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-738">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="85585-739">問題 #15020 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-739">Tracking Issue #15020</span></span>](https://github.com/dotnet/efcore/issues/15020)

#### <a name="old-behavior"></a><span data-ttu-id="85585-740">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-740">Old behavior</span></span>

<span data-ttu-id="85585-741">Char の値は、以前は SQLite に整数値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-741">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="85585-742">たとえば、Char の値 *A* は整数値 65 として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-742">For example, a char value of *A* was stored as the integer value 65.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-743">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-743">New behavior</span></span>

<span data-ttu-id="85585-744">Char の値はテキストとして格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85585-744">Char values are now stored as TEXT.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-745">理由</span><span class="sxs-lookup"><span data-stu-id="85585-745">Why</span></span>

<span data-ttu-id="85585-746">値をテキストとして格納する方が自然であり、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="85585-746">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-747">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-747">Mitigations</span></span>

<span data-ttu-id="85585-748">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="85585-748">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

```sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="85585-749">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="85585-749">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="85585-750">Microsoft.Data.Sqlite では、引き続き整数とテキストの両方の列の文字列値を読み取ることができるため、一部のシナリオではアクションが必要ありません。</span><span class="sxs-lookup"><span data-stu-id="85585-750">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="85585-751">移行 ID がインバリアント カルチャの暦を使用して生成されるようになった</span><span class="sxs-lookup"><span data-stu-id="85585-751">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="85585-752">問題 #12978 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-752">Tracking Issue #12978</span></span>](https://github.com/dotnet/efcore/issues/12978)

#### <a name="old-behavior"></a><span data-ttu-id="85585-753">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-753">Old behavior</span></span>

<span data-ttu-id="85585-754">移行 ID は、誤って現在のカルチャの暦を使用して生成されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-754">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-755">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-755">New behavior</span></span>

<span data-ttu-id="85585-756">移行 ID がインバリアント カルチャの暦 (グレゴリオ暦) を使用して常に生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85585-756">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

#### <a name="why"></a><span data-ttu-id="85585-757">理由</span><span class="sxs-lookup"><span data-stu-id="85585-757">Why</span></span>

<span data-ttu-id="85585-758">データベースを更新するときやマージの競合を解決するときに、移行の順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="85585-758">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="85585-759">インバリアントの暦を使用することで、順序の問題が回避され、システムの予定表がチームのメンバーごとに違うことがなくなります。</span><span class="sxs-lookup"><span data-stu-id="85585-759">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-760">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-760">Mitigations</span></span>

<span data-ttu-id="85585-761">この変更は、1 年がグレゴリオ暦より長くなるグレゴリオ暦以外の暦 (タイ仏暦など) を使用しているすべてのユーザーに影響します。</span><span class="sxs-lookup"><span data-stu-id="85585-761">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="85585-762">既存の移行後に新しい移行順序が設定されるように、既存の移行 ID を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-762">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="85585-763">移行 ID は、移行のデザイナー ファイルの移行属性にあります。</span><span class="sxs-lookup"><span data-stu-id="85585-763">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

```diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="85585-764">移行履歴テーブルも更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-764">The Migrations history table also needs to be updated.</span></span>

```sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="85585-765">UseRowNumberForPaging の削除</span><span class="sxs-lookup"><span data-stu-id="85585-765">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="85585-766">問題 #16400 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-766">Tracking Issue #16400</span></span>](https://github.com/dotnet/efcore/issues/16400)

#### <a name="old-behavior"></a><span data-ttu-id="85585-767">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-767">Old behavior</span></span>

<span data-ttu-id="85585-768">EF Core 3.0 より前では、`UseRowNumberForPaging` を使って、SQL Server 2008 と互換性のあるページング用の SQL を生成することができました。</span><span class="sxs-lookup"><span data-stu-id="85585-768">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-769">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-769">New behavior</span></span>

<span data-ttu-id="85585-770">EF Core 3.0 以降では、EF によって生成できるのは、それ以降のバージョンの SQL Server とのみ互換性があるページング用の SQL のみとなります。</span><span class="sxs-lookup"><span data-stu-id="85585-770">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-771">理由</span><span class="sxs-lookup"><span data-stu-id="85585-771">Why</span></span>

<span data-ttu-id="85585-772">[SQL Server 2008 はもうサポートされていない製品であり](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/)、EF Core 3.0 で行われたクエリの変更に対応するようこの機能を更新する作業は負荷が大きいため、このような変更が行われます。</span><span class="sxs-lookup"><span data-stu-id="85585-772">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-773">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-773">Mitigations</span></span>

<span data-ttu-id="85585-774">生成された SQL がサポートされるように、より新しいバージョンの SQL Server に更新するか、より高い互換性レベルを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="85585-774">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="85585-775">ただし、これを実行できない場合は、詳細情報とともに[問題の追跡にコメント](https://github.com/dotnet/efcore/issues/16400)してください。</span><span class="sxs-lookup"><span data-stu-id="85585-775">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/dotnet/efcore/issues/16400) with details.</span></span> <span data-ttu-id="85585-776">Microsoft では、フィードバックに基づいてこの決定を再検討する場合があります。</span><span class="sxs-lookup"><span data-stu-id="85585-776">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="85585-777">IDbContextOptionsExtension から拡張機能の情報/メタデータを削除</span><span class="sxs-lookup"><span data-stu-id="85585-777">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="85585-778">問題 #16119 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-778">Tracking Issue #16119</span></span>](https://github.com/dotnet/efcore/issues/16119)

#### <a name="old-behavior"></a><span data-ttu-id="85585-779">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-779">Old behavior</span></span>

<span data-ttu-id="85585-780">`IDbContextOptionsExtension` には、拡張機能に関するメタデータを提供するためのメソッドが含まれていました。</span><span class="sxs-lookup"><span data-stu-id="85585-780">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-781">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-781">New behavior</span></span>

<span data-ttu-id="85585-782">これらのメソッドは、新しい抽象基底クラス `DbContextOptionsExtensionInfo` 上に移動されました。これは新しい `IDbContextOptionsExtension.Info` プロパティから返されます。</span><span class="sxs-lookup"><span data-stu-id="85585-782">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-783">理由</span><span class="sxs-lookup"><span data-stu-id="85585-783">Why</span></span>

<span data-ttu-id="85585-784">2\.0 から 3.0 までのリリースを通して、これらのメソッドに追加または変更を行う必要が複数回発生しました。</span><span class="sxs-lookup"><span data-stu-id="85585-784">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="85585-785">それらを抜き出して新しい抽象基底クラスに含めることで、既存の拡張機能を損なうことなく、簡単にこのような変更を加えられるようになります。</span><span class="sxs-lookup"><span data-stu-id="85585-785">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-786">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-786">Mitigations</span></span>

<span data-ttu-id="85585-787">新しいパターンに従うよう拡張機能を更新します。</span><span class="sxs-lookup"><span data-stu-id="85585-787">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="85585-788">その例は、EF Core のソース コードの、さまざまな種類の拡張機能に対する多数の `IDbContextOptionsExtension` の実装で見つかります。</span><span class="sxs-lookup"><span data-stu-id="85585-788">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="85585-789">LogQueryPossibleExceptionWithAggregateOperator の名前が変更された</span><span class="sxs-lookup"><span data-stu-id="85585-789">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="85585-790">問題 #10985 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-790">Tracking Issue #10985</span></span>](https://github.com/dotnet/efcore/issues/10985)

#### <a name="change"></a><span data-ttu-id="85585-791">Change</span><span class="sxs-lookup"><span data-stu-id="85585-791">Change</span></span>

<span data-ttu-id="85585-792">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` の名前が `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="85585-792">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-793">理由</span><span class="sxs-lookup"><span data-stu-id="85585-793">Why</span></span>

<span data-ttu-id="85585-794">この警告イベントの名前が他のすべての警告イベントと照合されるためです。</span><span class="sxs-lookup"><span data-stu-id="85585-794">Aligns the naming of this warning event with all other warning events.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-795">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-795">Mitigations</span></span>

<span data-ttu-id="85585-796">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="85585-796">Use the new name.</span></span> <span data-ttu-id="85585-797">(イベント ID 番号が変更されていないことを確認してください。)</span><span class="sxs-lookup"><span data-stu-id="85585-797">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="85585-798">外部キー制約名の API が明確化された</span><span class="sxs-lookup"><span data-stu-id="85585-798">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="85585-799">問題 #10730 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-799">Tracking Issue #10730</span></span>](https://github.com/dotnet/efcore/issues/10730)

#### <a name="old-behavior"></a><span data-ttu-id="85585-800">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-800">Old behavior</span></span>

<span data-ttu-id="85585-801">EF Core 3.0 以前は、外部キー制約名が単に "名前" と呼ばれていました。</span><span class="sxs-lookup"><span data-stu-id="85585-801">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="85585-802">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-802">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

#### <a name="new-behavior"></a><span data-ttu-id="85585-803">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-803">New behavior</span></span>

<span data-ttu-id="85585-804">EF Core 3.0 以降は、外部キー制約名が "制約名" と呼ばれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="85585-804">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="85585-805">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-805">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

#### <a name="why"></a><span data-ttu-id="85585-806">理由</span><span class="sxs-lookup"><span data-stu-id="85585-806">Why</span></span>

<span data-ttu-id="85585-807">この変更により、この領域の名前付けに一貫性がもたらされます。また、それが外部キーが定義されている列やプロパティの名前ではなく、外部キー制約の名前であることが明確になりました。</span><span class="sxs-lookup"><span data-stu-id="85585-807">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-808">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-808">Mitigations</span></span>

<span data-ttu-id="85585-809">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="85585-809">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="85585-810">IRelationalDatabaseCreator.HasTables/HasTablesAsync をパブリックに変更</span><span class="sxs-lookup"><span data-stu-id="85585-810">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="85585-811">問題 #15997 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-811">Tracking Issue #15997</span></span>](https://github.com/dotnet/efcore/issues/15997)

#### <a name="old-behavior"></a><span data-ttu-id="85585-812">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-812">Old behavior</span></span>

<span data-ttu-id="85585-813">EF Core 3.0 より前では、これらのメソッドは保護されていました。</span><span class="sxs-lookup"><span data-stu-id="85585-813">Before EF Core 3.0, these methods were protected.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-814">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-814">New behavior</span></span>

<span data-ttu-id="85585-815">EF Core 3.0 以降、これらのメソッドはパブリックになります。</span><span class="sxs-lookup"><span data-stu-id="85585-815">Starting with EF Core 3.0, these methods are public.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-816">理由</span><span class="sxs-lookup"><span data-stu-id="85585-816">Why</span></span>

<span data-ttu-id="85585-817">これらのメソッドは、作成されたデータベースが空であるかどうかを判断するために EF によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="85585-817">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="85585-818">これは、EF の外部から、移行を適用するかどうか判断する場合にも役立ちます。</span><span class="sxs-lookup"><span data-stu-id="85585-818">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-819">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-819">Mitigations</span></span>

<span data-ttu-id="85585-820">すべてのオーバーライドのアクセシビリティを変更します。</span><span class="sxs-lookup"><span data-stu-id="85585-820">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="85585-821">Microsoft.EntityFrameworkCore.Design を DevelopmentDependency パッケージに変更</span><span class="sxs-lookup"><span data-stu-id="85585-821">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="85585-822">問題 #11506 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-822">Tracking Issue #11506</span></span>](https://github.com/dotnet/efcore/issues/11506)

#### <a name="old-behavior"></a><span data-ttu-id="85585-823">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-823">Old behavior</span></span>

<span data-ttu-id="85585-824">EF Core 3.0 より前では、Microsoft.EntityFrameworkCore.Design は通常の NuGet パッケージであり、それに依存していたプロジェクトによってそのアセンブリを参照できました。</span><span class="sxs-lookup"><span data-stu-id="85585-824">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-825">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-825">New behavior</span></span>

<span data-ttu-id="85585-826">EF Core 3.0 以降、これは DevelopmentDependency パッケージになります。</span><span class="sxs-lookup"><span data-stu-id="85585-826">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="85585-827">つまり、依存関係が他のプロジェクトに推移的にフローすることがなくなり、規定ではそのアセンブリを参照できなくなります。</span><span class="sxs-lookup"><span data-stu-id="85585-827">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-828">理由</span><span class="sxs-lookup"><span data-stu-id="85585-828">Why</span></span>

<span data-ttu-id="85585-829">このパッケージは、デザイン時に使用されることだけを想定しています。</span><span class="sxs-lookup"><span data-stu-id="85585-829">This package is only intended to be used at design time.</span></span> <span data-ttu-id="85585-830">デプロイされたアプリケーションからこれを参照すべきではありません。</span><span class="sxs-lookup"><span data-stu-id="85585-830">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="85585-831">パッケージを DevelopmentDependency にすることで、この推奨事項が強化されます。</span><span class="sxs-lookup"><span data-stu-id="85585-831">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-832">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-832">Mitigations</span></span>

<span data-ttu-id="85585-833">EF Core のデザイン時の動作をオーバーライドするためにこのパッケージを参照する必要がある場合は、プロジェクトの PackageReference 項目メタデータを更新することができます。</span><span class="sxs-lookup"><span data-stu-id="85585-833">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="85585-834">このパッケージが Microsoft.EntityFrameworkCore.Tools 経由で推移的に参照されている場合は、パッケージに明示的な PackageReference を追加して、そのメタデータを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-834">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="85585-835">このような明示的な参照は、パッケージの型が必要なプロジェクトに追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-835">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="85585-836">SQLitePCL.raw のバージョン 2.0.0 への更新</span><span class="sxs-lookup"><span data-stu-id="85585-836">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="85585-837">問題 #14824 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-837">Tracking Issue #14824</span></span>](https://github.com/dotnet/efcore/issues/14824)

#### <a name="old-behavior"></a><span data-ttu-id="85585-838">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-838">Old behavior</span></span>

<span data-ttu-id="85585-839">Microsoft.EntityFrameworkCore.Sqlite は、以前はバージョン 1.1.12 の SQLitePCL.raw に依存していました。</span><span class="sxs-lookup"><span data-stu-id="85585-839">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-840">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-840">New behavior</span></span>

<span data-ttu-id="85585-841">バージョン 2.0.0 に依存するようパッケージが更新されました。</span><span class="sxs-lookup"><span data-stu-id="85585-841">We've updated our package to depend on version 2.0.0.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-842">理由</span><span class="sxs-lookup"><span data-stu-id="85585-842">Why</span></span>

<span data-ttu-id="85585-843">バージョン 2.0.0 の SQLitePCL.raw では、.NET Standard 2.0 をターゲットとします。</span><span class="sxs-lookup"><span data-stu-id="85585-843">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="85585-844">これは以前は、推移的なパッケージの大規模なクロージャの動作を必要とする .NET Standard 1.1 をターゲットとしていました。</span><span class="sxs-lookup"><span data-stu-id="85585-844">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-845">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-845">Mitigations</span></span>

<span data-ttu-id="85585-846">SQLitePCL.raw バージョン 2.0.0 には、いくつかの破壊的変更が含まれています。</span><span class="sxs-lookup"><span data-stu-id="85585-846">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="85585-847">詳細については、[リリース ノート](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="85585-847">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="85585-848">NetTopologySuite のバージョン 2.0.0 への更新</span><span class="sxs-lookup"><span data-stu-id="85585-848">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="85585-849">問題 #14825 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-849">Tracking Issue #14825</span></span>](https://github.com/dotnet/efcore/issues/14825)

#### <a name="old-behavior"></a><span data-ttu-id="85585-850">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-850">Old behavior</span></span>

<span data-ttu-id="85585-851">以前の空間パッケージはバージョン 1.15.1 の NetTopologySuite に依存していました。</span><span class="sxs-lookup"><span data-stu-id="85585-851">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-852">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-852">New behavior</span></span>

<span data-ttu-id="85585-853">バージョン 2.0.0 に依存するようパッケージが更新されました。</span><span class="sxs-lookup"><span data-stu-id="85585-853">We've update our package to depend on version 2.0.0.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-854">理由</span><span class="sxs-lookup"><span data-stu-id="85585-854">Why</span></span>

<span data-ttu-id="85585-855">バージョン 2.0.0 の NetTopologySuite は、EF Core の使用に伴ういくつかのユーザビリティの問題に対処することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="85585-855">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-856">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-856">Mitigations</span></span>

<span data-ttu-id="85585-857">NetTopologySuite バージョン 2.0.0 には、いくつかの破壊的変更が含まれています。</span><span class="sxs-lookup"><span data-stu-id="85585-857">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="85585-858">詳細については、[リリース ノート](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="85585-858">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="85585-859">System.Data.SqlClient ではなく Microsoft.Data.SqlClient が使用される</span><span class="sxs-lookup"><span data-stu-id="85585-859">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="85585-860">問題 #15636 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-860">Tracking Issue #15636</span></span>](https://github.com/dotnet/efcore/issues/15636)

#### <a name="old-behavior"></a><span data-ttu-id="85585-861">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-861">Old behavior</span></span>

<span data-ttu-id="85585-862">Microsoft.EntityFrameworkCore.SqlServer は、以前は System.Data.SqlClient に依存していました。</span><span class="sxs-lookup"><span data-stu-id="85585-862">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="85585-863">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-863">New behavior</span></span>

<span data-ttu-id="85585-864">Microsoft.Data.SqlClient に依存するようにパッケージを更新しました。</span><span class="sxs-lookup"><span data-stu-id="85585-864">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-865">理由</span><span class="sxs-lookup"><span data-stu-id="85585-865">Why</span></span>

<span data-ttu-id="85585-866">Microsoft.Data.SqlClient は今後の SQL Server の主力データ アクセス ドライバーであり、System.Data.SqlClient は開発の中心ではなくなりました。</span><span class="sxs-lookup"><span data-stu-id="85585-866">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="85585-867">Always Encrypted などの重要な機能は、Microsoft.Data.SqlClient でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="85585-867">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-868">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-868">Mitigations</span></span>

<span data-ttu-id="85585-869">コードが System.Data.SqlClient に直接依存している場合は、代わりに Microsoft.Data.SqlClient を参照するように変更する必要があります。2 つのパッケージは非常に高度な API 互換性を維持しているため、これは単純なパッケージと名前空間の変更のみである必要があります。</span><span class="sxs-lookup"><span data-stu-id="85585-869">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="85585-870">複数のあいまいな自己参照リレーションシップを構成する必要がある</span><span class="sxs-lookup"><span data-stu-id="85585-870">Multiple ambiguous self-referencing relationships must be configured</span></span>

[<span data-ttu-id="85585-871">イシュー #13573 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-871">Tracking Issue #13573</span></span>](https://github.com/dotnet/efcore/issues/13573)

#### <a name="old-behavior"></a><span data-ttu-id="85585-872">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-872">Old behavior</span></span>

<span data-ttu-id="85585-873">複数の自己参照型一方向ナビゲーション プロパティと一致する FK を持つエンティティ型が、1 つのリレーションシップとして正しく構成されませんでした。</span><span class="sxs-lookup"><span data-stu-id="85585-873">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="85585-874">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-874">For example:</span></span>

```csharp
public class User
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

#### <a name="new-behavior"></a><span data-ttu-id="85585-875">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-875">New behavior</span></span>

<span data-ttu-id="85585-876">このシナリオがモデル構築で検出されるようになり、モデルがあいまいであることを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="85585-876">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-877">理由</span><span class="sxs-lookup"><span data-stu-id="85585-877">Why</span></span>

<span data-ttu-id="85585-878">結果として得られるモデルはあいまいであり、通常、この場合には間違っている可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="85585-878">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-879">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-879">Mitigations</span></span>

<span data-ttu-id="85585-880">完全に構成されたリレーションシップを使用します。</span><span class="sxs-lookup"><span data-stu-id="85585-880">Use full configuration of the relationship.</span></span> <span data-ttu-id="85585-881">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="85585-881">For example:</span></span>

```csharp
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();

 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```

<a name="udf-empty-string"></a>

### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="85585-882">DbFunction.Schema が null または空の文字列である場合、モデルの既定のスキーマに構成される</span><span class="sxs-lookup"><span data-stu-id="85585-882">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="85585-883">問題 #12757 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-883">Tracking Issue #12757</span></span>](https://github.com/dotnet/efcore/issues/12757)

#### <a name="old-behavior"></a><span data-ttu-id="85585-884">以前の動作</span><span class="sxs-lookup"><span data-stu-id="85585-884">Old behavior</span></span>

<span data-ttu-id="85585-885">空の文字列としてスキーマを使用して構成された DbFunction は、スキーマのない組み込み関数として扱われていました。</span><span class="sxs-lookup"><span data-stu-id="85585-885">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="85585-886">たとえば、次のコードは `DatePart` CLR 関数を SqlServer の組み込み関数 `DATEPART` にマップします。</span><span class="sxs-lookup"><span data-stu-id="85585-886">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

#### <a name="new-behavior"></a><span data-ttu-id="85585-887">新しい動作</span><span class="sxs-lookup"><span data-stu-id="85585-887">New behavior</span></span>

<span data-ttu-id="85585-888">すべての DbFunction マッピングは、ユーザー定義関数にマップされるものと見なされます。</span><span class="sxs-lookup"><span data-stu-id="85585-888">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="85585-889">そのため、空の文字列値を指定すると、関数がモデルの既定のスキーマ内に配置されます。</span><span class="sxs-lookup"><span data-stu-id="85585-889">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="85585-890">それ以外の、スキーマとなり得るものは fluent API の `modelBuilder.HasDefaultSchema()` または `dbo` を使用して明示的に構成されます。</span><span class="sxs-lookup"><span data-stu-id="85585-890">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

#### <a name="why"></a><span data-ttu-id="85585-891">理由</span><span class="sxs-lookup"><span data-stu-id="85585-891">Why</span></span>

<span data-ttu-id="85585-892">以前は空のスキーマを使用して関数を組み込みとして扱っていましたが、そのロジックは、組み込みの関数がどのスキーマにも属していない SqlServer にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="85585-892">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="85585-893">軽減策</span><span class="sxs-lookup"><span data-stu-id="85585-893">Mitigations</span></span>

<span data-ttu-id="85585-894">DbFunction の変換を手動で組み込み関数にマップされるように構成します。</span><span class="sxs-lookup"><span data-stu-id="85585-894">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```

<a name="netstandard21"></a>

### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20-reverted"></a><span data-ttu-id="85585-895">~~EF Core 3.0 では .NET Standard 2.0 ではなく .NET Standard 2.1 がターゲットになる~~が元に戻される</span><span class="sxs-lookup"><span data-stu-id="85585-895">~~EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0~~ Reverted</span></span>

[<span data-ttu-id="85585-896">問題 #15498 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-896">Tracking Issue #15498</span></span>](https://github.com/dotnet/efcore/issues/15498)

<span data-ttu-id="85585-897">EF Core 3.0 のターゲットが .NET Standard 2.1 になります。これは、.NET Framework アプリケーションが除外される重大な変更です。</span><span class="sxs-lookup"><span data-stu-id="85585-897">EF Core 3.0 targets .NET Standard 2.1, which is a breaking change which excludes .NET Framework applications.</span></span> <span data-ttu-id="85585-898">EF Core 3.1 では、これが元に戻され、.NET Standard 2.0 が再度ターゲットになります。</span><span class="sxs-lookup"><span data-stu-id="85585-898">EF Core 3.1 reverted this and targets .NET Standard 2.0 again.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="85585-899">~~クエリの実行がデバッグ レベルでログに記録される~~ 元に戻されます</span><span class="sxs-lookup"><span data-stu-id="85585-899">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="85585-900">問題 #14523 の追跡</span><span class="sxs-lookup"><span data-stu-id="85585-900">Tracking Issue #14523</span></span>](https://github.com/dotnet/efcore/issues/14523)

<span data-ttu-id="85585-901">EF Core 3.0 の新しい構成では、あらゆるイベントのログ レベルをアプリケーションによって指定できるため、この変更は元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="85585-901">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="85585-902">たとえば、SQL のログ記録を `Debug` に切り替えるには、`OnConfiguring` または `AddDbContext` で明示的にレベルを構成します。</span><span class="sxs-lookup"><span data-stu-id="85585-902">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```
