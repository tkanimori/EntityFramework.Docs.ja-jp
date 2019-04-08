---
title: EF Core 3.0 での破壊的変更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: fd593b2832a5a6ffe27cd4493127b5d405f684ba
ms.sourcegitcommit: ce44f85a5bce32ef2d3d09b7682108d3473511b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "58914128"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="e2b08-102">EF Core 3.0 (現在プレビュー段階) に含まれる破壊的変更</span><span class="sxs-lookup"><span data-stu-id="e2b08-102">Breaking changes included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e2b08-103">機能セットと今後のリリースのスケジュールは、常に変更される可能性があることに注意してください。また、このページを最新の状態に保持するようにしていますが、最新のプランが反映されていない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="e2b08-104">以下の API と動作変更により、3.0.0 へのアップグレード時に、EF Core 2.2.x 用に開発されたアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-104">The following API and behavior changes have the potential to break applications developed for EF Core 2.2.x when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="e2b08-105">データベース プロバイダーにのみ影響することが予想される変更については、[プロバイダーの変更](../../providers/provider-log.md)に関するページに記載されています。</span><span class="sxs-lookup"><span data-stu-id="e2b08-105">Changes that we expect to only impact database providers are documented under [provider changes](../../providers/provider-log.md).</span></span>
<span data-ttu-id="e2b08-106">ある 3.0 プレビューから別の 3.0 プレビューに導入される新機能での中断については、ここには記載されていません。</span><span class="sxs-lookup"><span data-stu-id="e2b08-106">Breaks in new features introduced from one 3.0 preview to another 3.0 preview aren't documented here.</span></span>

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="e2b08-107">LINQ クエリがクライアントで評価されなくなった</span><span class="sxs-lookup"><span data-stu-id="e2b08-107">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="e2b08-108">[問題 #14935 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[問題 #12795 も参照](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="e2b08-108">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="e2b08-109">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-109">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-110">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-110">Old behavior</span></span>**

<span data-ttu-id="e2b08-111">3.0 より前では、EF Core では、SQL またはパラメーターに対するクエリの一部だった式を変換できない場合、クライアントで自動的に式が評価されていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-111">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="e2b08-112">既定では、コストの高い式のクライアント評価でのみ警告がトリガーされました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-112">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

**<span data-ttu-id="e2b08-113">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-113">New behavior</span></span>**

<span data-ttu-id="e2b08-114">3.0 以降では、クライアントで評価される最上位のプロジェクション (クエリの最後の `Select()` 呼び出し) の式のみが EF Core で許可されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-114">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="e2b08-115">クエリの他の部分の式を SQL やパラメーターに変換できない場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-115">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

**<span data-ttu-id="e2b08-116">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-116">Why</span></span>**

<span data-ttu-id="e2b08-117">クエリの自動クライアント評価では、多くのクエリを実行できます。これらの重要な部分を変換できない場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="e2b08-117">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="e2b08-118">この動作が原因で、予期しない損害を与える動作となる可能性があります。この動作は、運用環境でのみ見られる場合があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-118">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="e2b08-119">たとえば、変換できない `Where()` 呼び出しの条件が原因で、テーブルのすべての行がデータベース サーバーから転送できるようになり、クライアントでフィルターを適用できるようになる場合があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-119">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="e2b08-120">このような状況は、開発時にテーブルにわずかな行が含まれている場合は、簡単に検出される可能性があります。しかし、テーブルに数百万もの行が含まれる、運用環境にアプリケーションを移行する場合は難しくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-120">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="e2b08-121">クライアント評価の警告では、開発中にあまりに簡単に無視されることも示されていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-121">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="e2b08-122">これだけでなく、自動クライアント評価は、特定の式のクエリ変換の改善が、リリース間の予期しない破壊的変更の原因となる問題につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-122">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

**<span data-ttu-id="e2b08-123">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-123">Mitigations</span></span>**

<span data-ttu-id="e2b08-124">クエリを完全に変換できない場合は、変換できる形式でクエリを書き直すか、`AsEnumerable()`、`ToList()`、または同様のものを使用して、LINQ-to-Objects を使ってさらに処理できるクライアントに明示的にデータを戻します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-124">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="e2b08-125">Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="e2b08-125">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="e2b08-126">問題 Announcements#325 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-126">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="e2b08-127">この変更は、ASP.NET Core 3.0 プレビュー 1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-127">This change was introduced in ASP.NET Core 3.0-preview 1.</span></span> 

**<span data-ttu-id="e2b08-128">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-128">Old behavior</span></span>**

<span data-ttu-id="e2b08-129">ASP.NET Core 3.0 より前では、パッケージ参照を `Microsoft.AspNetCore.App` または `Microsoft.AspNetCore.All` に追加したときに、EF Core と、SQL Server プロバイダーのような EF Core データ プロバイダーの一部が含まれました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-129">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

**<span data-ttu-id="e2b08-130">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-130">New behavior</span></span>**

<span data-ttu-id="e2b08-131">3.0 以降では、ASP.NET Core 共有フレームワークには、EF Core も、いずれの EF Core データ プロバイダーも含まれません。</span><span class="sxs-lookup"><span data-stu-id="e2b08-131">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

**<span data-ttu-id="e2b08-132">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-132">Why</span></span>**

<span data-ttu-id="e2b08-133">この変更の前では、アプリケーションのターゲットが ASP.NET Core であるか SQL Server であるかに応じて、EF Core を取得するには異なる手順が必要でした。</span><span class="sxs-lookup"><span data-stu-id="e2b08-133">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="e2b08-134">また、ASP.NET Core のアップグレードでは、必ずしも適切だとは言えない、EF Core と SQL Server プロバイダーのアップグレードが強制されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-134">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="e2b08-135">この変更では、EF Core の取得のエクスペリエンスは、.NET の実装とアプリケーションの種類がサポートされる、すべてのプロバイダーで同じになります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-135">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="e2b08-136">また、開発者は、EF Core と EF Core データ プロバイダーのアップグレードのタイミングを正確に制御できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-136">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

**<span data-ttu-id="e2b08-137">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-137">Mitigations</span></span>**

<span data-ttu-id="e2b08-138">ASP.NET Core 3.0 アプリケーションまたはその他のサポートされるアプリケーションで EF Core を使用するには、アプリケーションで使用される EF Core データベース プロバイダーにパッケージ参照を明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-138">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

## <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="e2b08-139">FromSql、ExecuteSql、および ExecuteSqlAsync の名前変更</span><span class="sxs-lookup"><span data-stu-id="e2b08-139">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="e2b08-140">問題 #10996 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-140">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="e2b08-141">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-141">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-142">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-142">Old behavior</span></span>**

<span data-ttu-id="e2b08-143">EF Core 3.0 より前のバージョンでは、通常の文字列または SQL およびパラメーターに挿入する必要がある文字列で使用するために、これらのメソッド名がオーバーロードされました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-143">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

**<span data-ttu-id="e2b08-144">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-144">New behavior</span></span>**

<span data-ttu-id="e2b08-145">EF Core 3.0 以降では、`FromSqlRaw`、`ExecuteSqlRaw`、および `ExecuteSqlRawAsync` を使用して、パラメーターがクエリ文字列とは別に渡される、パラメーター化クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-145">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="e2b08-146">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-146">For example:</span></span>

```C#
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="e2b08-147">`FromSqlInterpolated`、`ExecuteSqlInterpolated`、および `ExecuteSqlInterpolatedAsync` を使用して、パラメーターが挿入クエリ文字列の一部として渡されるパラメーター化クエリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-147">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="e2b08-148">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-148">For example:</span></span>

```C#
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="e2b08-149">上記のクエリはどちらも、同じ SQL パラメーターを持つ同じパラメーター化 SQL が生成されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e2b08-149">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

**<span data-ttu-id="e2b08-150">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-150">Why</span></span>**

<span data-ttu-id="e2b08-151">このようなメソッド オーバーロードは、挿入文字列メソッドを呼び出すつもりが、誤って raw 文字列メソッドを非常に簡単に呼び出せてしまいます。その逆も同様です。</span><span class="sxs-lookup"><span data-stu-id="e2b08-151">Method overloads like this make it very easy to accidentally call the raw srting method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="e2b08-152">これは、クエリをパラメーター化する必要があるときに、パラメーター化されない結果になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-152">This could result in queries not being parameterized when they should have been.</span></span>

**<span data-ttu-id="e2b08-153">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-153">Mitigations</span></span>**

<span data-ttu-id="e2b08-154">新しいメソッド名を使用するように切り替えます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-154">Switch to use the new method names.</span></span>

## <a name="query-execution-is-logged-at-debug-level"></a><span data-ttu-id="e2b08-155">クエリの実行がデバッグ レベルでログに記録される</span><span class="sxs-lookup"><span data-stu-id="e2b08-155">Query execution is logged at Debug level</span></span>

[<span data-ttu-id="e2b08-156">問題 #14523 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-156">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="e2b08-157">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-157">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-158">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-158">Old behavior</span></span>**

<span data-ttu-id="e2b08-159">EF Core 3.0 より前では、クエリと他のコマンドの実行は、`Info` レベルでログに記録されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-159">Before EF Core 3.0, execution of queries and other commands was logged at the `Info` level.</span></span>

**<span data-ttu-id="e2b08-160">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-160">New behavior</span></span>**

<span data-ttu-id="e2b08-161">EF Core 3.0 以降では、コマンド/SQL の実行は、`Debug` レベルでログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-161">Starting with EF Core 3.0, logging of command/SQL execution is at the `Debug` level.</span></span>

**<span data-ttu-id="e2b08-162">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-162">Why</span></span>**

<span data-ttu-id="e2b08-163">この変更は、`Info` ログ レベルでのノイズを減らすために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-163">This change was made to reduce the noise at the `Info` log level.</span></span>

**<span data-ttu-id="e2b08-164">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-164">Mitigations</span></span>**

<span data-ttu-id="e2b08-165">このログ イベントは、イベント ID 20100 の `RelationalEventId.CommandExecuting` によって定義されています。</span><span class="sxs-lookup"><span data-stu-id="e2b08-165">This logging event is defined by `RelationalEventId.CommandExecuting` with event ID 20100.</span></span>
<span data-ttu-id="e2b08-166">`Info` レベルで SQL をもう一度ログに記録するには、`OnConfiguring` または `AddDbContext` で明示的にレベルを構成します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-166">To log SQL at the `Info` level again, explicitly configure the level in `OnConfiguring` or `AddDbContext`.</span></span>
<span data-ttu-id="e2b08-167">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-167">For example:</span></span>
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Info)));
```

## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="e2b08-168">一時キーの値がエンティティ インスタンスに設定されなくなった</span><span class="sxs-lookup"><span data-stu-id="e2b08-168">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="e2b08-169">問題 #12378 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-169">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="e2b08-170">この変更は、EF Core 3.0 プレビュー 2 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-170">This change was introduced in EF Core 3.0-preview 2.</span></span>

**<span data-ttu-id="e2b08-171">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-171">Old behavior</span></span>**

<span data-ttu-id="e2b08-172">EF Core 3.0 より前では、後で実際の値がデータベースによって生成される、すべてのキー プロパティに一時的な値が割り当てられていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-172">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="e2b08-173">通常、これらの一時的な値は大きい負の数値でした。</span><span class="sxs-lookup"><span data-stu-id="e2b08-173">Usually these temporary values were large negative numbers.</span></span>

**<span data-ttu-id="e2b08-174">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-174">New behavior</span></span>**

<span data-ttu-id="e2b08-175">3.0 以降では、EF Core で、エンティティの追跡情報の一部として一時キーの値が格納され、キー プロパティ自体はそのままになります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-175">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

**<span data-ttu-id="e2b08-176">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-176">Why</span></span>**

<span data-ttu-id="e2b08-177">この変更は、何らかの `DbContext` インスタンスによって以前に追跡されたエンティティが、別の `DbContext` インスタンスに移動されるときに、一時キーの値が誤って永続的なものにならないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-177">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

**<span data-ttu-id="e2b08-178">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-178">Mitigations</span></span>**

<span data-ttu-id="e2b08-179">主キーの値を外部キーに割り当てて、エンティティ間の関連付けを形成するアプリケーションは、主キーがストアで生成されており、`Added` 状態のエンティティに属している場合、以前の動作に依存する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-179">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="e2b08-180">これは、次のようにして回避できます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-180">This can be avoided by:</span></span>
* <span data-ttu-id="e2b08-181">ストア生成キーを使用しない。</span><span class="sxs-lookup"><span data-stu-id="e2b08-181">Not using store-generated keys.</span></span>
* <span data-ttu-id="e2b08-182">外部キーの値を設定するのではなく、ナビゲーション プロパティを設定してリレーションシップを形成する。</span><span class="sxs-lookup"><span data-stu-id="e2b08-182">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="e2b08-183">エンティティの追跡情報から実際の一時キーの値を取得する。</span><span class="sxs-lookup"><span data-stu-id="e2b08-183">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="e2b08-184">たとえば、`context.Entry(blog).Property(e => e.Id).CurrentValue` では、`blog.Id` 自体が設定されていない場合でも、一時的な値が返されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-184">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

## <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="e2b08-185">DetectChanges でストア生成キーの値が優先される</span><span class="sxs-lookup"><span data-stu-id="e2b08-185">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="e2b08-186">問題 #14616 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-186">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="e2b08-187">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-187">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-188">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-188">Old behavior</span></span>**

<span data-ttu-id="e2b08-189">EF Core 3.0 より前では、`DetectChanges` によって検出された追跡対象外のエンティティが `Added` 状態で追跡され、`SaveChanges` が呼び出されたときに新しい行として挿入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-189">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

**<span data-ttu-id="e2b08-190">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-190">New behavior</span></span>**

<span data-ttu-id="e2b08-191">EF Core 3.0 以降では、エンティティで生成されたキーの値を使用し、何らかのキーの値が設定されている場合、そのエンティティは `Modified` 状態で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-191">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="e2b08-192">つまり、エンティティの行が存在していると見なされ、`SaveChanges` の呼び出し時に更新されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-192">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="e2b08-193">キーの値が設定されていないか、エンティティ型で生成されたキーが使用されない場合、新しいエンティティは引き続き、以前のバージョンと同様に `Added` として追跡されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-193">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

**<span data-ttu-id="e2b08-194">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-194">Why</span></span>**

<span data-ttu-id="e2b08-195">この変更は、ストア生成キーの使用中に、切り離されたエンティティ グラフをより簡単に一貫して操作できるようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-195">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

**<span data-ttu-id="e2b08-196">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-196">Mitigations</span></span>**

<span data-ttu-id="e2b08-197">エンティティ型が生成されたキーを使用するように構成されているものの、キーの値が新しいインスタンスに対して明示的に設定されている場合、この変更によってアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-197">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="e2b08-198">この問題を解決するには、キー プロパティで生成された値が使用されないように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-198">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="e2b08-199">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-199">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="e2b08-200">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-200">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="e2b08-201">既定で連鎖削除がすぐに行われるようになった</span><span class="sxs-lookup"><span data-stu-id="e2b08-201">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="e2b08-202">問題 #10114 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-202">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="e2b08-203">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-203">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-204">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-204">Old behavior</span></span>**

<span data-ttu-id="e2b08-205">3.0 より前では、EF Core で適用された連鎖操作 (必要なプリンシパルが削除されたか、必要なプリンシパルへのリレーションシップが切断されたときに依存エンティティを削除する) は、SaveChanges が呼び出されるまで行われませんでした。</span><span class="sxs-lookup"><span data-stu-id="e2b08-205">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

**<span data-ttu-id="e2b08-206">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-206">New behavior</span></span>**

<span data-ttu-id="e2b08-207">3.0 以降では、EF Core で、トリガー条件が検出されるとすぐに連鎖操作が適用されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-207">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="e2b08-208">たとえば、プリンシパル エンティティを削除するために `context.Remove()` を呼び出すと、すべての追跡対象の関連する必要な依存関係もすぐに `Deleted` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-208">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

**<span data-ttu-id="e2b08-209">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-209">Why</span></span>**

<span data-ttu-id="e2b08-210">この変更は、`SaveChanges` が呼び出される_前に_どのエンティティが削除されるかを把握することが重要である、データ バインディングおよび監査シナリオのエクスペリエンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-210">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

**<span data-ttu-id="e2b08-211">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-211">Mitigations</span></span>**

<span data-ttu-id="e2b08-212">`context.ChangedTracker` の設定を使用して、以前の動作を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-212">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="e2b08-213">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-213">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="e2b08-214">クエリ型がエンティティ型と統合される</span><span class="sxs-lookup"><span data-stu-id="e2b08-214">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="e2b08-215">問題 #14194 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-215">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="e2b08-216">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-216">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-217">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-217">Old behavior</span></span>**

<span data-ttu-id="e2b08-218">EF Core 3.0 より前では、[クエリ型](xref:core/modeling/query-types)は、構造化された方法で主キーが定義されないデータのクエリを実行するための手段でした。</span><span class="sxs-lookup"><span data-stu-id="e2b08-218">Before EF Core 3.0, [query types](xref:core/modeling/query-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="e2b08-219">つまり、クエリ型は、キーが利用できるときに (テーブルからの可能性が高くなりますが、ビューからの可能性もあります) 通常のエンティティ型が使用された場合に、キーなしの (ビューからの可能性が高くなりますが、テーブルからの可能性もあります) エンティティ型をマップするために使用されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-219">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

**<span data-ttu-id="e2b08-220">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-220">New behavior</span></span>**

<span data-ttu-id="e2b08-221">クエリ型は、現在、主キーなしの単なるエンティティ型になります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-221">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="e2b08-222">キーなしのエンティティ型の機能は、以前のバージョンのクエリ型と同じです。</span><span class="sxs-lookup"><span data-stu-id="e2b08-222">Keyless entity types have the same functionality as query types in previous versions.</span></span>

**<span data-ttu-id="e2b08-223">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-223">Why</span></span>**

<span data-ttu-id="e2b08-224">この変更は、クエリ型の目的に関する混乱を減らすために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-224">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="e2b08-225">つまり、これらはキーなしのエンティティ型であるため、本質的には読み取り専用となりますが、単にエンティティ型を読み取り専用にする必要があるという理由で使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="e2b08-225">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="e2b08-226">同様に、これらは多くの場合、ビューにマップされますが、これは、単にビューでは多くの場合、キーが定義されないためです。</span><span class="sxs-lookup"><span data-stu-id="e2b08-226">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

**<span data-ttu-id="e2b08-227">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-227">Mitigations</span></span>**

<span data-ttu-id="e2b08-228">API の以下の部分は使用されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-228">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="e2b08-229">**`ModelBuilder.Query<>()`** - 代わりに `ModelBuilder.Entity<>().HasNoKey()` を呼び出して、エンティティ型をキーなしとしてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-229">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="e2b08-230">これは、規則により、主キーは必要であるが、規則と一致しない場合に構成エラーを回避するようにはまだ構成されません。</span><span class="sxs-lookup"><span data-stu-id="e2b08-230">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="e2b08-231">**`DbQuery<>`** - 代わりに `DbSet<>` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-231">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="e2b08-232">**`DbContext.Query<>()`** - 代わりに `DbContext.Set<>()` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-232">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="e2b08-233">所有型のリレーションシップ用の構成 API が変更された</span><span class="sxs-lookup"><span data-stu-id="e2b08-233">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="e2b08-234">[問題 #12444 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[問題 #9148 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[問題 #14153 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="e2b08-234">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="e2b08-235">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-235">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-236">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-236">Old behavior</span></span>**

<span data-ttu-id="e2b08-237">EF Core 3.0 より前では、所有リレーションシップの構成は、`OwnsOne` または `OwnsMany` の呼び出し直後に実行されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-237">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

**<span data-ttu-id="e2b08-238">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-238">New behavior</span></span>**

<span data-ttu-id="e2b08-239">EF Core 3.0 以降では、fluent API で、`WithOwner()` を使用して、所有者に対してナビゲーション プロパティが構成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-239">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="e2b08-240">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-240">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="e2b08-241">現在、所有者と所有型間のリレーションシップに関する構成は、他のリレーションシップの構成方法と同様、`WithOwner()` 後にチェーンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-241">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="e2b08-242">一方、所有型自体の構成は引き続き、`OwnsOne()/OwnsMany()` 後にチェーンされます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-242">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="e2b08-243">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-243">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");
            
        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

<span data-ttu-id="e2b08-244">さらに所有型ターゲットでの `Entity()`、`HasOne()`、または `Set()` の呼び出しでは、例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-244">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

**<span data-ttu-id="e2b08-245">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-245">Why</span></span>**

<span data-ttu-id="e2b08-246">この変更は、所有型自体と、所有型_へのリレーションシップ_の構成を明確に分離するために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-246">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="e2b08-247">これにより、`HasForeignKey` のようなメソッドに関するあいまいさと混乱はなくなります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-247">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

**<span data-ttu-id="e2b08-248">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-248">Mitigations</span></span>**

<span data-ttu-id="e2b08-249">上記の例で示すように、新しい API サーフェスを使用するように、所有型リレーションシップの構成を変更します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-249">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="e2b08-250">プリンシパルとテーブルを共有する依存エンティティが省略可能になりました</span><span class="sxs-lookup"><span data-stu-id="e2b08-250">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="e2b08-251">問題 #9005 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-251">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="e2b08-252">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-252">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-253">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-253">Old behavior</span></span>**

<span data-ttu-id="e2b08-254">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="e2b08-254">Consider the following model:</span></span>
```C#
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```
<span data-ttu-id="e2b08-255">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、新しい `Order` を追加する場合には常に `OrderDetails` インスタンスが必要でした。</span><span class="sxs-lookup"><span data-stu-id="e2b08-255">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


**<span data-ttu-id="e2b08-256">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-256">New behavior</span></span>**

<span data-ttu-id="e2b08-257">EF Core 3.0 以降では、`OrderDetails` なしで `Order` を追加することができ、主キー以外のすべての `OrderDetails` プロパティが NULL 値が許可される列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-257">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="e2b08-258">`OrderDetails` は、必要なプロパティのいずれにも値がない場合、または主キー以外に必要なプロパティがなく、すべてのプロパティが `null` の場合、EF Core のクエリの実行時に `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-258">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

