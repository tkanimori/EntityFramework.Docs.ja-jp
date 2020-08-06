---
title: Entity Framework Core 5.0 の計画
author: ajcvickers
ms.date: 06/11/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: 4abb6f500dce320dd0c32f8f3bf5c529b59fb28b
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526889"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="d8d06-102">Entity Framework Core 5.0 の計画</span><span class="sxs-lookup"><span data-stu-id="d8d06-102">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="d8d06-103">[計画プロセス](xref:core/what-is-new/release_planning)で説明されているように、EF Core 5.0 リリースの暫定的な計画には関係者からの意見が取り込まれています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-103">As described in the [planning process](xref:core/what-is-new/release_planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d8d06-104">この計画はまだ進行中です。</span><span class="sxs-lookup"><span data-stu-id="d8d06-104">This plan is still a work-in-progress.</span></span> <span data-ttu-id="d8d06-105">ここで説明されている内容は確約されたものではありません。</span><span class="sxs-lookup"><span data-stu-id="d8d06-105">Nothing here is a commitment.</span></span> <span data-ttu-id="d8d06-106">この計画は、さらに学習して発展する出発点となります。</span><span class="sxs-lookup"><span data-stu-id="d8d06-106">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="d8d06-107">現在、5.0 に対して計画されていない機能が盛り込まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8d06-107">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="d8d06-108">現在、5.0 に対して計画されている機能が除外される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8d06-108">Some things currently planned for 5.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="d8d06-109">一般情報</span><span class="sxs-lookup"><span data-stu-id="d8d06-109">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="d8d06-110">バージョン番号とリリース日</span><span class="sxs-lookup"><span data-stu-id="d8d06-110">Version number and release date</span></span>

<span data-ttu-id="d8d06-111">現在、EF Core 5.0 は、[.NET 5.0 と同時に](https://devblogs.microsoft.com/dotnet/introducing-net-5/)リリースされる予定です。</span><span class="sxs-lookup"><span data-stu-id="d8d06-111">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="d8d06-112">"5.0" というバージョンは、.NET 5.0 に合わせて選択されました。</span><span class="sxs-lookup"><span data-stu-id="d8d06-112">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="d8d06-113">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="d8d06-113">Supported platforms</span></span>

<span data-ttu-id="d8d06-114">EF Core 5.0 は、[.NET 5.0 プラットフォームが .NET Core に収束する](https://devblogs.microsoft.com/dotnet/introducing-net-5/)ことに基づいて、任意の .NET 5.0 プラットフォームで動作するように計画されています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-114">EF Core 5.0 is planned to run on any .NET 5.0 platform based on the [convergence of these platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="d8d06-115">.NET Standard および実際に使用される TFM の観点から、これが何を意味するかについては未定です。</span><span class="sxs-lookup"><span data-stu-id="d8d06-115">What this means in terms of .NET Standard and the actual TFM used is still TBD.</span></span>

<span data-ttu-id="d8d06-116">EF Core 5.0 は、.NET Framework では実行されません。</span><span class="sxs-lookup"><span data-stu-id="d8d06-116">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="d8d06-117">互換性に影響する変更</span><span class="sxs-lookup"><span data-stu-id="d8d06-117">Breaking changes</span></span>

<span data-ttu-id="d8d06-118">EF Core 5.0 には[破壊的変更](xref:core/what-is-new/ef-core-5.0/breaking-changes)がいくつか含まれていますが、EF Core 3.0 のときほど深刻なものは多くありません。</span><span class="sxs-lookup"><span data-stu-id="d8d06-118">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="d8d06-119">目標は、アプリケーションの大部分を中断せずに更新できるようにすることです。</span><span class="sxs-lookup"><span data-stu-id="d8d06-119">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="d8d06-120">データベース プロバイダーに対して、破壊的変更がいくつかあることが予想されます (特に TPT のサポートに関して)。</span><span class="sxs-lookup"><span data-stu-id="d8d06-120">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="d8d06-121">ただし、5.0 対応にプロバイダーを更新する作業は、3.0 の更新に要した時間より短いものと予想されます。</span><span class="sxs-lookup"><span data-stu-id="d8d06-121">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="d8d06-122">テーマ</span><span class="sxs-lookup"><span data-stu-id="d8d06-122">Themes</span></span>

<span data-ttu-id="d8d06-123">EF Core 5.0 への大規模な投資の基礎となる、いくつかの主要な領域やテーマが抽出されています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-123">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="d8d06-124">慣例による完全に透過的な多対多マッピング</span><span class="sxs-lookup"><span data-stu-id="d8d06-124">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="d8d06-125">開発リーダー: @smitpatel、@AndriySvyryd、@lajones</span><span class="sxs-lookup"><span data-stu-id="d8d06-125">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="d8d06-126">追跡: [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="d8d06-126">Tracked by [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span></span>

<span data-ttu-id="d8d06-127">規模: L</span><span class="sxs-lookup"><span data-stu-id="d8d06-127">T-shirt size: L</span></span>

<span data-ttu-id="d8d06-128">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="d8d06-128">Status: In-progress</span></span>

<span data-ttu-id="d8d06-129">多対多は、GitHub バックログで[最も要求の多かった機能](https://github.com/aspnet/EntityFrameworkCore/issues/1368)です (506 票)。</span><span class="sxs-lookup"><span data-stu-id="d8d06-129">Many-to-many is the [most requested feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="d8d06-130">多対多リレーションシップのサポートは、次の 3 つの主要領域に分けることができます。</span><span class="sxs-lookup"><span data-stu-id="d8d06-130">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="d8d06-131">スキップ ナビゲーションのプロパティ -- 次のテーマで取り上げます。</span><span class="sxs-lookup"><span data-stu-id="d8d06-131">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="d8d06-132">property-bag エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="d8d06-132">Property-bag entity types.</span></span> <span data-ttu-id="d8d06-133">これにより、標準の CLR 型 (例: `Dictionary`) をエンティティ インスタンスに使用できるようになり、エンティティ型ごとに明示的な CLR 型は不要になります。</span><span class="sxs-lookup"><span data-stu-id="d8d06-133">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="d8d06-134">追跡: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)</span><span class="sxs-lookup"><span data-stu-id="d8d06-134">Tracked by [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span></span>
* <span data-ttu-id="d8d06-135">多対多リレーションシップを簡単に構成するための Sugar。</span><span class="sxs-lookup"><span data-stu-id="d8d06-135">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="d8d06-136">スキップ ナビゲーションのサポートに加えて、完全なエクスペリエンスが提供されるように、多対多のその他の領域を EF Core 5.0 に導入しました。</span><span class="sxs-lookup"><span data-stu-id="d8d06-136">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="d8d06-137">多対多のナビゲーション プロパティ (別名 "スキップ ナビゲーション")</span><span class="sxs-lookup"><span data-stu-id="d8d06-137">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="d8d06-138">開発リーダー: @smitpatel、@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="d8d06-138">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="d8d06-139">追跡: [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="d8d06-139">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="d8d06-140">規模: L</span><span class="sxs-lookup"><span data-stu-id="d8d06-140">T-shirt size: L</span></span>

<span data-ttu-id="d8d06-141">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="d8d06-141">Status: In-progress</span></span>

<span data-ttu-id="d8d06-142">最初のテーマで説明したように、多対多のサポートには複数の側面があります。</span><span class="sxs-lookup"><span data-stu-id="d8d06-142">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="d8d06-143">このテーマでは、特にスキップ ナビゲーションの使用を追跡します。</span><span class="sxs-lookup"><span data-stu-id="d8d06-143">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="d8d06-144">多対多のサポートを望んでいるユーザーにとって最も大きな阻害要因は、クエリなどのビジネス ロジックにおいて、結合テーブルを参照せずに、"自然な" リレーションシップを使用できないことであると考えています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-144">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="d8d06-145">結合テーブルのエンティティ型がまだ存在する場合がありますが、ビジネス ロジックを妨げることはないはずです。</span><span class="sxs-lookup"><span data-stu-id="d8d06-145">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="d8d06-146">Table-Per-Type (TPT) の継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="d8d06-146">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="d8d06-147">開発リーダー: @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="d8d06-147">Lead developer: @AndriySvyryd</span></span>

<span data-ttu-id="d8d06-148">追跡: [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="d8d06-148">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="d8d06-149">規模: XL</span><span class="sxs-lookup"><span data-stu-id="d8d06-149">T-shirt size: XL</span></span>

<span data-ttu-id="d8d06-150">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="d8d06-150">Status: In-progress</span></span>

<span data-ttu-id="d8d06-151">TPT の作業が行われているのは、要求の多かった機能であることと (289 票、全体で 3 番目)、.NET 5 の全体的な計画の基本特性に適していると考えられる低レベルの変更が必要であるためです。</span><span class="sxs-lookup"><span data-stu-id="d8d06-151">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="d8d06-152">これにより、データベース プロバイダーに対して破壊的変更が行われることが予想されますが、3.0 で必要であった変更ほど大がかりにはならないはずです。</span><span class="sxs-lookup"><span data-stu-id="d8d06-152">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="d8d06-153">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="d8d06-153">Filtered Include</span></span>

<span data-ttu-id="d8d06-154">開発リーダー: @maumar</span><span class="sxs-lookup"><span data-stu-id="d8d06-154">Lead developer: @maumar</span></span>

<span data-ttu-id="d8d06-155">追跡: [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="d8d06-155">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="d8d06-156">規模: M</span><span class="sxs-lookup"><span data-stu-id="d8d06-156">T-shirt size: M</span></span>

<span data-ttu-id="d8d06-157">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="d8d06-157">Status: In-progress</span></span>

<span data-ttu-id="d8d06-158">フィルター処理されたインクルードは、要求の多かった機能であり (376 票、全体で 2 番目)、作業量は多くなく、現在はモデル レベルのフィルターやさらに複雑なクエリを必要とする多くのシナリオの障害が取り除かれたり、実現がいっそう簡単になるものと考えられます。</span><span class="sxs-lookup"><span data-stu-id="d8d06-158">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="d8d06-159">インクルードの分割</span><span class="sxs-lookup"><span data-stu-id="d8d06-159">Split Include</span></span>

<span data-ttu-id="d8d06-160">開発リーダー: @smitpatel</span><span class="sxs-lookup"><span data-stu-id="d8d06-160">Lead developer: @smitpatel</span></span>

<span data-ttu-id="d8d06-161">追跡: [#20892](https://github.com/dotnet/efcore/issues/20892)</span><span class="sxs-lookup"><span data-stu-id="d8d06-161">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="d8d06-162">規模: L</span><span class="sxs-lookup"><span data-stu-id="d8d06-162">T-shirt size: L</span></span>

<span data-ttu-id="d8d06-163">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="d8d06-163">Status: In-progress</span></span>

<span data-ttu-id="d8d06-164">EF Core 3.0 では、特定の LINQ クエリに対して単一の SQL クエリを作成するための既定の動作が変更されました。</span><span class="sxs-lookup"><span data-stu-id="d8d06-164">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="d8d06-165">これにより、複数のコレクション向けのインクルードを使用するクエリのパフォーマンスが大幅に低下していました。</span><span class="sxs-lookup"><span data-stu-id="d8d06-165">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="d8d06-166">EF Core 5.0 では、新しい既定の動作を保持しています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-166">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="d8d06-167">ただし、EF Core 5.0 では、単一のクエリによってパフォーマンスが低下しているコレクションのインクルードで複数クエリの生成が許可されます。</span><span class="sxs-lookup"><span data-stu-id="d8d06-167">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="d8d06-168">ToTable、ToQuery、ToView、FromSql などの合理化</span><span class="sxs-lookup"><span data-stu-id="d8d06-168">Rationalize ToTable, ToQuery, ToView, FromSql, etc</span></span>

<span data-ttu-id="d8d06-169">開発リーダー: @maumar、@smitpatel</span><span class="sxs-lookup"><span data-stu-id="d8d06-169">Lead developers: @maumar and @smitpatel</span></span>

<span data-ttu-id="d8d06-170">追跡: [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="d8d06-170">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="d8d06-171">規模: L</span><span class="sxs-lookup"><span data-stu-id="d8d06-171">T-shirt size: L</span></span>

<span data-ttu-id="d8d06-172">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="d8d06-172">Status: In-progress</span></span>

<span data-ttu-id="d8d06-173">以前のリリースでは、未加工の SQL、キーなしの型、および関連する領域のサポートを強化する作業が行われました。</span><span class="sxs-lookup"><span data-stu-id="d8d06-173">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="d8d06-174">ただし、すべてが連携して全体として機能するには、まだギャップと不整合の両方があります。</span><span class="sxs-lookup"><span data-stu-id="d8d06-174">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="d8d06-175">5\.0 での目標は、これらを修正し、異なる型のエンティティとそれに関連付けられたクエリおよびデータベース アイテムを定義、移行、使用するための優れたエクスペリエンスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="d8d06-175">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="d8d06-176">これには、コンパイル済みクエリ API の更新が含まれる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="d8d06-176">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="d8d06-177">現在の機能の一部は制限が緩すぎるために簡単に誤る可能性があるので、この項目ではアプリケーション レベルでいくつか破壊的変更が発生する可能性があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d8d06-177">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="d8d06-178">このような機能の一部を遮断し、代わりの方法についてのガイダンスを提供することになると思われます。</span><span class="sxs-lookup"><span data-stu-id="d8d06-178">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="d8d06-179">一般的なクエリの機能強化</span><span class="sxs-lookup"><span data-stu-id="d8d06-179">General query enhancements</span></span>

<span data-ttu-id="d8d06-180">開発リーダー: @smitpatel、@maumar</span><span class="sxs-lookup"><span data-stu-id="d8d06-180">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="d8d06-181">追跡: [5.0 のマイルストーンで `area-query` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="d8d06-181">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="d8d06-182">規模: XL</span><span class="sxs-lookup"><span data-stu-id="d8d06-182">T-shirt size: XL</span></span>

<span data-ttu-id="d8d06-183">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="d8d06-183">Status: In-progress</span></span>

<span data-ttu-id="d8d06-184">クエリ変換コードは、EF Core 3.0 で大幅に書き換えられました。</span><span class="sxs-lookup"><span data-stu-id="d8d06-184">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="d8d06-185">一般に、このため、クエリ コードはより堅牢な状態になっています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-185">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="d8d06-186">5\.0 では、TPT とスキップ ナビゲーション プロパティをサポートするために必要な部分を除いて、クエリに関する大きな変更が行われる予定はありません。</span><span class="sxs-lookup"><span data-stu-id="d8d06-186">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="d8d06-187">ただしそれでも、3.0 の見直しから残っているいくつかの技術的負債を修正するためにかなりの作業が必要です。</span><span class="sxs-lookup"><span data-stu-id="d8d06-187">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="d8d06-188">また、多くのバグの修正と、クエリ全体のエクスペリエンスをさらに向上させるための小さな機能拡張の実装も予定されています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-188">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="d8d06-189">移行と展開のエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="d8d06-189">Migrations and deployment experience</span></span>

<span data-ttu-id="d8d06-190">開発リーダー: @bricelam</span><span class="sxs-lookup"><span data-stu-id="d8d06-190">Lead developers: @bricelam</span></span>

<span data-ttu-id="d8d06-191">追跡: [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="d8d06-191">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="d8d06-192">規模: L</span><span class="sxs-lookup"><span data-stu-id="d8d06-192">T-shirt size: L</span></span>

<span data-ttu-id="d8d06-193">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="d8d06-193">Status: In-progress</span></span>

<span data-ttu-id="d8d06-194">現在は、多くの開発者が、アプリケーションの起動時にデータベースを移行しています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-194">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="d8d06-195">これは簡単ですが、次の理由で推奨されません。</span><span class="sxs-lookup"><span data-stu-id="d8d06-195">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="d8d06-196">複数のスレッド、プロセス、サーバーによって、データベースの移行が同時に試みられる可能性があります</span><span class="sxs-lookup"><span data-stu-id="d8d06-196">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="d8d06-197">これが行われている間に、アプリケーションによって不整合な状態へのアクセスが試みられる可能性があります</span><span class="sxs-lookup"><span data-stu-id="d8d06-197">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="d8d06-198">通常、スキーマを変更するためのデータベース アクセス許可を、アプリケーションの実行に付与してはなりません</span><span class="sxs-lookup"><span data-stu-id="d8d06-198">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="d8d06-199">何か問題が発生した場合に、クリーンな状態に戻すことが困難です</span><span class="sxs-lookup"><span data-stu-id="d8d06-199">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="d8d06-200">展開時にデータベースを簡単に移行できる、より良いエクスペリエンスを提供したいと考えています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-200">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="d8d06-201">これは次のようなものになるはずです。</span><span class="sxs-lookup"><span data-stu-id="d8d06-201">This should:</span></span>

* <span data-ttu-id="d8d06-202">Linux、Mac、Windows で動作します</span><span class="sxs-lookup"><span data-stu-id="d8d06-202">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="d8d06-203">コマンド ラインでの適切なエクスペリエンスです</span><span class="sxs-lookup"><span data-stu-id="d8d06-203">Be a good experience on the command line</span></span>
* <span data-ttu-id="d8d06-204">コンテナーでのシナリオをサポートします</span><span class="sxs-lookup"><span data-stu-id="d8d06-204">Support scenarios with containers</span></span>
* <span data-ttu-id="d8d06-205">よく使用される実際の展開ツールとフローを使用します</span><span class="sxs-lookup"><span data-stu-id="d8d06-205">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="d8d06-206">少なくとも Visual Studio に統合されます</span><span class="sxs-lookup"><span data-stu-id="d8d06-206">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="d8d06-207">結果として、EF Core での多くの小規模な機能向上 (たとえば、SQLite での移行の向上など) と、EF だけではないエンドツーエンドのエクスペリエンスを向上させるための、他のチームによるガイダンスと長期的な共同作業になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8d06-207">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="d8d06-208">EF Core プラットフォームのエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="d8d06-208">EF Core platforms experience</span></span>

<span data-ttu-id="d8d06-209">開発リーダー: @roji、@bricelam</span><span class="sxs-lookup"><span data-stu-id="d8d06-209">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="d8d06-210">追跡: [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="d8d06-210">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="d8d06-211">規模: L</span><span class="sxs-lookup"><span data-stu-id="d8d06-211">T-shirt size: L</span></span>

<span data-ttu-id="d8d06-212">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="d8d06-212">Status: Not started</span></span>

<span data-ttu-id="d8d06-213">従来の MVC に似た Web アプリケーションでの EF Core の使用に関しては適切なガイダンスがあります。</span><span class="sxs-lookup"><span data-stu-id="d8d06-213">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="d8d06-214">他のプラットフォームとアプリケーション モデルについてのガイダンスはないか、または古くなっています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-214">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="d8d06-215">EF Core 5.0 では、以下のもので EF Core を使用するエクスペリエンスの調査、改善、文書化が計画されています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-215">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="d8d06-216">Blazor</span><span class="sxs-lookup"><span data-stu-id="d8d06-216">Blazor</span></span>
* <span data-ttu-id="d8d06-217">Xamarin (AOT とリンカーのストーリーの使用を含む)</span><span class="sxs-lookup"><span data-stu-id="d8d06-217">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="d8d06-218">WinForms、WPF、WinUI、場合によっては他の UI</span><span class="sxs-lookup"><span data-stu-id="d8d06-218">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="d8d06-219">frameworks</span><span class="sxs-lookup"><span data-stu-id="d8d06-219">frameworks</span></span>

<span data-ttu-id="d8d06-220">これは、EF Core での多くの小規模な機能向上と、EF だけではないエンドツーエンドのエクスペリエンスを向上させるための、他のチームによるガイダンスと長期的な共同作業になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="d8d06-220">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="d8d06-221">ここで検討されている具体的な領域は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="d8d06-221">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="d8d06-222">展開 (移行などの EF ツールの使用に関するエクスペリエンスを含む)</span><span class="sxs-lookup"><span data-stu-id="d8d06-222">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="d8d06-223">アプリケーション モデル、(Xamarin と Blazor、場合によってはその他を含む)</span><span class="sxs-lookup"><span data-stu-id="d8d06-223">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="d8d06-224">SQLite のエクスペリエンス (空間エクスペリエンスとテーブルの再構築を含む)</span><span class="sxs-lookup"><span data-stu-id="d8d06-224">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="d8d06-225">AOT とリンクのエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="d8d06-225">AOT and linking experiences</span></span>
* <span data-ttu-id="d8d06-226">診断の統合 (パフォーマンス カウンターを含む)</span><span class="sxs-lookup"><span data-stu-id="d8d06-226">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="d8d06-227">パフォーマンス</span><span class="sxs-lookup"><span data-stu-id="d8d06-227">Performance</span></span>

<span data-ttu-id="d8d06-228">開発リーダー: @roji</span><span class="sxs-lookup"><span data-stu-id="d8d06-228">Lead developer: @roji</span></span>

<span data-ttu-id="d8d06-229">追跡: [5.0 のマイルストーンで `area-perf` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="d8d06-229">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="d8d06-230">規模: L</span><span class="sxs-lookup"><span data-stu-id="d8d06-230">T-shirt size: L</span></span>

<span data-ttu-id="d8d06-231">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="d8d06-231">Status: In-progress</span></span>

<span data-ttu-id="d8d06-232">EF Core では、パフォーマンス ベンチマークのスイートの向上と、ランタイムに対する特定のパフォーマンスの向上が計画されています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-232">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="d8d06-233">さらに、3.0 リリース サイクル中にプロトタイプ化された新しい ADO.NET バッチ API を完了する予定です。</span><span class="sxs-lookup"><span data-stu-id="d8d06-233">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="d8d06-234">また、ADO.NET レイヤーでは、Npgsql プロバイダーのさらなるパフォーマンス向上が計画されています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-234">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="d8d06-235">この作業の一環として、ADO.NET および EF Core パフォーマンス カウンターや他の診断を必要に応じて追加することも計画されています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-235">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="d8d06-236">アーキテクチャおよび共同作成者向けドキュメント</span><span class="sxs-lookup"><span data-stu-id="d8d06-236">Architectural/contributor documentation</span></span>

<span data-ttu-id="d8d06-237">ドキュメント作成リーダー: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="d8d06-237">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="d8d06-238">追跡: [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="d8d06-238">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="d8d06-239">規模: L</span><span class="sxs-lookup"><span data-stu-id="d8d06-239">T-shirt size: L</span></span>

<span data-ttu-id="d8d06-240">状態: 切り取り</span><span class="sxs-lookup"><span data-stu-id="d8d06-240">Status: Cut</span></span>

<span data-ttu-id="d8d06-241">ここでのアイデアは、EF Core の内部で何が起こっているかを理解しやすくすることです。</span><span class="sxs-lookup"><span data-stu-id="d8d06-241">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="d8d06-242">これは、EF Core を使用するすべてのユーザーにとって便利なことですが、主な目的は、外部ユーザーが次のことを容易にできるようにすることです。</span><span class="sxs-lookup"><span data-stu-id="d8d06-242">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="d8d06-243">EF Core コードに投稿する</span><span class="sxs-lookup"><span data-stu-id="d8d06-243">Contribute to the EF Core code</span></span>
* <span data-ttu-id="d8d06-244">データベース プロバイダーを作成する</span><span class="sxs-lookup"><span data-stu-id="d8d06-244">Create database providers</span></span>
* <span data-ttu-id="d8d06-245">他の拡張機能を作成する</span><span class="sxs-lookup"><span data-stu-id="d8d06-245">Build other extensions</span></span>

<span data-ttu-id="d8d06-246">更新:残念ながら、この計画は壮大すぎました。</span><span class="sxs-lookup"><span data-stu-id="d8d06-246">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="d8d06-247">これが重要なことは変わりませんが、残念ながら EF Core 5.0 では実現できません。</span><span class="sxs-lookup"><span data-stu-id="d8d06-247">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="d8d06-248">Microsoft.Data.Sqlite のドキュメント</span><span class="sxs-lookup"><span data-stu-id="d8d06-248">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="d8d06-249">ドキュメント作成リーダー: @bricelam</span><span class="sxs-lookup"><span data-stu-id="d8d06-249">Lead documenter: @bricelam</span></span>

<span data-ttu-id="d8d06-250">追跡: [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="d8d06-250">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="d8d06-251">規模: M</span><span class="sxs-lookup"><span data-stu-id="d8d06-251">T-shirt size: M</span></span>

<span data-ttu-id="d8d06-252">状態: 完了しました。</span><span class="sxs-lookup"><span data-stu-id="d8d06-252">Status: Completed.</span></span> <span data-ttu-id="d8d06-253">新しいドキュメントは、[Microsoft docs サイトで公開](/dotnet/standard/data/sqlite/?tabs=netcore-cli)されています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-253">The new documentation is [live on the Microsoft docs site](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="d8d06-254">EF チームも、Microsoft.Data.Sqlite ADO.NET プロバイダーを所有しています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-254">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="d8d06-255">5\.0 リリースの一環として、このプロバイダーの完全な文書化が予定されています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-255">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="d8d06-256">一般的なドキュメント</span><span class="sxs-lookup"><span data-stu-id="d8d06-256">General documentation</span></span>

<span data-ttu-id="d8d06-257">ドキュメント作成リーダー: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="d8d06-257">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="d8d06-258">追跡: [5.0 マイルストーンでのドキュメント リポジトリの問題](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="d8d06-258">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="d8d06-259">規模: L</span><span class="sxs-lookup"><span data-stu-id="d8d06-259">T-shirt size: L</span></span>

<span data-ttu-id="d8d06-260">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="d8d06-260">Status: In-progress</span></span>

<span data-ttu-id="d8d06-261">既に、3.0 および 3.1 リリースのドキュメントの更新が行われています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-261">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="d8d06-262">また、以下の作業も行われています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-262">We are also working on:</span></span>

* <span data-ttu-id="d8d06-263">概要ドキュメントをいっそう理解しやすく実行しやすいものにするための見直し</span><span class="sxs-lookup"><span data-stu-id="d8d06-263">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
* <span data-ttu-id="d8d06-264">検索を容易にして相互参照を追加するためのドキュメントの再編成</span><span class="sxs-lookup"><span data-stu-id="d8d06-264">Reorganization of docs to make things easier to find and to add cross-references</span></span>
* <span data-ttu-id="d8d06-265">既存ドキュメントの詳細さと明快さの向上</span><span class="sxs-lookup"><span data-stu-id="d8d06-265">Adding more details and clarifications to existing docs</span></span>
* <span data-ttu-id="d8d06-266">サンプルの更新とその他の例の追加</span><span class="sxs-lookup"><span data-stu-id="d8d06-266">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="d8d06-267">バグの修正</span><span class="sxs-lookup"><span data-stu-id="d8d06-267">Fixing bugs</span></span>

<span data-ttu-id="d8d06-268">追跡: [5.0 のマイルストーンで `type-bug` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="d8d06-268">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="d8d06-269">開発者: @roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="d8d06-269">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="d8d06-270">規模: L</span><span class="sxs-lookup"><span data-stu-id="d8d06-270">T-shirt size: L</span></span>

<span data-ttu-id="d8d06-271">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="d8d06-271">Status: In-progress</span></span>

<span data-ttu-id="d8d06-272">この執筆時点では、5.0 リリースでの修正対象として 135 件のバグがトリアージされていますが (62 件は既に修正済み)、上記の「_一般的なクエリの機能強化_」セクションとかなり重複しています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-272">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="d8d06-273">発生率 (マイルストーンで作業するようになった問題) は、3.0 リリースの全体を通して、1 か月あたり約 23 件の問題でした。</span><span class="sxs-lookup"><span data-stu-id="d8d06-273">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="d8d06-274">これらのすべてを 5.0 で修正する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d8d06-274">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="d8d06-275">大まかな見積もりとして、5.0 の時間枠では 150 件の問題をさらに修正することが計画されています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-275">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="d8d06-276">小規模な機能強化</span><span class="sxs-lookup"><span data-stu-id="d8d06-276">Small enhancements</span></span>

<span data-ttu-id="d8d06-277">追跡: [5.0 のマイルストーンで `type-enhancement` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span><span class="sxs-lookup"><span data-stu-id="d8d06-277">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="d8d06-278">開発者: @roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="d8d06-278">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="d8d06-279">規模: L</span><span class="sxs-lookup"><span data-stu-id="d8d06-279">T-shirt size: L</span></span>

<span data-ttu-id="d8d06-280">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="d8d06-280">Status: In-progress</span></span>

<span data-ttu-id="d8d06-281">これまでに説明した比較的大きな機能に加えて、5.0 では、"小さな傷" を修正するために多くの小規模な改善が予定されています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-281">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="d8d06-282">これらの機能強化の多くは、上で説明した一般的なテーマにも含まれていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="d8d06-282">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="d8d06-283">対象外</span><span class="sxs-lookup"><span data-stu-id="d8d06-283">Below-the-line</span></span>

<span data-ttu-id="d8d06-284">追跡: [`consider-for-next-release` というラベルが付けられている問題](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span><span class="sxs-lookup"><span data-stu-id="d8d06-284">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="d8d06-285">これらは、現時点では 5.0 リリースで予定されて**いない**バグ修正および機能強化ですが、上記の作業の進行状況によっては、努力目標として見られています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-285">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="d8d06-286">また、計画を立てるときは常に、[最も投票が多かった問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)が考慮されています。</span><span class="sxs-lookup"><span data-stu-id="d8d06-286">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="d8d06-287">これらの問題をリリースから切り捨てることは常に苦痛ですが、保有するリソースにとって現実的な計画を立てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="d8d06-287">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="suggestions"></a><span data-ttu-id="d8d06-288">推奨事項</span><span class="sxs-lookup"><span data-stu-id="d8d06-288">Suggestions</span></span>

<span data-ttu-id="d8d06-289">計画に関するフィードバックは重要です。</span><span class="sxs-lookup"><span data-stu-id="d8d06-289">Your feedback on planning is important.</span></span> <span data-ttu-id="d8d06-290">イシューの重要度を示す最善の方法は、GitHub でそのイシューに投票 (上向きの親指) することです。</span><span class="sxs-lookup"><span data-stu-id="d8d06-290">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="d8d06-291">このデータが、次のリリースの[計画プロセス](xref:core/what-is-new/release_planning)に取り込まれます。</span><span class="sxs-lookup"><span data-stu-id="d8d06-291">This data will then feed into the [planning process](xref:core/what-is-new/release_planning) for the next release.</span></span>
