---
title: 以前のバージョンから EF Core 2 へのアップグレード - EF Core
description: Entity Framework Core 2.0 へのアップグレードの手順と注意事項
author: ajcvickers
ms.date: 08/13/2017
uid: core/what-is-new/ef-core-2.0/upgrade
ms.openlocfilehash: 5054661d308e7ea6acd433981dfb2af6026b7765
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430093"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a><span data-ttu-id="9c2b6-103">以前のバージョンから EF Core 2.0 へのアプリケーションのアップグレード</span><span class="sxs-lookup"><span data-stu-id="9c2b6-103">Upgrading applications from previous versions to EF Core 2.0</span></span>

<span data-ttu-id="9c2b6-104">2\.0 では既存の API と動作を大幅に改善する機会を得ました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-104">We have taken the opportunity to significantly refine our existing APIs and behaviors in 2.0.</span></span> <span data-ttu-id="9c2b6-105">いくつかの改善点では既存のアプリケーション コードの変更を必要とする可能性があります。ほとんどのアプリケーションで、その影響は少ないと考えられ、ほとんどの場合は、古い API を置き換えるための再コンパイルと最小限のガイド付き変更が必要なだけです。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-105">There are a few improvements that can require modifying existing application code, although we believe that for the majority of applications the impact will be low, in most cases requiring just recompilation and minimal guided changes to replace obsolete APIs.</span></span>

<span data-ttu-id="9c2b6-106">既存のアプリケーションを EF Core 2.0 に更新する場合は、次のことが必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-106">Updating an existing application to EF Core 2.0 may require:</span></span>

1. <span data-ttu-id="9c2b6-107">アプリケーションのターゲット .NET 実装を、.NET Standard 2.0 をサポートするものにアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-107">Upgrading the target .NET implementation of the application to one that supports .NET Standard 2.0.</span></span> <span data-ttu-id="9c2b6-108">詳細については、[サポートされている .NET 実装](xref:core/miscellaneous/platforms)に関するページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-108">See [Supported .NET Implementations](xref:core/miscellaneous/platforms) for more details.</span></span>