**<span data-ttu-id="e2b08-259">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-259">Mitigations</span></span>**

<span data-ttu-id="e2b08-260">モデルに省略可能なすべての列と依存関係を共有するテーブルがあるが、それを指すナビゲーションが `null` になることが想定されていない場合は、ナビゲーションが `null` の場合のケースを処理するようにアプリケーションを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-260">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="e2b08-261">これができない場合は、必要なプロパティをエンティティ型に追加するか、少なくとも 1 つのプロパティに割り当てられている値が `null` 以外である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-261">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

## <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="e2b08-262">同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-262">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="e2b08-263">問題 #14154 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-263">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="e2b08-264">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-264">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-265">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-265">Old behavior</span></span>**

<span data-ttu-id="e2b08-266">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="e2b08-266">Consider the following model:</span></span>
```C#
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```
<span data-ttu-id="e2b08-267">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、`OrderDetails` だけを更新してもクライアント上の `Version` 値は更新されず、次回の更新が失敗します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-267">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


**<span data-ttu-id="e2b08-268">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-268">New behavior</span></span>**

<span data-ttu-id="e2b08-269">EF Core 3.0 以降では、新しい `Version` 値が `OrderDetails` を所有している場合には `Order` に伝達されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-269">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="e2b08-270">それ以外の場合は、モデルの検証中に例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-270">Otherwise an exception is thrown during model validation.</span></span>

