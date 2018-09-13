---
title: EF4、EF5 と EF6 のパフォーマンスに関する考慮事項
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: a58461a6d18d9d53c002b5d45cecbff7b0cdf81e
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490260"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a><span data-ttu-id="cc8e7-102">EF 4、5、および 6 のパフォーマンスに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="cc8e7-102">Performance considerations for EF 4, 5, and 6</span></span>
<span data-ttu-id="cc8e7-103">David Obando、Eric Dettinger およびその他のユーザー</span><span class="sxs-lookup"><span data-stu-id="cc8e7-103">By David Obando, Eric Dettinger and others</span></span>

<span data-ttu-id="cc8e7-104">公開日: 2012 年 4 月</span><span class="sxs-lookup"><span data-stu-id="cc8e7-104">Published: April 2012</span></span>

<span data-ttu-id="cc8e7-105">最終更新日: 2014 年 5 月</span><span class="sxs-lookup"><span data-stu-id="cc8e7-105">Last updated: May 2014</span></span>

------------------------------------------------------------------------

## <a name="1-introduction"></a><span data-ttu-id="cc8e7-106">1.はじめに</span><span class="sxs-lookup"><span data-stu-id="cc8e7-106">1. Introduction</span></span>

<span data-ttu-id="cc8e7-107">オブジェクト リレーショナル マッピング フレームワークは、オブジェクト指向のアプリケーションでのデータ アクセスの抽象化を提供する便利な方法です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-107">Object-Relational Mapping frameworks are a convenient way to provide an abstraction for data access in an object-oriented application.</span></span> <span data-ttu-id="cc8e7-108">.NET アプリケーションでは、Microsoft は、O/RM Entity Framework は、お勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-108">For .NET applications, Microsoft's recommended O/RM is Entity Framework.</span></span> <span data-ttu-id="cc8e7-109">すべての抽象化、パフォーマンスになる可能性が問題になります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-109">With any abstraction though, performance can become a concern.</span></span>

<span data-ttu-id="cc8e7-110">このホワイト ペーパーでは、パフォーマンスに影響する Entity Framework の内部アルゴリズムのアイデアを開発者に提供し、調査のためのヒントを提供する Entity Framework を使用してアプリケーションを開発する際に、パフォーマンスに関する考慮事項を表示するのに書き込まれたとEntity Framework を使用するアプリケーションでのパフォーマンスを向上します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-110">This whitepaper was written to show the performance considerations when developing applications using Entity Framework, to give developers an idea of the Entity Framework internal algorithms that can affect performance, and to provide tips for investigation and improving performance in their applications that use Entity Framework.</span></span> <span data-ttu-id="cc8e7-111">多くのパフォーマンスに優れたトピック既に、web では、使用可能であれば、これらのリソースを指すしようとしたも。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-111">There are a number of good topics on performance already available on the web, and we've also tried pointing to these resources where possible.</span></span>

<span data-ttu-id="cc8e7-112">パフォーマンスは注意が必要です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-112">Performance is a tricky topic.</span></span> <span data-ttu-id="cc8e7-113">このホワイト ペーパーでは、リソースに役立つパフォーマンスを行う Entity Framework を使用するアプリケーションの決定に関連します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-113">This whitepaper is intended as a resource to help you make performance related decisions for your applications that use Entity Framework.</span></span> <span data-ttu-id="cc8e7-114">一部のパフォーマンスを示すためにテストのメトリックが含まれていますが、これらのメトリックは、アプリケーションに表示されるパフォーマンスの絶対インジケーターとして意図していません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-114">We have included some test metrics to demonstrate performance, but these metrics aren't intended as absolute indicators of the performance you will see in your application.</span></span>

<span data-ttu-id="cc8e7-115">実際には、このドキュメントには、Entity Framework 4、.NET 4.0 と Entity Framework 5 で実行され、6 は .NET 4.5 で動作が想定しています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-115">For practical purposes, this document assumes Entity Framework 4 is run under .NET 4.0 and Entity Framework 5 and 6 are run under .NET 4.5.</span></span> <span data-ttu-id="cc8e7-116">Entity Framework 5 のパフォーマンスの強化の多くは、.NET 4.5 に付属するコア コンポーネント内に存在します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-116">Many of the performance improvements made for Entity Framework 5 reside within the core components that ship with .NET 4.5.</span></span>

<span data-ttu-id="cc8e7-117">Entity Framework 6 は、帯域外リリース、.NET に付属する Entity Framework のコンポーネントに依存しません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-117">Entity Framework 6 is an out of band release and does not depend on the Entity Framework components that ship with .NET.</span></span> <span data-ttu-id="cc8e7-118">Entity Framework 6 では、.NET 4.0 と .NET 4.5 では、両方で動作し、.NET 4.0 からアップグレードしていないけれども、そのアプリケーション内で Entity Framework の最新の必要なものに大きなパフォーマンス上の利点を提供することができます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-118">Entity Framework 6 work on both .NET 4.0 and .NET 4.5, and can offer a big performance benefit to those who haven’t upgraded from .NET 4.0 but want the latest Entity Framework bits in their application.</span></span> <span data-ttu-id="cc8e7-119">この記事の執筆時に使用可能な最新バージョンを参照場合、このドキュメントでは、Entity Framework 6 をメンション、: バージョン 6.1.0 します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-119">When this document mentions Entity Framework 6, it refers to the latest version available at the time of this writing: version 6.1.0.</span></span>

## <a name="2-cold-vs-warm-query-execution"></a><span data-ttu-id="cc8e7-120">2.コールド vs します。ウォーム クエリの実行</span><span class="sxs-lookup"><span data-stu-id="cc8e7-120">2. Cold vs. Warm Query Execution</span></span>

<span data-ttu-id="cc8e7-121">任意のクエリが特定のモデルに対して行われた最初の時間はバック グラウンドで読み込むし、モデルを検証する作業の多くを Entity Framework。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-121">The very first time any query is made against a given model, the Entity Framework does a lot of work behind the scenes to load and validate the model.</span></span> <span data-ttu-id="cc8e7-122">頻繁にこの最初のクエリと呼ば「コールド」クエリ。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-122">We frequently refer to this first query as a "cold" query.</span></span>  <span data-ttu-id="cc8e7-123">既に読み込まれたモデルに対するクエリをさらには「ウォーム」のクエリと呼ばれ、はるかに速くなります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-123">Further queries against an already loaded model are known as "warm" queries, and are much faster.</span></span>

<span data-ttu-id="cc8e7-124">Entity Framework を使用してクエリを実行するときに時間を要した箇所の概要と Entity Framework 6 のものを改善する場所を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-124">Let’s take a high-level view of where time is spent when executing a query using Entity Framework, and see where things are improving in Entity Framework 6.</span></span>

<span data-ttu-id="cc8e7-125">**最初のクエリの実行 – コールド クエリ**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-125">**First Query Execution – cold query**</span></span>

| <span data-ttu-id="cc8e7-126">コードのユーザーの書き込み</span><span class="sxs-lookup"><span data-stu-id="cc8e7-126">Code User Writes</span></span>                                                                                     | <span data-ttu-id="cc8e7-127">アクション</span><span class="sxs-lookup"><span data-stu-id="cc8e7-127">Action</span></span>                    | <span data-ttu-id="cc8e7-128">EF4 パフォーマンスに与える影響</span><span class="sxs-lookup"><span data-stu-id="cc8e7-128">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="cc8e7-129">EF5 パフォーマンスに与える影響</span><span class="sxs-lookup"><span data-stu-id="cc8e7-129">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="cc8e7-130">EF6 パフォーマンスに与える影響</span><span class="sxs-lookup"><span data-stu-id="cc8e7-130">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="cc8e7-131">コンテキストの作成</span><span class="sxs-lookup"><span data-stu-id="cc8e7-131">Context creation</span></span>          | <span data-ttu-id="cc8e7-132">Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-132">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="cc8e7-133">Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-133">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="cc8e7-134">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-134">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="cc8e7-135">クエリ式の作成</span><span class="sxs-lookup"><span data-stu-id="cc8e7-135">Query expression creation</span></span> | <span data-ttu-id="cc8e7-136">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-136">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="cc8e7-137">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-137">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="cc8e7-138">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-138">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="cc8e7-139">LINQ クエリの実行</span><span class="sxs-lookup"><span data-stu-id="cc8e7-139">LINQ query execution</span></span>      | <span data-ttu-id="cc8e7-140">メタデータの読み込み: キャッシュですが高</span><span class="sxs-lookup"><span data-stu-id="cc8e7-140">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="cc8e7-141">-生成の表示: 可能性のある非常に高いが、キャッシュされました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-141">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="cc8e7-142">パラメーターの評価: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-142">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="cc8e7-143">-クエリの変換: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-143">- Query translation: Medium</span></span> <br/> <span data-ttu-id="cc8e7-144">実体化の生成: キャッシュされたが、中規模</span><span class="sxs-lookup"><span data-stu-id="cc8e7-144">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="cc8e7-145">-データベース クエリの実行: 可能性のある高</span><span class="sxs-lookup"><span data-stu-id="cc8e7-145">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="cc8e7-146">+ Connection.Open</span><span class="sxs-lookup"><span data-stu-id="cc8e7-146">+ Connection.Open</span></span> <br/> <span data-ttu-id="cc8e7-147">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="cc8e7-147">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="cc8e7-148">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="cc8e7-148">+ DataReader.Read</span></span> <br/> <span data-ttu-id="cc8e7-149">オブジェクトの具体化: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-149">Object materialization: Medium</span></span> <br/> <span data-ttu-id="cc8e7-150">Id 参照: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-150">- Identity lookup: Medium</span></span> | <span data-ttu-id="cc8e7-151">メタデータの読み込み: キャッシュですが高</span><span class="sxs-lookup"><span data-stu-id="cc8e7-151">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="cc8e7-152">-生成の表示: 可能性のある非常に高いが、キャッシュされました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-152">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="cc8e7-153">パラメーターを評価します低。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-153">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="cc8e7-154">-変換のクエリ: キャッシュされたが、中規模</span><span class="sxs-lookup"><span data-stu-id="cc8e7-154">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="cc8e7-155">実体化の生成: キャッシュされたが、中規模</span><span class="sxs-lookup"><span data-stu-id="cc8e7-155">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="cc8e7-156">-データベース クエリの実行: 度の高い (いくつかの状況でのクエリの向上)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-156">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="cc8e7-157">+ Connection.Open</span><span class="sxs-lookup"><span data-stu-id="cc8e7-157">+ Connection.Open</span></span> <br/> <span data-ttu-id="cc8e7-158">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="cc8e7-158">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="cc8e7-159">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="cc8e7-159">+ DataReader.Read</span></span> <br/> <span data-ttu-id="cc8e7-160">オブジェクトの具体化: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-160">Object materialization: Medium</span></span> <br/> <span data-ttu-id="cc8e7-161">Id 参照: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-161">- Identity lookup: Medium</span></span> | <span data-ttu-id="cc8e7-162">メタデータの読み込み: キャッシュですが高</span><span class="sxs-lookup"><span data-stu-id="cc8e7-162">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="cc8e7-163">-生成の表示: キャッシュされたが、中規模</span><span class="sxs-lookup"><span data-stu-id="cc8e7-163">- View generation: Medium but cached</span></span> <br/> <span data-ttu-id="cc8e7-164">パラメーターを評価します低。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-164">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="cc8e7-165">-変換のクエリ: キャッシュされたが、中規模</span><span class="sxs-lookup"><span data-stu-id="cc8e7-165">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="cc8e7-166">実体化の生成: キャッシュされたが、中規模</span><span class="sxs-lookup"><span data-stu-id="cc8e7-166">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="cc8e7-167">-データベース クエリの実行: 度の高い (いくつかの状況でのクエリの向上)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-167">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="cc8e7-168">+ Connection.Open</span><span class="sxs-lookup"><span data-stu-id="cc8e7-168">+ Connection.Open</span></span> <br/> <span data-ttu-id="cc8e7-169">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="cc8e7-169">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="cc8e7-170">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="cc8e7-170">+ DataReader.Read</span></span> <br/> <span data-ttu-id="cc8e7-171">オブジェクトの具体化: 中規模 (EF5 よりも高速)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-171">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="cc8e7-172">Id 参照: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-172">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="cc8e7-173">Connection.Close</span><span class="sxs-lookup"><span data-stu-id="cc8e7-173">Connection.Close</span></span>          | <span data-ttu-id="cc8e7-174">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-174">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="cc8e7-175">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-175">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="cc8e7-176">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-176">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


<span data-ttu-id="cc8e7-177">**2 番目のクエリの実行 – ウォーム クエリ**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-177">**Second Query Execution – warm query**</span></span>

| <span data-ttu-id="cc8e7-178">コードのユーザーの書き込み</span><span class="sxs-lookup"><span data-stu-id="cc8e7-178">Code User Writes</span></span>                                                                                     | <span data-ttu-id="cc8e7-179">アクション</span><span class="sxs-lookup"><span data-stu-id="cc8e7-179">Action</span></span>                    | <span data-ttu-id="cc8e7-180">EF4 パフォーマンスに与える影響</span><span class="sxs-lookup"><span data-stu-id="cc8e7-180">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="cc8e7-181">EF5 パフォーマンスに与える影響</span><span class="sxs-lookup"><span data-stu-id="cc8e7-181">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="cc8e7-182">EF6 パフォーマンスに与える影響</span><span class="sxs-lookup"><span data-stu-id="cc8e7-182">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="cc8e7-183">コンテキストの作成</span><span class="sxs-lookup"><span data-stu-id="cc8e7-183">Context creation</span></span>          | <span data-ttu-id="cc8e7-184">Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-184">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="cc8e7-185">Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-185">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="cc8e7-186">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-186">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="cc8e7-187">クエリ式の作成</span><span class="sxs-lookup"><span data-stu-id="cc8e7-187">Query expression creation</span></span> | <span data-ttu-id="cc8e7-188">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-188">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="cc8e7-189">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-189">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="cc8e7-190">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-190">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="cc8e7-191">LINQ クエリの実行</span><span class="sxs-lookup"><span data-stu-id="cc8e7-191">LINQ query execution</span></span>      | <span data-ttu-id="cc8e7-192">メタデータ~~読み込み~~参照:~~キャッシュですが高~~低</span><span class="sxs-lookup"><span data-stu-id="cc8e7-192">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="cc8e7-193">-表示~~生成~~参照:~~可能性のある非常に高いが、キャッシュされた~~低</span><span class="sxs-lookup"><span data-stu-id="cc8e7-193">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="cc8e7-194">パラメーターの評価: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-194">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="cc8e7-195">-クエリ~~翻訳~~参照: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-195">- Query ~~translation~~ lookup: Medium</span></span> <br/> <span data-ttu-id="cc8e7-196">実体化~~生成~~参照:~~キャッシュですが中程度~~低</span><span class="sxs-lookup"><span data-stu-id="cc8e7-196">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="cc8e7-197">-データベース クエリの実行: 可能性のある高</span><span class="sxs-lookup"><span data-stu-id="cc8e7-197">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="cc8e7-198">+ Connection.Open</span><span class="sxs-lookup"><span data-stu-id="cc8e7-198">+ Connection.Open</span></span> <br/> <span data-ttu-id="cc8e7-199">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="cc8e7-199">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="cc8e7-200">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="cc8e7-200">+ DataReader.Read</span></span> <br/> <span data-ttu-id="cc8e7-201">オブジェクトの具体化: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-201">Object materialization: Medium</span></span> <br/> <span data-ttu-id="cc8e7-202">Id 参照: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-202">- Identity lookup: Medium</span></span> | <span data-ttu-id="cc8e7-203">メタデータ~~読み込み~~参照:~~キャッシュですが高~~低</span><span class="sxs-lookup"><span data-stu-id="cc8e7-203">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="cc8e7-204">-表示~~生成~~参照:~~可能性のある非常に高いが、キャッシュされた~~低</span><span class="sxs-lookup"><span data-stu-id="cc8e7-204">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="cc8e7-205">パラメーターを評価します低。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-205">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="cc8e7-206">-クエリ~~翻訳~~参照:~~キャッシュですが中程度~~低</span><span class="sxs-lookup"><span data-stu-id="cc8e7-206">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="cc8e7-207">実体化~~生成~~参照:~~キャッシュですが中程度~~低</span><span class="sxs-lookup"><span data-stu-id="cc8e7-207">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="cc8e7-208">-データベース クエリの実行: 度の高い (いくつかの状況でのクエリの向上)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-208">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="cc8e7-209">+ Connection.Open</span><span class="sxs-lookup"><span data-stu-id="cc8e7-209">+ Connection.Open</span></span> <br/> <span data-ttu-id="cc8e7-210">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="cc8e7-210">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="cc8e7-211">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="cc8e7-211">+ DataReader.Read</span></span> <br/> <span data-ttu-id="cc8e7-212">オブジェクトの具体化: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-212">Object materialization: Medium</span></span> <br/> <span data-ttu-id="cc8e7-213">Id 参照: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-213">- Identity lookup: Medium</span></span> | <span data-ttu-id="cc8e7-214">メタデータ~~読み込み~~参照:~~キャッシュですが高~~低</span><span class="sxs-lookup"><span data-stu-id="cc8e7-214">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="cc8e7-215">-表示~~生成~~参照:~~キャッシュですが中程度~~低</span><span class="sxs-lookup"><span data-stu-id="cc8e7-215">- View ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="cc8e7-216">パラメーターを評価します低。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-216">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="cc8e7-217">-クエリ~~翻訳~~参照:~~キャッシュですが中程度~~低</span><span class="sxs-lookup"><span data-stu-id="cc8e7-217">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="cc8e7-218">実体化~~生成~~参照:~~キャッシュですが中程度~~低</span><span class="sxs-lookup"><span data-stu-id="cc8e7-218">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="cc8e7-219">-データベース クエリの実行: 度の高い (いくつかの状況でのクエリの向上)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-219">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="cc8e7-220">+ Connection.Open</span><span class="sxs-lookup"><span data-stu-id="cc8e7-220">+ Connection.Open</span></span> <br/> <span data-ttu-id="cc8e7-221">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="cc8e7-221">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="cc8e7-222">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="cc8e7-222">+ DataReader.Read</span></span> <br/> <span data-ttu-id="cc8e7-223">オブジェクトの具体化: 中規模 (EF5 よりも高速)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-223">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="cc8e7-224">Id 参照: Medium</span><span class="sxs-lookup"><span data-stu-id="cc8e7-224">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="cc8e7-225">Connection.Close</span><span class="sxs-lookup"><span data-stu-id="cc8e7-225">Connection.Close</span></span>          | <span data-ttu-id="cc8e7-226">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-226">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="cc8e7-227">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-227">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="cc8e7-228">Low</span><span class="sxs-lookup"><span data-stu-id="cc8e7-228">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


<span data-ttu-id="cc8e7-229">ウォームとコールドの両方のクエリのパフォーマンス コストを削減するいくつかの方法し、次のセクションでこれらを見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-229">There are several ways to reduce the performance cost of both cold and warm queries, and we'll take a look at these in the following section.</span></span> <span data-ttu-id="cc8e7-230">具体的には、モデル ビューの生成中に発生したパフォーマンスの問題を解消する必要がありますが、事前生成済みのビューを使用してコールド クエリで読み込みのコストの削減について説明します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-230">Specifically, we'll look at reducing the cost of model loading in cold queries by using pre-generated views, which should help alleviate performance pains experienced during view generation.</span></span> <span data-ttu-id="cc8e7-231">ウォーム クエリでは、クエリ プランのキャッシュ、なし、追跡クエリ、および別のクエリ実行オプションについて説明します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-231">For warm queries, we'll cover query plan caching, no tracking queries, and different query execution options.</span></span>

### <a name="21-what-is-view-generation"></a><span data-ttu-id="cc8e7-232">2.1 ビューの生成をとは</span><span class="sxs-lookup"><span data-stu-id="cc8e7-232">2.1 What is View Generation?</span></span>

<span data-ttu-id="cc8e7-233">どのようなビューを理解するために生成は、「ビューのマッピング」とはまず理解する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-233">In order to understand what view generation is, we must first understand what “Mapping Views” are.</span></span> <span data-ttu-id="cc8e7-234">マッピング ビューは、各エンティティ セットおよびアソシエーションのマッピングで指定されている変換の実行可能表現です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-234">Mapping Views are executable representations of the transformations specified in the mapping for each entity set and association.</span></span> <span data-ttu-id="cc8e7-235">内部的には、これらのマッピング ビューは、CQTs (正規のクエリ ツリー) の形状を実行します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-235">Internally, these mapping views take the shape of CQTs (canonical query trees).</span></span> <span data-ttu-id="cc8e7-236">2 種類のマッピング ビューがあります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-236">There are two types of mapping views:</span></span>

-   <span data-ttu-id="cc8e7-237">ビューに対してクエリを: これらは、データベース スキーマから概念モデルに移行するために必要な変換を表します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-237">Query views: these represent the transformation necessary to go from the database schema to the conceptual model.</span></span>
-   <span data-ttu-id="cc8e7-238">ビューを更新します。 これらは、概念モデルからデータベース スキーマに移行するために必要な変換を表します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-238">Update views: these represent the transformation necessary to go from the conceptual model to the database schema.</span></span>

<span data-ttu-id="cc8e7-239">概念モデルのさまざまな方法でデータベース スキーマが異なる場合があります留意してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-239">Keep in mind that the conceptual model might differ from the database schema in various ways.</span></span> <span data-ttu-id="cc8e7-240">たとえば、1 つ 1 つのテーブルを使用して、2 つの異なるエンティティ型のデータを格納する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-240">For example, one single table might be used to store the data for two different entity types.</span></span> <span data-ttu-id="cc8e7-241">継承と重要なマッピングは、マッピング ビューの複雑さの役割を果たします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-241">Inheritance and non-trivial mappings play a role in the complexity of the mapping views.</span></span>

<span data-ttu-id="cc8e7-242">マッピングの仕様に基づいてこれらのビューの計算処理は、ビューの生成と呼ばれるものです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-242">The process of computing these views based on the specification of the mapping is what we call view generation.</span></span> <span data-ttu-id="cc8e7-243">ビューの生成するかを実行できる動的にモデルが読み込まれるときに、またはビルド時に、「事前に生成されたビュー」; を使用して後者の場合は、c の Entity SQL ステートメントの形式でシリアル化する\#または VB ファイル。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-243">View generation can either take place dynamically when a model is loaded, or at build time, by using "pre-generated views"; the latter are serialized in the form of Entity SQL statements to a C\# or VB file.</span></span>

<span data-ttu-id="cc8e7-244">ビューが生成されると、それらも検証されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-244">When views are generated, they are also validated.</span></span> <span data-ttu-id="cc8e7-245">パフォーマンスの観点からは、ビューの生成のコストの大半は、これによって、エンティティ間の接続が合理的なられ、適切なカーディナリティのサポートされているすべての操作があるビューの検証実際にです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-245">From a performance standpoint, the vast majority of the cost of view generation is actually the validation of the views which ensures that the connections between the entities make sense and have the correct cardinality for all the supported operations.</span></span>

<span data-ttu-id="cc8e7-246">エンティティ セットに対するクエリを実行すると、クエリは、対応するクエリ ビューと結合されますと、この構成の結果がバッキング ストアが理解できるクエリの表現を作成するプランのコンパイラで処理実行します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-246">When a query over an entity set is executed, the query is combined with the corresponding query view, and the result of this composition is run through the plan compiler to create the representation of the query that the backing store can understand.</span></span> <span data-ttu-id="cc8e7-247">SQL Server では、このコンパイルの最終的な結果は、T-SQL の SELECT ステートメントになります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-247">For SQL Server, the final result of this compilation will be a T-SQL SELECT statement.</span></span> <span data-ttu-id="cc8e7-248">最初に、エンティティ セットに対する更新が実行されると、同様の手順にターゲット データベースの DML ステートメントに変換する更新プログラム ビューが実行されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-248">The first time an update over an entity set is performed, the update view is run through a similar process to transform it into DML statements for the target database.</span></span>

### <a name="22-factors-that-affect-view-generation-performance"></a><span data-ttu-id="cc8e7-249">2.2 ビューの生成のパフォーマンスに影響する要因を</span><span class="sxs-lookup"><span data-stu-id="cc8e7-249">2.2 Factors that affect View Generation performance</span></span>

<span data-ttu-id="cc8e7-250">ビューの生成のステップのパフォーマンスは、モデルのサイズでどのように相互接続されたモデルにもだけでなく依存します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-250">The performance of view generation step not only depends on the size of your model but also on how interconnected the model is.</span></span> <span data-ttu-id="cc8e7-251">2 つのエンティティが継承チェーンまたはアソシエーションを使用して接続している場合は接続されていると言います。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-251">If two Entities are connected via an inheritance chain or an Association, they are said to be connected.</span></span> <span data-ttu-id="cc8e7-252">同様に 2 つのテーブルを外部キーを使用して接続している場合は接続されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-252">Similarly if two tables are connected via a foreign key, they are connected.</span></span> <span data-ttu-id="cc8e7-253">接続されているエンティティと、スキーマ内のテーブルの数を増やすと、ビューの生成、コストが増えます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-253">As the number of connected Entities and tables in your schemas increase, the view generation cost increases.</span></span>

<span data-ttu-id="cc8e7-254">生成し、検証のビューを使用しているアルゴリズムはこれを改善するいくつかの最適化を使用して操作を行います最悪の場合、指数。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-254">The algorithm that we use to generate and validate views is exponential in the worst case, though we do use some optimizations to improve this.</span></span> <span data-ttu-id="cc8e7-255">パフォーマンスが低下すると思われる最大の要因は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-255">The biggest factors that seem to negatively affect performance are:</span></span>

-   <span data-ttu-id="cc8e7-256">エンティティの数とこれらのエンティティ間のアソシエーションの量を参照するモデルのサイズ。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-256">Model size, referring to the number of entities and the amount of associations between these entities.</span></span>
-   <span data-ttu-id="cc8e7-257">モデルの複雑さ、多くの型に関連する継承具体的には。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-257">Model complexity, specifically inheritance involving a large number of types.</span></span>
-   <span data-ttu-id="cc8e7-258">外部キー アソシエーションではなく独立アソシエーションでを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-258">Using Independent Associations, instead of Foreign Key Associations.</span></span>

