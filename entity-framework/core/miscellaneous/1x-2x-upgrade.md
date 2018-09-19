---
title: 以前のバージョンから EF Core 2 - EF Core へのアップグレード
author: divega
ms.date: 08/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: 5371c8f3b7c6102c621296bbae145d13779e0c6e
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283772"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a><span data-ttu-id="33a9e-102">以前のバージョンから EF Core 2.0 へのアプリケーションのアップグレード</span><span class="sxs-lookup"><span data-stu-id="33a9e-102">Upgrading applications from previous versions to EF Core 2.0</span></span>

<span data-ttu-id="33a9e-103">既存の Api と 2.0 の動作を大幅に絞り込むことを思い出させています。</span><span class="sxs-lookup"><span data-stu-id="33a9e-103">We have taken the opportunity to significantly refine our existing APIs and behaviors in 2.0.</span></span> <span data-ttu-id="33a9e-104">既存のアプリケーション コードを変更することが必要になるいくつかの機能強化への影響が再コンパイルおよび廃止された Api を置換する最小限のガイド付きの変更だけを必要とするほとんどの場合は低くなりますが、大半のアプリケーションと考えています。</span><span class="sxs-lookup"><span data-stu-id="33a9e-104">There are a few improvements that can require modifying existing application code, although we believe that for the majority of applications the impact will be low, in most cases requiring just recompilation and minimal guided changes to replace obsolete APIs.</span></span>

<span data-ttu-id="33a9e-105">既存のアプリケーションを EF Core 2.0 を更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33a9e-105">Updating an existing application to EF Core 2.0 may require:</span></span>

1. <span data-ttu-id="33a9e-106">.NET Standard 2.0 をサポートしているアプリケーションのターゲットの .NET 実装をアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="33a9e-106">Upgrading the target .NET implementation of the application to one that supports .NET Standard 2.0.</span></span> <span data-ttu-id="33a9e-107">参照してください[サポートされている .NET 実装](../platforms/index.md)の詳細。</span><span class="sxs-lookup"><span data-stu-id="33a9e-107">See [Supported .NET Implementations](../platforms/index.md) for more details.</span></span>

2. <span data-ttu-id="33a9e-108">EF Core 2.0 と互換性がターゲット データベースのプロバイダーを識別します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-108">Identify a provider for the target database which is compatible with EF Core 2.0.</span></span> <span data-ttu-id="33a9e-109">参照してください[EF Core 2.0 には、2.0 データベース プロバイダーが必要があります](#ef-core-20-requires-a-20-database-provider)以下。</span><span class="sxs-lookup"><span data-stu-id="33a9e-109">See [EF Core 2.0 requires a 2.0 database provider](#ef-core-20-requires-a-20-database-provider) below.</span></span>

3. <span data-ttu-id="33a9e-110">すべての EF Core パッケージ (ランタイムおよびツール) を 2.0 にアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="33a9e-110">Upgrading all the EF Core packages (runtime and tooling) to 2.0.</span></span> <span data-ttu-id="33a9e-111">参照してください[EF Core のインストール](../get-started/install/index.md)の詳細。</span><span class="sxs-lookup"><span data-stu-id="33a9e-111">Refer to [Installing EF Core](../get-started/install/index.md) for more details.</span></span>

4. <span data-ttu-id="33a9e-112">このドキュメントの残りの部分で説明されている重大な変更を補正するために必要なコード変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="33a9e-112">Make any necessary code changes to compensate for the breaking changes described in the rest of this document.</span></span>

## <a name="aspnet-core-now-includes-ef-core"></a><span data-ttu-id="33a9e-113">ASP.NET Core が EF Core が含まれています</span><span class="sxs-lookup"><span data-stu-id="33a9e-113">ASP.NET Core now includes EF Core</span></span>