**<span data-ttu-id="e2b08-271">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-271">Why</span></span>**

<span data-ttu-id="e2b08-272">この変更は、エンティティの 1 つだけが同じテーブルにマップされている場合に、古い同時実行トークン値が更新されるのを回避するために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-272">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

**<span data-ttu-id="e2b08-273">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-273">Mitigations</span></span>**

<span data-ttu-id="e2b08-274">テーブルを共有するすべてのエンティティには、同時実行トークン列にマップされるプロパティを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-274">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="e2b08-275">シャドウ状態で作成することができます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-275">It's possible the create one in shadow-state:</span></span>
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

## <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="e2b08-276">マップされていない型から継承されたプロパティは、すべての派生型の 1 つの列にマップされるようになりました</span><span class="sxs-lookup"><span data-stu-id="e2b08-276">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="e2b08-277">問題 #13998 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-277">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="e2b08-278">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-278">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-279">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-279">Old behavior</span></span>**

<span data-ttu-id="e2b08-280">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="e2b08-280">Consider the following model:</span></span>
```C#
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

<span data-ttu-id="e2b08-281">EF Core 3.0 より前のバージョンでは、`ShippingAddress` プロパティは既定で `BulkOrder` と `Order` の個別の列にマップされていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-281">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

**<span data-ttu-id="e2b08-282">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-282">New behavior</span></span>**

<span data-ttu-id="e2b08-283">EF Core 3.0 以降では、`ShippingAddress` に対して 1 つの列だけが作成されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-283">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

**<span data-ttu-id="e2b08-284">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-284">Why</span></span>**

<span data-ttu-id="e2b08-285">以前の動作は予期しないものでした。</span><span class="sxs-lookup"><span data-stu-id="e2b08-285">The old behavoir was unexpected.</span></span>

**<span data-ttu-id="e2b08-286">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-286">Mitigations</span></span>**

<span data-ttu-id="e2b08-287">プロパティは、派生型の個別の列に引き続き明示的にマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-287">The property can still be explicitly mapped to separate column on the derived types:</span></span>

```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="e2b08-288">外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった</span><span class="sxs-lookup"><span data-stu-id="e2b08-288">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="e2b08-289">問題 #13274 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-289">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="e2b08-290">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-290">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-291">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-291">Old behavior</span></span>**

