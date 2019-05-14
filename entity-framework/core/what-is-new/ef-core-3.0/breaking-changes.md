---
title: EF Core 3.0 での破壊的変更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: b1b5e286e08a8b6b4efe225a176e76023f9fdd20
ms.sourcegitcommit: 960e42a01b3a2f76da82e074f64f52252a8afecc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405238"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="14a01-102">EF Core 3.0 (現在プレビュー段階) に含まれる破壊的変更</span><span class="sxs-lookup"><span data-stu-id="14a01-102">Breaking changes included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="14a01-103">機能セットと今後のリリースのスケジュールは、常に変更される可能性があることに注意してください。また、このページを最新の状態に保持するようにしていますが、最新のプランが反映されていない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="14a01-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="14a01-104">以下の API と動作変更により、3.0.0 へのアップグレード時に、EF Core 2.2.x 用に開発されたアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-104">The following API and behavior changes have the potential to break applications developed for EF Core 2.2.x when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="14a01-105">データベース プロバイダーにのみ影響することが予想される変更については、[プロバイダーの変更](../../providers/provider-log.md)に関するページに記載されています。</span><span class="sxs-lookup"><span data-stu-id="14a01-105">Changes that we expect to only impact database providers are documented under [provider changes](../../providers/provider-log.md).</span></span>
<span data-ttu-id="14a01-106">ある 3.0 プレビューから別の 3.0 プレビューに導入される新機能での中断については、ここには記載されていません。</span><span class="sxs-lookup"><span data-stu-id="14a01-106">Breaks in new features introduced from one 3.0 preview to another 3.0 preview aren't documented here.</span></span>

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="14a01-107">LINQ クエリがクライアントで評価されなくなった</span><span class="sxs-lookup"><span data-stu-id="14a01-107">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="14a01-108">[問題 #14935 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[問題 #12795 も参照](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="14a01-108">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="14a01-109">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-109">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-110">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-110">**Old behavior**</span></span>

<span data-ttu-id="14a01-111">3.0 より前では、EF Core では、SQL またはパラメーターに対するクエリの一部だった式を変換できない場合、クライアントで自動的に式が評価されていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-111">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="14a01-112">既定では、コストの高い式のクライアント評価でのみ警告がトリガーされました。</span><span class="sxs-lookup"><span data-stu-id="14a01-112">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="14a01-113">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-113">**New behavior**</span></span>

<span data-ttu-id="14a01-114">3.0 以降では、クライアントで評価される最上位のプロジェクション (クエリの最後の `Select()` 呼び出し) の式のみが EF Core で許可されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-114">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="14a01-115">クエリの他の部分の式を SQL やパラメーターに変換できない場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="14a01-115">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="14a01-116">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-116">**Why**</span></span>

<span data-ttu-id="14a01-117">クエリの自動クライアント評価では、多くのクエリを実行できます。これらの重要な部分を変換できない場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="14a01-117">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="14a01-118">この動作が原因で、予期しない損害を与える動作となる可能性があります。この動作は、運用環境でのみ見られる場合があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-118">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="14a01-119">たとえば、変換できない `Where()` 呼び出しの条件が原因で、テーブルのすべての行がデータベース サーバーから転送できるようになり、クライアントでフィルターを適用できるようになる場合があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-119">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="14a01-120">このような状況は、開発時にテーブルにわずかな行が含まれている場合は、簡単に検出される可能性があります。しかし、テーブルに数百万もの行が含まれる、運用環境にアプリケーションを移行する場合は難しくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-120">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="14a01-121">クライアント評価の警告では、開発中にあまりに簡単に無視されることも示されていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-121">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="14a01-122">これだけでなく、自動クライアント評価は、特定の式のクエリ変換の改善が、リリース間の予期しない破壊的変更の原因となる問題につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-122">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="14a01-123">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-123">**Mitigations**</span></span>

<span data-ttu-id="14a01-124">クエリを完全に変換できない場合は、変換できる形式でクエリを書き直すか、`AsEnumerable()`、`ToList()`、または同様のものを使用して、LINQ-to-Objects を使ってさらに処理できるクライアントに明示的にデータを戻します。</span><span class="sxs-lookup"><span data-stu-id="14a01-124">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="14a01-125">Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="14a01-125">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="14a01-126">問題 Announcements#325 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-126">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="14a01-127">この変更は、ASP.NET Core 3.0 プレビュー 1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-127">This change was introduced in ASP.NET Core 3.0-preview 1.</span></span> 

<span data-ttu-id="14a01-128">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-128">**Old behavior**</span></span>

<span data-ttu-id="14a01-129">ASP.NET Core 3.0 より前では、パッケージ参照を `Microsoft.AspNetCore.App` または `Microsoft.AspNetCore.All` に追加したときに、EF Core と、SQL Server プロバイダーのような EF Core データ プロバイダーの一部が含まれました。</span><span class="sxs-lookup"><span data-stu-id="14a01-129">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="14a01-130">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-130">**New behavior**</span></span>

<span data-ttu-id="14a01-131">3.0 以降では、ASP.NET Core 共有フレームワークには、EF Core も、いずれの EF Core データ プロバイダーも含まれません。</span><span class="sxs-lookup"><span data-stu-id="14a01-131">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="14a01-132">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-132">**Why**</span></span>

<span data-ttu-id="14a01-133">この変更の前では、アプリケーションのターゲットが ASP.NET Core であるか SQL Server であるかに応じて、EF Core を取得するには異なる手順が必要でした。</span><span class="sxs-lookup"><span data-stu-id="14a01-133">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="14a01-134">また、ASP.NET Core のアップグレードでは、必ずしも適切だとは言えない、EF Core と SQL Server プロバイダーのアップグレードが強制されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-134">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="14a01-135">この変更では、EF Core の取得のエクスペリエンスは、.NET の実装とアプリケーションの種類がサポートされる、すべてのプロバイダーで同じになります。</span><span class="sxs-lookup"><span data-stu-id="14a01-135">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="14a01-136">また、開発者は、EF Core と EF Core データ プロバイダーのアップグレードのタイミングを正確に制御できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-136">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="14a01-137">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-137">**Mitigations**</span></span>

<span data-ttu-id="14a01-138">ASP.NET Core 3.0 アプリケーションまたはその他のサポートされるアプリケーションで EF Core を使用するには、アプリケーションで使用される EF Core データベース プロバイダーにパッケージ参照を明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="14a01-138">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

## <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="14a01-139">EF Core のコマンドライン ツールである dotnet ef が .NET Core SDK の一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="14a01-139">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="14a01-140">問題 #14016 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-140">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="14a01-141">この変更は、EF Core 3.0 プレビュー 4 および .NET Core SDK の対応するバージョンで導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-141">This change was introduced in EF Core 3.0-preview 4 and the corresponding version of the .NET Core SDK.</span></span>

<span data-ttu-id="14a01-142">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-142">**Old behavior**</span></span>

<span data-ttu-id="14a01-143">3.0 より前は `dotnet ef` ツールが .NET Core SDK に含まれており、追加の手順を必要とせずに、任意のプロジェクトのコマンド ラインから簡単に使用できました。</span><span class="sxs-lookup"><span data-stu-id="14a01-143">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="14a01-144">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-144">**New behavior**</span></span>

<span data-ttu-id="14a01-145">3.0 以降は .NET SDK に `dotnet ef` ツールが含まれないため、これを使用するにはローカルまたはグローバルなツールとして明示的にインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-145">Starting in 3.0, the .NET SDK does not incude the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="14a01-146">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-146">**Why**</span></span>

<span data-ttu-id="14a01-147">この変更により、`dotnet ef` を通常の .NET CLI ツールとして NuGet 上で配信したり更新したりできるようになります。これは EF Core 3.0 も常に NuGet パッケージとして配信されるという事実と一致します。</span><span class="sxs-lookup"><span data-stu-id="14a01-147">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="14a01-148">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-148">**Mitigations**</span></span>

<span data-ttu-id="14a01-149">移行の管理や `DbContext` のスキャフォールディングを行うには、`dotnet-ef` を `dotnet tool install` コマンドを使用してインストールします。</span><span class="sxs-lookup"><span data-stu-id="14a01-149">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` using the `dotnet tool install` command.</span></span>
<span data-ttu-id="14a01-150">たとえば、グローバルなツールとしてインストールするには、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="14a01-150">For example, to install it as a global tool, you can type this command:</span></span>

  ``` console
  $ dotnet tool install --global dotnet-ef --version <exact-version>
  ```

<span data-ttu-id="14a01-151">[ツール マニフェスト ファイル](https://github.com/dotnet/cli/issues/10288)を使用してツールの依存関係として宣言するプロジェクトの依存関係を復元するときに、ローカルなツールとして取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="14a01-151">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](https://github.com/dotnet/cli/issues/10288).</span></span>

## <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="14a01-152">FromSql、ExecuteSql、および ExecuteSqlAsync の名前変更</span><span class="sxs-lookup"><span data-stu-id="14a01-152">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="14a01-153">問題 #10996 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-153">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="14a01-154">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-154">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-155">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-155">**Old behavior**</span></span>

<span data-ttu-id="14a01-156">EF Core 3.0 より前のバージョンでは、通常の文字列または SQL およびパラメーターに挿入する必要がある文字列で使用するために、これらのメソッド名がオーバーロードされました。</span><span class="sxs-lookup"><span data-stu-id="14a01-156">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="14a01-157">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-157">**New behavior**</span></span>

<span data-ttu-id="14a01-158">EF Core 3.0 以降では、`FromSqlRaw`、`ExecuteSqlRaw`、および `ExecuteSqlRawAsync` を使用して、パラメーターがクエリ文字列とは別に渡される、パラメーター化クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-158">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="14a01-159">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-159">For example:</span></span>

```C#
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="14a01-160">`FromSqlInterpolated`、`ExecuteSqlInterpolated`、および `ExecuteSqlInterpolatedAsync` を使用して、パラメーターが挿入クエリ文字列の一部として渡されるパラメーター化クエリを作成します。</span><span class="sxs-lookup"><span data-stu-id="14a01-160">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="14a01-161">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-161">For example:</span></span>

```C#
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="14a01-162">上記のクエリはどちらも、同じ SQL パラメーターを持つ同じパラメーター化 SQL が生成されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="14a01-162">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="14a01-163">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-163">**Why**</span></span>

<span data-ttu-id="14a01-164">このようなメソッド オーバーロードは、挿入文字列メソッドを呼び出すつもりが、誤って raw 文字列メソッドを非常に簡単に呼び出せてしまいます。その逆も同様です。</span><span class="sxs-lookup"><span data-stu-id="14a01-164">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="14a01-165">これは、クエリをパラメーター化する必要があるときに、パラメーター化されない結果になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-165">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="14a01-166">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-166">**Mitigations**</span></span>

<span data-ttu-id="14a01-167">新しいメソッド名を使用するように切り替えます。</span><span class="sxs-lookup"><span data-stu-id="14a01-167">Switch to use the new method names.</span></span>

## <a name="query-execution-is-logged-at-debug-level"></a><span data-ttu-id="14a01-168">クエリの実行がデバッグ レベルでログに記録される</span><span class="sxs-lookup"><span data-stu-id="14a01-168">Query execution is logged at Debug level</span></span>

[<span data-ttu-id="14a01-169">問題 #14523 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-169">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="14a01-170">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-170">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-171">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-171">**Old behavior**</span></span>

<span data-ttu-id="14a01-172">EF Core 3.0 より前では、クエリと他のコマンドの実行は、`Info` レベルでログに記録されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-172">Before EF Core 3.0, execution of queries and other commands was logged at the `Info` level.</span></span>

<span data-ttu-id="14a01-173">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-173">**New behavior**</span></span>

<span data-ttu-id="14a01-174">EF Core 3.0 以降では、コマンド/SQL の実行は、`Debug` レベルでログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-174">Starting with EF Core 3.0, logging of command/SQL execution is at the `Debug` level.</span></span>

<span data-ttu-id="14a01-175">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-175">**Why**</span></span>

<span data-ttu-id="14a01-176">この変更は、`Info` ログ レベルでのノイズを減らすために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-176">This change was made to reduce the noise at the `Info` log level.</span></span>

<span data-ttu-id="14a01-177">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-177">**Mitigations**</span></span>

<span data-ttu-id="14a01-178">このログ イベントは、イベント ID 20100 の `RelationalEventId.CommandExecuting` によって定義されています。</span><span class="sxs-lookup"><span data-stu-id="14a01-178">This logging event is defined by `RelationalEventId.CommandExecuting` with event ID 20100.</span></span>
<span data-ttu-id="14a01-179">`Info` レベルで SQL をもう一度ログに記録するには、`OnConfiguring` または `AddDbContext` で明示的にレベルを構成します。</span><span class="sxs-lookup"><span data-stu-id="14a01-179">To log SQL at the `Info` level again, explicitly configure the level in `OnConfiguring` or `AddDbContext`.</span></span>
<span data-ttu-id="14a01-180">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-180">For example:</span></span>
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Info)));
```

## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="14a01-181">一時キーの値がエンティティ インスタンスに設定されなくなった</span><span class="sxs-lookup"><span data-stu-id="14a01-181">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="14a01-182">問題 #12378 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-182">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="14a01-183">この変更は、EF Core 3.0 プレビュー 2 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-183">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="14a01-184">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-184">**Old behavior**</span></span>

<span data-ttu-id="14a01-185">EF Core 3.0 より前では、後で実際の値がデータベースによって生成される、すべてのキー プロパティに一時的な値が割り当てられていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-185">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="14a01-186">通常、これらの一時的な値は大きい負の数値でした。</span><span class="sxs-lookup"><span data-stu-id="14a01-186">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="14a01-187">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-187">**New behavior**</span></span>

<span data-ttu-id="14a01-188">3.0 以降では、EF Core で、エンティティの追跡情報の一部として一時キーの値が格納され、キー プロパティ自体はそのままになります。</span><span class="sxs-lookup"><span data-stu-id="14a01-188">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="14a01-189">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-189">**Why**</span></span>

<span data-ttu-id="14a01-190">この変更は、何らかの `DbContext` インスタンスによって以前に追跡されたエンティティが、別の `DbContext` インスタンスに移動されるときに、一時キーの値が誤って永続的なものにならないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-190">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="14a01-191">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-191">**Mitigations**</span></span>

<span data-ttu-id="14a01-192">主キーの値を外部キーに割り当てて、エンティティ間の関連付けを形成するアプリケーションは、主キーがストアで生成されており、`Added` 状態のエンティティに属している場合、以前の動作に依存する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-192">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="14a01-193">これは、次のようにして回避できます。</span><span class="sxs-lookup"><span data-stu-id="14a01-193">This can be avoided by:</span></span>
* <span data-ttu-id="14a01-194">ストア生成キーを使用しない。</span><span class="sxs-lookup"><span data-stu-id="14a01-194">Not using store-generated keys.</span></span>
* <span data-ttu-id="14a01-195">外部キーの値を設定するのではなく、ナビゲーション プロパティを設定してリレーションシップを形成する。</span><span class="sxs-lookup"><span data-stu-id="14a01-195">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="14a01-196">エンティティの追跡情報から実際の一時キーの値を取得する。</span><span class="sxs-lookup"><span data-stu-id="14a01-196">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="14a01-197">たとえば、`context.Entry(blog).Property(e => e.Id).CurrentValue` では、`blog.Id` 自体が設定されていない場合でも、一時的な値が返されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-197">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

## <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="14a01-198">DetectChanges でストア生成キーの値が優先される</span><span class="sxs-lookup"><span data-stu-id="14a01-198">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="14a01-199">問題 #14616 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-199">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="14a01-200">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-200">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-201">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-201">**Old behavior**</span></span>

<span data-ttu-id="14a01-202">EF Core 3.0 より前では、`DetectChanges` によって検出された追跡対象外のエンティティが `Added` 状態で追跡され、`SaveChanges` が呼び出されたときに新しい行として挿入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-202">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="14a01-203">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-203">**New behavior**</span></span>

<span data-ttu-id="14a01-204">EF Core 3.0 以降では、エンティティで生成されたキーの値を使用し、何らかのキーの値が設定されている場合、そのエンティティは `Modified` 状態で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-204">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="14a01-205">つまり、エンティティの行が存在していると見なされ、`SaveChanges` の呼び出し時に更新されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-205">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="14a01-206">キーの値が設定されていないか、エンティティ型で生成されたキーが使用されない場合、新しいエンティティは引き続き、以前のバージョンと同様に `Added` として追跡されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-206">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="14a01-207">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-207">**Why**</span></span>

<span data-ttu-id="14a01-208">この変更は、ストア生成キーの使用中に、切り離されたエンティティ グラフをより簡単に一貫して操作できるようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-208">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="14a01-209">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-209">**Mitigations**</span></span>

<span data-ttu-id="14a01-210">エンティティ型が生成されたキーを使用するように構成されているものの、キーの値が新しいインスタンスに対して明示的に設定されている場合、この変更によってアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-210">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="14a01-211">この問題を解決するには、キー プロパティで生成された値が使用されないように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="14a01-211">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="14a01-212">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="14a01-212">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="14a01-213">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="14a01-213">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="14a01-214">既定で連鎖削除がすぐに行われるようになった</span><span class="sxs-lookup"><span data-stu-id="14a01-214">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="14a01-215">問題 #10114 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-215">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="14a01-216">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-216">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-217">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-217">**Old behavior**</span></span>

<span data-ttu-id="14a01-218">3.0 より前では、EF Core で適用された連鎖操作 (必要なプリンシパルが削除されたか、必要なプリンシパルへのリレーションシップが切断されたときに依存エンティティを削除する) は、SaveChanges が呼び出されるまで行われませんでした。</span><span class="sxs-lookup"><span data-stu-id="14a01-218">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="14a01-219">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-219">**New behavior**</span></span>

<span data-ttu-id="14a01-220">3.0 以降では、EF Core で、トリガー条件が検出されるとすぐに連鎖操作が適用されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-220">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="14a01-221">たとえば、プリンシパル エンティティを削除するために `context.Remove()` を呼び出すと、すべての追跡対象の関連する必要な依存関係もすぐに `Deleted` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-221">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="14a01-222">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-222">**Why**</span></span>

<span data-ttu-id="14a01-223">この変更は、`SaveChanges` が呼び出される_前に_どのエンティティが削除されるかを把握することが重要である、データ バインディングおよび監査シナリオのエクスペリエンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-223">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="14a01-224">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-224">**Mitigations**</span></span>

<span data-ttu-id="14a01-225">`context.ChangedTracker` の設定を使用して、以前の動作を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="14a01-225">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="14a01-226">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-226">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="14a01-227">DeleteBehavior.Restrict のセマンティクスがクリーンになりました</span><span class="sxs-lookup"><span data-stu-id="14a01-227">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="14a01-228">問題 #12661 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-228">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="14a01-229">この変更は、EF Core 3.0 プレビュー 5 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-229">This change will be introduced in EF Core 3.0-preview 5.</span></span>

<span data-ttu-id="14a01-230">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-230">**Old behavior**</span></span>

<span data-ttu-id="14a01-231">3.0 以前は、`DeleteBehavior.Restrict` により `Restrict` セマンティクスでデータベースに外部キーが作成されましたが、内部の修正がどのように変更されたのかがはっきりしませんでした。</span><span class="sxs-lookup"><span data-stu-id="14a01-231">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="14a01-232">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-232">**New behavior**</span></span>

<span data-ttu-id="14a01-233">3.0 以降では、`DeleteBehavior.Restrict` により `Restrict` セマンティクスで外部キーが作成されます。つまり、カスケードがありません。EF 内部修正には影響を出さず、制約違反で例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="14a01-233">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="14a01-234">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-234">**Why**</span></span>

<span data-ttu-id="14a01-235">この変更は、副作用を出さず、直観的に `DeleteBehavior` を使用するために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-235">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="14a01-236">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-236">**Mitigations**</span></span>

<span data-ttu-id="14a01-237">`DeleteBehavior.ClientNoAction` を使用し、以前の動作を復元できます。</span><span class="sxs-lookup"><span data-stu-id="14a01-237">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

## <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="14a01-238">クエリ型がエンティティ型と統合される</span><span class="sxs-lookup"><span data-stu-id="14a01-238">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="14a01-239">問題 #14194 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-239">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="14a01-240">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-240">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-241">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-241">**Old behavior**</span></span>

<span data-ttu-id="14a01-242">EF Core 3.0 より前では、[クエリ型](xref:core/modeling/query-types)は、構造化された方法で主キーが定義されないデータのクエリを実行するための手段でした。</span><span class="sxs-lookup"><span data-stu-id="14a01-242">Before EF Core 3.0, [query types](xref:core/modeling/query-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="14a01-243">つまり、クエリ型は、キーが利用できるときに (テーブルからの可能性が高くなりますが、ビューからの可能性もあります) 通常のエンティティ型が使用された場合に、キーなしの (ビューからの可能性が高くなりますが、テーブルからの可能性もあります) エンティティ型をマップするために使用されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-243">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="14a01-244">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-244">**New behavior**</span></span>

<span data-ttu-id="14a01-245">クエリ型は、現在、主キーなしの単なるエンティティ型になります。</span><span class="sxs-lookup"><span data-stu-id="14a01-245">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="14a01-246">キーなしのエンティティ型の機能は、以前のバージョンのクエリ型と同じです。</span><span class="sxs-lookup"><span data-stu-id="14a01-246">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="14a01-247">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-247">**Why**</span></span>

<span data-ttu-id="14a01-248">この変更は、クエリ型の目的に関する混乱を減らすために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-248">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="14a01-249">つまり、これらはキーなしのエンティティ型であるため、本質的には読み取り専用となりますが、単にエンティティ型を読み取り専用にする必要があるという理由で使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="14a01-249">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="14a01-250">同様に、これらは多くの場合、ビューにマップされますが、これは、単にビューでは多くの場合、キーが定義されないためです。</span><span class="sxs-lookup"><span data-stu-id="14a01-250">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="14a01-251">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-251">**Mitigations**</span></span>

<span data-ttu-id="14a01-252">API の以下の部分は使用されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-252">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="14a01-253">**`ModelBuilder.Query<>()`** - 代わりに `ModelBuilder.Entity<>().HasNoKey()` を呼び出して、エンティティ型をキーなしとしてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-253">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="14a01-254">これは、規則により、主キーは必要であるが、規則と一致しない場合に構成エラーを回避するようにはまだ構成されません。</span><span class="sxs-lookup"><span data-stu-id="14a01-254">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="14a01-255">**`DbQuery<>`** - 代わりに `DbSet<>` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-255">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="14a01-256">**`DbContext.Query<>()`** - 代わりに `DbContext.Set<>()` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-256">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="14a01-257">所有型のリレーションシップ用の構成 API が変更された</span><span class="sxs-lookup"><span data-stu-id="14a01-257">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="14a01-258">[問題 #12444 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[問題 #9148 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[問題 #14153 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="14a01-258">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="14a01-259">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-259">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-260">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-260">**Old behavior**</span></span>

<span data-ttu-id="14a01-261">EF Core 3.0 より前では、所有リレーションシップの構成は、`OwnsOne` または `OwnsMany` の呼び出し直後に実行されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-261">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="14a01-262">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-262">**New behavior**</span></span>

<span data-ttu-id="14a01-263">EF Core 3.0 以降では、fluent API で、`WithOwner()` を使用して、所有者に対してナビゲーション プロパティが構成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-263">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="14a01-264">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-264">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="14a01-265">現在、所有者と所有型間のリレーションシップに関する構成は、他のリレーションシップの構成方法と同様、`WithOwner()` 後にチェーンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-265">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="14a01-266">一方、所有型自体の構成は引き続き、`OwnsOne()/OwnsMany()` 後にチェーンされます。</span><span class="sxs-lookup"><span data-stu-id="14a01-266">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="14a01-267">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-267">For example:</span></span>

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

<span data-ttu-id="14a01-268">さらに所有型ターゲットでの `Entity()`、`HasOne()`、または `Set()` の呼び出しでは、例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="14a01-268">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="14a01-269">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-269">**Why**</span></span>

<span data-ttu-id="14a01-270">この変更は、所有型自体と、所有型_へのリレーションシップ_の構成を明確に分離するために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-270">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="14a01-271">これにより、`HasForeignKey` のようなメソッドに関するあいまいさと混乱はなくなります。</span><span class="sxs-lookup"><span data-stu-id="14a01-271">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="14a01-272">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-272">**Mitigations**</span></span>

<span data-ttu-id="14a01-273">上記の例で示すように、新しい API サーフェスを使用するように、所有型リレーションシップの構成を変更します。</span><span class="sxs-lookup"><span data-stu-id="14a01-273">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="14a01-274">プリンシパルとテーブルを共有する依存エンティティが省略可能になりました</span><span class="sxs-lookup"><span data-stu-id="14a01-274">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="14a01-275">問題 #9005 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-275">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="14a01-276">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-276">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-277">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-277">**Old behavior**</span></span>

<span data-ttu-id="14a01-278">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="14a01-278">Consider the following model:</span></span>
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
<span data-ttu-id="14a01-279">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、新しい `Order` を追加する場合には常に `OrderDetails` インスタンスが必要でした。</span><span class="sxs-lookup"><span data-stu-id="14a01-279">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="14a01-280">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-280">**New behavior**</span></span>

<span data-ttu-id="14a01-281">EF Core 3.0 以降では、`OrderDetails` なしで `Order` を追加することができ、主キー以外のすべての `OrderDetails` プロパティが NULL 値が許可される列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="14a01-281">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="14a01-282">`OrderDetails` は、必要なプロパティのいずれにも値がない場合、または主キー以外に必要なプロパティがなく、すべてのプロパティが `null` の場合、EF Core のクエリの実行時に `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-282">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="14a01-283">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-283">**Mitigations**</span></span>

<span data-ttu-id="14a01-284">モデルに省略可能なすべての列と依存関係を共有するテーブルがあるが、それを指すナビゲーションが `null` になることが想定されていない場合は、ナビゲーションが `null` の場合のケースを処理するようにアプリケーションを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-284">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="14a01-285">これができない場合は、必要なプロパティをエンティティ型に追加するか、少なくとも 1 つのプロパティに割り当てられている値が `null` 以外である必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-285">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

## <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="14a01-286">同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-286">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="14a01-287">問題 #14154 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-287">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="14a01-288">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-288">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-289">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-289">**Old behavior**</span></span>

<span data-ttu-id="14a01-290">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="14a01-290">Consider the following model:</span></span>
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
<span data-ttu-id="14a01-291">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、`OrderDetails` だけを更新してもクライアント上の `Version` 値は更新されず、次回の更新が失敗します。</span><span class="sxs-lookup"><span data-stu-id="14a01-291">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="14a01-292">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-292">**New behavior**</span></span>

<span data-ttu-id="14a01-293">EF Core 3.0 以降では、新しい `Version` 値が `OrderDetails` を所有している場合には `Order` に伝達されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-293">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="14a01-294">それ以外の場合は、モデルの検証中に例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="14a01-294">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="14a01-295">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-295">**Why**</span></span>

<span data-ttu-id="14a01-296">この変更は、エンティティの 1 つだけが同じテーブルにマップされている場合に、古い同時実行トークン値が更新されるのを回避するために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-296">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="14a01-297">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-297">**Mitigations**</span></span>

<span data-ttu-id="14a01-298">テーブルを共有するすべてのエンティティには、同時実行トークン列にマップされるプロパティを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-298">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="14a01-299">シャドウ状態で作成することができます。</span><span class="sxs-lookup"><span data-stu-id="14a01-299">It's possible the create one in shadow-state:</span></span>
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

## <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="14a01-300">マップされていない型から継承されたプロパティは、すべての派生型の 1 つの列にマップされるようになりました</span><span class="sxs-lookup"><span data-stu-id="14a01-300">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="14a01-301">問題 #13998 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-301">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="14a01-302">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-302">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-303">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-303">**Old behavior**</span></span>

<span data-ttu-id="14a01-304">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="14a01-304">Consider the following model:</span></span>
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

<span data-ttu-id="14a01-305">EF Core 3.0 より前のバージョンでは、`ShippingAddress` プロパティは既定で `BulkOrder` と `Order` の個別の列にマップされていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-305">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="14a01-306">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-306">**New behavior**</span></span>

<span data-ttu-id="14a01-307">EF Core 3.0 以降では、`ShippingAddress` に対して 1 つの列だけが作成されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-307">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="14a01-308">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-308">**Why**</span></span>

<span data-ttu-id="14a01-309">以前の動作は予期しないものでした。</span><span class="sxs-lookup"><span data-stu-id="14a01-309">The old behavoir was unexpected.</span></span>

<span data-ttu-id="14a01-310">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-310">**Mitigations**</span></span>

<span data-ttu-id="14a01-311">プロパティは、派生型の個別の列に引き続き明示的にマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="14a01-311">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="14a01-312">外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった</span><span class="sxs-lookup"><span data-stu-id="14a01-312">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="14a01-313">問題 #13274 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-313">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="14a01-314">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-314">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-315">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-315">**Old behavior**</span></span>

<span data-ttu-id="14a01-316">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="14a01-316">Consider the following model:</span></span>
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
<span data-ttu-id="14a01-317">EF Core 3.0 より前では、規則により、外部キーで `CustomerId` プロパティが使用されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-317">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="14a01-318">しかし、`Order` が所有型である場合、`CustomerId` も主キーとマークされ、これは通常、期待されることではありません。</span><span class="sxs-lookup"><span data-stu-id="14a01-318">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="14a01-319">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-319">**New behavior**</span></span>

<span data-ttu-id="14a01-320">EF Core 3.0 以降では、プリンシパル プロパティと同じ名前である場合、規則により、外部キーに対するプロパティの使用は試行されません。</span><span class="sxs-lookup"><span data-stu-id="14a01-320">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="14a01-321">プリンシパル プロパティ名と連結されたプリンシパル型名、およびプリンシパル プロパティ名パターンと連結されたナビゲーション名は、引き続き一致します。</span><span class="sxs-lookup"><span data-stu-id="14a01-321">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="14a01-322">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-322">For example:</span></span>

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

<span data-ttu-id="14a01-323">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-323">**Why**</span></span>

<span data-ttu-id="14a01-324">この変更は、所有型に主キー プロパティが誤って定義されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-324">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="14a01-325">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-325">**Mitigations**</span></span>

<span data-ttu-id="14a01-326">プロパティを外部キーにする、したがって、主キーの一部にする予定だった場合は、そのように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="14a01-326">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

## <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="14a01-327">データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになりました</span><span class="sxs-lookup"><span data-stu-id="14a01-327">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="14a01-328">問題 #14218 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-328">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="14a01-329">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-329">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-330">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-330">**Old behavior**</span></span>

<span data-ttu-id="14a01-331">EF Core 3.0 より前のバージョンでは、コンテキストにより `TransactionScope` 内部で接続が開かれると、現在の `TransactionScope` がアクティブの間、接続が開いたままになります。</span><span class="sxs-lookup"><span data-stu-id="14a01-331">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

<span data-ttu-id="14a01-332">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-332">**New behavior**</span></span>

<span data-ttu-id="14a01-333">EF Core 3.0 以降では、使用が終了したらすぐに接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="14a01-333">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="14a01-334">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-334">**Why**</span></span>

<span data-ttu-id="14a01-335">この変更により、同じ `TransactionScope` で複数のコンテキストを使用できます。</span><span class="sxs-lookup"><span data-stu-id="14a01-335">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="14a01-336">新しい動作は、EF6 にも一致します。</span><span class="sxs-lookup"><span data-stu-id="14a01-336">The new behavior also matches EF6.</span></span>

<span data-ttu-id="14a01-337">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-337">**Mitigations**</span></span>

<span data-ttu-id="14a01-338">接続を開いたままにする必要がある場合は、`OpenConnection()` を明示的に呼び出して、EF Core が途中で閉じないようにします。</span><span class="sxs-lookup"><span data-stu-id="14a01-338">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="14a01-339">各プロパティで独立したメモリ内整数キー生成が使用される</span><span class="sxs-lookup"><span data-stu-id="14a01-339">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="14a01-340">問題 #6872 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-340">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="14a01-341">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-341">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-342">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-342">**Old behavior**</span></span>

<span data-ttu-id="14a01-343">EF Core 3.0 より前では、1 つの共有値ジェネレーターが、すべてのメモリ内整数キー プロパティで使用されていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-343">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="14a01-344">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-344">**New behavior**</span></span>

<span data-ttu-id="14a01-345">EF Core 3.0 以降では、各整数キー プロパティで、メモリ内データベースを使用するときに独自の値ジェネレーターが取得されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-345">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="14a01-346">また、データベースが削除された場合は、すべてのテーブルに対してキーの生成がリセットされます。</span><span class="sxs-lookup"><span data-stu-id="14a01-346">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="14a01-347">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-347">**Why**</span></span>

<span data-ttu-id="14a01-348">この変更は、実際のデータベース キー生成により近くなるようにメモリ内キー生成を調整するため、また、メモリ内データベースを使用するときに互いのテストを分離させる機能を向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-348">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="14a01-349">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-349">**Mitigations**</span></span>

<span data-ttu-id="14a01-350">これにより、設定される特定のメモリ内キー値に依存しているアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-350">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="14a01-351">代わりに特定のキーの値に依存しないようにするか、あるいは新しい動作と一致するように更新することを検討します。</span><span class="sxs-lookup"><span data-stu-id="14a01-351">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

## <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="14a01-352">バッキング フィールドが既定で使用される</span><span class="sxs-lookup"><span data-stu-id="14a01-352">Backing fields are used by default</span></span>

[<span data-ttu-id="14a01-353">問題 #12430 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-353">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="14a01-354">この変更は、EF Core 3.0 プレビュー 2 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-354">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="14a01-355">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-355">**Old behavior**</span></span>

<span data-ttu-id="14a01-356">3.0 より前では、プロパティのバッキング フィールドがわかっていた場合でも、EF Core では引き続き、既定でプロパティの getter および setter メソッドを使用して、プロパティ値の読み取りと書き込みが行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-356">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="14a01-357">ただし、バッキング フィールドが直接設定されることがわかっている場合のクエリの実行は例外です。</span><span class="sxs-lookup"><span data-stu-id="14a01-357">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="14a01-358">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-358">**New behavior**</span></span>

<span data-ttu-id="14a01-359">EF Core 3.0 以降では、プロパティのバッキング フィールドがわかっている場合、バッキング フィールドを使用して、常に EF Core がそのプロパティの読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="14a01-359">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="14a01-360">アプリケーションが getter および setter メソッドにコード化された追加動作に依存している場合、これによりアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-360">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="14a01-361">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-361">**Why**</span></span>

<span data-ttu-id="14a01-362">この変更は、EF Core が、エンティティに関するデータベース操作の実行時に、既定でビジネス ロジックを誤ってトリガーしないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-362">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="14a01-363">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-363">**Mitigations**</span></span>

<span data-ttu-id="14a01-364">3.0 より前の動作は、modelBuilder fluent API でプロパティ アクセス モードの構成を使用して、復元できます。</span><span class="sxs-lookup"><span data-stu-id="14a01-364">The pre-3.0 behavior can be restored through configuration of the property access mode in the modelBuilder fluent API.</span></span>
<span data-ttu-id="14a01-365">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-365">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="14a01-366">複数の互換性があるバッキング フィールドが見つかった場合にスローされる</span><span class="sxs-lookup"><span data-stu-id="14a01-366">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="14a01-367">問題 #12523 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-367">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="14a01-368">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-368">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-369">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-369">**Old behavior**</span></span>

<span data-ttu-id="14a01-370">EF Core 3.0 より前では、複数のフィールドが、プロパティのバッキング フィールドを見つけるための規則と一致した場合、何らかの優先順位に基づいて、1 つのフィールドが選択されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-370">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="14a01-371">これにより、あいまいなケースで不適切なフィールドが使用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-371">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="14a01-372">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-372">**New behavior**</span></span>

<span data-ttu-id="14a01-373">EF Core 3.0 以降では、複数のフィールドが同じプロパティと一致した場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="14a01-373">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="14a01-374">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-374">**Why**</span></span>

<span data-ttu-id="14a01-375">この変更は、1 つのフィールドのみが適切である可能性がある場合に、他のものより優先して暗黙的に 1 つのフィールドが使用されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-375">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="14a01-376">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-376">**Mitigations**</span></span>

<span data-ttu-id="14a01-377">あいまいなバッキング フィールドがあるプロパティでは、使用するフィールドを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-377">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="14a01-378">たとえば、fluent API を使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="14a01-378">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="14a01-379">フィールド専用プロパティの名前はフィールドの名前に一致する必要があります</span><span class="sxs-lookup"><span data-stu-id="14a01-379">Field-only property names should match the field name</span></span>

<span data-ttu-id="14a01-380">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-380">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-381">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-381">**Old behavior**</span></span>

<span data-ttu-id="14a01-382">EF Core 3.0 以前では、プロパティは文字列値により指定できました。CLR 型でその名前のプロパティが見つからなかった場合、EF Core では一般的な規則でフィールドの照合が試されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-382">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the CLR type then EF Core would try to match it to a field using convetion rules.</span></span>
```C#
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```
```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

<span data-ttu-id="14a01-383">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-383">**New behavior**</span></span>

<span data-ttu-id="14a01-384">EF Core 3.0 以降では、フィールド専用プロパティはフィールドの名前に厳密に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-384">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="14a01-385">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-385">**Why**</span></span>

<span data-ttu-id="14a01-386">この変更は、同じような名前が付けられた 2 つのプロパティに同じフィールドが使用されるのを回避する目的で行われました。また、フィールド専用プロパティの照合規則を、CLR プロパティにマッピングされているプロパティの場合と同じにします。</span><span class="sxs-lookup"><span data-stu-id="14a01-386">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="14a01-387">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-387">**Mitigations**</span></span>

<span data-ttu-id="14a01-388">フィールド専用プロパティには、それがマッピングされるフィールドと同じ名前を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-388">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="14a01-389">EF Core 3.0 の今後のプレビューでは、プロパティ名とは異なるフィールド名を明示的に構成できるように戻す予定です。</span><span class="sxs-lookup"><span data-stu-id="14a01-389">In a later preview of EF Core 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

## <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="14a01-390">AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなりました</span><span class="sxs-lookup"><span data-stu-id="14a01-390">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="14a01-391">問題 #14756 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-391">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="14a01-392">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-392">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-393">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-393">**Old behavior**</span></span>

<span data-ttu-id="14a01-394">EF Core 3.0 以前では、`AddDbContext` または `AddDbContextPool` を呼び出すと、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) および [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) への呼び出しを通じて、D.I を使ってログ記録とメモリ キャッシュ サービスも登録されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-394">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with D.I through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="14a01-395">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-395">**New behavior**</span></span>

<span data-ttu-id="14a01-396">EF Core 3.0 以降、`AddDbContext` と `AddDbContextPool` では、依存関係の挿入 (DI) を使ってこれらのサービスを登録しなくなります。</span><span class="sxs-lookup"><span data-stu-id="14a01-396">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="14a01-397">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-397">**Why**</span></span>

<span data-ttu-id="14a01-398">EF Core 3.0 では、これらのサービスをアプリケーションの DI コンテナーに含める必要がありません。</span><span class="sxs-lookup"><span data-stu-id="14a01-398">EF Core 3.0 does not require that these services are in the application's DI cotainer.</span></span> <span data-ttu-id="14a01-399">ただし、`ILoggerFactory` がアプリケーションの DI コンテナーに登録された場合、それは引き続き EF Core によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-399">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="14a01-400">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-400">**Mitigations**</span></span>

<span data-ttu-id="14a01-401">ご自身のアプリケーションでこれらのサービスが必要な場合は、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) または [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) を使って、DI コンテナーで明示的にそれらを登録します。</span><span class="sxs-lookup"><span data-stu-id="14a01-401">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="14a01-402">DbContext.Entry でローカルの DetectChanges が実行されるようになった</span><span class="sxs-lookup"><span data-stu-id="14a01-402">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="14a01-403">問題 #13552 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-403">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="14a01-404">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-404">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-405">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-405">**Old behavior**</span></span>

<span data-ttu-id="14a01-406">EF Core 3.0 より前では、`DbContext.Entry` を呼び出すと、追跡対象のすべてのエンティティで変更が検出されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-406">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="14a01-407">これにより、`EntityEntry` で示された状態が確実に最新のものとなりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-407">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="14a01-408">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-408">**New behavior**</span></span>

<span data-ttu-id="14a01-409">EF Core 3.0 以降では、`DbContext.Entry` の呼び出しで、特定のエンティティと、それに関連する追跡対象のプリンシパル エンティティでの変更のみの検出が試行されるようになります。</span><span class="sxs-lookup"><span data-stu-id="14a01-409">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="14a01-410">つまり、他の場所での変更は、このメソッドの呼び出しで検出されなかった可能性があり、アプリケーション状態に影響する場合があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-410">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="14a01-411">`ChangeTracker.AutoDetectChangesEnabled` が `false` に設定されている場合、このローカル変更の検出も無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="14a01-411">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="14a01-412">`ChangeTracker.Entries` や `SaveChanges` など、変更の検出の原因となる他のメソッドは引き続き、すべての追跡対象エンティティの完全な `DetectChanges` の原因となります。</span><span class="sxs-lookup"><span data-stu-id="14a01-412">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="14a01-413">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-413">**Why**</span></span>

<span data-ttu-id="14a01-414">この変更は、`context.Entry` を使用する既定のパフォーマンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-414">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="14a01-415">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-415">**Mitigations**</span></span>

<span data-ttu-id="14a01-416">3.0 より前の動作を確保するには、`Entry` を呼び出す前に明示的に `ChgangeTracker.DetectChanges()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="14a01-416">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="14a01-417">文字列とバイト配列のキーが既定でクライアントによって生成されない</span><span class="sxs-lookup"><span data-stu-id="14a01-417">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="14a01-418">問題 #14617 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-418">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="14a01-419">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-419">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-420">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-420">**Old behavior**</span></span>

<span data-ttu-id="14a01-421">EF Core 3.0 より前では、`string` と `byte[]` のキー プロパティは、null 以外の値を明示的に設定しなくても使用できました。</span><span class="sxs-lookup"><span data-stu-id="14a01-421">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="14a01-422">このような場合、キーの値は GUID としてクライアントで生成され、`byte[]` のバイトにシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-422">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="14a01-423">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-423">**New behavior**</span></span>

<span data-ttu-id="14a01-424">EF Core 3.0 以降では、キー値が設定されていないことを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="14a01-424">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="14a01-425">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-425">**Why**</span></span>

<span data-ttu-id="14a01-426">この変更は、クライアントで生成された `string`/`byte[]` 値が一般的に役に立たず、既定の動作では一般的な方法で生成されたキー値について判断するのが難しくなったため、行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-426">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="14a01-427">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-427">**Mitigations**</span></span>

<span data-ttu-id="14a01-428">3.0 より前の動作は、他の null 以外の値が設定されていない場合に、キー プロパティで生成された値を使用するように明示的に指定することで取得できます。</span><span class="sxs-lookup"><span data-stu-id="14a01-428">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="14a01-429">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="14a01-429">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="14a01-430">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="14a01-430">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="14a01-431">ILoggerFactory がスコープ サービスになった</span><span class="sxs-lookup"><span data-stu-id="14a01-431">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="14a01-432">問題 #14698 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-432">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="14a01-433">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-433">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-434">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-434">**Old behavior**</span></span>

<span data-ttu-id="14a01-435">EF Core 3.0 より前では、`ILoggerFactory` はシングルトン サービスとして登録されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-435">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="14a01-436">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-436">**New behavior**</span></span>

<span data-ttu-id="14a01-437">EF Core 3.0 以降では、`ILoggerFactory` がスコープ化されたものとして登録されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-437">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="14a01-438">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-438">**Why**</span></span>

<span data-ttu-id="14a01-439">この変更は、`DbContext` インスタンスでのロガーの関連付けを許可し、他の機能を有効にし、内部サービス プロバイダーの急増などの異常な動作の一部のケースをなくすために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-439">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="14a01-440">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-440">**Mitigations**</span></span>

<span data-ttu-id="14a01-441">この変更は、EF Core の内部サービス プロバイダーでカスタム サービスを登録して使用しない限り、アプリケーション コードに影響しないはずです。</span><span class="sxs-lookup"><span data-stu-id="14a01-441">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="14a01-442">これは一般的なことではありません。</span><span class="sxs-lookup"><span data-stu-id="14a01-442">This isn't common.</span></span>
<span data-ttu-id="14a01-443">このような場合、ほとんどのものが引き続き機能しますが、`ILoggerFactory` に依存していたシングルトン サービスは、異なる方法で `ILoggerFactory` を取得するように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-443">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="14a01-444">このような状況になった場合は、[EF Core GitHub の問題追跡ツール](https://github.com/aspnet/EntityFrameworkCore/issues)で問題を提出し、`ILoggerFactory` の使用方法をお知らせください。これにより、Microsoft では、今後、再びこのように中断しない方法についてよりよく理解できます。</span><span class="sxs-lookup"><span data-stu-id="14a01-444">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a><span data-ttu-id="14a01-445">IDbContextOptionsExtension に IDbContextOptionsExtensionWithDebugInfo が結合される</span><span class="sxs-lookup"><span data-stu-id="14a01-445">IDbContextOptionsExtensionWithDebugInfo merged into IDbContextOptionsExtension</span></span>

[<span data-ttu-id="14a01-446">問題 #13552 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-446">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="14a01-447">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-447">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-448">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-448">**Old behavior**</span></span>

<span data-ttu-id="14a01-449">`IDbContextOptionsExtensionWithDebugInfo` は、2.x リリース サイクル時のインターフェイスへの破壊的変更を回避するために、`IDbContextOptionsExtension` から拡張された追加のオプションのインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="14a01-449">`IDbContextOptionsExtensionWithDebugInfo` was an additional optional interface extended from `IDbContextOptionsExtension` to avoid making a breaking change to the interface during the 2.x release cycle.</span></span>

<span data-ttu-id="14a01-450">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-450">**New behavior**</span></span>

<span data-ttu-id="14a01-451">インターフェイスはまとめて `IDbContextOptionsExtension` に結合されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-451">The interfaces are now merged together into `IDbContextOptionsExtension`.</span></span>

<span data-ttu-id="14a01-452">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-452">**Why**</span></span>

<span data-ttu-id="14a01-453">この変更は、インターフェイスが概念的には 1 つであるため、行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-453">This change was made because the interfaces are conceptually one.</span></span>

<span data-ttu-id="14a01-454">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-454">**Mitigations**</span></span>

<span data-ttu-id="14a01-455">`IDbContextOptionsExtension` のすべての実装を、新しいメンバーをサポートするように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-455">Any implementations of `IDbContextOptionsExtension` will need to be updated to support the new member.</span></span>

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="14a01-456">遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった</span><span class="sxs-lookup"><span data-stu-id="14a01-456">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="14a01-457">問題 #12780 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-457">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="14a01-458">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-458">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-459">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-459">**Old behavior**</span></span>

<span data-ttu-id="14a01-460">EF Core 3.0 より前では、`DbContext` が破棄されると、そのコンテキストから取得されたエンティティの特定のナビゲーション プロパティが完全に読み込まれたかどうかを知る方法はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="14a01-460">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="14a01-461">プロキシでは、代わりに、null 以外の値がある場合は参照ナビゲーションが読み込まれ、空でない場合はコレクション ナビゲーションが読み込まれたと見なされます。</span><span class="sxs-lookup"><span data-stu-id="14a01-461">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="14a01-462">このような場合、遅延読み込みを試みても操作なしとなります。</span><span class="sxs-lookup"><span data-stu-id="14a01-462">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="14a01-463">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-463">**New behavior**</span></span>

<span data-ttu-id="14a01-464">EF Core 3.0 以降では、プロキシで、ナビゲーション プロパティが読み込まれたかどうかの追跡が行われます。</span><span class="sxs-lookup"><span data-stu-id="14a01-464">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="14a01-465">つまり、コンテキストが破棄された後に読み込まれたナビゲーション プロパティにアクセスしようとしても、通常は操作なしとなります。読み込まれたナビゲーションが空でも null であっても同様です。</span><span class="sxs-lookup"><span data-stu-id="14a01-465">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="14a01-466">逆に、コンテキストが破棄された場合、読み込まれなかったナビゲーション プロパティにアクセスしようとすると例外がスローされます。ナビゲーション プロパティが空ではないコレクションである場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="14a01-466">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="14a01-467">このような状況が発生した場合、アプリケーション コードで無効な時間に遅延読み込みの使用が試行されていることを意味し、アプリケーションを変更して、このようにならないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-467">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="14a01-468">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-468">**Why**</span></span>

<span data-ttu-id="14a01-469">この変更は、破棄された `DbContext` インスタンスで遅延読み込みが試行されたときの動作を一貫した正しいものにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-469">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="14a01-470">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-470">**Mitigations**</span></span>

<span data-ttu-id="14a01-471">破棄されたコンテキストで遅延読み込みが試行されないようにアプリケーション コードを更新するか、例外メッセージの説明に従って、これを操作なしとなるように構成します。</span><span class="sxs-lookup"><span data-stu-id="14a01-471">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="14a01-472">内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった</span><span class="sxs-lookup"><span data-stu-id="14a01-472">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="14a01-473">問題 #10236 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-473">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="14a01-474">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-474">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-475">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-475">**Old behavior**</span></span>

<span data-ttu-id="14a01-476">EF Core 3.0 より前では、異常な数の内部サービス プロバイダーを作成するアプリケーションに対する警告がログに記録されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-476">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="14a01-477">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-477">**New behavior**</span></span>

<span data-ttu-id="14a01-478">EF Core 3.0 以降では、この警告はエラーと見なされるようになり、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="14a01-478">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="14a01-479">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-479">**Why**</span></span>

<span data-ttu-id="14a01-480">この変更は、この異常なケースをより明示的に示すことで、アプリケーション コードの改善を促進するために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-480">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="14a01-481">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-481">**Mitigations**</span></span>

<span data-ttu-id="14a01-482">このエラーの発生時の最も適切なアクションは、根本原因を理解し、非常に多くの内部サービス プロバイダーを作成しないようにすることです。</span><span class="sxs-lookup"><span data-stu-id="14a01-482">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="14a01-483">しかし、`DbContextOptionsBuilder` での構成を使用して、エラーを警告に戻す (または無視する) ことがことができます。</span><span class="sxs-lookup"><span data-stu-id="14a01-483">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="14a01-484">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-484">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="14a01-485">1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作</span><span class="sxs-lookup"><span data-stu-id="14a01-485">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="14a01-486">問題 #9171 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-486">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="14a01-487">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-487">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-488">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-488">**Old behavior**</span></span>

<span data-ttu-id="14a01-489">EF Core 3.0 以前では、1 つの文字列と共に `HasOne` または `HasMany` を呼び出すコードは、わかりにくい方法で解釈されていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-489">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpretted in a confusing way.</span></span>
<span data-ttu-id="14a01-490">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-490">For example:</span></span>
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="14a01-491">このコードは、プライベートである可能性がある `Entrance` ナビゲーション プロパティを使って、`Samurai` を他のエンティティ型に関連付けているように見えます。</span><span class="sxs-lookup"><span data-stu-id="14a01-491">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="14a01-492">実際には、このコードは、ナビゲーション プロパティなしで、`Entrance` と呼ばれるエンティティ型に対してリレーションシップを作成しようとしています。</span><span class="sxs-lookup"><span data-stu-id="14a01-492">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="14a01-493">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-493">**New behavior**</span></span>

<span data-ttu-id="14a01-494">EF Core 3.0 以降では、上記のコードは、前にそれが実行すべきであるように見えたことを実行するようになりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-494">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="14a01-495">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-495">**Why**</span></span>

<span data-ttu-id="14a01-496">以前の動作は、特に構成コードを読み取ってエラーを探す場合は、非常にわかりにくいものでした。</span><span class="sxs-lookup"><span data-stu-id="14a01-496">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="14a01-497">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-497">**Mitigations**</span></span>

<span data-ttu-id="14a01-498">これが中断させるのは、型名の文字列を使って明示的にリレーションシップを構成し、かつ明示的にナビゲーション プロパティを指定しないアプリケーションのみです。</span><span class="sxs-lookup"><span data-stu-id="14a01-498">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="14a01-499">これは一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="14a01-499">This is not common.</span></span>
<span data-ttu-id="14a01-500">前の動作は、ナビゲーション プロパティ名に対して明示的に `null` を渡すことで実現できます。</span><span class="sxs-lookup"><span data-stu-id="14a01-500">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="14a01-501">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-501">For example:</span></span>

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

## <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="14a01-502">いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更されました</span><span class="sxs-lookup"><span data-stu-id="14a01-502">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="14a01-503">問題 #15184 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-503">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="14a01-504">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-504">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-505">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-505">**Old behavior**</span></span>

<span data-ttu-id="14a01-506">次の非同期メソッドでは、以前は `Task<T>` が返されていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-506">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="14a01-507">`ValueGenerator.NextValueAsync()` (および派生クラス)</span><span class="sxs-lookup"><span data-stu-id="14a01-507">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="14a01-508">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-508">**New behavior**</span></span>

<span data-ttu-id="14a01-509">前述のメソッドは、以前と同じ `T` に対して `ValueTask<T>` を返すようになりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-509">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="14a01-510">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-510">**Why**</span></span>

<span data-ttu-id="14a01-511">この変更により、これらのメソッドを呼び出すときに発生するヒープ割り当ての数を削減して全般的なパフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="14a01-511">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="14a01-512">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-512">**Mitigations**</span></span>

<span data-ttu-id="14a01-513">上記の API を待機しているだけのアプリケーションのみを再コンパイルする必要があります。ソースの変更は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="14a01-513">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="14a01-514">より複雑な使用方法 (返された `Task` を `Task.WhenAny()` に渡すなど) では通常、返された `ValueTask<T>` を `AsTask()` を呼び出すことによって `Task<T>` に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-514">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="14a01-515">これにより、この変更による割り当ての削減が無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="14a01-515">Note that this negates the allocation reduction that this change brings.</span></span>

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="14a01-516">Relational:TypeMapping 注釈が単に TypeMapping となった</span><span class="sxs-lookup"><span data-stu-id="14a01-516">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="14a01-517">問題 #9913 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-517">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="14a01-518">この変更は、EF Core 3.0 プレビュー 2 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-518">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="14a01-519">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-519">**Old behavior**</span></span>

<span data-ttu-id="14a01-520">型マッピング注釈の注釈名は "Relational:TypeMapping" でした。</span><span class="sxs-lookup"><span data-stu-id="14a01-520">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="14a01-521">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-521">**New behavior**</span></span>

<span data-ttu-id="14a01-522">型マッピング注釈の注釈名は、"TypeMapping" となりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-522">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="14a01-523">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-523">**Why**</span></span>

<span data-ttu-id="14a01-524">型マッピングが、リレーショナル データベース プロバイダー以外でも使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-524">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="14a01-525">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-525">**Mitigations**</span></span>

<span data-ttu-id="14a01-526">これにより、一般的ではない、注釈として直接型マッピングにアクセスするアプリケーションのみが中断されるようになります。</span><span class="sxs-lookup"><span data-stu-id="14a01-526">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="14a01-527">問題を解決するための最も適切なアクションは、注釈を直接使用するのではなく、API サーフェスを使用して型マッピングにアクセスすることです。</span><span class="sxs-lookup"><span data-stu-id="14a01-527">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

## <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="14a01-528">派生型の ToTable で例外がスローされる</span><span class="sxs-lookup"><span data-stu-id="14a01-528">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="14a01-529">問題 #11811 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-529">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="14a01-530">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-530">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-531">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-531">**Old behavior**</span></span>

<span data-ttu-id="14a01-532">EF Core 3.0 より前では、唯一の継承マッピング方法が TPH であり、これが有効でない場合には、派生型で呼び出された `ToTable()` が無視されていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-532">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="14a01-533">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-533">**New behavior**</span></span>

<span data-ttu-id="14a01-534">EF Core 3.0 以降、および今後のリリースでの TPT と TPC のサポートの追加準備中には、派生型で呼び出された `ToTable()` で、今後の予期されないマッピング変更を回避するために例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="14a01-534">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="14a01-535">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-535">**Why**</span></span>

<span data-ttu-id="14a01-536">現在、派生型を別のテーブルにマップすることは有効ではありません。</span><span class="sxs-lookup"><span data-stu-id="14a01-536">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="14a01-537">この変更により、今後、これを行うことが有効になった場合に中断されなくなります。</span><span class="sxs-lookup"><span data-stu-id="14a01-537">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="14a01-538">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-538">**Mitigations**</span></span>

<span data-ttu-id="14a01-539">派生型を他のテーブルにマップしないようにします。</span><span class="sxs-lookup"><span data-stu-id="14a01-539">Remove any attempts to map derived types to other tables.</span></span>

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="14a01-540">ForSqlServerHasIndex が HasIndex に置き換えられた</span><span class="sxs-lookup"><span data-stu-id="14a01-540">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="14a01-541">問題 #12366 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-541">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="14a01-542">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-542">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-543">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-543">**Old behavior**</span></span>

<span data-ttu-id="14a01-544">EF Core 3.0 より前では、`ForSqlServerHasIndex().ForSqlServerInclude()` によって、`INCLUDE` で使用される列を構成する方法が提供されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-544">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="14a01-545">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-545">**New behavior**</span></span>

<span data-ttu-id="14a01-546">EF Core 3.0 以降では、インデックスでの `Include` の使用が、リレーショナル レベルでサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-546">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="14a01-547">`HasIndex().ForSqlServerInclude()` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="14a01-547">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="14a01-548">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-548">**Why**</span></span>

<span data-ttu-id="14a01-549">この変更は、すべてのデータ プロバイダーのために `Include` でインデックス用の API を 1 か所に統合するために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-549">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="14a01-550">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-550">**Mitigations**</span></span>

<span data-ttu-id="14a01-551">上記のように、新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="14a01-551">Use the new API, as shown above.</span></span>

## <a name="metadata-api-changes"></a><span data-ttu-id="14a01-552">メタデータ API の変更点</span><span class="sxs-lookup"><span data-stu-id="14a01-552">Metadata API changes</span></span>

[<span data-ttu-id="14a01-553">問題 #214 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-553">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="14a01-554">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-554">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-555">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-555">**New behavior**</span></span>

<span data-ttu-id="14a01-556">次のプロパティは拡張メソッドに変換されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-556">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="14a01-557">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-557">**Why**</span></span>

<span data-ttu-id="14a01-558">この変更により、前述のインターフェイスの実装が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="14a01-558">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="14a01-559">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-559">**Mitigations**</span></span>

<span data-ttu-id="14a01-560">新しい拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="14a01-560">Use the new extension methods.</span></span>

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="14a01-561">EF Core で SQLite FK を適用するためのプラグマが送信されなくなった</span><span class="sxs-lookup"><span data-stu-id="14a01-561">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="14a01-562">問題 #12151 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-562">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="14a01-563">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-563">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="14a01-564">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-564">**Old behavior**</span></span>

<span data-ttu-id="14a01-565">EF Core 3.0 より前では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-565">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="14a01-566">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-566">**New behavior**</span></span>

<span data-ttu-id="14a01-567">EF Core 3.0 以降では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-567">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="14a01-568">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-568">**Why**</span></span>

<span data-ttu-id="14a01-569">この変更は、既定で EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用され、その後、FK の適用が既定で有効になり、接続が開かれるたびに明示的に有効にする必要がないため、行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-569">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="14a01-570">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-570">**Mitigations**</span></span>

<span data-ttu-id="14a01-571">外部キーは、EF Core に対して既定で使用される、SQLitePCLRaw.bundle_e_sqlite3 で既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="14a01-571">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="14a01-572">それ以外の場合は、接続文字列で `Foreign Keys=True` を指定することで、外部キーを有効にできます。</span><span class="sxs-lookup"><span data-stu-id="14a01-572">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a><span data-ttu-id="14a01-573">Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった</span><span class="sxs-lookup"><span data-stu-id="14a01-573">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="14a01-574">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-574">**Old behavior**</span></span>

<span data-ttu-id="14a01-575">EF Core 3.0 より前では、EF Core では `SQLitePCLRaw.bundle_green` が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-575">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="14a01-576">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-576">**New behavior**</span></span>

<span data-ttu-id="14a01-577">EF Core 3.0 以降では、EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-577">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="14a01-578">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-578">**Why**</span></span>

<span data-ttu-id="14a01-579">この変更は、iOS 上で使用される SQLite のバージョンを、他のプラットフォームと一致させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="14a01-579">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="14a01-580">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-580">**Mitigations**</span></span>

<span data-ttu-id="14a01-581">iOS でネイティブの SQLite バージョンを使用するには、別の `SQLitePCLRaw` バンドルを使用するように `Microsoft.Data.Sqlite` を構成します。</span><span class="sxs-lookup"><span data-stu-id="14a01-581">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

## <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="14a01-582">GUID の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="14a01-582">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="14a01-583">問題 #15078 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-583">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="14a01-584">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-584">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-585">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-585">**Old behavior**</span></span>

<span data-ttu-id="14a01-586">GUID の値は、以前は SQLite に BLOB 値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-586">Guid values were previously sored as BLOB values on SQLite.</span></span>

<span data-ttu-id="14a01-587">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-587">**New behavior**</span></span>

<span data-ttu-id="14a01-588">GUID の値はテキストとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-588">Guid values are now sotred as TEXT.</span></span>

<span data-ttu-id="14a01-589">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-589">**Why**</span></span>

<span data-ttu-id="14a01-590">GUID のバイナリ形式は標準化されていません。</span><span class="sxs-lookup"><span data-stu-id="14a01-590">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="14a01-591">値をテキストとして格納する方が、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="14a01-591">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="14a01-592">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-592">**Mitigations**</span></span>

<span data-ttu-id="14a01-593">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="14a01-593">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

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

<span data-ttu-id="14a01-594">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="14a01-594">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="14a01-595">Microsoft.Data.Sqlite は引き続き、BLOB とテキストの両方の列から GUID 値を読み取ることができます。ただし、パラメーターと定数の既定の形式が変更されているため、GUID が含まれる多くのシナリオではアクションが必要になります。</span><span class="sxs-lookup"><span data-stu-id="14a01-595">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

## <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="14a01-596">Char の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="14a01-596">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="14a01-597">問題 #15020 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-597">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="14a01-598">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-598">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-599">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-599">**Old behavior**</span></span>

<span data-ttu-id="14a01-600">Char の値は、以前は SQLite に整数値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-600">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="14a01-601">たとえば、Char の値 *A* は整数値 65 として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-601">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="14a01-602">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-602">**New behavior**</span></span>

<span data-ttu-id="14a01-603">Char の値はテキストとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="14a01-603">Char values are now sotred as TEXT.</span></span>

<span data-ttu-id="14a01-604">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-604">**Why**</span></span>

<span data-ttu-id="14a01-605">値をテキストとして格納する方が自然であり、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="14a01-605">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="14a01-606">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-606">**Mitigations**</span></span>

<span data-ttu-id="14a01-607">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="14a01-607">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="14a01-608">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="14a01-608">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="14a01-609">Microsoft.Data.Sqlite では、引き続き整数とテキストの両方の列の文字列値を読み取ることができるため、一部のシナリオではアクションが必要ありません。</span><span class="sxs-lookup"><span data-stu-id="14a01-609">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

## <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="14a01-610">移行 ID がインバリアント カルチャの暦を使用して生成されるようになった</span><span class="sxs-lookup"><span data-stu-id="14a01-610">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="14a01-611">問題 #12978 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-611">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="14a01-612">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-612">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-613">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-613">**Old behavior**</span></span>

<span data-ttu-id="14a01-614">移行 ID は選択されているカルチャの暦を使用して意図なく生成されていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-614">Migration IDs were inadvertantly generated using the currret culture's calendar.</span></span>

<span data-ttu-id="14a01-615">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-615">**New behavior**</span></span>

<span data-ttu-id="14a01-616">移行 ID がインバリアント カルチャの暦 (グレゴリオ暦) を使用して常に生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-616">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="14a01-617">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-617">**Why**</span></span>

<span data-ttu-id="14a01-618">データベースを更新するときやマージの競合を解決するときに、移行の順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="14a01-618">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="14a01-619">インバリアントの暦を使用することで、順序の問題が回避され、システムの予定表がチームのメンバーごとに違うことがなくなります。</span><span class="sxs-lookup"><span data-stu-id="14a01-619">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="14a01-620">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-620">**Mitigations**</span></span>

<span data-ttu-id="14a01-621">この変更は、グレゴリオ暦以外の暦 (タイ仏暦など) を使用し、1 年がグレゴリオ暦より長くなるすべてのユーザーに影響します。</span><span class="sxs-lookup"><span data-stu-id="14a01-621">This change affects anyone using a non-Gregorian calender where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="14a01-622">既存の移行後に新しい移行順序が設定されるように、既存の移行 ID を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-622">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="14a01-623">移行 ID は、移行のデザイナー ファイルの移行属性にあります。</span><span class="sxs-lookup"><span data-stu-id="14a01-623">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="14a01-624">移行履歴テーブルも更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="14a01-624">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

## <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="14a01-625">LogQueryPossibleExceptionWithAggregateOperator の名前が変更された</span><span class="sxs-lookup"><span data-stu-id="14a01-625">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="14a01-626">問題 #10985 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-626">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="14a01-627">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-627">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-628">**変更**</span><span class="sxs-lookup"><span data-stu-id="14a01-628">**Change**</span></span>

<span data-ttu-id="14a01-629">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` の名前が `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-629">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="14a01-630">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-630">**Why**</span></span>

<span data-ttu-id="14a01-631">この警告イベントの名前が他のすべての警告イベントと照合されるためです。</span><span class="sxs-lookup"><span data-stu-id="14a01-631">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="14a01-632">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-632">**Mitigations**</span></span>

<span data-ttu-id="14a01-633">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="14a01-633">Use the new name.</span></span> <span data-ttu-id="14a01-634">(イベント ID 番号が変更されていないことを確認してください。)</span><span class="sxs-lookup"><span data-stu-id="14a01-634">(Note that the event ID number has not changed.)</span></span>

## <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="14a01-635">外部キー制約名の API が明確化された</span><span class="sxs-lookup"><span data-stu-id="14a01-635">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="14a01-636">問題 #10730 の追跡</span><span class="sxs-lookup"><span data-stu-id="14a01-636">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="14a01-637">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="14a01-637">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="14a01-638">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-638">**Old behavior**</span></span>

<span data-ttu-id="14a01-639">EF Core 3.0 以前は、外部キー制約名が単に "名前" と呼ばれていました。</span><span class="sxs-lookup"><span data-stu-id="14a01-639">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="14a01-640">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-640">For example:</span></span>

```C#
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="14a01-641">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="14a01-641">**New behavior**</span></span>

<span data-ttu-id="14a01-642">EF Core 3.0 以降は、外部キー制約名が "定数名" と呼ばれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-642">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constaint name".</span></span> <span data-ttu-id="14a01-643">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="14a01-643">For example:</span></span>

```C#
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="14a01-644">**理由**</span><span class="sxs-lookup"><span data-stu-id="14a01-644">**Why**</span></span>

<span data-ttu-id="14a01-645">この変更により、この領域の名前付けに一貫性が生まれ、それが外部キーが定義されている列やプロパティの名前ではなく、外部キー制約の名前であることが明確になりました。</span><span class="sxs-lookup"><span data-stu-id="14a01-645">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constaint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="14a01-646">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="14a01-646">**Mitigations**</span></span>

<span data-ttu-id="14a01-647">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="14a01-647">Use the new name.</span></span>