2. <span data-ttu-id="9c2b6-109">EF Core 2.0 と互換性のあるターゲット データベース用のプロバイダーを特定します。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-109">Identify a provider for the target database which is compatible with EF Core 2.0.</span></span> <span data-ttu-id="9c2b6-110">「[EF Core 2.0 には 2.0 データベース プロバイダーが必要](#ef-core-20-requires-a-20-database-provider)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-110">See [EF Core 2.0 requires a 2.0 database provider](#ef-core-20-requires-a-20-database-provider) below.</span></span>

3. <span data-ttu-id="9c2b6-111">すべての EF Core パッケージ (ランタイムおよびツール) を 2.0 にアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-111">Upgrading all the EF Core packages (runtime and tooling) to 2.0.</span></span> <span data-ttu-id="9c2b6-112">詳細については、「[EF Core のインストール](xref:core/get-started/overview/install)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-112">Refer to [Installing EF Core](xref:core/get-started/overview/install) for more details.</span></span>

4. <span data-ttu-id="9c2b6-113">このドキュメントの残りの部分で説明されている破壊的変更を補正するために必要なコード変更を行います。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-113">Make any necessary code changes to compensate for the breaking changes described in the rest of this document.</span></span>

## <a name="aspnet-core-now-includes-ef-core"></a><span data-ttu-id="9c2b6-114">ASP.NET Core に EF Core が含まれるようになりました</span><span class="sxs-lookup"><span data-stu-id="9c2b6-114">ASP.NET Core now includes EF Core</span></span>

<span data-ttu-id="9c2b6-115">ASP.NET Core 2.0 を対象とするアプリケーションは、サードパーティ データベース プロバイダー以外の依存関係を追加せずに EF Core 2.0 を使用できます。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-115">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="9c2b6-116">ただし、前バージョンの ASP.NET Core をターゲットとするアプリケーションで EF Core 2.0 を使用するには、ASP.NET Core 2.0 にアップグレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-116">However, applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span> <span data-ttu-id="9c2b6-117">ASP.NET Core アプリケーションを 2.0 にアップグレードする方法の詳細については、[この件に関する ASP.NET Core のドキュメント](/aspnet/core/migration/1x-to-2x/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-117">For more details on upgrading ASP.NET Core applications to 2.0 see [the ASP.NET Core documentation on the subject](/aspnet/core/migration/1x-to-2x/).</span></span>

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a><span data-ttu-id="9c2b6-118">ASP.NET Core でアプリケーション サービスを取得するための新しい方法</span><span class="sxs-lookup"><span data-stu-id="9c2b6-118">New way of getting application services in ASP.NET Core</span></span>

<span data-ttu-id="9c2b6-119">ASP.NET Core Web アプリケーションに推奨されるパターンは、2.0 では、1.x で使用されているデザイン時ロジック EF Core を破壊する方法で更新されています。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-119">The recommended pattern for ASP.NET Core web applications has been updated for 2.0 in a way that broke the design-time logic EF Core used in 1.x.</span></span> <span data-ttu-id="9c2b6-120">以前はデザイン時に、EF Core はアプリケーションのサービス プロバイダーにアクセスするために、`Startup.ConfigureServices` の直接呼び出しを試みていました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-120">Previously at design-time, EF Core would try to invoke `Startup.ConfigureServices` directly in order to access the application's service provider.</span></span> <span data-ttu-id="9c2b6-121">ASP.NET Core 2.0 では、構成は `Startup` クラスの外部で初期化されます。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-121">In ASP.NET Core 2.0, Configuration is initialized outside of the `Startup` class.</span></span> <span data-ttu-id="9c2b6-122">EF Core を使用するアプリケーションでは、通常、構成から接続文字列へのアクセスが行われるため、`Startup` だけではもはや十分ではありません。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-122">Applications using EF Core typically access their connection string from Configuration, so `Startup` by itself is no longer sufficient.</span></span> <span data-ttu-id="9c2b6-123">ASP.NET Core 1.x アプリケーションをアップグレードすると、EF Core ツールの使用時に次のエラーが表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-123">If you upgrade an ASP.NET Core 1.x application, you may receive the following error when using the EF Core tools.</span></span>

> <span data-ttu-id="9c2b6-124">'ApplicationContext' にパラメーターなしのコンストラクターが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-124">No parameterless constructor was found on 'ApplicationContext'.</span></span> <span data-ttu-id="9c2b6-125">パラメーターなしのコンストラクターを ' ApplicationContext' に追加するか、'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' の実装を 'ApplicationContext' と同じアセンブリに追加してください</span><span class="sxs-lookup"><span data-stu-id="9c2b6-125">Either add a parameterless constructor to 'ApplicationContext' or add an implementation of 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' in the same assembly as 'ApplicationContext'</span></span>

<span data-ttu-id="9c2b6-126">新しいデザイン時フックが ASP.NET Core 2.0 の既定のテンプレートに追加されました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-126">A new design-time hook has been added in ASP.NET Core 2.0's default template.</span></span> <span data-ttu-id="9c2b6-127">静的メソッド `Program.BuildWebHost` を使用すると、デザイン時に EF Core がアプリケーションのサービスプロバイダーにアクセスするのを可能にすることができます。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-127">The static `Program.BuildWebHost` method enables EF Core to access the application's service provider at design time.</span></span> <span data-ttu-id="9c2b6-128">ASP.NET Core 1.x アプリケーションをアップグレードする場合は、次のように `Program` クラスを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-128">If you are upgrading an ASP.NET Core 1.x application, you will need to update the `Program` class to resemble the following.</span></span>

```csharp
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

<span data-ttu-id="9c2b6-129">アプリケーションを 2.0 に更新するとき、この新しいパターンを採用することを強くお勧めします。このことは、Entity Framework Core Migrations などの製品機能を動作させるために必要です。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-129">The adoption of this new pattern when updating applications to 2.0 is highly recommended and is required in order for product features like Entity Framework Core Migrations to work.</span></span> <span data-ttu-id="9c2b6-130">もう 1 つの一般的な方法は、 [*IDesignTimeDbContextFactory\<TContext> を実装する*](xref:core/cli/dbcontext-creation#from-a-design-time-factory)ことです。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-130">The other common alternative is to [implement *IDesignTimeDbContextFactory\<TContext>*](xref:core/cli/dbcontext-creation#from-a-design-time-factory).</span></span>

## <a name="idbcontextfactory-renamed"></a><span data-ttu-id="9c2b6-131">IDbContextFactory の名前変更</span><span class="sxs-lookup"><span data-stu-id="9c2b6-131">IDbContextFactory renamed</span></span>

<span data-ttu-id="9c2b6-132">さまざまなアプリケーション パターンをサポートし、デザイン時の `DbContext` の使用方法をユーザーがより細かく制御できるようにするために、以前に `IDbContextFactory<TContext>` インターフェイスを用意しました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-132">In order to support diverse application patterns and give users more control over how their `DbContext` is used at design time, we have, in the past, provided the `IDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="9c2b6-133">デザイン時に、EF Core ツールでは、プロジェクト内のこのインターフェイスの実装を検出し、それを使用して `DbContext` オブジェクトが作成されます。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-133">At design-time, the EF Core tools will discover implementations of this interface in your project and use it to create `DbContext` objects.</span></span>

<span data-ttu-id="9c2b6-134">このインターフェイスには非常に一般的な名前が付いているため、一部のユーザーが誤って他の `DbContext` 作成シナリオでそれを再利用しようとすることがありました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-134">This interface had a very general name which mislead some users to try re-using it for other `DbContext`-creating scenarios.</span></span> <span data-ttu-id="9c2b6-135">デザイン時に EF ツールが彼らの実装を使用することを試み、それにより `Update-Database` や `dotnet ef database update` などのコマンドが失敗したとき、彼らは予期せぬ出来事に驚かされていました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-135">They were caught off guard when the EF Tools then tried to use their implementation at design-time and caused commands like `Update-Database` or `dotnet ef database update` to fail.</span></span>

<span data-ttu-id="9c2b6-136">このインターフェイスの強力なデザイン時セマンティクスとやりとりするために、その名前を `IDesignTimeDbContextFactory<TContext>` に変更しました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-136">In order to communicate the strong design-time semantics of this interface, we have renamed it to `IDesignTimeDbContextFactory<TContext>`.</span></span>

<span data-ttu-id="9c2b6-137">2\.0 リリースで、`IDbContextFactory<TContext>` はまだ存在していますが、非推奨とマークされています。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-137">For the 2.0 release the `IDbContextFactory<TContext>` still exists but is marked as obsolete.</span></span>

## <a name="dbcontextfactoryoptions-removed"></a><span data-ttu-id="9c2b6-138">DbContextFactoryOptions の削除</span><span class="sxs-lookup"><span data-stu-id="9c2b6-138">DbContextFactoryOptions removed</span></span>

<span data-ttu-id="9c2b6-139">上記で説明した ASP.NET Core 2.0 の変更のため、新しい `IDesignTimeDbContextFactory<TContext>` インターフェイス上で `DbContextFactoryOptions` が不要になったことがわかりました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-139">Because of the ASP.NET Core 2.0 changes described above, we found that `DbContextFactoryOptions` was no longer needed on the new `IDesignTimeDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="9c2b6-140">代わりに、使用する必要がある代替手段を次に示します。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-140">Here are the alternatives you should be using instead.</span></span>

| <span data-ttu-id="9c2b6-141">DbContextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="9c2b6-141">DbContextFactoryOptions</span></span> | <span data-ttu-id="9c2b6-142">代替手段</span><span class="sxs-lookup"><span data-stu-id="9c2b6-142">Alternative</span></span>                                                  |
|:------------------------|:-------------------------------------------------------------|
| <span data-ttu-id="9c2b6-143">ApplicationBasePath</span><span class="sxs-lookup"><span data-stu-id="9c2b6-143">ApplicationBasePath</span></span>     | <span data-ttu-id="9c2b6-144">AppContext.BaseDirectory</span><span class="sxs-lookup"><span data-stu-id="9c2b6-144">AppContext.BaseDirectory</span></span>                                     |
| <span data-ttu-id="9c2b6-145">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="9c2b6-145">ContentRootPath</span></span>         | <span data-ttu-id="9c2b6-146">Directory.GetCurrentDirectory()</span><span class="sxs-lookup"><span data-stu-id="9c2b6-146">Directory.GetCurrentDirectory()</span></span>                              |
| <span data-ttu-id="9c2b6-147">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="9c2b6-147">EnvironmentName</span></span>         | <span data-ttu-id="9c2b6-148">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span><span class="sxs-lookup"><span data-stu-id="9c2b6-148">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span></span> |

## <a name="design-time-working-directory-changed"></a><span data-ttu-id="9c2b6-149">デザイン時の作業ディレクトリの変更</span><span class="sxs-lookup"><span data-stu-id="9c2b6-149">Design-time working directory changed</span></span>

<span data-ttu-id="9c2b6-150">ASP.NET Core 2.0 の変更では、`dotnet ef` が使用する作業ディレクトリを、アプリケーションの実行時に Visual Studio が使用する作業ディレクトリに合わせることも必要でした。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-150">The ASP.NET Core 2.0 changes also required the working directory used by `dotnet ef` to align with the working directory used by Visual Studio when running your application.</span></span> <span data-ttu-id="9c2b6-151">このことの観測可能な副作用の 1 つは、SQLite ファイル名が、以前のように出力ディレクトリに対してではなく、プロジェクト ディレクトリに対して相対的になったことです。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-151">One observable side effect of this is that SQLite filenames are now relative to the project directory and not the output directory like they used to be.</span></span>

## <a name="ef-core-20-requires-a-20-database-provider"></a><span data-ttu-id="9c2b6-152">EF Core 2.0 には 2.0 データベース プロバイダーが必要</span><span class="sxs-lookup"><span data-stu-id="9c2b6-152">EF Core 2.0 requires a 2.0 database provider</span></span>

<span data-ttu-id="9c2b6-153">EF Core 2.0 では、データベース プロバイダーの動作方法について、多くの簡素化と改善が行われています。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-153">For EF Core 2.0 we have made many simplifications and improvements in the way database providers work.</span></span> <span data-ttu-id="9c2b6-154">これは 1.0.x および 1.1.x プロバイダーが EF Core 2.0 で動作しなくなることを意味します。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-154">This means that 1.0.x and 1.1.x providers will not work with EF Core 2.0.</span></span>

<span data-ttu-id="9c2b6-155">SQL Server および SQLite プロバイダーは EF チームによって出荷され、2.0 バージョンは 2.0 リリースの一部として提供されます。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-155">The SQL Server and SQLite providers are shipped by the EF team and 2.0 versions will be available as part of the 2.0 release.</span></span> <span data-ttu-id="9c2b6-156">[SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、[PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、および [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) 用のオープンソースのサードパーティ プロバイダーは、2.0 に対して更新されています。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-156">The open-source third party providers for [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), and [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) are being updated for 2.0.</span></span> <span data-ttu-id="9c2b6-157">他のすべてのプロバイダーについては、プロバイダーの作成者にお問い合わせください。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-157">For all other providers, please contact the provider writer.</span></span>

## <a name="logging-and-diagnostics-events-have-changed"></a><span data-ttu-id="9c2b6-158">ログおよび診断イベントの変更</span><span class="sxs-lookup"><span data-stu-id="9c2b6-158">Logging and Diagnostics events have changed</span></span>

<span data-ttu-id="9c2b6-159">注: これらの変更は、ほとんどのアプリケーション コードに影響ありません。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-159">Note: these changes should not impact most application code.</span></span>

<span data-ttu-id="9c2b6-160">[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) に送信されるメッセージのイベント ID が 2.0 で変更されました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-160">The event IDs for messages sent to an [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) have changed in 2.0.</span></span> <span data-ttu-id="9c2b6-161">イベント ID が EF Core コード全体で一意になりました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-161">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="9c2b6-162">これらのメッセージはまた、たとえば MVC で使用される構造化されたログ記録の標準パターンに従います。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-162">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="9c2b6-163">ロガー カテゴリも変更されました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-163">Logger categories have also changed.</span></span> <span data-ttu-id="9c2b6-164">現在、[DbLoggerCategory](https://github.com/dotnet/efcore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs) 経由でアクセスされる既知のカテゴリ セットがあります。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-164">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/dotnet/efcore/blob/rel/2.0.0/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="9c2b6-165">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) イベントでは、それに対応する `ILogger` メッセージと同じイベント ID 名が使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-165">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) events now use the same event ID names as the corresponding `ILogger` messages.</span></span> <span data-ttu-id="9c2b6-166">イベント ペイロードはすべて、[EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata) から派生する標準型です。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-166">The event payloads are all nominal types derived from [EventData](/dotnet/api/microsoft.entityframeworkcore.diagnostics.eventdata).</span></span>

<span data-ttu-id="9c2b6-167">イベント ID、ペイロードの種類、およびカテゴリは、[CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) および [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) クラスに記載されています。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-167">Event IDs, payload types, and categories are documented in the [CoreEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.coreeventid) and the [RelationalEventId](/dotnet/api/microsoft.entityframeworkcore.diagnostics.relationaleventid) classes.</span></span>

<span data-ttu-id="9c2b6-168">ID もまた、Microsoft.EntityFrameworkCore.Infrastructure から新しい Microsoft.EntityFrameworkCore.Diagnostics 名前空間に移動されています。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-168">IDs have also moved from Microsoft.EntityFrameworkCore.Infrastructure to the new Microsoft.EntityFrameworkCore.Diagnostics namespace.</span></span>

## <a name="ef-core-relational-metadata-api-changes"></a><span data-ttu-id="9c2b6-169">EF Core リレーショナル メタデータ API の変更</span><span class="sxs-lookup"><span data-stu-id="9c2b6-169">EF Core relational metadata API changes</span></span>

<span data-ttu-id="9c2b6-170">EF Core 2.0 は、使用されるプロバイダーごとに異なる [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) をビルドするようになりました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-170">EF Core 2.0 will now build a different [IModel](/dotnet/api/microsoft.entityframeworkcore.metadata.imodel) for each different provider being used.</span></span> <span data-ttu-id="9c2b6-171">これは通常、アプリケーションに対して透過的です。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-171">This is usually transparent to the application.</span></span> <span data-ttu-id="9c2b6-172">下位レベルのメタデータ API が単純になり、 _一般的なリレーショナル メタデータ コンセプト_ へのあらゆるアクセスが `.SqlServer` や `.Sqlite` などではなく、`.Relational` の呼び出しで常に行われます。たとえば、次のような 1.1.x コードがあるとします。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-172">This has facilitated a simplification of lower-level metadata APIs such that any access to _common relational metadata concepts_ is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc. For example, 1.1.x code like this:</span></span>

```csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

<span data-ttu-id="9c2b6-173">次のように記述されるようになります。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-173">Should now be written like this:</span></span>

```csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

<span data-ttu-id="9c2b6-174">`ForSqlServerToTable` のようなメソッドを使用する代わりに、拡張メソッドを使用できるようになり、現在使用しているプロバイダーに基づいて条件付きコードを記述できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-174">Instead of using methods like `ForSqlServerToTable`, extension methods are now available to write conditional code based on the current provider in use.</span></span> <span data-ttu-id="9c2b6-175">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-175">For example:</span></span>

```csharp
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

<span data-ttu-id="9c2b6-176">この変更は _すべての_ リレーショナル プロバイダーに対して定義されている API およびメタデータにのみ適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-176">Note that this change only applies to APIs/metadata that is defined for _all_ relational providers.</span></span> <span data-ttu-id="9c2b6-177">API とメタデータは、1 つのプロバイダーのみに限定されている場合、変りありません。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-177">The API and metadata remains the same when it is specific to only a single provider.</span></span> <span data-ttu-id="9c2b6-178">たとえば、クラスター化インデックスは SQL Server に固有のものであるため、`ForSqlServerIsClustered` と `.SqlServer().IsClustered()` を引き続き使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-178">For example, clustered indexes are specific to SQL Sever, so `ForSqlServerIsClustered` and  `.SqlServer().IsClustered()` must still be used.</span></span>

## <a name="dont-take-control-of-the-ef-service-provider"></a><span data-ttu-id="9c2b6-179">EF サービス プロバイダーを制御しない</span><span class="sxs-lookup"><span data-stu-id="9c2b6-179">Don’t take control of the EF service provider</span></span>

<span data-ttu-id="9c2b6-180">EF Core では、その内部実装に内部 `IServiceProvider` (依存関係挿入コンテナー) が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-180">EF Core uses an internal `IServiceProvider` (a dependency injection container) for its internal implementation.</span></span> <span data-ttu-id="9c2b6-181">アプリケーションでは、特別な場合を除き、EF Core によるこのプロバイダーの作成および管理を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-181">Applications should allow EF Core to create and manage this provider except in special cases.</span></span> <span data-ttu-id="9c2b6-182">`UseInternalServiceProvider` の呼び出しを削除することを強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-182">Strongly consider removing any calls to `UseInternalServiceProvider`.</span></span> <span data-ttu-id="9c2b6-183">アプリケーションで `UseInternalServiceProvider` を呼び出す必要がある場合は、こちらでお客様のシナリオに対処するための他の方法を調査できるように、[問題の報告](https://github.com/dotnet/efcore/Issues)を検討してください。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-183">If an application does need to call `UseInternalServiceProvider`, then please consider [filing an issue](https://github.com/dotnet/efcore/Issues) so we can investigate other ways to handle your scenario.</span></span>

<span data-ttu-id="9c2b6-184">アプリケーション コードでは、`UseInternalServiceProvider` の呼び出しが行われない限り、`AddEntityFramework` や `AddEntityFrameworkSqlServer` などの呼び出しを行う必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-184">Calling `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. is not required by application code unless `UseInternalServiceProvider` is also called.</span></span> <span data-ttu-id="9c2b6-185">`AddEntityFramework` や `AddEntityFrameworkSqlServer` などの既存の呼び出しは削除します。`AddDbContext` は、前と同じ方法で使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-185">Remove any existing calls to `AddEntityFramework` or `AddEntityFrameworkSqlServer`, etc. `AddDbContext` should still be used in the same way as before.</span></span>

## <a name="in-memory-databases-must-be-named"></a><span data-ttu-id="9c2b6-186">インメモリ データベースには名前を付ける必要があります</span><span class="sxs-lookup"><span data-stu-id="9c2b6-186">In-memory databases must be named</span></span>

<span data-ttu-id="9c2b6-187">名前のないグローバルなインメモリ データベースは削除されているので、代わりに、すべてのインメモリ データベースに名前を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-187">The global unnamed in-memory database has been removed and instead all in-memory databases must be named.</span></span> <span data-ttu-id="9c2b6-188">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-188">For example:</span></span>

```csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

<span data-ttu-id="9c2b6-189">これにより、"MyDatabase" という名前のデータベースが作成および使用されます。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-189">This creates/uses a database with the name “MyDatabase”.</span></span> <span data-ttu-id="9c2b6-190">同じ名前で `UseInMemoryDatabase` が再度呼び出されると、同じインメモリ データベースが使用されるので、それを複数のコンテキスト インスタンスで共有することができます。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-190">If `UseInMemoryDatabase` is called again with the same name, then the same in-memory database will be used, allowing it to be shared by multiple context instances.</span></span>

## <a name="read-only-api-changes"></a><span data-ttu-id="9c2b6-191">読み取り専用 API の変更</span><span class="sxs-lookup"><span data-stu-id="9c2b6-191">Read-only API changes</span></span>

<span data-ttu-id="9c2b6-192">`IsReadOnlyBeforeSave`、`IsReadOnlyAfterSave`、`IsStoreGeneratedAlways` は廃止され、[BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) および [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior) に置き換えられています。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-192">`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave`, and `IsStoreGeneratedAlways` have been obsoleted and replaced with [BeforeSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.beforesavebehavior) and [AfterSaveBehavior](/dotnet/api/microsoft.entityframeworkcore.metadata.iproperty.aftersavebehavior).</span></span> <span data-ttu-id="9c2b6-193">これらの動作は、(ストア生成プロパティだけでなく) 任意のプロパティに適用され、データベース行 (`BeforeSaveBehavior`) に挿入するとき、または既存のデータベース行 (`AfterSaveBehavior`) を更新するときにプロパティの値をどのように使用すべきかを決定します。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-193">These behaviors apply to any property (not only store-generated properties) and determine how the value of the property should be used when inserting into a database row (`BeforeSaveBehavior`) or when updating an existing database row (`AfterSaveBehavior`).</span></span>

<span data-ttu-id="9c2b6-194">[ValueGenerated.OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) とマークされたプロパティ (たとえば、計算列の場合) では、プロパティに現在設定されている値が既定で無視されます。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-194">Properties marked as [ValueGenerated.OnAddOrUpdate](/dotnet/api/microsoft.entityframeworkcore.metadata.valuegenerated) (for example, for computed columns) will by default ignore any value currently set on the property.</span></span> <span data-ttu-id="9c2b6-195">これは、追跡対象のエンティティ上で値が設定または変更されているかどうかに関係なく、ストア生成の値が常に取得されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-195">This means that a store-generated value will always be obtained regardless of whether any value has been set or modified on the tracked entity.</span></span> <span data-ttu-id="9c2b6-196">これを変更するには、異なる `Before\AfterSaveBehavior` を設定します。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-196">This can be changed by setting a different `Before\AfterSaveBehavior`.</span></span>

## <a name="new-clientsetnull-delete-behavior"></a><span data-ttu-id="9c2b6-197">新しい ClientSetNull の削除動作</span><span class="sxs-lookup"><span data-stu-id="9c2b6-197">New ClientSetNull delete behavior</span></span>

<span data-ttu-id="9c2b6-198">以前のリリースで、[DeleteBehavior.Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) の動作は、`SetNull` セマンティクスにより限定的に一致するコンテキストによって追跡されるエンティティに対するものでした。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-198">In previous releases, [DeleteBehavior.Restrict](/dotnet/api/microsoft.entityframeworkcore.deletebehavior) had a behavior for entities tracked by the context that more closed matched `SetNull` semantics.</span></span> <span data-ttu-id="9c2b6-199">EF Core 2.0 では、新しい `ClientSetNull` 動作が、オプションのリレーションシップの既定値として導入されました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-199">In EF Core 2.0, a new `ClientSetNull` behavior has been introduced as the default for optional relationships.</span></span> <span data-ttu-id="9c2b6-200">この動作には、追跡対象エンティティに対する `SetNull` セマンティクスと、EF Core を使用して作成されたデータベースに対する `Restrict` 動作があります。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-200">This behavior has `SetNull` semantics for tracked entities and `Restrict` behavior for databases created using EF Core.</span></span> <span data-ttu-id="9c2b6-201">経験上で、これらは追跡対象のエンティティとデータベースに対して最も期待される、または役に立つ動作です。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-201">In our experience, these are the most expected/useful behaviors for tracked entities and the database.</span></span> <span data-ttu-id="9c2b6-202">`DeleteBehavior.Restrict` は、オプションのリレーションシップに設定された場合、追跡対象エンティティに対して使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-202">`DeleteBehavior.Restrict` is now honored for tracked entities when set for optional relationships.</span></span>

## <a name="provider-design-time-packages-removed"></a><span data-ttu-id="9c2b6-203">プロバイダーのデザイン時パッケージの削除</span><span class="sxs-lookup"><span data-stu-id="9c2b6-203">Provider design-time packages removed</span></span>

<span data-ttu-id="9c2b6-204">`Microsoft.EntityFrameworkCore.Relational.Design` パッケージは削除されました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-204">The `Microsoft.EntityFrameworkCore.Relational.Design` package has been removed.</span></span> <span data-ttu-id="9c2b6-205">そのコンテンツは `Microsoft.EntityFrameworkCore.Relational` と `Microsoft.EntityFrameworkCore.Design` に統合されました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-205">It's contents were consolidated into `Microsoft.EntityFrameworkCore.Relational` and `Microsoft.EntityFrameworkCore.Design`.</span></span>

<span data-ttu-id="9c2b6-206">これは、プロバイダーのデザイン時パッケージに反映されます。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-206">This propagates into the provider design-time packages.</span></span> <span data-ttu-id="9c2b6-207">それらのパッケージ (`Microsoft.EntityFrameworkCore.Sqlite.Design` や `Microsoft.EntityFrameworkCore.SqlServer.Design` など) は削除され、その内容はメイン プロバイダー パッケージに統合されました。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-207">Those packages (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) were removed and their contents consolidated into the main provider packages.</span></span>

<span data-ttu-id="9c2b6-208">EF Core 2.0 で `Scaffold-DbContext` または `dotnet ef dbcontext scaffold` を有効にするには、1 つのプロバイダー パッケージを参照するだけで済みます。</span><span class="sxs-lookup"><span data-stu-id="9c2b6-208">To enable `Scaffold-DbContext` or `dotnet ef dbcontext scaffold` in EF Core 2.0, you only need to reference the single provider package:</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