<span data-ttu-id="e2b08-292">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="e2b08-292">Consider the following model:</span></span>
```C#
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```
<span data-ttu-id="e2b08-293">EF Core 3.0 より前では、規則により、外部キーで `CustomerId` プロパティが使用されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-293">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="e2b08-294">しかし、`Order` が所有型である場合、`CustomerId` も主キーとマークされ、これは通常、期待されることではありません。</span><span class="sxs-lookup"><span data-stu-id="e2b08-294">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

**<span data-ttu-id="e2b08-295">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-295">New behavior</span></span>**

<span data-ttu-id="e2b08-296">EF Core 3.0 以降では、プリンシパル プロパティと同じ名前である場合、規則により、外部キーに対するプロパティの使用は試行されません。</span><span class="sxs-lookup"><span data-stu-id="e2b08-296">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="e2b08-297">プリンシパル プロパティ名と連結されたプリンシパル型名、およびプリンシパル プロパティ名パターンと連結されたナビゲーション名は、引き続き一致します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-297">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="e2b08-298">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-298">For example:</span></span>

```C#
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```C#
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

**<span data-ttu-id="e2b08-299">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-299">Why</span></span>**

<span data-ttu-id="e2b08-300">この変更は、所有型に主キー プロパティが誤って定義されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-300">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

**<span data-ttu-id="e2b08-301">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-301">Mitigations</span></span>**

<span data-ttu-id="e2b08-302">プロパティを外部キーにする、したがって、主キーの一部にする予定だった場合は、そのように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-302">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

## <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="e2b08-303">データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになりました</span><span class="sxs-lookup"><span data-stu-id="e2b08-303">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="e2b08-304">問題 #14218 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-304">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="e2b08-305">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-305">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-306">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-306">Old behavior</span></span>**

<span data-ttu-id="e2b08-307">EF Core 3.0 より前のバージョンでは、コンテキストにより `TransactionScope` 内部で接続が開かれると、現在の `TransactionScope` がアクティブの間、接続が開いたままになります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-307">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

```C#
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point
        
        var categories = context.ProductCategories().ToList();
    }
}
```

**<span data-ttu-id="e2b08-308">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-308">New behavior</span></span>**

<span data-ttu-id="e2b08-309">EF Core 3.0 以降では、使用が終了したらすぐに接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-309">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

**<span data-ttu-id="e2b08-310">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-310">Why</span></span>**

<span data-ttu-id="e2b08-311">この変更により、同じ `TransactionScope` で複数のコンテキストを使用できます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-311">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="e2b08-312">新しい動作は、EF6 とほぼ一致します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-312">The new behavior alose matches EF6.</span></span>

**<span data-ttu-id="e2b08-313">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-313">Mitigations</span></span>**

<span data-ttu-id="e2b08-314">接続を開いたままにする必要がある場合は、`OpenConnection()` を明示的に呼び出して、EF Core が途中で閉じないようにします。</span><span class="sxs-lookup"><span data-stu-id="e2b08-314">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

