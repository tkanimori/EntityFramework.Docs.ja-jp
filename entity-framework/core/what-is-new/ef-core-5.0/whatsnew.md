---
title: EF Core 5.0 の新機能
author: ajcvickers
ms.date: 03/15/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: 08a93555fd76d8a9f6d3011f59d9a34f76d0b22f
ms.sourcegitcommit: c3b8386071d64953ee68788ef9d951144881a6ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80136250"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="da1e5-102">EF Core 5.0 の新機能</span><span class="sxs-lookup"><span data-stu-id="da1e5-102">What's New in EF Core 5.0</span></span>

<span data-ttu-id="da1e5-103">EF Core 5.0 は現在開発中です。</span><span class="sxs-lookup"><span data-stu-id="da1e5-103">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="da1e5-104">このページには、各プレビューで導入された耳寄りな変更の概要が記載されています。</span><span class="sxs-lookup"><span data-stu-id="da1e5-104">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="da1e5-105">このページには [EF Core 5.0 のプラン](plan.md)を記載していません。</span><span class="sxs-lookup"><span data-stu-id="da1e5-105">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="da1e5-106">このプランでは、最終リリースの出荷前に含めようとしているものすべてを含めた、EF Core 5.0 のテーマ全体について説明します。</span><span class="sxs-lookup"><span data-stu-id="da1e5-106">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="da1e5-107">公開されている公式ドキュメントについては、リンクが追加されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-107">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-1"></a><span data-ttu-id="da1e5-108">Preview 1</span><span class="sxs-lookup"><span data-stu-id="da1e5-108">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="da1e5-109">シンプルなログ</span><span class="sxs-lookup"><span data-stu-id="da1e5-109">Simple logging</span></span>

<span data-ttu-id="da1e5-110">この機能により、EF6 の `Database.Log` に似た機能が追加されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-110">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="da1e5-111">つまり、外部のログ記録フレームワークを構成せずに、EF Core からログを簡単に取得できるようになります。</span><span class="sxs-lookup"><span data-stu-id="da1e5-111">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="da1e5-112">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="da1e5-112">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="da1e5-113">追加のドキュメントは、イシュー [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-113">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="da1e5-114">生成された SQL の取得方法がシンプルに</span><span class="sxs-lookup"><span data-stu-id="da1e5-114">Simple way to get generated SQL</span></span>

