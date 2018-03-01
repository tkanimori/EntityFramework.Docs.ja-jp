---
title: "以前のバージョンから EF コア 2 - EF コアへのアップグレード"
author: divega
ms.author: divega
ms.date: 8/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
ms.technology: entity-framework-core
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: 30f4de794d42b1385145286e77c2e7c67987fea6
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a><span data-ttu-id="8da81-102">以前のバージョンから EF コア 2.0 へのアプリケーションのアップグレード</span><span class="sxs-lookup"><span data-stu-id="8da81-102">Upgrading applications from previous versions to EF Core 2.0</span></span>

## <a name="procedures-common-to-all-applications"></a><span data-ttu-id="8da81-103">すべてのアプリケーションに共通の手順</span><span class="sxs-lookup"><span data-stu-id="8da81-103">Procedures Common to All Applications</span></span>

<span data-ttu-id="8da81-104">EF コア 2.0 に既存のアプリケーションの更新が必要です。</span><span class="sxs-lookup"><span data-stu-id="8da81-104">Updating an existing application to EF Core 2.0 may require:</span></span>

1. <span data-ttu-id="8da81-105">.NET 標準 2.0 をサポートしているアプリケーションのターゲットの .NET プラットフォームをアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="8da81-105">Upgrading the target .NET platform of the application to one that supports .NET Standard 2.0.</span></span> <span data-ttu-id="8da81-106">参照してください[サポートされているプラットフォーム](../platforms/index.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="8da81-106">See [Supported Platforms](../platforms/index.md) for more details.</span></span>

2. <span data-ttu-id="8da81-107">EF コア 2.0 と互換性があるターゲット データベースのプロバイダーを識別します。</span><span class="sxs-lookup"><span data-stu-id="8da81-107">Identify a provider for the target database which is compatible with EF Core 2.0.</span></span> <span data-ttu-id="8da81-108">参照してください[EF コア 2.0 には、2.0 データベース プロバイダーが必要があります](#ef-core-20-requires-a-20-database-provider)以下です。</span><span class="sxs-lookup"><span data-stu-id="8da81-108">See [EF Core 2.0 requires a 2.0 database provider](#ef-core-20-requires-a-20-database-provider) below.</span></span>

3. <span data-ttu-id="8da81-109">2.0 へのすべての EF Core パッケージ (ランタイムおよびツール) をアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="8da81-109">Upgrading all the EF Core packages (runtime and tooling) to 2.0.</span></span> <span data-ttu-id="8da81-110">参照してください[EF Core のインストール](../get-started/install/index.md)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="8da81-110">Refer to [Installing EF Core](../get-started/install/index.md) for more details.</span></span>

4. <span data-ttu-id="8da81-111">重大な変更を補正するために必要なコード変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="8da81-111">Make any necessary code changes to compensate for breaking changes.</span></span> <span data-ttu-id="8da81-112">参照してください、[の重大な変更](#breaking-changes)詳細については、後述の「します。</span><span class="sxs-lookup"><span data-stu-id="8da81-112">See the [Breaking Changes](#breaking-changes) section below for more details.</span></span>

## <a name="aspnet-core-applications"></a><span data-ttu-id="8da81-113">ASP.NET Core アプリケーション</span><span class="sxs-lookup"><span data-stu-id="8da81-113">ASP.NET Core applications</span></span>

1. <span data-ttu-id="8da81-114">具体的にを参照してください、[アプリケーションのサービス プロバイダーを初期化するための新しいパターン](#new-way-of-getting-application-services)以下で説明します。</span><span class="sxs-lookup"><span data-stu-id="8da81-114">See in particular the [new pattern for initializing the application's service provider](#new-way-of-getting-application-services) described below.</span></span>

> [!TIP]  
> <span data-ttu-id="8da81-115">この新しいパターンを強くお勧めし、Entity Framework の中核となる移行と同様に、製品の機能の動作するために必要な 2.0 アプリケーションの更新うちに導入します。</span><span class="sxs-lookup"><span data-stu-id="8da81-115">The adoption of this new pattern when updating applications to 2.0 is highly recommended and is required in order for product features like Entity Framework Core Migrations to work.</span></span> <span data-ttu-id="8da81-116">他の一般的な手段[実装*IDesignTimeDbContextFactory\<TContext >*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)です。</span><span class="sxs-lookup"><span data-stu-id="8da81-116">The other common alternative is to [implement *IDesignTimeDbContextFactory\<TContext>*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).</span></span>

2. <span data-ttu-id="8da81-117">ASP.NET Core 2.0 を対象とするアプリケーションは、サードパーティ データベース プロバイダー以外の依存関係を追加せずに EF Core 2.0 を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8da81-117">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="8da81-118">ただし、以前のバージョンの ASP.NET Core をターゲットとするアプリケーションでは、EF コア 2.0 を使用するために ASP.NET Core 2.0 にアップグレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8da81-118">However, applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span> <span data-ttu-id="8da81-119">2.0 に ASP.NET Core アプリケーションのアップグレードに関する詳細を参照してください[ASP.NET Core のドキュメントにあるサブジェクトに](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/)です。</span><span class="sxs-lookup"><span data-stu-id="8da81-119">For more details on upgrading ASP.NET Core applications to 2.0 see [the ASP.NET Core documentation on the subject](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/).</span></span>

## <a name="breaking-changes"></a><span data-ttu-id="8da81-120">互換性に影響する変更点</span><span class="sxs-lookup"><span data-stu-id="8da81-120">Breaking Changes</span></span>

<span data-ttu-id="8da81-121">大幅に改良、既存の Api 2.0 で動作する機会を移動しました。</span><span class="sxs-lookup"><span data-stu-id="8da81-121">We have taken the opportunity to significantly refine our existing APIs and behaviors in 2.0.</span></span> <span data-ttu-id="8da81-122">既存のアプリケーション コードの変更が必要になる改良がいくつか行われて、アプリケーションの大部分のあることが影響を再コンパイルして古い Api を置換する最小限の画面の指示に従って変更だけを必要とするほとんどの場合、低なります。</span><span class="sxs-lookup"><span data-stu-id="8da81-122">There are a few improvements that can require modifying existing application code, although we believe that for the majority of applications the impact will be low, in most cases requiring just recompilation and minimal guided changes to replace obsolete APIs.</span></span>

### <a name="new-way-of-getting-application-services"></a><span data-ttu-id="8da81-123">アプリケーション サービスを取得する新しい方法</span><span class="sxs-lookup"><span data-stu-id="8da81-123">New way of getting application services</span></span>

<span data-ttu-id="8da81-124">1.x で EF コアが使用されるデザイン時のロジックを無効にした方法で 2.0 の ASP.NET Core web アプリケーションに対して、推奨パターンが更新されました。</span><span class="sxs-lookup"><span data-stu-id="8da81-124">The recommended pattern for ASP.NET Core web applications has been updated for 2.0 in a way that broke the design-time logic EF Core used in 1.x.</span></span> <span data-ttu-id="8da81-125">以前のデザイン時に、EF コアを試行を呼び出す`Startup.ConfigureServices`アプリケーションのサービス プロバイダーにアクセスするために直接できます。</span><span class="sxs-lookup"><span data-stu-id="8da81-125">Previously at design-time, EF Core would try to invoke `Startup.ConfigureServices` directly in order to access the application's service provider.</span></span> <span data-ttu-id="8da81-126">外部の ASP.NET Core 2.0 の構成が初期化されて、`Startup`クラスです。</span><span class="sxs-lookup"><span data-stu-id="8da81-126">In ASP.NET Core 2.0, Configuration is initialized outside of the `Startup` class.</span></span> <span data-ttu-id="8da81-127">通常 EF コアを使用してアプリケーションへのアクセス、接続文字列構成からため`Startup`自体では十分なできなくします。</span><span class="sxs-lookup"><span data-stu-id="8da81-127">Applications using EF Core typically access their connection string from Configuration, so `Startup` by itself is no longer sufficient.</span></span> <span data-ttu-id="8da81-128">ASP.NET Core 1.x アプリケーションをアップグレードする場合は、EF コア ツールを使用する場合、次のエラーを受信可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8da81-128">If you upgrade an ASP.NET Core 1.x application, you may receive the following error when using the EF Core tools.</span></span>

> <span data-ttu-id="8da81-129">パラメーターなしコンス トラクターは、'ApplicationContext' で見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="8da81-129">No parameterless constructor was found on 'ApplicationContext'.</span></span> <span data-ttu-id="8da81-130">パラメーターなしのコンス トラクターを 'ApplicationContext' に追加するか、追加の実装 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' 'ApplicationContext' と同じアセンブリ内</span><span class="sxs-lookup"><span data-stu-id="8da81-130">Either add a parameterless constructor to 'ApplicationContext' or add an implementation of 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' in the same assembly as 'ApplicationContext'</span></span>

<span data-ttu-id="8da81-131">ASP.NET Core 2.0 の既定のテンプレートで、新しいデザイン時フックが追加されました。</span><span class="sxs-lookup"><span data-stu-id="8da81-131">A new design-time hook has been added in ASP.NET Core 2.0's default template.</span></span> <span data-ttu-id="8da81-132">静的な`Program.BuildWebHost`メソッドにより、EF のコアがデザイン時に、アプリケーションのサービス プロバイダーにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="8da81-132">The static `Program.BuildWebHost` method enables EF Core to access the application's service provider at design time.</span></span> <span data-ttu-id="8da81-133">ASP.NET Core 1.x アプリケーションをアップグレードする場合は、更新する必要があります。`Program`クラスを、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8da81-133">If you are upgrading an ASP.NET Core 1.x application, you will need to update you `Program` class to resemble the following.</span></span>

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

### <a name="idbcontextfactory-renamed"></a><span data-ttu-id="8da81-134">IDbContextFactory の名前を変更</span><span class="sxs-lookup"><span data-stu-id="8da81-134">IDbContextFactory renamed</span></span>

<span data-ttu-id="8da81-135">さまざまなアプリケーション パターンをサポートし、方法をより細かく制御できるようにするために、`DbContext`使用は、デザイン時にある、以前は、指定、`IDbContextFactory<TContext>`インターフェイス。</span><span class="sxs-lookup"><span data-stu-id="8da81-135">In order to support diverse application patterns and give users more control over how their `DbContext` is used at design time, we have, in the past, provided the `IDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="8da81-136">デザイン時に、EF コア ツールはプロジェクトでこのインターフェイスの実装を見つけてを使用して作成`DbContext`オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="8da81-136">At design-time, the EF Core tools will discover implementations of this interface in your project and use it to create `DbContext` objects.</span></span>

<span data-ttu-id="8da81-137">このインターフェイスには、一部のユーザーに再を使用してその他の誤解する可能性が非常に一般的な名前が必要がある`DbContext`のシナリオを作成します。</span><span class="sxs-lookup"><span data-stu-id="8da81-137">This interface had a very general name which mislead some users to try re-using it for other `DbContext`-creating scenarios.</span></span> <span data-ttu-id="8da81-138">デザイン時にその実装を使用する EF ツールが、試みられたときにガード オフ キャッチおよびのようなコマンドの原因となった`Update-Database`または`dotnet ef database update`が失敗します。</span><span class="sxs-lookup"><span data-stu-id="8da81-138">They were caught off guard when the EF Tools then tried to use their implementation at design-time and caused commands like `Update-Database` or `dotnet ef database update` to fail.</span></span>

<span data-ttu-id="8da81-139">このインターフェイスの強力なデザイン時のセマンティクスを通信するために名前を変更しましたに`IDesignTimeDbContextFactory<TContext>`です。</span><span class="sxs-lookup"><span data-stu-id="8da81-139">In order to communicate the strong design-time semantics of this interface, we have renamed it to `IDesignTimeDbContextFactory<TContext>`.</span></span>

<span data-ttu-id="8da81-140">2.0 リリース、`IDbContextFactory<TContext>`引き続き存在しますが、不使用とマークされます。</span><span class="sxs-lookup"><span data-stu-id="8da81-140">For the 2.0 release the `IDbContextFactory<TContext>` still exists but is marked as obsolete.</span></span>

### <a name="dbcontextfactoryoptions-removed"></a><span data-ttu-id="8da81-141">DbContextFactoryOptions removed</span><span class="sxs-lookup"><span data-stu-id="8da81-141">DbContextFactoryOptions removed</span></span>

<span data-ttu-id="8da81-142">変更のため、ASP.NET Core 2.0 上で説明した、ことを確認しました`DbContextFactoryOptions`が不要になった新しい`IDesignTimeDbContextFactory<TContext>`インターフェイスです。</span><span class="sxs-lookup"><span data-stu-id="8da81-142">Because of the ASP.NET Core 2.0 changes described above, we found that `DbContextFactoryOptions` was no longer needed on the new `IDesignTimeDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="8da81-143">ここでは、代替の代わりに使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8da81-143">Here are the alternatives you should be using instead.</span></span>

| <span data-ttu-id="8da81-144">DbContextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="8da81-144">DbContextFactoryOptions</span></span> | <span data-ttu-id="8da81-145">代替</span><span class="sxs-lookup"><span data-stu-id="8da81-145">Alternative</span></span>                                                  |
|:------------------------|:-------------------------------------------------------------|
| <span data-ttu-id="8da81-146">ApplicationBasePath</span><span class="sxs-lookup"><span data-stu-id="8da81-146">ApplicationBasePath</span></span>     | <span data-ttu-id="8da81-147">AppContext.BaseDirectory</span><span class="sxs-lookup"><span data-stu-id="8da81-147">AppContext.BaseDirectory</span></span>                                     |
| <span data-ttu-id="8da81-148">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="8da81-148">ContentRootPath</span></span>         | <span data-ttu-id="8da81-149">Directory.GetCurrentDirectory()</span><span class="sxs-lookup"><span data-stu-id="8da81-149">Directory.GetCurrentDirectory()</span></span>                              |
| <span data-ttu-id="8da81-150">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="8da81-150">EnvironmentName</span></span>         | <span data-ttu-id="8da81-151">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span><span class="sxs-lookup"><span data-stu-id="8da81-151">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span></span> |

### <a name="design-time-working-directory-changed"></a><span data-ttu-id="8da81-152">デザイン時の作業ディレクトリが変更されました</span><span class="sxs-lookup"><span data-stu-id="8da81-152">Design-time working directory changed</span></span>

<span data-ttu-id="8da81-153">使用される作業ディレクトリにも必要な ASP.NET Core 2.0 変更`dotnet ef`アプリケーションを実行するときに、Visual Studio で使用される作業ディレクトリに合うようにします。</span><span class="sxs-lookup"><span data-stu-id="8da81-153">The ASP.NET Core 2.0 changes also required the working directory used by `dotnet ef` to align with the working directory used by Visual Studio when running your application.</span></span> <span data-ttu-id="8da81-154">この監視可能な副作用 1 つは、ファイル名は今すぐに対する相対パス、プロジェクト ディレクトリと出力ディレクトリではなく、使用するようにその SQLite です。</span><span class="sxs-lookup"><span data-stu-id="8da81-154">One observable side effect of this is that SQLite filenames are now relative to the project directory and not the output directory like they used to be.</span></span>

### <a name="ef-core-20-requires-a-20-database-provider"></a><span data-ttu-id="8da81-155">EF コア 2.0 2.0 データベース プロバイダーが必要です。</span><span class="sxs-lookup"><span data-stu-id="8da81-155">EF Core 2.0 requires a 2.0 database provider</span></span>

<span data-ttu-id="8da81-156">EF コア 2.0 は、多くの簡素化と機能強化データベース プロバイダーの方法で作業を行いました。</span><span class="sxs-lookup"><span data-stu-id="8da81-156">For EF Core 2.0 we have made many simplifications and improvements in the way database providers work.</span></span> <span data-ttu-id="8da81-157">これは 1.0.x および 1.1.x プロバイダーは、EF コア 2.0 では動作しないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="8da81-157">This means that 1.0.x and 1.1.x providers will not work with EF Core 2.0.</span></span>

<span data-ttu-id="8da81-158">EF チームによって、SQL Server と SQLite プロバイダーが付属してでき 2.0 バージョンは 2.0 の一部としてリリースされます。</span><span class="sxs-lookup"><span data-stu-id="8da81-158">The SQL Server and SQLite providers are shipped by the EF team and 2.0 versions will be available as part of the 2.0 release.</span></span> <span data-ttu-id="8da81-159">オープン ソース サード パーティ プロバイダー [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、 [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、および[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) 2.0 の更新中です。</span><span class="sxs-lookup"><span data-stu-id="8da81-159">The open-source third party providers for [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), and [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) are being updated for 2.0.</span></span> <span data-ttu-id="8da81-160">その他のすべてのプロバイダーでは、プロバイダーの作成者に問い合わせてください。</span><span class="sxs-lookup"><span data-stu-id="8da81-160">For all other providers, please contact the provider writer.</span></span>

### <a name="logging-and-diagnostics-events-have-changed"></a><span data-ttu-id="8da81-161">ログ記録と診断イベントが変更されました</span><span class="sxs-lookup"><span data-stu-id="8da81-161">Logging and Diagnostics events have changed</span></span>

<span data-ttu-id="8da81-162">注: これらの変更では、ほとんどのアプリケーション コードを影響はありません。</span><span class="sxs-lookup"><span data-stu-id="8da81-162">Note: these changes should not impact most application code.</span></span>

<span data-ttu-id="8da81-163">イベント Id に送信されたメッセージについて、 [ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs)が 2.0 に変更します。</span><span class="sxs-lookup"><span data-stu-id="8da81-163">The event IDs for messages sent to an [ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs) have changed in 2.0.</span></span> <span data-ttu-id="8da81-164">イベント ID が EF Core コード全体で一意になりました。</span><span class="sxs-lookup"><span data-stu-id="8da81-164">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="8da81-165">これらのメッセージはまた、たとえば MVC で使用される構造化されたログ記録の標準パターンに従います。</span><span class="sxs-lookup"><span data-stu-id="8da81-165">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="8da81-166">ロガー カテゴリも変更されました。</span><span class="sxs-lookup"><span data-stu-id="8da81-166">Logger categories have also changed.</span></span> <span data-ttu-id="8da81-167">現在、[DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs) 経由でアクセスされる既知のカテゴリ セットがあります。</span><span class="sxs-lookup"><span data-stu-id="8da81-167">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="8da81-168">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)イベントは、対応するとして同じイベント ID 名を使用するようになりました`ILogger`メッセージ。</span><span class="sxs-lookup"><span data-stu-id="8da81-168">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) events now use the same event ID names as the corresponding `ILogger` messages.</span></span> <span data-ttu-id="8da81-169">イベント ペイロードは、すべての標準型から派生した[EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs)です。</span><span class="sxs-lookup"><span data-stu-id="8da81-169">The event payloads are all nominal types derived from [EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs).</span></span>

<span data-ttu-id="8da81-170">イベント Id、ペイロードの種類、およびカテゴリは、『、 [CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs)と[RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs)クラスです。</span><span class="sxs-lookup"><span data-stu-id="8da81-170">Event IDs, payload types, and categories are documented in the [CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs) and the [RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs) classes.</span></span>

<span data-ttu-id="8da81-171">Id は、新しい Microsoft.EntityFrameworkCore.Diagnostics 名前空間にも Microsoft.EntityFrameworkCore.Infraestructure から移動されました。</span><span class="sxs-lookup"><span data-stu-id="8da81-171">IDs have also moved from Microsoft.EntityFrameworkCore.Infraestructure to the new Microsoft.EntityFrameworkCore.Diagnostics namespace.</span></span>

### <a name="ef-core-relational-metadata-api-changes"></a><span data-ttu-id="8da81-172">EF コア リレーショナル メタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="8da81-172">EF Core relational metadata API changes</span></span>

<span data-ttu-id="8da81-173">EF Core 2.0 は、使用されるプロバイダーごとに異なる [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) をビルドするようになりました。</span><span class="sxs-lookup"><span data-stu-id="8da81-173">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="8da81-174">これは通常、アプリケーションに対して透過的です。</span><span class="sxs-lookup"><span data-stu-id="8da81-174">This is usually transparent to the application.</span></span> <span data-ttu-id="8da81-175">下位レベルのメタデータ API が単純になり、_一般的なリレーショナル メタデータ コンセプト_へのあらゆるアクセスが `.SqlServer` や `.Sqlite` などではなく、`.Relational` の呼び出しで常に行われます。たとえば、1.1.x コード次のようにします。</span><span class="sxs-lookup"><span data-stu-id="8da81-175">This has facilitated a simplification of lower-level metadata APIs such that any access to _common relational metadata concepts_ is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc. For example, 1.1.x code like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

<span data-ttu-id="8da81-176">次のようになりました記述する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8da81-176">Should now be written like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

<span data-ttu-id="8da81-177">などのメソッドを使用する代わりに`ForSqlServerToTable`、拡張メソッドは現在使用中の現在のプロバイダーに基づく条件付きのコードを記述できます。</span><span class="sxs-lookup"><span data-stu-id="8da81-177">Instead of using methods like `ForSqlServerToTable`, extension methods are now available to write conditional code based on the current provider in use.</span></span> <span data-ttu-id="8da81-178">例:</span><span class="sxs-lookup"><span data-stu-id="8da81-178">For example:</span></span>

```C#
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

<span data-ttu-id="8da81-179">この変更は、定義されている Api/メタデータにのみ適用する注_すべて_リレーショナル プロバイダー。</span><span class="sxs-lookup"><span data-stu-id="8da81-179">Note that this change only applies to APIs/metadata that is defined for _all_ relational providers.</span></span> <span data-ttu-id="8da81-180">API とメタデータは、1 つのプロバイダーのみに固有であるときに、同じです。</span><span class="sxs-lookup"><span data-stu-id="8da81-180">The API and metadata remains the same when it is specific to only a single provider.</span></span> <span data-ttu-id="8da81-181">たとえば、クラスター化インデックスは SQL Sever、特定ように`ForSqlServerIsClustered`と`.SqlServer().IsClustered()`まだを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8da81-181">For example, clustered indexes are specific to SQL Sever, so `ForSqlServerIsClustered` and  `.SqlServer().IsClustered()` must still be used.</span></span>

### <a name="dont-take-control-of-the-ef-service-provider"></a><span data-ttu-id="8da81-182">EF サービス プロバイダーを制御することがありません。</span><span class="sxs-lookup"><span data-stu-id="8da81-182">Don’t take control of the EF service provider</span></span>

<span data-ttu-id="8da81-183">EF コアを使用して、内部`IServiceProvider`(依存関係の注入コンテナーなど) ため、内部の実装です。</span><span class="sxs-lookup"><span data-stu-id="8da81-183">EF Core uses an internal `IServiceProvider` (i.e. a dependency injection container) for its internal implementation.</span></span> <span data-ttu-id="8da81-184">アプリケーションでは、EF のコアを作成し、特殊なケースで以外は、このプロバイダーを管理するを許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8da81-184">Applications should allow EF Core to create and manage this provider except in special cases.</span></span> <span data-ttu-id="8da81-185">呼び出しの削除を検討してください`UseInternalServiceProvider`です。</span><span class="sxs-lookup"><span data-stu-id="8da81-185">Strongly consider removing any calls to `UseInternalServiceProvider`.</span></span> <span data-ttu-id="8da81-186">アプリケーションを呼び出す必要がある場合`UseInternalServiceProvider`、しを検討してください[問題をファイリング](https://github.com/aspnet/EntityFramework/Issues)シナリオを処理するには、その他の方法で調査を行えるようにします。</span><span class="sxs-lookup"><span data-stu-id="8da81-186">If an application does need to call `UseInternalServiceProvider`, then please consider [filing an issue](https://github.com/aspnet/EntityFramework/Issues) so we can investigate other ways to handle your scenario.</span></span>

<span data-ttu-id="8da81-187">呼び出す`AddEntityFramework`、 `AddEntityFrameworkSqlServer`、しない限り、アプリケーション コードによってなどが必要ありません`UseInternalServiceProvider`とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="8da81-187">Calling `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. is not required by application code unless `UseInternalServiceProvider` is also called.</span></span> <span data-ttu-id="8da81-188">呼び出しの既存の削除`AddEntityFramework`または`AddEntityFrameworkSqlServer`など`AddDbContext`必要がありますも使用する同じ方法で以前と同様です。</span><span class="sxs-lookup"><span data-stu-id="8da81-188">Remove any existing calls to `AddEntityFramework` or `AddEntityFrameworkSqlServer`, etc. `AddDbContext` should still be used in the same way as before.</span></span>

### <a name="in-memory-databases-must-be-named"></a><span data-ttu-id="8da81-189">インメモリ データベースを指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8da81-189">In-memory databases must be named</span></span>

<span data-ttu-id="8da81-190">名前のないメモリ内のグローバル データベースが削除され、代わりにメモリ内のすべてのデータベースを名前必要があります。</span><span class="sxs-lookup"><span data-stu-id="8da81-190">The global unnamed in-memory database has been removed and instead all in-memory databases must be named.</span></span> <span data-ttu-id="8da81-191">例:</span><span class="sxs-lookup"><span data-stu-id="8da81-191">For example:</span></span>

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

<span data-ttu-id="8da81-192">これを作成/は"MyDatabase"という名前のデータベースが使用します。</span><span class="sxs-lookup"><span data-stu-id="8da81-192">This creates/uses a database with the name “MyDatabase”.</span></span> <span data-ttu-id="8da81-193">場合`UseInMemoryDatabase`が再度呼び出され、同じ名前を持つ、複数のコンテキストのインスタンスで共有することができるメモリ内の同じデータベースが使用されます。</span><span class="sxs-lookup"><span data-stu-id="8da81-193">If `UseInMemoryDatabase` is called again with the same name, then the same in-memory database will be used, allowing it to be shared by multiple context instances.</span></span>

### <a name="read-only-api-changes"></a><span data-ttu-id="8da81-194">読み取り専用の API の変更</span><span class="sxs-lookup"><span data-stu-id="8da81-194">Read-only API changes</span></span>

<span data-ttu-id="8da81-195">`IsReadOnlyBeforeSave`、 `IsReadOnlyAferSave`、および`IsStoreGeneratedAlways`廃止され、で置き換えられました[BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39)と[AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55)です。</span><span class="sxs-lookup"><span data-stu-id="8da81-195">`IsReadOnlyBeforeSave`, `IsReadOnlyAferSave`, and `IsStoreGeneratedAlways` have been obsoleted and replaced with [BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39) and [AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55).</span></span> <span data-ttu-id="8da81-196">これらの動作は、任意のプロパティ (ストア生成のプロパティだけでなく) に適用され、データベースの行に挿入するときに、プロパティの値を使用する方法を決定 (`BeforeSaveBehavior`) ときに、行をデータベースを既存の更新または (`AfterSaveBehavior`)。</span><span class="sxs-lookup"><span data-stu-id="8da81-196">These behaviors apply to any property (not only store-generated properties) and determine how the value of the property should be used when inserting into a database row (`BeforeSaveBehavior`) or when updating an existing database row (`AfterSaveBehavior`).</span></span>

<span data-ttu-id="8da81-197">としてマークされているプロパティ[ValueGenerated.OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) (例: 計算列) が既定では無視してプロパティに設定されている任意の値。</span><span class="sxs-lookup"><span data-stu-id="8da81-197">Properties marked as [ValueGenerated.OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) (e.g. for computed columns) will by default ignore any value currently set on the property.</span></span> <span data-ttu-id="8da81-198">これは、任意の値が設定または追跡対象エンティティで変更されたかどうかに関係なくストア生成の値が常に取得することを意味します。</span><span class="sxs-lookup"><span data-stu-id="8da81-198">This means that a store-generated value will always be obtained regardless of whether any value has been set or modified on the tracked entity.</span></span> <span data-ttu-id="8da81-199">これは、別の設定で変更できます`Before\AfterSaveBehavior`です。</span><span class="sxs-lookup"><span data-stu-id="8da81-199">This can be changed by setting a different `Before\AfterSaveBehavior`.</span></span>

### <a name="new-clientsetnull-delete-behavior"></a><span data-ttu-id="8da81-200">新しい ClientSetNull 削除の動作</span><span class="sxs-lookup"><span data-stu-id="8da81-200">New ClientSetNull delete behavior</span></span>

<span data-ttu-id="8da81-201">以前のリリースで[DeleteBehavior.Restrict](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs)がエンティティの動作がコンテキストによって追跡以上の一致した閉じたこと`SetNull`セマンティクスです。</span><span class="sxs-lookup"><span data-stu-id="8da81-201">In previous releases, [DeleteBehavior.Restrict](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs) had a behavior for entities tracked by the context that more closed matched `SetNull` semantics.</span></span> <span data-ttu-id="8da81-202">EF コア 2.0 で新しい`ClientSetNull`動作が省略可能なリレーションシップの既定値として導入されました。</span><span class="sxs-lookup"><span data-stu-id="8da81-202">In EF Core 2.0, a new `ClientSetNull` behavior has been introduced as the default for optional relationships.</span></span> <span data-ttu-id="8da81-203">この動作は、`SetNull`追跡対象のエンティティのセマンティクスと`Restrict`EF コアを使用して作成されたデータベースの動作です。</span><span class="sxs-lookup"><span data-stu-id="8da81-203">This behavior has `SetNull` semantics for tracked entities and `Restrict` behavior for databases created using EF Core.</span></span> <span data-ttu-id="8da81-204">経験では、これらは、追跡対象のエンティティとデータベースの最も期待/役に立つの動作です。</span><span class="sxs-lookup"><span data-stu-id="8da81-204">In our experience, these are the most expected/useful behaviors for tracked entities and the database.</span></span> <span data-ttu-id="8da81-205">`DeleteBehavior.Restrict` 省略可能なリレーションシップを設定すると、追跡対象のエンティティのようになりました受け入れられます。</span><span class="sxs-lookup"><span data-stu-id="8da81-205">`DeleteBehavior.Restrict` is now honored for tracked entities when set for optional relationships.</span></span>

### <a name="provider-design-time-packages-removed"></a><span data-ttu-id="8da81-206">プロバイダーのデザイン時パッケージの削除</span><span class="sxs-lookup"><span data-stu-id="8da81-206">Provider design-time packages removed</span></span>

<span data-ttu-id="8da81-207">`Microsoft.EntityFrameworkCore.Relational.Design`パッケージが削除されました。</span><span class="sxs-lookup"><span data-stu-id="8da81-207">The `Microsoft.EntityFrameworkCore.Relational.Design` package has been removed.</span></span> <span data-ttu-id="8da81-208">ファイルの内容を統合していた`Microsoft.EntityFrameworkCore.Relational`と`Microsoft.EntityFrameworkCore.Design`です。</span><span class="sxs-lookup"><span data-stu-id="8da81-208">It's contents were consolidated into `Microsoft.EntityFrameworkCore.Relational` and `Microsoft.EntityFrameworkCore.Design`.</span></span>

<span data-ttu-id="8da81-209">プロバイダーのデザイン時パッケージに反映されます。</span><span class="sxs-lookup"><span data-stu-id="8da81-209">This propagates into the provider design-time packages.</span></span> <span data-ttu-id="8da81-210">それらのパッケージ (`Microsoft.EntityFrameworkCore.Sqlite.Design`、`Microsoft.EntityFrameworkCore.SqlServer.Design`など) が削除し、その内容がメイン プロバイダーのパッケージに統合します。</span><span class="sxs-lookup"><span data-stu-id="8da81-210">Those packages (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) were removed and their contents consolidated into the main provider packages.</span></span>

<span data-ttu-id="8da81-211">有効にする`Scaffold-DbContext`または`dotnet ef dbcontext scaffold`EF コア 2.0 でのみ参照する必要が 1 つのプロバイダーのパッケージ。</span><span class="sxs-lookup"><span data-stu-id="8da81-211">To enable `Scaffold-DbContext` or `dotnet ef dbcontext scaffold` in EF Core 2.0, you only need to reference the single provider package:</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
