---
title: Code First Migrations - EF6
description: Entity Framework 6 での Code First Migrations
author: divega
ms.date: 10/23/2016
ms.assetid: 36591d8f-36e1-4835-8a51-90f34f633d1e
uid: ef6/modeling/code-first/migrations/index
ms.openlocfilehash: b2200a91b087cf6f2dff0803b710e2518193af40
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616962"
---
# <a name="code-first-migrations"></a><span data-ttu-id="89031-103">Code First Migrations</span><span class="sxs-lookup"><span data-stu-id="89031-103">Code First Migrations</span></span>
<span data-ttu-id="89031-104">Code First Migrations は、Code First ワークフローを使用している場合に、アプリケーションのデータベース スキーマを進化させるために推奨される方法です。</span><span class="sxs-lookup"><span data-stu-id="89031-104">Code First Migrations is the recommended way to evolve your application's database schema if you are using the Code First workflow.</span></span> <span data-ttu-id="89031-105">移行では次を許可するツール セットを提供します。</span><span class="sxs-lookup"><span data-stu-id="89031-105">Migrations provide a set of tools that allow:</span></span>

1. <span data-ttu-id="89031-106">EF モデルを使用する初期データベースを作成する</span><span class="sxs-lookup"><span data-stu-id="89031-106">Create an initial database that works with your EF model</span></span>
2. <span data-ttu-id="89031-107">EF モデルに行った変更の記録を保持するために移行を生成する</span><span class="sxs-lookup"><span data-stu-id="89031-107">Generating migrations to keep track of changes you make to your EF model</span></span>
2. <span data-ttu-id="89031-108">これらの変更でデータベースを最新の状態に保つ</span><span class="sxs-lookup"><span data-stu-id="89031-108">Keep your database up to date with those changes</span></span>

<span data-ttu-id="89031-109">次のチュートリアルでは、Entity Framework での Code First Migrations の概要を示します。</span><span class="sxs-lookup"><span data-stu-id="89031-109">The following walkthrough will provide an overview Code First Migrations in Entity Framework.</span></span> <span data-ttu-id="89031-110">チュートリアル全体を完了するか、関心のあるトピックにスキップするかのいずれかを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="89031-110">You can either complete the entire walkthrough or skip to the topic you are interested in.</span></span> <span data-ttu-id="89031-111">次のトピックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="89031-111">The following topics are covered:</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="89031-112">初期モデルとデータベースをビルドする</span><span class="sxs-lookup"><span data-stu-id="89031-112">Building an Initial Model & Database</span></span>

<span data-ttu-id="89031-113">移行の使用を開始する前に、使用するプロジェクトと Code First モデルが必要です。</span><span class="sxs-lookup"><span data-stu-id="89031-113">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="89031-114">このチュートリアルでは、正規の **Blog** と **Post** モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="89031-114">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="89031-115">新しい **MigrationsDemo** コンソール アプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="89031-115">Create a new **MigrationsDemo** Console application</span></span>
-   <span data-ttu-id="89031-116">プロジェクトに最新バージョンの **EntityFramework** NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="89031-116">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="89031-117">**ツール -&gt; ライブラリ パッケージ マネージャー -&gt; パッケージ マネージャー コンソール**</span><span class="sxs-lookup"><span data-stu-id="89031-117">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="89031-118">**Install-Package EntityFramework** コマンドを実行する</span><span class="sxs-lookup"><span data-stu-id="89031-118">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="89031-119">以下に示すコードで **Model.cs** ファイルを追加する。</span><span class="sxs-lookup"><span data-stu-id="89031-119">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="89031-120">このコードでは、ドメイン モデルを構成する 1 つの **Blog** クラスと EF Code First コンテキストである **BlogContext** クラスを定義します</span><span class="sxs-lookup"><span data-stu-id="89031-120">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsDemo
      {
          public class BlogContext : DbContext
          {
              public DbSet<Blog> Blogs { get; set; }
          }

          public class Blog
          {
              public int BlogId { get; set; }
              public string Name { get; set; }
          }
      }
  ```

-   <span data-ttu-id="89031-121">これでモデルが用意できました。モデルを使ってデータ アクセスを実行してみましょう。</span><span class="sxs-lookup"><span data-stu-id="89031-121">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="89031-122">**Program.cs** ファイルを以下のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="89031-122">Update the **Program.cs** file with the code shown below.</span></span>

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsDemo
      {
          class Program
          {
              static void Main(string[] args)
              {
                  using (var db = new BlogContext())
                  {
                      db.Blogs.Add(new Blog { Name = "Another Blog " });
                      db.SaveChanges();

                      foreach (var blog in db.Blogs)
                      {
                          Console.WriteLine(blog.Name);
                      }
                  }

                  Console.WriteLine("Press any key to exit...");
                  Console.ReadKey();
              }
          }
      }
  ```

