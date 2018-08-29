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
# <a name="upgrading-from-ef-core-10-rc1-to-10-rc2"></a>EF Core 1.0 RC1 から 1.0 の RC2 にアップグレードします。

この記事では、RC2、RC1 パッケージにビルドされたアプリケーションの移動のガイダンスを提供します。

## <a name="package-names-and-versions"></a>パッケージの名前とバージョン

RC1 と RC2 では、間を変更しました"Entity Framework 7"から"Entity Framework Core"します。 詳細の変更の理由について[Scott Hanselman によるこの投稿](http://www.hanselman.com/blog/ASPNET5IsDeadIntroducingASPNETCore10AndNETCore10.aspx)します。 この変更により、パッケージ名の変更から`EntityFramework.*`に`Microsoft.EntityFrameworkCore.*`のバージョンから`7.0.0-rc1-final`に`1.0.0-rc2-final`(または`1.0.0-preview1-final`ツールの)。

**RC1 パッケージを完全に削除してから、RC2 をインストールする必要があるものです。** 一般的なパッケージのマッピングを次に示します。

| RC1 パッケージ                                               | RC2 と同等                                                       |
|:----------------------------------------------------------|:---------------------------------------------------------------------|
| EntityFramework.MicrosoftSqlServer 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.SqlServer 1.0.0-rc2-final      |
| EntityFramework.SQLite 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.Sqlite 1.0.0-rc2-final      |
| EntityFramework7.Npgsql                   3.1.0-rc1-3     | NpgSql.EntityFrameworkCore.Postgres             <to be advised>      |
| EntityFramework.SqlServerCompact35 7.0.0-rc1-final | EntityFrameworkCore.SqlServerCompact35 1.0.0-rc2-final      |
| EntityFramework.SqlServerCompact40 7.0.0-rc1-final | EntityFrameworkCore.SqlServerCompact40 1.0.0-rc2-final      |
| EntityFramework.InMemory                  7.0.0-rc1-final | Microsoft.EntityFrameworkCore.InMemory 1.0.0-rc2-final      |
| EntityFramework.IBMDataServer 7.0.0-beta1     | Rc2 はまだ使用できません。                                            |
| EntityFramework.Commands 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.Tools 1.0.0-preview1-final |
| EntityFramework.MicrosoftSqlServer.Design 7.0.0-rc1-final | Microsoft.EntityFrameworkCore.SqlServer.Design 1.0.0-rc2-final      |

## <a name="namespaces"></a>名前空間

パッケージの名前と名前空間の変更から`Microsoft.Data.Entity.*`に`Microsoft.EntityFrameworkCore.*`します。 検索/置換では、この変更を処理できる`using Microsoft.Data.Entity`で`using Microsoft.EntityFrameworkCore`します。

## <a name="table-naming-convention-changes"></a>テーブルの命名規約の変更

RC2 でしました、重要な機能の変更の名前を使用する方法が、`DbSet<TEntity>`テーブル名として指定されたエンティティのプロパティにマップ、クラス名だけではなく。 詳細では、この変更に関する[関連のお知らせ問題](https://github.com/aspnet/Announcements/issues/167)します。

既存の RC1 アプリケーションをお勧めの先頭に次のコードを追加、 `OnModelCreating` RC1 の命名方法を保持するメソッド。

``` csharp
foreach (var entity in modelBuilder.Model.GetEntityTypes())
{
    entity.Relational().TableName = entity.DisplayName();
}
```

新しい名前付け戦略を採用する場合はお勧めしますが正常に完了、残りのアップグレードの手順とし、コードを削除し、テーブルを適用する移行を作成する名前を変更します。

## <a name="adddbcontext--startupcs-changes-aspnet-core-projects-only"></a>AddDbContext/Startup.cs の変更 (ASP.NET Core プロジェクトのみ)

RC1 でアプリケーション サービス プロバイダーで Entity Framework サービスを追加する必要がある`Startup.ConfigureServices(...)`:。

``` csharp
services.AddEntityFramework()
  .AddSqlServer()
  .AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

RC2 への呼び出しを削除できます`AddEntityFramework()`、`AddSqlServer()`など。

``` csharp
services.AddDbContext<ApplicationDbContext>(options =>
  options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"]));
```

また、コンテキストのオプションを受け取り、基底コンス トラクターに渡して、派生コンテキストに、コンス トラクターを追加する必要があります。 これを削除しました背後に忍び込んでいる恐ろしい魔法のために必要です。

``` csharp
public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
    : base(options)
{
}
```

## <a name="passing-in-an-iserviceprovider"></a>IServiceProvider 内で渡す

渡される RC1 コードがある場合、`IServiceProvider`コンテキストにこれが移動したを`DbContextOptions`、別のコンス トラクターのパラメーターではなく。 使用`DbContextOptionsBuilder.UseInternalServiceProvider(...)`サービス プロバイダーを設定します。

### <a name="testing"></a>テスト

これを行うための最も一般的なシナリオをテストするときに、InMemory データベースのスコープを制御することでした。 更新された参照[テスト](testing/index.md)RC2 を使用してこれを実現する例については、資料。

### <a name="resolving-internal-services-from-application-service-provider-aspnet-core-projects-only"></a>アプリケーション サービス プロバイダー (ASP.NET Core プロジェクトのみ) からの内部サービスを解決します。

オーバー ロードがある場合は、ASP.NET Core アプリケーションがあり、アプリケーションのサービス プロバイダーからの内部サービスを解決するのには EF をする`AddDbContext`これを構成することができます。

``` csharp
services.AddEntityFrameworkSqlServer()
  .AddDbContext<ApplicationDbContext>((serviceProvider, options) =>
    options.UseSqlServer(Configuration["ConnectionStrings:DefaultConnection"])
           .UseInternalServiceProvider(serviceProvider)); );
```

> [!WARNING]  
> アプリケーション サービス プロバイダーに EF の内部サービスを結合する理由がない限り、内部的には、独自のサービスを管理する EF を許可することをお勧めします。 主な理由はこうしたい場合がありますがアプリケーション サービス プロバイダーを使用して、EF が内部的に使用するサービスを置換するには

## <a name="dnx-commands--net-cli-aspnet-core-projects-only"></a>DNX コマンド = > .NET CLI (ASP.NET Core プロジェクトのみ)

使用していた場合、 `dnx ef` ASP.NET 5 プロジェクトのコマンドは、これらに移動`dotnet ef`コマンド。 同じコマンドの構文は引き続き適用されます。 使用することができます`dotnet ef --help`構文の詳細について。

RC2 では、.NET CLI によって置き換えられる DNX が原因でコマンドが登録されている方法が変更されました。 コマンドで登録が完了しました、`tools`セクション`project.json`:

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
> Visual Studio を使用する場合は、(これがサポートされていません RC1 で) ASP.NET Core プロジェクトの EF コマンドを実行するパッケージ マネージャー コンソールを使用することができますようになりました。 内のコマンドを登録する必要があります、`tools`の`project.json`これを行う。

## <a name="package-manager-commands-require-powershell-5"></a>パッケージ マネージャーのコマンドが PowerShell 5 が必要です。

Visual Studio でパッケージ マネージャー コンソールで、Entity Framework のコマンドを使用する場合は、PowerShell 5 がインストールされている必要があることを確認する必要があります。 これは、次のリリースで削除される一時的な要件 (を参照してください[発行 #5327](https://github.com/aspnet/EntityFramework/issues/5327)の詳細)。

## <a name="using-imports-in-projectjson"></a>Project.json での"imports"の使用

EF Core の依存関係のいくつかまだサポートされていません .NET Standard です。 .NET Standard および .NET Core プロジェクトでの EF Core は、一時的な回避策として、project.json には、"imports"を追加する必要があります。

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

回避策では、「ポータブル net451 + win8」ポータブル プロファイルを手動でインポートします。 この強制的に NuGet がこれに一致するこのバイナリを処理するは、ない場合でも、.NET Standard を使用した互換性のあるフレームワークとして指定します。 「ポータブル net451 + win8」は、.NET Standard と互換性のある 100% ではない、.NET standard PCL からの移行に十分な互換性です。 EF の依存関係は、最終的に .NET Standard にアップグレードする場合、インポートを削除できます。

複数のフレームワークは、配列の構文で、"imports"に追加できます。 他のインポートをプロジェクトに追加のライブラリを追加する場合は、必要があります。

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

参照してください[発行 #5176](https://github.com/aspnet/EntityFramework/issues/5176)します。
