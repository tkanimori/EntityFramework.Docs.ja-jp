---
title: Entity Framework Core のロードマップ
author: divega
ms.date: 02/20/2018
ms.assetid: 834C9729-7F6E-4355-917D-DE3EE9FE149E
uid: core/what-is-new/roadmap
ms.openlocfilehash: 36d5fa8009de0aadba3b636630e1d5bddf41723c
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490832"
---
# <a name="entity-framework-core-roadmap"></a><span data-ttu-id="a960d-102">Entity Framework Core のロードマップ</span><span class="sxs-lookup"><span data-stu-id="a960d-102">Entity Framework Core Roadmap</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a960d-103">機能セットと今後のリリースのスケジュールは、常に変更される可能性があることに注意してください。また、このページを最新の状態に保持するようにしていますが、最新のプランが反映されていない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="a960d-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

## <a name="last-release-ef-core-21"></a><span data-ttu-id="a960d-104">前回のリリース: EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="a960d-104">Last release: EF Core 2.1</span></span>

<span data-ttu-id="a960d-105">安定したバージョンの EF Core 2.1 は、2018 年 5 月 30 日にリリースされました。</span><span class="sxs-lookup"><span data-stu-id="a960d-105">The stable version of EF Core 2.1 was released on May 30, 2018.</span></span> <span data-ttu-id="a960d-106">このリリースの詳細については、「[EF Core 2.1 の新機能](xref:core/what-is-new/ef-core-2.1)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a960d-106">You can find more information about this release in [What is new in EF Core 2.1](xref:core/what-is-new/ef-core-2.1).</span></span>

## <a name="future-releases"></a><span data-ttu-id="a960d-107">リリースの予定</span><span class="sxs-lookup"><span data-stu-id="a960d-107">Future releases</span></span>

### <a name="ef-core-22"></a><span data-ttu-id="a960d-108">EF Core 2.2</span><span class="sxs-lookup"><span data-stu-id="a960d-108">EF Core 2.2</span></span>

<span data-ttu-id="a960d-109">このリリースには、多くのバグの修正と比較的少数の新機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a960d-109">This release will include many bug fixes, and relatively small number of new features.</span></span> <span data-ttu-id="a960d-110">このリリースの詳細は、[EF Core 2.2 ロードマップのお知らせ](https://github.com/aspnet/Announcements/issues/308)に記載されています。</span><span class="sxs-lookup"><span data-stu-id="a960d-110">Details about this release are included in the the [EF Core 2.2 roadmap announcement](https://github.com/aspnet/Announcements/issues/308).</span></span> 

### <a name="ef-core-30"></a><span data-ttu-id="a960d-111">EF Core 3.0</span><span class="sxs-lookup"><span data-stu-id="a960d-111">EF Core 3.0</span></span>

