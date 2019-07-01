---
title: EF Core 3.0 での破壊的変更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: 96586808862c4373168dcd34a5f00c9f2f7563c3
ms.sourcegitcommit: 9bd64a1a71b7f7aeb044aeecc7c4785b57db1ec9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394829"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="513af-102">EF Core 3.0 (現在プレビュー段階) に含まれる破壊的変更</span><span class="sxs-lookup"><span data-stu-id="513af-102">Breaking changes included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="513af-103">機能セットと今後のリリースのスケジュールは、常に変更される可能性があることに注意してください。また、このページを最新の状態に保持するようにしていますが、最新のプランが反映されていない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="513af-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="513af-104">以下の API と動作変更により、3.0.0 へのアップグレード時に、EF Core 2.2.x 用に開発されたアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="513af-104">The following API and behavior changes have the potential to break applications developed for EF Core 2.2.x when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="513af-105">データベース プロバイダーにのみ影響することが予想される変更については、[プロバイダーの変更](../../providers/provider-log.md)に関するページに記載されています。</span><span class="sxs-lookup"><span data-stu-id="513af-105">Changes that we expect to only impact database providers are documented under [provider changes](../../providers/provider-log.md).</span></span>
<span data-ttu-id="513af-106">ある 3.0 プレビューから別の 3.0 プレビューに導入される新機能での中断については、ここには記載されていません。</span><span class="sxs-lookup"><span data-stu-id="513af-106">Breaks in new features introduced from one 3.0 preview to another 3.0 preview aren't documented here.</span></span>

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="513af-107">LINQ クエリがクライアントで評価されなくなった</span><span class="sxs-lookup"><span data-stu-id="513af-107">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="513af-108">[問題 #14935 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[問題 #12795 も参照](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="513af-108">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="513af-109">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-109">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-110">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-110">**Old behavior**</span></span>

<span data-ttu-id="513af-111">3\.0 より前では、EF Core では、SQL またはパラメーターに対するクエリの一部だった式を変換できない場合、クライアントで自動的に式が評価されていました。</span><span class="sxs-lookup"><span data-stu-id="513af-111">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="513af-112">既定では、コストの高い式のクライアント評価でのみ警告がトリガーされました。</span><span class="sxs-lookup"><span data-stu-id="513af-112">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="513af-113">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-113">**New behavior**</span></span>

<span data-ttu-id="513af-114">3\.0 以降では、クライアントで評価される最上位のプロジェクション (クエリの最後の `Select()` 呼び出し) の式のみが EF Core で許可されます。</span><span class="sxs-lookup"><span data-stu-id="513af-114">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="513af-115">クエリの他の部分の式を SQL やパラメーターに変換できない場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="513af-115">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="513af-116">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-116">**Why**</span></span>

<span data-ttu-id="513af-117">クエリの自動クライアント評価では、多くのクエリを実行できます。これらの重要な部分を変換できない場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="513af-117">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="513af-118">この動作が原因で、予期しない損害を与える動作となる可能性があります。この動作は、運用環境でのみ見られる場合があります。</span><span class="sxs-lookup"><span data-stu-id="513af-118">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="513af-119">たとえば、変換できない `Where()` 呼び出しの条件が原因で、テーブルのすべての行がデータベース サーバーから転送できるようになり、クライアントでフィルターを適用できるようになる場合があります。</span><span class="sxs-lookup"><span data-stu-id="513af-119">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="513af-120">このような状況は、開発時にテーブルにわずかな行が含まれている場合は、簡単に検出される可能性があります。しかし、テーブルに数百万もの行が含まれる、運用環境にアプリケーションを移行する場合は難しくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="513af-120">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="513af-121">クライアント評価の警告では、開発中にあまりに簡単に無視されることも示されていました。</span><span class="sxs-lookup"><span data-stu-id="513af-121">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="513af-122">これだけでなく、自動クライアント評価は、特定の式のクエリ変換の改善が、リリース間の予期しない破壊的変更の原因となる問題につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="513af-122">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="513af-123">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-123">**Mitigations**</span></span>

<span data-ttu-id="513af-124">クエリを完全に変換できない場合は、変換できる形式でクエリを書き直すか、`AsEnumerable()`、`ToList()`、または同様のものを使用して、LINQ-to-Objects を使ってさらに処理できるクライアントに明示的にデータを戻します。</span><span class="sxs-lookup"><span data-stu-id="513af-124">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="513af-125">Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="513af-125">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="513af-126">問題 Announcements#325 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-126">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="513af-127">この変更は、ASP.NET Core 3.0 プレビュー 1 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-127">This change is introduced in ASP.NET Core 3.0-preview 1.</span></span> 

<span data-ttu-id="513af-128">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-128">**Old behavior**</span></span>

<span data-ttu-id="513af-129">ASP.NET Core 3.0 より前では、パッケージ参照を `Microsoft.AspNetCore.App` または `Microsoft.AspNetCore.All` に追加したときに、EF Core と、SQL Server プロバイダーのような EF Core データ プロバイダーの一部が含まれました。</span><span class="sxs-lookup"><span data-stu-id="513af-129">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="513af-130">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-130">**New behavior**</span></span>

<span data-ttu-id="513af-131">3\.0 以降では、ASP.NET Core 共有フレームワークには、EF Core も、いずれの EF Core データ プロバイダーも含まれません。</span><span class="sxs-lookup"><span data-stu-id="513af-131">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="513af-132">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-132">**Why**</span></span>

<span data-ttu-id="513af-133">この変更の前では、アプリケーションのターゲットが ASP.NET Core であるか SQL Server であるかに応じて、EF Core を取得するには異なる手順が必要でした。</span><span class="sxs-lookup"><span data-stu-id="513af-133">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="513af-134">また、ASP.NET Core のアップグレードでは、必ずしも適切だとは言えない、EF Core と SQL Server プロバイダーのアップグレードが強制されました。</span><span class="sxs-lookup"><span data-stu-id="513af-134">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="513af-135">この変更では、EF Core の取得のエクスペリエンスは、.NET の実装とアプリケーションの種類がサポートされる、すべてのプロバイダーで同じになります。</span><span class="sxs-lookup"><span data-stu-id="513af-135">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="513af-136">また、開発者は、EF Core と EF Core データ プロバイダーのアップグレードのタイミングを正確に制御できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="513af-136">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="513af-137">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-137">**Mitigations**</span></span>

<span data-ttu-id="513af-138">ASP.NET Core 3.0 アプリケーションまたはその他のサポートされるアプリケーションで EF Core を使用するには、アプリケーションで使用される EF Core データベース プロバイダーにパッケージ参照を明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="513af-138">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

## <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="513af-139">EF Core のコマンドライン ツールである dotnet ef が .NET Core SDK の一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="513af-139">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="513af-140">問題 #14016 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-140">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="513af-141">この変更は、EF Core 3.0 プレビュー 4 および .NET Core SDK の対応するバージョンで導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-141">This change is introduced in EF Core 3.0-preview 4 and the corresponding version of the .NET Core SDK.</span></span>

<span data-ttu-id="513af-142">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-142">**Old behavior**</span></span>

<span data-ttu-id="513af-143">3\.0 より前は `dotnet ef` ツールが .NET Core SDK に含まれており、追加の手順を必要とせずに、任意のプロジェクトのコマンド ラインから簡単に使用できました。</span><span class="sxs-lookup"><span data-stu-id="513af-143">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="513af-144">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-144">**New behavior**</span></span>

<span data-ttu-id="513af-145">3\.0 以降は .NET SDK に `dotnet ef` ツールが含まれないため、これを使用するにはローカルまたはグローバルなツールとして明示的にインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-145">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="513af-146">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-146">**Why**</span></span>

<span data-ttu-id="513af-147">この変更により、`dotnet ef` を通常の .NET CLI ツールとして NuGet 上で配信したり更新したりできるようになります。これは EF Core 3.0 も常に NuGet パッケージとして配信されるという事実と一致します。</span><span class="sxs-lookup"><span data-stu-id="513af-147">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="513af-148">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-148">**Mitigations**</span></span>

<span data-ttu-id="513af-149">移行の管理や `DbContext` のスキャフォールディングを行うには、`dotnet-ef` を `dotnet tool install` コマンドを使用してインストールします。</span><span class="sxs-lookup"><span data-stu-id="513af-149">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` using the `dotnet tool install` command.</span></span>
<span data-ttu-id="513af-150">たとえば、グローバルなツールとしてインストールするには、次のコマンドを入力します。</span><span class="sxs-lookup"><span data-stu-id="513af-150">For example, to install it as a global tool, you can type this command:</span></span>

  ``` console
  $ dotnet tool install --global dotnet-ef --version <exact-version>
  ```

<span data-ttu-id="513af-151">[ツール マニフェスト ファイル](https://github.com/dotnet/cli/issues/10288)を使用してツールの依存関係として宣言するプロジェクトの依存関係を復元するときに、ローカルなツールとして取得することもできます。</span><span class="sxs-lookup"><span data-stu-id="513af-151">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](https://github.com/dotnet/cli/issues/10288).</span></span>

## <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="513af-152">FromSql、ExecuteSql、および ExecuteSqlAsync の名前変更</span><span class="sxs-lookup"><span data-stu-id="513af-152">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="513af-153">問題 #10996 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-153">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="513af-154">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-154">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-155">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-155">**Old behavior**</span></span>

<span data-ttu-id="513af-156">EF Core 3.0 より前のバージョンでは、通常の文字列または SQL およびパラメーターに挿入する必要がある文字列で使用するために、これらのメソッド名がオーバーロードされました。</span><span class="sxs-lookup"><span data-stu-id="513af-156">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="513af-157">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-157">**New behavior**</span></span>

<span data-ttu-id="513af-158">EF Core 3.0 以降では、`FromSqlRaw`、`ExecuteSqlRaw`、および `ExecuteSqlRawAsync` を使用して、パラメーターがクエリ文字列とは別に渡される、パラメーター化クエリが作成されます。</span><span class="sxs-lookup"><span data-stu-id="513af-158">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="513af-159">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="513af-159">For example:</span></span>

```C#
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="513af-160">`FromSqlInterpolated`、`ExecuteSqlInterpolated`、および `ExecuteSqlInterpolatedAsync` を使用して、パラメーターが挿入クエリ文字列の一部として渡されるパラメーター化クエリを作成します。</span><span class="sxs-lookup"><span data-stu-id="513af-160">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="513af-161">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="513af-161">For example:</span></span>

```C#
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="513af-162">上記のクエリはどちらも、同じ SQL パラメーターを持つ同じパラメーター化 SQL が生成されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="513af-162">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="513af-163">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-163">**Why**</span></span>

<span data-ttu-id="513af-164">このようなメソッド オーバーロードは、挿入文字列メソッドを呼び出すつもりが、誤って raw 文字列メソッドを非常に簡単に呼び出せてしまいます。その逆も同様です。</span><span class="sxs-lookup"><span data-stu-id="513af-164">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="513af-165">これは、クエリをパラメーター化する必要があるときに、パラメーター化されない結果になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="513af-165">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="513af-166">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-166">**Mitigations**</span></span>

<span data-ttu-id="513af-167">新しいメソッド名を使用するように切り替えます。</span><span class="sxs-lookup"><span data-stu-id="513af-167">Switch to use the new method names.</span></span>

## <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="513af-168">FromSql メソッドはクエリのルートでのみ指定できる</span><span class="sxs-lookup"><span data-stu-id="513af-168">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="513af-169">問題 #15704 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-169">Tracking Issue #15704</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

<span data-ttu-id="513af-170">この変更は、EF Core 3.0 プレビュー 6 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-170">This change is introduced in EF Core 3.0-preview 6.</span></span>

<span data-ttu-id="513af-171">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-171">**Old behavior**</span></span>

<span data-ttu-id="513af-172">EF Core 3.0 以前は、クエリ内の任意の場所で `FromSql` メソッドを指定できました。</span><span class="sxs-lookup"><span data-stu-id="513af-172">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="513af-173">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-173">**New behavior**</span></span>

<span data-ttu-id="513af-174">EF Core 3.0 以降、新しい `FromSqlRaw` および `FromSqlInterpolated` メソッド (`FromSql` の置き換え) は、クエリのルート上でのみ (つまり `DbSet<>` で直接) 指定できます。</span><span class="sxs-lookup"><span data-stu-id="513af-174">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="513af-175">他の場所でそれらを指定しようとすると、コンパイル エラーが発生します。</span><span class="sxs-lookup"><span data-stu-id="513af-175">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="513af-176">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-176">**Why**</span></span>

<span data-ttu-id="513af-177">`DbSet` 以外の任意の場所で `FromSql` を指定しても、さらなる意味や価値が追加されることはなく、特定のシナリオではあいまいさの原因となる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="513af-177">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="513af-178">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-178">**Mitigations**</span></span>

<span data-ttu-id="513af-179">`FromSql` の呼び出し場所を移動して、それらが適用される `DbSet` 上で直接実行されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-179">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

## <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="513af-180">~~クエリの実行がデバッグ レベルでログに記録される~~ 元に戻されます</span><span class="sxs-lookup"><span data-stu-id="513af-180">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="513af-181">問題 #14523 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-181">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="513af-182">この変更は、EF Core 3.0 プレビュー 7 で元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="513af-182">This change is reverted in EF Core 3.0-preview 7.</span></span>

<span data-ttu-id="513af-183">EF Core 3.0 の新しい構成では、あらゆるイベントのログ レベルをアプリケーションによって指定できるため、この変更は元に戻されます。</span><span class="sxs-lookup"><span data-stu-id="513af-183">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="513af-184">たとえば、SQL のログ記録を `Debug` に切り替えるには、`OnConfiguring` または `AddDbContext` で明示的にレベルを構成します。</span><span class="sxs-lookup"><span data-stu-id="513af-184">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="513af-185">一時キーの値がエンティティ インスタンスに設定されなくなった</span><span class="sxs-lookup"><span data-stu-id="513af-185">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="513af-186">問題 #12378 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-186">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="513af-187">この変更は、EF Core 3.0 プレビュー 2 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-187">This change is introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="513af-188">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-188">**Old behavior**</span></span>

<span data-ttu-id="513af-189">EF Core 3.0 より前では、後で実際の値がデータベースによって生成される、すべてのキー プロパティに一時的な値が割り当てられていました。</span><span class="sxs-lookup"><span data-stu-id="513af-189">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="513af-190">通常、これらの一時的な値は大きい負の数値でした。</span><span class="sxs-lookup"><span data-stu-id="513af-190">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="513af-191">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-191">**New behavior**</span></span>

<span data-ttu-id="513af-192">3\.0 以降では、EF Core で、エンティティの追跡情報の一部として一時キーの値が格納され、キー プロパティ自体はそのままになります。</span><span class="sxs-lookup"><span data-stu-id="513af-192">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="513af-193">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-193">**Why**</span></span>

<span data-ttu-id="513af-194">この変更は、何らかの `DbContext` インスタンスによって以前に追跡されたエンティティが、別の `DbContext` インスタンスに移動されるときに、一時キーの値が誤って永続的なものにならないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-194">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="513af-195">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-195">**Mitigations**</span></span>

<span data-ttu-id="513af-196">主キーの値を外部キーに割り当てて、エンティティ間の関連付けを形成するアプリケーションは、主キーがストアで生成されており、`Added` 状態のエンティティに属している場合、以前の動作に依存する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="513af-196">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="513af-197">これは、次のようにして回避できます。</span><span class="sxs-lookup"><span data-stu-id="513af-197">This can be avoided by:</span></span>
* <span data-ttu-id="513af-198">ストア生成キーを使用しない。</span><span class="sxs-lookup"><span data-stu-id="513af-198">Not using store-generated keys.</span></span>
* <span data-ttu-id="513af-199">外部キーの値を設定するのではなく、ナビゲーション プロパティを設定してリレーションシップを形成する。</span><span class="sxs-lookup"><span data-stu-id="513af-199">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="513af-200">エンティティの追跡情報から実際の一時キーの値を取得する。</span><span class="sxs-lookup"><span data-stu-id="513af-200">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="513af-201">たとえば、`context.Entry(blog).Property(e => e.Id).CurrentValue` では、`blog.Id` 自体が設定されていない場合でも、一時的な値が返されます。</span><span class="sxs-lookup"><span data-stu-id="513af-201">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

## <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="513af-202">DetectChanges でストア生成キーの値が優先される</span><span class="sxs-lookup"><span data-stu-id="513af-202">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="513af-203">問題 #14616 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-203">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="513af-204">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-204">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="513af-205">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-205">**Old behavior**</span></span>

<span data-ttu-id="513af-206">EF Core 3.0 より前では、`DetectChanges` によって検出された追跡対象外のエンティティが `Added` 状態で追跡され、`SaveChanges` が呼び出されたときに新しい行として挿入されました。</span><span class="sxs-lookup"><span data-stu-id="513af-206">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="513af-207">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-207">**New behavior**</span></span>

<span data-ttu-id="513af-208">EF Core 3.0 以降では、エンティティで生成されたキーの値を使用し、何らかのキーの値が設定されている場合、そのエンティティは `Modified` 状態で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="513af-208">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="513af-209">つまり、エンティティの行が存在していると見なされ、`SaveChanges` の呼び出し時に更新されます。</span><span class="sxs-lookup"><span data-stu-id="513af-209">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="513af-210">キーの値が設定されていないか、エンティティ型で生成されたキーが使用されない場合、新しいエンティティは引き続き、以前のバージョンと同様に `Added` として追跡されます。</span><span class="sxs-lookup"><span data-stu-id="513af-210">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="513af-211">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-211">**Why**</span></span>

<span data-ttu-id="513af-212">この変更は、ストア生成キーの使用中に、切り離されたエンティティ グラフをより簡単に一貫して操作できるようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-212">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="513af-213">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-213">**Mitigations**</span></span>

<span data-ttu-id="513af-214">エンティティ型が生成されたキーを使用するように構成されているものの、キーの値が新しいインスタンスに対して明示的に設定されている場合、この変更によってアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="513af-214">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="513af-215">この問題を解決するには、キー プロパティで生成された値が使用されないように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="513af-215">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="513af-216">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="513af-216">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="513af-217">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="513af-217">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="513af-218">既定で連鎖削除がすぐに行われるようになった</span><span class="sxs-lookup"><span data-stu-id="513af-218">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="513af-219">問題 #10114 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-219">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="513af-220">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-220">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="513af-221">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-221">**Old behavior**</span></span>

<span data-ttu-id="513af-222">3\.0 より前では、EF Core で適用された連鎖操作 (必要なプリンシパルが削除されたか、必要なプリンシパルへのリレーションシップが切断されたときに依存エンティティを削除する) は、SaveChanges が呼び出されるまで行われませんでした。</span><span class="sxs-lookup"><span data-stu-id="513af-222">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="513af-223">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-223">**New behavior**</span></span>

<span data-ttu-id="513af-224">3\.0 以降では、EF Core で、トリガー条件が検出されるとすぐに連鎖操作が適用されます。</span><span class="sxs-lookup"><span data-stu-id="513af-224">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="513af-225">たとえば、プリンシパル エンティティを削除するために `context.Remove()` を呼び出すと、すべての追跡対象の関連する必要な依存関係もすぐに `Deleted` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="513af-225">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="513af-226">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-226">**Why**</span></span>

<span data-ttu-id="513af-227">この変更は、`SaveChanges` が呼び出される_前に_どのエンティティが削除されるかを把握することが重要である、データ バインディングおよび監査シナリオのエクスペリエンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-227">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="513af-228">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-228">**Mitigations**</span></span>

<span data-ttu-id="513af-229">`context.ChangedTracker` の設定を使用して、以前の動作を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="513af-229">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="513af-230">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="513af-230">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="513af-231">DeleteBehavior.Restrict のセマンティクスがクリーンになりました</span><span class="sxs-lookup"><span data-stu-id="513af-231">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="513af-232">問題 #12661 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-232">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="513af-233">この変更は、EF Core 3.0 プレビュー 5 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-233">This change is introduced in EF Core 3.0-preview 5.</span></span>

<span data-ttu-id="513af-234">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-234">**Old behavior**</span></span>

<span data-ttu-id="513af-235">3\.0 以前は、`DeleteBehavior.Restrict` により `Restrict` セマンティクスでデータベースに外部キーが作成されましたが、内部の修正がどのように変更されたのかがはっきりしませんでした。</span><span class="sxs-lookup"><span data-stu-id="513af-235">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="513af-236">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-236">**New behavior**</span></span>

<span data-ttu-id="513af-237">3\.0 以降では、`DeleteBehavior.Restrict` により `Restrict` セマンティクスで外部キーが作成されます。つまり、カスケードがありません。EF 内部修正には影響を出さず、制約違反で例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="513af-237">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="513af-238">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-238">**Why**</span></span>

<span data-ttu-id="513af-239">この変更は、副作用を出さず、直観的に `DeleteBehavior` を使用するために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-239">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="513af-240">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-240">**Mitigations**</span></span>

<span data-ttu-id="513af-241">`DeleteBehavior.ClientNoAction` を使用し、以前の動作を復元できます。</span><span class="sxs-lookup"><span data-stu-id="513af-241">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

## <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="513af-242">クエリ型がエンティティ型と統合される</span><span class="sxs-lookup"><span data-stu-id="513af-242">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="513af-243">問題 #14194 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-243">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="513af-244">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-244">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="513af-245">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-245">**Old behavior**</span></span>

<span data-ttu-id="513af-246">EF Core 3.0 より前では、[クエリ型](xref:core/modeling/query-types)は、構造化された方法で主キーが定義されないデータのクエリを実行するための手段でした。</span><span class="sxs-lookup"><span data-stu-id="513af-246">Before EF Core 3.0, [query types](xref:core/modeling/query-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="513af-247">つまり、クエリ型は、キーが利用できるときに (テーブルからの可能性が高くなりますが、ビューからの可能性もあります) 通常のエンティティ型が使用された場合に、キーなしの (ビューからの可能性が高くなりますが、テーブルからの可能性もあります) エンティティ型をマップするために使用されました。</span><span class="sxs-lookup"><span data-stu-id="513af-247">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="513af-248">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-248">**New behavior**</span></span>

<span data-ttu-id="513af-249">クエリ型は、現在、主キーなしの単なるエンティティ型になります。</span><span class="sxs-lookup"><span data-stu-id="513af-249">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="513af-250">キーなしのエンティティ型の機能は、以前のバージョンのクエリ型と同じです。</span><span class="sxs-lookup"><span data-stu-id="513af-250">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="513af-251">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-251">**Why**</span></span>

<span data-ttu-id="513af-252">この変更は、クエリ型の目的に関する混乱を減らすために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-252">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="513af-253">つまり、これらはキーなしのエンティティ型であるため、本質的には読み取り専用となりますが、単にエンティティ型を読み取り専用にする必要があるという理由で使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="513af-253">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="513af-254">同様に、これらは多くの場合、ビューにマップされますが、これは、単にビューでは多くの場合、キーが定義されないためです。</span><span class="sxs-lookup"><span data-stu-id="513af-254">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="513af-255">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-255">**Mitigations**</span></span>

<span data-ttu-id="513af-256">API の以下の部分は使用されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="513af-256">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="513af-257">**`ModelBuilder.Query<>()`** - 代わりに `ModelBuilder.Entity<>().HasNoKey()` を呼び出して、エンティティ型をキーなしとしてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-257">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="513af-258">これは、規則により、主キーは必要であるが、規則と一致しない場合に構成エラーを回避するようにはまだ構成されません。</span><span class="sxs-lookup"><span data-stu-id="513af-258">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="513af-259">**`DbQuery<>`** - 代わりに `DbSet<>` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-259">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="513af-260">**`DbContext.Query<>()`** - 代わりに `DbContext.Set<>()` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-260">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="513af-261">所有型のリレーションシップ用の構成 API が変更された</span><span class="sxs-lookup"><span data-stu-id="513af-261">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="513af-262">[問題 #12444 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[問題 #9148 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[問題 #14153 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="513af-262">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="513af-263">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-263">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="513af-264">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-264">**Old behavior**</span></span>

<span data-ttu-id="513af-265">EF Core 3.0 より前では、所有リレーションシップの構成は、`OwnsOne` または `OwnsMany` の呼び出し直後に実行されました。</span><span class="sxs-lookup"><span data-stu-id="513af-265">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="513af-266">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-266">**New behavior**</span></span>

<span data-ttu-id="513af-267">EF Core 3.0 以降では、fluent API で、`WithOwner()` を使用して、所有者に対してナビゲーション プロパティが構成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="513af-267">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="513af-268">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="513af-268">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="513af-269">現在、所有者と所有型間のリレーションシップに関する構成は、他のリレーションシップの構成方法と同様、`WithOwner()` 後にチェーンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-269">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="513af-270">一方、所有型自体の構成は引き続き、`OwnsOne()/OwnsMany()` 後にチェーンされます。</span><span class="sxs-lookup"><span data-stu-id="513af-270">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="513af-271">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="513af-271">For example:</span></span>

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

<span data-ttu-id="513af-272">さらに所有型ターゲットでの `Entity()`、`HasOne()`、または `Set()` の呼び出しでは、例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="513af-272">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="513af-273">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-273">**Why**</span></span>

<span data-ttu-id="513af-274">この変更は、所有型自体と、所有型_へのリレーションシップ_の構成を明確に分離するために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-274">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="513af-275">これにより、`HasForeignKey` のようなメソッドに関するあいまいさと混乱はなくなります。</span><span class="sxs-lookup"><span data-stu-id="513af-275">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="513af-276">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-276">**Mitigations**</span></span>

<span data-ttu-id="513af-277">上記の例で示すように、新しい API サーフェスを使用するように、所有型リレーションシップの構成を変更します。</span><span class="sxs-lookup"><span data-stu-id="513af-277">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="513af-278">プリンシパルとテーブルを共有する依存エンティティが省略可能になりました</span><span class="sxs-lookup"><span data-stu-id="513af-278">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="513af-279">問題 #9005 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-279">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="513af-280">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-280">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-281">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-281">**Old behavior**</span></span>

<span data-ttu-id="513af-282">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="513af-282">Consider the following model:</span></span>
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
<span data-ttu-id="513af-283">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、新しい `Order` を追加する場合には常に `OrderDetails` インスタンスが必要でした。</span><span class="sxs-lookup"><span data-stu-id="513af-283">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="513af-284">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-284">**New behavior**</span></span>

<span data-ttu-id="513af-285">EF Core 3.0 以降では、`OrderDetails` なしで `Order` を追加することができ、主キー以外のすべての `OrderDetails` プロパティが NULL 値が許可される列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="513af-285">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="513af-286">`OrderDetails` は、必要なプロパティのいずれにも値がない場合、または主キー以外に必要なプロパティがなく、すべてのプロパティが `null` の場合、EF Core のクエリの実行時に `null` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="513af-286">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="513af-287">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-287">**Mitigations**</span></span>

<span data-ttu-id="513af-288">モデルに省略可能なすべての列と依存関係を共有するテーブルがあるが、それを指すナビゲーションが `null` になることが想定されていない場合は、ナビゲーションが `null` の場合のケースを処理するようにアプリケーションを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-288">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="513af-289">これができない場合は、必要なプロパティをエンティティ型に追加するか、少なくとも 1 つのプロパティに割り当てられている値が `null` 以外である必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-289">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

## <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="513af-290">同時実行トークン列とテーブルを共有するすべてのエンティティをプロパティにマップする必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-290">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="513af-291">問題 #14154 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-291">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="513af-292">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-292">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-293">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-293">**Old behavior**</span></span>

<span data-ttu-id="513af-294">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="513af-294">Consider the following model:</span></span>
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
<span data-ttu-id="513af-295">EF Core 3.0 より前のバージョンでは、`OrderDetails` が `Order` によって所有されている場合、または同じテーブルに明示的にマップされている場合、`OrderDetails` だけを更新してもクライアント上の `Version` 値は更新されず、次回の更新が失敗します。</span><span class="sxs-lookup"><span data-stu-id="513af-295">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="513af-296">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-296">**New behavior**</span></span>

<span data-ttu-id="513af-297">EF Core 3.0 以降では、新しい `Version` 値が `OrderDetails` を所有している場合には `Order` に伝達されます。</span><span class="sxs-lookup"><span data-stu-id="513af-297">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="513af-298">それ以外の場合は、モデルの検証中に例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="513af-298">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="513af-299">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-299">**Why**</span></span>

<span data-ttu-id="513af-300">この変更は、エンティティの 1 つだけが同じテーブルにマップされている場合に、古い同時実行トークン値が更新されるのを回避するために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-300">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="513af-301">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-301">**Mitigations**</span></span>

<span data-ttu-id="513af-302">テーブルを共有するすべてのエンティティには、同時実行トークン列にマップされるプロパティを含める必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-302">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="513af-303">シャドウ状態で作成することができます。</span><span class="sxs-lookup"><span data-stu-id="513af-303">It's possible the create one in shadow-state:</span></span>
```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

## <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="513af-304">マップされていない型から継承されたプロパティは、すべての派生型の 1 つの列にマップされるようになりました</span><span class="sxs-lookup"><span data-stu-id="513af-304">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="513af-305">問題 #13998 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-305">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="513af-306">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-306">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-307">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-307">**Old behavior**</span></span>

<span data-ttu-id="513af-308">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="513af-308">Consider the following model:</span></span>
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

<span data-ttu-id="513af-309">EF Core 3.0 より前のバージョンでは、`ShippingAddress` プロパティは既定で `BulkOrder` と `Order` の個別の列にマップされていました。</span><span class="sxs-lookup"><span data-stu-id="513af-309">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="513af-310">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-310">**New behavior**</span></span>

<span data-ttu-id="513af-311">EF Core 3.0 以降では、`ShippingAddress` に対して 1 つの列だけが作成されます。</span><span class="sxs-lookup"><span data-stu-id="513af-311">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="513af-312">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-312">**Why**</span></span>

<span data-ttu-id="513af-313">以前の動作は予期しないものでした。</span><span class="sxs-lookup"><span data-stu-id="513af-313">The old behavoir was unexpected.</span></span>

<span data-ttu-id="513af-314">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-314">**Mitigations**</span></span>

<span data-ttu-id="513af-315">プロパティは、派生型の個別の列に引き続き明示的にマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="513af-315">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="513af-316">外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった</span><span class="sxs-lookup"><span data-stu-id="513af-316">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="513af-317">問題 #13274 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-317">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="513af-318">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-318">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="513af-319">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-319">**Old behavior**</span></span>

<span data-ttu-id="513af-320">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="513af-320">Consider the following model:</span></span>
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
<span data-ttu-id="513af-321">EF Core 3.0 より前では、規則により、外部キーで `CustomerId` プロパティが使用されました。</span><span class="sxs-lookup"><span data-stu-id="513af-321">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="513af-322">しかし、`Order` が所有型である場合、`CustomerId` も主キーとマークされ、これは通常、期待されることではありません。</span><span class="sxs-lookup"><span data-stu-id="513af-322">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="513af-323">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-323">**New behavior**</span></span>

<span data-ttu-id="513af-324">EF Core 3.0 以降では、プリンシパル プロパティと同じ名前である場合、規則により、外部キーに対するプロパティの使用は試行されません。</span><span class="sxs-lookup"><span data-stu-id="513af-324">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="513af-325">プリンシパル プロパティ名と連結されたプリンシパル型名、およびプリンシパル プロパティ名パターンと連結されたナビゲーション名は、引き続き一致します。</span><span class="sxs-lookup"><span data-stu-id="513af-325">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="513af-326">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="513af-326">For example:</span></span>

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

<span data-ttu-id="513af-327">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-327">**Why**</span></span>

<span data-ttu-id="513af-328">この変更は、所有型に主キー プロパティが誤って定義されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-328">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="513af-329">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-329">**Mitigations**</span></span>

<span data-ttu-id="513af-330">プロパティを外部キーにする、したがって、主キーの一部にする予定だった場合は、そのように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="513af-330">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

## <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="513af-331">データベース接続は、これ以上使用されない場合、TransactionScope が完了する前に閉じられるようになりました</span><span class="sxs-lookup"><span data-stu-id="513af-331">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="513af-332">問題 #14218 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-332">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="513af-333">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-333">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-334">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-334">**Old behavior**</span></span>

<span data-ttu-id="513af-335">EF Core 3.0 より前のバージョンでは、コンテキストにより `TransactionScope` 内部で接続が開かれると、現在の `TransactionScope` がアクティブの間、接続が開いたままになります。</span><span class="sxs-lookup"><span data-stu-id="513af-335">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

<span data-ttu-id="513af-336">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-336">**New behavior**</span></span>

<span data-ttu-id="513af-337">EF Core 3.0 以降では、使用が終了したらすぐに接続が閉じられます。</span><span class="sxs-lookup"><span data-stu-id="513af-337">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="513af-338">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-338">**Why**</span></span>

<span data-ttu-id="513af-339">この変更により、同じ `TransactionScope` で複数のコンテキストを使用できます。</span><span class="sxs-lookup"><span data-stu-id="513af-339">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="513af-340">新しい動作は、EF6 にも一致します。</span><span class="sxs-lookup"><span data-stu-id="513af-340">The new behavior also matches EF6.</span></span>

<span data-ttu-id="513af-341">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-341">**Mitigations**</span></span>

<span data-ttu-id="513af-342">接続を開いたままにする必要がある場合は、`OpenConnection()` を明示的に呼び出して、EF Core が途中で閉じないようにします。</span><span class="sxs-lookup"><span data-stu-id="513af-342">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="513af-343">各プロパティで独立したメモリ内整数キー生成が使用される</span><span class="sxs-lookup"><span data-stu-id="513af-343">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="513af-344">問題 #6872 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-344">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="513af-345">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-345">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-346">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-346">**Old behavior**</span></span>

<span data-ttu-id="513af-347">EF Core 3.0 より前では、1 つの共有値ジェネレーターが、すべてのメモリ内整数キー プロパティで使用されていました。</span><span class="sxs-lookup"><span data-stu-id="513af-347">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="513af-348">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-348">**New behavior**</span></span>

<span data-ttu-id="513af-349">EF Core 3.0 以降では、各整数キー プロパティで、メモリ内データベースを使用するときに独自の値ジェネレーターが取得されます。</span><span class="sxs-lookup"><span data-stu-id="513af-349">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="513af-350">また、データベースが削除された場合は、すべてのテーブルに対してキーの生成がリセットされます。</span><span class="sxs-lookup"><span data-stu-id="513af-350">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="513af-351">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-351">**Why**</span></span>

<span data-ttu-id="513af-352">この変更は、実際のデータベース キー生成により近くなるようにメモリ内キー生成を調整するため、また、メモリ内データベースを使用するときに互いのテストを分離させる機能を向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-352">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="513af-353">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-353">**Mitigations**</span></span>

<span data-ttu-id="513af-354">これにより、設定される特定のメモリ内キー値に依存しているアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="513af-354">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="513af-355">代わりに特定のキーの値に依存しないようにするか、あるいは新しい動作と一致するように更新することを検討します。</span><span class="sxs-lookup"><span data-stu-id="513af-355">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

## <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="513af-356">バッキング フィールドが既定で使用される</span><span class="sxs-lookup"><span data-stu-id="513af-356">Backing fields are used by default</span></span>

[<span data-ttu-id="513af-357">問題 #12430 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-357">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="513af-358">この変更は、EF Core 3.0 プレビュー 2 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-358">This change is introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="513af-359">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-359">**Old behavior**</span></span>

<span data-ttu-id="513af-360">3\.0 より前では、プロパティのバッキング フィールドがわかっていた場合でも、EF Core では引き続き、既定でプロパティの getter および setter メソッドを使用して、プロパティ値の読み取りと書き込みが行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-360">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="513af-361">ただし、バッキング フィールドが直接設定されることがわかっている場合のクエリの実行は例外です。</span><span class="sxs-lookup"><span data-stu-id="513af-361">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="513af-362">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-362">**New behavior**</span></span>

<span data-ttu-id="513af-363">EF Core 3.0 以降では、プロパティのバッキング フィールドがわかっている場合、バッキング フィールドを使用して、常に EF Core がそのプロパティの読み取りと書き込みを行います。</span><span class="sxs-lookup"><span data-stu-id="513af-363">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="513af-364">アプリケーションが getter および setter メソッドにコード化された追加動作に依存している場合、これによりアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="513af-364">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="513af-365">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-365">**Why**</span></span>

<span data-ttu-id="513af-366">この変更は、EF Core が、エンティティに関するデータベース操作の実行時に、既定でビジネス ロジックを誤ってトリガーしないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-366">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="513af-367">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-367">**Mitigations**</span></span>

<span data-ttu-id="513af-368">3\.0 より前の動作は、`ModelBuilder` のプロパティ アクセス モードの構成を通じて復元できます。</span><span class="sxs-lookup"><span data-stu-id="513af-368">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="513af-369">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="513af-369">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="513af-370">複数の互換性があるバッキング フィールドが見つかった場合にスローされる</span><span class="sxs-lookup"><span data-stu-id="513af-370">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="513af-371">問題 #12523 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-371">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="513af-372">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-372">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-373">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-373">**Old behavior**</span></span>

<span data-ttu-id="513af-374">EF Core 3.0 より前では、複数のフィールドが、プロパティのバッキング フィールドを見つけるための規則と一致した場合、何らかの優先順位に基づいて、1 つのフィールドが選択されました。</span><span class="sxs-lookup"><span data-stu-id="513af-374">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="513af-375">これにより、あいまいなケースで不適切なフィールドが使用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="513af-375">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="513af-376">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-376">**New behavior**</span></span>

<span data-ttu-id="513af-377">EF Core 3.0 以降では、複数のフィールドが同じプロパティと一致した場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="513af-377">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="513af-378">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-378">**Why**</span></span>

<span data-ttu-id="513af-379">この変更は、1 つのフィールドのみが適切である可能性がある場合に、他のものより優先して暗黙的に 1 つのフィールドが使用されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-379">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="513af-380">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-380">**Mitigations**</span></span>

<span data-ttu-id="513af-381">あいまいなバッキング フィールドがあるプロパティでは、使用するフィールドを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-381">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="513af-382">たとえば、fluent API を使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="513af-382">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="513af-383">フィールド専用プロパティの名前はフィールドの名前に一致する必要があります</span><span class="sxs-lookup"><span data-stu-id="513af-383">Field-only property names should match the field name</span></span>

<span data-ttu-id="513af-384">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-384">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-385">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-385">**Old behavior**</span></span>

<span data-ttu-id="513af-386">EF Core 3.0 以前では、プロパティは文字列値により指定できました。CLR 型でその名前のプロパティが見つからなかった場合、EF Core では、一般的な規則を使ってそれとフィールドの照合が試行されました。</span><span class="sxs-lookup"><span data-stu-id="513af-386">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the CLR type then EF Core would try to match it to a field using convention rules.</span></span>
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

<span data-ttu-id="513af-387">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-387">**New behavior**</span></span>

<span data-ttu-id="513af-388">EF Core 3.0 以降では、フィールド専用プロパティはフィールドの名前に厳密に一致する必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-388">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="513af-389">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-389">**Why**</span></span>

<span data-ttu-id="513af-390">この変更は、同じような名前が付けられた 2 つのプロパティに同じフィールドが使用されるのを回避する目的で行われました。また、フィールド専用プロパティの照合規則を、CLR プロパティにマッピングされているプロパティの場合と同じにします。</span><span class="sxs-lookup"><span data-stu-id="513af-390">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="513af-391">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-391">**Mitigations**</span></span>

<span data-ttu-id="513af-392">フィールド専用プロパティには、それがマッピングされるフィールドと同じ名前を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-392">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="513af-393">EF Core 3.0 の今後のプレビューでは、プロパティ名とは異なるフィールド名を明示的に構成できるように戻す予定です。</span><span class="sxs-lookup"><span data-stu-id="513af-393">In a later preview of EF Core 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

## <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="513af-394">AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなりました</span><span class="sxs-lookup"><span data-stu-id="513af-394">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="513af-395">問題 #14756 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-395">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="513af-396">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-396">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-397">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-397">**Old behavior**</span></span>

<span data-ttu-id="513af-398">EF Core 3.0 以前では、`AddDbContext` または `AddDbContextPool` を呼び出すと、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) および [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) への呼び出しを通じて、D.I を使ってログ記録とメモリ キャッシュ サービスも登録されました。</span><span class="sxs-lookup"><span data-stu-id="513af-398">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with D.I through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="513af-399">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-399">**New behavior**</span></span>

<span data-ttu-id="513af-400">EF Core 3.0 以降、`AddDbContext` と `AddDbContextPool` では、依存関係の挿入 (DI) を使ってこれらのサービスを登録しなくなります。</span><span class="sxs-lookup"><span data-stu-id="513af-400">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="513af-401">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-401">**Why**</span></span>

<span data-ttu-id="513af-402">EF Core 3.0 では、これらのサービスをアプリケーションの DI コンテナーに含める必要がありません。</span><span class="sxs-lookup"><span data-stu-id="513af-402">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="513af-403">ただし、`ILoggerFactory` がアプリケーションの DI コンテナーに登録された場合、それは引き続き EF Core によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="513af-403">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="513af-404">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-404">**Mitigations**</span></span>

<span data-ttu-id="513af-405">ご自身のアプリケーションでこれらのサービスが必要な場合は、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) または [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) を使って、DI コンテナーで明示的にそれらを登録します。</span><span class="sxs-lookup"><span data-stu-id="513af-405">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="513af-406">DbContext.Entry でローカルの DetectChanges が実行されるようになった</span><span class="sxs-lookup"><span data-stu-id="513af-406">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="513af-407">問題 #13552 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-407">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="513af-408">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-408">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="513af-409">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-409">**Old behavior**</span></span>