```C#
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();
        
        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="e2b08-315">各プロパティで独立したメモリ内整数キー生成が使用される</span><span class="sxs-lookup"><span data-stu-id="e2b08-315">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="e2b08-316">問題 #6872 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-316">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="e2b08-317">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-317">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-318">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-318">Old behavior</span></span>**

<span data-ttu-id="e2b08-319">EF Core 3.0 より前では、1 つの共有値ジェネレーターが、すべてのメモリ内整数キー プロパティで使用されていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-319">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

**<span data-ttu-id="e2b08-320">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-320">New behavior</span></span>**

<span data-ttu-id="e2b08-321">EF Core 3.0 以降では、各整数キー プロパティで、メモリ内データベースを使用するときに独自の値ジェネレーターが取得されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-321">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="e2b08-322">また、データベースが削除された場合は、すべてのテーブルに対してキーの生成がリセットされます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-322">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

**<span data-ttu-id="e2b08-323">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-323">Why</span></span>**

<span data-ttu-id="e2b08-324">この変更は、実際のデータベース キー生成により近くなるようにメモリ内キー生成を調整するため、また、メモリ内データベースを使用するときに互いのテストを分離させる機能を向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-324">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

**<span data-ttu-id="e2b08-325">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-325">Mitigations</span></span>**

<span data-ttu-id="e2b08-326">これにより、設定される特定のメモリ内キー値に依存しているアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-326">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="e2b08-327">代わりに特定のキーの値に依存しないようにするか、あるいは新しい動作と一致するように更新することを検討します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-327">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

## <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="e2b08-328">バッキング フィールドが既定で使用される</span><span class="sxs-lookup"><span data-stu-id="e2b08-328">Backing fields are used by default</span></span>

[<span data-ttu-id="e2b08-329">問題 #12430 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-329">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="e2b08-330">この変更は、EF Core 3.0 プレビュー 2 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-330">This change was introduced in EF Core 3.0-preview 2.</span></span>

**<span data-ttu-id="e2b08-331">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-331">Old behavior</span></span>**

<span data-ttu-id="e2b08-332">3.0 より前では、プロパティのバッキング フィールドがわかっていた場合でも、EF Core では引き続き、既定でプロパティの getter および setter メソッドを使用して、プロパティ値の読み取りと書き込みが行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-332">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="e2b08-333">ただし、バッキング フィールドが直接設定されることがわかっている場合のクエリの実行は例外です。</span><span class="sxs-lookup"><span data-stu-id="e2b08-333">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

**<span data-ttu-id="e2b08-334">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-334">New behavior</span></span>**

<span data-ttu-id="e2b08-335">EF Core 3.0 以降では、プロパティのバッキング フィールドがわかっている場合、バッキング フィールドを使用して、常にそのプロパティの読み取りと書き込みが行われます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-335">Starting with EF Core 3.0, if the backing field for a property is known, then will always read and write that property using the backing field.</span></span>
<span data-ttu-id="e2b08-336">アプリケーションが getter および setter メソッドにコード化された追加動作に依存している場合、これによりアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-336">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

**<span data-ttu-id="e2b08-337">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-337">Why</span></span>**

<span data-ttu-id="e2b08-338">この変更は、EF Core が、エンティティに関するデータベース操作の実行時に、既定でビジネス ロジックを誤ってトリガーしないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-338">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

**<span data-ttu-id="e2b08-339">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-339">Mitigations</span></span>**

<span data-ttu-id="e2b08-340">3.0 より前の動作は、modelBuilder fluent API でプロパティ アクセス モードの構成を使用して、復元できます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-340">The pre-3.0 behavior can be restored through configuration of the property access mode in the modelBuilder fluent API.</span></span>
<span data-ttu-id="e2b08-341">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-341">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="e2b08-342">複数の互換性があるバッキング フィールドが見つかった場合にスローされる</span><span class="sxs-lookup"><span data-stu-id="e2b08-342">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="e2b08-343">問題 #12523 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-343">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="e2b08-344">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-344">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-345">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-345">Old behavior</span></span>**

<span data-ttu-id="e2b08-346">EF Core 3.0 より前では、複数のフィールドが、プロパティのバッキング フィールドを見つけるための規則と一致した場合、何らかの優先順位に基づいて、1 つのフィールドが選択されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-346">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="e2b08-347">これにより、あいまいなケースで不適切なフィールドが使用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-347">This could cause the wrong field to be used in ambiguous cases.</span></span>

**<span data-ttu-id="e2b08-348">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-348">New behavior</span></span>**

<span data-ttu-id="e2b08-349">EF Core 3.0 以降では、複数のフィールドが同じプロパティと一致した場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-349">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

**<span data-ttu-id="e2b08-350">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-350">Why</span></span>**

<span data-ttu-id="e2b08-351">この変更は、1 つのフィールドのみが適切である可能性がある場合に、他のものより優先して暗黙的に 1 つのフィールドが使用されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-351">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

**<span data-ttu-id="e2b08-352">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-352">Mitigations</span></span>**

<span data-ttu-id="e2b08-353">あいまいなバッキング フィールドがあるプロパティでは、使用するフィールドを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-353">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="e2b08-354">たとえば、fluent API を使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-354">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="e2b08-355">AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなりました</span><span class="sxs-lookup"><span data-stu-id="e2b08-355">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="e2b08-356">問題 #14756 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-356">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="e2b08-357">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-357">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-358">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-358">Old behavior</span></span>**

<span data-ttu-id="e2b08-359">EF Core 3.0 以前では、`AddDbContext` または `AddDbContextPool` を呼び出すと、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) および [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) への呼び出しを通じて、D.I を使ってログ記録とメモリ キャッシュ サービスも登録されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-359">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with D.I through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

**<span data-ttu-id="e2b08-360">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-360">New behavior</span></span>**

<span data-ttu-id="e2b08-361">EF Core 3.0 以降、`AddDbContext` と `AddDbContextPool` では、依存関係の挿入 (DI) を使ってこれらのサービスを登録しなくなります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-361">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

**<span data-ttu-id="e2b08-362">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-362">Why</span></span>**

<span data-ttu-id="e2b08-363">EF Core 3.0 では、これらのサービスをアプリケーションの DI コンテナーに含める必要がありません。</span><span class="sxs-lookup"><span data-stu-id="e2b08-363">EF Core 3.0 does not require that these services are in the application's DI cotainer.</span></span> <span data-ttu-id="e2b08-364">ただし、`ILoggerFactory` がアプリケーションの DI コンテナーに登録された場合、それは引き続き EF Core によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-364">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

**<span data-ttu-id="e2b08-365">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-365">Mitigations</span></span>**

<span data-ttu-id="e2b08-366">ご自身のアプリケーションでこれらのサービスが必要な場合は、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) または [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) を使って、DI コンテナーで明示的にそれらを登録します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-366">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="e2b08-367">DbContext.Entry でローカルの DetectChanges が実行されるようになった</span><span class="sxs-lookup"><span data-stu-id="e2b08-367">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="e2b08-368">問題 #13552 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-368">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="e2b08-369">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-369">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-370">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-370">Old behavior</span></span>**

<span data-ttu-id="e2b08-371">EF Core 3.0 より前では、`DbContext.Entry` を呼び出すと、追跡対象のすべてのエンティティで変更が検出されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-371">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="e2b08-372">これにより、`EntityEntry` で示された状態が確実に最新のものとなりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-372">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

**<span data-ttu-id="e2b08-373">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-373">New behavior</span></span>**

<span data-ttu-id="e2b08-374">EF Core 3.0 以降では、`DbContext.Entry` の呼び出しで、特定のエンティティと、それに関連する追跡対象のプリンシパル エンティティでの変更のみの検出が試行されるようになります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-374">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="e2b08-375">つまり、他の場所での変更は、このメソッドの呼び出しで検出されなかった可能性があり、アプリケーション状態に影響する場合があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-375">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="e2b08-376">`ChangeTracker.AutoDetectChangesEnabled` が `false` に設定されている場合、このローカル変更の検出も無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e2b08-376">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="e2b08-377">`ChangeTracker.Entries` や `SaveChanges` など、変更の検出の原因となる他のメソッドは引き続き、すべての追跡対象エンティティの完全な `DetectChanges` の原因となります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-377">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

**<span data-ttu-id="e2b08-378">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-378">Why</span></span>**

<span data-ttu-id="e2b08-379">この変更は、`context.Entry` を使用する既定のパフォーマンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-379">This change was made to improve the default performance of using `context.Entry`.</span></span>

**<span data-ttu-id="e2b08-380">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-380">Mitigations</span></span>**

<span data-ttu-id="e2b08-381">3.0 より前の動作を確保するには、`Entry` を呼び出す前に明示的に `ChgangeTracker.DetectChanges()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-381">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="e2b08-382">文字列とバイト配列のキーが既定でクライアントによって生成されない</span><span class="sxs-lookup"><span data-stu-id="e2b08-382">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="e2b08-383">問題 #14617 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-383">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="e2b08-384">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-384">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-385">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-385">Old behavior</span></span>**

<span data-ttu-id="e2b08-386">EF Core 3.0 より前では、`string` と `byte[]` のキー プロパティは、null 以外の値を明示的に設定しなくても使用できました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-386">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="e2b08-387">このような場合、キーの値は GUID としてクライアントで生成され、`byte[]` のバイトにシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-387">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

**<span data-ttu-id="e2b08-388">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-388">New behavior</span></span>**

<span data-ttu-id="e2b08-389">EF Core 3.0 以降では、キー値が設定されていないことを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-389">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

**<span data-ttu-id="e2b08-390">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-390">Why</span></span>**

<span data-ttu-id="e2b08-391">この変更は、クライアントで生成された `string`/`byte[]` 値が一般的に役に立たず、既定の動作では一般的な方法で生成されたキー値について判断するのが難しくなったため、行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-391">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

**<span data-ttu-id="e2b08-392">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-392">Mitigations</span></span>**

<span data-ttu-id="e2b08-393">3.0 より前の動作は、他の null 以外の値が設定されていない場合に、キー プロパティで生成された値を使用するように明示的に指定することで取得できます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-393">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="e2b08-394">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-394">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="e2b08-395">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-395">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="e2b08-396">ILoggerFactory がスコープ サービスになった</span><span class="sxs-lookup"><span data-stu-id="e2b08-396">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="e2b08-397">問題 #14698 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-397">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="e2b08-398">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-398">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-399">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-399">Old behavior</span></span>**

<span data-ttu-id="e2b08-400">EF Core 3.0 より前では、`ILoggerFactory` はシングルトン サービスとして登録されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-400">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

**<span data-ttu-id="e2b08-401">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-401">New behavior</span></span>**

<span data-ttu-id="e2b08-402">EF Core 3.0 以降では、`ILoggerFactory` がスコープ化されたものとして登録されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-402">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

**<span data-ttu-id="e2b08-403">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-403">Why</span></span>**

<span data-ttu-id="e2b08-404">この変更は、`DbContext` インスタンスでのロガーの関連付けを許可し、他の機能を有効にし、内部サービス プロバイダーの急増などの異常な動作の一部のケースをなくすために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-404">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

**<span data-ttu-id="e2b08-405">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-405">Mitigations</span></span>**

