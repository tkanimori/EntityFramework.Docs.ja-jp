---
title: 接続文字列-EF Core
description: Entity Framework Core を使用したさまざまな環境での接続文字列の管理
author: bricelam
ms.date: 10/27/2016
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: 75e364eddd02087cffdffd1c152d1e988a99817b
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983561"
---
# <a name="connection-strings"></a><span data-ttu-id="e9467-103">接続文字列</span><span class="sxs-lookup"><span data-stu-id="e9467-103">Connection Strings</span></span>

<span data-ttu-id="e9467-104">ほとんどのデータベースプロバイダーでは、データベースに接続するために何らかの形式の接続文字列が必要です。</span><span class="sxs-lookup"><span data-stu-id="e9467-104">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="e9467-105">この接続文字列には、保護する必要がある機密情報が含まれている場合があります。</span><span class="sxs-lookup"><span data-stu-id="e9467-105">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="e9467-106">また、開発、テスト、運用などの環境間でアプリケーションを移動するときに、接続文字列の変更が必要になる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="e9467-106">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="e9467-107">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e9467-107">ASP.NET Core</span></span>

<span data-ttu-id="e9467-108">ASP.NET Core 構成システムは非常に柔軟であり、接続文字列は `appsettings.json` 、環境変数、ユーザーシークレットストア、または別の構成ソースに格納できます。</span><span class="sxs-lookup"><span data-stu-id="e9467-108">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="e9467-109">詳細については [、ASP.NET Core のドキュメントの構成セクション](/aspnet/core/fundamentals/configuration) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e9467-109">See the [Configuration section of the ASP.NET Core documentation](/aspnet/core/fundamentals/configuration) for more details.</span></span>

<span data-ttu-id="e9467-110">たとえば、 [シークレットマネージャーツール](/aspnet/core/security/app-secrets#secret-manager) を使用してデータベースパスワードを格納し、スキャフォールディングで、単純にで構成される接続文字列を使用でき `Name=<database-alias>` ます。</span><span class="sxs-lookup"><span data-stu-id="e9467-110">For instance, you can use the [Secret Manager tool](/aspnet/core/security/app-secrets#secret-manager) to store your database password and then, in scaffolding, use a connection string that simply consists of `Name=<database-alias>`.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings:YourDatabaseAlias "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=YourDatabase"
dotnet ef dbcontext scaffold Name=ConnectionStrings:YourDatabaseAlias Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="e9467-111">または、次の例は、に格納されている接続文字列を示して `appsettings.json` います。</span><span class="sxs-lookup"><span data-stu-id="e9467-111">Or the following example shows the connection string stored in `appsettings.json`.</span></span>

```json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="e9467-112">その後、コンテキストは、通常、 `Startup.cs` 構成から読み取られる接続文字列を使用してで構成されます。</span><span class="sxs-lookup"><span data-stu-id="e9467-112">Then the context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="e9467-113">メソッドは、 `GetConnectionString()` キーがである構成値を検索し `ConnectionStrings:<connection string name>` ます。</span><span class="sxs-lookup"><span data-stu-id="e9467-113">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span> <span data-ttu-id="e9467-114">この拡張メソッドを使用するには、 [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) 名前空間をインポートする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e9467-114">You need to import the [Microsoft.Extensions.Configuration](/dotnet/api/microsoft.extensions.configuration) namespace to use this extension method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```

## <a name="winforms--wpf-applications"></a><span data-ttu-id="e9467-115">WinForms & WPF アプリケーション</span><span class="sxs-lookup"><span data-stu-id="e9467-115">WinForms & WPF Applications</span></span>

<span data-ttu-id="e9467-116">WinForms、WPF、および ASP.NET 4 アプリケーションには、テスト済みの接続文字列パターンが用意されています。</span><span class="sxs-lookup"><span data-stu-id="e9467-116">WinForms, WPF, and ASP.NET 4 applications have a tried and tested connection string pattern.</span></span> <span data-ttu-id="e9467-117">接続文字列は、アプリケーションの App.config ファイル (ASP.NET を使用している場合は Web.config) に追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e9467-117">The connection string should be added to your application's App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="e9467-118">接続文字列にユーザー名やパスワードなどの機密情報が含まれている場合は、 [保護さ](/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration)れた構成を使用して構成ファイルの内容を保護することができます。</span><span class="sxs-lookup"><span data-stu-id="e9467-118">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using [Protected Configuration](/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration).</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>

  <connectionStrings>
    <add name="BloggingDatabase"
         connectionString="Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" />
  </connectionStrings>
</configuration>
```

> [!TIP]
> <span data-ttu-id="e9467-119">`providerName`データベースプロバイダーがコードによって構成されているため、App.config に格納されている EF Core 接続文字列では、この設定は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="e9467-119">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="e9467-120">その後、 `ConfigurationManager` コンテキストのメソッドで API を使用して接続文字列を読み取ることができ `OnConfiguring` ます。</span><span class="sxs-lookup"><span data-stu-id="e9467-120">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="e9467-121">この API を使用できるようにするには、`System.Configuration` フレームワーク アセンブリに参照を追加しなければならない場合があります。</span><span class="sxs-lookup"><span data-stu-id="e9467-121">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

```csharp
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

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="e9467-122">ユニバーサル Windows プラットフォーム (UWP)</span><span class="sxs-lookup"><span data-stu-id="e9467-122">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="e9467-123">UWP アプリケーションの接続文字列は、通常、ローカルファイル名を指定するだけの SQLite 接続です。</span><span class="sxs-lookup"><span data-stu-id="e9467-123">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="e9467-124">これらは通常、機密情報を含んでいないため、アプリケーションの展開時に変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e9467-124">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="e9467-125">そのため、次に示すように、これらの接続文字列は通常、コードに残しておくのが適切です。</span><span class="sxs-lookup"><span data-stu-id="e9467-125">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="e9467-126">コードから移動する場合、UWP は設定の概念をサポートします。詳細については、 [uwp ドキュメントの「アプリ設定」セクション](/windows/uwp/app-settings/store-and-retrieve-app-data) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e9467-126">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

```csharp
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
