---
title: EF Core 3.0 での破壊的変更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: b2e3881e3454377dab7851cba999ed6b891def4e
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812126"
---
# <a name="breaking-changes-included-in-ef-core-30"></a><span data-ttu-id="a36d7-102">EF Core 3.0 に含まれる破壊的変更</span><span class="sxs-lookup"><span data-stu-id="a36d7-102">Breaking changes included in EF Core 3.0</span></span>
<span data-ttu-id="a36d7-103">以下の API と動作変更により、3.0.0 へのアップグレード時に、既存のアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-103">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="a36d7-104">データベース プロバイダーにのみ影響することが予想される変更については、[プロバイダーの変更](xref:core/providers/provider-log)に関するページに記載されています。</span><span class="sxs-lookup"><span data-stu-id="a36d7-104">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="a36d7-105">まとめ</span><span class="sxs-lookup"><span data-stu-id="a36d7-105">Summary</span></span>

| <span data-ttu-id="a36d7-106">**重大な変更**</span><span class="sxs-lookup"><span data-stu-id="a36d7-106">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="a36d7-107">**影響**</span><span class="sxs-lookup"><span data-stu-id="a36d7-107">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="a36d7-108">LINQ クエリがクライアントで評価されなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-108">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="a36d7-109">High</span><span class="sxs-lookup"><span data-stu-id="a36d7-109">High</span></span>       |
| [<span data-ttu-id="a36d7-110">EF Core 3.0 では .NET Standard 2.0 ではなく .NET Standard 2.1 がターゲットにされる</span><span class="sxs-lookup"><span data-stu-id="a36d7-110">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>](#netstandard21) | <span data-ttu-id="a36d7-111">High</span><span class="sxs-lookup"><span data-stu-id="a36d7-111">High</span></span>      |
| [<span data-ttu-id="a36d7-112">EF Core のコマンドライン ツールである dotnet ef が .NET Core SDK の一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-112">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="a36d7-113">High</span><span class="sxs-lookup"><span data-stu-id="a36d7-113">High</span></span>      |
| [<span data-ttu-id="a36d7-114">DetectChanges でストア生成キーの値が優先される</span><span class="sxs-lookup"><span data-stu-id="a36d7-114">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="a36d7-115">High</span><span class="sxs-lookup"><span data-stu-id="a36d7-115">High</span></span>      |
| [<span data-ttu-id="a36d7-116">FromSql、ExecuteSql、および ExecuteSqlAsync の名前が変更された</span><span class="sxs-lookup"><span data-stu-id="a36d7-116">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="a36d7-117">High</span><span class="sxs-lookup"><span data-stu-id="a36d7-117">High</span></span>      |
| [<span data-ttu-id="a36d7-118">クエリ型がエンティティ型と統合される</span><span class="sxs-lookup"><span data-stu-id="a36d7-118">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="a36d7-119">High</span><span class="sxs-lookup"><span data-stu-id="a36d7-119">High</span></span>      |
| [<span data-ttu-id="a36d7-120">Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-120">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="a36d7-121">Medium</span><span class="sxs-lookup"><span data-stu-id="a36d7-121">Medium</span></span>      |
| [<span data-ttu-id="a36d7-122">既定で連鎖削除がすぐに行われるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-122">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="a36d7-123">Medium</span><span class="sxs-lookup"><span data-stu-id="a36d7-123">Medium</span></span>      |
| [<span data-ttu-id="a36d7-124">関連エンティティの一括読み込みが 1 つのクエリで行われるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-124">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="a36d7-125">Medium</span><span class="sxs-lookup"><span data-stu-id="a36d7-125">Medium</span></span>      |
| [<span data-ttu-id="a36d7-126">DeleteBehavior.Restrict のセマンティクスがクリーンになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-126">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="a36d7-127">Medium</span><span class="sxs-lookup"><span data-stu-id="a36d7-127">Medium</span></span>      |
| [<span data-ttu-id="a36d7-128">所有型のリレーションシップ用の構成 API が変更された</span><span class="sxs-lookup"><span data-stu-id="a36d7-128">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="a36d7-129">Medium</span><span class="sxs-lookup"><span data-stu-id="a36d7-129">Medium</span></span>      |
| [<span data-ttu-id="a36d7-130">各プロパティで独立したメモリ内整数キー生成が使用される</span><span class="sxs-lookup"><span data-stu-id="a36d7-130">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="a36d7-131">Medium</span><span class="sxs-lookup"><span data-stu-id="a36d7-131">Medium</span></span>      |
| [<span data-ttu-id="a36d7-132">追跡なしのクエリでは、識別子の解決が実行されなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-132">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="a36d7-133">Medium</span><span class="sxs-lookup"><span data-stu-id="a36d7-133">Medium</span></span>      |
| [<span data-ttu-id="a36d7-134">メタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="a36d7-134">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="a36d7-135">Medium</span><span class="sxs-lookup"><span data-stu-id="a36d7-135">Medium</span></span>      |
| [<span data-ttu-id="a36d7-136">プロバイダー固有のメタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="a36d7-136">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="a36d7-137">Medium</span><span class="sxs-lookup"><span data-stu-id="a36d7-137">Medium</span></span>      |
| [<span data-ttu-id="a36d7-138">UseRowNumberForPaging が削除された</span><span class="sxs-lookup"><span data-stu-id="a36d7-138">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="a36d7-139">Medium</span><span class="sxs-lookup"><span data-stu-id="a36d7-139">Medium</span></span>      |
| [<span data-ttu-id="a36d7-140">FromSql メソッドをストアド プロシージャと共に使用して構成することができない</span><span class="sxs-lookup"><span data-stu-id="a36d7-140">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="a36d7-141">Medium</span><span class="sxs-lookup"><span data-stu-id="a36d7-141">Medium</span></span>      |
| [<span data-ttu-id="a36d7-142">FromSql メソッドはクエリのルートでのみ指定できる</span><span class="sxs-lookup"><span data-stu-id="a36d7-142">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="a36d7-143">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-143">Low</span></span>      |
| [<span data-ttu-id="a36d7-144">~~クエリの実行がデバッグ レベルでログに記録される~~ 元に戻されます</span><span class="sxs-lookup"><span data-stu-id="a36d7-144">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | <span data-ttu-id="a36d7-145">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-145">Low</span></span>      |
| [<span data-ttu-id="a36d7-146">一時キーの値がエンティティ インスタンスに設定されなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-146">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="a36d7-147">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-147">Low</span></span>      |
| [<span data-ttu-id="a36d7-148">プリンシパルとテーブルを共有する依存エンティティが省略可能になった</span><span class="sxs-lookup"><span data-stu-id="a36d7-148">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="a36d7-149">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-149">Low</span></span>      |
| [<span data-ttu-id="a36d7-150">同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要がある</span><span class="sxs-lookup"><span data-stu-id="a36d7-150">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="a36d7-151">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-151">Low</span></span>      |
| [<span data-ttu-id="a36d7-152">マップされていない型から継承されたプロパティが、すべての派生型の 1 つの列にマップされるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-152">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="a36d7-153">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-153">Low</span></span>      |
| [<span data-ttu-id="a36d7-154">外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-154">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="a36d7-155">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-155">Low</span></span>      |
| [<span data-ttu-id="a36d7-156">データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-156">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="a36d7-157">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-157">Low</span></span>      |
| [<span data-ttu-id="a36d7-158">バッキング フィールドが既定で使用される</span><span class="sxs-lookup"><span data-stu-id="a36d7-158">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="a36d7-159">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-159">Low</span></span>      |
| [<span data-ttu-id="a36d7-160">複数の互換性があるバッキング フィールドが見つかった場合にスローされる</span><span class="sxs-lookup"><span data-stu-id="a36d7-160">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="a36d7-161">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-161">Low</span></span>      |
| [<span data-ttu-id="a36d7-162">フィールド専用プロパティの名前はフィールドの名前に一致する必要がある</span><span class="sxs-lookup"><span data-stu-id="a36d7-162">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="a36d7-163">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-163">Low</span></span>      |
| [<span data-ttu-id="a36d7-164">AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-164">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="a36d7-165">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-165">Low</span></span>      |
| [<span data-ttu-id="a36d7-166">DbContext.Entry でローカルの DetectChanges が実行されるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-166">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="a36d7-167">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-167">Low</span></span>      |
| [<span data-ttu-id="a36d7-168">文字列とバイト配列のキーが既定でクライアントによって生成されない</span><span class="sxs-lookup"><span data-stu-id="a36d7-168">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="a36d7-169">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-169">Low</span></span>      |
| [<span data-ttu-id="a36d7-170">ILoggerFactory がスコープ サービスになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-170">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="a36d7-171">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-171">Low</span></span>      |
| [<span data-ttu-id="a36d7-172">遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-172">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="a36d7-173">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-173">Low</span></span>      |
| [<span data-ttu-id="a36d7-174">内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-174">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="a36d7-175">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-175">Low</span></span>      |
| [<span data-ttu-id="a36d7-176">1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作</span><span class="sxs-lookup"><span data-stu-id="a36d7-176">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="a36d7-177">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-177">Low</span></span>      |
| [<span data-ttu-id="a36d7-178">いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更された</span><span class="sxs-lookup"><span data-stu-id="a36d7-178">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="a36d7-179">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-179">Low</span></span>      |
| [<span data-ttu-id="a36d7-180">Relational:TypeMapping 注釈が単に TypeMapping となった</span><span class="sxs-lookup"><span data-stu-id="a36d7-180">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="a36d7-181">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-181">Low</span></span>      |
| [<span data-ttu-id="a36d7-182">派生型の ToTable で例外がスローされる</span><span class="sxs-lookup"><span data-stu-id="a36d7-182">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="a36d7-183">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-183">Low</span></span>      |
| [<span data-ttu-id="a36d7-184">EF Core で SQLite FK を適用するためのプラグマが送信されなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-184">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="a36d7-185">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-185">Low</span></span>      |
| [<span data-ttu-id="a36d7-186">Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-186">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="a36d7-187">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-187">Low</span></span>      |
| [<span data-ttu-id="a36d7-188">GUID の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-188">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="a36d7-189">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-189">Low</span></span>      |
| [<span data-ttu-id="a36d7-190">Char の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-190">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="a36d7-191">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-191">Low</span></span>      |
| [<span data-ttu-id="a36d7-192">移行 ID がインバリアント カルチャの暦を使用して生成されるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-192">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="a36d7-193">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-193">Low</span></span>      |
| [<span data-ttu-id="a36d7-194">IDbContextOptionsExtension から拡張機能の情報/メタデータが削除された</span><span class="sxs-lookup"><span data-stu-id="a36d7-194">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="a36d7-195">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-195">Low</span></span>      |
| [<span data-ttu-id="a36d7-196">LogQueryPossibleExceptionWithAggregateOperator の名前が変更された</span><span class="sxs-lookup"><span data-stu-id="a36d7-196">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="a36d7-197">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-197">Low</span></span>      |
| [<span data-ttu-id="a36d7-198">外部キー制約名の API が明確化された</span><span class="sxs-lookup"><span data-stu-id="a36d7-198">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="a36d7-199">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-199">Low</span></span>      |
| [<span data-ttu-id="a36d7-200">IRelationalDatabaseCreator.HasTables/HasTablesAsync がパブリックに変更された</span><span class="sxs-lookup"><span data-stu-id="a36d7-200">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="a36d7-201">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-201">Low</span></span>      |
| [<span data-ttu-id="a36d7-202">Microsoft.EntityFrameworkCore.Design が DevelopmentDependency パッケージになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-202">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="a36d7-203">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-203">Low</span></span>      |
| [<span data-ttu-id="a36d7-204">SQLitePCL.raw がバージョン 2.0.0 に更新された</span><span class="sxs-lookup"><span data-stu-id="a36d7-204">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="a36d7-205">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-205">Low</span></span>      |
| [<span data-ttu-id="a36d7-206">NetTopologySuite がバージョン 2.0.0 に更新された</span><span class="sxs-lookup"><span data-stu-id="a36d7-206">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="a36d7-207">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-207">Low</span></span>      |
| [<span data-ttu-id="a36d7-208">System.Data.SqlClient ではなく Microsoft.Data.SqlClient が使用される</span><span class="sxs-lookup"><span data-stu-id="a36d7-208">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="a36d7-209">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-209">Low</span></span>      |
| [<span data-ttu-id="a36d7-210">複数のあいまいな自己参照リレーションシップを構成する必要がある</span><span class="sxs-lookup"><span data-stu-id="a36d7-210">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="a36d7-211">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-211">Low</span></span>      |
| [<span data-ttu-id="a36d7-212">DbFunction.Schema が null または空の文字列である場合、モデルの既定のスキーマに構成される</span><span class="sxs-lookup"><span data-stu-id="a36d7-212">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="a36d7-213">Low</span><span class="sxs-lookup"><span data-stu-id="a36d7-213">Low</span></span>      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="a36d7-214">LINQ クエリがクライアントで評価されなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-214">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="a36d7-215">[問題 #14935 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[問題 #12795 も参照](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="a36d7-215">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="a36d7-216">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-216">**Old behavior**</span></span>

<span data-ttu-id="a36d7-217">3\.0 より前では、EF Core では、SQL またはパラメーターに対するクエリの一部だった式を変換できない場合、クライアントで自動的に式が評価されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-217">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="a36d7-218">既定では、コストの高い式のクライアント評価でのみ警告がトリガーされました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-218">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="a36d7-219">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-219">**New behavior**</span></span>

<span data-ttu-id="a36d7-220">3\.0 以降では、クライアントで評価される最上位のプロジェクション (クエリの最後の `Select()` 呼び出し) の式のみが EF Core で許可されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-220">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="a36d7-221">クエリの他の部分の式を SQL やパラメーターに変換できない場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-221">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="a36d7-222">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-222">**Why**</span></span>

<span data-ttu-id="a36d7-223">クエリの自動クライアント評価では、多くのクエリを実行できます。これらの重要な部分を変換できない場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="a36d7-223">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="a36d7-224">この動作が原因で、予期しない損害を与える動作となる可能性があります。この動作は、運用環境でのみ見られる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-224">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="a36d7-225">たとえば、変換できない `Where()` 呼び出しの条件が原因で、テーブルのすべての行がデータベース サーバーから転送できるようになり、クライアントでフィルターを適用できるようになる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-225">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="a36d7-226">このような状況は、開発時にテーブルにわずかな行が含まれている場合は、簡単に検出される可能性があります。しかし、テーブルに数百万もの行が含まれる、運用環境にアプリケーションを移行する場合は難しくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-226">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="a36d7-227">クライアント評価の警告では、開発中にあまりに簡単に無視されることも示されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-227">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="a36d7-228">これだけでなく、自動クライアント評価は、特定の式のクエリ変換の改善が、リリース間の予期しない破壊的変更の原因となる問題につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-228">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="a36d7-229">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-229">**Mitigations**</span></span>

<span data-ttu-id="a36d7-230">クエリを完全に変換できない場合は、変換できる形式でクエリを書き直すか、`AsEnumerable()`、`ToList()`、または同様のものを使用して、LINQ-to-Objects を使ってさらに処理できるクライアントに明示的にデータを戻します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-230">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a><span data-ttu-id="a36d7-231">EF Core 3.0 では .NET Standard 2.0 ではなく .NET Standard 2.1 がターゲットにされる</span><span class="sxs-lookup"><span data-stu-id="a36d7-231">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>

[<span data-ttu-id="a36d7-232">問題 #15498 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-232">Tracking Issue #15498</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

<span data-ttu-id="a36d7-233">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-233">**Old behavior**</span></span>

<span data-ttu-id="a36d7-234">3\.0 以前の EF Core の場合は、.NET Standard 2.0 がターゲットとされ、その標準をサポートするすべてのプラットフォーム (.NET Framework を含む) 上で実行されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-234">Before 3.0, EF Core targeted .NET Standard 2.0 and would run on all platforms that support that standard, including .NET Framework.</span></span>

<span data-ttu-id="a36d7-235">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-235">**New behavior**</span></span>

<span data-ttu-id="a36d7-236">3\.0 以降の EF Core の場合は、.NET Standard 2.1 がターゲットとされ、この標準をサポートするすべてのプラットフォームで実行されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-236">Starting with 3.0, EF Core targets .NET Standard 2.1 and will run on all platforms that support this standard.</span></span> <span data-ttu-id="a36d7-237">これには、.NET Framework は含まれません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-237">This does not include .NET Framework.</span></span>

<span data-ttu-id="a36d7-238">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-238">**Why**</span></span>

<span data-ttu-id="a36d7-239">これは、.NET Core やその他の最新の .NET プラットフォーム (Xamarin など) にエネルギーを集中するために .NET テクノロジ全体にわたって行われる戦略的な決定の一部です。</span><span class="sxs-lookup"><span data-stu-id="a36d7-239">This is part of a strategic decision across .NET technologies to focus energy on .NET Core and other modern .NET platforms, such as Xamarin.</span></span>

<span data-ttu-id="a36d7-240">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-240">**Mitigations**</span></span>

<span data-ttu-id="a36d7-241">最新の .NET プラットフォームへの移行を検討してください。</span><span class="sxs-lookup"><span data-stu-id="a36d7-241">Consider moving to a modern .NET platform.</span></span> <span data-ttu-id="a36d7-242">これが不可能な場合は、EF Core 2.1 または EF Core 2.2 を引き続き使用します。どちらも .NET Framework をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="a36d7-242">If this is not possible, then continue to use EF Core 2.1 or EF Core 2.2, both of which support .NET Framework.</span></span>

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="a36d7-243">Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-243">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="a36d7-244">問題 Announcements#325 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-244">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="a36d7-245">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-245">**Old behavior**</span></span>

<span data-ttu-id="a36d7-246">ASP.NET Core 3.0 より前では、パッケージ参照を `Microsoft.AspNetCore.App` または `Microsoft.AspNetCore.All` に追加したときに、EF Core と、SQL Server プロバイダーのような EF Core データ プロバイダーの一部が含まれました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-246">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="a36d7-247">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-247">**New behavior**</span></span>

<span data-ttu-id="a36d7-248">3\.0 以降では、ASP.NET Core 共有フレームワークには、EF Core も、いずれの EF Core データ プロバイダーも含まれません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-248">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="a36d7-249">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-249">**Why**</span></span>

<span data-ttu-id="a36d7-250">この変更の前では、アプリケーションのターゲットが ASP.NET Core であるか SQL Server であるかに応じて、EF Core を取得するには異なる手順が必要でした。</span><span class="sxs-lookup"><span data-stu-id="a36d7-250">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="a36d7-251">また、ASP.NET Core のアップグレードでは、必ずしも適切だとは言えない、EF Core と SQL Server プロバイダーのアップグレードが強制されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-251">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="a36d7-252">この変更では、EF Core の取得のエクスペリエンスは、.NET の実装とアプリケーションの種類がサポートされる、すべてのプロバイダーで同じになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-252">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="a36d7-253">また、開発者は、EF Core と EF Core データ プロバイダーのアップグレードのタイミングを正確に制御できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-253">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="a36d7-254">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-254">**Mitigations**</span></span>

<span data-ttu-id="a36d7-255">ASP.NET Core 3.0 アプリケーションまたはその他のサポートされるアプリケーションで EF Core を使用するには、アプリケーションで使用される EF Core データベース プロバイダーにパッケージ参照を明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-255">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="a36d7-256">EF Core のコマンドライン ツールである dotnet ef が .NET Core SDK の一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-256">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="a36d7-257">問題 #14016 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-257">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="a36d7-258">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-258">**Old behavior**</span></span>

<span data-ttu-id="a36d7-259">3\.0 より前は `dotnet ef` ツールが .NET Core SDK に含まれており、追加の手順を必要とせずに、任意のプロジェクトのコマンド ラインから簡単に使用できました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-259">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="a36d7-260">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-260">**New behavior**</span></span>

<span data-ttu-id="a36d7-261">3\.0 以降は .NET SDK に `dotnet ef` ツールが含まれないため、これを使用するにはローカルまたはグローバルなツールとして明示的にインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-261">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="a36d7-262">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-262">**Why**</span></span>

<span data-ttu-id="a36d7-263">この変更により、`dotnet ef` を通常の .NET CLI ツールとして NuGet 上で配信したり更新したりできるようになります。これは EF Core 3.0 も常に NuGet パッケージとして配信されるという事実と一致します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-263">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="a36d7-264">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-264">**Mitigations**</span></span>

<span data-ttu-id="a36d7-265">移行の管理や `DbContext` のスキャフォールディングを行えるようにするには、`dotnet-ef` をグローバル ツールとしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="a36d7-265">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

  ``` console
    $ dotnet tool install --global dotnet-ef
  ```

<span data-ttu-id="a36d7-266">[ツール マニフェスト ファイル](https://github.com/dotnet/cli/issues/10288)を使用してツールの依存関係として宣言するプロジェクトの依存関係を復元するときに、ローカルなツールとして取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-266">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](https://github.com/dotnet/cli/issues/10288).</span></span>

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="a36d7-267">FromSql、ExecuteSql、および ExecuteSqlAsync の名前変更</span><span class="sxs-lookup"><span data-stu-id="a36d7-267">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="a36d7-268">問題 #10996 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-268">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="a36d7-269">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-269">**Old behavior**</span></span>

<span data-ttu-id="a36d7-270">EF Core 3.0 より前のバージョンでは、通常の文字列または SQL およびパラメーターに挿入する必要がある文字列で使用するために、これらのメソッド名がオーバーロードされました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-270">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="a36d7-271">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-271">**New behavior**</span></span>

<span data-ttu-id="a36d7-272">EF Core 3.0 以降では、`FromSqlRaw`、`ExecuteSqlRaw`、および `ExecuteSqlRawAsync` を使用して、パラメーターがクエリ文字列とは別に渡される、パラメーター化クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-272">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="a36d7-273">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-273">For example:</span></span>

```C#
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="a36d7-274">`FromSqlInterpolated`、`ExecuteSqlInterpolated`、および `ExecuteSqlInterpolatedAsync` を使用して、パラメーターが挿入クエリ文字列の一部として渡されるパラメーター化クエリを作成します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-274">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="a36d7-275">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-275">For example:</span></span>

```C#
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="a36d7-276">上記のクエリはどちらも、同じ SQL パラメーターを持つ同じパラメーター化 SQL が生成されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a36d7-276">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="a36d7-277">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-277">**Why**</span></span>

<span data-ttu-id="a36d7-278">このようなメソッド オーバーロードは、挿入文字列メソッドを呼び出すつもりが、誤って raw 文字列メソッドを非常に簡単に呼び出せてしまいます。その逆も同様です。</span><span class="sxs-lookup"><span data-stu-id="a36d7-278">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="a36d7-279">これは、クエリをパラメーター化する必要があるときに、パラメーター化されない結果になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-279">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="a36d7-280">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-280">**Mitigations**</span></span>

<span data-ttu-id="a36d7-281">新しいメソッド名を使用するように切り替えます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-281">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="a36d7-282">FromSql メソッドをストアド プロシージャと共に使用して構成することができない</span><span class="sxs-lookup"><span data-stu-id="a36d7-282">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="a36d7-283">問題 #15392 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-283">Tracking Issue #15392</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

<span data-ttu-id="a36d7-284">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-284">**Old behavior**</span></span>

<span data-ttu-id="a36d7-285">3\.0 EF Core より前では、FromSql メソッドにより、渡された SQL を構成できるかどうかの検出が試行されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-285">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="a36d7-286">SQL がストアド プロシージャのように非コンポーザブルである場合、クライアント評価が行われていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-286">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="a36d7-287">次のクエリは、サーバー上でストアド プロシージャを実行し、クライアント側で FirstOrDefault を実行することで機能していました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-287">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```C#
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

<span data-ttu-id="a36d7-288">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-288">**New behavior**</span></span>

<span data-ttu-id="a36d7-289">EF Core 3.0 以降では、SQL の解析は試行されません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-289">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="a36d7-290">そのため、FromSqlRaw/FromSqlInterpolated の後に構成する場合、EF Core では、サブ クエリを実行することで SQL が構成されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-290">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="a36d7-291">したがって、構成でストアド プロシージャを使用している場合は、無効な SQL 構文の例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-291">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

<span data-ttu-id="a36d7-292">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-292">**Why**</span></span>

<span data-ttu-id="a36d7-293">EF Core 3.0 では、自動クライアント評価はサポートされていません。これは、[ここ](#linq-queries-are-no-longer-evaluated-on-the-client)で説明するように、エラーが発生しやすいためです。</span><span class="sxs-lookup"><span data-stu-id="a36d7-293">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

<span data-ttu-id="a36d7-294">**対応策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-294">**Mitigation**</span></span>

<span data-ttu-id="a36d7-295">FromSqlRaw/FromSqlInterpolated でストアド プロシージャを使用している場合は、それを構成できないことがわかっているので、サーバー側での構成を回避するために、FromSql メソッド呼び出しの直後に __AsEnumerable/AsAsyncEnumerable__ を追加します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-295">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```C#
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="a36d7-296">FromSql メソッドはクエリのルートでのみ指定できる</span><span class="sxs-lookup"><span data-stu-id="a36d7-296">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="a36d7-297">問題 #15704 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-297">Tracking Issue #15704</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

<span data-ttu-id="a36d7-298">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-298">**Old behavior**</span></span>

<span data-ttu-id="a36d7-299">EF Core 3.0 以前は、クエリ内の任意の場所で `FromSql` メソッドを指定できました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-299">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="a36d7-300">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-300">**New behavior**</span></span>

<span data-ttu-id="a36d7-301">EF Core 3.0 以降、新しい `FromSqlRaw` および `FromSqlInterpolated` メソッド (`FromSql` の置き換え) は、クエリのルート上でのみ (つまり `DbSet<>` で直接) 指定できます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-301">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="a36d7-302">他の場所でそれらを指定しようとすると、コンパイル エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-302">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="a36d7-303">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-303">**Why**</span></span>

<span data-ttu-id="a36d7-304">`DbSet` 以外の任意の場所で `FromSql` を指定しても、さらなる意味や価値が追加されることはなく、特定のシナリオではあいまいさの原因となる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-304">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="a36d7-305">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-305">**Mitigations**</span></span>

<span data-ttu-id="a36d7-306">`FromSql` の呼び出し場所を移動して、それらが適用される `DbSet` 上で直接実行されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-306">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="a36d7-307">追跡なしのクエリでは、識別子の解決が実行されなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-307">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="a36d7-308">問題 #13518 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-308">Tracking Issue #13518</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

<span data-ttu-id="a36d7-309">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-309">**Old behavior**</span></span>

<span data-ttu-id="a36d7-310">EF Core 3.0 以前の場合は、指定した型と ID を持つエンティティが出現するたびに同じエンティティ インスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-310">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="a36d7-311">これは、追跡クエリの動作と一致します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-311">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="a36d7-312">次のクエリを例にします。</span><span class="sxs-lookup"><span data-stu-id="a36d7-312">For example, this query:</span></span>

```C#
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
<span data-ttu-id="a36d7-313">指定したカテゴリに関連付けられている各 `Category` に対して同じ `Product` インスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-313">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

<span data-ttu-id="a36d7-314">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-314">**New behavior**</span></span>

<span data-ttu-id="a36d7-315">EF Core 3.0 以降では、指定した型と ID を持つエンティティが、返されたグラフ内の異なる場所で検出されると、それぞれ異なるエンティティ インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-315">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="a36d7-316">たとえば、上記のクエリでは、2 つの製品が同じカテゴリに関連付けられている場合でも、各 `Product` に対して新しい `Category` インスタンスが返されるようになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-316">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

<span data-ttu-id="a36d7-317">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-317">**Why**</span></span>

<span data-ttu-id="a36d7-318">識別子の解決 (つまり、エンティティの型と ID が以前に検出されたエンティティと同じであることを確認する) を行う場合は、パフォーマンスおよびメモリのオーバーヘッドが増大します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-318">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="a36d7-319">これは、通常、最初の場所で追跡なしのクエリが使用されている理由に矛盾しています。</span><span class="sxs-lookup"><span data-stu-id="a36d7-319">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="a36d7-320">また、識別子の解決は有用なこともありますが、エンティティをシリアル化してクライアントに送信する場合 (追跡なしのクエリでは一般的な処理) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-320">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

<span data-ttu-id="a36d7-321">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-321">**Mitigations**</span></span>

<span data-ttu-id="a36d7-322">識別子の解決が必要な場合は、追跡クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-322">Use a tracking query if identity resolution is required.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="a36d7-323">~~クエリの実行がデバッグ レベルでログに記録される~~ 元に戻されます</span><span class="sxs-lookup"><span data-stu-id="a36d7-323">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="a36d7-324">問題 #14523 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-324">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="a36d7-325">EF Core 3.0 の新しい構成では、あらゆるイベントのログ レベルをアプリケーションによって指定できるため、この変更は元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-325">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="a36d7-326">たとえば、SQL のログ記録を `Debug` に切り替えるには、`OnConfiguring` または `AddDbContext` で明示的にレベルを構成します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-326">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="a36d7-327">一時キーの値がエンティティ インスタンスに設定されなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-327">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="a36d7-328">問題 #12378 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-328">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="a36d7-329">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-329">**Old behavior**</span></span>

<span data-ttu-id="a36d7-330">EF Core 3.0 より前では、後で実際の値がデータベースによって生成される、すべてのキー プロパティに一時的な値が割り当てられていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-330">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="a36d7-331">通常、これらの一時的な値は大きい負の数値でした。</span><span class="sxs-lookup"><span data-stu-id="a36d7-331">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="a36d7-332">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-332">**New behavior**</span></span>

<span data-ttu-id="a36d7-333">3\.0 以降では、EF Core で、エンティティの追跡情報の一部として一時キーの値が格納され、キー プロパティ自体はそのままになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-333">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="a36d7-334">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-334">**Why**</span></span>

<span data-ttu-id="a36d7-335">この変更は、何らかの `DbContext` インスタンスによって以前に追跡されたエンティティが、別の `DbContext` インスタンスに移動されるときに、一時キーの値が誤って永続的なものにならないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-335">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="a36d7-336">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-336">**Mitigations**</span></span>

<span data-ttu-id="a36d7-337">主キーの値を外部キーに割り当てて、エンティティ間の関連付けを形成するアプリケーションは、主キーがストアで生成されており、`Added` 状態のエンティティに属している場合、以前の動作に依存する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-337">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="a36d7-338">これは、次のようにして回避できます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-338">This can be avoided by:</span></span>
* <span data-ttu-id="a36d7-339">ストア生成キーを使用しない。</span><span class="sxs-lookup"><span data-stu-id="a36d7-339">Not using store-generated keys.</span></span>
* <span data-ttu-id="a36d7-340">外部キーの値を設定するのではなく、ナビゲーション プロパティを設定してリレーションシップを形成する。</span><span class="sxs-lookup"><span data-stu-id="a36d7-340">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="a36d7-341">エンティティの追跡情報から実際の一時キーの値を取得する。</span><span class="sxs-lookup"><span data-stu-id="a36d7-341">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="a36d7-342">たとえば、`context.Entry(blog).Property(e => e.Id).CurrentValue` では、`blog.Id` 自体が設定されていない場合でも、一時的な値が返されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-342">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="a36d7-343">DetectChanges でストア生成キーの値が優先される</span><span class="sxs-lookup"><span data-stu-id="a36d7-343">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="a36d7-344">問題 #14616 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-344">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="a36d7-345">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-345">**Old behavior**</span></span>

<span data-ttu-id="a36d7-346">EF Core 3.0 より前では、`DetectChanges` によって検出された追跡対象外のエンティティが `Added` 状態で追跡され、`SaveChanges` が呼び出されたときに新しい行として挿入されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-346">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="a36d7-347">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-347">**New behavior**</span></span>

<span data-ttu-id="a36d7-348">EF Core 3.0 以降では、エンティティで生成されたキーの値を使用し、何らかのキーの値が設定されている場合、そのエンティティは `Modified` 状態で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-348">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="a36d7-349">つまり、エンティティの行が存在していると見なされ、`SaveChanges` の呼び出し時に更新されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-349">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="a36d7-350">キーの値が設定されていないか、エンティティ型で生成されたキーが使用されない場合、新しいエンティティは引き続き、以前のバージョンと同様に `Added` として追跡されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-350">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="a36d7-351">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-351">**Why**</span></span>

<span data-ttu-id="a36d7-352">この変更は、ストア生成キーの使用中に、切り離されたエンティティ グラフをより簡単に一貫して操作できるようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-352">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="a36d7-353">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-353">**Mitigations**</span></span>

<span data-ttu-id="a36d7-354">エンティティ型が生成されたキーを使用するように構成されているものの、キーの値が新しいインスタンスに対して明示的に設定されている場合、この変更によってアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-354">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="a36d7-355">この問題を解決するには、キー プロパティで生成された値が使用されないように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-355">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="a36d7-356">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-356">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="a36d7-357">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-357">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="a36d7-358">既定で連鎖削除がすぐに行われるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-358">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="a36d7-359">問題 #10114 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-359">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="a36d7-360">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-360">**Old behavior**</span></span>

<span data-ttu-id="a36d7-361">3\.0 より前では、EF Core で適用された連鎖操作 (必要なプリンシパルが削除されたか、必要なプリンシパルへのリレーションシップが切断されたときに依存エンティティを削除する) は、SaveChanges が呼び出されるまで行われませんでした。</span><span class="sxs-lookup"><span data-stu-id="a36d7-361">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="a36d7-362">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-362">**New behavior**</span></span>

<span data-ttu-id="a36d7-363">3\.0 以降では、EF Core で、トリガー条件が検出されるとすぐに連鎖操作が適用されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-363">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="a36d7-364">たとえば、プリンシパル エンティティを削除するために `context.Remove()` を呼び出すと、すべての追跡対象の関連する必要な依存関係もすぐに `Deleted` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-364">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="a36d7-365">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-365">**Why**</span></span>

<span data-ttu-id="a36d7-366">この変更は、`SaveChanges` が呼び出される_前に_どのエンティティが削除されるかを把握することが重要である、データ バインディングおよび監査シナリオのエクスペリエンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-366">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="a36d7-367">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-367">**Mitigations**</span></span>

<span data-ttu-id="a36d7-368">`context.ChangedTracker` の設定を使用して、以前の動作を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-368">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="a36d7-369">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-369">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="a36d7-370">関連エンティティの一括読み込みが 1 つのクエリで行われるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-370">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="a36d7-371">問題 #18022 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-371">Tracking issue #18022</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

<span data-ttu-id="a36d7-372">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-372">**Old behavior**</span></span>

<span data-ttu-id="a36d7-373">3\.0 より前では、`Include` 演算子を使用してコレクション ナビゲーションを一括で読み込むと、関連するエンティティ型ごとに 1 つずつ、リレーショナル データベースで複数のクエリが生成されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-373">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

<span data-ttu-id="a36d7-374">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-374">**New behavior**</span></span>

<span data-ttu-id="a36d7-375">3\.0 以降の EF Core では、リレーショナル データベースで結合を使用した単一のクエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-375">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

<span data-ttu-id="a36d7-376">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-376">**Why**</span></span>

<span data-ttu-id="a36d7-377">単一の LINQ クエリを実装するために複数のクエリを実行すると、多くの問題が発生していました。これらの問題には、複数のデータベース ラウンドトリップが必要になった際のパフォーマンスの低下や、各クエリによってデータベースの異なる状態が観察された際のデータの一貫性の問題などが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-377">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

<span data-ttu-id="a36d7-378">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-378">**Mitigations**</span></span>

<span data-ttu-id="a36d7-379">技術的に、これは破壊的変更ではありませんが、コレクション ナビゲーションで単一のクエリに多数の `Include` 演算子が含まれている場合、アプリケーションのパフォーマンスに大きな影響を及ぼす可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-379">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="a36d7-380">詳細情報、およびより効率的な方法でクエリを再記述する方法については、[こちらのコメントを参照してください](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085)。</span><span class="sxs-lookup"><span data-stu-id="a36d7-380">[See this comment](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="a36d7-381">DeleteBehavior.Restrict のセマンティクスがクリーンになりました</span><span class="sxs-lookup"><span data-stu-id="a36d7-381">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="a36d7-382">問題 #12661 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-382">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="a36d7-383">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-383">**Old behavior**</span></span>

<span data-ttu-id="a36d7-384">3\.0 以前は、`DeleteBehavior.Restrict` により `Restrict` セマンティクスでデータベースに外部キーが作成されましたが、内部の修正がどのように変更されたのかがはっきりしませんでした。</span><span class="sxs-lookup"><span data-stu-id="a36d7-384">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="a36d7-385">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-385">**New behavior**</span></span>

<span data-ttu-id="a36d7-386">3\.0 以降では、`DeleteBehavior.Restrict` により `Restrict` セマンティクスで外部キーが作成されます。つまり、カスケードがありません。EF 内部修正には影響を出さず、制約違反で例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-386">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="a36d7-387">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-387">**Why**</span></span>

<span data-ttu-id="a36d7-388">この変更は、副作用を出さず、直観的に `DeleteBehavior` を使用するために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-388">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="a36d7-389">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-389">**Mitigations**</span></span>

<span data-ttu-id="a36d7-390">`DeleteBehavior.ClientNoAction` を使用し、以前の動作を復元できます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-390">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="a36d7-391">クエリ型がエンティティ型と統合される</span><span class="sxs-lookup"><span data-stu-id="a36d7-391">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="a36d7-392">問題 #14194 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-392">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="a36d7-393">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-393">**Old behavior**</span></span>

<span data-ttu-id="a36d7-394">EF Core 3.0 より前では、[クエリ型](xref:core/modeling/keyless-entity-types)は、構造化された方法で主キーが定義されないデータのクエリを実行するための手段でした。</span><span class="sxs-lookup"><span data-stu-id="a36d7-394">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="a36d7-395">つまり、クエリ型は、キーが利用できるときに (テーブルからの可能性が高くなりますが、ビューからの可能性もあります) 通常のエンティティ型が使用された場合に、キーなしの (ビューからの可能性が高くなりますが、テーブルからの可能性もあります) エンティティ型をマップするために使用されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-395">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="a36d7-396">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-396">**New behavior**</span></span>

<span data-ttu-id="a36d7-397">クエリ型は、現在、主キーなしの単なるエンティティ型になります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-397">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="a36d7-398">キーなしのエンティティ型の機能は、以前のバージョンのクエリ型と同じです。</span><span class="sxs-lookup"><span data-stu-id="a36d7-398">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="a36d7-399">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-399">**Why**</span></span>

<span data-ttu-id="a36d7-400">この変更は、クエリ型の目的に関する混乱を減らすために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-400">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="a36d7-401">つまり、これらはキーなしのエンティティ型であるため、本質的には読み取り専用となりますが、単にエンティティ型を読み取り専用にする必要があるという理由で使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="a36d7-401">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="a36d7-402">同様に、これらは多くの場合、ビューにマップされますが、これは、単にビューでは多くの場合、キーが定義されないためです。</span><span class="sxs-lookup"><span data-stu-id="a36d7-402">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="a36d7-403">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-403">**Mitigations**</span></span>

<span data-ttu-id="a36d7-404">API の以下の部分は使用されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-404">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="a36d7-405">**`ModelBuilder.Query<>()`** - 代わりに `ModelBuilder.Entity<>().HasNoKey()` を呼び出して、エンティティ型をキーなしとしてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-405">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="a36d7-406">これは、規則により、主キーは必要であるが、規則と一致しない場合に構成エラーを回避するようにはまだ構成されません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-406">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="a36d7-407">**`DbQuery<>`** - 代わりに `DbSet<>` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-407">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="a36d7-408">**`DbContext.Query<>()`** - 代わりに `DbContext.Set<>()` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-408">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="a36d7-409">所有型のリレーションシップ用の構成 API が変更された</span><span class="sxs-lookup"><span data-stu-id="a36d7-409">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="a36d7-410">[問題 #12444 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[問題 #9148 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[問題 #14153 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="a36d7-410">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="a36d7-411">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-411">**Old behavior**</span></span>

<span data-ttu-id="a36d7-412">EF Core 3.0 より前では、所有リレーションシップの構成は、`OwnsOne` または `OwnsMany` の呼び出し直後に実行されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-412">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="a36d7-413">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-413">**New behavior**</span></span>

<span data-ttu-id="a36d7-414">EF Core 3.0 以降では、fluent API で、`WithOwner()` を使用して、所有者に対してナビゲーション プロパティが構成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-414">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="a36d7-415">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-415">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="a36d7-416">現在、所有者と所有型間のリレーションシップに関する構成は、他のリレーションシップの構成方法と同様、`WithOwner()` 後にチェーンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-416">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="a36d7-417">一方、所有型自体の構成は引き続き、`OwnsOne()/OwnsMany()` 後にチェーンされます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-417">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="a36d7-418">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-418">For example:</span></span>

```C#
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

<span data-ttu-id="a36d7-419">さらに所有型ターゲットでの `Entity()`、`HasOne()`、または `Set()` の呼び出しでは、例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-419">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="a36d7-420">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-420">**Why**</span></span>

<span data-ttu-id="a36d7-421">この変更は、所有型自体と、所有型_へのリレーションシップ_の構成を明確に分離するために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-421">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="a36d7-422">これにより、`HasForeignKey` のようなメソッドに関するあいまいさと混乱はなくなります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-422">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="a36d7-423">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-423">**Mitigations**</span></span>

<span data-ttu-id="a36d7-424">上記の例で示すように、新しい API サーフェスを使用するように、所有型リレーションシップの構成を変更します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-424">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="a36d7-425">プリンシパルとテーブルを共有する依存エンティティが省略可能になりました</span><span class="sxs-lookup"><span data-stu-id="a36d7-425">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="a36d7-426">問題 #9005 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-426">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="a36d7-427">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-427">**Old behavior**</span></span>

<span data-ttu-id="a36d7-428">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="a36d7-428">Consider the following model:</span></span>
```C#
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
<span data-ttu-id="a36d7-429">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、新しい `Order` を追加する場合には常に `OrderDetails` インスタンスが必要でした。</span><span class="sxs-lookup"><span data-stu-id="a36d7-429">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="a36d7-430">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-430">**New behavior**</span></span>

<span data-ttu-id="a36d7-431">EF Core 3.0 以降では、`OrderDetails` なしで `Order` を追加することができ、主キー以外のすべての `OrderDetails` プロパティが NULL 値が許可される列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-431">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="a36d7-432">`OrderDetails` は、必要なプロパティのいずれにも値がない場合、または主キー以外に必要なプロパティがなく、すべてのプロパティが `null` の場合、EF Core のクエリの実行時に `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-432">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="a36d7-433">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-433">**Mitigations**</span></span>

<span data-ttu-id="a36d7-434">モデルに省略可能なすべての列と依存関係を共有するテーブルがあるが、それを指すナビゲーションが `null` になることが想定されていない場合は、ナビゲーションが `null` の場合のケースを処理するようにアプリケーションを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-434">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="a36d7-435">これができない場合は、必要なプロパティをエンティティ型に追加するか、少なくとも 1 つのプロパティに割り当てられている値が `null` 以外である必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-435">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="a36d7-436">同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-436">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="a36d7-437">問題 #14154 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-437">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="a36d7-438">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-438">**Old behavior**</span></span>

<span data-ttu-id="a36d7-439">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="a36d7-439">Consider the following model:</span></span>
```C#
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
<span data-ttu-id="a36d7-440">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、`OrderDetails` だけを更新してもクライアント上の `Version` 値は更新されず、次回の更新が失敗します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-440">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="a36d7-441">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-441">**New behavior**</span></span>

<span data-ttu-id="a36d7-442">EF Core 3.0 以降では、新しい `Version` 値が `OrderDetails` を所有している場合には `Order` に伝達されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-442">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="a36d7-443">それ以外の場合は、モデルの検証中に例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-443">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="a36d7-444">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-444">**Why**</span></span>

<span data-ttu-id="a36d7-445">この変更は、エンティティの 1 つだけが同じテーブルにマップされている場合に、古い同時実行トークン値が更新されるのを回避するために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-445">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="a36d7-446">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-446">**Mitigations**</span></span>

<span data-ttu-id="a36d7-447">テーブルを共有するすべてのエンティティには、同時実行トークン列にマップされるプロパティを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-447">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="a36d7-448">シャドウ状態で作成することができます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-448">It's possible the create one in shadow-state:</span></span>
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="a36d7-449">マップされていない型から継承されたプロパティは、すべての派生型の 1 つの列にマップされるようになりました</span><span class="sxs-lookup"><span data-stu-id="a36d7-449">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="a36d7-450">問題 #13998 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-450">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="a36d7-451">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-451">**Old behavior**</span></span>

<span data-ttu-id="a36d7-452">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="a36d7-452">Consider the following model:</span></span>
```C#
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

<span data-ttu-id="a36d7-453">EF Core 3.0 より前のバージョンでは、`ShippingAddress` プロパティは既定で `BulkOrder` と `Order` の個別の列にマップされていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-453">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="a36d7-454">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-454">**New behavior**</span></span>

<span data-ttu-id="a36d7-455">EF Core 3.0 以降では、`ShippingAddress` に対して 1 つの列だけが作成されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-455">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="a36d7-456">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-456">**Why**</span></span>

<span data-ttu-id="a36d7-457">以前の動作は予期しないものでした。</span><span class="sxs-lookup"><span data-stu-id="a36d7-457">The old behavoir was unexpected.</span></span>

<span data-ttu-id="a36d7-458">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-458">**Mitigations**</span></span>

<span data-ttu-id="a36d7-459">プロパティは、派生型の個別の列に引き続き明示的にマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-459">The property can still be explicitly mapped to separate column on the derived types:</span></span>

```C#
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

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="a36d7-460">外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-460">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="a36d7-461">問題 #13274 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-461">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="a36d7-462">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-462">**Old behavior**</span></span>

<span data-ttu-id="a36d7-463">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="a36d7-463">Consider the following model:</span></span>
```C#
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
<span data-ttu-id="a36d7-464">EF Core 3.0 より前では、規則により、外部キーで `CustomerId` プロパティが使用されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-464">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="a36d7-465">しかし、`Order` が所有型である場合、`CustomerId` も主キーとマークされ、これは通常、期待されることではありません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-465">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="a36d7-466">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-466">**New behavior**</span></span>

<span data-ttu-id="a36d7-467">EF Core 3.0 以降では、プリンシパル プロパティと同じ名前である場合、規則により、外部キーに対するプロパティの使用は試行されません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-467">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="a36d7-468">プリンシパル プロパティ名と連結されたプリンシパル型名、およびプリンシパル プロパティ名パターンと連結されたナビゲーション名は、引き続き一致します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-468">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="a36d7-469">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-469">For example:</span></span>

```C#
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

```C#
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

<span data-ttu-id="a36d7-470">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-470">**Why**</span></span>

<span data-ttu-id="a36d7-471">この変更は、所有型に主キー プロパティが誤って定義されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-471">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="a36d7-472">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-472">**Mitigations**</span></span>

<span data-ttu-id="a36d7-473">プロパティを外部キーにする、したがって、主キーの一部にする予定だった場合は、そのように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-473">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="a36d7-474">データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになりました</span><span class="sxs-lookup"><span data-stu-id="a36d7-474">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="a36d7-475">問題 #14218 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-475">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="a36d7-476">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-476">**Old behavior**</span></span>

<span data-ttu-id="a36d7-477">EF Core 3.0 より前のバージョンでは、コンテキストにより `TransactionScope` 内部で接続が開かれると、現在の `TransactionScope` がアクティブの間、接続が開いたままになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-477">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

```C#
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

<span data-ttu-id="a36d7-478">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-478">**New behavior**</span></span>

<span data-ttu-id="a36d7-479">EF Core 3.0 以降では、使用が終了したらすぐに接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-479">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="a36d7-480">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-480">**Why**</span></span>

<span data-ttu-id="a36d7-481">この変更により、同じ `TransactionScope` で複数のコンテキストを使用できます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-481">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="a36d7-482">新しい動作は、EF6 にも一致します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-482">The new behavior also matches EF6.</span></span>

<span data-ttu-id="a36d7-483">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-483">**Mitigations**</span></span>

<span data-ttu-id="a36d7-484">接続を開いたままにする必要がある場合は、`OpenConnection()` を明示的に呼び出して、EF Core が途中で閉じないようにします。</span><span class="sxs-lookup"><span data-stu-id="a36d7-484">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

```C#
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

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="a36d7-485">各プロパティで独立したメモリ内整数キー生成が使用される</span><span class="sxs-lookup"><span data-stu-id="a36d7-485">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="a36d7-486">問題 #6872 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-486">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="a36d7-487">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-487">**Old behavior**</span></span>

<span data-ttu-id="a36d7-488">EF Core 3.0 より前では、1 つの共有値ジェネレーターが、すべてのメモリ内整数キー プロパティで使用されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-488">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="a36d7-489">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-489">**New behavior**</span></span>

<span data-ttu-id="a36d7-490">EF Core 3.0 以降では、各整数キー プロパティで、メモリ内データベースを使用するときに独自の値ジェネレーターが取得されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-490">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="a36d7-491">また、データベースが削除された場合は、すべてのテーブルに対してキーの生成がリセットされます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-491">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="a36d7-492">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-492">**Why**</span></span>

<span data-ttu-id="a36d7-493">この変更は、実際のデータベース キー生成により近くなるようにメモリ内キー生成を調整するため、また、メモリ内データベースを使用するときに互いのテストを分離させる機能を向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-493">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="a36d7-494">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-494">**Mitigations**</span></span>

<span data-ttu-id="a36d7-495">これにより、設定される特定のメモリ内キー値に依存しているアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-495">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="a36d7-496">代わりに特定のキーの値に依存しないようにするか、あるいは新しい動作と一致するように更新することを検討します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-496">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="a36d7-497">バッキング フィールドが既定で使用される</span><span class="sxs-lookup"><span data-stu-id="a36d7-497">Backing fields are used by default</span></span>

[<span data-ttu-id="a36d7-498">問題 #12430 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-498">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="a36d7-499">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-499">**Old behavior**</span></span>

<span data-ttu-id="a36d7-500">3\.0 より前では、プロパティのバッキング フィールドがわかっていた場合でも、EF Core では引き続き、既定でプロパティの getter および setter メソッドを使用して、プロパティ値の読み取りと書き込みが行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-500">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="a36d7-501">ただし、バッキング フィールドが直接設定されることがわかっている場合のクエリの実行は例外です。</span><span class="sxs-lookup"><span data-stu-id="a36d7-501">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="a36d7-502">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-502">**New behavior**</span></span>

<span data-ttu-id="a36d7-503">EF Core 3.0 以降では、プロパティのバッキング フィールドがわかっている場合、バッキング フィールドを使用して、常に EF Core がそのプロパティの読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="a36d7-503">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="a36d7-504">アプリケーションが getter および setter メソッドにコード化された追加動作に依存している場合、これによりアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-504">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="a36d7-505">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-505">**Why**</span></span>

<span data-ttu-id="a36d7-506">この変更は、EF Core が、エンティティに関するデータベース操作の実行時に、既定でビジネス ロジックを誤ってトリガーしないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-506">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="a36d7-507">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-507">**Mitigations**</span></span>

<span data-ttu-id="a36d7-508">3\.0 より前の動作は、`ModelBuilder` のプロパティ アクセス モードの構成を通じて復元できます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-508">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="a36d7-509">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-509">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="a36d7-510">複数の互換性があるバッキング フィールドが見つかった場合にスローされる</span><span class="sxs-lookup"><span data-stu-id="a36d7-510">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="a36d7-511">問題 #12523 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-511">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="a36d7-512">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-512">**Old behavior**</span></span>

<span data-ttu-id="a36d7-513">EF Core 3.0 より前では、複数のフィールドが、プロパティのバッキング フィールドを見つけるための規則と一致した場合、何らかの優先順位に基づいて、1 つのフィールドが選択されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-513">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="a36d7-514">これにより、あいまいなケースで不適切なフィールドが使用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-514">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="a36d7-515">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-515">**New behavior**</span></span>

<span data-ttu-id="a36d7-516">EF Core 3.0 以降では、複数のフィールドが同じプロパティと一致した場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-516">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="a36d7-517">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-517">**Why**</span></span>

<span data-ttu-id="a36d7-518">この変更は、1 つのフィールドのみが適切である可能性がある場合に、他のものより優先して暗黙的に 1 つのフィールドが使用されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-518">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="a36d7-519">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-519">**Mitigations**</span></span>

<span data-ttu-id="a36d7-520">あいまいなバッキング フィールドがあるプロパティでは、使用するフィールドを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-520">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="a36d7-521">たとえば、fluent API を使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-521">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="a36d7-522">フィールド専用プロパティの名前はフィールドの名前に一致する必要があります</span><span class="sxs-lookup"><span data-stu-id="a36d7-522">Field-only property names should match the field name</span></span>

<span data-ttu-id="a36d7-523">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-523">**Old behavior**</span></span>

<span data-ttu-id="a36d7-524">EF Core 3.0 以前では、プロパティは文字列値により指定できました。 .NET 型でその名前のプロパティが見つからなかった場合、EF Core では、一般的な規則を使ってそれとフィールドの照合が試行されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-524">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>
```C#
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```
```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

<span data-ttu-id="a36d7-525">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-525">**New behavior**</span></span>

<span data-ttu-id="a36d7-526">EF Core 3.0 以降では、フィールド専用プロパティはフィールドの名前に厳密に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-526">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="a36d7-527">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-527">**Why**</span></span>

<span data-ttu-id="a36d7-528">この変更は、同じような名前が付けられた 2 つのプロパティに同じフィールドが使用されるのを回避する目的で行われました。また、フィールド専用プロパティの照合規則を、CLR プロパティにマッピングされているプロパティの場合と同じにします。</span><span class="sxs-lookup"><span data-stu-id="a36d7-528">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="a36d7-529">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-529">**Mitigations**</span></span>

<span data-ttu-id="a36d7-530">フィールド専用プロパティには、それがマッピングされるフィールドと同じ名前を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-530">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="a36d7-531">EF Core 3.0 以降の今後のリリースでは、プロパティ名とは異なるフィールド名を明示的に構成できるように戻す予定です (問題 [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307) を参照)。</span><span class="sxs-lookup"><span data-stu-id="a36d7-531">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="a36d7-532">AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなりました</span><span class="sxs-lookup"><span data-stu-id="a36d7-532">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="a36d7-533">問題 #14756 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-533">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="a36d7-534">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-534">**Old behavior**</span></span>

<span data-ttu-id="a36d7-535">EF Core 3.0 以前では、`AddDbContext` または `AddDbContextPool` を呼び出すと、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) および [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) への呼び出しを通じて、D.I を使ってログ記録とメモリ キャッシュ サービスも登録されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-535">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with D.I through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="a36d7-536">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-536">**New behavior**</span></span>

<span data-ttu-id="a36d7-537">EF Core 3.0 以降、`AddDbContext` と `AddDbContextPool` では、依存関係の挿入 (DI) を使ってこれらのサービスを登録しなくなります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-537">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="a36d7-538">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-538">**Why**</span></span>

<span data-ttu-id="a36d7-539">EF Core 3.0 では、これらのサービスをアプリケーションの DI コンテナーに含める必要がありません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-539">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="a36d7-540">ただし、`ILoggerFactory` がアプリケーションの DI コンテナーに登録された場合、それは引き続き EF Core によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-540">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="a36d7-541">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-541">**Mitigations**</span></span>

<span data-ttu-id="a36d7-542">ご自身のアプリケーションでこれらのサービスが必要な場合は、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) または [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) を使って、DI コンテナーで明示的にそれらを登録します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-542">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="a36d7-543">DbContext.Entry でローカルの DetectChanges が実行されるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-543">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="a36d7-544">問題 #13552 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-544">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="a36d7-545">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-545">**Old behavior**</span></span>

<span data-ttu-id="a36d7-546">EF Core 3.0 より前では、`DbContext.Entry` を呼び出すと、追跡対象のすべてのエンティティで変更が検出されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-546">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="a36d7-547">これにより、`EntityEntry` で示された状態が確実に最新のものとなりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-547">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="a36d7-548">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-548">**New behavior**</span></span>

<span data-ttu-id="a36d7-549">EF Core 3.0 以降では、`DbContext.Entry` の呼び出しで、特定のエンティティと、それに関連する追跡対象のプリンシパル エンティティでの変更のみの検出が試行されるようになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-549">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="a36d7-550">つまり、他の場所での変更は、このメソッドの呼び出しで検出されなかった可能性があり、アプリケーション状態に影響する場合があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-550">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="a36d7-551">`ChangeTracker.AutoDetectChangesEnabled` が `false` に設定されている場合、このローカル変更の検出も無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a36d7-551">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="a36d7-552">`ChangeTracker.Entries` や `SaveChanges` など、変更の検出の原因となる他のメソッドは引き続き、すべての追跡対象エンティティの完全な `DetectChanges` の原因となります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-552">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="a36d7-553">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-553">**Why**</span></span>

<span data-ttu-id="a36d7-554">この変更は、`context.Entry` を使用する既定のパフォーマンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-554">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="a36d7-555">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-555">**Mitigations**</span></span>

<span data-ttu-id="a36d7-556">3\.0 より前の動作を確保するには、`Entry` を呼び出す前に明示的に `ChgangeTracker.DetectChanges()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-556">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="a36d7-557">文字列とバイト配列のキーが既定でクライアントによって生成されない</span><span class="sxs-lookup"><span data-stu-id="a36d7-557">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="a36d7-558">問題 #14617 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-558">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="a36d7-559">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-559">**Old behavior**</span></span>

<span data-ttu-id="a36d7-560">EF Core 3.0 より前では、`string` と `byte[]` のキー プロパティは、null 以外の値を明示的に設定しなくても使用できました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-560">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="a36d7-561">このような場合、キーの値は GUID としてクライアントで生成され、`byte[]` のバイトにシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-561">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="a36d7-562">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-562">**New behavior**</span></span>

<span data-ttu-id="a36d7-563">EF Core 3.0 以降では、キー値が設定されていないことを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-563">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="a36d7-564">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-564">**Why**</span></span>

<span data-ttu-id="a36d7-565">この変更は、クライアントで生成された `string`/`byte[]` 値が一般的に役に立たず、既定の動作では一般的な方法で生成されたキー値について判断するのが難しくなったため、行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-565">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="a36d7-566">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-566">**Mitigations**</span></span>

<span data-ttu-id="a36d7-567">3\.0 より前の動作は、他の null 以外の値が設定されていない場合に、キー プロパティで生成された値を使用するように明示的に指定することで取得できます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-567">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="a36d7-568">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-568">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="a36d7-569">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-569">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="a36d7-570">ILoggerFactory がスコープ サービスになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-570">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="a36d7-571">問題 #14698 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-571">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="a36d7-572">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-572">**Old behavior**</span></span>

<span data-ttu-id="a36d7-573">EF Core 3.0 より前では、`ILoggerFactory` はシングルトン サービスとして登録されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-573">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="a36d7-574">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-574">**New behavior**</span></span>

<span data-ttu-id="a36d7-575">EF Core 3.0 以降では、`ILoggerFactory` がスコープ化されたものとして登録されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-575">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="a36d7-576">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-576">**Why**</span></span>

<span data-ttu-id="a36d7-577">この変更は、`DbContext` インスタンスでのロガーの関連付けを許可し、他の機能を有効にし、内部サービス プロバイダーの急増などの異常な動作の一部のケースをなくすために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-577">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="a36d7-578">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-578">**Mitigations**</span></span>

<span data-ttu-id="a36d7-579">この変更は、EF Core の内部サービス プロバイダーでカスタム サービスを登録して使用しない限り、アプリケーション コードに影響しないはずです。</span><span class="sxs-lookup"><span data-stu-id="a36d7-579">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="a36d7-580">これは一般的なことではありません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-580">This isn't common.</span></span>
<span data-ttu-id="a36d7-581">このような場合、ほとんどのものが引き続き機能しますが、`ILoggerFactory` に依存していたシングルトン サービスは、異なる方法で `ILoggerFactory` を取得するように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-581">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="a36d7-582">このような状況になった場合は、[EF Core GitHub の問題追跡ツール](https://github.com/aspnet/EntityFrameworkCore/issues)で問題を提出し、`ILoggerFactory` の使用方法をお知らせください。これにより、Microsoft では、今後、再びこのように中断しない方法についてよりよく理解できます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-582">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="a36d7-583">遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-583">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="a36d7-584">問題 #12780 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-584">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="a36d7-585">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-585">**Old behavior**</span></span>

<span data-ttu-id="a36d7-586">EF Core 3.0 より前では、`DbContext` が破棄されると、そのコンテキストから取得されたエンティティの特定のナビゲーション プロパティが完全に読み込まれたかどうかを知る方法はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="a36d7-586">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="a36d7-587">プロキシでは、代わりに、null 以外の値がある場合は参照ナビゲーションが読み込まれ、空でない場合はコレクション ナビゲーションが読み込まれたと見なされます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-587">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="a36d7-588">このような場合、遅延読み込みを試みても操作なしとなります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-588">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="a36d7-589">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-589">**New behavior**</span></span>

<span data-ttu-id="a36d7-590">EF Core 3.0 以降では、プロキシで、ナビゲーション プロパティが読み込まれたかどうかの追跡が行われます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-590">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="a36d7-591">つまり、コンテキストが破棄された後に読み込まれたナビゲーション プロパティにアクセスしようとしても、通常は操作なしとなります。読み込まれたナビゲーションが空でも null であっても同様です。</span><span class="sxs-lookup"><span data-stu-id="a36d7-591">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="a36d7-592">逆に、コンテキストが破棄された場合、読み込まれなかったナビゲーション プロパティにアクセスしようとすると例外がスローされます。ナビゲーション プロパティが空ではないコレクションである場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="a36d7-592">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="a36d7-593">このような状況が発生した場合、アプリケーション コードで無効な時間に遅延読み込みの使用が試行されていることを意味し、アプリケーションを変更して、このようにならないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-593">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="a36d7-594">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-594">**Why**</span></span>

<span data-ttu-id="a36d7-595">この変更は、破棄された `DbContext` インスタンスで遅延読み込みが試行されたときの動作を一貫した正しいものにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-595">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="a36d7-596">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-596">**Mitigations**</span></span>

<span data-ttu-id="a36d7-597">破棄されたコンテキストで遅延読み込みが試行されないようにアプリケーション コードを更新するか、例外メッセージの説明に従って、これを操作なしとなるように構成します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-597">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="a36d7-598">内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-598">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="a36d7-599">問題 #10236 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-599">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="a36d7-600">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-600">**Old behavior**</span></span>

<span data-ttu-id="a36d7-601">EF Core 3.0 より前では、異常な数の内部サービス プロバイダーを作成するアプリケーションに対する警告がログに記録されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-601">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="a36d7-602">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-602">**New behavior**</span></span>

<span data-ttu-id="a36d7-603">EF Core 3.0 以降では、この警告はエラーと見なされるようになり、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-603">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="a36d7-604">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-604">**Why**</span></span>

<span data-ttu-id="a36d7-605">この変更は、この異常なケースをより明示的に示すことで、アプリケーション コードの改善を促進するために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-605">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="a36d7-606">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-606">**Mitigations**</span></span>

<span data-ttu-id="a36d7-607">このエラーの発生時の最も適切なアクションは、根本原因を理解し、非常に多くの内部サービス プロバイダーを作成しないようにすることです。</span><span class="sxs-lookup"><span data-stu-id="a36d7-607">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="a36d7-608">しかし、`DbContextOptionsBuilder` での構成を使用して、エラーを警告に戻す (または無視する) ことがことができます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-608">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="a36d7-609">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-609">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="a36d7-610">1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作</span><span class="sxs-lookup"><span data-stu-id="a36d7-610">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="a36d7-611">問題 #9171 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-611">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="a36d7-612">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-612">**Old behavior**</span></span>

<span data-ttu-id="a36d7-613">EF Core 3.0 以前では、1 つの文字列と共に `HasOne` または `HasMany` が呼び出されるコードは、わかりにくい方法で解釈されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-613">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="a36d7-614">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-614">For example:</span></span>
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="a36d7-615">このコードは、プライベートである可能性がある `Entrance` ナビゲーション プロパティを使って、`Samurai` を他のエンティティ型に関連付けているように見えます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-615">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="a36d7-616">実際には、このコードは、ナビゲーション プロパティなしで、`Entrance` と呼ばれるエンティティ型に対してリレーションシップを作成しようとしています。</span><span class="sxs-lookup"><span data-stu-id="a36d7-616">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="a36d7-617">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-617">**New behavior**</span></span>

<span data-ttu-id="a36d7-618">EF Core 3.0 以降では、上記のコードは、前にそれが実行すべきであるように見えたことを実行するようになりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-618">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="a36d7-619">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-619">**Why**</span></span>

<span data-ttu-id="a36d7-620">以前の動作は、特に構成コードを読み取ってエラーを探す場合は、非常にわかりにくいものでした。</span><span class="sxs-lookup"><span data-stu-id="a36d7-620">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="a36d7-621">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-621">**Mitigations**</span></span>

<span data-ttu-id="a36d7-622">これが中断させるのは、型名の文字列を使って明示的にリレーションシップを構成し、かつ明示的にナビゲーション プロパティを指定しないアプリケーションのみです。</span><span class="sxs-lookup"><span data-stu-id="a36d7-622">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="a36d7-623">これは一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-623">This is not common.</span></span>
<span data-ttu-id="a36d7-624">前の動作は、ナビゲーション プロパティ名に対して明示的に `null` を渡すことで実現できます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-624">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="a36d7-625">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-625">For example:</span></span>

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="a36d7-626">いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更されました</span><span class="sxs-lookup"><span data-stu-id="a36d7-626">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="a36d7-627">問題 #15184 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-627">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="a36d7-628">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-628">**Old behavior**</span></span>

<span data-ttu-id="a36d7-629">次の非同期メソッドでは、以前は `Task<T>` が返されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-629">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="a36d7-630">`ValueGenerator.NextValueAsync()` (および派生クラス)</span><span class="sxs-lookup"><span data-stu-id="a36d7-630">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="a36d7-631">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-631">**New behavior**</span></span>

<span data-ttu-id="a36d7-632">前述のメソッドは、以前と同じ `T` に対して `ValueTask<T>` を返すようになりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-632">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="a36d7-633">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-633">**Why**</span></span>

<span data-ttu-id="a36d7-634">この変更により、これらのメソッドを呼び出すときに発生するヒープ割り当ての数を削減して全般的なパフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-634">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="a36d7-635">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-635">**Mitigations**</span></span>

<span data-ttu-id="a36d7-636">上記の API を待機しているだけのアプリケーションのみを再コンパイルする必要があります。ソースの変更は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-636">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="a36d7-637">より複雑な使用方法 (返された `Task` を `Task.WhenAny()` に渡すなど) では通常、返された `ValueTask<T>` を `AsTask()` を呼び出すことによって `Task<T>` に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-637">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="a36d7-638">これにより、この変更による割り当ての削減が無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a36d7-638">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="a36d7-639">Relational:TypeMapping 注釈が単に TypeMapping となった</span><span class="sxs-lookup"><span data-stu-id="a36d7-639">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="a36d7-640">問題 #9913 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-640">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="a36d7-641">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-641">**Old behavior**</span></span>

<span data-ttu-id="a36d7-642">型マッピング注釈の注釈名は "Relational:TypeMapping" でした。</span><span class="sxs-lookup"><span data-stu-id="a36d7-642">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="a36d7-643">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-643">**New behavior**</span></span>

<span data-ttu-id="a36d7-644">型マッピング注釈の注釈名は、"TypeMapping" となりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-644">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="a36d7-645">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-645">**Why**</span></span>

<span data-ttu-id="a36d7-646">型マッピングが、リレーショナル データベース プロバイダー以外でも使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-646">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="a36d7-647">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-647">**Mitigations**</span></span>

<span data-ttu-id="a36d7-648">これにより、一般的ではない、注釈として直接型マッピングにアクセスするアプリケーションのみが中断されるようになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-648">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="a36d7-649">問題を解決するための最も適切なアクションは、注釈を直接使用するのではなく、API サーフェスを使用して型マッピングにアクセスすることです。</span><span class="sxs-lookup"><span data-stu-id="a36d7-649">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="a36d7-650">派生型の ToTable で例外がスローされる</span><span class="sxs-lookup"><span data-stu-id="a36d7-650">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="a36d7-651">問題 #11811 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-651">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="a36d7-652">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-652">**Old behavior**</span></span>

<span data-ttu-id="a36d7-653">EF Core 3.0 より前では、唯一の継承マッピング方法が TPH であり、これが有効でない場合には、派生型で呼び出された `ToTable()` が無視されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-653">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="a36d7-654">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-654">**New behavior**</span></span>

<span data-ttu-id="a36d7-655">EF Core 3.0 以降、および今後のリリースでの TPT と TPC のサポートの追加準備中には、派生型で呼び出された `ToTable()` で、今後の予期されないマッピング変更を回避するために例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-655">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="a36d7-656">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-656">**Why**</span></span>

<span data-ttu-id="a36d7-657">現在、派生型を別のテーブルにマップすることは有効ではありません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-657">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="a36d7-658">この変更により、今後、これを行うことが有効になった場合に中断されなくなります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-658">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="a36d7-659">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-659">**Mitigations**</span></span>

<span data-ttu-id="a36d7-660">派生型を他のテーブルにマップしないようにします。</span><span class="sxs-lookup"><span data-stu-id="a36d7-660">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="a36d7-661">ForSqlServerHasIndex が HasIndex に置き換えられた</span><span class="sxs-lookup"><span data-stu-id="a36d7-661">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="a36d7-662">問題 #12366 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-662">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="a36d7-663">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-663">**Old behavior**</span></span>

<span data-ttu-id="a36d7-664">EF Core 3.0 より前では、`ForSqlServerHasIndex().ForSqlServerInclude()` によって、`INCLUDE` で使用される列を構成する方法が提供されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-664">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="a36d7-665">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-665">**New behavior**</span></span>

<span data-ttu-id="a36d7-666">EF Core 3.0 以降では、インデックスでの `Include` の使用が、リレーショナル レベルでサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-666">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="a36d7-667">`HasIndex().ForSqlServerInclude()` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="a36d7-667">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="a36d7-668">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-668">**Why**</span></span>

<span data-ttu-id="a36d7-669">この変更は、すべてのデータ プロバイダーのために `Include` でインデックス用の API を 1 か所に統合するために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-669">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="a36d7-670">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-670">**Mitigations**</span></span>

<span data-ttu-id="a36d7-671">上記のように、新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-671">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="a36d7-672">メタデータ API の変更点</span><span class="sxs-lookup"><span data-stu-id="a36d7-672">Metadata API changes</span></span>

[<span data-ttu-id="a36d7-673">問題 #214 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-673">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="a36d7-674">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-674">**New behavior**</span></span>

<span data-ttu-id="a36d7-675">次のプロパティは拡張メソッドに変換されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-675">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="a36d7-676">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-676">**Why**</span></span>

<span data-ttu-id="a36d7-677">この変更により、前述のインターフェイスの実装が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-677">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="a36d7-678">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-678">**Mitigations**</span></span>

<span data-ttu-id="a36d7-679">新しい拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-679">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="a36d7-680">プロバイダー固有のメタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="a36d7-680">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="a36d7-681">問題 #214 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-681">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="a36d7-682">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-682">**New behavior**</span></span>

<span data-ttu-id="a36d7-683">プロバイダー固有の拡張メソッドがフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-683">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

<span data-ttu-id="a36d7-684">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-684">**Why**</span></span>

<span data-ttu-id="a36d7-685">この変更により、前述の拡張メソッドの実装が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-685">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="a36d7-686">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-686">**Mitigations**</span></span>

<span data-ttu-id="a36d7-687">新しい拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-687">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="a36d7-688">EF Core で SQLite FK を適用するためのプラグマが送信されなくなった</span><span class="sxs-lookup"><span data-stu-id="a36d7-688">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="a36d7-689">問題 #12151 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-689">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="a36d7-690">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-690">**Old behavior**</span></span>

<span data-ttu-id="a36d7-691">EF Core 3.0 より前では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-691">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="a36d7-692">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-692">**New behavior**</span></span>

<span data-ttu-id="a36d7-693">EF Core 3.0 以降では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-693">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="a36d7-694">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-694">**Why**</span></span>

<span data-ttu-id="a36d7-695">この変更は、既定で EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用され、その後、FK の適用が既定で有効になり、接続が開かれるたびに明示的に有効にする必要がないため、行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-695">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="a36d7-696">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-696">**Mitigations**</span></span>

<span data-ttu-id="a36d7-697">外部キーは、EF Core に対して既定で使用される、SQLitePCLRaw.bundle_e_sqlite3 で既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-697">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="a36d7-698">それ以外の場合は、接続文字列で `Foreign Keys=True` を指定することで、外部キーを有効にできます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-698">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="a36d7-699">Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-699">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="a36d7-700">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-700">**Old behavior**</span></span>

<span data-ttu-id="a36d7-701">EF Core 3.0 より前では、EF Core では `SQLitePCLRaw.bundle_green` が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-701">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="a36d7-702">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-702">**New behavior**</span></span>

<span data-ttu-id="a36d7-703">EF Core 3.0 以降では、EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-703">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="a36d7-704">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-704">**Why**</span></span>

<span data-ttu-id="a36d7-705">この変更は、iOS 上で使用される SQLite のバージョンを、他のプラットフォームと一致させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-705">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="a36d7-706">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-706">**Mitigations**</span></span>

<span data-ttu-id="a36d7-707">iOS でネイティブの SQLite バージョンを使用するには、別の `SQLitePCLRaw` バンドルを使用するように `Microsoft.Data.Sqlite` を構成します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-707">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="a36d7-708">GUID の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-708">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="a36d7-709">問題 #15078 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-709">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="a36d7-710">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-710">**Old behavior**</span></span>

<span data-ttu-id="a36d7-711">GUID の値は、以前は SQLite に BLOB 値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-711">Guid values were previously stored as BLOB values on SQLite.</span></span>

<span data-ttu-id="a36d7-712">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-712">**New behavior**</span></span>

<span data-ttu-id="a36d7-713">GUID の値はテキストとして格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-713">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="a36d7-714">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-714">**Why**</span></span>

<span data-ttu-id="a36d7-715">GUID のバイナリ形式は標準化されていません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-715">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="a36d7-716">値をテキストとして格納する方が、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="a36d7-716">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="a36d7-717">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-717">**Mitigations**</span></span>

<span data-ttu-id="a36d7-718">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-718">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
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

<span data-ttu-id="a36d7-719">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-719">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="a36d7-720">Microsoft.Data.Sqlite は引き続き、BLOB とテキストの両方の列から GUID 値を読み取ることができます。ただし、パラメーターと定数の既定の形式が変更されているため、GUID が含まれる多くのシナリオではアクションが必要になります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-720">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="a36d7-721">Char の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-721">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="a36d7-722">問題 #15020 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-722">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="a36d7-723">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-723">**Old behavior**</span></span>

<span data-ttu-id="a36d7-724">Char の値は、以前は SQLite に整数値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-724">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="a36d7-725">たとえば、Char の値 *A* は整数値 65 として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-725">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="a36d7-726">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-726">**New behavior**</span></span>

<span data-ttu-id="a36d7-727">Char の値はテキストとして格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-727">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="a36d7-728">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-728">**Why**</span></span>

<span data-ttu-id="a36d7-729">値をテキストとして格納する方が自然であり、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="a36d7-729">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="a36d7-730">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-730">**Mitigations**</span></span>

<span data-ttu-id="a36d7-731">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-731">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="a36d7-732">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-732">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="a36d7-733">Microsoft.Data.Sqlite では、引き続き整数とテキストの両方の列の文字列値を読み取ることができるため、一部のシナリオではアクションが必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-733">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="a36d7-734">移行 ID がインバリアント カルチャの暦を使用して生成されるようになった</span><span class="sxs-lookup"><span data-stu-id="a36d7-734">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="a36d7-735">問題 #12978 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-735">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="a36d7-736">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-736">**Old behavior**</span></span>

<span data-ttu-id="a36d7-737">移行 ID は、誤って現在のカルチャの暦を使用して生成されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-737">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="a36d7-738">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-738">**New behavior**</span></span>

<span data-ttu-id="a36d7-739">移行 ID がインバリアント カルチャの暦 (グレゴリオ暦) を使用して常に生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-739">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="a36d7-740">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-740">**Why**</span></span>

<span data-ttu-id="a36d7-741">データベースを更新するときやマージの競合を解決するときに、移行の順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="a36d7-741">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="a36d7-742">インバリアントの暦を使用することで、順序の問題が回避され、システムの予定表がチームのメンバーごとに違うことがなくなります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-742">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="a36d7-743">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-743">**Mitigations**</span></span>

<span data-ttu-id="a36d7-744">この変更は、1 年がグレゴリオ暦より長くなるグレゴリオ暦以外の暦 (タイ仏暦など) を使用しているすべてのユーザーに影響します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-744">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="a36d7-745">既存の移行後に新しい移行順序が設定されるように、既存の移行 ID を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-745">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="a36d7-746">移行 ID は、移行のデザイナー ファイルの移行属性にあります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-746">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="a36d7-747">移行履歴テーブルも更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-747">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="a36d7-748">UseRowNumberForPaging の削除</span><span class="sxs-lookup"><span data-stu-id="a36d7-748">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="a36d7-749">問題 #16400 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-749">Tracking Issue #16400</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

<span data-ttu-id="a36d7-750">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-750">**Old behavior**</span></span>

<span data-ttu-id="a36d7-751">EF Core 3.0 より前では、`UseRowNumberForPaging` を使って、SQL Server 2008 と互換性のあるページング用の SQL を生成することができました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-751">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

<span data-ttu-id="a36d7-752">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-752">**New behavior**</span></span>

<span data-ttu-id="a36d7-753">EF Core 3.0 以降では、EF によって生成できるのは、それ以降のバージョンの SQL Server とのみ互換性があるページング用の SQL のみとなります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-753">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span> 

<span data-ttu-id="a36d7-754">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-754">**Why**</span></span>

<span data-ttu-id="a36d7-755">[SQL Server 2008 はもうサポートされていない製品であり](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/)、EF Core 3.0 で行われたクエリの変更に対応するようこの機能を更新する作業は負荷が大きいため、このような変更が行われます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-755">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

<span data-ttu-id="a36d7-756">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-756">**Mitigations**</span></span>

<span data-ttu-id="a36d7-757">生成された SQL がサポートされるように、より新しいバージョンの SQL Server に更新するか、より高い互換性レベルを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a36d7-757">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="a36d7-758">ただし、これを実行できない場合は、詳細情報とともに[問題の追跡にコメント](https://github.com/aspnet/EntityFrameworkCore/issues/16400)してください。</span><span class="sxs-lookup"><span data-stu-id="a36d7-758">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) with details.</span></span> <span data-ttu-id="a36d7-759">Microsoft では、フィードバックに基づいてこの決定を再検討する場合があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-759">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="a36d7-760">IDbContextOptionsExtension から拡張機能の情報/メタデータを削除</span><span class="sxs-lookup"><span data-stu-id="a36d7-760">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="a36d7-761">問題 #16119 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-761">Tracking Issue #16119</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

<span data-ttu-id="a36d7-762">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-762">**Old behavior**</span></span>

<span data-ttu-id="a36d7-763">`IDbContextOptionsExtension` には、拡張機能に関するメタデータを提供するためのメソッドが含まれていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-763">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

<span data-ttu-id="a36d7-764">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-764">**New behavior**</span></span>

<span data-ttu-id="a36d7-765">これらのメソッドは、新しい抽象基底クラス `DbContextOptionsExtensionInfo` 上に移動されました。これは新しい `IDbContextOptionsExtension.Info` プロパティから返されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-765">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

<span data-ttu-id="a36d7-766">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-766">**Why**</span></span>

<span data-ttu-id="a36d7-767">2\.0 から 3.0 までのリリースを通して、これらのメソッドに追加または変更を行う必要が複数回発生しました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-767">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="a36d7-768">それらを抜き出して新しい抽象基底クラスに含めることで、既存の拡張機能を損なうことなく、簡単にこのような変更を加えられるようになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-768">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

<span data-ttu-id="a36d7-769">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-769">**Mitigations**</span></span>

<span data-ttu-id="a36d7-770">新しいパターンに従うよう拡張機能を更新します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-770">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="a36d7-771">その例は、EF Core のソース コードの、さまざまな種類の拡張機能に対する多数の `IDbContextOptionsExtension` の実装で見つかります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-771">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="a36d7-772">LogQueryPossibleExceptionWithAggregateOperator の名前が変更された</span><span class="sxs-lookup"><span data-stu-id="a36d7-772">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="a36d7-773">問題 #10985 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-773">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="a36d7-774">**変更**</span><span class="sxs-lookup"><span data-stu-id="a36d7-774">**Change**</span></span>

<span data-ttu-id="a36d7-775">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` の名前が `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-775">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="a36d7-776">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-776">**Why**</span></span>

<span data-ttu-id="a36d7-777">この警告イベントの名前が他のすべての警告イベントと照合されるためです。</span><span class="sxs-lookup"><span data-stu-id="a36d7-777">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="a36d7-778">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-778">**Mitigations**</span></span>

<span data-ttu-id="a36d7-779">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-779">Use the new name.</span></span> <span data-ttu-id="a36d7-780">(イベント ID 番号が変更されていないことを確認してください。)</span><span class="sxs-lookup"><span data-stu-id="a36d7-780">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="a36d7-781">外部キー制約名の API が明確化された</span><span class="sxs-lookup"><span data-stu-id="a36d7-781">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="a36d7-782">問題 #10730 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-782">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="a36d7-783">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-783">**Old behavior**</span></span>

<span data-ttu-id="a36d7-784">EF Core 3.0 以前は、外部キー制約名が単に "名前" と呼ばれていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-784">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="a36d7-785">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-785">For example:</span></span>

```C#
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="a36d7-786">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-786">**New behavior**</span></span>

<span data-ttu-id="a36d7-787">EF Core 3.0 以降は、外部キー制約名が "制約名" と呼ばれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-787">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="a36d7-788">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-788">For example:</span></span>

```C#
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="a36d7-789">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-789">**Why**</span></span>

<span data-ttu-id="a36d7-790">この変更により、この領域の名前付けに一貫性がもたらされます。また、それが外部キーが定義されている列やプロパティの名前ではなく、外部キー制約の名前であることが明確になりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-790">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="a36d7-791">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-791">**Mitigations**</span></span>

<span data-ttu-id="a36d7-792">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-792">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="a36d7-793">IRelationalDatabaseCreator.HasTables/HasTablesAsync をパブリックに変更</span><span class="sxs-lookup"><span data-stu-id="a36d7-793">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="a36d7-794">問題 #15997 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-794">Tracking Issue #15997</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

<span data-ttu-id="a36d7-795">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-795">**Old behavior**</span></span>

<span data-ttu-id="a36d7-796">EF Core 3.0 より前では、これらのメソッドは保護されていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-796">Before EF Core 3.0, these methods were protected.</span></span>

<span data-ttu-id="a36d7-797">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-797">**New behavior**</span></span>

<span data-ttu-id="a36d7-798">EF Core 3.0 以降、これらのメソッドはパブリックになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-798">Starting with EF Core 3.0, these methods are public.</span></span>

<span data-ttu-id="a36d7-799">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-799">**Why**</span></span>

<span data-ttu-id="a36d7-800">これらのメソッドは、作成されたデータベースが空であるかどうかを判断するために EF によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-800">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="a36d7-801">これは、EF の外部から、移行を適用するかどうか判断する場合にも役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-801">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

<span data-ttu-id="a36d7-802">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-802">**Mitigations**</span></span>

<span data-ttu-id="a36d7-803">すべてのオーバーライドのアクセシビリティを変更します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-803">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="a36d7-804">Microsoft.EntityFrameworkCore.Design を DevelopmentDependency パッケージに変更</span><span class="sxs-lookup"><span data-stu-id="a36d7-804">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="a36d7-805">問題 #11506 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-805">Tracking Issue #11506</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

<span data-ttu-id="a36d7-806">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-806">**Old behavior**</span></span>

<span data-ttu-id="a36d7-807">EF Core 3.0 より前では、Microsoft.EntityFrameworkCore.Design は通常の NuGet パッケージであり、それに依存していたプロジェクトによってそのアセンブリを参照できました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-807">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

<span data-ttu-id="a36d7-808">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-808">**New behavior**</span></span>

<span data-ttu-id="a36d7-809">EF Core 3.0 以降、これは DevelopmentDependency パッケージになります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-809">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="a36d7-810">つまり、依存関係が他のプロジェクトに推移的にフローすることがなくなり、規定ではそのアセンブリを参照できなくなります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-810">Which means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

<span data-ttu-id="a36d7-811">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-811">**Why**</span></span>

<span data-ttu-id="a36d7-812">このパッケージは、デザイン時に使用されることだけを想定しています。</span><span class="sxs-lookup"><span data-stu-id="a36d7-812">This package is only intended to be used at design time.</span></span> <span data-ttu-id="a36d7-813">デプロイされたアプリケーションからこれを参照すべきではありません。</span><span class="sxs-lookup"><span data-stu-id="a36d7-813">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="a36d7-814">パッケージを DevelopmentDependency にすることで、この推奨事項が強化されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-814">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

<span data-ttu-id="a36d7-815">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-815">**Mitigations**</span></span>

<span data-ttu-id="a36d7-816">EF Core のデザイン時の動作をオーバーライドするためにこのパッケージを参照する必要がある場合は、プロジェクトの PackageReference 項目メタデータを更新することができます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-816">If you need to reference this package to override EF Core's design-time behavior, you can update update PackageReference item metadata in your project.</span></span> <span data-ttu-id="a36d7-817">このパッケージが Microsoft.EntityFrameworkCore.Tools 経由で推移的に参照されている場合は、パッケージに明示的な PackageReference を追加して、そのメタデータを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-817">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="a36d7-818">SQLitePCL.raw のバージョン 2.0.0 への更新</span><span class="sxs-lookup"><span data-stu-id="a36d7-818">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="a36d7-819">問題 #14824 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-819">Tracking Issue #14824</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

<span data-ttu-id="a36d7-820">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-820">**Old behavior**</span></span>

<span data-ttu-id="a36d7-821">Microsoft.EntityFrameworkCore.Sqlite は、以前はバージョン 1.1.12 の SQLitePCL.raw に依存していました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-821">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

<span data-ttu-id="a36d7-822">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-822">**New behavior**</span></span>

<span data-ttu-id="a36d7-823">バージョン 2.0.0 に依存するようパッケージが更新されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-823">We've updated our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="a36d7-824">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-824">**Why**</span></span>

<span data-ttu-id="a36d7-825">バージョン 2.0.0 の SQLitePCL.raw では、.NET Standard 2.0 をターゲットとします。</span><span class="sxs-lookup"><span data-stu-id="a36d7-825">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="a36d7-826">これは以前は、推移的なパッケージの大規模なクロージャの動作を必要とする .NET Standard 1.1 をターゲットとしていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-826">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

<span data-ttu-id="a36d7-827">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-827">**Mitigations**</span></span>

<span data-ttu-id="a36d7-828">SQLitePCL.raw バージョン 2.0.0 には、いくつかの破壊的変更が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a36d7-828">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="a36d7-829">詳細については、[リリース ノート](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a36d7-829">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="a36d7-830">NetTopologySuite のバージョン 2.0.0 への更新</span><span class="sxs-lookup"><span data-stu-id="a36d7-830">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="a36d7-831">問題 #14825 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-831">Tracking Issue #14825</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

<span data-ttu-id="a36d7-832">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-832">**Old behavior**</span></span>

<span data-ttu-id="a36d7-833">以前の空間パッケージはバージョン 1.15.1 の NetTopologySuite に依存していました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-833">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

<span data-ttu-id="a36d7-834">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-834">**New behavior**</span></span>

<span data-ttu-id="a36d7-835">バージョン 2.0.0 に依存するようパッケージが更新されました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-835">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="a36d7-836">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-836">**Why**</span></span>

<span data-ttu-id="a36d7-837">バージョン 2.0.0 の NetTopologySuite は、EF Core の使用に伴ういくつかのユーザビリティの問題に対処することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="a36d7-837">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

<span data-ttu-id="a36d7-838">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-838">**Mitigations**</span></span>

<span data-ttu-id="a36d7-839">NetTopologySuite バージョン 2.0.0 には、いくつかの破壊的変更が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a36d7-839">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="a36d7-840">詳細については、[リリース ノート](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a36d7-840">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="a36d7-841">System.Data.SqlClient ではなく Microsoft.Data.SqlClient が使用される</span><span class="sxs-lookup"><span data-stu-id="a36d7-841">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="a36d7-842">問題 #15636 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-842">Tracking Issue #15636</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

<span data-ttu-id="a36d7-843">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-843">**Old behavior**</span></span>

<span data-ttu-id="a36d7-844">Microsoft.EntityFrameworkCore.SqlServer は、以前は System.Data.SqlClient に依存していました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-844">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

<span data-ttu-id="a36d7-845">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-845">**New behavior**</span></span>

<span data-ttu-id="a36d7-846">Microsoft.Data.SqlClient に依存するようにパッケージを更新しました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-846">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="a36d7-847">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-847">**Why**</span></span>

<span data-ttu-id="a36d7-848">Microsoft.Data.SqlClient は今後の SQL Server の主力データ アクセス ドライバーであり、System.Data.SqlClient は開発の中心ではなくなりました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-848">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="a36d7-849">Always Encrypted などの重要な機能は、Microsoft.Data.SqlClient でのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-849">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="a36d7-850">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-850">**Mitigations**</span></span>

<span data-ttu-id="a36d7-851">コードが System.Data.SqlClient に直接依存している場合は、代わりに Microsoft.Data.SqlClient を参照するように変更する必要があります。2 つのパッケージは非常に高度な API 互換性を維持しているため、これは単純なパッケージと名前空間の変更のみである必要があります。</span><span class="sxs-lookup"><span data-stu-id="a36d7-851">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="a36d7-852">複数のあいまいな自己参照リレーションシップを構成する必要がある</span><span class="sxs-lookup"><span data-stu-id="a36d7-852">Multiple ambiguous self-referencing relationships must be configured</span></span> 

[<span data-ttu-id="a36d7-853">イシュー #13573 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-853">Tracking Issue #13573</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

<span data-ttu-id="a36d7-854">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-854">**Old behavior**</span></span>

<span data-ttu-id="a36d7-855">複数の自己参照型一方向ナビゲーション プロパティと一致する FK を持つエンティティ型が、1 つのリレーションシップとして正しく構成されませんでした。</span><span class="sxs-lookup"><span data-stu-id="a36d7-855">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="a36d7-856">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-856">For example:</span></span>

```C#
public class User 
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

<span data-ttu-id="a36d7-857">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-857">**New behavior**</span></span>

<span data-ttu-id="a36d7-858">このシナリオがモデル構築で検出されるようになり、モデルがあいまいであることを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-858">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

<span data-ttu-id="a36d7-859">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-859">**Why**</span></span>

<span data-ttu-id="a36d7-860">結果として得られるモデルはあいまいであり、通常、この場合には間違っている可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="a36d7-860">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

<span data-ttu-id="a36d7-861">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-861">**Mitigations**</span></span>

<span data-ttu-id="a36d7-862">完全に構成されたリレーションシップを使用します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-862">Use full configuration of the relationship.</span></span> <span data-ttu-id="a36d7-863">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-863">For example:</span></span>

```C#
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
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="a36d7-864">DbFunction.Schema が null または空の文字列である場合、モデルの既定のスキーマに構成される</span><span class="sxs-lookup"><span data-stu-id="a36d7-864">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="a36d7-865">問題 #12757 の追跡</span><span class="sxs-lookup"><span data-stu-id="a36d7-865">Tracking Issue #12757</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

<span data-ttu-id="a36d7-866">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-866">**Old behavior**</span></span>

<span data-ttu-id="a36d7-867">空の文字列としてスキーマを使用して構成された DbFunction は、スキーマのない組み込み関数として扱われていました。</span><span class="sxs-lookup"><span data-stu-id="a36d7-867">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="a36d7-868">たとえば、次のコードは `DatePart` CLR 関数を SqlServer の組み込み関数 `DATEPART` にマップします。</span><span class="sxs-lookup"><span data-stu-id="a36d7-868">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```C#
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

<span data-ttu-id="a36d7-869">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="a36d7-869">**New behavior**</span></span>

<span data-ttu-id="a36d7-870">すべての DbFunction マッピングは、ユーザー定義関数にマップされるものと見なされます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-870">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="a36d7-871">そのため、空の文字列値を指定すると、関数がモデルの既定のスキーマ内に配置されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-871">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="a36d7-872">それ以外の、スキーマとなり得るものは fluent API の `modelBuilder.HasDefaultSchema()` または `dbo` を使用して明示的に構成されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-872">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

<span data-ttu-id="a36d7-873">**理由**</span><span class="sxs-lookup"><span data-stu-id="a36d7-873">**Why**</span></span>

<span data-ttu-id="a36d7-874">以前は空のスキーマを使用して関数を組み込みとして扱っていましたが、そのロジックは、組み込みの関数がどのスキーマにも属していない SqlServer にのみ適用されます。</span><span class="sxs-lookup"><span data-stu-id="a36d7-874">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

<span data-ttu-id="a36d7-875">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="a36d7-875">**Mitigations**</span></span>

<span data-ttu-id="a36d7-876">DbFunction の変換を手動で組み込み関数にマップされるように構成します。</span><span class="sxs-lookup"><span data-stu-id="a36d7-876">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```C#
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```