<span data-ttu-id="e2b08-406">この変更は、EF Core の内部サービス プロバイダーでカスタム サービスを登録して使用しない限り、アプリケーション コードに影響しないはずです。</span><span class="sxs-lookup"><span data-stu-id="e2b08-406">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="e2b08-407">これは一般的なことではありません。</span><span class="sxs-lookup"><span data-stu-id="e2b08-407">This isn't common.</span></span>
<span data-ttu-id="e2b08-408">このような場合、ほとんどのものが引き続き機能しますが、`ILoggerFactory` に依存していたシングルトン サービスは、異なる方法で `ILoggerFactory` を取得するように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-408">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="e2b08-409">このような状況になった場合は、[EF Core GitHub の問題追跡ツール](https://github.com/aspnet/EntityFrameworkCore/issues)で問題を提出し、`ILoggerFactory` の使用方法をお知らせください。これにより、Microsoft では、今後、再びこのように中断しない方法についてよりよく理解できます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-409">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a><span data-ttu-id="e2b08-410">IDbContextOptionsExtension に IDbContextOptionsExtensionWithDebugInfo が結合される</span><span class="sxs-lookup"><span data-stu-id="e2b08-410">IDbContextOptionsExtensionWithDebugInfo merged into IDbContextOptionsExtension</span></span>

