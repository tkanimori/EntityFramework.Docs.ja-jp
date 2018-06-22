---
title: EF Core 1.0 RC1 から RC2 - EF コアへのアップグレード
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
ms.technology: entity-framework-core
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: e76886729248101ccac024568cf9abcd945fca33
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678628"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a><span data-ttu-id="a066d-102">EF Core 1.0 RC1 から RC2 1.0 へのアップグレード</span><span class="sxs-lookup"><span data-stu-id="a066d-102">Upgrading from EF Core 1.0 RC1 to 1.0 RC2</span></span>

<span data-ttu-id="a066d-103">この記事では、RC2、RC1 パッケージにビルドされたアプリケーションの移動のガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="a066d-103">This article provides guidance for moving an application built with the RC1 packages to RC2.</span></span>

## <a name="package-names-and-versions"></a><span data-ttu-id="a066d-104">パッケージの名前とバージョン</span><span class="sxs-lookup"><span data-stu-id="a066d-104">Package Names and Versions</span></span>

<span data-ttu-id="a066d-105">RC1 と RC2 では、間で変更しました"Entity Framework 7"から"Entity Framework Core"を。</span><span class="sxs-lookup"><span data-stu-id="a066d-105">Between RC1 and RC2, we changed from "Entity Framework 7" to "Entity Framework Core".</span></span> <span data-ttu-id="a066d-106">詳細の変更の理由に関する[Scott Hanselman によるこの投稿](http://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)です。</span><span class="sxs-lookup"><span data-stu-id="a066d-106">You can read more about the reasons for the change in [this post by Scott Hanselman](http://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx).</span></span> <span data-ttu-id="a066d-107">この変更のため、パッケージ名が変更された`EntityFramework.*`に`Microsoft.EntityFrameworkCore.*`と、バージョン`7.0.0-rc1-final`に`1.0.0-rc2-final`(または`1.0.0-preview1-final`ツールの)。</span><span class="sxs-lookup"><span data-stu-id="a066d-107">Because of this change, our package names changed from `EntityFramework.*` to `Microsoft.EntityFrameworkCore.*` and our versions from `7.0.0-rc1-final` to `1.0.0-rc2-final` (or `1.0.0-preview1-final` for tooling).</span></span>

<span data-ttu-id="a066d-108">**RC1 パッケージを完全に削除してから、RC2 をインストールする必要があるものです。**</span><span class="sxs-lookup"><span data-stu-id="a066d-108">**You will need to completely remove the RC1 packages and then install the RC2 ones.**</span></span> <span data-ttu-id="a066d-109">一般的なパッケージの一部のマッピングを次に示します。</span><span class="sxs-lookup"><span data-stu-id="a066d-109">Here is the mapping for some common packages.</span></span>

| <span data-ttu-id="a066d-110">RC1 パッケージ</span><span class="sxs-lookup"><span data-stu-id="a066d-110">RC1 Package</span></span>                                               | <span data-ttu-id="a066d-111">該当するショートカットは RC2</span><span class="sxs-lookup"><span data-stu-id="a066d-111">RC2 Equivalent</span></span>                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| <span data-ttu-id="a066d-112">EntityFramework.MicrosoftSqlServer 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="a066d-112">EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final</span></span> | <span data-ttu-id="a066d-113">Microsoft.EntityFrameworkCore.SqlServer 1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="a066d-113">Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="a066d-114">EntityFramework.SQLite 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="a066d-114">EntityFramework.SQLite                    7.0.0-rc1-final</span></span> | <span data-ttu-id="a066d-115">Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="a066d-115">Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="a066d-116">EntityFramework7.Npgsql                   3.1.0-rc1-3</span><span class="sxs-lookup"><span data-stu-id="a066d-116">EntityFramework7.Npgsql                   3.1.0-rc1-3</span></span>     | <span data-ttu-id="a066d-117">NpgSql.EntityFrameworkCore.Postgres             <to be advised></span><span class="sxs-lookup"><span data-stu-id="a066d-117">NpgSql.EntityFrameworkCore.Postgres             <to be advised></span></span>      |
| <span data-ttu-id="a066d-118">EntityFramework.SqlServerCompact35        7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="a066d-118">EntityFramework.SqlServerCompact35        7.0.0-rc1-final</span></span> | <span data-ttu-id="a066d-119">EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="a066d-119">EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="a066d-120">EntityFramework.SqlServerCompact40        7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="a066d-120">EntityFramework.SqlServerCompact40        7.0.0-rc1-final</span></span> | <span data-ttu-id="a066d-121">EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="a066d-121">EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="a066d-122">EntityFramework.InMemory                  7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="a066d-122">EntityFramework.InMemory                  7.0.0-rc1-final</span></span> | <span data-ttu-id="a066d-123">Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="a066d-123">Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="a066d-124">EntityFramework.IBMDataServer             7.0.0-beta1</span><span class="sxs-lookup"><span data-stu-id="a066d-124">EntityFramework.IBMDataServer             7.0.0-beta1</span></span>     | <span data-ttu-id="a066d-125">RC2 ではまだ使用できません。</span><span class="sxs-lookup"><span data-stu-id="a066d-125">Not yet available for RC2</span></span>                                            |
| <span data-ttu-id="a066d-126">EntityFramework.Commands                  7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="a066d-126">EntityFramework.Commands                  7.0.0-rc1-final</span></span> | <span data-ttu-id="a066d-127">Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final</span><span class="sxs-lookup"><span data-stu-id="a066d-127">Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final</span></span> |
| <span data-ttu-id="a066d-128">EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="a066d-128">EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final</span></span> | <span data-ttu-id="a066d-129">Microsoft.EntityFrameworkCore.SqlServer.Design 1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="a066d-129">Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final</span></span>      |

## <a name="namespaces"></a><span data-ttu-id="a066d-130">名前空間</span><span class="sxs-lookup"><span data-stu-id="a066d-130">Namespaces</span></span>

<span data-ttu-id="a066d-131">パッケージ名と名前空間の変更から`Microsoft.Data.Entity.*`に`Microsoft.EntityFrameworkCore.*`です。</span><span class="sxs-lookup"><span data-stu-id="a066d-131">Along with package names, namespaces changed from `Microsoft.Data.Entity.*` to `Microsoft.EntityFrameworkCore.*`.</span></span> <span data-ttu-id="a066d-132">検索と置換では、この変更を処理できる`using Microsoft.Data.Entity`で`using Microsoft.EntityFrameworkCore`です。</span><span class="sxs-lookup"><span data-stu-id="a066d-132">You can handle this change with a find/replace of `using Microsoft.Data.Entity` with `using Microsoft.EntityFrameworkCore`.</span></span>

## <a name="table-naming-convention-changes"></a><span data-ttu-id="a066d-133">テーブルの名前付け規約の変更</span><span class="sxs-lookup"><span data-stu-id="a066d-133">Table Naming Convention Changes</span></span>

<span data-ttu-id="a066d-134">RC2 で作成しました、重要な機能の変更がの名前を使用するが、`DbSet<TEntity>`テーブル名として指定されたエンティティのプロパティにマップ、クラス名だけではなくです。</span><span class="sxs-lookup"><span data-stu-id="a066d-134">A significant functional change we took in RC2 was to use the name of the `DbSet<TEntity>` property for a given entity as the table name it maps to, rather than just the class name.</span></span> <span data-ttu-id="a066d-135">詳細を読み取ることができますにこの変更に関する[関連アナウンス問題](https://github.com/aspnet/Announcements/issues/167)です。</span><span class="sxs-lookup"><span data-stu-id="a066d-135">You can read more about this change in [the related announcement issue](https://github.com/aspnet/Announcements/issues/167).</span></span>

<span data-ttu-id="a066d-136">RC1 の既存のアプリケーションのことをお勧めの先頭に次のコードを追加、 `OnModelCreating` RC1 の命名方法を保持する方法。</span><span class="sxs-lookup"><span data-stu-id="a066d-136">For existing RC1 applications, we recommend adding the following code to the start of your `OnModelCreating` method to keep the RC1 naming strategy:</span></span>

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

<span data-ttu-id="a066d-137">新しい名前付けの戦略を採用する場合を推奨が正常に完了、アップグレードの手順と、コードを削除し、テーブルを適用する移行の作成の残りの部分の名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="a066d-137">If you want to adopt the new naming strategy, we would recommend successfully completing the rest of the upgrade steps and then removing the code and creating a migration to apply the table renames.</span></span>

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a><span data-ttu-id="a066d-138">AddDbContext/Startup.cs 変更 (ASP.NET Core プロジェクトのみ)</span><span class="sxs-lookup"><span data-stu-id="a066d-138">AddDbContext / Startup.cs Changes (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="a066d-139">RC1 では、アプリケーション サービス プロバイダーのに Entity Framework サービスを追加する必要がある`Startup.ConfigureServices(...)`:。</span><span class="sxs-lookup"><span data-stu-id="a066d-139">In RC1, you had to add Entity Framework services to the application service provider - in `Startup.ConfigureServices(...)`:</span></span>

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="a066d-140">RC2 では、への呼び出しを削除することができます`AddEntityFramework()`、 `AddSqlServer()`, などです。 します。</span><span class="sxs-lookup"><span data-stu-id="a066d-140">In RC2, you can remove the calls to `AddEntityFramework()`, `AddSqlServer()`, etc.:</span></span>

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="a066d-141">コンテキストのオプションを受け取り、基底コンス トラクターに渡して、派生のコンテキストに、コンス トラクターを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a066d-141">You also need to add a constructor, to your derived context, that takes context options and passes them to the base constructor.</span></span> <span data-ttu-id="a066d-142">以下は、バック グラウンドで忍び込んででそれらを恐ろしいマジックの一部を削除したために必要です。</span><span class="sxs-lookup"><span data-stu-id="a066d-142">This is needed because we removed some of the scary magic that snuck them in behind the scenes:</span></span>

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a><span data-ttu-id="a066d-143">IServiceProvider に渡すこと</span><span class="sxs-lookup"><span data-stu-id="a066d-143">Passing in an IServiceProvider</span></span>

<span data-ttu-id="a066d-144">合格する RC1 コードがある場合、`IServiceProvider`コンテキストにこれが今すぐに移動`DbContextOptions`、別のコンス トラクターのパラメーターの中ではなくです。</span><span class="sxs-lookup"><span data-stu-id="a066d-144">If you have RC1 code that passes an `IServiceProvider` to the context, this has now moved to `DbContextOptions`, rather than being a separate constructor parameter.</span></span> <span data-ttu-id="a066d-145">使用して`DbContextOptionsBuilder.UseInternalServiceProvider(...)`サービス プロバイダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="a066d-145">Use `DbContextOptionsBuilder.UseInternalServiceProvider(...)` to set the service provider.</span></span>

### <a name="testing"></a><span data-ttu-id="a066d-146">テスト中</span><span class="sxs-lookup"><span data-stu-id="a066d-146">Testing</span></span>

<span data-ttu-id="a066d-147">これを行うための最も一般的なシナリオをテストする場合は、InMemory データベースのスコープを制御することでした。</span><span class="sxs-lookup"><span data-stu-id="a066d-147">The most common scenario for doing this was to control the scope of an InMemory database when testing.</span></span> <span data-ttu-id="a066d-148">表示、更新された[テスト](testing/index.md)RC2 を使用してこれを行う例については資料です。</span><span class="sxs-lookup"><span data-stu-id="a066d-148">See the updated [Testing](testing/index.md) article for an example of doing this with RC2.</span></span>

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a><span data-ttu-id="a066d-149">アプリケーション サービス プロバイダー (ASP.NET Core プロジェクトのみ) からの内部のサービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="a066d-149">Resolving Internal Services from Application Service Provider (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="a066d-150">オーバー ロードがある場合、ASP.NET Core アプリケーションがある EF アプリケーション サービス プロバイダーからの内部のサービスを解決するのには、`AddDbContext`これを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="a066d-150">If you have an ASP.NET Core application and you want EF to resolve internal services from the application service provider, there is an overload of `AddDbContext` that allows you to configure this:</span></span>

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider)); );
```

> [!WARNING]  
> <span data-ttu-id="a066d-151">アプリケーション サービス プロバイダーに内部の EF サービスを結合する理由がない限り、内部的には、独自のサービスを管理する EF を許可することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a066d-151">We recommend allowing EF to internally manage its own services, unless you have a reason to combine the internal EF services into your application service provider.</span></span> <span data-ttu-id="a066d-152">これを行うことがある主な理由は、EF を内部的に使用するサービスを置き換えるには、アプリケーション サービス プロバイダーを使用するには</span><span class="sxs-lookup"><span data-stu-id="a066d-152">The main reason you may want to do this is to use your application service provider to replace services that EF uses internally</span></span>

## <a name="dnx-commands--net-cli-aspnet-core-projects-only"></a><span data-ttu-id="a066d-153">DNX コマンド = > .NET CLI (ASP.NET Core プロジェクトのみ)</span><span class="sxs-lookup"><span data-stu-id="a066d-153">DNX Commands => .NET CLI (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="a066d-154">使用していた場合、 `dnx ef` ASP.NET 5 プロジェクトのコマンドは、これらは今やに`dotnet ef`コマンド。</span><span class="sxs-lookup"><span data-stu-id="a066d-154">If you previously used the `dnx ef` commands for ASP.NET 5 projects, these have now moved to `dotnet ef` commands.</span></span> <span data-ttu-id="a066d-155">同じコマンドの構文は引き続き適用されます。</span><span class="sxs-lookup"><span data-stu-id="a066d-155">The same command syntax still applies.</span></span> <span data-ttu-id="a066d-156">使用することができます`dotnet ef --help`構文についてはします。</span><span class="sxs-lookup"><span data-stu-id="a066d-156">You can use `dotnet ef --help` for syntax information.</span></span>

<span data-ttu-id="a066d-157">RC2 では、.NET CLI によって置き換えられる DNX のためのコマンドが登録されている方法が変更されました。</span><span class="sxs-lookup"><span data-stu-id="a066d-157">The way commands are registered has changed in RC2, due to DNX being replaced by .NET CLI.</span></span> <span data-ttu-id="a066d-158">コマンドに登録されました、 `tools` 」の「 `project.json`:</span><span class="sxs-lookup"><span data-stu-id="a066d-158">Commands are now registered in a `tools` section in `project.json`:</span></span>

``` json
"tools": {
  "Microsoft.EntityFrameworkCore.Tools": {
    "version": "1.0.0-preview1-final",
    "imports": [
      "portable-net45+win8+dnxcore50",
      "portable-net45+win8"
    ]
  }
}
```

> [!TIP]  
> <span data-ttu-id="a066d-159">Visual Studio を使用する場合は、ASP.NET Core プロジェクト (これがサポートされていません rc1) の EF コマンドを実行するパッケージ マネージャー コンソールを使用することができますようになりました。</span><span class="sxs-lookup"><span data-stu-id="a066d-159">If you use Visual Studio, you can now use Package Manager Console to run EF commands for ASP.NET Core projects (this was not supported in RC1).</span></span> <span data-ttu-id="a066d-160">内のコマンドを登録する必要があります、`tools`のセクション`project.json`これを行う。</span><span class="sxs-lookup"><span data-stu-id="a066d-160">You still need to register the commands in the `tools` section of `project.json` to do this.</span></span>

## <a name="package-manager-commands-require-powershell-5"></a><span data-ttu-id="a066d-161">必要な PowerShell 5 のパッケージ マネージャーのコマンド</span><span class="sxs-lookup"><span data-stu-id="a066d-161">Package Manager Commands Require PowerShell 5</span></span>

<span data-ttu-id="a066d-162">Visual Studio でパッケージ マネージャー コンソールで Entity Framework のコマンドを使用する場合は、PowerShell 5 がインストールされていることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a066d-162">If you use the Entity Framework commands in Package Manager Console in Visual Studio, then you will need to ensure you have PowerShell 5 installed.</span></span> <span data-ttu-id="a066d-163">これは、次のリリースで削除される一時要件 (を参照してください[発行 #5327](https://github.com/aspnet/EntityFramework/issues/5327)詳細については)。</span><span class="sxs-lookup"><span data-stu-id="a066d-163">This is a temporary requirement that will be removed in the next release (see [issue #5327](https://github.com/aspnet/EntityFramework/issues/5327) for more details).</span></span>

## <a name="using-imports-in-projectjson"></a><span data-ttu-id="a066d-164">Project.json に「インポート」を使用します。</span><span class="sxs-lookup"><span data-stu-id="a066d-164">Using "imports" in project.json</span></span>

<span data-ttu-id="a066d-165">一部の EF コアの依存関係はサポートされません .NET 標準まだ。</span><span class="sxs-lookup"><span data-stu-id="a066d-165">Some of EF Core's dependencies do not support .NET Standard yet.</span></span> <span data-ttu-id="a066d-166">EF コア .NET 標準と .NET Core プロジェクトでは、一時的な回避策として project.json に「インポート」を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a066d-166">EF Core in .NET Standard and .NET Core projects may require adding "imports" to project.json as a temporary workaround.</span></span>

<span data-ttu-id="a066d-167">EF を追加するときに NuGet の復元によってこのエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="a066d-167">When adding EF, NuGet restore will display this error message:</span></span>

``` Console
Package Ix-Async 1.2.5 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Ix-Async 1.2.5 supports:
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8 (.NETPortable,Version=v0.0,Profile=Profile78)
Package Remotion.Linq 2.0.2 is not compatible with netcoreapp1.0 (.NETCoreApp,Version=v1.0). Package Remotion.Linq 2.0.2 supports:
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
```

<span data-ttu-id="a066d-168">回避策では、ポータブル プロファイル「portable net451 + win8」を手動でインポートします。</span><span class="sxs-lookup"><span data-stu-id="a066d-168">The workaround is to manually import the portable profile "portable-net451+win8".</span></span> <span data-ttu-id="a066d-169">この強制的 NuGet これに一致するこのバイナリを扱うには、されない場合でも .NET 標準で互換のフレームワークとして提供します。</span><span class="sxs-lookup"><span data-stu-id="a066d-169">This forces NuGet to treat this binaries that match this provide as a compatible framework with .NET Standard, even though they are not.</span></span> <span data-ttu-id="a066d-170">「Portable net451 + win8」ではありませんが 100% .NET 標準との互換性は、PCL から .NET 標準への移行に十分な互換性です。</span><span class="sxs-lookup"><span data-stu-id="a066d-170">Although "portable-net451+win8" is not 100% compatible with .NET Standard, it is compatible enough for the transition from PCL to .NET Standard.</span></span> <span data-ttu-id="a066d-171">EF の依存関係は最終的に .NET Standard にアップグレードする場合、インポートを削除できます。</span><span class="sxs-lookup"><span data-stu-id="a066d-171">Imports can be removed when EF's dependencies eventually upgrade to .NET Standard.</span></span>

<span data-ttu-id="a066d-172">複数のフレームワークは、配列の構文では、「インポート」に追加できます。</span><span class="sxs-lookup"><span data-stu-id="a066d-172">Multiple frameworks can be added to "imports" in array syntax.</span></span> <span data-ttu-id="a066d-173">他のインポートは、追加のライブラリをプロジェクトに追加する場合に必要な可能性があります。</span><span class="sxs-lookup"><span data-stu-id="a066d-173">Other imports may be necessary if you add additional libraries to your project.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

<span data-ttu-id="a066d-174">参照してください[発行 #5176](https://github.com/aspnet/EntityFramework/issues/5176)です。</span><span class="sxs-lookup"><span data-stu-id="a066d-174">See [Issue #5176](https://github.com/aspnet/EntityFramework/issues/5176).</span></span>
