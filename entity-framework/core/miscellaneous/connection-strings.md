---
title: 接続文字列の EF コア
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
ms.technology: entity-framework-core
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: b4ed01f0452d74ac49d3fde780caa5f1b25a6e97
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052532"
---
# <a name="connection-strings"></a>接続文字列

ほとんどのデータベース プロバイダーでは、何らかの形式のデータベースに接続する接続文字列が必要です。 場合によってこの接続文字列には、保護する必要がある機密情報が含まれています。 また、アプリケーションを開発、テスト、および運用環境などの環境間で移動すると、接続文字列を変更する必要があります。

## <a name="net-framework-applications"></a>.NET framework アプリケーション

WinForms、WPF、コンソール、および ASP.NET 4 などの .NET framework アプリケーションでは、しようとして、テスト対象の接続文字列のパターンがあります。 接続文字列は、アプリケーションの App.config ファイル (Web.config ASP.NET を使用している場合) に追加する必要があります。 使用して、構成ファイルの内容を保護するには、接続文字列には、ユーザー名とパスワードなどの機密情報が含まれている場合[保護された構成](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration)です。

``` xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]  
> `providerName`コードを使用して、データベース プロバイダーが構成されているために App.config に保存された EF コア接続文字列では、設定は必要はありません。

使用する接続文字列を読み取ることができますし、`ConfigurationManager`コンテキストの内の API`OnConfiguring`メソッドです。 参照を追加する必要があります、`System.Configuration`フレームワーク アセンブリをこの API を使用することです。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="universal-windows-platform-uwp"></a>ユニバーサル Windows プラットフォーム (UWP)

UWP アプリケーションで接続文字列は、通常、同じローカル ファイル名を指定する SQLite 接続です。 通常、機密情報が含まれていない、アプリケーションを展開するように変更する必要はありません。 そのため、これらの接続文字列は、次のように、コード内に残す通常は問題ありませんです。 コードの外に移動する場合、UWP をサポートしている設定の概念を参照してください、 [UWP アプリの設定で](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)詳細についてはします。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
            optionsBuilder.UseSqlite("Data Source=blogging.db");
    }
}
```

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core では、構成システムは非常に柔軟なと接続文字列に格納`appsettings.json`、環境変数、ユーザー、シークレット ストアまたは別の構成ソース。 参照してください、[構成セクションの ASP.NET Core](https://docs.asp.net/en/latest/fundamentals/configuration.html)詳細についてはします。 次の例は、接続文字列に格納されている`appsettings.json`です。

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

コンテキストが通常で構成されている`Startup.cs`構成から読み取られている接続文字列を使用します。 注、`GetConnectionString()`メソッドはキーを持つは、構成値を検索`ConnectionStrings:<connection string name>`です。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
