---
title: EF Core 3.0 での破壊的変更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: 534ac95cccc03e9797ba766e601e2fe86eaf8061
ms.sourcegitcommit: eb8359b7ab3b0a1a08522faf67b703a00ecdcefd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319219"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a><span data-ttu-id="42358-102">EF Core 3.0 (現在プレビュー段階) に含まれる破壊的変更</span><span class="sxs-lookup"><span data-stu-id="42358-102">Breaking changes included in EF Core 3.0 (currently in preview)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="42358-103">機能セットと今後のリリースのスケジュールは、常に変更される可能性があることに注意してください。また、このページを最新の状態に保持するようにしていますが、最新のプランが反映されていない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="42358-103">Please note that the feature sets and schedules of future releases are always subject to change, and although we will try to keep this page up to date, it may not reflect our latest plans at all times.</span></span>

<span data-ttu-id="42358-104">以下の API と動作変更により、3.0.0 へのアップグレード時に、EF Core 2.2.x 用に開発されたアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42358-104">The following API and behavior changes have the potential to break applications developed for EF Core 2.2.x when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="42358-105">データベース プロバイダーにのみ影響することが予想される変更については、[プロバイダーの変更](../../providers/provider-log.md)に関するページに記載されています。</span><span class="sxs-lookup"><span data-stu-id="42358-105">Changes that we expect to only impact database providers are documented under [provider changes](../../providers/provider-log.md).</span></span>
<span data-ttu-id="42358-106">ある 3.0 プレビューから別の 3.0 プレビューに導入される新機能での中断については、ここには記載されていません。</span><span class="sxs-lookup"><span data-stu-id="42358-106">Breaks in new features introduced from one 3.0 preview to another 3.0 preview aren't documented here.</span></span>

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="42358-107">LINQ クエリがクライアントで評価されなくなった</span><span class="sxs-lookup"><span data-stu-id="42358-107">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="42358-108">[問題 #14935 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[問題 #12795 も参照](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="42358-108">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="42358-109">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="42358-109">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="42358-110">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-110">**Old behavior**</span></span>

<span data-ttu-id="42358-111">3.0 より前では、EF Core では、SQL またはパラメーターに対するクエリの一部だった式を変換できない場合、クライアントで自動的に式が評価されていました。</span><span class="sxs-lookup"><span data-stu-id="42358-111">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="42358-112">既定では、コストの高い式のクライアント評価でのみ警告がトリガーされました。</span><span class="sxs-lookup"><span data-stu-id="42358-112">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="42358-113">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-113">**New behavior**</span></span>

<span data-ttu-id="42358-114">3.0 以降では、クライアントで評価される最上位のプロジェクション (クエリの最後の `Select()` 呼び出し) の式のみが EF Core で許可されます。</span><span class="sxs-lookup"><span data-stu-id="42358-114">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="42358-115">クエリの他の部分の式を SQL やパラメーターに変換できない場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="42358-115">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="42358-116">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-116">**Why**</span></span>

<span data-ttu-id="42358-117">クエリの自動クライアント評価では、多くのクエリを実行できます。これらの重要な部分を変換できない場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="42358-117">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="42358-118">この動作が原因で、予期しない損害を与える動作となる可能性があります。この動作は、運用環境でのみ見られる場合があります。</span><span class="sxs-lookup"><span data-stu-id="42358-118">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="42358-119">たとえば、変換できない `Where()` 呼び出しの条件が原因で、テーブルのすべての行がデータベース サーバーから転送できるようになり、クライアントでフィルターを適用できるようになる場合があります。</span><span class="sxs-lookup"><span data-stu-id="42358-119">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="42358-120">このような状況は、開発時にテーブルにわずかな行が含まれている場合は、簡単に検出される可能性があります。しかし、テーブルに数百万もの行が含まれる、運用環境にアプリケーションを移行する場合は難しくなる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42358-120">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="42358-121">クライアント評価の警告では、開発中にあまりに簡単に無視されることも示されていました。</span><span class="sxs-lookup"><span data-stu-id="42358-121">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="42358-122">これだけでなく、自動クライアント評価は、特定の式のクエリ変換の改善が、リリース間の予期しない破壊的変更の原因となる問題につながる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42358-122">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="42358-123">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-123">**Mitigations**</span></span>

<span data-ttu-id="42358-124">クエリを完全に変換できない場合は、変換できる形式でクエリを書き直すか、`AsEnumerable()`、`ToList()`、または同様のものを使用して、LINQ-to-Objects を使ってさらに処理できるクライアントに明示的にデータを戻します。</span><span class="sxs-lookup"><span data-stu-id="42358-124">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="42358-125">Entity Framework Core が ASP.NET Core 共有フレームワークの一部ではなくなった</span><span class="sxs-lookup"><span data-stu-id="42358-125">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="42358-126">問題 Announcements#325 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-126">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="42358-127">この変更は、ASP.NET Core 3.0 プレビュー 1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-127">This change was introduced in ASP.NET Core 3.0-preview 1.</span></span> 

<span data-ttu-id="42358-128">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-128">**Old behavior**</span></span>

<span data-ttu-id="42358-129">ASP.NET Core 3.0 より前では、パッケージ参照を `Microsoft.AspNetCore.App` または `Microsoft.AspNetCore.All` に追加したときに、EF Core と、SQL Server プロバイダーのような EF Core データ プロバイダーの一部が含まれました。</span><span class="sxs-lookup"><span data-stu-id="42358-129">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="42358-130">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-130">**New behavior**</span></span>

<span data-ttu-id="42358-131">3.0 以降では、ASP.NET Core 共有フレームワークには、EF Core も、いずれの EF Core データ プロバイダーも含まれません。</span><span class="sxs-lookup"><span data-stu-id="42358-131">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="42358-132">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-132">**Why**</span></span>

<span data-ttu-id="42358-133">この変更の前では、アプリケーションのターゲットが ASP.NET Core であるか SQL Server であるかに応じて、EF Core を取得するには異なる手順が必要でした。</span><span class="sxs-lookup"><span data-stu-id="42358-133">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="42358-134">また、ASP.NET Core のアップグレードでは、必ずしも適切だとは言えない、EF Core と SQL Server プロバイダーのアップグレードが強制されました。</span><span class="sxs-lookup"><span data-stu-id="42358-134">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="42358-135">この変更では、EF Core の取得のエクスペリエンスは、.NET の実装とアプリケーションの種類がサポートされる、すべてのプロバイダーで同じになります。</span><span class="sxs-lookup"><span data-stu-id="42358-135">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="42358-136">また、開発者は、EF Core と EF Core データ プロバイダーのアップグレードのタイミングを正確に制御できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="42358-136">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="42358-137">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-137">**Mitigations**</span></span>

<span data-ttu-id="42358-138">ASP.NET Core 3.0 アプリケーションまたはその他のサポートされるアプリケーションで EF Core を使用するには、アプリケーションで使用される EF Core データベース プロバイダーにパッケージ参照を明示的に追加します。</span><span class="sxs-lookup"><span data-stu-id="42358-138">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

## <a name="query-execution-is-logged-at-debug-level"></a><span data-ttu-id="42358-139">クエリの実行がデバッグ レベルでログに記録される</span><span class="sxs-lookup"><span data-stu-id="42358-139">Query execution is logged at Debug level</span></span>

[<span data-ttu-id="42358-140">問題 #14523 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-140">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="42358-141">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-141">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-142">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-142">**Old behavior**</span></span>

<span data-ttu-id="42358-143">EF Core 3.0 より前では、クエリと他のコマンドの実行は、`Info` レベルでログに記録されました。</span><span class="sxs-lookup"><span data-stu-id="42358-143">Before EF Core 3.0, execution of queries and other commands was logged at the `Info` level.</span></span>

<span data-ttu-id="42358-144">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-144">**New behavior**</span></span>

<span data-ttu-id="42358-145">EF Core 3.0 以降では、コマンド/SQL の実行は、`Debug` レベルでログに記録されます。</span><span class="sxs-lookup"><span data-stu-id="42358-145">Starting with EF Core 3.0, logging of command/SQL execution is at the `Debug` level.</span></span>

<span data-ttu-id="42358-146">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-146">**Why**</span></span>

<span data-ttu-id="42358-147">この変更は、`Info` ログ レベルでのノイズを減らすために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-147">This change was made to reduce the noise at the `Info` log level.</span></span>

<span data-ttu-id="42358-148">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-148">**Mitigations**</span></span>

<span data-ttu-id="42358-149">このログ イベントは、イベント ID 20100 の `RelationalEventId.CommandExecuting` によって定義されています。</span><span class="sxs-lookup"><span data-stu-id="42358-149">This logging event is defined by `RelationalEventId.CommandExecuting` with event ID 20100.</span></span>
<span data-ttu-id="42358-150">`Info` レベルで SQL をもう一度ログに記録するには、`OnConfiguring` または `AddDbContext` で明示的にレベルを構成します。</span><span class="sxs-lookup"><span data-stu-id="42358-150">To log SQL at the `Info` level again, explicitly configure the level in `OnConfiguring` or `AddDbContext`.</span></span>
<span data-ttu-id="42358-151">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="42358-151">For example:</span></span>
```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Info)));
```

## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="42358-152">一時キーの値がエンティティ インスタンスに設定されなくなった</span><span class="sxs-lookup"><span data-stu-id="42358-152">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="42358-153">問題 #12378 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-153">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="42358-154">この変更は、EF Core 3.0 プレビュー 2 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-154">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="42358-155">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-155">**Old behavior**</span></span>

<span data-ttu-id="42358-156">EF Core 3.0 より前では、後で実際の値がデータベースによって生成される、すべてのキー プロパティに一時的な値が割り当てられていました。</span><span class="sxs-lookup"><span data-stu-id="42358-156">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="42358-157">通常、これらの一時的な値は大きい負の数値でした。</span><span class="sxs-lookup"><span data-stu-id="42358-157">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="42358-158">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-158">**New behavior**</span></span>

<span data-ttu-id="42358-159">3.0 以降では、EF Core で、エンティティの追跡情報の一部として一時キーの値が格納され、キー プロパティ自体はそのままになります。</span><span class="sxs-lookup"><span data-stu-id="42358-159">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="42358-160">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-160">**Why**</span></span>

<span data-ttu-id="42358-161">この変更は、何らかの `DbContext` インスタンスによって以前に追跡されたエンティティが、別の `DbContext` インスタンスに移動されるときに、一時キーの値が誤って永続的なものにならないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-161">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="42358-162">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-162">**Mitigations**</span></span>

<span data-ttu-id="42358-163">主キーの値を外部キーに割り当てて、エンティティ間の関連付けを形成するアプリケーションは、主キーがストアで生成されており、`Added` 状態のエンティティに属している場合、以前の動作に依存する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42358-163">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="42358-164">これは、次のようにして回避できます。</span><span class="sxs-lookup"><span data-stu-id="42358-164">This can be avoided by:</span></span>
* <span data-ttu-id="42358-165">ストア生成キーを使用しない。</span><span class="sxs-lookup"><span data-stu-id="42358-165">Not using store-generated keys.</span></span>
* <span data-ttu-id="42358-166">外部キーの値を設定するのではなく、ナビゲーション プロパティを設定してリレーションシップを形成する。</span><span class="sxs-lookup"><span data-stu-id="42358-166">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="42358-167">エンティティの追跡情報から実際の一時キーの値を取得する。</span><span class="sxs-lookup"><span data-stu-id="42358-167">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="42358-168">たとえば、`context.Entry(blog).Property(e => e.Id).CurrentValue` では、`blog.Id` 自体が設定されていない場合でも、一時的な値が返されます。</span><span class="sxs-lookup"><span data-stu-id="42358-168">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

## <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="42358-169">DetectChanges でストア生成キーの値が優先される</span><span class="sxs-lookup"><span data-stu-id="42358-169">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="42358-170">問題 #14616 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-170">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="42358-171">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-171">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-172">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-172">**Old behavior**</span></span>

<span data-ttu-id="42358-173">EF Core 3.0 より前では、`DetectChanges` によって検出された追跡対象外のエンティティが `Added` 状態で追跡され、`SaveChanges` が呼び出されたときに新しい行として挿入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-173">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="42358-174">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-174">**New behavior**</span></span>

<span data-ttu-id="42358-175">EF Core 3.0 以降では、エンティティで生成されたキーの値を使用し、何らかのキーの値が設定されている場合、そのエンティティは `Modified` 状態で追跡されます。</span><span class="sxs-lookup"><span data-stu-id="42358-175">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="42358-176">つまり、エンティティの行が存在していると見なされ、`SaveChanges` の呼び出し時に更新されます。</span><span class="sxs-lookup"><span data-stu-id="42358-176">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="42358-177">キーの値が設定されていないか、エンティティ型で生成されたキーが使用されない場合、新しいエンティティは引き続き、以前のバージョンと同様に `Added` として追跡されます。</span><span class="sxs-lookup"><span data-stu-id="42358-177">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="42358-178">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-178">**Why**</span></span>

<span data-ttu-id="42358-179">この変更は、ストア生成キーの使用中に、切り離されたエンティティ グラフをより簡単に一貫して操作できるようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-179">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="42358-180">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-180">**Mitigations**</span></span>

<span data-ttu-id="42358-181">エンティティ型が生成されたキーを使用するように構成されているものの、キーの値が新しいインスタンスに対して明示的に設定されている場合、この変更によってアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42358-181">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="42358-182">この問題を解決するには、キー プロパティで生成された値が使用されないように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="42358-182">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="42358-183">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="42358-183">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="42358-184">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="42358-184">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="42358-185">既定で連鎖削除がすぐに行われるようになった</span><span class="sxs-lookup"><span data-stu-id="42358-185">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="42358-186">問題 #10114 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-186">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="42358-187">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-187">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-188">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-188">**Old behavior**</span></span>

<span data-ttu-id="42358-189">3.0 より前では、EF Core で適用された連鎖操作 (必要なプリンシパルが削除されたか、必要なプリンシパルへのリレーションシップが切断されたときに依存エンティティを削除する) は、SaveChanges が呼び出されるまで行われませんでした。</span><span class="sxs-lookup"><span data-stu-id="42358-189">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="42358-190">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-190">**New behavior**</span></span>

<span data-ttu-id="42358-191">3.0 以降では、EF Core で、トリガー条件が検出されるとすぐに連鎖操作が適用されます。</span><span class="sxs-lookup"><span data-stu-id="42358-191">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="42358-192">たとえば、プリンシパル エンティティを削除するために `context.Remove()` を呼び出すと、すべての追跡対象の関連する必要な依存関係もすぐに `Deleted` に設定されます。</span><span class="sxs-lookup"><span data-stu-id="42358-192">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="42358-193">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-193">**Why**</span></span>

<span data-ttu-id="42358-194">この変更は、`SaveChanges` が呼び出される_前に_どのエンティティが削除されるかを把握することが重要である、データ バインディングおよび監査シナリオのエクスペリエンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-194">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="42358-195">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-195">**Mitigations**</span></span>

<span data-ttu-id="42358-196">`context.ChangedTracker` の設定を使用して、以前の動作を復元することができます。</span><span class="sxs-lookup"><span data-stu-id="42358-196">The previous behavior can be restored through settings on `context.ChangedTracker`.</span></span>
<span data-ttu-id="42358-197">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="42358-197">For example:</span></span>

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="42358-198">クエリ型がエンティティ型と統合される</span><span class="sxs-lookup"><span data-stu-id="42358-198">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="42358-199">問題 #14194 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-199">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="42358-200">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-200">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-201">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-201">**Old behavior**</span></span>

<span data-ttu-id="42358-202">EF Core 3.0 より前では、[クエリ型](xref:core/modeling/query-types)は、構造化された方法で主キーが定義されないデータのクエリを実行するための手段でした。</span><span class="sxs-lookup"><span data-stu-id="42358-202">Before EF Core 3.0, [query types](xref:core/modeling/query-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="42358-203">つまり、クエリ型は、キーが利用できるときに (テーブルからの可能性が高くなりますが、ビューからの可能性もあります) 通常のエンティティ型が使用された場合に、キーなしの (ビューからの可能性が高くなりますが、テーブルからの可能性もあります) エンティティ型をマップするために使用されました。</span><span class="sxs-lookup"><span data-stu-id="42358-203">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="42358-204">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-204">**New behavior**</span></span>

<span data-ttu-id="42358-205">クエリ型は、現在、主キーなしの単なるエンティティ型になります。</span><span class="sxs-lookup"><span data-stu-id="42358-205">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="42358-206">キーなしのエンティティ型の機能は、以前のバージョンのクエリ型と同じです。</span><span class="sxs-lookup"><span data-stu-id="42358-206">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="42358-207">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-207">**Why**</span></span>

<span data-ttu-id="42358-208">この変更は、クエリ型の目的に関する混乱を減らすために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-208">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="42358-209">つまり、これらはキーなしのエンティティ型であるため、本質的には読み取り専用となりますが、単にエンティティ型を読み取り専用にする必要があるという理由で使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="42358-209">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="42358-210">同様に、これらは多くの場合、ビューにマップされますが、これは、単にビューでは多くの場合、キーが定義されないためです。</span><span class="sxs-lookup"><span data-stu-id="42358-210">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="42358-211">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-211">**Mitigations**</span></span>

<span data-ttu-id="42358-212">API の以下の部分は使用されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="42358-212">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="42358-213">**`ModelBuilder.Query<>()`** - 代わりに `ModelBuilder.Entity<>().HasNoKey()` を呼び出して、エンティティ型をキーなしとしてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="42358-213">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="42358-214">これは、規則により、主キーは必要であるが、規則と一致しない場合に構成エラーを回避するようにはまだ構成されません。</span><span class="sxs-lookup"><span data-stu-id="42358-214">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="42358-215">**`DbQuery<>`** - 代わりに `DbSet<>` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="42358-215">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="42358-216">**`DbContext.Query<>()`** - 代わりに `DbContext.Set<>()` を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="42358-216">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="42358-217">所有型のリレーションシップ用の構成 API が変更された</span><span class="sxs-lookup"><span data-stu-id="42358-217">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="42358-218">[問題 #12444 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[問題 #9148 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[問題 #14153 の追跡](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="42358-218">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="42358-219">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-219">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-220">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-220">**Old behavior**</span></span>

<span data-ttu-id="42358-221">EF Core 3.0 より前では、所有リレーションシップの構成は、`OwnsOne` または `OwnsMany` の呼び出し直後に実行されました。</span><span class="sxs-lookup"><span data-stu-id="42358-221">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="42358-222">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-222">**New behavior**</span></span>

<span data-ttu-id="42358-223">EF Core 3.0 以降では、fluent API で、`WithOwner()` を使用して、所有者に対してナビゲーション プロパティが構成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="42358-223">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="42358-224">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="42358-224">For example:</span></span>

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="42358-225">現在、所有者と所有型間のリレーションシップに関する構成は、他のリレーションシップの構成方法と同様、`WithOwner()` 後にチェーンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="42358-225">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="42358-226">一方、所有型自体の構成は引き続き、`OwnsOne()/OwnsMany()` 後にチェーンされます。</span><span class="sxs-lookup"><span data-stu-id="42358-226">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="42358-227">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="42358-227">For example:</span></span>

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

<span data-ttu-id="42358-228">さらに所有型ターゲットでの `Entity()`、`HasOne()`、または `Set()` の呼び出しでは、例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="42358-228">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="42358-229">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-229">**Why**</span></span>

<span data-ttu-id="42358-230">この変更は、所有型自体と、所有型_へのリレーションシップ_の構成を明確に分離するために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-230">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="42358-231">これにより、`HasForeignKey` のようなメソッドに関するあいまいさと混乱はなくなります。</span><span class="sxs-lookup"><span data-stu-id="42358-231">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="42358-232">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-232">**Mitigations**</span></span>

<span data-ttu-id="42358-233">上記の例で示すように、新しい API サーフェスを使用するように、所有型リレーションシップの構成を変更します。</span><span class="sxs-lookup"><span data-stu-id="42358-233">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="42358-234">外部キー プロパティの規則がプリンシパル プロパティと同じ名前と一致しなくなった</span><span class="sxs-lookup"><span data-stu-id="42358-234">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="42358-235">問題 #13274 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-235">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="42358-236">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-236">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-237">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-237">**Old behavior**</span></span>

<span data-ttu-id="42358-238">次のモデルがあるとします。</span><span class="sxs-lookup"><span data-stu-id="42358-238">Consider the following model:</span></span>
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
<span data-ttu-id="42358-239">EF Core 3.0 より前では、規則により、外部キーで `CustomerId` プロパティが使用されました。</span><span class="sxs-lookup"><span data-stu-id="42358-239">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="42358-240">しかし、`Order` が所有型である場合、`CustomerId` も主キーとマークされ、これは通常、期待されることではありません。</span><span class="sxs-lookup"><span data-stu-id="42358-240">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="42358-241">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-241">**New behavior**</span></span>

<span data-ttu-id="42358-242">3.0 以降では、EF Core で、プリンシパル プロパティと同じ名前である場合、規則により、外部キーに対するプロパティの使用は試行されません。</span><span class="sxs-lookup"><span data-stu-id="42358-242">Starting with 3.0, EF Core won't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="42358-243">プリンシパル プロパティ名と連結されたプリンシパル型名、およびプリンシパル プロパティ名パターンと連結されたナビゲーション名は、引き続き一致します。</span><span class="sxs-lookup"><span data-stu-id="42358-243">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="42358-244">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="42358-244">For example:</span></span>

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

<span data-ttu-id="42358-245">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-245">**Why**</span></span>

<span data-ttu-id="42358-246">この変更は、所有型に主キー プロパティが誤って定義されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-246">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="42358-247">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-247">**Mitigations**</span></span>

<span data-ttu-id="42358-248">プロパティを外部キーにする、したがって、主キーの一部にする予定だった場合は、そのように明示的に構成します。</span><span class="sxs-lookup"><span data-stu-id="42358-248">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="42358-249">各プロパティで独立したメモリ内整数キー生成が使用される</span><span class="sxs-lookup"><span data-stu-id="42358-249">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="42358-250">問題 #6872 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-250">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="42358-251">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="42358-251">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="42358-252">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-252">**Old behavior**</span></span>

<span data-ttu-id="42358-253">EF Core 3.0 より前では、1 つの共有値ジェネレーターが、すべてのメモリ内整数キー プロパティで使用されていました。</span><span class="sxs-lookup"><span data-stu-id="42358-253">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="42358-254">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-254">**New behavior**</span></span>

<span data-ttu-id="42358-255">EF Core 3.0 以降では、各整数キー プロパティで、メモリ内データベースを使用するときに独自の値ジェネレーターが取得されます。</span><span class="sxs-lookup"><span data-stu-id="42358-255">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="42358-256">また、データベースが削除された場合は、すべてのテーブルに対してキーの生成がリセットされます。</span><span class="sxs-lookup"><span data-stu-id="42358-256">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="42358-257">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-257">**Why**</span></span>

<span data-ttu-id="42358-258">この変更は、実際のデータベース キー生成により近くなるようにメモリ内キー生成を調整するため、また、メモリ内データベースを使用するときに互いのテストを分離させる機能を向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-258">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="42358-259">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-259">**Mitigations**</span></span>

<span data-ttu-id="42358-260">これにより、設定される特定のメモリ内キー値に依存しているアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42358-260">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="42358-261">代わりに特定のキーの値に依存しないようにするか、あるいは新しい動作と一致するように更新することを検討します。</span><span class="sxs-lookup"><span data-stu-id="42358-261">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

## <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="42358-262">バッキング フィールドが既定で使用される</span><span class="sxs-lookup"><span data-stu-id="42358-262">Backing fields are used by default</span></span>

[<span data-ttu-id="42358-263">問題 #12430 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-263">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="42358-264">この変更は、EF Core 3.0 プレビュー 2 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-264">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="42358-265">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-265">**Old behavior**</span></span>

<span data-ttu-id="42358-266">3.0 より前では、プロパティのバッキング フィールドがわかっていた場合でも、EF Core では引き続き、既定でプロパティの getter および setter メソッドを使用して、プロパティ値の読み取りと書き込みが行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-266">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="42358-267">ただし、バッキング フィールドが直接設定されることがわかっている場合のクエリの実行は例外です。</span><span class="sxs-lookup"><span data-stu-id="42358-267">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="42358-268">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-268">**New behavior**</span></span>

<span data-ttu-id="42358-269">EF Core 3.0 以降では、プロパティのバッキング フィールドがわかっている場合、バッキング フィールドを使用して、常にそのプロパティの読み取りと書き込みが行われます。</span><span class="sxs-lookup"><span data-stu-id="42358-269">Starting with EF Core 3.0, if the backing field for a property is known, then will always read and write that property using the backing field.</span></span>
<span data-ttu-id="42358-270">アプリケーションが getter および setter メソッドにコード化された追加動作に依存している場合、これによりアプリケーションが中断される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42358-270">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="42358-271">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-271">**Why**</span></span>

<span data-ttu-id="42358-272">この変更は、EF Core が、エンティティに関するデータベース操作の実行時に、既定でビジネス ロジックを誤ってトリガーしないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-272">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="42358-273">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-273">**Mitigations**</span></span>

<span data-ttu-id="42358-274">3.0 より前の動作は、modelBuilder fluent API でプロパティ アクセス モードの構成を使用して、復元できます。</span><span class="sxs-lookup"><span data-stu-id="42358-274">The pre-3.0 behavior can be restored through configuration of the property access mode in the modelBuilder fluent API.</span></span>
<span data-ttu-id="42358-275">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="42358-275">For example:</span></span>

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="42358-276">複数の互換性があるバッキング フィールドが見つかった場合にスローされる</span><span class="sxs-lookup"><span data-stu-id="42358-276">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="42358-277">問題 #12523 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-277">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="42358-278">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="42358-278">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="42358-279">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-279">**Old behavior**</span></span>

<span data-ttu-id="42358-280">EF Core 3.0 より前では、複数のフィールドが、プロパティのバッキング フィールドを見つけるための規則と一致した場合、何らかの優先順位に基づいて、1 つのフィールドが選択されました。</span><span class="sxs-lookup"><span data-stu-id="42358-280">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="42358-281">これにより、あいまいなケースで不適切なフィールドが使用される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="42358-281">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="42358-282">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-282">**New behavior**</span></span>

<span data-ttu-id="42358-283">EF Core 3.0 以降では、複数のフィールドが同じプロパティと一致した場合、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="42358-283">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="42358-284">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-284">**Why**</span></span>

<span data-ttu-id="42358-285">この変更は、1 つのフィールドのみが適切である可能性がある場合に、他のものより優先して暗黙的に 1 つのフィールドが使用されないようにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-285">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="42358-286">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-286">**Mitigations**</span></span>

<span data-ttu-id="42358-287">あいまいなバッキング フィールドがあるプロパティでは、使用するフィールドを明示的に指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="42358-287">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="42358-288">たとえば、fluent API を使用する場合、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="42358-288">For example, using the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="42358-289">AddDbContext/AddDbContextPool で AddLogging および AddMemoryCache を呼び出さなくなりました</span><span class="sxs-lookup"><span data-stu-id="42358-289">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="42358-290">問題 #14756 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-290">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="42358-291">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="42358-291">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="42358-292">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-292">**Old behavior**</span></span>

<span data-ttu-id="42358-293">EF Core 3.0 以前では、`AddDbContext` または `AddDbContextPool` を呼び出すと、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) および [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) への呼び出しを通じて、D.I を使ってログ記録とメモリ キャッシュ サービスも登録されました。</span><span class="sxs-lookup"><span data-stu-id="42358-293">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with D.I through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="42358-294">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-294">**New behavior**</span></span>

<span data-ttu-id="42358-295">EF Core 3.0 以降、`AddDbContext` と `AddDbContextPool` では、依存関係の挿入 (DI) を使ってこれらのサービスを登録しなくなります。</span><span class="sxs-lookup"><span data-stu-id="42358-295">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="42358-296">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-296">**Why**</span></span>

<span data-ttu-id="42358-297">EF Core 3.0 では、これらのサービスをアプリケーションの DI コンテナーに含める必要がありません。</span><span class="sxs-lookup"><span data-stu-id="42358-297">EF Core 3.0 does not require that these services are in the application's DI cotainer.</span></span> <span data-ttu-id="42358-298">ただし、`ILoggerFactory` がアプリケーションの DI コンテナーに登録された場合、それは引き続き EF Core によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="42358-298">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="42358-299">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-299">**Mitigations**</span></span>

<span data-ttu-id="42358-300">ご自身のアプリケーションでこれらのサービスが必要な場合は、[AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) または [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache) を使って、DI コンテナーで明示的にそれらを登録します。</span><span class="sxs-lookup"><span data-stu-id="42358-300">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="42358-301">DbContext.Entry でローカルの DetectChanges が実行されるようになった</span><span class="sxs-lookup"><span data-stu-id="42358-301">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="42358-302">問題 #13552 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-302">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="42358-303">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-303">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-304">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-304">**Old behavior**</span></span>

<span data-ttu-id="42358-305">EF Core 3.0 より前では、`DbContext.Entry` を呼び出すと、追跡対象のすべてのエンティティで変更が検出されました。</span><span class="sxs-lookup"><span data-stu-id="42358-305">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="42358-306">これにより、`EntityEntry` で示された状態が確実に最新のものとなりました。</span><span class="sxs-lookup"><span data-stu-id="42358-306">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="42358-307">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-307">**New behavior**</span></span>

<span data-ttu-id="42358-308">EF Core 3.0 以降では、`DbContext.Entry` の呼び出しで、特定のエンティティと、それに関連する追跡対象のプリンシパル エンティティでの変更のみの検出が試行されるようになります。</span><span class="sxs-lookup"><span data-stu-id="42358-308">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="42358-309">つまり、他の場所での変更は、このメソッドの呼び出しで検出されなかった可能性があり、アプリケーション状態に影響する場合があります。</span><span class="sxs-lookup"><span data-stu-id="42358-309">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="42358-310">`ChangeTracker.AutoDetectChangesEnabled` が `false` に設定されている場合、このローカル変更の検出も無効になることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="42358-310">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="42358-311">`ChangeTracker.Entries` や `SaveChanges` など、変更の検出の原因となる他のメソッドは引き続き、すべての追跡対象エンティティの完全な `DetectChanges` の原因となります。</span><span class="sxs-lookup"><span data-stu-id="42358-311">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="42358-312">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-312">**Why**</span></span>

<span data-ttu-id="42358-313">この変更は、`context.Entry` を使用する既定のパフォーマンスを向上させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-313">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="42358-314">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-314">**Mitigations**</span></span>

<span data-ttu-id="42358-315">3.0 より前の動作を確保するには、`Entry` を呼び出す前に明示的に `ChgangeTracker.DetectChanges()` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="42358-315">Call `ChgangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="42358-316">文字列とバイト配列のキーが既定でクライアントによって生成されない</span><span class="sxs-lookup"><span data-stu-id="42358-316">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="42358-317">問題 #14617 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-317">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="42358-318">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="42358-318">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="42358-319">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-319">**Old behavior**</span></span>

<span data-ttu-id="42358-320">EF Core 3.0 より前では、`string` と `byte[]` のキー プロパティは、null 以外の値を明示的に設定しなくても使用できました。</span><span class="sxs-lookup"><span data-stu-id="42358-320">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="42358-321">このような場合、キーの値は GUID としてクライアントで生成され、`byte[]` のバイトにシリアル化されます。</span><span class="sxs-lookup"><span data-stu-id="42358-321">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="42358-322">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-322">**New behavior**</span></span>

<span data-ttu-id="42358-323">EF Core 3.0 以降では、キー値が設定されていないことを示す例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="42358-323">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="42358-324">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-324">**Why**</span></span>

<span data-ttu-id="42358-325">この変更は、クライアントで生成された `string`/`byte[]` 値が一般的に役に立たず、既定の動作では一般的な方法で生成されたキー値について判断するのが難しくなったため、行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-325">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="42358-326">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-326">**Mitigations**</span></span>

<span data-ttu-id="42358-327">3.0 より前の動作は、他の null 以外の値が設定されていない場合に、キー プロパティで生成された値を使用するように明示的に指定することで取得できます。</span><span class="sxs-lookup"><span data-stu-id="42358-327">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="42358-328">たとえば、fluent API を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="42358-328">For example, with the fluent API:</span></span>

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="42358-329">データ注釈を使用する場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="42358-329">Or with data annotations:</span></span>

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="42358-330">ILoggerFactory がスコープ サービスになった</span><span class="sxs-lookup"><span data-stu-id="42358-330">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="42358-331">問題 #14698 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-331">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="42358-332">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-332">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-333">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-333">**Old behavior**</span></span>

<span data-ttu-id="42358-334">EF Core 3.0 より前では、`ILoggerFactory` はシングルトン サービスとして登録されました。</span><span class="sxs-lookup"><span data-stu-id="42358-334">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="42358-335">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-335">**New behavior**</span></span>

<span data-ttu-id="42358-336">EF Core 3.0 以降では、`ILoggerFactory` がスコープ化されたものとして登録されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="42358-336">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="42358-337">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-337">**Why**</span></span>

<span data-ttu-id="42358-338">この変更は、`DbContext` インスタンスでのロガーの関連付けを許可し、他の機能を有効にし、内部サービス プロバイダーの急増などの異常な動作の一部のケースをなくすために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-338">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="42358-339">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-339">**Mitigations**</span></span>

<span data-ttu-id="42358-340">この変更は、EF Core の内部サービス プロバイダーでカスタム サービスを登録して使用しない限り、アプリケーション コードに影響しないはずです。</span><span class="sxs-lookup"><span data-stu-id="42358-340">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="42358-341">これは一般的なことではありません。</span><span class="sxs-lookup"><span data-stu-id="42358-341">This isn't common.</span></span>
<span data-ttu-id="42358-342">このような場合、ほとんどのものが引き続き機能しますが、`ILoggerFactory` に依存していたシングルトン サービスは、異なる方法で `ILoggerFactory` を取得するように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="42358-342">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="42358-343">このような状況になった場合は、[EF Core GitHub の問題追跡ツール](https://github.com/aspnet/EntityFrameworkCore/issues)で問題を提出し、`ILoggerFactory` の使用方法をお知らせください。これにより、Microsoft では、今後、再びこのように中断しない方法についてよりよく理解できます。</span><span class="sxs-lookup"><span data-stu-id="42358-343">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a><span data-ttu-id="42358-344">IDbContextOptionsExtension に IDbContextOptionsExtensionWithDebugInfo が結合される</span><span class="sxs-lookup"><span data-stu-id="42358-344">IDbContextOptionsExtensionWithDebugInfo merged into IDbContextOptionsExtension</span></span>

[<span data-ttu-id="42358-345">問題 #13552 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-345">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="42358-346">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-346">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-347">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-347">**Old behavior**</span></span>

<span data-ttu-id="42358-348">`IDbContextOptionsExtensionWithDebugInfo` は、2.x リリース サイクル時のインターフェイスへの破壊的変更を回避するために、`IDbContextOptionsExtension` から拡張された追加のオプションのインターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="42358-348">`IDbContextOptionsExtensionWithDebugInfo` was an additional optional interface extended from `IDbContextOptionsExtension` to avoid making a breaking change to the interface during the 2.x release cycle.</span></span>

<span data-ttu-id="42358-349">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-349">**New behavior**</span></span>

<span data-ttu-id="42358-350">インターフェイスはまとめて `IDbContextOptionsExtension` に結合されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="42358-350">The interfaces are now merged together into `IDbContextOptionsExtension`.</span></span>

<span data-ttu-id="42358-351">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-351">**Why**</span></span>

<span data-ttu-id="42358-352">この変更は、インターフェイスが概念的には 1 つであるため、行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-352">This change was made because the interfaces are conceptually one.</span></span>

<span data-ttu-id="42358-353">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-353">**Mitigations**</span></span>

<span data-ttu-id="42358-354">`IDbContextOptionsExtension` のすべての実装を、新しいメンバーをサポートするように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="42358-354">Any implementations of `IDbContextOptionsExtension` will need to be updated to support the new member.</span></span>

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="42358-355">遅延読み込みプロキシで、ナビゲーション プロパティが完全に読み込まれたと見なされなくなった</span><span class="sxs-lookup"><span data-stu-id="42358-355">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="42358-356">問題 #12780 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-356">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="42358-357">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="42358-357">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="42358-358">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-358">**Old behavior**</span></span>

<span data-ttu-id="42358-359">EF Core 3.0 より前では、`DbContext` が破棄されると、そのコンテキストから取得されたエンティティの特定のナビゲーション プロパティが完全に読み込まれたかどうかを知る方法はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="42358-359">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="42358-360">プロキシでは、代わりに、null 以外の値がある場合は参照ナビゲーションが読み込まれ、空でない場合はコレクション ナビゲーションが読み込まれたと見なされます。</span><span class="sxs-lookup"><span data-stu-id="42358-360">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="42358-361">このような場合、遅延読み込みを試みても操作なしとなります。</span><span class="sxs-lookup"><span data-stu-id="42358-361">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="42358-362">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-362">**New behavior**</span></span>

<span data-ttu-id="42358-363">EF Core 3.0 以降では、プロキシで、ナビゲーション プロパティが読み込まれたかどうかの追跡が行われます。</span><span class="sxs-lookup"><span data-stu-id="42358-363">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="42358-364">つまり、コンテキストが破棄された後に読み込まれたナビゲーション プロパティにアクセスしようとしても、通常は操作なしとなります。読み込まれたナビゲーションが空でも null であっても同様です。</span><span class="sxs-lookup"><span data-stu-id="42358-364">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="42358-365">逆に、コンテキストが破棄された場合、読み込まれなかったナビゲーション プロパティにアクセスしようとすると例外がスローされます。ナビゲーション プロパティが空ではないコレクションである場合でも同様です。</span><span class="sxs-lookup"><span data-stu-id="42358-365">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="42358-366">このような状況が発生した場合、アプリケーション コードで無効な時間に遅延読み込みの使用が試行されていることを意味し、アプリケーションを変更して、このようにならないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="42358-366">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="42358-367">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-367">**Why**</span></span>

<span data-ttu-id="42358-368">この変更は、破棄された `DbContext` インスタンスで遅延読み込みが試行されたときの動作を一貫した正しいものにするために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-368">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="42358-369">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-369">**Mitigations**</span></span>

<span data-ttu-id="42358-370">破棄されたコンテキストで遅延読み込みが試行されないようにアプリケーション コードを更新するか、例外メッセージの説明に従って、これを操作なしとなるように構成します。</span><span class="sxs-lookup"><span data-stu-id="42358-370">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="42358-371">内部サービス プロバイダーの過剰な作成が既定でエラーと見なされるようになった</span><span class="sxs-lookup"><span data-stu-id="42358-371">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="42358-372">問題 #10236 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-372">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="42358-373">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-373">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-374">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-374">**Old behavior**</span></span>

<span data-ttu-id="42358-375">EF Core 3.0 より前では、異常な数の内部サービス プロバイダーを作成するアプリケーションに対する警告がログに記録されました。</span><span class="sxs-lookup"><span data-stu-id="42358-375">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="42358-376">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-376">**New behavior**</span></span>

<span data-ttu-id="42358-377">EF Core 3.0 以降では、この警告はエラーと見なされるようになり、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="42358-377">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="42358-378">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-378">**Why**</span></span>

<span data-ttu-id="42358-379">この変更は、この異常なケースをより明示的に示すことで、アプリケーション コードの改善を促進するために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-379">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="42358-380">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-380">**Mitigations**</span></span>

<span data-ttu-id="42358-381">このエラーの発生時の最も適切なアクションは、根本原因を理解し、非常に多くの内部サービス プロバイダーを作成しないようにすることです。</span><span class="sxs-lookup"><span data-stu-id="42358-381">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="42358-382">しかし、`DbContextOptionsBuilder` での構成を使用して、エラーを警告に戻す (または無視する) ことがことができます。</span><span class="sxs-lookup"><span data-stu-id="42358-382">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="42358-383">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="42358-383">For example:</span></span>

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="42358-384">1 つの文字列と共に呼び出される HasOne/HasMany の新しい動作</span><span class="sxs-lookup"><span data-stu-id="42358-384">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="42358-385">問題 #9171 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-385">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="42358-386">この変更は、EF Core 3.0 プレビュー 4 で導入されます。</span><span class="sxs-lookup"><span data-stu-id="42358-386">This change will be introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="42358-387">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-387">**Old behavior**</span></span>

<span data-ttu-id="42358-388">EF Core 3.0 以前では、1 つの文字列と共に `HasOne` または `HasMany` を呼び出すコードは、わかりにくい方法で解釈されていました。</span><span class="sxs-lookup"><span data-stu-id="42358-388">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpretted in a confusing way.</span></span>
<span data-ttu-id="42358-389">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="42358-389">For example:</span></span>
```C#
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="42358-390">このコードは、プライベートである可能性がある `Entrance` ナビゲーション プロパティを使って、`Samurai` を他のエンティティ型に関連付けているように見えます。</span><span class="sxs-lookup"><span data-stu-id="42358-390">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="42358-391">実際には、このコードは、ナビゲーション プロパティなしで、`Entrance` と呼ばれるエンティティ型に対してリレーションシップを作成しようとしています。</span><span class="sxs-lookup"><span data-stu-id="42358-391">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="42358-392">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-392">**New behavior**</span></span>

<span data-ttu-id="42358-393">EF Core 3.0 以降では、上記のコードは、前にそれが実行すべきであるように見えたことを実行するようになりました。</span><span class="sxs-lookup"><span data-stu-id="42358-393">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="42358-394">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-394">**Why**</span></span>

<span data-ttu-id="42358-395">以前の動作は、特に構成コードを読み取ってエラーを探す場合は、非常にわかりにくいものでした。</span><span class="sxs-lookup"><span data-stu-id="42358-395">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="42358-396">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-396">**Mitigations**</span></span>

<span data-ttu-id="42358-397">これが中断させるのは、型名の文字列を使って明示的にリレーションシップを構成し、かつ明示的にナビゲーション プロパティを指定しないアプリケーションのみです。</span><span class="sxs-lookup"><span data-stu-id="42358-397">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="42358-398">これは一般的ではありません。</span><span class="sxs-lookup"><span data-stu-id="42358-398">This is not common.</span></span>
<span data-ttu-id="42358-399">前の動作は、ナビゲーション プロパティ名に対して明示的に `null` を渡すことで実現できます。</span><span class="sxs-lookup"><span data-stu-id="42358-399">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="42358-400">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="42358-400">For example:</span></span>

```C#
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="42358-401">Relational:TypeMapping 注釈が単に TypeMapping となった</span><span class="sxs-lookup"><span data-stu-id="42358-401">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="42358-402">問題 #9913 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-402">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="42358-403">この変更は、EF Core 3.0 プレビュー 2 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-403">This change was introduced in EF Core 3.0-preview 2.</span></span>

<span data-ttu-id="42358-404">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-404">**Old behavior**</span></span>

<span data-ttu-id="42358-405">型マッピング注釈の注釈名は "Relational:TypeMapping" でした。</span><span class="sxs-lookup"><span data-stu-id="42358-405">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="42358-406">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-406">**New behavior**</span></span>

<span data-ttu-id="42358-407">型マッピング注釈の注釈名は、"TypeMapping" となりました。</span><span class="sxs-lookup"><span data-stu-id="42358-407">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="42358-408">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-408">**Why**</span></span>

<span data-ttu-id="42358-409">型マッピングが、リレーショナル データベース プロバイダー以外でも使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="42358-409">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="42358-410">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-410">**Mitigations**</span></span>

<span data-ttu-id="42358-411">これにより、一般的ではない、注釈として直接型マッピングにアクセスするアプリケーションのみが中断されるようになります。</span><span class="sxs-lookup"><span data-stu-id="42358-411">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="42358-412">問題を解決するための最も適切なアクションは、注釈を直接使用するのではなく、API サーフェスを使用して型マッピングにアクセスすることです。</span><span class="sxs-lookup"><span data-stu-id="42358-412">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

## <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="42358-413">派生型の ToTable で例外がスローされる</span><span class="sxs-lookup"><span data-stu-id="42358-413">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="42358-414">問題 #11811 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-414">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="42358-415">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-415">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-416">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-416">**Old behavior**</span></span>

<span data-ttu-id="42358-417">EF Core 3.0 より前では、唯一の継承マッピング方法が TPH であり、これが有効でない場合には、派生型で呼び出された `ToTable()` が無視されていました。</span><span class="sxs-lookup"><span data-stu-id="42358-417">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="42358-418">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-418">**New behavior**</span></span>

<span data-ttu-id="42358-419">EF Core 3.0 以降、および今後のリリースでの TPT と TPC のサポートの追加準備中には、派生型で呼び出された `ToTable()` で、今後の予期されないマッピング変更を回避するために例外がスローされるようになります。</span><span class="sxs-lookup"><span data-stu-id="42358-419">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="42358-420">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-420">**Why**</span></span>

<span data-ttu-id="42358-421">現在、派生型を別のテーブルにマップすることは有効ではありません。</span><span class="sxs-lookup"><span data-stu-id="42358-421">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="42358-422">この変更により、今後、これを行うことが有効になった場合に中断されなくなります。</span><span class="sxs-lookup"><span data-stu-id="42358-422">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="42358-423">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-423">**Mitigations**</span></span>

<span data-ttu-id="42358-424">派生型を他のテーブルにマップしないようにします。</span><span class="sxs-lookup"><span data-stu-id="42358-424">Remove any attempts to map derived types to other tables.</span></span>

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="42358-425">ForSqlServerHasIndex が HasIndex に置き換えられた</span><span class="sxs-lookup"><span data-stu-id="42358-425">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="42358-426">問題 #12366 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-426">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="42358-427">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-427">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-428">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-428">**Old behavior**</span></span>

<span data-ttu-id="42358-429">EF Core 3.0 より前では、`ForSqlServerHasIndex().ForSqlServerInclude()` によって、`INCLUDE` で使用される列を構成する方法が提供されました。</span><span class="sxs-lookup"><span data-stu-id="42358-429">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="42358-430">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-430">**New behavior**</span></span>

<span data-ttu-id="42358-431">EF Core 3.0 以降では、インデックスでの `Include` の使用が、リレーショナル レベルでサポートされるようになりました。</span><span class="sxs-lookup"><span data-stu-id="42358-431">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="42358-432">`HasIndex().ForSqlServerInclude()` を使用してください。</span><span class="sxs-lookup"><span data-stu-id="42358-432">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="42358-433">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-433">**Why**</span></span>

<span data-ttu-id="42358-434">この変更は、すべてのデータ プロバイダーのために `Includes` でインデックス用の API を 1 か所に統合するために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-434">This change was made to consolidate the API for indexes with `Includes` into one place for all database providers.</span></span>

<span data-ttu-id="42358-435">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-435">**Mitigations**</span></span>

<span data-ttu-id="42358-436">上記のように、新しい API を使用します。</span><span class="sxs-lookup"><span data-stu-id="42358-436">Use the new API, as shown above.</span></span>

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="42358-437">EF Core で SQLite FK を適用するためのプラグマが送信されなくなった</span><span class="sxs-lookup"><span data-stu-id="42358-437">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="42358-438">問題 #12151 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-438">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="42358-439">この変更は、EF Core 3.0 プレビュー 3 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-439">This change was introduced in EF Core 3.0-preview 3.</span></span>

<span data-ttu-id="42358-440">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-440">**Old behavior**</span></span>

<span data-ttu-id="42358-441">EF Core 3.0 より前では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されました。</span><span class="sxs-lookup"><span data-stu-id="42358-441">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="42358-442">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-442">**New behavior**</span></span>

<span data-ttu-id="42358-443">EF Core 3.0 以降では、EF Core で、SQLite への接続が開かれたときに `PRAGMA foreign_keys = 1` が送信されなくなりました。</span><span class="sxs-lookup"><span data-stu-id="42358-443">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="42358-444">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-444">**Why**</span></span>

<span data-ttu-id="42358-445">この変更は、既定で EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用され、その後、FK の適用が既定で有効になり、接続が開かれるたびに明示的に有効にする必要がないため、行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-445">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="42358-446">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-446">**Mitigations**</span></span>

<span data-ttu-id="42358-447">外部キーは、EF Core に対して既定で使用される、SQLitePCLRaw.bundle_e_sqlite3 で既定で有効になります。</span><span class="sxs-lookup"><span data-stu-id="42358-447">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="42358-448">それ以外の場合は、接続文字列で `Foreign Keys=True` を指定することで、外部キーを有効にできます。</span><span class="sxs-lookup"><span data-stu-id="42358-448">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a><span data-ttu-id="42358-449">Microsoft.EntityFrameworkCore.Sqlite が SQLitePCLRaw.bundle_e_sqlite3 に依存するようになった</span><span class="sxs-lookup"><span data-stu-id="42358-449">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="42358-450">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-450">**Old behavior**</span></span>

<span data-ttu-id="42358-451">EF Core 3.0 より前では、EF Core では `SQLitePCLRaw.bundle_green` が使用されていました。</span><span class="sxs-lookup"><span data-stu-id="42358-451">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="42358-452">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-452">**New behavior**</span></span>

<span data-ttu-id="42358-453">EF Core 3.0 以降では、EF Core で `SQLitePCLRaw.bundle_e_sqlite3` が使用されます。</span><span class="sxs-lookup"><span data-stu-id="42358-453">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="42358-454">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-454">**Why**</span></span>

<span data-ttu-id="42358-455">この変更は、iOS 上で使用される SQLite のバージョンを、他のプラットフォームと一致させるために行われました。</span><span class="sxs-lookup"><span data-stu-id="42358-455">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="42358-456">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-456">**Mitigations**</span></span>

<span data-ttu-id="42358-457">iOS でネイティブの SQLite バージョンを使用するには、別の `SQLitePCLRaw` バンドルを使用するように `Microsoft.Data.Sqlite` を構成します。</span><span class="sxs-lookup"><span data-stu-id="42358-457">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

## <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="42358-458">Char の値が SQLite にテキストとして格納されるようになった</span><span class="sxs-lookup"><span data-stu-id="42358-458">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="42358-459">問題 #15020 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-459">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="42358-460">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-460">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="42358-461">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-461">**Old behavior**</span></span>

<span data-ttu-id="42358-462">Char の値は、以前は SQLite に整数値として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="42358-462">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="42358-463">たとえば、Char の値 *A* は整数値 65 として格納されていました。</span><span class="sxs-lookup"><span data-stu-id="42358-463">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="42358-464">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-464">**New behavior**</span></span>

<span data-ttu-id="42358-465">Char の値はテキストとして格納されます。</span><span class="sxs-lookup"><span data-stu-id="42358-465">Char values are now sotred as TEXT.</span></span>

<span data-ttu-id="42358-466">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-466">**Why**</span></span>

<span data-ttu-id="42358-467">値をテキストとして格納する方が自然であり、データベースと他のテクノロジとの互換性が高まるためです。</span><span class="sxs-lookup"><span data-stu-id="42358-467">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="42358-468">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-468">**Mitigations**</span></span>

<span data-ttu-id="42358-469">次のように SQL を実行すると、既存のデータベースを新しい形式に移行することができます。</span><span class="sxs-lookup"><span data-stu-id="42358-469">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="42358-470">EF Core では、このようなプロパティで値コンバーターを構成することで、以前の動作を使い続けることもできます。</span><span class="sxs-lookup"><span data-stu-id="42358-470">In EF Core, you could also continue using the previous behavior by configuirng a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="42358-471">Microsoft.Data.Sqlite では、引き続き整数とテキストの両方の列の文字列値を読み取ることができるため、一部のシナリオではアクションが必要ありません。</span><span class="sxs-lookup"><span data-stu-id="42358-471">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

## <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="42358-472">移行 ID がインバリアント カルチャの暦を使用して生成されるようになった</span><span class="sxs-lookup"><span data-stu-id="42358-472">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="42358-473">問題 #12978 の追跡</span><span class="sxs-lookup"><span data-stu-id="42358-473">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="42358-474">この変更は、EF Core 3.0 プレビュー 4 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="42358-474">This change was introduced in EF Core 3.0-preview 4.</span></span>

<span data-ttu-id="42358-475">**以前の動作**</span><span class="sxs-lookup"><span data-stu-id="42358-475">**Old behavior**</span></span>

<span data-ttu-id="42358-476">移行 ID は選択されているカルチャの暦を使用して意図なく生成されていました。</span><span class="sxs-lookup"><span data-stu-id="42358-476">Migration IDs were inadvertantly generated using the currret culture's calendar.</span></span>

<span data-ttu-id="42358-477">**新しい動作**</span><span class="sxs-lookup"><span data-stu-id="42358-477">**New behavior**</span></span>

<span data-ttu-id="42358-478">移行 ID がインバリアント カルチャの暦 (グレゴリオ暦) を使用して常に生成されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="42358-478">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="42358-479">**理由**</span><span class="sxs-lookup"><span data-stu-id="42358-479">**Why**</span></span>

<span data-ttu-id="42358-480">データベースを更新するときやマージの競合を解決するときに、移行の順序は重要です。</span><span class="sxs-lookup"><span data-stu-id="42358-480">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="42358-481">インバリアントの暦を使用することで、順序の問題が回避され、システムの予定表がチームのメンバーごとに違うことがなくなります。</span><span class="sxs-lookup"><span data-stu-id="42358-481">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="42358-482">**軽減策**</span><span class="sxs-lookup"><span data-stu-id="42358-482">**Mitigations**</span></span>

<span data-ttu-id="42358-483">この変更は、グレゴリオ暦以外の暦 (タイ仏暦など) を使用し、1 年がグレゴリオ暦より長くなるすべてのユーザーに影響します。</span><span class="sxs-lookup"><span data-stu-id="42358-483">This change affects anyone using a non-Gregorian calender where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="42358-484">既存の移行後に新しい移行順序が設定されるように、既存の移行 ID を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="42358-484">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="42358-485">移行 ID は、移行のデザイナー ファイルの移行属性にあります。</span><span class="sxs-lookup"><span data-stu-id="42358-485">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="42358-486">移行履歴テーブルも更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="42358-486">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```