<span data-ttu-id="cc8e7-259">小規模で単純なモデル、コストが事前生成済みのビューを使用することはありませんに十分小さい可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-259">For small, simple models the cost may be small enough to not bother using pre-generated views.</span></span> <span data-ttu-id="cc8e7-260">モデルのサイズと複雑さの増加、ビューの生成と検証のコストを削減する使用可能ないくつかのオプションがあります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-260">As model size and complexity increase, there are several options available to reduce the cost of view generation and validation.</span></span>

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a><span data-ttu-id="cc8e7-261">2.3 モデルを小さく Pre-Generated ビューを使用して読み込み時間</span><span class="sxs-lookup"><span data-stu-id="cc8e7-261">2.3 Using Pre-Generated Views to decrease model load time</span></span>

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools"></a><span data-ttu-id="cc8e7-262">2.3.1 Entity Framework Power Tools を使用して事前生成されたビュー</span><span class="sxs-lookup"><span data-stu-id="cc8e7-262">2.3.1 Pre-Generated views using the Entity Framework Power Tools</span></span>

<span data-ttu-id="cc8e7-263">Entity Framework Power Tools を使用して、モデルのクラス ファイルを右クリックし、「ビューの生成」を選択する Entity Framework メニューを使用して EDMX と Code First モデルのビューを生成することも検討できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-263">You can also consider using the Entity Framework Power Tools to generate views of EDMX and Code First models by right-clicking the model class file and using the Entity Framework menu to select “Generate Views”.</span></span> <span data-ttu-id="cc8e7-264">Entity Framework Power Tools が DbContext 派生コンテキストでのみ動作しをご覧ください\<http://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-264">The Entity Framework Power Tools work only on DbContext-derived contexts and can be found at \<http://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d>.</span></span>

<span data-ttu-id="cc8e7-265">Entity Framework 6 で事前生成済みのビューを使用する方法の詳細についてを参照してください。 [Pre-Generated マッピング ビュー](~/ef6/fundamentals/performance/pre-generated-views.md)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-265">For more information on how to use pre-generated views on Entity Framework 6 visit [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md).</span></span>

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a><span data-ttu-id="cc8e7-266">2.3.2 EDMGen によって作成されたモデルで事前に生成したビューを使用する方法</span><span class="sxs-lookup"><span data-stu-id="cc8e7-266">2.3.2 How to use Pre-generated views with a model created by EDMGen</span></span>

<span data-ttu-id="cc8e7-267">EDMGen は、.NET に付属し、機能が Entity Framework 4 および 5、Entity Framework 6 ではなくユーティリティです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-267">EDMGen is a utility that ships with .NET and works with Entity Framework 4 and 5, but not with Entity Framework 6.</span></span> <span data-ttu-id="cc8e7-268">EDMGen を使用して、コマンドラインから、モデル ファイルをオブジェクト レイヤー、およびビューを生成できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-268">EDMGen allows you to generate a model file, the object layer and the views from the command line.</span></span> <span data-ttu-id="cc8e7-269">VB または C の任意の言語でビュー ファイルである出力の 1 つは\#します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-269">One of the outputs will be a Views file in your language of choice, VB or C\#.</span></span> <span data-ttu-id="cc8e7-270">これは、各エンティティ セットの Entity SQL スニペットを含むコード ファイルです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-270">This is a code file containing Entity SQL snippets for each entity set.</span></span> <span data-ttu-id="cc8e7-271">事前生成済みのビューを有効にするのには、プロジェクトでファイルをインクルードだけです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-271">To enable pre-generated views, you simply include the file in your project.</span></span>

<span data-ttu-id="cc8e7-272">モデルのスキーマ ファイルに手動で編集を作成する場合は、ビュー ファイルを再生成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-272">If you manually make edits to the schema files for the model, you will need to re-generate the views file.</span></span> <span data-ttu-id="cc8e7-273">EDMGen を使用してこれを行う、 **/mode:ViewGeneration**フラグ。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-273">You can do this by running EDMGen with the **/mode:ViewGeneration** flag.</span></span>

