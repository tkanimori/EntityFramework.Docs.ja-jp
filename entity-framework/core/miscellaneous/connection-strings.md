---
title: 接続文字列 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: c306f9ca7a51fc9e3db18e883fd44f56dd1a3cb4
ms.sourcegitcommit: e90d6cfa3e96f10b8b5275430759a66a0c714ed1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286445"
---
# <a name="connection-strings"></a>接続文字列

ほとんどのデータベース プロバイダーでは、何らかの形式のデータベースに接続する接続文字列が必要です。 場合によってこの接続文字列には、保護する必要がある機密情報が含まれています。 また、アプリケーションを開発、テスト、および運用環境などの環境間で移動すると、接続文字列を変更する必要があります。

## <a name="net-framework-applications"></a>.NET framework アプリケーション

WinForms、WPF、コンソール、および ASP.NET 4 などの .NET framework アプリケーションでは、実証済みの接続文字列のパターンがあります。 接続文字列は、アプリケーションの App.config ファイル (Web.config ASP.NET を使用している場合) に追加する必要があります。 接続文字列には、ユーザー名やパスワードなどの機密情報が含まれている場合は、使用して、構成ファイルの内容を保護することができます[保護された構成](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration)します。

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
> `providerName`設定は、コードを使用して、データベース プロバイダーが構成されているために、App.config に格納されている EF Core 接続文字列では必要ありません。

使用して、接続文字列を読み取ることができますし、 `ConfigurationManager` API では、コンテキストの`OnConfiguring`メソッド。 参照を追加する必要があります、`System.Configuration`この API を使用できるフレームワーク アセンブリ。

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

UWP アプリケーションで接続文字列は、通常、ローカルのファイル名をだけを指定する SQLite 接続です。 通常は、機密情報が含まれていないと、アプリケーションを展開するように変更する必要はありません。 そのため、これらの接続文字列は、次に示すようにコードでは、通常は問題ありません。 コードの外に移動する場合、UWP がサポートの設定の概念を参照してください、 [UWP ドキュメントのアプリ設定セクション](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)詳細についてはします。

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

ASP.NET Core 構成システムは非常に柔軟性と接続文字列に格納される`appsettings.json`、環境変数、user secret ストア、または別の構成ソース。 参照してください、 [ASP.NET Core のドキュメントの構成セクション](https://docs.asp.net/en/latest/fundamentals/configuration.html)の詳細。 次の例では、接続文字列に格納されている`appsettings.json`します。

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

コンテキストが構成されている通常`Startup.cs`構成から読み取られる接続文字列に置き換えます。 注、`GetConnectionString()`メソッドはキーが構成値を検索`ConnectionStrings:<connection string name>`します。 インポートする必要がある、 [Microsoft.Extensions.Configuration](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration)この拡張メソッドを使用する名前空間。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
