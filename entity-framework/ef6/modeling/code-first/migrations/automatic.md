---
title: 自動の Code First Migrations の EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
ms.openlocfilehash: 21f77ef49db2485047292b3928b4f63d49dbb180
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489987"
---
# <a name="automatic-code-first-migrations"></a><span data-ttu-id="cbc34-102">自動の Code First Migrations</span><span class="sxs-lookup"><span data-stu-id="cbc34-102">Automatic Code First Migrations</span></span>
<span data-ttu-id="cbc34-103">自動移行では、各変更のプロジェクト内のコード ファイルをしなくても Code First Migrations を使用できます。</span><span class="sxs-lookup"><span data-stu-id="cbc34-103">Automatic Migrations allows you to use Code First Migrations without having a code file in your project for each change you make.</span></span> <span data-ttu-id="cbc34-104">すべての変更を自動的に適用できる - 列名の変更がコード ベースの移行の使用を要求するなど。</span><span class="sxs-lookup"><span data-stu-id="cbc34-104">Not all changes can be applied automatically - for example column renames require the use of a code-based migration.</span></span>

> [!NOTE]
> <span data-ttu-id="cbc34-105">この記事では、基本的なシナリオで Code First Migrations を使用する方法を理解する前提としています。</span><span class="sxs-lookup"><span data-stu-id="cbc34-105">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="cbc34-106">そうしないかどうかを読み取る必要があります[Code First Migrations](~/ef6/modeling/code-first/migrations/index.md)続行する前にします。</span><span class="sxs-lookup"><span data-stu-id="cbc34-106">If you don’t, then you’ll need to read [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) before continuing.</span></span>

## <a name="recommendation-for-team-environments"></a><span data-ttu-id="cbc34-107">チーム環境の推奨事項</span><span class="sxs-lookup"><span data-stu-id="cbc34-107">Recommendation for Team Environments</span></span>

<span data-ttu-id="cbc34-108">自動およびコード ベースの移行を挿入することができますが、これは、チーム開発シナ リオでは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="cbc34-108">You can intersperse automatic and code-based migrations but this is not recommended in team development scenarios.</span></span> <span data-ttu-id="cbc34-109">ソース管理を使用する開発者のチームに参加している場合のみ自動移行または純粋なコード ベースの移行が使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cbc34-109">If you are part of a team of developers that use source control you should either use purely automatic migrations or purely code-based migrations.</span></span> <span data-ttu-id="cbc34-110">特定の自動移行の制限事項、チーム環境でのコード ベースの移行の使用をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="cbc34-110">Given the limitations of automatic migrations we recommend using code-based migrations in team environments.</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="cbc34-111">初期モデルとデータベースをビルドする</span><span class="sxs-lookup"><span data-stu-id="cbc34-111">Building an Initial Model & Database</span></span>

<span data-ttu-id="cbc34-112">移行の使用を開始する前に、使用するプロジェクトと Code First モデルが必要です。</span><span class="sxs-lookup"><span data-stu-id="cbc34-112">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="cbc34-113">このチュートリアルでは、正規の **Blog** と **Post** モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="cbc34-113">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="cbc34-114">新規作成**MigrationsAutomaticDemo**コンソール アプリケーション</span><span class="sxs-lookup"><span data-stu-id="cbc34-114">Create a new **MigrationsAutomaticDemo** Console application</span></span>
-   <span data-ttu-id="cbc34-115">プロジェクトに最新バージョンの **EntityFramework** NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="cbc34-115">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="cbc34-116">**ツール -&gt; ライブラリ パッケージ マネージャー -&gt; パッケージ マネージャー コンソール**</span><span class="sxs-lookup"><span data-stu-id="cbc34-116">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="cbc34-117">**Install-Package EntityFramework** コマンドを実行する</span><span class="sxs-lookup"><span data-stu-id="cbc34-117">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="cbc34-118">以下に示すコードで **Model.cs** ファイルを追加する。</span><span class="sxs-lookup"><span data-stu-id="cbc34-118">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="cbc34-119">このコードでは、ドメイン モデルを構成する 1 つの **Blog** クラスと EF Code First コンテキストである **BlogContext** クラスを定義します</span><span class="sxs-lookup"><span data-stu-id="cbc34-119">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsAutomaticDemo
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

-   <span data-ttu-id="cbc34-120">これでモデルが用意できました。モデルを使ってデータ アクセスを実行してみましょう。</span><span class="sxs-lookup"><span data-stu-id="cbc34-120">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="cbc34-121">**Program.cs** ファイルを以下のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="cbc34-121">Update the **Program.cs** file with the code shown below.</span></span>

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsAutomaticDemo
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

