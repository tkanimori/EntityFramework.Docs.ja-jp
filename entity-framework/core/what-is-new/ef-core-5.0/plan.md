---
title: Entity Framework Core 5.0 の計画
author: ajcvickers
ms.date: 01/14/2020
uid: core/what-is-new/ef-core-5.0/plan.md
ms.openlocfilehash: 0472841fdcd105ec8ea38db062c6768510b8735d
ms.sourcegitcommit: f2a38c086291699422d8b28a72d9611d1b24ad0d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76125356"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="fe296-102">Entity Framework Core 5.0 の計画</span><span class="sxs-lookup"><span data-stu-id="fe296-102">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="fe296-103">[計画プロセス](../release-planning.md)で説明されているように、EF Core 5.0 リリースの暫定的な計画には関係者からの意見が取り込まれています。</span><span class="sxs-lookup"><span data-stu-id="fe296-103">As described in the [planning process](../release-planning.md), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT] 
> <span data-ttu-id="fe296-104">この計画はまだ進行中です。</span><span class="sxs-lookup"><span data-stu-id="fe296-104">This plan is still a work-in-progress.</span></span> <span data-ttu-id="fe296-105">ここで説明されている内容は確約されたものではありません。</span><span class="sxs-lookup"><span data-stu-id="fe296-105">Nothing here is a commitment.</span></span> <span data-ttu-id="fe296-106">この計画は、さらに学習して発展する出発点となります。</span><span class="sxs-lookup"><span data-stu-id="fe296-106">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="fe296-107">現在、5.0 に対して計画されていない機能が盛り込まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fe296-107">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="fe296-108">現在、5.0 に対して計画されている機能が除外される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fe296-108">Some things currently planned for 5.0 may get punted out.</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="fe296-109">バージョン番号とリリース日</span><span class="sxs-lookup"><span data-stu-id="fe296-109">Version number and release date.</span></span>

