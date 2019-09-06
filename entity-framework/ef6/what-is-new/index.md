---
title: 新機能 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 41d1f86b-ce66-4bf2-8963-48514406fb4c
ms.openlocfilehash: 01dc618954da5dbd12fbd37c2c47701ce251be92
ms.sourcegitcommit: 0cc9578fd49802789a00c0044b4e57325476ca2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70271437"
---
# <a name="whats-new-in-ef6"></a><span data-ttu-id="fa250-102">EF6 の新機能</span><span class="sxs-lookup"><span data-stu-id="fa250-102">What's New in EF6</span></span>

<span data-ttu-id="fa250-103">最新の機能と最高の信頼性を得るには、最新リリース バージョンの Entity Framework を使用することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="fa250-103">We highly recommend that you use the latest released version of Entity Framework to ensure you get the latest features and the highest stability.</span></span>
<span data-ttu-id="fa250-104">ただし、前のバージョンを使用しなければならないことや、新しい改善点を最新のプレリリースで試さなければならないこともあります。</span><span class="sxs-lookup"><span data-stu-id="fa250-104">However, we realize that you may need to use a previous version, or that you may want to experiment with new improvements in the latest pre-release.</span></span>
<span data-ttu-id="fa250-105">特定のバージョンの EF をインストールする場合は、「[Get Entity Framework](~/ef6/fundamentals/install.md)」(Entity Framework の取得) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fa250-105">To install specific versions of EF, see [Get Entity Framework](~/ef6/fundamentals/install.md).</span></span>

<span data-ttu-id="fa250-106">このページでは、新しい各リリースに含まれる機能を説明します。</span><span class="sxs-lookup"><span data-stu-id="fa250-106">This page documents the features that are included on each new release.</span></span>

## <a name="recent-releases"></a><span data-ttu-id="fa250-107">最近のリリース</span><span class="sxs-lookup"><span data-stu-id="fa250-107">Recent releases</span></span>

### <a name="ef-tools-update-in-visual-studio-2017-157"></a><span data-ttu-id="fa250-108">Visual Studio 2017 15.7 の EF ツールの更新プログラム</span><span class="sxs-lookup"><span data-stu-id="fa250-108">EF Tools Update in Visual Studio 2017 15.7</span></span>

<span data-ttu-id="fa250-109">2018 年 5 月に、Visual Studio 2017 15.7 の一部として EF ツールの更新バージョンをリリースしました。</span><span class="sxs-lookup"><span data-stu-id="fa250-109">In May 2018, we released an updated version of the EF Tools as part of Visual Studio 2017 15.7.</span></span>
<span data-ttu-id="fa250-110">これには、次のような一般的な問題点の機能強化が含まれています。</span><span class="sxs-lookup"><span data-stu-id="fa250-110">It includes improvements for some common pain points:</span></span>