-   <span data-ttu-id="89031-123">アプリケーションを実行すると、**MigrationsCodeDemo.BlogContext** データベースが作成されたことが表示されます。</span><span class="sxs-lookup"><span data-stu-id="89031-123">Run your application and you will see that a **MigrationsCodeDemo.BlogContext** database is created for you.</span></span>

    ![データベース LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="89031-125">移行を有効にする</span><span class="sxs-lookup"><span data-stu-id="89031-125">Enabling Migrations</span></span>

<span data-ttu-id="89031-126">モデルにさらに変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="89031-126">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="89031-127">Blog クラスに Url プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="89031-127">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="89031-128">もう一度アプリケーションを実行すると、次を示す InvalidOperationException を取得します: *データベースの作成後、'BlogContext' コンテキストの背後にあるモデルが変更されました。Code First Migrations を使用したデータベースの更新を検討してください (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *)。*</span><span class="sxs-lookup"><span data-stu-id="89031-128">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="89031-129">例外で示されているように、Code First Migrations の使用を開始します。</span><span class="sxs-lookup"><span data-stu-id="89031-129">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="89031-130">最初の手順では、コンテキストの移行を有効にします。</span><span class="sxs-lookup"><span data-stu-id="89031-130">The first step is to enable migrations for our context.</span></span>

-   <span data-ttu-id="89031-131">パッケージ マネージャー コンソールで **Enable-Migrations** コマンドを実行します</span><span class="sxs-lookup"><span data-stu-id="89031-131">Run the **Enable-Migrations** command in Package Manager Console</span></span>

    <span data-ttu-id="89031-132">このコマンドでは、**Migrations** フォルダーをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="89031-132">This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="89031-133">この新しいフォルダーには、次の 2 つのファイルが含まれます。</span><span class="sxs-lookup"><span data-stu-id="89031-133">This new folder contains two files:</span></span>

-   <span data-ttu-id="89031-134">**構成クラス**。</span><span class="sxs-lookup"><span data-stu-id="89031-134">**The Configuration class.**</span></span> <span data-ttu-id="89031-135">このクラスでは、Migrations がコンテキストに対してどのように動作するかを構成できます。</span><span class="sxs-lookup"><span data-stu-id="89031-135">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="89031-136">このチュートリアルでは、既定の構成を使用します。</span><span class="sxs-lookup"><span data-stu-id="89031-136">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="89031-137">*プロジェクト内の Code First コンテキストは 1 つのみのため、Enable-Migrations ではこの構成が適用されるコンテキストの種類を自動的に入力します。*</span><span class="sxs-lookup"><span data-stu-id="89031-137">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>
-   <span data-ttu-id="89031-138">**InitialCreate 移行**。</span><span class="sxs-lookup"><span data-stu-id="89031-138">**An InitialCreate migration**.</span></span> <span data-ttu-id="89031-139">移行を有効にする前に、既に Code First でデータベースを作成しているため、この移行が生成されました。</span><span class="sxs-lookup"><span data-stu-id="89031-139">This migration was generated because we already had Code First create a database for us, before we enabled migrations.</span></span> <span data-ttu-id="89031-140">このスキャフォールディングされた移行でのコードは、データベース内に既に作成されているオブジェクトを表しています。</span><span class="sxs-lookup"><span data-stu-id="89031-140">The code in this scaffolded migration represents the objects that have already been created in the database.</span></span> <span data-ttu-id="89031-141">このケースでは、**BlogId** 列と **Name** 列を含む **Blog** テーブルです。</span><span class="sxs-lookup"><span data-stu-id="89031-141">In our case that is the **Blog** table with a **BlogId** and **Name** columns.</span></span> <span data-ttu-id="89031-142">ファイル名には、並べ替えに役立つタイムスタンプが含まれます。</span><span class="sxs-lookup"><span data-stu-id="89031-142">The filename includes a timestamp to help with ordering.</span></span>
    <span data-ttu-id="89031-143">*データベースがまだ作成されていない場合、この InitialCreate 移行がプロジェクトに追加されることはありません。代わりに、最初に Add-Migration を呼び出し、これらのテーブルを作成するコードは、新しい移行に対してスキャフォールディングされます。*</span><span class="sxs-lookup"><span data-stu-id="89031-143">*If the database had not already been created this InitialCreate migration would not have been added to the project. Instead, the first time we call Add-Migration the code to create these tables would be scaffolded to a new migration.*</span></span>

### <a name="multiple-models-targeting-the-same-database"></a><span data-ttu-id="89031-144">同じデータベースを対象とする複数のモデル</span><span class="sxs-lookup"><span data-stu-id="89031-144">Multiple Models Targeting the Same Database</span></span>

<span data-ttu-id="89031-145">EF6 より前のバージョンを使用している場合、データベースのスキーマを生成または管理するために、1 つの Code First モデルのみを使用できます。</span><span class="sxs-lookup"><span data-stu-id="89031-145">When using versions prior to EF6, only one Code First model could be used to generate/manage the schema of a database.</span></span> <span data-ttu-id="89031-146">これは、どのエントリがどのモデルに属するかを識別することができないデータベースごとの 1 つの **\_\_MigrationsHistory** テーブルの結果です。</span><span class="sxs-lookup"><span data-stu-id="89031-146">This is the result of a single **\_\_MigrationsHistory** table per database with no way to identify which entries belong to which model.</span></span>

<span data-ttu-id="89031-147">EF6 以降、**Configuration** クラスには **ContextKey** プロパティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="89031-147">Starting with EF6, the **Configuration** class includes a **ContextKey** property.</span></span> <span data-ttu-id="89031-148">これは、Code First モデルごとに一意識別子として指定されます。</span><span class="sxs-lookup"><span data-stu-id="89031-148">This acts as a unique identifier for each Code First model.</span></span> <span data-ttu-id="89031-149">**\_\_MigrationsHistory** テーブルの対応する列には、このテーブルを共有する複数のモデルからのエントリを許可します。</span><span class="sxs-lookup"><span data-stu-id="89031-149">A corresponding column in the **\_\_MigrationsHistory** table allows entries from multiple models to share the table.</span></span> <span data-ttu-id="89031-150">既定では、このプロパティにはコンテキストの完全修飾名が設定されます。</span><span class="sxs-lookup"><span data-stu-id="89031-150">By default, this property is set to the fully qualified name of your context.</span></span>

## <a name="generating--running-migrations"></a><span data-ttu-id="89031-151">移行を生成して実行する</span><span class="sxs-lookup"><span data-stu-id="89031-151">Generating & Running Migrations</span></span>

<span data-ttu-id="89031-152">Code First Migrations には、よく使うことになる 2 つのプライマリ コマンドがあります。</span><span class="sxs-lookup"><span data-stu-id="89031-152">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="89031-153">**Add-Migration** では、最後の移行が作成されてからモデルに加えた変更に基づいて、次の移行をスキャフォールディングします</span><span class="sxs-lookup"><span data-stu-id="89031-153">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="89031-154">**Update-Database** では、保留中の移行をデータベースに適用します</span><span class="sxs-lookup"><span data-stu-id="89031-154">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="89031-155">追加した新しい Url プロパティを処理するには、移行をスキャフォールディングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="89031-155">We need to scaffold a migration to take care of the new Url property we have added.</span></span> <span data-ttu-id="89031-156">**Add-Migration** コマンドを使用すると、これらの移行に名前を付けることができます。ここでは、**AddBlogUrl** という名前を付けます。</span><span class="sxs-lookup"><span data-stu-id="89031-156">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogUrl**.</span></span>

-   <span data-ttu-id="89031-157">パッケージ マネージャー コンソールで **Add-Migration AddBlogUrl** コマンドを実行します</span><span class="sxs-lookup"><span data-stu-id="89031-157">Run the **Add-Migration AddBlogUrl** command in Package Manager Console</span></span>
-   <span data-ttu-id="89031-158">**Migrations** フォルダーに、新しい **AddBlogUrl** 移行ができました。</span><span class="sxs-lookup"><span data-stu-id="89031-158">In the **Migrations** folder we now have a new **AddBlogUrl** migration.</span></span> <span data-ttu-id="89031-159">移行のファイル名の先頭には、並べ替えに役立つタイムスタンプが付きます</span><span class="sxs-lookup"><span data-stu-id="89031-159">The migration filename is pre-fixed with a timestamp to help with ordering</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogUrl : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Blogs", "Url", c => c.String());
            }

            public override void Down()
            {
                DropColumn("dbo.Blogs", "Url");
            }
        }
    }
