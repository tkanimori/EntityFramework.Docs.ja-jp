---
title: EF Core 5.0 の新機能
author: ajcvickers
ms.date: 01/29/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: e858379cc46abbef999fd32a3685e1d522524889
ms.sourcegitcommit: 89567d08c9d8bf9c33bb55a62f17067094a4065a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77052018"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="03347-102">EF Core 5.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="03347-102">What's New in EF Core 5.0</span></span>

<span data-ttu-id="03347-103">EF Core 5.0 は現在開発中です。</span><span class="sxs-lookup"><span data-stu-id="03347-103">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="03347-104">このページには、各プレビューで導入された耳寄りな変更の概要が記載されています。</span><span class="sxs-lookup"><span data-stu-id="03347-104">This page will contain an overview of interesting changes introduced in each preview.</span></span>
<span data-ttu-id="03347-105">EF Core 5.0 の最初のプレビューは、現在のところ 2020 年の第一四半期になると予想されています。</span><span class="sxs-lookup"><span data-stu-id="03347-105">The first preview of EF Core 5.0 is tentatively expected in in the first quarter of 2020.</span></span>

<span data-ttu-id="03347-106">このページには [EF Core 5.0 のプラン](plan.md)を記載していません。</span><span class="sxs-lookup"><span data-stu-id="03347-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="03347-107">このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 5.0 のテーマ全体について説明します。</span><span class="sxs-lookup"><span data-stu-id="03347-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="03347-108">公開されている公式ドキュメントについては、リンクが追加されます。</span><span class="sxs-lookup"><span data-stu-id="03347-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-1-not-yet-shipped"></a><span data-ttu-id="03347-109">プレビュー 1 (未出荷)</span><span class="sxs-lookup"><span data-stu-id="03347-109">Preview 1 (Not yet shipped)</span></span>

### <a name="simple-logging"></a><span data-ttu-id="03347-110">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="03347-110">Simple logging</span></span>