<span data-ttu-id="513af-410">EF Core 3.0 より前では、`DbContext.Entry` を呼び出すと、追跡対象のすべてのエンティティで変更が検出されました。</span><span class="sxs-lookup"><span data-stu-id="513af-410">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="513af-411">これにより、`EntityEntry` で示された状態が確実に最新のものとなりました。</span><span class="sxs-lookup"><span data-stu-id="513af-411">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="513af-412">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-412">**New behavior**</span></span>

<span data-ttu-id="513af-413">EF Core 3.0 以降では、`DbContext.Entry` の呼び出しで、特定のエンティティと、それに関連する追跡対象のプリンシパル エンティティでの変更のみの検出が試行されるようになります。</span><span class="sxs-lookup"><span data-stu-id="513af-413">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="513af-414">つまり、他の場所での変更は、このメソッドの呼び出しで検出されなかった可能性があり、アプリケーション状態に影響する場合があります。</span><span class="sxs-lookup"><span data-stu-id="513af-414">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="513af-415">`ChangeTracker.AutoDetectChangesEnabled` が `false` に設定されている場合、このローカル変更の検出も無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="513af-415">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="513af-416">`ChangeTracker.Entries` や `SaveChanges` など、変更の検出の原因となる他のメソッドは引き続き、すべての追跡対象エンティティの完全な `DetectChanges` の原因となります。</span><span class="sxs-lookup"><span data-stu-id="513af-416">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="513af-417">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-417">**Why**</span></span>

