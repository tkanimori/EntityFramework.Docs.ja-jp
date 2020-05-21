---
title: 接続文字列-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: 062a7f292d16deb3840fd116f270edb11c6e0687
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672921"
---
# <a name="connection-strings"></a><span data-ttu-id="4bdd9-102">接続文字列</span><span class="sxs-lookup"><span data-stu-id="4bdd9-102">Connection Strings</span></span>

<span data-ttu-id="4bdd9-103">ほとんどのデータベースプロバイダーでは、データベースに接続するために何らかの形式の接続文字列が必要です。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="4bdd9-104">この接続文字列には、保護する必要がある機密情報が含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="4bdd9-105">また、開発、テスト、運用などの環境間でアプリケーションを移動するときに、接続文字列の変更が必要になる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="winforms--wpf-applications"></a><span data-ttu-id="4bdd9-106">WinForms & WPF アプリケーション</span><span class="sxs-lookup"><span data-stu-id="4bdd9-106">WinForms & WPF Applications</span></span>

<span data-ttu-id="4bdd9-107">WinForms、WPF、および ASP.NET 4 アプリケーションには、テスト済みの接続文字列パターンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-107">WinForms, WPF, and ASP.NET 4 applications have a tried and tested connection string pattern.</span></span> <span data-ttu-id="4bdd9-108">接続文字列は、アプリケーションの app.config ファイル (ASP.NET を使用している場合は web.config) に追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-108">The connection string should be added to your application's App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="4bdd9-109">接続文字列にユーザー名やパスワードなどの機密情報が含まれている場合は、[シークレットマネージャーツール](/aspnet/core/security/app-secrets#secret-manager)を使用して構成ファイルの内容を保護することができます。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager).</span></span>

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
> <span data-ttu-id="4bdd9-110">`providerName`データベースプロバイダーはコードを使用して構成されているため、app.config に格納されている EF Core 接続文字列では、この設定は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="4bdd9-111">その後、 `ConfigurationManager` コンテキストのメソッドで API を使用して接続文字列を読み取ることができ `OnConfiguring` ます。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="4bdd9-112">この API を使用できるようにするには、フレームワークアセンブリへの参照を追加する必要がある場合があり `System.Configuration` ます。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="4bdd9-113">ユニバーサル Windows プラットフォーム (UWP)</span><span class="sxs-lookup"><span data-stu-id="4bdd9-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="4bdd9-114">UWP アプリケーションの接続文字列は、通常、ローカルファイル名を指定するだけの SQLite 接続です。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="4bdd9-115">これらは通常、機密情報を含んでいないため、アプリケーションの展開時に変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="4bdd9-116">そのため、次に示すように、これらの接続文字列は通常、コードに残しておくのが適切です。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="4bdd9-117">コードから移動する場合、UWP は設定の概念をサポートします。詳細については、 [uwp ドキュメントの「アプリ設定」セクション](/windows/uwp/app-settings/store-and-retrieve-app-data)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

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

## <a name="aspnet-core"></a><span data-ttu-id="4bdd9-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4bdd9-118">ASP.NET Core</span></span>

<span data-ttu-id="4bdd9-119">ASP.NET Core 構成システムは非常に柔軟であり、接続文字列は `appsettings.json` 、環境変数、ユーザーシークレットストア、または別の構成ソースに格納できます。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="4bdd9-120">詳細については[、ASP.NET Core のドキュメントの構成セクション](/aspnet/core/fundamentals/configuration)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-120">See the [Configuration section of the ASP.NET Core documentation](/aspnet/core/fundamentals/configuration) for more details.</span></span>

<span data-ttu-id="4bdd9-121">たとえば、[シークレットマネージャーツール](/aspnet/core/security/app-secrets#secret-manager)を使用してデータベースパスワードを格納し、スキャフォールディングで、単純にで構成される接続文字列を使用でき `Name=<database-alias>` ます。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-121">For instance, you can use the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager) to store your database password and then, in scaffolding, use a connection string that simply consists of `Name=<database-alias>`.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="4bdd9-122">または、次の例は、に格納されている接続文字列を示して `appsettings.json` います。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-122">Or the following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="4bdd9-123">その後、コンテキストは、通常、 `Startup.cs` 構成から読み取られる接続文字列を使用してで構成されます。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-123">Then the context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="4bdd9-124">メソッドは、 `GetConnectionString()` キーがである構成値を検索し `ConnectionStrings:<connection string name>` ます。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-124">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="4bdd9-125">この拡張メソッドを使用するには、この[拡張機能の名前空間](/dotnet/api/microsoft.extensions.configuration)をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="4bdd9-125">You need to import the [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