[<span data-ttu-id="e2b08-411">問題 #13552 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-411">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="e2b08-412">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-412">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-413">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-413">Old behavior</span></span>**

`IDbContextOptionsExtensionWithDebugInfo` <span data-ttu-id="e2b08-414">は、2.x リリース サイクル時のインターフェイスへの破壊的変更を回避するために、`IDbContextOptionsExtension` から拡張された追加のオプションのインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="e2b08-414">was an additional optional interface extended from `IDbContextOptionsExtension` to avoid making a breaking change to the interface during the 2.x release cycle.</span></span>

**<span data-ttu-id="e2b08-415">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-415">New behavior</span></span>**

<span data-ttu-id="e2b08-416">インターフェイスはまとめて `IDbContextOptionsExtension` に結合されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-416">The interfaces are now merged together into `IDbContextOptionsExtension`.</span></span>

**<span data-ttu-id="e2b08-417">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-417">Why</span></span>**

<span data-ttu-id="e2b08-418">この変更は、インターフェイスが概念的には 1 つであるため、行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-418">This change was made because the interfaces are conceptually one.</span></span>

**<span data-ttu-id="e2b08-419">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-419">Mitigations</span></span>**

<span data-ttu-id="e2b08-420">`IDbContextOptionsExtension` のすべての実装を、新しいメンバーをサポートするように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-420">Any implementations of `IDbContextOptionsExtension` will need to be updated to support the new member.</span></span>

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="e2b08-421">遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった</span><span class="sxs-lookup"><span data-stu-id="e2b08-421">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="e2b08-422">問題 #12780 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-422">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="e2b08-423">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-423">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-424">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-424">Old behavior</span></span>**

<span data-ttu-id="e2b08-425">EF Core 3.0 より前では、`DbContext` が破棄されると、そのコンテキストから取得されたエンティティの特定のナビゲーション プロパティが完全に読み込まれたかどうかを知る方法はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="e2b08-425">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="e2b08-426">プロキシでは、代わりに、null 以外の値がある場合は参照ナビゲーションが読み込まれ、空でない場合はコレクション ナビゲーションが読み込まれたと見なされます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-426">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="e2b08-427">このような場合、遅延読み込みを試みても操作なしとなります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-427">In these cases, attempting to lazy-load would be a no-op.</span></span>

**<span data-ttu-id="e2b08-428">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-428">New behavior</span></span>**

<span data-ttu-id="e2b08-429">EF Core 3.0 以降では、プロキシで、ナビゲーション プロパティが読み込まれたかどうかの追跡が行われます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-429">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="e2b08-430">つまり、コンテキストが破棄された後に読み込まれたナビゲーション プロパティにアクセスしようとしても、通常は操作なしとなります。読み込まれたナビゲーションが空でも null であっても同様です。</span><span class="sxs-lookup"><span data-stu-id="e2b08-430">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="e2b08-431">逆に、コンテキストが破棄された場合、読み込まれなかったナビゲーション プロパティにアクセスしようとすると例外がスローされます。ナビゲーション プロパティが空ではないコレクションである場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="e2b08-431">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="e2b08-432">このような状況が発生した場合、アプリケーション コードで無効な時間に遅延読み込みの使用が試行されていることを意味し、アプリケーションを変更して、このようにならないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-432">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

**<span data-ttu-id="e2b08-433">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-433">Why</span></span>**

<span data-ttu-id="e2b08-434">この変更は、破棄された `DbContext` インスタンスで遅延読み込みが試行されたときの動作を一貫した正しいものにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-434">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

**<span data-ttu-id="e2b08-435">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-435">Mitigations</span></span>**

<span data-ttu-id="e2b08-436">破棄されたコンテキストで遅延読み込みが試行されないようにアプリケーション コードを更新するか、例外メッセージの説明に従って、これを操作なしとなるように構成します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-436">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="e2b08-437">内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった</span><span class="sxs-lookup"><span data-stu-id="e2b08-437">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="e2b08-438">問題 #10236 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-438">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="e2b08-439">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-439">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-440">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-440">Old behavior</span></span>**

<span data-ttu-id="e2b08-441">EF Core 3.0 より前では、異常な数の内部サービス プロバイダーを作成するアプリケーションに対する警告がログに記録されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-441">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

**<span data-ttu-id="e2b08-442">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-442">New behavior</span></span>**

<span data-ttu-id="e2b08-443">EF Core 3.0 以降では、この警告はエラーと見なされるようになり、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-443">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

**<span data-ttu-id="e2b08-444">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-444">Why</span></span>**

<span data-ttu-id="e2b08-445">この変更は、この異常なケースをより明示的に示すことで、アプリケーション コードの改善を促進するために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-445">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

**<span data-ttu-id="e2b08-446">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-446">Mitigations</span></span>**

<span data-ttu-id="e2b08-447">このエラーの発生時の最も適切なアクションは、根本原因を理解し、非常に多くの内部サービス プロバイダーを作成しないようにすることです。</span><span class="sxs-lookup"><span data-stu-id="e2b08-447">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="e2b08-448">しかし、`DbContextOptionsBuilder` での構成を使用して、エラーを警告に戻す (または無視する) ことがことができます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-448">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="e2b08-449">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-449">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="e2b08-450">1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-450">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="e2b08-451">問題 #9171 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-451">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="e2b08-452">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-452">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-453">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-453">Old behavior</span></span>**

<span data-ttu-id="e2b08-454">EF Core 3.0 以前では、1 つの文字列と共に `HasOne` または `HasMany` を呼び出すコードは、わかりにくい方法で解釈されていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-454">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpretted in a confusing way.</span></span>
<span data-ttu-id="e2b08-455">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-455">For example:</span></span>
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="e2b08-456">このコードは、プライベートである可能性がある `Entrance` ナビゲーション プロパティを使って、`Samurai` を他のエンティティ型に関連付けているように見えます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-456">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="e2b08-457">実際には、このコードは、ナビゲーション プロパティなしで、`Entrance` と呼ばれるエンティティ型に対してリレーションシップを作成しようとしています。</span><span class="sxs-lookup"><span data-stu-id="e2b08-457">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

**<span data-ttu-id="e2b08-458">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-458">New behavior</span></span>**

<span data-ttu-id="e2b08-459">EF Core 3.0 以降では、上記のコードは、前にそれが実行すべきであるように見えたことを実行するようになりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-459">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

**<span data-ttu-id="e2b08-460">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-460">Why</span></span>**

<span data-ttu-id="e2b08-461">以前の動作は、特に構成コードを読み取ってエラーを探す場合は、非常にわかりにくいものでした。</span><span class="sxs-lookup"><span data-stu-id="e2b08-461">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

**<span data-ttu-id="e2b08-462">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-462">Mitigations</span></span>**

<span data-ttu-id="e2b08-463">これが中断させるのは、型名の文字列を使って明示的にリレーションシップを構成し、かつ明示的にナビゲーション プロパティを指定しないアプリケーションのみです。</span><span class="sxs-lookup"><span data-stu-id="e2b08-463">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="e2b08-464">これは一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="e2b08-464">This is not common.</span></span>
<span data-ttu-id="e2b08-465">前の動作は、ナビゲーション プロパティ名に対して明示的に `null` を渡すことで実現できます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-465">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="e2b08-466">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-466">For example:</span></span>

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

## <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="e2b08-467">いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更されました</span><span class="sxs-lookup"><span data-stu-id="e2b08-467">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="e2b08-468">問題 #15184 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-468">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="e2b08-469">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-469">This change will be introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-470">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-470">Old behavior</span></span>**

<span data-ttu-id="e2b08-471">次の非同期メソッドでは、以前は `Task<T>` が返されていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-471">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* `ValueGenerator.NextValueAsync()` <span data-ttu-id="e2b08-472">(および派生クラス)</span><span class="sxs-lookup"><span data-stu-id="e2b08-472">(and deriving classes)</span></span>

**<span data-ttu-id="e2b08-473">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-473">New behavior</span></span>**

<span data-ttu-id="e2b08-474">前述のメソッドは、以前と同じ `T` に対して `ValueTask<T>` を返すようになりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-474">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

**<span data-ttu-id="e2b08-475">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-475">Why</span></span>**

<span data-ttu-id="e2b08-476">この変更により、これらのメソッドを呼び出すときに発生するヒープ割り当ての数を削減して全般的なパフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-476">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

**<span data-ttu-id="e2b08-477">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-477">Mitigations</span></span>**

<span data-ttu-id="e2b08-478">上記の API を待機しているだけのアプリケーションのみを再コンパイルする必要があります。ソースの変更は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e2b08-478">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="e2b08-479">より複雑な使用方法 (返された `Task` を `Task.WhenAny()` に渡すなど) では通常、返された `ValueTask<T>` を `AsTask()` を呼び出すことによって `Task<T>` に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-479">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="e2b08-480">これにより、この変更による割り当ての削減が無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e2b08-480">Note that this negates the allocation reduction that this change brings.</span></span>

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="e2b08-481">Relational:TypeMapping 注釈が単に TypeMapping となった</span><span class="sxs-lookup"><span data-stu-id="e2b08-481">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="e2b08-482">問題 #9913 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-482">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="e2b08-483">この変更は、EF Core 3.0 プレビュー 2 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-483">This change was introduced in EF Core 3.0-preview 2.</span></span>

**<span data-ttu-id="e2b08-484">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-484">Old behavior</span></span>**

<span data-ttu-id="e2b08-485">型マッピング注釈の注釈名は "Relational:TypeMapping" でした。</span><span class="sxs-lookup"><span data-stu-id="e2b08-485">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

**<span data-ttu-id="e2b08-486">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-486">New behavior</span></span>**

<span data-ttu-id="e2b08-487">型マッピング注釈の注釈名は、"TypeMapping" となりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-487">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

**<span data-ttu-id="e2b08-488">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-488">Why</span></span>**

<span data-ttu-id="e2b08-489">型マッピングが、リレーショナル データベース プロバイダー以外でも使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-489">Type mappings are now used for more than just relational database providers.</span></span>

**<span data-ttu-id="e2b08-490">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-490">Mitigations</span></span>**

<span data-ttu-id="e2b08-491">これにより、一般的ではない、注釈として直接型マッピングにアクセスするアプリケーションのみが中断されるようになります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-491">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="e2b08-492">問題を解決するための最も適切なアクションは、注釈を直接使用するのではなく、API サーフェスを使用して型マッピングにアクセスすることです。</span><span class="sxs-lookup"><span data-stu-id="e2b08-492">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

## <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="e2b08-493">派生型の ToTable で例外がスローされる</span><span class="sxs-lookup"><span data-stu-id="e2b08-493">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="e2b08-494">問題 #11811 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-494">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="e2b08-495">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-495">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-496">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-496">Old behavior</span></span>**

<span data-ttu-id="e2b08-497">EF Core 3.0 より前では、唯一の継承マッピング方法が TPH であり、これが有効でない場合には、派生型で呼び出された `ToTable()` が無視されていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-497">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

**<span data-ttu-id="e2b08-498">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-498">New behavior</span></span>**

<span data-ttu-id="e2b08-499">EF Core 3.0 以降、および今後のリリースでの TPT と TPC のサポートの追加準備中には、派生型で呼び出された `ToTable()` で、今後の予期されないマッピング変更を回避するために例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-499">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

**<span data-ttu-id="e2b08-500">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-500">Why</span></span>**

<span data-ttu-id="e2b08-501">現在、派生型を別のテーブルにマップすることは有効ではありません。</span><span class="sxs-lookup"><span data-stu-id="e2b08-501">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="e2b08-502">この変更により、今後、これを行うことが有効になった場合に中断されなくなります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-502">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

**<span data-ttu-id="e2b08-503">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-503">Mitigations</span></span>**

<span data-ttu-id="e2b08-504">派生型を他のテーブルにマップしないようにします。</span><span class="sxs-lookup"><span data-stu-id="e2b08-504">Remove any attempts to map derived types to other tables.</span></span>

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="e2b08-505">ForSqlServerHasIndex が HasIndex に置き換えられた</span><span class="sxs-lookup"><span data-stu-id="e2b08-505">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="e2b08-506">問題 #12366 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-506">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="e2b08-507">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-507">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-508">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-508">Old behavior</span></span>**

<span data-ttu-id="e2b08-509">EF Core 3.0 より前では、`ForSqlServerHasIndex().ForSqlServerInclude()` によって、`INCLUDE` で使用される列を構成する方法が提供されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-509">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

**<span data-ttu-id="e2b08-510">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-510">New behavior</span></span>**

<span data-ttu-id="e2b08-511">EF Core 3.0 以降では、インデックスでの `Include` の使用が、リレーショナル レベルでサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-511">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="e2b08-512">`HasIndex().ForSqlServerInclude()` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="e2b08-512">Use `HasIndex().ForSqlServerInclude()`.</span></span>

**<span data-ttu-id="e2b08-513">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-513">Why</span></span>**

<span data-ttu-id="e2b08-514">この変更は、すべてのデータ プロバイダーのために `Includes` でインデックス用の API を 1 か所に統合するために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-514">This change was made to consolidate the API for indexes with `Includes` into one place for all database providers.</span></span>

**<span data-ttu-id="e2b08-515">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-515">Mitigations</span></span>**

<span data-ttu-id="e2b08-516">上記のように、新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-516">Use the new API, as shown above.</span></span>

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="e2b08-517">EF Core で SQLite FK を適用するためのプラグマが送信されなくなった</span><span class="sxs-lookup"><span data-stu-id="e2b08-517">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="e2b08-518">問題 #12151 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-518">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="e2b08-519">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-519">This change was introduced in EF Core 3.0-preview 3.</span></span>

**<span data-ttu-id="e2b08-520">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-520">Old behavior</span></span>**

<span data-ttu-id="e2b08-521">EF Core 3.0 より前では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-521">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

**<span data-ttu-id="e2b08-522">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-522">New behavior</span></span>**

<span data-ttu-id="e2b08-523">EF Core 3.0 以降では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-523">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

**<span data-ttu-id="e2b08-524">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-524">Why</span></span>**

<span data-ttu-id="e2b08-525">この変更は、既定で EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用され、その後、FK の適用が既定で有効になり、接続が開かれるたびに明示的に有効にする必要がないため、行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-525">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

**<span data-ttu-id="e2b08-526">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-526">Mitigations</span></span>**

<span data-ttu-id="e2b08-527">外部キーは、EF Core に対して既定で使用される、SQLitePCLRaw.bundle_e_sqlite3 で既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-527">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="e2b08-528">それ以外の場合は、接続文字列で `Foreign Keys=True` を指定することで、外部キーを有効にできます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-528">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a><span data-ttu-id="e2b08-529">Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった</span><span class="sxs-lookup"><span data-stu-id="e2b08-529">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

**<span data-ttu-id="e2b08-530">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-530">Old behavior</span></span>**

<span data-ttu-id="e2b08-531">EF Core 3.0 より前では、EF Core では `SQLitePCLRaw.bundle_green` が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-531">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

**<span data-ttu-id="e2b08-532">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-532">New behavior</span></span>**

<span data-ttu-id="e2b08-533">EF Core 3.0 以降では、EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-533">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

**<span data-ttu-id="e2b08-534">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-534">Why</span></span>**

<span data-ttu-id="e2b08-535">この変更は、iOS 上で使用される SQLite のバージョンを、他のプラットフォームと一致させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-535">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

**<span data-ttu-id="e2b08-536">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-536">Mitigations</span></span>**

<span data-ttu-id="e2b08-537">iOS でネイティブの SQLite バージョンを使用するには、別の `SQLitePCLRaw` バンドルを使用するように `Microsoft.Data.Sqlite` を構成します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-537">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

## <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="e2b08-538">GUID の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="e2b08-538">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="e2b08-539">問題 #15078 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-539">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="e2b08-540">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-540">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-541">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-541">Old behavior</span></span>**

<span data-ttu-id="e2b08-542">GUID の値は、以前は SQLite に BLOB 値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-542">Guid values were previously sored as BLOB values on SQLite.</span></span>

**<span data-ttu-id="e2b08-543">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-543">New behavior</span></span>**

<span data-ttu-id="e2b08-544">GUID の値はテキストとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-544">Guid values are now sotred as TEXT.</span></span>

**<span data-ttu-id="e2b08-545">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-545">Why</span></span>**

<span data-ttu-id="e2b08-546">GUID のバイナリ形式は標準化されていません。</span><span class="sxs-lookup"><span data-stu-id="e2b08-546">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="e2b08-547">値をテキストとして格納する方が、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="e2b08-547">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

**<span data-ttu-id="e2b08-548">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-548">Mitigations</span></span>**

<span data-ttu-id="e2b08-549">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-549">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

<span data-ttu-id="e2b08-550">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-550">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="e2b08-551">Microsoft.Data.Sqlite は引き続き、BLOB とテキストの両方の列から GUID 値を読み取ることができます。ただし、パラメーターと定数の既定の形式が変更されているため、GUID が含まれる多くのシナリオではアクションが必要になります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-551">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

## <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="e2b08-552">Char の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="e2b08-552">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="e2b08-553">問題 #15020 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-553">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="e2b08-554">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-554">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-555">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-555">Old behavior</span></span>**

<span data-ttu-id="e2b08-556">Char の値は、以前は SQLite に整数値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-556">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="e2b08-557">たとえば、Char の値 *A* は整数値 65 として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-557">For example, a char value of *A* was stored as the integer value 65.</span></span>

**<span data-ttu-id="e2b08-558">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-558">New behavior</span></span>**

<span data-ttu-id="e2b08-559">Char の値はテキストとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-559">Char values are now sotred as TEXT.</span></span>

**<span data-ttu-id="e2b08-560">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-560">Why</span></span>**

<span data-ttu-id="e2b08-561">値をテキストとして格納する方が自然であり、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="e2b08-561">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

**<span data-ttu-id="e2b08-562">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-562">Mitigations</span></span>**

<span data-ttu-id="e2b08-563">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-563">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="e2b08-564">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="e2b08-564">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="e2b08-565">Microsoft.Data.Sqlite では、引き続き整数とテキストの両方の列の文字列値を読み取ることができるため、一部のシナリオではアクションが必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e2b08-565">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

## <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="e2b08-566">移行 ID がインバリアント カルチャの暦を使用して生成されるようになった</span><span class="sxs-lookup"><span data-stu-id="e2b08-566">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="e2b08-567">問題 #12978 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-567">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="e2b08-568">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-568">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-569">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-569">Old behavior</span></span>**

<span data-ttu-id="e2b08-570">移行 ID は選択されているカルチャの暦を使用して意図なく生成されていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-570">Migration IDs were inadvertantly generated using the currret culture's calendar.</span></span>

**<span data-ttu-id="e2b08-571">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-571">New behavior</span></span>**

<span data-ttu-id="e2b08-572">移行 ID がインバリアント カルチャの暦 (グレゴリオ暦) を使用して常に生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-572">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

**<span data-ttu-id="e2b08-573">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-573">Why</span></span>**

<span data-ttu-id="e2b08-574">データベースを更新するときやマージの競合を解決するときに、移行の順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="e2b08-574">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="e2b08-575">インバリアントの暦を使用することで、順序の問題が回避され、システムの予定表がチームのメンバーごとに違うことがなくなります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-575">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

**<span data-ttu-id="e2b08-576">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-576">Mitigations</span></span>**

<span data-ttu-id="e2b08-577">この変更は、グレゴリオ暦以外の暦 (タイ仏暦など) を使用し、1 年がグレゴリオ暦より長くなるすべてのユーザーに影響します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-577">This change affects anyone using a non-Gregorian calender where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="e2b08-578">既存の移行後に新しい移行順序が設定されるように、既存の移行 ID を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-578">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="e2b08-579">移行 ID は、移行のデザイナー ファイルの移行属性にあります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-579">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="e2b08-580">移行履歴テーブルも更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e2b08-580">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

## <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="e2b08-581">LogQueryPossibleExceptionWithAggregateOperator の名前が変更された</span><span class="sxs-lookup"><span data-stu-id="e2b08-581">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="e2b08-582">問題 #10985 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-582">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="e2b08-583">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-583">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-584">変更</span><span class="sxs-lookup"><span data-stu-id="e2b08-584">Change</span></span>**

`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` <span data-ttu-id="e2b08-585">の名前が `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-585">has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

**<span data-ttu-id="e2b08-586">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-586">Why</span></span>**

<span data-ttu-id="e2b08-587">この警告イベントの名前が他のすべての警告イベントと照合されるためです。</span><span class="sxs-lookup"><span data-stu-id="e2b08-587">Aligns the naming of this warning event with all other warning events.</span></span>

**<span data-ttu-id="e2b08-588">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-588">Mitigations</span></span>**

<span data-ttu-id="e2b08-589">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-589">Use the new name.</span></span> <span data-ttu-id="e2b08-590">(イベント ID 番号が変更されていないことを確認してください。)</span><span class="sxs-lookup"><span data-stu-id="e2b08-590">(Note that the event ID number has not changed.)</span></span>

## <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="e2b08-591">外部キー制約名の API が明確化された</span><span class="sxs-lookup"><span data-stu-id="e2b08-591">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="e2b08-592">問題 #10730 の追跡</span><span class="sxs-lookup"><span data-stu-id="e2b08-592">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="e2b08-593">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-593">This change was introduced in EF Core 3.0-preview 4.</span></span>

**<span data-ttu-id="e2b08-594">以前の動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-594">Old behavior</span></span>**

<span data-ttu-id="e2b08-595">EF Core 3.0 以前は、外部キー制約名が単に "名前" と呼ばれていました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-595">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="e2b08-596">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-596">For example:</span></span>

```C#
var constraintName = myForeignKey.Name;
```

**<span data-ttu-id="e2b08-597">新しい動作</span><span class="sxs-lookup"><span data-stu-id="e2b08-597">New behavior</span></span>**

<span data-ttu-id="e2b08-598">EF Core 3.0 以降は、外部キー制約名が "定数名" と呼ばれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-598">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constaint name".</span></span> <span data-ttu-id="e2b08-599">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-599">For example:</span></span>

```C#
var constraintName = myForeignKey.ConstraintName;
```

**<span data-ttu-id="e2b08-600">理由</span><span class="sxs-lookup"><span data-stu-id="e2b08-600">Why</span></span>**

<span data-ttu-id="e2b08-601">この変更により、この領域の名前付けに一貫性が生まれ、それが外部キーが定義されている列やプロパティの名前ではなく、外部キー制約の名前であることが明確になりました。</span><span class="sxs-lookup"><span data-stu-id="e2b08-601">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constaint, and not the column or property name that the foreign key is defined on.</span></span>

**<span data-ttu-id="e2b08-602">軽減策</span><span class="sxs-lookup"><span data-stu-id="e2b08-602">Mitigations</span></span>**

<span data-ttu-id="e2b08-603">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="e2b08-603">Use the new name.</span></span>
