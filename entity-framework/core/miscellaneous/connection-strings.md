---
title: 接続文字列 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: aeb0f5f8-b212-4f89-ae83-c642a5190ba0
uid: core/miscellaneous/connection-strings
ms.openlocfilehash: 942865effba7b491dd950886ea30b69a86f1186c
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997672"
---
# <a name="connection-strings"></a><span data-ttu-id="9d2bc-102">接続文字列</span><span class="sxs-lookup"><span data-stu-id="9d2bc-102">Connection Strings</span></span>

<span data-ttu-id="9d2bc-103">ほとんどのデータベース プロバイダーでは、何らかの形式のデータベースに接続する接続文字列が必要です。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-103">Most database providers require some form of connection string to connect to the database.</span></span> <span data-ttu-id="9d2bc-104">場合によってこの接続文字列には、保護する必要がある機密情報が含まれています。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-104">Sometimes this connection string contains sensitive information that needs to be protected.</span></span> <span data-ttu-id="9d2bc-105">また、アプリケーションを開発、テスト、および運用環境などの環境間で移動すると、接続文字列を変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-105">You may also need to change the connection string as you move your application between environments, such as development, testing, and production.</span></span>

## <a name="net-framework-applications"></a><span data-ttu-id="9d2bc-106">.NET framework アプリケーション</span><span class="sxs-lookup"><span data-stu-id="9d2bc-106">.NET Framework Applications</span></span>

<span data-ttu-id="9d2bc-107">WinForms、WPF、コンソール、および ASP.NET 4 などの .NET framework アプリケーションでは、実証済みの接続文字列のパターンがあります。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-107">.NET Framework applications, such as WinForms, WPF, Console, and ASP.NET 4, have a tried and tested connection string pattern.</span></span> <span data-ttu-id="9d2bc-108">接続文字列は、アプリケーションの App.config ファイル (Web.config ASP.NET を使用している場合) に追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-108">The connection string should be added to your applications App.config file (Web.config if you are using ASP.NET).</span></span> <span data-ttu-id="9d2bc-109">接続文字列には、ユーザー名やパスワードなどの機密情報が含まれている場合は、使用して、構成ファイルの内容を保護することができます[保護された構成](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration)します。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-109">If your connection string contains sensitive information, such as username and password, you can protect the contents of the configuration file using [Protected Configuration](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-strings-and-configuration-files#encrypting-configuration-file-sections-using-protected-configuration).</span></span>

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
> <span data-ttu-id="9d2bc-110">`providerName`設定は、コードを使用して、データベース プロバイダーが構成されているために、App.config に格納されている EF Core 接続文字列では必要ありません。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-110">The `providerName` setting is not required on EF Core connection strings stored in App.config because the database provider is configured via code.</span></span>

<span data-ttu-id="9d2bc-111">使用して、接続文字列を読み取ることができますし、 `ConfigurationManager` API では、コンテキストの`OnConfiguring`メソッド。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-111">You can then read the connection string using the `ConfigurationManager` API in your context's `OnConfiguring` method.</span></span> <span data-ttu-id="9d2bc-112">参照を追加する必要があります、`System.Configuration`この API を使用できるフレームワーク アセンブリ。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-112">You may need to add a reference to the `System.Configuration` framework assembly to be able to use this API.</span></span>

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

## <a name="universal-windows-platform-uwp"></a><span data-ttu-id="9d2bc-113">ユニバーサル Windows プラットフォーム (UWP)</span><span class="sxs-lookup"><span data-stu-id="9d2bc-113">Universal Windows Platform (UWP)</span></span>

<span data-ttu-id="9d2bc-114">UWP アプリケーションで接続文字列は、通常、ローカルのファイル名をだけを指定する SQLite 接続です。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-114">Connection strings in a UWP application are typically a SQLite connection that just specifies a local filename.</span></span> <span data-ttu-id="9d2bc-115">通常は、機密情報が含まれていないと、アプリケーションを展開するように変更する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-115">They typically do not contain sensitive information, and do not need to be changed as an application is deployed.</span></span> <span data-ttu-id="9d2bc-116">そのため、これらの接続文字列は、次に示すようにコードでは、通常は問題ありません。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-116">As such, these connection strings are usually fine to be left in code, as shown below.</span></span> <span data-ttu-id="9d2bc-117">コードの外に移動する場合、UWP がサポートの設定の概念を参照してください、 [UWP ドキュメントのアプリ設定セクション](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-117">If you wish to move them out of code then UWP supports the concept of settings, see the [App Settings section of the UWP documentation](https://docs.microsoft.com/windows/uwp/app-settings/store-and-retrieve-app-data) for details.</span></span>

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

## <a name="aspnet-core"></a><span data-ttu-id="9d2bc-118">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9d2bc-118">ASP.NET Core</span></span>

<span data-ttu-id="9d2bc-119">ASP.NET Core 構成システムは非常に柔軟性と接続文字列に格納される`appsettings.json`、環境変数、user secret ストア、または別の構成ソース。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-119">In ASP.NET Core the configuration system is very flexible, and the connection string could be stored in `appsettings.json`, an environment variable, the user secret store, or another configuration source.</span></span> <span data-ttu-id="9d2bc-120">参照してください、 [ASP.NET Core のドキュメントの構成セクション](https://docs.asp.net/en/latest/fundamentals/configuration.html)の詳細。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-120">See the [Configuration section of the ASP.NET Core documentation](https://docs.asp.net/en/latest/fundamentals/configuration.html) for more details.</span></span> <span data-ttu-id="9d2bc-121">次の例では、接続文字列に格納されている`appsettings.json`します。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-121">The following example shows the connection string stored in `appsettings.json`.</span></span>

``` json
{
  "ConnectionStrings": {
    "BloggingDatabase": "Server=(localdb)\\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;"
  },
}
```

<span data-ttu-id="9d2bc-122">コンテキストが構成されている通常`Startup.cs`構成から読み取られる接続文字列に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-122">The context is typically configured in `Startup.cs` with the connection string being read from configuration.</span></span> <span data-ttu-id="9d2bc-123">注、`GetConnectionString()`メソッドはキーが構成値を検索`ConnectionStrings:<connection string name>`します。</span><span class="sxs-lookup"><span data-stu-id="9d2bc-123">Note the `GetConnectionString()` method looks for a configuration value whose key is `ConnectionStrings:<connection string name>`.</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("BloggingDatabase")));
}
```
