---
title: ".NET Framework - 新しいデータベース - EF Core の概要"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 52b69727-ded9-4a7b-b8d5-73f3acfbbad3
ms.technology: entity-framework-core
uid: core/get-started/full-dotnet/new-db
ms.openlocfilehash: bd7054c6834ae11bfdc352d63654e4304771e432
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="getting-started-with-ef-core-on-net-framework-with-a-new-database"></a><span data-ttu-id="2fc20-102">新しいデータベースを使用した .NET Framework での EF Core の概要</span><span class="sxs-lookup"><span data-stu-id="2fc20-102">Getting started with EF Core on .NET Framework with a New Database</span></span>

<span data-ttu-id="2fc20-103">このチュートリアルでは、Entity Framework を使用して Microsoft SQL Server データベースに対して基本データ アクセスを実行するコンソール アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-103">In this walkthrough, you will build a console application that performs basic data access against a Microsoft SQL Server database using Entity Framework.</span></span> <span data-ttu-id="2fc20-104">モデルからの移行によってデータベースを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-104">You will use migrations to create the database from your model.</span></span>

> [!TIP]  
> <span data-ttu-id="2fc20-105">この記事の[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.NewDb)は GitHub で確認できます。</span><span class="sxs-lookup"><span data-stu-id="2fc20-105">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/FullNet/ConsoleApp.NewDb) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2fc20-106">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="2fc20-106">Prerequisites</span></span>

<span data-ttu-id="2fc20-107">このチュートリアルを完了するには、次の条件を満たす必要があります。</span><span class="sxs-lookup"><span data-stu-id="2fc20-107">The following prerequisites are needed to complete this walkthrough:</span></span>