<span data-ttu-id="03347-111">この機能により、EF6 の `Database.Log` に似た機能が追加されます。</span><span class="sxs-lookup"><span data-stu-id="03347-111">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="03347-112">つまり、外部のログ記録フレームワークを構成せずに、EF Core からログを簡単に取得できるようになります。</span><span class="sxs-lookup"><span data-stu-id="03347-112">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="03347-113">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="03347-113">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="03347-114">追加のドキュメントは、イシュー [#2085](https://github.com/aspnet/EntityFramework.Docs/issues/2085) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-114">Additional documentation is tracked by issue [#2085](https://github.com/aspnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="03347-115">生成された SQL の取得方法がシンプルに</span><span class="sxs-lookup"><span data-stu-id="03347-115">Simple way to get generated SQL</span></span>

<span data-ttu-id="03347-116">EF Core 5.0 では、LINQ クエリの実行時に EF Core によって生成される SQL を返す `ToQueryString` 拡張メソッドが導入されています。</span><span class="sxs-lookup"><span data-stu-id="03347-116">EF Core 5.0 introduces the `ToQueryString` extension method which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="03347-117">暫定版ドキュメントは、[2020 年 1 月 9 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="03347-117">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="03347-118">追加のドキュメントは、イシュー [#1331](https://github.com/aspnet/EntityFramework.Docs/issues/1331) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-118">Additional documentation is tracked by issue [#1331](https://github.com/aspnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="03347-119">強化されたデバッグ ビュー</span><span class="sxs-lookup"><span data-stu-id="03347-119">Enhanced debug views</span></span>

<span data-ttu-id="03347-120">デバッグ ビューで、イシューのデバッグ時に EF Core の内部を簡単に確認できます。</span><span class="sxs-lookup"><span data-stu-id="03347-120">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="03347-121">モデルのデバッグ ビューは少し前に実装されました。</span><span class="sxs-lookup"><span data-stu-id="03347-121">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="03347-122">EF Core 5.0 では、モデル ビューを読みやすくし、状態マネージャーの追跡対象エンティティの新しいデバッグ ビューを追加しました。</span><span class="sxs-lookup"><span data-stu-id="03347-122">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="03347-123">暫定版のドキュメントは、[2019 年 12 月 12 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="03347-123">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="03347-124">追加のドキュメントは、イシュー [#2086](https://github.com/aspnet/EntityFramework.Docs/issues/2086) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-124">Additional documentation is tracked by issue [#2086](https://github.com/aspnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="03347-125">初期化された DbContext で接続または接続文字列が変更可能に</span><span class="sxs-lookup"><span data-stu-id="03347-125">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="03347-126">接続や接続文字列を使用しなくても、DbContext インスタンスを簡単に作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03347-126">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="03347-127">また、コンテキスト インスタンスで接続または接続文字列をミュートできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03347-127">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="03347-128">これにより、同じコンテキスト インスタンスを異なるデータベースに動的に接続できるようになります。</span><span class="sxs-lookup"><span data-stu-id="03347-128">This allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="03347-129">ドキュメントは、イシュー [#2075](https://github.com/aspnet/EntityFramework.Docs/issues/2075) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-129">Documentation is tracked by issue [#2075](https://github.com/aspnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="03347-130">変更追跡のプロキシ</span><span class="sxs-lookup"><span data-stu-id="03347-130">Change-tracking proxies</span></span>

<span data-ttu-id="03347-131">EF Core で、[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) および [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) を自動的に実装するランタイム プロキシを生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03347-131">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="03347-132">これにより、エンティティ プロパティの値の変更が EF Core に直接報告されるため、変更をスキャンする必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="03347-132">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="03347-133">ただし、プロキシには独自の制限のセットが付属しているため、すべてのユーザーが使用できるわけではありません</span><span class="sxs-lookup"><span data-stu-id="03347-133">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="03347-134">ドキュメントは、イシュー [#2076](https://github.com/aspnet/EntityFramework.Docs/issues/2076) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-134">Documentation is tracked by issue [#2076](https://github.com/aspnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="03347-135">データベースの null セマンティクスの処理の向上</span><span class="sxs-lookup"><span data-stu-id="03347-135">Improved handling of database null semantics</span></span>

<span data-ttu-id="03347-136">リレーショナル データベースは通常、NULL を不明な値として扱うため、他の NULL とは等しくありません。</span><span class="sxs-lookup"><span data-stu-id="03347-136">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="03347-137">一方、C# では、null は、他の null と等しいかどうかを比較する定義済みの値として扱われます。</span><span class="sxs-lookup"><span data-stu-id="03347-137">C#, on the other hand, treats null as a defined value which compares equal to any other null.</span></span>
<span data-ttu-id="03347-138">EF Core は、既定で C# の null セマンティクスを使用できるようにクエリを変換します。</span><span class="sxs-lookup"><span data-stu-id="03347-138">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="03347-139">EF Core 5.0 では、この変換の効率が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="03347-139">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="03347-140">ドキュメントは、イシュー [#1612](https://github.com/aspnet/EntityFramework.Docs/issues/1612) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-140">Documentation is tracked by issue [#1612](https://github.com/aspnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="03347-141">インデクサーのプロパティ</span><span class="sxs-lookup"><span data-stu-id="03347-141">Indexer properties</span></span>

<span data-ttu-id="03347-142">EF Core 5.0 では、C# インデクサー プロパティのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="03347-142">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="03347-143">これにより、エンティティはプロパティ バッグとして機能し、列がバッグの名前付きプロパティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="03347-143">This allows entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="03347-144">ドキュメントは、イシュー [#2018](https://github.com/aspnet/EntityFramework.Docs/issues/2018) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-144">Documentation is tracked by issue [#2018](https://github.com/aspnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="03347-145">列挙型マッピングの CHECK 制約の生成</span><span class="sxs-lookup"><span data-stu-id="03347-145">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="03347-146">EF Core 5.0 の移行で、列挙型プロパティのマッピングに CHECK 制約を生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03347-146">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="03347-147">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="03347-147">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="03347-148">ドキュメントは、イシュー [#2082](https://github.com/aspnet/EntityFramework.Docs/issues/2082) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-148">Documentation is tracked by issue [#2082](https://github.com/aspnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="03347-149">より多くの DateTime コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="03347-149">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="03347-150">新しい DataTime のコンストラクトが含まれるクエリを変換できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03347-150">Queries containing new DataTime construction are now translated.</span></span>
<span data-ttu-id="03347-151">また、SQL Server 関数 DateDiffWeek がマップされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03347-151">Also, the SQL Server function DateDiffWeek is now mapped.</span></span>

<span data-ttu-id="03347-152">ドキュメントは、イシュー [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-152">Documentation is tracked by issue [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="03347-153">より多くのバイト配列コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="03347-153">Query translations for more byte array constructs</span></span>

<span data-ttu-id="03347-154">byte[] プロパティで Contains、Length、SequenceEqual などを使用するクエリが SQL に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03347-154">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="03347-155">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="03347-155">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="03347-156">追加のドキュメントは、イシュー [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-156">Additional documentation is tracked by issue [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="03347-157">Reverse のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="03347-157">Query translation for Reverse</span></span>

<span data-ttu-id="03347-158">`Reverse` を使用したクエリが変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03347-158">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="03347-159">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="03347-159">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="03347-160">ドキュメントは、イシュー [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-160">Documentation is tracked by issue [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="03347-161">ビット処理演算子のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="03347-161">Query translation for bitwise operators</span></span>

<span data-ttu-id="03347-162">ビット処理演算子を使用するクエリが、次のようなケースでも変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03347-162">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="03347-163">ドキュメントは、イシュー [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-163">Documentation is tracked by issue [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="03347-164">Cosmos の文字列のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="03347-164">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="03347-165">文字列メソッド Contains、StartsWith、EndsWith を使用するクエリが、Azure Cosmos DB プロバイダーの使用時に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="03347-165">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="03347-166">ドキュメントは、イシュー [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="03347-166">Documentation is tracked by issue [#2079](https://github.com/aspnet/EntityFramework.Docs/issues/2079).</span></span>
