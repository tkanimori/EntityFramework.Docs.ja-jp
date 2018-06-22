---
title: .NET Framework - 既存のデータベース - EF Core の概要
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: a29a3d97-b2d8-4d33-9475-40ac67b3b2c6
ms.technology: entity-framework-core
uid: core/get-started/full-dotnet/existing-db
ms.openlocfilehash: 3cd69109e3cf8dbc103f9eea6e2553df17f29a98
ms.sourcegitcommit: 507a40ed050fee957bcf8cf05f6e0ec8a3b1a363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2018
ms.locfileid: "31812626"
---
# <a name="getting-started-with-ef-core-on-net-framework-with-an-existing-database"></a><span data-ttu-id="dc705-102">既存のデータベースを使用した .NET Framework での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="dc705-102">Getting started with EF Core on .NET Framework with an Existing Database</span></span>

<span data-ttu-id="dc705-103">このチュートリアルでは、Entity Framework を使用して Microsoft SQL Server データベースに対して基本データ アクセスを実行するコンソール アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="dc705-103">In this walkthrough, you will build a console application that performs basic data access against a Microsoft SQL Server database using Entity Framework.</span></span> <span data-ttu-id="dc705-104">既存のデータベースに基づく Entity Framework モデルを作成するために、リバース エンジニアリングを使用します。</span><span class="sxs-lookup"><span data-stu-id="dc705-104">You will use reverse engineering to create an Entity Framework model based on an existing database.</span></span>

> [!TIP]  
> <span data-ttu-id="dc705-105">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="dc705-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dc705-106">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="dc705-106">Prerequisites</span></span>

<span data-ttu-id="dc705-107">このチュートリアルを完了するには、次の条件を満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="dc705-107">The following prerequisites are needed to complete this walkthrough:</span></span>