<span data-ttu-id="cc8e7-274">さらに参照は、次を参照してください。[方法: クエリ パフォーマンスを向上させる Pre-Generate ビュー](https://msdn.microsoft.com/library/bb896240.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-274">For further reference, see [How to: Pre-Generate Views to Improve Query Performance](https://msdn.microsoft.com/library/bb896240.aspx).</span></span>

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a><span data-ttu-id="cc8e7-275">2.3.3 EDMX ファイルを Pre-Generated ビューを使用する方法</span><span class="sxs-lookup"><span data-stu-id="cc8e7-275">2.3.3 How to use Pre-Generated Views with an EDMX file</span></span>

<span data-ttu-id="cc8e7-276">EDMX ファイルのビューを生成する EDMGen を使用することもできます。 - 前述の MSDN トピックでは、- これを行うビルド前イベントを追加する方法を説明しますが、これは、複雑なとことができない場合がします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-276">You can also use EDMGen to generate views for an EDMX file - the previously referenced MSDN topic describes how to add a pre-build event to do this - but this is complicated and there are some cases where it isn't possible.</span></span> <span data-ttu-id="cc8e7-277">通常、モデルが edmx ファイルの場合、ビューを生成する T4 テンプレートを使用する方が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-277">It's generally easier to use a T4 template to generate the views when your model is in an edmx file.</span></span>

<span data-ttu-id="cc8e7-278">ADO.NET チームのブログが投稿をビューの生成に T4 テンプレートを使用する方法について説明します ( \<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-278">The ADO.NET team blog has a post that describes how to use a T4 template for view generation ( \<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>).</span></span> <span data-ttu-id="cc8e7-279">この投稿には、ダウンロードしてプロジェクトに追加できるテンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-279">This post includes a template that can be downloaded and added to your project.</span></span> <span data-ttu-id="cc8e7-280">テンプレートは、Entity Framework の最新バージョンで動作する保証はありませんが、Entity Framework の最初のバージョンとして記述されました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-280">The template was written for the first version of Entity Framework, so they aren’t guaranteed to work with the latest versions of Entity Framework.</span></span> <span data-ttu-id="cc8e7-281">ただし、ダウンロードできますビュー生成テンプレートの最新セット Entity Framework 4 と 5from、Visual Studio ギャラリー。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-281">However, you can download a more up-to-date set of view generation templates for Entity Framework 4 and 5from the Visual Studio Gallery:</span></span>

-   <span data-ttu-id="cc8e7-282">VB.NET の場合: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span><span class="sxs-lookup"><span data-stu-id="cc8e7-282">VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span></span>
-   <span data-ttu-id="cc8e7-283">C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span><span class="sxs-lookup"><span data-stu-id="cc8e7-283">C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span></span>

<span data-ttu-id="cc8e7-284">Entity Framework 6 を使用している場合は、ビューを取得できます、生成 T4 テンプレートで Visual Studio ギャラリーから\<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-284">If you’re using Entity Framework 6 you can get the view generation T4 templates from the Visual Studio Gallery at \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>.</span></span>

#### <a name="234-how-to-use-pre-generated-views-with-a-code-first-model"></a><span data-ttu-id="cc8e7-285">2.3.4 Code First モデルを Pre-Generated ビューを使用する方法</span><span class="sxs-lookup"><span data-stu-id="cc8e7-285">2.3.4 How to use Pre-Generated Views with a Code First model</span></span>

<span data-ttu-id="cc8e7-286">コードの最初のプロジェクトで事前に生成したビューを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-286">It's also possible to use pre-generated views with a Code First project.</span></span> <span data-ttu-id="cc8e7-287">Entity Framework Power Tools では、コードの最初のプロジェクト ビュー ファイルを生成する機能があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-287">The Entity Framework Power Tools has the ability to generate a views file for your Code First project.</span></span> <span data-ttu-id="cc8e7-288">Visual Studio ギャラリーで、Entity Framework Power Tools が見つかります\<http://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d/>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-288">You can find the Entity Framework Power Tools in the Visual Studio Gallery at \<http://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d/>.</span></span>

### <a name="24-reducing-the-cost-of-view-generation"></a><span data-ttu-id="cc8e7-289">2.4 ビューの生成のコストを削減します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-289">2.4 Reducing the cost of view generation</span></span>

<span data-ttu-id="cc8e7-290">事前生成済みのビューを使用したコンパイル時にモデルの読み込み (実行時) からビューの生成のコストを移動します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-290">Using pre-generated views moves the cost of view generation from model loading (run time) to compile time.</span></span> <span data-ttu-id="cc8e7-291">これにより、実行時に起動時のパフォーマンスが向上、中に開発する際にビューの生成の問題点を引き続き発生するされます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-291">While this improves startup performance at runtime, you will still experience the pain of view generation while you are developing.</span></span> <span data-ttu-id="cc8e7-292">ビューの生成、コンパイル時と実行時の両方のコストを削減するのに役立ついくつかの追加テクニックがあります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-292">There are several additional tricks that can help reduce the cost of view generation, both at compile time and run time.</span></span>

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a><span data-ttu-id="cc8e7-293">2.4.1 を外部キー アソシエーションを使用してビューの生成のコストを削減するには</span><span class="sxs-lookup"><span data-stu-id="cc8e7-293">2.4.1 Using Foreign Key Associations to reduce view generation cost</span></span>

<span data-ttu-id="cc8e7-294">さまざまな場合において、ビューの生成に費やされた時間を改善する独立アソシエーションからモデルを外部キー アソシエーションで関連付けを大幅に切り替えきました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-294">We have seen a number of cases where switching the associations in the model from Independent Associations to Foreign Key Associations dramatically improved the time spent in view generation.</span></span>

<span data-ttu-id="cc8e7-295">この機能強化の例として、EDMGen を使用して Navision モデルの 2 つのバージョンを生成します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-295">To demonstrate this improvement, we generated two versions of the Navision model by using EDMGen.</span></span> <span data-ttu-id="cc8e7-296">*注: seeappendix Cfor Navision モデルの説明。*</span><span class="sxs-lookup"><span data-stu-id="cc8e7-296">*Note: seeappendix Cfor a description of the Navision model.*</span></span> <span data-ttu-id="cc8e7-297">Navision モデルは、非常に大きなエンティティとそれらの間のリレーションシップの原因には、この演習では興味深いです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-297">The Navision model is interesting for this exercise due to its very large amount of entities and relationships between them.</span></span>

<span data-ttu-id="cc8e7-298">この非常に大規模なモデルの 1 つのバージョンは、外部キー アソシエーションで生成され、独立アソシエーションで生成された他のです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-298">One version of this very large model was generated with Foreign Keys Associations and the other was generated with Independent Associations.</span></span> <span data-ttu-id="cc8e7-299">各モデルのビューを生成するにかかる所要時間をタイムアウトしました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-299">We then timed how long it took to generate the views for each model.</span></span> <span data-ttu-id="cc8e7-300">エンティティ Framework5 テストでは、Entity Framework 6 のテスト クラス StorageMappingItemCollection から GenerateViews() メソッドを使用するときに、ビューを生成するのにクラス EntityViewGenerator から GenerateViews() メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-300">Entity Framework5 test used the GenerateViews() method from class EntityViewGenerator to generate the views, while the Entity Framework 6 test used the GenerateViews() method from class StorageMappingItemCollection.</span></span> <span data-ttu-id="cc8e7-301">これにより、Entity Framework 6 のコードベースで発生したコードの再構築します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-301">This due to code restructuring that occurred in the Entity Framework 6 codebase.</span></span>

<span data-ttu-id="cc8e7-302">Entity Framework 5 を使用して、外部キーを持つモデルのビューの生成は、ラボのコンピューターで 65 分をかかりました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-302">Using Entity Framework 5, view generation for the model with Foreign Keys took 65 minutes in a lab machine.</span></span> <span data-ttu-id="cc8e7-303">不明などのくらいの時間がかかりましたが、独立アソシエーションを使用するモデルのビューを生成します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-303">It's unknown how long it would have taken to generate the views for the model that used independent associations.</span></span> <span data-ttu-id="cc8e7-304">テストの月次の更新プログラムをインストールするラボで、コンピューターが再起動された前に、1 か月間実行されているままです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-304">We left the test running for over a month before the machine was rebooted in our lab to install monthly updates.</span></span>

<span data-ttu-id="cc8e7-305">Entity Framework 6 を使用して、外部キーを持つモデルのビューの生成は、同じマシンで、28 秒をかかりました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-305">Using Entity Framework 6, view generation for the model with Foreign Keys took 28 seconds in the same lab machine.</span></span> <span data-ttu-id="cc8e7-306">独立アソシエーションを使用するモデルのビューの生成は、58 秒かかりました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-306">View generation for the model that uses Independent Associations took 58 seconds.</span></span> <span data-ttu-id="cc8e7-307">そのビューの生成コードに Entity Framework 6 を改善という意味では多くのプロジェクトがスタートアップ時間の短縮を取得する事前生成済みのビューを必要はありません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-307">The improvements done to Entity Framework 6 on its view generation code mean that many projects won’t need pre-generated views to obtain faster startup times.</span></span>

<span data-ttu-id="cc8e7-308">EDMGen または Entity Framework Power Tools で生成前のビューでは、Entity Framework 4 および 5 を実行できる注釈に重要です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-308">It’s important to remark that pre-generating views in Entity Framework 4 and 5 can be done with EDMGen or the Entity Framework Power Tools.</span></span> <span data-ttu-id="cc8e7-309">Entity Framework 6 の表示、Entity Framework Power Tools を使用して、または」の説明に従ってプログラムで生成は実行できます[Pre-Generated マッピング ビュー](~/ef6/fundamentals/performance/pre-generated-views.md)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-309">For Entity Framework 6 view generation can be done via the Entity Framework Power Tools or programmatically as described in [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md).</span></span>

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a><span data-ttu-id="cc8e7-310">2.4.1.1 独立アソシエーションではなく、外部キーを使用する方法</span><span class="sxs-lookup"><span data-stu-id="cc8e7-310">2.4.1.1 How to use Foreign Keys instead of Independent Associations</span></span>

<span data-ttu-id="cc8e7-311">EDMGen またはエンティティ デザイナーを使用して、Visual Studio で、既定では、Fk を表示し、Fk と IAs を切り替える、1 つのチェック ボックスまたはコマンド ライン フラグしかかかりません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-311">When using EDMGen or the Entity Designer in Visual Studio, you get FKs by default, and it only takes a single checkbox or command line flag to switch between FKs and IAs.</span></span>

<span data-ttu-id="cc8e7-312">独立アソシエーションを使用して大規模な Code First モデルがある場合と、ビューの生成に同じ効果があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-312">If you have a large Code First model, using Independent Associations will have the same effect on view generation.</span></span> <span data-ttu-id="cc8e7-313">一部の開発者はこれと、そのオブジェクト モデルを汚染するを考慮する場合、依存オブジェクトのクラスの外部キー プロパティを含めることによってこの影響を回避できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-313">You can avoid this impact by including Foreign Key properties on the classes for your dependent objects, though some developers will consider this to be polluting their object model.</span></span> <span data-ttu-id="cc8e7-314">この件に関する詳細は見つかります\<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-314">You can find more information on this subject in \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>.</span></span>

| <span data-ttu-id="cc8e7-315">使用する場合</span><span class="sxs-lookup"><span data-stu-id="cc8e7-315">When using</span></span>      | <span data-ttu-id="cc8e7-316">方法</span><span class="sxs-lookup"><span data-stu-id="cc8e7-316">Do this</span></span>                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cc8e7-317">エンティティ デザイナー</span><span class="sxs-lookup"><span data-stu-id="cc8e7-317">Entity Designer</span></span> | <span data-ttu-id="cc8e7-318">2 つのエンティティ間の関連付けを追加すると、参照に関する制約があることを確認します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-318">After adding an association between two entities, make sure you have a referential constraint.</span></span> <span data-ttu-id="cc8e7-319">参照に関する制約は、独立アソシエーションではなく、外部キーを使用する Entity Framework を伝えます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-319">Referential constraints tell Entity Framework to use Foreign Keys instead of Independent Associations.</span></span> <span data-ttu-id="cc8e7-320">追加の詳細をご覧ください\<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-320">For additional details visit \<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>.</span></span> |
| <span data-ttu-id="cc8e7-321">EDMGen</span><span class="sxs-lookup"><span data-stu-id="cc8e7-321">EDMGen</span></span>          | <span data-ttu-id="cc8e7-322">EDMGen を使用して、データベースから、ファイルを生成する、外部キーが適用されるため、そのため、モデルに追加します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-322">When using EDMGen to generate your files from the database, your Foreign Keys will be respected and added to the model as such.</span></span> <span data-ttu-id="cc8e7-323">EDMGen によって公開されるさまざまなオプションの詳細については、次を参照してください。 [ http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-323">For more information on the different options exposed by EDMGen visit [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx).</span></span>                           |
| <span data-ttu-id="cc8e7-324">Code First</span><span class="sxs-lookup"><span data-stu-id="cc8e7-324">Code First</span></span>      | <span data-ttu-id="cc8e7-325">「リレーションシップの規則」セクションを参照してください、[コードの最初の規則](~/ef6/modeling/code-first/conventions/built-in.md)Code First を使用する場合は、依存オブジェクトで外部キー プロパティを組み込む方法については、トピック。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-325">See the "Relationship Convention" section of the [Code First Conventions](~/ef6/modeling/code-first/conventions/built-in.md) topic for information on how to include foreign key properties on dependent objects when using Code First.</span></span>                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a><span data-ttu-id="cc8e7-326">2.4.2、モデルを別のアセンブリに移動します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-326">2.4.2 Moving your model to a separate assembly</span></span>

<span data-ttu-id="cc8e7-327">アプリケーションのプロジェクトに直接、モデルが含まれているし、ビルド前イベントまたは、T4 テンプレートを使ってビューを生成する、ビューの生成と検証は行わ、プロジェクトをリビルドするたびに、モデルが変更されていない場合でも。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-327">When your model is included directly in your application's project and you generate views through a pre-build event or a T4 template, view generation and validation will take place whenever the project is rebuilt, even if the model wasn't changed.</span></span> <span data-ttu-id="cc8e7-328">モデルを別のアセンブリに移動して、アプリケーションのプロジェクトから参照するの変更を行うアプリケーションにモデルを含むプロジェクトをリビルドする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-328">If you move the model to a separate assembly and reference it from your application's project, you can make other changes to your application without needing to rebuild the project containing the model.</span></span>

<span data-ttu-id="cc8e7-329">*注:* クライアント プロジェクトのアプリケーション構成ファイルに、モデルの接続文字列をコピーするを区切るためにモデルを移動するときにアセンブリに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-329">*Note:*  when moving your model to separate assemblies remember to copy the connection strings for the model into the application configuration file of the client project.</span></span>

#### <a name="243-disable-validation-of-an-edmx-based-model"></a><span data-ttu-id="cc8e7-330">2.4.3 edmx ベース モデルの検証を無効にします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-330">2.4.3 Disable validation of an edmx-based model</span></span>

<span data-ttu-id="cc8e7-331">EDMX モデルは、モデルが変更されていない場合でも、コンパイル時に検証されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-331">EDMX models are validated at compile time, even if the model is unchanged.</span></span> <span data-ttu-id="cc8e7-332">モデルは既に検証されている場合は、[プロパティ] ウィンドウで、「ビルドの検証」プロパティを false に設定によってコンパイル時に検証を抑制できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-332">If your model has already been validated, you can suppress validation at compile time by setting the "Validate on Build" property to false in the properties window.</span></span> <span data-ttu-id="cc8e7-333">モデル、マッピングを変更するときに有効にできます一時的に再検証、変更を確認します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-333">When you change your mapping or model, you can temporarily re-enable validation to verify your changes.</span></span>

<span data-ttu-id="cc8e7-334">パフォーマンスの向上に加え、Entity Framework デザイナーの Entity Framework 6 では、「検証でビルド」のコストは、デザイナーの以前のバージョンよりはるかに小さいことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-334">Note that performance improvements were made to the Entity Framework Designer for Entity Framework 6, and the cost of the “Validate on Build” is much lower than in previous versions of the designer.</span></span>

## <a name="3-caching-in-the-entity-framework"></a><span data-ttu-id="cc8e7-335">Entity Framework で 3 つのキャッシュ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-335">3 Caching in the Entity Framework</span></span>

<span data-ttu-id="cc8e7-336">Entity Framework では、キャッシュの組み込みの次の形式があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-336">Entity Framework has the following forms of caching built-in:</span></span>

1.  <span data-ttu-id="cc8e7-337">オブジェクト キャッシュ – ObjectContext インスタンスに組み込まれている、ObjectStateManager がそのインスタンスを使用して取得されたオブジェクトのメモリ内で追跡します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-337">Object caching – the ObjectStateManager built into an ObjectContext instance keeps track in memory of the objects that have been retrieved using that instance.</span></span> <span data-ttu-id="cc8e7-338">これは、最初のレベルのキャッシュとも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-338">This is also known as first-level cache.</span></span>
2.  <span data-ttu-id="cc8e7-339">クエリ プラン キャッシュのクエリが複数回実行されたときに生成されるストア コマンドを再利用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-339">Query Plan Caching - reusing the generated store command when a query is executed more than once.</span></span>
3.  <span data-ttu-id="cc8e7-340">メタデータ キャッシュ - 同じモデルに異なる接続で、モデルのメタデータを共有します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-340">Metadata caching - sharing the metadata for a model across different connections to the same model.</span></span>

<span data-ttu-id="cc8e7-341">だけでなく、ADO.NET データ プロバイダーのラップ プロバイダーを使用して、データベースから取得された結果のキャッシュを使用して Entity Framework を拡張することもできますと呼ばれる特殊なボックスで、EF が提供されているキャッシュは、2 番目のレベルのキャッシュと呼ばれるもします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-341">Besides the caches that EF provides out of the box, a special kind of ADO.NET data provider known as a wrapping provider can also be used to extend Entity Framework with a cache for the results retrieved from the database, also known as second-level caching.</span></span>

### <a name="31-object-caching"></a><span data-ttu-id="cc8e7-342">3.1 オブジェクトのキャッシュ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-342">3.1 Object Caching</span></span>

<span data-ttu-id="cc8e7-343">既定でエンティティが、クエリの結果で返されるときに、EF が実体化する前に、ObjectContext は確認、ObjectStateManager に同じキーを持つエンティティが既に読み込まれたします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-343">By default when an entity is returned in the results of a query, just before EF materializes it, the ObjectContext will check if an entity with the same key has already been loaded into its ObjectStateManager.</span></span> <span data-ttu-id="cc8e7-344">同じキーを持つエンティティが既に存在する場合 EF が含まれますが、クエリの結果で。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-344">If an entity with the same keys is already present EF will include it in the results of the query.</span></span> <span data-ttu-id="cc8e7-345">EF はデータベースに対してクエリを発行しても、この動作は、エンティティを複数回に実体化のコストの多くをバイパスできます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-345">Although EF will still issue the query against the database, this behavior can bypass much of the cost of materializing the entity multiple times.</span></span>

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a><span data-ttu-id="cc8e7-346">3.1.1 DbContext の検索を使用して、オブジェクト キャッシュからエンティティを取得します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-346">3.1.1 Getting entities from the object cache using DbContext Find</span></span>

<span data-ttu-id="cc8e7-347">標準のクエリとは異なり DbSet (EF 4.1 で最初に含まれている Api) の Find メソッドは検索を実行メモリ内でも、データベースに対してクエリを発行する前にします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-347">Unlike a regular query, the Find method in DbSet (APIs included for the first time in EF 4.1) will perform a search in memory before even issuing the query against the database.</span></span> <span data-ttu-id="cc8e7-348">2 つの異なる ObjectContext インスタンスが個別のオブジェクトのキャッシュがあることを意味する、2 つの異なる ObjectStateManager インスタンスをあることに注意する重要です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-348">It’s important to note that two different ObjectContext instances will have two different ObjectStateManager instances, meaning that they have separate object caches.</span></span>

<span data-ttu-id="cc8e7-349">検索では、主キーの値を使用して、そのコンテキストによって追跡されるエンティティを検索しようとしています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-349">Find uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="cc8e7-350">コンテキストにエンティティがない場合、クエリの実行、および、データベースに対して評価方法と、コンテキストまたはデータベース内にエンティティが見つからない場合、null が返されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-350">If the entity is not in the context then a query will be executed and evaluated against the database, and null is returned if the entity is not found in the context or in the database.</span></span> <span data-ttu-id="cc8e7-351">検索にも、コンテキストに追加されているが、データベースに保存されていないエンティティが返されますことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-351">Note that Find also returns entities that have been added to the context but have not yet been saved to the database.</span></span>

<span data-ttu-id="cc8e7-352">検索を使用する場合にパフォーマンスに関する考慮事項があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-352">There is a performance consideration to be taken when using Find.</span></span> <span data-ttu-id="cc8e7-353">既定では、このメソッドを呼び出し、データベースにコミット保留中の変更を検出するために、オブジェクト キャッシュの検証がトリガーされます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-353">Invocations to this method by default will trigger a validation of the object cache in order to detect changes that are still pending commit to the database.</span></span> <span data-ttu-id="cc8e7-354">このプロセスは非常に多数のオブジェクト キャッシュ内、または、オブジェクト キャッシュに追加される大きなオブジェクト グラフ内のオブジェクトがある場合に非常に安価にできますも無効にできます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-354">This process can be very expensive if there are a very large number of objects in the object cache or in a large object graph being added to the object cache, but it can also be disabled.</span></span> <span data-ttu-id="cc8e7-355">場合によっては、することに気付くかもしれません自動を無効にすると、メソッドを検出する検索を呼び出すことの違いの桁に変更します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-355">In certain cases, you may perceive over an order of magnitude of difference in calling the Find method when you disable auto detect changes.</span></span> <span data-ttu-id="cc8e7-356">まだいる際にオブジェクト実際には、キャッシュと、オブジェクトがあるデータベースから取得する際に、第 2 桁が認識します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-356">Yet a second order of magnitude is perceived when the object actually is in the cache versus when the object has to be retrieved from the database.</span></span> <span data-ttu-id="cc8e7-357">5000 エンティティの負荷がかかっている、ミリ秒単位で、当社 microbenchmarks の一部を使用して取得された測定値と、グラフの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-357">Here is an example graph with measurements taken using some of our microbenchmarks, expressed in milliseconds, with a load of 5000 entities:</span></span>

<span data-ttu-id="cc8e7-358">![.NET 4.5 の対数スケール](~/ef6/media/net45logscale.png ".NET 4.5 - 対数スケール")</span><span class="sxs-lookup"><span data-stu-id="cc8e7-358">![.NET 4.5 logarithmic scale](~/ef6/media/net45logscale.png ".NET 4.5 - logarithmic scale")</span></span>

<span data-ttu-id="cc8e7-359">自動検出の変更を無効になっていると検索の例:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-359">Example of Find with auto-detect changes disabled:</span></span>

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

<span data-ttu-id="cc8e7-360">Find メソッドを使用するときに考慮する必要があることです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-360">What you have to consider when using the Find method is:</span></span>

1.  <span data-ttu-id="cc8e7-361">オブジェクトがキャッシュにない場合は、検索の利点が否定しますが、構文がキーで、クエリよりも簡単です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-361">If the object is not in the cache the benefits of Find are negated, but the syntax is still simpler than a query by key.</span></span>
2.  <span data-ttu-id="cc8e7-362">自動の変更を検出する場合は、有効になっている Find メソッドのコストが 1 桁、または、モデルと、オブジェクト キャッシュ内のエンティティの量の複雑さに応じて、さらに増やすことができます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-362">If auto detect changes is enabled the cost of the Find method may increase by one order of magnitude, or even more depending on the complexity of your model and the amount of entities in your object cache.</span></span>

<span data-ttu-id="cc8e7-363">また、のみを見つけることに注意してが探しているエンティティを返すは自動的に読み込み、関連付けられているエンティティが既にキャッシュにない、オブジェクトの場合。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-363">Also, keep in mind that Find only returns the entity you are looking for and it does not automatically loads its associated entities if they are not already in the object cache.</span></span> <span data-ttu-id="cc8e7-364">関連付けられているエンティティを取得する必要がある場合は、一括読み込みでのキーによって、クエリを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-364">If you need to retrieve associated entities, you can use a query by key with eager loading.</span></span> <span data-ttu-id="cc8e7-365">詳細については、次を参照してください。 **8.1 Lazy Loading とします。一括読み込み**します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-365">For more information see **8.1 Lazy Loading vs. Eager Loading**.</span></span>

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a><span data-ttu-id="cc8e7-366">3.1.2 パフォーマンスの問題をオブジェクト キャッシュに多数のエンティティがある場合</span><span class="sxs-lookup"><span data-stu-id="cc8e7-366">3.1.2 Performance issues when the object cache has many entities</span></span>

<span data-ttu-id="cc8e7-367">オブジェクト キャッシュは、Entity Framework の全体的な応答性の向上に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-367">The object cache helps to increase the overall responsiveness of Entity Framework.</span></span> <span data-ttu-id="cc8e7-368">ただし、オブジェクト キャッシュが非常に大量のエンティティの追加などの特定の操作に影響を与える読み込まれた場合は、削除、エントリ、SaveChanges を検索します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-368">However, when the object cache has a very large amount of entities loaded it may affect certain operations such as Add, Remove, Find, Entry, SaveChanges and more.</span></span> <span data-ttu-id="cc8e7-369">具体的には、DetectChanges への呼び出しをトリガーする操作は非常に大きいオブジェクトのキャッシュによって悪影響を及ぼす影響します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-369">In particular, operations that trigger a call to DetectChanges will be negatively affected by very large object caches.</span></span> <span data-ttu-id="cc8e7-370">DetectChanges は、オブジェクト状態マネージャーと、オブジェクト グラフのサイズを直接判断のパフォーマンスは、オブジェクト グラフを同期します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-370">DetectChanges synchronizes the object graph with the object state manager and its performance will determined directly by the size of the object graph.</span></span> <span data-ttu-id="cc8e7-371">DetectChanges の詳細については、次を参照してください。 [POCO エンティティでの変更を追跡](https://msdn.microsoft.com/library/dd456848.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-371">For more information about DetectChanges, see [Tracking Changes in POCO Entities](https://msdn.microsoft.com/library/dd456848.aspx).</span></span>

<span data-ttu-id="cc8e7-372">Entity Framework 6 を使用して、開発者はコレクションの反復処理し、インスタンスにつき 1 回追加を呼び出すのではなく、DbSet に直接 AddRange と RemoveRange を呼び出すことができません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-372">When using Entity Framework 6, developers are able to call AddRange and RemoveRange directly on a DbSet, instead of iterating on a collection and calling Add once per instance.</span></span> <span data-ttu-id="cc8e7-373">Range メソッドを使用する利点は、DetectChanges のコストは、追加された各エンティティごとに 1 回ではなくエンティティのセット全体の 1 回有料のみです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-373">The advantage of using the range methods is that the cost of DetectChanges is only paid once for the entire set of entities as opposed to once per each added entity.</span></span>

### <a name="32-query-plan-caching"></a><span data-ttu-id="cc8e7-374">3.2 はクエリ プランのキャッシュ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-374">3.2 Query Plan Caching</span></span>

<span data-ttu-id="cc8e7-375">初めてクエリを実行すると、それはストア コマンド (たとえば、T-SQL が SQL Server に対して実行するときに実行されます) に概念のクエリを変換するプランを内部コンパイラ説明します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-375">The first time a query is executed, it goes through the internal plan compiler to translate the conceptual query into the store command (for example, the T-SQL which is executed when run against SQL Server).</span></span>  <span data-ttu-id="cc8e7-376">ストアが実行されるクエリ プランのキャッシュが有効になっている場合、クエリは、[次へ] にコマンドは、プランのコンパイラをバイパスして、実行するためのクエリ プラン キャッシュから直接取得されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-376">If query plan caching is enabled, the next time the query is executed the store command is retrieved directly from the query plan cache for execution, bypassing the plan compiler.</span></span>

<span data-ttu-id="cc8e7-377">クエリ プランのキャッシュは、同じ AppDomain 内で ObjectContext インスタンス間で共有されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-377">The query plan cache is shared across ObjectContext instances within the same AppDomain.</span></span> <span data-ttu-id="cc8e7-378">クエリ プランのキャッシュのメリットを ObjectContext インスタンスを保持する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-378">You don't need to hold onto an ObjectContext instance to benefit from query plan caching.</span></span>

#### <a name="321-some-notes-about-query-plan-caching"></a><span data-ttu-id="cc8e7-379">3.2.1 クエリ プランのキャッシュについての注意事項</span><span class="sxs-lookup"><span data-stu-id="cc8e7-379">3.2.1 Some notes about Query Plan Caching</span></span>

-   <span data-ttu-id="cc8e7-380">すべてのクエリの種類のクエリ プランのキャッシュが共有: Entity SQL、LINQ to Entities と CompiledQuery オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-380">The query plan cache is shared for all query types: Entity SQL, LINQ to Entities, and CompiledQuery objects.</span></span>
-   <span data-ttu-id="cc8e7-381">既定では、クエリ プランのキャッシュが有効になっている Entity SQL クエリでは、ObjectQuery EntityCommand を使用またはを実行するかどうか。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-381">By default, query plan caching is enabled for Entity SQL queries, whether executed through an EntityCommand or through an ObjectQuery.</span></span> <span data-ttu-id="cc8e7-382">これも既定で有効に linq to Entities クエリでは、.NET 4.5 では、Entity Framework と Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-382">It is also enabled by default for LINQ to Entities queries in Entity Framework on .NET 4.5, and in Entity Framework 6</span></span>
    -   <span data-ttu-id="cc8e7-383">(EntityCommand ObjectQuery 上) EnablePlanCaching プロパティを false に設定して、クエリ プランのキャッシュを無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-383">Query plan caching can be disabled by setting the EnablePlanCaching property (on EntityCommand or ObjectQuery) to false.</span></span> <span data-ttu-id="cc8e7-384">例えば:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-384">For example:</span></span>
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
-   <span data-ttu-id="cc8e7-385">パラメーター化されたクエリでは、パラメーターの値を変更する、まだヒット、キャッシュされたクエリ。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-385">For parameterized queries, changing the parameter's value will still hit the cached query.</span></span> <span data-ttu-id="cc8e7-386">パラメーターのファセット (たとえば、サイズ、有効桁数、またはスケール) を変更すると、キャッシュ内の異なるエントリがヒットします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-386">But changing a parameter's facets (for example, size, precision, or scale) will hit a different entry in the cache.</span></span>
-   <span data-ttu-id="cc8e7-387">Entity SQL を使用する場合、クエリ文字列は、キーの一部です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-387">When using Entity SQL, the query string is part of the key.</span></span> <span data-ttu-id="cc8e7-388">クエリが機能的に同等場合でも、クエリのすべての変更が、別のキャッシュ エントリで発生します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-388">Changing the query at all will result in different cache entries, even if the queries are functionally equivalent.</span></span> <span data-ttu-id="cc8e7-389">これには、大文字小文字の区別、または空白文字への変更が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-389">This includes changes to casing or whitespace.</span></span>
-   <span data-ttu-id="cc8e7-390">LINQ を使用する場合は、キーの一部を生成するクエリが処理されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-390">When using LINQ, the query is processed to generate a part of the key.</span></span> <span data-ttu-id="cc8e7-391">LINQ 式を変更すると、そのため、別のキーが生成されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-391">Changing the LINQ expression will therefore generate a different key.</span></span>
-   <span data-ttu-id="cc8e7-392">他の技術的な制限が適用可能性があります。詳細については、Autocompiled クエリを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-392">Other technical limitations may apply; see Autocompiled Queries for more details.</span></span>

#### <a name="322------cache-eviction-algorithm"></a><span data-ttu-id="cc8e7-393">3.2.2 キャッシュ削除アルゴリズム</span><span class="sxs-lookup"><span data-stu-id="cc8e7-393">3.2.2      Cache eviction algorithm</span></span>

<span data-ttu-id="cc8e7-394">内部アルゴリズムの動作がヘルプを有効または無効にするクエリ プランのキャッシュを解明する方法について理解します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-394">Understanding how the internal algorithm works will help you figure out when to enable or disable query plan caching.</span></span> <span data-ttu-id="cc8e7-395">クリーンアップ アルゴリズムは次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-395">The cleanup algorithm is as follows:</span></span>

1.  <span data-ttu-id="cc8e7-396">キャッシュに設定された数のエントリ (800) が含まれているを定期的に (1 回数/分) キャッシュからフラッシュされるタイマーが開始します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-396">Once the cache contains a set number of entries (800), we start a timer that periodically (once-per-minute) sweeps the cache.</span></span>
2.  <span data-ttu-id="cc8e7-397">キャッシュのスイープ中に、エントリが LFRU (最も頻繁に – 最近使用した) 上のキャッシュから削除されます単位。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-397">During cache sweeps, entries are removed from the cache on a LFRU (Least frequently – recently used) basis.</span></span> <span data-ttu-id="cc8e7-398">このアルゴリズムは両方のヒット カウントと年齢考慮エントリを取り出すを決定する際にします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-398">This algorithm takes both hit count and age into account when deciding which entries are ejected.</span></span>
3.  <span data-ttu-id="cc8e7-399">キャッシュには各キャッシュのスイープの末尾には、800 のエントリにはもう一度が含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-399">At the end of each cache sweep, the cache again contains 800 entries.</span></span>

<span data-ttu-id="cc8e7-400">すべてのキャッシュ エントリは削除するエントリを決定する際に均等に扱われます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-400">All cache entries are treated equally when determining which entries to evict.</span></span> <span data-ttu-id="cc8e7-401">これを CompiledQuery のストア コマンドが同じ確率として Entity SQL クエリのストア コマンドの削除のことを意味します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-401">This means the store command for a CompiledQuery has the same chance of eviction as the store command for an Entity SQL query.</span></span>

<span data-ttu-id="cc8e7-402">キャッシュに 800 エンティティがあるが、このタイマーが開始した後、60 秒間はキャッシュのスイープを行うとき、キャッシュの削除タイマーが開始に注意してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-402">Note that the cache eviction timer is kicked in when there are 800 entities in the cache, but the cache is only swept 60 seconds after this timer is started.</span></span> <span data-ttu-id="cc8e7-403">つまり、最大で 60 秒間、キャッシュは非常に大きくなるになることがあります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-403">That means that for up to 60 seconds your cache may grow to be quite large.</span></span>

#### <a name="323-------test-metrics-demonstrating-query-plan-caching-performance"></a><span data-ttu-id="cc8e7-404">3.2.3 メトリックが示すクエリ プランのキャッシュのパフォーマンスをテストします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-404">3.2.3       Test Metrics demonstrating query plan caching performance</span></span>

<span data-ttu-id="cc8e7-405">クエリ プランが、アプリケーションのパフォーマンス上のキャッシュの効果を示すためにテストを実行数 Navision モデルに対する Entity SQL クエリを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-405">To demonstrate the effect of query plan caching on your application's performance, we performed a test where we executed a number of Entity SQL queries against the Navision model.</span></span> <span data-ttu-id="cc8e7-406">Navision モデルと実行されたクエリの種類の説明については、付録を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-406">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="cc8e7-407">このテストで最初のクエリのリストを反復処理し、(キャッシュが有効になっている) 場合は、キャッシュに追加する 1 つずつを 1 回実行します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-407">In this test, we first iterate through the list of queries and execute each one once to add them to the cache (if caching is enabled).</span></span> <span data-ttu-id="cc8e7-408">この手順は一定ではありません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-408">This step is untimed.</span></span> <span data-ttu-id="cc8e7-409">スイープを実行します。 キャッシュを許可するのには 60 秒以上のメイン スレッドをスリープ状態次に、最後に、キャッシュされたクエリを実行するリストの 2 つ目を時間の経過に反復処理します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-409">Next, we sleep the main thread for over 60 seconds to allow cache sweeping to take place; finally, we iterate through the list a 2nd time to execute the cached queries.</span></span> <span data-ttu-id="cc8e7-410">さらに、時間を正確に取得したクエリ プランのキャッシュで指定された特典を反映するように、クエリの各セットが実行される前に、SQL Server プラン キャッシュがフラッシュされます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-410">Additionally, he SQL Server plan cache is flushed before each set of queries is executed so that the times we obtain accurately reflect the benefit given by the query plan cache.</span></span>

##### <a name="3231-------test-results"></a><span data-ttu-id="cc8e7-411">3.2.3.1 テスト結果</span><span class="sxs-lookup"><span data-stu-id="cc8e7-411">3.2.3.1       Test Results</span></span>

| <span data-ttu-id="cc8e7-412">テスト</span><span class="sxs-lookup"><span data-stu-id="cc8e7-412">Test</span></span>                                                                   | <span data-ttu-id="cc8e7-413">EF5 キャッシュなし</span><span class="sxs-lookup"><span data-stu-id="cc8e7-413">EF5 no cache</span></span> | <span data-ttu-id="cc8e7-414">EF5 キャッシュ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-414">EF5 cached</span></span> | <span data-ttu-id="cc8e7-415">EF6 キャッシュなし</span><span class="sxs-lookup"><span data-stu-id="cc8e7-415">EF6 no cache</span></span> | <span data-ttu-id="cc8e7-416">EF6 のキャッシュ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-416">EF6 cached</span></span> |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| <span data-ttu-id="cc8e7-417">18723 のすべてのクエリを列挙します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-417">Enumerating all 18723 queries</span></span>                                          | <span data-ttu-id="cc8e7-418">124</span><span class="sxs-lookup"><span data-stu-id="cc8e7-418">124</span></span>          | <span data-ttu-id="cc8e7-419">125.4</span><span class="sxs-lookup"><span data-stu-id="cc8e7-419">125.4</span></span>      | <span data-ttu-id="cc8e7-420">124.3</span><span class="sxs-lookup"><span data-stu-id="cc8e7-420">124.3</span></span>        | <span data-ttu-id="cc8e7-421">125.3</span><span class="sxs-lookup"><span data-stu-id="cc8e7-421">125.3</span></span>      |
| <span data-ttu-id="cc8e7-422">スイープ (800 最初のクエリについて、複雑さに関係なく) を回避します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-422">Avoiding sweep (just the first 800 queries, regardless of complexity)</span></span>  | <span data-ttu-id="cc8e7-423">41.7</span><span class="sxs-lookup"><span data-stu-id="cc8e7-423">41.7</span></span>         | <span data-ttu-id="cc8e7-424">5.5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-424">5.5</span></span>        | <span data-ttu-id="cc8e7-425">40.5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-425">40.5</span></span>         | <span data-ttu-id="cc8e7-426">5.4</span><span class="sxs-lookup"><span data-stu-id="cc8e7-426">5.4</span></span>        |
| <span data-ttu-id="cc8e7-427">AggregatingSubtotals クエリ (178 合計 - スイープが避けられます)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-427">Just the AggregatingSubtotals queries (178 total - which avoids sweep)</span></span> | <span data-ttu-id="cc8e7-428">39.5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-428">39.5</span></span>         | <span data-ttu-id="cc8e7-429">4.5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-429">4.5</span></span>        | <span data-ttu-id="cc8e7-430">38.1</span><span class="sxs-lookup"><span data-stu-id="cc8e7-430">38.1</span></span>         | <span data-ttu-id="cc8e7-431">4.6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-431">4.6</span></span>        |

<span data-ttu-id="cc8e7-432">*すべての時刻の秒数。*</span><span class="sxs-lookup"><span data-stu-id="cc8e7-432">*All times in seconds.*</span></span>

<span data-ttu-id="cc8e7-433">精神的の多くを実行するときに (たとえば、クエリを動的に作成された)、個別のクエリのキャッシュ問題が解決しないし、キャッシュの結果として得られるフラッシュすると、実際にこれを使用してからプランのキャッシュを最大限に効果的なクエリを保つことができます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-433">Moral - when executing lots of distinct queries (for example,  dynamically created queries), caching doesn't help and the resulting flushing of the cache can keep the queries that would benefit the most from plan caching from actually using it.</span></span>

<span data-ttu-id="cc8e7-434">AggregatingSubtotals クエリでは、最も複雑なクエリをテストしました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-434">The AggregatingSubtotals queries are the most complex of the queries we tested with.</span></span> <span data-ttu-id="cc8e7-435">予想どおり、クエリが複雑で利点から、クエリ プランのキャッシュが表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-435">As expected, the more complex the query is, the more benefit you will see from query plan caching.</span></span>

<span data-ttu-id="cc8e7-436">CompiledQuery が本当にそのプランがキャッシュでの LINQ クエリであるため、CompiledQuery 同等の Entity SQL クエリとの比較は、同様の結果が必要です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-436">Because a CompiledQuery is really a LINQ query with its plan cached, the comparison of a CompiledQuery versus the equivalent Entity SQL query should have similar results.</span></span> <span data-ttu-id="cc8e7-437">実際には、アプリに大量の動的な Entity SQL クエリがある場合は、クエリでキャッシュがいっぱいも事実上により CompiledQueries「逆コンパイル」がキャッシュからフラッシュされるときにします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-437">In fact, if an app has lots of dynamic Entity SQL queries, filling the cache with queries will also effectively cause CompiledQueries to “decompile” when they are flushed from the cache.</span></span> <span data-ttu-id="cc8e7-438">このシナリオで、CompiledQueries の優先順位を動的クエリでキャッシュを無効にするとパフォーマンスが向上する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-438">In this scenario, performance may be improved by disabling caching on the dynamic queries to prioritize the CompiledQueries.</span></span> <span data-ttu-id="cc8e7-439">さらに、もちろん、書き直すことをアプリにパラメーター化クエリを使用して動的クエリではなく。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-439">Better yet, of course, would be to rewrite the app to use parameterized queries instead of dynamic queries.</span></span>

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a><span data-ttu-id="cc8e7-440">3.3 CompiledQuery を使用して LINQ クエリのパフォーマンスを向上させる</span><span class="sxs-lookup"><span data-stu-id="cc8e7-440">3.3 Using CompiledQuery to improve performance with LINQ queries</span></span>

<span data-ttu-id="cc8e7-441">テストの結果を示すこと CompiledQuery を使用して実行できる 7% の特典 autocompiled 経由で LINQ クエリです。つまり、7%、Entity Framework スタックからコードを実行する時間を費やすしますアプリケーションは 7% 高速になることは限りません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-441">Our tests indicate that using CompiledQuery can bring a benefit of 7% over autocompiled LINQ queries; this means that you’ll spend 7% less time executing code from the Entity Framework stack; it does not mean your application will be 7% faster.</span></span> <span data-ttu-id="cc8e7-442">一般に、作成と EF 5.0 CompiledQuery オブジェクトの保守のコストは価値のメリットと比較した場合、ここで問題が発生可能性がありますできません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-442">Generally speaking, the cost of writing and maintaining CompiledQuery objects in EF 5.0 may not be worth the trouble when compared to the benefits.</span></span> <span data-ttu-id="cc8e7-443">走行距離は異なる可能性があります、ため、プロジェクトに追加のプッシュが必要な場合にこのオプションを実行します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-443">Your mileage may vary, so exercise this option if your project requires the extra push.</span></span> <span data-ttu-id="cc8e7-444">CompiledQueries は ObjectContext から派生したモデルと互換性があり、DbContext 派生モデルと互換性がない、のみことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-444">Note that CompiledQueries are only compatible with ObjectContext-derived models, and not compatible with DbContext-derived models.</span></span>

<span data-ttu-id="cc8e7-445">作成して、CompiledQuery を呼び出すことの詳細については、次を参照してください。[コンパイルされたクエリ (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-445">For more information on creating and invoking a CompiledQuery, see [Compiled Queries (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).</span></span>

<span data-ttu-id="cc8e7-446">2 つの考慮事項が CompiledQuery、つまり、使用するという要件静的インスタンスと、問題と構成可能性があるを使用する場合に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-446">There are two considerations you have to take when using a CompiledQuery, namely the requirement to use static instances and the problems they have with composability.</span></span> <span data-ttu-id="cc8e7-447">詳細については、これら 2 つの考慮事項を次に示します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-447">Here follows an in-depth explanation of these two considerations.</span></span>

#### <a name="331-------use-static-compiledquery-instances"></a><span data-ttu-id="cc8e7-448">3.3.1 CompiledQuery の静的インスタンスを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-448">3.3.1       Use static CompiledQuery instances</span></span>

<span data-ttu-id="cc8e7-449">これを行うたびに、データベースからデータをフェッチする必要があるように時間のかかるプロセスは、LINQ クエリをコンパイル、ので。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-449">Since compiling a LINQ query is a time-consuming process, we don’t want to do it every time we need to fetch data from the database.</span></span> <span data-ttu-id="cc8e7-450">CompiledQuery インスタンスでは、1 回コンパイルして、複数回実行することができますが、注意が必要し調達を何度もコンパイルではなく、毎回同じ CompiledQuery インスタンスを再利用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-450">CompiledQuery instances allow you to compile once and run multiple times, but you have to be careful and procure to re-use the same CompiledQuery instance every time instead of compiling it over and over again.</span></span> <span data-ttu-id="cc8e7-451">CompiledQuery インスタンスを格納する静的メンバーの使用が必要になります。それ以外の場合、上の利点は表示されません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-451">The use of static members to store the CompiledQuery instances becomes necessary; otherwise you won’t see any benefit.</span></span>

<span data-ttu-id="cc8e7-452">たとえば、ページが選択したカテゴリの製品の表示を処理するために次のメソッドの本体とします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-452">For example, suppose your page has the following method body to handle displaying the products for the selected category:</span></span>

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

<span data-ttu-id="cc8e7-453">ここでは、メソッドが呼び出されるたびに、実行時に新しい CompiledQuery インスタンスを作成するは。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-453">In this case, you will create a new CompiledQuery instance on the fly every time the method is called.</span></span> <span data-ttu-id="cc8e7-454">クエリのプラン キャッシュからのストア コマンドを取得することによってパフォーマンス上の利点を確認するのではなくは新しいインスタンスが作成されるたびに、CompiledQuery はプラン コンパイラを移動します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-454">Instead of seeing performance benefits by retrieving the store command from the query plan cache, the CompiledQuery will go through the plan compiler every time a new instance is created.</span></span> <span data-ttu-id="cc8e7-455">実際には、するが汚染 CompiledQuery の新しいエントリで、クエリ プランのキャッシュ、メソッドが呼び出されるたびにします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-455">In fact, you will be polluting your query plan cache with a new CompiledQuery entry every time the method is called.</span></span>

<span data-ttu-id="cc8e7-456">代わりに、メソッドが呼び出されるたびに、同じコンパイル済みクエリを呼び出すために、コンパイル済みのクエリの静的インスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-456">Instead, you want to create a static instance of the compiled query, so you are invoking the same compiled query every time the method is called.</span></span> <span data-ttu-id="cc8e7-457">1 つの方法にこれは、オブジェクト コンテキストのメンバーとして追加すると、CompiledQuery インスタンス。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-457">One way to so this is by adding the CompiledQuery instance as a member of your object context.</span></span>  <span data-ttu-id="cc8e7-458">行うことができますし、モ ノほとんど明確になり、CompiledQuery にアクセスするヘルパー メソッドを使用しています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-458">You can then make things a little cleaner by accessing the CompiledQuery through a helper method:</span></span>

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

<span data-ttu-id="cc8e7-459">このヘルパー メソッドには、次のように呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-459">This helper method would be invoked as follows:</span></span>

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-------composing-over-a-compiledquery"></a><span data-ttu-id="cc8e7-460">3.3.2 を CompiledQuery 経由で作成します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-460">3.3.2       Composing over a CompiledQuery</span></span>

<span data-ttu-id="cc8e7-461">任意の LINQ クエリを作成する機能は非常に便利です。これを行うには、単にメソッドを呼び出す対象の IQueryable 後など*Skip()* または*Count()* します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-461">The ability to compose over any LINQ query is extremely useful; to do this, you simply invoke a method after the IQueryable such as *Skip()* or *Count()*.</span></span> <span data-ttu-id="cc8e7-462">ただし、これは基本的には、新しい IQueryable オブジェクトを返します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-462">However, doing so essentially returns a new IQueryable object.</span></span> <span data-ttu-id="cc8e7-463">CompiledQuery 経由での作成から技術的には停止するにはありませんが、新しい IQueryable オブジェクトの生成は原因はプラン コンパイラを通るをもう一度必要です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-463">While there’s nothing to stop you technically from composing over a CompiledQuery, doing so will cause the generation of a new IQueryable object that requires passing through the plan compiler again.</span></span>

<span data-ttu-id="cc8e7-464">一部のコンポーネントとの構成済みの IQueryable を使用して、高度な機能を有効にするオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-464">Some components will make use of composed IQueryable objects to enable advanced functionality.</span></span> <span data-ttu-id="cc8e7-465">たとえば、ASP です。NET の GridView データ バインドできます IQueryable オブジェクトに SelectMethod プロパティを使用しています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-465">For example, ASP.NET’s GridView can be data-bound to an IQueryable object via the SelectMethod property.</span></span> <span data-ttu-id="cc8e7-466">並べ替えとデータ モデルに対するページングを許可するには、この IQueryable オブジェクトは、GridView を作成します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-466">The GridView will then compose over this IQueryable object to allow sorting and paging over the data model.</span></span> <span data-ttu-id="cc8e7-467">、ご覧のとおり、CompiledQuery を GridView を使用してコンパイル済みのクエリは達しないが新しい autocompiled クエリが生成します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-467">As you can see, using a CompiledQuery for the GridView would not hit the compiled query but would generate a new autocompiled query.</span></span>

<span data-ttu-id="cc8e7-468">Customer Advisory Team はこれで、「潜在的なパフォーマンスの問題でコンパイルされた LINQ クエリ再コンパイル」ブログの投稿について説明します。<http://blogs.msdn.com/b/appfabriccat/archive/2010/08/06/potential-performance-issues-with-compiled-linq-query-re-compiles.aspx>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-468">The Customer Advisory Team discusses this in their "Potential Performance Issues with Compiled LINQ Query Re-Compiles" blog post: <http://blogs.msdn.com/b/appfabriccat/archive/2010/08/06/potential-performance-issues-with-compiled-linq-query-re-compiles.aspx>.</span></span>

<span data-ttu-id="cc8e7-469">これを実行することがあります 1 つの場所は、プログレッシブのフィルターをクエリに追加する場合です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-469">One place where you may run into this is when adding progressive filters to a query.</span></span> <span data-ttu-id="cc8e7-470">たとえば、省略可能なフィルター (たとえば、国と OrdersCount) のいくつかのドロップダウン リストで、[顧客] ページがあるとします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-470">For example, suppose you had a Customers page with several drop-down lists for optional filters (for example, Country and OrdersCount).</span></span> <span data-ttu-id="cc8e7-471">これらのフィルターを作成するには、CompiledQuery の IQueryable 結果に対するが発生で、新しいクエリを実行するたびに、プランのコンパイラを通過します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-471">You can compose these filters over the IQueryable results of a CompiledQuery, but doing so will result in the new query going through the plan compiler every time you execute it.</span></span>

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

 <span data-ttu-id="cc8e7-472">この再コンパイルを避けるためには、可能なフィルターを考慮に入れてに CompiledQuery を書き直すことができます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-472">To avoid this re-compilation, you can rewrite the CompiledQuery to take the possible filters into account:</span></span>

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

<span data-ttu-id="cc8e7-473">などの UI でが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-473">Which would be invoked in the UI like:</span></span>

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

 <span data-ttu-id="cc8e7-474">ここでのトレードオフがある、生成されるストア コマンドは、null チェックでフィルターを常にこれらを最適化するには、データベース サーバーの非常に単純にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-474">A tradeoff here is the generated store command will always have the filters with the null checks, but these should be fairly simple for the database server to optimize:</span></span>

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a><span data-ttu-id="cc8e7-475">3.4 メタデータのキャッシュ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-475">3.4 Metadata caching</span></span>

<span data-ttu-id="cc8e7-476">Entity Framework では、メタデータのキャッシュもサポートしています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-476">The Entity Framework also supports Metadata caching.</span></span> <span data-ttu-id="cc8e7-477">同じモデルに異なる接続間で型情報とデータベースへの型マッピング情報のキャッシュこれは基本的にします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-477">This is essentially caching of type information and type-to-database mapping information across different connections to the same model.</span></span> <span data-ttu-id="cc8e7-478">メタデータ キャッシュは、AppDomain ごとに一意です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-478">The Metadata cache is unique per AppDomain.</span></span>

#### <a name="341-metadata-caching-algorithm"></a><span data-ttu-id="cc8e7-479">3.4.1 メタデータ キャッシュ アルゴリズム</span><span class="sxs-lookup"><span data-stu-id="cc8e7-479">3.4.1 Metadata Caching algorithm</span></span>

1.  <span data-ttu-id="cc8e7-480">モデルのメタデータ情報は、各 EntityConnection の ItemCollection に格納されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-480">Metadata information for a model is stored in an ItemCollection for each EntityConnection.</span></span>
    -   <span data-ttu-id="cc8e7-481">なお、別の ItemCollection オブジェクト モデルのさまざまな部分があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-481">As a side note, there are different ItemCollection objects for different parts of the model.</span></span> <span data-ttu-id="cc8e7-482">たとえば、StoreItemCollections には、データベース モデルに関する情報が含まれます。ObjectItemCollection には、データ モデルに関する情報が含まれています。EdmItemCollection には、概念モデルに関する情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-482">For example, StoreItemCollections contains the information about the database model; ObjectItemCollection contains information about the data model; EdmItemCollection contains information about the conceptual model.</span></span>

2.  <span data-ttu-id="cc8e7-483">2 つの接続は、同じ接続文字列を使用して、同じ ItemCollection インスタンスが共有されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-483">If two connections use the same connection string, they will share the same ItemCollection instance.</span></span>
3.  <span data-ttu-id="cc8e7-484">機能的に同等ですが、異なるテキスト上の接続文字列は、さまざまなメタデータ キャッシュがあります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-484">Functionally equivalent but textually different connection strings may result in different metadata caches.</span></span> <span data-ttu-id="cc8e7-485">共有メタデータが発生する必要があります、トークンの順序を変更するだけ、接続文字列をトークン化操作を行います。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-485">We do tokenize connection strings, so simply changing the order of the tokens should result in shared metadata.</span></span> <span data-ttu-id="cc8e7-486">機能的に同じに見える 2 つの接続文字列可能性がありますいない評価と同じトークン化の後にします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-486">But two connection strings that seem functionally the same may not be evaluated as identical after tokenization.</span></span>
4.  <span data-ttu-id="cc8e7-487">ItemCollection を使用して定期的にチェックします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-487">The ItemCollection is periodically checked for use.</span></span> <span data-ttu-id="cc8e7-488">あるワークスペースが最近アクセスされませんがされていると判断された場合は、[次へ] のキャッシュのスイープのクリーンアップのマークされます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-488">If it is determined that a workspace has not been accessed recently, it will be marked for cleanup on the next cache sweep.</span></span>
5.  <span data-ttu-id="cc8e7-489">EntityConnection を作成するだけで、(ただし、接続が開かれるまで、項目のコレクションは初期化されません) を作成するメタデータ キャッシュが発生します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-489">Merely creating an EntityConnection will cause a metadata cache to be created (though the item collections in it will not be initialized until the connection is opened).</span></span> <span data-ttu-id="cc8e7-490">キャッシュ アルゴリズムは、"使用"でないを判断するまでは、このワークスペースをメモリ内に残ります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-490">This workspace will remain in-memory until the caching algorithm determines it is not “in use”.</span></span>

<span data-ttu-id="cc8e7-491">Customer Advisory Team ブログの投稿、ItemCollection への参照を保持する大規模なモデルを使用する場合は、「廃止」を回避するために説明を執筆しています: \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-491">The Customer Advisory Team has written a blog post that describes holding a reference to an ItemCollection in order to avoid "deprecation" when using large models: \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>.</span></span>

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a><span data-ttu-id="cc8e7-492">3.4.2 メタデータのキャッシュと、クエリ プランのキャッシュ間のリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-492">3.4.2 The relationship between Metadata Caching and Query Plan Caching</span></span>

<span data-ttu-id="cc8e7-493">クエリ プランのキャッシュ インスタンスは、ストアの種類の MetadataWorkspace の ItemCollection に住んでいます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-493">The query plan cache instance lives in the MetadataWorkspace's ItemCollection of store types.</span></span> <span data-ttu-id="cc8e7-494">これは、特定の MetadataWorkspace を使用してインスタンス化された任意のコンテキストに対するクエリのストアのキャッシュされたコマンドが使用されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-494">This means that cached store commands will be used for queries against any context instantiated using a given MetadataWorkspace.</span></span> <span data-ttu-id="cc8e7-495">若干異なりますが、トークン化した後は一致しない 2 つの接続文字列があれば、別のクエリ プランのキャッシュ インスタンスを必要があることを意味します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-495">It also means that if you have two connections strings that are slightly different and don't match after tokenizing, you will have different query plan cache instances.</span></span>

### <a name="35-results-caching"></a><span data-ttu-id="cc8e7-496">3.5 結果のキャッシュ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-496">3.5 Results caching</span></span>

<span data-ttu-id="cc8e7-497">結果のキャッシュ (とも呼ばれます「第 2 レベルのキャッシュ」) がローカル キャッシュ内でのクエリの結果を保持します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-497">With results caching (also known as "second-level caching"), you keep the results of queries in a local cache.</span></span> <span data-ttu-id="cc8e7-498">クエリを発行するときに初めて表示かどうか、結果は、ストアに対してクエリする前にローカルで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-498">When issuing a query, you first see if the results are available locally before you query against the store.</span></span> <span data-ttu-id="cc8e7-499">結果のキャッシュは、Entity Framework によって直接サポートされていない、折り返しプロバイダーを使用して 2 番目のレベルのキャッシュを追加すること勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-499">While results caching isn't directly supported by Entity Framework, it's possible to add a second level cache by using a wrapping provider.</span></span> <span data-ttu-id="cc8e7-500">2 番目のレベルのキャッシュを使用してラップ プロバイダーの例は、Alachisoft の[NCache に基づいてエンティティ フレームワークの第 2 レベルのキャッシュ](http://www.alachisoft.com/ncache/entity-framework.html)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-500">An example wrapping provider with a second-level cache is Alachisoft's [Entity Framework Second Level Cache based on NCache](http://www.alachisoft.com/ncache/entity-framework.html).</span></span>

<span data-ttu-id="cc8e7-501">この 2 番目のレベルのキャッシュの実装は、挿入された機能を LINQ 式が評価された後の場所 (と funcletized) を受け取ると、クエリ実行プランを計算または最初のレベルのキャッシュから取得します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-501">This implementation of second-level caching is an injected functionality that takes place after the LINQ expression has been evaluated (and funcletized) and the query execution plan is computed or retrieved from the first-level cache.</span></span> <span data-ttu-id="cc8e7-502">具体化パイプラインは後で実行されるため、第 2 レベルのキャッシュは生のデータベースの結果のみを格納します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-502">The second-level cache will then store only the raw database results, so the materialization pipeline still executes afterwards.</span></span>

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a><span data-ttu-id="cc8e7-503">3.5.1 結果の折り返しのプロバイダーでのキャッシュのその他の参照</span><span class="sxs-lookup"><span data-stu-id="cc8e7-503">3.5.1 Additional references for results caching with the wrapping provider</span></span>

-   <span data-ttu-id="cc8e7-504">Julie Lerman には、Windows Server AppFabric のキャッシュを使用するサンプルの折り返しのプロバイダーを更新する方法を含む"2 番目のレベルのキャッシュで Entity Framework と Windows Azure"MSDN の記事が書き込まれます。 [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-504">Julie Lerman has written a "Second-Level Caching in Entity Framework and Windows Azure" MSDN article that includes how to update the sample wrapping provider to use Windows Server AppFabric caching: [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span></span>
-   <span data-ttu-id="cc8e7-505">チームのブログに Entity Framework 5 のキャッシュ プロバイダーを実行しているものを取得する方法を説明する投稿が Entity Framework 5 を使用する場合: \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-505">If you are working with Entity Framework 5, the team blog has a post that describes how to get things running with the caching provider for Entity Framework 5: \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>.</span></span> <span data-ttu-id="cc8e7-506">プロジェクトには、第 2 レベルのキャッシュの追加を自動化するために T4 テンプレートも含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-506">It also includes a T4 template to help automate adding the 2nd-level caching to your project.</span></span>

## <a name="4-autocompiled-queries"></a><span data-ttu-id="cc8e7-507">4 つの Autocompiled クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-507">4 Autocompiled Queries</span></span>

<span data-ttu-id="cc8e7-508">Entity Framework を使用してデータベースに対してクエリが発行されるときに実際には、結果を具体化する前に、一連の手順を移動する必要があります。このような 1 つの手順では、クエリのコンパイルです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-508">When a query is issued against a database using Entity Framework, it must go through a series of steps before actually materializing the results; one such step is Query Compilation.</span></span> <span data-ttu-id="cc8e7-509">Entity SQL クエリは、パフォーマンスが向上は自動的にキャッシュとして 2 番目または 3 番目の時間計画コンパイラをスキップし、キャッシュされたプランを使用して、代わりに、同じクエリを実行するように呼ばれていました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-509">Entity SQL queries were known to have good performance as they are automatically cached, so the second or third time you execute the same query it can skip the plan compiler and use the cached plan instead.</span></span>

<span data-ttu-id="cc8e7-510">Entity Framework 5 では、自動キャッシュの LINQ to Entities クエリにも導入されました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-510">Entity Framework 5 introduced automatic caching for LINQ to Entities queries as well.</span></span> <span data-ttu-id="cc8e7-511">作成を高速化の CompiledQuery Entity Framework の過去のエディションで、パフォーマンスが一般的に、こうと、LINQ to Entities クエリ キャッシュされます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-511">In past editions of Entity Framework creating a CompiledQuery to speed your performance was a common practice, as this would make your LINQ to Entities query cacheable.</span></span> <span data-ttu-id="cc8e7-512">キャッシュは今すぐ自動的には、CompiledQuery を使用せず、ためにを呼び出してこの機能「autocompiled クエリ」します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-512">Since caching is now done automatically without the use of a CompiledQuery, we call this feature “autocompiled queries”.</span></span> <span data-ttu-id="cc8e7-513">クエリ プランのキャッシュとそのしくみの詳細については、クエリ プランのキャッシュを参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-513">For more information about the query plan cache and its mechanics, see Query Plan Caching.</span></span>

<span data-ttu-id="cc8e7-514">クエリは、再コンパイルする必要がある場合、entity Framework が検出された場合でも、前にコンパイルされている必要があるが、クエリが呼び出されるようにするとします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-514">Entity Framework detects when a query requires to be recompiled, and does so when the query is invoked even if it had been compiled before.</span></span> <span data-ttu-id="cc8e7-515">再コンパイルするクエリが発生する一般的な条件は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-515">Common conditions that cause the query to be recompiled are:</span></span>

-   <span data-ttu-id="cc8e7-516">クエリに関連付けられている MergeOption を変更します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-516">Changing the MergeOption associated to your query.</span></span> <span data-ttu-id="cc8e7-517">キャッシュされたクエリは使用されませんプラン コンパイラが再び実行、代わりに、新しく作成されたプランがキャッシュを取得します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-517">The cached query will not be used, instead the plan compiler will run again and the newly created plan gets cached.</span></span>
-   <span data-ttu-id="cc8e7-518">ContextOptions.UseCSharpNullComparisonBehavior の値を変更します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-518">Changing the value of ContextOptions.UseCSharpNullComparisonBehavior.</span></span> <span data-ttu-id="cc8e7-519">MergeOption を変更すると同じ効果が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-519">You get the same effect as changing the MergeOption.</span></span>

<span data-ttu-id="cc8e7-520">その他の条件によって防止、クエリがキャッシュを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-520">Other conditions can prevent your query from using the cache.</span></span> <span data-ttu-id="cc8e7-521">一般的な例は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-521">Common examples are:</span></span>

-   <span data-ttu-id="cc8e7-522">IEnumerable を使用して&lt;T&gt;します。含む&lt;&gt;(T 値)。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-522">Using IEnumerable&lt;T&gt;.Contains&lt;&gt;(T value).</span></span>
-   <span data-ttu-id="cc8e7-523">定数を使用してクエリを生成する関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-523">Using functions that produce queries with constants.</span></span>
-   <span data-ttu-id="cc8e7-524">マップされていないオブジェクトのプロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-524">Using the properties of a non-mapped object.</span></span>
-   <span data-ttu-id="cc8e7-525">クエリを再コンパイルを必要とする別のクエリにリンクしています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-525">Linking your query to another query that requires to be recompiled.</span></span>

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a><span data-ttu-id="cc8e7-526">4.1 は IEnumerable を使用して&lt;T&gt;します。含む&lt;T&gt;(T 値)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-526">4.1 Using IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value)</span></span>

<span data-ttu-id="cc8e7-527">Entity Framework は、IEnumerable を呼び出すクエリをキャッシュしません&lt;T&gt;します。含む&lt;T&gt;(T 値) に対するメモリ内コレクション、コレクションの値が揮発性と見なされるためです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-527">Entity Framework does not cache queries that invoke IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) against an in-memory collection, since the values of the collection are considered volatile.</span></span> <span data-ttu-id="cc8e7-528">プラン コンパイラは常に処理するため、次のクエリはキャッシュされません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-528">The following example query will not be cached, so it will always be processed by the plan compiler:</span></span>

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

<span data-ttu-id="cc8e7-529">クエリがコンパイル速度または低速方法を決定するに対してどの値を含む IEnumerable のサイズが実行されるに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-529">Note that the size of the IEnumerable against which Contains is executed determines how fast or how slow your query is compiled.</span></span> <span data-ttu-id="cc8e7-530">上記の例のような大規模なコレクションを使用する場合、パフォーマンスが大幅に低下することができます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-530">Performance can suffer significantly when using large collections such as the one shown in the example above.</span></span>

<span data-ttu-id="cc8e7-531">Entity Framework 6 には、IEnumerable の方法は、最適化が含まれています。&lt;T&gt;します。含む&lt;T&gt;(T 値) は、クエリが実行されたときに機能します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-531">Entity Framework 6 contains optimizations to the way IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) works when queries are executed.</span></span> <span data-ttu-id="cc8e7-532">生成された SQL コードが生成するために高速化と読みやすくし、ほとんどの場合でも速く実行サーバーにします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-532">The SQL code that is generated is much faster to produce and more readable, and in most cases it also executes faster in the server.</span></span>

### <a name="42-using-functions-that-produce-queries-with-constants"></a><span data-ttu-id="cc8e7-533">4.2 は、定数を使用してクエリを生成する関数を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-533">4.2 Using functions that produce queries with constants</span></span>

<span data-ttu-id="cc8e7-534">Skip()、利用、Contains() および DefautIfEmpty() LINQ 演算子では、パラメーターを含む SQL クエリを作成しませんが、代わりに、定数として渡された値を格納します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-534">The Skip(), Take(), Contains() and DefautIfEmpty() LINQ operators do not produce SQL queries with parameters but instead put the values passed to them as constants.</span></span> <span data-ttu-id="cc8e7-535">このため、可能性のあるそれ以外の場合、クエリを汚染最終的に同じクエリはプランでは、EF のスタックと、データベース サーバーの両方をキャッシュ、およびは reutilized 後続のクエリ実行で同じ定数を使用しない限り取得されません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-535">Because of this, queries that might otherwise be identical end up polluting the query plan cache, both on the EF stack and on the database server, and do not get reutilized unless the same constants are used in a subsequent query execution.</span></span> <span data-ttu-id="cc8e7-536">例えば:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-536">For example:</span></span>

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

<span data-ttu-id="cc8e7-537">この例では、クエリ id の値が異なる次のクエリが実行されるたびには新しいプランにコンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-537">In this example, each time this query is executed with a different value for id the query will be compiled into a new plan.</span></span>

<span data-ttu-id="cc8e7-538">特定に注意してください Skip と Take ページングを実施する際に使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-538">In particular pay attention to the use of Skip and Take when doing paging.</span></span> <span data-ttu-id="cc8e7-539">EF6 では、これらのメソッドは、実質的には、キャッシュされたクエリ プラン再利用可能な EF がこれらのメソッドに渡された変数のキャプチャし、Sqlparameter に変換するため、ラムダのオーバー ロードがあります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-539">In EF6 these methods have a lambda overload that effectively makes the cached query plan reusable because EF can capture variables passed to these methods and translate them to SQLparameters.</span></span> <span data-ttu-id="cc8e7-540">これは、ため、各クエリの Skip と Take を異なる定数は、独自のクエリ プランのキャッシュ エントリを取得するそれ以外の場合、キャッシュをより明確な保持にも役立ちます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-540">This also helps keep the cache cleaner since otherwise each query with a different constant for Skip and Take would get its own query plan cache entry.</span></span>

<span data-ttu-id="cc8e7-541">次のようなコードが最適では、このクラスのクエリを体現だけを目的とを考慮してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-541">Consider the following code, which is suboptimal but is only meant to exemplify this class of queries:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="cc8e7-542">これと同じコードの高速バージョンでは、ラムダでスキップを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-542">A faster version of this same code would involve calling Skip with a lambda:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i \< count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="cc8e7-543">2 番目のスニペットは、クエリが実行される CPU 時間を節約し、クエリ キャッシュを汚染することを回避するたびに、同じクエリ プランが使用されるため、最大 11% 高速化を実行できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-543">The second snippet may run up to 11% faster because the same query plan is used every time the query is run, which saves CPU time and avoids polluting the query cache.</span></span> <span data-ttu-id="cc8e7-544">さらに、スキップするパラメーターは、クロージャでは、コードもようになりますようになりました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-544">Furthermore, because the parameter to Skip is in a closure the code might as well look like this now:</span></span>

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a><span data-ttu-id="cc8e7-545">4.3、マップされていないオブジェクトのプロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-545">4.3 Using the properties of a non-mapped object</span></span>

<span data-ttu-id="cc8e7-546">ときに、パラメーター、クエリがキャッシュされないが、クエリは、マップされていないオブジェクト型のプロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-546">When a query uses the properties of a non-mapped object type as a parameter then the query will not get cached.</span></span> <span data-ttu-id="cc8e7-547">例えば:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-547">For example:</span></span>

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

<span data-ttu-id="cc8e7-548">この例では、クラス NonMappedType がエンティティ モデルの一部ではないことを想定しています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-548">In this example, assume that class NonMappedType is not part of the Entity model.</span></span> <span data-ttu-id="cc8e7-549">このクエリは、マップされていない型を使用し、代わりにローカル変数をクエリにパラメーターとして使用する簡単に変更できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-549">This query can easily be changed to not use a non-mapped type and instead use a local variable as the parameter to the query:</span></span>

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

<span data-ttu-id="cc8e7-550">ここでは、クエリでは、キャッシュできなくなり、クエリ プランのキャッシュのパフォーマンスが得られます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-550">In this case, the query will be able to get cached and will benefit from the query plan cache.</span></span>

### <a name="44-linking-to-queries-that-require-recompiling"></a><span data-ttu-id="cc8e7-551">4.4 リンクを再コンパイルを必要とするクエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-551">4.4 Linking to queries that require recompiling</span></span>

<span data-ttu-id="cc8e7-552">上記と同じ例を次の 2 番目のクエリを再コンパイルする必要のあるクエリに依存しているがある場合、全体の 2 番目のクエリも再コンパイルされます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-552">Following the same example as above, if you have a second query that relies on a query that needs to be recompiled, your entire second query will also be recompiled.</span></span> <span data-ttu-id="cc8e7-553">このシナリオを説明する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-553">Here’s an example to illustrate this scenario:</span></span>

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

<span data-ttu-id="cc8e7-554">例は、ジェネリックですがどの firstQuery へのリンクの原因となって secondQuery キャッシュを取得できないようにすることを示しています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-554">The example is generic, but it illustrates how linking to firstQuery is causing secondQuery to be unable to get cached.</span></span> <span data-ttu-id="cc8e7-555">FirstQuery で、再コンパイルを必要とするクエリがなかった場合、secondQuery はキャッシュされています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-555">If firstQuery had not been a query that requires recompiling, then secondQuery would have been cached.</span></span>

## <a name="5-notracking-queries"></a><span data-ttu-id="cc8e7-556">5 つのクエリの NoTracking</span><span class="sxs-lookup"><span data-stu-id="cc8e7-556">5 NoTracking Queries</span></span>

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a><span data-ttu-id="cc8e7-557">5.1 には、状態管理のオーバーヘッドを削減する変更追跡が無効にします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-557">5.1 Disabling change tracking to reduce state management overhead</span></span>

<span data-ttu-id="cc8e7-558">読み取り専用のシナリオには、し、ObjectStateManager にオブジェクトの読み込みのオーバーヘッドを回避する場合は、「No 追跡」クエリを発行できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-558">If you are in a read-only scenario and want to avoid the overhead of loading the objects into the ObjectStateManager, you can issue "No Tracking" queries.</span></span>  <span data-ttu-id="cc8e7-559">クエリ レベルでは、変更の追跡を無効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-559">Change tracking can be disabled at the query level.</span></span>

<span data-ttu-id="cc8e7-560">ただし、変更の追跡を無効にすると、効果的に変えることオブジェクトのキャッシュをオフにしてください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-560">Note though that by disabling change tracking you are effectively turning off the object cache.</span></span> <span data-ttu-id="cc8e7-561">エンティティのクエリを実行するときは、ObjectStateManager から既に具体化されたクエリの結果を取得することによって結果の具体化をスキップすることはできません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-561">When you query for an entity, we can't skip materialization by pulling the previously-materialized query results from the ObjectStateManager.</span></span> <span data-ttu-id="cc8e7-562">繰り返し同じコンテキストで同じエンティティのクエリを実行する場合は、変更の追跡を有効化のメリットのパフォーマンスを実際に見ることがあります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-562">If you are repeatedly querying for the same entities on the same context, you might actually see a performance benefit from enabling change tracking.</span></span>

<span data-ttu-id="cc8e7-563">ObjectContext を使用してクエリを実行するときに、ObjectQuery および ObjectSet インスタンスは設定されているとしてで構成されているクエリは、親クエリの有効な MergeOption を継承、MergeOption が記憶されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-563">When querying using ObjectContext, ObjectQuery and ObjectSet instances will remember a MergeOption once it is set, and queries that are composed on them will inherit the effective MergeOption of the parent query.</span></span> <span data-ttu-id="cc8e7-564">DbContext を使用する場合は、AsNoTracking() 修飾子を呼び出して、DbSet の追跡を無効にできます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-564">When using DbContext, tracking can be disabled by calling the AsNoTracking() modifier on the DbSet.</span></span>

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a><span data-ttu-id="cc8e7-565">5.1.1 変更の DbContext を使用する場合は、クエリの追跡を無効にします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-565">5.1.1 Disabling change tracking for a query when using DbContext</span></span>

<span data-ttu-id="cc8e7-566">クエリで AsNoTracking() メソッドの呼び出しを連鎖させることにより、クエリのモードを NoTracking を切り替えることができます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-566">You can switch the mode of a query to NoTracking by chaining a call to the AsNoTracking() method in the query.</span></span> <span data-ttu-id="cc8e7-567">ObjectQuery とは異なり、DbContext API のクラスの DbSet と DbQuery クラスは変更可能なプロパティを MergeOption の必要はありません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-567">Unlike ObjectQuery, the DbSet and DbQuery classes in the DbContext API don’t have a mutable property for the MergeOption.</span></span>

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a><span data-ttu-id="cc8e7-568">5.1.2 ObjectContext を使用して、クエリ レベルで変更の追跡を無効にします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-568">5.1.2 Disabling change tracking at the query level using ObjectContext</span></span>

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a><span data-ttu-id="cc8e7-569">5.1.3 ObjectContext を使用して設定全体のエンティティの変更の追跡を無効にします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-569">5.1.3 Disabling change tracking for an entire entity set using ObjectContext</span></span>

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52-test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a><span data-ttu-id="cc8e7-570">5.2 NoTracking クエリのパフォーマンスの利点を示すメトリックをテストします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-570">5.2 Test Metrics demonstrating the performance benefit of NoTracking queries</span></span>

<span data-ttu-id="cc8e7-571">このテストで Navision モデルに対するクエリを NoTracking する追跡を比較することによって、ObjectStateManager の入力が注目します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-571">In this test we look at the cost of filling the ObjectStateManager by comparing Tracking to NoTracking queries for the Navision model.</span></span> <span data-ttu-id="cc8e7-572">Navision モデルと実行されたクエリの種類の説明については、付録を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-572">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="cc8e7-573">このテストでは、クエリのリストを反復処理し、それぞれを 1 回実行します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-573">In this test, we iterate through the list of queries and execute each one once.</span></span> <span data-ttu-id="cc8e7-574">"AppendOnly"の既定のマージ オプションを使用し、テスト、NoTracking クエリが 1 回に 1 回の 2 つのバリエーションをしました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-574">We ran two variations of the test, once with NoTracking queries and once with the default merge option of "AppendOnly".</span></span> <span data-ttu-id="cc8e7-575">各バリエーションを 3 回実行し、実行の平均値を取得します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-575">We ran each variation 3 times and take the mean value of the runs.</span></span> <span data-ttu-id="cc8e7-576">テスト間は SQL Server でクエリ キャッシュをクリアし、次のコマンドを実行して、tempdb の圧縮します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-576">Between the tests we clear the query cache on the SQL Server and shrink the tempdb by running the following commands:</span></span>

1.  <span data-ttu-id="cc8e7-577">DBCC DROPCLEANBUFFERS</span><span class="sxs-lookup"><span data-stu-id="cc8e7-577">DBCC DROPCLEANBUFFERS</span></span>
2.  <span data-ttu-id="cc8e7-578">DBCC FREEPROCCACHE</span><span class="sxs-lookup"><span data-stu-id="cc8e7-578">DBCC FREEPROCCACHE</span></span>
3.  <span data-ttu-id="cc8e7-579">DBCC SHRINKDATABASE (tempdb、0)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-579">DBCC SHRINKDATABASE (tempdb, 0)</span></span>

<span data-ttu-id="cc8e7-580">テスト結果、中央値 3 を実行します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-580">Test Results, median over 3 runs:</span></span>

|                        | <span data-ttu-id="cc8e7-581">ない追跡 – ワーキング セット</span><span class="sxs-lookup"><span data-stu-id="cc8e7-581">NO TRACKING – WORKING SET</span></span> | <span data-ttu-id="cc8e7-582">追跡なし – 時間</span><span class="sxs-lookup"><span data-stu-id="cc8e7-582">NO TRACKING – TIME</span></span> | <span data-ttu-id="cc8e7-583">ワーキング セットにのみ – 追加します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-583">APPEND ONLY – WORKING SET</span></span> | <span data-ttu-id="cc8e7-584">のみ: 時間の追加します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-584">APPEND ONLY – TIME</span></span> |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| <span data-ttu-id="cc8e7-585">**Entity Framework 5**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-585">**Entity Framework 5**</span></span> | <span data-ttu-id="cc8e7-586">460361728</span><span class="sxs-lookup"><span data-stu-id="cc8e7-586">460361728</span></span>                 | <span data-ttu-id="cc8e7-587">1163536 ms</span><span class="sxs-lookup"><span data-stu-id="cc8e7-587">1163536 ms</span></span>         | <span data-ttu-id="cc8e7-588">596545536</span><span class="sxs-lookup"><span data-stu-id="cc8e7-588">596545536</span></span>                 | <span data-ttu-id="cc8e7-589">1273042 ms</span><span class="sxs-lookup"><span data-stu-id="cc8e7-589">1273042 ms</span></span>         |
| <span data-ttu-id="cc8e7-590">**Entity Framework 6**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-590">**Entity Framework 6**</span></span> | <span data-ttu-id="cc8e7-591">647127040</span><span class="sxs-lookup"><span data-stu-id="cc8e7-591">647127040</span></span>                 | <span data-ttu-id="cc8e7-592">190228 ms</span><span class="sxs-lookup"><span data-stu-id="cc8e7-592">190228 ms</span></span>          | <span data-ttu-id="cc8e7-593">832798720</span><span class="sxs-lookup"><span data-stu-id="cc8e7-593">832798720</span></span>                 | <span data-ttu-id="cc8e7-594">195521 ms</span><span class="sxs-lookup"><span data-stu-id="cc8e7-594">195521 ms</span></span>          |

<span data-ttu-id="cc8e7-595">Entity Framework 5 は、小さいメモリ フット プリントが、実行の最後に Entity Framework 6 よりも必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-595">Entity Framework 5 will have a smaller memory footprint at the end of the run than Entity Framework 6 does.</span></span> <span data-ttu-id="cc8e7-596">Entity Framework 6 で使用される追加のメモリは、追加のメモリ構造体と新機能とパフォーマンスの向上を実現するコードの結果です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-596">The additional memory consumed by Entity Framework 6 is the result of additional memory structures and code that enable new features and better performance.</span></span>

<span data-ttu-id="cc8e7-597">違いがあるもクリアのメモリ使用量、ObjectStateManager を使用する場合。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-597">There’s also a clear difference in memory footprint when using the ObjectStateManager.</span></span> <span data-ttu-id="cc8e7-598">Entity Framework 5 は、データベースから具体化すべてのエンティティを追跡するときに 30% のフット プリントを増加します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-598">Entity Framework 5 increased its footprint by 30% when keeping track of all the entities we materialized from the database.</span></span> <span data-ttu-id="cc8e7-599">Entity Framework 6 は、そのときに、28% によってそのフット プリントを向上します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-599">Entity Framework 6 increased its footprint by 28% when doing so.</span></span>

<span data-ttu-id="cc8e7-600">時間で Entity Framework 6 先、大規模な余白によってこのテストでの Entity Framework 5 を進んでいます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-600">In terms of time, Entity Framework 6 outperforms Entity Framework 5 in this test by a large margin.</span></span> <span data-ttu-id="cc8e7-601">Entity Framework 6 には、Entity Framework 5 で使用された時間の約 16% のテストが完了しました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-601">Entity Framework 6 completed the test in roughly 16% of the time consumed by Entity Framework 5.</span></span> <span data-ttu-id="cc8e7-602">さらに、Entity Framework 5 には、ObjectStateManager が使用されているときに完了する 9% 以上の時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-602">Additionally, Entity Framework 5 takes 9% more time to complete when the ObjectStateManager is being used.</span></span> <span data-ttu-id="cc8e7-603">比較では、Entity Framework 6 が 3%、ObjectStateManager を使用するときに多くの時間を使用しています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-603">In comparison, Entity Framework 6 is using 3% more time when using the ObjectStateManager.</span></span>

## <a name="6-query-execution-options"></a><span data-ttu-id="cc8e7-604">6 つのクエリ実行オプション</span><span class="sxs-lookup"><span data-stu-id="cc8e7-604">6 Query Execution Options</span></span>

<span data-ttu-id="cc8e7-605">Entity Framework では、クエリをいくつかの方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-605">Entity Framework offers several different ways to query.</span></span> <span data-ttu-id="cc8e7-606">次のオプションについて見て、長所と短所、それぞれの比較やそのパフォーマンス特性を確認します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-606">We'll take a look at the following options, compare the pros and cons of each, and examine their performance characteristics:</span></span>

-   <span data-ttu-id="cc8e7-607">LINQ to Entities。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-607">LINQ to Entities.</span></span>
-   <span data-ttu-id="cc8e7-608">追跡なし LINQ to Entities。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-608">No Tracking LINQ to Entities.</span></span>
-   <span data-ttu-id="cc8e7-609">ObjectQuery 経由で SQL のエンティティ。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-609">Entity SQL over an ObjectQuery.</span></span>
-   <span data-ttu-id="cc8e7-610">EntityCommand を経由で SQL のエンティティ。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-610">Entity SQL over an EntityCommand.</span></span>
-   <span data-ttu-id="cc8e7-611">ExecuteStoreQuery します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-611">ExecuteStoreQuery.</span></span>
-   <span data-ttu-id="cc8e7-612">SqlQuery します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-612">SqlQuery.</span></span>
-   <span data-ttu-id="cc8e7-613">CompiledQuery します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-613">CompiledQuery.</span></span>

### <a name="61-------linq-to-entities-queries"></a><span data-ttu-id="cc8e7-614">6.1 LINQ to Entities クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-614">6.1       LINQ to Entities queries</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="cc8e7-615">**プロフェッショナル**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-615">**Pros**</span></span>

-   <span data-ttu-id="cc8e7-616">CUD 操作に適しています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-616">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="cc8e7-617">完全に具体化されたオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-617">Fully materialized objects.</span></span>
-   <span data-ttu-id="cc8e7-618">構文で記述する最も簡単なプログラミング言語に組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-618">Simplest to write with syntax built into the programming language.</span></span>
-   <span data-ttu-id="cc8e7-619">適切なパフォーマンス。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-619">Good performance.</span></span>

<span data-ttu-id="cc8e7-620">**短所**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-620">**Cons**</span></span>

-   <span data-ttu-id="cc8e7-621">特定の技術的な制限事項など。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-621">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="cc8e7-622">OUTER JOIN クエリ DefaultIfEmpty の使用パターンは、Entity SQL での単純な OUTER JOIN ステートメントよりも複雑なクエリで発生します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-622">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="cc8e7-623">使用できない場合が一般的なパターンに一致するような。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-623">You still can’t use LIKE with general pattern matching.</span></span>

### <a name="62-------no-tracking-linq-to-entities-queries"></a><span data-ttu-id="cc8e7-624">6.2 なしの追跡 LINQ to Entities クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-624">6.2       No Tracking LINQ to Entities queries</span></span>

<span data-ttu-id="cc8e7-625">ときに、コンテキストは、ObjectContext を派生します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-625">When the context derives ObjectContext:</span></span>

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="cc8e7-626">ときに、コンテキストでは、DbContext が派生します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-626">When the context derives DbContext:</span></span>

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="cc8e7-627">**プロフェッショナル**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-627">**Pros**</span></span>

-   <span data-ttu-id="cc8e7-628">定期的な LINQ クエリでパフォーマンスが向上します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-628">Improved performance over regular LINQ queries.</span></span>
-   <span data-ttu-id="cc8e7-629">完全に具体化されたオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-629">Fully materialized objects.</span></span>
-   <span data-ttu-id="cc8e7-630">構文で記述する最も簡単なプログラミング言語に組み込まれています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-630">Simplest to write with syntax built into the programming language.</span></span>

<span data-ttu-id="cc8e7-631">**短所**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-631">**Cons**</span></span>

-   <span data-ttu-id="cc8e7-632">CUD 操作には適していません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-632">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="cc8e7-633">特定の技術的な制限事項など。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-633">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="cc8e7-634">OUTER JOIN クエリ DefaultIfEmpty の使用パターンは、Entity SQL での単純な OUTER JOIN ステートメントよりも複雑なクエリで発生します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-634">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="cc8e7-635">使用できない場合が一般的なパターンに一致するような。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-635">You still can’t use LIKE with general pattern matching.</span></span>

<span data-ttu-id="cc8e7-636">スカラー プロパティを射影するクエリは、NoTracking が指定されていない場合でも追跡されないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-636">Note that queries that project scalar properties are not tracked even if the NoTracking is not specified.</span></span> <span data-ttu-id="cc8e7-637">例えば:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-637">For example:</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

<span data-ttu-id="cc8e7-638">この特定のクエリの NoTracking、されているが明示的に指定しますが、いない具体化することがあるため、型が知られているオブジェクトの状態マネージャーから具体化された結果は追跡されません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-638">This particular query doesn’t explicitly specify being NoTracking, but since it’s not materializing a type that’s known to the object state manager then the materialized result is not tracked.</span></span>

### <a name="63-------entity-sql-over-an-objectquery"></a><span data-ttu-id="cc8e7-639">6.3 entity SQL で ObjectQuery</span><span class="sxs-lookup"><span data-stu-id="cc8e7-639">6.3       Entity SQL over an ObjectQuery</span></span>

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

<span data-ttu-id="cc8e7-640">**プロフェッショナル**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-640">**Pros**</span></span>

-   <span data-ttu-id="cc8e7-641">CUD 操作に適しています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-641">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="cc8e7-642">完全に具体化されたオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-642">Fully materialized objects.</span></span>
-   <span data-ttu-id="cc8e7-643">サポートは、プランのキャッシュをクエリします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-643">Supports query plan caching.</span></span>

<span data-ttu-id="cc8e7-644">**短所**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-644">**Cons**</span></span>

-   <span data-ttu-id="cc8e7-645">ユーザー エラーよりやすい言語に組み込まれているクエリの構造はテキスト形式のクエリ文字列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-645">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>

### <a name="64-------entity-sql-over-an-entity-command"></a><span data-ttu-id="cc8e7-646">6.4 エンティティ、エンティティ コマンド経由で SQL</span><span class="sxs-lookup"><span data-stu-id="cc8e7-646">6.4       Entity SQL over an Entity Command</span></span>

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

<span data-ttu-id="cc8e7-647">**プロフェッショナル**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-647">**Pros**</span></span>

-   <span data-ttu-id="cc8e7-648">サポートは、.NET 4.0 では (.NET 4.5 では他のクエリの種類をすべてでサポートは、プランのキャッシュ) プランのキャッシュをクエリします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-648">Supports query plan caching in .NET 4.0 (plan caching is supported by all other query types in .NET 4.5).</span></span>

<span data-ttu-id="cc8e7-649">**短所**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-649">**Cons**</span></span>

-   <span data-ttu-id="cc8e7-650">ユーザー エラーよりやすい言語に組み込まれているクエリの構造はテキスト形式のクエリ文字列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-650">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>
-   <span data-ttu-id="cc8e7-651">CUD 操作には適していません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-651">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="cc8e7-652">結果は自動的に具体化されていないと、データ リーダーから読み取る必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-652">Results are not automatically materialized, and must be read from the data reader.</span></span>

### <a name="65-------sqlquery-and-executestorequery"></a><span data-ttu-id="cc8e7-653">6.5 SqlQuery と ExecuteStoreQuery</span><span class="sxs-lookup"><span data-stu-id="cc8e7-653">6.5       SqlQuery and ExecuteStoreQuery</span></span>

<span data-ttu-id="cc8e7-654">データベースで SqlQuery:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-654">SqlQuery on Database:</span></span>

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

<span data-ttu-id="cc8e7-655">DbSet に SqlQuery:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-655">SqlQuery on DbSet:</span></span>

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

<span data-ttu-id="cc8e7-656">ExecyteStoreQuery:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-656">ExecyteStoreQuery:</span></span>

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

<span data-ttu-id="cc8e7-657">**プロフェッショナル**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-657">**Pros**</span></span>

-   <span data-ttu-id="cc8e7-658">一般に最速のパフォーマンス計画のコンパイラがバイパスされるためです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-658">Generally fastest performance since plan compiler is bypassed.</span></span>
-   <span data-ttu-id="cc8e7-659">完全に具体化されたオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-659">Fully materialized objects.</span></span>
-   <span data-ttu-id="cc8e7-660">CUD 操作、DbSet から使用する場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-660">Suitable for CUD operations when used from the DbSet.</span></span>

<span data-ttu-id="cc8e7-661">**短所**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-661">**Cons**</span></span>

-   <span data-ttu-id="cc8e7-662">クエリがテキストの場合、エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-662">Query is textual and error prone.</span></span>
-   <span data-ttu-id="cc8e7-663">クエリは、概念のセマンティクスではなく、ストア セマンティクスを使用して、特定のバックエンドに関連付けられます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-663">Query is tied to a specific backend by using store semantics instead of conceptual semantics.</span></span>
-   <span data-ttu-id="cc8e7-664">継承が存在する場合は、手動作成したクエリは、要求された型のマッピングの条件を考慮する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-664">When inheritance is present, handcrafted query needs to account for mapping conditions for the type requested.</span></span>

### <a name="66-------compiledquery"></a><span data-ttu-id="cc8e7-665">6.6 CompiledQuery</span><span class="sxs-lookup"><span data-stu-id="cc8e7-665">6.6       CompiledQuery</span></span>

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

<span data-ttu-id="cc8e7-666">**プロフェッショナル**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-666">**Pros**</span></span>

-   <span data-ttu-id="cc8e7-667">定期的な LINQ クエリで最大 7% のパフォーマンス向上を提供します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-667">Provides up to a 7% performance improvement over regular LINQ queries.</span></span>
-   <span data-ttu-id="cc8e7-668">完全に具体化されたオブジェクト。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-668">Fully materialized objects.</span></span>
-   <span data-ttu-id="cc8e7-669">CUD 操作に適しています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-669">Suitable for CUD operations.</span></span>

<span data-ttu-id="cc8e7-670">**短所**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-670">**Cons**</span></span>

-   <span data-ttu-id="cc8e7-671">複雑さとオーバーヘッドのプログラミングを向上します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-671">Increased complexity and programming overhead.</span></span>
-   <span data-ttu-id="cc8e7-672">パフォーマンスの向上は、コンパイル済みクエリ上に作成するときに失われます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-672">The performance improvement is lost when composing on top of a compiled query.</span></span>
-   <span data-ttu-id="cc8e7-673">CompiledQuery - 匿名型のプロジェクションなどとしては、いくつかの LINQ クエリを記述することはできません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-673">Some LINQ queries can't be written as a CompiledQuery - for example, projections of anonymous types.</span></span>

### <a name="67-------performance-comparison-of-different-query-options"></a><span data-ttu-id="cc8e7-674">6.7 別のクエリ オプションのパフォーマンス比較</span><span class="sxs-lookup"><span data-stu-id="cc8e7-674">6.7       Performance Comparison of different query options</span></span>

<span data-ttu-id="cc8e7-675">テストにコンテキストの作成がタイムアウトしたいない単純な microbenchmarks に配置されています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-675">Simple microbenchmarks where the context creation was not timed were put to the test.</span></span> <span data-ttu-id="cc8e7-676">一連の管理された環境でのエンティティの非キャッシュの 5000 回のクエリを測定しました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-676">We measured querying 5000 times for a set of non-cached entities in a controlled environment.</span></span> <span data-ttu-id="cc8e7-677">これらの数値が警告を実行するには: アプリケーションによって生成された実際の数値は反映されませんが、どれだけが異なるクエリのオプションを比較するとパフォーマンスの違いの非常に正確な測定では代わりに-公正、新しいコンテキストを作成するコストを除外します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-677">These numbers are to be taken with a warning: they do not reflect actual numbers produced by an application, but instead they are a very accurate measurement of how much of a performance difference there is when different querying options are compared apples-to-apples, excluding the cost of creating a new context.</span></span>

| <span data-ttu-id="cc8e7-678">EF</span><span class="sxs-lookup"><span data-stu-id="cc8e7-678">EF</span></span>  | <span data-ttu-id="cc8e7-679">テスト</span><span class="sxs-lookup"><span data-stu-id="cc8e7-679">Test</span></span>                                 | <span data-ttu-id="cc8e7-680">時間 (ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-680">Time (ms)</span></span> | <span data-ttu-id="cc8e7-681">メモリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-681">Memory</span></span>   |
|:----|:-------------------------------------|:----------|:---------|
| <span data-ttu-id="cc8e7-682">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-682">EF5</span></span> | <span data-ttu-id="cc8e7-683">ObjectContext ESQL</span><span class="sxs-lookup"><span data-stu-id="cc8e7-683">ObjectContext ESQL</span></span>                   | <span data-ttu-id="cc8e7-684">2414</span><span class="sxs-lookup"><span data-stu-id="cc8e7-684">2414</span></span>      | <span data-ttu-id="cc8e7-685">38801408</span><span class="sxs-lookup"><span data-stu-id="cc8e7-685">38801408</span></span> |
| <span data-ttu-id="cc8e7-686">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-686">EF5</span></span> | <span data-ttu-id="cc8e7-687">ObjectContext の Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-687">ObjectContext Linq Query</span></span>             | <span data-ttu-id="cc8e7-688">2692</span><span class="sxs-lookup"><span data-stu-id="cc8e7-688">2692</span></span>      | <span data-ttu-id="cc8e7-689">38277120</span><span class="sxs-lookup"><span data-stu-id="cc8e7-689">38277120</span></span> |
| <span data-ttu-id="cc8e7-690">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-690">EF5</span></span> | <span data-ttu-id="cc8e7-691">DbContext Linq には、追跡をクエリなし</span><span class="sxs-lookup"><span data-stu-id="cc8e7-691">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="cc8e7-692">2818</span><span class="sxs-lookup"><span data-stu-id="cc8e7-692">2818</span></span>      | <span data-ttu-id="cc8e7-693">41840640</span><span class="sxs-lookup"><span data-stu-id="cc8e7-693">41840640</span></span> |
| <span data-ttu-id="cc8e7-694">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-694">EF5</span></span> | <span data-ttu-id="cc8e7-695">DbContext の Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-695">DbContext Linq Query</span></span>                 | <span data-ttu-id="cc8e7-696">2930</span><span class="sxs-lookup"><span data-stu-id="cc8e7-696">2930</span></span>      | <span data-ttu-id="cc8e7-697">41771008</span><span class="sxs-lookup"><span data-stu-id="cc8e7-697">41771008</span></span> |
| <span data-ttu-id="cc8e7-698">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-698">EF5</span></span> | <span data-ttu-id="cc8e7-699">ObjectContext の Linq には、追跡をクエリなし</span><span class="sxs-lookup"><span data-stu-id="cc8e7-699">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="cc8e7-700">3013</span><span class="sxs-lookup"><span data-stu-id="cc8e7-700">3013</span></span>      | <span data-ttu-id="cc8e7-701">38412288</span><span class="sxs-lookup"><span data-stu-id="cc8e7-701">38412288</span></span> |
|     |                                      |           |          |
| <span data-ttu-id="cc8e7-702">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-702">EF6</span></span> | <span data-ttu-id="cc8e7-703">ObjectContext ESQL</span><span class="sxs-lookup"><span data-stu-id="cc8e7-703">ObjectContext ESQL</span></span>                   | <span data-ttu-id="cc8e7-704">2059</span><span class="sxs-lookup"><span data-stu-id="cc8e7-704">2059</span></span>      | <span data-ttu-id="cc8e7-705">46039040</span><span class="sxs-lookup"><span data-stu-id="cc8e7-705">46039040</span></span> |
| <span data-ttu-id="cc8e7-706">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-706">EF6</span></span> | <span data-ttu-id="cc8e7-707">ObjectContext の Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-707">ObjectContext Linq Query</span></span>             | <span data-ttu-id="cc8e7-708">3074</span><span class="sxs-lookup"><span data-stu-id="cc8e7-708">3074</span></span>      | <span data-ttu-id="cc8e7-709">45248512</span><span class="sxs-lookup"><span data-stu-id="cc8e7-709">45248512</span></span> |
| <span data-ttu-id="cc8e7-710">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-710">EF6</span></span> | <span data-ttu-id="cc8e7-711">DbContext Linq には、追跡をクエリなし</span><span class="sxs-lookup"><span data-stu-id="cc8e7-711">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="cc8e7-712">3125</span><span class="sxs-lookup"><span data-stu-id="cc8e7-712">3125</span></span>      | <span data-ttu-id="cc8e7-713">47575040</span><span class="sxs-lookup"><span data-stu-id="cc8e7-713">47575040</span></span> |
| <span data-ttu-id="cc8e7-714">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-714">EF6</span></span> | <span data-ttu-id="cc8e7-715">DbContext の Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-715">DbContext Linq Query</span></span>                 | <span data-ttu-id="cc8e7-716">3420</span><span class="sxs-lookup"><span data-stu-id="cc8e7-716">3420</span></span>      | <span data-ttu-id="cc8e7-717">47652864</span><span class="sxs-lookup"><span data-stu-id="cc8e7-717">47652864</span></span> |
| <span data-ttu-id="cc8e7-718">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-718">EF6</span></span> | <span data-ttu-id="cc8e7-719">ObjectContext の Linq には、追跡をクエリなし</span><span class="sxs-lookup"><span data-stu-id="cc8e7-719">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="cc8e7-720">3593</span><span class="sxs-lookup"><span data-stu-id="cc8e7-720">3593</span></span>      | <span data-ttu-id="cc8e7-721">45260800</span><span class="sxs-lookup"><span data-stu-id="cc8e7-721">45260800</span></span> |

![EF5 ミクロ ベンチマーク、5000 ウォーム イテレーション](~/ef6/media/ef5micro5000warm.png)

![EF6 ミクロ ベンチマーク、5000 ウォーム イテレーション](~/ef6/media/ef6micro5000warm.png)

<span data-ttu-id="cc8e7-724">Microbenchmarks は非常に重視するコードに小さな変更です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-724">Microbenchmarks are very sensitive to small changes in the code.</span></span> <span data-ttu-id="cc8e7-725">Entity Framework 5 のコストと Entity Framework 6 の違いが追加されるため、この場合、[インターセプション](~/ef6/fundamentals/logging-and-interception.md)と[トランザクションの機能強化](~/ef6/saving/transactions.md)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-725">In this case, the difference between the costs of Entity Framework 5 and Entity Framework 6 are due to the addition of [interception](~/ef6/fundamentals/logging-and-interception.md) and [transactional improvements](~/ef6/saving/transactions.md).</span></span> <span data-ttu-id="cc8e7-726">これらの microbenchmarks 番号では、増幅のビジョンの Entity Framework では非常に小さいフラグメントをただしは。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-726">These microbenchmarks numbers, however, are an amplified vision into a very small fragment of what Entity Framework does.</span></span> <span data-ttu-id="cc8e7-727">ウォーム クエリの実際のシナリオでは Entity Framework 6 Entity Framework 5 からアップグレードすると、パフォーマンスの低下は表示されません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-727">Real-world scenarios of warm queries should not see a performance regression when upgrading from Entity Framework 5 to Entity Framework 6.</span></span>

<span data-ttu-id="cc8e7-728">別のクエリ オプションの実際のパフォーマンスを比較するには、5 つの別個のテスト バリエーションは「飲み物」のカテゴリ名を持つすべての製品を選択する別のクエリ オプションを使用しますを作成しました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-728">To compare the real-world performance of the different query options, we created 5 separate test variations where we use a different query option to select all products whose category name is "Beverages".</span></span> <span data-ttu-id="cc8e7-729">各イテレーションには、コンテキストの作成のコストと、返されたすべてのエンティティの具体化のコストが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-729">Each iteration includes the cost of creating the context, and the cost of materializing all returned entities.</span></span> <span data-ttu-id="cc8e7-730">イテレーションは 10 回は、タイムアウトになりました 1000 回の繰り返しの合計を実行する前に untimed で実行されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-730">10 iterations are run untimed before taking the sum of 1000 timed iterations.</span></span> <span data-ttu-id="cc8e7-731">表示される結果は、各テストの回数が 5 回から中央値、実行します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-731">The results shown are the median run taken from 5 runs of each test.</span></span> <span data-ttu-id="cc8e7-732">詳細については、テストのコードを含む付録 B を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-732">For more information, see Appendix B which includes the code for the test.</span></span>

| <span data-ttu-id="cc8e7-733">EF</span><span class="sxs-lookup"><span data-stu-id="cc8e7-733">EF</span></span>  | <span data-ttu-id="cc8e7-734">テスト</span><span class="sxs-lookup"><span data-stu-id="cc8e7-734">Test</span></span>                                        | <span data-ttu-id="cc8e7-735">時間 (ミリ秒)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-735">Time (ms)</span></span> | <span data-ttu-id="cc8e7-736">メモリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-736">Memory</span></span>   |
|:----|:--------------------------------------------|:----------|:---------|
| <span data-ttu-id="cc8e7-737">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-737">EF5</span></span> | <span data-ttu-id="cc8e7-738">ObjectContext エンティティ コマンド</span><span class="sxs-lookup"><span data-stu-id="cc8e7-738">ObjectContext Entity Command</span></span>                | <span data-ttu-id="cc8e7-739">621</span><span class="sxs-lookup"><span data-stu-id="cc8e7-739">621</span></span>       | <span data-ttu-id="cc8e7-740">39350272</span><span class="sxs-lookup"><span data-stu-id="cc8e7-740">39350272</span></span> |
| <span data-ttu-id="cc8e7-741">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-741">EF5</span></span> | <span data-ttu-id="cc8e7-742">データベースに対する Sql クエリを DbContext</span><span class="sxs-lookup"><span data-stu-id="cc8e7-742">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="cc8e7-743">825</span><span class="sxs-lookup"><span data-stu-id="cc8e7-743">825</span></span>       | <span data-ttu-id="cc8e7-744">37519360</span><span class="sxs-lookup"><span data-stu-id="cc8e7-744">37519360</span></span> |
| <span data-ttu-id="cc8e7-745">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-745">EF5</span></span> | <span data-ttu-id="cc8e7-746">ObjectContext のストアのクエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-746">ObjectContext Store Query</span></span>                   | <span data-ttu-id="cc8e7-747">878</span><span class="sxs-lookup"><span data-stu-id="cc8e7-747">878</span></span>       | <span data-ttu-id="cc8e7-748">39460864</span><span class="sxs-lookup"><span data-stu-id="cc8e7-748">39460864</span></span> |
| <span data-ttu-id="cc8e7-749">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-749">EF5</span></span> | <span data-ttu-id="cc8e7-750">ObjectContext の Linq には、追跡をクエリなし</span><span class="sxs-lookup"><span data-stu-id="cc8e7-750">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="cc8e7-751">969</span><span class="sxs-lookup"><span data-stu-id="cc8e7-751">969</span></span>       | <span data-ttu-id="cc8e7-752">38293504</span><span class="sxs-lookup"><span data-stu-id="cc8e7-752">38293504</span></span> |
| <span data-ttu-id="cc8e7-753">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-753">EF5</span></span> | <span data-ttu-id="cc8e7-754">ObjectContext の Entity Sql クエリのオブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-754">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="cc8e7-755">1089</span><span class="sxs-lookup"><span data-stu-id="cc8e7-755">1089</span></span>      | <span data-ttu-id="cc8e7-756">38981632</span><span class="sxs-lookup"><span data-stu-id="cc8e7-756">38981632</span></span> |
| <span data-ttu-id="cc8e7-757">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-757">EF5</span></span> | <span data-ttu-id="cc8e7-758">コンパイル済みクエリの ObjectContext</span><span class="sxs-lookup"><span data-stu-id="cc8e7-758">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="cc8e7-759">1099</span><span class="sxs-lookup"><span data-stu-id="cc8e7-759">1099</span></span>      | <span data-ttu-id="cc8e7-760">38682624</span><span class="sxs-lookup"><span data-stu-id="cc8e7-760">38682624</span></span> |
| <span data-ttu-id="cc8e7-761">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-761">EF5</span></span> | <span data-ttu-id="cc8e7-762">ObjectContext の Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-762">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="cc8e7-763">1152</span><span class="sxs-lookup"><span data-stu-id="cc8e7-763">1152</span></span>      | <span data-ttu-id="cc8e7-764">38178816</span><span class="sxs-lookup"><span data-stu-id="cc8e7-764">38178816</span></span> |
| <span data-ttu-id="cc8e7-765">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-765">EF5</span></span> | <span data-ttu-id="cc8e7-766">DbContext Linq には、追跡をクエリなし</span><span class="sxs-lookup"><span data-stu-id="cc8e7-766">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="cc8e7-767">1208</span><span class="sxs-lookup"><span data-stu-id="cc8e7-767">1208</span></span>      | <span data-ttu-id="cc8e7-768">41803776</span><span class="sxs-lookup"><span data-stu-id="cc8e7-768">41803776</span></span> |
| <span data-ttu-id="cc8e7-769">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-769">EF5</span></span> | <span data-ttu-id="cc8e7-770">DbSet に DbContext Sql クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-770">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="cc8e7-771">1414</span><span class="sxs-lookup"><span data-stu-id="cc8e7-771">1414</span></span>      | <span data-ttu-id="cc8e7-772">37982208</span><span class="sxs-lookup"><span data-stu-id="cc8e7-772">37982208</span></span> |
| <span data-ttu-id="cc8e7-773">EF5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-773">EF5</span></span> | <span data-ttu-id="cc8e7-774">DbContext の Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-774">DbContext Linq Query</span></span>                        | <span data-ttu-id="cc8e7-775">1574</span><span class="sxs-lookup"><span data-stu-id="cc8e7-775">1574</span></span>      | <span data-ttu-id="cc8e7-776">41738240</span><span class="sxs-lookup"><span data-stu-id="cc8e7-776">41738240</span></span> |
|     |                                             |           |          |
| <span data-ttu-id="cc8e7-777">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-777">EF6</span></span> | <span data-ttu-id="cc8e7-778">ObjectContext エンティティ コマンド</span><span class="sxs-lookup"><span data-stu-id="cc8e7-778">ObjectContext Entity Command</span></span>                | <span data-ttu-id="cc8e7-779">480</span><span class="sxs-lookup"><span data-stu-id="cc8e7-779">480</span></span>       | <span data-ttu-id="cc8e7-780">47247360</span><span class="sxs-lookup"><span data-stu-id="cc8e7-780">47247360</span></span> |
| <span data-ttu-id="cc8e7-781">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-781">EF6</span></span> | <span data-ttu-id="cc8e7-782">ObjectContext のストアのクエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-782">ObjectContext Store Query</span></span>                   | <span data-ttu-id="cc8e7-783">493</span><span class="sxs-lookup"><span data-stu-id="cc8e7-783">493</span></span>       | <span data-ttu-id="cc8e7-784">46739456</span><span class="sxs-lookup"><span data-stu-id="cc8e7-784">46739456</span></span> |
| <span data-ttu-id="cc8e7-785">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-785">EF6</span></span> | <span data-ttu-id="cc8e7-786">データベースに対する Sql クエリを DbContext</span><span class="sxs-lookup"><span data-stu-id="cc8e7-786">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="cc8e7-787">614</span><span class="sxs-lookup"><span data-stu-id="cc8e7-787">614</span></span>       | <span data-ttu-id="cc8e7-788">41607168</span><span class="sxs-lookup"><span data-stu-id="cc8e7-788">41607168</span></span> |
| <span data-ttu-id="cc8e7-789">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-789">EF6</span></span> | <span data-ttu-id="cc8e7-790">ObjectContext の Linq には、追跡をクエリなし</span><span class="sxs-lookup"><span data-stu-id="cc8e7-790">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="cc8e7-791">684</span><span class="sxs-lookup"><span data-stu-id="cc8e7-791">684</span></span>       | <span data-ttu-id="cc8e7-792">46333952</span><span class="sxs-lookup"><span data-stu-id="cc8e7-792">46333952</span></span> |
| <span data-ttu-id="cc8e7-793">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-793">EF6</span></span> | <span data-ttu-id="cc8e7-794">ObjectContext の Entity Sql クエリのオブジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-794">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="cc8e7-795">767</span><span class="sxs-lookup"><span data-stu-id="cc8e7-795">767</span></span>       | <span data-ttu-id="cc8e7-796">48865280</span><span class="sxs-lookup"><span data-stu-id="cc8e7-796">48865280</span></span> |
| <span data-ttu-id="cc8e7-797">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-797">EF6</span></span> | <span data-ttu-id="cc8e7-798">コンパイル済みクエリの ObjectContext</span><span class="sxs-lookup"><span data-stu-id="cc8e7-798">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="cc8e7-799">788</span><span class="sxs-lookup"><span data-stu-id="cc8e7-799">788</span></span>       | <span data-ttu-id="cc8e7-800">48467968</span><span class="sxs-lookup"><span data-stu-id="cc8e7-800">48467968</span></span> |
| <span data-ttu-id="cc8e7-801">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-801">EF6</span></span> | <span data-ttu-id="cc8e7-802">DbContext Linq には、追跡をクエリなし</span><span class="sxs-lookup"><span data-stu-id="cc8e7-802">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="cc8e7-803">878</span><span class="sxs-lookup"><span data-stu-id="cc8e7-803">878</span></span>       | <span data-ttu-id="cc8e7-804">47554560</span><span class="sxs-lookup"><span data-stu-id="cc8e7-804">47554560</span></span> |
| <span data-ttu-id="cc8e7-805">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-805">EF6</span></span> | <span data-ttu-id="cc8e7-806">ObjectContext の Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-806">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="cc8e7-807">953</span><span class="sxs-lookup"><span data-stu-id="cc8e7-807">953</span></span>       | <span data-ttu-id="cc8e7-808">47632384</span><span class="sxs-lookup"><span data-stu-id="cc8e7-808">47632384</span></span> |
| <span data-ttu-id="cc8e7-809">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-809">EF6</span></span> | <span data-ttu-id="cc8e7-810">DbSet に DbContext Sql クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-810">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="cc8e7-811">1023</span><span class="sxs-lookup"><span data-stu-id="cc8e7-811">1023</span></span>      | <span data-ttu-id="cc8e7-812">41992192</span><span class="sxs-lookup"><span data-stu-id="cc8e7-812">41992192</span></span> |
| <span data-ttu-id="cc8e7-813">EF6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-813">EF6</span></span> | <span data-ttu-id="cc8e7-814">DbContext の Linq クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-814">DbContext Linq Query</span></span>                        | <span data-ttu-id="cc8e7-815">1290</span><span class="sxs-lookup"><span data-stu-id="cc8e7-815">1290</span></span>      | <span data-ttu-id="cc8e7-816">47529984</span><span class="sxs-lookup"><span data-stu-id="cc8e7-816">47529984</span></span> |


![EF5 ウォーム クエリ 1000 のイテレーション](~/ef6/media/ef5warmquery1000.png)

![EF6 ウォーム クエリ 1000 のイテレーション](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> <span data-ttu-id="cc8e7-819">完全を期すため、EntityCommand を Entity SQL クエリを実行バリエーションの 1 つが含まれます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-819">For completeness, we included a variation where we execute an Entity SQL query on an EntityCommand.</span></span> <span data-ttu-id="cc8e7-820">ただし、そのようなクエリは結果を具体化されません、ため、比較は必ずしも公正にします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-820">However, because results are not materialized for such queries, the comparison isn't necessarily apples-to-apples.</span></span> <span data-ttu-id="cc8e7-821">テストには、近似値してみること、比較を公平に具体化にはが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-821">The test includes a close approximation to materializing to try making the comparison fairer.</span></span>

<span data-ttu-id="cc8e7-822">このエンド ツー エンドの場合は、Entity Framework 6 は、はるかに軽量の DbContext の初期化と高速 MetadataCollection を含む、スタックの複数の部分のパフォーマンス強化による Entity Framework 5 をよりも&lt;T&gt;参照します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-822">In this end-to-end case, Entity Framework 6 outperforms Entity Framework 5 due to performance improvements made on several parts of the stack, including a much lighter DbContext initialization and faster MetadataCollection&lt;T&gt; lookups.</span></span>

## <a name="7-design-time-performance-considerations"></a><span data-ttu-id="cc8e7-823">7 の設計時のパフォーマンスの考慮事項</span><span class="sxs-lookup"><span data-stu-id="cc8e7-823">7 Design time performance considerations</span></span>

### <a name="71-------inheritance-strategies"></a><span data-ttu-id="cc8e7-824">7.1 の継承方法</span><span class="sxs-lookup"><span data-stu-id="cc8e7-824">7.1       Inheritance Strategies</span></span>

<span data-ttu-id="cc8e7-825">別のパフォーマンスの考慮事項 Entity Framework を使用する場合は、使用する継承戦略です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-825">Another performance consideration when using Entity Framework is the inheritance strategy you use.</span></span> <span data-ttu-id="cc8e7-826">Entity Framework には、3 の基本的な種類の継承とそれらの組み合わせがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-826">Entity Framework supports 3 basic types of inheritance and their combinations:</span></span>

-   <span data-ttu-id="cc8e7-827">テーブルごとの階層 (TPH) – 各継承が行で表現されて、階層内でどの特定の種類を示す識別子列を持つテーブルにマップを設定します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-827">Table per Hierarchy (TPH) – where each inheritance set maps to a table with a discriminator column to indicate which particular type in the hierarchy is being represented in the row.</span></span>
-   <span data-ttu-id="cc8e7-828">テーブルごとの種類 (TPT) – それぞれの種類がデータベースでは、独自のテーブルを持ちます子テーブルは、親テーブルが含まれていない列を定義するだけです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-828">Table per Type (TPT) – where each type has its own table in the database; the child tables only define the columns that the parent table doesn’t contain.</span></span>
-   <span data-ttu-id="cc8e7-829">テーブルあたりクラス (TPC) – それぞれの種類がデータベースでは、独自の完全なテーブルを持ちます子テーブルでは、親の型で定義されているものも含め、すべてのフィールドを定義します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-829">Table per Class (TPC) – where each type has its own full table in the database; the child tables define all their fields, including those defined in parent types.</span></span>

<span data-ttu-id="cc8e7-830">モデルは、TPT 継承を使用している場合、生成されたクエリは、他の継承方法、ストアの実行時間が長いになる可能性がありますで生成されるよりも複雑になります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-830">If your model uses TPT inheritance, the queries which are generated will be more complex than those that are generated with the other inheritance strategies, which may result on longer execution times on the store.</span></span>  <span data-ttu-id="cc8e7-831">一般的に時間がかかります、TPT モデルに対するクエリを生成して、結果のオブジェクトを具体化します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-831">It will generally take longer to generate queries over a TPT model, and to materialize the resulting objects.</span></span>

<span data-ttu-id="cc8e7-832">「パフォーマンスの考慮事項 (テーブルの種類ごと) TPT 継承を Entity Framework を使用する場合"を参照してください。 MSDN ブログの投稿: \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-832">See the "Performance Considerations when using TPT (Table per Type) Inheritance in the Entity Framework" MSDN blog post: \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>.</span></span>

#### <a name="711-------avoiding-tpt-in-model-first-or-code-first-applications"></a><span data-ttu-id="cc8e7-833">7.1.1 Model First または Code First アプリケーションで TPT の回避</span><span class="sxs-lookup"><span data-stu-id="cc8e7-833">7.1.1       Avoiding TPT in Model First or Code First applications</span></span>

<span data-ttu-id="cc8e7-834">TPT スキーマを持つ既存のデータベース上にモデルを作成するときに、多くのオプションがありません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-834">When you create a model over an existing database that has a TPT schema, you don't have many options.</span></span> <span data-ttu-id="cc8e7-835">Model First または Code First を使用してアプリケーションを作成する場合は、TPT 継承のパフォーマンスの問題を避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-835">But when creating an application using Model First or Code First, you should avoid TPT inheritance for performance concerns.</span></span>

<span data-ttu-id="cc8e7-836">エンティティ デザイナーのウィザードで Model First を使用すると、モデル内の任意の継承 TPT が表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-836">When you use Model First in the Entity Designer Wizard, you will get TPT for any inheritance in your model.</span></span> <span data-ttu-id="cc8e7-837">Visual Studio ギャラリーから"エンティティ デザイナー データベース生成 Power Pack"使用可能なを使用するには Model First と TPH 継承戦略に切り替える場合は、( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-837">If you want to switch to a TPH inheritance strategy with Model First, you can use the "Entity Designer Database Generation Power Pack" available from the Visual Studio Gallery ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>).</span></span>

<span data-ttu-id="cc8e7-838">Code First を使用して継承でモデルのマッピングを構成する場合は、EF は、TPH を使用して既定では、ため、継承階層内のすべてのエンティティに同じテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-838">When using Code First to configure the mapping of a model with inheritance, EF will use TPH by default, therefore all entities in the inheritance hierarchy will be mapped to the same table.</span></span> <span data-ttu-id="cc8e7-839">MSDN マガジンで「コード最初エンティティ Framework4.1 をでいます」記事の"マッピングに Fluent API"を参照してください ( [ http://msdn.microsoft.com/magazine/hh126815.aspx ](https://msdn.microsoft.com/magazine/hh126815.aspx)) の詳細。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-839">See the "Mapping with the Fluent API" section of the "Code First in Entity Framework4.1" article in MSDN Magazine ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) for more details.</span></span>

### <a name="72-------upgrading-from-ef4-to-improve-model-generation-time"></a><span data-ttu-id="cc8e7-840">7.2 は、モデルの生成を向上させるために EF4 からアップグレードする時間</span><span class="sxs-lookup"><span data-stu-id="cc8e7-840">7.2       Upgrading from EF4 to improve model generation time</span></span>

<span data-ttu-id="cc8e7-841">モデルのストア層 (SSDL) を生成するアルゴリズムに SQL Server に固有の向上は、Visual Studio 2010 SP1 がインストールされているときに Entity Framework 4 への更新および Entity Framework 5 および 6 で利用できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-841">A SQL Server-specific improvement to the algorithm that generates the store-layer (SSDL) of the model is available in Entity Framework 5 and 6, and as an update to Entity Framework 4 when Visual Studio 2010 SP1 is installed.</span></span> <span data-ttu-id="cc8e7-842">次のテスト結果は場合 Navision モデルはこれで、非常に大きなモデルを生成するときに、向上を示します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-842">The following test results demonstrate the improvement when generating a very big model, in this case the Navision model.</span></span> <span data-ttu-id="cc8e7-843">詳細については、付録 C を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-843">See Appendix C for more details about it.</span></span>

<span data-ttu-id="cc8e7-844">モデルには、1005 エンティティ セットおよび 4227 のアソシエーション セットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-844">The model contains 1005 entity sets and 4227 association sets.</span></span>

| <span data-ttu-id="cc8e7-845">構成</span><span class="sxs-lookup"><span data-stu-id="cc8e7-845">Configuration</span></span>                              | <span data-ttu-id="cc8e7-846">消費された時間の内訳</span><span class="sxs-lookup"><span data-stu-id="cc8e7-846">Breakdown of time consumed</span></span>                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cc8e7-847">Visual Studio 2010 で Entity Framework 4</span><span class="sxs-lookup"><span data-stu-id="cc8e7-847">Visual Studio 2010, Entity Framework 4</span></span>     | <span data-ttu-id="cc8e7-848">SSDL の生成: 2 時間の 27 分</span><span class="sxs-lookup"><span data-stu-id="cc8e7-848">SSDL Generation: 2 hr 27 min</span></span> <br/> <span data-ttu-id="cc8e7-849">1 秒間のマッピングの生成:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-849">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-850">CSDL の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="cc8e7-850">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-851">ObjectLayer 生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="cc8e7-851">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-852">ビューの生成: 2 時間 14 分</span><span class="sxs-lookup"><span data-stu-id="cc8e7-852">View Generation: 2 h 14 min</span></span> |
| <span data-ttu-id="cc8e7-853">Visual Studio 2010 SP1、Entity Framework 4</span><span class="sxs-lookup"><span data-stu-id="cc8e7-853">Visual Studio 2010 SP1, Entity Framework 4</span></span> | <span data-ttu-id="cc8e7-854">SSDL の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="cc8e7-854">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-855">1 秒間のマッピングの生成:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-855">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-856">CSDL の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="cc8e7-856">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-857">ObjectLayer 生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="cc8e7-857">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-858">ビューの生成: 1 時間 53 分</span><span class="sxs-lookup"><span data-stu-id="cc8e7-858">View Generation: 1 hr 53 min</span></span>   |
| <span data-ttu-id="cc8e7-859">Visual Studio 2013 で Entity Framework 5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-859">Visual Studio 2013, Entity Framework 5</span></span>     | <span data-ttu-id="cc8e7-860">SSDL の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="cc8e7-860">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-861">1 秒間のマッピングの生成:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-861">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-862">CSDL の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="cc8e7-862">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-863">ObjectLayer 生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="cc8e7-863">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-864">ビューの生成: 65 分</span><span class="sxs-lookup"><span data-stu-id="cc8e7-864">View Generation: 65 minutes</span></span>    |
| <span data-ttu-id="cc8e7-865">Visual Studio 2013 で Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="cc8e7-865">Visual Studio 2013, Entity Framework 6</span></span>     | <span data-ttu-id="cc8e7-866">SSDL の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="cc8e7-866">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-867">1 秒間のマッピングの生成:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-867">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-868">CSDL の生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="cc8e7-868">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-869">ObjectLayer 生成: 1 秒</span><span class="sxs-lookup"><span data-stu-id="cc8e7-869">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="cc8e7-870">ビューの生成: 28 秒。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-870">View Generation: 28 seconds.</span></span>   |


<span data-ttu-id="cc8e7-871">SSDL を生成するときに、負荷がほぼ完全で消費 SQL Server では、クライアントの開発用コンピューターが待機している間に注目すべきアイドル状態が、サーバーから返される結果。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-871">It's worth noting that when generating the SSDL, the load is almost entirely spent on the SQL Server, while the client development machine is waiting idle for results to come back from the server.</span></span> <span data-ttu-id="cc8e7-872">Dba は、この機能強化を特に感謝する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-872">DBAs should particularly appreciate this improvement.</span></span> <span data-ttu-id="cc8e7-873">行われるビューの生成でを今すぐコストは、モデルの生成の本質的には全体の注目すべきです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-873">It's also worth noting that essentially the entire cost of model generation takes place in View Generation now.</span></span>

### <a name="73-------splitting-large-models-with-database-first-and-model-first"></a><span data-ttu-id="cc8e7-874">7.3 データベースでの大規模なモデルを最初に分割して、最初のモデル</span><span class="sxs-lookup"><span data-stu-id="cc8e7-874">7.3       Splitting Large Models with Database First and Model First</span></span>

<span data-ttu-id="cc8e7-875">モデルのサイズの増大に合わせて、デザイナー画面が乱雑および使用するが困難になります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-875">As model size increases, the designer surface becomes cluttered and difficult to use.</span></span> <span data-ttu-id="cc8e7-876">ここには、300 を超えるエンティティを効果的にデザイナーを使用するには大きすぎると、モデル通常検討してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-876">We typically consider a model with more than 300 entities to be too large to effectively use the designer.</span></span> <span data-ttu-id="cc8e7-877">次のブログの投稿が大規模なモデルを分割するためのいくつかのオプションについて説明します。 \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-877">The following blog post describes several options for splitting large models: \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>.</span></span>

<span data-ttu-id="cc8e7-878">投稿は、Entity Framework の最初のバージョンとして記述されましたが、手順が引き続き適用されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-878">The post was written for the first version of Entity Framework, but the steps still apply.</span></span>

### <a name="74-------performance-considerations-with-the-entity-data-source-control"></a><span data-ttu-id="cc8e7-879">7.4 エンティティのデータ ソース コントロールにのパフォーマンスに関する考慮事項</span><span class="sxs-lookup"><span data-stu-id="cc8e7-879">7.4       Performance considerations with the Entity Data Source Control</span></span>

<span data-ttu-id="cc8e7-880">EntityDataSource コントロールを使用して web アプリケーションのパフォーマンスが大幅に低下マルチ スレッドのパフォーマンスとストレス テスト内のケースを説明しました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-880">We've seen cases in multi-threaded performance and stress tests where the performance of a web application using the EntityDataSource Control deteriorates significantly.</span></span> <span data-ttu-id="cc8e7-881">基になる原因は、EntityDataSource は、エンティティとして使用する種類を検出する Web アプリケーションによって参照されるアセンブリの MetadataWorkspace.LoadFromAssembly を繰り返し呼び出します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-881">The underlying cause is that the EntityDataSource repeatedly calls MetadataWorkspace.LoadFromAssembly on the assemblies referenced by the Web application to discover the types to be used as entities.</span></span>

<span data-ttu-id="cc8e7-882">ソリューションでは、ObjectContext は、派生クラスの型名を EntityDataSource の ContextTypeName を設定します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-882">The solution is to set the ContextTypeName of the EntityDataSource to the type name of your derived ObjectContext class.</span></span> <span data-ttu-id="cc8e7-883">エンティティ型のすべての参照アセンブリをスキャンするメカニズムをオフにします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-883">This turns off the mechanism that scans all referenced assemblies for entity types.</span></span>

<span data-ttu-id="cc8e7-884">今回は ContextTypeName フィールドを設定すると、リフレクションを使用してアセンブリから型を読み込めない場合に、.NET 4.0 で EntityDataSource が ReflectionTypeLoadException をスローする場所、機能の問題も回避されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-884">Setting the ContextTypeName field also prevents a functional problem where the EntityDataSource in .NET 4.0 throws a ReflectionTypeLoadException when it can't load a type from an assembly via reflection.</span></span> <span data-ttu-id="cc8e7-885">この問題は .NET 4.5 で修正されました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-885">This issue has been fixed in .NET 4.5.</span></span>

### <a name="75-------poco-entities-and-change-tracking-proxies"></a><span data-ttu-id="cc8e7-886">POCO エンティティと変更追跡プロキシを 7.5</span><span class="sxs-lookup"><span data-stu-id="cc8e7-886">7.5       POCO entities and change tracking proxies</span></span>

<span data-ttu-id="cc8e7-887">Entity Framework では、データ クラス自体に変更を加えずに、データ モデルと共にカスタム データ クラスを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-887">Entity Framework enables you to use custom data classes together with your data model without making any modifications to the data classes themselves.</span></span> <span data-ttu-id="cc8e7-888">つまり、既存のドメイン オブジェクトなどの POCO ("plain-old" CLR object) をデータ モデルで使用できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-888">This means that you can use "plain-old" CLR objects (POCO), such as existing domain objects, with your data model.</span></span> <span data-ttu-id="cc8e7-889">同じクエリのほとんどをサポートして挿入、更新、および Entity Data Model ツールによって生成されるエンティティ型としての動作を削除にデータ モデルで定義されているエンティティにマップされます、こうした POCO データ クラス (永続化非依存オブジェクトとも呼ばれます)。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-889">These POCO data classes (also known as persistence-ignorant objects), which are mapped to entities that are defined in a data model, support most of the same query, insert, update, and delete behaviors as entity types that are generated by the Entity Data Model tools.</span></span>

<span data-ttu-id="cc8e7-890">Entity Framework は、自動変更の追跡 POCO エンティティを遅延読み込みなどの機能を有効にするときに使用される POCO 型から派生したプロキシ クラスを作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-890">Entity Framework can also create proxy classes derived from your POCO types, which are used when you want to enable features such as lazy loading and automatic change tracking on POCO entities.</span></span> <span data-ttu-id="cc8e7-891">POCO クラスは、ここで説明したように、プロキシを使用する Entity Framework を許可する特定の要件を満たす必要があります: [ http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-891">Your POCO classes must meet certain requirements to allow Entity Framework to use proxies, as described here: [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx).</span></span>

<span data-ttu-id="cc8e7-892">可能性の追跡プロキシは、オブジェクト状態マネージャーを毎回変更されると、その値は、Entity Framework は、エンティティの実際の状態を常にわかるように、エンティティのプロパティのいずれかが通知されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-892">Chance tracking proxies will notify the object state manager each time any of the properties of your entities has its value changed, so Entity Framework knows the actual state of your entities all the time.</span></span> <span data-ttu-id="cc8e7-893">これは、プロパティの setter メソッドの本体を通知イベントを追加し、オブジェクト状態マネージャーでこのようなイベントを処理します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-893">This is done by adding notification events to the body of the setter methods of your properties, and having the object state manager processing such events.</span></span> <span data-ttu-id="cc8e7-894">プロキシを作成するエンティティは通常注は、追加した一連の Entity Framework によって作成されたイベントのためのプロキシではない POCO エンティティを作成するよりも高価でください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-894">Note that creating a proxy entity will typically be more expensive than creating a non-proxy POCO entity due to the added set of events created by Entity Framework.</span></span>

<span data-ttu-id="cc8e7-895">POCO エンティティが、変更追跡プロキシを持たない場合に、以前保存された状態のコピーに対して、エンティティの内容を比較することによって変更が見つかりました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-895">When a POCO entity does not have a change tracking proxy, changes are found by comparing the contents of your entities against a copy of a previous saved state.</span></span> <span data-ttu-id="cc8e7-896">この詳細な比較になります時間のかかるプロセスにおいては、多数のエンティティがある場合、またはエンティティがある非常に大量のプロパティ の場合に最後の比較が行われた後に変更しない場合でもです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-896">This deep comparison will become a lengthy process when you have many entities in your context, or when your entities have a very large amount of properties, even if none of them changed since the last comparison took place.</span></span>

<span data-ttu-id="cc8e7-897">概要: パフォーマンス変更追跡プロキシを作成するときに料金がかかりますが、変更の追跡に役立つ、エンティティは、多くのプロパティまたはモデルに多数のエンティティがある場合がある場合は、変更の検出プロセスを高速化します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-897">In summary: you’ll pay a performance hit when creating the change tracking proxy, but change tracking will help you speed up the change detection process when your entities have many properties or when you have many entities in your model.</span></span> <span data-ttu-id="cc8e7-898">プロパティは、エンティティの量膨大にならないほどの数が少ないエンティティは、変更追跡プロキシを持つできない場合がありますのメリットです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-898">For entities with a small number of properties where the amount of entities doesn’t grow too much, having change tracking proxies may not be of much benefit.</span></span>

## <a name="8-loading-related-entities"></a><span data-ttu-id="cc8e7-899">8 の読み込みに関連するエンティティ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-899">8 Loading Related Entities</span></span>

### <a name="81-lazy-loading-vs-eager-loading"></a><span data-ttu-id="cc8e7-900">8.1 の遅延読み込みと一括読み込み</span><span class="sxs-lookup"><span data-stu-id="cc8e7-900">8.1 Lazy Loading vs. Eager Loading</span></span>

<span data-ttu-id="cc8e7-901">Entity Framework では、いくつかのターゲット エンティティに関連するエンティティを読み込む方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-901">Entity Framework offers several different ways to load the entities that are related to your target entity.</span></span> <span data-ttu-id="cc8e7-902">たとえば、製品を照会するときに方法はありますいる、関連する注文が読み込まれるオブジェクトの状態マネージャーにします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-902">For example, when you query for Products, there are different ways that the related Orders will be loaded into the Object State Manager.</span></span> <span data-ttu-id="cc8e7-903">パフォーマンスの観点から、関連エンティティを読み込むときに考慮する最大の質問は Lazy Loading と Eager Loading 使用するかになります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-903">From a performance standpoint, the biggest question to consider when loading related entities will be whether to use Lazy Loading or Eager Loading.</span></span>

<span data-ttu-id="cc8e7-904">Eager Loading を使用する場合、ターゲット エンティティ セットと一緒に関連エンティティが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-904">When using Eager Loading, the related entities are loaded along with your target entity set.</span></span> <span data-ttu-id="cc8e7-905">関連プッシュしたいエンティティを示す、クエリで Include ステートメントを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-905">You use an Include statement in your query to indicate which related entities you want to bring in.</span></span>

<span data-ttu-id="cc8e7-906">Lazy Loading を使用する場合、ターゲット エンティティ セットにのみ、最初のクエリが表示されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-906">When using Lazy Loading, your initial query only brings in the target entity set.</span></span> <span data-ttu-id="cc8e7-907">別のクエリが、関連するエンティティを読み込むストアに対して発行されたナビゲーション プロパティにアクセスするたびにします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-907">But whenever you access a navigation property, another query is issued against the store to load the related entity.</span></span>

<span data-ttu-id="cc8e7-908">エンティティが読み込まれた後にエンティティのクエリから直接読み込むオブジェクト状態マネージャーで、遅延読み込みまたは一括読み込みを使用しているかどうか。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-908">Once an entity has been loaded, any further queries for the entity will load it directly from the Object State Manager, whether you are using lazy loading or eager loading.</span></span>

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a><span data-ttu-id="cc8e7-909">8.2 Lazy Loading と Eager Loading の選択方法</span><span class="sxs-lookup"><span data-stu-id="cc8e7-909">8.2 How to choose between Lazy Loading and Eager Loading</span></span>

<span data-ttu-id="cc8e7-910">重要な点は、アプリケーションの適切な選択を行うことができますように Lazy Loading と Eager Loading の違いを理解することです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-910">The important thing is that you understand the difference between Lazy Loading and Eager Loading so that you can make the correct choice for your application.</span></span> <span data-ttu-id="cc8e7-911">これからは、大きなペイロードを含む可能性のある 1 つの要求ではなく、データベースに対して複数の要求間のトレードオフを評価できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-911">This will help you evaluate the tradeoff between multiple requests against the database versus a single request that may contain a large payload.</span></span> <span data-ttu-id="cc8e7-912">他の部分で、アプリケーションの一部の一括読み込みと遅延読み込みを使用する適切な場合があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-912">It may be appropriate to use eager loading in some parts of your application and lazy loading in other parts.</span></span>

<span data-ttu-id="cc8e7-913">内部的には何が起こっているかの例は、英国およびその注文数に居住する顧客に対してクエリを実行するとします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-913">As an example of what's happening under the hood, suppose you want to query for the customers who live in the UK and their order count.</span></span>

<span data-ttu-id="cc8e7-914">**一括読み込みを使用します。**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-914">**Using Eager Loading**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

<span data-ttu-id="cc8e7-915">**遅延読み込みを使用します。**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-915">**Using Lazy Loading**</span></span>

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

<span data-ttu-id="cc8e7-916">一括読み込みを使用する場合は、すべての顧客を返す 1 つのクエリを発行し、すべての注文します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-916">When using eager loading, you'll issue a single query that returns all customers and all orders.</span></span> <span data-ttu-id="cc8e7-917">ストア コマンドは、ようになります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-917">The store command looks like:</span></span>

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

<span data-ttu-id="cc8e7-918">遅延読み込みを使用する場合は、最初に、次のクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-918">When using lazy loading, you'll issue the following query initially:</span></span>

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

<span data-ttu-id="cc8e7-919">顧客の注文のナビゲーション プロパティにアクセスするたびに、次のような別のクエリは、ストアに対して発行します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-919">And each time you access the Orders navigation property of a customer another query like the following is issued against the store:</span></span>

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

<span data-ttu-id="cc8e7-920">詳細については、次を参照してください。、[関連オブジェクトの読み込み](https://msdn.microsoft.com/library/bb896272.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-920">For more information, see the [Loading Related Objects](https://msdn.microsoft.com/library/bb896272.aspx).</span></span>

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a><span data-ttu-id="cc8e7-921">8.2.1 遅延読み込みと Eager Loading チート シート</span><span class="sxs-lookup"><span data-stu-id="cc8e7-921">8.2.1 Lazy Loading versus Eager Loading cheat sheet</span></span>

<span data-ttu-id="cc8e7-922">遅延読み込みと一括読み込みを選択するという、画一的なものはありません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-922">There’s no such thing as a one-size-fits-all to choosing eager loading versus lazy loading.</span></span> <span data-ttu-id="cc8e7-923">最初に両方の戦略; も十分な情報に基づいて意思決定を行うことができますので違いについて理解するにはまた、次のシナリオのいずれかに、コードに対応するかどうかに検討してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-923">Try first to understand the differences between both strategies so you can do a well informed decision; also, consider if your code fits to any of the following scenarios:</span></span>

| <span data-ttu-id="cc8e7-924">シナリオ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-924">Scenario</span></span>                                                                    | <span data-ttu-id="cc8e7-925">私たちの提案</span><span class="sxs-lookup"><span data-stu-id="cc8e7-925">Our Suggestion</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cc8e7-926">フェッチされたエンティティから多くのナビゲーション プロパティにアクセスする必要がありますか。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-926">Do you need to access many navigation properties from the fetched entities?</span></span> | <span data-ttu-id="cc8e7-927">**いいえ**-両方のオプションが行う可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-927">**No** - Both options will probably do.</span></span> <span data-ttu-id="cc8e7-928">ただし、クエリ結果のペイロードが大きすぎるため、小さい必要がありますが、一括読み込みを使用して、パフォーマンス上の利点がありますがない場合、オブジェクトを具体化するためのラウンド トリップをネットワークします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-928">However, if the payload your query is bringing is not too big, you may experience performance benefits by using Eager loading as it’ll require less network round trips to materialize your objects.</span></span> <br/> <br/> <span data-ttu-id="cc8e7-929">**[はい]** -エンティティから多くのナビゲーション プロパティにアクセスする必要がある場合、複数を使用して、クエリの一括読み込みでのステートメント含めることを行うとします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-929">**Yes** -  If you need to access many navigation properties from the entities, you’d do that by using multiple include statements in your query with Eager loading.</span></span> <span data-ttu-id="cc8e7-930">複数のエンティティを含めると、サイズが大きくなるほど、ペイロードが返されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-930">The more entities you include, the bigger the payload your query will return.</span></span> <span data-ttu-id="cc8e7-931">次の 3 つ以上のエンティティをクエリに含めると、読み込む非同期 (lazy) への切り替えを検討してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-931">Once you include three or more entities into your query, consider switching to Lazy loading.</span></span> |
| <span data-ttu-id="cc8e7-932">実行時にどのようなデータが必要になります正確をご存知でしょうか。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-932">Do you know exactly what data will be needed at run time?</span></span>                   | <span data-ttu-id="cc8e7-933">**いいえ**-遅延読み込みをすることをお勧めになります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-933">**No** - Lazy loading will be better for you.</span></span> <span data-ttu-id="cc8e7-934">それ以外の場合、データのクエリを実行する必要はありません最終的にすることがあります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-934">Otherwise, you may end up querying for data that you will not need.</span></span> <br/> <br/> <span data-ttu-id="cc8e7-935">**[はい]** - Eager の読み込みは、おそらく最善の方法です。 セット全体の読み込みが速くなりますのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-935">**Yes** - Eager loading is probably your best bet; it will help loading entire sets faster.</span></span> <span data-ttu-id="cc8e7-936">クエリでは、非常に大量のデータをフェッチしていますが必要ですされ、これが遅すぎるから読み込む代わりに非同期 (lazy) を再試行してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-936">If your query requires fetching a very large amount of data, and this becomes too slow, then try Lazy loading instead.</span></span>                                                                                                                                                                                                                                                       |
| <span data-ttu-id="cc8e7-937">データベースから遠く離れた、コードを実行しますか。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-937">Is your code executing far from your database?</span></span> <span data-ttu-id="cc8e7-938">(強化されたネットワークの待機時間)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-938">(increased network latency)</span></span>  | <span data-ttu-id="cc8e7-939">**いいえ**ネットワーク待機時間は、問題がない場合 - 遅延読み込みを使用すると、コードが簡略化されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-939">**No** - When the network latency is not an issue, using Lazy loading may simplify your code.</span></span> <span data-ttu-id="cc8e7-940">アプリケーションのトポロジが変わったり、当たり前のデータベースの近接性を使用しないように注意してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-940">Remember that the topology of your application may change, so don’t take database proximity for granted.</span></span> <br/> <br/> <span data-ttu-id="cc8e7-941">**[はい]** - ネットワークには、問題があるシナリオより適切に合ったものを決定できます専用です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-941">**Yes** - When the network is a problem, only you can decide what fits better for your scenario.</span></span> <span data-ttu-id="cc8e7-942">通常より少ないラウンド トリップを必要とするため、一括読み込みは改善されます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-942">Typically Eager loading will be better because it requires fewer round trips.</span></span>                                                                                                                                                                                                      |


#### <a name="822-------performance-concerns-with-multiple-includes"></a><span data-ttu-id="cc8e7-943">8.2.2 複数含むパフォーマンスの問題</span><span class="sxs-lookup"><span data-stu-id="cc8e7-943">8.2.2       Performance concerns with multiple Includes</span></span>

<span data-ttu-id="cc8e7-944">サーバー応答時間に関する問題に関連するパフォーマンスに関する質問を聞くことと、問題の原因が頻繁に複数の Include ステートメントを使用したクエリにします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-944">When we hear performance questions that involve server response time problems, the source of the issue is frequently queries with multiple Include statements.</span></span> <span data-ttu-id="cc8e7-945">関連エンティティを含むクエリでは、強力なは、内部で何が起こっているかを理解する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-945">While including related entities in a query is powerful, it's important to understand what's happening under the covers.</span></span>

<span data-ttu-id="cc8e7-946">ストア コマンドを生成するために、プランを内部コンパイラを経由することで複数の Include ステートメントを持つクエリの比較的長い時間がかかります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-946">It takes a relatively long time for a query with multiple Include statements in it to go through our internal plan compiler to produce the store command.</span></span> <span data-ttu-id="cc8e7-947">結果のクエリを最適化しようとしています。 この時間の大部分が費やされます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-947">The majority of this time is spent trying to optimize the resulting query.</span></span> <span data-ttu-id="cc8e7-948">生成されたストア コマンドには、マッピングによって、各に Outer Join または共用体が含まれます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-948">The generated store command will contain an Outer Join or Union for each Include, depending on your mapping.</span></span> <span data-ttu-id="cc8e7-949">このようなクエリが (たとえば、Include の複数のレベルを使用して走査するペイロード内の冗長性の多くがある場合に特に、帯域幅の問題を acerbate は単一のペイロードで、データベースから大きな接続されているグラフに表示されます。アソシエーションの一対多方向)。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-949">Queries like this will bring in large connected graphs from your database in a single payload, which will acerbate any bandwidth issues, especially when there is a lot of redundancy in the payload (for example, when multiple levels of Include are used to traverse associations in the one-to-many direction).</span></span>

<span data-ttu-id="cc8e7-950">場合、クエリが ToTraceString の使用と、ペイロード サイズを表示する SQL Server Management Studio でストア コマンドを実行したクエリの基になる TSQL へのアクセスを非常に大きなペイロードを返すは場所を確認できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-950">You can check for cases where your queries are returning excessively large payloads by accessing the underlying TSQL for the query by using ToTraceString and executing the store command in SQL Server Management Studio to see the payload size.</span></span> <span data-ttu-id="cc8e7-951">軽減しようとすることができます、このような場合だけに、クエリで Include ステートメントの数を必要なデータを取り込みます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-951">In such cases you can try to reduce the number of Include statements in your query to just bring in the data you need.</span></span> <span data-ttu-id="cc8e7-952">または、たとえば、サブクエリの小さいシーケンスに、クエリを分割することができます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-952">Or you may be able to break your query into a smaller sequence of subqueries, for example:</span></span>

<span data-ttu-id="cc8e7-953">**前に、クエリが侵入します。**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-953">**Before breaking the query:**</span></span>

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

<span data-ttu-id="cc8e7-954">**クエリの重大な: の後**</span><span class="sxs-lookup"><span data-stu-id="cc8e7-954">**After breaking the query:**</span></span>

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

<span data-ttu-id="cc8e7-955">私たちは、追跡対象のクエリでのみ使用できるコンテキストは、identity の解像度との関連付けの修正を自動的に実行する必要がありますを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-955">This will work only on tracked queries, as we are making use of the ability the context has to perform identity resolution and association fixup automatically.</span></span>

<span data-ttu-id="cc8e7-956">遅延読み込みと同様のトレードオフをより小さいペイロードのより多くのクエリとなります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-956">As with lazy loading, the tradeoff will be more queries for smaller payloads.</span></span> <span data-ttu-id="cc8e7-957">エンティティごとから必要なデータのみを明示的に選択する個々 のプロパティのプロジェクションを使用することもできますが、するは読み込まれていないエンティティここと更新はサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-957">You can also use projections of individual properties to explicitly select only the data you need from each entity, but you will not be loading entities in this case, and updates will not be supported.</span></span>

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a><span data-ttu-id="cc8e7-958">8.2.3 回避策は、プロパティの遅延読み込みを取得するには</span><span class="sxs-lookup"><span data-stu-id="cc8e7-958">8.2.3 Workaround to get lazy loading of properties</span></span>

<span data-ttu-id="cc8e7-959">現在 entity Framework は、スカラーまたは複合プロパティの遅延読み込みをサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-959">Entity Framework currently doesn’t support lazy loading of scalar or complex properties.</span></span> <span data-ttu-id="cc8e7-960">ただし、BLOB などのラージ オブジェクトを含むテーブルがある場合、テーブル分割を使用、大きなプロパティを個別のエンティティに分割します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-960">However, in cases where you have a table that includes a large object such as a BLOB, you can use table splitting to separate the large properties into a separate entity.</span></span> <span data-ttu-id="cc8e7-961">たとえば、varbinary フォト列を含む製品テーブルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-961">For example, suppose you have a Product table that includes a varbinary photo column.</span></span> <span data-ttu-id="cc8e7-962">多くの場合、クエリ内でこのプロパティにアクセスする必要はない場合、は、通常必要があるエンティティの部分のみを分割するテーブルを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-962">If you don't frequently need to access this property in your queries, you can use table splitting to bring in only the parts of the entity that you normally need.</span></span> <span data-ttu-id="cc8e7-963">製品の写真を表すエンティティが明示的に必要なときにのみ読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-963">The entity representing the product photo will only be loaded when you explicitly need it.</span></span>

<span data-ttu-id="cc8e7-964">テーブル分割を有効にする方法を示す適切なリソースは、Gil Fink の"テーブル分割の Entity Framework"ブログの投稿: \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-964">A good resource that shows how to enable table splitting is Gil Fink's "Table Splitting in Entity Framework" blog post: \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>.</span></span>

## <a name="9-other-considerations"></a><span data-ttu-id="cc8e7-965">9 他の考慮事項</span><span class="sxs-lookup"><span data-stu-id="cc8e7-965">9 Other considerations</span></span>

### <a name="91------server-garbage-collection"></a><span data-ttu-id="cc8e7-966">9.1 サーバーのガベージ コレクション</span><span class="sxs-lookup"><span data-stu-id="cc8e7-966">9.1      Server Garbage Collection</span></span>

<span data-ttu-id="cc8e7-967">一部のユーザー、ガベージ コレクターが正しく構成されていない場合に予想されるは、並列処理を制限するリソースの競合が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-967">Some users might experience resource contention that limits the parallelism they are expecting when the Garbage Collector is not properly configured.</span></span> <span data-ttu-id="cc8e7-968">EF がマルチ スレッドのシナリオで使用されるか、サーバー側のシステムのような任意のアプリケーションで、必ずサーバーのガベージ コレクションを有効にします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-968">Whenever EF is used in a multithreaded scenario, or in any application that resembles a server-side system, make sure to enable Server Garbage Collection.</span></span> <span data-ttu-id="cc8e7-969">これは、アプリケーション構成ファイルの簡単な設定を使用して行います。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-969">This is done via a simple setting in your application config file:</span></span>

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

<span data-ttu-id="cc8e7-970">スレッドの競合を減らすし、CPU が飽和状態のシナリオで最大 30% してスループットを増やすこのする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-970">This should decrease your thread contention and increase your throughput by up to 30% in CPU saturated scenarios.</span></span> <span data-ttu-id="cc8e7-971">一般的な用語では、常に、アプリケーションの動作 (これはより適切な UI とクライアント側のシナリオ) クラシックのガベージ コレクションを使用してサーバーのガベージ コレクションとをテストする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-971">In general terms, you should always test how your application behaves using the classic Garbage Collection (which is better tuned for UI and client side scenarios) as well as the Server Garbage Collection.</span></span>

### <a name="92------autodetectchanges"></a><span data-ttu-id="cc8e7-972">9.2 AutoDetectChanges</span><span class="sxs-lookup"><span data-stu-id="cc8e7-972">9.2      AutoDetectChanges</span></span>

<span data-ttu-id="cc8e7-973">前述のように、Entity Framework はオブジェクト キャッシュに多数のエンティティがある場合にパフォーマンスの問題を表示可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-973">As mentioned earlier, Entity Framework might show performance issues when the object cache has many entities.</span></span> <span data-ttu-id="cc8e7-974">追加、削除、検索、エントリおよび SaveChanges などの特定の操作では、大量のオブジェクト キャッシュにどのくらいなったに基づいて CPU を消費する可能性があります DetectChanges への呼び出しをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-974">Certain operations, such as Add, Remove, Find, Entry and SaveChanges, trigger calls to DetectChanges which might consume a large amount of CPU based on how large the object cache has become.</span></span> <span data-ttu-id="cc8e7-975">この理由は、オブジェクト キャッシュとオブジェクトの状態マネージャーとして常にお試しがコンテキストに実行されるので、生成されたデータのさまざまなシナリオで正しいことが保証されて操作のたびにできるだけ同期されたことです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-975">The reason for this is that the object cache and the object state manager try to stay as synchronized as possible on each operation performed to a context so that the produced data is guaranteed to be correct under a wide array of scenarios.</span></span>

<span data-ttu-id="cc8e7-976">一般に、Entity Framework の変更の自動検出が、アプリケーションの有効期間にわたって有効のままにすることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-976">It is generally a good practice to leave Entity Framework’s automatic change detection enabled for the entire life of your application.</span></span> <span data-ttu-id="cc8e7-977">高い CPU 使用率が悪影響を及ぼす影響されるシナリオと、プロファイルの場合、原因が DetectChanges への呼び出しであることを示す、コードの重要な部分で一時的にオフ AutoDetectChanges を検討してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-977">If your scenario is being negatively affected by high CPU usage and your profiles indicate that the culprit is the call to DetectChanges, consider temporarily turning off AutoDetectChanges in the sensitive portion of your code:</span></span>

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

<span data-ttu-id="cc8e7-978">AutoDetectChanges を切る前に、Entity Framework エンティティで実行されている変更に関する特定の情報を追跡するには、その機能が失われる可能性を理解することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-978">Before turning off AutoDetectChanges, it’s good to understand that this might cause Entity Framework to lose its ability to track certain information about the changes that are taking place on the entities.</span></span> <span data-ttu-id="cc8e7-979">正しく処理されない場合、アプリケーションでデータの不整合が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-979">If handled incorrectly, this might cause data inconsistency on your application.</span></span> <span data-ttu-id="cc8e7-980">AutoDetectChanges をオフにする詳細については、読み取る\<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-980">For more information on turning off AutoDetectChanges, read \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>.</span></span>

### <a name="93------context-per-request"></a><span data-ttu-id="cc8e7-981">9.3 要求ごとのコンテキスト</span><span class="sxs-lookup"><span data-stu-id="cc8e7-981">9.3      Context per request</span></span>

<span data-ttu-id="cc8e7-982">Entity Framework のコンテキストは、発生する最適なパフォーマンスを提供するために有効期間が短いインスタンスとして使用するものです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-982">Entity Framework’s contexts are meant to be used as short-lived instances in order to provide the most optimal performance experience.</span></span> <span data-ttu-id="cc8e7-983">コンテキストが短くなると予想される有効期間し、破棄されるので、そのため、reutilize 可能であれば、メタデータと非常に軽量な実装されているとします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-983">Contexts are expected to be short lived and discarded, and as such have been implemented to be very lightweight and reutilize metadata whenever possible.</span></span> <span data-ttu-id="cc8e7-984">Web シナリオでは、この点に注意しないコンテキストを持っている 1 つの要求の期間より多くの重要なは。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-984">In web scenarios it’s important to keep this in mind and not have a context for more than the duration of a single request.</span></span> <span data-ttu-id="cc8e7-985">同様に、web 以外のシナリオでコンテキストを破棄する Entity Framework でのキャッシュのさまざまなレベルの理解に基づいて。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-985">Similarly, in non-web scenarios, context should be discarded based on your understanding of the different levels of caching in the Entity Framework.</span></span> <span data-ttu-id="cc8e7-986">一般に、アプリケーションだけでなく、1 つのスレッドのコンテキストと静的コンテキストの有効期間にわたってコンテキスト インスタンスを持つ 1 つは避ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-986">Generally speaking, one should avoid having a context instance throughout the life of the application, as well as contexts per thread and static contexts.</span></span>

### <a name="94------database-null-semantics"></a><span data-ttu-id="cc8e7-987">9.4 データベースの null セマンティクス</span><span class="sxs-lookup"><span data-stu-id="cc8e7-987">9.4      Database null semantics</span></span>

<span data-ttu-id="cc8e7-988">既定では、entity Framework では、C が SQL コードを生成します。\#比較セマンティクスは null です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-988">Entity Framework by default will generate SQL code that has C\# null comparison semantics.</span></span> <span data-ttu-id="cc8e7-989">次のクエリ例を検討してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-989">Consider the following example query:</span></span>

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

<span data-ttu-id="cc8e7-990">この例での SupplierID および単価など、エンティティのプロパティを null 許容型に対して null 許容変数の数を比較しています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-990">In this example, we’re comparing a number of nullable variables against nullable properties on the entity, such as SupplierID and UnitPrice.</span></span> <span data-ttu-id="cc8e7-991">パラメーター値が列の値と同じである場合、または両方のパラメーターと列の値が null の場合、このクエリに対して生成される SQL が求められます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-991">The generated SQL for this query will ask if the parameter value is the same as the column value, or if both the parameter and the column values are null.</span></span> <span data-ttu-id="cc8e7-992">これは、データベース サーバーで null 値の処理方法は非表示にし、一貫性のある C を提供して\#別のデータベース ベンダー間でのエクスペリエンスは null です。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-992">This will hide the way the database server handles nulls and will provide a consistent C\# null experience across different database vendors.</span></span> <span data-ttu-id="cc8e7-993">一方で、生成されたコードは多少複雑な処理し、場合にもを実行しない可能性があります比較の量で、クエリのステートメントは数が多いに増加します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-993">On the other hand, the generated code is a bit convoluted and may not perform well when the amount of comparisons in the where statement of the query grows to a large number.</span></span>

<span data-ttu-id="cc8e7-994">このような状況に対処する方法の 1 つは、データベースの null セマンティクスを使用することです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-994">One way to deal with this situation is by using database null semantics.</span></span> <span data-ttu-id="cc8e7-995">この可能性のある動作が異なる c 注\#Entity Framework は、データベース エンジンは、null 値を処理する方法を公開するより単純な SQL を生成ここでは、以降のセマンティクスを null。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-995">Note that this might potentially behave differently to the C\# null semantics since now Entity Framework will generate simpler SQL that exposes the way the database engine handles null values.</span></span> <span data-ttu-id="cc8e7-996">データベースの null セマンティクスはアクティブ化されたあたりコンテキスト、コンテキストの構成に対して 1 つの 1 つの構成行になります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-996">Database null semantics can be activated per-context with one single configuration line against the context configuration:</span></span>

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

<span data-ttu-id="cc8e7-997">小規模中規模からサイズのクエリしますが、表示されない比較的長いパフォーマンス向上のデータベースの null セマンティクスを使用する場合の違いは潜在的な null 比較の数が多いクエリをさらに顕著になります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-997">Small to medium sized queries will not display a perceptible performance improvement when using database null semantics, but the difference will become more noticeable on queries with a large number of potential null comparisons.</span></span>

<span data-ttu-id="cc8e7-998">上記の例のクエリでは、パフォーマンスの違いは、制御された環境内で実行されている microbenchmark で 2% より小さいをでした。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-998">In the example query above, the performance difference was less than 2% in a microbenchmark running in a controlled environment.</span></span>

### <a name="95------async"></a><span data-ttu-id="cc8e7-999">9.5 非同期</span><span class="sxs-lookup"><span data-stu-id="cc8e7-999">9.5      Async</span></span>

<span data-ttu-id="cc8e7-1000">Entity Framework 6 が導入された .NET 4.5 以降を実行しているときに、非同期操作をサポートします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1000">Entity Framework 6 introduced support of async operations when running on .NET 4.5 or later.</span></span> <span data-ttu-id="cc8e7-1001">IO を持つアプリケーションが競合に関連するほとんどの場合、保存操作と非同期クエリを使用して、最もメリットが。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1001">For the most part, applications that have IO related contention will benefit the most from using asynchronous query and save operations.</span></span> <span data-ttu-id="cc8e7-1002">場合の IO の競合は、アプリケーションの低下がない、非同期は、最適な場合、同期的に実行しまたは最悪の場合の同期呼び出しと同じ量では、結果を返す、単にする非同期タスクの実行を遅らせるおよび使用余分な tim を追加自分のシナリオを完了する電子メール。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1002">If your application does not suffer from IO contention, the use of async will, in the best cases, run synchronously and return the result in the same amount of time as a synchronous call, or in the worst case, simply defer execution to an asynchronous task and add extra time to the completion of your scenario.</span></span>

<span data-ttu-id="cc8e7-1003">非同期のプログラミング作業に役立つかどうかも、非同期には、アプリケーションのパフォーマンスは向上を決定するアクセスについて[ http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1003">For information on how asynchronous programming work that will help you deciding if async will improve the performance of your application visit [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx).</span></span> <span data-ttu-id="cc8e7-1004">Entity Framework での非同期操作の使用に関する詳細については、次を参照してください。[非同期クエリと保存](~/ef6/fundamentals/async.md
)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1004">For more information on the use of async operations on Entity Framework, see [Async Query and Save](~/ef6/fundamentals/async.md
).</span></span>

### <a name="96------ngen"></a><span data-ttu-id="cc8e7-1005">9.6 NGEN</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1005">9.6      NGEN</span></span>

<span data-ttu-id="cc8e7-1006">Entity Framework 6 には、.NET framework の既定のインストールにはなりません。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1006">Entity Framework 6 does not come in the default installation of .NET framework.</span></span> <span data-ttu-id="cc8e7-1007">そのため、Entity Framework のアセンブリは NGEN が既定ですべての Entity Framework コードが他の任意の MSIL アセンブリとして同じ JIT'ing コストの対象であることを意味するの。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1007">As such, the Entity Framework assemblies are not NGEN’d by default which means that all the Entity Framework code is subject to the same JIT’ing costs as any other MSIL assembly.</span></span> <span data-ttu-id="cc8e7-1008">これにより、開発と運用環境でアプリケーションのコールド起動も中に、f5 キーを押してエクスペリエンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1008">This might degrade the F5 experience while developing and also the cold startup of your application in the production environments.</span></span> <span data-ttu-id="cc8e7-1009">JIT'ing の CPU とメモリのコストを削減するために、適切なイメージを Entity Framework ngen ことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1009">In order to reduce the CPU and memory costs of JIT’ing it is advisable to NGEN the Entity Framework images as appropriate.</span></span> <span data-ttu-id="cc8e7-1010">アセンブリを NGEN で Entity Framework 6 の起動時のパフォーマンスを向上させる方法の詳細については、次を参照してください。[アセンブリを NGen で起動時のパフォーマンスを向上させる](~/ef6/fundamentals/performance/ngen.md)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1010">For more information on how to improve the startup performance of Entity Framework 6 with NGEN, see [Improving Startup Performance with NGen](~/ef6/fundamentals/performance/ngen.md).</span></span>

### <a name="97------code-first-versus-edmx"></a><span data-ttu-id="cc8e7-1011">9.7 は code First と EDMX</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1011">9.7      Code First versus EDMX</span></span>

<span data-ttu-id="cc8e7-1012">オブジェクト指向プログラミングと概念モデル (オブジェクト)、ストレージ スキーマ (データベース) との間のマッピングのメモリ内表現することでリレーショナル データベースの間のインピー ダンス ミスマッチ喰いについてエンティティ フレームワークの理由から、2 つです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1012">Entity Framework reasons about the impedance mismatch problem between object oriented programming and relational databases by having an in-memory representation of the conceptual model (the objects), the storage schema (the database) and a mapping between the two.</span></span> <span data-ttu-id="cc8e7-1013">このメタデータには、エンティティ データ モデル、または EDM を短いと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1013">This metadata is called an Entity Data Model, or EDM for short.</span></span> <span data-ttu-id="cc8e7-1014">この EDM では、Entity Framework はラウンドト リップのデータをデータベースにメモリ内のオブジェクトから派生して、ビューと、バックアップを作成します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1014">From this EDM, Entity Framework will derive the views to roundtrip data from the objects in memory to the database and back.</span></span>

<span data-ttu-id="cc8e7-1015">概念モデル、ストレージ スキーマとマッピング、Entity Framework を使用する場合、EDMX ファイルを正式に指定し、モデルの読み込みステージは、EDM が正しいことを検証するのみが (たとえば、必ずマッピングが不足しないこと)、し、ビューを生成、ビューを検証して、このメタデータを使用できる状態があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1015">When Entity Framework is used with an EDMX file that formally specifies the conceptual model, the storage schema, and the mapping, then the model loading stage only has to validate that the EDM is correct (for example, make sure that no mappings are missing), then generate the views, then validate the views and have this metadata ready for use.</span></span> <span data-ttu-id="cc8e7-1016">のみがクエリを実行またはデータ ストアに新しいデータを保存します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1016">Only then can a query be executed or new data be saved to the data store.</span></span>

<span data-ttu-id="cc8e7-1017">Code First アプローチは、感覚的に、高度な Entity Data Model ジェネレーターです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1017">The Code First approach is, at its heart, a sophisticated Entity Data Model generator.</span></span> <span data-ttu-id="cc8e7-1018">Entity Framework で提供されているコード; から EDM を生成するためにはモデル、規則を適用して、Fluent API を使用してモデルの構成に関連するクラスを分析します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1018">The Entity Framework has to produce an EDM from the provided code; it does so by analyzing the classes involved in the model, applying conventions and configuring the model via the Fluent API.</span></span> <span data-ttu-id="cc8e7-1019">EDM を作成すると、Entity Framework 基本的に同じように動作方法されて、プロジェクトに存在する EDMX ファイルがあったとします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1019">After the EDM is built, the Entity Framework essentially behaves the same way as it would had an EDMX file been present in the project.</span></span> <span data-ttu-id="cc8e7-1020">したがって、Code First のモデルの構築は、Entity Framework EDMX を持つと比較すると低速起動時間を変換する余分な複雑さを追加します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1020">Thus, building the model from Code First adds extra complexity that translates into a slower startup time for the Entity Framework when compared to having an EDMX.</span></span> <span data-ttu-id="cc8e7-1021">コストは、サイズと複雑さが構築されるモデルの完全に依存します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1021">The cost is completely dependent on the size and complexity of the model that’s being built.</span></span>

<span data-ttu-id="cc8e7-1022">Code First と EDMX を使用するかは、Code First によって導入された柔軟性に最初に、モデルを構築するためのコストが増えることを知る必要があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1022">When choosing to use EDMX versus Code First, it’s important to know that the flexibility introduced by Code First increases the cost of building the model for the first time.</span></span> <span data-ttu-id="cc8e7-1023">場合、アプリケーションには、この初回使用時の負荷のコストが耐えられる、通常 Code First されます移動することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1023">If your application can withstand the cost of this first-time load then typically Code First will be the preferred way to go.</span></span>

## <a name="10-investigating-performance"></a><span data-ttu-id="cc8e7-1024">調査の 10 のパフォーマンス</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1024">10 Investigating Performance</span></span>

### <a name="101-using-the-visual-studio-profiler"></a><span data-ttu-id="cc8e7-1025">10.1 Visual Studio Profiler を使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1025">10.1 Using the Visual Studio Profiler</span></span>

<span data-ttu-id="cc8e7-1026">Entity Framework のパフォーマンスの問題が発生した場合は、アプリケーションは、時間を費やしている場所を表示する Visual Studio に組み込まれているようにプロファイラーを使用できます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1026">If you are having performance issues with the Entity Framework, you can use a profiler like the one built into Visual Studio to see where your application is spending its time.</span></span> <span data-ttu-id="cc8e7-1027">これは、ツールの「ADO.NET Entity Framework - パート 1 のパフォーマンスを探索」ブログの投稿で円グラフを生成するために使用します ( \< http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>) Entity Framework がコールドとウォームのクエリ中に、時間を費やすを表示します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1027">This is the tool we used to generate the pie charts in the “Exploring the Performance of the ADO.NET Entity Framework - Part 1” blog post ( \<http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>) that show where Entity Framework spends its time during cold and warm queries.</span></span>

<span data-ttu-id="cc8e7-1028">データとモデリングの Customer Advisory Team によって書き込まれた"プロファイルの Entity Framework が Visual Studio 2010 Profiler を使用して"ブログの投稿では、パフォーマンスの問題を調査するプロファイラーを使用する方法の実際の例を示します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1028">The "Profiling Entity Framework using the Visual Studio 2010 Profiler" blog post written by the Data and Modeling Customer Advisory Team shows a real-world example of how they used the profiler to investigate a performance problem.</span></span>  <span data-ttu-id="cc8e7-1029">\<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>.</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1029">\<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>.</span></span> <span data-ttu-id="cc8e7-1030">この投稿は、windows アプリケーション用に記述されています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1030">This post was written for a windows application.</span></span> <span data-ttu-id="cc8e7-1031">Web アプリケーションをプロファイリングする必要がある場合、Windows Performance Recorder (WPR) と Windows パフォーマンス アナライザー (WPA) ツールが Visual Studio からの作業よりも優れた動作する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1031">If you need to profile a web application the Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA) tools may work better than working from Visual Studio.</span></span> <span data-ttu-id="cc8e7-1032">Windows アセスメントおよびデプロイメント キットに含まれており、Windows パフォーマンス ツールキットの一部である WPR および WPA ( [ http://www.microsoft.com/en-US/download/details.aspx?id=39982 ](https://www.microsoft.com/en-US/download/details.aspx?id=39982))。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1032">WPR and WPA are part of the Windows Performance Toolkit which is included with the Windows Assessment and Deployment Kit ( [http://www.microsoft.com/en-US/download/details.aspx?id=39982](https://www.microsoft.com/en-US/download/details.aspx?id=39982)).</span></span>

### <a name="102-applicationdatabase-profiling"></a><span data-ttu-id="cc8e7-1033">10.2 データベース/アプリケーションのプロファイリング</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1033">10.2 Application/Database profiling</span></span>

<span data-ttu-id="cc8e7-1034">Visual Studio に組み込まれているプロファイラーなどのツールは、アプリケーションが時間を費やす場所を伝えます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1034">Tools like the profiler built into Visual Studio tell you where your application is spending time.</span></span>  <span data-ttu-id="cc8e7-1035">プロファイラーの別の型が使用可能な実稼働またはニーズに応じて、実稼働前に、実行中のアプリケーションの動的分析を実行し、よくある落とし穴とデータベースへのアクセスのアンチ パターンの検索をします。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1035">Another type of profiler is available that performs dynamic analysis of your running application, either in production or pre-production depending on needs, and looks for common pitfalls and anti-patterns of database access.</span></span>

<span data-ttu-id="cc8e7-1036">2 つの市販プロファイラーは、Entity Framework Profiler ( \< http://efprof.com>)と ORMProfiler ( \<http://ormprofiler.com>)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1036">Two commercially available profilers are the Entity Framework Profiler ( \<http://efprof.com>) and ORMProfiler ( \<http://ormprofiler.com>).</span></span>

<span data-ttu-id="cc8e7-1037">アプリケーションでは、Code First を使用して MVC アプリケーションは、StackExchange の MiniProfiler を使用することができます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1037">If your application is an MVC application using Code First, you can use StackExchange's MiniProfiler.</span></span> <span data-ttu-id="cc8e7-1038">Scott Hanselman は、彼のブログでこのツールをについて説明します。 \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1038">Scott Hanselman describes this tool in his blog at: \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>.</span></span>

<span data-ttu-id="cc8e7-1039">Julie Lerman の MSDN Magazine の記事「を参照してくださいアプリケーションのデータベース アクティビティのプロファイリングの詳細については[Entity Framework でのデータベース アクティビティのプロファイリング](https://msdn.microsoft.com/magazine/gg490349.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1039">For more information on profiling your application's database activity, see Julie Lerman's MSDN Magazine article titled [Profiling Database Activity in the Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).</span></span>

### <a name="103-database-logger"></a><span data-ttu-id="cc8e7-1040">10.3 データベース ロガー</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1040">10.3 Database logger</span></span>

<span data-ttu-id="cc8e7-1041">Entity Framework 6 を使用している場合も、組み込みのログ記録機能を使用して検討してください。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1041">If you are using Entity Framework 6 also consider using the built-in logging functionality.</span></span> <span data-ttu-id="cc8e7-1042">コンテキストのデータベース プロパティは、単純な 1 行の構成を使用して、アクティビティ ログに記録するように指示することができます。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1042">The Database property of the context can be instructed to log its activity via a simple one-line configuration:</span></span>

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

<span data-ttu-id="cc8e7-1043">この例では、データベース アクティビティをコンソールに記録されますが、ログのプロパティは、任意のアクションを呼び出します。 するように構成できます&lt;文字列&gt;を委任します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1043">In this example the database activity will be logged to the console, but the Log property can be configured to call any Action&lt;string&gt; delegate.</span></span>

<span data-ttu-id="cc8e7-1044">有効にするせず、再コンパイルして、データベースのログ記録は、Entity Framework 6.1 を使用しているか、後で、これを行うアプリケーションの web.config または app.config ファイルでインターセプターを追加することで。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1044">If you want to enable database logging without recompiling, and you are using Entity Framework 6.1 or later, you can do so by adding an interceptor in the web.config or app.config file of your application.</span></span>

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

<span data-ttu-id="cc8e7-1045">詳細に移動してを再コンパイルせずにログ記録を追加する方法について\<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1045">For more information on how to add logging without recompiling go to \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>.</span></span>

## <a name="11-appendix"></a><span data-ttu-id="cc8e7-1046">11 の付録</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1046">11 Appendix</span></span>

### <a name="111-a-test-environment"></a><span data-ttu-id="cc8e7-1047">11.1 A. テスト環境</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1047">11.1 A. Test Environment</span></span>

<span data-ttu-id="cc8e7-1048">この環境では、クライアント アプリケーションから別のコンピューター上のデータベースと 2 台のコンピューターのセットアップを使用します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1048">This environment uses a 2-machine setup with the database on a separate machine from the client application.</span></span> <span data-ttu-id="cc8e7-1049">マシンは、ネットワーク待機時間が比較的低いが、1 台のコンピューター環境よりもより現実的なので、同じラックです。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1049">Machines are in the same rack, so network latency is relatively low, but more realistic than a single-machine environment.</span></span>

#### <a name="1111-------app-server"></a><span data-ttu-id="cc8e7-1050">11.1.1 アプリ サーバー</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1050">11.1.1       App Server</span></span>

##### <a name="11111------software-environment"></a><span data-ttu-id="cc8e7-1051">11.1.1.1 ソフトウェア環境</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1051">11.1.1.1      Software Environment</span></span>

-   <span data-ttu-id="cc8e7-1052">Entity Framework 4 のソフトウェアの環境</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1052">Entity Framework 4 Software Environment</span></span>
    -   <span data-ttu-id="cc8e7-1053">OS 名: Windows Server 2008 R2 Enterprise SP1。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1053">OS Name: Windows Server 2008 R2 Enterprise SP1.</span></span>
    -   <span data-ttu-id="cc8e7-1054">Visual Studio 2010 – Ultimate。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1054">Visual Studio 2010 – Ultimate.</span></span>
    -   <span data-ttu-id="cc8e7-1055">(いくつかの比較) の場合のみの visual Studio 2010 SP1</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1055">Visual Studio 2010 SP1 (only for some comparisons).</span></span>
-   <span data-ttu-id="cc8e7-1056">Entity Framework 5 および 6 ソフトウェア環境</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1056">Entity Framework 5 and 6 Software Environment</span></span>
    -   <span data-ttu-id="cc8e7-1057">Windows 8.1 Enterprise の OS 名:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1057">OS Name: Windows 8.1 Enterprise</span></span>
    -   <span data-ttu-id="cc8e7-1058">Visual Studio 2013 – Ultimate。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1058">Visual Studio 2013 – Ultimate.</span></span>

##### <a name="11112------hardware-environment"></a><span data-ttu-id="cc8e7-1059">11.1.1.2 ハードウェア環境</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1059">11.1.1.2      Hardware Environment</span></span>

-   <span data-ttu-id="cc8e7-1060">デュアル プロセッサ: @ 2.27 GHz intel (r) Xeon(R) CPU L5520 W3530、2261 Mhz8 GHz、4 個のコア、84 の論理プロセッサ。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1060">Dual Processor:     Intel(R) Xeon(R) CPU L5520 W3530 @ 2.27GHz, 2261 Mhz8 GHz, 4 Core(s), 84 Logical Processor(s).</span></span>
-   <span data-ttu-id="cc8e7-1061">2412 GB RamRAM します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1061">2412 GB RamRAM.</span></span>
-   <span data-ttu-id="cc8e7-1062">136 GB SCSI250GB SATA 7200 rpm 3 GB/秒、ドライブが 4 つのパーティションに分割します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1062">136 GB SCSI250GB SATA 7200 rpm 3GB/s drive split into 4 partitions.</span></span>

#### <a name="1112-------db-server"></a><span data-ttu-id="cc8e7-1063">11.1.2 DB サーバー</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1063">11.1.2       DB server</span></span>

##### <a name="11121------software-environment"></a><span data-ttu-id="cc8e7-1064">11.1.2.1 ソフトウェア環境</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1064">11.1.2.1      Software Environment</span></span>

-   <span data-ttu-id="cc8e7-1065">OS 名: Windows Server 2008 の R28.1 Enterprise SP1。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1065">OS Name: Windows Server 2008 R28.1 Enterprise SP1.</span></span>
-   <span data-ttu-id="cc8e7-1066">SQL Server 2008 R22012 します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1066">SQL Server 2008 R22012.</span></span>

##### <a name="11122------hardware-environment"></a><span data-ttu-id="cc8e7-1067">11.1.2.2 ハードウェア環境</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1067">11.1.2.2      Hardware Environment</span></span>

-   <span data-ttu-id="cc8e7-1068">1 つのプロセッサ: intel (r) Xeon(R) CPU L5520 2.27 GHz、2261 MhzES-1620 0 @ 3.60 GHz、4 個のコア、8 の論理プロセッサ。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1068">Single Processor: Intel(R) Xeon(R) CPU L5520  @ 2.27GHz, 2261 MhzES-1620 0 @ 3.60GHz, 4 Core(s), 8 Logical Processor(s).</span></span>
-   <span data-ttu-id="cc8e7-1069">824 GB RamRAM します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1069">824 GB RamRAM.</span></span>
-   <span data-ttu-id="cc8e7-1070">465 GB ATA500GB SATA 7200 rpm 6gb/s ドライブが 4 つのパーティションに分割します。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1070">465 GB ATA500GB SATA 7200 rpm 6GB/s drive split into 4 partitions.</span></span>

### <a name="112------b-query-performance-comparison-tests"></a><span data-ttu-id="cc8e7-1071">11.2 B. クエリのパフォーマンス比較テスト</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1071">11.2      B. Query performance comparison tests</span></span>

<span data-ttu-id="cc8e7-1072">Northwind モデルは、これらのテストの実行に使用されました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1072">The Northwind model was used to execute these tests.</span></span> <span data-ttu-id="cc8e7-1073">これは、Entity Framework デザイナーを使用してデータベースから生成されました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1073">It was generated from the database using the Entity Framework designer.</span></span> <span data-ttu-id="cc8e7-1074">次に、次のコードは、クエリの実行オプションのパフォーマンスを比較に使用しました。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1074">Then, the following code was used to compare the performance of the query execution options:</span></span>

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

### <a name="113-c-navision-model"></a><span data-ttu-id="cc8e7-1075">11.3 C. Navision モデル</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1075">11.3 C. Navision Model</span></span>

<span data-ttu-id="cc8e7-1076">Navision データベースは、Microsoft Dynamics – ナビゲーションのデモに使用される大規模なデータベース</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1076">The Navision database is a large database used to demo Microsoft Dynamics – NAV.</span></span> <span data-ttu-id="cc8e7-1077">生成された概念モデルには、1005 エンティティ セットおよび 4227 のアソシエーション セットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1077">The generated conceptual model contains 1005 entity sets and 4227 association sets.</span></span> <span data-ttu-id="cc8e7-1078">テストで使用されるモデルは「フラット」– 継承が追加されていませんが。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1078">The model used in the test is “flat” – no inheritance has been added to it.</span></span>

#### <a name="1131-queries-used-for-navision-tests"></a><span data-ttu-id="cc8e7-1079">11.3.1 Navision テストに使用されるクエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1079">11.3.1 Queries used for Navision tests</span></span>

<span data-ttu-id="cc8e7-1080">Navision モデルで使用するクエリの一覧には、Entity SQL クエリの 3 つのカテゴリが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1080">The queries list used with the Navision model contains 3 categories of Entity SQL queries:</span></span>

##### <a name="11311-lookup"></a><span data-ttu-id="cc8e7-1081">11.3.1.1 参照</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1081">11.3.1.1 Lookup</span></span>

<span data-ttu-id="cc8e7-1082">集計を持たないシンプルなルックアップ クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1082">A simple lookup query with no aggregations</span></span>

-   <span data-ttu-id="cc8e7-1083">カウント: 16232</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1083">Count: 16232</span></span>
-   <span data-ttu-id="cc8e7-1084">例:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1084">Example:</span></span>

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312-singleaggregating"></a><span data-ttu-id="cc8e7-1085">11.3.1.2 SingleAggregating</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1085">11.3.1.2 SingleAggregating</span></span>

<span data-ttu-id="cc8e7-1086">通常の BI クエリで複数の集計がない小計 (1 つのクエリ)</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1086">A normal BI query with multiple aggregations, but no subtotals (single query)</span></span>

-   <span data-ttu-id="cc8e7-1087">カウント: 2313</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1087">Count: 2313</span></span>
-   <span data-ttu-id="cc8e7-1088">例:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1088">Example:</span></span>

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

<span data-ttu-id="cc8e7-1089">場所 MDF\_SessionLogin\_時間\_Max() がモデルで定義されています。</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1089">Where MDF\_SessionLogin\_Time\_Max() is defined in the model as:</span></span>

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313-aggregatingsubtotals"></a><span data-ttu-id="cc8e7-1090">11.3.1.3 AggregatingSubtotals</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1090">11.3.1.3 AggregatingSubtotals</span></span>

<span data-ttu-id="cc8e7-1091">集計と集計 (すべての和集合) を使用して BI クエリ</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1091">A BI query with aggregations and subtotals (via union all)</span></span>

-   <span data-ttu-id="cc8e7-1092">カウント: 178</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1092">Count: 178</span></span>
-   <span data-ttu-id="cc8e7-1093">例:</span><span class="sxs-lookup"><span data-stu-id="cc8e7-1093">Example:</span></span>

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
