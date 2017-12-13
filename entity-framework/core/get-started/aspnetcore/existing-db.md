---
title: "ASP.NET Core - 既存のデータベース - EF Core の概要"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2bc68bea-ff77-4860-bf0b-cf00db6712a0
ms.technology: entity-framework-core
uid: core/get-started/aspnetcore/existing-db
ms.openlocfilehash: afd99d68d2ba25ce58a21dc48d2c7ce27f208807
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="getting-started-with-ef-core-on-aspnet-core-with-an-existing-database"></a><span data-ttu-id="9de2b-102">既存のデータベースを使用した ASP.NET Core での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="9de2b-102">Getting Started with EF Core on ASP.NET Core with an Existing Database</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="9de2b-103">[.NET Core SDK](https://www.microsoft.com/net/download/core) では、`project.json` と Visual Studio 2015 はサポートされなくなりました。</span><span class="sxs-lookup"><span data-stu-id="9de2b-103">The [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or Visual Studio 2015.</span></span> <span data-ttu-id="9de2b-104">.NET Core で開発を行っているすべてのユーザーには、[project.json から csproj に移行](https://docs.microsoft.com/dotnet/articles/core/migration/)、および [Visual Studio 2017](https://www.visualstudio.com/downloads/) に移行することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-104">Everyone doing .NET Core development is encouraged to [migrate from project.json to csproj](https://docs.microsoft.com/dotnet/articles/core/migration/) and [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

<span data-ttu-id="9de2b-105">このチュートリアルでは、Entity Framework を使用して基本的なデータ アクセスを実行する ASP.NET Core MVC アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-105">In this walkthrough, you will build an ASP.NET Core MVC application that performs basic data access using Entity Framework.</span></span> <span data-ttu-id="9de2b-106">既存のデータベースに基づく Entity Framework モデルを作成するために、リバース エンジニアリングを使用します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-106">You will use reverse engineering to create an Entity Framework model based on an existing database.</span></span>

> [!TIP]  
> <span data-ttu-id="9de2b-107">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="9de2b-107">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9de2b-108">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="9de2b-108">Prerequisites</span></span>

<span data-ttu-id="9de2b-109">このチュートリアルを完了するには、次の条件を満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="9de2b-109">The following prerequisites are needed to complete this walkthrough:</span></span>

* <span data-ttu-id="9de2b-110">[Visual Studio 2017 15.3](https://www.visualstudio.com/downloads/) および以下のワークロード。</span><span class="sxs-lookup"><span data-stu-id="9de2b-110">[Visual Studio 2017 15.3](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="9de2b-111">**ASP.NET と Web 開発** (**[Web & Cloud]\(Web とクラウド\)** の下)</span><span class="sxs-lookup"><span data-stu-id="9de2b-111">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="9de2b-112">**.NET Core クロスプラットフォームの開発** (**[他のツールセット]** の下)</span><span class="sxs-lookup"><span data-stu-id="9de2b-112">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="9de2b-113">[.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="9de2b-113">[.NET Core 2.0 SDK](https://www.microsoft.com/net/download/core).</span></span>
* [<span data-ttu-id="9de2b-114">Blogging データベース</span><span class="sxs-lookup"><span data-stu-id="9de2b-114">Blogging database</span></span>](#blogging-database)

### <a name="blogging-database"></a><span data-ttu-id="9de2b-115">Blogging データベース</span><span class="sxs-lookup"><span data-stu-id="9de2b-115">Blogging database</span></span>

<span data-ttu-id="9de2b-116">このチュートリアルでは、お使いの LocalDb インスタンス上の **Blogging** データベースを、既存のデータベースとして使用します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-116">This tutorial uses a **Blogging** database on your LocalDb instance as the existing database.</span></span>

> [!TIP]  
> <span data-ttu-id="9de2b-117">**Blogging** データベースを、別のチュートリアルの一部として既に作成している場合は、次の手順を省略できます。</span><span class="sxs-lookup"><span data-stu-id="9de2b-117">If you have already created the **Blogging** database as part of another tutorial, you can skip these steps.</span></span>

* <span data-ttu-id="9de2b-118">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="9de2b-118">Open Visual Studio</span></span>
* <span data-ttu-id="9de2b-119">**[ツール] -> [データベースへの接続]**</span><span class="sxs-lookup"><span data-stu-id="9de2b-119">**Tools -> Connect to Database...**</span></span>
* <span data-ttu-id="9de2b-120">**[Microsoft SQL Server]** を選択して **[続行]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-120">Select **Microsoft SQL Server** and click **Continue**</span></span>
* <span data-ttu-id="9de2b-121">**[サーバー名]** に「**(localdb) \mssqllocaldb**」を入力します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-121">Enter **(localdb)\mssqllocaldb** as the **Server Name**</span></span>
* <span data-ttu-id="9de2b-122">**[データベース名]** に「**master**」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-122">Enter **master** as the **Database Name** and click **OK**</span></span>
* <span data-ttu-id="9de2b-123">**[サーバー エクスプローラー]** 内の **[データ接続]** の下に、master データベースが表示されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="9de2b-123">The master database is now displayed under **Data Connections** in **Server Explorer**</span></span>
* <span data-ttu-id="9de2b-124">**[サーバー エクスプローラー]** でこのデータベースを右クリックし、**[新しいクエリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-124">Right-click on the database in **Server Explorer** and select **New Query**</span></span>
* <span data-ttu-id="9de2b-125">以下に表示されているスクリプトを、クエリ エディターにコピーします</span><span class="sxs-lookup"><span data-stu-id="9de2b-125">Copy the script, listed below, into the query editor</span></span>
* <span data-ttu-id="9de2b-126">クエリ エディターを右クリックし、**[実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-126">Right-click on the query editor and select **Execute**</span></span>

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a><span data-ttu-id="9de2b-127">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="9de2b-127">Create a new project</span></span>

* <span data-ttu-id="9de2b-128">Visual Studio 2017 を開きます</span><span class="sxs-lookup"><span data-stu-id="9de2b-128">Open Visual Studio 2017</span></span>
* <span data-ttu-id="9de2b-129">**[ファイル] -> [新規] -> [プロジェクト...]**</span><span class="sxs-lookup"><span data-stu-id="9de2b-129">**File -> New -> Project...**</span></span>
* <span data-ttu-id="9de2b-130">左側のメニューから **[インストール済み] -> [テンプレート] -> [Visual C#] -> [Web]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-130">From the left menu select **Installed -> Templates -> Visual C# -> Web**</span></span>
* <span data-ttu-id="9de2b-131">**[ASP.NET Core Web アプリケーション (.NET Core)]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-131">Select the **ASP.NET Core Web Application (.NET Core)** project template</span></span>
* <span data-ttu-id="9de2b-132">名前に「**EFGetStarted.AspNetCore.ExistingDb**」を入力して **[OK]** をクリックします</span><span class="sxs-lookup"><span data-stu-id="9de2b-132">Enter **EFGetStarted.AspNetCore.ExistingDb** as the name and click **OK**</span></span>
* <span data-ttu-id="9de2b-133">**[新しい ASP.NET Core Web アプリケーション]** ダイアログが表示されるのを待ちます</span><span class="sxs-lookup"><span data-stu-id="9de2b-133">Wait for the **New ASP.NET Core Web Application** dialog to appear</span></span>
* <span data-ttu-id="9de2b-134">**[ASP.NET Core Templates 2.0]\(ASP.NET Core テンプレート 2.0\)** で、**[Web アプリケーション (モデル ビュー コントローラー)]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-134">Under **ASP.NET Core Templates 2.0** select the **Web Application (Model-View-Controller)**</span></span>
* <span data-ttu-id="9de2b-135">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-135">Ensure that **Authentication** is set to **No Authentication**</span></span>
* <span data-ttu-id="9de2b-136">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-136">Click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="9de2b-137">Entity Framework をインストールする</span><span class="sxs-lookup"><span data-stu-id="9de2b-137">Install Entity Framework</span></span>

<span data-ttu-id="9de2b-138">EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-138">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="9de2b-139">このチュートリアルでは SQL Server を使用します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-139">This walkthrough uses SQL Server.</span></span> <span data-ttu-id="9de2b-140">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9de2b-140">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="9de2b-141">**[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="9de2b-141">**Tools > NuGet Package Manager > Package Manager Console**</span></span>

* <span data-ttu-id="9de2b-142">`Install-Package Microsoft.EntityFrameworkCore.SqlServer` を実行します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-142">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="9de2b-143">いくつかの Entity Framework Tools を使用して、データベースからモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-143">We will be using some Entity Framework Tools to create a model from the database.</span></span> <span data-ttu-id="9de2b-144">そのため、ツールのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-144">So we will install the tools package as well:</span></span>

* <span data-ttu-id="9de2b-145">`Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-145">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

<span data-ttu-id="9de2b-146">後で、いくつかの ASP.NET Core スキャフォールディング ツールを使用して、コント ローラーとビューを作成します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-146">We will be using some ASP.NET Core Scaffolding tools to create controllers and views later on.</span></span> <span data-ttu-id="9de2b-147">そのため、このデザイン パッケージもインストールします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-147">So we will install this design package as well:</span></span>

* <span data-ttu-id="9de2b-148">`Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design` を実行します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-148">Run `Install-Package Microsoft.VisualStudio.Web.CodeGeneration.Design`</span></span>

## <a name="reverse-engineer-your-model"></a><span data-ttu-id="9de2b-149">モデルのリバース エンジニアリングを行う</span><span class="sxs-lookup"><span data-stu-id="9de2b-149">Reverse engineer your model</span></span>

<span data-ttu-id="9de2b-150">ここで、お使いの既存のデータベースに基づいて、EF モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-150">Now it's time to create the EF model based on your existing database.</span></span>

* <span data-ttu-id="9de2b-151">**[ツール] –> [NuGet パッケージ マネージャー] –> [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="9de2b-151">**Tools –> NuGet Package Manager –> Package Manager Console**</span></span>
* <span data-ttu-id="9de2b-152">次のコマンドを実行して、既存のデータベースからモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-152">Run the following command to create a model from the existing database:</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="9de2b-153">`The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet` という内容のエラーが表示された場合は、Visual Studio を閉じて再度開きます。</span><span class="sxs-lookup"><span data-stu-id="9de2b-153">If you receive an error stating `The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet`, then close and reopen Visual Studio.</span></span>

> [!TIP]  
> <span data-ttu-id="9de2b-154">上記のコマンドに引数 `-Tables` を追加すると、エンティティを生成するテーブルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="9de2b-154">You can specify which tables you want to generate entities for by adding the `-Tables` argument to the command above.</span></span> <span data-ttu-id="9de2b-155">例:</span><span class="sxs-lookup"><span data-stu-id="9de2b-155">E.g.</span></span> <span data-ttu-id="9de2b-156">`-Tables Blog,Post`。</span><span class="sxs-lookup"><span data-stu-id="9de2b-156">`-Tables Blog,Post`.</span></span>

<span data-ttu-id="9de2b-157">リバース エンジニアリングのプロセスにより、既存のデータベースのスキーマに基づいてエンティティ クラス (`Blog.cs` & `Post.cs`) と派生コンテキスト (`BloggingContext.cs`) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="9de2b-157">The reverse engineer process created entity classes (`Blog.cs` & `Post.cs`) and a derived context (`BloggingContext.cs`) based on the schema of the existing database.</span></span>

 <span data-ttu-id="9de2b-158">エンティティ クラスはクエリを実行し保存するデータを表す、単純な C# オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="9de2b-158">The entity classes are simple C# objects that represent the data you will be querying and saving.</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Blog.cs)]

 <span data-ttu-id="9de2b-159">コンテキストはデータベースのセッションを表しており、これによってエンティティ クラスのインスタンスにクエリを実行し保存できます。</span><span class="sxs-lookup"><span data-stu-id="9de2b-159">The context represents a session with the database and allows you to query and save instances of the entity classes.</span></span>

<!-- Static code listing, rather than a linked file, because the walkthrough modifies the context file heavily -->
 ``` csharp
public partial class BloggingContext : DbContext
{
    public virtual DbSet<Blog> Blog { get; set; }
    public virtual DbSet<Post> Post { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        if (!optionsBuilder.IsConfigured)
        {
            #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
        }
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>(entity =>
        {
            entity.Property(e => e.Url).IsRequired();
        });

        modelBuilder.Entity<Post>(entity =>
        {
            entity.HasOne(d => d.Blog)
                .WithMany(p => p.Post)
                .HasForeignKey(d => d.BlogId);
        });
    }
}
```

## <a name="register-your-context-with-dependency-injection"></a><span data-ttu-id="9de2b-160">依存関係の挿入にコンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-160">Register your context with dependency injection</span></span>

<span data-ttu-id="9de2b-161">依存関係の挿入の概念は、ASP.NET Core の中心となるものです。</span><span class="sxs-lookup"><span data-stu-id="9de2b-161">The concept of dependency injection is central to ASP.NET Core.</span></span> <span data-ttu-id="9de2b-162">サービス (`BloggingContext` など) は、アプリケーションの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="9de2b-162">Services (such as `BloggingContext`) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9de2b-163">これらのサービス (MVC コント ローラーなど) を必要とするコンポーネントには、コンス トラクターのパラメーターまたはプロパティを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="9de2b-163">Components that require these services (such as your MVC controllers) are then provided these services via constructor parameters or properties.</span></span> <span data-ttu-id="9de2b-164">依存関係の挿入の詳細については、ASP.NET サイトの[依存関係の挿入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9de2b-164">For more information on dependency injection see the [Dependency Injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) article on the ASP.NET site.</span></span>

### <a name="remove-inline-context-configuration"></a><span data-ttu-id="9de2b-165">インライン コンテキスト構成の削除</span><span class="sxs-lookup"><span data-stu-id="9de2b-165">Remove inline context configuration</span></span>

<span data-ttu-id="9de2b-166">ASP.NET Core では、**Startup.cs** 内で構成を行うのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="9de2b-166">In ASP.NET Core, configuration is generally performed in **Startup.cs**.</span></span> <span data-ttu-id="9de2b-167">このパターンに合わせるために、データベース プロバイダーの構成を **Startup.cs** に移動します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-167">To conform to this pattern, we will move configuration of the database provider to **Startup.cs**.</span></span>

* <span data-ttu-id="9de2b-168">開く `Models\BloggingContext.cs`</span><span class="sxs-lookup"><span data-stu-id="9de2b-168">Open `Models\BloggingContext.cs`</span></span>
* <span data-ttu-id="9de2b-169">`OnConfiguring(...)` メソッドを削除します</span><span class="sxs-lookup"><span data-stu-id="9de2b-169">Delete the `OnConfiguring(...)` method</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
    optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
}
```

* <span data-ttu-id="9de2b-170">次のコンストラクターを追加すると、依存関係の挿入によって構成をコンテキストに渡すことができます。</span><span class="sxs-lookup"><span data-stu-id="9de2b-170">Add the following constructor, which will allow configuration to be passed into the context by dependency injection</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/BloggingContext.cs#Constructor)]

### <a name="register-and-configure-your-context-in-startupcs"></a><span data-ttu-id="9de2b-171">Startup.cs にコンテキストを登録して構成する</span><span class="sxs-lookup"><span data-stu-id="9de2b-171">Register and configure your context in Startup.cs</span></span>

<span data-ttu-id="9de2b-172">MVC コントローラーで利用するために、`BloggingContext` をサービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-172">In order for our MVC controllers to make use of `BloggingContext` we are going to register it as a service.</span></span>

* <span data-ttu-id="9de2b-173">**Startup.cs** を開きます。</span><span class="sxs-lookup"><span data-stu-id="9de2b-173">Open **Startup.cs**</span></span>
* <span data-ttu-id="9de2b-174">ファイルの先頭に次の `using` ステートメントを追加します</span><span class="sxs-lookup"><span data-stu-id="9de2b-174">Add the following `using` statements at the start of the file</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs#AddedUsings)]

<span data-ttu-id="9de2b-175">`AddDbContext(...)` メソッドを使用して、BloggingContext をサービスとして登録できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="9de2b-175">Now we can use the `AddDbContext(...)` method to register it as a service.</span></span>
* <span data-ttu-id="9de2b-176">`ConfigureServices(...)` メソッドを見つけます</span><span class="sxs-lookup"><span data-stu-id="9de2b-176">Locate the `ConfigureServices(...)` method</span></span>
* <span data-ttu-id="9de2b-177">次のコードを追加して、コンテキストをサービスとして登録します</span><span class="sxs-lookup"><span data-stu-id="9de2b-177">Add the following code to register the context as a service</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs?name=ConfigureServices&highlight=7-8)]

> [!TIP]  
> <span data-ttu-id="9de2b-178">実際のアプリケーションでは、接続文字列は構成ファイルに記述するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="9de2b-178">In a real application you would typically put the connection string in a configuration file.</span></span> <span data-ttu-id="9de2b-179">わかりやすくするために、接続文字列はコード内に定義しています。</span><span class="sxs-lookup"><span data-stu-id="9de2b-179">For the sake of simplicity, we are defining it in code.</span></span> <span data-ttu-id="9de2b-180">詳細については、「[Connection Strings (接続文字列)](../../miscellaneous/connection-strings.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="9de2b-180">For more information, see [Connection Strings](../../miscellaneous/connection-strings.md).</span></span>

## <a name="create-a-controller"></a><span data-ttu-id="9de2b-181">コントローラーを作成する</span><span class="sxs-lookup"><span data-stu-id="9de2b-181">Create a controller</span></span>

<span data-ttu-id="9de2b-182">次に、プロジェクトでスキャフォールディングを有効にします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-182">Next, we'll enable scaffolding in our project.</span></span>

* <span data-ttu-id="9de2b-183">**ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加] -> [コントローラー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-183">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add -> Controller...**</span></span>
* <span data-ttu-id="9de2b-184">**[完全な依存関係]** を選択して **[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-184">Select **Full Dependencies** and click **Add**</span></span>
* <span data-ttu-id="9de2b-185">開いた `ScaffoldingReadMe.txt` ファイル内の指示は無視してかまいません。</span><span class="sxs-lookup"><span data-stu-id="9de2b-185">You can ignore the instructions in the `ScaffoldingReadMe.txt` file that opens</span></span>

<span data-ttu-id="9de2b-186">スキャフォールディングが有効になったら、`Blog` エンティティのコントローラーをスキャフォールディングできます。</span><span class="sxs-lookup"><span data-stu-id="9de2b-186">Now that scaffolding is enabled, we can scaffold a controller for the `Blog` entity.</span></span>

* <span data-ttu-id="9de2b-187">**ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、**[追加] -> [コントローラー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-187">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add -> Controller...**</span></span>
* <span data-ttu-id="9de2b-188">**[Entity Framework を使用したビューがある MVC コントローラー]** を選択し、**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-188">Select **MVC Controller with views, using Entity Framework** and click **Ok**</span></span>
* <span data-ttu-id="9de2b-189">**[モデル クラス]** に **Blog** を、**[データ コンテキスト クラス]** に **[BloggingContext]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-189">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**</span></span>
* <span data-ttu-id="9de2b-190">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-190">Click **Add**</span></span>

## <a name="run-the-application"></a><span data-ttu-id="9de2b-191">アプリケーションの実行</span><span class="sxs-lookup"><span data-stu-id="9de2b-191">Run the application</span></span>

<span data-ttu-id="9de2b-192">アプリケーションを実行して動作を確認できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="9de2b-192">You can now run the application to see it in action.</span></span>

* <span data-ttu-id="9de2b-193">**[デバッグ] -> [デバッグなしで開始]**</span><span class="sxs-lookup"><span data-stu-id="9de2b-193">**Debug -> Start Without Debugging**</span></span>
* <span data-ttu-id="9de2b-194">アプリケーションが作成され、Web ブラウザー上で開かれます。</span><span class="sxs-lookup"><span data-stu-id="9de2b-194">The application will build and open in a web browser</span></span>
* <span data-ttu-id="9de2b-195">`/Blogs` に移動します。</span><span class="sxs-lookup"><span data-stu-id="9de2b-195">Navigate to `/Blogs`</span></span>
* <span data-ttu-id="9de2b-196">**[新規作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-196">Click **Create New**</span></span>
* <span data-ttu-id="9de2b-197">新しいブログの **URL** を入力して、**[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="9de2b-197">Enter a **Url** for the new blog and click **Create**</span></span>

![image](_static/create.png)

![image](_static/index-existing-db.png)
