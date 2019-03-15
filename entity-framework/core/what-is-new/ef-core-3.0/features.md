---
title: EF Core 3.0 の新機能 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/features
ms.openlocfilehash: b6774f615b04bf9579aac5dea217e7321631da0c
ms.sourcegitcommit: a709054b2bc7a8365201d71f59325891aacd315f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2019
ms.locfileid: "57829188"
---
# <a name="new-features-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="84b8d-102">EF Core 3.0 (現在、プレビュー段階) に含まれる新機能</span><span class="sxs-lookup"><span data-stu-id="84b8d-102">New features included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="84b8d-103">機能セットと今後のリリースのスケジュールは、常に変更される可能性があることに注意してください。また、このページを最新の状態に保持するようにしていますが、最新のプランが反映されていない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="84b8d-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="84b8d-104">以下のリストには、EF Core 3.0 について計画されている主な新機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="84b8d-104">The following list includes the major new features planned for EF Core 3.0.</span></span>
<span data-ttu-id="84b8d-105">これらの機能のほとんどは現在のプレビューに含まれていませんが、RTM に向けて進行中であるため、使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="84b8d-105">Most of these features are not included in the current preview, but will become available as we make progress towards RTM.</span></span>

<span data-ttu-id="84b8d-106">理由は、リリースの開始時に、計画されている[破壊的変更](xref:core/what-is-new/ef-core-3.0/breaking-changes)の実装に焦点を合わせているためです。</span><span class="sxs-lookup"><span data-stu-id="84b8d-106">The reason is that at the beginning of the release we are focusing on implementing planned [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes).</span></span>
<span data-ttu-id="84b8d-107">これらの破壊的変更の多くは、EF Core 自体の機能強化です。</span><span class="sxs-lookup"><span data-stu-id="84b8d-107">Many of these breaking changes are improvements to EF Core on their own.</span></span>
<span data-ttu-id="84b8d-108">さらに強化するために他にも多くの変更が必要です。</span><span class="sxs-lookup"><span data-stu-id="84b8d-108">Many others are required to unblock further improvements.</span></span> 

<span data-ttu-id="84b8d-109">進行中のバグ修正と機能強化に関する完全なリストについては、[問題の追跡ツールのこのクエリ](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc)で確認できます。</span><span class="sxs-lookup"><span data-stu-id="84b8d-109">For a complete list of bug fixes and enhancements underway, you can see [this query in our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc).</span></span>

## <a name="linq-improvements"></a><span data-ttu-id="84b8d-110">LINQ の機能強化</span><span class="sxs-lookup"><span data-stu-id="84b8d-110">LINQ improvements</span></span> 

