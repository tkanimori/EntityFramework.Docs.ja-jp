---
title: EF Core 3.0 での破壊的変更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: 04487291f24bb702dad4b497c34234afdd5e3c9a
ms.sourcegitcommit: d01fc19aa42ca34c3bebccbc96ee26d06fcecaa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005583"
---
# <a name="breaking-changes-included-in-ef-core-30"></a><span data-ttu-id="33f33-102">EF Core 3.0 に含まれる破壊的変更</span><span class="sxs-lookup"><span data-stu-id="33f33-102">Breaking changes included in EF Core 3.0</span></span>
<span data-ttu-id="33f33-103">以下の API と動作変更により、3.0.0 へのアップグレード時に、既存のアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-103">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="33f33-104">データベース プロバイダーにのみ影響することが予想される変更については、[プロバイダーの変更](../../providers/provider-log.md)に関するページに記載されています。</span><span class="sxs-lookup"><span data-stu-id="33f33-104">Changes that we expect to only impact database providers are documented under [provider changes](../../providers/provider-log.md).</span></span>
<span data-ttu-id="33f33-105">ある 3.0 プレビューから別の 3.0 プレビューへの破壊的変更については、ここには記載されていません。</span><span class="sxs-lookup"><span data-stu-id="33f33-105">Breaks from one 3.0 preview to another 3.0 preview aren't documented here.</span></span>

## <a name="summary"></a><span data-ttu-id="33f33-106">まとめ</span><span class="sxs-lookup"><span data-stu-id="33f33-106">Summary</span></span>

