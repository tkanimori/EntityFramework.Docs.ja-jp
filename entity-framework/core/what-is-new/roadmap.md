---
title: Entity Framework Core のロードマップ
author: divega
ms.date: 02/20/2018
ms.assetid: 834C9729-7F6E-4355-917D-DE3EE9FE149E
uid: core/what-is-new/roadmap
ms.openlocfilehash: f466d112e5ed8661ad1e2d91079c1c01d34b9002
ms.sourcegitcommit: a013e243a14f384999ceccaf9c779b8c1ae3b936
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463231"
---
# <a name="entity-framework-core-roadmap"></a><span data-ttu-id="4d50d-102">Entity Framework Core のロードマップ</span><span class="sxs-lookup"><span data-stu-id="4d50d-102">Entity Framework Core Roadmap</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4d50d-103">機能セットと今後のリリースのスケジュールは、常に変更される可能性があることに注意してください。また、このページを最新の状態に保持するようにしていますが、最新のプランが反映されていない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="4d50d-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

### <a name="ef-core-30"></a><span data-ttu-id="4d50d-104">EF Core 3.0</span><span class="sxs-lookup"><span data-stu-id="4d50d-104">EF Core 3.0</span></span>

<span data-ttu-id="4d50d-105">EF Core 2.2 は完了したので、現在は EF Core 3.0 に重点が置かれています。</span><span class="sxs-lookup"><span data-stu-id="4d50d-105">With EF Core 2.2 out the door, our main focus is now EF Core 3.0.</span></span>
<span data-ttu-id="4d50d-106">このリリースに含まれる、計画されている[新機能](xref:core/what-is-new/ef-core-3.0/features)と意図的な[破壊的変更](xref:core/what-is-new/ef-core-3.0/breaking-changes)については、[EF Core 3.0 の新機能](xref:core/what-is-new/ef-core-3.0/index)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="4d50d-106">See [What's new in EF Core 3.0](xref:core/what-is-new/ef-core-3.0/index) for information on planned [new features](xref:core/what-is-new/ef-core-3.0/features) and intentional [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes) included in the release.</span></span>

## <a name="schedule"></a><span data-ttu-id="4d50d-107">スケジュール</span><span class="sxs-lookup"><span data-stu-id="4d50d-107">Schedule</span></span>

<span data-ttu-id="4d50d-108">EF Core のリリース スケジュールは、[.NET Core のリリース スケジュール](https://github.com/dotnet/core/blob/master/roadmap.md)と同期されています。</span><span class="sxs-lookup"><span data-stu-id="4d50d-108">The release schedule for EF Core is in-sync with the [.NET Core release schedule](https://github.com/dotnet/core/blob/master/roadmap.md).</span></span>

## <a name="backlog"></a><span data-ttu-id="4d50d-109">Backlog</span><span class="sxs-lookup"><span data-stu-id="4d50d-109">Backlog</span></span>

<span data-ttu-id="4d50d-110">問題の追跡ツールの[バックログ マイルストーン](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)には、いつか取り組む予定の問題や、コミュニティのだれかが対処できると考えられる問題が含まれています。</span><span class="sxs-lookup"><span data-stu-id="4d50d-110">The [Backlog Milestone](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc) in our issue tracker contains issues that we expect to work on someday, or that we think someone from the community could tackle.</span></span>
<span data-ttu-id="4d50d-111">これらの問題に関するお客様からのコメントや投票をお待ちしております。</span><span class="sxs-lookup"><span data-stu-id="4d50d-111">Customers are welcome to submit comments and votes on these issues.</span></span>
<span data-ttu-id="4d50d-112">これらの問題のいずれかに取り組むことを考えている共同作成者は、まずそれに対するアプローチ方法について議論を始めることが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="4d50d-112">Contributors looking to work on any of these issues are encouraged to first start a discussion on how to approach them.</span></span>

<span data-ttu-id="4d50d-113">特定のバージョンの EF Core において特定の機能に取り組むという保証は、いかなる場合もありません。</span><span class="sxs-lookup"><span data-stu-id="4d50d-113">There is never a guarantee that we will work on any given feature in a specific version of EF Core.</span></span>
<span data-ttu-id="4d50d-114">すべてのソフトウェア プロジェクトと同じように、優先順位、リリース スケジュール、および使用可能なリソースは常に変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4d50d-114">As in all software projects, priorities, release schedules, and available resources can change at any point.</span></span>
<span data-ttu-id="4d50d-115">ただし、特定の時間枠の中で問題を解決する予定の場合は、バックログ マイルストーンではなくリリース マイルストーンにそれを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="4d50d-115">But if we intend to resolve an issue in a specific timeframe, we'll assign it to a release milestone instead of the backlog milestone.</span></span>
<span data-ttu-id="4d50d-116">[リリースの計画プロセス](#release-planning-process)の一環として、バックログおよびリリース マイルストーン間で定期的に問題を移動させています。</span><span class="sxs-lookup"><span data-stu-id="4d50d-116">We routinely move issues between the backlog and release milestones as part of our [release planning process](#release-planning-process).</span></span>

<span data-ttu-id="4d50d-117">対応する予定がない場合、その問題は閉じられる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4d50d-117">We'll likely close an issue if we don't plan to ever address it.</span></span>
<span data-ttu-id="4d50d-118">ただし、以前に閉じた問題に関する新情報が得られた場合は、それを再検討する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="4d50d-118">But we can reconsider an issue that we previously closed if we get new information about it.</span></span>

## <a name="release-planning-process"></a><span data-ttu-id="4d50d-119">リリースの計画プロセス</span><span class="sxs-lookup"><span data-stu-id="4d50d-119">Release planning process</span></span>

<span data-ttu-id="4d50d-120">特定のリリースに含める特定の機能をどのように選ぶかについて、よく質問を受けます。</span><span class="sxs-lookup"><span data-stu-id="4d50d-120">We often get questions about how we choose specific features to go into a particular release.</span></span>
<span data-ttu-id="4d50d-121">バックログは確かに、リリースの計画に自動的に変換されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="4d50d-121">Our backlog certainly doesn't automatically translate into release plans.</span></span>
<span data-ttu-id="4d50d-122">また、ある機能が EF6 に存在する場合、自動的にその機能を EF Core に実装する必要があるというわけでもありません。</span><span class="sxs-lookup"><span data-stu-id="4d50d-122">The presence of a feature in EF6 also doesn't automatically mean that the feature needs to be implemented in EF Core.</span></span>

<span data-ttu-id="4d50d-123">リリースを計画するために実行するプロセス全体について詳しく説明することは困難です。</span><span class="sxs-lookup"><span data-stu-id="4d50d-123">It's difficult to detail the whole process we follow to plan a release.</span></span>
<span data-ttu-id="4d50d-124">その大半は、特定の機能、機会、優先度に関する議論であり、プロセス自体もリリースごとに進化します。</span><span class="sxs-lookup"><span data-stu-id="4d50d-124">Much of it is discussing the specific features, opportunities and priorities, and the process itself also evolves with every release.</span></span>
<span data-ttu-id="4d50d-125">ただし、次の作業内容を決定するときに答えようと試みている共通の質問についてまとめることは可能です。</span><span class="sxs-lookup"><span data-stu-id="4d50d-125">However, we can summarize the common questions we try to answer when deciding what to work on next:</span></span>

1. <span data-ttu-id="4d50d-126">**この機能を使用する開発者の数は? アプリケーション/エクスペリエンスをどの程度向上させますか?**</span><span class="sxs-lookup"><span data-stu-id="4d50d-126">**How many developers we think will use the feature and how much better will it make their applications/experience?**</span></span> <span data-ttu-id="4d50d-127">これに答えるために、多くのソースからフィードバックを収集します。問題についてのコメントと投票は、これらのソースの 1 つです。</span><span class="sxs-lookup"><span data-stu-id="4d50d-127">To answer this, we collect feedback from many sources — Comments and votes on issues is one of those sources.</span></span>

2. <span data-ttu-id="4d50d-128">**この機能をまだ実装していない場合、ユーザーが使用できる回避策とは?**</span><span class="sxs-lookup"><span data-stu-id="4d50d-128">**What are the workarounds people can use if we don't implement this feature yet?**</span></span> <span data-ttu-id="4d50d-129">たとえば、多対多のネイティブ サポートがないことを回避するために、多くの開発者は統合テーブルをマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="4d50d-129">For example, many developers can map a join table to work around lack of native many-to-many support.</span></span> <span data-ttu-id="4d50d-130">当然ながら、すべての開発者がこれを実行するわけではありませんが、その多くは実行できます。そしてこれは決定の要因としてカウントされます。</span><span class="sxs-lookup"><span data-stu-id="4d50d-130">Obviously, not all developers want to do it, but many can, and that counts as a factor in our decision.</span></span>

3. <span data-ttu-id="4d50d-131">**他の機能を実装させるようにするなど、この機能の実装によって EF Core のアーキテクチャは進化しますか?**</span><span class="sxs-lookup"><span data-stu-id="4d50d-131">**Does implementing this feature evolve the architecture of EF Core such that it moves us closer to implementing other features?**</span></span> <span data-ttu-id="4d50d-132">他の機能の構成要素として動作する機能は優先される傾向があります。</span><span class="sxs-lookup"><span data-stu-id="4d50d-132">We tend to favor features that act as building blocks for other features.</span></span> <span data-ttu-id="4d50d-133">たとえば、プロパティ バッグ エンティティによって多対多のサポートを進めることができ、エンティティ コンストラクターによって遅延読み込みのサポートが可能になりました。</span><span class="sxs-lookup"><span data-stu-id="4d50d-133">For example, property bag entities can help us move towards many-to-many support, and entity constructors enabled our lazy loading support.</span></span>

4. <span data-ttu-id="4d50d-134">**この機能は拡張ポイントですか?**</span><span class="sxs-lookup"><span data-stu-id="4d50d-134">**Is the feature an extensibility point?**</span></span> <span data-ttu-id="4d50d-135">拡張ポイントは通常の機能よりも優先される傾向があります。なぜなら、開発者がこれらを使用して、それぞれの独自の動作をフックし、不足している機能を補うことができるためです。</span><span class="sxs-lookup"><span data-stu-id="4d50d-135">We tend to favor extensibility points over normal features because they enable developers to hook their own behaviors and compensate for any missing functionality.</span></span>

5. <span data-ttu-id="4d50d-136">**他の製品と組み合わせて使用するときの機能のシナジーとは何ですか?**</span><span class="sxs-lookup"><span data-stu-id="4d50d-136">**What is the synergy of the feature when used in combination with other products?**</span></span> <span data-ttu-id="4d50d-137">.NET Core、最新バージョンの Visual Studio、Microsoft Azure など、その他の製品と共に EF Core を使用するエクスペリエンスを可能にする、または大幅に向上させる機能は、優先される傾向があります。</span><span class="sxs-lookup"><span data-stu-id="4d50d-137">We favor features that enable or significantly improve the experience of using EF Core with other products, such as .NET Core, the latest version of Visual Studio, Microsoft Azure, etc.</span></span>

6. <span data-ttu-id="4d50d-138">**機能に取り組むために利用できるユーザーのスキルは何ですか? これらのリソースを最大限に活用する方法はありますか?**</span><span class="sxs-lookup"><span data-stu-id="4d50d-138">**What are the skills of the people available to work on a feature, and how to best leverage these resources?**</span></span> <span data-ttu-id="4d50d-139">EF チームの各メンバーおよびコミュニティの共同作成者は、異なる領域におけるさまざまなレベルの経験を持っているので、それに応じて計画を立てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="4d50d-139">Each member of the EF team and our community contributors has different levels of experience in different areas, so we have to plan accordingly.</span></span> <span data-ttu-id="4d50d-140">GroupBy の変換や多対多など、特定の機能に取り組むために "全員の協力" が欲しくなる場合であっても、それは実用的ではありません。</span><span class="sxs-lookup"><span data-stu-id="4d50d-140">Even if we wanted to have "all hands on deck" to work on a specific feature like GroupBy translations, or many-to-many, that wouldn't be practical.</span></span>

<span data-ttu-id="4d50d-141">前述のように、プロセスはリリースごとに進化していきます。</span><span class="sxs-lookup"><span data-stu-id="4d50d-141">As mentioned before, the process evolves on every release.</span></span>
<span data-ttu-id="4d50d-142">今後は、コミュニティのメンバーがリリースの計画にインプットを提供する機会を、もっと増やす予定です。</span><span class="sxs-lookup"><span data-stu-id="4d50d-142">In the future we'll try to add more opportunities for members of the community to provide inputs into our release plans.</span></span>
<span data-ttu-id="4d50d-143">たとえば、機能やリリースの計画自体の設計案を、簡単に確認できるようにしたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="4d50d-143">For example, we would like to make it easier to review draft designs of the features and of the release plan itself.</span></span>
