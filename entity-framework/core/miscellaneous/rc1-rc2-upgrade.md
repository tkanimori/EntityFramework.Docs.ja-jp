---
title: EF Core 1.0 RC1 から RC2 - EF Core へのアップグレード
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: 83b98fda5ac9491994b5b3fb333c9951ec01188a
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996898"
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a><span data-ttu-id="972f5-102">EF Core 1.0 RC1 から 1.0 の RC2 にアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="972f5-102">Upgrading from EF Core 1.0 RC1 to 1.0 RC2</span></span>

<span data-ttu-id="972f5-103">この記事では、RC2、RC1 パッケージにビルドされたアプリケーションの移動のガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="972f5-103">This article provides guidance for moving an application built with the RC1 packages to RC2.</span></span>

## <a name="package-names-and-versions"></a><span data-ttu-id="972f5-104">パッケージの名前とバージョン</span><span class="sxs-lookup"><span data-stu-id="972f5-104">Package Names and Versions</span></span>

<span data-ttu-id="972f5-105">RC1 と RC2 では、間を変更しました"Entity Framework 7"から"Entity Framework Core"します。</span><span class="sxs-lookup"><span data-stu-id="972f5-105">Between RC1 and RC2, we changed from "Entity Framework 7" to "Entity Framework Core".</span></span> <span data-ttu-id="972f5-106">詳細の変更の理由について[Scott Hanselman によるこの投稿](http://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="972f5-106">You can read more about the reasons for the change in [this post by Scott Hanselman](http://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx).</span></span> <span data-ttu-id="972f5-107">この変更により、パッケージ名の変更から`EntityFramework.*`に`Microsoft.EntityFrameworkCore.*`のバージョンから`7.0.0-rc1-final`に`1.0.0-rc2-final`(または`1.0.0-preview1-final`ツールの)。</span><span class="sxs-lookup"><span data-stu-id="972f5-107">Because of this change, our package names changed from `EntityFramework.*` to `Microsoft.EntityFrameworkCore.*` and our versions from `7.0.0-rc1-final` to `1.0.0-rc2-final` (or `1.0.0-preview1-final` for tooling).</span></span>

<span data-ttu-id="972f5-108">**RC1 パッケージを完全に削除してから、RC2 をインストールする必要があるものです。**</span><span class="sxs-lookup"><span data-stu-id="972f5-108">**You will need to completely remove the RC1 packages and then install the RC2 ones.**</span></span> <span data-ttu-id="972f5-109">一般的なパッケージのマッピングを次に示します。</span><span class="sxs-lookup"><span data-stu-id="972f5-109">Here is the mapping for some common packages.</span></span>

| <span data-ttu-id="972f5-110">RC1 パッケージ</span><span class="sxs-lookup"><span data-stu-id="972f5-110">RC1 Package</span></span>                                               | <span data-ttu-id="972f5-111">RC2 と同等</span><span class="sxs-lookup"><span data-stu-id="972f5-111">RC2 Equivalent</span></span>                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| <span data-ttu-id="972f5-112">EntityFramework.MicrosoftSqlServer 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="972f5-112">EntityFramework.MicrosoftSqlServer        7.0.0-rc1-final</span></span> | <span data-ttu-id="972f5-113">Microsoft.EntityFrameworkCore.SqlServer 1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="972f5-113">Microsoft.EntityFrameworkCore.SqlServer         1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="972f5-114">EntityFramework.SQLite 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="972f5-114">EntityFramework.SQLite                    7.0.0-rc1-final</span></span> | <span data-ttu-id="972f5-115">Microsoft.EntityFrameworkCore.Sqlite 1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="972f5-115">Microsoft.EntityFrameworkCore.Sqlite            1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="972f5-116">EntityFramework7.Npgsql                   3.1.0-rc1-3</span><span class="sxs-lookup"><span data-stu-id="972f5-116">EntityFramework7.Npgsql                   3.1.0-rc1-3</span></span>     | <span data-ttu-id="972f5-117">NpgSql.EntityFrameworkCore.Postgres             <to be advised></span><span class="sxs-lookup"><span data-stu-id="972f5-117">NpgSql.EntityFrameworkCore.Postgres             <to be advised></span></span>      |
| <span data-ttu-id="972f5-118">EntityFramework.SqlServerCompact35 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="972f5-118">EntityFramework.SqlServerCompact35        7.0.0-rc1-final</span></span> | <span data-ttu-id="972f5-119">EntityFrameworkCore.SqlServerCompact35 1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="972f5-119">EntityFrameworkCore.SqlServerCompact35          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="972f5-120">EntityFramework.SqlServerCompact40 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="972f5-120">EntityFramework.SqlServerCompact40        7.0.0-rc1-final</span></span> | <span data-ttu-id="972f5-121">EntityFrameworkCore.SqlServerCompact40 1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="972f5-121">EntityFrameworkCore.SqlServerCompact40          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="972f5-122">EntityFramework.InMemory                  7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="972f5-122">EntityFramework.InMemory                  7.0.0-rc1-final</span></span> | <span data-ttu-id="972f5-123">Microsoft.EntityFrameworkCore.InMemory 1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="972f5-123">Microsoft.EntityFrameworkCore.InMemory          1.0.0-rc2-final</span></span>      |
| <span data-ttu-id="972f5-124">EntityFramework.IBMDataServer 7.0.0-beta1</span><span class="sxs-lookup"><span data-stu-id="972f5-124">EntityFramework.IBMDataServer             7.0.0-beta1</span></span>     | <span data-ttu-id="972f5-125">Rc2 はまだ使用できません。</span><span class="sxs-lookup"><span data-stu-id="972f5-125">Not yet available for RC2</span></span>                                            |
| <span data-ttu-id="972f5-126">EntityFramework.Commands 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="972f5-126">EntityFramework.Commands                  7.0.0-rc1-final</span></span> | <span data-ttu-id="972f5-127">Microsoft.EntityFrameworkCore.Tools 1.0.0-preview1-final</span><span class="sxs-lookup"><span data-stu-id="972f5-127">Microsoft.EntityFrameworkCore.Tools             1.0.0-preview1-final</span></span> |
| <span data-ttu-id="972f5-128">EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final</span><span class="sxs-lookup"><span data-stu-id="972f5-128">EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final</span></span> | <span data-ttu-id="972f5-129">Microsoft.EntityFrameworkCore.SqlServer.Design 1.0.0-rc2-final</span><span class="sxs-lookup"><span data-stu-id="972f5-129">Microsoft.EntityFrameworkCore.SqlServer.Design  1.0.0-rc2-final</span></span>      |

## <a name="namespaces"></a><span data-ttu-id="972f5-130">名前空間</span><span class="sxs-lookup"><span data-stu-id="972f5-130">Namespaces</span></span>

<span data-ttu-id="972f5-131">パッケージの名前と名前空間の変更から`Microsoft.Data.Entity.*`に`Microsoft.EntityFrameworkCore.*`します。</span><span class="sxs-lookup"><span data-stu-id="972f5-131">Along with package names, namespaces changed from `Microsoft.Data.Entity.*` to `Microsoft.EntityFrameworkCore.*`.</span></span> <span data-ttu-id="972f5-132">検索/置換では、この変更を処理できる`using Microsoft.Data.Entity`で`using Microsoft.EntityFrameworkCore`します。</span><span class="sxs-lookup"><span data-stu-id="972f5-132">You can handle this change with a find/replace of `using Microsoft.Data.Entity` with `using Microsoft.EntityFrameworkCore`.</span></span>

## <a name="table-naming-convention-changes"></a><span data-ttu-id="972f5-133">テーブルの命名規約の変更</span><span class="sxs-lookup"><span data-stu-id="972f5-133">Table Naming Convention Changes</span></span>

<span data-ttu-id="972f5-134">RC2 でしました、重要な機能の変更の名前を使用する方法が、`DbSet<TEntity>`テーブル名として指定されたエンティティのプロパティにマップ、クラス名だけではなく。</span><span class="sxs-lookup"><span data-stu-id="972f5-134">A significant functional change we took in RC2 was to use the name of the `DbSet<TEntity>` property for a given entity as the table name it maps to, rather than just the class name.</span></span> <span data-ttu-id="972f5-135">詳細では、この変更に関する[関連のお知らせ問題](https://github.com/aspnet/Announcements/issues/167)します。</span><span class="sxs-lookup"><span data-stu-id="972f5-135">You can read more about this change in [the related announcement issue](https://github.com/aspnet/Announcements/issues/167).</span></span>

<span data-ttu-id="972f5-136">既存の RC1 アプリケーションをお勧めの先頭に次のコードを追加、 `OnModelCreating` RC1 の命名方法を保持するメソッド。</span><span class="sxs-lookup"><span data-stu-id="972f5-136">For existing RC1 applications, we recommend adding the following code to the start of your `OnModelCreating` method to keep the RC1 naming strategy:</span></span>

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

<span data-ttu-id="972f5-137">新しい名前付け戦略を採用する場合はお勧めしますが正常に完了、残りのアップグレードの手順とし、コードを削除し、テーブルを適用する移行を作成する名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="972f5-137">If you want to adopt the new naming strategy, we would recommend successfully completing the rest of the upgrade steps and then removing the code and creating a migration to apply the table renames.</span></span>

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a><span data-ttu-id="972f5-138">AddDbContext/Startup.cs の変更 (ASP.NET Core プロジェクトのみ)</span><span class="sxs-lookup"><span data-stu-id="972f5-138">AddDbContext / Startup.cs Changes (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="972f5-139">RC1 でアプリケーション サービス プロバイダーで Entity Framework サービスを追加する必要がある`Startup.ConfigureServices(...)`:。</span><span class="sxs-lookup"><span data-stu-id="972f5-139">In RC1, you had to add Entity Framework services to the application service provider - in `Startup.ConfigureServices(...)`:</span></span>

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="972f5-140">RC2 への呼び出しを削除できます`AddEntityFramework()`、`AddSqlServer()`など。</span><span class="sxs-lookup"><span data-stu-id="972f5-140">In RC2, you can remove the calls to `AddEntityFramework()`, `AddSqlServer()`, etc.:</span></span>

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

<span data-ttu-id="972f5-141">また、コンテキストのオプションを受け取り、基底コンス トラクターに渡して、派生コンテキストに、コンス トラクターを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="972f5-141">You also need to add a constructor, to your derived context, that takes context options and passes them to the base constructor.</span></span> <span data-ttu-id="972f5-142">これを削除しました背後に忍び込んでいる恐ろしい魔法のために必要です。</span><span class="sxs-lookup"><span data-stu-id="972f5-142">This is needed because we removed some of the scary magic that snuck them in behind the scenes:</span></span>

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a><span data-ttu-id="972f5-143">IServiceProvider 内で渡す</span><span class="sxs-lookup"><span data-stu-id="972f5-143">Passing in an IServiceProvider</span></span>

<span data-ttu-id="972f5-144">渡される RC1 コードがある場合、`IServiceProvider`コンテキストにこれが移動したを`DbContextOptions`、別のコンス トラクターのパラメーターではなく。</span><span class="sxs-lookup"><span data-stu-id="972f5-144">If you have RC1 code that passes an `IServiceProvider` to the context, this has now moved to `DbContextOptions`, rather than being a separate constructor parameter.</span></span> <span data-ttu-id="972f5-145">使用`DbContextOptionsBuilder.UseInternalServiceProvider(...)`サービス プロバイダーを設定します。</span><span class="sxs-lookup"><span data-stu-id="972f5-145">Use `DbContextOptionsBuilder.UseInternalServiceProvider(...)` to set the service provider.</span></span>

### <a name="testing"></a><span data-ttu-id="972f5-146">テスト</span><span class="sxs-lookup"><span data-stu-id="972f5-146">Testing</span></span>

<span data-ttu-id="972f5-147">これを行うための最も一般的なシナリオをテストするときに、InMemory データベースのスコープを制御することでした。</span><span class="sxs-lookup"><span data-stu-id="972f5-147">The most common scenario for doing this was to control the scope of an InMemory database when testing.</span></span> <span data-ttu-id="972f5-148">更新された参照[テスト](testing/index.md)RC2 を使用してこれを実現する例については、資料。</span><span class="sxs-lookup"><span data-stu-id="972f5-148">See the updated [Testing](testing/index.md) article for an example of doing this with RC2.</span></span>

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a><span data-ttu-id="972f5-149">アプリケーション サービス プロバイダー (ASP.NET Core プロジェクトのみ) からの内部サービスを解決します。</span><span class="sxs-lookup"><span data-stu-id="972f5-149">Resolving Internal Services from Application Service Provider (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="972f5-150">オーバー ロードがある場合は、ASP.NET Core アプリケーションがあり、アプリケーションのサービス プロバイダーからの内部サービスを解決するのには EF をする`AddDbContext`これを構成することができます。</span><span class="sxs-lookup"><span data-stu-id="972f5-150">If you have an ASP.NET Core application and you want EF to resolve internal services from the application service provider, there is an overload of `AddDbContext` that allows you to configure this:</span></span>

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider)); );
```

> [!WARNING]  
> <span data-ttu-id="972f5-151">アプリケーション サービス プロバイダーに EF の内部サービスを結合する理由がない限り、内部的には、独自のサービスを管理する EF を許可することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="972f5-151">We recommend allowing EF to internally manage its own services, unless you have a reason to combine the internal EF services into your application service provider.</span></span> <span data-ttu-id="972f5-152">主な理由はこうしたい場合がありますがアプリケーション サービス プロバイダーを使用して、EF が内部的に使用するサービスを置換するには</span><span class="sxs-lookup"><span data-stu-id="972f5-152">The main reason you may want to do this is to use your application service provider to replace services that EF uses internally</span></span>

## <a name="dnx-commands--net-cli-aspnet-core-projects-only"></a><span data-ttu-id="972f5-153">DNX コマンド = > .NET CLI (ASP.NET Core プロジェクトのみ)</span><span class="sxs-lookup"><span data-stu-id="972f5-153">DNX Commands => .NET CLI (ASP.NET Core Projects Only)</span></span>

<span data-ttu-id="972f5-154">使用していた場合、 `dnx ef` ASP.NET 5 プロジェクトのコマンドは、これらに移動`dotnet ef`コマンド。</span><span class="sxs-lookup"><span data-stu-id="972f5-154">If you previously used the `dnx ef` commands for ASP.NET 5 projects, these have now moved to `dotnet ef` commands.</span></span> <span data-ttu-id="972f5-155">同じコマンドの構文は引き続き適用されます。</span><span class="sxs-lookup"><span data-stu-id="972f5-155">The same command syntax still applies.</span></span> <span data-ttu-id="972f5-156">使用することができます`dotnet ef --help`構文の詳細について。</span><span class="sxs-lookup"><span data-stu-id="972f5-156">You can use `dotnet ef --help` for syntax information.</span></span>

<span data-ttu-id="972f5-157">RC2 では、.NET CLI によって置き換えられる DNX が原因でコマンドが登録されている方法が変更されました。</span><span class="sxs-lookup"><span data-stu-id="972f5-157">The way commands are registered has changed in RC2, due to DNX being replaced by .NET CLI.</span></span> <span data-ttu-id="972f5-158">コマンドで登録が完了しました、`tools`セクション`project.json`:</span><span class="sxs-lookup"><span data-stu-id="972f5-158">Commands are now registered in a `tools` section in `project.json`:</span></span>

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
> <span data-ttu-id="972f5-159">Visual Studio を使用する場合は、(これがサポートされていません RC1 で) ASP.NET Core プロジェクトの EF コマンドを実行するパッケージ マネージャー コンソールを使用することができますようになりました。</span><span class="sxs-lookup"><span data-stu-id="972f5-159">If you use Visual Studio, you can now use Package Manager Console to run EF commands for ASP.NET Core projects (this was not supported in RC1).</span></span> <span data-ttu-id="972f5-160">内のコマンドを登録する必要があります、`tools`の`project.json`これを行う。</span><span class="sxs-lookup"><span data-stu-id="972f5-160">You still need to register the commands in the `tools` section of `project.json` to do this.</span></span>

## <a name="package-manager-commands-require-powershell-5"></a><span data-ttu-id="972f5-161">パッケージ マネージャーのコマンドが PowerShell 5 が必要です。</span><span class="sxs-lookup"><span data-stu-id="972f5-161">Package Manager Commands Require PowerShell 5</span></span>

<span data-ttu-id="972f5-162">Visual Studio でパッケージ マネージャー コンソールで、Entity Framework のコマンドを使用する場合は、PowerShell 5 がインストールされている必要があることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="972f5-162">If you use the Entity Framework commands in Package Manager Console in Visual Studio, then you will need to ensure you have PowerShell 5 installed.</span></span> <span data-ttu-id="972f5-163">これは、次のリリースで削除される一時的な要件 (を参照してください[発行 #5327](https://github.com/aspnet/EntityFramework/issues/5327)の詳細)。</span><span class="sxs-lookup"><span data-stu-id="972f5-163">This is a temporary requirement that will be removed in the next release (see [issue #5327](https://github.com/aspnet/EntityFramework/issues/5327) for more details).</span></span>

## <a name="using-imports-in-projectjson"></a><span data-ttu-id="972f5-164">Project.json での"imports"の使用</span><span class="sxs-lookup"><span data-stu-id="972f5-164">Using "imports" in project.json</span></span>

<span data-ttu-id="972f5-165">EF Core の依存関係のいくつかまだサポートされていません .NET Standard です。</span><span class="sxs-lookup"><span data-stu-id="972f5-165">Some of EF Core's dependencies do not support .NET Standard yet.</span></span> <span data-ttu-id="972f5-166">.NET Standard および .NET Core プロジェクトでの EF Core は、一時的な回避策として、project.json には、"imports"を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="972f5-166">EF Core in .NET Standard and .NET Core projects may require adding "imports" to project.json as a temporary workaround.</span></span>

<span data-ttu-id="972f5-167">EF を追加するときに NuGet の復元によってこのエラー メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="972f5-167">When adding EF, NuGet restore will display this error message:</span></span>

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

<span data-ttu-id="972f5-168">回避策では、「ポータブル net451 + win8」ポータブル プロファイルを手動でインポートします。</span><span class="sxs-lookup"><span data-stu-id="972f5-168">The workaround is to manually import the portable profile "portable-net451+win8".</span></span> <span data-ttu-id="972f5-169">この強制的に NuGet がこれに一致するこのバイナリを処理するは、ない場合でも、.NET Standard を使用した互換性のあるフレームワークとして指定します。</span><span class="sxs-lookup"><span data-stu-id="972f5-169">This forces NuGet to treat this binaries that match this provide as a compatible framework with .NET Standard, even though they are not.</span></span> <span data-ttu-id="972f5-170">「ポータブル net451 + win8」は、.NET Standard と互換性のある 100% ではない、.NET standard PCL からの移行に十分な互換性です。</span><span class="sxs-lookup"><span data-stu-id="972f5-170">Although "portable-net451+win8" is not 100% compatible with .NET Standard, it is compatible enough for the transition from PCL to .NET Standard.</span></span> <span data-ttu-id="972f5-171">EF の依存関係は、最終的に .NET Standard にアップグレードする場合、インポートを削除できます。</span><span class="sxs-lookup"><span data-stu-id="972f5-171">Imports can be removed when EF's dependencies eventually upgrade to .NET Standard.</span></span>

<span data-ttu-id="972f5-172">複数のフレームワークは、配列の構文で、"imports"に追加できます。</span><span class="sxs-lookup"><span data-stu-id="972f5-172">Multiple frameworks can be added to "imports" in array syntax.</span></span> <span data-ttu-id="972f5-173">他のインポートをプロジェクトに追加のライブラリを追加する場合は、必要があります。</span><span class="sxs-lookup"><span data-stu-id="972f5-173">Other imports may be necessary if you add additional libraries to your project.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

<span data-ttu-id="972f5-174">参照してください[発行 #5176](https://github.com/aspnet/EntityFramework/issues/5176)します。</span><span class="sxs-lookup"><span data-stu-id="972f5-174">See [Issue #5176](https://github.com/aspnet/EntityFramework/issues/5176).</span></span>