| <span data-ttu-id="33f33-107">**重大な変更**</span><span class="sxs-lookup"><span data-stu-id="33f33-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="33f33-108">**影響**</span><span class="sxs-lookup"><span data-stu-id="33f33-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="33f33-109">LINQ クエリがクライアントで評価されなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="33f33-110">High</span><span class="sxs-lookup"><span data-stu-id="33f33-110">High</span></span>       |
| [<span data-ttu-id="33f33-111">EF Core 3.0 では .NET Standard 2.0 ではなく .NET Standard 2.1 がターゲットにされる</span><span class="sxs-lookup"><span data-stu-id="33f33-111">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>](#netstandard21) | <span data-ttu-id="33f33-112">High</span><span class="sxs-lookup"><span data-stu-id="33f33-112">High</span></span>      |
| [<span data-ttu-id="33f33-113">EF Core のコマンドライン ツールである dotnet ef が .NET Core SDK の一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-113">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="33f33-114">High</span><span class="sxs-lookup"><span data-stu-id="33f33-114">High</span></span>      |
| [<span data-ttu-id="33f33-115">FromSql、ExecuteSql、および ExecuteSqlAsync の名前が変更された</span><span class="sxs-lookup"><span data-stu-id="33f33-115">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="33f33-116">High</span><span class="sxs-lookup"><span data-stu-id="33f33-116">High</span></span>      |
| [<span data-ttu-id="33f33-117">クエリ型がエンティティ型と統合される</span><span class="sxs-lookup"><span data-stu-id="33f33-117">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="33f33-118">High</span><span class="sxs-lookup"><span data-stu-id="33f33-118">High</span></span>      |
| [<span data-ttu-id="33f33-119">Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-119">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="33f33-120">Medium</span><span class="sxs-lookup"><span data-stu-id="33f33-120">Medium</span></span>      |
| [<span data-ttu-id="33f33-121">既定で連鎖削除がすぐに行われるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-121">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="33f33-122">Medium</span><span class="sxs-lookup"><span data-stu-id="33f33-122">Medium</span></span>      |
| [<span data-ttu-id="33f33-123">DeleteBehavior.Restrict のセマンティクスがクリーンになった</span><span class="sxs-lookup"><span data-stu-id="33f33-123">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="33f33-124">Medium</span><span class="sxs-lookup"><span data-stu-id="33f33-124">Medium</span></span>      |
| [<span data-ttu-id="33f33-125">所有型のリレーションシップ用の構成 API が変更された</span><span class="sxs-lookup"><span data-stu-id="33f33-125">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="33f33-126">Medium</span><span class="sxs-lookup"><span data-stu-id="33f33-126">Medium</span></span>      |
| [<span data-ttu-id="33f33-127">各プロパティで独立したメモリ内整数キー生成が使用される</span><span class="sxs-lookup"><span data-stu-id="33f33-127">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="33f33-128">Medium</span><span class="sxs-lookup"><span data-stu-id="33f33-128">Medium</span></span>      |
| [<span data-ttu-id="33f33-129">追跡なしのクエリでは、識別子の解決が実行されなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-129">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="33f33-130">Medium</span><span class="sxs-lookup"><span data-stu-id="33f33-130">Medium</span></span>      |
| [<span data-ttu-id="33f33-131">メタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="33f33-131">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="33f33-132">Medium</span><span class="sxs-lookup"><span data-stu-id="33f33-132">Medium</span></span>      |
| [<span data-ttu-id="33f33-133">プロバイダー固有のメタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="33f33-133">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="33f33-134">Medium</span><span class="sxs-lookup"><span data-stu-id="33f33-134">Medium</span></span>      |
| [<span data-ttu-id="33f33-135">UseRowNumberForPaging が削除された</span><span class="sxs-lookup"><span data-stu-id="33f33-135">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="33f33-136">Medium</span><span class="sxs-lookup"><span data-stu-id="33f33-136">Medium</span></span>      |
| [<span data-ttu-id="33f33-137">FromSql メソッドはクエリのルートでのみ指定できる</span><span class="sxs-lookup"><span data-stu-id="33f33-137">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="33f33-138">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-138">Low</span></span>      |
| [<span data-ttu-id="33f33-139">~~クエリの実行がデバッグ レベルでログに記録される~~ 元に戻されます</span><span class="sxs-lookup"><span data-stu-id="33f33-139">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | <span data-ttu-id="33f33-140">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-140">Low</span></span>      |
| [<span data-ttu-id="33f33-141">一時キーの値がエンティティ インスタンスに設定されなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-141">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="33f33-142">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-142">Low</span></span>      |
| [<span data-ttu-id="33f33-143">DetectChanges でストア生成キーの値が優先される</span><span class="sxs-lookup"><span data-stu-id="33f33-143">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="33f33-144">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-144">Low</span></span>      |
| [<span data-ttu-id="33f33-145">プリンシパルとテーブルを共有する依存エンティティが省略可能になった</span><span class="sxs-lookup"><span data-stu-id="33f33-145">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="33f33-146">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-146">Low</span></span>      |
| [<span data-ttu-id="33f33-147">同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要がある</span><span class="sxs-lookup"><span data-stu-id="33f33-147">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="33f33-148">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-148">Low</span></span>      |
| [<span data-ttu-id="33f33-149">マップされていない型から継承されたプロパティが、すべての派生型の 1 つの列にマップされるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-149">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="33f33-150">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-150">Low</span></span>      |
| [<span data-ttu-id="33f33-151">外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-151">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="33f33-152">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-152">Low</span></span>      |
| [<span data-ttu-id="33f33-153">データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-153">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="33f33-154">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-154">Low</span></span>      |
| [<span data-ttu-id="33f33-155">バッキング フィールドが既定で使用される</span><span class="sxs-lookup"><span data-stu-id="33f33-155">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="33f33-156">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-156">Low</span></span>      |
| [<span data-ttu-id="33f33-157">複数の互換性があるバッキング フィールドが見つかった場合にスローされる</span><span class="sxs-lookup"><span data-stu-id="33f33-157">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="33f33-158">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-158">Low</span></span>      |
| [<span data-ttu-id="33f33-159">フィールド専用プロパティの名前はフィールドの名前に一致する必要がある</span><span class="sxs-lookup"><span data-stu-id="33f33-159">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="33f33-160">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-160">Low</span></span>      |
| [<span data-ttu-id="33f33-161">AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-161">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="33f33-162">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-162">Low</span></span>      |
| [<span data-ttu-id="33f33-163">DbContext.Entry でローカルの DetectChanges が実行されるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-163">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="33f33-164">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-164">Low</span></span>      |
| [<span data-ttu-id="33f33-165">文字列とバイト配列のキーが既定でクライアントによって生成されない</span><span class="sxs-lookup"><span data-stu-id="33f33-165">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="33f33-166">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-166">Low</span></span>      |
| [<span data-ttu-id="33f33-167">ILoggerFactory がスコープ サービスになった</span><span class="sxs-lookup"><span data-stu-id="33f33-167">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="33f33-168">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-168">Low</span></span>      |
| [<span data-ttu-id="33f33-169">遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-169">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="33f33-170">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-170">Low</span></span>      |
| [<span data-ttu-id="33f33-171">内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-171">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="33f33-172">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-172">Low</span></span>      |
| [<span data-ttu-id="33f33-173">1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作</span><span class="sxs-lookup"><span data-stu-id="33f33-173">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="33f33-174">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-174">Low</span></span>      |
| [<span data-ttu-id="33f33-175">いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更された</span><span class="sxs-lookup"><span data-stu-id="33f33-175">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="33f33-176">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-176">Low</span></span>      |
| [<span data-ttu-id="33f33-177">Relational:TypeMapping 注釈が単に TypeMapping となった</span><span class="sxs-lookup"><span data-stu-id="33f33-177">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="33f33-178">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-178">Low</span></span>      |
| [<span data-ttu-id="33f33-179">派生型の ToTable で例外がスローされる</span><span class="sxs-lookup"><span data-stu-id="33f33-179">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="33f33-180">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-180">Low</span></span>      |
| [<span data-ttu-id="33f33-181">EF Core で SQLite FK を適用するためのプラグマが送信されなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-181">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="33f33-182">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-182">Low</span></span>      |
| [<span data-ttu-id="33f33-183">Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-183">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="33f33-184">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-184">Low</span></span>      |
| [<span data-ttu-id="33f33-185">GUID の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-185">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="33f33-186">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-186">Low</span></span>      |
| [<span data-ttu-id="33f33-187">Char の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-187">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="33f33-188">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-188">Low</span></span>      |
| [<span data-ttu-id="33f33-189">移行 ID がインバリアント カルチャの暦を使用して生成されるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-189">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="33f33-190">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-190">Low</span></span>      |
| [<span data-ttu-id="33f33-191">IDbContextOptionsExtension から拡張機能の情報/メタデータが削除された</span><span class="sxs-lookup"><span data-stu-id="33f33-191">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="33f33-192">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-192">Low</span></span>      |
| [<span data-ttu-id="33f33-193">LogQueryPossibleExceptionWithAggregateOperator の名前が変更された</span><span class="sxs-lookup"><span data-stu-id="33f33-193">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="33f33-194">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-194">Low</span></span>      |
| [<span data-ttu-id="33f33-195">外部キー制約名の API が明確化された</span><span class="sxs-lookup"><span data-stu-id="33f33-195">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="33f33-196">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-196">Low</span></span>      |
| [<span data-ttu-id="33f33-197">IRelationalDatabaseCreator.HasTables/HasTablesAsync がパブリックに変更された</span><span class="sxs-lookup"><span data-stu-id="33f33-197">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="33f33-198">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-198">Low</span></span>      |
| [<span data-ttu-id="33f33-199">Microsoft.EntityFrameworkCore.Design が DevelopmentDependency パッケージになった</span><span class="sxs-lookup"><span data-stu-id="33f33-199">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="33f33-200">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-200">Low</span></span>      |
| [<span data-ttu-id="33f33-201">SQLitePCL.raw がバージョン 2.0.0 に更新された</span><span class="sxs-lookup"><span data-stu-id="33f33-201">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="33f33-202">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-202">Low</span></span>      |
| [<span data-ttu-id="33f33-203">NetTopologySuite がバージョン 2.0.0 に更新された</span><span class="sxs-lookup"><span data-stu-id="33f33-203">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="33f33-204">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-204">Low</span></span>      |
| [<span data-ttu-id="33f33-205">複数のあいまいな自己参照リレーションシップを構成する必要がある</span><span class="sxs-lookup"><span data-stu-id="33f33-205">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="33f33-206">Low</span><span class="sxs-lookup"><span data-stu-id="33f33-206">Low</span></span>      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="33f33-207">LINQ クエリがクライアントで評価されなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-207">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="33f33-208">[問題 #14935 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[問題 #12795 も参照](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="33f33-208">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="33f33-209">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-209">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-210">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-210">**Old behavior**</span></span>

<span data-ttu-id="33f33-211">3\.0 より前では、EF Core では、SQL またはパラメーターに対するクエリの一部だった式を変換できない場合、クライアントで自動的に式が評価されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-211">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="33f33-212">既定では、コストの高い式のクライアント評価でのみ警告がトリガーされました。</span><span class="sxs-lookup"><span data-stu-id="33f33-212">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="33f33-213">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-213">**New behavior**</span></span>

<span data-ttu-id="33f33-214">3\.0 以降では、クライアントで評価される最上位のプロジェクション (クエリの最後の `Select()` 呼び出し) の式のみが EF Core で許可されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-214">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="33f33-215">クエリの他の部分の式を SQL やパラメーターに変換できない場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="33f33-215">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="33f33-216">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-216">**Why**</span></span>

<span data-ttu-id="33f33-217">クエリの自動クライアント評価では、多くのクエリを実行できます。これらの重要な部分を変換できない場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="33f33-217">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="33f33-218">この動作が原因で、予期しない損害を与える動作となる可能性があります。この動作は、運用環境でのみ見られる場合があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-218">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="33f33-219">たとえば、変換できない `Where()` 呼び出しの条件が原因で、テーブルのすべての行がデータベース サーバーから転送できるようになり、クライアントでフィルターを適用できるようになる場合があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-219">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="33f33-220">このような状況は、開発時にテーブルにわずかな行が含まれている場合は、簡単に検出される可能性があります。しかし、テーブルに数百万もの行が含まれる、運用環境にアプリケーションを移行する場合は難しくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-220">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="33f33-221">クライアント評価の警告では、開発中にあまりに簡単に無視されることも示されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-221">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="33f33-222">これだけでなく、自動クライアント評価は、特定の式のクエリ変換の改善が、リリース間の予期しない破壊的変更の原因となる問題につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-222">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="33f33-223">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-223">**Mitigations**</span></span>

<span data-ttu-id="33f33-224">クエリを完全に変換できない場合は、変換できる形式でクエリを書き直すか、`AsEnumerable()`、`ToList()`、または同様のものを使用して、LINQ-to-Objects を使ってさらに処理できるクライアントに明示的にデータを戻します。</span><span class="sxs-lookup"><span data-stu-id="33f33-224">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a><span data-ttu-id="33f33-225">EF Core 3.0 では .NET Standard 2.0 ではなく .NET Standard 2.1 がターゲットにされる</span><span class="sxs-lookup"><span data-stu-id="33f33-225">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>

[<span data-ttu-id="33f33-226">問題 #15498 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-226">Tracking Issue #15498</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

<span data-ttu-id="33f33-227">この変更は、EF Core 3.0 プレビュー 7 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-227">This change is introduced in EF Core 3.0-preview 7.</span></span>

<span data-ttu-id="33f33-228">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-228">**Old behavior**</span></span>

<span data-ttu-id="33f33-229">3\.0 以前の EF Core の場合は、.NET Standard 2.0 がターゲットとされ、その標準をサポートするすべてのプラットフォーム (.NET Framework を含む) 上で実行されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-229">Before 3.0, EF Core targeted .NET Standard 2.0 and would run on all platforms that support that standard, including .NET Framework.</span></span>

<span data-ttu-id="33f33-230">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-230">**New behavior**</span></span>

<span data-ttu-id="33f33-231">3\.0 以降の EF Core の場合は、.NET Standard 2.1 がターゲットとされ、この標準をサポートするすべてのプラットフォームで実行されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-231">Starting with 3.0, EF Core targets .NET Standard 2.1 and will run on all platforms that support this standard.</span></span> <span data-ttu-id="33f33-232">これには、.NET Framework は含まれません。</span><span class="sxs-lookup"><span data-stu-id="33f33-232">This does not include .NET Framework.</span></span>

<span data-ttu-id="33f33-233">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-233">**Why**</span></span>

<span data-ttu-id="33f33-234">これは、.NET Core やその他の最新の .NET プラットフォーム (Xamarin など) にエネルギーを集中するために .NET テクノロジ全体にわたって行われる戦略的な決定の一部です。</span><span class="sxs-lookup"><span data-stu-id="33f33-234">This is part of a strategic decision across .NET technologies to focus energy on .NET Core and other modern .NET platforms, such as Xamarin.</span></span>

<span data-ttu-id="33f33-235">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-235">**Mitigations**</span></span>

<span data-ttu-id="33f33-236">最新の .NET プラットフォームへの移行を検討してください。</span><span class="sxs-lookup"><span data-stu-id="33f33-236">Consider moving to a modern .NET platform.</span></span> <span data-ttu-id="33f33-237">これが不可能な場合は、EF Core 2.1 または EF Core 2.2 を引き続き使用します。どちらも .NET Framework をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="33f33-237">If this is not possible, then continue to use EF Core 2.1 or EF Core 2.2, both of which support .NET Framework.</span></span>

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="33f33-238">Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-238">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="33f33-239">問題 Announcements#325 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-239">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="33f33-240">この変更は、ASP.NET Core 3.0 プレビュー 1 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-240">This change is introduced in ASP.NET Core 3.0-preview 1.</span></span> 

<span data-ttu-id="33f33-241">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-241">**Old behavior**</span></span>

<span data-ttu-id="33f33-242">ASP.NET Core 3.0 より前では、パッケージ参照を `Microsoft.AspNetCore.App` または `Microsoft.AspNetCore.All` に追加したときに、EF Core と、SQL Server プロバイダーのような EF Core データ プロバイダーの一部が含まれました。</span><span class="sxs-lookup"><span data-stu-id="33f33-242">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="33f33-243">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-243">**New behavior**</span></span>

<span data-ttu-id="33f33-244">3\.0 以降では、ASP.NET Core 共有フレームワークには、EF Core も、いずれの EF Core データ プロバイダーも含まれません。</span><span class="sxs-lookup"><span data-stu-id="33f33-244">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="33f33-245">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-245">**Why**</span></span>

<span data-ttu-id="33f33-246">この変更の前では、アプリケーションのターゲットが ASP.NET Core であるか SQL Server であるかに応じて、EF Core を取得するには異なる手順が必要でした。</span><span class="sxs-lookup"><span data-stu-id="33f33-246">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="33f33-247">また、ASP.NET Core のアップグレードでは、必ずしも適切だとは言えない、EF Core と SQL Server プロバイダーのアップグレードが強制されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-247">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="33f33-248">この変更では、EF Core の取得のエクスペリエンスは、.NET の実装とアプリケーションの種類がサポートされる、すべてのプロバイダーで同じになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-248">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="33f33-249">また、開発者は、EF Core と EF Core データ プロバイダーのアップグレードのタイミングを正確に制御できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-249">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="33f33-250">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-250">**Mitigations**</span></span>

<span data-ttu-id="33f33-251">ASP.NET Core 3.0 アプリケーションまたはその他のサポートされるアプリケーションで EF Core を使用するには、アプリケーションで使用される EF Core データベース プロバイダーにパッケージ参照を明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="33f33-251">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="33f33-252">EF Core のコマンドライン ツールである dotnet ef が .NET Core SDK の一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-252">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="33f33-253">問題 #14016 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-253">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="33f33-254">この変更は、EF Core 3.0 プレビュー 4 および .NET Core SDK の対応するバージョンで導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-254">This change is introduced in EF Core 3.0-preview 4 and the corresponding version of the .NET Core SDK.</span></span>

<span data-ttu-id="33f33-255">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-255">**Old behavior**</span></span>

<span data-ttu-id="33f33-256">3\.0 より前は `dotnet ef` ツールが .NET Core SDK に含まれており、追加の手順を必要とせずに、任意のプロジェクトのコマンド ラインから簡単に使用できました。</span><span class="sxs-lookup"><span data-stu-id="33f33-256">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="33f33-257">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-257">**New behavior**</span></span>

<span data-ttu-id="33f33-258">3\.0 以降は .NET SDK に `dotnet ef` ツールが含まれないため、これを使用するにはローカルまたはグローバルなツールとして明示的にインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-258">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="33f33-259">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-259">**Why**</span></span>

<span data-ttu-id="33f33-260">この変更により、`dotnet ef` を通常の .NET CLI ツールとして NuGet 上で配信したり更新したりできるようになります。これは EF Core 3.0 も常に NuGet パッケージとして配信されるという事実と一致します。</span><span class="sxs-lookup"><span data-stu-id="33f33-260">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="33f33-261">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-261">**Mitigations**</span></span>

<span data-ttu-id="33f33-262">移行の管理や `DbContext` のスキャフォールディングを行えるようにするには、`dotnet-ef` をグローバル ツールとしてインストールします。</span><span class="sxs-lookup"><span data-stu-id="33f33-262">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

  ``` console
    $ dotnet tool install --global dotnet-ef --version 3.0.0-*
  ```

<span data-ttu-id="33f33-263">[ツール マニフェスト ファイル](https://github.com/dotnet/cli/issues/10288)を使用してツールの依存関係として宣言するプロジェクトの依存関係を復元するときに、ローカルなツールとして取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="33f33-263">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](https://github.com/dotnet/cli/issues/10288).</span></span>

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="33f33-264">FromSql、ExecuteSql、および ExecuteSqlAsync の名前変更</span><span class="sxs-lookup"><span data-stu-id="33f33-264">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="33f33-265">問題 #10996 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-265">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="33f33-266">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-266">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-267">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-267">**Old behavior**</span></span>

<span data-ttu-id="33f33-268">EF Core 3.0 より前のバージョンでは、通常の文字列または SQL およびパラメーターに挿入する必要がある文字列で使用するために、これらのメソッド名がオーバーロードされました。</span><span class="sxs-lookup"><span data-stu-id="33f33-268">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="33f33-269">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-269">**New behavior**</span></span>

<span data-ttu-id="33f33-270">EF Core 3.0 以降では、`FromSqlRaw`、`ExecuteSqlRaw`、および `ExecuteSqlRawAsync` を使用して、パラメーターがクエリ文字列とは別に渡される、パラメーター化クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-270">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="33f33-271">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-271">For example:</span></span>

```C#
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="33f33-272">`FromSqlInterpolated`、`ExecuteSqlInterpolated`、および `ExecuteSqlInterpolatedAsync` を使用して、パラメーターが挿入クエリ文字列の一部として渡されるパラメーター化クエリを作成します。</span><span class="sxs-lookup"><span data-stu-id="33f33-272">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="33f33-273">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-273">For example:</span></span>

```C#
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="33f33-274">上記のクエリはどちらも、同じ SQL パラメーターを持つ同じパラメーター化 SQL が生成されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="33f33-274">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="33f33-275">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-275">**Why**</span></span>

<span data-ttu-id="33f33-276">このようなメソッド オーバーロードは、挿入文字列メソッドを呼び出すつもりが、誤って raw 文字列メソッドを非常に簡単に呼び出せてしまいます。その逆も同様です。</span><span class="sxs-lookup"><span data-stu-id="33f33-276">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="33f33-277">これは、クエリをパラメーター化する必要があるときに、パラメーター化されない結果になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-277">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="33f33-278">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-278">**Mitigations**</span></span>

<span data-ttu-id="33f33-279">新しいメソッド名を使用するように切り替えます。</span><span class="sxs-lookup"><span data-stu-id="33f33-279">Switch to use the new method names.</span></span>

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="33f33-280">FromSql メソッドはクエリのルートでのみ指定できる</span><span class="sxs-lookup"><span data-stu-id="33f33-280">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="33f33-281">問題 #15704 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-281">Tracking Issue #15704</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

<span data-ttu-id="33f33-282">この変更は、EF Core 3.0 プレビュー 6 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-282">This change is introduced in EF Core 3.0-preview 6.</span></span>

<span data-ttu-id="33f33-283">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-283">**Old behavior**</span></span>

<span data-ttu-id="33f33-284">EF Core 3.0 以前は、クエリ内の任意の場所で `FromSql` メソッドを指定できました。</span><span class="sxs-lookup"><span data-stu-id="33f33-284">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="33f33-285">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-285">**New behavior**</span></span>

<span data-ttu-id="33f33-286">EF Core 3.0 以降、新しい `FromSqlRaw` および `FromSqlInterpolated` メソッド (`FromSql` の置き換え) は、クエリのルート上でのみ (つまり `DbSet<>` で直接) 指定できます。</span><span class="sxs-lookup"><span data-stu-id="33f33-286">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="33f33-287">他の場所でそれらを指定しようとすると、コンパイル エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="33f33-287">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="33f33-288">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-288">**Why**</span></span>

<span data-ttu-id="33f33-289">`DbSet` 以外の任意の場所で `FromSql` を指定しても、さらなる意味や価値が追加されることはなく、特定のシナリオではあいまいさの原因となる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-289">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="33f33-290">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-290">**Mitigations**</span></span>

<span data-ttu-id="33f33-291">`FromSql` の呼び出し場所を移動して、それらが適用される `DbSet` 上で直接実行されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-291">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="33f33-292">追跡なしのクエリでは、識別子の解決が実行されなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-292">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="33f33-293">問題 #13518 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-293">Tracking Issue #13518</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

<span data-ttu-id="33f33-294">この変更は、EF Core 3.0 プレビュー 6 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-294">This change is introduced in EF Core 3.0-preview 6.</span></span>

<span data-ttu-id="33f33-295">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-295">**Old behavior**</span></span>

<span data-ttu-id="33f33-296">EF Core 3.0 以前の場合は、指定した型と ID を持つエンティティが出現するたびに同じエンティティ インスタンスが使用されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-296">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="33f33-297">これは、追跡クエリの動作と一致します。</span><span class="sxs-lookup"><span data-stu-id="33f33-297">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="33f33-298">次のクエリを例にします。</span><span class="sxs-lookup"><span data-stu-id="33f33-298">For example, this query:</span></span>

```C#
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
<span data-ttu-id="33f33-299">指定したカテゴリに関連付けられている各 `Category` に対して同じ `Product` インスタンスが返されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-299">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

<span data-ttu-id="33f33-300">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-300">**New behavior**</span></span>

<span data-ttu-id="33f33-301">EF Core 3.0 以降では、指定した型と ID を持つエンティティが、返されたグラフ内の異なる場所で検出されると、それぞれ異なるエンティティ インスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-301">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="33f33-302">たとえば、上記のクエリでは、2 つの製品が同じカテゴリに関連付けられている場合でも、各 `Product` に対して新しい `Category` インスタンスが返されるようになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-302">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

<span data-ttu-id="33f33-303">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-303">**Why**</span></span>

<span data-ttu-id="33f33-304">識別子の解決 (つまり、エンティティの型と ID が以前に検出されたエンティティと同じであることを確認する) を行う場合は、パフォーマンスおよびメモリのオーバーヘッドが増大します。</span><span class="sxs-lookup"><span data-stu-id="33f33-304">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="33f33-305">これは、通常、最初の場所で追跡なしのクエリが使用されている理由に矛盾しています。</span><span class="sxs-lookup"><span data-stu-id="33f33-305">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="33f33-306">また、識別子の解決は有用なこともありますが、エンティティをシリアル化してクライアントに送信する場合 (追跡なしのクエリでは一般的な処理) は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="33f33-306">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

<span data-ttu-id="33f33-307">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-307">**Mitigations**</span></span>

<span data-ttu-id="33f33-308">識別子の解決が必要な場合は、追跡クエリを使用します。</span><span class="sxs-lookup"><span data-stu-id="33f33-308">Use a tracking query if identity resolution is required.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="33f33-309">~~クエリの実行がデバッグ レベルでログに記録される~~ 元に戻されます</span><span class="sxs-lookup"><span data-stu-id="33f33-309">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="33f33-310">問題 #14523 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-310">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="33f33-311">この変更は、EF Core 3.0 プレビュー 7 で元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-311">This change is reverted in EF Core 3.0-preview 7.</span></span>

<span data-ttu-id="33f33-312">EF Core 3.0 の新しい構成では、あらゆるイベントのログ レベルをアプリケーションによって指定できるため、この変更は元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-312">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="33f33-313">たとえば、SQL のログ記録を `Debug` に切り替えるには、`OnConfiguring` または `AddDbContext` で明示的にレベルを構成します。</span><span class="sxs-lookup"><span data-stu-id="33f33-313">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="33f33-314">一時キーの値がエンティティ インスタンスに設定されなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-314">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="33f33-315">問題 #12378 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-315">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="33f33-316">この変更は、EF Core 3.0 プレビュー 2 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-316">This change is introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="33f33-317">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-317">**Old behavior**</span></span>

<span data-ttu-id="33f33-318">EF Core 3.0 より前では、後で実際の値がデータベースによって生成される、すべてのキー プロパティに一時的な値が割り当てられていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-318">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="33f33-319">通常、これらの一時的な値は大きい負の数値でした。</span><span class="sxs-lookup"><span data-stu-id="33f33-319">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="33f33-320">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-320">**New behavior**</span></span>

<span data-ttu-id="33f33-321">3\.0 以降では、EF Core で、エンティティの追跡情報の一部として一時キーの値が格納され、キー プロパティ自体はそのままになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-321">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="33f33-322">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-322">**Why**</span></span>

<span data-ttu-id="33f33-323">この変更は、何らかの `DbContext` インスタンスによって以前に追跡されたエンティティが、別の `DbContext` インスタンスに移動されるときに、一時キーの値が誤って永続的なものにならないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-323">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="33f33-324">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-324">**Mitigations**</span></span>

<span data-ttu-id="33f33-325">主キーの値を外部キーに割り当てて、エンティティ間の関連付けを形成するアプリケーションは、主キーがストアで生成されており、`Added` 状態のエンティティに属している場合、以前の動作に依存する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-325">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="33f33-326">これは、次のようにして回避できます。</span><span class="sxs-lookup"><span data-stu-id="33f33-326">This can be avoided by:</span></span>
* <span data-ttu-id="33f33-327">ストア生成キーを使用しない。</span><span class="sxs-lookup"><span data-stu-id="33f33-327">Not using store-generated keys.</span></span>
* <span data-ttu-id="33f33-328">外部キーの値を設定するのではなく、ナビゲーション プロパティを設定してリレーションシップを形成する。</span><span class="sxs-lookup"><span data-stu-id="33f33-328">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="33f33-329">エンティティの追跡情報から実際の一時キーの値を取得する。</span><span class="sxs-lookup"><span data-stu-id="33f33-329">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="33f33-330">たとえば、`context.Entry(blog).Property(e => e.Id).CurrentValue` では、`blog.Id` 自体が設定されていない場合でも、一時的な値が返されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-330">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="33f33-331">DetectChanges でストア生成キーの値が優先される</span><span class="sxs-lookup"><span data-stu-id="33f33-331">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="33f33-332">問題 #14616 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-332">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="33f33-333">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-333">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="33f33-334">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-334">**Old behavior**</span></span>

<span data-ttu-id="33f33-335">EF Core 3.0 より前では、`DetectChanges` によって検出された追跡対象外のエンティティが `Added` 状態で追跡され、`SaveChanges` が呼び出されたときに新しい行として挿入されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-335">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="33f33-336">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-336">**New behavior**</span></span>

<span data-ttu-id="33f33-337">EF Core 3.0 以降では、エンティティで生成されたキーの値を使用し、何らかのキーの値が設定されている場合、そのエンティティは `Modified` 状態で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-337">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="33f33-338">つまり、エンティティの行が存在していると見なされ、`SaveChanges` の呼び出し時に更新されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-338">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="33f33-339">キーの値が設定されていないか、エンティティ型で生成されたキーが使用されない場合、新しいエンティティは引き続き、以前のバージョンと同様に `Added` として追跡されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-339">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="33f33-340">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-340">**Why**</span></span>

<span data-ttu-id="33f33-341">この変更は、ストア生成キーの使用中に、切り離されたエンティティ グラフをより簡単に一貫して操作できるようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-341">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="33f33-342">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-342">**Mitigations**</span></span>

<span data-ttu-id="33f33-343">エンティティ型が生成されたキーを使用するように構成されているものの、キーの値が新しいインスタンスに対して明示的に設定されている場合、この変更によってアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-343">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="33f33-344">この問題を解決するには、キー プロパティで生成された値が使用されないように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="33f33-344">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="33f33-345">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-345">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="33f33-346">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-346">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="33f33-347">既定で連鎖削除がすぐに行われるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-347">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="33f33-348">問題 #10114 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-348">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="33f33-349">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-349">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="33f33-350">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-350">**Old behavior**</span></span>

<span data-ttu-id="33f33-351">3\.0 より前では、EF Core で適用された連鎖操作 (必要なプリンシパルが削除されたか、必要なプリンシパルへのリレーションシップが切断されたときに依存エンティティを削除する) は、SaveChanges が呼び出されるまで行われませんでした。</span><span class="sxs-lookup"><span data-stu-id="33f33-351">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="33f33-352">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-352">**New behavior**</span></span>

<span data-ttu-id="33f33-353">3\.0 以降では、EF Core で、トリガー条件が検出されるとすぐに連鎖操作が適用されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-353">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="33f33-354">たとえば、プリンシパル エンティティを削除するために `context.Remove()` を呼び出すと、すべての追跡対象の関連する必要な依存関係もすぐに `Deleted` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-354">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="33f33-355">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-355">**Why**</span></span>

<span data-ttu-id="33f33-356">この変更は、`SaveChanges` が呼び出される_前に_どのエンティティが削除されるかを把握することが重要である、データ バインディングおよび監査シナリオのエクスペリエンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-356">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="33f33-357">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-357">**Mitigations**</span></span>

<span data-ttu-id="33f33-358">`context.ChangedTracker` の設定を使用して、以前の動作を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="33f33-358">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="33f33-359">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-359">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="33f33-360">DeleteBehavior.Restrict のセマンティクスがクリーンになりました</span><span class="sxs-lookup"><span data-stu-id="33f33-360">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="33f33-361">問題 #12661 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-361">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="33f33-362">この変更は、EF Core 3.0 プレビュー 5 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-362">This change is introduced in EF Core 3.0-preview 5.</span></span>

<span data-ttu-id="33f33-363">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-363">**Old behavior**</span></span>

<span data-ttu-id="33f33-364">3\.0 以前は、`DeleteBehavior.Restrict` により `Restrict` セマンティクスでデータベースに外部キーが作成されましたが、内部の修正がどのように変更されたのかがはっきりしませんでした。</span><span class="sxs-lookup"><span data-stu-id="33f33-364">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="33f33-365">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-365">**New behavior**</span></span>

<span data-ttu-id="33f33-366">3\.0 以降では、`DeleteBehavior.Restrict` により `Restrict` セマンティクスで外部キーが作成されます。つまり、カスケードがありません。EF 内部修正には影響を出さず、制約違反で例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="33f33-366">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="33f33-367">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-367">**Why**</span></span>

<span data-ttu-id="33f33-368">この変更は、副作用を出さず、直観的に `DeleteBehavior` を使用するために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-368">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="33f33-369">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-369">**Mitigations**</span></span>

<span data-ttu-id="33f33-370">`DeleteBehavior.ClientNoAction` を使用し、以前の動作を復元できます。</span><span class="sxs-lookup"><span data-stu-id="33f33-370">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="33f33-371">クエリ型がエンティティ型と統合される</span><span class="sxs-lookup"><span data-stu-id="33f33-371">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="33f33-372">問題 #14194 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-372">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="33f33-373">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-373">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="33f33-374">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-374">**Old behavior**</span></span>

<span data-ttu-id="33f33-375">EF Core 3.0 より前では、[クエリ型](xref:core/modeling/query-types)は、構造化された方法で主キーが定義されないデータのクエリを実行するための手段でした。</span><span class="sxs-lookup"><span data-stu-id="33f33-375">Before EF Core 3.0, [query types](xref:core/modeling/query-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="33f33-376">つまり、クエリ型は、キーが利用できるときに (テーブルからの可能性が高くなりますが、ビューからの可能性もあります) 通常のエンティティ型が使用された場合に、キーなしの (ビューからの可能性が高くなりますが、テーブルからの可能性もあります) エンティティ型をマップするために使用されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-376">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="33f33-377">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-377">**New behavior**</span></span>

<span data-ttu-id="33f33-378">クエリ型は、現在、主キーなしの単なるエンティティ型になります。</span><span class="sxs-lookup"><span data-stu-id="33f33-378">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="33f33-379">キーなしのエンティティ型の機能は、以前のバージョンのクエリ型と同じです。</span><span class="sxs-lookup"><span data-stu-id="33f33-379">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="33f33-380">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-380">**Why**</span></span>

<span data-ttu-id="33f33-381">この変更は、クエリ型の目的に関する混乱を減らすために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-381">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="33f33-382">つまり、これらはキーなしのエンティティ型であるため、本質的には読み取り専用となりますが、単にエンティティ型を読み取り専用にする必要があるという理由で使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="33f33-382">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="33f33-383">同様に、これらは多くの場合、ビューにマップされますが、これは、単にビューでは多くの場合、キーが定義されないためです。</span><span class="sxs-lookup"><span data-stu-id="33f33-383">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="33f33-384">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-384">**Mitigations**</span></span>

<span data-ttu-id="33f33-385">API の以下の部分は使用されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-385">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="33f33-386">**`ModelBuilder.Query<>()`** - 代わりに `ModelBuilder.Entity<>().HasNoKey()` を呼び出して、エンティティ型をキーなしとしてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-386">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="33f33-387">これは、規則により、主キーは必要であるが、規則と一致しない場合に構成エラーを回避するようにはまだ構成されません。</span><span class="sxs-lookup"><span data-stu-id="33f33-387">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="33f33-388">**`DbQuery<>`** - 代わりに `DbSet<>` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-388">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="33f33-389">**`DbContext.Query<>()`** - 代わりに `DbContext.Set<>()` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-389">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="33f33-390">所有型のリレーションシップ用の構成 API が変更された</span><span class="sxs-lookup"><span data-stu-id="33f33-390">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="33f33-391">[問題 #12444 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[問題 #9148 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[問題 #14153 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="33f33-391">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="33f33-392">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-392">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="33f33-393">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-393">**Old behavior**</span></span>

<span data-ttu-id="33f33-394">EF Core 3.0 より前では、所有リレーションシップの構成は、`OwnsOne` または `OwnsMany` の呼び出し直後に実行されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-394">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="33f33-395">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-395">**New behavior**</span></span>

<span data-ttu-id="33f33-396">EF Core 3.0 以降では、fluent API で、`WithOwner()` を使用して、所有者に対してナビゲーション プロパティが構成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-396">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="33f33-397">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-397">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="33f33-398">現在、所有者と所有型間のリレーションシップに関する構成は、他のリレーションシップの構成方法と同様、`WithOwner()` 後にチェーンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-398">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="33f33-399">一方、所有型自体の構成は引き続き、`OwnsOne()/OwnsMany()` 後にチェーンされます。</span><span class="sxs-lookup"><span data-stu-id="33f33-399">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="33f33-400">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-400">For example:</span></span>

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

<span data-ttu-id="33f33-401">さらに所有型ターゲットでの `Entity()`、`HasOne()`、または `Set()` の呼び出しでは、例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-401">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="33f33-402">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-402">**Why**</span></span>

<span data-ttu-id="33f33-403">この変更は、所有型自体と、所有型_へのリレーションシップ_の構成を明確に分離するために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-403">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="33f33-404">これにより、`HasForeignKey` のようなメソッドに関するあいまいさと混乱はなくなります。</span><span class="sxs-lookup"><span data-stu-id="33f33-404">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="33f33-405">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-405">**Mitigations**</span></span>

<span data-ttu-id="33f33-406">上記の例で示すように、新しい API サーフェスを使用するように、所有型リレーションシップの構成を変更します。</span><span class="sxs-lookup"><span data-stu-id="33f33-406">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="33f33-407">プリンシパルとテーブルを共有する依存エンティティが省略可能になりました</span><span class="sxs-lookup"><span data-stu-id="33f33-407">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="33f33-408">問題 #9005 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-408">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="33f33-409">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-409">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-410">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-410">**Old behavior**</span></span>

<span data-ttu-id="33f33-411">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="33f33-411">Consider the following model:</span></span>
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
<span data-ttu-id="33f33-412">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、新しい `Order` を追加する場合には常に `OrderDetails` インスタンスが必要でした。</span><span class="sxs-lookup"><span data-stu-id="33f33-412">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="33f33-413">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-413">**New behavior**</span></span>

<span data-ttu-id="33f33-414">EF Core 3.0 以降では、`OrderDetails` なしで `Order` を追加することができ、主キー以外のすべての `OrderDetails` プロパティが NULL 値が許可される列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="33f33-414">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="33f33-415">`OrderDetails` は、必要なプロパティのいずれにも値がない場合、または主キー以外に必要なプロパティがなく、すべてのプロパティが `null` の場合、EF Core のクエリの実行時に `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-415">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="33f33-416">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-416">**Mitigations**</span></span>

<span data-ttu-id="33f33-417">モデルに省略可能なすべての列と依存関係を共有するテーブルがあるが、それを指すナビゲーションが `null` になることが想定されていない場合は、ナビゲーションが `null` の場合のケースを処理するようにアプリケーションを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-417">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="33f33-418">これができない場合は、必要なプロパティをエンティティ型に追加するか、少なくとも 1 つのプロパティに割り当てられている値が `null` 以外である必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-418">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="33f33-419">同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-419">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="33f33-420">問題 #14154 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-420">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="33f33-421">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-421">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-422">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-422">**Old behavior**</span></span>

<span data-ttu-id="33f33-423">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="33f33-423">Consider the following model:</span></span>
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
<span data-ttu-id="33f33-424">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、`OrderDetails` だけを更新してもクライアント上の `Version` 値は更新されず、次回の更新が失敗します。</span><span class="sxs-lookup"><span data-stu-id="33f33-424">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="33f33-425">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-425">**New behavior**</span></span>

<span data-ttu-id="33f33-426">EF Core 3.0 以降では、新しい `Version` 値が `OrderDetails` を所有している場合には `Order` に伝達されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-426">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="33f33-427">それ以外の場合は、モデルの検証中に例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="33f33-427">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="33f33-428">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-428">**Why**</span></span>

<span data-ttu-id="33f33-429">この変更は、エンティティの 1 つだけが同じテーブルにマップされている場合に、古い同時実行トークン値が更新されるのを回避するために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-429">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="33f33-430">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-430">**Mitigations**</span></span>

<span data-ttu-id="33f33-431">テーブルを共有するすべてのエンティティには、同時実行トークン列にマップされるプロパティを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-431">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="33f33-432">シャドウ状態で作成することができます。</span><span class="sxs-lookup"><span data-stu-id="33f33-432">It's possible the create one in shadow-state:</span></span>
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="33f33-433">マップされていない型から継承されたプロパティは、すべての派生型の 1 つの列にマップされるようになりました</span><span class="sxs-lookup"><span data-stu-id="33f33-433">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="33f33-434">問題 #13998 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-434">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="33f33-435">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-435">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-436">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-436">**Old behavior**</span></span>

<span data-ttu-id="33f33-437">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="33f33-437">Consider the following model:</span></span>
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

<span data-ttu-id="33f33-438">EF Core 3.0 より前のバージョンでは、`ShippingAddress` プロパティは既定で `BulkOrder` と `Order` の個別の列にマップされていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-438">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="33f33-439">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-439">**New behavior**</span></span>

<span data-ttu-id="33f33-440">EF Core 3.0 以降では、`ShippingAddress` に対して 1 つの列だけが作成されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-440">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="33f33-441">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-441">**Why**</span></span>

<span data-ttu-id="33f33-442">以前の動作は予期しないものでした。</span><span class="sxs-lookup"><span data-stu-id="33f33-442">The old behavoir was unexpected.</span></span>

<span data-ttu-id="33f33-443">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-443">**Mitigations**</span></span>

<span data-ttu-id="33f33-444">プロパティは、派生型の個別の列に引き続き明示的にマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="33f33-444">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="33f33-445">外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-445">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="33f33-446">問題 #13274 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-446">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="33f33-447">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-447">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="33f33-448">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-448">**Old behavior**</span></span>

<span data-ttu-id="33f33-449">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="33f33-449">Consider the following model:</span></span>
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
<span data-ttu-id="33f33-450">EF Core 3.0 より前では、規則により、外部キーで `CustomerId` プロパティが使用されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-450">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="33f33-451">しかし、`Order` が所有型である場合、`CustomerId` も主キーとマークされ、これは通常、期待されることではありません。</span><span class="sxs-lookup"><span data-stu-id="33f33-451">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="33f33-452">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-452">**New behavior**</span></span>

<span data-ttu-id="33f33-453">EF Core 3.0 以降では、プリンシパル プロパティと同じ名前である場合、規則により、外部キーに対するプロパティの使用は試行されません。</span><span class="sxs-lookup"><span data-stu-id="33f33-453">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="33f33-454">プリンシパル プロパティ名と連結されたプリンシパル型名、およびプリンシパル プロパティ名パターンと連結されたナビゲーション名は、引き続き一致します。</span><span class="sxs-lookup"><span data-stu-id="33f33-454">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="33f33-455">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-455">For example:</span></span>

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

<span data-ttu-id="33f33-456">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-456">**Why**</span></span>

<span data-ttu-id="33f33-457">この変更は、所有型に主キー プロパティが誤って定義されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-457">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="33f33-458">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-458">**Mitigations**</span></span>

<span data-ttu-id="33f33-459">プロパティを外部キーにする、したがって、主キーの一部にする予定だった場合は、そのように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="33f33-459">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="33f33-460">データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになりました</span><span class="sxs-lookup"><span data-stu-id="33f33-460">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="33f33-461">問題 #14218 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-461">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="33f33-462">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-462">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-463">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-463">**Old behavior**</span></span>

<span data-ttu-id="33f33-464">EF Core 3.0 より前のバージョンでは、コンテキストにより `TransactionScope` 内部で接続が開かれると、現在の `TransactionScope` がアクティブの間、接続が開いたままになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-464">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

<span data-ttu-id="33f33-465">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-465">**New behavior**</span></span>

<span data-ttu-id="33f33-466">EF Core 3.0 以降では、使用が終了したらすぐに接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="33f33-466">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="33f33-467">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-467">**Why**</span></span>

<span data-ttu-id="33f33-468">この変更により、同じ `TransactionScope` で複数のコンテキストを使用できます。</span><span class="sxs-lookup"><span data-stu-id="33f33-468">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="33f33-469">新しい動作は、EF6 にも一致します。</span><span class="sxs-lookup"><span data-stu-id="33f33-469">The new behavior also matches EF6.</span></span>

<span data-ttu-id="33f33-470">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-470">**Mitigations**</span></span>

<span data-ttu-id="33f33-471">接続を開いたままにする必要がある場合は、`OpenConnection()` を明示的に呼び出して、EF Core が途中で閉じないようにします。</span><span class="sxs-lookup"><span data-stu-id="33f33-471">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="33f33-472">各プロパティで独立したメモリ内整数キー生成が使用される</span><span class="sxs-lookup"><span data-stu-id="33f33-472">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="33f33-473">問題 #6872 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-473">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="33f33-474">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-474">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-475">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-475">**Old behavior**</span></span>

<span data-ttu-id="33f33-476">EF Core 3.0 より前では、1 つの共有値ジェネレーターが、すべてのメモリ内整数キー プロパティで使用されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-476">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="33f33-477">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-477">**New behavior**</span></span>

<span data-ttu-id="33f33-478">EF Core 3.0 以降では、各整数キー プロパティで、メモリ内データベースを使用するときに独自の値ジェネレーターが取得されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-478">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="33f33-479">また、データベースが削除された場合は、すべてのテーブルに対してキーの生成がリセットされます。</span><span class="sxs-lookup"><span data-stu-id="33f33-479">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="33f33-480">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-480">**Why**</span></span>

<span data-ttu-id="33f33-481">この変更は、実際のデータベース キー生成により近くなるようにメモリ内キー生成を調整するため、また、メモリ内データベースを使用するときに互いのテストを分離させる機能を向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-481">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="33f33-482">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-482">**Mitigations**</span></span>

<span data-ttu-id="33f33-483">これにより、設定される特定のメモリ内キー値に依存しているアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-483">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="33f33-484">代わりに特定のキーの値に依存しないようにするか、あるいは新しい動作と一致するように更新することを検討します。</span><span class="sxs-lookup"><span data-stu-id="33f33-484">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="33f33-485">バッキング フィールドが既定で使用される</span><span class="sxs-lookup"><span data-stu-id="33f33-485">Backing fields are used by default</span></span>

[<span data-ttu-id="33f33-486">問題 #12430 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-486">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="33f33-487">この変更は、EF Core 3.0 プレビュー 2 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-487">This change is introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="33f33-488">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-488">**Old behavior**</span></span>

<span data-ttu-id="33f33-489">3\.0 より前では、プロパティのバッキング フィールドがわかっていた場合でも、EF Core では引き続き、既定でプロパティの getter および setter メソッドを使用して、プロパティ値の読み取りと書き込みが行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-489">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="33f33-490">ただし、バッキング フィールドが直接設定されることがわかっている場合のクエリの実行は例外です。</span><span class="sxs-lookup"><span data-stu-id="33f33-490">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="33f33-491">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-491">**New behavior**</span></span>

<span data-ttu-id="33f33-492">EF Core 3.0 以降では、プロパティのバッキング フィールドがわかっている場合、バッキング フィールドを使用して、常に EF Core がそのプロパティの読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="33f33-492">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="33f33-493">アプリケーションが getter および setter メソッドにコード化された追加動作に依存している場合、これによりアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-493">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="33f33-494">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-494">**Why**</span></span>

<span data-ttu-id="33f33-495">この変更は、EF Core が、エンティティに関するデータベース操作の実行時に、既定でビジネス ロジックを誤ってトリガーしないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-495">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="33f33-496">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-496">**Mitigations**</span></span>

<span data-ttu-id="33f33-497">3\.0 より前の動作は、`ModelBuilder` のプロパティ アクセス モードの構成を通じて復元できます。</span><span class="sxs-lookup"><span data-stu-id="33f33-497">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="33f33-498">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-498">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="33f33-499">複数の互換性があるバッキング フィールドが見つかった場合にスローされる</span><span class="sxs-lookup"><span data-stu-id="33f33-499">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="33f33-500">問題 #12523 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-500">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="33f33-501">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-501">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-502">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-502">**Old behavior**</span></span>

<span data-ttu-id="33f33-503">EF Core 3.0 より前では、複数のフィールドが、プロパティのバッキング フィールドを見つけるための規則と一致した場合、何らかの優先順位に基づいて、1 つのフィールドが選択されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-503">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="33f33-504">これにより、あいまいなケースで不適切なフィールドが使用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-504">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="33f33-505">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-505">**New behavior**</span></span>

<span data-ttu-id="33f33-506">EF Core 3.0 以降では、複数のフィールドが同じプロパティと一致した場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="33f33-506">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="33f33-507">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-507">**Why**</span></span>

<span data-ttu-id="33f33-508">この変更は、1 つのフィールドのみが適切である可能性がある場合に、他のものより優先して暗黙的に 1 つのフィールドが使用されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-508">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="33f33-509">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-509">**Mitigations**</span></span>

<span data-ttu-id="33f33-510">あいまいなバッキング フィールドがあるプロパティでは、使用するフィールドを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-510">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="33f33-511">たとえば、fluent API を使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-511">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="33f33-512">フィールド専用プロパティの名前はフィールドの名前に一致する必要があります</span><span class="sxs-lookup"><span data-stu-id="33f33-512">Field-only property names should match the field name</span></span>

<span data-ttu-id="33f33-513">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-513">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-514">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-514">**Old behavior**</span></span>

<span data-ttu-id="33f33-515">EF Core 3.0 以前では、プロパティは文字列値により指定できました。CLR 型でその名前のプロパティが見つからなかった場合、EF Core では、一般的な規則を使ってそれとフィールドの照合が試行されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-515">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the CLR type then EF Core would try to match it to a field using convention rules.</span></span>
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

<span data-ttu-id="33f33-516">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-516">**New behavior**</span></span>

<span data-ttu-id="33f33-517">EF Core 3.0 以降では、フィールド専用プロパティはフィールドの名前に厳密に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-517">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="33f33-518">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-518">**Why**</span></span>

<span data-ttu-id="33f33-519">この変更は、同じような名前が付けられた 2 つのプロパティに同じフィールドが使用されるのを回避する目的で行われました。また、フィールド専用プロパティの照合規則を、CLR プロパティにマッピングされているプロパティの場合と同じにします。</span><span class="sxs-lookup"><span data-stu-id="33f33-519">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="33f33-520">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-520">**Mitigations**</span></span>

<span data-ttu-id="33f33-521">フィールド専用プロパティには、それがマッピングされるフィールドと同じ名前を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-521">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="33f33-522">EF Core 3.0 以降の今後のリリースでは、プロパティ名とは異なるフィールド名を明示的に構成できるように戻す予定です (問題 [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307) を参照)。</span><span class="sxs-lookup"><span data-stu-id="33f33-522">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="33f33-523">AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなりました</span><span class="sxs-lookup"><span data-stu-id="33f33-523">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="33f33-524">問題 #14756 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-524">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="33f33-525">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-525">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-526">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-526">**Old behavior**</span></span>

<span data-ttu-id="33f33-527">EF Core 3.0 以前では、`AddDbContext` または `AddDbContextPool` を呼び出すと、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) および [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) への呼び出しを通じて、D.I を使ってログ記録とメモリ キャッシュ サービスも登録されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-527">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with D.I through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="33f33-528">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-528">**New behavior**</span></span>

<span data-ttu-id="33f33-529">EF Core 3.0 以降、`AddDbContext` と `AddDbContextPool` では、依存関係の挿入 (DI) を使ってこれらのサービスを登録しなくなります。</span><span class="sxs-lookup"><span data-stu-id="33f33-529">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="33f33-530">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-530">**Why**</span></span>

<span data-ttu-id="33f33-531">EF Core 3.0 では、これらのサービスをアプリケーションの DI コンテナーに含める必要がありません。</span><span class="sxs-lookup"><span data-stu-id="33f33-531">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="33f33-532">ただし、`ILoggerFactory` がアプリケーションの DI コンテナーに登録された場合、それは引き続き EF Core によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-532">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="33f33-533">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-533">**Mitigations**</span></span>

<span data-ttu-id="33f33-534">ご自身のアプリケーションでこれらのサービスが必要な場合は、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) または [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) を使って、DI コンテナーで明示的にそれらを登録します。</span><span class="sxs-lookup"><span data-stu-id="33f33-534">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="33f33-535">DbContext.Entry でローカルの DetectChanges が実行されるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-535">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="33f33-536">問題 #13552 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-536">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="33f33-537">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-537">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="33f33-538">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-538">**Old behavior**</span></span>

<span data-ttu-id="33f33-539">EF Core 3.0 より前では、`DbContext.Entry` を呼び出すと、追跡対象のすべてのエンティティで変更が検出されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-539">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="33f33-540">これにより、`EntityEntry` で示された状態が確実に最新のものとなりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-540">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="33f33-541">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-541">**New behavior**</span></span>

<span data-ttu-id="33f33-542">EF Core 3.0 以降では、`DbContext.Entry` の呼び出しで、特定のエンティティと、それに関連する追跡対象のプリンシパル エンティティでの変更のみの検出が試行されるようになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-542">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="33f33-543">つまり、他の場所での変更は、このメソッドの呼び出しで検出されなかった可能性があり、アプリケーション状態に影響する場合があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-543">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="33f33-544">`ChangeTracker.AutoDetectChangesEnabled` が `false` に設定されている場合、このローカル変更の検出も無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="33f33-544">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="33f33-545">`ChangeTracker.Entries` や `SaveChanges` など、変更の検出の原因となる他のメソッドは引き続き、すべての追跡対象エンティティの完全な `DetectChanges` の原因となります。</span><span class="sxs-lookup"><span data-stu-id="33f33-545">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="33f33-546">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-546">**Why**</span></span>

<span data-ttu-id="33f33-547">この変更は、`context.Entry` を使用する既定のパフォーマンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-547">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="33f33-548">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-548">**Mitigations**</span></span>

<span data-ttu-id="33f33-549">3\.0 より前の動作を確保するには、`Entry` を呼び出す前に明示的に `ChgangeTracker.DetectChanges()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="33f33-549">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="33f33-550">文字列とバイト配列のキーが既定でクライアントによって生成されない</span><span class="sxs-lookup"><span data-stu-id="33f33-550">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="33f33-551">問題 #14617 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-551">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="33f33-552">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-552">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-553">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-553">**Old behavior**</span></span>

<span data-ttu-id="33f33-554">EF Core 3.0 より前では、`string` と `byte[]` のキー プロパティは、null 以外の値を明示的に設定しなくても使用できました。</span><span class="sxs-lookup"><span data-stu-id="33f33-554">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="33f33-555">このような場合、キーの値は GUID としてクライアントで生成され、`byte[]` のバイトにシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-555">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="33f33-556">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-556">**New behavior**</span></span>

<span data-ttu-id="33f33-557">EF Core 3.0 以降では、キー値が設定されていないことを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="33f33-557">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="33f33-558">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-558">**Why**</span></span>

<span data-ttu-id="33f33-559">この変更は、クライアントで生成された `string`/`byte[]` 値が一般的に役に立たず、既定の動作では一般的な方法で生成されたキー値について判断するのが難しくなったため、行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-559">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="33f33-560">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-560">**Mitigations**</span></span>

<span data-ttu-id="33f33-561">3\.0 より前の動作は、他の null 以外の値が設定されていない場合に、キー プロパティで生成された値を使用するように明示的に指定することで取得できます。</span><span class="sxs-lookup"><span data-stu-id="33f33-561">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="33f33-562">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-562">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="33f33-563">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-563">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="33f33-564">ILoggerFactory がスコープ サービスになった</span><span class="sxs-lookup"><span data-stu-id="33f33-564">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="33f33-565">問題 #14698 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-565">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="33f33-566">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-566">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="33f33-567">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-567">**Old behavior**</span></span>

<span data-ttu-id="33f33-568">EF Core 3.0 より前では、`ILoggerFactory` はシングルトン サービスとして登録されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-568">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="33f33-569">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-569">**New behavior**</span></span>

<span data-ttu-id="33f33-570">EF Core 3.0 以降では、`ILoggerFactory` がスコープ化されたものとして登録されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-570">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="33f33-571">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-571">**Why**</span></span>

<span data-ttu-id="33f33-572">この変更は、`DbContext` インスタンスでのロガーの関連付けを許可し、他の機能を有効にし、内部サービス プロバイダーの急増などの異常な動作の一部のケースをなくすために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-572">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="33f33-573">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-573">**Mitigations**</span></span>

<span data-ttu-id="33f33-574">この変更は、EF Core の内部サービス プロバイダーでカスタム サービスを登録して使用しない限り、アプリケーション コードに影響しないはずです。</span><span class="sxs-lookup"><span data-stu-id="33f33-574">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="33f33-575">これは一般的なことではありません。</span><span class="sxs-lookup"><span data-stu-id="33f33-575">This isn't common.</span></span>
<span data-ttu-id="33f33-576">このような場合、ほとんどのものが引き続き機能しますが、`ILoggerFactory` に依存していたシングルトン サービスは、異なる方法で `ILoggerFactory` を取得するように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-576">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="33f33-577">このような状況になった場合は、[EF Core GitHub の問題追跡ツール](https://github.com/aspnet/EntityFrameworkCore/issues)で問題を提出し、`ILoggerFactory` の使用方法をお知らせください。これにより、Microsoft では、今後、再びこのように中断しない方法についてよりよく理解できます。</span><span class="sxs-lookup"><span data-stu-id="33f33-577">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="33f33-578">遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-578">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="33f33-579">問題 #12780 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-579">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="33f33-580">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-580">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-581">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-581">**Old behavior**</span></span>

<span data-ttu-id="33f33-582">EF Core 3.0 より前では、`DbContext` が破棄されると、そのコンテキストから取得されたエンティティの特定のナビゲーション プロパティが完全に読み込まれたかどうかを知る方法はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="33f33-582">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="33f33-583">プロキシでは、代わりに、null 以外の値がある場合は参照ナビゲーションが読み込まれ、空でない場合はコレクション ナビゲーションが読み込まれたと見なされます。</span><span class="sxs-lookup"><span data-stu-id="33f33-583">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="33f33-584">このような場合、遅延読み込みを試みても操作なしとなります。</span><span class="sxs-lookup"><span data-stu-id="33f33-584">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="33f33-585">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-585">**New behavior**</span></span>

<span data-ttu-id="33f33-586">EF Core 3.0 以降では、プロキシで、ナビゲーション プロパティが読み込まれたかどうかの追跡が行われます。</span><span class="sxs-lookup"><span data-stu-id="33f33-586">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="33f33-587">つまり、コンテキストが破棄された後に読み込まれたナビゲーション プロパティにアクセスしようとしても、通常は操作なしとなります。読み込まれたナビゲーションが空でも null であっても同様です。</span><span class="sxs-lookup"><span data-stu-id="33f33-587">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="33f33-588">逆に、コンテキストが破棄された場合、読み込まれなかったナビゲーション プロパティにアクセスしようとすると例外がスローされます。ナビゲーション プロパティが空ではないコレクションである場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="33f33-588">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="33f33-589">このような状況が発生した場合、アプリケーション コードで無効な時間に遅延読み込みの使用が試行されていることを意味し、アプリケーションを変更して、このようにならないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-589">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="33f33-590">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-590">**Why**</span></span>

<span data-ttu-id="33f33-591">この変更は、破棄された `DbContext` インスタンスで遅延読み込みが試行されたときの動作を一貫した正しいものにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-591">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="33f33-592">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-592">**Mitigations**</span></span>

<span data-ttu-id="33f33-593">破棄されたコンテキストで遅延読み込みが試行されないようにアプリケーション コードを更新するか、例外メッセージの説明に従って、これを操作なしとなるように構成します。</span><span class="sxs-lookup"><span data-stu-id="33f33-593">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="33f33-594">内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-594">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="33f33-595">問題 #10236 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-595">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="33f33-596">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-596">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="33f33-597">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-597">**Old behavior**</span></span>

<span data-ttu-id="33f33-598">EF Core 3.0 より前では、異常な数の内部サービス プロバイダーを作成するアプリケーションに対する警告がログに記録されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-598">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="33f33-599">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-599">**New behavior**</span></span>

<span data-ttu-id="33f33-600">EF Core 3.0 以降では、この警告はエラーと見なされるようになり、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="33f33-600">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="33f33-601">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-601">**Why**</span></span>

<span data-ttu-id="33f33-602">この変更は、この異常なケースをより明示的に示すことで、アプリケーション コードの改善を促進するために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-602">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="33f33-603">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-603">**Mitigations**</span></span>

<span data-ttu-id="33f33-604">このエラーの発生時の最も適切なアクションは、根本原因を理解し、非常に多くの内部サービス プロバイダーを作成しないようにすることです。</span><span class="sxs-lookup"><span data-stu-id="33f33-604">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="33f33-605">しかし、`DbContextOptionsBuilder` での構成を使用して、エラーを警告に戻す (または無視する) ことがことができます。</span><span class="sxs-lookup"><span data-stu-id="33f33-605">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="33f33-606">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-606">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="33f33-607">1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作</span><span class="sxs-lookup"><span data-stu-id="33f33-607">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="33f33-608">問題 #9171 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-608">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="33f33-609">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-609">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-610">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-610">**Old behavior**</span></span>

<span data-ttu-id="33f33-611">EF Core 3.0 以前では、1 つの文字列と共に `HasOne` または `HasMany` が呼び出されるコードは、わかりにくい方法で解釈されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-611">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="33f33-612">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-612">For example:</span></span>
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="33f33-613">このコードは、プライベートである可能性がある `Entrance` ナビゲーション プロパティを使って、`Samurai` を他のエンティティ型に関連付けているように見えます。</span><span class="sxs-lookup"><span data-stu-id="33f33-613">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="33f33-614">実際には、このコードは、ナビゲーション プロパティなしで、`Entrance` と呼ばれるエンティティ型に対してリレーションシップを作成しようとしています。</span><span class="sxs-lookup"><span data-stu-id="33f33-614">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="33f33-615">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-615">**New behavior**</span></span>

<span data-ttu-id="33f33-616">EF Core 3.0 以降では、上記のコードは、前にそれが実行すべきであるように見えたことを実行するようになりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-616">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="33f33-617">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-617">**Why**</span></span>

<span data-ttu-id="33f33-618">以前の動作は、特に構成コードを読み取ってエラーを探す場合は、非常にわかりにくいものでした。</span><span class="sxs-lookup"><span data-stu-id="33f33-618">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="33f33-619">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-619">**Mitigations**</span></span>

<span data-ttu-id="33f33-620">これが中断させるのは、型名の文字列を使って明示的にリレーションシップを構成し、かつ明示的にナビゲーション プロパティを指定しないアプリケーションのみです。</span><span class="sxs-lookup"><span data-stu-id="33f33-620">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="33f33-621">これは一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="33f33-621">This is not common.</span></span>
<span data-ttu-id="33f33-622">前の動作は、ナビゲーション プロパティ名に対して明示的に `null` を渡すことで実現できます。</span><span class="sxs-lookup"><span data-stu-id="33f33-622">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="33f33-623">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-623">For example:</span></span>

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="33f33-624">いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更されました</span><span class="sxs-lookup"><span data-stu-id="33f33-624">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="33f33-625">問題 #15184 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-625">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="33f33-626">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-626">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-627">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-627">**Old behavior**</span></span>

<span data-ttu-id="33f33-628">次の非同期メソッドでは、以前は `Task<T>` が返されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-628">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="33f33-629">`ValueGenerator.NextValueAsync()` (および派生クラス)</span><span class="sxs-lookup"><span data-stu-id="33f33-629">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="33f33-630">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-630">**New behavior**</span></span>

<span data-ttu-id="33f33-631">前述のメソッドは、以前と同じ `T` に対して `ValueTask<T>` を返すようになりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-631">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="33f33-632">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-632">**Why**</span></span>

<span data-ttu-id="33f33-633">この変更により、これらのメソッドを呼び出すときに発生するヒープ割り当ての数を削減して全般的なパフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="33f33-633">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="33f33-634">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-634">**Mitigations**</span></span>

<span data-ttu-id="33f33-635">上記の API を待機しているだけのアプリケーションのみを再コンパイルする必要があります。ソースの変更は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="33f33-635">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="33f33-636">より複雑な使用方法 (返された `Task` を `Task.WhenAny()` に渡すなど) では通常、返された `ValueTask<T>` を `AsTask()` を呼び出すことによって `Task<T>` に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-636">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="33f33-637">これにより、この変更による割り当ての削減が無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="33f33-637">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="33f33-638">Relational:TypeMapping 注釈が単に TypeMapping となった</span><span class="sxs-lookup"><span data-stu-id="33f33-638">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="33f33-639">問題 #9913 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-639">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="33f33-640">この変更は、EF Core 3.0 プレビュー 2 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-640">This change is introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="33f33-641">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-641">**Old behavior**</span></span>

<span data-ttu-id="33f33-642">型マッピング注釈の注釈名は "Relational:TypeMapping" でした。</span><span class="sxs-lookup"><span data-stu-id="33f33-642">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="33f33-643">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-643">**New behavior**</span></span>

<span data-ttu-id="33f33-644">型マッピング注釈の注釈名は、"TypeMapping" となりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-644">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="33f33-645">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-645">**Why**</span></span>

<span data-ttu-id="33f33-646">型マッピングが、リレーショナル データベース プロバイダー以外でも使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-646">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="33f33-647">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-647">**Mitigations**</span></span>

<span data-ttu-id="33f33-648">これにより、一般的ではない、注釈として直接型マッピングにアクセスするアプリケーションのみが中断されるようになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-648">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="33f33-649">問題を解決するための最も適切なアクションは、注釈を直接使用するのではなく、API サーフェスを使用して型マッピングにアクセスすることです。</span><span class="sxs-lookup"><span data-stu-id="33f33-649">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="33f33-650">派生型の ToTable で例外がスローされる</span><span class="sxs-lookup"><span data-stu-id="33f33-650">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="33f33-651">問題 #11811 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-651">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="33f33-652">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-652">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="33f33-653">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-653">**Old behavior**</span></span>

<span data-ttu-id="33f33-654">EF Core 3.0 より前では、唯一の継承マッピング方法が TPH であり、これが有効でない場合には、派生型で呼び出された `ToTable()` が無視されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-654">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="33f33-655">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-655">**New behavior**</span></span>

<span data-ttu-id="33f33-656">EF Core 3.0 以降、および今後のリリースでの TPT と TPC のサポートの追加準備中には、派生型で呼び出された `ToTable()` で、今後の予期されないマッピング変更を回避するために例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-656">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="33f33-657">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-657">**Why**</span></span>

<span data-ttu-id="33f33-658">現在、派生型を別のテーブルにマップすることは有効ではありません。</span><span class="sxs-lookup"><span data-stu-id="33f33-658">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="33f33-659">この変更により、今後、これを行うことが有効になった場合に中断されなくなります。</span><span class="sxs-lookup"><span data-stu-id="33f33-659">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="33f33-660">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-660">**Mitigations**</span></span>

<span data-ttu-id="33f33-661">派生型を他のテーブルにマップしないようにします。</span><span class="sxs-lookup"><span data-stu-id="33f33-661">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="33f33-662">ForSqlServerHasIndex が HasIndex に置き換えられた</span><span class="sxs-lookup"><span data-stu-id="33f33-662">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="33f33-663">問題 #12366 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-663">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="33f33-664">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-664">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="33f33-665">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-665">**Old behavior**</span></span>

<span data-ttu-id="33f33-666">EF Core 3.0 より前では、`ForSqlServerHasIndex().ForSqlServerInclude()` によって、`INCLUDE` で使用される列を構成する方法が提供されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-666">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="33f33-667">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-667">**New behavior**</span></span>

<span data-ttu-id="33f33-668">EF Core 3.0 以降では、インデックスでの `Include` の使用が、リレーショナル レベルでサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-668">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="33f33-669">`HasIndex().ForSqlServerInclude()` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="33f33-669">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="33f33-670">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-670">**Why**</span></span>

<span data-ttu-id="33f33-671">この変更は、すべてのデータ プロバイダーのために `Include` でインデックス用の API を 1 か所に統合するために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-671">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="33f33-672">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-672">**Mitigations**</span></span>

<span data-ttu-id="33f33-673">上記のように、新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="33f33-673">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="33f33-674">メタデータ API の変更点</span><span class="sxs-lookup"><span data-stu-id="33f33-674">Metadata API changes</span></span>

[<span data-ttu-id="33f33-675">問題 #214 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-675">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="33f33-676">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-676">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-677">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-677">**New behavior**</span></span>

<span data-ttu-id="33f33-678">次のプロパティは拡張メソッドに変換されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-678">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="33f33-679">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-679">**Why**</span></span>

<span data-ttu-id="33f33-680">この変更により、前述のインターフェイスの実装が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="33f33-680">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="33f33-681">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-681">**Mitigations**</span></span>

<span data-ttu-id="33f33-682">新しい拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="33f33-682">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="33f33-683">プロバイダー固有のメタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="33f33-683">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="33f33-684">問題 #214 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-684">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="33f33-685">この変更は、EF Core 3.0 プレビュー 6 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-685">This change is introduced in EF Core 3.0-preview 6.</span></span>

<span data-ttu-id="33f33-686">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-686">**New behavior**</span></span>

<span data-ttu-id="33f33-687">プロバイダー固有の拡張メソッドがフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-687">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

<span data-ttu-id="33f33-688">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-688">**Why**</span></span>

<span data-ttu-id="33f33-689">この変更により、前述の拡張メソッドの実装が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="33f33-689">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="33f33-690">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-690">**Mitigations**</span></span>

<span data-ttu-id="33f33-691">新しい拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="33f33-691">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="33f33-692">EF Core で SQLite FK を適用するためのプラグマが送信されなくなった</span><span class="sxs-lookup"><span data-stu-id="33f33-692">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="33f33-693">問題 #12151 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-693">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="33f33-694">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-694">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="33f33-695">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-695">**Old behavior**</span></span>

<span data-ttu-id="33f33-696">EF Core 3.0 より前では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-696">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="33f33-697">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-697">**New behavior**</span></span>

<span data-ttu-id="33f33-698">EF Core 3.0 以降では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-698">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="33f33-699">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-699">**Why**</span></span>

<span data-ttu-id="33f33-700">この変更は、既定で EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用され、その後、FK の適用が既定で有効になり、接続が開かれるたびに明示的に有効にする必要がないため、行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-700">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="33f33-701">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-701">**Mitigations**</span></span>

<span data-ttu-id="33f33-702">外部キーは、EF Core に対して既定で使用される、SQLitePCLRaw.bundle_e_sqlite3 で既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="33f33-702">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="33f33-703">それ以外の場合は、接続文字列で `Foreign Keys=True` を指定することで、外部キーを有効にできます。</span><span class="sxs-lookup"><span data-stu-id="33f33-703">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="33f33-704">Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-704">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="33f33-705">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-705">**Old behavior**</span></span>

<span data-ttu-id="33f33-706">EF Core 3.0 より前では、EF Core では `SQLitePCLRaw.bundle_green` が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-706">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="33f33-707">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-707">**New behavior**</span></span>

<span data-ttu-id="33f33-708">EF Core 3.0 以降では、EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-708">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="33f33-709">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-709">**Why**</span></span>

<span data-ttu-id="33f33-710">この変更は、iOS 上で使用される SQLite のバージョンを、他のプラットフォームと一致させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="33f33-710">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="33f33-711">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-711">**Mitigations**</span></span>

<span data-ttu-id="33f33-712">iOS でネイティブの SQLite バージョンを使用するには、別の `SQLitePCLRaw` バンドルを使用するように `Microsoft.Data.Sqlite` を構成します。</span><span class="sxs-lookup"><span data-stu-id="33f33-712">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="33f33-713">GUID の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-713">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="33f33-714">問題 #15078 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-714">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="33f33-715">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-715">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-716">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-716">**Old behavior**</span></span>

<span data-ttu-id="33f33-717">GUID の値は、以前は SQLite に BLOB 値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-717">Guid values were previously stored as BLOB values on SQLite.</span></span>

<span data-ttu-id="33f33-718">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-718">**New behavior**</span></span>

<span data-ttu-id="33f33-719">GUID の値はテキストとして格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-719">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="33f33-720">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-720">**Why**</span></span>

<span data-ttu-id="33f33-721">GUID のバイナリ形式は標準化されていません。</span><span class="sxs-lookup"><span data-stu-id="33f33-721">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="33f33-722">値をテキストとして格納する方が、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="33f33-722">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="33f33-723">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-723">**Mitigations**</span></span>

<span data-ttu-id="33f33-724">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="33f33-724">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

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

<span data-ttu-id="33f33-725">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="33f33-725">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="33f33-726">Microsoft.Data.Sqlite は引き続き、BLOB とテキストの両方の列から GUID 値を読み取ることができます。ただし、パラメーターと定数の既定の形式が変更されているため、GUID が含まれる多くのシナリオではアクションが必要になります。</span><span class="sxs-lookup"><span data-stu-id="33f33-726">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="33f33-727">Char の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-727">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="33f33-728">問題 #15020 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-728">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="33f33-729">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-729">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-730">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-730">**Old behavior**</span></span>

<span data-ttu-id="33f33-731">Char の値は、以前は SQLite に整数値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-731">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="33f33-732">たとえば、Char の値 *A* は整数値 65 として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-732">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="33f33-733">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-733">**New behavior**</span></span>

<span data-ttu-id="33f33-734">Char の値はテキストとして格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-734">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="33f33-735">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-735">**Why**</span></span>

<span data-ttu-id="33f33-736">値をテキストとして格納する方が自然であり、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="33f33-736">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="33f33-737">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-737">**Mitigations**</span></span>

<span data-ttu-id="33f33-738">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="33f33-738">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="33f33-739">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="33f33-739">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="33f33-740">Microsoft.Data.Sqlite では、引き続き整数とテキストの両方の列の文字列値を読み取ることができるため、一部のシナリオではアクションが必要ありません。</span><span class="sxs-lookup"><span data-stu-id="33f33-740">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="33f33-741">移行 ID がインバリアント カルチャの暦を使用して生成されるようになった</span><span class="sxs-lookup"><span data-stu-id="33f33-741">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="33f33-742">問題 #12978 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-742">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="33f33-743">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-743">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-744">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-744">**Old behavior**</span></span>

<span data-ttu-id="33f33-745">移行 ID は、誤って現在のカルチャの暦を使用して生成されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-745">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="33f33-746">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-746">**New behavior**</span></span>

<span data-ttu-id="33f33-747">移行 ID がインバリアント カルチャの暦 (グレゴリオ暦) を使用して常に生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-747">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="33f33-748">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-748">**Why**</span></span>

<span data-ttu-id="33f33-749">データベースを更新するときやマージの競合を解決するときに、移行の順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="33f33-749">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="33f33-750">インバリアントの暦を使用することで、順序の問題が回避され、システムの予定表がチームのメンバーごとに違うことがなくなります。</span><span class="sxs-lookup"><span data-stu-id="33f33-750">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="33f33-751">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-751">**Mitigations**</span></span>

<span data-ttu-id="33f33-752">この変更は、1 年がグレゴリオ暦より長くなるグレゴリオ暦以外の暦 (タイ仏暦など) を使用しているすべてのユーザーに影響します。</span><span class="sxs-lookup"><span data-stu-id="33f33-752">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="33f33-753">既存の移行後に新しい移行順序が設定されるように、既存の移行 ID を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-753">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="33f33-754">移行 ID は、移行のデザイナー ファイルの移行属性にあります。</span><span class="sxs-lookup"><span data-stu-id="33f33-754">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="33f33-755">移行履歴テーブルも更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-755">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="33f33-756">UseRowNumberForPaging の削除</span><span class="sxs-lookup"><span data-stu-id="33f33-756">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="33f33-757">問題 #16400 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-757">Tracking Issue #16400</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

<span data-ttu-id="33f33-758">この変更は、EF Core 3.0 プレビュー 6 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-758">This change is introduced in EF Core 3.0-preview 6.</span></span>

<span data-ttu-id="33f33-759">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-759">**Old behavior**</span></span>

<span data-ttu-id="33f33-760">EF Core 3.0 より前では、`UseRowNumberForPaging` を使って、SQL Server 2008 と互換性のあるページング用の SQL を生成することができました。</span><span class="sxs-lookup"><span data-stu-id="33f33-760">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

<span data-ttu-id="33f33-761">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-761">**New behavior**</span></span>

<span data-ttu-id="33f33-762">EF Core 3.0 以降では、EF によって生成できるのは、それ以降のバージョンの SQL Server とのみ互換性があるページング用の SQL のみとなります。</span><span class="sxs-lookup"><span data-stu-id="33f33-762">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span> 

<span data-ttu-id="33f33-763">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-763">**Why**</span></span>

<span data-ttu-id="33f33-764">[SQL Server 2008 はもうサポートされていない製品であり](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/)、EF Core 3.0 で行われたクエリの変更に対応するようこの機能を更新する作業は負荷が大きいため、このような変更が行われます。</span><span class="sxs-lookup"><span data-stu-id="33f33-764">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

<span data-ttu-id="33f33-765">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-765">**Mitigations**</span></span>

<span data-ttu-id="33f33-766">生成された SQL がサポートされるように、より新しいバージョンの SQL Server に更新するか、より高い互換性レベルを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="33f33-766">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="33f33-767">ただし、これを実行できない場合は、詳細情報とともに[問題の追跡にコメント](https://github.com/aspnet/EntityFrameworkCore/issues/16400)してください。</span><span class="sxs-lookup"><span data-stu-id="33f33-767">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) with details.</span></span> <span data-ttu-id="33f33-768">Microsoft では、フィードバックに基づいてこの決定を再検討する場合があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-768">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="33f33-769">IDbContextOptionsExtension から拡張機能の情報/メタデータを削除</span><span class="sxs-lookup"><span data-stu-id="33f33-769">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="33f33-770">問題 #16119 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-770">Tracking Issue #16119</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

<span data-ttu-id="33f33-771">この変更は、EF Core 3.0 プレビュー 7 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-771">This change is introduced in EF Core 3.0-preview 7.</span></span>

<span data-ttu-id="33f33-772">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-772">**Old behavior**</span></span>

<span data-ttu-id="33f33-773">`IDbContextOptionsExtension` には、拡張機能に関するメタデータを提供するためのメソッドが含まれていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-773">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

<span data-ttu-id="33f33-774">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-774">**New behavior**</span></span>

<span data-ttu-id="33f33-775">これらのメソッドは、新しい抽象基底クラス `DbContextOptionsExtensionInfo` 上に移動されました。これは新しい `IDbContextOptionsExtension.Info` プロパティから返されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-775">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

<span data-ttu-id="33f33-776">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-776">**Why**</span></span>

<span data-ttu-id="33f33-777">2\.0 から 3.0 までのリリースを通して、これらのメソッドに追加または変更を行う必要が複数回発生しました。</span><span class="sxs-lookup"><span data-stu-id="33f33-777">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="33f33-778">それらを抜き出して新しい抽象基底クラスに含めることで、既存の拡張機能を損なうことなく、簡単にこのような変更を加えられるようになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-778">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

<span data-ttu-id="33f33-779">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-779">**Mitigations**</span></span>

<span data-ttu-id="33f33-780">新しいパターンに従うよう拡張機能を更新します。</span><span class="sxs-lookup"><span data-stu-id="33f33-780">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="33f33-781">その例は、EF Core のソース コードの、さまざまな種類の拡張機能に対する多数の `IDbContextOptionsExtension` の実装で見つかります。</span><span class="sxs-lookup"><span data-stu-id="33f33-781">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="33f33-782">LogQueryPossibleExceptionWithAggregateOperator の名前が変更された</span><span class="sxs-lookup"><span data-stu-id="33f33-782">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="33f33-783">問題 #10985 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-783">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="33f33-784">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-784">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-785">**変更**</span><span class="sxs-lookup"><span data-stu-id="33f33-785">**Change**</span></span>

<span data-ttu-id="33f33-786">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` の名前が `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-786">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="33f33-787">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-787">**Why**</span></span>

<span data-ttu-id="33f33-788">この警告イベントの名前が他のすべての警告イベントと照合されるためです。</span><span class="sxs-lookup"><span data-stu-id="33f33-788">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="33f33-789">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-789">**Mitigations**</span></span>

<span data-ttu-id="33f33-790">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="33f33-790">Use the new name.</span></span> <span data-ttu-id="33f33-791">(イベント ID 番号が変更されていないことを確認してください。)</span><span class="sxs-lookup"><span data-stu-id="33f33-791">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="33f33-792">外部キー制約名の API が明確化された</span><span class="sxs-lookup"><span data-stu-id="33f33-792">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="33f33-793">問題 #10730 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-793">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="33f33-794">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-794">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-795">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-795">**Old behavior**</span></span>

<span data-ttu-id="33f33-796">EF Core 3.0 以前は、外部キー制約名が単に "名前" と呼ばれていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-796">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="33f33-797">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-797">For example:</span></span>

```C#
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="33f33-798">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-798">**New behavior**</span></span>

<span data-ttu-id="33f33-799">EF Core 3.0 以降は、外部キー制約名が "制約名" と呼ばれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-799">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="33f33-800">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-800">For example:</span></span>

```C#
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="33f33-801">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-801">**Why**</span></span>

<span data-ttu-id="33f33-802">この変更により、この領域の名前付けに一貫性がもたらされます。また、それが外部キーが定義されている列やプロパティの名前ではなく、外部キー制約の名前であることが明確になりました。</span><span class="sxs-lookup"><span data-stu-id="33f33-802">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="33f33-803">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-803">**Mitigations**</span></span>

<span data-ttu-id="33f33-804">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="33f33-804">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="33f33-805">IRelationalDatabaseCreator.HasTables/HasTablesAsync をパブリックに変更</span><span class="sxs-lookup"><span data-stu-id="33f33-805">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="33f33-806">問題 #15997 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-806">Tracking Issue #15997</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

<span data-ttu-id="33f33-807">この変更は、EF Core 3.0 プレビュー 7 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-807">This change is introduced in EF Core 3.0-preview 7.</span></span>

<span data-ttu-id="33f33-808">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-808">**Old behavior**</span></span>

<span data-ttu-id="33f33-809">EF Core 3.0 より前では、これらのメソッドは保護されていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-809">Before EF Core 3.0, these methods were protected.</span></span>

<span data-ttu-id="33f33-810">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-810">**New behavior**</span></span>

<span data-ttu-id="33f33-811">EF Core 3.0 以降、これらのメソッドはパブリックになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-811">Starting with EF Core 3.0, these methods are public.</span></span>

<span data-ttu-id="33f33-812">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-812">**Why**</span></span>

<span data-ttu-id="33f33-813">これらのメソッドは、作成されたデータベースが空であるかどうかを判断するために EF によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-813">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="33f33-814">これは、EF の外部から、移行を適用するかどうか判断する場合にも役立ちます。</span><span class="sxs-lookup"><span data-stu-id="33f33-814">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

<span data-ttu-id="33f33-815">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-815">**Mitigations**</span></span>

<span data-ttu-id="33f33-816">すべてのオーバーライドのアクセシビリティを変更します。</span><span class="sxs-lookup"><span data-stu-id="33f33-816">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="33f33-817">Microsoft.EntityFrameworkCore.Design を DevelopmentDependency パッケージに変更</span><span class="sxs-lookup"><span data-stu-id="33f33-817">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="33f33-818">問題 #11506 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-818">Tracking Issue #11506</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

<span data-ttu-id="33f33-819">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-819">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="33f33-820">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-820">**Old behavior**</span></span>

<span data-ttu-id="33f33-821">EF Core 3.0 より前では、Microsoft.EntityFrameworkCore.Design は通常の NuGet パッケージであり、それに依存していたプロジェクトによってそのアセンブリを参照できました。</span><span class="sxs-lookup"><span data-stu-id="33f33-821">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

<span data-ttu-id="33f33-822">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-822">**New behavior**</span></span>

<span data-ttu-id="33f33-823">EF Core 3.0 以降、これは DevelopmentDependency パッケージになります。</span><span class="sxs-lookup"><span data-stu-id="33f33-823">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="33f33-824">つまり、依存関係が他のプロジェクトに推移的にフローすることがなくなり、規定ではそのアセンブリを参照できなくなります。</span><span class="sxs-lookup"><span data-stu-id="33f33-824">Which means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

<span data-ttu-id="33f33-825">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-825">**Why**</span></span>

<span data-ttu-id="33f33-826">このパッケージは、デザイン時に使用されることだけを想定しています。</span><span class="sxs-lookup"><span data-stu-id="33f33-826">This package is only intended to be used at design time.</span></span> <span data-ttu-id="33f33-827">デプロイされたアプリケーションからこれを参照すべきではありません。</span><span class="sxs-lookup"><span data-stu-id="33f33-827">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="33f33-828">パッケージを DevelopmentDependency にすることで、この推奨事項が強化されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-828">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

<span data-ttu-id="33f33-829">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-829">**Mitigations**</span></span>

<span data-ttu-id="33f33-830">EF Core のデザイン時の動作をオーバーライドするためにこのパッケージを参照する必要がある場合は、プロジェクトの PackageReference 項目メタデータを更新することができます。</span><span class="sxs-lookup"><span data-stu-id="33f33-830">If you need to reference this package to override EF Core's design-time behavior, you can update update PackageReference item metadata in your project.</span></span> <span data-ttu-id="33f33-831">このパッケージが Microsoft.EntityFrameworkCore.Tools 経由で推移的に参照されている場合は、パッケージに明示的な PackageReference を追加して、そのメタデータを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33f33-831">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0-preview4.19216.3">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="33f33-832">SQLitePCL.raw のバージョン 2.0.0 への更新</span><span class="sxs-lookup"><span data-stu-id="33f33-832">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="33f33-833">問題 #14824 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-833">Tracking Issue #14824</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

<span data-ttu-id="33f33-834">この変更は、EF Core 3.0 プレビュー 7 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-834">This change is introduced in EF Core 3.0-preview 7.</span></span>

<span data-ttu-id="33f33-835">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-835">**Old behavior**</span></span>

<span data-ttu-id="33f33-836">Microsoft.EntityFrameworkCore.Sqlite は、以前はバージョン 1.1.12 の SQLitePCL.raw に依存していました。</span><span class="sxs-lookup"><span data-stu-id="33f33-836">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

<span data-ttu-id="33f33-837">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-837">**New behavior**</span></span>

<span data-ttu-id="33f33-838">バージョン 2.0.0 に依存するようパッケージが更新されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-838">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="33f33-839">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-839">**Why**</span></span>

<span data-ttu-id="33f33-840">バージョン 2.0.0 の SQLitePCL.raw では、.NET Standard 2.0 をターゲットとします。</span><span class="sxs-lookup"><span data-stu-id="33f33-840">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="33f33-841">これは以前は、推移的なパッケージの大規模なクロージャの動作を必要とする .NET Standard 1.1 をターゲットとしていました。</span><span class="sxs-lookup"><span data-stu-id="33f33-841">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

<span data-ttu-id="33f33-842">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-842">**Mitigations**</span></span>

<span data-ttu-id="33f33-843">SQLitePCL.raw バージョン 2.0.0 には、いくつかの破壊的変更が含まれています。</span><span class="sxs-lookup"><span data-stu-id="33f33-843">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="33f33-844">詳細については、[リリース ノート](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="33f33-844">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="33f33-845">NetTopologySuite のバージョン 2.0.0 への更新</span><span class="sxs-lookup"><span data-stu-id="33f33-845">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="33f33-846">問題 #14825 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-846">Tracking Issue #14825</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

<span data-ttu-id="33f33-847">この変更は、EF Core 3.0 プレビュー 7 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-847">This change is introduced in EF Core 3.0-preview 7.</span></span>

<span data-ttu-id="33f33-848">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-848">**Old behavior**</span></span>

<span data-ttu-id="33f33-849">以前の空間パッケージはバージョン 1.15.1 の NetTopologySuite に依存していました。</span><span class="sxs-lookup"><span data-stu-id="33f33-849">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

<span data-ttu-id="33f33-850">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-850">**New behavior**</span></span>

<span data-ttu-id="33f33-851">バージョン 2.0.0 に依存するようパッケージが更新されました。</span><span class="sxs-lookup"><span data-stu-id="33f33-851">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="33f33-852">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-852">**Why**</span></span>

<span data-ttu-id="33f33-853">バージョン 2.0.0 の NetTopologySuite は、EF Core の使用に伴ういくつかのユーザビリティの問題に対処することを目的としています。</span><span class="sxs-lookup"><span data-stu-id="33f33-853">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

<span data-ttu-id="33f33-854">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-854">**Mitigations**</span></span>

<span data-ttu-id="33f33-855">NetTopologySuite バージョン 2.0.0 には、いくつかの破壊的変更が含まれています。</span><span class="sxs-lookup"><span data-stu-id="33f33-855">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="33f33-856">詳細については、[リリース ノート](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="33f33-856">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="33f33-857">複数のあいまいな自己参照リレーションシップを構成する必要がある</span><span class="sxs-lookup"><span data-stu-id="33f33-857">Multiple ambiguous self-referencing relationships must be configured</span></span> 

[<span data-ttu-id="33f33-858">イシュー #13573 の追跡</span><span class="sxs-lookup"><span data-stu-id="33f33-858">Tracking Issue #13573</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

<span data-ttu-id="33f33-859">この変更は、EF Core 3.0 プレビュー 6 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="33f33-859">This change is introduced in EF Core 3.0-preview 6.</span></span>

<span data-ttu-id="33f33-860">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-860">**Old behavior**</span></span>

<span data-ttu-id="33f33-861">複数の自己参照型一方向ナビゲーション プロパティと一致する FK を持つエンティティ型が、1 つのリレーションシップとして正しく構成されませんでした。</span><span class="sxs-lookup"><span data-stu-id="33f33-861">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="33f33-862">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-862">For example:</span></span>

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

<span data-ttu-id="33f33-863">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="33f33-863">**New behavior**</span></span>

<span data-ttu-id="33f33-864">このシナリオがモデル構築で検出されるようになり、モデルがあいまいであることを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="33f33-864">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

<span data-ttu-id="33f33-865">**理由**</span><span class="sxs-lookup"><span data-stu-id="33f33-865">**Why**</span></span>

<span data-ttu-id="33f33-866">結果として得られるモデルはあいまいであり、通常、この場合には間違っている可能性があるためです。</span><span class="sxs-lookup"><span data-stu-id="33f33-866">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

<span data-ttu-id="33f33-867">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="33f33-867">**Mitigations**</span></span>

<span data-ttu-id="33f33-868">完全に構成されたリレーションシップを使用します。</span><span class="sxs-lookup"><span data-stu-id="33f33-868">Use full configuration of the relationship.</span></span> <span data-ttu-id="33f33-869">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="33f33-869">For example:</span></span>

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