<span data-ttu-id="33a9e-114">ASP.NET Core 2.0 を対象とするアプリケーションは、サードパーティ データベース プロバイダー以外の依存関係を追加せずに EF Core 2.0 を使用できます。</span><span class="sxs-lookup"><span data-stu-id="33a9e-114">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="33a9e-115">ただし、以前のバージョンの ASP.NET Core を対象とするアプリケーションは、EF Core 2.0 を使用するには、ASP.NET Core 2.0 にアップグレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="33a9e-115">However, applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span> <span data-ttu-id="33a9e-116">ASP.NET Core アプリケーションを 2.0 にアップグレードする方法の詳細を参照してください[サブジェクトで ASP.NET Core ドキュメント](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/)します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-116">For more details on upgrading ASP.NET Core applications to 2.0 see [the ASP.NET Core documentation on the subject](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/).</span></span>

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a><span data-ttu-id="33a9e-117">ASP.NET Core でのアプリケーション サービスを取得する新しい方法</span><span class="sxs-lookup"><span data-stu-id="33a9e-117">New way of getting application services in ASP.NET Core</span></span>

<span data-ttu-id="33a9e-118">EF Core が 1.x で使用されるデザイン時ロジックを無効にした方法で 2.0 の ASP.NET Core web アプリケーションの推奨されるパターンが更新されました。</span><span class="sxs-lookup"><span data-stu-id="33a9e-118">The recommended pattern for ASP.NET Core web applications has been updated for 2.0 in a way that broke the design-time logic EF Core used in 1.x.</span></span> <span data-ttu-id="33a9e-119">以前のデザイン時に、EF Core は呼び出しを試みる`Startup.ConfigureServices`アプリケーションのサービス プロバイダーにアクセスするために直接します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-119">Previously at design-time, EF Core would try to invoke `Startup.ConfigureServices` directly in order to access the application's service provider.</span></span> <span data-ttu-id="33a9e-120">外部で ASP.NET Core 2.0 では、構成が初期化されて、`Startup`クラス。</span><span class="sxs-lookup"><span data-stu-id="33a9e-120">In ASP.NET Core 2.0, Configuration is initialized outside of the `Startup` class.</span></span> <span data-ttu-id="33a9e-121">通常、EF Core を使用してアプリケーションへのアクセス、接続文字列構成からため`Startup`自体では十分なできなくします。</span><span class="sxs-lookup"><span data-stu-id="33a9e-121">Applications using EF Core typically access their connection string from Configuration, so `Startup` by itself is no longer sufficient.</span></span> <span data-ttu-id="33a9e-122">ASP.NET Core 1.x アプリケーションをアップグレードする場合は、EF Core ツールを使用する場合、次のエラーを受信可能性があります。</span><span class="sxs-lookup"><span data-stu-id="33a9e-122">If you upgrade an ASP.NET Core 1.x application, you may receive the following error when using the EF Core tools.</span></span>

> <span data-ttu-id="33a9e-123">'ApplicationContext' で、パラメーターなしのコンス トラクターが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="33a9e-123">No parameterless constructor was found on 'ApplicationContext'.</span></span> <span data-ttu-id="33a9e-124">パラメーターなしのコンス トラクターを 'ApplicationContext' に追加するか、追加の実装 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' 'ApplicationContext' と同じアセンブリ内</span><span class="sxs-lookup"><span data-stu-id="33a9e-124">Either add a parameterless constructor to 'ApplicationContext' or add an implementation of 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' in the same assembly as 'ApplicationContext'</span></span>

<span data-ttu-id="33a9e-125">ASP.NET Core 2.0 の既定のテンプレートで、新しいデザイン時フックが追加されました。</span><span class="sxs-lookup"><span data-stu-id="33a9e-125">A new design-time hook has been added in ASP.NET Core 2.0's default template.</span></span> <span data-ttu-id="33a9e-126">静的な`Program.BuildWebHost`メソッドは、デザイン時に、アプリケーションのサービス プロバイダーへのアクセスに EF Core を使用できます。</span><span class="sxs-lookup"><span data-stu-id="33a9e-126">The static `Program.BuildWebHost` method enables EF Core to access the application's service provider at design time.</span></span> <span data-ttu-id="33a9e-127">ASP.NET Core 1.x アプリケーションをアップグレードする場合は、更新する必要があります。、`Program`クラスを、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="33a9e-127">If you are upgrading an ASP.NET Core 1.x application, you will need to update the `Program` class to resemble the following.</span></span>

``` csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace AspNetCoreDotNetCore2._0App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

<span data-ttu-id="33a9e-128">2.0 アプリケーションの更新は強く推奨され、動作する Entity Framework Core Migrations のような製品機能のために必要がときにこの新しいパターンの導入します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-128">The adoption of this new pattern when updating applications to 2.0 is highly recommended and is required in order for product features like Entity Framework Core Migrations to work.</span></span> <span data-ttu-id="33a9e-129">その他の一般的な方法が[実装*IDesignTimeDbContextFactory\<TContext >*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-129">The other common alternative is to [implement *IDesignTimeDbContextFactory\<TContext>*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).</span></span>

## <a name="idbcontextfactory-renamed"></a><span data-ttu-id="33a9e-130">IDbContextFactory 名前変更</span><span class="sxs-lookup"><span data-stu-id="33a9e-130">IDbContextFactory renamed</span></span>

<span data-ttu-id="33a9e-131">多様なアプリケーション パターンをサポートし、ユーザーを細かく制御できるようにするために、`DbContext`使用は、デザイン時があるまで、提供される、`IDbContextFactory<TContext>`インターフェイス。</span><span class="sxs-lookup"><span data-stu-id="33a9e-131">In order to support diverse application patterns and give users more control over how their `DbContext` is used at design time, we have, in the past, provided the `IDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="33a9e-132">デザイン時に、EF Core ツールは、プロジェクトでは、このインターフェイスの実装が検出して使用を作成する`DbContext`オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="33a9e-132">At design-time, the EF Core tools will discover implementations of this interface in your project and use it to create `DbContext` objects.</span></span>

<span data-ttu-id="33a9e-133">このインターフェイスが非常に一般的な名前を試す再を使用してその他のいくつかのユーザーを困惑させる`DbContext`のシナリオを作成します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-133">This interface had a very general name which mislead some users to try re-using it for other `DbContext`-creating scenarios.</span></span> <span data-ttu-id="33a9e-134">EF ツールは、デザイン時にその実装を使用する試みられたときに不意をつかれた状態されが発生したようにコマンドが`Update-Database`または`dotnet ef database update`失敗します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-134">They were caught off guard when the EF Tools then tried to use their implementation at design-time and caused commands like `Update-Database` or `dotnet ef database update` to fail.</span></span>

<span data-ttu-id="33a9e-135">このインターフェイスの強力なデザイン時のセマンティクスを伝達するために変更しましたに`IDesignTimeDbContextFactory<TContext>`します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-135">In order to communicate the strong design-time semantics of this interface, we have renamed it to `IDesignTimeDbContextFactory<TContext>`.</span></span>

<span data-ttu-id="33a9e-136">2.0 リリース、`IDbContextFactory<TContext>`も存在しますが、不使用とマークされます。</span><span class="sxs-lookup"><span data-stu-id="33a9e-136">For the 2.0 release the `IDbContextFactory<TContext>` still exists but is marked as obsolete.</span></span>

## <a name="dbcontextfactoryoptions-removed"></a><span data-ttu-id="33a9e-137">DbContextFactoryOptions の削除</span><span class="sxs-lookup"><span data-stu-id="33a9e-137">DbContextFactoryOptions removed</span></span>