<span data-ttu-id="fe296-110">現在、EF Core 5.0 は、[.NET 5.0 と同時に](https://devblogs.microsoft.com/dotnet/introducing-net-5/)リリースされる予定です。</span><span class="sxs-lookup"><span data-stu-id="fe296-110">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="fe296-111">"5.0" というバージョンは、.NET 5.0 に合わせて選択されました。</span><span class="sxs-lookup"><span data-stu-id="fe296-111">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="fe296-112">サポートされているプラットフォーム</span><span class="sxs-lookup"><span data-stu-id="fe296-112">Supported platforms</span></span>

<span data-ttu-id="fe296-113">EF Core 5.0 は、[.NET 5.0 プラットフォームが .NET Core に収束する](https://devblogs.microsoft.com/dotnet/introducing-net-5/)ことに基づいて、任意の .NET 5.0 プラットフォームで動作するように計画されています。</span><span class="sxs-lookup"><span data-stu-id="fe296-113">EF Core 5.0 is planned to run on any .NET 5.0 platform based on the [convergence of these platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="fe296-114">.NET Standard および実際に使用される TFM の観点から、これが何を意味するかについては未定です。</span><span class="sxs-lookup"><span data-stu-id="fe296-114">What this means in terms of .NET Standard and the actual TFM used is still TBD.</span></span>

<span data-ttu-id="fe296-115">EF Core 5.0 は、.NET Framework では実行されません。</span><span class="sxs-lookup"><span data-stu-id="fe296-115">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="fe296-116">互換性に影響する変更</span><span class="sxs-lookup"><span data-stu-id="fe296-116">Breaking changes</span></span>

<span data-ttu-id="fe296-117">EF Core 5.0 には破壊的変更がいくつか含まれていますが、EF Core 3.0 のときほど深刻なものは多くありません。</span><span class="sxs-lookup"><span data-stu-id="fe296-117">EF Core 5.0 will contain some breaking changes, but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="fe296-118">目標は、アプリケーションの大部分を中断せずに更新できるようにすることです。</span><span class="sxs-lookup"><span data-stu-id="fe296-118">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="fe296-119">データベース プロバイダーに対して、破壊的変更がいくつかあることが予想されます (特に TPT のサポートに関して)。</span><span class="sxs-lookup"><span data-stu-id="fe296-119">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="fe296-120">ただし、5.0 対応にプロバイダーを更新する作業は、3.0 の更新に要した時間より短いものと予想されます。</span><span class="sxs-lookup"><span data-stu-id="fe296-120">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="fe296-121">テーマ</span><span class="sxs-lookup"><span data-stu-id="fe296-121">Themes</span></span>

<span data-ttu-id="fe296-122">EF Core 5.0 への大規模な投資の基礎となる、いくつかの主要な領域やテーマが抽出されています。</span><span class="sxs-lookup"><span data-stu-id="fe296-122">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="fe296-123">多対多のナビゲーション プロパティ (別名 "スキップ ナビゲーション")</span><span class="sxs-lookup"><span data-stu-id="fe296-123">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="fe296-124">開発リーダー: @smitpatel、@AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="fe296-124">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="fe296-125">追跡: [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="fe296-125">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="fe296-126">規模: L</span><span class="sxs-lookup"><span data-stu-id="fe296-126">T-shirt size: L</span></span>

<span data-ttu-id="fe296-127">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="fe296-127">Status: In-progress</span></span>

<span data-ttu-id="fe296-128">多対多は、GitHub バックログで最も要求の多かった機能です (407 票)。</span><span class="sxs-lookup"><span data-stu-id="fe296-128">Many-to-many is the most requested feature (~407 votes) on the GitHub backlog.</span></span> <span data-ttu-id="fe296-129">多対多リレーションシップのサポートは、次の 3 つの主要領域に分けることができます。</span><span class="sxs-lookup"><span data-stu-id="fe296-129">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="fe296-130">スキップ ナビゲーション プロパティ。</span><span class="sxs-lookup"><span data-stu-id="fe296-130">Skip navigation properties.</span></span> <span data-ttu-id="fe296-131">これにより、基になる結合テーブル エンティティを参照することなく、クエリなどにモデルを使用できます。</span><span class="sxs-lookup"><span data-stu-id="fe296-131">These allow the model to be used for queries, etc. without reference to the underlying join table entity.</span></span>
* <span data-ttu-id="fe296-132">property-bag エンティティ型。</span><span class="sxs-lookup"><span data-stu-id="fe296-132">Property-bag entity types.</span></span> <span data-ttu-id="fe296-133">これにより、標準の CLR 型 (例: `Dictionary`) をエンティティ インスタンスに使用できるようになり、エンティティ型ごとに明示的な CLR 型は不要になります。</span><span class="sxs-lookup"><span data-stu-id="fe296-133">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span>
* <span data-ttu-id="fe296-134">多対多リレーションシップを簡単に構成するための Sugar。</span><span class="sxs-lookup"><span data-stu-id="fe296-134">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="fe296-135">多対多のサポートを望んでいるユーザーにとって最も大きな阻害要因は、クエリなどのビジネス ロジックにおいて、結合テーブルを参照せずに、"自然な" リレーションシップを使用できないことであると考えています。</span><span class="sxs-lookup"><span data-stu-id="fe296-135">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span> <span data-ttu-id="fe296-136">結合テーブルのエンティティ型がまだ存在する場合がありますが、ビジネス ロジックを妨げることはないはずです。</span><span class="sxs-lookup"><span data-stu-id="fe296-136">The join table entity type may still exist, but it should not get in the way of business logic.</span></span> <span data-ttu-id="fe296-137">5\.0 でスキップ ナビゲーション プロパティに取り組んだのはそのためです。</span><span class="sxs-lookup"><span data-stu-id="fe296-137">This is why we have chosen to tackle skip navigation properties for 5.0.</span></span>

<span data-ttu-id="fe296-138">現時点では、多対多の他の部分は、EF Core 5.0 の努力目標として調査中です。</span><span class="sxs-lookup"><span data-stu-id="fe296-138">At this time the other parts of many-to-many are being pursued as a stretch goal for EF Core 5.0.</span></span> <span data-ttu-id="fe296-139">つまり、現在は 5.0 の計画に含まれていませんが、うまくいきそうであれば、盛り込みたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="fe296-139">This means they are not currently in the plan for 5.0, but if things go well we hope to pull them in.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="fe296-140">Table-Per-Type (TPT) の継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="fe296-140">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="fe296-141">開発リーダー: @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="fe296-141">Lead developer: @AndriySvyryd</span></span>

<span data-ttu-id="fe296-142">追跡: [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="fe296-142">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="fe296-143">規模: XL</span><span class="sxs-lookup"><span data-stu-id="fe296-143">T-shirt size: XL</span></span>

<span data-ttu-id="fe296-144">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="fe296-144">Status: Not started</span></span>

<span data-ttu-id="fe296-145">TPT の作業が行われているのは、要求の多かった機能であることと (254 票、全体で 3 番目)、.NET 5 の全体的な計画の基本特性に適していると考えられる低レベルの変更が必要であるためです。</span><span class="sxs-lookup"><span data-stu-id="fe296-145">We're doing TPT because it is both a highly requested feature (~254 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="fe296-146">これにより、データベース プロバイダーに対して破壊的変更が行われることが予想されますが、3.0 で必要であった変更ほど大がかりにはならないはずです。</span><span class="sxs-lookup"><span data-stu-id="fe296-146">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="fe296-147">フィルター処理されたインクルード</span><span class="sxs-lookup"><span data-stu-id="fe296-147">Filtered Include</span></span>

<span data-ttu-id="fe296-148">開発リーダー: @maumar</span><span class="sxs-lookup"><span data-stu-id="fe296-148">Lead developer: @maumar</span></span>

<span data-ttu-id="fe296-149">追跡: [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="fe296-149">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="fe296-150">規模: M</span><span class="sxs-lookup"><span data-stu-id="fe296-150">T-shirt size: M</span></span>

<span data-ttu-id="fe296-151">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="fe296-151">Status: Not started</span></span>

<span data-ttu-id="fe296-152">フィルター処理されたインクルードは、要求の多かった機能であり (317 票、全体で 2 番目)、作業量は多くなく、現在はモデル レベルのフィルターやさらに複雑なクエリを必要とする多くのシナリオの障害が取り除かれたり、実現がいっそう簡単になるものと考えられます。</span><span class="sxs-lookup"><span data-stu-id="fe296-152">Filtered Include is a highly-requested feature (~317 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="fe296-153">ToTable、ToQuery、ToView、FromSql などの合理化</span><span class="sxs-lookup"><span data-stu-id="fe296-153">Rationalize ToTable, ToQuery, ToView, FromSql, etc.</span></span>

<span data-ttu-id="fe296-154">開発リーダー: @maumar、@smitpatel</span><span class="sxs-lookup"><span data-stu-id="fe296-154">Lead developers: @maumar and @smitpatel</span></span>

<span data-ttu-id="fe296-155">追跡: [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="fe296-155">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="fe296-156">規模: L</span><span class="sxs-lookup"><span data-stu-id="fe296-156">T-shirt size: L</span></span>

<span data-ttu-id="fe296-157">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="fe296-157">Status: Not started</span></span>

<span data-ttu-id="fe296-158">以前のリリースでは、未加工の SQL、キーなしの型、および関連する領域のサポートを強化する作業が行われました。</span><span class="sxs-lookup"><span data-stu-id="fe296-158">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="fe296-159">ただし、すべてが連携して全体として機能するには、まだギャップと不整合の両方があります。</span><span class="sxs-lookup"><span data-stu-id="fe296-159">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="fe296-160">5\.0 での目標は、これらを修正し、異なる型のエンティティとそれに関連付けられたクエリおよびデータベース アイテムを定義、移行、使用するための優れたエクスペリエンスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="fe296-160">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="fe296-161">これには、コンパイル済みクエリ API の更新が含まれる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="fe296-161">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="fe296-162">現在の機能の一部は制限が緩すぎるために簡単に誤る可能性があるので、この項目ではアプリケーション レベルでいくつか破壊的変更が発生する可能性があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fe296-162">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="fe296-163">このような機能の一部を遮断し、代わりの方法についてのガイダンスを提供することになると思われます。</span><span class="sxs-lookup"><span data-stu-id="fe296-163">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="fe296-164">一般的なクエリの機能強化</span><span class="sxs-lookup"><span data-stu-id="fe296-164">General query enhancements</span></span>

<span data-ttu-id="fe296-165">開発リーダー: @smitpatel、@maumar</span><span class="sxs-lookup"><span data-stu-id="fe296-165">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="fe296-166">追跡: [5.0 のマイルストーンで `area-query` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="fe296-166">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="fe296-167">規模: XL</span><span class="sxs-lookup"><span data-stu-id="fe296-167">T-shirt size: XL</span></span>

<span data-ttu-id="fe296-168">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="fe296-168">Status: In-progress</span></span>

<span data-ttu-id="fe296-169">クエリ変換コードは、EF Core 3.0 で大幅に書き換えられました。</span><span class="sxs-lookup"><span data-stu-id="fe296-169">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="fe296-170">一般に、このため、クエリ コードはより堅牢な状態になっています。</span><span class="sxs-lookup"><span data-stu-id="fe296-170">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="fe296-171">5\.0 では、TPT とスキップ ナビゲーション プロパティをサポートするために必要な部分を除いて、クエリに関する大きな変更が行われる予定はありません。</span><span class="sxs-lookup"><span data-stu-id="fe296-171">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="fe296-172">ただしそれでも、3.0 の見直しから残っているいくつかの技術的負債を修正するためにかなりの作業が必要です。</span><span class="sxs-lookup"><span data-stu-id="fe296-172">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="fe296-173">また、多くのバグの修正と、クエリ全体のエクスペリエンスをさらに向上させるための小さな機能拡張の実装も予定されています。</span><span class="sxs-lookup"><span data-stu-id="fe296-173">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="fe296-174">移行と展開のエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="fe296-174">Migrations and deployment experience</span></span>

<span data-ttu-id="fe296-175">開発リーダー: @bricelam</span><span class="sxs-lookup"><span data-stu-id="fe296-175">Lead developers: @bricelam</span></span>

<span data-ttu-id="fe296-176">追跡: [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="fe296-176">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="fe296-177">規模: L</span><span class="sxs-lookup"><span data-stu-id="fe296-177">T-shirt size: L</span></span>

<span data-ttu-id="fe296-178">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="fe296-178">Status: In-progress</span></span>

<span data-ttu-id="fe296-179">現在は、多くの開発者が、アプリケーションの起動時にデータベースを移行しています。</span><span class="sxs-lookup"><span data-stu-id="fe296-179">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="fe296-180">これは簡単ですが、次の理由で推奨されません。</span><span class="sxs-lookup"><span data-stu-id="fe296-180">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="fe296-181">複数のスレッド、プロセス、サーバーによって、データベースの移行が同時に試みられる可能性があります</span><span class="sxs-lookup"><span data-stu-id="fe296-181">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="fe296-182">これが行われている間に、アプリケーションによって不整合な状態へのアクセスが試みられる可能性があります</span><span class="sxs-lookup"><span data-stu-id="fe296-182">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="fe296-183">通常、スキーマを変更するためのデータベース アクセス許可を、アプリケーションの実行に付与してはなりません</span><span class="sxs-lookup"><span data-stu-id="fe296-183">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="fe296-184">何か問題が発生した場合に、クリーンな状態に戻すことが困難です</span><span class="sxs-lookup"><span data-stu-id="fe296-184">Its hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="fe296-185">展開時にデータベースを簡単に移行できる、より良いエクスペリエンスを提供したいと考えています。</span><span class="sxs-lookup"><span data-stu-id="fe296-185">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="fe296-186">これは次のようなものになるはずです。</span><span class="sxs-lookup"><span data-stu-id="fe296-186">This should:</span></span>

* <span data-ttu-id="fe296-187">Linux、Mac、Windows で動作します</span><span class="sxs-lookup"><span data-stu-id="fe296-187">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="fe296-188">コマンド ラインでの適切なエクスペリエンスです</span><span class="sxs-lookup"><span data-stu-id="fe296-188">Be a good experience on the command line</span></span>
* <span data-ttu-id="fe296-189">コンテナーでのシナリオをサポートします</span><span class="sxs-lookup"><span data-stu-id="fe296-189">Support scenarios with containers</span></span>
* <span data-ttu-id="fe296-190">よく使用される実際の展開ツールとフローを使用します</span><span class="sxs-lookup"><span data-stu-id="fe296-190">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="fe296-191">少なくとも Visual Studio に統合されます</span><span class="sxs-lookup"><span data-stu-id="fe296-191">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="fe296-192">結果として、EF Core での多くの小規模な機能向上 (たとえば、SQLite での移行の向上など) と、EF だけではないエンドツーエンドのエクスペリエンスを向上させるための、他のチームによるガイダンスと長期的な共同作業になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fe296-192">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="fe296-193">EF Core プラットフォームのエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="fe296-193">EF Core platforms experience</span></span> 

<span data-ttu-id="fe296-194">開発リーダー: @roji、@bricelam</span><span class="sxs-lookup"><span data-stu-id="fe296-194">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="fe296-195">追跡: [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="fe296-195">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="fe296-196">規模: L</span><span class="sxs-lookup"><span data-stu-id="fe296-196">T-shirt size: L</span></span>

<span data-ttu-id="fe296-197">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="fe296-197">Status: Not started</span></span>

<span data-ttu-id="fe296-198">従来の MVC に似た Web アプリケーションでの EF Core の使用に関しては適切なガイダンスがあります。</span><span class="sxs-lookup"><span data-stu-id="fe296-198">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="fe296-199">他のプラットフォームとアプリケーション モデルについてのガイダンスはないか、または古くなっています。</span><span class="sxs-lookup"><span data-stu-id="fe296-199">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="fe296-200">EF Core 5.0 では、以下のもので EF Core を使用するエクスペリエンスの調査、改善、文書化が計画されています。</span><span class="sxs-lookup"><span data-stu-id="fe296-200">For EF Core 5.0 we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="fe296-201">Blazor</span><span class="sxs-lookup"><span data-stu-id="fe296-201">Blazor</span></span>
* <span data-ttu-id="fe296-202">Xamarin (AOT とリンカーのストーリーの使用を含む)</span><span class="sxs-lookup"><span data-stu-id="fe296-202">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="fe296-203">WinForms、WPF、WinUI、場合によっては他の UI</span><span class="sxs-lookup"><span data-stu-id="fe296-203">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="fe296-204">フレームワーク</span><span class="sxs-lookup"><span data-stu-id="fe296-204">frameworks</span></span>

<span data-ttu-id="fe296-205">これは、EF Core での多くの小規模な機能向上と、EF だけではないエンドツーエンドのエクスペリエンスを向上させるための、他のチームによるガイダンスと長期的な共同作業になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fe296-205">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="fe296-206">ここで検討されている具体的な領域は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="fe296-206">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="fe296-207">展開 (移行などの EF ツールの使用に関するエクスペリエンスを含む)</span><span class="sxs-lookup"><span data-stu-id="fe296-207">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="fe296-208">アプリケーション モデル、(Xamarin と Blazor、場合によってはその他を含む)</span><span class="sxs-lookup"><span data-stu-id="fe296-208">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="fe296-209">SQLite のエクスペリエンス (空間エクスペリエンスとテーブルの再構築を含む)</span><span class="sxs-lookup"><span data-stu-id="fe296-209">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="fe296-210">AOT とリンクのエクスペリエンス</span><span class="sxs-lookup"><span data-stu-id="fe296-210">AOT and linking experiences</span></span>
* <span data-ttu-id="fe296-211">診断の統合 (パフォーマンス カウンターを含む)</span><span class="sxs-lookup"><span data-stu-id="fe296-211">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="fe296-212">パフォーマンス</span><span class="sxs-lookup"><span data-stu-id="fe296-212">Performance</span></span>

<span data-ttu-id="fe296-213">開発リーダー: @roji</span><span class="sxs-lookup"><span data-stu-id="fe296-213">Lead developer: @roji</span></span>

<span data-ttu-id="fe296-214">追跡: [5.0 のマイルストーンで `area-perf` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="fe296-214">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="fe296-215">規模: L</span><span class="sxs-lookup"><span data-stu-id="fe296-215">T-shirt size: L</span></span>

<span data-ttu-id="fe296-216">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="fe296-216">Status: In-progress</span></span>

<span data-ttu-id="fe296-217">EF Core では、パフォーマンス ベンチマークのスイートの向上と、ランタイムのパフォーマンスの向上が計画されています。</span><span class="sxs-lookup"><span data-stu-id="fe296-217">For EF Core we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="fe296-218">さらに、3.0 リリース サイクル中にプロトタイプ化された新しい ADO.NET バッチ API を完了する予定です。</span><span class="sxs-lookup"><span data-stu-id="fe296-218">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="fe296-219">また、ADO.NET レイヤーでは、Npgsql プロバイダーのさらなるパフォーマンス向上が計画されています。</span><span class="sxs-lookup"><span data-stu-id="fe296-219">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="fe296-220">この作業の一環として、ADO.NET および EF Core パフォーマンス カウンターや他の診断を必要に応じて追加することも計画されています。</span><span class="sxs-lookup"><span data-stu-id="fe296-220">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="fe296-221">アーキテクチャおよび共同作成者向けドキュメント</span><span class="sxs-lookup"><span data-stu-id="fe296-221">Architectural/contributor documentation</span></span>

<span data-ttu-id="fe296-222">ドキュメント作成リーダー: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="fe296-222">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="fe296-223">追跡: [#1920](https://github.com/aspnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="fe296-223">Tracked by [#1920](https://github.com/aspnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="fe296-224">規模: L</span><span class="sxs-lookup"><span data-stu-id="fe296-224">T-shirt size: L</span></span>

<span data-ttu-id="fe296-225">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="fe296-225">Status: Not started</span></span>

<span data-ttu-id="fe296-226">ここでのアイデアは、EF Core の内部で何が起こっているかを理解しやすくすることです。</span><span class="sxs-lookup"><span data-stu-id="fe296-226">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="fe296-227">これは、EF Core を使用するすべてのユーザーにとって便利なことですが、主な目的は、外部ユーザーが次のことを容易にできるようにすることです。</span><span class="sxs-lookup"><span data-stu-id="fe296-227">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="fe296-228">EF Core コードに投稿する</span><span class="sxs-lookup"><span data-stu-id="fe296-228">Contribute to the EF Core code</span></span>
* <span data-ttu-id="fe296-229">データベース プロバイダーを作成する</span><span class="sxs-lookup"><span data-stu-id="fe296-229">Create database providers</span></span>
* <span data-ttu-id="fe296-230">他の拡張機能を作成する</span><span class="sxs-lookup"><span data-stu-id="fe296-230">Build other extensions</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="fe296-231">Microsoft.Data.Sqlite のドキュメント</span><span class="sxs-lookup"><span data-stu-id="fe296-231">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="fe296-232">ドキュメント作成リーダー: @bricelam</span><span class="sxs-lookup"><span data-stu-id="fe296-232">Lead documenter: @bricelam</span></span>

<span data-ttu-id="fe296-233">追跡: [#1675](https://github.com/aspnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="fe296-233">Tracked by [#1675](https://github.com/aspnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="fe296-234">規模: M</span><span class="sxs-lookup"><span data-stu-id="fe296-234">T-shirt size: M</span></span>

<span data-ttu-id="fe296-235">状態: 完了しました。</span><span class="sxs-lookup"><span data-stu-id="fe296-235">Status: Completed.</span></span> <span data-ttu-id="fe296-236">新しいドキュメントは、[Microsoft docs サイトで公開](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli)されています。</span><span class="sxs-lookup"><span data-stu-id="fe296-236">The new documentation is [live on the Microsoft docs site](https://docs.microsoft.com/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="fe296-237">EF チームも、Microsoft.Data.Sqlite ADO.NET プロバイダーを所有しています。</span><span class="sxs-lookup"><span data-stu-id="fe296-237">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="fe296-238">5\.0 リリースの一環として、このプロバイダーの完全な文書化が予定されています。</span><span class="sxs-lookup"><span data-stu-id="fe296-238">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="fe296-239">一般的なドキュメント</span><span class="sxs-lookup"><span data-stu-id="fe296-239">General documentation</span></span>

<span data-ttu-id="fe296-240">ドキュメント作成リーダー: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="fe296-240">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="fe296-241">追跡: [5.0 マイルストーンでのドキュメント リポジトリの問題](https://github.com/aspnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="fe296-241">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/aspnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="fe296-242">規模: L</span><span class="sxs-lookup"><span data-stu-id="fe296-242">T-shirt size: L</span></span>

<span data-ttu-id="fe296-243">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="fe296-243">Status: In-progress</span></span>

<span data-ttu-id="fe296-244">既に、3.0 および 3.1 リリースのドキュメントの更新が行われています。</span><span class="sxs-lookup"><span data-stu-id="fe296-244">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="fe296-245">また、以下の作業も行われています。</span><span class="sxs-lookup"><span data-stu-id="fe296-245">We are also working on:</span></span>
  * <span data-ttu-id="fe296-246">概要ドキュメントをいっそう理解しやすく実行しやすいものにするための見直し</span><span class="sxs-lookup"><span data-stu-id="fe296-246">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
  * <span data-ttu-id="fe296-247">検索を容易にして相互参照を追加するためのドキュメントの再編成</span><span class="sxs-lookup"><span data-stu-id="fe296-247">Reorganization of docs to make things easier to find and to add cross-references</span></span>
  * <span data-ttu-id="fe296-248">既存ドキュメントの詳細さと明快さの向上</span><span class="sxs-lookup"><span data-stu-id="fe296-248">Adding more details and clarifications to existing docs</span></span>
  * <span data-ttu-id="fe296-249">サンプルの更新とその他の例の追加</span><span class="sxs-lookup"><span data-stu-id="fe296-249">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="fe296-250">バグの修正</span><span class="sxs-lookup"><span data-stu-id="fe296-250">Fixing bugs</span></span>

<span data-ttu-id="fe296-251">追跡: [5.0 のマイルストーンで `type-bug` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="fe296-251">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="fe296-252">開発者: @roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="fe296-252">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="fe296-253">規模: L</span><span class="sxs-lookup"><span data-stu-id="fe296-253">T-shirt size: L</span></span>

<span data-ttu-id="fe296-254">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="fe296-254">Status: In-progress</span></span>

<span data-ttu-id="fe296-255">この執筆時点では、5.0 リリースでの修正対象として 135 件のバグがトリアージされていますが (62 件は既に修正済み)、上記の「_一般的なクエリの機能強化_」セクションとかなり重複しています。</span><span class="sxs-lookup"><span data-stu-id="fe296-255">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="fe296-256">発生率 (マイルストーンで作業するようになった問題) は、3.0 リリースの全体を通して、1 か月あたり約 23 件の問題でした。</span><span class="sxs-lookup"><span data-stu-id="fe296-256">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="fe296-257">これらのすべてを 5.0 で修正する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="fe296-257">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="fe296-258">大まかな見積もりとして、5.0 の時間枠では 150 件の問題をさらに修正することが計画されています。</span><span class="sxs-lookup"><span data-stu-id="fe296-258">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="fe296-259">小規模な機能強化</span><span class="sxs-lookup"><span data-stu-id="fe296-259">Small enhancements</span></span>

<span data-ttu-id="fe296-260">追跡: [5.0 のマイルストーンで `type-enhancement` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span><span class="sxs-lookup"><span data-stu-id="fe296-260">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="fe296-261">開発者: @roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers</span><span class="sxs-lookup"><span data-stu-id="fe296-261">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="fe296-262">規模: L</span><span class="sxs-lookup"><span data-stu-id="fe296-262">T-shirt size: L</span></span>

<span data-ttu-id="fe296-263">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="fe296-263">Status: In-progress</span></span>

<span data-ttu-id="fe296-264">これまでに説明した比較的大きな機能に加えて、5.0 では、"小さな傷" を修正するために多くの小規模な改善が予定されています。</span><span class="sxs-lookup"><span data-stu-id="fe296-264">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="fe296-265">これらの機能強化の多くは、上で説明した一般的なテーマにも含まれていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fe296-265">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="fe296-266">対象外</span><span class="sxs-lookup"><span data-stu-id="fe296-266">Below-the-line</span></span>

<span data-ttu-id="fe296-267">追跡: [`consider-for-next-release` というラベルが付けられている問題](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span><span class="sxs-lookup"><span data-stu-id="fe296-267">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="fe296-268">これらは、現時点では 5.0 リリースで予定されて**いない**バグ修正および機能強化ですが、上記の作業の進行状況によっては、努力目標として見られています。</span><span class="sxs-lookup"><span data-stu-id="fe296-268">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="fe296-269">また、計画を立てるときは常に、[最も投票が多かった問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)が考慮されています。</span><span class="sxs-lookup"><span data-stu-id="fe296-269">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="fe296-270">これらの問題をリリースから切り捨てることは常に苦痛ですが、保有するリソースにとって現実的な計画を立てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="fe296-270">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="feedback"></a><span data-ttu-id="fe296-271">フィードバック</span><span class="sxs-lookup"><span data-stu-id="fe296-271">Feedback</span></span>

<span data-ttu-id="fe296-272">計画に関するフィードバックは重要です。</span><span class="sxs-lookup"><span data-stu-id="fe296-272">Your feedback on planning is important.</span></span> <span data-ttu-id="fe296-273">イシューの重要度を示す最善の方法は、GitHub でそのイシューに投票 (上向きの親指) することです。</span><span class="sxs-lookup"><span data-stu-id="fe296-273">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="fe296-274">このデータが、次のリリースの[計画プロセス](../release-planning.md)に取り込まれます。</span><span class="sxs-lookup"><span data-stu-id="fe296-274">This data will then feed into the [planning process](../release-planning.md) for the next release.</span></span>
