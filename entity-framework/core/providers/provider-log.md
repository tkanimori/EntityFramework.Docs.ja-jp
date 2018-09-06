---
title: プロバイダーに影響を与える変更 - EF Core のログ
author: ajcvickers
ms.author: avickers
ms.date: 08/08/2018
ms.assetid: 7CEF496E-A5B0-4F5F-B68E-529609B23EF9
ms.technology: entity-framework-core
uid: core/providers/provider-log
ms.openlocfilehash: 44b200223153fca44cb2cfa3e78b3bedc7b4a552
ms.sourcegitcommit: a81aed575372637997b18a0f9466d8fefb33350a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2018
ms.locfileid: "43821336"
---
# <a name="provider-impacting-changes"></a><span data-ttu-id="2cd32-102">プロバイダーに影響を与える変更</span><span class="sxs-lookup"><span data-stu-id="2cd32-102">Provider-impacting changes</span></span>

<span data-ttu-id="2cd32-103">このページには、プル要求に対応するためには、他のデータベース プロバイダーの作成者が必要な EF Core リポジトリへのリンクが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2cd32-103">This page contains links to pull requests made on the EF Core repo that may require authors of other database providers to react.</span></span> <span data-ttu-id="2cd32-104">目的がプロバイダーを新しいバージョンに更新するときに、既存のサードパーティ データベース プロバイダーの作成者の開始点を提供することです。</span><span class="sxs-lookup"><span data-stu-id="2cd32-104">The intention is to provide a starting point for authors of existing third-party database providers when updating their provider to a new version.</span></span>

<span data-ttu-id="2cd32-105">2.1 から 2.2 への変更でこのログを開始します。</span><span class="sxs-lookup"><span data-stu-id="2cd32-105">We are starting this log with changes from 2.1 to 2.2.</span></span> <span data-ttu-id="2cd32-106">使用して 2.1 より前、 [ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)と[ `providers-fyi` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)問題とプル要求のラベル。</span><span class="sxs-lookup"><span data-stu-id="2cd32-106">Prior to 2.1 we used the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) and [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) labels on our issues and pull requests.</span></span>

### <a name="21-----22"></a><span data-ttu-id="2cd32-107">2.1 2.2---> します。</span><span class="sxs-lookup"><span data-stu-id="2cd32-107">2.1 ---> 2.2</span></span>

#### <a name="test-only-changes"></a><span data-ttu-id="2cd32-108">テスト専用の変更</span><span class="sxs-lookup"><span data-stu-id="2cd32-108">Test-only changes</span></span>

* <span data-ttu-id="2cd32-109">https://github.com/aspnet/EntityFrameworkCore/pull/12057 -テストでカスタマイズ可能な SQL の区切り文字を許可します。</span><span class="sxs-lookup"><span data-stu-id="2cd32-109">https://github.com/aspnet/EntityFrameworkCore/pull/12057 - Allow customizable SQL delimeters in tests</span></span>
  * <span data-ttu-id="2cd32-110">非厳格フローティングできるようにするテストの変更ではポイントイン BuiltInDataTypesTestBase の比較</span><span class="sxs-lookup"><span data-stu-id="2cd32-110">Test changes that allow non-strict floating point comparisons in BuiltInDataTypesTestBase</span></span>
  * <span data-ttu-id="2cd32-111">別の SQL の区切り文字を再利用するクエリのテストを実行できるテストの変更</span><span class="sxs-lookup"><span data-stu-id="2cd32-111">Test changes that allow query tests to be re-used with different SQL delimeters</span></span>
* <span data-ttu-id="2cd32-112">https://github.com/aspnet/EntityFrameworkCore/pull/12072 -リレーショナル仕様テストに DbFunction テストを追加します。</span><span class="sxs-lookup"><span data-stu-id="2cd32-112">https://github.com/aspnet/EntityFrameworkCore/pull/12072 - Add DbFunction tests to the relational specification tests</span></span>
  * <span data-ttu-id="2cd32-113">これらのテストは、すべてのデータベース プロバイダーに対して実行できるように</span><span class="sxs-lookup"><span data-stu-id="2cd32-113">Such that these tests can be run against all database providers</span></span>
* <span data-ttu-id="2cd32-114">https://github.com/aspnet/EntityFrameworkCore/pull/12362 でテストは、非同期のクリーンアップ</span><span class="sxs-lookup"><span data-stu-id="2cd32-114">https://github.com/aspnet/EntityFrameworkCore/pull/12362 - Async test cleanup</span></span>
  * <span data-ttu-id="2cd32-115">削除`Wait`呼び出し、非同期、不要ないくつかのテスト メソッドの名前を変更</span><span class="sxs-lookup"><span data-stu-id="2cd32-115">Remove `Wait` calls, unneeded async, and renamed some test methods</span></span>
* <span data-ttu-id="2cd32-116">https://github.com/aspnet/EntityFrameworkCore/pull/12666 -ログ記録テスト インフラストラクチャを統合します。</span><span class="sxs-lookup"><span data-stu-id="2cd32-116">https://github.com/aspnet/EntityFrameworkCore/pull/12666 - Unify logging test infrastructure</span></span>
  * <span data-ttu-id="2cd32-117">追加`CreateListLoggerFactory`対応するため、これらのテストを使用して、プロバイダーが必要になりますいくつかの以前のログ記録インフラストラクチャの削除</span><span class="sxs-lookup"><span data-stu-id="2cd32-117">Added `CreateListLoggerFactory` and removed some previous logging infrastructure, which will require providers using these tests to react</span></span>