<span data-ttu-id="33a9e-138">変更のため、ASP.NET Core 2.0 上で説明した、ことがわかりました`DbContextFactoryOptions`が不要になった新しい`IDesignTimeDbContextFactory<TContext>`インターフェイス。</span><span class="sxs-lookup"><span data-stu-id="33a9e-138">Because of the ASP.NET Core 2.0 changes described above, we found that `DbContextFactoryOptions` was no longer needed on the new `IDesignTimeDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="33a9e-139">ここでは、代替の代わりに使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33a9e-139">Here are the alternatives you should be using instead.</span></span>

| <span data-ttu-id="33a9e-140">DbContextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="33a9e-140">DbContextFactoryOptions</span></span> | <span data-ttu-id="33a9e-141">代替</span><span class="sxs-lookup"><span data-stu-id="33a9e-141">Alternative</span></span>                                                  |
|:------------------------|:-------------------------------------------------------------|
| <span data-ttu-id="33a9e-142">ApplicationBasePath</span><span class="sxs-lookup"><span data-stu-id="33a9e-142">ApplicationBasePath</span></span>     | <span data-ttu-id="33a9e-143">AppContext.BaseDirectory</span><span class="sxs-lookup"><span data-stu-id="33a9e-143">AppContext.BaseDirectory</span></span>                                     |
| <span data-ttu-id="33a9e-144">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="33a9e-144">ContentRootPath</span></span>         | <span data-ttu-id="33a9e-145">Directory.GetCurrentDirectory()</span><span class="sxs-lookup"><span data-stu-id="33a9e-145">Directory.GetCurrentDirectory()</span></span>                              |
| <span data-ttu-id="33a9e-146">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="33a9e-146">EnvironmentName</span></span>         | <span data-ttu-id="33a9e-147">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span><span class="sxs-lookup"><span data-stu-id="33a9e-147">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span></span> |

## <a name="design-time-working-directory-changed"></a><span data-ttu-id="33a9e-148">デザイン時の作業ディレクトリが変更されました</span><span class="sxs-lookup"><span data-stu-id="33a9e-148">Design-time working directory changed</span></span>

<span data-ttu-id="33a9e-149">使用される作業ディレクトリにも必要な ASP.NET Core 2.0 変更`dotnet ef`に合うよう、アプリケーションを実行するときに、Visual Studio で使用される作業ディレクトリにします。</span><span class="sxs-lookup"><span data-stu-id="33a9e-149">The ASP.NET Core 2.0 changes also required the working directory used by `dotnet ef` to align with the working directory used by Visual Studio when running your application.</span></span> <span data-ttu-id="33a9e-150">この監視可能な副作用 1 つは、ファイル名はプロジェクト ディレクトリと出力ディレクトリではなく今すぐするために使用するようにその SQLite です。</span><span class="sxs-lookup"><span data-stu-id="33a9e-150">One observable side effect of this is that SQLite filenames are now relative to the project directory and not the output directory like they used to be.</span></span>

## <a name="ef-core-20-requires-a-20-database-provider"></a><span data-ttu-id="33a9e-151">EF Core 2.0 には、2.0 データベース プロバイダーが必要です。</span><span class="sxs-lookup"><span data-stu-id="33a9e-151">EF Core 2.0 requires a 2.0 database provider</span></span>

<span data-ttu-id="33a9e-152">EF Core 2.0 には、多くの簡略化を行い、データベース プロバイダーの方法で機能強化を行いました。</span><span class="sxs-lookup"><span data-stu-id="33a9e-152">For EF Core 2.0 we have made many simplifications and improvements in the way database providers work.</span></span> <span data-ttu-id="33a9e-153">これは 1.0.x および 1.1.x プロバイダーは EF Core 2.0 で動作しないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-153">This means that 1.0.x and 1.1.x providers will not work with EF Core 2.0.</span></span>

<span data-ttu-id="33a9e-154">EF チームに、SQL Server と SQLite プロバイダーが付属して、2.0 バージョンが利用できる、2.0 の一部としてリリースします。</span><span class="sxs-lookup"><span data-stu-id="33a9e-154">The SQL Server and SQLite providers are shipped by the EF team and 2.0 versions will be available as part of the 2.0 release.</span></span> <span data-ttu-id="33a9e-155">サード パーティ製のオープン ソースのプロバイダー [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、 [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、および[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) 2.0 に対して更新されています。</span><span class="sxs-lookup"><span data-stu-id="33a9e-155">The open-source third party providers for [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), and [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) are being updated for 2.0.</span></span> <span data-ttu-id="33a9e-156">その他のすべてのプロバイダーでは、プロバイダーの作成者に問い合わせてください。</span><span class="sxs-lookup"><span data-stu-id="33a9e-156">For all other providers, please contact the provider writer.</span></span>

## <a name="logging-and-diagnostics-events-have-changed"></a><span data-ttu-id="33a9e-157">ログ記録と診断のイベントが変更されました。</span><span class="sxs-lookup"><span data-stu-id="33a9e-157">Logging and Diagnostics events have changed</span></span>

<span data-ttu-id="33a9e-158">注: これらの変更ではほとんどのアプリケーション コードは影響しません。</span><span class="sxs-lookup"><span data-stu-id="33a9e-158">Note: these changes should not impact most application code.</span></span>

<span data-ttu-id="33a9e-159">イベント Id に送信されるメッセージについて、 [ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs)が 2.0 で変更されました。</span><span class="sxs-lookup"><span data-stu-id="33a9e-159">The event IDs for messages sent to an [ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs) have changed in 2.0.</span></span> <span data-ttu-id="33a9e-160">イベント ID が EF Core コード全体で一意になりました。</span><span class="sxs-lookup"><span data-stu-id="33a9e-160">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="33a9e-161">これらのメッセージはまた、たとえば MVC で使用される構造化されたログ記録の標準パターンに従います。</span><span class="sxs-lookup"><span data-stu-id="33a9e-161">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="33a9e-162">ロガー カテゴリも変更されました。</span><span class="sxs-lookup"><span data-stu-id="33a9e-162">Logger categories have also changed.</span></span> <span data-ttu-id="33a9e-163">現在、[DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs) 経由でアクセスされる既知のカテゴリ セットがあります。</span><span class="sxs-lookup"><span data-stu-id="33a9e-163">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="33a9e-164">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)イベントは、対応する同じイベント ID の名前を使用するようになりました`ILogger`メッセージ。</span><span class="sxs-lookup"><span data-stu-id="33a9e-164">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) events now use the same event ID names as the corresponding `ILogger` messages.</span></span> <span data-ttu-id="33a9e-165">イベント ペイロードがすべての標準型から派生した[EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs)します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-165">The event payloads are all nominal types derived from [EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs).</span></span>

<span data-ttu-id="33a9e-166">イベント Id、ペイロードの種類、およびカテゴリに記載されている、 [CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs)と[RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs)クラス。</span><span class="sxs-lookup"><span data-stu-id="33a9e-166">Event IDs, payload types, and categories are documented in the [CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs) and the [RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs) classes.</span></span>

<span data-ttu-id="33a9e-167">Id は、新しい Microsoft.EntityFrameworkCore.Diagnostics 名前空間にも Microsoft.EntityFrameworkCore.Infraestructure から移動されました。</span><span class="sxs-lookup"><span data-stu-id="33a9e-167">IDs have also moved from Microsoft.EntityFrameworkCore.Infraestructure to the new Microsoft.EntityFrameworkCore.Diagnostics namespace.</span></span>

## <a name="ef-core-relational-metadata-api-changes"></a><span data-ttu-id="33a9e-168">EF Core のリレーショナル メタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="33a9e-168">EF Core relational metadata API changes</span></span>

<span data-ttu-id="33a9e-169">EF Core 2.0 は、使用されるプロバイダーごとに異なる [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) をビルドするようになりました。</span><span class="sxs-lookup"><span data-stu-id="33a9e-169">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="33a9e-170">これは通常、アプリケーションに対して透過的です。</span><span class="sxs-lookup"><span data-stu-id="33a9e-170">This is usually transparent to the application.</span></span> <span data-ttu-id="33a9e-171">下位レベルのメタデータ API が単純になり、_一般的なリレーショナル メタデータ コンセプト_へのあらゆるアクセスが `.SqlServer` や `.Sqlite` などではなく、`.Relational` の呼び出しで常に行われます。たとえば、1.1.x ようなコード:</span><span class="sxs-lookup"><span data-stu-id="33a9e-171">This has facilitated a simplification of lower-level metadata APIs such that any access to _common relational metadata concepts_ is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc. For example, 1.1.x code like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

<span data-ttu-id="33a9e-172">このようなようになりました記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33a9e-172">Should now be written like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

<span data-ttu-id="33a9e-173">などのメソッドを使用する代わりに`ForSqlServerToTable`、拡張メソッドは使用中の現在のプロバイダーに基づく条件付きのコードを記述できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="33a9e-173">Instead of using methods like `ForSqlServerToTable`, extension methods are now available to write conditional code based on the current provider in use.</span></span> <span data-ttu-id="33a9e-174">例えば:</span><span class="sxs-lookup"><span data-stu-id="33a9e-174">For example:</span></span>

```C#
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