<span data-ttu-id="513af-418">この変更は、`context.Entry` を使用する既定のパフォーマンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-418">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="513af-419">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-419">**Mitigations**</span></span>

<span data-ttu-id="513af-420">3\.0 より前の動作を確保するには、`Entry` を呼び出す前に明示的に `ChgangeTracker.DetectChanges()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="513af-420">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="513af-421">文字列とバイト配列のキーが既定でクライアントによって生成されない</span><span class="sxs-lookup"><span data-stu-id="513af-421">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="513af-422">問題 #14617 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-422">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="513af-423">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-423">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-424">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-424">**Old behavior**</span></span>

<span data-ttu-id="513af-425">EF Core 3.0 より前では、`string` と `byte[]` のキー プロパティは、null 以外の値を明示的に設定しなくても使用できました。</span><span class="sxs-lookup"><span data-stu-id="513af-425">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="513af-426">このような場合、キーの値は GUID としてクライアントで生成され、`byte[]` のバイトにシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="513af-426">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="513af-427">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-427">**New behavior**</span></span>

<span data-ttu-id="513af-428">EF Core 3.0 以降では、キー値が設定されていないことを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="513af-428">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="513af-429">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-429">**Why**</span></span>

<span data-ttu-id="513af-430">この変更は、クライアントで生成された `string`/`byte[]` 値が一般的に役に立たず、既定の動作では一般的な方法で生成されたキー値について判断するのが難しくなったため、行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-430">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="513af-431">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-431">**Mitigations**</span></span>

<span data-ttu-id="513af-432">3\.0 より前の動作は、他の null 以外の値が設定されていない場合に、キー プロパティで生成された値を使用するように明示的に指定することで取得できます。</span><span class="sxs-lookup"><span data-stu-id="513af-432">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="513af-433">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="513af-433">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="513af-434">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="513af-434">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="513af-435">ILoggerFactory がスコープ サービスになった</span><span class="sxs-lookup"><span data-stu-id="513af-435">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="513af-436">問題 #14698 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-436">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="513af-437">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-437">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="513af-438">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-438">**Old behavior**</span></span>

<span data-ttu-id="513af-439">EF Core 3.0 より前では、`ILoggerFactory` はシングルトン サービスとして登録されました。</span><span class="sxs-lookup"><span data-stu-id="513af-439">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="513af-440">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-440">**New behavior**</span></span>

<span data-ttu-id="513af-441">EF Core 3.0 以降では、`ILoggerFactory` がスコープ化されたものとして登録されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="513af-441">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="513af-442">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-442">**Why**</span></span>

<span data-ttu-id="513af-443">この変更は、`DbContext` インスタンスでのロガーの関連付けを許可し、他の機能を有効にし、内部サービス プロバイダーの急増などの異常な動作の一部のケースをなくすために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-443">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="513af-444">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-444">**Mitigations**</span></span>

<span data-ttu-id="513af-445">この変更は、EF Core の内部サービス プロバイダーでカスタム サービスを登録して使用しない限り、アプリケーション コードに影響しないはずです。</span><span class="sxs-lookup"><span data-stu-id="513af-445">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="513af-446">これは一般的なことではありません。</span><span class="sxs-lookup"><span data-stu-id="513af-446">This isn't common.</span></span>
<span data-ttu-id="513af-447">このような場合、ほとんどのものが引き続き機能しますが、`ILoggerFactory` に依存していたシングルトン サービスは、異なる方法で `ILoggerFactory` を取得するように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-447">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="513af-448">このような状況になった場合は、[EF Core GitHub の問題追跡ツール](https://github.com/aspnet/EntityFrameworkCore/issues)で問題を提出し、`ILoggerFactory` の使用方法をお知らせください。これにより、Microsoft では、今後、再びこのように中断しない方法についてよりよく理解できます。</span><span class="sxs-lookup"><span data-stu-id="513af-448">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="513af-449">遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった</span><span class="sxs-lookup"><span data-stu-id="513af-449">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="513af-450">問題 #12780 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-450">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="513af-451">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-451">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-452">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-452">**Old behavior**</span></span>

<span data-ttu-id="513af-453">EF Core 3.0 より前では、`DbContext` が破棄されると、そのコンテキストから取得されたエンティティの特定のナビゲーション プロパティが完全に読み込まれたかどうかを知る方法はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="513af-453">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="513af-454">プロキシでは、代わりに、null 以外の値がある場合は参照ナビゲーションが読み込まれ、空でない場合はコレクション ナビゲーションが読み込まれたと見なされます。</span><span class="sxs-lookup"><span data-stu-id="513af-454">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="513af-455">このような場合、遅延読み込みを試みても操作なしとなります。</span><span class="sxs-lookup"><span data-stu-id="513af-455">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="513af-456">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-456">**New behavior**</span></span>

<span data-ttu-id="513af-457">EF Core 3.0 以降では、プロキシで、ナビゲーション プロパティが読み込まれたかどうかの追跡が行われます。</span><span class="sxs-lookup"><span data-stu-id="513af-457">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="513af-458">つまり、コンテキストが破棄された後に読み込まれたナビゲーション プロパティにアクセスしようとしても、通常は操作なしとなります。読み込まれたナビゲーションが空でも null であっても同様です。</span><span class="sxs-lookup"><span data-stu-id="513af-458">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="513af-459">逆に、コンテキストが破棄された場合、読み込まれなかったナビゲーション プロパティにアクセスしようとすると例外がスローされます。ナビゲーション プロパティが空ではないコレクションである場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="513af-459">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="513af-460">このような状況が発生した場合、アプリケーション コードで無効な時間に遅延読み込みの使用が試行されていることを意味し、アプリケーションを変更して、このようにならないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-460">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="513af-461">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-461">**Why**</span></span>

<span data-ttu-id="513af-462">この変更は、破棄された `DbContext` インスタンスで遅延読み込みが試行されたときの動作を一貫した正しいものにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-462">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="513af-463">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-463">**Mitigations**</span></span>

<span data-ttu-id="513af-464">破棄されたコンテキストで遅延読み込みが試行されないようにアプリケーション コードを更新するか、例外メッセージの説明に従って、これを操作なしとなるように構成します。</span><span class="sxs-lookup"><span data-stu-id="513af-464">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="513af-465">内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった</span><span class="sxs-lookup"><span data-stu-id="513af-465">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="513af-466">問題 #10236 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-466">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="513af-467">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-467">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="513af-468">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-468">**Old behavior**</span></span>

<span data-ttu-id="513af-469">EF Core 3.0 より前では、異常な数の内部サービス プロバイダーを作成するアプリケーションに対する警告がログに記録されました。</span><span class="sxs-lookup"><span data-stu-id="513af-469">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="513af-470">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-470">**New behavior**</span></span>

<span data-ttu-id="513af-471">EF Core 3.0 以降では、この警告はエラーと見なされるようになり、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="513af-471">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="513af-472">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-472">**Why**</span></span>

<span data-ttu-id="513af-473">この変更は、この異常なケースをより明示的に示すことで、アプリケーション コードの改善を促進するために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-473">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="513af-474">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-474">**Mitigations**</span></span>

<span data-ttu-id="513af-475">このエラーの発生時の最も適切なアクションは、根本原因を理解し、非常に多くの内部サービス プロバイダーを作成しないようにすることです。</span><span class="sxs-lookup"><span data-stu-id="513af-475">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="513af-476">しかし、`DbContextOptionsBuilder` での構成を使用して、エラーを警告に戻す (または無視する) ことがことができます。</span><span class="sxs-lookup"><span data-stu-id="513af-476">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="513af-477">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="513af-477">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="513af-478">1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作</span><span class="sxs-lookup"><span data-stu-id="513af-478">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="513af-479">問題 #9171 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-479">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="513af-480">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-480">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-481">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-481">**Old behavior**</span></span>

<span data-ttu-id="513af-482">EF Core 3.0 以前では、1 つの文字列と共に `HasOne` または `HasMany` が呼び出されるコードは、わかりにくい方法で解釈されていました。</span><span class="sxs-lookup"><span data-stu-id="513af-482">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="513af-483">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="513af-483">For example:</span></span>
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="513af-484">このコードは、プライベートである可能性がある `Entrance` ナビゲーション プロパティを使って、`Samurai` を他のエンティティ型に関連付けているように見えます。</span><span class="sxs-lookup"><span data-stu-id="513af-484">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="513af-485">実際には、このコードは、ナビゲーション プロパティなしで、`Entrance` と呼ばれるエンティティ型に対してリレーションシップを作成しようとしています。</span><span class="sxs-lookup"><span data-stu-id="513af-485">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="513af-486">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-486">**New behavior**</span></span>

<span data-ttu-id="513af-487">EF Core 3.0 以降では、上記のコードは、前にそれが実行すべきであるように見えたことを実行するようになりました。</span><span class="sxs-lookup"><span data-stu-id="513af-487">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="513af-488">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-488">**Why**</span></span>

<span data-ttu-id="513af-489">以前の動作は、特に構成コードを読み取ってエラーを探す場合は、非常にわかりにくいものでした。</span><span class="sxs-lookup"><span data-stu-id="513af-489">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="513af-490">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-490">**Mitigations**</span></span>

<span data-ttu-id="513af-491">これが中断させるのは、型名の文字列を使って明示的にリレーションシップを構成し、かつ明示的にナビゲーション プロパティを指定しないアプリケーションのみです。</span><span class="sxs-lookup"><span data-stu-id="513af-491">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="513af-492">これは一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="513af-492">This is not common.</span></span>
<span data-ttu-id="513af-493">前の動作は、ナビゲーション プロパティ名に対して明示的に `null` を渡すことで実現できます。</span><span class="sxs-lookup"><span data-stu-id="513af-493">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="513af-494">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="513af-494">For example:</span></span>

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

## <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="513af-495">いくつかの非同期メソッドの戻り値の型が Task から ValueTask に変更されました</span><span class="sxs-lookup"><span data-stu-id="513af-495">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="513af-496">問題 #15184 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-496">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="513af-497">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-497">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-498">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-498">**Old behavior**</span></span>

<span data-ttu-id="513af-499">次の非同期メソッドでは、以前は `Task<T>` が返されていました。</span><span class="sxs-lookup"><span data-stu-id="513af-499">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="513af-500">`ValueGenerator.NextValueAsync()` (および派生クラス)</span><span class="sxs-lookup"><span data-stu-id="513af-500">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="513af-501">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-501">**New behavior**</span></span>

<span data-ttu-id="513af-502">前述のメソッドは、以前と同じ `T` に対して `ValueTask<T>` を返すようになりました。</span><span class="sxs-lookup"><span data-stu-id="513af-502">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="513af-503">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-503">**Why**</span></span>

<span data-ttu-id="513af-504">この変更により、これらのメソッドを呼び出すときに発生するヒープ割り当ての数を削減して全般的なパフォーマンスを向上させます。</span><span class="sxs-lookup"><span data-stu-id="513af-504">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="513af-505">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-505">**Mitigations**</span></span>

<span data-ttu-id="513af-506">上記の API を待機しているだけのアプリケーションのみを再コンパイルする必要があります。ソースの変更は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="513af-506">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="513af-507">より複雑な使用方法 (返された `Task` を `Task.WhenAny()` に渡すなど) では通常、返された `ValueTask<T>` を `AsTask()` を呼び出すことによって `Task<T>` に変換する必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-507">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="513af-508">これにより、この変更による割り当ての削減が無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="513af-508">Note that this negates the allocation reduction that this change brings.</span></span>

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="513af-509">Relational:TypeMapping 注釈が単に TypeMapping となった</span><span class="sxs-lookup"><span data-stu-id="513af-509">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="513af-510">問題 #9913 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-510">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="513af-511">この変更は、EF Core 3.0 プレビュー 2 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-511">This change is introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="513af-512">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-512">**Old behavior**</span></span>

<span data-ttu-id="513af-513">型マッピング注釈の注釈名は "Relational:TypeMapping" でした。</span><span class="sxs-lookup"><span data-stu-id="513af-513">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="513af-514">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-514">**New behavior**</span></span>

<span data-ttu-id="513af-515">型マッピング注釈の注釈名は、"TypeMapping" となりました。</span><span class="sxs-lookup"><span data-stu-id="513af-515">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="513af-516">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-516">**Why**</span></span>

<span data-ttu-id="513af-517">型マッピングが、リレーショナル データベース プロバイダー以外でも使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="513af-517">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="513af-518">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-518">**Mitigations**</span></span>

<span data-ttu-id="513af-519">これにより、一般的ではない、注釈として直接型マッピングにアクセスするアプリケーションのみが中断されるようになります。</span><span class="sxs-lookup"><span data-stu-id="513af-519">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="513af-520">問題を解決するための最も適切なアクションは、注釈を直接使用するのではなく、API サーフェスを使用して型マッピングにアクセスすることです。</span><span class="sxs-lookup"><span data-stu-id="513af-520">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

## <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="513af-521">派生型の ToTable で例外がスローされる</span><span class="sxs-lookup"><span data-stu-id="513af-521">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="513af-522">問題 #11811 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-522">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="513af-523">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-523">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="513af-524">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-524">**Old behavior**</span></span>

<span data-ttu-id="513af-525">EF Core 3.0 より前では、唯一の継承マッピング方法が TPH であり、これが有効でない場合には、派生型で呼び出された `ToTable()` が無視されていました。</span><span class="sxs-lookup"><span data-stu-id="513af-525">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="513af-526">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-526">**New behavior**</span></span>

<span data-ttu-id="513af-527">EF Core 3.0 以降、および今後のリリースでの TPT と TPC のサポートの追加準備中には、派生型で呼び出された `ToTable()` で、今後の予期されないマッピング変更を回避するために例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="513af-527">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="513af-528">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-528">**Why**</span></span>

<span data-ttu-id="513af-529">現在、派生型を別のテーブルにマップすることは有効ではありません。</span><span class="sxs-lookup"><span data-stu-id="513af-529">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="513af-530">この変更により、今後、これを行うことが有効になった場合に中断されなくなります。</span><span class="sxs-lookup"><span data-stu-id="513af-530">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="513af-531">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-531">**Mitigations**</span></span>

<span data-ttu-id="513af-532">派生型を他のテーブルにマップしないようにします。</span><span class="sxs-lookup"><span data-stu-id="513af-532">Remove any attempts to map derived types to other tables.</span></span>

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="513af-533">ForSqlServerHasIndex が HasIndex に置き換えられた</span><span class="sxs-lookup"><span data-stu-id="513af-533">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="513af-534">問題 #12366 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-534">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="513af-535">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-535">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="513af-536">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-536">**Old behavior**</span></span>

<span data-ttu-id="513af-537">EF Core 3.0 より前では、`ForSqlServerHasIndex().ForSqlServerInclude()` によって、`INCLUDE` で使用される列を構成する方法が提供されました。</span><span class="sxs-lookup"><span data-stu-id="513af-537">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="513af-538">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-538">**New behavior**</span></span>

<span data-ttu-id="513af-539">EF Core 3.0 以降では、インデックスでの `Include` の使用が、リレーショナル レベルでサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="513af-539">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="513af-540">`HasIndex().ForSqlServerInclude()` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="513af-540">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="513af-541">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-541">**Why**</span></span>

<span data-ttu-id="513af-542">この変更は、すべてのデータ プロバイダーのために `Include` でインデックス用の API を 1 か所に統合するために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-542">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="513af-543">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-543">**Mitigations**</span></span>

<span data-ttu-id="513af-544">上記のように、新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="513af-544">Use the new API, as shown above.</span></span>

## <a name="metadata-api-changes"></a><span data-ttu-id="513af-545">メタデータ API の変更点</span><span class="sxs-lookup"><span data-stu-id="513af-545">Metadata API changes</span></span>

[<span data-ttu-id="513af-546">問題 #214 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-546">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="513af-547">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-547">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-548">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-548">**New behavior**</span></span>

<span data-ttu-id="513af-549">次のプロパティは拡張メソッドに変換されました。</span><span class="sxs-lookup"><span data-stu-id="513af-549">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="513af-550">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-550">**Why**</span></span>

<span data-ttu-id="513af-551">この変更により、前述のインターフェイスの実装が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="513af-551">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="513af-552">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-552">**Mitigations**</span></span>

<span data-ttu-id="513af-553">新しい拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="513af-553">Use the new extension methods.</span></span>

## <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="513af-554">プロバイダー固有のメタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="513af-554">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="513af-555">問題 #214 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-555">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="513af-556">この変更は、EF Core 3.0 プレビュー 6 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-556">This change is introduced in EF Core 3.0-preview 6.</span></span>

<span data-ttu-id="513af-557">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-557">**New behavior**</span></span>

<span data-ttu-id="513af-558">プロバイダー固有の拡張メソッドがフラット化されます。</span><span class="sxs-lookup"><span data-stu-id="513af-558">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.GetSqlServerIsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.ForSqlServerUseIdentityColumn()`

