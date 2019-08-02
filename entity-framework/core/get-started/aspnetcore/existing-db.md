---
title: ASP.NET Core - 既存のデータベース - EF Core の概要
author: rowanmiller
description: 既存のデータベースを使用した ASP.NET Core での EF Core の概要
ms.date: 08/02/2018
ms.assetid: 2bc68bea-ff77-4860-bf0b-cf00db6712a0
uid: core/get-started/aspnetcore/existing-db
ms.openlocfilehash: 6b0ed0a9222644bee31d23234aa27b2084137f4a
ms.sourcegitcommit: 755a15a789631cc4ea581e2262a2dcc49c219eef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68497509"
---
# <a name="get-started-with-ef-core-on-aspnet-core-with-an-existing-database"></a><span data-ttu-id="d4f00-103">既存のデータベースを使用した ASP.NET Core での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="d4f00-103">Get started with EF Core on ASP.NET Core with an existing database</span></span>

<span data-ttu-id="d4f00-104">このチュートリアルでは、Entity Framework Core を使用して、基本的なデータ アクセスを実行する ASP.NET Core MVC アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-104">In this tutorial, you build an ASP.NET Core MVC application that performs basic data access using Entity Framework Core.</span></span> <span data-ttu-id="d4f00-105">既存のデータベースのリバース エンジニアリングによって Entity Framework モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-105">You reverse engineer an existing database to create an Entity Framework model.</span></span>

