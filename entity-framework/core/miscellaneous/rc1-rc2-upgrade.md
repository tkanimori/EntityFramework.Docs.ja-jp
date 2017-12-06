---
title: "EF Core 1.0 RC1 から RC2 - EF コアへのアップグレード"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 6d75b229-cc79-4d08-88cd-3a1c1b24d88f
ms.technology: entity-framework-core
uid: core/miscellaneous/rc1-rc2-upgrade
ms.openlocfilehash: ae5077c30642e3f40f51adee429821978f194460
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a>EF Core 1.0 RC1 から RC2 1.0 へのアップグレード

この記事では、RC2、RC1 パッケージにビルドされたアプリケーションの移動のガイダンスを提供します。

## <a name="package-names-and-versions"></a>パッケージの名前とバージョン

RC1 と RC2 では、間で変更しました"Entity Framework 7"から"Entity Framework Core"を。 詳細の変更の理由に関する[Scott Hanselman によるこの投稿](http://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)です。 この変更のため、パッケージ名が変更された`EntityFramework.*`に`Microsoft.EntityFrameworkCore.*`と、バージョン`7.0.0-rc1-final`に`1.0.0-rc2-final`(または`1.0.0-preview1-final`ツールの)。

**RC1 パッケージを完全に削除してから、RC2 をインストールする必要があるものです。** 一般的なパッケージの一部のマッピングを次に示します。

| RC1 パッケージ                                               | 該当するショートカットは RC2                                                       |
| --------------------------------------------------------- | -------------------------------------------------------------------- |
| EntityFramework.MicrosoftSqlServer 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.SqlServer 1.0.0-rc2-final      |
| EntityFramework.SQLite 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.Sqlite 1.0.0-rc2-final      |
| EntityFramework7.Npgsql 3.1.0-rc1-3     | NpgSql.EntityFrameworkCore.Postgres<to be advised>      |
| EntityFramework.SqlServerCompact35 7.0.0-rc1-final | EntityFrameworkCore.SqlServerCompact35 1.0.0-rc2-final      |
| EntityFramework.SqlServerCompact40 7.0.0-rc1-final | EntityFrameworkCore.SqlServerCompact40 1.0.0-rc2-final      |
| EntityFramework.InMemory 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.InMemory 1.0.0-rc2-final      |
| EntityFramework.IBMDataServer 7.0.0-beta1     | RC2 ではまだ使用できません。                                            |
| EntityFramework.Commands 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.Tools 1.0.0-preview1-final |
| EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.SqlServer.Design 1.0.0-rc2-final      |

## <a name="namespaces"></a>名前空間

パッケージ名と名前空間の変更から`Microsoft.Data.Entity.*`に`Microsoft.EntityFrameworkCore.*`です。 検索と置換では、この変更を処理できる`using Microsoft.Data.Entity`で`using Microsoft.EntityFrameworkCore`です。

## <a name="table-naming-convention-changes"></a>テーブルの名前付け規約の変更

RC2 で作成しました、重要な機能の変更がの名前を使用するが、`DbSet<TEntity>`テーブル名として指定されたエンティティのプロパティにマップ、クラス名だけではなくです。 詳細を読み取ることができますにこの変更に関する[関連アナウンス問題](https://github.com/aspnet/Announcements/issues/167)です。

RC1 の既存のアプリケーションのことをお勧めの先頭に次のコードを追加、 `OnModelCreating` RC1 の命名方法を保持する方法。

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

新しい名前付けの戦略を採用する場合を推奨が正常に完了、アップグレードの手順と、コードを削除し、テーブルを適用する移行の作成の残りの部分の名前を変更します。

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a>AddDbContext/Startup.cs 変更 (ASP.NET Core プロジェクトのみ)

RC1 では、アプリケーション サービス プロバイダーのに Entity Framework サービスを追加する必要がある`Startup.ConfigureServices(...)`:。

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

RC2 では、への呼び出しを削除することができます`AddEntityFramework()`、 `AddSqlServer()`, などです。 します。

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

コンテキストのオプションを受け取り、基底コンス トラクターに渡して、派生のコンテキストに、コンス トラクターを追加する必要があります。 以下は、バック グラウンドで忍び込んででそれらを恐ろしいマジックの一部を削除したために必要です。

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a>IServiceProvider に渡すこと

合格する RC1 コードがある場合、`IServiceProvider`コンテキストにこれが今すぐに移動`DbContextOptions`、別のコンス トラクターのパラメーターの中ではなくです。 使用して`DbContextOptionsBuilder.UseInternalServiceProvider(...)`サービス プロバイダーを設定します。

### <a name="testing"></a>テスト中

これを行うための最も一般的なシナリオをテストする場合は、InMemory データベースのスコープを制御することでした。 表示、更新された[テスト](testing/index.md)RC2 を使用してこれを行う例については資料です。

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a>アプリケーション サービス プロバイダー (ASP.NET Core プロジェクトのみ) からの内部のサービスを解決します。

オーバー ロードがある場合、ASP.NET Core アプリケーションがある EF アプリケーション サービス プロバイダーからの内部のサービスを解決するのには、`AddDbContext`これを構成することができます。

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider)); );
```

> [!WARNING]  
> アプリケーション サービス プロバイダーに内部の EF サービスを結合する理由がない限り、内部的には、独自のサービスを管理する EF を許可することをお勧めします。 これを行うことがある主な理由は、EF を内部的に使用するサービスを置き換えるには、アプリケーション サービス プロバイダーを使用するには

## <a name="dnx-commands--net-cli-aspnet-core-projects-only"></a>DNX コマンド = > .NET CLI (ASP.NET Core プロジェクトのみ)

使用していた場合、 `dnx ef` ASP.NET 5 プロジェクトのコマンドは、これらは今やに`dotnet ef`コマンド。 同じコマンドの構文は引き続き適用されます。 使用することができます`dotnet ef --help`構文についてはします。

RC2 では、.NET CLI によって置き換えられる DNX のためのコマンドが登録されている方法が変更されました。 コマンドに登録されました、 `tools` 」の「 `project.json`:

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
> Visual Studio を使用する場合は、ASP.NET Core プロジェクト (これがサポートされていません rc1) の EF コマンドを実行するパッケージ マネージャー コンソールを使用することができますようになりました。 内のコマンドを登録する必要があります、`tools`のセクション`project.json`これを行う。

## <a name="package-manager-commands-require-powershell-5"></a>必要な PowerShell 5 のパッケージ マネージャーのコマンド

Visual Studio でパッケージ マネージャー コンソールで Entity Framework のコマンドを使用する場合は、PowerShell 5 がインストールされていることを確認する必要があります。 これは、次のリリースで削除される一時要件 (を参照してください[発行 #5327](https://github.com/aspnet/EntityFramework/issues/5327)詳細については)。

## <a name="using-imports-in-projectjson"></a>Project.json に「インポート」を使用します。

一部の EF コアの依存関係はサポートされません .NET 標準まだ。 EF コア .NET 標準と .NET Core プロジェクトでは、一時的な回避策として project.json に「インポート」を追加する必要があります。

EF を追加するときに NuGet の復元によってこのエラー メッセージが表示されます。

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

回避策では、ポータブル プロファイル「portable net451 + win8」を手動でインポートします。 この強制的 NuGet これに一致するこのバイナリを扱うには、されない場合でも .NET 標準で互換のフレームワークとして提供します。 「Portable net451 + win8」ではありませんが 100% .NET 標準との互換性は、PCL から .NET 標準への移行に十分な互換性です。 EF の依存関係は最終的に .NET Standard にアップグレードする場合、インポートを削除できます。

複数のフレームワークは、配列の構文では、「インポート」に追加できます。 他のインポートは、追加のライブラリをプロジェクトに追加する場合に必要な可能性があります。

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

参照してください[発行 #5176](https://github.com/aspnet/EntityFramework/issues/5176)です。