<span data-ttu-id="513af-559">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-559">**Why**</span></span>

<span data-ttu-id="513af-560">この変更により、前述の拡張メソッドの実装が簡単になります。</span><span class="sxs-lookup"><span data-stu-id="513af-560">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="513af-561">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-561">**Mitigations**</span></span>

<span data-ttu-id="513af-562">新しい拡張メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="513af-562">Use the new extension methods.</span></span>

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="513af-563">EF Core で SQLite FK を適用するためのプラグマが送信されなくなった</span><span class="sxs-lookup"><span data-stu-id="513af-563">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="513af-564">問題 #12151 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-564">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="513af-565">この変更は、EF Core 3.0 プレビュー 3 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-565">This change is introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="513af-566">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-566">**Old behavior**</span></span>

<span data-ttu-id="513af-567">EF Core 3.0 より前では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されました。</span><span class="sxs-lookup"><span data-stu-id="513af-567">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="513af-568">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-568">**New behavior**</span></span>

<span data-ttu-id="513af-569">EF Core 3.0 以降では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="513af-569">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="513af-570">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-570">**Why**</span></span>

<span data-ttu-id="513af-571">この変更は、既定で EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用され、その後、FK の適用が既定で有効になり、接続が開かれるたびに明示的に有効にする必要がないため、行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-571">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="513af-572">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-572">**Mitigations**</span></span>

