---
title: EF Core 3.0 での破壊的変更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: 9112d8d235237e68232aac54453d584af0edb524
ms.sourcegitcommit: b188194a1901f4d086d05765cbc5c9b8c9dc5eed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2019
ms.locfileid: "66829493"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="eea32-102">EF Core 3.0 (現在プレビュー段階) に含まれる破壊的変更</span><span class="sxs-lookup"><span data-stu-id="eea32-102">Breaking changes included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="eea32-103">機能セットと今後のリリースのスケジュールは、常に変更される可能性があることに注意してください。また、このページを最新の状態に保持するようにしていますが、最新のプランが反映されていない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="eea32-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="eea32-104">以下の API と動作変更により、3.0.0 へのアップグレード時に、EF Core 2.2.x 用に開発されたアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-104">The following API and behavior changes have the potential to break applications developed for EF Core 2.2.x when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="eea32-105">データベース プロバイダーにのみ影響することが予想される変更については、[プロバイダーの変更](../../providers/provider-log.md)に関するページに記載されています。</span><span class="sxs-lookup"><span data-stu-id="eea32-105">Changes that we expect to only impact database providers are documented under [provider changes](../../providers/provider-log.md).</span></span>
<span data-ttu-id="eea32-106">ある 3.0 プレビューから別の 3.0 プレビューに導入される新機能での中断については、ここには記載されていません。</span><span class="sxs-lookup"><span data-stu-id="eea32-106">Breaks in new features introduced from one 3.0 preview to another 3.0 preview aren't documented here.</span></span>

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="eea32-107">LINQ クエリがクライアントで評価されなくなった</span><span class="sxs-lookup"><span data-stu-id="eea32-107">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="eea32-108">[問題 #14935 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[問題 #12795 も参照](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="eea32-108">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="eea32-109">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-109">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-110">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-110">**Old behavior**</span></span>

<span data-ttu-id="eea32-111">3\.0 より前では、EF Core では、SQL またはパラメーターに対するクエリの一部だった式を変換できない場合、クライアントで自動的に式が評価されていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-111">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="eea32-112">既定では、コストの高い式のクライアント評価でのみ警告がトリガーされました。</span><span class="sxs-lookup"><span data-stu-id="eea32-112">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="eea32-113">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-113">**New behavior**</span></span>

<span data-ttu-id="eea32-114">3\.0 以降では、クライアントで評価される最上位のプロジェクション (クエリの最後の `Select()` 呼び出し) の式のみが EF Core で許可されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-114">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="eea32-115">クエリの他の部分の式を SQL やパラメーターに変換できない場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="eea32-115">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="eea32-116">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-116">**Why**</span></span>

<span data-ttu-id="eea32-117">クエリの自動クライアント評価では、多くのクエリを実行できます。これらの重要な部分を変換できない場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="eea32-117">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="eea32-118">この動作が原因で、予期しない損害を与える動作となる可能性があります。この動作は、運用環境でのみ見られる場合があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-118">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="eea32-119">たとえば、変換できない `Where()` 呼び出しの条件が原因で、テーブルのすべての行がデータベース サーバーから転送できるようになり、クライアントでフィルターを適用できるようになる場合があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-119">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="eea32-120">このような状況は、開発時にテーブルにわずかな行が含まれている場合は、簡単に検出される可能性があります。しかし、テーブルに数百万もの行が含まれる、運用環境にアプリケーションを移行する場合は難しくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-120">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="eea32-121">クライアント評価の警告では、開発中にあまりに簡単に無視されることも示されていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-121">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="eea32-122">これだけでなく、自動クライアント評価は、特定の式のクエリ変換の改善が、リリース間の予期しない破壊的変更の原因となる問題につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-122">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="eea32-123">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-123">**Mitigations**</span></span>

<span data-ttu-id="eea32-124">クエリを完全に変換できない場合は、変換できる形式でクエリを書き直すか、`AsEnumerable()`、`ToList()`、または同様のものを使用して、LINQ-to-Objects を使ってさらに処理できるクライアントに明示的にデータを戻します。</span><span class="sxs-lookup"><span data-stu-id="eea32-124">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="eea32-125">Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="eea32-125">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="eea32-126">問題 Announcements#325 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-126">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="eea32-127">この変更は、ASP.NET Core 3.0 プレビュー 1 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-127">This change is introduced in ASP.NET Core 3.0-preview 1.</span></span> 

<span data-ttu-id="eea32-128">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-128">**Old behavior**</span></span>

<span data-ttu-id="eea32-129">ASP.NET Core 3.0 より前では、パッケージ参照を `Microsoft.AspNetCore.App` または `Microsoft.AspNetCore.All` に追加したときに、EF Core と、SQL Server プロバイダーのような EF Core データ プロバイダーの一部が含まれました。</span><span class="sxs-lookup"><span data-stu-id="eea32-129">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="eea32-130">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-130">**New behavior**</span></span>

<span data-ttu-id="eea32-131">3\.0 以降では、ASP.NET Core 共有フレームワークには、EF Core も、いずれの EF Core データ プロバイダーも含まれません。</span><span class="sxs-lookup"><span data-stu-id="eea32-131">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="eea32-132">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-132">**Why**</span></span>

<span data-ttu-id="eea32-133">この変更の前では、アプリケーションのターゲットが ASP.NET Core であるか SQL Server であるかに応じて、EF Core を取得するには異なる手順が必要でした。</span><span class="sxs-lookup"><span data-stu-id="eea32-133">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="eea32-134">また、ASP.NET Core のアップグレードでは、必ずしも適切だとは言えない、EF Core と SQL Server プロバイダーのアップグレードが強制されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-134">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="eea32-135">この変更では、EF Core の取得のエクスペリエンスは、.NET の実装とアプリケーションの種類がサポートされる、すべてのプロバイダーで同じになります。</span><span class="sxs-lookup"><span data-stu-id="eea32-135">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="eea32-136">また、開発者は、EF Core と EF Core データ プロバイダーのアップグレードのタイミングを正確に制御できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-136">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="eea32-137">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-137">**Mitigations**</span></span>

<span data-ttu-id="eea32-138">ASP.NET Core 3.0 アプリケーションまたはその他のサポートされるアプリケーションで EF Core を使用するには、アプリケーションで使用される EF Core データベース プロバイダーにパッケージ参照を明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="eea32-138">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

## <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="eea32-139">EF Core のコマンドライン ツールである dotnet ef が .NET Core SDK の一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="eea32-139">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="eea32-140">問題 #14016 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-140">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="eea32-141">この変更は、EF Core 3.0 プレビュー 4 および .NET Core SDK の対応するバージョンで導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-141">This change is introduced in EF Core 3.0-preview 4 and the corresponding version of the .NET Core SDK.</span></span>

<span data-ttu-id="eea32-142">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-142">**Old behavior**</span></span>

<span data-ttu-id="eea32-143">3\.0 より前は `dotnet ef` ツールが .NET Core SDK に含まれており、追加の手順を必要とせずに、任意のプロジェクトのコマンド ラインから簡単に使用できました。</span><span class="sxs-lookup"><span data-stu-id="eea32-143">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="eea32-144">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-144">**New behavior**</span></span>

<span data-ttu-id="eea32-145">3\.0 以降は .NET SDK に `dotnet ef` ツールが含まれないため、これを使用するにはローカルまたはグローバルなツールとして明示的にインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-145">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="eea32-146">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-146">**Why**</span></span>

<span data-ttu-id="eea32-147">この変更により、`dotnet ef` を通常の .NET CLI ツールとして NuGet 上で配信したり更新したりできるようになります。これは EF Core 3.0 も常に NuGet パッケージとして配信されるという事実と一致します。</span><span class="sxs-lookup"><span data-stu-id="eea32-147">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="eea32-148">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-148">**Mitigations**</span></span>

<span data-ttu-id="eea32-149">移行の管理や `DbContext` のスキャフォールディングを行うには、`dotnet-ef` を `dotnet tool install` コマンドを使用してインストールします。</span><span class="sxs-lookup"><span data-stu-id="eea32-149">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` using the `dotnet tool install` command.</span></span>
<span data-ttu-id="eea32-150">たとえば、グローバルなツールとしてインストールするには、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="eea32-150">For example, to install it as a global tool, you can type this command:</span></span>

  ``` console
  $ dotnet tool install --global dotnet-ef --version <exact-version>
  ```

<span data-ttu-id="eea32-151">[ツール マニフェスト ファイル](https://github.com/dotnet/cli/issues/10288)を使用してツールの依存関係として宣言するプロジェクトの依存関係を復元するときに、ローカルなツールとして取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="eea32-151">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](https://github.com/dotnet/cli/issues/10288).</span></span>

## <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="eea32-152">FromSql、ExecuteSql、および ExecuteSqlAsync の名前変更</span><span class="sxs-lookup"><span data-stu-id="eea32-152">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="eea32-153">問題 #10996 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-153">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="eea32-154">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-154">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-155">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-155">**Old behavior**</span></span>

<span data-ttu-id="eea32-156">EF Core 3.0 より前のバージョンでは、通常の文字列または SQL およびパラメーターに挿入する必要がある文字列で使用するために、これらのメソッド名がオーバーロードされました。</span><span class="sxs-lookup"><span data-stu-id="eea32-156">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="eea32-157">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-157">**New behavior**</span></span>

<span data-ttu-id="eea32-158">EF Core 3.0 以降では、`FromSqlRaw`、`ExecuteSqlRaw`、および `ExecuteSqlRawAsync` を使用して、パラメーターがクエリ文字列とは別に渡される、パラメーター化クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-158">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="eea32-159">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-159">For example:</span></span>

```C#
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="eea32-160">`FromSqlInterpolated`、`ExecuteSqlInterpolated`、および `ExecuteSqlInterpolatedAsync` を使用して、パラメーターが挿入クエリ文字列の一部として渡されるパラメーター化クエリを作成します。</span><span class="sxs-lookup"><span data-stu-id="eea32-160">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="eea32-161">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-161">For example:</span></span>

```C#
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="eea32-162">上記のクエリはどちらも、同じ SQL パラメーターを持つ同じパラメーター化 SQL が生成されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="eea32-162">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="eea32-163">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-163">**Why**</span></span>

<span data-ttu-id="eea32-164">このようなメソッド オーバーロードは、挿入文字列メソッドを呼び出すつもりが、誤って raw 文字列メソッドを非常に簡単に呼び出せてしまいます。その逆も同様です。</span><span class="sxs-lookup"><span data-stu-id="eea32-164">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="eea32-165">これは、クエリをパラメーター化する必要があるときに、パラメーター化されない結果になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-165">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="eea32-166">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-166">**Mitigations**</span></span>

<span data-ttu-id="eea32-167">新しいメソッド名を使用するように切り替えます。</span><span class="sxs-lookup"><span data-stu-id="eea32-167">Switch to use the new method names.</span></span>

## <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="eea32-168">FromSql メソッドはクエリのルートでのみ指定できる</span><span class="sxs-lookup"><span data-stu-id="eea32-168">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="eea32-169">問題 #15704 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-169">Tracking Issue #15704</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

<span data-ttu-id="eea32-170">この変更は、EF Core 3.0 プレビュー 6 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-170">This change is introduced in EF Core 3.0-preview 6.</span></span>

<span data-ttu-id="eea32-171">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-171">**Old behavior**</span></span>

<span data-ttu-id="eea32-172">EF Core 3.0 以前は、クエリ内の任意の場所で `FromSql` メソッドを指定できました。</span><span class="sxs-lookup"><span data-stu-id="eea32-172">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="eea32-173">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-173">**New behavior**</span></span>

<span data-ttu-id="eea32-174">EF Core 3.0 以降、新しい `FromSqlRaw` および `FromSqlInterpolated` メソッド (`FromSql` の置き換え) は、クエリのルート上でのみ (つまり `DbSet<>` で直接) 指定できます。</span><span class="sxs-lookup"><span data-stu-id="eea32-174">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="eea32-175">他の場所でそれらを指定しようとすると、コンパイル エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="eea32-175">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="eea32-176">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-176">**Why**</span></span>

<span data-ttu-id="eea32-177">`DbSet` 以外の任意の場所で `FromSql` を指定しても、さらなる意味や価値が追加されることはなく、特定のシナリオではあいまいさの原因となる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-177">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="eea32-178">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-178">**Mitigations**</span></span>

<span data-ttu-id="eea32-179">`FromSql` の呼び出し場所を移動して、それらが適用される `DbSet` 上で直接実行されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-179">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

## <a name="query-execution-is-logged-at-debug-level"></a><span data-ttu-id="eea32-180">クエリの実行がデバッグ レベルでログに記録される</span><span class="sxs-lookup"><span data-stu-id="eea32-180">Query execution is logged at Debug level</span></span>

[<span data-ttu-id="eea32-181">問題 #14523 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-181">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="eea32-182">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-182">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-183">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-183">**Old behavior**</span></span>

<span data-ttu-id="eea32-184">EF Core 3.0 より前では、クエリと他のコマンドの実行は、`Info` レベルでログに記録されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-184">Before EF Core 3.0, execution of queries and other commands was logged at the `Info` level.</span></span>

<span data-ttu-id="eea32-185">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-185">**New behavior**</span></span>

<span data-ttu-id="eea32-186">EF Core 3.0 以降では、コマンド/SQL の実行は、`Debug` レベルでログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-186">Starting with EF Core 3.0, logging of command/SQL execution is at the `Debug` level.</span></span>

<span data-ttu-id="eea32-187">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-187">**Why**</span></span>

<span data-ttu-id="eea32-188">この変更は、`Info` ログ レベルでのノイズを減らすために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-188">This change was made to reduce the noise at the `Info` log level.</span></span>

<span data-ttu-id="eea32-189">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-189">**Mitigations**</span></span>

<span data-ttu-id="eea32-190">このログ イベントは、イベント ID 20100 の `RelationalEventId.CommandExecuting` によって定義されています。</span><span class="sxs-lookup"><span data-stu-id="eea32-190">This logging event is defined by `RelationalEventId.CommandExecuting` with event ID 20100.</span></span>
<span data-ttu-id="eea32-191">`Info` レベルで SQL をもう一度ログに記録するには、`OnConfiguring` または `AddDbContext` で明示的にレベルを構成します。</span><span class="sxs-lookup"><span data-stu-id="eea32-191">To log SQL at the `Info` level again, explicitly configure the level in `OnConfiguring` or `AddDbContext`.</span></span>
<span data-ttu-id="eea32-192">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-192">For example:</span></span>
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Info)));
```

## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="eea32-193">一時キーの値がエンティティ インスタンスに設定されなくなった</span><span class="sxs-lookup"><span data-stu-id="eea32-193">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="eea32-194">問題 #12378 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-194">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="eea32-195">この変更は、EF Core 3.0 プレビュー 2 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-195">This change is introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="eea32-196">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-196">**Old behavior**</span></span>

<span data-ttu-id="eea32-197">EF Core 3.0 より前では、後で実際の値がデータベースによって生成される、すべてのキー プロパティに一時的な値が割り当てられていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-197">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="eea32-198">通常、これらの一時的な値は大きい負の数値でした。</span><span class="sxs-lookup"><span data-stu-id="eea32-198">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="eea32-199">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-199">**New behavior**</span></span>

<span data-ttu-id="eea32-200">3\.0 以降では、EF Core で、エンティティの追跡情報の一部として一時キーの値が格納され、キー プロパティ自体はそのままになります。</span><span class="sxs-lookup"><span data-stu-id="eea32-200">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="eea32-201">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-201">**Why**</span></span>

<span data-ttu-id="eea32-202">この変更は、何らかの `DbContext` インスタンスによって以前に追跡されたエンティティが、別の `DbContext` インスタンスに移動されるときに、一時キーの値が誤って永続的なものにならないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-202">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="eea32-203">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-203">**Mitigations**</span></span>

<span data-ttu-id="eea32-204">主キーの値を外部キーに割り当てて、エンティティ間の関連付けを形成するアプリケーションは、主キーがストアで生成されており、`Added` 状態のエンティティに属している場合、以前の動作に依存する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-204">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="eea32-205">これは、次のようにして回避できます。</span><span class="sxs-lookup"><span data-stu-id="eea32-205">This can be avoided by:</span></span>
* <span data-ttu-id="eea32-206">ストア生成キーを使用しない。</span><span class="sxs-lookup"><span data-stu-id="eea32-206">Not using store-generated keys.</span></span>
* <span data-ttu-id="eea32-207">外部キーの値を設定するのではなく、ナビゲーション プロパティを設定してリレーションシップを形成する。</span><span class="sxs-lookup"><span data-stu-id="eea32-207">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="eea32-208">エンティティの追跡情報から実際の一時キーの値を取得する。</span><span class="sxs-lookup"><span data-stu-id="eea32-208">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="eea32-209">たとえば、`context.Entry(blog).Property(e => e.Id).CurrentValue` では、`blog.Id` 自体が設定されていない場合でも、一時的な値が返されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-209">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

## <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="eea32-210">DetectChanges でストア生成キーの値が優先される</span><span class="sxs-lookup"><span data-stu-id="eea32-210">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="eea32-211">問題 #14616 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-211">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="eea32-212">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-212">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-213">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-213">**Old behavior**</span></span>

<span data-ttu-id="eea32-214">EF Core 3.0 より前では、`DetectChanges` によって検出された追跡対象外のエンティティが `Added` 状態で追跡され、`SaveChanges` が呼び出されたときに新しい行として挿入されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-214">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="eea32-215">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-215">**New behavior**</span></span>

<span data-ttu-id="eea32-216">EF Core 3.0 以降では、エンティティで生成されたキーの値を使用し、何らかのキーの値が設定されている場合、そのエンティティは `Modified` 状態で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-216">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="eea32-217">つまり、エンティティの行が存在していると見なされ、`SaveChanges` の呼び出し時に更新されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-217">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="eea32-218">キーの値が設定されていないか、エンティティ型で生成されたキーが使用されない場合、新しいエンティティは引き続き、以前のバージョンと同様に `Added` として追跡されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-218">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="eea32-219">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-219">**Why**</span></span>

<span data-ttu-id="eea32-220">この変更は、ストア生成キーの使用中に、切り離されたエンティティ グラフをより簡単に一貫して操作できるようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-220">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="eea32-221">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-221">**Mitigations**</span></span>

<span data-ttu-id="eea32-222">エンティティ型が生成されたキーを使用するように構成されているものの、キーの値が新しいインスタンスに対して明示的に設定されている場合、この変更によってアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-222">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="eea32-223">この問題を解決するには、キー プロパティで生成された値が使用されないように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="eea32-223">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="eea32-224">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="eea32-224">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="eea32-225">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="eea32-225">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="eea32-226">既定で連鎖削除がすぐに行われるようになった</span><span class="sxs-lookup"><span data-stu-id="eea32-226">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="eea32-227">問題 #10114 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-227">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="eea32-228">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-228">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-229">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-229">**Old behavior**</span></span>

<span data-ttu-id="eea32-230">3\.0 より前では、EF Core で適用された連鎖操作 (必要なプリンシパルが削除されたか、必要なプリンシパルへのリレーションシップが切断されたときに依存エンティティを削除する) は、SaveChanges が呼び出されるまで行われませんでした。</span><span class="sxs-lookup"><span data-stu-id="eea32-230">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="eea32-231">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-231">**New behavior**</span></span>

<span data-ttu-id="eea32-232">3\.0 以降では、EF Core で、トリガー条件が検出されるとすぐに連鎖操作が適用されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-232">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="eea32-233">たとえば、プリンシパル エンティティを削除するために `context.Remove()` を呼び出すと、すべての追跡対象の関連する必要な依存関係もすぐに `Deleted` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-233">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="eea32-234">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-234">**Why**</span></span>

<span data-ttu-id="eea32-235">この変更は、`SaveChanges` が呼び出される_前に_どのエンティティが削除されるかを把握することが重要である、データ バインディングおよび監査シナリオのエクスペリエンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-235">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="eea32-236">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-236">**Mitigations**</span></span>

<span data-ttu-id="eea32-237">`context.ChangedTracker` の設定を使用して、以前の動作を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="eea32-237">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="eea32-238">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-238">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="eea32-239">DeleteBehavior.Restrict のセマンティクスがクリーンになりました</span><span class="sxs-lookup"><span data-stu-id="eea32-239">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="eea32-240">問題 #12661 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-240">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="eea32-241">この変更は、EF Core 3.0 プレビュー 5 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-241">This change is introduced in EF Core 3.0-preview 5.</span></span>

<span data-ttu-id="eea32-242">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-242">**Old behavior**</span></span>

<span data-ttu-id="eea32-243">3\.0 以前は、`DeleteBehavior.Restrict` により `Restrict` セマンティクスでデータベースに外部キーが作成されましたが、内部の修正がどのように変更されたのかがはっきりしませんでした。</span><span class="sxs-lookup"><span data-stu-id="eea32-243">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="eea32-244">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-244">**New behavior**</span></span>

<span data-ttu-id="eea32-245">3\.0 以降では、`DeleteBehavior.Restrict` により `Restrict` セマンティクスで外部キーが作成されます。つまり、カスケードがありません。EF 内部修正には影響を出さず、制約違反で例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="eea32-245">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="eea32-246">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-246">**Why**</span></span>

<span data-ttu-id="eea32-247">この変更は、副作用を出さず、直観的に `DeleteBehavior` を使用するために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-247">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="eea32-248">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-248">**Mitigations**</span></span>

<span data-ttu-id="eea32-249">`DeleteBehavior.ClientNoAction` を使用し、以前の動作を復元できます。</span><span class="sxs-lookup"><span data-stu-id="eea32-249">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

## <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="eea32-250">クエリ型がエンティティ型と統合される</span><span class="sxs-lookup"><span data-stu-id="eea32-250">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="eea32-251">問題 #14194 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-251">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="eea32-252">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-252">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-253">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-253">**Old behavior**</span></span>

<span data-ttu-id="eea32-254">EF Core 3.0 より前では、[クエリ型](xref:core/modeling/query-types)は、構造化された方法で主キーが定義されないデータのクエリを実行するための手段でした。</span><span class="sxs-lookup"><span data-stu-id="eea32-254">Before EF Core 3.0, [query types](xref:core/modeling/query-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="eea32-255">つまり、クエリ型は、キーが利用できるときに (テーブルからの可能性が高くなりますが、ビューからの可能性もあります) 通常のエンティティ型が使用された場合に、キーなしの (ビューからの可能性が高くなりますが、テーブルからの可能性もあります) エンティティ型をマップするために使用されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-255">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="eea32-256">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-256">**New behavior**</span></span>

<span data-ttu-id="eea32-257">クエリ型は、現在、主キーなしの単なるエンティティ型になります。</span><span class="sxs-lookup"><span data-stu-id="eea32-257">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="eea32-258">キーなしのエンティティ型の機能は、以前のバージョンのクエリ型と同じです。</span><span class="sxs-lookup"><span data-stu-id="eea32-258">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="eea32-259">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-259">**Why**</span></span>

<span data-ttu-id="eea32-260">この変更は、クエリ型の目的に関する混乱を減らすために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-260">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="eea32-261">つまり、これらはキーなしのエンティティ型であるため、本質的には読み取り専用となりますが、単にエンティティ型を読み取り専用にする必要があるという理由で使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="eea32-261">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="eea32-262">同様に、これらは多くの場合、ビューにマップされますが、これは、単にビューでは多くの場合、キーが定義されないためです。</span><span class="sxs-lookup"><span data-stu-id="eea32-262">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="eea32-263">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-263">**Mitigations**</span></span>

<span data-ttu-id="eea32-264">API の以下の部分は使用されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-264">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="eea32-265">**`ModelBuilder.Query<>()`** - 代わりに `ModelBuilder.Entity<>().HasNoKey()` を呼び出して、エンティティ型をキーなしとしてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-265">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="eea32-266">これは、規則により、主キーは必要であるが、規則と一致しない場合に構成エラーを回避するようにはまだ構成されません。</span><span class="sxs-lookup"><span data-stu-id="eea32-266">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="eea32-267">**`DbQuery<>`** - 代わりに `DbSet<>` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-267">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="eea32-268">**`DbContext.Query<>()`** - 代わりに `DbContext.Set<>()` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-268">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="eea32-269">所有型のリレーションシップ用の構成 API が変更された</span><span class="sxs-lookup"><span data-stu-id="eea32-269">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="eea32-270">[問題 #12444 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[問題 #9148 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[問題 #14153 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="eea32-270">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="eea32-271">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-271">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-272">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-272">**Old behavior**</span></span>

<span data-ttu-id="eea32-273">EF Core 3.0 より前では、所有リレーションシップの構成は、`OwnsOne` または `OwnsMany` の呼び出し直後に実行されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-273">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="eea32-274">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-274">**New behavior**</span></span>

<span data-ttu-id="eea32-275">EF Core 3.0 以降では、fluent API で、`WithOwner()` を使用して、所有者に対してナビゲーション プロパティが構成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-275">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="eea32-276">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-276">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="eea32-277">現在、所有者と所有型間のリレーションシップに関する構成は、他のリレーションシップの構成方法と同様、`WithOwner()` 後にチェーンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-277">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="eea32-278">一方、所有型自体の構成は引き続き、`OwnsOne()/OwnsMany()` 後にチェーンされます。</span><span class="sxs-lookup"><span data-stu-id="eea32-278">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="eea32-279">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-279">For example:</span></span>

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

<span data-ttu-id="eea32-280">さらに所有型ターゲットでの `Entity()`、`HasOne()`、または `Set()` の呼び出しでは、例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="eea32-280">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="eea32-281">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-281">**Why**</span></span>

<span data-ttu-id="eea32-282">この変更は、所有型自体と、所有型_へのリレーションシップ_の構成を明確に分離するために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-282">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="eea32-283">これにより、`HasForeignKey` のようなメソッドに関するあいまいさと混乱はなくなります。</span><span class="sxs-lookup"><span data-stu-id="eea32-283">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="eea32-284">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-284">**Mitigations**</span></span>

<span data-ttu-id="eea32-285">上記の例で示すように、新しい API サーフェスを使用するように、所有型リレーションシップの構成を変更します。</span><span class="sxs-lookup"><span data-stu-id="eea32-285">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="eea32-286">プリンシパルとテーブルを共有する依存エンティティが省略可能になりました</span><span class="sxs-lookup"><span data-stu-id="eea32-286">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="eea32-287">問題 #9005 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-287">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="eea32-288">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-288">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-289">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-289">**Old behavior**</span></span>

<span data-ttu-id="eea32-290">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="eea32-290">Consider the following model:</span></span>
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
<span data-ttu-id="eea32-291">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、新しい `Order` を追加する場合には常に `OrderDetails` インスタンスが必要でした。</span><span class="sxs-lookup"><span data-stu-id="eea32-291">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="eea32-292">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-292">**New behavior**</span></span>

<span data-ttu-id="eea32-293">EF Core 3.0 以降では、`OrderDetails` なしで `Order` を追加することができ、主キー以外のすべての `OrderDetails` プロパティが NULL 値が許可される列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="eea32-293">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="eea32-294">`OrderDetails` は、必要なプロパティのいずれにも値がない場合、または主キー以外に必要なプロパティがなく、すべてのプロパティが `null` の場合、EF Core のクエリの実行時に `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-294">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="eea32-295">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-295">**Mitigations**</span></span>

<span data-ttu-id="eea32-296">モデルに省略可能なすべての列と依存関係を共有するテーブルがあるが、それを指すナビゲーションが `null` になることが想定されていない場合は、ナビゲーションが `null` の場合のケースを処理するようにアプリケーションを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-296">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="eea32-297">これができない場合は、必要なプロパティをエンティティ型に追加するか、少なくとも 1 つのプロパティに割り当てられている値が `null` 以外である必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-297">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

## <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="eea32-298">同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-298">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="eea32-299">問題 #14154 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-299">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="eea32-300">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-300">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-301">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-301">**Old behavior**</span></span>

<span data-ttu-id="eea32-302">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="eea32-302">Consider the following model:</span></span>
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
<span data-ttu-id="eea32-303">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、`OrderDetails` だけを更新してもクライアント上の `Version` 値は更新されず、次回の更新が失敗します。</span><span class="sxs-lookup"><span data-stu-id="eea32-303">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="eea32-304">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-304">**New behavior**</span></span>

<span data-ttu-id="eea32-305">EF Core 3.0 以降では、新しい `Version` 値が `OrderDetails` を所有している場合には `Order` に伝達されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-305">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="eea32-306">それ以外の場合は、モデルの検証中に例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="eea32-306">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="eea32-307">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-307">**Why**</span></span>

<span data-ttu-id="eea32-308">この変更は、エンティティの 1 つだけが同じテーブルにマップされている場合に、古い同時実行トークン値が更新されるのを回避するために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-308">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="eea32-309">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-309">**Mitigations**</span></span>

<span data-ttu-id="eea32-310">テーブルを共有するすべてのエンティティには、同時実行トークン列にマップされるプロパティを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-310">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="eea32-311">シャドウ状態で作成することができます。</span><span class="sxs-lookup"><span data-stu-id="eea32-311">It's possible the create one in shadow-state:</span></span>
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

## <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="eea32-312">マップされていない型から継承されたプロパティは、すべての派生型の 1 つの列にマップされるようになりました</span><span class="sxs-lookup"><span data-stu-id="eea32-312">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="eea32-313">問題 #13998 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-313">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="eea32-314">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-314">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-315">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-315">**Old behavior**</span></span>

<span data-ttu-id="eea32-316">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="eea32-316">Consider the following model:</span></span>
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

<span data-ttu-id="eea32-317">EF Core 3.0 より前のバージョンでは、`ShippingAddress` プロパティは既定で `BulkOrder` と `Order` の個別の列にマップされていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-317">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="eea32-318">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-318">**New behavior**</span></span>

<span data-ttu-id="eea32-319">EF Core 3.0 以降では、`ShippingAddress` に対して 1 つの列だけが作成されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-319">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="eea32-320">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-320">**Why**</span></span>

<span data-ttu-id="eea32-321">以前の動作は予期しないものでした。</span><span class="sxs-lookup"><span data-stu-id="eea32-321">The old behavoir was unexpected.</span></span>

<span data-ttu-id="eea32-322">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-322">**Mitigations**</span></span>

<span data-ttu-id="eea32-323">プロパティは、派生型の個別の列に引き続き明示的にマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="eea32-323">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="eea32-324">外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった</span><span class="sxs-lookup"><span data-stu-id="eea32-324">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="eea32-325">問題 #13274 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-325">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="eea32-326">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-326">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-327">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-327">**Old behavior**</span></span>

<span data-ttu-id="eea32-328">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="eea32-328">Consider the following model:</span></span>
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
<span data-ttu-id="eea32-329">EF Core 3.0 より前では、規則により、外部キーで `CustomerId` プロパティが使用されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-329">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="eea32-330">しかし、`Order` が所有型である場合、`CustomerId` も主キーとマークされ、これは通常、期待されることではありません。</span><span class="sxs-lookup"><span data-stu-id="eea32-330">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="eea32-331">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-331">**New behavior**</span></span>

<span data-ttu-id="eea32-332">EF Core 3.0 以降では、プリンシパル プロパティと同じ名前である場合、規則により、外部キーに対するプロパティの使用は試行されません。</span><span class="sxs-lookup"><span data-stu-id="eea32-332">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="eea32-333">プリンシパル プロパティ名と連結されたプリンシパル型名、およびプリンシパル プロパティ名パターンと連結されたナビゲーション名は、引き続き一致します。</span><span class="sxs-lookup"><span data-stu-id="eea32-333">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="eea32-334">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-334">For example:</span></span>

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

<span data-ttu-id="eea32-335">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-335">**Why**</span></span>

<span data-ttu-id="eea32-336">この変更は、所有型に主キー プロパティが誤って定義されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-336">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="eea32-337">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-337">**Mitigations**</span></span>

<span data-ttu-id="eea32-338">プロパティを外部キーにする、したがって、主キーの一部にする予定だった場合は、そのように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="eea32-338">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

## <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="eea32-339">データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになりました</span><span class="sxs-lookup"><span data-stu-id="eea32-339">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="eea32-340">問題 #14218 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-340">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="eea32-341">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-341">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-342">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-342">**Old behavior**</span></span>

<span data-ttu-id="eea32-343">EF Core 3.0 より前のバージョンでは、コンテキストにより `TransactionScope` 内部で接続が開かれると、現在の `TransactionScope` がアクティブの間、接続が開いたままになります。</span><span class="sxs-lookup"><span data-stu-id="eea32-343">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

<span data-ttu-id="eea32-344">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-344">**New behavior**</span></span>

<span data-ttu-id="eea32-345">EF Core 3.0 以降では、使用が終了したらすぐに接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="eea32-345">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="eea32-346">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-346">**Why**</span></span>

<span data-ttu-id="eea32-347">この変更により、同じ `TransactionScope` で複数のコンテキストを使用できます。</span><span class="sxs-lookup"><span data-stu-id="eea32-347">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="eea32-348">新しい動作は、EF6 にも一致します。</span><span class="sxs-lookup"><span data-stu-id="eea32-348">The new behavior also matches EF6.</span></span>

<span data-ttu-id="eea32-349">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-349">**Mitigations**</span></span>

<span data-ttu-id="eea32-350">接続を開いたままにする必要がある場合は、`OpenConnection()` を明示的に呼び出して、EF Core が途中で閉じないようにします。</span><span class="sxs-lookup"><span data-stu-id="eea32-350">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="eea32-351">各プロパティで独立したメモリ内整数キー生成が使用される</span><span class="sxs-lookup"><span data-stu-id="eea32-351">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="eea32-352">問題 #6872 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-352">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="eea32-353">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-353">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-354">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-354">**Old behavior**</span></span>

<span data-ttu-id="eea32-355">EF Core 3.0 より前では、1 つの共有値ジェネレーターが、すべてのメモリ内整数キー プロパティで使用されていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-355">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="eea32-356">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-356">**New behavior**</span></span>

<span data-ttu-id="eea32-357">EF Core 3.0 以降では、各整数キー プロパティで、メモリ内データベースを使用するときに独自の値ジェネレーターが取得されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-357">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="eea32-358">また、データベースが削除された場合は、すべてのテーブルに対してキーの生成がリセットされます。</span><span class="sxs-lookup"><span data-stu-id="eea32-358">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="eea32-359">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-359">**Why**</span></span>

<span data-ttu-id="eea32-360">この変更は、実際のデータベース キー生成により近くなるようにメモリ内キー生成を調整するため、また、メモリ内データベースを使用するときに互いのテストを分離させる機能を向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-360">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="eea32-361">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-361">**Mitigations**</span></span>

<span data-ttu-id="eea32-362">これにより、設定される特定のメモリ内キー値に依存しているアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-362">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="eea32-363">代わりに特定のキーの値に依存しないようにするか、あるいは新しい動作と一致するように更新することを検討します。</span><span class="sxs-lookup"><span data-stu-id="eea32-363">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

## <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="eea32-364">バッキング フィールドが既定で使用される</span><span class="sxs-lookup"><span data-stu-id="eea32-364">Backing fields are used by default</span></span>

[<span data-ttu-id="eea32-365">問題 #12430 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-365">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="eea32-366">この変更は、EF Core 3.0 プレビュー 2 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-366">This change is introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="eea32-367">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-367">**Old behavior**</span></span>

<span data-ttu-id="eea32-368">3\.0 より前では、プロパティのバッキング フィールドがわかっていた場合でも、EF Core では引き続き、既定でプロパティの getter および setter メソッドを使用して、プロパティ値の読み取りと書き込みが行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-368">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="eea32-369">ただし、バッキング フィールドが直接設定されることがわかっている場合のクエリの実行は例外です。</span><span class="sxs-lookup"><span data-stu-id="eea32-369">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="eea32-370">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-370">**New behavior**</span></span>

<span data-ttu-id="eea32-371">EF Core 3.0 以降では、プロパティのバッキング フィールドがわかっている場合、バッキング フィールドを使用して、常に EF Core がそのプロパティの読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="eea32-371">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="eea32-372">アプリケーションが getter および setter メソッドにコード化された追加動作に依存している場合、これによりアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-372">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="eea32-373">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-373">**Why**</span></span>

<span data-ttu-id="eea32-374">この変更は、EF Core が、エンティティに関するデータベース操作の実行時に、既定でビジネス ロジックを誤ってトリガーしないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-374">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="eea32-375">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-375">**Mitigations**</span></span>

<span data-ttu-id="eea32-376">3\.0 より前の動作は、`ModelBuilder` のプロパティ アクセス モードの構成を通じて復元できます。</span><span class="sxs-lookup"><span data-stu-id="eea32-376">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="eea32-377">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-377">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="eea32-378">複数の互換性があるバッキング フィールドが見つかった場合にスローされる</span><span class="sxs-lookup"><span data-stu-id="eea32-378">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="eea32-379">問題 #12523 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-379">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="eea32-380">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-380">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-381">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-381">**Old behavior**</span></span>

<span data-ttu-id="eea32-382">EF Core 3.0 より前では、複数のフィールドが、プロパティのバッキング フィールドを見つけるための規則と一致した場合、何らかの優先順位に基づいて、1 つのフィールドが選択されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-382">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="eea32-383">これにより、あいまいなケースで不適切なフィールドが使用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-383">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="eea32-384">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-384">**New behavior**</span></span>

<span data-ttu-id="eea32-385">EF Core 3.0 以降では、複数のフィールドが同じプロパティと一致した場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="eea32-385">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="eea32-386">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-386">**Why**</span></span>

<span data-ttu-id="eea32-387">この変更は、1 つのフィールドのみが適切である可能性がある場合に、他のものより優先して暗黙的に 1 つのフィールドが使用されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-387">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="eea32-388">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-388">**Mitigations**</span></span>

<span data-ttu-id="eea32-389">あいまいなバッキング フィールドがあるプロパティでは、使用するフィールドを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-389">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="eea32-390">たとえば、fluent API を使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="eea32-390">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="eea32-391">フィールド専用プロパティの名前はフィールドの名前に一致する必要があります</span><span class="sxs-lookup"><span data-stu-id="eea32-391">Field-only property names should match the field name</span></span>

<span data-ttu-id="eea32-392">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-392">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-393">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-393">**Old behavior**</span></span>

<span data-ttu-id="eea32-394">EF Core 3.0 以前では、プロパティは文字列値により指定できました。CLR 型でその名前のプロパティが見つからなかった場合、EF Core では、一般的な規則を使ってそれとフィールドの照合が試行されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-394">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the CLR type then EF Core would try to match it to a field using convention rules.</span></span>
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

<span data-ttu-id="eea32-395">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-395">**New behavior**</span></span>

<span data-ttu-id="eea32-396">EF Core 3.0 以降では、フィールド専用プロパティはフィールドの名前に厳密に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-396">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="eea32-397">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-397">**Why**</span></span>

<span data-ttu-id="eea32-398">この変更は、同じような名前が付けられた 2 つのプロパティに同じフィールドが使用されるのを回避する目的で行われました。また、フィールド専用プロパティの照合規則を、CLR プロパティにマッピングされているプロパティの場合と同じにします。</span><span class="sxs-lookup"><span data-stu-id="eea32-398">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="eea32-399">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-399">**Mitigations**</span></span>

<span data-ttu-id="eea32-400">フィールド専用プロパティには、それがマッピングされるフィールドと同じ名前を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-400">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="eea32-401">EF Core 3.0 の今後のプレビューでは、プロパティ名とは異なるフィールド名を明示的に構成できるように戻す予定です。</span><span class="sxs-lookup"><span data-stu-id="eea32-401">In a later preview of EF Core 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

## <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="eea32-402">AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなりました</span><span class="sxs-lookup"><span data-stu-id="eea32-402">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="eea32-403">問題 #14756 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-403">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="eea32-404">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-404">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-405">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-405">**Old behavior**</span></span>

<span data-ttu-id="eea32-406">EF Core 3.0 以前では、`AddDbContext` または `AddDbContextPool` を呼び出すと、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) および [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) への呼び出しを通じて、D.I を使ってログ記録とメモリ キャッシュ サービスも登録されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-406">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with D.I through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="eea32-407">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-407">**New behavior**</span></span>

<span data-ttu-id="eea32-408">EF Core 3.0 以降、`AddDbContext` と `AddDbContextPool` では、依存関係の挿入 (DI) を使ってこれらのサービスを登録しなくなります。</span><span class="sxs-lookup"><span data-stu-id="eea32-408">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="eea32-409">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-409">**Why**</span></span>

<span data-ttu-id="eea32-410">EF Core 3.0 では、これらのサービスをアプリケーションの DI コンテナーに含める必要がありません。</span><span class="sxs-lookup"><span data-stu-id="eea32-410">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="eea32-411">ただし、`ILoggerFactory` がアプリケーションの DI コンテナーに登録された場合、それは引き続き EF Core によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-411">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="eea32-412">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-412">**Mitigations**</span></span>

<span data-ttu-id="eea32-413">ご自身のアプリケーションでこれらのサービスが必要な場合は、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) または [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) を使って、DI コンテナーで明示的にそれらを登録します。</span><span class="sxs-lookup"><span data-stu-id="eea32-413">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="eea32-414">DbContext.Entry でローカルの DetectChanges が実行されるようになった</span><span class="sxs-lookup"><span data-stu-id="eea32-414">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="eea32-415">問題 #13552 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-415">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="eea32-416">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-416">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-417">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-417">**Old behavior**</span></span>

<span data-ttu-id="eea32-418">EF Core 3.0 より前では、`DbContext.Entry` を呼び出すと、追跡対象のすべてのエンティティで変更が検出されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-418">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="eea32-419">これにより、`EntityEntry` で示された状態が確実に最新のものとなりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-419">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="eea32-420">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-420">**New behavior**</span></span>

<span data-ttu-id="eea32-421">EF Core 3.0 以降では、`DbContext.Entry` の呼び出しで、特定のエンティティと、それに関連する追跡対象のプリンシパル エンティティでの変更のみの検出が試行されるようになります。</span><span class="sxs-lookup"><span data-stu-id="eea32-421">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="eea32-422">つまり、他の場所での変更は、このメソッドの呼び出しで検出されなかった可能性があり、アプリケーション状態に影響する場合があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-422">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="eea32-423">`ChangeTracker.AutoDetectChangesEnabled` が `false` に設定されている場合、このローカル変更の検出も無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="eea32-423">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="eea32-424">`ChangeTracker.Entries` や `SaveChanges` など、変更の検出の原因となる他のメソッドは引き続き、すべての追跡対象エンティティの完全な `DetectChanges` の原因となります。</span><span class="sxs-lookup"><span data-stu-id="eea32-424">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="eea32-425">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-425">**Why**</span></span>

<span data-ttu-id="eea32-426">この変更は、`context.Entry` を使用する既定のパフォーマンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-426">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="eea32-427">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-427">**Mitigations**</span></span>

<span data-ttu-id="eea32-428">3\.0 より前の動作を確保するには、`Entry` を呼び出す前に明示的に `ChgangeTracker.DetectChanges()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="eea32-428">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="eea32-429">文字列とバイト配列のキーが既定でクライアントによって生成されない</span><span class="sxs-lookup"><span data-stu-id="eea32-429">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="eea32-430">問題 #14617 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-430">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="eea32-431">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-431">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-432">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-432">**Old behavior**</span></span>

<span data-ttu-id="eea32-433">EF Core 3.0 より前では、`string` と `byte[]` のキー プロパティは、null 以外の値を明示的に設定しなくても使用できました。</span><span class="sxs-lookup"><span data-stu-id="eea32-433">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="eea32-434">このような場合、キーの値は GUID としてクライアントで生成され、`byte[]` のバイトにシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-434">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="eea32-435">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-435">**New behavior**</span></span>

<span data-ttu-id="eea32-436">EF Core 3.0 以降では、キー値が設定されていないことを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="eea32-436">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="eea32-437">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-437">**Why**</span></span>

<span data-ttu-id="eea32-438">この変更は、クライアントで生成された `string`/`byte[]` 値が一般的に役に立たず、既定の動作では一般的な方法で生成されたキー値について判断するのが難しくなったため、行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-438">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="eea32-439">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-439">**Mitigations**</span></span>

<span data-ttu-id="eea32-440">3\.0 より前の動作は、他の null 以外の値が設定されていない場合に、キー プロパティで生成された値を使用するように明示的に指定することで取得できます。</span><span class="sxs-lookup"><span data-stu-id="eea32-440">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="eea32-441">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="eea32-441">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="eea32-442">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="eea32-442">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="eea32-443">ILoggerFactory がスコープ サービスになった</span><span class="sxs-lookup"><span data-stu-id="eea32-443">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="eea32-444">問題 #14698 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-444">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="eea32-445">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-445">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-446">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-446">**Old behavior**</span></span>

<span data-ttu-id="eea32-447">EF Core 3.0 より前では、`ILoggerFactory` はシングルトン サービスとして登録されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-447">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="eea32-448">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-448">**New behavior**</span></span>

<span data-ttu-id="eea32-449">EF Core 3.0 以降では、`ILoggerFactory` がスコープ化されたものとして登録されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-449">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="eea32-450">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-450">**Why**</span></span>

<span data-ttu-id="eea32-451">この変更は、`DbContext` インスタンスでのロガーの関連付けを許可し、他の機能を有効にし、内部サービス プロバイダーの急増などの異常な動作の一部のケースをなくすために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-451">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="eea32-452">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-452">**Mitigations**</span></span>

<span data-ttu-id="eea32-453">この変更は、EF Core の内部サービス プロバイダーでカスタム サービスを登録して使用しない限り、アプリケーション コードに影響しないはずです。</span><span class="sxs-lookup"><span data-stu-id="eea32-453">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="eea32-454">これは一般的なことではありません。</span><span class="sxs-lookup"><span data-stu-id="eea32-454">This isn't common.</span></span>
<span data-ttu-id="eea32-455">このような場合、ほとんどのものが引き続き機能しますが、`ILoggerFactory` に依存していたシングルトン サービスは、異なる方法で `ILoggerFactory` を取得するように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-455">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="eea32-456">このような状況になった場合は、[EF Core GitHub の問題追跡ツール](https://github.com/aspnet/EntityFrameworkCore/issues)で問題を提出し、`ILoggerFactory` の使用方法をお知らせください。これにより、Microsoft では、今後、再びこのように中断しない方法についてよりよく理解できます。</span><span class="sxs-lookup"><span data-stu-id="eea32-456">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a><span data-ttu-id="eea32-457">IDbContextOptionsExtension に IDbContextOptionsExtensionWithDebugInfo が結合される</span><span class="sxs-lookup"><span data-stu-id="eea32-457">IDbContextOptionsExtensionWithDebugInfo merged into IDbContextOptionsExtension</span></span>

[<span data-ttu-id="eea32-458">問題 #13552 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-458">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="eea32-459">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-459">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-460">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-460">**Old behavior**</span></span>

<span data-ttu-id="eea32-461">`IDbContextOptionsExtensionWithDebugInfo` は、2.x リリース サイクル時のインターフェイスへの破壊的変更を回避するために、`IDbContextOptionsExtension` から拡張された追加のオプションのインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="eea32-461">`IDbContextOptionsExtensionWithDebugInfo` was an additional optional interface extended from `IDbContextOptionsExtension` to avoid making a breaking change to the interface during the 2.x release cycle.</span></span>

<span data-ttu-id="eea32-462">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-462">**New behavior**</span></span>

<span data-ttu-id="eea32-463">インターフェイスはまとめて `IDbContextOptionsExtension` に結合されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-463">The interfaces are now merged together into `IDbContextOptionsExtension`.</span></span>

<span data-ttu-id="eea32-464">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-464">**Why**</span></span>

<span data-ttu-id="eea32-465">この変更は、インターフェイスが概念的には 1 つであるため、行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-465">This change was made because the interfaces are conceptually one.</span></span>

<span data-ttu-id="eea32-466">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-466">**Mitigations**</span></span>

<span data-ttu-id="eea32-467">`IDbContextOptionsExtension` のすべての実装を、新しいメンバーをサポートするように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-467">Any implementations of `IDbContextOptionsExtension` will need to be updated to support the new member.</span></span>

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="eea32-468">遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった</span><span class="sxs-lookup"><span data-stu-id="eea32-468">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="eea32-469">問題 #12780 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-469">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="eea32-470">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-470">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-471">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-471">**Old behavior**</span></span>

<span data-ttu-id="eea32-472">EF Core 3.0 より前では、`DbContext` が破棄されると、そのコンテキストから取得されたエンティティの特定のナビゲーション プロパティが完全に読み込まれたかどうかを知る方法はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="eea32-472">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="eea32-473">プロキシでは、代わりに、null 以外の値がある場合は参照ナビゲーションが読み込まれ、空でない場合はコレクション ナビゲーションが読み込まれたと見なされます。</span><span class="sxs-lookup"><span data-stu-id="eea32-473">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="eea32-474">このような場合、遅延読み込みを試みても操作なしとなります。</span><span class="sxs-lookup"><span data-stu-id="eea32-474">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="eea32-475">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-475">**New behavior**</span></span>

<span data-ttu-id="eea32-476">EF Core 3.0 以降では、プロキシで、ナビゲーション プロパティが読み込まれたかどうかの追跡が行われます。</span><span class="sxs-lookup"><span data-stu-id="eea32-476">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="eea32-477">つまり、コンテキストが破棄された後に読み込まれたナビゲーション プロパティにアクセスしようとしても、通常は操作なしとなります。読み込まれたナビゲーションが空でも null であっても同様です。</span><span class="sxs-lookup"><span data-stu-id="eea32-477">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="eea32-478">逆に、コンテキストが破棄された場合、読み込まれなかったナビゲーション プロパティにアクセスしようとすると例外がスローされます。ナビゲーション プロパティが空ではないコレクションである場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="eea32-478">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="eea32-479">このような状況が発生した場合、アプリケーション コードで無効な時間に遅延読み込みの使用が試行されていることを意味し、アプリケーションを変更して、このようにならないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-479">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="eea32-480">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-480">**Why**</span></span>

<span data-ttu-id="eea32-481">この変更は、破棄された `DbContext` インスタンスで遅延読み込みが試行されたときの動作を一貫した正しいものにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-481">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="eea32-482">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-482">**Mitigations**</span></span>

<span data-ttu-id="eea32-483">破棄されたコンテキストで遅延読み込みが試行されないようにアプリケーション コードを更新するか、例外メッセージの説明に従って、これを操作なしとなるように構成します。</span><span class="sxs-lookup"><span data-stu-id="eea32-483">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="eea32-484">内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった</span><span class="sxs-lookup"><span data-stu-id="eea32-484">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="eea32-485">問題 #10236 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-485">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="eea32-486">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-486">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-487">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-487">**Old behavior**</span></span>

<span data-ttu-id="eea32-488">EF Core 3.0 より前では、異常な数の内部サービス プロバイダーを作成するアプリケーションに対する警告がログに記録されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-488">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="eea32-489">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-489">**New behavior**</span></span>

<span data-ttu-id="eea32-490">EF Core 3.0 以降では、この警告はエラーと見なされるようになり、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="eea32-490">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="eea32-491">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-491">**Why**</span></span>

<span data-ttu-id="eea32-492">この変更は、この異常なケースをより明示的に示すことで、アプリケーション コードの改善を促進するために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-492">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="eea32-493">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-493">**Mitigations**</span></span>

<span data-ttu-id="eea32-494">このエラーの発生時の最も適切なアクションは、根本原因を理解し、非常に多くの内部サービス プロバイダーを作成しないようにすることです。</span><span class="sxs-lookup"><span data-stu-id="eea32-494">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="eea32-495">しかし、`DbContextOptionsBuilder` での構成を使用して、エラーを警告に戻す (または無視する) ことがことができます。</span><span class="sxs-lookup"><span data-stu-id="eea32-495">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="eea32-496">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-496">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="eea32-497">1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作</span><span class="sxs-lookup"><span data-stu-id="eea32-497">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="eea32-498">問題 #9171 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-498">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="eea32-499">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-499">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-500">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-500">**Old behavior**</span></span>

<span data-ttu-id="eea32-501">EF Core 3.0 以前では、1 つの文字列と共に `HasOne` または `HasMany` が呼び出されるコードは、わかりにくい方法で解釈されていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-501">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="eea32-502">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-502">For example:</span></span>
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="eea32-503">このコードは、プライベートである可能性がある `Entrance` ナビゲーション プロパティを使って、`Samurai` を他のエンティティ型に関連付けているように見えます。</span><span class="sxs-lookup"><span data-stu-id="eea32-503">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="eea32-504">実際には、このコードは、ナビゲーション プロパティなしで、`Entrance` と呼ばれるエンティティ型に対してリレーションシップを作成しようとしています。</span><span class="sxs-lookup"><span data-stu-id="eea32-504">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="eea32-505">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-505">**New behavior**</span></span>

<span data-ttu-id="eea32-506">EF Core 3.0 以降では、上記のコードは、前にそれが実行すべきであるように見えたことを実行するようになりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-506">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="eea32-507">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-507">**Why**</span></span>

<span data-ttu-id="eea32-508">以前の動作は、特に構成コードを読み取ってエラーを探す場合は、非常にわかりにくいものでした。</span><span class="sxs-lookup"><span data-stu-id="eea32-508">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="eea32-509">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-509">**Mitigations**</span></span>

<span data-ttu-id="eea32-510">これが中断させるのは、型名の文字列を使って明示的にリレーションシップを構成し、かつ明示的にナビゲーション プロパティを指定しないアプリケーションのみです。</span><span class="sxs-lookup"><span data-stu-id="eea32-510">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="eea32-511">これは一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="eea32-511">This is not common.</span></span>
<span data-ttu-id="eea32-512">前の動作は、ナビゲーション プロパティ名に対して明示的に `null` を渡すことで実現できます。</span><span class="sxs-lookup"><span data-stu-id="eea32-512">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="eea32-513">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-513">For example:</span></span>

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

## <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="eea32-514">いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更されました</span><span class="sxs-lookup"><span data-stu-id="eea32-514">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="eea32-515">問題 #15184 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-515">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="eea32-516">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-516">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-517">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-517">**Old behavior**</span></span>

<span data-ttu-id="eea32-518">次の非同期メソッドでは、以前は `Task<T>` が返されていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-518">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="eea32-519">`ValueGenerator.NextValueAsync()` (および派生クラス)</span><span class="sxs-lookup"><span data-stu-id="eea32-519">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="eea32-520">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-520">**New behavior**</span></span>

<span data-ttu-id="eea32-521">前述のメソッドは、以前と同じ `T` に対して `ValueTask<T>` を返すようになりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-521">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="eea32-522">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-522">**Why**</span></span>

<span data-ttu-id="eea32-523">この変更により、これらのメソッドを呼び出すときに発生するヒープ割り当ての数を削減して全般的なパフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="eea32-523">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="eea32-524">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-524">**Mitigations**</span></span>

<span data-ttu-id="eea32-525">上記の API を待機しているだけのアプリケーションのみを再コンパイルする必要があります。ソースの変更は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="eea32-525">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="eea32-526">より複雑な使用方法 (返された `Task` を `Task.WhenAny()` に渡すなど) では通常、返された `ValueTask<T>` を `AsTask()` を呼び出すことによって `Task<T>` に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-526">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="eea32-527">これにより、この変更による割り当ての削減が無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="eea32-527">Note that this negates the allocation reduction that this change brings.</span></span>

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="eea32-528">Relational:TypeMapping 注釈が単に TypeMapping となった</span><span class="sxs-lookup"><span data-stu-id="eea32-528">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="eea32-529">問題 #9913 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-529">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="eea32-530">この変更は、EF Core 3.0 プレビュー 2 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-530">This change is introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="eea32-531">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-531">**Old behavior**</span></span>

<span data-ttu-id="eea32-532">型マッピング注釈の注釈名は "Relational:TypeMapping" でした。</span><span class="sxs-lookup"><span data-stu-id="eea32-532">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="eea32-533">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-533">**New behavior**</span></span>

<span data-ttu-id="eea32-534">型マッピング注釈の注釈名は、"TypeMapping" となりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-534">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="eea32-535">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-535">**Why**</span></span>

<span data-ttu-id="eea32-536">型マッピングが、リレーショナル データベース プロバイダー以外でも使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-536">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="eea32-537">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-537">**Mitigations**</span></span>

<span data-ttu-id="eea32-538">これにより、一般的ではない、注釈として直接型マッピングにアクセスするアプリケーションのみが中断されるようになります。</span><span class="sxs-lookup"><span data-stu-id="eea32-538">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="eea32-539">問題を解決するための最も適切なアクションは、注釈を直接使用するのではなく、API サーフェスを使用して型マッピングにアクセスすることです。</span><span class="sxs-lookup"><span data-stu-id="eea32-539">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

## <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="eea32-540">派生型の ToTable で例外がスローされる</span><span class="sxs-lookup"><span data-stu-id="eea32-540">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="eea32-541">問題 #11811 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-541">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="eea32-542">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-542">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-543">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-543">**Old behavior**</span></span>

<span data-ttu-id="eea32-544">EF Core 3.0 より前では、唯一の継承マッピング方法が TPH であり、これが有効でない場合には、派生型で呼び出された `ToTable()` が無視されていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-544">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="eea32-545">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-545">**New behavior**</span></span>

<span data-ttu-id="eea32-546">EF Core 3.0 以降、および今後のリリースでの TPT と TPC のサポートの追加準備中には、派生型で呼び出された `ToTable()` で、今後の予期されないマッピング変更を回避するために例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="eea32-546">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="eea32-547">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-547">**Why**</span></span>

<span data-ttu-id="eea32-548">現在、派生型を別のテーブルにマップすることは有効ではありません。</span><span class="sxs-lookup"><span data-stu-id="eea32-548">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="eea32-549">この変更により、今後、これを行うことが有効になった場合に中断されなくなります。</span><span class="sxs-lookup"><span data-stu-id="eea32-549">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="eea32-550">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-550">**Mitigations**</span></span>

<span data-ttu-id="eea32-551">派生型を他のテーブルにマップしないようにします。</span><span class="sxs-lookup"><span data-stu-id="eea32-551">Remove any attempts to map derived types to other tables.</span></span>

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="eea32-552">ForSqlServerHasIndex が HasIndex に置き換えられた</span><span class="sxs-lookup"><span data-stu-id="eea32-552">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="eea32-553">問題 #12366 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-553">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="eea32-554">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-554">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-555">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-555">**Old behavior**</span></span>

<span data-ttu-id="eea32-556">EF Core 3.0 より前では、`ForSqlServerHasIndex().ForSqlServerInclude()` によって、`INCLUDE` で使用される列を構成する方法が提供されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-556">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="eea32-557">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-557">**New behavior**</span></span>

<span data-ttu-id="eea32-558">EF Core 3.0 以降では、インデックスでの `Include` の使用が、リレーショナル レベルでサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-558">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="eea32-559">`HasIndex().ForSqlServerInclude()` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="eea32-559">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="eea32-560">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-560">**Why**</span></span>

<span data-ttu-id="eea32-561">この変更は、すべてのデータ プロバイダーのために `Include` でインデックス用の API を 1 か所に統合するために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-561">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="eea32-562">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-562">**Mitigations**</span></span>

<span data-ttu-id="eea32-563">上記のように、新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="eea32-563">Use the new API, as shown above.</span></span>

## <a name="metadata-api-changes"></a><span data-ttu-id="eea32-564">メタデータ API の変更点</span><span class="sxs-lookup"><span data-stu-id="eea32-564">Metadata API changes</span></span>

[<span data-ttu-id="eea32-565">問題 #214 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-565">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="eea32-566">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-566">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-567">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-567">**New behavior**</span></span>

<span data-ttu-id="eea32-568">次のプロパティは拡張メソッドに変換されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-568">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="eea32-569">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-569">**Why**</span></span>

<span data-ttu-id="eea32-570">この変更により、前述のインターフェイスの実装が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="eea32-570">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="eea32-571">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-571">**Mitigations**</span></span>

<span data-ttu-id="eea32-572">新しい拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="eea32-572">Use the new extension methods.</span></span>

## <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="eea32-573">プロバイダー固有のメタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="eea32-573">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="eea32-574">問題 #214 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-574">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="eea32-575">この変更は、EF Core 3.0 プレビュー 6 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-575">This change is introduced in EF Core 3.0-preview 6.</span></span>

<span data-ttu-id="eea32-576">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-576">**New behavior**</span></span>

<span data-ttu-id="eea32-577">プロバイダー固有の拡張メソッドがフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-577">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.GetSqlServerIsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.ForSqlServerUseIdentityColumn()`

<span data-ttu-id="eea32-578">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-578">**Why**</span></span>

<span data-ttu-id="eea32-579">この変更により、前述の拡張メソッドの実装が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="eea32-579">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="eea32-580">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-580">**Mitigations**</span></span>

<span data-ttu-id="eea32-581">新しい拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="eea32-581">Use the new extension methods.</span></span>

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="eea32-582">EF Core で SQLite FK を適用するためのプラグマが送信されなくなった</span><span class="sxs-lookup"><span data-stu-id="eea32-582">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="eea32-583">問題 #12151 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-583">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="eea32-584">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-584">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="eea32-585">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-585">**Old behavior**</span></span>

<span data-ttu-id="eea32-586">EF Core 3.0 より前では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-586">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="eea32-587">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-587">**New behavior**</span></span>

<span data-ttu-id="eea32-588">EF Core 3.0 以降では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-588">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="eea32-589">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-589">**Why**</span></span>

<span data-ttu-id="eea32-590">この変更は、既定で EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用され、その後、FK の適用が既定で有効になり、接続が開かれるたびに明示的に有効にする必要がないため、行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-590">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="eea32-591">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-591">**Mitigations**</span></span>

<span data-ttu-id="eea32-592">外部キーは、EF Core に対して既定で使用される、SQLitePCLRaw.bundle_e_sqlite3 で既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="eea32-592">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="eea32-593">それ以外の場合は、接続文字列で `Foreign Keys=True` を指定することで、外部キーを有効にできます。</span><span class="sxs-lookup"><span data-stu-id="eea32-593">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a><span data-ttu-id="eea32-594">Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった</span><span class="sxs-lookup"><span data-stu-id="eea32-594">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="eea32-595">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-595">**Old behavior**</span></span>

<span data-ttu-id="eea32-596">EF Core 3.0 より前では、EF Core では `SQLitePCLRaw.bundle_green` が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-596">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="eea32-597">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-597">**New behavior**</span></span>

<span data-ttu-id="eea32-598">EF Core 3.0 以降では、EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-598">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="eea32-599">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-599">**Why**</span></span>

<span data-ttu-id="eea32-600">この変更は、iOS 上で使用される SQLite のバージョンを、他のプラットフォームと一致させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="eea32-600">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="eea32-601">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-601">**Mitigations**</span></span>

<span data-ttu-id="eea32-602">iOS でネイティブの SQLite バージョンを使用するには、別の `SQLitePCLRaw` バンドルを使用するように `Microsoft.Data.Sqlite` を構成します。</span><span class="sxs-lookup"><span data-stu-id="eea32-602">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

## <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="eea32-603">GUID の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="eea32-603">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="eea32-604">問題 #15078 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-604">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="eea32-605">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-605">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-606">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-606">**Old behavior**</span></span>

<span data-ttu-id="eea32-607">GUID の値は、以前は SQLite に BLOB 値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-607">Guid values were previously sored as BLOB values on SQLite.</span></span>

<span data-ttu-id="eea32-608">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-608">**New behavior**</span></span>

<span data-ttu-id="eea32-609">GUID の値はテキストとして格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-609">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="eea32-610">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-610">**Why**</span></span>

<span data-ttu-id="eea32-611">GUID のバイナリ形式は標準化されていません。</span><span class="sxs-lookup"><span data-stu-id="eea32-611">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="eea32-612">値をテキストとして格納する方が、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="eea32-612">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="eea32-613">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-613">**Mitigations**</span></span>

<span data-ttu-id="eea32-614">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="eea32-614">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

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

<span data-ttu-id="eea32-615">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="eea32-615">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="eea32-616">Microsoft.Data.Sqlite は引き続き、BLOB とテキストの両方の列から GUID 値を読み取ることができます。ただし、パラメーターと定数の既定の形式が変更されているため、GUID が含まれる多くのシナリオではアクションが必要になります。</span><span class="sxs-lookup"><span data-stu-id="eea32-616">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

## <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="eea32-617">Char の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="eea32-617">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="eea32-618">問題 #15020 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-618">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="eea32-619">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-619">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-620">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-620">**Old behavior**</span></span>

<span data-ttu-id="eea32-621">Char の値は、以前は SQLite に整数値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-621">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="eea32-622">たとえば、Char の値 *A* は整数値 65 として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-622">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="eea32-623">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-623">**New behavior**</span></span>

<span data-ttu-id="eea32-624">Char の値はテキストとして格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-624">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="eea32-625">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-625">**Why**</span></span>

<span data-ttu-id="eea32-626">値をテキストとして格納する方が自然であり、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="eea32-626">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="eea32-627">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-627">**Mitigations**</span></span>

<span data-ttu-id="eea32-628">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="eea32-628">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="eea32-629">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="eea32-629">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="eea32-630">Microsoft.Data.Sqlite では、引き続き整数とテキストの両方の列の文字列値を読み取ることができるため、一部のシナリオではアクションが必要ありません。</span><span class="sxs-lookup"><span data-stu-id="eea32-630">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

## <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="eea32-631">移行 ID がインバリアント カルチャの暦を使用して生成されるようになった</span><span class="sxs-lookup"><span data-stu-id="eea32-631">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="eea32-632">問題 #12978 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-632">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="eea32-633">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-633">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-634">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-634">**Old behavior**</span></span>

<span data-ttu-id="eea32-635">移行 ID は、誤って現在のカルチャの暦を使用して生成されていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-635">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="eea32-636">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-636">**New behavior**</span></span>

<span data-ttu-id="eea32-637">移行 ID がインバリアント カルチャの暦 (グレゴリオ暦) を使用して常に生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-637">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="eea32-638">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-638">**Why**</span></span>

<span data-ttu-id="eea32-639">データベースを更新するときやマージの競合を解決するときに、移行の順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="eea32-639">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="eea32-640">インバリアントの暦を使用することで、順序の問題が回避され、システムの予定表がチームのメンバーごとに違うことがなくなります。</span><span class="sxs-lookup"><span data-stu-id="eea32-640">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="eea32-641">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-641">**Mitigations**</span></span>

<span data-ttu-id="eea32-642">この変更は、1 年がグレゴリオ暦より長くなるグレゴリオ暦以外の暦 (タイ仏暦など) を使用しているすべてのユーザーに影響します。</span><span class="sxs-lookup"><span data-stu-id="eea32-642">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="eea32-643">既存の移行後に新しい移行順序が設定されるように、既存の移行 ID を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-643">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="eea32-644">移行 ID は、移行のデザイナー ファイルの移行属性にあります。</span><span class="sxs-lookup"><span data-stu-id="eea32-644">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="eea32-645">移行履歴テーブルも更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="eea32-645">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

## <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="eea32-646">LogQueryPossibleExceptionWithAggregateOperator の名前が変更された</span><span class="sxs-lookup"><span data-stu-id="eea32-646">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="eea32-647">問題 #10985 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-647">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="eea32-648">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-648">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-649">**変更**</span><span class="sxs-lookup"><span data-stu-id="eea32-649">**Change**</span></span>

<span data-ttu-id="eea32-650">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` の名前が `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="eea32-650">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="eea32-651">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-651">**Why**</span></span>

<span data-ttu-id="eea32-652">この警告イベントの名前が他のすべての警告イベントと照合されるためです。</span><span class="sxs-lookup"><span data-stu-id="eea32-652">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="eea32-653">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-653">**Mitigations**</span></span>

<span data-ttu-id="eea32-654">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="eea32-654">Use the new name.</span></span> <span data-ttu-id="eea32-655">(イベント ID 番号が変更されていないことを確認してください。)</span><span class="sxs-lookup"><span data-stu-id="eea32-655">(Note that the event ID number has not changed.)</span></span>

## <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="eea32-656">外部キー制約名の API が明確化された</span><span class="sxs-lookup"><span data-stu-id="eea32-656">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="eea32-657">問題 #10730 の追跡</span><span class="sxs-lookup"><span data-stu-id="eea32-657">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="eea32-658">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="eea32-658">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="eea32-659">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-659">**Old behavior**</span></span>

<span data-ttu-id="eea32-660">EF Core 3.0 以前は、外部キー制約名が単に "名前" と呼ばれていました。</span><span class="sxs-lookup"><span data-stu-id="eea32-660">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="eea32-661">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-661">For example:</span></span>

```C#
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="eea32-662">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="eea32-662">**New behavior**</span></span>

<span data-ttu-id="eea32-663">EF Core 3.0 以降は、外部キー制約名が "制約名" と呼ばれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-663">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="eea32-664">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="eea32-664">For example:</span></span>

```C#
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="eea32-665">**理由**</span><span class="sxs-lookup"><span data-stu-id="eea32-665">**Why**</span></span>

<span data-ttu-id="eea32-666">この変更により、この領域の名前付けに一貫性がもたらされます。また、それが外部キーが定義されている列やプロパティの名前ではなく、外部キー制約の名前であることが明確になりました。</span><span class="sxs-lookup"><span data-stu-id="eea32-666">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="eea32-667">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="eea32-667">**Mitigations**</span></span>

<span data-ttu-id="eea32-668">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="eea32-668">Use the new name.</span></span>