<span data-ttu-id="d4f00-106">[この記事のサンプルは GitHub で確認してください](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb)。</span><span class="sxs-lookup"><span data-stu-id="d4f00-106">[View this article's sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d4f00-107">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="d4f00-107">Prerequisites</span></span>

<span data-ttu-id="d4f00-108">以下のソフトウェアをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d4f00-108">Install the following software:</span></span>

* <span data-ttu-id="d4f00-109">[Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) および以下のワークロード。</span><span class="sxs-lookup"><span data-stu-id="d4f00-109">[Visual Studio 2017 15.7](https://www.visualstudio.com/downloads/) with these workloads:</span></span>
  * <span data-ttu-id="d4f00-110">**ASP.NET と Web 開発** ( **[Web & Cloud]\(Web とクラウド\)** の下)</span><span class="sxs-lookup"><span data-stu-id="d4f00-110">**ASP.NET and web development** (under **Web & Cloud**)</span></span>
  * <span data-ttu-id="d4f00-111">**.NET Core クロスプラットフォームの開発** ( **[他のツールセット]** の下)</span><span class="sxs-lookup"><span data-stu-id="d4f00-111">**.NET Core cross-platform development** (under **Other Toolsets**)</span></span>
* <span data-ttu-id="d4f00-112">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core)。</span><span class="sxs-lookup"><span data-stu-id="d4f00-112">[.NET Core 2.1 SDK](https://www.microsoft.com/net/download/core).</span></span>

## <a name="create-blogging-database"></a><span data-ttu-id="d4f00-113">Blogging データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="d4f00-113">Create Blogging database</span></span>

<span data-ttu-id="d4f00-114">このチュートリアルでは、お使いの LocalDb インスタンス上の **Blogging** データベースを、既存のデータベースとして使用します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-114">This tutorial uses a **Blogging** database on your LocalDb instance as the existing database.</span></span> <span data-ttu-id="d4f00-115">**Blogging** データベースを、別のチュートリアルの一部として既に作成している場合は、次の手順を省略してください。</span><span class="sxs-lookup"><span data-stu-id="d4f00-115">If you have already created the **Blogging** database as part of another tutorial, skip these steps.</span></span>

* <span data-ttu-id="d4f00-116">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="d4f00-116">Open Visual Studio</span></span>
* <span data-ttu-id="d4f00-117">**[ツール] -> [データベースへの接続]**</span><span class="sxs-lookup"><span data-stu-id="d4f00-117">**Tools -> Connect to Database...**</span></span>
* <span data-ttu-id="d4f00-118">**[Microsoft SQL Server]** を選択して **[続行]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d4f00-118">Select **Microsoft SQL Server** and click **Continue**</span></span>
* <span data-ttu-id="d4f00-119">**[サーバー名]** に「 **(localdb) \mssqllocaldb**」を入力します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-119">Enter **(localdb)\mssqllocaldb** as the **Server Name**</span></span>
* <span data-ttu-id="d4f00-120">**[データベース名]** に「**master**」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d4f00-120">Enter **master** as the **Database Name** and click **OK**</span></span>
* <span data-ttu-id="d4f00-121">**[サーバー エクスプローラー]** 内の **[データ接続]** の下に、master データベースが表示されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d4f00-121">The master database is now displayed under **Data Connections** in **Server Explorer**</span></span>
* <span data-ttu-id="d4f00-122">**[サーバー エクスプローラー]** でこのデータベースを右クリックし、 **[新しいクエリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-122">Right-click on the database in **Server Explorer** and select **New Query**</span></span>
* <span data-ttu-id="d4f00-123">以下に表示されているスクリプトを、クエリ エディターにコピーします。</span><span class="sxs-lookup"><span data-stu-id="d4f00-123">Copy the script listed below into the query editor</span></span>
* <span data-ttu-id="d4f00-124">クエリ エディターを右クリックし、 **[実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-124">Right-click on the query editor and select **Execute**</span></span>

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a><span data-ttu-id="d4f00-125">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="d4f00-125">Create a new project</span></span>

* <span data-ttu-id="d4f00-126">Visual Studio 2017 を開きます</span><span class="sxs-lookup"><span data-stu-id="d4f00-126">Open Visual Studio 2017</span></span>
* <span data-ttu-id="d4f00-127">**[ファイル] > [新規] > [プロジェクト]**</span><span class="sxs-lookup"><span data-stu-id="d4f00-127">**File > New > Project...**</span></span>
* <span data-ttu-id="d4f00-128">左側のメニューから **[インストール済み] > [Visual C#] > [Web]** の順に選択します</span><span class="sxs-lookup"><span data-stu-id="d4f00-128">From the left menu select **Installed > Visual C# > Web**</span></span>
* <span data-ttu-id="d4f00-129">**[ASP.NET Core Web アプリケーション]** プロジェクト テンプレートを選択します</span><span class="sxs-lookup"><span data-stu-id="d4f00-129">Select the **ASP.NET Core Web Application** project template</span></span>
* <span data-ttu-id="d4f00-130">名前には「**EFGetStarted.AspNetCore.ExistingDb**」と入力し (コードで後で使用される名前空間と正確に一致する必要があります)、 **[OK]** をクリックします</span><span class="sxs-lookup"><span data-stu-id="d4f00-130">Enter **EFGetStarted.AspNetCore.ExistingDb** as the name (it has to match exactly the namespace later used in the code) and click **OK**</span></span> 
* <span data-ttu-id="d4f00-131">**[新しい ASP.NET Core Web アプリケーション]** ダイアログが表示されるのを待ちます</span><span class="sxs-lookup"><span data-stu-id="d4f00-131">Wait for the **New ASP.NET Core Web Application** dialog to appear</span></span>
* <span data-ttu-id="d4f00-132">ターゲット フレームワークのドロップダウンが **[.NET Core]** に設定されており、バージョンのドロップダウンが **[ASP.NET Core 2.1]** に設定されていることを確認します</span><span class="sxs-lookup"><span data-stu-id="d4f00-132">Make sure that the target framework dropdown is set to **.NET Core**, and the version dropdown is set to **ASP.NET Core 2.1**</span></span>
* <span data-ttu-id="d4f00-133">**[Web アプリケーション (モデル ビュー コントローラー)]** テンプレートを選択します</span><span class="sxs-lookup"><span data-stu-id="d4f00-133">Select the **Web Application (Model-View-Controller)** template</span></span>
* <span data-ttu-id="d4f00-134">**[認証]** に **[認証なし]** が設定されていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-134">Ensure that **Authentication** is set to **No Authentication**</span></span>
* <span data-ttu-id="d4f00-135">**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d4f00-135">Click **OK**</span></span>

## <a name="install-entity-framework-core"></a><span data-ttu-id="d4f00-136">Entity Framework Core をインストールする</span><span class="sxs-lookup"><span data-stu-id="d4f00-136">Install Entity Framework Core</span></span>

<span data-ttu-id="d4f00-137">EF Core をインストールするには、対象となる EF Core データベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="d4f00-137">To install EF Core, you install the package for the EF Core database provider(s) you want to target.</span></span> <span data-ttu-id="d4f00-138">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4f00-138">For a list of available providers see [Database Providers](../../providers/index.md).</span></span> 

<span data-ttu-id="d4f00-139">このチュートリアルでは、SQL Server を使用するため、プロバイダー パッケージをインストールする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="d4f00-139">For this tutorial, you don't have to install a provider package because the tutorial uses SQL Server.</span></span> <span data-ttu-id="d4f00-140">この SQL Server のプロバイダー パッケージは、[Microsoft.AspnetCore.App メタパッケージ](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1)に含まれます。</span><span class="sxs-lookup"><span data-stu-id="d4f00-140">The SQL Server provider package is included in the [Microsoft.AspnetCore.App metapackage](https://docs.microsoft.com/aspnet/core/fundamentals/metapackage-app?view=aspnetcore-2.1).</span></span>

## <a name="reverse-engineer-your-model"></a><span data-ttu-id="d4f00-141">モデルのリバース エンジニアリングを行う</span><span class="sxs-lookup"><span data-stu-id="d4f00-141">Reverse engineer your model</span></span>

<span data-ttu-id="d4f00-142">ここで、お使いの既存のデータベースに基づいて、EF モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-142">Now it's time to create the EF model based on your existing database.</span></span>

* <span data-ttu-id="d4f00-143">**[ツール] –> [NuGet パッケージ マネージャー] –> [パッケージ マネージャー コンソール]**</span><span class="sxs-lookup"><span data-stu-id="d4f00-143">**Tools –> NuGet Package Manager –> Package Manager Console**</span></span>
* <span data-ttu-id="d4f00-144">次のコマンドを実行して、既存のデータベースからモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-144">Run the following command to create a model from the existing database:</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

<span data-ttu-id="d4f00-145">`The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet` という内容のエラーが表示された場合は、Visual Studio を閉じて再度開きます。</span><span class="sxs-lookup"><span data-stu-id="d4f00-145">If you receive an error stating `The term 'Scaffold-DbContext' is not recognized as the name of a cmdlet`, then close and reopen Visual Studio.</span></span>

> [!TIP]  
> <span data-ttu-id="d4f00-146">上記のコマンドに引数 `-Tables` を追加すると、エンティティを生成するテーブルを指定できます。</span><span class="sxs-lookup"><span data-stu-id="d4f00-146">You can specify which tables you want to generate entities for by adding the `-Tables` argument to the command above.</span></span> <span data-ttu-id="d4f00-147">たとえば、`-Tables Blog,Post` のようにします。</span><span class="sxs-lookup"><span data-stu-id="d4f00-147">For example, `-Tables Blog,Post`.</span></span>

<span data-ttu-id="d4f00-148">リバース エンジニアリングのプロセスにより、既存のデータベースのスキーマに基づいてエンティティ クラス (`Blog.cs` & `Post.cs`) と派生コンテキスト (`BloggingContext.cs`) が作成されます。</span><span class="sxs-lookup"><span data-stu-id="d4f00-148">The reverse engineer process created entity classes (`Blog.cs` & `Post.cs`) and a derived context (`BloggingContext.cs`) based on the schema of the existing database.</span></span>

 <span data-ttu-id="d4f00-149">エンティティ クラスはクエリを実行し保存するデータを表す、単純な C# オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="d4f00-149">The entity classes are simple C# objects that represent the data you will be querying and saving.</span></span> <span data-ttu-id="d4f00-150">こちらが、`Blog` と `Post` のエンティティ クラスです。</span><span class="sxs-lookup"><span data-stu-id="d4f00-150">Here are the `Blog` and `Post` entity classes:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Blog.cs)]

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Models/Post.cs)]

> [!TIP]  
> <span data-ttu-id="d4f00-151">遅延読み込みを有効にするため、ナビゲーション プロパティ `virtual` を作成することができます (Blog.Post および Post.Blog)。</span><span class="sxs-lookup"><span data-stu-id="d4f00-151">To enable lazy loading, you can make navigation properties `virtual` (Blog.Post and Post.Blog).</span></span>

 <span data-ttu-id="d4f00-152">コンテキストはデータベースのセッションを表しており、これによってエンティティ クラスのインスタンスにクエリを実行し保存できます。</span><span class="sxs-lookup"><span data-stu-id="d4f00-152">The context represents a session with the database and allows you to query and save instances of the entity classes.</span></span>

<!-- Static code listing, rather than a linked file, because the tutorial modifies the context file heavily -->
 ``` csharp
public partial class BloggingContext : DbContext
{
    public BloggingContext()
    {
    }

    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    {
    }

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

## <a name="register-your-context-with-dependency-injection"></a><span data-ttu-id="d4f00-153">依存関係の挿入にコンテキストを登録します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-153">Register your context with dependency injection</span></span>

<span data-ttu-id="d4f00-154">依存関係の挿入の概念は、ASP.NET Core の中心となるものです。</span><span class="sxs-lookup"><span data-stu-id="d4f00-154">The concept of dependency injection is central to ASP.NET Core.</span></span> <span data-ttu-id="d4f00-155">サービス (`BloggingContext` など) は、アプリケーションの起動時に依存関係の挿入に登録されます。</span><span class="sxs-lookup"><span data-stu-id="d4f00-155">Services (such as `BloggingContext`) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="d4f00-156">これらのサービス (MVC コント ローラーなど) を必要とするコンポーネントには、コンス トラクターのパラメーターまたはプロパティを介してこれらのサービスが指定されます。</span><span class="sxs-lookup"><span data-stu-id="d4f00-156">Components that require these services (such as your MVC controllers) are then provided these services via constructor parameters or properties.</span></span> <span data-ttu-id="d4f00-157">依存関係の挿入の詳細については、ASP.NET サイトの[依存関係の挿入](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html)に関する記事をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4f00-157">For more information on dependency injection see the [Dependency Injection](http://docs.asp.net/en/latest/fundamentals/dependency-injection.html) article on the ASP.NET site.</span></span>

### <a name="register-and-configure-your-context-in-startupcs"></a><span data-ttu-id="d4f00-158">Startup.cs にコンテキストを登録して構成する</span><span class="sxs-lookup"><span data-stu-id="d4f00-158">Register and configure your context in Startup.cs</span></span>

<span data-ttu-id="d4f00-159">`BloggingContext` を MVC コント ローラーで使用できるようにするには、サービスとして登録します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-159">To make `BloggingContext` available to MVC controllers, register it as a service.</span></span>

* <span data-ttu-id="d4f00-160">**Startup.cs** を開きます。</span><span class="sxs-lookup"><span data-stu-id="d4f00-160">Open **Startup.cs**</span></span>
* <span data-ttu-id="d4f00-161">ファイルの先頭に次の `using` ステートメントを追加します</span><span class="sxs-lookup"><span data-stu-id="d4f00-161">Add the following `using` statements at the start of the file</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs#AddedUsings)]

<span data-ttu-id="d4f00-162">`AddDbContext(...)` メソッドを使用して、サービスとして登録できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d4f00-162">Now you can use the `AddDbContext(...)` method to register it as a service.</span></span>
* <span data-ttu-id="d4f00-163">`ConfigureServices(...)` メソッドを見つけます</span><span class="sxs-lookup"><span data-stu-id="d4f00-163">Locate the `ConfigureServices(...)` method</span></span>
* <span data-ttu-id="d4f00-164">強調表示されている次のコードを追加して、コンテキストをサービスとして登録します</span><span class="sxs-lookup"><span data-stu-id="d4f00-164">Add the following highlighted code to register the context as a service</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/AspNetCore/EFGetStarted.AspNetCore.ExistingDb/Startup.cs?name=ConfigureServices&highlight=14-15)]

> [!TIP]  
> <span data-ttu-id="d4f00-165">実際のアプリケーションでは、接続文字列は構成ファイルまたは環境変数に記述するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="d4f00-165">In a real application you would typically put the connection string in a configuration file or environment variable.</span></span> <span data-ttu-id="d4f00-166">わかりやすくするために、このチュートリアルではコード内に定義します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-166">For the sake of simplicity, this tutorial has you define it in code.</span></span> <span data-ttu-id="d4f00-167">詳細については、「[Connection Strings (接続文字列)](../../miscellaneous/connection-strings.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="d4f00-167">For more information, see [Connection Strings](../../miscellaneous/connection-strings.md).</span></span>

## <a name="create-a-controller-and-views"></a><span data-ttu-id="d4f00-168">コントローラーとビューの作成</span><span class="sxs-lookup"><span data-stu-id="d4f00-168">Create a controller and views</span></span>

* <span data-ttu-id="d4f00-169">**ソリューション エクスプローラー**の **Controllers** フォルダーを右クリックし、 **[追加] -> [コントローラー]** の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-169">Right-click on the **Controllers** folder in **Solution Explorer** and select **Add -> Controller...**</span></span>
* <span data-ttu-id="d4f00-170">**[Entity Framework を使用したビューがある MVC コントローラー]** を選択し、 **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d4f00-170">Select **MVC Controller with views, using Entity Framework** and click **Ok**</span></span>
* <span data-ttu-id="d4f00-171">**[モデル クラス]** に **Blog** を、 **[データ コンテキスト クラス]** に **[BloggingContext]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-171">Set **Model class** to **Blog** and **Data context class** to **BloggingContext**</span></span>
* <span data-ttu-id="d4f00-172">**[追加]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d4f00-172">Click **Add**</span></span>

## <a name="run-the-application"></a><span data-ttu-id="d4f00-173">アプリケーションの実行</span><span class="sxs-lookup"><span data-stu-id="d4f00-173">Run the application</span></span>

<span data-ttu-id="d4f00-174">アプリケーションを実行して動作を確認できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="d4f00-174">You can now run the application to see it in action.</span></span>

* <span data-ttu-id="d4f00-175">**[デバッグ] -> [デバッグなしで開始]**</span><span class="sxs-lookup"><span data-stu-id="d4f00-175">**Debug -> Start Without Debugging**</span></span>
* <span data-ttu-id="d4f00-176">アプリケーションが作成され、Web ブラウザー内で開きます</span><span class="sxs-lookup"><span data-stu-id="d4f00-176">The application builds and opens in a web browser</span></span>
* <span data-ttu-id="d4f00-177">`/Blogs` に移動します。</span><span class="sxs-lookup"><span data-stu-id="d4f00-177">Navigate to `/Blogs`</span></span>
* <span data-ttu-id="d4f00-178">**[新規作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d4f00-178">Click **Create New**</span></span>
* <span data-ttu-id="d4f00-179">新しいブログの **URL** を入力して、 **[作成]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="d4f00-179">Enter a **Url** for the new blog and click **Create**</span></span>

  ![[作成] ページ](_static/create.png)

  ![インデックス ページ](_static/index-existing-db.png)

## <a name="next-steps"></a><span data-ttu-id="d4f00-182">次の手順</span><span class="sxs-lookup"><span data-stu-id="d4f00-182">Next steps</span></span>

<span data-ttu-id="d4f00-183">コンテキスト クラスとエンティティ クラスをスキャフォールディングする方法の詳細については、次の記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d4f00-183">For more information about how to scaffold a context and entity classes, see the following articles:</span></span>
* [<span data-ttu-id="d4f00-184">リバース エンジニアリング</span><span class="sxs-lookup"><span data-stu-id="d4f00-184">Reverse Engineering</span></span>](xref:core/managing-schemas/scaffolding)
* [<span data-ttu-id="d4f00-185">Entity Framework Core ツールのリファレンス - .NET CLI</span><span class="sxs-lookup"><span data-stu-id="d4f00-185">Entity Framework Core tools reference - .NET CLI</span></span>](xref:core/miscellaneous/cli/dotnet#dotnet-ef-dbcontext-scaffold)
* [<span data-ttu-id="d4f00-186">Entity Framework Core ツールのリファレンス - パッケージ マネージャー コンソール</span><span class="sxs-lookup"><span data-stu-id="d4f00-186">Entity Framework Core tools reference - Package Manager Console</span></span>](xref:core/miscellaneous/cli/powershell#scaffold-dbcontext)