<span data-ttu-id="513af-573">外部キーは、EF Core に対して既定で使用される、SQLitePCLRaw.bundle_e_sqlite3 で既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="513af-573">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="513af-574">それ以外の場合は、接続文字列で `Foreign Keys=True` を指定することで、外部キーを有効にできます。</span><span class="sxs-lookup"><span data-stu-id="513af-574">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a><span data-ttu-id="513af-575">Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった</span><span class="sxs-lookup"><span data-stu-id="513af-575">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="513af-576">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-576">**Old behavior**</span></span>

<span data-ttu-id="513af-577">EF Core 3.0 より前では、EF Core では `SQLitePCLRaw.bundle_green` が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="513af-577">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="513af-578">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-578">**New behavior**</span></span>

<span data-ttu-id="513af-579">EF Core 3.0 以降では、EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="513af-579">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="513af-580">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-580">**Why**</span></span>

<span data-ttu-id="513af-581">この変更は、iOS 上で使用される SQLite のバージョンを、他のプラットフォームと一致させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="513af-581">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="513af-582">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-582">**Mitigations**</span></span>

<span data-ttu-id="513af-583">iOS でネイティブの SQLite バージョンを使用するには、別の `SQLitePCLRaw` バンドルを使用するように `Microsoft.Data.Sqlite` を構成します。</span><span class="sxs-lookup"><span data-stu-id="513af-583">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