- <span data-ttu-id="fa250-111">ユーザー インターフェイス アクセシビリティに関するいくつかのバグの修正プログラム</span><span class="sxs-lookup"><span data-stu-id="fa250-111">Fixes for several user interface accessibility bugs</span></span>
- <span data-ttu-id="fa250-112">既存のデータベースからモデルを生成するときの SQL Server のパフォーマンス低下の回避策 [#4](https://github.com/aspnet/entityframework6/issues/4)</span><span class="sxs-lookup"><span data-stu-id="fa250-112">Workaround for SQL Server performance regression when generating models from existing databases [#4](https://github.com/aspnet/entityframework6/issues/4)</span></span>
- <span data-ttu-id="fa250-113">SQL Server での大規模モデルに対するモデル更新のサポート [#185](https://github.com/aspnet/EntityFramework6/issues/185)</span><span class="sxs-lookup"><span data-stu-id="fa250-113">Support for updating models for larger models on SQL Server [#185](https://github.com/aspnet/EntityFramework6/issues/185)</span></span>

<span data-ttu-id="fa250-114">この EF ツールの新しいバージョンの別の改善点として、新しいプロジェクトでのモデル作成時に EF 6.2 ランタイムがインストールされます。</span><span class="sxs-lookup"><span data-stu-id="fa250-114">Another improvement in this new version of EF Tools is that it installs the EF 6.2 runtime when creating a model in a new project.</span></span> <span data-ttu-id="fa250-115">以前のバージョンの Visual Studio では、NuGet パッケージの対応するバージョンをインストールすることで、EF 6.2 ランタイム (と過去のすべてのバージョンの EF) を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="fa250-115">With older versions of Visual Studio, it is possible to use the EF 6.2 runtime (as well as any past version of EF) by installing the corresponding version of the NuGet package.</span></span>

### <a name="ef-62-runtime"></a><span data-ttu-id="fa250-116">EF 6.2 ランタイム</span><span class="sxs-lookup"><span data-stu-id="fa250-116">EF 6.2 Runtime</span></span>

<span data-ttu-id="fa250-117">EF 6.2 ランタイムは、NuGet で 2017 年 10 月にリリースされました。</span><span class="sxs-lookup"><span data-stu-id="fa250-117">The EF 6.2 runtime was released to NuGet in October of 2017.</span></span>
<span data-ttu-id="fa250-118">オープン ソースの共同作成者のコミュニティによる多大な尽力のおかげで、EF 6.2 には多数の[バグ修正](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-bug)と[製品の拡張機能](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-enhancement%20)が含まれています。</span><span class="sxs-lookup"><span data-stu-id="fa250-118">Thanks in great part to the efforts our community of open source contributors, EF 6.2 includes numerous [bugs fixes](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-bug) and [product enhancements](https://github.com/aspnet/entityframework6/issues?utf8=%E2%9C%93&q=is%3Aissue%20milestone%3A6.2.0%20is%3Aclosed%20label%3Aclosed-fixed%20-label%3Aarea-tools%20label%3Atype-enhancement%20).</span></span>

<span data-ttu-id="fa250-119">EF 6.2 ランタイムに影響を与える、特に重要な変更の簡単な一覧を次に示します。</span><span class="sxs-lookup"><span data-stu-id="fa250-119">Here is a brief list of the most important changes affecting the EF 6.2 runtime:</span></span>

- <span data-ttu-id="fa250-120">永続的キャッシュから完了済みコードの最初のモデルを読み込むことで、起動時間が短縮される [#275](https://github.com/aspnet/EntityFramework6/issues/275)</span><span class="sxs-lookup"><span data-stu-id="fa250-120">Reduce start up time by loading finished code first models from a persistent cache [#275](https://github.com/aspnet/EntityFramework6/issues/275)</span></span>
- <span data-ttu-id="fa250-121">インデックスを定義する fluent API [#274](https://github.com/aspnet/EntityFramework6/issues/274)</span><span class="sxs-lookup"><span data-stu-id="fa250-121">Fluent API to define indexes [#274](https://github.com/aspnet/EntityFramework6/issues/274)</span></span>
- <span data-ttu-id="fa250-122">SQL で LIKE に変換される LINQ クエリの記述を有効にするための DbFunctions.Like() [#241](https://github.com/aspnet/EntityFramework6/issues/241)</span><span class="sxs-lookup"><span data-stu-id="fa250-122">DbFunctions.Like() to enable writing LINQ queries that translate to LIKE in SQL [#241](https://github.com/aspnet/EntityFramework6/issues/241)</span></span>
- <span data-ttu-id="fa250-123">Migrate.exe で -script オプションがサポート [#240](https://github.com/aspnet/EntityFramework6/issues/240)</span><span class="sxs-lookup"><span data-stu-id="fa250-123">Migrate.exe now supports -script option [#240](https://github.com/aspnet/EntityFramework6/issues/240)</span></span>
- <span data-ttu-id="fa250-124">EF6 で SQL Server のシーケンスで生成されたキーの値が使用可能に [#165](https://github.com/aspnet/EntityFramework6/issues/165)</span><span class="sxs-lookup"><span data-stu-id="fa250-124">EF6 can now work with key values generated by a sequence in SQL Server [#165](https://github.com/aspnet/EntityFramework6/issues/165)</span></span>
- <span data-ttu-id="fa250-125">SQL Azure の実行方法に関する一時的なエラーのリストの更新 [#83](https://github.com/aspnet/EntityFramework6/issues/83)</span><span class="sxs-lookup"><span data-stu-id="fa250-125">Update list of transient errors for SQL Azure Execution Strategy [#83](https://github.com/aspnet/EntityFramework6/issues/83)</span></span>
- <span data-ttu-id="fa250-126">バグ:クエリまたは SQL コマンドの再試行が "The SqlParameter is already contained by another SqlParameterCollection" (この SqlParameter は別の SqlParameterCollection に既に含まれています) で失敗する [#81](https://github.com/aspnet/EntityFramework6/issues/81)</span><span class="sxs-lookup"><span data-stu-id="fa250-126">Bug: Retrying queries or SQL commands fails with "The SqlParameter is already contained by another SqlParameterCollection" [#81](https://github.com/aspnet/EntityFramework6/issues/81)</span></span>
- <span data-ttu-id="fa250-127">バグ:DbQuery.ToString() の評価がデバッガーで頻繁にタイムアウトになる [#73](https://github.com/aspnet/EntityFramework6/issues/73)</span><span class="sxs-lookup"><span data-stu-id="fa250-127">Bug: Evaluation of DbQuery.ToString() frequently times out in the debugger [#73](https://github.com/aspnet/EntityFramework6/issues/73)</span></span>

## <a name="future-releases"></a><span data-ttu-id="fa250-128">リリースの予定</span><span class="sxs-lookup"><span data-stu-id="fa250-128">Future Releases</span></span>

<span data-ttu-id="fa250-129">今後のバージョンの EF6 については、[ロードマップ](roadmap.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="fa250-129">For information on future version of EF6, please look at our [Roadmap](roadmap.md).</span></span>

## <a name="past-releases"></a><span data-ttu-id="fa250-130">以前のリリース</span><span class="sxs-lookup"><span data-stu-id="fa250-130">Past Releases</span></span>

<span data-ttu-id="fa250-131">[以前のリリース](past-releases.md)のページには、EF の過去のすべてのバージョンのアーカイブと、各リリースで導入された主要機能が含まれています。</span><span class="sxs-lookup"><span data-stu-id="fa250-131">The [Past Releases](past-releases.md) page contains an archive of all previous versions of EF and the major features that were introduced on each release.</span></span>