* [<span data-ttu-id="dc705-108">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="dc705-108">Visual Studio 2017</span></span>](https://www.visualstudio.com/downloads/)

* [<span data-ttu-id="dc705-109">最新バージョンの NuGet パッケージ マネージャー</span><span class="sxs-lookup"><span data-stu-id="dc705-109">Latest version of NuGet Package Manager</span></span>](https://dist.nuget.org/index.html)

* [<span data-ttu-id="dc705-110">最新バージョンの Windows PowerShell</span><span class="sxs-lookup"><span data-stu-id="dc705-110">Latest version of Windows PowerShell</span></span>](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell)

* [<span data-ttu-id="dc705-111">Blogging データベース</span><span class="sxs-lookup"><span data-stu-id="dc705-111">Blogging database</span></span>](#blogging-database)

### <a name="blogging-database"></a><span data-ttu-id="dc705-112">Blogging データベース</span><span class="sxs-lookup"><span data-stu-id="dc705-112">Blogging database</span></span>

<span data-ttu-id="dc705-113">このチュートリアルでは、お使いの LocalDb インスタンス上の **Blogging** データベースを、既存のデータベースとして使用します。</span><span class="sxs-lookup"><span data-stu-id="dc705-113">This tutorial uses a **Blogging** database on your LocalDb instance as the existing database.</span></span>

> [!TIP]  
> <span data-ttu-id="dc705-114">**Blogging** データベースを、別のチュートリアルの一部として既に作成している場合は、次の手順を省略できます。</span><span class="sxs-lookup"><span data-stu-id="dc705-114">If you have already created the **Blogging** database as part of another tutorial, you can skip these steps.</span></span>

* <span data-ttu-id="dc705-115">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="dc705-115">Open Visual Studio</span></span>

* <span data-ttu-id="dc705-116">[ツール] > [データベースへの接続...]</span><span class="sxs-lookup"><span data-stu-id="dc705-116">Tools > Connect to Database...</span></span>

* <span data-ttu-id="dc705-117">**[Microsoft SQL Server]** を選択して **[続行]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dc705-117">Select **Microsoft SQL Server** and click **Continue**</span></span>

* <span data-ttu-id="dc705-118">**[サーバー名]** に「**(localdb) \mssqllocaldb**」を入力します。</span><span class="sxs-lookup"><span data-stu-id="dc705-118">Enter **(localdb)\mssqllocaldb** as the **Server Name**</span></span>

* <span data-ttu-id="dc705-119">**[データベース名]** に「**master**」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dc705-119">Enter **master** as the **Database Name** and click **OK**</span></span>

* <span data-ttu-id="dc705-120">**[サーバー エクスプローラー]** 内の **[データ接続]** の下に、master データベースが表示されるようになりました。</span><span class="sxs-lookup"><span data-stu-id="dc705-120">The master database is now displayed under **Data Connections** in **Server Explorer**</span></span>

* <span data-ttu-id="dc705-121">**[サーバー エクスプローラー]** でこのデータベースを右クリックし、**[新しいクエリ]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dc705-121">Right-click on the database in **Server Explorer** and select **New Query**</span></span>

* <span data-ttu-id="dc705-122">以下に表示されているスクリプトを、クエリ エディターにコピーします</span><span class="sxs-lookup"><span data-stu-id="dc705-122">Copy the script, listed below, into the query editor</span></span>

* <span data-ttu-id="dc705-123">クエリ エディターを右クリックし、**[実行]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dc705-123">Right-click on the query editor and select **Execute**</span></span>

[!code-sql[Main](../_shared/create-blogging-database-script.sql)]

## <a name="create-a-new-project"></a><span data-ttu-id="dc705-124">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="dc705-124">Create a new project</span></span>

* <span data-ttu-id="dc705-125">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="dc705-125">Open Visual Studio</span></span>

* <span data-ttu-id="dc705-126">[ファイル] > [新規] > [プロジェクト...]</span><span class="sxs-lookup"><span data-stu-id="dc705-126">File > New > Project...</span></span>

* <span data-ttu-id="dc705-127">左側のメニューから [テンプレート] > [Visual C#] > [Windows] の順に選択します</span><span class="sxs-lookup"><span data-stu-id="dc705-127">From the left menu select Templates > Visual C# > Windows</span></span>

* <span data-ttu-id="dc705-128">**[コンソール アプリケーション]** プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="dc705-128">Select the **Console Application** project template</span></span>

* <span data-ttu-id="dc705-129">**.NET Framework 4.5.1** 以降を対象としていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="dc705-129">Ensure you are targeting **.NET Framework 4.5.1** or later</span></span>

* <span data-ttu-id="dc705-130">プロジェクト名を設定し、**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="dc705-130">Give the project a name and click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="dc705-131">Entity Framework をインストールする</span><span class="sxs-lookup"><span data-stu-id="dc705-131">Install Entity Framework</span></span>

<span data-ttu-id="dc705-132">EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="dc705-132">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="dc705-133">このチュートリアルでは SQL Server を使用します。</span><span class="sxs-lookup"><span data-stu-id="dc705-133">This walkthrough uses SQL Server.</span></span> <span data-ttu-id="dc705-134">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="dc705-134">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="dc705-135">[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]</span><span class="sxs-lookup"><span data-stu-id="dc705-135">Tools > NuGet Package Manager > Package Manager Console</span></span>

* <span data-ttu-id="dc705-136">`Install-Package Microsoft.EntityFrameworkCore.SqlServer` を実行します。</span><span class="sxs-lookup"><span data-stu-id="dc705-136">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="dc705-137">既存のデータベースのリバース エンジニアリングを有効にするには、他にも 2, 3 個のパッケージをインストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="dc705-137">To enable reverse engineering from an existing database we need to install a couple of other packages too.</span></span>

* <span data-ttu-id="dc705-138">`Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。</span><span class="sxs-lookup"><span data-stu-id="dc705-138">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

## <a name="reverse-engineer-your-model"></a><span data-ttu-id="dc705-139">モデルのリバース エンジニアリングを行う</span><span class="sxs-lookup"><span data-stu-id="dc705-139">Reverse engineer your model</span></span>

<span data-ttu-id="dc705-140">ここで、お使いの既存のデータベースに基づいて、EF モデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="dc705-140">Now it's time to create the EF model based on your existing database.</span></span>

* <span data-ttu-id="dc705-141">[ツール] -> [NuGet パッケージ マネージャー] -> [パッケージ マネージャー コンソール]</span><span class="sxs-lookup"><span data-stu-id="dc705-141">Tools –> NuGet Package Manager –> Package Manager Console</span></span>

* <span data-ttu-id="dc705-142">既存のデータベースからモデルを作成するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="dc705-142">Run the following command to create a model from the existing database</span></span>

``` powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="dc705-143">リバース エンジニアリングのプロセスでは、エンティティ クラスと、既存のデータベースのスキーマに基づく派生コンテキストを作成しました。</span><span class="sxs-lookup"><span data-stu-id="dc705-143">The reverse engineer process created entity classes and a derived context based on the schema of the existing database.</span></span> <span data-ttu-id="dc705-144">エンティティ クラスはクエリを実行し保存するデータを表す、単純な C# オブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="dc705-144">The entity classes are simple C# objects that represent the data you will be querying and saving.</span></span>

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Blog.cs)] -->
``` csharp
using System;
using System.Collections.Generic;

namespace EFGetStarted.ConsoleApp.ExistingDb
{
    public partial class Blog
    {
        public Blog()
        {
            Post = new HashSet<Post>();
        }

        public int BlogId { get; set; }
        public string Url { get; set; }

        public virtual ICollection<Post> Post { get; set; }
    }
}
```

<span data-ttu-id="dc705-145">コンテキストはデータベースのセッションを表しており、これによってエンティティ クラスのインスタンスにクエリを実行し保存できます。</span><span class="sxs-lookup"><span data-stu-id="dc705-145">The context represents a session with the database and allows you to query and save instances of the entity classes.</span></span>

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/BloggingContext.cs)] -->
``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata;