## <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="513af-584">GUID の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="513af-584">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="513af-585">問題 #15078 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-585">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="513af-586">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-586">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-587">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-587">**Old behavior**</span></span>

<span data-ttu-id="513af-588">GUID の値は、以前は SQLite に BLOB 値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="513af-588">Guid values were previously sored as BLOB values on SQLite.</span></span>

<span data-ttu-id="513af-589">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-589">**New behavior**</span></span>

<span data-ttu-id="513af-590">GUID の値はテキストとして格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="513af-590">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="513af-591">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-591">**Why**</span></span>

<span data-ttu-id="513af-592">GUID のバイナリ形式は標準化されていません。</span><span class="sxs-lookup"><span data-stu-id="513af-592">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="513af-593">値をテキストとして格納する方が、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="513af-593">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="513af-594">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-594">**Mitigations**</span></span>

<span data-ttu-id="513af-595">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="513af-595">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

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

<span data-ttu-id="513af-596">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="513af-596">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="513af-597">Microsoft.Data.Sqlite は引き続き、BLOB とテキストの両方の列から GUID 値を読み取ることができます。ただし、パラメーターと定数の既定の形式が変更されているため、GUID が含まれる多くのシナリオではアクションが必要になります。</span><span class="sxs-lookup"><span data-stu-id="513af-597">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

