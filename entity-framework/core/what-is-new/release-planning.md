---
title: EF Core のリリースの計画
author: ajcvickers
ms.date: 01/14/2020
uid: core/what-is-new/release_planning.md
ms.openlocfilehash: c60040aa4acc33ba8b5a54b619539b275690f70e
ms.sourcegitcommit: f2a38c086291699422d8b28a72d9611d1b24ad0d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76125368"
---
# <a name="release-planning-process"></a><span data-ttu-id="68ff7-102">リリースの計画プロセス</span><span class="sxs-lookup"><span data-stu-id="68ff7-102">Release planning process</span></span>

<span data-ttu-id="68ff7-103">特定のリリースに含める特定の機能をどのように選ぶかについて、よく質問を受けます。</span><span class="sxs-lookup"><span data-stu-id="68ff7-103">We often get questions about how we choose specific features to go into a particular release.</span></span>
<span data-ttu-id="68ff7-104">バックログは確かに、リリースの計画に自動的に変換されるわけではありません。</span><span class="sxs-lookup"><span data-stu-id="68ff7-104">Our backlog certainly doesn't automatically translate into release plans.</span></span>
<span data-ttu-id="68ff7-105">また、ある機能が EF6 に存在する場合、自動的にその機能を EF Core に実装する必要があるというわけでもありません。</span><span class="sxs-lookup"><span data-stu-id="68ff7-105">The presence of a feature in EF6 also doesn't automatically mean that the feature needs to be implemented in EF Core.</span></span>

<span data-ttu-id="68ff7-106">リリースを計画するために実行するプロセス全体について詳しく説明することは困難です。</span><span class="sxs-lookup"><span data-stu-id="68ff7-106">It's difficult to detail the whole process we follow to plan a release.</span></span>
<span data-ttu-id="68ff7-107">その大半は、特定の機能、機会、優先度に関する議論であり、プロセス自体もリリースごとに進化します。</span><span class="sxs-lookup"><span data-stu-id="68ff7-107">Much of it is discussing the specific features, opportunities and priorities, and the process itself also evolves with every release.</span></span>
<span data-ttu-id="68ff7-108">ただし、次の作業内容を決定するときに答えようと試みている共通の質問についてまとめることは可能です。</span><span class="sxs-lookup"><span data-stu-id="68ff7-108">However, we can summarize the common questions we try to answer when deciding what to work on next:</span></span>

1. <span data-ttu-id="68ff7-109">**この機能を使用する開発者の数は? アプリケーション/エクスペリエンスをどの程度向上させますか?**</span><span class="sxs-lookup"><span data-stu-id="68ff7-109">**How many developers we think will use the feature and how much better will it make their applications or experience?**</span></span> <span data-ttu-id="68ff7-110">この質問に答えるために、多くのソースからフィードバックを収集します。問題についてのコメントと投票は、これらのソースの 1 つです。</span><span class="sxs-lookup"><span data-stu-id="68ff7-110">To answer this question, we collect feedback from many sources — Comments and votes on issues is one of those sources.</span></span>

2. <span data-ttu-id="68ff7-111">**この機能をまだ実装していない場合、ユーザーが使用できる回避策とは?**</span><span class="sxs-lookup"><span data-stu-id="68ff7-111">**What are the workarounds people can use if we don't implement this feature yet?**</span></span> <span data-ttu-id="68ff7-112">たとえば、多対多のネイティブ サポートがないことを回避するために、多くの開発者は統合テーブルをマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="68ff7-112">For example, many developers can map a join table to work around lack of native many-to-many support.</span></span> <span data-ttu-id="68ff7-113">当然ながら、すべての開発者がこれを実行するわけではありませんが、その多くは実行できます。そしてこれは決定の要因としてカウントされます。</span><span class="sxs-lookup"><span data-stu-id="68ff7-113">Obviously, not all developers want to do it, but many can, and that counts as a factor in our decision.</span></span>

3. <span data-ttu-id="68ff7-114">**他の機能を実装させるようにするなど、この機能の実装によって EF Core のアーキテクチャは進化しますか?**</span><span class="sxs-lookup"><span data-stu-id="68ff7-114">**Does implementing this feature evolve the architecture of EF Core such that it moves us closer to implementing other features?**</span></span> <span data-ttu-id="68ff7-115">他の機能の構成要素として動作する機能は優先される傾向があります。</span><span class="sxs-lookup"><span data-stu-id="68ff7-115">We tend to favor features that act as building blocks for other features.</span></span> <span data-ttu-id="68ff7-116">たとえば、プロパティ バッグ エンティティによって多対多のサポートを進めることができ、エンティティ コンストラクターによって遅延読み込みのサポートが可能になりました。</span><span class="sxs-lookup"><span data-stu-id="68ff7-116">For instance, property bag entities can help us move towards many-to-many support, and entity constructors enabled our lazy loading support.</span></span>

4. <span data-ttu-id="68ff7-117">**この機能は拡張ポイントですか?**</span><span class="sxs-lookup"><span data-stu-id="68ff7-117">**Is the feature an extensibility point?**</span></span> <span data-ttu-id="68ff7-118">拡張ポイントは通常の機能よりも優先される傾向があります。なぜなら、開発者がこれらを使用して、それぞれの独自の動作をフックし、不足している機能を補うことができるためです。</span><span class="sxs-lookup"><span data-stu-id="68ff7-118">We tend to favor extensibility points over normal features because they enable developers to hook their own behaviors and compensate for any missing functionality.</span></span>