namespace EFGetStarted.ConsoleApp.ExistingDb
{
    public partial class BloggingContext : DbContext
    {
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            #warning To protect potentially sensitive information in your connection string, you should move it out of source code. See http://go.microsoft.com/fwlink/?LinkId=723263 for guidance on storing connection strings.
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;");
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

        public virtual DbSet<Blog> Blog { get; set; }
        public virtual DbSet<Post> Post { get; set; }
    }
}
```

## <a name="use-your-model"></a><span data-ttu-id="dc705-146">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="dc705-146">Use your model</span></span>

<span data-ttu-id="dc705-147">モデルを使用してデータ アクセスを実行できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="dc705-147">You can now use your model to perform data access.</span></span>

* <span data-ttu-id="dc705-148">*Program.cs* を開きます。</span><span class="sxs-lookup"><span data-stu-id="dc705-148">Open *Program.cs*</span></span>

* <span data-ttu-id="dc705-149">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="dc705-149">Replace the contents of the file with the following code</span></span>

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.ExistingDb/Program.cs)] -->
``` csharp
using System;

namespace EFGetStarted.ConsoleApp.ExistingDb
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var db = new BloggingContext())
            {
                db.Blog.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                var count = db.SaveChanges();
                Console.WriteLine("{0} records saved to database", count);

                Console.WriteLine();
                Console.WriteLine("All blogs in database:");
                foreach (var blog in db.Blog)
                {
                    Console.WriteLine(" - {0}", blog.Url);
                }
            }
        }
    }
}
```

* <span data-ttu-id="dc705-150">[デバッグ] > [デバッグなしで開始]</span><span class="sxs-lookup"><span data-stu-id="dc705-150">Debug > Start Without Debugging</span></span>

<span data-ttu-id="dc705-151">1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに出力されます。</span><span class="sxs-lookup"><span data-stu-id="dc705-151">You will see that one blog is saved to the database and then the details of all blogs are printed to the console.</span></span>

![イメージ](_static/output-existing-db.png)
