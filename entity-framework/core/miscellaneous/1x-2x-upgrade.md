---
title: 以前のバージョンから EF Core 2 EF Core へのアップグレード
author: divega
ms.date: 08/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: 1222f10811914f65822a49e18522c287ece12174
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306501"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a><span data-ttu-id="5edc2-102">以前のバージョンから EF Core 2.0 にアプリケーションをアップグレードする</span><span class="sxs-lookup"><span data-stu-id="5edc2-102">Upgrading applications from previous versions to EF Core 2.0</span></span>

<span data-ttu-id="5edc2-103">既存の Api と動作を2.0 で大幅に改善する機会を得ました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-103">We have taken the opportunity to significantly refine our existing APIs and behaviors in 2.0.</span></span> <span data-ttu-id="5edc2-104">既存のアプリケーションコードの変更が必要になることはいくつかありますが、ほとんどのアプリケーションでは影響が少ないと考えられますが、ほとんどの場合、再コンパイルだけで、最新ではない Api を置き換えるための最小限のガイド変更が必要です。</span><span class="sxs-lookup"><span data-stu-id="5edc2-104">There are a few improvements that can require modifying existing application code, although we believe that for the majority of applications the impact will be low, in most cases requiring just recompilation and minimal guided changes to replace obsolete APIs.</span></span>

<span data-ttu-id="5edc2-105">既存のアプリケーションを EF Core 2.0 に更新する場合、次のことが必要になることがあります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-105">Updating an existing application to EF Core 2.0 may require:</span></span>