[<span data-ttu-id="84b8d-111">問題 #12795 の追跡</span><span class="sxs-lookup"><span data-stu-id="84b8d-111">Tracking Issue #12795</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

<span data-ttu-id="84b8d-112">この機能への取り組みは始まっていますが、現在のプレビューには含まれていません。</span><span class="sxs-lookup"><span data-stu-id="84b8d-112">Work on this feature has started but it isn't included in the current preview.</span></span>

<span data-ttu-id="84b8d-113">LINQ を使うと、好みの言語を使ってデータベース クエリを記述でき、豊富な型情報を利用して IntelliSense とコンパイル時の型チェックを活用できます。</span><span class="sxs-lookup"><span data-stu-id="84b8d-113">LINQ enables you to write database queries without leaving your language of choice, taking advantage of rich type information to get IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="84b8d-114">ただし、LINQ では複雑なクエリを無制限に記述することもできます。これは、LINQ プロバイダーにとって常に重要な課題となります。</span><span class="sxs-lookup"><span data-stu-id="84b8d-114">But LINQ also enables you to write an unlimited number of complicated queries, and that has always been a huge challenge for LINQ providers.</span></span>
<span data-ttu-id="84b8d-115">EF Core の最初のいくつかのバージョンでは、クエリのうち SQL に変換可能な部分を特定してから、クエリの残りの部分をクライアント側のメモリ内で実行させることにより、これを部分的に解決しました。</span><span class="sxs-lookup"><span data-stu-id="84b8d-115">In the first few versions of EF Core, we solved that in part by figuring out what portions of a query could be translated to SQL, and then by allowing the rest of the query to execute in memory on the client.</span></span>
<span data-ttu-id="84b8d-116">状況によってはこのクライアント側の実行が望ましいものとなる場合がありますが、その他の多くの場合では、アプリケーションが運用環境に展開されるまで特定できない非効率なクエリが発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="84b8d-116">This client-side execution can be desirable in some situations, but in many other cases it can result in inefficient queries that may not be identified until an application is deployed to production.</span></span>
<span data-ttu-id="84b8d-117">EF Core 3.0 では、LINQ の実装のしくみとそのテスト方法に関して、大きな変更を加える予定です。</span><span class="sxs-lookup"><span data-stu-id="84b8d-117">In EF Core 3.0, we're planning to make profound changes to how our LINQ implementation works, and how we test it.</span></span>
<span data-ttu-id="84b8d-118">その目的は、これをより堅牢にすること (たとえば、修正プログラムのリリースで破壊的なクエリを回避すること)、より多くの式を適切に SQL に変換できるようにすること、効率的なクエリをより多くのケースで生成すること、および非効率なクエリの見逃しを防ぐことです。</span><span class="sxs-lookup"><span data-stu-id="84b8d-118">The goals are to make it more robust (for example, to avoid breaking queries in patch releases), to enable translating more expressions correctly into SQL, to generate efficient queries in more cases, and to prevent inefficient queries from going undetected.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="84b8d-119">Cosmos DB のサポート</span><span class="sxs-lookup"><span data-stu-id="84b8d-119">Cosmos DB support</span></span> 

[<span data-ttu-id="84b8d-120">問題 #8443 の追跡</span><span class="sxs-lookup"><span data-stu-id="84b8d-120">Tracking Issue #8443</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/8443)

<span data-ttu-id="84b8d-121">この機能は現在のプレビューに含まれていますが、まだ完成していません。</span><span class="sxs-lookup"><span data-stu-id="84b8d-121">This feature is included in the current preview, but isn't complete yet.</span></span> 

<span data-ttu-id="84b8d-122">EF のプログラミング モデルに慣れている開発者が、Azure Cosmos DB をアプリケーション データベースとして簡単にターゲット設定できるようにするために、EF Core 用の Cosmos DB プロバイダーに取り組んでいます。</span><span class="sxs-lookup"><span data-stu-id="84b8d-122">We're working on a Cosmos DB provider for EF Core, to enable developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span>
<span data-ttu-id="84b8d-123">その目的は、グローバル配布、"常にオン" 機能、高いスケーラビリティ、低待機時間など、Cosmos DB の利点のいくつかを .NET 開発者がさらに使いやすくすることです。</span><span class="sxs-lookup"><span data-stu-id="84b8d-123">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span>
<span data-ttu-id="84b8d-124">プロバイダーでは、Cosmos DB の SQL API に対して、変更の自動追跡、LINQ、値変換など、ほとんどの EF Core 機能が有効になります。</span><span class="sxs-lookup"><span data-stu-id="84b8d-124">The provider will enable most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>
<span data-ttu-id="84b8d-125">この作業は EF Core 2.2 以前に開始されたため、[いくつかのプロバイダーのプレビュー バージョンを利用できます](https://blogs.msdn.microsoft.com/dotnet/2018/10/17/announcing-entity-framework-core-2-2-preview-3/)。</span><span class="sxs-lookup"><span data-stu-id="84b8d-125">We started this effort before EF Core 2.2, and [we have made some preview versions of the provider available](https://blogs.msdn.microsoft.com/dotnet/2018/10/17/announcing-entity-framework-core-2-2-preview-3/).</span></span>
<span data-ttu-id="84b8d-126">新しいプランでは、EF Core 3.0 と共にプロバイダーの開発を継続していきます。</span><span class="sxs-lookup"><span data-stu-id="84b8d-126">The new plan is to continue developing the provider alongside EF Core 3.0.</span></span> 

## <a name="c-80-support"></a><span data-ttu-id="84b8d-127">C# 8.0 のサポート</span><span class="sxs-lookup"><span data-stu-id="84b8d-127">C# 8.0 support</span></span>

<span data-ttu-id="84b8d-128">[問題 #12047 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12047)
[問題 #10347 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/10347)</span><span class="sxs-lookup"><span data-stu-id="84b8d-128">[Tracking Issue #12047](https://github.com/aspnet/EntityFrameworkCore/issues/12047)
[Tracking Issue #10347](https://github.com/aspnet/EntityFrameworkCore/issues/10347)</span></span>

<span data-ttu-id="84b8d-129">この機能への取り組みは始まっていますが、現在のプレビューには含まれていません。</span><span class="sxs-lookup"><span data-stu-id="84b8d-129">Work on this feature has started but it isn't included in the current preview.</span></span>

<span data-ttu-id="84b8d-130">非同期ストリーム (`await foreach` を含む) や Null 許容参照型など、[C# 8.0 で導入される新機能](https://blogs.msdn.microsoft.com/dotnet/2018/11/12/building-c-8-0/)の一部を EF Core を使う際にも利用していただきたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="84b8d-130">We want our customers to take advantage of some of the [new features coming in C# 8.0](https://blogs.msdn.microsoft.com/dotnet/2018/11/12/building-c-8-0/) like async streams (including `await foreach`) and nullable reference types while using EF Core.</span></span>

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="84b8d-131">データベース ビューのリバース エンジニアリング</span><span class="sxs-lookup"><span data-stu-id="84b8d-131">Reverse engineering of database views</span></span>

[<span data-ttu-id="84b8d-132">問題 #1679 の追跡</span><span class="sxs-lookup"><span data-stu-id="84b8d-132">Tracking Issue #1679</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/1679)

<span data-ttu-id="84b8d-133">この機能は現在のプレビューに含まれていません。</span><span class="sxs-lookup"><span data-stu-id="84b8d-133">This feature isn't included in the current preview.</span></span>

<span data-ttu-id="84b8d-134">EF Core 2.1 で導入され、EF Core 3.0 ではキーなしのエンティティ型が考慮された[クエリ型](xref:core/modeling/query-types)は、データベースから読み取れるものの、更新することはできないデータを表します。</span><span class="sxs-lookup"><span data-stu-id="84b8d-134">[Query types](xref:core/modeling/query-types), introduced in EF Core 2.1 and considered entity types without keys in EF Core 3.0, represent data that can be read from the database, but cannot be updated.</span></span>
<span data-ttu-id="84b8d-135">この特性によって、ほとんどのシナリオのデータベース ビューに最適なものとなるため、データベース ビューのリバース エンジニアリング時にキーなしのエンティティ型の作成を自動化することを計画しています。</span><span class="sxs-lookup"><span data-stu-id="84b8d-135">This characteristic makes them an excellent fit for database views in most scenarios, so we plan to automate the creation of entity types without keys when reverse engineering database views.</span></span>

## <a name="property-bag-entities"></a><span data-ttu-id="84b8d-136">プロパティ バッグのエンティティ</span><span class="sxs-lookup"><span data-stu-id="84b8d-136">Property bag entities</span></span> 

<span data-ttu-id="84b8d-137">[問題 #13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) と [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) の追跡</span><span class="sxs-lookup"><span data-stu-id="84b8d-137">[Tracking Issue #13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) and [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914)</span></span>

<span data-ttu-id="84b8d-138">この機能への取り組みは始まっていますが、現在のプレビューには含まれていません。</span><span class="sxs-lookup"><span data-stu-id="84b8d-138">Work on this feature has started but it isn't included in the current preview.</span></span> 

<span data-ttu-id="84b8d-139">この機能は、通常のプロパティではなくインデックス付きプロパティにデータを格納するエンティティを有効にすること、および、同じ EF Core モデル内で異なるエンティティ型を表すために、同じ .NET クラスのインスタンス (`Dictionary<string, object>` のように単純なものである可能性があります) を使用できるようにすることと関係しています。</span><span class="sxs-lookup"><span data-stu-id="84b8d-139">This feature is about enabling entities that store data in indexed properties instead of regular properties, and also about being able to use instances of the same .NET class (potentially something as simple as a `Dictionary<string, object>`) to represent different entity types in the same EF Core model.</span></span>
<span data-ttu-id="84b8d-140">この機能は、結合エンティティなしの多対多リレーションシップをサポートするための足がかりとなります ([問題 #1368](https://github.com/aspnet/EntityFrameworkCore/issues/1368))。これは EF Core に対して特に要望が多かった機能強化の 1 つです。</span><span class="sxs-lookup"><span data-stu-id="84b8d-140">This feature is a stepping stone to support many-to-many relationships without a join entity ([issue #1368](https://github.com/aspnet/EntityFrameworkCore/issues/1368)), which is one of the most requested improvements for EF Core.</span></span>

## <a name="ef-63-on-net-core"></a><span data-ttu-id="84b8d-141">.NET Core での EF 6.3</span><span class="sxs-lookup"><span data-stu-id="84b8d-141">EF 6.3 on .NET Core</span></span> 

[<span data-ttu-id="84b8d-142">問題 EF6#271 の追跡</span><span class="sxs-lookup"><span data-stu-id="84b8d-142">Tracking Issue EF6#271</span></span>](https://github.com/aspnet/EntityFramework6/issues/271)

<span data-ttu-id="84b8d-143">この機能への取り組みは始まっていますが、現在のプレビューには含まれていません。</span><span class="sxs-lookup"><span data-stu-id="84b8d-143">Work on this feature has started but it isn't included in the current preview.</span></span> 

<span data-ttu-id="84b8d-144">多くの既存のアプリケーションでは以前のバージョンの EF が使われていて、.NET Core を利用するためだけにそれらを EF Core に移植すると多大な労力が必要となる場合があります。</span><span class="sxs-lookup"><span data-stu-id="84b8d-144">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can sometimes require a significant effort.</span></span>
<span data-ttu-id="84b8d-145">そのため、次のバージョンの EF 6 は .NET Core 3.0 上で実行するように調整されます。</span><span class="sxs-lookup"><span data-stu-id="84b8d-145">For that reason, we will be adapting the next version of EF 6 to run on .NET Core 3.0.</span></span>
<span data-ttu-id="84b8d-146">最小限の変更で既存のアプリケーションを移植できるようにするために、この作業を行っています。</span><span class="sxs-lookup"><span data-stu-id="84b8d-146">We are doing this to facilitate porting existing applications with minimal changes.</span></span>
<span data-ttu-id="84b8d-147">これにはいくつかの制限があります。</span><span class="sxs-lookup"><span data-stu-id="84b8d-147">There are going to be some limitations.</span></span> <span data-ttu-id="84b8d-148">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="84b8d-148">For example:</span></span>
- <span data-ttu-id="84b8d-149">.NET Core での SQL Server の含まれているサポート以外の、他のデータベースを操作するための新しいプロバイダーが必要になります</span><span class="sxs-lookup"><span data-stu-id="84b8d-149">It will require new providers to work with other databases besides the included SQL Server support on .NET Core</span></span>
- <span data-ttu-id="84b8d-150">SQL Server での空間サポートは有効になりません</span><span class="sxs-lookup"><span data-stu-id="84b8d-150">Spatial support with SQL Server won't be enabled</span></span>

<span data-ttu-id="84b8d-151">この時点では、EF 6 について計画されている新機能がないことにも注意してください。</span><span class="sxs-lookup"><span data-stu-id="84b8d-151">Note also that there are no new features planned for EF 6 at this point.</span></span>
