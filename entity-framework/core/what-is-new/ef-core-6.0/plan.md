---
title: Entity Framework Core 6.0 の計画
description: EF Core 6.0 に対して計画されているテーマと機能
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/ef-core-6.0/plan
ms.openlocfilehash: 612461bc6ad30778baa5c6d10dda5cabac91dcb2
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129551"
---
# <a name="plan-for-entity-framework-core-60"></a><span data-ttu-id="cf8dd-103">Entity Framework Core 6.0 の計画</span><span class="sxs-lookup"><span data-stu-id="cf8dd-103">Plan for Entity Framework Core 6.0</span></span>

<span data-ttu-id="cf8dd-104">[計画プロセス](xref:core/what-is-new/release-planning)で説明されているように、Entity Framework Core (EF Core) 6.0 リリースの計画には関係者からの意見が取り入れられています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-104">As described in the [planning process](xref:core/what-is-new/release-planning), we have gathered input from stakeholders into a plan for the Entity Framework Core (EF Core) 6.0 release.</span></span>

<span data-ttu-id="cf8dd-105">これまでのリリースとは異なり、この計画では、6.0 リリースのすべての作業を対象にすることは予定されていません。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-105">Unlike previous releases, this plan does not attempt to cover all work for the 6.0 release.</span></span> <span data-ttu-id="cf8dd-106">代わりに、このリリースで投資する場所と方法を示しますが、リリースの作業を行いながらフィードバックを集めて学習し、柔軟に新しい作業の範囲を調整したり作業に取り入れたりします。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-106">Instead, it indicates where and how we intend to invest in this release, but with flexibility to adjust scope or pull in new work as we gather feedback and learn while working on the release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cf8dd-107">このプランは確約ではありません。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-107">This plan is not a commitment.</span></span> <span data-ttu-id="cf8dd-108">さらに学習して進化する出発点となります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-108">It is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="cf8dd-109">現在、6.0 で計画されていない機能が盛り込まれる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-109">Some things not currently planned for 6.0 may get pulled in.</span></span> <span data-ttu-id="cf8dd-110">現在、6.0 で計画されている機能が除外される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-110">Some things currently planned for 6.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="cf8dd-111">一般情報</span><span class="sxs-lookup"><span data-stu-id="cf8dd-111">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="cf8dd-112">バージョン番号とリリース日</span><span class="sxs-lookup"><span data-stu-id="cf8dd-112">Version number and release date</span></span>

<span data-ttu-id="cf8dd-113">EF Core 6.0 は、5.0 EF Core に続く次のリリースであり、現時点では、2021 年 11 月に .NET 6 と同時にリリースされる予定です。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-113">EF Core 6.0 is the next release after EF Core 5.0 and is currently scheduled for release in November 2021 at the same time as .NET 6.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="cf8dd-114">[サポートされているプラットフォーム]</span><span class="sxs-lookup"><span data-stu-id="cf8dd-114">Supported platforms</span></span>