<span data-ttu-id="a960d-112">2.2 の後の次のリリースに向けた[リリース計画プロセス](#release-planning-process)は完了していませんが、現在、.NET Core 3.0 および ASP.NET 3.0 と整合するメジャー リリースを計画しています。</span><span class="sxs-lookup"><span data-stu-id="a960d-112">Although we have not completed the [release planning process](#release-planning-process) for the next release after 2.2, we are currently planning to have a major release, aligned with .NET Core 3.0 and ASP.NET 3.0.</span></span> 

<span data-ttu-id="a960d-113">この将来のリリースに暫定的に割り当てられている作業項目は、[この問題追跡ツールのクエリ](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc)を使用して確認することができます。</span><span class="sxs-lookup"><span data-stu-id="a960d-113">You can use [this query in our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc) to see work items tenatively assigned to this future release.</span></span>

## <a name="schedule"></a><span data-ttu-id="a960d-114">スケジュール</span><span class="sxs-lookup"><span data-stu-id="a960d-114">Schedule</span></span>

<span data-ttu-id="a960d-115">EF Core のスケジュールは、[.NET Core のスケジュール](https://github.com/dotnet/core/blob/master/roadmap.md)と [ASP.NET Core のスケジュール](https://github.com/aspnet/Home/wiki/Roadmap)と同期されています。</span><span class="sxs-lookup"><span data-stu-id="a960d-115">The schedule for EF Core is in-sync with the [.NET Core schedule](https://github.com/dotnet/core/blob/master/roadmap.md) and [ASP.NET Core schedule](https://github.com/aspnet/Home/wiki/Roadmap).</span></span>

## <a name="backlog"></a><span data-ttu-id="a960d-116">バックログ</span><span class="sxs-lookup"><span data-stu-id="a960d-116">Backlog</span></span>

<span data-ttu-id="a960d-117">Microsoft では問題の追跡ツールの[バックログ マイルストーン](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)を使用して、問題と機能の詳細な一覧を保持しています。</span><span class="sxs-lookup"><span data-stu-id="a960d-117">We use the [Backlog Milestone](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc) in our issue tracker to maintain a detailed list of issues and features.</span></span> <span data-ttu-id="a960d-118">顧客はこれらにコメントしたり、投票したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="a960d-118">Customers can comment and up-vote these.</span></span>

<span data-ttu-id="a960d-119">Microsoft では、ある程度予期することができ、どこかの時点で作業を行う、またはコミュニティの誰かが取り組む未処理の懸案事項を、そのままにしておく傾向がありますが、これは[リリースの計画プロセス](#release-planning-process)の一部として特定のマイルストーンに割り当てるまでに、特定の時間枠で解決する意図を暗に示しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="a960d-119">We tend to leave issues open that we reasonably expect we will work on at some point, or that someone from the community could tackle, but that does not imply the intent to resolve them in a specific timeframe until we assign them to a specific milestone as part of our [release planning process](#release-planning-process).</span></span>

<span data-ttu-id="a960d-120">機能を実装する計画がなければ、懸案事項を終了する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a960d-120">If we do not plan to ever implement a feature, we will likely close the issue.</span></span> <span data-ttu-id="a960d-121">懸案事項に関する新しい情報を取得した場合は、終了した懸案事項が後で再検討されることがあります。</span><span class="sxs-lookup"><span data-stu-id="a960d-121">An issue that we closed can be reconsidered at a later point if we obtain new information about it.</span></span>

<span data-ttu-id="a960d-122">つまり、機能 X が時間/リリース Y で解決されるというように、機能に関する十分な情報はありません。すべてのソフトウェア プロジェクトと同様に、優先度、リリース スケジュール、および利用可能なリリースはいつでも変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a960d-122">All that said, we don’t have enough information about the future to be able to say that feature X will be resolved by time/release Y. As in all software projects, priorities, release schedules, and available resources can change at any point.</span></span>

## <a name="release-planning-process"></a><span data-ttu-id="a960d-123">リリースの計画プロセス</span><span class="sxs-lookup"><span data-stu-id="a960d-123">Release planning process</span></span>

<span data-ttu-id="a960d-124">特定のリリースに含める特定の機能をどのように選ぶかについて、よく質問を受けます。</span><span class="sxs-lookup"><span data-stu-id="a960d-124">We often get questions about how we choose specific features to go into a particular release.</span></span> <span data-ttu-id="a960d-125">バックログはリリースの計画に自動的に変換されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="a960d-125">Our backlog certainly does not automatically translate into release plans.</span></span> <span data-ttu-id="a960d-126">また、EF6 に機能が存在することで、自動的にその機能が EF Core に実装される必要があるということにもなりません。</span><span class="sxs-lookup"><span data-stu-id="a960d-126">The presence of a feature in EF6 also does not automatically mean that the feature needs to be implemented in EF Core.</span></span>

<span data-ttu-id="a960d-127">一部は多くが特定の機能、機会、優先度について説明しているため、また、一部はプロセス自体がすべてのリリースと共に進化することが多いため、リリースを計画するために従うプロセス全体をここで詳述することは困難です。</span><span class="sxs-lookup"><span data-stu-id="a960d-127">It is difficult to detail here the whole process we follow to plan a release, partly because a lot of it is discussing the specific features, opportunities and priorities, and partly because the process itself usually evolves with every release.</span></span> <span data-ttu-id="a960d-128">ただし、次の作業内容を決定するときの一般的な質問を要約し、回答するのは、比較的簡単です。</span><span class="sxs-lookup"><span data-stu-id="a960d-128">However, it is relatively easy to summarize the common questions we try to answer when deciding what to work on next:</span></span>

1. <span data-ttu-id="a960d-129">**この機能を使用する開発者の数は? アプリケーション/エクスペリエンスをどの程度向上させますか?**</span><span class="sxs-lookup"><span data-stu-id="a960d-129">**How many developers we think will use the feature and how much better will it make their applications/experience?**</span></span> <span data-ttu-id="a960d-130">多くのソースからここにフィードバックを集約します。懸念事項のコメントと投票はこれらのソースの 1 つになります。</span><span class="sxs-lookup"><span data-stu-id="a960d-130">We aggregate feedback from many sources into this — Comments and votes on issues is one of those sources.</span></span>

2. <span data-ttu-id="a960d-131">**この機能をまだ実装していない場合、ユーザーが使用できる回避策とは?**</span><span class="sxs-lookup"><span data-stu-id="a960d-131">**What are the workarounds people can use if we don’t implement this feature yet?**</span></span> <span data-ttu-id="a960d-132">たとえば、多くの開発者がネイティブの多対多のサポートの不足を回避するために、統合テーブルにマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="a960d-132">For example, many developers are able to map a join table in order to work around lack of native many-to-many support.</span></span> <span data-ttu-id="a960d-133">明らかに、すべての開発者がこの操作を実行できるわけではありませんが、多くの開発者が実行できます。これが考慮される要因です。</span><span class="sxs-lookup"><span data-stu-id="a960d-133">Obviously, not all developers can do this, but many can, and this is a factor which counts.</span></span>

3. <span data-ttu-id="a960d-134">**他の機能を実装させるようにするなど、この機能の実装によって EF Core のアーキテクチャは進化しますか?**</span><span class="sxs-lookup"><span data-stu-id="a960d-134">**Does implementing this feature evolve the architecture of EF Core such that it moves us closer to implementing other features?**</span></span> <span data-ttu-id="a960d-135">他の機能のブロックをビルドするように動作する機能を優先させる傾向にあります。たとえば、所有する種類に対して実行されたテーブルの分割は、TPT のサポートを進めるのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a960d-135">We tend to favor features that act as building blocks for other features—for example, the table splitting that was done for owned types helps us move towards TPT support.</span></span>

4. <span data-ttu-id="a960d-136">**この機能は拡張ポイントですか?**</span><span class="sxs-lookup"><span data-stu-id="a960d-136">**Is the feature an extensibility point?**</span></span> <span data-ttu-id="a960d-137">拡張ポイントは開発者が独自の動作に簡単につながり、一部の不足している機能をそのように取得できるようにできるため、優先させる傾向にあります。</span><span class="sxs-lookup"><span data-stu-id="a960d-137">We tend to favor extensibility points because they enable developers to more easily hook in their own behaviors and get some of the missing functionality that way.</span></span> <span data-ttu-id="a960d-138">遅延読み込み作業を開始するときに、この一部を実行することを計画しています。</span><span class="sxs-lookup"><span data-stu-id="a960d-138">We’re planning to do some of this as a start to the lazy loading work.</span></span>

5. <span data-ttu-id="a960d-139">**他の製品と組み合わせて使用するときの機能のシナジーとは何ですか?**</span><span class="sxs-lookup"><span data-stu-id="a960d-139">**What is the synergy of the feature when used in combination with other products?**</span></span> <span data-ttu-id="a960d-140">EF Core を他の製品と共に使用したり、.NET Core、最新バージョンの Visual Studio、Microsoft Azure など、その他の製品を使用するときの操作性を大幅に向上させたりすることができる機能を優先させる傾向にあります。</span><span class="sxs-lookup"><span data-stu-id="a960d-140">We tend to favor features that allow EF Core to be used with other products or to significantly improve the experience of using other products, such as .NET Core, the latest version of Visual Studio, Microsoft Azure, etc.</span></span>

6. <span data-ttu-id="a960d-141">**機能を操作できるユーザーの機能とは? これらのリソースを最大限に活用する方法はありますか?**</span><span class="sxs-lookup"><span data-stu-id="a960d-141">**What are the capabilities of the people available to work on a feature, and how to best leverage these resources?**</span></span> <span data-ttu-id="a960d-142">EF チームの各メンバーおよびコミュニティの共同作成者であっても、異なる領域でさまざまなレベルを経験するので、Microsoft はそれに応じて計画する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a960d-142">Each member of the EF team and even our community contributors have different levels of experience in different areas and we have to plan accordingly.</span></span> <span data-ttu-id="a960d-143">GroupBy の変換または多対多などの特定の機能の作業を行うために、“全員の協力” が必要な場合でも、それは実用的ではないことがあります。</span><span class="sxs-lookup"><span data-stu-id="a960d-143">Even if we wanted to have “all hands on deck” to work on a specific feature like GroupBy translations, or many-to-many, that wouldn’t be practical.</span></span>

<span data-ttu-id="a960d-144">前述のように、このプロセスはすべてのリリースを進化させます。また、今後は開発者コミュニティのメンバー向けに機会を増やし、リリースの計画に情報を提供します (たとえば、機能とリリースの計画自体における提案された下書きを簡単に確認できるようにします)。</span><span class="sxs-lookup"><span data-stu-id="a960d-144">As mentioned before, this process evolves on every release, and in the future we would like to add more opportunities for members of the developer community to provide inputs into release plans, for example, by making it easier to review proposed drafts of the features and of the release plan itself.</span></span>
