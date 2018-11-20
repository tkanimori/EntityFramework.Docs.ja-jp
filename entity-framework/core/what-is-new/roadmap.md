---
title: Entity Framework Core のロードマップ
author: divega
ms.date: 02/20/2018
ms.assetid: 834C9729-7F6E-4355-917D-DE3EE9FE149E
uid: core/what-is-new/roadmap
ms.openlocfilehash: a12d628a28515f0c6710bfa59bc6dcdf41fcb58b
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688590"
---
# <a name="entity-framework-core-roadmap"></a><span data-ttu-id="42fcb-102">Entity Framework Core のロードマップ</span><span class="sxs-lookup"><span data-stu-id="42fcb-102">Entity Framework Core Roadmap</span></span>

> [!IMPORTANT]
> <span data-ttu-id="42fcb-103">機能セットと今後のリリースのスケジュールは、常に変更される可能性があることに注意してください。また、このページを最新の状態に保持するようにしていますが、最新のプランが反映されていない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="42fcb-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

### <a name="ef-core-22"></a><span data-ttu-id="42fcb-104">EF Core 2.2</span><span class="sxs-lookup"><span data-stu-id="42fcb-104">EF Core 2.2</span></span>

<span data-ttu-id="42fcb-105">このリリースには、多くのバグの修正と比較的少数の新機能が含まれます。</span><span class="sxs-lookup"><span data-stu-id="42fcb-105">This release will include many bug fixes, and relatively small number of new features.</span></span> <span data-ttu-id="42fcb-106">このリリースは 2018 年の年末に予定されています。</span><span class="sxs-lookup"><span data-stu-id="42fcb-106">This release is planned for end of year 2018.</span></span> <span data-ttu-id="42fcb-107">このリリースについて詳しくは、[EF Core 2.2 の新機能](xref:core/what-is-new/ef-core-2.2)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="42fcb-107">Details about this release are included in [What's new in EF Core 2.2](xref:core/what-is-new/ef-core-2.2).</span></span> 

### <a name="ef-core-30"></a><span data-ttu-id="42fcb-108">EF Core 3.0</span><span class="sxs-lookup"><span data-stu-id="42fcb-108">EF Core 3.0</span></span>

<span data-ttu-id="42fcb-109">EF Core のメジャー リリースは .NET Core 3.0 と ASP.NET 3.0 に合わせる予定ですが、そのための[リリースの計画プロセス](#release-planning-process)がまだ完成していません。</span><span class="sxs-lookup"><span data-stu-id="42fcb-109">We plan to have major EF Core release aligned with .NET Core 3.0 and ASP.NET 3.0, but we haven't completed the [release planning process](#release-planning-process) for it.</span></span>

<span data-ttu-id="42fcb-110">3.0 に暫定的に割り当てられている作業項目を確認するには、[この問題の追跡ツールのクエリ](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc)を使用します。</span><span class="sxs-lookup"><span data-stu-id="42fcb-110">Use [this query in our issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A3.0.0+sort%3Areactions-%2B1-desc) to see work items tentatively assigned to 3.0.</span></span>

## <a name="schedule"></a><span data-ttu-id="42fcb-111">スケジュール</span><span class="sxs-lookup"><span data-stu-id="42fcb-111">Schedule</span></span>

<span data-ttu-id="42fcb-112">EF Core のスケジュールは、[.NET Core のスケジュール](https://github.com/dotnet/core/blob/master/roadmap.md)と [ASP.NET Core のスケジュール](https://github.com/aspnet/Home/wiki/Roadmap)と同期されています。</span><span class="sxs-lookup"><span data-stu-id="42fcb-112">The schedule for EF Core is in-sync with the [.NET Core schedule](https://github.com/dotnet/core/blob/master/roadmap.md) and [ASP.NET Core schedule](https://github.com/aspnet/Home/wiki/Roadmap).</span></span>

## <a name="backlog"></a><span data-ttu-id="42fcb-113">バックログ</span><span class="sxs-lookup"><span data-stu-id="42fcb-113">Backlog</span></span>

<span data-ttu-id="42fcb-114">問題の追跡ツールの[バックログ マイルストーン](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)には、いつか取り組む予定の問題や、コミュニティのだれかが対処できると考えられる問題が含まれています。</span><span class="sxs-lookup"><span data-stu-id="42fcb-114">The [Backlog Milestone](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc) in our issue tracker contains issues that we expect to work on someday, or that we think someone from the community could tackle.</span></span>
<span data-ttu-id="42fcb-115">これらの問題に関するお客様からのコメントや投票をお待ちしております。</span><span class="sxs-lookup"><span data-stu-id="42fcb-115">Customers are welcome to submit comments and votes on these issues.</span></span>
<span data-ttu-id="42fcb-116">これらの問題のいずれかに取り組むことを考えている共同作成者は、まずそれに対するアプローチ方法について議論を始めることが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="42fcb-116">Contributors looking to work on any of these issues are encouraged to first start a discussion on how to approach them.</span></span>

<span data-ttu-id="42fcb-117">特定のバージョンの EF Core において特定の機能に取り組むという保証は、いかなる場合もありません。</span><span class="sxs-lookup"><span data-stu-id="42fcb-117">There is never a guarantee that we will work on any given feature in a specific version of EF Core.</span></span>
<span data-ttu-id="42fcb-118">すべてのソフトウェア プロジェクトと同じように、優先順位、リリース スケジュール、および使用可能なリソースは常に変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42fcb-118">As in all software projects, priorities, release schedules, and available resources can change at any point.</span></span>
<span data-ttu-id="42fcb-119">ただし、特定の時間枠の中で問題を解決する予定の場合は、バックログ マイルストーンではなくリリース マイルストーンにそれを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="42fcb-119">But if we intend to resolve an issue in a specific timeframe, we'll assign it to a release milestone instead of the backlog milestone.</span></span>
<span data-ttu-id="42fcb-120">[リリースの計画プロセス](#release-planning-process)の一環として、バックログおよびリリース マイルストーン間で定期的に問題を移動させています。</span><span class="sxs-lookup"><span data-stu-id="42fcb-120">We routinely move issues between the backlog and release milestones as part of our [release planning process](#release-planning-process).</span></span>

<span data-ttu-id="42fcb-121">対応する予定がない場合、その問題は閉じられる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42fcb-121">We'll likely close an issue if we don't plan to ever address it.</span></span>
<span data-ttu-id="42fcb-122">ただし、以前に閉じた問題に関する新情報が得られた場合は、それを再検討する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42fcb-122">But we can reconsider an issue that we previously closed if we get new information about it.</span></span>

## <a name="release-planning-process"></a><span data-ttu-id="42fcb-123">リリースの計画プロセス</span><span class="sxs-lookup"><span data-stu-id="42fcb-123">Release planning process</span></span>

<span data-ttu-id="42fcb-124">特定のリリースに含める特定の機能をどのように選ぶかについて、よく質問を受けます。</span><span class="sxs-lookup"><span data-stu-id="42fcb-124">We often get questions about how we choose specific features to go into a particular release.</span></span>
<span data-ttu-id="42fcb-125">バックログは確かに、リリースの計画に自動的に変換されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="42fcb-125">Our backlog certainly doesn't automatically translate into release plans.</span></span>
<span data-ttu-id="42fcb-126">また、ある機能が EF6 に存在する場合、自動的にその機能を EF Core に実装する必要があるというわけでもありません。</span><span class="sxs-lookup"><span data-stu-id="42fcb-126">The presence of a feature in EF6 also doesn't automatically mean that the feature needs to be implemented in EF Core.</span></span>

<span data-ttu-id="42fcb-127">リリースを計画するために実行するプロセス全体について詳しく説明することは困難です。</span><span class="sxs-lookup"><span data-stu-id="42fcb-127">It's difficult to detail the whole process we follow to plan a release.</span></span>
<span data-ttu-id="42fcb-128">その大半は、特定の機能、機会、優先度に関する議論であり、プロセス自体もリリースごとに進化します。</span><span class="sxs-lookup"><span data-stu-id="42fcb-128">Much of it is discussing the specific features, opportunities and priorities, and the process itself also evolves with every release.</span></span>
<span data-ttu-id="42fcb-129">ただし、次の作業内容を決定するときに答えようと試みている共通の質問についてまとめることは可能です。</span><span class="sxs-lookup"><span data-stu-id="42fcb-129">However, we can summarize the common questions we try to answer when deciding what to work on next:</span></span>

1. <span data-ttu-id="42fcb-130">**この機能を使用する開発者の数は? アプリケーション/エクスペリエンスをどの程度向上させますか?**</span><span class="sxs-lookup"><span data-stu-id="42fcb-130">**How many developers we think will use the feature and how much better will it make their applications/experience?**</span></span> <span data-ttu-id="42fcb-131">これに答えるために、多くのソースからフィードバックを収集します。問題についてのコメントと投票は、これらのソースの 1 つです。</span><span class="sxs-lookup"><span data-stu-id="42fcb-131">To answer this, we collect feedback from many sources — Comments and votes on issues is one of those sources.</span></span>

2. <span data-ttu-id="42fcb-132">**この機能をまだ実装していない場合、ユーザーが使用できる回避策とは?**</span><span class="sxs-lookup"><span data-stu-id="42fcb-132">**What are the workarounds people can use if we don't implement this feature yet?**</span></span> <span data-ttu-id="42fcb-133">たとえば、多対多のネイティブ サポートがないことを回避するために、多くの開発者は統合テーブルをマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="42fcb-133">For example, many developers can map a join table to work around lack of native many-to-many support.</span></span> <span data-ttu-id="42fcb-134">当然ながら、すべての開発者がこれを実行するわけではありませんが、その多くは実行できます。そしてこれは決定の要因としてカウントされます。</span><span class="sxs-lookup"><span data-stu-id="42fcb-134">Obviously, not all developers want to do it, but many can, and that counts as a factor in our decision.</span></span>

3. <span data-ttu-id="42fcb-135">**他の機能を実装させるようにするなど、この機能の実装によって EF Core のアーキテクチャは進化しますか?**</span><span class="sxs-lookup"><span data-stu-id="42fcb-135">**Does implementing this feature evolve the architecture of EF Core such that it moves us closer to implementing other features?**</span></span> <span data-ttu-id="42fcb-136">他の機能の構成要素として動作する機能は優先される傾向があります。</span><span class="sxs-lookup"><span data-stu-id="42fcb-136">We tend to favor features that act as building blocks for other features.</span></span> <span data-ttu-id="42fcb-137">たとえば、プロパティ バッグ エンティティによって多対多のサポートを進めることができ、エンティティ コンストラクターによって遅延読み込みのサポートが可能になりました。</span><span class="sxs-lookup"><span data-stu-id="42fcb-137">For example, property bag entities can help us move towards many-to-many support, and entity constructors enabled our lazy loading support.</span></span> 

4. <span data-ttu-id="42fcb-138">**この機能は拡張ポイントですか?**</span><span class="sxs-lookup"><span data-stu-id="42fcb-138">**Is the feature an extensibility point?**</span></span> <span data-ttu-id="42fcb-139">拡張ポイントは通常の機能よりも優先される傾向があります。なぜなら、開発者がこれらを使用して、それぞれの独自の動作をフックし、不足している機能を補うことができるためです。</span><span class="sxs-lookup"><span data-stu-id="42fcb-139">We tend to favor extensibility points over normal features because they enable developers to hook their own behaviors and compensate for any missing functionality.</span></span> 

5. <span data-ttu-id="42fcb-140">**他の製品と組み合わせて使用するときの機能のシナジーとは何ですか?**</span><span class="sxs-lookup"><span data-stu-id="42fcb-140">**What is the synergy of the feature when used in combination with other products?**</span></span> <span data-ttu-id="42fcb-141">.NET Core、最新バージョンの Visual Studio、Microsoft Azure など、その他の製品と共に EF Core を使用するエクスペリエンスを可能にする、または大幅に向上させる機能は、優先される傾向があります。</span><span class="sxs-lookup"><span data-stu-id="42fcb-141">We favor features that enable or significantly improve the experience of using EF Core with other products, such as .NET Core, the latest version of Visual Studio, Microsoft Azure, etc.</span></span>

6. <span data-ttu-id="42fcb-142">**機能に取り組むために利用できるユーザーのスキルは何ですか? これらのリソースを最大限に活用する方法はありますか?**</span><span class="sxs-lookup"><span data-stu-id="42fcb-142">**What are the skills of the people available to work on a feature, and how to best leverage these resources?**</span></span> <span data-ttu-id="42fcb-143">EF チームの各メンバーおよびコミュニティの共同作成者は、異なる領域におけるさまざまなレベルの経験を持っているので、それに応じて計画を立てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="42fcb-143">Each member of the EF team and our community contributors has different levels of experience in different areas, so we have to plan accordingly.</span></span> <span data-ttu-id="42fcb-144">GroupBy の変換や多対多など、特定の機能に取り組むために "全員の協力" が欲しくなる場合であっても、それは実用的ではありません。</span><span class="sxs-lookup"><span data-stu-id="42fcb-144">Even if we wanted to have "all hands on deck" to work on a specific feature like GroupBy translations, or many-to-many, that wouldn't be practical.</span></span>

<span data-ttu-id="42fcb-145">前述のように、プロセスはリリースごとに進化していきます。</span><span class="sxs-lookup"><span data-stu-id="42fcb-145">As mentioned before, the process evolves on every release.</span></span>
<span data-ttu-id="42fcb-146">今後は、コミュニティのメンバーがリリースの計画にインプットを提供する機会を、もっと増やす予定です。</span><span class="sxs-lookup"><span data-stu-id="42fcb-146">In the future we'll try to add more opportunities for members of the community to provide inputs into our release plans.</span></span>
<span data-ttu-id="42fcb-147">たとえば、機能やリリースの計画自体の設計案を、簡単に確認できるようにしたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="42fcb-147">For example, we would like to make it easier to review draft designs of the features and of the release plan itself.</span></span>