## <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="513af-598">Char の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="513af-598">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="513af-599">問題 #15020 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-599">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="513af-600">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-600">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-601">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-601">**Old behavior**</span></span>

<span data-ttu-id="513af-602">Char の値は、以前は SQLite に整数値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="513af-602">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="513af-603">たとえば、Char の値 *A* は整数値 65 として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="513af-603">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="513af-604">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-604">**New behavior**</span></span>

<span data-ttu-id="513af-605">Char の値はテキストとして格納されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="513af-605">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="513af-606">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-606">**Why**</span></span>

<span data-ttu-id="513af-607">値をテキストとして格納する方が自然であり、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="513af-607">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="513af-608">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-608">**Mitigations**</span></span>

<span data-ttu-id="513af-609">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="513af-609">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="513af-610">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="513af-610">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="513af-611">Microsoft.Data.Sqlite では、引き続き整数とテキストの両方の列の文字列値を読み取ることができるため、一部のシナリオではアクションが必要ありません。</span><span class="sxs-lookup"><span data-stu-id="513af-611">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

## <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="513af-612">移行 ID がインバリアント カルチャの暦を使用して生成されるようになった</span><span class="sxs-lookup"><span data-stu-id="513af-612">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="513af-613">問題 #12978 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-613">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="513af-614">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-614">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-615">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-615">**Old behavior**</span></span>

<span data-ttu-id="513af-616">移行 ID は、誤って現在のカルチャの暦を使用して生成されていました。</span><span class="sxs-lookup"><span data-stu-id="513af-616">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="513af-617">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-617">**New behavior**</span></span>

<span data-ttu-id="513af-618">移行 ID がインバリアント カルチャの暦 (グレゴリオ暦) を使用して常に生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="513af-618">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="513af-619">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-619">**Why**</span></span>

<span data-ttu-id="513af-620">データベースを更新するときやマージの競合を解決するときに、移行の順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="513af-620">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="513af-621">インバリアントの暦を使用することで、順序の問題が回避され、システムの予定表がチームのメンバーごとに違うことがなくなります。</span><span class="sxs-lookup"><span data-stu-id="513af-621">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="513af-622">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-622">**Mitigations**</span></span>

<span data-ttu-id="513af-623">この変更は、1 年がグレゴリオ暦より長くなるグレゴリオ暦以外の暦 (タイ仏暦など) を使用しているすべてのユーザーに影響します。</span><span class="sxs-lookup"><span data-stu-id="513af-623">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="513af-624">既存の移行後に新しい移行順序が設定されるように、既存の移行 ID を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-624">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="513af-625">移行 ID は、移行のデザイナー ファイルの移行属性にあります。</span><span class="sxs-lookup"><span data-stu-id="513af-625">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="513af-626">移行履歴テーブルも更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-626">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

## <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="513af-627">IDbContextOptionsExtension から拡張機能の情報/メタデータを削除</span><span class="sxs-lookup"><span data-stu-id="513af-627">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="513af-628">問題 #16119 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-628">Tracking Issue #16119</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

<span data-ttu-id="513af-629">この変更は、EF Core 3.0 プレビュー 7 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-629">This change is introduced in EF Core 3.0-preview 7.</span></span>

<span data-ttu-id="513af-630">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-630">**Old behavior**</span></span>

<span data-ttu-id="513af-631">`IDbContextOptionsExtension` には、拡張機能に関するメタデータを提供するためのメソッドが含まれていました。</span><span class="sxs-lookup"><span data-stu-id="513af-631">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

<span data-ttu-id="513af-632">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-632">**New behavior**</span></span>

