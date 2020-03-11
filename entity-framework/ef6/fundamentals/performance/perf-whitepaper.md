---
title: EF4、EF5、および EF6 のパフォーマンスに関する考慮事項-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 07eb605f0d39f0c1bcfe781540525180f0dd0b22
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416079"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a><span data-ttu-id="a15b4-102">EF 4、5、および6のパフォーマンスに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="a15b4-102">Performance considerations for EF 4, 5, and 6</span></span>
<span data-ttu-id="a15b4-103">David Obando、Eric Dettinger、その他</span><span class="sxs-lookup"><span data-stu-id="a15b4-103">By David Obando, Eric Dettinger and others</span></span>

<span data-ttu-id="a15b4-104">公開日: 2012 年4月</span><span class="sxs-lookup"><span data-stu-id="a15b4-104">Published: April 2012</span></span>

<span data-ttu-id="a15b4-105">最終更新日: 2014 年5月</span><span class="sxs-lookup"><span data-stu-id="a15b4-105">Last updated: May 2014</span></span>

------------------------------------------------------------------------

## <a name="1-introduction"></a><span data-ttu-id="a15b4-106">1. 概要</span><span class="sxs-lookup"><span data-stu-id="a15b4-106">1. Introduction</span></span>

<span data-ttu-id="a15b4-107">オブジェクトリレーショナルマッピングフレームワークは、オブジェクト指向アプリケーションでのデータアクセスの抽象化を提供するのに便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-107">Object-Relational Mapping frameworks are a convenient way to provide an abstraction for data access in an object-oriented application.</span></span> <span data-ttu-id="a15b4-108">.NET アプリケーションの場合、Microsoft の推奨される O/RM が Entity Framework ます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-108">For .NET applications, Microsoft's recommended O/RM is Entity Framework.</span></span> <span data-ttu-id="a15b4-109">ただし、抽象化を使用すると、パフォーマンスが問題になる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-109">With any abstraction though, performance can become a concern.</span></span>

<span data-ttu-id="a15b4-110">このホワイトペーパーは、Entity Framework を使用してアプリケーションを開発するときのパフォーマンスに関する考慮事項を示し、開発者がパフォーマンスに影響を与える可能性がある Entity Framework 内部アルゴリズムを理解し、調査のヒントを提供するために書かれています。Entity Framework を使用したアプリケーションのパフォーマンスの向上。</span><span class="sxs-lookup"><span data-stu-id="a15b4-110">This whitepaper was written to show the performance considerations when developing applications using Entity Framework, to give developers an idea of the Entity Framework internal algorithms that can affect performance, and to provide tips for investigation and improving performance in their applications that use Entity Framework.</span></span> <span data-ttu-id="a15b4-111">Web で既に利用可能なパフォーマンスに関する多くの優れたトピックがあります。また、可能な場合は、これらのリソースをポイントすることもできました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-111">There are a number of good topics on performance already available on the web, and we've also tried pointing to these resources where possible.</span></span>

<span data-ttu-id="a15b4-112">パフォーマンスは、厄介なトピックです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-112">Performance is a tricky topic.</span></span> <span data-ttu-id="a15b4-113">このホワイトペーパーは、Entity Framework を使用するアプリケーションについて、パフォーマンスに関する意思決定を行うのに役立つリソースとして提供されています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-113">This whitepaper is intended as a resource to help you make performance related decisions for your applications that use Entity Framework.</span></span> <span data-ttu-id="a15b4-114">パフォーマンスを実証するためのテストメトリックがいくつか含まれていますが、これらのメトリックは、アプリケーションに表示されるパフォーマンスの絶対インジケーターとしては意図されていません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-114">We have included some test metrics to demonstrate performance, but these metrics aren't intended as absolute indicators of the performance you will see in your application.</span></span>

<span data-ttu-id="a15b4-115">実際には、このドキュメントでは Entity Framework 4 が .NET 4.0 で実行され、Entity Framework 5 および6は .NET 4.5 で実行されることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-115">For practical purposes, this document assumes Entity Framework 4 is run under .NET 4.0 and Entity Framework 5 and 6 are run under .NET 4.5.</span></span> <span data-ttu-id="a15b4-116">Entity Framework 5 に加えられたパフォーマンスの向上の多くは、.NET 4.5 に付属するコアコンポーネント内に存在します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-116">Many of the performance improvements made for Entity Framework 5 reside within the core components that ship with .NET 4.5.</span></span>

<span data-ttu-id="a15b4-117">Entity Framework 6 は帯域外リリースであり、.NET に付属する Entity Framework コンポーネントには依存しません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-117">Entity Framework 6 is an out of band release and does not depend on the Entity Framework components that ship with .NET.</span></span> <span data-ttu-id="a15b4-118">Entity Framework 6 は .net 4.0 と .NET 4.5 の両方で動作し、.NET 4.0 からアップグレードしていないが、アプリケーションで最新の Entity Framework ビットを必要とするユーザーにとって、大きなパフォーマンス上の利点を提供できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-118">Entity Framework 6 work on both .NET 4.0 and .NET 4.5, and can offer a big performance benefit to those who haven’t upgraded from .NET 4.0 but want the latest Entity Framework bits in their application.</span></span> <span data-ttu-id="a15b4-119">このドキュメントには Entity Framework 6 と記載されていますが、このドキュメントの執筆時点で利用可能な最新バージョンを参照しています: バージョン6.1.0。</span><span class="sxs-lookup"><span data-stu-id="a15b4-119">When this document mentions Entity Framework 6, it refers to the latest version available at the time of this writing: version 6.1.0.</span></span>

## <a name="2-cold-vs-warm-query-execution"></a><span data-ttu-id="a15b4-120">2. コールドクエリとウォームクエリの実行</span><span class="sxs-lookup"><span data-stu-id="a15b4-120">2. Cold vs. Warm Query Execution</span></span>

<span data-ttu-id="a15b4-121">特定のモデルに対してクエリを初めて実行すると、Entity Framework は、モデルの読み込みと検証のために多くの作業をバックグラウンドで実行します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-121">The very first time any query is made against a given model, the Entity Framework does a lot of work behind the scenes to load and validate the model.</span></span> <span data-ttu-id="a15b4-122">この最初のクエリは、"コールド" クエリとして頻繁に参照されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-122">We frequently refer to this first query as a "cold" query.</span></span><span data-ttu-id="a15b4-123">  既に読み込まれているモデルに対するクエリは、"ウォーム" クエリとも呼ばれ、はるかに高速です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-123">  Further queries against an already loaded model are known as "warm" queries, and are much faster.</span></span>

<span data-ttu-id="a15b4-124">ここでは、Entity Framework を使用してクエリを実行するときに時間が費やされる場所の概要を示し、Entity Framework 6 での改善点について説明します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-124">Let’s take a high-level view of where time is spent when executing a query using Entity Framework, and see where things are improving in Entity Framework 6.</span></span>

<span data-ttu-id="a15b4-125">**最初のクエリ実行–コールドクエリ**</span><span class="sxs-lookup"><span data-stu-id="a15b4-125">**First Query Execution – cold query**</span></span>

| <span data-ttu-id="a15b4-126">コードユーザーの書き込み</span><span class="sxs-lookup"><span data-stu-id="a15b4-126">Code User Writes</span></span>                                                                                     | <span data-ttu-id="a15b4-127">アクション</span><span class="sxs-lookup"><span data-stu-id="a15b4-127">Action</span></span>                    | <span data-ttu-id="a15b4-128">EF4 のパフォーマンスへの影響</span><span class="sxs-lookup"><span data-stu-id="a15b4-128">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="a15b4-129">EF5 のパフォーマンスへの影響</span><span class="sxs-lookup"><span data-stu-id="a15b4-129">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="a15b4-130">EF6 のパフォーマンスへの影響</span><span class="sxs-lookup"><span data-stu-id="a15b4-130">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="a15b4-131">コンテキストの作成</span><span class="sxs-lookup"><span data-stu-id="a15b4-131">Context creation</span></span>          | <span data-ttu-id="a15b4-132">Medium</span><span class="sxs-lookup"><span data-stu-id="a15b4-132">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="a15b4-133">Medium</span><span class="sxs-lookup"><span data-stu-id="a15b4-133">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="a15b4-134">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-134">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="a15b4-135">クエリ式の作成</span><span class="sxs-lookup"><span data-stu-id="a15b4-135">Query expression creation</span></span> | <span data-ttu-id="a15b4-136">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-136">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="a15b4-137">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-137">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="a15b4-138">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-138">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="a15b4-139">LINQ クエリの実行</span><span class="sxs-lookup"><span data-stu-id="a15b4-139">LINQ query execution</span></span>      | <span data-ttu-id="a15b4-140">-メタデータの読み込み: 高ですが、キャッシュされています</span><span class="sxs-lookup"><span data-stu-id="a15b4-140">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="a15b4-141">-ビューの生成: 非常に高い可能性があり、キャッシュされている可能性があります</span><span class="sxs-lookup"><span data-stu-id="a15b4-141">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="a15b4-142">-パラメーターの評価: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-142">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="a15b4-143">-クエリ変換: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-143">- Query translation: Medium</span></span> <br/> <span data-ttu-id="a15b4-144">-具体化 generation: Medium、cached</span><span class="sxs-lookup"><span data-stu-id="a15b4-144">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="a15b4-145">-データベースクエリの実行: 高い可能性があります</span><span class="sxs-lookup"><span data-stu-id="a15b4-145">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="a15b4-146">+ 接続します。開く</span><span class="sxs-lookup"><span data-stu-id="a15b4-146">+ Connection.Open</span></span> <br/> <span data-ttu-id="a15b4-147">+ コマンド ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="a15b4-147">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="a15b4-148">+ DataReader。読み取り</span><span class="sxs-lookup"><span data-stu-id="a15b4-148">+ DataReader.Read</span></span> <br/> <span data-ttu-id="a15b4-149">オブジェクトの具体化: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-149">Object materialization: Medium</span></span> <br/> <span data-ttu-id="a15b4-150">-Id 参照: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-150">- Identity lookup: Medium</span></span> | <span data-ttu-id="a15b4-151">-メタデータの読み込み: 高ですが、キャッシュされています</span><span class="sxs-lookup"><span data-stu-id="a15b4-151">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="a15b4-152">-ビューの生成: 非常に高い可能性があり、キャッシュされている可能性があります</span><span class="sxs-lookup"><span data-stu-id="a15b4-152">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="a15b4-153">-Parameter 評価: 低</span><span class="sxs-lookup"><span data-stu-id="a15b4-153">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="a15b4-154">-クエリ変換: 中、キャッシュ済み</span><span class="sxs-lookup"><span data-stu-id="a15b4-154">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="a15b4-155">-具体化 generation: Medium、cached</span><span class="sxs-lookup"><span data-stu-id="a15b4-155">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="a15b4-156">-データベースクエリの実行: 高可用 (状況によってはクエリが向上)</span><span class="sxs-lookup"><span data-stu-id="a15b4-156">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="a15b4-157">+ 接続します。開く</span><span class="sxs-lookup"><span data-stu-id="a15b4-157">+ Connection.Open</span></span> <br/> <span data-ttu-id="a15b4-158">+ コマンド ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="a15b4-158">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="a15b4-159">+ DataReader。読み取り</span><span class="sxs-lookup"><span data-stu-id="a15b4-159">+ DataReader.Read</span></span> <br/> <span data-ttu-id="a15b4-160">オブジェクトの具体化: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-160">Object materialization: Medium</span></span> <br/> <span data-ttu-id="a15b4-161">-Id 参照: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-161">- Identity lookup: Medium</span></span> | <span data-ttu-id="a15b4-162">-メタデータの読み込み: 高ですが、キャッシュされています</span><span class="sxs-lookup"><span data-stu-id="a15b4-162">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="a15b4-163">-ビューの生成: 中ですが、キャッシュされています</span><span class="sxs-lookup"><span data-stu-id="a15b4-163">- View generation: Medium but cached</span></span> <br/> <span data-ttu-id="a15b4-164">-Parameter 評価: 低</span><span class="sxs-lookup"><span data-stu-id="a15b4-164">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="a15b4-165">-クエリ変換: 中、キャッシュ済み</span><span class="sxs-lookup"><span data-stu-id="a15b4-165">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="a15b4-166">-具体化 generation: Medium、cached</span><span class="sxs-lookup"><span data-stu-id="a15b4-166">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="a15b4-167">-データベースクエリの実行: 高可用 (状況によってはクエリが向上)</span><span class="sxs-lookup"><span data-stu-id="a15b4-167">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="a15b4-168">+ 接続します。開く</span><span class="sxs-lookup"><span data-stu-id="a15b4-168">+ Connection.Open</span></span> <br/> <span data-ttu-id="a15b4-169">+ コマンド ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="a15b4-169">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="a15b4-170">+ DataReader。読み取り</span><span class="sxs-lookup"><span data-stu-id="a15b4-170">+ DataReader.Read</span></span> <br/> <span data-ttu-id="a15b4-171">オブジェクトの具体化: 中 (EF5 より高速)</span><span class="sxs-lookup"><span data-stu-id="a15b4-171">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="a15b4-172">-Id 参照: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-172">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="a15b4-173">接続します。閉じる</span><span class="sxs-lookup"><span data-stu-id="a15b4-173">Connection.Close</span></span>          | <span data-ttu-id="a15b4-174">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-174">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="a15b4-175">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-175">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="a15b4-176">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-176">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


<span data-ttu-id="a15b4-177">**2番目のクエリの実行–ウォームクエリ**</span><span class="sxs-lookup"><span data-stu-id="a15b4-177">**Second Query Execution – warm query**</span></span>

| <span data-ttu-id="a15b4-178">コードユーザーの書き込み</span><span class="sxs-lookup"><span data-stu-id="a15b4-178">Code User Writes</span></span>                                                                                     | <span data-ttu-id="a15b4-179">アクション</span><span class="sxs-lookup"><span data-stu-id="a15b4-179">Action</span></span>                    | <span data-ttu-id="a15b4-180">EF4 のパフォーマンスへの影響</span><span class="sxs-lookup"><span data-stu-id="a15b4-180">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="a15b4-181">EF5 のパフォーマンスへの影響</span><span class="sxs-lookup"><span data-stu-id="a15b4-181">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="a15b4-182">EF6 のパフォーマンスへの影響</span><span class="sxs-lookup"><span data-stu-id="a15b4-182">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="a15b4-183">コンテキストの作成</span><span class="sxs-lookup"><span data-stu-id="a15b4-183">Context creation</span></span>          | <span data-ttu-id="a15b4-184">Medium</span><span class="sxs-lookup"><span data-stu-id="a15b4-184">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="a15b4-185">Medium</span><span class="sxs-lookup"><span data-stu-id="a15b4-185">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="a15b4-186">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-186">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="a15b4-187">クエリ式の作成</span><span class="sxs-lookup"><span data-stu-id="a15b4-187">Query expression creation</span></span> | <span data-ttu-id="a15b4-188">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-188">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="a15b4-189">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-189">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="a15b4-190">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-190">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="a15b4-191">LINQ クエリの実行</span><span class="sxs-lookup"><span data-stu-id="a15b4-191">LINQ query execution</span></span>      | <span data-ttu-id="a15b4-192">-メタデータ~~読み込み~~の参照:~~高、キャッシュ~~低</span><span class="sxs-lookup"><span data-stu-id="a15b4-192">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="a15b4-193">-ビューの~~生成~~の参照:~~非常に高い可能性があり、キャッシュ~~された低</span><span class="sxs-lookup"><span data-stu-id="a15b4-193">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="a15b4-194">-パラメーターの評価: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-194">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="a15b4-195">-クエリ~~変換~~の参照: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-195">- Query ~~translation~~ lookup: Medium</span></span> <br/> <span data-ttu-id="a15b4-196">-具体化~~generation~~ Lookup: ~~Medium、cached~~低</span><span class="sxs-lookup"><span data-stu-id="a15b4-196">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="a15b4-197">-データベースクエリの実行: 高い可能性があります</span><span class="sxs-lookup"><span data-stu-id="a15b4-197">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="a15b4-198">+ 接続します。開く</span><span class="sxs-lookup"><span data-stu-id="a15b4-198">+ Connection.Open</span></span> <br/> <span data-ttu-id="a15b4-199">+ コマンド ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="a15b4-199">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="a15b4-200">+ DataReader。読み取り</span><span class="sxs-lookup"><span data-stu-id="a15b4-200">+ DataReader.Read</span></span> <br/> <span data-ttu-id="a15b4-201">オブジェクトの具体化: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-201">Object materialization: Medium</span></span> <br/> <span data-ttu-id="a15b4-202">-Id 参照: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-202">- Identity lookup: Medium</span></span> | <span data-ttu-id="a15b4-203">-メタデータ~~読み込み~~の参照:~~高、キャッシュ~~低</span><span class="sxs-lookup"><span data-stu-id="a15b4-203">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="a15b4-204">-ビューの~~生成~~の参照:~~非常に高い可能性があり、キャッシュ~~された低</span><span class="sxs-lookup"><span data-stu-id="a15b4-204">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="a15b4-205">-Parameter 評価: 低</span><span class="sxs-lookup"><span data-stu-id="a15b4-205">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="a15b4-206">-クエリ~~変換~~の参照:~~中、キャッシュ~~低</span><span class="sxs-lookup"><span data-stu-id="a15b4-206">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="a15b4-207">-具体化~~generation~~ Lookup: ~~Medium、cached~~低</span><span class="sxs-lookup"><span data-stu-id="a15b4-207">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="a15b4-208">-データベースクエリの実行: 高可用 (状況によってはクエリが向上)</span><span class="sxs-lookup"><span data-stu-id="a15b4-208">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="a15b4-209">+ 接続します。開く</span><span class="sxs-lookup"><span data-stu-id="a15b4-209">+ Connection.Open</span></span> <br/> <span data-ttu-id="a15b4-210">+ コマンド ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="a15b4-210">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="a15b4-211">+ DataReader。読み取り</span><span class="sxs-lookup"><span data-stu-id="a15b4-211">+ DataReader.Read</span></span> <br/> <span data-ttu-id="a15b4-212">オブジェクトの具体化: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-212">Object materialization: Medium</span></span> <br/> <span data-ttu-id="a15b4-213">-Id 参照: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-213">- Identity lookup: Medium</span></span> | <span data-ttu-id="a15b4-214">-メタデータ~~読み込み~~の参照:~~高、キャッシュ~~低</span><span class="sxs-lookup"><span data-stu-id="a15b4-214">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="a15b4-215">-ビューの~~生成~~の参照:~~中で、キャッシュ~~された低</span><span class="sxs-lookup"><span data-stu-id="a15b4-215">- View ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="a15b4-216">-Parameter 評価: 低</span><span class="sxs-lookup"><span data-stu-id="a15b4-216">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="a15b4-217">-クエリ~~変換~~の参照:~~中、キャッシュ~~低</span><span class="sxs-lookup"><span data-stu-id="a15b4-217">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="a15b4-218">-具体化~~generation~~ Lookup: ~~Medium、cached~~低</span><span class="sxs-lookup"><span data-stu-id="a15b4-218">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="a15b4-219">-データベースクエリの実行: 高可用 (状況によってはクエリが向上)</span><span class="sxs-lookup"><span data-stu-id="a15b4-219">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="a15b4-220">+ 接続します。開く</span><span class="sxs-lookup"><span data-stu-id="a15b4-220">+ Connection.Open</span></span> <br/> <span data-ttu-id="a15b4-221">+ コマンド ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="a15b4-221">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="a15b4-222">+ DataReader。読み取り</span><span class="sxs-lookup"><span data-stu-id="a15b4-222">+ DataReader.Read</span></span> <br/> <span data-ttu-id="a15b4-223">オブジェクトの具体化: 中 (EF5 より高速)</span><span class="sxs-lookup"><span data-stu-id="a15b4-223">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="a15b4-224">-Id 参照: 中</span><span class="sxs-lookup"><span data-stu-id="a15b4-224">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="a15b4-225">接続します。閉じる</span><span class="sxs-lookup"><span data-stu-id="a15b4-225">Connection.Close</span></span>          | <span data-ttu-id="a15b4-226">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-226">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="a15b4-227">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-227">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="a15b4-228">低</span><span class="sxs-lookup"><span data-stu-id="a15b4-228">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


<span data-ttu-id="a15b4-229">コールドクエリとウォームクエリの両方のパフォーマンスコストを削減するには、いくつかの方法があります。これらについては、次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-229">There are several ways to reduce the performance cost of both cold and warm queries, and we'll take a look at these in the following section.</span></span> <span data-ttu-id="a15b4-230">具体的には、事前に生成されたビューを使用して、コールドクエリでのモデル読み込みのコストを削減することを検討します。これは、ビューの生成中に発生するパフォーマンスの問題を軽減するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-230">Specifically, we'll look at reducing the cost of model loading in cold queries by using pre-generated views, which should help alleviate performance pains experienced during view generation.</span></span> <span data-ttu-id="a15b4-231">ウォームクエリでは、クエリプランのキャッシュ、追跡クエリ、およびさまざまなクエリ実行オプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-231">For warm queries, we'll cover query plan caching, no tracking queries, and different query execution options.</span></span>

### <a name="21-what-is-view-generation"></a><span data-ttu-id="a15b4-232">2.1 ビューの生成とは</span><span class="sxs-lookup"><span data-stu-id="a15b4-232">2.1 What is View Generation?</span></span>

<span data-ttu-id="a15b4-233">ビューの世代を理解するために、まず、"マッピングビュー" とは何かを理解する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-233">In order to understand what view generation is, we must first understand what “Mapping Views” are.</span></span> <span data-ttu-id="a15b4-234">マッピングビューは、各エンティティセットとアソシエーションのマッピングで指定された変換の実行可能表現です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-234">Mapping Views are executable representations of the transformations specified in the mapping for each entity set and association.</span></span> <span data-ttu-id="a15b4-235">内部的には、これらのマッピングビューは CQTs (正規クエリツリー) の形になります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-235">Internally, these mapping views take the shape of CQTs (canonical query trees).</span></span> <span data-ttu-id="a15b4-236">マッピングビューには、次の2種類があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-236">There are two types of mapping views:</span></span>

-   <span data-ttu-id="a15b4-237">クエリビュー: データベーススキーマから概念モデルに移動するために必要な変換を表します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-237">Query views: these represent the transformation necessary to go from the database schema to the conceptual model.</span></span>
-   <span data-ttu-id="a15b4-238">更新ビュー: 概念モデルからデータベーススキーマに移動するために必要な変換を表します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-238">Update views: these represent the transformation necessary to go from the conceptual model to the database schema.</span></span>

<span data-ttu-id="a15b4-239">概念モデルは、さまざまな方法でデータベーススキーマとは異なる場合があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-239">Keep in mind that the conceptual model might differ from the database schema in various ways.</span></span> <span data-ttu-id="a15b4-240">たとえば、1つのテーブルを使用して、2つの異なるエンティティ型のデータを格納することができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-240">For example, one single table might be used to store the data for two different entity types.</span></span> <span data-ttu-id="a15b4-241">継承と非自明なマッピングでは、マッピングビューの複雑さが増します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-241">Inheritance and non-trivial mappings play a role in the complexity of the mapping views.</span></span>

<span data-ttu-id="a15b4-242">マッピングの仕様に基づいてこれらのビューを計算するプロセスは、ビュー生成と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-242">The process of computing these views based on the specification of the mapping is what we call view generation.</span></span> <span data-ttu-id="a15b4-243">ビューの生成は、モデルが読み込まれたとき、またはビルド時に "事前に生成されたビュー" を使用して動的に実行できます。後者は、Entity SQL ステートメントの形式で C\# または VB ファイルにシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-243">View generation can either take place dynamically when a model is loaded, or at build time, by using "pre-generated views"; the latter are serialized in the form of Entity SQL statements to a C\# or VB file.</span></span>

<span data-ttu-id="a15b4-244">ビューが生成されると、それらも検証されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-244">When views are generated, they are also validated.</span></span> <span data-ttu-id="a15b4-245">パフォーマンスの観点から見ると、ビュー生成のコストの大部分は、実際にはビューの検証であり、エンティティ間の接続が意味を持ち、サポートされているすべての操作に対して適切なカーディナリティがあることが確認されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-245">From a performance standpoint, the vast majority of the cost of view generation is actually the validation of the views which ensures that the connections between the entities make sense and have the correct cardinality for all the supported operations.</span></span>

<span data-ttu-id="a15b4-246">エンティティセットに対するクエリを実行すると、クエリが対応するクエリビューと結合され、この構成の結果がプランコンパイラを通じて実行され、バッキングストアが理解できるクエリの表現が作成されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-246">When a query over an entity set is executed, the query is combined with the corresponding query view, and the result of this composition is run through the plan compiler to create the representation of the query that the backing store can understand.</span></span> <span data-ttu-id="a15b4-247">SQL Server の場合、このコンパイルの最終的な結果は T-sql SELECT ステートメントになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-247">For SQL Server, the final result of this compilation will be a T-SQL SELECT statement.</span></span> <span data-ttu-id="a15b4-248">エンティティセットに対して最初に更新を実行すると、同様のプロセスによって更新ビューが実行され、ターゲットデータベースの DML ステートメントに変換されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-248">The first time an update over an entity set is performed, the update view is run through a similar process to transform it into DML statements for the target database.</span></span>

### <a name="22-factors-that-affect-view-generation-performance"></a><span data-ttu-id="a15b4-249">2.2 ビューの生成パフォーマンスに影響を与える要因</span><span class="sxs-lookup"><span data-stu-id="a15b4-249">2.2 Factors that affect View Generation performance</span></span>

<span data-ttu-id="a15b4-250">ビュー生成手順のパフォーマンスは、モデルのサイズだけでなく、モデルの相互接続方法にも依存します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-250">The performance of view generation step not only depends on the size of your model but also on how interconnected the model is.</span></span> <span data-ttu-id="a15b4-251">2つのエンティティが継承チェーンまたはアソシエーションを介して接続されている場合は、接続されていると言います。</span><span class="sxs-lookup"><span data-stu-id="a15b4-251">If two Entities are connected via an inheritance chain or an Association, they are said to be connected.</span></span> <span data-ttu-id="a15b4-252">同様に、2つのテーブルが外部キーを介して接続されている場合は、接続されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-252">Similarly if two tables are connected via a foreign key, they are connected.</span></span> <span data-ttu-id="a15b4-253">スキーマ内の接続されたエンティティとテーブルの数が増えるにつれて、ビューの生成コストが増加します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-253">As the number of connected Entities and tables in your schemas increase, the view generation cost increases.</span></span>

<span data-ttu-id="a15b4-254">ビューの生成と検証に使用するアルゴリズムは、最悪の場合には指数関数的ですが、これを向上させるためにいくつかの最適化を使用します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-254">The algorithm that we use to generate and validate views is exponential in the worst case, though we do use some optimizations to improve this.</span></span> <span data-ttu-id="a15b4-255">パフォーマンスに悪影響を及ぼす最大の要因は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-255">The biggest factors that seem to negatively affect performance are:</span></span>

-   <span data-ttu-id="a15b4-256">エンティティの数と、これらのエンティティ間のアソシエーションの量を参照するモデルサイズ。</span><span class="sxs-lookup"><span data-stu-id="a15b4-256">Model size, referring to the number of entities and the amount of associations between these entities.</span></span>
-   <span data-ttu-id="a15b4-257">モデルの複雑さ、特に多数の型が関係する継承。</span><span class="sxs-lookup"><span data-stu-id="a15b4-257">Model complexity, specifically inheritance involving a large number of types.</span></span>
-   <span data-ttu-id="a15b4-258">外部キーの関連付けではなく、独立した関連付けを使用します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-258">Using Independent Associations, instead of Foreign Key Associations.</span></span>

<span data-ttu-id="a15b4-259">小規模の単純なモデルでは、事前に生成されたビューを使用しないほどコストが小さくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-259">For small, simple models the cost may be small enough to not bother using pre-generated views.</span></span> <span data-ttu-id="a15b4-260">モデルのサイズと複雑さが増すにつれて、ビューの生成と検証のコストを削減するために使用できるオプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-260">As model size and complexity increase, there are several options available to reduce the cost of view generation and validation.</span></span>

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a><span data-ttu-id="a15b4-261">2.3 事前に生成されたビューを使用したモデルの読み込み時間の短縮</span><span class="sxs-lookup"><span data-stu-id="a15b4-261">2.3 Using Pre-Generated Views to decrease model load time</span></span>