<span data-ttu-id="cf8dd-115">現在、EF Core 6.0 の対象は .NET 5 です。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-115">EF Core 6.0 currently targets .NET 5.</span></span> <span data-ttu-id="cf8dd-116">これは、リリースが近くなった時点で .NET 6 に更新される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-116">This will likely be updated to .NET 6 as we near the release.</span></span> <span data-ttu-id="cf8dd-117">EF Core 6.0 では、.NET Standard のどのバージョンも対象になっていません。詳細については、「[.NET Standard の将来](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-117">EF Core 6.0 does not target any .NET Standard version; for more information see [the future of .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span></span>

<span data-ttu-id="cf8dd-118">EF Core 6.0 は、.NET Framework では実行されません。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-118">EF Core 6.0 will not run on .NET Framework.</span></span>

<span data-ttu-id="cf8dd-119">EF Core 6.0 は、[長期的なサポート (LTS) リリース](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)として .NET 6 と連携します。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-119">EF Core 6.0 will align with .NET 6 as a [long-term support (LTS) release](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="cf8dd-120">重大な変更</span><span class="sxs-lookup"><span data-stu-id="cf8dd-120">Breaking changes</span></span>

<span data-ttu-id="cf8dd-121">EF Core と .NET プラットフォームの両方の機能強化が継続的に行われているため、EF Core 6.0 に含まれる[破壊的変更](xref:core/what-is-new/ef-core-6.0/breaking-changes)の数は多くありません。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-121">EF Core 6.0 will contain a small number of [breaking changes](xref:core/what-is-new/ef-core-6.0/breaking-changes) as we continue to evolve both EF Core and the .NET platform.</span></span> <span data-ttu-id="cf8dd-122">目標は、アプリケーションの大部分を中断せずに更新できるようにすることです。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-122">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

## <a name="themes"></a><span data-ttu-id="cf8dd-123">テーマ</span><span class="sxs-lookup"><span data-stu-id="cf8dd-123">Themes</span></span>

<span data-ttu-id="cf8dd-124">EF Core 6.0 では、次の領域の基礎を形成するために大きな投資が注ぎ込まれます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-124">The following areas will form the basis for the large investments in EF Core 6.0.</span></span>

## <a name="highly-requested-features"></a><span data-ttu-id="cf8dd-125">要求の多かった機能</span><span class="sxs-lookup"><span data-stu-id="cf8dd-125">Highly requested features</span></span>

<span data-ttu-id="cf8dd-126">これまでと同じように、[計画プロセス](xref:core/what-is-new/release-planning)の基になっているものは主に、[GitHub でのフィーチャーへの投票 (👍)](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) です。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-126">As always, a major input into the [planning process](xref:core/what-is-new/release-planning) comes from the [voting (👍) for features on GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span></span> <span data-ttu-id="cf8dd-127">EF Core 6.0 では、次のような要求の多かったフィーチャーについての作業が行われる予定です。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-127">For EF Core 6.0 we plan to work on the following highly requested features:</span></span>

### <a name="sql-server-temporal-tables"></a><span data-ttu-id="cf8dd-128">SQL Server のテンポラル テーブル</span><span class="sxs-lookup"><span data-stu-id="cf8dd-128">SQL Server temporal tables</span></span>

<span data-ttu-id="cf8dd-129">追跡: [#4693](https://github.com/dotnet/efcore/issues/4693)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-129">Tracked by [#4693](https://github.com/dotnet/efcore/issues/4693)</span></span>

<span data-ttu-id="cf8dd-130">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-130">Status: Not started</span></span>

<span data-ttu-id="cf8dd-131">規模: 大</span><span class="sxs-lookup"><span data-stu-id="cf8dd-131">T-shirt size: Large</span></span>

<span data-ttu-id="cf8dd-132">テンポラル テーブルを使用すると、通常のテーブルのように格納された最新のデータのみに対してではなく、テーブルに格納された _任意の時点_ のデータに対するクエリの実行をサポートします。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-132">Temporal tables support queries for data stored in the table at _any point in time_, as opposed to only the most recent data stored as is the case for normal tables.</span></span> <span data-ttu-id="cf8dd-133">EF Core 6.0 の場合、Migrations によってテンポラル テーブルを作成できるだけでなく、LINQ クエリを使用してデータにアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-133">EF Core 6.0 will allow temporal tables to be created via Migrations, as well as allowing access to the data through LINQ queries.</span></span>

<span data-ttu-id="cf8dd-134">この作業の初期のスコープは、[イシューで説明](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974)されています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-134">This work is initially scoped as [described on the issue](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974).</span></span> <span data-ttu-id="cf8dd-135">リリース時のフィードバックに基づいて、サポートが追加される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-135">We may pull in additional support based on feedback during the release.</span></span>

### <a name="json-columns"></a><span data-ttu-id="cf8dd-136">JSON 列</span><span class="sxs-lookup"><span data-stu-id="cf8dd-136">JSON columns</span></span>

<span data-ttu-id="cf8dd-137">追跡: [#4021](https://github.com/dotnet/efcore/issues/4021)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-137">Tracked by [#4021](https://github.com/dotnet/efcore/issues/4021)</span></span>

<span data-ttu-id="cf8dd-138">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-138">Status: Not started</span></span>

<span data-ttu-id="cf8dd-139">規模: Medium</span><span class="sxs-lookup"><span data-stu-id="cf8dd-139">T-shirt size: Medium</span></span>

<span data-ttu-id="cf8dd-140">この機能により、任意のデータベース プロバイダーによって実装できる JSON サポートのための一般的なメカニズムとパターンが導入されます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-140">This feature will introduce a common mechanism and patterns for JSON support that can be implemented by any database provider.</span></span> <span data-ttu-id="cf8dd-141">コミュニティと協力して、[Npgsql](https://github.com/npgsql/efcore.pg) および [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) の既存の実装を調整し、SQL Server と SQLite のサポートも追加します。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-141">We will work with the community to align existing implementations for [Npgsql](https://github.com/npgsql/efcore.pg) and [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and also add support for SQL Server and SQLite.</span></span>

### <a name="columnattributeorder"></a><span data-ttu-id="cf8dd-142">ColumnAttribute.Order</span><span class="sxs-lookup"><span data-stu-id="cf8dd-142">ColumnAttribute.Order</span></span>

<span data-ttu-id="cf8dd-143">追跡: [#10059](https://github.com/dotnet/efcore/issues/10059)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-143">Tracked by [#10059](https://github.com/dotnet/efcore/issues/10059)</span></span>

<span data-ttu-id="cf8dd-144">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-144">Status: Not started</span></span>

<span data-ttu-id="cf8dd-145">規模: 小</span><span class="sxs-lookup"><span data-stu-id="cf8dd-145">T-shirt size: Small</span></span>

<span data-ttu-id="cf8dd-146">この機能を使用すると、Migrations または `EnsureCreated` で **テーブルを作成する** ときに、列を任意の順序にすることができます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-146">This feature will allow arbitrary ordering of columns when **creating a table** with Migrations or `EnsureCreated`.</span></span> <span data-ttu-id="cf8dd-147">既存のテーブルの列の順序を変更するには、テーブルを再構築する必要があることに注意してください。この機能のサポートは、EF Core のリリースでは予定されていません。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-147">Note that changing the order of columns in an existing tables requires that the table be rebuilt, and this is not something that we plan to support in any EF Core release.</span></span>

## <a name="performance"></a><span data-ttu-id="cf8dd-148">パフォーマンス</span><span class="sxs-lookup"><span data-stu-id="cf8dd-148">Performance</span></span>

<span data-ttu-id="cf8dd-149">EF Core は一般に EF6 より高速ですが、パフォーマンスの大幅な向上が可能な領域がまだあります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-149">While EF Core is generally faster than EF6, there are still areas where significant improvements in performance are possible.</span></span> <span data-ttu-id="cf8dd-150">これらの領域のいくつかには EF Core 6.0 で取り組むことが計画されていますが、パフォーマンスのインフラストラクチャとテストも改善されています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-150">We plan to tackle several of these areas in EF Core 6.0, while also improving our perf infrastructure and testing.</span></span>

<span data-ttu-id="cf8dd-151">このテーマに関しては反復的な調査が何回も行われる予定であり、それによりリソースを集中させる場所が明らかになります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-151">This theme will involve a lot of iterative investigation, which will inform where we focus resources.</span></span> <span data-ttu-id="cf8dd-152">最初は次の作業が計画されています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-152">We plan to begin with:</span></span>

### <a name="performance-infrastructure-and-new-tests"></a><span data-ttu-id="cf8dd-153">パフォーマンス インフラストラクチャと新しいテスト</span><span class="sxs-lookup"><span data-stu-id="cf8dd-153">Performance infrastructure and new tests</span></span>

<span data-ttu-id="cf8dd-154">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-154">Status: Not started</span></span>

<span data-ttu-id="cf8dd-155">規模: Medium</span><span class="sxs-lookup"><span data-stu-id="cf8dd-155">T-shirt size: Medium</span></span>

<span data-ttu-id="cf8dd-156">EF Core のコードベースには、毎日実行されるパフォーマンス ベンチマークのセットが既に含まれています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-156">The EF Core codebase already contains a set of performance benchmarks that are executed every day.</span></span> <span data-ttu-id="cf8dd-157">6\.0 では、これらのテストのためのインフラストラクチャを強化し、新しいテストを追加することが計画されています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-157">For 6.0, we plan to improve the infrastructure for these tests as well as adding new tests.</span></span> <span data-ttu-id="cf8dd-158">また、メインラインのパフォーマンス シナリオのプロファイリングを行い、簡単に実行できるものを見つけて解決します。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-158">We will also profile mainline perf scenarios and fix any low-hanging fruit found.</span></span>

### <a name="compiled-models"></a><span data-ttu-id="cf8dd-159">コンパイル済みモデル</span><span class="sxs-lookup"><span data-stu-id="cf8dd-159">Compiled models</span></span>

<span data-ttu-id="cf8dd-160">追跡: [#1906](https://github.com/dotnet/efcore/issues/1906)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-160">Tracked by [#1906](https://github.com/dotnet/efcore/issues/1906)</span></span>

<span data-ttu-id="cf8dd-161">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-161">Status: Not started</span></span>

<span data-ttu-id="cf8dd-162">規模: 特大</span><span class="sxs-lookup"><span data-stu-id="cf8dd-162">T-shirt size: X-Large</span></span>

<span data-ttu-id="cf8dd-163">コンパイル済みモデルを使用すると、EF モデルのコンパイル済み形式を生成できます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-163">Compiled models will allow the generation of a compiled form of the EF model.</span></span> <span data-ttu-id="cf8dd-164">これにより、起動パフォーマンスが向上すると共に、モデルにアクセスするときのパフォーマンスが一般に向上します。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-164">This will provide both better startup performance, as well as generally better performance when accessing the model.</span></span>

### <a name="techempower-fortunes"></a><span data-ttu-id="cf8dd-165">TechEmpower の Fortunes</span><span class="sxs-lookup"><span data-stu-id="cf8dd-165">TechEmpower Fortunes</span></span>

<span data-ttu-id="cf8dd-166">追跡: [#23611](https://github.com/dotnet/efcore/issues/23611)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-166">Tracked by [#23611](https://github.com/dotnet/efcore/issues/23611)</span></span>

<span data-ttu-id="cf8dd-167">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-167">Status: Not started</span></span>

<span data-ttu-id="cf8dd-168">規模: 特大</span><span class="sxs-lookup"><span data-stu-id="cf8dd-168">T-shirt size: X-Large</span></span>

<span data-ttu-id="cf8dd-169">何年も、業界標準の [TechEmpower ベンチマーク](https://www.techempower.com/benchmarks/)を .NET 上で PostgreSQL データベースに対して実行してきました。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-169">We have been running the industry standard [TechEmpower benchmarks](https://www.techempower.com/benchmarks/) on .NET against a PostgreSQL database for several years.</span></span> <span data-ttu-id="cf8dd-170">[Fortunes ベンチマーク](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune)は、EF のシナリオに特に関連しています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-170">The [Fortunes benchmark](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) is particularly relevant to EF scenarios.</span></span> <span data-ttu-id="cf8dd-171">このベンチマークには、次のようなさまざまなバリエーションがあります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-171">We have multiple variations of this benchmark, including:</span></span>

- <span data-ttu-id="cf8dd-172">ADO.NET を直接使用する実装。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-172">An implementation that uses ADO.NET directly.</span></span> <span data-ttu-id="cf8dd-173">これは、ここで示す 3 つのものの中で最も速い実装です。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-173">This is the fastest implementation of the three listed here.</span></span>
- <span data-ttu-id="cf8dd-174">[Dapper](https://www.nuget.org/packages/Dapper/) を使用する実装。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-174">An implementation that uses [Dapper](https://www.nuget.org/packages/Dapper/).</span></span> <span data-ttu-id="cf8dd-175">これは ADO.NET を直接使用するより遅くなりますが、それでも高速です。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-175">This is slower than using ADO.NET directly, but still fast.</span></span>
- <span data-ttu-id="cf8dd-176">EF Core を使用する実装。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-176">An implementation that uses EF Core.</span></span> <span data-ttu-id="cf8dd-177">現在、これは 3 つの中で最も遅い実装です。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-177">This is currently the slowest implementation of the three.</span></span>

<span data-ttu-id="cf8dd-178">EF Core 6.0 の目標は、EF Core のパフォーマンスを TechEmpower Fortunes ベンチマークでの Dapper のパフォーマンスと匹敵するものにすることです。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-178">The goal for EF Core 6.0 is to get the EF Core performance to match that of Dapper on the TechEmpower Fortunes benchmark.</span></span> <span data-ttu-id="cf8dd-179">(これは簡単なことではありませんが、できるだけ近付けるように最善を尽くします)。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-179">(This is a significant challenge but we will do our best to get as close as we can.)</span></span>

### <a name="linkeraot"></a><span data-ttu-id="cf8dd-180">リンカー/AOT</span><span class="sxs-lookup"><span data-stu-id="cf8dd-180">Linker/AOT</span></span>

<span data-ttu-id="cf8dd-181">追跡: [#10963](https://github.com/dotnet/efcore/issues/10963)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-181">Tracked by [#10963](https://github.com/dotnet/efcore/issues/10963)</span></span>

<span data-ttu-id="cf8dd-182">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-182">Status: Not started</span></span>

<span data-ttu-id="cf8dd-183">規模: Medium</span><span class="sxs-lookup"><span data-stu-id="cf8dd-183">T-shirt size: Medium</span></span>

<span data-ttu-id="cf8dd-184">EF Core では、大量のランタイム コードの生成が実行されます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-184">EF Core performs large amounts of runtime code generation.</span></span> <span data-ttu-id="cf8dd-185">これは、Xamarin や Blazor のようなリンカー ツリーのシェイキングに依存するアプリ モデルや、iOS のような動的コンパイルを実行できないプラットフォームでは課題になっています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-185">This is challenging for app models that depend on linker tree shaking, such as Xamarin and Blazor, and platforms that don't allow dynamic compilation, such as iOS.</span></span> <span data-ttu-id="cf8dd-186">EF Core 6.0 の一部としてこの領域の調査を続け、可能な限り改善目標を達成します。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-186">We will continue investigating in this space as part of EF Core 6.0 and make targeted improvements as we can.</span></span> <span data-ttu-id="cf8dd-187">ただし、6.0 の期間内にギャップが完全に埋まることは期待していません。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-187">However, we do not expect to fully close the gap in the 6.0 time frame.</span></span>

## <a name="migrations-and-deployment"></a><span data-ttu-id="cf8dd-188">移行と展開</span><span class="sxs-lookup"><span data-stu-id="cf8dd-188">Migrations and deployment</span></span>

<span data-ttu-id="cf8dd-189">[EF Core 5.0 で行われた調査](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience)から継続して、移行の管理とデータベースの展開に関するサポートを強化することを計画しています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-189">Following on from the [investigations done for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), we plan to introduce improved support for managing migrations and deploying databases.</span></span> <span data-ttu-id="cf8dd-190">これに関しては、2 つの主要な領域があります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-190">This includes two major areas:</span></span>

### <a name="migrations-bundles"></a><span data-ttu-id="cf8dd-191">移行バンドル</span><span class="sxs-lookup"><span data-stu-id="cf8dd-191">Migrations bundles</span></span>

<span data-ttu-id="cf8dd-192">追跡: [#19693](https://github.com/dotnet/efcore/issues/19693)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-192">Tracked by [#19693](https://github.com/dotnet/efcore/issues/19693)</span></span>

<span data-ttu-id="cf8dd-193">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-193">Status: Not started</span></span>

<span data-ttu-id="cf8dd-194">規模: Medium</span><span class="sxs-lookup"><span data-stu-id="cf8dd-194">T-shirt size: Medium</span></span>

<span data-ttu-id="cf8dd-195">移行バンドルは、運用データベースに移行を適用する自己完結型の実行可能ファイルです。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-195">A migrations bundle is a self-contained executable that applies migrations to a production database.</span></span> <span data-ttu-id="cf8dd-196">その動作は `dotnet ef database update` と同じですが、必要なものがすべて 1 つの実行可能ファイルに含まれているため、SSH、Docker、PowerShell の展開がはるかに簡単になるはずです。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-196">The behavior will match `dotnet ef database update`, but should make SSH/Docker/PowerShell deployment much easier, since everything needed is contained in the single executable.</span></span>

### <a name="managing-migrations"></a><span data-ttu-id="cf8dd-197">移行の管理</span><span class="sxs-lookup"><span data-stu-id="cf8dd-197">Managing migrations</span></span>

<span data-ttu-id="cf8dd-198">追跡: [#22945](https://github.com/dotnet/efcore/issues/22945)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-198">Tracked by [#22945](https://github.com/dotnet/efcore/issues/22945)</span></span>

<span data-ttu-id="cf8dd-199">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-199">Status: Not started</span></span>

<span data-ttu-id="cf8dd-200">規模: 大</span><span class="sxs-lookup"><span data-stu-id="cf8dd-200">T-shirt size: Large</span></span>

<span data-ttu-id="cf8dd-201">1 つのアプリケーションに対して作成される移行の数が増加して負担になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-201">The number of migrations created for an application can grow to become a burden.</span></span> <span data-ttu-id="cf8dd-202">また、これらの移行は、必要ない場合でも、アプリケーションと共に展開されることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-202">In addition, these migrations are frequently deployed with the application even when this is not needed.</span></span> <span data-ttu-id="cf8dd-203">EF Core 6.0 では、ツールおよびプロジェクトとアセンブリの管理の向上により、これを改善する予定です。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-203">In EF Core 6.0, we plan to improve this through better tooling and project/assembly management.</span></span> <span data-ttu-id="cf8dd-204">対応を予定している特定の 2 つのイシューは、[多くの移行を 1 つにまとめる](https://github.com/dotnet/efcore/issues/2174)ことと、[クリーンなモデルのスナップショットを再生成する](https://github.com/dotnet/efcore/issues/18557)ことです。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-204">Two specific issues we plan to address are [squash many migrations into one](https://github.com/dotnet/efcore/issues/2174) and [regenerate a clean model snapshot](https://github.com/dotnet/efcore/issues/18557).</span></span>

## <a name="improve-existing-features-and-fix-bugs"></a><span data-ttu-id="cf8dd-205">既存のフィーチャーの改善とバグの修正</span><span class="sxs-lookup"><span data-stu-id="cf8dd-205">Improve existing features and fix bugs</span></span>

<span data-ttu-id="cf8dd-206">[6.0.0 のマイルストーンに割り当てられているイシューまたはバグ](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0)は、現在、このリリースで計画されています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-206">Any [issue or bug assigned to the 6.0.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0) is currently planned for this release.</span></span> <span data-ttu-id="cf8dd-207">これには、多くの小さな機能強化とバグ修正が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-207">This includes many small enhancements and bug fixes.</span></span>

### <a name="ef6-query-parity"></a><span data-ttu-id="cf8dd-208">EF6 のクエリのパリティ</span><span class="sxs-lookup"><span data-stu-id="cf8dd-208">EF6 query parity</span></span>

<span data-ttu-id="cf8dd-209">追跡: ["ef6-parity" というラベルが付けられていて 6.0 のマイルストーンに含まれるイシュー](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-209">Tracked by [issues labeled with 'ef6-parity' and in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)</span></span>

<span data-ttu-id="cf8dd-210">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-210">Status: Not started</span></span>

<span data-ttu-id="cf8dd-211">規模: 大</span><span class="sxs-lookup"><span data-stu-id="cf8dd-211">T-shirt size: Large</span></span>

<span data-ttu-id="cf8dd-212">EF Core 5.0 の場合、EF6 でサポートされていないパターンに加えて、EF6 によってサポートされているほとんどのクエリ パターンがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-212">EF Core 5.0 supports most query patterns supported by EF6, in addition to patterns not supported in EF6.</span></span> <span data-ttu-id="cf8dd-213">EF Core 6.0 の場合は、ギャップを埋め、サポートされる EF Core クエリを、サポートされている EF6 クエリの真のスーパーセットにすることが計画されています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-213">For EF Core 6.0, we plan to close the gap and make supported EF Core queries a true superset of supported EF6 queries.</span></span> <span data-ttu-id="cf8dd-214">これはギャップの調査によって進められますが、[後に FirstOrDefault が続く GroupBy を変換する](https://github.com/dotnet/efcore/issues/12088)のような GroupBy のイシューや、[プリミティブ](https://github.com/dotnet/efcore/issues/11624)型および[マップされていない](https://github.com/dotnet/efcore/issues/10753)型に対する生の SQL クエリが既に含まれています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-214">This will be driven by investigation of the gaps, but already includes GroupBy issues such as [translate GroupBy followed by FirstOrDefault](https://github.com/dotnet/efcore/issues/12088), and raw SQL queries for [primitive](https://github.com/dotnet/efcore/issues/11624) and [unmapped](https://github.com/dotnet/efcore/issues/10753) types.</span></span>  

### <a name="value-objects"></a><span data-ttu-id="cf8dd-215">値オブジェクト</span><span class="sxs-lookup"><span data-stu-id="cf8dd-215">Value objects</span></span>

<span data-ttu-id="cf8dd-216">追跡: [#9906](https://github.com/dotnet/efcore/issues/9906)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-216">Tracked by [#9906](https://github.com/dotnet/efcore/issues/9906)</span></span>

<span data-ttu-id="cf8dd-217">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-217">Status: Not started</span></span>

<span data-ttu-id="cf8dd-218">規模: Medium</span><span class="sxs-lookup"><span data-stu-id="cf8dd-218">T-shirt size: Medium</span></span>

<span data-ttu-id="cf8dd-219">以前は、[集計のサポート](https://www.martinfowler.com/bliki/DDD_Aggregate.html)を目的として、エンティティを所有するのはチーム ビューであり、[値オブジェクト](https://www.martinfowler.com/bliki/ValueObject.html)に対する妥当な近似でもありました。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-219">It was previously the team view that owned entities, intended for [aggregate support](https://www.martinfowler.com/bliki/DDD_Aggregate.html), would also be a reasonable approximation to [value objects](https://www.martinfowler.com/bliki/ValueObject.html).</span></span> <span data-ttu-id="cf8dd-220">経験上、そうではないことがわかりました。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-220">Experience has shown this not to be the case.</span></span> <span data-ttu-id="cf8dd-221">そのため、EF Core 6.0 では、ドメイン駆動設計での値オブジェクトのニーズに焦点を当てて、より優れたエクスペリエンスの導入が計画されています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-221">Therefore, in EF Core 6.0 we plan to introduce a better experience focused on the needs of value objects in domain-driven design.</span></span> <span data-ttu-id="cf8dd-222">この方法は、所有エンティティではなく値コンバーターに基づいています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-222">This approach will be based on value converters rather than owned entities.</span></span>

<span data-ttu-id="cf8dd-223">この作業の最初の対象は、[複数の列にマップする値コンバーター](https://github.com/dotnet/efcore/issues/13947)を可能にすることです。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-223">This work is initially scoped to allow [value converters which map to multiple columns](https://github.com/dotnet/efcore/issues/13947).</span></span> <span data-ttu-id="cf8dd-224">リリース時のフィードバックに基づいて、サポートが追加される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-224">We may pull in additional support based on feedback during the release.</span></span>

### <a name="cosmos-database-provider"></a><span data-ttu-id="cf8dd-225">Cosmos データベース プロバイダー</span><span class="sxs-lookup"><span data-stu-id="cf8dd-225">Cosmos database provider</span></span>

<span data-ttu-id="cf8dd-226">追跡: ["area-cosmos" というラベルが付けられていて 6.0 のマイルストーンに含まれるイシュー](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-226">Tracked by [issues labeled with 'area-cosmos' and in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)</span></span>

<span data-ttu-id="cf8dd-227">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-227">Status: Not started</span></span>

<span data-ttu-id="cf8dd-228">規模: 大</span><span class="sxs-lookup"><span data-stu-id="cf8dd-228">T-shirt size: Large</span></span>

<span data-ttu-id="cf8dd-229">EF Core 6.0 で Cosmos プロバイダーに対して行う機能強化に関するフィードバックの収集が積極的に行われています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-229">We are actively gathering feedback on which improvements to make to the Cosmos provider in EF Core 6.0.</span></span> <span data-ttu-id="cf8dd-230">情報が増えたらこのドキュメントを更新します。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-230">We will update this document as we learn more.</span></span> <span data-ttu-id="cf8dd-231">現時点では、必要な Cosmos のフィーチャーに投票 (👍) してください。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-231">For now, please make sure to vote (👍) for the Cosmos features that you need.</span></span>

### <a name="expose-model-building-conventions-to-applications"></a><span data-ttu-id="cf8dd-232">モデル構築規則をアプリケーションに公開する</span><span class="sxs-lookup"><span data-stu-id="cf8dd-232">Expose model building conventions to applications</span></span>

<span data-ttu-id="cf8dd-233">追跡: [#214](https://github.com/dotnet/efcore/issues/214)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-233">Tracked by [#214](https://github.com/dotnet/efcore/issues/214)</span></span>

<span data-ttu-id="cf8dd-234">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-234">Status: Not started</span></span>

<span data-ttu-id="cf8dd-235">規模: 中</span><span class="sxs-lookup"><span data-stu-id="cf8dd-235">T-shirt size: medium</span></span>

<span data-ttu-id="cf8dd-236">EF Core では、.NET 型からモデルを構築するために規則のセットが使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-236">EF Core uses a set of conventions for building a model from .NET types.</span></span> <span data-ttu-id="cf8dd-237">これらの規則は、現在、データベース プロバイダーによって制御されています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-237">These conventions are currently controlled by the database provider.</span></span> <span data-ttu-id="cf8dd-238">EF Core 6.0 では、アプリケーションでこれらの規則に接続して変更できるようにする予定です。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-238">In EF Core 6.0, we intend to allow applications to hook into and change these conventions.</span></span>

### <a name="zero-bug-balance-zbb"></a><span data-ttu-id="cf8dd-239">残バグ ゼロ (ZBB)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-239">Zero bug balance (ZBB)</span></span>

<span data-ttu-id="cf8dd-240">追跡: [6.0 のマイルストーンで `type-bug` というラベルが付けられているイシュー](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-240">Tracked by [issues labeled with `type-bug` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="cf8dd-241">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="cf8dd-241">Status: In-progress</span></span>

<span data-ttu-id="cf8dd-242">規模: 大</span><span class="sxs-lookup"><span data-stu-id="cf8dd-242">T-shirt size: Large</span></span>

<span data-ttu-id="cf8dd-243">EF Core 6.0 の期間中に、すべての未解決バグを修正することを計画しています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-243">We plan to fix all outstanding bugs during the EF Core 6.0 time frame.</span></span> <span data-ttu-id="cf8dd-244">いくつかの留意事項:</span><span class="sxs-lookup"><span data-stu-id="cf8dd-244">Some things to keep in mind:</span></span>

- <span data-ttu-id="cf8dd-245">これは、特に [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug) というラベルが付いているイシューに当てはまります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-245">This specifically applies to issues labeled [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug).</span></span>
- <span data-ttu-id="cf8dd-246">バグを適切に修正するために設計の変更や新しい機能が必要になる場合など、例外があります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-246">There will be exceptions, such as when the bug requires a design change or new feature to fix properly.</span></span> <span data-ttu-id="cf8dd-247">そのようなイシューは、`blocked` というラベルでマークされます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-247">These issues will be marked with the `blocked` label.</span></span>
- <span data-ttu-id="cf8dd-248">GA/RTM リリースに近付いたら一般に行われるように、必要な場合はリスクに基づいてバグをそのままにします。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-248">We will punt bugs based on risk when needed as is normal as we get close to a GA/RTM release.</span></span>

### <a name="miscellaneous-features"></a><span data-ttu-id="cf8dd-249">その他の機能</span><span class="sxs-lookup"><span data-stu-id="cf8dd-249">Miscellaneous features</span></span>

<span data-ttu-id="cf8dd-250">追跡: [6.0 のマイルストーンで `type-enhancement` というラベルが付けられているイシュー](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-250">Tracked by [issues labeled with `type-enhancement` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)</span></span>

<span data-ttu-id="cf8dd-251">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="cf8dd-251">Status: In-progress</span></span>

<span data-ttu-id="cf8dd-252">規模: 大</span><span class="sxs-lookup"><span data-stu-id="cf8dd-252">T-shirt size: Large</span></span>

<span data-ttu-id="cf8dd-253">EF 6.0 では他に次のようなフィーチャーが計画されていますが、これらに限定されません。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-253">Miscellaneous features planned for EF 6.0 include, but are not limited to:</span></span>

- [<span data-ttu-id="cf8dd-254">非ナビゲーション コレクションに対するクエリを分割する</span><span class="sxs-lookup"><span data-stu-id="cf8dd-254">Split query for non-navigation collections</span></span>](https://github.com/dotnet/efcore/issues/21234)
- [<span data-ttu-id="cf8dd-255">リバース エンジニアリングで単純な結合テーブルを検出し、多対多リレーションシップを作成する</span><span class="sxs-lookup"><span data-stu-id="cf8dd-255">Detect simple join tables in reverse engineering and create many-to-many relationships</span></span>](https://github.com/dotnet/efcore/issues/22475)
- [<span data-ttu-id="cf8dd-256">SQLite と SQL Server での完全な自由テキスト検索を完成させる</span><span class="sxs-lookup"><span data-stu-id="cf8dd-256">Complete full/free-text search on SQLite and SQL Server</span></span>](https://github.com/dotnet/efcore/issues/4823)
- [<span data-ttu-id="cf8dd-257">SQL Server の空間インデックス</span><span class="sxs-lookup"><span data-stu-id="cf8dd-257">SQL Server spatial indexes</span></span>](https://github.com/dotnet/efcore/issues/12538)
- [<span data-ttu-id="cf8dd-258">モデル内の特定の型のプロパティに既定の変換を指定するためのメカニズムと API</span><span class="sxs-lookup"><span data-stu-id="cf8dd-258">Mechanism/API to specify a default conversion for any property of a given type in the model</span></span>](https://github.com/dotnet/efcore/issues/10784)
- [<span data-ttu-id="cf8dd-259">ADO.NET から新しいバッチ API を使用する</span><span class="sxs-lookup"><span data-stu-id="cf8dd-259">Use the new batching API from ADO.NET</span></span>](https://github.com/dotnet/efcore/issues/18990)

## <a name="net-integration"></a><span data-ttu-id="cf8dd-260">.NET 統合</span><span class="sxs-lookup"><span data-stu-id="cf8dd-260">.NET integration</span></span>

<span data-ttu-id="cf8dd-261">EF Core チームは、関連性はあるが独立した複数のテクノロジについての作業も行っています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-261">The EF Core team also works on several related but independent technologies.</span></span> <span data-ttu-id="cf8dd-262">具体的には、以下の作業が予定されています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-262">In particular, we plan to work on:</span></span>

### <a name="enhancements-to-systemdata"></a><span data-ttu-id="cf8dd-263">System.Data の機能強化</span><span class="sxs-lookup"><span data-stu-id="cf8dd-263">Enhancements to System.Data</span></span>

<span data-ttu-id="cf8dd-264">追跡: [6.0 のマイルストーンで `area-System.Data` というラベルが付けられている dotnet\runtime リポジトリのイシュー](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-264">Tracked by [issues in the dotnet\runtime repo labeled with `area-System.Data` in the 6.0 milestone](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)</span></span>

<span data-ttu-id="cf8dd-265">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-265">Status: Not started</span></span>

<span data-ttu-id="cf8dd-266">規模: 大</span><span class="sxs-lookup"><span data-stu-id="cf8dd-266">T-shirt size: Large</span></span>

<span data-ttu-id="cf8dd-267">この作業には次のものが含まれます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-267">This work includes:</span></span>

- <span data-ttu-id="cf8dd-268">新しい[バッチ処理 API](https://github.com/dotnet/runtime/issues/28633) の実装。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-268">Implementation of the new [batching API](https://github.com/dotnet/runtime/issues/28633).</span></span>
- <span data-ttu-id="cf8dd-269">ADO.NET の理解と進化のための、他の .NET チームやコミュニティとの継続的な連携。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-269">Continued work with other .NET teams and the community to understand and evolve ADO.NET.</span></span>
- <span data-ttu-id="cf8dd-270">[System.Data.\* コンポーネントでのトレースのために DiagnosticSource を標準化する](https://github.com/dotnet/runtime/issues/22336)。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-270">[Standardize on DiagnosticSource for tracing in System.Data.\* components](https://github.com/dotnet/runtime/issues/22336).</span></span>

### <a name="enhancements-to-microsoftdatasqlite"></a><span data-ttu-id="cf8dd-271">Microsoft.Data.Sqlite の機能強化</span><span class="sxs-lookup"><span data-stu-id="cf8dd-271">Enhancements to Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="cf8dd-272">追跡: [6.0 のマイルストーンで `type-enhancement` および `area-adonet-sqlite` というラベルが付けられているイシュー](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-272">Tracked by [issues labeled with `type-enhancement` and `area-adonet-sqlite` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)</span></span>

<span data-ttu-id="cf8dd-273">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="cf8dd-273">Status: In-progress</span></span>

<span data-ttu-id="cf8dd-274">規模: Medium</span><span class="sxs-lookup"><span data-stu-id="cf8dd-274">T-shirt size: Medium</span></span>

<span data-ttu-id="cf8dd-275">パフォーマンスのための[接続プール](https://github.com/dotnet/efcore/issues/13837)や[準備されたステートメント](https://github.com/dotnet/efcore/issues/14044)など、Microsoft.Data.Sqlite に関していくつかの小さな改良が計画されています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-275">Several small improvements are planned for the Microsoft.Data.Sqlite, including [connection pooling](https://github.com/dotnet/efcore/issues/13837) and [prepared statements](https://github.com/dotnet/efcore/issues/14044) for performance.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="cf8dd-276">null 許容参照型</span><span class="sxs-lookup"><span data-stu-id="cf8dd-276">Nullable reference types</span></span>

<span data-ttu-id="cf8dd-277">追跡: [#14150](https://github.com/dotnet/efcore/issues/14150)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-277">Tracked by [#14150](https://github.com/dotnet/efcore/issues/14150)</span></span>

<span data-ttu-id="cf8dd-278">状態: 進行中</span><span class="sxs-lookup"><span data-stu-id="cf8dd-278">Status: In-progress</span></span>

<span data-ttu-id="cf8dd-279">規模: 大</span><span class="sxs-lookup"><span data-stu-id="cf8dd-279">T-shirt size: Large</span></span>

<span data-ttu-id="cf8dd-280">[null 許容参照型](/dotnet/csharp/nullable-references)を使用するために EF Core コードに注釈を付けます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-280">We will annotate the EF Core code to use [nullable reference types](/dotnet/csharp/nullable-references).</span></span>

## <a name="experiments-and-investigations"></a><span data-ttu-id="cf8dd-281">実験と調査</span><span class="sxs-lookup"><span data-stu-id="cf8dd-281">Experiments and investigations</span></span>

<span data-ttu-id="cf8dd-282">EF チームは、EF Core 6.0 の期間中に、2 つの領域での実験と調査に時間を費やすことを計画しています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-282">The EF team is planning to invest time during the EF Core 6.0 timeframe experimenting and investigating in two areas.</span></span> <span data-ttu-id="cf8dd-283">これは学習プロセスであるため、6.0 リリースでは具体的な成果物は計画されていません。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-283">This is a learning process and as such no concrete deliverables are planned for the 6.0 release.</span></span>

### <a name="sqlservercore"></a><span data-ttu-id="cf8dd-284">SqlServer.Core</span><span class="sxs-lookup"><span data-stu-id="cf8dd-284">SqlServer.Core</span></span>

<span data-ttu-id="cf8dd-285">追跡: [.NET Data Lab リポジトリで](https://github.com/dotnet/datalab/)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-285">Tracked in the [.NET Data Lab repo](https://github.com/dotnet/datalab/)</span></span>

<span data-ttu-id="cf8dd-286">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-286">Status: Not started</span></span>

<span data-ttu-id="cf8dd-287">規模: 継続</span><span class="sxs-lookup"><span data-stu-id="cf8dd-287">T-shirt size: Ongoing</span></span>

<span data-ttu-id="cf8dd-288">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) は、SQL Server 用の完全な機能を備えた ADO.NET データベース プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-288">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) is a fully-featured ADO.NET database provider for SQL Server.</span></span> <span data-ttu-id="cf8dd-289">.NET Core と .NET Framework の両方で、広範な SQL Server 機能がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-289">It supports a broad range of SQL Server features on both .NET Core and .NET Framework.</span></span> <span data-ttu-id="cf8dd-290">しかし、その動作間での多くの複雑なやり取りが含まれる、大規模で古いコードベースでもあります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-290">However, it is also a large and old codebase with many complex interactions between its behaviors.</span></span> <span data-ttu-id="cf8dd-291">そのため、新しい .NET Core フィーチャーを使用することにより得られる可能性がある利点を調査するのが難しくなっています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-291">This makes it difficult to investigate the potential gains the could be made using newer .NET Core features.</span></span> <span data-ttu-id="cf8dd-292">したがって、.NET 用の高パフォーマンス SQL Server ドライバーの可能性を判断するための実験を、コミュニティと協力して開始しています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-292">Therefore, we are starting an experiment in collaboration with the community to determine what potential there is for a highly performing SQL Server driver for .NET.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="cf8dd-293">Microsoft.Data.SqlClient への取り組みは変わりません。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-293">Investment in Microsoft.Data.SqlClient is not changing.</span></span> <span data-ttu-id="cf8dd-294">EF Core であってもなくても、引き続き SQL Server と SQL Azure の両方に接続するための推奨される方法です。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-294">It will continue to be the recommended way to connect to SQL Server and SQL Azure, both with and without EF Core.</span></span> <span data-ttu-id="cf8dd-295">新しく導入される SQL Server のフィーチャーは引き続きサポートされます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-295">It will continue to support new SQL Server features as they are introduced.</span></span>

### <a name="graphql"></a><span data-ttu-id="cf8dd-296">GraphQL</span><span class="sxs-lookup"><span data-stu-id="cf8dd-296">GraphQL</span></span>

<span data-ttu-id="cf8dd-297">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-297">Status: Not started</span></span>

<span data-ttu-id="cf8dd-298">規模: 継続</span><span class="sxs-lookup"><span data-stu-id="cf8dd-298">T-shirt size: Ongoing</span></span>

<span data-ttu-id="cf8dd-299">[GraphQL](https://graphql.org/) は、過去数年間にさまざまなプラットフォームで広く使用されるようになっています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-299">[GraphQL](https://graphql.org/) has been gaining traction over the last few years across a variety of platforms.</span></span> <span data-ttu-id="cf8dd-300">これに関する調査を行い、.NET でのエクスペリエンスを向上させる方法を見つけることを計画しています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-300">We plan to investigate the space and find ways to improve the experience with .NET.</span></span> <span data-ttu-id="cf8dd-301">これには、既存のエコシステムを理解してサポートするためのコミュニティとの連携が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-301">This will involve working with the community on understanding and supporting the existing ecosystem.</span></span> <span data-ttu-id="cf8dd-302">また、既存の作業への貢献の形式や、Microsoft スタックでの無償要素の開発での、Microsoft による具体的な取り組みも含まれる場合があります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-302">It may also involve specific investment from Microsoft, either in the form of contributions to existing work or in developing complimentary pieces in the Microsoft stack.</span></span>

### <a name="dataverse-formerly-common-data-services"></a><span data-ttu-id="cf8dd-303">DataVerse (旧称 Common Data Services)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-303">DataVerse (formerly Common Data Services)</span></span>

<span data-ttu-id="cf8dd-304">状態: 開始前</span><span class="sxs-lookup"><span data-stu-id="cf8dd-304">Status: Not started</span></span>

<span data-ttu-id="cf8dd-305">規模: 継続</span><span class="sxs-lookup"><span data-stu-id="cf8dd-305">T-shirt size: Ongoing</span></span>

<span data-ttu-id="cf8dd-306">[DataVerse](/powerapps/maker/data-platform/data-platform-intro) は、ビジネス アプリケーションを短期間で開発できるように設計された列ベースのデータ ストアです。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-306">[DataVerse](/powerapps/maker/data-platform/data-platform-intro) is a column-based data store designed for rapid development of business applications.</span></span> <span data-ttu-id="cf8dd-307">バイナリ オブジェクト (BLOB) などの複雑なデータ型が自動的に処理され、組織や連絡先などの組み込みのエンティティとリレーションシップが用意されています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-307">It automatically handles complex data types like binary objects (BLOBs) and has built-in entities and relationships like organizations and contacts.</span></span> <span data-ttu-id="cf8dd-308">SDK は存在しますが、EF Core プロバイダーを利用することで、開発者には高度な LINQ クエリの使用、作業単位の利用、一貫性のあるデータ アクセス API などのメリットがあります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-308">An SDK exists but developers may benefit from having an EF Core provider to use advanced LINQ queries, take advantage of unit of work and have a consistent data access API.</span></span> <span data-ttu-id="cf8dd-309">チームは、DataVerse に接続する .NET 開発者エクスペリエンスを向上させるためのさまざまな方法を検討します。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-309">The team will consider different ways to improve the .NET developer experience connecting to DataVerse.</span></span>

## <a name="below-the-cut-line"></a><span data-ttu-id="cf8dd-310">対象外</span><span class="sxs-lookup"><span data-stu-id="cf8dd-310">Below-the-cut-line</span></span>

<span data-ttu-id="cf8dd-311">追跡: [`consider-for-current-release` というラベルが付けられている問題](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)</span><span class="sxs-lookup"><span data-stu-id="cf8dd-311">Tracked by [issues labeled with `consider-for-current-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)</span></span>

<span data-ttu-id="cf8dd-312">これらは、現時点では 6.0 リリースで予定されて **いない** バグ修正および機能強化ですが、リリース全体を通したフィードバックと上記の作業の進行状況に基づいて検討されます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-312">These are bug fixes and enhancements that are **not** currently scheduled for the 6.0 release, but we will look at based on feedback throughout the release together with progress made on the work above.</span></span> <span data-ttu-id="cf8dd-313">これらのイシューは、EF Core 6.0 に取り込まれ、自動的に次のリリースの候補になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-313">These issues may be pulled in to EF Core 6.0, and automatically become candidates for the next release.</span></span>

<span data-ttu-id="cf8dd-314">また、計画を立てるときは常に、[最も投票が多かった問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)が考慮されています。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-314">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="cf8dd-315">これらの問題をリリースから切り捨てることは常に苦痛ですが、保有するリソースにとって現実的な計画を立てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-315">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span> <span data-ttu-id="cf8dd-316">必要なフィーチャーに投票 (👍) したことを確認してください。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-316">Make sure you have voted (👍) for the features you need.</span></span>

## <a name="suggestions"></a><span data-ttu-id="cf8dd-317">推奨事項</span><span class="sxs-lookup"><span data-stu-id="cf8dd-317">Suggestions</span></span>

<span data-ttu-id="cf8dd-318">計画に関するフィードバックは重要です。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-318">Your feedback on planning is important.</span></span> <span data-ttu-id="cf8dd-319">イシューの重要度を示す最善の方法は、GitHub でそのイシューに投票 (👍) することです。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-319">The best way to indicate the importance of an issue is to vote (👍) for that issue on GitHub.</span></span> <span data-ttu-id="cf8dd-320">このデータが、次のリリースの[計画プロセス](xref:core/what-is-new/release-planning)に取り込まれます。</span><span class="sxs-lookup"><span data-stu-id="cf8dd-320">This data will then feed into the [planning process](xref:core/what-is-new/release-planning) for the next release.</span></span>