<span data-ttu-id="33a9e-175">この変更は、定義されている Api/メタデータにのみ適用されます_すべて_リレーショナル プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="33a9e-175">Note that this change only applies to APIs/metadata that is defined for _all_ relational providers.</span></span> <span data-ttu-id="33a9e-176">API とメタデータは、1 つのプロバイダーのみに固有である場合に、同じままです。</span><span class="sxs-lookup"><span data-stu-id="33a9e-176">The API and metadata remains the same when it is specific to only a single provider.</span></span> <span data-ttu-id="33a9e-177">たとえば、クラスター化インデックスは SQL Sever に固有ように`ForSqlServerIsClustered`と`.SqlServer().IsClustered()`引き続き使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33a9e-177">For example, clustered indexes are specific to SQL Sever, so `ForSqlServerIsClustered` and  `.SqlServer().IsClustered()` must still be used.</span></span>

## <a name="dont-take-control-of-the-ef-service-provider"></a><span data-ttu-id="33a9e-178">EF のサービス プロバイダーの管理を使用しません。</span><span class="sxs-lookup"><span data-stu-id="33a9e-178">Don’t take control of the EF service provider</span></span>

<span data-ttu-id="33a9e-179">EF Core を使用して内部`IServiceProvider`(依存関係注入コンテナー) の内部実装します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-179">EF Core uses an internal `IServiceProvider` (a dependency injection container) for its internal implementation.</span></span> <span data-ttu-id="33a9e-180">アプリケーションでは、EF Core を作成し、このプロバイダーは、特殊なケースで以外の管理を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33a9e-180">Applications should allow EF Core to create and manage this provider except in special cases.</span></span> <span data-ttu-id="33a9e-181">呼び出しの削除を厳密に検討`UseInternalServiceProvider`します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-181">Strongly consider removing any calls to `UseInternalServiceProvider`.</span></span> <span data-ttu-id="33a9e-182">アプリケーションを呼び出す必要がある場合`UseInternalServiceProvider`、しを検討してください[問題をファイリング](https://github.com/aspnet/EntityFramework/Issues)シナリオを処理する他の方法で調査を行えるようにします。</span><span class="sxs-lookup"><span data-stu-id="33a9e-182">If an application does need to call `UseInternalServiceProvider`, then please consider [filing an issue](https://github.com/aspnet/EntityFramework/Issues) so we can investigate other ways to handle your scenario.</span></span>