<span data-ttu-id="da1e5-115">EF Core 5.0 では、LINQ クエリの実行時に EF Core によって生成される SQL を返す `ToQueryString` 拡張メソッドが導入されています。</span><span class="sxs-lookup"><span data-stu-id="da1e5-115">EF Core 5.0 introduces the `ToQueryString` extension method which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="da1e5-116">暫定版ドキュメントは、[2020 年 1 月 9 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="da1e5-116">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="da1e5-117">追加のドキュメントは、イシュー [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-117">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="da1e5-118">エンティティにキーがないことを示すために C# 属性を使用</span><span class="sxs-lookup"><span data-stu-id="da1e5-118">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="da1e5-119">新しい `KeylessAttribute` を使用したキーを持たないようにエンティティ タイプを構成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-119">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="da1e5-120">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="da1e5-120">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="da1e5-121">ドキュメントは、イシュー [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-121">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="da1e5-122">初期化された DbContext で接続または接続文字列が変更可能に</span><span class="sxs-lookup"><span data-stu-id="da1e5-122">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="da1e5-123">接続や接続文字列を使用しなくても、DbContext インスタンスを簡単に作成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-123">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="da1e5-124">また、コンテキスト インスタンスで接続または接続文字列をミュートできるようになりました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-124">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="da1e5-125">これにより、同じコンテキスト インスタンスを異なるデータベースに動的に接続できるようになります。</span><span class="sxs-lookup"><span data-stu-id="da1e5-125">This allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="da1e5-126">ドキュメントは、イシュー [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-126">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="da1e5-127">変更追跡のプロキシ</span><span class="sxs-lookup"><span data-stu-id="da1e5-127">Change-tracking proxies</span></span>

<span data-ttu-id="da1e5-128">EF Core で、[INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) および [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1) を自動的に実装するランタイム プロキシを生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-128">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="da1e5-129">これにより、エンティティ プロパティの値の変更が EF Core に直接報告されるため、変更をスキャンする必要がなくなります。</span><span class="sxs-lookup"><span data-stu-id="da1e5-129">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="da1e5-130">ただし、プロキシには独自の制限のセットが付属しているため、すべてのユーザーが使用できるわけではありません</span><span class="sxs-lookup"><span data-stu-id="da1e5-130">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="da1e5-131">ドキュメントは、イシュー [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-131">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="da1e5-132">強化されたデバッグ ビュー</span><span class="sxs-lookup"><span data-stu-id="da1e5-132">Enhanced debug views</span></span>

<span data-ttu-id="da1e5-133">デバッグ ビューで、イシューのデバッグ時に EF Core の内部を簡単に確認できます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-133">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="da1e5-134">モデルのデバッグ ビューは少し前に実装されました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-134">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="da1e5-135">EF Core 5.0 では、モデル ビューを読みやすくし、状態マネージャーの追跡対象エンティティの新しいデバッグ ビューを追加しました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-135">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="da1e5-136">暫定版のドキュメントは、[2019 年 12 月 12 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="da1e5-136">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="da1e5-137">追加のドキュメントは、イシュー [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-137">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="da1e5-138">データベースの null セマンティクスの処理の向上</span><span class="sxs-lookup"><span data-stu-id="da1e5-138">Improved handling of database null semantics</span></span>

<span data-ttu-id="da1e5-139">リレーショナル データベースは通常、NULL を不明な値として扱うため、他の NULL とは等しくありません。</span><span class="sxs-lookup"><span data-stu-id="da1e5-139">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="da1e5-140">一方、C# では、null は、他の null と等しいかどうかを比較する定義済みの値として扱われます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-140">C#, on the other hand, treats null as a defined value which compares equal to any other null.</span></span>
<span data-ttu-id="da1e5-141">EF Core は、既定で C# の null セマンティクスを使用できるようにクエリを変換します。</span><span class="sxs-lookup"><span data-stu-id="da1e5-141">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="da1e5-142">EF Core 5.0 では、この変換の効率が大幅に向上します。</span><span class="sxs-lookup"><span data-stu-id="da1e5-142">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="da1e5-143">ドキュメントは、イシュー [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-143">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="da1e5-144">インデクサーのプロパティ</span><span class="sxs-lookup"><span data-stu-id="da1e5-144">Indexer properties</span></span>

<span data-ttu-id="da1e5-145">EF Core 5.0 では、C# インデクサー プロパティのマッピングがサポートされています。</span><span class="sxs-lookup"><span data-stu-id="da1e5-145">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="da1e5-146">これにより、エンティティはプロパティ バッグとして機能し、列がバッグの名前付きプロパティにマップされます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-146">This allows entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="da1e5-147">ドキュメントは、イシュー [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-147">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="da1e5-148">列挙型マッピングの CHECK 制約の生成</span><span class="sxs-lookup"><span data-stu-id="da1e5-148">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="da1e5-149">EF Core 5.0 の移行で、列挙型プロパティのマッピングに CHECK 制約を生成できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-149">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="da1e5-150">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="da1e5-150">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="da1e5-151">ドキュメントは、イシュー [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-151">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="da1e5-152">IsRelational</span><span class="sxs-lookup"><span data-stu-id="da1e5-152">IsRelational</span></span>

<span data-ttu-id="da1e5-153">既存の `IsSqlServer`、`IsSqlite`、`IsInMemory` に加えて、新しい `IsRelational` メソッドが追加されました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-153">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="da1e5-154">これは、DbContext がリレーショナル データベース プロバイダーを使用しているかどうかをテストするために使用できます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-154">This can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="da1e5-155">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="da1e5-155">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="da1e5-156">ドキュメントは、イシュー [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-156">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="da1e5-157">ETag を使用した Cosmos オプティミスティック同時実行制御</span><span class="sxs-lookup"><span data-stu-id="da1e5-157">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="da1e5-158">Azure Cosmos DB データベース プロバイダーで、Etag を使用したオプティミスティック同時実行制御がサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-158">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="da1e5-159">OnModelCreating のモデル ビルダーを使用して ETag を構成します。</span><span class="sxs-lookup"><span data-stu-id="da1e5-159">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="da1e5-160">SaveChanges はコンカレンシーの競合に対して `DbUpdateConcurrencyException` をスローします。これを[処理して](https://docs.microsoft.com/ef/core/saving/concurrency)、たとえば再試行を実装することができます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-160">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>


<span data-ttu-id="da1e5-161">ドキュメントは、イシュー [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-161">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="da1e5-162">より多くの DateTime コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="da1e5-162">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="da1e5-163">新しい DateTime のコンストラクトが含まれるクエリを変換できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-163">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="da1e5-164">さらに、次の SQL Server 関数がマップされました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-164">In addition, the following SQL Server functions are now mapped:</span></span>
* <span data-ttu-id="da1e5-165">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="da1e5-165">DateDiffWeek</span></span>
* <span data-ttu-id="da1e5-166">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="da1e5-166">DateFromParts</span></span>

<span data-ttu-id="da1e5-167">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="da1e5-167">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="da1e5-168">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-168">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="da1e5-169">より多くのバイト配列コンストラクトに対するクエリ変換</span><span class="sxs-lookup"><span data-stu-id="da1e5-169">Query translations for more byte array constructs</span></span>

<span data-ttu-id="da1e5-170">byte[] プロパティで Contains、Length、SequenceEqual などを使用するクエリが SQL に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-170">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="da1e5-171">暫定版のドキュメントは、[2019 年 12 月 5 日の EF 週次ステータス](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)に含まれています。</span><span class="sxs-lookup"><span data-stu-id="da1e5-171">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="da1e5-172">追加のドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-172">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="da1e5-173">Reverse のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="da1e5-173">Query translation for Reverse</span></span>

<span data-ttu-id="da1e5-174">`Reverse` を使用したクエリが変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-174">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="da1e5-175">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="da1e5-175">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="da1e5-176">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-176">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="da1e5-177">ビット処理演算子のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="da1e5-177">Query translation for bitwise operators</span></span>

<span data-ttu-id="da1e5-178">ビット処理演算子を使用するクエリが、次のようなケースでも変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-178">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="da1e5-179">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-179">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="da1e5-180">Cosmos の文字列のクエリ変換</span><span class="sxs-lookup"><span data-stu-id="da1e5-180">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="da1e5-181">文字列メソッド Contains、StartsWith、EndsWith を使用するクエリが、Azure Cosmos DB プロバイダーの使用時に変換されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="da1e5-181">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="da1e5-182">ドキュメントは、イシュー [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="da1e5-182">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