-   <span data-ttu-id="cbc34-122">アプリケーションを実行し、ことがわかりますが、 **MigrationsAutomaticCodeDemo.BlogContext**データベースが作成されます。</span><span class="sxs-lookup"><span data-stu-id="cbc34-122">Run your application and you will see that a **MigrationsAutomaticCodeDemo.BlogContext** database is created for you.</span></span>

    ![LocalDB のデータベース](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="cbc34-124">移行を有効にする</span><span class="sxs-lookup"><span data-stu-id="cbc34-124">Enabling Migrations</span></span>

<span data-ttu-id="cbc34-125">モデルにさらに変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="cbc34-125">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="cbc34-126">Blog クラスに Url プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="cbc34-126">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="cbc34-127">もう一度アプリケーションを実行すると、次を示す InvalidOperationException を取得します: *データベースの作成後、'BlogContext' コンテキストの背後にあるモデルが変更されました。Code First Migrations を使用したデータベースの更新を検討してください (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](http://go.microsoft.com/fwlink/?LinkId=238269)*)。*</span><span class="sxs-lookup"><span data-stu-id="cbc34-127">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](http://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="cbc34-128">例外で示されているように、Code First Migrations の使用を開始します。</span><span class="sxs-lookup"><span data-stu-id="cbc34-128">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="cbc34-129">自動移行を使用するため、ここを指定する、 **– EnableAutomaticMigrations**スイッチします。</span><span class="sxs-lookup"><span data-stu-id="cbc34-129">Because we want to use automatic migrations we’re going to specify the **–EnableAutomaticMigrations** switch.</span></span>

-   <span data-ttu-id="cbc34-130">実行、 **Enable-migrations – EnableAutomaticMigrations**パッケージ マネージャー コンソールのコマンドでコマンドが追加、**移行**フォルダー、プロジェクトにします。</span><span class="sxs-lookup"><span data-stu-id="cbc34-130">Run the **Enable-Migrations –EnableAutomaticMigrations** command in Package Manager Console This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="cbc34-131">この新しいフォルダーには、1 つのファイルが含まれています。</span><span class="sxs-lookup"><span data-stu-id="cbc34-131">This new folder contains one file:</span></span>

-   <span data-ttu-id="cbc34-132">**構成クラス**。</span><span class="sxs-lookup"><span data-stu-id="cbc34-132">**The Configuration class.**</span></span> <span data-ttu-id="cbc34-133">このクラスでは、Migrations がコンテキストに対してどのように動作するかを構成できます。</span><span class="sxs-lookup"><span data-stu-id="cbc34-133">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="cbc34-134">このチュートリアルでは、既定の構成を使用します。</span><span class="sxs-lookup"><span data-stu-id="cbc34-134">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="cbc34-135">*プロジェクト内の Code First コンテキストは 1 つのみのため、Enable-Migrations ではこの構成が適用されるコンテキストの種類を自動的に入力します。*</span><span class="sxs-lookup"><span data-stu-id="cbc34-135">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>

 

## <a name="your-first-automatic-migration"></a><span data-ttu-id="cbc34-136">最初の自動移行</span><span class="sxs-lookup"><span data-stu-id="cbc34-136">Your First Automatic Migration</span></span>

<span data-ttu-id="cbc34-137">Code First Migrations には、よく使うことになる 2 つのプライマリ コマンドがあります。</span><span class="sxs-lookup"><span data-stu-id="cbc34-137">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="cbc34-138">**Add-Migration** では、最後の移行が作成されてからモデルに加えた変更に基づいて、次の移行をスキャフォールディングします</span><span class="sxs-lookup"><span data-stu-id="cbc34-138">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="cbc34-139">**Update-Database** では、保留中の移行をデータベースに適用します</span><span class="sxs-lookup"><span data-stu-id="cbc34-139">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="cbc34-140">回避するためにここでは (実際する必要があります) 場合を除き、追加の移行を使用して、Code First Migrations を自動的に通知することに焦点を計算し、変更を適用します。</span><span class="sxs-lookup"><span data-stu-id="cbc34-140">We are going to avoid using Add-Migration (unless we really need to) and focus on letting Code First Migrations automatically calculate and apply the changes.</span></span> <span data-ttu-id="cbc34-141">使用しましょう**データベースを更新**私たちのモデルに変更をプッシュする Code First Migrations を取得する (新しい**Blog.Ur**l プロパティ)、データベースにします。</span><span class="sxs-lookup"><span data-stu-id="cbc34-141">Let’s use **Update-Database** to get Code First Migrations to push the changes to our model (the new **Blog.Ur**l property) to the database.</span></span>

-   <span data-ttu-id="cbc34-142">実行、 **Update-database**パッケージ マネージャー コンソール コマンド。</span><span class="sxs-lookup"><span data-stu-id="cbc34-142">Run the **Update-Database** command in Package Manager Console.</span></span>

<span data-ttu-id="cbc34-143">**MigrationsAutomaticDemo.BlogContext**に含めるデータベースが更新されるようになりました、 **Url**内の列、**ブログ**テーブル。</span><span class="sxs-lookup"><span data-stu-id="cbc34-143">The **MigrationsAutomaticDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

 

## <a name="your-second-automatic-migration"></a><span data-ttu-id="cbc34-144">2 番目の自動移行</span><span class="sxs-lookup"><span data-stu-id="cbc34-144">Your Second Automatic Migration</span></span>

<span data-ttu-id="cbc34-145">別の変更し、Code First Migrations をデータベースに変更を自動的にプッシュできるようにしましょう。</span><span class="sxs-lookup"><span data-stu-id="cbc34-145">Let’s make another change and let Code First Migrations automatically push the changes to the database for us.</span></span>

-   <span data-ttu-id="cbc34-146">新しい **Post** クラスも追加します</span><span class="sxs-lookup"><span data-stu-id="cbc34-146">Let's also add a new **Post** class</span></span>

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

-   <span data-ttu-id="cbc34-147">**Blog** と **Post** の間のリレーションシップのもう一方を形成するために、**Blog** クラスに **Posts** コレクションも追加します</span><span class="sxs-lookup"><span data-stu-id="cbc34-147">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="cbc34-148">使用して**Update-database**データベースの最新の状態にします。</span><span class="sxs-lookup"><span data-stu-id="cbc34-148">Now use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="cbc34-149">今回は、Code First Migrations が実行している SQL を確認できるように、**–Verbose** フラグを指定します。</span><span class="sxs-lookup"><span data-stu-id="cbc34-149">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="cbc34-150">パッケージ マネージャー コンソールで **Update-Database –Verbose** コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="cbc34-150">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="adding-a-code-based-migration"></a><span data-ttu-id="cbc34-151">コードを追加するベースの移行</span><span class="sxs-lookup"><span data-stu-id="cbc34-151">Adding a Code Based Migration</span></span>

<span data-ttu-id="cbc34-152">ここでのコード ベースの移行を使用する可能性が何かを見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="cbc34-152">Now let’s look at something we might want to use a code-based migration for.</span></span>

-   <span data-ttu-id="cbc34-153">追加、**評価**プロパティを**ブログ**クラス</span><span class="sxs-lookup"><span data-stu-id="cbc34-153">Let’s add a **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

<span data-ttu-id="cbc34-154">だけを実行できます**Update-database**データベースにこれらの変更をプッシュします。</span><span class="sxs-lookup"><span data-stu-id="cbc34-154">We could just run **Update-Database** to push these changes to the database.</span></span> <span data-ttu-id="cbc34-155">ただし、追加しています、null 非許容**Blogs.Rating**列、テーブル内の既存のデータがある場合に割り当てる新しい列のデータ型の CLR 既定 (ことは、評価は、整数**0**).</span><span class="sxs-lookup"><span data-stu-id="cbc34-155">However, we're adding a non-nullable **Blogs.Rating** column, if there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="cbc34-156">ただし、**Blogs** テーブルの既存の行が適切な評価で開始されるように、既定値の **3** を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cbc34-156">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
<span data-ttu-id="cbc34-157">Add-migration コマンドを使用して、編集できるように、コード ベースの移行をアウトには、この変更を記述しましょう。</span><span class="sxs-lookup"><span data-stu-id="cbc34-157">Let’s use the Add-Migration command to write this change out to a code-based migration so that we can edit it.</span></span> <span data-ttu-id="cbc34-158">**Add-migration**コマンドで、これらの移行に名前を付け、という名前を付けましょう私たち**AddBlogRating**します。</span><span class="sxs-lookup"><span data-stu-id="cbc34-158">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogRating**.</span></span>

-   <span data-ttu-id="cbc34-159">実行、 **Add-migration AddBlogRating**パッケージ マネージャー コンソール コマンド。</span><span class="sxs-lookup"><span data-stu-id="cbc34-159">Run the **Add-Migration AddBlogRating** command in Package Manager Console.</span></span>
-   <span data-ttu-id="cbc34-160">**移行**フォルダーある新しい**AddBlogRating**移行します。</span><span class="sxs-lookup"><span data-stu-id="cbc34-160">In the **Migrations** folder we now have a new **AddBlogRating** migration.</span></span> <span data-ttu-id="cbc34-161">移行ファイル名は事前のタイムスタンプを持つ順序付けに関するヘルプを修正しました。</span><span class="sxs-lookup"><span data-stu-id="cbc34-161">The migration filename is pre-fixed with a timestamp to help with ordering.</span></span> <span data-ttu-id="cbc34-162">Blog.Rating (次のコード行 10) の 3 の既定値を指定する、生成されたコードを編集しましょう</span><span class="sxs-lookup"><span data-stu-id="cbc34-162">Let’s edit the generated code to specify a default value of 3 for Blog.Rating (Line 10 in the code below)</span></span>

<span data-ttu-id="cbc34-163">*移行では、いくつかのメタデータをキャプチャする分離コード ファイルもあります。このメタデータは、このコード ベースの移行の前に実行された自動移行をレプリケートする Code First Migrations をことができます。これは、機能は、他の開発者が、移行を実行する場合、または、アプリケーションをデプロイするときに重要です。*</span><span class="sxs-lookup"><span data-stu-id="cbc34-163">*The migration also has a code-behind file that captures some metadata. This metadata will allow Code First Migrations to replicate the automatic migrations we performed before this code-based migration. This is important if another developer wants to run our migrations or when it’s time to deploy our application.*</span></span>

``` csharp
    namespace MigrationsAutomaticDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogRating : DbMigration
        {
            public override void Up()
            {
                AddColumn("Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropColumn("Blogs", "Rating");
            }
        }
    }
```

<span data-ttu-id="cbc34-164">編集した移行は問題なさそうなので、**Update-Database** を使用してデータベースを最新の状態にします。</span><span class="sxs-lookup"><span data-stu-id="cbc34-164">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span>

-   <span data-ttu-id="cbc34-165">実行、 **Update-database**パッケージ マネージャー コンソール コマンド。</span><span class="sxs-lookup"><span data-stu-id="cbc34-165">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="back-to-automatic-migrations"></a><span data-ttu-id="cbc34-166">自動移行に戻る</span><span class="sxs-lookup"><span data-stu-id="cbc34-166">Back to Automatic Migrations</span></span>

<span data-ttu-id="cbc34-167">単純な変更の自動移行に戻るに空きができました。</span><span class="sxs-lookup"><span data-stu-id="cbc34-167">We are now free to switch back to automatic migrations for our simpler changes.</span></span> <span data-ttu-id="cbc34-168">Code First Migrations は、自動およびコード ベースの移行を実行する各コード ベースの移行の分離コード ファイルに保存するが、メタデータに基づいて、正しい順序で行われます。</span><span class="sxs-lookup"><span data-stu-id="cbc34-168">Code First Migrations will take care of performing the automatic and code-based migrations in the correct order based on the metadata it is storing in the code-behind file for each code-based migration.</span></span>

-   <span data-ttu-id="cbc34-169">Post.Abstract プロパティをモデルに追加しましょう</span><span class="sxs-lookup"><span data-stu-id="cbc34-169">Let’s add a Post.Abstract property to our model</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="cbc34-170">使用できるようになりました**Update-database**自動移行を使用してデータベースにこの変更をプッシュする Code First Migrations を取得します。</span><span class="sxs-lookup"><span data-stu-id="cbc34-170">Now we can use **Update-Database** to get Code First Migrations to push this change to the database using an automatic migration.</span></span>

-   <span data-ttu-id="cbc34-171">実行、 **Update-database**パッケージ マネージャー コンソール コマンド。</span><span class="sxs-lookup"><span data-stu-id="cbc34-171">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="summary"></a><span data-ttu-id="cbc34-172">まとめ</span><span class="sxs-lookup"><span data-stu-id="cbc34-172">Summary</span></span>

<span data-ttu-id="cbc34-173">自動移行を使用してプッシュする方法を説明するこのチュートリアルでは、モデルは、データベースに変更されます。</span><span class="sxs-lookup"><span data-stu-id="cbc34-173">In this walkthrough you saw how to use automatic migrations to push model changes to the database.</span></span> <span data-ttu-id="cbc34-174">スキャフォールディングし、詳細に制御する必要がある場合は、自動移行の間のコード ベースの移行を実行する方法も説明しました。</span><span class="sxs-lookup"><span data-stu-id="cbc34-174">You also saw how to scaffold and run code-based migrations in between automatic migrations when you need more control.</span></span>