* <span data-ttu-id="2cd32-118">https://github.com/aspnet/EntityFrameworkCore/pull/12500 -同期的および非同期的に複数のクエリのテストを実行します。</span><span class="sxs-lookup"><span data-stu-id="2cd32-118">https://github.com/aspnet/EntityFrameworkCore/pull/12500 - Run more query tests both synchronously and asynchronously</span></span>
  * <span data-ttu-id="2cd32-119">テスト名およびファクタリングが変更された、対応するため、これらのテストを使用して、プロバイダーが必要になります</span><span class="sxs-lookup"><span data-stu-id="2cd32-119">Test names and factoring has changed, which will require providers using these tests to react</span></span>
* <span data-ttu-id="2cd32-120">https://github.com/aspnet/EntityFrameworkCore/pull/12766 名前変更、ComplexNavigations モデルでのナビゲーション</span><span class="sxs-lookup"><span data-stu-id="2cd32-120">https://github.com/aspnet/EntityFrameworkCore/pull/12766 - Renaming navigations in the ComplexNavigations model</span></span>
  * <span data-ttu-id="2cd32-121">これらのテストを使用して、プロバイダーは、対応する必要があります。</span><span class="sxs-lookup"><span data-stu-id="2cd32-121">Providers using these tests may need to react</span></span>
* <span data-ttu-id="2cd32-122">https://github.com/aspnet/EntityFrameworkCore/pull/12141 -機能テストの破棄ではなく、プールにコンテキストを返します</span><span class="sxs-lookup"><span data-stu-id="2cd32-122">https://github.com/aspnet/EntityFrameworkCore/pull/12141 - Return the context to the pool instead of disposing in functional tests</span></span>
  * <span data-ttu-id="2cd32-123">この変更には、対応するためのプロバイダーを必要がありますテスト リファクタリングが含まれています。</span><span class="sxs-lookup"><span data-stu-id="2cd32-123">This change includes some test refactoring which may require providers to react</span></span>


#### <a name="test-and-product-code-changes"></a><span data-ttu-id="2cd32-124">テストと製品コードの変更</span><span class="sxs-lookup"><span data-stu-id="2cd32-124">Test and product code changes</span></span>

* <span data-ttu-id="2cd32-125">https://github.com/aspnet/EntityFrameworkCore/pull/12109 -統合 RelationalTypeMapping.Clone メソッド</span><span class="sxs-lookup"><span data-stu-id="2cd32-125">https://github.com/aspnet/EntityFrameworkCore/pull/12109 - Consolidate RelationalTypeMapping.Clone methods</span></span>
  * <span data-ttu-id="2cd32-126">派生クラスでの単純化するため、RelationalTypeMapping 2.1 での変更を許可します。</span><span class="sxs-lookup"><span data-stu-id="2cd32-126">Changes in 2.1 to the RelationalTypeMapping allowed for a simplification in derived classes.</span></span> <span data-ttu-id="2cd32-127">私たちを信じて、プロバイダーの重大ながこれが、プロバイダーを利用してこの変更の派生型のマッピング クラス。</span><span class="sxs-lookup"><span data-stu-id="2cd32-127">We don't believe this was breaking to providers, but providers can take advantage of this change in their derived type mapping classes.</span></span>
* <span data-ttu-id="2cd32-128">https://github.com/aspnet/EntityFrameworkCore/pull/12069 -タグが付けられたまたは名前付きクエリ</span><span class="sxs-lookup"><span data-stu-id="2cd32-128">https://github.com/aspnet/EntityFrameworkCore/pull/12069 - Tagged or named queries</span></span>
  * <span data-ttu-id="2cd32-129">LINQ クエリをタグ付けし、それらのタグを SQL 内のコメントとして表示するためのインフラストラクチャを追加します。</span><span class="sxs-lookup"><span data-stu-id="2cd32-129">Adds infrastructure for tagging LINQ queries and having those tags show up as comments in the SQL.</span></span> <span data-ttu-id="2cd32-130">SQL の生成に対応するためのプロバイダーが必要です。</span><span class="sxs-lookup"><span data-stu-id="2cd32-130">This may require providers to react in SQL generation.</span></span>
* <span data-ttu-id="2cd32-131">https://github.com/aspnet/EntityFrameworkCore/pull/13115 -NTS を使用して空間データをサポートします。</span><span class="sxs-lookup"><span data-stu-id="2cd32-131">https://github.com/aspnet/EntityFrameworkCore/pull/13115 - Support spatial data via NTS</span></span>
  * <span data-ttu-id="2cd32-132">型のマッピングとメンバー翻訳外部プロバイダーを登録するのには、します。</span><span class="sxs-lookup"><span data-stu-id="2cd32-132">Allows type mappings and member translators to be registered outside of the provider</span></span>
    * <span data-ttu-id="2cd32-133">プロバイダーは、基本で呼び出す必要があります。動作するためには、その ITypeMappingSource 実装 FindMapping()</span><span class="sxs-lookup"><span data-stu-id="2cd32-133">Providers must call base.FindMapping() in their ITypeMappingSource implementation for it to work</span></span>
  * <span data-ttu-id="2cd32-134">プロバイダー間で一貫性のあるプロバイダーに空間的なサポートを追加するには、このパターンに従います。</span><span class="sxs-lookup"><span data-stu-id="2cd32-134">Follow this pattern to add spatial support to your provider that is consistent across providers.</span></span>