* [<span data-ttu-id="2fc20-108">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="2fc20-108">Visual Studio 2017</span></span>](https://www.visualstudio.com/downloads/)

* [<span data-ttu-id="2fc20-109">最新バージョンの NuGet パッケージ マネージャー</span><span class="sxs-lookup"><span data-stu-id="2fc20-109">Latest version of NuGet Package Manager</span></span>](https://dist.nuget.org/index.html)

* [<span data-ttu-id="2fc20-110">最新バージョンの Windows PowerShell</span><span class="sxs-lookup"><span data-stu-id="2fc20-110">Latest version of Windows PowerShell</span></span>](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell)

## <a name="create-a-new-project"></a><span data-ttu-id="2fc20-111">新しいプロジェクトを作成する</span><span class="sxs-lookup"><span data-stu-id="2fc20-111">Create a new project</span></span>

* <span data-ttu-id="2fc20-112">Visual Studio を開く</span><span class="sxs-lookup"><span data-stu-id="2fc20-112">Open Visual Studio</span></span>

* <span data-ttu-id="2fc20-113">[ファイル] > [新規] > [プロジェクト...]</span><span class="sxs-lookup"><span data-stu-id="2fc20-113">File > New > Project...</span></span>

* <span data-ttu-id="2fc20-114">左側のメニューから、[テンプレート] > [Visual C#] > [Windows クラシック デスクトップ] の順に選択します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-114">From the left menu select Templates > Visual C# > Windows Classic Desktop</span></span>

* <span data-ttu-id="2fc20-115">**[コンソール アプリ (.NET Framework)**] プロジェクト テンプレートを選択します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-115">Select the **Console App (.NET Framework)** project template</span></span>

* <span data-ttu-id="2fc20-116">**.NET Framework 4.5.1** 以降を対象としていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-116">Ensure you are targeting **.NET Framework 4.5.1** or later</span></span>

* <span data-ttu-id="2fc20-117">プロジェクト名を設定し、**[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2fc20-117">Give the project a name and click **OK**</span></span>

## <a name="install-entity-framework"></a><span data-ttu-id="2fc20-118">Entity Framework をインストールする</span><span class="sxs-lookup"><span data-stu-id="2fc20-118">Install Entity Framework</span></span>

<span data-ttu-id="2fc20-119">EF Core を使用するには、対象となるデータベース プロバイダーのパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="2fc20-119">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="2fc20-120">このチュートリアルでは SQL Server を使用します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-120">This walkthrough uses SQL Server.</span></span> <span data-ttu-id="2fc20-121">使用可能なプロバイダーの一覧については、「[Database Providers (データベース プロバイダー)](../../providers/index.md)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="2fc20-121">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="2fc20-122">[ツール] > [NuGet パッケージ マネージャー] > [パッケージ マネージャー コンソール]</span><span class="sxs-lookup"><span data-stu-id="2fc20-122">Tools > NuGet Package Manager > Package Manager Console</span></span>

* <span data-ttu-id="2fc20-123">`Install-Package Microsoft.EntityFrameworkCore.SqlServer` を実行します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-123">Run `Install-Package Microsoft.EntityFrameworkCore.SqlServer`</span></span>

<span data-ttu-id="2fc20-124">このチュートリアルの後半では、データベースのメンテナンスで複数の Entity Framework ツールも使用します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-124">Later in this walkthrough we will also be using some Entity Framework Tools to maintain the database.</span></span> <span data-ttu-id="2fc20-125">そこで、そのツールのパッケージもインストールします。</span><span class="sxs-lookup"><span data-stu-id="2fc20-125">So we will install the tools package as well.</span></span>

* <span data-ttu-id="2fc20-126">`Install-Package Microsoft.EntityFrameworkCore.Tools` を実行します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-126">Run `Install-Package Microsoft.EntityFrameworkCore.Tools`</span></span>

## <a name="create-your-model"></a><span data-ttu-id="2fc20-127">モデルを作成する</span><span class="sxs-lookup"><span data-stu-id="2fc20-127">Create your model</span></span>

<span data-ttu-id="2fc20-128">ここで、モデルを編成するコンテキストとエンティティ クラスを定義します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-128">Now it's time to define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="2fc20-129">[プロジェクト] > [クラスの追加...]</span><span class="sxs-lookup"><span data-stu-id="2fc20-129">Project > Add Class...</span></span>

* <span data-ttu-id="2fc20-130">名前に「*Model.cs*」を入力して **[OK]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="2fc20-130">Enter *Model.cs* as the name and click **OK**</span></span>

* <span data-ttu-id="2fc20-131">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2fc20-131">Replace the contents of the file with the following code</span></span>

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Model.cs)] -->
``` csharp
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;

namespace EFGetStarted.ConsoleApp
{
    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFGetStarted.ConsoleApp.NewDb;Trusted_Connection=True;");
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Url { get; set; }

        public List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
}
```

> [!TIP]  
> <span data-ttu-id="2fc20-132">実際のアプリケーションでは、各クラスは別のファイルに、接続文字列は `App.Config` ファイルにそれぞれ記述し、`ConfigurationManager` を使用して読み取ります。</span><span class="sxs-lookup"><span data-stu-id="2fc20-132">In a real application you would put each class in a separate file and put the connection string in the `App.Config` file and read it out using `ConfigurationManager`.</span></span> <span data-ttu-id="2fc20-133">わかりやすくするために、このチュートリアルではすべてのクラスを 1 つのコード ファイルに記述しています。</span><span class="sxs-lookup"><span data-stu-id="2fc20-133">For the sake of simplicity, we are putting everything in a single code file for this tutorial.</span></span>

## <a name="create-your-database"></a><span data-ttu-id="2fc20-134">データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="2fc20-134">Create your database</span></span>

<span data-ttu-id="2fc20-135">これでモデルができたので、移行を利用して自分用のデータベースを作成できます。</span><span class="sxs-lookup"><span data-stu-id="2fc20-135">Now that you have a model, you can use migrations to create a database for you.</span></span>

* <span data-ttu-id="2fc20-136">[ツール] -> [NuGet パッケージ マネージャー] -> [パッケージ マネージャー コンソール]</span><span class="sxs-lookup"><span data-stu-id="2fc20-136">Tools –> NuGet Package Manager –> Package Manager Console</span></span>

* <span data-ttu-id="2fc20-137">`Add-Migration MyFirstMigration` を実行して移行をスキャフォールディングし、モデルの最初のテーブル セットを作成します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-137">Run `Add-Migration MyFirstMigration` to scaffold a migration to create the initial set of tables for your model.</span></span>

* <span data-ttu-id="2fc20-138">`Update-Database` を実行して、新しい移行をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-138">Run `Update-Database` to apply the new migration to the database.</span></span> <span data-ttu-id="2fc20-139">データベースがまだ存在しないため、作成されてから移行が適用されます。</span><span class="sxs-lookup"><span data-stu-id="2fc20-139">Because your database doesn't exist yet, it will be created for you before the migration is applied.</span></span>

> [!TIP]  
> <span data-ttu-id="2fc20-140">モデルをさらに変更する場合、`Add-Migration` コマンドを使用して新しい移行をスキャフォールディングし、対応するスキーマの変更をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-140">If you make future changes to your model, you can use the `Add-Migration` command to scaffold a new migration to make the corresponding schema changes to the database.</span></span> <span data-ttu-id="2fc20-141">スキャフォールディング コードを確認 (および必要な変更) したら、`Update-Database` コマンドを使用してデータベースに変更を適用できます。</span><span class="sxs-lookup"><span data-stu-id="2fc20-141">Once you have checked the scaffolded code (and made any required changes), you can use the `Update-Database` command to apply the changes to the database.</span></span>
>
><span data-ttu-id="2fc20-142">EF はデータベース内の `__EFMigrationsHistory` テーブルを使用して、どの移行がデータベースに既に適用されているかを追跡します。</span><span class="sxs-lookup"><span data-stu-id="2fc20-142">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>

## <a name="use-your-model"></a><span data-ttu-id="2fc20-143">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="2fc20-143">Use your model</span></span>

<span data-ttu-id="2fc20-144">モデルを使用してデータ アクセスを実行できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="2fc20-144">You can now use your model to perform data access.</span></span>

* <span data-ttu-id="2fc20-145">*Program.cs* を開きます。</span><span class="sxs-lookup"><span data-stu-id="2fc20-145">Open *Program.cs*</span></span>

* <span data-ttu-id="2fc20-146">このファイルの内容を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="2fc20-146">Replace the contents of the file with the following code</span></span>

<!-- [!code-csharp[Main](samples/core/GetStarted/FullNet/ConsoleApp.NewDb/Program.cs)] -->
``` csharp
using System;

namespace EFGetStarted.ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var db = new BloggingContext())
            {
                db.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/adonet" });
                var count = db.SaveChanges();
                Console.WriteLine("{0} records saved to database", count);

                Console.WriteLine();
                Console.WriteLine("All blogs in database:");
                foreach (var blog in db.Blogs)
                {
                    Console.WriteLine(" - {0}", blog.Url);
                }
            }
        }
    }
}
```

* <span data-ttu-id="2fc20-147">[デバッグ] > [デバッグなしで開始]</span><span class="sxs-lookup"><span data-stu-id="2fc20-147">Debug > Start Without Debugging</span></span>

<span data-ttu-id="2fc20-148">1 つのブログがデータベースに保存され、すべてのブログの詳細がコンソールに出力されます。</span><span class="sxs-lookup"><span data-stu-id="2fc20-148">You will see that one blog is saved to the database and then the details of all blogs are printed to the console.</span></span>

![image](_static/output-new-db.png)
