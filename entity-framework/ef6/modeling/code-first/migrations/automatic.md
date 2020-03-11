---
title: 自動 Code First Migrations-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
ms.openlocfilehash: 2713afaf09707b7696e90464aac9945c2d82d274
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415701"
---
# <a name="automatic-code-first-migrations"></a><span data-ttu-id="8b10c-102">自動 Code First Migrations</span><span class="sxs-lookup"><span data-stu-id="8b10c-102">Automatic Code First Migrations</span></span>
<span data-ttu-id="8b10c-103">自動移行を使用すると、変更ごとにプロジェクトにコードファイルを作成しなくても Code First Migrations を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8b10c-103">Automatic Migrations allows you to use Code First Migrations without having a code file in your project for each change you make.</span></span> <span data-ttu-id="8b10c-104">すべての変更を自動的に適用できるわけではありません。たとえば、列の名前を変更するには、コードベースの移行を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8b10c-104">Not all changes can be applied automatically - for example column renames require the use of a code-based migration.</span></span>

> [!NOTE]
> <span data-ttu-id="8b10c-105">この記事では、基本的なシナリオでの Code First Migrations の使用方法を理解していることを前提としています。</span><span class="sxs-lookup"><span data-stu-id="8b10c-105">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="8b10c-106">そうでない場合は、続行する前に[Code First Migrations](~/ef6/modeling/code-first/migrations/index.md)を読む必要があります。</span><span class="sxs-lookup"><span data-stu-id="8b10c-106">If you don’t, then you’ll need to read [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) before continuing.</span></span>

## <a name="recommendation-for-team-environments"></a><span data-ttu-id="8b10c-107">チーム環境の推奨事項</span><span class="sxs-lookup"><span data-stu-id="8b10c-107">Recommendation for Team Environments</span></span>

<span data-ttu-id="8b10c-108">自動およびコードベースの移行を intersperse することはできますが、これはチーム開発シナリオでは推奨されません。</span><span class="sxs-lookup"><span data-stu-id="8b10c-108">You can intersperse automatic and code-based migrations but this is not recommended in team development scenarios.</span></span> <span data-ttu-id="8b10c-109">ソース管理を使用する開発者チームの一員である場合は、純粋な自動移行または純粋なコードベースの移行を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8b10c-109">If you are part of a team of developers that use source control you should either use purely automatic migrations or purely code-based migrations.</span></span> <span data-ttu-id="8b10c-110">自動移行の制限を考慮して、チーム環境でコードベースの移行を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8b10c-110">Given the limitations of automatic migrations we recommend using code-based migrations in team environments.</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="8b10c-111">初期モデルとデータベースをビルドする</span><span class="sxs-lookup"><span data-stu-id="8b10c-111">Building an Initial Model & Database</span></span>

<span data-ttu-id="8b10c-112">移行の使用を開始する前に、使用するプロジェクトと Code First モデルが必要です。</span><span class="sxs-lookup"><span data-stu-id="8b10c-112">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="8b10c-113">このチュートリアルでは、正規の **Blog** と **Post** モデルを使用します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-113">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="8b10c-114">新しい**MigrationsAutomaticDemo**コンソールアプリケーションを作成する</span><span class="sxs-lookup"><span data-stu-id="8b10c-114">Create a new **MigrationsAutomaticDemo** Console application</span></span>
-   <span data-ttu-id="8b10c-115">プロジェクトに最新バージョンの **EntityFramework** NuGet パッケージを追加する</span><span class="sxs-lookup"><span data-stu-id="8b10c-115">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="8b10c-116">**ツール -&gt; ライブラリ パッケージ マネージャー -&gt; パッケージ マネージャー コンソール**</span><span class="sxs-lookup"><span data-stu-id="8b10c-116">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="8b10c-117">**Install-Package EntityFramework** コマンドを実行する</span><span class="sxs-lookup"><span data-stu-id="8b10c-117">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="8b10c-118">以下に示すコードで **Model.cs** ファイルを追加する。</span><span class="sxs-lookup"><span data-stu-id="8b10c-118">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="8b10c-119">このコードでは、ドメイン モデルを構成する 1 つの **Blog** クラスと EF Code First コンテキストである **BlogContext** クラスを定義します</span><span class="sxs-lookup"><span data-stu-id="8b10c-119">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

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

