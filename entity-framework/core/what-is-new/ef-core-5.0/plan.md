---
title: Entity Framework Core 5.0 の計画
description: Entity Framework Core 5.0 で計画されている機能
author: ajcvickers
ms.date: 08/22/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: ba56a5f3c79dacc25b3402be114c57067da49433
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129045"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="57f60-103">Entity Framework Core 5.0 の計画</span><span class="sxs-lookup"><span data-stu-id="57f60-103">Plan for Entity Framework Core 5.0</span></span>

> [!IMPORTANT]
> <span data-ttu-id="57f60-104">EF Core 5.0 が[リリースされました](xref:core/what-is-new/index)。</span><span class="sxs-lookup"><span data-stu-id="57f60-104">EF Core 5.0 [has now been released](xref:core/what-is-new/index).</span></span> <span data-ttu-id="57f60-105">このページは、プランの履歴レコードとして残ります。</span><span class="sxs-lookup"><span data-stu-id="57f60-105">This page remains as a historical record of the plan.</span></span>

<span data-ttu-id="57f60-106">[計画プロセス](xref:core/what-is-new/release-planning)で説明されているように、EF Core 5.0 リリースの暫定的な計画には関係者からの意見が取り込まれています。</span><span class="sxs-lookup"><span data-stu-id="57f60-106">As described in the [planning process](xref:core/what-is-new/release-planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="57f60-107">この計画はまだ進行中です。</span><span class="sxs-lookup"><span data-stu-id="57f60-107">This plan is still a work-in-progress.</span></span> <span data-ttu-id="57f60-108">ここで説明されている内容は確約されたものではありません。</span><span class="sxs-lookup"><span data-stu-id="57f60-108">Nothing here is a commitment.</span></span> <span data-ttu-id="57f60-109">この計画は、さらに学習して発展する出発点となります。</span><span class="sxs-lookup"><span data-stu-id="57f60-109">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="57f60-110">現在、5.0 に対して計画されていない機能が盛り込まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="57f60-110">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="57f60-111">現在、5.0 に対して計画されている機能が除外される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="57f60-111">Some things currently planned for 5.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="57f60-112">一般情報</span><span class="sxs-lookup"><span data-stu-id="57f60-112">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="57f60-113">バージョン番号とリリース日</span><span class="sxs-lookup"><span data-stu-id="57f60-113">Version number and release date</span></span>

<span data-ttu-id="57f60-114">現在、EF Core 5.0 は、[.NET 5.0 と同時に](https://devblogs.microsoft.com/dotnet/introducing-net-5/)リリースされる予定です。</span><span class="sxs-lookup"><span data-stu-id="57f60-114">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="57f60-115">"5.0" というバージョンは、.NET 5.0 に合わせて選択されました。</span><span class="sxs-lookup"><span data-stu-id="57f60-115">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="57f60-116">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="57f60-116">Supported platforms</span></span>

<span data-ttu-id="57f60-117">EF Core 5.0 は、.NET 5.0 を含む任意の .NET Standard 2.1 プラットフォームで動作するように計画されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-117">EF Core 5.0 is planned to run on any .NET Standard 2.1 platform, including .NET 5.0.</span></span> <span data-ttu-id="57f60-118">これは、より全般的な .NET 全体での [.NET Core へのプラットフォーム収束](https://devblogs.microsoft.com/dotnet/introducing-net-5/)の一部です。</span><span class="sxs-lookup"><span data-stu-id="57f60-118">This is part of the more general .NET wide [convergence of platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span>

<span data-ttu-id="57f60-119">EF Core 5.0 は、.NET Framework では実行されません。</span><span class="sxs-lookup"><span data-stu-id="57f60-119">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="57f60-120">互換性に影響する変更</span><span class="sxs-lookup"><span data-stu-id="57f60-120">Breaking changes</span></span>

<span data-ttu-id="57f60-121">EF Core 5.0 には[破壊的変更](xref:core/what-is-new/ef-core-5.0/breaking-changes)がいくつか含まれていますが、EF Core 3.0 のときほど深刻なものは多くありません。</span><span class="sxs-lookup"><span data-stu-id="57f60-121">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="57f60-122">目標は、アプリケーションの大部分を中断せずに更新できるようにすることです。</span><span class="sxs-lookup"><span data-stu-id="57f60-122">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="57f60-123">データベース プロバイダーに対して、破壊的変更がいくつかあることが予想されます (特に TPT のサポートに関して)。</span><span class="sxs-lookup"><span data-stu-id="57f60-123">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="57f60-124">ただし、5.0 対応にプロバイダーを更新する作業は、3.0 の更新に要した時間より短いものと予想されます。</span><span class="sxs-lookup"><span data-stu-id="57f60-124">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="57f60-125">テーマ</span><span class="sxs-lookup"><span data-stu-id="57f60-125">Themes</span></span>

<span data-ttu-id="57f60-126">EF Core 5.0 への大規模な投資の基礎となる、いくつかの主要な領域やテーマが抽出されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-126">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="57f60-127">慣例による完全に透過的な多対多マッピング</span><span class="sxs-lookup"><span data-stu-id="57f60-127">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="57f60-128">開発リーダー: @smitpatel、@AndriySvyryd、@lajones</span><span class="sxs-lookup"><span data-stu-id="57f60-128">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="57f60-129">追跡: [#10508](https://github.com/dotnet/efcore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="57f60-129">Tracked by [#10508](https://github.com/dotnet/efcore/issues/10508)</span></span>

<span data-ttu-id="57f60-130">規模: L</span><span class="sxs-lookup"><span data-stu-id="57f60-130">T-shirt size: L</span></span>

<span data-ttu-id="57f60-131">状態: 完了</span><span class="sxs-lookup"><span data-stu-id="57f60-131">Status: Done</span></span>

<span data-ttu-id="57f60-132">多対多は、GitHub バックログで[最も要求の多かった機能](https://github.com/dotnet/efcore/issues/1368)です (506 票)。</span><span class="sxs-lookup"><span data-stu-id="57f60-132">Many-to-many is the [most requested feature](https://github.com/dotnet/efcore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="57f60-133">多対多リレーションシップのサポートは、次の 3 つの主要領域に分けることができます。</span><span class="sxs-lookup"><span data-stu-id="57f60-133">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="57f60-134">スキップ ナビゲーションのプロパティ -- 次のテーマで取り上げます。</span><span class="sxs-lookup"><span data-stu-id="57f60-134">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="57f60-135">property-bag エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="57f60-135">Property-bag entity types.</span></span> <span data-ttu-id="57f60-136">これにより、標準の CLR 型 (例: `Dictionary`) をエンティティ インスタンスに使用できるようになり、エンティティ型ごとに明示的な CLR 型は不要になります。</span><span class="sxs-lookup"><span data-stu-id="57f60-136">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="57f60-137">追跡: [#9914](https://github.com/dotnet/efcore/issues/9914)</span><span class="sxs-lookup"><span data-stu-id="57f60-137">Tracked by [#9914](https://github.com/dotnet/efcore/issues/9914).</span></span>
* <span data-ttu-id="57f60-138">多対多リレーションシップを簡単に構成するための Sugar。</span><span class="sxs-lookup"><span data-stu-id="57f60-138">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="57f60-139">スキップ ナビゲーションのサポートに加えて、完全なエクスペリエンスが提供されるように、多対多のその他の領域を EF Core 5.0 に導入しました。</span><span class="sxs-lookup"><span data-stu-id="57f60-139">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="57f60-140">多対多のナビゲーション プロパティ (別名 "スキップ ナビゲーション")</span><span class="sxs-lookup"><span data-stu-id="57f60-140">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="57f60-141">開発リーダー: @smitpatel、@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="57f60-141">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="57f60-142">追跡: [#19003](https://github.com/dotnet/efcore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="57f60-142">Tracked by [#19003](https://github.com/dotnet/efcore/issues/19003)</span></span>

<span data-ttu-id="57f60-143">規模: L</span><span class="sxs-lookup"><span data-stu-id="57f60-143">T-shirt size: L</span></span>

<span data-ttu-id="57f60-144">状態: 完了</span><span class="sxs-lookup"><span data-stu-id="57f60-144">Status: Done</span></span>

<span data-ttu-id="57f60-145">最初のテーマで説明したように、多対多のサポートには複数の側面があります。</span><span class="sxs-lookup"><span data-stu-id="57f60-145">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="57f60-146">このテーマでは、特にスキップ ナビゲーションの使用を追跡します。</span><span class="sxs-lookup"><span data-stu-id="57f60-146">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="57f60-147">多対多のサポートを望んでいるユーザーにとって最も大きな阻害要因は、クエリなどのビジネス ロジックにおいて、結合テーブルを参照せずに、"自然な" リレーションシップを使用できないことであると考えています。</span><span class="sxs-lookup"><span data-stu-id="57f60-147">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="57f60-148">結合テーブルのエンティティ型がまだ存在する場合がありますが、ビジネス ロジックを妨げることはないはずです。</span><span class="sxs-lookup"><span data-stu-id="57f60-148">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="57f60-149">Table-Per-Type (TPT) の継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="57f60-149">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="57f60-150">開発リーダー: @AndriySvyryd、@smitpatel</span><span class="sxs-lookup"><span data-stu-id="57f60-150">Lead developer: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="57f60-151">追跡: [#2266](https://github.com/dotnet/efcore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="57f60-151">Tracked by [#2266](https://github.com/dotnet/efcore/issues/2266)</span></span>

<span data-ttu-id="57f60-152">規模: XL</span><span class="sxs-lookup"><span data-stu-id="57f60-152">T-shirt size: XL</span></span>

<span data-ttu-id="57f60-153">状態: 完了</span><span class="sxs-lookup"><span data-stu-id="57f60-153">Status: Done</span></span>

<span data-ttu-id="57f60-154">TPT の作業が行われているのは、要求の多かった機能であることと (289 票、全体で 3 番目)、.NET 5 の全体的な計画の基本特性に適していると考えられる低レベルの変更が必要であるためです。</span><span class="sxs-lookup"><span data-stu-id="57f60-154">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="57f60-155">これにより、データベース プロバイダーに対して破壊的変更が行われることが予想されますが、3.0 で必要であった変更ほど大がかりにはならないはずです。</span><span class="sxs-lookup"><span data-stu-id="57f60-155">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="57f60-156">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="57f60-156">Filtered Include</span></span>

<span data-ttu-id="57f60-157">開発リーダー: @maumar</span><span class="sxs-lookup"><span data-stu-id="57f60-157">Lead developer: @maumar</span></span>

<span data-ttu-id="57f60-158">追跡: [#1833](https://github.com/dotnet/efcore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="57f60-158">Tracked by [#1833](https://github.com/dotnet/efcore/issues/1833)</span></span>

<span data-ttu-id="57f60-159">規模: M</span><span class="sxs-lookup"><span data-stu-id="57f60-159">T-shirt size: M</span></span>

<span data-ttu-id="57f60-160">状態: 完了</span><span class="sxs-lookup"><span data-stu-id="57f60-160">Status: Done</span></span>

<span data-ttu-id="57f60-161">フィルター処理されたインクルードは、要求の多かった機能であり (376 票、全体で 2 番目)、作業量は多くなく、現在はモデル レベルのフィルターやさらに複雑なクエリを必要とする多くのシナリオの障害が取り除かれたり、実現がいっそう簡単になるものと考えられます。</span><span class="sxs-lookup"><span data-stu-id="57f60-161">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="57f60-162">インクルードの分割</span><span class="sxs-lookup"><span data-stu-id="57f60-162">Split Include</span></span>

<span data-ttu-id="57f60-163">開発リーダー: @smitpatel</span><span class="sxs-lookup"><span data-stu-id="57f60-163">Lead developer: @smitpatel</span></span>

<span data-ttu-id="57f60-164">追跡: [#20892](https://github.com/dotnet/efcore/issues/20892)</span><span class="sxs-lookup"><span data-stu-id="57f60-164">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="57f60-165">規模: L</span><span class="sxs-lookup"><span data-stu-id="57f60-165">T-shirt size: L</span></span>

<span data-ttu-id="57f60-166">状態: 完了</span><span class="sxs-lookup"><span data-stu-id="57f60-166">Status: Done</span></span>

<span data-ttu-id="57f60-167">EF Core 3.0 では、特定の LINQ クエリに対して単一の SQL クエリを作成するための既定の動作が変更されました。</span><span class="sxs-lookup"><span data-stu-id="57f60-167">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="57f60-168">これにより、複数のコレクション向けのインクルードを使用するクエリのパフォーマンスが大幅に低下していました。</span><span class="sxs-lookup"><span data-stu-id="57f60-168">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="57f60-169">EF Core 5.0 では、新しい既定の動作を保持しています。</span><span class="sxs-lookup"><span data-stu-id="57f60-169">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="57f60-170">ただし、EF Core 5.0 では、単一のクエリによってパフォーマンスが低下しているコレクションのインクルードで複数クエリの生成が許可されます。</span><span class="sxs-lookup"><span data-stu-id="57f60-170">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance.</span></span>

## <a name="required-one-to-one-dependents"></a><span data-ttu-id="57f60-171">必須の一対一の依存関係</span><span class="sxs-lookup"><span data-stu-id="57f60-171">Required one-to-one dependents</span></span>

<span data-ttu-id="57f60-172">開発リーダー: @AndriySvyryd、@smitpatel</span><span class="sxs-lookup"><span data-stu-id="57f60-172">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="57f60-173">追跡: [#12100](https://github.com/dotnet/efcore/issues/12100)</span><span class="sxs-lookup"><span data-stu-id="57f60-173">Tracked by [#12100](https://github.com/dotnet/efcore/issues/12100)</span></span>

<span data-ttu-id="57f60-174">規模: M</span><span class="sxs-lookup"><span data-stu-id="57f60-174">T-shirt size: M</span></span>

<span data-ttu-id="57f60-175">状態: 完了</span><span class="sxs-lookup"><span data-stu-id="57f60-175">Status: Done</span></span>

<span data-ttu-id="57f60-176">EF Core 3.0 では、所有されている型を含むすべての依存関係は省略可能です (たとえば、Person.Address は null にすることができます)。</span><span class="sxs-lookup"><span data-stu-id="57f60-176">In EF Core 3.0, all dependents, including owned types are optional (e.g. Person.Address can be null).</span></span> <span data-ttu-id="57f60-177">EF Core 5.0 では、必要に応じて依存関係を構成できます。</span><span class="sxs-lookup"><span data-stu-id="57f60-177">In EF Core 5.0, dependents can be configured as required.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="57f60-178">ToTable、ToQuery、ToView、FromSql などの合理化</span><span class="sxs-lookup"><span data-stu-id="57f60-178">Rationalize ToTable, ToQuery, ToView, FromSql, etc</span></span>

<span data-ttu-id="57f60-179">開発リーダー: @AndriySvyryd、@smitpatel</span><span class="sxs-lookup"><span data-stu-id="57f60-179">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="57f60-180">追跡: [#17270](https://github.com/dotnet/efcore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="57f60-180">Tracked by [#17270](https://github.com/dotnet/efcore/issues/17270)</span></span>

<span data-ttu-id="57f60-181">規模: L</span><span class="sxs-lookup"><span data-stu-id="57f60-181">T-shirt size: L</span></span>

<span data-ttu-id="57f60-182">状態: 完了</span><span class="sxs-lookup"><span data-stu-id="57f60-182">Status: Done</span></span>

<span data-ttu-id="57f60-183">以前のリリースでは、未加工の SQL、キーなしの型、および関連する領域のサポートを強化する作業が行われました。</span><span class="sxs-lookup"><span data-stu-id="57f60-183">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="57f60-184">ただし、すべてが連携して全体として機能するには、まだギャップと不整合の両方があります。</span><span class="sxs-lookup"><span data-stu-id="57f60-184">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="57f60-185">5\.0 での目標は、これらを修正し、異なる型のエンティティとそれに関連付けられたクエリおよびデータベース アイテムを定義、移行、使用するための優れたエクスペリエンスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="57f60-185">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="57f60-186">これには、コンパイル済みクエリ API の更新が含まれる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="57f60-186">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="57f60-187">現在の機能の一部は制限が緩すぎるために簡単に誤る可能性があるので、この項目ではアプリケーション レベルでいくつか破壊的変更が発生する可能性があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="57f60-187">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="57f60-188">このような機能の一部を遮断し、代わりの方法についてのガイダンスを提供することになると思われます。</span><span class="sxs-lookup"><span data-stu-id="57f60-188">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="57f60-189">一般的なクエリの機能強化</span><span class="sxs-lookup"><span data-stu-id="57f60-189">General query enhancements</span></span>

<span data-ttu-id="57f60-190">開発リーダー: @smitpatel、@maumar</span><span class="sxs-lookup"><span data-stu-id="57f60-190">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="57f60-191">追跡: [5.0 のマイルストーンで `area-query` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="57f60-191">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="57f60-192">規模: XL</span><span class="sxs-lookup"><span data-stu-id="57f60-192">T-shirt size: XL</span></span>

<span data-ttu-id="57f60-193">状態: 完了</span><span class="sxs-lookup"><span data-stu-id="57f60-193">Status: Done</span></span>

<span data-ttu-id="57f60-194">クエリ変換コードは、EF Core 3.0 で大幅に書き換えられました。</span><span class="sxs-lookup"><span data-stu-id="57f60-194">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="57f60-195">一般に、このため、クエリ コードはより堅牢な状態になっています。</span><span class="sxs-lookup"><span data-stu-id="57f60-195">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="57f60-196">5\.0 では、TPT とスキップ ナビゲーション プロパティをサポートするために必要な部分を除いて、クエリに関する大きな変更が行われる予定はありません。</span><span class="sxs-lookup"><span data-stu-id="57f60-196">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="57f60-197">ただしそれでも、3.0 の見直しから残っているいくつかの技術的負債を修正するためにかなりの作業が必要です。</span><span class="sxs-lookup"><span data-stu-id="57f60-197">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="57f60-198">また、多くのバグの修正と、クエリ全体のエクスペリエンスをさらに向上させるための小さな機能拡張の実装も予定されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-198">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="57f60-199">移行と展開のエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="57f60-199">Migrations and deployment experience</span></span>

<span data-ttu-id="57f60-200">開発リーダー: @bricelam</span><span class="sxs-lookup"><span data-stu-id="57f60-200">Lead developers: @bricelam</span></span>

<span data-ttu-id="57f60-201">追跡: [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="57f60-201">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="57f60-202">規模: L</span><span class="sxs-lookup"><span data-stu-id="57f60-202">T-shirt size: L</span></span>

<span data-ttu-id="57f60-203">状態: スコープ設定済み/完了</span><span class="sxs-lookup"><span data-stu-id="57f60-203">Status: Scoped/Done</span></span>

<span data-ttu-id="57f60-204">スコーピング:[移行バンドル機能](https://github.com/dotnet/efcore/issues/19693)は、EF Core 5.0 リリースの後まで延期されました。</span><span class="sxs-lookup"><span data-stu-id="57f60-204">Scoping: The [migrations bundles feature](https://github.com/dotnet/efcore/issues/19693) has been deferred until after the EF Core 5.0 release.</span></span> <span data-ttu-id="57f60-205">ただし、その他いくつかの[移行に関連する特定目的の機能強化](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460)は、EF Core 5.0 に含まれます。</span><span class="sxs-lookup"><span data-stu-id="57f60-205">However, several other [targeted improvements related to migrations](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) will be included in EF Core 5.0</span></span>

<span data-ttu-id="57f60-206">現在は、多くの開発者が、アプリケーションの起動時にデータベースを移行しています。</span><span class="sxs-lookup"><span data-stu-id="57f60-206">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="57f60-207">これは簡単ですが、次の理由で推奨されません。</span><span class="sxs-lookup"><span data-stu-id="57f60-207">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="57f60-208">複数のスレッド、プロセス、サーバーによって、データベースの移行が同時に試みられる可能性があります</span><span class="sxs-lookup"><span data-stu-id="57f60-208">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="57f60-209">これが行われている間に、アプリケーションによって不整合な状態へのアクセスが試みられる可能性があります</span><span class="sxs-lookup"><span data-stu-id="57f60-209">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="57f60-210">通常、スキーマを変更するためのデータベース アクセス許可を、アプリケーションの実行に付与してはなりません</span><span class="sxs-lookup"><span data-stu-id="57f60-210">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="57f60-211">何か問題が発生した場合に、クリーンな状態に戻すことが困難です</span><span class="sxs-lookup"><span data-stu-id="57f60-211">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="57f60-212">展開時にデータベースを簡単に移行できる、より良いエクスペリエンスを提供したいと考えています。</span><span class="sxs-lookup"><span data-stu-id="57f60-212">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="57f60-213">これは次のようなものになるはずです。</span><span class="sxs-lookup"><span data-stu-id="57f60-213">This should:</span></span>

* <span data-ttu-id="57f60-214">Linux、Mac、Windows で動作します</span><span class="sxs-lookup"><span data-stu-id="57f60-214">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="57f60-215">コマンド ラインでの適切なエクスペリエンスです</span><span class="sxs-lookup"><span data-stu-id="57f60-215">Be a good experience on the command line</span></span>
* <span data-ttu-id="57f60-216">コンテナーでのシナリオをサポートします</span><span class="sxs-lookup"><span data-stu-id="57f60-216">Support scenarios with containers</span></span>
* <span data-ttu-id="57f60-217">よく使用される実際の展開ツールとフローを使用します</span><span class="sxs-lookup"><span data-stu-id="57f60-217">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="57f60-218">少なくとも Visual Studio に統合されます</span><span class="sxs-lookup"><span data-stu-id="57f60-218">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="57f60-219">結果として、EF Core での多くの小規模な機能向上 (たとえば、SQLite での移行の向上など) と、EF だけではないエンドツーエンドのエクスペリエンスを向上させるための、他のチームによるガイダンスと長期的な共同作業になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="57f60-219">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="57f60-220">EF Core プラットフォームのエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="57f60-220">EF Core platforms experience</span></span>

<span data-ttu-id="57f60-221">開発リーダー: @roji、@bricelam</span><span class="sxs-lookup"><span data-stu-id="57f60-221">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="57f60-222">追跡: [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="57f60-222">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="57f60-223">規模: L</span><span class="sxs-lookup"><span data-stu-id="57f60-223">T-shirt size: L</span></span>

<span data-ttu-id="57f60-224">状態: スコープ設定/完了</span><span class="sxs-lookup"><span data-stu-id="57f60-224">Status: Scope/Done</span></span>

<span data-ttu-id="57f60-225">スコーピング:プラットフォームのガイダンスとサンプルは、Blazor、Xamarin、WinForms、および WPF 向けに公開されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-225">Scoping: Platform guidance and samples are published for Blazor, Xamarin, WinForms, and WPF.</span></span> <span data-ttu-id="57f60-226">Xamarin とその他の AOT またはリンカーの作業は、現在 EF Core 6.0 で予定されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-226">Xamarin and other AOT/linker work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="57f60-227">従来の MVC に似た Web アプリケーションでの EF Core の使用に関しては適切なガイダンスがあります。</span><span class="sxs-lookup"><span data-stu-id="57f60-227">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="57f60-228">他のプラットフォームとアプリケーション モデルについてのガイダンスはないか、または古くなっています。</span><span class="sxs-lookup"><span data-stu-id="57f60-228">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="57f60-229">EF Core 5.0 では、以下のもので EF Core を使用するエクスペリエンスの調査、改善、文書化が計画されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-229">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="57f60-230">Blazor</span><span class="sxs-lookup"><span data-stu-id="57f60-230">Blazor</span></span>
* <span data-ttu-id="57f60-231">Xamarin (AOT とリンカーのストーリーの使用を含む)</span><span class="sxs-lookup"><span data-stu-id="57f60-231">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="57f60-232">WinForms、WPF、WinUI、場合によっては他の UI</span><span class="sxs-lookup"><span data-stu-id="57f60-232">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="57f60-233">frameworks</span><span class="sxs-lookup"><span data-stu-id="57f60-233">frameworks</span></span>

<span data-ttu-id="57f60-234">これは、EF Core での多くの小規模な機能向上と、EF だけではないエンドツーエンドのエクスペリエンスを向上させるための、他のチームによるガイダンスと長期的な共同作業になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="57f60-234">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="57f60-235">ここで検討されている具体的な領域は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="57f60-235">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="57f60-236">展開 (移行などの EF ツールの使用に関するエクスペリエンスを含む)</span><span class="sxs-lookup"><span data-stu-id="57f60-236">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="57f60-237">アプリケーション モデル、(Xamarin と Blazor、場合によってはその他を含む)</span><span class="sxs-lookup"><span data-stu-id="57f60-237">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="57f60-238">SQLite のエクスペリエンス (空間エクスペリエンスとテーブルの再構築を含む)</span><span class="sxs-lookup"><span data-stu-id="57f60-238">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="57f60-239">AOT とリンクのエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="57f60-239">AOT and linking experiences</span></span>
* <span data-ttu-id="57f60-240">診断の統合 (パフォーマンス カウンターを含む)</span><span class="sxs-lookup"><span data-stu-id="57f60-240">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="57f60-241">パフォーマンス</span><span class="sxs-lookup"><span data-stu-id="57f60-241">Performance</span></span>

<span data-ttu-id="57f60-242">開発リーダー: @roji</span><span class="sxs-lookup"><span data-stu-id="57f60-242">Lead developer: @roji</span></span>

<span data-ttu-id="57f60-243">追跡: [5.0 のマイルストーンで `area-perf` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="57f60-243">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="57f60-244">規模: L</span><span class="sxs-lookup"><span data-stu-id="57f60-244">T-shirt size: L</span></span>

<span data-ttu-id="57f60-245">状態: スコープ設定済み/完了</span><span class="sxs-lookup"><span data-stu-id="57f60-245">Status: Scoped/Done</span></span>

<span data-ttu-id="57f60-246">スコーピング:Npgsql プロバイダーでのパフォーマンスの大幅な強化が完了しました。</span><span class="sxs-lookup"><span data-stu-id="57f60-246">Scoping: Major performance improvements in the Npgsql provider are complete.</span></span> <span data-ttu-id="57f60-247">現在、EF Core 6.0 でパフォーマンスに関するその他の作業が予定されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-247">Other performance work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="57f60-248">EF Core では、パフォーマンス ベンチマークのスイートの向上と、ランタイムに対する特定のパフォーマンスの向上が計画されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-248">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="57f60-249">さらに、3.0 リリース サイクル中にプロトタイプ化された新しい ADO.NET バッチ API を完了する予定です。</span><span class="sxs-lookup"><span data-stu-id="57f60-249">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="57f60-250">また、ADO.NET レイヤーでは、Npgsql プロバイダーのさらなるパフォーマンス向上が計画されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-250">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="57f60-251">この作業の一環として、ADO.NET および EF Core パフォーマンス カウンターや他の診断を必要に応じて追加することも計画されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-251">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="57f60-252">アーキテクチャおよび共同作成者向けドキュメント</span><span class="sxs-lookup"><span data-stu-id="57f60-252">Architectural/contributor documentation</span></span>

<span data-ttu-id="57f60-253">ドキュメント作成リーダー: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="57f60-253">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="57f60-254">追跡: [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="57f60-254">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="57f60-255">規模: L</span><span class="sxs-lookup"><span data-stu-id="57f60-255">T-shirt size: L</span></span>

<span data-ttu-id="57f60-256">状態: 切り取り</span><span class="sxs-lookup"><span data-stu-id="57f60-256">Status: Cut</span></span>

<span data-ttu-id="57f60-257">ここでのアイデアは、EF Core の内部で何が起こっているかを理解しやすくすることです。</span><span class="sxs-lookup"><span data-stu-id="57f60-257">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="57f60-258">これは、EF Core を使用するすべてのユーザーにとって便利なことですが、主な目的は、外部ユーザーが次のことを容易にできるようにすることです。</span><span class="sxs-lookup"><span data-stu-id="57f60-258">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="57f60-259">EF Core コードに投稿する</span><span class="sxs-lookup"><span data-stu-id="57f60-259">Contribute to the EF Core code</span></span>
* <span data-ttu-id="57f60-260">データベース プロバイダーを作成する</span><span class="sxs-lookup"><span data-stu-id="57f60-260">Create database providers</span></span>
* <span data-ttu-id="57f60-261">他の拡張機能を作成する</span><span class="sxs-lookup"><span data-stu-id="57f60-261">Build other extensions</span></span>

<span data-ttu-id="57f60-262">更新:残念ながら、この計画は壮大すぎました。</span><span class="sxs-lookup"><span data-stu-id="57f60-262">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="57f60-263">これが重要なことは変わりませんが、残念ながら EF Core 5.0 では実現できません。</span><span class="sxs-lookup"><span data-stu-id="57f60-263">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="57f60-264">Microsoft.Data.Sqlite のドキュメント</span><span class="sxs-lookup"><span data-stu-id="57f60-264">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="57f60-265">ドキュメント作成リーダー: @bricelam</span><span class="sxs-lookup"><span data-stu-id="57f60-265">Lead documenter: @bricelam</span></span>

<span data-ttu-id="57f60-266">追跡: [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="57f60-266">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="57f60-267">規模: M</span><span class="sxs-lookup"><span data-stu-id="57f60-267">T-shirt size: M</span></span>

<span data-ttu-id="57f60-268">状態: 完了しました。</span><span class="sxs-lookup"><span data-stu-id="57f60-268">Status: Completed.</span></span> <span data-ttu-id="57f60-269">新しいドキュメントは、[Microsoft docs サイトで公開](/dotnet/standard/data/sqlite/?tabs=netcore-cli)されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-269">The new documentation is [live on the Microsoft docs site](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="57f60-270">EF チームも、Microsoft.Data.Sqlite ADO.NET プロバイダーを所有しています。</span><span class="sxs-lookup"><span data-stu-id="57f60-270">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="57f60-271">5\.0 リリースの一環として、このプロバイダーの完全な文書化が予定されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-271">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="57f60-272">一般的なドキュメント</span><span class="sxs-lookup"><span data-stu-id="57f60-272">General documentation</span></span>

<span data-ttu-id="57f60-273">ドキュメント作成リーダー: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="57f60-273">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="57f60-274">追跡: [5.0 マイルストーンでのドキュメント リポジトリの問題](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="57f60-274">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="57f60-275">規模: L</span><span class="sxs-lookup"><span data-stu-id="57f60-275">T-shirt size: L</span></span>

<span data-ttu-id="57f60-276">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="57f60-276">Status: In-progress</span></span>

<span data-ttu-id="57f60-277">既に、3.0 および 3.1 リリースのドキュメントの更新が行われています。</span><span class="sxs-lookup"><span data-stu-id="57f60-277">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="57f60-278">また、以下の作業も行われています。</span><span class="sxs-lookup"><span data-stu-id="57f60-278">We are also working on:</span></span>

* <span data-ttu-id="57f60-279">概要ドキュメントをいっそう理解しやすく実行しやすいものにするための見直し</span><span class="sxs-lookup"><span data-stu-id="57f60-279">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
* <span data-ttu-id="57f60-280">検索を容易にして相互参照を追加するためのドキュメントの再編成</span><span class="sxs-lookup"><span data-stu-id="57f60-280">Reorganization of docs to make things easier to find and to add cross-references</span></span>
* <span data-ttu-id="57f60-281">既存ドキュメントの詳細さと明快さの向上</span><span class="sxs-lookup"><span data-stu-id="57f60-281">Adding more details and clarifications to existing docs</span></span>
* <span data-ttu-id="57f60-282">サンプルの更新とその他の例の追加</span><span class="sxs-lookup"><span data-stu-id="57f60-282">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="57f60-283">バグの修正</span><span class="sxs-lookup"><span data-stu-id="57f60-283">Fixing bugs</span></span>

<span data-ttu-id="57f60-284">追跡: [5.0 のマイルストーンで `type-bug` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="57f60-284">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="57f60-285">開発者: @roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="57f60-285">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="57f60-286">規模: L</span><span class="sxs-lookup"><span data-stu-id="57f60-286">T-shirt size: L</span></span>

<span data-ttu-id="57f60-287">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="57f60-287">Status: In-progress</span></span>

<span data-ttu-id="57f60-288">この執筆時点では、5.0 リリースでの修正対象として 135 件のバグがトリアージされていますが (62 件は既に修正済み)、上記の「_一般的なクエリの機能強化_」セクションとかなり重複しています。</span><span class="sxs-lookup"><span data-stu-id="57f60-288">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="57f60-289">発生率 (マイルストーンで作業するようになった問題) は、3.0 リリースの全体を通して、1 か月あたり約 23 件の問題でした。</span><span class="sxs-lookup"><span data-stu-id="57f60-289">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="57f60-290">これらのすべてを 5.0 で修正する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="57f60-290">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="57f60-291">大まかな見積もりとして、5.0 の時間枠では 150 件の問題をさらに修正することが計画されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-291">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="57f60-292">小規模な機能強化</span><span class="sxs-lookup"><span data-stu-id="57f60-292">Small enhancements</span></span>

<span data-ttu-id="57f60-293">追跡: [5.0 のマイルストーンで `type-enhancement` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span><span class="sxs-lookup"><span data-stu-id="57f60-293">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="57f60-294">開発者: @roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="57f60-294">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="57f60-295">規模: L</span><span class="sxs-lookup"><span data-stu-id="57f60-295">T-shirt size: L</span></span>

<span data-ttu-id="57f60-296">状態: 完了</span><span class="sxs-lookup"><span data-stu-id="57f60-296">Status: Done</span></span>

<span data-ttu-id="57f60-297">これまでに説明した比較的大きな機能に加えて、5.0 では、"小さな傷" を修正するために多くの小規模な改善が予定されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-297">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="57f60-298">これらの機能強化の多くは、上で説明した一般的なテーマにも含まれていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="57f60-298">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="57f60-299">対象外</span><span class="sxs-lookup"><span data-stu-id="57f60-299">Below-the-line</span></span>

<span data-ttu-id="57f60-300">追跡: [`consider-for-next-release` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span><span class="sxs-lookup"><span data-stu-id="57f60-300">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="57f60-301">これらは、現時点では 5.0 リリースで予定されて **いない** バグ修正および機能強化ですが、上記の作業の進行状況によっては、努力目標として見られています。</span><span class="sxs-lookup"><span data-stu-id="57f60-301">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="57f60-302">また、計画を立てるときは常に、[最も投票が多かった問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)が考慮されています。</span><span class="sxs-lookup"><span data-stu-id="57f60-302">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="57f60-303">これらの問題をリリースから切り捨てることは常に苦痛ですが、保有するリソースにとって現実的な計画を立てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="57f60-303">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="suggestions"></a><span data-ttu-id="57f60-304">推奨事項</span><span class="sxs-lookup"><span data-stu-id="57f60-304">Suggestions</span></span>

<span data-ttu-id="57f60-305">計画に関するフィードバックは重要です。</span><span class="sxs-lookup"><span data-stu-id="57f60-305">Your feedback on planning is important.</span></span> <span data-ttu-id="57f60-306">イシューの重要度を示す最善の方法は、GitHub でそのイシューに投票 (上向きの親指) することです。</span><span class="sxs-lookup"><span data-stu-id="57f60-306">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="57f60-307">このデータが、次のリリースの[計画プロセス](xref:core/what-is-new/release-planning)に取り込まれます。</span><span class="sxs-lookup"><span data-stu-id="57f60-307">This data will then feed into the [planning process](xref:core/what-is-new/release-planning) for the next release.</span></span>
