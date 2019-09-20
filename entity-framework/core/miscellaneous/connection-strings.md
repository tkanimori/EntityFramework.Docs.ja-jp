---
title: 接続文字列-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: ed89d6d09b15b0dea7fd8bc3ff3e3f631495ecb7
ms.sourcegitcommit: cbaa6cc89bd71d5e0bcc891e55743f0e8ea3393b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149110"
---
# <a name="connection-strings"></a>接続文字列

ほとんどのデータベースプロバイダーでは、データベースに接続するために何らかの形式の接続文字列が必要です。 この接続文字列には、保護する必要がある機密情報が含まれている場合があります。 また、開発、テスト、運用などの環境間でアプリケーションを移動するときに、接続文字列の変更が必要になる場合もあります。

## <a name="winforms--wpf-applications"></a>WinForms & WPF アプリケーション

WinForms、WPF、および ASP.NET 4 アプリケーションには、テスト済みの接続文字列パターンが用意されています。 接続文字列は、アプリケーションの app.config ファイル (ASP.NET を使用している場合は web.config) に追加する必要があります。 接続文字列にユーザー名やパスワードなどの機密情報が含まれている場合は、[シークレットマネージャーツール](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)を使用して構成ファイルの内容を保護することができます。

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
> データベース`providerName`プロバイダーはコードを使用して構成されているため、app.config に格納されている EF Core 接続文字列では、この設定は必要ありません。

その後、コンテキストの`ConfigurationManager` `OnConfiguring`メソッドで API を使用して接続文字列を読み取ることができます。 この API を使用できるようにするに`System.Configuration`は、フレームワークアセンブリへの参照を追加する必要がある場合があります。

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

UWP アプリケーションの接続文字列は、通常、ローカルファイル名を指定するだけの SQLite 接続です。 これらは通常、機密情報を含んでいないため、アプリケーションの展開時に変更する必要はありません。 そのため、次に示すように、これらの接続文字列は通常、コードに残しておくのが適切です。 コードから移動する場合、UWP は設定の概念をサポートします。詳細については、 [uwp ドキュメントの「アプリ設定」セクション](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)を参照してください。

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

ASP.NET Core 構成システムは非常に柔軟であり、接続文字列は、環境変数、 `appsettings.json`ユーザーシークレットストア、または別の構成ソースに格納できます。 詳細については[、ASP.NET Core のドキュメントの構成セクション](https://docs.asp.net/en/latest/fundamentals/configuration.html)を参照してください。 次の例は、に`appsettings.json`格納されている接続文字列を示しています。

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

コンテキストは、通常、構成`Startup.cs`から読み取られる接続文字列を使用してで構成されます。 メソッドは`GetConnectionString()` 、キーがで`ConnectionStrings:<connection string name>`ある構成値を検索します。 この拡張メソッドを使用するには、この[拡張機能の名前空間](https://docs.microsoft.com/dotnet/api/microsoft.extensions.configuration)をインポートする必要があります。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