<span data-ttu-id="a15b4-262">Entity Framework 6 で事前に生成されたビューを使用する方法の詳細については[、事前に生成されたマッピングビュー](~/ef6/fundamentals/performance/pre-generated-views.md)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-262">For detailed information on how to use pre-generated views on Entity Framework 6 visit [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md)</span></span>

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a><span data-ttu-id="a15b4-263">2.3.1 Entity Framework Power Tools Community エディションを使用して事前に生成されたビュー</span><span class="sxs-lookup"><span data-stu-id="a15b4-263">2.3.1 Pre-Generated views using the Entity Framework Power Tools Community Edition</span></span>

<span data-ttu-id="a15b4-264">[Entity Framework 6 の Power Tools Community エディション](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)を使用して、モデルクラスファイルを右クリックし、[Entity Framework] メニューを使用して [ビューの生成] を選択すると、EDMX モデルと Code First モデルのビューを生成できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-264">You can use the [Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) to generate views of EDMX and Code First models by right-clicking the model class file and using the Entity Framework menu to select “Generate Views”.</span></span> <span data-ttu-id="a15b4-265">Entity Framework Power Tools Community Edition は、DbContext で派生したコンテキストでのみ動作します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-265">The Entity Framework Power Tools Community Edition work only on DbContext-derived contexts.</span></span>

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a><span data-ttu-id="a15b4-266">2.3.2 Edmgen.exe によって作成されたモデルで事前生成されたビューを使用する方法</span><span class="sxs-lookup"><span data-stu-id="a15b4-266">2.3.2 How to use Pre-generated views with a model created by EDMGen</span></span>