```

<span data-ttu-id="89031-160">この移行を編集したり、この移行に追加したりすることができますが、すべてが適切なようです。</span><span class="sxs-lookup"><span data-stu-id="89031-160">We could now edit or add to this migration but everything looks pretty good.</span></span> <span data-ttu-id="89031-161">**Update-Database** を使って、この移行をデータベースに適用します。</span><span class="sxs-lookup"><span data-stu-id="89031-161">Let’s use **Update-Database** to apply this migration to the database.</span></span>

-   <span data-ttu-id="89031-162">パッケージ マネージャー コンソールで **Update-Database** コマンドを実行します</span><span class="sxs-lookup"><span data-stu-id="89031-162">Run the **Update-Database** command in Package Manager Console</span></span>
-   <span data-ttu-id="89031-163">Code First Migrations では、**Migrations** フォルダー内の移行と、データベースに適用されている移行が比較されます。</span><span class="sxs-lookup"><span data-stu-id="89031-163">Code First Migrations will compare the migrations in our **Migrations** folder with the ones that have been applied to the database.</span></span> <span data-ttu-id="89031-164">**AddBlogUrl** 移行を適用する必要があることがわかるため、これを実行します。</span><span class="sxs-lookup"><span data-stu-id="89031-164">It will see that the **AddBlogUrl** migration needs to be applied, and run it.</span></span>

<span data-ttu-id="89031-165">これで、**MigrationsDemo.BlogContext** データベースが、**Blogs** テーブル内の **Url** 列を含めるように更新されました。</span><span class="sxs-lookup"><span data-stu-id="89031-165">The **MigrationsDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

## <a name="customizing-migrations"></a><span data-ttu-id="89031-166">移行をカスタマイズする</span><span class="sxs-lookup"><span data-stu-id="89031-166">Customizing Migrations</span></span>

<span data-ttu-id="89031-167">これまで、変更を加えずに移行を生成して実行しています。</span><span class="sxs-lookup"><span data-stu-id="89031-167">So far we’ve generated and run a migration without making any changes.</span></span> <span data-ttu-id="89031-168">既定で生成されたコードの編集について見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="89031-168">Now let’s look at editing the code that gets generated by default.</span></span>

-   <span data-ttu-id="89031-169">モデルにさらに変更を加えます。新しい **Rating** プロパティを **Blog** クラスに追加しましょう</span><span class="sxs-lookup"><span data-stu-id="89031-169">It’s time to make some more changes to our model, let’s add a new **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

-   <span data-ttu-id="89031-170">新しい **Post** クラスも追加します</span><span class="sxs-lookup"><span data-stu-id="89031-170">Let's also add a new **Post** class</span></span>

``` csharp
    public class Post
    {
        public int PostId { get; set; }
        [MaxLength(200)]
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
```

-   <span data-ttu-id="89031-171">**Blog** と **Post** の間のリレーションシップのもう一方を形成するために、**Blog** クラスに **Posts** コレクションも追加します</span><span class="sxs-lookup"><span data-stu-id="89031-171">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="89031-172">移行時に Code First Migrations によってその最適な推量をスキャフォールディングできるように、**Add-Migration** コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="89031-172">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span> <span data-ttu-id="89031-173">この移行を **AddPostClass** と呼びます。</span><span class="sxs-lookup"><span data-stu-id="89031-173">We’re going to call this migration **AddPostClass**.</span></span>

-   <span data-ttu-id="89031-174">パッケージ マネージャー コンソールで **Add-Migration AddPostClass** コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89031-174">Run the **Add-Migration AddPostClass** command in Package Manager Console.</span></span>

<span data-ttu-id="89031-175">Code First Migrations では、これらの変更のスキャフォールディングを適切に処理していますが、変更が必要な可能性がある内容がいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="89031-175">Code First Migrations did a pretty good job of scaffolding these changes, but there are some things we might want to change:</span></span>

1.  <span data-ttu-id="89031-176">最初に、(以下のコードに、行 22 と行 29 を追加して) 一意のインデックスを **Posts.Title** 列に追加します。</span><span class="sxs-lookup"><span data-stu-id="89031-176">First up, let’s add a unique index to **Posts.Title** column (Adding in line 22 & 29 in the code below).</span></span>
2.  <span data-ttu-id="89031-177">また、NULL 非許容の **Blogs.Rating** 列も追加します。</span><span class="sxs-lookup"><span data-stu-id="89031-177">We’re also adding a non-nullable **Blogs.Rating** column.</span></span> <span data-ttu-id="89031-178">テーブルに既存のデータがある場合は、新しい列に CLR の既定のデータの種類を割り当てます (Rating が整数の場合、**0** になります)。</span><span class="sxs-lookup"><span data-stu-id="89031-178">If there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="89031-179">ただし、**Blogs** テーブルの既存の行が適切な評価で開始されるように、既定値の **3** を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="89031-179">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
    <span data-ttu-id="89031-180">(以下のコードの行 24 で指定した既定値を確認できます)</span><span class="sxs-lookup"><span data-stu-id="89031-180">(You can see the default value specified on line 24 of the code below)</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostClass : DbMigration
        {
            public override void Up()
            {
                CreateTable(
                    "dbo.Posts",
                    c => new
                        {
                            PostId = c.Int(nullable: false, identity: true),
                            Title = c.String(maxLength: 200),
                            Content = c.String(),
                            BlogId = c.Int(nullable: false),
                        })
                    .PrimaryKey(t => t.PostId)
                    .ForeignKey("dbo.Blogs", t => t.BlogId, cascadeDelete: true)
                    .Index(t => t.BlogId)
                    .Index(p => p.Title, unique: true);

                AddColumn("dbo.Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropIndex("dbo.Posts", new[] { "Title" });
                DropIndex("dbo.Posts", new[] { "BlogId" });
                DropForeignKey("dbo.Posts", "BlogId", "dbo.Blogs");
                DropColumn("dbo.Blogs", "Rating");
                DropTable("dbo.Posts");
            }
        }
    }
```

<span data-ttu-id="89031-181">編集済みの移行の準備ができたので、**Update-Database** を使用してデータベースを最新の状態にします。</span><span class="sxs-lookup"><span data-stu-id="89031-181">Our edited migration is ready to go, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="89031-182">今回は、Code First Migrations が実行している SQL を確認できるように、**–Verbose** フラグを指定します。</span><span class="sxs-lookup"><span data-stu-id="89031-182">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="89031-183">パッケージ マネージャー コンソールで **Update-Database –Verbose** コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89031-183">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="data-motion--custom-sql"></a><span data-ttu-id="89031-184">データ モーション / カスタム SQL</span><span class="sxs-lookup"><span data-stu-id="89031-184">Data Motion / Custom SQL</span></span>

<span data-ttu-id="89031-185">これまでデータを変更または移動を行わない移行操作を見てきました。ここでは、一部のデータを移動させる必要がある内容を見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="89031-185">So far we have looked at migration operations that don’t change or move any data, now let’s look at something that needs to move some data around.</span></span> <span data-ttu-id="89031-186">まだデータ モーション用のネイティブ サポートはありませんが、スクリプトの任意の時点でいくつかの任意の SQL コマンドを実行できます。</span><span class="sxs-lookup"><span data-stu-id="89031-186">There is no native support for data motion yet, but we can run some arbitrary SQL commands at any point in our script.</span></span>

-   <span data-ttu-id="89031-187">**Post.Abstract** プロパティをモデルに追加します。</span><span class="sxs-lookup"><span data-stu-id="89031-187">Let’s add a **Post.Abstract** property to our model.</span></span> <span data-ttu-id="89031-188">後で、**Content** 列の先頭から一部のテキストを使用して、既存の投稿用に **Abstract** を事前に設定します。</span><span class="sxs-lookup"><span data-stu-id="89031-188">Later, we’re going to pre-populate the **Abstract** for existing posts using some text from the start of the **Content** column.</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="89031-189">移行時に Code First Migrations によってその最適な推量をスキャフォールディングできるように、**Add-Migration** コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="89031-189">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span>

-   <span data-ttu-id="89031-190">パッケージ マネージャー コンソールで **Add-Migration AddPostAbstract** コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89031-190">Run the **Add-Migration AddPostAbstract** command in Package Manager Console.</span></span>
-   <span data-ttu-id="89031-191">生成された移行でスキーマの変更が行われますが、投稿ごとにコンテンツの最初の 100 文字を使用して、**Abstract** 列を事前に設定する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="89031-191">The generated migration takes care of the schema changes but we also want to pre-populate the **Abstract** column using the first 100 characters of content for each post.</span></span> <span data-ttu-id="89031-192">列が追加された後、SQL にドロップ ダウンして **UPDATE** ステートメントを実行することにより、この操作を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="89031-192">We can do this by dropping down to SQL and running an **UPDATE** statement after the column is added.</span></span>
    <span data-ttu-id="89031-193">(以下のコードの行 12 に追加します)</span><span class="sxs-lookup"><span data-stu-id="89031-193">(Adding in line 12 in the code below)</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostAbstract : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Posts", "Abstract", c => c.String());

                Sql("UPDATE dbo.Posts SET Abstract = LEFT(Content, 100) WHERE Abstract IS NULL");
            }

            public override void Down()
            {
                DropColumn("dbo.Posts", "Abstract");
            }
        }
    }