<span data-ttu-id="513af-633">これらのメソッドは、新しい抽象基底クラス `DbContextOptionsExtensionInfo` 上に移動されました。これは新しい `IDbContextOptionsExtension.Info` プロパティから返されます。</span><span class="sxs-lookup"><span data-stu-id="513af-633">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

<span data-ttu-id="513af-634">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-634">**Why**</span></span>

<span data-ttu-id="513af-635">2\.0 から 3.0 までのリリースを通して、これらのメソッドに追加または変更を行う必要が複数回発生しました。</span><span class="sxs-lookup"><span data-stu-id="513af-635">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="513af-636">それらを抜き出して新しい抽象基底クラスに含めることで、既存の拡張機能を損なうことなく、簡単にこのような変更を加えられるようになります。</span><span class="sxs-lookup"><span data-stu-id="513af-636">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

<span data-ttu-id="513af-637">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-637">**Mitigations**</span></span>

<span data-ttu-id="513af-638">新しいパターンに従うよう拡張機能を更新します。</span><span class="sxs-lookup"><span data-stu-id="513af-638">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="513af-639">その例は、EF Core のソース コードの、さまざまな種類の拡張機能に対する多数の `IDbContextOptionsExtension` の実装で見つかります。</span><span class="sxs-lookup"><span data-stu-id="513af-639">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

## <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="513af-640">LogQueryPossibleExceptionWithAggregateOperator の名前が変更された</span><span class="sxs-lookup"><span data-stu-id="513af-640">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="513af-641">問題 #10985 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-641">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="513af-642">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-642">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-643">**変更**</span><span class="sxs-lookup"><span data-stu-id="513af-643">**Change**</span></span>

<span data-ttu-id="513af-644">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` の名前が `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning` に変更されました。</span><span class="sxs-lookup"><span data-stu-id="513af-644">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="513af-645">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-645">**Why**</span></span>

<span data-ttu-id="513af-646">この警告イベントの名前が他のすべての警告イベントと照合されるためです。</span><span class="sxs-lookup"><span data-stu-id="513af-646">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="513af-647">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-647">**Mitigations**</span></span>

<span data-ttu-id="513af-648">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="513af-648">Use the new name.</span></span> <span data-ttu-id="513af-649">(イベント ID 番号が変更されていないことを確認してください。)</span><span class="sxs-lookup"><span data-stu-id="513af-649">(Note that the event ID number has not changed.)</span></span>

## <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="513af-650">外部キー制約名の API が明確化された</span><span class="sxs-lookup"><span data-stu-id="513af-650">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="513af-651">問題 #10730 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-651">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="513af-652">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-652">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-653">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-653">**Old behavior**</span></span>

<span data-ttu-id="513af-654">EF Core 3.0 以前は、外部キー制約名が単に "名前" と呼ばれていました。</span><span class="sxs-lookup"><span data-stu-id="513af-654">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="513af-655">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="513af-655">For example:</span></span>

```C#
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="513af-656">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-656">**New behavior**</span></span>

<span data-ttu-id="513af-657">EF Core 3.0 以降は、外部キー制約名が "制約名" と呼ばれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="513af-657">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="513af-658">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="513af-658">For example:</span></span>

```C#
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="513af-659">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-659">**Why**</span></span>

<span data-ttu-id="513af-660">この変更により、この領域の名前付けに一貫性がもたらされます。また、それが外部キーが定義されている列やプロパティの名前ではなく、外部キー制約の名前であることが明確になりました。</span><span class="sxs-lookup"><span data-stu-id="513af-660">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="513af-661">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-661">**Mitigations**</span></span>

<span data-ttu-id="513af-662">新しい名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="513af-662">Use the new name.</span></span>

## <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="513af-663">IRelationalDatabaseCreator.HasTables/HasTablesAsync をパブリックに変更</span><span class="sxs-lookup"><span data-stu-id="513af-663">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="513af-664">問題 #15997 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-664">Tracking Issue #15997</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

<span data-ttu-id="513af-665">この変更は、EF Core 3.0 プレビュー 7 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-665">This change is introduced in EF Core 3.0-preview 7.</span></span>

<span data-ttu-id="513af-666">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-666">**Old behavior**</span></span>

<span data-ttu-id="513af-667">EF Core 3.0 より前では、これらのメソッドは保護されていました。</span><span class="sxs-lookup"><span data-stu-id="513af-667">Before EF Core 3.0, these methods were protected.</span></span>

```C#
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="513af-668">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-668">**New behavior**</span></span>

<span data-ttu-id="513af-669">EF Core 3.0 以降、これらのメソッドはパブリックになります。</span><span class="sxs-lookup"><span data-stu-id="513af-669">Starting with EF Core 3.0, these methods are public.</span></span>

<span data-ttu-id="513af-670">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-670">**Why**</span></span>

<span data-ttu-id="513af-671">これらのメソッドは、作成されたデータベースが空であるかどうかを判断するために EF によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="513af-671">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="513af-672">これは、EF の外部から、移行を適用するかどうか判断する場合にも役立ちます。</span><span class="sxs-lookup"><span data-stu-id="513af-672">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

<span data-ttu-id="513af-673">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-673">**Mitigations**</span></span>

<span data-ttu-id="513af-674">すべてのオーバーライドのアクセシビリティを変更します。</span><span class="sxs-lookup"><span data-stu-id="513af-674">Change the accessibility of any overrides.</span></span>

## <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="513af-675">Microsoft.EntityFrameworkCore.Design を DevelopmentDependency パッケージに変更</span><span class="sxs-lookup"><span data-stu-id="513af-675">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="513af-676">問題 #11506 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-676">Tracking Issue #11506</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

<span data-ttu-id="513af-677">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-677">This change is introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="513af-678">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-678">**Old behavior**</span></span>

<span data-ttu-id="513af-679">EF Core 3.0 より前では、Microsoft.EntityFrameworkCore.Design は通常の NuGet パッケージであり、それに依存していたプロジェクトによってそのアセンブリを参照できました。</span><span class="sxs-lookup"><span data-stu-id="513af-679">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

<span data-ttu-id="513af-680">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-680">**New behavior**</span></span>

<span data-ttu-id="513af-681">EF Core 3.0 以降、これは DevelopmentDependency パッケージになります。</span><span class="sxs-lookup"><span data-stu-id="513af-681">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="513af-682">つまり、依存関係が他のプロジェクトに推移的にフローすることがなくなり、規定ではそのアセンブリを参照できなくなります。</span><span class="sxs-lookup"><span data-stu-id="513af-682">Which means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

<span data-ttu-id="513af-683">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-683">**Why**</span></span>

<span data-ttu-id="513af-684">このパッケージは、デザイン時に使用されることだけを想定しています。</span><span class="sxs-lookup"><span data-stu-id="513af-684">This package is only intended to be used at design time.</span></span> <span data-ttu-id="513af-685">デプロイされたアプリケーションからこれを参照すべきではありません。</span><span class="sxs-lookup"><span data-stu-id="513af-685">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="513af-686">パッケージを DevelopmentDependency にすることで、この推奨事項が強化されます。</span><span class="sxs-lookup"><span data-stu-id="513af-686">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

<span data-ttu-id="513af-687">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-687">**Mitigations**</span></span>

<span data-ttu-id="513af-688">EF Core のデザイン時の動作をオーバーライドするためにこのパッケージを参照する必要がある場合は、プロジェクトの PackageReference 項目メタデータを更新することができます。</span><span class="sxs-lookup"><span data-stu-id="513af-688">If you need to reference this package to override EF Core's design-time behavior, you can update update PackageReference item metadata in your project.</span></span> <span data-ttu-id="513af-689">このパッケージが Microsoft.EntityFrameworkCore.Tools 経由で推移的に参照されている場合は、パッケージに明示的な PackageReference を追加して、そのメタデータを変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="513af-689">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0-preview4.19216.3">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

## <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="513af-690">SQLitePCL.raw のバージョン 2.0.0 への更新</span><span class="sxs-lookup"><span data-stu-id="513af-690">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="513af-691">問題 #14824 の追跡</span><span class="sxs-lookup"><span data-stu-id="513af-691">Tracking Issue #14824</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

<span data-ttu-id="513af-692">この変更は、EF Core 3.0 プレビュー 7 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="513af-692">This change is introduced in EF Core 3.0-preview 7.</span></span>

<span data-ttu-id="513af-693">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="513af-693">**Old behavior**</span></span>

<span data-ttu-id="513af-694">Microsoft.EntityFrameworkCore.Sqlite は、以前はバージョン 1.1.12 の SQLitePCL.raw に依存していました。</span><span class="sxs-lookup"><span data-stu-id="513af-694">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

<span data-ttu-id="513af-695">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="513af-695">**New behavior**</span></span>

<span data-ttu-id="513af-696">バージョン 2.0.0 に依存するようパッケージが更新されました。</span><span class="sxs-lookup"><span data-stu-id="513af-696">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="513af-697">**理由**</span><span class="sxs-lookup"><span data-stu-id="513af-697">**Why**</span></span>

<span data-ttu-id="513af-698">バージョン 2.0.0 の SQLitePCL.raw では、.NET Standard 2.0 をターゲットとします。</span><span class="sxs-lookup"><span data-stu-id="513af-698">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="513af-699">これは以前は、推移的なパッケージの大規模なクロージャの動作を必要とする .NET Standard 1.1 をターゲットとしていました。</span><span class="sxs-lookup"><span data-stu-id="513af-699">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

<span data-ttu-id="513af-700">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="513af-700">**Mitigations**</span></span>

<span data-ttu-id="513af-701">SQLitePCL.raw バージョン 2.0.0 には、いくつかの破壊的変更が含まれています。</span><span class="sxs-lookup"><span data-stu-id="513af-701">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="513af-702">詳細については、[リリース ノート](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md)をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="513af-702">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>