1. <span data-ttu-id="5edc2-106">アプリケーションのターゲット .NET 実装を、.NET Standard 2.0 をサポートするものにアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="5edc2-106">Upgrading the target .NET implementation of the application to one that supports .NET Standard 2.0.</span></span> <span data-ttu-id="5edc2-107">詳細については、「[サポートされる .net の実装](../platforms/index.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5edc2-107">See [Supported .NET Implementations](../platforms/index.md) for more details.</span></span>

2. <span data-ttu-id="5edc2-108">EF Core 2.0 と互換性のあるターゲットデータベースのプロバイダーを特定します。</span><span class="sxs-lookup"><span data-stu-id="5edc2-108">Identify a provider for the target database which is compatible with EF Core 2.0.</span></span> <span data-ttu-id="5edc2-109">EF Core 2.0 を参照してください。次の[2.0 データベースプロバイダーが必要](#ef-core-20-requires-a-20-database-provider)です。</span><span class="sxs-lookup"><span data-stu-id="5edc2-109">See [EF Core 2.0 requires a 2.0 database provider](#ef-core-20-requires-a-20-database-provider) below.</span></span>

3. <span data-ttu-id="5edc2-110">すべての EF Core パッケージ (ランタイムおよびツール) を2.0 にアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="5edc2-110">Upgrading all the EF Core packages (runtime and tooling) to 2.0.</span></span> <span data-ttu-id="5edc2-111">詳細については、「 [EF Core のインストール](../get-started/install/index.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5edc2-111">Refer to [Installing EF Core](../get-started/install/index.md) for more details.</span></span>

4. <span data-ttu-id="5edc2-112">このドキュメントの残りの部分で説明されている互換性に影響する変更を補うために、必要なコード変更を行います。</span><span class="sxs-lookup"><span data-stu-id="5edc2-112">Make any necessary code changes to compensate for the breaking changes described in the rest of this document.</span></span>

## <a name="aspnet-core-now-includes-ef-core"></a><span data-ttu-id="5edc2-113">ASP.NET Core に EF Core が含まれるようになりました</span><span class="sxs-lookup"><span data-stu-id="5edc2-113">ASP.NET Core now includes EF Core</span></span>

<span data-ttu-id="5edc2-114">ASP.NET Core 2.0 を対象とするアプリケーションは、サードパーティ データベース プロバイダー以外の依存関係を追加せずに EF Core 2.0 を使用できます。</span><span class="sxs-lookup"><span data-stu-id="5edc2-114">Applications targeting ASP.NET Core 2.0 can use EF Core 2.0 without additional dependencies besides third party database providers.</span></span> <span data-ttu-id="5edc2-115">ただし、以前のバージョンの ASP.NET Core を対象とするアプリケーションでは、EF Core 2.0 を使用するために ASP.NET Core 2.0 にアップグレードする必要があります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-115">However, applications targeting previous versions of ASP.NET Core need to upgrade to ASP.NET Core 2.0 in order to use EF Core 2.0.</span></span> <span data-ttu-id="5edc2-116">ASP.NET Core アプリケーションを2.0 にアップグレードする方法の詳細については[、ASP.NET Core のドキュメント](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="5edc2-116">For more details on upgrading ASP.NET Core applications to 2.0 see [the ASP.NET Core documentation on the subject](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/).</span></span>

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a><span data-ttu-id="5edc2-117">ASP.NET Core でアプリケーションサービスを取得するための新しい方法</span><span class="sxs-lookup"><span data-stu-id="5edc2-117">New way of getting application services in ASP.NET Core</span></span>

<span data-ttu-id="5edc2-118">ASP.NET Core web アプリケーションで推奨されるパターンは、1. x で使用 EF Core デザイン時のロジックが破損しているように2.0 に更新されています。</span><span class="sxs-lookup"><span data-stu-id="5edc2-118">The recommended pattern for ASP.NET Core web applications has been updated for 2.0 in a way that broke the design-time logic EF Core used in 1.x.</span></span> <span data-ttu-id="5edc2-119">以前はデザイン時に、EF Core はアプリケーションのサービス`Startup.ConfigureServices`プロバイダーにアクセスするために、を直接呼び出そうとします。</span><span class="sxs-lookup"><span data-stu-id="5edc2-119">Previously at design-time, EF Core would try to invoke `Startup.ConfigureServices` directly in order to access the application's service provider.</span></span> <span data-ttu-id="5edc2-120">ASP.NET Core 2.0 では、構成は`Startup`クラスの外部で初期化されます。</span><span class="sxs-lookup"><span data-stu-id="5edc2-120">In ASP.NET Core 2.0, Configuration is initialized outside of the `Startup` class.</span></span> <span data-ttu-id="5edc2-121">EF Core を使用するアプリケーションは通常、構成から接続文字列`Startup`にアクセスするので、それ自体では不十分です。</span><span class="sxs-lookup"><span data-stu-id="5edc2-121">Applications using EF Core typically access their connection string from Configuration, so `Startup` by itself is no longer sufficient.</span></span> <span data-ttu-id="5edc2-122">ASP.NET Core 1.x アプリケーションをアップグレードすると、EF Core ツールの使用時に次のエラーが表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-122">If you upgrade an ASP.NET Core 1.x application, you may receive the following error when using the EF Core tools.</span></span>

> <span data-ttu-id="5edc2-123">' ApplicationContext ' にパラメーターなしのコンストラクターが見つかりませんでした。</span><span class="sxs-lookup"><span data-stu-id="5edc2-123">No parameterless constructor was found on 'ApplicationContext'.</span></span> <span data-ttu-id="5edc2-124">パラメーターなしのコンストラクターを ' applicationcontext ' に追加するか、' applicationcontext ' と同じアセンブリに&lt;' idesigntimedbcontextfactory applicationcontext&gt;' の実装を追加してください</span><span class="sxs-lookup"><span data-stu-id="5edc2-124">Either add a parameterless constructor to 'ApplicationContext' or add an implementation of 'IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' in the same assembly as 'ApplicationContext'</span></span>

<span data-ttu-id="5edc2-125">新しいデザイン時フックが ASP.NET Core 2.0 の既定のテンプレートに追加されました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-125">A new design-time hook has been added in ASP.NET Core 2.0's default template.</span></span> <span data-ttu-id="5edc2-126">静的`Program.BuildWebHost`なメソッドを使用すると、デザイン時にアプリケーションのサービスプロバイダーにアクセスする EF Core できます。</span><span class="sxs-lookup"><span data-stu-id="5edc2-126">The static `Program.BuildWebHost` method enables EF Core to access the application's service provider at design time.</span></span> <span data-ttu-id="5edc2-127">ASP.NET Core 1.x アプリケーションをアップグレードする場合は、次のように`Program`クラスを更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-127">If you are upgrading an ASP.NET Core 1.x application, you will need to update the `Program` class to resemble the following.</span></span>

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

<span data-ttu-id="5edc2-128">アプリケーションを2.0 に更新するときにこの新しいパターンを採用することを強くお勧めします。 Entity Framework Core の移行などの製品機能を使用するには、この新しいパターンが必要です。</span><span class="sxs-lookup"><span data-stu-id="5edc2-128">The adoption of this new pattern when updating applications to 2.0 is highly recommended and is required in order for product features like Entity Framework Core Migrations to work.</span></span> <span data-ttu-id="5edc2-129">もう1つの一般的な方法は、 [ *\<idesigntimedbcontextfactory tcontext >* を実装](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory)することです。</span><span class="sxs-lookup"><span data-stu-id="5edc2-129">The other common alternative is to [implement *IDesignTimeDbContextFactory\<TContext>*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).</span></span>

## <a name="idbcontextfactory-renamed"></a><span data-ttu-id="5edc2-130">IDbContextFactory の名前変更</span><span class="sxs-lookup"><span data-stu-id="5edc2-130">IDbContextFactory renamed</span></span>

<span data-ttu-id="5edc2-131">さまざまなアプリケーションパターンをサポートし、ユーザー `DbContext`がデザイン時の使用方法をより細かく制御できるようにするために、以前は`IDbContextFactory<TContext>`インターフェイスを提供してきました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-131">In order to support diverse application patterns and give users more control over how their `DbContext` is used at design time, we have, in the past, provided the `IDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="5edc2-132">デザイン時に、EF Core ツールは、プロジェクト内のこのインターフェイスの実装を検出し、それを使用`DbContext`してオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="5edc2-132">At design-time, the EF Core tools will discover implementations of this interface in your project and use it to create `DbContext` objects.</span></span>

<span data-ttu-id="5edc2-133">このインターフェイスには非常に一般的な名前が付いているため、一部のユーザーが`DbContext`他の作成シナリオで再利用を試みることがあります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-133">This interface had a very general name which mislead some users to try re-using it for other `DbContext`-creating scenarios.</span></span> <span data-ttu-id="5edc2-134">EF ツールが設計時に実装を使用しようとしたときに、や`Update-Database` `dotnet ef database update`などのコマンドが失敗すると、ガードが解除されました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-134">They were caught off guard when the EF Tools then tried to use their implementation at design-time and caused commands like `Update-Database` or `dotnet ef database update` to fail.</span></span>

<span data-ttu-id="5edc2-135">このインターフェイスの強力なデザイン時セマンティクスを伝えるために、名前をに`IDesignTimeDbContextFactory<TContext>`変更しました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-135">In order to communicate the strong design-time semantics of this interface, we have renamed it to `IDesignTimeDbContextFactory<TContext>`.</span></span>

<span data-ttu-id="5edc2-136">2\.0 リリースでは、 `IDbContextFactory<TContext>`は引き続き存在しますが、不使用とマークされています。</span><span class="sxs-lookup"><span data-stu-id="5edc2-136">For the 2.0 release the `IDbContextFactory<TContext>` still exists but is marked as obsolete.</span></span>

## <a name="dbcontextfactoryoptions-removed"></a><span data-ttu-id="5edc2-137">削除された DbContextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="5edc2-137">DbContextFactoryOptions removed</span></span>

<span data-ttu-id="5edc2-138">上記で説明した ASP.NET Core 2.0 の変更により、 `DbContextFactoryOptions`新しい`IDesignTimeDbContextFactory<TContext>`インターフェイスでは不要になったことがわかりました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-138">Because of the ASP.NET Core 2.0 changes described above, we found that `DbContextFactoryOptions` was no longer needed on the new `IDesignTimeDbContextFactory<TContext>` interface.</span></span> <span data-ttu-id="5edc2-139">代わりに、次の代替手段を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-139">Here are the alternatives you should be using instead.</span></span>

| <span data-ttu-id="5edc2-140">DbContextFactoryOptions</span><span class="sxs-lookup"><span data-stu-id="5edc2-140">DbContextFactoryOptions</span></span> | <span data-ttu-id="5edc2-141">代替</span><span class="sxs-lookup"><span data-stu-id="5edc2-141">Alternative</span></span>                                                  |
|:------------------------|:-------------------------------------------------------------|
| <span data-ttu-id="5edc2-142">ApplicationBasePath</span><span class="sxs-lookup"><span data-stu-id="5edc2-142">ApplicationBasePath</span></span>     | <span data-ttu-id="5edc2-143">AppContext.BaseDirectory</span><span class="sxs-lookup"><span data-stu-id="5edc2-143">AppContext.BaseDirectory</span></span>                                     |
| <span data-ttu-id="5edc2-144">ContentRootPath</span><span class="sxs-lookup"><span data-stu-id="5edc2-144">ContentRootPath</span></span>         | <span data-ttu-id="5edc2-145">Directory.GetCurrentDirectory()</span><span class="sxs-lookup"><span data-stu-id="5edc2-145">Directory.GetCurrentDirectory()</span></span>                              |
| <span data-ttu-id="5edc2-146">EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="5edc2-146">EnvironmentName</span></span>         | <span data-ttu-id="5edc2-147">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span><span class="sxs-lookup"><span data-stu-id="5edc2-147">Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT")</span></span> |

## <a name="design-time-working-directory-changed"></a><span data-ttu-id="5edc2-148">デザイン時の作業ディレクトリが変更されました</span><span class="sxs-lookup"><span data-stu-id="5edc2-148">Design-time working directory changed</span></span>

<span data-ttu-id="5edc2-149">ASP.NET Core 2.0 の変更では、アプリケーションの実行時`dotnet ef`に Visual Studio によって使用される作業ディレクトリに合わせるために、で使用される作業ディレクトリも必要でした。</span><span class="sxs-lookup"><span data-stu-id="5edc2-149">The ASP.NET Core 2.0 changes also required the working directory used by `dotnet ef` to align with the working directory used by Visual Studio when running your application.</span></span> <span data-ttu-id="5edc2-150">これは、SQLite ファイル名がプロジェクトディレクトリに対して相対的であり、のような出力ディレクトリではないという副作用の1つです。</span><span class="sxs-lookup"><span data-stu-id="5edc2-150">One observable side effect of this is that SQLite filenames are now relative to the project directory and not the output directory like they used to be.</span></span>

## <a name="ef-core-20-requires-a-20-database-provider"></a><span data-ttu-id="5edc2-151">EF Core 2.0 には2.0 データベースプロバイダーが必要です</span><span class="sxs-lookup"><span data-stu-id="5edc2-151">EF Core 2.0 requires a 2.0 database provider</span></span>

<span data-ttu-id="5edc2-152">EF Core 2.0 では、データベースプロバイダーの動作方法について、多くの簡素化と改善が行われています。</span><span class="sxs-lookup"><span data-stu-id="5edc2-152">For EF Core 2.0 we have made many simplifications and improvements in the way database providers work.</span></span> <span data-ttu-id="5edc2-153">これは、1.0. x および 1.1. x プロバイダーが EF Core 2.0 で動作しないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="5edc2-153">This means that 1.0.x and 1.1.x providers will not work with EF Core 2.0.</span></span>

<span data-ttu-id="5edc2-154">SQL Server および SQLite プロバイダーは EF チームによって出荷され、2.0 バージョンは2.0 リリースの一部として提供されます。</span><span class="sxs-lookup"><span data-stu-id="5edc2-154">The SQL Server and SQLite providers are shipped by the EF team and 2.0 versions will be available as part of the 2.0 release.</span></span> <span data-ttu-id="5edc2-155">[SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)、 [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、および[MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)のオープンソースのサードパーティプロバイダーは2.0 用に更新されています。</span><span class="sxs-lookup"><span data-stu-id="5edc2-155">The open-source third party providers for [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), and [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) are being updated for 2.0.</span></span> <span data-ttu-id="5edc2-156">他のすべてのプロバイダーについては、プロバイダーの作成者にお問い合わせください。</span><span class="sxs-lookup"><span data-stu-id="5edc2-156">For all other providers, please contact the provider writer.</span></span>

## <a name="logging-and-diagnostics-events-have-changed"></a><span data-ttu-id="5edc2-157">ログ記録と診断イベントが変更されました</span><span class="sxs-lookup"><span data-stu-id="5edc2-157">Logging and Diagnostics events have changed</span></span>

<span data-ttu-id="5edc2-158">注: これらの変更は、ほとんどのアプリケーションコードに影響しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="5edc2-158">Note: these changes should not impact most application code.</span></span>

<span data-ttu-id="5edc2-159">[ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs)に送信されたメッセージのイベント id が2.0 で変更されました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-159">The event IDs for messages sent to an [ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs) have changed in 2.0.</span></span> <span data-ttu-id="5edc2-160">イベント ID が EF Core コード全体で一意になりました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-160">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="5edc2-161">これらのメッセージはまた、たとえば MVC で使用される構造化されたログ記録の標準パターンに従います。</span><span class="sxs-lookup"><span data-stu-id="5edc2-161">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="5edc2-162">ロガー カテゴリも変更されました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-162">Logger categories have also changed.</span></span> <span data-ttu-id="5edc2-163">現在、[DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs) 経由でアクセスされる既知のカテゴリ セットがあります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-163">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="5edc2-164">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md)イベントでは、対応する`ILogger`メッセージと同じイベント ID 名が使用されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-164">[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) events now use the same event ID names as the corresponding `ILogger` messages.</span></span> <span data-ttu-id="5edc2-165">イベントペイロードは、 [EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs)から派生したすべての公称型です。</span><span class="sxs-lookup"><span data-stu-id="5edc2-165">The event payloads are all nominal types derived from [EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs).</span></span>

<span data-ttu-id="5edc2-166">イベント Id、ペイロードの種類、およびカテゴリは、 [Coreeventid](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs)と[RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs)クラスに記載されています。</span><span class="sxs-lookup"><span data-stu-id="5edc2-166">Event IDs, payload types, and categories are documented in the [CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs) and the [RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs) classes.</span></span>

<span data-ttu-id="5edc2-167">Id は、Microsoft の EntityFrameworkCore. Infrastructure 名前空間にも移動されています。</span><span class="sxs-lookup"><span data-stu-id="5edc2-167">IDs have also moved from Microsoft.EntityFrameworkCore.Infrastructure to the new Microsoft.EntityFrameworkCore.Diagnostics namespace.</span></span>

## <a name="ef-core-relational-metadata-api-changes"></a><span data-ttu-id="5edc2-168">リレーショナルメタデータ API の変更の EF Core</span><span class="sxs-lookup"><span data-stu-id="5edc2-168">EF Core relational metadata API changes</span></span>

<span data-ttu-id="5edc2-169">EF Core 2.0 は、使用されるプロバイダーごとに異なる [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) をビルドするようになりました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-169">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="5edc2-170">これは通常、アプリケーションに対して透過的です。</span><span class="sxs-lookup"><span data-stu-id="5edc2-170">This is usually transparent to the application.</span></span> <span data-ttu-id="5edc2-171">下位レベルのメタデータ API が単純になり、_一般的なリレーショナル メタデータ コンセプト_へのあらゆるアクセスが `.SqlServer` や `.Sqlite` などではなく、`.Relational` の呼び出しで常に行われます。たとえば、次のような 1.1. x コードです。</span><span class="sxs-lookup"><span data-stu-id="5edc2-171">This has facilitated a simplification of lower-level metadata APIs such that any access to _common relational metadata concepts_ is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc. For example, 1.1.x code like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

<span data-ttu-id="5edc2-172">次のようになります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-172">Should now be written like this:</span></span>

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

<span data-ttu-id="5edc2-173">のような`ForSqlServerToTable`メソッドを使用する代わりに、拡張メソッドを使用して、現在使用されているプロバイダーに基づいて条件付きコードを記述できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-173">Instead of using methods like `ForSqlServerToTable`, extension methods are now available to write conditional code based on the current provider in use.</span></span> <span data-ttu-id="5edc2-174">例えば:</span><span class="sxs-lookup"><span data-stu-id="5edc2-174">For example:</span></span>

```C#
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

<span data-ttu-id="5edc2-175">この変更は、_すべて_のリレーショナルプロバイダーに対して定義されている api/メタデータにのみ適用されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="5edc2-175">Note that this change only applies to APIs/metadata that is defined for _all_ relational providers.</span></span> <span data-ttu-id="5edc2-176">API とメタデータは、1つのプロバイダーのみに限定されていても変わりません。</span><span class="sxs-lookup"><span data-stu-id="5edc2-176">The API and metadata remains the same when it is specific to only a single provider.</span></span> <span data-ttu-id="5edc2-177">たとえば、クラスター化インデックスは SQL server に固有のもの`ForSqlServerIsClustered`で`.SqlServer().IsClustered()`あるため、引き続き使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-177">For example, clustered indexes are specific to SQL Sever, so `ForSqlServerIsClustered` and  `.SqlServer().IsClustered()` must still be used.</span></span>

## <a name="dont-take-control-of-the-ef-service-provider"></a><span data-ttu-id="5edc2-178">EF サービスプロバイダーを制御しない</span><span class="sxs-lookup"><span data-stu-id="5edc2-178">Don’t take control of the EF service provider</span></span>

<span data-ttu-id="5edc2-179">内部実装には`IServiceProvider` 、内部 (依存関係挿入コンテナー) を使用 EF Core。</span><span class="sxs-lookup"><span data-stu-id="5edc2-179">EF Core uses an internal `IServiceProvider` (a dependency injection container) for its internal implementation.</span></span> <span data-ttu-id="5edc2-180">アプリケーションでは、特別な場合を除き、このプロバイダーを作成および管理する EF Core を許可する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-180">Applications should allow EF Core to create and manage this provider except in special cases.</span></span> <span data-ttu-id="5edc2-181">の呼び出しを削除すること`UseInternalServiceProvider`を強くお勧めします。</span><span class="sxs-lookup"><span data-stu-id="5edc2-181">Strongly consider removing any calls to `UseInternalServiceProvider`.</span></span> <span data-ttu-id="5edc2-182">アプリケーションでを呼び出す`UseInternalServiceProvider`必要がある場合は、シナリオを処理する他の方法を調査できるように、問題を[提出](https://github.com/aspnet/EntityFramework/Issues)することを検討してください。</span><span class="sxs-lookup"><span data-stu-id="5edc2-182">If an application does need to call `UseInternalServiceProvider`, then please consider [filing an issue](https://github.com/aspnet/EntityFramework/Issues) so we can investigate other ways to handle your scenario.</span></span>

<span data-ttu-id="5edc2-183">、 `AddEntityFramework`、 `AddEntityFrameworkSqlServer`などを呼び出すことは、が呼び出され`UseInternalServiceProvider`ない限り、アプリケーションコードでは必要ありません。</span><span class="sxs-lookup"><span data-stu-id="5edc2-183">Calling `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. is not required by application code unless `UseInternalServiceProvider` is also called.</span></span> <span data-ttu-id="5edc2-184">または`AddEntityFramework` `AddEntityFrameworkSqlServer`などへの既存の呼び出しを`AddDbContext`削除する場合でも、以前と同じ方法で使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-184">Remove any existing calls to `AddEntityFramework` or `AddEntityFrameworkSqlServer`, etc. `AddDbContext` should still be used in the same way as before.</span></span>

## <a name="in-memory-databases-must-be-named"></a><span data-ttu-id="5edc2-185">インメモリデータベースは、という名前にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-185">In-memory databases must be named</span></span>

<span data-ttu-id="5edc2-186">名前のないグローバルなメモリ内データベースデータベースは削除されているため、すべてのインメモリデータベースに名前を付ける必要があります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-186">The global unnamed in-memory database has been removed and instead all in-memory databases must be named.</span></span> <span data-ttu-id="5edc2-187">例えば:</span><span class="sxs-lookup"><span data-stu-id="5edc2-187">For example:</span></span>

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

<span data-ttu-id="5edc2-188">これにより、"MyDatabase" という名前のデータベースが作成/使用されます。</span><span class="sxs-lookup"><span data-stu-id="5edc2-188">This creates/uses a database with the name “MyDatabase”.</span></span> <span data-ttu-id="5edc2-189">同じ名前で再度を呼び出すと、同じメモリ内データベースが使用され、複数のコンテキストインスタンスで共有できるようになります。`UseInMemoryDatabase`</span><span class="sxs-lookup"><span data-stu-id="5edc2-189">If `UseInMemoryDatabase` is called again with the same name, then the same in-memory database will be used, allowing it to be shared by multiple context instances.</span></span>

## <a name="read-only-api-changes"></a><span data-ttu-id="5edc2-190">読み取り専用 API の変更</span><span class="sxs-lookup"><span data-stu-id="5edc2-190">Read-only API changes</span></span>

<span data-ttu-id="5edc2-191">`IsReadOnlyBeforeSave`、 `IsReadOnlyAfterSave`、および`IsStoreGeneratedAlways`は廃止され、 [BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39)と[aftersavebehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55)に置き換えられました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-191">`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave`, and `IsStoreGeneratedAlways` have been obsoleted and replaced with [BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39) and [AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55).</span></span> <span data-ttu-id="5edc2-192">これらの動作は、(ストアによって生成されるプロパティだけでなく) 任意のプロパティに適用され、データベース行`BeforeSaveBehavior`に挿入するとき、または既存のデータベース行 (`AfterSaveBehavior`) を更新するときに、プロパティの値をどのように使用するかを決定します。</span><span class="sxs-lookup"><span data-stu-id="5edc2-192">These behaviors apply to any property (not only store-generated properties) and determine how the value of the property should be used when inserting into a database row (`BeforeSaveBehavior`) or when updating an existing database row (`AfterSaveBehavior`).</span></span>

<span data-ttu-id="5edc2-193">[Valuegenerated. OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) (計算列など) としてマークされたプロパティは、既定では、プロパティに現在設定されている値を無視します。</span><span class="sxs-lookup"><span data-stu-id="5edc2-193">Properties marked as [ValueGenerated.OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) (for example, for computed columns) will by default ignore any value currently set on the property.</span></span> <span data-ttu-id="5edc2-194">これは、追跡対象のエンティティで値が設定または変更されたかどうかに関係なく、ストアによって生成される値が常に取得されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="5edc2-194">This means that a store-generated value will always be obtained regardless of whether any value has been set or modified on the tracked entity.</span></span> <span data-ttu-id="5edc2-195">これは、別`Before\AfterSaveBehavior`のを設定することによって変更できます。</span><span class="sxs-lookup"><span data-stu-id="5edc2-195">This can be changed by setting a different `Before\AfterSaveBehavior`.</span></span>

## <a name="new-clientsetnull-delete-behavior"></a><span data-ttu-id="5edc2-196">新しい ClientSetNull の削除動作</span><span class="sxs-lookup"><span data-stu-id="5edc2-196">New ClientSetNull delete behavior</span></span>

<span data-ttu-id="5edc2-197">以前のリリースでは、 [deletebehavior. 制限](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs)は、終了したセマンティクスに一致`SetNull`したコンテキストによって追跡されるエンティティに対して動作がありました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-197">In previous releases, [DeleteBehavior.Restrict](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs) had a behavior for entities tracked by the context that more closed matched `SetNull` semantics.</span></span> <span data-ttu-id="5edc2-198">EF Core 2.0 では、オプション`ClientSetNull`のリレーションシップの既定値として新しい動作が導入されました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-198">In EF Core 2.0, a new `ClientSetNull` behavior has been introduced as the default for optional relationships.</span></span> <span data-ttu-id="5edc2-199">この動作に`SetNull`は、EF Core を使用`Restrict`して作成されたデータベースの追跡対象エンティティおよび動作のセマンティクスがあります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-199">This behavior has `SetNull` semantics for tracked entities and `Restrict` behavior for databases created using EF Core.</span></span> <span data-ttu-id="5edc2-200">ここでは、追跡対象のエンティティとデータベースに対して最も期待される、または役に立つ動作を示します。</span><span class="sxs-lookup"><span data-stu-id="5edc2-200">In our experience, these are the most expected/useful behaviors for tracked entities and the database.</span></span> <span data-ttu-id="5edc2-201">`DeleteBehavior.Restrict`は、オプションのリレーションシップが設定されている場合、追跡対象エンティティに対して使用されるようになりました</span><span class="sxs-lookup"><span data-stu-id="5edc2-201">`DeleteBehavior.Restrict` is now honored for tracked entities when set for optional relationships.</span></span>

## <a name="provider-design-time-packages-removed"></a><span data-ttu-id="5edc2-202">プロバイダーのデザイン時に削除されたパッケージ</span><span class="sxs-lookup"><span data-stu-id="5edc2-202">Provider design-time packages removed</span></span>

<span data-ttu-id="5edc2-203">`Microsoft.EntityFrameworkCore.Relational.Design`パッケージが削除されました。</span><span class="sxs-lookup"><span data-stu-id="5edc2-203">The `Microsoft.EntityFrameworkCore.Relational.Design` package has been removed.</span></span> <span data-ttu-id="5edc2-204">コンテンツはと`Microsoft.EntityFrameworkCore.Relational` `Microsoft.EntityFrameworkCore.Design`に統合されています。</span><span class="sxs-lookup"><span data-stu-id="5edc2-204">It's contents were consolidated into `Microsoft.EntityFrameworkCore.Relational` and `Microsoft.EntityFrameworkCore.Design`.</span></span>

<span data-ttu-id="5edc2-205">これは、プロバイダーのデザイン時パッケージに反映されます。</span><span class="sxs-lookup"><span data-stu-id="5edc2-205">This propagates into the provider design-time packages.</span></span> <span data-ttu-id="5edc2-206">これらのパッケージ`Microsoft.EntityFrameworkCore.Sqlite.Design`( `Microsoft.EntityFrameworkCore.SqlServer.Design`、など) は削除され、その内容はメインプロバイダーパッケージに統合されています。</span><span class="sxs-lookup"><span data-stu-id="5edc2-206">Those packages (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) were removed and their contents consolidated into the main provider packages.</span></span>

<span data-ttu-id="5edc2-207">EF Core 2.0 `Scaffold-DbContext`で`dotnet ef dbcontext scaffold`またはを有効にするには、1つのプロバイダーパッケージのみを参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="5edc2-207">To enable `Scaffold-DbContext` or `dotnet ef dbcontext scaffold` in EF Core 2.0, you only need to reference the single provider package:</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