```

<span data-ttu-id="89031-194">編集した移行は問題なさそうなので、**Update-Database** を使用してデータベースを最新の状態にします。</span><span class="sxs-lookup"><span data-stu-id="89031-194">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="89031-195">このデータベースに対して実行を開始する SQL を確認できるように、**–Verbose** フラグを指定します。</span><span class="sxs-lookup"><span data-stu-id="89031-195">We’ll specify the **–Verbose** flag so that we can see the SQL being run against the database.</span></span>

-   <span data-ttu-id="89031-196">パッケージ マネージャー コンソールで **Update-Database –Verbose** コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89031-196">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="migrate-to-a-specific-version-including-downgrade"></a><span data-ttu-id="89031-197">特定のバージョンへの移行 (ダウングレードを含む)</span><span class="sxs-lookup"><span data-stu-id="89031-197">Migrate to a Specific Version (Including Downgrade)</span></span>

<span data-ttu-id="89031-198">これまでは常に、最新の移行にアップグレードしてきましたが、特定の移行にアップグレードまたはダウングレードしたい場合があります。</span><span class="sxs-lookup"><span data-stu-id="89031-198">So far we have always upgraded to the latest migration, but there may be times when you want upgrade/downgrade to a specific migration.</span></span>

<span data-ttu-id="89031-199">**AddBlogUrl** 移行を実行した後、データベースを以前の状態に移行する必要があるとします。</span><span class="sxs-lookup"><span data-stu-id="89031-199">Let’s say we want to migrate our database to the state it was in after running our **AddBlogUrl** migration.</span></span> <span data-ttu-id="89031-200">**–TargetMigration** スイッチを使用して、この移行にダウングレードすることができます。</span><span class="sxs-lookup"><span data-stu-id="89031-200">We can use the **–TargetMigration** switch to downgrade to this migration.</span></span>

-   <span data-ttu-id="89031-201">パッケージ マネージャー コンソールで **Update-Database –TargetMigration: AddBlogUrl** コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="89031-201">Run the **Update-Database –TargetMigration: AddBlogUrl** command in Package Manager Console.</span></span>

<span data-ttu-id="89031-202">このコマンドでは、**AddBlogAbstract** 移行と **AddPostClass** 移行に対して Down スクリプトが実行されます。</span><span class="sxs-lookup"><span data-stu-id="89031-202">This command will run the Down script for our **AddBlogAbstract** and **AddPostClass** migrations.</span></span>

<span data-ttu-id="89031-203">空のデータベースに完全にロール バックする場合は、**Update-Database –TargetMigration: $InitialDatabase** コマンドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="89031-203">If you want to roll all the way back to an empty database then you can use the **Update-Database –TargetMigration: $InitialDatabase** command.</span></span>

## <a name="getting-a-sql-script"></a><span data-ttu-id="89031-204">SQL スクリプトを取得する</span><span class="sxs-lookup"><span data-stu-id="89031-204">Getting a SQL Script</span></span>

<span data-ttu-id="89031-205">別の開発者が使用しているマシン上でこれらの変更が必要な場合は、ソース管理への変更を確認したときに同期することができます。</span><span class="sxs-lookup"><span data-stu-id="89031-205">If another developer wants these changes on their machine they can just sync once we check our changes into source control.</span></span> <span data-ttu-id="89031-206">新しい移行が用意できたら、Update-Database コマンドを実行するだけで、変更をローカルに適用できます。</span><span class="sxs-lookup"><span data-stu-id="89031-206">Once they have our new migrations they can just run the Update-Database command to have the changes applied locally.</span></span> <span data-ttu-id="89031-207">ただし、これらの変更をテスト サーバーにプッシュし、最終的に実稼働環境のサーバーにプッシュする必要がある場合は、DBA にハンドオフできる SQL スクリプトが必要な場合があります。</span><span class="sxs-lookup"><span data-stu-id="89031-207">However if we want to push these changes out to a test server, and eventually production, we probably want a SQL script we can hand off to our DBA.</span></span>

-   <span data-ttu-id="89031-208">**Update-Database** コマンドを実行しますが、今回は **–Script** フラグを指定するため、変更は適用されずにスクリプトに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="89031-208">Run the **Update-Database** command but this time specify the **–Script** flag so that changes are written to a script rather than applied.</span></span> <span data-ttu-id="89031-209">スクリプトを生成するソース移行とターゲット移行も指定します。</span><span class="sxs-lookup"><span data-stu-id="89031-209">We’ll also specify a source and target migration to generate the script for.</span></span> <span data-ttu-id="89031-210">空のデータベース (**$InitialDatabase**) から最新バージョン (移行 **AddPostAbstract**) に移動するスクリプトが必要です。</span><span class="sxs-lookup"><span data-stu-id="89031-210">We want a script to go from an empty database (**$InitialDatabase**) to the latest version (migration **AddPostAbstract**).</span></span>
    <span data-ttu-id="89031-211">*ターゲット移行を指定しない場合、Migrations ではターゲットとして最新の移行を使用します。ソース移行を指定しない場合は、Migrations ではデータベースの現在の状態を使用します。*</span><span class="sxs-lookup"><span data-stu-id="89031-211">*If you don’t specify a target migration, Migrations will use the latest migration as the target. If you don't specify a source migrations, Migrations will use the current state of the database.*</span></span>
-   <span data-ttu-id="89031-212">パッケージ マネージャー コンソールで **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** コマンドを実行します</span><span class="sxs-lookup"><span data-stu-id="89031-212">Run the **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** command in Package Manager Console</span></span>

<span data-ttu-id="89031-213">Code First Migrations では、実際に変更を適用するのではなく、移行パイプラインを実行しますが、これにより変更が .sql ファイルに書き込まれます。</span><span class="sxs-lookup"><span data-stu-id="89031-213">Code First Migrations will run the migration pipeline but instead of actually applying the changes it will write them out to a .sql file for you.</span></span> <span data-ttu-id="89031-214">スクリプトが生成されると、表示または保存できるように、Visual Studio で開かれます。</span><span class="sxs-lookup"><span data-stu-id="89031-214">Once the script is generated, it is opened for you in Visual Studio, ready for you to view or save.</span></span>

### <a name="generating-idempotent-scripts"></a><span data-ttu-id="89031-215">べき等スクリプトを生成する</span><span class="sxs-lookup"><span data-stu-id="89031-215">Generating Idempotent Scripts</span></span>

<span data-ttu-id="89031-216">EF6 以降、**–SourceMigration $InitialDatabase** を指定した場合、生成されたスクリプトは "べき等" になります。</span><span class="sxs-lookup"><span data-stu-id="89031-216">Starting with EF6, if you specify **–SourceMigration $InitialDatabase** then the generated script will be ‘idempotent’.</span></span> <span data-ttu-id="89031-217">べき等スクリプトでは、任意のバージョンのデータベースを最新バージョン (または、**–TargetMigration** を使用する場合、指定されたバージョン) にアップグレードできます。</span><span class="sxs-lookup"><span data-stu-id="89031-217">Idempotent scripts can upgrade a database currently at any version to the latest version (or the specified version if you use **–TargetMigration**).</span></span> <span data-ttu-id="89031-218">生成されたスクリプトには、 **\_\_MigrationsHistory** テーブルを確認し、以前に適用されている変更のみを適用するロジックが含まれます。</span><span class="sxs-lookup"><span data-stu-id="89031-218">The generated script includes logic to check the **\_\_MigrationsHistory** table and only apply changes that haven't been previously applied.</span></span>

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a><span data-ttu-id="89031-219">アプリケーションの起動で自動的にアップグレードする (MigrateDatabaseToLatestVersion 初期化子)</span><span class="sxs-lookup"><span data-stu-id="89031-219">Automatically Upgrading on Application Startup (MigrateDatabaseToLatestVersion Initializer)</span></span>

<span data-ttu-id="89031-220">アプリケーションをデプロイしている場合は、アプリケーションの起動時に、(保留中の移行を適用して) データベースを自動的にアップグレードするために必要なことがあります。</span><span class="sxs-lookup"><span data-stu-id="89031-220">If you are deploying your application you may want it to automatically upgrade the database (by applying any pending migrations) when the application launches.</span></span> <span data-ttu-id="89031-221">**MigrateDatabaseToLatestVersion** データベース初期化子を登録して、この操作を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="89031-221">You can do this by registering the **MigrateDatabaseToLatestVersion** database initializer.</span></span> <span data-ttu-id="89031-222">データベース初期化子には、単にデータベースが正しく設定されていることを確認するために使用されるいくつかのロジックが含まれています。</span><span class="sxs-lookup"><span data-stu-id="89031-222">A database initializer simply contains some logic that is used to make sure the database is setup correctly.</span></span> <span data-ttu-id="89031-223">このロジックは、最初にコンテキストがアプリケーション プロセス (**AppDomain**) 内で使用されるときに実行されます。</span><span class="sxs-lookup"><span data-stu-id="89031-223">This logic is run the first time the context is used within the application process (**AppDomain**).</span></span>

<span data-ttu-id="89031-224">以下に示すように、**Program.cs** ファイルを更新して、コンテキストを使用する前に BlogContext に **MigrateDatabaseToLatestVersion** 初期化子を設定できます (行 14)。</span><span class="sxs-lookup"><span data-stu-id="89031-224">We can update the **Program.cs** file, as shown below, to set the **MigrateDatabaseToLatestVersion** initializer for BlogContext before we use the context (Line 14).</span></span> <span data-ttu-id="89031-225">**System.Data.Entity** 名前空間の using ステートメント (行 5) も追加する必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="89031-225">Note that you also need to add a using statement for the **System.Data.Entity** namespace (Line 5).</span></span>

<span data-ttu-id="89031-226">*この初期化子のインスタンスを作成した場合、コンテキストの種類 (**BlogContext**) と移行の構成 (**Configuration**) を指定する必要があります。移行の構成は、移行を有効にしたときの **Migrations** フォルダーに追加されたクラスです。*</span><span class="sxs-lookup"><span data-stu-id="89031-226">*When we create an instance of this initializer we need to specify the context type (**BlogContext**) and the migrations configuration (**Configuration**) - the migrations configuration is the class that got added to our **Migrations** folder when we enabled Migrations.*</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Data.Entity;
    using MigrationsDemo.Migrations;

    namespace MigrationsDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                Database.SetInitializer(new MigrateDatabaseToLatestVersion<BlogContext, Configuration>());

                using (var db = new BlogContext())
                {
                    db.Blogs.Add(new Blog { Name = "Another Blog " });
                    db.SaveChanges();

                    foreach (var blog in db.Blogs)
                    {
                        Console.WriteLine(blog.Name);
                    }
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }
        }
    }
```

<span data-ttu-id="89031-227">これで、アプリケーションが実行されるたびに、最初にターゲットにしているデータベースが最新かどうかを確認し、最新でない場合は保留中の移行を適用するようになりました。</span><span class="sxs-lookup"><span data-stu-id="89031-227">Now whenever our application runs it will first check if the database it is targeting is up-to-date, and apply any pending migrations if it is not.</span></span>