<span data-ttu-id="33a9e-183">呼び出す`AddEntityFramework`、 `AddEntityFrameworkSqlServer`、アプリケーション コードでは、しない限りなどは必要ありません`UseInternalServiceProvider`ともいいます。</span><span class="sxs-lookup"><span data-stu-id="33a9e-183">Calling `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. is not required by application code unless `UseInternalServiceProvider` is also called.</span></span> <span data-ttu-id="33a9e-184">既存の呼び出しをすべて削除`AddEntityFramework`または`AddEntityFrameworkSqlServer`など`AddDbContext`と同様に、以前とする必要があります使用もします。</span><span class="sxs-lookup"><span data-stu-id="33a9e-184">Remove any existing calls to `AddEntityFramework` or `AddEntityFrameworkSqlServer`, etc. `AddDbContext` should still be used in the same way as before.</span></span>

## <a name="in-memory-databases-must-be-named"></a><span data-ttu-id="33a9e-185">メモリ内データベースを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="33a9e-185">In-memory databases must be named</span></span>

<span data-ttu-id="33a9e-186">グローバルな名前のないメモリ内データベースが削除され、代わりにメモリ内のすべてのデータベースを名前必要があります。</span><span class="sxs-lookup"><span data-stu-id="33a9e-186">The global unnamed in-memory database has been removed and instead all in-memory databases must be named.</span></span> <span data-ttu-id="33a9e-187">例えば:</span><span class="sxs-lookup"><span data-stu-id="33a9e-187">For example:</span></span>

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

<span data-ttu-id="33a9e-188">これを作成/使用"MyDatabase"という名前のデータベース。</span><span class="sxs-lookup"><span data-stu-id="33a9e-188">This creates/uses a database with the name “MyDatabase”.</span></span> <span data-ttu-id="33a9e-189">場合`UseInMemoryDatabase`が再度呼び出される同じ名前の同じメモリ内データベースが使用され、複数のコンテキスト インスタンスで共有することができます。</span><span class="sxs-lookup"><span data-stu-id="33a9e-189">If `UseInMemoryDatabase` is called again with the same name, then the same in-memory database will be used, allowing it to be shared by multiple context instances.</span></span>

## <a name="read-only-api-changes"></a><span data-ttu-id="33a9e-190">読み取り専用の API の変更</span><span class="sxs-lookup"><span data-stu-id="33a9e-190">Read-only API changes</span></span>

<span data-ttu-id="33a9e-191">`IsReadOnlyBeforeSave`、 `IsReadOnlyAfterSave`、および`IsStoreGeneratedAlways`廃止され、置き換え[BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39)と[AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55)します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-191">`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave`, and `IsStoreGeneratedAlways` have been obsoleted and replaced with [BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39) and [AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55).</span></span> <span data-ttu-id="33a9e-192">これらの動作 (ストア生成プロパティだけでなく) プロパティには適用し、データベースの行に挿入するときのプロパティの値の使用方法を判断 (`BeforeSaveBehavior`) ときに、行をデータベース、既存の更新または (`AfterSaveBehavior`)。</span><span class="sxs-lookup"><span data-stu-id="33a9e-192">These behaviors apply to any property (not only store-generated properties) and determine how the value of the property should be used when inserting into a database row (`BeforeSaveBehavior`) or when updating an existing database row (`AfterSaveBehavior`).</span></span>

<span data-ttu-id="33a9e-193">プロパティとしてマーク[ValueGenerated.OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) (たとえば、計算列)、既定では無視されます、プロパティに設定されている任意の値。</span><span class="sxs-lookup"><span data-stu-id="33a9e-193">Properties marked as [ValueGenerated.OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) (for example, for computed columns) will by default ignore any value currently set on the property.</span></span> <span data-ttu-id="33a9e-194">これは、任意の値が設定や追跡対象エンティティで変更されているかどうかに関係なくストア生成値が常に得られることを意味します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-194">This means that a store-generated value will always be obtained regardless of whether any value has been set or modified on the tracked entity.</span></span> <span data-ttu-id="33a9e-195">これは、さまざまな設定で変更できます`Before\AfterSaveBehavior`します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-195">This can be changed by setting a different `Before\AfterSaveBehavior`.</span></span>

## <a name="new-clientsetnull-delete-behavior"></a><span data-ttu-id="33a9e-196">新しい ClientSetNull 削除の動作</span><span class="sxs-lookup"><span data-stu-id="33a9e-196">New ClientSetNull delete behavior</span></span>

<span data-ttu-id="33a9e-197">以前のリリースで[DeleteBehavior.Restrict](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs)がエンティティの動作をコンテキストによって追跡の詳細は一致した閉じられたこと`SetNull`セマンティクスです。</span><span class="sxs-lookup"><span data-stu-id="33a9e-197">In previous releases, [DeleteBehavior.Restrict](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs) had a behavior for entities tracked by the context that more closed matched `SetNull` semantics.</span></span> <span data-ttu-id="33a9e-198">EF Core 2.0 で新しい`ClientSetNull`省略可能なリレーションシップの既定値としての動作が導入されています。</span><span class="sxs-lookup"><span data-stu-id="33a9e-198">In EF Core 2.0, a new `ClientSetNull` behavior has been introduced as the default for optional relationships.</span></span> <span data-ttu-id="33a9e-199">この動作は、`SetNull`追跡対象のエンティティのセマンティクスと`Restrict`EF Core を使用して作成されたデータベースの動作。</span><span class="sxs-lookup"><span data-stu-id="33a9e-199">This behavior has `SetNull` semantics for tracked entities and `Restrict` behavior for databases created using EF Core.</span></span> <span data-ttu-id="33a9e-200">経験から言うと、これらは、追跡対象のエンティティとデータベースの最も期待/役立つ動作です。</span><span class="sxs-lookup"><span data-stu-id="33a9e-200">In our experience, these are the most expected/useful behaviors for tracked entities and the database.</span></span> <span data-ttu-id="33a9e-201">`DeleteBehavior.Restrict` 省略可能なリレーションシップを設定する場合、追跡対象エンティティのようになりました受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="33a9e-201">`DeleteBehavior.Restrict` is now honored for tracked entities when set for optional relationships.</span></span>

## <a name="provider-design-time-packages-removed"></a><span data-ttu-id="33a9e-202">プロバイダーのデザイン時パッケージを削除</span><span class="sxs-lookup"><span data-stu-id="33a9e-202">Provider design-time packages removed</span></span>

<span data-ttu-id="33a9e-203">`Microsoft.EntityFrameworkCore.Relational.Design`パッケージが削除されました。</span><span class="sxs-lookup"><span data-stu-id="33a9e-203">The `Microsoft.EntityFrameworkCore.Relational.Design` package has been removed.</span></span> <span data-ttu-id="33a9e-204">ファイルの内容が統合された`Microsoft.EntityFrameworkCore.Relational`と`Microsoft.EntityFrameworkCore.Design`します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-204">It's contents were consolidated into `Microsoft.EntityFrameworkCore.Relational` and `Microsoft.EntityFrameworkCore.Design`.</span></span>

<span data-ttu-id="33a9e-205">プロバイダーのデザイン時パッケージに反映されます。</span><span class="sxs-lookup"><span data-stu-id="33a9e-205">This propagates into the provider design-time packages.</span></span> <span data-ttu-id="33a9e-206">これらのパッケージ (`Microsoft.EntityFrameworkCore.Sqlite.Design`、`Microsoft.EntityFrameworkCore.SqlServer.Design`など) が削除されましたし、その内容は、メイン プロバイダーのパッケージに統合します。</span><span class="sxs-lookup"><span data-stu-id="33a9e-206">Those packages (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) were removed and their contents consolidated into the main provider packages.</span></span>

<span data-ttu-id="33a9e-207">有効にする`Scaffold-DbContext`または`dotnet ef dbcontext scaffold`EF Core 2.0 でのみ参照する必要が 1 つのプロバイダー パッケージ。</span><span class="sxs-lookup"><span data-stu-id="33a9e-207">To enable `Scaffold-DbContext` or `dotnet ef dbcontext scaffold` in EF Core 2.0, you only need to reference the single provider package:</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
