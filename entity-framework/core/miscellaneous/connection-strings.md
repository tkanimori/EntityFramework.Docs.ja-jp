---
title: 接続文字列-EF Core
description: Entity Framework Core を使用したさまざまな環境での接続文字列の管理
author: rowanmiller
ms.date: 10/27/2016
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: e4283ada88a557e4f1e3eeea3de2634a7d0dce61
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071681"
---
# <a name="connection-strings"></a>接続文字列

ほとんどのデータベースプロバイダーでは、データベースに接続するために何らかの形式の接続文字列が必要です。 この接続文字列には、保護する必要がある機密情報が含まれている場合があります。 また、開発、テスト、運用などの環境間でアプリケーションを移動するときに、接続文字列の変更が必要になる場合もあります。

## <a name="winforms--wpf-applications"></a>WinForms & WPF アプリケーション

WinForms、WPF、および ASP.NET 4 アプリケーションには、テスト済みの接続文字列パターンが用意されています。 接続文字列は、アプリケーションの App.config ファイル (ASP.NET を使用している場合は Web.config) に追加する必要があります。 接続文字列にユーザー名やパスワードなどの機密情報が含まれている場合は、 [シークレットマネージャーツール](/aspnet/core/security/app-secrets#secret-manager)を使用して構成ファイルの内容を保護することができます。

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
> `providerName`データベースプロバイダーがコードによって構成されているため、App.config に格納されている EF Core 接続文字列では、この設定は必要ありません。

その後、 `ConfigurationManager` コンテキストのメソッドで API を使用して接続文字列を読み取ることができ `OnConfiguring` ます。 この API を使用できるようにするには、`System.Configuration` フレームワーク アセンブリに参照を追加しなければならない場合があります。

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

UWP アプリケーションの接続文字列は、通常、ローカルファイル名を指定するだけの SQLite 接続です。 これらは通常、機密情報を含んでいないため、アプリケーションの展開時に変更する必要はありません。 そのため、次に示すように、これらの接続文字列は通常、コードに残しておくのが適切です。 コードから移動する場合、UWP は設定の概念をサポートします。詳細については、 [uwp ドキュメントの「アプリ設定」セクション](/windows/uwp/app-settings/store-and-retrieve-app-data) を参照してください。

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

ASP.NET Core 構成システムは非常に柔軟であり、接続文字列は `appsettings.json` 、環境変数、ユーザーシークレットストア、または別の構成ソースに格納できます。 詳細については [、ASP.NET Core のドキュメントの構成セクション](/aspnet/core/fundamentals/configuration) を参照してください。

たとえば、 [シークレットマネージャーツール](/aspnet/core/security/app-secrets#secret-manager) を使用してデータベースパスワードを格納し、スキャフォールディングで、単純にで構成される接続文字列を使用でき `Name=<database-alias>` ます。

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings.YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

または、次の例は、に格納されている接続文字列を示して `appsettings.json` います。

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

その後、コンテキストは、通常、 `Startup.cs` 構成から読み取られる接続文字列を使用してで構成されます。 メソッドは、 `GetConnectionString()` キーがである構成値を検索し `ConnectionStrings:<connection string name>` ます。 この拡張メソッドを使用するには、 [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) 名前空間をインポートする必要があります。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