-   <span data-ttu-id="8b10c-120">これでモデルが用意できました。モデルを使ってデータ アクセスを実行してみましょう。</span><span class="sxs-lookup"><span data-stu-id="8b10c-120">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="8b10c-121">**Program.cs** ファイルを以下のコードで更新します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-121">Update the **Program.cs** file with the code shown below.</span></span>

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

-   <span data-ttu-id="8b10c-122">アプリケーションを実行すると、 **MigrationsAutomaticCodeDemo コンテキスト**データベースが作成されていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="8b10c-122">Run your application and you will see that a **MigrationsAutomaticCodeDemo.BlogContext** database is created for you.</span></span>

    ![データベース LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="8b10c-124">移行を有効にする</span><span class="sxs-lookup"><span data-stu-id="8b10c-124">Enabling Migrations</span></span>

<span data-ttu-id="8b10c-125">モデルにさらに変更を加えます。</span><span class="sxs-lookup"><span data-stu-id="8b10c-125">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="8b10c-126">Blog クラスに Url プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-126">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="8b10c-127">アプリケーションをもう一度実行すると、*データベースの作成後に ' ブログのコンテキスト ' コンテキストが変更されたモデルを示す InvalidOperationException が表示されます。Code First Migrations を使用してデータベースを更新することを検討してください (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *)。*</span><span class="sxs-lookup"><span data-stu-id="8b10c-127">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="8b10c-128">例外で示されているように、Code First Migrations の使用を開始します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-128">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="8b10c-129">自動移行を使用するため、 **– enable自動移行**スイッチを指定します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-129">Because we want to use automatic migrations we’re going to specify the **–EnableAutomaticMigrations** switch.</span></span>

-   <span data-ttu-id="8b10c-130">パッケージマネージャーコンソールで、 **[移行の有効化-Enable自動移行]** コマンドを実行します。このコマンドにより、 **[移行]** フォルダーがプロジェクトに追加されました。</span><span class="sxs-lookup"><span data-stu-id="8b10c-130">Run the **Enable-Migrations –EnableAutomaticMigrations** command in Package Manager Console This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="8b10c-131">この新しいフォルダーには、次のファイルが1つ含まれています:</span><span class="sxs-lookup"><span data-stu-id="8b10c-131">This new folder contains one file:</span></span>

-   <span data-ttu-id="8b10c-132">**構成クラス**。</span><span class="sxs-lookup"><span data-stu-id="8b10c-132">**The Configuration class.**</span></span> <span data-ttu-id="8b10c-133">このクラスでは、Migrations がコンテキストに対してどのように動作するかを構成できます。</span><span class="sxs-lookup"><span data-stu-id="8b10c-133">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="8b10c-134">このチュートリアルでは、既定の構成を使用します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-134">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="8b10c-135">*プロジェクト内の Code First コンテキストは 1 つのみのため、Enable-Migrations ではこの構成が適用されるコンテキストの種類を自動的に入力します。*</span><span class="sxs-lookup"><span data-stu-id="8b10c-135">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>

 

## <a name="your-first-automatic-migration"></a><span data-ttu-id="8b10c-136">最初の自動移行</span><span class="sxs-lookup"><span data-stu-id="8b10c-136">Your First Automatic Migration</span></span>

<span data-ttu-id="8b10c-137">Code First Migrations には、よく使うことになる 2 つのプライマリ コマンドがあります。</span><span class="sxs-lookup"><span data-stu-id="8b10c-137">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="8b10c-138">**Add-Migration** では、最後の移行が作成されてからモデルに加えた変更に基づいて、次の移行をスキャフォールディングします</span><span class="sxs-lookup"><span data-stu-id="8b10c-138">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="8b10c-139">**Update-Database** では、保留中の移行をデータベースに適用します</span><span class="sxs-lookup"><span data-stu-id="8b10c-139">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="8b10c-140">(本当に必要な場合を除き) 追加移行を使用しないようにし、Code First Migrations によって自動的に計算され、変更が適用されるようにします。</span><span class="sxs-lookup"><span data-stu-id="8b10c-140">We are going to avoid using Add-Migration (unless we really need to) and focus on letting Code First Migrations automatically calculate and apply the changes.</span></span> <span data-ttu-id="8b10c-141">**更新データベース**を使用して、モデル (新しい**Blog. Ur**l プロパティ) への変更をデータベースにプッシュ Code First Migrations を取得してみましょう。</span><span class="sxs-lookup"><span data-stu-id="8b10c-141">Let’s use **Update-Database** to get Code First Migrations to push the changes to our model (the new **Blog.Ur**l property) to the database.</span></span>

-   <span data-ttu-id="8b10c-142">パッケージマネージャーコンソールで、**データベースの更新**コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-142">Run the **Update-Database** command in Package Manager Console.</span></span>

<span data-ttu-id="8b10c-143">これで、 **MigrationsAutomaticDemo コンテキスト**データベースが更新され、**ブログ**テーブルに**Url**列が含まれるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8b10c-143">The **MigrationsAutomaticDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

 

## <a name="your-second-automatic-migration"></a><span data-ttu-id="8b10c-144">2回目の自動移行</span><span class="sxs-lookup"><span data-stu-id="8b10c-144">Your Second Automatic Migration</span></span>

<span data-ttu-id="8b10c-145">別の変更を加えて、Code First Migrations によってデータベースに変更が自動的にプッシュされるようにしましょう。</span><span class="sxs-lookup"><span data-stu-id="8b10c-145">Let’s make another change and let Code First Migrations automatically push the changes to the database for us.</span></span>

-   <span data-ttu-id="8b10c-146">新しい **Post** クラスも追加します</span><span class="sxs-lookup"><span data-stu-id="8b10c-146">Let's also add a new **Post** class</span></span>

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

-   <span data-ttu-id="8b10c-147">**Blog** と **Post** の間のリレーションシップのもう一方を形成するために、**Blog** クラスに **Posts** コレクションも追加します</span><span class="sxs-lookup"><span data-stu-id="8b10c-147">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="8b10c-148">次に、データベースを最新の状態にするために、データベースを**更新**します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-148">Now use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="8b10c-149">今回は、Code First Migrations が実行している SQL を確認できるように、 **–Verbose** フラグを指定します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-149">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="8b10c-150">パッケージ マネージャー コンソールで **Update-Database –Verbose** コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-150">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="adding-a-code-based-migration"></a><span data-ttu-id="8b10c-151">コードベースの移行の追加</span><span class="sxs-lookup"><span data-stu-id="8b10c-151">Adding a Code Based Migration</span></span>

<span data-ttu-id="8b10c-152">では、コードベースの移行を使用する必要があるものを見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="8b10c-152">Now let’s look at something we might want to use a code-based migration for.</span></span>

-   <span data-ttu-id="8b10c-153">**評価**プロパティを**ブログ**クラスに追加してみましょう</span><span class="sxs-lookup"><span data-stu-id="8b10c-153">Let’s add a **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

<span data-ttu-id="8b10c-154">これらの変更をデータベースにプッシュするには、 **Update データベース**を実行するだけです。</span><span class="sxs-lookup"><span data-stu-id="8b10c-154">We could just run **Update-Database** to push these changes to the database.</span></span> <span data-ttu-id="8b10c-155">ただし、null 非許容のブログを追加**しています。評価**列には、テーブルに既存のデータがある場合は、新しい列のデータ型の CLR の既定値が割り当てられます (評価は整数であり、 **0**になります)。</span><span class="sxs-lookup"><span data-stu-id="8b10c-155">However, we're adding a non-nullable **Blogs.Rating** column, if there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="8b10c-156">ただし、**Blogs** テーブルの既存の行が適切な評価で開始されるように、既定値の **3** を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8b10c-156">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
<span data-ttu-id="8b10c-157">ここでは、[移行の追加] コマンドを使用して、コードベースの移行にこの変更を書き込んで、編集できるようにしましょう。</span><span class="sxs-lookup"><span data-stu-id="8b10c-157">Let’s use the Add-Migration command to write this change out to a code-based migration so that we can edit it.</span></span> <span data-ttu-id="8b10c-158">**[移行の追加]** コマンドを使用すると、これらの移行に名前を付けることができます。私たちの**AddBlogRating**を呼び出すだけです。</span><span class="sxs-lookup"><span data-stu-id="8b10c-158">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogRating**.</span></span>

-   <span data-ttu-id="8b10c-159">パッケージマネージャーコンソールで、 **[AddBlogRating の追加と移行]** コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-159">Run the **Add-Migration AddBlogRating** command in Package Manager Console.</span></span>
-   <span data-ttu-id="8b10c-160">**移行**フォルダーに、新しい**AddBlogRating**移行が追加されました。</span><span class="sxs-lookup"><span data-stu-id="8b10c-160">In the **Migrations** folder we now have a new **AddBlogRating** migration.</span></span> <span data-ttu-id="8b10c-161">移行ファイル名は、順序付けを支援するタイムスタンプで事前に固定されています。</span><span class="sxs-lookup"><span data-stu-id="8b10c-161">The migration filename is pre-fixed with a timestamp to help with ordering.</span></span> <span data-ttu-id="8b10c-162">生成されたコードを編集して、ブログの既定値3を指定してみましょう (下のコードの10行目)</span><span class="sxs-lookup"><span data-stu-id="8b10c-162">Let’s edit the generated code to specify a default value of 3 for Blog.Rating (Line 10 in the code below)</span></span>

<span data-ttu-id="8b10c-163">*移行には、いくつかのメタデータをキャプチャする分離コードファイルもあります。このメタデータにより、Code First Migrations は、このコードベースの移行の前に実行した自動移行をレプリケートできます。これは、別の開発者が移行を実行する場合、またはアプリケーションをデプロイする時期がある場合に重要です。*</span><span class="sxs-lookup"><span data-stu-id="8b10c-163">*The migration also has a code-behind file that captures some metadata. This metadata will allow Code First Migrations to replicate the automatic migrations we performed before this code-based migration. This is important if another developer wants to run our migrations or when it’s time to deploy our application.*</span></span>

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

<span data-ttu-id="8b10c-164">編集した移行は問題なさそうなので、**Update-Database** を使用してデータベースを最新の状態にします。</span><span class="sxs-lookup"><span data-stu-id="8b10c-164">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span>

-   <span data-ttu-id="8b10c-165">パッケージマネージャーコンソールで、**データベースの更新**コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-165">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="back-to-automatic-migrations"></a><span data-ttu-id="8b10c-166">自動移行に戻る</span><span class="sxs-lookup"><span data-stu-id="8b10c-166">Back to Automatic Migrations</span></span>

<span data-ttu-id="8b10c-167">自動移行に切り替えて、簡単に変更できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8b10c-167">We are now free to switch back to automatic migrations for our simpler changes.</span></span> <span data-ttu-id="8b10c-168">Code First Migrations は、コードベースの移行のたびに分離コードファイルに格納されているメタデータに基づいて、自動およびコードベースの移行を正しい順序で実行します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-168">Code First Migrations will take care of performing the automatic and code-based migrations in the correct order based on the metadata it is storing in the code-behind file for each code-based migration.</span></span>

-   <span data-ttu-id="8b10c-169">ここでは、モデルに "Post. 抽象" プロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-169">Let’s add a Post.Abstract property to our model</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="8b10c-170">これで、**更新データベース**を使用して、自動移行を使用してこの変更をデータベースにプッシュ Code First Migrations を取得できるようになりました。</span><span class="sxs-lookup"><span data-stu-id="8b10c-170">Now we can use **Update-Database** to get Code First Migrations to push this change to the database using an automatic migration.</span></span>

-   <span data-ttu-id="8b10c-171">パッケージマネージャーコンソールで、**データベースの更新**コマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8b10c-171">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="summary"></a><span data-ttu-id="8b10c-172">まとめ</span><span class="sxs-lookup"><span data-stu-id="8b10c-172">Summary</span></span>

<span data-ttu-id="8b10c-173">このチュートリアルでは、自動移行を使用してモデルの変更をデータベースにプッシュする方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="8b10c-173">In this walkthrough you saw how to use automatic migrations to push model changes to the database.</span></span> <span data-ttu-id="8b10c-174">さらに制御が必要な場合に、自動移行の間にコードベースの移行をスキャフォールディングして実行する方法についても説明しました。</span><span class="sxs-lookup"><span data-stu-id="8b10c-174">You also saw how to scaffold and run code-based migrations in between automatic migrations when you need more control.</span></span>