5. <span data-ttu-id="68ff7-119">**他の製品と組み合わせて使用するときの機能のシナジーとは何ですか?**</span><span class="sxs-lookup"><span data-stu-id="68ff7-119">**What is the synergy of the feature when used in combination with other products?**</span></span> <span data-ttu-id="68ff7-120">.NET Core、最新バージョンの Visual Studio、Microsoft Azure など、その他の製品と共に EF Core を使用するエクスペリエンスを可能にする、または大幅に向上させる機能は、優先される傾向があります。</span><span class="sxs-lookup"><span data-stu-id="68ff7-120">We favor features that enable or significantly improve the experience of using EF Core with other products, such as .NET Core, the latest version of Visual Studio, Microsoft Azure, and so on.</span></span>

6. <span data-ttu-id="68ff7-121">**機能に取り組むために利用できるユーザーのスキルは何ですか? これらのリソースを最大限に活用する方法はありますか?**</span><span class="sxs-lookup"><span data-stu-id="68ff7-121">**What are the skills of the people available to work on a feature, and how to best leverage these resources?**</span></span> <span data-ttu-id="68ff7-122">EF チームの各メンバーおよびコミュニティの共同作成者は、異なる領域におけるさまざまなレベルの経験を持っているので、それに応じて計画を立てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="68ff7-122">Each member of the EF team and our community contributors has different levels of experience in different areas, so we have to plan accordingly.</span></span> <span data-ttu-id="68ff7-123">GroupBy の変換や多対多など、特定の機能に取り組むために "全員の協力" が欲しくなる場合であっても、それは実用的ではありません。</span><span class="sxs-lookup"><span data-stu-id="68ff7-123">Even if we wanted to have "all hands on deck" to work on a specific feature like GroupBy translations, or many-to-many, that wouldn't be practical.</span></span>

<span data-ttu-id="68ff7-124">前述のように、プロセスはリリースごとに進化していきます。</span><span class="sxs-lookup"><span data-stu-id="68ff7-124">As mentioned before, the process evolves on every release.</span></span>
<span data-ttu-id="68ff7-125">今後は、コミュニティのメンバーがリリースの計画にインプットを提供する機会を、もっと増やす予定です。</span><span class="sxs-lookup"><span data-stu-id="68ff7-125">In the future, we'll try to add more opportunities for members of the community to provide inputs into our release plans.</span></span>
<span data-ttu-id="68ff7-126">たとえば、機能やリリースの計画自体の設計案を、簡単に確認できるようにしたいと考えています。</span><span class="sxs-lookup"><span data-stu-id="68ff7-126">For example, we would like to make it easier to review draft designs of the features and of the release plan itself.</span></span>

## <a name="backlog"></a><span data-ttu-id="68ff7-127">Backlog</span><span class="sxs-lookup"><span data-stu-id="68ff7-127">Backlog</span></span>

<span data-ttu-id="68ff7-128">問題の追跡ツールの[バックログ マイルストーン](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc)には、いつか取り組む予定の問題や、コミュニティのだれかが対処できると考えられる問題が含まれています。</span><span class="sxs-lookup"><span data-stu-id="68ff7-128">The [Backlog Milestone](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog+sort%3Areactions-%2B1-desc) in our issue tracker contains issues that we either expect to work on someday, or we think someone from the community could tackle.</span></span>
<span data-ttu-id="68ff7-129">これらの問題に関するお客様からのコメントや投票をお待ちしております。</span><span class="sxs-lookup"><span data-stu-id="68ff7-129">Customers are welcome to submit comments and votes on these issues.</span></span>
<span data-ttu-id="68ff7-130">これらの問題のいずれかに取り組むことを考えている共同作成者は、まずそれに対するアプローチ方法について議論を始めることが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="68ff7-130">Contributors looking to work on any of these issues are encouraged to first start a discussion on how to approach them.</span></span>

<span data-ttu-id="68ff7-131">特定のバージョンの EF Core において特定の機能に取り組むという保証は、いかなる場合もありません。</span><span class="sxs-lookup"><span data-stu-id="68ff7-131">There's never a guarantee that we'll work on any given feature in a specific version of EF Core.</span></span>
<span data-ttu-id="68ff7-132">すべてのソフトウェア プロジェクトと同じように、優先順位、リリース スケジュール、および使用可能なリソースは常に変更される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="68ff7-132">As in all software projects, priorities, release schedules, and available resources can change at any point.</span></span>
<span data-ttu-id="68ff7-133">ただし、特定の時間枠の中で問題を解決する予定の場合は、バックログ マイルストーンではなくリリース マイルストーンにそれを割り当てます。</span><span class="sxs-lookup"><span data-stu-id="68ff7-133">But if we intend to resolve an issue in a specific time-frame, we'll assign it to a release milestone instead of the backlog milestone.</span></span>

<span data-ttu-id="68ff7-134">対応する予定がない場合、その問題は閉じられる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="68ff7-134">We'll likely close an issue if we don't plan to ever address it.</span></span>
<span data-ttu-id="68ff7-135">ただし、以前に閉じた問題に関する新情報が得られた場合は、それを再検討する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="68ff7-135">But we can reconsider an issue that we previously closed if we get new information about it.</span></span>
