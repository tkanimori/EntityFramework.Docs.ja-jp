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
# <a name="connection-strings"></a><span data-ttu-id="bf0e6-102">接続文字列</span><span class="sxs-lookup"><span data-stu-id="bf0e6-102">Connection Strings</span></span>

<span data-ttu-id="bf0e6-103">ほとんどのデータベース プロバイダーでは、何らかの形式のデータベースに接続する接続文字列が必要です。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="bf0e6-104">場合によってこの接続文字列には、保護する必要がある機密情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="bf0e6-105">また、アプリケーションを開発、テスト、および運用環境などの環境間で移動すると、接続文字列を変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="net-framework-applications"></a><span data-ttu-id="bf0e6-106">.NET framework アプリケーション</span><span class="sxs-lookup"><span data-stu-id="bf0e6-106">.NET Framework Applications</span></span>

<span data-ttu-id="bf0e6-107">WinForms、WPF、コンソール、および ASP.NET 4 などの .NET framework アプリケーションでは、しようとして、テスト対象の接続文字列のパターンがあります。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-107">.NET Framework applications, such as WinForms, WPF, Console, and ASP.NET 4, have a tried and tested connection string pattern.</span></span> <span data-ttu-id="bf0e6-108">接続文字列は、アプリケーションの App.config ファイル (Web.config ASP.NET を使用している場合) に追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-108">The connection string should be added to your applications App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="bf0e6-109">使用して、構成ファイルの内容を保護するには、接続文字列には、ユーザー名とパスワードなどの機密情報が含まれている場合[保護された構成](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration)です。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using [Protected Configuration](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration).</span></span>

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
> <span data-ttu-id="bf0e6-110">`providerName`コードを使用して、データベース プロバイダーが構成されているために App.config に保存された EF コア接続文字列では、設定は必要はありません。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="bf0e6-111">使用する接続文字列を読み取ることができますし、`ConfigurationManager`コンテキストの内の API`OnConfiguring`メソッドです。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="bf0e6-112">参照を追加する必要があります、`System.Configuration`フレームワーク アセンブリをこの API を使用することです。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="bf0e6-113">ユニバーサル Windows プラットフォーム (UWP)</span><span class="sxs-lookup"><span data-stu-id="bf0e6-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="bf0e6-114">UWP アプリケーションで接続文字列は、通常、同じローカル ファイル名を指定する SQLite 接続です。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="bf0e6-115">通常、機密情報が含まれていない、アプリケーションを展開するように変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="bf0e6-116">そのため、これらの接続文字列は、次のように、コード内に残す通常は問題ありませんです。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="bf0e6-117">コードの外に移動する場合、UWP をサポートしている設定の概念を参照してください、 [UWP アプリの設定で](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

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

## <a name="aspnet-core"></a><span data-ttu-id="bf0e6-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf0e6-118">ASP.NET Core</span></span>

<span data-ttu-id="bf0e6-119">ASP.NET Core では、構成システムは非常に柔軟なと接続文字列に格納`appsettings.json`、環境変数、ユーザー、シークレット ストアまたは別の構成ソース。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="bf0e6-120">参照してください、[構成セクションの ASP.NET Core](https://docs.asp.net/en/latest/fundamentals/configuration.html)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-120">See the [Configuration section of the ASP.NET Core documentation](https://docs.asp.net/en/latest/fundamentals/configuration.html) for more details.</span></span> <span data-ttu-id="bf0e6-121">次の例は、接続文字列に格納されている`appsettings.json`です。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-121">The following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="bf0e6-122">コンテキストが通常で構成されている`Startup.cs`構成から読み取られている接続文字列を使用します。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-122">The context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="bf0e6-123">注、`GetConnectionString()`メソッドはキーを持つは、構成値を検索`ConnectionStrings:<connection string name>`です。</span><span class="sxs-lookup"><span data-stu-id="bf0e6-123">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