<span data-ttu-id="a15b4-267">Edmgen.exe は、.NET に付属し、Entity Framework 4 および5で動作するユーティリティですが、Entity Framework 6 では動作しません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-267">EDMGen is a utility that ships with .NET and works with Entity Framework 4 and 5, but not with Entity Framework 6.</span></span> <span data-ttu-id="a15b4-268">Edmgen.exe を使用すると、モデルファイル、オブジェクトレイヤー、およびビューをコマンドラインから生成できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-268">EDMGen allows you to generate a model file, the object layer and the views from the command line.</span></span> <span data-ttu-id="a15b4-269">出力の1つは、選択した言語 (VB または C\#) のビューファイルになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-269">One of the outputs will be a Views file in your language of choice, VB or C\#.</span></span> <span data-ttu-id="a15b4-270">これは、各エンティティセットの Entity SQL スニペットを含むコードファイルです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-270">This is a code file containing Entity SQL snippets for each entity set.</span></span> <span data-ttu-id="a15b4-271">事前に生成されたビューを有効にするには、単にファイルをプロジェクトに含めます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-271">To enable pre-generated views, you simply include the file in your project.</span></span>

<span data-ttu-id="a15b4-272">モデルのスキーマファイルを手動で編集する場合は、ビューファイルを再生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-272">If you manually make edits to the schema files for the model, you will need to re-generate the views file.</span></span> <span data-ttu-id="a15b4-273">これを行うには、 **/mode: ViewGeneration**フラグを使用して edmgen.exe を実行します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-273">You can do this by running EDMGen with the **/mode:ViewGeneration** flag.</span></span>

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a><span data-ttu-id="a15b4-274">2.3.3 EDMX ファイルで事前に生成されたビューを使用する方法</span><span class="sxs-lookup"><span data-stu-id="a15b4-274">2.3.3 How to use Pre-Generated Views with an EDMX file</span></span>

<span data-ttu-id="a15b4-275">また、Edmgen.exe を使用して EDMX ファイルのビューを生成することもできます。前に参照した MSDN トピックでは、これを行うためのビルド前イベントを追加する方法について説明していますが、これは複雑であり、不可能な場合もあります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-275">You can also use EDMGen to generate views for an EDMX file - the previously referenced MSDN topic describes how to add a pre-build event to do this - but this is complicated and there are some cases where it isn't possible.</span></span> <span data-ttu-id="a15b4-276">一般に、モデルが edmx ファイル内にある場合は、T4 テンプレートを使用してビューを生成する方が簡単です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-276">It's generally easier to use a T4 template to generate the views when your model is in an edmx file.</span></span>

<span data-ttu-id="a15b4-277">ADO.NET チームのブログには、ビューの生成に T4 テンプレートを使用する方法を説明する投稿があります (\<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>)。</span><span class="sxs-lookup"><span data-stu-id="a15b4-277">The ADO.NET team blog has a post that describes how to use a T4 template for view generation ( \<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>).</span></span> <span data-ttu-id="a15b4-278">この投稿には、ダウンロードしてプロジェクトに追加できるテンプレートが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-278">This post includes a template that can be downloaded and added to your project.</span></span> <span data-ttu-id="a15b4-279">テンプレートは最初のバージョンの Entity Framework 用に記述されているので、最新バージョンの Entity Framework で動作することは保証されていません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-279">The template was written for the first version of Entity Framework, so they aren’t guaranteed to work with the latest versions of Entity Framework.</span></span> <span data-ttu-id="a15b4-280">ただし、Visual Studio ギャラリーから Entity Framework 4 と5from ビュー生成テンプレートの最新のセットをダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-280">However, you can download a more up-to-date set of view generation templates for Entity Framework 4 and 5from the Visual Studio Gallery:</span></span>

-   <span data-ttu-id="a15b4-281">VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span><span class="sxs-lookup"><span data-stu-id="a15b4-281">VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span></span>
-   <span data-ttu-id="a15b4-282">C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span><span class="sxs-lookup"><span data-stu-id="a15b4-282">C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span></span>

<span data-ttu-id="a15b4-283">Entity Framework 6 を使用している場合は、\<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>で Visual Studio ギャラリーからビュー生成 T4 テンプレートを取得できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-283">If you’re using Entity Framework 6 you can get the view generation T4 templates from the Visual Studio Gallery at \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>.</span></span>

### <a name="24-reducing-the-cost-of-view-generation"></a><span data-ttu-id="a15b4-284">2.4 ビューの生成コストの削減</span><span class="sxs-lookup"><span data-stu-id="a15b4-284">2.4 Reducing the cost of view generation</span></span>

<span data-ttu-id="a15b4-285">事前に生成されたビューを使用すると、モデルの読み込み (実行時) からデザイン時までのビューの生成コストが変化します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-285">Using pre-generated views moves the cost of view generation from model loading (run time) to design time.</span></span> <span data-ttu-id="a15b4-286">これにより、実行時の起動時のパフォーマンスが向上しますが、開発中はビューの生成が困難になります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-286">While this improves startup performance at runtime, you will still experience the pain of view generation while you are developing.</span></span> <span data-ttu-id="a15b4-287">コンパイル時と実行時の両方で、ビュー生成のコストを削減するために役立ついくつかの追加のテクニックがあります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-287">There are several additional tricks that can help reduce the cost of view generation, both at compile time and run time.</span></span>

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a><span data-ttu-id="a15b4-288">2.4.1 外部キーの関連付けを使用してビューの生成コストを削減する</span><span class="sxs-lookup"><span data-stu-id="a15b4-288">2.4.1 Using Foreign Key Associations to reduce view generation cost</span></span>

<span data-ttu-id="a15b4-289">モデル内のアソシエーションを依存関係から外部キーへの関連付けに切り替えると、ビューの生成にかかる時間が大幅に短縮されました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-289">We have seen a number of cases where switching the associations in the model from Independent Associations to Foreign Key Associations dramatically improved the time spent in view generation.</span></span>

<span data-ttu-id="a15b4-290">この改善点を示すために、Edmgen.exe を使用して2つのバージョンの Navision モデルを生成しました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-290">To demonstrate this improvement, we generated two versions of the Navision model by using EDMGen.</span></span> <span data-ttu-id="a15b4-291">*注: Navision モデルの説明については、「付録 C」を参照してください。*</span><span class="sxs-lookup"><span data-stu-id="a15b4-291">*Note: see appendix C for a description of the Navision model.*</span></span> <span data-ttu-id="a15b4-292">Navision モデルは、非常に大量のエンティティとリレーションシップがあるため、この演習では興味深いものです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-292">The Navision model is interesting for this exercise due to its very large amount of entities and relationships between them.</span></span>

<span data-ttu-id="a15b4-293">この非常に大きなモデルの1つのバージョンが、外部キーの関連付けを使用して生成され、もう一方は独立したアソシエーションを使用して生成されました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-293">One version of this very large model was generated with Foreign Keys Associations and the other was generated with Independent Associations.</span></span> <span data-ttu-id="a15b4-294">次に、各モデルのビューの生成に要した時間を計測しました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-294">We then timed how long it took to generate the views for each model.</span></span> <span data-ttu-id="a15b4-295">Entity Framework 5 テストでは、クラス EntityViewGenerator から GenerateViews () メソッドを使用してビューを生成していましたが、Entity Framework 6 テストでは StorageMappingItemCollection クラスの GenerateViews () メソッドが使用されていました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-295">Entity Framework 5 test used the GenerateViews() method from class EntityViewGenerator to generate the views, while the Entity Framework 6 test used the GenerateViews() method from class StorageMappingItemCollection.</span></span> <span data-ttu-id="a15b4-296">これは、Entity Framework 6 コードベースで発生したコードの再構築に起因します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-296">This due to code restructuring that occurred in the Entity Framework 6 codebase.</span></span>

<span data-ttu-id="a15b4-297">Entity Framework 5 を使用すると、外部キーを使用したモデルのビュー生成には、ラボコンピューターで65分かかりました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-297">Using Entity Framework 5, view generation for the model with Foreign Keys took 65 minutes in a lab machine.</span></span> <span data-ttu-id="a15b4-298">独立した関連付けを使用したモデルのビューを生成するのにかかる時間は不明です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-298">It's unknown how long it would have taken to generate the views for the model that used independent associations.</span></span> <span data-ttu-id="a15b4-299">毎月の更新プログラムをインストールするために、ラボでコンピューターを再起動する前に、1か月以上テストを実行しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-299">We left the test running for over a month before the machine was rebooted in our lab to install monthly updates.</span></span>

<span data-ttu-id="a15b4-300">Entity Framework 6 を使用すると、外部キーを使用したモデルのビュー生成は、同じラボコンピューターで28秒かかりました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-300">Using Entity Framework 6, view generation for the model with Foreign Keys took 28 seconds in the same lab machine.</span></span> <span data-ttu-id="a15b4-301">独立アソシエーションを使用するモデルのビュー生成には、58秒かかりました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-301">View generation for the model that uses Independent Associations took 58 seconds.</span></span> <span data-ttu-id="a15b4-302">ビュー生成コードで Entity Framework 6 に対して行われた機能強化により、多くのプロジェクトでは、起動時間を短縮するために事前に生成されたビューは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-302">The improvements done to Entity Framework 6 on its view generation code mean that many projects won’t need pre-generated views to obtain faster startup times.</span></span>

<span data-ttu-id="a15b4-303">Entity Framework 4 および5の事前生成ビューは、Edmgen.exe または Entity Framework パワーツールで実行できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-303">It’s important to remark that pre-generating views in Entity Framework 4 and 5 can be done with EDMGen or the Entity Framework Power Tools.</span></span> <span data-ttu-id="a15b4-304">Entity Framework 6 ビューの生成は、Entity Framework パワーツールを使用するか、[事前に生成されたマッピングビュー](~/ef6/fundamentals/performance/pre-generated-views.md)で説明されているようにプログラムで行うことができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-304">For Entity Framework 6 view generation can be done via the Entity Framework Power Tools or programmatically as described in [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md).</span></span>

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a><span data-ttu-id="a15b4-305">2.4.1.1 独立した関連付けの代わりに外部キーを使用する方法</span><span class="sxs-lookup"><span data-stu-id="a15b4-305">2.4.1.1 How to use Foreign Keys instead of Independent Associations</span></span>

<span data-ttu-id="a15b4-306">Edmgen.exe または Visual Studio で Entity Designer を使用する場合、既定では FKs が使用され、FKs と IAs を切り替えるためのチェックボックスまたはコマンドラインフラグは1つだけになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-306">When using EDMGen or the Entity Designer in Visual Studio, you get FKs by default, and it only takes a single checkbox or command line flag to switch between FKs and IAs.</span></span>

<span data-ttu-id="a15b4-307">大きな Code First モデルを使用している場合は、独立した関連付けを使用すると、ビューの生成に対して同じ効果が得られます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-307">If you have a large Code First model, using Independent Associations will have the same effect on view generation.</span></span> <span data-ttu-id="a15b4-308">依存オブジェクトのクラスに外部キープロパティを含めることで、この影響を回避できます。ただし、開発者によっては、オブジェクトモデルが汚染されることを考慮しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-308">You can avoid this impact by including Foreign Key properties on the classes for your dependent objects, though some developers will consider this to be polluting their object model.</span></span> <span data-ttu-id="a15b4-309">この件名の詳細については \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-309">You can find more information on this subject in \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>.</span></span>

| <span data-ttu-id="a15b4-310">使用する場合</span><span class="sxs-lookup"><span data-stu-id="a15b4-310">When using</span></span>      | <span data-ttu-id="a15b4-311">方法</span><span class="sxs-lookup"><span data-stu-id="a15b4-311">Do this</span></span>                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a15b4-312">エンティティ デザイナー</span><span class="sxs-lookup"><span data-stu-id="a15b4-312">Entity Designer</span></span> | <span data-ttu-id="a15b4-313">2つのエンティティ間の関連付けを追加した後、参照に関する制約があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-313">After adding an association between two entities, make sure you have a referential constraint.</span></span> <span data-ttu-id="a15b4-314">参照制約は、独立した関連付けではなく外部キーを使用するように Entity Framework に指示します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-314">Referential constraints tell Entity Framework to use Foreign Keys instead of Independent Associations.</span></span> <span data-ttu-id="a15b4-315">詳細については、\<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-315">For additional details visit \<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>.</span></span> |
| <span data-ttu-id="a15b4-316">Edmgen.exe</span><span class="sxs-lookup"><span data-stu-id="a15b4-316">EDMGen</span></span>          | <span data-ttu-id="a15b4-317">Edmgen.exe を使用してデータベースからファイルを生成する場合、外部キーが尊重され、そのようなモデルに追加されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-317">When using EDMGen to generate your files from the database, your Foreign Keys will be respected and added to the model as such.</span></span> <span data-ttu-id="a15b4-318">Edmgen.exe によって公開されるさまざまなオプションの詳細については[http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-318">For more information on the different options exposed by EDMGen visit [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx).</span></span>                           |
| <span data-ttu-id="a15b4-319">Code First</span><span class="sxs-lookup"><span data-stu-id="a15b4-319">Code First</span></span>      | <span data-ttu-id="a15b4-320">Code First を使用する場合に依存オブジェクトの外部キープロパティを含める方法については、 [Code First 規則](~/ef6/modeling/code-first/conventions/built-in.md)のトピックの「リレーションシップ規則」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-320">See the "Relationship Convention" section of the [Code First Conventions](~/ef6/modeling/code-first/conventions/built-in.md) topic for information on how to include foreign key properties on dependent objects when using Code First.</span></span>                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a><span data-ttu-id="a15b4-321">2.4.2 モデルを別のアセンブリに移動する</span><span class="sxs-lookup"><span data-stu-id="a15b4-321">2.4.2 Moving your model to a separate assembly</span></span>

<span data-ttu-id="a15b4-322">モデルがアプリケーションのプロジェクトに直接含まれており、ビルド前のイベントまたは T4 テンプレートを使用してビューを生成すると、モデルが変更されていなくても、プロジェクトが再構築されるたびに、ビューの生成と検証が行われます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-322">When your model is included directly in your application's project and you generate views through a pre-build event or a T4 template, view generation and validation will take place whenever the project is rebuilt, even if the model wasn't changed.</span></span> <span data-ttu-id="a15b4-323">モデルを別のアセンブリに移動し、アプリケーションのプロジェクトから参照する場合は、モデルを含むプロジェクトをリビルドする必要なく、アプリケーションに他の変更を加えることができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-323">If you move the model to a separate assembly and reference it from your application's project, you can make other changes to your application without needing to rebuild the project containing the model.</span></span>

<span data-ttu-id="a15b4-324">*注:* モデルを別のアセンブリに移動する場合は、モデルの接続文字列をクライアントプロジェクトのアプリケーション構成ファイルにコピーすることを忘れないでください  。</span><span class="sxs-lookup"><span data-stu-id="a15b4-324">*Note:*  when moving your model to separate assemblies remember to copy the connection strings for the model into the application configuration file of the client project.</span></span>

#### <a name="243-disable-validation-of-an-edmx-based-model"></a><span data-ttu-id="a15b4-325">2.4.3 edmx ベースのモデルの検証を無効にする</span><span class="sxs-lookup"><span data-stu-id="a15b4-325">2.4.3 Disable validation of an edmx-based model</span></span>

<span data-ttu-id="a15b4-326">EDMX モデルは、モデルが変更されていない場合でも、コンパイル時に検証されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-326">EDMX models are validated at compile time, even if the model is unchanged.</span></span> <span data-ttu-id="a15b4-327">モデルが既に検証済みの場合は、[プロパティ] ウィンドウで "ビルド時に検証" プロパティを false に設定することにより、コンパイル時に検証を抑制できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-327">If your model has already been validated, you can suppress validation at compile time by setting the "Validate on Build" property to false in the properties window.</span></span> <span data-ttu-id="a15b4-328">マッピングまたはモデルを変更すると、変更を確認するための検証を一時的に再び有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-328">When you change your mapping or model, you can temporarily re-enable validation to verify your changes.</span></span>

<span data-ttu-id="a15b4-329">Entity Framework 6 の Entity Framework Designer に対してパフォーマンスが向上しました。また、"ビルド時の検証" のコストは、以前のバージョンのデザイナーよりもはるかに少なくなっています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-329">Note that performance improvements were made to the Entity Framework Designer for Entity Framework 6, and the cost of the “Validate on Build” is much lower than in previous versions of the designer.</span></span>

## <a name="3-caching-in-the-entity-framework"></a><span data-ttu-id="a15b4-330">Entity Framework での3キャッシュ</span><span class="sxs-lookup"><span data-stu-id="a15b4-330">3 Caching in the Entity Framework</span></span>

<span data-ttu-id="a15b4-331">Entity Framework には、次の形式のキャッシュが組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-331">Entity Framework has the following forms of caching built-in:</span></span>

1.  <span data-ttu-id="a15b4-332">オブジェクトキャッシュ: ObjectContext インスタンスに組み込まれている ObjectStateManager は、そのインスタンスを使用して取得されたオブジェクトのメモリを追跡します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-332">Object caching – the ObjectStateManager built into an ObjectContext instance keeps track in memory of the objects that have been retrieved using that instance.</span></span> <span data-ttu-id="a15b4-333">これは、第1レベルのキャッシュとも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-333">This is also known as first-level cache.</span></span>
2.  <span data-ttu-id="a15b4-334">クエリプランのキャッシュ-クエリが複数回実行された場合に、生成されたストアコマンドを再利用します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-334">Query Plan Caching - reusing the generated store command when a query is executed more than once.</span></span>
3.  <span data-ttu-id="a15b4-335">メタデータのキャッシュ-モデルのメタデータを、同じモデルへの異なる接続間で共有します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-335">Metadata caching - sharing the metadata for a model across different connections to the same model.</span></span>

<span data-ttu-id="a15b4-336">EF が既定で提供するキャッシュに加えて、ラッピングプロバイダーと呼ばれる特殊な種類の ADO.NET データプロバイダーを使用して、データベースから取得した結果のキャッシュを使用して Entity Framework を拡張することもできます。これは、第2レベルのキャッシュとも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-336">Besides the caches that EF provides out of the box, a special kind of ADO.NET data provider known as a wrapping provider can also be used to extend Entity Framework with a cache for the results retrieved from the database, also known as second-level caching.</span></span>

### <a name="31-object-caching"></a><span data-ttu-id="a15b4-337">3.1 オブジェクトキャッシュ</span><span class="sxs-lookup"><span data-stu-id="a15b4-337">3.1 Object Caching</span></span>

<span data-ttu-id="a15b4-338">既定では、EF によって具体化される直前に、クエリの結果でエンティティが返されるときに、ObjectContext は同じキーを持つエンティティが既に ObjectStateManager に読み込まれているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-338">By default when an entity is returned in the results of a query, just before EF materializes it, the ObjectContext will check if an entity with the same key has already been loaded into its ObjectStateManager.</span></span> <span data-ttu-id="a15b4-339">同じキーを持つエンティティが既に存在する場合は、EF によってクエリの結果に含まれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-339">If an entity with the same keys is already present EF will include it in the results of the query.</span></span> <span data-ttu-id="a15b4-340">EF は引き続きデータベースに対してクエリを発行しますが、この動作は、エンティティを複数回具体化するコストの多くを回避できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-340">Although EF will still issue the query against the database, this behavior can bypass much of the cost of materializing the entity multiple times.</span></span>

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a><span data-ttu-id="a15b4-341">3.1.1 DbContext 検索を使用してオブジェクトキャッシュからエンティティを取得する</span><span class="sxs-lookup"><span data-stu-id="a15b4-341">3.1.1 Getting entities from the object cache using DbContext Find</span></span>

<span data-ttu-id="a15b4-342">通常のクエリとは異なり、DbSet の Find メソッド (EF 4.1 で初めて使用される Api) は、データベースに対してクエリを発行する前に、メモリ内で検索を実行します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-342">Unlike a regular query, the Find method in DbSet (APIs included for the first time in EF 4.1) will perform a search in memory before even issuing the query against the database.</span></span> <span data-ttu-id="a15b4-343">2つの異なる ObjectContext インスタンスは2つの異なる ObjectStateManager インスタンスを持つことに注意してください。つまり、個別のオブジェクトキャッシュを持つことになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-343">It’s important to note that two different ObjectContext instances will have two different ObjectStateManager instances, meaning that they have separate object caches.</span></span>

<span data-ttu-id="a15b4-344">Find は、primary key 値を使用して、コンテキストによって追跡されるエンティティを検索します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-344">Find uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="a15b4-345">エンティティがコンテキスト内にない場合は、クエリが実行され、データベースに対して評価されます。また、エンティティがコンテキストまたはデータベースに見つからない場合は null が返されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-345">If the entity is not in the context then a query will be executed and evaluated against the database, and null is returned if the entity is not found in the context or in the database.</span></span> <span data-ttu-id="a15b4-346">また、Find では、コンテキストに追加されていても、まだデータベースに保存されていないエンティティも返されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-346">Note that Find also returns entities that have been added to the context but have not yet been saved to the database.</span></span>

<span data-ttu-id="a15b4-347">Find を使用する場合は、パフォーマンスに関する考慮事項があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-347">There is a performance consideration to be taken when using Find.</span></span> <span data-ttu-id="a15b4-348">既定でこのメソッドを呼び出すと、データベースへのコミットが保留中の変更を検出するために、オブジェクトキャッシュの検証がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-348">Invocations to this method by default will trigger a validation of the object cache in order to detect changes that are still pending commit to the database.</span></span> <span data-ttu-id="a15b4-349">オブジェクトキャッシュ内に非常に多くのオブジェクトがある場合、またはオブジェクトキャッシュに追加されている大きなオブジェクトグラフにオブジェクトが含まれている場合、このプロセスは非常にコストが高くなる可能性がありますが、無効にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-349">This process can be very expensive if there are a very large number of objects in the object cache or in a large object graph being added to the object cache, but it can also be disabled.</span></span> <span data-ttu-id="a15b4-350">場合によっては、自動的に検出された変更を無効にしたときに、Find メソッドを呼び出すことによって、大きな違いを感じることがあります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-350">In certain cases, you may perceive over an order of magnitude of difference in calling the Find method when you disable auto detect changes.</span></span> <span data-ttu-id="a15b4-351">ただし、2番目の次数の次数は、オブジェクトが実際にキャッシュ内にある場合と、オブジェクトをデータベースから取得する必要がある場合に認識されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-351">Yet a second order of magnitude is perceived when the object actually is in the cache versus when the object has to be retrieved from the database.</span></span> <span data-ttu-id="a15b4-352">次に示すのは、マイクロベンチマークのいくつかを使用して取得した測定値が、5000エンティティの読み込みでミリ秒単位で示されたグラフの例です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-352">Here is an example graph with measurements taken using some of our microbenchmarks, expressed in milliseconds, with a load of 5000 entities:</span></span>

<span data-ttu-id="a15b4-353">![.NET 4.5 対数スケール](~/ef6/media/net45logscale.png ".NET 4.5-対数スケール")</span><span class="sxs-lookup"><span data-stu-id="a15b4-353">![.NET 4.5 logarithmic scale](~/ef6/media/net45logscale.png ".NET 4.5 - logarithmic scale")</span></span>

<span data-ttu-id="a15b4-354">自動検出の変更が無効になっている Find の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-354">Example of Find with auto-detect changes disabled:</span></span>

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

<span data-ttu-id="a15b4-355">Find メソッドを使用する場合は、次の点を考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-355">What you have to consider when using the Find method is:</span></span>

1.  <span data-ttu-id="a15b4-356">オブジェクトがキャッシュにない場合は、Find の利点は否定されますが、構文はキーによるクエリよりも単純です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-356">If the object is not in the cache the benefits of Find are negated, but the syntax is still simpler than a query by key.</span></span>
2.  <span data-ttu-id="a15b4-357">[変更を自動的に検出する] が有効になっている場合、Find メソッドのコストは、モデルの複雑さとオブジェクトキャッシュ内のエンティティの量に応じて、1つの大きさになるか、さらに多くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-357">If auto detect changes is enabled the cost of the Find method may increase by one order of magnitude, or even more depending on the complexity of your model and the amount of entities in your object cache.</span></span>

<span data-ttu-id="a15b4-358">また、Find は検索するエンティティだけを返し、関連付けられているエンティティがまだオブジェクトキャッシュにない場合は、そのエンティティが自動的に読み込まれないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-358">Also, keep in mind that Find only returns the entity you are looking for and it does not automatically loads its associated entities if they are not already in the object cache.</span></span> <span data-ttu-id="a15b4-359">関連付けられたエンティティを取得する必要がある場合は、一括読み込みでキーによるクエリを使用できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-359">If you need to retrieve associated entities, you can use a query by key with eager loading.</span></span> <span data-ttu-id="a15b4-360">詳細については、「 **8.1 の遅延読み込みと一括読み込み**」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-360">For more information see **8.1 Lazy Loading vs. Eager Loading**.</span></span>

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a><span data-ttu-id="a15b4-361">オブジェクトキャッシュに多数のエンティティがある場合のパフォーマンスの問題の3.1.2</span><span class="sxs-lookup"><span data-stu-id="a15b4-361">3.1.2 Performance issues when the object cache has many entities</span></span>

<span data-ttu-id="a15b4-362">オブジェクトキャッシュを使用すると、Entity Framework の全体的な応答性を向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-362">The object cache helps to increase the overall responsiveness of Entity Framework.</span></span> <span data-ttu-id="a15b4-363">ただし、オブジェクトキャッシュに大量のエンティティが読み込まれている場合、Add、Remove、Find、Entry、SaveChanges などの特定の操作に影響を与える可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-363">However, when the object cache has a very large amount of entities loaded it may affect certain operations such as Add, Remove, Find, Entry, SaveChanges and more.</span></span> <span data-ttu-id="a15b4-364">特に、検出された変更の呼び出しをトリガーする操作は、非常に大きなオブジェクトキャッシュによって悪影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-364">In particular, operations that trigger a call to DetectChanges will be negatively affected by very large object caches.</span></span> <span data-ttu-id="a15b4-365">[検出された変更] オブジェクトグラフをオブジェクト状態マネージャーと同期し、そのパフォーマンスがオブジェクトグラフのサイズによって直接決定されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-365">DetectChanges synchronizes the object graph with the object state manager and its performance will determined directly by the size of the object graph.</span></span> <span data-ttu-id="a15b4-366">検出の変更の詳細については、「 [POCO エンティティの変更の追跡](https://msdn.microsoft.com/library/dd456848.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-366">For more information about DetectChanges, see [Tracking Changes in POCO Entities](https://msdn.microsoft.com/library/dd456848.aspx).</span></span>

<span data-ttu-id="a15b4-367">Entity Framework 6 を使用する場合、開発者は、コレクションに対して反復処理を実行し、インスタンスごとに Add を呼び出すのではなく、DbSet に対して直接 AddRange と RemoveRange 設定を呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-367">When using Entity Framework 6, developers are able to call AddRange and RemoveRange directly on a DbSet, instead of iterating on a collection and calling Add once per instance.</span></span> <span data-ttu-id="a15b4-368">範囲のメソッドを使用する利点は、追加されたエンティティごとに1回ではなく、エンティティのセット全体に対して、検出された変更のコストが1回だけ支払われることです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-368">The advantage of using the range methods is that the cost of DetectChanges is only paid once for the entire set of entities as opposed to once per each added entity.</span></span>

### <a name="32-query-plan-caching"></a><span data-ttu-id="a15b4-369">3.2 クエリプランのキャッシュ</span><span class="sxs-lookup"><span data-stu-id="a15b4-369">3.2 Query Plan Caching</span></span>

<span data-ttu-id="a15b4-370">クエリが最初に実行されると、内部プランコンパイラによって、概念クエリがストアコマンドに変換されます (たとえば、SQL Server に対して実行される T-sql)。</span><span class="sxs-lookup"><span data-stu-id="a15b4-370">The first time a query is executed, it goes through the internal plan compiler to translate the conceptual query into the store command (for example, the T-SQL which is executed when run against SQL Server).</span></span><span data-ttu-id="a15b4-371">  クエリプランのキャッシュが有効になっている場合、次にクエリが実行されるときに、ストアコマンドが実行のためにクエリプランキャッシュから直接取得され、プランコンパイラはバイパスされます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-371">  If query plan caching is enabled, the next time the query is executed the store command is retrieved directly from the query plan cache for execution, bypassing the plan compiler.</span></span>

<span data-ttu-id="a15b4-372">クエリプランキャッシュは、同じ AppDomain 内の ObjectContext インスタンス間で共有されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-372">The query plan cache is shared across ObjectContext instances within the same AppDomain.</span></span> <span data-ttu-id="a15b4-373">クエリプランのキャッシュを活用するために、ObjectContext インスタンスを保持する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-373">You don't need to hold onto an ObjectContext instance to benefit from query plan caching.</span></span>

#### <a name="321-some-notes-about-query-plan-caching"></a><span data-ttu-id="a15b4-374">3.2.1 クエリプランのキャッシュに関する注意事項</span><span class="sxs-lookup"><span data-stu-id="a15b4-374">3.2.1 Some notes about Query Plan Caching</span></span>

-   <span data-ttu-id="a15b4-375">クエリプランキャッシュは、すべての種類のクエリ (Entity SQL、LINQ to Entities、CompiledQuery オブジェクト) で共有されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-375">The query plan cache is shared for all query types: Entity SQL, LINQ to Entities, and CompiledQuery objects.</span></span>
-   <span data-ttu-id="a15b4-376">既定では、EntityCommand または ObjectQuery のどちらを使用して実行されたかにかかわらず、Entity SQL クエリに対してクエリプランのキャッシュが有効になっています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-376">By default, query plan caching is enabled for Entity SQL queries, whether executed through an EntityCommand or through an ObjectQuery.</span></span> <span data-ttu-id="a15b4-377">また、.NET 4.5 の Entity Framework の LINQ to Entities クエリと Entity Framework 6 では、既定で有効になっています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-377">It is also enabled by default for LINQ to Entities queries in Entity Framework on .NET 4.5, and in Entity Framework 6</span></span>
    -   <span data-ttu-id="a15b4-378">クエリプランのキャッシュを無効にするには、EnablePlanCaching プロパティ (EntityCommand または ObjectQuery) を false に設定します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-378">Query plan caching can be disabled by setting the EnablePlanCaching property (on EntityCommand or ObjectQuery) to false.</span></span> <span data-ttu-id="a15b4-379">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-379">For example:</span></span>
``` csharp
                    var query = from customer in context.Customer
                                where customer.CustomerId == id
                                select new
                                {
                                    customer.CustomerId,
                                    customer.Name
                                };
                    ObjectQuery oQuery = query as ObjectQuery;
                    oQuery.EnablePlanCaching = false;
```
-   <span data-ttu-id="a15b4-380">パラメーター化クエリの場合、パラメーターの値を変更しても、キャッシュされたクエリにヒットします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-380">For parameterized queries, changing the parameter's value will still hit the cached query.</span></span> <span data-ttu-id="a15b4-381">ただし、パラメーターのファセット (サイズ、有効桁数、小数点以下桁数など) を変更すると、キャッシュ内の別のエントリがヒットします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-381">But changing a parameter's facets (for example, size, precision, or scale) will hit a different entry in the cache.</span></span>
-   <span data-ttu-id="a15b4-382">Entity SQL を使用する場合、クエリ文字列はキーの一部になります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-382">When using Entity SQL, the query string is part of the key.</span></span> <span data-ttu-id="a15b4-383">クエリを変更しても、クエリが機能的に同等である場合でも、キャッシュエントリが異なることになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-383">Changing the query at all will result in different cache entries, even if the queries are functionally equivalent.</span></span> <span data-ttu-id="a15b4-384">これには、大文字と小文字の違いや空白の変更も含まれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-384">This includes changes to casing or whitespace.</span></span>
-   <span data-ttu-id="a15b4-385">LINQ を使用する場合、クエリはキーの一部を生成するために処理されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-385">When using LINQ, the query is processed to generate a part of the key.</span></span> <span data-ttu-id="a15b4-386">したがって、LINQ 式を変更すると、別のキーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-386">Changing the LINQ expression will therefore generate a different key.</span></span>
-   <span data-ttu-id="a15b4-387">その他の技術的な制限が適用される場合があります。詳細については、「Autocompiled されたクエリ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-387">Other technical limitations may apply; see Autocompiled Queries for more details.</span></span>

#### <a name="322-cache-eviction-algorithm"></a><span data-ttu-id="a15b4-388">3.2.2 キャッシュ削除アルゴリズム</span><span class="sxs-lookup"><span data-stu-id="a15b4-388">3.2.2      Cache eviction algorithm</span></span>

<span data-ttu-id="a15b4-389">内部アルゴリズムがどのように機能するかを理解することは、クエリプランのキャッシュを有効または無効にするタイミングを判断するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-389">Understanding how the internal algorithm works will help you figure out when to enable or disable query plan caching.</span></span> <span data-ttu-id="a15b4-390">クリーンアップアルゴリズムは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-390">The cleanup algorithm is as follows:</span></span>

1.  <span data-ttu-id="a15b4-391">キャッシュに一連のエントリ (800) が格納されたら、定期的に (分単位) キャッシュをスイープするタイマーを開始します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-391">Once the cache contains a set number of entries (800), we start a timer that periodically (once-per-minute) sweeps the cache.</span></span>
2.  <span data-ttu-id="a15b4-392">キャッシュスイープ中、エントリは LFRU (最も頻繁に使用されていない) のキャッシュから削除されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-392">During cache sweeps, entries are removed from the cache on a LFRU (Least frequently – recently used) basis.</span></span> <span data-ttu-id="a15b4-393">このアルゴリズムは、どのエントリを取り出すかを決定するときに、ヒットカウントと有効期間の両方を考慮します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-393">This algorithm takes both hit count and age into account when deciding which entries are ejected.</span></span>
3.  <span data-ttu-id="a15b4-394">キャッシュスイープが終了するたびに、キャッシュに800エントリが再度含まれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-394">At the end of each cache sweep, the cache again contains 800 entries.</span></span>

<span data-ttu-id="a15b4-395">すべてのキャッシュエントリは、削除するエントリを決定するときに、同じように処理されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-395">All cache entries are treated equally when determining which entries to evict.</span></span> <span data-ttu-id="a15b4-396">これは、CompiledQuery のストアコマンドが、Entity SQL クエリのストアコマンドと同じように削除される可能性があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-396">This means the store command for a CompiledQuery has the same chance of eviction as the store command for an Entity SQL query.</span></span>

<span data-ttu-id="a15b4-397">キャッシュの削除タイマーは、キャッシュ内に800エンティティがあるときに開始されますが、キャッシュはこのタイマーが開始されてから60秒しかスイープされないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-397">Note that the cache eviction timer is kicked in when there are 800 entities in the cache, but the cache is only swept 60 seconds after this timer is started.</span></span> <span data-ttu-id="a15b4-398">つまり、最大60秒の場合、キャッシュのサイズが非常に大きくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-398">That means that for up to 60 seconds your cache may grow to be quite large.</span></span>

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a><span data-ttu-id="a15b4-399">クエリプランのキャッシュパフォーマンスを示す3.2.3 テストメトリック</span><span class="sxs-lookup"><span data-stu-id="a15b4-399">3.2.3       Test Metrics demonstrating query plan caching performance</span></span>

<span data-ttu-id="a15b4-400">アプリケーションのパフォーマンスに対するクエリプランのキャッシュの効果を示すために、Navision モデルに対して多数の Entity SQL クエリを実行したテストを実行しました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-400">To demonstrate the effect of query plan caching on your application's performance, we performed a test where we executed a number of Entity SQL queries against the Navision model.</span></span> <span data-ttu-id="a15b4-401">Navision モデルの説明と実行されたクエリの種類については、「付録」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-401">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="a15b4-402">このテストでは、まずクエリの一覧を反復処理し、各クエリを1回ずつ実行してキャッシュに追加します (キャッシュが有効になっている場合)。</span><span class="sxs-lookup"><span data-stu-id="a15b4-402">In this test, we first iterate through the list of queries and execute each one once to add them to the cache (if caching is enabled).</span></span> <span data-ttu-id="a15b4-403">このステップは、時間がありません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-403">This step is untimed.</span></span> <span data-ttu-id="a15b4-404">次に、メインスレッドを60秒以上スリープ状態にして、キャッシュスイープを実行できるようにします。最後に、キャッシュされたクエリを実行するために、2回目にリストを反復処理します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-404">Next, we sleep the main thread for over 60 seconds to allow cache sweeping to take place; finally, we iterate through the list a 2nd time to execute the cached queries.</span></span> <span data-ttu-id="a15b4-405">さらに、SQL Server プランのキャッシュは、クエリの各セットが実行される前にフラッシュされます。これにより、クエリプランのキャッシュによって得られる利点が正確に反映されるようになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-405">Additionally, the SQL Server plan cache is flushed before each set of queries is executed so that the times we obtain accurately reflect the benefit given by the query plan cache.</span></span>

##### <a name="3231-test-results"></a><span data-ttu-id="a15b4-406">3.2.3.1 テスト結果</span><span class="sxs-lookup"><span data-stu-id="a15b4-406">3.2.3.1       Test Results</span></span>

| <span data-ttu-id="a15b4-407">テスト</span><span class="sxs-lookup"><span data-stu-id="a15b4-407">Test</span></span>                                                                   | <span data-ttu-id="a15b4-408">キャッシュなし EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-408">EF5 no cache</span></span> | <span data-ttu-id="a15b4-409">キャッシュされた EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-409">EF5 cached</span></span> | <span data-ttu-id="a15b4-410">キャッシュなし EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-410">EF6 no cache</span></span> | <span data-ttu-id="a15b4-411">キャッシュされた EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-411">EF6 cached</span></span> |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| <span data-ttu-id="a15b4-412">すべての18723クエリの列挙</span><span class="sxs-lookup"><span data-stu-id="a15b4-412">Enumerating all 18723 queries</span></span>                                          | <span data-ttu-id="a15b4-413">124</span><span class="sxs-lookup"><span data-stu-id="a15b4-413">124</span></span>          | <span data-ttu-id="a15b4-414">125.4</span><span class="sxs-lookup"><span data-stu-id="a15b4-414">125.4</span></span>      | <span data-ttu-id="a15b4-415">124.3</span><span class="sxs-lookup"><span data-stu-id="a15b4-415">124.3</span></span>        | <span data-ttu-id="a15b4-416">125.3</span><span class="sxs-lookup"><span data-stu-id="a15b4-416">125.3</span></span>      |
| <span data-ttu-id="a15b4-417">スイープの回避 (複雑さに関係なく最初の800のクエリのみ)</span><span class="sxs-lookup"><span data-stu-id="a15b4-417">Avoiding sweep (just the first 800 queries, regardless of complexity)</span></span>  | <span data-ttu-id="a15b4-418">41.7</span><span class="sxs-lookup"><span data-stu-id="a15b4-418">41.7</span></span>         | <span data-ttu-id="a15b4-419">5.5</span><span class="sxs-lookup"><span data-stu-id="a15b4-419">5.5</span></span>        | <span data-ttu-id="a15b4-420">40.5</span><span class="sxs-lookup"><span data-stu-id="a15b4-420">40.5</span></span>         | <span data-ttu-id="a15b4-421">5.4</span><span class="sxs-lookup"><span data-stu-id="a15b4-421">5.4</span></span>        |
| <span data-ttu-id="a15b4-422">集計の集計クエリのみ (スイープを回避する合計 178)</span><span class="sxs-lookup"><span data-stu-id="a15b4-422">Just the AggregatingSubtotals queries (178 total - which avoids sweep)</span></span> | <span data-ttu-id="a15b4-423">39.5</span><span class="sxs-lookup"><span data-stu-id="a15b4-423">39.5</span></span>         | <span data-ttu-id="a15b4-424">4.5</span><span class="sxs-lookup"><span data-stu-id="a15b4-424">4.5</span></span>        | <span data-ttu-id="a15b4-425">38.1</span><span class="sxs-lookup"><span data-stu-id="a15b4-425">38.1</span></span>         | <span data-ttu-id="a15b4-426">4.6</span><span class="sxs-lookup"><span data-stu-id="a15b4-426">4.6</span></span>        |

<span data-ttu-id="a15b4-427">*すべての時間 (秒単位)。*</span><span class="sxs-lookup"><span data-stu-id="a15b4-427">*All times in seconds.*</span></span>

<span data-ttu-id="a15b4-428">教訓-多数の個別のクエリ (動的に作成されたクエリなど) を実行する場合、キャッシュは役に立ちません。また、キャッシュの結果として得られるキャッシュは、実際には、プランのキャッシュを使用した場合のパフォーマンスを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-428">Moral - when executing lots of distinct queries (for example,  dynamically created queries), caching doesn't help and the resulting flushing of the cache can keep the queries that would benefit the most from plan caching from actually using it.</span></span>

<span data-ttu-id="a15b4-429">集計の集計クエリは、テストしたクエリの中で最も複雑なクエリです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-429">The AggregatingSubtotals queries are the most complex of the queries we tested with.</span></span> <span data-ttu-id="a15b4-430">クエリが複雑になるほど、クエリプランのキャッシュによって得られる利点が多くなります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-430">As expected, the more complex the query is, the more benefit you will see from query plan caching.</span></span>

<span data-ttu-id="a15b4-431">CompiledQuery は実際にはプランがキャッシュされた LINQ クエリであるため、CompiledQuery と同等の Entity SQL クエリの比較には同様の結果が得られます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-431">Because a CompiledQuery is really a LINQ query with its plan cached, the comparison of a CompiledQuery versus the equivalent Entity SQL query should have similar results.</span></span> <span data-ttu-id="a15b4-432">実際、アプリに多数の動的 Entity SQL クエリがある場合、キャッシュにクエリを入力すると、CompiledQueries がキャッシュからフラッシュされるときに、実質的に "逆コンパイル" されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-432">In fact, if an app has lots of dynamic Entity SQL queries, filling the cache with queries will also effectively cause CompiledQueries to “decompile” when they are flushed from the cache.</span></span> <span data-ttu-id="a15b4-433">このシナリオでは、動的クエリのキャッシュを無効にして CompiledQueries の優先順位を設定することにより、パフォーマンスが向上する場合があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-433">In this scenario, performance may be improved by disabling caching on the dynamic queries to prioritize the CompiledQueries.</span></span> <span data-ttu-id="a15b4-434">しかし、もちろん、動的クエリではなく、パラメーター化クエリを使用するようにアプリケーションを書き直してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-434">Better yet, of course, would be to rewrite the app to use parameterized queries instead of dynamic queries.</span></span>

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a><span data-ttu-id="a15b4-435">3.3 LINQ クエリで CompiledQuery を使用してパフォーマンスを向上させる</span><span class="sxs-lookup"><span data-stu-id="a15b4-435">3.3 Using CompiledQuery to improve performance with LINQ queries</span></span>

<span data-ttu-id="a15b4-436">Microsoft のテストでは、CompiledQuery を使用すると、autocompiled された LINQ クエリで7% のメリットが得られることが示されています。つまり、Entity Framework スタックからコードを実行するのに7% 少ない時間を費やすことになります。アプリケーションが7% 高速であるという意味ではありません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-436">Our tests indicate that using CompiledQuery can bring a benefit of 7% over autocompiled LINQ queries; this means that you’ll spend 7% less time executing code from the Entity Framework stack; it does not mean your application will be 7% faster.</span></span> <span data-ttu-id="a15b4-437">一般に、EF 5.0 での CompiledQuery オブジェクトの作成と保守にかかるコストは、利点と比較すると問題にならない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-437">Generally speaking, the cost of writing and maintaining CompiledQuery objects in EF 5.0 may not be worth the trouble when compared to the benefits.</span></span> <span data-ttu-id="a15b4-438">マイレージは異なる場合があるため、プロジェクトに余分なプッシュが必要な場合は、このオプションを選択してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-438">Your mileage may vary, so exercise this option if your project requires the extra push.</span></span> <span data-ttu-id="a15b4-439">CompiledQueries は ObjectContext 派生モデルとのみ互換性があり、DbContext 派生モデルと互換性がないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-439">Note that CompiledQueries are only compatible with ObjectContext-derived models, and not compatible with DbContext-derived models.</span></span>

<span data-ttu-id="a15b4-440">CompiledQuery の作成と呼び出しの詳細については、「[コンパイル済みクエリ (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-440">For more information on creating and invoking a CompiledQuery, see [Compiled Queries (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).</span></span>

<span data-ttu-id="a15b4-441">CompiledQuery を使用する場合は、次の2つの点を考慮する必要があります。つまり、静的インスタンスを使用する必要があり、省かによって発生する問題です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-441">There are two considerations you have to take when using a CompiledQuery, namely the requirement to use static instances and the problems they have with composability.</span></span> <span data-ttu-id="a15b4-442">ここでは、これら2つの考慮事項の詳細について説明します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-442">Here follows an in-depth explanation of these two considerations.</span></span>

#### <a name="331-use-static-compiledquery-instances"></a><span data-ttu-id="a15b4-443">3.3.1 static CompiledQuery インスタンスを使用する</span><span class="sxs-lookup"><span data-stu-id="a15b4-443">3.3.1       Use static CompiledQuery instances</span></span>

<span data-ttu-id="a15b4-444">LINQ クエリのコンパイルは時間がかかるプロセスであるため、データベースからデータをフェッチする必要があるたびに、このクエリを実行しないようにします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-444">Since compiling a LINQ query is a time-consuming process, we don’t want to do it every time we need to fetch data from the database.</span></span> <span data-ttu-id="a15b4-445">CompiledQuery インスタンスを使用すると、一度コンパイルして複数回実行できますが、同じ CompiledQuery インスタンスを何度もコンパイルするのではなく、毎回再利用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-445">CompiledQuery instances allow you to compile once and run multiple times, but you have to be careful and procure to re-use the same CompiledQuery instance every time instead of compiling it over and over again.</span></span> <span data-ttu-id="a15b4-446">CompiledQuery インスタンスを格納するための静的メンバーの使用が必要になります。そうしないと、利点は得られません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-446">The use of static members to store the CompiledQuery instances becomes necessary; otherwise you won’t see any benefit.</span></span>

<span data-ttu-id="a15b4-447">たとえば、選択したカテゴリの製品の表示を処理するために、ページに次のメソッド本体があるとします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-447">For example, suppose your page has the following method body to handle displaying the products for the selected category:</span></span>

``` csharp
    // Warning: this is the wrong way of using CompiledQuery
    using (NorthwindEntities context = new NorthwindEntities())
    {
        string selectedCategory = this.categoriesList.SelectedValue;

        var productsForCategory = CompiledQuery.Compile<NorthwindEntities, string, IQueryable<Product>>(
            (NorthwindEntities nwnd, string category) =>
                nwnd.Products.Where(p => p.Category.CategoryName == category)
        );

        this.productsGrid.DataSource = productsForCategory.Invoke(context, selectedCategory).ToList();
        this.productsGrid.DataBind();
    }

    this.productsGrid.Visible = true;
```

<span data-ttu-id="a15b4-448">この場合は、メソッドが呼び出されるたびに、新しい CompiledQuery インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-448">In this case, you will create a new CompiledQuery instance on the fly every time the method is called.</span></span> <span data-ttu-id="a15b4-449">クエリプランキャッシュからストアコマンドを取得することによってパフォーマンス上の利点を確認するのではなく、新しいインスタンスが作成されるたびに CompiledQuery がプランコンパイラを経由します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-449">Instead of seeing performance benefits by retrieving the store command from the query plan cache, the CompiledQuery will go through the plan compiler every time a new instance is created.</span></span> <span data-ttu-id="a15b4-450">実際には、メソッドが呼び出されるたびに、新しい CompiledQuery エントリでクエリプランキャッシュに汚染されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-450">In fact, you will be polluting your query plan cache with a new CompiledQuery entry every time the method is called.</span></span>

<span data-ttu-id="a15b4-451">代わりに、コンパイルされたクエリの静的インスタンスを作成する必要があるため、メソッドが呼び出されるたびに、同じコンパイル済みのクエリを呼び出すことになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-451">Instead, you want to create a static instance of the compiled query, so you are invoking the same compiled query every time the method is called.</span></span> <span data-ttu-id="a15b4-452">これを行う方法の1つとして、CompiledQuery インスタンスをオブジェクトコンテキストのメンバーとして追加する方法があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-452">One way to so this is by adding the CompiledQuery instance as a member of your object context.</span></span><span data-ttu-id="a15b4-453">  次に、ヘルパーメソッドを使用して CompiledQuery にアクセスすることにより、これを簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-453">  You can then make things a little cleaner by accessing the CompiledQuery through a helper method:</span></span>

``` csharp
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IEnumerable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
            );

        public IEnumerable<Product> GetProductsForCategory(string categoryName)
        {
            return productsForCategoryCQ.Invoke(this, categoryName).ToList();
        }
```

<span data-ttu-id="a15b4-454">このヘルパーメソッドは次のように呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-454">This helper method would be invoked as follows:</span></span>

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a><span data-ttu-id="a15b4-455">3.3.2 CompiledQuery を構成する</span><span class="sxs-lookup"><span data-stu-id="a15b4-455">3.3.2       Composing over a CompiledQuery</span></span>

<span data-ttu-id="a15b4-456">LINQ クエリを構成する機能は非常に便利です。これを行うには、 *Skip ()* や*Count ()* などの IQueryable の後にメソッドを呼び出すだけです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-456">The ability to compose over any LINQ query is extremely useful; to do this, you simply invoke a method after the IQueryable such as *Skip()* or *Count()*.</span></span> <span data-ttu-id="a15b4-457">ただし、これを行うと、実質的に新しい IQueryable オブジェクトが返されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-457">However, doing so essentially returns a new IQueryable object.</span></span> <span data-ttu-id="a15b4-458">技術的には CompiledQuery の構成を防ぐことはできませんが、これを行うと、プランコンパイラを再度渡す必要がある新しい IQueryable オブジェクトが生成されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-458">While there’s nothing to stop you technically from composing over a CompiledQuery, doing so will cause the generation of a new IQueryable object that requires passing through the plan compiler again.</span></span>

<span data-ttu-id="a15b4-459">一部のコンポーネントでは、構成済みの IQueryable オブジェクトを使用して高度な機能を有効にします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-459">Some components will make use of composed IQueryable objects to enable advanced functionality.</span></span> <span data-ttu-id="a15b4-460">たとえば、ASP のようにします。ネットワークの GridView は、SelectMethod プロパティを使用して IQueryable オブジェクトにデータバインドできます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-460">For example, ASP.NET’s GridView can be data-bound to an IQueryable object via the SelectMethod property.</span></span> <span data-ttu-id="a15b4-461">次に、GridView がこの IQueryable オブジェクトを構成して、データモデルに対する並べ替えとページングを可能にします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-461">The GridView will then compose over this IQueryable object to allow sorting and paging over the data model.</span></span> <span data-ttu-id="a15b4-462">ご覧のように、GridView に CompiledQuery を使用しても、コンパイルされたクエリにはヒットしませんが、新しい autocompiled 済みクエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-462">As you can see, using a CompiledQuery for the GridView would not hit the compiled query but would generate a new autocompiled query.</span></span>

<span data-ttu-id="a15b4-463">これには、クエリにプログレッシブフィルターを追加する場合があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-463">One place where you may run into this is when adding progressive filters to a query.</span></span> <span data-ttu-id="a15b4-464">たとえば、オプションのフィルター (Country や OrdersCount など) に対して複数のドロップダウンリストを含む Customers ページがあるとします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-464">For example, suppose you had a Customers page with several drop-down lists for optional filters (for example, Country and OrdersCount).</span></span> <span data-ttu-id="a15b4-465">CompiledQuery の IQueryable の結果に対してこれらのフィルターを構成できますが、これを行うと、実行するたびに新しいクエリがプランコンパイラを通過します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-465">You can compose these filters over the IQueryable results of a CompiledQuery, but doing so will result in the new query going through the plan compiler every time you execute it.</span></span>

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployee();

        if (this.orderCountFilterList.SelectedItem.Value != defaultFilterText)
        {
            int orderCount = int.Parse(orderCountFilterList.SelectedValue);
            myCustomers = myCustomers.Where(c => c.Orders.Count > orderCount);
        }

        if (this.countryFilterList.SelectedItem.Value != defaultFilterText)
        {
            myCustomers = myCustomers.Where(c => c.Address.Country == countryFilterList.SelectedValue);
        }

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 <span data-ttu-id="a15b4-466">この再コンパイルを回避するには、考えられるフィルターを考慮して CompiledQuery を書き直します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-466">To avoid this re-compilation, you can rewrite the CompiledQuery to take the possible filters into account:</span></span>

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

<span data-ttu-id="a15b4-467">これは、次のような UI で呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-467">Which would be invoked in the UI like:</span></span>

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        int? countFilter = (this.orderCountFilterList.SelectedIndex == 0) ?
            (int?)null :
            int.Parse(this.orderCountFilterList.SelectedValue);

        string countryFilter = (this.countryFilterList.SelectedIndex == 0) ?
            null :
            this.countryFilterList.SelectedValue;

        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployeeWithFilters(
                countFilter, countryFilter);

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 <span data-ttu-id="a15b4-468">ここでのトレードオフとして、生成されたストアコマンドには常に null チェック付きのフィルターがありますが、データベースサーバーを最適化するには非常に簡単である必要があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-468">A tradeoff here is the generated store command will always have the filters with the null checks, but these should be fairly simple for the database server to optimize:</span></span>

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a><span data-ttu-id="a15b4-469">3.4 メタデータのキャッシュ</span><span class="sxs-lookup"><span data-stu-id="a15b4-469">3.4 Metadata caching</span></span>

<span data-ttu-id="a15b4-470">Entity Framework では、メタデータのキャッシュもサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-470">The Entity Framework also supports Metadata caching.</span></span> <span data-ttu-id="a15b4-471">これは基本的に、同じモデルへの異なる接続間での型情報と型対データベースマッピング情報のキャッシュです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-471">This is essentially caching of type information and type-to-database mapping information across different connections to the same model.</span></span> <span data-ttu-id="a15b4-472">メタデータキャッシュは、AppDomain ごとに一意です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-472">The Metadata cache is unique per AppDomain.</span></span>

#### <a name="341-metadata-caching-algorithm"></a><span data-ttu-id="a15b4-473">3.4.1 Metadata キャッシュアルゴリズム</span><span class="sxs-lookup"><span data-stu-id="a15b4-473">3.4.1 Metadata Caching algorithm</span></span>

1.  <span data-ttu-id="a15b4-474">モデルのメタデータ情報は、各 EntityConnection の ItemCollection に格納されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-474">Metadata information for a model is stored in an ItemCollection for each EntityConnection.</span></span>
    -   <span data-ttu-id="a15b4-475">注として、モデルのさまざまな部分に異なる ItemCollection オブジェクトがあります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-475">As a side note, there are different ItemCollection objects for different parts of the model.</span></span> <span data-ttu-id="a15b4-476">たとえば、StoreItemCollections には、データベースモデルに関する情報が含まれています。ObjectItemCollection には、データモデルに関する情報が含まれています。EdmItemCollection には、概念モデルに関する情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-476">For example, StoreItemCollections contains the information about the database model; ObjectItemCollection contains information about the data model; EdmItemCollection contains information about the conceptual model.</span></span>

2.  <span data-ttu-id="a15b4-477">2つの接続が同じ接続文字列を使用する場合は、同じ ItemCollection インスタンスを共有します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-477">If two connections use the same connection string, they will share the same ItemCollection instance.</span></span>
3.  <span data-ttu-id="a15b4-478">機能的に同等の接続文字列では、異なるメタデータキャッシュが生成される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-478">Functionally equivalent but textually different connection strings may result in different metadata caches.</span></span> <span data-ttu-id="a15b4-479">接続文字列をトークン化するので、トークンの順序を変更するだけで、共有メタデータが生成されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-479">We do tokenize connection strings, so simply changing the order of the tokens should result in shared metadata.</span></span> <span data-ttu-id="a15b4-480">しかし、機能的に同じように見える2つの接続文字列は、トークン化後に同一として評価されない場合があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-480">But two connection strings that seem functionally the same may not be evaluated as identical after tokenization.</span></span>
4.  <span data-ttu-id="a15b4-481">ItemCollection は、使用するために定期的にチェックされます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-481">The ItemCollection is periodically checked for use.</span></span> <span data-ttu-id="a15b4-482">ワークスペースが最近アクセスされていないと判断された場合は、次回のキャッシュスイープでクリーンアップ用にマークされます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-482">If it is determined that a workspace has not been accessed recently, it will be marked for cleanup on the next cache sweep.</span></span>
5.  <span data-ttu-id="a15b4-483">EntityConnection を作成するだけで、メタデータキャッシュが作成されます (ただし、接続が開かれるまで、その中の項目コレクションは初期化されません)。</span><span class="sxs-lookup"><span data-stu-id="a15b4-483">Merely creating an EntityConnection will cause a metadata cache to be created (though the item collections in it will not be initialized until the connection is opened).</span></span> <span data-ttu-id="a15b4-484">このワークスペースは、"使用中" ではないことがキャッシュアルゴリズムによって判断されるまで、メモリ内に残ります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-484">This workspace will remain in-memory until the caching algorithm determines it is not “in use”.</span></span>

<span data-ttu-id="a15b4-485">お客様のアドバイザリチームは、大規模なモデルを使用する場合に "廃止" されることを避けるために、ItemCollection への参照を保持することを説明するブログ投稿を作成しました: \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>。</span><span class="sxs-lookup"><span data-stu-id="a15b4-485">The Customer Advisory Team has written a blog post that describes holding a reference to an ItemCollection in order to avoid "deprecation" when using large models: \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>.</span></span>

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a><span data-ttu-id="a15b4-486">メタデータのキャッシュとクエリプランのキャッシュの関係を3.4.2</span><span class="sxs-lookup"><span data-stu-id="a15b4-486">3.4.2 The relationship between Metadata Caching and Query Plan Caching</span></span>

<span data-ttu-id="a15b4-487">クエリプランのキャッシュインスタンスは、MetadataWorkspace のストア型の ItemCollection に存在します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-487">The query plan cache instance lives in the MetadataWorkspace's ItemCollection of store types.</span></span> <span data-ttu-id="a15b4-488">つまり、キャッシュされたストアコマンドは、特定の MetadataWorkspace を使用してインスタンス化されたコンテキストに対してクエリを実行するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-488">This means that cached store commands will be used for queries against any context instantiated using a given MetadataWorkspace.</span></span> <span data-ttu-id="a15b4-489">また、2つの接続文字列が少し異なり、トークン化後に一致しない場合は、クエリプランのキャッシュインスタンスが異なることになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-489">It also means that if you have two connections strings that are slightly different and don't match after tokenizing, you will have different query plan cache instances.</span></span>

### <a name="35-results-caching"></a><span data-ttu-id="a15b4-490">3.5 結果のキャッシュ</span><span class="sxs-lookup"><span data-stu-id="a15b4-490">3.5 Results caching</span></span>

<span data-ttu-id="a15b4-491">結果のキャッシュ ("第2レベルのキャッシュ" とも呼ばれます) を使用すると、クエリの結果をローカルキャッシュに保持できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-491">With results caching (also known as "second-level caching"), you keep the results of queries in a local cache.</span></span> <span data-ttu-id="a15b4-492">クエリを発行するときは、まず、ストアに対してクエリを実行する前に、結果をローカルで使用できるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-492">When issuing a query, you first see if the results are available locally before you query against the store.</span></span> <span data-ttu-id="a15b4-493">Entity Framework では結果のキャッシュが直接サポートされていませんが、ラッピングプロバイダーを使用して第2レベルのキャッシュを追加することができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-493">While results caching isn't directly supported by Entity Framework, it's possible to add a second level cache by using a wrapping provider.</span></span> <span data-ttu-id="a15b4-494">2番目のレベルのキャッシュを使用してプロバイダーをラップする例は、 [NCache に基づく Entity Framework 第2レベルのキャッシュ](https://www.alachisoft.com/ncache/entity-framework.html)です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-494">An example wrapping provider with a second-level cache is Alachisoft's [Entity Framework Second Level Cache based on NCache](https://www.alachisoft.com/ncache/entity-framework.html).</span></span>

<span data-ttu-id="a15b4-495">この第2レベルのキャッシュの実装は、LINQ 式の評価 (および funcletized) 後に行われる挿入された機能であり、クエリ実行プランが最初のレベルのキャッシュから計算または取得されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-495">This implementation of second-level caching is an injected functionality that takes place after the LINQ expression has been evaluated (and funcletized) and the query execution plan is computed or retrieved from the first-level cache.</span></span> <span data-ttu-id="a15b4-496">2番目のレベルのキャッシュでは、生のデータベースの結果だけが格納されるため、具体化パイプラインは後でも実行されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-496">The second-level cache will then store only the raw database results, so the materialization pipeline still executes afterwards.</span></span>

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a><span data-ttu-id="a15b4-497">3.5.1 ラッププロバイダーを使用した結果のキャッシュに関するその他の参照情報</span><span class="sxs-lookup"><span data-stu-id="a15b4-497">3.5.1 Additional references for results caching with the wrapping provider</span></span>

-   <span data-ttu-id="a15b4-498">ジュリー Lerman は、Windows Server AppFabric のキャッシュを使用するようにサンプルラッピングプロバイダーを更新する方法を含む「Entity Framework と Windows Azure での第2レベルのキャッシュ」という MSDN 記事を執筆しました。 [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span><span class="sxs-lookup"><span data-stu-id="a15b4-498">Julie Lerman has written a "Second-Level Caching in Entity Framework and Windows Azure" MSDN article that includes how to update the sample wrapping provider to use Windows Server AppFabric caching: [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span></span>
-   <span data-ttu-id="a15b4-499">Entity Framework 5 で作業している場合、チームのブログには、Entity Framework 5: \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>のキャッシュプロバイダーで実行する方法を説明する投稿があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-499">If you are working with Entity Framework 5, the team blog has a post that describes how to get things running with the caching provider for Entity Framework 5: \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>.</span></span> <span data-ttu-id="a15b4-500">また、2番目のレベルのキャッシュをプロジェクトに自動的に追加するための T4 テンプレートも含まれています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-500">It also includes a T4 template to help automate adding the 2nd-level caching to your project.</span></span>

## <a name="4-autocompiled-queries"></a><span data-ttu-id="a15b4-501">4 autocompiled クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-501">4 Autocompiled Queries</span></span>

<span data-ttu-id="a15b4-502">Entity Framework を使用してデータベースに対してクエリを実行する場合は、実際に結果を具体化する前に一連の手順を実行する必要があります。このような手順の1つは、クエリのコンパイルです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-502">When a query is issued against a database using Entity Framework, it must go through a series of steps before actually materializing the results; one such step is Query Compilation.</span></span> <span data-ttu-id="a15b4-503">Entity SQL のクエリは自動的にキャッシュされるため、パフォーマンスが良好であるとわかっています。そのため、同じクエリを2回目または3回実行すると、プランコンパイラをスキップし、キャッシュされたプランを代わりに使用できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-503">Entity SQL queries were known to have good performance as they are automatically cached, so the second or third time you execute the same query it can skip the plan compiler and use the cached plan instead.</span></span>

<span data-ttu-id="a15b4-504">Entity Framework 5 では、LINQ to Entities クエリの自動キャッシュも導入されました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-504">Entity Framework 5 introduced automatic caching for LINQ to Entities queries as well.</span></span> <span data-ttu-id="a15b4-505">Entity Framework の過去のエディションでは、パフォーマンスを向上させるために CompiledQuery を作成するのが一般的です。これは、LINQ to Entities クエリをキャッシュ可能にするためです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-505">In past editions of Entity Framework creating a CompiledQuery to speed your performance was a common practice, as this would make your LINQ to Entities query cacheable.</span></span> <span data-ttu-id="a15b4-506">キャッシュは CompiledQuery を使用せずに自動的に実行されるため、この機能を "autocompiled されたクエリ" と呼びます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-506">Since caching is now done automatically without the use of a CompiledQuery, we call this feature “autocompiled queries”.</span></span> <span data-ttu-id="a15b4-507">クエリプランのキャッシュとそのしくみの詳細については、「クエリプランのキャッシュ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-507">For more information about the query plan cache and its mechanics, see Query Plan Caching.</span></span>

<span data-ttu-id="a15b4-508">Entity Framework は、クエリを再コンパイルする必要があるかどうかを検出し、以前にコンパイルされた場合でもクエリが呼び出されたときにそのクエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-508">Entity Framework detects when a query requires to be recompiled, and does so when the query is invoked even if it had been compiled before.</span></span> <span data-ttu-id="a15b4-509">クエリの再コンパイルの原因となる一般的な条件は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-509">Common conditions that cause the query to be recompiled are:</span></span>

-   <span data-ttu-id="a15b4-510">クエリに関連付けられている MergeOption を変更します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-510">Changing the MergeOption associated to your query.</span></span> <span data-ttu-id="a15b4-511">キャッシュされたクエリは使用されません。代わりに、プランコンパイラが再度実行され、新しく作成されたプランがキャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-511">The cached query will not be used, instead the plan compiler will run again and the newly created plan gets cached.</span></span>
-   <span data-ttu-id="a15b4-512">ContextOptions の値を変更します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-512">Changing the value of ContextOptions.UseCSharpNullComparisonBehavior.</span></span> <span data-ttu-id="a15b4-513">MergeOption を変更するのと同じ効果が得られます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-513">You get the same effect as changing the MergeOption.</span></span>

<span data-ttu-id="a15b4-514">その他の条件により、クエリでキャッシュを使用できなくなる場合があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-514">Other conditions can prevent your query from using the cache.</span></span> <span data-ttu-id="a15b4-515">一般的な例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-515">Common examples are:</span></span>

-   <span data-ttu-id="a15b4-516">IEnumerable&lt;T&gt;を使用します。&lt;&gt;(T 値) を格納します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-516">Using IEnumerable&lt;T&gt;.Contains&lt;&gt;(T value).</span></span>
-   <span data-ttu-id="a15b4-517">定数を使用してクエリを生成する関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-517">Using functions that produce queries with constants.</span></span>
-   <span data-ttu-id="a15b4-518">マップされていないオブジェクトのプロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-518">Using the properties of a non-mapped object.</span></span>
-   <span data-ttu-id="a15b4-519">再コンパイルが必要な別のクエリにクエリをリンクします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-519">Linking your query to another query that requires to be recompiled.</span></span>

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a><span data-ttu-id="a15b4-520">4.1 IEnumerable&lt;T&gt;を使用しています。&lt;T&gt;(T 値) を格納します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-520">4.1 Using IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value)</span></span>

<span data-ttu-id="a15b4-521">Entity Framework では、IEnumerable&lt;T&gt;を呼び出すクエリはキャッシュされません。メモリ内コレクションに対して&lt;T&gt;(T 値) を格納します。これは、コレクションの値が volatile であると見なされるためです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-521">Entity Framework does not cache queries that invoke IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) against an in-memory collection, since the values of the collection are considered volatile.</span></span> <span data-ttu-id="a15b4-522">次のクエリ例はキャッシュされないため、常にプランコンパイラによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-522">The following example query will not be cached, so it will always be processed by the plan compiler:</span></span>

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var query = context.MyEntities
                    .Where(entity => ids.Contains(entity.Id));

    var results = query.ToList();
    ...
}
```

<span data-ttu-id="a15b4-523">Contains が実行される IEnumerable のサイズによって、クエリのコンパイル速度や速度が決まります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-523">Note that the size of the IEnumerable against which Contains is executed determines how fast or how slow your query is compiled.</span></span> <span data-ttu-id="a15b4-524">上の例に示されているような大きなコレクションを使用すると、パフォーマンスが大幅に低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-524">Performance can suffer significantly when using large collections such as the one shown in the example above.</span></span>

<span data-ttu-id="a15b4-525">Entity Framework 6 では、IEnumerable&lt;T&gt;の最適化について説明しています。クエリが実行されるときに、&lt;T&gt;(T 値) を格納します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-525">Entity Framework 6 contains optimizations to the way IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) works when queries are executed.</span></span> <span data-ttu-id="a15b4-526">生成される SQL コードは、生成され、読みやすくなります。また、ほとんどの場合、サーバーでもより高速に実行されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-526">The SQL code that is generated is much faster to produce and more readable, and in most cases it also executes faster in the server.</span></span>

### <a name="42-using-functions-that-produce-queries-with-constants"></a><span data-ttu-id="a15b4-527">4.2 定数を使用したクエリを生成する関数の使用</span><span class="sxs-lookup"><span data-stu-id="a15b4-527">4.2 Using functions that produce queries with constants</span></span>

<span data-ttu-id="a15b4-528">Skip ()、Take ()、Contains ()、および Def/Tifempty () LINQ 演算子は、パラメーターを使用して SQL クエリを生成するのではなく、値を定数として渡します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-528">The Skip(), Take(), Contains() and DefautIfEmpty() LINQ operators do not produce SQL queries with parameters but instead put the values passed to them as constants.</span></span> <span data-ttu-id="a15b4-529">このため、それ以外の場合は、クエリプランのキャッシュが EF stack とデータベースサーバーの両方に汚染され、その後のクエリ実行で同じ定数が使用されていない限り、再利用されません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-529">Because of this, queries that might otherwise be identical end up polluting the query plan cache, both on the EF stack and on the database server, and do not get reutilized unless the same constants are used in a subsequent query execution.</span></span> <span data-ttu-id="a15b4-530">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-530">For example:</span></span>

``` csharp
var id = 10;
...
using (var context = new MyContext())
{
    var query = context.MyEntities.Select(entity => entity.Id).Contains(id);

    var results = query.ToList();
    ...
}
```

<span data-ttu-id="a15b4-531">この例では、このクエリが id に対して異なる値を使用して実行されるたびに、クエリが新しいプランにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-531">In this example, each time this query is executed with a different value for id the query will be compiled into a new plan.</span></span>

<span data-ttu-id="a15b4-532">特に、ページングを行うときに Skip と Take を使用することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-532">In particular pay attention to the use of Skip and Take when doing paging.</span></span> <span data-ttu-id="a15b4-533">EF6 では、これらのメソッドには、キャッシュされたクエリプランを効果的に再利用できるようにするラムダオーバーロードがあります。 EF はこれらのメソッドに渡された変数をキャプチャし、SQLparameters に変換することができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-533">In EF6 these methods have a lambda overload that effectively makes the cached query plan reusable because EF can capture variables passed to these methods and translate them to SQLparameters.</span></span> <span data-ttu-id="a15b4-534">これによってキャッシュクリーナーを維持することもできます。それ以外の場合、Skip と Take の異なる定数を使用する各クエリは、独自のクエリプランキャッシュエントリを取得します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-534">This also helps keep the cache cleaner since otherwise each query with a different constant for Skip and Take would get its own query plan cache entry.</span></span>

<span data-ttu-id="a15b4-535">次のコードについて考えてみます。これは最適ではありませんが、このクラスのクエリを知性することのみを目的としています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-535">Consider the following code, which is suboptimal but is only meant to exemplify this class of queries:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="a15b4-536">この同じコードのより高速なバージョンでは、ラムダを使用した Skip の呼び出しが必要になります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-536">A faster version of this same code would involve calling Skip with a lambda:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="a15b4-537">2番目のスニペットは、クエリが実行されるたびに同じクエリプランが使用されるため、最大で11% 高速に実行されます。これにより、CPU 時間が節約され、クエリキャッシュの汚染を回避できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-537">The second snippet may run up to 11% faster because the same query plan is used every time the query is run, which saves CPU time and avoids polluting the query cache.</span></span> <span data-ttu-id="a15b4-538">さらに、スキップするパラメーターはクロージャ内にあるため、コードは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-538">Furthermore, because the parameter to Skip is in a closure the code might as well look like this now:</span></span>

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a><span data-ttu-id="a15b4-539">4.3 マップされていないオブジェクトのプロパティを使用する</span><span class="sxs-lookup"><span data-stu-id="a15b4-539">4.3 Using the properties of a non-mapped object</span></span>

<span data-ttu-id="a15b4-540">クエリで、マップされていないオブジェクトの種類のプロパティをパラメーターとして使用する場合、クエリはキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-540">When a query uses the properties of a non-mapped object type as a parameter then the query will not get cached.</span></span> <span data-ttu-id="a15b4-541">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-541">For example:</span></span>

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();

    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myObject.MyProperty)
                select entity;

   var results = query.ToList();
    ...
}
```

<span data-ttu-id="a15b4-542">この例では、NonMappedType クラスがエンティティモデルの一部ではないとします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-542">In this example, assume that class NonMappedType is not part of the Entity model.</span></span> <span data-ttu-id="a15b4-543">このクエリは、マップされていない型を使用しないように簡単に変更できます。代わりに、クエリのパラメーターとしてローカル変数を使用します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-543">This query can easily be changed to not use a non-mapped type and instead use a local variable as the parameter to the query:</span></span>

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();
    var myValue = myObject.MyProperty;
    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myValue)
                select entity;

    var results = query.ToList();
    ...
}
```

<span data-ttu-id="a15b4-544">この場合、クエリはキャッシュされ、クエリプランキャッシュの恩恵を受けることができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-544">In this case, the query will be able to get cached and will benefit from the query plan cache.</span></span>

### <a name="44-linking-to-queries-that-require-recompiling"></a><span data-ttu-id="a15b4-545">4.4 再コンパイルが必要なクエリへのリンク</span><span class="sxs-lookup"><span data-stu-id="a15b4-545">4.4 Linking to queries that require recompiling</span></span>

<span data-ttu-id="a15b4-546">前と同じ例に従って、再コンパイルが必要なクエリに依存する2番目のクエリがある場合、2番目のクエリも再コンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-546">Following the same example as above, if you have a second query that relies on a query that needs to be recompiled, your entire second query will also be recompiled.</span></span> <span data-ttu-id="a15b4-547">このシナリオを説明する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-547">Here’s an example to illustrate this scenario:</span></span>

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var firstQuery = from entity in context.MyEntities
                        where ids.Contains(entity.Id)
                        select entity;

    var secondQuery = from entity in context.MyEntities
                        where firstQuery.Any(otherEntity => otherEntity.Id == entity.Id)
                        select entity;

    var results = secondQuery.ToList();
    ...
}
```

<span data-ttu-id="a15b4-548">この例はジェネリックですが、firstQuery へのリンクによって、クエリがキャッシュされないようにする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-548">The example is generic, but it illustrates how linking to firstQuery is causing secondQuery to be unable to get cached.</span></span> <span data-ttu-id="a15b4-549">最初のクエリが再コンパイルを必要とするクエリでなかった場合、そのクエリはキャッシュされています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-549">If firstQuery had not been a query that requires recompiling, then secondQuery would have been cached.</span></span>

## <a name="5-notracking-queries"></a><span data-ttu-id="a15b4-550">5個の NoTracking クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-550">5 NoTracking Queries</span></span>

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a><span data-ttu-id="a15b4-551">5.1 変更の追跡を無効にして状態管理のオーバーヘッドを削減する</span><span class="sxs-lookup"><span data-stu-id="a15b4-551">5.1 Disabling change tracking to reduce state management overhead</span></span>

<span data-ttu-id="a15b4-552">読み取り専用のシナリオで、ObjectStateManager にオブジェクトを読み込むオーバーヘッドを回避する必要がある場合は、"追跡しない" クエリを発行できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-552">If you are in a read-only scenario and want to avoid the overhead of loading the objects into the ObjectStateManager, you can issue "No Tracking" queries.</span></span><span data-ttu-id="a15b4-553">  変更の追跡は、クエリレベルで無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-553">  Change tracking can be disabled at the query level.</span></span>

<span data-ttu-id="a15b4-554">ただし、変更の追跡を無効にすることで、オブジェクトキャッシュを効果的に無効にすることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-554">Note though that by disabling change tracking you are effectively turning off the object cache.</span></span> <span data-ttu-id="a15b4-555">エンティティに対してクエリを実行する場合、以前に具体化されたクエリの結果を ObjectStateManager からプルすることで、具体化をスキップすることはできません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-555">When you query for an entity, we can't skip materialization by pulling the previously-materialized query results from the ObjectStateManager.</span></span> <span data-ttu-id="a15b4-556">同じコンテキストで同じエンティティに対して繰り返しクエリを実行する場合、変更の追跡を有効にすると、パフォーマンス上の利点が得られる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-556">If you are repeatedly querying for the same entities on the same context, you might actually see a performance benefit from enabling change tracking.</span></span>

<span data-ttu-id="a15b4-557">ObjectContext を使用してクエリを実行する場合、ObjectQuery インスタンスと ObjectSet インスタンスは、設定されると、MergeOption を記憶します。これらのインスタンスで構成されるクエリは、親クエリの有効な MergeOption を継承します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-557">When querying using ObjectContext, ObjectQuery and ObjectSet instances will remember a MergeOption once it is set, and queries that are composed on them will inherit the effective MergeOption of the parent query.</span></span> <span data-ttu-id="a15b4-558">DbContext を使用する場合、Dbcontext で AsNoTracking () 修飾子を呼び出すことによって追跡を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-558">When using DbContext, tracking can be disabled by calling the AsNoTracking() modifier on the DbSet.</span></span>

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a><span data-ttu-id="a15b4-559">5.1.1 DbContext を使用する場合のクエリの変更の追跡の無効化</span><span class="sxs-lookup"><span data-stu-id="a15b4-559">5.1.1 Disabling change tracking for a query when using DbContext</span></span>

<span data-ttu-id="a15b4-560">クエリで AsNoTracking () メソッドの呼び出しを連結することによって、クエリのモードを NoTracking に切り替えることができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-560">You can switch the mode of a query to NoTracking by chaining a call to the AsNoTracking() method in the query.</span></span> <span data-ttu-id="a15b4-561">ObjectQuery とは異なり、Dbset API の DbSet および Dbset クラスには、MergeOption の変更可能なプロパティがありません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-561">Unlike ObjectQuery, the DbSet and DbQuery classes in the DbContext API don’t have a mutable property for the MergeOption.</span></span>

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a><span data-ttu-id="a15b4-562">5.1.2 ObjectContext を使用してクエリレベルで変更の追跡を無効にする</span><span class="sxs-lookup"><span data-stu-id="a15b4-562">5.1.2 Disabling change tracking at the query level using ObjectContext</span></span>

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a><span data-ttu-id="a15b4-563">5.1.3 ObjectContext を使用してエンティティセット全体の変更の追跡を無効にする</span><span class="sxs-lookup"><span data-stu-id="a15b4-563">5.1.3 Disabling change tracking for an entire entity set using ObjectContext</span></span>

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a><span data-ttu-id="a15b4-564">5.2 NoTracking クエリのパフォーマンス上の利点を示す、テストメトリック</span><span class="sxs-lookup"><span data-stu-id="a15b4-564">5.2 Test Metrics demonstrating the performance benefit of NoTracking queries</span></span>

<span data-ttu-id="a15b4-565">このテストでは、追跡を Navision モデルの NoTracking クエリと比較することによって、ObjectStateManager の読み込みにかかるコストを確認します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-565">In this test we look at the cost of filling the ObjectStateManager by comparing Tracking to NoTracking queries for the Navision model.</span></span> <span data-ttu-id="a15b4-566">Navision モデルの説明と実行されたクエリの種類については、「付録」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-566">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="a15b4-567">このテストでは、クエリの一覧を反復処理し、各クエリを1回ずつ実行します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-567">In this test, we iterate through the list of queries and execute each one once.</span></span> <span data-ttu-id="a15b4-568">2種類のテストを実行しました。1回は NoTracking クエリ、もう1回は "AppendOnly" の既定のマージオプションです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-568">We ran two variations of the test, once with NoTracking queries and once with the default merge option of "AppendOnly".</span></span> <span data-ttu-id="a15b4-569">各バリエーションを3回実行し、実行の平均値を取得しました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-569">We ran each variation 3 times and take the mean value of the runs.</span></span> <span data-ttu-id="a15b4-570">テストの間に、SQL Server のクエリキャッシュをクリアし、次のコマンドを実行して tempdb を圧縮します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-570">Between the tests we clear the query cache on the SQL Server and shrink the tempdb by running the following commands:</span></span>

1.  <span data-ttu-id="a15b4-571">DBCC DROPCLEANBUFFERS</span><span class="sxs-lookup"><span data-stu-id="a15b4-571">DBCC DROPCLEANBUFFERS</span></span>
2.  <span data-ttu-id="a15b4-572">DBCC FREEPROCCACHE</span><span class="sxs-lookup"><span data-stu-id="a15b4-572">DBCC FREEPROCCACHE</span></span>
3.  <span data-ttu-id="a15b4-573">DBCC SHRINKDATABASE (tempdb、0)</span><span class="sxs-lookup"><span data-stu-id="a15b4-573">DBCC SHRINKDATABASE (tempdb, 0)</span></span>

<span data-ttu-id="a15b4-574">テスト結果、3回を超える実行:</span><span class="sxs-lookup"><span data-stu-id="a15b4-574">Test Results, median over 3 runs:</span></span>

|                        | <span data-ttu-id="a15b4-575">追跡なし–作業セット</span><span class="sxs-lookup"><span data-stu-id="a15b4-575">NO TRACKING – WORKING SET</span></span> | <span data-ttu-id="a15b4-576">追跡なし–時間</span><span class="sxs-lookup"><span data-stu-id="a15b4-576">NO TRACKING – TIME</span></span> | <span data-ttu-id="a15b4-577">追加のみ-ワーキングセット</span><span class="sxs-lookup"><span data-stu-id="a15b4-577">APPEND ONLY – WORKING SET</span></span> | <span data-ttu-id="a15b4-578">追加のみ–時間</span><span class="sxs-lookup"><span data-stu-id="a15b4-578">APPEND ONLY – TIME</span></span> |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| <span data-ttu-id="a15b4-579">**Entity Framework 5**</span><span class="sxs-lookup"><span data-stu-id="a15b4-579">**Entity Framework 5**</span></span> | <span data-ttu-id="a15b4-580">460361728</span><span class="sxs-lookup"><span data-stu-id="a15b4-580">460361728</span></span>                 | <span data-ttu-id="a15b4-581">1163536ミリ秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-581">1163536 ms</span></span>         | <span data-ttu-id="a15b4-582">596545536</span><span class="sxs-lookup"><span data-stu-id="a15b4-582">596545536</span></span>                 | <span data-ttu-id="a15b4-583">1273042ミリ秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-583">1273042 ms</span></span>         |
| <span data-ttu-id="a15b4-584">**Entity Framework 6**</span><span class="sxs-lookup"><span data-stu-id="a15b4-584">**Entity Framework 6**</span></span> | <span data-ttu-id="a15b4-585">647127040</span><span class="sxs-lookup"><span data-stu-id="a15b4-585">647127040</span></span>                 | <span data-ttu-id="a15b4-586">190228ミリ秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-586">190228 ms</span></span>          | <span data-ttu-id="a15b4-587">832798720</span><span class="sxs-lookup"><span data-stu-id="a15b4-587">832798720</span></span>                 | <span data-ttu-id="a15b4-588">195521ミリ秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-588">195521 ms</span></span>          |

<span data-ttu-id="a15b4-589">Entity Framework 5 では Entity Framework 6 の場合よりも、実行の終了時にメモリフットプリントが小さくなります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-589">Entity Framework 5 will have a smaller memory footprint at the end of the run than Entity Framework 6 does.</span></span> <span data-ttu-id="a15b4-590">Entity Framework 6 によって消費される追加メモリは、新しい機能を有効にし、パフォーマンスを向上させるための追加のメモリ構造とコードの結果になります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-590">The additional memory consumed by Entity Framework 6 is the result of additional memory structures and code that enable new features and better performance.</span></span>

<span data-ttu-id="a15b4-591">また、ObjectStateManager を使用する場合、メモリフットプリントにも明確な違いがあります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-591">There’s also a clear difference in memory footprint when using the ObjectStateManager.</span></span> <span data-ttu-id="a15b4-592">Entity Framework 5 では、データベースから具体化されたすべてのエンティティを追跡するときに、フットプリントを30% 増やしました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-592">Entity Framework 5 increased its footprint by 30% when keeping track of all the entities we materialized from the database.</span></span> <span data-ttu-id="a15b4-593">Entity Framework 6 では、その場合、フットプリントは28% 増加しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-593">Entity Framework 6 increased its footprint by 28% when doing so.</span></span>

<span data-ttu-id="a15b4-594">時間については、Entity Framework 6 は、このテストでは大きな余白によって 5 Entity Framework を意味します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-594">In terms of time, Entity Framework 6 outperforms Entity Framework 5 in this test by a large margin.</span></span> <span data-ttu-id="a15b4-595">Entity Framework 6 は、Entity Framework 5 によって消費された時間の約16% でテストを完了しました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-595">Entity Framework 6 completed the test in roughly 16% of the time consumed by Entity Framework 5.</span></span> <span data-ttu-id="a15b4-596">また、ObjectStateManager が使用されている場合、Entity Framework 5 は完了するまでに9% の時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-596">Additionally, Entity Framework 5 takes 9% more time to complete when the ObjectStateManager is being used.</span></span> <span data-ttu-id="a15b4-597">これに対し、Entity Framework 6 では、ObjectStateManager の使用時に3% の時間が使用されています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-597">In comparison, Entity Framework 6 is using 3% more time when using the ObjectStateManager.</span></span>

## <a name="6-query-execution-options"></a><span data-ttu-id="a15b4-598">6クエリ実行オプション</span><span class="sxs-lookup"><span data-stu-id="a15b4-598">6 Query Execution Options</span></span>

<span data-ttu-id="a15b4-599">Entity Framework は、いくつかの異なる方法でクエリを実行できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-599">Entity Framework offers several different ways to query.</span></span> <span data-ttu-id="a15b4-600">次のオプションを見て、それぞれの長所と短所を比較し、パフォーマンス特性を確認します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-600">We'll take a look at the following options, compare the pros and cons of each, and examine their performance characteristics:</span></span>

-   <span data-ttu-id="a15b4-601">LINQ to Entities。</span><span class="sxs-lookup"><span data-stu-id="a15b4-601">LINQ to Entities.</span></span>
-   <span data-ttu-id="a15b4-602">追跡 LINQ to Entities がありません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-602">No Tracking LINQ to Entities.</span></span>
-   <span data-ttu-id="a15b4-603">ObjectQuery を Entity SQL します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-603">Entity SQL over an ObjectQuery.</span></span>
-   <span data-ttu-id="a15b4-604">EntityCommand を Entity SQL します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-604">Entity SQL over an EntityCommand.</span></span>
-   <span data-ttu-id="a15b4-605">System.data.objects.objectcontext.executestorequery.</span><span class="sxs-lookup"><span data-stu-id="a15b4-605">ExecuteStoreQuery.</span></span>
-   <span data-ttu-id="a15b4-606">SqlQuery.</span><span class="sxs-lookup"><span data-stu-id="a15b4-606">SqlQuery.</span></span>
-   <span data-ttu-id="a15b4-607">CompiledQuery.</span><span class="sxs-lookup"><span data-stu-id="a15b4-607">CompiledQuery.</span></span>

### <a name="61-linq-to-entities-queries"></a><span data-ttu-id="a15b4-608">6.1 LINQ to Entities のクエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-608">6.1       LINQ to Entities queries</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="a15b4-609">**長所**</span><span class="sxs-lookup"><span data-stu-id="a15b4-609">**Pros**</span></span>

-   <span data-ttu-id="a15b4-610">CUD 操作に適しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-610">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="a15b4-611">完全に具体化したオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="a15b4-611">Fully materialized objects.</span></span>
-   <span data-ttu-id="a15b4-612">プログラミング言語に組み込まれている構文で記述するのが最も簡単です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-612">Simplest to write with syntax built into the programming language.</span></span>
-   <span data-ttu-id="a15b4-613">優れたパフォーマンス。</span><span class="sxs-lookup"><span data-stu-id="a15b4-613">Good performance.</span></span>

<span data-ttu-id="a15b4-614">**短所**</span><span class="sxs-lookup"><span data-stu-id="a15b4-614">**Cons**</span></span>

-   <span data-ttu-id="a15b4-615">次のような特定の技術的制限。</span><span class="sxs-lookup"><span data-stu-id="a15b4-615">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="a15b4-616">外部結合クエリで DefaultIfEmpty を使用するパターンは、Entity SQL の単純外部結合ステートメントよりも複雑なクエリになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-616">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="a15b4-617">一般的なパターンマッチングと同様にを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-617">You still can’t use LIKE with general pattern matching.</span></span>

### <a name="62-no-tracking-linq-to-entities-queries"></a><span data-ttu-id="a15b4-618">6.2 LINQ to Entities クエリは追跡しません</span><span class="sxs-lookup"><span data-stu-id="a15b4-618">6.2       No Tracking LINQ to Entities queries</span></span>

<span data-ttu-id="a15b4-619">コンテキストが ObjectContext を派生する場合:</span><span class="sxs-lookup"><span data-stu-id="a15b4-619">When the context derives ObjectContext:</span></span>

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="a15b4-620">コンテキストが DbContext を派生する場合:</span><span class="sxs-lookup"><span data-stu-id="a15b4-620">When the context derives DbContext:</span></span>

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="a15b4-621">**長所**</span><span class="sxs-lookup"><span data-stu-id="a15b4-621">**Pros**</span></span>

-   <span data-ttu-id="a15b4-622">通常の LINQ クエリよりもパフォーマンスが向上しました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-622">Improved performance over regular LINQ queries.</span></span>
-   <span data-ttu-id="a15b4-623">完全に具体化したオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="a15b4-623">Fully materialized objects.</span></span>
-   <span data-ttu-id="a15b4-624">プログラミング言語に組み込まれている構文で記述するのが最も簡単です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-624">Simplest to write with syntax built into the programming language.</span></span>

<span data-ttu-id="a15b4-625">**短所**</span><span class="sxs-lookup"><span data-stu-id="a15b4-625">**Cons**</span></span>

-   <span data-ttu-id="a15b4-626">CUD 操作には適していません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-626">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="a15b4-627">次のような特定の技術的制限。</span><span class="sxs-lookup"><span data-stu-id="a15b4-627">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="a15b4-628">外部結合クエリで DefaultIfEmpty を使用するパターンは、Entity SQL の単純外部結合ステートメントよりも複雑なクエリになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-628">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="a15b4-629">一般的なパターンマッチングと同様にを使用することはできません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-629">You still can’t use LIKE with general pattern matching.</span></span>

<span data-ttu-id="a15b4-630">NoTracking が指定されていない場合でも、スカラープロパティを射影するクエリは追跡されないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-630">Note that queries that project scalar properties are not tracked even if the NoTracking is not specified.</span></span> <span data-ttu-id="a15b4-631">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-631">For example:</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

<span data-ttu-id="a15b4-632">この特定のクエリでは、明示的に NoTracking が指定されていませんが、オブジェクト状態マネージャーが認識している型を具体化していないため、具体化された結果は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-632">This particular query doesn’t explicitly specify being NoTracking, but since it’s not materializing a type that’s known to the object state manager then the materialized result is not tracked.</span></span>

### <a name="63-entity-sql-over-an-objectquery"></a><span data-ttu-id="a15b4-633">6.3 ObjectQuery 上での Entity SQL</span><span class="sxs-lookup"><span data-stu-id="a15b4-633">6.3       Entity SQL over an ObjectQuery</span></span>

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

<span data-ttu-id="a15b4-634">**長所**</span><span class="sxs-lookup"><span data-stu-id="a15b4-634">**Pros**</span></span>

-   <span data-ttu-id="a15b4-635">CUD 操作に適しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-635">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="a15b4-636">完全に具体化したオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="a15b4-636">Fully materialized objects.</span></span>
-   <span data-ttu-id="a15b4-637">クエリプランのキャッシュをサポートします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-637">Supports query plan caching.</span></span>

<span data-ttu-id="a15b4-638">**短所**</span><span class="sxs-lookup"><span data-stu-id="a15b4-638">**Cons**</span></span>

-   <span data-ttu-id="a15b4-639">には、言語に組み込まれているクエリの構造よりもユーザーエラーが発生しやすいテキストクエリ文字列が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-639">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>

### <a name="64-entity-sql-over-an-entity-command"></a><span data-ttu-id="a15b4-640">6.4 Entity コマンドの Entity SQL</span><span class="sxs-lookup"><span data-stu-id="a15b4-640">6.4       Entity SQL over an Entity Command</span></span>

``` csharp
EntityCommand cmd = eConn.CreateCommand();
cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
{
    while (reader.Read())
    {
        // manually 'materialize' the product
    }
}
```

<span data-ttu-id="a15b4-641">**長所**</span><span class="sxs-lookup"><span data-stu-id="a15b4-641">**Pros**</span></span>

-   <span data-ttu-id="a15b4-642">は、.NET 4.0 のクエリプランのキャッシュをサポートしています (プランキャッシュは、.NET 4.5 の他のすべての種類のクエリでサポートされています)。</span><span class="sxs-lookup"><span data-stu-id="a15b4-642">Supports query plan caching in .NET 4.0 (plan caching is supported by all other query types in .NET 4.5).</span></span>

<span data-ttu-id="a15b4-643">**短所**</span><span class="sxs-lookup"><span data-stu-id="a15b4-643">**Cons**</span></span>

-   <span data-ttu-id="a15b4-644">には、言語に組み込まれているクエリの構造よりもユーザーエラーが発生しやすいテキストクエリ文字列が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-644">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>
-   <span data-ttu-id="a15b4-645">CUD 操作には適していません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-645">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="a15b4-646">結果は自動的に具体化されず、データリーダーから読み取る必要があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-646">Results are not automatically materialized, and must be read from the data reader.</span></span>

### <a name="65-sqlquery-and-executestorequery"></a><span data-ttu-id="a15b4-647">6.5 SqlQuery と ExecuteStoreQuery</span><span class="sxs-lookup"><span data-stu-id="a15b4-647">6.5       SqlQuery and ExecuteStoreQuery</span></span>

<span data-ttu-id="a15b4-648">データベースに対する SqlQuery:</span><span class="sxs-lookup"><span data-stu-id="a15b4-648">SqlQuery on Database:</span></span>

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

<span data-ttu-id="a15b4-649">DbSet に対する SqlQuery:</span><span class="sxs-lookup"><span data-stu-id="a15b4-649">SqlQuery on DbSet:</span></span>

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

<span data-ttu-id="a15b4-650">ExecyteStoreQuery:</span><span class="sxs-lookup"><span data-stu-id="a15b4-650">ExecyteStoreQuery:</span></span>

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

<span data-ttu-id="a15b4-651">**長所**</span><span class="sxs-lookup"><span data-stu-id="a15b4-651">**Pros**</span></span>

-   <span data-ttu-id="a15b4-652">通常、plan コンパイラをバイパスした後のパフォーマンスが最速になります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-652">Generally fastest performance since plan compiler is bypassed.</span></span>
-   <span data-ttu-id="a15b4-653">完全に具体化したオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="a15b4-653">Fully materialized objects.</span></span>
-   <span data-ttu-id="a15b4-654">DbSet から使用する場合の CUD 操作に適しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-654">Suitable for CUD operations when used from the DbSet.</span></span>

<span data-ttu-id="a15b4-655">**短所**</span><span class="sxs-lookup"><span data-stu-id="a15b4-655">**Cons**</span></span>

-   <span data-ttu-id="a15b4-656">クエリはテキストであり、エラーが発生しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-656">Query is textual and error prone.</span></span>
-   <span data-ttu-id="a15b4-657">クエリは、概念セマンティクスではなくストアセマンティクスを使用して、特定のバックエンドに関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-657">Query is tied to a specific backend by using store semantics instead of conceptual semantics.</span></span>
-   <span data-ttu-id="a15b4-658">継承が存在する場合、handcrafted クエリは、要求された型のマッピング条件を考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-658">When inheritance is present, handcrafted query needs to account for mapping conditions for the type requested.</span></span>

### <a name="66-compiledquery"></a><span data-ttu-id="a15b4-659">6.6 CompiledQuery</span><span class="sxs-lookup"><span data-stu-id="a15b4-659">6.6       CompiledQuery</span></span>

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

<span data-ttu-id="a15b4-660">**長所**</span><span class="sxs-lookup"><span data-stu-id="a15b4-660">**Pros**</span></span>

-   <span data-ttu-id="a15b4-661">は、通常の LINQ クエリよりも最大7% のパフォーマンス向上を実現します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-661">Provides up to a 7% performance improvement over regular LINQ queries.</span></span>
-   <span data-ttu-id="a15b4-662">完全に具体化したオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="a15b4-662">Fully materialized objects.</span></span>
-   <span data-ttu-id="a15b4-663">CUD 操作に適しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-663">Suitable for CUD operations.</span></span>

<span data-ttu-id="a15b4-664">**短所**</span><span class="sxs-lookup"><span data-stu-id="a15b4-664">**Cons**</span></span>

-   <span data-ttu-id="a15b4-665">複雑さとプログラミングのオーバーヘッドが増加します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-665">Increased complexity and programming overhead.</span></span>
-   <span data-ttu-id="a15b4-666">コンパイルされたクエリの上に作成すると、パフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-666">The performance improvement is lost when composing on top of a compiled query.</span></span>
-   <span data-ttu-id="a15b4-667">一部の LINQ クエリは、匿名型の射影など、CompiledQuery として書き込むことはできません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-667">Some LINQ queries can't be written as a CompiledQuery - for example, projections of anonymous types.</span></span>

### <a name="67-performance-comparison-of-different-query-options"></a><span data-ttu-id="a15b4-668">6.7 さまざまなクエリオプションのパフォーマンス比較</span><span class="sxs-lookup"><span data-stu-id="a15b4-668">6.7       Performance Comparison of different query options</span></span>

<span data-ttu-id="a15b4-669">コンテキストの作成がタイムアウトしていない単純なマイクロベンチマークがテストに配置されました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-669">Simple microbenchmarks where the context creation was not timed were put to the test.</span></span> <span data-ttu-id="a15b4-670">管理された環境で、キャッシュされていないエンティティのセットに対して、5000回のクエリを実行することを測定しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-670">We measured querying 5000 times for a set of non-cached entities in a controlled environment.</span></span> <span data-ttu-id="a15b4-671">これらの数値には、アプリケーションによって生成された実際の数値は反映されませんが、異なるクエリオプションを比較した場合に発生するパフォーマンスの差異の程度を正確に測定することができます。新しいコンテキストを作成するコストを除く、りんごからりんごまで。</span><span class="sxs-lookup"><span data-stu-id="a15b4-671">These numbers are to be taken with a warning: they do not reflect actual numbers produced by an application, but instead they are a very accurate measurement of how much of a performance difference there is when different querying options are compared apples-to-apples, excluding the cost of creating a new context.</span></span>

| <span data-ttu-id="a15b4-672">EF</span><span class="sxs-lookup"><span data-stu-id="a15b4-672">EF</span></span>  | <span data-ttu-id="a15b4-673">テスト</span><span class="sxs-lookup"><span data-stu-id="a15b4-673">Test</span></span>                                 | <span data-ttu-id="a15b4-674">時間 (ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a15b4-674">Time (ms)</span></span> | <span data-ttu-id="a15b4-675">メモリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-675">Memory</span></span>   |
|:----|:-------------------------------------|:----------|:---------|
| <span data-ttu-id="a15b4-676">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-676">EF5</span></span> | <span data-ttu-id="a15b4-677">ObjectContext ESQL</span><span class="sxs-lookup"><span data-stu-id="a15b4-677">ObjectContext ESQL</span></span>                   | <span data-ttu-id="a15b4-678">2414</span><span class="sxs-lookup"><span data-stu-id="a15b4-678">2414</span></span>      | <span data-ttu-id="a15b4-679">38801408</span><span class="sxs-lookup"><span data-stu-id="a15b4-679">38801408</span></span> |
| <span data-ttu-id="a15b4-680">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-680">EF5</span></span> | <span data-ttu-id="a15b4-681">ObjectContext Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-681">ObjectContext Linq Query</span></span>             | <span data-ttu-id="a15b4-682">2692</span><span class="sxs-lookup"><span data-stu-id="a15b4-682">2692</span></span>      | <span data-ttu-id="a15b4-683">38277120</span><span class="sxs-lookup"><span data-stu-id="a15b4-683">38277120</span></span> |
| <span data-ttu-id="a15b4-684">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-684">EF5</span></span> | <span data-ttu-id="a15b4-685">DbContext Linq クエリの追跡なし</span><span class="sxs-lookup"><span data-stu-id="a15b4-685">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="a15b4-686">2818</span><span class="sxs-lookup"><span data-stu-id="a15b4-686">2818</span></span>      | <span data-ttu-id="a15b4-687">41840640</span><span class="sxs-lookup"><span data-stu-id="a15b4-687">41840640</span></span> |
| <span data-ttu-id="a15b4-688">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-688">EF5</span></span> | <span data-ttu-id="a15b4-689">DbContext Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-689">DbContext Linq Query</span></span>                 | <span data-ttu-id="a15b4-690">2930</span><span class="sxs-lookup"><span data-stu-id="a15b4-690">2930</span></span>      | <span data-ttu-id="a15b4-691">41771008</span><span class="sxs-lookup"><span data-stu-id="a15b4-691">41771008</span></span> |
| <span data-ttu-id="a15b4-692">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-692">EF5</span></span> | <span data-ttu-id="a15b4-693">ObjectContext Linq クエリの追跡なし</span><span class="sxs-lookup"><span data-stu-id="a15b4-693">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="a15b4-694">3013</span><span class="sxs-lookup"><span data-stu-id="a15b4-694">3013</span></span>      | <span data-ttu-id="a15b4-695">38412288</span><span class="sxs-lookup"><span data-stu-id="a15b4-695">38412288</span></span> |
|     |                                      |           |          |
| <span data-ttu-id="a15b4-696">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-696">EF6</span></span> | <span data-ttu-id="a15b4-697">ObjectContext ESQL</span><span class="sxs-lookup"><span data-stu-id="a15b4-697">ObjectContext ESQL</span></span>                   | <span data-ttu-id="a15b4-698">2059</span><span class="sxs-lookup"><span data-stu-id="a15b4-698">2059</span></span>      | <span data-ttu-id="a15b4-699">46039040</span><span class="sxs-lookup"><span data-stu-id="a15b4-699">46039040</span></span> |
| <span data-ttu-id="a15b4-700">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-700">EF6</span></span> | <span data-ttu-id="a15b4-701">ObjectContext Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-701">ObjectContext Linq Query</span></span>             | <span data-ttu-id="a15b4-702">3,074</span><span class="sxs-lookup"><span data-stu-id="a15b4-702">3074</span></span>      | <span data-ttu-id="a15b4-703">45248512</span><span class="sxs-lookup"><span data-stu-id="a15b4-703">45248512</span></span> |
| <span data-ttu-id="a15b4-704">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-704">EF6</span></span> | <span data-ttu-id="a15b4-705">DbContext Linq クエリの追跡なし</span><span class="sxs-lookup"><span data-stu-id="a15b4-705">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="a15b4-706">3125</span><span class="sxs-lookup"><span data-stu-id="a15b4-706">3125</span></span>      | <span data-ttu-id="a15b4-707">47575040</span><span class="sxs-lookup"><span data-stu-id="a15b4-707">47575040</span></span> |
| <span data-ttu-id="a15b4-708">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-708">EF6</span></span> | <span data-ttu-id="a15b4-709">DbContext Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-709">DbContext Linq Query</span></span>                 | <span data-ttu-id="a15b4-710">3420</span><span class="sxs-lookup"><span data-stu-id="a15b4-710">3420</span></span>      | <span data-ttu-id="a15b4-711">47652864</span><span class="sxs-lookup"><span data-stu-id="a15b4-711">47652864</span></span> |
| <span data-ttu-id="a15b4-712">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-712">EF6</span></span> | <span data-ttu-id="a15b4-713">ObjectContext Linq クエリの追跡なし</span><span class="sxs-lookup"><span data-stu-id="a15b4-713">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="a15b4-714">3593</span><span class="sxs-lookup"><span data-stu-id="a15b4-714">3593</span></span>      | <span data-ttu-id="a15b4-715">45260800</span><span class="sxs-lookup"><span data-stu-id="a15b4-715">45260800</span></span> |

![EF5 マイクロベンチマーク、5000ウォームイテレーション](~/ef6/media/ef5micro5000warm.png)

![EF6 マイクロベンチマーク、5000ウォームイテレーション](~/ef6/media/ef6micro5000warm.png)

<span data-ttu-id="a15b4-718">マイクロベンチマークは、コードの小さな変更に非常に敏感です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-718">Microbenchmarks are very sensitive to small changes in the code.</span></span> <span data-ttu-id="a15b4-719">この場合、Entity Framework 5 と Entity Framework 6 のコストの差は、[インターセプト](~/ef6/fundamentals/logging-and-interception.md)と[トランザクションの機能強化](~/ef6/saving/transactions.md)が追加されているためです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-719">In this case, the difference between the costs of Entity Framework 5 and Entity Framework 6 are due to the addition of [interception](~/ef6/fundamentals/logging-and-interception.md) and [transactional improvements](~/ef6/saving/transactions.md).</span></span> <span data-ttu-id="a15b4-720">ただし、これらのマイクロベンチマーク数値は、Entity Framework が何を行うかをごくわずかな部分に分割したものです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-720">These microbenchmarks numbers, however, are an amplified vision into a very small fragment of what Entity Framework does.</span></span> <span data-ttu-id="a15b4-721">ウォームクエリの実際のシナリオでは、Entity Framework 5 から Entity Framework 6 にアップグレードした場合、パフォーマンスが低下することはありません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-721">Real-world scenarios of warm queries should not see a performance regression when upgrading from Entity Framework 5 to Entity Framework 6.</span></span>

<span data-ttu-id="a15b4-722">さまざまなクエリオプションの実際のパフォーマンスを比較するために、別のクエリオプションを使用してカテゴリ名が "飲み物" であるすべての製品を選択する5つの個別のテストバリエーションを作成しました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-722">To compare the real-world performance of the different query options, we created 5 separate test variations where we use a different query option to select all products whose category name is "Beverages".</span></span> <span data-ttu-id="a15b4-723">各イテレーションには、コンテキストを作成するコストと、返されたすべてのエンティティを具体化するコストが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-723">Each iteration includes the cost of creating the context, and the cost of materializing all returned entities.</span></span> <span data-ttu-id="a15b4-724">10回のイテレーションは、時間が1000のイテレーションの合計を取得する前に、タイムアウトしません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-724">10 iterations are run untimed before taking the sum of 1000 timed iterations.</span></span> <span data-ttu-id="a15b4-725">表示される結果は、各テストの5回の実行で行われた実行の中央値です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-725">The results shown are the median run taken from 5 runs of each test.</span></span> <span data-ttu-id="a15b4-726">詳細については、テスト用のコードが含まれている付録 B を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-726">For more information, see Appendix B which includes the code for the test.</span></span>

| <span data-ttu-id="a15b4-727">EF</span><span class="sxs-lookup"><span data-stu-id="a15b4-727">EF</span></span>  | <span data-ttu-id="a15b4-728">テスト</span><span class="sxs-lookup"><span data-stu-id="a15b4-728">Test</span></span>                                        | <span data-ttu-id="a15b4-729">時間 (ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="a15b4-729">Time (ms)</span></span> | <span data-ttu-id="a15b4-730">メモリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-730">Memory</span></span>   |
|:----|:--------------------------------------------|:----------|:---------|
| <span data-ttu-id="a15b4-731">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-731">EF5</span></span> | <span data-ttu-id="a15b4-732">ObjectContext エンティティコマンド</span><span class="sxs-lookup"><span data-stu-id="a15b4-732">ObjectContext Entity Command</span></span>                | <span data-ttu-id="a15b4-733">621</span><span class="sxs-lookup"><span data-stu-id="a15b4-733">621</span></span>       | <span data-ttu-id="a15b4-734">39350272</span><span class="sxs-lookup"><span data-stu-id="a15b4-734">39350272</span></span> |
| <span data-ttu-id="a15b4-735">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-735">EF5</span></span> | <span data-ttu-id="a15b4-736">データベースに対する DbContext Sql クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-736">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="a15b4-737">825</span><span class="sxs-lookup"><span data-stu-id="a15b4-737">825</span></span>       | <span data-ttu-id="a15b4-738">37519360</span><span class="sxs-lookup"><span data-stu-id="a15b4-738">37519360</span></span> |
| <span data-ttu-id="a15b4-739">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-739">EF5</span></span> | <span data-ttu-id="a15b4-740">ObjectContext ストアクエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-740">ObjectContext Store Query</span></span>                   | <span data-ttu-id="a15b4-741">878</span><span class="sxs-lookup"><span data-stu-id="a15b4-741">878</span></span>       | <span data-ttu-id="a15b4-742">39460864</span><span class="sxs-lookup"><span data-stu-id="a15b4-742">39460864</span></span> |
| <span data-ttu-id="a15b4-743">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-743">EF5</span></span> | <span data-ttu-id="a15b4-744">ObjectContext Linq クエリの追跡なし</span><span class="sxs-lookup"><span data-stu-id="a15b4-744">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="a15b4-745">969</span><span class="sxs-lookup"><span data-stu-id="a15b4-745">969</span></span>       | <span data-ttu-id="a15b4-746">38293504</span><span class="sxs-lookup"><span data-stu-id="a15b4-746">38293504</span></span> |
| <span data-ttu-id="a15b4-747">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-747">EF5</span></span> | <span data-ttu-id="a15b4-748">オブジェクトクエリを使用した ObjectContext エンティティ Sql</span><span class="sxs-lookup"><span data-stu-id="a15b4-748">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="a15b4-749">1089</span><span class="sxs-lookup"><span data-stu-id="a15b4-749">1089</span></span>      | <span data-ttu-id="a15b4-750">38981632</span><span class="sxs-lookup"><span data-stu-id="a15b4-750">38981632</span></span> |
| <span data-ttu-id="a15b4-751">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-751">EF5</span></span> | <span data-ttu-id="a15b4-752">ObjectContext コンパイル済みクエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-752">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="a15b4-753">1099</span><span class="sxs-lookup"><span data-stu-id="a15b4-753">1099</span></span>      | <span data-ttu-id="a15b4-754">38682624</span><span class="sxs-lookup"><span data-stu-id="a15b4-754">38682624</span></span> |
| <span data-ttu-id="a15b4-755">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-755">EF5</span></span> | <span data-ttu-id="a15b4-756">ObjectContext Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-756">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="a15b4-757">1152</span><span class="sxs-lookup"><span data-stu-id="a15b4-757">1152</span></span>      | <span data-ttu-id="a15b4-758">38178816</span><span class="sxs-lookup"><span data-stu-id="a15b4-758">38178816</span></span> |
| <span data-ttu-id="a15b4-759">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-759">EF5</span></span> | <span data-ttu-id="a15b4-760">DbContext Linq クエリの追跡なし</span><span class="sxs-lookup"><span data-stu-id="a15b4-760">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="a15b4-761">1208</span><span class="sxs-lookup"><span data-stu-id="a15b4-761">1208</span></span>      | <span data-ttu-id="a15b4-762">41803776</span><span class="sxs-lookup"><span data-stu-id="a15b4-762">41803776</span></span> |
| <span data-ttu-id="a15b4-763">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-763">EF5</span></span> | <span data-ttu-id="a15b4-764">Dbcontext に対する DbContext Sql クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-764">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="a15b4-765">1414</span><span class="sxs-lookup"><span data-stu-id="a15b4-765">1414</span></span>      | <span data-ttu-id="a15b4-766">37982208</span><span class="sxs-lookup"><span data-stu-id="a15b4-766">37982208</span></span> |
| <span data-ttu-id="a15b4-767">EF5</span><span class="sxs-lookup"><span data-stu-id="a15b4-767">EF5</span></span> | <span data-ttu-id="a15b4-768">DbContext Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-768">DbContext Linq Query</span></span>                        | <span data-ttu-id="a15b4-769">1574</span><span class="sxs-lookup"><span data-stu-id="a15b4-769">1574</span></span>      | <span data-ttu-id="a15b4-770">41738240</span><span class="sxs-lookup"><span data-stu-id="a15b4-770">41738240</span></span> |
|     |                                             |           |          |
| <span data-ttu-id="a15b4-771">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-771">EF6</span></span> | <span data-ttu-id="a15b4-772">ObjectContext エンティティコマンド</span><span class="sxs-lookup"><span data-stu-id="a15b4-772">ObjectContext Entity Command</span></span>                | <span data-ttu-id="a15b4-773">480</span><span class="sxs-lookup"><span data-stu-id="a15b4-773">480</span></span>       | <span data-ttu-id="a15b4-774">47247360</span><span class="sxs-lookup"><span data-stu-id="a15b4-774">47247360</span></span> |
| <span data-ttu-id="a15b4-775">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-775">EF6</span></span> | <span data-ttu-id="a15b4-776">ObjectContext ストアクエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-776">ObjectContext Store Query</span></span>                   | <span data-ttu-id="a15b4-777">493</span><span class="sxs-lookup"><span data-stu-id="a15b4-777">493</span></span>       | <span data-ttu-id="a15b4-778">46739456</span><span class="sxs-lookup"><span data-stu-id="a15b4-778">46739456</span></span> |
| <span data-ttu-id="a15b4-779">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-779">EF6</span></span> | <span data-ttu-id="a15b4-780">データベースに対する DbContext Sql クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-780">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="a15b4-781">614</span><span class="sxs-lookup"><span data-stu-id="a15b4-781">614</span></span>       | <span data-ttu-id="a15b4-782">41607168</span><span class="sxs-lookup"><span data-stu-id="a15b4-782">41607168</span></span> |
| <span data-ttu-id="a15b4-783">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-783">EF6</span></span> | <span data-ttu-id="a15b4-784">ObjectContext Linq クエリの追跡なし</span><span class="sxs-lookup"><span data-stu-id="a15b4-784">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="a15b4-785">684</span><span class="sxs-lookup"><span data-stu-id="a15b4-785">684</span></span>       | <span data-ttu-id="a15b4-786">46333952</span><span class="sxs-lookup"><span data-stu-id="a15b4-786">46333952</span></span> |
| <span data-ttu-id="a15b4-787">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-787">EF6</span></span> | <span data-ttu-id="a15b4-788">オブジェクトクエリを使用した ObjectContext エンティティ Sql</span><span class="sxs-lookup"><span data-stu-id="a15b4-788">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="a15b4-789">767</span><span class="sxs-lookup"><span data-stu-id="a15b4-789">767</span></span>       | <span data-ttu-id="a15b4-790">48865280</span><span class="sxs-lookup"><span data-stu-id="a15b4-790">48865280</span></span> |
| <span data-ttu-id="a15b4-791">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-791">EF6</span></span> | <span data-ttu-id="a15b4-792">ObjectContext コンパイル済みクエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-792">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="a15b4-793">788</span><span class="sxs-lookup"><span data-stu-id="a15b4-793">788</span></span>       | <span data-ttu-id="a15b4-794">48467968</span><span class="sxs-lookup"><span data-stu-id="a15b4-794">48467968</span></span> |
| <span data-ttu-id="a15b4-795">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-795">EF6</span></span> | <span data-ttu-id="a15b4-796">DbContext Linq クエリの追跡なし</span><span class="sxs-lookup"><span data-stu-id="a15b4-796">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="a15b4-797">878</span><span class="sxs-lookup"><span data-stu-id="a15b4-797">878</span></span>       | <span data-ttu-id="a15b4-798">47554560</span><span class="sxs-lookup"><span data-stu-id="a15b4-798">47554560</span></span> |
| <span data-ttu-id="a15b4-799">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-799">EF6</span></span> | <span data-ttu-id="a15b4-800">ObjectContext Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-800">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="a15b4-801">953</span><span class="sxs-lookup"><span data-stu-id="a15b4-801">953</span></span>       | <span data-ttu-id="a15b4-802">47632384</span><span class="sxs-lookup"><span data-stu-id="a15b4-802">47632384</span></span> |
| <span data-ttu-id="a15b4-803">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-803">EF6</span></span> | <span data-ttu-id="a15b4-804">Dbcontext に対する DbContext Sql クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-804">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="a15b4-805">1023</span><span class="sxs-lookup"><span data-stu-id="a15b4-805">1023</span></span>      | <span data-ttu-id="a15b4-806">41992192</span><span class="sxs-lookup"><span data-stu-id="a15b4-806">41992192</span></span> |
| <span data-ttu-id="a15b4-807">EF6</span><span class="sxs-lookup"><span data-stu-id="a15b4-807">EF6</span></span> | <span data-ttu-id="a15b4-808">DbContext Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-808">DbContext Linq Query</span></span>                        | <span data-ttu-id="a15b4-809">1290</span><span class="sxs-lookup"><span data-stu-id="a15b4-809">1290</span></span>      | <span data-ttu-id="a15b4-810">47529984</span><span class="sxs-lookup"><span data-stu-id="a15b4-810">47529984</span></span> |


![EF5 ウォームクエリ1000イテレーション](~/ef6/media/ef5warmquery1000.png)

![EF6 ウォームクエリ1000イテレーション](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> <span data-ttu-id="a15b4-813">完全を期すために、EntityCommand に対してクエリ Entity SQL を実行するバリエーションを含めています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-813">For completeness, we included a variation where we execute an Entity SQL query on an EntityCommand.</span></span> <span data-ttu-id="a15b4-814">ただし、このようなクエリの結果は具体化されていないため、比較は必ずしも公平なものではありません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-814">However, because results are not materialized for such queries, the comparison isn't necessarily apples-to-apples.</span></span> <span data-ttu-id="a15b4-815">このテストには、比較の fairer を試すための、具体化の近似値が含まれています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-815">The test includes a close approximation to materializing to try making the comparison fairer.</span></span>

<span data-ttu-id="a15b4-816">このエンドツーエンドのケースでは、スタックのいくつかの部分に対してパフォーマンスが向上したため、Entity Framework 6 は 5 Entity Framework になります。これには、DbContext の初期化と&gt; 参照の&lt;高速化が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-816">In this end-to-end case, Entity Framework 6 outperforms Entity Framework 5 due to performance improvements made on several parts of the stack, including a much lighter DbContext initialization and faster MetadataCollection&lt;T&gt; lookups.</span></span>

## <a name="7-design-time-performance-considerations"></a><span data-ttu-id="a15b4-817">7デザイン時のパフォーマンスに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="a15b4-817">7 Design time performance considerations</span></span>

### <a name="71-inheritance-strategies"></a><span data-ttu-id="a15b4-818">7.1 継承戦略</span><span class="sxs-lookup"><span data-stu-id="a15b4-818">7.1       Inheritance Strategies</span></span>

<span data-ttu-id="a15b4-819">Entity Framework を使用する場合のパフォーマンスに関するもう1つの考慮事項は、使用する継承方法です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-819">Another performance consideration when using Entity Framework is the inheritance strategy you use.</span></span> <span data-ttu-id="a15b4-820">Entity Framework では、3種類の基本的な継承とその組み合わせがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-820">Entity Framework supports 3 basic types of inheritance and their combinations:</span></span>

-   <span data-ttu-id="a15b4-821">階層あたりのテーブル (TPH) –各継承セットは、列で表されている階層内の特定の型を示す識別子列を持つテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-821">Table per Hierarchy (TPH) – where each inheritance set maps to a table with a discriminator column to indicate which particular type in the hierarchy is being represented in the row.</span></span>
-   <span data-ttu-id="a15b4-822">種類ごとのテーブル (TPT) –各型には、データベース内に独自のテーブルがあります。子テーブルは、親テーブルに含まれていない列のみを定義します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-822">Table per Type (TPT) – where each type has its own table in the database; the child tables only define the columns that the parent table doesn’t contain.</span></span>
-   <span data-ttu-id="a15b4-823">テーブルごとのテーブル (TPC) –各型には、データベース内に独自の完全なテーブルがあります。子テーブルは、親の型で定義されているものも含め、すべてのフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-823">Table per Class (TPC) – where each type has its own full table in the database; the child tables define all their fields, including those defined in parent types.</span></span>

<span data-ttu-id="a15b4-824">モデルで TPT 継承が使用されている場合、生成されるクエリは、他の継承戦略で生成されるクエリよりも複雑になります。そのため、ストアの実行時間が長くなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-824">If your model uses TPT inheritance, the queries which are generated will be more complex than those that are generated with the other inheritance strategies, which may result on longer execution times on the store.</span></span><span data-ttu-id="a15b4-825">  通常、TPT モデルに対してクエリを生成し、結果のオブジェクトを具体化するのに時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-825">  It will generally take longer to generate queries over a TPT model, and to materialize the resulting objects.</span></span>

<span data-ttu-id="a15b4-826">MSDN ブログの投稿「Entity Framework での TPT (テーブルあたりのテーブル数) の継承の使用時のパフォーマンスに関する考慮事項」 (\<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-826">See the "Performance Considerations when using TPT (Table per Type) Inheritance in the Entity Framework" MSDN blog post: \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>.</span></span>

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a><span data-ttu-id="a15b4-827">Model First または Code First アプリケーションの7.1.1 を回避する</span><span class="sxs-lookup"><span data-stu-id="a15b4-827">7.1.1       Avoiding TPT in Model First or Code First applications</span></span>

<span data-ttu-id="a15b4-828">TPT スキーマを持つ既存のデータベースに対してモデルを作成する場合、多くのオプションはありません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-828">When you create a model over an existing database that has a TPT schema, you don't have many options.</span></span> <span data-ttu-id="a15b4-829">ただし、Model First または Code First を使用してアプリケーションを作成する場合は、パフォーマンスの問題に対する TPT 継承を避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-829">But when creating an application using Model First or Code First, you should avoid TPT inheritance for performance concerns.</span></span>

<span data-ttu-id="a15b4-830">Entity Designer ウィザードで Model First を使用すると、モデル内のすべての継承について TPT が得られます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-830">When you use Model First in the Entity Designer Wizard, you will get TPT for any inheritance in your model.</span></span> <span data-ttu-id="a15b4-831">Model First で TPH 継承戦略に切り替える場合は、Visual Studio ギャラリー (\<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)から使用できる "Entity Designer データベース生成パワーパック" を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-831">If you want to switch to a TPH inheritance strategy with Model First, you can use the "Entity Designer Database Generation Power Pack" available from the Visual Studio Gallery ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>).</span></span>

<span data-ttu-id="a15b4-832">Code First を使用して、継承によってモデルのマッピングを構成する場合、EF では既定で TPH が使用されるため、継承階層内のすべてのエンティティが同じテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-832">When using Code First to configure the mapping of a model with inheritance, EF will use TPH by default, therefore all entities in the inheritance hierarchy will be mapped to the same table.</span></span> <span data-ttu-id="a15b4-833">詳細については、MSDN マガジンの「Code First in Entity Framework 4.1」 ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) の「Fluent API を使用したマッピング」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-833">See the "Mapping with the Fluent API" section of the "Code First in Entity Framework4.1" article in MSDN Magazine ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) for more details.</span></span>

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a><span data-ttu-id="a15b4-834">7.2 モデルの生成時間を向上させるための EF4 からのアップグレード</span><span class="sxs-lookup"><span data-stu-id="a15b4-834">7.2       Upgrading from EF4 to improve model generation time</span></span>

<span data-ttu-id="a15b4-835">モデルのストア層 (SSDL) を生成するアルゴリズムに対する SQL Server 固有の向上は、Visual Studio 2010 SP1 がインストールされている場合に Entity Framework 5 と6、および Entity Framework 4 の更新プログラムとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-835">A SQL Server-specific improvement to the algorithm that generates the store-layer (SSDL) of the model is available in Entity Framework 5 and 6, and as an update to Entity Framework 4 when Visual Studio 2010 SP1 is installed.</span></span> <span data-ttu-id="a15b4-836">次のテスト結果は、非常に大規模なモデル (この場合は Navision モデル) を生成するときの改善点を示しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-836">The following test results demonstrate the improvement when generating a very big model, in this case the Navision model.</span></span> <span data-ttu-id="a15b4-837">詳細については、「付録 C」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-837">See Appendix C for more details about it.</span></span>

<span data-ttu-id="a15b4-838">このモデルには、1005エンティティセットと4227アソシエーションセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-838">The model contains 1005 entity sets and 4227 association sets.</span></span>

| <span data-ttu-id="a15b4-839">構成</span><span class="sxs-lookup"><span data-stu-id="a15b4-839">Configuration</span></span>                              | <span data-ttu-id="a15b4-840">消費された時間の内訳</span><span class="sxs-lookup"><span data-stu-id="a15b4-840">Breakdown of time consumed</span></span>                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a15b4-841">Visual Studio 2010、Entity Framework 4</span><span class="sxs-lookup"><span data-stu-id="a15b4-841">Visual Studio 2010, Entity Framework 4</span></span>     | <span data-ttu-id="a15b4-842">SSDL Generation: 2 時間27分</span><span class="sxs-lookup"><span data-stu-id="a15b4-842">SSDL Generation: 2 hr 27 min</span></span> <br/> <span data-ttu-id="a15b4-843">マッピングの生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-843">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-844">CSDL の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-844">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-845">ObjectLayer の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-845">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-846">ビュー生成: 2 h 14 分</span><span class="sxs-lookup"><span data-stu-id="a15b4-846">View Generation: 2 h 14 min</span></span> |
| <span data-ttu-id="a15b4-847">Visual Studio 2010 SP1、Entity Framework 4</span><span class="sxs-lookup"><span data-stu-id="a15b4-847">Visual Studio 2010 SP1, Entity Framework 4</span></span> | <span data-ttu-id="a15b4-848">SSDL Generation: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-848">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-849">マッピングの生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-849">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-850">CSDL の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-850">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-851">ObjectLayer の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-851">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-852">ビューの生成: 1 時間53分</span><span class="sxs-lookup"><span data-stu-id="a15b4-852">View Generation: 1 hr 53 min</span></span>   |
| <span data-ttu-id="a15b4-853">Visual Studio 2013、Entity Framework 5</span><span class="sxs-lookup"><span data-stu-id="a15b4-853">Visual Studio 2013, Entity Framework 5</span></span>     | <span data-ttu-id="a15b4-854">SSDL Generation: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-854">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-855">マッピングの生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-855">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-856">CSDL の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-856">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-857">ObjectLayer の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-857">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-858">ビュー生成:65 分</span><span class="sxs-lookup"><span data-stu-id="a15b4-858">View Generation: 65 minutes</span></span>    |
| <span data-ttu-id="a15b4-859">Visual Studio 2013、Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="a15b4-859">Visual Studio 2013, Entity Framework 6</span></span>     | <span data-ttu-id="a15b4-860">SSDL Generation: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-860">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-861">マッピングの生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-861">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-862">CSDL の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-862">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-863">ObjectLayer の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="a15b4-863">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="a15b4-864">ビューの生成:28 秒。</span><span class="sxs-lookup"><span data-stu-id="a15b4-864">View Generation: 28 seconds.</span></span>   |


<span data-ttu-id="a15b4-865">SSDL を生成するときに、負荷はほぼすべて SQL Server で費やされていますが、クライアント開発マシンはサーバーから結果が返されるまで待機しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-865">It's worth noting that when generating the SSDL, the load is almost entirely spent on the SQL Server, while the client development machine is waiting idle for results to come back from the server.</span></span> <span data-ttu-id="a15b4-866">Dba は、特にこの改善に感謝します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-866">DBAs should particularly appreciate this improvement.</span></span> <span data-ttu-id="a15b4-867">また、基本的には、モデルの生成のコスト全体が、ビューの生成時に反映されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-867">It's also worth noting that essentially the entire cost of model generation takes place in View Generation now.</span></span>

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a><span data-ttu-id="a15b4-868">7.3 Database First と Model First を使用した大規模なモデルの分割</span><span class="sxs-lookup"><span data-stu-id="a15b4-868">7.3       Splitting Large Models with Database First and Model First</span></span>

<span data-ttu-id="a15b4-869">モデルのサイズが増加するにつれて、デザイナー画面が乱雑になり、使用が困難になります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-869">As model size increases, the designer surface becomes cluttered and difficult to use.</span></span> <span data-ttu-id="a15b4-870">通常、デザイナーを効果的に使用するには、300個を超えるエンティティを含むモデルが大きすぎると考えられます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-870">We typically consider a model with more than 300 entities to be too large to effectively use the designer.</span></span> <span data-ttu-id="a15b4-871">次のブログ記事では、大規模なモデルを分割するためのいくつかのオプション (\<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>について説明します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-871">The following blog post describes several options for splitting large models: \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>.</span></span>

<span data-ttu-id="a15b4-872">この投稿は Entity Framework の最初のバージョン用に書かれていますが、手順は引き続き適用されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-872">The post was written for the first version of Entity Framework, but the steps still apply.</span></span>

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a><span data-ttu-id="a15b4-873">7.4 Entity Data Source Control でのパフォーマンスに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="a15b4-873">7.4       Performance considerations with the Entity Data Source Control</span></span>

<span data-ttu-id="a15b4-874">EntityDataSource コントロールを使用する web アプリケーションのパフォーマンスが大幅に低下している、マルチスレッドのパフォーマンステストとストレステストのケースを見てきました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-874">We've seen cases in multi-threaded performance and stress tests where the performance of a web application using the EntityDataSource Control deteriorates significantly.</span></span> <span data-ttu-id="a15b4-875">根底にある原因は、EntityDataSource が、エンティティとして使用される型を検出するために、Web アプリケーションによって参照されるアセンブリに対して LoadFromAssembly を繰り返し呼び出すことです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-875">The underlying cause is that the EntityDataSource repeatedly calls MetadataWorkspace.LoadFromAssembly on the assemblies referenced by the Web application to discover the types to be used as entities.</span></span>

<span data-ttu-id="a15b4-876">解決策は、EntityDataSource の ContextTypeName を派生した ObjectContext クラスの型名に設定することです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-876">The solution is to set the ContextTypeName of the EntityDataSource to the type name of your derived ObjectContext class.</span></span> <span data-ttu-id="a15b4-877">これにより、参照されるすべてのアセンブリをエンティティ型に対してスキャンするメカニズムが無効になります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-877">This turns off the mechanism that scans all referenced assemblies for entity types.</span></span>

<span data-ttu-id="a15b4-878">また、[ContextTypeName] フィールドを設定すると、リフレクションを介してアセンブリから型を読み込むことができない場合に、.NET 4.0 の EntityDataSource が ReflectionTypeLoadException をスローする機能の問題も回避されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-878">Setting the ContextTypeName field also prevents a functional problem where the EntityDataSource in .NET 4.0 throws a ReflectionTypeLoadException when it can't load a type from an assembly via reflection.</span></span> <span data-ttu-id="a15b4-879">この問題は、.NET 4.5 で修正されました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-879">This issue has been fixed in .NET 4.5.</span></span>

### <a name="75-poco-entities-and-change-tracking-proxies"></a><span data-ttu-id="a15b4-880">7.5 POCO エンティティと変更追跡プロキシ</span><span class="sxs-lookup"><span data-stu-id="a15b4-880">7.5       POCO entities and change tracking proxies</span></span>

<span data-ttu-id="a15b4-881">Entity Framework を使用すると、データクラス自体を変更することなく、カスタムデータクラスをデータモデルと共に使用できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-881">Entity Framework enables you to use custom data classes together with your data model without making any modifications to the data classes themselves.</span></span> <span data-ttu-id="a15b4-882">つまり、既存のドメイン オブジェクトなどの POCO ("plain-old" CLR object) をデータ モデルで使用できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-882">This means that you can use "plain-old" CLR objects (POCO), such as existing domain objects, with your data model.</span></span> <span data-ttu-id="a15b4-883">これらの POCO データクラス (永続化非依存オブジェクトとも呼ばれます) は、データモデルで定義されているエンティティにマップされ、Entity Data Model ツールによって生成されるエンティティ型と同じクエリ、挿入、更新、および削除の動作のほとんどをサポートします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-883">These POCO data classes (also known as persistence-ignorant objects), which are mapped to entities that are defined in a data model, support most of the same query, insert, update, and delete behaviors as entity types that are generated by the Entity Data Model tools.</span></span>

<span data-ttu-id="a15b4-884">また Entity Framework は、POCO 型から派生したプロキシクラスを作成することもできます。これは、POCO エンティティに対する遅延読み込みや自動変更追跡などの機能を有効にする場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-884">Entity Framework can also create proxy classes derived from your POCO types, which are used when you want to enable features such as lazy loading and automatic change tracking on POCO entities.</span></span> <span data-ttu-id="a15b4-885">「 [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)」で説明されているように Entity Framework がプロキシを使用できるようにするには、POCO クラスが特定の要件を満たしている必要があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-885">Your POCO classes must meet certain requirements to allow Entity Framework to use proxies, as described here: [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx).</span></span>

<span data-ttu-id="a15b4-886">エンティティのいずれかのプロパティの値が変更されるたびに、追跡プロキシがオブジェクト状態マネージャーに通知します。そのため、Entity Framework エンティティの実際の状態が常に認識されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-886">Chance tracking proxies will notify the object state manager each time any of the properties of your entities has its value changed, so Entity Framework knows the actual state of your entities all the time.</span></span> <span data-ttu-id="a15b4-887">これを行うには、プロパティの setter メソッドの本体に通知イベントを追加し、オブジェクト状態マネージャーでそのようなイベントを処理します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-887">This is done by adding notification events to the body of the setter methods of your properties, and having the object state manager processing such events.</span></span> <span data-ttu-id="a15b4-888">プロキシエンティティの作成は、通常、Entity Framework によって作成されたイベントのセットによって、プロキシ以外の POCO エンティティを作成するよりもコストが高くなることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-888">Note that creating a proxy entity will typically be more expensive than creating a non-proxy POCO entity due to the added set of events created by Entity Framework.</span></span>

<span data-ttu-id="a15b4-889">POCO エンティティに変更追跡プロキシが設定されていない場合、エンティティのコンテンツと前回保存された状態のコピーとを比較することで、変更が検出されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-889">When a POCO entity does not have a change tracking proxy, changes are found by comparing the contents of your entities against a copy of a previous saved state.</span></span> <span data-ttu-id="a15b4-890">コンテキスト内に多数のエンティティがある場合、またはエンティティのプロパティの量が非常に多い場合、最後の比較が行われてから変更されていない場合でも、この詳細な比較は時間がかかるプロセスになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-890">This deep comparison will become a lengthy process when you have many entities in your context, or when your entities have a very large amount of properties, even if none of them changed since the last comparison took place.</span></span>

<span data-ttu-id="a15b4-891">まとめ: 変更追跡プロキシの作成時にパフォーマンスに影響を与えますが、変更の追跡機能を使用すると、エンティティに多数のプロパティがある場合、またはモデルに多数のエンティティがある場合に、変更の検出プロセスを高速化できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-891">In summary: you’ll pay a performance hit when creating the change tracking proxy, but change tracking will help you speed up the change detection process when your entities have many properties or when you have many entities in your model.</span></span> <span data-ttu-id="a15b4-892">エンティティの量があまり大きくならない、少数のプロパティを持つエンティティの場合は、変更追跡プロキシを使用した方がメリットがない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-892">For entities with a small number of properties where the amount of entities doesn’t grow too much, having change tracking proxies may not be of much benefit.</span></span>

## <a name="8-loading-related-entities"></a><span data-ttu-id="a15b4-893">8関連エンティティの読み込み</span><span class="sxs-lookup"><span data-stu-id="a15b4-893">8 Loading Related Entities</span></span>

### <a name="81-lazy-loading-vs-eager-loading"></a><span data-ttu-id="a15b4-894">8.1 遅延読み込みと一括読み込み</span><span class="sxs-lookup"><span data-stu-id="a15b4-894">8.1 Lazy Loading vs. Eager Loading</span></span>

<span data-ttu-id="a15b4-895">Entity Framework には、ターゲットエンティティに関連するエンティティを読み込むためのさまざまな方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-895">Entity Framework offers several different ways to load the entities that are related to your target entity.</span></span> <span data-ttu-id="a15b4-896">たとえば、製品のクエリを実行すると、関連する注文がオブジェクト状態マネージャーに読み込まれるさまざまな方法があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-896">For example, when you query for Products, there are different ways that the related Orders will be loaded into the Object State Manager.</span></span> <span data-ttu-id="a15b4-897">パフォーマンスの観点から、関連エンティティの読み込み時に考慮する最大の問題は、遅延読み込みと一括読み込みのどちらを使用するかということです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-897">From a performance standpoint, the biggest question to consider when loading related entities will be whether to use Lazy Loading or Eager Loading.</span></span>

<span data-ttu-id="a15b4-898">一括読み込みを使用する場合、関連エンティティはターゲットエンティティセットと共に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-898">When using Eager Loading, the related entities are loaded along with your target entity set.</span></span> <span data-ttu-id="a15b4-899">クエリで Include ステートメントを使用して、どの関連エンティティを取り込むかを指定します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-899">You use an Include statement in your query to indicate which related entities you want to bring in.</span></span>

<span data-ttu-id="a15b4-900">遅延読み込みを使用する場合、最初のクエリではターゲットエンティティセットのみが取り込まれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-900">When using Lazy Loading, your initial query only brings in the target entity set.</span></span> <span data-ttu-id="a15b4-901">ただし、ナビゲーションプロパティにアクセスするたびに、関連エンティティを読み込むために、ストアに対して別のクエリが発行されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-901">But whenever you access a navigation property, another query is issued against the store to load the related entity.</span></span>

<span data-ttu-id="a15b4-902">エンティティが読み込まれると、そのエンティティに対するその他のクエリは、遅延読み込みと一括読み込みのどちらを使用しているかにかかわらず、オブジェクト状態マネージャーから直接読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-902">Once an entity has been loaded, any further queries for the entity will load it directly from the Object State Manager, whether you are using lazy loading or eager loading.</span></span>

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a><span data-ttu-id="a15b4-903">8.2 遅延読み込みと一括読み込みを選択する方法</span><span class="sxs-lookup"><span data-stu-id="a15b4-903">8.2 How to choose between Lazy Loading and Eager Loading</span></span>

<span data-ttu-id="a15b4-904">重要なのは、アプリケーションに適切な選択を行うことができるように、遅延読み込みと一括読み込みの違いを理解することです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-904">The important thing is that you understand the difference between Lazy Loading and Eager Loading so that you can make the correct choice for your application.</span></span> <span data-ttu-id="a15b4-905">これは、データベースに対する複数の要求と、大きなペイロードを含む可能性のある単一の要求との間のトレードオフを評価するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-905">This will help you evaluate the tradeoff between multiple requests against the database versus a single request that may contain a large payload.</span></span> <span data-ttu-id="a15b4-906">アプリケーションの一部で一括読み込みを使用し、他の部分では遅延読み込みを使用することが適切な場合があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-906">It may be appropriate to use eager loading in some parts of your application and lazy loading in other parts.</span></span>

<span data-ttu-id="a15b4-907">内部で起こっていることの例として、英国に住んでいる顧客とその注文数についてクエリを実行するとします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-907">As an example of what's happening under the hood, suppose you want to query for the customers who live in the UK and their order count.</span></span>

<span data-ttu-id="a15b4-908">**一括読み込みの使用**</span><span class="sxs-lookup"><span data-stu-id="a15b4-908">**Using Eager Loading**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

<span data-ttu-id="a15b4-909">**遅延読み込みの使用**</span><span class="sxs-lookup"><span data-stu-id="a15b4-909">**Using Lazy Loading**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    context.ContextOptions.LazyLoadingEnabled = true;

    //Notice that the Include method call is missing in the query
    var ukCustomers = context.Customers.Where(c => c.Address.Country == "UK");

    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

<span data-ttu-id="a15b4-910">一括読み込みを使用する場合は、すべての顧客とすべての注文を返す単一のクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-910">When using eager loading, you'll issue a single query that returns all customers and all orders.</span></span> <span data-ttu-id="a15b4-911">ストアコマンドは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-911">The store command looks like:</span></span>

``` SQL
SELECT
[Project1].[C1] AS [C1],
[Project1].[CustomerID] AS [CustomerID],
[Project1].[CompanyName] AS [CompanyName],
[Project1].[ContactName] AS [ContactName],
[Project1].[ContactTitle] AS [ContactTitle],
[Project1].[Address] AS [Address],
[Project1].[City] AS [City],
[Project1].[Region] AS [Region],
[Project1].[PostalCode] AS [PostalCode],
[Project1].[Country] AS [Country],
[Project1].[Phone] AS [Phone],
[Project1].[Fax] AS [Fax],
[Project1].[C2] AS [C2],
[Project1].[OrderID] AS [OrderID],
[Project1].[CustomerID1] AS [CustomerID1],
[Project1].[EmployeeID] AS [EmployeeID],
[Project1].[OrderDate] AS [OrderDate],
[Project1].[RequiredDate] AS [RequiredDate],
[Project1].[ShippedDate] AS [ShippedDate],
[Project1].[ShipVia] AS [ShipVia],
[Project1].[Freight] AS [Freight],
[Project1].[ShipName] AS [ShipName],
[Project1].[ShipAddress] AS [ShipAddress],
[Project1].[ShipCity] AS [ShipCity],
[Project1].[ShipRegion] AS [ShipRegion],
[Project1].[ShipPostalCode] AS [ShipPostalCode],
[Project1].[ShipCountry] AS [ShipCountry]
FROM ( SELECT
      [Extent1].[CustomerID] AS [CustomerID],
       [Extent1].[CompanyName] AS [CompanyName],
       [Extent1].[ContactName] AS [ContactName],
       [Extent1].[ContactTitle] AS [ContactTitle],
       [Extent1].[Address] AS [Address],
       [Extent1].[City] AS [City],
       [Extent1].[Region] AS [Region],
       [Extent1].[PostalCode] AS [PostalCode],
       [Extent1].[Country] AS [Country],
       [Extent1].[Phone] AS [Phone],
       [Extent1].[Fax] AS [Fax],
      1 AS [C1],
       [Extent2].[OrderID] AS [OrderID],
       [Extent2].[CustomerID] AS [CustomerID1],
       [Extent2].[EmployeeID] AS [EmployeeID],
       [Extent2].[OrderDate] AS [OrderDate],
       [Extent2].[RequiredDate] AS [RequiredDate],
       [Extent2].[ShippedDate] AS [ShippedDate],
       [Extent2].[ShipVia] AS [ShipVia],
       [Extent2].[Freight] AS [Freight],
       [Extent2].[ShipName] AS [ShipName],
       [Extent2].[ShipAddress] AS [ShipAddress],
       [Extent2].[ShipCity] AS [ShipCity],
       [Extent2].[ShipRegion] AS [ShipRegion],
       [Extent2].[ShipPostalCode] AS [ShipPostalCode],
       [Extent2].[ShipCountry] AS [ShipCountry],
      CASE WHEN ([Extent2].[OrderID] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
      FROM  [dbo].[Customers] AS [Extent1]
      LEFT OUTER JOIN [dbo].[Orders] AS [Extent2] ON [Extent1].[CustomerID] = [Extent2].[CustomerID]
      WHERE N'UK' = [Extent1].[Country]
)  AS [Project1]
ORDER BY [Project1].[CustomerID] ASC, [Project1].[C2] ASC
```

<span data-ttu-id="a15b4-912">遅延読み込みを使用する場合は、最初に次のクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-912">When using lazy loading, you'll issue the following query initially:</span></span>

``` SQL
SELECT
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[CompanyName] AS [CompanyName],
[Extent1].[ContactName] AS [ContactName],
[Extent1].[ContactTitle] AS [ContactTitle],
[Extent1].[Address] AS [Address],
[Extent1].[City] AS [City],
[Extent1].[Region] AS [Region],
[Extent1].[PostalCode] AS [PostalCode],
[Extent1].[Country] AS [Country],
[Extent1].[Phone] AS [Phone],
[Extent1].[Fax] AS [Fax]
FROM [dbo].[Customers] AS [Extent1]
WHERE N'UK' = [Extent1].[Country]
```

<span data-ttu-id="a15b4-913">顧客の Orders ナビゲーションプロパティにアクセスするたびに、次のような別のクエリがストアに対して発行されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-913">And each time you access the Orders navigation property of a customer another query like the following is issued against the store:</span></span>

``` SQL
exec sp_executesql N'SELECT
[Extent1].[OrderID] AS [OrderID],
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[EmployeeID] AS [EmployeeID],
[Extent1].[OrderDate] AS [OrderDate],
[Extent1].[RequiredDate] AS [RequiredDate],
[Extent1].[ShippedDate] AS [ShippedDate],
[Extent1].[ShipVia] AS [ShipVia],
[Extent1].[Freight] AS [Freight],
[Extent1].[ShipName] AS [ShipName],
[Extent1].[ShipAddress] AS [ShipAddress],
[Extent1].[ShipCity] AS [ShipCity],
[Extent1].[ShipRegion] AS [ShipRegion],
[Extent1].[ShipPostalCode] AS [ShipPostalCode],
[Extent1].[ShipCountry] AS [ShipCountry]
FROM [dbo].[Orders] AS [Extent1]
WHERE [Extent1].[CustomerID] = @EntityKeyValue1',N'@EntityKeyValue1 nchar(5)',@EntityKeyValue1=N'AROUT'
```

<span data-ttu-id="a15b4-914">詳細については、「[関連オブジェクトの読み込み](https://msdn.microsoft.com/library/bb896272.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-914">For more information, see the [Loading Related Objects](https://msdn.microsoft.com/library/bb896272.aspx).</span></span>

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a><span data-ttu-id="a15b4-915">8.2.1 の遅延読み込みと一括読み込みのカンニングペーパー</span><span class="sxs-lookup"><span data-stu-id="a15b4-915">8.2.1 Lazy Loading versus Eager Loading cheat sheet</span></span>

<span data-ttu-id="a15b4-916">一括読み込みと遅延読み込みのどちらを選択した場合でも、そのようなことはありません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-916">There’s no such thing as a one-size-fits-all to choosing eager loading versus lazy loading.</span></span> <span data-ttu-id="a15b4-917">最初に両方の戦略の違いを理解して、十分な情報に基づいた意思決定を行うことができるようにします。また、コードが次のいずれかのシナリオに適合するかどうかを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-917">Try first to understand the differences between both strategies so you can do a well informed decision; also, consider if your code fits to any of the following scenarios:</span></span>

| <span data-ttu-id="a15b4-918">シナリオ</span><span class="sxs-lookup"><span data-stu-id="a15b4-918">Scenario</span></span>                                                                    | <span data-ttu-id="a15b4-919">提案</span><span class="sxs-lookup"><span data-stu-id="a15b4-919">Our Suggestion</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="a15b4-920">フェッチされたエンティティから多数のナビゲーションプロパティにアクセスする必要がありますか。</span><span class="sxs-lookup"><span data-stu-id="a15b4-920">Do you need to access many navigation properties from the fetched entities?</span></span> | <span data-ttu-id="a15b4-921">**いいえ**-両方のオプションが実行されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-921">**No** - Both options will probably do.</span></span> <span data-ttu-id="a15b4-922">ただし、クエリによって読み込まれるペイロードが大きすぎる場合は、一括読み込みを使用すると、オブジェクトを具体化するためのネットワークラウンドトリップが少なくなるため、パフォーマンスが向上する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-922">However, if the payload your query is bringing is not too big, you may experience performance benefits by using Eager loading as it’ll require less network round trips to materialize your objects.</span></span> <br/> <br/> <span data-ttu-id="a15b4-923">**はい**-エンティティから多数のナビゲーションプロパティにアクセスする必要がある場合は、一括読み込みを使用してクエリで複数の include ステートメントを使用します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-923">**Yes** -  If you need to access many navigation properties from the entities, you’d do that by using multiple include statements in your query with Eager loading.</span></span> <span data-ttu-id="a15b4-924">追加するエンティティが多いほど、クエリが返すペイロードが大きくなります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-924">The more entities you include, the bigger the payload your query will return.</span></span> <span data-ttu-id="a15b4-925">クエリに3つ以上のエンティティを含める場合は、遅延読み込みに切り替えることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-925">Once you include three or more entities into your query, consider switching to Lazy loading.</span></span> |
| <span data-ttu-id="a15b4-926">実行時にどのようなデータが必要になるかを正確に把握していますか。</span><span class="sxs-lookup"><span data-stu-id="a15b4-926">Do you know exactly what data will be needed at run time?</span></span>                   | <span data-ttu-id="a15b4-927">遅延読み込みは適切ではあり**ません**。</span><span class="sxs-lookup"><span data-stu-id="a15b4-927">**No** - Lazy loading will be better for you.</span></span> <span data-ttu-id="a15b4-928">そうしないと、不要なデータのクエリを終了する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-928">Otherwise, you may end up querying for data that you will not need.</span></span> <br/> <br/> <span data-ttu-id="a15b4-929">**はい**-一括読み込みが最適な方法です。これにより、セット全体を高速に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-929">**Yes** - Eager loading is probably your best bet; it will help loading entire sets faster.</span></span> <span data-ttu-id="a15b4-930">クエリで膨大な量のデータをフェッチする必要があり、それが遅すぎる場合は、代わりに遅延読み込みを試してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-930">If your query requires fetching a very large amount of data, and this becomes too slow, then try Lazy loading instead.</span></span>                                                                                                                                                                                                                                                       |
| <span data-ttu-id="a15b4-931">コードはデータベースから遠く実行されていますか?</span><span class="sxs-lookup"><span data-stu-id="a15b4-931">Is your code executing far from your database?</span></span> <span data-ttu-id="a15b4-932">(ネットワーク待機時間の増加)</span><span class="sxs-lookup"><span data-stu-id="a15b4-932">(increased network latency)</span></span>  | <span data-ttu-id="a15b4-933">**いいえ**-ネットワーク待機時間が問題にならない場合、遅延読み込みを使用するとコードが簡略化される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-933">**No** - When the network latency is not an issue, using Lazy loading may simplify your code.</span></span> <span data-ttu-id="a15b4-934">アプリケーションのトポロジが変更される可能性があることに注意してください。そのため、データベースの距離を許可しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-934">Remember that the topology of your application may change, so don’t take database proximity for granted.</span></span> <br/> <br/> <span data-ttu-id="a15b4-935">**はい**-ネットワークが問題になった場合は、お客様のシナリオに適したものを決定できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-935">**Yes** - When the network is a problem, only you can decide what fits better for your scenario.</span></span> <span data-ttu-id="a15b4-936">通常、一括読み込みは、必要なラウンドトリップが少なくなるため、より適切になります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-936">Typically Eager loading will be better because it requires fewer round trips.</span></span>                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a><span data-ttu-id="a15b4-937">複数のインクルードに関する8.2.2 のパフォーマンスの問題</span><span class="sxs-lookup"><span data-stu-id="a15b4-937">8.2.2       Performance concerns with multiple Includes</span></span>

<span data-ttu-id="a15b4-938">サーバーの応答時間の問題に関連するパフォーマンスの質問がある場合、問題の原因は複数の Include ステートメントを使用したクエリであることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-938">When we hear performance questions that involve server response time problems, the source of the issue is frequently queries with multiple Include statements.</span></span> <span data-ttu-id="a15b4-939">クエリ内に関連エンティティを含めることは強力ですが、内部で何が起こっているのかを理解することが重要です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-939">While including related entities in a query is powerful, it's important to understand what's happening under the covers.</span></span>

<span data-ttu-id="a15b4-940">内部プランコンパイラを通じてストアコマンドを生成するために、複数の Include ステートメントが含まれているクエリでは、比較的長い時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-940">It takes a relatively long time for a query with multiple Include statements in it to go through our internal plan compiler to produce the store command.</span></span> <span data-ttu-id="a15b4-941">この時間の大半は、結果として得られるクエリの最適化に費やされます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-941">The majority of this time is spent trying to optimize the resulting query.</span></span> <span data-ttu-id="a15b4-942">生成されたストアコマンドには、マッピングに応じて、各インクルードの外部結合または共用体が含まれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-942">The generated store command will contain an Outer Join or Union for each Include, depending on your mapping.</span></span> <span data-ttu-id="a15b4-943">このようなクエリでは、1つのペイロードでデータベースから大規模に接続されたグラフを取り込むことができます。これにより、特に、複数のレベルのインクルードが使用されている場合などに、ペイロードに多くの冗長性がある場合に、帯域幅の問題が acerbate されます。一対多の方向の関連付け)。</span><span class="sxs-lookup"><span data-stu-id="a15b4-943">Queries like this will bring in large connected graphs from your database in a single payload, which will acerbate any bandwidth issues, especially when there is a lot of redundancy in the payload (for example, when multiple levels of Include are used to traverse associations in the one-to-many direction).</span></span>

<span data-ttu-id="a15b4-944">クエリの基になる TSQL にアクセスすることによってクエリが非常に大きなペイロードを返すケースを確認するには、ToTraceString を使用し、SQL Server Management Studio でストアコマンドを実行して、ペイロードサイズを確認します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-944">You can check for cases where your queries are returning excessively large payloads by accessing the underlying TSQL for the query by using ToTraceString and executing the store command in SQL Server Management Studio to see the payload size.</span></span> <span data-ttu-id="a15b4-945">このような場合は、必要なデータを取り込むために、クエリ内の Include ステートメントの数を減らすことができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-945">In such cases you can try to reduce the number of Include statements in your query to just bring in the data you need.</span></span> <span data-ttu-id="a15b4-946">または、クエリを次のように、より小さなサブクエリのシーケンスに分割することもできます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-946">Or you may be able to break your query into a smaller sequence of subqueries, for example:</span></span>

<span data-ttu-id="a15b4-947">**クエリを中断する前に、次のようにします。**</span><span class="sxs-lookup"><span data-stu-id="a15b4-947">**Before breaking the query:**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var customers = from c in context.Customers.Include(c => c.Orders)
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

<span data-ttu-id="a15b4-948">**クエリを中断した後、次のようになります。**</span><span class="sxs-lookup"><span data-stu-id="a15b4-948">**After breaking the query:**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var orders = from o in context.Orders
                 where o.Customer.LastName.StartsWith(lastNameParameter)
                 select o;

    orders.Load();

    var customers = from c in context.Customers
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

<span data-ttu-id="a15b4-949">これは、コンテキストが id 解決とアソシエーションの修正を自動的に実行するために必要な機能を使用しているため、追跡対象のクエリでのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-949">This will work only on tracked queries, as we are making use of the ability the context has to perform identity resolution and association fixup automatically.</span></span>

<span data-ttu-id="a15b4-950">遅延読み込みと同様に、より小さなペイロードに対するクエリが増えることになります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-950">As with lazy loading, the tradeoff will be more queries for smaller payloads.</span></span> <span data-ttu-id="a15b4-951">個々のプロパティのプロジェクションを使用して、各エンティティから必要なデータのみを明示的に選択することもできますが、この場合、エンティティは読み込まれません。更新はサポートされません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-951">You can also use projections of individual properties to explicitly select only the data you need from each entity, but you will not be loading entities in this case, and updates will not be supported.</span></span>

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a><span data-ttu-id="a15b4-952">8.2.3 プロパティの遅延読み込みを取得するための回避策</span><span class="sxs-lookup"><span data-stu-id="a15b4-952">8.2.3 Workaround to get lazy loading of properties</span></span>

<span data-ttu-id="a15b4-953">Entity Framework は、スカラープロパティまたは複合プロパティの遅延読み込みを現在サポートしていません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-953">Entity Framework currently doesn’t support lazy loading of scalar or complex properties.</span></span> <span data-ttu-id="a15b4-954">ただし、BLOB などのラージオブジェクトを含むテーブルがある場合は、テーブル分割を使用して、大きなプロパティを別のエンティティに分けることができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-954">However, in cases where you have a table that includes a large object such as a BLOB, you can use table splitting to separate the large properties into a separate entity.</span></span> <span data-ttu-id="a15b4-955">たとえば、varbinary photo 列を含む Product テーブルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-955">For example, suppose you have a Product table that includes a varbinary photo column.</span></span> <span data-ttu-id="a15b4-956">クエリでこのプロパティに頻繁にアクセスする必要がない場合は、テーブル分割を使用して、通常必要なエンティティの部分だけを取り込むことができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-956">If you don't frequently need to access this property in your queries, you can use table splitting to bring in only the parts of the entity that you normally need.</span></span> <span data-ttu-id="a15b4-957">製品の写真を表すエンティティは、明示的に必要なときにのみ読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-957">The entity representing the product photo will only be loaded when you explicitly need it.</span></span>

<span data-ttu-id="a15b4-958">テーブル分割を有効にする方法を示す適切なリソースは、Gil Fink の「Entity Framework でのテーブル分割」のブログ投稿「\<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-958">A good resource that shows how to enable table splitting is Gil Fink's "Table Splitting in Entity Framework" blog post: \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>.</span></span>

## <a name="9-other-considerations"></a><span data-ttu-id="a15b4-959">9その他の考慮事項</span><span class="sxs-lookup"><span data-stu-id="a15b4-959">9 Other considerations</span></span>

### <a name="91-server-garbage-collection"></a><span data-ttu-id="a15b4-960">9.1 サーバーのガベージコレクション</span><span class="sxs-lookup"><span data-stu-id="a15b4-960">9.1      Server Garbage Collection</span></span>

<span data-ttu-id="a15b4-961">ユーザーによっては、ガベージコレクターが適切に構成されていないときに必要な並列処理を制限するリソースの競合が発生することがあります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-961">Some users might experience resource contention that limits the parallelism they are expecting when the Garbage Collector is not properly configured.</span></span> <span data-ttu-id="a15b4-962">マルチスレッドのシナリオで EF を使用する場合、またはサーバー側のシステムに似たアプリケーションで EF を使用する場合は、必ずサーバーのガベージコレクションを有効にしてください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-962">Whenever EF is used in a multithreaded scenario, or in any application that resembles a server-side system, make sure to enable Server Garbage Collection.</span></span> <span data-ttu-id="a15b4-963">これは、アプリケーション構成ファイルの単純な設定を使用して行います。</span><span class="sxs-lookup"><span data-stu-id="a15b4-963">This is done via a simple setting in your application config file:</span></span>

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

<span data-ttu-id="a15b4-964">これにより、スレッドの競合を減らし、CPU の飽和状態のシナリオで最大30% のスループットを向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-964">This should decrease your thread contention and increase your throughput by up to 30% in CPU saturated scenarios.</span></span> <span data-ttu-id="a15b4-965">一般的に、アプリケーションがどのように動作するかは、常に、クラシックガベージコレクション (UI とクライアント側のシナリオに合わせて調整されます) とサーバーのガベージコレクションを使用してテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-965">In general terms, you should always test how your application behaves using the classic Garbage Collection (which is better tuned for UI and client side scenarios) as well as the Server Garbage Collection.</span></span>

### <a name="92-autodetectchanges"></a><span data-ttu-id="a15b4-966">9.2 自動検出の変更</span><span class="sxs-lookup"><span data-stu-id="a15b4-966">9.2      AutoDetectChanges</span></span>

<span data-ttu-id="a15b4-967">既に説明したように、オブジェクトキャッシュに多数のエンティティがある場合、Entity Framework によってパフォーマンスの問題が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-967">As mentioned earlier, Entity Framework might show performance issues when the object cache has many entities.</span></span> <span data-ttu-id="a15b4-968">Add、Remove、Find、Entry、SaveChanges などの特定の操作では、オブジェクトキャッシュのサイズに基づいて大量の CPU を消費する可能性がある、検出された変更への呼び出しをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-968">Certain operations, such as Add, Remove, Find, Entry and SaveChanges, trigger calls to DetectChanges which might consume a large amount of CPU based on how large the object cache has become.</span></span> <span data-ttu-id="a15b4-969">その理由は、オブジェクトキャッシュとオブジェクト状態マネージャーは、コンテキストに対して実行される各操作で可能な限り同期を維持しようとし、生成されたデータがさまざまなシナリオで適切であることが保証されるためです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-969">The reason for this is that the object cache and the object state manager try to stay as synchronized as possible on each operation performed to a context so that the produced data is guaranteed to be correct under a wide array of scenarios.</span></span>

<span data-ttu-id="a15b4-970">一般に、アプリケーションの有効期間全体に対して、Entity Framework の自動変更検出を有効にしておくことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-970">It is generally a good practice to leave Entity Framework’s automatic change detection enabled for the entire life of your application.</span></span> <span data-ttu-id="a15b4-971">CPU の使用率が高いためにシナリオが低下しており、その原因が検出された変更への呼び出しであることがプロファイルに示されている場合は、コードの重要な部分で Auto検出の変更を一時的に無効にすることを検討してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-971">If your scenario is being negatively affected by high CPU usage and your profiles indicate that the culprit is the call to DetectChanges, consider temporarily turning off AutoDetectChanges in the sensitive portion of your code:</span></span>

``` csharp
try
{
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    ...
}
finally
{
    context.Configuration.AutoDetectChangesEnabled = true;
}
```

<span data-ttu-id="a15b4-972">Auto検出の変更をオフにする前に、エンティティに対して行われた変更に関する特定の情報を追跡する機能が Entity Framework によって失われる可能性があることを理解しておくことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-972">Before turning off AutoDetectChanges, it’s good to understand that this might cause Entity Framework to lose its ability to track certain information about the changes that are taking place on the entities.</span></span> <span data-ttu-id="a15b4-973">正しく処理されないと、アプリケーションのデータの不整合が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-973">If handled incorrectly, this might cause data inconsistency on your application.</span></span> <span data-ttu-id="a15b4-974">Auto検出の変更を無効にする方法の詳細については、「\<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-974">For more information on turning off AutoDetectChanges, read \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>.</span></span>

### <a name="93-context-per-request"></a><span data-ttu-id="a15b4-975">9.3 要求ごとのコンテキスト</span><span class="sxs-lookup"><span data-stu-id="a15b4-975">9.3      Context per request</span></span>

<span data-ttu-id="a15b4-976">Entity Framework のコンテキストは、最適なパフォーマンスエクスペリエンスを提供するために、有効期間の短いインスタンスとして使用することを意図しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-976">Entity Framework’s contexts are meant to be used as short-lived instances in order to provide the most optimal performance experience.</span></span> <span data-ttu-id="a15b4-977">コンテキストは、存続期間が短く、破棄されると想定されます。そのため、非常に軽量でメタデータを可能な限り再利用するように実装されています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-977">Contexts are expected to be short lived and discarded, and as such have been implemented to be very lightweight and reutilize metadata whenever possible.</span></span> <span data-ttu-id="a15b4-978">Web シナリオでは、このことを念頭に置いて、1つの要求の期間を超えてコンテキストを保持しないことが重要です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-978">In web scenarios it’s important to keep this in mind and not have a context for more than the duration of a single request.</span></span> <span data-ttu-id="a15b4-979">同様に、web 以外のシナリオでは、Entity Framework でのさまざまなキャッシュレベルの理解に基づいてコンテキストを破棄する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-979">Similarly, in non-web scenarios, context should be discarded based on your understanding of the different levels of caching in the Entity Framework.</span></span> <span data-ttu-id="a15b4-980">一般に、アプリケーションの有効期間全体にわたってコンテキストインスタンスが存在しないようにする必要があります。また、スレッドごとのコンテキストと静的コンテキストについても考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-980">Generally speaking, one should avoid having a context instance throughout the life of the application, as well as contexts per thread and static contexts.</span></span>

### <a name="94-database-null-semantics"></a><span data-ttu-id="a15b4-981">9.4 データベースの null セマンティクス</span><span class="sxs-lookup"><span data-stu-id="a15b4-981">9.4      Database null semantics</span></span>

<span data-ttu-id="a15b4-982">Entity Framework 既定では、C\# null 比較セマンティクスを持つ SQL コードが生成されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-982">Entity Framework by default will generate SQL code that has C\# null comparison semantics.</span></span> <span data-ttu-id="a15b4-983">次のようなクエリがあるとします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-983">Consider the following example query:</span></span>

``` csharp
            int? categoryId = 7;
            int? supplierId = 8;
            decimal? unitPrice = 0;
            short? unitsInStock = 100;
            short? unitsOnOrder = 20;
            short? reorderLevel = null;

            var q = from p incontext.Products
                    wherep.Category.CategoryName == "Beverages"
                          || (p.CategoryID == categoryId
                                || p.SupplierID == supplierId
                                || p.UnitPrice == unitPrice
                                || p.UnitsInStock == unitsInStock
                                || p.UnitsOnOrder == unitsOnOrder
                                || p.ReorderLevel == reorderLevel)
                    select p;

            var r = q.ToList();
```

<span data-ttu-id="a15b4-984">この例では、"仕入先" や "UnitPrice" など、エンティティの null 許容型の変数と null 値を許容する変数を比較しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-984">In this example, we’re comparing a number of nullable variables against nullable properties on the entity, such as SupplierID and UnitPrice.</span></span> <span data-ttu-id="a15b4-985">このクエリに対して生成された SQL は、パラメーター値が列の値と同じかどうか、またはパラメーターと列の両方の値が null であるかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-985">The generated SQL for this query will ask if the parameter value is the same as the column value, or if both the parameter and the column values are null.</span></span> <span data-ttu-id="a15b4-986">これにより、データベースサーバーが null を処理する方法が非表示になり、異なるデータベースベンダーに対して一貫した C\# の null エクスペリエンスが提供されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-986">This will hide the way the database server handles nulls and will provide a consistent C\# null experience across different database vendors.</span></span> <span data-ttu-id="a15b4-987">一方、生成されるコードは少し複雑で、クエリの where ステートメント内の比較の量が大きくなると、適切に動作しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-987">On the other hand, the generated code is a bit convoluted and may not perform well when the amount of comparisons in the where statement of the query grows to a large number.</span></span>

<span data-ttu-id="a15b4-988">この状況に対処する方法の1つは、データベースの null セマンティクスを使用することです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-988">One way to deal with this situation is by using database null semantics.</span></span> <span data-ttu-id="a15b4-989">これは、データベースエンジンが null 値を処理する方法を公開するより単純な SQL が生成されるように Entity Framework なったため、C\# の null セマンティクスとは動作が異なる可能性があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-989">Note that this might potentially behave differently to the C\# null semantics since now Entity Framework will generate simpler SQL that exposes the way the database engine handles null values.</span></span> <span data-ttu-id="a15b4-990">データベースの null セマンティクスは、コンテキスト構成に対して1つの構成行を使用して、コンテキストごとにアクティブにすることができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-990">Database null semantics can be activated per-context with one single configuration line against the context configuration:</span></span>

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

<span data-ttu-id="a15b4-991">小規模から中規模のクエリでは、データベースの null セマンティクスを使用しても、出ることのパフォーマンスが向上することはありませんが、大きな null 比較が多数あるクエリでは、この違いがより顕著になります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-991">Small to medium sized queries will not display a perceptible performance improvement when using database null semantics, but the difference will become more noticeable on queries with a large number of potential null comparisons.</span></span>

<span data-ttu-id="a15b4-992">上記のクエリの例では、制御された環境で実行されているマイクロベンチマークでパフォーマンスの差が2% 未満でした。</span><span class="sxs-lookup"><span data-stu-id="a15b4-992">In the example query above, the performance difference was less than 2% in a microbenchmark running in a controlled environment.</span></span>

### <a name="95-async"></a><span data-ttu-id="a15b4-993">9.5 非同期</span><span class="sxs-lookup"><span data-stu-id="a15b4-993">9.5      Async</span></span>

<span data-ttu-id="a15b4-994">Entity Framework 6 では、.NET 4.5 以降で実行する場合の非同期操作のサポートが導入されました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-994">Entity Framework 6 introduced support of async operations when running on .NET 4.5 or later.</span></span> <span data-ttu-id="a15b4-995">ほとんどの場合、IO に関連する競合が発生するアプリケーションでは、非同期クエリ操作と保存操作の使用が最も効果的です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-995">For the most part, applications that have IO related contention will benefit the most from using asynchronous query and save operations.</span></span> <span data-ttu-id="a15b4-996">アプリケーションで IO の競合が発生しない場合は、非同期の使用によって同期的に実行され、同期呼び出しと同じ時間で結果が返されます。最悪の場合は、非同期タスクに実行を遅延し、追加の tim を追加するだけです。お客様のシナリオを完了します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-996">If your application does not suffer from IO contention, the use of async will, in the best cases, run synchronously and return the result in the same amount of time as a synchronous call, or in the worst case, simply defer execution to an asynchronous task and add extra time to the completion of your scenario.</span></span>

<span data-ttu-id="a15b4-997">非同期のプログラミング作業によってアプリケーションのパフォーマンスが向上するかどうかを判断する方法については、 [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-997">For information on how asynchronous programming work that will help you deciding if async will improve the performance of your application visit [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx).</span></span> <span data-ttu-id="a15b4-998">Entity Framework での非同期操作の使用の詳細については、「 [Async Query And Save](~/ef6/fundamentals/async.md
)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-998">For more information on the use of async operations on Entity Framework, see [Async Query and Save](~/ef6/fundamentals/async.md
).</span></span>

### <a name="96-ngen"></a><span data-ttu-id="a15b4-999">9.6 NGEN</span><span class="sxs-lookup"><span data-stu-id="a15b4-999">9.6      NGEN</span></span>

<span data-ttu-id="a15b4-1000">Entity Framework 6 は、.NET Framework の既定のインストールには付属していません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1000">Entity Framework 6 does not come in the default installation of .NET framework.</span></span> <span data-ttu-id="a15b4-1001">そのため、Entity Framework のアセンブリは既定では NGEN ではありません。つまり、すべての Entity Framework コードは、他の MSIL アセンブリと同じ JIT'ing コストの影響を受けます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1001">As such, the Entity Framework assemblies are not NGEN’d by default which means that all the Entity Framework code is subject to the same JIT’ing costs as any other MSIL assembly.</span></span> <span data-ttu-id="a15b4-1002">これにより、開発中に F5 キーを押しながら、運用環境でアプリケーションのコールド起動を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1002">This might degrade the F5 experience while developing and also the cold startup of your application in the production environments.</span></span> <span data-ttu-id="a15b4-1003">JIT'ing の CPU とメモリのコストを削減するために、必要に応じて Entity Framework のイメージを NGEN することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1003">In order to reduce the CPU and memory costs of JIT’ing it is advisable to NGEN the Entity Framework images as appropriate.</span></span> <span data-ttu-id="a15b4-1004">NGEN を使用して Entity Framework 6 の起動パフォーマンスを向上させる方法の詳細については、「 [ngen を使用した起動時のパフォーマンスの向上](~/ef6/fundamentals/performance/ngen.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1004">For more information on how to improve the startup performance of Entity Framework 6 with NGEN, see [Improving Startup Performance with NGen](~/ef6/fundamentals/performance/ngen.md).</span></span>

### <a name="97-code-first-versus-edmx"></a><span data-ttu-id="a15b4-1005">9.7 Code First と EDMX の比較</span><span class="sxs-lookup"><span data-stu-id="a15b4-1005">9.7      Code First versus EDMX</span></span>

<span data-ttu-id="a15b4-1006">概念モデル (オブジェクト)、ストレージスキーマ (データベース)、およびの間のマッピングをメモリ内で表現することによって、オブジェクト指向プログラミングとリレーショナルデータベース間のインピーダンス不一致の問題について Entity Framework します。回.</span><span class="sxs-lookup"><span data-stu-id="a15b4-1006">Entity Framework reasons about the impedance mismatch problem between object oriented programming and relational databases by having an in-memory representation of the conceptual model (the objects), the storage schema (the database) and a mapping between the two.</span></span> <span data-ttu-id="a15b4-1007">このメタデータは、Entity Data Model、または短期の EDM と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1007">This metadata is called an Entity Data Model, or EDM for short.</span></span> <span data-ttu-id="a15b4-1008">この EDM からは、Entity Framework によって、メモリ内のオブジェクトからデータベースおよびデータベースへのラウンドトリップデータへのビューが導き出されます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1008">From this EDM, Entity Framework will derive the views to roundtrip data from the objects in memory to the database and back.</span></span>

<span data-ttu-id="a15b4-1009">概念モデル、ストレージスキーマ、およびマッピングを正式に指定する EDMX ファイルで Entity Framework を使用する場合、モデルの読み込み段階では、EDM が正しいことを検証する必要があります (たとえば、マッピングがないことを確認します)。ビューを生成し、ビューを検証して、このメタデータが使用できる状態になっていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1009">When Entity Framework is used with an EDMX file that formally specifies the conceptual model, the storage schema, and the mapping, then the model loading stage only has to validate that the EDM is correct (for example, make sure that no mappings are missing), then generate the views, then validate the views and have this metadata ready for use.</span></span> <span data-ttu-id="a15b4-1010">その後、クエリを実行したり、新しいデータをデータストアに保存したりできます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1010">Only then can a query be executed or new data be saved to the data store.</span></span>

<span data-ttu-id="a15b4-1011">Code First アプローチは、高度な Entity Data Model ジェネレーターです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1011">The Code First approach is, at its heart, a sophisticated Entity Data Model generator.</span></span> <span data-ttu-id="a15b4-1012">Entity Framework は、指定されたコードから EDM を生成する必要があります。そのためには、モデルに関連するクラスを分析し、規則を適用し、Fluent API を使用してモデルを構成します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1012">The Entity Framework has to produce an EDM from the provided code; it does so by analyzing the classes involved in the model, applying conventions and configuring the model via the Fluent API.</span></span> <span data-ttu-id="a15b4-1013">EDM がビルドされると、Entity Framework、プロジェクト内に EDMX ファイルが存在するのと同じように動作します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1013">After the EDM is built, the Entity Framework essentially behaves the same way as it would had an EDMX file been present in the project.</span></span> <span data-ttu-id="a15b4-1014">したがって、Code First からモデルを構築すると、EDMX を使用する場合と比較した場合に、Entity Framework の起動時間が長くなります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1014">Thus, building the model from Code First adds extra complexity that translates into a slower startup time for the Entity Framework when compared to having an EDMX.</span></span> <span data-ttu-id="a15b4-1015">コストは、構築されるモデルのサイズと複雑さに完全に依存しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1015">The cost is completely dependent on the size and complexity of the model that’s being built.</span></span>

<span data-ttu-id="a15b4-1016">EDMX と Code First の使用を選択する場合は、Code First によって導入された柔軟性によって、モデルを初めて構築するコストが高くなることを理解しておくことが重要です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1016">When choosing to use EDMX versus Code First, it’s important to know that the flexibility introduced by Code First increases the cost of building the model for the first time.</span></span> <span data-ttu-id="a15b4-1017">アプリケーションがこの初回読み込みのコストに耐えられる場合は、通常、Code First をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1017">If your application can withstand the cost of this first-time load then typically Code First will be the preferred way to go.</span></span>

## <a name="10-investigating-performance"></a><span data-ttu-id="a15b4-1018">10パフォーマンスの調査</span><span class="sxs-lookup"><span data-stu-id="a15b4-1018">10 Investigating Performance</span></span>

### <a name="101-using-the-visual-studio-profiler"></a><span data-ttu-id="a15b4-1019">10.1 Visual Studio Profiler の使用</span><span class="sxs-lookup"><span data-stu-id="a15b4-1019">10.1 Using the Visual Studio Profiler</span></span>

<span data-ttu-id="a15b4-1020">Entity Framework にパフォーマンス上の問題が発生している場合は、Visual Studio に組み込まれているもののようなプロファイラーを使用して、アプリケーションが時間を費やしている場所を確認できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1020">If you are having performance issues with the Entity Framework, you can use a profiler like the one built into Visual Studio to see where your application is spending its time.</span></span> <span data-ttu-id="a15b4-1021">これは、"ADO.NET Entity Framework-パート 1" のパフォーマンスを調査するために使用したツールであり、Entity Framework がコールドクエリとウォームクエリの時間を費やす場所を示す http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>) \<ます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1021">This is the tool we used to generate the pie charts in the “Exploring the Performance of the ADO.NET Entity Framework - Part 1” blog post ( \<http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>) that show where Entity Framework spends its time during cold and warm queries.</span></span>

<span data-ttu-id="a15b4-1022">「Visual Studio 2010 Profiler を使用したプロファイリング Entity Framework」では、データとモデリングの顧客アドバイザリチームによって作成された、実際の例を示しています。この記事では、プロファイラーを使用してパフォーマンスの問題を調査する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1022">The "Profiling Entity Framework using the Visual Studio 2010 Profiler" blog post written by the Data and Modeling Customer Advisory Team shows a real-world example of how they used the profiler to investigate a performance problem.</span></span><span data-ttu-id="a15b4-1023">  http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>を \<します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1023">  \<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>.</span></span> <span data-ttu-id="a15b4-1024">この投稿は windows アプリケーション用に作成されました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1024">This post was written for a windows application.</span></span> <span data-ttu-id="a15b4-1025">Web アプリケーションをプロファイリングする必要がある場合は、Windows パフォーマンスレコーダー (WPR) ツールと Windows Performance Analyzer (WPA) ツールが Visual Studio からの作業よりも適切に機能する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1025">If you need to profile a web application the Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA) tools may work better than working from Visual Studio.</span></span> <span data-ttu-id="a15b4-1026">WPR と WPA は、windows アセスメント & amp; デプロイメントキット ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)) に含まれる Windows Performance Toolkit の一部です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1026">WPR and WPA are part of the Windows Performance Toolkit which is included with the Windows Assessment and Deployment Kit ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)).</span></span>

### <a name="102-applicationdatabase-profiling"></a><span data-ttu-id="a15b4-1027">10.2 アプリケーション/データベースプロファイリング</span><span class="sxs-lookup"><span data-stu-id="a15b4-1027">10.2 Application/Database profiling</span></span>

<span data-ttu-id="a15b4-1028">Visual Studio に組み込まれているプロファイラーなどのツールによって、アプリケーションの使用時間がわかります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1028">Tools like the profiler built into Visual Studio tell you where your application is spending time.</span></span><span data-ttu-id="a15b4-1029">  また、必要に応じて運用中または運用前のアプリケーションの動的分析を実行し、データベースアクセスの一般的な落とし穴やアンチパターンを検索する、別の種類のプロファイラーを使用できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1029">  Another type of profiler is available that performs dynamic analysis of your running application, either in production or pre-production depending on needs, and looks for common pitfalls and anti-patterns of database access.</span></span>

<span data-ttu-id="a15b4-1030">Entity Framework Profiler (\<http://efprof.com>) と ORMProfiler (\<http://ormprofiler.com>)の2つの使用可能なプロファイラーがあります。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1030">Two commercially available profilers are the Entity Framework Profiler ( \<http://efprof.com>) and ORMProfiler ( \<http://ormprofiler.com>).</span></span>

<span data-ttu-id="a15b4-1031">アプリケーションが Code First を使用する MVC アプリケーションの場合は、StackExchange の MiniProfiler を使用できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1031">If your application is an MVC application using Code First, you can use StackExchange's MiniProfiler.</span></span> <span data-ttu-id="a15b4-1032">Scott マン Selman は、このツールについて、\<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>についてのブログで説明しています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1032">Scott Hanselman describes this tool in his blog at: \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>.</span></span>

<span data-ttu-id="a15b4-1033">アプリケーションのデータベースアクティビティのプロファイリングの詳細については、「 [Entity Framework でのデータベースアクティビティのプロファイリング」と](https://msdn.microsoft.com/magazine/gg490349.aspx)いうジュリー LERMAN の MSDN マガジンの記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1033">For more information on profiling your application's database activity, see Julie Lerman's MSDN Magazine article titled [Profiling Database Activity in the Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).</span></span>

### <a name="103-database-logger"></a><span data-ttu-id="a15b4-1034">10.3 データベースロガー</span><span class="sxs-lookup"><span data-stu-id="a15b4-1034">10.3 Database logger</span></span>

<span data-ttu-id="a15b4-1035">Entity Framework 6 を使用している場合は、組み込みのログ記録機能の使用も検討してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1035">If you are using Entity Framework 6 also consider using the built-in logging functionality.</span></span> <span data-ttu-id="a15b4-1036">コンテキストのデータベースプロパティは、単純な1行構成を使用してアクティビティをログに記録するように指示できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1036">The Database property of the context can be instructed to log its activity via a simple one-line configuration:</span></span>

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

<span data-ttu-id="a15b4-1037">この例では、データベースアクティビティはコンソールに記録されますが、Log プロパティは、任意のアクション&lt;文字列&gt; デリゲートを呼び出すように構成できます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1037">In this example the database activity will be logged to the console, but the Log property can be configured to call any Action&lt;string&gt; delegate.</span></span>

<span data-ttu-id="a15b4-1038">再コンパイルせずにデータベースのログ記録を有効にし、Entity Framework 6.1 以降を使用している場合は、アプリケーションの web.config ファイルまたは app.config ファイルにインターセプターを追加することで、データベースのログ記録を有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1038">If you want to enable database logging without recompiling, and you are using Entity Framework 6.1 or later, you can do so by adding an interceptor in the web.config or app.config file of your application.</span></span>

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

<span data-ttu-id="a15b4-1039">再コンパイルせずにログを追加する方法の詳細については \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>を参照してください。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1039">For more information on how to add logging without recompiling go to \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>.</span></span>

## <a name="11-appendix"></a><span data-ttu-id="a15b4-1040">11付録</span><span class="sxs-lookup"><span data-stu-id="a15b4-1040">11 Appendix</span></span>

### <a name="111-a-test-environment"></a><span data-ttu-id="a15b4-1041">11.1 A. テスト環境</span><span class="sxs-lookup"><span data-stu-id="a15b4-1041">11.1 A. Test Environment</span></span>

<span data-ttu-id="a15b4-1042">この環境では、2コンピューターのセットアップを、クライアントアプリケーションとは別のコンピューター上のデータベースと共に使用します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1042">This environment uses a 2-machine setup with the database on a separate machine from the client application.</span></span> <span data-ttu-id="a15b4-1043">マシンは同じラックに存在するため、ネットワーク待機時間は比較的低く、単一コンピューター環境よりも現実的です。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1043">Machines are in the same rack, so network latency is relatively low, but more realistic than a single-machine environment.</span></span>

#### <a name="1111-app-server"></a><span data-ttu-id="a15b4-1044">11.1.1 アプリサーバー</span><span class="sxs-lookup"><span data-stu-id="a15b4-1044">11.1.1       App Server</span></span>

##### <a name="11111-software-environment"></a><span data-ttu-id="a15b4-1045">11.1.1.1 ソフトウェア環境</span><span class="sxs-lookup"><span data-stu-id="a15b4-1045">11.1.1.1      Software Environment</span></span>

-   <span data-ttu-id="a15b4-1046">Entity Framework 4 ソフトウェア環境</span><span class="sxs-lookup"><span data-stu-id="a15b4-1046">Entity Framework 4 Software Environment</span></span>
    -   <span data-ttu-id="a15b4-1047">OS 名: Windows Server 2008 R2 Enterprise SP1。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1047">OS Name: Windows Server 2008 R2 Enterprise SP1.</span></span>
    -   <span data-ttu-id="a15b4-1048">Visual Studio 2010 – Ultimate。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1048">Visual Studio 2010 – Ultimate.</span></span>
    -   <span data-ttu-id="a15b4-1049">Visual Studio 2010 SP1 (一部の比較でのみ)。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1049">Visual Studio 2010 SP1 (only for some comparisons).</span></span>
-   <span data-ttu-id="a15b4-1050">Entity Framework 5 および6ソフトウェア環境</span><span class="sxs-lookup"><span data-stu-id="a15b4-1050">Entity Framework 5 and 6 Software Environment</span></span>
    -   <span data-ttu-id="a15b4-1051">OS 名: Windows 8.1 Enterprise</span><span class="sxs-lookup"><span data-stu-id="a15b4-1051">OS Name: Windows 8.1 Enterprise</span></span>
    -   <span data-ttu-id="a15b4-1052">Visual Studio 2013 – Ultimate。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1052">Visual Studio 2013 – Ultimate.</span></span>

##### <a name="11112-hardware-environment"></a><span data-ttu-id="a15b4-1053">11.1.1.2 ハードウェア環境</span><span class="sxs-lookup"><span data-stu-id="a15b4-1053">11.1.1.2      Hardware Environment</span></span>

-   <span data-ttu-id="a15b4-1054">デュアルプロセッサ: Intel (R) Xeon (R) CPU L5520 W3530 @ 2.27 GHz、2261 Mhz8 GHz、4コア (s)、84論理プロセッサ。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1054">Dual Processor:     Intel(R) Xeon(R) CPU L5520 W3530 @ 2.27GHz, 2261 Mhz8 GHz, 4 Core(s), 84 Logical Processor(s).</span></span>
-   <span data-ttu-id="a15b4-1055">2412 GB RamRAM。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1055">2412 GB RamRAM.</span></span>
-   <span data-ttu-id="a15b4-1056">136 GB SCSI250GB SATA 7200 rpm GB/s ドライブを4つのパーティションに分割します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1056">136 GB SCSI250GB SATA 7200 rpm 3GB/s drive split into 4 partitions.</span></span>

#### <a name="1112-db-server"></a><span data-ttu-id="a15b4-1057">11.1.2 DB サーバー</span><span class="sxs-lookup"><span data-stu-id="a15b4-1057">11.1.2       DB server</span></span>

##### <a name="11121-software-environment"></a><span data-ttu-id="a15b4-1058">11.1.2.1 ソフトウェア環境</span><span class="sxs-lookup"><span data-stu-id="a15b4-1058">11.1.2.1      Software Environment</span></span>

-   <span data-ttu-id="a15b4-1059">OS 名: Windows Server 2008 R 28.1 Enterprise SP1。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1059">OS Name: Windows Server 2008 R28.1 Enterprise SP1.</span></span>
-   <span data-ttu-id="a15b4-1060">SQL Server 2008 R22012。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1060">SQL Server 2008 R22012.</span></span>

##### <a name="11122-hardware-environment"></a><span data-ttu-id="a15b4-1061">11.1.2.2 ハードウェア環境</span><span class="sxs-lookup"><span data-stu-id="a15b4-1061">11.1.2.2      Hardware Environment</span></span>

-   <span data-ttu-id="a15b4-1062">シングルプロセッサ: Intel (R) Xeon (R) CPU L5520 @ 2.27 GHz、2261 MhzES-1620 0 @ 3.60 GHz、4コア (s)、8個の論理プロセッサ。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1062">Single Processor: Intel(R) Xeon(R) CPU L5520  @ 2.27GHz, 2261 MhzES-1620 0 @ 3.60GHz, 4 Core(s), 8 Logical Processor(s).</span></span>
-   <span data-ttu-id="a15b4-1063">824 GB RamRAM。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1063">824 GB RamRAM.</span></span>
-   <span data-ttu-id="a15b4-1064">465 GB ATA500GB SATA 7200 rpm 6 GB/s ドライブを4つのパーティションに分割します。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1064">465 GB ATA500GB SATA 7200 rpm 6GB/s drive split into 4 partitions.</span></span>

### <a name="112-b-query-performance-comparison-tests"></a><span data-ttu-id="a15b4-1065">11.2 b. クエリパフォーマンス比較テスト</span><span class="sxs-lookup"><span data-stu-id="a15b4-1065">11.2      B. Query performance comparison tests</span></span>

<span data-ttu-id="a15b4-1066">Northwind モデルは、これらのテストを実行するために使用されました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1066">The Northwind model was used to execute these tests.</span></span> <span data-ttu-id="a15b4-1067">これは、Entity Framework デザイナーを使用してデータベースから生成されたものです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1067">It was generated from the database using the Entity Framework designer.</span></span> <span data-ttu-id="a15b4-1068">次に、クエリ実行オプションのパフォーマンスを比較するために、次のコードを使用しました。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1068">Then, the following code was used to compare the performance of the query execution options:</span></span>

``` csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Common;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Data.Objects;
using System.Linq;

namespace QueryComparison
{
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
                );

        public IQueryable<Product> InvokeProductsForCategoryCQ(string categoryName)
        {
            return productsForCategoryCQ(this, categoryName);
        }
    }

    public class QueryTypePerfComparison
    {
        private static string entityConnectionStr = @"metadata=res://*/Northwind.csdl|res://*/Northwind.ssdl|res://*/Northwind.msl;provider=System.Data.SqlClient;provider connection string='data source=.;initial catalog=Northwind;integrated security=True;multipleactiveresultsets=True;App=EntityFramework'";

        public void LINQIncludingContextCreation()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTracking()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                context.Products.MergeOption = MergeOption.NoTracking;

                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void CompiledQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                var q = context.InvokeProductsForCategoryCQ("Beverages");
                q.ToList();
            }
        }

        public void ObjectQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
                products.ToList();
            }
        }

        public void EntityCommand()
        {
            using (EntityConnection eConn = new EntityConnection(entityConnectionStr))
            {
                eConn.Open();
                EntityCommand cmd = eConn.CreateCommand();
                cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

                using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
                {
                    List<Product> productsList = new List<Product>();
                    while (reader.Read())
                    {
                        DbDataRecord record = (DbDataRecord)reader.GetValue(0);

                        // 'materialize' the product by accessing each field and value. Because we are materializing products, we won't have any nested data readers or records.
                        int fieldCount = record.FieldCount;

                        // Treat all products as Product, even if they are the subtype DiscontinuedProduct.
                        Product product = new Product();  

                        product.ProductID = record.GetInt32(0);
                        product.ProductName = record.GetString(1);
                        product.SupplierID = record.GetInt32(2);
                        product.CategoryID = record.GetInt32(3);
                        product.QuantityPerUnit = record.GetString(4);
                        product.UnitPrice = record.GetDecimal(5);
                        product.UnitsInStock = record.GetInt16(6);
                        product.UnitsOnOrder = record.GetInt16(7);
                        product.ReorderLevel = record.GetInt16(8);
                        product.Discontinued = record.GetBoolean(9);

                        productsList.Add(product);
                    }
                }
            }
        }

        public void ExecuteStoreQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectResult<Product> beverages = context.ExecuteStoreQuery<Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Database.SqlQuery\<QueryComparison.DbC.Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbSet()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Products.SqlQuery(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void LINQIncludingContextCreationDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTrackingDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var q = context.Products.AsNoTracking().Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }
    }
}
```

### <a name="113-c-navision-model"></a><span data-ttu-id="a15b4-1069">11.3 Navision モデル</span><span class="sxs-lookup"><span data-stu-id="a15b4-1069">11.3 C. Navision Model</span></span>

<span data-ttu-id="a15b4-1070">Navision データベースは、Microsoft Dynamics – NAV のデモに使用される大規模なデータベースです。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1070">The Navision database is a large database used to demo Microsoft Dynamics – NAV.</span></span> <span data-ttu-id="a15b4-1071">生成された概念モデルには、1005エンティティセットと4227アソシエーションセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1071">The generated conceptual model contains 1005 entity sets and 4227 association sets.</span></span> <span data-ttu-id="a15b4-1072">テストで使用されているモデルは "フラット" です。継承は追加されていません。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1072">The model used in the test is “flat” – no inheritance has been added to it.</span></span>

#### <a name="1131-queries-used-for-navision-tests"></a><span data-ttu-id="a15b4-1073">Navision テストに使用される11.3.1 クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-1073">11.3.1 Queries used for Navision tests</span></span>

<span data-ttu-id="a15b4-1074">Navision モデルで使用されるクエリの一覧には、Entity SQL クエリの3つのカテゴリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1074">The queries list used with the Navision model contains 3 categories of Entity SQL queries:</span></span>

##### <a name="11311-lookup"></a><span data-ttu-id="a15b4-1075">11.3.1.1 Lookup</span><span class="sxs-lookup"><span data-stu-id="a15b4-1075">11.3.1.1 Lookup</span></span>

<span data-ttu-id="a15b4-1076">集計のない単純な参照クエリ</span><span class="sxs-lookup"><span data-stu-id="a15b4-1076">A simple lookup query with no aggregations</span></span>

-   <span data-ttu-id="a15b4-1077">カウント: 16232</span><span class="sxs-lookup"><span data-stu-id="a15b4-1077">Count: 16232</span></span>
-   <span data-ttu-id="a15b4-1078">例:</span><span class="sxs-lookup"><span data-stu-id="a15b4-1078">Example:</span></span>

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a><span data-ttu-id="a15b4-1079">11.3.1.2 SingleAggregating</span><span class="sxs-lookup"><span data-stu-id="a15b4-1079">11.3.1.2 SingleAggregating</span></span>

<span data-ttu-id="a15b4-1080">複数の集計を含む通常の BI クエリですが、小計はありません (単一のクエリ)</span><span class="sxs-lookup"><span data-stu-id="a15b4-1080">A normal BI query with multiple aggregations, but no subtotals (single query)</span></span>

-   <span data-ttu-id="a15b4-1081">カウント: 2313</span><span class="sxs-lookup"><span data-stu-id="a15b4-1081">Count: 2313</span></span>
-   <span data-ttu-id="a15b4-1082">例:</span><span class="sxs-lookup"><span data-stu-id="a15b4-1082">Example:</span></span>

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

<span data-ttu-id="a15b4-1083">ここで、MDF\_SessionLogin\_Time\_Max () は、モデルで次のように定義されています。</span><span class="sxs-lookup"><span data-stu-id="a15b4-1083">Where MDF\_SessionLogin\_Time\_Max() is defined in the model as:</span></span>

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a><span data-ttu-id="a15b4-1084">11.3.1.3 集計の集計</span><span class="sxs-lookup"><span data-stu-id="a15b4-1084">11.3.1.3 AggregatingSubtotals</span></span>

<span data-ttu-id="a15b4-1085">集計と小計を含む BI クエリ (union all 経由)</span><span class="sxs-lookup"><span data-stu-id="a15b4-1085">A BI query with aggregations and subtotals (via union all)</span></span>

-   <span data-ttu-id="a15b4-1086">カウント: 178</span><span class="sxs-lookup"><span data-stu-id="a15b4-1086">Count: 178</span></span>
-   <span data-ttu-id="a15b4-1087">例:</span><span class="sxs-lookup"><span data-stu-id="a15b4-1087">Example:</span></span>

``` xml
  <Query complexity="AggregatingSubtotals">
    <CommandText>
using NavisionFK;
function AmountConsumed(entities Collection([CRONUS_International_Ltd__Zone])) as
(
    Edm.Sum(select value N.Block_Movement FROM entities as E, E.CRONUS_International_Ltd__Bin as N)
)
function AmountConsumed(P1 Edm.Int32) as
(
    AmountConsumed(select value e from NavisionFKContext.CRONUS_International_Ltd__Zone as e where e.Zone_Ranking = P1)
)
----------------------------------------------------------------------------------------------------------------------
(
    select top(10) Zone_Ranking, Cross_Dock_Bin_Zone, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking, E.Cross_Dock_Bin_Zone
)
union all
(
    select top(10) Zone_Ranking, Cast(null as Edm.Byte) as P2, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking
)
union all
{
    Row(Cast(null as Edm.Int32) as P1, Cast(null as Edm.Byte) as P2, AmountConsumed(select value E
                                                                         from NavisionFKContext.CRONUS_International_Ltd__Zone as E
                                                                         where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed))
}</CommandText>
    <Parameters>
      <Parameter Name="MinAmountConsumed" DbType="Int32" Value="10000" />
    </Parameters>
  </Query>
```
